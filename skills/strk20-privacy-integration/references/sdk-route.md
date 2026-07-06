# Route: Privacy SDK direct (wallets & advanced integrators) — coming soon

The low-level route for teams that control their own account, prover, and viewing-key setup and want to interact with the pool directly instead of through a user's wallet.

**Who it's for**: wallets adding STRK20 support; backends that manage their own Starknet accounts *and can store the viewing key safely*; apps that want their own prover + account to send private transactions without a third-party wallet.

**Who it's not for**: normal end-user dapps. A dapp must never receive a user's viewing key — those integrate via the Wallet API route. If the interview reveals the "backend" actually acts on end-user funds with end-user consent through their wallets, that's the Wallet API route, not this one.

## What the SDK wraps

A TypeScript client (Apache 2.0) covering every step of working with STRK20 private notes:

- **Viewing key** registration
- **Channels and subchannels** (per-counterparty, per-token setup)
- **Proof generation** — builds the transaction sent to the prover, with a configurable proving backend (hosted service or self-hosted)
- **Proof submission** onchain to the pool contract
- **Discovery** — syncing notes/balances via an indexer (for holders of the viewing key)
- **Fluent builder** — composing multi-token, multi-action private transactions
- **History** — balance and transaction history for viewing-key holders

## Status and what to do today

The SDK repo opens after the v0.14.3/onchain-screening upgrade — **check whether `github.com/starkware-libs/starknet-privacy` is public before writing the plan**; if it is, the plan's SDK steps become buildable-now and the repo's own README/quickstart supersedes this file's expectations.

Readable today regardless:

- **Privacy Wallet API spec v0.10.3** — if the team is a wallet, this is the dapp-facing surface they'll eventually expose: https://github.com/starkware-libs/starknet-specs/releases/tag/v0.10.3
- **Prover Crate** (open source) — for teams planning self-hosted proving: https://github.com/starkware-libs/sequencer/tree/main/crates/starknet_transaction_prover
- **Whitepaper**: https://eprint.iacr.org/2026/474

Plan-able today: account and key-custody design (where the viewing key lives, who can read it), proving strategy (hosted service vs self-hosted — self-hosting is an infra commitment and is **still subject to onchain deposit screening**, never a workaround), and which flows to build first (register → shield → private transfer is the canonical first flow).

## Sub-accounts (Branch D) — coming soon

Private sub-accounts are the upcoming path for acting through real Starknet accounts with **no public onchain link** to the user's main wallet or to each other. Intended result: everyday DeFi — borrowing, staking, swapping — with the user↔account link hidden, while the dapp action and amounts may still be public.

Planned flow: wallet shields funds into the pool → a sub-account anonymizer creates and directs sub-accounts → each sub-account interacts with dapps like a normal account → funds return to the pool, shielded again.

**Status**: coming soon. Contract work exists; wallet support, SDK support, and an additional Privacy Wallet API call are on the way before builder-facing integration opens. There is nothing for builders to ship yet.

**What a plan does with Branch D**: scope and track. Identify which of the team's flows want unlinkable accounts (e.g. private collateral positions, private staking), note them as sub-account candidates with an entry criterion ("when sub-accounts become available to builders"), and route everything buildable today through Branches A–C. Do not name specific partner protocols as launch examples.
