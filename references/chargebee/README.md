# Chargebee

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `chargebee`
**Upstream base URL:** `{subdomain}.chargebee.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://{subdomain}.chargebee.com/api/v2/customers`
- Gateway: `https://api.maton.ai/chargebee/api/v2/customers`

### Customers API

#### List Customers

```bash
maton api '/chargebee/api/v2/customers?limit=10'
```

#### Get Customer

```bash
maton api '/chargebee/api/v2/customers/{customerId}'
```

**Note:** `{customerId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Customer

```bash
maton api -X POST '/chargebee/api/v2/customers' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
first_name=John&last_name=Doe&email=john@example.com
BODY
```

#### Update Customer

```bash
maton api -X POST '/chargebee/api/v2/customers/{customerId}' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
first_name=Jane
BODY
```

**Note:** `{customerId}` is a placeholder. Replace it with a real value before sending the request.

### Subscriptions API

#### List Subscriptions

```bash
maton api '/chargebee/api/v2/subscriptions?limit=10'
```

#### Get Subscription

```bash
maton api '/chargebee/api/v2/subscriptions/{subscriptionId}'
```

**Note:** `{subscriptionId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Subscription

```bash
maton api -X POST '/chargebee/api/v2/subscriptions' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
plan_id=basic-plan&customer[email]=john@example.com&customer[first_name]=John
BODY
```

#### Cancel Subscription

```bash
maton api -X POST '/chargebee/api/v2/subscriptions/{subscriptionId}/cancel' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
end_of_term=true
BODY
```

**Note:** `{subscriptionId}` is a placeholder. Replace it with a real value before sending the request.

### Item Prices API

#### List Item Prices

```bash
maton api '/chargebee/api/v2/item_prices?limit=10'
```

#### Get Item Price

```bash
maton api '/chargebee/api/v2/item_prices/{itemPriceId}'
```

**Note:** `{itemPriceId}` is a placeholder. Replace it with a real value before sending the request.

### Items API

#### List Items

```bash
maton api '/chargebee/api/v2/items?limit=10'
```

#### Get Item

```bash
maton api '/chargebee/api/v2/items/{itemId}'
```

**Note:** `{itemId}` is a placeholder. Replace it with a real value before sending the request.

### Plans API

#### List Plans

```bash
maton api '/chargebee/api/v2/plans?limit=10'
```

#### Get Plan

```bash
maton api '/chargebee/api/v2/plans/{planId}'
```

**Note:** `{planId}` is a placeholder. Replace it with a real value before sending the request.

### Invoices API

#### List Invoices

```bash
maton api '/chargebee/api/v2/invoices?limit=10'
```

#### Get Invoice

```bash
maton api '/chargebee/api/v2/invoices/{invoiceId}'
```

**Note:** `{invoiceId}` is a placeholder. Replace it with a real value before sending the request.

#### Download Invoice PDF

```bash
maton api -X POST '/chargebee/api/v2/invoices/{invoiceId}/pdf'
```

### Transactions API

#### List Transactions

```bash
maton api '/chargebee/api/v2/transactions?limit=10'
```

### Hosted Pages API

#### Checkout New Subscription

```bash
maton api -X POST '/chargebee/api/v2/hosted_pages/checkout_new_for_items' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
subscription[plan_id]=basic-plan&customer[email]=john@example.com
BODY
```

#### Manage Payment Sources

```bash
maton api -X POST '/chargebee/api/v2/hosted_pages/manage_payment_sources' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
customer[id]=cust_123
EOF
```

### Portal Sessions API

#### Create Portal Session

```bash
maton api -X POST '/chargebee/api/v2/portal_sessions' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
customer[id]=cust_123
BODY
```

### Filtering

Use filter parameters:
```bash
maton api '/chargebee/api/v2/subscriptions?status[is]=active'
maton api '/chargebee/api/v2/customers?email[is]=john@example.com'
maton api '/chargebee/api/v2/invoices?date[after]=1704067200'
```

### Notes

- Subdomain is automatically determined from your connection
- Uses form-urlencoded data for POST requests
- Nested objects use bracket notation: `customer[email]`
- Timestamps are Unix timestamps
- List responses include `next_offset` for pagination
- Status values: `active`, `cancelled`, `non_renewing`, etc.
- **Product Catalog versions**: Use `item_prices` and `items` for PC 2.0, or `plans` and `addons` for PC 1.0

### Resources

- [Chargebee Getting Started](https://apidocs.chargebee.com/docs/api)
- [List Customers](https://apidocs.chargebee.com/docs/api/customers/list-customers.md)
- [Retrieve a Customer](https://apidocs.chargebee.com/docs/api/customers/retrieve-a-customer.md)
- [Create a Customer](https://apidocs.chargebee.com/docs/api/customers/create-a-customer.md)
- [Update a Customer](https://apidocs.chargebee.com/docs/api/customers/update-a-customer.md)
- [List Subscriptions](https://apidocs.chargebee.com/docs/api/subscriptions/list-subscriptions.md)
- [Retrieve a Subscription](https://apidocs.chargebee.com/docs/api/subscriptions/retrieve-a-subscription.md)
- [Create a Subscription](https://apidocs.chargebee.com/docs/api/subscriptions/create-subscription-for-items.md)
- [Cancel a Subscription](https://apidocs.chargebee.com/docs/api/subscriptions/cancel-subscription-for-items.md)
- [List Items](https://apidocs.chargebee.com/docs/api/items/list-items.md)
- [Retrieve an Item](https://apidocs.chargebee.com/docs/api/items/retrieve-an-item.md)
- [List Item Prices](https://apidocs.chargebee.com/docs/api/item_prices/list-item-prices.md)
- [Retrieve an Item Price](https://apidocs.chargebee.com/docs/api/item_prices/retrieve-an-item-price.md)
- [List Plans](https://apidocs.chargebee.com/docs/api/v2/pcv-1/plans/list-plans.md)
- [Retrieve a Plan](https://apidocs.chargebee.com/docs/api/v2/pcv-1/plans/retrieve-a-plan.md)
- [List Invoices](https://apidocs.chargebee.com/docs/api/invoices/list-invoices.md)
- [Retrieve an Invoice](https://apidocs.chargebee.com/docs/api/invoices/retrieve-an-invoice.md)
- [Download Invoice as PDF](https://apidocs.chargebee.com/docs/api/invoices/download-e-invoice.md)
- [List Transactions](https://apidocs.chargebee.com/docs/api/transactions/list-transactions.md)
- [Checkout New Subscription](https://apidocs.chargebee.com/docs/api/hosted_pages/create-checkout-for-a-new-subscription.md)
- [Manage Payment Sources](https://apidocs.chargebee.com/docs/api/hosted_pages/manage-payment-sources.md)
- [Create a Portal Session](https://apidocs.chargebee.com/docs/api/portal_sessions/create-a-portal-session.md)
- [Maton CLI Manual](https://cli.maton.ai/manual)
