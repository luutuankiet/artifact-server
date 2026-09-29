# Documentation

Every page here is written for a maintainer six months from now who opened
exactly this file from a search result and has nothing else loaded.

This index is generated. Run `scripts/gen-docs-index.sh` after adding or
renaming a page; `--check` fails if it is stale.

<!-- BEGIN GENERATED INDEX -- edit the pages, not this block -->

## Where things live

One page per area of the system. Read before going looking for where
something is implemented.

| page | covers | verified |
|---|---|---|
| [JSX rendering](architecture/jsx-rendering.md) | how JSX source becomes an HTML page, which scripts the page loads, how the component gets mounted, where to add a library | 2026-09-29 |
| [MCP endpoint and tools](architecture/mcp-endpoint.md) | where the publish_artifact / list / get / delete tools are defined, how POST /mcp is handled, where the API key is checked | 2026-09-29 |
| [Storage and gallery](architecture/storage-and-gallery.md) | where published artifacts are written on disk, how metadata is stored, how the gallery page and its delete button work, what survives a container restart | 2026-09-29 |

## Traps

Failure modes that produce no error message, indexed by the symptom you
would observe. Read before debugging behaviour that is wrong but not
crashing.

| symptom | page | area | verified |
|---|---|---|---|
| published JSX artifact shows 'Error: No App or default export found in artifact source.' instead of the component | [ARTIFACT_SHOWS_NO_APP_OR_DEFAULT_EXPORT_FOUND](traps/ARTIFACT_SHOWS_NO_APP_OR_DEFAULT_EXPORT_FOUND.md) | jsx-rendering | 2026-09-29 |
| JSX artifact renders as a blank page or shows raw code as text when the source contains the string </script> | [CLOSING_SCRIPT_TAG_IN_SOURCE_BREAKS_ARTIFACT](traps/CLOSING_SCRIPT_TAG_IN_SOURCE_BREAKS_ARTIFACT.md) | jsx-rendering | 2026-09-29 |
| clicking Delete in the gallery shows 'Error: Unauthorized' and the artifact stays | [GALLERY_DELETE_SAYS_UNAUTHORIZED](traps/GALLERY_DELETE_SAYS_UNAUTHORIZED.md) | storage-and-gallery | 2026-09-29 |

## Reference

Simply true, and expensive to re-derive.

| page | summary | verified |
|---|---|---|
| [CDN library manifest](reference/cdn-library-manifest.md) | what libs.json contains, which libraries every JSX artifact gets, how to add one, and why the optional set looks the way it does | 2026-09-29 |

## Decisions

Why the repo is the way it is. A merged decision is immutable -- supersede
it with a new one rather than editing it.

- [Compile JSX in the viewer's browser, not on the server](adr/0001-compile-jsx-in-the-browser.md)
- [The filesystem is the database](adr/0002-filesystem-is-the-database.md)
- [The MCP handshake does not require the API key](adr/0003-mcp-handshake-skips-api-key.md)
- [Planning notes replaced by docs/ and a small AGENTS.md](adr/0004-planning-notes-promoted-into-docs.md)

<!-- END GENERATED INDEX -->
