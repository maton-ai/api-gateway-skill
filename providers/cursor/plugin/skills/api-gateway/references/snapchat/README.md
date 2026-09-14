# Snapchat

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `snapchat`
**Upstream base URL:** `adsapi.snapchat.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://adsapi.snapchat.com/v1/me`
- Gateway: `https://api.maton.ai/snapchat/v1/me`

### User Info API

#### Get Current User

```bash
maton api '/snapchat/v1/me'
```

**Response:**
```json
{
  "request_status": "SUCCESS",
  "request_id": "...",
  "me": {
    "id": "...",
    "email": "user@example.com",
    "display_name": "User Name"
  }
}
```

#### List Organizations

```bash
maton api '/snapchat/v1/me/organizations'
```

**Response:**
```json
{
  "request_status": "SUCCESS",
  "request_id": "...",
  "organizations": [
    {
      "sub_request_status": "SUCCESS",
      "organization": {
        "id": "63acee69-77ff-4378-8492-3f8d28e8f241",
        "name": "My Organization",
        "country": "US",
        "contact_name": "John Doe",
        "contact_email": "john@example.com"
      }
    }
  ]
}
```

### Organizations API

#### Get Organization

```bash
maton api '/snapchat/v1/organizations/{organizationId}'
```

**Note:** `{organizationId}` is a placeholder. Replace it with a real value before sending the request.

#### List Ad Accounts

```bash
maton api '/snapchat/v1/organizations/{organizationId}/adaccounts'
```

**Note:** `{organizationId}` is a placeholder. Replace it with a real value before sending the request.

#### List Funding Sources

```bash
maton api '/snapchat/v1/organizations/{organizationId}/fundingsources'
```

**Note:** `{organizationId}` is a placeholder. Replace it with a real value before sending the request.

#### List Organization Members

```bash
maton api '/snapchat/v1/organizations/{organizationId}/members'
```

**Note:** `{organizationId}` is a placeholder. Replace it with a real value before sending the request.

#### List Organization Roles

```bash
maton api '/snapchat/v1/organizations/{organizationId}/roles'
```

**Note:** `{organizationId}` is a placeholder. Replace it with a real value before sending the request.

#### List Product Catalogs

```bash
maton api '/snapchat/v1/organizations/{organizationId}/catalogs'
```

**Note:** `{organizationId}` is a placeholder. Replace it with a real value before sending the request.

### Ad Accounts API

#### Get Ad Account

```bash
maton api '/snapchat/v1/adaccounts/{adAccountId}'
```

**Note:** `{adAccountId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "request_status": "SUCCESS",
  "request_id": "...",
  "adaccounts": [
    {
      "sub_request_status": "SUCCESS",
      "adaccount": {
        "id": "6e916ba9-db2f-40cd-9553-a90e32cedea3",
        "name": "My Ad Account",
        "type": "PARTNER",
        "status": "ACTIVE",
        "organization_id": "...",
        "currency": "USD",
        "timezone": "America/Los_Angeles"
      }
    }
  ]
}
```

#### List Ad Account Roles

```bash
maton api '/snapchat/v1/adaccounts/{adAccountId}/roles'
```

**Note:** `{adAccountId}` is a placeholder. Replace it with a real value before sending the request.

#### List Pixels

```bash
maton api '/snapchat/v1/adaccounts/{adAccountId}/pixels'
```

**Note:** `{adAccountId}` is a placeholder. Replace it with a real value before sending the request.

#### List Segments

```bash
maton api '/snapchat/v1/adaccounts/{adAccountId}/segments'
```

**Note:** `{adAccountId}` is a placeholder. Replace it with a real value before sending the request.

### Campaigns API

#### List Campaigns

The Snapchat API uses cursor-based pagination with the `limit` parameter (50-1000) and returns a `paging` object with `next_link`.

```bash
maton api '/snapchat/v1/adaccounts/{adAccountId}/campaigns?limit=50'
```

**Note:** `{adAccountId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "request_status": "SUCCESS",
  "campaigns": [...],
  "paging": {
    "next_link": "https://adsapi.snapchat.com/v1/adaccounts/{id}/campaigns?cursor=..."
  }
}
```

To get the next page, use the `next_link` URL (replace host with gateway):

```bash
maton api '/snapchat/v1/adaccounts/{adAccountId}/campaigns?cursor=...'
```

