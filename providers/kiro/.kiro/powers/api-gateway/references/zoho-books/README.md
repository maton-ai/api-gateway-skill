# Zoho Books

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `zoho-books`
**Upstream base URL:** `www.zohoapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://www.zohoapis.com/books/v3/contacts`
- Gateway: `https://api.maton.ai/zoho-books/books/v3/contacts`

**Important:** Zoho Books organizes data into modules. Key modules include:

| Module | Endpoint | Description |
|--------|----------|-------------|
| Contacts | `/contacts` | Customers and vendors |
| Invoices | `/invoices` | Sales invoices |
| Bills | `/bills` | Vendor bills |
| Expenses | `/expenses` | Business expenses |
| Sales Orders | `/salesorders` | Sales orders |
| Purchase Orders | `/purchaseorders` | Purchase orders |
| Credit Notes | `/creditnotes` | Customer credit notes |
| Recurring Invoices | `/recurringinvoices` | Automated recurring invoices |
| Recurring Bills | `/recurringbills` | Automated recurring bills |

### Contacts API

#### List Contacts

```bash
maton api '/zoho-books/books/v3/contacts'
```

**Response:**
```json
{
  "code": 0,
  "message": "success",
  "contacts": [...],
  "page_context": {
    "page": 1,
    "per_page": 200,
    "has_more_page": false,
    "sort_column": "contact_name",
    "sort_order": "A"
  }
}
```

#### Get Contact

```bash
maton api '/zoho-books/books/v3/contacts/{contact_id}'
```

**Note:** `{contact_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Contact

```bash
maton api -X POST '/zoho-books/books/v3/contacts' -H 'Content-Type: application/json' --input - <<'JSON'
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
- `contact_name` (required) - Display name for the contact
- `contact_type` (required) - Either `customer` or `vendor`
- `company_name` (optional) - Legal entity name
- `email` (optional) - Email address
- `phone` (optional) - Phone number
- `billing_address` (optional) - Address object
- `payment_terms` (optional) - Days for payment

**Example:**

```bash
maton api -X POST '/zoho-books/books/v3/contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contact_name": "Acme Corporation",
  "contact_type": "customer",
  "company_name": "Acme Corp",
  "email": "billing@acme.com",
  "phone": "+1-555-1234"
}
JSON
```

**Response:**
```json
{
  "code": 0,
  "message": "The contact has been added.",
  "contact": {
    "contact_id": "8527119000000099001",
    "contact_name": "Acme Corporation",
    "company_name": "Acme Corp",
    "contact_type": "customer",
    ...
  }
}
```

#### Update Contact

```bash
maton api -X PUT '/zoho-books/books/v3/contacts/{contact_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contact_name": "Updated Name",
  "phone": "+1-555-9999"
}
JSON
```

**Note:** `{contact_id}` is a placeholder. Replace it with a real value before sending the request.

**Example:**

```bash
maton api -X PUT '/zoho-books/books/v3/contacts/{contact_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contact_name": "Acme Corporation Updated",
  "phone": "+1-555-9999"
}
JSON
```

