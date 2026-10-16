---
tags:
  - memory/linux
created: 2026-10-16T01:22:06
source: agent
---

On the CachyOS box, fastfetch (2.70) can be made to display any other distro. There is no runtime override for the OS detection — paths are compile-time macros, no --target-dir flag. Three ways: (1) `fastfetch --logo <name>` changes only the ASCII (544 built-ins via --list-logos; the OS line still says CachyOS). (2) A JSON config with `"logo": {"source": "..."}` plus `custom` modules for faked key/values — note `custom`/`command` modules cannot be used via -s/--structure, only in JSON. A working one lives at /OpenCode-Sandbox/Temp/larp.jsonc. (3) Full unprivileged spoof: fake an os-release file and bind-mount it with bwrap — `bwrap --dev-bind / / --ro-bind Temp/fake-os-release /etc/os-release -- fastfetch -s title:os:logo` — which auto-picks the logo and rewrites the OS line. Gotchas: Ubuntu flavour detection reads XDG_CONFIG_DIRS (KDE -> reports Kubuntu; set --setenv XDG_CONFIG_DIRS /etc/xdg for plain Ubuntu); the hostname still leaks via the title module; a fake ID=windows auto-picks the legacy tilted-flag logo, not the flat "Windows 11" one.

[[Inbox]]
