# Real estate deal memo

One US street address in; owner record, automated value estimate, rent estimate, comps, cap rate, cash-on-cash, DSCR, red flags and a one-paragraph memo out.

|                       |                                                                                                                   |
| --------------------- | ----------------------------------------------------------------------------------------------------------------- |
| **Service**           | https://agentwares-deal-memo.vercel.app                                                                           |
| **MCP endpoint**      | `https://agentwares-deal-memo.vercel.app/api/mcp` (Streamable HTTP)                                               |
| **Registry name**     | `io.github.agentwares/deal-memo` (v0.1.1)                                                                         |
| **Tools**             | `realestate_deal_memo`, `realestate_lookup`                                                                       |
| **In this directory** | [`server.json`](server.json) · [`llms.txt`](llms.txt) · [`pricing.json`](pricing.json) · [tool reference](API.md) |

Deterministic underwriting over [RentCast](https://www.rentcast.io/api) data.
`realestate_deal_memo` pulls the property record, the value estimate with its sale comps and the
long-term rent estimate with its rental comps, then computes NOI, cap rate, cash-on-cash, DSCR, GRM
and break-even occupancy from stated assumptions — plus the 70% rule, profit and ROI for a flip, and
a multi-year exit projection for a hold. Red flags come from fixed thresholds, not from a model, and
`screen.result` is `pass`, `review` or `fail`. Only the closing paragraph is written by an LLM, and
`memo_status` says whether it was generated, cached, templated or skipped.

Every response is wrapped as `{ data, sources[], as_of, confidence, notice, sample }`: which RentCast
endpoints were used and when, the oldest data point behind the answer, a data-quality score with its
reasons, and the disclaimer.

The same product is also an Apify Actor with pay-per-event pricing, and the plain HTTP endpoint
answers `402 Payment Required` with machine-readable payment options.

## Authentication

`Authorization: Bearer <api key>` for paid calls. `sample=true` (MCP) or `?sample=true` (HTTP) is
free, needs no key, and never calls a model or RentCast — it replays a fixed fixture, which makes it
a safe way to see the response shape before you pay for one.

## First call

Free, no key: the sample memo for a fixed example address.

```sh
curl -s "https://agentwares-deal-memo.vercel.app/api/v1/memo?sample=true&address=1547+Example+Ave,+Cleveland,+OH+44109"
```

## MCP client configuration

Claude Desktop, Cursor, Claude Code and anything else that speaks Streamable HTTP:

```json
{
  "mcpServers": {
    "deal-memo": {
      "url": "https://agentwares-deal-memo.vercel.app/api/mcp",
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

| Endpoint                       | What                                                     |
| ------------------------------ | -------------------------------------------------------- |
| `POST /api/mcp`                | the MCP server (Streamable HTTP)                         |
| `GET /api/v1/memo?address=…`   | full memo — $0.75, 402 without a key, ?sample=true free  |
| `GET /api/v1/lookup?address=…` | one RentCast record — $0.10, ?kind=property\|value\|rent |
| `GET /llms.txt`                | machine docs                                             |
| `GET /pricing.json`            | machine-readable pricing                                 |
| `GET /server.json`             | this registry entry, served live                         |

## Pricing

**$0.75 per memo, $0.10 per single lookup.** `sample=true` is free and needs no key. Full terms in [`pricing.json`](pricing.json).

## Source and issues

This is a hosted service; its implementation is not in this repository (see the
[root README](../README.md)). **Open an issue here** for anything about the running service — a wrong
answer, a dead endpoint, a tool description that misled your agent, a capability you need.

The files in this directory are generated from the service, so please report a problem rather than
sending a documentation patch.
