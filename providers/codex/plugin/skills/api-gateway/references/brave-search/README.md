# Brave Search Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `brave-search`
**Base URL proxied:** `api.search.brave.com`

## API Path Pattern

```
/brave-search/res/v1/{resource}
```

## Web Search

### Search
```bash
GET /brave-search/res/v1/web/search?q={query}&count=10
```

## Image Search

### Images
```bash
GET /brave-search/res/v1/images/search?q={query}&count=10
```

## News Search

### News
```bash
GET /brave-search/res/v1/news/search?q={query}&count=10
```

## Video Search

### Videos
```bash
GET /brave-search/res/v1/videos/search?q={query}&count=10
```

## Local Search

### Local POIs
```bash
GET /brave-search/res/v1/local/pois?ids={poi_ids}
```

### POI Descriptions
```bash
GET /brave-search/res/v1/local/descriptions?ids={poi_ids}
```

## Autosuggest (Requires Subscription)

### Suggest
```bash
GET /brave-search/res/v1/suggest/search?q={query}&count=5
```

## Spellcheck (Requires Subscription)

### Spellcheck
```bash
GET /brave-search/res/v1/spellcheck/search?q={query}&country=US
```

## Summarizer (Requires Subscription)

### Summarizer Search
```bash
GET /brave-search/res/v1/summarizer/search?key={summarizer_key}
```

### Summary Only
```bash
GET /brave-search/res/v1/summarizer/summary?key={key}
```

### Title Only
```bash
GET /brave-search/res/v1/summarizer/title?key={key}
```

### Enrichments
```bash
GET /brave-search/res/v1/summarizer/enrichments?key={key}
```

### Follow-ups
```bash
GET /brave-search/res/v1/summarizer/followups?key={key}
```

### Entity Info
```bash
GET /brave-search/res/v1/summarizer/entity_info?key={key}
```

## Query Parameters

### Common Parameters
- `q` (required): Search query (1-400 characters, max 50 words)
- `country`: 2-letter country code (default: "US")
- `search_lang`: Search language code (default: "en")
- `count`: Results per page, 1-20 (default: 20)
- `offset`: Page offset, 0-9 (default: 0)
- `safesearch`: Filter level - "off", "moderate", "strict"
- `freshness`: Time filter - "pd", "pw", "pm", "py"

### Location Headers

> **Privacy — these headers transmit the user's physical location.** `x-loc-lat`/`x-loc-long` are precise coordinates and, with city/state/postal code, can identify a home or workplace. They are sent to Brave Search on every request that includes them.
> - Only send location headers when the user's request is genuinely location-dependent (e.g. "restaurants near me") and the user has supplied or approved the location.
> - Never infer coordinates from the host machine, IP, system settings, or a previous unrelated request, and never populate them silently.
> - Prefer the coarsest value that satisfies the query — city or country rather than exact lat/long.
> - Do not log these values or carry them over into later requests.

- `x-loc-lat`: Latitude — precise coordinate, treat as personal data
- `x-loc-long`: Longitude — precise coordinate, treat as personal data
- `x-loc-city`: City name
- `x-loc-state`: State/province
- `x-loc-country`: Country code
- `x-loc-postal-code`: Postal code

## Response Format

All Brave Search API responses include:

```json
{
  "type": "search",
  "query": {
    "original": "query string",
    "country": "us",
    "more_results_available": true
  },
  "web": {
    "results": [...]
  },
  "news": {...},
  "videos": {...},
  "discussions": {...}
}
```

## Notes

- Maximum 20 results per request
- Maximum 10 pages (offset 0-9)
- Privacy-focused search engine
- Results include web, news, videos, discussions, FAQ, infobox
- Uses API key authentication
- Some endpoints require additional subscription plans

## Resources

- [Brave Search API Documentation](https://api-dashboard.search.brave.com/documentation)
- [Brave Search API Dashboard](https://api-dashboard.search.brave.com/)
