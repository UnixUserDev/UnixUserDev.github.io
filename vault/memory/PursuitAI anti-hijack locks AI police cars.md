---
tags:
  - memory/gaming
created: 2026-10-22T16:41:42
source: agent
---

PursuitAI (the Brick Rigs police mod ported to Linux) now refuses player hijacking of AI-driven cars. Layer 1 blocks the game's own CanEnterVehicle query through a UE4SS pre-hook (BrickCharacter:CanEnterVehicle) so the entry prompt never appears. Layer 2 is a 100 ms guard in the mod tick: any player-controlled character found in a car that has an AI driver bound (pursuit or city watch) is force-ejected, and if the ejection cannot clear the seat they are ejected and killed after grace_s. Intrusions add strikes (3 within 120 s by default), and a strike or grace ban benches the player for 600 s: entry refused, ejected and killed on sight. The ban is mod-side and in memory, not a game-server kick. Settings live in [anti_hijack] (enabled, prevent_entry, grace_s, ban_s, strikes, strike_window_s, kill) and in the F8 fold "Защита машин копов". Cars without an AI driver stay enterable on purpose so a player can sit in one to mark it.

[[Inbox]]
