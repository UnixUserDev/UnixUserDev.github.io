---
title: Switching Between NVIDIA and AMD GPUs
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-09-15
source: https://wiki.cachyos.org/features/chwd/gpu_migration/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-09-15).

# Switching Between NVIDIA and AMD GPUs

Prerequisites: the replacement GPU is properly installed and connected, and the system is fully up to date:

```bash
sudo pacman -Syu
```

## NVIDIA → AMD, in order

0. Make a note of your username and password — you may need to log in via TTY.
1. Check which NVIDIA driver profile is installed:

   ```bash
   sudo chwd --list-installed
   ```
2. Remove that profile with `-r`:

   ```bash
   sudo chwd -r <profile-name>
   ```
3. Shut down and install the AMD GPU. If the system lands on a black screen, press `CTRL+ALT+F3` to reach a TTY and log in (mind NUMLOCK).
4. After booting, let chwd detect the GPU and install the AMD packages:

   ```bash
   sudo chwd -a
   ```
5. Update the initramfs and bootloader config. CachyOS ships a pacman hook that rebuilds the initramfs automatically when drivers are added/removed, so the right drivers load at boot:
   - GRUB — `sudo grub-mkconfig -o /boot/grub/grub.cfg`
   - systemd-boot — `sudo sdboot-manage gen`
   - Limine — `sudo limine-mkinitcpio`

   See [[Boot Manager Configuration]].
6. Reboot:

   ```bash
   sudo reboot
   ```
7. Verify the AMD drivers are active:

   ```bash
   vulkaninfo | grep "deviceName"
   ```

   or

   ```bash
   glxinfo | grep "OpenGL renderer"
   ```

   If your AMD GPU is listed, they are working.

> Caveat: the wiki page is a tabbed guide ("From NVIDIA to AMD" / "From AMD to NVIDIA"). Only the NVIDIA→AMD tab body was recoverable — **do not assume the reverse direction mirrors it.** AMD drivers are in the kernel, so going AMD→NVIDIA is mainly cleaning up old config, but check the page for its own steps.

Related: [[Managing Hardware with chwd]], [[Dual GPU Setup]].
