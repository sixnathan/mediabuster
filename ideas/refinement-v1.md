# Refinement v1 — MediaBuster

**Date:** 2026-02-13
**Status:** Planning complete, ready for MVP build

---

## What is MediaBuster?

A tool that augments news articles with prediction market data from Polymarket. When you read a news article, MediaBuster shows you what people are actually betting on — a financially-incentivized signal that cuts through media bias.

## Problem

- News media has misaligned incentives (engagement > accuracy)
- Readers develop skewed perceptions of reality and public opinion
- Important context often goes unreported

## Solution

Paste article text → LLM extracts claims → Polymarket shows what people bet on those claims.

## Architecture

```
Article text
    ↓
extractClaims(text, headline?)     ← LLM API (Claude/GPT, swappable)
    ↓
{ claims, search_queries, tag_slugs }
    ↓
searchMarkets(queries, tags)       ← Polymarket /public-search + /events
    ↓
rankResults(markets)               ← sort by volume, relevance
    ↓
Display: market question + Yes/No % + volume
```

### Three core functions (shared between web app and extension)

| Function | Input | Output |
|---|---|---|
| `extractClaims` | text, headline? | claims[], search_queries[], tag_slugs[] |
| `searchMarkets` | queries[], tags[] | Market[] |
| `rankResults` | Market[] | Market[] (sorted) |

## Tech Decisions

| Decision | Choice | Reason |
|---|---|---|
| API | Polymarket only | Open data, no auth for reads, builder-friendly. Kalshi's terms prohibit public display. |
| Search endpoint | `/public-search?q=...` | Only Polymarket endpoint that supports free-text search. `/markets?_q=` is silently ignored. |
| Supplementary | `/events?tag_slug=...` | Broader topic discovery, sorted by volume |
| LLM approach | API-agnostic interface | Start with Claude/GPT API, swap to local model later |
| Form factor | Static web app → Chrome extension | Web app first (fastest to build), extension later (better UX). Port is low-effort. |
| Display | Minimal | Market question + Yes/No probability + volume per card |
| Auth | None | User provides their own LLM API key (stored in localStorage). Polymarket needs no auth. |

## MVP Scope (Web App v1)

**In scope:**
- Paste article text into textarea
- Optional headline field
- LLM extracts claims and generates search queries
- Polymarket `/public-search` returns matching markets
- Results displayed as cards: question, probability, volume
- Click card → opens market on Polymarket
- Settings: LLM API key, provider selector

**Out of scope (future):**
- URL input with article fetching/parsing
- In-textarea text highlighting
- Auto-mode (analyze full article without manual input)
- Price history charts / sparklines
- Chrome extension port
- Multiple prediction market sources

## Key Files

| File | Contents |
|---|---|
| `001-initial-transcript.md` | Raw idea, problem statement, original UX vision |
| `002-api-research.md` | Full Polymarket & Kalshi API details, rate limits, SDKs |
| `003-claim-extraction-design.md` | LLM prompt, pipeline, search endpoint discovery |
| `004-ux-design.md` | Form factor decision, wireframe, shared architecture |
| `refinement-v1.md` | This file — distilled decisions |

## Open Questions for Next Session

- Which LLM provider to start with? (Claude vs OpenAI — depends on pricing and structured output quality)
- How to rank results when multiple markets match?
- How to handle "no markets found"?
- Tech stack for web app? (React? Vanilla? Next.js?)
- Should results cache?
