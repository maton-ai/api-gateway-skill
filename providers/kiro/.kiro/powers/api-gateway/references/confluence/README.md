# Confluence Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `confluence`
**Base URL proxied:** `api.atlassian.com`

## Getting Cloud ID

Confluence Cloud requires a cloud ID in the API path. First, get accessible resources:

```bash
maton api '/confluence/oauth/token/accessible-resources'
```

Response:
```json
[{
  "id": "62909843-b784-4c35-b770-e4e2a26f024b",
  "url": "https://yoursite.atlassian.net",
  "name": "yoursite",
  "scopes": ["read:confluence-content.all", "write:confluence-content", ...]
}]
```

## API Path Pattern

V2 API (recommended):
```
/confluence/ex/confluence/{cloudId}/wiki/api/v2/{endpoint}
```

V1 REST API (limited):
```
/confluence/ex/confluence/{cloudId}/wiki/rest/api/{endpoint}
```

## Common Endpoints (V2 API)

### Pages

#### List Pages
```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages'
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages?space-id={spaceId}'
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages?limit=25'
```

#### Get Page
```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}'
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}?body-format=storage'
```

#### Create Page
```bash
maton api -X POST '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "spaceId": "98306",
  "status": "current",
  "title": "Page Title",
  "body": {
    "representation": "storage",
    "value": "<p>Page content</p>"
  }
}
EOF
```

#### Update Page
```bash
maton api -X PUT '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "id": "98391",
  "status": "current",
  "title": "Updated Title",
  "body": {
    "representation": "storage",
    "value": "<p>Updated content</p>"
  },
  "version": {"number": 2}
}
EOF
```

#### Delete Page
```bash
maton api -X DELETE '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}'
```

#### Get Page Children
```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}/children'
```

#### Get Page Labels
```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}/labels'
```

#### Get Page Comments
```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}/footer-comments'
```

### Spaces

#### List Spaces
```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/spaces'
```

#### Get Space
```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/spaces/{spaceId}'
```

#### Get Space Pages
```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/spaces/{spaceId}/pages'
```

### Blogposts

#### List Blogposts
```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/blogposts'
```

#### Create Blogpost
```bash
maton api -X POST '/confluence/ex/confluence/{cloudId}/wiki/api/v2/blogposts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "spaceId": "98306",
  "title": "Blog Post Title",
  "body": {
    "representation": "storage",
    "value": "<p>Blog content</p>"
  }
}
EOF
```

### Comments

#### List Footer Comments
```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/footer-comments'
```

#### Create Footer Comment
```bash
maton api -X POST '/confluence/ex/confluence/{cloudId}/wiki/api/v2/footer-comments' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "pageId": "98391",
  "body": {
    "representation": "storage",
    "value": "<p>Comment text</p>"
  }
}
EOF
```

### Attachments

#### List Attachments
```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/attachments'
```

#### Get Page Attachments
```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}/attachments'
```

### Tasks

#### List Tasks
```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/tasks'
```

### User (V1 API)

#### Get Current User
```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/rest/api/user/current'
```

## Notes

- Always fetch cloud ID first using `/oauth/token/accessible-resources`
- V2 API is recommended for most operations
- Content uses Confluence storage format (XML-like): `<p>Paragraph</p>`
- When updating pages, you must increment the version number
- DELETE operations return 204 No Content
- Pagination uses cursor-based approach with `_links.next` containing the cursor value

## Resources

- [Confluence REST API V2 Introduction](https://developer.atlassian.com/cloud/confluence/rest/v2/intro/)
- [Page Operations](https://developer.atlassian.com/cloud/confluence/rest/v2/api-group-page/)
- [Space Operations](https://developer.atlassian.com/cloud/confluence/rest/v2/api-group-space/)
- [Blogpost Operations](https://developer.atlassian.com/cloud/confluence/rest/v2/api-group-blog-post/)
- [Comment Operations](https://developer.atlassian.com/cloud/confluence/rest/v2/api-group-comment/)
- [Confluence Storage Format](https://confluence.atlassian.com/doc/confluence-storage-format-790796544.html)
