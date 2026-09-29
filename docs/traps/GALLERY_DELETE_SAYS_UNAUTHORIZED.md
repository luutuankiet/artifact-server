---
symptom: "clicking Delete in the gallery shows 'Error: Unauthorized' and the artifact stays"
area: storage-and-gallery
verified: 2026-09-29
---

# Gallery Delete says "Unauthorized"

**Symptom.** With `API_KEY` set, the gallery's Delete button confirms, then a red
toast says `Error: Unauthorized` and nothing is removed. With `API_KEY` empty it
works.

**Mechanism.** The route requires the key (`src/index.js`, lines 33–37):

```js
const providedKey = req.query.apikey || req.headers['x-api-key'];
if (API_KEY && providedKey !== API_KEY) {
  return res.status(401).json({ error: 'Unauthorized' });
}
```

but the button's script in `src/gallery.js` (around line 69) sends neither:

```js
const res = await fetch('/api/artifacts/' + slug, { method: 'DELETE' });
```

This is not a deliberate lock. The gallery is public, so it cannot embed the key
in the page, and it has no way to ask for one.

**Workarounds today.** Call the `delete_artifact` MCP tool with the key, or send
the request by hand with `x-api-key`, or remove `artifacts/<slug>.html` and
`artifacts/.meta/<slug>.json` on disk.

**Fix, for a maintainer.** Have the button prompt for the key (and keep it in
`sessionStorage`), then send it as `x-api-key`. Delete this page when that lands.

**How to verify.** Start with `API_KEY=x npm start`, publish anything, click
Delete in the gallery. Today you get the toast; after a fix it deletes.

## General rule

A public page cannot carry a secret. Any action on it that the server gates
needs its own way to collect the credential from the person clicking.
