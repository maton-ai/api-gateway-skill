# YouTube

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `youtube`
**Upstream base URL:** `www.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://www.googleapis.com/youtube/v3/search`
- Gateway: `https://api.maton.ai/youtube/youtube/v3/search`

### Search API

#### Search Videos, Channels, or Playlists

```bash
maton youtube search videos 'machine learning' --limit 10 --order viewCount
maton youtube search channels 'rob pike'
maton youtube search playlists 'study music'
```

Or with `maton api`:

```bash
maton api '/youtube/youtube/v3/search?part=snippet&q=machine%20learning&type=video&order=viewCount&maxResults=10'
```

**Query parameters:**
- `part` - Required: `snippet`
- `q` - Search query
- `type` - Filter by type: `video`, `channel`, `playlist`
- `maxResults` - Results per page (1-50, default 5)
- `order` - Sort order: `date`, `rating`, `relevance`, `title`, `viewCount`
- `publishedAfter` - Filter by publish date (RFC 3339)
- `publishedBefore` - Filter by publish date (RFC 3339)
- `channelId` - Filter by channel
- `videoDuration` - `short` (<4min), `medium` (4-20min), `long` (>20min)
- `pageToken` - Pagination token

### Videos API

#### Get Video Details

```bash
maton youtube video get {videoId}
```

Or with `maton api`:

```bash
maton api '/youtube/youtube/v3/videos?part=snippet,statistics,contentDetails&id={videoId}'
```

**Note:** `{videoId}` is a placeholder. Replace it with a real value before sending the request.

**Parts available:**
- `snippet` - Title, description, thumbnails, channel info
- `statistics` - View count, likes, comments
- `contentDetails` - Duration, dimension, definition
- `status` - Upload status, privacy status
- `player` - Embedded player HTML

#### Get My Videos (Uploaded)

```bash
maton youtube search videos --mine --order viewCount --limit 25
```

Or with `maton api`:

```bash
maton api '/youtube/youtube/v3/search?part=snippet&forMine=true&type=video&order=viewCount&maxResults=25'
```

#### Rate Video (Like/Dislike)

```bash
maton youtube video rate {videoId} --rating like
```

Or with `maton api`:

```bash
maton api -X POST '/youtube/youtube/v3/videos/rate?id={videoId}&rating=like'
```

