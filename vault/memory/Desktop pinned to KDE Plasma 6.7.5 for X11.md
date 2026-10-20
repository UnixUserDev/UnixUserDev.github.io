---
tags:
  - memory/system
created: 2026-10-20T09:25:27
source: agent
---

The desktop is pinned at KDE Plasma 6.7.5 with the X11 session (plasmax11.desktop), because Plasma 6.8 deleted the X11 session upstream and the user needs X11. 47 packages are held via IgnorePkg in /etc/pacman.conf. plasma-login-manager must stay at 6.7.x: the 6.8 greeter cannot launch X11 sessions. Rollback notes live in /OpenCode-Sandbox/Temp/plasma-x11-rollback/ROLLBACK.md, with the 6.8 packages kept in the pacman cache.

[[Inbox]]
