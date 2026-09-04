# ManyChat Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `manychat`
**Base URL proxied:** `api.manychat.com`

## API Path Pattern

```
/manychat/fb/{category}/{action}
```

## Common Endpoints

### Page Operations

#### Get Page Info
```bash
maton api '/manychat/fb/page/getInfo'
```

#### List Tags
```bash
maton api '/manychat/fb/page/getTags'
```

#### Create Tag
```bash
maton api -X POST '/manychat/fb/page/createTag' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "New Tag"
}
EOF
```

#### Remove Tag
```bash
maton api -X POST '/manychat/fb/page/removeTag' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "tag_id": 123
}
EOF
```

#### List Custom Fields
```bash
maton api '/manychat/fb/page/getCustomFields'
```

#### Create Custom Field
```bash
maton api -X POST '/manychat/fb/page/createCustomField' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "caption": "Phone Number",
  "type": "text",
  "description": "Customer phone number"
}
EOF
```

#### List Bot Fields
```bash
maton api '/manychat/fb/page/getBotFields'
```

#### Set Bot Field
```bash
maton api -X POST '/manychat/fb/page/setBotField' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "field_id": 123,
  "field_value": 42
}
EOF
```

#### List Flows
```bash
maton api '/manychat/fb/page/getFlows'
```

#### List Growth Tools
```bash
maton api '/manychat/fb/page/getGrowthTools'
```

#### List OTN Topics
```bash
maton api '/manychat/fb/page/getOtnTopics'
```

### Subscriber Operations

#### Get Subscriber Info
```bash
maton api '/manychat/fb/subscriber/getInfo?subscriber_id=123456789'
```

#### Find Subscriber by Name
```bash
maton api '/manychat/fb/subscriber/findByName?name=John%20Doe'
```

#### Find Subscriber by Email/Phone
```bash
maton api '/manychat/fb/subscriber/findBySystemField?email=john@example.com'
```

#### Create Subscriber
```bash
maton api -X POST '/manychat/fb/subscriber/createSubscriber' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "first_name": "John",
  "last_name": "Doe",
  "phone": "+1234567890",
  "email": "john@example.com"
}
EOF
```

#### Update Subscriber
```bash
maton api -X POST '/manychat/fb/subscriber/updateSubscriber' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "subscriber_id": 123456789,
  "first_name": "John",
  "last_name": "Smith"
}
EOF
```

#### Add Tag to Subscriber
```bash
maton api -X POST '/manychat/fb/subscriber/addTag' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "subscriber_id": 123456789,
  "tag_id": 1
}
EOF
```

#### Set Custom Field
```bash
maton api -X POST '/manychat/fb/subscriber/setCustomField' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "subscriber_id": 123456789,
  "field_id": 1,
  "field_value": "value"
}
EOF
```

### Sending Operations

#### Send Content
```bash
maton api -X POST '/manychat/fb/sending/sendContent' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "subscriber_id": 123456789,
  "data": {
    "version": "v2",
    "content": {
      "messages": [
        {"type": "text", "text": "Hello!"}
      ]
    }
  }
}
EOF
```

#### Send Flow
```bash
maton api -X POST '/manychat/fb/sending/sendFlow' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "subscriber_id": 123456789,
  "flow_ns": "content123456"
}
EOF
```

## Rate Limits

| Endpoint Category | Rate Limit |
|------------------|------------|
| Page GET endpoints | 100 queries/second |
| Page POST endpoints | 10 queries/second |
| Subscriber operations | 10-50 queries/second |
| Sending content | 25 queries/second |
| Sending flows | 20 queries/second |

## Notes

- Subscriber IDs are integers unique within a page
- Flow namespaces (flow_ns) identify automation flows
- Message tags are required for sending outside the 24-hour window
- All responses include `{"status": "success"}` or `{"status": "error"}`
- Custom field types: `text`, `number`, `date`, `datetime`, `boolean`

## Resources

- [ManyChat API Documentation](https://api.manychat.com/swagger)
- [ManyChat API Key Generation](https://help.manychat.com/hc/en-us/articles/14959510331420)
- [ManyChat Dev Program](https://help.manychat.com/hc/en-us/articles/14281269835548)
