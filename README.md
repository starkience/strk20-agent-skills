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

An ask-and-plan skill. Run your agent inside your Starknet project and ask it to "plan STRK20 privacy for this app." It scans your repo (starknet.js, get-starknet, starknet-react, Cairo/Scarb, Starknet Foundry, backend SDKs), interviews you about what exactly should be private, picks the right integration route — Privacy Wallet API via starknet.js (most dapps), anonymizer contract + Wallet API (DeFi protocols), Privacy SDK direct (wallets/backends), or private sub-accounts (tracked) — and writes a phased, versioned `STRK20_INTEGRATION_PLAN.md` that names your actual files.

Scope: Starknet apps.

## Roadmap (not yet in the skill)

- **EVM wallet support** — planned after internal audit and a contract upgrade
- **Polymarket-type app interop** — a dedicated flow for prediction-market-style apps
- **Frontend design guidance** — privacy UX patterns (shield/unshield affordances, honest private-vs-public labeling)

## Status

Current version reflects tooling statuses as of early July 2026 (pre open-source launch of the Privacy SDK monorepo). The skill instructs agents to re-verify fast-moving items before finalizing a plan. Maintainers: see `LAUNCH-DAY-UPDATE.md` for the pending status flips.

## Contributing

Issues and PRs welcome — especially reports from real integration runs (what the interview missed, where a plan was wrong).
