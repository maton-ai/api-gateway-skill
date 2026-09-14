# Clio

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

> **Privacy — this is a legal practice management system.** Matters, contacts, notes, communications, and documents here are client data, commonly covered by attorney-client privilege, legal professional privilege, or equivalent confidentiality duties. Mishandling it can cause real legal harm to the firm and its clients.
> - Retrieve only the specific records the task requires. Do not bulk-export matters, contacts, or documents to "have context".
> - **Never forward Clio data to a third-party host** — not to a trigger destination, webhook, external API, document-conversion service, or any non-`api.maton.ai` endpoint. Privileged material must not leave the firm's systems through this skill.
> - Do not copy document contents, client identities, or matter details into summaries, logs, or files beyond what the user asked to see.
> - Treat matter and contact identifiers as confidential; they map to real clients.

**App name:** `clio`
**Upstream base URL:** `app.clio.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://app.clio.com/api/v4/matters`
- Gateway: `https://api.maton.ai/clio/api/v4/matters`

### Matters API

#### List Matters

```bash
maton api '/clio/api/v4/matters?fields=id,display_number,description,status,client_reference'
```

#### Get Matter

```bash
maton api '/clio/api/v4/matters/{id}?fields=id,display_number,description,status,open_date,close_date'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Matter

```bash
maton api -X POST '/clio/api/v4/matters' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "description": "New Legal Matter",
    "status": "open",
    "client": {"id": 12345}
  }
}
JSON
```

#### Update Matter

```bash
maton api -X PATCH '/clio/api/v4/matters/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "description": "Updated Matter Description",
    "status": "closed"
  }
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Matter

```bash
maton api '/clio/api/v4/matters/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Contacts API

#### List Contacts

```bash
maton api '/clio/api/v4/contacts?fields=id,name,type,primary_email_address,primary_phone_number'
```

#### Get Contact

```bash
maton api '/clio/api/v4/contacts/{id}?fields=id,name,type,first_name,last_name,company'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Contact (Person)

```bash
maton api -X POST '/clio/api/v4/contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "type": "Person",
    "first_name": "John",
    "last_name": "Doe",
    "email_addresses": [
      {"name": "Work", "address": "john@example.com", "default_email": true}
    ]
  }
}
JSON
```

#### Create Contact (Company)

```bash
maton api -X POST '/clio/api/v4/contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "type": "Company",
    "name": "Acme Corporation"
  }
}
JSON
```

#### Update Contact

```bash
maton api -X PATCH '/clio/api/v4/contacts/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "first_name": "Jane"
  }
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Contact

```bash
maton api '/clio/api/v4/contacts/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Activities API

#### List Activities

```bash
maton api '/clio/api/v4/activities?fields=id,type,date,quantity,matter{id,description}'
```

#### Get Activity

```bash
maton api '/clio/api/v4/activities/{id}?fields=id,type,date,quantity,note'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Activity

```bash
maton api -X POST '/clio/api/v4/activities' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "type": "TimeEntry",
    "date": "2026-02-11",
    "quantity": 3600,
    "matter": {"id": 12345},
    "note": "Legal research"
  }
}
JSON
```

#### Update Activity

```bash
maton api -X PATCH '/clio/api/v4/activities/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "note": "Updated note"
  }
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Activity

```bash
maton api '/clio/api/v4/activities/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Tasks API

#### List Tasks

```bash
maton api '/clio/api/v4/tasks?fields=id,name,status,due_at,priority,matter{id,description}'
```

#### Get Task

```bash
maton api '/clio/api/v4/tasks/{id}?fields=id,name,description,status,due_at,priority'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Task

Requires `assignee` with both `id` and `type` ("User" or "Contact"):

```bash
maton api -X POST '/clio/api/v4/tasks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "name": "Review contract",
    "due_at": "2026-02-15T17:00:00Z",
    "priority": "Normal",
    "assignee": {"id": 12345, "type": "User"},
    "matter": {"id": 67890}
  }
}
JSON
```

#### Update Task

```bash
maton api -X PATCH '/clio/api/v4/tasks/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "status": "complete"
  }
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Task

```bash
maton api '/clio/api/v4/tasks/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Calendar Entries API

