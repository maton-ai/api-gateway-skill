# Mailchimp

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `mailchimp`
**Upstream base URL:** `{dc}.api.mailchimp.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://{dc}.api.mailchimp.com/3.0/lists`
- Gateway: `https://api.maton.ai/mailchimp/3.0/lists`

### Audiences API

#### Get All Lists

```bash
maton api '/mailchimp/3.0/lists'
```

**Query parameters:**
- `count` - Number of records to return (default 10, max 1000)
- `offset` - Number of records to skip (for pagination)
- `fields` - Comma-separated list of fields to include
- `exclude_fields` - Comma-separated list of fields to exclude

**Response:**

```json
{
  "lists": [
    {
      "id": "abc123def4",
      "name": "Newsletter Subscribers",
      "contact": {
        "company": "Acme Corp",
        "address1": "123 Main St"
      },
      "stats": {
        "member_count": 5000,
        "unsubscribe_count": 100,
        "open_rate": 0.25
      }
    }
  ],
  "total_items": 1
}
```

#### Get List

```bash
maton api '/mailchimp/3.0/lists/{list_id}'
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create List

```bash
maton api -X POST '/mailchimp/3.0/lists' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Newsletter",
  "contact": {
    "company": "Acme Corp",
    "address1": "123 Main St",
    "city": "New York",
    "state": "NY",
    "zip": "10001",
    "country": "US"
  },
  "permission_reminder": "You signed up for our newsletter",
  "campaign_defaults": {
    "from_name": "Acme Corp",
    "from_email": "newsletter@acme.com",
    "subject": "",
    "language": "en"
  },
  "email_type_option": true
}
JSON
```

#### Update List

```bash
maton api -X PATCH '/mailchimp/3.0/lists/{list_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated List Name",
  "permission_reminder": "You subscribed on our website."
}
JSON
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete List

```bash
maton api '/mailchimp/3.0/lists/{list_id}' -X DELETE
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get List Members

```bash
maton api '/mailchimp/3.0/lists/{list_id}/members'
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `status` - Filter by subscription status (subscribed, unsubscribed, cleaned, pending, transactional)
- `count` - Number of records to return
- `offset` - Number of records to skip

**Example:**

```bash
maton api '/mailchimp/3.0/lists/{list_id}/members?status=subscribed&count=50'
```

**Response:**
```json
{
  "members": [
    {
      "id": "f4b7c8d9e0",
      "email_address": "john@example.com",
      "status": "subscribed",
      "merge_fields": {
        "FNAME": "John",
        "LNAME": "Doe"
      },
      "tags": [
        {"id": 1, "name": "VIP"}
      ]
    }
  ],
  "total_items": 500
}
```

#### Get Member

```bash
maton api '/mailchimp/3.0/lists/{list_id}/members/{subscriber_hash}'
```

**Note:** `{list_id}` and `{subscriber_hash}` are placeholders. Replace each of them with real values before sending the request.

**Note:** The `subscriber_hash` is the MD5 hash of the lowercase email address.

#### Add Member

