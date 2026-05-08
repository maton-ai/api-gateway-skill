# TikTok Routing Reference

> **Private Beta:** This integration is currently in private beta. Contact [support@maton.ai](mailto:support@maton.ai) to get added to the allowlist.

**App name:** `tiktok`
**Base URL proxied:** `open.tiktokapis.com`

## API Path Pattern

```
/tiktok/v2/{resource}
```

## Common Endpoints

### Get User Info
```bash
GET /tiktok/v2/user/info/?fields={field_list}
```

**Available Fields:**

| Field | Type | Scope Required |
|-------|------|----------------|
| `open_id` | string | user.info.basic |
| `union_id` | string | user.info.basic |
| `avatar_url` | string | user.info.basic |
| `avatar_url_100` | string | user.info.basic |
| `avatar_large_url` | string | user.info.basic |
| `display_name` | string | user.info.basic |
| `bio_description` | string | user.info.profile |
| `profile_deep_link` | string | user.info.profile |
| `is_verified` | boolean | user.info.profile |
| `username` | string | user.info.profile |
| `follower_count` | int64 | user.info.stats |
| `following_count` | int64 | user.info.stats |
| `likes_count` | int64 | user.info.stats |
| `video_count` | int64 | user.info.stats |

### List User Videos
```bash
POST /tiktok/v2/video/list/?fields={field_list}
Content-Type: application/json

{
  "max_count": 10,
  "cursor": 0
}
```

**Query Parameters:**
- `fields` (required): Comma-separated list of video fields

**Available Video Fields:**
- `id` - Video identifier
- `title` - Video title/caption
- `cover_image_url` - Thumbnail image URL
- `create_time` - Video creation timestamp
- `share_url` - URL to share the video
- `video_description` - Video description
- `duration` - Video duration in seconds
- `like_count` - Number of likes
- `comment_count` - Number of comments
- `share_count` - Number of shares
- `view_count` - Number of views

**Body Parameters:**
- `max_count` (optional): Maximum videos per page (default: 10, max: 20)
- `cursor` (optional): Pagination cursor (UTC Unix timestamp in milliseconds)

### Query Creator Info
```bash
POST /tiktok/v2/post/publish/creator_info/query/
Content-Type: application/json

{}
```

### Direct Post Video
```bash
POST /tiktok/v2/post/publish/video/init/
Content-Type: application/json

{
  "post_info": {
    "title": "My video caption #hashtag",
    "privacy_level": "PUBLIC_TO_EVERYONE",
    "disable_duet": false,
    "disable_stitch": false,
    "disable_comment": false,
    "video_cover_timestamp_ms": 1000
  },
  "source_info": {
    "source": "PULL_FROM_URL",
    "video_url": "https://your-verified-domain.com/video.mp4"
  }
}
```

**Post Info Parameters:**
- `title` (optional): Video caption, max 2200 characters
- `privacy_level` (required): One of `PUBLIC_TO_EVERYONE`, `MUTUAL_FOLLOW_FRIENDS`, `FOLLOWER_OF_CREATOR`, `SELF_ONLY`
- `disable_duet` (optional): Prevent duets (default: false)
- `disable_stitch` (optional): Prevent stitches (default: false)
- `disable_comment` (optional): Disable comments (default: false)
- `video_cover_timestamp_ms` (optional): Frame position for cover image in milliseconds
- `brand_content_toggle` (optional): Mark as paid partnership
- `brand_organic_toggle` (optional): Mark as creator's own business
- `is_aigc` (optional): Mark as AI-generated content

**Source Info Parameters (PULL_FROM_URL):**
- `source`: Set to `PULL_FROM_URL`
- `video_url`: Publicly accessible video URL from a verified domain

**Source Info Parameters (FILE_UPLOAD):**
- `source`: Set to `FILE_UPLOAD`
- `video_size`: File size in bytes
- `chunk_size`: Chunk size in bytes
- `total_chunk_count`: Number of chunks

### Share as Draft
```bash
POST /tiktok/v2/post/publish/inbox/video/init/
Content-Type: application/json

{
  "source_info": {
    "source": "PULL_FROM_URL",
    "video_url": "https://your-verified-domain.com/video.mp4"
  }
}
```

**Source Info Parameters:**
- `source`: `PULL_FROM_URL` or `FILE_UPLOAD`
- `video_url`: Publicly accessible video URL (for PULL_FROM_URL)
- `video_size`, `chunk_size`, `total_chunk_count`: For FILE_UPLOAD

### Get Publish Status
```bash
POST /tiktok/v2/post/publish/status/fetch/
Content-Type: application/json

{
  "publish_id": "v_pub_url~v2.123456789"
}
```

**Status Values:**
- `PROCESSING_UPLOAD` - Video is being uploaded
- `PROCESSING_DOWNLOAD` - Video is being downloaded from URL
- `SEND_TO_USER_INBOX` - Video sent to user's inbox (draft)
- `PUBLISH_COMPLETE` - Video published successfully
- `FAILED` - Publishing failed

## Pagination

The video list endpoint uses cursor-based pagination:
- `max_count` — videos per page (max 20)
- `cursor` — UTC Unix timestamp in milliseconds; pass back the `cursor` from the previous response
- `has_more` — boolean in response indicating whether more videos exist

## OAuth Scopes

| Scope | Description |
|-------|-------------|
| `user.info.basic` | Read basic profile info (open_id, avatar, display_name) |
| `user.info.profile` | Read profile details (bio, username, verification status) |
| `user.info.stats` | Read statistics (follower count, video count, likes) |
| `video.list` | Read user's public videos |
| `video.publish` | Directly post content to user's TikTok profile |
| `video.upload` | Share content as draft for user to edit and post |

## Notes

- Video uploads require domain ownership verification for `PULL_FROM_URL` source
- Unaudited apps can only post to private/self-only visibility
- Maximum video duration depends on the creator's account (check via creator_info/query)
- Supported video formats: MP4 with H.264 codec
- Maximum title length: 2200 UTF-16 characters
- Direct post rate limit: 6 requests per minute per user
- Common error codes: `access_token_invalid`, `invalid_params`, `spam_risk_too_many_posts`, `unaudited_client_can_only_post_to_private_accounts`, `url_ownership_unverified`, `scope_not_authorized`
- IMPORTANT: Use `curl -g` when URLs contain brackets to disable glob parsing

## Resources

- [TikTok API Overview](https://developers.tiktok.com/doc/getting-started-api-overview)
- [Content Posting API](https://developers.tiktok.com/doc/content-posting-api-get-started)
- [Login Kit - User Info](https://developers.tiktok.com/doc/tiktok-api-v2-get-user-info)
- [Video List API](https://developers.tiktok.com/doc/tiktok-api-v2-video-list)
- [OAuth Token Management](https://developers.tiktok.com/doc/oauth-user-access-token-management)