# Quo Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `quo`
**Base URL proxied:** `api.openphone.com`

## API Path Pattern

```
/quo/v1/{resource}
```

## Common Endpoints

### Phone Numbers

#### List Phone Numbers
```bash
maton api '/quo/v1/phone-numbers'
```

### Users

#### List Users
```bash
maton api '/quo/v1/users?maxResults=50'
```

#### Get User
```bash
maton api '/quo/v1/users/{userId}'
```

### Messages

#### Send Text Message
```bash
maton api -X POST '/quo/v1/messages' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "content": "Hello, world!",
  "from": "PN123abc",
  "to": ["+15555555555"]
}
EOF
```

#### List Messages
```bash
maton api '/quo/v1/messages?phoneNumberId=PN123abc&participants[]=+15555555555&maxResults=100'
```

#### Get Message
```bash
maton api '/quo/v1/messages/{messageId}'
```

### Calls

#### List Calls
```bash
maton api '/quo/v1/calls?phoneNumberId=PN123abc&participants[]=+15555555555&maxResults=100'
```

#### Get Call
```bash
maton api '/quo/v1/calls/{callId}'
```

#### Get Call Recordings
```bash
maton api '/quo/v1/call-recordings/{callId}'
```

#### Get Call Summary
```bash
maton api '/quo/v1/call-summaries/{callId}'
```

#### Get Call Transcript
```bash
maton api '/quo/v1/call-transcripts/{callId}'
```

#### Get Call Voicemail
```bash
maton api '/quo/v1/call-voicemails/{callId}'
```

### Contacts

#### List Contacts
```bash
maton api '/quo/v1/contacts?maxResults=50'
```

#### Get Contact
```bash
maton api '/quo/v1/contacts/{contactId}'
```

#### Create Contact
```bash
maton api -X POST '/quo/v1/contacts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "defaultFields": {
    "firstName": "Jane",
    "lastName": "Doe",
    "phoneNumbers": [{"name": "mobile", "value": "+15555555555"}]
  }
}
EOF
```

#### Update Contact
```bash
maton api -X PATCH '/quo/v1/contacts/{contactId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "defaultFields": {
    "company": "New Company"
  }
}
EOF
```

#### Delete Contact
```bash
maton api -X DELETE '/quo/v1/contacts/{contactId}'
```

#### Get Contact Custom Fields
```bash
maton api '/quo/v1/contact-custom-fields'
```

### Conversations

#### List Conversations
```bash
maton api '/quo/v1/conversations?maxResults=100'
```

### Webhooks

#### List Webhooks
```bash
maton api '/quo/v1/webhooks'
```

#### Get Webhook
```bash
maton api '/quo/v1/webhooks/{webhookId}'
```

#### Create Webhook
```bash
maton api -X POST '/quo/v1/webhooks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/webhooks/calls",
  "resourceType": "call"
}
EOF
```

Resource types: `call`, `message`, `callSummary`, `callTranscript`

#### Delete Webhook
```bash
maton api -X DELETE '/quo/v1/webhooks/{webhookId}'
```

## Notes

- Phone number IDs start with `PN`
- User IDs start with `US`
- Call/Message IDs start with `AC`
- Phone numbers must be in E.164 format (e.g., `+15555555555`)
- Uses token-based pagination with `pageToken` parameter
- Maximum 1600 characters per SMS message
- List calls requires exactly 1 participant (1:1 conversations only)

## Resources

- [Quo API Introduction](https://www.quo.com/docs/mdx/api-reference/introduction)
- [Quo API Authentication](https://www.quo.com/docs/mdx/api-reference/authentication)
- [Quo Support Center](https://support.quo.com/core-concepts/integrations/api)
