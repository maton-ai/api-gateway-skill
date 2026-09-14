# ElevenLabs

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

> **⚠ Voice recordings are biometric data.** This app handles audio of real people speaking. A voice sample is a biometric identifier under GDPR Art. 9, BIPA, and similar laws — a stricter category than ordinary personal data — and the recordings themselves are usually private conversations: meetings, calls, voice notes, interviews, therapy or medical discussions.
>
> - **Voice cloning needs the speaker's consent, not just the user's.** Never create or fine-tune a voice from a recording unless the user confirms the person consented to a clone of their voice. A cloned voice can be used to impersonate them, including to defeat voice authentication and to fabricate statements they never made. Never clone a public figure or a voice taken from media the user does not own.
> - **Never upload audio the user did not name.** Speech-to-text and voice creation read a local file or URL and transmit it to ElevenLabs. Take the path from the user verbatim; do not search directories for audio, and do not upload a recording captured for some other purpose.
> - **Transcripts inherit the sensitivity of the conversation.** A meeting recording routinely contains third parties who never agreed to transcription, plus credentials, financials, and health details spoken aloud. Return the narrowest answer the task needs rather than printing whole transcripts, and do not forward them to another app or a trigger destination without explicit approval for that transfer.
> - **Generated speech is attributable to a person.** Confirm the exact text before synthesizing with a cloned or custom voice; the output sounds like a real human saying it.
> - Audio and voices persist in the user's ElevenLabs account until deleted, and generation consumes paid credits.

**App name:** `elevenlabs`
**Upstream base URL:** `api.elevenlabs.io`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.elevenlabs.io/v1/voices`
- Gateway: `https://api.maton.ai/elevenlabs/v1/voices`

### Text-to-Speech API

#### Convert Text to Speech

```bash
maton api -X POST '/elevenlabs/v1/text-to-speech/{voice_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "text": "Hello, this is a test of the ElevenLabs API.",
  "model_id": "eleven_multilingual_v2",
  "voice_settings": {
    "stability": 0.5,
    "similarity_boost": 0.75
  }
}
JSON
```

