---
title: JSX rendering
covers: how JSX source becomes an HTML page, which scripts the page loads, how the component gets mounted, where to add a library
verified: 2026-09-29
---

# JSX rendering

There is no server-side compile. `buildJsxHtml` returns a string: an HTML page
that loads React, ReactDOM, Babel standalone and Tailwind from a CDN, embeds the
JSX source in a `<script type="text/babel">` tag, and lets Babel compile it in
the viewer's browser.

Line numbers are a starting point, not an address; confirm by what the code
says and fix this page if a range has moved by more than a screen.

| file | lines | what lives there |
|---|---|---|
| `src/template.js` | 9–14 | `getLibs()`: reads `libs.json` once and caches it |
| `src/template.js` | 16–19 | `reloadLibs()`: clears the cache. Exported, but nothing calls it today, so edits to `libs.json` need a restart |
| `src/template.js` | 21–28 | `getAvailableLibraries()`: `name@version` for each optional lib, used in the MCP tool description |
| `src/template.js` | 30–54 | script tag assembly: core libs, then Tailwind always, then any requested optional libs by name. Unknown names are silently ignored |
| `src/template.js` | 56–57 | an escaped copy of the source that is computed and never used; see `docs/traps/CLOSING_SCRIPT_TAG_IN_SOURCE_BREAKS_ARTIFACT.md` |
| `src/template.js` | 59–100 | the page template itself |
| `src/template.js` | 74–89 | the Babel script: the raw source, then the auto-mount block |
| `src/template.js` | 90–98 | a `window` error listener that prints compile errors into `#root` if nothing rendered |
| `libs.json` | all | the CDN manifest; see `docs/reference/cdn-library-manifest.md` |

## How a component gets mounted

After the source, the template appends:

```js
const _ArtifactComponent = typeof App !== 'undefined' ? App
  : typeof default_export !== 'undefined' ? default_export
  : null;
```

and renders whichever it found into `#root`. In practice that means **the
top-level component must be named `App`**. See the trap page
`docs/traps/ARTIFACT_SHOWS_NO_APP_OR_DEFAULT_EXPORT_FOUND.md`.

Libraries are loaded as UMD scripts and exposed as globals (`React`,
`ReactDOM`, `Recharts`, `d3`, …, the `global` field in `libs.json`). The page
has no import map.

## `format: "html"`

HTML artifacts skip this file entirely: `src/mcp.js` stores the source string
unchanged. Nothing is injected, so an HTML artifact gets no Tailwind and no React
unless it brings its own.

## General rule

Every JSX artifact ever published is a copy of this one template string, frozen
at publish time. Changing the template changes future artifacts only; already
published pages keep whatever template they were built with.
