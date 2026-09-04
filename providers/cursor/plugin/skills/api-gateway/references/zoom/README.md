# Zoom Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `zoom`
**Base URL proxied:** `api.zoom.us`

## API Path Pattern

```
/zoom/v2/{resource}
```

## Common Endpoints

### Users

#### Get Current User
```bash
maton api '/zoom/v2/users/me'
```

### Meetings

#### List User's Meetings
```bash
maton api '/zoom/v2/users/me/meetings'
maton api '/zoom/v2/users/{userId}/meetings'
maton api '/zoom/v2/users/me/meetings?type=scheduled&page_size=50'
```

#### Get Upcoming Meetings
```bash
maton api '/zoom/v2/users/me/upcoming_meetings'
```

#### Create Meeting
```bash
maton api -X POST '/zoom/v2/users/me/meetings' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "topic": "Team Meeting",
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
EOF
```

#### Get Meeting
```bash
maton api '/zoom/v2/meetings/{meetingId}'
```

#### Update Meeting
```bash
maton api -X PATCH '/zoom/v2/meetings/{meetingId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "topic": "Updated Title",
  "duration": 45
}
EOF
```

#### Delete Meeting
```bash
maton api -X DELETE '/zoom/v2/meetings/{meetingId}'
```

### Recordings

#### List User's Recordings
```bash
maton api '/zoom/v2/users/me/recordings?from=2026-04-01&to=2026-04-10'
```

#### Get Meeting Recordings
```bash
maton api '/zoom/v2/meetings/{meetingId}/recordings'
```

#### Delete Meeting Recordings
```bash
maton api -X DELETE '/zoom/v2/meetings/{meetingId}/recordings'
```

### Webinars

**Note:** Requires Webinar add-on plan.

#### List User's Webinars
```bash
maton api '/zoom/v2/users/me/webinars'
```

#### Create Webinar
```bash
maton api -X POST '/zoom/v2/users/{userId}/webinars' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "topic": "Product Launch",
  "type": 5,
  "start_time": "2026-05-01T10:00:00Z",
  "duration": 90
}
EOF
```

#### Get/Update/Delete Webinar
```bash
maton api '/zoom/v2/webinars/{webinarId}'
maton api -X PATCH '/zoom/v2/webinars/{webinarId}'
maton api -X DELETE '/zoom/v2/webinars/{webinarId}'
```

### Meeting Registrants

#### List Registrants
```bash
maton api '/zoom/v2/meetings/{meetingId}/registrants'
```

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

### Past Meetings

#### Get Past Meeting Details
```bash
maton api '/zoom/v2/past_meetings/{meetingUUID}'
```

#### List Past Meeting Participants
```bash
maton api '/zoom/v2/past_meetings/{meetingUUID}/participants'
```

## Meeting Types

| Type | Description |
|------|-------------|
| 1 | Instant meeting |
| 2 | Scheduled meeting |
| 3 | Recurring meeting (no fixed time) |
| 8 | Recurring meeting (fixed time) |

## Webinar Types

| Type | Description |
|------|-------------|
| 5 | Scheduled webinar |
| 6 | Recurring webinar (no fixed time) |
| 9 | Recurring webinar (fixed time) |

## Pagination

Cursor-based pagination using `next_page_token`:

```bash
maton api '/zoom/v2/users/me/meetings?page_size=50&next_page_token={token}'
```

Response includes:
```json
{
  "page_size": 50,
  "total_records": 150,
  "next_page_token": "abc123...",
  "meetings": [...]
}
```

## Notes

- Use `me` to reference the authenticated user
- Meeting IDs are numeric; UUIDs are base64-encoded
- Double-encode UUID if it contains `/` or `//`
- Webinar endpoints require Webinar add-on subscription
- Some endpoints require admin scopes
- Rate limits: varies by plan, returns HTTP 429 when exceeded

## Resources

- [Zoom API Documentation](https://developers.zoom.us/docs/api/)
- [Zoom REST API Reference](https://developers.zoom.us/docs/api/rest/reference/zoom-api/methods/)
