# Vimeo

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `vimeo`
**Upstream base URL:** `api.vimeo.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.vimeo.com/me`
- Gateway: `https://api.maton.ai/vimeo/me`

### User API

#### Get Current User

```bash
maton api '/vimeo/me'
```

**Response:**
```json
{
  "uri": "/users/254399456",
  "name": "Chris",
  "link": "https://vimeo.com/user254399456",
  "account": "free",
  "created_time": "2026-02-09T07:00:20+00:00",
  "pictures": {...},
  "metadata": {
    "connections": {
      "videos": {"uri": "/users/254399456/videos", "total": 2},
      "albums": {"uri": "/users/254399456/albums", "total": 0},
      "folders": {"uri": "/users/254399456/folders", "total": 0},
      "likes": {"uri": "/users/254399456/likes", "total": 0},
      "followers": {"uri": "/users/254399456/followers", "total": 0},
      "following": {"uri": "/users/254399456/following", "total": 0}
    }
  }
}
```

#### Get User by ID

```bash
maton api '/vimeo/users/{user_id}'
```

**Note:** `{user_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get User Feed

```bash
maton api '/vimeo/me/feed'
```

### Video API

#### List User Videos

```bash
maton api '/vimeo/me/videos'
```

**Response:**
```json
{
  "total": 2,
  "page": 1,
  "per_page": 25,
  "paging": {
    "next": null,
    "previous": null,
    "first": "/me/videos?page=1",
    "last": "/me/videos?page=1"
  },
  "data": [
    {
      "uri": "/videos/1163160198",
      "name": "My Video",
      "description": "Video description",
      "link": "https://vimeo.com/1163160198",
      "duration": 20,
      "width": 1920,
      "height": 1080,
      "created_time": "2026-02-09T07:05:00+00:00"
    }
  ]
}
```

#### Get Video

```bash
maton api '/vimeo/videos/{video_id}'
```

**Note:** `{video_id}` is a placeholder. Replace it with a real value before sending the request.

#### Search Videos

```bash
maton api '/vimeo/videos?query=nature&per_page=10'
```

**Query parameters:**
- `query` - Search query
- `per_page` - Results per page (max 100)
- `page` - Page number
- `sort` - Sort order: `relevant`, `date`, `alphabetical`, `plays`, `likes`, `comments`, `duration`
- `direction` - Sort direction: `asc`, `desc`

#### Update Video

```bash
maton api -X PATCH '/vimeo/videos/{video_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Video Title",
  "description": "Updated description"
}
JSON
```

**Note:** `{video_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Video

```bash
maton api '/vimeo/videos/{video_id}' -X DELETE
```

**Note:** `{video_id}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content on success.

### Folder API

#### List Folders

```bash
maton api '/vimeo/me/folders'
```

**Response:**
```json
{
  "total": 1,
  "page": 1,
  "per_page": 25,
  "data": [
    {
      "uri": "/users/254399456/projects/28177219",
      "name": "My Folder",
      "created_time": "2026-02-09T08:59:20+00:00",
      "privacy": {"view": "nobody"},
      "manage_link": "https://vimeo.com/user/254399456/folder/28177219"
    }
  ]
}
```

#### Create Folder

```bash
maton api -X POST '/vimeo/me/folders' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Folder"
}
JSON
```

#### Update Folder

```bash
maton api -X PATCH '/vimeo/me/projects/{project_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Renamed Folder"
}
JSON
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Folder

```bash
maton api '/vimeo/me/projects/{project_id}' -X DELETE
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content on success.

#### Get Folder Videos

```bash
maton api '/vimeo/me/projects/{project_id}/videos'
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

#### Add Video to Folder

```bash
maton api -X PUT '/vimeo/me/projects/{project_id}/videos/{video_id}'
```

**Note:** `{project_id}` and `{video_id}` are placeholders. Replace each of them with real values before sending the request.

Returns 204 No Content on success.

#### Remove Video from Folder

```bash
maton api '/vimeo/me/projects/{project_id}/videos/{video_id}' -X DELETE
```

**Note:** `{project_id}` and `{video_id}` are placeholders. Replace each of them with real values before sending the request.

### Album API

#### List Albums

```bash
maton api '/vimeo/me/albums'
```

#### Create Album

```bash
maton api -X POST '/vimeo/me/albums' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "My Showcase",
  "description": "A collection of videos"
}
JSON
```

**Response:**
```json
{
  "uri": "/users/254399456/albums/12099981",
  "name": "My Showcase",
  "description": "A collection of videos",
  "created_time": "2026-02-09T09:00:00+00:00"
}
```

#### Update Album

```bash
maton api -X PATCH '/vimeo/me/albums/{album_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Showcase Name"
}
JSON
```

**Note:** `{album_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Album

```bash
maton api '/vimeo/me/albums/{album_id}' -X DELETE
```

**Note:** `{album_id}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content on success.

