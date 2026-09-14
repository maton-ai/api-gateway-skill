# Gmail

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `google-mail`
**Upstream base URL:** `gmail.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://gmail.googleapis.com/gmail/v1/users/me/messages/send`
- Gateway: `https://api.maton.ai/google-mail/gmail/v1/users/me/messages/send`

### User Info API

#### Get Profile

```bash
maton google-mail whoami
```

Or with `maton api`:

```bash
maton api '/google-mail/gmail/v1/users/me/profile'
```

### Messages API

#### List Messages

```bash
maton google-mail message list -L 10
```

Or with `maton api`:

```bash
maton api '/google-mail/gmail/v1/users/me/messages?maxResults=10'
```

**With a query filter:**

```bash
maton google-mail message list --query 'is:unread' -L 10
```

Or with `maton api`:

```bash
maton api '/google-mail/gmail/v1/users/me/messages?q=is:unread&maxResults=10'
```

#### Get Message

```bash
maton google-mail message get {messageId} --headers
```

Or with `maton api`:

```bash
maton api '/google-mail/gmail/v1/users/me/messages/{messageId}'
```

**Metadata only:**

```bash
maton google-mail message get {messageId} --format metadata --metadata-header From,Subject,Date
```

Or with `maton api`:

```bash
maton api '/google-mail/gmail/v1/users/me/messages/{messageId}?format=metadata&metadataHeaders=From&metadataHeaders=Subject&metadataHeaders=Date'
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

#### Send Message

```bash
maton google-mail message send --to alice@example.com --subject 'Hello' --body 'Hi there!'
```

Or with `maton api`:

```bash
maton api -X POST '/google-mail/gmail/v1/users/me/messages/send' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "raw": "{base64EncodedEmail}"
}
JSON
```

**Note:** `{base64EncodedEmail}` is a placeholder. Replace it with a real value before sending the request.

#### Reply to Message

```bash
maton google-mail message reply {messageId} --body 'Thanks!'
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

#### Forward Message

```bash
maton google-mail message forward {messageId} --to dave@example.com --body 'FYI'
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

#### List Labels

```bash
maton google-mail label list
```

Or with `maton api`:

```bash
maton api '/google-mail/gmail/v1/users/me/labels'
```

#### List Threads

```bash
maton google-mail thread list -L 10
```

Or with `maton api`:

```bash
maton api '/google-mail/gmail/v1/users/me/threads?maxResults=10'
```

#### Get Thread

```bash
maton google-mail thread get {threadId}
```

Or with `maton api`:

```bash
maton api '/google-mail/gmail/v1/users/me/threads/{threadId}'
```

**Note:** `{threadId}` is a placeholder. Replace it with a real value before sending the request.

#### Modify Message Labels

```bash
maton google-mail message modify {messageId} --add-label STARRED --remove-label UNREAD
```

Or with `maton api`:

```bash
maton api -X POST '/google-mail/gmail/v1/users/me/messages/{messageId}/modify' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "addLabelIds": ["STARRED"],
  "removeLabelIds": ["UNREAD"]
}
JSON
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

#### Trash Message

```bash
maton google-mail message trash {messageId}
```

Or with `maton api`:

```bash
maton api -X POST '/google-mail/gmail/v1/users/me/messages/{messageId}/trash'
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

### Drafts API

#### Create Draft

```bash
maton google-mail draft create --to alice@example.com --subject 'Hello' --body 'Draft content here'
```

Or with `maton api`:

```bash
maton api -X POST '/google-mail/gmail/v1/users/me/drafts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "message": {
    "raw": "{base64EncodedEmail}"
  }
}
JSON
```

**Note:** `{base64EncodedEmail}` is a placeholder. Replace it with a real value before sending the request.

#### Update Draft

```bash
maton api -X PUT '/google-mail/gmail/v1/users/me/drafts/{draftId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "message": {
    "raw": "{base64EncodedEmail}"
  }
}
JSON
```

**Note:** `{draftId}` and `{base64EncodedEmail}` are placeholders. Replace each of them with real values before sending the request.

#### Send Draft

```bash
maton google-mail draft send {draftId}
```

Or with `maton api`:

```bash
maton api -X POST '/google-mail/gmail/v1/users/me/drafts/send' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "id": "{draftId}"
}
JSON
```

**Note:** `{draftId}` is a placeholder. Replace it with a real value before sending the request.

### Filtering

Use in the `q` parameter:
- `is:unread` - Unread messages
- `is:starred` - Starred messages
- `from:email@example.com` - From specific sender
- `to:email@example.com` - To specific recipient
- `subject:keyword` - Subject contains keyword
- `after:2024/01/01` - After date
- `before:2024/12/31` - Before date
- `has:attachment` - Has attachments
- `newer_than:7d` - Within last 7 days
- `older_than:1y` - Older than 1 year
- `label:LABEL_NAME` - Has specific label

### Pagination

Gmail uses `pageToken`-based pagination. The CLI handles this automatically with `--paginate`:

```bash
maton google-mail message list --query 'newer_than:7d' --paginate
```

For raw HTTP requests, pass the `nextPageToken` from the previous response as the `pageToken` query parameter.

### Examples

```bash
# List unread messages with full headers resolved
maton google-mail message list --hydrate

# Filter with jq — e.g. only message IDs from a specific sender
maton google-mail message list -L 20 --query 'from:boss@example.com' --json --jq '.messages[].id'
```

### Notes

- Use `me` as userId for the authenticated user
- Message body is base64url encoded in the `raw` field (RFC 2822 format)
- Common labels: `INBOX`, `SENT`, `DRAFT`, `STARRED`, `UNREAD`, `TRASH`, `SPAM`, `IMPORTANT`
- Rate limit: ~10 requests/sec per account
- Use `format=metadata` with `metadataHeaders` to fetch only headers and avoid downloading full message bodies

### Resources

- [Gmail API Overview](https://developers.google.com/gmail/api/reference/rest)
- [List Messages](https://developers.google.com/gmail/api/reference/rest/v1/users.messages/list)
- [Get Message](https://developers.google.com/gmail/api/reference/rest/v1/users.messages/get)
- [Send Message](https://developers.google.com/gmail/api/reference/rest/v1/users.messages/send)
- [Modify Message Labels](https://developers.google.com/gmail/api/reference/rest/v1/users.messages/modify)
- [Trash Message](https://developers.google.com/gmail/api/reference/rest/v1/users.messages/trash)
- [List Threads](https://developers.google.com/gmail/api/reference/rest/v1/users.threads/list)
- [Get Thread](https://developers.google.com/gmail/api/reference/rest/v1/users.threads/get)
- [List Labels](https://developers.google.com/gmail/api/reference/rest/v1/users.labels/list)
- [Create Draft](https://developers.google.com/gmail/api/reference/rest/v1/users.drafts/create)
- [Update Draft](https://developers.google.com/gmail/api/reference/rest/v1/users.drafts/update)
- [Send Draft](https://developers.google.com/gmail/api/reference/rest/v1/users.drafts/send)
- [Get Profile](https://developers.google.com/gmail/api/reference/rest/v1/users/getProfile)
- [Search Operators](https://support.google.com/mail/answer/7190)
- [Maton CLI Manual](https://cli.maton.ai/manual)
