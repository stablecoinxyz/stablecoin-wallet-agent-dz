# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A [Pinata](https://agents.pinata.cloud) agent template — not a runnable application. It is deployed by an admin on Pinata, where it creates an AI agent that lets users manage their SBC stablecoin wallet (on Base mainnet) through natural conversation. There is no build step, test suite, or package manager.

## Repository structure

- `manifest.json` — Pinata template manifest: defines agent metadata, the template slug/category, and the required `DOLLAR_WALLET_API_URL` secret
- `README.md` — user-facing setup guide (system prompt, secrets, example prompts)
- `workspace/` — required by Pinata template validation; currently a placeholder

## How the agent works

The agent has no bundled tool implementations. Instead, it is given a system prompt that tells it to fetch the skill doc from `{DOLLAR_WALLET_API_URL}/skill.md` at startup. That skill doc (served by the DollarWallet backend) is the authoritative reference for all available API endpoints and flows.

The agent asks the user for their DollarWallet API key at the start of each session (keys are per-user, generated in DollarWallet → Settings → Agent Access).

## How the buying wallet works

The agent is preconfigured with credentials for the [SBC agent payments platform](https://agents.stablecoin.xyz). To pay for any x402-protected resource, it POSTs to:

```
POST https://agents.stablecoin.xyz/api/agents/{SBC_AGENT_ID}/pay
X-Agent-Key: {SBC_AGENT_API_KEY}

{ "sellerUrl": "https://api.example.com/data" }
```

The platform handles permit signing and on-chain settlement. The response returns `sellerResponse` (the protected content) and `payment.txHash`. The platform URL is hardcoded; `SBC_AGENT_ID` and `SBC_AGENT_API_KEY` are injected as secrets at deploy time.

## Key constraints

- **Send/bridge operations require explicit user confirmation** before executing — this is enforced in the system prompt and must be preserved in any prompt edits.
- Bridge to Radius is capped at $100/tx and goes through Brale.
- The token is SBC (18 decimals) on Base mainnet.
- `DOLLAR_WALLET_API_URL` is admin-set (the DollarWallet backend URL). `SBC_AGENT_ID` and `SBC_AGENT_API_KEY` are user-set at template launch from the SBC payments platform API Keys panel.
- SBC payments credentials are never exposed to end users — they are deploy-time secrets only.

## Deploying / making changes

Changes to `manifest.json` affect how the template appears and validates in the Pinata template registry. The `secrets` array must match what the agent actually needs at runtime. The `template.slug` must be stable once published — renaming it is a breaking change.
