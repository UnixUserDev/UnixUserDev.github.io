---
title: Qtile Keybinds and FAQ
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2025-11-02
source: https://wiki.cachyos.org/configuration/desktop_environments/qtile/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2025-11-02).

# Qtile Keybinds and FAQ

Credits to **Shendisx** (`https://github.com/Shendisx`). Provides **both X11 and Wayland** sessions.

Mod key is the Windows key (**SUPER**), changeable in the config; some binds use mod1 (ALT).

## Notable keybinds

| Action | Bind |
|---|---|
| Terminal | `Super+Return` |
| Rofi program launcher | `Alt+Space` |
| File manager (Thunar) | `Super+E` |
| Kill focused window | `Super+Q` |
| Workspaces | `Super+1` … `Super+9` |
| Drag floating window | `Super+Left_Click` |
| Resize floating window | `Super+Right_Click` |
| Bring window to front | `Super+Scroll_Wheel_Button` |
| Run Flameshot | `Print` |
| Full-screen screenshot (saved to `~/Pictures`) | `Control+Print` |
| Reload Qtile config | `Super+Control+R` |
| Exit Qtile (end X session) | `Super+Control+Q` |

Documented categories: Application Control, Workspace Navigation, Window Navigation, Window Management, Window Resizing, Layout Control, Mouse Controls, Screenshot, System Controls.

## FAQ

- **Volume widget shows an error or is stuck at 0%** — sometimes Qtile's volume widget cannot detect your default output device. See `https://docs.qtile.org/en/latest/manual/ref/widgets.html#pulsevolume`.
- **Is there an `autostart.sh`?** Yes — it lives in `scripts/` inside the Qtile folder.
- **Does the bar respond to the mouse?** Yes:
  - Scrolling on the workspace dots (Active/Inactive/Empty) switches left/right; clicking one switches to it.
  - Clicking the layout (columns by default) cycles available layouts.
  - Clicking CPU/RAM usage opens Btop (TUI system monitor).
  - Interacting with the volume widget raises/lowers/mutes.
  - More: `https://docs.qtile.org/en/stable/`.

Related: [[Switching Desktop Environments]], [[CachyOS Desktop Environments]].
