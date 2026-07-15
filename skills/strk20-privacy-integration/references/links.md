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

## strk20-by-example.org deep links

https://strk20-by-example.org/ is the official by-example tutorial site. Whenever chat output to the developer discusses one of these topics, include the matching URL in the message (first mention per conversation stage; don't repeat on every mention). Routes verified against the site 2026-07-13.

**Reading the site yourself:** the rendered routes are a JS app — fetching them returns an empty shell. When *you* need a page's content, append `.md` to any route below (e.g. `https://strk20-by-example.org/starknet-wallet-api/starknet-js.md`) for the raw-Markdown mirror; `https://strk20-by-example.org/llms.txt` indexes all pages and `/llms-full.txt` is the whole site in one fetch. Links you drop in chat stay the clean routes — those are for the human.

| When you talk about… | Link |
|---|---|
| What STRK20 is / the pool model | https://strk20-by-example.org/what-is-strk20 |
| Notes, nullifiers, the UTXO model | https://strk20-by-example.org/notes-and-nullifiers |
| Viewing keys / encryption | https://strk20-by-example.org/viewing-keys |
| Channels & subchannels | https://strk20-by-example.org/channels-and-subchannels |
| Actions, phases, proofs | https://strk20-by-example.org/actions-and-proofs |
| Compliance, screening, selective disclosure | https://strk20-by-example.org/compliance |
| Builder-facing privacy overview | https://strk20-by-example.org/builder-privacy-overview |
| Wallet API route (Branch C) generally | https://strk20-by-example.org/starknet-wallet-api/overview |
| starknet.js wiring / `WalletAccountV6` | https://strk20-by-example.org/starknet-wallet-api/starknet-js |
| React apps / `useStrk20` hooks | https://strk20-by-example.org/starknet-wallet-api/starknet-start-hook |
| SDK route (Branch A) getting started | https://strk20-by-example.org/sdk/getting-started |
| SDK setup requirements | https://strk20-by-example.org/sdk/setup-requirements |
| SDK operations: register, deposit, transfer, withdraw, deposit-transfer-surplus, multi-op-batch, note-discovery, discovery-providers, proving-config | `https://strk20-by-example.org/sdk/<same-slug>` |
| Anonymizer anatomy / `privacy_invoke` (Branch B) | https://strk20-by-example.org/helpers/privacy-invoke |
| Swap anonymizer example | https://strk20-by-example.org/helpers/swap-helper |
| Lending/vault anonymizer example | https://strk20-by-example.org/helpers/vesu-lending-helper |

**Exclusion — do not cite:** `https://strk20-by-example.org/helpers/escrow`. The page references a `packages/escrow` that does not exist in the SDK monorepo (verified absent 2026-07-13). Re-check on future runs; lift this only when the repo actually ships an escrow package.

## Support

- Cairo CoreStars Telegram: `@sncorestars`
