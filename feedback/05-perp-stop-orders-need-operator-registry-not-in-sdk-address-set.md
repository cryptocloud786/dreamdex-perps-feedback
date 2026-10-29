# Docs' SDK setup can't place a first perp stop order: OperatorPermissionsRegistry is missing from SOMNIA_HIDEKI_TESTNET_ADDRESSES
Severity: normal
Area: SDK

## Steps to reproduce
1. Configure `SomniaMarkets` exactly as the Connect example on https://app.testnet.dreamdex.io/docs/developers/perps-testnet does (`addresses: SOMNIA_HIDEKI_TESTNET_ADDRESSES`).
2. Inspect `SOMNIA_HIDEKI_TESTNET_ADDRESSES` in markets-sdk 0.32.1 (`dist/addresses.js:59-75`). It has no `operatorPermissionsRegistry` key, although `config.d.ts:88` declares `operatorPermissionsRegistry?: Address`.
3. Read `placePerpStopOrder` in `dist/perp/stops.js:567-583`. If the owner has not yet authorised the pool's stop registry, it calls `operatorGrantCall`, which at lines 489-492 does `throw new NotConfiguredError("operatorRegistry or addresses.operatorPermissionsRegistry", "a perp stop order")` when neither the param nor the address set has the registry.

## Expected
Either the Hideki address set includes the registry the docs publish (0x6B2FbfeD328FF5F20C0BE7D3C3437613aFFE8D3a), or the developer page says to pass it (`addresses: { ...SOMNIA_HIDEKI_TESTNET_ADDRESSES, operatorPermissionsRegistry: "0x6B2F…8D3a" }`, or `operatorRegistry` on the call) before placing TP/SL or stop orders.

## Actual
The developer page lists the OperatorPermissionsRegistry in its Contracts table, and its pitfalls cover stop orders (0.15 STT each). It never connects the two. A developer who follows the page gets `NotConfiguredError` on their first stop order per pool. The bot kit hit the same thing and hardcodes the address with the comment "It is NOT in the SDK's bundled address sets … so `placePerpStopOrder` throws `NotConfiguredError` unless it is passed explicitly" (`packages/perp-core/src/config.ts:72-84`).

The same set's `collateral`/`testUsdc` (0x9E9492cC…148f) is tUSDC, the binary collateral, not the perps collateral USDso (0xed7993A7…4F72). The docs work around this with a pitfall note instead of the SDK exposing the perps collateral.

## Evidence
- markets-sdk 0.32.1: `dist/addresses.js:59-75`, `dist/config.d.ts:88`, `dist/perp/stops.js:489-492`, `:574-582`
- `dist/somniaMarketsClient.d.ts` (getOperatorPermissionsRegistry doc): "…otherwise throw NotConfiguredError, and no deployment manifest carries the key yet"
- dreamdex-bot-kit `packages/perp-core/src/config.ts:72-84`
- Registry has code on Hideki (`eth_getCode` 0x6B2F…8D3a → 176 bytes; getcode-hideki.log)

## Environment
Docs as served 2026-10-29; @somnia-chain/markets-sdk 0.32.1; network Somnia Tokyo testnet (Hideki, chain 50383); wallet address: N/A (static review)
