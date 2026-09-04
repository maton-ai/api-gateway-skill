# GoHighLevel (PIT) Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `highlevel-pit`
**Base URL proxied:** `services.leadconnectorhq.com`

## API Path Pattern

```
/highlevel-pit/{resource}
```

## Two Token Types

GoHighLevel uses Agency tokens and Sub-Account tokens with different scopes:

- **Agency token**: Manage locations (sub-accounts), snapshots
- **Sub-Account token**: Contacts, calendars, pipelines, conversations, payments, custom fields, tags, workflows, campaigns

Use the `Maton-Connection` header to specify which token to use.

## Common Endpoints — Agency Token

### Search Locations
```bash
maton api '/highlevel-pit/locations/search?companyId={companyId}'
```

### Get Location
```bash
maton api '/highlevel-pit/locations/{locationId}'
```

### Create Location
```bash
maton api -X POST '/highlevel-pit/locations/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "companyId": "{companyId}",
  "name": "New Sub-Account",
  "address": "123 Main St",
  "city": "San Francisco",
  "state": "CA",
  "country": "US",
  "timezone": "America/Los_Angeles",
  "email": "admin@example.com"
}
EOF
```

### Update Location
```bash
maton api -X PUT '/highlevel-pit/locations/{locationId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Updated Name"
}
EOF
```

### Delete Location
```bash
maton api -X DELETE '/highlevel-pit/locations/{locationId}'
```

### List Snapshots
```bash
maton api '/highlevel-pit/snapshots/?companyId={companyId}'
```

## Common Endpoints — Sub-Account Token

### Contacts

#### List Contacts
```bash
maton api '/highlevel-pit/contacts/?locationId={locationId}&limit=20'
maton api '/highlevel-pit/contacts/?locationId={locationId}&query=john@example.com'
```

#### Get Contact
```bash
maton api '/highlevel-pit/contacts/{contactId}'
```

#### Create Contact
```bash
maton api -X POST '/highlevel-pit/contacts/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "locationId": "{locationId}",
  "firstName": "John",
  "lastName": "Doe",
  "email": "john@example.com",
  "phone": "+15551234567",
  "tags": ["customer"]
}
EOF
```

#### Update Contact
```bash
maton api -X PUT '/highlevel-pit/contacts/{contactId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "firstName": "Jane"
}
EOF
```

#### Delete Contact
```bash
maton api -X DELETE '/highlevel-pit/contacts/{contactId}'
```

### Contact Tags
```bash
maton api -X POST '/highlevel-pit/contacts/{contactId}/tags'  # Add tags
maton api -X DELETE '/highlevel-pit/contacts/{contactId}/tags'  # Remove tags
```

### Contact Notes
```bash
maton api '/highlevel-pit/contacts/{contactId}/notes'
maton api -X POST '/highlevel-pit/contacts/{contactId}/notes'
maton api -X PUT '/highlevel-pit/contacts/{contactId}/notes/{noteId}'
maton api -X DELETE '/highlevel-pit/contacts/{contactId}/notes/{noteId}'
```

### Contact Tasks
```bash
maton api '/highlevel-pit/contacts/{contactId}/tasks'
maton api -X POST '/highlevel-pit/contacts/{contactId}/tasks'  # requires "completed" field
maton api -X PUT '/highlevel-pit/contacts/{contactId}/tasks/{taskId}'
maton api -X DELETE '/highlevel-pit/contacts/{contactId}/tasks/{taskId}'
```

### Opportunities
```bash
maton api '/highlevel-pit/opportunities/search?location_id={locationId}'
maton api '/highlevel-pit/opportunities/{opportunityId}'
maton api -X POST '/highlevel-pit/opportunities/'
maton api -X PUT '/highlevel-pit/opportunities/{opportunityId}'  # requires pipelineId
maton api -X DELETE '/highlevel-pit/opportunities/{opportunityId}'
```

### Pipelines
```bash
maton api '/highlevel-pit/opportunities/pipelines?locationId={locationId}'
```

### Calendars
```bash
maton api '/highlevel-pit/calendars/?locationId={locationId}'
maton api '/highlevel-pit/calendars/{calendarId}'
maton api -X POST '/highlevel-pit/calendars/'
maton api -X PUT '/highlevel-pit/calendars/{calendarId}'  # do NOT include locationId
maton api -X DELETE '/highlevel-pit/calendars/{calendarId}'
maton api '/highlevel-pit/calendars/events?locationId={locationId}&calendarId={calendarId}&startTime={epochMs}&endTime={epochMs}'
maton api '/highlevel-pit/calendars/{calendarId}/free-slots?startDate={epochMs}&endDate={epochMs}'
maton api '/highlevel-pit/calendars/groups?locationId={locationId}'
```

