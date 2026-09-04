# Snapchat Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `snapchat`
**Base URL proxied:** `adsapi.snapchat.com`

## API Path Pattern

```
/snapchat/v1/{resource}
```

## Common Endpoints

### Current User

#### Get Current User
```bash
maton api '/snapchat/v1/me'
```

#### List Organizations
```bash
maton api '/snapchat/v1/me/organizations'
```

### Organizations

#### Get Organization
```bash
maton api '/snapchat/v1/organizations/{organizationId}'
```

#### List Ad Accounts
```bash
maton api '/snapchat/v1/organizations/{organizationId}/adaccounts'
```

#### List Funding Sources
```bash
maton api '/snapchat/v1/organizations/{organizationId}/fundingsources'
```

### Ad Accounts

#### Get Ad Account
```bash
maton api '/snapchat/v1/adaccounts/{adAccountId}'
```

### Campaigns

#### List Campaigns
```bash
maton api '/snapchat/v1/adaccounts/{adAccountId}/campaigns'
```

#### Create Campaign
```bash
maton api -X POST '/snapchat/v1/adaccounts/{adAccountId}/campaigns' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "campaigns": [{
    "name": "Campaign Name",
    "status": "PAUSED",
    "ad_account_id": "{adAccountId}",
    "start_time": "2026-02-15T00:00:00.000-08:00"
  }]
}
EOF
```

### Ad Squads

#### List Ad Squads
```bash
maton api '/snapchat/v1/adaccounts/{adAccountId}/adsquads'
```

### Ads

#### List Ads
```bash
maton api '/snapchat/v1/adaccounts/{adAccountId}/ads'
```

### Creatives

#### List Creatives
```bash
maton api '/snapchat/v1/adaccounts/{adAccountId}/creatives'
```

### Media

#### List Media
```bash
maton api '/snapchat/v1/adaccounts/{adAccountId}/media'
```

### Stats

#### Get Ad Account Stats
```bash
maton api '/snapchat/v1/adaccounts/{adAccountId}/stats?granularity=DAY&start_time=2026-02-01&end_time=2026-02-14'
```

### Targeting

#### Get Countries
```bash
maton api '/snapchat/v1/targeting/geo/country'
```

#### Get Regions
```bash
maton api '/snapchat/v1/targeting/geo/{countryCode}/region'
```

### Ads Gallery (Public Ads Library)

#### List Sponsored Content
```bash
maton api '/snapchat/v1/ads_library/sponsored_content'
```

#### Search Ads
```bash
maton api -X POST '/snapchat/v1/ads_library/ads/search' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "paying_advertiser_name": "Nike",
  "countries": ["fr", "de"],
  "limit": 50
}
EOF
```

## Notes

- Monetary values use micro-currency (1 USD = 1,000,000 micro)
- Bulk operations accept arrays for batch create/update
- Pagination uses `limit` (50-1000) and cursor via `next_link`
- Sorting: `sort=updated_at-desc` or `sort=created_at-desc`
- Ads Gallery: Use lowercase 2-letter ISO country codes (e.g., `fr`, `de`). US may not be available.

## Resources

- [Snapchat Ads API Introduction](https://developers.snap.com/api/marketing-api/Ads-API/introduction)
- [API Patterns](https://developers.snap.com/api/marketing-api/Ads-API/api-patterns)
- [Ads Gallery API](https://developers.snap.com/api/marketing-api/Ads-Gallery-Api/using-the-api)
