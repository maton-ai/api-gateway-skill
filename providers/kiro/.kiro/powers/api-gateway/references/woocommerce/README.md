# WooCommerce Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `woocommerce`
**Base URL proxied:** `{store-url}/wp-json/wc/v3`

## API Path Pattern

```
/woocommerce/wp-json/wc/v3/{endpoint}
```

## Common Endpoints

### Products

#### List Products
```bash
maton api '/woocommerce/wp-json/wc/v3/products?per_page=20&status=publish'
```

#### Get Product
```bash
maton api '/woocommerce/wp-json/wc/v3/products/{id}'
```

#### Create Product
```bash
maton api -X POST '/woocommerce/wp-json/wc/v3/products' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"name": "Premium Widget", "type": "simple", "regular_price": "19.99", "sku": "WDG-001"}
EOF
```

#### Update Product
```bash
maton api -X PUT '/woocommerce/wp-json/wc/v3/products/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"regular_price": "24.99", "sale_price": "19.99"}
EOF
```

#### Delete Product
```bash
maton api -X DELETE '/woocommerce/wp-json/wc/v3/products/{id}?force=true'
```

### Product Variations

#### List Variations
```bash
maton api '/woocommerce/wp-json/wc/v3/products/{product_id}/variations'
```

#### Create Variation
```bash
maton api -X POST '/woocommerce/wp-json/wc/v3/products/{product_id}/variations' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"regular_price": "29.99", "sku": "TSH-001-RED-M", "attributes": [{"id": 1, "option": "Red"}]}
EOF
```

### Product Categories

#### List Categories
```bash
maton api '/woocommerce/wp-json/wc/v3/products/categories'
```

#### Create Category
```bash
maton api -X POST '/woocommerce/wp-json/wc/v3/products/categories' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"name": "Electronics", "description": "Electronic products"}
EOF
```

### Orders

#### List Orders
```bash
maton api '/woocommerce/wp-json/wc/v3/orders?status=processing&per_page=50'
```

#### Get Order
```bash
maton api '/woocommerce/wp-json/wc/v3/orders/{id}'
```

#### Create Order
```bash
maton api -X POST '/woocommerce/wp-json/wc/v3/orders' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"payment_method": "stripe", "set_paid": true, "billing": {"first_name": "John", "last_name": "Doe", "email": "john@example.com"}, "line_items": [{"product_id": 123, "quantity": 2}]}
EOF
```

#### Update Order Status
```bash
maton api -X PUT '/woocommerce/wp-json/wc/v3/orders/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"status": "completed"}
EOF
```

### Order Notes

#### List Order Notes
```bash
maton api '/woocommerce/wp-json/wc/v3/orders/{order_id}/notes'
```

#### Create Order Note
```bash
maton api -X POST '/woocommerce/wp-json/wc/v3/orders/{order_id}/notes' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"note": "Order shipped via FedEx", "customer_note": true}
EOF
```

### Order Refunds

#### Create Refund
```bash
maton api -X POST '/woocommerce/wp-json/wc/v3/orders/{order_id}/refunds' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"amount": "25.00", "reason": "Product damaged", "api_refund": true}
EOF
```

### Customers

#### List Customers
```bash
maton api '/woocommerce/wp-json/wc/v3/customers?per_page=25'
```

#### Get Customer
```bash
maton api '/woocommerce/wp-json/wc/v3/customers/{id}'
```

#### Create Customer
```bash
maton api -X POST '/woocommerce/wp-json/wc/v3/customers' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"email": "jane@example.com", "first_name": "Jane", "last_name": "Smith", "username": "janesmith"}
EOF
```

### Coupons

#### List Coupons
```bash
maton api '/woocommerce/wp-json/wc/v3/coupons'
```

#### Create Coupon
```bash
maton api -X POST '/woocommerce/wp-json/wc/v3/coupons' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"code": "SUMMER2024", "discount_type": "percent", "amount": "15", "usage_limit": 100}
EOF
```

### Taxes

#### List Tax Rates
```bash
maton api '/woocommerce/wp-json/wc/v3/taxes'
```

#### Create Tax Rate
```bash
maton api -X POST '/woocommerce/wp-json/wc/v3/taxes' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"country": "US", "state": "CA", "rate": "7.25", "name": "CA State Tax"}
EOF
```

