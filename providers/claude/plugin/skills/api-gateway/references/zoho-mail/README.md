# Zoho Mail Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `zoho-mail`
**Base URL proxied:** `mail.zoho.com`

## API Path Pattern

```
/zoho-mail/api/{resource}
```

## Common Endpoints

### Accounts

```bash
# Get all accounts
maton api '/zoho-mail/api/accounts'

# Get account details
maton api '/zoho-mail/api/accounts/{accountId}'
```

### Folders

```bash
# List all folders
maton api '/zoho-mail/api/accounts/{accountId}/folders'

# Create folder
maton api -X POST '/zoho-mail/api/accounts/{accountId}/folders' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "folderName": "My Folder"
}
EOF

# Rename folder
maton api -X PUT '/zoho-mail/api/accounts/{accountId}/folders/{folderId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "folderName": "Renamed Folder"
}
EOF

# Delete folder
maton api -X DELETE '/zoho-mail/api/accounts/{accountId}/folders/{folderId}'
```

### Labels

```bash
# List labels
maton api '/zoho-mail/api/accounts/{accountId}/labels'

# Create label
maton api -X POST '/zoho-mail/api/accounts/{accountId}/labels' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "labelName": "Important"
}
EOF

# Update label
maton api -X PUT '/zoho-mail/api/accounts/{accountId}/labels/{labelId}'

# Delete label
maton api -X DELETE '/zoho-mail/api/accounts/{accountId}/labels/{labelId}'
```

### Messages

```bash
# List emails in folder
maton api '/zoho-mail/api/accounts/{accountId}/messages/view?folderId={folderId}&limit=50'

# Search emails
maton api '/zoho-mail/api/accounts/{accountId}/messages/search?searchKey={query}'

# Get email content
maton api '/zoho-mail/api/accounts/{accountId}/folders/{folderId}/messages/{messageId}/content'

# Get email headers
maton api '/zoho-mail/api/accounts/{accountId}/folders/{folderId}/messages/{messageId}/header'

# Get email metadata
maton api '/zoho-mail/api/accounts/{accountId}/folders/{folderId}/messages/{messageId}/details'

# Get original MIME message
maton api '/zoho-mail/api/accounts/{accountId}/messages/{messageId}/originalmessage'

# Send email
maton api -X POST '/zoho-mail/api/accounts/{accountId}/messages' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "fromAddress": "sender@yourdomain.com",
  "toAddress": "recipient@example.com",
  "subject": "Subject",
  "content": "Email body",
  "mailFormat": "html"
}
EOF

# Reply to email
maton api -X POST '/zoho-mail/api/accounts/{accountId}/messages/{messageId}'

# Update message (mark read, move, flag, archive, spam)
maton api -X PUT '/zoho-mail/api/accounts/{accountId}/updatemessage' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "messageId": ["messageId1"],
  "folderId": "folderId",
  "mode": "markAsRead"
}
EOF

# Delete email
maton api -X DELETE '/zoho-mail/api/accounts/{accountId}/folders/{folderId}/messages/{messageId}'
```

### Attachments

```bash
# Get attachment info
maton api '/zoho-mail/api/accounts/{accountId}/folders/{folderId}/messages/{messageId}/attachmentinfo'

# Download attachment
maton api '/zoho-mail/api/accounts/{accountId}/folders/{folderId}/messages/{messageId}/attachments/{attachmentId}'
```

Upload attachment (raw file body; `fileName` is required, `isInline` optional):

```bash
maton api -X POST '/zoho-mail/api/accounts/{accountId}/messages/attachments?fileName=document.pdf' \
  -H 'Content-Type: application/octet-stream' \
  --input '{file_path}'
```

## Update Message Modes

| Mode | Description |
|------|-------------|
| `markAsRead` | Mark messages as read |
| `markAsUnread` | Mark messages as unread |
| `moveMessage` | Move messages (requires `destfolderId`) |
| `setFlag` | Set flag (requires `flagid`) |
| `applyLabel` | Apply labels (requires `labelId`) |
| `archive` | Archive messages |
| `unArchive` | Unarchive messages |
| `spam` | Mark as spam |
| `notSpam` | Mark as not spam |

### Set Flag on Messages

```bash
maton api -X PUT '/zoho-mail/api/accounts/{accountId}/updatemessage' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "mode": "setFlag",
  "messageId": ["messageId1"],
  "flagid": "important"
}
EOF
```

**Flag ID Options:**
- `info` - Info flag (blue)
- `important` - Important flag (red)
- `followup` - Follow-up flag (orange)
- `flag_not_set` - Remove flag

**Optional:** `threadId`, `isFolderSpecific`, `folderId`, `isArchive`

### Apply Label to Messages

```bash
maton api -X PUT '/zoho-mail/api/accounts/{accountId}/updatemessage' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "mode": "applyLabel",
  "messageId": ["messageId1"],
  "labelId": ["labelId1", "labelId2"]
}
EOF
```

**Required:** Either `messageId` or `threadId` array, plus `labelId` array

**Optional:** `isFolderSpecific`, `folderId`, `isArchive`

## Default Folders

| Folder | Type |
|--------|------|
| Inbox | `Inbox` |
| Drafts | `Drafts` |
| Templates | `Templates` |
| Snoozed | `Snoozed` |
| Sent | `Sent` |
| Spam | `Spam` |
| Trash | `Trash` |
| Outbox | `Outbox` |

## Notes

- Account IDs are required for most operations - get via `/api/accounts`
- Message IDs and Folder IDs are numeric strings
- The `fromAddress` must be associated with the authenticated account
- Uses offset-based pagination with `start` and `limit` parameters
- Some operations require additional OAuth scopes

## Resources

- [Zoho Mail API Overview](https://www.zoho.com/mail/help/api/overview.html)
- [Email Messages API](https://www.zoho.com/mail/help/api/email-api.html)
- [Folders API](https://www.zoho.com/mail/help/api/get-all-folder-details.html)
