# ActiveCampaign

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `active-campaign`
**Upstream base URL:** `{account}.api-us1.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://{account}.api-us1.com/api/3/contacts`
- Gateway: `https://api.maton.ai/active-campaign/api/3/contacts`

### Contacts API

#### List Contacts

```bash
maton api '/active-campaign/api/3/contacts'
```

**Query parameters:**
- `limit` - Number of results (default: 20)
- `offset` - Starting index
- `search` - Search by email
- `filters[email]` - Filter by email
- `filters[listid]` - Filter by list ID

**Response:**
```json
{
  "contacts": [
    {
      "id": "1",
      "email": "user@example.com",
      "firstName": "John",
      "lastName": "Doe",
      "phone": "",
      "cdate": "2026-02-09T14:03:19-06:00",
      "udate": "2026-02-09T14:03:19-06:00"
    }
  ],
  "meta": {
    "total": "1"
  }
}
```

#### Get Contact

```bash
maton api '/active-campaign/api/3/contacts/{contactId}'
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

Returns contact with related data including lists, tags, deals, and field values.

#### Create Contact

```bash
maton api -X POST '/active-campaign/api/3/contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contact": {
    "email": "newcontact@example.com",
    "firstName": "John",
    "lastName": "Doe",
    "phone": "555-1234"
  }
}
JSON
```

**Response:**
```json
{
  "contact": {
    "id": "2",
    "email": "newcontact@example.com",
    "firstName": "John",
    "lastName": "Doe",
    "cdate": "2026-02-09T17:51:39-06:00",
    "udate": "2026-02-09T17:51:39-06:00"
  }
}
```

#### Update Contact

```bash
maton api -X PUT '/active-campaign/api/3/contacts/{contactId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contact": {
    "firstName": "Updated",
    "lastName": "Name"
  }
}
JSON
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Contact

