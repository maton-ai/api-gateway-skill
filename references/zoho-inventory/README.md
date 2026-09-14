# Zoho Inventory

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `zoho-inventory`
**Upstream base URL:** `www.zohoapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://www.zohoapis.com/inventory/v1/items`
- Gateway: `https://api.maton.ai/zoho-inventory/inventory/v1/items`

| Module | Endpoint | Description |
|--------|----------|-------------|
| Items | `/items` | Products and services |
| Item Groups | `/itemgroups` | Grouped product variants |
| Contacts | `/contacts` | Customers and vendors |
| Sales Orders | `/salesorders` | Sales orders |
| Invoices | `/invoices` | Sales invoices |
| Purchase Orders | `/purchaseorders` | Purchase orders |
| Bills | `/bills` | Vendor bills |
| Shipment Orders | `/shipmentorders` | Shipment tracking |

### Items API

#### List Items

```bash
maton api '/zoho-inventory/inventory/v1/items'
```

**Example:**

```bash
maton api '/zoho-inventory/inventory/v1/items'
```

**Response:**
```json
{
  "code": 0,
  "message": "success",
  "items": [
    {
      "item_id": "1234567890000",
      "name": "Widget",
      "status": "active",
      "sku": "WDG-001",
      "rate": 25.00,
      "purchase_rate": 10.00,
      "is_taxable": true
    }
  ],
  "page_context": {
    "page": 1,
    "per_page": 200,
    "has_more_page": false
  }
}
```

#### Get Item

```bash
maton api '/zoho-inventory/inventory/v1/items/{item_id}'
```

