# ETH Chain Swaps Monitor

**One operator desk for on-chain DEX swaps — subscribe to hundreds of pools or a wallet watchlist, decode multi-protocol trades, enrich with USD and gas context, and push ranked alerts to a live browser dashboard.**

**ETH Chain Swaps Monitor** turns fragmented Ethereum DEX activity into a single FastAPI workstation. Monitor configured Uniswap V2/V3, Curve, DODO, and Maverick-style pools over WebSocket logs, or scan blocks for wallets in your watchlist. Each swap is decoded, priced, and pushed as an accordion alert with quote ladders, address statistics, and SQLite persistence — without tab-hopping block explorers.

Built for **operators researching on-chain flow** on networks they may legally observe. Runs self-hosted; this overview describes product behaviour only.

**Made by [Logic Encoder](https://logicencoder.com)**

Private source: [logicencoder/eth-chain-swaps-monitor](https://github.com/logicencoder/eth-chain-swaps-monitor)

---

## What you can do

| Area | In plain language |
|------|-------------------|
| **Mode 1** | WebSocket pool logs with swap topic filter on your pool database |
| **Mode 2/4** | HTTP block scan for watchlist wallets — receipt parse and proof alerts |
| **Mode 3** | All pool logs without topic filter |
| **Live alerts** | Ring buffer feed with expandable swap cards and Etherscan links |
| **Quote ladders** | Per-pool USD notionals from $100 through $1000 with slippage math |
| **Address stats** | Top 20 active wallets, buy/sell counts, search by address |
| **Dashboard UX** | Light/medium/dark themes, independent freeze for alerts vs quotes |
| **REST API** | Mode switch, monitoring toggle, stats, quote tables, address search |
| **Persistence** | SQLite transactions, JSON alert snapshots, JSONL quote history |

Alerts and stats push over **browser WebSocket**; chain ingestion uses WebSocket or HTTP poll depending on mode.

---

## Feature examples (two per capability)

#### Monitor Mode 1 — pool swaps
1. You subscribe to hundreds of pools — swap topic logs trigger full decode and alert push.
2. New head updates block counter and session stats in the dashboard header.

#### Monitor Mode 2 — wallet watchlist
1. You load `add1.txt` addresses — each new block scans txs from those wallets.
2. Known pool plus swap topic runs full enrichment; unknown pool emits proof alert with raw log fields.

#### RPC connectivity
1. You point at a local execution client for low-latency `eth_subscribe` log batches.
2. Hosted RPC fallback works when the local node is unavailable — with higher latency tradeoff.

#### Pool configuration
1. `pool_discovery_database.json` lists hundreds of coins with nested V2/V3 pool entries.
2. Alternate coins file at startup swaps the monitored fleet without code changes.

#### Multi-DEX detection
1. V2 reserves probe identifies constant-product pools and factory mapping.
2. V3 fee tier read plus Maverick and Curve probes classify non-Uniswap layouts.

#### Swap log parsing
1. V2 swap decodes amount0In/Out and amount1In/Out from log data words.
2. V3 swap parses signed int128 amounts for direction and size.

#### Wallet watchlist reload
1. You switch to Mode 2 — watchlist reloads from disk before the poll loop starts.
2. CSV first column or bare `0x` lines both parse as wallet addresses.

#### Unknown-pool discovery
1. Watchlist tx hits swap topic on pool not in config — MODE2_SWAP_LOG alert with topic0 and decoded amounts.
2. Optional proof JSONL appends structured lines for later pool onboarding.

#### Swap enrichment
1. Full card shows gas used, effective gas price, TX fee USD, and EIP-1559 vs legacy type.
2. Pool context includes token symbols, liquidity USD both legs, and trade percent of pool.

#### USD pricing
1. ETH/USD from Binance ticker feeds notional on WETH legs.
2. Quote token rules handle USDC, USDT, DAI, and WBTC in pool math.

#### Quote ladders
1. Nine USD notionals generate buy/sell effective prices per pool on expand.
2. V3 pools use on-chain quoter simulation; V2 uses constant-product math.

#### Address and whale statistics
1. Each swap updates buy_count and sell_count for the trader address.
2. Top 20 table sorts by total_swaps for quick whale identification.

#### Alert feed
1. Two hundred fifty item ring prepends newest swap — older alerts drop with trim.
2. Accordion collapsed row shows route, USD, protocol; expanded shows gas and liquidity grid.

#### Real-time push
1. `new_alert` WebSocket event delivers full swap object on each confirmed decode.
2. `block_update` pushes latest head and blocks_checked counter.

#### REST control API
1. POST mode switch hot-restarts ingestion loop without killing uvicorn.
2. Toggle monitoring pauses ingestion while dashboard stays up for review.

#### Dashboard UX (primary)
1. Three themes persist in browser storage across sessions.
2. Feed freeze pauses alert DOM updates while socket keeps running for catch-up.

#### Persistence
1. SQLite transactions table stores twenty-plus columns per swap for history queries.
2. Throttled JSON saves write stats and alerts every few seconds unless forced.

#### Integration paths
1. GET quote tables with pool filter fetches single ladder for expand-on-click.
2. GET search by address returns buy/sell counts for the filter box.

#### Operational controls
1. UI-only flag starts dashboard without monitor thread for API testing.
2. Custom port bind serves multiple instances on one host for dev.

#### Debug and diagnostics
1. Debug flag verbose-logs pool detection failures during onboarding.
2. Mode2 debug first N txs emits structured debug alerts for receipt pipeline tuning.

#### Verification
1. Unit tests cover receipt log extraction for known and unknown pools.
2. Empirical script hits live mode and stats endpoints for smoke checks.

---

## What it does not do

- **Not** auto-trading — detection, enrichment, and alerts only
- **Not** a WordPress plugin — standalone operator service
- **Not** in-browser CSV export — data via REST and on-disk files
- **Not** pending-tx subscription in current code — confirmed log/receipt path only

Pool database, watchlist, and SQLite stay on your machine — not published in this overview repo.

---

## Tech stack

| Layer | Technologies |
|-------|----------------|
| Runtime | Python 3, FastAPI, uvicorn, asyncio |
| Chain | Web3.py, websockets, eth_defi quoter |
| UI | `dashboard.html` primary SPA + legacy `candy.html` |
| Pricing | Binance ETHUSDT REST |
| Persistence | SQLite, JSON/JSONL artifacts |
| Real-time | WebSocket to browser; chain WS or HTTP poll |

---

## Quick start

```bash
pip install web3 fastapi uvicorn websockets orjson eth-defi requests
python3 eth_chain_swaps_monitor.py --port 8059
```

Requires Ethereum RPC/WebSocket and configured pool database. See private repo README and [REPOS.md](REPOS.md).

---

## Related repositories

| Repository | Role |
|------------|------|
| [eth-chain-swaps-monitor](https://github.com/logicencoder/eth-chain-swaps-monitor) | Private application code |
| [eth-chain-swaps-monitor-overview](https://github.com/logicencoder/eth-chain-swaps-monitor-overview) | This product overview |
| [multi-coin-monitor-overview](https://github.com/logicencoder/multi-coin-monitor-overview) | MEXC fleet spread screener |
| [cex-dex-arb-overview](https://github.com/logicencoder/cex-dex-arb-overview) | Full CEX/DEX execution workstation |

See [REPOS.md](REPOS.md).

---

**Made by [Logic Encoder](https://logicencoder.com)** · [GitHub](https://github.com/logicencoder) · [Contact](https://logicencoder.com/contact/)
