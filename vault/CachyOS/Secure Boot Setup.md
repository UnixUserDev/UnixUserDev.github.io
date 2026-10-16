---
title: Secure Boot Setup
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-08-25
source: https://wiki.cachyos.org/configuration/secure_boot_setup/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-08-25).

# Secure Boot Setup

## GRUB: enable secure boot support with CA keys

> **Warning from the wiki:** loading unnecessary modules in your boot manager has the potential to present a security risk. **Only run this if you actually need Secure Boot.**

```bash
sudo grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=cachyos --modules="tpm" --disable-shim-lock
```

## Enter firmware settings

```bash
systemctl reboot --firmware-setup
```

Set Secure Boot mode to **"Setup Mode"** (or "reset to setup mode" / restore factory keys).

- **Gigabyte**: reject the "reset without saving" dialog after "reset to setup mode" — it disables setup mode. Instead use Save & Exit → boot override.
- **No Setup Mode — MSI**: Secure Boot Mode = "custom", select "maximum security" compatibility, then Key Management.
- **ASUS**: Boot → Secure Boot → mode Custom → Key Management → "Delete all Secure Boot Variables".

## sbctl (the key manager)

```bash
sudo sbctl status
sudo sbctl create-keys
sudo sbctl enroll-keys --microsoft --firmware-builtin
```

> **Gotcha:** on ASUS/Gigabyte, `--firmware-builtin` can create duplicate EFI key entries (`builtin-db` repeated) → Secure Boot Violation. Use only:

```bash
sudo sbctl enroll-keys --microsoft
```

> If `Vendor Keys` shows `microsoft builtin-db builtin-db ...`, re-enter Setup Mode (delete all keys) and re-run `sbctl enroll-keys --microsoft`.

Re-check `sudo sbctl status`. If `Setup Mode` shows Enabled, temporarily disable Secure Boot in firmware, boot back, sign, reboot, and re-enable.

## Limine

Only Limine's EFI binary needs signing — its boot process bypasses EFI chainloading/signature checks, so **do not** sign images with `sbctl-batch-sign` (see [[CachyOS Settings Package]]).

From Limine **11.2.0** Secure Boot is strictly enforced: a BLAKE2B config checksum must be enrolled (otherwise panic `SECURE BOOT IS ACTIVE BUT NO CONFIG CHECKSUM IS ENROLLED`), all `limine.conf` paths need BLAKE2B hashes (`boot():/vmlinuz-linux#<hash>`), the config editor is disabled, and a hash mismatch panics.

Enable in `/etc/default/limine`:

```
ENABLE_ENROLL_LIMINE_CONFIG=yes
```

Hash the splash image and append the hash to its path in `/boot/limine.conf`:

```bash
sudo b2sum /boot/limine-splash.png
```

```
wallpaper: boot():/limine-splash.png#75205d08fa9c61599897857e861d6b2f6da25465183fc4cc9efecffb22ee630efb510f2ef1b17677db94c28d5c69ad2ceb4d3892f5bec9cfa65c97b5ba16f52f
```

Then:

```bash
sudo limine-enroll-config
sudo limine-update
```

## Verify

```bash
sudo sbctl status
bootctl
```

`bootctl` should show `Secure Boot: enabled (user)`.

## fwupd with Secure Boot

Sign the UEFI executable:

```bash
sudo sbctl sign -s -o /usr/lib/fwupd/efi/fwupdx64.efi.signed /usr/lib/fwupd/efi/fwupdx64.efi
```

And add to `/etc/fwupd/fwupd.conf`:

```
[uefi_capsule]
DisableShimForSecureBoot=true
```

Related: [[CachyOS Boot Managers]], [[Boot Manager Configuration]].
