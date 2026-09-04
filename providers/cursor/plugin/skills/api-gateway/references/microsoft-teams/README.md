# Microsoft Teams Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `microsoft-teams`
**Base URL proxied:** `graph.microsoft.com`

## API Path Pattern

```
/microsoft-teams/v1.0/{resource}
```

## Common Endpoints

### Teams

#### List Joined Teams
```bash
maton api '/microsoft-teams/v1.0/me/joinedTeams'
```

#### Get Team
```bash
maton api '/microsoft-teams/v1.0/teams/{team-id}'
```

### Channels

#### List Channels
```bash
maton api '/microsoft-teams/v1.0/teams/{team-id}/channels'
```

#### List Private Channels
```bash
maton api "/microsoft-teams/v1.0/teams/{team-id}/channels?\$filter=membershipType%20eq%20'private'"
```

#### Get Channel
```bash
maton api '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}'
```

#### Create Channel
```bash
maton api -X POST '/microsoft-teams/v1.0/teams/{team-id}/channels' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "displayName": "Channel Name",
  "description": "Description",
  "membershipType": "standard"
}
EOF
```

#### Update Channel
```bash
maton api -X PATCH '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "description": "Updated description"
}
EOF
```

#### Delete Channel
```bash
maton api -X DELETE '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}'
```

### Channel Members

#### List Channel Members
```bash
maton api '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}/members'
```

### Messages

#### List Channel Messages
```bash
maton api '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}/messages'
```

#### Send Message
```bash
maton api -X POST '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}/messages' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "body": {
    "content": "Hello World"
  }
}
EOF
```

#### Send HTML Message
```bash
maton api -X POST '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}/messages' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "body": {
    "contentType": "html",
    "content": "<p>Formatted message</p>"
  }
}
EOF
```

#### Reply to Message
```bash
maton api -X POST '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}/messages/{message-id}/replies' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "body": {
    "content": "Reply content"
  }
}
EOF
```

#### List Message Replies
```bash
maton api '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}/messages/{message-id}/replies'
```

#### Edit Message
```bash
maton api -X PATCH '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}/messages/{message-id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "body": {
    "content": "Updated message content"
  }
}
EOF
```

### Team Members

#### List Members
```bash
maton api '/microsoft-teams/v1.0/teams/{team-id}/members'
```

### Presence

#### Get User Presence
```bash
maton api '/microsoft-teams/v1.0/me/presence'
```

#### Get User Presence by ID
```bash
maton api '/microsoft-teams/v1.0/users/{user-id}/presence'
```

### Tabs

#### List Channel Tabs
```bash
maton api '/microsoft-teams/v1.0/teams/{team-id}/channels/{channel-id}/tabs'
```

### Apps

#### List Installed Apps
```bash
maton api '/microsoft-teams/v1.0/teams/{team-id}/installedApps'
```

### Online Meetings

#### Create Meeting
```bash
maton api -X POST '/microsoft-teams/v1.0/me/onlineMeetings' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "subject": "Team Sync",
  "startDateTime": "2026-02-18T10:00:00Z",
  "endDateTime": "2026-02-18T11:00:00Z"
}
EOF
```

#### Create Meeting with Attendees
```bash
maton api -X POST '/microsoft-teams/v1.0/me/onlineMeetings' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
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
EOF
```

#### Get Meeting
```bash
maton api '/microsoft-teams/v1.0/me/onlineMeetings/{meeting-id}'
```

#### Find Meeting by Join URL
```bash
maton api "/microsoft-teams/v1.0/me/onlineMeetings?\$filter=JoinWebUrl%20eq%20'{encoded-join-url}'"
```

#### Delete Meeting
```bash
maton api -X DELETE '/microsoft-teams/v1.0/me/onlineMeetings/{meeting-id}'
```

#### List Calendar Events
```bash
maton api '/microsoft-teams/v1.0/me/calendar/events?$top=10'
```

#### List Meeting Recordings
```bash
maton api '/microsoft-teams/v1.0/me/onlineMeetings/{meeting-id}/recordings'
```

#### Get Meeting Recording
```bash
maton api '/microsoft-teams/v1.0/me/onlineMeetings/{meeting-id}/recordings/{recording-id}'
```

#### List Meeting Transcripts
```bash
maton api '/microsoft-teams/v1.0/me/onlineMeetings/{meeting-id}/transcripts'
```

#### Get Meeting Transcript
```bash
maton api '/microsoft-teams/v1.0/me/onlineMeetings/{meeting-id}/transcripts/{transcript-id}'
```

#### List Attendance Reports
```bash
maton api '/microsoft-teams/v1.0/me/onlineMeetings/{meeting-id}/attendanceReports'
```

#### Get Attendance Report
```bash
maton api '/microsoft-teams/v1.0/me/onlineMeetings/{meeting-id}/attendanceReports/{report-id}'
```

### Chats

#### List Chats
```bash
maton api '/microsoft-teams/v1.0/me/chats'
```

#### Get Chat
```bash
maton api '/microsoft-teams/v1.0/chats/{chat-id}'
```

#### List Chat Messages
```bash
maton api '/microsoft-teams/v1.0/chats/{chat-id}/messages'
```

#### Send Chat Message
```bash
maton api -X POST '/microsoft-teams/v1.0/chats/{chat-id}/messages' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "body": {
    "content": "Hello"
  }
}
EOF
```

## OData Query Parameters

- `$top=10` - Limit results
- `$skip=20` - Skip results
- `$select=id,displayName` - Select specific fields
- `$filter=membershipType eq 'private'` - Filter results
- `$orderby=displayName` - Sort results

## Notes

- Uses Microsoft Graph API (`graph.microsoft.com`)
- Channel IDs include thread suffix: `19:xxx@thread.tacv2`
- Message body content types: `text` or `html`
- Channel membership types: `standard`, `private`, `shared`
- Supports OData query parameters for filtering and pagination
- Meeting recordings/transcripts available after meeting ends

## Resources

- [Microsoft Teams API Overview](https://learn.microsoft.com/en-us/graph/api/resources/teams-api-overview)
- [Microsoft Graph API Reference](https://learn.microsoft.com/en-us/graph/api/overview)
- [Channel Resource](https://learn.microsoft.com/en-us/graph/api/resources/channel)
- [ChatMessage Resource](https://learn.microsoft.com/en-us/graph/api/resources/chatmessage)
