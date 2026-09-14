# GetResponse

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `getresponse`
**Upstream base URL:** `api.getresponse.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.getresponse.com/v3/accounts`
- Gateway: `https://api.maton.ai/getresponse/v3/accounts`

### Account API

#### Get Account Details

```bash
maton api '/getresponse/v3/accounts'
```

#### Get Billing Info

```bash
maton api '/getresponse/v3/accounts/billing'
```

### Campaign API

#### List Campaigns

```bash
maton api '/getresponse/v3/campaigns'
```

**Query parameters:**
- `page` - Page number (starts at 1)
- `perPage` - Records per page (max 1000)

#### Get Campaign

```bash
maton api '/getresponse/v3/campaigns/{campaignId}'
```

**Note:** `{campaignId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Campaign

```bash
maton api -X POST '/getresponse/v3/campaigns' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "My Campaign"
}
JSON
```

### Contact API

#### List Contacts

```bash
maton api '/getresponse/v3/contacts?page=1&perPage=100'
```

**Query parameters:**
- `query[campaignId]` - Filter by campaign
- `query[email]` - Filter by email
- `sort[createdOn]` - Sort by creation date (asc/desc)

#### Get Contact

```bash
maton api '/getresponse/v3/contacts/{contactId}'
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Contact

```bash
maton api -X POST '/getresponse/v3/contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "email": "john@example.com",
  "name": "John Doe",
  "campaign": {
    "campaignId": "abc123"
  },
  "customFieldValues": [
    {
      "customFieldId": "xyz789",
      "value": ["Custom Value"]
    }
  ]
}
JSON
```

#### Update Contact

```bash
maton api -X POST '/getresponse/v3/contacts/{contactId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "John Smith",
  "customFieldValues": [
    {
      "customFieldId": "xyz789",
      "value": ["Updated Value"]
    }
  ]
}
JSON
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Contact

```bash
maton api '/getresponse/v3/contacts/{contactId}' -X DELETE
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Contact Activities

