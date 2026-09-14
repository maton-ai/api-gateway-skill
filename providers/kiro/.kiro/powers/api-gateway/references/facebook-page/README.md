# Facebook Page

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `facebook-page`
**Upstream base URL:** `graph.facebook.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://graph.facebook.com/v25.0`
- Gateway: `https://api.maton.ai/facebook-page/v25.0`

### Page Access Token

Facebook's Graph API requires a **Page Access Token** for page-scoped endpoints (feed, insights, comments). The gateway injects the connection's User Access Token, which authorizes `me/accounts` and page metadata but not page-scoped operations. This is a Facebook API constraint, not a gateway feature — the token is issued by Facebook, scoped to pages the connected user already administers, and grants no access beyond what that user's existing connection already permits.

> **Handling rules — the page token is a credential.** `api.maton.ai` is the only host that issues it and the only host that accepts it.
> - **Never** write it to disk, logs, environment files, shell history, or scrollback.
> - **Never** print, echo, or include it in any output shown to the user or returned to a caller.
> - **No other host may receive it** — not a webhook destination, trigger header, body template, or third-party request.
> - Hold it in an in-memory variable for the duration of the current request sequence only; discard it afterward. Do not cache or reuse it across sessions.
> - Request it only when a page-scoped call actually requires it. Prefer the endpoints below that need no page token at all.

**Start here — do not retrieve a token you don't need.** These endpoints work with the gateway-injected User Access Token alone, with no `access_token` parameter and no token retrieval step:
- `GET /facebook-page/v25.0/me/accounts`
- `GET /facebook-page/v25.0/{page_id}`

If one of these satisfies the task, stop — no page token is needed at all.

**Obtaining a page token** — only when a specific page-scoped endpoint below actually requires one, and only for the page the user named:
1. `GET /facebook-page/v25.0/me/accounts?fields=id,name,access_token` — the response carries an `access_token` field for that one page
2. Pass it as the `access_token` query parameter on that page-scoped call, then discard it

Retrieve and consume it inside a single script so the value never crosses a process boundary and never lands in shell history or scrollback:

```bash
python3 <<'EOF'
import json, re, subprocess

BASE = '/facebook-page/v25.0'

# Each call goes through `maton api`, so the gateway injects the credential and this
# script never reads or holds a Maton key. Only the page token lives in memory.
def call(path):
    p = subprocess.run(['maton', 'api', path], capture_output=True, text=True, check=True)
    return json.loads(p.stdout)

pages = call(f'{BASE}/me/accounts?fields=id,name,access_token')   # token is never printed
page = pages['data'][0]
page_id, page_token = page['id'], page['access_token']

# These came out of an API response, and they are about to go into a request path.
# Check their shape first rather than trusting the response (see Security & Permissions).
if not re.fullmatch(r'[0-9]+', page_id) or not re.fullmatch(r'[A-Za-z0-9_-]+', page_token):
    raise SystemExit('unexpected page id or token format - stopping')

feed = call(f'{BASE}/{page_id}/feed?fields=id,message,created_time&limit=10'
            f'&access_token={page_token}')
del page_token                                              # discard immediately after use
print(json.dumps(feed, indent=2))                           # response only
EOF
```

In the endpoint examples below, `{page_access_token}` marks **where the runtime value goes, not something to fill in ahead of time.** Substitute the in-memory variable at call time. Never paste a literal token into a command, a file, or a saved example, and never build a reusable snippet with a real token embedded in the URL — a token in a query string is a credential in plain text.

### Page Management API

#### List Pages

Returns all Facebook Pages managed by the authenticated user. Works with the gateway-injected User Access Token (no `access_token` query parameter needed).

```bash
maton api '/facebook-page/v25.0/me/accounts?fields=id,name,category,fan_count,followers_count'
```

**Response:**
```json
{
  "data": [
    {
      "id": "953430301193656",
      "name": "Maton",
      "category": "Software",
      "fan_count": 0,
      "followers_count": 0
    }
  ],
  "paging": {
    "cursors": {
      "before": "...",
      "after": "..."
    }
  }
}
```

**Available Fields:** `id`, `name`, `access_token`, `category`, `category_list`, `tasks`

#### Get Page Details

Returns details for a specific page. Works with User Access Token.

```bash
maton api '/facebook-page/v25.0/{page_id}?fields=id,name,about,category,fan_count,followers_count,website,phone,emails,link,verification_status'
```