**Note:** `{adAccountId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `limit` - Number of results (50-1000)

#### Get Campaign

```bash
maton api '/snapchat/v1/campaigns/{campaignId}'
```

**Note:** `{campaignId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Campaign

```bash
maton api -X POST '/snapchat/v1/adaccounts/{adAccountId}/campaigns' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "campaigns": [{
    "name": "Campaign Name",
    "status": "PAUSED",
    "ad_account_id": "{adAccountId}",
    "start_time": "2026-02-15T00:00:00.000-08:00"
  }]
}
JSON
```

**Note:** `{adAccountId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Campaign

```bash
maton api -X PUT '/snapchat/v1/adaccounts/{adAccountId}/campaigns' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "campaigns": [{
    "id": "{campaignId}",
    "name": "Updated Campaign Name",
    "status": "ACTIVE"
  }]
}
JSON
```

**Note:** `{adAccountId}` and `{campaignId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Campaign

```bash
maton api '/snapchat/v1/campaigns/{campaignId}' -X DELETE
```

**Note:** `{campaignId}` is a placeholder. Replace it with a real value before sending the request.

### Ad Squads API

#### List Ad Squads

```bash
maton api '/snapchat/v1/adaccounts/{adAccountId}/adsquads'

maton api '/snapchat/v1/campaigns/{campaignId}/adsquads'
```

**Note:** `{adAccountId}` and `{campaignId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Ad Squad

```bash
maton api '/snapchat/v1/adsquads/{adSquadId}'
```

**Note:** `{adSquadId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Ad Squad

```bash
maton api -X POST '/snapchat/v1/campaigns/{campaignId}/adsquads' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "adsquads": [{
    "name": "Ad Squad Name",
    "status": "PAUSED",
    "campaign_id": "{campaignId}",
    "type": "SNAP_ADS",
    "placement": "SNAP_ADS",
    "optimization_goal": "IMPRESSIONS",
    "bid_micro": 1000000,
    "daily_budget_micro": 50000000,
    "start_time": "2026-02-15T00:00:00.000-08:00",
    "targeting": {
      "geos": [{"country_code": "us"}]
    }
  }]
}
JSON
```

**Note:** `{campaignId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Ad Squad

```bash
maton api -X PUT '/snapchat/v1/campaigns/{campaignId}/adsquads' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "adsquads": [{
    "id": "{adSquadId}",
    "name": "Updated Ad Squad Name"
  }]
}
JSON
```

**Note:** `{campaignId}` and `{adSquadId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Ad Squad

```bash
maton api '/snapchat/v1/adsquads/{adSquadId}' -X DELETE
```

**Note:** `{adSquadId}` is a placeholder. Replace it with a real value before sending the request.

### Ads API

#### List Ads

```bash
maton api '/snapchat/v1/adaccounts/{adAccountId}/ads'

maton api '/snapchat/v1/adsquads/{adSquadId}/ads'
```

**Note:** `{adAccountId}` and `{adSquadId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Ad

```bash
maton api '/snapchat/v1/ads/{adId}'
```

**Note:** `{adId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Ad

```bash
maton api -X POST '/snapchat/v1/adsquads/{adSquadId}/ads' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "ads": [{
    "name": "Ad Name",
    "status": "PAUSED",
    "ad_squad_id": "{adSquadId}",
    "creative_id": "{creativeId}",
    "type": "SNAP_AD"
  }]
}
JSON
```

**Note:** `{adSquadId}` and `{creativeId}` are placeholders. Replace each of them with real values before sending the request.

#### Update Ad

```bash
maton api -X PUT '/snapchat/v1/adsquads/{adSquadId}/ads' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "ads": [{
    "id": "{adId}",
    "name": "Updated Ad Name"
  }]
}
JSON
```

**Note:** `{adSquadId}` and `{adId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Ad

```bash
maton api '/snapchat/v1/ads/{adId}' -X DELETE
```

**Note:** `{adId}` is a placeholder. Replace it with a real value before sending the request.

### Creatives API

#### List Creatives

Some endpoints support sorting with the `sort` parameter:

```bash
maton api '/snapchat/v1/adaccounts/{adAccountId}/creatives?sort=updated_at-desc'

maton api '/snapchat/v1/adaccounts/{adAccountId}/media?sort=created_at-desc'
```

**Note:** `{adAccountId}` is a placeholder. Replace it with a real value before sending the request.

Supported values: `updated_at-desc`, `created_at-desc`

#### Get Creative

```bash
maton api '/snapchat/v1/creatives/{creativeId}'
```

**Note:** `{creativeId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Creative

```bash
maton api -X POST '/snapchat/v1/adaccounts/{adAccountId}/creatives' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "creatives": [{
    "name": "Creative Name",
    "ad_account_id": "{adAccountId}",
    "type": "SNAP_AD",
    "top_snap_media_id": "{mediaId}",
    "headline": "Headline Text",
    "brand_name": "Brand Name",
    "call_to_action": "VIEW_MORE"
  }]
}
JSON
```

**Note:** `{adAccountId}` and `{mediaId}` are placeholders. Replace each of them with real values before sending the request.

#### Update Creative

```bash
maton api -X PUT '/snapchat/v1/adaccounts/{adAccountId}/creatives' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "creatives": [{
    "id": "{creativeId}",
    "name": "Updated Creative Name"
  }]
}
JSON
```

**Note:** `{adAccountId}` and `{creativeId}` are placeholders. Replace each of them with real values before sending the request.

### Media API

#### List Media

```bash
maton api '/snapchat/v1/adaccounts/{adAccountId}/media'
```

**Note:** `{adAccountId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Media

```bash
maton api '/snapchat/v1/media/{mediaId}'
```

**Note:** `{mediaId}` is a placeholder. Replace it with a real value before sending the request.

### Pixels API

#### Get Pixel

```bash
maton api '/snapchat/v1/pixels/{pixelId}'
```

**Note:** `{pixelId}` is a placeholder. Replace it with a real value before sending the request.

### Audience Segments API

#### Get Segment

```bash
maton api '/snapchat/v1/segments/{segmentId}'
```

**Note:** `{segmentId}` is a placeholder. Replace it with a real value before sending the request.

### Stats API

#### Get Ad Account Stats

```bash
maton api '/snapchat/v1/adaccounts/{adAccountId}/stats?granularity=DAY&start_time=2026-02-01&end_time=2026-02-14'
```

**Note:** `{adAccountId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `granularity` - `HOUR`, `DAY`, `LIFETIME`
- `start_time` - Start date (YYYY-MM-DD)
- `end_time` - End date (YYYY-MM-DD)

#### Get Campaign Stats

```bash
maton api '/snapchat/v1/campaigns/{campaignId}/stats?granularity=DAY&start_time=2026-02-01&end_time=2026-02-14'
```

**Note:** `{campaignId}` is a placeholder. Replace it with a real value before sending the request.

### Targeting API

#### Get Countries

```bash
maton api '/snapchat/v1/targeting/geo/country'
```

#### Get Regions

```bash
maton api '/snapchat/v1/targeting/geo/{countryCode}/region'
```

**Note:** `{countryCode}` is a placeholder. Replace it with a real value before sending the request.

Example: `GET /v1/targeting/geo/us/region`

#### Get OS Types

```bash
maton api '/snapchat/v1/targeting/device/os_type'
```

#### Get Location Categories

```bash
maton api '/snapchat/v1/targeting/location/categories_loi'
```

### Ads Gallery API

#### List Sponsored Content

```bash
maton api '/snapchat/v1/ads_library/sponsored_content'
```

**Response:**
```json
{
  "request_status": "SUCCESS",
  "request_id": "...",
  "sponsored_content": [
    {
      "sub_request_status": "SUCCESS",
      "sponsored_content": {
        "id": "...",
        "name": "Content Name",
        "status": "ACTIVE"
      }
    }
  ]
}
```

#### Search Sponsored Content

```bash
maton api -X POST '/snapchat/v1/ads_library/sponsored_content/search' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "limit": 50
}
JSON
```

#### Search Ads

Search for ads in the public Ads Library by advertiser name and country.

```bash
maton api -X POST '/snapchat/v1/ads_library/ads/search' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "paying_advertiser_name": "Nike",
  "countries": ["fr", "de"],
  "limit": 50
}
JSON
```

**Request body:**
- `paying_advertiser_name` (required) - Advertiser name to search for
- `countries` (required) - Array of lowercase 2-letter ISO country codes (e.g., `["fr", "de", "gb"]`)
- `start_date` - ISO 8601 timestamp for date range start
- `end_date` - ISO 8601 timestamp for date range end
- `status` - Filter by status (e.g., `"ACTIVE"`, `"PAUSED"`)
- `limit` - Number of results to return

**Note:** Not all countries are available in the Ads Library. EU countries (fr, de, gb, etc.) are supported. US ads may not be available due to regional restrictions.

**Response:**
```json
{
  "request_status": "SUCCESS",
  "request_id": "...",
  "paging": {
    "next_link": "..."
  },
  "ad_previews": [
    {
      "sub_request_status": "SUCCESS",
      "ad_preview": {
        "id": "...",
        "name": "Ad Name",
        "ad_account_name": "Advertiser Name",
        "status": "ACTIVE",
        "creative_type": "WEB_VIEW",
        "headline": "Ad Headline",
        "call_to_action": "SHOP NOW"
      }
    }
  ]
}
```

### Notes

- Monetary values use micro-currency (1 USD = 1,000,000 micro)
- Bulk operations accept arrays for batch create/update
- Pagination uses `limit` (50-1000) and cursor via `next_link`
- Sorting: `sort=updated_at-desc` or `sort=created_at-desc`
- Ads Gallery: Use lowercase 2-letter ISO country codes (e.g., `fr`, `de`). US may not be available.

### Resources

- [Snapchat Ads API Introduction](https://developers.snap.com/api/marketing-api/Ads-API/introduction)
- [API Patterns](https://developers.snap.com/api/marketing-api/Ads-API/api-patterns)
- [Ads Gallery API](https://developers.snap.com/api/marketing-api/Ads-Gallery-Api/using-the-api)
- [Maton CLI Manual](https://cli.maton.ai/manual)
