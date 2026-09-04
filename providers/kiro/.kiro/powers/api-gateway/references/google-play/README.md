# Google Play Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `google-play`
**Base URL proxied:** `androidpublisher.googleapis.com`

## API Path Pattern

```
/google-play/androidpublisher/v3/applications/{packageName}/{resource}
```

## Common Endpoints

### In-App Products

#### List In-App Products
```bash
maton api '/google-play/androidpublisher/v3/applications/{packageName}/inappproducts'
```

#### Get In-App Product
```bash
maton api '/google-play/androidpublisher/v3/applications/{packageName}/inappproducts/{sku}'
```

#### Create In-App Product
```bash
maton api -X POST '/google-play/androidpublisher/v3/applications/{packageName}/inappproducts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
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
EOF
```

#### Delete In-App Product
```bash
maton api -X DELETE '/google-play/androidpublisher/v3/applications/{packageName}/inappproducts/{sku}'
```

### Subscriptions

#### List Subscriptions
```bash
maton api '/google-play/androidpublisher/v3/applications/{packageName}/subscriptions'
```

#### Get Subscription
```bash
maton api '/google-play/androidpublisher/v3/applications/{packageName}/subscriptions/{productId}'
```

### Purchases

#### Get Product Purchase
```bash
maton api '/google-play/androidpublisher/v3/applications/{packageName}/purchases/products/{productId}/tokens/{token}'
```

#### Acknowledge Purchase
```bash
maton api -X POST '/google-play/androidpublisher/v3/applications/{packageName}/purchases/products/{productId}/tokens/{token}:acknowledge' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "developerPayload": "optional payload"
}
EOF
```

#### Get Subscription Purchase
```bash
maton api '/google-play/androidpublisher/v3/applications/{packageName}/purchases/subscriptions/{subscriptionId}/tokens/{token}'
```

#### Cancel Subscription
```bash
maton api -X POST '/google-play/androidpublisher/v3/applications/{packageName}/purchases/subscriptions/{subscriptionId}/tokens/{token}:cancel'
```

### Reviews

#### List Reviews
```bash
maton api '/google-play/androidpublisher/v3/applications/{packageName}/reviews'
```

#### Get Review
```bash
maton api '/google-play/androidpublisher/v3/applications/{packageName}/reviews/{reviewId}'
```

#### Reply to Review
```bash
maton api -X POST '/google-play/androidpublisher/v3/applications/{packageName}/reviews/{reviewId}:reply' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "replyText": "Thank you for your feedback!"
}
EOF
```

### Edits (App Updates)

#### Create Edit
```bash
maton api -X POST '/google-play/androidpublisher/v3/applications/{packageName}/edits'
```

#### Commit Edit
```bash
maton api -X POST '/google-play/androidpublisher/v3/applications/{packageName}/edits/{editId}:commit'
```

#### Delete Edit
```bash
maton api -X DELETE '/google-play/androidpublisher/v3/applications/{packageName}/edits/{editId}'
```

## Notes

- Replace `{packageName}` with your app's package name (e.g., `com.example.app`)
- The Google Play Developer API requires the app to be published on Google Play
- Subscription management requires the app to have active subscriptions configured
- Edits are transactional - create an edit, make changes, then commit
- Prices are in micros (1,000,000 micros = 1 unit of currency)

## Resources

- [Android Publisher API Overview](https://developers.google.com/android-publisher)
- [In-App Products](https://developers.google.com/android-publisher/api-ref/rest/v3/inappproducts)
- [Subscriptions](https://developers.google.com/android-publisher/api-ref/rest/v3/monetization.subscriptions)
- [Purchases](https://developers.google.com/android-publisher/api-ref/rest/v3/purchases.products)
- [Reviews](https://developers.google.com/android-publisher/api-ref/rest/v3/reviews)
- [Edits](https://developers.google.com/android-publisher/api-ref/rest/v3/edits)
