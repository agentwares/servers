# Agent access index — tool reference

3 tools on `https://agentwares-access-index.vercel.app/mcp`, generated from a live `tools/list`
call against the deployed server. Errors are JSON objects carrying `code`, `cause`, `fix` and
`retryable`, so a client can decide whether to retry without parsing prose.

See [README.md](README.md) for authentication and a call you can paste into a terminal.

## `access_check_url`

Given a URL, say whether an AI agent may fetch it, on what terms, and at what price. Reads robots.txt for a generic agent and for every AI user-agent the operators document, the Content-signal and Content-Usage directives, any RSL licence the origin links, and the live response to an honestly-identified request. Verdicts: `allowed` (it served us), `allowed_if_identified` (the written policy permits this but bot management refused an anonymous request — signing as a named crawler is the documented route), `charged` (there is a price, quoted where the rail will show one), `blocked` (the site disallows the agents that would want this, with nothing to buy), and `unknown` (the origin did not answer, or the URL is not there). Never evades: no CAPTCHA solving, no proxies, no spoofed user-agents. Refusals come back as findings with the evidence attached.

| Parameter         | Type    | Required | Description                                                                      |
| ----------------- | ------- | -------- | -------------------------------------------------------------------------------- |
| `url`             | string  | yes      | Absolute http(s) URL of the page to check, e.g. https://example.com/article      |
| `includeEvidence` | boolean | no       | Include every request made, with status and headers (default false: it is large) |
| `skipWellKnown`   | boolean | no       | Skip the five well-known probes; faster, and they are almost always absent       |

Annotations: `readOnlyHint`, `idempotentHint`, `openWorldHint`

## `access_explain_verdicts`

Return the five verdicts this index can return, what each one means for an agent that has just been refused, and what the agent's next move is for each. Costs nothing and makes no request; call it once to interpret access_check_url results.

_Takes no arguments._

Annotations: `readOnlyHint`, `idempotentHint`

## `access_list_agents`

Return the AI crawler tokens this index reads robots.txt rules for — only tokens their operators document — plus the widely-written tokens that no operator sends, which make a robots rule unenforceable. Useful when writing or auditing a robots.txt.

_Takes no arguments._

Annotations: `readOnlyHint`, `idempotentHint`
