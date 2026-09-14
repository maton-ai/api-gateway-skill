# Outlook

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `outlook`
**Upstream base URL:** `graph.microsoft.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://graph.microsoft.com/v1.0/me`
- Gateway: `https://api.maton.ai/outlook/v1.0/me`

**Important:** This file documents Outlook route shapes. For any non-read endpoint below, first retrieve the target item where possible, verify the connected mailbox, and confirm the exact recipient, resource, payload, and expected result with the user. Prefer draft and read-before-change workflows.

### User Profile API

#### Get User Profile

```bash
maton outlook whoami
```

Or with `maton api`:

```bash
maton api '/outlook/v1.0/me'
```

### Mail Folders API

#### List Mail Folders

```bash
maton outlook folder list
```

Or with `maton api`:

```bash
maton api '/outlook/v1.0/me/mailFolders'
```

**Note:** Well-known folder names: `Inbox`, `Drafts`, `SentItems`, `DeletedItems`, `Archive`, `JunkEmail`.

#### Get Mail Folder

```bash
maton outlook folder get {folderId}
```

Or with `maton api`:

```bash
maton api '/outlook/v1.0/me/mailFolders/{folderId}'
```

**Note:** `{folderId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Mail Folder

```bash
maton outlook folder create --name "My Folder"
```

Or with `maton api`:

```bash
maton api -X POST '/outlook/v1.0/me/mailFolders' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "displayName": "My Folder"
}
JSON
```

#### Delete Mail Folder

```bash
maton outlook folder delete {folderId}
```

Or with `maton api`:

```bash
maton api '/outlook/v1.0/me/mailFolders/{folderId}' -X DELETE
```

**Note:** `{folderId}` is a placeholder. Replace it with a real value before sending the request.

#### List Child Folders

```bash
maton outlook folder list --parent {folderId}
```

Or with `maton api`:

```bash
maton api '/outlook/v1.0/me/mailFolders/{folderId}/childFolders'
```

**Note:** `{folderId}` is a placeholder. Replace it with a real value before sending the request.

### Messages API

#### List Messages

```bash
maton outlook message list
```

Or with `maton api`:

```bash
maton api '/outlook/v1.0/me/messages'
```

**From a specific folder:**

```bash
maton outlook message list --folder Inbox
```

Or with `maton api`:

```bash
maton api '/outlook/v1.0/me/mailFolders/Inbox/messages'
```

**With a filter:**

```bash
maton outlook message list --filter "isRead eq false" --top 10
```

Or with `maton api`:

```bash
maton api '/outlook/v1.0/me/messages?$filter=isRead%20eq%20false&$top=10'
```

#### Get Message

```bash
maton outlook message get {messageId}
```

Or with `maton api`:

```bash
maton api '/outlook/v1.0/me/messages/{messageId}'
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Draft

```bash
maton outlook message draft --to recipient@example.com --subject "Hello" --body "This is the email body."
```

Or with `maton api`:

```bash
maton api -X POST '/outlook/v1.0/me/messages' -H 'Content-Type: application/json' --input - <<'JSON'
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
JSON
```

#### Send Message

```bash
maton outlook message send --to recipient@example.com --subject "Hello" --body "This is the email body."
```

Or with `maton api`:

```bash
maton api -X POST '/outlook/v1.0/me/sendMail' -H 'Content-Type: application/json' --input - <<'JSON'
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
JSON
```

#### Send Existing Draft

```bash
maton outlook message send {messageId}
```

Or with `maton api`:

```bash
maton api -X POST '/outlook/v1.0/me/messages/{messageId}/send'
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Message (Mark as Read)

```bash
maton outlook message update {messageId} --read
```

Or with `maton api`:

```bash
maton api -X PATCH '/outlook/v1.0/me/messages/{messageId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "isRead": true
}
JSON
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Message

```bash
maton outlook message delete {messageId}
```

Or with `maton api`:

