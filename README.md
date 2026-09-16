# mcp-zyte

Zyte API MCP — unified web fetch + AI extraction (zyte.com)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1576+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `zyte_fetch` | Fetch a web page through Zyte API and return its content. By default returns the raw HTTP response body (decoded to text). For JS-heavy sites that need a real browser, pass render:true to get browser-rendered HTML instead. Example: zyte_fetch({ url: "https://example.com", _apiKey: "your-key" }) |
| `zyte_extract` | AI-powered automatic extraction of structured data from a web page. Set type to 'product' for e-commerce product pages (name, price, currency, images, SKU, availability...) or 'article' for news/blog pages (headline, author, date, body text...). Example: zyte_extract({ url: "https://shop.example.com/item/123", type: "product", _apiKey: "your-key" }) |
| `zyte_screenshot` | Capture a screenshot of a web page via Zyte API (rendered in a headless browser). Returns metadata about the base64-encoded PNG (its length) rather than inlining the full blob. Example: zyte_screenshot({ url: "https://example.com", _apiKey: "your-key" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "zyte": {
      "url": "https://gateway.pipeworx.io/zyte/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/zyte/mcp` returns the tools in the table
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

Both URLs reach the same gateway and the same 1576+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "zyte": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-zyte"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-zyte
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Zyte data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
