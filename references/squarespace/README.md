# Squarespace Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `squarespace`
**Base URL proxied:** `api.squarespace.com`

## API Path Pattern

```
/squarespace/v2/commerce/products      # Products API (v2)
/squarespace/1.0/commerce/store_pages  # Store Pages (1.0 only)
/squarespace/1.0/commerce/inventory    # Inventory API
/squarespace/1.0/commerce/orders       # Orders API
/squarespace/1.0/commerce/transactions # Transactions API
/squarespace/1.0/profiles              # Profiles API
```

## Important Notes

- All requests require a `User-Agent` header describing your application
- Requests without a custom User-Agent are subject to stricter rate limits
- Maximum 50 items per batch request
- Idempotency-Key header is required for stock adjustments and order creation
- Rate limit: 300 requests per minute (5 per second)
- Create Order has a stricter rate limit: 100 requests per hour per website

## Common Endpoints

### Inventory

#### List All Inventory
```bash
maton api '/squarespace/1.0/commerce/inventory'
maton api '/squarespace/1.0/commerce/inventory?cursor={cursor}'
```

#### Get Specific Inventory
```bash
maton api '/squarespace/1.0/commerce/inventory/{variantId1},{variantId2}'
```
Max 50 variant IDs per request.

#### Adjust Stock Quantities
```bash
maton api -X POST '/squarespace/1.0/commerce/inventory/adjustments' \
  -H 'Content-Type: application/json' \
  -H 'Idempotency-Key: unique-key-here' \
  --input - <<'EOF'
{
  "incrementOperations": [{"variantId": "variant-id", "quantity": 5}],
  "decrementOperations": [{"variantId": "variant-id", "quantity": 2}],
  "setFiniteOperations": [{"variantId": "variant-id", "quantity": 100}],
  "setUnlimitedOperations": ["variant-id"]
}
EOF
```

### Orders

#### List All Orders
```bash
maton api '/squarespace/1.0/commerce/orders'
maton api '/squarespace/1.0/commerce/orders?fulfillmentStatus=PENDING'
maton api '/squarespace/1.0/commerce/orders?modifiedAfter=2024-01-01T00:00:00Z&modifiedBefore=2024-12-31T23:59:59Z'
maton api '/squarespace/1.0/commerce/orders?customerId={customerId}'
```

Note: Cannot combine `cursor` with date range parameters.

#### Get Specific Order
```bash
maton api '/squarespace/1.0/commerce/orders/{orderId}'
```

#### Create Order
```bash
maton api -X POST '/squarespace/1.0/commerce/orders' \
  -H 'Content-Type: application/json' \
  -H 'Idempotency-Key: unique-key-here' \
  --input - <<'EOF'
{
  "channelName": "External Store",
  "externalOrderReference": "ORDER-12345",
  "customerEmail": "customer@example.com",
  "lineItems": [
    {
      "lineItemType": "PHYSICAL_PRODUCT",
      "variantId": "variant-id",
      "quantity": 2,
      "unitPricePaid": {"currency": "USD", "value": "29.99"}
    }
  ],
  "subtotal": {"currency": "USD", "value": "59.98"},
  "priceTaxInterpretation": "EXCLUSIVE",
  "grandTotal": {"currency": "USD", "value": "59.98"},
  "createdOn": "2024-01-15T10:30:00Z"
}
EOF
```

Note: `subtotal` must equal sum of `lineItems.unitPricePaid.value * quantity`.

#### Fulfill Order
```bash
maton api -X POST '/squarespace/1.0/commerce/orders/{orderId}/fulfillments' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "shouldSendNotification": true,
  "shipments": [
    {
      "shipDate": "2024-01-16T08:00:00Z",
      "carrierName": "USPS",
      "service": "Priority Mail",
      "trackingNumber": "9400111899223456789012",
      "trackingUrl": "https://tools.usps.com/go/TrackConfirmAction?tLabels=9400111899223456789012"
    }
  ]
}
EOF
```

### Products (v2 API)

#### List Store Pages
```bash
maton api '/squarespace/1.0/commerce/store_pages'
```
Note: Store Pages endpoint uses v1.0 (no v2 available).

#### List All Products
```bash
maton api '/squarespace/v2/commerce/products'
maton api '/squarespace/v2/commerce/products?type=PHYSICAL,SERVICE,GIFT_CARD,DIGITAL'
maton api '/squarespace/v2/commerce/products?modifiedAfter=2024-01-01T00:00:00Z'
```

Note: Cannot combine `cursor` with date/type filters.

#### Get Specific Products
```bash
maton api '/squarespace/v2/commerce/products/{productId1},{productId2}'
```
Max 50 product IDs per request.

