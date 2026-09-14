# Xero

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `xero`
**Upstream base URL:** `api.xero.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.xero.com/api.xro/2.0/Contacts`
- Gateway: `https://api.maton.ai/xero/api.xro/2.0/Contacts`

### Contacts API

#### List Contacts

```bash
maton api '/xero/api.xro/2.0/Contacts'
```

#### Get Contact

```bash
maton api '/xero/api.xro/2.0/Contacts/{contactId}'
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Contact

```bash
maton api -X POST '/xero/api.xro/2.0/Contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "Contacts": [{
    "Name": "John Doe",
    "EmailAddress": "john@example.com",
    "Phones": [{"PhoneType": "DEFAULT", "PhoneNumber": "555-1234"}]
  }]
}
JSON
```

### Invoices API

#### List Invoices

```bash
maton api '/xero/api.xro/2.0/Invoices'
```

#### Get Invoice

```bash
maton api '/xero/api.xro/2.0/Invoices/{invoiceId}'
```

**Note:** `{invoiceId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Invoice

```bash
maton api -X POST '/xero/api.xro/2.0/Invoices' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "Invoices": [{
    "Type": "ACCREC",
    "Contact": {"ContactID": "xxx"},
    "LineItems": [{
      "Description": "Service",
      "Quantity": 1,
      "UnitAmount": 100.00,
      "AccountCode": "200"
    }]
  }]
}
JSON
```

### Accounts API

#### List Accounts

```bash
maton api '/xero/api.xro/2.0/Accounts'
```

### Items API

#### List Items

```bash
maton api '/xero/api.xro/2.0/Items'
```

### Payments API

#### List Payments

```bash
maton api '/xero/api.xro/2.0/Payments'
```

### Bank Transactions API

#### List Bank Transactions

```bash
maton api '/xero/api.xro/2.0/BankTransactions'
```

### Reports API

#### Profit and Loss

```bash
maton api '/xero/api.xro/2.0/Reports/ProfitAndLoss?fromDate=2024-01-01&toDate=2024-12-31'
```

#### Balance Sheet

```bash
maton api '/xero/api.xro/2.0/Reports/BalanceSheet?date=2024-12-31'
```

#### Trial Balance

```bash
maton api '/xero/api.xro/2.0/Reports/TrialBalance?date=2024-12-31'
```

### Currencies API

#### List Currencies

```bash
maton api '/xero/api.xro/2.0/Currencies'
```

### Tax Rates API

#### List Tax Rates

```bash
maton api '/xero/api.xro/2.0/TaxRates'
```

### Credit Notes API

#### List Credit Notes

```bash
maton api '/xero/api.xro/2.0/CreditNotes'
```

### Purchase Orders API

#### List Purchase Orders

```bash
maton api '/xero/api.xro/2.0/PurchaseOrders'
```

### Organisation API

#### Get Organisation

```bash
maton api '/xero/api.xro/2.0/Organisation'
```

### Automatic Tenant ID Injection

The router automatically injects the `Xero-Tenant-Id` header from your connection config. You do not need to provide it manually.

### Invoice Types

- `ACCREC` - Accounts Receivable (sales invoice)
- `ACCPAY` - Accounts Payable (bill)

### Notes

- `Xero-Tenant-Id` header is automatically injected by the router
- Dates are in `YYYY-MM-DD` format
- Multiple records can be created in a single request using arrays
- Updates use POST method with the record ID in the URL
- Draft invoices can be deleted by setting `Status` to `DELETED`
- Use `where` query parameter for filtering (e.g., `where=Status=="VOIDED"`)

### Resources

- [Xero API Overview](https://developer.xero.com/documentation/api/accounting/overview)
- [List Contacts](https://developer.xero.com/documentation/api/accounting/contacts#get-contacts)
- [Get Contact](https://developer.xero.com/documentation/api/accounting/contacts#get-contacts)
- [Create Contact](https://developer.xero.com/documentation/api/accounting/contacts#put-contacts)
- [Update Contact](https://developer.xero.com/documentation/api/accounting/contacts#post-contacts)
- [List Invoices](https://developer.xero.com/documentation/api/accounting/invoices#get-invoices)
- [Get Invoice](https://developer.xero.com/documentation/api/accounting/invoices#get-invoices)
- [Create Invoice](https://developer.xero.com/documentation/api/accounting/invoices#put-invoices)
- [Update Invoice](https://developer.xero.com/documentation/api/accounting/invoices#post-invoices)
- [Email Invoice](https://developer.xero.com/documentation/api/accounting/invoices#emailing-an-invoice)
- [List Accounts](https://developer.xero.com/documentation/api/accounting/accounts#get-accounts)
- [Get Account](https://developer.xero.com/documentation/api/accounting/accounts#get-accounts)
- [Create Account](https://developer.xero.com/documentation/api/accounting/accounts#put-accounts)
- [Update Account](https://developer.xero.com/documentation/api/accounting/accounts#post-accounts)
- [Delete Account](https://developer.xero.com/documentation/api/accounting/accounts#delete-accounts)
- [List Items](https://developer.xero.com/documentation/api/accounting/items#get-items)
- [Get Item](https://developer.xero.com/documentation/api/accounting/items#get-items)
- [Create Item](https://developer.xero.com/documentation/api/accounting/items#put-items)
- [Update Item](https://developer.xero.com/documentation/api/accounting/items#post-items)
- [Delete Item](https://developer.xero.com/documentation/api/accounting/items#delete-items)
- [List Payments](https://developer.xero.com/documentation/api/accounting/payments#get-payments)
- [Get Payment](https://developer.xero.com/documentation/api/accounting/payments#get-payments)
- [Create Payment](https://developer.xero.com/documentation/api/accounting/payments#put-payments)
- [Update Payment](https://developer.xero.com/documentation/api/accounting/payments#post-payments)
- [List Bank Transactions](https://developer.xero.com/documentation/api/accounting/banktransactions#get-banktransactions)
- [Get Bank Transaction](https://developer.xero.com/documentation/api/accounting/banktransactions#get-banktransactions)
- [Create Bank Transaction](https://developer.xero.com/documentation/api/accounting/banktransactions#put-banktransactions)
- [Update Bank Transaction](https://developer.xero.com/documentation/api/accounting/banktransactions#post-banktransactions)
- [Profit and Loss Report](https://developer.xero.com/documentation/api/accounting/reports#profitandloss)
- [Balance Sheet Report](https://developer.xero.com/documentation/api/accounting/reports#balancesheet)
- [Trial Balance Report](https://developer.xero.com/documentation/api/accounting/reports#trialbalance)
- [Bank Summary Report](https://developer.xero.com/documentation/api/accounting/reports#banksummary)
- [Get Organisation](https://developer.xero.com/documentation/api/accounting/organisation#get-organisation)
- [Maton CLI Manual](https://cli.maton.ai/manual)
