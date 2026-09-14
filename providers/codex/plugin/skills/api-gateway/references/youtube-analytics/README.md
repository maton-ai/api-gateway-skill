# YouTube Analytics

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `youtube-analytics`
**Upstream base URL:** `youtubeanalytics.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://youtubeanalytics.googleapis.com/v2/groups`
- Gateway: `https://api.maton.ai/youtube-analytics/v2/groups`

### Reports API

#### Query Reports

```bash
maton api '/youtube-analytics/v2/reports?ids=channel==MINE&startDate=2025-01-01&endDate=2025-01-31&metrics=views,likes,comments'
```

With dimensions and sorting:
```bash
maton api '/youtube-analytics/v2/reports?ids=channel==MINE&startDate=2025-01-01&endDate=2025-03-31&metrics=views,estimatedMinutesWatched&dimensions=day&sort=-views&maxResults=10'
```

Monthly aggregation (endDate must align to 1st of month):
```bash
maton api '/youtube-analytics/v2/reports?ids=channel==MINE&startDate=2024-01-01&endDate=2024-12-01&metrics=views,subscribersGained&dimensions=month'
```

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `ids` (required) | string | Channel identifier: `channel==MINE` or `channel==CHANNEL_ID` |
| `startDate` (required) | string | Start date in `YYYY-MM-DD` format |
| `endDate` (required) | string | End date in `YYYY-MM-DD` format |
| `metrics` (required) | string | Comma-separated metrics (e.g., `views,likes,comments`) |
| `dimensions` (optional) | string | Comma-separated dimensions (e.g., `day`, `month`, `country`, `video`) |
| `filters` (optional) | string | Filters in format `dimension==value` (e.g., `country==US`) |
| `sort` (optional) | string | Sort field; prefix with `-` for descending (e.g., `-views`) |
| `maxResults` (optional) | integer | Maximum rows to return |
| `startIndex` (optional) | integer | 1-based pagination start index |
| `currency` (optional) | string | ISO 4217 currency code for revenue metrics (default: USD) |

**Response:**

```json
{
  "kind": "youtubeAnalytics#resultTable",
  "columnHeaders": [
    {
      "name": "day",
      "columnType": "DIMENSION",
      "dataType": "STRING"
    },
    {
      "name": "views",
      "columnType": "METRIC",
      "dataType": "INTEGER"
    }
  ],
  "rows": [
    ["2025-03-12", 4],
    ["2025-03-15", 2]
  ]
}
```

**Common Metrics:**
- `views` - Total video views
- `likes` - Total likes
- `dislikes` - Total dislikes
- `comments` - Total comments
- `shares` - Total shares
- `estimatedMinutesWatched` - Total watch time in minutes
- `averageViewDuration` - Average view duration in seconds
- `subscribersGained` - New subscribers gained
- `subscribersLost` - Subscribers lost
- `averageViewPercentage` - Average percentage of video watched
- `cardClickRate` - Card click rate

**Common Dimensions:**
- `day` - Daily aggregation (YYYY-MM-DD)
- `month` - Monthly aggregation (YYYY-MM); endDate must align to 1st of month
- `country` - ISO 3166-1 alpha-2 country code
- `video` - Per-video breakdown
- `deviceType` - Device type (DESKTOP, MOBILE, TABLET, TV, etc.)
- `operatingSystem` - OS (ANDROID, IOS, WINDOWS, etc.)
- `liveOrOnDemand` - LIVE or ON_DEMAND
- `subscribedStatus` - SUBSCRIBED or UNSUBSCRIBED

### Groups API

#### List Groups

```bash
maton api '/youtube-analytics/v2/groups?mine=true'
```

Or by specific IDs:

```bash
maton api '/youtube-analytics/v2/groups?id={group_id}'
```

