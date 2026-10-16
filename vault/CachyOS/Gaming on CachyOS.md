---
title: Gaming on CachyOS
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-07-26
source: https://wiki.cachyos.org/configuration/gaming/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-07-26).

# Gaming on CachyOS

Prerequisite: working GPU drivers.

Essential packages live in the meta-packages `cachyos-gaming-meta` and `cachyos-gaming-applications` (CachyOS Hello → Apps/Tweaks → "Install Gaming packages" installs both).

## Proton-CachyOS

Based on Proton's `bleeding-edge`: Wine-staging patches, Wine Fullscreen FSR, video/audio codecs for cutscenes, `umu-launcher` + UMU-Protonfixes support, early game hotfixes. Disable with `PROTON_NO_NTSYNC=1`.

## Steam launch option pattern

```
[env variables] [wrappers] %command% [application arguments]
```

Examples:

```
PROTON_DXVK_D3D8=1
```

```
DXVK_HUD="fps,memory,version,api"
```

```
mangohud --dlsym
```

```
gamescope -W 1680 -H 1050 -w 1280 -h 720 -S fit -F fsr --mangoapp --
```

```
__GL_SHADER_DISK_CACHE_SKIP_CLEANUP=1 prime-run game-performance %command% -dx11
```

> **Only the first `%command%` counts** — everything after it is treated as game arguments. Never chain multiple `%command%` separators.

## umu-launcher (Lutris / Heroic)

```bash
sudo pacman -S cachyos/umu-launcher
```

- Lutris Runner Options: Wine version = `proton-cachyos-slr` or `proton-cachyos-native`; Use System winetricks = Disabled; Enable DXVK = Enabled. System Options: Disable Lutris Runtime = Enabled, Prefer system libraries = Enabled; env var `PROTON_VERB=waitforexitandrun` (optional).
- Note: user-defined DXVK/VKD3D/DXVK-NVAPI versions are **not** applied with `umu-launcher`.

## Anti-cheat (EAC / BattlEye)

```bash
sudo pacman -S proton-cachyos-slr
```

## Wine-CachyOS

Standalone (Lutris / Heroic / Bottles — **not** for Steam). Packages: `wine-cachyos` (replaces system `wine`/`wine-staging`) and `wine-cachyos-opt` (separate location, lets versions coexist).

Options: `WINE_WMCLASS="[name]"`, `WINEUSERSANDBOX=1`, `WINE_NO_WM_DECORATION=1`, `WINE_PREFER_SDL_INPUT=1`.

Strict env setup:

```bash
export PATH="/opt/wine-cachyos/bin/:$PATH"
export WINEDLLPATH="/opt/wine-cachyos/lib/wine:/opt/wine-cachyos/lib32/wine:$WINEDLLPATH"
export LD_LIBRARY_PATH="/opt/wine-cachyos/lib/:/opt/wine-cachyos/lib32/:$LD_LIBRARY_PATH"
```

```bash
WINE=/opt/wine-cachyos/bin/wine WINEPREFIX=[your prefix] winetricks [verb]
```

## Proton flavours

`proton-cachyos` (in `cachyos-gaming-meta`); packages `proton-cachyos-native` and `proton-cachyos-slr` (**slr preferred**). Also Proton-EM, dwproton, Proton-GE, plus Valve's Proton Experimental / 11.0 / 10.0.

## Common fixes

- H264 codec (run once, then restart Steam normally): `steam steam://unlockh264/`
- Steam recorder stutter (breaks the Steam Overlay): `LD_PRELOAD="" %command%`
- Proton logging: `PROTON_LOG=1 %command%` → `~/steam-[AppID].log` (CS2 = `steam-730.log`); upload to `paste.cachyos.org`.
- **Disable Steam shader pre-caching** when using Proton-CachyOS/-GE/-EM: Steam → Settings → Downloads → uncheck "Allow background processing of Vulkan shaders" and "Enable Shader Pre-caching".
- **NTFS**: do not run Proton games from NTFS drives (unsupported by Valve).
- gamescope session:

  ```bash
  pacman -S gamescope-session-cachyos
  ```

  then set it as the default session.
- Lutris: arguments in Game options → Arguments; wrappers (`mangohud --dlsym`, `game-performance`) in System options → Command prefix; env vars in the Environment variables table (**no `=` in the Key field**). Logs: Output debugging info = `Inherit from environment`, plus `PROTON_LOG=1`.

## Performance

- Do **not** combine `gamemode` and `ananicy-cpp` (both modify niceness):

  ```bash
  systemctl stop ananicy-cpp
  ```
- `game-performance` uses `power-profiles-daemon` to switch to the `performance` profile until the game exits (and switches the active scx scheduler to its gaming profile — see [[sched-ext Schedulers]]):

  ```
  game-performance %command%
  ```

## Increase the maximum shader cache size

Old shaders get evicted at the default size, and big games can have >1GB of shaders — which means recompiling on every launch. Create the env file:

```bash
mkdir -p ~/.config/environment.d
touch ~/.config/environment.d/gaming.conf
micro ~/.config/environment.d/gaming.conf
```

Paste the vendor value:

```
# Increase AMD's shader cache size to 12GB
MESA_SHADER_CACHE_MAX_SIZE=12G
```

```
# Increase Nvidia's shader cache size to 12GB
__GL_SHADER_DISK_CACHE_SIZE=12000000000
```

Restart for it to take effect. (Credit on the wiki: psygreg's shader booster.)

## Misc

- DLSS preset via launch option: `dlss-swapper %command%` — see [[CachyOS Settings Package]].
- Ray tracing: see the Arch Wiki `Hardware_raytracing` page. The NVIDIA DX12 performance drop is a driver/scheduling issue, not CachyOS; no known workaround.
- GPU selection: [[Dual GPU Setup]].

Related: [[General System Tweaks]] (NVIDIA Smooth Motion), [[Dual GPU Setup]].
