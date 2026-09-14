# Zoom

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `zoom`
**Upstream base URL:** `api.zoom.us`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.zoom.us/v2/users/me`
- Gateway: `https://api.maton.ai/zoom/v2/users/me`

### Users API

#### Get Current User

```bash
maton api '/zoom/v2/users/me'
```

**Response:**
```json
{
  "id": "APv5EPHiSvitxgPAw0DbaQ",
  "first_name": "John",
  "last_name": "Doe",
  "display_name": "John Doe",
  "email": "john@example.com",
  "type": 1,
  "role_name": "Owner",
  "pmi": 5017823017,
  "timezone": "America/Los_Angeles",
  "status": "active",
  "created_at": "2023-06-01T19:33:22Z",
  "last_login_time": "2026-04-10T00:35:21Z"
}
```

**User Types:**
- `1` - Basic
- `2` - Licensed
- `3` - On-prem

### Meetings API

#### List User's Meetings

```bash
maton api '/zoom/v2/users/me/meetings'

maton api '/zoom/v2/users/{userId}/meetings'
```

**Note:** `{userId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `type` | string | `scheduled`, `live`, `upcoming`, `upcoming_meetings`, `previous_meetings` |
| `page_size` | integer | Results per page (max 300, default 30) |
| `next_page_token` | string | Pagination token |
| `from` | string | Start date (YYYY-MM-DD) |
| `to` | string | End date (YYYY-MM-DD) |

**Response:**
```json
{
  "page_size": 30,
  "total_records": 1,
  "next_page_token": "",
  "meetings": [
    {
      "uuid": "RPrVctdSRxaVmIUTHVUlGQ==",
      "id": 82931897821,
      "host_id": "APv5EPHiSvitxgPAw0DbaQ",
      "topic": "Team Standup",
      "type": 2,
      "start_time": "2026-04-10T00:39:32Z",
      "duration": 30,
      "timezone": "America/Los_Angeles",
      "join_url": "https://us05web.zoom.us/j/82931897821?pwd=..."
    }
  ]
}
```

#### Get Upcoming Meetings

```bash
maton api '/zoom/v2/users/me/upcoming_meetings'
```

Returns meetings scheduled for the future.

#### Create Meeting

```bash
maton api -X POST '/zoom/v2/users/me/meetings'

maton api -X POST '/zoom/v2/users/{userId}/meetings' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "topic": "Weekly Team Sync",
  "type": 2,
  "start_time": "2026-04-15T14:00:00Z",
  "duration": 60,
  "timezone": "America/Los_Angeles",
  "agenda": "Discuss project updates",
  "settings": {
    "host_video": true,
    "participant_video": true,
    "join_before_host": false,
    "mute_upon_entry": true,
    "waiting_room": true
  }
}
JSON
```

**Note:** `{userId}` is a placeholder. Replace it with a real value before sending the request.

**Meeting Types:**
- `1` - Instant meeting
- `2` - Scheduled meeting
- `3` - Recurring meeting with no fixed time
- `8` - Recurring meeting with fixed time

**Example - Create Meeting:**

```bash
maton api -X POST '/zoom/v2/users/me/meetings' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "topic": "Project Review",
  "type": 2,
  "start_time": "2026-04-15T14:00:00Z",
  "duration": 60,
  "timezone": "America/Los_Angeles",
  "settings": {
    "host_video": true,
    "participant_video": true,
    "waiting_room": true
  }
}
JSON
```

**Response:**
```json
{
  "uuid": "RPrVctdSRxaVmIUTHVUlGQ==",
  "id": 82931897821,
  "host_id": "APv5EPHiSvitxgPAw0DbaQ",
  "host_email": "john@example.com",
  "topic": "Project Review",
  "type": 2,
  "status": "waiting",
  "start_time": "2026-04-15T14:00:00Z",
  "duration": 60,
  "timezone": "America/Los_Angeles",
  "start_url": "https://us05web.zoom.us/s/82931897821?zak=...",
  "join_url": "https://us05web.zoom.us/j/82931897821?pwd=...",
  "password": "AX2hsd"
}
```

#### Get Meeting

```bash
maton api '/zoom/v2/meetings/{meetingId}'
```

**Note:** `{meetingId}` is a placeholder. Replace it with a real value before sending the request.

**Path Parameters:**
- `meetingId` - Meeting ID or UUID (double-encode UUID if it contains `/` or `//`)

#### Update Meeting

```bash
maton api -X PATCH '/zoom/v2/meetings/{meetingId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "topic": "Updated Meeting Title",
  "duration": 45,
  "settings": {
    "waiting_room": false
  }
}
JSON
```

**Note:** `{meetingId}` is a placeholder. Replace it with a real value before sending the request.

**Example - Update Meeting:**

```bash
maton api -X PATCH '/zoom/v2/meetings/{meetingId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "topic": "Updated Meeting Title",
  "duration": 45,
  "settings": {
    "waiting_room": false
  }
}
JSON
```

