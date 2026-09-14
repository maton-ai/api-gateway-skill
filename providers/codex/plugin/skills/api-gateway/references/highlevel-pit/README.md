# GoHighLevel (PIT)

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `highlevel-pit`
**Upstream base URL:** `services.leadconnectorhq.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://services.leadconnectorhq.com/locations/`
- Gateway: `https://api.maton.ai/highlevel-pit/locations/`

**Important:** GoHighLevel uses Agency tokens and Sub-Account tokens with different scopes:

- **Agency token**: Manage locations (sub-accounts), snapshots
- **Sub-Account token**: Contacts, calendars, pipelines, conversations, payments, custom fields, tags, workflows, campaigns

Use the `Maton-Connection` header to specify which token to use.

### Agency Token

#### Locations API

##### Search Locations

```bash
maton api '/highlevel-pit/locations/search?companyId={companyId}'
```

**Note:** `{companyId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `companyId` (required) - The agency's company ID
- `limit` - Results per page
- `skip` - Number to skip (offset)
- `order` - Sort order
- `email` - Filter by email

**Response:**
```json
{
  "locations": [
    {
      "id": "abc123",
      "companyId": "xyz789",
      "name": "My Sub-Account",
      "address": "123 Main St",
      "city": "San Francisco",
      "state": "CA",
      "country": "US",
      "postalCode": "94105",
      "timezone": "America/Los_Angeles",
      "email": "admin@example.com",
      "phone": "+15551234567"
    }
  ]
}
```

##### Get Location

```bash
maton api '/highlevel-pit/locations/{locationId}'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "location": {
    "id": "abc123",
    "name": "My Sub-Account",
    "address": "123 Main St",
    "city": "San Francisco",
    "state": "CA",
    "settings": {
      "allowDuplicateContact": false,
      "allowDuplicateOpportunity": false
    },
    "social": { ... },
    "permissions": { ... }
  }
}
```

##### Create Location

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

**Note:** `{companyId}` is a placeholder. Replace it with a real value before sending the request.

##### Update Location

```bash
maton api -X PUT '/highlevel-pit/locations/{locationId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Updated Name"
}
EOF
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

##### Delete Location

```bash
maton api '/highlevel-pit/locations/{locationId}' -X DELETE
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

#### Snapshots API

##### List Snapshots

```bash
maton api '/highlevel-pit/snapshots/?companyId={companyId}'
```

**Note:** `{companyId}` is a placeholder. Replace it with a real value before sending the request.

### Sub-Account Token

#### Contacts API

##### List Contacts

```bash
maton api '/highlevel-pit/contacts/?locationId={locationId}'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `locationId` (required)
- `limit` - Results per page (default 20)
- `query` - Search by name, email, or phone
- `startAfter` - Cursor for pagination (contact ID)
- `startAfterId` - Cursor for pagination

**Response:**
```json
{
  "contacts": [
    {
      "id": "abc123",
      "locationId": "loc123",
      "firstName": "John",
      "lastName": "Doe",
      "email": "john@example.com",
      "phone": "+15551234567",
      "companyName": "Acme Inc",
      "tags": ["customer", "vip"],
      "type": "lead",
      "dnd": false,
      "dateAdded": "2026-04-28T07:34:32.829Z",
      "customFields": []
    }
  ],
  "meta": {
    "total": 150,
    "startAfter": "abc123",
    "startAfterId": "abc123"
  }
}
```

##### Get Contact

```bash
maton api '/highlevel-pit/contacts/{contactId}'
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "contact": {
    "id": "abc123",
    "firstName": "John",
    "lastName": "Doe",
    "email": "john@example.com",
    "phone": "+15551234567",
    "tags": ["customer"],
    "type": "lead",
    "companyName": "Acme Inc",
    "customFields": [],
    "additionalEmails": [],
    "additionalPhones": []
  }
}
```

##### Create Contact

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

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

##### Update Contact

```bash
maton api -X PUT '/highlevel-pit/contacts/{contactId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "firstName": "Jane"
}
EOF
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

##### Delete Contact

```bash
maton api '/highlevel-pit/contacts/{contactId}' -X DELETE
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

#### Contact Tags API

##### Add Tags

