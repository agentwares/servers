# Stablecoin reserve attestations — tool reference

2 tools on `https://agentwares-stablecoin.vercel.app/mcp`, generated from a live `tools/list`
call against the deployed server. Errors are JSON objects carrying `code`, `cause`, `fix` and
`retryable`, so a client can decide whether to retry without parsing prose.

See [README.md](README.md) for authentication and a call you can paste into a terminal.

## `stablecoin_reserves`

Latest parsed reserve attestation for one stablecoin issuer as JSON: as_of date, total reserves, composition by asset category, attestor, source_url, current on-chain supply (DefiLlama) and the reserve ratio. Use it when an agent needs machine-readable backing data instead of reading the issuer's PDF. Pass symbol (USDC, USDT, ...) or issuer; period=YYYY-MM selects an earlier month. Issuers without a third-party attestation return supply only with confidence 'none'. Paid: $0.02 per call; without credit you get a PAYMENT_REQUIRED result. Set sample=true for a free example response.

| Parameter | Type    | Required | Description                                                                                 |
| --------- | ------- | -------- | ------------------------------------------------------------------------------------------- |
| `symbol`  | string  | no       | Token symbol, case-insensitive: USDC, USDT, PYUSD, RLUSD, ... (see stablecoin_list_issuers) |
| `issuer`  | string  | no       | Issuer name or registry id when the symbol is unknown, e.g. "circle" or "paxos"             |
| `period`  | string  | no       | Report month as YYYY-MM. Default: the newest parsed month. See history_available.           |
| `sample`  | boolean | no       | Set true to return an example response at no charge (sample mode). Default false.           |

Annotations: `readOnlyHint`, `idempotentHint`, `openWorldHint`

## `stablecoin_list_issuers`

Free. Lists every issuer in the registry with symbol, issuer, attestation type, attestor, cadence, source_url and which months are parsed. Call it first to pick a symbol for stablecoin_reserves.

_Takes no arguments._

Annotations: `readOnlyHint`, `idempotentHint`
