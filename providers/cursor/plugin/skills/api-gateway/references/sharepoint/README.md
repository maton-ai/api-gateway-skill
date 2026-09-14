# SharePoint

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `sharepoint`
**Upstream base URL:** `graph.microsoft.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://graph.microsoft.com/v1.0/sites/root`
- Gateway: `https://api.maton.ai/sharepoint/v1.0/sites/root`

### Sites API

#### Get Root Site

```bash
maton api '/sharepoint/v1.0/sites/root'
```

**Response:**
```json
{
  "id": "contoso.sharepoint.com,guid1,guid2",
  "displayName": "Communication site",
  "name": "root",
  "webUrl": "https://contoso.sharepoint.com"
}
```

#### Get Site by ID

```bash
maton api '/sharepoint/v1.0/sites/{site_id}'
```

**Note:** `{site_id}` is a placeholder. Replace it with a real value before sending the request.

Site IDs follow the format: `{hostname},{site-guid},{web-guid}`

#### Get Site by Hostname and Path

```bash
maton api '/sharepoint/v1.0/sites/{hostname}:/{site-path}'
```

**Note:** `{hostname}` and `{site-path}` are placeholders. Replace each of them with real values before sending the request.

Example: `GET /sharepoint/v1.0/sites/contoso.sharepoint.com:/sites/marketing`

#### Get Root Site by Hostname

```bash
maton api '/sharepoint/v1.0/sites/{hostname}:/'
```

**Note:** `{hostname}` is a placeholder. Replace it with a real value before sending the request.

#### Search Sites

```bash
maton api '/sharepoint/v1.0/sites?search={query}'
```

**Note:** `{query}` is a placeholder. Replace it with a real value before sending the request.

#### List Subsites

```bash
maton api '/sharepoint/v1.0/sites/{site_id}/sites'
```

**Note:** `{site_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Site Columns

```bash
maton api '/sharepoint/v1.0/sites/{site_id}/columns'
```

**Note:** `{site_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Followed Sites

```bash
maton api '/sharepoint/v1.0/me/followedSites'
```

### Lists API

#### List Site Lists

```bash
maton api '/sharepoint/v1.0/sites/{site_id}/lists'
```

**Note:** `{site_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "value": [
    {
      "id": "b23974d6-a0aa-4e9b-9535-25393598b973",
      "name": "Events",
      "displayName": "Events",
      "webUrl": "https://contoso.sharepoint.com/Lists/Events"
    }
  ]
}
```

#### Get List

```bash
maton api '/sharepoint/v1.0/sites/{site_id}/lists/{list_id}'
```

**Note:** `{site_id}` and `{list_id}` are placeholders. Replace each of them with real values before sending the request.

#### List Columns

```bash
maton api '/sharepoint/v1.0/sites/{site_id}/lists/{list_id}/columns'
```

**Note:** `{site_id}` and `{list_id}` are placeholders. Replace each of them with real values before sending the request.

#### List Content Types

```bash
maton api '/sharepoint/v1.0/sites/{site_id}/lists/{list_id}/contentTypes'
```

**Note:** `{site_id}` and `{list_id}` are placeholders. Replace each of them with real values before sending the request.

#### List Items

```bash
maton api '/sharepoint/v1.0/sites/{site_id}/lists/{list_id}/items'
```

**Note:** `{site_id}` and `{list_id}` are placeholders. Replace each of them with real values before sending the request.

With field values (use `$expand=fields`):

```bash
maton api '/sharepoint/v1.0/sites/{site_id}/lists/{list_id}/items?$expand=fields'
```

**Note:** `{site_id}` and `{list_id}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "value": [
    {
      "id": "1",
      "createdDateTime": "2026-03-05T08:00:00Z",
      "fields": {
        "Title": "Team Meeting",
        "EventDate": "2026-03-10T14:00:00Z",
        "Location": "Conference Room A"
      }
    }
  ]
}
```

#### Get List Item

```bash
maton api '/sharepoint/v1.0/sites/{site_id}/lists/{list_id}/items/{item_id}?$expand=fields'
```

**Note:** `{site_id}`, `{list_id}` and `{item_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create List Item

```bash
maton api -X POST '/sharepoint/v1.0/sites/{site_id}/lists/{list_id}/items' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "fields": {
    "Title": "New Event",
    "EventDate": "2026-04-01T10:00:00Z",
    "Location": "Main Hall"
  }
}
JSON
```

**Note:** `{site_id}` and `{list_id}` are placeholders. Replace each of them with real values before sending the request.

#### Update List Item

```bash
maton api -X PATCH '/sharepoint/v1.0/sites/{site_id}/lists/{list_id}/items/{item_id}/fields' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "Title": "Updated Event Title"
}
JSON
```

**Note:** `{site_id}`, `{list_id}` and `{item_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete List Item

```bash
maton api '/sharepoint/v1.0/sites/{site_id}/lists/{list_id}/items/{item_id}' -X DELETE
```

**Note:** `{site_id}`, `{list_id}` and `{item_id}` are placeholders. Replace each of them with real values before sending the request.

Returns `204 No Content` on success.

### Drives API

#### List Site Drives

```bash
maton api '/sharepoint/v1.0/sites/{site_id}/drives'
```

**Note:** `{site_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Default Drive

```bash
maton api '/sharepoint/v1.0/sites/{site_id}/drive'
```

**Note:** `{site_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Drive by ID

```bash
maton api '/sharepoint/v1.0/drives/{drive_id}'
```

**Note:** `{drive_id}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Drive IDs containing `!` (e.g., `b!abc123`) must be URL-encoded: `b%21abc123`

### Drive Content API

#### List Root Contents

```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/root/children'
```

**Note:** `{drive_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "value": [
    {
      "id": "01WBMXT7NQEEYJ3BAXL5...",
      "name": "Documents",
      "folder": { "childCount": 5 },
      "webUrl": "https://contoso.sharepoint.com/Shared%20Documents/Documents"
    },
    {
      "id": "01WBMXT7LISS5OMIG4CZ...",
      "name": "report.docx",
      "file": { "mimeType": "application/vnd.openxmlformats-officedocument.wordprocessingml.document" },
      "size": 25600
    }
  ]
}
```

#### Get Item by ID

```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}'
```

**Note:** `{drive_id}` and `{item_id}` are placeholders. Replace each of them with real values before sending the request.

#### Get Item by Path

```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/root:/{path}'
```

**Note:** `{drive_id}` and `{path}` are placeholders. Replace each of them with real values before sending the request.

Example: `GET /sharepoint/v1.0/drives/{drive_id}/root:/Reports/Q1.xlsx`

#### List Folder Contents

```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/items/{folder_id}/children'
```

**Note:** `{drive_id}` and `{folder_id}` are placeholders. Replace each of them with real values before sending the request.

Or by path:

```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/root:/{folder_path}:/children'
```

**Note:** `{drive_id}` and `{folder_path}` are placeholders. Replace each of them with real values before sending the request.

#### Download File

```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}/content'
```

**Note:** `{drive_id}` and `{item_id}` are placeholders. Replace each of them with real values before sending the request.

Or by path:

```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/root:/{path}:/content'
```

**Note:** `{drive_id}` and `{path}` are placeholders. Replace each of them with real values before sending the request.

Returns a redirect to the download URL (follow redirects to get file content).

#### Upload File (Simple - up to 4MB)

```bash
maton api -X PUT '/sharepoint/v1.0/drives/{drive_id}/root:/{filename}:/content' -H 'Content-Type: application/octet-stream' \
  --input ./file.txt
