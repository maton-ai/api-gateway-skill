# Google Meet

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `google-meet`
**Upstream base URL:** `meet.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://meet.googleapis.com/v2/spaces`
- Gateway: `https://api.maton.ai/google-meet/v2/spaces`

### Spaces API

#### Create Space

```bash
maton api -X POST '/google-meet/v2/spaces' -H 'Content-Type: application/json' --input - <<'JSON'
{}
JSON
```

Response:
```json
{
  "name": "spaces/abc123",
  "meetingUri": "https://meet.google.com/abc-defg-hij",
  "meetingCode": "abc-defg-hij",
  "config": {
    "accessType": "OPEN",
    "entryPointAccess": "ALL"
  }
}
```

**Response:**

#### Get Space

```bash
maton api '/google-meet/v2/spaces/{spaceId}'
```

**Note:** `{spaceId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Space

```bash
maton api -X PATCH '/google-meet/v2/spaces/{spaceId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "config": {
    "accessType": "TRUSTED"
  }
}
JSON
```

**Note:** `{spaceId}` is a placeholder. Replace it with a real value before sending the request.

#### End Active Call

```bash
maton api -X POST '/google-meet/v2/spaces/{spaceId}:endActiveConference'
```

**Note:** `{spaceId}` is a placeholder. Replace it with a real value before sending the request.

### Conference Records API

#### List Conference Records

```bash
maton api '/google-meet/v2/conferenceRecords'
```

With filter:
```bash
maton api '/google-meet/v2/conferenceRecords?filter=space.name="spaces/abc123"'
```

#### Get Conference Record

```bash
maton api '/google-meet/v2/conferenceRecords/{conferenceRecordId}'
```

**Note:** `{conferenceRecordId}` is a placeholder. Replace it with a real value before sending the request.

### Participants API

#### List Participants

```bash
maton api '/google-meet/v2/conferenceRecords/{conferenceRecordId}/participants'
```

**Note:** `{conferenceRecordId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Participant

```bash
maton api '/google-meet/v2/conferenceRecords/{conferenceRecordId}/participants/{participantId}'
```

**Note:** `{conferenceRecordId}` and `{participantId}` are placeholders. Replace each of them with real values before sending the request.

### Participant Sessions API

#### List Participant Sessions

```bash
maton api '/google-meet/v2/conferenceRecords/{conferenceRecordId}/participants/{participantId}/participantSessions'
```

**Note:** `{conferenceRecordId}` and `{participantId}` are placeholders. Replace each of them with real values before sending the request.

### Recordings API

#### List Recordings

```bash
maton api '/google-meet/v2/conferenceRecords/{conferenceRecordId}/recordings'
```

**Note:** `{conferenceRecordId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Recording

```bash
maton api '/google-meet/v2/conferenceRecords/{conferenceRecordId}/recordings/{recordingId}'
```

**Note:** `{conferenceRecordId}` and `{recordingId}` are placeholders. Replace each of them with real values before sending the request.

### Transcripts API

#### List Transcripts

```bash
maton api '/google-meet/v2/conferenceRecords/{conferenceRecordId}/transcripts'
```

**Note:** `{conferenceRecordId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Transcript

```bash
maton api '/google-meet/v2/conferenceRecords/{conferenceRecordId}/transcripts/{transcriptId}'
```

**Note:** `{conferenceRecordId}` and `{transcriptId}` are placeholders. Replace each of them with real values before sending the request.

#### List Transcript Entries

```bash
maton api '/google-meet/v2/conferenceRecords/{conferenceRecordId}/transcripts/{transcriptId}/entries'
```

**Note:** `{conferenceRecordId}` and `{transcriptId}` are placeholders. Replace each of them with real values before sending the request.

### Notes

- Spaces are persistent meeting rooms that can be reused
- Conference records are created when a meeting starts and track meeting history
- Access types: `OPEN` (anyone with link), `TRUSTED` (organization members only), `RESTRICTED` (invited only)
- Recordings and transcripts require Google Workspace with recording enabled

### Resources

- [Google Meet API Overview](https://developers.google.com/meet/api/reference/rest)
- [Spaces](https://developers.google.com/meet/api/reference/rest/v2/spaces)
- [Conference Records](https://developers.google.com/meet/api/reference/rest/v2/conferenceRecords)
- [Participants](https://developers.google.com/meet/api/reference/rest/v2/conferenceRecords.participants)
- [Recordings](https://developers.google.com/meet/api/reference/rest/v2/conferenceRecords.recordings)
- [Transcripts](https://developers.google.com/meet/api/reference/rest/v2/conferenceRecords.transcripts)
- [Maton CLI Manual](https://cli.maton.ai/manual)
