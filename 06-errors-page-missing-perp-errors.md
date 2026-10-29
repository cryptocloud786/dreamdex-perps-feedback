# Contracts → Errors page has no perp/MarginBank errors, though the perps dev page sends readers there for "the full list"
Severity: normal
Area: docs

## Steps to reproduce
1. On https://app.testnet.dreamdex.io/docs/developers/perps-testnet, note the perp-specific reverts it names: `InsufficientCollateral()` (0x3a23d825), `MaxPositionSizeExceeded()` (0x714b36d6), `IsolatedMarketBlocked`, the voucher block, and "ERC20: transfer amount exceeds balance". It closes with "The full list of order errors is on the [Errors](/docs/developers/contracts/errors#order-rejection) page."
2. Open https://app.testnet.dreamdex.io/docs/developers/contracts/errors and search the page.

## Expected
The Errors page has a perps / MarginBank / LiquidationEngine section listing the perp reverts with selectors and fixes, as it does for SpotPool and SpotStopOrderRegistry.

## Actual
The page sections are: decoding-a-revert, spotpool-errors, order-validation, order-rejection, funding-and-vault, gas, order-lifecycle, batch-and-amend, operators-and-authorization, builder-codes, spotstoporderregistry-errors and preflight-http-api-reverts. None covers perps. Search counts on the rendered text: `InsufficientCollateral` 0, `MaxPositionSizeExceeded` 0, `IsolatedMarketBlocked` 0, "Voucher" 0. A bot author decoding `0x3a23d825`, `0x714b36d6` or `0xaa4fc774` (IsolatedMarketBlocked(), which I computed) finds nothing there.

I checked the selectors given on the perps page against keccak256 of the signatures, and they are correct. The gap is only that the Errors page doesn't list them.

## Evidence
- Errors page section ids (from HTML): spotpool-errors, order-validation, order-rejection, funding-and-vault, gas, order-lifecycle, batch-and-amend, operators-and-authorization, builder-codes, spotstoporderregistry-errors, preflight-http-api-reverts
- keccak check: `InsufficientCollateral()`→0x3a23d825, `ImmediateOrCancelNoFill()`→0xd48c4403, `MaxPositionSizeExceeded()`→0x714b36d6, `IsolatedMarketBlocked()`→0xaa4fc774
- Saved copy: pages/errors.txt

## Environment
Docs as served 2026-10-29; @somnia-chain/markets-sdk 0.32.1; network Somnia Tokyo testnet (Hideki, chain 50383); wallet address: N/A (static review)
