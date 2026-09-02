---
name: vibe
description: >
  Your AI agent's Web3 trading brain. One skill, 85 commands for DeFi trading
  (Solana + EVM via Enso), perps on Hyperliquid, prediction markets on Polymarket,
  token launches (bags.fm, pump.fun, Clanker), data providers, wallet management,
  token leaderboard, Top Picks, OpenClaw gateway, x402 marketplace (call AND publish),
  Enso Shield tx safety, Twitter/X API v2 integration, and cross-chain swap execution
  across Solana, Base, Ethereum, and Robinhood Chain.
metadata:
  version: "0.5.1"
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
| "stake", "lend", "yield", "DeFi" on Solana | DeFi (Solana) | `vibe defi-discover`, `vibe defi-quote`, `vibe defi-deposit` |
| "park ETH", "earn yield on Base", "DeFi" on EVM | DeFi (EVM) | `vibe defi-discover --network base`, `vibe evm-defi-quote`, `vibe evm-defi-deposit` |
| "bridge", "borrow", "repay", "migrate position", "add liquidity", "LP", "withdraw" on EVM | DeFi (EVM advanced) | `vibe evm-defi-bridge`, `vibe evm-defi-borrow`, `vibe evm-defi-migrate`, `vibe evm-defi-lp-deposit`, `vibe evm-defi-withdraw`, `vibe evm-defi-bundle` |
| "perps", "leverage", "short", "long", "funding", "Hyperliquid" | Perps (Hyperliquid) | `vibe hyperliquid-info`, `vibe hyperliquid-mids`, `vibe hyperliquid-place-order`, `vibe hyperliquid-portfolio` |
| "prediction market", "Polymarket", "odds", "arbitrage" | Prediction Markets | `vibe pmxt-search`, `vibe pmxt-quote`, `vibe pmxt-order`, `vibe pmxt-arbitrage` |
| "launch token", "create token" (Solana) | Token Launch | `vibe bags-launch-token`, `vibe pump-create-coin` |
| "deploy token on Base", "ERC-20 launch", "Clanker" | Token Launch (EVM) | `vibe clanker-deploy`, `vibe clanker-rewards` |
| "fees", "claim fees", "my positions" | Fee Management | `vibe bags-claim-fees`, `vibe pump-claim-fees`, `vibe clanker-rewards`, `vibe pump-positions` |
| "market data", "smart money", "analytics", "on-chain" | Data | `vibe data-provider` |
| "tokens", "leaderboard", "price", "market cap" | Token Leaderboard | `vibe tokens`, `vibe token-info` |
| "top picks", "smart money picks", "what to buy" | Top Picks | `vibe top-picks` |
| "token price", "how much is X worth" | Price | `vibe evm-token-price` |
| "simulate tx", "is this tx safe", "MEV", "tamper" | Safety Shields | `vibe shield-simulate`, `vibe shield-validate` |
| "wallet", "balance", "address", "transactions" | Wallet | `vibe wallet-config`, `vibe wallet-address`, `vibe wallet-balance`, `vibe wallet-transactions` |
| "credits", "top up", "balance low", "billing" | Credits | `vibe credits-balance`, `vibe credits-packages`, `vibe credits-agent-topup` |
| "buy data", "call x402", "pay for API", "spending" | x402 Client | `vibe x402-discover`, `vibe x402-call`, `vibe x402-spending` |
| "sell data", "publish", "marketplace", "x402 seller", "monetize", "earnings" | x402 Marketplace | `vibe marketplace-profile`, `vibe marketplace-publish`, `vibe marketplace-catalog`, `vibe marketplace-earnings` |
| "gateway", "openclaw", "start agent", "trading bot" | OpenClaw Gateway | `vibe gateway-status`, `vibe gateway-start`, `vibe gateway-stop`, `vibe gateway-templates` |
| "telegram bot", "telegram config" | Telegram | `vibe telegram-config` |
| "twitter", "tweet", "post", "search tweets", "like", "retweet", "follow" | Twitter/X | `vibe data-provider --service twitter` |

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

### DeFi (Solana)
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