**Note:** `{group_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `mine` | boolean | Set to `true` to retrieve all groups owned by authenticated user |
| `id` | string | Comma-separated group IDs to retrieve |
| `pageToken` | string | Token for paginating results |

**Response:**
```json
{
  "items": [
    {
      "kind": "youtube#group",
      "etag": "CQVfQEQY1xqZ2O8xKat5QfS2cik",
      "id": "JiAz5ne9Wwk",
      "snippet": {
        "title": "My Video Group",
        "publishedAt": "2026-05-04T22:02:12Z"
      },
      "contentDetails": {
        "itemType": "youtube#video"
      }
    }
  ],
  "nextPageToken": "..."
}
```

#### Get Groups by ID

```bash
maton api '/youtube-analytics/v2/groups?id={group_id}'
```

**Note:** `{group_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Group

```bash
maton api -X POST '/youtube-analytics/v2/groups' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "snippet": {
    "title": "My New Group"
  },
  "contentDetails": {
    "itemType": "youtube#video"
  }
}
JSON
```

**Valid item types:** `youtube#video`, `youtube#playlist`, `youtube#channel`, `youtubePartner#asset`

**Example:**

```bash
maton api -X POST '/youtube-analytics/v2/groups' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "snippet": {
    "title": "My New Group"
  },
  "contentDetails": {
    "itemType": "youtube#video"
  }
}
JSON
```

#### Update Group

```bash
maton api -X PUT '/youtube-analytics/v2/groups' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "id": "{group_id}",
  "snippet": {
    "title": "Updated Title"
  },
  "contentDetails": {
    "itemType": "youtube#video"
  }
}
JSON
```

**Note:** `{group_id}` is a placeholder. Replace it with a real value before sending the request.

Only the group title can be updated.

#### Delete Group

```bash
maton api '/youtube-analytics/v2/groups?id={group_id}' -X DELETE
```

**Note:** `{group_id}` is a placeholder. Replace it with a real value before sending the request.

### Group Items API

#### List Group Items

```bash
maton api '/youtube-analytics/v2/groupItems?groupId={group_id}'
```

**Note:** `{group_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "kind": "youtube#groupItemListResponse",
  "etag": "...",
  "items": [
    {
      "kind": "youtube#groupItem",
      "etag": "...",
      "groupId": "JiAz5ne9Wwk",
      "resource": {
        "kind": "youtube#video",
        "id": "VIDEO_ID"
      }
    }
  ]
}
```

#### Add Item to Group

```bash
maton api -X POST '/youtube-analytics/v2/groupItems' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "groupId": "{group_id}",
  "resource": {
    "kind": "youtube#video",
    "id": "{video_id}"
  }
}
JSON
```

**Note:** `{group_id}` and `{video_id}` are placeholders. Replace each of them with real values before sending the request.

Returns 201 on success, 204 if item already exists in group. Maximum 500 items per group.

#### Remove Item from Group

```bash
maton api '/youtube-analytics/v2/groupItems?id={group_item_id}' -X DELETE
```

**Note:** `{group_item_id}` is a placeholder. Replace it with a real value before sending the request.

### Report Parameters

**Query parameters:** `ids` (required), `startDate` (required), `endDate` (required), `metrics` (required), `dimensions` (optional), `filters` (optional), `sort` (optional), `maxResults` (optional), `startIndex` (optional), `currency` (optional)

**Common Metrics:** `views`, `likes`, `comments`, `shares`, `estimatedMinutesWatched`, `averageViewDuration`, `subscribersGained`, `subscribersLost`

**Common Dimensions:** `day`, `month`, `country`, `video`, `deviceType`, `operatingSystem`

### Notes

- Dates use `YYYY-MM-DD` format
- `month` dimension requires `endDate` aligned to 1st of month
- `ids=channel==MINE` targets authenticated user's channel
- Groups support up to 500 items of a single type: `youtube#video`, `youtube#playlist`, `youtube#channel`, `youtubePartner#asset`
- Only group title can be updated; use groupItems methods for membership
- Groups pagination uses `pageToken`; reports use `startIndex` + `maxResults`

### Resources

- [YouTube Analytics API Reference](https://developers.google.com/youtube/analytics/reference)
- [Channel Reports](https://developers.google.com/youtube/analytics/channel_reports)
- [Metrics Reference](https://developers.google.com/youtube/analytics/metrics)
- [Maton CLI Manual](https://cli.maton.ai/manual)
