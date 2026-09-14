# Resend

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `resend`
**Upstream base URL:** `api.resend.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.resend.com/emails`
- Gateway: `https://api.maton.ai/resend/emails`

### Emails API

#### Send Email

```bash
maton api -X POST '/resend/emails'
```

**Example:**

```bash
maton api -X POST '/resend/emails' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "from": "you@yourdomain.com",
  "to": [
    "recipient@example.com"
  ],
  "subject": "Hello from Resend",
  "html": "<p>Welcome to our service!</p>"
}
JSON
```

**Request body:**
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `from` | string | Yes | Sender email (must be from verified domain) |
| `to` | string[] | Yes | Recipient email addresses |
| `subject` | string | Yes | Email subject |
| `html` | string | No | HTML content |
| `text` | string | No | Plain text content |
| `cc` | string[] | No | CC recipients |
| `bcc` | string[] | No | BCC recipients |
| `reply_to` | string[] | No | Reply-to addresses |
| `attachments` | object[] | No | File attachments |
| `tags` | object[] | No | Email tags for tracking |
| `scheduled_at` | string | No | ISO 8601 datetime for scheduled send |

**Response:**
```json
{
  "id": "a52ac168-338f-4fbc-9354-e6049b193d99"
}
```

#### Send Batch Emails

```bash
maton api -X POST '/resend/emails/batch'
```

**Example:**

```bash
maton api -X POST '/resend/emails/batch' -H 'Content-Type: application/json' --input - <<'JSON'
[
  {
    "from": "you@yourdomain.com",
    "to": [
      "a@example.com"
    ],
    "subject": "Email 1",
    "text": "Content 1"
  },
  {
    "from": "you@yourdomain.com",
    "to": [
      "b@example.com"
    ],
    "subject": "Email 2",
    "text": "Content 2"
  }
]
JSON
```

#### List Emails

```bash
maton api '/resend/emails'
```

**Response:**
```json
{
  "data": [
    {
      "id": "a52ac168-338f-4fbc-9354-e6049b193d99",
      "from": "you@yourdomain.com",
      "to": ["recipient@example.com"],
      "subject": "Hello from Resend",
      "created_at": "2026-03-13T10:00:00.000Z"
    }
  ]
}
```

#### Get Email

```bash
maton api '/resend/emails/{email_id}'
```

**Note:** `{email_id}` is a placeholder. Replace it with a real value before sending the request.

#### Cancel Email

```bash
maton api -X POST '/resend/emails/{email_id}/cancel'
```

