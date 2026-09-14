# Brevo

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `brevo`
**Upstream base URL:** `api.brevo.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.brevo.com/v3/account`
- Gateway: `https://api.maton.ai/brevo/v3/account`

### Account API

#### Get Account

```bash
maton api '/brevo/v3/account'
```

**Response:**
```json
{
  "email": "user@example.com",
  "firstName": "John",
  "lastName": "Doe",
  "companyName": "Acme Inc",
  "relay": {
    "enabled": true,
    "data": {
      "userName": "user@smtp-brevo.com",
      "relay": "smtp-relay.brevo.com",
      "port": 587
    }
  }
}
```

### Contacts API

#### List Contacts

```bash
maton api '/brevo/v3/contacts'
```

**Query parameters:**
- `limit` - Number of results per page (default: 50, max: 500)
- `offset` - Index of first result (0-based)
- `modifiedSince` - Filter by modification date (ISO 8601)

**Response:**
```json
{
  "contacts": [
    {
      "id": 1,
      "email": "contact@example.com",
      "emailBlacklisted": false,
      "smsBlacklisted": false,
      "createdAt": "2026-02-09T20:33:59.705+01:00",
      "modifiedAt": "2026-02-09T20:35:19.529+01:00",
      "listIds": [2],
      "attributes": {
        "FIRSTNAME": "John",
        "LASTNAME": "Doe"
      }
    }
  ],
  "count": 1
}
```

#### Get Contact

```bash
maton api '/brevo/v3/contacts/{identifier}'
```

**Note:** `{identifier}` is a placeholder. Replace it with a real value before sending the request. The identifier can be email address, phone number, or contact ID.

**Query parameters:**
- `identifierType` - Type of identifier: `email_id`, `phone_id`, `contact_id`, `ext_id`

#### Create Contact

```bash
maton api -X POST '/brevo/v3/contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "email": "newcontact@example.com",
  "attributes": {
    "FIRSTNAME": "Jane",
    "LASTNAME": "Smith"
  },
  "listIds": [2],
  "updateEnabled": false
}
JSON
```

**Response:**
```json
{
  "id": 2
}
```

**Note:** Set `updateEnabled: true` to update the contact if it already exists.

#### Update Contact

```bash
maton api -X PUT '/brevo/v3/contacts/{identifier}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "attributes": {
    "FIRSTNAME": "Updated",
    "LASTNAME": "Name"
  }
}
JSON
```

**Note:** `{identifier}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content on success.

#### Delete Contact

```bash
maton api '/brevo/v3/contacts/{identifier}' -X DELETE
```

**Note:** `{identifier}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content on success.

#### Get Contact Campaign Stats

```bash
maton api '/brevo/v3/contacts/{identifier}/campaignStats'
```

**Note:** `{identifier}` is a placeholder. Replace it with a real value before sending the request.

### Lists API

#### List All Lists

```bash
maton api '/brevo/v3/contacts/lists'
```

**Response:**
```json
{
  "lists": [
    {
      "id": 2,
      "name": "Newsletter Subscribers",
      "folderId": 1,
      "uniqueSubscribers": 150,
      "totalBlacklisted": 2,
      "totalSubscribers": 148
    }
  ],
  "count": 1
}
```

#### Get List

```bash
maton api '/brevo/v3/contacts/lists/{listId}'
```

**Note:** `{listId}` is a placeholder. Replace it with a real value before sending the request.

#### Create List

```bash
maton api -X POST '/brevo/v3/contacts/lists' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New List",
  "folderId": 1
}
JSON
```

**Response:**
```json
{
  "id": 3
}
```

#### Update List

```bash
maton api -X PUT '/brevo/v3/contacts/lists/{listId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated List Name"
}
JSON
```

**Note:** `{listId}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content on success.

#### Delete List

```bash
maton api '/brevo/v3/contacts/lists/{listId}' -X DELETE
```

**Note:** `{listId}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content on success.

#### Get Contacts in List

```bash
maton api '/brevo/v3/contacts/lists/{listId}/contacts'
```

**Note:** `{listId}` is a placeholder. Replace it with a real value before sending the request.

