# Front

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `front`
**Upstream base URL:** `api2.frontapp.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api2.frontapp.com/me`
- Gateway: `https://api.maton.ai/front/me`

### User Info API

#### Get Current Company

```bash
maton api '/front/me'
```

**Response:**
```json
{
  "_links": {"self": "https://company.api.frontapp.com/me"},
  "name": "Company Name",
  "id": "cmp_12345"
}
```

### Teammates API

#### List Teammates

```bash
maton api '/front/teammates'
```

**Response:**
```json
{
  "_pagination": {"next": null},
  "_results": [
    {
      "id": "tea_pa3u0",
      "email": "user@example.com",
      "username": "username",
      "first_name": "John",
      "last_name": "Doe",
      "is_admin": true,
      "is_available": true,
      "is_blocked": false,
      "type": "user"
    }
  ]
}
```

#### Get Teammate

```bash
maton api '/front/teammates/{teammate_id}'
```

**Note:** `{teammate_id}` is a placeholder. Replace it with a real value before sending the request.

### Teams API

#### List Teams

```bash
maton api '/front/teams'
```

**Response:**
```json
{
  "_pagination": {"next": null},
  "_results": [
    {
      "id": "tim_9p8dk",
      "name": "Customer Support"
    },
    {
      "id": "tim_9p8fc",
      "name": "Sales"
    }
  ]
}
```

### Inboxes API

#### List Inboxes

```bash
maton api '/front/inboxes'
```

**Response:**
```json
{
  "_pagination": {"next": null},
  "_results": [
    {
      "id": "inb_lzrag",
      "name": "Support",
      "is_private": false,
      "is_public": true,
      "address": "support@company.com",
      "send_as": "support@company.com",
      "type": "smtp"
    }
  ]
}
```

#### Get Inbox

```bash
maton api '/front/inboxes/{inbox_id}'
```

