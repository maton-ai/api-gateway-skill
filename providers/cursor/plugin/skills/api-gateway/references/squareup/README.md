# Square

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `squareup`
**Upstream base URL:** `connect.squareup.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://connect.squareup.com/v2/locations`
- Gateway: `https://api.maton.ai/squareup/v2/locations`

### Locations API

#### List Locations

```bash
maton api '/squareup/v2/locations'
```

#### Get Location

```bash
maton api '/squareup/v2/locations/{location_id}'
```

**Note:** `{location_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Location

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

#### Update Location

```bash
maton api -X PUT '/squareup/v2/locations/{location_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "location": {
    "name": "Updated Location Name"
  }
}
JSON
```

**Note:** `{location_id}` is a placeholder. Replace it with a real value before sending the request.

### Merchants API

#### Get Current Merchant

```bash
maton api '/squareup/v2/merchants/me'
```

#### List Merchants

```bash
maton api '/squareup/v2/merchants'
```

### Payments API

#### List Payments

```bash
maton api '/squareup/v2/payments'
```

With filters:

```bash
maton api '/squareup/v2/payments?location_id={location_id}&begin_time=2026-01-01T00:00:00Z&end_time=2026-02-01T00:00:00Z'
```

**Note:** `{location_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Payment

```bash
maton api '/squareup/v2/payments/{payment_id}'
```

**Note:** `{payment_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Payment

```bash
maton api -X POST '/squareup/v2/payments' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "source_id": "cnon:card-nonce-ok",
  "idempotency_key": "unique-key-12345",
  "amount_money": {
    "amount": 1000,
    "currency": "USD"
  },
  "location_id": "{location_id}"
}
JSON
```

**Note:** `{location_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Payment

```bash
maton api -X PUT '/squareup/v2/payments/{payment_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "payment": {
    "tip_money": {
      "amount": 200,
      "currency": "USD"
    }
  },
  "idempotency_key": "unique-key-67890"
}
JSON
```

**Note:** `{payment_id}` is a placeholder. Replace it with a real value before sending the request.

#### Complete Payment

```bash
maton api -X POST '/squareup/v2/payments/{payment_id}/complete' -H 'Content-Type: application/json' --input - <<'JSON'
{}
JSON
```

**Note:** `{payment_id}` is a placeholder. Replace it with a real value before sending the request.

#### Cancel Payment

```bash
maton api -X POST '/squareup/v2/payments/{payment_id}/cancel' -H 'Content-Type: application/json' --input - <<'JSON'
{}
JSON
```

**Note:** `{payment_id}` is a placeholder. Replace it with a real value before sending the request.

### Refunds API

#### List Refunds

```bash
maton api '/squareup/v2/refunds'
```

#### Get Refund

```bash
maton api '/squareup/v2/refunds/{refund_id}'
```

**Note:** `{refund_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Refund

```bash
maton api -X POST '/squareup/v2/refunds' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "idempotency_key": "unique-refund-key",
  "payment_id": "{payment_id}",
  "amount_money": {
    "amount": 500,
    "currency": "USD"
  },
  "reason": "Customer requested refund"
}
JSON
```

**Note:** `{payment_id}` is a placeholder. Replace it with a real value before sending the request.

### Customers API

#### List Customers

```bash
maton api '/squareup/v2/customers'
```

#### Get Customer

```bash
maton api '/squareup/v2/customers/{customer_id}'
```

**Note:** `{customer_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Customer

```bash
maton api -X POST '/squareup/v2/customers' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "given_name": "John",
  "family_name": "Doe",
  "email_address": "john.doe@example.com",
  "phone_number": "+15551234567"
}
JSON
```

#### Update Customer

```bash
maton api -X PUT '/squareup/v2/customers/{customer_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "given_name": "Jane"
}
EOF
```

**Note:** `{customer_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Customer

```bash
maton api '/squareup/v2/customers/{customer_id}' -X DELETE
```

**Note:** `{customer_id}` is a placeholder. Replace it with a real value before sending the request.

#### Search Customers

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

### Orders API

#### Create Order

```bash
maton api -X POST '/squareup/v2/orders' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "order": {
    "location_id": "{location_id}",
    "line_items": [
      {
        "name": "Item 1",
        "quantity": "1",
        "base_price_money": {
          "amount": 1000,
          "currency": "USD"
        }
      }
    ]
  },
  "idempotency_key": "unique-order-key"
}
JSON
```

**Note:** `{location_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Order

```bash
maton api '/squareup/v2/orders/{order_id}'
```

**Note:** `{order_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Order

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

**Note:** `{order_id}` and `{location_id}` are placeholders. Replace each of them with real values before sending the request.

#### Search Orders

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

**Note:** `{location_id}` is a placeholder. Replace it with a real value before sending the request.

#### Batch Retrieve Orders

```bash
maton api -X POST '/squareup/v2/orders/batch-retrieve' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "location_id": "{location_id}",
  "order_ids": ["{order_id_1}", "{order_id_2}"]
}
JSON
```

**Note:** `{location_id}`, `{order_id_1}` and `{order_id_2}` are placeholders. Replace each of them with real values before sending the request.

#### Pay Order

```bash
maton api -X POST '/squareup/v2/orders/{order_id}/pay' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "idempotency_key": "unique-key",
  "payment_ids": ["{payment_id}"]
}
JSON
```

**Note:** `{order_id}` and `{payment_id}` are placeholders. Replace each of them with real values before sending the request.

### Catalog API

#### List Catalog

```bash
maton api '/squareup/v2/catalog/list'
```

With type filter:

```bash
maton api '/squareup/v2/catalog/list?types=ITEM,CATEGORY'
```

#### Get Catalog Object

```bash
maton api '/squareup/v2/catalog/object/{object_id}'
```

**Note:** `{object_id}` is a placeholder. Replace it with a real value before sending the request.

#### Upsert Catalog Object

```bash
maton api -X POST '/squareup/v2/catalog/object' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "idempotency_key": "unique-catalog-key",
  "object": {
    "type": "ITEM",
    "id": "#new-item",
    "item_data": {
      "name": "Coffee",
      "description": "Hot brewed coffee",
      "variations": [
        {
          "type": "ITEM_VARIATION",
          "id": "#small-coffee",
          "item_variation_data": {
            "name": "Small",
            "pricing_type": "FIXED_PRICING",
            "price_money": {
              "amount": 300,
              "currency": "USD"
            }
          }
        }
      ]
    }
  }
}
JSON
```

#### Delete Catalog Object

```bash
maton api '/squareup/v2/catalog/object/{object_id}' -X DELETE
```

**Note:** `{object_id}` is a placeholder. Replace it with a real value before sending the request.

#### Batch Upsert

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

#### Search Catalog

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

#### Get Catalog Info

```bash
maton api '/squareup/v2/catalog/info'
```

### Inventory API

#### Retrieve Inventory Count

```bash
maton api '/squareup/v2/inventory/{catalog_object_id}'
```

**Note:** `{catalog_object_id}` is a placeholder. Replace it with a real value before sending the request.

#### Batch Retrieve Inventory Counts

```bash
maton api -X POST '/squareup/v2/inventory/counts/batch-retrieve' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "catalog_object_ids": ["{object_id_1}", "{object_id_2}"],
  "location_ids": ["{location_id}"]
}
JSON
```

**Note:** `{object_id_1}`, `{object_id_2}` and `{location_id}` are placeholders. Replace each of them with real values before sending the request.

#### Batch Change Inventory

```bash
maton api -X POST '/squareup/v2/inventory/changes/batch-create' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "idempotency_key": "unique-inventory-key",
  "changes": [
    {
      "type": "ADJUSTMENT",
      "adjustment": {
        "catalog_object_id": "{object_id}",
        "location_id": "{location_id}",
        "quantity": "10",
        "from_state": "NONE",
        "to_state": "IN_STOCK"
      }
    }
  ]
}
JSON
```

**Note:** `{object_id}` and `{location_id}` are placeholders. Replace each of them with real values before sending the request.

#### Retrieve Inventory Adjustment

```bash
maton api '/squareup/v2/inventory/adjustments/{adjustment_id}'
```

**Note:** `{adjustment_id}` is a placeholder. Replace it with a real value before sending the request.

### Invoices API

#### List Invoices

```bash
maton api '/squareup/v2/invoices?location_id={location_id}'
```

**Note:** `{location_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Invoice

```bash
maton api '/squareup/v2/invoices/{invoice_id}'
```

**Note:** `{invoice_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Invoice

```bash
maton api -X POST '/squareup/v2/invoices' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "invoice": {
    "location_id": "{location_id}",
    "order_id": "{order_id}",
    "primary_recipient": {
      "customer_id": "{customer_id}"
    },
    "payment_requests": [
      {
        "request_type": "BALANCE",
        "due_date": "2026-02-15"
      }
    ],
    "delivery_method": "EMAIL"
  },
  "idempotency_key": "unique-invoice-key"
}
JSON
```

**Note:** `{location_id}`, `{order_id}` and `{customer_id}` are placeholders. Replace each of them with real values before sending the request.

#### Update Invoice

```bash
maton api -X PUT '/squareup/v2/invoices/{invoice_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "invoice": {
    "version": 1,
    "payment_requests": [
      {
        "uid": "{payment_request_uid}",
        "due_date": "2026-02-20"
      }
    ]
  },
  "idempotency_key": "unique-update-key"
}
JSON
```

**Note:** `{invoice_id}` and `{payment_request_uid}` are placeholders. Replace each of them with real values before sending the request.

#### Publish Invoice

```bash
maton api -X POST '/squareup/v2/invoices/{invoice_id}/publish' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "version": 1,
  "idempotency_key": "unique-publish-key"
}
JSON
```

**Note:** `{invoice_id}` is a placeholder. Replace it with a real value before sending the request.

#### Cancel Invoice

```bash
maton api -X POST '/squareup/v2/invoices/{invoice_id}/cancel' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "version": 1
}
JSON
```

**Note:** `{invoice_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Invoice

```bash
maton api '/squareup/v2/invoices/{invoice_id}' -X DELETE
```

**Note:** `{invoice_id}` is a placeholder. Replace it with a real value before sending the request.

#### Search Invoices

```bash
maton api -X POST '/squareup/v2/invoices/search' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": {
    "filter": {
      "location_ids": ["{location_id}"],
      "customer_ids": ["{customer_id}"]
    }
  }
}
JSON
```

**Note:** `{location_id}` and `{customer_id}` are placeholders. Replace each of them with real values before sending the request.

### Team Members API

#### Search Team Members

```bash
maton api -X POST '/squareup/v2/team-members/search' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": {
    "filter": {
      "location_ids": ["{location_id}"],
      "status": "ACTIVE"
    }
  }
}
JSON
```

**Note:** `{location_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Team Member

```bash
maton api '/squareup/v2/team-members/{team_member_id}'
```

**Note:** `{team_member_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Team Member

```bash
maton api -X PUT '/squareup/v2/team-members/{team_member_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "team_member": {
    "given_name": "Updated Name"
  }
}
JSON
```

**Note:** `{team_member_id}` is a placeholder. Replace it with a real value before sending the request.

### Loyalty API

#### List Loyalty Programs

```bash
maton api '/squareup/v2/loyalty/programs'
```

#### Get Loyalty Program

```bash
maton api '/squareup/v2/loyalty/programs/{program_id}'
```

**Note:** `{program_id}` is a placeholder. Replace it with a real value before sending the request.

#### Search Loyalty Accounts

```bash
maton api -X POST '/squareup/v2/loyalty/accounts/search' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": {
    "customer_ids": ["{customer_id}"]
  }
}
JSON
```

**Note:** `{customer_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Loyalty Account

```bash
maton api -X POST '/squareup/v2/loyalty/accounts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "loyalty_account": {
    "program_id": "{program_id}",
    "mapping": {
      "phone_number": "+15551234567"
    }
  },
  "idempotency_key": "unique-key"
}
JSON
```

**Note:** `{program_id}` is a placeholder. Replace it with a real value before sending the request.

#### Accumulate Loyalty Points

```bash
maton api -X POST '/squareup/v2/loyalty/accounts/{account_id}/accumulate' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "accumulate_points": {
    "order_id": "{order_id}"
  },
  "location_id": "{location_id}",
  "idempotency_key": "unique-key"
}
JSON
```

**Note:** `{account_id}`, `{order_id}` and `{location_id}` are placeholders. Replace each of them with real values before sending the request.

### Payment Links API

#### List Payment Links

```bash
maton api '/squareup/v2/online-checkout/payment-links'
```

#### Get Payment Link

```bash
maton api '/squareup/v2/online-checkout/payment-links/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Payment Link

