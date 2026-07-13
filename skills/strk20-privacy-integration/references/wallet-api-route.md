# Route: Privacy Wallet API via starknet.js (normal dapps) — buildable now

The standard route for any dapp that relies on the user's wallet. The dapp asks the user's privacy-enabled wallet to perform the private action; the wallet handles keys, notes, proving, and the pool. The dapp never touches the viewing key or the SDK.

## How the pieces fit

```
your dapp
  └─ get-starknet v6.0.2          (wallet discovery + connection)
      └─ starknet.js v10.4.0      (WalletAccountV6 — adds STRK20 actions)
          └─ Privacy Wallet API   (spec v0.10.3 — dapp↔wallet plumbing; you don't implement it)
              └─ privacy-enabled wallet (runs the Privacy SDK internally)
                  └─ STRK20 pool contract (mainnet)
```

Pitch it to the team as "use starknet.js" — most dapps never need to know the Wallet API exists. It matters only as the reason a privacy-enabled wallet is required.

## What the dapp can ask the wallet to do

- **Shield** (deposit an ERC-20 into the pool)
- **Private transfer** (shielded, between registered users — wallets handle registration automatically)
- **Unshield** (withdraw back to a public balance)
- **Swap**, where the wallet supports it

Anything protocol-specific beyond these (lend, stake, LP, vault deposits) additionally needs an anonymizer contract — see `references/anonymizer-route.md`.

What the dapp **cannot** do: hold the viewing key, manage notes, or generate proofs. It **can** request wallet-mediated reads: `WalletAccountV6.strk20Balances(tokens)` returns shielded balances through the wallet, so balance UX is possible without the app ever seeing a key — confirm the wallet's consent behavior against the Ready extension before designing around it.

## Integration recipe

1. **Install pinned versions** (v6.x is on the npm `next` tag — explicit versions or you get the wrong major):

   ```sh
   npm install @starknet-io/get-starknet-discovery@6.0.2 \
               @starknet-io/get-starknet-wallet-standard@6.0.2 \
               @starknet-io/types-js@0.10.3 \
               starknet@10.4.0
   ```

2. **Connect** with get-starknet v6 and construct a `WalletAccountV6` from the connected wallet. **Fetch the WalletAccount guide for the exact current API before writing code** — do not guess method names: https://starknet-js.com/docs/next/guides/account/walletAccount/#with-get-starknet-v6

3. **Wire the STRK20 actions** (shield / private transfer / unshield / swap) into the app's transaction layer where the plan identified plug-in points.

4. **Handle the no-privacy-wallet case.** Wallet scope is **Ready and Xverse only**: test against the Ready extension today; Xverse's dapp-facing Wallet API is in progress. Braavos, Privy, and other wallets or embedded-wallet providers are not prepared for STRK20 — treat them as unsupported and don't plan around them. The dapp should detect a wallet without privacy support and degrade gracefully (hide private actions or prompt for a supported wallet) — check the guide for the capability-detection mechanism.

5. **Test** against the Ready extension and sanity-check behavior against the wallet test dapp: https://starknet-wallet-account.vercel.app/

## Plug-in points to name in the plan

From the repo scan, identify and name:

- The **wallet-connection module** (where `connect()` lives — get-starknet upgrade lands here). For starknet-react/starknetkit apps this is the provider/kit config; verify the wrapper's compatibility with starknet.js v10.4.0 first (see `references/starknet-dev-context.md`).
- The **transaction layer** (where the app builds and sends calls — `WalletAccountV6` actions land here).
- The **UI surfaces** that gain shield/unshield/private-send affordances, plus honest labeling of what's private vs public per `references/concepts.md`.

## Integration gotchas (verified 2026-07-13 against `starknet@10.4.0` + get-starknet 6.0.2)

- Import the wallet type from the subpath: `import type { WalletWithStarknetFeatures } from "@starknet-io/get-starknet-wallet-standard/features"` — the package root declares it locally but does not export it (TS2459).
- The wallet-standard feature version (`starknet:walletApi` → `"1.0.0"`) does **not** flag STRK20 support. Detect capability at runtime: probe a read-only call (`strk20Balances([])` in try/catch) or check `wallet_supportedSpecs`.
- `strk20InvokeTransaction(actions)` takes an **array** of actions — several private transfers batch into one wallet request (multicall-style settlements work).

## Gotchas for the plan

- Upgrading an older starknet.js pin to v10.4.0 is a migration task of its own — breaking changes are likely across majors.
- Fees: wallet flows currently sponsor gas but not pool fees; the fee UX is still being designed. Flag as re-check-at-build-time.
- Deposit/withdrawal amounts stay public (they're the ERC-20 legs) — don't design UX copy that implies otherwise.
- The pool enforces deposit screening onchain — a deposit can be declined by screening; surface that state in UX rather than treating it as an error bug.

## Executing this route (Step 5)

At each phase handoff use the **Wallet API route** manual checklist in `references/execute.md`. By-example pages to link during interview, plan walkthrough, and handoffs: https://strk20-by-example.org/starknet-wallet-api/overview, https://strk20-by-example.org/starknet-wallet-api/starknet-js, and for React apps https://strk20-by-example.org/starknet-wallet-api/starknet-start-hook.
