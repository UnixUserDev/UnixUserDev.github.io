---
tags:
  - memory/projects
created: 2026-10-20T19:10:03
source: agent
---

BONELAB (Steam app 1592190) is installed at /mnt/External/SteamLibrary/steamapps/common/BONELAB, running under Proton 11.0 from compatdata/1592190.

Modding stack installed for the Doppel mod (RUDYHILL, "bots that learn to move from recordings of you"): MelonLoader 0.7.3 (version.dll + MelonLoader/), BoneLib 3.2.2 (Mods/BoneLib.dll + Plugins/BoneLibUpdater.dll), Mods/Doppel.dll, and UserData/Bot Player/ with the stock starter brain (brain.json tracks weights, tries, kills, closeCalls, minutes).

Gotcha: MelonLoader's version.dll proxy needs a Wine override or it never loads. Set per-game via the prefix registry rather than Steam launch options — "version"="native,builtin" under [Software\\Wine\\DllOverrides] in compatdata/1592190/pfx/user.reg. Fallback is Steam launch option WINEDLLOVERRIDES="version=n,b" %command%.

Saves live in compatdata/1592190/pfx/drive_c/users/steamuser/AppData/LocalLow/Stress Level Zero/BONELAB/Saves/ as save_YYYYMMDD.HHMMSS.save.json.

Doppel dependency trap: Thunderstore's gnonme-BoneLib-3.1.3 is a dead stub with no DLL; the live package is bonelib-BoneLib.

[[Inbox]]

[[Inbox]]