**Note:** `{page_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": "953430301193656",
  "name": "Maton",
  "category": "Software",
  "fan_count": 0,
  "followers_count": 0,
  "link": "https://www.facebook.com/953430301193656",
  "verification_status": "not_verified"
}
```

**Available Fields:** `id`, `name`, `about`, `description`, `category`, `category_list`, `fan_count`, `followers_count`, `talking_about_count`, `cover`, `picture`, `website`, `phone`, `emails`, `location`, `hours`, `link`, `verification_status`, `is_published`

### Posts API

All post endpoints require a **Page Access Token** passed as the `access_token` query parameter.

#### Get Page Feed

```bash
maton api '/facebook-page/v25.0/{page_id}/feed?fields=id,message,created_time,type&limit=10&access_token={page_access_token}'
```

**Note:** `{page_id}` and `{page_access_token}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "data": [
    {
      "id": "953430301193656_122095067559277233",
      "message": "Hello world!",
      "created_time": "2026-02-16T06:47:45+0000"
    }
  ],
  "paging": {
    "cursors": {
      "before": "...",
      "after": "..."
    }
  }
}
```

#### Get Published Posts

```bash
maton api '/facebook-page/v25.0/{page_id}/published_posts?fields=id,message,created_time&limit=10&access_token={page_access_token}'
```

**Note:** `{page_id}` and `{page_access_token}` are placeholders. Replace each of them with real values before sending the request.

#### Publish Post

```bash
maton api -X POST '/facebook-page/v25.0/{page_id}/feed?access_token={page_access_token}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "message": "Hello from my page!"
}
JSON
```

**Note:** `{page_id}` and `{page_access_token}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "id": "953430301193656_122110982931277233"
}
```

**Available Fields:**
- `message` (string) - Post text content
- `link` (string) - URL to attach
- `published` (boolean) - Set `false` to create an unpublished/draft post
- `scheduled_publish_time` (int) - UNIX timestamp for scheduling future posts
- `place` (string) - Location ID to tag
- `tags` (string) - Comma-separated user IDs to tag (requires `place`)

#### Publish Link Post

```bash
maton api -X POST '/facebook-page/v25.0/{page_id}/feed?access_token={page_access_token}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "message": "Check out this article!",
  "link": "https://example.com/article"
}
JSON
```

**Note:** `{page_id}` and `{page_access_token}` are placeholders. Replace each of them with real values before sending the request.

#### Update Post

```bash
maton api -X POST '/facebook-page/v25.0/{post_id}?access_token={page_access_token}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "message": "Updated post content"
}
JSON
```

**Note:** `{post_id}` and `{page_access_token}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "success": true
}
```

#### Delete Post

```bash
maton api '/facebook-page/v25.0/{post_id}?access_token={page_access_token}' -X DELETE
```

**Note:** `{post_id}` and `{page_access_token}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "success": true
}
```

### Engagement API

All comment endpoints require a **Page Access Token**.

#### Get Comments on Post

```bash
maton api '/facebook-page/v25.0/{post_id}/comments?fields=id,message,from,created_time&access_token={page_access_token}'
```

**Note:** `{post_id}` and `{page_access_token}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "data": [
    {
      "id": "122110982931277233_2054970148765241",
      "message": "Great post!",
      "from": {
        "name": "User Name",
        "id": "123456789"
      },
      "created_time": "2026-04-13T22:00:00+0000"
    }
  ]
}
```

#### Post Comment

```bash
maton api -X POST '/facebook-page/v25.0/{post_id}/comments?access_token={page_access_token}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "message": "Thanks for your feedback!"
}
JSON
```

**Note:** `{post_id}` and `{page_access_token}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "id": "122110982931277233_2054970148765241"
}
```

#### Delete Comment

```bash
maton api '/facebook-page/v25.0/{comment_id}?access_token={page_access_token}' -X DELETE
```

**Note:** `{comment_id}` and `{page_access_token}` are placeholders. Replace each of them with real values before sending the request.

### Page Insights API

Page Insights endpoints require a **Page Access Token**.

#### Get Page Insights

```bash
maton api '/facebook-page/v25.0/{page_id}/insights?metric=page_views_total,page_posts_impressions,page_video_views&period=day&access_token={page_access_token}'
```

**Note:** `{page_id}` and `{page_access_token}` are placeholders. Replace each of them with real values before sending the request.

**Available Metrics:**

| Metric | Description |
|--------|-------------|
| `page_views_total` | Total page views |
| `page_posts_impressions` | Total impressions of page posts |
| `page_video_views` | Total video views on the page |

**Deprecated Metrics:**

