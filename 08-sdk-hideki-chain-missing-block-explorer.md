# SDK hidekiTestnet chain has no blockExplorers and says "No public block explorer is known", but the docs list one that indexes Hideki v2
Severity: normal
Area: SDK

## Steps to reproduce
1. Read markets-sdk 0.32.1 `dist/chains/definitions/hidekiTestnet.js`. Line 37 says "No public block explorer is known for Hideki, so that field stays unset", and the `defineChain` call has no `blockExplorers`.
2. The dreamDEX perps dev page Network table lists Explorer: https://explorer.testnet.somnia.network/tokyo.
3. Compare heights, read-only: `eth_blockNumber` on `https://api.hideki2.infra.testnet.somnia.network` with `GET https://explorer.testnet.somnia.network/tokyo/api/v2/stats` and `/tokyo/api/v2/addresses/0x478769E1177C0773c43A593DCCc4Ad15F15F1394` (the Hideki MarginBank).

## Expected
`hidekiTestnet.blockExplorers.default` is set to the Tokyo explorer, so viem/wagmi users get tx links, and the JSDoc matches the docs.

## Actual
The explorer indexes the same chain. At the time of checking, RPC height was 36,386,465 and the explorer's `total_blocks` was 36,386,340, with the latest explorer block at 36,386,485. The explorer also recognises the Hideki MarginBank as a contract (`"is_contract":true`). The SDK leaves the field unset, and its comment tells integrators that no explorer exists.

## Evidence
- 0.32.1 `dist/chains/definitions/hidekiTestnet.js:37` and the `defineChain({…})` block with no `blockExplorers`
- Docs: https://app.testnet.dreamdex.io/docs/developers/perps-testnet (Network table)
- `eth_chainId` → `0xc4cf` (50383), `eth_blockNumber` → `0x22b369a`; explorer stats `total_blocks: "36386340"`

## Environment
Docs as served 2026-10-29; @somnia-chain/markets-sdk 0.32.1; network Somnia Tokyo testnet (Hideki, chain 50383); wallet address: N/A (static review)
