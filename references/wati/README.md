# WATI

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `wati`
**Upstream base URL:** `{tenant}.wati.io`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://{tenant}.wati.io/api/v1/sendTemplateMessages`
- Gateway: `https://api.maton.ai/wati/api/v1/sendTemplateMessages`

**Note:** Both v1 and v2 endpoints are supported. v2 provides enhanced response formats with message tracking IDs.

### Contacts API

#### Get Contacts

```bash
maton api '/wati/api/v1/getContacts?pageSize=10&pageNumber=1'
```

Optional filters: `name`, `attribute`, `createdDate`

**Query parameters:**
- `pageSize` - Number of results per page
- `pageNumber` - Page number (1-indexed)
- `name` (optional) - Filter by contact name
- `attribute` (optional) - Filter by attribute (format: `[{"name": "name", "operator": "contain", "value": "test"}]`)
- `createdDate` (optional) - Filter by created date (YYYY-MM-DD)

**Attribute operators:** `contain`, `notContain`, `exist`, `notExist`, `==`, `!=`, `valid`, `invalid`

#### Add Contact

```bash
maton api -X POST '/wati/api/v1/addContact/{whatsappNumber}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "John Doe",
  "customParams": [
    {
      "name": "member",
      "value": "VIP"
    }
  ]
}
JSON
```

**Note:** `{whatsappNumber}` is a placeholder. Replace it with a real value before sending the request.

#### Update Contact Attributes

```bash
maton api -X POST '/wati/api/v1/updateContactAttributes/{whatsappNumber}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "customParams": [
    {
      "name": "member",
      "value": "VIP"
    }
  ]
}
JSON
```

**Note:** `{whatsappNumber}` is a placeholder. Replace it with a real value before sending the request.

### Messages API

#### Get Messages

```bash
maton api '/wati/api/v1/getMessages/{whatsappNumber}?pageSize=10&pageNumber=1'
```

**Note:** `{whatsappNumber}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `pageSize` - Number of results per page
- `pageNumber` - Page number (1-indexed)

#### Send Session Message

Send a text message within an active session (24-hour window):

```bash
maton api -X POST '/wati/api/v1/sendSessionMessage/{whatsappNumber}' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
messageText=Hello%20from%20WATI!
BODY
```

**Note:** `{whatsappNumber}` is a placeholder. Replace it with a real value before sending the request.

#### Send Session File

```bash
# `maton api` sends a body verbatim but does not build a multipart envelope: assemble it
# first, then hand the result to --input. Nothing here handles a credential — the CLI injects it.
# Read only the path the user gave. The assembled body contains a copy of that file's bytes,
# so it is written under /tmp and deleted as soon as the upload returns.
FILE=/path/to/document.pdf            # exactly the path the user gave, never a discovered one
BOUNDARY="maton-$$"
{
  printf -- '--%s\r\nContent-Disposition: form-data; name="file"; filename="%s"\r\nContent-Type: application/octet-stream\r\n\r\n' "$BOUNDARY" "$(basename "$FILE")"
  cat "$FILE"
  printf -- '\r\n'
  printf -- '--%s--\r\n' "$BOUNDARY"
} > /tmp/wati-upload.body

maton api -X POST '/wati/api/v1/sendSessionFile/{whatsappNumber}' \
  -H "Content-Type: multipart/form-data; boundary=$BOUNDARY" \
  --input /tmp/wati-upload.body
rm -f /tmp/wati-upload.body
```

**Note:** `{whatsappNumber}` is a placeholder. Replace it with a real value before sending the request.

#### Post Session File

Send a file within an active session:

```bash
# maton api sends a body verbatim but does not build a multipart envelope:
# assemble it first, then hand the file to --input.
BOUNDARY="maton-$$"
{
  printf -- '--%s\r\nContent-Disposition: form-data; name="file"; filename="document.pdf"\r\nContent-Type: application/octet-stream\r\n\r\n' "$BOUNDARY"
  cat document.pdf
  printf -- '\r\n'
  printf -- '--%s--\r\n' "$BOUNDARY"
} > /tmp/upload.body

maton api -X POST '/wati/api/v1/sendSessionFile/{whatsappNumber}?caption=Check%20this%20out' \
  -H "Content-Type: multipart/form-data; boundary=$BOUNDARY" \
  --input /tmp/upload.body
```

**Note:** `{whatsappNumber}` is a placeholder. Replace it with a real value before sending the request.

### Message Templates API

#### Get Message Templates

```bash
maton api '/wati/api/v1/getMessageTemplates?pageSize=10&pageNumber=1'
```

#### Send Template Message

Send a pre-approved template message to a single contact:

```bash
maton api -X POST '/wati/api/v1/sendTemplateMessage?whatsappNumber={whatsappNumber}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "template_name": "order_update",
  "broadcast_name": "order_update",
  "parameters": [
    {
      "name": "name",
      "value": "John"
    },
    {
      "name": "ordernumber",
      "value": "12345"
    }
  ]
}
JSON
```

**Note:** `{whatsappNumber}` is a placeholder. Replace it with a real value before sending the request.

#### Send Template Messages (Bulk)

Send template messages to multiple contacts:

```bash
maton api -X POST '/wati/api/v1/sendTemplateMessages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "template_name": "order_update",
  "broadcast_name": "order_update",
  "receivers": [
    {
      "whatsappNumber": "14155551234",
      "customParams": [
        {
          "name": "name",
          "value": "John"
        },
        {
          "name": "ordernumber",
          "value": "12345"
        }
      ]
    },
    {
      "whatsappNumber": "14155555678",
      "customParams": [
        {
          "name": "name",
          "value": "Jane"
        },
        {
          "name": "ordernumber",
          "value": "67890"
        }
      ]
    }
  ]
}
JSON
```

#### Send Template Message via CSV

```bash
# maton api sends a body verbatim but does not build a multipart envelope:
# assemble it first, then hand the file to --input.
BOUNDARY="maton-$$"
{
  printf -- '--%s\r\nContent-Disposition: form-data; name="whatsapp_numbers_csv"; filename="contacts.csv"\r\nContent-Type: application/octet-stream\r\n\r\n' "$BOUNDARY"
  cat contacts.csv
  printf -- '\r\n'
  printf -- '--%s--\r\n' "$BOUNDARY"
} > /tmp/upload.body

