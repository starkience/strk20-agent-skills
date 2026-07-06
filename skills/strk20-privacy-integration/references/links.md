# Links & pinned versions

Single source of truth for the plan's Links section. Statuses as of early July 2026 — re-verify the "coming soon" rows before citing (the SDK repo was scheduled to open-source days after this was written).

## Live now

| What | Where | Notes |
|---|---|---|
| STRK20 pool contract (mainnet, canonical) | https://voyager.online/contract/0x040337b1af3c663e86e333bab5a4b28da8d4652a15a69beee2b677776ffe812a | The pool everything integrates with |
| Whitepaper | https://eprint.iacr.org/2026/474 | Protocol design |
| starknet.js **v10.4.0** | https://github.com/starknet-io/starknet.js/releases/tag/v10.4.0 | Ships `WalletAccountV6` with STRK20 actions |
| WalletAccount guide (STRK20 with get-starknet v6) | https://starknet-js.com/docs/next/guides/account/walletAccount/#with-get-starknet-v6 | **Fetch this for the current API before writing code** |
| get-starknet **v6.0.2** | https://github.com/starknet-io/get-starknet | Install explicitly (npm `next` tag): `@starknet-io/get-starknet-discovery@6.0.2`, `@starknet-io/get-starknet-wallet-standard@6.0.2` |
| types-js **v0.10.3** | https://www.npmjs.com/package/@starknet-io/types-js/v/0.10.3 | Type definitions matching the Wallet API spec |
| Privacy Wallet API spec **v0.10.3** | https://github.com/starkware-libs/starknet-specs/releases/tag/v0.10.3 | Wallet-facing; dapps don't implement it |
| Wallet test dapp | https://starknet-wallet-account.vercel.app/ | Sanity-check wallet interactions |
| Prover Crate (open source) | https://github.com/starkware-libs/sequencer/tree/main/crates/starknet_transaction_prover | Self-hosted proving for advanced setups; subject to onchain screening like every route |
| Ready extension | — | Current privacy-enabled wallet to test against |
| Starknet docs | https://docs.starknet.io/ | General dev docs; tools index at https://docs.starknet.io/learn/cheatsheets/tools |

## Coming soon (verify before citing — check if already public)

| What | Expected | Check |
|---|---|---|
| Privacy SDK (TypeScript, Apache 2.0) + monorepo | Opens after the v0.14.3/onchain-screening upgrade | https://github.com/starkware-libs/starknet-privacy |
| Anonymizer reference examples (DEX swap, ERC-4626-style vault) | In the SDK monorepo | Verify package paths in the public repo |
| Private sub-accounts | Wallet + SDK + extra Wallet API call still required | Track announcements |
| Xverse dapp-facing Wallet API | In progress | Re-check status |

## Support

- Cairo CoreStars Telegram: `@sncorestars`
