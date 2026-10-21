---
tags:
  - memory/guide
created: 2026-10-20T19:10:03
source: agent
---

BONELAB save files are JSON (format "version": 16 on Patch 6) but the important fields are ciphertext, not editable data:

"unlocks" and "progression" are base64-encoded fixed-size blobs. A fresh/empty save has 32 bytes each; a 100% save has ~18064 and ~14848 bytes. They are not zlib/gzip-compressed (all decompression attempts fail), so hand-editing or "set all bits" is not possible without the key buried in the IL2CPP GameAssembly.

Consequence: you cannot fabricate unlocked progression, and the old Thunderstore unlock mods (UnlockAll, Achievement_Unlocker) are 2022 relics pinned to MelonLoader 0.5.x that do nothing on Patch 6. The practical route is a 100% save file from mod.io/Nexus, and it must match both the game patch and the save "version" field.

Steam Cloud for BONELAB syncs only "Stress Level Zero/BONELAB/settings.json" (see userdata/<id>/1592190/remotecache.vdf), so lost progress is never recoverable from the cloud.

[[Inbox]]

[[Inbox]]