```bash
maton api -X POST '/squareup/v2/online-checkout/payment-links' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "idempotency_key": "unique-key",
  "quick_pay": {
    "name": "Payment for Service",
    "price_money": {
      "amount": 1000,
      "currency": "USD"
    },
    "location_id": "{location_id}"
  }
}
JSON
```

**Note:** `{location_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Payment Link

```bash
maton api -X PUT '/squareup/v2/online-checkout/payment-links/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "payment_link": {
    "version": 1,
    "description": "Updated description"
  }
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Payment Link

```bash
maton api '/squareup/v2/online-checkout/payment-links/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Cards API

#### List Cards

```bash
maton api '/squareup/v2/cards'

maton api '/squareup/v2/cards?customer_id={customer_id}'
```

**Note:** `{customer_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Card

```bash
maton api '/squareup/v2/cards/{card_id}'
```

**Note:** `{card_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Card

```bash
maton api -X POST '/squareup/v2/cards' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "idempotency_key": "unique-key",
  "source_id": "cnon:card-nonce-ok",
  "card": {
    "customer_id": "{customer_id}"
  }
}
JSON
```

**Note:** `{customer_id}` is a placeholder. Replace it with a real value before sending the request.

#### Disable Card

```bash
maton api -X POST '/squareup/v2/cards/{card_id}/disable'
```

**Note:** `{card_id}` is a placeholder. Replace it with a real value before sending the request.

### Payouts API

#### List Payouts

```bash
maton api '/squareup/v2/payouts'

