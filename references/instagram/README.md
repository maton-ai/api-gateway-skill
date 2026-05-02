# Instagram Routing Reference

**App name:** `instagram`
**Base URL proxied:** `graph.instagram.com`

## API Path Pattern

```
/instagram/v25.0/{resource}
```

## Common Endpoints

### Get User Profile
```bash
GET /instagram/v25.0/me?fields=id,username,name,biography,followers_count,follows_count,media_count,profile_picture_url,website
```

### Get User Profile by ID
```bash
GET /instagram/v25.0/{ig-user-id}?fields=id,username,name,biography,followers_count,follows_count,media_count,profile_picture_url
```

### List User Media
```bash
GET /instagram/v25.0/me/media?fields=id,caption,media_type,media_url,permalink,timestamp,like_count,comments_count
```

### Get Single Media
```bash
GET /instagram/v25.0/{ig-media-id}?fields=id,caption,media_type,media_url,permalink,timestamp,like_count,comments_count,media_product_type
```

### Get User Stories
```bash
GET /instagram/v25.0/me/stories?fields=id,media_type,media_url,timestamp,permalink
```

### Get Live Media
```bash
GET /instagram/v25.0/me/live_media?fields=id,media_type,timestamp,permalink
```

### Get Carousel Children
```bash
GET /instagram/v25.0/{ig-media-id}/children?fields=id,media_type,media_url,timestamp
```

### Create Media Container (Image)
```bash
POST /instagram/v25.0/me/media
Content-Type: application/json

{
  "image_url": "https://example.com/photo.jpg",
  "caption": "My post"
}
```

### Create Media Container (Reel)
```bash
POST /instagram/v25.0/me/media
Content-Type: application/json

{
  "video_url": "https://example.com/video.mp4",
  "media_type": "REELS",
  "caption": "My reel"
}
```

### Create Media Container (Story)
```bash
POST /instagram/v25.0/me/media
Content-Type: application/json

{
  "image_url": "https://example.com/photo.jpg",
  "media_type": "STORIES"
}
```

### Create Carousel Item Container
```bash
POST /instagram/v25.0/me/media
Content-Type: application/json

{
  "image_url": "https://example.com/photo.jpg",
  "is_carousel_item": true
}
```

### Create Media Container (Carousel)
```bash
POST /instagram/v25.0/me/media
Content-Type: application/json

{
  "media_type": "CAROUSEL",
  "children": ["{container-id-1}", "{container-id-2}"],
  "caption": "My carousel"
}
```

### Check Container Status
```bash
GET /instagram/v25.0/{ig-container-id}?fields=status_code
```

### Publish Media
```bash
POST /instagram/v25.0/me/media_publish
Content-Type: application/json

{
  "creation_id": "{ig-container-id}"
}
```

### Check Publishing Rate Limit
```bash
GET /instagram/v25.0/me/content_publishing_limit?fields=quota_usage,config
```

### List Comments on Media
```bash
GET /instagram/v25.0/{ig-media-id}/comments?fields=id,text,timestamp,username,like_count,from
```

### Get Comment
```bash
GET /instagram/v25.0/{ig-comment-id}?fields=id,text,timestamp,username,like_count,from,hidden,parent_id
```

### Get Comment Replies
```bash
GET /instagram/v25.0/{ig-comment-id}/replies?fields=id,text,timestamp,username,like_count
```

### Post Comment
```bash
POST /instagram/v25.0/{ig-media-id}/comments
Content-Type: application/json

{
  "message": "Great post!"
}
```

### Reply to Comment
```bash
POST /instagram/v25.0/{ig-comment-id}/replies
Content-Type: application/json

{
  "message": "Thanks!"
}
```

### Hide/Unhide Comment
```bash
POST /instagram/v25.0/{ig-comment-id}
Content-Type: application/json

{
  "hide": true
}
```

### Delete Comment
```bash
DELETE /instagram/v25.0/{ig-comment-id}
```

### Toggle Comments on Media
```bash
POST /instagram/v25.0/{ig-media-id}
Content-Type: application/json

{
  "comment_enabled": false
}
```

### Account Insights
```bash
GET /instagram/v25.0/me/insights?metric=reach,accounts_engaged,total_interactions&period=day&metric_type=total_value
```

### Media Insights
```bash
GET /instagram/v25.0/{ig-media-id}/insights?metric=reach,likes,comments,shares,saved,total_interactions
```

