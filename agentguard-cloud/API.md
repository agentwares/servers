# agentguard cloud — tool reference

6 tools on `https://agentwares-agentguard.vercel.app/api/mcp`, generated from a live `tools/list`
call against the deployed server. Errors are JSON objects carrying `code`, `cause`, `fix` and
`retryable`, so a client can decide whether to retry without parsing prose.

See [README.md](README.md) for authentication and a call you can paste into a terminal.

## `agentguard_get_pricing`

Machine-readable pricing for agentguard hosted: bands (Starter/Pro/Team) with included tool calls per month, the 80% soft alert and 150% hard stop, features and the free trial allowance. Same data as /pricing.json.

_Takes no arguments._

Annotations: `readOnlyHint`, `idempotentHint`

## `agentguard_create_proxy`

Create a hosted agentguard proxy in front of an MCP server. Returns the proxy id, the per-customer MCP URL and the proxy key (shown once). Point your agent's MCP client at the URL with `Authorization: Bearer <key>`. Requires an account key.

| Parameter      | Type                       | Required | Description                                                    |
| -------------- | -------------------------- | -------- | -------------------------------------------------------------- |
| `slug`         | string                     | yes      | short name, a-z 0-9 -                                          |
| `upstreamUrl`  | string                     | yes      | Streamable HTTP URL of the MCP server to protect               |
| `upstreamName` | string                     | no       | name used to namespace tools when you add more upstreams later |
| `mode`         | `"dry-run"` \| `"enforce"` | no       |                                                                |

## `agentguard_set_policy`

Replace a proxy's policy (YAML: mode, upstreams, classify, caps, loop, dry_run, approvals, alerts, chaos, drift). Validated before saving; takes effect within 30 seconds. Requires an account key.

| Parameter    | Type   | Required | Description |
| ------------ | ------ | -------- | ----------- |
| `proxyId`    | string | yes      |             |
| `policyYaml` | string | yes      |             |

Annotations: `idempotentHint`

## `agentguard_get_run`

The incident-shaped report for one run: counts (calls, reads, writes, faked, blocked, spend), the call timeline with decisions, dry-run mutations (what would have changed), ledger entries and the audit-chain verification. Requires an account key.

| Parameter | Type   | Required | Description |
| --------- | ------ | -------- | ----------- |
| `proxyId` | string | yes      |             |
| `runId`   | string | yes      |             |

Annotations: `readOnlyHint`, `idempotentHint`

## `agentguard_export_audit`

Export a run's hash-chained audit entries (prev_hash, hash, redacted args) with the Merkle root, for disputes and questionnaires. Verify offline with sha256(prev_hash + canonical(entry)). Requires an account key.

| Parameter | Type   | Required | Description |
| --------- | ------ | -------- | ----------- |
| `proxyId` | string | yes      |             |
| `runId`   | string | yes      |             |

Annotations: `readOnlyHint`, `idempotentHint`

## `agentguard_recommend_allowlist`

Least-privilege recommendation from the proxy's recorded calls: the minimal tool allowlist, argument keys used per tool, and the upstream tools never used (remove them first). No LLM call here; apply it in the dashboard. Requires an account key.

| Parameter | Type   | Required | Description |
| --------- | ------ | -------- | ----------- |
| `proxyId` | string | yes      |             |

Annotations: `readOnlyHint`, `idempotentHint`
