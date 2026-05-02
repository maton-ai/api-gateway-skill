# Threads Routing Reference

**App name:** `threads`
**Base URL proxied:** `graph.threads.net`

## API Path Pattern

```
/threads/v1.0/{resource}
```

## Common Endpoints

### Profile

#### Get Authenticated User Profile
```bash
GET /threads/v1.0/me?fields=id,username,name,threads_profile_picture_url,threads_biography,is_verified
```

#### Get User Profile by ID
```bash
GET /threads/v1.0/{threads-user-id}?fields=id,username,name,threads_profile_picture_url,threads_biography,is_verified
```

#### Look Up Public Profile
```bash
GET /threads/v1.0/profile_lookup?username={username}&fields=username,name,profile_picture_url,biography,follower_count,is_verified
```

### Media Retrieval

#### List User's Threads
```bash
GET /threads/v1.0/me/threads?fields=id,media_type,text,permalink,timestamp,username&limit=25
```

#### Get Single Thread
```bash
GET /threads/v1.0/{threads-media-id}?fields=id,media_type,text,permalink,timestamp,username,has_replies
```

#### Get Public Profile Posts
```bash
GET /threads/v1.0/profile_posts?username={username}&fields=id,text,media_type,permalink,timestamp
```

### Publishing (Two-Step)

#### Step 1: Create Media Container
```bash
POST /threads/v1.0/me/threads
Content-Type: application/json

{
  "media_type": "TEXT",
  "text": "Hello from Threads!"
}
```

#### Step 2: Publish Container
```bash
POST /threads/v1.0/me/threads_publish
Content-Type: application/json

{
  "creation_id": "<CONTAINER_ID>"
}
```

#### Check Container Status
```bash
GET /threads/v1.0/{container-id}?fields=status,error_message
```

### Replies

#### Read Replies
```bash
GET /threads/v1.0/{threads-media-id}/replies?fields=id,text,username,timestamp,has_replies&reverse=true
```

#### Read Conversation
```bash
GET /threads/v1.0/{threads-media-id}/conversation?fields=id,text,username,timestamp,has_replies
```

#### Hide/Unhide Reply
```bash
POST /threads/v1.0/{threads-reply-id}/manage_reply
Content-Type: application/json

{
  "hide": true
}
```

#### Get Pending Replies
```bash
GET /threads/v1.0/{threads-media-id}/pending_replies?fields=id,text,timestamp,reply_approval_status&approval_status=pending
```

#### Approve/Ignore Pending Reply
```bash
POST /threads/v1.0/{threads-reply-id}/manage_pending_reply
Content-Type: application/json

{
  "approve": true
}
```

### Mentions

#### Get Mentions
```bash
GET /threads/v1.0/me/mentions?fields=id,text,username,timestamp,permalink
```

### Keyword Search

```bash
GET /threads/v1.0/keyword_search?q={query}&search_type=TOP&limit=25&fields=id,text,username,timestamp,permalink
```

### Delete

```bash
DELETE /threads/v1.0/{threads-media-id}
```

### Location Search & Tagging

#### Search by Query
```bash
GET /threads/v1.0/location_search?q={search-term}&fields=id,name,address,city,country
```

#### Search by Coordinates
```bash
GET /threads/v1.0/location_search?latitude={lat}&longitude={lng}&fields=id,name,address,city,country
```

#### Get Location
```bash
GET /threads/v1.0/{location-id}?fields=id,name,address,city,country,latitude,longitude,postal_code
```

### Insights

#### Media Insights
```bash
GET /threads/v1.0/{threads-media-id}/insights?metric=views,likes,replies,reposts,quotes
```

#### User Insights
```bash
GET /threads/v1.0/me/threads_insights?metric=views,likes,replies,reposts,quotes&since={unix-timestamp}&until={unix-timestamp}
```

#### Follower Demographics
```bash
GET /threads/v1.0/me/threads_insights?metric=follower_demographics&breakdown=country
```

### Publishing Limits

```bash
GET /threads/v1.0/me/threads_publishing_limit?fields=quota_usage,config
```

## Pagination

Cursor-based pagination:

```bash
GET /threads/v1.0/me/threads?fields=id,text&limit=25&after={AFTER_CURSOR}
```

Response includes:
```json
{
  "data": [...],
  "paging": {
    "cursors": {
      "before": "...",
      "after": "..."
    },
    "next": "https://graph.threads.net/v1.0/..."
  }
}
```

## Notes

- Publishing is a two-step process: create container, then publish
- The `me` alias can be used for the authenticated user's ID
- Text posts have a 500 character maximum
- Carousels support 2-20 items
- Video containers may take time to process; poll status before publishing
- Media containers expire after 24 hours if not published
- Profile lookup only works for public profiles with 100+ followers

## Resources

- [Threads API Overview](https://developers.facebook.com/docs/threads/overview)
- [Threads API Reference](https://developers.facebook.com/docs/threads/reference)
