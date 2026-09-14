# Brave Search

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `brave-search`
**Upstream base URL:** `api.search.brave.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.search.brave.com/res/v1/web/search`
- Gateway: `https://api.maton.ai/brave-search/res/v1/web/search`

### Web Search API

```bash
maton api '/brave-search/res/v1/web/search?q={query}'
```

**Note:** `{query}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `q` (string, required): Search query (1-400 characters, max 50 words)
- `country` (string, optional): 2-letter country code (the API defaults to "US" — that is Brave's default, not a setting this skill mandates; pass the country, search language, and UI language that match the user's locale, and ask when it is not stated)
- `search_lang` (string, optional): Search language code (default: "en")
- `ui_lang` (string, optional): UI language in RFC 9110 format (default: "en-US")
- `count` (integer, optional): Results per page, 1-20 (default: 20)
- `offset` (integer, optional): Page offset, 0-9 (default: 0)
- `safesearch` (string, optional): Filter level - "off", "moderate", "strict" (default: "moderate")
- `freshness` (string, optional): Time filter - "pd" (past day), "pw" (past week), "pm" (past month), "py" (past year), or date range
- `text_decorations` (boolean, optional): Include highlighting markers (default: true)
- `result_filter` (string, optional): Comma-separated result types (discussions, faq, infobox, news, videos, web)
- `extra_snippets` (boolean, optional): Get up to 5 alternative excerpts
- `summary` (boolean, optional): Enable summarizer

**Example:**
```bash
maton api '/brave-search/res/v1/web/search?q=machine+learning&count=10&freshness=pw'
```

**Response:**
```json
{
  "type": "search",
  "query": {
    "original": "machine learning",
    "show_strict_warning": false,
    "is_navigational": false,
    "country": "us",
    "more_results_available": true
  },
  "web": {
    "type": "search",
    "results": [
      {
        "title": "Machine Learning - Wikipedia",
        "url": "https://en.wikipedia.org/wiki/Machine_learning",
        "description": "Machine learning is a subset of artificial intelligence...",
        "language": "en",
        "family_friendly": true
      }
    ]
  },
  "discussions": {...},
  "faq": {...},
  "videos": {...}
}
```

### Image Search API

```bash
maton api '/brave-search/res/v1/images/search?q={query}'
```

**Note:** `{query}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `q` (string, required): Search query
- `country` (string, optional): 2-letter country code
- `search_lang` (string, optional): Search language code
- `count` (integer, optional): Results per page, 1-20
- `safesearch` (string, optional): Filter level - "off", "moderate", "strict"

**Example:**
```bash
maton api '/brave-search/res/v1/images/search?q=sunset&count=5'
```

**Response:**
```json
{
  "type": "images",
  "results": [
    {
      "title": "Beautiful Sunset",
      "url": "https://example.com/sunset.jpg",
      "source": "https://example.com/gallery",
      "thumbnail": {
        "src": "https://imgs.search.brave.com/..."
      },
      "properties": {
        "width": 1920,
        "height": 1080,
        "format": "jpeg"
      }
    }
  ]
}
```

### News Search API

```bash
maton api '/brave-search/res/v1/news/search?q={query}'
```

**Note:** `{query}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `q` (string, required): Search query
- `country` (string, optional): 2-letter country code
- `search_lang` (string, optional): Search language code
- `count` (integer, optional): Results per page, 1-20
- `freshness` (string, optional): Time filter - "pd", "pw", "pm", "py"
- `safesearch` (string, optional): Filter level

**Example:**
```bash
maton api '/brave-search/res/v1/news/search?q=technology&count=5&freshness=pd'
```

**Response:**
```json
{
  "type": "news",
  "results": [
    {
      "title": "Latest Tech News",
      "url": "https://example.com/news/tech",
      "description": "Breaking technology news...",
      "age": "2 hours ago",
      "source": {
        "name": "Tech News",
        "url": "https://technews.com"
      },
      "thumbnail": {
        "src": "https://imgs.search.brave.com/..."
      }
    }
  ]
}
```

### Video Search API

```bash
maton api '/brave-search/res/v1/videos/search?q={query}'
```

**Note:** `{query}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `q` (string, required): Search query
- `country` (string, optional): 2-letter country code
- `search_lang` (string, optional): Search language code
- `count` (integer, optional): Results per page, 1-20
- `safesearch` (string, optional): Filter level

