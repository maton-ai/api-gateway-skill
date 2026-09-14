# Microsoft Teams

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `microsoft-teams`
**Upstream base URL:** `graph.microsoft.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://graph.microsoft.com/v1.0/me/joinedTeams`
- Gateway: `https://api.maton.ai/microsoft-teams/v1.0/me/joinedTeams`

### Teams API

#### List Joined Teams

```bash
maton microsoft-teams team list
```

Or with `maton api`:

```bash
maton api '/microsoft-teams/v1.0/me/joinedTeams'
```

**Response:**
```json
{
  "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#teams",
  "@odata.count": 1,
  "value": [
    {
      "id": "b643f103-870d-4f98-a23d-e6f164fae33e",
      "displayName": "carvedai.com",
      "description": null,
      "isArchived": false,
      "tenantId": "cb83c3f9-6d16-4cf3-bd8c-ab16b37932f9"
    }
  ]
}
```

#### Get Team

```bash
maton microsoft-teams team get {team-id}
```

Or with `maton api`:

```bash
maton api '/microsoft-teams/v1.0/teams/{team-id}'
```

**Note:** `{team-id}` is a placeholder. Replace it with a real value before sending the request.

### Channels API

#### List Channels

```bash
maton microsoft-teams channel list --team {team-id}
```

Or with `maton api`:

```bash
maton api '/microsoft-teams/v1.0/teams/{team-id}/channels'
```

**Note:** `{team-id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#teams('...')/channels",
  "@odata.count": 1,
  "value": [
    {
      "id": "19:9fwtZjo3IM0D8bLdQqR-_oMFw1eUDlzWjPfIhNGhVd41@thread.tacv2",
      "createdDateTime": "2026-02-16T20:09:27.254Z",
      "displayName": "General",
      "description": null,
      "email": "carvedai.com473@carvedai.com",
      "membershipType": "standard",
      "isArchived": false
    }
  ]
}
```

#### List Private Channels

```bash
maton microsoft-teams channel list --team {team-id} --filter "membershipType eq 'private'"
```

Or with `maton api`:

```bash
maton api "/microsoft-teams/v1.0/teams/{team-id}/channels?\$filter=membershipType%20eq%20'private'"
```

