# SendGrid

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `sendgrid`
**Upstream base URL:** `api.sendgrid.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.sendgrid.com/v3/mail/send`
- Gateway: `https://api.maton.ai/sendgrid/v3/mail/send`

All SendGrid API endpoints follow this pattern:

```
/sendgrid/v3/{resource}
```

### Mail API

#### Send Email

```bash
maton api -X POST '/sendgrid/v3/mail/send' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "personalizations": [
    {
      "to": [{"email": "recipient@example.com", "name": "Recipient"}],
      "subject": "Hello from SendGrid"
    }
  ],
  "from": {"email": "sender@example.com", "name": "Sender"},
  "content": [
    {
      "type": "text/plain",
      "value": "This is a test email."
    }
  ]
}
JSON
```

**With HTML content:**
```bash
maton api -X POST '/sendgrid/v3/mail/send' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "personalizations": [
    {
      "to": [{"email": "recipient@example.com"}],
      "subject": "HTML Email"
    }
  ],
  "from": {"email": "sender@example.com"},
  "content": [
    {
      "type": "text/html",
      "value": "<h1>Hello</h1><p>This is an HTML email.</p>"
    }
  ]
}
JSON
```

**With template:**
```bash
maton api -X POST '/sendgrid/v3/mail/send' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "personalizations": [
    {
      "to": [{"email": "recipient@example.com"}],
      "dynamic_template_data": {
        "first_name": "John",
        "order_id": "12345"
      }
    }
  ],
  "from": {"email": "sender@example.com"},
  "template_id": "d-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
}
JSON
```

### User Profile API

#### Get User Profile

```bash
maton api '/sendgrid/v3/user/profile'
```

**Response:**
```json
{
  "type": "user",
  "userid": 59796657
}
```

#### Get Account Details

```bash
maton api '/sendgrid/v3/user/account'
```

### Marketing Contacts API

#### List Contacts

```bash
maton api '/sendgrid/v3/marketing/contacts'
```

**Response:**
```json
{
  "result": [],
  "contact_count": 0,
  "_metadata": {
    "self": ".../v3/marketing/contacts"
  }
}
```

#### Search Contacts

```bash
maton api -X POST '/sendgrid/v3/marketing/contacts/search' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "email LIKE '%@example.com%'"
}
JSON
```

#### Add/Update Contacts

```bash
maton api -X PUT '/sendgrid/v3/marketing/contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contacts": [
    {
      "email": "contact@example.com",
      "first_name": "John",
      "last_name": "Doe"
    }
  ]
}
JSON
```

**Response:**
```json
{
  "job_id": "2387e363-4104-4225-8960-4a5758492351"
}
```

**Note:** Contact operations are asynchronous. Use the job status endpoint to check progress.

#### Get Import Job Status

```bash
maton api '/sendgrid/v3/marketing/contacts/imports/{job_id}'
```

**Note:** `{job_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": "2387e363-4104-4225-8960-4a5758492351",
  "status": "pending",
  "job_type": "upsert_contacts",
  "results": {
    "requested_count": 1,
    "created_count": 1
  },
  "started_at": "2026-02-11T11:00:14Z"
}
```

#### Delete Contacts

```bash
maton api '/sendgrid/v3/marketing/contacts?ids=contact_id_1,contact_id_2' -X DELETE
```

#### Get Contact by ID

```bash
maton api '/sendgrid/v3/marketing/contacts/{contact_id}'
```

**Note:** `{contact_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Contact by Email

```bash
maton api -X POST '/sendgrid/v3/marketing/contacts/search/emails' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "emails": ["contact@example.com"]
}
JSON
```

### Marketing Lists API

#### List All Lists

```bash
maton api '/sendgrid/v3/marketing/lists'
```

**Response:**
```json
{
  "result": [],
  "_metadata": {
    "self": ".../v3/marketing/lists?page_size=100&page_token="
  }
}
```

#### Create List

```bash
maton api -X POST '/sendgrid/v3/marketing/lists' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "My Contact List"
}
JSON
```

**Response:**
```json
{
  "name": "My Contact List",
  "id": "b050f139-4231-47c8-bf32-94ad76376d3b",
  "contact_count": 0,
  "_metadata": {
    "self": ".../v3/marketing/lists/b050f139-4231-47c8-bf32-94ad76376d3b"
  }
}
```

#### Get List by ID

```bash
maton api '/sendgrid/v3/marketing/lists/{list_id}'
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update List

