---
name: farmdash-x402-payment
status: archived_reference
not_official: true
superseded_by: https://www.farmdash.one/api/v1/fees.json
---
# Archived API Evangelist note — do not use as payment instructions

The former guide contains stale request counts and prices. There is no claim that FarmDash publishes a dedicated x402 skill.

- Current fee and quota contract: https://www.farmdash.one/api/v1/fees.json
- Runtime status: https://www.farmdash.one/api/v1/agent/status
- OpenAPI: https://www.farmdash.one/agents/openapi.yaml

If a route returns HTTP 402, inspect that response's current amount and destination; do not reuse cached prices, pay twice, or automate payment without explicit user consent. A payment buys API capacity only, not transaction authority.
