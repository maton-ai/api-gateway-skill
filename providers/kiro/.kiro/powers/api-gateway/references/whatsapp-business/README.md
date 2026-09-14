# WhatsApp Business

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `whatsapp-business`
**Upstream base URL:** `graph.facebook.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://graph.facebook.com/v21.0/{phone_number_id}/messages`
- Gateway: `https://api.maton.ai/whatsapp-business/v21.0/{phone_number_id}/messages`

### Messages API

#### Send Text Message

```bash
maton api -X POST '/whatsapp-business/v21.0/{phone_number_id}/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messaging_product": "whatsapp",
  "recipient_type": "individual",
  "to": "1234567890",
  "type": "text",
  "text": {
    "preview_url": true,
    "body": "Hello! Check out https://example.com"
  }
}
JSON
```

**Note:** `{phone_number_id}` is a placeholder. Replace it with a real value before sending the request.

#### Send Template Message

The template language code must match the locale the user or recipient asked for, and the template must already be approved in that language — `en_US` below is only an example, not a default to reuse.

```bash
maton api -X POST '/whatsapp-business/v21.0/{phone_number_id}/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messaging_product": "whatsapp",
  "to": "1234567890",
  "type": "template",
  "template": {
    "name": "hello_world",
    "language": {
      "code": "en_US"
    },
    "components": [
      {
        "type": "body",
        "parameters": [
          {"type": "text", "text": "John"}
        ]
      }
    ]
  }
}
JSON
```

**Note:** `{phone_number_id}` is a placeholder. Replace it with a real value before sending the request.

#### Send Image Message

```bash
maton api -X POST '/whatsapp-business/v21.0/{phone_number_id}/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messaging_product": "whatsapp",
  "to": "1234567890",
  "type": "image",
  "image": {
    "link": "https://example.com/image.jpg",
    "caption": "Check out this image!"
  }
}
JSON
```

**Note:** `{phone_number_id}` is a placeholder. Replace it with a real value before sending the request.

#### Send Document Message

```bash
maton api -X POST '/whatsapp-business/v21.0/{phone_number_id}/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messaging_product": "whatsapp",
  "to": "1234567890",
  "type": "document",
  "document": {
    "link": "https://example.com/document.pdf",
    "caption": "Here's the document",
    "filename": "report.pdf"
  }
}
JSON
```

**Note:** `{phone_number_id}` is a placeholder. Replace it with a real value before sending the request.

#### Send Video Message

```bash
maton api -X POST '/whatsapp-business/v21.0/{phone_number_id}/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messaging_product": "whatsapp",
  "to": "1234567890",
  "type": "video",
  "video": {
    "link": "https://example.com/video.mp4",
    "caption": "Watch this video"
  }
}
JSON
```

**Note:** `{phone_number_id}` is a placeholder. Replace it with a real value before sending the request.

#### Send Audio Message

```bash
maton api -X POST '/whatsapp-business/v21.0/{phone_number_id}/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messaging_product": "whatsapp",
  "to": "1234567890",
  "type": "audio",
  "audio": {
    "link": "https://example.com/audio.mp3"
  }
}
JSON
```

**Note:** `{phone_number_id}` is a placeholder. Replace it with a real value before sending the request.

#### Send Location Message

```bash
maton api -X POST '/whatsapp-business/v21.0/{phone_number_id}/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messaging_product": "whatsapp",
  "to": "1234567890",
  "type": "location",
  "location": {
    "latitude": 37.7749,
    "longitude": -122.4194,
    "name": "San Francisco",
    "address": "San Francisco, CA, USA"
  }
}
JSON
```

**Note:** `{phone_number_id}` is a placeholder. Replace it with a real value before sending the request.

#### Send Contact Message

```bash
maton api -X POST '/whatsapp-business/v21.0/{phone_number_id}/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messaging_product": "whatsapp",
  "to": "1234567890",
  "type": "contacts",
  "contacts": [
    {
      "name": {
        "formatted_name": "John Doe",
        "first_name": "John",
        "last_name": "Doe"
      },
      "phones": [
        {"phone": "+1234567890", "type": "MOBILE"}
      ]
    }
  ]
}
JSON
```

**Note:** `{phone_number_id}` is a placeholder. Replace it with a real value before sending the request.

#### Send Interactive Button Message

```bash
maton api -X POST '/whatsapp-business/v21.0/{phone_number_id}/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messaging_product": "whatsapp",
  "to": "1234567890",
  "type": "interactive",
  "interactive": {
    "type": "button",
    "body": {
      "text": "Would you like to proceed?"
    },
    "action": {
      "buttons": [
        {"type": "reply", "reply": {"id": "yes", "title": "Yes"}},
        {"type": "reply", "reply": {"id": "no", "title": "No"}}
      ]
    }
  }
}
JSON
```

**Note:** `{phone_number_id}` is a placeholder. Replace it with a real value before sending the request.

#### Send Interactive List Message

```bash
maton api -X POST '/whatsapp-business/v21.0/{phone_number_id}/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messaging_product": "whatsapp",
  "to": "1234567890",
  "type": "interactive",
  "interactive": {
    "type": "list",
    "header": {"type": "text", "text": "Select an option"},
    "body": {"text": "Choose from the list below"},
    "action": {
      "button": "View Options",
      "sections": [
        {
          "title": "Products",
          "rows": [
            {"id": "prod1", "title": "Product 1", "description": "First product"},
            {"id": "prod2", "title": "Product 2", "description": "Second product"}
          ]
        }
      ]
    }
  }
}
JSON
```

**Note:** `{phone_number_id}` is a placeholder. Replace it with a real value before sending the request.