**Note:** `{team-id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Channel

```bash
maton microsoft-teams channel get {channel-id} --team {team-id}
```

Or with `maton api`:

```bash
maton api '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}'
```

**Note:** `{team-id}` and `{channel-id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Channel

```bash
maton api -X POST '/microsoft-teams/v1.0/teams/{team-id}/channels' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "displayName": "New Channel",
  "description": "Channel description",
  "membershipType": "standard"
}
JSON
```

**Note:** `{team-id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": "19:3b3361df822044558a062bb1a4ac8357@thread.tacv2",
  "createdDateTime": "2026-02-17T20:24:33.9284462Z",
  "displayName": "Maton Test Channel",
  "description": "Channel created by Maton integration test",
  "membershipType": "standard",
  "isArchived": false
}
```

#### Update Channel

```bash
maton api -X PATCH '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "description": "Updated description"
}
JSON
```

**Note:** `{team-id}` and `{channel-id}` are placeholders. Replace each of them with real values before sending the request.

**Note:** Returns `204 No Content` on success. The default "General" channel cannot be updated.

#### Delete Channel

```bash
maton api '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}' -X DELETE
```

**Note:** `{team-id}` and `{channel-id}` are placeholders. Replace each of them with real values before sending the request.

**Note:** Returns `204 No Content` on success.

### Channel Members API

#### List Channel Members

```bash
maton api '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}/members'
```

**Note:** `{team-id}` and `{channel-id}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "@odata.count": 1,
  "value": [
    {
      "@odata.type": "#microsoft.graph.aadUserConversationMember",
      "id": "MCMjMiMj...",
      "roles": ["owner"],
      "displayName": "Kevin Kim",
      "userId": "5f56d55b-2ffb-448d-982a-b52547431f71",
      "email": "richard@carvedai.com"
    }
  ]
}
```

### Messages API

#### List Channel Messages

```bash
maton microsoft-teams message list --team {team-id} --channel {channel-id}
```

Or with `maton api`:

```bash
maton api '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}/messages'
```

**Note:** `{team-id}` and `{channel-id}` are placeholders. Replace each of them with real values before sending the request.

#### Send Message

```bash
maton microsoft-teams message send --team {team-id} --channel {channel-id} --text 'Hello World'
```

Or with `maton api`:

```bash
maton api -X POST '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "body": {
    "content": "Hello World"
  }
}
JSON
```

**Note:** `{team-id}` and `{channel-id}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "id": "1771359569239",
  "replyToId": null,
  "messageType": "message",
  "createdDateTime": "2026-02-17T20:19:29.239Z",
  "importance": "normal",
  "locale": "en-us",
  "from": {
    "user": {
      "id": "5f56d55b-2ffb-448d-982a-b52547431f71",
      "displayName": "Kevin Kim",
      "userIdentityType": "aadUser",
      "tenantId": "cb83c3f9-6d16-4cf3-bd8c-ab16b37932f9"
    }
  },
  "body": {
    "contentType": "text",
    "content": "Hello World"
  },
  "channelIdentity": {
    "teamId": "b643f103-870d-4f98-a23d-e6f164fae33e",
    "channelId": "19:9fwtZjo3IM0D8bLdQqR-_oMFw1eUDlzWjPfIhNGhVd41@thread.tacv2"
  }
}
```

#### Send HTML Message

```bash
maton microsoft-teams message send --team {team-id} --channel {channel-id} --html --text '<h1>Hello</h1><p>This is <strong>formatted</strong> content.</p>'
```

Or with `maton api`:

```bash
maton api -X POST '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "body": {
    "contentType": "html",
    "content": "<h1>Hello</h1><p>This is <strong>formatted</strong> content.</p>"
  }
}
JSON
```

**Note:** `{team-id}` and `{channel-id}` are placeholders. Replace each of them with real values before sending the request.

#### Reply to Message

```bash
maton microsoft-teams message reply {message-id} --team {team-id} --channel {channel-id} --text 'This is a reply'
```

Or with `maton api`:

```bash
maton api -X POST '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}/messages/{message-id}/replies' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "body": {
    "content": "This is a reply"
  }
}
JSON
```

**Note:** `{team-id}`, `{channel-id}` and `{message-id}` are placeholders. Replace each of them with real values before sending the request.

#### List Message Replies

```bash
maton api '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}/messages/{message-id}/replies'
```

**Note:** `{team-id}`, `{channel-id}` and `{message-id}` are placeholders. Replace each of them with real values before sending the request.

#### Edit Message

```bash
maton api -X PATCH '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}/messages/{message-id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "body": {
    "content": "Updated message content"
  }
}
JSON
```

**Note:** `{team-id}`, `{channel-id}` and `{message-id}` are placeholders. Replace each of them with real values before sending the request.

**Note:** Returns `204 No Content` on success.

### Team Members API

#### List Members

```bash
maton microsoft-teams team members {team-id}
```

Or with `maton api`:

```bash
maton api '/microsoft-teams/v1.0/teams/{team-id}/members'
```

**Note:** `{team-id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#teams('...')/members",
  "@odata.count": 1,
  "value": [
    {
      "@odata.type": "#microsoft.graph.aadUserConversationMember",
      "id": "MCMjMSMj...",
      "roles": ["owner"],
      "displayName": "Kevin Kim",
      "userId": "5f56d55b-2ffb-448d-982a-b52547431f71",
      "email": "richard@carvedai.com",
      "tenantId": "cb83c3f9-6d16-4cf3-bd8c-ab16b37932f9"
    }
  ]
}
```

### Presence API

#### Get User Presence

```bash
maton microsoft-teams presence get
```

Or with `maton api`:

```bash
maton api '/microsoft-teams/v1.0/me/presence'
```

**Response:**
```json
{
  "id": "5f56d55b-2ffb-448d-982a-b52547431f71",
  "availability": "Offline",
  "activity": "Offline",
  "outOfOfficeSettings": {
    "message": null,
    "isOutOfOffice": false
  }
}
```

**Note:** Availability values are `Available`, `Busy`, `DoNotDisturb`, `Away`, `Offline`.

#### Get User Presence by ID

```bash
maton microsoft-teams presence get --user {user-id}
```

Or with `maton api`:

```bash
maton api '/microsoft-teams/v1.0/users/{user-id}/presence'
```

**Note:** `{user-id}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Returns presence information for a specific user by their ID.

### Tabs API

#### List Channel Tabs

```bash
maton api '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}/tabs'
```

**Note:** `{team-id}` and `{channel-id}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "@odata.count": 2,
  "value": [
    {
      "id": "ee0b3e8b-dfc8-4945-a45d-28ceaf787d92",
      "displayName": "Notes",
      "webUrl": "https://teams.microsoft.com/l/entity/..."
    },
    {
      "id": "3ed5b337-c2c9-4d5d-b7b4-84ff09a8fc1c",
      "displayName": "Files",
      "webUrl": "https://teams.microsoft.com/l/entity/..."
    }
  ]
}
```

### Apps API

#### List Installed Apps

```bash
maton api '/microsoft-teams/v1.0/teams/{team-id}/installedApps'
```

**Note:** `{team-id}` is a placeholder. Replace it with a real value before sending the request.

### Online Meetings API

#### Create Meeting

```bash
maton microsoft-teams meeting create --subject 'Team Sync' --start 2026-02-18T10:00:00Z --end 2026-02-18T11:00:00Z
```

Or with `maton api`:

```bash
maton api -X POST '/microsoft-teams/v1.0/me/onlineMeetings' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "subject": "Team Sync",
  "startDateTime": "2026-02-18T10:00:00Z",
  "endDateTime": "2026-02-18T11:00:00Z"
}
JSON
```

**Response:**
```json
{
  "id": "MSo1ZjU2ZDU1Yi0yZmZi...",
  "subject": "Team Sync",
  "startDateTime": "2026-02-18T10:00:00Z",
  "endDateTime": "2026-02-18T11:00:00Z",
  "joinUrl": "https://teams.microsoft.com/l/meetup-join/...",
  "joinWebUrl": "https://teams.microsoft.com/l/meetup-join/...",
  "meetingCode": "28636743235745",
  "joinMeetingIdSettings": {
    "joinMeetingId": "28636743235745",
    "passcode": "qh37NK9V",
    "isPasscodeRequired": true
  },
  "participants": {
    "organizer": {
      "upn": "richard@carvedai.com",
      "role": "presenter"
    }
  }
}
```

**Note:** The `joinUrl` can be shared with attendees to join the meeting.

#### Get Meeting

```bash
maton api '/microsoft-teams/v1.0/me/onlineMeetings/{meeting-id}'
```

**Note:** `{meeting-id}` is a placeholder. Replace it with a real value before sending the request.

#### Find Meeting by Join URL

```bash
maton api '/microsoft-teams/v1.0/me/onlineMeetings?$filter=JoinWebUrl%20eq%20%27{encoded-join-url}%27'
```

**Note:** `{encoded-join-url}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Microsoft Graph requires a filter to query meetings. You cannot list all meetings without filtering by `JoinWebUrl`.

