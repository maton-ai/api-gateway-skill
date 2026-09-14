# Zoho Calendar

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `zoho-calendar`
**Upstream base URL:** `calendar.zoho.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://calendar.zoho.com/api/v1/calendars`
- Gateway: `https://api.maton.ai/zoho-calendar/api/v1/calendars`

### Calendars API

#### List Calendars

```bash
maton api '/zoho-calendar/api/v1/calendars'
```

**Response:**
```json
{
  "calendars": [
    {
      "uid": "fda9b0b4ad834257b622cb3dc3555727",
      "name": "My Calendar",
      "color": "#8cbf40",
      "textcolor": "#FFFFFF",
      "timezone": "PST",
      "isdefault": true,
      "category": "own",
      "privilege": "owner"
    }
  ]
}
```

#### Create Calendar

```bash
maton api -X POST '/zoho-calendar/api/v1/calendars?calendarData={json}'
```

**Note:** `{json}` is a placeholder. Replace it with a real value before sending the request.

**Request body:**
- `name` (required) - Calendar name (max 50 characters)
- `color` (required) - Hex color code (e.g., `#FF5733`)
- `textcolor` (optional) - Text color hex code
- `description` (optional) - Calendar description (max 1000 characters)
- `timezone` (optional) - Calendar timezone
- `include_infreebusy` (optional) - Show as Busy/Free (boolean)
- `public` (optional) - Visibility level (`disable`, `freebusy`, or `view`)

**Example:**

```bash
maton api -X POST '/zoho-calendar/api/v1/calendars?calendarData={urllib.parse.quote(json.dumps(calendarData))}'
```

**Response:**
```json
{
  "calendars": [
    {
      "uid": "86fb9745076e4672ae4324f05e1f5393",
      "name": "Work Calendar",
      "color": "#FF5733",
      "textcolor": "#FFFFFF"
    }
  ]
}
```

#### Delete Calendar

```bash
maton api '/zoho-calendar/api/v1/calendars/{calendar_uid}' -X DELETE
```

**Note:** `{calendar_uid}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "calendars": [
    {
      "uid": "86fb9745076e4672ae4324f05e1f5393",
      "calstatus": "deleted"
    }
  ]
}
```

### Events API

#### List Events

```bash
maton api '/zoho-calendar/api/v1/calendars/{calendar_uid}/events?range={json}'
```

**Note:** `{calendar_uid}` and `{json}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `range` | JSON object | **Required.** Start and end dates in format `{"start":"yyyyMMdd","end":"yyyyMMdd"}`. Max 31-day span. |
| `byinstance` | boolean | If true, recurring event instances are returned separately |
| `timezone` | string | Timezone for datetime values |

**Example:**

```bash
maton api '/zoho-calendar/api/v1/calendars/{calendar_uid}/events?range={urllib.parse.quote(range_param)}'
```

**Note:** `{calendar_uid}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "events": [
    {
      "uid": "c63e8b9fcb3e48c2a00b16729932d636@zoho.com",
      "title": "Team Meeting",
      "dateandtime": {
        "timezone": "America/Los_Angeles",
        "start": "20260206T100000-0800",
        "end": "20260206T110000-0800"
      },
      "isallday": false,
      "etag": "1770368451507",
      "organizer": "user@example.com"
    }
  ]
}
```

#### Get Event Details

```bash
maton api '/zoho-calendar/api/v1/calendars/{calendar_uid}/events/{event_uid}'
```

**Note:** `{calendar_uid}` and `{event_uid}` are placeholders. Replace each of them with real values before sending the request.

**Example:**

```bash
maton api '/zoho-calendar/api/v1/calendars/fda9b0b4ad834257b622cb3dc3555727/events/c63e8b9fcb3e48c2a00b16729932d636@zoho.com'
```

#### Create Event

```bash
maton api -X POST '/zoho-calendar/api/v1/calendars/{calendar_uid}/events?eventdata={json}'
```

**Note:** `{calendar_uid}` and `{json}` are placeholders. Replace each of them with real values before sending the request.

**Request body (in eventdata):**
- `dateandtime` (required) - Object with `start`, `end`, and optionally `timezone`
  - Format: `yyyyMMdd'T'HHmmss'Z'` (GMT) for timed events
  - Format: `yyyyMMdd` for all-day events
- `title` (optional) - Event name
- `description` (optional) - Event details (max 10,000 characters)
- `location` (optional) - Event location (max 255 characters)
- `isallday` (optional) - Boolean for all-day events
- `isprivate` (optional) - Boolean to hide details from non-delegates
- `color` (optional) - Hex color code
- `attendees` (optional) - Array of attendee objects
- `reminders` (optional) - Array of reminder objects
- `rrule` (optional) - Recurrence rule string (e.g., `FREQ=DAILY;COUNT=5`)

**Example:**

Zoho takes the event as URL-encoded JSON in the `eventdata` query parameter, so encode the payload first and pass it to `maton api`:

```bash
EVENTDATA='{"title":"Team Meeting","dateandtime":{"timezone":"America/Los_Angeles","start":"20260220T170000Z","end":"20260220T180000Z"},"description":"Weekly team sync","location":"Conference Room A"}'

