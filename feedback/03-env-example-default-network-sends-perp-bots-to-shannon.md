# Root .env.example (and README "keep NETWORK=testnet") quietly points local perp bots at Shannon instead of Hideki
Severity: normal
Area: docs

## Steps to reproduce
1. Follow the bot-kit README quick start: `cp .env.example .env  # add your PRIVATE_KEY, keep NETWORK=testnet` (README.md:71).
2. Follow docs/perps.md: link a bot key in the app (the app's perps live on Hideki), set `OWNER_ADDRESS`, and run `npm start -w perp-starter`.
3. Or, with no network at all, load perp-core's config from a directory whose `.env` is an unmodified copy of `.env.example`:
   ```
   cp .env.example /tmp/envdemo/.env
   cd /tmp/envdemo && env -u NETWORK tsx demo.ts   # demo.ts prints loadConfig()
   ```

## Expected
Perp strategies run on Hideki (chain 50383) unless the user deliberately picks Shannon. `.env.example` line 130 and `perp-core/src/config.ts:17` both say Hideki "is the default when NETWORK is unset".

## Actual
`.env.example:8` ships `NETWORK=testnet` uncommented, and its own description (line 7) lists only "testnet" or "mainnet" with no mention of `hideki`. The perp default never applies, so a local perp bot connects to Shannon, which is not where the user's app account or link grant live:
```
{"network":"testnet","chainId":50312,"rpcUrl":"https://api.infra.testnet.somnia.network","indexerUrl":"https://dev.smk.somnia.host/v1/graphql","operatorRegistry":"0x15C7e8CE38F021c5b45d098AaD788f63090bF20A"}
```
The repo already knows about this for Railway. `scripts/railway-start.mjs:73-78` says: "The baked .env says NETWORK=testnet for the spot bots and would win once the strategy loads it, so a perp-* service that sets no NETWORK of its own is pointed at Hideki here." The local path gets no such fix. The user then hits the startup link check ("…and that NETWORK matches the app's network", `perp-core/src/operator.ts:138`) with no hint that the template caused it.

## Evidence
- `.env.example:7-8`, `.env.example:128-132`; `README.md:71`
- `packages/perp-core/src/config.ts:145` (`process.env.NETWORK ?? "hideki"`)
- `scripts/railway-start.mjs:73-78`
- Output above: envdemo.log (first line)
- docs/perps.md:36-44 shows `NETWORK=hideki` only inside the example block, without saying to change the existing line

## Environment
dreamdex-bot-kit @ 25798660 (main, 2026-10-12); @somnia-chain/markets-sdk 0.32.1 (npm latest) / 0.31.0 / 0.30.0 as noted; Node 22.23.3, npm 10.9.9; network Somnia Tokyo testnet (Hideki, chain 50383); wallet address: N/A (static review)
