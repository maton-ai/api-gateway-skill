# Cognito Forms

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `cognito-forms`
**Upstream base URL:** `www.cognitoforms.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://www.cognitoforms.com/api/forms`
- Gateway: `https://api.maton.ai/cognito-forms/api/forms`

### Forms API

#### List Forms

```bash
maton api '/cognito-forms/api/forms'
```

Returns all forms in the organization.

#### Get Form

```bash
maton api '/cognito-forms/api/forms/{formId}'
```

**Note:** `{formId}` is a placeholder. Replace it with a real value before sending the request.

### Entries API

#### Get Entry

```bash
maton api '/cognito-forms/api/forms/{formId}/entries/{entryId}'
```

**Note:** `{formId}` and `{entryId}` are placeholders. Replace each of them with real values before sending the request.

Returns a specific entry by ID or entry number.

#### List Entries

```bash
maton api '/cognito-forms/api/forms/{formId}/entries'
```

**Note:** `{formId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Entry

```bash
maton api -X POST '/cognito-forms/api/forms/{formId}/entries' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "Name": {
    "First": "John",
    "Last": "Doe"
  },
  "Email": "john.doe@example.com",
  "Phone": "555-1234"
}
JSON
```

**Note:** `{formId}` is a placeholder. Replace it with a real value before sending the request.

Field names match your form's field names. Complex fields like Name and Address use nested objects.

#### Update Entry

```bash
maton api -X PUT '/cognito-forms/api/forms/{formId}/entries/{entryId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "Name": {
    "First": "Jane",
    "Last": "Doe"
  },
  "Email": "jane.doe@example.com"
}
EOF
```

**Note:** `{formId}` and `{entryId}` are placeholders. Replace each of them with real values before sending the request.

#### Update Entry (Partial)

```bash
maton api -X PATCH '/cognito-forms/api/forms/{formId}/entries/{entryId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "Name": {
    "First": "Jane",
    "Last": "Doe"
  },
  "Email": "jane.doe@example.com"
}
JSON
```

**Note:** `{formId}` and `{entryId}` are placeholders. Replace each of them with real values before sending the request.

Updates an existing entry. Uses PATCH method (not PUT). Fails if the entry includes a paid order.

#### Delete Entry

```bash
maton api '/cognito-forms/api/forms/{formId}/entries/{entryId}' -X DELETE
```

**Note:** `{formId}` and `{entryId}` are placeholders. Replace each of them with real values before sending the request.

Deletes an entry. Requires Read/Write/Delete API scope.

### Form Availability API

#### Set Form Availability

```bash
maton api -X PUT '/cognito-forms/api/forms/{formId}/availability' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "start": "2026-03-01T00:00:00Z",
  "end": "2026-03-31T23:59:59Z",
  "message": "This form is currently unavailable."
}
EOF
```

**Note:** `{formId}` is a placeholder. Replace it with a real value before sending the request.

### Documents API

#### Get Document

```bash
maton api '/cognito-forms/api/forms/{formId}/entries/{entryId}/documents/{templateNumber}'
```

**Note:** `{formId}`, `{entryId}` and `{templateNumber}` are placeholders. Replace each of them with real values before sending the request.

Generates and returns a document from an entry using the specified template number.

**Response:**
```json
{
  "Id": "abc123",
  "Name": "Entry-Document.pdf",
  "ContentType": "application/pdf",
  "Size": 12345,
  "File": "https://temporary-download-url..."
}
```

### Files API

#### Get File

```bash
maton api '/cognito-forms/api/files/{fileId}'
```

**Note:** `{fileId}` is a placeholder. Replace it with a real value before sending the request.

Retrieves a file uploaded to a form entry.

**Response:**
```json
{
  "Id": "file-id",
  "Name": "upload.pdf",
  "ContentType": "application/pdf",
  "Size": 54321,
  "File": "https://temporary-download-url..."
}
```

### Field Types

Complex fields use nested JSON objects:

- **Name**: `{"First": "...", "Last": "..."}`
- **Address**: `{"Line1": "...", "Line2": "...", "City": "...", "State": "...", "PostalCode": "..."}`
- **Choice (single)**: `"OptionValue"`
- **Choice (multiple)**: `["Option1", "Option2"]`

### Notes

- Form IDs can be internal form name (string) or numeric ID
- Entry IDs can be entry number (integer) or entry ID (GUID)
- Rate limit: 100 requests per 60 seconds
- File and document endpoints return temporary download URLs
- API scopes: Read, Read/Write, or Read/Write/Delete

### Resources

- [Cognito Forms API Overview](https://www.cognitoforms.com/support/475/data-integration/cognito-forms-api)
- [Cognito Forms REST API Reference](https://www.cognitoforms.com/support/476/data-integration/cognito-forms-api/rest-api-reference)
- [Cognito Forms API Reference](https://www.cognitoforms.com/support/476/data-integration/cognito-forms-api/api-reference)
- [Maton CLI Manual](https://cli.maton.ai/manual)