### DeFi (EVM via Enso Finance)
```bash
# Discover yield opportunities on Base with APY data
vibe defi-discover --network base
vibe defi-discover --network base --min-apy 5.0
vibe defi-discover --network ethereum --project aave-v3

# Get DeFi quote for depositing ETH into a vault on Base
vibe evm-defi-quote --network base --token-in eth --token-out <vault_address> --amount 0.1

# Execute DeFi deposit (returns unsigned tx)
vibe evm-defi-deposit --network base --token-in eth --token-out <vault_address> --amount 0.1

# Networks: base, ethereum, arbitrum, optimism, polygon
# token-in: 'eth' for native, or 0x contract address
# token-out: vault/pool contract address from defi-discover results
# Filters: --min-apy, --min-tvl, --project, --page-size

# Full EVM DeFi suite (all via Enso, all return unsigned tx unless noted):
vibe evm-defi-withdraw    --network base --amount 0.1   # exit a vault/lending position
vibe evm-defi-bridge      --network base --to-chain polygon  # bridge tokens between EVM chains
vibe evm-defi-borrow      --network base                 # borrow via Enso Bundle
vibe evm-defi-repay       --network base                 # repay a borrow via Enso Bundle
vibe evm-defi-migrate     --network base                 # move a position between protocols/vaults
vibe evm-defi-lp-deposit  --network base                 # add liquidity to a pool
vibe evm-defi-lp-withdraw --network base                 # remove liquidity
vibe evm-defi-bundle      --network base                 # MULTIPLE actions atomically (one tx)
vibe defi-nontokenized                                  # staking/farming positions without LP tokens
vibe evm-token-price --network base --token <0xaddr>   # ERC-20 price in USD via Enso
```

### Safety Shields (Enso Shield)
```bash
# Simulate a DeFi tx on a forked EVM BEFORE broadcasting
vibe shield-simulate

# Validate tx integrity — tamper/MEV detection
vibe shield-validate
```
RECOMMENDED for any high-value EVM DeFi action: simulate first, validate, then broadcast.

### Perps (Hyperliquid)
```bash
# Auth: get the VIBE wallet EVM address for EIP-712 signing
vibe hyperliquid-auth

# Read (all query types via raw /info)
vibe hyperliquid-info --body '{"type":"clearinghouseState","user":"<addr>"}'
vibe hyperliquid-mids                              # mid prices, ~340 assets
vibe hyperliquid-orderbook --coin BTC              # L2 top-20 per side
vibe hyperliquid-candles --coin BTC --interval 1h
vibe hyperliquid-fills --user <addr>               # recent fills (up to 2000)
vibe hyperliquid-orders --user <addr>              # open orders
vibe hyperliquid-portfolio --user <addr>           # account value, PnL by day/week/month

# Write (EIP-712 signed via the wallet)
vibe hyperliquid-place-order --coin BTC --side buy --size 0.01 --price 50000  # limit or trigger
vibe hyperliquid-cancel --coin BTC --oid 123
vibe hyperliquid-cancel-all                        # dead man's switch
vibe hyperliquid-set-leverage --coin BTC --leverage 5
```

### Prediction Markets (Polymarket via PMXT)
```bash
vibe pmxt-search --keyword "fed rate"              # find markets
vibe pmxt-quote --market <id>                      # midpoint + spread
vibe pmxt-orderbook --market <id>                  # depth
vibe pmxt-compare                                  # prices across exchanges
vibe pmxt-arbitrage                                # scan for arb opportunities
vibe pmxt-balance                                  # USDC balance on Polymarket
vibe pmxt-positions                                # open positions
vibe pmxt-portfolio                                # balance + PnL summary
vibe pmxt-order --market <id> --side buy --amount 10  # limit or market
vibe pmxt-deposit --amount 50                      # deposit USDC via Bridge API
```

### Token Launch (bags.fm + pump.fun — Solana)
```bash
vibe bags-launch-token --name "My Token" --symbol MYT --description "A token" --image-url https://...
vibe pump-create-coin --name "My Coin" --symbol MYC   # pump.fun coin
vibe pump-swap                                        # swap on pump.fun
vibe pump-coin-info                                   # coin info
```

### Token Launch (Clanker — EVM)
```bash
vibe clanker-deploy --name "My Token" --symbol MYT    # ERC-20 on EVM chains (80/20 fee split)
vibe clanker-deployments                              # your deployments
vibe clanker-rewards                                  # check/claim LP fee rewards
vibe clanker-vault                                    # vault balance / claim vaulted tokens
vibe clanker-update-image --token <addr> --image <url>
vibe clanker-update-metadata --token <addr>
vibe clanker-airdrop --token <addr>                   # Merkle airdrop registration
```

