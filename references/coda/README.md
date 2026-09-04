# Coda Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `coda`
**Base URL proxied:** `coda.io/apis/v1`

## API Path Pattern

```
/coda/apis/v1/{resource}
```

## Common Endpoints

### Account

#### Get Current User
```bash
maton api '/coda/apis/v1/whoami'
```

### Docs

#### List Docs
```bash
maton api '/coda/apis/v1/docs'
```

#### Create Doc
```bash
maton api -X POST '/coda/apis/v1/docs'
```

#### Get Doc
```bash
maton api '/coda/apis/v1/docs/{docId}'
```

#### Delete Doc
```bash
maton api -X DELETE '/coda/apis/v1/docs/{docId}'
```

### Pages

#### List Pages
```bash
maton api '/coda/apis/v1/docs/{docId}/pages'
```

#### Create Page
```bash
maton api -X POST '/coda/apis/v1/docs/{docId}/pages'
```

#### Get Page
```bash
maton api '/coda/apis/v1/docs/{docId}/pages/{pageIdOrName}'
```

#### Update Page
```bash
maton api -X PUT '/coda/apis/v1/docs/{docId}/pages/{pageIdOrName}'
```

#### Delete Page
```bash
maton api -X DELETE '/coda/apis/v1/docs/{docId}/pages/{pageIdOrName}'
```

### Tables

#### List Tables
```bash
maton api '/coda/apis/v1/docs/{docId}/tables'
```

#### Get Table
```bash
maton api '/coda/apis/v1/docs/{docId}/tables/{tableIdOrName}'
```

### Columns

#### List Columns
```bash
maton api '/coda/apis/v1/docs/{docId}/tables/{tableIdOrName}/columns'
```

#### Get Column
```bash
maton api '/coda/apis/v1/docs/{docId}/tables/{tableIdOrName}/columns/{columnIdOrName}'
```

### Rows

#### List Rows
```bash
maton api '/coda/apis/v1/docs/{docId}/tables/{tableIdOrName}/rows'
```

#### Get Row
```bash
maton api '/coda/apis/v1/docs/{docId}/tables/{tableIdOrName}/rows/{rowIdOrName}'
```

#### Insert/Upsert Rows
```bash
maton api -X POST '/coda/apis/v1/docs/{docId}/tables/{tableIdOrName}/rows'
```

#### Update Row
```bash
maton api -X PUT '/coda/apis/v1/docs/{docId}/tables/{tableIdOrName}/rows/{rowIdOrName}'
```

#### Delete Row
```bash
maton api -X DELETE '/coda/apis/v1/docs/{docId}/tables/{tableIdOrName}/rows/{rowIdOrName}'
```

### Formulas

#### List Formulas
```bash
maton api '/coda/apis/v1/docs/{docId}/formulas'
```

#### Get Formula
```bash
maton api '/coda/apis/v1/docs/{docId}/formulas/{formulaIdOrName}'
```

### Controls

#### List Controls
```bash
maton api '/coda/apis/v1/docs/{docId}/controls'
```

#### Get Control
```bash
maton api '/coda/apis/v1/docs/{docId}/controls/{controlIdOrName}'
```

### Permissions

#### Get Sharing Metadata
```bash
maton api '/coda/apis/v1/docs/{docId}/acl/metadata'
```

#### List Permissions
```bash
maton api '/coda/apis/v1/docs/{docId}/acl/permissions'
```

#### Add Permission
```bash
maton api -X POST '/coda/apis/v1/docs/{docId}/acl/permissions'
```

#### Delete Permission
```bash
maton api -X DELETE '/coda/apis/v1/docs/{docId}/acl/permissions/{permissionId}'
```

### Categories

#### List Categories
```bash
maton api '/coda/apis/v1/categories'
```

### Utilities

#### Resolve Browser Link
```bash
maton api '/coda/apis/v1/resolveBrowserLink?url={encodedUrl}'
```

#### Get Mutation Status
```bash
maton api '/coda/apis/v1/mutationStatus/{requestId}'
```

### Analytics

#### List Doc Analytics
```bash
maton api '/coda/apis/v1/analytics/docs'
```

#### List Pack Analytics
```bash
maton api '/coda/apis/v1/analytics/packs'
```

#### Get Analytics Update Time
```bash
maton api '/coda/apis/v1/analytics/updated'
```

## Query Parameters

Common parameters across endpoints:
- `limit` - Page size (max: 200)
- `pageToken` - Cursor for pagination
- `query` - Search filter
- `useColumnNames` - Use column names vs IDs (rows)
- `valueFormat` - simple, simpleWithArrays, rich (rows)

## Notes

- Mutations (create/update/delete) return HTTP 202 with requestId
- Use `/mutationStatus/{requestId}` to check completion
- Newly created docs need a moment before child resources are accessible
- Table/column names can be used instead of IDs
- Row operations require base tables, not views
- Page-level analytics require Enterprise plan

## Resources

- [Coda API Documentation](https://coda.io/developers/apis/v1)
