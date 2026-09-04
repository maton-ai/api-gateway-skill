# Square Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `squareup`
**Base URL proxied:** `connect.squareup.com`

## API Path Pattern

```
/squareup/v2/{resource}
```

## Merchants

### Get Current Merchant
```bash
maton api '/squareup/v2/merchants/me'
```

## Locations

### List Locations
```bash
maton api '/squareup/v2/locations'
```

### Get Location
```bash
maton api '/squareup/v2/locations/{location_id}'
```

### Create Location
```bash
maton api -X POST '/squareup/v2/locations' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "location": {
    "name": "New Location",
    "address": {...}
  }
}
EOF
```

### Update Location
```bash
maton api -X PUT '/squareup/v2/locations/{location_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "location": {"name": "Updated Name"}
}
EOF
```

## Customers

### List Customers
```bash
maton api '/squareup/v2/customers'
```

### Get Customer
```bash
maton api '/squareup/v2/customers/{customer_id}'
```

### Create Customer
```bash
maton api -X POST '/squareup/v2/customers' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "given_name": "John",
  "family_name": "Doe",
  "email_address": "john@example.com"
}
EOF
```

### Update Customer
```bash
maton api -X PUT '/squareup/v2/customers/{customer_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "given_name": "Jane"
}
EOF
```

### Delete Customer
```bash
maton api -X DELETE '/squareup/v2/customers/{customer_id}'
```

### Search Customers
```bash
maton api -X POST '/squareup/v2/customers/search' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": {
    "filter": {
      "email_address": {"exact": "john@example.com"}
    }
  },
  "limit": 10
}
EOF
```

## Payments

### List Payments
```bash
maton api '/squareup/v2/payments'
maton api '/squareup/v2/payments?location_id={location_id}&begin_time=2026-01-01T00:00:00Z'
```

### Get Payment
```bash
maton api '/squareup/v2/payments/{payment_id}'
```

### Create Payment
```bash
maton api -X POST '/squareup/v2/payments' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "source_id": "cnon:card-nonce-ok",
  "idempotency_key": "unique-key",
  "amount_money": {"amount": 1000, "currency": "USD"},
  "location_id": "{location_id}"
}
EOF
```

### Complete Payment
```bash
maton api -X POST '/squareup/v2/payments/{payment_id}/complete'
```

### Cancel Payment
```bash
maton api -X POST '/squareup/v2/payments/{payment_id}/cancel'
```

## Refunds

### List Refunds
```bash
maton api '/squareup/v2/refunds'
```

### Get Refund
```bash
maton api '/squareup/v2/refunds/{refund_id}'
```

### Create Refund
```bash
maton api -X POST '/squareup/v2/refunds' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "idempotency_key": "unique-key",
  "payment_id": "{payment_id}",
  "amount_money": {"amount": 500, "currency": "USD"}
}
EOF
```

## Orders

### Create Order
```bash
maton api -X POST '/squareup/v2/orders' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "order": {
    "location_id": "{location_id}",
    "line_items": [
      {
        "name": "Item",
        "quantity": "1",
        "base_price_money": {"amount": 1000, "currency": "USD"}
      }
    ]
  },
  "idempotency_key": "unique-key"
}
EOF
```

### Get Order
```bash
maton api '/squareup/v2/orders/{order_id}'
```

### Search Orders
```bash
maton api -X POST '/squareup/v2/orders/search' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "location_ids": ["{location_id}"],
  "limit": 10
}
EOF
```

### Update Order
```bash
maton api -X PUT '/squareup/v2/orders/{order_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "order": {
    "location_id": "{location_id}",
    "version": 1
  },
  "idempotency_key": "unique-key"
}
EOF
```

### Pay Order
```bash
maton api -X POST '/squareup/v2/orders/{order_id}/pay' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "idempotency_key": "unique-key",
  "payment_ids": ["{payment_id}"]
}
EOF
```

## Catalog

### List Catalog
```bash
maton api '/squareup/v2/catalog/list'
maton api '/squareup/v2/catalog/list?types=ITEM,CATEGORY'
```

### Get Catalog Object
```bash
maton api '/squareup/v2/catalog/object/{object_id}'
```

### Upsert Catalog Object
```bash
maton api -X POST '/squareup/v2/catalog/object' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "idempotency_key": "unique-key",
  "object": {
    "type": "ITEM",
    "id": "#new-item",
    "item_data": {
      "name": "Coffee",
      "variations": [
        {
          "type": "ITEM_VARIATION",
          "id": "#variation",
          "item_variation_data": {
            "name": "Regular",
            "pricing_type": "FIXED_PRICING",
            "price_money": {"amount": 500, "currency": "USD"}
          }
        }
      ]
    }
  }
}
EOF
```

### Delete Catalog Object
```bash
maton api -X DELETE '/squareup/v2/catalog/object/{object_id}'
```

### Search Catalog
```bash
maton api -X POST '/squareup/v2/catalog/search' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "object_types": ["ITEM"],
  "query": {
    "text_query": {"keywords": ["coffee"]}
  }
}
EOF
```

### Get Catalog Info
```bash
maton api '/squareup/v2/catalog/info'
```

### Batch Upsert
```bash
maton api -X POST '/squareup/v2/catalog/batch-upsert' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "idempotency_key": "unique-key",
  "batches": [{"objects": [...]}]
}
EOF
```

## Invoices

### List Invoices
```bash
maton api '/squareup/v2/invoices?location_id={location_id}'
```

### Get Invoice
```bash
maton api '/squareup/v2/invoices/{invoice_id}'
```