### Fee Management
```bash
vibe bags-positions                              # View all positions (requires wallet)
vibe bags-claim-fees --token-mint <address>      # Claim bags.fm fees
vibe pump-claim-fees                             # Claim pump.fun fees
vibe pump-fee-sharing                            # Configure pump.fun fee sharing
vibe pump-positions                              # pump.fun positions
```

### Data Providers

**Nansen credit costs — READ BEFORE CALLING:**

**You pay VIBE $0.01 per `data-provider` call** (1 cent USD). VIBE credits = dollars, 1:1.
Behind the scenes, VIBE pays Nansen per endpoint tier. Both matter — if VIBE runs out of Nansen credits, calls fail for everyone.

| Tier | Your cost | VIBE pays Nansen | Endpoints |
|------|-----------|-----------------|-----------|
| Basic | 0.01cr/call | $0.01 (10cr) | All Profiler (except labels/counterparties), all TGM (1cr), portfolio, points |
| Premium | 0.06cr/call | $0.05 (50cr) | Smart Money APIs, tgm_holders, tgm_pnl_leaderboard, profiler_counterparties |
| Labels | 1.50cr/call | $1.00-$5.00 (1000-5000cr) | profiler_labels — VIBE eats most of the cost |
| Agent | NOT AVAILABLE | $2.00-$7.50 (2000-7500cr) | Nansen AI Agent — not wired up, cannot be called |

**Daily cost examples (at $0.01/call):**
- 30min scan (48 calls/day) = **$0.48/day** — sustainable
- 10min loop (144 calls/day) = **$1.44/day** — expensive
- 5min loop (288 calls/day) = **$2.88/day** — burns $86/month on data alone
- Retry loop (10 retries on failure) = **$0.10 wasted per failure**

**ANTI-LOOP RULES:**
- **NEVER call `data-provider` in a retry loop** — each retry costs $0.01 even on errors
- **Max 48 calls/day** (every 30 min) — that's already $0.48/day
- **Pre-filter with DEXScreener (FREE)** before spending a single VIBE credit
- **Cache results** — same call twice in 5 min = wasted $0.01
- **Check credit balance first:** `GET /api/vibe-tools/credits/balance` or `vibe credits-balance`

```bash
vibe data-provider --service nansen --route smart_money_netflows --payload '{"chains":["base"]}'
vibe data-provider --service twitter --route search --payload '{"query":"ethereum"}'

vibe data-provider --service yahoo_finance --route get_tickers --payload '{"tickers":["BTC-USD","ETH-USD"]}'

# Services and routes:
# nansen: smart_money_netflows, smart_money_inflows, smart_money_outflows, smart_money_holdings

# yahoo_finance: get_tickers, search, get_news, get_stock_module, get_sma, get_rsi, get_earnings_calendar, get_insider_trades
```

