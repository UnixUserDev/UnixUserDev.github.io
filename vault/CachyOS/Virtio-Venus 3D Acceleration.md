---
title: Virtio-Venus 3D Acceleration
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2025-11-24
source: https://wiki.cachyos.org/virtualization/virtio-venus/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2025-11-24).

# Virtio-Venus (near-native 3D acceleration)

> URL note: `/virtualization/virtio_venus/` (underscore) 404s — the live page is the hyphenated slug `/virtualization/virtio-venus/`.

For guests that use `libvulkan` — **Linux**, **BSD**s, **Android** — you can get near-native performance by translating calls from the VM to Vulkan, then directly to the GPU and back, **without** passing the GPU through to the VM.

**Limitation: Virtio-Venus is not exposed in VMM**, so it requires `qemu-cli`. You need to know the VM's name and where it lives.

## Example invocation

```bash
qemu-system-x86_64                                                                             \
    -enable-kvm                                                                                \
    -M q35                                                                                     \
    -smp 6                                                                                     \
    -m 12G                                                                                     \
    -cpu host                                                                                  \
    -net nic,model=virtio                                                                      \
    -net user,hostfwd=tcp::2222-:22                                                            \
    -device virtio-sound-pci,audiodev=my_audiodev -audiodev pipewire,id=my_audiodev            \
    -device virtio-vga-gl,hostmem=4G,blob=true,venus=true                                      \
    -vga none                                                                                  \
    -display gtk,gl=on,show-cursor=on                                                          \
    -usb -device usb-tablet                                                                    \
    -object memory-backend-memfd,id=mem1,size=12G                                              \
    -machine memory-backend=mem1                                                               \
    -drive if=pflash,format=raw,readonly=on,file=/usr/share/edk2/x64/OVMF_CODE.secboot.4m.fd   \
    -drive if=pflash,format=raw,file=.config/libvirt/qemu/nvram/archlinux_VARS.fd              \
    -drive file=.local/share/libvirt/images/archlinux.qcow2
```

## Flags explained

- `-smp 6` — 6 CPU cores.
- `-m 12G` — 12 GB RAM.
- `-net user,hostfwd=tcp::2222-:22` — forwards guest port 22 to host port 2222:

  ```bash
  ssh -p 2222 vmusername@vmipaddress
  ```

- `-device virtio-vga-gl,hostmem=4G,blob=true,venus=true` — the Venus driver, not yet in virt-manager; once it is, everything here except user mode is superseded. `hostmem=4G` lets the VM use up to 4 GB of your VRAM.
- `-object memory-backend-memfd,id=mem1,size=12G` — size must match the RAM you gave the VM.
- `-drive if=pflash,...OVMF_CODE.secboot.4m.fd` — the firmware file path.
- `-drive if=pflash,format=raw,file=.config/libvirt/qemu/nvram/archlinux_VARS.fd` — the VM's nvram; without it (or if read-only) the VM won't run.
- `-drive file=.local/share/libvirt/images/archlinux.qcow2` — the VM's storage.
- Audio uses `pipewire` instead of `ich9` in the example; the page says that detail is irrelevant.
