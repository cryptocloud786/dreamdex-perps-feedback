# Perps dev docs require SDK 0.32.x, but the bot kit pins perp-core to ^0.31.0 (and ec-core to ^0.30.0)
Severity: normal
Area: SDK

## Steps to reproduce
1. Open https://app.testnet.dreamdex.io/docs/developers/perps-testnet. It says the page "uses version 0.32.1, the one the app runs", and the Connect / Get test funds snippets use `SOMNIA_HIDEKI_TESTNET_ADDRESSES`, `client.getPerpVoucherEligibility` and `trader.claimPerpVoucher`.
2. Clone https://github.com/somnia-chain/dreamdex-bot-kit and look at `packages/perp-core/package.json` line 17: `"@somnia-chain/markets-sdk": "^0.31.0"`. Under 0.x semver, `^0.31.0` means `>=0.31.0 <0.32.0`, so it can never resolve 0.32.x.
3. `npm ci` at the repo root and check the lockfile: `packages/perp-core/node_modules/@somnia-chain/markets-sdk` is 0.31.0 and the hoisted root copy `node_modules/@somnia-chain/markets-sdk` is 0.30.0 (from `packages/ec-core/package.json:17` `^0.30.0`).
4. Paste the docs snippet verbatim into a project pinned to `@somnia-chain/markets-sdk@0.31.0` and run `tsc --noEmit`.

## Expected
The official bot kit, which the perps docs link to as the way to run a perps bot, builds on the same SDK line the docs describe (0.32.x). Alternatively, the docs state the minimum SDK version the snippets need.

## Actual
The docs snippet does not compile on the version the bot kit installs:
```
src/dev-doc-snippet.ts(3,3): error TS2724: '"@somnia-chain/markets-sdk"' has no exported member named 'SOMNIA_HIDEKI_TESTNET_ADDRESSES'. Did you mean 'SOMNIA_TESTNET_ADDRESSES'?
src/dev-doc-snippet.ts(20,42): error TS2339: Property 'getPerpVoucherEligibility' does not exist on type 'SomniaMarketsClientWithObservations'.
src/dev-doc-snippet.ts(22,24): error TS2339: Property 'claimPerpVoucher' does not exist on type 'Trader'.
```
The same file compiles with 0 errors against 0.32.1.

The repo also installs two SDK copies. The hoisted root copy (0.30.0) is what any script at the repo root resolves. Its `hidekiTestnet` chain points at the pre-regenesis host `api.hideki.infra.testnet.somnia.network`, whose `/ws` returns HTTP 502 today. The SDK reads the chain over WebSocket. The repo's own `npm run check` → `sdk-floor` only inspects ec-core (`PASS ^0.30.0`), so it misses this.

## Evidence
- Docs: https://app.testnet.dreamdex.io/docs/developers/perps-testnet ("this page uses version 0.32.1")
- `packages/perp-core/package.json:17` → `^0.31.0`; `packages/ec-core/package.json:17` → `^0.30.0`
- package-lock.json resolution: `node_modules/@somnia-chain/markets-sdk 0.30.0`, `packages/perp-core/node_modules/@somnia-chain/markets-sdk 0.31.0`
- npm registry: 0.30.0 published 2026-09-10, 0.31.0 published 2026-09-29, 0.32.1 published 2026-10-13 (`latest`)
- Export presence by version (count of .d.ts files mentioning the symbol): `SOMNIA_HIDEKI_TESTNET_ADDRESSES` 0/0/3, `getPerpVoucherEligibility` 0/0/3, `claimPerpVoucher` 0/0/3 for 0.30.0/0.31.0/0.32.1
- tsc output above: tsc-0.31.0.log (project snippet031); passing control: snippet (0.32.1, `tsc` exit 0)
- 0.30.0 `dist/chains/definitions/hidekiTestnet.js` uses `https://api.hideki.infra.testnet.somnia.network`; `curl` WebSocket upgrade to `…/ws` → `502`
- `scripts/checks.mjs` lines 208-218 (`sdk-floor` reads only ec-core)

## Environment
dreamdex-bot-kit @ 25798660 (main, 2026-10-12); @somnia-chain/markets-sdk 0.32.1 (npm latest) / 0.31.0 / 0.30.0 as noted; Node 22.23.3, npm 10.9.9; network Somnia Tokyo testnet (Hideki, chain 50383); wallet address: N/A (static review)
