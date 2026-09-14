# CallRail

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `callrail`
**Upstream base URL:** `api.callrail.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.callrail.com/v3/a.json`
- Gateway: `https://api.maton.ai/callrail/v3/a.json`

**Important:** All CallRail API endpoints end with `.json`. Account IDs start with `ACC`.

### Accounts API

#### List Accounts

```bash
maton api '/callrail/v3/a.json'
```

**Response:**
```json
{
  "page": 1,
  "per_page": 100,
  "total_pages": 1,
  "total_records": 1,
  "accounts": [
    {
      "id": "ACC019c46b8a0807fbdb81c8bf12af91cb3",
      "name": "My Account",
      "numeric_id": 518664017,
      "inbound_recording_enabled": false,
      "outbound_recording_enabled": false,
      "hipaa_account": false,
      "created_at": "2026-02-10 03:43:50 -0500"
    }
  ]
}
```

#### Get Account

```bash
maton api '/callrail/v3/a/{account_id}.json'
```

**Note:** `{account_id}` is a placeholder. Replace it with a real value before sending the request.

### Companies API

#### List Companies

```bash
maton api '/callrail/v3/a/{account_id}/companies.json'
```

**Note:** `{account_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "page": 1,
  "per_page": 100,
  "total_pages": 1,
  "total_records": 1,
  "companies": [
    {
      "id": "COM019c46b8a26376a9a4f29671dcdd49e9",
      "name": "My Company",
      "status": "active",
      "time_zone": "America/Los_Angeles",
      "created_at": "2026-02-10T08:43:51.280Z",
      "callscore_enabled": false,
      "lead_scoring_enabled": true,
      "callscribe_enabled": true
    }
  ]
}
```

#### Get Company

```bash
maton api '/callrail/v3/a/{account_id}/companies/{company_id}.json'
```

**Note:** `{account_id}` and `{company_id}` are placeholders. Replace each of them with real values before sending the request.

### Calls API

#### List Calls

```bash
maton api '/callrail/v3/a/{account_id}/calls.json'
```

