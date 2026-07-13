# Ask & Plan & Execute (v0.3.0) Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Extend `skills/strk20-privacy-integration` from Ask & Plan to Ask & Plan & Execute (new Step 5, phase-gated, app-code-only) and add strk20-by-example.org deep links to all chat output, per the approved spec at `docs/superpowers/specs/2026-07-13-ask-plan-execute-design.md`.

**Architecture:** All changes are markdown inside one skill folder plus the two READMEs. `SKILL.md` gains a lean Step 5 that points to a new `references/execute.md` playbook (loop detail, per-stack verification, manual checklists, failure protocol). `references/links.md` gains the topic→URL deep-link map. Verification is grep/curl-based plus a final skills-CLI install check.

**Tech Stack:** Markdown, git, `npx skills` CLI. Repo: `/Users/starkience/Documents/SuperPrivateAgent/strk20-agent-skills` (git repo, branch `main`, pushes to `github.com/starkience/strk20-agent-skills`).

## Global Constraints

- **App code only, never Cairo**: Execute must never generate or edit anonymizer Cairo contracts; reference packages are `packages/ekubo_swap_anonymizer`, `packages/vesu_lending_anonymizer`.
- **No key material in files**: viewing keys/private keys only as env-var placeholders.
- **Testnet by default**; mainnet-affecting changes need explicit developer confirmation.
- **Escrow exclusion**: never cite `https://strk20-by-example.org/helpers/escrow` (repo has no `packages/escrow`, verified 2026-07-13).
- **Wording rules unchanged**: selective disclosure, screening-not-bypassable, not-zero-code-DeFi, no proving benchmarks, dapps-never-touch-viewing-keys.
- Canonical SDK quickstart link: `https://github.com/starkware-libs/starknet-privacy/blob/main/sdk/README.md`.
- Run all commands from the repo root `/Users/starkience/Documents/SuperPrivateAgent/strk20-agent-skills`.
- Ship as commits on `main`; the changelog entry (Task 6) marks the v0.3.0 release; push happens in Task 7.

---

### Task 1: Create `references/execute.md`

**Files:**
- Create: `skills/strk20-privacy-integration/references/execute.md`

**Interfaces:**
- Produces: the file that Task 3's Step 5 section, Task 4's plan-template closing, and Task 5's route pointers reference by the exact path `references/execute.md`; section headings "Manual checklist templates" and "Plan-file conventions" are referenced by name in later tasks' text.

- [ ] **Step 1: Write the file with exactly this content**

````markdown
# Execute — the phase-by-phase build loop (Step 5)

You enter this file only after the developer has approved `STRK20_INTEGRATION_PLAN.md`. Execute one phase at a time; between phases the developer verifies manually — you cannot: shield/transfer/unshield need their privacy-enabled wallet and a funded account.

## The loop (every phase)

