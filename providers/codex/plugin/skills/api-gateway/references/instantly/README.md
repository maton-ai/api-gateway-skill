# Instantly

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `instantly`
**Upstream base URL:** `api.instantly.ai`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.instantly.ai/api/v2/campaigns`
- Gateway: `https://api.maton.ai/instantly/api/v2/campaigns`

### Campaigns API

#### List Campaigns

```bash
maton api '/instantly/api/v2/campaigns?limit=10&status=1&search=keyword'
```

**Query parameters:**
- `limit` - Number of results (default: 10)
- `status` - Campaign status filter (0=draft, 1=active, 2=paused, 3=completed)
- `search` - Search by campaign name
- `starting_after` - Cursor for pagination

#### Get Campaign

```bash
maton api '/instantly/api/v2/campaigns/{campaign_id}'
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Campaign

```bash
maton api -X POST '/instantly/api/v2/campaigns' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "My Campaign",
  "campaign_schedule": {
    "schedules": [
      {
        "name": "My Schedule",
        "timing": {
          "from": "09:00",
          "to": "17:00"
        },
        "days": {
          "0": true,
          "1": true,
          "2": true,
          "3": true,
          "4": true
        },
        "timezone": "Etc/GMT+5"
      }
    ]
  }
}
JSON
```

Note: the `Etc/GMT` format is an Instantly API constraint, not a preference of this skill. Any timezone the user wants can be expressed in it — pick the offset matching their timezone (e.g., "Etc/GMT+5", "Etc/GMT-8", "Etc/GMT+12"); note the sign is inverted relative to UTC labels.

#### Activate Campaign

```bash
maton api -X POST '/instantly/api/v2/campaigns/{campaign_id}/activate'
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

#### Pause Campaign

```bash
maton api -X POST '/instantly/api/v2/campaigns/{campaign_id}/pause'
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Campaign

```bash
maton api '/instantly/api/v2/campaigns/{campaign_id}' -X DELETE
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

#### Search Campaigns by Lead Email

```bash
maton api '/instantly/api/v2/campaigns/search-by-contact?search=lead@example.com'
```

### Leads API

#### Create Lead

```bash
maton api -X POST '/instantly/api/v2/leads' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "campaign_id": "019bb3bd-9963-789e-b776-6c6927ef3f79",
  "email": "lead@example.com",
  "first_name": "John",
  "last_name": "Doe",
  "company_name": "Acme Inc",
  "variables": {
    "custom_field": "custom_value"
  }
}
JSON
```

#### Bulk Add Leads

```bash
maton api -X POST '/instantly/api/v2/leads' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "campaign_id": "019bb3bd-9963-789e-b776-6c6927ef3f79",
  "leads": [
    {
      "email": "lead1@example.com",
      "first_name": "John"
    },
    {
      "email": "lead2@example.com",
      "first_name": "Jane"
    }
  ]
}
JSON
```

#### List Leads

Note: This is a POST endpoint due to complex filtering requirements.

```bash
maton api -X POST '/instantly/api/v2/leads/list' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "campaign_id": "019bb3bd-9963-789e-b776-6c6927ef3f79",
  "limit": 100
}
JSON
```

#### Get Lead

```bash
maton api '/instantly/api/v2/leads/{lead_id}'
```

**Note:** `{lead_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Lead

```bash
maton api '/instantly/api/v2/leads/{lead_id}' -X DELETE
```

**Note:** `{lead_id}` is a placeholder. Replace it with a real value before sending the request.

#### Move Leads

```bash
maton api -X POST '/instantly/api/v2/leads/move' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "lead_ids": ["lead_id_1", "lead_id_2"],
  "to_campaign_id": "target_campaign_id"
}
JSON
```

### Lead Lists API

#### List Lead Lists

```bash
maton api '/instantly/api/v2/lead-lists?limit=10'
```

#### Create Lead List

```bash
maton api -X POST '/instantly/api/v2/lead-lists' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "My Lead List"
}
JSON
```

#### Get Lead List

```bash
maton api '/instantly/api/v2/lead-lists/{list_id}'
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Lead List

```bash
maton api -X PATCH '/instantly/api/v2/lead-lists/{list_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated List Name"
}
JSON
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Lead List

```bash
maton api '/instantly/api/v2/lead-lists/{list_id}' -X DELETE
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

### Accounts API

#### List Accounts

```bash
maton api '/instantly/api/v2/accounts?limit=10'
```

#### Get Account

```bash
maton api '/instantly/api/v2/accounts/{email}'
```

**Note:** `{email}` is a placeholder. Replace it with a real value before sending the request.

#### Create Account

```bash
maton api -X POST '/instantly/api/v2/accounts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "email": "sender@example.com",
  "first_name": "John",
  "last_name": "Doe",
  "provider_code": "google",
  "smtp_host": "smtp.gmail.com",
  "smtp_port": 587,
  "smtp_username": "sender@example.com",
  "smtp_password": "app_password",
  "imap_host": "imap.gmail.com",
  "imap_port": 993,
  "imap_username": "sender@example.com",
  "imap_password": "app_password"
}
JSON
```

#### Update Account

```bash
maton api -X PATCH '/instantly/api/v2/accounts/{email}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "first_name": "Jane"
}
JSON
```