**Example:**
```bash
maton api '/brave-search/res/v1/videos/search?q=tutorial&count=5'
```

**Response:**
```json
{
  "type": "videos",
  "results": [
    {
      "title": "Python Tutorial for Beginners",
      "url": "https://www.youtube.com/watch?v=...",
      "description": "Learn Python programming...",
      "age": "1 year ago",
      "duration": "3:45:00",
      "thumbnail": {
        "src": "https://imgs.search.brave.com/..."
      },
      "meta_url": {
        "hostname": "www.youtube.com"
      }
    }
  ]
}
```

### Local POIs API

```bash
maton api '/brave-search/res/v1/local/pois?ids={poi_ids}'
```

**Note:** `{poi_ids}` is a placeholder. Replace it with a real value before sending the request.

Get details about local points of interest by their IDs (obtained from web search results).

**Query parameters:**
- `ids` (string, required): Comma-separated POI IDs

**Example:**
```bash
maton api '/brave-search/res/v1/local/pois?ids=poi_123,poi_456'
```

**Response:**
```json
{
  "type": "local_pois",
  "results": [
    {
      "id": "poi_123",
      "name": "Coffee Shop",
      "address": "123 Main St",
      "phone": "+1-555-1234",
      "rating": 4.5,
      "reviews": 128
    }
  ]
}
```

### POI Descriptions API

```bash
maton api '/brave-search/res/v1/local/descriptions?ids={poi_ids}'
```

**Note:** `{poi_ids}` is a placeholder. Replace it with a real value before sending the request.

Get detailed descriptions for local points of interest.

**Query parameters:**
- `ids` (string, required): Comma-separated POI IDs

**Example:**
```bash
maton api '/brave-search/res/v1/local/descriptions?ids=poi_123'
```

**Response:**
```json
{
  "type": "local_descriptions",
  "results": [
    {
      "id": "poi_123",
      "description": "A cozy coffee shop known for artisanal brews..."
    }
  ]
}
```

### Autosuggest API

> **Note:** Requires Autosuggest subscription plan.

```bash
maton api '/brave-search/res/v1/suggest/search?q={query}'
```

**Note:** `{query}` is a placeholder. Replace it with a real value before sending the request.

Get search suggestions as users type.

**Query parameters:**
- `q` (string, required): Partial search query
- `country` (string, optional): 2-letter country code
- `count` (integer, optional): Number of suggestions to return
- `rich` (boolean, optional): Enable enhanced metadata

**Example:**
```bash
maton api '/brave-search/res/v1/suggest/search?q=how+to&count=5&rich=true'
```

**Response:**
```json
{
  "type": "suggest",
  "query": {
    "original": "how to"
  },
  "results": [
    {
      "query": "how to learn python",
      "is_entity": false
    },
    {
      "query": "how to code",
      "is_entity": false
    }
  ]
}
```

### Spellcheck API

> **Note:** Requires Spellcheck subscription plan.

```bash
maton api '/brave-search/res/v1/spellcheck/search?q={query}'
```

**Note:** `{query}` is a placeholder. Replace it with a real value before sending the request.

Check spelling and get corrections.

**Query parameters:**
- `q` (string, required): Query to check for spelling errors
- `country` (string, required): Country code for localized corrections

**Example:**
```bash
maton api '/brave-search/res/v1/spellcheck/search?q=helo+wrold&country=US'
```

**Response:**
```json
{
  "type": "spellcheck",
  "query": {
    "original": "helo wrold"
  },
  "results": [
    {
      "query": "hello world"
    }
  ]
}
```

### Summarizer Search API

> **Note:** Requires Summarizer subscription plan.

First, perform a web search with `summary=1` to get a summarizer key, then use that key to fetch the summary.

#### Get Summarizer Key

```bash
maton api '/brave-search/res/v1/web/search?q={query}&summary=1'
```

**Note:** `{query}` is a placeholder. Replace it with a real value before sending the request.

#### Fetch Summary

```bash
maton api '/brave-search/res/v1/summarizer/search?key={summarizer_key}'
```

