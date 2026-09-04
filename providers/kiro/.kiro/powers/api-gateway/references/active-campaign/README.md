# ActiveCampaign Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `active-campaign`
**Base URL proxied:** `{account}.api-us1.com`

## API Path Pattern

```
/active-campaign/api/3/{resource}
```

## Common Endpoints

### Contacts

#### List Contacts
```bash
maton api '/active-campaign/api/3/contacts'
```

#### Get Contact
```bash
maton api '/active-campaign/api/3/contacts/{contactId}'
```

#### Create Contact
```bash
maton api -X POST '/active-campaign/api/3/contacts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "contact": {
    "email": "user@example.com",
    "firstName": "John",
    "lastName": "Doe"
  }
}
EOF
```

#### Update Contact
```bash
maton api -X PUT '/active-campaign/api/3/contacts/{contactId}'
```

#### Delete Contact
```bash
maton api -X DELETE '/active-campaign/api/3/contacts/{contactId}'
```

### Tags

#### List Tags
```bash
maton api '/active-campaign/api/3/tags'
```

#### Create Tag
```bash
maton api -X POST '/active-campaign/api/3/tags' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "tag": {
    "tag": "Tag Name",
    "tagType": "contact"
  }
}
EOF
```

### Contact Tags

#### Add Tag to Contact
```bash
maton api -X POST '/active-campaign/api/3/contactTags' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "contactTag": {
    "contact": "1",
    "tag": "1"
  }
}
EOF
```

#### Remove Tag from Contact
```bash
maton api -X DELETE '/active-campaign/api/3/contactTags/{contactTagId}'
```

### Lists

#### List All Lists
```bash
maton api '/active-campaign/api/3/lists'
```

#### Create List
```bash
maton api -X POST '/active-campaign/api/3/lists'
```

### Deals

#### List Deals
```bash
maton api '/active-campaign/api/3/deals'
```

#### Create Deal
```bash
maton api -X POST '/active-campaign/api/3/deals' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "deal": {
    "title": "New Deal",
    "value": "10000",
    "currency": "usd",
    "contact": "1",
    "stage": "1"
  }
}
EOF
```

### Deal Stages & Pipelines

#### List Deal Stages
```bash
maton api '/active-campaign/api/3/dealStages'
```

#### List Pipelines (Deal Groups)
```bash
maton api '/active-campaign/api/3/dealGroups'
```

### Automations

#### List Automations
```bash
maton api '/active-campaign/api/3/automations'
```

### Campaigns

#### List Campaigns
```bash
maton api '/active-campaign/api/3/campaigns'
```

### Users

#### List Users
```bash
maton api '/active-campaign/api/3/users'
```

### Accounts

#### List Accounts
```bash
maton api '/active-campaign/api/3/accounts'
```

### Custom Fields

#### List Fields
```bash
maton api '/active-campaign/api/3/fields'
```

### Notes

#### List Notes
```bash
maton api '/active-campaign/api/3/notes'
```

### Webhooks

#### List Webhooks
```bash
maton api '/active-campaign/api/3/webhooks'
```

## Pagination

Uses offset-based pagination:

```bash
maton api '/active-campaign/api/3/contacts?limit=20&offset=0'
```

**Parameters:**
- `limit` - Results per page (default: 20)
- `offset` - Starting index

Response includes meta with total:
```json
{
  "contacts": [...],
  "meta": {
    "total": "150"
  }
}
```

## Notes

- All endpoints require `/api/3/` prefix
- Request bodies use singular resource names (e.g., `{"contact": {...}}`)
- IDs returned as strings
- Rate limit: 5 requests per second per account
- DELETE returns 200 OK (not 204)

## Resources

- [ActiveCampaign API Overview](https://developers.activecampaign.com/reference/overview)
- [Developer Portal](https://developers.activecampaign.com/)
- [Contacts API](https://developers.activecampaign.com/reference/list-all-contacts)
