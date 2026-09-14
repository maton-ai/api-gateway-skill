# Attio

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `attio`
**Upstream base URL:** `api.attio.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.attio.com/v2/objects`
- Gateway: `https://api.maton.ai/attio/v2/objects`

### Objects API

#### List Objects

```bash
maton api '/attio/v2/objects'
```

Returns all system-defined and custom objects in your workspace.

#### Get Object

```bash
maton api '/attio/v2/objects/{object}'
```

**Note:** `{object}` is a placeholder. Replace it with a real value before sending the request.

Get a specific object by slug (e.g., `people`, `companies`) or UUID.

### Attributes API

#### List Attributes

```bash
maton api '/attio/v2/objects/{object}/attributes'
```

**Note:** `{object}` is a placeholder. Replace it with a real value before sending the request.

Returns all attributes for an object.

### Records API

#### Query Records

```bash
maton api -X POST '/attio/v2/objects/{object}/records/query' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "limit": 50,
  "offset": 0,
  "filter": {},
  "sorts": []
}
JSON
```

**Note:** `{object}` is a placeholder. Replace it with a real value before sending the request.

**Request body:**
- `limit`: Maximum results (default 500)
- `offset`: Number of results to skip
- `filter`: Filter criteria object
- `sorts`: Array of sort specifications

#### Get Record

```bash
maton api '/attio/v2/objects/{object}/records/{record_id}'
```

