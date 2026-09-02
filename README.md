# primo-mcp

An Agent Plugin that points Cursor, Grok Bot, and other Agent-Plugins-compatible clients
at [Primo](https://www.getprimo.com)'s hosted MCP server, so an agent can answer and act
on IT fleet questions: devices, employees, tickets, accessories, SaaS identities,
installed software and CVEs, MDM controls, compliance alerts, onboarding and offboarding.

There is nothing to install and no server to run. The plugin is a manifest, an MCP
endpoint, and a skill that tells the agent how to use it.

## What's in the tree

| File | Purpose |
| --- | --- |
| `plugin.json` | Agent Plugins 1.0.0 manifest |
| `mcp.json` | Declares the hosted `primo` streamable-HTTP server |
| `skills/primo-mcp/SKILL.md` | When to reach for Primo, and how to use it safely |

## The endpoint

```
https://api.getprimo.com/mcp
```

Streamable HTTP. Opening it in a browser returns **404**. That is expected. It is an MCP
transport endpoint, not a web page. Use an MCP client.

## Authentication

OAuth, handled by the client. On first connection the client opens a Primo login and
stores the token itself.

No API keys, no headers, no environment variables. Nothing in this repo holds a secret,
and nothing here should ever be edited to hold one.

## Read-only vs. write

The default URL is **read-only**. Write tools are still listed, but calling one returns
HTTP 403 with `WRITE_OPERATION_BLOCKED_BY_READONLY_MODE`.

To allow mutations, connect with:

```
https://api.getprimo.com/mcp?readOnly=false
```

Write mode covers real actions on real hardware and real access. It locks and wipes
devices, deprovisions SaaS identities, runs scripts, and executes offboarding. Enable it
deliberately, and keep the read-only URL configured for anyone who only needs answers.

## Multi-tenant

If your Primo account manages more than one company:

- Set a default in Primo: **My Account → AI Settings → Default MCP Tenant**
- Or scope a single connection with a query parameter: `?x-company-id=<company-id>`

Both parameters combine, e.g.
`https://api.getprimo.com/mcp?readOnly=false&x-company-id=<company-id>`.

## Tool surface

The MCP server covers close to the whole Primo REST surface, and it moves as Primo ships.
This repo deliberately does not list tool names. Read the live `tools/list` from the
connected server and use the schemas it returns.

## Links

- [Primo MCP server docs](https://docs.getprimo.com/get-started/primo-mcp-server)
- [REST API reference](https://api.getprimo.com/apidoc)
- [OpenAPI document](https://api.getprimo.com/openapi.json)

## License

MIT. See [LICENSE](./LICENSE).
