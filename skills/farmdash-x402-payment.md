---
name: farmdash-x402-payment
description: >-
  Handle a FarmDash HTTP 402 challenge — pay the stated USDC amount on Base, retry with the
  payment header, or fall back to the free sandbox — without over-spending or double-paying.
api: FarmDash Agent Hub API
base_url: https://www.farmdash.one/api
operations:
  - getSwapQuote
  - getProtocolCatalog
  - getLiveTrailHeat
  - simulateSwapExecution
  - executeSwap
generated: '2026-08-26'
method: generated
source: 'errors/farmdash-problem-types.yml + live 402 observed 2026-08-26'
---

# FarmDash: handling a 402

402 is the most common error in this API — **24 of 27 operations declare it**. On FarmDash it
means two things at once: you hit the quota wall, *and* the paywall. There is no separate 429
in practice.

## What a 402 body contains

Verified live on 2026-08-26 against `/api/v1/agent/sybil-audit`:

```json
{
  "ok": false, "error": "payment_required", "code": "payment_required",
  "retryable": true, "request_id": "…",
  "rate_limit": { "tier": "scout", "used": 0, "limit": 0, "remaining": 0, "reset_epoch_seconds": … },
  "x402": {
    "protocol": "x402", "price": "4.99", "currency": "USDC",
    "network": "Base", "network_caip2": "eip155:8453", "chainId": "8453",
    "token": "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913",
    "destination": "0xb0Ed0d7bca24BBaD635B977C2efbE06742e33377",
    "amount": "4990000", "policy_key": "sybil_audit", "compute_class": "heavy",
    "payment_required_header": "PAYMENT-REQUIRED",
    "signature_header": "PAYMENT-SIGNATURE",
    "proof_header": "X-Payment-Proof"
  },
  "developer_sandbox": { "public_api_key": "fd_sandbox_mock", "live_data": false, … },
  "upgrade_url": "https://www.farmdash.one/pricing"
}
```

## Decide in this order

### 1. Can you answer from the free sandbox instead?

If the request targets `/v1/agent/protocols` or `/v1/trail-heat` and live data is not required,
retry with `?mock=true` or `Authorization: Bearer fd_sandbox_mock`. **Free and unmetered.**
Only these two endpoints support it.

### 2. Can you use Scout?

Omit the `Authorization` header, or send `Bearer fd_scout_free`. 5 requests / 24h per IP.

### 3. Is the price worth it — and is spending authorised?

Published one-off prices (USDC): market data 0.99 · full Trail Heat 1.99 · points simulation
2.99 · historical Trail Heat 2.99 · wallet portfolio 3.99 · sybil audit 4.99 · portfolio
optimization 6.99 · futures strategy 7.99. Default overage 0.99.

**Read `x402.price` from the body — do not assume.** The OpenAPI text says the default overage
is 0.01 USDC while the MCP manifest says 0.99 and the live response said 4.99. The runtime body
is authoritative.

**This spends real money and there is no refund path.** Confirm against the user's spend policy
before paying. If no cap is configured, ask.

### 4. Pay and retry

Send `x402.amount` base units of USDC on Base (chainId 8453) from the token contract to
`x402.destination`. Then retry **the exact same request** with:

- `PAYMENT-SIGNATURE` — the standard x402 header, preferred; or
- `X-Payment-Proof: 0x<txHash>` — FarmDash's own legacy proof path.

Payment grants `access_tier_after_payment` (e.g. `pioneer`) for that request.

### 5. Or upgrade

Repeated paid calls are cheaper on a tier: Pioneer $39.99/mo (1,500 req/day), Syndicate
$199/mo (50,000 req/day). See https://www.farmdash.one/pricing.

## Cautions

- **Never pay twice for one 402.** There is no idempotency key anywhere in this API. Record the
  `request_id` and the tx hash, and reconcile before any retry.
- The rate-limit numbers arrive **in the 402 body**, not in response headers. The documented
  `X-RateLimit-*` headers were not present on a live 200 (checked 2026-08-26), so you cannot
  pace yourself pre-emptively — budget defensively.
