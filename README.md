# @pipeworx/federal-rules

Text of the federal court rules -- Civil Procedure (FRCP), Evidence (FRE), Appellate Procedure (FRAP), Criminal Procedure (FRCrP) and Bankruptcy Procedure (FRBP) -- with subdivision lookup such as FRCP 64(b), FRE 803(6) or FRAP 4(a)(1)(B).

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1875+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

- `get_federal_rule(set, rule, subdivision?, include_notes?)` -- the full rule text, the requested subdivision on its own, the amendment credit line, and whether committee notes exist (returned when `include_notes` is true). An unknown rule or subdivision is an explicit not-found.
- `list_federal_rules(set)` -- every rule in a set with its number and title, plus the "as amended to" date the publisher states.
- `search_federal_rules(query, set?, limit?)` -- find rules by words in their title or number (titles only, not full text).

## Auth

Keyless.

## Data sources

- Legal Information Institute (Cornell Law School), Federal Rules of Civil Procedure, Federal Rules of Evidence, Federal Rules of Appellate Procedure, Federal Rules of Criminal Procedure, Federal Rules of Bankruptcy Procedure. The rules are authored by the Judicial Conference / Administrative Office of the U.S. Courts and are a US federal work.

Every call is read live from the publisher; nothing is stored. Subdivision selection is done by the pack from the indentation of the rule text, so a subdivision answer carries its sub-parts and stops at the next sibling. Search matches rule titles only.

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "federal-rules": {
      "url": "https://gateway.pipeworx.io/federal-rules/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/federal-rules/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1875+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/get_federal_rule \
  -H 'Content-Type: application/json' \
  -d '{"set":"frcp","rule":"64","subdivision":"(b)"}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/get_federal_rule`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-federal-rules` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/federal-rules/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "federal-rules": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-federal-rules"]
    }
  }
}
```

Without a key it calls anonymously, capped at 50 calls a day. A free account
raises that to 200 a day: get a key at https://pipeworx.io/signup and add
`"env": { "PIPEWORX_API_KEY": "<your key>" }` to the server entry.
The key is sent as a Bearer token, the same way a hosted connection sends it,
so calls are metered and billed exactly as they are there.

It lists **only** this pack's tools by default. Set `PIPEWORX_META_TOOLS=1` to
also list `ask_pipeworx` and the other gateway-wide tools.

Behind an HTTPS proxy: Node's built-in `fetch` does not read `HTTPS_PROXY` by
default. Newer Node releases honour it when `NODE_USE_ENV_PROXY=1` is set; check
your Node version's documentation.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Federal Rules data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
