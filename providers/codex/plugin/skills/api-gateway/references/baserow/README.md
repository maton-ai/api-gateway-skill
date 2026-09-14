# Baserow

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `baserow`
**Upstream base URL:** `api.baserow.io`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.baserow.io/api/database/tables/all-tables/`
- Gateway: `https://api.maton.ai/baserow/api/database/tables/all-tables/`

### Rows API

#### List Rows

```bash
maton api '/baserow/api/database/rows/table/{table_id}/'
```

**Note:** `{table_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `user_field_names=true` - Use human-readable field names instead of `field_123` IDs
- `size` - Number of rows per page (default: 100)
- `page` - Page number (1-indexed)
- `order_by` - Field name to sort by (prefix with `-` for descending)
- `filter__{field}__{operator}` - Filter rows (see Filtering section)
- `search` - Search query across all fields
- `include` - Comma-separated field names to include
- `exclude` - Comma-separated field names to exclude

**Response:**
```json
{
  "count": 5,
  "next": ".../api/database/rows/table/123/?page=2&size=2",
  "previous": null,
  "results": [
    {
      "id": 1,
      "order": "1.00000000000000000000",
      "Assignee Name": "Alice Johnson",
      "Email": "alice.johnson@example.com",
      "Tasks": []
    }
  ]
}
```

#### Get Row

```bash
maton api '/baserow/api/database/rows/table/{table_id}/{row_id}/'
```

**Note:** `{table_id}` and `{row_id}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "id": 1,
  "order": "1.00000000000000000000",
  "field_7456198": "Alice Johnson",
  "field_7456201": "alice.johnson@example.com",
  "field_7456215": []
}
```

#### Create Row

```bash
maton api -X POST '/baserow/api/database/rows/table/{table_id}/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "field_7456198": "New User",
  "field_7456201": "newuser@example.com"
}
JSON
```

Or with user field names:

```bash
maton api -X POST '/baserow/api/database/rows/table/{table_id}/?user_field_names=true' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "Assignee Name": "New User",
  "Email": "newuser@example.com"
}
JSON
```

**Note:** `{table_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": 6,
  "order": "6.00000000000000000000",
  "field_7456198": "New User",
  "field_7456201": "newuser@example.com",
  "field_7456215": []
}
```

#### Update Row

```bash
maton api -X PATCH '/baserow/api/database/rows/table/{table_id}/{row_id}/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "field_7456198": "Updated Name"
}
JSON
```

**Note:** `{table_id}` and `{row_id}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "id": 1,
  "order": "1.00000000000000000000",
  "field_7456198": "Updated Name",
  "field_7456201": "alice.johnson@example.com",
  "field_7456215": []
}
```

#### Delete Row

```bash
maton api '/baserow/api/database/rows/table/{table_id}/{row_id}/' -X DELETE
```

**Note:** `{table_id}` and `{row_id}` are placeholders. Replace each of them with real values before sending the request.

Returns HTTP 204 No Content on success.

#### Move Row

Reposition a row within a table.

```bash
maton api -X PATCH '/baserow/api/database/rows/table/{table_id}/{row_id}/move/'
```

**Note:** `{table_id}` and `{row_id}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**
- `before_id` - Row ID to move before (if omitted, moves to end)

**Response:**
```json
{
  "id": 5,
  "order": "2.50000000000000000000",
  "field_7456198": "Moved User",
  "field_7456201": "moved@example.com"
}
```

**Example - Move row to before row 3:**
```bash
maton api -X PATCH '/baserow/api/database/rows/table/{table_id}/{row_id}/move/?before_id={row_id}'
```

### Batch API

#### Batch Create Rows

```bash
maton api -X POST '/baserow/api/database/rows/table/{table_id}/batch/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "items": [
    {"field_7456198": "User 1", "field_7456201": "user1@example.com"},
    {"field_7456198": "User 2", "field_7456201": "user2@example.com"}
  ]
}
JSON
```

**Note:** `{table_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "items": [
    {"id": 7, "order": "7.00000000000000000000", "field_7456198": "User 1", ...},
    {"id": 8, "order": "8.00000000000000000000", "field_7456198": "User 2", ...}
  ]
}
```

#### Batch Update Rows

```bash
maton api -X PATCH '/baserow/api/database/rows/table/{table_id}/batch/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "items": [
    {"id": 7, "field_7456198": "Updated User 1"},
    {"id": 8, "field_7456198": "Updated User 2"}
  ]
}
JSON
```

**Note:** `{table_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "items": [
    {"id": 7, "order": "7.00000000000000000000", "field_7456198": "Updated User 1", ...},
    {"id": 8, "order": "8.00000000000000000000", "field_7456198": "Updated User 2", ...}
  ]
}
```

#### Batch Delete Rows

```bash
maton api -X POST '/baserow/api/database/rows/table/{table_id}/batch-delete/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "items": [7, 8]
}
JSON
```

**Note:** `{table_id}` is a placeholder. Replace it with a real value before sending the request.

Returns HTTP 204 No Content on success.

### Fields API

#### List Fields

