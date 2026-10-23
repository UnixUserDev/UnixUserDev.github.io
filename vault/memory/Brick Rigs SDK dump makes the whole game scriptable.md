---
tags:
  - memory/gaming
created: 2026-10-22T23:04:29
source: agent
---

UE4SS can dump the full reflected SDK for Brick Rigs. Set GuiConsoleEnabled=1 in UE4SS-settings.ini, press Ctrl+O at the main menu (never after loading a map, it crashes) and run the CXX header dump; output lands in CXXHeaderDump next to the exe. The 1.11.7 dump gives 799 headers: 492 Brick Rigs classes, 142 structs, 93 enums, 1571 UFunctions in BrickRigs.hpp and about 7928 across the whole dump, each property with a byte offset. That turns every reflected call from a guess into a known signature, so "can we do X" becomes a grep. Limits: only UPROPERTY/UFUNCTION data is exposed, so private non-reflected state is invisible; Lua only gets 100 ms game-thread timers, not a per-frame tick; RegisterHook only attaches to native functions, not Blueprint-implemented ones; anything that changes multiplayer gameplay needs host authority.

[[Inbox]]
