---
title: Kimaki
tags:
  - note/kimaki
created: 2026-10-16
source: https://github.com/remorses/kimaki
---

# Kimaki

A **collaborative agent orchestrator that lives inside Discord**, by [[Other Remorses Projects|Remorses]] (Tommy D. Rossi). It drives every feature of [[Kimaki Internals|OpenCode]] from Discord: **each Discord channel is a project, each thread is a coding session.** Send a message, an agent edits code on your machine.

Not a chat toy — the author runs *all* his development through it. Docs: <https://kimaki.dev>. Source: <https://github.com/remorses/kimaki>. Try it: [Kimaki Discord](https://discord.gg/qz3hapKcMM).

## Quick start

```bash
npx -y kimaki@latest
```

Setup is ~1 minute: install the bot to your server with one click, and Kimaki opens an onboarding thread where the agent asks which projects you want (one channel per project).

Requires **OpenCode 2**. If none is installed, Kimaki installs it (`curl -fsSL https://opencode.ai/v2/install | bash`). Had OpenCode 1? Install v2 first with the same command.

## The model (why it's split this way)

Other Discord/iMessage agent tools cram everything into **one channel**; sessions pile up. Kimaki maps work onto Discord's native structure instead:

- **Channels are projects** — linked to a directory on your machine.
- **Threads are sessions** — every message starts a thread mapping to one OpenCode session.

Switch projects by switching channels, tasks by switching threads. Search, resume, and fork later.

```
Discord server            Your machine
#web-app  ─────────────▶  /code/web-app  ──▶ OpenCode session (thread)
#api      ─────────────▶  /code/api      ──▶ OpenCode session (thread)
   ▲ thread = session            │ agent reads/edits/runs in the project dir
   └── agent replies ◀───────────┘
```

## Models & subscriptions

All models OpenCode supports (Anthropic, OpenAI, Google, …). Crucially: use your existing **Claude Pro/Max** and **ChatGPT/Codex** subscriptions instead of per-token billing — run `/login`, pick a provider, choose OAuth. Logins are stored by OpenCode, so they work in Discord *and* the TUI. Kimaki adds Claude Pro/Max login to OpenCode via a small plugin.

## Core features

- **Routines** — run the bot on a schedule (cron or a future time). e.g. every morning read your inbox with [[Other Remorses Projects|Zele]] and post an email-digest thread.
- **The queue** — queue a message for when the current run finishes. End any message with `. queue` (editable later) or use `/queue`.
- **btw** — fork the current context into a new thread to ask a side question in parallel while the agent keeps working. `. btw` or `/btw`.
- **Worktrees** — `/new-worktree` starts a session in an isolated git worktree; `/merge-worktree` merges back (rebase or squash).
- **Diff viewer** — `/diff` generates a shareable review URL you can open from your phone.
- **Voice messages** — record a voice note; transcribed using your project's file tree for accuracy.
- **Voice calls** — join the *Kimaki voice* channel and talk; GPT-Live answers on your ChatGPT subscription and a fast backend model starts/reads threads. Default voice `cove`; `kimaki --voice juniper` changes it (persisted in SQLite), `--voice default` resets.
- **Images / files** — attach to a message; the agent can also upload files back into the thread.
- **OpenCode commands & skills** — become Discord slash commands.
- **Shell commands** — prefix a message with `!` to run a shell command in the session folder.
- **Tunnels** — expose a local dev server to a public URL (phone access).
- **Quick agent switching** — swap model or system prompt with `/<name>-agent`.
- **Same sessions in the TUI** — Kimaki uses your OpenCode 2 background service, so every session also opens in the OpenCode TUI. `/session-id` prints the command.

## Message handling

A message sent during an active run **interrupts** it: Kimaki cancels pending questions and permission prompts, stops the run, and steers your new input in immediately. To wait instead, end with `. queue` or use `/queue`.

## Commands

| Command | Description |
| --- | --- |
| `/resume <session>` | Resume a previous session (autocomplete) |
| `/abort` | Stop the current running session |
| `/model` | Change the AI model for this channel/session |
| `/agent` | Change the agent for this channel/session |
| `/login` | Authenticate a provider (OAuth subscription or API key) |
| `/queue <message>` | Queue a message for after the current response |
| `/btw <prompt>` | Fork context into a new thread for a side question |
| `/new-worktree <name>` | Start a session in an isolated git worktree |
| `/merge-worktree` | Merge this worktree into a local branch |
| `/diff` | Generate a shareable diff URL |
| `/session-id` | Show the session ID + the command to open it in the TUI |

Full list: <https://kimaki.dev/docs/reference/commands>.

## Setup modes

- **Gateway mode (default, recommended)** — uses Kimaki's pre-built Discord bot; no Developer Portal setup. One install link, authorize, done.
- **Self-hosted** — make your own bot at <https://discord.com/developers/applications> (~5–10 min) for full control of the bot identity.

Both behave the same after setup. Keep the CLI running — it's the bridge between Discord and your machine. Agents installing on a laptop/VPS should follow [Set up Kimaki from an agent](https://kimaki.dev/docs/guides/agent-machine-setup) rather than cloning the repo.

## Access control

A user needs **one** of: Server Owner · Manage Server · Administrator · a role literally named **`Kimaki`** (case-insensitive). Messages from anyone else are ignored. The `Kimaki` role is the recommended team approach.

- **Block** a user with a role named **`no-kimaki`** — yes, even blocks server owners.
- **Other bots** are ignored. For script/CI sessions use `kimaki send`.

## Best practices

- Run a **dedicated Discord server** for your agents.
- Use the **`Kimaki` role** for team access.
- Send **long prompts as file attachments** ("Send message as file").

## Troubleshooting

- Sessions stop responding / won't start → restart the OpenCode service: `opencode service restart` (Kimaki reconnects itself).
- Bot itself broken (messages not picked up, no threads) → `kimaki restart`.
- Update → `npm i -g kimaki@latest`, then `kimaki restart`.
- Logs → `kimaki logs` (path printed; `--follow` tails).

Full guide: <https://kimaki.dev/docs/guides/troubleshooting>.

## Advanced

[Channels & Threads](https://kimaki.dev/docs/core-concepts/channels-threads) · [Models & Subscriptions](https://kimaki.dev/docs/getting-started/subscriptions) · [CI & Automation](https://kimaki.dev/docs/guides/ci-automation) · [Routines](https://kimaki.dev/docs/features/routines) · [Advanced Setup](https://kimaki.dev/docs/guides/advanced-setup) · [Docker](https://kimaki.dev/docs/guides/docker) · [[Kimaki Internals]]

---
Part of the Kimaki bundle, together with [[Kimaki Internals]] and [[Other Remorses Projects]]. AI-generated with OpenCode + DeepSeek V4.1 Flash.

[[Inbox]]
