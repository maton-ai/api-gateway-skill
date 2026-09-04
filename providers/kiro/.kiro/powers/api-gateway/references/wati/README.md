# WATI Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `wati`
**Base URL proxied:** `{tenant}.wati.io`

## API Path Pattern

```
/wati/api/v1/{endpoint}
/wati/api/v2/{endpoint}
```

Both v1 and v2 endpoints are supported. v2 provides enhanced response formats with message tracking IDs.

## Common Endpoints

### Contacts

#### Get Contacts
```bash
maton api '/wati/api/v1/getContacts?pageSize=10&pageNumber=1'
```

Optional filters: `name`, `attribute`, `createdDate`

#### Add Contact
```bash
maton api -X POST '/wati/api/v1/addContact/{whatsappNumber}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "John Doe",
  "customParams": [
    {"name": "member", "value": "VIP"}
  ]
}
EOF
```

#### Update Contact Attributes
```bash
maton api -X POST '/wati/api/v1/updateContactAttributes/{whatsappNumber}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "customParams": [
    {"name": "member", "value": "VIP"}
  ]
}
EOF
```

### Messages

#### Get Messages
```bash
maton api '/wati/api/v1/getMessages/{whatsappNumber}?pageSize=10&pageNumber=1'
```

#### Send Session Message
```bash
maton api -X POST '/wati/api/v1/sendSessionMessage/{whatsappNumber}' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
messageText=Hello%20World
EOF
```

#### Send Session File
```bash
# multipart/form-data is not expressible with `maton api`; call the gateway directly with `MATON_API_KEY` (see SKILL.md appendix).
python <<'EOF'
import json, mimetypes, os, urllib.request, uuid

# Maton API key from the environment; never print, log, or persist it.
TOKEN = os.environ["MATON_API_KEY"]

# Exactly the path the user gave — never a discovered or inferred one.
file_path = '/path/to/document.pdf'
whatsapp_number = '{whatsappNumber}'

boundary = uuid.uuid4().hex
mime = mimetypes.guess_type(file_path)[0] or 'application/octet-stream'
with open(file_path, 'rb') as f:
    body = (f'--{boundary}\r\nContent-Disposition: form-data; name="file"; filename="{os.path.basename(file_path)}"\r\n'
            f'Content-Type: {mime}\r\n\r\n').encode() + f.read() + f'\r\n--{boundary}--\r\n'.encode()

req = urllib.request.Request(f'https://api.maton.ai/wati/api/v1/sendSessionFile/{whatsapp_number}', data=body, method='POST')
req.add_header('Authorization', f'Bearer {TOKEN}')
req.add_header('User-Agent', 'maton-gateway-skill/1.2')
req.add_header('Content-Type', f'multipart/form-data; boundary={boundary}')
print(json.dumps(json.load(urllib.request.urlopen(req)), indent=2))
EOF
```

### Message Templates

#### Get Message Templates
```bash
maton api '/wati/api/v1/getMessageTemplates?pageSize=10&pageNumber=1'
```

#### Send Template Message
```bash
maton api -X POST '/wati/api/v1/sendTemplateMessage?whatsappNumber={whatsappNumber}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "template_name": "order_update",
  "broadcast_name": "order_update",
  "parameters": [
    {"name": "name", "value": "John"},
    {"name": "ordernumber", "value": "12345"}
  ]
}
EOF
```

#### Send Template Messages (Bulk)
```bash
maton api -X POST '/wati/api/v1/sendTemplateMessages' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "template_name": "order_update",
  "broadcast_name": "order_update",
  "receivers": [
    {
      "whatsappNumber": "14155551234",
      "customParams": [{"name": "name", "value": "John"}]
    }
  ]
}
EOF
```

### Message Templates (v2)

v2 endpoints return `localMessageId` for tracking.

#### Send Template Message (v2)
```bash
maton api -X POST '/wati/api/v2/sendTemplateMessage?whatsappNumber={whatsappNumber}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "template_name": "order_update",
  "broadcast_name": "order_update",
  "parameters": [{"name": "name", "value": "John"}]
}
EOF
```

#### Send Template Messages (v2 - Bulk)
```bash
maton api -X POST '/wati/api/v2/sendTemplateMessages' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "template_name": "order_update",
  "broadcast_name": "order_update",
  "receivers": [
    {
      "whatsappNumber": "14155551234",
      "customParams": [{"name": "name", "value": "John"}]
    }
  ]
}
EOF
```

### Interactive Messages

#### Send Interactive Buttons Message
```bash
maton api -X POST '/wati/api/v1/sendInteractiveButtonsMessage?whatsappNumber={whatsappNumber}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "header": {"type": "text", "text": "Header"},
  "body": "Message body",
  "footer": "Footer text",
  "buttons": [{"text": "Button 1"}]
}
EOF
```

#### Send Interactive List Message
```bash
maton api -X POST '/wati/api/v1/sendInteractiveListMessage?whatsappNumber={whatsappNumber}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "header": "Header",
  "body": "Message body",
  "footer": "Footer",
  "buttonText": "View Options",
  "sections": [
    {
      "title": "Section 1",
      "rows": [{"title": "Option A", "description": "Description"}]
    }
  ]
}
EOF
```

### Operators

#### Assign Operator
```bash
maton api -X POST '/wati/api/v1/assignOperator?email=agent@example.com&whatsappNumber={whatsappNumber}'
```

### Media

#### Get Media
```bash
maton api '/wati/api/v1/getMedia?fileName={fileName}'
```

## Pagination

Uses page-based pagination:

```bash
maton api '/wati/api/v1/getContacts?pageSize=50&pageNumber=1'
```

**Parameters:**
- `pageSize` - Results per page
- `pageNumber` - Page number (1-indexed)

## Notes

- WhatsApp numbers should include country code without + or spaces (e.g., `14155551234`)
- Session messages require an active 24-hour conversation window
- Template messages require pre-approved WhatsApp templates
- Interactive messages have character limits enforced by WhatsApp

## Resources

- [WATI API Documentation](https://docs.wati.io/reference/introduction)
- [WATI Help Center](https://docs.wati.io/)
