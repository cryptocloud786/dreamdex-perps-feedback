# How to get paper USDso is described two different ways (join Perps Arena vs. anyone can claim via claimPerpVoucher)
Severity: normal
Area: docs

## Steps to reproduce
1. https://app.testnet.dreamdex.io/docs/trading/perps-testnet → Get started: "Get paper USDso. Join [Perps Arena](https://app.testnet.dreamdex.io/competition), the paper-trading competition, to receive 1,000 paper USDso."
2. https://app.testnet.dreamdex.io/docs/trading/perps-testnet/margin-and-liquidations#paper-usdso: "The 1,000 USDso that Perps Arena gives you is a credit voucher."
3. https://app.testnet.dreamdex.io/docs/developers/perps-testnet → Get test funds: "Any address can claim the 1,000 USDso perps credit once, as long as it holds no perp positions or orders", using `trader.claimPerpVoucher`.
4. Compare the link targets. The trading page links `/competition` (page title "Perps Arena | dreamDEX"), while the site banner and nav link `/competition-hub` (title "Competition | dreamDEX").

## Expected
One consistent statement of who is eligible (Arena entrants only, or any address) and whether claiming via the SDK enrols the address in Perps Arena. Plus one canonical Arena URL.

## Actual
The user-facing pages describe the credit as something Perps Arena "gives you". The developer page says any address can self-claim on chain once. Neither page says whether an SDK claim counts as joining the Arena, or how it interacts with the voucher rules (14-day clawback, the market allowlist, leverage locked while any voucher balance remains). The rules are only on the margin page and aren't mentioned beside the SDK claim. Two different Arena URLs are in use.

## Evidence
- Pages and quotes above (fetched 2026-10-29)
- `/competition` → `<title>Perps Arena | dreamDEX</title>`; `/competition-hub` → `<title>Competition | dreamDEX</title>`

## Environment
Docs as served 2026-10-29; @somnia-chain/markets-sdk 0.32.1; network Somnia Tokyo testnet (Hideki, chain 50383); wallet address: N/A (static review)
