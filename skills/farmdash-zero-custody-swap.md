---
name: farmdash-zero-custody-swap
status: archived_reference
not_official: true
superseded_by: https://github.com/Parmasanandgarlic/farmdash-openclaw-skills/tree/main/farmdash-signal-architect
---
# Archived API Evangelist note — do not use as current execution instructions

This document replaces an older generated workflow that no longer matches the current API contract. Use the official Signal Architect skill and the live contracts below.

- Official skill: https://github.com/Parmasanandgarlic/farmdash-openclaw-skills/tree/main/farmdash-signal-architect
- Runtime availability: https://www.farmdash.one/api/v1/agent/status
- Current fees: https://www.farmdash.one/api/v1/fees.json
- OpenAPI: https://www.farmdash.one/agents/openapi.yaml

Current boundary: compatibility swaps are EVM-only; the customer's wallet signs and submits. Solana is preview-only, 0x is operator-paused, and every state-changing action requires the live contract's simulation and user-confirmation rules. Never infer transaction authority from tool discovery.
