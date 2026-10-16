---
title: General System Tweaks
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-07-26
source: https://wiki.cachyos.org/configuration/general_system_tweaks/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-07-26). Mostly AMD-specific power tuning plus a few cross-cutting tweaks.

# AMD P-State driver (`amd_pstate`)

CPU frequency scaling driver for modern AMD APUs/CPUs, based on Collaborative Processor Performance Control (CPPC): finer-grained frequency management and a low-latency interface for the kernel to send performance hints to hardware (legacy ACPI P-states only switch in 3 P-states).

Kernel cmdline entries per mode:

- Non-Autonomous Mode — `amd_pstate=passive`
- Guided Autonomous Mode — `amd_pstate=guided`
- Autonomous Mode (EPP) — `amd_pstate=active`

**AMD P-State EPP is the default when nothing is configured.** Test at runtime:

```bash
echo active | sudo tee /sys/devices/system/cpu/amd_pstate/status
echo guided | sudo tee /sys/devices/system/cpu/amd_pstate/status
echo passive | sudo tee /sys/devices/system/cpu/amd_pstate/status
```

Modes: autonomous = platform considers only min/max performance + Energy Performance Preference; guided-autonomous = platform sets the operating performance level per workload within OS-set min/max registers; non-autonomous = the OS hands the platform the desired performance level directly.

## Configuring AMD P-State EPP

Governors: `powersave` and `performance`. The page recommends `powersave` plus a preference.

```bash
sudo cpupower frequency-set -g powersave
sudo cpupower frequency-set -g performance
```

```bash
echo power | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/energy_performance_preference
```

Preferences: `performance`, `power`, `balance_power`, `balance_performance`.

## AMD 3D V-Cache Optimizer

Optimizes cache scheduling on dual-CCD 3D CPUs (7950X3D, 7900X3D). **Requires the BIOS CPPC option set to "Driver"** so sysfs can override the mode.

```bash
echo frequency | sudo tee /sys/bus/platform/drivers/amd_x3d_vcache/AMDI0101:00/amd_x3d_mode
echo cache | sudo tee /sys/bus/platform/drivers/amd_x3d_vcache/AMDI0101:00/amd_x3d_mode
```

- `frequency` (default) — tasks go to the second CCD, which clocks higher than the 3D-cache CCD.
- `cache` — tasks go first to the higher-cache CCD; mainly profitable for games.

After switching, the preferred-core ranking should change; read it with:

```bash
grep -v /sys/devices/system/cpu/cpu*/cpufreq/amd_pstate_prefcore_ranking
```

## AMD P-State Core Performance Boost (AMD Turbo Core)

```bash
echo 0 | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/boost   # Disable boost for all cores
lscpu -ae                                                       # Shows AMD CPB disabled globally
```

```bash
echo 1 | sudo tee /sys/devices/system/cpu/cpu0/cpufreq/boost   # Enable boost on cpu0
lscpu -ae
```

CachyOS ships a `power-profiles-daemon` backporting AMD CPB support: CPB is disabled under the `powersave` profile, enabled under `balanced` or `performance`.

# ADIOS I/O scheduler

Adaptive Deadline I/O Scheduler (firelzrd; stable branch is in the kernel): a blk-mq block-layer scheduler aiming at low-latency I/O by combining deadline scheduling with learning-based adaptive latency control. Inspired by and building on mq-deadline and Kyber — it predicts I/O completion latency from past performance and request characteristics (type, size) and uses that to adjust deadlines and batching.

- Version in `linux-cachyos`: **3.2.0**; same in `linux-cachyos-rc`.
- Four priority tiers, Tier 0 (critical system ops) to Tier 3 (background); tunable via sysfs.
- Enable temporarily (lost on reboot):

  ```bash
  sync && echo adios | sudo tee /sys/block/<yourdrive>/queue/scheduler
  ```

  Replace `<yourdrive>` with the real identifier (e.g. `sda`, `sdb`, `nvme0n1`).

> **Gotcha:** NOT enabled by default — still under active development and testing, not considered stable for all workloads and hardware. In edge cases bugs could cause system lockups.

# NVIDIA Smooth Motion (RTX 40xx & 50xx)

Driver-based AI frame interpolation: infers an extra frame between two rendered frames — an alternative for games without DLSS Frame Generation. Works for DX11, DX12 and Vulkan (DXVK and VKD3D both fine).

```bash
NVPRESENT_ENABLE_SMOOTH_MOTION=1
```

