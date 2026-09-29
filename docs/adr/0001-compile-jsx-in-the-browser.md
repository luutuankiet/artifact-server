# Compile JSX in the viewer's browser, not on the server

Two ways to turn JSX into a viewable page were on the table: build each artifact
on the server with Vite or esbuild into one self-contained HTML file, or ship an
HTML wrapper that loads React and Babel standalone from a CDN and compiles in the
browser. We chose the browser. The container then needs no build toolchain, has
no per-publish build step to fail or to time, and stays within the few hundred MB
of RAM it is allowed.

## Considered options

- **Server-side build (Vite / esbuild).** Smaller pages, faster first render,
  works offline once loaded, and only the Tailwind classes actually used. Costs a
  Node build toolchain and a bundling step inside the container on every publish.
- **Pre-bundling the library set into the image.** Rejected with the above:
  it is the same toolchain by another route.

## Consequences

- Every artifact page downloads the Babel compiler on every view and compiles
  before it paints. Accepted knowingly.
- Artifacts are not self-contained: they depend on the CDN being reachable from
  the viewer's browser.
- Library versions are CDN URLs in `libs.json`, and source must use UMD globals
  rather than `import` statements.
- Compile errors surface in the browser, not as an error from `publish_artifact`.