**Note:** `{voice_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `output_format` - Audio format (e.g., `mp3_44100_128`, `pcm_16000`, `pcm_22050`)

Returns audio data (mp3 by default).

#### Stream Text to Speech

```bash
maton api -X POST '/elevenlabs/v1/text-to-speech/{voice_id}/stream' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "text": "Hello, this is streamed audio.",
  "model_id": "eleven_multilingual_v2"
}
JSON
```

**Note:** `{voice_id}` is a placeholder. Replace it with a real value before sending the request.

Returns streaming audio data.

#### Text to Speech with Timestamps

```bash
maton api -X POST '/elevenlabs/v1/text-to-speech/{voice_id}/with-timestamps' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "text": "Hello world",
  "model_id": "eleven_multilingual_v2"
}
JSON
```

**Note:** `{voice_id}` is a placeholder. Replace it with a real value before sending the request.

Returns audio with word-level timestamps.

### Voices API

#### List Voices

```bash
maton api '/elevenlabs/v1/voices'
```

Returns all available voices including premade and cloned voices.

#### Get Voice

```bash
maton api '/elevenlabs/v1/voices/{voice_id}'
```

**Note:** `{voice_id}` is a placeholder. Replace it with a real value before sending the request.

Returns metadata about a specific voice.

#### Get Default Voice Settings

```bash
maton api '/elevenlabs/v1/voices/settings/default'
```

#### Get Voice Settings

```bash
maton api '/elevenlabs/v1/voices/{voice_id}/settings'
```

**Note:** `{voice_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Voice Clone

```bash
# maton api sends a body verbatim but does not build a multipart envelope:
# assemble it first, then hand the file to --input.
BOUNDARY="maton-$$"
{
  printf -- '--%s\r\nContent-Disposition: form-data; name="name"\r\n\r\nMy Cloned Voice\r\n' "$BOUNDARY"
  printf -- '--%s\r\nContent-Disposition: form-data; name="files"; filename="audio_sample.mp3"\r\nContent-Type: application/octet-stream\r\n\r\n' "$BOUNDARY"
  cat audio_sample.mp3
  printf -- '\r\n'
  printf -- '--%s\r\nContent-Disposition: form-data; name="description"\r\n\r\nA custom voice clone\r\n' "$BOUNDARY"
  printf -- '--%s\r\nContent-Disposition: form-data; name="remove_background_noise"\r\n\r\nfalse\r\n' "$BOUNDARY"
  printf -- '--%s--\r\n' "$BOUNDARY"
} > /tmp/upload.body

maton api -X POST '/elevenlabs/v1/voices/add' \
  -H "Content-Type: multipart/form-data; boundary=$BOUNDARY" \
  --input /tmp/upload.body
```

#### Edit Voice

```bash
# maton api sends a body verbatim but does not build a multipart envelope:
# assemble it first, then hand the file to --input.
BOUNDARY="maton-$$"
{
  printf -- '--%s\r\nContent-Disposition: form-data; name="name"\r\n\r\nUpdated Voice Name\r\n' "$BOUNDARY"
  printf -- '--%s\r\nContent-Disposition: form-data; name="description"\r\n\r\nUpdated description\r\n' "$BOUNDARY"
  printf -- '--%s--\r\n' "$BOUNDARY"
} > /tmp/upload.body

maton api -X PATCH '/elevenlabs/v1/voices/{voice_id}/edit' \
  -H "Content-Type: multipart/form-data; boundary=$BOUNDARY" \
  --input /tmp/upload.body
```

**Note:** `{voice_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Voice

```bash
maton api '/elevenlabs/v1/voices/{voice_id}' -X DELETE
```

**Note:** `{voice_id}` is a placeholder. Replace it with a real value before sending the request.

### Models API

#### List Models

```bash
maton api '/elevenlabs/v1/models'
```

Returns available models:
- `eleven_multilingual_v2` - Latest multilingual model
- `eleven_turbo_v2_5` - Low-latency model
- `eleven_monolingual_v1` - Legacy English model (deprecated)

### User API

#### Get User Info

```bash
maton api '/elevenlabs/v1/user'
```

#### Get Subscription Info

```bash
maton api '/elevenlabs/v1/user/subscription'
```

Returns subscription details including character limits and usage.

### History API

#### List History

```bash
maton api '/elevenlabs/v1/history?page_size=100'
```

**Query parameters:**
- `page_size` - Number of items per page (default: 100, max: 1000)
- `start_after_history_item_id` - Cursor for pagination
- `voice_id` - Filter by voice

#### Get History Item

```bash
maton api '/elevenlabs/v1/history/{history_item_id}'
```

**Note:** `{history_item_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Audio from History

```bash
maton api '/elevenlabs/v1/history/{history_item_id}/audio'
```

**Note:** `{history_item_id}` is a placeholder. Replace it with a real value before sending the request.

Returns the audio file for a history item.

#### Delete History Item

```bash
maton api '/elevenlabs/v1/history/{history_item_id}' -X DELETE
```

**Note:** `{history_item_id}` is a placeholder. Replace it with a real value before sending the request.

#### Download History Items

```bash
maton api -X POST '/elevenlabs/v1/history/download' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "history_item_ids": ["id1", "id2", "id3"]
}
JSON
```

Returns a zip file with the requested audio files.

### Sound Effects API

#### Generate Sound Effect

```bash
maton api -X POST '/elevenlabs/v1/sound-generation' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "text": "A thunderstorm with heavy rain and distant thunder",
  "duration_seconds": 10.0
}
JSON
```

**Query parameters:**
- `output_format` - Audio format (e.g., `mp3_44100_128`)

### Audio Isolation API

#### Remove Background Noise

```bash
# maton api sends a body verbatim but does not build a multipart envelope:
# assemble it first, then hand the file to --input.
BOUNDARY="maton-$$"
{
  printf -- '--%s\r\nContent-Disposition: form-data; name="audio"; filename="audio_file.mp3"\r\nContent-Type: application/octet-stream\r\n\r\n' "$BOUNDARY"
  cat audio_file.mp3
  printf -- '\r\n'
  printf -- '--%s--\r\n' "$BOUNDARY"
} > /tmp/upload.body

maton api -X POST '/elevenlabs/v1/audio-isolation' \
  -H "Content-Type: multipart/form-data; boundary=$BOUNDARY" \
  --input /tmp/upload.body
```

Returns cleaned audio with background noise removed.

#### Stream Audio Isolation

```bash
# maton api sends a body verbatim but does not build a multipart envelope:
# assemble it first, then hand the file to --input.
BOUNDARY="maton-$$"
{
  printf -- '--%s\r\nContent-Disposition: form-data; name="audio"; filename="audio_file.mp3"\r\nContent-Type: application/octet-stream\r\n\r\n' "$BOUNDARY"
  cat audio_file.mp3
  printf -- '\r\n'
  printf -- '--%s--\r\n' "$BOUNDARY"
} > /tmp/upload.body

maton api -X POST '/elevenlabs/v1/audio-isolation/stream' \
  -H "Content-Type: multipart/form-data; boundary=$BOUNDARY" \
  --input /tmp/upload.body
```

### Speech-to-Text API

#### Transcribe Audio

```bash
# maton api sends a body verbatim but does not build a multipart envelope:
# assemble it first, then hand the file to --input.
BOUNDARY="maton-$$"
{
  printf -- '--%s\r\nContent-Disposition: form-data; name="audio"; filename="audio_file.mp3"\r\nContent-Type: application/octet-stream\r\n\r\n' "$BOUNDARY"
  cat audio_file.mp3
  printf -- '\r\n'
  printf -- '--%s\r\nContent-Disposition: form-data; name="model_id"\r\n\r\nscribe_v1\r\n' "$BOUNDARY"
  printf -- '--%s--\r\n' "$BOUNDARY"
} > /tmp/upload.body

maton api -X POST '/elevenlabs/v1/speech-to-text' \
  -H "Content-Type: multipart/form-data; boundary=$BOUNDARY" \
  --input /tmp/upload.body
```

Returns transcription with optional word-level timestamps.

### Speech-to-Speech API

#### Convert Voice

```bash
# maton api sends a body verbatim but does not build a multipart envelope:
# assemble it first, then hand the file to --input.
BOUNDARY="maton-$$"
{
  printf -- '--%s\r\nContent-Disposition: form-data; name="audio"; filename="source_audio.mp3"\r\nContent-Type: application/octet-stream\r\n\r\n' "$BOUNDARY"
  cat source_audio.mp3
  printf -- '\r\n'
  printf -- '--%s\r\nContent-Disposition: form-data; name="model_id"\r\n\r\neleven_multilingual_sts_v2\r\n' "$BOUNDARY"
  printf -- '--%s--\r\n' "$BOUNDARY"
} > /tmp/upload.body

maton api -X POST '/elevenlabs/v1/speech-to-speech/{voice_id}' \
  -H "Content-Type: multipart/form-data; boundary=$BOUNDARY" \
  --input /tmp/upload.body
```

**Note:** `{voice_id}` is a placeholder. Replace it with a real value before sending the request.

Transforms audio to use a different voice while preserving intonation.

### Projects API

#### List Projects

```bash
maton api '/elevenlabs/v1/projects'
```

#### Get Project

```bash
maton api '/elevenlabs/v1/projects/{project_id}'
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Project

```bash
maton api -X POST '/elevenlabs/v1/projects' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "My Audiobook Project",
  "default_title_voice_id": "voice_id",
  "default_paragraph_voice_id": "voice_id"
}
JSON
```

### Pronunciation Dictionaries API

#### List Pronunciation Dictionaries

```bash
maton api '/elevenlabs/v1/pronunciation-dictionaries'
```

#### Create Pronunciation Dictionary

```bash
# maton api sends a body verbatim but does not build a multipart envelope:
# assemble it first, then hand the file to --input.
BOUNDARY="maton-$$"
{
  printf -- '--%s\r\nContent-Disposition: form-data; name="name"\r\n\r\nMy Dictionary\r\n' "$BOUNDARY"
  printf -- '--%s\r\nContent-Disposition: form-data; name="file"; filename="lexicon.pls"\r\nContent-Type: application/octet-stream\r\n\r\n' "$BOUNDARY"
  cat lexicon.pls
  printf -- '\r\n'
  printf -- '--%s--\r\n' "$BOUNDARY"
} > /tmp/upload.body

maton api -X POST '/elevenlabs/v1/pronunciation-dictionaries/add-from-file' \
  -H "Content-Type: multipart/form-data; boundary=$BOUNDARY" \
  --input /tmp/upload.body
```

### Response Headers

ElevenLabs API responses include useful headers:
- `x-character-count` - Characters used in the request
- `request-id` - Unique request identifier

#### Pagination

History and other list endpoints use cursor-based pagination:

```bash
maton api '/elevenlabs/v1/history?page_size=100&start_after_history_item_id=last_item_id'
```

### Notes

- Text-to-Speech returns audio/mpeg data
- Sound Effects returns audio/mpeg data
- Cursor-based pagination with `page_size` and `start_after_history_item_id`
- Response headers include `x-character-count` for usage tracking
- Models available: `eleven_multilingual_v2`, `eleven_turbo_v2_5`

### Resources

- [ElevenLabs API Documentation](https://elevenlabs.io/docs/api-reference)
- [Maton CLI Manual](https://cli.maton.ai/manual)
