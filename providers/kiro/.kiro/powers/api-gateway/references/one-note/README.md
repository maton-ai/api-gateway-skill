# OneNote Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `one-note`
**Base URL proxied:** `graph.microsoft.com`

## API Path Pattern

```
/one-note/v1.0/me/onenote/{resource}
```

## Common Endpoints

### List Notebooks
```bash
maton api '/one-note/v1.0/me/onenote/notebooks'
```

### Get Notebook
```bash
maton api '/one-note/v1.0/me/onenote/notebooks/{notebook_id}'
```

### Create Notebook
```bash
maton api -X POST '/one-note/v1.0/me/onenote/notebooks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "displayName": "New Notebook"
}
EOF
```

### List Notebooks with Sections
```bash
maton api '/one-note/v1.0/me/onenote/notebooks?$expand=sections,sectionGroups'
```

### Copy Notebook
```bash
maton api -X POST '/one-note/v1.0/me/onenote/notebooks/{notebook_id}/copyNotebook' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "renameAs": "Copied Notebook"
}
EOF
```

### Get Recent Notebooks
```bash
maton api '/one-note/v1.0/me/onenote/notebooks/getRecentNotebooks(includePersonalNotebooks=true)'
```

### List Sections
```bash
maton api '/one-note/v1.0/me/onenote/sections'
```

### List Notebook Sections
```bash
maton api '/one-note/v1.0/me/onenote/notebooks/{notebook_id}/sections'
```

### Create Section
```bash
maton api -X POST '/one-note/v1.0/me/onenote/notebooks/{notebook_id}/sections' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "displayName": "New Section"
}
EOF
```

### List Section Groups
```bash
maton api '/one-note/v1.0/me/onenote/sectionGroups'
```

### Create Section Group
```bash
maton api -X POST '/one-note/v1.0/me/onenote/notebooks/{notebook_id}/sectionGroups' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "displayName": "New Section Group"
}
EOF
```

### List Pages
```bash
maton api '/one-note/v1.0/me/onenote/pages'
```

### List Section Pages
```bash
maton api '/one-note/v1.0/me/onenote/sections/{section_id}/pages'
```

### Get Page Content
```bash
maton api '/one-note/v1.0/me/onenote/pages/{page_id}/content'
```

### Create Page
```bash
maton api -X POST '/one-note/v1.0/me/onenote/sections/{section_id}/pages' \
  -H 'Content-Type: text/html' \
  --input - <<'EOF'
<!DOCTYPE html>
<html>
  <head><title>Page Title</title></head>
  <body><p>Content</p></body>
</html>
EOF
```

### Update Page Content
```bash
maton api -X PATCH '/one-note/v1.0/me/onenote/pages/{page_id}/content' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
[
  {
    "target": "body",
    "action": "append",
    "content": "<p>Appended content</p>"
  }
]
EOF
```

## OData Query Parameters

| Parameter | Description |
|-----------|-------------|
| `$select` | Select properties (`$select=id,displayName`) |
| `$expand` | Expand relations (`$expand=sections`) |
| `$filter` | Filter results (`$filter=isDefault eq true`) |
| `$orderby` | Sort results |
| `$top` | Limit results |

## Notes

- Uses Microsoft Graph API v1.0
- Pages created with HTML (Content-Type: text/html)
- Page updates use PATCH with JSON operations
- Copy operations are asynchronous
- Use `$expand=sections,sectionGroups` to get full notebook structure

## Resources

- [OneNote API Overview](https://learn.microsoft.com/en-us/graph/integrate-with-onenote)
- [OneNote REST API Reference](https://learn.microsoft.com/en-us/graph/api/resources/onenote-api-overview)
