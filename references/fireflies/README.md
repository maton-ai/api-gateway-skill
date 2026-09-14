# Fireflies

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `fireflies`
**Upstream base URL:** `api.fireflies.ai`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.fireflies.ai/graphql`
- Gateway: `https://api.maton.ai/fireflies/graphql`

### Users API

#### Get User

```bash
maton api -X POST '/fireflies/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ user { user_id name email is_admin num_transcripts minutes_consumed recent_transcript recent_meeting } }"}
JSON
```

**Response:**
```json
{
  "data": {
    "user": {
      "user_id": "01KH5131Z0W4TS7BBSEP66CV6V",
      "name": "John Doe",
      "email": "john@example.com",
      "is_admin": true,
      "num_transcripts": null,
      "minutes_consumed": 0
    }
  }
}
```

#### List Users

```bash
maton api -X POST '/fireflies/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ users { user_id name email is_admin } }"}
JSON
```

### Transcripts API

#### List Transcripts

```bash
maton api -X POST '/fireflies/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ transcripts { id title date duration host_email privacy } }"}
JSON
```

#### Get Transcript

```bash
maton api -X POST '/fireflies/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "query($id: String!) { transcript(id: $id) { id title date duration summary { overview action_items } sentences { text speaker_name } } }",
  "variables": {"id": "{transcriptId}"}
}
JSON
```

**Note:** `{transcriptId}` is a placeholder. Replace it with a real value before sending the request.

#### Upload Audio

```bash
maton api -X POST '/fireflies/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "mutation($input: AudioUploadInput!) { uploadAudio(input: $input) { success title message } }",
  "variables": {"input": {"url": "{audioUrl}", "title": "{title}"}}
}
JSON
```

**Note:** `{audioUrl}` and `{title}` are placeholders. Replace each of them with real values before sending the request.

**Input:**
- `url` (required): Publicly accessible HTTPS URL of the media file (mp3, mp4, wav, m4a, ogg)
- `title`: Identifier for the transcribed file
- `webhook`: Endpoint notified when transcription completes
- `custom_language`: Language code (e.g. `es`, `de`)
- `save_video`: Retain the video file
- `attendees`: List of `{displayName, email, phoneNumber}`
- `client_reference_id`: Custom identifier echoed in webhook events

#### Delete Transcript

```bash
maton api -X POST '/fireflies/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "mutation($id: String!) { deleteTranscript(id: $id) { id title date duration host_email } }",
  "variables": {"id": "{transcriptId}"}
}
JSON
```

**Note:** `{transcriptId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Meeting Title

```bash
maton api -X POST '/fireflies/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "mutation($input: UpdateMeetingTitleInput!) { updateMeetingTitle(input: $input) { title } }",
  "variables": {"input": {"id": "{transcriptId}", "title": "{title}"}}
}
JSON
```

**Note:** `{transcriptId}` and `{title}` are placeholders. Replace each of them with real values before sending the request.

**Input:**
- `id` (required): Transcript ID
- `title` (required): New meeting title

Requires admin privileges.

### Channels API

#### List Channels

```bash
maton api -X POST '/fireflies/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ channels { id title created_at is_private } }"}
JSON
```

### Contacts API

#### List Contacts

```bash
maton api -X POST '/fireflies/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ contacts { email name picture last_meeting_date } }"}
JSON
```

### Bites API

#### List Bites

At least one of `mine`, `transcript_id`, or `my_team` is required. Optional: `limit` (max 50) and `skip`.

```bash
maton api -X POST '/fireflies/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ bites(mine: true) { id name transcript_id summary status } }"}
JSON
```

### AskFred API

#### List AskFred Threads

```bash
maton api -X POST '/fireflies/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ askfred_threads { id title transcript_id created_at } }"}
JSON
```

#### Create AskFred Thread

```bash
maton api -X POST '/fireflies/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "mutation($input: CreateAskFredThreadInput!) { createAskFredThread(input: $input) { message { id thread_id answer suggested_queries } } }",
  "variables": {"input": {"query": "What were the action items?", "transcript_id": "{transcriptId}"}}
}
JSON
```

**Note:** `{transcriptId}` is a placeholder. Replace it with a real value before sending the request.

**Input:**
- `query` (required): Natural language question
- `transcript_id`: Scope the question to one meeting
- `filters`: Cross-meeting scope — `{start_time, end_time, participants}` — used instead of `transcript_id`
- `response_language`: e.g. `en`
- `format_mode`: e.g. `markdown`

#### Continue AskFred Thread

```bash
maton api -X POST '/fireflies/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "mutation($input: ContinueAskFredThreadInput!) { continueAskFredThread(input: $input) { message { answer suggested_queries } } }",
  "variables": {"input": {"thread_id": "{threadId}", "query": "Who owns the first one?"}}
}
JSON
```

**Note:** `{threadId}` is a placeholder. Replace it with a real value before sending the request.

### Notes

- All requests are POST with Content-Type: application/json
- Request body: `{ "query": "...", "variables": {...} }`
- User IDs are ULIDs
- Rate limits: 50 calls/day (free), more on Business plan
- Summary field contains AI-generated content: `overview`, `action_items`, `outline`, `keywords`
- Timestamps are Unix timestamps (milliseconds)
- AskFred answers natural language questions across meeting transcripts

### Resources

- [Fireflies API Documentation](https://docs.fireflies.ai/)
- [Fireflies GraphQL API](https://docs.fireflies.ai/graphql-api)
- [Maton CLI Manual](https://cli.maton.ai/manual)
