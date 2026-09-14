# Google Search Console

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `google-search-console`
**Upstream base URL:** `www.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://www.googleapis.com/webmasters/v3/sites`
- Gateway: `https://api.maton.ai/google-search-console/webmasters/v3/sites`

### Sites API

#### List Sites

```bash
maton api '/google-search-console/webmasters/v3/sites'

maton api '/google-search-console/webmasters/v3/sites/{siteUrl}'
```

**Note:** `{siteUrl}` is a placeholder. Replace it with a real value before sending the request.

Note: Site URL must be URL-encoded (e.g., `https%3A%2F%2Fexample.com%2F`)

#### Get Site

```bash
maton api '/google-search-console/webmasters/v3/sites/{siteUrl}'
```

**Note:** `{siteUrl}` is a placeholder. Replace it with a real value before sending the request.

Note: Site URL must be URL-encoded (e.g., `https%3A%2F%2Fexample.com%2F`)

### Search API

#### Search Analytics Query

```bash
maton api -X POST '/google-search-console/webmasters/v3/sites/{siteUrl}/searchAnalytics/query' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "startDate": "2024-01-01",
  "endDate": "2024-01-31",
  "dimensions": ["query"],
  "rowLimit": 100
}
EOF
```

**Note:** `{siteUrl}` is a placeholder. Replace it with a real value before sending the request.

### Sitemaps API

#### List Sitemaps

```bash
maton api '/google-search-console/webmasters/v3/sites/{siteUrl}/sitemaps'

```

**Note:** `{siteUrl}` is a placeholder. Replace it with a real value before sending the request.

#### Get Sitemap

```bash
maton api '/google-search-console/webmasters/v3/sites/{siteUrl}/sitemaps/{feedpath}'
```

**Note:** `{siteUrl}` and `{feedpath}` are placeholders. Replace each of them with real values before sending the request.

#### Submit Sitemap

```bash
maton api -X PUT '/google-search-console/webmasters/v3/sites/{siteUrl}/sitemaps/{feedpath}'
```

**Note:** `{siteUrl}` and `{feedpath}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Sitemap

```bash
maton api '/google-search-console/webmasters/v3/sites/{siteUrl}/sitemaps/{feedpath}' -X DELETE
```

**Note:** `{siteUrl}` and `{feedpath}` are placeholders. Replace each of them with real values before sending the request.

### Search Analytics Query Examples

#### Top Queries

```json
{
  "startDate": "2024-01-01",
  "endDate": "2024-01-31",
  "dimensions": ["query"],
  "rowLimit": 25,
  "startRow": 0
}
```

#### Top Pages

```json
{
  "startDate": "2024-01-01",
  "endDate": "2024-01-31",
  "dimensions": ["page"],
  "rowLimit": 25
}
```

#### Queries by Country

```json
{
  "startDate": "2024-01-01",
  "endDate": "2024-01-31",
  "dimensions": ["query", "country"],
  "rowLimit": 100
}
```

#### Device Breakdown

```json
{
  "startDate": "2024-01-01",
  "endDate": "2024-01-31",
  "dimensions": ["device"],
  "rowLimit": 10
}
```

#### Daily Performance

```json
{
  "startDate": "2024-01-01",
  "endDate": "2024-01-31",
  "dimensions": ["date"],
  "rowLimit": 31
}
```

#### Filtered Query

```json
{
  "startDate": "2024-01-01",
  "endDate": "2024-01-31",
  "dimensions": ["query"],
  "dimensionFilterGroups": [{
    "filters": [{
      "dimension": "query",
      "operator": "contains",
      "expression": "keyword"
    }]
  }],
  "rowLimit": 100
}
```

#### Search Type Filter

```json
{
  "startDate": "2024-01-01",
  "endDate": "2024-01-31",
  "dimensions": ["query"],
  "type": "image",
  "rowLimit": 25
}
```

### Dimensions

- `query` - Search query
- `page` - Page URL
- `country` - Country code (ISO 3166-1 alpha-3)
- `device` - DESKTOP, MOBILE, TABLET
- `date` - Date in YYYY-MM-DD format
- `searchAppearance` - Rich result types

### Metrics (returned automatically)

- `clicks` - Number of clicks
- `impressions` - Number of impressions
- `ctr` - Click-through rate
- `position` - Average position

### Filter Operators

- `equals`
- `contains`
- `notContains`
- `includingRegex`
- `excludingRegex`

### Search Types

- `web` - Web search (default)
- `image` - Image search
- `video` - Video search
- `news` - News search

### Notes

- Site URLs must be URL-encoded in the path (e.g., `sc-domain%3Aexample.com`)
- Date range is limited to 16 months of data
- Maximum 25,000 rows per request
- Use `startRow` for pagination
- Data has a 2-3 day delay

### Resources

- [Google Search Console API Reference](https://developers.google.com/webmaster-tools/v1/api_reference_index)
- [List Sites](https://developers.google.com/webmaster-tools/v1/sites/list)
- [Get Site](https://developers.google.com/webmaster-tools/v1/sites/get)
- [Search Analytics Query](https://developers.google.com/webmaster-tools/v1/searchanalytics/query)
- [List Sitemaps](https://developers.google.com/webmaster-tools/v1/sitemaps/list)
- [Get Sitemap](https://developers.google.com/webmaster-tools/v1/sitemaps/get)
- [Submit Sitemap](https://developers.google.com/webmaster-tools/v1/sitemaps/submit)
- [Delete Sitemap](https://developers.google.com/webmaster-tools/v1/sitemaps/delete)
- [Maton CLI Manual](https://cli.maton.ai/manual)
