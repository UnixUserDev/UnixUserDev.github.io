---
title: Hyprland Setup and Keybinds
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-09-02
source: https://wiki.cachyos.org/configuration/desktop_environments/hyprland/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-09-02).

# Hyprland Setup and Keybinds

Hyprland is a Wayland compositor supporting dwindle (tiling), scrolling, master, monocle and custom layouts. It is **not** a full desktop environment. CachyOS dots are usable out of the box, with apps following the general system theme. Dotfiles maintained by **Jinra** (`https://github.com/Jinra`).

## Config locations

- `~/.config/hypr/config` — `monitors.lua`, `variables.lua`, `input.lua`, `binds.lua`, `windowrules.lua`, `workspaces.lua`, `environment.lua`.
  - `monitors.lua` sets up monitors (edit output values in `variables.lua`; get the id with `hyprctl monitors`, e.g. `DP-1`; `mode` looks like `1920x1080@60`; `scale` is most likely `1`).
  - Workspaces per monitor: define them but **do not exceed 10**. The wiki recommends 3 workspaces per monitor plus the gaming workspace on the primary monitor.
  - `environment.lua` is not required when using UWSM.
- `~/.config/uwsm` — the `env` file for global user variables (e.g. `HYPRCURSOR_THEME`, `XCURSOR_THEME`, `QT_QPA_PLATFORM`).
- `~/.config/noctalia` — `config.toml` holds the default configuration for `cachyos-hypr-noctalia`; you shouldn't need to touch it (V5; V4 files live there but are configured through Noctalia settings).
- Noctalia settings: `Super+Z`. Recommended: enable color templates for the apps you use; there is also a plugin store.

## Notable keybinds

Mod key is Windows/Command (**SUPER**), changeable in `binds.lua`.

| Action | Bind |
|---|---|
| Terminal | `Super+Return` |
| File manager | `Super+E` |
| Editor | `Super+T` |
| Calculator | `Super+C` |
| Browser | `Super+W` |
| btop | `Control+Shift+Escape` |
| Noctalia settings | `Super+Z` |
| Control center | `Super+X` |
| App launcher | `Super+Space` |
| Lock | `Super+L` |
| Color picker | `Super+P` |
| Screen capture & annotate | `Print` |
| Clipboard history | `Super+V` |
| Notification history | `Super+A` |
| Workspace | `Super+<number>` |
| Special workspace (scratchpad) | `Super+S` |

## Migrating from another DE/WM

A new user account is highly recommended; replace everything in angle brackets `<>` including the brackets.

1. `sudo pacman -S cachyos-hypr-noctalia`. If you hit a package conflict with another `-settings` package (e.g. `cachyos-kde-settings`), you can delete the conflicting one — they do not affect current users.
2. `sudo useradd -m <new_user> -s /bin/fish` (another shell such as `/bin/zsh` is fine).
3. `sudo passwd <new_user>`. If you *really* want the same account (**highly discouraged**): `cp -r /etc/skel/. ~` (back up/snapshot first).
4. Optionally install a display manager: `noctalia-greeter` or `sddm`.
   - sddm: `sudo pacman -Syu sddm`, then `systemctl disable display-manager` and `systemctl enable sddm`.
   - Noctalia Greeter: `sudo pacman -Syu noctalia-greeter`, follow the "Setting up greetd" section of its docs, then `systemctl disable display-manager` and `systemctl enable greetd`.
5. Reboot and pick the Hyprland (UWSM) session from the display manager. If the shell fails to load, reboot once more.
6. Log in and follow the Post Setup Tasks.

## Migrating v4 dots → V5 / updating dots

Changelogs: `https://github.com/CachyOS/cachyos-hypr-noctalia/releases`. **Once keybinds are migrated v4 → v5, the old ones no longer work for Noctalia IPC calls.**

1. Install `meld` if missing (`pacman -Q meld` to check).
2. `meld /etc/skel/.config/hypr ~/.config/hypr`
3. Blue files have changes, green files are net new.
4. Merge as you see fit; if you never changed a file it's safe to overwrite it whole. **Do not overwrite what you changed**, such as monitor settings.
5. Save and exit.
6. Net-new green files go into `~/.config/hypr/config/` with the same name. There are 4 new files; renames: `defaults.lua` → `variables.lua`, `keybinds.lua` → `binds.lua`, `input.lua` → `inputs.lua`; `workspaces.lua` is new and can be moved cleanly. Custom additions must be moved by hand.

**Noctalia updates:**

```bash
rm -rf ~/.config/noctalia
cp -r /etc/skel/.config/noctalia ~/.config
```

Only `config.toml` matters, so it can be copied individually instead.

**Removing orphans after the v4 → v5 migration:**

```bash
shelly purify -o
```

Then reboot; if the shell fails to load, reboot once more.

## FAQ

- **Clipboard history doesn't survive reboots** without a secrets store. Kwallet is installed by default (gnome-keyring or another store works too): install `kwalletmanager`, create a new wallet (blank password is easiest), then initialize it in `autostart.lua` with `hl.exec_cmd("kwalletd6")`.
- **Dismissing satty** — `ctrl+s` saves, `ctrl+c` copies. Disable it in Noctalia settings → shell → screenshot.
- **Change keyboard input** — edit `inputs.lua` and add `kb_layout`.
- **Theming gaps** — Cachy's default repo only provides `qt6ct`; for Plasma Qt apps (dolphin, gwenview) get `qt6ct-kde` from the AUR.
- **Default browser** — `xdg-settings set default-web-browser your_browser.desktop` (find the desktop file name with `ls /usr/share/applications/`).

Related: [[Switching Desktop Environments]], [[CachyOS Desktop Environments]].
