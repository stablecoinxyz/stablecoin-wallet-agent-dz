# StableCoin Wallet Agent

A [Pinata](https://agents.pinata.cloud) AI agent template that connects to your DollarWallet and lets you manage your SBC stablecoin on Base mainnet through natural conversation.

## What it can do

- Check your SBC balance
- Get your wallet address for receiving funds
- View recent transaction history
- Send SBC to any address on Base (with confirmation step before executing)
- Bridge SBC from Base to Radius (max $100/tx, with confirmation + status polling)

## Requirements

You need a running instance of [DollarWallet](https://github.com/stablecoin-xyz/dollar-wallet-web) with agent access enabled.

## Setup

### 1. Generate an API key

In your DollarWallet app, go to **Settings → Agent Access** and click **Generate API Key**. Copy the key — it's only shown once.

### 2. Deploy the template on Pinata (admin — one time)

1. Go to [agents.pinata.cloud](https://agents.pinata.cloud) and create a new agent from this template
2. Set the following secret when prompted:

| Secret | Value |
|--------|-------|
| `DOLLAR_WALLET_API_URL` | Your DollarWallet app URL (e.g. `https://app.yourdomain.com`) |

### 3. Set the system prompt

Paste the following as the agent's system prompt:

```
You are a DollarWallet agent. On startup, load your skill doc at {DOLLAR_WALLET_API_URL}/skill.md and use it as the authoritative reference for every endpoint and flow. Ask the user for their DollarWallet API key before making any requests. Never send funds without first showing a preview and receiving explicit user confirmation.
```

### 4. Users connect with their own key

Each user generates their own API key in DollarWallet → Settings → Agent Access, then pastes it into the agent chat when prompted. The key is used only for that session.

## Example prompts

```
What's my SBC balance?
What's my wallet address?
Show my recent transactions
Send 5 SBC to 0x1234...
Send 10 SBC to 0x1234... on Radius
Bridge 20 SBC to 0xabcd... on Radius
```

## Supported chains

- **Base mainnet** — direct send
- **Radius** — bridge via Brale (max $100/tx)
- **Token:** SBC (18 decimals)
- **Explorer:** [basescan.org](https://basescan.org)

## Security

- API keys are hashed (SHA-256) before storage — the raw key is never saved
- Send operations always require explicit user confirmation before executing
- Rate limited to 10 requests per minute per key
