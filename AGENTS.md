# artifact-server

A small self-hosted server that turns React/JSX (or raw HTML) into a page you
can open in a browser. An agent calls the `publish_artifact` MCP tool with the
source; the server wraps it in an HTML page, stores it on disk, and returns a
URL. A gallery at `/` lists and deletes what has been published. It exists so an
agent session with no browser of its own can produce interactive artifacts the
way a chat app with a built-in artifact renderer does.

## Hard constraints

- **JSX is compiled in the viewer's browser, not on the server.** The page loads
  React, ReactDOM, Babel standalone and Tailwind from a CDN. There is no build
  step and no bundler in the container. See `docs/adr/0001`.
- **The filesystem is the database.** `artifacts/<slug>.html` plus a sidecar
  `artifacts/.meta/<slug>.json`. No other state survives a restart.
- **Viewing is public; writing needs `API_KEY`.** An empty `API_KEY` means fully
  open. The MCP handshake methods are unauthenticated on purpose (`docs/adr/0003`).
- **One container, a few hundred MB of RAM.** No headless browser, no heavy runtime.

## Layout

```
src/index.js      Express app: static /artifacts, gallery at /, REST delete, mounts MCP
src/mcp.js        hand-rolled JSON-RPC MCP endpoint (POST /mcp) and the four tools
src/template.js   JSX -> HTML page template; reads libs.json
src/storage.js    read/write artifacts/ and artifacts/.meta/
src/gallery.js    server-rendered gallery HTML
libs.json         CDN library manifest (core + optional)
Dockerfile, docker-compose.yaml   single-container deploy
docs/             architecture, traps, reference, decision records
scripts/gen-docs-index.sh         regenerates docs/README.md and the skill indexes
```

## Commands

```sh
npm install
npm start          # node src/index.js, listens on $PORT (default 3333)
npm run dev        # same, with node --watch
```

There is no test runner, no linter and no type checker.

<!-- Standard block. Everything above belongs to this project; everything below is
     the pointer every repo laid out this way carries. -->

## Documentation

Indexed in [docs/README.md](docs/README.md). Every page is self-contained — it
assumes you opened that one file and have nothing else loaded.

| where | what | read it |
|---|---|---|
| [architecture/](docs/architecture/) | where behaviour lives, one page per area | before going looking for something |
| [traps/](docs/traps/) | failure modes with no error message, indexed by symptom | before debugging something wrong but not crashing |
| [reference/](docs/reference/) | simply true, expensive to re-derive | when you need the detail |
| [adr/](docs/adr/) | why the repo is the way it is | before changing something that looks odd |

## Before you wrap up

Leave the repo holding what this session cost you to find out. Four rules.

1. **Sort it, and expect most of it to go nowhere.** A next action is an issue. A
   durable, expensive-to-re-derive fact is a page. A choice that was hard to
   reverse, surprising without context and a real trade-off is a decision record
   under `docs/adr/`. Status, dates, version pins and plans are none of those —
   delete them.
2. **A doc is the last resort.** Type error → test → comment at the site → doc.
   Name the single line you would have commented instead; if you can name it,
   comment it and stop.
3. **Verify against running code before writing, and date the page `verified:`.**
   Anything remembered from earlier in the session is stale until re-read. Deleting
   a draft because the problem is already fixed is a success.
4. **Append, never rewrite.** Supersede a merged decision record with a new one
   naming what it replaces. A trap filename is an identifier quoted elsewhere:
   edit the body, never the name.

Then run `scripts/gen-docs-index.sh`. Never hand-maintain an index.