#### Add Contacts to List

```bash
maton api -X POST '/brevo/v3/contacts/lists/{listId}/contacts/add' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "emails": ["contact1@example.com", "contact2@example.com"]
}
JSON
```

**Note:** `{listId}` is a placeholder. Replace it with a real value before sending the request.

#### Remove Contacts from List

```bash
maton api -X POST '/brevo/v3/contacts/lists/{listId}/contacts/remove' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "emails": ["contact1@example.com"]
}
JSON
```

**Note:** `{listId}` is a placeholder. Replace it with a real value before sending the request.

### Folders API

#### List Folders

```bash
maton api '/brevo/v3/contacts/folders'
```

**Response:**
```json
{
  "folders": [
    {
      "id": 1,
      "name": "Marketing",
      "uniqueSubscribers": 500,
      "totalSubscribers": 480,
      "totalBlacklisted": 20
    }
  ],
  "count": 1
}
```

#### Get Folder

```bash
maton api '/brevo/v3/contacts/folders/{folderId}'
```

**Note:** `{folderId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Folder

```bash
maton api -X POST '/brevo/v3/contacts/folders' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Folder"
}
JSON
```

**Response:**
```json
{
  "id": 4
}
```

#### Update Folder

```bash
maton api -X PUT '/brevo/v3/contacts/folders/{folderId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Renamed Folder"
}
JSON
```

**Note:** `{folderId}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content on success.

#### Delete Folder

```bash
maton api '/brevo/v3/contacts/folders/{folderId}' -X DELETE
```

**Note:** `{folderId}` is a placeholder. Replace it with a real value before sending the request.

Deletes folder and all lists within it. Returns 204 No Content on success.

#### Get Lists in Folder

```bash
maton api '/brevo/v3/contacts/folders/{folderId}/lists'
```

**Note:** `{folderId}` is a placeholder. Replace it with a real value before sending the request.

### Attributes API

#### List Attributes

```bash
maton api '/brevo/v3/contacts/attributes'
```

**Response:**
```json
{
  "attributes": [
    {
      "name": "FIRSTNAME",
      "category": "normal",
      "type": "text"
    },
    {
      "name": "LASTNAME",
      "category": "normal",
      "type": "text"
    }
  ]
}
```

#### Create Attribute

```bash
maton api -X POST '/brevo/v3/contacts/attributes/{category}/{attributeName}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "type": "text"
}
JSON
```

**Note:** `{category}` and `{attributeName}` are placeholders. Replace each of them with real values before sending the request.

Categories: `normal`, `transactional`, `category`, `calculated`, `global`

#### Update Attribute

```bash
maton api -X PUT '/brevo/v3/contacts/attributes/{category}/{attributeName}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "value": "new value"
}
JSON
```