**Note:** `{email_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Email

```bash
maton api -X PATCH '/resend/emails/{email_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "scheduled_at": "2026-10-01T09:00:00.000Z"
}
JSON
```

**Note:** `{email_id}` is a placeholder. Replace it with a real value before sending the request.

#### Cancel Scheduled Email

```bash
maton api '/resend/emails/{email_id}' -X DELETE
```

**Note:** `{email_id}` is a placeholder. Replace it with a real value before sending the request.

### Domains API

#### List Domains

```bash
maton api '/resend/domains'
```

**Response:**
```json
{
  "data": [
    {
      "id": "5eb93a2e-e849-40a1-81b7-ed0fb574ddd8",
      "name": "yourdomain.com",
      "status": "verified",
      "created_at": "2026-03-13T10:00:00.000Z"
    }
  ]
}
```

#### Create Domain

```bash
maton api -X POST '/resend/domains'
```

**Example:**

```bash
maton api -X POST '/resend/domains' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "yourdomain.com"
}
JSON
```

**Response:**
```json
{
  "id": "5eb93a2e-e849-40a1-81b7-ed0fb574ddd8",
  "name": "yourdomain.com",
  "status": "pending",
  "records": [
    {"type": "MX", "name": "...", "value": "..."},
    {"type": "TXT", "name": "...", "value": "..."}
  ]
}
```

#### Get Domain

```bash
maton api '/resend/domains/{domain_id}'
```

**Note:** `{domain_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Domain

```bash
maton api -X PATCH '/resend/domains/{domain_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "open_tracking": true,
  "click_tracking": false
}
JSON
```

**Note:** `{domain_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Domain

```bash
maton api '/resend/domains/{domain_id}' -X DELETE
```

**Note:** `{domain_id}` is a placeholder. Replace it with a real value before sending the request.

#### Verify Domain

```bash
maton api -X POST '/resend/domains/{domain_id}/verify'
```

**Note:** `{domain_id}` is a placeholder. Replace it with a real value before sending the request.

### Audiences API

#### List Audiences

```bash
maton api '/resend/audiences'
```

#### Create Audience

```bash
maton api -X POST '/resend/audiences' -H 'Content-Type: application/json' --input - <<'JSON'
{"name": "Newsletter Subscribers"}
JSON
```

#### Get Audience

```bash
maton api '/resend/audiences/{audience_id}'
```

**Note:** `{audience_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Audience

```bash
maton api '/resend/audiences/{audience_id}' -X DELETE
```

**Note:** `{audience_id}` is a placeholder. Replace it with a real value before sending the request.

### Contacts API

Contacts always belong to an audience, so every contact path is scoped by
`{audience_id}`.

#### List Contacts

```bash
maton api '/resend/audiences/{audience_id}/contacts'
```

**Note:** `{audience_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Contact

```bash
maton api -X POST '/resend/audiences/{audience_id}/contacts'
```

**Note:** `{audience_id}` is a placeholder. Replace it with a real value before sending the request.

**Example:**

```bash
maton api -X POST '/resend/audiences/{audience_id}/contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "email": "contact@example.com",
  "first_name": "John",
  "last_name": "Doe"
}
JSON
```

**Note:** `{audience_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": "3cdc4bbb-0c79-46e5-be2a-48a89c29203d"
}
```

#### Get Contact

```bash
maton api '/resend/audiences/{audience_id}/contacts/{contact_id}'
```

**Note:** `{audience_id}` and `{contact_id}` are placeholders. Replace each of them with real values before sending the request.

#### Update Contact

```bash
maton api -X PATCH '/resend/audiences/{audience_id}/contacts/{contact_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "first_name": "Ada",
  "unsubscribed": false
}
JSON
```

**Note:** `{audience_id}` and `{contact_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Contact

```bash
maton api '/resend/audiences/{audience_id}/contacts/{contact_id}' -X DELETE
```

**Note:** `{audience_id}` and `{contact_id}` are placeholders. Replace each of them with real values before sending the request.

### Templates API

#### List Templates

```bash
maton api '/resend/templates'
```

#### Create Template

```bash
maton api -X POST '/resend/templates'
```

**Example:**

```bash
maton api -X POST '/resend/templates' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Welcome Email",
  "subject": "Welcome to our service!",
  "html": "<h1>Welcome!</h1><p>Thanks for signing up.</p>"
}
JSON
```

**Response:**
```json
{
  "id": "9b84737c-8a80-448a-aca1-c6e1fddd0f23"
}
```

#### Get Template

```bash
maton api '/resend/templates/{template_id}'
```

**Note:** `{template_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Template

```bash
maton api -X PATCH '/resend/templates/{template_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Template Name",
  "html": "<p>Updated content</p>"
}
JSON
```

**Note:** `{template_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Template

```bash
maton api '/resend/templates/{template_id}' -X DELETE
```

**Note:** `{template_id}` is a placeholder. Replace it with a real value before sending the request.

#### Publish Template

```bash
maton api -X POST '/resend/templates/{template_id}/publish'
```

**Note:** `{template_id}` is a placeholder. Replace it with a real value before sending the request.

#### Duplicate Template

```bash
maton api -X POST '/resend/templates/{template_id}/duplicate'
```

**Note:** `{template_id}` is a placeholder. Replace it with a real value before sending the request.

### Segments API

Create audience segments for targeting.

#### List Segments

```bash
maton api '/resend/segments'
```

#### Create Segment

```bash
maton api -X POST '/resend/segments'
```

**Example:**

```bash
maton api -X POST '/resend/segments' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Active Users",
  "filter": {
    "and": [
      {
        "field": "email",
        "operator": "contains",
        "value": "@"
      }
    ]
  }
}
JSON
```

#### Get Segment

```bash
maton api '/resend/segments/{segment_id}'
```

**Note:** `{segment_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Segment

```bash
maton api '/resend/segments/{segment_id}' -X DELETE
```

**Note:** `{segment_id}` is a placeholder. Replace it with a real value before sending the request.

### Broadcasts API

Send emails to segments.

#### List Broadcasts

```bash
maton api '/resend/broadcasts'
```

#### Create Broadcast

```bash
maton api -X POST '/resend/broadcasts'
```

**Example:**

```bash
maton api -X POST '/resend/broadcasts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Weekly Newsletter",
  "from": "newsletter@yourdomain.com",
  "subject": "This Week's Update",
  "html": "<h1>Weekly Update</h1><p>Here's what happened...</p>",
  "segment_id": "segment-uuid"
}
JSON
```

#### Get Broadcast

```bash
maton api '/resend/broadcasts/{broadcast_id}'
```

**Note:** `{broadcast_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Broadcast

```bash
maton api -X PATCH '/resend/broadcasts/{broadcast_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Broadcast Name",
  "subject": "Updated subject"
}
JSON
```

**Note:** `{broadcast_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Broadcast

```bash
maton api '/resend/broadcasts/{broadcast_id}' -X DELETE
```

**Note:** `{broadcast_id}` is a placeholder. Replace it with a real value before sending the request.

#### Send Broadcast

```bash
maton api -X POST '/resend/broadcasts/{broadcast_id}/send'
```

**Note:** `{broadcast_id}` is a placeholder. Replace it with a real value before sending the request.

### Webhooks API

Configure event notifications.

#### List Webhooks

```bash
maton api '/resend/webhooks'
```

#### Create Webhook

```bash
maton api -X POST '/resend/webhooks'
```

**Example:**

```bash
maton api -X POST '/resend/webhooks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "endpoint": "https://yoursite.com/webhook",
  "events": [
    "email.delivered",
    "email.bounced",
    "email.opened"
  ]
}
JSON
```

**Webhook Events:**
- `email.sent` - Email was sent
- `email.delivered` - Email was delivered
- `email.opened` - Email was opened
- `email.clicked` - Link in email was clicked
- `email.bounced` - Email bounced
- `email.complained` - Recipient marked as spam

#### Get Webhook

```bash
maton api '/resend/webhooks/{webhook_id}'
```

**Note:** `{webhook_id}` is a placeholder. Replace it with a real value before sending the request.

#### Replace Webhook (Full Update)

```bash
maton api -X PUT '/resend/webhooks/{webhook_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "endpoint": "https://example.com/new-webhook",
  "events": ["email.sent", "email.delivered"]
}
EOF
```

**Note:** `{webhook_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Webhook

```bash
maton api -X PATCH '/resend/webhooks/{webhook_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "endpoint": "https://example.com/webhook",
  "events": ["email.delivered", "email.bounced"],
  "status": "enabled"
}
JSON
```

**Note:** `{webhook_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Webhook

```bash
maton api '/resend/webhooks/{webhook_id}' -X DELETE
```

**Note:** `{webhook_id}` is a placeholder. Replace it with a real value before sending the request.

### API Keys

#### List API Keys

```bash
maton api '/resend/api-keys'
```

#### Create API Key

```bash
maton api -X POST '/resend/api-keys'
```

**Example:**

```bash
maton api -X POST '/resend/api-keys' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Production Key"
}
JSON
```

> **Note:** The actual API key value is only returned once on creation.

#### Delete API Key

```bash
maton api '/resend/api-keys/{api_key_id}' -X DELETE
```

**Note:** `{api_key_id}` is a placeholder. Replace it with a real value before sending the request.

### Topics API

#### List Topics

```bash
maton api '/resend/topics'
```

#### Create Topic

```bash
maton api -X POST '/resend/topics'
```

**Example:**

```bash
maton api -X POST '/resend/topics' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Newsletter",
  "default_subscription": "subscribed"
}
JSON
```

> **Note:** `default_subscription` is required. Values: `subscribed` or `unsubscribed`.

#### Get Topic

```bash
maton api '/resend/topics/{topic_id}'
```

**Note:** `{topic_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Topic

```bash
maton api -X PATCH '/resend/topics/{topic_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Topic Name",
  "description": "Product announcements"
}
JSON
```

**Note:** `{topic_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Topic

```bash
maton api '/resend/topics/{topic_id}' -X DELETE
```

**Note:** `{topic_id}` is a placeholder. Replace it with a real value before sending the request.

### Contact Properties API

#### List Contact Properties

```bash
maton api '/resend/contact-properties'
```

#### Create Contact Property

```bash
maton api -X POST '/resend/contact-properties' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "plan_tier",
  "type": "string"
}
JSON
```

#### Get Contact Property

```bash
maton api '/resend/contact-properties/{property_id}'
```

**Note:** `{property_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Contact Property

```bash
maton api -X PATCH '/resend/contact-properties/{property_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "plan_tier"
}
JSON
```

**Note:** `{property_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Contact Property

```bash
maton api '/resend/contact-properties/{property_id}' -X DELETE
```

**Note:** `{property_id}` is a placeholder. Replace it with a real value before sending the request.

### Notes

- Sending emails requires a verified domain
- Rate limit: 2 requests per second
- Batch emails accept up to 100 emails per request
- Scheduled emails can be set up to 7 days in advance
- Attachments support base64 encoded content or URLs
- The `from` address must use a verified domain

### Resources

- [Resend API Documentation](https://resend.com/docs/api-reference/introduction)
- [Resend Dashboard](https://resend.com/overview)
- [Maton CLI Manual](https://cli.maton.ai/manual)