**Note:** `{contact_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Contact

```bash
maton api '/zoho-books/books/v3/contacts/{contact_id}' -X DELETE
```

**Note:** `{contact_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "code": 0,
  "message": "The customer has been deleted."
}
```

### Invoices API

#### List Invoices

```bash
maton api '/zoho-books/books/v3/invoices'
```

#### Get Invoice

```bash
maton api '/zoho-books/books/v3/invoices/{invoice_id}'
```

**Note:** `{invoice_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Invoice

```bash
maton api -X POST '/zoho-books/books/v3/invoices' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "customer_id": "8527119000000099001",
  "line_items": [
    {
      "item_id": "8527119000000100001",
      "quantity": 1,
      "rate": 100.00
    }
  ]
}
JSON
```

**Request body:**
- `customer_id` (required) - Customer identifier
- `line_items` (required) - Array of items with `item_id` or manual entry
- `invoice_number` (optional) - Auto-generated if not specified
- `date` (optional) - Invoice date (yyyy-mm-dd format)
- `due_date` (optional) - Payment due date
- `discount` (optional) - Percentage or fixed amount
- `payment_terms` (optional) - Days until due

#### Update Invoice

```bash
maton api -X PUT '/zoho-books/books/v3/invoices/{invoice_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "customer_id": "{customer_id}",
  "line_items": [
    {"item_id": "{item_id}", "quantity": 2, "rate": 100.00}
  ]
}
JSON
```

**Note:** `{invoice_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Invoice

```bash
maton api '/zoho-books/books/v3/invoices/{invoice_id}' -X DELETE
```

**Note:** `{invoice_id}` is a placeholder. Replace it with a real value before sending the request.

#### Invoice Actions

```bash
# Mark as sent
maton api -X POST '/zoho-books/books/v3/invoices/{invoice_id}/status/sent'

# Void invoice
maton api -X POST '/zoho-books/books/v3/invoices/{invoice_id}/status/void'

# Email invoice
maton api -X POST '/zoho-books/books/v3/invoices/{invoice_id}/email' -H 'Content-Type: application/json' --input - <<'JSON'
{"to_mail_ids": ["customer@example.com"]}
JSON
```

**Note:** `{invoice_id}` is a placeholder. Replace it with a real value before sending the request.

### Bills API

#### List Bills

```bash
maton api '/zoho-books/books/v3/bills'
```

#### Create Bill

```bash
maton api -X POST '/zoho-books/books/v3/bills' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "vendor_id": "8527119000000099002",
  "bill_number": "BILL-001",
  "date": "2026-02-06",
  "line_items": [
    {
      "account_id": "8527119000000100002",
      "description": "Office Supplies",
      "amount": 150.00
    }
  ]
}
JSON
```

**Request body:**
- `vendor_id` (required) - Vendor identifier
- `bill_number` (required) - Unique bill number
- `date` (required) - Bill date (yyyy-mm-dd)

#### Update Bill

```bash
maton api -X PUT '/zoho-books/books/v3/bills/{bill_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "vendor_id": "{vendor_id}",
  "bill_number": "BILL-001",
  "date": "2026-09-09",
  "line_items": [
    {"name": "Consulting", "quantity": 1, "rate": 500.00}
  ]
}
JSON
```

**Note:** `{bill_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Bill

```bash
maton api '/zoho-books/books/v3/bills/{bill_id}' -X DELETE
```

**Note:** `{bill_id}` is a placeholder. Replace it with a real value before sending the request.

### Expenses API

#### List Expenses

```bash
maton api '/zoho-books/books/v3/expenses'
```

#### Create Expense

```bash
maton api -X POST '/zoho-books/books/v3/expenses' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "account_id": "8527119000000100003",
  "date": "2026-02-06",
  "amount": 75.50,
  "paid_through_account_id": "8527119000000100004",
  "description": "Business lunch"
}
JSON
```

**Request body:**
- `account_id` (required) - Expense account ID
- `date` (required) - Expense date (yyyy-mm-dd)
- `amount` (required) - Expense amount
- `paid_through_account_id` (required) - Payment account ID
- `description` (optional) - Expense details
- `customer_id` (optional) - Billable customer ID
- `is_billable` (optional) - Boolean for billable expenses
- `project_id` (optional) - Associated project

#### Update Expense

```bash
maton api -X PUT '/zoho-books/books/v3/expenses/{expense_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "account_id": "{expense_account_id}",
  "date": "2026-09-09",
  "amount": 42.50
}
JSON
```

**Note:** `{expense_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Expense

```bash
maton api '/zoho-books/books/v3/expenses/{expense_id}' -X DELETE
```

**Note:** `{expense_id}` is a placeholder. Replace it with a real value before sending the request.

### Sales Orders API

#### List Sales Orders

```bash
maton api '/zoho-books/books/v3/salesorders'
```

#### Create Sales Order

```bash
maton api -X POST '/zoho-books/books/v3/salesorders' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "customer_id": "{customer_id}",
  "date": "2026-09-09",
  "line_items": [
    {"item_id": "{item_id}", "quantity": 1, "rate": 100.00}
  ]
}
JSON
```

### Purchase Orders API

#### List Purchase Orders

```bash
maton api '/zoho-books/books/v3/purchaseorders'
```

#### Create Purchase Order

```bash
maton api -X POST '/zoho-books/books/v3/purchaseorders' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "vendor_id": "{vendor_id}",
  "date": "2026-09-09",
  "line_items": [
    {"item_id": "{item_id}", "quantity": 1, "rate": 100.00}
  ]
}
JSON
```

### Credit Notes API

#### List Credit Notes

```bash
maton api '/zoho-books/books/v3/creditnotes'
```

### Recurring Invoices API

#### List Recurring Invoices

```bash
maton api '/zoho-books/books/v3/recurringinvoices'
```

### Recurring Bills API

#### List Recurring Bills

```bash
maton api '/zoho-books/books/v3/recurringbills'
```

### Pagination

Zoho Books uses page-based pagination:

```bash
maton api '/zoho-books/books/v3/contacts?page=1&per_page=50'
```

Response includes pagination info in `page_context`:

```json
{
  "code": 0,
  "message": "success",
  "contacts": [...],
  "page_context": {
    "page": 1,
    "per_page": 50,
    "has_more_page": true,
    "sort_column": "contact_name",
    "sort_order": "A"
  }
}
```

Continue fetching while `has_more_page` is `true`, incrementing `page` each time.

### Notes

- All successful responses have `code: 0` and a `message` field
- Dates should be in `yyyy-mm-dd` format
- Contact types are `customer` or `vendor`
- Some modules (items, chart of accounts, bank accounts, projects) may require additional OAuth scopes. If you receive a scope error, contact Maton support at support@maton.ai with the specific operations/APIs you need and your use-case
- Rate limits: 100 requests/minute per organization
- Daily limits vary by plan: Free (1,000), Standard (2,000), Professional (5,000), Paid (10,000)

### Resources

- [Zoho Books API v3 Introduction](https://www.zoho.com/books/api/v3/introduction/)
- [Invoices API](https://www.zoho.com/books/api/v3/invoices/)
- [Contacts API](https://www.zoho.com/books/api/v3/contacts/)
- [Bills API](https://www.zoho.com/books/api/v3/bills/)
- [Expenses API](https://www.zoho.com/books/api/v3/expenses/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
