# Vimeo Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `vimeo`
**Base URL proxied:** `api.vimeo.com`

## API Path Pattern

```
/vimeo/{resource}
```

## Common Endpoints

### User

```bash
maton api '/vimeo/me'
maton api '/vimeo/users/{user_id}'
maton api '/vimeo/me/feed'
```

### Videos

```bash
# List user videos
maton api '/vimeo/me/videos'

# Search videos
maton api '/vimeo/videos?query=nature'

# Get video
maton api '/vimeo/videos/{video_id}'

# Update video
maton api -X PATCH '/vimeo/videos/{video_id}'

# Delete video
maton api -X DELETE '/vimeo/videos/{video_id}'
```

### Folders (Projects)

```bash
maton api '/vimeo/me/folders'
maton api -X POST '/vimeo/me/folders'
maton api -X PATCH '/vimeo/me/projects/{project_id}'
maton api -X DELETE '/vimeo/me/projects/{project_id}'

# Folder videos
maton api '/vimeo/me/projects/{project_id}/videos'
maton api -X PUT '/vimeo/me/projects/{project_id}/videos/{video_id}'
maton api -X DELETE '/vimeo/me/projects/{project_id}/videos/{video_id}'
```

### Albums (Showcases)

```bash
maton api '/vimeo/me/albums'
maton api -X POST '/vimeo/me/albums'
maton api -X PATCH '/vimeo/me/albums/{album_id}'
maton api -X DELETE '/vimeo/me/albums/{album_id}'

# Album videos
maton api '/vimeo/me/albums/{album_id}/videos'
maton api -X PUT '/vimeo/me/albums/{album_id}/videos/{video_id}'
maton api -X DELETE '/vimeo/me/albums/{album_id}/videos/{video_id}'
```

### Comments

```bash
maton api '/vimeo/videos/{video_id}/comments'
maton api -X POST '/vimeo/videos/{video_id}/comments'
maton api -X DELETE '/vimeo/videos/{video_id}/comments/{comment_id}'
```

### Likes

```bash
maton api '/vimeo/me/likes'
maton api -X PUT '/vimeo/me/likes/{video_id}'
maton api -X DELETE '/vimeo/me/likes/{video_id}'
```

### Watch Later

```bash
maton api '/vimeo/me/watchlater'
maton api -X PUT '/vimeo/me/watchlater/{video_id}'
maton api -X DELETE '/vimeo/me/watchlater/{video_id}'
```

### Following

```bash
maton api '/vimeo/me/followers'
maton api '/vimeo/me/following'
maton api -X PUT '/vimeo/me/following/{user_id}'
maton api -X DELETE '/vimeo/me/following/{user_id}'
```

### Channels and Categories

```bash
maton api '/vimeo/channels'
maton api '/vimeo/channels/{channel_id}'
maton api '/vimeo/categories'
maton api '/vimeo/categories/{category}/videos'
```

## Notes

- Video and user IDs are numeric
- Folders are called "projects" in API paths
- Albums are "Showcases" in the Vimeo UI
- DELETE and PUT operations return 204 No Content
- Video uploads require TUS protocol
- Page-based pagination with `page` and `per_page` parameters

## Resources

- [Vimeo API Reference](https://developer.vimeo.com/api/reference)
- [Vimeo Developer Portal](https://developer.vimeo.com)
