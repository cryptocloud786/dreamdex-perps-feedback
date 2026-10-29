# perp-core passes SOMNIA_TESTNET_ADDRESSES (Shannon) on Hideki; its "same deterministic addresses" comment is false and breaks on an SDK upgrade
Severity: normal
Area: SDK

## Steps to reproduce
1. Open `packages/perp-core/src/exchange.ts` in dreamdex-bot-kit. Line 12 imports `SOMNIA_TESTNET_ADDRESSES`, and line 56 passes it as `addresses` for every perp network, Hideki included. The comment at lines 36-39 says: "The bundled testnet address set serves Hideki too: the singletons the SDK looks up sit at the same deterministic addresses there".
2. Compare `SOMNIA_TESTNET_ADDRESSES` with `SOMNIA_HIDEKI_TESTNET_ADDRESSES` in `@somnia-chain/markets-sdk@0.32.1` `dist/addresses.js` (lines 10-27 vs 59-75).
3. Using read-only `eth_getCode` on the Hideki RPC (`https://api.hideki2.infra.testnet.somnia.network`), check the Shannon `marginBank` and `perpPoolFactory` from the testnet set.
4. Read `discoverPerpPools` in 0.32.1 `dist/unified/exchange.js` around line 1384: `let factory = engine.config.addresses?.perpPoolFactory;`. A configured factory "wins when present".

## Expected
On Hideki the bot uses the Hideki address set (`SOMNIA_HIDEKI_TESTNET_ADDRESSES`), as the dreamDEX developer docs' Connect example does. Any shared-address claim in comments is accurate.

## Actual
In 0.32.1 the two sets differ for `marginBank`, `perpPoolFactory`, `collateral`/`testUsdc`, `marketCreator` and `priceFeedScheduler`:
- Shannon `perpPoolFactory` 0x516d2cb6de41bE98444e887793946E65989Ba07b has **0 bytes of code on Hideki**. Hideki's is 0xE631fAEE547baeE07353E290f0562ea67d856560 (176 bytes, a proxy).
- Shannon `marginBank` 0xdd4A14A2763FDa39b9759D2D4150DB0e0f085C4E has **0 bytes of code on Hideki**. Hideki's is 0x478769E1177C0773c43A593DCCc4Ad15F15F1394.

This works today only because perp-core is held on SDK 0.31.0 (see report 01), whose `SOMNIA_TESTNET_ADDRESSES` has no `perpPoolFactory` or `marginBank` keys. When perp-core moves to 0.32.x, as the docs direct, three things go wrong on Hideki. Perp market discovery takes the configured Shannon factory, which is an empty address there. `listPerpPoolStatuses` and `listTradeablePerpPools` default to `config.addresses?.marginBank` (`dist/createClient.js:1181-1182`), which is the Shannon bank. And `addresses.collateral` names the wrong token.

## Evidence
- `packages/perp-core/src/exchange.ts:12`, `:36-39` (comment), `:56` (`addresses: SOMNIA_TESTNET_ADDRESSES`)
- markets-sdk 0.32.1 `dist/addresses.js:18` `marginBank: "0xdd4A…5C4E"`, `:23` `perpPoolFactory: "0x516d…a07b"` (testnet) vs `:66` `0x4787…1394`, `:71` `0xE631…6560` (Hideki)
- `eth_getCode` on Hideki v2 (log: getcode-hideki.log):
  `0x516d…a07b codebytes=0`, `0xdd4A…5C4E codebytes=0`, `0xE631…6560 codebytes=176`, `0x4787…1394 codebytes=176`
- 0.32.1 `dist/unified/exchange.js:1364` ("`addresses.perpPoolFactory` wins when present") and `:1384`
- Docs Connect example uses `SOMNIA_HIDEKI_TESTNET_ADDRESSES`: https://app.testnet.dreamdex.io/docs/developers/perps-testnet

## Environment
dreamdex-bot-kit @ 25798660 (main, 2026-10-12); @somnia-chain/markets-sdk 0.32.1 (npm latest) / 0.31.0 / 0.30.0 as noted; Node 22.23.3, npm 10.9.9; network Somnia Tokyo testnet (Hideki, chain 50383); wallet address: N/A (static review)
