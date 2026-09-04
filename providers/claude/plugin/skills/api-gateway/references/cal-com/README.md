# Cal.com Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `cal-com`
**Base URL proxied:** `api.cal.com`

## API Path Pattern

```
/cal-com/v2/{resource}
```

## Common Endpoints

### User Profile

#### Get Profile
```bash
maton api '/cal-com/v2/me'
```

#### Update Profile
```bash
maton api -X PATCH '/cal-com/v2/me'
```

### Event Types

#### List Event Types
```bash
maton api '/cal-com/v2/event-types'
```

#### Get Event Type
```bash
maton api '/cal-com/v2/event-types/{eventTypeId}'
```

#### Create Event Type
```bash
maton api -X POST '/cal-com/v2/event-types'
```

#### Update Event Type
```bash
maton api -X PATCH '/cal-com/v2/event-types/{eventTypeId}'
```

#### Delete Event Type
```bash
maton api -X DELETE '/cal-com/v2/event-types/{eventTypeId}'
```

### Event Type Webhooks

#### List Webhooks
```bash
maton api '/cal-com/v2/event-types/{eventTypeId}/webhooks'
```

#### Create Webhook
```bash
maton api -X POST '/cal-com/v2/event-types/{eventTypeId}/webhooks'
```

#### Get Webhook
```bash
maton api '/cal-com/v2/event-types/{eventTypeId}/webhooks/{webhookId}'
```

#### Update Webhook
```bash
maton api -X PATCH '/cal-com/v2/event-types/{eventTypeId}/webhooks/{webhookId}'
```

#### Delete Webhook
```bash
maton api -X DELETE '/cal-com/v2/event-types/{eventTypeId}/webhooks/{webhookId}'
```

### Bookings

#### List Bookings
```bash
maton api '/cal-com/v2/bookings'
maton api '/cal-com/v2/bookings?status=upcoming'
maton api '/cal-com/v2/bookings?status=past'
maton api '/cal-com/v2/bookings?status=cancelled'
maton api '/cal-com/v2/bookings?take=10'
```

#### Get Booking
```bash
maton api '/cal-com/v2/bookings/{bookingUid}'
```

#### Create Booking
```bash
maton api -X POST '/cal-com/v2/bookings'
```

#### Cancel Booking
```bash
maton api -X POST '/cal-com/v2/bookings/{bookingUid}/cancel'
```

### Schedules

#### Get Default Schedule
```bash
maton api '/cal-com/v2/schedules/default'
```

#### Get Schedule
```bash
maton api '/cal-com/v2/schedules/{scheduleId}'
```

#### Create Schedule
```bash
maton api -X POST '/cal-com/v2/schedules'
```

#### Update Schedule
```bash
maton api -X PATCH '/cal-com/v2/schedules/{scheduleId}'
```

#### Delete Schedule
```bash
maton api -X DELETE '/cal-com/v2/schedules/{scheduleId}'
```

### Availability Slots

#### Get Available Slots
```bash
maton api '/cal-com/v2/slots/available?eventTypeId={id}&startTime={iso8601}&endTime={iso8601}'
```

#### Reserve Slot
```bash
maton api -X POST '/cal-com/v2/slots/reserve'
```

### Calendars

#### List Connected Calendars
```bash
maton api '/cal-com/v2/calendars'
```

### Conferencing

#### List Conferencing Apps
```bash
maton api '/cal-com/v2/conferencing'
```

#### Get Default Conferencing App
```bash
maton api '/cal-com/v2/conferencing/default'
```

### Webhooks (User-level)

#### List Webhooks
```bash
maton api '/cal-com/v2/webhooks'
```

#### Create Webhook
```bash
maton api -X POST '/cal-com/v2/webhooks'
```

#### Get Webhook
```bash
maton api '/cal-com/v2/webhooks/{webhookId}'
```

#### Update Webhook
```bash
maton api -X PATCH '/cal-com/v2/webhooks/{webhookId}'
```

#### Delete Webhook
```bash
maton api -X DELETE '/cal-com/v2/webhooks/{webhookId}'
```

### Teams

#### List Teams
```bash
maton api '/cal-com/v2/teams'
```

### Verified Resources

#### List Verified Emails
```bash
maton api '/cal-com/v2/verified-resources/emails'
```

## Notes

- All API endpoints are v2
- All times are in UTC (ISO 8601 format)
- Booking creation requires an available slot - check `/v2/slots/available` first
- Required fields for booking: `eventTypeId`, `start`, `timeZone`, `language`, `responses.name`, `responses.email`
- `GET /v2/schedules` may return 500 errors; use `GET /v2/schedules/{id}` instead
- Event type creation requires: `title`, `slug`, `length` (in minutes)

## Resources

- [Cal.com API Documentation](https://cal.com/docs/api-reference/v2/introduction)
