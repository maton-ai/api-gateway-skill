# ManyChat

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `manychat`
**Upstream base URL:** `api.manychat.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.manychat.com/fb/page/getInfo`
- Gateway: `https://api.maton.ai/manychat/fb/page/getInfo`

### Page API

#### Get Page Info

```bash
maton api '/manychat/fb/page/getInfo'
```

**Note:** Rate limit - 100 queries per second

**Response:**
```json
{
  "status": "success",
  "data": {
    "id": 123456789,
    "name": "Page Name",
    "category": "Business",
    "avatar_link": "https://...",
    "username": "pagename",
    "about": "About text",
    "description": "Page description",
    "is_pro": true,
    "timezone": "America/New_York"
  }
}
```

#### List Page Tags

```bash
maton api '/manychat/fb/page/getTags'
```

**Note:** Rate limit - 100 queries per second

**Response:**
```json
{
  "status": "success",
  "data": [
    {"id": 1, "name": "VIP"},
    {"id": 2, "name": "Customer"}
  ]
}
```

#### Create Page Tag

```bash
maton api -X POST '/manychat/fb/page/createTag' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Tag"
}
JSON
```

**Note:** Rate limit - 10 queries per second

#### Remove Page Tag

```bash
maton api -X POST '/manychat/fb/page/removeTag' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "tag_id": 123
}
JSON
```

**Note:** Rate limit - 10 queries per second. Removes tag from page and all subscribers.

#### Remove Page Tag by Name

```bash
maton api -X POST '/manychat/fb/page/removeTagByName' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "tag_name": "Old Tag"
}
JSON
```

**Note:** Rate limit - 10 queries per second

#### List Custom Fields

```bash
maton api '/manychat/fb/page/getCustomFields'
```

**Note:** Rate limit - 100 queries per second

**Response:**
```json
{
  "status": "success",
  "data": [
    {"id": 1, "name": "phone_number", "type": "text"},
    {"id": 2, "name": "purchase_count", "type": "number"}
  ]
}
```

#### Create Custom Field

```bash
maton api -X POST '/manychat/fb/page/createCustomField' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "caption": "Phone Number",
  "type": "text",
  "description": "Customer phone number"
}
JSON
```

**Note:** Rate limit - 10 queries per second

**Field Types:** `text`, `number`, `date`, `datetime`, `boolean`

#### List Bot Fields

```bash
maton api '/manychat/fb/page/getBotFields'
```

**Note:** Rate limit - 100 queries per second

#### Create Bot Field

```bash
maton api -X POST '/manychat/fb/page/createBotField' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "counter",
  "type": "number",
  "description": "Global counter",
  "value": 0
}
JSON
```

**Note:** Rate limit - 10 queries per second

#### Set Bot Field

```bash
maton api -X POST '/manychat/fb/page/setBotField' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "field_id": 123,
  "field_value": 42
}
JSON
```

**Note:** Rate limit - 10 queries per second

#### Set Bot Field by Name

```bash
maton api -X POST '/manychat/fb/page/setBotFieldByName' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "field_name": "counter",
  "field_value": 42
}
JSON
```

**Note:** Rate limit - 10 queries per second

#### Set Multiple Bot Fields

```bash
maton api -X POST '/manychat/fb/page/setBotFields' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "fields": [
    {"field_id": 123, "field_value": "value1"},
    {"field_name": "field2", "field_value": "value2"}
  ]
}
JSON
```

**Note:** Rate limit - 10 queries per second. Maximum 20 fields per request.

#### List Flows

```bash
maton api '/manychat/fb/page/getFlows'
```

**Note:** Rate limit - 10 queries per second

**Response:**
```json
{
  "status": "success",
  "data": {
    "flows": [
      {"ns": "content123456", "name": "Welcome Flow", "folder_id": 1}
    ],
    "folders": [
      {"id": 1, "name": "Main Folder"}
    ]
  }
}
```

#### List Growth Tools

```bash
maton api '/manychat/fb/page/getGrowthTools'
```

**Note:** Rate limit - 100 queries per second

#### List OTN Topics

```bash
maton api '/manychat/fb/page/getOtnTopics'
```

**Note:** Rate limit - 100 queries per second

### Subscriber API

#### Remove Subscriber Tag by Name

```bash
maton api -X POST '/manychat/fb/subscriber/removeTagByName' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "subscriber_id": 123456789,
  "tag_name": "VIP"
}
JSON
```

**Note:** Rate limit - 10 queries per second

#### Get Subscriber Info

```bash
maton api '/manychat/fb/subscriber/getInfo?subscriber_id=123456789'
```

**Note:** Rate limit - 10 queries per second

**Response:**
```json
{
  "status": "success",
  "data": {
    "id": 123456789,
    "name": "John Doe",
    "first_name": "John",
    "last_name": "Doe",
    "gender": "male",
    "profile_pic": "https://...",
    "subscribed": "2025-01-15T10:30:00Z",
    "last_interaction": "2025-02-01T14:20:00Z",
    "tags": [{"id": 1, "name": "VIP"}],
    "custom_fields": [{"id": 1, "name": "phone", "value": "+1234567890"}]
  }
}
```

#### Find Subscriber by Name

```bash
maton api '/manychat/fb/subscriber/findByName?name=John%20Doe'
```

**Note:** Rate limit - 10 queries per second. Maximum 100 results.

#### Find Subscriber by Custom Field

```bash
maton api '/manychat/fb/subscriber/findByCustomField?field_id=123&field_value=value'
```

**Note:** Rate limit - 10 queries per second. Works with Text and Number fields. Maximum 100 results.

#### Find Subscriber by Email/Phone

