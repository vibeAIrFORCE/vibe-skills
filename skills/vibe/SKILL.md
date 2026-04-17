---
name: vibe
description: >
  Your AI agent's Web3 trading brain. One skill, 25+ commands for DeFi trading,
  token launches, data providers, wallet management, token leaderboard, OpenClaw gateway,
  and cross-chain swap execution across Solana, Base, and Ethereum.
metadata:
  version: "0.2.0"
tools:
  - bash
---

# VIBE Airforce Skill

You have access to the VIBE CLI (`vibe`), a command-line tool for Web3 trading, data, and gateway management.

## Setup Protocol

ALWAYS run these at the start of every session:

1. Check CLI is installed: `which vibe`
2. If not installed: `curl -fsSL https://api.vibe.airforce/api/cli/install.sh | sh`
3. Sync manifest: `vibe sync`
4. Check version matches `metadata.version` above. If outdated: `vibe install`

## Discovery Workflow

BEFORE executing any command, follow this workflow:

1. `vibe sync` — refresh the operations manifest
2. `vibe list-operations` — see all available commands
3. `vibe list-operations | grep <keyword>` — filter by domain
4. `vibe <command> --help` — check full params and types before executing

NEVER guess command names or parameters. Always discover first.

## Domain Routing

Map user intent to the correct command:

| User says | Domain | Commands |
|-----------|--------|----------|
| "swap", "trade", "exchange" on Solana | Solana Trading | `vibe swap-quote`, `vibe swap` |
| "swap on Base", "swap on Ethereum", "EVM trade" | EVM Trading | `vibe evm-swap-quote`, `vibe evm-swap` |
| "stake", "lend", "yield", "DeFi" | DeFi | `vibe defi-discover`, `vibe defi-quote`, `vibe defi-deposit` |
| "launch token", "create token" | Token Launch | `vibe bags-launch-token` |
| "fees", "claim fees", "my positions" | Fee Management | `vibe bags-claim-fees`, `vibe bags-positions` |
| "market data", "smart money", "analytics", "on-chain" | Data | `vibe data-provider` |
| "tokens", "leaderboard", "price", "market cap" | Token Leaderboard | `vibe tokens`, `vibe token-info` |
| "wallet", "balance", "address", "transactions" | Wallet | `vibe wallet-config`, `vibe wallet-address`, `vibe wallet-balance`, `vibe wallet-transactions` |
| "gateway", "openclaw", "start agent", "trading bot" | OpenClaw Gateway | `vibe gateway-status`, `vibe gateway-start`, `vibe gateway-stop`, `vibe gateway-templates` |
| "telegram bot", "telegram config" | Telegram | `vibe telegram-config` |

## Command Reference

### Solana Swap
```bash
# Quote
vibe swap-quote --from-token <mint_address> --to-token <mint_address> --amount <lamports>
# Execute (returns unsigned tx — client must sign)
vibe swap --from-token <mint_address> --to-token <mint_address> --amount <lamports>

# Common token mints:
# SOL:   So11111111111111111111111111111111111111112
# USDC:  EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v
# USDT:  Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB
```

### EVM Swap (Base/Ethereum)
```bash
# Quote on Base
vibe evm-swap-quote --network base --from-asset eth --to-asset usdc --amount 0.1

# Execute on Base (uses CDP managed wallet — no client signing needed)
vibe evm-swap --network base --from-asset eth --to-asset usdc --amount 0.1

# Networks: base, ethereum
# Assets: eth, usdc, or 0x contract addresses
```

### DeFi
```bash
# Discover protocols (Solana by default)
vibe defi-discover
vibe defi-discover --network solana

# Get quote (use "supply" not "lend")
vibe defi-quote --action stake --token SOL --amount 1.0 --protocol marinade
vibe defi-quote --action supply --token USDC --amount 100 --protocol solend

# Execute deposit (returns unsigned tx)
vibe defi-deposit --action stake --token SOL --amount 1.0 --protocol marinade

# Actions: stake, supply, borrow, add_liquidity
# NOTE: Use "supply" for lending — "lend" is NOT a valid action
# Protocols: marinade, jito, solend, marginfi, kamino, orca, raydium
```

