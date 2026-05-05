# StableCoin Wallet Agent

A [Pinata](https://agents.pinata.cloud) AI agent template for managing your SBC stablecoin on Base mainnet through natural conversation. The agent comes preconfigured with a buying wallet via the [SBC agent payments platform](https://agents.stablecoin.xyz), so it can autonomously pay for x402-protected resources on your behalf.

## What it can do

- Check your SBC balance
- Get your wallet address for receiving funds
- View recent transaction history
- Send SBC to any address on Base (with confirmation step before executing)
- Bridge SBC from Base to Radius (max $100/tx, with confirmation + status polling)
- Pay for x402-protected API resources using your agent's buying wallet

## Setup

### 1. Get your SBC agent payments credentials

Go to [agents.stablecoin.xyz](https://agents.stablecoin.xyz), create an agent, and open the **API Keys** panel. Copy your **Agent ID** and **API Key**.

### 2. Deploy the template on Pinata

1. Go to [agents.pinata.cloud](https://agents.pinata.cloud) and create a new agent from this template
2. Set the following secrets when prompted:

| Secret | Value |
|--------|-------|
| `SBC_AGENT_ID` | Your agent ID from the SBC payments platform |
| `SBC_AGENT_API_KEY` | Your API key from the SBC payments platform |

### 3. Set the system prompt

Paste the following as the agent's system prompt:

```
You are an SBC stablecoin wallet agent. You help users manage their SBC wallet on Base mainnet — check balances, send SBC, bridge to Radius, and pay for x402-protected resources. Never send or bridge funds without first showing a preview and receiving explicit user confirmation.

You have a buying wallet on the SBC agent payments platform. To pay for any x402-protected resource, POST to https://agents.stablecoin.xyz/api/agents/{SBC_AGENT_ID}/pay with header X-Agent-Key: {SBC_AGENT_API_KEY} and body { "sellerUrl": "<resource url>" }. The platform handles permit signing and on-chain settlement. The response contains sellerResponse (the protected resource) and payment.txHash (the on-chain transaction).
```

## Example prompts

```
What's my SBC balance?
What's my wallet address?
Show my recent transactions
Send 5 SBC to 0x1234...
Send 10 SBC to 0x1234... on Radius
Bridge 20 SBC to 0xabcd... on Radius
Fetch the report at https://api.example.com/data
```

## Supported chains

- **Base mainnet** — direct send
- **Radius** — bridge via Brale (max $100/tx)
- **Token:** SBC (18 decimals)
- **Explorer:** [basescan.org](https://basescan.org)

## Security

- Send operations always require explicit user confirmation before executing
- SBC agent payments credentials (`SBC_AGENT_ID`, `SBC_AGENT_API_KEY`) are set once at deploy time and never exposed to end users
