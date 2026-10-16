---
title: Web fetch by client
tags:
  - note/agents
created: 2026-10-16
---

# Web fetch by client

Which URL-fetch / web-fetch tools each model will actually accept. Learned the hard way.

| Client       | `.html` | `.txt` | `.json` | Notes                                                                                                                                                                                                            |
| ------------ | ------- | ------ | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Gemini**   | ✅       | ❌      | ❌       | Non-HTML fails with `URL_FETCH_STATUS_MISC_ERROR`. A plain `.txt` and a `.json` both failed; the root HTML URL went through immediately. Its fetcher only accepts `text/html`.                                   |
| **ChatGPT**  | ✅       | ✅      | ✅       | **Corrected 2026-10-16 — the original claim was WRONG.** ChatGPT read `/static/contentIndex.json` (note bodies and all), the light JSON, *and* the plain-text index. Not restricted to HTML. However, its flaky. |
| **DeepSeek** | ✅       | ✅      | ✅       | Accepts **all three**. No format restriction observed.                                                                                                                                                           |

## Correction (2026-10-16)

**ChatGPT can read `.json` and `.txt`.** An earlier version of this note said otherwise — that claim was
wrong and is retracted. Only **Gemini** has shown a format restriction. Treat per-client format support
as volatile: publish all three formats, and test rather than assume.

## The full index is the rich one

`static/contentIndex.json` carries each note's `title`, `filePath`, `links`, `tags`, **and full
`content`** — i.e. the whole vault in one fetch. The *light* JSON is the same minus bodies, for
cheap navigation. If a client can read JSON at all, **this is the one to give it.**

## The practical consequence

Never expose **one** artifact format. Whatever you want an agent to read, publish it in **three** shapes:

- **`.html`** — for HTML-only fetchers (Gemini, ChatGPT)
- **`.json`** — for JSON-only / structured clients
- **`.txt`** — for `text/plain`-only fetchers

That's exactly why this vault's map exists three times, with the same content. Live, on the published vault:

- `https://unixuserdev.github.io/obsidian-vault/llms.html`
- `https://unixuserdev.github.io/obsidian-vault/llms.json`
- `https://unixuserdev.github.io/obsidian-vault/llms.txt`

Diagnosis trick: if a fetch fails, try the **HTML** version of the same thing. If HTML works, it's a content-type restriction, not a network block.

[[Inbox]]