### Create Invoice
```bash
maton api -X POST '/squareup/v2/invoices' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "invoice": {
    "location_id": "{location_id}",
    "order_id": "{order_id}",
    "primary_recipient": {"customer_id": "{customer_id}"},
    "payment_requests": [
      {"request_type": "BALANCE", "due_date": "2026-02-15"}
    ]
  },
  "idempotency_key": "unique-key"
}
EOF
```

### Update Invoice
```bash
maton api -X PUT '/squareup/v2/invoices/{invoice_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "invoice": {"version": 1},
  "idempotency_key": "unique-key"
}
EOF
```

### Publish Invoice
```bash
maton api -X POST '/squareup/v2/invoices/{invoice_id}/publish' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"version": 1, "idempotency_key": "unique-key"}
EOF
```

### Cancel Invoice
```bash
maton api -X POST '/squareup/v2/invoices/{invoice_id}/cancel' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"version": 1}
EOF
```

### Delete Invoice
```bash
maton api -X DELETE '/squareup/v2/invoices/{invoice_id}'
```

## Team Members

### Search Team Members
```bash
maton api -X POST '/squareup/v2/team-members/search' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": {
    "filter": {
      "location_ids": ["{location_id}"],
      "status": "ACTIVE"
    }
  }
}
EOF
```

### Get Team Member
```bash
maton api '/squareup/v2/team-members/{team_member_id}'
```

### Update Team Member
```bash
maton api -X PUT '/squareup/v2/team-members/{team_member_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "team_member": {
    "given_name": "Updated Name"
  }
}
EOF
```

## Loyalty

### List Loyalty Programs
```bash
maton api '/squareup/v2/loyalty/programs'
```

### Get Loyalty Program
```bash
maton api '/squareup/v2/loyalty/programs/{program_id}'
```

### Search Loyalty Accounts
```bash
maton api -X POST '/squareup/v2/loyalty/accounts/search' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": {
    "customer_ids": ["{customer_id}"]
  }
}
EOF
```

### Create Loyalty Account
```bash
maton api -X POST '/squareup/v2/loyalty/accounts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "loyalty_account": {
    "program_id": "{program_id}",
    "mapping": {"phone_number": "+15551234567"}
  },
  "idempotency_key": "unique-key"
}
EOF
```

## Payment Links (Online Checkout)

### List Payment Links
```bash
maton api '/squareup/v2/online-checkout/payment-links'
```

### Get Payment Link
```bash
maton api '/squareup/v2/online-checkout/payment-links/{id}'
```

### Create Payment Link
```bash
maton api -X POST '/squareup/v2/online-checkout/payment-links' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "idempotency_key": "unique-key",
  "quick_pay": {
    "name": "Test Payment",
    "price_money": {"amount": 1000, "currency": "USD"},
    "location_id": "{location_id}"
  }
}
EOF
```

### Update Payment Link
```bash
maton api -X PUT '/squareup/v2/online-checkout/payment-links/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "payment_link": {"version": 1}
}
EOF
```

### Delete Payment Link
```bash
maton api -X DELETE '/squareup/v2/online-checkout/payment-links/{id}'
```

## Cards

### List Cards
```bash
maton api '/squareup/v2/cards'
maton api '/squareup/v2/cards?customer_id={customer_id}'
```

### Get Card
```bash
maton api '/squareup/v2/cards/{card_id}'
```

### Create Card
```bash
maton api -X POST '/squareup/v2/cards' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "idempotency_key": "unique-key",
  "source_id": "cnon:card-nonce-ok",
  "card": {
    "customer_id": "{customer_id}"
  }
}
EOF
```

### Disable Card
```bash
maton api -X POST '/squareup/v2/cards/{card_id}/disable'
```

## Payouts

### List Payouts
```bash
maton api '/squareup/v2/payouts'
maton api '/squareup/v2/payouts?location_id={location_id}'
```

### Get Payout
```bash
maton api '/squareup/v2/payouts/{payout_id}'
```

### List Payout Entries
```bash
maton api '/squareup/v2/payouts/{payout_id}/payout-entries'
```

## Bank Accounts

### List Bank Accounts
```bash
maton api '/squareup/v2/bank-accounts'
```

### Get Bank Account
```bash
maton api '/squareup/v2/bank-accounts/{bank_account_id}'
```

## Terminal

### List Terminal Checkouts
```bash
maton api '/squareup/v2/terminals/checkouts'
```

### Create Terminal Checkout
```bash
maton api -X POST '/squareup/v2/terminals/checkouts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "idempotency_key": "unique-key",
  "checkout": {
    "amount_money": {"amount": 1000, "currency": "USD"},
    "device_options": {"device_id": "{device_id}"}
  }
}
EOF
```

### Search Terminal Checkouts
```bash
maton api -X POST '/squareup/v2/terminals/checkouts/search' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": {
    "filter": {
      "status": "COMPLETED"
    }
  }
}
EOF
```

## Notes

- All amounts are in smallest currency unit (cents for USD: 1000 = $10.00)
- Most write operations require an `idempotency_key`
- Cursor-based pagination: use `cursor` parameter with value from response
- Timestamps are ISO 8601 format
- Some endpoints require specific OAuth scopes

## Resources

- [Square API Overview](https://developer.squareup.com/docs)
- [Square API Reference](https://developer.squareup.com/reference/square)
- [Payments API](https://developer.squareup.com/reference/square/payments-api)
- [Customers API](https://developer.squareup.com/reference/square/customers-api)
- [Orders API](https://developer.squareup.com/reference/square/orders-api)
- [Catalog API](https://developer.squareup.com/reference/square/catalog-api)
- [Invoices API](https://developer.squareup.com/reference/square/invoices-api)
- [Team Members API](https://developer.squareup.com/reference/square/team-api)
- [Loyalty API](https://developer.squareup.com/reference/square/loyalty-api)
- [Online Checkout API](https://developer.squareup.com/reference/square/checkout-api)
