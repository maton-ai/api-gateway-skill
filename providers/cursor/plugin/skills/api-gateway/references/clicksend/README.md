# ClickSend

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `clicksend`
**Upstream base URL:** `rest.clicksend.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://rest.clicksend.com/v3/account`
- Gateway: `https://api.maton.ai/clicksend/v3/account`

### Account API

#### Get Account

```bash
maton api '/clicksend/v3/account'
```

**Response:**
```json
{
  "http_code": 200,
  "response_code": "SUCCESS",
  "response_msg": "Here's your account",
  "data": {
    "user_id": 672721,
    "username": "user@example.com",
    "user_email": "user@example.com",
    "balance": "2.005718",
    "user_phone": "+18019234886",
    "user_first_name": "John",
    "user_last_name": "Doe",
    "country": "US",
    "default_country_sms": "US",
    "timezone": "America/Chicago",
    "_currency": {
      "currency_name_short": "USD",
      "currency_prefix_d": "$"
    }
  }
}
```

### SMS API

#### Send SMS

```bash
maton api -X POST '/clicksend/v3/sms/send' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messages": [
    {
      "to": "+15551234567",
      "body": "Hello from ClickSend!",
      "source": "api"
    }
  ]
}
JSON
```

**Request body:**

| Field | Type | Description |
|-------|------|-------------|
| `to` | string | Recipient phone number (E.164 format) |
| `body` | string | SMS message content |
| `source` | string | Source identifier (e.g., "api", "sdk") |
| `from` | string | Sender ID (optional) |
| `schedule` | int | Unix timestamp for scheduled send (optional) |
| `custom_string` | string | Custom reference (optional) |

#### Get SMS Price

```bash
maton api -X POST '/clicksend/v3/sms/price' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messages": [
    {
      "to": "+15551234567",
      "body": "Test message",
      "source": "api"
    }
  ]
}
JSON
```

#### SMS History

```bash
maton api '/clicksend/v3/sms/history'
```

**Query parameters:**

| Parameter | Description |
|-----------|-------------|
| `date_from` | Unix timestamp for start date |
| `date_to` | Unix timestamp for end date |
| `page` | Page number (default: 1) |
| `limit` | Results per page (default: 15) |

#### Inbound SMS

```bash
maton api '/clicksend/v3/sms/inbound'
```

#### SMS Receipts (Delivery Reports)

```bash
maton api '/clicksend/v3/sms/receipts'
```

#### Cancel Scheduled SMS

```bash
maton api -X PUT '/clicksend/v3/sms/{message_id}/cancel'
```

**Note:** `{message_id}` is a placeholder. Replace it with a real value before sending the request.

#### Cancel All Scheduled SMS

```bash
maton api -X PUT '/clicksend/v3/sms/cancel-all'
```

### SMS Templates API

#### List Templates

```bash
maton api '/clicksend/v3/sms/templates'
```

**Response:**
```json
{
  "http_code": 200,
  "response_code": "SUCCESS",
  "response_msg": "Here are your templates.",
  "data": {
    "total": 1,
    "per_page": 15,
    "current_page": 1,
    "data": [
      {
        "template_id": 632497,
        "body": "Hello {name}, this is a test message.",
        "template_name": "Test Template"
      }
    ]
  }
}
```

#### Create Template

```bash
maton api -X POST '/clicksend/v3/sms/templates' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "template_name": "Welcome Message",
  "body": "Hello {name}, welcome to our service!"
}
JSON
```

**Note:** `{name}` is a placeholder. Replace it with a real value before sending the request.

#### Update Template

```bash
maton api -X PUT '/clicksend/v3/sms/templates/{template_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "template_name": "Updated Template",
  "body": "Updated message content"
}
JSON
```

