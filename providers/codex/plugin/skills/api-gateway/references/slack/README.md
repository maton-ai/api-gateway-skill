# Slack Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `slack`
**Base URL proxied:** `slack.com`

## API Path Pattern

```
/slack/api/{method}
```

## Authentication

### Auth Test
```bash
maton api '/slack/api/auth.test'
```

Returns current user and team info.

Example:

```bash
maton slack whoami
```

---

## Messages

### Post Message
```bash
maton api -X POST '/slack/api/chat.postMessage' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789",
  "text": "Hello, world!"
}
EOF
```

Example:

```bash
maton slack message send --channel C0123456789 --text 'Hello, world!'
```

With blocks:
```bash
maton api -X POST '/slack/api/chat.postMessage' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789",
  "blocks": [
    {"type": "section", "text": {"type": "mrkdwn", "text": "*Bold* and _italic_"}}
  ]
}
EOF
```

Example:

```bash
maton slack message send --channel C0123456789 --blocks '[{"type":"section","text":{"type":"mrkdwn","text":"*Bold* and _italic_"}}]'
```

### Post Thread Reply
```bash
maton api -X POST '/slack/api/chat.postMessage' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789",
  "thread_ts": "1234567890.123456",
  "text": "This is a reply in a thread"
}
EOF
```

Example:

```bash
maton slack message reply --channel C0123456789 --thread-ts 1234567890.123456 --text 'This is a reply in a thread'
```

### Update Message
```bash
maton api -X POST '/slack/api/chat.update' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789",
  "ts": "1234567890.123456",
  "text": "Updated message"
}
EOF
```

Example:

```bash
maton slack message update --channel C0123456789 --ts 1234567890.123456 --text 'Updated message'
```

### Delete Message
```bash
maton api -X POST '/slack/api/chat.delete' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789",
  "ts": "1234567890.123456"
}
EOF
```

Example:

```bash
maton slack message delete --channel C0123456789 --ts 1234567890.123456
```

### Schedule Message
```bash
maton api -X POST '/slack/api/chat.scheduleMessage' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789",
  "text": "Scheduled message",
  "post_at": 1734567890
}
EOF
```

Example:

```bash
maton slack schedule create --channel C0123456789 --text 'Scheduled message' --post-at 1734567890
```

### List Scheduled Messages
```bash
maton api '/slack/api/chat.scheduledMessages.list'
```

Example:

```bash
maton slack schedule list
```

### Delete Scheduled Message
```bash
maton api -X POST '/slack/api/chat.deleteScheduledMessage' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789",
  "scheduled_message_id": "Q1234567890"
}
EOF
```

Example:

```bash
maton slack schedule delete --channel C0123456789 --id Q1234567890
```

### Get Permalink
```bash
maton api '/slack/api/chat.getPermalink?channel=C0123456789&message_ts=1234567890.123456'
```

Example:

```bash
maton slack message permalink --channel C0123456789 --message-ts 1234567890.123456
```

---

## Conversations (Channels)

### List Channels
```bash
maton api '/slack/api/conversations.list?types=public_channel,private_channel&limit=100'
```

Types: `public_channel`, `private_channel`, `im`, `mpim`

Example:

```bash
maton slack channel list --types public_channel,private_channel --limit 100
```

### Get Channel Info
```bash
maton api '/slack/api/conversations.info?channel=C0123456789'
```

Example:

```bash
maton slack channel view C0123456789
```

### Get Channel History
```bash
maton api '/slack/api/conversations.history?channel=C0123456789&limit=100'
```

Example:

```bash
maton slack message list --channel C0123456789 --limit 100
```

With time range:
```bash
maton api '/slack/api/conversations.history?channel=C0123456789&oldest=1234567890&latest=1234567899'
```

Example:

```bash
maton slack message list --channel C0123456789 --oldest 1234567890 --latest 1234567899
```

### Get Thread Replies
```bash
maton api '/slack/api/conversations.replies?channel=C0123456789&ts=1234567890.123456'
```

Example:

```bash
maton slack message replies --channel C0123456789 --ts 1234567890.123456
```

### Get Channel Members
```bash
maton api '/slack/api/conversations.members?channel=C0123456789&limit=100'
```

Example:

```bash
maton slack channel members C0123456789 --limit 100
```

### Create Channel
```bash
maton api -X POST '/slack/api/conversations.create' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "new-channel-name",
  "is_private": false
}
EOF
```

Example:

```bash
maton slack channel create --name new-channel-name
```

