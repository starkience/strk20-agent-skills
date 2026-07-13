# STRK20 Agent Skills

Agent skills for integrating [STRK20 privacy](https://eprint.iacr.org/2026/474) — the live-on-mainnet privacy pool for any ERC-20 on Starknet — into your project. Works with Claude Code, Codex, Cursor, and any agent supported by the [skills CLI](https://skills.sh).

## Install

```sh
npx skills add starkience/strk20-agent-skills
```

Or pick the skill directly:

```sh
npx skills add starkience/strk20-agent-skills --skill strk20-privacy-integration
```

## Skills

### `strk20-privacy-integration`

An ask, plan & execute skill. Run your agent inside your Starknet project and ask it to "plan STRK20 privacy for this app." It scans your repo (starknet.js, get-starknet, starknet-react, Cairo/Scarb, Starknet Foundry, backend SDKs), interviews you about what exactly should be private, picks the right integration route — Privacy Wallet API via starknet.js (most dapps), anonymizer contract + Wallet API (DeFi protocols), Privacy SDK direct (wallets/backends), or private sub-accounts (tracked) — and writes a phased, versioned `STRK20_INTEGRATION_PLAN.md` that names your actual files. Once you approve the plan, it executes it phase by phase — app code only, never Cairo contracts — with a manual wallet-verification handoff at every phase boundary, linking the matching https://strk20-by-example.org/ tutorial pages as it goes.

Scope: Starknet apps.

## Roadmap (not yet in the skill)

- **EVM wallet support** — planned after internal audit and a contract upgrade
- **Polymarket-type app interop** — a dedicated flow for prediction-market-style apps
- **Frontend design guidance** — privacy UX patterns (shield/unshield affordances, honest private-vs-public labeling)

## Status

**v0.3.0** — Ask & Plan & Execute. Statuses as of mid-July 2026, post the July 8, 2026 open-source launch. The [Privacy SDK monorepo](https://github.com/starkware-libs/starknet-privacy) is public (Apache 2.0) and the skill treats it as an official resource — quickstart at [`sdk/README.md`](https://github.com/starkware-libs/starknet-privacy/blob/main/sdk/README.md), verified anonymizer reference packages (`packages/ekubo_swap_anonymizer`, `packages/vesu_lending_anonymizer`). The skill still instructs agents to re-verify fast-moving items (wallet readiness, npm versions, repo paths) before finalizing a plan. `LAUNCH-DAY-UPDATE.md` is completed and kept for provenance.

## Changelog

- **v0.3.1** (2026-07-13) — plan walkthrough now opens with the integration problem summarized in 3–6 plain bullets (first-use feedback from a simulated run).
- **v0.3.0** (2026-07-13) — Ask & Plan & Execute: new Step 5 executes the approved plan phase by phase (app code only, never Cairo; phase-gate handoffs with manual wallet checks; new `references/execute.md`), plus strk20-by-example.org deep links throughout chat output.
- **v0.2.0** (2026-07-13) — post-launch statuses: SDK monorepo + anonymizer examples flipped to live with verified paths; SDK route now buildable; pinned-version notes refreshed (starknet.js STRK20 releases live on the npm `next` tag).
- **v0.1.0** (2026-07-06) — initial release (pre-launch statuses), plus first-use feedback.

## Contributing

Issues and PRs welcome — especially reports from real integration runs (what the interview missed, where a plan was wrong).
