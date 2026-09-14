# Gumroad

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `gumroad`
**Upstream base URL:** `api.gumroad.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.gumroad.com/v2/user`
- Gateway: `https://api.maton.ai/gumroad/v2/user`

### User API

#### Get Current User

```bash
maton api '/gumroad/v2/user'
```

**Response:**
```json
{
  "success": true,
  "user": {
    "name": "Chris",
    "currency_type": "usd",
    "bio": null,
    "twitter_handle": null,
    "id": "1690942847664",
    "user_id": "QmTtTnViFSoocHAexgLuJw==",
    "url": "https://chriswave1246.gumroad.com",
    "profile_url": "https://public-files.gumroad.com/...",
    "email": "chris@example.com",
    "display_name": "Chris"
  }
}
```

### Product API

#### List Products

```bash
maton api '/gumroad/v2/products'
```

**Response:**
```json
{
  "success": true,
  "products": [
    {
      "id": "ABC123",
      "name": "My Product",
      "price": 500,
      "currency": "usd",
      "short_url": "https://gumroad.com/l/abc",
      "sales_count": 10,
      "sales_usd_cents": 5000
    }
  ]
}
```

#### Get Product

```bash
maton api '/gumroad/v2/products/{product_id}'
```

**Note:** `{product_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Product

```bash
maton api -X PUT '/gumroad/v2/products/{product_id}' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
name=Updated%20Name&price=1000
BODY
```

**Note:** `{product_id}` is a placeholder. Replace it with a real value before sending the request.

#### Enable/Disable Product

```bash
maton api -X PUT '/gumroad/v2/products/{product_id}/disable' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
disabled=true
BODY
```

**Note:** `{product_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Product

```bash
maton api '/gumroad/v2/products/{product_id}' -X DELETE
```

**Note:** `{product_id}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Creating new products via API is not supported. Products must be created through the Gumroad website.

### Offer Code API

#### List Offer Codes

```bash
maton api '/gumroad/v2/products/{product_id}/offer_codes'
```

**Note:** `{product_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Offer Code

```bash
maton api '/gumroad/v2/products/{product_id}/offer_codes/{offer_code_id}'
```

**Note:** `{product_id}` and `{offer_code_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Offer Code

```bash
maton api -X POST '/gumroad/v2/products/{product_id}/offer_codes' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
name=SUMMER20&amount_off=20
BODY
```

**Note:** `{product_id}` is a placeholder. Replace it with a real value before sending the request.

Parameters:
- `name` - The code customers enter (required)
- `amount_off` - Cents or percentage off (required)
- `offer_type` - "cents" or "percent" (default: "cents")
- `max_purchase_count` - Maximum uses (optional)

#### Update Offer Code

```bash
maton api -X PUT '/gumroad/v2/products/{product_id}/offer_codes/{offer_code_id}' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
max_purchase_count=100
BODY
```

**Note:** `{product_id}` and `{offer_code_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Offer Code

```bash
maton api '/gumroad/v2/products/{product_id}/offer_codes/{offer_code_id}' -X DELETE
```

**Note:** `{product_id}` and `{offer_code_id}` are placeholders. Replace each of them with real values before sending the request.

### Sales API

#### List Sales

```bash
maton api '/gumroad/v2/sales'
```

**Query parameters:**
- `after` - Only sales after this date (YYYY-MM-DD)
- `before` - Only sales before this date (YYYY-MM-DD)
- `page` - Page number for pagination

**Example with filters:**
```bash
maton api '/gumroad/v2/sales?after=2026-01-01&before=2026-12-31'
```

**Response:**
```json
{
  "success": true,
  "sales": [
    {
      "id": "sale_abc123",
      "email": "customer@example.com",
      "seller_id": "seller123",
      "product_id": "prod123",
      "product_name": "My Product",
      "price": 500,
      "currency_symbol": "$",
      "created_at": "2026-01-15T10:30:00Z"
    }
  ]
}
```

#### Get Sale

```bash
maton api '/gumroad/v2/sales/{sale_id}'
```

**Note:** `{sale_id}` is a placeholder. Replace it with a real value before sending the request.

### Subscriber API

#### List Subscribers

```bash
maton api '/gumroad/v2/products/{product_id}/subscribers'
```

**Note:** `{product_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Subscriber

```bash
maton api '/gumroad/v2/subscribers/{subscriber_id}'
```

**Note:** `{subscriber_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "success": true,
  "subscriber": {
    "id": "sub123",
    "product_id": "prod123",
    "product_name": "Monthly Subscription",
    "user_id": "user123",
    "user_email": "subscriber@example.com",
    "status": "alive",
    "created_at": "2026-01-01T00:00:00Z"
  }
}
```

### License API

#### Verify License

```bash
maton api -X POST '/gumroad/v2/licenses/verify' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
product_id={product_id}&license_key={license_key}
BODY
```

**Note:** `{product_id}` and `{license_key}` are placeholders. Replace each of them with real values before sending the request.

Parameters:
- `product_id` - The product ID (required)
- `license_key` - The license key to verify (required)
- `increment_uses_count` - Increment the use count (default: true)

**Response (success):**
```json
{
  "success": true,
  "uses": 1,
  "purchase": {
    "seller_id": "seller123",
    "product_id": "prod123",
    "product_name": "My Product",
    "permalink": "abc",
    "email": "customer@example.com",
    "license_key": "ABC-123-DEF",
    "quantity": 1,
    "created_at": "2026-01-15T00:00:00Z"
  }
}
```

**Response (failure):**
```json
{
  "success": false,
  "message": "That license does not exist for the provided product."
}
```

#### Enable License

```bash
maton api -X PUT '/gumroad/v2/licenses/enable' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
product_id={product_id}&license_key={license_key}
BODY
```

**Note:** `{product_id}` and `{license_key}` are placeholders. Replace each of them with real values before sending the request.

#### Disable License

```bash
maton api -X PUT '/gumroad/v2/licenses/disable' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
product_id={product_id}&license_key={license_key}
BODY
```

**Note:** `{product_id}` and `{license_key}` are placeholders. Replace each of them with real values before sending the request.

#### Decrement License Uses

```bash
maton api -X PUT '/gumroad/v2/licenses/decrement_uses_count' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
product_id={product_id}&license_key={license_key}
BODY
```

**Note:** `{product_id}` and `{license_key}` are placeholders. Replace each of them with real values before sending the request.

### Resource Subscriptions API

#### List Resource Subscriptions

```bash
maton api '/gumroad/v2/resource_subscriptions?resource_name=sale'
```

Parameters:
- `resource_name` - Required. One of: `sale`, `refund`, `dispute`, `dispute_won`, `cancellation`, `subscription_updated`, `subscription_ended`, `subscription_restarted`

**Response:**
```json
{
  "success": true,
  "resource_subscriptions": [
    {
      "id": "wX43hzi-s7W4JfYFkxyeiQ==",
      "resource_name": "sale",
      "post_url": "https://example.com/webhook"
    }
  ]
}
```

#### Create Resource Subscription

```bash
maton api -X PUT '/gumroad/v2/resource_subscriptions' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
resource_name=sale&post_url=https://example.com/webhook
EOF
```

#### Delete Resource Subscription

```bash
maton api '/gumroad/v2/resource_subscriptions/{resource_subscription_id}' -X DELETE
```

**Note:** `{resource_subscription_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "success": true,
  "message": "The resource_subscription was deleted successfully."
}
```

### Variant Categories API

#### List Variant Categories

```bash
maton api '/gumroad/v2/products/{product_id}/variant_categories'
```

**Note:** `{product_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Variant Category

```bash
maton api '/gumroad/v2/products/{product_id}/variant_categories/{variant_category_id}'
```

**Note:** `{product_id}` and `{variant_category_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Variant Category

```bash
maton api -X POST '/gumroad/v2/products/{product_id}/variant_categories' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
title=Size
BODY
```

**Note:** `{product_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Variant Category

```bash
maton api '/gumroad/v2/products/{product_id}/variant_categories/{variant_category_id}' -X DELETE
```

**Note:** `{product_id}` and `{variant_category_id}` are placeholders. Replace each of them with real values before sending the request.

### Variants API

#### List Variants

```bash
maton api '/gumroad/v2/products/{product_id}/variant_categories/{variant_category_id}/variants'
```

**Note:** `{product_id}` and `{variant_category_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Variant

```bash
maton api -X POST '/gumroad/v2/products/{product_id}/variant_categories/{variant_category_id}/variants' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
name=Large&price_difference=200
BODY
```

**Note:** `{product_id}` and `{variant_category_id}` are placeholders. Replace each of them with real values before sending the request.

#### Update Variant

```bash
maton api -X PUT '/gumroad/v2/products/{product_id}/variant_categories/{variant_category_id}/variants/{variant_id}' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
name=Extra%20Large
BODY
```

**Note:** `{product_id}`, `{variant_category_id}` and `{variant_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Variant

```bash
maton api '/gumroad/v2/products/{product_id}/variant_categories/{variant_category_id}/variants/{variant_id}' -X DELETE
```

**Note:** `{product_id}`, `{variant_category_id}` and `{variant_id}` are placeholders. Replace each of them with real values before sending the request.

### Custom Fields API

#### List Custom Fields

```bash
maton api '/gumroad/v2/products/{product_id}/custom_fields'
```

**Note:** `{product_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Custom Field

```bash
maton api -X POST '/gumroad/v2/products/{product_id}/custom_fields' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
name=Company%20Name&required=true
BODY
```

**Note:** `{product_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Custom Field

```bash
maton api -X PUT '/gumroad/v2/products/{product_id}/custom_fields/{name}' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
required=false
BODY
```

**Note:** `{product_id}` and `{name}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Custom Field

```bash
maton api '/gumroad/v2/products/{product_id}/custom_fields/{name}' -X DELETE
```

**Note:** `{product_id}` and `{name}` are placeholders. Replace each of them with real values before sending the request.

### Pagination

Gumroad uses page-based pagination for endpoints that return lists:

```bash
maton api '/gumroad/v2/sales?page=1'

maton api '/gumroad/v2/sales?page=2'
```

Continue incrementing the page number until you receive an empty list.

### Notes

- All responses include a `success` boolean field
- Product creation is not available via API - products must be created through the Gumroad website
- POST/PUT requests use `application/x-www-form-urlencoded` content type (not JSON)
- Prices are in cents (e.g., 500 = $5.00)
- License keys are case-insensitive
- Resource subscription webhooks send POST requests to your specified URL

### Resources

- [Gumroad API Documentation](https://gumroad.com/api)
- [Create API Application](https://help.gumroad.com/article/280-create-application-api)
- [Maton CLI Manual](https://cli.maton.ai/manual)