**Note:** `{category}` and `{attributeName}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Attribute

```bash
maton api '/brevo/v3/contacts/attributes/{category}/{attributeName}' -X DELETE
```

**Note:** `{category}` and `{attributeName}` are placeholders. Replace each of them with real values before sending the request.

### Transactional Emails API

#### Send Email

```bash
maton api -X POST '/brevo/v3/smtp/email' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "sender": {
    "name": "John Doe",
    "email": "john@example.com"
  },
  "to": [
    {
      "email": "recipient@example.com",
      "name": "Jane Smith"
    }
  ],
  "subject": "Welcome!",
  "htmlContent": "<html><body><h1>Hello!</h1><p>Welcome to our service.</p></body></html>"
}
JSON
```

**Response:**
```json
{
  "messageId": "<202602092329.12910305853@smtp-relay.mailin.fr>"
}
```

**Request body:**
- `cc` (optional) - Carbon copy recipients
- `bcc` (optional) - Blind carbon copy recipients
- `replyTo` (optional) - Reply-to address
- `textContent` (optional) - Plain text version
- `templateId` (optional) - Use a template instead of htmlContent
- `params` (optional) - Template parameters
- `attachment` (optional) - File attachments
- `headers` (optional) - Custom headers
- `tags` (optional) - Email tags for tracking
- `scheduledAt` (optional) - Schedule for later (ISO 8601)

#### Get Transactional Emails

```bash
maton api '/brevo/v3/smtp/emails'
```

**Query parameters:**
- `email` - Filter by recipient email
- `templateId` - Filter by template
- `messageId` - Filter by message ID
- `startDate` - Start date (YYYY-MM-DD)
- `endDate` - End date (YYYY-MM-DD)
- `limit` - Results per page
- `offset` - Starting index

#### Delete Scheduled Email

```bash
maton api '/brevo/v3/smtp/email/{identifier}' -X DELETE
```

**Note:** `{identifier}` is a placeholder. Replace it with a real value before sending the request.

The identifier can be a messageId or batchId.

#### Get Email Statistics

```bash
maton api '/brevo/v3/smtp/statistics/events'
```

**Query parameters:**
- `limit` - Results per page
- `offset` - Starting index
- `startDate` - Start date
- `endDate` - End date
- `email` - Filter by recipient
- `event` - Filter by event type: `delivered`, `opened`, `clicked`, `bounced`, etc.

### Email Templates API

#### List Templates

```bash
maton api '/brevo/v3/smtp/templates'
```

**Response:**
```json
{
  "count": 1,
  "templates": [
    {
      "id": 1,
      "name": "Welcome Email",
      "subject": "Welcome {{params.name}}!",
      "isActive": true,
      "sender": {
        "name": "Company",
        "email": "noreply@company.com"
      },
      "htmlContent": "<html>...</html>",
      "createdAt": "2026-02-09 23:29:38",
      "modifiedAt": "2026-02-09 23:29:38"
    }
  ]
}
```

#### Get Template

```bash
maton api '/brevo/v3/smtp/templates/{templateId}'
```

**Note:** `{templateId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Template

```bash
maton api -X POST '/brevo/v3/smtp/templates' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "sender": {
    "name": "Company",
    "email": "noreply@company.com"
  },
  "templateName": "Welcome Email",
  "subject": "Welcome {{params.name}}!",
  "htmlContent": "<html><body><h1>Hello {{params.name}}!</h1></body></html>"
}
JSON
```

**Response:**
```json
{
  "id": 1
}
```

#### Update Template

```bash
maton api -X PUT '/brevo/v3/smtp/templates/{templateId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "templateName": "Updated Template Name",
  "subject": "New Subject"
}
JSON
```

**Note:** `{templateId}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content on success.

#### Delete Template

```bash
maton api '/brevo/v3/smtp/templates/{templateId}' -X DELETE
```

**Note:** `{templateId}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content on success.

#### Send Test Email

```bash
maton api -X POST '/brevo/v3/smtp/templates/{templateId}/sendTest' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "emailTo": ["test@example.com"]
}
JSON
```

**Note:** `{templateId}` is a placeholder. Replace it with a real value before sending the request.

### Email Campaigns API

#### List Campaigns

```bash
maton api '/brevo/v3/emailCampaigns'
```

**Query parameters:**
- `type` - Filter by type: `classic`, `trigger`
- `status` - Filter by status: `draft`, `sent`, `archive`, `queued`, `suspended`, `in_process`
- `limit` - Results per page
- `offset` - Starting index

**Response:**
```json
{
  "count": 1,
  "campaigns": [
    {
      "id": 2,
      "name": "Monthly Newsletter",
      "subject": "Our March Update",
      "type": "classic",
      "status": "draft",
      "sender": {
        "name": "Company",
        "email": "news@company.com"
      },
      "createdAt": "2026-02-09T23:29:39.000Z"
    }
  ]
}
```

#### Get Campaign

```bash
maton api '/brevo/v3/emailCampaigns/{campaignId}'
```

**Note:** `{campaignId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Campaign

```bash
maton api -X POST '/brevo/v3/emailCampaigns' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "March Newsletter",
  "subject": "Our March Update",
  "sender": {
    "name": "Company",
    "email": "news@company.com"
  },
  "htmlContent": "<html><body><h1>March News</h1></body></html>",
  "recipients": {
    "listIds": [2]
  }
}
JSON
```

**Response:**
```json
{
  "id": 2
}
```

#### Update Campaign

```bash
maton api -X PUT '/brevo/v3/emailCampaigns/{campaignId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Campaign Name",
  "subject": "Updated Subject"
}
JSON
```

**Note:** `{campaignId}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content on success.