### Twitter/X (22 endpoints — X API v2 full access)
```bash
# Read endpoints (no Twitter connection needed)
vibe data-provider --service twitter --route search --payload '{"query":"ethereum","max_results":10}'
vibe data-provider --service twitter --route get_tweet --payload '{"tweet_id":"123456"}'
vibe data-provider --service twitter --route get_user --payload '{"username":"vibe_ai_dev"}'
vibe data-provider --service twitter --route user_timeline --payload '{"user_id":"123456","max_results":10}'
vibe data-provider --service twitter --route user_mentions --payload '{"user_id":"123456","max_results":10}'
vibe data-provider --service twitter --route followers --payload '{"user_id":"123456","max_results":100}'
vibe data-provider --service twitter --route following --payload '{"user_id":"123456","max_results":100}'
vibe data-provider --service twitter --route liking_users --payload '{"tweet_id":"123456"}'
vibe data-provider --service twitter --route quote_tweets --payload '{"tweet_id":"123456"}'
vibe data-provider --service twitter --route tweet_counts --payload '{"query":"ethereum","granularity":"day"}'
vibe data-provider --service twitter --route trending --payload '{}'

# Write endpoints (requires Twitter OAuth connected at vibe.airforce/settings/connections)
vibe data-provider --service twitter --route create_tweet --payload '{"text":"Hello from VIBE!"}'
vibe data-provider --service twitter --route delete_tweet --payload '{"tweet_id":"123456"}'
vibe data-provider --service twitter --route like --payload '{"tweet_id":"123456"}'
vibe data-provider --service twitter --route unlike --payload '{"tweet_id":"123456"}'
vibe data-provider --service twitter --route retweet --payload '{"tweet_id":"123456"}'
vibe data-provider --service twitter --route undo_retweet --payload '{"tweet_id":"123456"}'
vibe data-provider --service twitter --route follow --payload '{"target_user_id":"123456"}'
vibe data-provider --service twitter --route unfollow --payload '{"user_id":"123456"}'
vibe data-provider --service twitter --route bookmark --payload '{"tweet_id":"123456"}'
vibe data-provider --service twitter --route bookmarks --payload '{"max_results":10}'
vibe data-provider --service twitter --route hide_reply --payload '{"tweet_id":"123456","hidden":true}'
vibe data-provider --service twitter --route me --payload '{}'

# Pricing: reads $0.005-0.01, writes $0.01-0.02 per call. Pay with web3 wallet credits.
# Unlimited use, no rate limits on VIBE's end.
# Auth modes: read works without connection, write requires X OAuth at vibe.airforce/settings/connections

# More read endpoints (v2.0):
vibe data-provider --service twitter --route search_users --payload '{"query":"vibe","max_results":10}'
vibe data-provider --service twitter --route get_user_by_id --payload '{"user_id":"123456"}'
vibe data-provider --service twitter --route get_tweets_by_ids --payload '{"ids":"123,456,789"}'
vibe data-provider --service twitter --route get_liked_tweets --payload '{"user_id":"123456"}'
vibe data-provider --service twitter --route get_retweets --payload '{"tweet_id":"123456"}'
vibe data-provider --service twitter --route get_retweeted_by --payload '{"tweet_id":"123456"}'
vibe data-provider --service twitter --route get_trends --payload '{"woeid":1}'
vibe data-provider --service twitter --route get_personalized_trends --payload '{}'
vibe data-provider --service twitter --route delete_bookmark --payload '{"tweet_id":"123456"}'
vibe data-provider --service twitter --route block_user --payload '{"target_user_id":"123456"}'
vibe data-provider --service twitter --route unblock_user --payload '{"target_user_id":"123456"}'
vibe data-provider --service twitter --route get_blocked_users --payload '{"max_results":100}'
vibe data-provider --service twitter --route mute_user --payload '{"target_user_id":"123456"}'
vibe data-provider --service twitter --route unmute_user --payload '{"target_user_id":"123456"}'
vibe data-provider --service twitter --route get_muted_users --payload '{"max_results":100}'
```

### Token Leaderboard + Top Picks (public — no auth needed)
```bash
vibe tokens                                      # Token leaderboard (sorted by market cap)
vibe tokens --sort volume_24h --limit 10         # Top 10 by 24h volume
vibe tokens --stats                              # Platform-wide stats
vibe tokens --creators                           # Creator XP leaderboard
vibe tokens --agents                             # Agent performance leaderboard
vibe tokens --agents --sort fees_claimed         # Agents sorted by fees claimed
vibe token-info So11111111111111111111111111111111111111112  # Single token detail
vibe top-picks                                   # Top 3 smart money tokens per chain (VIBE Score)

# Sort fields: market_cap, volume_24h, price_change_24h, created_at, fees_claimed
# Agent sort: roi_percent, fees_claimed, trades, tokens_launched
```

### Wallet (API key or JWT auth)
```bash
vibe wallet-config                               # Get wallet config (network, auto-confirm)
vibe wallet-address --network base               # Get wallet address for network
vibe wallet-balance --network base               # Get token balances
vibe wallet-transactions --network base --limit 20  # Transaction history

# Networks: base, base-sepolia, base-mainnet, ethereum, solana, solana-mainnet, robinhood
# Auth: works with API key (Bearer pk_xxx:sk_xxx) or JWT
```

### Credits & Top-Up (API key auth)

Check balance and autonomously top up credits using your agent wallet.

```bash
# Check credit balance
vibe credits-balance

# List available top-up packages
vibe credits-packages

# Autonomous top-up (swap + pay + credit in one call)
vibe credits-agent-topup --amount 5.00
```

**Top-Up Packages:**

| Package | Cost | Credits | Bonus |
|---------|------|---------|-------|
| Micro   | $5   | $5.00   | 0%    |
| Small   | $10  | $10.00  | 0%    |
| Medium  | $25  | $27.50  | +10%  |
| Large   | $50  | $60.00  | +20%  |