maton api '/squareup/v2/payouts?location_id={location_id}'
```

**Note:** `{location_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Payout

```bash
maton api '/squareup/v2/payouts/{payout_id}'
```

**Note:** `{payout_id}` is a placeholder. Replace it with a real value before sending the request.

#### List Payout Entries

```bash
maton api '/squareup/v2/payouts/{payout_id}/payout-entries'
```

**Note:** `{payout_id}` is a placeholder. Replace it with a real value before sending the request.

### Bank Accounts API

#### List Bank Accounts

```bash
maton api '/squareup/v2/bank-accounts'
```

#### Get Bank Account

```bash
maton api '/squareup/v2/bank-accounts/{bank_account_id}'
```

**Note:** `{bank_account_id}` is a placeholder. Replace it with a real value before sending the request.

### Terminal API

#### List Terminal Checkouts

```bash
maton api '/squareup/v2/terminals/checkouts'
```

#### Create Terminal Checkout

```bash
maton api -X POST '/squareup/v2/terminals/checkouts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "idempotency_key": "unique-key",
  "checkout": {
    "amount_money": {
      "amount": 1000,
      "currency": "USD"
    },
    "device_options": {
      "device_id": "{device_id}"
    }
  }
}
JSON
```

**Note:** `{device_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Terminal Checkout

```bash
maton api '/squareup/v2/terminals/checkouts/{checkout_id}'
```

**Note:** `{checkout_id}` is a placeholder. Replace it with a real value before sending the request.

#### Search Terminal Checkouts

```bash
maton api -X POST '/squareup/v2/terminals/checkouts/search' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": {
    "filter": {
      "status": "COMPLETED"
    }
  }
}
JSON
```

#### Cancel Terminal Checkout

```bash
maton api -X POST '/squareup/v2/terminals/checkouts/{checkout_id}/cancel'
```

**Note:** `{checkout_id}` is a placeholder. Replace it with a real value before sending the request.

### Notes

- All amounts are in smallest currency unit (cents for USD: 1000 = $10.00)
- Most write operations require an `idempotency_key`
- Cursor-based pagination: use `cursor` parameter with value from response
- Timestamps are ISO 8601 format
- Some endpoints require specific OAuth scopes

### Resources

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
- [Maton CLI Manual](https://cli.maton.ai/manual)
