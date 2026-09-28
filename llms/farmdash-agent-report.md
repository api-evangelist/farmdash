# FarmDash live agent-report pointer

This repository does not republish a dated runtime snapshot. Use the first-party sources:

- Narrative report: https://www.farmdash.one/agent-report.md
- Runtime capability contract: https://www.farmdash.one/api/v1/agent/status
- MCP manifest: https://www.farmdash.one/.well-known/mcp.json
- Fees and quotas: https://www.farmdash.one/api/v1/fees.json

Verified 2026-09-28: the MCP manifest describes a public-source stdio server with 84 tools; it does not advertise a hosted remote endpoint. The live status contract reports A2A transport as not exposed. The generated narrative report contains a conflicting source-availability sentence; use the live manifest for the MCP source and dedicated status/fee contracts for current runtime facts.
