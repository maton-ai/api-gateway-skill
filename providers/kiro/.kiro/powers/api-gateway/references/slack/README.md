# Slack

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `slack`
**Upstream base URL:** `slack.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://slack.com/api/auth.test`
- Gateway: `https://api.maton.ai/slack/api/auth.test`

### Auth API

#### Auth Test

```bash
maton slack whoami
```

Returns current user and team info.

Or with `maton api`:

```bash
maton api '/slack/api/auth.test'
```

### Messages API

#### Post Message

```bash
maton slack message send --channel C0123456789 --text 'Hello, world!'
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/chat.postMessage' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789",
  "text": "Hello, world!"
}
JSON
```

With blocks:

```bash
maton slack message send --channel C0123456789 --blocks '[{"type":"section","text":{"type":"mrkdwn","text":"*Bold* and _italic_"}}]'
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/chat.postMessage' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789",
  "blocks": [
    {"type": "section", "text": {"type": "mrkdwn", "text": "*Bold* and _italic_"}}
  ]
}
JSON
```

#### Post /me-style Message

```bash
maton slack message me --channel C0123456789 --text 'is deploying'
```

#### Post Thread Reply

```bash
maton slack message reply --channel C0123456789 --thread-ts 1234567890.123456 --text 'This is a reply in a thread'
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/chat.postMessage' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789",
  "thread_ts": "1234567890.123456",
  "text": "This is a reply in a thread"
}
JSON
```

#### Update Message

```bash
maton slack message update --channel C0123456789 --ts 1234567890.123456 --text 'Updated message'
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/chat.update' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789",
  "ts": "1234567890.123456",
  "text": "Updated message"
}
JSON
```

#### Delete Message

```bash
maton slack message delete --channel C0123456789 --ts 1234567890.123456
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/chat.delete' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789",
  "ts": "1234567890.123456"
}
JSON
```

#### Schedule Message

```bash
maton slack schedule create --channel C0123456789 --text 'Scheduled message' --post-at 1734567890
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/chat.scheduleMessage' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789",
  "text": "Scheduled message",
  "post_at": 1734567890
}
JSON
```

#### List Scheduled Messages

```bash
maton slack schedule list
```

Or with `maton api`:

```bash
maton api '/slack/api/chat.scheduledMessages.list'
```

#### Delete Scheduled Message

```bash
maton slack schedule delete --channel C0123456789 --id Q1234567890
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/chat.deleteScheduledMessage' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789",
  "scheduled_message_id": "Q1234567890"
}
JSON
```

#### Get Permalink

```bash
maton slack message permalink --channel C0123456789 --message-ts 1234567890.123456
```

Or with `maton api`:

```bash
maton api '/slack/api/chat.getPermalink?channel=C0123456789&message_ts=1234567890.123456'
```

### Channels API

#### List Channels

```bash
maton slack channel list --types public_channel,private_channel --limit 100
```

Types: `public_channel`, `private_channel`, `im`, `mpim`

Or with `maton api`:

```bash
maton api '/slack/api/conversations.list?types=public_channel,private_channel&limit=100'
```

#### Get Channel Info

```bash
maton slack channel get C0123456789
```

Or with `maton api`:

```bash
maton api '/slack/api/conversations.info?channel=C0123456789'
```

#### Get Channel History

```bash
maton slack message list --channel C0123456789 --limit 100
```

Or with `maton api`:

```bash
maton api '/slack/api/conversations.history?channel=C0123456789&limit=100'
```

With time range:

```bash
maton slack message list --channel C0123456789 --oldest 1234567890 --latest 1234567899
```

Or with `maton api`:

```bash
maton api '/slack/api/conversations.history?channel=C0123456789&oldest=1234567890&latest=1234567899'
```

#### Get Thread Replies

```bash
maton slack message replies --channel C0123456789 --ts 1234567890.123456
```

Or with `maton api`:

```bash
maton api '/slack/api/conversations.replies?channel=C0123456789&ts=1234567890.123456'
```

#### Get Channel Members

```bash
maton slack channel members C0123456789 --limit 100
```

Or with `maton api`:

```bash
maton api '/slack/api/conversations.members?channel=C0123456789&limit=100'
```

#### Create Channel

```bash
maton slack channel create --name new-channel-name
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/conversations.create' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "new-channel-name",
  "is_private": false
}
JSON
```

#### Join Channel

```bash
maton slack channel join C0123456789
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/conversations.join' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789"
}
JSON
```

#### Leave Channel

```bash
maton slack channel leave C0123456789
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/conversations.leave' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789"
}
JSON
```

#### Archive Channel

```bash
maton slack channel archive C0123456789
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/conversations.archive' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789"
}
JSON
```

#### Unarchive Channel

```bash
maton slack channel unarchive C0123456789
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/conversations.unarchive' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789"
}
JSON
```

#### Rename Channel

```bash
maton slack channel rename C0123456789 --name new-name
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/conversations.rename' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789",
  "name": "new-name"
}
JSON
```

#### Set Channel Topic

