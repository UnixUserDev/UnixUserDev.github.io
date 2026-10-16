---
title: i3 Keybinds and FAQ
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2025-11-02
source: https://wiki.cachyos.org/configuration/desktop_environments/i3/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2025-11-02).

# i3 Keybinds and FAQ

Credits to **vnepogodin** (`https://github.com/vnepogodin`) for the simple config. **X11 only — not compatible with Wayland.**

Mod key is the Windows key (**SUPER**), changeable in the config file.

## Notable keybinds

| Action | Bind |
|---|---|
| Terminal | `Super+Return` |
| Rofi program launcher | `Control+Space` |
| Kill focused window | `Super+Q` |
| Workspaces | `Super+1` … `Super+9` |
| Resize mode | `Super+R` (exit with Return / Escape / `Super+R`) |
| Lock screen | `Super+L` |
| Reload i3 config | `Super+Shift+C` |
| Restart i3 in place | `Super+Shift+R` |
| Exit i3 (end X session) | `Super+Shift+E` |

Documented categories: Application Control, Workspace Navigation, Window Navigation, Window Management, Layout Control, Resize Mode, System Controls.

## FAQ

**Autostart a program** (e.g. set a wallpaper at start) — i3's flexible autostart feature. At the end of the config file there are lines starting with `exec` and `exec_always`:

```
exec chromium
```

That starts Chromium in workspace 1 whenever you log into your i3 session.

**`exec` vs `exec_always`** — `exec_always` runs the action even after i3 gets reloaded. Reference: `https://i3wm.org/docs/userguide.html#exec`.

**Do multimedia keys work?** Yes. General reference: `https://i3wm.org/docs/userguide.html`.

Related: [[Switching Desktop Environments]], [[CachyOS Desktop Environments]].