**Note:** `{template_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Template

```bash
maton api '/clicksend/v3/sms/templates/{template_id}' -X DELETE
```

**Note:** `{template_id}` is a placeholder. Replace it with a real value before sending the request.

### MMS API

#### Send MMS

```bash
maton api -X POST '/clicksend/v3/mms/send' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messages": [
    {
      "to": "+15551234567",
      "body": "Check out this image!",
      "media_file": "https://example.com/image.jpg",
      "source": "api"
    }
  ]
}
JSON
```

#### MMS History

```bash
maton api '/clicksend/v3/mms/history'
```

#### Get MMS Price

```bash
maton api -X POST '/clicksend/v3/mms/price' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messages": [...]
}
JSON
```

#### MMS Receipts

```bash
maton api '/clicksend/v3/mms/receipts'
```

### Voice API

#### Send Voice Message

Pick `lang` (and `voice`) from the locale the user asked for or the recipient's preferred language — `en-us` below is only an example. See the parameter table for the supported values.

```bash
maton api -X POST '/clicksend/v3/voice/send' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messages": [
    {
      "to": "+15551234567",
      "body": "Hello, this is a voice message.",
      "voice": "female",
      "lang": "en-us",
      "source": "api"
    }
  ]
}
JSON
```

**Voice Parameters:**

| Field | Description |
|-------|-------------|
| `to` | Recipient phone number |
| `body` | Text to be spoken |
| `voice` | Voice gender: `male` or `female` |
| `lang` | Language code (e.g., `en-us`, `en-gb`, `de-de`) |
| `schedule` | Unix timestamp for scheduled call |
| `require_input` | Require a keypad response (0-1) |
| `machine_detection` | Detect answering machine (0-1) |

#### Available Languages

```bash
maton api '/clicksend/v3/voice/lang'
```

Returns list of supported languages with codes and available genders.

#### Voice History

```bash
maton api '/clicksend/v3/voice/history'
```

**Note:** Requires voice access enabled on account.

#### Get Voice Price

```bash
maton api -X POST '/clicksend/v3/voice/price' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messages": [
    {
      "to": "+15551234567",
      "body": "This is a test voice message.",
      "voice": "female",
      "lang": "en-us"
    }
  ]
}
JSON
```

#### Cancel Voice Message

```bash
maton api -X PUT '/clicksend/v3/voice/{message_id}/cancel'
```

**Note:** `{message_id}` is a placeholder. Replace it with a real value before sending the request.

### Contact Lists API

#### List All Lists

```bash
maton api '/clicksend/v3/lists'
```

**Response:**
```json
{
  "http_code": 200,
  "response_code": "SUCCESS",
  "response_msg": "Here are your contact lists.",
  "data": {
    "total": 2,
    "data": [
      {
        "list_id": 3555277,
        "list_name": "Opt-Out List",
        "_contacts_count": 0
      },
      {
        "list_id": 3555278,
        "list_name": "Example List",
        "_contacts_count": 10
      }
    ]
  }
}
```

#### Get List

```bash
maton api '/clicksend/v3/lists/{list_id}'
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create List

```bash
maton api -X POST '/clicksend/v3/lists' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "list_name": "My New List"
}
JSON
```

#### Update List

```bash
maton api -X PUT '/clicksend/v3/lists/{list_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "list_name": "Updated List Name"
}
JSON
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete List

```bash
maton api '/clicksend/v3/lists/{list_id}' -X DELETE
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

#### Remove Duplicates

```bash
maton api -X PUT '/clicksend/v3/lists/{list_id}/remove-duplicates'
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

### Contacts API

#### List Contacts in List

```bash
maton api '/clicksend/v3/lists/{list_id}/contacts'
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Parameter | Description |
|-----------|-------------|
| `page` | Page number |
| `limit` | Results per page |
| `updated_after` | Filter contacts updated after timestamp |

#### Get Contact

```bash
maton api '/clicksend/v3/lists/{list_id}/contacts/{contact_id}'
```

**Note:** `{list_id}` and `{contact_id}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "http_code": 200,
  "response_code": "SUCCESS",
  "data": {
    "contact_id": 1581565666,
    "list_id": 3555278,
    "phone_number": "+18019234886",
    "first_name": "John",
    "last_name": "Doe",
    "email": "john@example.com",
    "custom_1": "",
    "custom_2": "",
    "custom_3": "",
    "custom_4": "",
    "organization_name": "",
    "address_city": "",
    "address_state": "",
    "address_country": "US"
  }
}
```

