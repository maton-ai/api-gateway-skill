# Google Calendar

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `google-calendar`
**Upstream base URL:** `www.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://www.googleapis.com/calendar/v3/users/me/calendarList`
- Gateway: `https://api.maton.ai/google-calendar/calendar/v3/users/me/calendarList`

### Calendars API

#### List Calendars

```bash
maton google-calendar calendar list
```

Or with `maton api`:

```bash
maton api '/google-calendar/calendar/v3/users/me/calendarList'
```

#### Get Calendar

```bash
maton google-calendar calendar get primary
```

Or with `maton api`:

```bash
maton api '/google-calendar/calendar/v3/calendars/{calendarId}'
```

**Note:** `{calendarId}` is a placeholder. Replace it with a real value before sending the request.

### Events API

#### List Events

```bash
maton api '/google-calendar/calendar/v3/calendars/primary/events?maxResults=10&orderBy=startTime&singleEvents=true'
```

With time bounds:

```bash
maton google-calendar event list -c team@example.com --time-min 2026-06-17T00:00:00Z --time-max 2026-06-18T00:00:00Z
```

Or with `maton api`:

```bash
maton api '/google-calendar/calendar/v3/calendars/primary/events?timeMin=2024-01-01T00:00:00Z&timeMax=2024-12-31T23:59:59Z&singleEvents=true&orderBy=startTime'
```

#### Get Event

```bash
maton google-calendar event get {eventId}
```

Or with `maton api`:

```bash
maton api '/google-calendar/calendar/v3/calendars/primary/events/{eventId}'
```

**Note:** `{eventId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Event

```bash
maton google-calendar event create --summary 'Team Meeting' --description 'Weekly sync' --start 2024-01-15T10:00:00-08:00 --end 2024-01-15T11:00:00-08:00 --attendee attendee@example.com
```

Or with `maton api`:

```bash
maton api -X POST '/google-calendar/calendar/v3/calendars/primary/events' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "summary": "Team Meeting",
  "description": "Weekly sync",
  "start": {
    "dateTime": "2024-01-15T10:00:00",
    "timeZone": "America/Los_Angeles"
  },
  "end": {
    "dateTime": "2024-01-15T11:00:00",
    "timeZone": "America/Los_Angeles"
  },
  "attendees": [
    {"email": "attendee@example.com"}
  ]
}
JSON
```

#### Create All-Day Event

```bash
maton api -X POST '/google-calendar/calendar/v3/calendars/primary/events' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "summary": "All Day Event",
  "start": {"date": "2024-01-15"},
  "end": {"date": "2024-01-16"}
}
JSON
```

#### Update Event

```bash
maton google-calendar event update {eventId} --summary 'Updated Meeting Title' --start 2024-01-15T10:00:00Z --end 2024-01-15T11:00:00Z
```

Or with `maton api`:

```bash
maton api -X PUT '/google-calendar/calendar/v3/calendars/primary/events/{eventId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "summary": "Updated Meeting Title",
  "start": {"dateTime": "2024-01-15T10:00:00Z"},
  "end": {"dateTime": "2024-01-15T11:00:00Z"}
}
JSON
```

**Note:** `{eventId}` is a placeholder. Replace it with a real value before sending the request.

#### Patch Event

```bash
maton google-calendar event update {eventId} --summary 'New Title Only'
```

Or with `maton api`:

```bash
maton api -X PATCH '/google-calendar/calendar/v3/calendars/primary/events/{eventId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "summary": "New Title Only"
}
JSON
```

**Note:** `{eventId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Event

```bash
maton google-calendar event delete {eventId}
```

Or with `maton api`:

```bash
maton api '/google-calendar/calendar/v3/calendars/primary/events/{eventId}' -X DELETE
```

**Note:** `{eventId}` is a placeholder. Replace it with a real value before sending the request.

#### Quick Add Event

```bash
maton google-calendar event quick-add --text 'Meeting with John tomorrow at 3pm'
```

Or with `maton api`:

```bash
maton api -X POST '/google-calendar/calendar/v3/calendars/primary/events/quickAdd?text=Meeting+with+John+tomorrow+at+3pm'
```

### Free/Busy API

#### Query Free/Busy

```bash
maton google-calendar freebusy query --time-min 2024-01-15T00:00:00Z --time-max 2024-01-16T00:00:00Z
```

Or with `maton api`:

```bash
maton api -X POST '/google-calendar/calendar/v3/freeBusy' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "timeMin": "2024-01-15T00:00:00Z",
  "timeMax": "2024-01-16T00:00:00Z",
  "items": [{"id": "primary"}]
}
JSON
```

### Pagination

Google Calendar uses token-based pagination. The CLI automatically paginates with '--paginate'.

```bash
maton google-calendar event list --paginate
```

### Examples

```bash
# Show today's agenda (defaults to primary calendar when -c is omitted)
maton google-calendar agenda --today

# Filter with jq
maton google-calendar event list --json --jq '.items[] | {summary: .summary, start: .start.dateTime}'

# Extract specific fields
maton google-calendar calendar list --json --jq '.items[].summary'
```

### Notes

- Use `primary` as calendarId for the user's main calendar
- Times must be in RFC3339 format (e.g., `2024-01-15T10:00:00Z`)
- For recurring events, use `singleEvents=true` to expand instances
- `orderBy=startTime` requires `singleEvents=true`

### Resources

- [Google Calendar API Overview](https://developers.google.com/calendar/api/v3/reference)
- [List Calendars](https://developers.google.com/workspace/calendar/api/v3/reference/calendarList/list)
- [Get Calendar](https://developers.google.com/workspace/calendar/api/v3/reference/calendarList/get)
- [List Events](https://developers.google.com/workspace/calendar/api/v3/reference/events/list)
- [Get Event](https://developers.google.com/workspace/calendar/api/v3/reference/events/get)
- [Insert Event](https://developers.google.com/workspace/calendar/api/v3/reference/events/insert)
- [Update Event](https://developers.google.com/workspace/calendar/api/v3/reference/events/update)
- [Patch Event](https://developers.google.com/workspace/calendar/api/v3/reference/events/patch)
- [Delete Event](https://developers.google.com/workspace/calendar/api/v3/reference/events/delete)
- [Quick Add Event](https://developers.google.com/workspace/calendar/api/v3/reference/events/quickAdd)
- [Free/Busy Query](https://developers.google.com/workspace/calendar/api/v3/reference/freebusy/query)
- [Maton CLI Manual](https://cli.maton.ai/manual)
