# 002 - API Research

**Date:** 2026-02-13
**Status:** Research complete, decision made

---

## Decision: Polymarket only (for now)

Kalshi's data terms prohibit public display/redistribution without written consent. Polymarket has no such restriction and actively encourages builders. Can revisit Kalshi later if needed.

## Polymarket API — What We're Working With

### Three services (all free, no auth for reads)

| Service | Base URL | Purpose |
|---|---|---|
| Gamma | `https://gamma-api.polymarket.com` | Markets, events, tags, search |
| CLOB | `https://clob.polymarket.com` | Prices, order books, price history |
| WebSocket | `wss://ws-subscriptions-clob.polymarket.com/ws/` | Real-time price streaming |

### Key endpoints for our use case

| Endpoint | Service | What it gives us |
|---|---|---|
| `GET /markets` | Gamma | Search/filter markets by keyword, tag, volume, date |
| `GET /events` | Gamma | Grouped markets under a single event |
| `GET /tags` | Gamma | All available categories/tags |
| `GET /prices-history` | CLOB | Historical price movement for charts |
| `GET /book` | CLOB | Full order book depth |
| `GET /midpoint` | CLOB | Quick consensus price |
| `market` channel | WebSocket | Real-time price/trade updates |

### Key data fields per market

- `question` — the actual prediction question (e.g., "Will X happen by Y?")
- `outcomePrices` — current Yes/No prices (implied probability)
- `volume` / `volume24hr` / `volume1wk` — trade volume at various intervals
- `liquidity` — how much money is backing the market
- `bestBid` / `bestAsk` / `spread` — order book state
- `lastTradePrice` / `oneDayPriceChange` — recent movement
- `description` — full market description
- `resolutionSource` — where the outcome is verified
- `tags` — categories for filtering
- `endDate` — when the market resolves

### Rate limits

| Service | Limit |
|---|---|
| Gamma (general) | 4,000 req/10s |
| CLOB (general) | 9,000 req/10s |
| CLOB (market data) | 500–1,500 req/10s |

### SDKs

- Python: `pip install py-clob-client`
- TypeScript: `npm i @polymarket/clob-client`
- Rust: `rs-clob-client`
- Community: `polymarket-apis` (Python, unified client)

## Kalshi API — Parked

- CFTC-regulated, rich data, good WebSocket support
- **Blocked by:** Data Terms of Use prohibit public display without written consent
- **Action if needed later:** Contact Kalshi developer program for permission

## Open Questions

- Which Polymarket endpoint is best for **search/matching** article claims to markets? (`/markets` with query params? or fetch all and embed locally?)
- How stale is the Gamma API data vs CLOB? (Do we need WebSocket for our use case, or is polling sufficient?)
- What does the `tags` taxonomy look like? (Could help with coarse-grained article-to-market matching)
