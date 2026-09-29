---
symptom: "JSX artifact renders as a blank page or shows raw code as text when the source contains the string </script>"
area: jsx-rendering
verified: 2026-09-29
---

# A `</script>` in the source breaks the artifact

**Symptom.** A JSX artifact that mentions `</script>` anywhere in its source (in
a string, a template literal, a code sample it displays) renders blank, shows a
compile error, or dumps the tail of the source onto the page as text.

**Mechanism.** The source is embedded inside
`<script type="text/babel">…</script>`. The browser's HTML parser ends that
element at the first `</script>` it sees, whatever the JavaScript around it
means, so Babel gets a truncated program and the rest becomes page text.

`src/template.js` already contains the escape, at lines 56–57:

```js
const escapedSource = source
  .replace(/<\/script>/gi, '<\\/script>');
```

but the template interpolates the unescaped variable at line 75:

```js
${source}
```

`escapedSource` is computed and never used.

**Fix, for a maintainer.** Interpolate `${escapedSource}` instead of `${source}`.
`<\/script>` is the same string to JavaScript, so nothing else changes. Delete
this page when that lands.

**How to verify.** Publish a component that renders
`<pre>{"</script>"}</pre>`. Today the page breaks; after the fix it shows the
literal text.

## General rule

Anything embedded inside a `<script>` element has to be escaped for the HTML
parser, not only for JavaScript. The HTML parser runs first and knows nothing
about string literals.
