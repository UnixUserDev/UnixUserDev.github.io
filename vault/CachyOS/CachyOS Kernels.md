---
title: CachyOS Kernels
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-08-31
source: https://wiki.cachyos.org/features/kernel/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-08-31).

# CachyOS Kernels

A customized kernel: upstream enhancements, configs and patches.

## Performance characteristics

- Customizable PKGBUILD (GCC or Clang); **Clang Thin LTO by default on `linux-cachyos`**; distributed ThinLTO builds; AutoFDO profiling on the default kernel; kCFI with LLVM.
- Timer frequency options 100/250/300/500/600/750/1000 Hz (**default 1000 Hz**); x86-64-v3, x86-64-v4 and AMD Zen4 builds; `-fivopts` / `-fmodulo-sched`; `PREEMPT_DYNAMIC`.
- CPU/sched: BORE, EEVDF, BMQ schedulers; POC Selector (scx_cake-inspired fast idle CPU selector); `CONFIG_CACHY` ("CachyOS Sauce"); AMD P-State preferred-core + linux-next amd-pstate; RT builds with BORE; sched/wait LIFO accept().
- Networking: **BBRv3 TCP** available as a separate module alongside BBRv1.
- FS/memory: built-in ZFS with precompiled modules; NTFS fixes; MGLRU/MM tuning; NVIDIA proprietary (patched) + open modules ready in-repo; BFQ and mq-deadline improvements; ADIOS 3.2.0 multi-queue I/O scheduler; VRAM cgroup (DMEM).
- Hardware: AMD ISP4 camera; Steam Deck / ROG Ally / MSI Claw (deckify) HID; T2 MacBook `apple-bce` in staging; ASUS patches; Lenovo WMI battery charge limiting; HP OMEN Slim/MAX via hp-wmi; HDR enabled; `amdgpu_ignore_min_pcap`; HDMI VRR on AMD, ALLM, passive VRR, VESA DSC from EDID; AW88399 and MAX98390 HDA side codecs.
- Other: v4l2loopback by default; ACS Override for VFIO/GPU passthrough; Clear Linux and linux-next cherry-picks; unstripped `dbg` variant (needed for AutoFDO); Binder module enabled (Waydroid).

## Variants

All ship the CachyOS Base Patchset. Each has a corresponding `-lto` variant built with Clang instead of GCC.

| Variant | Notes |
|---|---|
| `linux-cachyos` | Default, recommended. 1000Hz, Clang + ThinLTO, AutoFDO profiled |
| `linux-cachyos-bore` | BORE scheduler |
| `linux-cachyos-bmq` | BMQ scheduler from Project C (Alfred Chen). **No sched-ext support** |
| `linux-cachyos-deckify` | Default for handhelds; BORE + handheld patches. Other kernels are not recommended/unsupported on handhelds |
| `linux-cachyos-eevdf` | Tweaks the default scheduler for responsiveness |
| `linux-cachyos-lts` | Latest LTS, BORE, minimally patched for stability |
| `linux-cachyos-hardened` | BORE + linux-hardened patchset and config; aggressive hardening that stifles performance. **No sched-ext support** |
| `linux-cachyos-rc` | Latest mainline (Linus's tree); where new patchset features land first |
| `linux-cachyos-server` | Server workloads: 300Hz tickrate, no preemption, stock EEVDF |
| `linux-cachyos-rt-bore` | Real-time preemption, BORE scheduler |

## Package naming convention

```
linux-cachyos                                    # default, Clang + ThinLTO
linux-cachyos-hardened                           # GCC-built
linux-cachyos-hardened-lto                       # clang-compiled counterpart
linux-cachyos-hardened-{,lto-}headers
linux-cachyos-hardened-{,lto-}nvidia
linux-cachyos-hardened-{,lto-}nvidia-open
linux-cachyos-hardened-{,lto-}zfs
linux-cachyos-hardened-{,lto-}dbg
```

## Prebuilt modules

ZFS and NVIDIA modules ship **precompiled**, so there is no rebuild per kernel update and `-dkms` equivalents are obsolete. Both NVIDIA closed and open modules are precompiled and patched with community/NVIDIA patches to stay compatible with the latest kernel (out-of-tree module, off upstream cadence).

## FAQ

- AutoFDO is only on `linux-cachyos` because it is expensive: build with AutoFDO+debug → create a profile by running workloads → rebuild with the profile.
- **The realtime kernel does not improve gaming performance**: far more code is preemptible, so gaming processes get preempted frequently and give resources back.

Related: [[CachyOS Kernel Manager]], [[Optimized Repositories and x86-64 Levels]], [[sched-ext Schedulers]].
