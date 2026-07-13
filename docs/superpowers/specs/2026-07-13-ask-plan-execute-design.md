# Design: Ask & Plan & Execute (skill v0.3.0)

Date: 2026-07-13. Status: awaiting approval.
Applies to: `skills/strk20-privacy-integration`.
Baseline: v0.2.0 (post-launch statuses — the SDK monorepo is public and treated as an official resource).

## 1. Goal

Extend the `strk20-privacy-integration` skill from **Ask & Plan** to **Ask & Plan & Execute**: after the developer approves `STRK20_INTEGRATION_PLAN.md`, the same skill executes the plan phase by phase — app code only — with a hard stop and a manual verification handoff at every phase boundary. Additionally, every chat output of the skill (interview, plan walkthrough, execute handoffs) drops deep links to https://strk20-by-example.org/ wherever the topic has a matching page.

## 2. Decisions already made (interview outcomes)

| Question | Decision |
|---|---|
| Execution scope | **App code, not Cairo.** Execute installs packages, wires get-starknet/starknet.js, builds shield/transfer/unshield flows, and may write SDK TypeScript for Branch A teams. It never generates or edits anonymizer Cairo contracts — those stay design-on-paper guidance plus pointers to the public reference packages. |
| Packaging | **Extend the one skill.** A new Step 5 in `SKILL.md`, gated on plan approval. The plan file is the contract between halves; a returning developer with an existing plan skips straight to Execute after a staleness re-check. |
| Verification model | **Phase gates + handoff.** Per phase: headless verification (build/typecheck/tests) must pass, then the developer gets a short manual checklist (Ready wallet, testnet shield, wallet test dapp) and Execute stops until they confirm. |

## 3. Step 5 — Execute (added to `SKILL.md`)

### Entry conditions

- `STRK20_INTEGRATION_PLAN.md` exists in the repo root **and** the developer has explicitly approved it or asked to execute. Never roll from Step 4 into Step 5 without that go-ahead.
- Returning session with an existing plan: re-verify staleness first (npm versions/dist-tags, monorepo paths, wallet statuses) and surface any drift before building.

### Per-phase loop

1. **Re-verify before building**: fetch the WalletAccount guide for the current API (never guess method names); confirm npm versions; pull/check the SDK monorepo if the phase touches it.
2. **Make the phase's changes** exactly as scoped in the plan, following the repo's existing conventions (imports, error handling, state management, test layout).
3. **Verify headlessly**: package install clean, build/typecheck passes, existing tests pass; add tests where the repo already has a test setup.
4. **Mark progress in the plan file**: phase status + date (e.g. `## 5. Phase 1 — first shielded flow ✅ done 2026-07-14`), so the plan stays the single source of truth across sessions.
5. **Hand off and stop**: give the developer the phase's manual verification checklist (from `references/execute.md`) with relevant strk20-by-example.org links, and wait for their confirmation before starting the next phase.

### Guardrails (mandatory, restated at point of use)

- **No Cairo, ever.** If a plan phase requires an anonymizer contract, Execute produces design guidance and points to the reference packages (`packages/ekubo_swap_anonymizer`, `packages/vesu_lending_anonymizer`) — the team writes, reviews, audits, deploys, and maintains its own contract.
- **No key material.** Never write viewing keys, private keys, or secrets into files; use env-var placeholders. If an execution step appears to need a user's viewing key, the route is wrong — go back to Step 3, do not improvise.
- **Testnet by default.** Any mainnet-affecting change (config, deployment target, funded flows) requires the developer's explicit confirmation in that moment.
- **Graceful degradation is mandatory**, not optional: Phase 1 must include the no-privacy-wallet detection path (wallet scope is Ready now, Xverse in progress).
- **UX copy rules carry over**: any labels/tooltips/empty-states written during Execute obey the hidden-vs-visible honesty rules in `references/concepts.md` and the wording rules (no compliance framing, no screening-workaround framing).

### Messaging change in Step 2

The Step 2 "what-happens-next" message changes from "I won't modify your app, install packages, or build any contracts" to: nothing changes until you approve the plan; after approval I execute it phase by phase — app code only; anonymizer contracts remain your team's own code to build and audit.

## 4. New file: `references/execute.md`

Holds the execution depth so `SKILL.md` stays lean:

- **The per-phase loop** in full (the SKILL.md Step 5 section summarizes and points here).
- **Headless verification guidance per stack**: npm/pnpm/yarn detection, build/typecheck/test commands for Vite/Next/plain-TS repos; what "clean" means before a handoff.
- **Manual checklist templates per route**:
  - Wallet API route: connect via Ready → capability detection shows privacy support → testnet shield → private transfer → unshield → cross-check behavior against the wallet test dapp.
  - SDK route (Branch A): env/key custody sanity check (no secrets in repo), register → shield → private transfer on testnet, discovery/balance readback.
