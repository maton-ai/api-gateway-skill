# Mailchimp Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `mailchimp`
**Base URL proxied:** `{dc}.api.mailchimp.com`

## API Path Pattern

```
/mailchimp/3.0/{resource}
```

## Common Endpoints

### Get All Lists (Audiences)
```bash
maton api '/mailchimp/3.0/lists'
```

Query parameters:
- `count` - Number of records to return (default 10, max 1000)
- `offset` - Number of records to skip (for pagination)

### Get a List
```bash
maton api '/mailchimp/3.0/lists/{list_id}'
```

### Create a List
```bash
maton api -X POST '/mailchimp/3.0/lists' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
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
EOF
```

### Get List Members
```bash
maton api '/mailchimp/3.0/lists/{list_id}/members?status=subscribed&count=50'
```

### Add a Member
```bash
maton api -X POST '/mailchimp/3.0/lists/{list_id}/members' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "email_address": "newuser@example.com",
  "status": "subscribed",
  "merge_fields": {
    "FNAME": "Jane",
    "LNAME": "Smith"
  }
}
EOF
```

### Update a Member
```bash
maton api -X PATCH '/mailchimp/3.0/lists/{list_id}/members/{subscriber_hash}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "merge_fields": {
    "FNAME": "Jane",
    "LNAME": "Doe"
  }
}
EOF
```

### Add or Update a Member (Upsert)
```bash
maton api -X PUT '/mailchimp/3.0/lists/{list_id}/members/{subscriber_hash}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "email_address": "user@example.com",
  "status_if_new": "subscribed",
  "merge_fields": {
    "FNAME": "Jane",
    "LNAME": "Smith"
  }
}
EOF
```

### Delete a Member
```bash
maton api -X DELETE '/mailchimp/3.0/lists/{list_id}/members/{subscriber_hash}'
```

### Add or Remove Tags
```bash
maton api -X POST '/mailchimp/3.0/lists/{list_id}/members/{subscriber_hash}/tags' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "tags": [
    {"name": "VIP", "status": "active"},
    {"name": "Old Tag", "status": "inactive"}
  ]
}
EOF
```

### Get Segments
```bash
maton api '/mailchimp/3.0/lists/{list_id}/segments'
```

### Get All Campaigns
```bash
maton api '/mailchimp/3.0/campaigns?status=sent&count=20'
```

### Create a Campaign
```bash
maton api -X POST '/mailchimp/3.0/campaigns' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "type": "regular",
  "recipients": {
    "list_id": "LIST_ID"
  },
  "settings": {
    "subject_line": "Your Monthly Update",
    "from_name": "Acme Corp",
    "reply_to": "hello@acme.com"
  }
}
EOF
```

### Set Campaign Content
```bash
maton api -X PUT '/mailchimp/3.0/campaigns/{campaign_id}/content' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "html": "<html><body><h1>Hello!</h1><p>Newsletter content here.</p></body></html>",
  "plain_text": "Hello! Newsletter content here."
}
EOF
```

### Send a Campaign
```bash
maton api -X POST '/mailchimp/3.0/campaigns/{campaign_id}/actions/send'
```

### Schedule a Campaign
```bash
maton api -X POST '/mailchimp/3.0/campaigns/{campaign_id}/actions/schedule' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "schedule_time": "2025-03-01T10:00:00+00:00"
}
EOF
```

### Get All Templates
```bash
maton api '/mailchimp/3.0/templates?type=user'
```

### Get All Automations
```bash
maton api '/mailchimp/3.0/automations'
```

### Start an Automation
```bash
maton api -X POST '/mailchimp/3.0/automations/{workflow_id}/actions/start-all-emails'
```

### Get Campaign Reports
```bash
maton api '/mailchimp/3.0/reports?count=20'
```

### Get Campaign Report
```bash
maton api '/mailchimp/3.0/reports/{campaign_id}'
```

## Notes

- List IDs are 10-character alphanumeric strings
- Subscriber hashes are MD5 hashes of lowercase email addresses
- Timestamps are in ISO 8601 format
- Maximum 1000 records per request for list endpoints
- "Audience" and "list" are used interchangeably (app vs API terminology)
- "Contact" and "member" are used interchangeably (app vs API terminology)
- Use offset-based pagination with `count` and `offset` parameters

## Resources

- [Mailchimp Marketing API Documentation](https://mailchimp.com/developer/marketing/)
- [API Reference](https://mailchimp.com/developer/marketing/api/)
- [Quick Start Guide](https://mailchimp.com/developer/marketing/guides/quick-start/)
