---
title: QEMU and VMM Setup
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-03-08
source: https://wiki.cachyos.org/virtualization/qemu_and_vmm_setup/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-03-08).

# QEMU and VMM Setup

Installing Virtual Machine Manager (VMM) + QEMU on CachyOS.

## Packages

`qemu-full`, `virt-manager`, `swtpm`.

`swtpm` is only needed for Windows 11 guests when you have no compatible TPM module to pass through — otherwise you can add a TPM later during configuration.

```bash
# Install the packages
sudo pacman -S qemu-full virt-manager swtpm

# Force libvirt to use iptables
echo 'firewall_backend = "iptables"' | sudo tee -a /etc/libvirt/network.conf

# Add the user to the libvirt group
sudo usermod -aG libvirt $USER

# LXC backend (optional, for Linux containers)
systemctl enable --now libvirtd.service

# QEMU backend (for VMs)
systemctl enable --now libvirtd.socket

# Bring Internet up in a VM whenever one starts
sudo virsh net-autostart default

# Let the whole VM subnet route out — consider tighter rules for your threat model
sudo ufw route allow from 192.168.122.0/24
```

- Config file touched: `/etc/libvirt/network.conf`.
- Both backends can be enabled at once; they do not conflict.
- Before creating a VM, the wiki defers to [[QEMU User Session]].

## Creating a VM in VMM

- `Edit -> Preferences -> ✅ Enable XML editing`.
- New VM (star/play icon, or `File -> New Virtual Machine`), choose `Connection:`, locate the `.iso` via `Browse...` (add a dedicated ISO pool with `+ (Add Pool)`; the user-session pool path lives in `~/.config/libvirt/`).
- If the OS is not autodetected, untick autodetection and pick the closest match manually (CachyOS = Arch Linux).
- Set RAM and CPU cores (changeable later), create virtual storage (makes a **`.qcow2`**) or give a path.
- Tick `Customize configuration before install -> Finish`.
- Linux guests: in `Overview` confirm chipset **Q35** and firmware **UEFI**.
- Windows 11: confirm a TPM is present, else `Add Hardware -> TPM` (Emulated or Passthrough), then `Finish`.
- Click `Begin Installation`.
- Guest uses OpenGL and/or ships libvulkan → see [[EGL Headless 3D Acceleration]] and [[Virtio-Venus 3D Acceleration]] respectively.

## Windows 11 performance notes

- Install **Virtio-Win** drivers (RedHat) inside the guest.
- Intel iGPU: look into **GVT-g** and **MDEV**. Intel iGPU + dGPU: **GVT-d**.
- dGPU passthrough (iGPU + dGPU, or 2+ dGPUs): **VFIO**.
