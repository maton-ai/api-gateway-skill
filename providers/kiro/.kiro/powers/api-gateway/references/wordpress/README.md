# WordPress.com Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `wordpress`
**Base URL proxied:** `public-api.wordpress.com`

## API Path Pattern

```
/wordpress/rest/v1.1/{endpoint}
```

**Important:** WordPress.com uses REST API v1.1. Site-specific endpoints use `/sites/{site_id_or_domain}/{resource}`.

## Site Identifiers

Sites can be identified by:
- Numeric site ID (e.g., `252505333`)
- Domain name (e.g., `myblog.wordpress.com`)

## Common Endpoints

### Sites

#### Get Site Information
```bash
maton api '/wordpress/rest/v1.1/sites/{site}'
```

### Posts

#### List Posts
```bash
maton api '/wordpress/rest/v1.1/sites/{site}/posts'
```

Query parameters: `number`, `offset`, `page_handle`, `status`, `search`, `category`, `tag`, `author`

#### Get Post
```bash
maton api '/wordpress/rest/v1.1/sites/{site}/posts/{post_id}'
```

#### Create Post
```bash
maton api -X POST '/wordpress/rest/v1.1/sites/{site}/posts/new' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "Post Title",
  "content": "<p>Post content...</p>",
  "status": "draft",
  "categories": "news",
  "tags": "featured"
}
EOF
```

#### Update Post
```bash
maton api -X POST '/wordpress/rest/v1.1/sites/{site}/posts/{post_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "Updated Title",
  "content": "<p>Updated content...</p>"
}
EOF
```

#### Delete Post
```bash
maton api -X POST '/wordpress/rest/v1.1/sites/{site}/posts/{post_id}/delete'
```

### Pages

#### List Pages
```bash
maton api '/wordpress/rest/v1.1/sites/{site}/posts?type=page'
```

#### Create Page
```bash
maton api -X POST '/wordpress/rest/v1.1/sites/{site}/posts/new?type=page' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "Page Title",
  "content": "<p>Page content...</p>",
  "status": "publish"
}
EOF
```

### Post Likes

#### Get Post Likes
```bash
maton api '/wordpress/rest/v1.1/sites/{site}/posts/{post_id}/likes'
```

#### Like Post
```bash
maton api -X POST '/wordpress/rest/v1.1/sites/{site}/posts/{post_id}/likes/new'
```

### Users

#### List Site Users
```bash
maton api '/wordpress/rest/v1.1/sites/{site}/users'
```

### User Settings

#### Get My Settings
```bash
maton api '/wordpress/rest/v1.1/me/settings'
```

#### Update My Settings
```bash
maton api -X POST '/wordpress/rest/v1.1/me/settings/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "enable_translator": false
}
EOF
```

### Post Types

#### List Post Types
```bash
maton api '/wordpress/rest/v1.1/sites/{site}/post-types'
```

### Post Counts

#### Get Post Counts
```bash
maton api '/wordpress/rest/v1.1/sites/{site}/post-counts/{post_type}'
```

## Pagination

WordPress.com uses cursor-based pagination with `page_handle`:

```bash
maton api '/wordpress/rest/v1.1/sites/{site}/posts?number=20'
# Response includes "meta": {"next_page": "..."}

maton api '/wordpress/rest/v1.1/sites/{site}/posts?number=20&page_handle={next_page}'
```

Alternatively, use `offset` for simple pagination.

## Notes

- API version is v1.1 (not v2)
- POST is used for updates (not PUT/PATCH)
- POST to `/delete` endpoint is used for deletes (not HTTP DELETE)
- Categories and tags are created automatically when referenced in posts
- Content is HTML-formatted
- Date/time values are in ISO 8601 format

## Resources

- [WordPress.com REST API Overview](https://developer.wordpress.com/docs/api/)
- [Getting Started Guide](https://developer.wordpress.com/docs/api/getting-started/)
- [API Reference](https://developer.wordpress.com/docs/api/rest-api-reference/)
