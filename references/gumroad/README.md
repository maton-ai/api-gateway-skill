# Gumroad Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `gumroad`
**Base URL proxied:** `api.gumroad.com`

## API Path Pattern

```
/gumroad/v2/{resource}
```

## Common Endpoints

### Get Current User
```bash
maton api '/gumroad/v2/user'
```

### List Products
```bash
maton api '/gumroad/v2/products'
```

### Get Product
```bash
maton api '/gumroad/v2/products/{product_id}'
```

### Update Product
```bash
maton api -X PUT '/gumroad/v2/products/{product_id}' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
name=Updated%20Name
EOF
```

### Delete Product
```bash
maton api -X DELETE '/gumroad/v2/products/{product_id}'
```

### List Sales
```bash
maton api '/gumroad/v2/sales'
maton api '/gumroad/v2/sales?after=2026-01-01&before=2026-12-31'
```

### Get Sale
```bash
maton api '/gumroad/v2/sales/{sale_id}'
```

### List Subscribers
```bash
maton api '/gumroad/v2/products/{product_id}/subscribers'
```

### Get Subscriber
```bash
maton api '/gumroad/v2/subscribers/{subscriber_id}'
```

### Verify License
```bash
maton api -X POST '/gumroad/v2/licenses/verify' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
product_id={product_id}&license_key={license_key}
EOF
```

### Enable/Disable License
```bash
maton api -X PUT '/gumroad/v2/licenses/enable'
maton api -X PUT '/gumroad/v2/licenses/disable'
```

### List Resource Subscriptions (Webhooks)
```bash
maton api '/gumroad/v2/resource_subscriptions?resource_name=sale'
```

Resource names: `sale`, `refund`, `dispute`, `dispute_won`, `cancellation`, `subscription_updated`, `subscription_ended`, `subscription_restarted`

### Create Resource Subscription
```bash
maton api -X PUT '/gumroad/v2/resource_subscriptions' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
resource_name=sale&post_url=https://example.com/webhook
EOF
```

### Delete Resource Subscription
```bash
maton api -X DELETE '/gumroad/v2/resource_subscriptions/{resource_subscription_id}'
```

### Offer Codes
```bash
maton api '/gumroad/v2/products/{product_id}/offer_codes'
maton api -X POST '/gumroad/v2/products/{product_id}/offer_codes'
maton api -X PUT '/gumroad/v2/products/{product_id}/offer_codes/{offer_code_id}'
maton api -X DELETE '/gumroad/v2/products/{product_id}/offer_codes/{offer_code_id}'
```

### Variant Categories
```bash
maton api '/gumroad/v2/products/{product_id}/variant_categories'
maton api -X POST '/gumroad/v2/products/{product_id}/variant_categories'
maton api -X DELETE '/gumroad/v2/products/{product_id}/variant_categories/{variant_category_id}'
```

### Variants
```bash
maton api '/gumroad/v2/products/{product_id}/variant_categories/{variant_category_id}/variants'
maton api -X POST '/gumroad/v2/products/{product_id}/variant_categories/{variant_category_id}/variants'
maton api -X PUT '/gumroad/v2/products/{product_id}/variant_categories/{variant_category_id}/variants/{variant_id}'
maton api -X DELETE '/gumroad/v2/products/{product_id}/variant_categories/{variant_category_id}/variants/{variant_id}'
```

### Custom Fields
```bash
maton api '/gumroad/v2/products/{product_id}/custom_fields'
maton api -X POST '/gumroad/v2/products/{product_id}/custom_fields'
maton api -X PUT '/gumroad/v2/products/{product_id}/custom_fields/{name}'
maton api -X DELETE '/gumroad/v2/products/{product_id}/custom_fields/{name}'
```

## Pagination

Page-based pagination:
```bash
maton api '/gumroad/v2/sales?page=1'
maton api '/gumroad/v2/sales?page=2'
```

## Notes

- All responses include `success` boolean field
- Product creation not available via API
- POST/PUT use `application/x-www-form-urlencoded` (not JSON)
- Prices in cents (500 = $5.00)
- License keys are case-insensitive

## Resources

- [Gumroad API Documentation](https://gumroad.com/api)
- [Create API Application](https://help.gumroad.com/article/280-create-application-api)
