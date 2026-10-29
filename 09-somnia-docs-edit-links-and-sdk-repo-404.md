# Somnia Markets docs "Edit this page" links and the SDK's npm repository field point to a GitHub repo that returns 404
Severity: normal
Area: docs

## Steps to reproduce
1. Open https://somnia-platform-docs.vercel.app/markets/somnia-markets and click "Edit this page". It links to https://github.com/somnia-chain/somnia-markets/blob/main/platform/docs/index.md.
2. The Perps SDK page links to https://github.com/somnia-chain/somnia-markets/blob/main/packages/sdk/docs/PERPS.md, and the SDK page links `…/packages/sdk/RELEASE_PROCESS.md`.
3. `npm view @somnia-chain/markets-sdk@0.32.1 repository` → `git+https://github.com/somnia-chain/somnia-markets.git` (directory `packages/sdk`). There is no `bugs` field.
4. Unauthenticated `curl -L` of those URLs.

## Expected
Public docs and the published package point at a public source or issue tracker. If the repo is private, the links are removed and the feedback form is offered instead.

## Actual
`https://github.com/somnia-chain/somnia-markets` → 404, `…/platform/docs/index.md` → 404, `…/packages/sdk/docs/PERPS.md` → 404. For comparison, `https://github.com/somnia-chain/dreamdex-bot-kit` → 200. External developers can't follow "Edit this page", can't read the SDK source repo, and have no public bug tracker for the SDK.

## Evidence
- curl status codes listed above (2026-10-29)
- package.json of markets-sdk 0.32.1: `repository.url = git+https://github.com/somnia-chain/somnia-markets.git`, `homepage = https://prd.smk.somnia.host/docs/typescript`, no `bugs`

## Environment
Docs as served 2026-10-29; @somnia-chain/markets-sdk 0.32.1; network Somnia Tokyo testnet (Hideki, chain 50383); wallet address: N/A (static review)
