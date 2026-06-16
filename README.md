# Ethereum Chain Swap Monitor

![Ethereum Chain Swap Monitor — dark theme](assets/dashboard-featured-dark.png)

Real-time **Ethereum swap monitor** with a compact operator dashboard. The service ingests chain events over WebSocket subscriptions or HTTP block polling, decodes pool swap activity across multiple AMM signatures, enriches events with USD context and quote ladders, and pushes updates to the browser in real time.

Private source: [logicencoder/eth-chain-swaps-monitor](https://github.com/logicencoder/eth-chain-swaps-monitor). RPC keys and wallet lists stay on your infrastructure — not in this public overview.

## Tech stack

| Layer | Technologies |
|-------|--------------|
| Core service | Python + FastAPI |
| Chain ingestion | Web3.py — WebSocket `logs` and HTTP block polling |
| Persistence | SQLite (`whale_monitor.db`) + rolling JSON/JSONL artifacts |
| UI | Single-page `dashboard.html` (CSS/JS inline) — primary operator UI |
| Streaming | REST + WebSocket (`/ws`) on port **8059** (configurable) |

## Dashboard overview

The default UI is **Ethereum Chain Swap Monitor** (`dashboard.html`). It is optimized for long monitoring sessions: dense typography, two-column layout, accordion swap cards, and three visual themes (**Light**, **Medium**, **Dark**). Operator preferences (theme, auto-expand, freeze toggles, scroll position, expanded swap, panel collapse, quote sort, monitor mode) persist in browser `localStorage` under `ecs-dashboard-prefs`.

### Visual themes — Dark, Medium, Light

**Dark** is the recommended theme for low-glare night work — slate panels on a charcoal background with high-contrast buy/sell chips. Cycle themes from the header toolbar or switch at runtime; the choice is saved locally.

| Theme | Screenshot |
|-------|------------|
| **Dark** (featured) | Hero image above — full dashboard with live stats, alerts feed, quote tables, and terminal |
| **Medium** | ![Medium theme](assets/dashboard-theme-medium.png) |
| **Light** | ![Light theme](assets/dashboard-theme-light.png) |

**Medium** uses layered blue panels on a soft page background for comfortable daytime contrast. **Light** is a neutral gray-white layout for bright rooms and screenshots.

### Header — live stats, controls, and monitor mode

The top band combines **session telemetry**, **transport controls**, a **monitor mode selector**, and a **live swap preview** of the currently focused alert.

| Stat | Meaning |
|------|---------|
| **Pools** | Configured liquidity pools loaded from discovery metadata |
| **Blocks** | Blocks scanned in the current monitor session |
| **Swaps** | Swap events detected in the current session |
| **Block** | Latest chain head seen by the poller/subscriber |
| **TX in block** | Transaction count in that head block |
| **Monitoring on** | Ingestion active (green) or paused |
| **Mode / Watched** | Active monitor mode and watchlist size (modes 2 and 4) |

Toolbar actions:

- **Stop monitoring** — pauses chain ingestion via `POST /api/toggle_monitoring`; WebSocket stays connected.
- **Disable terminal** / **Clear terminal** — control the on-page log mirror.
- **Auto-expand: ON/OFF** — when ON, each new swap opens as the expanded accordion card.
- **Refresh quotes** — reload saved quote tables from `/api/quote_tables`.
- **Theme: Light / Medium / Dark** — cycles visual theme.
- **Monitor mode** dropdown + **Apply mode** — switches ingestion strategy at runtime (see below).

The right-hand **swap preview** mirrors the expanded or selected alert as a compact mini-card (route, chips, amounts).

### Top addresses — repeat trader ranking

The left column ranks wallets by activity (`swap_stats.json`) with sortable columns: **Buys**, **Sells**, **Total Swaps**, **Last Swap**, **Last TX** (European `DD/MM/YYYY` dates). Address cells link to **Etherscan**. The panel collapses; state is remembered.

### Live alerts — accordion swap feed

![Expanded swap card — dark theme](assets/dashboard-dark-feed.png)

The **Live alerts** feed is a scrollable stack of swap cards fed by `/api/alerts` and live `new_alert` WebSocket events. Expanded cards show block/protocol chips, token route, trader address and transaction hash (both Etherscan links), and a three-column detail matrix (liquidity, transaction, fees).

**Freeze** on the alerts header pauses feed re-rendering while WebSocket ingestion continues — independent from quote-table freeze.

### Saved quote tables

![Saved quote tables — dark theme](assets/dashboard-dark-quotes.png)

**Saved quote tables** list per-pool trade-size ladders with sort options (**Latest first**, coin name, liquidity). **Freeze** on this panel stops quote UI updates only.

### Live terminal

![Live terminal — dark theme](assets/dashboard-dark-terminal.png)

The **terminal** mirrors server log lines and WebSocket status (connect, theme changes, mode switches, quote updates). It can be disabled or cleared without stopping the monitor.

## Monitoring modes (backend)

| Mode | Transport | Behavior |
|------|-----------|----------|
| **1** — pool swaps | WebSocket `logs` | Subscriptions on configured pool addresses filtered to known swap topic signatures; pending txs and new heads |
| **2** — wallet receipts | HTTP block poll | For each address in your watchlist: scan blocks, fetch receipts, detect swap logs |
| **3** — all pool logs | WebSocket | Same pools as mode 1 without topic filter |
| **4** — wallet all tx | HTTP poll | Watchlist-centric block scan (same family as mode 2) |

Switch at runtime via the dashboard **Apply mode** control or `POST /api/mode`. The service hot-restarts the ingestion loop so **active** mode matches **desired** immediately; modes **2** and **4** reload the wallet watchlist without a full process restart.

## Event enrichment and persistence

Each confirmed swap can include buy/sell direction, USD size (ETH price from Binance), gas paid, LP fee estimate, liquidity snapshot, and **quote tables** at standard notionals.

| Store | Role |
|-------|------|
| `whale_monitor.db` | Durable transaction history |
| `swap_stats.json` | Per-address buy/sell totals — powers Top addresses |
| `recent_alerts.json` | Ring buffer for dashboard feed |
| `quotes/` + index | Saved quote ladders |

Session counters (**Blocks**, **Swaps** in the header) live in process memory and reset on service restart; ranked addresses, alerts history, and SQLite data survive restarts.

## API surface

| Endpoint | Role |
|----------|------|
| `GET /` | Serves `dashboard.html` |
| `GET /candy` | Legacy candy dashboard (optional) |
| `GET /api/stats` | Aggregated statistics + top addresses + mode2 counters |
| `GET /api/alerts` | Recent alert ring buffer |
| `GET /api/search/{address}` | History for an address |
| `GET /api/quote_tables` | Trade-size ladder quotes |
| `GET/POST /api/mode` | Read or change monitoring mode (hot restart) |
| `POST /api/toggle_monitoring` | Pause/resume ingestion |
| `WebSocket /ws` | Live push (`new_alert`, `stats_update`, `quote_table_update`, `terminal_log`) |

## Operator value

The monitor compresses a noisy raw-chain stream into actionable alerts with context:

- runtime mode switching for pool-focused or wallet-focused workflows
- three dashboard themes with local layout memory and independent freeze controls
- consistent event payloads across V2/V3 and alternate AMM layouts
- immediate UI broadcast for alert triage
- persistent history for later investigation and pattern analysis

## Quick start

```bash
python3 eth_chain_swaps_monitor.py --local
# Dashboard: http://localhost:8059/
```

Common flags: `--geth-custom`, `--drpc`, `--infura`, `--alchemy`, `--monitor-mode`, `--coins-file`, `--port`, `--ui-only`, `--interactive`.

See the private repo README for the full flag list and [REPOS.md](REPOS.md) for repository links.

---

**Made by [Logic Encoder](https://logicencoder.com)** · [GitHub](https://github.com/logicencoder) · [Contact](https://logicencoder.com/contact/)