### Join Channel
```bash
maton api -X POST '/slack/api/conversations.join' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789"
}
EOF
```

Example:

```bash
maton slack channel join C0123456789
```

### Leave Channel
```bash
maton api -X POST '/slack/api/conversations.leave' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789"
}
EOF
```

Example:

```bash
maton slack channel leave C0123456789
```

### Archive Channel
```bash
maton api -X POST '/slack/api/conversations.archive' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789"
}
EOF
```

Example:

```bash
maton slack channel archive C0123456789
```

### Unarchive Channel
```bash
maton api -X POST '/slack/api/conversations.unarchive' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789"
}
EOF
```

Example:

```bash
maton slack channel unarchive C0123456789
```

### Rename Channel
```bash
maton api -X POST '/slack/api/conversations.rename' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789",
  "name": "new-name"
}
EOF
```

Example:

```bash
maton slack channel rename C0123456789 --name new-name
```

### Set Channel Topic
```bash
maton api -X POST '/slack/api/conversations.setTopic' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789",
  "topic": "Channel topic here"
}
EOF
```

Example:

```bash
maton slack channel set-topic C0123456789 --topic 'Channel topic here'
```

### Set Channel Purpose
```bash
maton api -X POST '/slack/api/conversations.setPurpose' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789",
  "purpose": "Channel purpose here"
}
EOF
```

Example:

```bash
maton slack channel set-purpose C0123456789 --purpose 'Channel purpose here'
```

### Invite to Channel
```bash
maton api -X POST '/slack/api/conversations.invite' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789",
  "users": "U0123456789,U9876543210"
}
EOF
```

Example:

```bash
maton slack channel invite C0123456789 --users U0123456789,U9876543210
```

### Kick from Channel
```bash
maton api -X POST '/slack/api/conversations.kick' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789",
  "user": "U0123456789"
}
EOF
```

Example:

```bash
maton slack channel kick C0123456789 --user U0123456789
```

### Mark Channel Read
```bash
maton api -X POST '/slack/api/conversations.mark' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789",
  "ts": "1234567890.123456"
}
EOF
```

Example:

```bash
maton slack channel mark C0123456789 --ts 1234567890.123456
```

---

## Direct Messages

### Open DM Conversation
```bash
maton api -X POST '/slack/api/conversations.open' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "users": "U0123456789"
}
EOF
```

Example:

```bash
maton slack conversation open --users U0123456789
```

For group DM:
```bash
maton api -X POST '/slack/api/conversations.open' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "users": "U0123456789,U9876543210"
}
EOF
```

Example:

```bash
maton slack conversation open --users U0123456789,U9876543210
```

### List DM Channels
```bash
maton api '/slack/api/conversations.list?types=im'
```

Example:

```bash
maton slack channel list --types im
```

### List Group DM Channels
```bash
maton api '/slack/api/conversations.list?types=mpim'
```

Example:

```bash
maton slack channel list --types mpim
```

### My Conversations
```bash
maton api '/slack/api/users.conversations?limit=100'
```

Example:

```bash
maton slack conversation list --limit 100
```

---

## Users

### List Users
```bash
maton api '/slack/api/users.list?limit=100'
```

Example:

```bash
maton slack user list --limit 100
```

### Get User Info
```bash
maton api '/slack/api/users.info?user=U0123456789'
```

Example:

```bash
maton slack user view U0123456789
```

### Get User Presence
```bash
maton api '/slack/api/users.getPresence?user=U0123456789'
```

Example:

```bash
maton slack user presence U0123456789
```

### Set User Presence
```bash
maton api -X POST '/slack/api/users.setPresence' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "presence": "away"
}
EOF
```

### Lookup User by Email
```bash
maton api '/slack/api/users.lookupByEmail?email=user@example.com'
```

Example:

```bash
maton slack user lookup --email user@example.com
```

---

## Reactions

### Add Reaction
```bash
maton api -X POST '/slack/api/reactions.add' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789",
  "name": "thumbsup",
  "timestamp": "1234567890.123456"
}
EOF
```

Example:

```bash
maton slack reaction add --channel C0123456789 --ts 1234567890.123456 --emoji thumbsup
```

### Remove Reaction
```bash
maton api -X POST '/slack/api/reactions.remove' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789",
  "name": "thumbsup",
  "timestamp": "1234567890.123456"
}
EOF
```

Example:

```bash
maton slack reaction remove --channel C0123456789 --ts 1234567890.123456 --emoji thumbsup
```

