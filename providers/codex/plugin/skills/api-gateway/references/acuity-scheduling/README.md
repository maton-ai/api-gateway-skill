# Acuity Scheduling

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `acuity-scheduling`
**Upstream base URL:** `acuityscheduling.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://acuityscheduling.com/api/v1/me`
- Gateway: `https://api.maton.ai/acuity-scheduling/api/v1/me`

### User Info API

#### Get Account Info

```bash
maton api '/acuity-scheduling/api/v1/me'
```

**Response:**
```json
{
  "id": 12345,
  "email": "user@example.com",
  "timezone": "America/Los_Angeles",
  "name": "My Business",
  "schedulingPage": "https://app.acuityscheduling.com/schedule.php?owner=12345",
  "plan": "Professional",
  "currency": "USD"
}
```

### Appointments API

#### List Appointments

```bash
maton api '/acuity-scheduling/api/v1/appointments'
```

**Query parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `max` | integer | Maximum results (default: 100) |
| `minDate` | date | Appointments on or after this date |
| `maxDate` | date | Appointments on or before this date |
| `calendarID` | integer | Filter by calendar |
| `appointmentTypeID` | integer | Filter by appointment type |
| `canceled` | boolean | Include canceled appointments (default: false) |
| `firstName` | string | Filter by client first name |
| `lastName` | string | Filter by client last name |
| `email` | string | Filter by client email |
| `excludeForms` | boolean | Omit intake forms for faster response |
| `direction` | string | Sort order: ASC or DESC (default: DESC) |

**Example:**
```bash
maton api '/acuity-scheduling/api/v1/appointments?max=10&minDate=2026-02-01'
```

**Response:**
```json
[
  {
    "id": 1630290133,
    "firstName": "Jane",
    "lastName": "McTest",
    "phone": "1235550101",
    "email": "jane.mctest@example.com",
    "date": "February 4, 2026",
    "time": "9:30am",
    "endTime": "10:20am",
    "datetime": "2026-02-04T09:30:00-0800",
    "type": "Consultation",
    "appointmentTypeID": 88791369,
    "duration": "50",
    "calendar": "Chris",
    "calendarID": 13499175,
    "canceled": false,
    "confirmationPage": "https://app.acuityscheduling.com/schedule.php?..."
  }
]
```

#### Get Appointment

```bash
maton api '/acuity-scheduling/api/v1/appointments/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Appointment

```bash
maton api -X POST '/acuity-scheduling/api/v1/appointments' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "datetime": "2026-02-15T09:00",
  "appointmentTypeID": 123,
  "firstName": "John",
  "lastName": "Doe",
  "email": "john.doe@example.com",
  "phone": "555-123-4567",
  "timezone": "America/New_York"
}
JSON
```

**Request body:**
- `datetime` (required) - Date and time (parseable by PHP's strtotime)
- `appointmentTypeID` (required) - Appointment type ID
- `firstName` (required) - Client's first name
- `lastName` (required) - Client's last name
- `email` (required) - Client's email
- `phone` (optional) - Client phone number
- `calendarID` (optional) - Specific calendar (auto-selected if omitted)
- `timezone` (optional) - Client's timezone
- `certificate` (optional) - Package or coupon code
- `notes` (optional) - Admin notes
- `addonIDs` (optional) - Array of addon IDs
- `fields` (optional) - Array of form field values

**Example:**
```bash
maton api -X POST '/acuity-scheduling/api/v1/appointments' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "datetime": "2026-02-15T09:00",
  "appointmentTypeID": 123,
  "firstName": "John",
  "lastName": "Doe",
  "email": "john.doe@example.com",
  "phone": "555-123-4567",
  "timezone": "America/New_York"
}
JSON
```

#### Update Appointment

```bash
maton api -X PUT '/acuity-scheduling/api/v1/appointments/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "firstName": "Jane",
  "lastName": "Smith",
  "email": "jane.smith@example.com"
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Cancel Appointment

```bash
maton api -X PUT '/acuity-scheduling/api/v1/appointments/{id}/cancel'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

Returns the canceled appointment with `canceled: true`.

#### Reschedule Appointment

```bash
maton api -X PUT '/acuity-scheduling/api/v1/appointments/{id}/reschedule' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "datetime": "2026-02-20T10:00"
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

**Note:** The new datetime must be an available time slot.

### Calendars API

#### List Calendars

```bash
maton api '/acuity-scheduling/api/v1/calendars'
```

**Response:**
```json
[
  {
    "id": 13499175,
    "name": "Chris",
    "email": "",
    "replyTo": "chris@example.com",
    "description": "",
    "location": "",
    "timezone": "America/Los_Angeles"
  }
]
```

### Appointment Types API

#### List Appointment Types

```bash
maton api '/acuity-scheduling/api/v1/appointment-types'
```

**Query parameters:**
- `includeDeleted` (boolean) - Include deleted types

**Response:**
```json
[
  {
    "id": 88791369,
    "name": "Consultation",
    "active": true,
    "description": "",
    "duration": 50,
    "price": "45.00",
    "category": "",
    "color": "#ED7087",
    "private": false,
    "type": "service",
    "calendarIDs": [13499175],
    "schedulingUrl": "https://app.acuityscheduling.com/schedule.php?..."
  }
]
```

### Availability API

#### Get Available Dates

```bash
maton api '/acuity-scheduling/api/v1/availability/dates?month=2026-02&appointmentTypeID=123'
```