#### List Calendar Events

```bash
maton api '/microsoft-teams/v1.0/me/calendar/events?$top=10'
```

**Note:** Scheduled Teams meetings appear as calendar events with `isOnlineMeeting: true`.

#### Delete Meeting

```bash
maton api '/microsoft-teams/v1.0/me/onlineMeetings/{meeting-id}' -X DELETE
```

**Note:** `{meeting-id}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Returns `204 No Content` on success.

#### Create Meeting with Attendees

```bash
maton api -X POST '/microsoft-teams/v1.0/me/onlineMeetings' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "subject": "Project Review",
  "startDateTime": "2026-02-18T14:00:00Z",
  "endDateTime": "2026-02-18T15:00:00Z",
  "participants": {
    "attendees": [
      {
        "upn": "attendee@example.com",
        "role": "attendee"
      }
    ]
  }
}
JSON
```

#### List Meeting Recordings

```bash
maton api '/microsoft-teams/v1.0/me/onlineMeetings/{meeting-id}/recordings'
```

**Note:** `{meeting-id}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Returns a list of recordings for a meeting (available after the meeting has ended and recording was enabled).

#### Get Meeting Recording

```bash
maton api '/microsoft-teams/v1.0/me/onlineMeetings/{meeting-id}/recordings/{recording-id}'
```

