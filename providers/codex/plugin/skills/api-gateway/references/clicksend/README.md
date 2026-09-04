# ClickSend Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `clicksend`
**Base URL proxied:** `rest.clicksend.com`

## API Path Pattern

```
/clicksend/v3/{resource}
```

## Common Endpoints

### Account

#### Get Account
```bash
maton api '/clicksend/v3/account'
```

### SMS

#### Send SMS
```bash
maton api -X POST '/clicksend/v3/sms/send' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "messages": [
    {
      "to": "+15551234567",
      "body": "Hello!",
      "source": "api"
    }
  ]
}
EOF
```

#### SMS History
```bash
maton api '/clicksend/v3/sms/history'
```

#### SMS Templates
```bash
maton api '/clicksend/v3/sms/templates'
maton api -X POST '/clicksend/v3/sms/templates'
maton api -X PUT '/clicksend/v3/sms/templates/{template_id}'
maton api -X DELETE '/clicksend/v3/sms/templates/{template_id}'
```

### MMS

#### Send MMS
```bash
maton api -X POST '/clicksend/v3/mms/send'
```

#### MMS History
```bash
maton api '/clicksend/v3/mms/history'
```

### Voice

#### Send Voice
```bash
maton api -X POST '/clicksend/v3/voice/send'
```

#### Voice Languages
```bash
maton api '/clicksend/v3/voice/lang'
```

### Contact Lists

#### List All Lists
```bash
maton api '/clicksend/v3/lists'
```

#### CRUD Operations
```bash
maton api '/clicksend/v3/lists/{list_id}'
maton api -X POST '/clicksend/v3/lists'
maton api -X PUT '/clicksend/v3/lists/{list_id}'
maton api -X DELETE '/clicksend/v3/lists/{list_id}'
```

### Contacts

#### List Contacts
```bash
maton api '/clicksend/v3/lists/{list_id}/contacts'
```

#### CRUD Operations
```bash
maton api '/clicksend/v3/lists/{list_id}/contacts/{contact_id}'
maton api -X POST '/clicksend/v3/lists/{list_id}/contacts'
maton api -X PUT '/clicksend/v3/lists/{list_id}/contacts/{contact_id}'
maton api -X DELETE '/clicksend/v3/lists/{list_id}/contacts/{contact_id}'
```

### Email Addresses

```bash
maton api '/clicksend/v3/email/addresses'
maton api -X POST '/clicksend/v3/email/addresses'
maton api -X DELETE '/clicksend/v3/email/addresses/{email_address_id}'
```

### Utility

```bash
maton api '/clicksend/v3/countries'
```

## Response Format

All responses follow this structure:

```json
{
  "http_code": 200,
  "response_code": "SUCCESS",
  "response_msg": "Description",
  "data": { ... }
}
```

## Pagination

Uses page-based pagination:

```bash
maton api '/clicksend/v3/lists?page=2&limit=50'
# Response includes total, per_page, current_page, last_page
```

## Notes

- Phone numbers must be E.164 format
- Timestamps are Unix timestamps
- Voice access requires account permissions
- SMS over 160 chars split into segments

## Resources

- [ClickSend Developer Portal](https://developers.clicksend.com/)
- [ClickSend REST API v3](https://developers.clicksend.com/docs)
