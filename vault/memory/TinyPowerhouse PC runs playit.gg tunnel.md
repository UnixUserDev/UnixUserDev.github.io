---
tags:
  - memory/homelab
created: 2026-10-17T04:53:01
source: agent
---

TinyPowerhouse is a Windows 11 PC on the LAN at 192.168.1.178 (mDNS TinyPowerhouse.local), user `Space`. Reachable via SSH (:22, OpenSSH) and WinRM (:5985). It runs the playit.gg agent as Windows service `playitd` (auto-start, LocalSystem), version 1.0.10, installed at C:\Program Files\playit_gg\bin, config C:\ProgramData\playit_gg\playit.toml, logs C:\ProgramData\playit_gg\logs\playitd.log. Started with `sc start playitd`.

[[Inbox]]