```bash
maton api '/outlook/v1.0/me/messages/{messageId}' -X DELETE
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

#### Move Message

```bash
maton outlook message move {messageId} --to {folderId}
```

Or with `maton api`:

```bash
maton api -X POST '/outlook/v1.0/me/messages/{messageId}/move' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "destinationId": "{folderId}"
}
JSON
```

**Note:** `{messageId}` and `{folderId}` are placeholders. Replace each of them with real values before sending the request.

#### Search Messages

```bash
maton outlook message search "quarterly report"
```

Or with `maton api`:

```bash
maton api '/outlook/v1.0/me/messages?$search=%22quarterly%20report%22'
```

### Calendar API

#### List Calendars

```bash
maton outlook calendar list
```

Or with `maton api`:

```bash
maton api '/outlook/v1.0/me/calendars'
```

#### List Events

```bash
maton outlook event list
```

Or with `maton api`:

```bash
maton api '/outlook/v1.0/me/calendar/events'
```

**With a filter:**

```bash
maton outlook event list --filter "start/dateTime ge '2024-01-01'" --top 10
```

Or with `maton api`:

```bash
maton api "/outlook/v1.0/me/calendar/events?\$filter=start/dateTime%20ge%20'2024-01-01'&\$top=10"
```

#### Get Event

```bash
maton outlook event get {eventId}
```

Or with `maton api`:

```bash
maton api '/outlook/v1.0/me/events/{eventId}'
```

**Note:** `{eventId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Event

```bash
maton outlook event create --subject "Meeting" --start 2024-01-15T10:00:00 --end 2024-01-15T11:00:00 --timezone UTC --attendees attendee@example.com
```

Or with `maton api`:

```bash
maton api -X POST '/outlook/v1.0/me/calendar/events' -H 'Content-Type: application/json' --input - <<'JSON'
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
JSON
```

#### Delete Event

```bash
maton outlook event delete {eventId}
```

Or with `maton api`:

```bash
maton api '/outlook/v1.0/me/events/{eventId}' -X DELETE
```

**Note:** `{eventId}` is a placeholder. Replace it with a real value before sending the request.

### Contacts API

#### List Contacts

```bash
maton outlook contact list
```

Or with `maton api`:

```bash
maton api '/outlook/v1.0/me/contacts'
```

#### Get Contact

```bash
maton outlook contact get {contactId}
```

Or with `maton api`:

```bash
maton api '/outlook/v1.0/me/contacts/{contactId}'
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Contact

```bash
maton outlook contact create --given-name John --surname Doe --email john.doe@example.com
```

Or with `maton api`:

```bash
maton api -X POST '/outlook/v1.0/me/contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "givenName": "John",
  "surname": "Doe",
  "emailAddresses": [
    {
      "address": "john.doe@example.com"
    }
  ]
}
JSON
```

#### Delete Contact

```bash
maton outlook contact delete {contactId}
```

Or with `maton api`:

```bash
maton api '/outlook/v1.0/me/contacts/{contactId}' -X DELETE
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

### OData Query parameters

- `$top=10` - Limit results
- `$skip=20` - Skip results (pagination)
- `$select=subject,from` - Select specific fields
- `$filter=isRead eq false` - Filter results
- `$orderby=receivedDateTime desc` - Sort results
- `$search="keyword"` - Search content

### Security & Review Requirements

- **Outbound mail requires review.** Before delivery, confirm the exact recipients, subject, and body content with the user.
- **Removal actions require review.** Always retrieve and display the target resource first so the user can verify before confirming.
- **Prefer drafts over direct send.** Use `POST /outlook/v1.0/me/messages` to create a draft, then let the user review before sending with `POST /outlook/v1.0/me/messages/{messageId}/send`.
- **Moving messages** changes folder location — confirm the destination folder with the user.
- All write operations (send, delete, move, create events/contacts) require explicit user confirmation with specific resource details (message subject, event title, contact name).

### Pagination

Outlook uses cursor-based pagination via `@odata.nextLink`. The CLI handles this automatically with `--paginate`:

```bash
maton outlook message list --folder Inbox --paginate
```

### Notes

- Use `me` as the user identifier for the authenticated user
- Message body content types: `Text` or `HTML`
- Well-known folder names work as folder IDs: `Inbox`, `Drafts`, `SentItems`, etc.
- Calendar events use ISO 8601 datetime format

### Resources

- [Microsoft Graph API Overview](https://learn.microsoft.com/en-us/graph/api/overview)
- [Mail API](https://learn.microsoft.com/en-us/graph/api/resources/mail-api-overview)
- [Calendar API](https://learn.microsoft.com/en-us/graph/api/resources/calendar)
- [Contacts API](https://learn.microsoft.com/en-us/graph/api/resources/contact)
- [Query parameters](https://learn.microsoft.com/en-us/graph/query-parameters)
- [Maton CLI Manual](https://cli.maton.ai/manual)