- **Plan-file status conventions**: how phases are marked done/blocked, where drift notes go.
- **Failure protocol**: build/test failure → fix within the phase; external blocker (wallet unavailable, screening decline, missing testnet funds) → record in the plan under Open items, stop, hand back to the developer.

## 5. strk20-by-example.org deep links

**Rule (one paragraph in `SKILL.md`, applies to Ask, Plan, and Execute):** whenever chat output to the developer discusses a topic that has a page on https://strk20-by-example.org/, include that page's URL in the message. Link on first mention per conversation stage, don't spam every repeat.

**Topic → URL map (new section in `references/links.md`)** — routes verified from the site bundle 2026-07-13:

| When you talk about… | Link |
|---|---|
| What STRK20 is / the pool model | https://strk20-by-example.org/what-is-strk20 |
| Notes, nullifiers, UTXO model | https://strk20-by-example.org/notes-and-nullifiers |
| Viewing keys / encryption | https://strk20-by-example.org/viewing-keys |
| Channels & subchannels | https://strk20-by-example.org/channels-and-subchannels |
| Actions, phases, proofs | https://strk20-by-example.org/actions-and-proofs |
| Compliance, screening, selective disclosure | https://strk20-by-example.org/compliance |
| Builder-facing privacy overview | https://strk20-by-example.org/builder-privacy-overview |
| Wallet API route (Branch C) generally | https://strk20-by-example.org/starknet-wallet-api/overview |
| starknet.js wiring / WalletAccountV6 | https://strk20-by-example.org/starknet-wallet-api/starknet-js |
| React apps / hooks (`useStrk20`) | https://strk20-by-example.org/starknet-wallet-api/starknet-start-hook |
| SDK route (Branch A) getting started | https://strk20-by-example.org/sdk/getting-started |
| SDK setup requirements | https://strk20-by-example.org/sdk/setup-requirements |
| SDK per-operation pages (register, deposit, transfer, withdraw, surplus, batches, note discovery, discovery providers, proving config) | `https://strk20-by-example.org/sdk/<operation>` (same slugs) |
| Anonymizer anatomy / `privacy_invoke` (Branch B) | https://strk20-by-example.org/helpers/privacy-invoke |
| Swap anonymizer example | https://strk20-by-example.org/helpers/swap-helper |
| Lending/vault anonymizer example | https://strk20-by-example.org/helpers/vesu-lending-helper |

**Exclusion:** never cite https://strk20-by-example.org/helpers/escrow in plans or chat — the site's escrow page references a `packages/escrow` that does not exist in the SDK monorepo (verified absent 2026-07-13). Re-check on future runs; lift the exclusion only when the repo ships it.

**Plan template:** the Links section of `STRK20_INTEGRATION_PLAN.md` includes the by-example subset relevant to the chosen route.

## 6. File-by-file changes (v0.3.0)

| File | Change |
|---|---|
| `SKILL.md` | Frontmatter description: "ask-and-plan" → "ask, plan, and execute" (+ execute trigger phrases). Intro/loop: add step 5. New Step 5 — Execute section (entry conditions, loop summary, guardrails). Step 2 messaging change. Deep-link rule paragraph. |
| `references/execute.md` | New (section 4 above). |
| `references/links.md` | New "strk20-by-example.org deep links" section (topic→URL map + escrow exclusion). |
| `references/plan-template.md` | Closing instruction: after plan approval, Execute begins (replaces "stop — do not start implementing"); phase-status conventions note; by-example links in the Links section. |
| `references/wallet-api-route.md` / `references/sdk-route.md` | One line each pointing to their manual checklist in `execute.md` and their by-example section links. |
| `skills/strk20-privacy-integration/README.md` + root `README.md` | Describe Ask & Plan & Execute; changelog entry v0.3.0. |

## 7. Out of scope (unchanged from roadmap)

EVM wallet support; Polymarket-type app flows; frontend privacy-UX design guidance; generating/editing any Cairo; sub-accounts execution (still not builder-facing).

## 8. Acceptance

- A fresh run in a scratch Starknet dapp: scan → interview → plan → approval → Phase 1 executes, headless checks pass, agent stops with a manual checklist containing by-example links.
- A run with a pre-existing approved plan skips to Execute after a staleness check.
- A DeFi-route run produces guidance + reference-package pointers for the anonymizer, and writes no `.cairo` file.
- Chat transcripts show by-example links at concept/route/handoff moments; no escrow link anywhere.
- Install sanity-check (`npx skills add starkience/strk20-agent-skills`) in a scratch project.

## 9. Shipping

Single commit, tagged in the changelog as **v0.3.0 — Ask & Plan & Execute + strk20-by-example.org deep links**, then push and install sanity-check. (v0.2.0 already shipped separately.)
