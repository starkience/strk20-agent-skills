# Links & pinned versions

Single source of truth for the plan's Links section. Statuses as of mid-July 2026, after the July 8, 2026 open-source launch of the Privacy SDK monorepo — re-verify the "coming soon" rows before citing.

## Live now

| What | Where | Notes |
|---|---|---|
| STRK20 pool contract (mainnet, canonical) | https://voyager.online/contract/0x040337b1af3c663e86e333bab5a4b28da8d4652a15a69beee2b677776ffe812a | The pool everything integrates with |
| Whitepaper | https://eprint.iacr.org/2026/474 | Protocol design |
| **Privacy SDK monorepo** (Apache 2.0, public) | https://github.com/starkware-libs/starknet-privacy | Open-sourced Jul 8, 2026. **Quickstart: https://github.com/starkware-libs/starknet-privacy/blob/main/sdk/README.md** — the repo's own README supersedes any expectations in this skill |
| Privacy SDK package | `@starkware-libs/starknet-privacy-sdk` | GitHub npm registry (not npmjs); **Node ≥ 24** required (verified from `sdk/package.json`) |
| Anonymizer reference examples | `packages/ekubo_swap_anonymizer`, `packages/vesu_lending_anonymizer` in the monorepo | Verified paths. Ekubo single-hop swap; Vesu lending deposit/withdraw. Skeletons to adapt, not drop-in templates — the team owns review + audit. (`packages/sub_account_anonymizer` also exists, but sub-accounts are not builder-ready — see coming soon) |
| starknet.js **v10.4.0** | https://github.com/starknet-io/starknet.js/releases/tag/v10.4.0 | Ships `WalletAccountV6` with STRK20 actions. STRK20-era releases are on the npm `next` tag (`latest` is still 10.0.x); 10.5.0 exists but adds nothing STRK20-related — pin ≥ 10.4.0 explicitly |
| WalletAccount guide (STRK20 with get-starknet v6) | https://starknet-js.com/docs/next/guides/account/walletAccount/#with-get-starknet-v6 | **Fetch this for the current API before writing code** |
| get-starknet **v6.0.2** | https://github.com/starknet-io/get-starknet | Install explicitly (npm `next` tag): `@starknet-io/get-starknet-discovery@6.0.2`, `@starknet-io/get-starknet-wallet-standard@6.0.2` |
| types-js **v0.10.3** | https://www.npmjs.com/package/@starknet-io/types-js/v/0.10.3 | Type definitions matching the Wallet API spec |
| Privacy Wallet API spec **v0.10.3** | https://github.com/starkware-libs/starknet-specs/releases/tag/v0.10.3 | Wallet-facing; dapps don't implement it |
| Wallet test dapp | https://starknet-wallet-account.vercel.app/ | Sanity-check wallet interactions |
| Prover Crate (open source) | https://github.com/starkware-libs/sequencer/tree/main/crates/starknet_transaction_prover | Self-hosted proving for advanced setups; subject to onchain screening like every route |
| Ready extension | — | Current privacy-enabled wallet to test against |
| Starknet docs | https://docs.starknet.io/ | General dev docs; tools index at https://docs.starknet.io/learn/cheatsheets/tools |

## Coming soon (verify before citing — check if already available)

| What | Expected | Check |
|---|---|---|
| Private sub-accounts | Wallet + SDK + extra Wallet API call still required (the `sub_account_anonymizer` contract package is already visible in the monorepo, but there is nothing builder-facing yet) | Track announcements |
| Xverse dapp-facing Wallet API | In progress (as of mid-July 2026) | Re-check status |

## Support

- Cairo CoreStars Telegram: `@sncorestars`
