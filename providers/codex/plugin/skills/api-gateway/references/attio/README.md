# Attio Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `attio`
**Base URL proxied:** `api.attio.com`

## API Path Pattern

```
/attio/v2/{resource}
```

## Common Endpoints

### List Objects
```bash
maton api '/attio/v2/objects'
```

### Get Object
```bash
maton api '/attio/v2/objects/{object}'
```

### List Attributes
```bash
maton api '/attio/v2/objects/{object}/attributes'
```

### Query Records
```bash
maton api -X POST '/attio/v2/objects/{object}/records/query' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "limit": 50,
  "offset": 0
}
EOF
```

### Get Record
```bash
maton api '/attio/v2/objects/{object}/records/{record_id}'
```

### Create Record
```bash
maton api -X POST '/attio/v2/objects/{object}/records' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "values": {
      "name": [{"first_name": "John", "last_name": "Doe", "full_name": "John Doe"}],
      "email_addresses": ["john@example.com"]
    }
  }
}
EOF
```

### Update Record
```bash
maton api -X PATCH '/attio/v2/objects/{object}/records/{record_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "values": {
      "job_title": "Engineer"
    }
  }
}
EOF
```

### Delete Record
```bash
maton api -X DELETE '/attio/v2/objects/{object}/records/{record_id}'
```

### List Tasks
```bash
maton api '/attio/v2/tasks?limit=50'
```

### Create Task
```bash
maton api -X POST '/attio/v2/tasks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "content": "Task description",
    "format": "plaintext",
    "deadline_at": null,
    "assignees": [],
    "linked_records": []
  }
}
EOF
```

### List Workspace Members
```bash
maton api '/attio/v2/workspace_members'
```

### Identify Self
```bash
maton api '/attio/v2/self'
```

### Notes

#### List Notes
```bash
maton api '/attio/v2/notes?limit=50&parent_object={object}&parent_record_id={record_id}'
```

#### Get Note
```bash
maton api '/attio/v2/notes/{note_id}'
```

#### Create Note
```bash
maton api -X POST '/attio/v2/notes' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "format": "plaintext",
    "title": "Meeting Summary",
    "content": "Note content here",
    "parent_object": "companies",
    "parent_record_id": "{record_id}",
    "created_by_actor": {
      "type": "workspace-member",
      "id": "{workspace_member_id}"
    }
  }
}
EOF
```

#### Delete Note
```bash
maton api -X DELETE '/attio/v2/notes/{note_id}'
```

### Comments

#### Create Comment on Record
```bash
maton api -X POST '/attio/v2/comments' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "format": "plaintext",
    "content": "Comment text",
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
EOF
```

#### Reply to Comment Thread
```bash
maton api -X POST '/attio/v2/comments' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
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
EOF
```

### Lists

#### List All Lists
```bash
maton api '/attio/v2/lists'
```

#### Get List
```bash
maton api '/attio/v2/lists/{list_id}'
```

### List Entries

#### Query List Entries
```bash
maton api -X POST '/attio/v2/lists/{list}/entries/query' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "limit": 50,
  "offset": 0
}
EOF
```

#### Create List Entry
```bash
maton api -X POST '/attio/v2/lists/{list}/entries' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "parent_record_id": "{record_id}",
    "parent_object": "companies",
    "entry_values": {}
  }
}
EOF
```

#### Get List Entry
```bash
maton api '/attio/v2/lists/{list}/entries/{entry_id}'
```

#### Update List Entry
```bash
maton api -X PATCH '/attio/v2/lists/{list}/entries/{entry_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "entry_values": {
      "status": "Active"
    }
  }
}
EOF
```

#### Delete List Entry
```bash
maton api -X DELETE '/attio/v2/lists/{list}/entries/{entry_id}'
```

### Meetings

#### List Meetings
```bash
maton api '/attio/v2/meetings?limit=50'
```

#### Get Meeting
```bash
maton api '/attio/v2/meetings/{meeting_id}'
```

### Call Recordings

#### List Call Recordings for Meeting
```bash
maton api '/attio/v2/meetings/{meeting_id}/call_recordings?limit=50'
```

#### Get Call Recording
```bash
maton api '/attio/v2/meetings/{meeting_id}/call_recordings/{call_recording_id}'
```

## Usage Notes

- Object slugs are lowercase snake_case (e.g., `people`, `companies`)
- Record IDs are UUIDs
- For personal-name attributes, include `full_name` when creating records
- Task creation requires `format`, `deadline_at`, `assignees`, and `linked_records` fields
- Note creation requires `format`, `content`, `parent_object`, and `parent_record_id`
- Comment creation requires `format`, `content`, `author`, plus one of `record`, `entry`, or `thread_id`
- Meetings use cursor-based pagination
- Rate limits: 100 read/sec, 25 write/sec
- Pagination uses `limit` and `offset` parameters (or `cursor` for meetings)

## Resources

- [Attio API Overview](https://docs.attio.com/rest-api/overview)
- [Attio API Reference](https://docs.attio.com/rest-api/endpoint-reference)
- [Records API](https://docs.attio.com/rest-api/endpoint-reference/records)
