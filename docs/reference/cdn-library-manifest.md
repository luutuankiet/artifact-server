---
title: CDN library manifest
summary: what libs.json contains, which libraries every JSX artifact gets, how to add one, and why the optional set looks the way it does
verified: 2026-09-29
---

# CDN library manifest (`libs.json`)

`libs.json` at the repository root is the only place library versions and URLs
live. `src/template.js` reads it once and caches it; `src/mcp.js` reads it to
list the optional libraries in the `publish_artifact` tool description.

## Shape

```json
{
  "core":     { "<name>": { "version": "…", "cdn": "<url>", "global": "<window name>" } },
  "optional": { "<name>": { "version": "…", "cdn": "<url>", "global": "<window name>" } }
}
```

- **`core`**: React, ReactDOM and Babel standalone. Loaded on every JSX artifact.
- **`optional`**: loaded only when the caller lists the name in
  `publish_artifact`'s `libraries` argument. **Exception: `tailwindcss` is always
  loaded**, because most artifacts use its classes.
- **`global`** is the name the UMD bundle puts on `window`. Artifact source uses
  that name (`Recharts.LineChart`, `d3.select`, `THREE.Scene`), because the page
  has no module loader.

Every `cdn` URL must be a browser-ready UMD or plain script build, not an ES
module. It is dropped straight into a `<script src>` tag.

## Adding a library

Add an entry under `optional` and restart the server. `reloadLibs()` exists in
`src/template.js` but nothing calls it, so there is no hot reload. Clients see
the new name the next time they call `tools/list`.

A name the caller asks for that is not in the manifest is skipped silently.

## Why this set

The optional list mirrors what Claude.ai's built-in artifact renderer preloads
(Recharts, Lucide, D3, Three.js, Chart.js, Papaparse, mathjs, lodash, Tone.js),
so the same libraries are available to a component written there. `three` is
pinned to `0.128.0` for the same reason: that is the version such components
are written against.

Same libraries is not the same loading model. That renderer resolves
`import { LineChart } from 'recharts'`; this page has no import map, so a bare
`import` does not resolve here and the source has to use the global instead.

## General rule

Versions are pinned in exactly one file. Upgrading a library there changes
future artifacts only; already published pages keep the URLs they were built
with.
