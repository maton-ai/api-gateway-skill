# Outlook Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `outlook`
**Service API host:** `graph.microsoft.com`

## API Path Pattern

```
/outlook/v1.0/me/{resource}
```

## Review Requirements

This file documents Outlook route shapes. For any non-read endpoint below, first retrieve the target item where possible, verify the connected mailbox, and confirm the exact recipient, resource, payload, and expected result with the user. Prefer draft and read-before-change workflows.

## Common Endpoints

### User Profile
```bash
maton api '/outlook/v1.0/me'
```

Example:

```bash
maton outlook whoami
```

### Mail Folders

#### List Mail Folders
```bash
maton api '/outlook/v1.0/me/mailFolders'
```

Well-known folder names: `Inbox`, `Drafts`, `SentItems`, `DeletedItems`, `Archive`, `JunkEmail`

Example:

```bash
maton outlook folder list
```

#### Get Mail Folder
```bash
maton api '/outlook/v1.0/me/mailFolders/{folderId}'
```

Example:

```bash
maton outlook folder view {folderId}
```

#### Create Mail Folder
```bash
maton api -X POST '/outlook/v1.0/me/mailFolders' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "displayName": "My Folder"
}
EOF
```

Example:

```bash
maton outlook folder create --name "My Folder"
```

### Messages

#### List Messages
```bash
maton api '/outlook/v1.0/me/messages'
```

Example:

```bash
maton outlook message list
```

From specific folder:
```bash
maton api '/outlook/v1.0/me/mailFolders/Inbox/messages'
```

Example:

```bash
maton outlook message list --folder Inbox
```

With filter:
```bash
maton api '/outlook/v1.0/me/messages?$filter=isRead%20eq%20false&$top=10'
```

Example:

```bash
maton outlook message list --filter "isRead eq false" --top 10
```

#### Get Message
```bash
maton api '/outlook/v1.0/me/messages/{messageId}'
```

Example:

```bash
maton outlook message view {messageId}
```

#### Send Message
```bash
maton api -X POST '/outlook/v1.0/me/sendMail' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "message": {
    "subject": "Hello",
    "body": {
      "contentType": "Text",
      "content": "This is the email body."
    },
    "toRecipients": [
      {
        "emailAddress": {
          "address": "recipient@example.com"
        }
      }
    ]
  },
  "saveToSentItems": true
}
EOF
```

Example:

```bash
maton outlook message send --to recipient@example.com --subject "Hello" --body "This is the email body."
```

#### Create Draft
```bash
maton api -X POST '/outlook/v1.0/me/messages' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "subject": "Hello",
  "body": {
    "contentType": "Text",
    "content": "This is the email body."
  },
  "toRecipients": [
    {
      "emailAddress": {
        "address": "recipient@example.com"
      }
    }
  ]
}
EOF
```

Example:

```bash
maton outlook message draft --to recipient@example.com --subject "Hello" --body "This is the email body."
```

#### Send Existing Draft
```bash
maton api -X POST '/outlook/v1.0/me/messages/{messageId}/send'
```

Example:

```bash
maton outlook message send {messageId}
```

#### Update Message (Mark as Read)
```bash
maton api -X PATCH '/outlook/v1.0/me/messages/{messageId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "isRead": true
}
EOF
```

Example:

```bash
maton outlook message update {messageId} --read
```

#### Delete Message
```bash
maton api -X DELETE '/outlook/v1.0/me/messages/{messageId}'
```

Example:

```bash
maton outlook message delete {messageId}
```

#### Move Message
```bash
maton api -X POST '/outlook/v1.0/me/messages/{messageId}/move' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "destinationId": "{folderId}"
}
EOF
```

Example:

```bash
maton outlook message move {messageId} --to {folderId}
```

#### Search Messages

Example:

```bash
maton outlook message search "quarterly report"
```

### Calendar

#### List Calendars
```bash
maton api '/outlook/v1.0/me/calendars'
```

Example:

```bash
maton outlook calendar list
```

