# 001 - Initial Idea Transcript

**Date:** 2026-02-13
**Status:** Raw idea, pre-refinement

---

## The Problem

When reading the news, it's very difficult to filter intentionally inciteful content from real, unbiased facts. As a result:

- Readers develop a **skewed perception of reality** and of other people's opinions
- Example: assuming Trump is universally revered in America just because he was elected
- News companies have **misaligned objectives** — engagement, revenue, political leaning — that diverge from neutral reporting
- Important context or counter-narratives often go **unreported**

## Core Insight

Prediction markets represent a form of **crowdsourced, financially-incentivized truth**. People putting real money on outcomes tend to be more honest than pundits or headlines. This makes prediction market data a powerful antidote to media bias.

## Proposed Solution

A tool (browser extension or overlay) that augments news articles with real-time prediction market data from **Polymarket** and **Kalshi**.

## UX Flow

1. Open a news article
2. Read the article normally
3. **Manual mode:** Highlight a claim or interesting passage with your cursor → see relevant prediction markets/bets for that specific claim
4. **Auto mode:** The tool automatically surfaces the most relevant prediction markets for the article, ranked by:
   - Volume traded
   - Frequency/recency of trades
   - Relevance to the headline
   - Relevance to the article's core claims (via vector embedding or NLP)

## APIs to Explore

- **Polymarket** — decentralized prediction market, likely richer data on political/world events
- **Kalshi** — CFTC-regulated, US-based, may have different market categories

## Open Questions

- What data do these APIs actually expose? (markets, prices, volume, resolution criteria, comments?)
- Browser extension vs. standalone app vs. mobile?
- How to match article claims to prediction markets? (keyword matching, embeddings, LLM-based extraction?)
- Should it show historical price movement (i.e., how consensus shifted over time)?
- Could it also pull from other "truth signals" beyond prediction markets? (polling data, fact-check databases, etc.)
- Monetization? Or purely a personal/open-source tool?
- Name: "MediaBuster" — working title, open to change

## Why This Matters

The gap between what media reports and what people actually believe (backed by money) is a powerful signal. Making this visible at the point of consumption could fundamentally change how people process news.
