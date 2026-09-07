# Real estate deal memo — tool reference

2 tools on `https://agentwares-deal-memo.vercel.app/api/mcp`, generated from a live `tools/list`
call against the deployed server. Errors are JSON objects carrying `code`, `cause`, `fix` and
`retryable`, so a client can decide whether to retry without parsing prose.

See [README.md](README.md) for authentication and a call you can paste into a terminal.

## `realestate_deal_memo`

Underwrite a US residential property from its street address for a rental, flip or hold strategy. Pulls the property record (owner, last sale, taxes, HOA), the automated value estimate with sale comps and the long-term rent estimate with rental comps from RentCast, then computes NOI, cap rate, cash-on-cash, DSCR, GRM and break-even occupancy from stated assumptions with deterministic math (plus 70%-rule, profit and ROI for a flip; a multi-year exit projection for a hold), lists red flags with a pass/review/fail screen, and adds a one-paragraph analyst memo. Returns {data, sources, as_of, confidence, notice}. Not financial advice: verify before acting. Paid: $0.75 per call; without credit you get a PAYMENT_REQUIRED result. Set sample=true for a free example response.

| Parameter     | Type                               | Required | Description                                                                                                                                                                                                                |
| ------------- | ---------------------------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `address`     | string                             | no       | Full US street address: number, street, city, state, zip (e.g. 1547 Example Ave, Cleveland, OH 44109). Required unless sample=true.                                                                                        |
| `strategy`    | `"rental"` \| `"flip"` \| `"hold"` | no       | rental: buy and rent out (NOI, cap rate, cash-on-cash, DSCR). flip: buy, rehab, resell (70% rule, profit, ROI). hold: rental metrics plus a multi-year exit projection (appreciation, principal paydown, equity multiple). |
| `assumptions` | object                             | no       | Underwriting assumptions. Every field is optional; omitted fields use the documented defaults.                                                                                                                             |
| `memo`        | boolean                            | no       | Include the one-paragraph memo (cheap model, cached). Default true.                                                                                                                                                        |
| `sample`      | boolean                            | no       | Set true to return an example response at no charge (sample mode). Default false.                                                                                                                                          |

Annotations: `readOnlyHint`, `idempotentHint`, `openWorldHint`

## `realestate_lookup`

Fetch one RentCast record for a US street address. kind=property: characteristics, owner, last sale, tax bill, assessment, HOA and sale history. kind=value: automated value estimate with a low/high range and the sale comps behind it. kind=rent: long-term rent estimate with a range and rental comps. One upstream call per lookup; use realestate_deal_memo when you need all three plus the underwriting. Returns {data, sources, as_of, confidence, notice}. Paid: $0.10 per call; without credit you get a PAYMENT_REQUIRED result. Set sample=true for a free example response.

| Parameter | Type                                  | Required | Description                                                                                                                                                                    |
| --------- | ------------------------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `address` | string                                | no       | Full US street address: number, street, city, state, zip (e.g. 1547 Example Ave, Cleveland, OH 44109). Required unless sample=true.                                            |
| `kind`    | `"property"` \| `"value"` \| `"rent"` | no       | property: characteristics, owner, last sale, taxes, HOA. value: automated value estimate with range and sale comps. rent: long-term rent estimate with range and rental comps. |
| `sample`  | boolean                               | no       | Set true to return an example response at no charge (sample mode). Default false.                                                                                              |

Annotations: `readOnlyHint`, `idempotentHint`, `openWorldHint`
