# Coda

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `coda`
**Upstream base URL:** `coda.io/apis/v1`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://coda.io/apis/v1/whoami`
- Gateway: `https://api.maton.ai/coda/apis/v1/whoami`

### Account API

#### Get Current User

```bash
maton api '/coda/apis/v1/whoami'
```

Returns information about the authenticated user.

### Docs API

#### List Docs

Coda uses cursor-based pagination with `pageToken`:

```bash
maton api '/coda/apis/v1/docs?limit=25'
```

Response includes `nextPageToken` when more results exist:

```json
{
  "items": [...],
  "href": "https://coda.io/apis/v1/docs?pageToken=...",
  "nextPageToken": "eyJsaW1..."
}
```

Use the `nextPageToken` value as `pageToken` in subsequent requests.

**Query parameters:**
- `isOwner` - Show only owned docs (true/false)
- `query` - Search query
- `sourceDoc` - Filter by source doc ID
- `isStarred` - Show only starred docs
- `inGallery` - Show only gallery docs
- `workspaceId` - Filter by workspace
- `folderId` - Filter by folder
- `limit` - Page size (default: 25, max: 200)
- `pageToken` - Pagination token

#### Create Doc

```bash
maton api -X POST '/coda/apis/v1/docs' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "My New Doc",
  "sourceDoc": "optional-source-doc-id",
  "timezone": "America/Los_Angeles",
  "folderId": "optional-folder-id"
}
JSON
```

#### Get Doc

```bash
maton api '/coda/apis/v1/docs/{docId}'
```

**Note:** `{docId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Doc

```bash
maton api '/coda/apis/v1/docs/{docId}' -X DELETE
```

**Note:** `{docId}` is a placeholder. Replace it with a real value before sending the request.

### Pages API

#### List Pages

```bash
maton api '/coda/apis/v1/docs/{docId}/pages'
```

**Note:** `{docId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `limit` - Page size
- `pageToken` - Pagination token

#### Create Page

```bash
maton api -X POST '/coda/apis/v1/docs/{docId}/pages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Page",
  "subtitle": "Optional subtitle",
  "parentPageId": "optional-parent-page-id"
}
JSON
```

**Note:** `{docId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Page

```bash
maton api '/coda/apis/v1/docs/{docId}/pages/{pageIdOrName}'
```

**Note:** `{docId}` and `{pageIdOrName}` are placeholders. Replace each of them with real values before sending the request.

#### Update Page

```bash
maton api -X PUT '/coda/apis/v1/docs/{docId}/pages/{pageIdOrName}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Page Name",
  "subtitle": "Updated subtitle"
}
JSON
```

**Note:** `{docId}` and `{pageIdOrName}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Page

```bash
maton api '/coda/apis/v1/docs/{docId}/pages/{pageIdOrName}' -X DELETE
```

**Note:** `{docId}` and `{pageIdOrName}` are placeholders. Replace each of them with real values before sending the request.

### Tables API

#### List Tables

```bash
maton api '/coda/apis/v1/docs/{docId}/tables'
```

**Note:** `{docId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `limit` - Page size
- `pageToken` - Pagination token
- `sortBy` - Sort by field
- `tableTypes` - Filter by table type

#### Get Table

```bash
maton api '/coda/apis/v1/docs/{docId}/tables/{tableIdOrName}'
```

**Note:** `{docId}` and `{tableIdOrName}` are placeholders. Replace each of them with real values before sending the request.

### Columns API

#### List Columns

```bash
maton api '/coda/apis/v1/docs/{docId}/tables/{tableIdOrName}/columns'
```

**Note:** `{docId}` and `{tableIdOrName}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**
- `limit` - Page size
- `pageToken` - Pagination token

#### Get Column

```bash
maton api '/coda/apis/v1/docs/{docId}/tables/{tableIdOrName}/columns/{columnIdOrName}'
```

**Note:** `{docId}`, `{tableIdOrName}` and `{columnIdOrName}` are placeholders. Replace each of them with real values before sending the request.

### Rows API

#### List Rows

```bash
maton api '/coda/apis/v1/docs/{docId}/tables/{tableIdOrName}/rows'
```

**Note:** `{docId}` and `{tableIdOrName}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**
- `query` - Filter rows by search query
- `useColumnNames` - Use column names instead of IDs in response (true/false)
- `valueFormat` - Value format (simple, simpleWithArrays, rich)
- `sortBy` - Sort by column
- `limit` - Page size
- `pageToken` - Pagination token

#### Get Row

```bash
maton api '/coda/apis/v1/docs/{docId}/tables/{tableIdOrName}/rows/{rowIdOrName}'
```

**Note:** `{docId}`, `{tableIdOrName}` and `{rowIdOrName}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**
- `useColumnNames` - Use column names instead of IDs
- `valueFormat` - Value format

#### Insert/Upsert Rows

```bash
maton api -X POST '/coda/apis/v1/docs/{docId}/tables/{tableIdOrName}/rows' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "rows": [
    {
      "cells": [
        {"column": "Column Name", "value": "Cell Value"},
        {"column": "Another Column", "value": 123}
      ]
    }
  ],
  "keyColumns": ["Column Name"]
}
JSON
```

