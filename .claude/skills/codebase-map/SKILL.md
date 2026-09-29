---
name: codebase-map
description: Where behaviour lives in this repo — a per-area map of which files and line ranges own what, so you can open the right file instead of searching for it. Use before hunting for where something is implemented, before adding a feature that touches existing behaviour, and when a change seems to need edits in more places than expected.
---

# Where things live

This repo is small, but each file mixes several concerns, so the pages below
still save a read. They are maps: for each area, which files own it and roughly where in
them.

**Pick the area, open that one page, then go straight to the file.** Do not read
every page — that defeats the point.

<!-- BEGIN GENERATED INDEX -- edit the pages, not this block -->

## Where things live

One page per area of the system. Read before going looking for where
something is implemented.

| page | covers | verified |
|---|---|---|
| [JSX rendering](../../../docs/architecture/jsx-rendering.md) | how JSX source becomes an HTML page, which scripts the page loads, how the component gets mounted, where to add a library | 2026-09-29 |
| [MCP endpoint and tools](../../../docs/architecture/mcp-endpoint.md) | where the publish_artifact / list / get / delete tools are defined, how POST /mcp is handled, where the API key is checked | 2026-09-29 |
| [Storage and gallery](../../../docs/architecture/storage-and-gallery.md) | where published artifacts are written on disk, how metadata is stored, how the gallery page and its delete button work, what survives a container restart | 2026-09-29 |

<!-- END GENERATED INDEX -->

The same table, browsable, is [docs/README.md](../../../docs/README.md).

## How to read a line range

Every entry is `file — lines — what lives there`. **The line numbers are a
starting point, not an address.** They drift with every commit that touches the
file above them, and nothing regenerates them.

So: jump to roughly that line, then confirm you are in the right place by what the
code says, not by the number. If a range is off by more than a screen or two, fix
it in the page and re-date it — that is a one-line edit and it is how the map
stays worth having.

Every file under `src/` is short (the largest, `src/mcp.js`, is about 200
lines), so a range mostly tells you which of several things in a file you want,
not where to scroll.

## What this map does not tell you

It tells you **where**, not **why it is dangerous**. Several of the areas below
have failure modes that produce no error message. Those are catalogued
separately, by symptom, in the `repo-maintenance` skill and in
[docs/README.md](../../../docs/README.md). If you are about to change shared state
or anything on the page template in `src/template.js` (every published JSX artifact is a copy of it), look there first.

## Keeping it accurate

A map that lies is worse than none, because it sends people confidently to the
wrong file. Two habits keep it honest:

- **Re-date what you touch.** If you worked in an area and the page was right,
  bump `verified:`. If it was wrong, fix it and bump.
- **Add an area when you create one, not later.** A new subsystem with no page is
  invisible; the next person re-derives its shape from scratch.

To add a page, follow the same rules as any other doc — see the `repo-maintenance`
skill. Frontmatter for an area page is `title`, `covers` (what someone would be
looking for, phrased as they would phrase it), and `verified`. Then run
`scripts/gen-docs-index.sh`; the block above is generated and hand edits to it
are overwritten.
