# mcp-ilostat

ILOSTAT (International Labour Organization statistics) MCP — global labour

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1394+ live data sources.

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

Or connect to the full Pipeworx gateway for access to all 1394+ data sources:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English:

```
ask_pipeworx({ question: "your question about Ilostat data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