### Token Launch (bags.fm)
```bash
vibe bags-launch-token --name "My Token" --symbol MYT --description "A token" --image-url https://...
```

### Fee Management
```bash
vibe bags-positions                              # View all positions (requires wallet)
vibe bags-claim-fees --token-mint <address>      # Claim fees
```

### Data Providers
```bash
vibe data-provider --service nansen --route smart_money_netflows --payload '{"chains":["base"]}'
vibe data-provider --service twitter --route search --payload '{"query":"ethereum"}'
vibe data-provider --service blokiments --route smart_money_latest_buys --payload '{"chain":"base"}'
vibe data-provider --service yahoo_finance --route get_tickers --payload '{"tickers":["BTC-USD","ETH-USD"]}'

# Services and routes:
# nansen: smart_money_netflows, smart_money_inflows, smart_money_outflows, smart_money_holdings
# twitter: search, trending
# blokiments: general_token_metrics, token_current_price, token_data, smart_money_latest_buys, smart_money_latest_big_buys
# yahoo_finance: get_tickers, search, get_news, get_stock_module, get_sma, get_rsi, get_earnings_calendar, get_insider_trades
```

### Token Leaderboard (public — no auth needed)
```bash
vibe tokens                                      # Token leaderboard (sorted by market cap)
vibe tokens --sort volume_24h --limit 10         # Top 10 by 24h volume
vibe tokens --stats                              # Platform-wide stats
vibe tokens --creators                           # Creator XP leaderboard
vibe tokens --agents                             # Agent performance leaderboard
vibe tokens --agents --sort fees_claimed         # Agents sorted by fees claimed
vibe token-info So11111111111111111111111111111111111111112  # Single token detail

# Sort fields: market_cap, volume_24h, price_change_24h, created_at, fees_claimed
# Agent sort: roi_percent, fees_claimed, trades, tokens_launched
```

### Wallet (requires JWT auth — `vibe auth` first)
```bash
vibe wallet-config                               # Get wallet config (network, auto-confirm)
vibe wallet-address --network base               # Get wallet address for network
vibe wallet-balance --network base               # Get token balances
vibe wallet-transactions --network base --limit 20  # Transaction history

# Networks: base, ethereum, solana
```

### OpenClaw Gateway (requires JWT auth — `vibe auth` first)
```bash
vibe gateway-status                              # Get gateway status
vibe gateway-templates                           # List available templates
vibe gateway-start --template trader             # Start gateway with template
vibe gateway-start --template degen --bot-token 123456:ABC  # Start with Telegram
vibe gateway-stop                                # Stop gateway
vibe gateway-logs                                # Stream gateway logs (Ctrl+C to stop)
vibe telegram-config                             # Get Telegram config
vibe telegram-config --bot-token 123456:ABC --enabled  # Set Telegram config

# Templates: trader, shitcoin-trader, degen, analyst
```

## Output Format

For structured processing by agents:
```bash
vibe swap-quote -o json -f body.data --from-token ... --to-token ... --amount ...
```

- `-o json` — output as JSON
- `-f body.data` — extract nested field using dot-path
- `-o raw` — unformatted JSON

## Gotchas

