# Stablecoin reserve attestations

Stablecoin issuer reserve attestations as JSON: total reserves, composition by asset category, attestor, on-chain supply and the reserve ratio.

|                       |                                                                                                                    |
| --------------------- | ------------------------------------------------------------------------------------------------------------------ |
| **Service**           | https://agentwares-stablecoin.vercel.app                                                                           |
| **MCP endpoint**      | `https://agentwares-stablecoin.vercel.app/mcp` (Streamable HTTP)                                                   |
| **Registry name**     | `io.github.agentwares/stablecoin-attestations` (v0.1.2)                                                            |
| **npm**               | [`@agentwares/stablecoin-attestations`](https://www.npmjs.com/package/@agentwares/stablecoin-attestations) — stdio |
| **Tools**             | `stablecoin_reserves`, `stablecoin_list_issuers`                                                                   |
| **In this directory** | [`server.json`](server.json) · [`llms.txt`](llms.txt) · [`pricing.json`](pricing.json) · [tool reference](API.md)  |

Each issuer's monthly (or quarterly) reserve report is parsed once into a fixed schema, joined with
the current on-chain supply from DefiLlama, and served over MCP and plain HTTP — so an agent never
has to read a PDF. Every response carries `as_of`, `period`, `source_url`, `report_url`, the
`sources` it used, a `confidence` grade and a notice. Issuers with no third-party attestation return
supply only, with `confidence: "none"`, rather than a guess. `period=YYYY-MM` selects an earlier
month from the history the response lists.

This is the one hosted service that also ships as an npm package:
`npx @agentwares/stablecoin-attestations` serves the bundled snapshot over stdio, with no key and no
network call.

The data is informational only — not financial, investment or legal advice. Every response says so.

## Authentication

`Authorization: Bearer <api key>` for paid `stablecoin_reserves` calls.
`stablecoin_list_issuers` is free, and `sample=true` (MCP) or `?sample=1` (HTTP) returns a free
example. Without credit the HTTP endpoint answers `402 Payment Required` carrying x402 (USDC on Base)
and Stripe pay-per-call options.

## First call

Free, no key: the sample USDC attestation.

```sh
curl -s "https://agentwares-stablecoin.vercel.app/v1/reserves/USDC?sample=1"
```

## MCP client configuration

Claude Desktop, Cursor, Claude Code and anything else that speaks Streamable HTTP:

```json
{
  "mcpServers": {
    "stablecoin-attestations": {
      "url": "https://agentwares-stablecoin.vercel.app/mcp",
      "headers": {
        "Authorization": "Bearer <your api key>"
      }
    }
  }
}
```

The `headers` block is optional: this server also answers without a key — see Authentication above
for what you get.

Or over stdio, with no account and no network call:

```json
{
  "mcpServers": {
    "stablecoin-attestations": {
      "command": "npx",
      "args": ["-y", "@agentwares/stablecoin-attestations"]
    }
  }
}
```

## Endpoints

| Endpoint                    | What                                                                 |
| --------------------------- | -------------------------------------------------------------------- |
| `POST /mcp`                 | the MCP server (Streamable HTTP)                                     |
| `GET /v1/reserves/{symbol}` | one attestation — 402 without a key, ?sample=1 free, ?period=YYYY-MM |
| `GET /v1/issuers`           | the issuer registry, free                                            |
| `GET /llms.txt`             | machine docs                                                         |
| `GET /pricing.json`         | machine-readable pricing                                             |
| `GET /bazaar.json`          | x402 Bazaar discovery metadata                                       |
| `GET /health`               | health                                                               |

## Pricing

**$0.05 per `stablecoin_reserves` call.** `stablecoin_list_issuers` and `?sample=1` are free. Terms in [`pricing.json`](pricing.json).

## Source and issues

This is a hosted service; its implementation is not in this repository (see the
[root README](../README.md)). **Open an issue here** for anything about the running service — a wrong
answer, a dead endpoint, a tool description that misled your agent, a capability you need.

The files in this directory are generated from the service, so please report a problem rather than
sending a documentation patch.
