# Baserow Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `baserow`
**Base URL proxied:** `api.baserow.io`

## API Path Pattern

```
/baserow/api/database/rows/table/{table_id}/
/baserow/api/database/fields/table/{table_id}/
/baserow/api/database/tables/all-tables/
/baserow/api/user-files/upload-file/
/baserow/api/user-files/upload-via-url/
```

## Important Notes

- Connection uses API_KEY authentication (database token), not OAuth
- By default, fields return as `field_{id}`; use `user_field_names=true` for readable names
- Database tokens grant access only to database row endpoints
- Cloud has a limit of 10 concurrent API requests

## Common Endpoints

### List Rows
```bash
maton api '/baserow/api/database/rows/table/{table_id}/?user_field_names=true'
```

### Get Row
```bash
maton api '/baserow/api/database/rows/table/{table_id}/{row_id}/'
```

### Create Row
```bash
maton api -X POST '/baserow/api/database/rows/table/{table_id}/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "field_123": "value"
}
EOF
```

### Update Row
```bash
maton api -X PATCH '/baserow/api/database/rows/table/{table_id}/{row_id}/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "field_123": "updated value"
}
EOF
```

### Delete Row
```bash
maton api -X DELETE '/baserow/api/database/rows/table/{table_id}/{row_id}/'
```

### Batch Create Rows
```bash
maton api -X POST '/baserow/api/database/rows/table/{table_id}/batch/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "items": [
    {"field_123": "value1"},
    {"field_123": "value2"}
  ]
}
EOF
```

### Batch Update Rows
```bash
maton api -X PATCH '/baserow/api/database/rows/table/{table_id}/batch/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "items": [
    {"id": 1, "field_123": "updated1"},
    {"id": 2, "field_123": "updated2"}
  ]
}
EOF
```

### Batch Delete Rows
```bash
maton api -X POST '/baserow/api/database/rows/table/{table_id}/batch-delete/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "items": [1, 2, 3]
}
EOF
```

### List Fields
```bash
maton api '/baserow/api/database/fields/table/{table_id}/'
```

### List All Tables
```bash
maton api '/baserow/api/database/tables/all-tables/'
```

### Move Row
```bash
maton api -X PATCH '/baserow/api/database/rows/table/{table_id}/{row_id}/move/?before_id={row_id}'
```

### Upload File via URL
```bash
maton api -X POST '/baserow/api/user-files/upload-via-url/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/image.png"
}
EOF
```

### Upload File (Multipart)
```bash
# multipart/form-data is not expressible with `maton api`; call the gateway directly with `MATON_API_KEY` (see SKILL.md appendix).
python <<'EOF'
import json, mimetypes, os, urllib.request, uuid

# Maton API key from the environment; never print, log, or persist it.
TOKEN = os.environ["MATON_API_KEY"]

# Exactly the path the user gave — never a discovered or inferred one.
file_path = '/path/to/file.png'

boundary = uuid.uuid4().hex
mime = mimetypes.guess_type(file_path)[0] or 'application/octet-stream'
with open(file_path, 'rb') as f:
    body = (f'--{boundary}\r\nContent-Disposition: form-data; name="file"; filename="{os.path.basename(file_path)}"\r\n'
            f'Content-Type: {mime}\r\n\r\n').encode() + f.read() + f'\r\n--{boundary}--\r\n'.encode()

req = urllib.request.Request('https://api.maton.ai/baserow/api/user-files/upload-file/', data=body, method='POST')
req.add_header('Authorization', f'Bearer {TOKEN}')
req.add_header('User-Agent', 'maton-gateway-skill/1.2')
req.add_header('Content-Type', f'multipart/form-data; boundary={boundary}')
print(json.dumps(json.load(urllib.request.urlopen(req)), indent=2))
EOF
```

## Query Parameters

- `user_field_names=true` - Use human-readable field names
- `size` - Rows per page (default: 100)
- `page` - Page number (1-indexed)
- `order_by` - Field to sort by (prefix `-` for descending)
- `filter__{field}__{operator}` - Filter rows
- `search` - Search across all fields
- `include` - Fields to include
- `exclude` - Fields to exclude

## Filter Operators

**Text:** `equal`, `not_equal`, `contains`, `contains_not`, `contains_word`, `doesnt_contain_word`, `length_is_lower_than`

**Numeric:** `higher_than`, `higher_than_or_equal`, `lower_than`, `lower_than_or_equal`, `is_even_and_whole`

**Date:** `date_is`, `date_is_not`, `date_is_before`, `date_is_on_or_before`, `date_is_after`, `date_is_on_or_after`, `date_is_within`, `date_equals_today`, `date_within_days`, `date_within_weeks`, `date_within_months`

**Boolean:** `boolean`

**Link Row:** `link_row_has`, `link_row_has_not`, `link_row_contains`, `link_row_not_contains`

**Select:** `single_select_equal`, `single_select_not_equal`, `single_select_is_any_of`, `single_select_is_none_of`, `multiple_select_has`, `multiple_select_has_not`

**File:** `filename_contains`, `has_file_type`, `files_lower_than`

**General:** `empty`, `not_empty`

## Resources

- [Baserow API Documentation](https://baserow.io/api-docs)
- [Baserow API Spec](https://api.baserow.io/api/redoc/)
- [Database Tokens](https://baserow.io/user-docs/personal-api-tokens)
