---
symptom: "published JSX artifact shows 'Error: No App or default export found in artifact source.' instead of the component"
area: jsx-rendering
verified: 2026-09-29
---

# Artifact shows "No App or default export found"

**Symptom.** `publish_artifact` succeeds and returns a URL, but the page shows
only:

```
Error: No App or default export found in artifact source.
```

**Mechanism.** The page template in `src/template.js` (lines 78–80) mounts
whichever of two global names exists:

```js
const _ArtifactComponent = typeof App !== 'undefined' ? App
  : typeof default_export !== 'undefined' ? default_export
  : null;
```

Nothing ever defines `default_export`. Babel does not create a binding by that
name for `export default`, and the template does not either. So the second
branch never fires, and the only way to get mounted is for the source to declare
a top-level identifier called `App`.

| source | mounted? |
|---|---|
| `function App() { … }` | yes |
| `export default function App() { … }` | yes: the declaration still binds `App` |
| `export default function Dashboard() { … }` | **no** |
| `export default () => <div/>` | **no** |

**Fix, for a publisher.** Name the top-level component `App`.

**Fix, for a maintainer.** Either rewrite `export default` in the source before
embedding it (for example to `const default_export =`), or say "name your
component `App`" in the `publish_artifact` tool description in `src/mcp.js` so
calling agents learn it before they publish. Delete this page when that lands.

**How to verify.** Publish `export default function Hello(){ return <p>hi</p> }`
and open the URL. Today it shows the error above; after a fix it shows `hi`.

## General rule

The mount step only sees globals. Any convention the template relies on for
finding the component has to be stated to whoever writes the source, because the
failure is a rendered page, not an error returned by the tool.