```

**Note:** `{drive_id}` and `{filename}` are placeholders. Replace each of them with real values before sending the request.

Example:
```bash
maton api -X PUT '/sharepoint/v1.0/drives/{drive_id}/root:/{file_path}:/content' -H 'Content-Type: text/plain' --input - <<'BODY'
File content here
BODY
```

**Note:** `{drive_id}` and `{file_path}` are placeholders. Replace each of them with real values before sending the request.

#### Create Folder

```bash
maton api -X POST '/sharepoint/v1.0/drives/{drive_id}/root/children' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Folder",
  "folder": {},
  "@microsoft.graph.conflictBehavior": "rename"
}
JSON
```

**Note:** `{drive_id}` is a placeholder. Replace it with a real value before sending the request.

Or in a specific folder:

```bash
maton api -X POST '/sharepoint/v1.0/drives/{drive_id}/items/{parent_id}/children'
```

**Note:** `{drive_id}` and `{parent_id}` are placeholders. Replace each of them with real values before sending the request.

#### Rename/Move Item

```bash
maton api -X PATCH '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "new-filename.txt"
}
JSON
```

**Note:** `{drive_id}` and `{item_id}` are placeholders. Replace each of them with real values before sending the request.

To move to another folder:

```bash
maton api -X PATCH '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "parentReference": {
    "id": "{target_folder_id}"
  }
}
JSON
```

**Note:** `{drive_id}`, `{item_id}` and `{target_folder_id}` are placeholders. Replace each of them with real values before sending the request.

#### Copy Item

```bash
maton api -X POST '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}/copy' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "copied-file.txt"
}
JSON
```

**Note:** `{drive_id}` and `{item_id}` are placeholders. Replace each of them with real values before sending the request.

This is an async operation - returns `202 Accepted` with a `Location` header for progress tracking.

#### Delete Item

```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}' -X DELETE
```

**Note:** `{drive_id}` and `{item_id}` are placeholders. Replace each of them with real values before sending the request.

Returns `204 No Content` on success. Deleted items go to the recycle bin.

#### Search Files

```bash
maton api "/sharepoint/v1.0/drives/{drive_id}/root/search(q='{query}')"
```

**Note:** `{drive_id}` and `{query}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "value": [
    {
      "id": "01WBMXT7...",
      "name": "quarterly-report.xlsx",
      "webUrl": "https://contoso.sharepoint.com/..."
    }
  ]
}
```

#### Track Changes (Delta)

```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/root/delta'
```

**Note:** `{drive_id}` is a placeholder. Replace it with a real value before sending the request.

Returns changed items and a `@odata.deltaLink` for subsequent requests.

### Permissions API

#### Get Item Permissions

```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}/permissions'
```

**Note:** `{drive_id}` and `{item_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Sharing Link

```bash
maton api -X POST '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}/createLink' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "type": "view",
  "scope": "organization"
}
JSON
```

**Note:** `{drive_id}` and `{item_id}` are placeholders. Replace each of them with real values before sending the request.

**Request body:**
- `type`: `view`, `edit`, or `embed`
- `scope`: `anonymous`, `organization`, or `users`

**Response:**
```json
{
  "id": "f0cfb2bd-ef5f-4451-9932-8e9a3e219aaa",
  "roles": ["read"],
  "link": {
    "type": "view",
    "scope": "organization",
    "webUrl": "https://contoso.sharepoint.com/:t:/g/..."
  }
}
```

### Versions API

#### List File Versions

```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}/versions'
```

**Note:** `{drive_id}` and `{item_id}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "value": [
    {
      "id": "2.0",
      "lastModifiedDateTime": "2026-03-05T08:07:12Z",
      "size": 25600,
      "lastModifiedBy": {
        "user": { "displayName": "John Doe" }
      }
    },
    {
      "id": "1.0",
      "lastModifiedDateTime": "2026-03-04T10:00:00Z",
      "size": 24000
    }
  ]
}
```

#### Get Specific Version

```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}/versions/{version_id}'
```

**Note:** `{drive_id}`, `{item_id}` and `{version_id}` are placeholders. Replace each of them with real values before sending the request.

#### Download Version Content

```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}/versions/{version_id}/content'
```

**Note:** `{drive_id}`, `{item_id}` and `{version_id}` are placeholders. Replace each of them with real values before sending the request.

### Thumbnails API

#### Get Item Thumbnails

```bash
maton api '/sharepoint/v1.0/drives/{drive_id}/items/{item_id}/thumbnails'
```

**Note:** `{drive_id}` and `{item_id}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "value": [
    {
      "id": "0",
      "small": { "height": 96, "width": 96, "url": "..." },
      "medium": { "height": 176, "width": 176, "url": "..." },
      "large": { "height": 800, "width": 800, "url": "..." }
    }
  ]
}
```

### OData Query parameters

SharePoint/Graph API supports OData query parameters:

| Parameter | Description | Example |
|-----------|-------------|---------|
| `$select` | Select specific properties | `?$select=id,name,size` |
| `$expand` | Expand related entities | `?$expand=fields` |
| `$filter` | Filter results | `?$filter=name eq 'Report'` |
| `$orderby` | Sort results | `?$orderby=lastModifiedDateTime desc` |
| `$top` | Limit results | `?$top=10` |
| `$skip` | Skip results (pagination) | `?$skip=10` |

Example with multiple parameters:

```bash
maton api '/sharepoint/v1.0/sites/{site_id}/lists/{list_id}/items?$expand=fields&$top=50&$orderby=createdDateTime%20desc'
```

**Note:** `{site_id}` and `{list_id}` are placeholders. Replace each of them with real values before sending the request.

### Notes

- Site IDs follow the format: `{hostname},{site-guid},{web-guid}`
- Drive IDs with `!` (e.g., `b!abc123`) must be URL-encoded (`b%21abc123`)
- Item IDs are opaque strings (e.g., `01WBMXT7NQEEYJ3BAXL5...`)
- File uploads via PUT are limited to 4MB; use upload sessions for larger files
- Copy operations are asynchronous - check the Location header for progress
- Deleted items go to the SharePoint recycle bin
- Some admin operations require elevated permissions (Sites.FullControl.All)

### Resources

- [Sites API](https://learn.microsoft.com/en-us/graph/api/resources/sharepoint)
- [DriveItem API](https://learn.microsoft.com/en-us/graph/api/resources/driveitem)
- [List API](https://learn.microsoft.com/en-us/graph/api/resources/list)
- [Maton CLI Manual](https://cli.maton.ai/manual)
