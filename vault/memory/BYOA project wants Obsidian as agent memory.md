---
tags:
  - memory/projects
created: 2026-10-18T04:50:18
source: agent
---

User is designing "BYOA" (bring your own everything): a local-first coding-agent harness at /OpenCode-Sandbox/byoa, Node 26 + TypeScript, no build step (native type-stripping), node:sqlite for persistence, bubblewrap for the sandbox, OpenTUI on Node for the TUI, plus CLI and Web frontends (optional Electron, not shipped by default). Three system-prompt tiers: lite (small/local models), normal (cloud), large (max precision).

Key user preference: the agent's long-term memory should be Obsidian (markdown notes in the vault), NOT a database — the user prefers Obsidian because it saves tokens via targeted retrieval and looks satisfying to browse. A plain SQLite memory backend should stay available as a fallback. Vault conventions still apply (human-readable titles with no dates, one group tag per note).

[[Inbox]]
