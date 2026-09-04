# Keap Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `keap`
**Base URL proxied:** `api.infusionsoft.com/crm/rest`

## API Path Pattern

```
/keap/crm/rest/v2/{resource}
```

Note: The `/crm/rest` prefix is required in the path.

## Common Endpoints

### Get Current User
```bash
maton api '/keap/crm/rest/v2/oauth/connect/userinfo'
```

### List Contacts
```bash
maton api '/keap/crm/rest/v2/contacts'
```

Query parameters: `page_size`, `page_token`, `filter`, `order_by`, `fields`

### Get Contact
```bash
maton api '/keap/crm/rest/v2/contacts/{contact_id}'
```

### Create Contact
```bash
maton api -X POST '/keap/crm/rest/v2/contacts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "given_name": "John",
  "family_name": "Doe",
  "email_addresses": [{"email": "john@example.com", "field": "EMAIL1"}]
}
EOF
```

### Update Contact
```bash
maton api -X PATCH '/keap/crm/rest/v2/contacts/{contact_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "given_name": "Jane"
}
EOF
```

### Delete Contact
```bash
maton api -X DELETE '/keap/crm/rest/v2/contacts/{contact_id}'
```

### List Companies
```bash
maton api '/keap/crm/rest/v2/companies'
```

### List Tags
```bash
maton api '/keap/crm/rest/v2/tags'
```

### Apply Tags to Contacts
```bash
maton api -X POST '/keap/crm/rest/v2/tags/{tag_id}/contacts:applyTags' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "contact_ids": ["1", "2", "3"]
}
EOF
```

### List Tasks
```bash
maton api '/keap/crm/rest/v2/tasks'
```

### Create Task
```bash
maton api -X POST '/keap/crm/rest/v2/tasks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "Follow up call",
  "due_date": "2026-02-15T10:00:00Z",
  "contact": {"id": "9"}
}
EOF
```

### List Opportunities
```bash
maton api '/keap/crm/rest/v2/opportunities'
```

### List Orders
```bash
maton api '/keap/crm/rest/v2/orders'
```

### List Products
```bash
maton api '/keap/crm/rest/v2/products'
```

### List Campaigns
```bash
maton api '/keap/crm/rest/v2/campaigns'
```

### Add Contacts to Campaign Sequence
```bash
maton api -X POST '/keap/crm/rest/v2/campaigns/{campaign_id}/sequences/{sequence_id}:addContacts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "contact_ids": ["1", "2"]
}
EOF
```

### List Emails
```bash
maton api '/keap/crm/rest/v2/emails'
```

### Send Email
```bash
maton api -X POST '/keap/crm/rest/v2/emails:send' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "contacts": [{"id": "9"}],
  "subject": "Hello",
  "html_content": "<p>Email body</p>"
}
EOF
```

### List Automations
```bash
maton api '/keap/crm/rest/v2/automations'
```

### List Affiliates
```bash
maton api '/keap/crm/rest/v2/affiliates'
```

### List Subscriptions
```bash
maton api '/keap/crm/rest/v2/subscriptions'
```

## Pagination

Uses token-based pagination:

```bash
maton api '/keap/crm/rest/v2/contacts?page_size=50'
maton api '/keap/crm/rest/v2/contacts?page_size=50&page_token=NEXT_TOKEN'
```

Response includes `next_page_token` (empty when no more pages).

## Filtering

Use the `filter` parameter:

```bash
maton api '/keap/crm/rest/v2/contacts?filter=given_name==John'
maton api '/keap/crm/rest/v2/tasks?filter=completed==false'
```

## Notes

- API version is v2 (v1 is deprecated)
- Path must include `/crm/rest` prefix
- IDs are returned as strings
- Maximum `page_size` is 1000
- Timestamps use ISO 8601 format

## Resources

- [Keap Developer Portal](https://developer.infusionsoft.com/)
- [Keap REST API V2 Documentation](https://developer.infusionsoft.com/docs/restv2/)
