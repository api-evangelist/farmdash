---
name: farmdash-guarded-futures
status: archived_reference
not_official: true
superseded_by: https://github.com/Parmasanandgarlic/farmdash-openclaw-skills/tree/main/farmdash-futures-strategist
---
# Archived API Evangelist note — do not use as current order instructions

Use the official Futures Strategist skill and the live status/OpenAPI contracts. This archived note is not an order template.

- Official skill: https://github.com/Parmasanandgarlic/farmdash-openclaw-skills/tree/main/farmdash-futures-strategist
- Runtime availability: https://www.farmdash.one/api/v1/agent/status
- OpenAPI: https://www.farmdash.one/agents/openapi.yaml

Hyperliquid orders and cancellations can change real market exposure. Require fresh, explicit user authorization and follow the live signature, risk, and tier gates. FarmDash does not receive raw customer private keys. A listed tool is not proof that execution is enabled.
