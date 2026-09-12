# Readiness scorer

Give it a signup URL you own; a headless browser agent tries to sign up the way an AI agent would, and publishes a 0–100 score, the evidence behind every point, a fix list and a badge. Free.

|                       |                                                                                    |
| --------------------- | ---------------------------------------------------------------------------------- |
| **Service**           | https://agentwares-readiness.vercel.app                                            |
| **MCP endpoint**      | `https://agentwares-readiness.vercel.app/api/mcp` (Streamable HTTP)                |
| **Registry name**     | `io.github.agentwares/readiness-scorer` (v0.1.2)                                   |
| **Tools**             | `readiness_get_verification_token`, `readiness_request_scan`, `readiness_get_scan` |
| **In this directory** | [`server.json`](server.json) · [`llms.txt`](llms.txt) · [tool reference](API.md)   |

The rubric is 100 points over seven items: a signup form an agent can fill, no CAPTCHA or bot wall, a
signup that completes without a human, terms acceptable inside the flow, terms that allow automated
accounts, API-key minting, and docs an agent can act on. Items the probe could not observe score 0
and are listed as "not observable" — never guessed. The full wording is at
[`/rubric`](https://agentwares-readiness.vercel.app/rubric); results are public pages at `/r/<id>` with the raw evidence at
`/r/<id>/evidence.json`.

Worth knowing before you call it:

- **Verified domains only.** The first call returns a token to publish as a DNS `TXT` record, a
  `<meta>` tag or a `/.well-known` file. Nothing is stored until the host verifies, so you cannot
  scan somebody else's site.
- **No CAPTCHA solving, ever.** The probe detects reCAPTCHA / hCaptcha / Turnstile / Arkose, records
  the vendor, and stops.
- **Rate limits:** 3 scans per host per day, 10 per client per hour, 30 per hour overall.
- The probe identifies itself as `AgentwaresReadinessProbe/0.1` and signs up with an
  `@example.com` address.

## Authentication

None, and there is no API key. The gate is domain ownership: call
`readiness_get_verification_token` (or `GET /api/token?url=…`), publish the token it returns, then
call `readiness_request_scan`. An unverified host answers `403 DOMAIN_NOT_VERIFIED` with the exact
record to publish in the error's `verification` block.

## First call

Free, no key: the verification token and the three ways to publish it.

```sh
curl -s "https://agentwares-readiness.vercel.app/api/token?url=https://example.com/signup"
```

## MCP client configuration

Claude Desktop, Cursor, Claude Code and anything else that speaks Streamable HTTP:

```json
{
  "mcpServers": {
    "readiness-scorer": {
      "url": "https://agentwares-readiness.vercel.app/api/mcp"
    }
  }
}
```

## Endpoints

| Endpoint              | What                                                            |
| --------------------- | --------------------------------------------------------------- |
| `POST /api/mcp`       | the MCP server (Streamable HTTP), no key                        |
| `GET /api/token?url=` | stateless verification token + instructions                     |
| `POST /api/scans`     | verify, rate-limit, queue a scan; 202 or 400/403/429 with a fix |
| `GET /api/scans/<id>` | scan status / result JSON                                       |
| `GET /r/<id>`         | public results page (+ /evidence.json)                          |
| `GET /badge/<id>.svg` | score badge (also /badge/host/<host>.svg)                       |
| `GET /rubric`         | the scoring rubric in full                                      |
| `GET /llms.txt`       | machine docs                                                    |
| `GET /api/health`     | health                                                          |

## Pricing

Free, with the rate limits above. This service has no `pricing.json` because it does not charge.

## Source and issues

This is a hosted service; its implementation is not in this repository (see the
[root README](../README.md)). **Open an issue here** for anything about the running service — a wrong
answer, a dead endpoint, a tool description that misled your agent, a capability you need.

The files in this directory are generated from the service, so please report a problem rather than
sending a documentation patch.
