# Twenty CRM Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `twenty`
**Base URL proxied:** `api.twenty.com`

## API Path Pattern

```
/twenty/rest/{resource}
```

## Common Endpoints

### Companies

#### List Companies
```bash
maton api '/twenty/rest/companies?limit=20'
```

#### Get Company
```bash
maton api '/twenty/rest/companies/{id}'
```

#### Create Company
```bash
maton api -X POST '/twenty/rest/companies' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Company Name",
  "domainName": {"primaryLinkUrl": "https://company.com"},
  "employees": 100
}
EOF
```

#### Update Company
```bash
maton api -X PATCH '/twenty/rest/companies/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Updated Name"
}
EOF
```

#### Delete Company
```bash
maton api -X DELETE '/twenty/rest/companies/{id}'
```

### People

#### List People
```bash
maton api '/twenty/rest/people?limit=20'
```

#### Get Person
```bash
maton api '/twenty/rest/people/{id}'
```

#### Create Person
```bash
maton api -X POST '/twenty/rest/people' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": {"firstName": "John", "lastName": "Doe"},
  "emails": {"primaryEmail": "john@company.com"},
  "companyId": "{companyId}"
}
EOF
```

#### Update Person
```bash
maton api -X PATCH '/twenty/rest/people/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "jobTitle": "CEO"
}
EOF
```

### Opportunities

#### List Opportunities
```bash
maton api '/twenty/rest/opportunities?limit=20'
```

#### Create Opportunity
```bash
maton api -X POST '/twenty/rest/opportunities' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Deal Name",
  "amount": {"amountMicros": 50000000000, "currencyCode": "USD"},
  "stage": "SCREENING",
  "companyId": "{companyId}"
}
EOF
```

### Notes

#### List Notes
```bash
maton api '/twenty/rest/notes?limit=20'
```

#### Create Note
```bash
maton api -X POST '/twenty/rest/notes' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "Note Title",
  "body": "Note content"
}
EOF
```

### Tasks

#### List Tasks
```bash
maton api '/twenty/rest/tasks?limit=20'
```

#### Create Task
```bash
maton api -X POST '/twenty/rest/tasks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "Task Title",
  "status": "TODO",
  "dueAt": "2026-04-01T00:00:00.000Z"
}
EOF
```

### Workspace Members

#### List Workspace Members
```bash
maton api '/twenty/rest/workspaceMembers?limit=20'
```

## Filtering

```bash
maton api '/twenty/rest/companies?filter=employees[gte]:100'
maton api '/twenty/rest/opportunities?filter=stage[eq]:"MEETING"'
```

Comparators: `eq`, `neq`, `gt`, `gte`, `lt`, `lte`, `in`, `is`, `like`, `ilike`, `startsWith`

## Pagination

Cursor-based pagination:

```bash
maton api '/twenty/rest/companies?limit=20&starting_after={endCursor}'
```

Parameters:
- `limit` - Max 60 (default: 60)
- `starting_after` - Next page cursor
- `ending_before` - Previous page cursor

## Ordering

```bash
maton api '/twenty/rest/companies?order_by=createdAt[DescNullsLast]'
```

Directions: `AscNullsFirst`, `AscNullsLast`, `DescNullsFirst`, `DescNullsLast`

## Notes

- All IDs are UUIDs
- Amount fields use micros (value × 1,000,000)
- Opportunity stages: SCREENING, MEETING, PROPOSAL, NEGOTIATION, WON, LOST
- Task statuses: TODO, IN_PROGRESS, DONE

## Resources

- [Twenty API Documentation](https://docs.twenty.com/developers/extend/api)
- [Twenty GitHub](https://github.com/twentyhq/twenty)
