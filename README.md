# ETH Chain Swaps Monitor (Standalone) — Overview

> This repository is a public overview of a private implementation.
> It excludes sensitive source internals and operational secrets.

## Positioning

This is a **standalone local-first monitoring application** for Ethereum swap and transaction flow tracking.
It is not a hosted SaaS platform; heavy processing runs on dedicated infrastructure under my control.

- Current delivery mode: **standalone local-first monitor**
- Live website mode: **planned (public link will be added when release is ready)**
- Live URL: `Coming soon`

## UI Snapshot

![ETH Chain Swaps Monitor UI](assets/eth-chain-swaps-monitor-overview.png)

## What this project is

A realtime on-chain monitoring console focused on swap detection, transaction interpretation, and operator visibility.
It combines Python collectors with a live dashboard for fast triage and investigation.

## Tech Stack Used

- **Backend**: Python, FastAPI, async event handling, Web3 integration
- **Realtime transport**: WebSocket stream (`/ws`) + REST endpoints for dashboard queries
- **Frontend**: HTML/CSS/JavaScript dashboard views (`dashboard.html`, `dashboard1.html`, `candy.html`)
- **Data artifacts**: JSON knowledge stores (method signatures, pool discovery) + runtime stats persistence
- **Infra style**: local-first processing with controlled public exposure

## High-Level Architecture

- **Collector Layer**: chain event ingestion + transaction decoding
- **Interpretation Layer**: swap parser routing across multiple pool/protocol patterns
- **Service Layer**: FastAPI endpoints for stats, mode controls, search, and feed retrieval
- **Realtime Layer**: WebSocket broadcast for instant dashboard updates
- **UI Layer**: operator dashboard for live alerts, quote tables, terminal feed, and address search

## Feature Inventory

### A) Monitoring Runtime

1. Realtime transaction monitoring loop
2. Toggle monitoring without process teardown
3. Runtime mode switching via API
4. Terminal feed on/off toggle
5. Live block progression visibility
6. Transaction-in-block position tracking
7. Address-centric event aggregation
8. Watched-wallet support pipeline

### B) Swap Interpretation & Parsing

9. Universal swap parse entrypoint
10. V2 swap parser support
11. V3 swap parser support
12. Curve swap parser support
13. DODO swap parser support
14. Maverick swap parser support
15. Method signature normalization
16. Method signature metadata loading
17. Pool type detection pipeline
18. DEX identification helpers

### C) Data Products & APIs

19. `/api/stats` operational stats endpoint
20. `/api/alerts` alert feed endpoint
21. `/api/search/{address}` address investigation endpoint
22. `/api/quote_tables` quote table endpoint
23. `/api/mode` mode get/set endpoints
24. `/api/toggle_monitoring` control endpoint
25. `/api/toggle_terminal_log` control endpoint
26. Dashboard route endpoint (`/dashboard`)
27. Candy route endpoint (`/candy`)
28. Root route endpoint (`/`)

### D) Realtime UI & Operator Tools

29. WebSocket live stream endpoint (`/ws`)
30. Live alert feed panel
31. Live terminal stream panel
32. Search-driven address drill-down
33. Top-addresses table
34. Quote table rendering
35. Mode selector controls
36. Status indicator panel
37. Expand/collapse behavior for dense feeds
38. Manual refresh controls for quote data

### E) Reliability & Maintenance

39. Database initialization bootstrap
40. Alerts load/save persistence
41. Stats load/save persistence
42. Quote-table caching/warmup logic
43. Legacy quote migration utility
44. Broadcast fanout helper for multi-clients
45. WebSocket reconnect-oriented service design
46. Compatible with iterative parser extension

## Target Audience

### Recruiters

This shows practical backend + realtime engineering ability from real shipped tooling, not only tutorial projects.

### System Engineers

This shows event ingestion, decoding pipelines, WebSocket/REST hybrid service shape, and operator-facing observability.

### Collaborators / Employers

This shows how I build and learn independently: start from a concrete problem, ship a working monitor, then iterate architecture and parsing coverage.

## Working Style (Grounded)

- Self-taught, built incrementally from zero
- Focus on delivery and measurable behavior
- Learn missing pieces fast when requirements grow
- Prefer practical system understanding over theory-only claims

## Security & Disclosure

- Private source internals remain private
- No credentials, private endpoints, or sensitive infra details are published here
- This repository is strictly a non-sensitive architecture and capability overview

## Related Private Implementation

- Private app repo: `logicencoder/eth_chain_swaps_monitor`
