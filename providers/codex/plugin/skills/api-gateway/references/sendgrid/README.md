# SendGrid Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `sendgrid`
**Base URL proxied:** `api.sendgrid.com`

## API Path Pattern

```
/sendgrid/v3/{resource}
```

## Common Endpoints

### Mail Send

```bash
maton api -X POST '/sendgrid/v3/mail/send' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "personalizations": [{"to": [{"email": "recipient@example.com"}], "subject": "Hello"}],
  "from": {"email": "sender@example.com"},
  "content": [{"type": "text/plain", "value": "Hello World"}]
}
EOF
```

### User Profile

```bash
maton api '/sendgrid/v3/user/profile'
maton api '/sendgrid/v3/user/account'
```

### Marketing Contacts

```bash
maton api '/sendgrid/v3/marketing/contacts'
maton api -X PUT '/sendgrid/v3/marketing/contacts'
maton api -X DELETE '/sendgrid/v3/marketing/contacts?ids=id1,id2'
maton api -X POST '/sendgrid/v3/marketing/contacts/search'
```

### Marketing Lists

```bash
maton api '/sendgrid/v3/marketing/lists'
maton api -X POST '/sendgrid/v3/marketing/lists'
maton api '/sendgrid/v3/marketing/lists/{list_id}'
maton api -X PATCH '/sendgrid/v3/marketing/lists/{list_id}'
maton api -X DELETE '/sendgrid/v3/marketing/lists/{list_id}'
```

### Segments

```bash
maton api '/sendgrid/v3/marketing/segments'
maton api -X POST '/sendgrid/v3/marketing/segments'
maton api -X DELETE '/sendgrid/v3/marketing/segments/{segment_id}'
```

### Templates

```bash
maton api '/sendgrid/v3/templates'
maton api -X POST '/sendgrid/v3/templates'
maton api '/sendgrid/v3/templates/{template_id}'
maton api -X PATCH '/sendgrid/v3/templates/{template_id}'
maton api -X DELETE '/sendgrid/v3/templates/{template_id}'
```

### Senders

```bash
maton api '/sendgrid/v3/senders'
maton api -X POST '/sendgrid/v3/senders'
maton api -X PATCH '/sendgrid/v3/senders/{sender_id}'
maton api -X DELETE '/sendgrid/v3/senders/{sender_id}'
```

### Suppressions

```bash
maton api '/sendgrid/v3/suppression/bounces'
maton api '/sendgrid/v3/suppression/blocks'
maton api '/sendgrid/v3/suppression/invalid_emails'
maton api '/sendgrid/v3/suppression/spam_reports'
maton api '/sendgrid/v3/suppression/unsubscribes'
```

### Unsubscribe Groups (ASM)

```bash
maton api '/sendgrid/v3/asm/groups'
maton api -X POST '/sendgrid/v3/asm/groups'
maton api -X PATCH '/sendgrid/v3/asm/groups/{group_id}'
maton api -X DELETE '/sendgrid/v3/asm/groups/{group_id}'
```

### Statistics

```bash
maton api '/sendgrid/v3/stats?start_date=2026-02-01'
maton api '/sendgrid/v3/categories/stats?start_date=2026-02-01&categories=cat1'
maton api '/sendgrid/v3/mailbox_providers/stats?start_date=2026-02-01'
```

### API Keys

```bash
maton api '/sendgrid/v3/api_keys'
maton api -X POST '/sendgrid/v3/api_keys'
maton api -X PATCH '/sendgrid/v3/api_keys/{api_key_id}'
maton api -X DELETE '/sendgrid/v3/api_keys/{api_key_id}'
```

## Pagination

Marketing endpoints use token-based pagination:
```bash
maton api '/sendgrid/v3/marketing/lists?page_size=100&page_token={token}'
```

Suppression endpoints use offset pagination:
```bash
maton api '/sendgrid/v3/suppression/bounces?limit=100&offset=0'
```

## Notes

- All requests use JSON content type
- Dates are in YYYY-MM-DD format
- Mail send returns 202 Accepted on success
- Dynamic template IDs start with `d-`
- Marketing contact operations are asynchronous

## Resources

- [SendGrid API Documentation](https://www.twilio.com/docs/sendgrid/api-reference)
- [Mail Send API](https://www.twilio.com/docs/sendgrid/api-reference/mail-send)
