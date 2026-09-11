# agentwares servers

The hosted MCP servers behind [agentwares](https://agentwares.vercel.app). One directory per service:
what it does, the endpoint, how to authenticate, a call you can paste into a terminal, a client
config, and a reference for every tool it serves.

| Service                                                     | What it does                                                                                                                                                                                                                                                             | MCP endpoint                                       |
| ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------- |
| [agentcheck](agentcheck/)                                   | Drift and regression monitoring for production AI agents and MCP servers. Uptime-style checks, a public status page, a badge and `trust.json` are free; paid tiers re-run your suite when a model changes, replay production traces nightly and run an adversarial pack. | `https://agentwares-agentcheck.vercel.app/api/mcp` |
| [agentguard cloud](agentguard-cloud/)                       | The hosted control plane for agentguard, the MCP policy proxy: spend caps, approvals for destructive tools, a kill switch, dry-run diffs, a loop breaker and a hash-chained audit log.                                                                                   | `https://agentwares-agentguard.vercel.app/api/mcp` |
| [Real estate deal memo](deal-memo/)                         | One US street address in; owner record, automated value estimate, rent estimate, comps, cap rate, cash-on-cash, DSCR, red flags and a one-paragraph memo out.                                                                                                            | `https://agentwares-deal-memo.vercel.app/api/mcp`  |
| [Readiness scorer](readiness-scorer/)                       | Give it a signup URL you own; a headless browser agent tries to sign up the way an AI agent would, and publishes a 0–100 score, the evidence behind every point, a fix list and a badge. Free.                                                                           | `https://agentwares-readiness.vercel.app/api/mcp`  |
| [Agent access index](agent-access-index/)                   | Ask whether an honestly identified agent may fetch a URL, and why: the verdict, the exact robots.txt line that decided it, what each named AI crawler is told, and the payment rail when access is for sale. Free, no key.                                               | `https://agentwares-access-index.vercel.app/mcp`   |
| [Stablecoin reserve attestations](stablecoin-attestations/) | Stablecoin issuer reserve attestations as JSON: total reserves, composition by asset category, attestor, on-chain supply and the reserve ratio.                                                                                                                          | `https://agentwares-stablecoin.vercel.app/mcp`     |

## These are hosted services

There is no product source in this repository. Each directory documents a service we run, and each
`server.json` here is the entry published to the
[official MCP registry](https://registry.modelcontextprotocol.io) under `io.github.agentwares/*`.
The workflow in [`.github/workflows/publish-mcp-registry.yml`](.github/workflows/publish-mcp-registry.yml)
publishes them from this repository with GitHub OIDC — no tokens — which is why every listing's
`repository` link points here.

The implementations live in a private monorepo, because that repository also carries the commercial
material for the portfolio. What is open source is open source in full, with its own repo, its own
issues and a normal `git clone && pnpm install && pnpm test`:

| Repository                                                              | What                                                                                                              |
| ----------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| [agentwares/agentguard](https://github.com/agentwares/agentguard)       | the MCP policy proxy: engine, SDK, CLI and the permission-diff GitHub Action                                      |
| [agentwares/overnight-kit](https://github.com/agentwares/overnight-kit) | run a sequence of Claude Code prompts against a repo while you sleep                                              |
| [agentwares/agentsmd-lint](https://github.com/agentwares/agentsmd-lint) | lint AGENTS.md / CLAUDE.md / .cursorrules against the repo they describe                                          |
| [agentwares/libs](https://github.com/agentwares/libs)                   | the shared libraries: `@agentwares/mcp-kit`, `@agentwares/notify`, `@agentwares/x402`, `@agentwares/web-bot-auth` |

## Issues

Open an issue **in this repository** for anything about a hosted service: a wrong answer, a dead
endpoint, a tool description that misled your agent, a rate limit that is in your way, a capability
you need. Bugs in the open-source clients belong in their own repositories above.

Every file here is generated from the monorepo and from the live services, so a documentation pull
request would be overwritten on the next sync — please open an issue instead and it will be fixed at
the source.

## Machine-readable

Each service serves its own `llms.txt` and, where it charges, `pricing.json`. The copies in this
repository are snapshots from the last sync; **the live URL is authoritative**.

| Service                                                     | llms.txt                                            | pricing.json                                          |
| ----------------------------------------------------------- | --------------------------------------------------- | ----------------------------------------------------- |
| [agentcheck](agentcheck/)                                   | https://agentwares-agentcheck.vercel.app/llms.txt   | https://agentwares-agentcheck.vercel.app/pricing.json |
| [agentguard cloud](agentguard-cloud/)                       | https://agentwares-agentguard.vercel.app/llms.txt   | https://agentwares-agentguard.vercel.app/pricing.json |
| [Real estate deal memo](deal-memo/)                         | https://agentwares-deal-memo.vercel.app/llms.txt    | https://agentwares-deal-memo.vercel.app/pricing.json  |
| [Readiness scorer](readiness-scorer/)                       | https://agentwares-readiness.vercel.app/llms.txt    | — (free)                                              |
| [Agent access index](agent-access-index/)                   | https://agentwares-access-index.vercel.app/llms.txt | — (free)                                              |
| [Stablecoin reserve attestations](stablecoin-attestations/) | https://agentwares-stablecoin.vercel.app/llms.txt   | https://agentwares-stablecoin.vercel.app/pricing.json |

MIT © agentwares contributors. This repository is documentation, and the licence covers it.