#### List Events
```bash
maton api '/outlook/v1.0/me/calendar/events'
```

Example:

```bash
maton outlook event list
```

With filter:
```bash
maton api "/outlook/v1.0/me/calendar/events?\$filter=start/dateTime%20ge%20'2024-01-01'&\$top=10"
```

Example:

```bash
maton outlook event list --filter "start/dateTime ge '2024-01-01'" --top 10
```

#### Get Event
```bash
maton api '/outlook/v1.0/me/events/{eventId}'
```

Example:

```bash
maton outlook event view {eventId}
```

#### Create Event
```bash
maton api -X POST '/outlook/v1.0/me/calendar/events' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "subject": "Meeting",
  "start": {
    "dateTime": "2024-01-15T10:00:00",
    "timeZone": "UTC"
  },
  "end": {
    "dateTime": "2024-01-15T11:00:00",
    "timeZone": "UTC"
  },
  "attendees": [
    {
      "emailAddress": {
        "address": "attendee@example.com"
      },
      "type": "required"
    }
  ]
}
EOF
```

Example:

```bash
maton outlook event create --subject "Meeting" --start 2024-01-15T10:00:00 --end 2024-01-15T11:00:00 --timezone UTC --attendees attendee@example.com
```

#### Delete Event
```bash
maton api -X DELETE '/outlook/v1.0/me/events/{eventId}'
```

Example:

```bash
maton outlook event delete {eventId}
```

### Contacts

#### List Contacts
```bash
maton api '/outlook/v1.0/me/contacts'
```

Example:

```bash
maton outlook contact list
```

#### Create Contact
```bash
maton api -X POST '/outlook/v1.0/me/contacts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "givenName": "John",
  "surname": "Doe",
  "emailAddresses": [
    {
      "address": "john.doe@example.com"
    }
  ]
}
EOF
```

Example:

```bash
maton outlook contact create --given-name John --surname Doe --email john.doe@example.com
```

#### Delete Contact
```bash
maton api -X DELETE '/outlook/v1.0/me/contacts/{contactId}'
```

Example:

```bash
maton outlook contact delete {contactId}
```

## OData Query Parameters

- `$top=10` - Limit results
- `$skip=20` - Skip results (pagination)
- `$select=subject,from` - Select specific fields
- `$filter=isRead eq false` - Filter results
- `$orderby=receivedDateTime desc` - Sort results
- `$search="keyword"` - Search content

## Security & Review Requirements

- **Outbound mail requires review.** Before delivery, confirm the exact recipients, subject, and body content with the user.
- **Removal actions require review.** Always retrieve and display the target resource first so the user can verify before confirming.
- **Prefer drafts over direct send.** Use `POST /outlook/v1.0/me/messages` to create a draft, then let the user review before sending with `POST /outlook/v1.0/me/messages/{messageId}/send`.
- **Moving messages** changes folder location — confirm the destination folder with the user.
- All write operations (send, delete, move, create events/contacts) require explicit user confirmation with specific resource details (message subject, event title, contact name).

## Pagination

Outlook uses cursor-based pagination via `@odata.nextLink`. The CLI handles this automatically with `--paginate`:

```bash
maton outlook message list --folder Inbox --paginate
```

## Notes

- Use `me` as the user identifier for the authenticated user
- Message body content types: `Text` or `HTML`
- Well-known folder names work as folder IDs: `Inbox`, `Drafts`, `SentItems`, etc.
- Calendar events use ISO 8601 datetime format

## Resources

- [Microsoft Graph API Overview](https://learn.microsoft.com/en-us/graph/api/overview)
- [Mail API](https://learn.microsoft.com/en-us/graph/api/resources/mail-api-overview)
- [Calendar API](https://learn.microsoft.com/en-us/graph/api/resources/calendar)
- [Contacts API](https://learn.microsoft.com/en-us/graph/api/resources/contact)
- [Query Parameters](https://learn.microsoft.com/en-us/graph/query-parameters)
- [Maton CLI Manual](https://cli.maton.ai/manual)
