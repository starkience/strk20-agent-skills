---
name: strk20-privacy-integration
description: Ask-and-plan skill for adding STRK20 privacy to a Starknet project. Use when a developer wants to add privacy to the Starknet app open in this session — private/shielded transfers, confidential balances, private payments, private swaps or DeFi, hiding user↔account links — or mentions STRK20, privacy pool, shielding, viewing keys, or the Privacy Wallet API. Inspects the open repo, interviews the developer, picks the right integration route, and writes a repo-specific integration plan.
---

# STRK20 Privacy Integration Planner

This is an **ask-and-plan** skill. You are running inside the developer's own project. Your deliverable is not code — it is a conversation that converges on the right integration route, followed by a concrete, repo-specific plan written to `STRK20_INTEGRATION_PLAN.md`. Everything you ask and everything you plan should be grounded in what is actually in the open repo.

STRK20 is a live-on-mainnet, note-based (UTXO) privacy pool for any ERC-20 on Starknet — not a mixer. Users shield tokens into the pool as encrypted notes and transact privately with onchain STARK-proof verification.

The loop:

1. **Scan** the open project — learn everything you can before asking anything.
2. **Ask** — a short, adaptive interview; confirm what you detected, ask only what the repo can't tell you.
3. **Route** — map their answers to one of four integration routes.
4. **Plan** — write `STRK20_INTEGRATION_PLAN.md`, naming their real files and modules.

Before starting, read `references/concepts.md` — what STRK20 hides, what stays visible, and mandatory wording rules; nothing you say or plan may contradict it. Consult `references/starknet-dev-context.md` (the full Starknet toolchain map, wrapper-library compatibility warnings, greenfield guidance) during the scan whenever the checklist below isn't enough.

## Scope (current version)

**Starknet apps only.** If the project is on an EVM chain or elsewhere: say that EVM routes for STRK20 are planned (after internal audit and a contract upgrade) but not builder-ready, suggest tracking STRK20 announcements, and stop — do not improvise an EVM plan. If the project is multichain, plan only the Starknet side.

## Step 1 — Scan the open project

Look for (full detection table with privacy implications per tool: `references/starknet-dev-context.md`):

- **Frontend/dapp stack**: `package.json` — `starknet` (starknet.js, which version?), `@starknet-io/get-starknet*`, `@starknet-react/core`, `starknetkit`, `starkzap`; the frontend framework; where the app calls `connect()` and where it sends transactions.
- **Contracts**: `Scarb.toml`, `.cairo` files, `snfoundry.toml` — does the team ship its own Cairo contracts? Look for DeFi shapes: ERC-4626/SNIP-22-style vaults, lending/borrowing, swap/AMM, staking, escrow.
- **Backend**: does a server manage Starknet accounts with its own keys (`starknet_py`, `starknet-rs`, key material in config), or does everything go through the user's wallet?
- **Testing/dev environment**: starknet-devnet, Katana, Foundry (`snforge`) tests, deploy scripts (`sncast`, starkli).

Produce a short **project snapshot** (stack, versions, plug-in points as `file:line` where possible). You will mirror this back to the developer in Step 2 and embed it in the plan. If nothing Starknet-related is found, ask before assuming — the Starknet part may live in another repo, or the project may be greenfield; for greenfield, the plan recommends the standard stack (Scarb + Starknet Foundry for contracts, starknet.js + get-starknet for the dapp) with the privacy pieces pinned per `references/links.md`.

## Step 2 — Ask (the interview)

This is the heart of the skill. Rules:

- **Confirm, don't re-ask.** Open by mirroring the snapshot: "Here's what I see in your repo: … — did I get that right?" Detected facts become statements to confirm, not questions.
- **Ask in one or two short rounds**, not a questionnaire. Use the platform's structured question tool if available (e.g. AskUserQuestion in Claude Code); otherwise plain text.
- **Make questions concrete to their code.** "You have a vault contract in `src/vault.cairo` — should deposits into it be private, or just transfers between users?" beats "do you do DeFi?".

