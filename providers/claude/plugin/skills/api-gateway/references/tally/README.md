# Tally Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `tally`
**Base URL proxied:** `api.tally.so`

## API Path Pattern

```
/tally/{resource}
```

Tally's API does not use version prefixes in paths.

## Required Headers

THe `User-Agent` header is required to avoid Cloudflare blocks:

```
User-Agent: Maton/1.0
```

## Common Endpoints

### Get Current User
```bash
maton api '/tally/users/me'
```

### List Forms
```bash
maton api '/tally/forms'
```

**Query Parameters:**
- `page` - Page number (default: 1)
- `limit` - Items per page (default: 50)

### Get Form
```bash
maton api '/tally/forms/{formId}'
```

### Create Form
```bash
maton api -X POST '/tally/forms' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "status": "DRAFT",
  "blocks": [
    {
      "type": "FORM_TITLE",
      "uuid": "11111111-1111-1111-1111-111111111111",
      "groupUuid": "22222222-2222-2222-2222-222222222222",
      "groupType": "FORM_TITLE",
      "title": "My Form",
      "payload": {}
    },
    {
      "type": "INPUT_TEXT",
      "uuid": "33333333-3333-3333-3333-333333333333",
      "groupUuid": "44444444-4444-4444-4444-444444444444",
      "groupType": "INPUT_TEXT",
      "title": "Your name",
      "payload": {}
    }
  ]
}
EOF
```

### Update Form
```bash
maton api -X PATCH '/tally/forms/{formId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Updated Form Name",
  "status": "PUBLISHED"
}
EOF
```

### Delete Form
```bash
maton api -X DELETE '/tally/forms/{formId}'
```

### List Form Questions
```bash
maton api '/tally/forms/{formId}/questions'
```

### List Form Submissions
```bash
maton api '/tally/forms/{formId}/submissions'
```

**Query Parameters:**
- `page` - Page number
- `limit` - Items per page
- `startDate` - Filter by start date (ISO 8601)
- `endDate` - Filter by end date (ISO 8601)
- `afterId` - Cursor for pagination

### Get Submission
```bash
maton api '/tally/forms/{formId}/submissions/{submissionId}'
```

### Delete Submission
```bash
maton api -X DELETE '/tally/forms/{formId}/submissions/{submissionId}'
```

### List Workspaces
```bash
maton api '/tally/workspaces'
```

### Get Workspace
```bash
maton api '/tally/workspaces/{workspaceId}'
```

### Create Workspace
```bash
maton api -X POST '/tally/workspaces' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "New Workspace"
}
EOF
```

### List Organization Users
```bash
maton api '/tally/organizations/{organizationId}/users'
```

### List Organization Invites
```bash
maton api '/tally/organizations/{organizationId}/invites'
```

### List Webhooks
```bash
maton api '/tally/webhooks'
```

### Create Webhook
```bash
maton api -X POST '/tally/webhooks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "formId": "GxdRaQ",
  "url": "https://your-endpoint.com/webhook",
  "eventTypes": ["FORM_RESPONSE"]
}
EOF
```

## Notes

- Form and workspace IDs are short alphanumeric strings (e.g., `GxdRaQ`, `3jW9Q1`)
- Block `uuid` and `groupUuid` fields must be valid UUIDs (GUIDs)
- Page-based pagination with `page` and `limit` parameters
- Rate limit: 100 requests per minute
- API is in public beta and subject to changes
- Creating workspaces requires a Pro subscription

## Resources

- [Tally API Introduction](https://developers.tally.so/api-reference/introduction)
- [Tally API Reference](https://developers.tally.so/llms.txt)
- [Tally Help Center](https://help.tally.so/)
