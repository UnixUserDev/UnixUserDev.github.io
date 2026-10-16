---
title: CachyOS Kernel Manager
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2025-12-22
source: https://wiki.cachyos.org/features/kernel_manager/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2025-12-22).

# CachyOS Kernel Manager

Installs/manages kernels from any Arch repo, builds custom CachyOS kernels, and controls sched-ext schedulers via GUI.

## Install / uninstall a kernel

- Launch `CachyOS Kernel Manager`, tick the `[]` box for the desired kernel, press `Execute`.
- Uninstall: uncheck the `[]` next to the installed kernel and press `Execute`.
- `Execute` goes unavailable when an already-installed kernel is selected.

## Configuring and building a custom kernel

**Only builds CachyOS variants** — it cannot build or configure a stock Arch kernel.

- Click `Configure`, adjust settings, then `Build kernel`.
- Built packages and cache land in `~/.cache/cachyos-km/`.
- After a successful build you are prompted for your sudo password to install it.

Available options: custom package name (e.g. `linux-custom-cachy`); scheduler (BORE, RC, RT, RT+BORE, EEVDF, BMQ); enable CachyOS config; tweak via nconfig/menuconfig/xconfig/gconfig; NUMA; Modprobed-db; KBUILD CFLAGS (`-O3`/`-O2`); performance governor as default; enable BBR3; tick rate (100/250/300/500/600/750/1000Hz); tickless mode (idle/periodic/full); preemption (Full/Voluntary/Server); transparent hugepages (Always/Madvise); DAMON; automatic CPU arch detection; CPU-arch-specific optimizations; LTO (Full/Thin/No); build ZFS module; build NVIDIA closed module; build NVIDIA open module; include `vmlinux` with debug info/symbols; load/save Kernel Manager presets (its own only); Kernel Patches Management (remote and local).

## Sched-ext framework management

Open with the `sched-ext scheduler config` button in the main window. The GUI can switch between sched-ext (scx) schedulers, enable/disable the scheduler service, show the running scheduler, and set scheduler flags and profiles.

It uses `scx_loader`, config at `/etc/scx_loader.toml`. Selecting a scheduler + profile starts it with that profile and persists the config for reboots. Example (`scx_bpfland` with the `Gaming` profile):

```toml
default_sched = "scx_bpfland"
default_mode = "Gaming"

[scheds.scx_bpfland]
auto_mode = []
gaming_mode = ["-m", "performance"]
lowlatency_mode = ["-k", "-s", "5000", "-l", "5000"]
powersave_mode = ["-m", "powersave"]
```

Profiles are presets that modify scheduler flags based on proven combinations for a use case. Details in the sched-ext guide's scheduler-guide section — see [[sched-ext Schedulers]].
