---
tags:
  - memory/gaming
created: 2026-10-22T23:04:33
source: agent
---

On-foot AI in Brick Rigs is possible, contrary to the earlier assumption that it would need hand-rolled trace steering. AAIController::MoveToLocation(Dest, AcceptanceRadius, bStopOnOverlap, bUsePathfinding, bProjectDestinationToNavigation, bCanStrafe, FilterClass, bAllowPartialPath) and UAIBlueprintHelperLibrary::SimpleMoveToLocation(Controller, Goal) are exposed to reflection, and the game ships ABrickAIController : AAIController as the native zombie brain. A spawned dummy given an AI controller can walk the navmesh. The requirement is a map with navmesh, which the official maps have because the zombie mode pathfinds. Same trick would give pedestrians, medics on foot or riot lines, all of which need no custom path planner.

[[Inbox]]
