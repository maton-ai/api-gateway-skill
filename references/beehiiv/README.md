# beehiiv

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `beehiiv`
**Upstream base URL:** `api.beehiiv.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.beehiiv.com/v2/publications`
- Gateway: `https://api.maton.ai/beehiiv/v2/publications`

### Publications API

#### List Publications

```bash
maton api '/beehiiv/v2/publications'
```

**Query parameters:**

| Parameter | Description |
|-----------|-------------|
| `limit` | Results per page (1-100, default: 10) |
| `page` | Page number (default: 1) |
| `expand[]` | Expand with: `stats`, `stat_active_subscriptions`, `stat_average_open_rate`, etc. |
| `order_by` | Sort by: `created` or `name` |
| `direction` | Sort direction: `asc` or `desc` |

**Response:**
```json
{
  "data": [
    {
      "id": "pub_c6c521e4-91ac-4c14-8a52-06987b7e32f2",
      "name": "My Newsletter",
      "organization_name": "My Organization",
      "referral_program_enabled": true,
      "created": 1770767522
    }
  ],
  "page": 1,
  "limit": 10,
  "total_results": 1,
  "total_pages": 1
}
```

#### Get Publication

```bash
maton api '/beehiiv/v2/publications/{publication_id}'
```

**Note:** `{publication_id}` is a placeholder. Replace it with a real value before sending the request.

### Subscriptions API

#### List Subscriptions

```bash
maton api '/beehiiv/v2/publications/{publication_id}/subscriptions'
```

**Note:** `{publication_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Parameter | Description |
|-----------|-------------|
| `limit` | Results per page (1-100, default: 10) |
| `cursor` | Cursor for pagination (recommended) |
| `page` | Page number (deprecated, max 100 pages) |
| `email` | Filter by exact email (case-insensitive) |
| `status` | Filter: `validating`, `invalid`, `pending`, `active`, `inactive`, `all` |
| `tier` | Filter: `free`, `premium`, `all` |
| `expand[]` | Expand with: `stats`, `custom_fields`, `referrals` |
| `order_by` | Sort field (default: `created`) |
| `direction` | Sort direction: `asc` or `desc` |

**Response:**
```json
{
  "data": [
    {
      "id": "sub_c27d9640-f418-43a8-a0f9-528c20a05002",
      "email": "subscriber@example.com",
      "status": "active",
      "created": 1770767524,
      "subscription_tier": "free",
      "subscription_premium_tier_names": [],
      "utm_source": "direct",
      "utm_medium": "",
      "utm_channel": "website",
      "utm_campaign": "",
      "referring_site": "",
      "referral_code": "gBZbSVal1X",
      "stripe_customer_id": ""
    }
  ],
  "limit": 10,
  "has_more": false,
  "next_cursor": null
}
```

#### Get Subscription by ID

```bash
maton api '/beehiiv/v2/publications/{publication_id}/subscriptions/{subscription_id}'
```

**Note:** `{publication_id}` and `{subscription_id}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**

| Parameter | Description |
|-----------|-------------|
| `expand[]` | Expand with: `stats`, `custom_fields`, `referrals`, `tags` |

#### Get Subscription by Email

```bash
maton api '/beehiiv/v2/publications/{publication_id}/subscriptions/by_email/{email}'
```

**Note:** `{publication_id}` and `{email}` are placeholders. Replace each of them with real values before sending the request.

#### Create Subscription

```bash
maton api -X POST '/beehiiv/v2/publications/{publication_id}/subscriptions' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "email": "newsubscriber@example.com",
  "utm_source": "api",
  "send_welcome_email": false,
  "reactivate_existing": false
}
JSON
```

**Note:** `{publication_id}` is a placeholder. Replace it with a real value before sending the request.

**Request body:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `email` | string | Yes | Subscriber email address |
| `reactivate_existing` | boolean | No | Reactivate if previously unsubscribed |
| `send_welcome_email` | boolean | No | Send welcome email |
| `utm_source` | string | No | UTM source for tracking |
| `utm_medium` | string | No | UTM medium |
| `referring_site` | string | No | Referral code of referring subscriber |
| `custom_fields` | object | No | Custom field values (fields must exist) |
| `double_opt_override` | string | No | `on` or `off` to override double opt-in |
| `tier` | string | No | Subscription tier |
| `premium_tier_names` | array | No | Premium tier names to assign |

#### Update Subscription

```bash
maton api -X PATCH '/beehiiv/v2/publications/{publication_id}/subscriptions/{subscription_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "utm_source": "updated-source",
  "custom_fields": [
    {"name": "First Name", "value": "John"}
  ]
}
JSON
```

