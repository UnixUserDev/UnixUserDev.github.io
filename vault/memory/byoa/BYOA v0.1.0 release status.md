---
tags:
  - memory/projects
created: 2026-10-19T16:01:59
source: byoa
---

Version: 0.1.0
Release tag: v0.1.0
Repository: https://github.com/UnixUserDev/byoa
License: MIT

BYOA stands for Bring Your Own Agent: a local-first, bring-your-own-agent coding harness, released publicly on GitHub.

Included: the core agent loop, OpenAI-compatible and Anthropic providers (plus local servers such as LM Studio, Ollama and llama.cpp), session persistence with resume, the shell and file tools, the bubblewrap sandbox with a read-allowlist skeleton root, three prompt tiers (lite/normal/large), and namespaced Obsidian-backed memory.

Not included yet: TUI, Web and Electron adapters, MCP, subagents, and web fetch/search.

Quality: 184 tests passing including real-bubblewrap isolation tests, tsc --noEmit clean. Requires Node 26 or newer; bubblewrap is needed for sandboxing, and strict mode (the default) refuses to run unwrapped when it is missing.

[[Inbox]]
