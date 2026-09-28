# FarmDash Agent API

FarmDash helps wallets and agents research DeFi protocols and prepare guarded transactions. It separates live quantitative Trail Heat from the editorial protocol catalog, and customer-signed EVM swap preparation from guarded Hyperliquid order workflows.

- **Provider:** https://www.farmdash.one
- **Documentation:** https://www.farmdash.one/docs
- **Developer portal:** https://www.farmdash.one/agents
- **REST contract:** OpenAPI 3.1.0, API contract 2.0.0, 43 operations across 15 tags — https://www.farmdash.one/agents/openapi.yaml
- **Runtime authority:** https://www.farmdash.one/api/v1/agent/status
- **Fees and quotas:** https://www.farmdash.one/api/v1/fees.json
- **MCP:** 84 tools, local stdio transport; public source at https://github.com/Parmasanandgarlic/farmdash-openclaw-skills/tree/main/mcp-server. No hosted endpoint or published npm package is claimed.
- **Skills:** 10 public OpenClaw skill directories — https://github.com/Parmasanandgarlic/farmdash-openclaw-skills
- **Boundaries:** EVM swap preparation is customer-signed/submitted; Solana is preview-only; 0x is paused. Hyperliquid orders can change real exposure and require explicit authorization. FarmDash does not receive raw customer private keys. A2A transport is not exposed.

The published OpenAPI references bearerAuth/ApiKeyAuth without declaring those security schemes. This profile documents a directory-only Bearer alias and does not modify FarmDash source. Use live status and fees when static narrative claims conflict.
