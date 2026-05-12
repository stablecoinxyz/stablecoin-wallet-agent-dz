# SBC Payment Agent

An agent pre-configured with the SBC agent payments platform to manage your stablecoin wallet across multiple chains, make x402 and MPP payments, and control spending autonomously.

## What is this?

A pre-configured agent template backed by the [SBC Agent Payments](https://agents.stablecoin.xyz) platform. Deploy it and get an agent that has its own SBC stablecoin wallet across Base, Radius, and Tempo — separate from yours.

No CLI. Every operation is a plain REST call to `https://agents.stablecoin.xyz`. The system prompt contains instructions and context on how to call each API endpoint.

## What is SBC Agent Payments?

[SBC Agent Payments](https://agents.stablecoin.xyz) gives your agent a securely managed stablecoin wallet to send payments, pay for x402 and MPP-gated API resources, and manage spending policies autonomously — with full observability and controls over every action.

## Key Capabilities

- **Stablecoin wallet** across 6 chains (Base, Base Sepolia, Radius, Radius Testnet, Tempo, Tempo Testnet) with balance checks and transfers
- **x402 payments** to pay for permit-gated API resources using ERC-2612 signing
- **MPP payments** to pay for resources via direct ERC-20 transfer on Radius and Tempo
- **Spending controls** with daily/monthly limits, per-transaction caps, seller allowlists, time restrictions, and seller caps
- **Transaction history and analytics** with filters, CSV export, and daily timeseries
- **Webhooks** for payment events (confirmed, failed, rejected)
- **API key management** with the ability to view and revoke keys

The agent uses a system prompt loaded at deploy time as the authoritative reference for every endpoint, parameter, and flow.

## Example prompts

**Balances and wallet**

> "What's my SBC balance?"

> "What's my SBC balance on Base?"

> "What's my wallet address?"

**Send SBC**

> "Send 5 SBC to 0x1234..." or "Send 10 SBC to 0x1234... on Radius"

**Pay for a protected resource (x402 or MPP)**

> "Fetch the report at https://api.example.com/data"

> "Pay for https://api.example.com/premium using POST"

> "What protocol was used for that last payment?"

**Transaction history (dashboard session required)**

> "Show my recent transactions" or "Show only failed transactions"

> "Search transactions for 0xabcd..."

> "How do I export my transactions as a CSV?"

**Analytics (dashboard session required)**

> "Show my spending analytics for the last 30 days"

> "Which seller have I paid the most?"

> "Show my daily spending over the last 7 days"

**Spending policy rules (dashboard session required)**

> "Show my spending policy rules"

> "Disable the daily spending limit rule"

**Seller allowlist (dashboard session required)**

> "Add 0xabc... to my agent's seller allowlist"

> "Remove 0xabc... from my seller allowlist"

**Create spending rules (dashboard session required)**

> "Set a daily spending limit of 50 SBC"

> "Create a per-transaction limit of 10 SBC"

> "Restrict payments to business hours in UTC"

**Webhooks and API keys (dashboard session required)**

> "Register a webhook at https://myapp.com/hook for payment.confirmed events"

> "Revoke the key with prefix sk_live_abc123"

## Supported chains

| Chain | Network | MPP | Gas | Explorer |
|-------|---------|-----|-----|----------|
| `base` | Base mainnet | No | ETH | [basescan.org](https://basescan.org) |
| `base-sepolia` | Base Sepolia | No | ETH | [sepolia.basescan.org](https://sepolia.basescan.org) |
| `radius` | Radius mainnet | Yes | RUSD | [network.radiustech.xyz](https://network.radiustech.xyz) |
| `radius-testnet` | Radius Testnet | Yes | RUSD | [testnet.radiustech.xyz](https://testnet.radiustech.xyz) |
| `tempo` | Tempo mainnet | Yes | SBC | [explore.tempo.xyz](https://explore.tempo.xyz) |
| `tempo-testnet` | Tempo Testnet | Yes | SBC | [explore.moderato.tempo.xyz](https://explore.moderato.tempo.xyz) |

**Default chain:** `radius-testnet`

## How it works

1. Deploy the template on Pinata and open the chat
2. Set your `SBC_AGENT_ID` and `SBC_AGENT_API_KEY` secrets from the [SBC platform](https://agents.stablecoin.xyz) → API Keys panel
3. Fund your agent wallet by sending SBC to the agent's address (returned by any `/pay` response)
4. Ask for anything — check balances, send SBC, pay for x402 or MPP-gated resources, view analytics, manage webhooks, or control spending policies. The agent picks the right endpoint and executes with your confirmation.

By default, send and payment operations require explicit confirmation before executing. Spending limits and controls are configurable from the dashboard.

## Post-deploy setup

Set the two required secrets (`SBC_AGENT_ID` and `SBC_AGENT_API_KEY`) when prompted — the agent handles everything from there.

| Secret | Where to find it |
|--------|-----------------|
| `SBC_AGENT_ID` | [agents.stablecoin.xyz](https://agents.stablecoin.xyz) → API Keys panel |
| `SBC_AGENT_API_KEY` | [agents.stablecoin.xyz](https://agents.stablecoin.xyz) → API Keys panel |