```bash
maton api -X POST '/mailchimp/3.0/lists/{list_id}/members' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "email_address": "newuser@example.com",
  "status": "subscribed",
  "merge_fields": {
    "FNAME": "Jane",
    "LNAME": "Smith"
  },
  "tags": ["Newsletter", "Premium"]
}
JSON
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

**Example:**

```bash
maton api -X POST '/mailchimp/3.0/lists/{list_id}/members' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "email_address": "newuser@example.com",
  "status": "subscribed",
  "merge_fields": {
    "FNAME": "Jane",
    "LNAME": "Smith"
  },
  "tags": ["Newsletter", "Premium"]
}
JSON
```

#### Update Member

```bash
maton api -X PATCH '/mailchimp/3.0/lists/{list_id}/members/{subscriber_hash}'
```

**Note:** `{list_id}` and `{subscriber_hash}` are placeholders. Replace each of them with real values before sending the request.

**Example:**

```bash
maton api -X PATCH '/mailchimp/3.0/lists/{list_id}/members/{subscriber_hash}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "merge_fields": {
    "FNAME": "Jane",
    "LNAME": "Doe"
  }
}
JSON
```

#### Upsert Member

```bash
maton api -X PUT '/mailchimp/3.0/lists/{list_id}/members/{subscriber_hash}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "email_address": "user@example.com",
  "status_if_new": "subscribed",
  "merge_fields": {
    "FNAME": "Jane",
    "LNAME": "Smith"
  }
}
JSON
```

**Note:** `{list_id}` and `{subscriber_hash}` are placeholders. Replace each of them with real values before sending the request.

Creates a new member or updates an existing one based on the email hash. Use `status_if_new` to set the status when creating a new member.

#### Delete Member

Archives a member (can be re-added later):

```bash
maton api '/mailchimp/3.0/lists/{list_id}/members/{subscriber_hash}' -X DELETE
```

**Note:** `{list_id}` and `{subscriber_hash}` are placeholders. Replace each of them with real values before sending the request.

Returns `204 No Content` on success.

#### Permanently Delete Member

```bash
maton api -X POST '/mailchimp/3.0/lists/{list_id}/members/{subscriber_hash}/actions/delete-permanent'
```

**Note:** `{list_id}` and `{subscriber_hash}` are placeholders. Replace each of them with real values before sending the request.

#### Get Member Tags

```bash
maton api '/mailchimp/3.0/lists/{list_id}/members/{subscriber_hash}/tags'
```

**Note:** `{list_id}` and `{subscriber_hash}` are placeholders. Replace each of them with real values before sending the request.

#### Add or Remove Tags

```bash
maton api -X POST '/mailchimp/3.0/lists/{list_id}/members/{subscriber_hash}/tags' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "tags": [
    {"name": "VIP", "status": "active"},
    {"name": "Old Tag", "status": "inactive"}
  ]
}
JSON
```

**Note:** `{list_id}` and `{subscriber_hash}` are placeholders. Replace each of them with real values before sending the request.

Returns `204 No Content` on success.

### Segments API

#### Get Segments

```bash
maton api '/mailchimp/3.0/lists/{list_id}/segments'
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Segment

```bash
maton api -X POST '/mailchimp/3.0/lists/{list_id}/segments' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Active Subscribers",
  "options": {
    "match": "all",
    "conditions": [
      {
        "condition_type": "EmailActivity",
        "field": "opened",
        "op": "date_within",
        "value": "30"
      }
    ]
  }
}
JSON
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Segment

```bash
maton api -X PATCH '/mailchimp/3.0/lists/{list_id}/segments/{segment_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Segment Name"
}
JSON
```

**Note:** `{list_id}` and `{segment_id}` are placeholders. Replace each of them with real values before sending the request.

#### Get Segment Members

```bash
maton api '/mailchimp/3.0/lists/{list_id}/segments/{segment_id}/members'
```

**Note:** `{list_id}` and `{segment_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Segment

```bash
maton api '/mailchimp/3.0/lists/{list_id}/segments/{segment_id}' -X DELETE
```

**Note:** `{list_id}` and `{segment_id}` are placeholders. Replace each of them with real values before sending the request.

Returns `204 No Content` on success.

### Campaigns API

#### List Campaigns

```bash
maton api '/mailchimp/3.0/campaigns'
```

**Query parameters:**
- `type` - Campaign type (regular, plaintext, absplit, rss, variate)
- `status` - Campaign status (save, paused, schedule, sending, sent)
- `list_id` - Filter by list ID
- `count` - Number of records to return
- `offset` - Number of records to skip

**Example:**

```bash
maton api '/mailchimp/3.0/campaigns?status=sent&count=20'
```

**Response:**
```json
{
  "campaigns": [
    {
      "id": "campaign123",
      "type": "regular",
      "status": "sent",
      "settings": {
        "subject_line": "Monthly Newsletter",
        "from_name": "Acme Corp"
      },
      "send_time": "2025-02-01T10:00:00Z",
      "report_summary": {
        "opens": 1500,
        "clicks": 300,
        "open_rate": 0.30,
        "click_rate": 0.06
      }
    }
  ],
  "total_items": 50
}
```

#### Get Campaign

```bash
maton api '/mailchimp/3.0/campaigns/{campaign_id}'
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Campaign

```bash
maton api -X POST '/mailchimp/3.0/campaigns' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "type": "regular",
  "recipients": {
    "list_id": "abc123def4"
  },
  "settings": {
    "subject_line": "Your Monthly Update",
    "from_name": "Acme Corp",
    "reply_to": "hello@acme.com"
  }
}
JSON
```

**Example:**

```bash
maton api -X POST '/mailchimp/3.0/campaigns' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "type": "regular",
  "recipients": {
    "list_id": "abc123def4"
  },
  "settings": {
    "subject_line": "Your Monthly Update",
    "from_name": "Acme Corp",
    "reply_to": "hello@acme.com"
  }
}
JSON
```

#### Update Campaign

```bash
maton api -X PATCH '/mailchimp/3.0/campaigns/{campaign_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "settings": {
    "subject_line": "Updated subject line",
    "title": "Updated Campaign Title"
  }
}
JSON
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Campaign

