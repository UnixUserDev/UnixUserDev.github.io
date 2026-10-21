---
tags:
  - memory/guide
created: 2026-10-20T19:10:03
source: agent
---

mod.io's REST API (api.mod.io) rejects anonymous requests with 401 "malformed/missing api_key", which makes it look like every mod file needs an account. It doesn't.

The mod web page renders a plain signed CDN URL for the release file, and that URL downloads with no key and no cookies:
https://g-<shard>.modapi.io/v1/games/<game_id>/mods/<mod_id>/files/<file_id>/download

Example that worked: game 3809 (BONELAB), mod 5704051, file 7316411 returned 200 OK / 38 MB zip anonymously.

To find it: load the mod page in a real browser (the HTML is a JS shell, so curl gets 3 KB of nothing), then query the DOM for anchors whose href contains "modapi.io". Do not bother with api.mod.io unless an api_key is available.

Also: on mod.io pages the "Download" control is not a top-level button — it sits in the release-file row further down the page, which is why people report "there's no download button". Subscribing is not the same as downloading.

[[Inbox]]

[[Inbox]]
