# Launch-day update checklist — Wednesday Jul 8, 2026

The Privacy SDK monorepo open-sources on Wed Jul 8, 2026. This checklist flips every "coming soon / verify before citing" status in the skill to live links. Statuses are deliberately duplicated across files (rules sit at point of use), so **every** item below must be done or the copies diverge.

## 0. Verify before editing anything

- [ ] Confirm `https://github.com/starkware-libs/starknet-privacy` is actually public (fetch it unauthenticated).
- [ ] Record the real paths in the public repo: SDK quickstart (expected `sdk/README.md`), anonymizer packages (expected `packages/ekubo_swap_anonymizer`, `packages/vesu_lending_anonymizer`), demo app.
- [ ] Check npm: current `starknet` version (still 10.4.x?), get-starknet v6 still on `next` tag or promoted to `latest`?
- [ ] Check Xverse dapp-facing Wallet API status and whether Ready is still the canonical test wallet.

## 1. `skills/strk20-privacy-integration/references/links.md`

- [ ] Move the SDK monorepo row from "Coming soon" to "Live now" with the verified URL; add the quickstart path.
- [ ] Move anonymizer reference examples to "Live now" with verified package paths.
- [ ] Update the Xverse row per finding in step 0.
- [ ] Update the "Statuses as of…" preamble line.

## 2. `skills/strk20-privacy-integration/SKILL.md`

- [ ] Route table: Branch B status — anonymizer examples now public (update "coming soon — design now, build when public").
- [ ] Route table: Branch A status — SDK repo now public (update "coming soon").
- [ ] Accuracy rules: rewrite the "check whether github.com/starkware-libs/starknet-privacy is public yet" bullet — it is public; keep the general "statuses drift, verify versions" instruction.

## 3. `skills/strk20-privacy-integration/references/anonymizer-route.md`

- [ ] "Status and references": replace expectations with verified package paths + link to the repo quickstart.
- [ ] Merge "What the team can do today" / "When the SDK repo is public" phases — the entry criterion is now met.

## 4. `skills/strk20-privacy-integration/references/sdk-route.md`

- [ ] "Status and what to do today": repo is public — add clone/install pointers from the repo's own README (don't restate; link).
- [ ] Keep sub-accounts marked coming-soon unless the launch says otherwise (verify).

## 5. `skills/strk20-privacy-integration/README.md` + root `README.md`

- [ ] Update the status paragraphs (pre-launch → post-launch).

## 6. Ship

- [ ] Note the update in the root README or a CHANGELOG (v0.2.0 — post-launch statuses).
- [ ] Commit and push.
- [ ] Sanity-check install: `npx skills add starkience/strk20-agent-skills` in a scratch project.

## Wording rules that do NOT change on launch day

Selective-disclosure framing, screening-not-bypassable, not-zero-code-DeFi, no proving benchmarks, no protocol attributions, dapps-never-touch-viewing-keys. These are permanent.