**Note:** `{inbox_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Inbox

```bash
maton api -X POST '/front/inboxes' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Inbox",
  "teammate_ids": ["tea_abc123"]
}
JSON
```

### Channels API

#### List Channels

```bash
maton api '/front/channels'
```

**Response:**
```json
{
  "_pagination": {"next": null},
  "_results": [
    {
      "id": "cha_ogobs",
      "name": "support@company.com",
      "address": "support@company.com",
      "send_as": "support@company.com",
      "type": "smtp",
      "is_private": false,
      "is_valid": true
    }
  ]
}
```

#### Get Channel

```bash
maton api '/front/channels/{channel_id}'
```

**Note:** `{channel_id}` is a placeholder. Replace it with a real value before sending the request.

### Conversations API

#### List Conversations

```bash
maton api '/front/conversations'
```

**Query parameters:**
- `q` - Search query
- `page_token` - Pagination token

**Response:**
```json
{
  "_pagination": {"next": null},
  "_results": [
    {
      "id": "cnv_abc123",
      "subject": "Help with order",
      "status": "open",
      "assignee": {
        "id": "tea_pa3u0",
        "email": "agent@company.com"
      },
      "recipient": {
        "handle": "customer@example.com"
      },
      "last_message": {
        "body": "Message content..."
      },
      "created_at": 1774828390.948
    }
  ]
}
```

#### Get Conversation

```bash
maton api '/front/conversations/{conversation_id}'
```

**Note:** `{conversation_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Conversation

```bash
maton api -X PATCH '/front/conversations/{conversation_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "assignee_id": "tea_abc123",
  "inbox_id": "inb_xyz789",
  "status": "archived",
  "tag_ids": ["tag_123"]
}
JSON
```

**Note:** `{conversation_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Assignee

```bash
maton api -X PUT '/front/conversations/{conversation_id}/assignee' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"assignee_id": "tea_abc123"}
EOF
```

**Note:** `{conversation_id}` is a placeholder. Replace it with a real value before sending the request.

### Messages API

#### Get Message

```bash
maton api '/front/messages/{message_id}'
```

**Note:** `{message_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": "msg_abc123",
  "type": "email",
  "is_inbound": true,
  "created_at": 1774828390.948,
  "blurb": "Message preview...",
  "body": "Full message content...",
  "author": {
    "id": "tea_pa3u0",
    "email": "agent@company.com"
  },
  "recipients": [
    {
      "handle": "customer@example.com",
      "role": "to"
    }
  ]
}
```

#### Send Reply

```bash
maton api -X POST '/front/conversations/{conversation_id}/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "author_id": "tea_abc123",
  "body": "Thank you for reaching out!",
  "type": "reply"
}
JSON
```

**Note:** `{conversation_id}` is a placeholder. Replace it with a real value before sending the request.

#### Send New Message

```bash
maton api -X POST '/front/channels/{channel_id}/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "author_id": "tea_abc123",
  "to": ["customer@example.com"],
  "subject": "Following up",
  "body": "Hi, just following up on your inquiry..."
}
JSON
```

**Note:** `{channel_id}` is a placeholder. Replace it with a real value before sending the request.

### Contacts API

#### List Contacts

```bash
maton api '/front/contacts'
```

**Query parameters:**
- `q` - Search query (email, name, phone)
- `page_token` - Pagination token

**Response:**
```json
{
  "_pagination": {"next": null},
  "_results": [
    {
      "id": "crd_54wgwiw",
      "name": "John Doe",
      "description": "",
      "handles": [
        {"source": "email", "handle": "john@example.com"}
      ],
      "groups": [],
      "updated_at": 1774828390.948,
      "is_private": false
    }
  ]
}
```

#### Get Contact

```bash
maton api '/front/contacts/{contact_id}'
```

**Note:** `{contact_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Contact

```bash
maton api -X POST '/front/contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Jane Smith",
  "handles": [
    {"source": "email", "handle": "jane@example.com"}
  ],
  "description": "VIP customer"
}
JSON
```

#### Update Contact

```bash
maton api -X PATCH '/front/contacts/{contact_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Jane Smith-Jones",
  "description": "Updated description"
}
JSON
```

**Note:** `{contact_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Contact

```bash
maton api '/front/contacts/{contact_id}' -X DELETE
```

**Note:** `{contact_id}` is a placeholder. Replace it with a real value before sending the request.

### Tags API

#### List Tags

```bash
maton api '/front/tags'
```

**Response:**
```json
{
  "_pagination": {"next": null},
  "_results": [
    {
      "id": "tag_6v3mzs",
      "name": "Urgent",
      "highlight": "red",
      "description": "High priority items",
      "is_private": false,
      "is_visible_in_conversation_lists": true
    }
  ]
}
```

#### Get Tag

```bash
maton api '/front/tags/{tag_id}'
```

**Note:** `{tag_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Tag

```bash
maton api -X POST '/front/tags' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Follow-up",
  "highlight": "blue",
  "description": "Needs follow-up"
}
JSON
```

#### Update Tag

```bash
maton api -X PATCH '/front/tags/{tag_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Tag Name",
  "highlight": "green"
}
JSON
```

**Note:** `{tag_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Tag

```bash
maton api '/front/tags/{tag_id}' -X DELETE
```

**Note:** `{tag_id}` is a placeholder. Replace it with a real value before sending the request.

### Accounts API

#### List Accounts

```bash
maton api '/front/accounts'
```

#### Get Account

```bash
maton api '/front/accounts/{account_id}'
```

**Note:** `{account_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Account

```bash
maton api -X POST '/front/accounts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Acme Corp",
  "description": "Enterprise customer",
  "domains": ["acme.com"]
}
JSON
```

#### Update Account

```bash
maton api -X PATCH '/front/accounts/{account_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Acme Corporation",
  "description": "Updated description"
}
JSON
```

**Note:** `{account_id}` is a placeholder. Replace it with a real value before sending the request.

### Comments API

#### List Conversation Comments

```bash
maton api '/front/conversations/{conversation_id}/comments'
```

**Note:** `{conversation_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Comment

```bash
maton api -X POST '/front/conversations/{conversation_id}/comments' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "author_id": "tea_abc123",
  "body": "Internal note: Customer is a VIP"
}
JSON
```

**Note:** `{conversation_id}` is a placeholder. Replace it with a real value before sending the request.

### Pagination

Cursor-based pagination:

```bash
maton api '/front/contacts?page_token={token}'
```

**Note:** `{token}` is a placeholder. Replace it with a real value before sending the request.

Response includes:
```json
{
  "_pagination": {"next": "https://...?page_token=abc123"},
  "_results": [...]
}
```

### Notes

- Resource ID prefixes: `tea_` (teammate), `tim_` (team), `inb_` (inbox), `cha_` (channel), `cnv_` (conversation), `msg_` (message), `crd_` (contact), `tag_` (tag), `cmp_` (company)
- Timestamps are Unix timestamps (seconds)
- Responses include `_links` with related resource URLs
- Gateway proxies to company-specific subdomain

### Resources

- [Front API Reference](https://dev.frontapp.com/reference/introduction)
- [Front API Authentication](https://dev.frontapp.com/docs/authentication)
- [Maton CLI Manual](https://cli.maton.ai/manual)
