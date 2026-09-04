# ClickFunnels Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `clickfunnels`
**Base URL proxied:** `{subdomain}.myclickfunnels.com`

The router automatically handles the subdomain from your OAuth connection.

## API Path Pattern

```
/clickfunnels/api/v2/{resource}
```

## Required Headers

THe `User-Agent` header is required to avoid Cloudflare blocks:

```
User-Agent: Maton/1.0
```

## Common Endpoints

### Teams

#### List Teams
```bash
maton api '/clickfunnels/api/v2/teams'
```

#### Get Team
```bash
maton api '/clickfunnels/api/v2/teams/{team_id}'
```

### Workspaces

#### List Workspaces
```bash
maton api '/clickfunnels/api/v2/teams/{team_id}/workspaces'
```

#### Get Workspace
```bash
maton api '/clickfunnels/api/v2/workspaces/{workspace_id}'
```

### Contacts

#### List Contacts
```bash
maton api '/clickfunnels/api/v2/workspaces/{workspace_id}/contacts'
```

#### Get Contact
```bash
maton api '/clickfunnels/api/v2/contacts/{contact_id}'
```

#### Create Contact
```bash
maton api -X POST '/clickfunnels/api/v2/workspaces/{workspace_id}/contacts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "contact": {
    "email_address": "user@example.com",
    "first_name": "John",
    "last_name": "Doe"
  }
}
EOF
```

#### Update Contact
```bash
maton api -X PUT '/clickfunnels/api/v2/contacts/{contact_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "contact": {
    "first_name": "Updated"
  }
}
EOF
```

#### Delete Contact
```bash
maton api -X DELETE '/clickfunnels/api/v2/contacts/{contact_id}'
```

#### Upsert Contact
```bash
maton api -X POST '/clickfunnels/api/v2/workspaces/{workspace_id}/contacts/upsert'
```

### Products

#### List Products
```bash
maton api '/clickfunnels/api/v2/workspaces/{workspace_id}/products'
```

#### Get Product
```bash
maton api '/clickfunnels/api/v2/products/{product_id}'
```

#### Create Product
```bash
maton api -X POST '/clickfunnels/api/v2/workspaces/{workspace_id}/products' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "product": {
    "name": "New Product",
    "visible_in_store": true
  }
}
EOF
```

#### Archive/Unarchive Product
```bash
maton api -X POST '/clickfunnels/api/v2/products/{product_id}/archive'
maton api -X POST '/clickfunnels/api/v2/products/{product_id}/unarchive'
```

### Orders

#### List Orders
```bash
maton api '/clickfunnels/api/v2/workspaces/{workspace_id}/orders'
```

#### Get Order
```bash
maton api '/clickfunnels/api/v2/orders/{order_id}'
```

### Fulfillments

#### List Fulfillments
```bash
maton api '/clickfunnels/api/v2/workspaces/{workspace_id}/fulfillments'
```

#### Create Fulfillment
```bash
maton api -X POST '/clickfunnels/api/v2/workspaces/{workspace_id}/fulfillments'
```

#### Cancel Fulfillment
```bash
maton api -X POST '/clickfunnels/api/v2/fulfillments/{fulfillment_id}/cancel'
```

### Courses & Enrollments

#### List Courses
```bash
maton api '/clickfunnels/api/v2/workspaces/{workspace_id}/courses'
```

#### List Enrollments
```bash
maton api '/clickfunnels/api/v2/courses/{course_id}/enrollments'
```

#### Create Enrollment
```bash
maton api -X POST '/clickfunnels/api/v2/courses/{course_id}/enrollments'
```

### Forms & Submissions

#### List Forms
```bash
maton api '/clickfunnels/api/v2/workspaces/{workspace_id}/forms'
```

#### List Submissions
```bash
maton api '/clickfunnels/api/v2/forms/{form_id}/submissions'
```

### Webhooks

#### List Webhook Endpoints
```bash
maton api '/clickfunnels/api/v2/workspaces/{workspace_id}/webhooks/outgoing/endpoints'
```

#### Create Webhook Endpoint
```bash
maton api -X POST '/clickfunnels/api/v2/workspaces/{workspace_id}/webhooks/outgoing/endpoints' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "webhooks_outgoing_endpoint": {
    "url": "https://example.com/webhook",
    "name": "My Webhook",
    "event_type_ids": ["contact.created"]
  }
}
EOF
```

#### Delete Webhook Endpoint
```bash
maton api -X DELETE '/clickfunnels/api/v2/webhooks/outgoing/endpoints/{endpoint_id}'
```

### Images

#### List Images
```bash
maton api '/clickfunnels/api/v2/workspaces/{workspace_id}/images'
```

#### Upload Image via URL
```bash
maton api -X POST '/clickfunnels/api/v2/workspaces/{workspace_id}/images' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "image": {
    "upload_source_url": "https://example.com/image.png"
  }
}
EOF
```

## Pagination

Cursor-based pagination with 20 items per page:

```bash
# First page
maton api '/clickfunnels/api/v2/workspaces/{workspace_id}/contacts'

# Next page (use ID from Pagination-Next header)
maton api '/clickfunnels/api/v2/workspaces/{workspace_id}/contacts?after=1087091674'
```

Response headers:
- `Pagination-Next`: ID of last item
- `Link`: Full URL for next page

## Filtering

```bash
# Single filter
maton api '/clickfunnels/api/v2/workspaces/{workspace_id}/contacts?filter[email_address]=user@example.com'

# Multiple values (OR)
maton api '/clickfunnels/api/v2/workspaces/{workspace_id}/contacts?filter[email_address]=a@example.com,b@example.com'

# Multiple filters (AND)
maton api '/clickfunnels/api/v2/workspaces/{workspace_id}/contacts?filter[email_address]=user@example.com&filter[id]=123'
```

## Notes

- Subdomain is automatically determined from your OAuth connection
- IDs are integers; each resource also has a `public_id` string
- Request bodies use nested keys: `{"contact": {...}}`, `{"product": {...}}`
- List endpoints: use `workspaces/{id}/{resource}` pattern
- Single resource: use `/{resource}/{id}` pattern (no workspace prefix)
- Delete operations return HTTP 204 with empty body
- Max 20 items per page, use `after` parameter for pagination

## Resources

- [ClickFunnels API Introduction](https://developers.myclickfunnels.com/docs/intro)
- [ClickFunnels API Reference](https://developers.myclickfunnels.com/reference)
- [Pagination Guide](https://developers.myclickfunnels.com/docs/pagination)
- [Filtering Guide](https://developers.myclickfunnels.com/docs/filtering)
