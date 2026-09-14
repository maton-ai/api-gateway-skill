# Google Play

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `google-play`
**Upstream base URL:** `androidpublisher.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://androidpublisher.googleapis.com/androidpublisher/v3/applications/{packageName}/inappproducts`
- Gateway: `https://api.maton.ai/google-play/androidpublisher/v3/applications/{packageName}/inappproducts`

### In-App Products API

#### List In-App Products

```bash
maton api '/google-play/androidpublisher/v3/applications/{packageName}/inappproducts'
```

**Note:** `{packageName}` is a placeholder. Replace it with a real value before sending the request.

#### Get In-App Product

```bash
maton api '/google-play/androidpublisher/v3/applications/{packageName}/inappproducts/{sku}'
```

**Note:** `{packageName}` and `{sku}` are placeholders. Replace each of them with real values before sending the request.

#### Create In-App Product

```bash
maton api -X POST '/google-play/androidpublisher/v3/applications/{packageName}/inappproducts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "packageName": "com.example.app",
  "sku": "premium_upgrade",
  "status": "active",
  "purchaseType": "managedUser",
  "defaultPrice": {
    "priceMicros": "990000",
    "currency": "USD"
  },
  "listings": {
    "en-US": {
      "title": "Premium Upgrade",
      "description": "Unlock all premium features"
    }
  }
}
JSON
```

**Note:** `{packageName}` is a placeholder. Replace it with a real value before sending the request.

#### Update In-App Product

```bash
maton api -X PUT '/google-play/androidpublisher/v3/applications/{packageName}/inappproducts/{sku}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "packageName": "com.example.app",
  "sku": "premium_upgrade",
  "status": "active",
  "purchaseType": "managedUser",
  "defaultPrice": {
    "priceMicros": "1990000",
    "currency": "USD"
  }
}
JSON
```

**Note:** `{packageName}` and `{sku}` are placeholders. Replace each of them with real values before sending the request.

#### Delete In-App Product

```bash
maton api '/google-play/androidpublisher/v3/applications/{packageName}/inappproducts/{sku}' -X DELETE
```

**Note:** `{packageName}` and `{sku}` are placeholders. Replace each of them with real values before sending the request.

### Subscriptions API

#### List Subscriptions

```bash
maton api '/google-play/androidpublisher/v3/applications/{packageName}/subscriptions'
```

**Note:** `{packageName}` is a placeholder. Replace it with a real value before sending the request.

#### Get Subscription

```bash
maton api '/google-play/androidpublisher/v3/applications/{packageName}/subscriptions/{productId}'
```

**Note:** `{packageName}` and `{productId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Subscription

```bash
maton api -X POST '/google-play/androidpublisher/v3/applications/{packageName}/subscriptions' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "productId": "monthly_premium",
  "basePlans": [
    {
      "basePlanId": "p1m",
      "autoRenewingBasePlanType": {
        "billingPeriodDuration": "P1M"
      }
    }
  ],
  "listings": [
    {
      "languageCode": "en-US",
      "title": "Premium Monthly"
    }
  ]
}
JSON
```

**Note:** `{packageName}` is a placeholder. Replace it with a real value before sending the request.

### Purchases API

#### Get Product Purchase

```bash
maton api '/google-play/androidpublisher/v3/applications/{packageName}/purchases/products/{productId}/tokens/{token}'
```

**Note:** `{packageName}`, `{productId}` and `{token}` are placeholders. Replace each of them with real values before sending the request.

#### Acknowledge Purchase

```bash
maton api -X POST '/google-play/androidpublisher/v3/applications/{packageName}/purchases/products/{productId}/tokens/{token}:acknowledge' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "developerPayload": "optional payload"
}
JSON
```

**Note:** `{packageName}`, `{productId}` and `{token}` are placeholders. Replace each of them with real values before sending the request.

#### Get Subscription Purchase

```bash
maton api '/google-play/androidpublisher/v3/applications/{packageName}/purchases/subscriptions/{subscriptionId}/tokens/{token}'
```

**Note:** `{packageName}`, `{subscriptionId}` and `{token}` are placeholders. Replace each of them with real values before sending the request.

#### Cancel Subscription

```bash
maton api -X POST '/google-play/androidpublisher/v3/applications/{packageName}/purchases/subscriptions/{subscriptionId}/tokens/{token}:cancel'
```

**Note:** `{packageName}`, `{subscriptionId}` and `{token}` are placeholders. Replace each of them with real values before sending the request.

#### Refund Subscription

```bash
maton api -X POST '/google-play/androidpublisher/v3/applications/{packageName}/purchases/subscriptions/{subscriptionId}/tokens/{token}:refund'
```

**Note:** `{packageName}`, `{subscriptionId}` and `{token}` are placeholders. Replace each of them with real values before sending the request.

### Reviews API

#### List Reviews

```bash
maton api '/google-play/androidpublisher/v3/applications/{packageName}/reviews'
```

**Note:** `{packageName}` is a placeholder. Replace it with a real value before sending the request.

#### Get Review

```bash
maton api '/google-play/androidpublisher/v3/applications/{packageName}/reviews/{reviewId}'
```

**Note:** `{packageName}` and `{reviewId}` are placeholders. Replace each of them with real values before sending the request.

#### Reply to Review

```bash
maton api -X POST '/google-play/androidpublisher/v3/applications/{packageName}/reviews/{reviewId}:reply' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "replyText": "Thank you for your feedback!"
}
JSON
```

**Note:** `{packageName}` and `{reviewId}` are placeholders. Replace each of them with real values before sending the request.

### Edits API

#### Create Edit

```bash
maton api -X POST '/google-play/androidpublisher/v3/applications/{packageName}/edits'
```

**Note:** `{packageName}` is a placeholder. Replace it with a real value before sending the request.

#### Get Edit

```bash
maton api '/google-play/androidpublisher/v3/applications/{packageName}/edits/{editId}'
```

**Note:** `{packageName}` and `{editId}` are placeholders. Replace each of them with real values before sending the request.

#### Commit Edit

```bash
maton api -X POST '/google-play/androidpublisher/v3/applications/{packageName}/edits/{editId}:commit'
```

**Note:** `{packageName}` and `{editId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Edit

```bash
maton api '/google-play/androidpublisher/v3/applications/{packageName}/edits/{editId}' -X DELETE
```

**Note:** `{packageName}` and `{editId}` are placeholders. Replace each of them with real values before sending the request.

### Notes

- Replace `{packageName}` with your app's package name (e.g., `com.example.app`)
- The Google Play Developer API requires the app to be published on Google Play
- Subscription management requires the app to have active subscriptions configured
- Edits are transactional - create an edit, make changes, then commit
- Prices are in micros (1,000,000 micros = 1 unit of currency)

### Resources

- [Android Publisher API Overview](https://developers.google.com/android-publisher)
- [In-App Products](https://developers.google.com/android-publisher/api-ref/rest/v3/inappproducts)
- [Subscriptions](https://developers.google.com/android-publisher/api-ref/rest/v3/monetization.subscriptions)
- [Purchases](https://developers.google.com/android-publisher/api-ref/rest/v3/purchases.products)
- [Reviews](https://developers.google.com/android-publisher/api-ref/rest/v3/reviews)
- [Edits](https://developers.google.com/android-publisher/api-ref/rest/v3/edits)
- [Maton CLI Manual](https://cli.maton.ai/manual)