### Get Reactions on Message
```bash
maton api '/slack/api/reactions.get?channel=C0123456789&timestamp=1234567890.123456'
```

Example:

```bash
maton slack reaction get --channel C0123456789 --ts 1234567890.123456
```

### List My Reactions
```bash
maton api '/slack/api/reactions.list?limit=100'
```

Example:

```bash
maton slack reaction list --limit 100
```

---

## Stars

### List Stars
```bash
maton api '/slack/api/stars.list?limit=100'
```

Example:

```bash
maton slack star list --limit 100
```

### Add Star
```bash
maton api -X POST '/slack/api/stars.add' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789",
  "timestamp": "1234567890.123456"
}
EOF
```

Example:

```bash
maton slack star add --channel C0123456789 --ts 1234567890.123456
```

### Remove Star
```bash
maton api -X POST '/slack/api/stars.remove' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "channel": "C0123456789",
  "timestamp": "1234567890.123456"
}
EOF
```

Example:

```bash
maton slack star remove --channel C0123456789 --ts 1234567890.123456
```

---

## Bots

### Get Bot Info
```bash
maton api '/slack/api/bots.info?bot=B0123456789'
```

Example:

```bash
maton slack bot view B0123456789
```

Note: this expects the `B`-prefixed bot ID (from `bot_id` on a message), not the bot's `U`-prefixed user ID. Passing a `U…` ID returns `bot_not_found`.

---

## Files

### Upload File
```bash
# multipart/form-data is not expressible with `maton api`; call the gateway directly with `MATON_API_KEY` (see SKILL.md appendix).
python <<'EOF'
import json, os, urllib.request, uuid

# Maton API key from the environment; never print, log, or persist it.
TOKEN = os.environ["MATON_API_KEY"]

fields = {
    'channels': 'C0123456789',
    'content': 'file content here',
    'filename': 'example.txt',
    'title': 'Example File',
}

boundary = uuid.uuid4().hex
body = b''
for name, value in fields.items():
    body += f'--{boundary}\r\nContent-Disposition: form-data; name="{name}"\r\n\r\n{value}\r\n'.encode()
body += f'--{boundary}--\r\n'.encode()

req = urllib.request.Request('https://api.maton.ai/slack/api/files.upload', data=body, method='POST')
req.add_header('Authorization', f'Bearer {TOKEN}')
req.add_header('User-Agent', 'maton-gateway-skill/1.2')
req.add_header('Content-Type', f'multipart/form-data; boundary={boundary}')
print(json.dumps(json.load(urllib.request.urlopen(req)), indent=2))
EOF
```

### Upload File v2 (Get Upload URL)
```bash
maton api '/slack/api/files.getUploadURLExternal?filename=example.txt&length=1024'
```

### Complete File Upload
```bash
maton api -X POST '/slack/api/files.completeUploadExternal' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "files": [{"id": "F0123456789", "title": "My File"}],
  "channel_id": "C0123456789"
}
EOF
```

Example:

```bash
maton slack file upload --file ./example.txt --channel C0123456789 --title 'My File'
```

### Delete File
```bash
maton api -X POST '/slack/api/files.delete' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "file": "F0123456789"
}
EOF
```

Example:

```bash
maton slack file delete F0123456789
```

### Get File Info
```bash
maton api '/slack/api/files.info?file=F0123456789'
```

Example:

```bash
maton slack file view F0123456789
```

---

## Pagination

Slack uses cursor-based pagination. The CLI handles this automatically with `--paginate`:

```bash
maton slack message list --channel C0123456789 --paginate
```

For raw HTTP requests, use the `cursor` from `response_metadata.next_cursor`.

## Notes

- Authentication is automatic via managed OAuth
- Channel IDs: `C` (public), `G` (private/group), `D` (DM)
- User IDs start with `U`, Bot IDs start with `B`, Team IDs start with `T`
- Message timestamps (`ts`) are unique identifiers
- Use `mrkdwn` type for Slack-flavored markdown
- Thread replies use `thread_ts` to reference parent message
- If you encounter `missing_scope` errors, contact [Maton Support](mailto:support@maton.ai) to request additional scopes

## Resources

- [Slack API Methods](https://api.slack.com/methods)
- [Web API Reference](https://api.slack.com/web)
- [Block Kit Reference](https://api.slack.com/reference/block-kit)
- [Message Formatting](https://api.slack.com/reference/surfaces/formatting)
- [Rate Limits](https://api.slack.com/docs/rate-limits)
- [Maton CLI Manual](https://cli.maton.ai/manual)
