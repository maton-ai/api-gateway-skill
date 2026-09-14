# Google Ads

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `google-ads`
**Upstream base URL:** `googleads.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://googleads.googleapis.com/v24/customers:listAccessibleCustomers`
- Gateway: `https://api.maton.ai/google-ads/v24/customers:listAccessibleCustomers`

### Customers API

#### List Accessible Customers

```bash
maton google-ads account list
```

Or with `maton api`:

```bash
maton api '/google-ads/v24/customers:listAccessibleCustomers'
```

### Search API

#### Search

```bash
maton google-ads query -c 1234567890 --resource campaign --fields 'campaign.id, campaign.name, campaign.status' --order-by 'campaign.id'
```

Or with `maton api`:

```bash
maton api -X POST '/google-ads/v24/customers/{customerId}/googleAds:search' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "SELECT campaign.id, campaign.name, campaign.status FROM campaign ORDER BY campaign.id"
}
JSON
```

**Note:** `{customerId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** For a client account under a manager (MCC) account, see [Manager (MCC) Account Access](#manager-mcc-account-access).

#### Search Stream

```bash
maton google-ads query-stream -c 1234567890 --resource campaign --fields 'campaign.id, campaign.name'
```

Or with `maton api`:

```bash
maton api -X POST '/google-ads/v24/customers/{customerId}/googleAds:searchStream' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "SELECT campaign.id, campaign.name FROM campaign"
}
JSON
```

**Note:** `{customerId}` is a placeholder. Replace it with a real value before sending the request.

#### List Campaigns

```bash
maton google-ads campaign list -c 1234567890
```

#### List Keywords

```bash
maton google-ads keyword list -c 1234567890 --date-range LAST_7_DAYS -L 25 --campaign-id 99999
```

Or with `maton api`:

```bash
maton api -X POST '/google-ads/v24/customers/{customerId}/googleAds:search' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "SELECT ad_group_criterion.keyword.text, ad_group_criterion.keyword.match_type, ad_group_criterion.status, metrics.impressions, metrics.clicks, metrics.cost_micros FROM keyword_view WHERE segments.date DURING LAST_30_DAYS ORDER BY metrics.impressions DESC"
}
JSON
```

**Note:** `{customerId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Keyword queries request metrics, so they cannot be run against a manager (MCC) account directly. Run against the client customer ID under the manager, optionally with `--login-customer-id`.

### Mutate API

#### Create Campaign

```bash
maton api -X POST '/google-ads/v24/customers/{customerId}/campaigns:mutate' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "operations": [
    {
      "create": {
        "name": "New Campaign",
        "advertisingChannelType": "SEARCH",
        "status": "PAUSED",
        "manualCpc": {},
        "campaignBudget": "customers/{customerId}/campaignBudgets/{budgetId}"
      }
    }
  ]
}
JSON
```

**Note:** `{customerId}` and `{budgetId}` are placeholders. Replace each of them with real values before sending the request.

#### Update Campaign Status

```bash
maton api -X POST '/google-ads/v24/customers/{customerId}/campaigns:mutate' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "operations": [
    {
      "update": {
        "resourceName": "customers/{customerId}/campaigns/{campaignId}",
        "status": "ENABLED"
      },
      "updateMask": "status"
    }
  ]
}
JSON
```

**Note:** `{customerId}` and `{campaignId}` are placeholders. Replace each of them with real values before sending the request.

### Pagination

Google Ads uses token-based pagination. The CLI handles this automatically with `--paginate`:

```bash
maton google-ads campaign list -c 1234567890 --paginate
```

### Common GAQL Queries

#### List Campaigns

```sql
SELECT campaign.id, campaign.name, campaign.status, campaign.advertising_channel_type
FROM campaign
WHERE campaign.status != 'REMOVED'
ORDER BY campaign.name
```

#### Campaign Performance

```sql
SELECT campaign.id, campaign.name, metrics.impressions, metrics.clicks, metrics.cost_micros, metrics.conversions
FROM campaign
WHERE segments.date DURING LAST_30_DAYS
ORDER BY metrics.impressions DESC
```

#### List Ad Groups

```sql
SELECT ad_group.id, ad_group.name, ad_group.status, campaign.id, campaign.name
FROM ad_group
WHERE ad_group.status != 'REMOVED'
```

#### List Keywords with Performance

```sql
SELECT ad_group_criterion.keyword.text, ad_group_criterion.keyword.match_type, metrics.impressions, metrics.clicks, metrics.cost_micros
FROM keyword_view
WHERE segments.date DURING LAST_30_DAYS
  AND ad_group_criterion.status = 'ENABLED'
ORDER BY metrics.cost_micros DESC
LIMIT 50
```

#### Search Term Report

```sql
SELECT search_term_view.search_term, campaign.name, ad_group.name, metrics.impressions, metrics.clicks, metrics.conversions
FROM search_term_view
WHERE segments.date DURING LAST_30_DAYS
ORDER BY metrics.clicks DESC
```

#### Account-level Performance

```sql
SELECT customer.descriptive_name, segments.date, metrics.impressions, metrics.clicks, metrics.cost_micros, metrics.conversions
FROM customer
WHERE segments.date DURING LAST_7_DAYS
```

### Manager (MCC) Account Access

When accessing a customer account through a Google Ads manager (MCC) account, pass the manager's customer ID via `--login-customer-id` (CLI) or the `login-customer-id` header (direct API). The customer ID in the path is still the client account being queried.

```bash
# List campaigns in client account 1234567890 via manager 9876543210
maton google-ads campaign list -c 1234567890 --login-customer-id 9876543210
```

### Notes

- Use `listAccessibleCustomers` first to get available customer IDs
- Customer IDs are 10-digit numbers (remove dashes if formatted as XXX-XXX-XXXX)
- Monetary values are in micros (divide by 1,000,000)
- Use GAQL (Google Ads Query Language) for querying
- Date ranges: `LAST_7_DAYS`, `LAST_30_DAYS`, `THIS_MONTH`, etc.
- Status values: `ENABLED`, `PAUSED`, `REMOVED`
- API version updates frequently - check release notes for latest (currently v24)

### Resources

- [Google Ads API Overview](https://developers.google.com/google-ads/api/docs/start)
- [List Accessible Customers](https://developers.google.com/google-ads/api/reference/rpc/v24/CustomerService/ListAccessibleCustomers?transport=rest)
- [Search](https://developers.google.com/google-ads/api/reference/rpc/v24/GoogleAdsService/Search?transport=rest)
- [Search Stream](https://developers.google.com/google-ads/api/reference/rpc/v24/GoogleAdsService/SearchStream?transport=rest)
- [GAQL Reference](https://developers.google.com/google-ads/api/docs/query/overview)
- [Metrics Reference](https://developers.google.com/google-ads/api/fields/v24/metrics)
- [Maton CLI Manual](https://cli.maton.ai/manual)
