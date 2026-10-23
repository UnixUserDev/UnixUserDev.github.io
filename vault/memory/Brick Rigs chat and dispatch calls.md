---
tags:
  - memory/gaming
created: 2026-10-22T23:04:33
source: agent
---

Chat from a Brick Rigs mod: ABrickPlayerController::Say(const FText& Message) sends a plain line, and SendChatMessage(EChatContext Context, const FText& Message) or ServerSendChatMessage sends a context-aware one. EChatContext is None=0, Global=1, Team=2, Admin=3, so Global reaches everyone and is not limited by the game mode's MaxLocalChatDistance. Build the FText with UKismetTextLibrary::Conv_StringToText(FString) before calling. FBrickChatMessage (the 0xA8 struct behind ClientReceiveChatMessage(s)) carries Type, Player, OtherPlayer, TextOption and option fields, but Say/SendChatMessage avoids laying it out by hand. This is the notification channel for anything the player cannot see happen, such as an incident director dispatching units.

[[Inbox]]
