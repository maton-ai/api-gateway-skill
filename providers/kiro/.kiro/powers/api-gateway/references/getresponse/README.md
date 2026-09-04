# GetResponse Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `getresponse`
**Base URL proxied:** `api.getresponse.com`

## API Path Pattern

```
/getresponse/v3/{resource}
```

## Common Endpoints

### Get Account Details
```bash
maton api '/getresponse/v3/accounts'
```

### List Campaigns
```bash
maton api '/getresponse/v3/campaigns'
```

Query parameters:
- `page` - Page number (starts at 1)
- `perPage` - Records per page (max 1000)

### Get Campaign
```bash
maton api '/getresponse/v3/campaigns/{campaignId}'
```

### Create Campaign
```bash
maton api -X POST '/getresponse/v3/campaigns' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "My Campaign"
}
EOF
```

### List Contacts
```bash
maton api '/getresponse/v3/contacts?page=1&perPage=100'
```

Query parameters:
- `query[campaignId]` - Filter by campaign
- `query[email]` - Filter by email
- `sort[createdOn]` - Sort by creation date (asc/desc)

### Get Contact
```bash
maton api '/getresponse/v3/contacts/{contactId}'
```

### Create Contact
```bash
maton api -X POST '/getresponse/v3/contacts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "email": "user@example.com",
  "name": "John Doe",
  "campaign": {
    "campaignId": "abc123"
  }
}
EOF
```

### Update Contact
```bash
maton api -X POST '/getresponse/v3/contacts/{contactId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Jane Doe"
}
EOF
```

### Delete Contact
```bash
maton api -X DELETE '/getresponse/v3/contacts/{contactId}'
```

### List Custom Fields
```bash
maton api '/getresponse/v3/custom-fields'
```

### List Tags
```bash
maton api '/getresponse/v3/tags'
```

### Create Tag
```bash
maton api -X POST '/getresponse/v3/tags' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "VIP Customer"
}
EOF
```

### List Segments
```bash
maton api '/getresponse/v3/search-contacts'
```

### Get Contacts from Segment
```bash
maton api '/getresponse/v3/search-contacts/{searchContactId}/contacts'
```

### Send Newsletter
```bash
maton api -X POST '/getresponse/v3/newsletters' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "subject": "Newsletter Subject",
  "name": "Internal Name",
  "campaign": {
    "campaignId": "abc123"
  },
  "content": {
    "html": "<html><body>Content</body></html>",
    "plain": "Content"
  },
  "sendOn": "2026-02-15T10:00:00Z"
}
EOF
```

### List Autoresponders
```bash
maton api '/getresponse/v3/autoresponders'
```

### List From Fields
```bash
maton api '/getresponse/v3/from-fields'
```

## Notes

- Campaign IDs and Contact IDs are alphanumeric strings (e.g., "fZ0Xg", "VZ4Sa5g")
- Timestamps are in ISO 8601 format
- Field names use camelCase
- Use page-based pagination with `page` and `perPage` parameters
- Rate limits: 30,000 requests per 10 minutes, 80 requests per second
- "Campaigns" in GetResponse are equivalent to email lists/audiences
- "Search contacts" and "segments" refer to the same resource

## Resources

- [GetResponse API Documentation](https://apidocs.getresponse.com/v3)
- [GetResponse OpenAPI Spec](https://apireference.getresponse.com/open-api.json)
- [GetResponse Help Center](https://www.getresponse.com/help)
