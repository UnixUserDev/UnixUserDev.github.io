---
tags:
  - memory/projects
created: 2026-10-16T20:02:06
source: agent
---

Roblox Studio's MCP server (StudioMCP.exe, "studio_mcp_proxy") now works on Linux. The wrapper is /OpenCode-Sandbox/roblox-studio-mcp/roblox-studio-mcp.sh (registered in OpenCode as the local MCP server "roblox-studio"). KEY FIX: do NOT run the proxy under Vinegar's bundled patched Wine ("kombucha") — its socket layer never delivers readiness to tokio, so the proxy binds 127.0.0.1:13469 but never accepts a connection, tools/list stays empty, and Studio's main thread hangs (StudioHangMonitor) which wedges the wineserver. Run StudioMCP.exe with STOCK SYSTEM WINE in its own prefix (~/.wine-studiomcp) instead, with <prefix>/drive_c/users/<user>/AppData/Local/Roblox symlinked to Vinegar's appdata/Roblox so it shares auth.token and ClientSettings. Studio<->proxy is pure TCP loopback, so no shared Wine prefix is needed. Verified: Studio connects and the MCP client receives 28 tools; Studio survives proxy disconnects without hanging.

[[Inbox]]