**Query parameters:**
- `month` (required) - Month to check (e.g., "2026-02")
- `appointmentTypeID` (required) - Appointment type ID
- `calendarID` (optional) - Specific calendar
- `timezone` (optional) - Timezone for results (e.g., "America/New_York")

**Response:**
```json
[
  {"date": "2026-02-09"},
  {"date": "2026-02-10"},
  {"date": "2026-02-11"}
]
```

#### Get Available Times

```bash
maton api '/acuity-scheduling/api/v1/availability/times?date=2026-02-10&appointmentTypeID=123'
```

**Query parameters:**
- `date` (required) - Date to check
- `appointmentTypeID` (required) - Appointment type ID
- `calendarID` (optional) - Specific calendar
- `timezone` (optional) - Timezone for results

**Response:**
```json
[
  {"time": "2026-02-10T09:00:00-0800", "slotsAvailable": 1},
  {"time": "2026-02-10T09:50:00-0800", "slotsAvailable": 1},
  {"time": "2026-02-10T10:40:00-0800", "slotsAvailable": 1}
]
```

### Clients API

#### List Clients

```bash
maton api '/acuity-scheduling/api/v1/clients'
```

**Query parameters:**
- `search` - Filter by first name, last name, or phone

**Example:**
```bash
maton api '/acuity-scheduling/api/v1/clients?search=John'
```

**Response:**
```json
[
  {
    "firstName": "Jane",
    "lastName": "McTest",
    "email": "jane.mctest@example.com",
    "phone": "(123) 555-0101",
    "notes": ""
  }
]
```

#### Create Client

```bash
maton api -X POST '/acuity-scheduling/api/v1/clients' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "firstName": "John",
  "lastName": "Doe",
  "email": "john@example.com",
  "phone": "555-123-4567"
}
JSON
```

#### Update Client

```bash
maton api -X PUT '/acuity-scheduling/api/v1/clients' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "firstName": "John",
  "lastName": "Doe",
  "email": "john.updated@example.com"
}
JSON
```

**Note:** Client update/delete only works for clients with existing appointments.

#### Delete Client

```bash
maton api '/acuity-scheduling/api/v1/clients' -X DELETE -H 'Content-Type: application/json' --input - <<'JSON'
{
  "firstName": "John",
  "lastName": "Doe"
}
JSON
```

### Blocks API

#### List Blocks

```bash
maton api '/acuity-scheduling/api/v1/blocks'
```

**Query parameters:**
- `max` - Maximum results (default: 100)
- `minDate` - Blocks on or after this date
- `maxDate` - Blocks on or before this date
- `calendarID` - Filter by calendar

#### Get Block

```bash
maton api '/acuity-scheduling/api/v1/blocks/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Block

```bash
maton api -X POST '/acuity-scheduling/api/v1/blocks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "start": "2026-02-15T12:00",
  "end": "2026-02-15T13:00",
  "calendarID": 1234,
  "notes": "Lunch break"
}
JSON
```

**Response:**
```json
{
  "id": 9589304654,
  "calendarID": 13499175,
  "start": "2026-02-15T12:00:00-0800",
  "end": "2026-02-15T13:00:00-0800",
  "notes": "Lunch break",
  "description": "Sunday, February 15, 2026 12:00pm - 1:00pm"
}
```

#### Delete Block

```bash
maton api '/acuity-scheduling/api/v1/blocks/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content on success.

### Forms API

#### List Forms

```bash
maton api '/acuity-scheduling/api/v1/forms'
```

**Response:**
```json
[
  {
    "id": 123,
    "name": "Client Intake Form",
    "appointmentTypeIDs": [456, 789],
    "fields": [
      {
        "id": 1,
        "name": "How did you hear about us?",
        "type": "dropdown",
        "options": ["Google", "Friend", "Social Media"],
        "required": true
      }
    ]
  }
]
```

### Labels API

#### List Labels

```bash
maton api '/acuity-scheduling/api/v1/labels'
```

**Response:**
```json
[
  {"id": 23116714, "name": "Checked In", "color": "green"},
  {"id": 23116715, "name": "Completed", "color": "pink"},
  {"id": 23116713, "name": "Confirmed", "color": "yellow"}
]
```

### Pagination

Acuity Scheduling uses the `max` parameter to limit results. Use `minDate` and `maxDate` to paginate through date ranges:

```bash
# First page
maton api '/acuity-scheduling/api/v1/appointments?max=100&minDate=2026-01-01&maxDate=2026-01-31'

# Next page
maton api '/acuity-scheduling/api/v1/appointments?max=100&minDate=2026-02-01&maxDate=2026-02-28'
```

### Notes

- Datetime values must be parseable by PHP's `strtotime()` function
- Timezones use IANA format (e.g., "America/New_York")
- Use `max` parameter to limit results (default: 100)
- Use `minDate` and `maxDate` for date-range filtering
- Client update/delete only works for clients with existing appointments
- Rescheduling requires the new datetime to be an available time slot
- Use `excludeForms=true` for faster appointment list responses

### Resources

- [Acuity Scheduling API Quick Start](https://developers.acuityscheduling.com/reference/quick-start)
- [Appointments API](https://developers.acuityscheduling.com/reference/get-appointments)
- [Availability API](https://developers.acuityscheduling.com/reference/get-availability-dates)
- [Calendars API](https://developers.acuityscheduling.com/reference/get-calendars)
- [Clients API](https://developers.acuityscheduling.com/reference/clients)
- [OAuth2 Documentation](https://developers.acuityscheduling.com/docs/oauth2)
- [Maton CLI Manual](https://cli.maton.ai/manual)
