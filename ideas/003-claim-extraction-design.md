# 003 - Claim Extraction & Polymarket Search Design

**Date:** 2026-02-13
**Status:** Design complete, not yet implemented

---

## Critical API Discovery

| Endpoint | Text search? | Use for |
|---|---|---|
| `/markets?_q=...` | **No** — `_q` silently ignored | Filter by ID, tag, volume, date only |
| `/search?query=...` | Yes but **401 — private** | Not available |
| **`/public-search?q=...`** | **Yes — primary search** | Free-text, no auth, returns events + markets + tags |
| `/events?tag_slug=...` | Tag-based browsing | Broad topic discovery |

## Pipeline

```
highlighted text
    → LLM claim extractor (API-agnostic interface)
        → search_queries sent to /public-search?q=...
        → tag_slugs sent to /events?tag_slug=...
    → results merged, ranked, displayed
```

## LLM Interface Contract

**Input:** `{ text: string, headline?: string }`

**Output:**
```json
{
  "claims": ["string"],
  "search_queries": ["string"],
  "tag_slugs": ["string"]
}
```

- `claims` — falsifiable, time-bound assertions (explicit + implicit)
- `search_queries` — 2-5 word phrases optimized for Polymarket `/public-search`
- `tag_slugs` — lowercase, hyphenated topic categories for `/events?tag_slug=`

The LLM backend is **swappable** — start with Claude/GPT API, can swap to local model later.

## Extraction Prompt (v1)

```
You are a claim extractor for a prediction market search tool.

Given a passage of text from a news article, extract:

1. CLAIMS: Factual assertions that could be verified by a prediction market.
   Focus on falsifiable, time-bound statements. Extract BOTH explicit
   and implicit claims.

2. SEARCH_QUERIES: Short search phrases (2-5 words each) optimized for
   searching prediction markets. These should capture the core subject,
   not the full nuance.

3. TAG_SLUGS: Broad topic categories. Use lowercase, hyphenated slugs
   (e.g., "tariffs", "trump-presidency", "us-economy").

Respond in JSON only.

---

PASSAGE:
"{highlighted_text}"

ARTICLE HEADLINE (if available):
"{headline}"
```

## Example

**Input:**
> "Trump's new tariffs on Chinese goods are expected to devastate the US tech sector, with analysts predicting a 15% drop in semiconductor stocks by Q3."

**Output:**
```json
{
  "claims": [
    "Trump will impose new tariffs on Chinese goods",
    "US tech sector will decline due to tariffs",
    "Semiconductor stocks will drop 15% by Q3"
  ],
  "search_queries": [
    "Trump tariffs China",
    "US tech sector tariffs",
    "semiconductor stocks decline"
  ],
  "tag_slugs": [
    "tariffs",
    "trump-presidency",
    "us-economy",
    "technology"
  ]
}
```

## Polymarket Search Details

### `/public-search` parameters

| Param | Type | Description |
|---|---|---|
| `q` | string | Free-text search query (required) |
| `limit_per_type` | int | Max results per type (events, tags, profiles) |
| `events_status` | string | `"active"` to filter live markets |
| `events_tag` | string[] | Filter by event tags |
| `sort` | string | Sort field |
| `ascending` | bool | Sort direction |

### `/events` parameters (for tag-based browsing)

| Param | Type | Description |
|---|---|---|
| `tag_slug` | string | Topic slug (e.g., "tariffs") |
| `active` | bool | Only active events |
| `closed` | bool | Only closed events |
| `order` | string | Sort field (e.g., "volume") |
| `ascending` | bool | Sort direction |
| `limit` | int | Max results |

### Known useful tag slugs

`tariffs`, `trade-war`, `trump-presidency`, `trump`, `politics`, `china`, `us-economy`, `technology`

## Open Questions

- How many search queries should the LLM generate per highlight? (too many = slow, too few = miss markets)
- Should we deduplicate results across `/public-search` and `/events` responses?
- What's the latency budget? (LLM extraction + API call + render)
- How do we rank results when multiple markets match? (volume? recency? relevance score?)