```bash
maton api -X POST '/highlevel-pit/contacts/{contactId}/tags' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "tags": ["vip", "priority"]
}
JSON
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "tags": ["customer", "vip", "priority"],
  "tagsAdded": ["vip", "priority"]
}
```

##### Remove Tags

```bash
maton api '/highlevel-pit/contacts/{contactId}/tags' -X DELETE -H 'Content-Type: application/json' --input - <<'JSON'
{
  "tags": ["vip"]
}
JSON
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "tags": ["customer", "priority"],
  "tagsRemoved": ["vip"]
}
```

#### Notes API

##### Contact Notes

```bash
maton api '/highlevel-pit/contacts/{contactId}/notes'
maton api '/highlevel-pit/contacts/{contactId}/notes/{noteId}' -X DELETE
```

**Note:** `{contactId}` and `{noteId}` are placeholders. Replace each of them with real values before sending the request.

##### Create Note

```bash
maton api -X POST '/highlevel-pit/contacts/{contactId}/notes' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "body": "Spoke with client about renewal"
}
JSON
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "note": {
    "id": "note123",
    "body": "Spoke with client about renewal",
    "dateAdded": "2026-04-30T10:22:47.934Z",
    "contactId": "abc123"
  }
}
```

##### Update Note

```bash
maton api -X PUT '/highlevel-pit/contacts/{contactId}/notes/{noteId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "body": "Updated note content"
}
JSON
```

**Note:** `{contactId}` and `{noteId}` are placeholders. Replace each of them with real values before sending the request.

#### Tasks API

##### List Tasks

```bash
maton api '/highlevel-pit/contacts/{contactId}/tasks'
```

**Note:** `{contactId}` is a placeholder. Replace each of them with real values before sending the request.

###### Create Task

**IMPORTANT:** The `completed` field is required.

```bash
maton api -X POST '/highlevel-pit/contacts/{contactId}/tasks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Follow up call",
  "body": "Discuss contract renewal",
  "dueDate": "2026-06-01T10:00:00Z",
  "completed": false
}
JSON
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "task": {
    "id": "task123",
    "title": "Follow up call",
    "body": "Discuss contract renewal",
    "dueDate": "2026-06-01T10:00:00.000Z",
    "completed": false,
    "contactId": "abc123"
  }
}
```

##### Update Task

```bash
maton api -X PUT '/highlevel-pit/contacts/{contactId}/tasks/{taskId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Updated task",
  "completed": true
}
JSON
```

