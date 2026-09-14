# Keap

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `keap`
**Upstream base URL:** `api.infusionsoft.com/crm/rest`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.infusionsoft.com/crm/rest/v2/oauth/connect/userinfo`
- Gateway: `https://api.maton.ai/keap/crm/rest/v2/oauth/connect/userinfo`

### User Info API

#### Get Current User

```bash
maton api '/keap/crm/rest/v2/oauth/connect/userinfo'
```

**Response:**
```json
{
  "email": "user@example.com",
  "sub": "1",
  "id": "4236128",
  "keap_id": "user@example.com",
  "family_name": "Doe",
  "given_name": "John",
  "is_admin": true
}
```

### Contact API

#### List Contacts

```bash
maton api '/keap/crm/rest/v2/contacts'
```

**Query parameters:**
- `page_size` - Number of results per page (default 50, max 1000)
- `page_token` - Token for next page
- `filter` - Filter expression
- `order_by` - Sort order
- `fields` - Fields to include in response

**Response:**

```json
{
  "contacts": [
    {
      "id": "9",
      "family_name": "Park",
      "given_name": "John"
    }
  ],
  "next_page_token": ""
}
```

#### Get Contact

```bash
maton api '/keap/crm/rest/v2/contacts/{contact_id}'
```

**Note:** `{contact_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Contact

```bash
maton api -X POST '/keap/crm/rest/v2/contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "given_name": "John",
  "family_name": "Doe",
  "email_addresses": [
    {"email": "john@example.com", "field": "EMAIL1"}
  ],
  "phone_numbers": [
    {"number": "555-1234", "field": "PHONE1"}
  ]
}
JSON
```

**Response:**
```json
{
  "id": "13",
  "family_name": "Doe",
  "given_name": "John"
}
```

#### Update Contact

```bash
maton api -X PATCH '/keap/crm/rest/v2/contacts/{contact_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "given_name": "Jane"
}
JSON
```

**Note:** `{contact_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Contact

```bash
maton api '/keap/crm/rest/v2/contacts/{contact_id}' -X DELETE
```

**Note:** `{contact_id}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 on success.

#### Get Contact Notes

```bash
maton api '/keap/crm/rest/v2/contacts/{contact_id}/notes'
```

**Note:** `{contact_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Contact Note

```bash
maton api -X POST '/keap/crm/rest/v2/contacts/{contact_id}/notes' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "body": "Note content here",
  "title": "Note Title"
}
JSON
```

**Note:** `{contact_id}` is a placeholder. Replace it with a real value before sending the request.

### Company API

#### List Companies

```bash
maton api '/keap/crm/rest/v2/companies'
```

#### Get Company

```bash
maton api '/keap/crm/rest/v2/companies/{company_id}'
```

**Note:** `{company_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Company

```bash
maton api -X POST '/keap/crm/rest/v2/companies' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "company_name": "Acme Corp",
  "phone_number": {"number": "555-1234", "type": "MAIN"},
  "website": "https://acme.com"
}
JSON
```

#### Update Company

```bash
maton api -X PATCH '/keap/crm/rest/v2/companies/{company_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "company_name": "Acme Corporation"
}
JSON
```

**Note:** `{company_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Company

```bash
maton api '/keap/crm/rest/v2/companies/{company_id}' -X DELETE
```

**Note:** `{company_id}` is a placeholder. Replace it with a real value before sending the request.

### Tag API

#### List Tags

```bash
maton api '/keap/crm/rest/v2/tags'
```

**Response:**
```json
{
  "tags": [
    {
      "id": "91",
      "name": "Nurture Subscriber",
      "description": "",
      "category": {"id": "10"},
      "create_time": "2017-04-24T17:26:26Z",
      "update_time": "2017-04-24T17:26:26Z"
    }
  ],
  "next_page_token": ""
}
```

#### Get Tag

```bash
maton api '/keap/crm/rest/v2/tags/{tag_id}'
```

**Note:** `{tag_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Tag

```bash
maton api -X POST '/keap/crm/rest/v2/tags' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "VIP Customer",
  "description": "High value customers"
}
JSON
```

#### Update Tag

```bash
maton api -X PATCH '/keap/crm/rest/v2/tags/{tag_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Premium Customer"
}
JSON
```

**Note:** `{tag_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Tag

```bash
maton api '/keap/crm/rest/v2/tags/{tag_id}' -X DELETE
```

**Note:** `{tag_id}` is a placeholder. Replace it with a real value before sending the request.

#### List Contacts with Tag

```bash
maton api '/keap/crm/rest/v2/tags/{tag_id}/contacts'
```

**Note:** `{tag_id}` is a placeholder. Replace it with a real value before sending the request.

#### Apply Tags to Contacts

```bash
maton api -X POST '/keap/crm/rest/v2/tags/{tag_id}/contacts:applyTags' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contact_ids": ["1", "2", "3"]
}
JSON
```

**Note:** `{tag_id}` is a placeholder. Replace it with a real value before sending the request.

#### Remove Tags from Contacts

```bash
maton api -X POST '/keap/crm/rest/v2/tags/{tag_id}/contacts:removeTags' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contact_ids": ["1", "2", "3"]
}
JSON
```

**Note:** `{tag_id}` is a placeholder. Replace it with a real value before sending the request.

### Tag Category API

#### List Tag Categories

```bash
maton api '/keap/crm/rest/v2/tags/categories'
```

#### Create Tag Category

```bash
maton api -X POST '/keap/crm/rest/v2/tags/categories' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Customer Segments"
}
JSON
```

### Task API

#### List Tasks

Use the `filter` parameter for filtering results:

```bash
maton api '/keap/crm/rest/v2/contacts?filter=given_name==John'

maton api '/keap/crm/rest/v2/contacts?filter=email_addresses.email==john@example.com'

maton api '/keap/crm/rest/v2/tasks?filter=completed==false'
```

#### Get Task

```bash
maton api '/keap/crm/rest/v2/tasks/{task_id}'
```

**Note:** `{task_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Task

```bash
maton api -X POST '/keap/crm/rest/v2/tasks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Follow up call",
  "description": "Call to discuss proposal",
  "due_date": "2026-02-15T10:00:00Z",
  "contact": {"id": "9"}
}
JSON
```

#### Update Task

```bash
maton api -X PATCH '/keap/crm/rest/v2/tasks/{task_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "completed": true
}
JSON
```

**Note:** `{task_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Task

```bash
maton api '/keap/crm/rest/v2/tasks/{task_id}' -X DELETE
```

**Note:** `{task_id}` is a placeholder. Replace it with a real value before sending the request.

### Opportunity API

#### List Opportunities

```bash
maton api '/keap/crm/rest/v2/opportunities'
```

#### Get Opportunity

```bash
maton api '/keap/crm/rest/v2/opportunities/{opportunity_id}'
```

**Note:** `{opportunity_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Opportunity

```bash
maton api -X POST '/keap/crm/rest/v2/opportunities' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "opportunity_title": "New Deal",
  "contact": {"id": "9"},
  "stage": {"id": "1"},
  "estimated_close_date": "2026-03-01"
}
JSON
```

#### Update Opportunity

```bash
maton api -X PATCH '/keap/crm/rest/v2/opportunities/{opportunity_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "stage": {"id": "2"}
}
JSON
```

**Note:** `{opportunity_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Opportunity

```bash
maton api '/keap/crm/rest/v2/opportunities/{opportunity_id}' -X DELETE
```

**Note:** `{opportunity_id}` is a placeholder. Replace it with a real value before sending the request.

#### List Opportunity Stages

```bash
maton api '/keap/crm/rest/v2/opportunities/stages'
```

### Order API

#### List Orders

```bash
maton api '/keap/crm/rest/v2/orders'
```

#### Get Order

```bash
maton api '/keap/crm/rest/v2/orders/{order_id}'
```

**Note:** `{order_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Order

```bash
maton api -X POST '/keap/crm/rest/v2/orders' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contact": {"id": "9"},
  "order_date": "2026-02-08",
  "order_title": "Product Order"
}
JSON
```

#### Add Order Item

```bash
maton api -X POST '/keap/crm/rest/v2/orders/{order_id}/items' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "product": {"id": "1"},
  "quantity": 2
}
JSON
```

**Note:** `{order_id}` is a placeholder. Replace it with a real value before sending the request.

### Product API

#### List Products

```bash
maton api '/keap/crm/rest/v2/products'
```

#### Get Product

```bash
maton api '/keap/crm/rest/v2/products/{product_id}'
```

**Note:** `{product_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Product

```bash
maton api -X POST '/keap/crm/rest/v2/products' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "product_name": "Consulting Package",
  "product_price": 500.00,
  "product_short_description": "1 hour consulting"
}
JSON
```

### Campaign API

#### List Campaigns

```bash
maton api '/keap/crm/rest/v2/campaigns'
```

#### Get Campaign

```bash
maton api '/keap/crm/rest/v2/campaigns/{campaign_id}'
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

#### List Campaign Sequences

```bash
maton api '/keap/crm/rest/v2/campaigns/{campaign_id}/sequences'
```

**Note:** `{campaign_id}` is a placeholder. Replace it with a real value before sending the request.

#### Add Contacts to Campaign Sequence

```bash
maton api -X POST '/keap/crm/rest/v2/campaigns/{campaign_id}/sequences/{sequence_id}:addContacts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "contact_ids": ["1", "2"]
}
EOF
```

**Note:** `{campaign_id}` and `{sequence_id}` are placeholders. Replace each of them with real values before sending the request.

#### Remove Contacts from Sequence

```bash
maton api -X POST '/keap/crm/rest/v2/campaigns/{campaign_id}/sequences/{sequence_id}:removeContacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contact_ids": ["1", "2"]
}
JSON
```

**Note:** `{campaign_id}` and `{sequence_id}` are placeholders. Replace each of them with real values before sending the request.

### Email API

#### List Emails

```bash
maton api '/keap/crm/rest/v2/emails'
```

#### Get Email

```bash
maton api '/keap/crm/rest/v2/emails/{email_id}'
```

**Note:** `{email_id}` is a placeholder. Replace it with a real value before sending the request.

#### Send Email

```bash
maton api -X POST '/keap/crm/rest/v2/emails:send' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contacts": [{"id": "9"}],
  "subject": "Hello",
  "html_content": "<p>Email body</p>"
}
JSON
```

### User API

#### List Users

```bash
maton api '/keap/crm/rest/v2/users'
```

#### Get User

```bash
maton api '/keap/crm/rest/v2/users/{user_id}'
```

**Note:** `{user_id}` is a placeholder. Replace it with a real value before sending the request.

### Subscription API

#### List Subscriptions

```bash
maton api '/keap/crm/rest/v2/subscriptions'
```

#### Get Subscription

```bash
maton api '/keap/crm/rest/v2/subscriptions/{subscription_id}'
```

**Note:** `{subscription_id}` is a placeholder. Replace it with a real value before sending the request.

### Affiliate API

#### List Affiliates

```bash
maton api '/keap/crm/rest/v2/affiliates'
```

#### Get Affiliate

```bash
maton api '/keap/crm/rest/v2/affiliates/{affiliate_id}'
```

**Note:** `{affiliate_id}` is a placeholder. Replace it with a real value before sending the request.

### Automation API

#### List Automations

```bash
maton api '/keap/crm/rest/v2/automations'
```

#### Get Automation

```bash
maton api '/keap/crm/rest/v2/automations/{automation_id}'
```

**Note:** `{automation_id}` is a placeholder. Replace it with a real value before sending the request.

### Pagination

Uses token-based pagination:

```bash
maton api '/keap/crm/rest/v2/contacts?page_size=50'
maton api '/keap/crm/rest/v2/contacts?page_size=50&page_token=NEXT_TOKEN'
```

Response includes `next_page_token` (empty when no more pages).

### Filtering

Use the `filter` parameter:

```bash
maton api '/keap/crm/rest/v2/contacts?filter=given_name==John'
maton api '/keap/crm/rest/v2/tasks?filter=completed==false'
```

### Notes

- API version is v2 (v1 is deprecated)
- Path must include `/crm/rest` prefix
- IDs are returned as strings
- Maximum `page_size` is 1000
- Timestamps use ISO 8601 format

### Resources

- [Keap Developer Portal](https://developer.infusionsoft.com/)
- [Keap REST API V2 Documentation](https://developer.infusionsoft.com/docs/restv2/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