```bash
maton api '/mailchimp/3.0/campaigns/{campaign_id}' -X DELETE
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

Returns `204 No Content` on success.

#### Get Campaign Content

```bash
maton api '/mailchimp/3.0/campaigns/{campaign_id}/content'
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

#### Set Campaign Content

```bash
maton api -X PUT '/mailchimp/3.0/campaigns/{campaign_id}/content' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "html": "<html><body><h1>Hello!</h1><p>Newsletter content here.</p></body></html>",
  "plain_text": "Hello! Newsletter content here."
}
JSON
```

Or use a template:

```bash
maton api -X PUT '/mailchimp/3.0/campaigns/{campaign_id}/content' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "template": {
    "id": 12345,
    "sections": {
      "body": "<p>Custom content for the template section</p>"
    }
  }
}
JSON
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Campaign Send Checklist

Check if a campaign is ready to send:

```bash
maton api '/mailchimp/3.0/campaigns/{campaign_id}/send-checklist'
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

#### Send Campaign

```bash
maton api -X POST '/mailchimp/3.0/campaigns/{campaign_id}/actions/send'
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

#### Schedule Campaign

```bash
maton api -X POST '/mailchimp/3.0/campaigns/{campaign_id}/actions/schedule' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "schedule_time": "2025-03-01T10:00:00+00:00"
}
JSON
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

#### Cancel Scheduled Campaign

