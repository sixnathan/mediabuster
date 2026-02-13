# 004 - UX & Form Factor Design

**Date:** 2026-02-13
**Status:** Design complete, not yet implemented

---

## Form Factor Decision

**Phase 1:** Static web app (no backend, user provides LLM API key)
**Phase 2:** Chrome extension (port core logic, add highlight-to-search)

Porting is low-effort because the core logic (extract, search, rank) is three pure functions that work identically in both environments. Extension adds: manifest.json, content script for text selection, sidepanel for display, chrome.storage.local for API key.

## Web App UX

### Layout

```
┌─────────────────────────────────────────────────┐
│  MediaBuster                        [Settings ⚙]│
├─────────────────────────────────────────────────┤
│                                                  │
│  ┌─────────────────────────────────────────────┐│
│  │ Paste article text here...                  ││
│  │                                             ││
│  │                                             ││
│  └─────────────────────────────────────────────┘│
│                                                  │
│  [Headline (optional): ________________________]│
│                                                  │
│  [Analyze]                                       │
│                                                  │
├─────────────────────────────────────────────────┤
│  Results (3 markets found)                       │
│                                                  │
│  ┌─────────────────────────────────────────────┐│
│  │ Will Trump impose tariffs on China by Q3?   ││
│  │ YES: 72%  ·  NO: 28%  ·  Vol: $1.2M        ││
│  └─────────────────────────────────────────────┘│
│                                                  │
│  ┌─────────────────────────────────────────────┐│
│  │ US recession in 2026?                       ││
│  │ YES: 34%  ·  NO: 66%  ·  Vol: $4.5M        ││
│  └─────────────────────────────────────────────┘│
│                                                  │
│  ┌─────────────────────────────────────────────┐│
│  │ Semiconductor stocks drop >10% by Q3?       ││
│  │ YES: 18%  ·  NO: 82%  ·  Vol: $230K        ││
│  └─────────────────────────────────────────────┘│
│                                                  │
└─────────────────────────────────────────────────┘
```

### Per-Market Card — Minimal Display

| Field | Source | Format |
|---|---|---|
| Market question | Polymarket `question` | Bold text, full question |
| Yes probability | `outcomePrices[0]` | "YES: 72%" — green if >50%, red if <50% |
| No probability | `outcomePrices[1]` | "NO: 28%" |
| Volume | `volume` | "$1.2M" — formatted, acts as credibility indicator |

Click on a card → opens the market on Polymarket in a new tab.

### Settings Panel

- LLM API key input (stored in localStorage, never sent to any server)
- LLM provider selector (Claude / OpenAI / custom)
- Optional: number of results to show

### Input Modes

1. **Paste text** — paste article body into textarea, click Analyze
2. **Paste URL** — enter a URL, app fetches and parses the article (may have CORS issues — could use a proxy or skip for MVP)
3. **Highlight within textarea** — select a portion of pasted text to analyze just that section (stretch goal)

### MVP scope

For v1, support **paste text only**. URL fetching and in-text highlighting are stretch goals.

## Extension UX (Phase 2)

1. User is on a news article
2. Highlights text with cursor
3. Small floating button appears near selection: [🔍 Check Markets]
4. Click → sidepanel opens with results (same card format as web app)
5. Auto-mode: extension icon shows a badge with number of relevant markets for the current page

## Architecture — Shared Core

```
extractClaims(text, headline?) → { claims, search_queries, tag_slugs }
searchMarkets(queries, tags) → Market[]
rankResults(markets) → Market[]
```

These three functions are identical between web app and extension.

## Open Questions

- Should results link to Polymarket directly, or show an embedded mini-view?
- How to handle "no markets found" gracefully?
- Should we cache results for repeated queries?
- Color coding: green/red for Yes/No probability, or neutral?
