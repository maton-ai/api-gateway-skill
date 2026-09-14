# Stripe

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

> **Elevated risk — this API moves real money and holds cardholder data.** Two distinct concerns:
>
> **1. Financial operations are irreversible or costly to reverse.** Creating charges, issuing refunds, canceling subscriptions, and deleting customers all have immediate financial effect on real people. Before any write: state the exact customer, amount, and currency, and get explicit user confirmation. Never infer an amount, retry a charge after an ambiguous failure (risk of double-charging — use idempotency keys), or cancel/refund based on vague intent. Confirm you are in the intended mode: check `livemode` and never assume a request is a harmless test.
>
> **2. Responses contain payment PII.** Customer records and events include email addresses, billing addresses, phone numbers, card `last4`/brand/expiry, bank account fragments, receipt URLs, and authorization codes. This is regulated data (PCI-DSS scope, and personal data under GDPR/CCPA).
> - Retrieve only the records and fields the task needs; do not bulk-export the customer list.
> - Do not print full customer records, card details, or receipt URLs into output beyond what the user asked for. Receipt URLs are publicly reachable — treat them as sensitive links.
> - **Never forward Stripe data to a third-party host** — not to a trigger destination, external webhook, spreadsheet service, or analytics endpoint — without explicit user approval for that specific transfer.
> - Never store card data, and never attempt to retrieve or reconstruct a full card number (Stripe does not expose it — do not try).

**App name:** `stripe`
**Upstream base URL:** `api.stripe.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.stripe.com/v1/balance`
- Gateway: `https://api.maton.ai/stripe/v1/balance`

### Events API

#### List Stripe Events

```bash
maton api '/stripe/v1/events?limit=10&type=customer.created'
```

### Balance API

#### Get Balance

```bash
maton stripe balance
```

Or with `maton api`:

```bash
maton api '/stripe/v1/balance'
```

**Response:**
```json
{
  "object": "balance",
  "available": [
    {
      "amount": 0,
      "currency": "usd",
      "source_types": {"card": 0}
    }
  ],
  "pending": [
    {
      "amount": 5000,
      "currency": "usd",
      "source_types": {"card": 5000}
    }
  ]
}
```

#### List Balance Transactions

```bash
maton stripe balance-transaction list -L 10
```

Or with `maton api`:

```bash
maton api '/stripe/v1/balance_transactions?limit=10'
```

### Customers API

#### List Customers

```bash
maton stripe customer list -L 10
```

Or with `maton api`:

```bash
maton api '/stripe/v1/customers?limit=10'
```

**Query parameters:**

| Parameter | Description |
|-----------|-------------|
| `limit` | Number of results (1-100, default: 10) |
| `starting_after` | Cursor for pagination |
| `ending_before` | Cursor for reverse pagination |
| `email` | Filter by email |
| `created` | Filter by creation date |

**Response:**
```json
{
  "object": "list",
  "data": [
    {
      "id": "cus_TxKtN8Irvzx9BQ",
      "object": "customer",
      "email": "customer@example.com",
      "name": null,
      "balance": 0,
      "currency": "usd",
      "created": 1770765579,
      "metadata": {}
    }
  ],
  "has_more": true,
  "url": "/v1/customers"
}
```

#### Get Customer

```bash
maton stripe customer get {customer_id}
```

Or with `maton api`:

```bash
maton api '/stripe/v1/customers/{customer_id}'
```