#### List Calendar Entries

```bash
maton api '/clio/api/v4/calendar_entries?fields=id,summary,start_at,end_at,matter{id,description}'
```

#### Get Calendar Entry

```bash
maton api '/clio/api/v4/calendar_entries/{id}?fields=id,summary,description,start_at,end_at,location'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Calendar Entry

Requires `calendar_owner` with `id` and `type`:

```bash
maton api -X POST '/clio/api/v4/calendar_entries' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "summary": "Client Meeting",
    "start_at": "2026-02-15T10:00:00Z",
    "end_at": "2026-02-15T11:00:00Z",
    "calendar_owner": {"id": 12345, "type": "User"}
  }
}
JSON
```

**Note:** Associating a matter with a calendar entry during creation may return a 404 error. To link a matter, update the calendar entry after creation using PATCH.

#### Update Calendar Entry

```bash
maton api -X PATCH '/clio/api/v4/calendar_entries/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "summary": "Updated Meeting Title"
  }
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Calendar Entry

```bash
maton api '/clio/api/v4/calendar_entries/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Documents API

#### List Documents

```bash
maton api '/clio/api/v4/documents?fields=id,name,content_type,size,matter{id,description}'
```

#### Get Document

```bash
maton api '/clio/api/v4/documents/{id}?fields=id,name,content_type,size,created_at'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Download Document

> **Privileged client material.** This returns the full contents of a legal document. Confirm the specific document and the reason with the user first. Do not save it outside the working directory the user specified, do not include its contents in output beyond what was asked, and never upload or forward it to any third-party host (including document-processing or conversion APIs).

```bash
maton api '/clio/api/v4/documents/{id}/download'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Users API

#### Get Current User

```bash
maton api '/clio/api/v4/users/who_am_i?fields=id,name,email,enabled'
```

#### List Users

```bash
maton api '/clio/api/v4/users?fields=id,name,email,enabled,rate'
```

### Bills API

#### List Bills

```bash
maton api '/clio/api/v4/bills?fields=id,number,issued_at,due_at,total,balance,state'
```

#### Get Bill

```bash
maton api '/clio/api/v4/bills/{id}?fields=id,number,issued_at,due_at,total,balance,state'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Pagination

Clio uses cursor-based pagination. Response includes pagination metadata:

```bash
maton api '/clio/api/v4/matters?fields=id,description&limit=50'
```

Response includes pagination info in the `meta` object:

```json
{
  "data": [...],
  "meta": {
    "paging": {
      "next": "https://app.clio.com/api/v4/matters?page_token=xyz123"
    },
    "records": 50
  }
}
```

Use the `page_token` parameter to fetch the next page:

```bash
maton api '/clio/api/v4/matters?fields=id,description&page_token=xyz123'
```

### Field Selection

By default, Clio returns minimal fields (`id`, `etag`). Use the `fields` parameter to request specific fields:

```bash
maton api '/clio/api/v4/matters?fields=id,display_number,description,status'
```

For nested resources, use curly bracket syntax:

```bash
maton api '/clio/api/v4/activities?fields=id,type,matter{id,description}'
```

### Notes

- Field selection is important - default responses only include `id` and `etag`
- Nested resources use curly bracket syntax: `matter{id,description}`
- Only one level of nesting is supported
- Contact types: `Person` or `Company`
- Task assignees require both `id` and `type` ("User" or "Contact")
- Calendar entries require `calendar_owner` with `id` and `type`; associating a matter during creation may fail - use PATCH to link matters after creation
- Activity quantity is in seconds (3600 = 1 hour)
- Contact records limited to 20 email addresses, phone numbers, and addresses each
- Activities, Documents, and Bills endpoints require additional OAuth scopes beyond the basic integration

### Resources

- [Clio API Documentation](https://docs.developers.clio.com/api-reference/)
- [Fields Guide](https://docs.developers.clio.com/api-docs/clio-manage/fields/)
- [Rate Limits](https://docs.developers.clio.com/api-docs/clio-manage/rate-limits/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