**Note:** `{contactId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

##### Delete Task

```bash
maton api '/highlevel-pit/contacts/{contactId}/tasks/{taskId}' -X DELETE
```

**Note:** `{contactId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

#### Opportunities API

```bash
maton api '/highlevel-pit/opportunities/{opportunityId}'
maton api '/highlevel-pit/opportunities/{opportunityId}' -X DELETE
```

**Note:** `{opportunityId}` is a placeholder. Replace it with a real value before sending the request.

##### Search Opportunities

```bash
maton api '/highlevel-pit/opportunities/search?location_id={locationId}'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `location_id` (required)
- `pipeline_id` - Filter by pipeline
- `pipeline_stage_id` - Filter by stage
- `status` - `open`, `won`, `lost`, `abandoned`, `all`
- `contact_id` - Filter by contact
- `q` - Search query
- `limit` - Results per page
- `page` - Page number

**Response:**
```json
{
  "opportunities": [
    {
      "id": "opp123",
      "name": "Enterprise Deal",
      "monetaryValue": 50000,
      "pipelineId": "pipe123",
      "pipelineStageId": "stage123",
      "status": "open",
      "contactId": "abc123",
      "contact": {
        "id": "abc123",
        "name": "John Doe",
        "email": "john@example.com"
      }
    }
  ],
  "meta": {
    "total": 25,
    "currentPage": 1,
    "nextPage": 2,
    "prevPage": null
  }
}
```

##### Create Opportunity

```bash
maton api -X POST '/highlevel-pit/opportunities/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "pipelineId": "{pipelineId}",
  "locationId": "{locationId}",
  "name": "Enterprise Deal",
  "pipelineStageId": "{stageId}",
  "status": "open",
  "contactId": "{contactId}",
  "monetaryValue": 50000
}
JSON
```

**Note:** `{pipelineId}`, `{locationId}`, `{stageId}` and `{contactId}` are placeholders. Replace each of them with real values before sending the request.

##### Update Opportunity

**IMPORTANT:** `pipelineId` is required even when not changing it.

```bash
maton api -X PUT '/highlevel-pit/opportunities/{opportunityId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "pipelineId": "{pipelineId}",
  "name": "Updated Deal",
  "monetaryValue": 75000,
  "status": "won"
}
JSON
```

**Note:** `{opportunityId}` and `{pipelineId}` are placeholders. Replace each of them with real values before sending the request.

#### Pipelines API

##### List Pipelines

```bash
maton api '/highlevel-pit/opportunities/pipelines?locationId={locationId}'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "pipelines": [
    {
      "id": "pipe123",
      "name": "Sales Pipeline",
      "stages": [
        {
          "id": "stage-uuid",
          "name": "New Lead",
          "position": 0,
          "stageWinProbability": 14.29
        },
        {
          "id": "stage-uuid-2",
          "name": "Contacted",
          "position": 1,
          "stageWinProbability": 28.57
        }
      ]
    }
  ]
}
```

#### Calendars API

##### List Calendars

```bash
maton api '/highlevel-pit/calendars/?locationId={locationId}'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "calendars": [
    {
      "id": "cal123",
      "locationId": "loc123",
      "name": "Personal Calendar",
      "calendarType": "personal",
      "eventType": "RoundRobin_OptimizeForAvailability",
      "slotDuration": 30,
      "teamMembers": [
        {
          "userId": "user123",
          "selected": true,
          "priority": 0.5
        }
      ]
    }
  ]
}
```

##### Create Calendar

```bash
maton api -X POST '/highlevel-pit/calendars/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "locationId": "{locationId}",
  "name": "Team Calendar",
  "calendarType": "personal",
  "eventType": "RoundRobin_OptimizeForAvailability",
  "teamMembers": [
    {
      "userId": "{userId}",
      "priority": 0.5,
      "selected": true
    }
  ]
}
JSON
```

**Note:** `{locationId}` and `{userId}` are placeholders. Replace each of them with real values before sending the request.

##### Update Calendar

**Note:** Do NOT include `locationId` in the update body.

```bash
maton api -X PUT '/highlevel-pit/calendars/{calendarId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Calendar",
  "calendarType": "personal",
  "eventType": "RoundRobin_OptimizeForAvailability",
  "teamMembers": [
    {
      "userId": "{userId}",
      "priority": 0.5,
      "selected": true
    }
  ]
}
JSON
```

**Note:** `{calendarId}` and `{userId}` are placeholders. Replace each of them with real values before sending the request.

##### Get Calendar Events

Requires at least one of `calendarId`, `userId`, or `groupId`.

```bash
maton api '/highlevel-pit/calendars/events?locationId={locationId}&calendarId={calendarId}&startTime={epochMs}&endTime={epochMs}'
```

**Note:** `{locationId}`, `{calendarId}` and `{epochMs}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**
- `locationId` (required)
- `calendarId`, `userId`, or `groupId` (at least one required)
- `startTime` - Start of range (epoch milliseconds)
- `endTime` - End of range (epoch milliseconds)

##### Get Free Slots

```bash
maton api '/highlevel-pit/calendars/{calendarId}/free-slots?startDate={epochMs}&endDate={epochMs}&timezone={timezone}'
```

**Note:** `{calendarId}`, `{epochMs}` and `{timezone}` are placeholders. Replace each of them with real values before sending the request.

##### Calendar Groups

```bash
maton api '/highlevel-pit/calendars/groups?locationId={locationId}'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

#### Conversations API

```bash
maton api '/highlevel-pit/conversations/{conversationId}'
maton api '/highlevel-pit/conversations/{conversationId}/messages'
```

**Note:** `{conversationId}` is a placeholder. Replace it with a real value before sending the request.

##### Search Conversations

```bash
maton api '/highlevel-pit/conversations/search?locationId={locationId}'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `locationId` (required)
- `limit` - Results per page
- `contactId` - Filter by contact
- `assignedTo` - Filter by assigned user
- `status` - Filter by status

**Response:**
```json
{
  "conversations": [
    {
      "id": "conv123",
      "locationId": "loc123",
      "contactId": "abc123",
      "fullName": "John Doe",
      "type": "TYPE_PHONE",
      "lastMessageDate": 1777361673411,
      "lastMessageType": "TYPE_NO_SHOW",
      "unreadCount": 0
    }
  ],
  "total": 5
}
```

