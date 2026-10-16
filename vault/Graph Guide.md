---
tags:
  - note/guide
created: 2026-10-15
---

# Graph Guide

How to build the graph **by hand** — one note, one `[[link]]`, at a time.

> [!tip] The one-sentence version
> The graph is nothing but your links. No links → lonely dots. Links → edges. Everything below is a way of adding more edges.

## 1. A note is just a file

Every note is a plain `.md` file in this folder. That's it. Rename the file, the note changes. Delete it, the note's gone.

- **New note:** `Ctrl+N`
- **Or:** click any `[[link]]` to a note that doesn't exist yet — Obsidian creates it for you (this is the fastest way to grow).

## 2. The link syntax — this is the whole trick

| You type                    | You get                                            |
| --------------------------- | -------------------------------------------------- |
| `[[Note Name]]`             | A link to that note (and an **edge** in the graph) |
| `[[Note Name\|shown text]]` | Same link, displays "shown text"                   |
| `[[Note Name#Heading]]`     | Jumps to a *heading* inside that note              |
| `[[Note Name^block-id]]`    | Jumps to a specific *block*                        |
| `![[Note Name]]`            | **Embeds** the whole note inline (transclusion)    |
| `[[Brand New Note]]`        | Doesn't exist? Click it → creates the note         |


## 3. Making an edge

Two ways links happen:

1. **You write it.** Type `[[` anywhere in a note and Obsidian pops an autocomplete of every note you can link to.
2. **Backlinks (automatic).** You only ever write one direction. If `A` links to `B`, Obsidian *automatically* shows `A` in `B`'s **Backlinks** panel (right sidebar). So:
   - **Outgoing links** = what this note points to.
   - **Backlinks** = what points *to* this note.
   You never have to write the reverse link yourself.

## 4. The build loop (do this on repeat)

```
open a note  →  ask "what does this connect to?"  →  type [[target]]
     ↑                                                        │
     └──────────  click it, create it if missing  ←───────────┘
```

That's the entire craft. Open [[Welcome]]. Think *"what's this related to?"* — type `[[`. Pick or create. Move to the new note. Repeat. Ten minutes of this and your graph looks alive.

## 5. The graph view

- **`Ctrl+G`** → whole-vault graph.
- **Local graph** → `Ctrl+P` → *"Open local graph"*. Shows **only this note's neighborhood**. This one is actually useful; the big one is eye candy.
- **Color it:** top-right of graph → **Groups** → `+` → type a query, pick a color. Examples:
  - `tag:#project`
  - `tag:#guide`
  - `path:Daily`
- **Filters:** the graph panel can also filter by search text, hide orphans, show tags.

## 6. Tags & frontmatter — structure the graph

Tags let you color, filter, and query. Put them in the frontmatter at the very top:

```markdown
---
tags:
  - project
  - guide
created: 2026-10-15
---
```

Or inline, anywhere in the text: `#project`.

Tags are how the color **Groups** know what to paint. Link = edge; tag = category. Together they turn a hairball into a map.

## 7. Hygiene — the two things to watch

- **Dangling links** (show up in a different color, "unresolved"): you linked to a note that doesn't exist. Either **click to create it**, or delete the link. Not an error — a to-do.
- **Orphans:** notes with **no links in or out**. They float alone. Fix by linking them into something. (Frontmatter/tags don't count as links.)

## 8. Naming rules (save yourself pain)

- **Filenames must be unique** for short `[[links]]` to work.
- Spaces are fine — `[[Graph Guide]]` works.
- Renaming a note: Obsidian can auto-update every link pointing to it (enable *Options → Files & Links → Automatically update internal links*).

## 9. Practice — go do it now

1. Make a note (`Ctrl+N`), name it anything. Add the line `See also: [[Graph Guide]]` and save. Watch the edge appear in the graph.
2. Open two notes you care about and link them together.
3. Hit `Ctrl+G`, then open the **local graph** of the note with the most links.

> [!warning] The trap
> The graph is the most fun part of Obsidian and the least valuable. Pretty dots aren't knowledge — **links are**. A thousand linked notes is a brain; a thousand colored dots is a screensaver. Play for five minutes, then go write.

