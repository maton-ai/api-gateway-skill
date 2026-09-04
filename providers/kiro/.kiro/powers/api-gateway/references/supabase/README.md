# Supabase Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `supabase`
**Base URL proxied:** `{project_ref}.supabase.co`

## API Path Pattern

```
/supabase/{service}/{native-api-path}
```

Services:
- `rest/v1` - PostgREST API (database tables)
- `auth/v1` - GoTrue authentication API
- `storage/v1` - Storage API

## Common Endpoints

### Database (PostgREST)

#### Get OpenAPI Schema
```bash
maton api '/supabase/rest/v1/'
```

#### List Records
```bash
maton api '/supabase/rest/v1/{table_name}?select=*&limit=10'
```

#### Get Single Record
```bash
maton api '/supabase/rest/v1/{table_name}?id=eq.{id}'
```

#### Insert Record
```bash
maton api -X POST '/supabase/rest/v1/{table_name}' \
  -H 'Content-Type: application/json' \
  -H 'Prefer: return=representation' \
  --input - <<'EOF'
{"name": "value"}
EOF
```

#### Update Record
```bash
maton api -X PATCH '/supabase/rest/v1/{table_name}?id=eq.{id}' \
  -H 'Content-Type: application/json' \
  -H 'Prefer: return=representation' \
  --input - <<'EOF'
{"name": "new_value"}
EOF
```

#### Delete Record
```bash
maton api -X DELETE '/supabase/rest/v1/{table_name}?id=eq.{id}'
```

### Auth (GoTrue)

#### Get Health
```bash
maton api '/supabase/auth/v1/health'
```

#### Get Settings
```bash
maton api '/supabase/auth/v1/settings'
```

#### List Users (Admin)
```bash
maton api '/supabase/auth/v1/admin/users'
```

#### Get User (Admin)
```bash
maton api '/supabase/auth/v1/admin/users/{user_id}'
```

#### Create User (Admin)
```bash
maton api -X POST '/supabase/auth/v1/admin/users' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "email": "user@example.com",
  "password": "password123",
  "email_confirm": true
}
EOF
```

#### Delete User (Admin)
```bash
maton api -X DELETE '/supabase/auth/v1/admin/users/{user_id}'
```

### Storage

#### List Buckets
```bash
maton api '/supabase/storage/v1/bucket'
```

#### Get Bucket
```bash
maton api '/supabase/storage/v1/bucket/{bucket_id}'
```

#### Create Bucket
```bash
maton api -X POST '/supabase/storage/v1/bucket' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "id": "my-bucket",
  "name": "my-bucket",
  "public": false
}
EOF
```

#### Delete Bucket
```bash
maton api -X DELETE '/supabase/storage/v1/bucket/{bucket_id}'
```

#### List Objects
```bash
maton api -X POST '/supabase/storage/v1/object/list/{bucket_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"prefix": "", "limit": 100}
EOF
```

#### Upload Object
```bash
maton api -X POST '/supabase/storage/v1/object/{bucket_id}/{path}' \
  -H 'Content-Type: {mime_type}' \
  --input - <<'EOF'
{binary_data}
EOF
```

#### Download Object
```bash
maton api '/supabase/storage/v1/object/{bucket_id}/{path}'
```

#### Delete Object
```bash
maton api -X DELETE '/supabase/storage/v1/object/{bucket_id}/{path}'
```

## Pagination

### PostgREST
```bash
maton api '/supabase/rest/v1/{table}?limit=10&offset=20'
```

Or use Range header:
```
Range: 0-9
```

### Auth Users
```bash
maton api '/supabase/auth/v1/admin/users?page=1&per_page=50'
```

## PostgREST Filter Operators

| Operator | Meaning | Example |
|----------|---------|---------|
| `eq` | Equals | `?status=eq.active` |
| `neq` | Not equals | `?status=neq.deleted` |
| `gt` | Greater than | `?age=gt.18` |
| `lt` | Less than | `?age=lt.65` |
| `like` | Pattern match | `?name=like.*john*` |
| `in` | In list | `?status=in.(active,pending)` |
| `is` | Is null | `?deleted_at=is.null` |

## Notes

- Connection routes to a specific Supabase project
- PostgREST endpoints auto-generate from database schema
- Use `Prefer: return=representation` to get created/updated records
- Auth admin endpoints require service role permissions
- Bucket names must be unique within project

## Resources

- [Supabase REST API Guide](https://supabase.com/docs/guides/api)
- [PostgREST Documentation](https://postgrest.org/en/stable/)
- [Supabase Auth API](https://supabase.com/docs/reference/javascript/auth-api)
- [Supabase Storage API](https://supabase.com/docs/reference/javascript/storage-api)
