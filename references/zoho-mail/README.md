# Zoho Mail

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `zoho-mail`
**Upstream base URL:** `mail.zoho.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://mail.zoho.com/api/accounts`
- Gateway: `https://api.maton.ai/zoho-mail/api/accounts`

### Account API

#### Get All Accounts

Retrieve all mail accounts for the authenticated user.

```bash
maton api '/zoho-mail/api/accounts'
```

#### Get Account Details

```bash
maton api '/zoho-mail/api/accounts/{accountId}'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

### Folder API

#### List All Folders

```bash
maton api '/zoho-mail/api/accounts/{accountId}/folders'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "status": {
    "code": 200,
    "description": "success"
  },
  "data": [
    {
      "folderId": "1367000000000008014",
      "folderName": "Inbox",
      "folderType": "Inbox",
      "path": "/Inbox",
      "imapAccess": true,
      "isArchived": 0,
      "URI": "https://mail.zoho.com/api/accounts/1367000000000008002/folders/1367000000000008014"
    },
    {
      "folderId": "1367000000000008016",
      "folderName": "Drafts",
      "folderType": "Drafts",
      "path": "/Drafts",
      "imapAccess": true,
      "isArchived": 0
    }
  ]
}
```

#### Create Folder

```bash
maton api -X POST '/zoho-mail/api/accounts/{accountId}/folders' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "folderName": "My Custom Folder"
}
JSON
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### Rename Folder

```bash
maton api -X PUT '/zoho-mail/api/accounts/{accountId}/folders/{folderId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "folderName": "Renamed Folder"
}
JSON
```

**Note:** `{accountId}` and `{folderId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Folder

```bash
maton api '/zoho-mail/api/accounts/{accountId}/folders/{folderId}' -X DELETE
```

**Note:** `{accountId}` and `{folderId}` are placeholders. Replace each of them with real values before sending the request.

### Label API

#### List Labels

```bash
maton api '/zoho-mail/api/accounts/{accountId}/labels'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Label

```bash
maton api -X POST '/zoho-mail/api/accounts/{accountId}/labels' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "labelName": "Important"
}
JSON
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Label

```bash
maton api -X PUT '/zoho-mail/api/accounts/{accountId}/labels/{labelId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "labelName": "Updated Label"
}
JSON
```

**Note:** `{accountId}` and `{labelId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Label

```bash
maton api '/zoho-mail/api/accounts/{accountId}/labels/{labelId}' -X DELETE
```

**Note:** `{accountId}` and `{labelId}` are placeholders. Replace each of them with real values before sending the request.

### Email Message API

#### List Emails in Folder

```bash
maton api '/zoho-mail/api/accounts/{accountId}/messages/view?folderId={folderId}'
```

**Note:** `{accountId}` and `{folderId}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `folderId` | long | Folder ID to list messages from |
| `limit` | integer | Number of messages to return (default: 50) |
| `start` | integer | Offset for pagination |
| `sortBy` | string | Sort field (e.g., `date`) |
| `sortOrder` | boolean | `true` for ascending, `false` for descending |

**Example:**

```bash
maton api '/zoho-mail/api/accounts/{accountId}/messages/view?folderId={folderId}&limit=10'
```

**Note:** `{accountId}` and `{folderId}` are placeholders. Replace each of them with real values before sending the request.

#### Search Emails

```bash
maton api '/zoho-mail/api/accounts/{accountId}/messages/search?searchKey={query}'
```

**Note:** `{accountId}` and `{query}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `searchKey` | string | Search query |
| `limit` | integer | Number of results to return |
| `start` | integer | Offset for pagination |

#### Get Email Content

```bash
maton api '/zoho-mail/api/accounts/{accountId}/folders/{folderId}/messages/{messageId}/content'
```

**Note:** `{accountId}`, `{folderId}` and `{messageId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Email Headers

```bash
maton api '/zoho-mail/api/accounts/{accountId}/folders/{folderId}/messages/{messageId}/header'
```

**Note:** `{accountId}`, `{folderId}` and `{messageId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Email Metadata

```bash
maton api '/zoho-mail/api/accounts/{accountId}/folders/{folderId}/messages/{messageId}/details'
```

**Note:** `{accountId}`, `{folderId}` and `{messageId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Original Message (MIME)

```bash
maton api '/zoho-mail/api/accounts/{accountId}/messages/{messageId}/originalmessage'
```

**Note:** `{accountId}` and `{messageId}` are placeholders. Replace each of them with real values before sending the request.

#### Send Email

```bash
maton api -X POST '/zoho-mail/api/accounts/{accountId}/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "fromAddress": "sender@yourdomain.com",
  "toAddress": "recipient@example.com",
  "subject": "Email Subject",
  "content": "Email body content",
  "mailFormat": "html"
}
JSON
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

**Request Body Fields:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `fromAddress` | string | Yes | Sender's email address |
| `toAddress` | string | Yes | Recipient's email address |
| `subject` | string | Yes | Email subject |
| `content` | string | Yes | Email body content |
| `ccAddress` | string | No | CC recipient |
| `bccAddress` | string | No | BCC recipient |
| `mailFormat` | string | No | `html` or `plaintext` (default: `html`) |
| `askReceipt` | string | No | `yes` or `no` for read receipt |
| `encoding` | string | No | Character encoding (default: `UTF-8`) |

**Example - Send Email:**

```bash
maton api -X POST '/zoho-mail/api/accounts/{accountId}/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "fromAddress": "sender@yourdomain.com",
  "toAddress": "recipient@example.com",
  "subject": "Hello from Zoho Mail API",
  "content": "<h1>Hello!</h1><p>This is a test email.</p>",
  "mailFormat": "html"
}
JSON
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

**Scheduling Parameters (Optional):**

| Field | Type | Description |
|-------|------|-------------|
| `isSchedule` | boolean | Enable scheduling |
| `scheduleType` | integer | 1-5 for preset times; 6 for custom |
| `timeZone` | string | Required if scheduleType=6 (e.g., `GMT 5:30`) |
| `scheduleTime` | string | Required if scheduleType=6 (format: `MM/DD/YYYY HH:MM:SS`) |

#### Reply to Email

```bash
maton api -X POST '/zoho-mail/api/accounts/{accountId}/messages/{messageId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "fromAddress": "sender@yourdomain.com",
  "toAddress": "recipient@example.com",
  "subject": "Re: Original Subject",
  "content": "Reply content"
}
JSON
```

**Note:** `{accountId}` and `{messageId}` are placeholders. Replace each of them with real values before sending the request.

#### Save Draft

```bash
maton api -X POST '/zoho-mail/api/accounts/{accountId}/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "fromAddress": "sender@yourdomain.com",
  "toAddress": "recipient@example.com",
  "subject": "Draft Subject",
  "content": "Draft content",
  "mode": "draft"
}
JSON
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Message (Mark as Read/Unread, Move, Flag)

