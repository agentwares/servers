# agentguard cloud

The hosted control plane for agentguard, the MCP policy proxy: spend caps, approvals for destructive tools, a kill switch, dry-run diffs, a loop breaker and a hash-chained audit log.

|                       |                                                                                                                                                                                            |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Service**           | https://agentwares-agentguard.vercel.app                                                                                                                                                   |
| **MCP endpoint**      | `https://agentwares-agentguard.vercel.app/api/mcp` (Streamable HTTP)                                                                                                                       |
| **Registry name**     | `io.github.agentwares/agentguard-cloud` (v0.1.1)                                                                                                                                           |
| **Tools**             | `agentguard_get_pricing`, `agentguard_spend_report`, `agentguard_create_proxy`, `agentguard_set_policy`, `agentguard_get_run`, `agentguard_export_audit`, `agentguard_recommend_allowlist` |
| **In this directory** | [`server.json`](server.json) · [`llms.txt`](llms.txt) · [`pricing.json`](pricing.json) · [tool reference](API.md)                                                                          |

agentguard sits between an agent and its MCP servers and refuses the calls your policy does not
allow. **The proxy, the policy engine and the CLI are open source** and live in
[agentwares/agentguard](https://github.com/agentwares/agentguard) — `npx @agentwares/agentguard` runs
the whole thing locally with no account.

This listing is the hosted layer on top: create a proxy in front of an MCP server, push a policy,
read a run report with the diff a dry run would have produced, export the hash-chained audit log, and
ask for a least-privilege allowlist derived from what the agent actually called.

A live view of a seeded demo proxy — calls, policy decisions and the audit chain — is public at
[`/demo`](https://agentwares-agentguard.vercel.app/demo).

## Authentication

`Authorization: Bearer <account key>`, created in the dashboard at [`/dashboard`](https://agentwares-agentguard.vercel.app/dashboard)
after signing in with GitHub. Only `agentguard_get_pricing` works without a key.

## First call

No key needed — `agentguard_get_pricing` is the one open tool.

```sh
curl -s -X POST https://agentwares-agentguard.vercel.app/api/mcp \
  -H 'content-type: application/json' \
  -H 'accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"agentguard_get_pricing","arguments":{}}}'
```

## MCP client configuration

Claude Desktop, Cursor, Claude Code and anything else that speaks Streamable HTTP:

```json
{
  "mcpServers": {
    "agentguard-cloud": {
      "url": "https://agentwares-agentguard.vercel.app/api/mcp",
      "headers": {
        "Authorization": "Bearer <your api key>"
      }
    }
  }
}
```

The `headers` block is optional: this server also answers without a key — see Authentication above
for what you get.

## Endpoints

| Endpoint            | What                                                               |
| ------------------- | ------------------------------------------------------------------ |
| `POST /api/mcp`     | the MCP server (Streamable HTTP)                                   |
| `GET /openapi.json` | REST equivalents of the management tools                           |
| `GET /demo`         | public live view of the seeded demo proxy                          |
| `GET /status/<id>`  | public status page for a proxy (+ .svg, /trust.json, /merkle.json) |
| `GET /llms.txt`     | machine docs                                                       |
| `GET /pricing.json` | machine-readable pricing                                           |
| `GET /api/health`   | health                                                             |

## Pricing

Monthly bands by included tool calls (Starter / Pro / Team) with a free trial allowance, a soft alert
at 80% of the band and a hard stop at 150% — over which a call returns `BAND_EXCEEDED` with an
upgrade URL rather than a surprise invoice. Numbers in [`pricing.json`](pricing.json).

## Source and issues

This is a hosted service; its implementation is not in this repository (see the
[root README](../README.md)). **Open an issue here** for anything about the running service — a wrong
answer, a dead endpoint, a tool description that misled your agent, a capability you need.

The files in this directory are generated from the service, so please report a problem rather than
sending a documentation patch.
