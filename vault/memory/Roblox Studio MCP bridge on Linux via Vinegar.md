---
tags:
  - memory/projects
created: 2026-10-16T20:02:06
source: agent
---

Roblox Studio's MCP server (StudioMCP.exe, "studio_mcp_proxy") works on Linux only by running Studio's own mcp.bat inside the Vinegar Flatpak Wine prefix. The wrapper is /OpenCode-Sandbox/roblox-studio-mcp/roblox-studio-mcp.sh (deliberately NOT registered as an OpenCode MCP server yet, because attaching it hangs Studio). Confirmed working: stdio MCP initialize returns serverInfo.name=RobloxStudio, and the proxy binds 127.0.0.1:13469. BLOCKER (reproduced twice): Studio's WebSocket upgrade to the proxy never completes, so tools/list stays empty; and when the proxy process exits, Studio's main thread hangs (StudioHangMonitor) and wedges the wineserver, requiring a full Vinegar restart.

[[Inbox]]