```bash
maton api -X PUT '/zoho-mail/api/accounts/{accountId}/updatemessage' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messageId": ["messageId1", "messageId2"],
  "folderId": "folderId",
  "mode": "markAsRead"
}
JSON
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

**Mode Options:**
- `markAsRead` - Mark messages as read
- `markAsUnread` - Mark messages as unread
- `moveMessage` - Move messages (requires `destfolderId`)
- `setFlag` - Set flag (requires `flagid`)
- `applyLabel` - Apply labels (requires `labelId`)
- `archive` - Archive messages
- `unArchive` - Unarchive messages
- `spam` - Mark as spam
- `notSpam` - Mark as not spam

**Example - Mark as Read:**

```bash
maton api -X PUT '/zoho-mail/api/accounts/{accountId}/updatemessage' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "messageId": [
    "1234567890123456789"
  ],
  "folderId": "9876543210987654321",
  "mode": "markAsRead"
}
JSON
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### Set Flag on Messages

Flag messages with a color/status indicator.

```bash
maton api -X PUT '/zoho-mail/api/accounts/{accountId}/updatemessage' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "mode": "setFlag",
  "messageId": [
    "1234567890123456789"
  ],
  "flagid": "important",
  "isFolderSpecific": true,
  "folderId": "9876543210987654321"
}
JSON
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

**Flag ID Options:**

| Flag ID | Description |
|---------|-------------|
| `info` | Info flag (blue) |
| `important` | Important flag (red) |
| `followup` | Follow-up flag (orange) |
| `flag_not_set` | Remove flag |

**Request body:**
- `threadId` (optional) - Array of thread IDs (alternative to messageId)
- `isFolderSpecific` (optional) - Set to `true` if using `folderId`
- `folderId` (optional) - Folder ID (required if `isFolderSpecific` is true)
- `isArchive` (optional) - Set to `true` to include archived emails

**Example - Flag as Important:**

```bash
maton api -X PUT '/zoho-mail/api/accounts/{accountId}/updatemessage' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "mode": "setFlag",
  "messageId": [
    "1234567890123456789"
  ],
  "flagid": "important",
  "isFolderSpecific": true,
  "folderId": "9876543210987654321"
}
JSON
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### Apply Label to Messages

Apply one or more labels to messages or threads.

