# Template — STRK20_INTEGRATION_PLAN.md

Write the plan to the repo root using this structure. Every `<>` gets filled from the scan and interview — if you can't fill a section specifically, the interview isn't done. Delete sections that don't apply to the chosen route.

```markdown
# STRK20 Privacy Integration Plan — <project name>

Generated <date> by the strk20-privacy-integration skill. Statuses below were current at generation time — re-verify the "coming soon" items before building against them.

## 1. Project snapshot

- Stack: <frontend framework, starknet.js version, get-starknet/starknet-react/etc., Cairo contracts y/n, backend + SDK, test tooling>
- Relevant code: <file:line for wallet connection, transaction layer, DeFi contracts/integrations>
- Privacy goal (from interview): <precise statement — e.g. "hide who pays whom in P2P transfers; balances private; vault deposits private later">
- Environment: <mainnet/testnet, wallets users hold>

## 2. Chosen route: <route name>

<2–4 sentences: which route and why, tied to the builder type and goal. If mixed (e.g. Wallet API now + anonymizer later), say so.>

**The rule this follows:** this app <never touches viewing keys — the user's wallet acts on its behalf via starknet.js / owns its accounts and keys, so it can use the SDK directly>.

## 3. What this delivers — hidden vs visible

| Private | Public |
|---|---|
| <adapted from concepts.md to this app's flows> | <deposit/withdrawal amounts, fact+timing of pool interaction, and any route-specific visibility> |

<One honest sentence on limits, e.g.: "Vault deposits will hide the depositor's address; amounts and the vault activity itself remain public.">

## 4. Prerequisites & versions

- `starknet@10.4.0` <(upgrade from <current> — migration task, see Phase 1)>
- `@starknet-io/get-starknet-discovery@6.0.2`, `@starknet-io/get-starknet-wallet-standard@6.0.2` (npm `next` tag — pin explicitly)
- `@starknet-io/types-js@0.10.3`
- Test wallet: Ready extension
- <Cairo toolchain versions if Branch B>

## 5. Phase 1 — first shielded flow (buildable now)

1. <Upgrade/install steps, naming the actual files that change>
2. <Connect via get-starknet v6 in <file> — per the WalletAccount guide (link below)>
3. <Wire shield / private transfer / unshield into <file/component>>
4. <Graceful degradation for wallets without privacy support>
5. Verify against the Ready extension + wallet test dapp.

## 6. Phase 2 — feature integration

<App-specific: which screens/flows/endpoints adopt private actions, UX labeling of private vs public, fee-UX re-check item.>

## 7. Phase 3 — <anonymizer contract / SDK adoption / sub-accounts> (tracked, with entry criteria)

- Entry criterion: <e.g. "sub-accounts become builder-facing (wallet + SDK + Wallet API support)" or "Xverse dapp-facing Wallet API ships" — the SDK monorepo itself is already public>
- <Design-now items already done on paper; build steps once the criterion is met>
- <Branch B: audit step — owner, before mainnet, non-negotiable>

## 8. Testing

<Testnet-first sequence; Ready extension; wallet test dapp; Branch B: snforge tests + atomic rollback tests; note that pure-local devnet doesn't exercise the wallet/proving path today.>

## 9. Compliance & security notes

- Deposit screening is enforced onchain by the protocol; it applies on every route.
- Selective disclosure exists for legitimate regulatory requests; it is not automatic compliance and carries no regulator endorsement — the team owns its own legal/compliance decisions and any use-case KYC.
- <Branch B: the team owns review, audit, deployment, maintenance of its anonymizer contract.>

## 10. Open items to re-verify at build time

- <Xverse status? sub-accounts builder-facing yet? wrapper-library compat? fee/paymaster design? current `starknet` dist-tags?>

## 11. Links

<Relevant subset of references/links.md, with versions.>
```

After writing the file, summarize it to the developer in chat: the route, the one-sentence why, and the very next action they can take today. Then stop — do not start implementing, installing packages, or generating contracts. Offer to begin Phase 1 and wait for their go-ahead.