**Note:** `{object}` and `{record_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Record

```bash
maton api -X POST '/attio/v2/objects/{object}/records' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "values": {
      "name": [{"first_name": "John", "last_name": "Doe", "full_name": "John Doe"}],
      "email_addresses": ["john@example.com"]
    }
  }
}
JSON
```

**Note:** `{object}` is a placeholder. Replace it with a real value before sending the request.

**Note:** For `personal-name` type attributes (like `name` on people), you must include `full_name` along with `first_name` and `last_name`.

#### Update Record

```bash
maton api -X PATCH '/attio/v2/objects/{object}/records/{record_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "values": {
      "job_title": "Software Engineer"
    }
  }
}
JSON
```

**Note:** `{object}` and `{record_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Record

```bash
maton api '/attio/v2/objects/{object}/records/{record_id}' -X DELETE
```

**Note:** `{object}` and `{record_id}` are placeholders. Replace each of them with real values before sending the request.

### Tasks API

#### List Tasks

```bash
maton api '/attio/v2/tasks?limit=50'
```

**Query parameters:**
- `limit`: Maximum results (default 500)
- `offset`: Number to skip
- `sort`: `created_at:asc` or `created_at:desc`
- `linked_object`: Filter by object type (e.g., `people`)
- `linked_record_id`: Filter by specific record
- `assignee`: Filter by assignee email/ID
- `is_completed`: Filter by completion status (true/false)

#### Get Task

```bash
maton api '/attio/v2/tasks/{task_id}'
```

**Note:** `{task_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Task

```bash
maton api -X POST '/attio/v2/tasks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "content": "Follow up with customer",
    "format": "plaintext",
    "deadline_at": "2026-02-15T00:00:00.000000000Z",
    "is_completed": false,
    "assignees": [],
    "linked_records": [
      {
        "target_object": "companies",
        "target_record_id": "16f2fc57-5d22-48b8-b9db-8b0e6d99e9bc"
      }
    ]
  }
}
JSON
```

**Request body:** `content` (required), `format` (required), `deadline_at` (required), `assignees` (required), `linked_records` (required)

#### Update Task

```bash
maton api -X PATCH '/attio/v2/tasks/{task_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "is_completed": true
  }
}
JSON
```

**Note:** `{task_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Task

```bash
maton api '/attio/v2/tasks/{task_id}' -X DELETE
```

**Note:** `{task_id}` is a placeholder. Replace it with a real value before sending the request.

### Workspace Members API

#### List Workspace Members

```bash
maton api '/attio/v2/workspace_members'
```

#### Get Workspace Member

```bash
maton api '/attio/v2/workspace_members/{workspace_member_id}'
```

**Note:** `{workspace_member_id}` is a placeholder. Replace it with a real value before sending the request.

### Self API

#### Identify Current Token

```bash
maton api '/attio/v2/self'
```

Returns workspace info and OAuth scopes for the current credential.

### Comments API

#### Create Comment on Record

```bash
maton api -X POST '/attio/v2/comments' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "format": "plaintext",
    "content": "This is a comment",
    "author": {
      "type": "workspace-member",
      "id": "{workspace_member_id}"
    },
    "record": {
      "object": "companies",
      "record_id": "{record_id}"
    }
  }
}
JSON
```

**Note:** `{workspace_member_id}` and `{record_id}` are placeholders. Replace each of them with real values before sending the request.

**Request body:** `format` (required), `content` (required), `author` (required)

Plus one of:
- `record`: Object with `object` slug and `record_id` (for record comments)
- `entry`: Object with `list` slug and `entry_id` (for list entry comments)
- `thread_id`: UUID of existing thread (for replies)

#### Reply to Comment Thread

```bash
maton api -X POST '/attio/v2/comments' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "format": "plaintext",
    "content": "This is a reply",
    "author": {
      "type": "workspace-member",
      "id": "{workspace_member_id}"
    },
    "thread_id": "{thread_id}"
  }
}
JSON
```

**Note:** `{workspace_member_id}` and `{thread_id}` are placeholders. Replace each of them with real values before sending the request.

### Lists API

#### List All Lists

```bash
maton api '/attio/v2/lists'
```

#### Get List

```bash
maton api '/attio/v2/lists/{list_id}'
```

**Note:** `{list_id}` is a placeholder. Replace it with a real value before sending the request.

### List Entries API

#### Query List Entries

```bash
maton api -X POST '/attio/v2/lists/{list}/entries/query' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "limit": 50,
  "offset": 0,
  "filter": {},
  "sorts": []
}
JSON
```

**Note:** `{list}` is a placeholder. Replace it with a real value before sending the request.

Request body:
- `limit`: Maximum results (default 500)
- `offset`: Number of results to skip
- `filter`: Filter criteria object
- `sorts`: Array of sort specifications

#### Create List Entry

```bash
maton api -X POST '/attio/v2/lists/{list}/entries' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "parent_record_id": "{record_id}",
    "parent_object": "companies",
    "entry_values": {}
  }
}
JSON
```

**Note:** `{list}` and `{record_id}` are placeholders. Replace each of them with real values before sending the request.

#### Get List Entry

```bash
maton api '/attio/v2/lists/{list}/entries/{entry_id}'
```

**Note:** `{list}` and `{entry_id}` are placeholders. Replace each of them with real values before sending the request.

#### Update List Entry

```bash
maton api -X PATCH '/attio/v2/lists/{list}/entries/{entry_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "entry_values": {
      "status": "Active"
    }
  }
}
JSON
```

**Note:** `{list}` and `{entry_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete List Entry

```bash
maton api '/attio/v2/lists/{list}/entries/{entry_id}' -X DELETE
```

**Note:** `{list}` and `{entry_id}` are placeholders. Replace each of them with real values before sending the request.

### Notes API

#### List Notes

```bash
maton api '/attio/v2/notes?limit=50'
```

**Query parameters:**
- `limit`: Maximum results (default 10, max 50)
- `offset`: Number to skip
- `parent_object`: Object slug containing notes
- `parent_record_id`: Filter by specific record

#### Get Note

```bash
maton api '/attio/v2/notes/{note_id}'
```

**Note:** `{note_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Note

```bash
maton api -X POST '/attio/v2/notes' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {
    "format": "plaintext",
    "title": "Meeting Summary",
    "content": "Discussed Q1 goals and roadmap priorities.",
    "parent_object": "companies",
    "parent_record_id": "{record_id}",
    "created_by_actor": {
      "type": "workspace-member",
      "id": "{workspace_member_id}"
    }
  }
}
JSON
```

**Note:** `{record_id}` and `{workspace_member_id}` are placeholders. Replace each of them with real values before sending the request.

Required parameters: `format`, `content`, `parent_object`, `parent_record_id`

#### Delete Note

```bash
maton api '/attio/v2/notes/{note_id}' -X DELETE
```

**Note:** `{note_id}` is a placeholder. Replace it with a real value before sending the request.

### Meetings API

#### List Meetings

```bash
maton api '/attio/v2/meetings?limit=50'
```

**Query parameters:**
- `limit`: Maximum results (default 50, max 200)
- `cursor`: Pagination cursor from previous response

Uses cursor-based pagination.

#### Get Meeting

```bash
maton api '/attio/v2/meetings/{meeting_id}'
```

**Note:** `{meeting_id}` is a placeholder. Replace it with a real value before sending the request.

### Call Recordings API

#### List Call Recordings for Meeting

```bash
maton api '/attio/v2/meetings/{meeting_id}/call_recordings?limit=50'
```

**Note:** `{meeting_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `limit`: Maximum results (default 50, max 200)
- `cursor`: Pagination cursor from previous response

#### Get Call Recording

```bash
maton api '/attio/v2/meetings/{meeting_id}/call_recordings/{call_recording_id}'
```

**Note:** `{meeting_id}` and `{call_recording_id}` are placeholders. Replace each of them with real values before sending the request.

### Pagination

Attio supports two pagination methods:

#### Limit/Offset Pagination

```bash
maton api '/attio/v2/tasks?limit=50&offset=0'

maton api '/attio/v2/tasks?limit=50&offset=50'

maton api '/attio/v2/tasks?limit=50&offset=100'
```

#### Cursor-Based Pagination (for some endpoints)

```bash
maton api '/attio/v2/meetings?limit=50'

maton api '/attio/v2/meetings?limit=50&cursor={next_cursor}'
```

**Note:** `{next_cursor}` is a placeholder. Replace it with a real value before sending the request.

Response includes `pagination.next_cursor` when more results exist.

### Notes

- Object slugs are lowercase snake_case (e.g., `people`, `companies`)
- Record IDs and other IDs are UUIDs
- For personal-name attributes, always include `full_name` when creating records
- Task creation requires `format: "plaintext"`, `deadline_at`, `assignees` array (can be empty), and `linked_records` array (can be empty)
- Note creation requires `format`, `content`, `parent_object`, and `parent_record_id`
- Comment creation requires `format`, `content`, `author`, plus one of `record`, `entry`, or `thread_id`
- Meetings use cursor-based pagination
- Some endpoints require additional OAuth scopes (lists, notes, webhooks)
- Rate limits: 100 read requests/second, 25 write requests/second
- Pagination uses `limit` and `offset` parameters (or `cursor` for meetings)
- IMPORTANT: When using curl commands, use `curl -g` when URLs contain brackets to disable glob parsing

### Resources

- [Attio API Overview](https://docs.attio.com/rest-api/overview)
- [Attio API Reference](https://docs.attio.com/rest-api/endpoint-reference)
- [Records API](https://docs.attio.com/rest-api/endpoint-reference/records)
- [Objects API](https://docs.attio.com/rest-api/endpoint-reference/objects)
- [Tasks API](https://docs.attio.com/rest-api/endpoint-reference/tasks)
- [Rate Limiting](https://docs.attio.com/rest-api/guides/rate-limiting)
- [Pagination](https://docs.attio.com/rest-api/guides/pagination)
- [Maton CLI Manual](https://cli.maton.ai/manual)