**Note:** `{email}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Account

```bash
maton api '/instantly/api/v2/accounts/{email}' -X DELETE
```

**Note:** `{email}` is a placeholder. Replace it with a real value before sending the request.

#### Enable Warmup

```bash
maton api -X POST '/instantly/api/v2/accounts/warmup/enable' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "emails": ["account1@example.com", "account2@example.com"]
}
JSON
```

#### Disable Warmup

```bash
maton api -X POST '/instantly/api/v2/accounts/warmup/disable' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "emails": ["account1@example.com"]
}
JSON
```

### Emails API

#### List Emails

```bash
maton api '/instantly/api/v2/emails?limit=20'
```

#### Get Email

```bash
maton api '/instantly/api/v2/emails/{email_id}'
```

**Note:** `{email_id}` is a placeholder. Replace it with a real value before sending the request.

#### Reply to Email

```bash
maton api -X POST '/instantly/api/v2/emails/reply' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "reply_to_uuid": "email_uuid",
  "body": "Thank you for your response!"
}
JSON
```

#### Forward Email

```bash
maton api -X POST '/instantly/api/v2/emails/forward' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "email_uuid": "email_uuid",
  "to": "forward@example.com"
}
JSON
```

#### Mark Thread as Read

```bash
maton api -X POST '/instantly/api/v2/emails/threads/{thread_id}/mark-as-read'
```

**Note:** `{thread_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Unread Count

```bash
maton api '/instantly/api/v2/emails/unread/count'
```

#### Update Email

```bash
maton api -X PATCH '/instantly/api/v2/emails/{email_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "is_read": true
}
JSON
```

**Note:** `{email_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Email

```bash
maton api '/instantly/api/v2/emails/{email_id}' -X DELETE
```

**Note:** `{email_id}` is a placeholder. Replace it with a real value before sending the request.

### Analytics API

#### Get Campaign Analytics

```bash
maton api '/instantly/api/v2/campaigns/analytics?id={campaign_id}'
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `id` - Campaign ID (leave empty for all campaigns)
- `start_date` - Filter start date (YYYY-MM-DD)
- `end_date` - Filter end date (YYYY-MM-DD)
- `exclude_total_leads_count` - Set to true for faster response

#### Get Campaign Analytics Overview

```bash
maton api '/instantly/api/v2/campaigns/analytics/overview?id={campaign_id}'
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Daily Campaign Analytics

```bash
maton api '/instantly/api/v2/campaigns/analytics/daily?id={campaign_id}'
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Campaign Step Analytics

```bash
maton api '/instantly/api/v2/campaigns/analytics/steps?id={campaign_id}'
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Warmup Analytics

```bash
maton api -X POST '/instantly/api/v2/accounts/warmup/analytics' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "emails": ["account@example.com"]
}
JSON
```

### Block List API

#### List Block List Entries

```bash
maton api '/instantly/api/v2/block-lists-entries?limit=100'
```

**Query parameters:**
- `domains_only` - Filter to domain entries only
- `search` - Search entries

#### Create Block List Entry

```bash
maton api -X POST '/instantly/api/v2/block-lists-entries' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "bl_value": "blocked@example.com"
}
JSON
```

Or block a domain:

```bash
maton api -X POST '/instantly/api/v2/block-lists-entries' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "bl_value": "blockeddomain.com"
}
JSON
```

#### Delete Block List Entry

```bash
maton api '/instantly/api/v2/block-lists-entries/{entry_id}' -X DELETE
```

**Note:** `{entry_id}` is a placeholder. Replace it with a real value before sending the request.

### Email Verification API

#### Verify Email

```bash
maton api '/instantly/api/v2/email-verification/{email}'
```

**Note:** `{email}` is a placeholder. Replace it with a real value before sending the request.

If verification takes longer than 10 seconds, status will be `pending`. Poll this endpoint to check status.

Response fields:
- `verification_status` - Use this field (not `status`) to determine verification result

### Background Jobs API

#### Get Background Job Status

```bash
maton api '/instantly/api/v2/background-jobs/{job_id}'
```

**Note:** `{job_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `data_fields` - Comma-separated fields (e.g., `success_count,failed_count,total_to_process`)

### Workspace API

#### Get Current Workspace

```bash
maton api '/instantly/api/v2/workspaces/current'
```

### Custom Tags API

#### Toggle Tag on Resource

```bash
maton api -X POST '/instantly/api/v2/custom-tags/toggle-resource' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "tag_id": "tag_uuid",
  "resource_id": "campaign_or_account_id",
  "resource_type": "campaign"
}
JSON
```

### Pagination

Instantly uses cursor-based pagination with `limit` and `starting_after`:

```bash
maton api '/instantly/api/v2/campaigns?limit=10&starting_after=cursor_value'
```

Response includes pagination info:

```json
{
  "items": [...],
  "next_starting_after": "cursor_for_next_page"
}
```

Use `next_starting_after` value in the next request's `starting_after` parameter.

### Notes

- Instantly API v2 uses snake_case for all field names
- Lead custom variables must be string, number, boolean, or null (no objects/arrays)
- The List Leads endpoint is POST (not GET) due to complex filtering requirements
- Campaign status values: 0=draft, 1=active, 2=paused, 3=completed
- Email verification may return `pending` status if it takes longer than 10 seconds
- Warmup operations return background job IDs - poll the background jobs endpoint for status

### Resources

- [Instantly API V2 Documentation](https://developer.instantly.ai/api-reference)
- [Maton CLI Manual](https://cli.maton.ai/manual)
