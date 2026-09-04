# SharePoint Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `sharepoint`
**Base URL proxied:** `graph.microsoft.com`

## API Path Pattern

```
/sharepoint/v1.0/sites/{site_id}
/sharepoint/v1.0/sites/{site_id}/lists
/sharepoint/v1.0/sites/{site_id}/lists/{list_id}/items
/sharepoint/v1.0/sites/{site_id}/drives
/sharepoint/v1.0/drives/{drive_id}/root/children
/sharepoint/v1.0/drives/{drive_id}/items/{item_id}
```

## Sites

### Get Root Site
```bash
maton api '/sharepoint/v1.0/sites/root'
```

### Get Site by ID
```bash
maton api '/sharepoint/v1.0/sites/{site_id}'
```

Site IDs follow the format: `{hostname},{site-guid},{web-guid}`

### Get Site by Hostname
```bash
maton api '/sharepoint/v1.0/sites/{hostname}:/'
maton api '/sharepoint/v1.0/sites/{hostname}:/{site-path}'
```

### Search Sites
```bash
maton api '/sharepoint/v1.0/sites?search={query}'
```

### List Subsites
```bash
maton api '/sharepoint/v1.0/sites/{site_id}/sites'
```

### Get Site Columns
```bash
maton api '/sharepoint/v1.0/sites/{site_id}/columns'
```

### Get Followed Sites
```bash
maton api '/sharepoint/v1.0/me/followedSites'
```

## Lists

### List Site Lists
```bash
maton api '/sharepoint/v1.0/sites/{site_id}/lists'
```

### Get List
```bash
maton api '/sharepoint/v1.0/sites/{site_id}/lists/{list_id}'
```

### List Columns
```bash
maton api '/sharepoint/v1.0/sites/{site_id}/lists/{list_id}/columns'
```

### List Content Types
```bash
maton api '/sharepoint/v1.0/sites/{site_id}/lists/{list_id}/contentTypes'
```

### List Items
```bash
maton api '/sharepoint/v1.0/sites/{site_id}/lists/{list_id}/items'
maton api '/sharepoint/v1.0/sites/{site_id}/lists/{list_id}/items?$expand=fields'
```

### Create List Item
```bash
maton api -X POST '/sharepoint/v1.0/sites/{site_id}/lists/{list_id}/items' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "fields": {
    "Title": "New Item",
    "Description": "Item description"
  }
}
EOF
```

### Update List Item
```bash
maton api -X PATCH '/sharepoint/v1.0/sites/{site_id}/lists/{list_id}/items/{item_id}/fields' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "Title": "Updated Title"
}
EOF
```

### Delete List Item
```bash
maton api -X DELETE '/sharepoint/v1.0/sites/{site_id}/lists/{list_id}/items/{item_id}'
```

## Drives (Document Libraries)

### List Site Drives
```bash
maton api '/sharepoint/v1.0/sites/{site_id}/drives'
```

### Get Default Drive
```bash
maton api '/sharepoint/v1.0/sites/{site_id}/drive'
```

### Get Drive by ID
```bash
maton api '/sharepoint/v1.0/drives/{drive_id}'
```

## Files and Folders

### List Root Contents
```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/root/children'
```

### Get Item by ID
```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}'
```

### Get Item by Path
```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/root:/{path}'
```

### List Folder Contents
```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/items/{folder_id}/children'
```

### Download File
```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}/content'
maton api '/sharepoint/v1.0/drives/{drive_id}/root:/{path}:/content'
```

### Upload File
```bash
maton api -X PUT '/sharepoint/v1.0/drives/{drive_id}/root:/{filename}:/content' \
  -H 'Content-Type: application/octet-stream'
```

### Create Folder
```bash
maton api -X POST '/sharepoint/v1.0/drives/{drive_id}/root/children' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "New Folder",
  "folder": {},
  "@microsoft.graph.conflictBehavior": "rename"
}
EOF
```

### Rename/Move Item
```bash
maton api -X PATCH '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "new-name.txt"
}
EOF
```

### Copy Item
```bash
maton api -X POST '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}/copy' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "copied-file.txt"
}
EOF
```

### Delete Item
```bash
maton api -X DELETE '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}'
```

### Search Files
```bash
maton api "/sharepoint/v1.0/drives/{drive_id}/root/search(q='{query}')"
```

### Track Changes (Delta)
```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/root/delta'
```

## Sharing and Permissions

### Get Permissions
```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}/permissions'
```

### Create Sharing Link
```bash
maton api -X POST '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}/createLink' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "type": "view",
  "scope": "organization"
}
EOF
```

## Versions

### List Versions
```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}/versions'
```

### Get Version
```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}/versions/{version_id}'
```

### Download Version Content
```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}/versions/{version_id}/content'
```

## Thumbnails

### Get Thumbnails
```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}/thumbnails'
```

## Query Parameters

- `$select` - Select specific properties
- `$expand` - Expand related entities (e.g., `fields` for list items)
- `$filter` - Filter results
- `$orderby` - Sort results
- `$top` - Limit results
- `$skip` - Skip results (pagination)

## Notes

- Site IDs follow the format: `{hostname},{site-guid},{web-guid}`
- Drive IDs with `!` must be URL-encoded: `b!abc123` → `b%21abc123`
- File uploads via PUT limited to 4MB; use upload sessions for larger files
- Copy operations are asynchronous (returns 202)
- Deleted items go to the SharePoint recycle bin

## Resources

- [SharePoint Sites API](https://learn.microsoft.com/en-us/graph/api/resources/sharepoint)
- [DriveItem API](https://learn.microsoft.com/en-us/graph/api/resources/driveitem)
- [List API](https://learn.microsoft.com/en-us/graph/api/resources/list)
