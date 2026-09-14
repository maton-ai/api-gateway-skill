# OneNote

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `one-note`
**Upstream base URL:** `graph.microsoft.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://graph.microsoft.com/v1.0/me/onenote/notebooks`
- Gateway: `https://api.maton.ai/one-note/v1.0/me/onenote/notebooks`

### Notebooks API

#### List Notebooks

```bash
maton api '/one-note/v1.0/me/onenote/notebooks'
```

**Example:**

```bash
maton api '/one-note/v1.0/me/onenote/notebooks'
```

**Response:**
```json
{
  "value": [
    {
      "id": "1-30487038-8c2e-440a-860d-e82c6dc74f10",
      "displayName": "My Notebook",
      "createdDateTime": "2026-03-12T10:25:00Z",
      "lastModifiedDateTime": "2026-03-12T10:30:00Z",
      "isDefault": true,
      "isShared": false,
      "sectionsUrl": "https://graph.microsoft.com/v1.0/me/onenote/notebooks/.../sections",
      "sectionGroupsUrl": "https://graph.microsoft.com/v1.0/me/onenote/notebooks/.../sectionGroups"
    }
  ]
}
```

#### List Notebooks with Sections

Use `$expand` to include sections and section groups:

```bash
maton api '/one-note/v1.0/me/onenote/notebooks?$expand=sections,sectionGroups'
```

#### Get Notebook

```bash
maton api '/one-note/v1.0/me/onenote/notebooks/{notebook_id}'
```

**Note:** `{notebook_id}` is a placeholder. Replace it with a real value before sending the request.

**Example:**

```bash
maton api '/one-note/v1.0/me/onenote/notebooks/{notebook_id}'
```