**Note:** `{item_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Item

```bash
maton api -X POST '/zoho-inventory/inventory/v1/items' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Widget",
  "rate": 25.00,
  "purchase_rate": 10.00,
  "sku": "WDG-001",
  "item_type": "inventory",
  "product_type": "goods",
  "unit": "pcs",
  "is_taxable": true
}
JSON
```

**Request body:**
- `name` (required) - Item name
- `rate` (optional) - Sales price
- `purchase_rate` (optional) - Purchase cost
- `sku` (optional) - Stock keeping unit (unique)
- `item_type` (optional) - `inventory`, `sales`, `purchases`, or `sales_and_purchases`
- `product_type` (optional) - `goods` or `service`
- `unit` (optional) - Unit of measurement
- `is_taxable` (optional) - Tax applicability
- `tax_id` (optional) - Tax identifier
- `description` (optional) - Item description
- `reorder_level` (optional) - Reorder point
- `vendor_id` (optional) - Preferred vendor

**Example:**

```bash
maton api -X POST '/zoho-inventory/inventory/v1/items' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Widget",
  "rate": 25.0,
  "purchase_rate": 10.0,
  "sku": "WDG-001",
  "item_type": "inventory",
  "product_type": "goods",
  "unit": "pcs"
}
JSON
```

**Response:**
```json
{
  "code": 0,
  "message": "The item has been added.",
  "item": {
    "item_id": "1234567890000",
    "name": "Widget",
    "status": "active",
    "rate": 25.00,
    "purchase_rate": 10.00,
    "sku": "WDG-001"
  }
}
```

#### Update Item

```bash
maton api -X PUT '/zoho-inventory/inventory/v1/items/{item_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Widget",
  "rate": 30.00
}
JSON
```

**Note:** `{item_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Item

```bash
maton api '/zoho-inventory/inventory/v1/items/{item_id}' -X DELETE
```

**Note:** `{item_id}` is a placeholder. Replace it with a real value before sending the request.

#### Item Status Actions

```bash
# Mark as active
maton api -X POST '/zoho-inventory/inventory/v1/items/{item_id}/active'

# Mark as inactive
maton api -X POST '/zoho-inventory/inventory/v1/items/{item_id}/inactive'
```

**Note:** `{item_id}` is a placeholder. Replace it with a real value before sending the request.

### Contacts API

#### List Contacts

```bash
maton api '/zoho-inventory/inventory/v1/contacts'
```

**Query parameters:**
- `filter_by` - `Status.All`, `Status.Active`, `Status.Inactive`, `Status.Duplicate`, `Status.Crm`
- `search_text` - Search across contact fields
- `sort_column` - `contact_name`, `first_name`, `last_name`, `email`, `created_time`, `last_modified_time`
- `contact_name`, `company_name`, `email`, `phone` - Field-specific filters

**Example:**

```bash
maton api '/zoho-inventory/inventory/v1/contacts'
```

#### Get Contact

```bash
maton api '/zoho-inventory/inventory/v1/contacts/{contact_id}'
```

**Note:** `{contact_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Contact

```bash
maton api -X POST '/zoho-inventory/inventory/v1/contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contact_name": "Acme Corporation",
  "contact_type": "customer",
  "company_name": "Acme Corp",
  "email": "billing@acme.com",
  "phone": "+1-555-1234"
}
JSON
```

**Request body:**
- `contact_name` (required) - Display name
- `contact_type` (optional) - `customer` or `vendor`
- `company_name` (optional) - Legal entity name
- `email` (optional) - Email address
- `phone` (optional) - Phone number
- `billing_address` (optional) - Address object
- `shipping_address` (optional) - Address object
- `payment_terms` (optional) - Days for payment
- `currency_id` (optional) - Currency identifier
- `website` (optional) - Website URL

#### Update Contact

```bash
maton api -X PUT '/zoho-inventory/inventory/v1/contacts/{contact_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contact_name": "Acme Corporation",
  "email": "billing@acme.com"
}
JSON
```

**Note:** `{contact_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Contact

```bash
maton api '/zoho-inventory/inventory/v1/contacts/{contact_id}' -X DELETE
```

**Note:** `{contact_id}` is a placeholder. Replace it with a real value before sending the request.

#### Contact Status Actions

```bash
# Mark as active
maton api -X POST '/zoho-inventory/inventory/v1/contacts/{contact_id}/active'

# Mark as inactive
maton api -X POST '/zoho-inventory/inventory/v1/contacts/{contact_id}/inactive'
```

**Note:** `{contact_id}` is a placeholder. Replace it with a real value before sending the request.

### Sales Orders API

#### List Sales Orders

```bash
maton api '/zoho-inventory/inventory/v1/salesorders'
```

**Example:**

```bash
maton api '/zoho-inventory/inventory/v1/salesorders'
```

#### Get Sales Order

```bash
maton api '/zoho-inventory/inventory/v1/salesorders/{salesorder_id}'
```

**Note:** `{salesorder_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Sales Order

```bash
maton api -X POST '/zoho-inventory/inventory/v1/salesorders' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "customer_id": "1234567890000",
  "date": "2026-02-06",
  "line_items": [
    {
      "item_id": "1234567890001",
      "quantity": 5,
      "rate": 25.00
    }
  ]
}
JSON
```

**Request body:**
- `customer_id` (required) - Customer identifier
- `line_items` (required) - Array of items with `item_id`, `quantity`, `rate`
- `salesorder_number` (optional) - Auto-generated if not specified (do not specify if auto-generation is enabled)
- `date` (optional) - Order date (yyyy-mm-dd)
- `shipment_date` (optional) - Expected shipment date
- `reference_number` (optional) - External reference
- `notes` (optional) - Internal notes
- `terms` (optional) - Terms and conditions
- `discount` (optional) - Discount percentage or amount
- `shipping_charge` (optional) - Shipping cost
- `adjustment` (optional) - Price adjustment

#### Update Sales Order

```bash
maton api -X PUT '/zoho-inventory/inventory/v1/salesorders/{salesorder_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "customer_id": "1234567890000",
  "line_items": [
    {"item_id": "1234567890001", "quantity": 5, "rate": 25.00}
  ]
}
JSON
```

**Note:** `{salesorder_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Sales Order

```bash
maton api '/zoho-inventory/inventory/v1/salesorders/{salesorder_id}' -X DELETE
```

**Note:** `{salesorder_id}` is a placeholder. Replace it with a real value before sending the request.

#### Sales Order Status Actions

```bash
# Mark as confirmed
maton api -X POST '/zoho-inventory/inventory/v1/salesorders/{salesorder_id}/status/confirmed'

# Mark as void
maton api -X POST '/zoho-inventory/inventory/v1/salesorders/{salesorder_id}/status/void'
```

**Note:** `{salesorder_id}` is a placeholder. Replace it with a real value before sending the request.

### Invoices API

#### List Invoices

```bash
maton api '/zoho-inventory/inventory/v1/invoices'
```

**Example:**

```bash
maton api '/zoho-inventory/inventory/v1/invoices'
```

#### Get Invoice

```bash
maton api '/zoho-inventory/inventory/v1/invoices/{invoice_id}'
```

**Note:** `{invoice_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Invoice

```bash
maton api -X POST '/zoho-inventory/inventory/v1/invoices' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "customer_id": "1234567890000",
  "line_items": [
    {
      "item_id": "1234567890001",
      "quantity": 5,
      "rate": 25.00
    }
  ]
}
JSON
```

**Request body:**
- `customer_id` (required) - Customer identifier
- `line_items` (required) - Array of items
- `invoice_number` (optional) - Auto-generated if not specified
- `date` (optional) - Invoice date (yyyy-mm-dd)
- `due_date` (optional) - Payment due date
- `payment_terms` (optional) - Days until due
- `discount` (optional) - Discount percentage or amount
- `shipping_charge` (optional) - Shipping cost
- `notes` (optional) - Internal notes
- `terms` (optional) - Terms and conditions

#### Update Invoice

```bash
maton api -X PUT '/zoho-inventory/inventory/v1/invoices/{invoice_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "customer_id": "1234567890000",
  "line_items": [
    {"item_id": "1234567890001", "quantity": 5, "rate": 25.00}
  ]
}
JSON
```

**Note:** `{invoice_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Invoice

```bash
maton api '/zoho-inventory/inventory/v1/invoices/{invoice_id}' -X DELETE
```

**Note:** `{invoice_id}` is a placeholder. Replace it with a real value before sending the request.

#### Invoice Status Actions

```bash
# Mark as sent
maton api -X POST '/zoho-inventory/inventory/v1/invoices/{invoice_id}/status/sent'

# Mark as draft
maton api -X POST '/zoho-inventory/inventory/v1/invoices/{invoice_id}/status/draft'

# Void invoice
maton api -X POST '/zoho-inventory/inventory/v1/invoices/{invoice_id}/status/void'
```

**Note:** `{invoice_id}` is a placeholder. Replace it with a real value before sending the request.

#### Invoice Email

```bash
# Email invoice to customer
maton api -X POST '/zoho-inventory/inventory/v1/invoices/{invoice_id}/email' -H 'Content-Type: application/json' --input - <<'JSON'
{"to_mail_ids": ["customer@example.com"]}
JSON

# Get email content template
maton api '/zoho-inventory/inventory/v1/invoices/{invoice_id}/email'
```

**Note:** `{invoice_id}` is a placeholder. Replace it with a real value before sending the request.

#### Invoice Payments

```bash
# List payments applied
maton api '/zoho-inventory/inventory/v1/invoices/{invoice_id}/payments'

# Delete a payment
maton api '/zoho-inventory/inventory/v1/invoices/{invoice_id}/payments/{invoice_payment_id}' -X DELETE
```

**Note:** `{invoice_id}` and `{invoice_payment_id}` are placeholders. Replace each of them with real values before sending the request.

#### Invoice Credits

```bash
# List credits applied
maton api '/zoho-inventory/inventory/v1/invoices/{invoice_id}/creditsapplied'

# Apply credits
maton api -X POST '/zoho-inventory/inventory/v1/invoices/{invoice_id}/credits' -H 'Content-Type: application/json' --input - <<'JSON'
{"apply_creditnotes": [{"creditnote_id": "{creditnote_id}", "amount_applied": 100.00}]}
JSON

# Delete applied credit
maton api '/zoho-inventory/inventory/v1/invoices/{invoice_id}/creditsapplied/{creditnotes_invoice_id}' -X DELETE
```

**Note:** `{invoice_id}`, `{creditnote_id}` and `{creditnotes_invoice_id}` are placeholders. Replace each of them with real values before sending the request.

#### Invoice Comments

```bash
# List comments
maton api '/zoho-inventory/inventory/v1/invoices/{invoice_id}/comments'

# Add comment
maton api -X POST '/zoho-inventory/inventory/v1/invoices/{invoice_id}/comments' -H 'Content-Type: application/json' --input - <<'JSON'
{"description": "Comment text"}
JSON

# Update comment
maton api -X PUT '/zoho-inventory/inventory/v1/invoices/{invoice_id}/comments/{comment_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{"description": "Updated comment text"}
JSON

# Delete comment
maton api '/zoho-inventory/inventory/v1/invoices/{invoice_id}/comments/{comment_id}' -X DELETE
```

**Note:** `{invoice_id}` and `{comment_id}` are placeholders. Replace each of them with real values before sending the request.

### Purchase Orders API

#### List Purchase Orders

```bash
maton api '/zoho-inventory/inventory/v1/purchaseorders'
```

**Example:**

```bash
maton api '/zoho-inventory/inventory/v1/purchaseorders'
```

#### Get Purchase Order

```bash
maton api '/zoho-inventory/inventory/v1/purchaseorders/{purchaseorder_id}'
```

**Note:** `{purchaseorder_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Purchase Order

```bash
maton api -X POST '/zoho-inventory/inventory/v1/purchaseorders' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "vendor_id": "1234567890000",
  "line_items": [
    {
      "item_id": "1234567890001",
      "quantity": 100,
      "rate": 10.00
    }
  ]
}
JSON
```

**Request body:**
- `vendor_id` (required) - Vendor identifier
- `line_items` (required) - Array of items
- `purchaseorder_number` (optional) - Auto-generated if not specified (do not specify if auto-generation is enabled)
- `date` (optional) - Order date (yyyy-mm-dd)
- `delivery_date` (optional) - Expected delivery date
- `reference_number` (optional) - External reference
- `ship_via` (optional) - Shipping method
- `notes` (optional) - Internal notes
- `terms` (optional) - Terms and conditions

#### Update Purchase Order

```bash
maton api -X PUT '/zoho-inventory/inventory/v1/purchaseorders/{purchaseorder_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "vendor_id": "1234567890002",
  "line_items": [
    {"item_id": "1234567890001", "quantity": 10, "rate": 15.00}
  ]
}
JSON
```

**Note:** `{purchaseorder_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Purchase Order

```bash
maton api '/zoho-inventory/inventory/v1/purchaseorders/{purchaseorder_id}' -X DELETE
```

**Note:** `{purchaseorder_id}` is a placeholder. Replace it with a real value before sending the request.

#### Purchase Order Status Actions

```bash
# Mark as issued
maton api -X POST '/zoho-inventory/inventory/v1/purchaseorders/{purchaseorder_id}/status/issued'

# Mark as cancelled
maton api -X POST '/zoho-inventory/inventory/v1/purchaseorders/{purchaseorder_id}/status/cancelled'
```

**Note:** `{purchaseorder_id}` is a placeholder. Replace it with a real value before sending the request.

### Bills API

#### List Bills

```bash
maton api '/zoho-inventory/inventory/v1/bills'
```

**Example:**

```bash
maton api '/zoho-inventory/inventory/v1/bills'
```

#### Get Bill

```bash
maton api '/zoho-inventory/inventory/v1/bills/{bill_id}'
```

**Note:** `{bill_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Bill

```bash
maton api -X POST '/zoho-inventory/inventory/v1/bills' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "vendor_id": "1234567890000",
  "bill_number": "BILL-001",
  "date": "2026-02-06",
  "due_date": "2026-03-06",
  "line_items": [
    {
      "item_id": "1234567890001",
      "quantity": 100,
      "rate": 10.00
    }
  ]
}
JSON
```

**Request body:**
- `vendor_id` (required) - Vendor identifier
- `bill_number` (required) - Unique bill number (required, not auto-generated)
- `date` (required) - Bill date (yyyy-mm-dd)
- `due_date` (required) - Payment due date
- `line_items` (required) - Array of items
- `reference_number` (optional) - External reference
- `notes` (optional) - Internal notes
- `terms` (optional) - Terms and conditions
- `currency_id` (optional) - Currency identifier
- `exchange_rate` (optional) - Exchange rate for foreign currency

#### Update Bill

```bash
maton api -X PUT '/zoho-inventory/inventory/v1/bills/{bill_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "vendor_id": "1234567890002",
  "bill_number": "BILL-001",
  "date": "2026-09-09",
  "line_items": [
    {"item_id": "1234567890001", "quantity": 10, "rate": 15.00}
  ]
}
JSON
```

**Note:** `{bill_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Bill

```bash
maton api '/zoho-inventory/inventory/v1/bills/{bill_id}' -X DELETE
```

**Note:** `{bill_id}` is a placeholder. Replace it with a real value before sending the request.

#### Bill Status Actions

```bash
# Mark as open
maton api -X POST '/zoho-inventory/inventory/v1/bills/{bill_id}/status/open'

# Mark as void
maton api -X POST '/zoho-inventory/inventory/v1/bills/{bill_id}/status/void'
```

**Note:** `{bill_id}` is a placeholder. Replace it with a real value before sending the request.

### Shipment Orders API

#### Create Shipment Order

```bash
maton api -X POST '/zoho-inventory/inventory/v1/shipmentorders' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "shipment_number": "SHP-001",
  "date": "2026-02-06",
  "delivery_method": "FedEx",
  "tracking_number": "1234567890"
}
JSON
```

**Request body:**
- `shipment_number` (required) - Unique shipment number
- `date` (required) - Shipment date
- `delivery_method` (required) - Carrier/delivery method
- `tracking_number` (optional) - Carrier tracking number
- `shipping_charge` (optional) - Shipping cost
- `notes` (optional) - Internal notes
- `reference_number` (optional) - External reference

#### Get Shipment Order

```bash
maton api '/zoho-inventory/inventory/v1/shipmentorders/{shipmentorder_id}'
```

**Note:** `{shipmentorder_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Shipment Order

```bash
maton api -X PUT '/zoho-inventory/inventory/v1/shipmentorders/{shipmentorder_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "shipment_number": "SHP-001",
  "date": "2026-09-09",
  "delivery_method": "FedEx",
  "tracking_number": "123456789012"
}
JSON
```

**Note:** `{shipmentorder_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Shipment Order

```bash
maton api '/zoho-inventory/inventory/v1/shipmentorders/{shipmentorder_id}' -X DELETE
```

**Note:** `{shipmentorder_id}` is a placeholder. Replace it with a real value before sending the request.

#### Mark as Delivered

```bash
maton api -X POST '/zoho-inventory/inventory/v1/shipmentorders/{shipmentorder_id}/status/delivered'
```

**Note:** `{shipmentorder_id}` is a placeholder. Replace it with a real value before sending the request.

### Item Groups API

#### List Item Groups

```bash
maton api '/zoho-inventory/inventory/v1/itemgroups'
```

#### Get Item Group

```bash
maton api '/zoho-inventory/inventory/v1/itemgroups/{itemgroup_id}'
```

**Note:** `{itemgroup_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Item Group

```bash
maton api -X POST '/zoho-inventory/inventory/v1/itemgroups' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "group_name": "T-Shirts",
  "unit": "pcs",
  "items": [
    {
      "name": "T-Shirt - Small",
      "rate": 20.00,
      "purchase_rate": 8.00,
      "sku": "TS-S"
    },
    {
      "name": "T-Shirt - Medium",
      "rate": 20.00,
      "purchase_rate": 8.00,
      "sku": "TS-M"
    }
  ]
}
JSON
```

**Request body:**
- `group_name` (required) - Group name
- `unit` (required) - Unit of measurement

#### Update Item Group

```bash
maton api -X PUT '/zoho-inventory/inventory/v1/itemgroups/{itemgroup_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "group_name": "T-Shirts",
  "unit": "pcs"
}
JSON
```

**Note:** `{itemgroup_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Item Group

```bash
maton api '/zoho-inventory/inventory/v1/itemgroups/{itemgroup_id}' -X DELETE
```

**Note:** `{itemgroup_id}` is a placeholder. Replace it with a real value before sending the request.

#### Item Group Status Actions

```bash
# Mark as active
maton api -X POST '/zoho-inventory/inventory/v1/itemgroups/{itemgroup_id}/active'

# Mark as inactive
maton api -X POST '/zoho-inventory/inventory/v1/itemgroups/{itemgroup_id}/inactive'
```

**Note:** `{itemgroup_id}` is a placeholder. Replace it with a real value before sending the request.

### Pagination

Zoho Inventory uses page-based pagination:

```bash
maton api '/zoho-inventory/inventory/v1/items?page=1&per_page=50'
```

Response includes pagination info in `page_context`:

```json
{
  "code": 0,
  "message": "success",
  "items": [...],
  "page_context": {
    "page": 1,
    "per_page": 50,
    "has_more_page": true,
    "sort_column": "name",
    "sort_order": "A"
  }
}
```

Continue fetching while `has_more_page` is `true`, incrementing `page` each time.

### Notes

- All successful responses have `code: 0` and a `message` field
- Dates should be in `yyyy-mm-dd` format
- Contact types are `customer` or `vendor`
- Item types: `inventory`, `sales`, `purchases`, `sales_and_purchases`
- Product types: `goods` or `service`
- The `organization_id` parameter is automatically handled by the gateway - you do not need to specify it
- Sales order and purchase order numbers are auto-generated by default - do not specify `salesorder_number` or `purchaseorder_number` unless auto-generation is disabled in settings
- Status action endpoints use POST method (e.g., `/status/confirmed`, `/status/void`)
- Rate limits: 100 requests/minute per organization
- Daily limits vary by plan: Free (1,000), Standard (2,500), Professional (5,000), Premium (7,500), Enterprise (10,000)

### Resources

- [Zoho Inventory API v1 Introduction](https://www.zoho.com/inventory/api/v1/introduction/)
- [Items API](https://www.zoho.com/inventory/api/v1/items/)
- [Contacts API](https://www.zoho.com/inventory/api/v1/contacts/)
- [Sales Orders API](https://www.zoho.com/inventory/api/v1/salesorders/)
- [Invoices API](https://www.zoho.com/inventory/api/v1/invoices/)
- [Purchase Orders API](https://www.zoho.com/inventory/api/v1/purchaseorders/)
- [Bills API](https://www.zoho.com/inventory/api/v1/bills/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
