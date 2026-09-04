# Twilio Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `twilio`
**Base URL proxied:** `api.twilio.com`

## API Path Pattern

```
/twilio/2010-04-01/Accounts/{AccountSid}/{resource}.json
```

**Important:** Most Twilio endpoints require your Account SID in the path. Get it from `/Accounts.json`.

## Common Endpoints

### Accounts

#### List Accounts
```bash
maton api '/twilio/2010-04-01/Accounts.json'
```

#### Get Account
```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}.json'
```

### Messages (SMS/MMS)

#### List Messages
```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Messages.json'
```

#### Send Message
```bash
maton api -X POST '/twilio/2010-04-01/Accounts/{AccountSid}/Messages.json' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
To=+15559876543&From=+15551234567&Body=Hello%20from%20Twilio!
EOF
```

#### Get Message
```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Messages/{MessageSid}.json'
```

#### Delete Message
```bash
maton api -X DELETE '/twilio/2010-04-01/Accounts/{AccountSid}/Messages/{MessageSid}.json'
```

### Calls (Voice)

#### List Calls
```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Calls.json'
```

#### Make Call
```bash
maton api -X POST '/twilio/2010-04-01/Accounts/{AccountSid}/Calls.json' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
To=+15559876543&From=+15551234567&Url=https://example.com/twiml
EOF
```

#### Get Call
```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Calls/{CallSid}.json'
```

#### End Call
```bash
maton api -X POST '/twilio/2010-04-01/Accounts/{AccountSid}/Calls/{CallSid}.json' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
Status=completed
EOF
```

### Phone Numbers

#### List Incoming Phone Numbers
```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/IncomingPhoneNumbers.json'
```

#### Get Phone Number
```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/IncomingPhoneNumbers/{PhoneNumberSid}.json'
```

#### Update Phone Number
```bash
maton api -X POST '/twilio/2010-04-01/Accounts/{AccountSid}/IncomingPhoneNumbers/{PhoneNumberSid}.json' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
FriendlyName=Updated%20Name
EOF
```

### Applications

#### List Applications
```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Applications.json'
```

#### Create Application
```bash
maton api -X POST '/twilio/2010-04-01/Accounts/{AccountSid}/Applications.json' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
FriendlyName=My%20App&VoiceUrl=https://example.com/voice
EOF
```

#### Delete Application
```bash
maton api -X DELETE '/twilio/2010-04-01/Accounts/{AccountSid}/Applications/{ApplicationSid}.json'
```

### Queues

#### List Queues
```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Queues.json'
```

#### Create Queue
```bash
maton api -X POST '/twilio/2010-04-01/Accounts/{AccountSid}/Queues.json' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
FriendlyName=Support%20Queue&MaxSize=100
EOF
```

### Usage Records

#### List Usage Records
```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Usage/Records.json'
```

## Pagination

Uses page-based pagination:

```bash
maton api '/twilio/2010-04-01/Accounts/{AccountSid}/Messages.json?PageSize=50&Page=0'
```

**Parameters:**
- `PageSize` - Results per page (default: 50)
- `Page` - Page number (0-indexed)

Response includes `next_page_uri` for fetching next page.

## Notes

- All endpoints require `/2010-04-01/` API version prefix
- Request bodies use `application/x-www-form-urlencoded` (not JSON)
- Phone numbers must be in E.164 format (+15551234567)
- SID prefixes: AC (account), SM/MM (messages), CA (calls), PN (phone numbers), AP (applications), QU (queues)
- POST is used for both creating and updating resources
- DELETE returns 204 No Content on success

## Resources

- [Twilio API Overview](https://www.twilio.com/docs/usage/api)
- [Messages API](https://www.twilio.com/docs/messaging/api/message-resource)
- [Calls API](https://www.twilio.com/docs/voice/api/call-resource)