### Demographic Insights
```bash
GET /instagram/v25.0/me/insights?metric=follower_demographics&period=lifetime&timeframe=last_30_days&breakdown=country&metric_type=total_value
```

### Get Tagged Media
```bash
GET /instagram/v25.0/me/tags?fields=id,caption,media_type,media_url,permalink,timestamp,username
```

### Reply to Caption Mention
```bash
POST /instagram/v25.0/me/mentions
Content-Type: application/json

{
  "media_id": "{ig-media-id}",
  "message": "Thanks for the mention!"
}
```

### Reply to Comment Mention
```bash
POST /instagram/v25.0/me/mentions
Content-Type: application/json

{
  "media_id": "{ig-media-id}",
  "comment_id": "{ig-comment-id}",
  "message": "Thanks for mentioning me!"
}
```

## Messaging

### List Conversations
```bash
GET /instagram/v25.0/me/conversations?fields=id,participants,messages{id,message,from,to,created_time}
```

### Send Text Message
```bash
POST /instagram/v25.0/me/messages
Content-Type: application/json

{
  "recipient": {
    "id": "{instagram-scoped-user-id}"
  },
  "message": {
    "text": "Hello! How can I help you?"
  }
}
```

### Send Image Message
```bash
POST /instagram/v25.0/me/messages
Content-Type: application/json

{
  "recipient": {
    "id": "{instagram-scoped-user-id}"
  },
  "message": {
    "attachment": {
      "type": "image",
      "payload": {
        "url": "https://example.com/image.jpg"
      }
    }
  }
}
```

Supported attachment types: `image`, `video`, `audio`, `file`

### Send Heart Sticker
```bash
POST /instagram/v25.0/me/messages
Content-Type: application/json

{
  "recipient": {
    "id": "{instagram-scoped-user-id}"
  },
  "message": {
    "attachment": {
      "type": "like_heart"
    }
  }
}
```

### Send Published Post
```bash
POST /instagram/v25.0/me/messages
Content-Type: application/json

{
  "recipient": {
    "id": "{instagram-scoped-user-id}"
  },
  "message": {
    "attachment": {
      "type": "MEDIA_SHARE",
      "payload": {
        "id": "{ig-media-id}"
      }
    }
  }
}
```

### React to Message
```bash
POST /instagram/v25.0/me/messages
Content-Type: application/json

{
  "recipient": {
    "id": "{instagram-scoped-user-id}"
  },
  "sender_action": "react",
  "payload": {
    "message_id": "{message-id}",
    "reaction": "love"
  }
}
```

### Unreact to Message
```bash
POST /instagram/v25.0/me/messages
Content-Type: application/json

{
  "recipient": {
    "id": "{instagram-scoped-user-id}"
  },
  "sender_action": "unreact",
  "payload": {
    "message_id": "{message-id}"
  }
}
```

### Private Reply to Comment
```bash
POST /instagram/v25.0/{ig-user-id}/messages
Content-Type: application/json

{
  "recipient": {
    "comment_id": "{ig-comment-id}"
  },
  "message": {
    "text": "Thanks for your comment! Let me help you privately."
  }
}
```

## Notes

- All endpoints require API version prefix (e.g., `v25.0`)
- IDs are app-scoped (different per app)
- Content publishing is two-step: create container, then publish
- Video containers must reach `FINISHED` status before publishing
- Cursor-based pagination with `before`/`after` cursors
- Publishing rate limit: 100 posts per 24h
- Container creation limit: 400 per 24h
- Carousel posts support up to 10 items
- Story metrics expire after 24 hours
- Insights data may be delayed up to 48 hours
- 24h messaging response window — you can only message users who have messaged you first within the last 24 hours

## Resources

- [Instagram Platform Overview](https://developers.facebook.com/docs/instagram-platform)
- [Instagram API with Instagram Login](https://developers.facebook.com/docs/instagram-platform/instagram-api-with-instagram-login)
- [IG User Reference](https://developers.facebook.com/docs/instagram-platform/reference/ig-user)
- [IG Media Reference](https://developers.facebook.com/docs/instagram-platform/reference/ig-media)
- [IG Comment Reference](https://developers.facebook.com/docs/instagram-platform/reference/ig-comment)
- [IG Container Reference](https://developers.facebook.com/docs/instagram-platform/reference/ig-container)
- [Content Publishing Guide](https://developers.facebook.com/docs/instagram-platform/instagram-api-with-instagram-login/content-publishing)
- [Insights Guide](https://developers.facebook.com/docs/instagram-platform/instagram-api-with-instagram-login/insights)