### Conversations
```bash
maton api '/highlevel-pit/conversations/search?locationId={locationId}'
maton api '/highlevel-pit/conversations/{conversationId}'
maton api '/highlevel-pit/conversations/{conversationId}/messages'
maton api -X POST '/highlevel-pit/conversations/'
```

### Users
```bash
maton api '/highlevel-pit/users/?locationId={locationId}'
```

### Location Tags
```bash
maton api '/highlevel-pit/locations/{locationId}/tags'
maton api -X POST '/highlevel-pit/locations/{locationId}/tags'
maton api '/highlevel-pit/locations/{locationId}/tags/{tagId}'
maton api -X PUT '/highlevel-pit/locations/{locationId}/tags/{tagId}'
maton api -X DELETE '/highlevel-pit/locations/{locationId}/tags/{tagId}'
```

### Custom Fields
```bash
maton api '/highlevel-pit/locations/{locationId}/customFields'
maton api -X POST '/highlevel-pit/locations/{locationId}/customFields'
maton api '/highlevel-pit/locations/{locationId}/customFields/{fieldId}'
maton api -X PUT '/highlevel-pit/locations/{locationId}/customFields/{fieldId}'
maton api -X DELETE '/highlevel-pit/locations/{locationId}/customFields/{fieldId}'
```

### Custom Values
```bash
maton api '/highlevel-pit/locations/{locationId}/customValues'
maton api -X POST '/highlevel-pit/locations/{locationId}/customValues'
maton api '/highlevel-pit/locations/{locationId}/customValues/{valueId}'
maton api -X PUT '/highlevel-pit/locations/{locationId}/customValues/{valueId}'
maton api -X DELETE '/highlevel-pit/locations/{locationId}/customValues/{valueId}'
```

### Businesses
```bash
maton api '/highlevel-pit/businesses/?locationId={locationId}'
maton api '/highlevel-pit/businesses/{businessId}'
maton api -X POST '/highlevel-pit/businesses/'
maton api -X PUT '/highlevel-pit/businesses/{businessId}'
maton api -X DELETE '/highlevel-pit/businesses/{businessId}'
```

### Products
```bash
maton api '/highlevel-pit/products/?locationId={locationId}'
maton api '/highlevel-pit/products/{productId}?locationId={locationId}'
maton api -X POST '/highlevel-pit/products/'
maton api -X DELETE '/highlevel-pit/products/{productId}?locationId={locationId}'
```

### Invoices
```bash
maton api '/highlevel-pit/invoices/?altId={locationId}&altType=location&limit=20&offset=0'
```

### Payments
```bash
maton api '/highlevel-pit/payments/orders?altId={locationId}&altType=location'
maton api '/highlevel-pit/payments/transactions?altId={locationId}&altType=location'
maton api '/highlevel-pit/payments/subscriptions?altId={locationId}&altType=location'
```

### Trigger Links
```bash
maton api '/highlevel-pit/links/?locationId={locationId}'
maton api -X POST '/highlevel-pit/links/'
maton api -X PUT '/highlevel-pit/links/{linkId}'
maton api -X DELETE '/highlevel-pit/links/{linkId}'
```

### Other
```bash
maton api '/highlevel-pit/workflows/?locationId={locationId}'
maton api '/highlevel-pit/campaigns/?locationId={locationId}'
maton api '/highlevel-pit/forms/?locationId={locationId}'
maton api '/highlevel-pit/surveys/?locationId={locationId}'
maton api '/highlevel-pit/funnels/funnel/list?locationId={locationId}'
maton api '/highlevel-pit/social-media-posting/{locationId}/accounts'
maton api '/highlevel-pit/social-media-posting/{locationId}/categories'
maton api '/highlevel-pit/medias/files?altId={locationId}&altType=location&type=file'
```

## Notes

- Two token types with different scopes — use `Maton-Connection` header
- Most sub-account endpoints require `locationId` query parameter
- Payment/invoice endpoints use `altId` + `altType=location` instead of `locationId`
- Calendar events use epoch milliseconds for time parameters
- Calendar update must NOT include `locationId` in body
- Contact task creation requires `completed` boolean field
- Opportunity update requires `pipelineId` even when not changing it
- All delete operations return HTTP 200

## Resources

- [GoHighLevel API Documentation](https://highlevel.stoplight.io/docs/integrations/)
- [GoHighLevel Marketplace](https://marketplace.gohighlevel.com/docs/)
