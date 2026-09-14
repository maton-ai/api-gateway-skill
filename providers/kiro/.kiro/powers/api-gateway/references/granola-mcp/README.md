# Granola MCP

## MCP Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

> **Privacy — meeting notes are among the most sensitive data in this gateway.** Responses contain private notes, AI-generated summaries, decisions, action items, and participant names and email addresses. Meetings routinely cover compensation, personnel matters, legal exposure, unannounced plans, and customer confidences. Other attendees did not consent to their words being read by an agent or relayed onward.
> - Retrieve only the meetings the task needs — a specific meeting or date range, not the full history.
> - Return the narrowest answer that satisfies the request. Do not reproduce whole transcripts or summaries when the user asked a specific question.
> - **Do not forward meeting content to third-party hosts** — no trigger destinations, external webhooks, or non-`api.maton.ai` APIs — without explicit, informed user approval for that specific transfer.
> - Treat participant names and emails as personal data: don't build contact lists from them or use them for anything outside the stated task.
> - Before posting meeting content anywhere shared (Slack, docs, email, issue trackers), confirm with the user — attendees may not expect it to travel beyond the meeting.

**App name:** `granola`
**Upstream base URL:** `mcp.granola.ai` (MCP server)

This app is reached over MCP so there is no upstream REST path to rewrite. Each MCP tool is a `POST` to the app name followed by the tool name; the arguments go in the JSON body. The MCP credentials are stored in the Maton connection, and the gateway injects them so requests never carry them. For example: `https://api.maton.ai/granola/query_granola_meetings`

All MCP tools use `POST` method:

| Tool | Description | Schema |
|------|-------------|--------|
| `query_granola_meetings` | Chat with meeting notes using natural language | [schema](schemas/query_granola_meetings.json) |
| `list_meetings` | List meetings with metadata and attendees | [schema](schemas/list_meetings.json) |
| `get_meetings` | Retrieve detailed content for specific meetings | [schema](schemas/get_meetings.json) |
| `get_meeting_transcript` | Get raw transcript (paid tiers only) | [schema](schemas/get_meeting_transcript.json) |

### Common Tools

#### Query Meetings Tool

```bash
maton api -X POST '/granola/query_granola_meetings' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "What action items came from my meetings this week?"
}
EOF
```

**Response:**
```json
{
  "content": [
    {
      "type": "text",
      "text": "You had 2 recent meetings:\n**Feb 4, 2026 at 7:30 PM** - \"Team sync\" [[0]](https://notes.granola.ai/d/abc123)\n- Action item: Review Q1 roadmap\n- Action item: Schedule follow-up with engineering\n**Jan 27, 2026 at 1:04 AM** - \"Finance integration\" [[1]](https://notes.granola.ai/d/def456)\n- Discussed workflow automation platforms\n- Action item: Evaluate n8n vs Zapier"
    }
  ],
  "isError": false
}
```

**Use cases:**
- "What action items were assigned to me?"
- "Summarize my meetings from last week"
- "What did we discuss about the product launch?"
- "Find all mentions of budget in my meetings"

#### List Meetings Tool

```bash
maton api -X POST '/granola/list_meetings' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{}
EOF
```

**Response:**
```json
{
  "content": [
    {
      "type": "text",
      "text": "<meetings_data from=\"Jan 27, 2026\" to=\"Feb 4, 2026\" count=\"2\">\n<meeting id=\"0dba4400-50f1-4262-9ac7-89cd27b79371\" title=\"Team sync\" date=\"Feb 4, 2026 7:30 PM\">\n    <known_participants>\n    John Doe (note creator) from Acme <john@acme.com>\n    Jane Smith from Acme <jane@acme.com>\n    </known_participants>\n  </meeting>\n\n<meeting id=\"4ebc086f-ba8d-49e8-8cd1-ed81ac8f2e3b\" title=\"Finance integration\" date=\"Jan 27, 2026 1:04 AM\">\n    <known_participants>\n    John Doe (note creator) from Acme <john@acme.com>\n    </known_participants>\n  </meeting>\n</meetings_data>"
    }
  ],
  "isError": false
}
```

#### Get Meetings Tool

```bash
maton api -X POST '/granola/get_meetings' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "meeting_ids": ["0dba4400-50f1-4262-9ac7-89cd27b79371"]
}
EOF
```

**Response:**
```json
{
  "content": [
    {
      "type": "text",
      "text": "<meetings_data from=\"Feb 4, 2026\" to=\"Feb 4, 2026\" count=\"1\">\n<meeting id=\"0dba4400-50f1-4262-9ac7-89cd27b79371\" title=\"Team sync\" date=\"Feb 4, 2026 7:30 PM\">\n  <known_participants>\n  John Doe (note creator) from Acme <john@acme.com>\n  </known_participants>\n  \n  <summary>\n## Key Decisions\n- Approved Q1 roadmap\n- Budget increased by 15%\n\n## Action Items\n- @john: Review design specs by Friday\n- @jane: Schedule engineering sync\n</summary>\n</meeting>\n</meetings_data>"
    }
  ],
  "isError": false
}
```

#### Get Meeting Transcript Tool

```bash
maton api -X POST '/granola/get_meeting_transcript' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "meeting_id": "0dba4400-50f1-4262-9ac7-89cd27b79371"
}
EOF
```

**Response (paid tier):**
```json
{
  "content": [
    {
      "type": "text",
      "text": "<transcript meeting_id=\"0dba4400-50f1-4262-9ac7-89cd27b79371\">\n[00:00:15] John: Let's get started with the Q1 planning...\n[00:01:23] Jane: I've prepared the budget breakdown...\n[00:03:45] John: That looks good. What about the timeline?\n</transcript>"
    }
  ],
  "isError": false
}
```

**Response (free tier):**
```json
{
  "content": [
    {
      "type": "text",
      "text": "Transcripts are only available to paid Granola tiers"
    }
  ],
  "isError": true
}
```

### Response Format

All MCP tool responses wrap content in a `content` array of typed blocks alongside an `isError` flag:

```json
{
  "content": [
    {
      "type": "text",
      "text": "..."
    }
  ],
  "isError": false
}
```

Tool-level failures (for example requesting a transcript on a free tier) return HTTP 200 with `isError` set to `true` and the message in the same `content` array, so check `isError` rather than the HTTP status:

```json
{
  "content": [
    {
      "type": "text",
      "text": "Transcripts are only available to paid Granola tiers"
    }
  ],
  "isError": true
}
```

### Notes

- All IDs are UUIDs (with or without hyphens)
- Users can only query their own meeting notes; shared notes from others are not accessible
- Basic (free) plan users are limited to notes from the last 30 days
- The `get_meeting_transcript` tool is only available on paid Granola tiers
- If multiple Granola connections exist, specify which to use with `Maton-Connection` header
- Session can be reused by passing the `Mcp-Session-Id` header from previous responses
- Rate limit: ~100 requests/minute

### Resources

- [Granola MCP Documentation](https://docs.granola.ai/help-center/sharing/integrations/mcp)
- [Granola Help Center](https://docs.granola.ai)
- [Maton Community](https://discord.com/invite/dBfFAcefs2)
- [Maton Support](mailto:support@maton.ai)
- [Maton CLI Manual](https://cli.maton.ai/manual)