**Note:** `{summarizer_key}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `entity_info` (boolean, optional): Include entity details
- `inline_references` (boolean, optional): Include citation markers

**Example:**
```bash
python3 <<'EOF'
import json, subprocess

def api(path):
    out = subprocess.run(['maton', 'api', path], capture_output=True, text=True, check=True).stdout
    return json.loads(out)

# Step 1: get the summarizer key from a web search
data = api('/brave-search/res/v1/web/search?q=what+is+python&summary=1')
key = data.get('summarizer', {}).get('key')

# Step 2: fetch the summary with that key
if key:
    print(json.dumps(api(f'/brave-search/res/v1/summarizer/search?key={key}'), indent=2))
EOF
```

#### Additional Summarizer Endpoints

```bash
maton api '/brave-search/res/v1/summarizer/summary?key={key}'  # Summary only

maton api '/brave-search/res/v1/summarizer/title?key={key}'  # Title only

maton api '/brave-search/res/v1/summarizer/enrichments?key={key}'  # Enrichment data

maton api '/brave-search/res/v1/summarizer/followups?key={key}'  # Follow-up suggestions

maton api '/brave-search/res/v1/summarizer/entity_info?key={key}'  # Entity information
```

**Note:** `{key}` is a placeholder. Replace it with a real value before sending the request.

### Response Format

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

### Pagination

Use `count` and `offset` for pagination:

```bash
# First page (results 1-10)
maton api '/brave-search/res/v1/web/search?q=test&count=10&offset=0'

# Second page (results 11-20)
maton api '/brave-search/res/v1/web/search?q=test&count=10&offset=1'
```

**Query parameters:**
- `q` (required): Search query (1-400 characters, max 50 words)
- `country`: 2-letter country code (default: "US")
- `search_lang`: Search language code (default: "en")
- `count`: Results per page, 1-20 (default: 20)
- `offset`: Page offset, 0-9 (default: 0)
- `safesearch`: Filter level - "off", "moderate", "strict"
- `freshness`: Time filter - "pd", "pw", "pm", "py"

**Note:** `offset` ranges from 0-9, giving access to up to 200 results (20 results × 10 pages).

Check `query.more_results_available` in the response to determine if more results exist.

### Location Headers

> **Privacy — these headers transmit the user's physical location.** `x-loc-lat`/`x-loc-long` are precise coordinates and, with city/state/postal code, can identify a home or workplace. They are sent to Brave Search on every request that includes them.
> - Only send location headers when the user's request is genuinely location-dependent (e.g. "restaurants near me") and the user has supplied or approved the location.
> - Never infer coordinates from the host machine, IP, system settings, or a previous unrelated request, and never populate them silently.
> - Prefer the coarsest value that satisfies the query — city or country rather than exact lat/long.
> - Do not log these values or carry them over into later requests.

For location-aware results, include location headers:

```bash
maton api '/brave-search/res/v1/web/search?q=restaurants+near+me&count=10' -H 'x-loc-lat: 37.7749' -H 'x-loc-long: -122.4194' -H 'x-loc-city: San Francisco' -H 'x-loc-state: CA' -H 'x-loc-country: US'
```

**Available Location Headers:**
- `x-loc-lat`: Latitude (-90 to 90)
- `x-loc-long`: Longitude (-180 to 180)
- `x-loc-timezone`: IANA timezone identifier
- `x-loc-city`: City name
- `x-loc-state`: State/province
- `x-loc-country`: 2-letter country code
- `x-loc-postal-code`: ZIP/postal code

### Notes

- Maximum 20 results per request
- Maximum 10 pages (offset 0-9)
- Privacy-focused search engine
- Results include web, news, videos, discussions, FAQ, infobox
- Authentication is handled by the gateway. Upstream, Brave Search uses an API key rather than OAuth, but that key belongs to the Maton connection and is injected server-side: do not build an `Authorization` header, do not ask the user for a Brave Search key, and never place one in a request, a script, or a trigger destination. Requests carry the Maton credential only, exactly like every other app in this gateway.
- Some endpoints require additional subscription plans

### Resources

- [Brave Search API Documentation](https://api-dashboard.search.brave.com/documentation)
- [Brave Search API Dashboard](https://api-dashboard.search.brave.com/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
