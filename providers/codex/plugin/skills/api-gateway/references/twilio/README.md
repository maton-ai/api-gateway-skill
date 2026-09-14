# Twilio

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `twilio`
**Upstream base URL:** `api.twilio.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.twilio.com/2010-04-01/Accounts.json`
- Gateway: `https://api.maton.ai/twilio/2010-04-01/Accounts.json`

**Important:** Most Twilio endpoints require your Account SID in the path. Get it from `/Accounts.json`.

### Accounts API

#### List Accounts

```bash
maton api '/twilio/2010-04-01/Accounts.json'
```

**Response:**
```json
{
  "accounts": [
    {
      "sid": "{AccountSid}",
      "friendly_name": "My first Twilio account",
      "status": "active",
      "date_created": "Mon, 09 Feb 2026 20:19:55 +0000",
      "date_updated": "Mon, 09 Feb 2026 20:20:05 +0000"
    }
  ]
}
```

#### Get Account

```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}.json'
```

**Note:** `{AccountSid}` is a placeholder. Replace it with a real value before sending the request.

### Messages API

#### List Messages

```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Messages.json'
```

**Note:** `{AccountSid}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `PageSize` - Number of results per page (default: 50)
- `To` - Filter by recipient phone number
- `From` - Filter by sender phone number
- `DateSent` - Filter by date sent

**Response:**
```json
{
  "messages": [
    {
      "sid": "SMxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "body": "Hello!",
      "from": "+15551234567",
      "to": "+15559876543",
      "status": "delivered",
      "date_sent": "Mon, 09 Feb 2026 21:00:00 +0000"
    }
  ],
  "page": 0,
  "page_size": 50
}
```

#### Get Message

```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Messages/{MessageSid}.json'
```

**Note:** `{AccountSid}` and `{MessageSid}` are placeholders. Replace each of them with real values before sending the request.

#### Send Message

```bash
maton api -X POST '/twilio/2010-04-01/Accounts/{AccountSid}/Messages.json' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
To=+15559876543&From=+15551234567&Body=Hello%20from%20Twilio!
BODY
```

**Note:** `{AccountSid}` is a placeholder. Replace it with a real value before sending the request.

**Request body:**
- `To` (required) - Recipient phone number (E.164 format)
- `From` (required) - Twilio phone number or messaging service SID
- `Body` (required) - Message text (max 1600 characters)
- `MessagingServiceSid` (optional) - Use instead of From for message routing
- `MediaUrl` (optional) - URL of media to send (MMS)
- `StatusCallback` (optional) - Webhook URL for status updates

**Response:**
```json
{
  "sid": "SMxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "body": "Hello from Twilio!",
  "from": "+15551234567",
  "to": "+15559876543",
  "status": "queued",
  "date_created": "Mon, 09 Feb 2026 21:00:00 +0000"
}
```

#### Update Message (Redact)

```bash
maton api -X POST '/twilio/2010-04-01/Accounts/{AccountSid}/Messages/{MessageSid}.json' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
Body=
BODY
```

**Note:** `{AccountSid}` and `{MessageSid}` are placeholders. Replace each of them with real values before sending the request.

Setting Body to empty string redacts the message content.

#### Delete Message

```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Messages/{MessageSid}.json' -X DELETE
```

**Note:** `{AccountSid}` and `{MessageSid}` are placeholders. Replace each of them with real values before sending the request.

Returns 204 No Content on success.

### Calls API

#### List Calls

```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Calls.json'
```

**Note:** `{AccountSid}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `PageSize` - Results per page
- `Status` - Filter by status (queued, ringing, in-progress, completed, etc.)
- `To` - Filter by recipient
- `From` - Filter by caller

**Response:**
```json
{
  "calls": [
    {
      "sid": "CAxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "from": "+15551234567",
      "to": "+15559876543",
      "status": "completed",
      "duration": "60",
      "direction": "outbound-api"
    }
  ],
  "page": 0,
  "page_size": 50
}
```

#### Get Call

```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Calls/{CallSid}.json'
```

**Note:** `{AccountSid}` and `{CallSid}` are placeholders. Replace each of them with real values before sending the request.

#### Make Call

```bash
maton api -X POST '/twilio/2010-04-01/Accounts/{AccountSid}/Calls.json' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
To=+15559876543&From=+15551234567&Url=https://example.com/twiml
BODY
```

**Note:** `{AccountSid}` is a placeholder. Replace it with a real value before sending the request.

**Request body:**
- `To` (required) - Recipient phone number
- `From` (required) - Twilio phone number
- `Url` (required) - TwiML application URL
- `StatusCallback` (optional) - Webhook URL for call status updates
- `StatusCallbackEvent` (optional) - Events to receive (initiated, ringing, answered, completed)
- `Timeout` (optional) - Seconds to wait for answer (default: 60)
- `Record` (optional) - Set to true to record the call

#### End Call

```bash
maton api -X POST '/twilio/2010-04-01/Accounts/{AccountSid}/Calls/{CallSid}.json' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
Status=completed
BODY
```

**Note:** `{AccountSid}` and `{CallSid}` are placeholders. Replace each of them with real values before sending the request.

Use `Status=completed` to end an in-progress call.

#### Delete Call

```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Calls/{CallSid}.json' -X DELETE
```

**Note:** `{AccountSid}` and `{CallSid}` are placeholders. Replace each of them with real values before sending the request.

### Phone Numbers API

#### List Incoming Phone Numbers

```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/IncomingPhoneNumbers.json'
```