**Round 1 — always ask (whatever the repo didn't answer):**

1. **Builder type** — which best describes the project?
   - Normal dapp: users connect their own wallet
   - DeFi protocol / team with its own onchain contracts (vaults, lending, DEX, staking…)
   - Wallet or infra team that runs its own accounts, keys, and possibly proving
   - Backend/service managing its own Starknet accounts server-side
2. **Privacy goal** — what exactly should be private? Transfers/payments between users; balances; swap activity; protocol actions (vault deposit, borrow, stake); or the link between a user and the account acting onchain. (These map to different routes — get this precise.)
3. **Environment** — mainnet or testnet first? Which wallets do their users hold today?

**Round 2 — conditional, based on Round 1 + the scan:**

- DeFi answers → which specific actions, on their own contracts or someone else's protocol, and who would own/audit a helper contract.
- Backend-managed accounts → can the service store secrets (a viewing key) safely? That gates the SDK route.
- "Hide the user↔account link" → set expectations immediately: that is sub-accounts, not builder-ready yet (see Step 3).

Stop asking as soon as the route is unambiguous. Do not proceed to the plan while the privacy goal is still vague — "add privacy" is not a goal; "hide who pays whom" is.

## Step 3 — Route

| Builder | Route | Status today | Reference |
|---|---|---|---|
| Normal dapp relying on the user's wallet | **Privacy Wallet API via starknet.js** — the dapp asks the user's privacy-enabled wallet to act; it never touches viewing keys | **Buildable now** (starknet.js v10.4.0 + get-starknet v6.0.2 + Ready extension) | `references/wallet-api-route.md` |
| DeFi protocol / team with own contracts | **App-specific anonymizer contract + Privacy Wallet API** — shield/transfer/unshield/swap work through the Wallet API alone; protocol-specific actions need your own anonymizer contract | Wallet API part buildable now; reference anonymizer examples ship with the SDK repo (coming soon) — design now, build when public | `references/anonymizer-route.md` |
| Wallet / advanced integrator / backend with its own accounts and keys | **Privacy SDK direct** (TypeScript) — full control of registration, proving, discovery, note management | **Coming soon** — repo opens after the v0.14.3/onchain-screening upgrade; the open-source Prover Crate and Wallet API spec are readable today | `references/sdk-route.md` |
| Unlinkable accounts for DeFi (hide user↔account link) | **Private sub-accounts** | **Not builder-ready** — wallet, SDK, and an additional Wallet API call still required; scope and track | `references/sdk-route.md` (sub-accounts section) |

The split is absolute and follows one rule: **a dapp must never touch the user's viewing key.** The SDK requires the viewing key in the clear; wallets will never expose it. Anything that relies on a user's wallet therefore goes through starknet.js — the Privacy Wallet API is wallet-facing plumbing underneath, so pitch the route to the team as "use starknet.js," not "implement a wallet API". Only teams that own their accounts and can store viewing keys safely use the SDK directly.

Mixed cases are normal: a DeFi protocol is usually Wallet API for user flows **plus** an anonymizer contract for protocol actions, and may use the SDK only in dev/test where the team controls the account.

Read the matching reference file(s) before writing the plan. `references/links.md` has every link and pinned version.

## Step 4 — Plan

Write `STRK20_INTEGRATION_PLAN.md` to the repo root following `references/plan-template.md`, then walk the developer through it in chat (route chosen, why, and the first buildable step). If the file already exists from a previous run, read it and update it in place — preserve decisions the team already made and mark what changed — rather than overwriting. Requirements:

- **Repo-specific**: name the actual files/modules where wallet connection, transaction sending, and (if DeFi) contract interactions live, and say what changes in each. A plan that could have been written without the scan is a failed plan.
- **Honest about what's hidden vs visible**: include the hidden/visible table from `references/concepts.md`, adapted to their flows.
- **Phased**: something buildable this week first (connect + first shielded flow on the Wallet API route), then feature integration, then coming-soon pieces as tracked items with entry criteria ("when the SDK repo is public, do X").
- **Versioned**: exact package versions from `references/links.md`; get-starknet v6.x is on the npm `next` tag so versions must be explicit.
- **Next steps beyond code**: testing against the Ready extension and the wallet test dapp; and for anonymizer contracts, the team owns review, audit, deployment, and maintenance — put an audit step in the plan.

## Accuracy rules (mandatory — plans are public-facing copy)

- **Statuses drift.** This skill's statuses are as of early July 2026. Before finalizing a plan, verify what you can: check npm for current `starknet` / get-starknet versions, and check whether `github.com/starkware-libs/starknet-privacy` is public yet (it was scheduled to open-source shortly after this skill was written). If public, SDK and anonymizer-example steps become buildable-now — but verify actual paths in the repo before citing them.
- **Don't invent API details.** When sketching integration code, fetch the WalletAccount guide (URL in `references/links.md`) for the current API rather than guessing method names.
- **Selective disclosure**: the pool supports disclosing the information needed to respond to a legitimate regulatory request without exposing unrelated users. Never frame this as automatic compliance, regulator approval, or endorsement.
- **Screening**: deposit screening is enforced onchain by the protocol (from v0.14.3). Self-hosted proving does **not** bypass it. Never present any route as a screening workaround.
- **DeFi is not zero-code**: protocol-specific private DeFi needs an anonymizer contract **and** the Wallet API. Don't promise pool composability alone covers it.
- **Anonymizers hide the user address** — amounts and app activity may still be public. **Sub-accounts hide the wallet link** — the dapp action and amounts may still be public. Say which one the plan delivers.
- **No proving-performance claims** for laptops/phones; proving numbers are machine-dependent server data. Omit numbers from plans.
- The dapp never handles viewing keys, notes, or proofs — if a plan step would require them, the route is wrong; re-check Step 3.