```bash
maton slack channel set-topic C0123456789 --topic 'Channel topic here'
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/conversations.setTopic' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789",
  "topic": "Channel topic here"
}
JSON
```

#### Set Channel Purpose

```bash
maton slack channel set-purpose C0123456789 --purpose 'Channel purpose here'
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/conversations.setPurpose' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789",
  "purpose": "Channel purpose here"
}
JSON
```

#### Invite to Channel

```bash
maton slack channel invite C0123456789 --users U0123456789,U9876543210
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/conversations.invite' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789",
  "users": "U0123456789,U9876543210"
}
JSON
```

#### Kick from Channel

```bash
maton slack channel kick C0123456789 --user U0123456789
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/conversations.kick' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789",
  "user": "U0123456789"
}
JSON
```

#### Mark Channel Read

```bash
maton slack channel mark C0123456789 --ts 1234567890.123456
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/conversations.mark' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789",
  "ts": "1234567890.123456"
}
JSON
```

### Direct Messages API

#### Open DM Conversation

```bash
maton slack conversation open --users U0123456789
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/conversations.open' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "users": "U0123456789"
}
JSON
```

For group DM:

```bash
maton slack conversation open --users U0123456789,U9876543210
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/conversations.open' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "users": "U0123456789,U9876543210"
}
JSON
```

#### List DM Channels

```bash
maton slack channel list --types im
```

Or with `maton api`:

```bash
maton api '/slack/api/conversations.list?types=im'
```

#### List Group DM Channels

```bash
maton slack channel list --types mpim
```

Or with `maton api`:

```bash
maton api '/slack/api/conversations.list?types=mpim'
```

#### My Conversations

```bash
maton slack conversation list --limit 100
```

Or with `maton api`:

```bash
maton api '/slack/api/users.conversations?limit=100'
```

### Users API

#### List Users

```bash
maton slack user list --limit 100
```

Or with `maton api`:

```bash
maton api '/slack/api/users.list?limit=100'
```

#### Get User Info

```bash
maton slack user get U0123456789
```

Or with `maton api`:

```bash
maton api '/slack/api/users.info?user=U0123456789'
```

#### Get User Presence

```bash
maton slack user presence U0123456789
```

Or with `maton api`:

```bash
maton api '/slack/api/users.getPresence?user=U0123456789'
```

#### Lookup User by Email

```bash
maton slack user lookup --email user@example.com
```

Or with `maton api`:

```bash
maton api '/slack/api/users.lookupByEmail?email=user@example.com'
```

#### Set Presence

```bash
maton api -X POST '/slack/api/users.setPresence' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "presence": "away"
}
EOF
```

### Reactions API

#### Add Reaction

```bash
maton slack reaction add --channel C0123456789 --ts 1234567890.123456 --emoji thumbsup
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/reactions.add' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789",
  "name": "thumbsup",
  "timestamp": "1234567890.123456"
}
JSON
```

#### Remove Reaction

```bash
maton slack reaction remove --channel C0123456789 --ts 1234567890.123456 --emoji thumbsup
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/reactions.remove' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789",
  "name": "thumbsup",
  "timestamp": "1234567890.123456"
}
JSON
```

#### Get Reactions on Message

```bash
maton slack reaction get --channel C0123456789 --ts 1234567890.123456
```

Or with `maton api`:

```bash
maton api '/slack/api/reactions.get?channel=C0123456789&timestamp=1234567890.123456'
```

#### List My Reactions

```bash
maton slack reaction list --limit 100
```

Or with `maton api`:

```bash
maton api '/slack/api/reactions.list?limit=100'
```

### Stars API

#### List Stars

```bash
maton slack star list --limit 100
```

Or with `maton api`:

```bash
maton api '/slack/api/stars.list?limit=100'
```

#### Add Star

```bash
maton slack star add --channel C0123456789 --ts 1234567890.123456
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/stars.add' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789",
  "timestamp": "1234567890.123456"
}
JSON
```

#### Remove Star

```bash
maton slack star remove --channel C0123456789 --ts 1234567890.123456
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/stars.remove' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789",
  "timestamp": "1234567890.123456"
}
JSON
```

### Pins API

#### List Pins

```bash
maton slack pin list C0123456789
```

Or with `maton api`:

```bash
maton api '/slack/api/pins.list?channel=C0123456789'
```

#### Add Pin

```bash
maton slack pin add --channel C0123456789 --ts 1234567890.123456
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/pins.add' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789",
  "timestamp": "1234567890.123456"
}
JSON
```

#### Remove Pin

```bash
maton slack pin remove --channel C0123456789 --ts 1234567890.123456
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/pins.remove' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel": "C0123456789",
  "timestamp": "1234567890.123456"
}
JSON
```

### Bookmarks API

#### List Bookmarks

```bash
maton slack bookmark list --channel C0123456789
```

Or with `maton api`:

```bash
maton api '/slack/api/bookmarks.list?channel_id=C0123456789'
```

#### Add Bookmark