**Note:** `{publication_id}` and `{subscription_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Subscription

```bash
maton api '/beehiiv/v2/publications/{publication_id}/subscriptions/{subscription_id}' -X DELETE
```

**Note:** `{publication_id}` and `{subscription_id}` are placeholders. Replace each of them with real values before sending the request.

### Posts API

#### List Posts

```bash
maton api '/beehiiv/v2/publications/{publication_id}/posts'
```

**Note:** `{publication_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Parameter | Description |
|-----------|-------------|
| `limit` | Results per page (1-100, default: 10) |
| `page` | Page number |
| `status` | Filter by status |
| `expand[]` | Expand with additional data |

**Response:**
```json
{
  "data": [],
  "page": 1,
  "limit": 10,
  "total_results": 0,
  "total_pages": 0
}
```

#### Get Post

```bash
maton api '/beehiiv/v2/publications/{publication_id}/posts/{post_id}'
```

**Note:** `{publication_id}` and `{post_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Post

```bash
maton api '/beehiiv/v2/publications/{publication_id}/posts/{post_id}' -X DELETE
```

**Note:** `{publication_id}` and `{post_id}` are placeholders. Replace each of them with real values before sending the request.

### Custom Fields API

#### List Custom Fields

```bash
maton api '/beehiiv/v2/publications/{publication_id}/custom_fields'
```

**Note:** `{publication_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "data": [
    {
      "id": "95c9653f-a1cf-45f0-a140-97feef19057b",
      "kind": "string",
      "display": "Last Name",
      "created": 1770767523
    },
    {
      "id": "4cfe081e-c89b-4da5-9c1a-52a4fb8ba69e",
      "kind": "string",
      "display": "First Name",
      "created": 1770767523
    }
  ],
  "page": 1,
  "limit": 10,
  "total_results": 2,
  "total_pages": 1
}
```

**Field Kinds:** `string`, `integer`, `boolean`, `date`, `datetime`, `list`, `double`

#### Create Custom Field

```bash
maton api -X POST '/beehiiv/v2/publications/{publication_id}/custom_fields' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "display": "Company",
  "kind": "string"
}
JSON
```

**Note:** `{publication_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Custom Field

```bash
maton api -X PATCH '/beehiiv/v2/publications/{publication_id}/custom_fields/{custom_field_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "display": "Company Name"
}
JSON
```

**Note:** `{publication_id}` and `{custom_field_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Custom Field

```bash
maton api '/beehiiv/v2/publications/{publication_id}/custom_fields/{custom_field_id}' -X DELETE
```

**Note:** `{publication_id}` and `{custom_field_id}` are placeholders. Replace each of them with real values before sending the request.

### Segments API

#### List Segments

```bash
maton api '/beehiiv/v2/publications/{publication_id}/segments'
```

**Note:** `{publication_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "data": [],
  "page": 1,
  "limit": 10,
  "total_results": 0,
  "total_pages": 0
}
```

#### Get Segment

```bash
maton api '/beehiiv/v2/publications/{publication_id}/segments/{segment_id}'
```

**Note:** `{publication_id}` and `{segment_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Segment

```bash
maton api '/beehiiv/v2/publications/{publication_id}/segments/{segment_id}' -X DELETE
```

**Note:** `{publication_id}` and `{segment_id}` are placeholders. Replace each of them with real values before sending the request.

### Tiers API

#### List Tiers

```bash
maton api '/beehiiv/v2/publications/{publication_id}/tiers'
```

**Note:** `{publication_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Tier

```bash
maton api '/beehiiv/v2/publications/{publication_id}/tiers/{tier_id}'
```

**Note:** `{publication_id}` and `{tier_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Tier

```bash
maton api -X POST '/beehiiv/v2/publications/{publication_id}/tiers' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Premium",
  "description": "Premium tier with exclusive content"
}
JSON
```

**Note:** `{publication_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Tier

```bash
maton api -X PATCH '/beehiiv/v2/publications/{publication_id}/tiers/{tier_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Tier Name"
}
JSON
```

**Note:** `{publication_id}` and `{tier_id}` are placeholders. Replace each of them with real values before sending the request.

### Automations API

#### List Automations

```bash
maton api '/beehiiv/v2/publications/{publication_id}/automations'
```

**Note:** `{publication_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Automation

```bash
maton api '/beehiiv/v2/publications/{publication_id}/automations/{automation_id}'
```

**Note:** `{publication_id}` and `{automation_id}` are placeholders. Replace each of them with real values before sending the request.

### Referral Program API

#### Get Referral Program

```bash
maton api '/beehiiv/v2/publications/{publication_id}/referral_program'
```

**Note:** `{publication_id}` is a placeholder. Replace it with a real value before sending the request.

### Pagination

beehiiv supports two pagination methods:

#### Cursor-Based (Recommended)

```bash
maton api '/beehiiv/v2/publications/{publication_id}/subscriptions?limit=10&cursor={next_cursor}'
```

**Note:** `{publication_id}` and `{next_cursor}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "data": [...],
  "limit": 10,
  "has_more": true,
  "next_cursor": "eyJ0aW1lc3RhbXAiOiIyMDI0LTA3LTAyVDE3OjMwOjAwLjAwMDAwMFoifQ=="
}
```

Use the `next_cursor` value for subsequent requests.

#### Page-Based (Deprecated)

```bash
maton api '/beehiiv/v2/publications?page=2&limit=10'
```

**Response:**
```json
{
  "data": [...],
  "page": 2,
  "limit": 10,
  "total_results": 50,
  "total_pages": 5
}
```

**Note:** Page-based pagination is limited to 100 pages maximum.

### Notes

- Publication IDs start with `pub_`
- Subscription IDs start with `sub_`
- Timestamps are Unix timestamps (seconds since epoch)
- Custom fields must be created before use in subscriptions
- Cursor-based pagination is recommended for better performance
- Page-based pagination is deprecated and limited to 100 pages

### Resources

- [beehiiv Developer Documentation](https://developers.beehiiv.com/)
- [beehiiv API Reference](https://developers.beehiiv.com/api-reference)
- [Maton CLI Manual](https://cli.maton.ai/manual)
