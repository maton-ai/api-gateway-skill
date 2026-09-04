# Clio Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `clio`
**Base URL proxied:** `app.clio.com`

> **Privacy — this is a legal practice management system.** Matters, contacts, notes, communications, and documents here are client data, commonly covered by attorney-client privilege, legal professional privilege, or equivalent confidentiality duties. Mishandling it can cause real legal harm to the firm and its clients.
> - Retrieve only the specific records the task requires. Do not bulk-export matters, contacts, or documents to "have context".
> - **Never forward Clio data to a third-party host** — not to a trigger destination, webhook, external API, document-conversion service, or any non-`api.maton.ai` endpoint. Privileged material must not leave the firm's systems through this skill.
> - Do not copy document contents, client identities, or matter details into summaries, logs, or files beyond what the user asked to see.
> - Treat matter and contact identifiers as confidential; they map to real clients.

## API Path Pattern

```
/clio/api/v4/{resource}
```

## Field Selection

By default, Clio returns minimal fields (`id`, `etag`). Always specify fields:

```bash
maton api '/clio/api/v4/matters?fields=id,display_number,description,status'
```

Nested resources use curly bracket syntax:

```bash
maton api '/clio/api/v4/activities?fields=id,type,matter{id,description}'
```

## Common Endpoints

### Matters

#### List Matters
```bash
maton api '/clio/api/v4/matters?fields=id,display_number,description,status'
```

#### Get Matter
```bash
maton api '/clio/api/v4/matters/{id}?fields=id,display_number,description,status,open_date'
```

#### Create Matter
```bash
maton api -X POST '/clio/api/v4/matters' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "description": "New Legal Matter",
    "status": "open",
    "client": {"id": 12345}
  }
}
EOF
```

#### Update Matter
```bash
maton api -X PATCH '/clio/api/v4/matters/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "description": "Updated Description"
  }
}
EOF
```

#### Delete Matter
```bash
maton api -X DELETE '/clio/api/v4/matters/{id}'
```

### Contacts

#### List Contacts
```bash
maton api '/clio/api/v4/contacts?fields=id,name,type,primary_email_address'
```

#### Get Contact
```bash
maton api '/clio/api/v4/contacts/{id}?fields=id,name,type,first_name,last_name'
```

#### Create Contact (Person)
```bash
maton api -X POST '/clio/api/v4/contacts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "type": "Person",
    "first_name": "John",
    "last_name": "Doe"
  }
}
EOF
```

#### Create Contact (Company)
```bash
maton api -X POST '/clio/api/v4/contacts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "type": "Company",
    "name": "Acme Corporation"
  }
}
EOF
```

#### Update Contact
```bash
maton api -X PATCH '/clio/api/v4/contacts/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "first_name": "Jane"
  }
}
EOF
```

#### Delete Contact
```bash
maton api -X DELETE '/clio/api/v4/contacts/{id}'
```

### Activities

#### List Activities
```bash
maton api '/clio/api/v4/activities?fields=id,type,date,quantity,matter{id,description}'
```

#### Get Activity
```bash
maton api '/clio/api/v4/activities/{id}?fields=id,type,date,quantity,note'
```

#### Create Activity
```bash
maton api -X POST '/clio/api/v4/activities' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "type": "TimeEntry",
    "date": "2026-02-11",
    "quantity": 3600,
    "matter": {"id": 12345}
  }
}
EOF
```

#### Update Activity
```bash
maton api -X PATCH '/clio/api/v4/activities/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "note": "Updated note"
  }
}
EOF
```

#### Delete Activity
```bash
maton api -X DELETE '/clio/api/v4/activities/{id}'
```

### Tasks

#### List Tasks
```bash
maton api '/clio/api/v4/tasks?fields=id,name,status,due_at,priority'
```

#### Get Task
```bash
maton api '/clio/api/v4/tasks/{id}?fields=id,name,description,status,due_at'
```

#### Create Task

Requires `assignee` with `id` and `type`:

