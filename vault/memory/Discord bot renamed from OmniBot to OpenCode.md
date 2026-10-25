---
tags:
  - memory/projects
created: 2026-10-24T22:46:22
source: agent
---

The Discord bot app and bot username formerly called OmniBot (id 1528550786223570944, discriminator 2821) is now named **OpenCode**, renamed 2026-10-24. Its token env var changed from OMNIBOT_DISCORD_TOKEN to OPENCODE_DISCORD_TOKEN (defined in ~/.config/opencode/env.sh). No process hosts it — it is a bare token/app, so it reads offline unless a gateway client connects. Operating instructions live in the global skill opencode-bot. Renaming cannot be done with the bot token (PATCH /applications/@me silently no-ops); it needs a user session token: PATCH /api/v9/applications/{id} for the app name and PATCH /api/v9/applications/{id}/bot for the bot username.

[[Inbox]]