```bash
maton api -X PUT '/zoho-mail/api/accounts/{accountId}/updatemessage' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "mode": "applyLabel",
  "messageId": ["messageId1"],
  "labelId": ["labelId1", "labelId2"]
}
JSON
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

**Request body:**
- `mode` (required) - Must be `"applyLabel"`
- `messageId` (required) or `threadId` - Array of message/thread IDs
- `labelId` (required) - Array of label IDs to apply
- `isFolderSpecific` (optional) - Set to `true` if using `folderId`
- `folderId` (optional) - Folder ID (required if `isFolderSpecific` is true)
- `isArchive` (optional) - Set to `true` to include archived emails

**Example - Apply Labels:**

```bash
maton api -X PUT '/zoho-mail/api/accounts/{accountId}/updatemessage' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "mode": "applyLabel",
  "messageId": [
    "1234567890123456789"
  ],
  "labelId": [
    "111222333444555666",
    "777888999000111222"
  ],
  "isFolderSpecific": true,
  "folderId": "9876543210987654321"
}
JSON
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Get label IDs by calling `GET /zoho-mail/api/accounts/{accountId}/labels` first.

#### Delete Email

```bash
maton api '/zoho-mail/api/accounts/{accountId}/folders/{folderId}/messages/{messageId}' -X DELETE
```

**Note:** `{accountId}`, `{folderId}` and `{messageId}` are placeholders. Replace each of them with real values before sending the request.

### Attachment API

#### Upload Attachment

```bash
maton api -X POST '/zoho-mail/api/accounts/{accountId}/messages/attachments?fileName=file.pdf' -H 'Content-Type: multipart/form-data'
```

**Example:**
`maton api` sends a body verbatim but does not build a multipart envelope, so assemble the body first and hand it to `--input`. Nothing here handles a credential — the CLI still injects it.

```bash
BOUNDARY="maton-$$"
{
  printf -- '--%s\r\nContent-Disposition: form-data; name="attach"; filename="file.pdf"\r\nContent-Type: application/pdf\r\n\r\n' "$BOUNDARY"
  cat /path/to/file.pdf
  printf -- '\r\n--%s--\r\n' "$BOUNDARY"
} > /tmp/zoho-mail-upload.body

maton api -X POST '/zoho-mail/api/accounts/{accountId}/messages/attachments?fileName=file.pdf' \
  -H "Content-Type: multipart/form-data; boundary=$BOUNDARY" \
  --input /tmp/zoho-mail-upload.body
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Attachment Info

```bash
maton api '/zoho-mail/api/accounts/{accountId}/folders/{folderId}/messages/{messageId}/attachmentinfo'
```

**Note:** `{accountId}`, `{folderId}` and `{messageId}` are placeholders. Replace each of them with real values before sending the request.

#### Download Attachment

```bash
maton api '/zoho-mail/api/accounts/{accountId}/folders/{folderId}/messages/{messageId}/attachments/{attachmentId}'
```

**Note:** `{accountId}`, `{folderId}`, `{messageId}` and `{attachmentId}` are placeholders. Replace each of them with real values before sending the request.

### Pagination

Zoho Mail uses offset-based pagination:

```bash
maton api '/zoho-mail/api/accounts/{accountId}/messages/view?folderId={folderId}&start=0&limit=50'
```

**Note:** `{accountId}` and `{folderId}` are placeholders. Replace each of them with real values before sending the request.

- `start`: Offset index (default: 0)
- `limit`: Number of records to return (default: 50)

For subsequent pages, increment `start` by `limit`:
- Page 1: `start=0&limit=50`
- Page 2: `start=50&limit=50`
- Page 3: `start=100&limit=50`

### Notes

- Account IDs are required for most operations - first call `/api/accounts` to get your account ID
- Message IDs and Folder IDs are numeric strings
- The `fromAddress` must be associated with the authenticated account
- Default folders include: Inbox, Drafts, Templates, Snoozed, Sent, Spam, Trash, Outbox
- Supported encodings: Big5, EUC-JP, EUC-KR, GB2312, ISO-2022-JP, ISO-8859-1, KOI8-R, Shift_JIS, US-ASCII, UTF-8, WINDOWS-1251
- Some operations (labels, folder management, sending) require additional OAuth scopes. If you receive an `INVALID_OAUTHSCOPE` error, contact Maton support at support@maton.ai with the specific operations/APIs you need and your use-case

### Resources

- [Zoho Mail API Overview](https://www.zoho.com/mail/help/api/overview.html)
- [Email Messages API](https://www.zoho.com/mail/help/api/email-api.html)
- [Folders API](https://www.zoho.com/mail/help/api/get-all-folder-details.html)
- [Maton CLI Manual](https://cli.maton.ai/manual)
