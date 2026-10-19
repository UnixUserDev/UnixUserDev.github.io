---
tags:
  - memory/projects
created: 2026-10-19T15:55:36
source: byoa
---

BYOA is at v0.1.0 (git tag v0.1.0), released publicly at https://github.com/UnixUserDev/byoa under the MIT license.

Included: the core agent loop, OpenAI-compatible and Anthropic providers (plus local servers such as LM Studio, Ollama and llama.cpp), session persistence with resume, the shell and file tools, the bubblewrap sandbox with a read-allowlist skeleton root, three prompt tiers (lite/normal/large), and namespaced Obsidian-backed memory.

Not included yet: TUI, Web and Electron adapters, MCP, subagents, and web fetch/search.

Quality: 184 tests passing including real-bubblewrap isolation tests, tsc --noEmit clean. Requires Node 26 or newer; bubblewrap is needed for sandboxing, and strict mode (the default) refuses to run unwrapped when it is missing.

[[Inbox]]
