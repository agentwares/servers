# agentguard cloud — tool reference

6 tools on `https://agentwares-agentguard.vercel.app/api/mcp`, generated from a live `tools/list`
call against the deployed server. Errors are JSON objects carrying `code`, `cause`, `fix` and
`retryable`, so a client can decide whether to retry without parsing prose.

See [README.md](README.md) for authentication and a call you can paste into a terminal.

## `agentguard_get_pricing`

Machine-readable pricing for agentguard hosted: bands (Starter/Pro/Team) with included tool calls per month, the 80% soft alert and 150% hard stop, features, the free trial allowance, and `per_call` — buying tool calls outright at POST /api/v1/calls from a prepaid balance, which is the one purchase here an unattended agent can complete. Same data as /pricing.json.

_Takes no arguments._

Annotations: `readOnlyHint`, `idempotentHint`

## `agentguard_create_proxy`

Create a hosted agentguard proxy in front of an MCP server. Returns the proxy id, the per-customer MCP URL and the proxy key (shown once). Point your agent's MCP client at the URL with `Authorization: Bearer <key>`. Requires an account key.

| Parameter      | Type                       | Required | Description                                                                                                                |
| -------------- | -------------------------- | -------- | -------------------------------------------------------------------------------------------------------------------------- |
| `slug`         | string                     | yes      | short name, a-z 0-9 -                                                                                                      |
| `upstreamUrl`  | string                     | yes      | Streamable HTTP URL of the MCP server to protect                                                                           |
| `upstreamName` | string                     | no       | name used to namespace tools when you add more upstreams later                                                             |
| `mode`         | `"dry-run"` \| `"enforce"` | no       | dry-run records what the policy would have blocked and lets every call through; enforce actually blocks. Start in dry-run. |

## `agentguard_set_policy`

Replace a proxy's policy (YAML: mode, upstreams, classify, caps, loop, dry_run, approvals, alerts, chaos, drift). Validated before saving; takes effect within 30 seconds. Requires an account key.

| Parameter    | Type   | Required | Description                                                                   |
| ------------ | ------ | -------- | ----------------------------------------------------------------------------- |
| `proxyId`    | string | yes      | the proxy id returned by agentguard_create_proxy                              |
| `policyYaml` | string | yes      | the complete policy document; it replaces the current one rather than merging |

Annotations: `idempotentHint`

## `agentguard_get_run`

The incident-shaped report for one run: counts (calls, reads, writes, faked, blocked, spend), the call timeline with decisions, dry-run mutations (what would have changed), ledger entries and the audit-chain verification. Requires an account key.

| Parameter | Type   | Required | Description                                                               |
| --------- | ------ | -------- | ------------------------------------------------------------------------- |
| `proxyId` | string | yes      | the proxy id returned by agentguard_create_proxy                          |
| `runId`   | string | yes      | the X-Run-Id your agent sent with the calls; one run is one agent session |

Annotations: `readOnlyHint`, `idempotentHint`

## `agentguard_export_audit`

Export a run's hash-chained audit entries (prev_hash, hash, redacted args) with the Merkle root, for disputes and questionnaires. Verify offline with sha256(prev_hash + canonical(entry)). Requires an account key.

| Parameter | Type   | Required | Description                                                               |
| --------- | ------ | -------- | ------------------------------------------------------------------------- |
| `proxyId` | string | yes      | the proxy id returned by agentguard_create_proxy                          |
| `runId`   | string | yes      | the X-Run-Id your agent sent with the calls; one run is one agent session |

Annotations: `readOnlyHint`, `idempotentHint`

## `agentguard_recommend_allowlist`

Least-privilege recommendation from the proxy's recorded calls: the minimal tool allowlist, argument keys used per tool, and the upstream tools never used (remove them first). No LLM call here; apply it in the dashboard. Requires an account key.

| Parameter | Type   | Required | Description                                      |
| --------- | ------ | -------- | ------------------------------------------------ |
| `proxyId` | string | yes      | the proxy id returned by agentguard_create_proxy |

Annotations: `readOnlyHint`, `idempotentHint`
