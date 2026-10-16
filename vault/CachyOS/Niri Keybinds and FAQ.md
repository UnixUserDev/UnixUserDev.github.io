---
title: Niri Keybinds and FAQ
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-01-25
source: https://wiki.cachyos.org/configuration/desktop_environments/niri/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-01-25).

# Niri Keybinds and FAQ

Niri is a **scrollable-tiling Wayland compositor** with a column-based layout: windows are arranged in columns you scroll horizontally (an "infinite desktop" metaphor — good for ultrawide monitors and many open apps).

CachyOS dotfiles are maintained by **Ly-sec** (`https://github.com/Ly-sec`). For Noctalia issues see `https://docs.noctalia.dev/`.

The mod key is the Windows key (**SUPER**); it can be changed in the config file.

## Notable keybinds

| Action | Bind |
|---|---|
| Terminal | `Super+Return` |
| App launcher | `Super+Control+Return` |
| Browser (Firefox) | `Super+B` |
| File manager | `Super+E` |
| Close window | `Super+Q` |
| Lock | `Super+Alt+L` |
| Screenshots | `Control+Shift+1/2/3` |
| Emergency escape (restore control) | `Super+Escape` |
| Quit Niri | `Control+Alt+Delete` |

Documented bind categories: Application Control, Workspace Navigation, Window Navigation, Monitor Navigation, Window Management, Layout Controls, Window Modes, Screenshots, System Controls, Media Controls, Advanced Navigation.

## Common config changes

**Keyboard layout** — edit the input section:

```
input { keyboard { xkb { layout "us" // Change from "de" to your preferred layout } } }
```

**Gaps / window appearance** — the layout section:

```
layout {
    gaps 8 // Change gap size
    focus-ring {
        width 2
        active-color "#your-color"
        inactive-color "#your-color"
    }
}
```

**Startup applications**:

```
spawn-at-startup "your-application"
```

**Multiple monitors** — uncomment and modify the output section. Get the correct names first:

```bash
niri msg outputs
```

```
output "DP-1" {
    mode "1920x1080@60.000"
    scale 1
}
```

## FAQ

- **Stuck in a fullscreen app that eats keybinds?** `Super+Escape` restores control — it disables any active keyboard shortcut inhibitor.

Related: [[Switching Desktop Environments]], [[CachyOS Desktop Environments]].
