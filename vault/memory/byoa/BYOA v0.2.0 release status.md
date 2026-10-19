---
tags:
  - memory/projects
created: 2026-10-19T16:22:28
source: byoa
---

Version: 0.2.0
Release tag: v0.2.0
Repository: https://github.com/UnixUserDev/byoa
Releases: https://github.com/UnixUserDev/byoa/releases
License: MIT

BYOA stands for Bring Your Own Agent: a local-first, bring-your-own-agent coding harness, released publicly on GitHub.

Included: the core agent loop, OpenAI-compatible and Anthropic providers (plus local servers such as LM Studio, Ollama and llama.cpp), session persistence with resume, the shell and file tools, the bubblewrap sandbox with a read-allowlist skeleton root, three prompt tiers (lite/normal/large), namespaced Obsidian-backed memory, and a TUI (--tui) on OpenTUI.

Not included yet: Web and Electron adapters, MCP, subagents, and web fetch/search.

Distribution: releases ship built artifacts (standalone tarball with node_modules beside the launcher, and an npm-installable tarball). The source tree is not an installable package because Node cannot type-strip TypeScript inside node_modules.

Quality: 212 tests passing including real-bubblewrap isolation tests and headless OpenTUI render tests; tsc --noEmit clean. Requires Node 26 or newer; bubblewrap is needed for sandboxing, and strict mode (the default) refuses to run unwrapped when it is missing.

[[Inbox]]
