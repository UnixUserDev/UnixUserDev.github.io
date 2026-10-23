---
tags:
  - memory/gaming
created: 2026-10-22T23:04:29
source: agent
---

Brick Rigs has a real timed kick, not just a local ban: ABasePlayerController::ServerKickPlayer(FUniqueNetIdRepl& OtherPlayerId, FString OtherPlayerName, FString KickReason, const FTimespan& KickDuration). The session keeps FKickedPlayer entries (KickTime, KickDuration, KickReason) and EChatMessageType includes JoinBanned, so a repeat join is reported back. The host can call it with another player's UniqueId (UPROPERTY on the player state at 0x0250), which means a mod can remove a griefer for a fixed time with a reason instead of only blacklisting them in memory. Still unproven in-game: constructing FTimespan and passing FUniqueNetIdRepl from Lua needs a runtime test first.

[[Inbox]]
