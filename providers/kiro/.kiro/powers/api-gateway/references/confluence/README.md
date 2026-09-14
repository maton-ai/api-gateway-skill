# Confluence

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `confluence`
**Upstream base URL:** `api.atlassian.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.atlassian.com/oauth/token/accessible-resources`
- Gateway: `https://api.maton.ai/confluence/oauth/token/accessible-resources`

**Important:**

V2 API (recommended):
```
/confluence/ex/confluence/{cloudId}/wiki/api/v2
```

V1 API (limited):
```
/confluence/ex/confluence/{cloudId}/wiki/rest/api
```

### User Info API

#### Get Cloud ID

Confluence Cloud requires a cloud ID in the API path. First, get accessible resources:

```bash
maton api '/confluence/oauth/token/accessible-resources'
```

**Response:**

```json
[{
  "id": "62909843-b784-4c35-b770-e4e2a26f024b",
  "url": "https://yoursite.atlassian.net",
  "name": "yoursite",
  "scopes": ["read:confluence-content.all", "write:confluence-content", ...]
}]
```

### Pages API

#### List Pages

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages'

maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages?space-id={spaceId}'

maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages?limit=25'

maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages?status=current'

maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages?body-format=storage'
```

**Note:** `{cloudId}` and `{spaceId}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "results": [
    {
      "id": "98391",
      "status": "current",
      "title": "My Page",
      "spaceId": "98306",
      "parentId": "98305",
      "parentType": "page",
      "authorId": "557058:...",
      "createdAt": "2026-02-12T23:00:00.000Z",
      "version": {
        "number": 1,
        "authorId": "557058:...",
        "createdAt": "2026-02-12T23:00:00.000Z"
      },
      "_links": {
        "webui": "/spaces/SPACEKEY/pages/98391/My+Page"
      }
    }
  ],
  "_links": {
    "next": "/wiki/api/v2/pages?cursor=..."
  }
}
```

#### Get Page

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}'

maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}?body-format=storage'

maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}?body-format=atlas_doc_format'

maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}?body-format=view'
```

**Note:** `{cloudId}` and `{pageId}` are placeholders. Replace each of them with real values before sending the request.

**Body formats:**
- `storage` - Confluence storage format (XML-like)
- `atlas_doc_format` - Atlassian Document Format (JSON)
- `view` - Rendered HTML

#### Create Page

```bash
maton api -X POST '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "spaceId": "98306",
  "status": "current",
  "title": "New Page Title",
  "body": {
    "representation": "storage",
    "value": "<p>Page content in storage format</p>"
  }
}
JSON
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

To create a child page, include `parentId`:

```json
{
  "spaceId": "98306",
  "parentId": "98391",
  "status": "current",
  "title": "Child Page",
  "body": {
    "representation": "storage",
    "value": "<p>Child page content</p>"
  }
}
```

**Response:**
```json
{
  "id": "98642",
  "status": "current",
  "title": "New Page Title",
  "spaceId": "98306",
  "version": {
    "number": 1
  }
}
```

#### Update Page

```bash
maton api -X PUT '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "id": "98391",
  "status": "current",
  "title": "Updated Page Title",
  "body": {
    "representation": "storage",
    "value": "<p>Updated content</p>"
  },
  "version": {
    "number": 2,
    "message": "Updated via API"
  }
}
JSON
```

**Note:** `{cloudId}` and `{pageId}` are placeholders. Replace each of them with real values before sending the request.

**Note:** You must increment the version number with each update.

#### Delete Page

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}' -X DELETE
```

**Note:** `{cloudId}` and `{pageId}` are placeholders. Replace each of them with real values before sending the request.

Returns `204 No Content` on success.

#### Get Page Children

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}/children'
```

**Note:** `{cloudId}` and `{pageId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Page Versions

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}/versions'
```

**Note:** `{cloudId}` and `{pageId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Page Labels

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}/labels'
```

**Note:** `{cloudId}` and `{pageId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Page Attachments

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}/attachments'
```

**Note:** `{cloudId}` and `{pageId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Page Comments

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}/footer-comments'
```

**Note:** `{cloudId}` and `{pageId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Page Properties

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}/properties'

maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}/properties/{propertyId}'
```

**Note:** `{cloudId}`, `{pageId}` and `{propertyId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Page Property

```bash
maton api -X POST '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}/properties' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "key": "my-property-key",
  "value": {"customKey": "customValue"}
}
JSON
```

**Note:** `{cloudId}` and `{pageId}` are placeholders. Replace each of them with real values before sending the request.

#### Update Page Property

```bash
maton api -X PUT '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}/properties/{propertyId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "key": "my-property-key",
  "value": {"customKey": "updatedValue"},
  "version": {"number": 2}
}
JSON
```

**Note:** `{cloudId}`, `{pageId}` and `{propertyId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Page Property

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages/{pageId}/properties/{propertyId}' -X DELETE
```

**Note:** `{cloudId}`, `{pageId}` and `{propertyId}` are placeholders. Replace each of them with real values before sending the request.

### Spaces API

#### List Spaces

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/spaces'

maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/spaces?limit=25'

maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/spaces?type=global'
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "results": [
    {
      "id": "98306",
      "key": "SPACEKEY",
      "name": "Space Name",
      "type": "global",
      "status": "current",
      "authorId": "557058:...",
      "createdAt": "2026-02-12T23:00:00.000Z",
      "homepageId": "98305",
      "_links": {
        "webui": "/spaces/SPACEKEY"
      }
    }
  ]
}
```

#### Get Space

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/spaces/{spaceId}'
```

**Note:** `{cloudId}` and `{spaceId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Space Pages

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/spaces/{spaceId}/pages'
```

**Note:** `{cloudId}` and `{spaceId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Space Blogposts

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/spaces/{spaceId}/blogposts'
```

**Note:** `{cloudId}` and `{spaceId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Space Properties

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/spaces/{spaceId}/properties'
```