```bash
maton api '/getresponse/v3/contacts/{contactId}/activities'
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

### Custom Fields API

#### List Custom Fields

```bash
maton api '/getresponse/v3/custom-fields'
```

#### Get Custom Field

```bash
maton api '/getresponse/v3/custom-fields/{customFieldId}'
```

**Note:** `{customFieldId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Custom Field

```bash
maton api -X POST '/getresponse/v3/custom-fields' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "company",
  "type": "text",
  "hidden": false,
  "values": []
}
JSON
```

### Newsletter API

#### List Newsletters

```bash
maton api '/getresponse/v3/newsletters'
```

#### Send Newsletter

```bash
maton api -X POST '/getresponse/v3/newsletters' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "subject": "Newsletter Subject",
  "name": "Internal Newsletter Name",
  "campaign": {
    "campaignId": "abc123"
  },
  "content": {
    "html": "<html><body>Newsletter content</body></html>",
    "plain": "Newsletter content"
  },
  "sendOn": "2026-02-15T10:00:00Z"
}
JSON
```

#### Send Draft Newsletter

```bash
maton api -X POST '/getresponse/v3/newsletters/send-draft' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messageId": "newsletter123",
  "sendOn": "2026-02-15T10:00:00Z"
}
JSON
```

#### List RSS Newsletters

```bash
maton api '/getresponse/v3/rss-newsletters'
```

### Tags API

#### List Tags

```bash
maton api '/getresponse/v3/tags'
```

#### Get Tag

```bash
maton api '/getresponse/v3/tags/{tagId}'
```

**Note:** `{tagId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Tag

```bash
maton api -X POST '/getresponse/v3/tags' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "VIP Customer"
}
JSON
```

#### Update Tag

```bash
maton api -X POST '/getresponse/v3/tags/{tagId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Premium Customer"
}
JSON
```

**Note:** `{tagId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Tag

```bash
maton api '/getresponse/v3/tags/{tagId}' -X DELETE
```

**Note:** `{tagId}` is a placeholder. Replace it with a real value before sending the request.

#### Assign Tags to Contact

```bash
maton api -X POST '/getresponse/v3/contacts/{contactId}/tags' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "tags": [
    {"tagId": "abc123"},
    {"tagId": "xyz789"}
  ]
}
JSON
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

### Autoresponders API

#### List Autoresponders

```bash
maton api '/getresponse/v3/autoresponders'
```

#### Get Autoresponder

```bash
maton api '/getresponse/v3/autoresponders/{autoresponderId}'
```

**Note:** `{autoresponderId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Autoresponder

```bash
maton api -X POST '/getresponse/v3/autoresponders' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Welcome Email",
  "subject": "Welcome to our list!",
  "campaign": {
    "campaignId": "abc123"
  },
  "triggerSettings": {
    "dayOfCycle": 0
  },
  "content": {
    "html": "<html><body>Welcome!</body></html>",
    "plain": "Welcome!"
  }
}
JSON
```

#### Update Autoresponder

```bash
maton api -X POST '/getresponse/v3/autoresponders/{autoresponderId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "subject": "Updated Welcome Email"
}
JSON
```

**Note:** `{autoresponderId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Autoresponder

```bash
maton api '/getresponse/v3/autoresponders/{autoresponderId}' -X DELETE
```

**Note:** `{autoresponderId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Autoresponder Statistics

```bash
maton api '/getresponse/v3/autoresponders/{autoresponderId}/statistics'
```

**Note:** `{autoresponderId}` is a placeholder. Replace it with a real value before sending the request.

#### Get All Autoresponder Statistics

```bash
maton api '/getresponse/v3/autoresponders/statistics'
```

### From Fields API

#### List From Fields

```bash
maton api '/getresponse/v3/from-fields'
```

#### Get From Field

```bash
maton api '/getresponse/v3/from-fields/{fromFieldId}'
```

**Note:** `{fromFieldId}` is a placeholder. Replace it with a real value before sending the request.

### Transactional Emails API

#### List Transactional Emails

```bash
maton api '/getresponse/v3/transactional-emails'
```

#### Send Transactional Email

```bash
maton api -X POST '/getresponse/v3/transactional-emails' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "fromField": {
    "fromFieldId": "abc123"
  },
  "subject": "Your Order Confirmation",
  "recipients": {
    "to": "customer@example.com"
  },
  "content": {
    "html": "<html><body>Order confirmed!</body></html>",
    "plain": "Order confirmed!"
  }
}
JSON
```

#### Get Transactional Email

```bash
maton api '/getresponse/v3/transactional-emails/{transactionalEmailId}'
```

**Note:** `{transactionalEmailId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Transactional Email Statistics

```bash
maton api '/getresponse/v3/transactional-emails/statistics'
```

### Imports API

#### List Imports

```bash
maton api '/getresponse/v3/imports'
```

#### Create Import

```bash
maton api -X POST '/getresponse/v3/imports' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "campaign": {
    "campaignId": "abc123"
  },
  "contacts": [
    {
      "email": "user1@example.com",
      "name": "User One"
    },
    {
      "email": "user2@example.com",
      "name": "User Two"
    }
  ]
}
JSON
```

#### Get Import

```bash
maton api '/getresponse/v3/imports/{importId}'
```

**Note:** `{importId}` is a placeholder. Replace it with a real value before sending the request.

### Workflows API

#### List Workflows

```bash
maton api '/getresponse/v3/workflow'
```

#### Get Workflow

```bash
maton api '/getresponse/v3/workflow/{workflowId}'
```

**Note:** `{workflowId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Workflow

```bash
maton api -X POST '/getresponse/v3/workflow/{workflowId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "status": "enabled"
}
JSON
```

**Note:** `{workflowId}` is a placeholder. Replace it with a real value before sending the request.

### Segments API

#### List Segments

```bash
maton api '/getresponse/v3/search-contacts'
```

#### Create Segment

```bash
maton api -X POST '/getresponse/v3/search-contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Active Subscribers",
  "subscribersType": ["subscribed"],
  "sectionLogicOperator": "or",
  "section": []
}
JSON
```

#### Get Segment

```bash
maton api '/getresponse/v3/search-contacts/{searchContactId}'
```

**Note:** `{searchContactId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Segment

```bash
maton api -X POST '/getresponse/v3/search-contacts/{searchContactId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Segment Name"
}
JSON
```

**Note:** `{searchContactId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Segment

```bash
maton api '/getresponse/v3/search-contacts/{searchContactId}' -X DELETE
```

**Note:** `{searchContactId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Contacts from Segment

```bash
maton api '/getresponse/v3/search-contacts/{searchContactId}/contacts'
```

**Note:** `{searchContactId}` is a placeholder. Replace it with a real value before sending the request.

#### Search Contacts Without Saving

```bash
maton api -X POST '/getresponse/v3/search-contacts/contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "subscribersType": ["subscribed"],
  "sectionLogicOperator": "or",
  "section": []
}
JSON
```

### Forms API

#### List Forms

```bash
maton api '/getresponse/v3/forms'
```

#### Get Form

```bash
maton api '/getresponse/v3/forms/{formId}'
```

**Note:** `{formId}` is a placeholder. Replace it with a real value before sending the request.

### Webforms API

#### List Webforms

```bash
maton api '/getresponse/v3/webforms'
```

#### Get Webform

```bash
maton api '/getresponse/v3/webforms/{webformId}'
```

**Note:** `{webformId}` is a placeholder. Replace it with a real value before sending the request.

### SMS Messages API

#### List SMS Messages

```bash
maton api '/getresponse/v3/sms'
```

#### Send SMS

```bash
maton api -X POST '/getresponse/v3/sms' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "recipients": {
    "campaignId": "abc123"
  },
  "content": {
    "message": "Your SMS message content"
  },
  "sendOn": "2026-02-15T10:00:00Z"
}
JSON
```

#### Get SMS Message

```bash
maton api '/getresponse/v3/sms/{smsId}'
```

**Note:** `{smsId}` is a placeholder. Replace it with a real value before sending the request.

#### Get SMS Statistics

```bash
maton api '/getresponse/v3/statistics/sms/{smsId}'
```

**Note:** `{smsId}` is a placeholder. Replace it with a real value before sending the request.

### Shops API

#### List Shops

```bash
maton api '/getresponse/v3/shops'
```

#### Create Shop

```bash
maton api -X POST '/getresponse/v3/shops' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "My Store",
  "locale": "en_US",
  "currency": "USD"
}
JSON
```

#### Get Shop

```bash
maton api '/getresponse/v3/shops/{shopId}'
```

**Note:** `{shopId}` is a placeholder. Replace it with a real value before sending the request.

#### List Products

```bash
maton api '/getresponse/v3/shops/{shopId}/products'
```

**Note:** `{shopId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Product

```bash
maton api -X POST '/getresponse/v3/shops/{shopId}/products' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Product Name",
  "url": "https://example.com/product",
  "variants": [
    {
      "name": "Default",
      "price": 29.99,
      "priceTax": 32.99
    }
  ]
}
JSON
```

**Note:** `{shopId}` is a placeholder. Replace it with a real value before sending the request.

#### List Orders

```bash
maton api '/getresponse/v3/shops/{shopId}/orders'
```

**Note:** `{shopId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Order

```bash
maton api -X POST '/getresponse/v3/shops/{shopId}/orders' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contactId": "abc123",
  "totalPrice": 99.99,
  "currency": "USD",
  "status": "completed"
}
JSON
```

**Note:** `{shopId}` is a placeholder. Replace it with a real value before sending the request.

### Webinars API

#### List Webinars

```bash
maton api '/getresponse/v3/webinars'
```

#### Get Webinar

```bash
maton api '/getresponse/v3/webinars/{webinarId}'
```

**Note:** `{webinarId}` is a placeholder. Replace it with a real value before sending the request.

### Landing Pages API

#### List Landing Pages

```bash
maton api '/getresponse/v3/lps'
```

#### Get Landing Page

```bash
maton api '/getresponse/v3/lps/{lpsId}'
```

**Note:** `{lpsId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Landing Page Statistics

```bash
maton api '/getresponse/v3/statistics/lps/{lpsId}/performance'
```

**Note:** `{lpsId}` is a placeholder. Replace it with a real value before sending the request.

### Notes

- Campaign IDs and Contact IDs are alphanumeric strings (e.g., "fZ0Xg", "VZ4Sa5g")
- Timestamps are in ISO 8601 format
- Field names use camelCase
- Use page-based pagination with `page` and `perPage` parameters
- Rate limits: 30,000 requests per 10 minutes, 80 requests per second
- "Campaigns" in GetResponse are equivalent to email lists/audiences
- "Search contacts" and "segments" refer to the same resource

### Resources

- [GetResponse API Documentation](https://apidocs.getresponse.com/v3)
- [GetResponse OpenAPI Spec](https://apireference.getresponse.com/open-api.json)
- [GetResponse Help Center](https://www.getresponse.com/help)
- [Maton CLI Manual](https://cli.maton.ai/manual)