**Note:** `{meeting-id}` and `{recording-id}` are placeholders. Replace each of them with real values before sending the request.

#### List Meeting Transcripts

```bash
maton api '/microsoft-teams/v1.0/me/onlineMeetings/{meeting-id}/transcripts'
```

**Note:** `{meeting-id}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Returns a list of transcripts for a meeting (available after the meeting has ended and transcription was enabled).

#### Get Meeting Transcript

```bash
maton api '/microsoft-teams/v1.0/me/onlineMeetings/{meeting-id}/transcripts/{transcript-id}'
```

**Note:** `{meeting-id}` and `{transcript-id}` are placeholders. Replace each of them with real values before sending the request.

#### List Attendance Reports

```bash
maton api '/microsoft-teams/v1.0/me/onlineMeetings/{meeting-id}/attendanceReports'
```

**Note:** `{meeting-id}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Returns attendance reports for a meeting (available after the meeting has ended).

#### Get Attendance Report

```bash
maton api '/microsoft-teams/v1.0/me/onlineMeetings/{meeting-id}/attendanceReports/{report-id}'
```

**Note:** `{meeting-id}` and `{report-id}` are placeholders. Replace each of them with real values before sending the request.

### Chats API

#### List Chats

```bash
maton microsoft-teams chat list
```

Or with `maton api`:

```bash
maton api '/microsoft-teams/v1.0/me/chats'
```

#### Get Chat

```bash
maton microsoft-teams chat get {chat-id}
```

Or with `maton api`:

```bash
maton api '/microsoft-teams/v1.0/chats/{chat-id}'
```

**Note:** `{chat-id}` is a placeholder. Replace it with a real value before sending the request.

#### List Chat Messages

```bash
maton microsoft-teams message list --chat {chat-id}
```

Or with `maton api`:

```bash
maton api '/microsoft-teams/v1.0/chats/{chat-id}/messages'
```

**Note:** `{chat-id}` is a placeholder. Replace it with a real value before sending the request.

#### Send Chat Message

```bash
maton microsoft-teams message send --chat {chat-id} --text 'Hello'
```

Or with `maton api`:

```bash
maton api -X POST '/microsoft-teams/v1.0/chats/{chat-id}/messages' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "body": {
    "content": "Hello"
  }
}
JSON
```

**Note:** `{chat-id}` is a placeholder. Replace it with a real value before sending the request.

### OData Query parameters

- `$top=10` - Limit results
- `$skip=20` - Skip results
- `$select=id,displayName` - Select specific fields
- `$filter=membershipType eq 'private'` - Filter results
- `$orderby=displayName` - Sort results

### Notes

- Uses Microsoft Graph API (`graph.microsoft.com`)
- Channel IDs include thread suffix: `19:xxx@thread.tacv2`
- Message body content types: `text` or `html`
- Channel membership types: `standard`, `private`, `shared`
- Supports OData query parameters for filtering and pagination
- Meeting recordings/transcripts available after meeting ends

### Resources

- [Microsoft Teams API Overview](https://learn.microsoft.com/en-us/graph/api/resources/teams-api-overview)
- [Microsoft Graph API Reference](https://learn.microsoft.com/en-us/graph/api/overview)
- [Channel Resource](https://learn.microsoft.com/en-us/graph/api/resources/channel)
- [ChatMessage Resource](https://learn.microsoft.com/en-us/graph/api/resources/chatmessage)
- [Maton CLI Manual](https://cli.maton.ai/manual)