```bash
maton api '/manychat/fb/subscriber/findBySystemField?email=john@example.com'
```

```bash
maton api '/manychat/fb/subscriber/findBySystemField?phone=+1234567890'
```

**Note:** Rate limit - 50 queries per second. Set either `email` OR `phone` parameter.

#### Get Subscriber by User Ref

```bash
maton api '/manychat/fb/subscriber/getInfoByUserRef?user_ref=123456'
```

#### Create Subscriber

```bash
maton api -X POST '/manychat/fb/subscriber/createSubscriber' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "first_name": "John",
  "last_name": "Doe",
  "phone": "+1234567890",
  "email": "john@example.com",
  "gender": "male",
  "has_opt_in_sms": true,
  "has_opt_in_email": true,
  "consent_phrase": "I agree to receive messages"
}
JSON
```

**Note:** Rate limit - 10 queries per second

**Note:** Importing subscribers with phone or email requires special permissions from ManyChat. Contact ManyChat support to enable this feature for your account.

#### Update Subscriber

```bash
maton api -X POST '/manychat/fb/subscriber/updateSubscriber' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "subscriber_id": 123456789,
  "first_name": "John",
  "last_name": "Smith",
  "phone": "+1234567890",
  "email": "john.smith@example.com"
}
JSON
```

**Note:** Rate limit - 10 queries per second

#### Add Tag to Subscriber

```bash
maton api -X POST '/manychat/fb/subscriber/addTag' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "subscriber_id": 123456789,
  "tag_id": 1
}
JSON
```

**Note:** Rate limit - 10 queries per second

#### Add Tag to Subscriber by Name

```bash
maton api -X POST '/manychat/fb/subscriber/addTagByName' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "subscriber_id": 123456789,
  "tag_name": "VIP"
}
JSON
```

**Note:** Rate limit - 10 queries per second

#### Remove Subscriber Tag

```bash
maton api -X POST '/manychat/fb/subscriber/removeTag' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "subscriber_id": 123456789,
  "tag_id": 1
}
JSON
```

**Note:** Rate limit - 10 queries per second

#### Set Custom Field

```bash
maton api -X POST '/manychat/fb/subscriber/setCustomField' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "subscriber_id": 123456789,
  "field_id": 1,
  "field_value": "+1234567890"
}
JSON
```

**Note:** Rate limit - 10 queries per second

#### Set Custom Field by Name

```bash
maton api -X POST '/manychat/fb/subscriber/setCustomFieldByName' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "subscriber_id": 123456789,
  "field_name": "phone_number",
  "field_value": "+1234567890"
}
JSON
```

**Note:** Rate limit - 10 queries per second

#### Set Multiple Custom Fields

```bash
maton api -X POST '/manychat/fb/subscriber/setCustomFields' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "subscriber_id": 123456789,
  "fields": [
    {"field_id": 1, "field_value": "value1"},
    {"field_name": "field2", "field_value": "value2"}
  ]
}
JSON
```

**Note:** Rate limit - 10 queries per second. Maximum 20 fields per request.

#### Verify Subscriber by Signed Request

```bash
maton api -X POST '/manychat/fb/subscriber/verifyBySignedRequest' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "subscriber_id": 123456789,
  "signed_request": "signed_request_token"
}
JSON
```

**Note:** Rate limit - 10 queries per second

### Sending API

#### Send Content

```bash
maton api -X POST '/manychat/fb/sending/sendContent' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "subscriber_id": 123456789,
  "data": {
    "version": "v2",
    "content": {
      "messages": [
        {
          "type": "text",
          "text": "Hello! How can I help you today?"
        }
      ]
    }
  },
  "message_tag": "CONFIRMED_EVENT_UPDATE"
}
JSON
```

**Note:** Rate limit - 25 queries per second

**Message Tags:** Required for sending outside the 24-hour messaging window
- `CONFIRMED_EVENT_UPDATE`
- `POST_PURCHASE_UPDATE`
- `ACCOUNT_UPDATE`

**OTN (One-Time Notification):**
```json
{
  "subscriber_id": 123456789,
  "data": {...},
  "otn_topic_name": "Price Drop Alert"
}
```

#### Send Content by User Ref

```bash
maton api -X POST '/manychat/fb/sending/sendContentByUserRef' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "user_ref": 123456,
  "data": {
    "version": "v2",
    "content": {
      "messages": [
        {
          "type": "text",
          "text": "Welcome!"
        }
      ]
    }
  }
}
JSON
```

**Note:** Rate limit - 25 queries per second

#### Send Flow

```bash
maton api -X POST '/manychat/fb/sending/sendFlow' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "subscriber_id": 123456789,
  "flow_ns": "content123456"
}
JSON
```

**Note:** Rate limit - 20 queries per second, maximum 100 per subscriber per hour

### Rate Limits

| Endpoint Category | Rate Limit |
|------------------|------------|
| Page GET endpoints | 100 queries/second |
| Page POST endpoints | 10 queries/second |
| Subscriber operations | 10-50 queries/second |
| Sending content | 25 queries/second |
| Sending flows | 20 queries/second |

### Notes

- Subscriber IDs are integers unique within a page
- Flow namespaces (flow_ns) identify automation flows
- Message tags are required for sending outside the 24-hour window
- All responses include `{"status": "success"}` or `{"status": "error"}`
- Custom field types: `text`, `number`, `date`, `datetime`, `boolean`

### Resources

- [ManyChat API Documentation](https://api.manychat.com/swagger)
- [API Key Generation](https://help.manychat.com/hc/en-us/articles/14959510331420)
- [Dev Program](https://help.manychat.com/hc/en-us/articles/14281269835548)
- [Maton CLI Manual](https://cli.maton.ai/manual)
