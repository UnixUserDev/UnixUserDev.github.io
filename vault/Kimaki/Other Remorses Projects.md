---
title: Other Remorses Projects
tags:
  - note/remorses
created: 2026-10-16
source: https://github.com/remorses
---

# Other Remorses Projects

**Remorses** = **Tommy D. Rossi**, Italy, [@\_\_morse](https://x.com/__morse) on X — ~674 public repos, 1.2k followers, 5k+ stars. Same author as [[Kimaki]]. Themes: agent tooling, terminal UIs, and doing docs/dev-tooling the open way.

## The big ones

### Playwriter
Chrome extension + CLI (+ MCP) that lets **agents drive your own running Chrome** — your logins, cookies and extensions already there. Other browser MCPs spawn a fresh Chrome (no logins, no extensions, instantly bot-flagged, double the RAM); Playwriter connects to the browser you already have via an extension and a localhost WebSocket relay (`:19988`). One `execute` tool with the full Playwright API and persistent `state` across calls, Vimium-style accessibility labels, raw CDP, native video, tab groups, remote control (share your authenticated browser with a remote agent over a Cloudflare tunnel). ~4k★.

```bash
npm i -g playwriter
playwriter browser start
playwriter session new
playwriter -s 1 -e 'state.page = await context.newPage(); await state.page.goto("https://example.com")'
```

- <https://github.com/remorses/playwriter> · <https://playwriter.dev>
- Note: Chrome ignores `--remote-debugging-port` on your default profile since **Chrome 136**, so the extension route is the only one that keeps your real profile.
- Telemetry goes to Strada (anonymous); opt out with `PLAYWRITER_TELEMETRY=0` or `DO_NOT_TRACK=1`.

### Holocron
**Drop-in, open-source replacement for Mintlify** as a Vite plugin. Same `docs.json` config shape, same MDX components, same frontmatter — migrate in ~2 minutes. Local `vite build`, deploy to Node/Cloudflare/holocron.so hosting; free vs Mintlify's $150/mo. Ships AI-readable exports out of the box: `.md` route per page, `/llms.txt`, `/docs.zip`, and skill discovery at `/.well-known/agent-skills/index.json`. ~800★.

```bash
npx -y "@holocron.so/cli" create
```

- <https://github.com/remorses/holocron> · <https://holocron.so>
- Built on Spiceflow (React Server Components) + Tailwind; search via Orama; OpenAPI reference pages.

### Termcast
**Arc/Raycast-style TUI framework**: build terminal apps with React using the [Raycast extension API](https://developers.raycast.com). Ports Raycast extensions (`@raycast/api` aliases to `termcast` at build time), hot module reloading via React Refresh, compiles to standalone executables, can even bundle a desktop app with an embedded terminal. **Requires Bun** (auto-respawns under bun if you run the TUI from a Node CLI). Terminal-only extras: braille charts, bar/stacked graphs, candlestick, calendar heatmap, tables. ~340★.

```bash
pnpm install -g termcast
termcast new my-extension
termcast dev
```

- <https://github.com/remorses/termcast> · <https://termcast.app>
- Profile TUIs with `termcast dev` + Bun `--cpu-prof`, analyze with **profano** (also his).

### Critique
**Terminal + web diff viewer.** `critique` in the repo opens the working-tree diff in a TUI (split view, word-level diffs, Tree-sitter highlighting, watch mode, file switcher, click-to-open in `$REACT_EDITOR`); `--web "title"` uploads it to critique.work and prints a shareable URL with a `.patch` endpoint. Also: GitHub PR URLs without a checkout, `--pdf` for e-readers, `--calldiff` (call-stack diffs), `critique hunks` (stable hunk IDs for agents — a scriptable `git add -p`), password-protected links, `critique pick` from another branch. Bun-only. ~1.3k★.

```bash
bunx critique                      # working tree, TUI
critique --web "Fix auth retry"    # shareable URL
critique https://github.com/owner/repo/pull/19 --web "PR #19"
```

- <https://github.com/remorses/critique> · <https://critique.work>
- Uploads expire after 7 days without a license key; identical diffs reuse a content-hash URL.

### gpuix
**React and Solid for [GPUI](https://github.com/zed-industries/zed/tree/main/crates/gpui)** — Zed's GPU UI framework. Write JSX/`useState` in TypeScript; GPUIX paints it with Metal / DirectX / Vulkan / WebGPU. No Electron, no web view. A napi-rs native bridge (Rust RetainedTree) plus a browser wasm build; the framework adapter sends only changed elements as atomic mutation batches. Animations run natively in Rust (no per-frame React render). Ships `bunx @gpuix/cli new`, standalone binaries via `bun build --compile` + cargo-packager, and an automation API that can click/screenshot a window **without stealing your focus** (`focus: false`). ~2.6k★.

- <https://github.com/remorses/gpuix>

### Zele
**Multi-account email & calendar CLI** — Gmail, Outlook/Microsoft (XOAUTH2), IMAP/SMTP (Fastmail, Proton Bridge, anything), plus free receive-only `@zele.sh` inboxes (up to 10). SQLite cache, **YAML output** for piping through `yq`. Read/search/send/reply/forward, drafts, labels, filters, attachments, Google Calendar, `mail watch` to block until a matching email arrives. Built for agents: `zele --help` is the source of truth, never use the TUI, run `zele whoami` before `--account`, reply refuses to send on a stale view. Interactive TUI needs Bun; CLI works on Node ≥22.16.

```bash
npm install -g zele
zele mail list --filter "is:unread" --limit 100 | yq '.[].id' | xargs zele mail read
```

- <https://github.com/remorses/zele>
- This environment already has a dedicated `zele` skill.

## Smaller / supporting

| Project | What it is |
| --- | --- |
| **goke** | CLI parser that infers option types from `.option()` calls (used by Kimaki, zele, termcast). |
| **errore** | Errors-as-values for TypeScript (errore.org); a Kimaki submodule. |
| **traforo** | Tunnels (traforo.dev) — used by Playwriter and Kimaki for remote access. |
| **bundless** | Dev server + bundler based on esbuild. |
| **profano** | CLI that reads V8 `.cpuprofile` files and prints top functions by self/total time. |
| **sigillo** | Release-notification bot Kimaki uses to post to `#changelog`. |
| **tuitube** | A termcast-based YouTube TUI; the reference example for `termcast release`. |
| **discord-digital-twin** | In-process fake Discord used by Kimaki's e2e tests. |
| **opencode-deterministic-provider** | Canned-response model provider for deterministic agent tests. |
| **sunder** | (Not his — that's [freedomofpress/sunder](https://github.com/freedomofpress/sunder), Shamir's Secret Sharing GUI. Don't confuse them.) |

Most of these ship an installable skill for coding agents:

```bash
npx -y skills add remorses/termcast        # or holocron, critique, ...
```

---
Part of the [[Kimaki]] bundle. AI-generated with OpenCode + DeepSeek V4.1 Flash.

[[Inbox]]