**Note:** `{AccountSid}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "incoming_phone_numbers": [
    {
      "sid": "PNxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "phone_number": "+15551234567",
      "friendly_name": "My Number",
      "capabilities": {
        "voice": true,
        "sms": true,
        "mms": true
      }
    }
  ]
}
```

#### Get Phone Number

```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/IncomingPhoneNumbers/{PhoneNumberSid}.json'
```

**Note:** `{AccountSid}` and `{PhoneNumberSid}` are placeholders. Replace each of them with real values before sending the request.

#### Update Phone Number

```bash
maton api -X POST '/twilio/2010-04-01/Accounts/{AccountSid}/IncomingPhoneNumbers/{PhoneNumberSid}.json' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
FriendlyName=Updated%20Name
EOF
```

**Note:** `{AccountSid}` and `{PhoneNumberSid}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Phone Number

```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/IncomingPhoneNumbers/{PhoneNumberSid}.json' -X DELETE
```

**Note:** `{AccountSid}` and `{PhoneNumberSid}` are placeholders. Replace each of them with real values before sending the request.

### Applications API

#### List Applications

```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Applications.json'
```

**Note:** `{AccountSid}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "applications": [
    {
      "sid": "APxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "friendly_name": "My App",
      "voice_url": "https://example.com/voice",
      "sms_url": "https://example.com/sms"
    }
  ]
}
```

#### Get Application

```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Applications/{ApplicationSid}.json'
```

**Note:** `{AccountSid}` and `{ApplicationSid}` are placeholders. Replace each of them with real values before sending the request.

#### Create Application

```bash
maton api -X POST '/twilio/2010-04-01/Accounts/{AccountSid}/Applications.json' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
FriendlyName=My%20App&VoiceUrl=https://example.com/voice
BODY
```

**Note:** `{AccountSid}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "sid": "APxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "friendly_name": "My App",
  "voice_url": "https://example.com/voice",
  "date_created": "Tue, 10 Feb 2026 00:20:15 +0000"
}
```

#### Update Application

```bash
maton api -X POST '/twilio/2010-04-01/Accounts/{AccountSid}/Applications/{ApplicationSid}.json' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
FriendlyName=Updated%20App%20Name
EOF
```

**Note:** `{AccountSid}` and `{ApplicationSid}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Application

```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Applications/{ApplicationSid}.json' -X DELETE
```

**Note:** `{AccountSid}` and `{ApplicationSid}` are placeholders. Replace each of them with real values before sending the request.

Returns 204 No Content on success.

### Queues API

#### List Queues

```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Queues.json'
```

**Note:** `{AccountSid}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "queues": [
    {
      "sid": "QUxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "friendly_name": "Support Queue",
      "current_size": 0,
      "max_size": 1000,
      "average_wait_time": 0
    }
  ]
}
```

#### Create Queue

```bash
maton api -X POST '/twilio/2010-04-01/Accounts/{AccountSid}/Queues.json' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
FriendlyName=Support%20Queue&MaxSize=100
BODY
```

**Note:** `{AccountSid}` is a placeholder. Replace it with a real value before sending the request.

#### Update Queue

```bash
maton api -X POST '/twilio/2010-04-01/Accounts/{AccountSid}/Queues/{QueueSid}.json' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
FriendlyName=Updated%20Queue%20Name
EOF
```

**Note:** `{AccountSid}` and `{QueueSid}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Queue

```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Queues/{QueueSid}.json' -X DELETE
```

**Note:** `{AccountSid}` and `{QueueSid}` are placeholders. Replace each of them with real values before sending the request.

#### List Addresses

```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Addresses.json'
```

**Note:** `{AccountSid}` is a placeholder. Replace it with a real value before sending the request.

#### Create Address

```bash
maton api -X POST '/twilio/2010-04-01/Accounts/{AccountSid}/Addresses.json' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
FriendlyName=Office&Street=123%20Main%20St&City=San%20Francisco&Region=CA&PostalCode=94105&IsoCountry=US&CustomerName=Acme%20Inc
EOF
```

**Note:** `{AccountSid}` is a placeholder. Replace it with a real value before sending the request.

### Usage Records API

#### List Usage Records

```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Usage/Records.json'
```

**Note:** `{AccountSid}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `Category` - Filter by usage category (calls, sms, etc.)
- `StartDate` - Start date (YYYY-MM-DD)
- `EndDate` - End date (YYYY-MM-DD)

**Response:**
```json
{
  "usage_records": [
    {
      "category": "sms",
      "description": "SMS Messages",
      "count": "100",
      "price": "0.75",
      "start_date": "2026-02-01",
      "end_date": "2026-02-28"
    }
  ]
}
```

### Pagination

Uses page-based pagination:

```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Messages.json?PageSize=50&Page=0'
```

**Note:** `{AccountSid}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `PageSize` - Results per page (default: 50)
- `Page` - Page number (0-indexed)

Response includes `next_page_uri` for fetching next page.

### Notes

- All endpoints require `/2010-04-01/` API version prefix
- Request bodies use `application/x-www-form-urlencoded` (not JSON)
- Phone numbers must be in E.164 format (+15551234567)
- SID prefixes: AC (account), SM/MM (messages), CA (calls), PN (phone numbers), AP (applications), QU (queues)
- POST is used for both creating and updating resources
- DELETE returns 204 No Content on success

### Resources

- [Twilio API Overview](https://www.twilio.com/docs/usage/api)
- [Messages API](https://www.twilio.com/docs/messaging/api/message-resource)
- [Calls API](https://www.twilio.com/docs/voice/api/call-resource)
- [Maton CLI Manual](https://cli.maton.ai/manual)