maton api -X POST "/zoho-calendar/api/v1/calendars/{calendar_uid}/events?eventdata=$(printf '%s' "$EVENTDATA" \
  | python3 -c 'import sys,urllib.parse; print(urllib.parse.quote(sys.stdin.read()), end="")')"
```

**Note:** `{calendar_uid}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "events": [
    {
      "uid": "c63e8b9fcb3e48c2a00b16729932d636@zoho.com",
      "title": "Team Meeting",
      "dateandtime": {
        "timezone": "America/Los_Angeles",
        "start": "20260206T100000-0800",
        "end": "20260206T110000-0800"
      },
      "etag": "1770368451507",
      "estatus": "added"
    }
  ]
}
```

#### Update Event

```bash
maton api -X PUT '/zoho-calendar/api/v1/calendars/{calendar_uid}/events/{event_uid}?eventdata={json}'
```

**Note:** `{calendar_uid}`, `{event_uid}` and `{json}` are placeholders. Replace each of them with real values before sending the request.

**Request body (in eventdata):**
- `dateandtime` (required) - Start and end times
- `etag` (required) - Current etag value (from Get Event Details)
- `title` (optional) - Event name
- `description` (optional) - Event details (max 10,000 characters)
- `location` (optional) - Event location (max 255 characters)
- `isallday` (optional) - Boolean for all-day events
- `isprivate` (optional) - Boolean to hide details from non-delegates
- `color` (optional) - Hex color code
- `attendees` (optional) - Array of attendee objects
- `reminders` (optional) - Array of reminder objects
- `rrule` (optional) - Recurrence rule string (e.g., `FREQ=DAILY;COUNT=5`)

**Example:**

```bash
EVENTDATA='{"title":"Updated Team Meeting","dateandtime":{"timezone":"America/Los_Angeles","start":"20260220T180000Z","end":"20260220T190000Z"},"etag":1770368451507}'

maton api -X PUT "/zoho-calendar/api/v1/calendars/{calendar_uid}/events/{event_uid}?eventdata=$(printf '%s' "$EVENTDATA" \
  | python3 -c 'import sys,urllib.parse; print(urllib.parse.quote(sys.stdin.read()), end="")')"
```

**Note:** `{calendar_uid}` and `{event_uid}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Event

```bash
maton api '/zoho-calendar/api/v1/calendars/{calendar_uid}/events/{event_uid}' -X DELETE
```

**Note:** `{calendar_uid}` and `{event_uid}` are placeholders. Replace each of them with real values before sending the request.

**Required Header:**
- `etag` - Current etag value of the event

**Example:**

```bash
maton api '/zoho-calendar/api/v1/calendars/fda9b0b4ad834257b622cb3dc3555727/events/c63e8b9fcb3e48c2a00b16729932d636@zoho.com' -X DELETE -H 'etag: 1770368451507'
```

**Response:**
```json
{
  "events": [
    {
      "uid": "c63e8b9fcb3e48c2a00b16729932d636@zoho.com",
      "estatus": "deleted",
      "caluid": "fda9b0b4ad834257b622cb3dc3555727"
    }
  ]
}
```

### Event Data Format

#### Create/Update Event

```json
{
  "title": "Meeting Title",
  "dateandtime": {
    "timezone": "America/Los_Angeles",
    "start": "yyyyMMdd'T'HHmmss'Z'",
    "end": "yyyyMMdd'T'HHmmss'Z'"
  },
  "description": "Event description",
  "location": "Meeting room",
  "isallday": false,
  "attendees": [
    {
      "email": "user@example.com",
      "permission": 1,
      "attendance": 1
    }
  ],
  "reminders": [
    {
      "action": "popup",
      "minutes": 30
    }
  ],
  "rrule": "FREQ=DAILY;COUNT=5"
}
```

#### Update Event (etag required)

```json
{
  "title": "Updated Title",
  "dateandtime": {...},
  "etag": 1770368451507
}
```

### Calendar Data Format

```json
{
  "name": "Calendar Name",
  "color": "#FF5733",
  "textcolor": "#FFFFFF",
  "description": "Calendar description"
}
```

### Notes

- Event and calendar data is passed as JSON in query parameters (`eventdata`, `calendarData`)
- Date/time format: `yyyyMMdd'T'HHmmss'Z'` (GMT) for timed events, `yyyyMMdd` for all-day
- The `range` parameter for listing events cannot exceed 31 days
- **IMPORTANT:** For delete operations, `etag` must be passed as an HTTP header, not a query parameter
- The `etag` is required for update and delete operations - always get the latest etag before modifying
- Permission levels for attendees: 0 (Guest), 1 (View), 2 (Invite), 3 (Edit)
- Attendance: 0 (Non-participant), 1 (Required), 2 (Optional)
- Reminder actions: `email`, `popup`, `notification`

### Resources

- [Zoho Calendar API Introduction](https://www.zoho.com/calendar/help/api/introduction.html)
- [Events API](https://www.zoho.com/calendar/help/api/events-api.html)
- [Calendars API](https://www.zoho.com/calendar/help/api/calendars-api.html)
- [Maton CLI Manual](https://cli.maton.ai/manual)
