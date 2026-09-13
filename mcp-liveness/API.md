# MCP liveness — tool reference

3 tools on `https://agentwares-mcp-liveness.vercel.app/mcp`, generated from a live `tools/list`
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

## `mcp_liveness_drift`

Given a server's exact name in the official MCP registry, report which of its tools were added, removed, or had their description, input schema or output schema change between the month you name and the latest monthly snapshot. Call this when a server worked before and is behaving differently now, when a prompt or an integration written against its tools has started failing, or before trusting a tool description you cached. A liveness check says it answers today; this says whether it is still the same server your code was written against. The answer names the changed tools and the kind of change. It does not diff the schemas themselves: the snapshots keep content hashes, not bodies, so `detail` hands back the release-asset location of both full months for a caller that needs the exact diff. A month with no snapshot for that server gets an error naming the months that do have one, never an empty diff that would read as `nothing changed`.

| Parameter | Type   | Required | Description                                                                                                                                                                                          |
| --------- | ------ | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `name`    | string | yes      | Exact registry name, namespaced — e.g. `io.github.owner/server`. Not a URL and not a title.                                                                                                          |
| `since`   | string | no       | The month to diff from, as `YYYY-MM`. Omit it to diff from the earliest month this server appears in, which is the stable choice — a hard-coded month goes wrong as soon as the next snapshot lands. |

Annotations: `readOnlyHint`, `idempotentHint`
