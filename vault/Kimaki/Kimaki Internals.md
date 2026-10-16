---
title: Kimaki Internals
tags:
  - note/kimaki
created: 2026-10-16
source: https://github.com/remorses/kimaki/blob/main/AGENTS.md
---

# Kimaki Internals

Part of [[Kimaki]]. Distilled from the repo's `AGENTS.md` — the maintainer's rules for working on Kimaki (bot + CLI, built on OpenCode V2).

## Repo shape

The important package is `kimaki/`: the Discord bot and the `kimaki` CLI (npm `kimaki`), built on **OpenCode V2 only**. The legacy V1 codebase lives in `cli/` on the `v1` branch (`git show v1:cli/<path>`) and is never re-added here. `kimaki/CHANGELOG.md` keeps the V1 history.

Other packages: `gateway-proxy/` (Rust, multi-tenant Discord Gateway + REST proxy, hosted on Fly.io), `website/` (kimaki.dev — OAuth callback + onboarding status), `db/` (shared Postgres/Prisma schema for website + gateway; the bot itself doesn't use it).

```
kimaki/ (bot + CLI, local SQLite) ──REST+WS (clientId:secret)──▶ gateway-proxy/ (Rust, fly.io) ──▶ Discord
   │    │                                                             ▲ polls every 1s
   │    └─ polls /api/onboarding/status ──▶ website/ (CF Worker) ──▶ shared Postgres (db/)
   └── one /api/event stream + HTTP client ──▶ user's shared OpenCode V2 service (not bot-owned)
```

Bot data flow:

```
Discord msg ─▶ ingress.ts ─▶ prompt.ts / sessions.ts ─▶ OpenCode (session.prompt, create, interrupt)
OpenCode ev ─▶ opencode-server.ts ─▶ event-loop.ts ─▶ thread-reducer.ts (pure) ─▶ effects.ts ─▶ Discord
```

Key modules: `main.ts` (wiring: lock port → SQLite migration → OpenCode + Discord login in parallel) · `bot.ts`/`store.ts` (context + the one zustand store) · `ingress.ts` (messages, attachments, voice) · `slash-commands.ts` + `commands/*` · `opencode-server.ts` (service discovery, event subscription, plugin shim) · `event-loop.ts` + `thread-reducer.ts` (pure fold → view + effects) · `effects.ts` (the **only** Discord writer for session output) · `lock-server.ts`/`lock-routes.ts` (single-instance lock + local HTTP API for CLI subcommands) · `cli.ts` + `cli/*` (goke CLI) · `plugin/` (runs inside the OpenCode process) · `autostart.ts`.

Build rule: after **every** change run `pnpm build` (tsc) inside `kimaki` — the plugin autostarts the bot from `dist/cli.js`, so stale dist runs old code. Try never to use `as any`.

## OpenCode SDK rules

Kimaki runs on native OpenCode V2. Import the client from `@opencode/client`, plugin types from `@opencode/plugin`. **Never** import `@opencode-ai/sdk` (v1). Calls use flat inputs and return results or reject — no V1 `.data`/`.error` wrapper; paginated results carry `data` + `cursor`.

- `session.create({ location: { directory }, permissions })`
- `session.prompt({ sessionID, text, files, delivery })` returns the inbox item, *not* the reply
- `session.interrupt({ sessionID })` (was `session.abort`)
- `message.list({ sessionID, order, cursor, limit })`, `session.list(...)` — paginated

In the bot, call OpenCode through `oc(bot, 'operation.name', (client) => ...)`, turning rejections into `OpenCodeError` values.

Kimaki uses the **user's shared OpenCode service**, not a bot-owned server. `opencode-server.ts` discovers it or starts it via `Service.ensure()` (binary search: `OPENCODE_PATH`, then `opencode`, `~/.opencode/bin/opencode`, then the rare `opencode2`; minimum version enforced). OpenCode is not bundled and versions are never pinned — that would replace the running server and kill the user's TUI sessions. For OpenCode questions, read the source from `anomalyco/opencode` (not `opencode-ai/opencode`, an unrelated repo).

## Prompt cache

OpenCode V2 keeps the system prompt stable until compaction starts a new instruction epoch, so the provider cache keeps hitting. Put session-level text in **instruction entries** (`session.instructions.entry.put`), never in the plugin `context` hook: `event.system.push(...)` runs on every request and sits before all messages, so any change invalidates the whole cached prefix. Only push text there that stays constant during a session. Kimaki prints `-# ⬦ prompt cache miss: ...` when the cached prefix of a root step shrinks.

## Gateway REST safety

When running with `client_id:secret` through gateway-proxy, Discord REST calls must be **guild-scoped** or explicitly allowlisted. Avoid global application routes like `/applications/{app_id}/commands`; use `/applications/{app_id}/guilds/{guild_id}/commands`. Invariants:

- Never let client-authenticated requests hit unscoped bot-token routes.
- Only tokenized interaction/webhook routes are allowed without auth (`/interactions/{id}/{token}/…`, `/webhooks/{id}/{token}/…`).
- Never treat `/webhooks/{id}` as allowlisted.
- For `AllowedWithoutAuth` routes, don't inject bot `Authorization` upstream.
- **Fail closed** (403/401) when route scope can't be proven guild-scoped or token-scoped.

## Running & restarting

- **Only restart the bot if the user explicitly asks.** `kimaki restart` — the root process supervises the bot as a child and respawns it with on-disk code; sessions keep running in OpenCode.
- Single-instance lock on `KIMAKI_LOCK_PORT` (default **29988**). A second bot on the same port **stops the running one** (SIGTERM, then SIGKILL after 20s) and takes the port. To run alongside the main bot: `KIMAKI_LOCK_PORT=31001 kimaki --data-dir ~/.kimaki-test`. CLI subcommands need the same port as their target bot.
- `kimaki profile cpu` / `kimaki profile heap` write profiles to `<dataDir>/profiles/`.

## SQLite

State persists between runs, and **the DB must never have breaking changes** — new Kimaki must keep working with older SQLite databases; if not, ask the user before adding a startup migration.

- File `<dataDir>/kimaki.db` (libSQL + Drizzle, `db.ts`); only the **bot start** migrates (`openDb({ migrate: true })`), subcommands never do.
- Schema `kimaki/src/schema.ts`; `pnpm generate` writes `src/schema-sql.ts` (idempotent `CREATE … IF NOT EXISTS`) — never edit by hand.
- Migrations live in `kimaki/src/migrations.ts`. First start imports the V1 `discord-sessions.db` read-only. `PRAGMA user_version` marks a Kimaki-made DB.
- **New columns on existing tables**: `IF NOT EXISTS` skips the whole CREATE, so also add an `ALTER TABLE … ADD COLUMN` (ignoring "duplicate column") — and run the ALTER **before** schema statements if a generated index uses the new column, else existing DBs crash with `no such column`. Add a `db.test.ts` case over an old-shaped DB (`fixtures/v1-schema.sql`).
- Inline straightforward Drizzle queries at the call site; no repository layer.

## Plugin (runs inside OpenCode)

`kimaki/src/plugin/` runs in the **OpenCode server process**, not the bot. On start the bot writes a shim per plugin into `<opencode config dir>/plugins/<name>/index.js` re-exporting `dist/plugin/`. Writing the shim only when content changes matters: any change in that folder reloads every location and cancels pending forms. Each plugin is a directory loaded through its `index` module; OpenCode keeps one plugin per ID (`Duplicate plugin ID` otherwise).

- `plugin/index.ts` (`kimaki`): git-branch reminder, `<discord-user>` turn context, extra tool inputs, shell env, file-edit log.
- `plugin/anthropic/index.ts` (`kimaki-anthropic`): Claude Pro/Max OAuth for every session, TUI included.

**The `kimaki` plugin must only act on Kimaki sessions** — OpenCode loads it for every session. Each hook first checks the session's `metadata.kimaki` marker (or the parent chain for subagents) and returns unchanged when missing. Exceptions: the built-in skills and the stable `<dataDir>/bin` shell shim. The plugin gets bot state from the marker, not env vars (`dataDir`, `lockPort`), and never gets the bot token. Plugin files must not import `logger.ts`/`db.ts` or anything pulling in discord.js/SQLite, and must never write to stdout/stderr — fail silently.

## Logging

Always `createLogger('PREFIX')` from `logger.ts`, never console. **Logs go to stderr, never stdout** — stdout is only for command results (`--json`, tables, markdown), onboarding prompts/install URLs, and programmatic `data: {…}` events. Anything else breaks piped commands and leaks into the TUI. Logs also go to `<dataDir>/kimaki.log` (default `~/.kimaki/kimaki.log`), reset on every bot start.

## Discord constraints (hard-won)

- **Rate limits**: global 50 req/s per bot; ~5 sends/5s and ~5 edits/5s per channel; thread rename 2 per 10 min; invalid requests 10k/10min per IP (then a temporary Cloudflare ban); interaction token valid 15 min. `effects.ts` keeps one FIFO worker per thread; merges consecutive bot lines when Discord is slower than the stream. Throttle live edits to ≤1/s (throttle, not debounce), serialize edits, new message past 2000 chars.
- **No TypeScript casts on Discord interaction objects** (`as GuildMember`) — values arrive as hydrated classes or raw API payloads. Narrow at runtime or fetch the member (`canUseKimaki()`). Fail closed on missing context.
- **`custom_id` max 100 chars** — store short IDs only, resolve the rest at interaction time (`resolveTarget(bot, channelId)` from `interaction-context.ts`). Never read the session location or channel row by hand in commands.
- **Message `nonce` max 25 chars** — never send a raw UUID (`NONCE_TYPE_TOO_LONG`).
- **Components V2**: max **40** components total (nested children count); no `content`/`embeds`; `Section` only 1–3 text children + one accessory; `Action Row` ≤5 buttons or one select; `Container` holds Action Row / Text Display / Section / Media Gallery / Separator / File. Don't use `Section` for wide table rows (`BASE_TYPE_BAD_LENGTH`).
- **No emojis** in bot messages; no blank lines between paragraphs in system messages.
- Typing indicator: one pulse ≈10s; reducer emits `typing` on/off, `effects.ts` runs one 7s interval per thread. User messages never start typing — only OpenCode events do.

Message formatting lives in `format-parts.ts`: assistant text is plain Discord markdown, never quoted; tool lines are subtext (`-# ┣ shell _ls_`, edits use `◼︎`); status lines use `⬦`; errors start `✗ `; a successful turn gets a footer (`-# *folder ⋅ branch ⋅ duration ⋅ % ⋅ model*`) — never after interrupt/failure or while a subagent runs.

## Testing

- `pnpm run test --run -u` in `kimaki` after changes to message handling/queueing/reducer; inspect snapshot updates in `git diff`. One file: `pnpm run test --run src/example.test.ts`. (Always include `run` — `pnpm test --run` makes pnpm eat the flag.)
- Each e2e file starts its own OpenCode server (~400 MB). `vitest.config.ts` caps parallel files at ⅓ of CPUs (`KIMAKI_TEST_WORKERS`). Run single files while iterating, the full suite once at the end.
- **Always assert on Discord messages, not internal state or logs.** Prefer reusable actor/wait methods on `DigitalDiscord` (playwright-like). Add `expect(await discord.thread(id).text()).toMatchInlineSnapshot()` first in any test that creates/modifies messages, with deterministic content.
- e2e uses `opencode-deterministic-provider` (model `deterministic-v2`) and canned turns; `waitFor` clamps timeouts to 8–10s. Snapshot only after `waitForFooter` (a `-# *using …*` banner is not a footer). Scope matchers with an explicit marker in the latest user text.

## Product analytics (Strada)

Anonymous install-level events (`bot_started`, `project_registered`, `session_created`, `turn_started`, `turn_completed`, `tokens_used`) — no Discord IDs, paths, prompts, or secrets. Metrics are **active installs**, not people. Disable with `kimaki --no-analytics` or `KIMAKI_STRADA_ENABLED=0` (off under vitest). Query with the `strada` CLI.

## Debugging a session

Read the recorded events before guessing:

```bash
kimaki session events <sessionId|threadId> > ./tmp/events.jsonl
jq -r .event.type ./tmp/events.jsonl | sort | uniq -c
kimaki session read <id>          # messages as markdown
```

Thread events live in `<dataDir>/session-events/<threadId>.jsonl` and can be replayed with `test/replay.ts`.

## Conventions & gotchas

- `errore` submodule = errors-as-values; always on `main`, never detached. Read the errore skill before editing code; shared tagged errors in `errors.ts`.
- CLI parsing uses **goke**, not cac — it infers option types from `.option()`, so never add manual type annotations to `.action()` callbacks.
- **Submodules** (`errore`, `gateway-proxy`, `traforo`, `opencode-injection-guard`): push the submodule branch *first*, then bump the gitlink. Prove the remote has the SHA (`gh api repos/remorses/<repo>/commits/<sha> --jq .sha`) before changing a gitlink. Never force-push a submodule in a way that drops commits Kimaki still points at.
- Never suggest installing from git (`npm i -g remorses/kimaki#main`) — it needs a build step. Point at the next npm release.
- CLI startup must stay light (~0.06s, ~70 MB): `kimaki/src/cli/*.ts` must not top-level import SQLite modules or discord.js — `await import()` inside the actions that need them. Use `discord-api-types/v10` for enums.

## Not ported from V1 yet

Documentation must not claim these exist: self-upgrade (`kimaki upgrade`, `/upgrade-and-restart`) · `sync-skills` · the image-optimizer plugin · the kitty-graphics plugin (images printed by shell tools sent to Discord).

---
Part of [[Kimaki]]. AI-generated with OpenCode + DeepSeek V4.1 Flash.

[[Inbox]]