#### Mark Message as Read

```bash
maton api -X POST '/whatsapp-business/v21.0/{phone_number_id}/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messaging_product": "whatsapp",
  "status": "read",
  "message_id": "wamid.xxxxx"
}
JSON
```

**Note:** `{phone_number_id}` is a placeholder. Replace it with a real value before sending the request.

### Media API

#### Upload Media

```bash
# maton api sends a body verbatim but does not build a multipart envelope:
# assemble it first, then hand the file to --input.
BOUNDARY="maton-$$"
{
  printf -- '--%s\r\nContent-Disposition: form-data; name="file"; filename="file.jpg"\r\nContent-Type: application/octet-stream\r\n\r\n' "$BOUNDARY"
  cat file.jpg
  printf -- '\r\n'
  printf -- '--%s\r\nContent-Disposition: form-data; name="type"\r\n\r\nimage/jpeg\r\n' "$BOUNDARY"
  printf -- '--%s\r\nContent-Disposition: form-data; name="messaging_product"\r\n\r\nwhatsapp\r\n' "$BOUNDARY"
  printf -- '--%s--\r\n' "$BOUNDARY"
} > /tmp/upload.body

maton api -X POST '/whatsapp-business/v21.0/{phone_number_id}/media' \
  -H "Content-Type: multipart/form-data; boundary=$BOUNDARY" \
  --input /tmp/upload.body
```

**Note:** `{phone_number_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Media URL

```bash
maton api '/whatsapp-business/v21.0/{media_id}'
```

**Note:** `{media_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Media

```bash
maton api '/whatsapp-business/v21.0/{media_id}' -X DELETE
```

**Note:** `{media_id}` is a placeholder. Replace it with a real value before sending the request.

### Message Templates API

#### List Templates

```bash
maton api '/whatsapp-business/v21.0/{whatsapp_business_account_id}/message_templates'
```

**Note:** `{whatsapp_business_account_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `limit` - Number of templates to return
- `status` - Filter by status: `APPROVED`, `PENDING`, `REJECTED`

#### Create Template

```bash
# `language` sets the locale this template is created for; use the one the user asked for.
maton api -X POST '/whatsapp-business/v21.0/{whatsapp_business_account_id}/message_templates' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "order_confirmation",
  "language": "en_US",
  "category": "UTILITY",
  "components": [
    {
      "type": "HEADER",
      "format": "TEXT",
      "text": "Order Confirmation"
    },
    {
      "type": "BODY",
      "text": "Hi {{1}}, your order #{{2}} has been confirmed!"
    },
    {
      "type": "FOOTER",
      "text": "Thank you for your purchase"
    }
  ]
}
JSON
```

**Note:** `{whatsapp_business_account_id}` is a placeholder. Replace it with a real value before sending the request.

Template categories: `AUTHENTICATION`, `MARKETING`, `UTILITY`

#### Delete Template

```bash
maton api '/whatsapp-business/v21.0/{whatsapp_business_account_id}/message_templates?name=template_name' -X DELETE
```

**Note:** `{whatsapp_business_account_id}` is a placeholder. Replace it with a real value before sending the request.

### Phone Numbers API

#### Get Phone Number

```bash
maton api '/whatsapp-business/v21.0/{phone_number_id}'
```

**Note:** `{phone_number_id}` is a placeholder. Replace it with a real value before sending the request.

#### List Phone Numbers

```bash
maton api '/whatsapp-business/v21.0/{whatsapp_business_account_id}/phone_numbers'
```

**Note:** `{whatsapp_business_account_id}` is a placeholder. Replace it with a real value before sending the request.

### Business Profile API

#### Get Business Profile

```bash
maton api '/whatsapp-business/v21.0/{phone_number_id}/whatsapp_business_profile?fields=about,address,description,email,profile_picture_url,websites,vertical'
```

**Note:** `{phone_number_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Business Profile

```bash
maton api -X POST '/whatsapp-business/v21.0/{phone_number_id}/whatsapp_business_profile' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messaging_product": "whatsapp",
  "about": "Your trusted partner",
  "address": "123 Business St",
  "description": "We provide excellent services",
  "email": "contact@example.com",
  "websites": ["https://example.com"],
  "vertical": "RETAIL"
}
JSON
```

**Note:** `{phone_number_id}` is a placeholder. Replace it with a real value before sending the request.

### Notes

- Phone numbers must be in international format without `+` or leading zeros (e.g., `1234567890`)
- `messaging_product` must always be set to `whatsapp`
- Template messages are required for initiating conversations (24-hour messaging window)
- Media files must be publicly accessible URLs or uploaded via the Media API
- Interactive messages support up to 3 buttons or 10 list items
- Message IDs (`wamid`) are used to track message status and replies
- API version `v21.0` is current; check Meta docs for latest version

### Resources

- [WhatsApp Business API Overview](https://developers.facebook.com/docs/whatsapp/cloud-api/overview)
- [Send Messages](https://developers.facebook.com/docs/whatsapp/cloud-api/guides/send-messages)
- [Message Templates](https://developers.facebook.com/docs/whatsapp/cloud-api/guides/send-message-templates)
- [Media](https://developers.facebook.com/docs/whatsapp/cloud-api/reference/media)
- [Business Profiles](https://developers.facebook.com/docs/whatsapp/cloud-api/reference/business-profiles)
- [Webhooks](https://developers.facebook.com/docs/whatsapp/cloud-api/webhooks)
- [Error Codes](https://developers.facebook.com/docs/whatsapp/cloud-api/support/error-codes)
- [Maton CLI Manual](https://cli.maton.ai/manual)