**Note:** `{notebook_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Notebook

```bash
maton api -X POST '/one-note/v1.0/me/onenote/notebooks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "displayName": "New Notebook"
}
JSON
```

**Example:**

```bash
maton api -X POST '/one-note/v1.0/me/onenote/notebooks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "displayName": "My New Notebook"
}
JSON
```

#### Copy Notebook

```bash
maton api -X POST '/one-note/v1.0/me/onenote/notebooks/{notebook_id}/copyNotebook'
```

**Note:** `{notebook_id}` is a placeholder. Replace it with a real value before sending the request.

**Example:**

```bash
maton api -X POST '/one-note/v1.0/me/onenote/notebooks/{notebook_id}/copyNotebook' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "renameAs": "Copied Notebook"
}
JSON
```

**Note:** `{notebook_id}` is a placeholder. Replace it with a real value before sending the request.

> **Note:** Copy operations are asynchronous. The response includes a status URL to check progress.

#### Get Recent Notebooks

```bash
maton api '/one-note/v1.0/me/onenote/notebooks/getRecentNotebooks(includePersonalNotebooks=true)'
```

**Example:**

```bash
maton api '/one-note/v1.0/me/onenote/notebooks/getRecentNotebooks(includePersonalNotebooks=true)'
```

### Sections API

#### List All Sections

```bash
maton api '/one-note/v1.0/me/onenote/sections'
```

**Example:**

```bash
maton api '/one-note/v1.0/me/onenote/sections'
```

**Response:**
```json
{
  "value": [
    {
      "id": "1-c9d63289-4f64-4579-9043-155543978c78",
      "displayName": "My Section",
      "createdDateTime": "2026-03-12T10:26:00Z",
      "lastModifiedDateTime": "2026-03-12T10:28:00Z",
      "isDefault": false,
      "pagesUrl": "https://graph.microsoft.com/v1.0/me/onenote/sections/.../pages"
    }
  ]
}
```

#### List Sections in Notebook

```bash
maton api '/one-note/v1.0/me/onenote/notebooks/{notebook_id}/sections'
```

**Note:** `{notebook_id}` is a placeholder. Replace it with a real value before sending the request.

**Example:**

```bash
maton api '/one-note/v1.0/me/onenote/notebooks/{notebook_id}/sections'
```

**Note:** `{notebook_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Section

```bash
maton api '/one-note/v1.0/me/onenote/sections/{section_id}'
```

**Note:** `{section_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Section

```bash
maton api -X POST '/one-note/v1.0/me/onenote/notebooks/{notebook_id}/sections' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "displayName": "New Section"
}
JSON
```

**Note:** `{notebook_id}` is a placeholder. Replace it with a real value before sending the request.

**Example:**

```bash
maton api -X POST '/one-note/v1.0/me/onenote/notebooks/{notebook_id}/sections' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "displayName": "Meeting Notes"
}
JSON
```

**Note:** `{notebook_id}` is a placeholder. Replace it with a real value before sending the request.

### Section Groups API

Organize sections into groups.

#### List All Section Groups

```bash
maton api '/one-note/v1.0/me/onenote/sectionGroups'
```

**Example:**

```bash
maton api '/one-note/v1.0/me/onenote/sectionGroups'
```

#### List Section Groups in Notebook

```bash
maton api '/one-note/v1.0/me/onenote/notebooks/{notebook_id}/sectionGroups'
```

**Note:** `{notebook_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Section Group

```bash
maton api '/one-note/v1.0/me/onenote/sectionGroups/{section_group_id}'
```

**Note:** `{section_group_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Section Group

```bash
maton api -X POST '/one-note/v1.0/me/onenote/notebooks/{notebook_id}/sectionGroups' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "displayName": "New Section Group"
}
JSON
```

**Note:** `{notebook_id}` is a placeholder. Replace it with a real value before sending the request.

**Example:**

```bash
maton api -X POST '/one-note/v1.0/me/onenote/notebooks/{notebook_id}/sectionGroups' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "displayName": "Project Notes"
}
JSON
```

**Note:** `{notebook_id}` is a placeholder. Replace it with a real value before sending the request.

### Pages API

#### List All Pages

```bash
maton api '/one-note/v1.0/me/onenote/pages'
```

**Example:**

```bash
maton api '/one-note/v1.0/me/onenote/pages'
```

**Response:**
```json
{
  "value": [
    {
      "id": "1-42a904024c734393b561d0a85428965d!251-c9d63289-4f64-4579-9043-155543978c78",
      "title": "My Page",
      "createdDateTime": "2026-03-12T10:29:42Z",
      "lastModifiedDateTime": "2026-03-12T10:30:00Z",
      "contentUrl": "https://graph.microsoft.com/v1.0/me/onenote/pages/.../content"
    }
  ]
}
```

#### List Pages in Section

```bash
maton api '/one-note/v1.0/me/onenote/sections/{section_id}/pages'
```

**Note:** `{section_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Page

```bash
maton api '/one-note/v1.0/me/onenote/pages/{page_id}'
```

**Note:** `{page_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Page Content

Returns the HTML content of a page:

```bash
maton api '/one-note/v1.0/me/onenote/pages/{page_id}/content'
```

**Note:** `{page_id}` is a placeholder. Replace it with a real value before sending the request.

**Example:**

```bash
maton api '/one-note/v1.0/me/onenote/pages/{page_id}/content'
```

**Note:** `{page_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Page

Pages are created with HTML content:

```bash
maton api -X POST '/one-note/v1.0/me/onenote/sections/{section_id}/pages' -H 'Content-Type: text/html' --input - <<'BODY'
<!DOCTYPE html>
<html>
  <head>
    <title>Page Title</title>
  </head>
  <body>
    <p>Page content here</p>
  </body>
</html>
BODY
```

**Note:** `{section_id}` is a placeholder. Replace it with a real value before sending the request.

**Example:**

```bash
maton api -X POST '/one-note/v1.0/me/onenote/sections/{section_id}/pages' -H 'Content-Type: text/html' --input - <<'BODY'
<!DOCTYPE html>
<html>
  <head>
    <title>Meeting Notes - March 12</title>
  </head>
  <body>
    <h1>Meeting Notes</h1>
    <p>Attendees: Alice, Bob, Charlie</p>
    <ul>
      <li>Discussed Q1 goals</li>
      <li>Reviewed project timeline</li>
    </ul>
  </body>
</html>
BODY
```

**Note:** `{section_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Page Content

Use PATCH to append, insert, or replace content:

```bash
maton api -X PATCH '/one-note/v1.0/me/onenote/pages/{page_id}/content' -H 'Content-Type: application/json' --input - <<'JSON'
[
  {
    "target": "body",
    "action": "append",
    "content": "<p>New paragraph added!</p>"
  }
]
JSON
```

**Note:** `{page_id}` is a placeholder. Replace it with a real value before sending the request.

**Actions:**
- `append` - Add content at the end of target
- `prepend` - Add content at the beginning of target
- `replace` - Replace target content
- `insert` - Insert after target

**Example:**

```bash
maton api -X PATCH '/one-note/v1.0/me/onenote/pages/{page_id}/content' -H 'Content-Type: application/json' --input - <<'JSON'
[
  {
    "target": "body",
    "action": "append",
    "content": "<p>Updated at 2026-03-12</p>"
  }
]
JSON
```

**Note:** `{page_id}` is a placeholder. Replace it with a real value before sending the request.

### OData Query parameters

The OneNote API supports OData query parameters:

| Parameter | Description | Example |
|-----------|-------------|---------|
| `$select` | Select specific properties | `$select=id,displayName` |
| `$expand` | Include related resources | `$expand=sections,sectionGroups` |
| `$filter` | Filter results | `$filter=isDefault eq true` |
| `$orderby` | Sort results | `$orderby=displayName` |
| `$top` | Limit results | `$top=10` |
| `$skip` | Skip results | `$skip=20` |

**Example with $select:**

```bash
maton api '/one-note/v1.0/me/onenote/notebooks?$select=id,displayName'
```

### Page HTML Format

OneNote pages use a specific HTML format:

#### Basic Structure

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Page Title</title>
    <meta name="created" content="2026-03-12T10:00:00Z" />
  </head>
  <body>
    <p>Content here</p>
  </body>
</html>
```

#### Supported Elements

- Headings: `<h1>` through `<h6>`
- Paragraphs: `<p>`
- Lists: `<ul>`, `<ol>`, `<li>`
- Tables: `<table>`, `<tr>`, `<td>`
- Images: `<img src="..." />`
- Links: `<a href="...">`
- Formatting: `<b>`, `<i>`, `<u>`, `<strike>`

#### Adding Images

```html
<img src="https://example.com/image.jpg" alt="Description" />
```

Or embed base64 images:

```html
<img src="data:image/png;base64,..." alt="Embedded image" />
```

### Notes

- OneNote uses Microsoft Graph API v1.0
- Pages are created with HTML content (Content-Type: text/html)
- Page updates use PATCH with JSON array of operations
- Copy operations are asynchronous - check the returned status URL
- Use `$expand=sections,sectionGroups` to get notebook contents in one call
- Notebook and section names must be unique within their container

### Resources

- [OneNote API Overview](https://learn.microsoft.com/en-us/graph/integrate-with-onenote)
- [OneNote REST API Reference](https://learn.microsoft.com/en-us/graph/api/resources/onenote-api-overview)
- [Maton CLI Manual](https://cli.maton.ai/manual)