#### Get Album Videos

```bash
maton api '/vimeo/me/albums/{album_id}/videos'
```

**Note:** `{album_id}` is a placeholder. Replace it with a real value before sending the request.

#### Add Video to Album

```bash
maton api -X PUT '/vimeo/me/albums/{album_id}/videos/{video_id}'
```

**Note:** `{album_id}` and `{video_id}` are placeholders. Replace each of them with real values before sending the request.

Returns 204 No Content on success.

#### Remove Video from Album

```bash
maton api '/vimeo/me/albums/{album_id}/videos/{video_id}' -X DELETE
```

**Note:** `{album_id}` and `{video_id}` are placeholders. Replace each of them with real values before sending the request.

### Comments API

#### Get Video Comments

```bash
maton api '/vimeo/videos/{video_id}/comments'
```

**Note:** `{video_id}` is a placeholder. Replace it with a real value before sending the request.

#### Add Comment

```bash
maton api -X POST '/vimeo/videos/{video_id}/comments' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "text": "Great video!"
}
JSON
```

**Note:** `{video_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "uri": "/videos/1163160198/comments/21372988",
  "text": "Great video!",
  "created_on": "2026-02-09T09:05:00+00:00"
}
```

#### Delete Comment

```bash
maton api '/vimeo/videos/{video_id}/comments/{comment_id}' -X DELETE
```

**Note:** `{video_id}` and `{comment_id}` are placeholders. Replace each of them with real values before sending the request.

Returns 204 No Content on success.

### Likes API

#### Get Liked Videos

```bash
maton api '/vimeo/me/likes'
```

#### Like Video

```bash
maton api -X PUT '/vimeo/me/likes/{video_id}'
```

**Note:** `{video_id}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content on success.

#### Unlike Video

```bash
maton api '/vimeo/me/likes/{video_id}' -X DELETE
```

**Note:** `{video_id}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content on success.

### Watch Later API

#### Get Watch Later List

```bash
maton api '/vimeo/me/watchlater'
```

#### Add to Watch Later

```bash
maton api -X PUT '/vimeo/me/watchlater/{video_id}'
```

**Note:** `{video_id}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content on success.

#### Remove from Watch Later

```bash
maton api '/vimeo/me/watchlater/{video_id}' -X DELETE
```

**Note:** `{video_id}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content on success.

### Follows API

#### Get Followers

```bash
maton api '/vimeo/me/followers'
```

#### Get Following

```bash
maton api '/vimeo/me/following'
```

#### Follow User

```bash
maton api -X PUT '/vimeo/me/following/{user_id}'
```

**Note:** `{user_id}` is a placeholder. Replace it with a real value before sending the request.

#### Unfollow User

```bash
maton api '/vimeo/me/following/{user_id}' -X DELETE
```

**Note:** `{user_id}` is a placeholder. Replace it with a real value before sending the request.

### Channels API

#### List All Channels

```bash
maton api '/vimeo/channels'
```

#### Get Channel

```bash
maton api '/vimeo/channels/{channel_id}'
```

**Note:** `{channel_id}` is a placeholder. Replace it with a real value before sending the request.

### Categories API

#### List All Categories

```bash
maton api '/vimeo/categories'
```

**Response:**
```json
{
  "total": 10,
  "data": [
    {"uri": "/categories/animation", "name": "Animation"},
    {"uri": "/categories/comedy", "name": "Comedy"},
    {"uri": "/categories/documentary", "name": "Documentary"}
  ]
}
```

#### Get Category Videos

```bash
maton api '/vimeo/categories/{category}/videos'
```

**Note:** `{category}` is a placeholder. Replace it with a real value before sending the request.

### Pagination

Vimeo uses page-based pagination:

```bash
maton api '/vimeo/me/videos?page=1&per_page=25'
```

**Response:**
```json
{
  "total": 50,
  "page": 1,
  "per_page": 25,
  "paging": {
    "next": "/me/videos?page=2",
    "previous": null,
    "first": "/me/videos?page=1",
    "last": "/me/videos?page=2"
  },
  "data": [...]
}
```

Parameters:
- `page` - Page number (default 1)
- `per_page` - Results per page (default 25, max 100)

### Notes

- Video IDs are numeric (e.g., `1163160198`)
- User IDs are numeric (e.g., `254399456`)
- Folders are called "projects" in the API paths
- Albums are also known as "Showcases" in the Vimeo UI
- DELETE and PUT operations return 204 No Content on success
- Video uploads require the TUS protocol (not covered here)
- Rate limits vary by account type

### Resources

- [Vimeo API Reference](https://developer.vimeo.com/api/reference)
- [Vimeo Developer Portal](https://developer.vimeo.com)
- [Maton CLI Manual](https://cli.maton.ai/manual)
