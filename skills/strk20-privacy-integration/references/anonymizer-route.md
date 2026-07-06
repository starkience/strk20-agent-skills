# Route: Anonymizer contracts (DeFi protocols) — design now, build when examples are public

App-specific helper contracts that make private DeFi flows work. If the privacy goal involves protocol actions — lend, stake, LP, vault deposit/withdraw, escrow — the Wallet API alone is not enough. **DeFi almost always needs both**: the Privacy Wallet API (to reach the user's wallet) *and* an app-specific anonymizer contract (to do the protocol action). Never present private DeFi as zero-code pool composability.

## The model

The pool calls the anonymizer contract **atomically** as part of the user's transaction, through one mandatory entrypoint: **`privacy_invoke`**. The flow:

1. The pool withdraws the input tokens to the anonymizer.
2. The anonymizer performs the protocol action (swap, lend, vault deposit, …).
3. It returns the output tokens, which the pool credits back to the user as private notes.

If anything reverts, the entire operation rolls back — funds return to the pool, no tokens stranded. One external invoke per private transaction, executed as the final step.

**What this hides**: the user's address behind the DeFi action. **What may stay public**: the amounts and the app activity itself. Say so in the plan.

## Ownership — non-negotiable

StarkWare ships *reference examples*. The app team owns its production anonymizer contract end to end: review, audit, deployment, and maintenance. Every Branch B plan must include an audit step before mainnet, owned by the team.

## Status and references

Reference examples ship inside the Privacy SDK monorepo, which opens after the v0.14.3/onchain-screening upgrade. Expected examples (verify names and paths once the repo is public at `github.com/starkware-libs/starknet-privacy`):

- A **single-hop DEX swap** anonymizer (Ekubo) — full-swap-only.
- An **ERC-4626/SNIP-22-style vault** anonymizer (Vesu lending, deposit/withdraw) — intentionally lean; treat it as a skeleton to adapt, not a drop-in template.

If the repo is public when you run, point the plan at the actual package paths and the repo's own quickstart instead of these expectations.

## What the team can do today (put these in Phase 1–2 of the plan)

1. **Ship the Wallet API basics first** (`references/wallet-api-route.md`): shield/transfer/unshield/swap need no anonymizer and get users into the pool.
2. **Design the anonymizer on paper**: for each protocol action, specify input token(s) → action → output token(s) in the withdraw→act→re-shield shape; note approvals needed and failure/rollback behavior. Map each action to a function in their existing Cairo contracts (name the files from the scan).
3. **Prepare the toolchain**: the anonymizer is a normal Cairo contract — the team's existing Scarb + Starknet Foundry setup (snforge tests, sncast deploy) is exactly what they'll use.
4. **Line up the audit** (owner, budget, timing).

## When the SDK repo is public (Phase 3, with entry criteria)

1. Study the reference example nearest their use case; adapt to their protocol action.
2. Develop and test via the SDK-direct path on testnet — in dev the *team* controls the account and keys, so the SDK is appropriate there. **Production user flows still go through the Privacy Wallet API** — end users never expose viewing keys.
3. Test atomicity: action succeeds → outputs credited as private notes; action reverts → clean rollback.
4. Audit, deploy, then wire the dapp's Wallet-API flow to trigger the anonymizer.