**Note:** `{cloudId}` and `{spaceId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Space Property

```bash
maton api -X POST '/confluence/ex/confluence/{cloudId}/wiki/api/v2/spaces/{spaceId}/properties' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "key": "space-property-key",
  "value": {"key": "value"}
}
JSON
```

**Note:** `{cloudId}` and `{spaceId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Space Permissions

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/spaces/{spaceId}/permissions'
```

**Note:** `{cloudId}` and `{spaceId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Space Labels

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/spaces/{spaceId}/labels'
```

**Note:** `{cloudId}` and `{spaceId}` are placeholders. Replace each of them with real values before sending the request.

### Blogposts API

#### List Blogposts

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/blogposts'

maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/blogposts?space-id={spaceId}'

maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/blogposts?limit=25'
```

**Note:** `{cloudId}` and `{spaceId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Blogpost

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/blogposts/{blogpostId}'

maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/blogposts/{blogpostId}?body-format=storage'
```

**Note:** `{cloudId}` and `{blogpostId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Blogpost

```bash
maton api -X POST '/confluence/ex/confluence/{cloudId}/wiki/api/v2/blogposts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "spaceId": "98306",
  "title": "My Blog Post",
  "body": {
    "representation": "storage",
    "value": "<p>Blog post content</p>"
  }
}
JSON
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Blogpost

```bash
maton api -X PUT '/confluence/ex/confluence/{cloudId}/wiki/api/v2/blogposts/{blogpostId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "id": "458753",
  "status": "current",
  "title": "Updated Blog Post",
  "body": {
    "representation": "storage",
    "value": "<p>Updated content</p>"
  },
  "version": {
    "number": 2
  }
}
JSON
```

**Note:** `{cloudId}` and `{blogpostId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Blogpost

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/blogposts/{blogpostId}' -X DELETE
```

**Note:** `{cloudId}` and `{blogpostId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Blogpost Labels

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/blogposts/{blogpostId}/labels'
```

**Note:** `{cloudId}` and `{blogpostId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Blogpost Versions

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/blogposts/{blogpostId}/versions'
```

**Note:** `{cloudId}` and `{blogpostId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Blogpost Comments

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/blogposts/{blogpostId}/footer-comments'
```

**Note:** `{cloudId}` and `{blogpostId}` are placeholders. Replace each of them with real values before sending the request.

### Comments API

#### List Footer Comments

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/footer-comments'

maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/footer-comments?body-format=storage'
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Comment

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/footer-comments/{commentId}'
```

**Note:** `{cloudId}` and `{commentId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Footer Comment

```bash
maton api -X POST '/confluence/ex/confluence/{cloudId}/wiki/api/v2/footer-comments' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "pageId": "98391",
  "body": {
    "representation": "storage",
    "value": "<p>Comment text</p>"
  }
}
JSON
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

For blogpost comments:
```json
{
  "blogpostId": "458753",
  "body": {
    "representation": "storage",
    "value": "<p>Comment on blogpost</p>"
  }
}
```

#### Update Comment

```bash
maton api -X PUT '/confluence/ex/confluence/{cloudId}/wiki/api/v2/footer-comments/{commentId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "version": {"number": 2},
  "body": {
    "representation": "storage",
    "value": "<p>Updated comment</p>"
  }
}
JSON
```

**Note:** `{cloudId}` and `{commentId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Comment

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/footer-comments/{commentId}' -X DELETE
```

**Note:** `{cloudId}` and `{commentId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Comment Replies

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/footer-comments/{commentId}/children'
```

**Note:** `{cloudId}` and `{commentId}` are placeholders. Replace each of them with real values before sending the request.

#### List Inline Comments

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/inline-comments'
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

### Attachments API

#### List Attachments

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/attachments'

maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/attachments?limit=25'
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Attachment

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/attachments/{attachmentId}'
```

**Note:** `{cloudId}` and `{attachmentId}` are placeholders. Replace each of them with real values before sending the request.

### Tasks API

#### List Tasks

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/tasks'
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Task

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/tasks/{taskId}'
```

**Note:** `{cloudId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

### Labels API

#### List Labels

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/labels'

maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/labels?prefix=global'
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

### Custom Content API

#### List Custom Content

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/custom-content'

maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/custom-content?type={customContentType}'
```

**Note:** `{cloudId}` and `{customContentType}` are placeholders. Replace each of them with real values before sending the request.

### User API

The current user endpoint uses the V1 REST API:

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/rest/api/user/current'
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "type": "known",
  "accountId": "557058:...",
  "accountType": "atlassian",
  "email": "user@example.com",
  "publicName": "User Name",
  "displayName": "User Name"
}
```

### Pagination

The V2 API uses cursor-based pagination. Responses include a `_links.next` URL when more results are available.

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages?limit=25'
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "results": [...],
  "_links": {
    "next": "/wiki/api/v2/pages?cursor=eyJpZCI6Ijk4MzkyIn0"
  }
}
```

To get the next page, extract the cursor and pass it:

```bash
maton api '/confluence/ex/confluence/{cloudId}/wiki/api/v2/pages?limit=25&cursor=eyJpZCI6Ijk4MzkyIn0'
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

### Notes

- **Cloud ID Required**: You must obtain your Cloud ID via `/oauth/token/accessible-resources` before making API calls
- **V2 API Recommended**: Use the V2 API (`/wiki/api/v2/`) for most operations. The V1 API (`/wiki/rest/api/`) is limited
- **Body Formats**: Use `storage` format for creating/updating content. Use `view` for rendered HTML
- **Version Numbers**: When updating pages or blogposts, you must increment the version number
- **Storage Format**: Content uses Confluence storage format (XML-like). Example: `<p>Paragraph</p>`, `<h1>Heading</h1>`
- **Delete Returns 204**: DELETE operations return 204 No Content with no response body
- **IDs are Strings**: Page, space, and other IDs should be passed as strings

### Resources

- [Confluence REST API V2 Introduction](https://developer.atlassian.com/cloud/confluence/rest/v2/intro/)
- [Page Operations](https://developer.atlassian.com/cloud/confluence/rest/v2/api-group-page/)
- [Space Operations](https://developer.atlassian.com/cloud/confluence/rest/v2/api-group-space/)
- [Blogpost Operations](https://developer.atlassian.com/cloud/confluence/rest/v2/api-group-blog-post/)
- [Comment Operations](https://developer.atlassian.com/cloud/confluence/rest/v2/api-group-comment/)
- [Storage Format](https://confluence.atlassian.com/doc/confluence-storage-format-790796544.html)
- [Maton CLI Manual](https://cli.maton.ai/manual)
