# SBC Agent Payments — API Reference

All endpoints are at https://agents.stablecoin.xyz

---

## 1. Wallet Balance

GET /api/agents/{SBC_AGENT_ID}/balance?chain={chainSlug}
X-Agent-Key: {SBC_AGENT_API_KEY}

Address is auto-resolved from your API key — no address param needed.

Response:
- formatted: human-readable balance, e.g. "5.000000"
- walletAddress: the agent's wallet address — use this to answer "what's my wallet address?"
- chain: display name
- chainSlug: slug used in API calls
- hasGas: boolean — if false, wallet may not have enough gas
- rusdBalance: (Radius chains only) RUSD gas balance

Present as: "Your SBC balance is {formatted} SBC on {chain}."
If hasGas is false: "Warning: your wallet may be low on gas."
For Radius: also show "Gas balance (RUSD): {rusdBalance}."

---

## 2. Supported Chains

| Chain slug     | Network          | Decimals | MPP | Gas                               |
|----------------|------------------|----------|-----|-----------------------------------|
| base           | Base mainnet     | 18       | No  | ETH                               |
| base-sepolia   | Base testnet     | 6        | No  | ETH                               |
| radius         | Radius mainnet   | 6        | Yes | RUSD (need > 0.0002)              |
| radius-testnet | Radius testnet   | 6        | Yes | RUSD (need > 0.0002)              |
| tempo          | Tempo mainnet    | 6        | Yes | SBC pays gas directly             |
| tempo-testnet  | Tempo testnet    | 6        | Yes | SBC pays gas directly             |

Default: radius-testnet. Always pass the chain slug when the user mentions a network.
Tempo: fees deducted from SBC balance automatically.
Radius: wallet needs RUSD for gas. If gas error, explain this.

---

## 3. Send SBC

POST /api/agents/{SBC_AGENT_ID}/send
X-Agent-Key: {SBC_AGENT_API_KEY}
Body: { "to": "0x...", "amount": 5 }

Always confirm first:
1. Show: "Send {amount} SBC to {to} on {chain}. Confirm? (yes/no)"
2. Only call API after explicit "yes".
3. On success: "Sent {amount} SBC to {to}. Transaction: {explorer}"

Response: { success, txHash, explorer, from }

Errors:
- Tempo: "Insufficient SBC balance. On Tempo, gas fees are paid from your SBC balance."
- Radius: "Insufficient RUSD for gas. Fund the wallet with more SBC to cover gas."
- Base: "Insufficient ETH for gas fees."

---

## 4. Pay for x402 or MPP Resources

POST /api/agents/{SBC_AGENT_ID}/pay
X-Agent-Key: {SBC_AGENT_API_KEY}
Body: { "sellerUrl": "https://...", "method": "GET", "headers": {}, "body": "..." }

Auto-detects protocol from seller's 402 response:
- x402: payment-required header → ERC-2612 permit signing
- MPP: www-authenticate header → direct ERC-20 transfer (Radius/Tempo only)

Response:
{
  "success": true,
  "sellerResponse": { ... },   <- ALWAYS show this to the user
  "payment": { "protocol", "txHash", "amount", "explorer" },
  "agent": { "address": "0x..." }
}

Present as: show sellerResponse fully, then "Paid {amount} SBC via {protocol}. Transaction: {explorer}"

---

## 5. Spending Control Errors (HTTP 403)

| error                      | Message                                                                                                        |
|----------------------------|----------------------------------------------------------------------------------------------------------------|
| seller_not_in_allowlist    | "This seller is not approved. Add it at agents.stablecoin.xyz."                                               |
| per_tx_limit_exceeded      | "This payment of {requested} SBC exceeds your per-transaction limit of {limit} SBC."                          |
| daily_limit_exceeded       | "Daily limit reached. Limit: {limit}, spent: {spent}, remaining: {remaining} SBC."                            |
| monthly_limit_exceeded     | "Monthly limit reached. Limit: {limit}, spent: {spent}, remaining: {remaining} SBC."                          |
| policy_max_amount_exceeded | "Payment exceeds the policy maximum. Contact your platform owner to adjust."                                   |
| policy_time_restricted     | "Payments are restricted to certain hours. Try again during the allowed window."                               |
| policy_seller_cap_exceeded | "You've hit the spending cap for this seller for the current period."                                         |
| agent_archived             | "This agent is archived. Funds can be withdrawn at agents.stablecoin.xyz."                                    |

Rate limit (HTTP 429): "Limited to 10 payments per minute. Wait ~60 seconds and try again."

---

## 6. Transaction History (session required)

GET /api/agents/{SBC_AGENT_ID}/transactions
  ?limit=20 &cursor={id} &search={query} &status={pending|confirmed|failed|rejected}

Response: { transactions: [...], nextCursor }
Fields: id, type, amount, network, counterparty, txHash, sellerUrl, status, failReason, createdAt
Present as table: Date | Type | Amount | Network | Status | Counterparty | Tx Link

Export CSV: /api/agents/{SBC_AGENT_ID}/transactions/export?status=confirmed
Tell user to open in browser while logged into agents.stablecoin.xyz.

---

## 7. Analytics (session required)

GET /api/analytics?range={7d|30d|90d|all}

Fields: totalSpent, totalEarned, sentCount, receivedCount, byNetwork[], byStatus[], topCounterparties[], timeseries[]

Summary: "In the last {range}: spent {totalSpent} SBC across {sentCount} payments. Top chain: {byNetwork[0].network}. Top seller: {topCounterparties[0].address}."

---

## 8. Seller Allowlist (session required)

Add:    POST   /api/agents/{SBC_AGENT_ID}/allowlist  { "sellerAddress": "0x..." }
Remove: DELETE /api/agents/{SBC_AGENT_ID}/allowlist  { "sellerAddress": "0x..." }

Always confirm before adding or removing.

---

## 9. Webhooks (session required)

List:     GET    /api/agents/{SBC_AGENT_ID}/webhooks
Register: POST   /api/agents/{SBC_AGENT_ID}/webhooks  { "url": "...", "events": ["payment.confirmed", "payment.failed", "payment.rejected"] }
Delete:   DELETE /api/agents/{SBC_AGENT_ID}/webhooks?endpointId={id}

On register, response includes a secret — tell user: "Your webhook secret is: {secret}. Save this now — it will not be shown again."
Always confirm before deleting.

---

## 10. Spending Policy Rules (session required)

List:   GET    /api/agents/{SBC_AGENT_ID}/policy-rules
Create: POST   /api/agents/{SBC_AGENT_ID}/policy-rules  { "type": "daily_spending_limit", "config": { "amount": 50 }, "active": true }
Toggle: PATCH  /api/agents/{SBC_AGENT_ID}/policy-rules  { "ruleId": "...", "active": true }
Delete: DELETE /api/agents/{SBC_AGENT_ID}/policy-rules?ruleId={id}

Valid types: per_tx_limit | daily_spending_limit | monthly_spending_limit | max_amount | time_restriction | per_seller_cap

Always confirm before creating, toggling, or deleting.

---

## 11. API Key Management (session required)

List:   GET    /api/agents/{SBC_AGENT_ID}/api-keys
Revoke: DELETE /api/agents/{SBC_AGENT_ID}/api-keys  { "keyId": "..." }

Always confirm before revoking: "Revoke key {keyPrefix}? This will immediately break any agent using it. (yes/no)"
To generate new keys: direct users to agents.stablecoin.xyz → API Keys panel.
