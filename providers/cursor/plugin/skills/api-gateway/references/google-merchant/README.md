# Google Merchant

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `google-merchant`
**Upstream base URL:** `merchantapi.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://merchantapi.googleapis.com/accounts/v1beta/accounts`
- Gateway: `https://api.maton.ai/google-merchant/accounts/v1beta/accounts`

**Important:** The v1 API requires one-time developer registration per Merchant Center account. Complete the registration steps below before using any endpoints.

### Developer Registration

Before using v1 endpoints, you must complete a one-time registration:

**Step 1: Get Your Account ID**

Option A: Try fetching via API first

Try listing accounts using the v1beta endpoint. If this works, you can get your account ID automatically:

```bash
maton api '/google-merchant/accounts/v1beta/accounts'
```

Response (if successful):
```json
{
  "accounts": [
    {"accountId": "123456789", "accountName": "My Store"}
  ]
}
```

Option B: From Merchant Center UI (if Option A fails)

If the v1beta endpoint is unavailable or returns an error:

1. Log in to [Google Merchant Center](https://merchants.google.com/)
2. Your account ID is in the URL: `https://merchants.google.com/mc/overview?a=YOUR_ACCOUNT_ID`

For example, if your URL is `https://merchants.google.com/mc/overview?a=123456789`, your account ID is `123456789`.

The account ID is a numeric identifier used in most paths. To find it:

1. Log in to [Google Merchant Center](https://merchants.google.com/)
2. Read it from the URL: `https://merchants.google.com/mc/overview?a=ACCOUNT_ID`

Or list the accounts the connection can see:

The Merchant API uses a modular sub-API structure:
- `{sub-api}` — the service module: `products`, `accounts`, `datasources`, `reports`, `promotions`, `inventories`, `notifications`, `conversions`
- `{version}` — currently `v1`
- `{accountId}` — your Merchant Center account ID

Important: The v1 API requires one-time developer registration. See [Developer Registration](#developer-registration) section.

**Step 2: Register for API Access**

```bash
maton api -X POST '/google-merchant/accounts/v1/accounts/{account_id}/developerRegistration:registerGcp' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "developerEmail": "your-email@example.com"
}
EOF
```

Note: `{account_id}` is a placeholder. Replace `{accountId}` with your account ID from Step 1, and use the email associated with your Google account.

Response:
```json
{
  "name": "accounts/123456789/developerRegistration",
  "gcpIds": ["..."]
}
```

```json
{
  "name": "accounts/123456789/developerRegistration",
  "gcpIds": ["216141799266"]
}
```

**Step 3: Verify Registration**

After registration, test that v1 endpoints work:

```bash
maton api '/google-merchant/accounts/v1/accounts/{accountId}'
```

Note: `{accountId}` is a placeholder. Replace it with a real value before sending the request.

Note: Registration only needs to be done once per Merchant Center account. After successful registration, all v1 endpoints will work for that account.

### Sub-API Structure

The Merchant API is organized into sub-APIs, each with its own path prefix:

| Sub-API | Purpose | Version |
|---------|---------|---------|
| `products` | Product catalog management | v1 |
| `accounts` | Account settings and users | v1 (some endpoints v1beta) |
| `datasources` | Data source configuration | v1 |
| `reports` | Analytics and reporting | v1 |
| `promotions` | Promotional offers (requires enrollment) | v1 |
| `inventories` | Local and regional inventory | v1 |
| `notifications` | Webhook subscriptions | v1 |
| `conversions` | Conversion tracking | v1 |

### Accounts API

#### List Accounts

```bash
maton api '/google-merchant/accounts/v1/accounts'
```

Returns all Merchant Center accounts accessible with your OAuth credentials. Use this to find your account ID.

#### Get Account

```bash
maton api '/google-merchant/accounts/v1/accounts/{accountId}'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### List Sub-accounts

```bash
maton api '/google-merchant/accounts/v1/accounts/{accountId}:listSubaccounts'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** This endpoint only works for multi-client accounts (MCAs). Standard merchant accounts will receive a 403 error.

#### Get Business Info

```bash
maton api '/google-merchant/accounts/v1/accounts/{accountId}/businessInfo'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Business Info

```bash
maton api -X PATCH '/google-merchant/accounts/v1/accounts/{accountId}/businessInfo?updateMask=customerService' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "customerService": {
    "email": "support@example.com"
  }
}
JSON
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Homepage

```bash
maton api '/google-merchant/accounts/v1/accounts/{accountId}/homepage'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Shipping Settings

```bash
maton api '/google-merchant/accounts/v1/accounts/{accountId}/shippingSettings'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### Insert Shipping Settings

```bash
maton api -X POST '/google-merchant/accounts/v1/accounts/{accountId}/shippingSettings:insert' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "services": [
    {
      "serviceName": "Standard Shipping",
      "deliveryCountries": ["US"],
      "currencyCode": "USD",
      "deliveryTime": {
        "minTransitDays": 3,
        "maxTransitDays": 7,
        "minHandlingDays": 0,
        "maxHandlingDays": 1
      },
      "rateGroups": [
        {
          "singleValue": {
            "flatRate": {
              "amountMicros": "0",
              "currencyCode": "USD"
            }
          }
        }
      ],
      "active": true
    }
  ]
}
JSON
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### List Users

```bash
maton api '/google-merchant/accounts/v1/accounts/{accountId}/users'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### Get User

```bash
maton api '/google-merchant/accounts/v1/accounts/{accountId}/users/{email}'
```

**Note:** `{accountId}` and `{email}` are placeholders. Replace each of them with real values before sending the request.

#### List Programs

```bash
maton api '/google-merchant/accounts/v1/accounts/{accountId}/programs'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### List Regions

```bash
maton api '/google-merchant/accounts/v1/accounts/{accountId}/regions'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### List Account Issues

```bash
maton api '/google-merchant/accounts/v1/accounts/{accountId}/issues'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### List Online Return Policies

```bash
maton api '/google-merchant/accounts/v1/accounts/{accountId}/onlineReturnPolicies'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

### Products API

#### List Products

The API uses token-based pagination:

```bash
maton api '/google-merchant/products/v1/accounts/{accountId}/products?pageSize=50'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

Response includes `nextPageToken` when more results exist:

```json
{
  "products": [...],
  "nextPageToken": "CAE..."
}
```

Use the token for the next page:

```bash
maton api '/google-merchant/products/v1/accounts/{accountId}/products?pageSize=50&pageToken=CAE...'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `pageSize` (integer): Maximum results per page
- `pageToken` (string): Pagination token

#### Get Product

```bash
maton api '/google-merchant/products/v1/accounts/{accountId}/products/{productId}'
```

**Note:** `{accountId}` and `{productId}` are placeholders. Replace each of them with real values before sending the request.

Product ID format: `contentLanguage~feedLabel~offerId` (e.g., `en~US~sku123`)

#### Insert Product Input

```bash
maton api -X POST '/google-merchant/products/v1/accounts/{accountId}/productInputs:insert?dataSource=accounts/{accountId}/dataSources/{dataSourceId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "offerId": "sku123",
  "contentLanguage": "en",
  "feedLabel": "US",
  "productAttributes": {
    "title": "Product Title",
    "description": "Product description",
    "link": "https://example.com/product",
    "imageLink": "https://example.com/image.jpg",
    "availability": "in_stock",
    "price": {
      "amountMicros": "19990000",
      "currencyCode": "USD"
    },
    "condition": "new"
  }
}
JSON
```

**Note:** `{accountId}` and `{dataSourceId}` are placeholders. Replace each of them with real values before sending the request.

**Note:** Products can only be inserted into data sources with `input: "API"` type. Create an API data source first if needed.

#### Delete Product Input

```bash
maton api '/google-merchant/products/v1/accounts/{accountId}/productInputs/{productId}?dataSource=accounts/{accountId}/dataSources/{dataSourceId}' -X DELETE
```

**Note:** `{accountId}`, `{productId}` and `{dataSourceId}` are placeholders. Replace each of them with real values before sending the request.

### Inventories API

#### List Local Inventories

```bash
maton api '/google-merchant/inventories/v1/accounts/{accountId}/products/{productId}/localInventories'
```

**Note:** `{accountId}` and `{productId}` are placeholders. Replace each of them with real values before sending the request.

Note: Local inventories only work for products with LOCAL channel.

**Note:** Local inventories are only available for products with `LOCAL` channel. Use a product ID like `local~en~US~sku123`.

#### Insert Local Inventory

```bash
maton api -X POST '/google-merchant/inventories/v1/accounts/{accountId}/products/{productId}/localInventories:insert' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "storeCode": "store123"
}
JSON
```

**Note:** `{accountId}` and `{productId}` are placeholders. Replace each of them with real values before sending the request.

**Note:** The `storeCode` must be a valid store code configured in your Merchant Center account. Additional inventory attributes may be available - refer to the [Google Merchant API Reference](https://developers.google.com/merchant/api/reference/rest) for the complete field list.

#### List Regional Inventories

```bash
maton api '/google-merchant/inventories/v1/accounts/{accountId}/products/{productId}/regionalInventories'
```

**Note:** `{accountId}` and `{productId}` are placeholders. Replace each of them with real values before sending the request.

### Data Sources API

#### List Data Sources

```bash
maton api '/google-merchant/datasources/v1/accounts/{accountId}/dataSources'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Data Source

```bash
maton api '/google-merchant/datasources/v1/accounts/{accountId}/dataSources/{dataSourceId}'
```

**Note:** `{accountId}` and `{dataSourceId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Data Source

```bash
maton api -X POST '/google-merchant/datasources/v1/accounts/{accountId}/dataSources' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "displayName": "API Data Source",
  "primaryProductDataSource": {
    "feedLabel": "US",
    "contentLanguage": "en"
  }
}
JSON
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "name": "accounts/123456/dataSources/789",
  "dataSourceId": "789",
  "displayName": "API Data Source",
  "primaryProductDataSource": {
    "feedLabel": "US",
    "contentLanguage": "en"
  },
  "input": "API"
}
```

#### Update Data Source

```bash
maton api -X PATCH '/google-merchant/datasources/v1/accounts/{accountId}/dataSources/{dataSourceId}?updateMask=displayName' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "displayName": "Updated Name"
}
JSON
```

**Note:** `{accountId}` and `{dataSourceId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Data Source

```bash
maton api '/google-merchant/datasources/v1/accounts/{accountId}/dataSources/{dataSourceId}' -X DELETE
```

**Note:** `{accountId}` and `{dataSourceId}` are placeholders. Replace each of them with real values before sending the request.

#### Fetch Data Source (trigger immediate refresh)

```bash
maton api -X POST '/google-merchant/datasources/v1/accounts/{accountId}/dataSources/{dataSourceId}:fetch'
```

**Note:** `{accountId}` and `{dataSourceId}` are placeholders. Replace each of them with real values before sending the request.

**Note:** Fetch only works for data sources with `FILE` input type. API and UI data sources cannot be fetched.

### Reports API

#### Search Reports

```bash
maton api -X POST '/google-merchant/reports/v1/accounts/{accountId}/reports:search' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "SELECT offer_id, title, clicks, impressions FROM product_performance_view WHERE date BETWEEN '2026-01-01' AND '2026-01-31'"
}
JSON
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

Note: The `product_view` table requires the `id` field in SELECT clause.

**Example: Query product_view (requires `id` field):**

```json
{
  "query": "SELECT id, offer_id, title, item_issues FROM product_view LIMIT 10"
}
```

**Note:** The `product_view` table requires the `id` field in the SELECT clause.

Available report tables:
- `product_performance_view` - Clicks, impressions, CTR by product
- `product_view` - Current inventory with attributes and issues (requires `id` in SELECT)
- `price_competitiveness_product_view` - Pricing vs competitors (requires Market Insights)
- `price_insights_product_view` - Suggested pricing
- `best_sellers_product_cluster_view` - Best sellers by category (requires Market Insights)
- `competitive_visibility_competitor_view` - Competitor visibility

### Promotions API

#### List Promotions

```bash
maton api '/google-merchant/promotions/v1/accounts/{accountId}/promotions'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

Note: Requires Promotions program enrollment.

#### Get Promotion

```bash
maton api '/google-merchant/promotions/v1/accounts/{accountId}/promotions/{promotionId}'
```

**Note:** `{accountId}` and `{promotionId}` are placeholders. Replace each of them with real values before sending the request.

#### Insert Promotion

```bash
maton api -X POST '/google-merchant/promotions/v1/accounts/{accountId}/promotions:insert' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "promotionId": "promo123",
  "contentLanguage": "en",
  "targetCountry": "US",
  "redemptionChannel": ["ONLINE"],
  "attributes": {
    "longTitle": "20% off all products",
    "promotionEffectiveDates": "2026-02-01T00:00:00Z/2026-02-28T23:59:59Z"
  }
}
JSON
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

### Notifications API

#### List Notification Subscriptions

```bash
maton api '/google-merchant/notifications/v1/accounts/{accountId}/notificationsubscriptions'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Notification Subscription

```bash
maton api -X POST '/google-merchant/notifications/v1/accounts/{accountId}/notificationsubscriptions' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "registeredEvent": "PRODUCT_STATUS_CHANGE",
  "callBackUri": "https://example.com/webhook",
  "allManagedAccounts": true
}
JSON
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** You must specify either `allManagedAccounts: true` OR `targetAccount: "accounts/{accountId}"` to indicate which accounts the subscription applies to.

**Alternative with targetAccount:**
```json
{
  "registeredEvent": "PRODUCT_STATUS_CHANGE",
  "callBackUri": "https://example.com/webhook",
  "targetAccount": "accounts/123456789"
}
```

#### Delete Notification Subscription

```bash
maton api '/google-merchant/notifications/v1/accounts/{accountId}/notificationsubscriptions/{subscriptionId}' -X DELETE
```

**Note:** `{accountId}` and `{subscriptionId}` are placeholders. Replace each of them with real values before sending the request.

### Conversion Sources API

#### List Conversion Sources

```bash
maton api '/google-merchant/conversions/v1/accounts/{accountId}/conversionSources'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Conversion Source

```bash
maton api -X POST '/google-merchant/conversions/v1/accounts/{accountId}/conversionSources' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "merchantCenterDestination": {
    "displayName": "My Conversion Source",
    "destination": "SHOPPING_ADS",
    "currencyCode": "USD",
    "attributionSettings": {
      "attributionLookbackWindowDays": 30,
      "attributionModel": "CROSS_CHANNEL_LAST_CLICK"
    }
  }
}
JSON
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Conversion Source

```bash
maton api '/google-merchant/conversions/v1/accounts/{accountId}/conversionSources/{conversionSourceId}' -X DELETE
```

**Note:** `{accountId}` and `{conversionSourceId}` are placeholders. Replace each of them with real values before sending the request.

### Notes

- **Developer registration required** - Complete registration before using v1 endpoints
- Account ID is your Merchant Center numeric ID (visible in MC URL)
- Product IDs use format `contentLanguage~feedLabel~offerId`
- Monetary values use micros (divide by 1,000,000)
- Products can only be inserted in data sources with `input: "API"` type
- Uses token-based pagination with `pageSize` and `pageToken`
- Promotions require account enrollment in Promotions program
- Local inventories only work for LOCAL channel products

### Resources

- [Merchant API Overview](https://developers.google.com/merchant/api/overview)
- [Merchant API Reference](https://developers.google.com/merchant/api/reference/rest)
- [Products Guide](https://developers.google.com/merchant/api/guides/products/overview)
- [Reports Guide](https://developers.google.com/merchant/api/guides/reports/overview)
- [Maton CLI Manual](https://cli.maton.ai/manual)
