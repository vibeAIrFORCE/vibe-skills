# VIBE Skills

Your AI agent's Web3 trading brain. One skill, **85 commands** for DeFi trading (Solana + EVM via Enso), perps on Hyperliquid, prediction markets on Polymarket, token launches (bags.fm, pump.fun, Clanker), data providers, wallet management, Top Picks, and cross-chain swap execution across Solana, Base, Ethereum, and Robinhood Chain.

## Install

```bash
npx skills add vibeAIrFORCE/vibe-skills --skill vibe
```

## What It Does

The VIBE skill teaches AI coding agents (Claude Code, Codex) how to use the VIBE CLI to:
- Get swap quotes and execute trades on Solana (Jupiter/Meteora) and EVM (Base/Ethereum via 0x)
- Discover DeFi protocols, get yield quotes, execute deposits — plus the full EVM suite: bridge, borrow, repay, migrate, LP add/remove, atomic multi-action bundles (Enso)
- Trade perps on Hyperliquid (orders, leverage, portfolio, dead-man's-switch cancel) and prediction markets on Polymarket (search, orders, arbitrage)
- Launch tokens on bags.fm, pump.fun, and Clanker (EVM) — and claim trading fees on all three
- Simulate and validate transactions before broadcasting (Enso Shield tamper/MEV detection)
- Query external data providers (Nansen, Twitter/X, Yahoo Finance)
- Follow smart-money flow with Top Picks, browse the token leaderboard
- Call and publish paid x402 APIs (USDC over HTTP 402 on Base)
- Work without an API key for the first 30 requests/day (trial mode)

## Data Coverage

| Domain | Commands |
|--------|----------|
| Solana Trading | `swap-quote`, `swap` |
| EVM Trading | `evm-swap-quote`, `evm-swap` |
| DeFi (Solana) | `defi-discover`, `defi-quote`, `defi-deposit`, `defi-nontokenized` |
| DeFi (EVM via Enso) | `evm-defi-quote/deposit/withdraw/bridge/borrow/repay/migrate/lp-deposit/lp-withdraw/bundle` |
| Perps (Hyperliquid) | `hyperliquid-auth/info/mids/orderbook/candles/fills/orders/portfolio/exchange/place-order/cancel/cancel-all/set-leverage` |
| Prediction Markets | `pmxt-search/quote/orderbook/compare/arbitrage/balance/positions/portfolio/order/deposit` |
| Token Launch (Solana) | `bags-launch-token`, `pump-create-coin`, `pump-swap`, `pump-coin-info` |
| Token Launch (EVM) | `clanker-deploy`, `clanker-deployments`, `clanker-rewards`, `clanker-vault`, `clanker-airdrop` |
| Fee Management | `bags-claim-fees`, `bags-positions`, `pump-claim-fees`, `pump-fee-sharing`, `pump-positions` |
| Safety Shields | `shield-simulate`, `shield-validate` |
| Price | `evm-token-price` |
| Top Picks | `top-picks` |
| Data Providers | `data-provider` (Nansen, Twitter, Yahoo Finance, etc.) |
| Token Leaderboard | `tokens`, `token-info` |
| Wallet | `wallet-config`, `wallet-address`, `wallet-balance`, `wallet-transactions` |
| x402 Client | `x402-discover`, `x402-call`, `x402-spending` |
| OpenClaw Gateway | `gateway-status/start/stop/templates/logs`, `telegram-config` |

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

# Perps (Hyperliquid)
vibe hyperliquid-mids                        # mid prices across ~340 markets
vibe hyperliquid-place-order --coin BTC --side buy --size 0.01 --price 50000

# Prediction markets (Polymarket)
vibe pmxt-search --keyword "fed rate"
vibe pmxt-order --market <id> --side buy --amount 10

# Structured output for agents
vibe swap-quote -o json -f body.data --from-token ... --to-token ... --amount ...
```

## Adding New Endpoints

No changes needed in this repo. When new endpoints are added upstream:
1. The operations manifest updates automatically
2. `vibe sync` fetches the latest manifest
3. `vibe list-operations` shows the new command
4. `vibe <new-command> --help` shows its parameters
