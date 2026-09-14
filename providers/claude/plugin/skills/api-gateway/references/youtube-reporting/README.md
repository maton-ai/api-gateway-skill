# YouTube Reporting

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `youtube-reporting`
**Upstream base URL:** `youtubereporting.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://youtubereporting.googleapis.com/v1/reportTypes`
- Gateway: `https://api.maton.ai/youtube-reporting/v1/reportTypes`

### Report Types API

#### List Report Types

```bash
maton api '/youtube-reporting/v1/reportTypes'
```

With pagination:
```bash
maton api '/youtube-reporting/v1/reportTypes?pageSize=10&pageToken={nextPageToken}'
```

**Note:** `{nextPageToken}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `pageSize` (optional) | number | Number of results per page |
| `pageToken` (optional) | string | Token for retrieving next page |
| `includeSystemManaged` (optional) | boolean | Include system-managed report types (default: `false`) |

**Response:**

```json
{
  "reportTypes": [
    {
      "id": "channel_basic_a3",
      "name": "User activity"
    },
    {
      "id": "channel_demographics_a1",
      "name": "Demographics"
    },
    {
      "id": "channel_device_os_a3",
      "name": "Device and OS"
    },
    {
      "id": "channel_traffic_source_a3",
      "name": "Traffic sources"
    }
  ],
  "nextPageToken": "..."
}
```

**Available Channel Report Types:**

| Report Type ID | Name |
|----------------|------|
| `channel_basic_a3` | User activity |
| `channel_combined_a3` | Combined |
| `channel_demographics_a1` | Demographics |
| `channel_device_os_a3` | Device and OS |
| `channel_annotations_a1` | Annotations |
| `channel_cards_a1` | Cards |
| `channel_end_screens_a1` | End screens |
| `channel_playback_location_a3` | Playback locations |
| `channel_province_a3` | Province |
| `channel_reach_basic_a1` | Reach basic |
| `channel_reach_combined_a1` | Reach combined |
| `channel_sharing_service_a1` | Sharing service |
| `channel_subtitles_a3` | Subtitles |
| `channel_traffic_source_a3` | Traffic sources |

**Available Playlist Report Types:**

| Report Type ID | Name |
|----------------|------|
| `playlist_basic_a2` | Playlist user activity |
| `playlist_combined_a2` | Playlist combined |
| `playlist_device_os_a2` | Playlist device and OS |
| `playlist_playback_location_a2` | Playlist playback locations |
| `playlist_province_a2` | Playlist province |
| `playlist_traffic_source_a2` | Playlist traffic sources |

### Jobs API

#### List Jobs

```bash
maton api '/youtube-reporting/v1/jobs'
```

Include system-managed:
```bash
maton api '/youtube-reporting/v1/jobs?includeSystemManaged=true'
```

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `pageSize` (optional) | number | Number of results per page |
| `pageToken` (optional) | string | Token for retrieving next page |
| `includeSystemManaged` (optional) | boolean | Include system-managed jobs (default: `false`) |

**Response:**

```json
{
  "jobs": [
    {
      "id": "92f0f65f-18c4-4d15-a815-82223ae93ead",
      "reportTypeId": "channel_basic_a3",
      "name": "Test User Activity Report",
      "createTime": "2026-05-04T22:21:48Z"
    }
  ],
  "nextPageToken": "..."
}
```

#### Get Job

```bash
maton api '/youtube-reporting/v1/jobs/{jobId}'
```

**Note:** `{jobId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": "92f0f65f-18c4-4d15-a815-82223ae93ead",
  "reportTypeId": "channel_basic_a3",
  "name": "Test User Activity Report",
  "createTime": "2026-05-04T22:21:48Z"
}
```

#### Create Job

```bash
maton api -X POST '/youtube-reporting/v1/jobs' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "reportTypeId": "channel_basic_a3",
  "name": "My Daily User Activity Report"
}
JSON
```

**Request body:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `reportTypeId` | string | Yes | Report type ID from `reportTypes.list` |
| `name` | string | Yes | Display name for the job |

**Response:**
```json
{
  "id": "92f0f65f-18c4-4d15-a815-82223ae93ead",
  "reportTypeId": "channel_basic_a3",
  "name": "Daily User Activity",
  "createTime": "2026-05-04T22:21:48.331114Z"
}
```

#### Delete Job

```bash
maton api '/youtube-reporting/v1/jobs/{jobId}' -X DELETE
```

**Note:** `{jobId}` is a placeholder. Replace it with a real value before sending the request.

**Example:**

```bash
maton api '/youtube-reporting/v1/jobs/{job_id}' -X DELETE
```

**Note:** `{job_id}` is a placeholder. Replace it with a real value before sending the request.

Returns empty response on success.

#### List Job Reports

```bash
maton api '/youtube-reporting/v1/jobs/{jobId}/reports'
```

**Note:** `{jobId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `createdAfter` (optional) | string | Filter reports created after this timestamp (RFC3339 UTC) |
| `startTimeAtOrAfter` (optional) | string | Filter by report data start time (on or after) |
| `startTimeBefore` (optional) | string | Filter by report data start time (before) |
| `pageSize` (optional) | number | Number of results per page |
| `pageToken` (optional) | string | Token for retrieving next page |

**Example:**

```bash
maton api '/youtube-reporting/v1/jobs/{job_id}/reports'
```

**Note:** `{job_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "reports": [
    {
      "id": "report-id-123",
      "startTime": "2025-04-01T07:00:00Z",
      "endTime": "2025-04-02T07:00:00Z",
      "downloadUrl": "https://youtubereporting.googleapis.com/...",
      "createTime": "2025-04-02T10:00:00Z"
    }
  ],
  "nextPageToken": "..."
}
```

### Reports API

#### List Reports for Job

```bash
maton api '/youtube-reporting/v1/jobs/{jobId}/reports'
```

With date filters:
```bash
maton api '/youtube-reporting/v1/jobs/{jobId}/reports?startTimeAtOrAfter=2025-04-01T00:00:00Z&startTimeBefore=2025-05-01T00:00:00Z'
```

**Note:** `{jobId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Report

```bash
maton api '/youtube-reporting/v1/jobs/{jobId}/reports/{reportId}'
```

**Note:** `{jobId}` and `{reportId}` are placeholders. Replace each of them with real values before sending the request.

#### Download Report

Reports provide a `downloadUrl` pointing at `https://youtubereporting.googleapis.com/...`. Do **not** send your `MATON_API_KEY` to that raw Google host — the key is a Maton credential and must only ever be sent to `api.maton.ai`. Instead, route the download through the Maton proxy by replacing the Google host with the skill's base URL, so Maton injects the correct Google OAuth token:

```bash
# downloadUrl looks like https://youtubereporting.googleapis.com/v1/media/{resourceName}
# Drop the Google host and call the same path through the gateway, which
# authenticates with your connection.
maton api '/youtube-reporting/v1/media/{resourceName}'
```

**Note:** `{resourceName}` is a placeholder. Replace it with a real value before sending the request.

### Notes

- Reports are generated daily as CSV files; first report available ~24 hours after job creation
- Each report covers a single day (startTime to endTime = 24 hours)
- Use `downloadUrl` from report metadata to download the CSV file
- Only one job per `reportTypeId` allowed (409 on duplicate)
- System-managed jobs cannot be created or deleted (403)
- Pagination uses `pageSize` + `pageToken`/`nextPageToken`
- Common report types: `channel_basic_a3` (user activity), `channel_demographics_a1`, `channel_traffic_source_a3`, `channel_device_os_a3`

### Resources

- [YouTube Reporting API Reference](https://developers.google.com/youtube/reporting/v1/reference/rest)
- [Bulk Reports Documentation](https://developers.google.com/youtube/reporting/v1/reports)
- [Report Types](https://developers.google.com/youtube/reporting/v1/reports/full_report_list)
- [Maton CLI Manual](https://cli.maton.ai/manual)
