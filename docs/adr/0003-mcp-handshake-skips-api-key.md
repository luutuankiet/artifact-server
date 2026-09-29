# The MCP handshake does not require the API key

`POST /mcp` accepts `initialize`, `notifications/initialized`, `tools/list` and
`ping` without a key; only `tools/call` (and anything else not on that list)
requires it. With the key enforced on every method, the MCP proxy this server
sits behind (mcpproxy-go) ran into OAuth negotiation issues on connect, which
this server does not implement. Letting the handshake through fixed the connection
while keeping every state-changing call gated.

## Consequences

- Anyone who can reach `/mcp` can read the tool list, including the names and
  versions of the optional libraries. Nothing in it is secret.
- The allow-list is one array in `src/mcp.js` (`readOnlyMethods`). A new
  JSON-RPC method is gated by default unless someone adds it there.
- An empty `API_KEY` still means no auth at all, on every method.