#### Delete Campaign

```bash
maton api '/brevo/v3/emailCampaigns/{campaignId}' -X DELETE
```

**Note:** `{campaignId}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content on success.

#### Send Campaign

```bash
maton api -X POST '/brevo/v3/emailCampaigns/{campaignId}/sendNow'
```

**Note:** `{campaignId}` is a placeholder. Replace it with a real value before sending the request.

#### Send Test Email

```bash
maton api -X POST '/brevo/v3/emailCampaigns/{campaignId}/sendTest' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "emailTo": ["test@example.com"]
}
JSON
```

**Note:** `{campaignId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Campaign Status

```bash
maton api -X PUT '/brevo/v3/emailCampaigns/{campaignId}/status' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "status": "suspended"
}
JSON
```

**Note:** `{campaignId}` is a placeholder. Replace it with a real value before sending the request.

### Senders API

#### List Senders

```bash
maton api '/brevo/v3/senders'
```

**Response:**
```json
{
  "senders": [
    {
      "id": 1,
      "name": "Company",
      "email": "noreply@company.com",
      "active": true,
      "ips": []
    }
  ]
}
```

#### Get Sender

```bash
maton api '/brevo/v3/senders/{senderId}'
```

**Note:** `{senderId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Sender

```bash
maton api -X POST '/brevo/v3/senders' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Marketing",
  "email": "marketing@company.com"
}
JSON
```

#### Update Sender

```bash
maton api -X PUT '/brevo/v3/senders/{senderId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Name"
}
JSON
```

**Note:** `{senderId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Sender

```bash
maton api '/brevo/v3/senders/{senderId}' -X DELETE
```

**Note:** `{senderId}` is a placeholder. Replace it with a real value before sending the request.

### Blocked Contacts API

#### List Blocked Contacts

```bash
maton api '/brevo/v3/smtp/blockedContacts'
```

#### Unblock Contact

```bash
maton api '/brevo/v3/smtp/blockedContacts/{email}' -X DELETE
```

**Note:** `{email}` is a placeholder. Replace it with a real value before sending the request.

### Blocked Domains API

#### List Blocked Domains

```bash
maton api '/brevo/v3/smtp/blockedDomains'
```

#### Add Blocked Domain

```bash
maton api -X POST '/brevo/v3/smtp/blockedDomains' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "domain": "spam-domain.com"
}
JSON
```

#### Remove Blocked Domain

```bash
maton api '/brevo/v3/smtp/blockedDomains/{domain}' -X DELETE
```

**Note:** `{domain}` is a placeholder. Replace it with a real value before sending the request.

### Pagination

Brevo uses offset-based pagination:

```bash
maton api '/brevo/v3/contacts?limit=50&offset=0'
```

**Query parameters:**
- `limit` - Number of results per page (varies by endpoint, typically max 500)
- `offset` - Starting index (0-based)

**Response:**
```json
{
  "contacts": [...],
  "count": 150
}
```

To get the next page, increment offset by limit:
- Page 1: `offset=0&limit=50`
- Page 2: `offset=50&limit=50`
- Page 3: `offset=100&limit=50`

### Notes

- All endpoints require the `/v3/` prefix in the path
- Attribute names must be in UPPERCASE
- Contact identifiers can be email, phone, or ID
- Sender email addresses must be verified in Brevo
- Template parameters use `{{params.name}}` syntax
- PUT and DELETE operations return 204 No Content on success
- Rate limits: 300 calls/minute on free plans, higher on paid plans

### Resources

- [Brevo API Overview](https://developers.brevo.com/)
- [Brevo API Key Concepts](https://developers.brevo.com/docs/how-it-works)
- [Manage Contacts](https://developers.brevo.com/docs/synchronise-contact-lists)
- [Send Transactional Email](https://developers.brevo.com/docs/send-a-transactional-email)
- [Maton CLI Manual](https://cli.maton.ai/manual)