1. **Solana amounts are in lamports** — multiply by 10^9. `1 SOL = 1000000000 lamports`. EVM amounts are human-readable: `0.1 ETH`.
2. **Solana swaps return unsigned transactions** — the client must sign before broadcasting. EVM swaps execute directly via CDP managed wallet (no client signing).
3. **`gateway:wallet_write` scope required** for: `swap`, `evm-swap`, `defi-deposit`, `bags-launch-token`, `bags-claim-fees`. Without it, you get 403.
4. **Network names** — must be: `solana`/`solana-mainnet`, `base`, `ethereum`. Never `eth`, `sol`, `basescan`.
5. **`data-provider` requires both `--service` and `--route`** — check `vibe data-provider --help` for valid combinations.
6. **Solana token addresses are base58** — 32-44 chars, not hex `0x...`. EVM uses hex addresses with `0x` prefix.
7. **EVM assets use CDP IDs** — `eth`, `usdc`, `base-eth`. For custom tokens, use the full `0x` contract address.
8. **Always quote JSON payloads** in shell — single quotes around the JSON string.
9. **Run `vibe sync` before first use** in any session — the manifest must be cached locally.
10. **`--amount` types differ** — Solana: integer (lamports), EVM: string (human-readable), DeFi: string (decimal).
11. **DeFi action is `supply` not `lend`** — the valid actions are: stake, supply, borrow, add_liquidity.
12. **EVM swap requires a connected wallet** — returns 400 if no wallet exists for the account on that network.
13. **`bags-positions` requires a wallet** — returns empty if no wallet is linked.
14. **Token leaderboard is public** — no auth needed for `vibe tokens` and `vibe token-info`.
15. **Wallet and gateway commands require JWT auth** — use `vibe auth` to set up. Trial tokens only have read scope.
16. **`defi-discover` accepts `--network`** — defaults to `solana`. Use `--network evm` for EVM DeFi.

## Common Token Mints Reference

### Solana
| Token | Mint Address |
|-------|-------------|
| SOL | `So11111111111111111111111111111111111111112` |
| USDC | `EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v` |
| USDT | `Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB` |
| BONK | `DezXAZ8z7PnrnRJjz3wXBoRgixCa6xjnB7YaB1pPB263` |
| JTO | `jtojtomepa8beP8AuQc6eXt5FrivrwYsowhRmLP4n38` |
| JUP | `JUPyiwrYJFskUPiHa7hkeR8VUtAeFoSYbKedZNsDvCN` |
| WIF | `EKpQGSJtjMFqWZL3YYqLwYQKaQoTsgEjbLqHfHq2CgJ3` |

### EVM (Base/Ethereum)
| Token | CDP Asset ID |
|-------|-------------|
| ETH | `eth` |
| USDC | `usdc` |
| DAI | `dai` |
| WETH | `weth` |
| Custom tokens | Full `0x` contract address |

## Auth and Quota Handling

**Try first, guide if needed.** NEVER ask about API keys before executing a command.

On error (exit code 4), check the error code:

| Error Code | Meaning | Guide User to |
|-----------|---------|---------------|
| `UNAUTHORIZED` | No valid API key | `vibe auth` or create key at vibe.airforce/settings/api-keys |
| `FREE_QUOTA_EXHAUSTED` | Trial limit reached | Create API key at vibe.airforce/settings/api-keys |
| `INSUFFICIENT_CREDITS` | Account out of credits | Top up credits at vibe.airforce/settings/billing |
| `RATE_LIMITED` | Too many requests | Wait `retry_after` seconds, then retry |

**Security rule:** NEVER accept API keys pasted into chat. Direct users to `vibe auth` instead.

## API Reference

- **Base URL:** `https://api.vibe.airforce/api/vibe-tools`
- **Auth (vibe-tools):** `Authorization: Bearer pk_xxx:sk_xxx` or `Authorization: Bearer trial_xxx`
- **Auth (wallet/openclaw):** `Authorization: Bearer <supabase_jwt>` (obtained via `vibe auth`)
- **Response envelope:** `{ "data": {...}, "meta": { "request_id": "...", "timestamp": "..." } }`
- **Error envelope:** `{ "error": { "code": "...", "message": "..." }, "meta": {...} }`
- **Error codes:** `UNAUTHORIZED`, `FORBIDDEN_SCOPE`, `NOT_FOUND`, `RATE_LIMITED`, `INSUFFICIENT_CREDITS`, `VALIDATION_ERROR`, `PROVIDER_ERROR`

## Feedback

If the user is dissatisfied with results or data is missing:
1. Ask for permission before submitting
2. `vibe feedback "<summary of the issue>"`
