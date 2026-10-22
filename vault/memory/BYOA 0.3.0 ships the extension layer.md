---
tags:
  - memory/projects
created: 2026-10-22T12:06:31
source: agent
---

BYOA (github.com/UnixUserDev/byoa) shipped v0.3.0 with an extension layer built as one feature: plugins (~/.config/byoa/plugins, enabled by name only, tools namespaced plugin__<id>__<tool>), an MCP client (stdio + Streamable HTTP, tools as mcp__<server>__<tool>, default mutating so --ask gates them), and subagents (the `task` tool, child sessions linked by sessions.parent_id, shown in `byoa sessions --tree`). Test count went 218 -> 300; DB schema v2. Deliberate trust boundaries documented rather than implied: plugins run in-process with full privileges, MCP servers are host-side and --net deny does not reach them. Supersedes the "BYOA v0.2.0 release status" note.

[[Inbox]]
