---
name: farmdash-zero-custody-swap
description: >-
  Execute a cross-chain token swap through FarmDash's zero-custody pipeline: quote, mandatory
  simulation, user-signed execution, and on-chain confirmation. FarmDash never broadcasts and
  never holds keys — the user's wallet signs and submits.
api: FarmDash Agent Hub API
base_url: https://www.farmdash.one/api
operations:
  - getSwapQuote
  - simulateSwapExecution
  - executeSwap
  - confirmSwap
  - getSwapHistory
  - analyzeRiskSentinel
generated: '2026-08-26'
method: generated
source: openapi/farmdash-agent-api-openapi.yaml
---

# FarmDash: zero-custody swap

Four API calls and one wallet signature. Every operationId below was verified against
`openapi/farmdash-agent-api-openapi.yaml`.

## Before you start

- **This is irreversible.** Once the user's wallet broadcasts, there is no cancel, refund,
  void or reverse operation in this API. All safety lives *before* the signature.
- **There is no idempotency key.** Do not retry `executeSwap` blindly on a network error —
  confirm state via `getSwapHistory` first.
- **There is no sandbox for this flow.** `?mock=true` returns a typed `mock_not_supported`
  error on quote requests. You rehearse with the real simulation against real market state.

## Steps

### 1. Quote — `getSwapQuote` (GET /agents/quote)

Anonymous-capable (Scout). Returns a route, fee context and a wallet-bound `intent_id`.
Useful query params: `protocol`, `walletAddress`, `toAddress`, `slippage`, `safetyMode`,
`healthFactor`, `liquidationBufferPct`.

Handle `502` — a typed live-quote provider degradation (Dust Storm). **Never read a degraded
response as a zero quote.** Check `warnings[]` for `{kind: "dust_storm"}`.

### 2. Optional pre-check — `analyzeRiskSentinel` (POST /v1/agent/risk-sentinel)

Returns `flags[]`. Worth calling before committing the user's attention to a route.

### 3. Simulate — `simulateSwapExecution` (POST /v1/simulate)

**Mandatory, not optional.** Consumes `intent_id`, emits `simulation_id`.

- `409` = intent and wallet mismatch. Re-quote against the correct wallet.
- The result is valid for **60 seconds**. Budget for it.

### 4. Get human approval

The spec's own `info.description` opens with a warning that autonomous agents must not
auto-run execution without manual user approval. Present the route, the fee, the slippage and
the irreversibility, and get an explicit confirmation. Do not proceed on inferred consent.

### 5. Execute — `executeSwap` (POST /agents/swap)

Consumes `intentId` + `simulationId` and a fresh EIP-191 signature over:

```
v1:FARMDASH_SWAP:{fromChainId}:{toChainId}:{fromToken}:{toToken}:{fromAmount}:{agentAddress}:{toAddress}
```

Returns transaction calldata and a `feeEventId`. **FarmDash does not broadcast it.**

Statuses to branch on:
- `428` — simulation missing, expired (>60s), failed or mismatched. Go back to step 3.
- `409` — halted by Risk Sentinel guardrails. Do not retry; resolve the finding.
- `401` — invalid signature or expired nonce. Re-sign.
- `402` — quota exhausted or paid route. Body carries a full x402 challenge; see
  `farmdash-x402-payment`.

### 6. Broadcast (off-API)

The user's wallet signs and submits the calldata. **This is the point of no return.**

### 7. Confirm — `confirmSwap` (POST /agents/confirm)

Send `feeEventId`, `txHash`, `agentAddress`. The server verifies the receipt against
server-committed fee fields. Do not consider the swap settled until this returns.

### 8. Audit — `getSwapHistory` (GET /agents/history)

Paginated with `limit` / `offset`. The only paginated operation in the API. Use it to
reconcile before any retry.

## Error envelope

Bespoke JSON, not RFC 9457: `{ok, error, code, message, retryable, request_id}`. Always log
`request_id` — it is on every response, success and failure.