```bash
maton api '/baserow/api/database/fields/table/{table_id}/'
```

**Note:** `{table_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
[
  {
    "id": 7456198,
    "table_id": 863922,
    "name": "Assignee Name",
    "order": 0,
    "type": "text",
    "primary": true,
    "read_only": false,
    "description": null
  },
  {
    "id": 7456201,
    "table_id": 863922,
    "name": "Email",
    "order": 1,
    "type": "text",
    "primary": false
  }
]
```

### Tables API

#### List All Tables

Get all tables across all databases accessible by your token.

```bash
maton api '/baserow/api/database/tables/all-tables/'
```

**Response:**
```json
[
  {
    "id": 863922,
    "name": "Assignees",
    "order": 0,
    "database_id": 419329
  },
  {
    "id": 863923,
    "name": "Tasks",
    "order": 1,
    "database_id": 419329
  }
]
```

### File Uploads API

#### Upload File via URL

Upload a file from a publicly accessible URL.

```bash
maton api -X POST '/baserow/api/user-files/upload-via-url/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "url": "https://example.com/image.png"
}
JSON
```

**Response:**
```json
{
  "url": "https://files.baserow.io/user_files/...",
  "thumbnails": {
    "tiny": {"url": "...", "width": 21, "height": 21},
    "small": {"url": "...", "width": 48, "height": 48},
    "card_cover": {"url": "...", "width": 300, "height": 160}
  },
  "visible_name": "image.png",
  "name": "abc123_image.png",
  "size": 8090,
  "mime_type": "image/png",
  "is_image": true,
  "image_width": 100,
  "image_height": 100,
  "uploaded_at": "2026-03-02T12:00:00Z"
}
```

#### Upload File (Multipart)

Upload a file directly using multipart form data.

```bash
maton api -X POST '/baserow/api/user-files/upload-file/' -H 'Content-Type: multipart/form-data'
```

**Response:** Same format as upload-via-url.

**Example:**
`maton api` sends a body verbatim but does not build a multipart envelope, so assemble the body first and hand it to `--input`. Nothing here handles a credential — the CLI still injects it.

```bash
BOUNDARY="maton-$$"
{
  printf -- '--%s\r\nContent-Disposition: form-data; name="file"; filename="file.pdf"\r\nContent-Type: application/pdf\r\n\r\n' "$BOUNDARY"
  cat /path/to/file.pdf
  printf -- '\r\n--%s--\r\n' "$BOUNDARY"
} > /tmp/baserow-upload.body

maton api -X POST '/baserow/api/user-files/upload-file/' \
  -H "Content-Type: multipart/form-data; boundary=$BOUNDARY" \
  --input /tmp/baserow-upload.body
```

#### Using Uploaded Files in Rows

After uploading, use the file object in a file field:

```bash
maton api -X POST '/baserow/api/database/rows/table/{table_id}/?user_field_names=true' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "Attachment": [{"name": "abc123_image.png"}]
}
JSON
```

**Note:** `{table_id}` is a placeholder. Replace it with a real value before sending the request.

### Filtering

Use filter parameters to query rows:

```
filter__{field}__{operator}={value}
```

With `user_field_names=true`:
```bash
maton api '/baserow/api/database/rows/table/{table_id}/?user_field_names=true&filter__Assignee+Name__contains=Alice'
```

**Note:** `{table_id}` is a placeholder. Replace it with a real value before sending the request.

Multiple filters use AND logic by default. Use `filter_type=OR` to change to OR logic.

#### Text Filters
| Operator | Description |
|----------|-------------|
| `equal` | Exact match |
| `not_equal` | Not equal |
| `contains` | Contains substring |
| `contains_not` | Does not contain substring |
| `contains_word` | Contains whole word |
| `doesnt_contain_word` | Does not contain whole word |
| `length_is_lower_than` | Text length is less than value |

#### Numeric Filters
| Operator | Description |
|----------|-------------|
| `higher_than` | Greater than |
| `higher_than_or_equal` | Greater than or equal |
| `lower_than` | Less than |
| `lower_than_or_equal` | Less than or equal |
| `is_even_and_whole` | Value is even and whole number |

#### Date Filters
| Operator | Description |
|----------|-------------|
| `date_is` | Date equals (use with timezone) |
| `date_is_not` | Date does not equal |
| `date_is_before` | Date is before |
| `date_is_on_or_before` | Date is on or before |
| `date_is_after` | Date is after |
| `date_is_on_or_after` | Date is on or after |
| `date_is_within` | Date is within period |
| `date_equal` | Date equals (legacy) |
| `date_not_equal` | Date does not equal (legacy) |
| `date_equals_today` | Date is today |
| `date_before_today` | Date is before today |
| `date_after_today` | Date is after today |
| `date_within_days` | Date within X days |
| `date_within_weeks` | Date within X weeks |
| `date_within_months` | Date within X months |
| `date_equals_days_ago` | Date equals X days ago |
| `date_equals_weeks_ago` | Date equals X weeks ago |
| `date_equals_months_ago` | Date equals X months ago |
| `date_equals_years_ago` | Date equals X years ago |
| `date_equals_day_of_month` | Date equals specific day of month |
| `date_before_or_equal` | Date is before or equal (legacy) |
| `date_after_or_equal` | Date is after or equal (legacy) |

