---
name: farmdash-guarded-futures
description: >-
  Run FarmDash's guarded Hyperliquid perpetual-futures workflow — funding scan, market
  conditions, account state, strategy analysis, position sizing — and place or cancel an
  EIP-712 pre-signed order only with explicit human approval.
api: FarmDash Agent Hub API
base_url: https://www.farmdash.one/api
operations:
  - scanFundingRates
  - getMarketConditions
  - getAccountState
  - analyzeStrategy
  - calculatePositionSize
  - executeOrder
  - cancelOrder
generated: '2026-08-26'
method: generated
source: openapi/farmdash-agent-api-openapi.yaml
---

# FarmDash: guarded Hyperliquid futures

> The spec's own `info.description` opens with this warning, in these words: running trade
> executions and cancellations **places real perpetual futures trades and alters active market
> exposure, carrying significant risk of financial loss.** Immediate, explicit manual end-user
> confirmation is strictly required immediately before any execution or cancellation. Do not
> allow autonomous agents to auto-run these.

Steps 1–5 are research and are safe to run autonomously. Steps 6–7 are not.

## Published risk bounds

From FarmDash's own agent card (`/.well-known/agent.json`):

- max leverage **5x**
- max risk per trade **2%**
- daily loss limit **-3%**
- circuit breaker **-15%**
- dead-man's switch: **60s auto-cancel on agent disconnect**

## Steps

### 1. `scanFundingRates` (GET /v1/agent/futures/scan-funding)

Returns `arbOpportunities[]` and `highFunding[]`. Handle `502` (Hyperliquid funding provider
degradation) — check `warnings[]` for Dust Storm.

### 2. `getMarketConditions` (GET /v1/agent/futures/market-conditions)

Requires `coin`. Technical indicators. `502` on candle-provider degradation.

### 3. `getAccountState` (GET /v1/agent/futures/account-state)

Requires `agentAddress`. Returns `positions[]` and risk state. Read this *before* sizing —
existing exposure is part of the risk calculation.

### 4. `analyzeStrategy` (POST /v1/agent/futures/analyze-strategy)

Pioneer+ (bearer required, no anonymous fallback). Full research pipeline with an adaptive
recommendation.

**This is a gate, not just research: it must have run within 5 minutes before any execution.**

### 5. `calculatePositionSize` (POST /v1/agent/futures/position-sizing)

Guardrail-enforced sizing. Do not compute size yourself and skip this.

### 6. Get explicit human approval

Present: the instrument, the direction, the size, the leverage, the liquidation context from
step 3, and the fact that this places a real trade. Wait for an unambiguous confirmation.
Silence, inference and prior blanket permission are not approval.

### 7. `executeOrder` (POST /v1/agent/futures/execute-order)

Submits a **pre-signed** EIP-712 order. Requires `agentAddress` and `intentHash`. FarmDash does
not sign for the user.

Statuses: `400` invalid request, `402` payment/tier required.

## Reversal — `cancelOrder` (POST /v1/agent/futures/cancel-order)

Cancels **open orders only**, keyed by `intentHash`. A filled order **cannot be cancelled** —
it can only be offset by a new opposing trade at market, which is a new position with its own
cost, not an undo. FarmDash publishes no time window because the boundary is fill state, not
elapsed time.

Cancellation is itself an exposure-altering action and carries the same human-approval
requirement.

## Tier note

Futures research is Pioneer; execution is Syndicate. The live status contract reports
`futures.hyperliquid_guarded` as `available_with_tier_gate` —
`pioneer_research_syndicate_execution`.
