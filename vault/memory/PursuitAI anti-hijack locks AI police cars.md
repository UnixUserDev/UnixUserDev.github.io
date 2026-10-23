---
tags:
  - memory/gaming
created: 2026-10-22T16:41:42
source: agent
---

PursuitAI (the Brick Rigs police mod ported to Linux) now refuses player hijacking of AI-driven cars. The first cut assumed CanEnterVehicle could be refused from a UE4SS pre-hook; the 1.11.7 CXX dump proved it returns void, so that never blocked anything. The working design post-hooks BrickCharacter:EnterVehicle and BrickCharacter:ServerEnterVehicle and force-ejects the player in the same frame, with the 100 ms guard as fallback. Intrusions add strikes (3 within 120 s by default), and a strike or grace ban benches the player for 600 s: ejected and killed on sight. The ban is mod-side and in memory, not a game-server kick; [[Brick Rigs server kick supports a duration and reason]] is the upgrade path. Settings live in [anti_hijack] (enabled, prevent_entry, grace_s, ban_s, strikes, strike_window_s, kill) and in the F8 fold "Защита машин копов". Cars without an AI driver stay enterable on purpose so a player can sit in one to mark it. Signatures for all of it come from [[Brick Rigs SDK dump makes the whole game scriptable]].

[[Inbox]]