##### Create Conversation

```bash
maton api -X POST '/highlevel-pit/conversations/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "locationId": "{locationId}",
  "contactId": "{contactId}"
}
JSON
```

**Note:** `{locationId}` and `{contactId}` are placeholders. Replace each of them with real values before sending the request.

#### Users API

##### List Users

```bash
maton api '/highlevel-pit/users/?locationId={locationId}'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "users": [
    {
      "id": "user123",
      "name": "Admin User",
      "firstName": "Admin",
      "lastName": "User",
      "email": "admin@example.com",
      "phone": "+15551234567",
      "roles": {
        "type": "admin",
        "role": "admin",
        "locationIds": ["loc123"]
      }
    }
  ]
}
```

#### Location Tags API

##### List Tags

```bash
maton api '/highlevel-pit/locations/{locationId}/tags'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "tags": [
    {
      "id": "tag123",
      "name": "VIP Customer",
      "locationId": "loc123"
    }
  ]
}
```

##### Create Tag

```bash
maton api -X POST '/highlevel-pit/locations/{locationId}/tags' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Tag"
}
JSON
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

##### Update Tag

```bash
maton api -X PUT '/highlevel-pit/locations/{locationId}/tags/{tagId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Tag"
}
JSON
```

**Note:** `{locationId}` and `{tagId}` are placeholders. Replace each of them with real values before sending the request.

#### Custom Fields API

##### List Custom Fields

```bash
maton api '/highlevel-pit/locations/{locationId}/customFields'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "customFields": [
    {
      "id": "cf123",
      "name": "Customer ID",
      "fieldKey": "contact.customer_id",
      "dataType": "TEXT",
      "model": "contact",
      "position": 50
    }
  ]
}
```

##### Create Custom Field

```bash
maton api -X POST '/highlevel-pit/locations/{locationId}/customFields' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Customer ID",
  "dataType": "TEXT",
  "model": "contact"
}
JSON
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

Valid `dataType` values: `TEXT`, `LARGE_TEXT`, `NUMERICAL`, `PHONE`, `MONETORY`, `CHECKBOX`, `SINGLE_OPTIONS`, `MULTIPLE_OPTIONS`, `FLOAT`, `DATE`, `TEXTBOX_LIST`, `FILE_UPLOAD`, `SIGNATURE`

Valid `model` values: `contact`, `opportunity`

##### Update Custom Field

```bash
maton api -X PUT '/highlevel-pit/locations/{locationId}/customFields/{customFieldId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Field Name"
}
JSON
```

**Note:** `{locationId}` and `{customFieldId}` are placeholders. Replace each of them with real values before sending the request.

#### Custom Values API

##### List Custom Values

```bash
maton api '/highlevel-pit/locations/{locationId}/customValues'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "customValues": [
    {
      "id": "cv123",
      "name": "Company Tagline",
      "fieldKey": "{{ custom_values.company_tagline }}",
      "value": "We build great things",
      "locationId": "loc123"
    }
  ]
}
```

##### Create Custom Value

```bash
maton api -X POST '/highlevel-pit/locations/{locationId}/customValues' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Company Tagline",
  "value": "We build great things"
}
JSON
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

##### Update Custom Value

```bash
maton api -X PUT '/highlevel-pit/locations/{locationId}/customValues/{customValueId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Name",
  "value": "Updated value"
}
JSON
```

**Note:** `{locationId}` and `{customValueId}` are placeholders. Replace each of them with real values before sending the request.

#### Businesses API

##### List Businesses

```bash
maton api '/highlevel-pit/businesses/?locationId={locationId}'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "success": true,
  "businesses": [
    {
      "id": "biz123",
      "name": "Acme Inc",
      "locationId": "loc123",
      "city": "Los Angeles",
      "website": "www.acme.com",
      "phone": "+15551234567",
      "email": "info@acme.com"
    }
  ]
}
```

##### Create Business

```bash
maton api -X POST '/highlevel-pit/businesses/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "locationId": "{locationId}",
  "name": "New Business",
  "city": "San Francisco",
  "phone": "+15551234567",
  "email": "info@newbiz.com",
  "website": "www.newbiz.com"
}
JSON
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

