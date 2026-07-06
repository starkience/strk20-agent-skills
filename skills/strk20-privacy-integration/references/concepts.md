# STRK20 Concepts — what it is, what it hides, how to talk about it

Read this before interviewing the developer or writing a plan. It defines the mental model you explain to teams and the wording rules every plan must follow.

## The one-sentence pitch

Confidential transfers and private DeFi on any ERC-20, built on the wallets and liquidity Starknet already has — no bespoke privacy coin, no rebuilding cryptography.

## Mental model

- The STRK20 pool is **live on Starknet mainnet**. It is a **note-based (UTXO) pool, not a mixer**: shielding deposits an ERC-20 into the pool, where the balance is held as an encrypted note; private transfers spend existing notes and create new ones.
- **Onchain proof verification**: every private transaction carries a zero-knowledge (STARK) proof confirming the spent notes exist, belong to the spender, haven't been double-spent, and that value is conserved. Starknet verifies the proof in-protocol before updating pool state.
- **Registration first**: an account must register in the pool (set a viewing key) before it can hold or receive private balances; both sender and recipient must be registered before private transfers between them. **Wallets handle registration automatically on first use — dapps don't.**
- **Any ERC-20**: standard ERC-20s (stablecoins, STRK, strkBTC, …) enter the pool without changing the token contract. Users shield when they want privacy and unshield when transparency is required.
- **Shield / unshield vocabulary**: shield = deposit into the pool (public → private); unshield = withdraw (private → public). Use these words consistently in plans.

## Hidden vs visible — always be honest about this

Every plan must include a version of this table, adapted to the app's flows:

| Private (inside the pool) | Public (visible onchain) |
|---|---|
| Sender and receiver of a private transfer | Deposit and withdrawal amounts (the public ERC-20 legs) |
| Transfer amounts and token type | The fact that an address interacted with the pool |
| Which notes were spent | Timing of pool interactions |

A paymaster can decouple the submitting address from the transaction. Additionally, per route:

- **Anonymizer contracts** hide the *user's address* in a DeFi action; the amounts and the app activity itself may still be public.
- **Private sub-accounts** (upcoming) hide the *public onchain link* between a user's main wallet and the account acting; the dapp action and amounts at the dapp may still be public.

Never let a plan imply more privacy than the route actually delivers.

## The golden rule

**A dapp must never touch the user's viewing key.** The Privacy SDK expects the viewing key (a secret) in the clear, and a wallet will never provide it. The wallet holds keys, runs the SDK internally, manages notes, and does the proving. The dapp only *asks* the wallet to act, via starknet.js. Everything in the route table follows from this rule. If a plan step would require the dapp to see keys, notes, balances-by-key, or proofs, the route is wrong.

Practical consequence for UX planning: a dapp cannot read a user's shielded balances itself (it has no viewing key). Design UI around actions the wallet performs, and verify against the current WalletAccount guide / Wallet API spec what state, if any, the wallet exposes to dapps.

## Screening and compliance — mandatory wording

- **Protocol-enforced screening**: deposit screening is part of the pool flow. From protocol version v0.14.3, screening enforcement moves onchain, so deposits require screening approval regardless of prover route. **Self-hosted proving does not bypass deposit screening.** Never present any route, self-hosting included, as a screening workaround.
- **Selective disclosure**: the system is confidential by default and can disclose the information necessary to respond to a legitimate regulatory request without exposing unrelated users. Never frame this as automatic compliance, regulator approval, or regulator endorsement.
- **Builders still own**: app-level legal/compliance decisions, any use-case-specific KYC, and their own anonymizer contracts (review, audit, deployment, maintenance).

## Fees — set expectations

Current wallet flows sponsor gas fees but not pool fees; shielded-token fee payment and paymaster-based fee estimation are still being designed. Don't promise a specific fee UX in plans — flag it as an item to re-check at build time.

## Performance claims — don't make them

Proving performance figures floating around are machine-dependent engineering data from server-class hardware. Never quote proving times in plans, and never as laptop/phone numbers. If asked, say proving is handled by hosted services in wallet flows and is machine-dependent when self-hosted.
