# Jobber Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `jobber`
**Base URL proxied:** `api.getjobber.com/api/`

## API Type

Jobber uses a GraphQL API exclusively. All requests are POST requests to the `/graphql` endpoint.

## API Path Pattern

```
/jobber/graphql
```

All operations use POST with a JSON body containing the `query` field.

## Version Header

Maton automatically injects the `X-JOBBER-GRAPHQL-VERSION` header (currently `2025-04-16`).

## Common Operations

### Get Account
```bash
maton api -X POST '/jobber/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "{ account { id name } }"
}
EOF
```

### List Clients
```bash
maton api -X POST '/jobber/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "{ clients(first: 20) { nodes { id name emails { address } phones { number } } pageInfo { hasNextPage endCursor } } }"
}
EOF
```

### Get Client
```bash
maton api -X POST '/jobber/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "query($id: EncodedId!) { client(id: $id) { id name emails { address } } }",
  "variables": { "id": "CLIENT_ID" }
}
EOF
```

### Create Client
```bash
maton api -X POST '/jobber/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "mutation($input: ClientCreateInput!) { clientCreate(input: $input) { client { id name } userErrors { message path } } }",
  "variables": {
    "input": {
      "firstName": "John",
      "lastName": "Doe",
      "emails": [{"address": "john@example.com"}]
    }
  }
}
EOF
```

### List Jobs
```bash
maton api -X POST '/jobber/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "{ jobs(first: 20) { nodes { id title jobNumber jobStatus client { name } } pageInfo { hasNextPage endCursor } } }"
}
EOF
```

### Create Job
```bash
maton api -X POST '/jobber/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "mutation($input: JobCreateInput!) { jobCreate(input: $input) { job { id jobNumber } userErrors { message path } } }",
  "variables": {
    "input": {
      "clientId": "CLIENT_ID",
      "title": "Service Job"
    }
  }
}
EOF
```

### List Invoices
```bash
maton api -X POST '/jobber/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "{ invoices(first: 20) { nodes { id invoiceNumber total invoiceStatus } pageInfo { hasNextPage endCursor } } }"
}
EOF
```

### List Quotes
```bash
maton api -X POST '/jobber/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "{ quotes(first: 20) { nodes { id quoteNumber title quoteStatus } pageInfo { hasNextPage endCursor } } }"
}
EOF
```

### List Properties
```bash
maton api -X POST '/jobber/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "{ properties(first: 20) { nodes { id address { street city } client { name } } } }"
}
EOF
```

### List Users
```bash
maton api -X POST '/jobber/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "{ users(first: 50) { nodes { id name { full } email { raw } } } }"
}
EOF
```

## Pagination

Jobber uses Relay-style cursor-based pagination:

```bash
# First page
{
  "query": "{ clients(first: 20) { nodes { id name } pageInfo { hasNextPage endCursor } } }"
}

# Next page
{
  "query": "{ clients(first: 20, after: \"CURSOR\") { nodes { id name } pageInfo { hasNextPage endCursor } } }"
}
```

## Notes

- Jobber uses GraphQL exclusively (no REST API)
- Gateway injects version header automatically (`2025-04-16`)
- IDs use `EncodedId` type (base64 encoded) - pass as strings
- Field naming: `emails`/`phones` (arrays), `jobStatus`/`invoiceStatus`/`quoteStatus`
- Rate limits: 2,500 requests per 5 minutes, plus query cost limits (max 10,000 points)
- Old API versions supported for 12-18 months
- Available resources: Clients, Jobs, Invoices, Quotes, Requests, Properties, Users, Custom Fields

## Resources

- [Jobber Developer Documentation](https://developer.getjobber.com/docs/)
- [API Changelog](https://developer.getjobber.com/docs/changelog)
- [API Support](mailto:api-support@getjobber.com)
