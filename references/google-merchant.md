# Google Merchant Routing Reference

**App name:** `google-merchant`
**Base URL proxied:** `merchantapi.googleapis.com`

## API Path Pattern

```
/google-merchant/{sub-api}/{version}/accounts/{accountId}/{resource}
```

The Merchant API uses sub-APIs: `products`, `accounts`, `datasources`, `reports`, `promotions`, `inventories`, `notifications`, `conversions`

**Important:** Use `v1beta` for all endpoints. The v1 API requires GCP project registration and will return 401 errors without it.

## Common Endpoints

### List Accounts
```bash
GET /google-merchant/accounts/v1beta/accounts
```

### List Products
```bash
GET /google-merchant/products/v1beta/accounts/{accountId}/products
```

### Get Product
```bash
GET /google-merchant/products/v1beta/accounts/{accountId}/products/{productId}
```

Product ID format: `channel~contentLanguage~feedLabel~offerId` (e.g., `online~en~US~sku123`)

### Insert Product Input
```bash
POST /google-merchant/products/v1beta/accounts/{accountId}/productInputs:insert?dataSource=accounts/{accountId}/dataSources/{dataSourceId}
Content-Type: application/json

{
  "offerId": "sku123",
  "contentLanguage": "en",
  "feedLabel": "US",
  "attributes": {
    "title": "Product Title",
    "link": "https://example.com/product",
    "imageLink": "https://example.com/image.jpg",
    "availability": "in_stock",
    "price": {"amountMicros": "19990000", "currencyCode": "USD"}
  }
}
```

### Delete Product Input
```bash
DELETE /google-merchant/products/v1beta/accounts/{accountId}/productInputs/{productId}?dataSource=accounts/{accountId}/dataSources/{dataSourceId}
```

### List Data Sources
```bash
GET /google-merchant/datasources/v1beta/accounts/{accountId}/dataSources
```

### Search Reports
```bash
POST /google-merchant/reports/v1beta/accounts/{accountId}/reports:search
Content-Type: application/json

{
  "query": "SELECT id, offer_id, title FROM product_view LIMIT 10"
}
```

Note: The `product_view` table requires the `id` field in SELECT clause.

### List Promotions
```bash
GET /google-merchant/promotions/v1beta/accounts/{accountId}/promotions
```

Note: Requires Promotions program enrollment.

### Get Account
```bash
GET /google-merchant/accounts/v1beta/accounts/{accountId}
```

### List Regional Inventories
```bash
GET /google-merchant/inventories/v1beta/accounts/{accountId}/products/{productId}/regionalInventories
```

### List Local Inventories
```bash
GET /google-merchant/inventories/v1beta/accounts/{accountId}/products/{productId}/localInventories
```

Note: Local inventories only work for products with LOCAL channel.

## Notes

- **Use v1beta** - v1 requires GCP project registration
- Authentication is automatic - the router injects the OAuth token
- Account ID is your Merchant Center numeric ID (visible in MC URL)
- Product IDs use format `channel~contentLanguage~feedLabel~offerId`
- Monetary values use micros (divide by 1,000,000)
- Products can only be inserted in data sources with `input: "API"` type
- Uses token-based pagination with `pageSize` and `pageToken`
- Promotions require account enrollment in Promotions program
- Local inventories only work for LOCAL channel products

## Resources

- [Merchant API Overview](https://developers.google.com/merchant/api/overview)
- [Merchant API Reference](https://developers.google.com/merchant/api/reference/rest)
- [Products Guide](https://developers.google.com/merchant/api/guides/products/overview)
- [Reports Guide](https://developers.google.com/merchant/api/guides/reports)
