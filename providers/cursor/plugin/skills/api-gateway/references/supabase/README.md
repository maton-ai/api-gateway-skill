# Supabase

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

> **⚠ Backend administration, not just data access.** A Supabase connection reaches a live application backend with three distinct blast radiuses, and the admin routes below are far more powerful than ordinary record reads:
>
> - **Auth admin (`auth/v1/admin/*`) manages end-user accounts.** These routes enumerate, create, and delete the *application's users* — not the Maton user. Listing them exports an account roster with email addresses and identity metadata; creating one provisions a real login (and can set a password or mark an email confirmed, which is an authentication bypass if misused); deleting one destroys a person's account and whatever the app keys to it. Never enumerate users to "look around", never create an account the user did not ask for, and confirm deletions per user by email, not by UUID.
> - **Storage admin manages buckets, not just files.** Deleting a bucket removes everything in it. Creating or updating one with `public: true` exposes every object it holds to anyone with the URL — a permanent, silent disclosure. State the intended visibility and get explicit approval before creating or changing a bucket.
> - **Database writes go through PostgREST against production tables.** A `PATCH` or `DELETE` without a filter applies to every matching row. Always include a filter that identifies the intended rows (see [PostgREST Filter Operators](#postgrest-filter-operators)), verify with a `GET` first, and never rely on the API's defaults to bound the change.
>
> Which of these the connection can actually do depends on the key behind it: a service-role key bypasses Row Level Security entirely and can read and write every table regardless of policy. Assume that level of access unless the user says otherwise, and prefer reads until they confirm the specific write.

**App name:** `supabase`
**Upstream base URL:** `{project_ref}.supabase.co`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://{project_ref}.supabase.co/rest/v1/`
- Gateway: `https://api.maton.ai/supabase/rest/v1/`

Services:
- `rest/v1` - PostgREST API (database tables)
- `auth/v1` - GoTrue authentication API
- `storage/v1` - Storage API

### Database API

#### Get OpenAPI Schema

```bash
maton api '/supabase/rest/v1/'
```

Returns the OpenAPI specification describing all available tables and endpoints.

#### List Records

```bash
maton api '/supabase/rest/v1/{table_name}'
```

**Note:** `{table_name}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `select` - Columns to return (e.g., `select=id,name,email`)
- `order` - Sort order (e.g., `order=created_at.desc`)
- `limit` / `offset` - See [Pagination](#pagination)

**Example:**
```bash
maton api '/supabase/rest/v1/users?select=id,email&order=created_at.desc&limit=10'
```

#### Get Single Record

```bash
maton api '/supabase/rest/v1/{table_name}?id=eq.{id}'
```

**Note:** `{table_name}` and `{id}` are placeholders. Replace each of them with real values before sending the request.

**Note:** `id=eq.{id}` is a PostgREST filter. See [PostgREST Filter Operators](#postgrest-filter-operators) for the full list and how to combine filters.

#### Insert Record

```bash
maton api -X POST '/supabase/rest/v1/{table_name}' \
  -H 'Content-Type: application/json' \
  -H 'Prefer: return=representation' \
  --input - <<'EOF'
{"name": "value"}
EOF
```

**Note:** `{table_name}` is a placeholder. Replace it with a real value before sending the request.

#### Update Record

```bash
maton api -X PATCH '/supabase/rest/v1/{table_name}?id=eq.{id}' \
  -H 'Content-Type: application/json' \
  -H 'Prefer: return=representation' \
  --input - <<'EOF'
{"name": "new_value"}
EOF
```

**Note:** `{table_name}` and `{id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Record

```bash
maton api '/supabase/rest/v1/{table_name}?id=eq.{id}' -X DELETE
```

**Note:** `{table_name}` and `{id}` are placeholders. Replace each of them with real values before sending the request.

### Auth API

#### Get Health

```bash
maton api '/supabase/auth/v1/health'
```

**Response:**
```json
{
  "version": "v2.188.1",
  "name": "GoTrue",
  "description": "GoTrue is a user registration and authentication API"
}
```

#### Get Settings

```bash
maton api '/supabase/auth/v1/settings'
```

**Response:**
```json
{
  "external": {
    "email": true,
    "phone": false,
    "google": false,
    "github": false
  },
  "disable_signup": false
}
```

#### List Users (Admin)

```bash
maton api '/supabase/auth/v1/admin/users'
```

**Response:**
```json
{
  "users": [
    {
      "id": "8974a9fa-95c4-4839-8d50-76f4666d2113",
      "email": "user@example.com",
      "email_confirmed_at": "2026-03-29T23:01:46.718322Z",
      "created_at": "2026-03-29T23:01:46.689584Z"
    }
  ],
  "aud": "authenticated"
}
```

#### Get User (Admin)

```bash
maton api '/supabase/auth/v1/admin/users/{user_id}'
```

**Note:** `{user_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create User (Admin)

```bash
maton api -X POST '/supabase/auth/v1/admin/users' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "email": "newuser@example.com",
  "password": "securepassword123",
  "email_confirm": true
}
JSON
```

**Response:**
```json
{
  "id": "8974a9fa-95c4-4839-8d50-76f4666d2113",
  "email": "newuser@example.com",
  "email_confirmed_at": "2026-03-29T23:01:46.718322Z",
  "role": "authenticated",
  "app_metadata": {
    "provider": "email",
    "providers": ["email"]
  }
}
```

#### Update User (Admin)

```bash
maton api -X PUT '/supabase/auth/v1/admin/users/{user_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "email": "updated@example.com",
  "user_metadata": {
    "name": "Updated Name"
  }
}
JSON
```

**Note:** `{user_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete User (Admin)

```bash
maton api '/supabase/auth/v1/admin/users/{user_id}' -X DELETE
```

**Note:** `{user_id}` is a placeholder. Replace it with a real value before sending the request.

### Storage API

#### List Buckets

```bash
maton api '/supabase/storage/v1/bucket'
```

**Response:**
```json
[
  {
    "id": "avatars",
    "name": "avatars",
    "public": true,
    "created_at": "2026-03-29T23:01:06.638Z",
    "updated_at": "2026-03-29T23:01:06.638Z"
  }
]
```

#### Get Bucket

```bash
maton api '/supabase/storage/v1/bucket/{bucket_id}'
```

**Note:** `{bucket_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Bucket

```bash
maton api -X POST '/supabase/storage/v1/bucket' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "id": "documents",
  "name": "documents",
  "public": false,
  "file_size_limit": 10485760,
  "allowed_mime_types": ["application/pdf", "image/png"]
}
JSON
```

#### Update Bucket

```bash
maton api -X PUT '/supabase/storage/v1/bucket/{bucket_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "public": true
}
JSON
```

**Note:** `{bucket_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Bucket

```bash
maton api '/supabase/storage/v1/bucket/{bucket_id}' -X DELETE
```

**Note:** `{bucket_id}` is a placeholder. Replace it with a real value before sending the request.

#### List Objects

```bash
maton api -X POST '/supabase/storage/v1/object/list/{bucket_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"prefix": "", "limit": 100}
EOF
```

**Note:** `{bucket_id}` is a placeholder. Replace it with a real value before sending the request.

#### Upload Object

```bash
maton api -X POST '/supabase/storage/v1/object/{bucket_id}/{path}' \
  -H 'Content-Type: {mime_type}' --input ./{local_file}
```

**Note:** `{bucket_id}`, `{path}`, `{mime_type}` and `{local_file}` are placeholders. Replace each of them with real values before sending the request. Pass the file with `--input`; do not paste its contents into a heredoc, which would add a trailing newline and corrupt binary data.

#### Download Object

```bash
maton api '/supabase/storage/v1/object/{bucket_id}/{path}'
```

**Note:** `{bucket_id}` and `{path}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Object

```bash
maton api '/supabase/storage/v1/object/{bucket_id}/{path}' -X DELETE
```

**Note:** `{bucket_id}` and `{path}` are placeholders. Replace each of them with real values before sending the request.

### PostgREST Filter Operators

| Operator | Meaning | Example |
|----------|---------|---------|
| `eq` | Equals | `?status=eq.active` |
| `neq` | Not equals | `?status=neq.deleted` |
| `gt` | Greater than | `?age=gt.18` |
| `lt` | Less than | `?age=lt.65` |
| `like` | Pattern match | `?name=like.*john*` |
| `in` | In list | `?status=in.(active,pending)` |
| `is` | Is null | `?deleted_at=is.null` |

Combine filters with `&` for AND, or use `or=(...)` for OR:

```bash
maton api '/supabase/rest/v1/{table_name}?status=eq.active&age=gt.18'
maton api '/supabase/rest/v1/{table_name}?or=(status.eq.active,status.eq.pending)'
```

**Note:** `{table_name}` is a placeholder. Replace it with a real value before sending the request.

### Pagination

Paging parameters differ by service — PostgREST and GoTrue do not share a scheme.

#### PostgREST (`rest/v1`) — `limit` / `offset`

```bash
maton api '/supabase/rest/v1/{table_name}?limit=10&offset=20'
```

**Note:** `{table_name}` is a placeholder. Replace it with a real value before sending the request.

Or use the `Range` header:

```bash
maton api '/supabase/rest/v1/{table_name}' -H 'Range: 0-9'
```

#### GoTrue admin (`auth/v1`) — `page` / `per_page`

```bash
maton api '/supabase/auth/v1/admin/users?page=1&per_page=50'
```

### Notes

- Connection routes to a specific Supabase project
- PostgREST endpoints auto-generate from database schema
- Use `Prefer: return=representation` to get created/updated records
- Bucket names must be unique within project

### Resources

- [Supabase REST API Guide](https://supabase.com/docs/guides/api)
- [PostgREST Documentation](https://postgrest.org/en/stable/)
- [Auth API](https://supabase.com/docs/reference/javascript/auth-api)
- [Storage API](https://supabase.com/docs/reference/javascript/storage-api)
- [Maton CLI Manual](https://cli.maton.ai/manual)