**How agent-topup works:**
1. Server checks your wallet for USDC (on Solana or Base)
2. If not enough USDC: auto-swaps SOL→USDC via Jupiter (Solana) or ETH→USDC via 0x (Base)
3. Transfers USDC to VIBE platform wallet
4. Adds credits to your balance

**Top-Up Flow:**
```bash
# 1. Check credits
vibe credits-balance
# If low (< $3), proceed:

# 2. Check wallet has funds
vibe wallet-balance --network solana

# 3. Execute top-up (server handles everything)
vibe credits-agent-topup --amount 5.00
# Response: {"success":true, "credits_added":5.00, "new_balance":6.55, "swap_performed":true}
```

### x402 — Call Paid Services (USDC over HTTP 402)
```bash
vibe x402-discover                               # Discover x402 services (Coinbase Bazaar + Orbis)
vibe x402-call --url <service_url>               # Call a paid endpoint — automatic USDC payment on Base
vibe x402-spending                               # Spending summary + tx history
```

### x402 Marketplace — Publish & Monetize

Monetize agent intelligence by publishing data products to the x402 marketplace. These are direct HTTP API calls, not `vibe` CLI commands.

```bash
# Create a seller profile
curl -X POST {VIBE_API_URL}/api/marketplace/profile \
  -H "Content-Type: application/json" \
  -H "x-api-key: {VIBE_API_TOKEN}" \
  -d '{"handle":"your-handle","display_name":"Your Name","bio":"What you offer","eth_address":"0x..."}'

# Register an agent as x402 seller
curl -X PUT {VIBE_API_URL}/api/agents/{agent_id}/x402 \
  -H "Content-Type: application/json" \
  -H "x-api-key: {VIBE_API_TOKEN}" \
  -d '{"enabled":true,"price_per_call_usdc":"0.50","description":"Service description"}'

# View agent x402 config
curl {VIBE_API_URL}/api/agents/{agent_id}/x402 \
  -H "x-api-key: {VIBE_API_TOKEN}"

# View agent earnings
curl {VIBE_API_URL}/api/agents/x402/earnings \
  -H "x-api-key: {VIBE_API_TOKEN}"

# Publish data product to catalog
curl -X PUT {VIBE_API_URL}/api/data-products/{product_id}/config \
  -H "Content-Type: application/json" \
  -H "x-api-key: {VIBE_API_TOKEN}" \
  -d '{"enabled":true,"price_usdc":"1.00","title":"Daily Report","description":"Description"}'

# Browse marketplace catalog
curl {VIBE_API_URL}/api/data-products/catalog \
  -H "x-api-key: {VIBE_API_TOKEN}"

# View data product earnings
curl {VIBE_API_URL}/api/data-products/earnings \
  -H "x-api-key: {VIBE_API_TOKEN}"

# Create marketplace bundle
curl -X POST {VIBE_API_URL}/api/marketplace/bundles/create \
  -H "Content-Type: application/json" \
  -H "x-api-key: {VIBE_API_TOKEN}" \
  -d '{"name":"Bundle Name","product_ids":["<id1>","<id2>"],"price_usdc":"5.00"}'
```

