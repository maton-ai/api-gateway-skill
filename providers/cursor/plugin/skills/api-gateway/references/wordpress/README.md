# WordPress.com

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `wordpress`
**Upstream base URL:** `public-api.wordpress.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://public-api.wordpress.com/rest/v1.1/me/settings`
- Gateway: `https://api.maton.ai/wordpress/rest/v1.1/me/settings`

**Important:** WordPress.com uses REST API v1.1. Site-specific endpoints use `/sites/{site_id_or_domain}/{resource}`. Sites can be identified by:
- Numeric site ID (e.g., `252505333`)
- Domain name (e.g., `myblog.wordpress.com`)

### Sites API

#### Get Site Information

```bash
maton api '/wordpress/rest/v1.1/sites/{site_id_or_domain}'
```

**Note:** `{site_id_or_domain}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "ID": 252505333,
  "name": "My Blog",
  "description": "Just another WordPress.com site",
  "URL": "https://myblog.wordpress.com",
  "capabilities": {
    "edit_pages": true,
    "edit_posts": true,
    "edit_others_posts": true,
    "delete_posts": true
  }
}
```

The site identifier can be either:
- Numeric site ID (e.g., `252505333`)
- Domain name (e.g., `myblog.wordpress.com` or `en.blog.wordpress.com`)

#### Get Site Embeds

```bash
maton api '/wordpress/rest/v1.1/sites/{site}/embeds'
```

**Note:** `{site}` is a placeholder. Replace it with a real value before sending the request.

Returns available embed handlers for the site.

#### Get Available Shortcodes

```bash
maton api '/wordpress/rest/v1.1/sites/{site}/shortcodes'
```

**Note:** `{site}` is a placeholder. Replace it with a real value before sending the request.

Returns shortcodes available on the site.

### Posts API

#### List Posts

```bash
maton api '/wordpress/rest/v1.1/sites/{site}/posts'
```

