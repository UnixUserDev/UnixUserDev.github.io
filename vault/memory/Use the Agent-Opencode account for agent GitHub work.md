---
tags:
  - memory/linux
created: 2026-10-16T02:28:29
source: agent
---

User wants the Agent-Opencode GitHub account (created 2026-10-16, display name Agent-Opencode) used for all of the agent's GitHub activity from now on, instead of their personal UnixUserDev account. The token is exposed as GH_TOKEN_AGENT (the gh-thread-watch watcher already prefers it over GH_TOKEN); use the ~/.local/bin/gh-agent wrapper to run gh as that account. As of setup it has 0 repos and only public read access, so it still needs: a fork of the target repo, plus Contents: Read and write and Pull requests: Read and write on the token. Exception: fastfetch PR #2655 and issue #2654 already live under UnixUserDev and should stay there rather than being migrated.

[[Inbox]]