```bash
maton api -X POST '/clio/api/v4/tasks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "name": "Review contract",
    "due_at": "2026-02-15T17:00:00Z",
    "priority": "Normal",
    "assignee": {"id": 12345, "type": "User"},
    "matter": {"id": 67890}
  }
}
EOF
```

#### Update Task
```bash
maton api -X PATCH '/clio/api/v4/tasks/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "status": "complete"
  }
}
EOF
```

#### Delete Task
```bash
maton api -X DELETE '/clio/api/v4/tasks/{id}'
```

### Calendar Entries

#### List Calendar Entries
```bash
maton api '/clio/api/v4/calendar_entries?fields=id,summary,start_at,end_at'
```

#### Get Calendar Entry
```bash
maton api '/clio/api/v4/calendar_entries/{id}?fields=id,summary,description,start_at,end_at'
```

#### Create Calendar Entry

Requires `calendar_owner` with `id` and `type`:

```bash
maton api -X POST '/clio/api/v4/calendar_entries' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "summary": "Client Meeting",
    "start_at": "2026-02-15T10:00:00Z",
    "end_at": "2026-02-15T11:00:00Z",
    "calendar_owner": {"id": 12345, "type": "User"}
  }
}
EOF
```

**Note:** Associating a matter during creation may return 404. Use PATCH to link matters after creation.

#### Update Calendar Entry
```bash
maton api -X PATCH '/clio/api/v4/calendar_entries/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "summary": "Updated Meeting"
  }
}
EOF
```

#### Delete Calendar Entry
```bash
maton api -X DELETE '/clio/api/v4/calendar_entries/{id}'
```

### Documents

#### List Documents
```bash
maton api '/clio/api/v4/documents?fields=id,name,content_type,size'
```

#### Get Document
```bash
maton api '/clio/api/v4/documents/{id}?fields=id,name,content_type,size,created_at'
```

#### Download Document

> **Privileged client material.** This returns the full contents of a legal document. Confirm the specific document and the reason with the user first. Do not save it outside the working directory the user specified, do not include its contents in output beyond what was asked, and never upload or forward it to any third-party host (including document-processing or conversion APIs).

```bash
maton api '/clio/api/v4/documents/{id}/download'
```

### Users

#### Get Current User
```bash
maton api '/clio/api/v4/users/who_am_i?fields=id,name,email,enabled'
```

#### List Users
```bash
maton api '/clio/api/v4/users?fields=id,name,email,enabled'
```

### Bills

#### List Bills
```bash
maton api '/clio/api/v4/bills?fields=id,number,issued_at,due_at,total,balance,state'
```

#### Get Bill
```bash
maton api '/clio/api/v4/bills/{id}?fields=id,number,total,balance,state'
```

## Pagination

Clio uses cursor-based pagination:

```bash
maton api '/clio/api/v4/matters?fields=id,description&limit=50'
```

Response includes pagination in `meta`:

```json
{
  "data": [...],
  "meta": {
    "paging": {
      "next": "https://app.clio.com/api/v4/matters?page_token=xyz123"
    }
  }
}
```

Use `page_token` for next page:

```bash
maton api '/clio/api/v4/matters?page_token=xyz123'
```

## Notes

- Always specify `fields` parameter - defaults are minimal (`id`, `etag` only)
- Nested resources use curly brackets: `matter{id,description}`
- Only one level of nesting supported
- Contact types: `Person` or `Company`
- Task assignees require both `id` and `type` ("User" or "Contact")
- Calendar entries require `calendar_owner` with `id` and `type`; linking matters during creation may fail - use PATCH after creation
- Activity quantity is in seconds (3600 = 1 hour)
- Rate limit: 50 requests/minute during peak hours
- Contact limits: max 20 emails, phones, and addresses each
- Activities, Documents, and Bills endpoints require additional OAuth scopes

## Resources

- [Clio API Documentation](https://docs.developers.clio.com/api-reference/)
- [Clio Fields Guide](https://docs.developers.clio.com/api-docs/clio-manage/fields/)
- [Clio Rate Limits](https://docs.developers.clio.com/api-docs/clio-manage/rate-limits/)