**Publishing Best Practices:**
- **Recommend, don't auto-publish** — Present the product, price, and rationale. Let the human approve before publishing.
- **Price based on signal quality** — High-frequency alpha (> daily) commands higher prices than weekly summaries.
- **Know your costs** — Check `vibe credits-balance` to understand what data costs to produce before setting a price.
- **Quality matters** — Stale or wrong data damages seller reputation. Verify before publishing.
- **Bundle for value** — If two products sell together often, create a discounted bundle.
- **Requires a connected wallet** — USDC payments flow through it. Check with `vibe wallet-config`.

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
3. **`gateway:wallet_write` scope required** for: `swap`, `evm-swap`, `defi-deposit`, `evm-defi-deposit`, `bags-launch-token`, `bags-claim-fees`. Without it, you get 403.
4. **Network names** — must be: `solana`/`solana-mainnet`, `base`, `ethereum`, `robinhood`. Never `eth`, `sol`, `basescan`.
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
15. **Wallet commands work with API key auth** — no JWT required if you have an API key. Falls back to JWT if no API key set.
16. **`defi-discover` accepts `--network`** — defaults to `solana`. Use `--network base` or `--network ethereum` for EVM DeFi via Enso Finance.
17. **EVM DeFi vault addresses** — get them from `vibe defi-discover --network base`. The `address` field in results is the vault/pool contract for `--token-out`.
18. **Enso supports 5 EVM chains** — base, ethereum, arbitrum, optimism, polygon. All use chain-specific vault addresses.
19. **Twitter write endpoints need OAuth** — user must connect X at vibe.airforce/settings/connections first. Read endpoints work without connection.
20. **Twitter user_timeline/mentions/followers/following need `user_id`** — not username. Get it via `get_user` first.
21. **Twitter `search` returns last 7 days** — X API v2 recent search limitation.
22. **Quote tweet 403** — If original tweet author restricted replies (to mentioned/followed users only), X also blocks quote tweets. Fall back to standalone tweet.
23. **One cashtag per tweet** — X rejects tweets with multiple `$SYMBOL` cashtags. Use only one, or replace extras with plain text (e.g. "SOL" instead of "$SOL").
24. **Check credits before data-heavy work** — Every `data-provider` call costs $0.01. At 48 calls/day = $0.48/day. Check balance with `vibe credits-balance` at session start.
25. **Low credits? Use agent-topup** — `vibe credits-agent-topup --amount 5.00` handles swap + transfer + credit in one call. Requires `gateway:wallet_write` scope.
26. **agent-topup needs wallet funds** — If wallet has no SOL/USDC, top-up fails. Check `vibe wallet-balance --network solana` first.
27. **Credit packages have bonuses** — Medium (+10%) and Large (+20%) give extra credits. Always prefer these over Micro/Small when funds allow.
28. **Marketplace endpoints require `x-api-key` header** — these are direct HTTP API calls, not `vibe` CLI commands. Use `pk_xxx:sk_xxx` format or JWT `Bearer` token.
29. **Don't auto-publish without approval** — publishing a data product with bad pricing or wrong data costs reputation. Present product + price + rationale first.
30. **Marketplace needs a connected wallet** — USDC payments flow through your VIBE wallet. Set it up with `vibe wallet-config` first.
31. **Enso Bundle = atomic** — `vibe evm-defi-bundle` executes multiple DeFi actions in ONE tx; if any step reverts, all revert. Use it for multi-step strategies.
32. **Hyperliquid signing** — `hyperliquid-auth` returns the VIBE wallet's EVM address used for EIP-712 signing; use it as the `user` for read endpoints.
33. **Hyperliquid cancel-all is a dead man's switch** — it cancels EVERY open order for the wallet. Confirm before running.
34. **Shield before broadcast** — for high-value EVM DeFi actions, `vibe shield-simulate` (forked-chain sim) + `vibe shield-validate` (tamper/MEV) before executing.
35. **Polymarket orders are USDC-denominated** — check `vibe pmxt-balance` first; deposits go via the Bridge API (`vibe pmxt-deposit`).
36. **Robinhood Chain is wallet-read** — `vibe wallet-address --network robinhood` and balances work; EVM trading on RH is not enabled yet.
37. **Trial mode** — no API key needed for the first 30 requests/day (read-only). Get a trial token: `vibe trial-auth`.

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
- **Auth (vibe-tools):** `x-api-key: pk_xxx:sk_xxx` or `x-api-key: trial_xxx` (also accepts `Authorization: Bearer`)
- **Auth (wallet via gateway):** `x-api-key: pk_xxx:sk_xxx` (API key) or `Authorization: Bearer <supabase_jwt>`
- **Auth (wallet/openclaw direct):** `Authorization: Bearer <supabase_jwt>` (obtained via `vibe auth`)
- **Response envelope:** `{ "data": {...}, "meta": { "request_id": "...", "timestamp": "..." } }`
- **Error envelope:** `{ "error": { "code": "...", "message": "..." }, "meta": {...} }`
- **Error codes:** `UNAUTHORIZED`, `FORBIDDEN_SCOPE`, `NOT_FOUND`, `RATE_LIMITED`, `INSUFFICIENT_CREDITS`, `VALIDATION_ERROR`, `PROVIDER_ERROR`

## Feedback

If the user is dissatisfied with results or data is missing:
1. Ask for permission before submitting
2. `vibe feedback "<summary of the issue>"`
