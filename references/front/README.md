# Front Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `front`
**Base URL proxied:** `api2.frontapp.com`

## API Path Pattern

```
/front/{resource}
```

## Common Endpoints

### Company / Me

#### Get Current Company
```bash
maton api '/front/me'
```

### Teammates

#### List Teammates
```bash
maton api '/front/teammates'
```

#### Get Teammate
```bash
maton api '/front/teammates/{teammate_id}'
```

### Teams

#### List Teams
```bash
maton api '/front/teams'
```

### Inboxes

#### List Inboxes
```bash
maton api '/front/inboxes'
```

#### Get Inbox
```bash
maton api '/front/inboxes/{inbox_id}'
```

#### Create Inbox
```bash
maton api -X POST '/front/inboxes' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "New Inbox",
  "teammate_ids": ["tea_abc123"]
}
EOF
```

### Channels

#### List Channels
```bash
maton api '/front/channels'
```

#### Get Channel
```bash
maton api '/front/channels/{channel_id}'
```

### Conversations

#### List Conversations
```bash
maton api '/front/conversations'
maton api '/front/conversations?q=search_term'
```

#### Get Conversation
```bash
maton api '/front/conversations/{conversation_id}'
```

#### Update Conversation
```bash
maton api -X PATCH '/front/conversations/{conversation_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "assignee_id": "tea_abc123",
  "status": "archived"
}
EOF
```

#### Update Assignee
```bash
maton api -X PUT '/front/conversations/{conversation_id}/assignee' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"assignee_id": "tea_abc123"}
EOF
```

### Messages

#### Get Message
```bash
maton api '/front/messages/{message_id}'
```

#### Send Reply
```bash
maton api -X POST '/front/conversations/{conversation_id}/messages' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "author_id": "tea_abc123",
  "body": "Reply content",
  "type": "reply"
}
EOF
```

#### Send New Message
```bash
maton api -X POST '/front/channels/{channel_id}/messages' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "author_id": "tea_abc123",
  "to": ["recipient@example.com"],
  "subject": "Subject",
  "body": "Message body"
}
EOF
```

### Contacts

#### List Contacts
```bash
maton api '/front/contacts'
maton api '/front/contacts?q=search_term'
```

#### Get Contact
```bash
maton api '/front/contacts/{contact_id}'
```

#### Create Contact
```bash
maton api -X POST '/front/contacts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "John Doe",
  "handles": [{"source": "email", "handle": "john@example.com"}]
}
EOF
```

#### Update Contact
```bash
maton api -X PATCH '/front/contacts/{contact_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"name": "Updated Name"}
EOF
```

#### Delete Contact
```bash
maton api -X DELETE '/front/contacts/{contact_id}'
```

### Tags

#### List Tags
```bash
maton api '/front/tags'
```

#### Get Tag
```bash
maton api '/front/tags/{tag_id}'
```

#### Create Tag
```bash
maton api -X POST '/front/tags' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Urgent",
  "highlight": "red"
}
EOF
```

#### Update Tag
```bash
maton api -X PATCH '/front/tags/{tag_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"name": "Updated Tag"}
EOF
```

#### Delete Tag
```bash
maton api -X DELETE '/front/tags/{tag_id}'
```

### Accounts

#### List Accounts
```bash
maton api '/front/accounts'
```

#### Get Account
```bash
maton api '/front/accounts/{account_id}'
```

#### Create Account
```bash
maton api -X POST '/front/accounts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Acme Corp",
  "domains": ["acme.com"]
}
EOF
```

### Comments

#### List Conversation Comments
```bash
maton api '/front/conversations/{conversation_id}/comments'
```

#### Create Comment
```bash
maton api -X POST '/front/conversations/{conversation_id}/comments' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "author_id": "tea_abc123",
  "body": "Internal note"
}
EOF
```

## Pagination

Cursor-based pagination:

```bash
maton api '/front/contacts?page_token={token}'
```

Response includes:
```json
{
  "_pagination": {"next": "https://...?page_token=abc123"},
  "_results": [...]
}
```

## Notes

- Resource ID prefixes: `tea_` (teammate), `tim_` (team), `inb_` (inbox), `cha_` (channel), `cnv_` (conversation), `msg_` (message), `crd_` (contact), `tag_` (tag), `cmp_` (company)
- Timestamps are Unix timestamps (seconds)
- Responses include `_links` with related resource URLs
- Gateway proxies to company-specific subdomain

## Resources

- [Front API Reference](https://dev.frontapp.com/reference/introduction)
- [Front API Authentication](https://dev.frontapp.com/docs/authentication)