```bash
maton api -X POST '/mailchimp/3.0/campaigns/{campaign_id}/actions/cancel-send'
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

### Templates API

#### Get All Templates

```bash
maton api '/mailchimp/3.0/templates'
```

**Query parameters:**
- `type` - Template type (user, base, gallery)
- `count` - Number of records to return
- `offset` - Number of records to skip

**Example:**

```bash
maton api '/mailchimp/3.0/templates?type=user'
```

#### Get Template

```bash
maton api '/mailchimp/3.0/templates/{template_id}'
```

**Note:** `{template_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Template Default Content

```bash
maton api '/mailchimp/3.0/templates/{template_id}/default-content'
```

**Note:** `{template_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Template

```bash
maton api -X POST '/mailchimp/3.0/templates' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Newsletter Template",
  "html": "<html><body mc:edit=\"body\"><h1>Title</h1><p>Content here</p></body></html>"
}
JSON
```

#### Update Template

```bash
maton api -X PATCH '/mailchimp/3.0/templates/{template_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Template Name",
  "html": "<html><body><h1>Updated</h1></body></html>"
}
JSON
```

**Note:** `{template_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Template

```bash
maton api '/mailchimp/3.0/templates/{template_id}' -X DELETE
```

**Note:** `{template_id}` is a placeholder. Replace it with a real value before sending the request.

Returns `204 No Content` on success.

#### Get All Automations

```bash
maton api '/mailchimp/3.0/automations'
```

#### Get Automation

```bash
maton api '/mailchimp/3.0/automations/{workflow_id}'
```

**Note:** `{workflow_id}` is a placeholder. Replace it with a real value before sending the request.

#### Start Automation

```bash
maton api -X POST '/mailchimp/3.0/automations/{workflow_id}/actions/start-all-emails'
```

**Note:** `{workflow_id}` is a placeholder. Replace it with a real value before sending the request.

#### Pause Automation

```bash
maton api -X POST '/mailchimp/3.0/automations/{workflow_id}/actions/pause-all-emails'
```

**Note:** `{workflow_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Automation Emails

```bash
maton api '/mailchimp/3.0/automations/{workflow_id}/emails'
```

**Note:** `{workflow_id}` is a placeholder. Replace it with a real value before sending the request.

#### Add Subscriber to Automation Queue

Manually add a subscriber to an automation workflow:

```bash
maton api -X POST '/mailchimp/3.0/automations/{workflow_id}/emails/{workflow_email_id}/queue' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "email_address": "subscriber@example.com"
}
JSON
```

**Note:** `{workflow_id}` and `{workflow_email_id}` are placeholders. Replace each of them with real values before sending the request.

### Reports API

#### Get Campaign Reports

```bash
maton api '/mailchimp/3.0/reports'
```

**Query parameters:**
- `count` - Number of records to return
- `offset` - Number of records to skip
- `type` - Campaign type

**Example:**

```bash
maton api '/mailchimp/3.0/reports?count=20'
```

**Response:**
```json
{
  "reports": [
    {
      "id": "campaign123",
      "campaign_title": "Monthly Newsletter",
      "emails_sent": 5000,
      "opens": {
        "opens_total": 1500,
        "unique_opens": 1200,
        "open_rate": 0.24
      },
      "clicks": {
        "clicks_total": 450,
        "unique_clicks": 300,
        "click_rate": 0.06
      },
      "unsubscribed": 10,
      "bounce_rate": 0.02
    }
  ]
}
```

#### Get Campaign Report

```bash
maton api '/mailchimp/3.0/reports/{campaign_id}'
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Campaign Open Details

```bash
maton api '/mailchimp/3.0/reports/{campaign_id}/open-details'
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Campaign Click Details

```bash
maton api '/mailchimp/3.0/reports/{campaign_id}/click-details'
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get List Activity

```bash
maton api '/mailchimp/3.0/lists/{list_id}/activity'
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

Returns recent daily aggregated activity stats (unsubscribes, signups, opens, clicks) for up to 180 days.

### Batch API

#### Create Batch Operation

```bash
maton api -X POST '/mailchimp/3.0/batches' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "operations": [
    {
      "method": "POST",
      "path": "/lists/abc123def4/members",
      "body": "{\"email_address\":\"user1@example.com\",\"status\":\"subscribed\"}"
    },
    {
      "method": "POST",
      "path": "/lists/abc123def4/members",
      "body": "{\"email_address\":\"user2@example.com\",\"status\":\"subscribed\"}"
    }
  ]
}
JSON
```

#### Get Batch Status

```bash
maton api '/mailchimp/3.0/batches/{batch_id}'
```

**Note:** `{batch_id}` is a placeholder. Replace it with a real value before sending the request.

#### List All Batches

```bash
maton api '/mailchimp/3.0/batches'
```

#### Delete a Batch

```bash
maton api '/mailchimp/3.0/batches/{batch_id}' -X DELETE
```

**Note:** `{batch_id}` is a placeholder. Replace it with a real value before sending the request.

Returns `204 No Content` on success.

### Notes

- List IDs are 10-character alphanumeric strings
- Subscriber hashes are MD5 hashes of lowercase email addresses
- Timestamps are in ISO 8601 format
- Maximum 1000 records per request for list endpoints
- "Audience" and "list" are used interchangeably (app vs API terminology)
- "Contact" and "member" are used interchangeably (app vs API terminology)
- Use offset-based pagination with `count` and `offset` parameters

### Resources

- [Mailchimp Marketing API Documentation](https://mailchimp.com/developer/marketing/)
- [Mailchimp API Reference](https://mailchimp.com/developer/marketing/api/)
- [Quick Start Guide](https://mailchimp.com/developer/marketing/guides/quick-start/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