#### Create Product
```bash
maton api -X POST '/squarespace/v2/commerce/products' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "type": "PHYSICAL",
  "storePageId": "store-page-id",
  "name": "New Product",
  "description": "<p>Product description</p>",
  "urlSlug": "new-product",
  "isVisible": true,
  "variants": [
    {
      "sku": "SKU-001",
      "pricing": {"basePrice": {"currency": "USD", "value": "49.99"}},
      "stock": {"quantity": 100, "unlimited": false}
    }
  ]
}
EOF
```

#### Update Product
```bash
maton api -X POST '/squarespace/v2/commerce/products/{productId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Updated Product Name",
  "isVisible": true
}
EOF
```

#### Delete Product
```bash
maton api -X DELETE '/squarespace/v2/commerce/products/{productId}'
```

### Product Variants (v2 API)

#### Create Variant
```bash
maton api -X POST '/squarespace/v2/commerce/products/{productId}/variants' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "sku": "SKU-002",
  "pricing": {"basePrice": {"currency": "USD", "value": "59.99"}},
  "stock": {"quantity": 50, "unlimited": false},
  "attributes": {"Size": "Large"}
}
EOF
```

Note: To use `attributes`, product must have matching `variantAttributes` set first via Update Product.

#### Update Variant
```bash
maton api -X POST '/squarespace/v2/commerce/products/{productId}/variants/{variantId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "sku": "SKU-002-UPDATED",
  "pricing": {"basePrice": {"currency": "USD", "value": "64.99"}}
}
EOF
```

#### Delete Variant
```bash
maton api -X DELETE '/squarespace/v2/commerce/products/{productId}/variants/{variantId}'
```

### Product Images (v2 API)

#### Upload Image
```bash
POST /squarespace/v2/commerce/products/{productId}/images
Content-Type: multipart/form-data

# Multipart bodies are the one thing `maton api` cannot express, so this call is raw.
# `$(maton token)` supplies a short-lived token instead of a long-lived key.
curl "https://api.maton.ai/squarespace/v2/commerce/products/{productId}/images" \
  -H "Authorization: Bearer $(maton token)" \
  -H "User-Agent: MyClaude/1.0" \
  -X POST \
  -F file=@image.png
```

#### Check Upload Status
```bash
maton api '/squarespace/v2/commerce/products/{productId}/images/{imageId}/status'
```

#### Update Image Alt Text
```bash
maton api -X POST '/squarespace/v2/commerce/products/{productId}/images/{imageId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"altText": "Product image description"}
EOF
```

#### Reorder Image
```bash
maton api -X POST '/squarespace/v2/commerce/products/{productId}/images/{imageId}/order' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"afterImageId": "other-image-id"}
EOF
```

#### Assign Image to Variant
```bash
maton api -X POST '/squarespace/v2/commerce/products/{productId}/variants/{variantId}/image' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"imageId": "image-id"}
EOF
```

#### Delete Image
```bash
maton api -X DELETE '/squarespace/v2/commerce/products/{productId}/images/{imageId}'
```

### Profiles (Customers)

#### List All Profiles
```bash
maton api '/squarespace/1.0/profiles'
maton api '/squarespace/1.0/profiles?filter=isCustomer,true'
maton api '/squarespace/1.0/profiles?sortField=email&sortDirection=asc'
```

Filters (semicolon-separated):
- `isCustomer,true` or `isCustomer,false`
- `hasAccount,true` or `hasAccount,false`
- `email,customer@example.com`

Sort fields: `createdOn`, `id`, `email`, `lastName`

#### Get Specific Profiles
```bash
maton api '/squarespace/1.0/profiles/{profileId1},{profileId2}'
```
Max 50 profile IDs per request.

### Transactions

#### List All Transactions
```bash
maton api '/squarespace/1.0/commerce/transactions'
maton api '/squarespace/1.0/commerce/transactions?modifiedAfter=2024-01-01T00:00:00Z&modifiedBefore=2024-12-31T23:59:59Z'
```

Note: Date filters must be used together (both `modifiedAfter` and `modifiedBefore` required).

#### Get Specific Transactions
```bash
maton api '/squarespace/1.0/commerce/transactions/{documentId1},{documentId2}'
```
Max 50 document IDs per request.

## Pagination

Squarespace uses cursor-based pagination:

```json
{
  "pagination": {
    "hasNextPage": true,
    "nextPageCursor": "cursor-value",
    "nextPageUrl": "https://api.squarespace.com/..."
  }
}
```

Use the `cursor` parameter to get the next page:
```bash
maton api '/squarespace/v2/commerce/products?cursor=cursor-value'
```

## Resources

- [Squarespace Commerce APIs Overview](https://developers.squarespace.com/commerce-apis/overview)
- [Inventory API](https://developers.squarespace.com/commerce-apis/inventory-overview)
- [Orders API](https://developers.squarespace.com/commerce-apis/orders-overview)
- [Products API](https://developers.squarespace.com/commerce-apis/products-overview)
- [Profiles API](https://developers.squarespace.com/commerce-apis/profiles-overview)
- [Transactions API](https://developers.squarespace.com/commerce-apis/transactions-overview)