#### Create Contact

```bash
maton api -X POST '/clicksend/v3/lists/{list_id}/contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "phone_number": "+15551234567",
  "first_name": "John",
  "last_name": "Doe",
  "email": "john@example.com"
}
JSON
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

**Contact Fields:**

| Field | Description |
|-------|-------------|
| `phone_number` | Phone number (E.164 format) |
| `first_name` | First name |
| `last_name` | Last name |
| `email` | Email address |
| `fax_number` | Fax number |
| `organization_name` | Company name |
| `custom_1` - `custom_4` | Custom fields |
| `address_line_1`, `address_line_2` | Address |
| `address_city`, `address_state`, `address_postal_code`, `address_country` | Address components |

#### Update Contact

```bash
maton api -X PUT '/clicksend/v3/lists/{list_id}/contacts/{contact_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "first_name": "Jane",
  "last_name": "Smith"
}
JSON
```

**Note:** `{list_id}` and `{contact_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Contact

```bash
maton api '/clicksend/v3/lists/{list_id}/contacts/{contact_id}' -X DELETE
```

**Note:** `{list_id}` and `{contact_id}` are placeholders. Replace each of them with real values before sending the request.

#### Copy Contact to Another List

```bash
maton api -X PUT '/clicksend/v3/lists/{from_list_id}/contacts/{contact_id}/copy/{to_list_id}'
```

**Note:** `{from_list_id}`, `{contact_id}` and `{to_list_id}` are placeholders. Replace each of them with real values before sending the request.

#### Transfer Contact to Another List

```bash
maton api -X PUT '/clicksend/v3/lists/{from_list_id}/contacts/{contact_id}/transfer/{to_list_id}'
```

**Note:** `{from_list_id}`, `{contact_id}` and `{to_list_id}` are placeholders. Replace each of them with real values before sending the request.

### Email Addresses API

> **Account configuration.** These endpoints manage verified sender email addresses on the ClickSend account. Adding or deleting addresses affects which sender identities are available for email campaigns. Confirm the address and intent with the user before modifying.

#### List Verified Email Addresses

```bash
maton api '/clicksend/v3/email/addresses'
```

#### Add Email Address

```bash
maton api -X POST '/clicksend/v3/email/addresses' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "email_address": "sender@example.com"
}
JSON
```

#### Delete Email Address

```bash
maton api '/clicksend/v3/email/addresses/{email_address_id}' -X DELETE
```

**Note:** `{email_address_id}` is a placeholder. Replace it with a real value before sending the request.

### Utility Endpoints API

#### List Countries

```bash
maton api '/clicksend/v3/countries'
```

Returns list of all supported countries with codes.

### Response Format

All ClickSend API responses follow this structure:

```json
{
  "http_code": 200,
  "response_code": "SUCCESS",
  "response_msg": "Description of the result",
  "data": { ... }
}
```

### Pagination

ClickSend uses page-based pagination:

```bash
maton api '/clicksend/v3/lists?page=2&limit=50'
```

**Response:**
```json
{
  "data": {
    "total": 100,
    "per_page": 50,
    "current_page": 2,
    "last_page": 2,
    "next_page_url": null,
    "prev_page_url": "...?page=1",
    "from": 51,
    "to": 100,
    "data": [...]
  }
}
```

**Query parameters:**
- `page` - Page number (default: 1)
- `limit` - Results per page (default: 15)

### Notes

- Phone numbers must be in E.164 format (e.g., `+15551234567`)
- All timestamps are Unix timestamps (seconds since epoch)
- Use `source` field to identify your application in analytics
- Templates support placeholders like `{name}`, `{custom_1}`, etc.
- SMS messages over 160 characters are split into multiple segments
- Voice access requires account-level permissions

### Resources

- [ClickSend Developer Portal](https://developers.clicksend.com/)
- [ClickSend REST API v3](https://developers.clicksend.com/docs)
- [Maton CLI Manual](https://cli.maton.ai/manual)