#### Boolean Filters
| Operator | Description |
|----------|-------------|
| `boolean` | Boolean equals (true/false) |

#### Link Row Filters
| Operator | Description |
|----------|-------------|
| `link_row_has` | Has linked row with ID |
| `link_row_has_not` | Does not have linked row with ID |
| `link_row_contains` | Linked row contains text |
| `link_row_not_contains` | Linked row does not contain text |

#### Single Select Filters
| Operator | Description |
|----------|-------------|
| `single_select_equal` | Single select equals option ID |
| `single_select_not_equal` | Single select does not equal option ID |
| `single_select_is_any_of` | Single select is any of option IDs |
| `single_select_is_none_of` | Single select is none of option IDs |

#### Multiple Select Filters
| Operator | Description |
|----------|-------------|
| `multiple_select_has` | Has option selected |
| `multiple_select_has_not` | Does not have option selected |
| `multiple_select_is_exactly` | Exactly these options selected |

#### Collaborator Filters
| Operator | Description |
|----------|-------------|
| `multiple_collaborators_has` | Has collaborator |
| `multiple_collaborators_has_not` | Does not have collaborator |

#### File Filters
| Operator | Description |
|----------|-------------|
| `filename_contains` | File name contains |
| `has_file_type` | Has file of type (image, document) |
| `files_lower_than` | Number of files less than |

#### Empty/Not Empty Filters
| Operator | Description |
|----------|-------------|
| `empty` | Field is empty (value: `true`) |
| `not_empty` | Field is not empty (value: `true`) |

#### User Filters
| Operator | Description |
|----------|-------------|
| `user_is` | User field equals user ID |
| `user_is_not` | User field does not equal user ID |

#### Filter Examples

**Text contains:**
```bash
maton api '/baserow/api/database/rows/table/{table_id}/?user_field_names=true&filter__Name__contains=John'
```

**Note:** `{table_id}` is a placeholder. Replace it with a real value before sending the request.

**Date within last 7 days:**
```bash
maton api '/baserow/api/database/rows/table/{table_id}/?user_field_names=true&filter__Created__date_within_days=7'
```

**Note:** `{table_id}` is a placeholder. Replace it with a real value before sending the request.

**Multiple filters (AND):**
```bash
maton api '/baserow/api/database/rows/table/{table_id}/?user_field_names=true&filter__Status__single_select_equal=1&filter__Priority__higher_than=3'
```

**Note:** `{table_id}` is a placeholder. Replace it with a real value before sending the request.

**Multiple filters (OR):**
```bash
maton api '/baserow/api/database/rows/table/{table_id}/?user_field_names=true&filter_type=OR&filter__Status__equal=Active&filter__Status__equal=Pending'
```

**Note:** `{table_id}` is a placeholder. Replace it with a real value before sending the request.

### Sorting

Use `order_by` parameter:

```bash
# Sort ascending by field name
maton api '/baserow/api/database/rows/table/{table_id}/?user_field_names=true&order_by=Assignee+Name'

# Sort descending (prefix with -)
maton api '/baserow/api/database/rows/table/{table_id}/?user_field_names=true&order_by=-Assignee+Name'
```

**Note:** `{table_id}` is a placeholder. Replace it with a real value before sending the request.

### Pagination

Use `size` and `page` parameters:

```bash
maton api '/baserow/api/database/rows/table/{table_id}/?size=25&page=2'
```

**Note:** `{table_id}` is a placeholder. Replace it with a real value before sending the request.

Response includes `next` and `previous` URLs:

```json
{
  "count": 100,
  "next": ".../api/database/rows/table/123/?page=3&size=25",
  "previous": ".../api/database/rows/table/123/?page=1&size=25",
  "results": [...]
}
```

### Notes

- The Baserow connection authenticates with a Baserow database token (`--method API_KEY`), not Baserow OAuth. That is a separate layer from signing in to Maton: `maton login --oauth` authenticates the CLI to your Maton account either way, and the gateway holds the database token for you.
- By default, fields are returned as `field_{id}` format; use `user_field_names=true` for human-readable names
- Database tokens grant access only to database row endpoints
- Row IDs are integers (not strings like Airtable's `recXXX` format)
- Table IDs can be found in the Baserow UI URL or API documentation
- A database token covers the row endpoints plus the schema and user-file endpoints this skill documents (listing fields and tables, uploading files); it does not reach instance-admin endpoints. A JWT from a user login is required for anything beyond that.
- Cloud version has a limit of 10 concurrent API requests

### Resources

- [Baserow API Documentation](https://baserow.io/api-docs)
- [Baserow API Spec](https://api.baserow.io/api/redoc/)
- [Database Tokens](https://baserow.io/user-docs/personal-api-tokens)
- [Maton CLI Manual](https://cli.maton.ai/manual)
