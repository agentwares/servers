# Agent access index

Ask whether an honestly identified agent may fetch a URL, and why: the verdict, the exact robots.txt line that decided it, what each named AI crawler is told, and the payment rail when access is for sale. Free, no key.

|                       |                                                                                  |
| --------------------- | -------------------------------------------------------------------------------- |
| **Service**           | https://agentwares-access-index.vercel.app                                       |
| **MCP endpoint**      | `https://agentwares-access-index.vercel.app/mcp` (Streamable HTTP)               |
| **Registry name**     | `io.github.agentwares/agent-access-index` (v0.1.0)                               |
| **Tools**             | `access_check_url`, `access_explain_verdicts`, `access_list_agents`              |
| **In this directory** | [`server.json`](server.json) · [`llms.txt`](llms.txt) · [tool reference](API.md) |

Verdicts are `allowed`, `allowed_if_identified`, `charged`, `blocked` and `unknown`, each with
reason codes and the rule behind them. `allowed_if_identified` is the useful one: the site's own
policy permits the fetch and bot management refused it anyway, which is a refusal with a documented
way out rather than a decision the publisher made about you. Across 48 hosts harvested from real
crawler-failure issues, 30.8% were that case.

It reads robots.txt per RFC 9309 for a generic agent and for the 19 AI user-agents their operators
document, plus `Content-Signal`, `Content-Usage`, RSL licences, `llms.txt`, the bot-management
vendor from response headers, and the payment rails.

What it will not do, ever:

- **No impersonation.** It never sends another operator's crawler token to see how a site treats
  it. The per-agent column comes from what the origin _declared_, because spoofing measures what an
  edge does with a liar.
- **No CAPTCHA solving, no proxies, no retry with a different face.** One documented user-agent.
- **It obeys its own robots.txt rule** and reports the refusal rather than working around it.
- An origin that will not serve its own robots.txt answers `unknown` with `ROBOTS_UNREADABLE`,
  never a guess about a policy it could not read.

No LLM is called, so a lookup costs nothing to serve and nothing to buy.

## Authentication

None. No key, no signup, no payment, no rate-limit tier to buy.

## First call

Free: the verdict for any public URL.

```sh
curl -s "https://agentwares-access-index.vercel.app/policy?url=https://example.com/article"
```

## MCP client configuration

Claude Desktop, Cursor, Claude Code and anything else that speaks Streamable HTTP:

```json
{
  "mcpServers": {
    "agent-access-index": {
      "url": "https://agentwares-access-index.vercel.app/mcp"
    }
  }
}
```

## Endpoints

| Endpoint           | What                                                    |
| ------------------ | ------------------------------------------------------- |
| `POST /mcp`        | the MCP server (Streamable HTTP), no key                |
| `GET /policy?url=` | verdict, reasons, per-agent terms and rails for one URL |
| `GET /llms.txt`    | machine docs                                            |
| `GET /server.json` | registry server card                                    |
| `GET /health`      | health                                                  |

## Pricing

Free. This service has no `pricing.json` because it does not charge and has no paid tier to advertise.

## Source and issues

This is a hosted service; its implementation is not in this repository (see the
[root README](../README.md)). **Open an issue here** for anything about the running service — a wrong
answer, a dead endpoint, a tool description that misled your agent, a capability you need.

The files in this directory are generated from the service, so please report a problem rather than
sending a documentation patch.
