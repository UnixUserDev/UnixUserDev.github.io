---
tags:
  - memory/cachyos
created: 2026-10-20T13:44:57
source: agent
---

Discord desktop on this machine self-updates into ~/.config/discord (pacman package is only a bootstrap; its version lags, which is normal). It can freeze forever on the "Discord Updater" splash at 90%.

Diagnosis: installs/downloads both report 10/10 but progress stays 90; updater threads sit idle in futex/epoll; a 0-byte file in ~/.config/discord/download/incoming/.tmp* opened at the stall time is the dead download; the last module (e.g. discord_spellcheck) never appears in .app-<ver>.installing/modules; installer.db-journal left behind. Cause: Discord's CDN (stable.dl2.discordapp.net) connection returns no bytes and the downloader has no read timeout, so it waits forever. Not disk, not permissions, not the bwrap camera wrapper.

Fix: kill the whole Discord tree (bwrap launcher + splash), delete ~/.config/discord/.app-<ver>.installing and download/incoming/*, relaunch /OpenCode-Sandbox/camera-pose/discord-nocam.sh --url --. Next run re-stages and finishes.

[[Inbox]]

[[Inbox]]