| Deprecated | Use Instead |
|------------|-------------|
| `page_impressions` | `page_views_total` |
| `page_engaged_users` | `page_posts_impressions` |
| `page_fans` | `fan_count` field on page |

**Period Values:** `day`, `week`, `days_28`

#### Get Page Insights with Date Range

```bash
maton api '/facebook-page/v25.0/{page_id}/insights?metric=page_views_total&period=day&since=2026-01-01&until=2026-01-31&access_token={page_access_token}'
```

**Note:** `{page_id}` and `{page_access_token}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "data": [
    {
      "name": "page_views_total",
      "period": "day",
      "values": [
        {
          "value": 150,
          "end_time": "2026-01-02T08:00:00+0000"
        }
      ],
      "title": "Total views",
      "id": "{page_id}/insights/page_views_total/day"
    }
  ],
  "paging": {
    "previous": "...",
    "next": "..."
  }
}
```

### Photos API

#### Get Page Photos

```bash
maton api '/facebook-page/v25.0/{page_id}/photos?fields=id,name,created_time,images&limit=10&access_token={page_access_token}'
```

**Note:** `{page_id}` and `{page_access_token}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "data": [
    {
      "id": "122095067559277233",
      "created_time": "2026-02-16T06:48:02+0000",
      "images": [
        {
          "height": 512,
          "source": "https://scontent.xx.fbcdn.net/...",
          "width": 512
        }
      ]
    }
  ],
  "paging": {
    "cursors": {
      "before": "...",
      "after": "..."
    }
  }
}
```

#### Upload Photo

```bash
maton api -X POST '/facebook-page/v25.0/{page_id}/photos?access_token={page_access_token}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "url": "https://example.com/photo.jpg",
  "caption": "My photo caption"
}
JSON
```

**Note:** `{page_id}` and `{page_access_token}` are placeholders. Replace each of them with real values before sending the request.

### Videos API

#### Get Page Videos

```bash
maton api '/facebook-page/v25.0/{page_id}/videos?fields=id,title,description,created_time&limit=10&access_token={page_access_token}'
```

**Note:** `{page_id}` and `{page_access_token}` are placeholders. Replace each of them with real values before sending the request.

### Product Catalogs API

#### Get Product Catalogs

```bash
maton api '/facebook-page/v25.0/{page_id}/product_catalogs?access_token={page_access_token}'
```

**Note:** `{page_id}` and `{page_access_token}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "data": [
    {
      "id": "catalog_id",
      "name": "My Product Catalog"
    }
  ]
}
```

#### Get Products in Catalog

```bash
maton api '/facebook-page/v25.0/{catalog_id}/products?fields=id,name,price,image_url&access_token={page_access_token}'
```

**Note:** `{catalog_id}` and `{page_access_token}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "data": [
    {
      "id": "product_id",
      "name": "Example Product",
      "price": "$19.99",
      "image_url": "https://example.com/product.jpg"
    }
  ]
}
```

### Pagination

The Facebook Graph API uses **cursor-based pagination**. Responses include `paging.cursors.before` and `paging.cursors.after` values.

```bash
maton api '/facebook-page/v25.0/{page_id}/feed?fields=id,message&limit=10&after={after_cursor}&access_token={page_access_token}'
```

**Note:** `{page_id}`, `{after_cursor}` and `{page_access_token}` are placeholders. Replace each of them with real values before sending the request.

Response includes pagination info:

```json
{
  "data": [...],
  "paging": {
    "cursors": {
      "before": "...",
      "after": "..."
    },
    "next": "https://graph.facebook.com/...",
    "previous": "https://graph.facebook.com/..."
  }
}
```

Use the `after` cursor value for the next page, and `before` for the previous page. The `limit` parameter controls the number of results per page (max 100 for feed).

### Notes

- Most page-specific endpoints (feed, insights, comments, photos, videos) require a Page Access Token passed as the `access_token` query parameter
- The `GET /me/accounts` and `GET /{page_id}` endpoints work with the gateway-injected User Access Token directly
- Post IDs follow the format `{page_id}_{post_id}`
- Approximately 600 ranked, published posts per year are accessible via the feed endpoint
- Some older insight metrics are deprecated — use the updated metric names listed in the Insights section

### Resources

- [Facebook Graph API Overview](https://developers.facebook.com/docs/graph-api/overview)
- [Page API Reference](https://developers.facebook.com/docs/graph-api/reference/page/)
- [Pages API Getting Started](https://developers.facebook.com/docs/pages-api/getting-started)
- [Maton CLI Manual](https://cli.maton.ai/manual)