```bash
maton api '/active-campaign/api/3/contacts/{contactId}' -X DELETE
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

Returns 200 OK on success.

#### Sync Contact (Create or Update)

```bash
maton api -X POST '/active-campaign/api/3/contact/sync' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contact": {
    "email": "user@example.com",
    "firstName": "Updated Name"
  }
}
JSON
```

Creates the contact if it doesn't exist, updates if it does.

### Tags API

#### List Tags

```bash
maton api '/active-campaign/api/3/tags'
```

**Response:**
```json
{
  "tags": [
    {
      "id": "1",
      "tag": "VIP Customer",
      "tagType": "contact",
      "description": "High-value customers",
      "cdate": "2026-02-09T17:51:39-06:00"
    }
  ],
  "meta": {
    "total": "1"
  }
}
```

#### Get Tag

```bash
maton api '/active-campaign/api/3/tags/{tagId}'
```

**Note:** `{tagId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Tag

```bash
maton api -X POST '/active-campaign/api/3/tags' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "tag": {
    "tag": "New Tag",
    "tagType": "contact",
    "description": "Tag description"
  }
}
JSON
```

#### Update Tag

```bash
maton api -X PUT '/active-campaign/api/3/tags/{tagId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "tag": {
    "tag": "Updated Tag Name"
  }
}
JSON
```

**Note:** `{tagId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Tag

```bash
maton api '/active-campaign/api/3/tags/{tagId}' -X DELETE
```

**Note:** `{tagId}` is a placeholder. Replace it with a real value before sending the request.

### Contact Tags API

#### Add Tag to Contact

```bash
maton api -X POST '/active-campaign/api/3/contactTags' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contactTag": {
    "contact": "2",
    "tag": "1"
  }
}
JSON
```

#### Remove Tag from Contact

```bash
maton api '/active-campaign/api/3/contactTags/{contactTagId}' -X DELETE
```

**Note:** `{contactTagId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Contact's Tags

```bash
maton api '/active-campaign/api/3/contacts/{contactId}/contactTags'
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

### Lists API

#### List All Lists

```bash
maton api '/active-campaign/api/3/lists'
```

**Response:**
```json
{
  "lists": [
    {
      "id": "1",
      "stringid": "master-contact-list",
      "name": "Master Contact List",
      "cdate": "2026-02-09T14:03:20-06:00"
    }
  ],
  "meta": {
    "total": "1"
  }
}
```

#### Get List

```bash
maton api '/active-campaign/api/3/lists/{listId}'
```

**Note:** `{listId}` is a placeholder. Replace it with a real value before sending the request.

#### Create List

```bash
maton api -X POST '/active-campaign/api/3/lists' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "list": {
    "name": "New List",
    "stringid": "new-list",
    "sender_url": "https://example.com",
    "sender_reminder": "You signed up on our website"
  }
}
JSON
```

#### Update List

```bash
maton api -X PUT '/active-campaign/api/3/lists/{listId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "list": {
    "name": "Updated List Name"
  }
}
JSON
```

**Note:** `{listId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete List

```bash
maton api '/active-campaign/api/3/lists/{listId}' -X DELETE
```

**Note:** `{listId}` is a placeholder. Replace it with a real value before sending the request.

### Contact Lists API

#### Subscribe Contact to List

```bash
maton api -X POST '/active-campaign/api/3/contactLists' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contactList": {
    "contact": "2",
    "list": "1",
    "status": "1"
  }
}
JSON
```

Status values: `1` = subscribed, `2` = unsubscribed

### Deals API

#### List Deals

```bash
maton api '/active-campaign/api/3/deals'
```

**Query parameters:**
- `search` - Search by title, contact, or org
- `filters[stage]` - Filter by stage ID
- `filters[owner]` - Filter by owner ID

**Response:**
```json
{
  "deals": [
    {
      "id": "1",
      "title": "New Deal",
      "value": "10000",
      "currency": "usd",
      "stage": "1",
      "owner": "1"
    }
  ],
  "meta": {
    "total": 0,
    "currencies": []
  }
}
```

#### Get Deal

```bash
maton api '/active-campaign/api/3/deals/{dealId}'
```

**Note:** `{dealId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Deal

```bash
maton api -X POST '/active-campaign/api/3/deals' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "deal": {
    "title": "New Deal",
    "value": "10000",
    "currency": "usd",
    "contact": "2",
    "stage": "1",
    "owner": "1"
  }
}
JSON
```

#### Update Deal

```bash
maton api -X PUT '/active-campaign/api/3/deals/{dealId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "deal": {
    "title": "Updated Deal",
    "value": "15000"
  }
}
JSON
```

**Note:** `{dealId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Deal

```bash
maton api '/active-campaign/api/3/deals/{dealId}' -X DELETE
```

**Note:** `{dealId}` is a placeholder. Replace it with a real value before sending the request.

### Deal Stages & Pipelines API

#### List Deal Stages

```bash
maton api '/active-campaign/api/3/dealStages'
```

#### Create Deal Stage

```bash
maton api -X POST '/active-campaign/api/3/dealStages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "dealStage": {
    "title": "New Stage",
    "group": "1",
    "order": "1"
  }
}
JSON
```

#### List Pipelines

```bash
maton api '/active-campaign/api/3/dealGroups'
```

#### Create Pipeline

```bash
maton api -X POST '/active-campaign/api/3/dealGroups' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "dealGroup": {
    "title": "Sales Pipeline",
    "currency": "usd"
  }
}
JSON
```

### Automations API

#### List Automations

```bash
maton api '/active-campaign/api/3/automations'
```

**Response:**
```json
{
  "automations": [
    {
      "id": "1",
      "name": "Welcome Series",
      "cdate": "2026-02-09T14:00:00-06:00",
      "mdate": "2026-02-09T14:00:00-06:00",
      "status": "1"
    }
  ],
  "meta": {
    "total": "1"
  }
}
```

#### Get Automation

```bash
maton api '/active-campaign/api/3/automations/{automationId}'
```

**Note:** `{automationId}` is a placeholder. Replace it with a real value before sending the request.

### Campaigns API

#### List Campaigns

```bash
maton api '/active-campaign/api/3/campaigns'
```

**Response:**
```json
{
  "campaigns": [
    {
      "id": "1",
      "name": "Newsletter",
      "type": "single",
      "status": "0"
    }
  ],
  "meta": {
    "total": "1"
  }
}
```

#### Get Campaign

```bash
maton api '/active-campaign/api/3/campaigns/{campaignId}'
```

**Note:** `{campaignId}` is a placeholder. Replace it with a real value before sending the request.

### Users API

#### List Users

```bash
maton api '/active-campaign/api/3/users'
```

**Response:**
```json
{
  "users": [
    {
      "id": "1",
      "username": "admin",
      "firstName": "John",
      "lastName": "Doe",
      "email": "admin@example.com"
    }
  ]
}
```

#### Get User

```bash
maton api '/active-campaign/api/3/users/{userId}'
```

**Note:** `{userId}` is a placeholder. Replace it with a real value before sending the request.

### Accounts API

#### List Accounts

```bash
maton api '/active-campaign/api/3/accounts'
```

#### Create Account

```bash
maton api -X POST '/active-campaign/api/3/accounts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "account": {
    "name": "Acme Inc"
  }
}
JSON
```

### Custom Fields API

#### List Fields

```bash
maton api '/active-campaign/api/3/fields'
```

#### Create Field

```bash
maton api -X POST '/active-campaign/api/3/fields' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "field": {
    "type": "text",
    "title": "Custom Field",
    "descript": "A custom field"
  }
}
JSON
```

### Field Values API

#### Update Contact Field Value

```bash
maton api -X PUT '/active-campaign/api/3/fieldValues/{fieldValueId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "fieldValue": {
    "value": "New Value"
  }
}
JSON
```

**Note:** `{fieldValueId}` is a placeholder. Replace it with a real value before sending the request.

### Notes API

#### List Notes

```bash
maton api '/active-campaign/api/3/notes'
```

#### Create Note

```bash
maton api -X POST '/active-campaign/api/3/notes' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "note": {
    "note": "This is a note",
    "relid": "2",
    "reltype": "Subscriber"
  }
}
JSON
```

### Webhooks API

#### List Webhooks

```bash
maton api '/active-campaign/api/3/webhooks'
```

#### Create Webhook

```bash
maton api -X POST '/active-campaign/api/3/webhooks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "webhook": {
    "name": "My Webhook",
    "url": "https://example.com/webhook",
    "events": ["subscribe", "unsubscribe"],
    "sources": ["public", "admin"]
  }
}
JSON
```

### Pagination

ActiveCampaign uses offset-based pagination:

```bash
maton api '/active-campaign/api/3/contacts?limit=20&offset=0'
```

**Query parameters:**
- `limit` - Results per page (default: 20)
- `offset` - Starting index

**Response:**
```json
{
  "contacts": [...],
  "meta": {
    "total": "150"
  }
}
```

For large datasets, use `orders[id]=ASC` and `id_greater` parameter for better performance:
```bash
maton api '/active-campaign/api/3/contacts?orders[id]=ASC&id_greater=100'
```

### Notes

- All endpoints require the `/api/3/` prefix
- Request bodies use singular resource names wrapped in an object (e.g., `{"contact": {...}}`)
- IDs are returned as strings
- Timestamps are in ISO 8601 format with timezone
- Rate limit: 5 requests per second per account
- DELETE operations return 200 OK (not 204)

### Resources

- [ActiveCampaign API Overview](https://developers.activecampaign.com/reference/overview)
- [ActiveCampaign Developer Portal](https://developers.activecampaign.com/)
- [Contacts API](https://developers.activecampaign.com/reference/list-all-contacts)
- [Tags API](https://developers.activecampaign.com/reference/contact-tags)
- [Deals API](https://developers.activecampaign.com/reference/list-all-deals)
- [Maton CLI Manual](https://cli.maton.ai/manual)
