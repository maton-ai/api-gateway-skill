# Zoom Admin

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

> **⚠ Account-wide administrative access.** This app is not the ordinary Zoom integration — it connects with **admin-level OAuth scopes covering the entire Zoom account**, so calls act across every user in the organization, not just the connected user. That reach includes other people's meetings and webinars, their per-user settings, account-level settings, and **cloud recordings and transcripts of meetings the user did not attend** — which routinely contain confidential discussion, personal data, and third-party confidences.
>
> - **Prefer the `zoom` app** for anything scoped to the user's own meetings and recordings. Reach for `zoom-admin` only when the task genuinely requires acting on other users or account-level configuration, and say why.
> - **Name the target user before acting.** `{userId}` is an opaque ID; resolve it, show the user whose account is being read or changed, and never fan out across users to "find" something.
> - **Recordings and transcripts are the most sensitive resource here.** Do not list, download, or summarize another user's recordings without the user confirming that specific person and that they are authorized to access it. Never bulk-export recordings.
> - **Account settings and per-user settings changes affect everyone** and are not obviously reversible. Read the current value, show the exact before/after, and get explicit approval per setting.
> - Deleting a meeting or webinar notifies and disrupts external participants — see the high-impact operations list in [SKILL.md](../../SKILL.md#security--permissions).

**App name:** `zoom-admin`
**Upstream base URL:** `api.zoom.us`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.zoom.us/v2/users/me`
- Gateway: `https://api.maton.ai/zoom-admin/v2/users/me`

### User API

#### List Users

```bash
maton api '/zoom-admin/v2/users?status=active&page_size=30'
```

**Query parameters:**

| Param | Type | Default | Description |
|-------|------|---------|-------------|
| `status` | string | `active` | `active`, `inactive`, or `pending` |
| `page_size` | integer | 30 | Max: 2000 |
| `next_page_token` | string | | Pagination token (15-min expiry) |
| `role_id` | string | | Filter by role ID |

**Response:**
```json
{
  "page_size": 30,
  "total_records": 1,
  "next_page_token": "",
  "users": [
    {
      "id": "a-IOECePRV265Gy_wotUdQ",
      "first_name": "Richard",
      "last_name": "Song",
      "display_name": "Richard Song",
      "email": "user@example.com",
      "type": 1,
      "pmi": 6862513852,
      "timezone": "America/Los_Angeles",
      "verified": 1,
      "status": "active",
      "created_at": "2025-03-21T21:52:50Z",
      "last_login_time": "2026-05-01T01:01:08Z",
      "role_id": "0"
    }
  ]
}
```

User type values: `1` = Basic, `2` = Licensed, `4` = Unassigned, `99` = None.

#### Get Own User

```bash
maton api '/zoom-admin/v2/users/me'
```

#### Get User

```bash
maton api '/zoom-admin/v2/users/{userId}'
```

**Note:** `{userId}` is a placeholder. Replace it with a real value before sending the request.

Use `me` for the authenticated user, or a user ID / email address.

**Response:**
```json
{
  "id": "a-IOECePRV265Gy_wotUdQ",
  "first_name": "Richard",
  "last_name": "Song",
  "display_name": "Richard Song",
  "email": "user@example.com",
  "type": 1,
  "role_name": "Owner",
  "pmi": 6862513852,
  "use_pmi": false,
  "personal_meeting_url": "https://us05web.zoom.us/j/6862513852?pwd=...",
  "timezone": "America/Los_Angeles",
  "status": "active",
  "account_id": "ciah2jjMRgedBSqxO8bOjA",
  "role_id": "0",
  "login_types": [100, 1],
  "created_at": "2025-03-21T21:52:50Z",
  "last_login_time": "2026-05-01T01:01:08Z"
}
```

#### Get User Settings

```bash
maton api '/zoom-admin/v2/users/{userId}/settings'
```

**Note:** `{userId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Param | Type | Description |
|-------|------|-------------|
| `option` | string | `meeting_authentication`, `recording_authentication`, or `meeting_security` |

Returns detailed settings grouped into sections: `schedule_meeting`, `in_meeting`, `email_notification`, `recording`, `telephony`, `feature`, `whiteboard`, `audio_conferencing`, etc.

### Meetings API

#### List User's Meetings

```bash
maton api '/zoom-admin/v2/users/{userId}/meetings?type=scheduled&page_size=30'
```

**Note:** `{userId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Param | Type | Default | Description |
|-------|------|---------|-------------|
| `type` | string | `live` | `scheduled`, `live`, `upcoming`, `upcoming_meetings`, `previous_meetings` |
| `page_size` | integer | 30 | Max: 300 |
| `next_page_token` | string | | Pagination token (15-min expiry) |

**Response:**
```json
{
  "page_size": 30,
  "total_records": 11,
  "next_page_token": "B4Tr0tLbJKQMnChspbYH7UUvt7g0UeDQNh2",
  "meetings": [
    {
      "uuid": "SukzvlkXQO2rNcNPKUGCpw==",
      "id": 89560318205,
      "host_id": "a-IOECePRV265Gy_wotUdQ",
      "topic": "Team Standup",
      "type": 2,
      "start_time": "2026-03-30T18:00:00Z",
      "duration": 30,
      "timezone": "America/Los_Angeles",
      "created_at": "2026-03-29T18:01:40Z",
      "join_url": "https://us05web.zoom.us/j/89560318205?pwd=..."
    }
  ]
}
```

Meeting type values: `1` = Instant, `2` = Scheduled, `3` = Recurring (no fixed time), `4` = PMI, `8` = Recurring (fixed time).

#### Get Meeting

```bash
maton api '/zoom-admin/v2/meetings/{meetingId}'
```

**Note:** `{meetingId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Param | Type | Description |
|-------|------|-------------|
| `occurrence_id` | string | For recurring meetings |
| `show_previous_occurrences` | boolean | Show previous occurrences |

Returns full meeting details including `settings`, `recurrence`, `occurrences`, `join_url`, `start_url`, `password`, etc.

#### Create Meeting

```bash
maton api -X POST '/zoom-admin/v2/users/{userId}/meetings' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "topic": "Weekly Team Sync",
  "type": 2,
  "start_time": "2026-05-02T10:00:00Z",
  "duration": 30,
  "timezone": "America/Los_Angeles",
  "agenda": "Discuss project updates",
  "settings": {
    "host_video": true,
    "participant_video": true,
    "join_before_host": false,
    "mute_upon_entry": true,
    "waiting_room": false,
    "auto_recording": "none",
    "audio": "voip"
  }
}
JSON
```

**Note:** `{userId}` is a placeholder. Replace it with a real value before sending the request.

**Key Request Fields:**

| Field | Type | Description |
|-------|------|-------------|
| `topic` | string | Meeting topic |
| `type` | integer | `1` = Instant, `2` = Scheduled, `3` = Recurring (no fixed), `8` = Recurring (fixed) |
| `start_time` | string | ISO 8601 datetime (required for type 2 and 8) |
| `duration` | integer | Duration in minutes |
| `timezone` | string | e.g., `America/New_York` |
| `password` | string | Up to 10 characters |
| `agenda` | string | Max 2000 characters |
| `recurrence` | object | Required for type 8 |
| `settings` | object | Meeting settings |

Returns the created meeting object (same as Get Meeting).

#### Update Meeting

```bash
maton api -X PATCH '/zoom-admin/v2/meetings/{meetingId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "topic": "Updated Meeting Topic",
  "duration": 45
}
JSON
```

**Note:** `{meetingId}` is a placeholder. Replace it with a real value before sending the request.

All fields are optional. Returns `204 No Content` on success.

#### Delete Meeting

```bash
maton api '/zoom-admin/v2/meetings/{meetingId}' -X DELETE
```

**Note:** `{meetingId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Param | Type | Description |
|-------|------|-------------|
| `occurrence_id` | string | Delete specific occurrence of recurring meeting |
| `schedule_for_reminder` | boolean | Notify host about cancellation |
| `cancel_meeting_reminder` | string | Notify registrants (`true`/`false`) |

Returns `204 No Content` on success.

#### Get Past Meeting Details

```bash
maton api '/zoom-admin/v2/past_meetings/{meetingId}'
```

**Note:** `{meetingId}` is a placeholder. Replace it with a real value before sending the request.

Use the meeting ID or UUID. If the UUID starts with `/` or contains `//`, it must be double-URL-encoded.

**Response:**
```json
{
  "uuid": "/LAYgqiEQ8CW4NlhkyOvVA==",
  "id": 89560318205,
  "host_id": "a-IOECePRV265Gy_wotUdQ",
  "type": 2,
  "topic": "Team Standup",
  "user_name": "Richard Song",
  "user_email": "user@example.com",
  "start_time": "2026-03-30T18:02:25Z",
  "end_time": "2026-03-30T18:09:50Z",
  "duration": 8,
  "total_minutes": 22,
  "participants_count": 3,
  "source": "Calendly for Zoom"
}
```

#### List Past Meeting Instances

```bash
maton api '/zoom-admin/v2/past_meetings/{meetingId}/instances'
```

**Note:** `{meetingId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "meetings": [
    {
      "uuid": "/LAYgqiEQ8CW4NlhkyOvVA==",
      "start_time": "2026-03-30T18:02:25Z"
    }
  ]
}
```

### Webinars API

**Note:** Requires Webinar add-on plan.

#### List Webinars

```bash
maton api '/zoom-admin/v2/users/{userId}/webinars?page_size=30'
```

**Note:** `{userId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Param | Type | Default | Description |
|-------|------|---------|-------------|
| `type` | string | `scheduled` | `scheduled` or `upcoming` |
| `page_size` | integer | 30 | Max: 300 |
| `next_page_token` | string | | Pagination token |

**Response:**
```json
{
  "page_size": 30,
  "total_records": 0,
  "next_page_token": "",
  "webinars": [
    {
      "uuid": "...",
      "id": 12345678901,
      "host_id": "...",
      "topic": "Product Launch Webinar",
      "type": 5,
      "start_time": "2026-05-15T14:00:00Z",
      "duration": 60,
      "timezone": "America/Los_Angeles",
      "join_url": "https://us05web.zoom.us/j/..."
    }
  ]
}
```

Webinar type values: `5` = Webinar, `6` = Recurring (no fixed time), `9` = Recurring (fixed time).

#### Get Webinar

```bash
maton api '/zoom-admin/v2/webinars/{webinarId}'
```

**Note:** `{webinarId}` is a placeholder. Replace it with a real value before sending the request.

Returns full webinar details including settings, recurrence, and registration info.

### Recordings API

#### List User Recordings

```bash
maton api '/zoom-admin/v2/users/{userId}/recordings?from=2026-04-01&to=2026-04-30&page_size=30'
```

**Note:** `{userId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Param | Type | Description |
|-------|------|-------------|
| `from` | string | Start date (`yyyy-mm-dd`). Max range: 1 month. Max past: 6 months |
| `to` | string | End date (`yyyy-mm-dd`) |
| `page_size` | integer | Max: 300 |
| `next_page_token` | string | Pagination token |
| `trash` | boolean | List trashed recordings |

**Response:**
```json
{
  "from": "2026-04-01",
  "to": "2026-04-30",
  "total_records": 0,
  "next_page_token": "",
  "meetings": [
    {
      "uuid": "...",
      "id": 12345678901,
      "host_id": "...",
      "topic": "Meeting Topic",
      "start_time": "2026-04-15T10:00:00Z",
      "duration": 45,
      "total_size": 52428800,
      "recording_count": 2,
      "recording_files": [
        {
          "id": "...",
          "file_type": "MP4",
          "file_extension": "MP4",
          "file_size": 41943040,
          "play_url": "https://...",
          "download_url": "https://...",
          "recording_type": "shared_screen_with_speaker_view",
          "status": "completed"
        }
      ]
    }
  ]
}
```

#### Get Meeting Recordings

```bash
maton api '/zoom-admin/v2/meetings/{meetingId}/recordings'
```

**Note:** `{meetingId}` is a placeholder. Replace it with a real value before sending the request.

Use the meeting ID or UUID. If the UUID starts with `/` or contains `//`, it must be double-URL-encoded.

**Query parameters:**

| Param | Type | Description |
|-------|------|-------------|
| `include_fields` | string | `download_access_token` for JWT download token |
| `ttl` | integer | Token TTL in seconds (0–604800, default: 172800) |

Returns the meeting's recording files with download URLs.

### Account API

#### Get Account Settings

```bash
maton api '/zoom-admin/v2/accounts/me/settings'
```

**Note:** Requires a paid Zoom plan.

#### Get Account Settings by ID

```bash
maton api '/zoom-admin/v2/accounts/{accountId}/settings'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

Use `me` for the connected account. Requires a paid Zoom plan.

**Query parameters:**

| Param | Type | Description |
|-------|------|-------------|
| `option` | string | `meeting_authentication`, `recording_authentication`, `security`, `meeting_security` |

Returns account-level settings grouped into sections: `security`, `schedule_meeting`, `in_meeting`, `recording`, `telephony`, `feature`, `chat`, etc.

### Meeting Types

| Type | Description |
|------|-------------|
| 1 | Instant meeting |
| 2 | Scheduled meeting |
| 3 | Recurring meeting (no fixed time) |
| 4 | PMI meeting |
| 8 | Recurring meeting (fixed time) |

### Pagination

Token-based pagination using `next_page_token` (15-minute expiry):

```bash
maton api '/zoom-admin/v2/users?page_size=30&next_page_token={token}'
```

**Note:** `{token}` is a placeholder. Replace it with a real value before sending the request.

### Notes

- Uses admin-level OAuth scopes for account-wide access - every call can reach any user in the account (see the warning at the top of this file)
- Use `me` to reference the authenticated user or account
- Meeting IDs are numeric; UUIDs are base64-encoded
- Double-encode UUID if it starts with `/` or contains `//`
- Webinar endpoints require Webinar add-on subscription
- Account Settings endpoint requires a paid Zoom plan
- Rate limits vary by plan; returns HTTP 429 when exceeded

### Resources

- [Zoom API Documentation](https://developers.zoom.us/docs/api/)
- [Zoom REST API Reference](https://developers.zoom.us/docs/api/rest/reference/zoom-api/methods/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