- **64-bit applications only** — no 32-bit games.
- Overlay conflicts → `NVPRESENT_QUEUE_FAMILY=1`.
- Drawbacks: may add slight input lag; MangoHud reports a misleading FPS (a monitor's built-in counter is correct); image quality below DLSS Frame Generation, artifacts in fast motion; incompatible with external frame capers (in-game caps fine); **cannot be combined with DLSS Frame Generation**; OpenGL needs Zink:

  ```bash
  NVPRESENT_ENABLE_SMOOTH_MOTION=1 zink-run <command>
  ```

  Zink costs performance and may glitch.
- Dual GPU: set `DRI_PRIME=1` to pick the NVIDIA dGPU.

# Power saving

## thermald

Thermal management daemon for **Intel** systems: monitors thermal sensors and applies cooling actions (CPU power limits, performance-state changes) before firmware throttles more aggressively. Useful on Intel laptops, convertibles, tablets, handhelds and SFF PCs exposing Intel thermal controls via `/sys/class/thermal`. **AMD CPUs are not supported.**

```bash
sudo pacman -S thermald
sudo systemctl enable --now thermald.service
```

```bash
systemctl status thermald.service
journalctl -u thermald.service -b
```

## RCU Lazy

Reduces power use at idle / light load — useful for laptops and handhelds. The page states **5–10%** power savings at the cost of slightly reduced performance depending on scenario. Enabled by default in `linux-cachyos-deckify`. Kernel cmdline:

```
rcutree.enable_rcu_lazy=1
```

# NVIDIA troubleshooting — GSP firmware

GSP firmware can "in some cases" hurt performance; the 555.58.02 driver largely fixed this, but it can persist on some systems (symptoms: KDE hiccups, bad performance). Disable via `/etc/modprobe.d/nvidia-gsp.conf`:

```
options nvidia NVreg_EnableGpuFirmware=0
```

Then:

```bash
sudo mkinitcpio -P
```

> **Gotcha:** NVIDIA's open kernel modules are built on GSP firmware, so GSP cannot be disabled there and this option is ignored — use `linux-cachyos-nvidia` or `nvidia-dkms`. Re-test GSP after each new driver install, since NVIDIA does its QA against GSP.

# Audio — enhancing laptop speakers (EasyEffects)

```bash
sudo pacman -S easyeffects
sudo pacman -S lsp-plugins-lv2
sudo pacman -S zam-plugins
sudo pacman -S calf
sudo pacman -S mda.lv2
```

- **Output** tab manages effects on speaker audio; **Effects** tab adds/modifies them.
- Community presets: download from the EasyEffects Community Presets repo → **Presets** → "Import preset from local storage" → select file → **Load**.
- Custom profile: **"+"** in the Presets menu → name it → **Load** → add effects under **Output > Effects**.
- **Convolver**: add it to the chain and load a `.wav` impulse response for your laptop model. **Gotcha:** it can raise volume a lot — add a **Limiter** after it.
- Automate startup in the app's preferences; use **PipeWire > Presets Autoloading** to bind presets to output devices.
- Alternative: JDSP4Linux (`github.com/Audio4Linux/JDSP4Linux`) for PipeWire/PulseAudio clients.

# OBS Studio

Use the CachyOS `obs-studio-browser` package instead of stock `obs-studio` — it carries patches for common issues such as CUDA errors and virtual camera problems.

```bash
sudo pacman -S obs-studio-browser
# If obs-studio was installed, pacman asks whether to replace it — answer "Y".
```

# Swap: switching from ZRam to Zswap

**CachyOS uses ZRam by default**; Zswap is optional. Assumes the default setup (ZRam enabled, no swap partition/file created at install).

1. Disable ZRam via kernel parameter: `systemd.zram=0`
2. Enable Zswap:

   ```
   zswap.enabled=1 zswap.shrinker_enabled=1 zswap.compressor=lz4 zswap.max_pool_percent=30
   ```
3. Neutralize CachyOS's udev rule that disables Zswap (`usr/lib/udev/rules.d/30-zram.rules` from `github.com/CachyOS/CachyOS-Settings`):

   ```bash
   sudo touch /etc/udev/rules.d/30-zram.rules
   ```
4. Create a swap file for Zswap.

   **Btrfs:**

   ```bash
   sudo btrfs subvolume create /swap
   sudo btrfs filesystem mkswapfile --size 4g --uuid clear /swap/swapfile
   sudo swapon /swap/swapfile
   echo "/swap/swapfile none swap defaults 0 0" | sudo tee -a /etc/fstab
   ```

   (Change `--size` for a different size — the page's prose says e.g. 8GB while its command uses 4g.)

   **Non-Btrfs:**

   ```bash
   sudo dd if=/dev/zero of=/swapfile bs=1G count=8
   sudo chmod 0600 /swapfile
   sudo mkswap /swapfile
   echo "/swapfile none swap defaults 0 0" | sudo tee -a /etc/fstab
   sudo swapon /swapfile
   ```
5. Add the `lz4` module to `/etc/mkinitcpio.conf`'s `MODULES` array:

   ```bash
   sudo nano /etc/mkinitcpio.conf
   ```

   ```
   MODULES=(... lz4)
   ```
6. Rebuild the initramfs — per bootloader:

   ```bash
   sudo sdboot-manage gen                      # systemd-boot
   sudo grub-mkconfig -o /boot/grub/grub.cfg   # GRUB
   sudo limine-mkinitcpio                      # Limine
   ```
7. Reboot.