1. **Re-verify before building.**
   - Fetch the WalletAccount guide (URL in `references/links.md`) for the current API — never guess method names.
   - Check pins: `npm view starknet dist-tags`, `npm view @starknet-io/get-starknet-discovery dist-tags` — do the plan's versions still hold?
   - If the phase touches the SDK or anonymizer examples: confirm the monorepo paths still exist (https://github.com/starkware-libs/starknet-privacy).
   - If anything drifted, update the plan's §4 Prerequisites first and tell the developer.
2. **Make the changes** exactly as scoped for this phase. Follow the repo's conventions: its package manager, module style, state management, error handling, and test layout. Don't refactor unrelated code.
3. **Verify headlessly** (per-stack table below). Everything must pass before handoff.
4. **Mark the phase in the plan** (conventions below) and record anything deferred or drifted under "Open items".
5. **Hand off and stop.** Post the phase's manual checklist with the matching strk20-by-example.org links (map in `references/links.md`), and wait for the developer's confirmation. Do not start the next phase without it.

## Headless verification per stack

Detect the package manager from the lockfile (`package-lock.json` → npm, `pnpm-lock.yaml` → pnpm, `yarn.lock` → yarn) and use it consistently.

| Repo shape | Clean means |
|---|---|
| Vite / Next / CRA frontend | install exits 0 · typecheck passes (`tsc --noEmit` or the repo's script) · production build passes · existing test script passes |
| Plain TS/Node backend | install exits 0 · typecheck passes · the repo's test command passes |
| Monorepo | the above, scoped to the affected workspace(s) |

Add tests only where the repo already has a test setup — match its framework and file layout; never introduce a new test framework during an integration.

## Manual checklist templates

Hand over only the items the phase actually built; adapt names to their app.

**Wallet API route (Branch C):**

- [ ] Connect with the Ready extension — the app discovers it via get-starknet v6.
- [ ] Capability check: privacy actions appear with Ready and degrade gracefully with a non-privacy wallet.
- [ ] Testnet shield: deposit a small amount into the pool. (A screening decline is a protocol outcome to surface in UX, not an app bug.)
- [ ] Private transfer to a second registered account.
- [ ] Unshield back to a public balance.
- [ ] Cross-check anything odd against the wallet test dapp (URL in `references/links.md`).

**SDK route (Branch A):**

- [ ] No secrets in the diff: viewing key / private key only via env vars.
- [ ] Register a viewing key on testnet.
- [ ] Shield, then private transfer between the service's own accounts.
- [ ] Discovery/balance readback returns the expected notes.

## Failure protocol

- **Headless check fails** → fix within the phase; the handoff message only goes out clean.
- **External blocker** (wallet unavailable, screening decline, missing testnet funds, upstream API change) → record it in the plan under "Open items to re-verify at build time", stop, and hand back to the developer with what you found.
- **A step seems to need a user's viewing key in the dapp** → the route is wrong; stop and re-run Step 3 of `SKILL.md` — do not improvise.

## Plan-file conventions

- Phase done: append to the phase heading — `## 5. Phase 1 — first shielded flow ✅ done 2026-07-14`
- Phase blocked: `⛔ blocked <date>: <one-line reason>` on the heading; details under Open items.
- Version drift found during re-verify: update §4 Prerequisites and note the change under Open items.
````

- [ ] **Step 2: Verify the file landed**

Run: `grep -c "^## " skills/strk20-privacy-integration/references/execute.md`
Expected: `5` (five H2 sections)

Run: `grep -n "escrow" skills/strk20-privacy-integration/references/execute.md`
Expected: no output (exit 1) — execute.md never mentions escrow.

- [ ] **Step 3: Commit**

```bash
git add skills/strk20-privacy-integration/references/execute.md
git commit -m "v0.3.0: add references/execute.md — phase-gated execution playbook"
```

---

### Task 2: Deep-link map in `references/links.md`

**Files:**
- Modify: `skills/strk20-privacy-integration/references/links.md` (append a new section before `## Support`)

**Interfaces:**
- Produces: section heading `## strk20-by-example.org deep links` — referenced by that name from SKILL.md (Task 3) and the route files (Task 5).

- [ ] **Step 1: Insert this section immediately before the `## Support` heading**

```markdown
## strk20-by-example.org deep links

https://strk20-by-example.org/ is the official by-example tutorial site. Whenever chat output to the developer discusses one of these topics, include the matching URL in the message (first mention per conversation stage; don't repeat on every mention). Routes verified against the site 2026-07-13.

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
```

- [ ] **Step 2: Verify**

Run: `grep -c "strk20-by-example.org" skills/strk20-privacy-integration/references/links.md`
Expected: `20` (1 intro + 17 table URLs (16 rows, one row has a pattern URL) + 1 exclusion + 1 heading mention = count must be ≥ 18; exact count 20 if matching this content verbatim)

Run: `grep -n "helpers/escrow" skills/strk20-privacy-integration/references/links.md`
Expected: exactly one line — the exclusion.

- [ ] **Step 3: Commit**

```bash
git add skills/strk20-privacy-integration/references/links.md
git commit -m "v0.3.0: strk20-by-example.org deep-link map (with escrow exclusion)"
```

---

### Task 3: `SKILL.md` — description, loop, linking rule, Step 2 message, Step 5

**Files:**
- Modify: `skills/strk20-privacy-integration/SKILL.md` (frontmatter line 3; intro ~line 8; loop list ~lines 12–17; after the "Before starting…" paragraph ~line 19; Step 2 closing ~line 62; new section after Step 4)

**Interfaces:**
- Consumes: `references/execute.md` (Task 1), `## strk20-by-example.org deep links` in links.md (Task 2).
- Produces: section heading `## Step 5 — Execute (only after the plan is approved)` — referenced by plan-template.md (Task 4).

- [ ] **Step 1: Replace the frontmatter description**

Old:
```
description: Ask-and-plan skill for adding STRK20 privacy to a Starknet project. Use when a developer wants to add privacy to the Starknet app open in this session — private/shielded transfers, confidential balances, private payments, private swaps or DeFi, hiding user↔account links — or mentions STRK20, privacy pool, shielding, viewing keys, or the Privacy Wallet API. Inspects the open repo, interviews the developer, picks the right integration route, and writes a repo-specific integration plan.
```

New:
```
description: Ask, plan & execute skill for adding STRK20 privacy to a Starknet project. Use when a developer wants to add privacy to the Starknet app open in this session — private/shielded transfers, confidential balances, private payments, private swaps or DeFi, hiding user↔account links — mentions STRK20, privacy pool, shielding, viewing keys, or the Privacy Wallet API, or wants an existing STRK20_INTEGRATION_PLAN.md executed. Inspects the open repo, interviews the developer, picks the right integration route, writes a repo-specific integration plan, and — after the developer approves it — executes the plan phase by phase (app code only, never Cairo contracts).
```

- [ ] **Step 2: Replace the intro paragraph**

Old:
```
This is an **ask-and-plan** skill. You are running inside the developer's own project. Your deliverable is not code — it is a conversation that converges on the right integration route, followed by a concrete, repo-specific plan written to `STRK20_INTEGRATION_PLAN.md`. Everything you ask and everything you plan should be grounded in what is actually in the open repo.
```

New:
```
This is an **ask, plan & execute** skill. You are running inside the developer's own project. Your first deliverable is a conversation that converges on the right integration route and a concrete, repo-specific plan written to `STRK20_INTEGRATION_PLAN.md`; code changes begin only after the developer approves that plan (Step 5). Everything you ask, plan, and build should be grounded in what is actually in the open repo.
```

- [ ] **Step 3: Extend the loop list**

Old:
```
4. **Plan** — write `STRK20_INTEGRATION_PLAN.md`, naming their real files and modules.
```

New:
```
4. **Plan** — write `STRK20_INTEGRATION_PLAN.md`, naming their real files and modules.
5. **Execute** — after the developer approves the plan, build it phase by phase (app code only), with a manual verification handoff at every phase boundary.
```

- [ ] **Step 4: Add the linking rule after the "Before starting…" paragraph**

Insert as a new paragraph directly after the paragraph that begins `Before starting, read `references/concepts.md``:

```markdown
**Linking rule (all steps):** https://strk20-by-example.org/ is the official by-example tutorial site. Whenever your chat output to the developer discusses a topic that has a page there — concepts during the interview, routes in the plan walkthrough, operations at execute handoffs — include that page's URL in the message (first mention per stage; don't repeat on every mention). The topic→URL map is the "strk20-by-example.org deep links" section of `references/links.md`. Never cite `/helpers/escrow` (see the exclusion note there).
```

- [ ] **Step 5: Replace the Step 2 closing message**

Old:
```
**Close the interview with a what-happens-next message.** Before writing anything, tell the developer explicitly what comes next, e.g.: "Thanks — I have what I need. Next I'll write `STRK20_INTEGRATION_PLAN.md` into your repo root. That's a plan, not code: I won't modify your app, install packages, or build any contracts (an anonymizer contract in particular is your team's own code to build, review, and audit — this skill never generates it for you). Review the plan, and when you're ready we start with Phase 1." Never jump from the questionnaire straight into implementation.
```

New:
```
**Close the interview with a what-happens-next message.** Before writing anything, tell the developer explicitly what comes next, e.g.: "Thanks — I have what I need. Next I'll write `STRK20_INTEGRATION_PLAN.md` into your repo root. Nothing in your app changes until you review and approve that plan. Once you approve it, I execute it phase by phase — app code only: I can install packages and wire your frontend or backend, but an anonymizer contract is your team's own code to build, review, and audit — this skill never generates it for you. Each phase ends with a short manual check you run with your wallet before we continue." Never jump from the questionnaire straight into implementation.
```

- [ ] **Step 6: Add Step 5 section between "## Step 4 — Plan" content and "## Accuracy rules"**

```markdown
## Step 5 — Execute (only after the plan is approved)

Execution starts only when the developer has reviewed `STRK20_INTEGRATION_PLAN.md` and explicitly approved it (or asks you to execute an existing plan). Never roll from Step 4 into Step 5 on your own. When resuming a session with an existing plan, re-verify staleness first — npm versions/dist-tags, monorepo paths, wallet statuses per `references/links.md` — and surface any drift before building.

Read `references/execute.md` before the first phase — it holds the full loop, per-stack verification commands, the manual checklist templates, and the failure protocol. The shape:

1. **Re-verify before building** — fetch the WalletAccount guide for the current API (never guess method names); confirm package versions; check the SDK monorepo if the phase touches it.
2. **Make the phase's changes** exactly as scoped in the plan, following the repo's existing conventions.
3. **Verify headlessly** — clean install, build/typecheck passes, existing tests pass; add tests where the repo already has a test setup.
4. **Mark the phase in the plan file** (status + date) so the plan stays the single source of truth across sessions.
5. **Hand off and stop** — give the developer the phase's manual verification checklist, with the matching strk20-by-example.org links, and wait for confirmation before the next phase.

**Execution guardrails (non-negotiable):**

- **App code only — never Cairo.** If a phase calls for an anonymizer contract, deliver design guidance and point at the public reference packages (`packages/ekubo_swap_anonymizer`, `packages/vesu_lending_anonymizer`); the team writes, reviews, audits, deploys, and maintains its own contract.
- **No key material.** Never write viewing keys, private keys, or other secrets into files — env-var placeholders only. If a step seems to need a user's viewing key, the route is wrong: go back to Step 3.
- **Testnet by default.** Mainnet-affecting changes need the developer's explicit confirmation at that moment.
- **Graceful degradation is part of Phase 1**, not optional — detect wallets without privacy support and degrade per `references/wallet-api-route.md`.
- **UX copy written during execution follows `references/concepts.md`** — honest hidden-vs-visible labeling, no compliance framing, no screening-workaround framing.
```

- [ ] **Step 7: Verify**

Run: `grep -c "Step 5" skills/strk20-privacy-integration/SKILL.md`
Expected: ≥ 4 (intro, loop mention, heading, guardrail back-reference)

Run: `grep -n "ask-and-plan\|ask and plan" skills/strk20-privacy-integration/SKILL.md`
Expected: no output — no stale naming.

Run: `grep -c "strk20-by-example.org" skills/strk20-privacy-integration/SKILL.md`
Expected: ≥ 2 (linking rule + Step 5 handoff mention)

- [ ] **Step 8: Commit**

```bash
git add skills/strk20-privacy-integration/SKILL.md
git commit -m "v0.3.0: SKILL.md — Step 5 Execute, linking rule, updated interview close"
```

---

### Task 4: `references/plan-template.md` — approval gate + status conventions + links

**Files:**
- Modify: `skills/strk20-privacy-integration/references/plan-template.md` (the `## 11. Links` line inside the template; the closing paragraph after the template block)

**Interfaces:**
- Consumes: `## Step 5 — Execute (only after the plan is approved)` in SKILL.md (Task 3); "Plan-file conventions" in execute.md (Task 1).

- [ ] **Step 1: Update the Links section placeholder inside the template**

Old:
```
<Relevant subset of references/links.md, with versions.>
```

New:
```
<Relevant subset of references/links.md, with versions, plus the strk20-by-example.org pages for the chosen route (deep-link map in references/links.md — never the escrow page).>
```

- [ ] **Step 2: Replace the closing paragraph**

Old:
```
After writing the file, summarize it to the developer in chat: the route, the one-sentence why, and the very next action they can take today. Then stop — do not start implementing, installing packages, or generating contracts. Offer to begin Phase 1 and wait for their go-ahead.
```

New:
```
After writing the file, summarize it to the developer in chat: the route, the one-sentence why, and the very next action they can take today — including the strk20-by-example.org links for the chosen route. Then stop — do not start implementing, installing packages, or generating contracts until the developer approves the plan. On approval, move to Step 5 (Execute) of `SKILL.md`: phases run one at a time and are marked off in this file (`✅ done <date>` on the phase heading, drift and blockers under Open items) per the plan-file conventions in `references/execute.md`.
```

- [ ] **Step 3: Verify**

Run: `grep -c "execute.md\|Step 5" skills/strk20-privacy-integration/references/plan-template.md`
Expected: ≥ 2

- [ ] **Step 4: Commit**

```bash
git add skills/strk20-privacy-integration/references/plan-template.md
git commit -m "v0.3.0: plan template — approval gate wording, phase-status conventions, by-example links"
```

---

### Task 5: Route files — execution pointers

**Files:**
- Modify: `skills/strk20-privacy-integration/references/wallet-api-route.md` (append at end)
- Modify: `skills/strk20-privacy-integration/references/sdk-route.md` (append at end of the main route content, before the `## Sub-accounts (Branch D) — coming soon` section)

**Interfaces:**
- Consumes: "Manual checklist templates" in execute.md (Task 1); deep-link map (Task 2).

- [ ] **Step 1: Append to `wallet-api-route.md`**

```markdown
## Executing this route (Step 5)

At each phase handoff use the **Wallet API route** manual checklist in `references/execute.md`. By-example pages to link during interview, plan walkthrough, and handoffs: https://strk20-by-example.org/starknet-wallet-api/overview, https://strk20-by-example.org/starknet-wallet-api/starknet-js, and for React apps https://strk20-by-example.org/starknet-wallet-api/starknet-start-hook.
```

- [ ] **Step 2: Insert into `sdk-route.md` before the Sub-accounts section**

```markdown
## Executing this route (Step 5)

At each phase handoff use the **SDK route** manual checklist in `references/execute.md` (env/key-custody check first — no secrets in the diff, ever). By-example pages to link: https://strk20-by-example.org/sdk/getting-started, https://strk20-by-example.org/sdk/setup-requirements, and the per-operation pages (`/sdk/register`, `/sdk/deposit`, `/sdk/transfer`, `/sdk/withdraw`, `/sdk/proving-config`, …) as each flow comes up.
```

- [ ] **Step 3: Verify**

Run: `grep -l "Executing this route" skills/strk20-privacy-integration/references/*.md`
Expected: exactly `wallet-api-route.md` and `sdk-route.md`.

- [ ] **Step 4: Commit**

```bash
git add skills/strk20-privacy-integration/references/wallet-api-route.md skills/strk20-privacy-integration/references/sdk-route.md
git commit -m "v0.3.0: route files — Step 5 checklist pointers + by-example links"
```

---

### Task 6: READMEs + changelog

**Files:**
- Modify: `skills/strk20-privacy-integration/README.md` (intro line, numbered list, Contents list)
- Modify: `README.md` (skill description paragraph, Changelog section)

- [ ] **Step 1: Skill README intro**

Old: `An ask-and-plan agent skill (Claude Code / Codex-compatible) that kick-starts an STRK20 privacy integration for Starknet projects.`

New: `An ask, plan & execute agent skill (Claude Code / Codex-compatible) that kick-starts — and, once you approve the plan, builds — an STRK20 privacy integration for Starknet projects.`

- [ ] **Step 2: Skill README numbered list — add item 5 after item 4**

```markdown
5. **Executes on your approval** — builds the plan phase by phase (app code only; anonymizer contracts remain your team's own code to write and audit), with headless checks per phase and a short manual wallet check handed to you at every phase boundary. Chat output links the relevant https://strk20-by-example.org/ pages throughout.
```

- [ ] **Step 3: Skill README Contents list — add after the links.md line**

```markdown
- `references/execute.md` — the Step 5 execution playbook (phase loop, verification, manual checklists, failure protocol)
```

- [ ] **Step 4: Root README skill description**

Old paragraph starting `An ask-and-plan skill. Run your agent inside your Starknet project…` — replace the first sentence and extend the end:

New paragraph:
```markdown
An ask, plan & execute skill. Run your agent inside your Starknet project and ask it to "plan STRK20 privacy for this app." It scans your repo (starknet.js, get-starknet, starknet-react, Cairo/Scarb, Starknet Foundry, backend SDKs), interviews you about what exactly should be private, picks the right integration route — Privacy Wallet API via starknet.js (most dapps), anonymizer contract + Wallet API (DeFi protocols), Privacy SDK direct (wallets/backends), or private sub-accounts (tracked) — and writes a phased, versioned `STRK20_INTEGRATION_PLAN.md` that names your actual files. Once you approve the plan, it executes it phase by phase — app code only, never Cairo contracts — with a manual wallet-verification handoff at every phase boundary, linking the matching https://strk20-by-example.org/ tutorial pages as it goes.
```

- [ ] **Step 5: Root README changelog — add at top of the list**

```markdown
- **v0.3.0** (2026-07-13) — Ask & Plan & Execute: new Step 5 executes the approved plan phase by phase (app code only, never Cairo; phase-gate handoffs with manual wallet checks; new `references/execute.md`), plus strk20-by-example.org deep links throughout chat output.
```

Also update the `## Status` line `**v0.2.0** — statuses as of mid-July 2026…` to begin `**v0.3.0** — Ask & Plan & Execute. Statuses as of mid-July 2026…` (keep the rest of the paragraph unchanged).

- [ ] **Step 6: Verify**

Run: `grep -rn "ask-and-plan" README.md skills/`
Expected: no output — stale naming fully gone.

Run: `grep -c "v0.3.0" README.md`
Expected: `2` (status + changelog)

- [ ] **Step 7: Commit**

```bash
git add README.md skills/strk20-privacy-integration/README.md
git commit -m "v0.3.0: READMEs — Ask & Plan & Execute description + changelog"
```

---

### Task 7: Final sweep, push, install sanity-check

**Files:**
- No new modifications expected (fixes only if the sweep finds issues).

- [ ] **Step 1: Consistency sweep**

Run: `grep -rn "ask-and-plan\|coming soon — design now\|do not start implementing, installing packages, or generating contracts. Offer" skills/ README.md`
Expected: no output.

Run: `grep -rn "strk20-by-example.org/helpers/escrow" skills/ README.md`
Expected: exactly one hit — the exclusion line in `references/links.md`.

- [ ] **Step 2: Spot-check live links (SPA returns 200 on all routes)**

Run: `for p in what-is-strk20 starknet-wallet-api/starknet-js sdk/getting-started helpers/privacy-invoke; do curl -s -o /dev/null -w "%{http_code} $p\n" "https://strk20-by-example.org/$p"; done`
Expected: four lines of `200 …`.

- [ ] **Step 3: Push**

```bash
git push
```

- [ ] **Step 4: Install sanity-check in a scratch dir**

Run (from the session scratchpad): `mkdir -p install-test-v030 && cd install-test-v030 && npx -y skills add starkience/strk20-agent-skills --skill strk20-privacy-integration -y`
Expected: "Installed 1 skill".

Run: `grep -c "Step 5" .agents/skills/strk20-privacy-integration/SKILL.md && test -f .agents/skills/strk20-privacy-integration/references/execute.md && echo EXECUTE-OK`
Expected: a number ≥ 4, then `EXECUTE-OK`.

- [ ] **Step 5: Mark the plan done**

Tick all checkboxes in this plan file, commit:
```bash
git add docs/superpowers/plans/2026-07-13-ask-plan-execute.md
git commit -m "v0.3.0 shipped — plan checked off" && git push
```
