# Typeform

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `typeform`
**Upstream base URL:** `api.typeform.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.typeform.com/me`
- Gateway: `https://api.maton.ai/typeform/me`

### User API

#### Get Current User

```bash
maton api '/typeform/me'
```

### Forms API

#### List Forms

```bash
maton api '/typeform/forms?page_size=10'
```

#### Get Form

```bash
maton api '/typeform/forms/{formId}'
```

**Note:** `{formId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Form

```bash
maton api -X POST '/typeform/forms' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Customer Survey",
  "fields": [
    {"type": "short_text", "title": "What is your name?"},
    {"type": "email", "title": "What is your email?"}
  ]
}
JSON
```

#### Update Form (Full Replace)

```bash
maton api -X PUT '/typeform/forms/{formId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "Updated Survey Title",
  "fields": [...]
}
EOF
```

**Note:** `{formId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Form (Partial - PATCH)

```bash
maton api -X PATCH '/typeform/forms/{formId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
[
  {"op": "replace", "path": "/title", "value": "New Title"}
]
EOF
```

**Note:** `{formId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Form

```bash
maton api '/typeform/forms/{formId}' -X DELETE
```

**Note:** `{formId}` is a placeholder. Replace it with a real value before sending the request.

### Responses API

#### List Responses

```bash
maton api '/typeform/forms/{formId}/responses?page_size=25'
```

**Note:** `{formId}` is a placeholder. Replace it with a real value before sending the request.

With filters:
```bash
maton api '/typeform/forms/{formId}/responses?since=2024-01-01T00:00:00Z&until=2024-12-31T23:59:59Z'
```

**Note:** `{formId}` is a placeholder. Replace it with a real value before sending the request.

Completed only:
```bash
maton api '/typeform/forms/{formId}/responses?completed=true'
```

**Note:** `{formId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Response

```bash
maton api '/typeform/forms/{formId}/responses?included_response_ids={responseId}' -X DELETE
```

**Note:** `{formId}` and `{responseId}` are placeholders. Replace each of them with real values before sending the request.

### Insights API

#### Get Form Insights

```bash
maton api '/typeform/insights/{formId}/summary'
```

**Note:** `{formId}` is a placeholder. Replace it with a real value before sending the request.

### Workspaces API

#### List Workspaces

```bash
maton api '/typeform/workspaces'
```

#### Get Workspace

```bash
maton api '/typeform/workspaces/{workspaceId}'
```

**Note:** `{workspaceId}` is a placeholder. Replace it with a real value before sending the request.

### Themes API

#### List Themes

```bash
maton api '/typeform/themes'
```

### Images API

#### List Images

```bash
maton api '/typeform/images'
```

### Field Types

- `short_text` - Single line text
- `long_text` - Multi-line text
- `email` - Email address
- `number` - Numeric input
- `rating` - Star rating
- `opinion_scale` - 0-10 scale
- `multiple_choice` - Single or multiple selection
- `yes_no` - Boolean
- `date` - Date picker
- `file_upload` - File attachment
- `dropdown` - Dropdown selection

### Notes

- Form IDs are alphanumeric strings (e.g., `JiLEvIgv`)
- Response pagination uses `before` token for cursor-based pagination
- Timestamps are in ISO 8601 format (e.g., `2026-01-01T00:00:00Z`)
- Responses include `answers` array with field references
- DELETE operations return HTTP 204 (no content) on success
- PATCH uses JSON Patch format (array of operations with `op`, `path`, `value`)

### Resources

- [Typeform API Overview](https://www.typeform.com/developers/get-started)
- [List Forms](https://www.typeform.com/developers/create/reference/retrieve-forms)
- [Get Form](https://www.typeform.com/developers/create/reference/retrieve-form)
- [Create Form](https://www.typeform.com/developers/create/reference/create-form)
- [Update Form](https://www.typeform.com/developers/create/reference/update-form)
- [Update Form Patch](https://www.typeform.com/developers/create/reference/update-form-patch)
- [Delete Form](https://www.typeform.com/developers/create/reference/delete-form)
- [Get Form Messages](https://www.typeform.com/developers/create/reference/retrieve-custom-form-messages)
- [Update Form Messages](https://www.typeform.com/developers/create/reference/update-custom-messages)
- [List Responses](https://www.typeform.com/developers/responses/reference/retrieve-responses)
- [Delete Responses](https://www.typeform.com/developers/responses/reference/delete-responses)
- [List Workspaces](https://www.typeform.com/developers/create/reference/retrieve-workspaces)
- [Get Workspace](https://www.typeform.com/developers/create/reference/retrieve-workspace)
- [Create Workspace](https://www.typeform.com/developers/create/reference/create-workspace)
- [Update Workspace](https://www.typeform.com/developers/create/reference/update-workspace)
- [Delete Workspace](https://www.typeform.com/developers/create/reference/delete-workspace)
- [List Themes](https://www.typeform.com/developers/create/reference/retrieve-themes)
- [Get Theme](https://www.typeform.com/developers/create/reference/retrieve-theme)
- [Create Theme](https://www.typeform.com/developers/create/reference/create-theme)
- [Update Theme](https://www.typeform.com/developers/create/reference/update-theme-partial-update)
- [Delete Theme](https://www.typeform.com/developers/create/reference/delete-theme)
- [Get Image](https://www.typeform.com/developers/create/reference/retrieve-image)
- [Get Image By Size](https://www.typeform.com/developers/create/reference/retrieve-image-by-size)
- [Create Image](https://www.typeform.com/developers/create/reference/create-image)
- [Delete Image](https://www.typeform.com/developers/create/reference/delete-image)
- [Create Or Update Webhook](https://www.typeform.com/developers/webhooks/reference/create-or-update-webhook)
- [Get Webhook](https://www.typeform.com/developers/webhooks/reference/retrieve-single-webhook)
- [Delete Webhook](https://www.typeform.com/developers/webhooks/reference/delete-webhook)
- [Maton CLI Manual](https://cli.maton.ai/manual)
