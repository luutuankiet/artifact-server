---
title: Storage and gallery
covers: where published artifacts are written on disk, how metadata is stored, how the gallery page and its delete button work, what survives a container restart
verified: 2026-09-29
---

# Storage and gallery

Line numbers are a starting point, not an address; confirm by what the code
says and fix this page if a range has moved by more than a screen.

## On disk

```
artifacts/<slug>.html          the page, served at /artifacts/<slug>.html
artifacts/.meta/<slug>.json    title, format, description, libraries, sizes, created
```

| file | lines | what lives there |
|---|---|---|
| `src/storage.js` | 7–12 | the two directories, created at import time with a top-level `await` |
| `src/storage.js` | 14–29 | `saveArtifact`: writes both files. Same slug overwrites silently |
| `src/storage.js` | 31–64 | `listArtifacts`: every `.html` in `artifacts/`, newest `created` first |
| `src/storage.js` | 66–89 | `getArtifact` |
| `src/storage.js` | 91–99 | `deleteArtifact`: removes the HTML, then tries the sidecar |

**A missing sidecar is tolerated.** List and get fall back to the file's mtime
and use the slug as the title, so an `.html` dropped into `artifacts/` by hand
shows up in the gallery with `format: unknown`.

In the container only `artifacts/` is a volume
(`./data/artifacts:/app/artifacts` in `docker-compose.yaml`). Because `.meta/`
lives inside it, metadata persists too. Nothing else does.

## HTTP routes

| file | lines | route |
|---|---|---|
| `src/index.js` | 20 | `GET /artifacts/*`: `express.static` over `artifacts/` |
| `src/index.js` | 23–30 | `GET /`: the gallery, rendered server-side on every request |
| `src/index.js` | 33–46 | `DELETE /api/artifacts/:slug`: same key rules as MCP (`?apikey=` or `x-api-key`) |

## Gallery page

`src/gallery.js` is one function returning an HTML string: a table of
artifacts, with an inline `deleteArtifact(slug)` script (around line 66) that
calls the REST delete route and reloads. Mobile layout is a CSS media query that
turns rows into cards. See `docs/traps/GALLERY_DELETE_SAYS_UNAUTHORIZED.md`
before relying on that button when a key is configured.

## Leftovers from the original plan

The `Dockerfile` still creates an `incoming/` directory and `package.json`
still depends on `uuid`. Neither is used: sources are never staged, and session
ids come from `crypto.randomUUID`.

## General rule

`ls artifacts/` is the full inventory. There is no index to rebuild and no
state to reconcile; anything that needs to survive a restart must be a file
under `artifacts/`.
