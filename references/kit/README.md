# Kit Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `kit`
**Base URL proxied:** `api.kit.com`

## API Path Pattern

```
/kit/v4/{resource}
```

## Common Endpoints

### List Subscribers
```bash
maton api '/kit/v4/subscribers'
```

Query parameters:
- `per_page` - Results per page (default: 500, max: 1000)
- `after` - Cursor for next page
- `before` - Cursor for previous page
- `status` - Filter by: `active`, `inactive`, `bounced`, `complained`, `cancelled`, or `all`
- `email_address` - Filter by specific email

### Get Subscriber
```bash
maton api '/kit/v4/subscribers/{id}'
```

### Create Subscriber
```bash
maton api -X POST '/kit/v4/subscribers' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "email_address": "user@example.com",
  "first_name": "John"
}
EOF
```

### Update Subscriber
```bash
maton api -X PUT '/kit/v4/subscribers/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "first_name": "Updated Name"
}
EOF
```

### List Tags
```bash
maton api '/kit/v4/tags'
```

### Create Tag
```bash
maton api -X POST '/kit/v4/tags' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "new-tag"
}
EOF
```

### Update Tag
```bash
maton api -X PUT '/kit/v4/tags/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "updated-tag-name"
}
EOF
```

### Delete Tag
```bash
maton api -X DELETE '/kit/v4/tags/{id}'
```

### Tag a Subscriber
```bash
maton api -X POST '/kit/v4/tags/{tag_id}/subscribers' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "email_address": "user@example.com"
}
EOF
```

### Remove Tag from Subscriber
```bash
maton api -X DELETE '/kit/v4/tags/{tag_id}/subscribers/{subscriber_id}'
```

### List Subscribers with Tag
```bash
maton api '/kit/v4/tags/{tag_id}/subscribers'
```

### List Forms
```bash
maton api '/kit/v4/forms'
```

### Add Subscriber to Form
```bash
maton api -X POST '/kit/v4/forms/{form_id}/subscribers' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "email_address": "user@example.com"
}
EOF
```

### List Form Subscribers
```bash
maton api '/kit/v4/forms/{form_id}/subscribers'
```

### List Sequences
```bash
maton api '/kit/v4/sequences'
```

### Add Subscriber to Sequence
```bash
maton api -X POST '/kit/v4/sequences/{sequence_id}/subscribers' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "email_address": "user@example.com"
}
EOF
```

### List Broadcasts
```bash
maton api '/kit/v4/broadcasts'
```

### List Segments
```bash
maton api '/kit/v4/segments'
```

### List Custom Fields
```bash
maton api '/kit/v4/custom_fields'
```

### Create Custom Field
```bash
maton api -X POST '/kit/v4/custom_fields' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "label": "Company"
}
EOF
```

### Update Custom Field
```bash
maton api -X PUT '/kit/v4/custom_fields/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "label": "Company Name"
}
EOF
```

### Delete Custom Field
```bash
maton api -X DELETE '/kit/v4/custom_fields/{id}'
```

### List Email Templates
```bash
maton api '/kit/v4/email_templates'
```

### List Purchases
```bash
maton api '/kit/v4/purchases'
```

### List Webhooks
```bash
maton api '/kit/v4/webhooks'
```

### Create Webhook
```bash
maton api -X POST '/kit/v4/webhooks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "target_url": "https://example.com/webhook",
  "event": {"name": "subscriber.subscriber_activate"}
}
EOF
```

### Delete Webhook
```bash
maton api -X DELETE '/kit/v4/webhooks/{id}'
```

## Notes

- Kit API uses V4 (V3 is deprecated)
- Subscriber IDs are integers
- Custom field keys are auto-generated from labels
- Uses cursor-based pagination with `after` and `before` parameters
- Delete operations return 204 No Content
- Bulk operations (>100 items) are processed asynchronously

## Resources

- [Kit API Overview](https://developers.kit.com/api-reference/overview)
- [Kit API Reference](https://developers.kit.com/api-reference)
- [Kit Developer Documentation](https://developers.kit.com)
