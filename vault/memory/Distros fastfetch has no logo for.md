---
tags:
  - memory/linux
created: 2026-10-16T01:26:04
source: agent
---

fastfetch 2.70 ships 544 built-in logos but has no art for a long list of real distros. Verified by running `fastfetch -s logo --logo <name> --show-errors true` (non-zero output only appears with --show-errors; without it fastfetch silently falls back to the current distro's logo and exits 0, which makes naive testing useless). Confirmed missing, on both the installed 2.70 and upstream dev (logos live per-letter in src/logo/ascii/<letter>/): Tiny Core Linux (ID=tinycore — it does have /etc/os-release, so fastfetch identifies it as "TinyCoreLinux" and then draws Tux), Damn Small Linux, Bazzite (ID=bazzite, falls back to the Fedora logo — byte-identical), postmarketOS, Slax, Batocera, Lakka, grml, CrunchBang (its fork BunsenLabs IS supported), Red Hat Linux, Corel Linux, MEPIS, Kanotix, Zenwalk, VectorLinux, Sabayon, Yellow Dog, Turbolinux, Vine, DD-WRT, Smoothwall, MeeGo, Maemo, Moblin, Tizen, Firefox OS, ChromiumOS, Softlanding Linux System, MCC Interim Linux, and Winux/Wubuntu (the Windows 11 clone — while fastfetch does ship a Windows 11 logo). Tiny Core's absence has zero GitHub issues mentioning it. Also: `--logo` in `-s/--structure` prints a bogus "Logo: Supported in JSON format only" line regardless of distro.

[[Inbox]]
