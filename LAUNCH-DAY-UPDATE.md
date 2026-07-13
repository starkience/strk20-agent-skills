# Launch-day update checklist — Wednesday Jul 8, 2026

> **✅ COMPLETED 2026-07-13** (shipped as v0.2.0). Kept for provenance. Findings from step 0 are recorded inline below.

The Privacy SDK monorepo open-sources on Wed Jul 8, 2026. This checklist flips every "coming soon / verify before citing" status in the skill to live links. Statuses are deliberately duplicated across files (rules sit at point of use), so **every** item below must be done or the copies diverge.

## 0. Verify before editing anything

- [x] Confirm `https://github.com/starkware-libs/starknet-privacy` is actually public (fetch it unauthenticated). → **Public: HTTP 200 unauthenticated on `blob/main/sdk/README.md` (2026-07-13).**
- [x] Record the real paths in the public repo: SDK quickstart (expected `sdk/README.md`), anonymizer packages (expected `packages/ekubo_swap_anonymizer`, `packages/vesu_lending_anonymizer`), demo app. → **All confirmed via fresh pull; also present: `packages/sub_account_anonymizer` (Branch D contract side, not builder-facing); no `packages/escrow`. SDK package `@starkware-libs/starknet-privacy-sdk` v0.14.3-rc.x, Node ≥ 24 (from `sdk/package.json`).**
- [x] Check npm: current `starknet` version (still 10.4.x?), get-starknet v6 still on `next` tag or promoted to `latest`? → **`starknet` `latest`=10.0.2, `next`=10.5.0 (10.5.0 adds only `Contract.compile()`, nothing STRK20 — 10.4.0 stays the pinned minimum for `WalletAccountV6`). get-starknet 6.0.2 still on `next` (`latest`=5.0.0-beta.0) — explicit pins still required. types-js latest = 0.10.3.**
- [x] Check Xverse dapp-facing Wallet API status and whether Ready is still the canonical test wallet. → **No new public signal found; kept as "in progress" / Ready remains the canonical test wallet, with as-of date refreshed.**

## 1. `skills/strk20-privacy-integration/references/links.md`

- [x] Move the SDK monorepo row from "Coming soon" to "Live now" with the verified URL; add the quickstart path.
- [x] Move anonymizer reference examples to "Live now" with verified package paths.
- [x] Update the Xverse row per finding in step 0.
- [x] Update the "Statuses as of…" preamble line.

## 2. `skills/strk20-privacy-integration/SKILL.md`

- [x] Route table: Branch B status — anonymizer examples now public (update "coming soon — design now, build when public").
- [x] Route table: Branch A status — SDK repo now public (update "coming soon").
- [x] Accuracy rules: rewrite the "check whether github.com/starkware-libs/starknet-privacy is public yet" bullet — it is public; keep the general "statuses drift, verify versions" instruction.

## 3. `skills/strk20-privacy-integration/references/anonymizer-route.md`

- [x] "Status and references": replace expectations with verified package paths + link to the repo quickstart.
- [x] Merge "What the team can do today" / "When the SDK repo is public" phases — the entry criterion is now met.

## 4. `skills/strk20-privacy-integration/references/sdk-route.md`

- [x] "Status and what to do today": repo is public — add clone/install pointers from the repo's own README (don't restate; link).
- [x] Keep sub-accounts marked coming-soon unless the launch says otherwise (verify).

## 5. `skills/strk20-privacy-integration/README.md` + root `README.md`

- [x] Update the status paragraphs (pre-launch → post-launch).

## 6. Ship

- [x] Note the update in the root README or a CHANGELOG (v0.2.0 — post-launch statuses).
- [x] Commit and push.
- [x] Sanity-check install: `npx skills add starkience/strk20-agent-skills` in a scratch project. → **OK 2026-07-13: installs, and the installed `links.md` carries the post-launch quickstart link.**

## Wording rules that do NOT change on launch day

Selective-disclosure framing, screening-not-bypassable, not-zero-code-DeFi, no proving benchmarks, no protocol attributions, dapps-never-touch-viewing-keys. These are permanent.