**Note:** `{account_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Parameter | Description |
|-----------|-------------|
| `page` | Page number (default: 1) |
| `per_page` | Results per page (default: 100, max: 250) |
| `date_range` | Preset: `recent`, `today`, `yesterday`, `last_7_days`, `last_30_days`, `this_month`, `last_month` |
| `start_date` | ISO 8601 date (e.g., `2026-02-01T00:00:00-08:00`) |
| `end_date` | ISO 8601 date |
| `company_id` | Filter by company |
| `tracker_id` | Filter by tracker |
| `search` | Search term |
| `fields` | Comma-separated field names to return |
| `sort` | Field to sort by |
| `order` | Sort order: `asc` or `desc` |

**Response:**

```json
{
  "page": 1,
  "per_page": 100,
  "total_pages": 1,
  "total_records": 1,
  "calls": [
    {
      "id": "CAL019c46b9fc277a7881e3728fea20869b",
      "answered": false,
      "customer_name": "John Doe",
      "customer_phone_number": "+18886757190",
      "direction": "inbound",
      "duration": 36,
      "recording": ".../v3/a/.../recording",
      "recording_duration": 36,
      "start_time": "2026-02-10T00:45:19.781-08:00",
      "tracking_phone_number": "+18017846712",
      "voicemail": true
    }
  ]
}
```

#### Get Call

```bash
maton api '/callrail/v3/a/{account_id}/calls/{call_id}.json'
```

**Note:** `{account_id}` and `{call_id}` are placeholders. Replace each of them with real values before sending the request.

#### Update Call

```bash
maton api -X PUT '/callrail/v3/a/{account_id}/calls/{call_id}.json' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "customer_name": "John Smith",
  "note": "Follow up scheduled",
  "lead_status": "good_lead",
  "spam": false
}
JSON
```

**Note:** `{account_id}` and `{call_id}` are placeholders. Replace each of them with real values before sending the request.

**Updatable Fields:**

| Field | Description |
|-------|-------------|
| `customer_name` | Customer's name |
| `note` | Call notes |
| `lead_status` | `good_lead`, `not_a_lead`, `previously_marked_good_lead` |
| `spam` | Mark as spam (boolean) |
| `tag_list` | Array of tag names to apply |
| `value` | Call value (numeric) |
| `append_tags` | Add tags without removing existing |

#### Call Summary

```bash
maton api '/callrail/v3/a/{account_id}/calls/summary.json'
```

**Note:** `{account_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Parameter | Description |
|-----------|-------------|
| `date_range` | Preset date range |
| `start_date` | Start date (ISO 8601) |
| `end_date` | End date (ISO 8601) |
| `group_by` | Group results: `company`, `tracker`, `source`, `medium`, etc. |

Get aggregated call statistics for a date range.

**Response:**
```json
{
  "start_date": "2026-02-03T00:00:00-0800",
  "end_date": "2026-02-10T23:59:59-0800",
  "time_zone": "Pacific Time (US & Canada)",
  "total_results": {
    "total_calls": 42
  }
}
```

#### Call Timeseries

```bash
maton api '/callrail/v3/a/{account_id}/calls/timeseries.json'
```

**Note:** `{account_id}` is a placeholder. Replace it with a real value before sending the request.

Get call data over time for charts and graphs.

**Response:**
```json
{
  "start_date": "2026-02-03T00:00:00-0800",
  "end_date": "2026-02-10T23:59:59-0800",
  "data": [
    {"key": "2026-02-03", "date": "2026-02-03", "total_calls": 5},
    {"key": "2026-02-04", "date": "2026-02-04", "total_calls": 8}
  ]
}
```

### Trackers API

#### List Trackers

```bash
maton api '/callrail/v3/a/{account_id}/trackers.json'
```

**Note:** `{account_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "page": 1,
  "per_page": 100,
  "total_records": 1,
  "trackers": [
    {
      "id": "TRK019c46b9f18174d68bb8d7985260a11f",
      "name": "Google My Business",
      "type": "source",
      "status": "active",
      "destination_number": "+18019234886",
      "tracking_numbers": ["+18017846712"],
      "sms_supported": true,
      "sms_enabled": true,
      "company": {
        "id": "COM019c46b8a26376a9a4f29671dcdd49e9",
        "name": "My Company"
      },
      "source": {"type": "google_my_business"},
      "call_flow": {
        "type": "basic",
        "recording_enabled": true,
        "destination_number": "+18019234886"
      }
    }
  ]
}
```

#### Get Tracker

```bash
maton api '/callrail/v3/a/{account_id}/trackers/{tracker_id}.json'
```

**Note:** `{account_id}` and `{tracker_id}` are placeholders. Replace each of them with real values before sending the request.

### Tags API

#### List Tags

```bash
maton api '/callrail/v3/a/{account_id}/tags.json'
```

**Note:** `{account_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "page": 1,
  "per_page": 100,
  "total_records": 6,
  "tags": [
    {
      "id": 7886733,
      "name": "Schedule requested",
      "tag_level": "account",
      "color": "orange3",
      "background_color": "gray1",
      "company_id": null,
      "status": "enabled"
    },
    {
      "id": 7886728,
      "name": "Opportunity",
      "tag_level": "company",
      "color": "gray1",
      "company_id": "COM019c46b8a26376a9a4f29671dcdd49e9",
      "status": "enabled"
    }
  ]
}
```

#### Create Tag

```bash
maton api -X POST '/callrail/v3/a/{account_id}/tags.json' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Tag",
  "tag_level": "account",
  "color": "blue1"
}
JSON
```

**Note:** `{account_id}` is a placeholder. Replace it with a real value before sending the request.

**Tag Levels:**
- `account` - Available to all companies in the account
- `company` - Specific to a company (requires `company_id`)

**Colors:** `gray1`, `blue1`, `blue2`, `green1`, `green2`, `orange1`, `orange2`, `orange3`, `red1`, etc.

#### Update Tag

```bash
maton api -X PUT '/callrail/v3/a/{account_id}/tags/{tag_id}.json' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Tag Name",
  "color": "green1"
}
JSON
```

**Note:** `{account_id}` and `{tag_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Tag

```bash
maton api '/callrail/v3/a/{account_id}/tags/{tag_id}.json' -X DELETE
```

**Note:** `{account_id}` and `{tag_id}` are placeholders. Replace each of them with real values before sending the request.

### Users API

#### List Users

```bash
maton api '/callrail/v3/a/{account_id}/users.json'
```

**Note:** `{account_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "page": 1,
  "per_page": 100,
  "total_records": 1,
  "users": [
    {
      "id": "USR019c46b8a0557b2e85e5e1c651452509",
      "email": "user@example.com",
      "first_name": "John",
      "last_name": "Doe",
      "name": "John Doe",
      "role": "admin",
      "accepted": true,
      "created_at": "2026-02-10T03:43:50.798-05:00",
      "companies": [
        {"id": "COM...", "name": "My Company"}
      ]
    }
  ]
}
```

#### Get User

```bash
maton api '/callrail/v3/a/{account_id}/users/{user_id}.json'
```

**Note:** `{account_id}` and `{user_id}` are placeholders. Replace each of them with real values before sending the request.

### Integrations API

#### List Integrations

```bash
maton api '/callrail/v3/a/{account_id}/integrations.json?company_id={company_id}'
```

**Note:** `{account_id}` and `{company_id}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:** `company_id` (required)

### Notifications API

#### List Notifications

```bash
maton api '/callrail/v3/a/{account_id}/notifications.json'
```

**Note:** `{account_id}` is a placeholder. Replace it with a real value before sending the request.

### ID Prefixes

- Account IDs: `ACC`
- Company IDs: `COM`
- Call IDs: `CAL`
- Tracker IDs: `TRK`
- User IDs: `USR`

### Pagination

Uses offset-based pagination with `page` and `per_page` parameters:

```bash
maton api '/callrail/v3/a/{account_id}/calls.json?page=2&per_page=50'
# Response includes page, per_page, total_pages, total_records
```

**Note:** `{account_id}` is a placeholder. Replace it with a real value before sending the request.

For calls endpoint, relative pagination is available via `relative_pagination=true`.

### Notes

- All endpoints end with `.json`
- Communication records retained for 25 months
- Rate limits: 1,000/hour, 10,000/day for general API
- ISO 8601 date format with timezone

### Resources

- [CallRail API Documentation](https://apidocs.callrail.com/)
- [CallRail Help Center - API](https://support.callrail.com/hc/en-us/sections/4426797289229-API)
- [Maton CLI Manual](https://cli.maton.ai/manual)
