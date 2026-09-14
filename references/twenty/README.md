# Twenty CRM

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `twenty`
**Upstream base URL:** `api.twenty.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.twenty.com/rest/companies`
- Gateway: `https://api.maton.ai/twenty/rest/companies`

### Companies API

#### List Companies

```bash
maton api '/twenty/rest/companies?limit=20'
```

**Response:**
```json
{
  "data": {
    "companies": [
      {
        "id": "06290608-8bf0-4806-99ae-a715a6a93fad",
        "name": "Acme Corp",
        "domainName": {
          "primaryLinkUrl": "https://acme.com"
        },
        "employees": 100,
        "address": {
          "addressCity": "San Francisco",
          "addressState": "CA",
          "addressCountry": "United States"
        },
        "createdAt": "2026-03-20T23:59:52.906Z",
        "updatedAt": "2026-03-20T23:59:52.906Z"
      }
    ]
  },
  "pageInfo": {
    "hasNextPage": true,
    "startCursor": "06290608-8bf0-4806-99ae-a715a6a93fad",
    "endCursor": "1f70157c-4ea5-4d81-bc49-e1401abfbb94"
  },
  "totalCount": 50
}
```

#### Get Company

```bash
maton api '/twenty/rest/companies/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Company

```bash
maton api -X POST '/twenty/rest/companies' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Company",
  "domainName": {
    "primaryLinkUrl": "https://newcompany.com"
  },
  "employees": 50
}
JSON
```

#### Update Company

```bash
maton api -X PATCH '/twenty/rest/companies/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Company Name",
  "employees": 100
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Company

```bash
maton api '/twenty/rest/companies/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### People API

#### List People

```bash
maton api '/twenty/rest/people?limit=20'
```

**Response:**
```json
{
  "data": {
    "people": [
      {
        "id": "7a93d1e5-3f74-4945-8a65-d7f996083f72",
        "name": {
          "firstName": "John",
          "lastName": "Doe"
        },
        "emails": {
          "primaryEmail": "john@company.com"
        },
        "phones": {
          "primaryPhoneNumber": "5551234567",
          "primaryPhoneCallingCode": "+1"
        },
        "jobTitle": "CEO",
        "city": "San Francisco",
        "companyId": "06290608-8bf0-4806-99ae-a715a6a93fad"
      }
    ]
  },
  "pageInfo": {...},
  "totalCount": 100
}
```

#### Get Person

```bash
maton api '/twenty/rest/people/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Person

```bash
maton api -X POST '/twenty/rest/people' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": {
    "firstName": "Jane",
    "lastName": "Smith"
  },
  "emails": {
    "primaryEmail": "jane@company.com"
  },
  "jobTitle": "CTO",
  "companyId": "06290608-8bf0-4806-99ae-a715a6a93fad"
}
JSON
```

#### Update Person

```bash
maton api -X PATCH '/twenty/rest/people/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "jobTitle": "VP of Engineering"
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Person

```bash
maton api '/twenty/rest/people/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Opportunities API

#### List Opportunities

```bash
maton api '/twenty/rest/opportunities?limit=20'
```

**Response:**
```json
{
  "data": {
    "opportunities": [
      {
        "id": "2beb07b0-340c-41d7-be33-5aa91757f329",
        "name": "Enterprise Deal",
        "amount": {
          "amountMicros": 75000000000,
          "currencyCode": "USD"
        },
        "closeDate": "2026-01-25T16:26:00.000Z",
        "stage": "SCREENING",
        "companyId": "1f70157c-4ea5-4d81-bc49-e1401abfbb94",
        "pointOfContactId": "edf6d445-13a7-4373-9a47-8f89e8c0a877"
      }
    ]
  },
  "pageInfo": {...},
  "totalCount": 25
}
```

**Note:** Amount is stored in micros (divide by 1,000,000 for actual value).

#### Get Opportunity

```bash
maton api '/twenty/rest/opportunities/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Opportunity

```bash
maton api -X POST '/twenty/rest/opportunities' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Deal",
  "amount": {
    "amountMicros": 50000000000,
    "currencyCode": "USD"
  },
  "stage": "SCREENING",
  "closeDate": "2026-06-01T00:00:00.000Z",
  "companyId": "06290608-8bf0-4806-99ae-a715a6a93fad"
}
JSON
```

#### Update Opportunity

```bash
maton api -X PATCH '/twenty/rest/opportunities/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "stage": "MEETING",
  "amount": {
    "amountMicros": 60000000000,
    "currencyCode": "USD"
  }
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Opportunity

```bash
maton api '/twenty/rest/opportunities/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Notes API

#### List Notes

```bash
maton api '/twenty/rest/notes?limit=20'
```

#### Get Note

```bash
maton api '/twenty/rest/notes/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Note

```bash
maton api -X POST '/twenty/rest/notes' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Meeting Notes",
  "body": "Discussed Q2 roadmap and partnership opportunities."
}
JSON
```

#### Update Note

```bash
maton api -X PATCH '/twenty/rest/notes/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "body": "Updated meeting notes with action items."
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Note

```bash
maton api '/twenty/rest/notes/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Tasks API

#### List Tasks

```bash
maton api '/twenty/rest/tasks?limit=20'
```

#### Get Task

```bash
maton api '/twenty/rest/tasks/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Task

```bash
maton api -X POST '/twenty/rest/tasks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Follow up with client",
  "body": "Send proposal and schedule demo",
  "dueAt": "2026-04-01T00:00:00.000Z",
  "status": "TODO"
}
JSON
```

#### Update Task

```bash
maton api -X PATCH '/twenty/rest/tasks/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "status": "DONE"
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Task

```bash
maton api '/twenty/rest/tasks/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Workspace Members API

#### List Workspace Members

```bash
maton api '/twenty/rest/workspaceMembers?limit=20'
```

### Filtering

```bash
maton api '/twenty/rest/companies?filter=employees[gte]:100'
maton api '/twenty/rest/opportunities?filter=stage[eq]:"MEETING"'
```

Comparators: `eq`, `neq`, `gt`, `gte`, `lt`, `lte`, `in`, `is`, `like`, `ilike`, `startsWith`

### Pagination

Cursor-based pagination:

```bash
maton api '/twenty/rest/companies?limit=20&starting_after={endCursor}'
```

**Note:** `{endCursor}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `limit` - Max 60 (default: 60)
- `starting_after` - Next page cursor
- `ending_before` - Previous page cursor

### Ordering

```bash
maton api '/twenty/rest/companies?order_by=createdAt[DescNullsLast]'
```

Directions: `AscNullsFirst`, `AscNullsLast`, `DescNullsFirst`, `DescNullsLast`

### Notes

- All IDs are UUIDs
- Amount fields use micros (value × 1,000,000)
- Opportunity stages: SCREENING, MEETING, PROPOSAL, NEGOTIATION, WON, LOST
- Task statuses: TODO, IN_PROGRESS, DONE

### Resources

- [Twenty API Documentation](https://docs.twenty.com/developers/extend/api)
- [Twenty GitHub](https://github.com/twentyhq/twenty)
- [Maton CLI Manual](https://cli.maton.ai/manual)
