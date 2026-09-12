# MCP liveness — tool reference

2 tools on `https://agentwares-mcp-liveness.vercel.app/mcp`, generated from a live `tools/list`
call against the deployed server. Errors are JSON objects carrying `code`, `cause`, `fix` and
`retryable`, so a client can decide whether to retry without parsing prose.

See [README.md](README.md) for authentication and a call you can paste into a terminal.

## `mcp_liveness_check`

Given a server's exact name in the official MCP registry, make one `initialize` call to the endpoint its listing names and report what a client with no credential gets. Outcomes: `usable` (it connected and negotiated a protocol version), `needs_credential` (401 or 403 — the answer then says whether a credential can be obtained in-band or whether a person has to create one), `charged` (402, access is for sale), `unreachable` (the listed endpoint is not an MCP server: wrong URL, dead host, or a 200 that is not JSON-RPC), `local_only` (no remote endpoint; it must be spawned as a process), and `not_listed` (no server with that exact name). Call this before spending a call on a server you found in the catalogue. Roughly two in five remote listings do not answer a stock client.

| Parameter             | Type    | Required | Description                                                                                                                                            |
| --------------------- | ------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `name`                | string  | yes      | Exact registry name, namespaced — e.g. `io.github.owner/server`. Not a URL and not a title.                                                            |
| `skipCredentialCheck` | boolean | no       | Skip the OAuth discovery chain on a 401. Faster (one request instead of up to five) but the answer then cannot say whether a credential is obtainable. |

Annotations: `readOnlyHint`, `openWorldHint`

## `mcp_liveness_explain_outcomes`

Return the six outcomes this server can report, what each means for an agent choosing a server from the registry, and what to do next for each. Makes no request and costs nothing; call it once to interpret mcp_liveness_check results.

_Takes no arguments._

Annotations: `readOnlyHint`, `idempotentHint`
