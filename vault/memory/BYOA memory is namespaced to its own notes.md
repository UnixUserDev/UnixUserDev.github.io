---
tags:
  - memory/projects
created: 2026-10-19T10:43:51
source: agent
---

BYOA's Obsidian memory store is scoped to the memory/byoa/ subfolder so it can only read and write notes it created; the OpenCode agent reads the whole vault (including memory/byoa/). Implemented with a namespace parameter on ObsidianMemoryStore/SqliteMemoryStore, and notes carry source: byoa. Files live in ~/Documents/Obsidian Vault/memory/byoa/.

[[Inbox]]
