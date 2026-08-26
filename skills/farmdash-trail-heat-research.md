---
name: farmdash-trail-heat-research
description: >-
  Research DeFi airdrop-farming opportunities with FarmDash Trail Heat, distinguishing the
  canonical DeFiLlama-backed quantitative score from the editorial catalog heuristic, and
  respecting per-number evidence states.
api: FarmDash Agent Hub API
base_url: https://www.farmdash.one/api
operations:
  - getLiveTrailHeat
  - getProtocolCatalog
  - getChainBreakdown
  - getTokenPrices
  - getAgentStatus
generated: '2026-08-26'
method: generated
source: openapi/farmdash-agent-api-openapi.yaml
---

# FarmDash: Trail Heat research

Read-only. Safe for autonomous use. The one thing you can get badly wrong here is citing the
wrong score.

## The two scores are not the same number

| Operation | Field | What it is |
|---|---|---|
| `getLiveTrailHeat` (GET /v1/trail-heat) | Trail Heat | **Canonical.** DeFiLlama-backed, quantitative, carries per-number `data_evidence` states. |
| `getProtocolCatalog` (GET /v1/agent/protocols) | `discovery_heuristic_score` | **Editorial ranking aid. Explicitly NOT Trail Heat.** |

FarmDash states this distinction in its own `llms.txt` and `agent-report.md`. Never present
`discovery_heuristic_score` as Trail Heat.

## Steps

### 1. Check the runtime authority — `getAgentStatus` (GET /v1/agent/status)

Anonymous, no key. The provider's rule: *"Tool discovery is not enablement. Read status first
and preserve typed feature_not_ready responses."* It reports whether each feature is
`available`, `paid_ready`, `preparation_only` or `deployment_prerequisites_required`.

### 2. Rehearse for free

Both research reads support a deterministic sandbox:
`?mock=true`, `X-FarmDash-Mock: true`, or `Authorization: Bearer fd_sandbox_mock`.
These are **the only two endpoints in the whole API with mock support**. Use them while
building; the data is not live.

### 3. Pull scores — `getLiveTrailHeat`

Scoring is a documented blend: calibrated TVL (44.4%), 7-day observed TVL momentum (27.8%),
chain diversification, category baseline comparison, and a FarmDash evidence prior. The
provider labels its own validation status `heuristic_not_statistically_validated`.

### 4. Respect evidence states

Every canonical number carries a `data_evidence` state distinguishing **observed** (measured
from the upstream provider at fetch time) from computed, curated, aged-out or missing.
`data_quality.confidence` is one of `high | medium | low | demo`.

**Lower your recommendation confidence, or ask the user, when confidence is `low` or `demo`.**

### 5. Context — `getChainBreakdown`, `getTokenPrices`

`getTokenPrices` accepts `symbols` or `tokens`. Handle `502` (upstream price degradation) and
`503` (price provider not configured).

## Dust Storm

On upstream failure FarmDash returns `ok:false` with `warnings[]` containing
`{kind: "dust_storm"}`, or explicitly-marked stale data with its age. The provider's
invariant: *provider failure is never converted into authoritative zero financial data.*
Honour it — a Dust Storm is not a zero balance, a zero price or a zero score.

## What to tell the user

Trail Heat is a decision-support heuristic derived from public chain data. It is not a
guaranteed yield, allocation or airdrop outcome. Airdrops are speculative.
