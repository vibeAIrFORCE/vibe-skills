# VIBE Skills

Your AI agent's Web3 trading brain. One skill, 12+ commands for DeFi trading, token launches, data providers, wallet management, and cross-chain swap execution across Solana, Base, and Ethereum.

## Install

```bash
npx skills add vibeAIrFORCE/vibe-skills --skill vibe
```

## What It Does

The VIBE skill teaches AI coding agents (Claude Code, Codex) how to use the VIBE CLI to:
- Get swap quotes and execute trades on Solana (Jupiter/Meteora) and EVM (Base/Ethereum via 0x)
- Discover DeFi protocols, get yield quotes, and execute deposits
- Launch tokens on bags.fm and claim trading fees
- Query external data providers (Nansen, Blokiments, Twitter)
- Work without an API key for the first 30 requests/day (trial mode)

## Data Coverage

| Domain | Commands |
|--------|----------|
| Solana Trading | `swap-quote`, `swap` |
| EVM Trading | `evm-swap-quote`, `evm-swap` |
| DeFi | `defi-discover`, `defi-quote`, `defi-deposit` |
| Token Launch | `bags-launch-token` |
| Fee Management | `bags-claim-fees`, `bags-positions` |
| Data Providers | `data-provider` (Nansen, Twitter, Blokiments, etc.) |

## Quick Reference

```bash
# Discovery
vibe sync                                    # Fetch operations manifest
vibe list-operations                         # List all commands
vibe list-operations | grep swap             # Filter by keyword
vibe <command> --help                        # Full params for any command

# Solana trading
vibe swap-quote --from-token <mint> --to-token <mint> --amount <lamports>
vibe swap --from-token <mint> --to-token <mint> --amount <lamports>

# EVM trading
vibe evm-swap-quote --network base --from-asset eth --to-asset usdc --amount 0.1
vibe evm-swap --network base --from-asset eth --to-asset usdc --amount 0.1

# Structured output for agents
vibe swap-quote -o json -f body.data --from-token ... --to-token ... --amount ...
```

## Adding New Endpoints

No changes needed in this repo. When new endpoints are added upstream:
1. The operations manifest updates automatically
2. `vibe sync` fetches the latest manifest
3. `vibe list-operations` shows the new command
4. `vibe <new-command> --help` shows its parameters
