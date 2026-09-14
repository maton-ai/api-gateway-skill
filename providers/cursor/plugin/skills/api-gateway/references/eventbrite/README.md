# Eventbrite

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `eventbrite`
**Upstream base URL:** `www.eventbriteapi.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://www.eventbriteapi.com/v3/users/me/`
- Gateway: `https://api.maton.ai/eventbrite/v3/users/me/`

**Important:** All Eventbrite API paths should end with a trailing slash.

### User API

#### Get Current User

```bash
maton api '/eventbrite/v3/users/me/'
```

**Response:**
```json
{
  "emails": [{"email": "user@example.com", "verified": true, "primary": true}],
  "id": "1234567890",
  "name": "John Doe",
  "first_name": "John",
  "last_name": "Doe",
  "is_public": false,
  "image_id": null
}
```

#### List User Organizations

```bash
maton api '/eventbrite/v3/users/me/organizations/'
```

#### List User Orders

```bash
maton api '/eventbrite/v3/users/me/orders/'
```

### Organization API

#### List Organization Events

```bash
maton api '/eventbrite/v3/organizations/{organization_id}/events/'
```

**Note:** `{organization_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `status` - Filter: `draft`, `live`, `started`, `ended`, `completed`, `canceled`
- `order_by` - Sort: `start_asc`, `start_desc`, `created_asc`, `created_desc`
- `time_filter` - Filter: `current_future`, `past`

#### List Organization Venues

```bash
maton api '/eventbrite/v3/organizations/{organization_id}/venues/'
```

**Note:** `{organization_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Venue

```bash
maton api -X POST '/eventbrite/v3/organizations/{organization_id}/venues/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "venue": {
    "name": "Conference Center",
    "address": {
      "address_1": "123 Main St",
      "city": "San Francisco",
      "region": "CA",
      "postal_code": "94105",
      "country": "US"
    }
  }
}
JSON
```

**Note:** `{organization_id}` is a placeholder. Replace it with a real value before sending the request.

### Event API

#### Get Event

Include related data by using the `expand` parameter:

```bash
maton api '/eventbrite/v3/events/{event_id}/?expand=venue,ticket_classes,category'
```

**Note:** `{event_id}` is a placeholder. Replace it with a real value before sending the request.

**Common expansions:**
- `venue` - Include venue details
- `ticket_classes` - Include ticket information
- `category` - Include category details
- `subcategory` - Include subcategory details
- `format` - Include format details
- `organizer` - Include organizer information

#### Expansions

```bash
maton api '/eventbrite/v3/events/{event_id}/?expand=venue,ticket_classes,category'
```

**Note:** `{event_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Event

Events must be created under an organization:

```bash
maton api -X POST '/eventbrite/v3/organizations/{organization_id}/events/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "event": {
    "name": {"html": "My Event"},
    "description": {"html": "<p>Event description</p>"},
    "start": {
      "timezone": "America/Los_Angeles",
      "utc": "2026-03-01T19:00:00Z"
    },
    "end": {
      "timezone": "America/Los_Angeles",
      "utc": "2026-03-01T22:00:00Z"
    },
    "currency": "USD",
    "online_event": false,
    "listed": true,
    "shareable": true,
    "capacity": 100,
    "category_id": "103",
    "format_id": "1"
  }
}
JSON
```

**Note:** `{organization_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Event

```bash
maton api -X POST '/eventbrite/v3/events/{event_id}/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "event": {
    "name": {"html": "Updated Event Name"},
    "capacity": 200
  }
}
JSON
```

**Note:** `{event_id}` is a placeholder. Replace it with a real value before sending the request.

#### Publish Event

```bash
maton api -X POST '/eventbrite/v3/events/{event_id}/publish/'
```

**Note:** `{event_id}` is a placeholder. Replace it with a real value before sending the request.

#### Unpublish Event

```bash
maton api -X POST '/eventbrite/v3/events/{event_id}/unpublish/'
```

**Note:** `{event_id}` is a placeholder. Replace it with a real value before sending the request.

#### Cancel Event

```bash
maton api -X POST '/eventbrite/v3/events/{event_id}/cancel/'
```

**Note:** `{event_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Event

```bash
maton api '/eventbrite/v3/events/{event_id}/' -X DELETE
```

**Note:** `{event_id}` is a placeholder. Replace it with a real value before sending the request.

### Ticket Classes API

#### List Ticket Classes

```bash
maton api '/eventbrite/v3/events/{event_id}/ticket_classes/'
```

**Note:** `{event_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Ticket Class

```bash
maton api -X POST '/eventbrite/v3/events/{event_id}/ticket_classes/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "ticket_class": {
    "name": "General Admission",
    "description": "Standard entry ticket",
    "quantity_total": 100,
    "cost": "USD,2500",
    "sales_start": "2026-01-01T00:00:00Z",
    "sales_end": "2026-02-28T23:59:59Z",
    "minimum_quantity": 1,
    "maximum_quantity": 10
  }
}
JSON
```

**Note:** `{event_id}` is a placeholder. Replace it with a real value before sending the request.

For free tickets, omit the `cost` field or set `free: true`.

#### Update Ticket Class

```bash
maton api -X POST '/eventbrite/v3/events/{event_id}/ticket_classes/{ticket_class_id}/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "ticket_class": {
    "quantity_total": 150
  }
}
JSON
```

**Note:** `{event_id}` and `{ticket_class_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Ticket Class

```bash
maton api '/eventbrite/v3/events/{event_id}/ticket_classes/{ticket_class_id}/' -X DELETE
```

**Note:** `{event_id}` and `{ticket_class_id}` are placeholders. Replace each of them with real values before sending the request.

### Attendees API

#### List Event Attendees

```bash
maton api '/eventbrite/v3/events/{event_id}/attendees/'
```

**Note:** `{event_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `status` - Filter by status: `attending`, `not_attending`, `unpaid`
- `changed_since` - ISO 8601 timestamp to get attendees changed after

#### Get Attendee

```bash
maton api '/eventbrite/v3/events/{event_id}/attendees/{attendee_id}/'
```

**Note:** `{event_id}` and `{attendee_id}` are placeholders. Replace each of them with real values before sending the request.

### Event Orders API

#### List Event Orders

```bash
maton api '/eventbrite/v3/events/{event_id}/orders/'
```

**Note:** `{event_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `status` - Filter by status: `active`, `inactive`, `all`
- `changed_since` - ISO 8601 timestamp

#### Get Order

```bash
maton api '/eventbrite/v3/orders/{order_id}/'
```

**Note:** `{order_id}` is a placeholder. Replace it with a real value before sending the request.

### Venues API

#### Get Venue

```bash
maton api '/eventbrite/v3/venues/{venue_id}/'
```

**Note:** `{venue_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Venue

```bash
maton api -X POST '/eventbrite/v3/venues/{venue_id}/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "venue": {
    "name": "Updated Venue Name"
  }
}
JSON
```

**Note:** `{venue_id}` is a placeholder. Replace it with a real value before sending the request.

### Categories API

#### List Categories

```bash
maton api '/eventbrite/v3/categories/'
```

**Response:**
```json
{
  "locale": "en_US",
  "pagination": {"object_count": 21, "page_number": 1, "page_size": 50},
  "categories": [
    {"id": "103", "name": "Music", "short_name": "Music"},
    {"id": "101", "name": "Business & Professional", "short_name": "Business"},
    {"id": "110", "name": "Food & Drink", "short_name": "Food & Drink"}
  ]
}
```

#### Get Category

```bash
maton api '/eventbrite/v3/categories/{category_id}/'
```

**Note:** `{category_id}` is a placeholder. Replace it with a real value before sending the request.

### Subcategories API

#### List Subcategories

```bash
maton api '/eventbrite/v3/subcategories/'
```

### Formats API

#### List Formats

```bash
maton api '/eventbrite/v3/formats/'
```

**Common formats:**
- `1` - Conference
- `2` - Seminar or Talk
- `5` - Festival or Fair
- `6` - Concert or Performance
- `9` - Class, Training, or Workshop
- `10` - Meeting or Networking Event
- `11` - Party or Social Gathering

### System API

#### List Countries

```bash
maton api '/eventbrite/v3/system/countries/'
```

#### List Regions

```bash
maton api '/eventbrite/v3/system/regions/'
```

### Pagination

Use `continuation` token for pagination:

```bash
maton api '/eventbrite/v3/organizations/{org_id}/events/?page_size=50'
maton api '/eventbrite/v3/organizations/{org_id}/events/?continuation=eyJwYWdlIjogMn0'
```

**Note:** `{org_id}` is a placeholder. Replace it with a real value before sending the request.

### Notes

- All endpoint paths must end with a trailing slash (`/`)
- Event creation requires an organization - use `/organizations/{org_id}/events/`
- Legacy user-based event endpoints (e.g., `/users/me/owned_events/`) are deprecated
- Timestamps are in ISO 8601 format (UTC)
- Currency amounts use format "CURRENCY,AMOUNT" where amount is in cents (e.g., "USD,2500" = $25.00)
- Rate limit: 1,000 calls per hour, 48,000 calls per day
- Event Search API is no longer publicly available (deprecated February 2020)

### Resources

- [Eventbrite API Documentation](https://www.eventbrite.com/platform/api)
- [Eventbrite API Basics](https://www.eventbrite.com/platform/docs/api-basics)
- [Eventbrite API Explorer](https://www.eventbrite.com/platform/docs/api-explorer)
- [Maton CLI Manual](https://cli.maton.ai/manual)