##### Update Business

```bash
maton api -X PUT '/highlevel-pit/businesses/{businessId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Business",
  "city": "Los Angeles"
}
JSON
```

**Note:** `{businessId}` is a placeholder. Replace it with a real value before sending the request.

#### Products API

##### Get Product

```bash
maton api '/highlevel-pit/products/{productId}?locationId={locationId}'
```

**Note:** `{productId}` and `{locationId}` are placeholders. Replace each of them with real values before sending the request.

**Note:** `locationId` query parameter is required even for single product retrieval.

##### Create Product

```bash
maton api -X POST '/highlevel-pit/products/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "locationId": "{locationId}",
  "name": "Digital Course",
  "description": "Online training program",
  "productType": "DIGITAL"
}
JSON
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

#### Invoices API

##### List Invoices

**IMPORTANT:** Both `offset` and `altId`/`altType` are required.

```bash
maton api '/highlevel-pit/invoices/?altId={locationId}&altType=location&limit=20&offset=0'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.


##### Get Invoice

```bash
maton api '/highlevel-pit/invoices/{invoiceId}?altId={locationId}&altType=location'
```

**Note:** `{invoiceId}` and `{locationId}` are placeholders. Replace each of them with real values before sending the request.

#### Payments

```bash
maton api '/highlevel-pit/payments/orders?altId={locationId}&altType=location'
maton api '/highlevel-pit/payments/transactions?altId={locationId}&altType=location'
maton api '/highlevel-pit/payments/subscriptions?altId={locationId}&altType=location'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

#### Trigger Links API

##### Create Link

```bash
maton api -X POST '/highlevel-pit/links/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "locationId": "{locationId}",
  "name": "Survey Link",
  "redirectTo": "https://example.com/survey"
}
JSON
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "link": {
    "id": "link123",
    "name": "Survey Link",
    "redirectTo": "https://example.com/survey",
    "fieldKey": "{{trigger_link.link123}}"
  }
}
```

##### Update Link

```bash
maton api -X PUT '/highlevel-pit/links/{linkId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Link",
  "redirectTo": "https://updated.com"
}
JSON
```

**Note:** `{linkId}` is a placeholder. Replace it with a real value before sending the request.

#### Funnels API

##### List Funnels

```bash
maton api '/highlevel-pit/funnels/funnel/list?locationId={locationId}'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "funnels": [...],
  "count": 5
}
```

#### Accounts API

##### List Accounts

```bash
maton api '/highlevel-pit/social-media-posting/{locationId}/accounts'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "success": true,
  "results": {
    "accounts": [...],
    "groups": [...]
  }
}
```

##### List Categories

```bash
maton api '/highlevel-pit/social-media-posting/{locationId}/categories'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

#### Workflows API

##### List Workflows

```bash
maton api '/highlevel-pit/workflows/?locationId={locationId}'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

#### Campaigns API

##### List Campaigns

```bash
maton api '/highlevel-pit/campaigns/?locationId={locationId}'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

#### Forms API

##### List Forms

```bash
maton api '/highlevel-pit/forms/?locationId={locationId}'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

#### Surveys API

##### List Surveys

```bash
maton api '/highlevel-pit/surveys/?locationId={locationId}'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

#### Files API

##### List Files

**IMPORTANT:** The `type` parameter is required.

```bash
maton api '/highlevel-pit/medias/files?altId={locationId}&altType=location&type=file&limit=20'
```

**Note:** `{locationId}` is a placeholder. Replace it with a real value before sending the request.

Valid `type` values: `file`, `image`, `video`, `audio`

### Notes

- Two token types with different scopes — use `Maton-Connection` header
- Most sub-account endpoints require `locationId` query parameter
- Payment/invoice endpoints use `altId` + `altType=location` instead of `locationId`
- Social media endpoints put `locationId` in the URL path, not as a query parameter
- Calendar events use epoch milliseconds for time parameters
- Calendar update must NOT include `locationId` in body
- Contact task creation requires `completed` boolean field
- Opportunity update requires `pipelineId` even when not changing it
- All delete operations return HTTP 200

### Resources

- [GoHighLevel API Documentation](https://highlevel.stoplight.io/docs/integrations/)
- [GoHighLevel Marketplace](https://marketplace.gohighlevel.com/docs/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