**Note:** `{docId}` and `{tableIdOrName}` are placeholders. Replace each of them with real values before sending the request.

- Use `keyColumns` for upsert behavior (update if exists, insert if not)
- Row inserts/upserts are processed asynchronously (returns requestId)

#### Update Row

```bash
maton api -X PUT '/coda/apis/v1/docs/{docId}/tables/{tableIdOrName}/rows/{rowIdOrName}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "row": {
    "cells": [
      {"column": "Column Name", "value": "Updated Value"}
    ]
  }
}
JSON
```

**Note:** `{docId}`, `{tableIdOrName}` and `{rowIdOrName}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Row

```bash
maton api '/coda/apis/v1/docs/{docId}/tables/{tableIdOrName}/rows/{rowIdOrName}' -X DELETE
```

**Note:** `{docId}`, `{tableIdOrName}` and `{rowIdOrName}` are placeholders. Replace each of them with real values before sending the request.

### Formulas API

#### List Formulas

```bash
maton api '/coda/apis/v1/docs/{docId}/formulas'
```

**Note:** `{docId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Formula

```bash
maton api '/coda/apis/v1/docs/{docId}/formulas/{formulaIdOrName}'
```

**Note:** `{docId}` and `{formulaIdOrName}` are placeholders. Replace each of them with real values before sending the request.

### Controls API

#### List Controls

```bash
maton api '/coda/apis/v1/docs/{docId}/controls'
```

**Note:** `{docId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Control

```bash
maton api '/coda/apis/v1/docs/{docId}/controls/{controlIdOrName}'
```

**Note:** `{docId}` and `{controlIdOrName}` are placeholders. Replace each of them with real values before sending the request.

### Permissions API

#### Get Sharing Metadata

```bash
maton api '/coda/apis/v1/docs/{docId}/acl/metadata'
```

**Note:** `{docId}` is a placeholder. Replace it with a real value before sending the request.

#### List Permissions

```bash
maton api '/coda/apis/v1/docs/{docId}/acl/permissions'
```

**Note:** `{docId}` is a placeholder. Replace it with a real value before sending the request.

#### Add Permission

```bash
maton api -X POST '/coda/apis/v1/docs/{docId}/acl/permissions' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "access": "readonly",
  "principal": {
    "type": "email",
    "email": "user@example.com"
  }
}
JSON
```

**Note:** `{docId}` is a placeholder. Replace it with a real value before sending the request.

Access values: `readonly`, `write`, `comment`

#### Delete Permission

```bash
maton api '/coda/apis/v1/docs/{docId}/acl/permissions/{permissionId}' -X DELETE
```

**Note:** `{docId}` and `{permissionId}` are placeholders. Replace each of them with real values before sending the request.

### Categories API

#### List Categories

```bash
maton api '/coda/apis/v1/categories'
```

### Utilities API

#### Resolve Browser Link

```bash
maton api '/coda/apis/v1/resolveBrowserLink?url={encodedUrl}'
```

**Note:** `{encodedUrl}` is a placeholder. Replace it with a real value before sending the request.

Converts a Coda browser URL to API resource information.

#### Get Mutation Status

Create, update, and delete operations return HTTP 202 with a `requestId`:

```json
{
  "id": "canvas-abc123",
  "requestId": "mutate:9f038510-be42-4d16-bccf-3468d38efd57"
}
```

Check mutation status:

```bash
maton api '/coda/apis/v1/mutationStatus/{requestId}'
```

**Note:** `{requestId}` is a placeholder. Replace it with a real value before sending the request.

Response:
```json
{
  "completed": true
}
```

Mutations are generally processed within a few seconds.

### Analytics API

#### List Doc Analytics

```bash
maton api '/coda/apis/v1/analytics/docs'
```

**Query parameters:**
- `isPublished` - Filter by published status
- `sinceDate` - Start date (YYYY-MM-DD)
- `untilDate` - End date (YYYY-MM-DD)
- `limit` - Page size
- `pageToken` - Pagination token

#### List Pack Analytics

```bash
maton api '/coda/apis/v1/analytics/packs'
```

#### Get Analytics Update Time

```bash
maton api '/coda/apis/v1/analytics/updated'
```

### Query parameters

Common parameters across endpoints:
- `limit` - Page size (max: 200)
- `pageToken` - Cursor for pagination
- `query` - Search filter
- `useColumnNames` - Use column names vs IDs (rows)
- `valueFormat` - simple, simpleWithArrays, rich (rows)

### Notes

- Mutations (create/update/delete) return HTTP 202 with requestId
- Use `/mutationStatus/{requestId}` to check completion
- Newly created docs need a moment before child resources are accessible
- Table/column names can be used instead of IDs
- Row operations require base tables, not views
- Page-level analytics require Enterprise plan

### Resources

- [Coda API Documentation](https://coda.io/developers/apis/v1)
- [Maton CLI Manual](https://cli.maton.ai/manual)
