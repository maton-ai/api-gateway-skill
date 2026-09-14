# Pipedrive

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

> **Privacy — person and deal records are personal data about real people.** Persons carry names, email addresses, and phone numbers; activities and notes often add meeting context and private commentary. This is personal data under GDPR/CCPA, and the contacts themselves are third parties who gave their details to the user's company, not to an agent.
> - The sample values below (`John Doe`, `john@example.com`, `+1234567890`) are **placeholders**. Never send them to a live account, and never invent contact details to fill a required field — ask the user.
> - Retrieve only the records the task needs. Do not page through the full person or deal list to build a contact list, and do not enumerate a pipeline to browse.
> - Return the narrowest answer that satisfies the request; don't print full person records into output when the user asked one question.
> - **Never forward Pipedrive contact data to a third-party host** — not to a trigger destination, external webhook, spreadsheet service, or enrichment API — without explicit user approval for that specific transfer.
> - Confirm the exact person by name or email (not just a numeric ID) before any write, and never bulk-update or bulk-delete records without per-record approval.

**App name:** `pipedrive`
**Upstream base URL:** `api.pipedrive.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.pipedrive.com/api/v1/deals`
- Gateway: `https://api.maton.ai/pipedrive/api/v1/deals`

### Deals API

#### List Deals

```bash
maton api '/pipedrive/api/v1/deals'
```

**Query parameters:**
- `status` - Filter by status: `open`, `won`, `lost`, `deleted`, `all_not_deleted`
- `filter_id` - Filter ID to use
- `stage_id` - Filter by stage
- `user_id` - Filter by user
- `start` - Pagination start (default 0)
- `limit` - Items per page (default 100)
- `sort` - Sort field and order (e.g., `add_time DESC`)

**Example:**

```bash
maton api '/pipedrive/api/v1/deals?status=open&limit=50'
```

#### Get Deal

```bash
maton api '/pipedrive/api/v1/deals/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Deal

```bash
maton api -X POST '/pipedrive/api/v1/deals' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "New Enterprise Deal",
  "value": 50000,
  "currency": "USD",
  "person_id": 123,
  "org_id": 456,
  "stage_id": 1,
  "expected_close_date": "2025-06-30"
}
JSON
```

#### Update Deal

```bash
maton api -X PUT '/pipedrive/api/v1/deals/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Updated Deal Title",
  "value": 75000,
  "status": "won"
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Deal

```bash
maton api '/pipedrive/api/v1/deals/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Search Deals

```bash
maton api '/pipedrive/api/v1/deals/search?term=enterprise'
```

### Persons API

#### List Persons

```bash
maton api '/pipedrive/api/v1/persons'
```

**Query parameters:**
- `filter_id` - Filter ID
- `start` - Pagination start
- `limit` - Items per page
- `sort` - Sort field and order

#### Get Person

```bash
maton api '/pipedrive/api/v1/persons/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Person

```bash
maton api -X POST '/pipedrive/api/v1/persons' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "John Doe",
  "email": ["john@example.com"],
  "phone": ["+1234567890"],
  "org_id": 456,
  "visible_to": 3
}
JSON
```

#### Update Person

```bash
maton api -X PUT '/pipedrive/api/v1/persons/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "John Smith",
  "email": ["john.smith@example.com"]
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Person

```bash
maton api '/pipedrive/api/v1/persons/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Search Persons

```bash
maton api '/pipedrive/api/v1/persons/search?term=john'
```

### Organizations API

#### List Organizations

```bash
maton api '/pipedrive/api/v1/organizations'
```

#### Get Organization

```bash
maton api '/pipedrive/api/v1/organizations/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Organization

```bash
maton api -X POST '/pipedrive/api/v1/organizations' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Acme Corporation",
  "address": "123 Main St, City, Country",
  "visible_to": 3
}
JSON
```

#### Update Organization

```bash
maton api -X PUT '/pipedrive/api/v1/organizations/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Acme Corp International"
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Organization

```bash
maton api '/pipedrive/api/v1/organizations/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Activities API

#### List Activities

```bash
maton api '/pipedrive/api/v1/activities'
```

**Query parameters:**
- `type` - Activity type (e.g., `call`, `meeting`, `task`, `email`)
- `done` - Filter by completion (0 or 1)
- `user_id` - Filter by user
- `start_date` - Filter by start date
- `end_date` - Filter by end date

#### Get Activity

```bash
maton api '/pipedrive/api/v1/activities/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Activity

```bash
maton api -X POST '/pipedrive/api/v1/activities' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "subject": "Follow-up call",
  "type": "call",
  "due_date": "2025-03-15",
  "due_time": "14:00",
  "duration": "00:30",
  "deal_id": 789,
  "person_id": 123,
  "note": "Discuss contract terms"
}
JSON
```

#### Update Activity

```bash
maton api -X PUT '/pipedrive/api/v1/activities/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "done": 1,
  "note": "Completed - customer agreed to terms"
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Activity

```bash
maton api '/pipedrive/api/v1/activities/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Pipelines API

#### List Pipelines

```bash
maton api '/pipedrive/api/v1/pipelines'
```

#### Get Pipeline

```bash
maton api '/pipedrive/api/v1/pipelines/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Stages API

#### List Stages

```bash
maton api '/pipedrive/api/v1/stages'
```

**Query parameters:**
- `pipeline_id` - Filter by pipeline

#### Get Stage

```bash
maton api '/pipedrive/api/v1/stages/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Notes API

#### List Notes

```bash
maton api '/pipedrive/api/v1/notes'
```

**Query parameters:**
- `deal_id` - Filter by deal
- `person_id` - Filter by person
- `org_id` - Filter by organization

#### Create Note

```bash
maton api -X POST '/pipedrive/api/v1/notes' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "content": "Meeting notes: Discussed pricing and timeline",
  "deal_id": 789,
  "pinned_to_deal_flag": 1
}
JSON
```

### Users API

#### List Users

```bash
maton api '/pipedrive/api/v1/users'
```

#### Get Current User

```bash
maton api '/pipedrive/api/v1/users/me'
```

### Notes

- IDs are integers
- Email and phone fields accept arrays for multiple values
- `visible_to` values: 1 (owner only), 3 (entire company), 5 (owner's visibility group), 7 (entire company and visibility group)
- Deal status: `open`, `won`, `lost`, `deleted`
- Use `start` and `limit` for pagination
- Custom fields are supported via their API key (e.g., `abc123_custom_field`)

### Resources

- [Pipedrive API Overview](https://developers.pipedrive.com/docs/api/v1)
- [Deals](https://developers.pipedrive.com/docs/api/v1/Deals)
- [Persons](https://developers.pipedrive.com/docs/api/v1/Persons)
- [Organizations](https://developers.pipedrive.com/docs/api/v1/Organizations)
- [Activities](https://developers.pipedrive.com/docs/api/v1/Activities)
- [Pipelines](https://developers.pipedrive.com/docs/api/v1/Pipelines)
- [Stages](https://developers.pipedrive.com/docs/api/v1/Stages)
- [Notes](https://developers.pipedrive.com/docs/api/v1/Notes)
- [Maton CLI Manual](https://cli.maton.ai/manual)
