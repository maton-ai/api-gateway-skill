# Acuity Scheduling Routing Reference

**App name:** `acuity-scheduling`
**Base URL proxied:** `acuityscheduling.com/api/v1`

## API Path Pattern

```
/acuity-scheduling/{resource}
```

The gateway automatically prepends `/api/v1` when proxying to Acuity.

## Common Endpoints

### Get Account Info
```bash
GET /acuity-scheduling/me
```

### List Appointments
```bash
GET /acuity-scheduling/appointments?max=100&minDate=2026-02-01
```

### Get Appointment
```bash
GET /acuity-scheduling/appointments/{id}
```

### Create Appointment
```bash
POST /acuity-scheduling/appointments
Content-Type: application/json

{
  "datetime": "2026-02-15T09:00",
  "appointmentTypeID": 123,
  "firstName": "John",
  "lastName": "Doe",
  "email": "john@example.com"
}
```

### Update Appointment
```bash
PUT /acuity-scheduling/appointments/{id}
Content-Type: application/json

{
  "firstName": "Jane",
  "lastName": "Smith"
}
```

### Cancel Appointment
```bash
PUT /acuity-scheduling/appointments/{id}/cancel
```

### Reschedule Appointment
```bash
PUT /acuity-scheduling/appointments/{id}/reschedule
Content-Type: application/json

{
  "datetime": "2026-02-20T10:00"
}
```

### List Calendars
```bash
GET /acuity-scheduling/calendars
```

### List Appointment Types
```bash
GET /acuity-scheduling/appointment-types
```

### Get Available Dates
```bash
GET /acuity-scheduling/availability/dates?month=2026-02&appointmentTypeID=123
```

### Get Available Times
```bash
GET /acuity-scheduling/availability/times?date=2026-02-04&appointmentTypeID=123
```

### List Clients
```bash
GET /acuity-scheduling/clients?search=John
```

### Create Client
```bash
POST /acuity-scheduling/clients
Content-Type: application/json

{
  "firstName": "John",
  "lastName": "Doe",
  "email": "john@example.com"
}
```

### List Blocks
```bash
GET /acuity-scheduling/blocks?calendarID=1234
```

### Create Block
```bash
POST /acuity-scheduling/blocks
Content-Type: application/json

{
  "start": "2026-02-15T12:00",
  "end": "2026-02-15T13:00",
  "calendarID": 1234
}
```

### Delete Block
```bash
DELETE /acuity-scheduling/blocks/{id}
```

### List Forms
```bash
GET /acuity-scheduling/forms
```

### List Labels
```bash
GET /acuity-scheduling/labels
```

## Notes

- Datetime values must be parseable by PHP's `strtotime()` function
- Timezones use IANA format (e.g., "America/New_York")
- Use `max` parameter to limit results (default: 100)
- Use `minDate` and `maxDate` for date-range filtering
- Client update/delete only works for clients with existing appointments
- Rescheduling requires the new datetime to be an available time slot

## Resources

- [Acuity Scheduling API Quick Start](https://developers.acuityscheduling.com/reference/quick-start)
- [Appointments API](https://developers.acuityscheduling.com/reference/get-appointments)
- [Availability API](https://developers.acuityscheduling.com/reference/get-availability-dates)
- [OAuth2 Documentation](https://developers.acuityscheduling.com/docs/oauth2)
