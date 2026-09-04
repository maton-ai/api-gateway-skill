# Systeme.io Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `systeme`
**Base URL proxied:** `api.systeme.io`

## API Path Pattern

```
/systeme/api/{resource}
```

## Common Endpoints

### List Contacts
```bash
maton api '/systeme/api/contacts'
```

Query parameters:
- `limit` - Results per page (10-100)
- `startingAfter` - ID of last item for pagination
- `order` - Sort order: `asc` or `desc` (default: `desc`)

### Get Contact
```bash
maton api '/systeme/api/contacts/{id}'
```

### Create Contact
```bash
maton api -X POST '/systeme/api/contacts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "email": "user@example.com",
  "firstName": "John",
  "lastName": "Doe"
}
EOF
```

### Update Contact
```bash
maton api -X PATCH '/systeme/api/contacts/{id}' \
  -H 'Content-Type: application/merge-patch+json' \
  --input - <<'EOF'
{
  "firstName": "Jane"
}
EOF
```

### Delete Contact
```bash
maton api -X DELETE '/systeme/api/contacts/{id}'
```

### List Tags
```bash
maton api '/systeme/api/tags'
```

### Create Tag
```bash
maton api -X POST '/systeme/api/tags' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "VIP Customer"
}
EOF
```

### Update Tag
```bash
maton api -X PUT '/systeme/api/tags/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Premium Customer"
}
EOF
```

### Delete Tag
```bash
maton api -X DELETE '/systeme/api/tags/{id}'
```

### Assign Tag to Contact
```bash
maton api -X POST '/systeme/api/contacts/{id}/tags' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "tagId": 12345
}
EOF
```

### Remove Tag from Contact
```bash
maton api -X DELETE '/systeme/api/contacts/{id}/tags/{tagId}'
```

### List Contact Fields
```bash
maton api '/systeme/api/contact_fields'
```

### List Courses
```bash
maton api '/systeme/api/school/courses'
```

### Create Enrollment
```bash
maton api -X POST '/systeme/api/school/courses/{courseId}/enrollments' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "contactId": 12345
}
EOF
```

### List Enrollments
```bash
maton api '/systeme/api/school/enrollments'
```

### Delete Enrollment
```bash
maton api -X DELETE '/systeme/api/school/enrollments/{id}'
```

### List Communities
```bash
maton api '/systeme/api/community/communities'
```

### Create Membership
```bash
maton api -X POST '/systeme/api/community/communities/{communityId}/memberships' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "contactId": 12345
}
EOF
```

### List Memberships
```bash
maton api '/systeme/api/community/memberships'
```

### Delete Membership
```bash
maton api -X DELETE '/systeme/api/community/memberships/{id}'
```

### List Subscriptions
```bash
maton api '/systeme/api/payment/subscriptions'
```

### Cancel Subscription
```bash
maton api -X POST '/systeme/api/payment/subscriptions/{id}/cancel'
```

### List Webhooks
```bash
maton api '/systeme/api/webhooks'
```

### Create Webhook
```bash
maton api -X POST '/systeme/api/webhooks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "My Webhook",
  "url": "https://example.com/webhook",
  "secret": "my-secret-key",
  "subscriptions": ["CONTACT_CREATED"]
}
EOF
```

Available events: `CONTACT_CREATED`, `CONTACT_TAG_ADDED`, `CONTACT_TAG_REMOVED`, `CONTACT_OPT_IN`, `SALE_NEW`, `SALE_CANCELED`

### Update Webhook
```bash
maton api -X PATCH '/systeme/api/webhooks/{id}' \
  -H 'Content-Type: application/merge-patch+json' \
  --input - <<'EOF'
{
  "name": "Updated Webhook Name"
}
EOF
```

### Delete Webhook
```bash
maton api -X DELETE '/systeme/api/webhooks/{id}'
```

## Notes

- Contact, tag, course, and enrollment IDs are numeric integers
- Webhook IDs are UUIDs
- Uses cursor-based pagination with `startingAfter` parameter
- PATCH requests require `Content-Type: application/merge-patch+json`
- Delete operations return 204 No Content
- Email addresses are validated for real MX records
- Payment/subscription endpoints may return 404 if not configured

## Resources

- [Systeme.io API Reference](https://developer.systeme.io/reference)
- [Systeme.io Developer Documentation](https://developer.systeme.io/)