**Note:** `{videoId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Rating values are `like`, `dislike`, `none`.

#### Get Trending Videos

```bash
maton youtube video list --region US --limit 10
```

Or with `maton api`:

```bash
maton api '/youtube/youtube/v3/videos?part=snippet,statistics&chart=mostPopular&regionCode=US&maxResults=10'
```

#### Get Video Categories

```bash
maton youtube video-category list --region US
```

Or with `maton api`:

```bash
maton api '/youtube/youtube/v3/videoCategories?part=snippet&regionCode=US'
```

### Channels API

#### Get Channel Details

```bash
maton youtube channel get {channelId}
```

Or with `maton api`:

```bash
maton api '/youtube/youtube/v3/channels?part=snippet,statistics,contentDetails&id={channelId}'
```

**Note:** `{channelId}` is a placeholder. Replace it with a real value before sending the request.

#### Get My Channel

```bash
maton youtube channel mine
```

Or with `maton api`:

```bash
maton api '/youtube/youtube/v3/channels?part=snippet,statistics,contentDetails&mine=true'
```

**Response:**
```json
{
  "items": [
    {
      "id": "UCxyz123",
      "snippet": {
        "title": "My Channel",
        "description": "Channel description",
        "customUrl": "@mychannel",
        "publishedAt": "2020-01-01T00:00:00Z",
        "thumbnails": {...}
      },
      "statistics": {
        "viewCount": "1000000",
        "subscriberCount": "50000",
        "videoCount": "100"
      },
      "contentDetails": {
        "relatedPlaylists": {
          "uploads": "UUxyz123"
        }
      }
    }
  ]
}
```

#### Get Channel by Username

```bash
maton youtube channel get --username GoogleDevelopers
```

Or with `maton api`:

```bash
maton api '/youtube/youtube/v3/channels?part=snippet,statistics&forUsername=GoogleDevelopers'
```

**Note:** To look up by `@handle` instead, use `maton youtube channel get --handle GoogleDevelopers`.

### Playlists API

#### List My Playlists

```bash
maton youtube playlist list --limit 25
```

Or with `maton api`:

```bash
maton api '/youtube/youtube/v3/playlists?part=snippet,contentDetails&mine=true&maxResults=25'
```

#### Get Playlist

```bash
maton youtube playlist get {playlistId}
```

Or with `maton api`:

```bash
maton api '/youtube/youtube/v3/playlists?part=snippet,contentDetails&id={playlistId}'
```

**Note:** `{playlistId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Playlist

```bash
maton youtube playlist create --title 'My New Playlist' --description 'A collection of videos' --privacy private
```

Or with `maton api`:

```bash
maton api -X POST '/youtube/youtube/v3/playlists?part=snippet,status' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "snippet": {
    "title": "My New Playlist",
    "description": "A collection of videos",
    "defaultLanguage": "en"
  },
  "status": {
    "privacyStatus": "private"
  }
}
JSON
```

**Note:** Privacy values are `public`, `private`, `unlisted`.

#### Update Playlist

```bash
maton youtube playlist update {playlistId} --title 'Updated Playlist Title' --description 'Updated description' --privacy public
```

Or with `maton api`:

```bash
maton api -X PUT '/youtube/youtube/v3/playlists?part=snippet,status' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "id": "{playlistId}",
  "snippet": {
    "title": "Updated Playlist Title",
    "description": "Updated description"
  },
  "status": {
    "privacyStatus": "public"
  }
}
JSON
```

**Note:** `{playlistId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Playlist

```bash
maton youtube playlist delete {playlistId}
```

Or with `maton api`:

```bash
maton api '/youtube/youtube/v3/playlists?id={playlistId}' -X DELETE
```

**Note:** `{playlistId}` is a placeholder. Replace it with a real value before sending the request.

### Playlist Items API

#### List Playlist Items

```bash
maton youtube playlist items {playlistId} --limit 50
```

Or with `maton api`:

```bash
maton api '/youtube/youtube/v3/playlistItems?part=snippet,contentDetails&playlistId={playlistId}&maxResults=50'
```

**Note:** `{playlistId}` is a placeholder. Replace it with a real value before sending the request.

#### Add Video to Playlist

```bash
maton youtube playlist add-video --playlist {playlistId} --video {videoId} --position 0
```

Or with `maton api`:

```bash
maton api -X POST '/youtube/youtube/v3/playlistItems?part=snippet' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "snippet": {
    "playlistId": "{playlistId}",
    "resourceId": {
      "kind": "youtube#video",
      "videoId": "{videoId}"
    },
    "position": 0
  }
}
JSON
```

**Note:** `{playlistId}` and `{videoId}` are placeholders. Replace each of them with real values before sending the request.

#### Remove from Playlist

```bash
maton youtube playlist remove-video {playlistItemId}
```

Or with `maton api`:

```bash
maton api '/youtube/youtube/v3/playlistItems?id={playlistItemId}' -X DELETE
```

**Note:** `{playlistItemId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** The argument is the **playlistItem ID** (from `maton youtube playlist items {playlistId}`), not the video ID.

### Subscriptions API

#### List My Subscriptions

```bash
maton youtube subscription list --limit 50
```

Or with `maton api`:

```bash
maton api '/youtube/youtube/v3/subscriptions?part=snippet&mine=true&maxResults=50'
```

#### Check Subscription to Channel

```bash
maton youtube subscription list --for-channel {channelId}
```

Or with `maton api`:

```bash
maton api '/youtube/youtube/v3/subscriptions?part=snippet&mine=true&forChannelId={channelId}'
```

**Note:** `{channelId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** The response is empty when no subscription exists.

#### Subscribe to Channel

```bash
maton youtube subscription create --channel {channelId}
```

Or with `maton api`:

```bash
maton api -X POST '/youtube/youtube/v3/subscriptions?part=snippet' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "snippet": {
    "resourceId": {
      "kind": "youtube#channel",
      "channelId": "{channelId}"
    }
  }
}
JSON
```

**Note:** `{channelId}` is a placeholder. Replace it with a real value before sending the request.

#### Unsubscribe

```bash
maton youtube subscription delete {subscriptionId}
```

Or with `maton api`:

```bash
maton api '/youtube/youtube/v3/subscriptions?id={subscriptionId}' -X DELETE
```

**Note:** `{subscriptionId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** The argument is the **subscription ID** (from `maton youtube subscription list`), not the channel ID.

### Comments API

#### List Video Comments

```bash
maton youtube comment list --video {videoId} --order time --limit 100
```

Or with `maton api`:

```bash
maton api '/youtube/youtube/v3/commentThreads?part=snippet,replies&videoId={videoId}&order=time&maxResults=100'
```

**Note:** `{videoId}` is a placeholder. Replace it with a real value before sending the request.

#### Add Comment to Video

```bash
maton youtube comment create --video {videoId} --text 'Great video!'
```

Or with `maton api`:

```bash
maton api -X POST '/youtube/youtube/v3/commentThreads?part=snippet' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "snippet": {
    "videoId": "{videoId}",
    "topLevelComment": {
      "snippet": {
        "textOriginal": "Great video!"
      }
    }
  }
}
JSON
```

**Note:** `{videoId}` is a placeholder. Replace it with a real value before sending the request.

#### Reply to Comment

```bash
maton youtube comment create --parent {commentId} --text 'Thanks for your comment!'
```

Or with `maton api`:

```bash
maton api -X POST '/youtube/youtube/v3/comments?part=snippet' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "snippet": {
    "parentId": "{commentId}",
    "textOriginal": "Thanks for your comment!"
  }
}
JSON
```

**Note:** `{commentId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Comment

```bash
maton youtube comment delete {commentId}
```

Or with `maton api`:

```bash
maton api '/youtube/youtube/v3/comments?id={commentId}' -X DELETE
```

**Note:** `{commentId}` is a placeholder. Replace it with a real value before sending the request.

### Pagination

YouTube uses cursor-based pagination via `pageToken`. The CLI automatically paginates with `--paginate`:

```bash
maton youtube playlist items {playlistId} --paginate
```

**Note:** `{playlistId}` is a placeholder. Replace it with a real value before sending the request.

### Examples

```bash
# Search videos as JSON (default format)
maton youtube search videos 'tutorial' --limit 10

# Filter with jq — e.g., extract just video IDs and titles
# Note: --jq requires --json
maton youtube search videos 'tutorial' --limit 10 \
  --json --jq '.items[] | {id: .id.videoId, title: .snippet.title}'

# List your playlists and extract titles only
maton youtube playlist list --json --jq '.items[].snippet.title'
```

### Notes

- Video IDs are 11 characters (e.g., `dQw4w9WgXcQ`)
- Channel IDs start with `UC` (e.g., `UCxyz123`)
- Playlist IDs start with `PL` (user) or `UU` (uploads)
- Use `pageToken` for pagination through large result sets
- The `part` parameter is required and determines what data is returned
- Quota costs vary by endpoint - search is expensive (100 units), reads are cheap (1 unit)
- Some write operations require channel verification

### Resources

- [YouTube Data API Overview](https://developers.google.com/youtube/v3)
- [Search](https://developers.google.com/youtube/v3/docs/search/list)
- [Videos](https://developers.google.com/youtube/v3/docs/videos)
- [Channels](https://developers.google.com/youtube/v3/docs/channels)
- [Playlists](https://developers.google.com/youtube/v3/docs/playlists)
- [PlaylistItems](https://developers.google.com/youtube/v3/docs/playlistItems)
- [Subscriptions](https://developers.google.com/youtube/v3/docs/subscriptions)
- [Comments](https://developers.google.com/youtube/v3/docs/comments)
- [Quota Calculator](https://developers.google.com/youtube/v3/determine_quota_cost)
- [Maton CLI Manual](https://cli.maton.ai/manual)
