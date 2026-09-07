# agentcheck

Drift and regression monitoring for production AI agents and MCP servers. Uptime-style checks, a public status page, a badge and `trust.json` are free; paid tiers re-run your suite when a model changes, replay production traces nightly and run an adversarial pack.

|                       |                                                                                                                                                                                                           |
| --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Service**           | https://agentwares-agentcheck.vercel.app                                                                                                                                                                  |
| **MCP endpoint**      | `https://agentwares-agentcheck.vercel.app/api/mcp` (Streamable HTTP)                                                                                                                                      |
| **Registry name**     | `io.github.agentwares/agentcheck` (v0.1.1)                                                                                                                                                                |
| **Tools**             | `agentcheck_get_status`, `agentcheck_get_pricing`, `agentcheck_create_target`, `agentcheck_add_check`, `agentcheck_run_now`, `agentcheck_record`, `agentcheck_promote_trace`, `agentcheck_list_incidents` |
| **In this directory** | [`server.json`](server.json) · [`llms.txt`](llms.txt) · [`pricing.json`](pricing.json) · [tool reference](API.md)                                                                                         |

agentcheck watches something that already works and tells you when it stops working the same way. A
target is any HTTP, MCP or A2A endpoint. Checks are $0 goldens (exact / contains / regex / JSON
schema / tool sequence) wherever the answer is deterministic, and a hash-cached LLM judge only where
it is not. It is quiet by design: it contacts you when a run changes, not on a schedule.

The public [model-drift index](https://agentwares-agentcheck.vercel.app/drift) re-runs a fixed prompt set against every frontier
model daily and publishes the pass rate and the answers that changed — free, no login, also at
[`/drift.json`](https://agentwares-agentcheck.vercel.app/drift.json).

Every MCP tool has a REST mirror under `/api/v1`; the OpenAPI document is at
[`/openapi.json`](https://agentwares-agentcheck.vercel.app/openapi.json).

## Authentication

`Authorization: Bearer ak_live_…` on both the MCP endpoint and the REST API.

An agent can get a key with no human in the loop — no email confirmation, no CAPTCHA, no card:

```sh
curl -X POST https://agentwares-agentcheck.vercel.app/api/v1/signup \
  -H 'content-type: application/json' -d '{"accept_terms": true}'
```

A person can create one at `/dashboard/keys` after signing in with GitHub. Check a key with
`GET /api/v1/whoami`, which answers `401 {"code":"UNAUTHORIZED"}` if it is wrong. Automated clients
are explicitly permitted by the [terms](https://agentwares-agentcheck.vercel.app/terms).

`agentcheck_get_status` and `agentcheck_get_pricing` need no key, so a client can read the service
before it has one.

## First call

No key needed — this reads the public demo target agentcheck keeps on itself.

```sh
curl -s -X POST https://agentwares-agentcheck.vercel.app/api/mcp \
  -H 'content-type: application/json' \
  -H 'accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"agentcheck_get_status","arguments":{"owner":"demo","slug":"demo"}}}'
```

## MCP client configuration

Claude Desktop, Cursor, Claude Code and anything else that speaks Streamable HTTP:

```json
{
  "mcpServers": {
    "agentcheck": {
      "url": "https://agentwares-agentcheck.vercel.app/api/mcp",
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

| Endpoint                         | What                                                     |
| -------------------------------- | -------------------------------------------------------- |
| `POST /api/mcp`                  | the MCP server (Streamable HTTP)                         |
| `GET /openapi.json`              | the REST mirror of every tool, under /api/v1             |
| `POST /api/v1/signup`            | account + api_key from {"accept_terms": true}, no human  |
| `GET /api/v1/whoami`             | check a key                                              |
| `GET /drift.json`                | public model-drift index, no login                       |
| `GET /<owner>/<slug>`            | public status page for a target                          |
| `GET /badge/<owner>/<slug>.svg`  | status badge (Cache-Control max-age=300)                 |
| `GET /<owner>/<slug>/trust.json` | uptime, last nightly, adversarial pass rate, models seen |
| `GET /llms.txt`                  | machine docs                                             |
| `GET /pricing.json`              | machine-readable pricing                                 |
| `GET /api/health`                | health                                                   |

## Pricing

Free: one target, hourly checks, badge, status page and `trust.json`. Paid tiers are a monthly
subscription per target and add nightly regression replay with cause grouping, re-runs when a model
changes, the adversarial pack and Slack/Discord alerts. Exact numbers, tier ids and Stripe lookup
keys are in [`pricing.json`](pricing.json).

## Source and issues

This is a hosted service; its implementation is not in this repository (see the
[root README](../README.md)). **Open an issue here** for anything about the running service — a wrong
answer, a dead endpoint, a tool description that misled your agent, a capability you need.

The files in this directory are generated from the service, so please report a problem rather than
sending a documentation patch.