**Note:** `{meetingId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Meeting

```bash
maton api '/zoom/v2/meetings/{meetingId}' -X DELETE
```

**Note:** `{meetingId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `schedule_for_reminder` - Send cancellation email to registrants (boolean)
- `cancel_meeting_reminder` - Send cancellation email notification (boolean)

### Recordings API

#### List User's Recordings

```bash
maton api '/zoom/v2/users/me/recordings'

maton api '/zoom/v2/users/{userId}/recordings'
```

**Note:** `{userId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `from` | string | Start date (YYYY-MM-DD) |
| `to` | string | End date (YYYY-MM-DD) |
| `page_size` | integer | Results per page (max 300, default 30) |
| `next_page_token` | string | Pagination token |
| `trash` | boolean | List trashed recordings |
| `trash_type` | string | `meeting_recordings` or `recording_file` |

**Response:**
```json
{
  "from": "2026-04-01",
  "to": "2026-04-10",
  "page_count": 1,
  "page_size": 30,
  "total_records": 2,
  "next_page_token": "",
  "meetings": [
    {
      "uuid": "...",
      "id": 123456789,
      "topic": "Team Meeting",
      "start_time": "2026-04-05T14:00:00Z",
      "duration": 45,
      "total_size": 52428800,
      "recording_count": 2,
      "recording_files": [
        {
          "id": "...",
          "meeting_id": "...",
          "recording_start": "2026-04-05T14:00:00Z",
          "recording_end": "2026-04-05T14:45:00Z",
          "file_type": "MP4",
          "file_size": 50000000,
          "play_url": "https://...",
          "download_url": "https://...",
          "status": "completed",
          "recording_type": "shared_screen_with_speaker_view"
        }
      ]
    }
  ]
}
```

#### List User's Cloud Recordings

```bash
maton api '/zoom/v2/users/{userId}/recordings'
```

**Note:** `{userId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Meeting Recordings

```bash
maton api '/zoom/v2/meetings/{meetingId}/recordings'
```

**Note:** `{meetingId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Meeting Recordings

```bash
maton api '/zoom/v2/meetings/{meetingId}/recordings' -X DELETE
```

**Note:** `{meetingId}` is a placeholder. Replace it with a real value before sending the request.

### Webinars API

**Note:** Requires Webinar add-on plan.

#### List User's Webinars

```bash
maton api '/zoom/v2/users/me/webinars'

maton api '/zoom/v2/users/{userId}/webinars'
```

**Note:** `{userId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Webinar

```bash
maton api -X POST '/zoom/v2/users/{userId}/webinars' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "topic": "Product Launch Webinar",
  "type": 5,
  "start_time": "2026-05-01T10:00:00Z",
  "duration": 90,
  "timezone": "America/Los_Angeles"
}
JSON
```

**Note:** `{userId}` is a placeholder. Replace it with a real value before sending the request.

**Webinar Types:**
- `5` - Scheduled webinar
- `6` - Recurring webinar with no fixed time
- `9` - Recurring webinar with fixed time

#### Get/Update/Delete Webinar

```bash
maton api '/zoom/v2/webinars/{webinarId}'
maton api '/zoom/v2/webinars/{webinarId}' -X DELETE
```

**Note:** `{webinarId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Webinar

```bash
maton api -X PATCH '/zoom/v2/webinars/{webinarId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "topic": "Updated Webinar Title"
}
JSON
```

**Note:** `{webinarId}` is a placeholder. Replace it with a real value before sending the request.

### Meeting Registrants API

#### List Registrants

```bash
maton api '/zoom/v2/meetings/{meetingId}/registrants'
```

**Note:** `{meetingId}` is a placeholder. Replace it with a real value before sending the request.

#### Add Registrant

```bash
maton api -X POST '/zoom/v2/meetings/{meetingId}/registrants' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "email": "attendee@example.com",
  "first_name": "Jane",
  "last_name": "Smith"
}
EOF
```

**Note:** `{meetingId}` is a placeholder. Replace it with a real value before sending the request.

### Past Meetings API

#### List Past Meeting Participants

```bash
maton api '/zoom/v2/past_meetings/{meetingUUID}/participants'
```

**Note:** `{meetingUUID}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Use double-encoded UUID if it contains `/` or `//`.

#### Get Past Meeting Details

```bash
maton api '/zoom/v2/past_meetings/{meetingUUID}'
```

**Note:** `{meetingUUID}` is a placeholder. Replace it with a real value before sending the request.

### Meeting Types

| Type | Description |
|------|-------------|
| 1 | Instant meeting |
| 2 | Scheduled meeting |
| 3 | Recurring meeting (no fixed time) |
| 8 | Recurring meeting (fixed time) |

### Webinar Types

| Type | Description |
|------|-------------|
| 5 | Scheduled webinar |
| 6 | Recurring webinar (no fixed time) |
| 9 | Recurring webinar (fixed time) |

### Pagination

Cursor-based pagination using `next_page_token`:

```bash
maton api '/zoom/v2/users/me/meetings?page_size=50&next_page_token={token}'
```

**Note:** `{token}` is a placeholder. Replace it with a real value before sending the request.

Response includes:
```json
{
  "page_size": 50,
  "total_records": 150,
  "next_page_token": "abc123...",
  "meetings": [...]
}
```

### Notes

- Use `me` to reference the authenticated user
- Meeting IDs are numeric; UUIDs are base64-encoded
- Double-encode UUID if it contains `/` or `//`
- Webinar endpoints require Webinar add-on subscription
- Some endpoints require admin scopes
- Rate limits: varies by plan, returns HTTP 429 when exceeded

### Resources

- [Zoom API Documentation](https://developers.zoom.us/docs/api/)
- [Zoom REST API Reference](https://developers.zoom.us/docs/api/rest/reference/zoom-api/methods/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
