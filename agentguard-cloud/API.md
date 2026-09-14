# agentguard cloud — tool reference

7 tools on `https://agentwares-agentguard.vercel.app/api/mcp`, generated from a live `tools/list`
call against the deployed server. Errors are JSON objects carrying `code`, `cause`, `fix` and
`retryable`, so a client can decide whether to retry without parsing prose.

See [README.md](README.md) for authentication and a call you can paste into a terminal.

## `agentguard_get_pricing`

Machine-readable pricing for agentguard hosted: bands (Starter/Pro/Team) with included tool calls per month, the 80% soft alert and 150% hard stop, features, the free trial allowance, and `per_call` — buying tool calls outright at POST /api/v1/calls from a prepaid balance, which is the one purchase here an unattended agent can complete. Same data as /pricing.json.

_Takes no arguments._

Annotations: `readOnlyHint`, `idempotentHint`

## `agentguard_spend_report`

Read an agent log and report what it spent: totals, calls by tool and by class, estimated dollars by rail, loops, the largest single run, and — for every overspend — the agentguard band whose cap would have stopped it, the exact cap, and the call that would have tripped it. Needs no account and no key. The log is read in the request and discarded; nothing is stored. Accepts an agentwares audit export (`agentwares.audit/v1`), a Claude Code session `.jsonl`, or a CSV/JSONL of tool calls with any of `tool`, `ts`, `class`, `amount_usd`, `rail`, `run_id`, `model`, `input_tokens`, `output_tokens`. Caps come from the proxy's own default policy, so a band named here refuses exactly what it says it refuses. Buying that band is a checkout a person completes; the calls-only purchase an unattended agent can complete is in `agentguard_get_pricing` instead.

| Parameter | Type                    | Required | Description                                                                                                                                                                                                         |
| --------- | ----------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `log`     | string                  | yes      | The log itself, verbatim — JSON, JSONL or CSV. Not a path and not a URL: this server does not fetch anything.                                                                                                       |
| `detail`  | `"concise"` \| `"full"` | no       | `concise` (default) returns the totals, the top ten tools, the rails, the loops and every overspend. `full` adds the complete per-tool breakdown and the window. Ask for `full` only when you are going to read it. |

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
