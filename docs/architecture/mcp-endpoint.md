---
title: MCP endpoint and tools
covers: where the publish_artifact / list / get / delete tools are defined, how POST /mcp is handled, where the API key is checked
verified: 2026-09-29
---

# MCP endpoint and tools

The MCP server is hand-rolled JSON-RPC over plain Express, not the MCP SDK. It
speaks the request/response form of Streamable HTTP only: every call is one
`POST /mcp` answered with one JSON body.

Line numbers below are a starting point, not an address. Jump roughly there and
confirm by what the code says; if a range is off by more than a screen, fix it
here and re-date the page.

| file | lines | what lives there |
|---|---|---|
| `src/mcp.js` | 7–12 | `slugify(title)`: lowercase, non-alphanumerics to `-`, capped at 60 chars |
| `src/mcp.js` | 14–61 | `getToolDefinitions()`: the four tool schemas. The available-library list in `publish_artifact`'s description is built from `libs.json` at call time |
| `src/mcp.js` | 71–137 | `handleToolCall()`: the switch that implements each tool |
| `src/mcp.js` | 73–106 | `publish_artifact`: default slug is `YYYY-MM-DD-<slugify(title)>`; `format: "html"` stores the source as-is, `jsx` goes through `buildJsxHtml` |
| `src/mcp.js` | 141–196 | `handleMcp()`: registers `POST /mcp`, the API-key gate, and the JSON-RPC method switch |
| `src/mcp.js` | 144–152 | the API-key gate. `initialize`, `notifications/initialized`, `tools/list` and `ping` skip it on purpose |
| `src/mcp.js` | 199–201 | `GET /mcp` returns 405; there is no SSE stream |
| `src/index.js` | 49 | where `handleMcp` is mounted |

## Things that look odd and are deliberate

- **Handshake without a key.** Listing tools needs no key; calling one does. This
  lets an MCP proxy connect and discover tools without negotiating auth first.
  The reasoning is `docs/adr/0003-mcp-handshake-skips-api-key.md`.
- **The key can arrive two ways**: `?apikey=` on the URL or an `x-api-key`
  header. The REST delete route in `src/index.js` accepts the same two.
- **Sessions are issued but never read.** `initialize` stores an id in the
  in-memory `sessions` map (line 139) and returns it as `mcp-session-id`; nothing
  looks it up afterwards, and it is lost on restart. No behaviour depends on it.

## General rule

Anything a client can see before authenticating is decided in one list at
`src/mcp.js:146`. Adding a new JSON-RPC method means deciding which side of that
list it belongs on.
