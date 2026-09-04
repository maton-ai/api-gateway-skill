# Zoho Inventory Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `zoho-inventory`
**Base URL proxied:** `www.zohoapis.com`

## API Path Pattern

```
/zoho-inventory/inventory/v1/{resource}
```

## Common Endpoints

### Items

```bash
# List items
maton api '/zoho-inventory/inventory/v1/items'

# Get item
maton api '/zoho-inventory/inventory/v1/items/{item_id}'

# Create item
maton api -X POST '/zoho-inventory/inventory/v1/items' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Widget",
  "rate": 25.00,
  "purchase_rate": 10.00,
  "sku": "WDG-001",
  "item_type": "inventory",
  "product_type": "goods",
  "unit": "pcs"
}
EOF

# Update item
maton api -X PUT '/zoho-inventory/inventory/v1/items/{item_id}'

# Delete item
maton api -X DELETE '/zoho-inventory/inventory/v1/items/{item_id}'

# Mark as active/inactive
maton api -X POST '/zoho-inventory/inventory/v1/items/{item_id}/active'
maton api -X POST '/zoho-inventory/inventory/v1/items/{item_id}/inactive'
```

### Contacts

```bash
# List contacts
maton api '/zoho-inventory/inventory/v1/contacts'

# Get contact
maton api '/zoho-inventory/inventory/v1/contacts/{contact_id}'

# Create contact
maton api -X POST '/zoho-inventory/inventory/v1/contacts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "contact_name": "Customer Name",
  "contact_type": "customer"
}
EOF

# Update contact
maton api -X PUT '/zoho-inventory/inventory/v1/contacts/{contact_id}'

# Delete contact
maton api -X DELETE '/zoho-inventory/inventory/v1/contacts/{contact_id}'
```

### Sales Orders

```bash
# List sales orders
maton api '/zoho-inventory/inventory/v1/salesorders'

# Get sales order
maton api '/zoho-inventory/inventory/v1/salesorders/{salesorder_id}'

# Create sales order
maton api -X POST '/zoho-inventory/inventory/v1/salesorders'

# Update sales order
maton api -X PUT '/zoho-inventory/inventory/v1/salesorders/{salesorder_id}'

# Delete sales order
maton api -X DELETE '/zoho-inventory/inventory/v1/salesorders/{salesorder_id}'

# Status actions
maton api -X POST '/zoho-inventory/inventory/v1/salesorders/{salesorder_id}/status/confirmed'
maton api -X POST '/zoho-inventory/inventory/v1/salesorders/{salesorder_id}/status/void'
```

### Invoices

```bash
# List invoices
maton api '/zoho-inventory/inventory/v1/invoices'

# Get invoice
maton api '/zoho-inventory/inventory/v1/invoices/{invoice_id}'

# Create invoice
maton api -X POST '/zoho-inventory/inventory/v1/invoices'

# Update invoice
maton api -X PUT '/zoho-inventory/inventory/v1/invoices/{invoice_id}'

# Delete invoice
maton api -X DELETE '/zoho-inventory/inventory/v1/invoices/{invoice_id}'

# Status actions
maton api -X POST '/zoho-inventory/inventory/v1/invoices/{invoice_id}/status/sent'
maton api -X POST '/zoho-inventory/inventory/v1/invoices/{invoice_id}/status/draft'
maton api -X POST '/zoho-inventory/inventory/v1/invoices/{invoice_id}/status/void'

# Email
maton api -X POST '/zoho-inventory/inventory/v1/invoices/{invoice_id}/email'
```

### Purchase Orders

```bash
# List purchase orders
maton api '/zoho-inventory/inventory/v1/purchaseorders'

# Create purchase order
maton api -X POST '/zoho-inventory/inventory/v1/purchaseorders'

# Update purchase order
maton api -X PUT '/zoho-inventory/inventory/v1/purchaseorders/{purchaseorder_id}'

# Delete purchase order
maton api -X DELETE '/zoho-inventory/inventory/v1/purchaseorders/{purchaseorder_id}'

# Status actions
maton api -X POST '/zoho-inventory/inventory/v1/purchaseorders/{purchaseorder_id}/status/issued'
maton api -X POST '/zoho-inventory/inventory/v1/purchaseorders/{purchaseorder_id}/status/cancelled'
```

### Bills

```bash
# List bills
maton api '/zoho-inventory/inventory/v1/bills'

# Create bill
maton api -X POST '/zoho-inventory/inventory/v1/bills'

# Update bill
maton api -X PUT '/zoho-inventory/inventory/v1/bills/{bill_id}'

# Delete bill
maton api -X DELETE '/zoho-inventory/inventory/v1/bills/{bill_id}'

# Status actions
maton api -X POST '/zoho-inventory/inventory/v1/bills/{bill_id}/status/open'
maton api -X POST '/zoho-inventory/inventory/v1/bills/{bill_id}/status/void'
```

### Item Groups

```bash
maton api '/zoho-inventory/inventory/v1/itemgroups'
maton api '/zoho-inventory/inventory/v1/itemgroups/{itemgroup_id}'
maton api -X POST '/zoho-inventory/inventory/v1/itemgroups'
maton api -X PUT '/zoho-inventory/inventory/v1/itemgroups/{itemgroup_id}'
maton api -X DELETE '/zoho-inventory/inventory/v1/itemgroups/{itemgroup_id}'
```

### Shipment Orders

```bash
maton api -X POST '/zoho-inventory/inventory/v1/shipmentorders'
maton api '/zoho-inventory/inventory/v1/shipmentorders/{shipmentorder_id}'
maton api -X PUT '/zoho-inventory/inventory/v1/shipmentorders/{shipmentorder_id}'
maton api -X DELETE '/zoho-inventory/inventory/v1/shipmentorders/{shipmentorder_id}'
maton api -X POST '/zoho-inventory/inventory/v1/shipmentorders/{shipmentorder_id}/status/delivered'
```

## Available Modules

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

## Notes

- All successful responses have `code: 0`
- Dates should be in `yyyy-mm-dd` format
- Contact types are `customer` or `vendor`
- The `organization_id` parameter is automatically handled by the gateway
- Sales order and purchase order numbers are auto-generated by default
- Pagination uses `page` and `per_page` parameters with `has_more_page` in response
- Rate limits: 100 requests/minute per organization

## Resources

- [Zoho Inventory API v1 Introduction](https://www.zoho.com/inventory/api/v1/introduction/)
- [Zoho Inventory Items API](https://www.zoho.com/inventory/api/v1/items/)
- [Zoho Inventory Contacts API](https://www.zoho.com/inventory/api/v1/contacts/)
- [Zoho Inventory Sales Orders API](https://www.zoho.com/inventory/api/v1/salesorders/)
- [Zoho Inventory Invoices API](https://www.zoho.com/inventory/api/v1/invoices/)
- [Zoho Inventory Purchase Orders API](https://www.zoho.com/inventory/api/v1/purchaseorders/)
- [Zoho Inventory Bills API](https://www.zoho.com/inventory/api/v1/bills/)