### Shipping

#### List Shipping Zones
```bash
maton api '/woocommerce/wp-json/wc/v3/shipping/zones'
```

#### List Shipping Zone Methods
```bash
maton api '/woocommerce/wp-json/wc/v3/shipping/zones/{zone_id}/methods'
```

### Webhooks

#### List Webhooks
```bash
maton api '/woocommerce/wp-json/wc/v3/webhooks'
```

#### Create Webhook
```bash
maton api -X POST '/woocommerce/wp-json/wc/v3/webhooks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"name": "Order Created", "topic": "order.created", "delivery_url": "https://example.com/webhook", "status": "active"}
EOF
```

### Reports

#### Sales Report
```bash
maton api '/woocommerce/wp-json/wc/v3/reports/sales?period=month'
```

#### Top Sellers
```bash
maton api '/woocommerce/wp-json/wc/v3/reports/top_sellers'
```

#### Orders Totals
```bash
maton api '/woocommerce/wp-json/wc/v3/reports/orders/totals'
```

### Settings

#### List Settings Groups
```bash
maton api '/woocommerce/wp-json/wc/v3/settings'
```

#### Get Settings in Group
```bash
maton api '/woocommerce/wp-json/wc/v3/settings/{group}'
```

### System Status

#### Get System Status
```bash
maton api '/woocommerce/wp-json/wc/v3/system_status'
```

## Notes

- All monetary amounts are returned as strings with two decimal places
- Dates are in ISO8601 format: `YYYY-MM-DDTHH:MM:SS`
- Resource IDs are integers
- Pagination uses `page` and `per_page` parameters (max 100 per page)
- Response headers include `X-WP-Total` and `X-WP-TotalPages`
- Order statuses: `pending`, `processing`, `on-hold`, `completed`, `cancelled`, `refunded`, `failed`
- Discount types: `percent`, `fixed_cart`, `fixed_product`
- Use `force=true` query parameter to permanently delete (otherwise moves to trash)
- Batch operations supported via `POST /{resource}/batch` with `create`, `update`, `delete` arrays

## Resources

- [WooCommerce REST API Documentation](https://woocommerce.github.io/woocommerce-rest-api-docs/)
- [Products](https://woocommerce.github.io/woocommerce-rest-api-docs/#products)
- [Product Variations](https://woocommerce.github.io/woocommerce-rest-api-docs/#product-variations)
- [Product Attributes](https://woocommerce.github.io/woocommerce-rest-api-docs/#product-attributes)
- [Product Categories](https://woocommerce.github.io/woocommerce-rest-api-docs/#product-categories)
- [Product Tags](https://woocommerce.github.io/woocommerce-rest-api-docs/#product-tags)
- [Product Reviews](https://woocommerce.github.io/woocommerce-rest-api-docs/#product-reviews)
- [Orders](https://woocommerce.github.io/woocommerce-rest-api-docs/#orders)
- [Order Notes](https://woocommerce.github.io/woocommerce-rest-api-docs/#order-notes)
- [Refunds](https://woocommerce.github.io/woocommerce-rest-api-docs/#refunds)
- [Customers](https://woocommerce.github.io/woocommerce-rest-api-docs/#customers)
- [Coupons](https://woocommerce.github.io/woocommerce-rest-api-docs/#coupons)
- [Tax Rates](https://woocommerce.github.io/woocommerce-rest-api-docs/#tax-rates)
- [Tax Classes](https://woocommerce.github.io/woocommerce-rest-api-docs/#tax-classes)
- [Shipping Zones](https://woocommerce.github.io/woocommerce-rest-api-docs/#shipping-zones)
- [Shipping Methods](https://woocommerce.github.io/woocommerce-rest-api-docs/#shipping-methods)
- [Payment Gateways](https://woocommerce.github.io/woocommerce-rest-api-docs/#payment-gateways)
- [Settings](https://woocommerce.github.io/woocommerce-rest-api-docs/#settings)
- [Webhooks](https://woocommerce.github.io/woocommerce-rest-api-docs/#webhooks)
- [Reports](https://woocommerce.github.io/woocommerce-rest-api-docs/#reports)
- [System Status](https://woocommerce.github.io/woocommerce-rest-api-docs/#system-status)
