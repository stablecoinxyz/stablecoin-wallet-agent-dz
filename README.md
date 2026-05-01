# StableCoin Wallet Agent

A [Pinata](https://agents.pinata.cloud) AI agent template that connects to your DollarWallet and lets you manage your SBC stablecoin on Base mainnet through natural conversation.

## What it can do

- Check your SBC balance
- Get your wallet address for receiving funds
- View recent transaction history
- Send SBC to any address (with confirmation step before executing)

## Requirements

You need a running instance of [DollarWallet](https://github.com/stablecoin-xyz/dollar-wallet-web) with agent access enabled.

## Setup

### 1. Generate an API key

In your DollarWallet app, go to **Settings → Agent Access** and click **Generate API Key**. Copy the key — it's only shown once.

### 2. Deploy the template on Pinata

1. Go to [agents.pinata.cloud](https://agents.pinata.cloud) and create a new agent from this template
2. Set the following secrets when prompted:

| Secret | Value |
|--------|-------|
| `DOLLAR_WALLET_API_KEY` | The key you generated in step 1 |
| `DOLLAR_WALLET_API_URL` | Your DollarWallet app URL (e.g. `https://app.yourdomain.com`) |

### 3. Set the system prompt

Paste the following as the agent's system prompt:

```
You are a DollarWallet agent. On startup, load your skill doc at {DOLLAR_WALLET_API_URL}/skill.md and use it as the authoritative reference for every endpoint and flow. Never send funds without first showing a preview and receiving explicit user confirmation.
```

## Example prompts

```
What's my SBC balance?
What's my wallet address?
Show my recent transactions
Send 5 SBC to 0x1234...
```

## Supported chain

- **Network:** Base mainnet
- **Token:** SBC (18 decimals)
- **Explorer:** [basescan.org](https://basescan.org)

## Security

- API keys are hashed (SHA-256) before storage — the raw key is never saved
- Send operations always require explicit user confirmation before executing
- Rate limited to 10 requests per minute per key
