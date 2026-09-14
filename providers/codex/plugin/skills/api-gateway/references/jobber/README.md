# Jobber

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `jobber`
**Upstream base URL:** `api.getjobber.com/api/`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.getjobber.com/api//graphql`
- Gateway: `https://api.maton.ai/jobber/graphql`

**Important:** Jobber uses a GraphQL API exclusively. All requests are POST requests to the `/graphql` endpoint. The gateway also injects the `X-JOBBER-GRAPHQL-VERSION` header (currently `2025-04-16`).

### Account API

#### Get Account Information

```bash
maton api -X POST '/jobber/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "{ account { id name } }"
}
JSON
```

### Client API

#### List Clients

```bash
maton api -X POST '/jobber/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "{ clients(first: 20) { nodes { id name emails { primary address } phones { primary number } } pageInfo { hasNextPage endCursor } } }"
}
JSON
```

#### Get Client by ID

```bash
maton api -X POST '/jobber/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "query($id: EncodedId!) { client(id: $id) { id name emails { primary address } phones { primary number } billingAddress { street city } } }",
  "variables": { "id": "CLIENT_ID" }
}
JSON
```

#### Create Client

```bash
maton api -X POST '/jobber/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "mutation($input: ClientCreateInput!) { clientCreate(input: $input) { client { id name } userErrors { message path } } }",
  "variables": {
    "input": {
      "firstName": "John",
      "lastName": "Doe",
      "email": "john@example.com",
      "phone": "555-1234"
    }
  }
}
JSON
```

#### Update Client

```bash
maton api -X POST '/jobber/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "mutation($id: EncodedId!, $input: ClientUpdateInput!) { clientUpdate(clientId: $id, input: $input) { client { id name } userErrors { message path } } }",
  "variables": {
    "id": "CLIENT_ID",
    "input": {
      "email": "newemail@example.com"
    }
  }
}
JSON
```

### Job API

#### List Jobs

```bash
maton api -X POST '/jobber/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "{ jobs(first: 20) { nodes { id title jobNumber jobStatus client { name } } pageInfo { hasNextPage endCursor } } }"
}
JSON
```

#### Get Job by ID

```bash
maton api -X POST '/jobber/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "query($id: EncodedId!) { job(id: $id) { id title jobNumber jobStatus instructions client { name } property { address { street city } } } }",
  "variables": { "id": "JOB_ID" }
}
JSON
```

#### Create Job

```bash
maton api -X POST '/jobber/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "mutation($input: JobCreateInput!) { jobCreate(input: $input) { job { id jobNumber title } userErrors { message path } } }",
  "variables": {
    "input": {
      "clientId": "CLIENT_ID",
      "title": "Lawn Maintenance",
      "instructions": "Weekly lawn care service"
    }
  }
}
JSON
```

### Invoice API

#### List Invoices

```bash
maton api -X POST '/jobber/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "{ invoices(first: 20) { nodes { id invoiceNumber subject total invoiceStatus client { name } } pageInfo { hasNextPage endCursor } } }"
}
JSON
```

#### Get Invoice by ID

```bash
maton api -X POST '/jobber/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "query($id: EncodedId!) { invoice(id: $id) { id invoiceNumber subject total amountDue invoiceStatus lineItems { nodes { name quantity unitPrice } } } }",
  "variables": { "id": "INVOICE_ID" }
}
JSON
```

#### Create Invoice

```bash
maton api -X POST '/jobber/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "mutation($input: InvoiceCreateInput!) { invoiceCreate(input: $input) { invoice { id invoiceNumber } userErrors { message path } } }",
  "variables": {
    "input": {
      "clientId": "CLIENT_ID",
      "subject": "Service Invoice",
      "lineItems": [
        {
          "name": "Lawn Care",
          "quantity": 1,
          "unitPrice": 75.00
        }
      ]
    }
  }
}
JSON
```

### Quote API

#### List Quotes

```bash
maton api -X POST '/jobber/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "{ quotes(first: 20) { nodes { id quoteNumber title quoteStatus client { name } } pageInfo { hasNextPage endCursor } } }"
}
JSON
```

#### Create Quote

```bash
maton api -X POST '/jobber/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "mutation($input: QuoteCreateInput!) { quoteCreate(input: $input) { quote { id quoteNumber } userErrors { message path } } }",
  "variables": {
    "input": {
      "clientId": "CLIENT_ID",
      "title": "Landscaping Quote",
      "lineItems": [
        {
          "name": "Garden Design",
          "quantity": 1,
          "unitPrice": 500.00
        }
      ]
    }
  }
}
JSON
```

### Property API

#### List Properties

```bash
maton api -X POST '/jobber/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "{ properties(first: 20) { nodes { id address { street city state postalCode } client { name } } pageInfo { hasNextPage endCursor } } }"
}
JSON
```

### Request API

#### List Requests

```bash
maton api -X POST '/jobber/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "{ requests(first: 20) { nodes { id title requestStatus client { name } } pageInfo { hasNextPage endCursor } } }"
}
JSON
```

### User/Team API

#### List Users

```bash
maton api -X POST '/jobber/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "{ users(first: 50) { nodes { id name { full } email { raw } } } }"
}
JSON
```

### Custom Field API

#### List Custom Fields

```bash
maton api -X POST '/jobber/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "{ customFields(first: 50) { nodes { id name fieldType } } }"
}
JSON
```

### Pagination

Jobber uses Relay-style cursor-based pagination:

```bash
# First page
maton api -X POST '/jobber/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ clients(first: 20) { nodes { id name } pageInfo { hasNextPage endCursor } } }"}
JSON

# Next page using cursor
maton api -X POST '/jobber/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ clients(first: 20, after: \"CURSOR_VALUE\") { nodes { id name } pageInfo { hasNextPage endCursor } } }"}
JSON
```

Response includes `pageInfo`:
```json
{
  "data": {
    "clients": {
      "nodes": [...],
      "pageInfo": {
        "hasNextPage": true,
        "endCursor": "abc123"
      }
    }
  }
}
```

### Webhooks

Jobber supports webhooks for real-time event notifications:

- `CLIENT_CREATE` - New client created
- `JOB_COMPLETE` - Job marked complete
- `QUOTE_CREATE` - New quote created
- `QUOTE_APPROVAL` - Quote approved
- `REQUEST_CREATE` - New request created
- `INVOICE_CREATE` - New invoice created
- `APP_CONNECT` - App connected

Webhooks include HMAC-SHA256 signatures for verification.

### Notes

- Jobber uses GraphQL exclusively (no REST API)
- Maton automatically injects the `X-JOBBER-GRAPHQL-VERSION` header
- Current gateway API version: `2025-04-16` (latest)
- Old API versions are supported for 12-18 months from release
- Use the GraphiQL explorer in Jobber's Developer Center for schema discovery
- IDs use `EncodedId` type (base64 encoded) - pass as strings
- Field naming: use `emails`/`phones` (arrays), `jobStatus`/`invoiceStatus`/`quoteStatus`/`requestStatus`
- Rate limits:
  - DDoS protection: 2,500 requests per 5 minutes per app/account
  - Query cost: Points-based using leaky bucket algorithm (max 10,000 points, restore 500/sec)
- Avoid deeply nested queries to reduce query cost

### Resources

- [Jobber Developer Documentation](https://developer.getjobber.com/docs/)
- [Jobber API Changelog](https://developer.getjobber.com/docs/changelog)
- [Jobber API Support](mailto:api-support@getjobber.com)
- [Maton CLI Manual](https://cli.maton.ai/manual)
