# TickTick

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `ticktick`
**Upstream base URL:** `api.ticktick.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.ticktick.com/open/v1/project`
- Gateway: `https://api.maton.ai/ticktick/open/v1/project`

### Project API

#### List Projects

```bash
maton api '/ticktick/open/v1/project'
```

**Response:**
```json
[
  {
    "id": "6984773291819e6d58b746a8",
    "name": "🏡Memo",
    "sortOrder": 0,
    "viewMode": "list",
    "kind": "TASK"
  },
  {
    "id": "6984773291819e6d58b746a9",
    "name": "🦄Wishlist",
    "sortOrder": -1099511627776,
    "viewMode": "list",
    "kind": "TASK"
  }
]
```

#### Get Project with Tasks

```bash
maton api '/ticktick/open/v1/project/{projectId}/data'
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**

```json
{
  "project": {
    "id": "69847732b8e5e969f70e7460",
    "name": "👋Welcome",
    "sortOrder": -3298534883328,
    "viewMode": "list",
    "kind": "TASK"
  },
  "tasks": [
    {
      "id": "69847732b8e5e969f70e7464",
      "projectId": "69847732b8e5e969f70e7460",
      "title": "Sample task",
      "content": "Task description",
      "priority": 0,
      "status": 0,
      "tags": [],
      "isAllDay": false
    }
  ],
  "columns": [
    {
      "id": "69847732b8e5e969f70e7463",
      "projectId": "69847732b8e5e969f70e7460",
      "name": "Getting Start",
      "sortOrder": -2199023255552
    }
  ]
}
```

#### Create Project

```bash
maton api -X POST '/ticktick/open/v1/project' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "My New Project",
  "viewMode": "list"
}
JSON
```

**viewMode options:** `list`, `kanban`, `timeline`

**Response:**

```json
{
  "id": "69870cbe8f08b4a6770a38d3",
  "name": "My New Project",
  "sortOrder": 0,
  "viewMode": "list",
  "kind": "TASK"
}
```

**viewMode options:**
- `list` - List view
- `kanban` - Kanban board view
- `timeline` - Timeline view

#### Delete Project

```bash
maton api '/ticktick/open/v1/project/{projectId}' -X DELETE
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

Returns empty response on success (status 200).

### Task API

#### Get Task

```bash
maton api '/ticktick/open/v1/project/{projectId}/task/{taskId}'
```

**Note:** `{projectId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "id": "69847732b8e5e969f70e7464",
  "projectId": "69847732b8e5e969f70e7460",
  "sortOrder": -1099511627776,
  "title": "Task title",
  "content": "Task description/notes",
  "timeZone": "Asia/Shanghai",
  "isAllDay": true,
  "priority": 0,
  "status": 0,
  "tags": [],
  "columnId": "69847732b8e5e969f70e7461",
  "etag": "2sayfdsh",
  "kind": "TEXT"
}
```

#### Create Task

```bash
maton api -X POST '/ticktick/open/v1/task' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "New task",
  "projectId": "6984773291819e6d58b746a8",
  "content": "Task description",
  "priority": 0,
  "dueDate": "2026-02-15T10:00:00+0000",
  "isAllDay": false
}
JSON
```

**Priority values:**
- `0` - None
- `1` - Low
- `3` - Medium
- `5` - High

**Response:**

```json
{
  "id": "69870cb08f08b86b38951175",
  "projectId": "6984773291819e6d58b746a8",
  "sortOrder": -1099511627776,
  "title": "New task",
  "timeZone": "America/Los_Angeles",
  "isAllDay": false,
  "priority": 0,
  "status": 0,
  "tags": [],
  "etag": "gl7ibhor",
  "kind": "TEXT"
}
```

#### Update Task

```bash
maton api -X POST '/ticktick/open/v1/task/{taskId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "id": "69870cb08f08b86b38951175",
  "projectId": "6984773291819e6d58b746a8",
  "title": "Updated task title",
  "priority": 1
}
JSON
```

**Note:** `{taskId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": "69870cb08f08b86b38951175",
  "projectId": "6984773291819e6d58b746a8",
  "title": "Updated task title",
  "priority": 1,
  "status": 0,
  "etag": "hmb7uk8c",
  "kind": "TEXT"
}
```

#### Complete Task

```bash
maton api -X POST '/ticktick/open/v1/project/{projectId}/task/{taskId}/complete'
```

**Note:** `{projectId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

Returns empty response on success (status 200).

#### Delete Task

```bash
maton api '/ticktick/open/v1/project/{projectId}/task/{taskId}' -X DELETE
```

**Note:** `{projectId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

Returns empty response on success (status 200).

### Task Fields

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Task ID |
| `projectId` | string | Parent project ID |
| `title` | string | Task title |
| `content` | string | Task description (Markdown) |
| `priority` | integer | 0=None, 1=Low, 3=Medium, 5=High |
| `status` | integer | 0=Active, 2=Completed |
| `dueDate` | string | ISO 8601 format |
| `startDate` | string | ISO 8601 format |
| `isAllDay` | boolean | All-day task flag |
| `timeZone` | string | e.g., "America/Los_Angeles" |
| `tags` | array | List of tag names |
| `columnId` | string | Kanban column ID |

### Notes

- The Open API provides access to tasks and projects only
- Habits, focus/pomodoro, and tags endpoints are not available through the Open API
- Task `status` values: 0 = Active, 2 = Completed
- Dates use ISO 8601 format with timezone offset (e.g., `2026-02-15T10:00:00+0000`)
- The `columns` field in project data is used for Kanban board columns

### Resources

- [TickTick Developer Portal](https://developer.ticktick.com/)
- [TickTick Help Center](https://help.ticktick.com/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