**Note:** `{site}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `number` - Number of posts to return (default: 20, max: 100)
- `offset` - Offset for pagination
- `page` - Page number
- `page_handle` - Cursor for pagination (from response `meta.next_page`)
- `order` - Sort order: `DESC` or `ASC`
- `order_by` - Sort field: `date`, `modified`, `title`, `comment_count`, `ID`
- `status` - Post status: `publish`, `draft`, `pending`, `private`, `future`, `trash`, `any`
- `type` - Post type: `post`, `page`, `any`
- `search` - Search term
- `category` - Category slug
- `tag` - Tag slug
- `author` - Author ID
- `fields` - Comma-separated list of fields to return

**Response:**

```json
{
  "found": 150,
  "posts": [
    {
      "ID": 83587,
      "site_ID": 3584907,
      "author": {
        "ID": 257479511,
        "login": "username",
        "name": "John Doe"
      },
      "date": "2026-02-09T15:00:00+00:00",
      "modified": "2026-02-09T16:30:00+00:00",
      "title": "My Post Title",
      "excerpt": "<p>Post excerpt...</p>",
      "content": "<p>Full post content...</p>",
      "slug": "my-post-title",
      "status": "publish",
      "type": "post",
      "categories": {...},
      "tags": {...}
    }
  ],
  "meta": {
    "next_page": "value=2026-02-09T15%3A00%3A00%2B00%3A00&id=83587"
  }
}
```

#### Get Post

```bash
maton api '/wordpress/rest/v1.1/sites/{site}/posts/{post_id}'
```

**Note:** `{site}` and `{post_id}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "ID": 83587,
  "site_ID": 3584907,
  "author": {...},
  "date": "2026-02-09T15:00:00+00:00",
  "title": "My Post Title",
  "content": "<p>Full post content...</p>",
  "slug": "my-post-title",
  "status": "publish",
  "type": "post",
  "categories": {
    "news": {
      "ID": 123,
      "name": "News",
      "slug": "news"
    }
  },
  "tags": {
    "featured": {
      "ID": 456,
      "name": "Featured",
      "slug": "featured"
    }
  }
}
```

#### Create Post

```bash
maton api -X POST '/wordpress/rest/v1.1/sites/{site}/posts/new' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "New Post Title",
  "content": "<p>Post content here...</p>",
  "status": "draft",
  "categories": "news, updates",
  "tags": "featured, important"
}
JSON
```

**Note:** `{site}` is a placeholder. Replace it with a real value before sending the request.

**Request body:**
- `title` - Post title (required)
- `content` - Post content (HTML)
- `excerpt` - Post excerpt
- `status` - `publish`, `draft`, `pending`, `private`, `future`
- `date` - Post date (ISO 8601)
- `categories` - Comma-separated category names or slugs
- `tags` - Comma-separated tag names or slugs
- `format` - Post format: `standard`, `aside`, `chat`, `gallery`, `link`, `image`, `quote`, `status`, `video`, `audio`
- `slug` - URL slug
- `featured_image` - Featured image attachment ID
- `sticky` - Whether post is sticky (boolean)
- `password` - Password to protect post

**Response:**
```json
{
  "ID": 123,
  "site_ID": 252505333,
  "title": "New Post Title",
  "status": "draft",
  "date": "2026-02-10T09:50:35+00:00"
}
```

#### Update Post

```bash
maton api -X POST '/wordpress/rest/v1.1/sites/{site}/posts/{post_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Updated Title",
  "content": "<p>Updated content...</p>"
}
JSON
```

**Note:** `{site}` and `{post_id}` are placeholders. Replace each of them with real values before sending the request.

Uses the same parameters as Create Post.

#### Delete Post

```bash
maton api -X POST '/wordpress/rest/v1.1/sites/{site}/posts/{post_id}/delete'
```

**Note:** `{site}` and `{post_id}` are placeholders. Replace each of them with real values before sending the request.

Moves post to trash. Returns the deleted post with `status: "trash"`.

#### Check Reblog Status

```bash
maton api '/wordpress/rest/v1.1/sites/{site}/posts/{post_id}/reblogs/mine'
```

**Note:** `{site}` and `{post_id}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "can_reblog": true,
  "can_user_reblog": true,
  "is_reblogged": false
}
```

### Pages API

#### List Pages

```bash
maton api '/wordpress/rest/v1.1/sites/{site}/posts?type=page'
```

**Note:** `{site}` is a placeholder. Replace it with a real value before sending the request.

#### Create Page

```bash
maton api -X POST '/wordpress/rest/v1.1/sites/{site}/posts/new?type=page' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "About Us",
  "content": "<p>About page content...</p>",
  "status": "publish"
}
JSON
```

**Note:** `{site}` is a placeholder. Replace it with a real value before sending the request.

#### Get Page Dropdown List

```bash
maton api '/wordpress/rest/v1.1/sites/{site}/dropdown-pages/'
```

**Note:** `{site}` is a placeholder. Replace it with a real value before sending the request.

Returns a simplified list of pages for dropdowns/menus.

#### Get Page Templates

```bash
maton api '/wordpress/rest/v1.1/sites/{site}/page-templates'
```

**Note:** `{site}` is a placeholder. Replace it with a real value before sending the request.

Returns available page templates for the site's theme.

### Post Likes API

#### Get Post Likes

```bash
maton api '/wordpress/rest/v1.1/sites/{site}/posts/{post_id}/likes'
```

**Note:** `{site}` and `{post_id}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "found": 99,
  "i_like": false,
  "can_like": true,
  "site_ID": 3584907,
  "post_ID": 83587,
  "likes": [...]
}
```

#### Like Post

```bash
maton api -X POST '/wordpress/rest/v1.1/sites/{site}/posts/{post_id}/likes/new'
```

**Note:** `{site}` and `{post_id}` are placeholders. Replace each of them with real values before sending the request.

#### Unlike Post

```bash
maton api -X POST '/wordpress/rest/v1.1/sites/{site}/posts/{post_id}/likes/mine/delete'
```

**Note:** `{site}` and `{post_id}` are placeholders. Replace each of them with real values before sending the request.

### Post Types API

#### List Post Types

```bash
maton api '/wordpress/rest/v1.1/sites/{site}/post-types'
```

**Note:** `{site}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "found": 3,
  "post_types": {
    "post": {
      "name": "post",
      "label": "Posts",
      "labels": {...}
    },
    "page": {
      "name": "page",
      "label": "Pages",
      "labels": {...}
    }
  }
}
```

### Post Counts API

#### Get Post Counts

```bash
maton api '/wordpress/rest/v1.1/sites/{site}/post-counts/{post_type}'
```

**Note:** `{site}` and `{post_type}` are placeholders. Replace each of them with real values before sending the request.

**Example:** `/sites/{site}/post-counts/post` or `/sites/{site}/post-counts/page`

**Response:**
```json
{
  "counts": {
    "all": {"count": 150},
    "publish": {"count": 120},
    "draft": {"count": 25},
    "trash": {"count": 5}
  }
}
```

### Users API

#### List Site Users

```bash
maton api '/wordpress/rest/v1.1/sites/{site}/users'
```

**Note:** `{site}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "found": 3,
  "users": [
    {
      "ID": 277004271,
      "login": "username",
      "name": "John Doe",
      "email": "john@example.com",
      "roles": ["administrator"]
    }
  ]
}
```

### User Settings API

#### Get My Settings

```bash
maton api '/wordpress/rest/v1.1/me/settings'
```

**Response:**
```json
{
  "enable_translator": true,
  "surprise_me": false,
  "holidaysnow": false,
  "user_login": "username"
}
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

#### Get User's Liked Posts

```bash
maton api '/wordpress/rest/v1.1/me/likes'
```

**Response:**
```json
{
  "found": 10,
  "likes": [
    {
      "ID": 83587,
      "site_ID": 3584907,
      "title": "Liked Post Title"
    }
  ]
}
```

### Pagination

WordPress.com uses cursor-based pagination with `page_handle`:

```bash
maton api '/wordpress/rest/v1.1/sites/{site}/posts?number=20'
# Response includes "meta": {"next_page": "..."}

maton api '/wordpress/rest/v1.1/sites/{site}/posts?number=20&page_handle={next_page}'
```

**Note:** `{site}` and `{next_page}` are placeholders. Replace each of them with real values before sending the request.

Alternatively, use `offset` for simple pagination.

### Notes

- API version is v1.1 (not v2)
- POST is used for updates (not PUT/PATCH)
- POST to `/delete` endpoint is used for deletes (not HTTP DELETE)
- Categories and tags are created automatically when referenced in posts
- Content is HTML-formatted
- Date/time values are in ISO 8601 format

### Resources

- [WordPress.com REST API Overview](https://developer.wordpress.com/docs/api/)
- [WordPress.com Getting Started Guide](https://developer.wordpress.com/docs/api/getting-started/)
- [WordPress.com API Reference](https://developer.wordpress.com/docs/api/rest-api-reference/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
