---
tags:
  - memory/projects
created: 2026-10-21T21:21:06
source: agent
---

Two WiZ bulbs in the room, both ESP03_SHRGB1C_01 on fw 1.38.0, controlled by JSON over UDP 38899 (no cloud/app needed). 192.168.1.225 (mac 6c2990361934) is the LEFT light; 192.168.1.226 (mac 6c29902628a1) is the RIGHT one. Confirmed 2026-10-21 by lighting one red and one blue and having the user call which side the red was on. Broadcast discovery fails on this Wi-Fi (AP filters it) — unicast to each IP works. Helper: /OpenCode-Sandbox/wiz-lights/wizctl.py with `status`, `on <left|right|all> --color --dim`, `off all`.

[[Inbox]]
