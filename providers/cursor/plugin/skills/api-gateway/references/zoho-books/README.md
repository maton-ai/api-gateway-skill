# Zoho Books Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `zoho-books`
**Base URL proxied:** `www.zohoapis.com`

## API Path Pattern

```
/zoho-books/books/v3/{resource}
```

## Common Endpoints

### Contacts

```bash
# List contacts
maton api '/zoho-books/books/v3/contacts'

# Get contact
maton api '/zoho-books/books/v3/contacts/{contact_id}'

# Create contact
maton api -X POST '/zoho-books/books/v3/contacts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "contact_name": "Customer Name",
  "contact_type": "customer"
}
EOF

# Update contact
maton api -X PUT '/zoho-books/books/v3/contacts/{contact_id}'

# Delete contact
maton api -X DELETE '/zoho-books/books/v3/contacts/{contact_id}'
```

### Invoices

```bash
# List invoices
maton api '/zoho-books/books/v3/invoices'

# Get invoice
maton api '/zoho-books/books/v3/invoices/{invoice_id}'

# Create invoice
maton api -X POST '/zoho-books/books/v3/invoices'

# Mark as sent
maton api -X POST '/zoho-books/books/v3/invoices/{invoice_id}/status/sent'

# Email invoice
maton api -X POST '/zoho-books/books/v3/invoices/{invoice_id}/email'
```

### Bills

```bash
# List bills
maton api '/zoho-books/books/v3/bills'

# Create bill
maton api -X POST '/zoho-books/books/v3/bills'

# Update bill
maton api -X PUT '/zoho-books/books/v3/bills/{bill_id}'

# Delete bill
maton api -X DELETE '/zoho-books/books/v3/bills/{bill_id}'
```

### Expenses

```bash
# List expenses
maton api '/zoho-books/books/v3/expenses'

# Create expense
maton api -X POST '/zoho-books/books/v3/expenses'

# Update expense
maton api -X PUT '/zoho-books/books/v3/expenses/{expense_id}'

# Delete expense
maton api -X DELETE '/zoho-books/books/v3/expenses/{expense_id}'
```

### Sales Orders

```bash
maton api '/zoho-books/books/v3/salesorders'
maton api -X POST '/zoho-books/books/v3/salesorders'
```

### Purchase Orders

```bash
maton api '/zoho-books/books/v3/purchaseorders'
maton api -X POST '/zoho-books/books/v3/purchaseorders'
```

### Credit Notes

```bash
maton api '/zoho-books/books/v3/creditnotes'
```

### Recurring Invoices

```bash
maton api '/zoho-books/books/v3/recurringinvoices'
```

### Recurring Bills

```bash
maton api '/zoho-books/books/v3/recurringbills'
```

## Available Modules

| Module | Endpoint | Description |
|--------|----------|-------------|
| Contacts | `/contacts` | Customers and vendors |
| Invoices | `/invoices` | Sales invoices |
| Bills | `/bills` | Vendor bills |
| Expenses | `/expenses` | Business expenses |
| Sales Orders | `/salesorders` | Sales orders |
| Purchase Orders | `/purchaseorders` | Purchase orders |
| Credit Notes | `/creditnotes` | Customer credit notes |
| Recurring Invoices | `/recurringinvoices` | Recurring invoices |
| Recurring Bills | `/recurringbills` | Recurring bills |

## Notes

- All successful responses have `code: 0`
- Dates should be in `yyyy-mm-dd` format
- Contact types are `customer` or `vendor`
- Some modules (items, chart of accounts, bank accounts, projects) require additional OAuth scopes
- Rate limits: 100 requests/minute per organization
- Pagination uses `page` and `per_page` parameters with `has_more_page` in response

## Resources

- [Zoho Books API v3 Introduction](https://www.zoho.com/books/api/v3/introduction/)
- [Zoho Books Invoices API](https://www.zoho.com/books/api/v3/invoices/)
- [Zoho Books Contacts API](https://www.zoho.com/books/api/v3/contacts/)
- [Zoho Books Bills API](https://www.zoho.com/books/api/v3/bills/)
- [Zoho Books Expenses API](https://www.zoho.com/books/api/v3/expenses/)