maton api -X POST '/wati/api/v1/sendTemplateMessageCSV?template_name=order_update&broadcast_name=order_update' \
  -H "Content-Type: multipart/form-data; boundary=$BOUNDARY" \
  --input /tmp/upload.body
```

v2 endpoints return `localMessageId` for tracking.

#### Send Template Message (v2)

```bash
maton api -X POST '/wati/api/v2/sendTemplateMessage?whatsappNumber={whatsappNumber}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "template_name": "order_update",
  "broadcast_name": "order_update",
  "parameters": [
    {
      "name": "name",
      "value": "John"
    }
  ]
}
JSON
```

**Note:** `{whatsappNumber}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "result": true,
  "error": null,
  "templateName": "order_update",
  "receivers": [
    {
      "localMessageId": "38aca0c0-f80a-409c-81ed-607fa5206529",
      "waId": "14155551234",
      "isValidWhatsAppNumber": true,
      "errors": []
    }
  ],
  "parameters": [
    {"name": "name", "value": "John"}
  ]
}
```

#### Send Template Messages (v2 - Bulk)

```bash
maton api -X POST '/wati/api/v2/sendTemplateMessages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "template_name": "order_update",
  "broadcast_name": "order_update",
  "receivers": [
    {
      "whatsappNumber": "14155551234",
      "customParams": [
        {"name": "name", "value": "John"}
      ]
    },
    {
      "whatsappNumber": "14155555678",
      "customParams": [
        {"name": "name", "value": "Jane"}
      ]
    }
  ]
}
JSON
```

**Response:**
```json
{
  "result": true,
  "error": null,
  "templateName": "order_update",
  "receivers": [
    {
      "localMessageId": "c486f386-d86d-431d-aa3b-fb1b6c494e58",
      "waId": "14155551234",
      "isValidWhatsAppNumber": true,
      "errors": []
    },
    {
      "localMessageId": "d597f497-e97e-542e-bb4c-718gb6d5a069",
      "waId": "14155555678",
      "isValidWhatsAppNumber": true,
      "errors": []
    }
  ]
}
```

### Interactive Messages API

#### Send Interactive Buttons Message

```bash
maton api -X POST '/wati/api/v1/sendInteractiveButtonsMessage?whatsappNumber={whatsappNumber}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "header": {
    "type": "text",
    "text": "Order Status"
  },
  "body": "Your order #12345 is ready. What would you like to do?",
  "footer": "Reply within 24 hours",
  "buttons": [
    {
      "text": "Track Order"
    },
    {
      "text": "Contact Support"
    }
  ]
}
JSON
```

**Note:** `{whatsappNumber}` is a placeholder. Replace it with a real value before sending the request.

#### Send Interactive List Message

```bash
maton api -X POST '/wati/api/v1/sendInteractiveListMessage?whatsappNumber={whatsappNumber}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "header": "Choose an option",
  "body": "Please select from the menu below",
  "footer": "Powered by WATI",
  "buttonText": "View Options",
  "sections": [
    {
      "title": "Products",
      "rows": [
        {
          "title": "Product A",
          "description": "Best seller item"
        },
        {
          "title": "Product B",
          "description": "New arrival"
        }
      ]
    }
  ]
}
JSON
```

**Note:** `{whatsappNumber}` is a placeholder. Replace it with a real value before sending the request.

### Operators API

#### Assign Operator

```bash
maton api -X POST '/wati/api/v1/assignOperator?email=agent@example.com&whatsappNumber={whatsappNumber}'
```

**Note:** `{whatsappNumber}` is a placeholder. Replace it with a real value before sending the request.

### Media API

#### Get Media

```bash
maton api '/wati/api/v1/getMedia?fileName={fileName}'
```

**Note:** `{fileName}` is a placeholder. Replace it with a real value before sending the request.

### Pagination

Uses page-based pagination:

```bash
maton api '/wati/api/v1/getContacts?pageSize=50&pageNumber=1'
```

**Query parameters:**
- `pageSize` - Results per page
- `pageNumber` - Page number (1-indexed)

### Notes

- WhatsApp numbers should include country code without + or spaces (e.g., `14155551234`)
- Session messages require an active 24-hour conversation window
- Template messages require pre-approved WhatsApp templates
- Interactive messages have character limits enforced by WhatsApp

### Resources

- [WATI API Documentation](https://docs.wati.io/reference/introduction)
- [WATI Help Center](https://docs.wati.io/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
