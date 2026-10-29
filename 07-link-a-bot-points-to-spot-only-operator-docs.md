# Link a Bot sends developers to "Operators & Session Keys", which has no Hideki/perps addresses (its testnet registry has no code on Hideki)
Severity: normal
Area: docs

## Steps to reproduce
1. On https://app.testnet.dreamdex.io/docs/trading/common/link-a-bot ("Run your bot against the wallet"): "Your bot … places orders for your wallet through placeOrderFor, cancelOrderFor and reduceOrderFor. [Operators & Session Keys](/docs/trading/spot/operators) covers the contract side."
2. Open https://app.testnet.dreamdex.io/docs/trading/spot/operators and read the Addresses table and the cast example.
3. Using read-only `eth_getCode` on Hideki (`https://api.hideki2.infra.testnet.somnia.network`), check the "Testnet (50312)" registry it lists.

## Expected
The page linked for "the contract side" of a perps-capable bot (Perps is ticked by default in Link a bot) covers the Hideki deployment, where the app's testnet wallets and perps live. That means the Hideki OperatorPermissionsRegistry (0x6B2FbfeD328FF5F20C0BE7D3C3437613aFFE8D3a, given only on the perps dev page) and the fact that perps access is granted per market.

## Actual
The Operators page lists only Mainnet (5031) 0xE7a1…05ce and Testnet (50312) 0x15C7e8CE38F021c5b45d098AaD788f63090bF20A. It talks only about SpotPools and the SpotPoolRegistry ("a global grant auto-extends to future pools"). On Hideki, 0x15C7… has **0 bytes of code**. The page also doesn't mention perp pools. Link a Bot itself says perps access is per market and must be re-added for new markets, which contradicts a reader's "global grant auto-extends" takeaway.

## Evidence
- https://app.testnet.dreamdex.io/docs/trading/common/link-a-bot (last section)
- https://app.testnet.dreamdex.io/docs/trading/spot/operators (Addresses table; saved text pages/operators.txt)
- `eth_getCode` on Hideki: `0x15C7…F20A codebytes=0`, `0x6B2F…8D3a codebytes=176` (getcode-hideki.log)

## Environment
Docs as served 2026-10-29; @somnia-chain/markets-sdk 0.32.1; network Somnia Tokyo testnet (Hideki, chain 50383); wallet address: N/A (static review)
