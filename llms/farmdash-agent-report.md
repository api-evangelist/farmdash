# FarmDash Agent Surface: Public Truth and Hostile Audit

> Evidence-based integration reference for agents, evaluators, and developers. This document classifies current behavior; it is not a roadmap or a marketing architecture diagram.

- Generated: 2026-08-26 (America/Los_Angeles)
- Runtime authority: [live status contract](https://www.farmdash.one/api/v1/agent/status)
- API contract: [OpenAPI](https://www.farmdash.one/agents/openapi.yaml)
- MCP discovery: [MCP manifest](https://www.farmdash.one/.well-known/mcp.json)
- OpenClaw skills: [Agent Hub](https://www.farmdash.one/agents)
- Build provenance: production resolves `VERCEL_GIT_COMMIT_SHA`; local generation represents the current working tree.

## Classification Rules

- **LIVE**: deployable behavior exists and its public route is intended to return an authoritative result or a typed degraded state.
- **PREVIEW / PREPARATION ONLY**: research, configuration, intent creation, or simulation exists, but it is not authority or proof of execution.
- **DEPLOYMENT-PREREQUISITE DEPENDENT**: implementation exists but the live status contract must confirm required infrastructure before an agent may use it.
- **REMOVE AS UNSUPPORTED**: the historical claim is not supported by the deployable public contract and must not be repeated.

## Hostile Claim Audit

| Claim | Classification | Current evidence and boundary |
|---|---|---|
| Trail Heat protocol ranking | **LIVE (CANONICAL) / EDITORIAL (CATALOG)** | Canonical quantitative Trail Heat is DeFiLlama-backed at /api/v1/trail-heat with per-number evidence states. Catalog surfaces (/api/v1/agent/protocols) expose discovery_heuristic_score — an editorial ranking aid explicitly NOT Trail Heat. Mock mode is deterministic and explicitly non-live. |
| Dynamically changing Trail Heat component weights | **REMOVE AS UNSUPPORTED** | Trail Heat uses documented scoring paths; do not claim market-regime weight learning. |
| Decision confidence and `recordOutcome` self-calibration | **PREVIEW / PREPARATION ONLY** | A library and unit-tested feedback function exist, but they are not a public autonomous-execution oracle and must not be described as mathematical certainty or hallucination eradication. |
| Coinbase CDP MPC wallet provisioning | **DEPLOYMENT-PREREQUISITE DEPENDENT** | Requires a wallet-bound tenant key, fixed server-side destination allowlist, CDP credentials, and live status readiness. FarmDash does not accept a customer raw private key. |
| Biconomy/MEE cross-chain intents | **PREVIEW / PREPARATION ONLY** | Intent creation and declared-input economics preview exist. Submission and settlement are disabled until a production coordinator and authoritative cross-chain receipt verifier exist. No timer-generated confirmation is accepted. |
| MCP server | **LIVE** | Source distribution builds to 84 unique tools. Every public tool input schema is synchronized from the built runtime `tools/list` contract. The public manifest declares zero MCP resources; HTTPS discovery documents are not MCP runtime resources. |
| Published npm/PyPI quick install | **REMOVE AS UNSUPPORTED** | Registry lookups returned not-found on 2026-08-23. Use the documented source build until publication and clean-registry installation are independently verified. |
| OpenClaw source skill coverage | **LIVE** | 10 repository skill contracts own the full 84-tool MCP union and link to the FarmDash website, Agent Hub, canonical manual, status, OpenAPI, MCP, fees, and security pages. |
| ClawHub publication | **LIVE** | The Parm ClawHub publisher profile listed 6 published skills when externally verified on 2026-08-23. Four additional repository contracts remain source-only and must not be described as published until their ClawHub pages exist. |
| Compatibility swap execution on Ethereum, Optimism, Polygon, Base, Arbitrum, Linea | **LIVE** | Enabled EVM routes require quote, authoritative simulation, customer-controlled EIP-191 signing, customer submission, and receipt verification. Availability is narrower than discovery coverage. |
| Solana compatibility swap execution | **PREVIEW / PREPARATION ONLY** | Discovery and receipt-related support do not establish authoritative Solana quote simulation or signing through the compatibility API. |
| Generic FarmDashIntent execution | **PREVIEW / PREPARATION ONLY** | Durable lifecycle creation, policy, simulation, approval, and preparation exist. Generic execution is disabled with `execution_verifier_unconfigured`. |
| Autonomous ERC-4337 submission | **PREVIEW / PREPARATION ONLY** | Session, grant, cycle, pause, resume, and status controls exist; server-side UserOperation construction/signature/submission remains disabled. |
| Virtuals ACP paid tender workflow | **DEPLOYMENT-PREREQUISITE DEPENDENT** | The readiness service must confirm evaluator identity, registry, signer, Base RPC, worker, migration, and customer-owned ACP client prerequisites. |
| Durable webhook delivery | **DEPLOYMENT-PREREQUISITE DEPENDENT** | Requires migration 028, `WEBHOOK_ENCRYPTION_KEY`, `CRON_SECRET`, and a scheduled delivery worker. |
| Fee-settlement receipt verification | **LIVE** | The confirmation route verifies the canonical-chain receipt and exact server-committed fee fields. A generic lifecycle receipt is not automatically proof of fill, finality, or realized P&L. |
| Universal private submission / no front-running | **REMOVE AS UNSUPPORTED** | Route-specific MEV controls may exist, but the execution capability registry marks private submission unsupported. |

## Commercial Contract

Values below are generated from the runtime commercial source, not copied from campaign prose.

| Tier or fee | Runtime default |
|---|---:|
| Scout | Free; 5 requests per 24 hours per IP |
| Pioneer | $39.99/month USDC; 1,500 requests/day |
| Syndicate | $199/month USDC; 50,000 requests/day |
| Default swap fee | 45 bps |
| Mid-volume swap fee | 35 bps at $10,000+ cumulative volume |
| High-volume swap fee | 25 bps at $100,000+ cumulative volume |
| Default x402 overage | $0.01 USDC; route-specific prices can differ and are returned by the route contract |

Pioneer browser CORS remains origin-restricted. Server-side agents, CLI clients, and MCP processes are not browser CORS clients. Unrestricted browser CORS remains a Syndicate entitlement.

## First-Run Agent Procedure

1. Read [`/api/v1/agent/status`](https://www.farmdash.one/api/v1/agent/status).
2. Make one keyless Scout call to [`/api/v1/agent/protocols`](https://www.farmdash.one/api/v1/agent/protocols), or use its explicitly non-live `?mock=true` fixture for deterministic development.
3. Use [OpenAPI](https://www.farmdash.one/agents/openapi.yaml) for REST schemas or build the [MCP source distribution](https://github.com/Parmasanandgarlic/farmdashbeta/tree/main/mcp-server) and enumerate its runtime tools.
4. When paid reads are needed, follow `GET /api/v1/agent/api-key` and issue a wallet-bound key with the documented paying-wallet signature.
5. Make the first authenticated read with that Bearer key; the key wallet binding must match wallet-scoped requests.
6. On HTTP 402, inspect the response-specific amount, network, token, pay-to address, reason, and retry instructions. A typed 402 is sufficient for integration testing; do not move value.

Treat `feature_not_ready`, preparation-only, stale, and low-confidence states as reasons to narrow scope—not reasons to invent missing data. Never treat configuration, a session token, an intent, a simulation, a submission hash, or a lifecycle receipt as stronger authority than its exact contract states.

## Source Distribution

`@farmdash/mcp-server`, `@farmdash/agent-kit`, and `farmdash-agent` were not available from the public npm/PyPI registries when verified on 2026-08-23. The supported path is a source checkout:

```bash
git clone https://github.com/Parmasanandgarlic/farmdashbeta.git
cd farmdashbeta
npm ci
npm run build --prefix agent-kit
npm run build --prefix mcp-server
node mcp-server/dist/index.js
```

The repository package harness packs both Node packages, installs the tarballs into a clean temporary project, imports `FarmDashClient`, starts the MCP server, and verifies 84 unique tools.

## Verification Commands

```bash
npm run typecheck
npm run test:repository
npm run test:packages
npm run test -- --run tests/openclawDiscoveryMetadata.test.ts tests/openclawSkillDocs.test.ts tests/openclawSkillCoverage.test.ts
npm run build
FARMDASH_BASE_URL=https://<preview-host> npm run smoke:agent-surface
```

Production truth is the deployed response from the status endpoint plus the versioned public contracts above. If those disagree, agents must fail closed and report the disagreement.