```bash
maton slack bookmark add --channel C0123456789 --title 'Team Handbook' --type link --link https://example.com/handbook
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/bookmarks.add' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "channel_id": "C0123456789",
  "title": "Team Handbook",
  "type": "link",
  "link": "https://example.com/handbook"
}
JSON
```

#### Edit Bookmark

```bash
maton slack bookmark edit --channel C0123456789 --bookmark-id Bk0123456789 --title 'Updated Title' --link https://example.com/new
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/bookmarks.edit' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "bookmark_id": "Bk0123456789",
  "channel_id": "C0123456789",
  "title": "Updated Title",
  "link": "https://example.com/new"
}
JSON
```

#### Remove Bookmark

```bash
maton slack bookmark remove --channel C0123456789 --bookmark-id Bk0123456789
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/bookmarks.remove' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "bookmark_id": "Bk0123456789",
  "channel_id": "C0123456789"
}
JSON
```

### Bots API

#### Get Bot Info

```bash
maton slack bot get B0123456789
```

Or with `maton api`:

```bash
maton api '/slack/api/bots.info?bot=B0123456789'
```

Note: this expects the `B`-prefixed bot ID (from `bot_id` on a message), not the bot's `U`-prefixed user ID. Passing a `U…` ID returns `bot_not_found`.

### Files API

#### List Files

```bash
maton api '/slack/api/files.list?count=100'
```

Filter by channel, user, or file types:

```bash
maton slack file list --count 100
```

Or with `maton api`:

```bash
maton api '/slack/api/files.list?channel=C0123456789&user=U0123456789&types=images,pdfs'
```

```bash
maton slack file list --channel C0123456789 --user U0123456789 --types images,pdfs
```

#### Upload File

```bash
# maton api sends a body verbatim but does not build a multipart envelope:
# assemble it first, then hand the file to --input.
BOUNDARY="maton-$$"
{
  printf -- '--%s\r\nContent-Disposition: form-data; name="channels"\r\n\r\nC0123456789\r\n' "$BOUNDARY"
  printf -- '--%s\r\nContent-Disposition: form-data; name="content"\r\n\r\nfile content here\r\n' "$BOUNDARY"
  printf -- '--%s\r\nContent-Disposition: form-data; name="filename"\r\n\r\nexample.txt\r\n' "$BOUNDARY"
  printf -- '--%s\r\nContent-Disposition: form-data; name="title"\r\n\r\nExample File\r\n' "$BOUNDARY"
  printf -- '--%s--\r\n' "$BOUNDARY"
} > /tmp/upload.body

maton api -X POST '/slack/api/files.upload' \
  -H "Content-Type: multipart/form-data; boundary=$BOUNDARY" \
  --input /tmp/upload.body
```

#### Upload File v2 (Get Upload URL)

```bash
maton api '/slack/api/files.getUploadURLExternal?filename=example.txt&length=1024'
```

#### Complete File Upload

```bash
maton slack file upload --file ./example.txt --channel C0123456789 --title 'My File'
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/files.completeUploadExternal' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "files": [{"id": "F0123456789", "title": "My File"}],
  "channel_id": "C0123456789"
}
JSON
```

#### Delete File

```bash
maton slack file delete F0123456789
```

Or with `maton api`:

```bash
maton api -X POST '/slack/api/files.delete' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "file": "F0123456789"
}
JSON
```

#### Get File Info

```bash
maton slack file get F0123456789
```

Or with `maton api`:

```bash
maton api '/slack/api/files.info?file=F0123456789'
```

### Search API

#### Search Messages

```bash
maton slack search messages 'keyword'
```

Or with `maton api`:

```bash
maton api '/slack/api/search.messages?query=keyword'
```

#### Search Files

```bash
maton api '/slack/api/search.files?query=keyword'
```

Note: `search.files` matches against filename and title, not file body content. Newly uploaded files may take a moment to appear in results due to indexing lag.

### Examples

```bash
# Send a message to a channel
maton slack message send --channel C0123456789 --text 'Hello team'

# List channels
maton slack channel list --types public_channel,private_channel

# Look up a user by email
maton slack user lookup --email alice@example.com

# Add a reaction to a message
maton slack reaction add --channel C012 --ts 1700000000.000100 --emoji thumbsup
```

### Notes

- Channel IDs: `C` (public), `G` (private/group), `D` (DM)
- User IDs start with `U`, Bot IDs start with `B`, Team IDs start with `T`
- Message timestamps (`ts`) are unique identifiers
- Use `mrkdwn` type for Slack-flavored markdown formatting
- Thread replies use `thread_ts` to reference the parent message
- Cursor-based pagination: use `cursor` from `response_metadata.next_cursor`

### Resources

- [Slack API Methods](https://api.slack.com/methods)
- [Slack Web API Reference](https://api.slack.com/web)
- [Block Kit Reference](https://api.slack.com/reference/block-kit)
- [Message Formatting](https://api.slack.com/reference/surfaces/formatting)
- [Rate Limits](https://api.slack.com/docs/rate-limits)
- [Maton CLI Manual](https://cli.maton.ai/manual)