**Note:** `{customer_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Customer

```bash
maton stripe customer create --email customer@example.com --name 'John Doe' --metadata user_id=123
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/customers' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
email=customer@example.com&name=John%20Doe&metadata[user_id]=123
BODY
```

#### Update Customer

```bash
maton stripe customer update {customer_id} --name 'Jane Doe' --email jane@example.com
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/customers/{customer_id}' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
name=Jane%20Doe&email=jane@example.com
BODY
```

**Note:** `{customer_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Customer

```bash
maton stripe customer delete {customer_id}
```

Or with `maton api`:

```bash
maton api '/stripe/v1/customers/{customer_id}' -X DELETE
```

**Note:** `{customer_id}` is a placeholder. Replace it with a real value before sending the request.

### Products API

#### List Products

```bash
maton stripe product list -L 10
```

Or with `maton api`:

```bash
maton api '/stripe/v1/products?limit=10'
```

**Query parameters:**

| Parameter | Description |
|-----------|-------------|
| `active` | Filter by active status |
| `type` | Filter by type: `good` or `service` |

**Response:**
```json
{
  "object": "list",
  "data": [
    {
      "id": "prod_TthCLBwTIXuzEw",
      "object": "product",
      "active": true,
      "name": "Premium Plan",
      "description": "Premium subscription",
      "type": "service",
      "created": 1769926024,
      "metadata": {}
    }
  ],
  "has_more": true
}
```

#### Get Product

```bash
maton stripe product get {product_id}
```

Or with `maton api`:

```bash
maton api '/stripe/v1/products/{product_id}'
```

**Note:** `{product_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Product

```bash
maton stripe product create --name 'Premium Plan' --description 'Premium subscription'
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/products' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
name=Premium%20Plan&description=Premium%20subscription
BODY
```

#### Update Product

```bash
maton stripe product update {product_id} --name 'Updated Plan' --active true
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/products/{product_id}' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
name=Updated%20Plan&active=true
BODY
```

**Note:** `{product_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Product

```bash
maton stripe product delete {product_id}
```

Or with `maton api`:

```bash
maton api '/stripe/v1/products/{product_id}' -X DELETE
```

**Note:** `{product_id}` is a placeholder. Replace it with a real value before sending the request.

### Prices API

#### List Prices

```bash
maton stripe price list -L 10
```

Or with `maton api`:

```bash
maton api '/stripe/v1/prices?limit=10'
```

**Query parameters:**

| Parameter | Description |
|-----------|-------------|
| `active` | Filter by active status |
| `product` | Filter by product ID |
| `type` | Filter: `one_time` or `recurring` |
| `currency` | Filter by currency |

**Response:**
```json
{
  "object": "list",
  "data": [
    {
      "id": "price_1SvtoVDfFKJhF88gKJv2eSmO",
      "object": "price",
      "active": true,
      "currency": "usd",
      "product": "prod_TthCLBwTIXuzEw",
      "unit_amount": 1999,
      "recurring": {
        "interval": "month",
        "interval_count": 1
      },
      "type": "recurring"
    }
  ],
  "has_more": true
}
```

#### Get Price

```bash
maton stripe price get {price_id}
```

Or with `maton api`:

```bash
maton api '/stripe/v1/prices/{price_id}'
```

**Note:** `{price_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Price

```bash
maton stripe price create --product prod_XXX --unit-amount 1999 --currency usd --recurring-interval month
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/prices' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
product=prod_XXX&unit_amount=1999&currency=usd&recurring[interval]=month
BODY
```

#### Update Price

```bash
maton stripe price update {price_id} --active false
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/prices/{price_id}' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
active=false
BODY
```

**Note:** `{price_id}` is a placeholder. Replace it with a real value before sending the request.

### Subscriptions API

#### List Subscriptions

```bash
maton stripe subscription list -L 10
```

Or with `maton api`:

```bash
maton api '/stripe/v1/subscriptions?limit=10'
```

**Query parameters:**

| Parameter | Description |
|-----------|-------------|
| `customer` | Filter by customer ID |
| `price` | Filter by price ID |
| `status` | Filter: `active`, `canceled`, `past_due`, etc. |

**Response:**
```json
{
  "object": "list",
  "data": [
    {
      "id": "sub_1SzQDXDfFKJhF88gf72x6tDh",
      "object": "subscription",
      "customer": "cus_TxKtN8Irvzx9BQ",
      "status": "active",
      "current_period_start": 1770765579,
      "current_period_end": 1773184779,
      "items": {
        "data": [
          {
            "id": "si_TxKtFWxlUW50cR",
            "price": {
              "id": "price_1RGbXsDfFKJhF88gMIShAq9m",
              "unit_amount": 0
            },
            "quantity": 1
          }
        ]
      }
    }
  ],
  "has_more": true
}
```

#### Get Subscription

```bash
maton stripe subscription get {subscription_id}
```

Or with `maton api`:

```bash
maton api '/stripe/v1/subscriptions/{subscription_id}'
```

**Note:** `{subscription_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Subscription

```bash
maton stripe subscription create --customer cus_XXX --price price_XXX
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/subscriptions' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
customer=cus_XXX&items[0][price]=price_XXX
BODY
```

#### Update Subscription

```bash
maton stripe subscription update {subscription_id} --items 'id=si_XXX,price=price_YYY'
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/subscriptions/{subscription_id}' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
items[0][id]=si_XXX&items[0][price]=price_YYY
BODY
```

**Note:** `{subscription_id}` is a placeholder. Replace it with a real value before sending the request.

#### Cancel Subscription

```bash
maton stripe subscription cancel {subscription_id}
```

Or with `maton api`:

```bash
maton api '/stripe/v1/subscriptions/{subscription_id}' -X DELETE
```

**Note:** `{subscription_id}` is a placeholder. Replace it with a real value before sending the request.

### Invoices API

#### List Invoices

```bash
maton stripe invoice list -L 10
```

Or with `maton api`:

```bash
maton api '/stripe/v1/invoices?limit=10'
```

**Query parameters:**

| Parameter | Description |
|-----------|-------------|
| `customer` | Filter by customer ID |
| `subscription` | Filter by subscription ID |
| `status` | Filter: `draft`, `open`, `paid`, `void`, `uncollectible` |

**Response:**
```json
{
  "object": "list",
  "data": [
    {
      "id": "in_1SzQDXDfFKJhF88g3nh4u2GS",
      "object": "invoice",
      "customer": "cus_TxKtN8Irvzx9BQ",
      "amount_due": 0,
      "amount_paid": 0,
      "currency": "usd",
      "status": "paid",
      "subscription": "sub_1SzQDXDfFKJhF88gf72x6tDh",
      "hosted_invoice_url": "https://invoice.stripe.com/...",
      "invoice_pdf": "https://pay.stripe.com/invoice/.../pdf"
    }
  ],
  "has_more": true
}
```

#### Get Invoice

```bash
maton stripe invoice get {invoice_id}
```

Or with `maton api`:

```bash
maton api '/stripe/v1/invoices/{invoice_id}'
```

**Note:** `{invoice_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Invoice

```bash
maton stripe invoice create --customer cus_XXX
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/invoices' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
customer=cus_XXX
BODY
```

#### Finalize Invoice

```bash
maton stripe invoice finalize {invoice_id}
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/invoices/{invoice_id}/finalize'
```

**Note:** `{invoice_id}` is a placeholder. Replace it with a real value before sending the request.

#### Pay Invoice

```bash
maton stripe invoice pay {invoice_id}
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/invoices/{invoice_id}/pay'
```

**Note:** `{invoice_id}` is a placeholder. Replace it with a real value before sending the request.

#### Void Invoice

```bash
maton stripe invoice void {invoice_id}
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/invoices/{invoice_id}/void'
```

**Note:** `{invoice_id}` is a placeholder. Replace it with a real value before sending the request.

### Charges API

#### List Charges

```bash
maton stripe charge list -L 10
```

Or with `maton api`:

```bash
maton api '/stripe/v1/charges?limit=10'
```

**Query parameters:**

| Parameter | Description |
|-----------|-------------|
| `customer` | Filter by customer ID |
| `payment_intent` | Filter by payment intent |

**Response:**
```json
{
  "object": "list",
  "data": [
    {
      "id": "ch_3SyXBvDfFKJhF88g1MHtT45f",
      "object": "charge",
      "amount": 5000,
      "currency": "usd",
      "customer": "cus_TuZ7GIjeZQOQ2m",
      "paid": true,
      "status": "succeeded",
      "payment_method_details": {
        "card": {
          "brand": "mastercard",
          "last4": "0833"
        },
        "type": "card"
      }
    }
  ],
  "has_more": true
}
```

#### Get Charge

```bash
maton stripe charge get {charge_id}
```

Or with `maton api`:

```bash
maton api '/stripe/v1/charges/{charge_id}'
```

**Note:** `{charge_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Charge

```bash
maton stripe charge create --amount 2000 --currency usd --source tok_XXX
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/charges' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
amount=2000&currency=usd&source=tok_XXX
BODY
```

### Payment Intents API

#### List Payment Intents

```bash
maton stripe payment list -L 10
```

Or with `maton api`:

```bash
maton api '/stripe/v1/payment_intents?limit=10'
```

**Response:**
```json
{
  "object": "list",
  "data": [
    {
      "id": "pi_3SyXBvDfFKJhF88g17PeHdpE",
      "object": "payment_intent",
      "amount": 5000,
      "currency": "usd",
      "customer": "cus_TuZ7GIjeZQOQ2m",
      "status": "succeeded",
      "payment_method": "pm_1SyXBpDfFKJhF88gmP3IjC8C"
    }
  ],
  "has_more": true
}
```

#### Get Payment Intent

```bash
maton stripe payment get {payment_intent_id}
```

Or with `maton api`:

```bash
maton api '/stripe/v1/payment_intents/{payment_intent_id}'
```

**Note:** `{payment_intent_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Payment Intent

```bash
maton stripe payment create --amount 2000 --currency usd --customer cus_XXX --payment-method-types card
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/payment_intents' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
amount=2000&currency=usd&customer=cus_XXX&payment_method_types[]=card
BODY
```

#### Confirm Payment Intent

```bash
maton stripe payment confirm {payment_intent_id}
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/payment_intents/{payment_intent_id}/confirm'
```

**Note:** `{payment_intent_id}` is a placeholder. Replace it with a real value before sending the request.

#### Cancel Payment Intent

```bash
maton stripe payment cancel {payment_intent_id}
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/payment_intents/{payment_intent_id}/cancel'
```

**Note:** `{payment_intent_id}` is a placeholder. Replace it with a real value before sending the request.

### Payment Methods API

#### List Payment Methods

```bash
maton stripe payment-method list --customer cus_XXX --type card
```

Or with `maton api`:

```bash
maton api '/stripe/v1/payment_methods?customer=cus_XXX&type=card'
```

#### Get Payment Method

```bash
maton stripe payment-method get {payment_method_id}
```

Or with `maton api`:

```bash
maton api '/stripe/v1/payment_methods/{payment_method_id}'
```

**Note:** `{payment_method_id}` is a placeholder. Replace it with a real value before sending the request.

#### Attach Payment Method

```bash
maton stripe payment-method attach {payment_method_id} --customer cus_XXX
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/payment_methods/{payment_method_id}/attach' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
customer=cus_XXX
BODY
```

**Note:** `{payment_method_id}` is a placeholder. Replace it with a real value before sending the request.

#### Detach Payment Method

```bash
maton stripe payment-method detach {payment_method_id}
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/payment_methods/{payment_method_id}/detach'
```

**Note:** `{payment_method_id}` is a placeholder. Replace it with a real value before sending the request.

### Coupons API

#### List Coupons

```bash
maton stripe coupon list -L 10
```

Or with `maton api`:

```bash
maton api '/stripe/v1/coupons?limit=10'
```

#### Get Coupon

```bash
maton stripe coupon get {coupon_id}
```

Or with `maton api`:

```bash
maton api '/stripe/v1/coupons/{coupon_id}'
```

**Note:** `{coupon_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Coupon

```bash
maton stripe coupon create --percent-off 25 --duration once
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/coupons' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
percent_off=25&duration=once
BODY
```

#### Delete Coupon

```bash
maton stripe coupon delete {coupon_id}
```

Or with `maton api`:

```bash
maton api '/stripe/v1/coupons/{coupon_id}' -X DELETE
```

**Note:** `{coupon_id}` is a placeholder. Replace it with a real value before sending the request.

### Refunds API

#### List Refunds

```bash
maton stripe refund list -L 10
```

Or with `maton api`:

```bash
maton api '/stripe/v1/refunds?limit=10'
```

#### Get Refund

```bash
maton stripe refund get {refund_id}
```

Or with `maton api`:

```bash
maton api '/stripe/v1/refunds/{refund_id}'
```

**Note:** `{refund_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Refund

```bash
maton stripe refund create --charge ch_XXX --amount 1000
```

Or with `maton api`:

```bash
maton api -X POST '/stripe/v1/refunds' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
charge=ch_XXX&amount=1000
BODY
```

### Pagination

Stripe uses cursor-based pagination with `starting_after` and `ending_before`:

```bash
maton stripe customer list -L 10 --starting-after cus_XXX
```

Or with `maton api`:

```bash
maton api '/stripe/v1/customers?limit=10&starting_after=cus_XXX'
```

**Response:**
```json
{
  "object": "list",
  "data": [...],
  "has_more": true,
  "url": "/v1/customers"
}
```

Use the last item's ID as `starting_after` for the next page.

### Notes

- Stripe API uses `application/x-www-form-urlencoded` for POST requests (not JSON)
- Amounts are in the smallest currency unit (e.g., cents for USD)
- IDs start with prefixes: `cus_` (customers), `prod_` (products), `price_` (prices), `sub_` (subscriptions), `in_` (invoices), `ch_` (charges), `pi_` (payment intents)
- Timestamps are Unix timestamps

### Resources

- [Stripe API Overview](https://docs.stripe.com/api)
- [List Customers](https://docs.stripe.com/api/customers/list.md)
- [Get Customer](https://docs.stripe.com/api/customers/retrieve.md)
- [Create Customer](https://docs.stripe.com/api/customers/create.md)
- [Update Customer](https://docs.stripe.com/api/customers/update.md)
- [Delete Customer](https://docs.stripe.com/api/customers/delete.md)
- [Search Customers](https://docs.stripe.com/api/customers/search.md)
- [List Products](https://docs.stripe.com/api/products/list.md)
- [Get Product](https://docs.stripe.com/api/products/retrieve.md)
- [Create Product](https://docs.stripe.com/api/products/create.md)
- [Update Product](https://docs.stripe.com/api/products/update.md)
- [Delete Product](https://docs.stripe.com/api/products/delete.md)
- [Search Products](https://docs.stripe.com/api/products/search.md)
- [List Prices](https://docs.stripe.com/api/prices/list.md)
- [Get Price](https://docs.stripe.com/api/prices/retrieve.md)
- [Create Price](https://docs.stripe.com/api/prices/create.md)
- [Update Price](https://docs.stripe.com/api/prices/update.md)
- [Search Prices](https://docs.stripe.com/api/prices/search.md)
- [List Subscriptions](https://docs.stripe.com/api/subscriptions/list.md)
- [Get Subscription](https://docs.stripe.com/api/subscriptions/retrieve.md)
- [Create Subscription](https://docs.stripe.com/api/subscriptions/create.md)
- [Update Subscription](https://docs.stripe.com/api/subscriptions/update.md)
- [Cancel Subscription](https://docs.stripe.com/api/subscriptions/cancel.md)
- [Resume Subscription](https://docs.stripe.com/api/subscriptions/resume.md)
- [Search Subscriptions](https://docs.stripe.com/api/subscriptions/search.md)
- [List Invoices](https://docs.stripe.com/api/invoices/list.md)
- [Get Invoice](https://docs.stripe.com/api/invoices/retrieve.md)
- [Create Invoice](https://docs.stripe.com/api/invoices/create.md)
- [Update Invoice](https://docs.stripe.com/api/invoices/update.md)
- [Delete Invoice](https://docs.stripe.com/api/invoices/delete.md)
- [Finalize Invoice](https://docs.stripe.com/api/invoices/finalize.md)
- [Pay Invoice](https://docs.stripe.com/api/invoices/pay.md)
- [Send Invoice](https://docs.stripe.com/api/invoices/send.md)
- [Void Invoice](https://docs.stripe.com/api/invoices/void.md)
- [Search Invoices](https://docs.stripe.com/api/invoices/search.md)
- [List Charges](https://docs.stripe.com/api/charges/list.md)
- [Get Charge](https://docs.stripe.com/api/charges/retrieve.md)
- [Create Charge](https://docs.stripe.com/api/charges/create.md)
- [Update Charge](https://docs.stripe.com/api/charges/update.md)
- [Capture Charge](https://docs.stripe.com/api/charges/capture.md)
- [Search Charges](https://docs.stripe.com/api/charges/search.md)
- [List Payment Intents](https://docs.stripe.com/api/payment_intents/list.md)
- [Get Payment Intent](https://docs.stripe.com/api/payment_intents/retrieve.md)
- [Create Payment Intent](https://docs.stripe.com/api/payment_intents/create.md)
- [Update Payment Intent](https://docs.stripe.com/api/payment_intents/update.md)
- [Confirm Payment Intent](https://docs.stripe.com/api/payment_intents/confirm.md)
- [Capture Payment Intent](https://docs.stripe.com/api/payment_intents/capture.md)
- [Cancel Payment Intent](https://docs.stripe.com/api/payment_intents/cancel.md)
- [Search Payment Intents](https://docs.stripe.com/api/payment_intents/search.md)
- [Get Balance](https://docs.stripe.com/api/balance/balance_retrieve.md)
- [List Balance Transactions](https://docs.stripe.com/api/balance_transactions/list.md)
- [Get Balance Transaction](https://docs.stripe.com/api/balance_transactions/retrieve.md)
- [List Events](https://docs.stripe.com/api/events/list.md)
- [Get Event](https://docs.stripe.com/api/events/retrieve.md)
- [Pagination](https://docs.stripe.com/api/pagination.md)
- [Expanding Responses](https://docs.stripe.com/api/expanding_objects.md)
- [LLM Reference](https://docs.stripe.com/llms.txt)
- [Maton CLI Manual](https://cli.maton.ai/manual)
