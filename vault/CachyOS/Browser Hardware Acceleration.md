---
title: Browser Hardware Acceleration
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-09-18
source: https://wiki.cachyos.org/configuration/enabling_hardware_acceleration_in_google_chrome/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-09-18).

# Browser Hardware Acceleration

Prerequisite: a Chromium-based browser (Chrome, Brave, Ungoogled Chromium, Edge, Vivaldi) plus up-to-date Mesa (AMD/Intel) or NVIDIA drivers with Vulkan/VA-API/VDPAU.

Optional monitors: `amdgpu_top` (AMD), `nvtop` (Intel Lunar Lake), `intel-gpu-tools` (pre-Lunar Lake).

## How the flags work

Flags live in `~/.config/[browser]-flags.conf`:

- Chromium — `~/.config/chromium-flags.conf`
- Brave — `~/.config/brave-flags.conf`
- Ungoogled Chromium — `~/.config/ungoogled-chromium-flags.conf`

Brave/Edge/Vivaldi/Opera may additionally need the `Exec=` line of a `.desktop` file edited — copy it from `/usr/share/applications/` to `~/.local/share/applications/` first.

Flow: edit the flags file (e.g. `nano ~/.config/chrome-flags.conf`) → paste flags → restart the browser → verify at `chrome://gpu` (or `brave://gpu`).

Verify decode is actually engaged: `amdgpu_top` → watch the `media` section during playback (**0% = not engaged**).

## Known-good configurations (verbatim from the wiki)

**AMD Radeon RX 6900 XT, Google Chrome** — `~/.config/chrome-flags.conf`:

```
--use-gl=angle
--use-angle=vulkan
--enable-features=Vulkan,VulkanFromANGLE,DefaultANGLEVulkan,AcceleratedVideoDecodeLinuxZeroCopyGL,AcceleratedVideoEncoder,VaapiIgnoreDriverChecks,UseMultiPlaneFormatForHardwareVideo
```

**NVIDIA RTX 4090, Vivaldi** — `~/.config/vivaldi-stable.conf`:

```
--enable-features=VaapiVideoDecoder,AcceleratedVideoDecodeLinuxGL,AcceleratedVideoDecodeLinuxZeroCopyGL
```

or in `/usr/share/applications/vivaldi-stable.desktop`:

```
Exec=/usr/bin/vivaldi-stable --enable-features=VaapiVideoDecoder,AcceleratedVideoDecodeLinuxGL,AcceleratedVideoDecodeLinuxZeroCopyGL %U
```

**AMD Radeon RX 550, Ungoogled Chromium** — `~/.config/chromium-flags.conf`:

```
--enable-wayland-ime
--enable-features=AcceleratedVideoDecodeLinuxGL,AcceleratedVideoDecodeLinuxZeroCopyGL,AcceleratedVideoEncoder,WaylandSessionManagement,WaylandTextInputV3,WaylandUiScale,WaylandWindowDecorations
```

**NVIDIA RTX 5070 Ti, Brave** — `~/.config/brave-flags.conf`:

```
--enable-features=VaapiVideoDecoder,AcceleratedVideoDecodeLinuxGL,AcceleratedVideoDecodeLinuxZeroCopyGL,AcceleratedVideoEncoder,VaapiIgnoreDriverChecks
```

**AMD RX 7700 XT, Brave** (DaJRJesus) — `~/.config/brave-flags.conf`:

```
--ignore-gpu-blocklist
--enable-gpu-rasterization
--enable-zero-copy
--enable-features=AcceleratedVideoDecodeLinuxGL,AcceleratedVideoDecodeLinuxZeroCopyGL,AcceleratedVideoEncoder,CanvasOopRasterization,VaapiIgnoreDriverChecks,UseMultiPlaneFormatForHardwareVideo
```

**AMD RX 9070 XT, Google Chrome** (naknak) — `~/.config/chrome-flags.conf`:

```
--ignore-gpu-blocklist
--enable-gpu-rasterization
--enable-zero-copy
--use-gl=angle
--use-angle=vulkan
--enable-features=VaapiVideoDecoder,AcceleratedVideoDecodeLinuxGL,AcceleratedVideoDecodeLinuxZeroCopyGL,AcceleratedVideoEncoder,VaapiIgnoreDriverChecks,UseMultiPlaneFormatForHardwareVideo,Vulkan,VulkanFromANGLE,DefaultANGLEVulkan
```

> `--ignore-gpu-blocklist` is required here: **RDNA 4 may be blocklisted.**

**NVIDIA RTX 5090, Vivaldi** (icetea) — `~/.config/vivaldi-stable.conf`:

```
--enable-features=VaapiVideoDecoder,AcceleratedVideoDecodeLinuxGL,AcceleratedVideoDecodeLinuxZeroCopyGL,VaapiOnNvidiaGPUs,VaapiIgnoreDriverChecks
--use-gl=angle
--use-angle=gl
--disable-features=Vulkan
--ignore-gpu-blocklist
--ozone-platform=wayland
```

Requires `libva-nvidia-driver`. `VaapiIgnoreDriverChecks` is key on NVIDIA; **AV1 decode is broken in `libva-nvidia-driver` 0.0.17**.

**NVIDIA RTX 5070, Google Chrome** (pisyukaev) — `~/.config/chrome-flags.conf`:

```
--enable-features=AcceleratedVideoDecodeLinuxGL,VaapiVideoDecoder,VaapiIgnoreDriverChecks,VaapiOnNvidiaGPUs
--ignore-gpu-blocklist
```

Requires `libva-nvidia-driver` + `nvidia-utils`. **`VaapiOnNvidiaGPUs` is the critical flag** — Chrome otherwise blocklists NVIDIA in `vaapi_wrapper.cc`.

**AMD RX 7600, Brave Origin** (RyuuPendragon) — `~/.config/brave-origin-flags.conf`:

```
--ignore-gpu-blocklist
--enable-gpu-rasterization
--enable-zero-copy
--enable-features=AcceleratedVideoDecodeLinuxGL,AcceleratedVideoDecodeLinuxZeroCopyGL,AcceleratedVideoEncoder,CanvasOopRasterization,VaapiIgnoreDriverChecks,UseMultiPlaneFormatForHardwareVideo
```

**NVIDIA RTX 5070 Ti, Vivaldi** (Peek4Boo7) — `~/.config/vivaldi-stable.conf`:

```
--enable-features=AcceleratedVideoDecodeLinuxGL,AcceleratedVideoDecodeLinuxZeroCopyGL,VaapiOnNvidiaGPUs
```

Driver 615.71.09 / libva-nvidia-driver 0.0.18-1.1; `VaapiVideoDecoder` and `VaapiIgnoreDriverChecks` are not required at that driver version.

> The wiki page carries more community submissions than are copied here — check it for your exact GPU/browser pair rather than guessing flags.

Related: [[Dual GPU Setup]], [[CachyOS Filesystems]].
