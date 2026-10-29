# Perp env vars missing from .env.example, and RPC_URL is shared between spot and perp so it can silently mix chains
Severity: normal
Area: docs

## Steps to reproduce
1. List every env var read by `packages/perp-core/src`, `strategies/perp-*/src` and `scripts/perp-doctor.ts` (`process.env.X`, `envNum("X")`, `envBool("X")`) and compare with root `.env.example`.
2. Note `.env.example:34`: "Strategy knobs are in each strategy's own .env.example". `strategies/perp-*` have no `.env.example` (only ensemble, grid, market-making, mean-reversion, momentum, starter, treasury, twap and yield-optimizer do).
3. Put `NETWORK=hideki` and `RPC_URL=https://api.infra.testnet.somnia.network` in `.env` (a plausible leftover from a spot setup) and print `loadConfig()` from perp-core. No network calls.

## Expected
Every perp knob and override is documented. An override that only makes sense for one network is either namespaced or checked against the chain ID.

## Actual
Read by perp code but absent from `.env.example`: `WS_RPC_URL`, `INDEXER_URL`, `CHAIN_ID`, `OPERATOR_PERMISSIONS_REGISTRY` (`perp-core/src/config.ts:158, 169-172`), and `PERP_FLATTEN_ON_EXIT` (`strategies/perp-starter/src/index.ts:240`, mentioned only in the strategy README). `.env.example:12-14` documents `WS_URL`, which is the spot core's name. Perp code reads `WS_RPC_URL`.

`RPC_URL` is read by both the spot core (`packages/core/src/config/networks.ts:38,48`) and perp-core. Perp-core applies it with no check against the selected network:
```
{"network":"hideki","chainId":50383,"rpcUrl":"https://api.infra.testnet.somnia.network","indexerUrl":"https://hideki-dev.smk.somnia.host/v1/graphql","operatorRegistry":"0x6B2FbfeD328FF5F20C0BE7D3C3437613aFFE8D3a"}
```
That is Hideki's chain ID and registry with Shannon's RPC. The CI step `env-documented` only scans `ec-*` strategies (`scripts/checks.mjs:115-134`, "46 ec knobs"), so it cannot catch this.

## Evidence
- `packages/perp-core/src/config.ts:158, 169-172`
- `strategies/perp-starter/src/index.ts:240`; `strategies/perp-starter/README.md:27`
- `.env.example:10-14`, `:34`
- `scripts/checks.mjs:115-134`
- Output above: envdemo.log (second line)

## Environment
dreamdex-bot-kit @ 25798660 (main, 2026-10-12); @somnia-chain/markets-sdk 0.32.1 (npm latest) / 0.31.0 / 0.30.0 as noted; Node 22.23.3, npm 10.9.9; network Somnia Tokyo testnet (Hideki, chain 50383); wallet address: N/A (static review)