```bash
maton api -X PATCH '/sendgrid/v3/marketing/lists/{list_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated List Name"
}
JSON
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete List

```bash
maton api '/sendgrid/v3/marketing/lists/{list_id}' -X DELETE
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

#### Add Contacts to List

```bash
maton api -X PUT '/sendgrid/v3/marketing/contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "list_ids": ["list_id"],
  "contacts": [
    {"email": "contact@example.com"}
  ]
}
JSON
```

### Segments API

#### List Segments

```bash
maton api '/sendgrid/v3/marketing/segments'
```

#### Create Segment

```bash
maton api -X POST '/sendgrid/v3/marketing/segments' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Active Users",
  "query_dsl": "email_clicks > 0"
}
JSON
```

#### Get Segment by ID

```bash
maton api '/sendgrid/v3/marketing/segments/{segment_id}'
```

**Note:** `{segment_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Segment

```bash
maton api '/sendgrid/v3/marketing/segments/{segment_id}' -X DELETE
```

**Note:** `{segment_id}` is a placeholder. Replace it with a real value before sending the request.

### Templates API

#### List Templates

```bash
maton api '/sendgrid/v3/templates'
```

**With generation filter:**
```bash
maton api '/sendgrid/v3/templates?generations=dynamic'
```

#### Create Template

```bash
maton api -X POST '/sendgrid/v3/templates' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "My Template",
  "generation": "dynamic"
}
JSON
```

**Response:**
```json
{
  "id": "d-ffcdb43ed8a04beba48a702e1717ddb5",
  "name": "My Template",
  "generation": "dynamic",
  "updated_at": "2026-02-11 11:00:20",
  "versions": []
}
```

#### Get Template by ID

```bash
maton api '/sendgrid/v3/templates/{template_id}'
```

**Note:** `{template_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Template

```bash
maton api -X PATCH '/sendgrid/v3/templates/{template_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Template Name"
}
JSON
```

**Note:** `{template_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Template

```bash
maton api '/sendgrid/v3/templates/{template_id}' -X DELETE
```

**Note:** `{template_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Template Version

```bash
maton api -X POST '/sendgrid/v3/templates/{template_id}/versions' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Version 1",
  "subject": "{{subject}}",
  "html_content": "<html><body><h1>Hello {{name}}</h1></body></html>",
  "active": 1
}
JSON
```

**Note:** `{template_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": "54230a99-1e89-4edf-821d-d4925b40c64b",
  "template_id": "d-ffcdb43ed8a04beba48a702e1717ddb5",
  "active": 1,
  "name": "Version 1",
  "html_content": "<html><body><h1>Hello {{name}}</h1></body></html>",
  "plain_content": "Hello {{name}}",
  "generate_plain_content": true,
  "subject": "{{subject}}",
  "editor": "code",
  "thumbnail_url": "//..."
}
```

### Senders API

#### List Senders

```bash
maton api '/sendgrid/v3/senders'
```

#### Create Sender

```bash
maton api -X POST '/sendgrid/v3/senders' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "nickname": "My Sender",
  "from": {"email": "sender@example.com", "name": "Sender Name"},
  "reply_to": {"email": "reply@example.com", "name": "Reply To"},
  "address": "123 Main St",
  "city": "San Francisco",
  "country": "USA"
}
JSON
```

**Response:**
```json
{
  "id": 8513177,
  "nickname": "My Sender",
  "from": {"email": "sender@example.com", "name": "Sender Name"},
  "reply_to": {"email": "reply@example.com", "name": "Reply To"},
  "address": "123 Main St",
  "city": "San Francisco",
  "country": "USA",
  "verified": {"status": false, "reason": null},
  "updated_at": 1770786031,
  "created_at": 1770786031,
  "locked": false
}
```

**Note:** Sender verification is required before use. Check `verified.status`.

#### Get Sender by ID

```bash
maton api '/sendgrid/v3/senders/{sender_id}'
```

**Note:** `{sender_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Sender

```bash
maton api -X PATCH '/sendgrid/v3/senders/{sender_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "nickname": "Updated Sender Name"
}
JSON
```

**Note:** `{sender_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Sender

```bash
maton api '/sendgrid/v3/senders/{sender_id}' -X DELETE
```

**Note:** `{sender_id}` is a placeholder. Replace it with a real value before sending the request.

### Suppressions API

#### Bounces

```bash
# List bounces
maton api '/sendgrid/v3/suppression/bounces'

# Get bounce by email
maton api '/sendgrid/v3/suppression/bounces/{email}'

# Delete bounces
maton api '/sendgrid/v3/suppression/bounces' -X DELETE -H 'Content-Type: application/json' --input - <<'JSON'
{
  "emails": ["bounce@example.com"]
}
JSON
```

**Note:** `{email}` is a placeholder. Replace it with a real value before sending the request.

#### Blocks

```bash
# List blocks
maton api '/sendgrid/v3/suppression/blocks'

# Get block by email
maton api '/sendgrid/v3/suppression/blocks/{email}'

# Delete blocks
maton api '/sendgrid/v3/suppression/blocks' -X DELETE -H 'Content-Type: application/json' --input - <<'JSON'
{
  "emails": ["blocked@example.com"]
}
JSON
```

**Note:** `{email}` is a placeholder. Replace it with a real value before sending the request.

#### Invalid Emails

```bash
# List invalid emails
maton api '/sendgrid/v3/suppression/invalid_emails'

# Delete invalid emails
maton api '/sendgrid/v3/suppression/invalid_emails' -X DELETE -H 'Content-Type: application/json' --input - <<'JSON'
{
  "emails": ["invalid@example.com"]
}
JSON
```

#### Spam Reports

```bash
# List spam reports
maton api '/sendgrid/v3/suppression/spam_reports'

# Delete spam reports
maton api '/sendgrid/v3/suppression/spam_reports' -X DELETE -H 'Content-Type: application/json' --input - <<'JSON'
{
  "emails": ["spam@example.com"]
}
JSON
```

#### Global Unsubscribes

```bash
# List global unsubscribes
maton api '/sendgrid/v3/suppression/unsubscribes'

# Add to global unsubscribes
maton api -X POST '/sendgrid/v3/asm/suppressions/global' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "recipient_emails": ["unsubscribe@example.com"]
}
JSON
```

### Unsubscribe Groups API

#### List Groups

```bash
maton api '/sendgrid/v3/asm/groups'
```

#### Create Group

```bash
maton api -X POST '/sendgrid/v3/asm/groups' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Weekly Newsletter",
  "description": "Weekly newsletter updates"
}
JSON
```

**Response:**
```json
{
  "name": "Weekly Newsletter",
  "id": 122741,
  "description": "Weekly newsletter updates",
  "is_default": false
}
```

#### Get Group by ID

```bash
maton api '/sendgrid/v3/asm/groups/{group_id}'
```

**Note:** `{group_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Group

```bash
maton api -X PATCH '/sendgrid/v3/asm/groups/{group_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Group Name"
}
JSON
```

**Note:** `{group_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Group

```bash
maton api '/sendgrid/v3/asm/groups/{group_id}' -X DELETE
```

**Note:** `{group_id}` is a placeholder. Replace it with a real value before sending the request.

#### Add Suppressions to Group

```bash
maton api -X POST '/sendgrid/v3/asm/groups/{group_id}/suppressions' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "recipient_emails": ["user@example.com"]
}
JSON
```

**Note:** `{group_id}` is a placeholder. Replace it with a real value before sending the request.

#### List Suppressions in Group

```bash
maton api '/sendgrid/v3/asm/groups/{group_id}/suppressions'
```

**Note:** `{group_id}` is a placeholder. Replace it with a real value before sending the request.

### Statistics API

#### Get Global Stats

```bash
maton api '/sendgrid/v3/stats?start_date=2026-02-01'
```

**With end date:**
```bash
maton api '/sendgrid/v3/stats?start_date=2026-02-01&end_date=2026-02-28'
```

**Response:**
```json
[
  {
    "date": "2026-02-01",
    "stats": [
      {
        "metrics": {
          "blocks": 0,
          "bounce_drops": 0,
          "bounces": 0,
          "clicks": 0,
          "deferred": 0,
          "delivered": 0,
          "invalid_emails": 0,
          "opens": 0,
          "processed": 0,
          "requests": 0,
          "spam_report_drops": 0,
          "spam_reports": 0,
          "unique_clicks": 0,
          "unique_opens": 0,
          "unsubscribe_drops": 0,
          "unsubscribes": 0
        }
      }
    ]
  }
]
```

#### Category Stats

```bash
maton api '/sendgrid/v3/categories/stats?start_date=2026-02-01&categories=category1,category2'
```

#### Mailbox Provider Stats

```bash
maton api '/sendgrid/v3/mailbox_providers/stats?start_date=2026-02-01'
```

#### Browser Stats

```bash
maton api '/sendgrid/v3/browsers/stats?start_date=2026-02-01'
```

### API Keys

> **Credential management.** API key operations create, modify, or delete long-lived SendGrid credentials that persist independently of the Maton OAuth session. A created key can be used outside this integration. Only invoke when the user explicitly requests API key management. Never log or display created key values.

#### List API Keys

```bash
maton api '/sendgrid/v3/api_keys'
```

**Response:**
```json
{
  "result": [
    {
      "name": "MatonTest",
      "api_key_id": "WJBgv5EKR8y0nn2F8Qfk5w"
    }
  ]
}
```

#### Create API Key

```bash
maton api -X POST '/sendgrid/v3/api_keys' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New API Key",
  "scopes": ["mail.send", "alerts.read"]
}
JSON
```

#### Get API Key by ID

```bash
maton api '/sendgrid/v3/api_keys/{api_key_id}'
```

**Note:** `{api_key_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update API Key

```bash
maton api -X PATCH '/sendgrid/v3/api_keys/{api_key_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Key Name"
}
JSON
```

**Note:** `{api_key_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete API Key

```bash
maton api '/sendgrid/v3/api_keys/{api_key_id}' -X DELETE
```

**Note:** `{api_key_id}` is a placeholder. Replace it with a real value before sending the request.

### Pagination

SendGrid uses token-based pagination for marketing endpoints:

```bash
maton api '/sendgrid/v3/marketing/lists?page_size=100&page_token={token}'
```

**Note:** `{token}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "result": [...],
  "_metadata": {
    "self": ".../v3/marketing/lists?page_size=100&page_token=",
    "next": ".../v3/marketing/lists?page_size=100&page_token=abc123"
  }
}
```

For suppression endpoints, use `limit` and `offset`:

```bash
maton api '/sendgrid/v3/suppression/bounces?limit=100&offset=0'
```

### Notes

- All requests use JSON content type
- Dates are in YYYY-MM-DD format
- Template IDs for dynamic templates start with `d-`
- Mail send returns 202 Accepted on success (not 200)
- Marketing contact operations are asynchronous - use job status endpoints
- Suppression endpoints support date filtering with `start_time` and `end_time` (Unix timestamps)

### Resources

- [SendGrid API Documentation](https://www.twilio.com/docs/sendgrid/api-reference)
- [Mail Send API](https://www.twilio.com/docs/sendgrid/api-reference/mail-send)
- [Maton CLI Manual](https://cli.maton.ai/manual)
