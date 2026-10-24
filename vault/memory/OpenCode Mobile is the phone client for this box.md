---
tags:
  - memory/projects
created: 2026-10-21T22:00:51
source: agent
---

OpenCode Mobile lives at /OpenCode-Sandbox/opencode-mobile — a zero-dependency Node gateway (server.js) that serves a mobile web UI and reverse-proxies /api/* to the local OpenCode V2 service on 127.0.0.1, injecting its basic auth. The phone opens http://192.168.1.182:10999 and logs in with a shared password + HMAC cookie. Runs as systemd --user `opencode-mobile.service` (enabled, linger on). The old Windows-era :10999 setup is dead; this replaced it. Details: docs/API.md (V2 API contract), README.md (ops). Linked from [[Inbox]].

[[Inbox]]
