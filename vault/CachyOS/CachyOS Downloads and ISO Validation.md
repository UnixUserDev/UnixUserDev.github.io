---
title: CachyOS Downloads and ISO Validation
tags:
  - note/cachyos
created: 2026-10-15T21:26:54
updated: 2026-08-10
source: https://wiki.cachyos.org/cachyos_basic/download/
---

Part of [[CachyOS Wiki Notes]]. From the official CachyOS wiki (last updated 2026-08-10).

# Downloads and Validation

## Official ISO sources

- Website — `https://cachyos.org/download`
- SourceForge — `https://sourceforge.net/projects/cachyos-arch/files/`
- CachyOS mirror — `https://mirror.cachyos.org/ISO/`

## Current ISO values (as published on the page)

- Version: **`260809`**
- SHA256: `959f6577f45e25ee9fd8c220fd221b08e4ea79412c7315c0f922dd6d86d5e33c`
- SHA256 file: `https://mirror.cachyos.org/ISO/desktop/260809/cachyos-desktop-linux-260809.iso.sha256`
- Signature file: `https://cdn77.cachyos.org/ISO/desktop/260809/cachyos-desktop-linux-260809.iso.sig`

> These are point-in-time values. Re-check the page before downloading — a rolling distro's ISO changes every few weeks.

## Verifying integrity (SHA256)

Always verify the ISO before installing or writing bootable media.

Windows (CMD/PowerShell **as Administrator**, in the folder holding both files):

```powershell
# Example:
certUtil -hashfile cachyos-desktop-linux-260809.iso SHA256
```

Compare against the hash in the downloaded file; match → proceed.

## Verifying authenticity (GPG)

1. Import the reference key:

   ```bash
   gpg --keyserver hkps://keys.openpgp.org --recv-key F3B607488DB35A47
   ```

2. Download the ISO plus its `.sig`, then:

   ```bash
   gpg --verify full_iso_name.iso.sig full_iso_name.iso
   ```

A genuine ISO returns **Good signature**:

```
gpg: Signature made dom 09 ago 2026 13:30:06 -03
gpg:                using RSA key 882DCFE48E2051D48E2562ABF3B607488DB35A47
gpg: Good signature from "CachyOS <...>" [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: 882D CFE4 8E20 51D4 8E25  62AB F3B6 0748 8DB3 5A47
```

> **Gotcha:** no `Good signature` string, or a key ID mismatch → do **not** use the ISO. A bad signature means tampering or corruption; make sure the image came from a legitimate CachyOS source.

The key `F3B607488DB35A47` is the same one used for the keyring fix in [[CachyOS FAQ and Troubleshooting]].

Next: [[Preparing a CachyOS USB Installer]].
