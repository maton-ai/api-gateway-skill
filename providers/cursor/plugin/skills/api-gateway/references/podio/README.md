# Podio

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

> **Never suppress notifications or webhooks to make a change less visible.** Podio accepts `silent=true` (no notifications to collaborators) and `hook=false` (no webhook triggers) on write operations. Both remove the signals coworkers and downstream automations rely on to notice a change, so a modification made with them set is effectively invisible to everyone but the caller.
> - Default to leaving both off. An agent writing to a shared workspace should be *more* visible than a human doing the same thing, not less.
> - Set either flag only when the user explicitly asks for it, and only for the specific call they asked about — never apply it broadly or carry it over to later calls.
> - Never use them to reduce noise, avoid alarming someone, hide a mistake, or work around a failing webhook. If a webhook is misfiring, say so instead of silencing it.
> - `hook=false` can also break integrations that depend on those triggers to stay in sync; the resulting drift is silent by definition.

**App name:** `podio`
**Upstream base URL:** `api.podio.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.podio.com/org/`
- Gateway: `https://api.maton.ai/podio/org/`

**Important:** Many Podio endpoints use trailing slashes.

### Organizations API

#### List Organizations

Returns all organizations and spaces the user is a member of.

```bash
maton api '/podio/org/'
```

**Response:**
```json
[
  {
    "org_id": 123456,
    "name": "My Organization",
    "url": "https://podio.com/myorg",
    "url_label": "myorg",
    "type": "premium",
    "role": "admin",
    "status": "active",
    "spaces": [
      {
        "space_id": 789,
        "name": "Project Space",
        "url": "https://podio.com/myorg/project-space",
        "role": "admin"
      }
    ]
  }
]
```

#### Get Organization

```bash
maton api '/podio/org/{org_id}'
```

**Note:** `{org_id}` is a placeholder. Replace it with a real value before sending the request.

### Spaces

### Space API

#### Get Space

```bash
maton api '/podio/space/{space_id}'
```

**Note:** `{space_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "space_id": 789,
  "name": "Project Space",
  "privacy": "closed",
  "auto_join": false,
  "url": "https://podio.com/myorg/project-space",
  "url_label": "project-space",
  "role": "admin",
  "created_on": "2025-01-15T10:30:00Z",
  "created_by": {
    "user_id": 12345,
    "name": "John Doe"
  }
}
```

#### List Spaces in Organization

```bash
maton api '/podio/space/org/{org_id}/'
```

**Note:** `{org_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Space

```bash
maton api -X POST '/podio/space/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "org_id": 123456,
  "name": "New Project Space",
  "privacy": "closed",
  "auto_join": false,
  "post_on_new_app": true,
  "post_on_new_member": true
}
JSON
```

**Response:**
```json
{
  "space_id": 790,
  "url": "https://podio.com/myorg/new-project-space"
}
```

#### Create Space

```bash
maton api -X POST '/podio/org/{org_id}/space/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "New Workspace",
  "privacy": "closed"
}
EOF
```

**Note:** `{org_id}` is a placeholder. Replace it with a real value before sending the request.

### Applications API

#### List Apps in Space

```bash
maton api '/podio/app/space/{space_id}/'
```

**Note:** `{space_id}` is a placeholder. Replace it with a real value before sending the request.

Optional query parameters:
- `include_inactive` - Include inactive apps (default: false)

#### Get App

```bash
maton api '/podio/app/{app_id}'
```

**Note:** `{app_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "app_id": 456,
  "status": "active",
  "space_id": 789,
  "config": {
    "name": "Tasks",
    "item_name": "Task",
    "description": "Track project tasks",
    "icon": "list"
  },
  "fields": [...]
}
```

### Items API

#### Get Item

```bash
maton api '/podio/item/{item_id}'
```

**Note:** `{item_id}` is a placeholder. Replace it with a real value before sending the request.

Optional query parameters:
- `mark_as_viewed` - Mark notifications as viewed (default: true)

**Response:**
```json
{
  "item_id": 123,
  "title": "Complete project plan",
  "app": {
    "app_id": 456,
    "name": "Tasks"
  },
  "fields": [
    {
      "field_id": 1,
      "external_id": "status",
      "type": "category",
      "values": [{"value": {"text": "In Progress"}}]
    }
  ],
  "created_on": "2025-01-20T14:00:00Z",
  "created_by": {
    "user_id": 12345,
    "name": "John Doe"
  }
}
```

#### Filter Items

```bash
maton api -X POST '/podio/item/app/{app_id}/filter/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "sort_by": "created_on",
  "sort_desc": true,
  "filters": {
    "status": [1, 2]
  },
  "limit": 30,
  "offset": 0
}
JSON
```

**Note:** `{app_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "total": 150,
  "filtered": 45,
  "items": [
    {
      "item_id": 123,
      "title": "Complete project plan",
      "fields": [...],
      "comment_count": 5,
      "file_count": 2
    }
  ]
}
```

#### Create Item

```bash
maton api -X POST '/podio/item/app/{app_id}/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "fields": {
    "title": "New task",
    "status": 1,
    "due-date": {"start": "2025-02-15"}
  },
  "tags": ["urgent", "project-alpha"],
  "file_ids": [12345]
}
JSON
```

**Note:** `{app_id}` is a placeholder. Replace it with a real value before sending the request.

Optional query parameters:
- `hook` - Execute hooks (default: true)
- `silent` - Suppress notifications (default: false)

**Response:**
```json
{
  "item_id": 124,
  "title": "New task"
}
```

#### Update Item

```bash
maton api -X PUT '/podio/item/{item_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "fields": {
    "status": 2
  },
  "revision": 5
}
JSON
```

**Note:** `{item_id}` is a placeholder. Replace it with a real value before sending the request.

Optional query parameters:
- `hook` - Execute hooks (default: true)
- `silent` - Suppress notifications (default: false)

**Response:**
```json
{
  "revision": 6,
  "title": "New task"
}
```

#### Delete Item

```bash
maton api '/podio/item/{item_id}' -X DELETE
```

**Note:** `{item_id}` is a placeholder. Replace it with a real value before sending the request.

Optional query parameters:
- `hook` - Execute hooks (default: true)
- `silent` - Suppress notifications (default: false)

### Tasks API

Tasks require at least one filter: org, space, app, responsible, reference, created_by, or completed_by.

#### List Tasks

**Note:** Tasks require at least one filter: `org`, `space`, `app`, `responsible`, `reference`, `created_by`, or `completed_by`.

```bash
maton api '/podio/task/?org={org_id}'

