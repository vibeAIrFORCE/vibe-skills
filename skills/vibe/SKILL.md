---
name: vibe
description: >
  Your AI agent's Web3 trading brain. One skill, 12+ commands for DeFi trading,
  token launches, data providers, wallet management, and cross-chain swap execution
  across Solana, Base, and Ethereum.
metadata:
  version: "0.1.0"
tools:
  - bash
---

# VIBE Airforce Skill

You have access to the VIBE CLI (`vibe`), a command-line tool for Web3 trading and data.

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
# Discover protocols
vibe defi-discover

# Get quote
vibe defi-quote --action stake --token SOL --amount 1.0 --protocol marinade

# Execute deposit (returns unsigned tx)
vibe defi-deposit --action stake --token SOL --amount 1.0 --protocol marinade

# Actions: stake, supply, borrow, add_liquidity
# Protocols: marinade, jito, solend, marginfi, kamino, orca, raydium
```

### Token Launch (bags.fm)
```bash
vibe bags-launch-token --name "My Token" --symbol MYT --description "A token" --image-url https://...
```

### Fee Management
```bash
vibe bags-positions                              # View all positions
vibe bags-claim-fees --token-mint <address>      # Claim fees
```

### Data Providers
```bash
vibe data-provider --service nansen --route smart_money_netflows --payload '{"chains":["base"]}'
vibe data-provider --service twitter --route search --payload '{"query":"ethereum"}'

# Services: nansen, blokiments, twitter, yahoo_finance, pumpfun_scraper, fourmeme
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

## Auth and Quota Handling

**Try first, guide if needed.** NEVER ask about API keys before executing a command.

On error (exit code 4), check the error code:

| Error Code | Meaning | Guide User To |
|-----------|---------|---------------|
| `UNAUTHORIZED` | No valid API key | `vibe auth` or create key at vibe.airforce/settings/api-keys |
| `FREE_QUOTA_EXHAUSTED` | Trial limit reached | Create API key at vibe.airforce/settings/api-keys |
| `INSUFFICIENT_CREDITS` | Account out of credits | Top up credits at vibe.airforce/settings/billing |
| `RATE_LIMITED` | Too many requests | Wait `retry_after` seconds, then retry |

**Security rule:** NEVER accept API keys pasted into chat. Direct users to `vibe auth` instead.

## API Reference

- **Base URL:** `https://api.vibe.airforce/api/vibe-tools`
- **Auth:** `Authorization: Bearer pk_xxx:sk_xxx` or `Authorization: Bearer trial_xxx`
- **Response envelope:** `{ "data": {...}, "meta": { "request_id": "...", "timestamp": "..." } }`
- **Error envelope:** `{ "error": { "code": "...", "message": "..." }, "meta": {...} }`
- **Error codes:** `UNAUTHORIZED`, `FORBIDDEN_SCOPE`, `NOT_FOUND`, `RATE_LIMITED`, `INSUFFICIENT_CREDITS`, `VALIDATION_ERROR`, `PROVIDER_ERROR`

## Feedback

If the user is dissatisfied with results or data is missing:
1. Ask for permission before submitting
2. `vibe feedback "<summary of the issue>"`
