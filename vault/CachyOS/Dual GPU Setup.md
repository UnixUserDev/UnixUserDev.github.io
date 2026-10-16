---
title: Dual GPU Setup
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-07-26
source: https://wiki.cachyos.org/configuration/dual_gpu/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-07-26).

# Dual GPU Setup (hybrid graphics)

Hybrid graphics = iGPU (CPU) + dGPU working in tandem, mainly on laptops. The dGPU can sleep when idle to save power and wake on demand.

**PRIME** is the unifying Linux hybrid-graphics tech (NVIDIA Optimus / AMD Dynamic Switchable Graphics); PRIME Offload moves rendering between GPUs.

- The closed NVIDIA driver supports PRIME from **435.17**; 390xx/340xx will not work.
- Avoid outdated methods: `nvidia-xrun`, `Bumblebee` (unsupported), `optimus-manager`.

## NVIDIA: nothing to configure

In CachyOS, PRIME Offload needs no configuration — `nvidia-utils` + `cachyos-settings` suffice.

Launch a program on the NVIDIA dGPU:

```bash
__NV_PRIME_RENDER_OFFLOAD=1 __VK_LAYER_NV_optimus=NVIDIA_only __GLX_VENDOR_LIBRARY_NAME=nvidia <program>
```

Shorthand via `nvidia-prime`:

```bash
sudo pacman -S nvidia-prime
```

```bash
prime-run <program>
```

Some DX12 games need this before `prime-run`:

```bash
VK_DRIVER_FILES=/usr/share/vulkan/icd.d/nvidia_icd.json
```

## Mesa pairs (AMD+AMD, AMD+Intel, Intel+Nouveau)

Set `DRI_PRIME=1` before the app. Nothing else to configure.

## Per-application (graphical)

- **Lutris** → Preferences → Global Options → Display → select the GPU.
- **Steam** → Properties → Launch options, e.g.:

  ```
  prime-run %command%
  ```

  Force the iGPU (Radeon example):

  ```
  VK_DRIVER_FILES=/usr/share/vulkan/icd.d/radeon_icd.x86_64.json:/usr/share/vulkan/icd.d/radeon_icd.i686.json %command%
  ```

## Desktop integration via switcheroo-control

- **KDE Plasma** — needs `switcheroo-control` (enabled by default on CachyOS fresh installs via chwd):

  ```bash
  sudo pacman -S switcheroo-control
  sudo systemctl enable --now switcheroo-control
  ```

  Then right-click the desktop entry → Properties → Application → Advanced Options → check **"Run using dedicated graphics card"**. Works for non-NVIDIA PRIME configs too (AMD APU + AMD dedicated).
- **GNOME** — install `switcheroo-control`, right-click the icon → "Run using discrete graphics". **GNOME does not remember the choice.**
- **Cinnamon** — right-click the desktop entry → Properties → enable the relevant option; needs `switcheroo-control` + the service.

Related: [[Managing Hardware with chwd]], [[Switching Between NVIDIA and AMD GPUs]], [[Gaming on CachyOS]].