maton api '/podio/task/?space={space_id}'

maton api '/podio/task/?app={app_id}&completed=false'
```

**Note:** `{org_id}`, `{space_id}` and `{app_id}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**
- `org` - Filter by organization ID (required if no other filter)
- `space` - Filter by space ID
- `app` - Filter by app ID
- `completed` - Filter by completion status (`true` or `false`)
- `responsible` - Filter by responsible user IDs
- `created_by` - Filter by creator
- `due_date` - Date range (YYYY-MM-DD-YYYY-MM-DD)
- `limit` - Maximum results
- `offset` - Result offset
- `sort_by` - Sort by: created_on, completed_on, rank (default: rank)
- `grouping` - Group by: due_date, created_by, responsible, app, space, org

#### Get Task

```bash
maton api '/podio/task/{task_id}'
```

**Note:** `{task_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "task_id": 789,
  "text": "Review project proposal",
  "description": "Detailed review of the Q1 proposal",
  "status": "active",
  "due_date": "2025-02-15",
  "due_time": "17:00:00",
  "responsible": {
    "user_id": 12345,
    "name": "John Doe"
  },
  "created_on": "2025-01-20T10:00:00Z",
  "labels": [
    {"label_id": 1, "text": "High Priority", "color": "red"}
  ]
}
```

#### Complete Task

```bash
maton api -X POST '/podio/task/{task_id}/complete'
```

**Note:** `{task_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Task

```bash
maton api '/podio/task/{task_id}' -X DELETE
```

**Note:** `{task_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Task

```bash
maton api -X POST '/podio/task/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "text": "Review project proposal",
  "description": "Detailed review of the Q1 proposal",
  "due_date": "2025-02-15",
  "due_time": "17:00:00",
  "responsible": 12345,
  "private": false,
  "ref_type": "item",
  "ref_id": 123,
  "labels": [1, 2]
}
JSON
```

Optional query parameters:
- `hook` - Execute hooks (default: true)
- `silent` - Suppress notifications (default: false)

**Response:**
```json
{
  "task_id": 790,
  ...
}
```

### Comments API

#### Get Comments on Object

```bash
maton api '/podio/comment/{type}/{id}/'
```

**Note:** `{type}` and `{id}` are placeholders. Replace each of them with real values before sending the request.

Where `{type}` is: item, task, status, etc.

Where `{type}` is the object type (e.g., "item", "task") and `{id}` is the object ID.

Optional query parameters:
- `limit` - Maximum comments (default: 100)
- `offset` - Pagination offset (default: 0)

**Response:**

```json
[
  {
    "comment_id": 456,
    "value": "This looks great!",
    "created_on": "2025-01-20T15:30:00Z",
    "created_by": {
      "user_id": 12345,
      "name": "John Doe"
    },
    "files": []
  }
]
```

#### Add Comment

```bash
maton api -X POST '/podio/comment/{type}/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "value": "Great progress on this task!",
  "file_ids": [12345],
  "embed_url": "https://example.com/doc"
}
JSON
```

**Note:** `{type}` and `{id}` are placeholders. Replace each of them with real values before sending the request.

Optional query parameters:
- `alert_invite` - Auto-invite mentioned users (default: false)
- `hook` - Execute hooks (default: true)
- `silent` - Suppress notifications (default: false)

**Response:**
```json
{
  "comment_id": 457,
  ...
}
```

### User API

#### Get User Status

```bash
maton api '/podio/user/status'
```

### Pagination

Podio uses offset-based pagination:

```json
{
  "limit": 30,
  "offset": 0
}
```

Response includes counts:
```json
{
  "total": 150,
  "filtered": 45,
  "items": [...]
}
```

### Notes

- Organization, space, app, and item IDs are integers
- Many endpoints use trailing slashes (e.g., `/org/`, `/filter/`)
- Category/status fields use option IDs (integers), not text values
- Field values can be specified by field_id or external_id
- Deleting an item cascades to associated tasks
- Tasks require at least one filter parameter
- `silent=true` suppresses notifications and `hook=false` skips webhook triggers — see the warning below before using either

### Resources

- [Podio API Documentation](https://developers.podio.com/doc)
- [Items API](https://developers.podio.com/doc/items)
- [Tasks API](https://developers.podio.com/doc/tasks)
- [Applications API](https://developers.podio.com/doc/applications)
- [Spaces API](https://developers.podio.com/doc/spaces)
- [Maton CLI Manual](https://cli.maton.ai/manual)
