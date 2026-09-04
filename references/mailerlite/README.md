# MailerLite Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `mailerlite`
**Base URL proxied:** `connect.mailerlite.com`

## API Path Pattern

```
/mailerlite/api/{resource}
```

## Common Endpoints

### Subscribers

#### List Subscribers
```bash
maton api '/mailerlite/api/subscribers'
```

Query parameters: `filter[status]`, `limit`, `cursor`, `include`

#### Get Subscriber
```bash
maton api '/mailerlite/api/subscribers/{subscriber_id_or_email}'
```

#### Create/Upsert Subscriber
```bash
maton api -X POST '/mailerlite/api/subscribers' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "email": "subscriber@example.com",
  "fields": {"name": "John Doe"},
  "groups": ["12345678901234567"],
  "status": "active"
}
EOF
```

#### Update Subscriber
```bash
maton api -X PUT '/mailerlite/api/subscribers/{subscriber_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "fields": {"name": "Jane Doe"}
}
EOF
```

#### Delete Subscriber
```bash
maton api -X DELETE '/mailerlite/api/subscribers/{subscriber_id}'
```

### Groups

#### List Groups
```bash
maton api '/mailerlite/api/groups'
```

Query parameters: `limit`, `page`, `filter[name]`, `sort`

#### Create Group
```bash
maton api -X POST '/mailerlite/api/groups' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Newsletter Subscribers"
}
EOF
```

#### Update Group
```bash
maton api -X PUT '/mailerlite/api/groups/{group_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Updated Group Name"
}
EOF
```

#### Delete Group
```bash
maton api -X DELETE '/mailerlite/api/groups/{group_id}'
```

#### Get Group Subscribers
```bash
maton api '/mailerlite/api/groups/{group_id}/subscribers'
```

### Campaigns

#### List Campaigns
```bash
maton api '/mailerlite/api/campaigns'
```

Query parameters: `filter[status]`, `filter[type]`, `limit`, `page`

#### Get Campaign
```bash
maton api '/mailerlite/api/campaigns/{campaign_id}'
```

#### Create Campaign
```bash
maton api -X POST '/mailerlite/api/campaigns' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "My Newsletter",
  "type": "regular",
  "emails": [
    {
      "subject": "Weekly Update",
      "from_name": "Newsletter",
      "from": "newsletter@example.com"
    }
  ],
  "groups": ["12345678901234567"]
}
EOF
```

#### Schedule Campaign
```bash
maton api -X POST '/mailerlite/api/campaigns/{campaign_id}/schedule' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "delivery": "instant"
}
EOF
```

#### Delete Campaign
```bash
maton api -X DELETE '/mailerlite/api/campaigns/{campaign_id}'
```

### Automations

#### List Automations
```bash
maton api '/mailerlite/api/automations'
```

Query parameters: `filter[enabled]`, `filter[name]`, `page`, `limit`

#### Get Automation
```bash
maton api '/mailerlite/api/automations/{automation_id}'
```

#### Delete Automation
```bash
maton api -X DELETE '/mailerlite/api/automations/{automation_id}'
```

### Fields

#### List Fields
```bash
maton api '/mailerlite/api/fields'
```

#### Create Field
```bash
maton api -X POST '/mailerlite/api/fields' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Company",
  "type": "text"
}
EOF
```

### Segments

#### List Segments
```bash
maton api '/mailerlite/api/segments'
```

#### Get Segment Subscribers
```bash
maton api '/mailerlite/api/segments/{segment_id}/subscribers'
```

### Forms

#### List Forms
```bash
maton api '/mailerlite/api/forms/{type}'
```

Path parameters: `type` - `popup`, `embedded`, or `promotion`

#### Get Form Subscribers
```bash
maton api '/mailerlite/api/forms/{form_id}/subscribers'
```

### Webhooks

#### List Webhooks
```bash
maton api '/mailerlite/api/webhooks'
```

#### Create Webhook
```bash
maton api -X POST '/mailerlite/api/webhooks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Subscriber Updates",
  "events": ["subscriber.created", "subscriber.updated"],
  "url": "https://example.com/webhook"
}
EOF
```

## Notes

- Rate limit: 120 requests per minute
- Subscriber emails serve as unique identifiers (POST creates or updates existing)
- Only draft campaigns can be updated
- Pagination: cursor-based for subscribers, page-based for groups/campaigns
- API versioning can be overridden via `X-Version: YYYY-MM-DD` header

## Resources

- [MailerLite API Documentation](https://developers.mailerlite.com/docs/)
- [MailerLite Subscribers API](https://developers.mailerlite.com/docs/subscribers.html)
- [MailerLite Groups API](https://developers.mailerlite.com/docs/groups.html)
- [MailerLite Campaigns API](https://developers.mailerlite.com/docs/campaigns.html)
