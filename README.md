# mcp-ilostat

ILOSTAT (International Labour Organization statistics) MCP — global labour

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1683+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `list_dataflows` | Browse or keyword-search ILOSTAT datasets (dataflows) from the International Labour Organization. Each result has an `id` (the dataflowId you pass to get_data / dataflow_structure) and an English name. ILO publishes ~1,200 datasets covering employment, unemployment, labour force participation, wages, hours worked, informality, working poverty, child labour, and occupational safety. Always pass `query` to filter unless you really want the whole list. Example: list_dataflows({ query: "unemployment rate" }) or list_dataflows({ query: "minimum wage" }). |
| `dataflow_structure` | Get the structure (Data Structure Definition) of one ILOSTAT dataset: its ordered dimensions and, for each, the valid codes. Use this to learn how to build the dot-separated SDMX key for get_data. The key positions correspond to the dimensions in order; an empty position is a wildcard. Common dimensions are REF_AREA (ISO3 country code, e.g. "USA", "FRA"), FREQ (A=annual, Q=quarterly, M=monthly), SEX, AGE, and MEASURE. Always call this before get_data. Example: dataflow_structure({ dataflow_id: "DF_SDG_0852_SEX_AGE_RT" }). |
| `get_data` | Pull observations from an ILOSTAT dataset. `key` is a dot-separated SDMX dimension filter, one position per dimension in the order given by dataflow_structure; leave a position empty to wildcard it. ILO keys typically start with REF_AREA then FREQ; e.g. get_data({ dataflow_id: "DF_SDG_0852_SEX_AGE_RT", key: "USA.A...", start_period: "2015", end_period: "2024" }) selects the USA, annual frequency, and wildcards the remaining dimensions. Omit `key` (or pass "") to fetch all series — caution, this can be large. Returns decoded series with their dimension labels and per-period values. |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "ilostat": {
      "url": "https://gateway.pipeworx.io/ilostat/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/ilostat/mcp` returns the tools in the table
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

Both URLs reach the same gateway and the same 1683+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/ilostat_list_dataflows \
  -H 'Content-Type: application/json' \
  -d '{"query":"unemployment rate"}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/ilostat_list_dataflows`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "ilostat": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-ilostat"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-ilostat
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Ilostat data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
