# Brevo Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `brevo`
**Base URL proxied:** `api.brevo.com`

## API Path Pattern

```
/brevo/v3/{resource}
```

## Common Endpoints

### Account

```bash
maton api '/brevo/v3/account'
```

### Contacts

#### List Contacts
```bash
maton api '/brevo/v3/contacts?limit=50&offset=0'
```

#### Get Contact
```bash
maton api '/brevo/v3/contacts/{identifier}'
```

#### Create Contact
```bash
maton api -X POST '/brevo/v3/contacts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "email": "contact@example.com",
  "attributes": {"FIRSTNAME": "John", "LASTNAME": "Doe"},
  "listIds": [2]
}
EOF
```

#### Update Contact
```bash
maton api -X PUT '/brevo/v3/contacts/{identifier}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "attributes": {"FIRSTNAME": "Updated"}
}
EOF
```

#### Delete Contact
```bash
maton api -X DELETE '/brevo/v3/contacts/{identifier}'
```

### Lists

#### List All Lists
```bash
maton api '/brevo/v3/contacts/lists'
```

#### Create List
```bash
maton api -X POST '/brevo/v3/contacts/lists' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "New List",
  "folderId": 1
}
EOF
```

#### Add Contacts to List
```bash
maton api -X POST '/brevo/v3/contacts/lists/{listId}/contacts/add' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "emails": ["contact@example.com"]
}
EOF
```

### Folders

#### List Folders
```bash
maton api '/brevo/v3/contacts/folders'
```

#### Create Folder
```bash
maton api -X POST '/brevo/v3/contacts/folders' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "New Folder"
}
EOF
```

### Transactional Emails

#### Send Email
```bash
maton api -X POST '/brevo/v3/smtp/email' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "sender": {"name": "John", "email": "john@example.com"},
  "to": [{"email": "recipient@example.com", "name": "Jane"}],
  "subject": "Hello!",
  "htmlContent": "<html><body><h1>Hi!</h1></body></html>"
}
EOF
```

#### Get Email Statistics
```bash
maton api '/brevo/v3/smtp/statistics/events?limit=50'
```

### Email Templates

#### List Templates
```bash
maton api '/brevo/v3/smtp/templates'
```

#### Create Template
```bash
maton api -X POST '/brevo/v3/smtp/templates' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "sender": {"name": "Company", "email": "noreply@company.com"},
  "templateName": "Welcome Email",
  "subject": "Welcome {{params.name}}!",
  "htmlContent": "<html><body><h1>Hello {{params.name}}!</h1></body></html>"
}
EOF
```

### Email Campaigns

#### List Campaigns
```bash
maton api '/brevo/v3/emailCampaigns'
```

#### Create Campaign
```bash
maton api -X POST '/brevo/v3/emailCampaigns' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Newsletter",
  "subject": "Monthly Update",
  "sender": {"name": "Company", "email": "news@company.com"},
  "htmlContent": "<html><body><h1>News</h1></body></html>",
  "recipients": {"listIds": [2]}
}
EOF
```

#### Send Campaign
```bash
maton api -X POST '/brevo/v3/emailCampaigns/{campaignId}/sendNow'
```

### Senders

#### List Senders
```bash
maton api '/brevo/v3/senders'
```

#### Create Sender
```bash
maton api -X POST '/brevo/v3/senders' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Marketing",
  "email": "marketing@company.com"
}
EOF
```

### Attributes

#### List Attributes
```bash
maton api '/brevo/v3/contacts/attributes'
```

## Pagination

Brevo uses offset-based pagination:

```bash
maton api '/brevo/v3/contacts?limit=50&offset=0'
```

**Parameters:**
- `limit` - Results per page (max varies by endpoint, typically 500)
- `offset` - Starting index (0-based)

Response includes count:
```json
{
  "contacts": [...],
  "count": 150
}
```

## Notes

- All endpoints require `/v3/` prefix
- Attribute names must be UPPERCASE
- Contact identifiers: email, phone, or ID
- Template parameters: `{{params.name}}` syntax
- PUT/DELETE return 204 No Content on success
- Rate limit: 300 calls/min (free), higher on paid plans

## Resources

- [Brevo API Overview](https://developers.brevo.com/)
- [API Key Concepts](https://developers.brevo.com/docs/how-it-works)
- [Manage Contacts](https://developers.brevo.com/docs/synchronise-contact-lists)
- [Send Transactional Email](https://developers.brevo.com/docs/send-a-transactional-email)
