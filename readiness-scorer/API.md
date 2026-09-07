# Readiness scorer — tool reference

3 tools on `https://agentwares-readiness.vercel.app/api/mcp`, generated from a live `tools/list`
call against the deployed server. Errors are JSON objects carrying `code`, `cause`, `fix` and
`retryable`, so a client can decide whether to retry without parsing prose.

See [README.md](README.md) for authentication and a call you can paste into a terminal.

## `readiness_get_verification_token`

Step 1 of a readiness scan. Returns the domain-ownership token for the URL's host plus the three ways to publish it (DNS TXT record, <meta> tag, /.well-known file). The token is deterministic for the host, so calling this again returns the same value. Nothing is stored.

| Parameter | Type   | Required | Description                                                                            |
| --------- | ------ | -------- | -------------------------------------------------------------------------------------- |
| `url`     | string | yes      | Public https URL of the SaaS signup (or landing) page, e.g. https://example.com/signup |

Annotations: `readOnlyHint`, `idempotentHint`

## `readiness_request_scan`

Step 2. Verifies domain ownership (DNS TXT / meta tag / .well-known must carry the token from readiness_get_verification_token), then queues a Playwright probe that tries to sign up, accept the terms and mint an API key with no human. Returns the scan id and the public results URL. Fails with DOMAIN_NOT_VERIFIED (with instructions) or RATE_LIMITED (3 scans per host per day). Never solves CAPTCHAs.

| Parameter | Type   | Required | Description                                                                            |
| --------- | ------ | -------- | -------------------------------------------------------------------------------------- |
| `url`     | string | yes      | Public https URL of the SaaS signup (or landing) page, e.g. https://example.com/signup |

Annotations: `openWorldHint`

## `readiness_get_scan`

Step 3. Poll a scan by id. While status is queued/running only metadata is returned; when done you get the 0-100 score, grade, per-item rubric points with evidence, the fix list, and what could not be observed.

| Parameter | Type   | Required | Description                                |
| --------- | ------ | -------- | ------------------------------------------ |
| `id`      | string | yes      | scan id returned by readiness_request_scan |

Annotations: `readOnlyHint`, `idempotentHint`
