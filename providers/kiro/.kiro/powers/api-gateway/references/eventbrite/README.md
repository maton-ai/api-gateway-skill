# Eventbrite Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `eventbrite`
**Base URL proxied:** `www.eventbriteapi.com`

## API Path Pattern

```
/eventbrite/v3/{resource}/
```

Note: All Eventbrite API paths should end with a trailing slash.

## Common Endpoints

### Get Current User
```bash
maton api '/eventbrite/v3/users/me/'
```

### List User Organizations
```bash
maton api '/eventbrite/v3/users/me/organizations/'
```

### List User Orders
```bash
maton api '/eventbrite/v3/users/me/orders/'
```

### List Organization Events
```bash
maton api '/eventbrite/v3/organizations/{organization_id}/events/'
```

Query parameters:
- `status` - Filter: `draft`, `live`, `started`, `ended`, `completed`, `canceled`
- `order_by` - Sort: `start_asc`, `start_desc`, `created_asc`, `created_desc`
- `time_filter` - Filter: `current_future`, `past`

### Create Event
```bash
maton api -X POST '/eventbrite/v3/organizations/{organization_id}/events/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "event": {
    "name": {"html": "My Event"},
    "start": {"timezone": "America/Los_Angeles", "utc": "2026-03-01T19:00:00Z"},
    "end": {"timezone": "America/Los_Angeles", "utc": "2026-03-01T22:00:00Z"},
    "currency": "USD"
  }
}
EOF
```

### Get Event
```bash
maton api '/eventbrite/v3/events/{event_id}/'
```

### Update Event
```bash
maton api -X POST '/eventbrite/v3/events/{event_id}/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "event": {
    "name": {"html": "Updated Name"}
  }
}
EOF
```

### Publish Event
```bash
maton api -X POST '/eventbrite/v3/events/{event_id}/publish/'
```

### Cancel Event
```bash
maton api -X POST '/eventbrite/v3/events/{event_id}/cancel/'
```

### Delete Event
```bash
maton api -X DELETE '/eventbrite/v3/events/{event_id}/'
```

### List Ticket Classes
```bash
maton api '/eventbrite/v3/events/{event_id}/ticket_classes/'
```

### Create Ticket Class
```bash
maton api -X POST '/eventbrite/v3/events/{event_id}/ticket_classes/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "ticket_class": {
    "name": "General Admission",
    "quantity_total": 100,
    "cost": "USD,2500"
  }
}
EOF
```

### List Event Attendees
```bash
maton api '/eventbrite/v3/events/{event_id}/attendees/'
```

### List Event Orders
```bash
maton api '/eventbrite/v3/events/{event_id}/orders/'
```

### Get Order
```bash
maton api '/eventbrite/v3/orders/{order_id}/'
```

### List Organization Venues
```bash
maton api '/eventbrite/v3/organizations/{organization_id}/venues/'
```

### Create Venue
```bash
maton api -X POST '/eventbrite/v3/organizations/{organization_id}/venues/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
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
EOF
```

### Get Venue
```bash
maton api '/eventbrite/v3/venues/{venue_id}/'
```

### List Categories
```bash
maton api '/eventbrite/v3/categories/'
```

### Get Category
```bash
maton api '/eventbrite/v3/categories/{category_id}/'
```

### List Subcategories
```bash
maton api '/eventbrite/v3/subcategories/'
```

### List Formats
```bash
maton api '/eventbrite/v3/formats/'
```

### List Countries
```bash
maton api '/eventbrite/v3/system/countries/'
```

### List Regions
```bash
maton api '/eventbrite/v3/system/regions/'
```

## Expansions

Include related data with the `expand` parameter:

```bash
maton api '/eventbrite/v3/events/{event_id}/?expand=venue,ticket_classes,category'
```

Available expansions: `venue`, `ticket_classes`, `category`, `subcategory`, `format`, `organizer`

## Pagination

Use `continuation` token for pagination:

```bash
maton api '/eventbrite/v3/organizations/{org_id}/events/?page_size=50'
maton api '/eventbrite/v3/organizations/{org_id}/events/?continuation=eyJwYWdlIjogMn0'
```

## Notes

- All endpoint paths must end with a trailing slash (`/`)
- Event creation requires an organization - use `/organizations/{org_id}/events/`
- Legacy user-based event endpoints (e.g., `/users/me/owned_events/`) are deprecated
- Timestamps are in ISO 8601 format (UTC)
- Currency amounts use format "CURRENCY,AMOUNT" where amount is in cents (e.g., "USD,2500" = $25.00)
- Rate limit: 1,000 calls per hour, 48,000 calls per day
- Event Search API is no longer publicly available (deprecated February 2020)

## Resources

- [Eventbrite API Documentation](https://www.eventbrite.com/platform/api)
- [API Basics](https://www.eventbrite.com/platform/docs/api-basics)
- [API Explorer](https://www.eventbrite.com/platform/docs/api-explorer)
