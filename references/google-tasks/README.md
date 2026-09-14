# Google Tasks

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `google-tasks`
**Upstream base URL:** `tasks.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://tasks.googleapis.com/tasks/v1/users/@me/lists`
- Gateway: `https://api.maton.ai/google-tasks/tasks/v1/users/@me/lists`

### Task Lists API

#### List Task Lists

```bash
maton api '/google-tasks/tasks/v1/users/@me/lists'
```

Example:

```bash
maton google-tasks tasklist list
```

With pagination:
```bash
maton api '/google-tasks/tasks/v1/users/@me/lists?maxResults=20'
```

**Query parameters:**
- `maxResults` - Maximum number of task lists to return (default: 20, max: 100)
- `pageToken` - Token for pagination

Or with `maton api`:

#### Get Task List

```bash
maton api '/google-tasks/tasks/v1/users/@me/lists/{tasklistId}'
```

**Note:** `{tasklistId}` is a placeholder. Replace it with a real value before sending the request.

Example:

```bash
maton google-tasks tasklist get {tasklistId}
```

**Note:** `{tasklistId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Task List

```bash
maton api -X POST '/google-tasks/tasks/v1/users/@me/lists' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "New Task List"
}
JSON
```

Example:

```bash
maton google-tasks tasklist create --title 'New Task List'
```

#### Update Task List

```bash
maton api -X PATCH '/google-tasks/tasks/v1/users/@me/lists/{tasklistId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "Updated Title"
}
EOF
```

**Note:** `{tasklistId}` is a placeholder. Replace it with a real value before sending the request.

Example:

```bash
maton google-tasks tasklist update <tasklistId> --title 'Updated Title'
```

#### Update Task List (PUT - full replace)

```bash
maton google-tasks tasklist update <tasklistId> --title 'Replaced Title' --replace
```

Or with `maton api`:

```bash
maton api -X PUT '/google-tasks/tasks/v1/users/@me/lists/{tasklistId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Replaced Title"
}
JSON
```

**Note:** `{tasklistId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Task List

```bash
maton api '/google-tasks/tasks/v1/users/@me/lists/{tasklistId}' -X DELETE
```

**Note:** `{tasklistId}` is a placeholder. Replace it with a real value before sending the request.

Example:

```bash
maton google-tasks tasklist delete {tasklistId}
```

**Note:** `{tasklistId}` is a placeholder. Replace it with a real value before sending the request.

### Tasks API

#### List Tasks

```bash
maton api '/google-tasks/tasks/v1/lists/{tasklistId}/tasks'
```

**Note:** `{tasklistId}` is a placeholder. Replace it with a real value before sending the request.

Example:

```bash
maton google-tasks task list -l {tasklistId}
```

**Note:** `{tasklistId}` is a placeholder. Replace it with a real value before sending the request.

With filters:
```bash
maton api '/google-tasks/tasks/v1/lists/{tasklistId}/tasks?showCompleted=true&showHidden=true&maxResults=50'
```

**Note:** `{tasklistId}` is a placeholder. Replace it with a real value before sending the request.

Example:

```bash
maton google-tasks task list -l <tasklistId> --show-completed
```

With date filters:
```bash
maton api '/google-tasks/tasks/v1/lists/{tasklistId}/tasks?dueMin=2026-01-01T00:00:00Z&dueMax=2026-12-31T23:59:59Z'
```

**Note:** `{tasklistId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `maxResults` - Maximum number of tasks to return (default: 20, max: 100)
- `pageToken` - Token for pagination
- `showCompleted` - Include completed tasks (default: true)
- `showDeleted` - Include deleted tasks (default: false)
- `showHidden` - Include hidden tasks (default: false)
- `dueMin` - Lower bound for due date (RFC 3339 timestamp)
- `dueMax` - Upper bound for due date (RFC 3339 timestamp)
- `completedMin` - Lower bound for completion date (RFC 3339 timestamp)
- `completedMax` - Upper bound for completion date (RFC 3339 timestamp)
- `updatedMin` - Lower bound for last update time (RFC 3339 timestamp)

Example:

#### Get Task

```bash
maton api '/google-tasks/tasks/v1/lists/{tasklistId}/tasks/{taskId}'
```

**Note:** `{tasklistId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

Example:

```bash
maton google-tasks task get {taskId} -l {tasklistId}
```

**Note:** `{taskId}` and `{tasklistId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Task

```bash
maton api -X POST '/google-tasks/tasks/v1/lists/{tasklistId}/tasks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "New Task",
  "notes": "Task description",
  "due": "2026-03-01T00:00:00.000Z"
}
JSON
```

**Note:** `{tasklistId}` is a placeholder. Replace it with a real value before sending the request.

Example:

```bash
maton google-tasks task create -l <tasklistId> --title 'New Task' --notes 'Task description' --due 2026-03-01
```

Create subtask:
```bash
maton api -X POST '/google-tasks/tasks/v1/lists/{tasklistId}/tasks?parent={parentTaskId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "Subtask"
}
EOF
```

**Note:** `{tasklistId}` and `{parentTaskId}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters (optional):**
- `parent` - Parent task ID (for subtasks)
- `previous` - Previous sibling task ID (for positioning)

Or with `maton api`:

#### Update Task (partial)

```bash
maton api -X PATCH '/google-tasks/tasks/v1/lists/{tasklistId}/tasks/{taskId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "Updated Title",
  "status": "completed"
}
EOF
```

**Note:** `{tasklistId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

Example:

```bash
maton google-tasks task update <taskId> -l <tasklistId> --title 'Updated Title' --status completed
```

#### Update Task (full replace)

```bash
maton api -X PUT '/google-tasks/tasks/v1/lists/{tasklistId}/tasks/{taskId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "Replaced Task",
  "notes": "New notes",
  "status": "needsAction"
}
EOF
```

**Note:** `{tasklistId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

Example:

```bash
maton google-tasks task update <taskId> -l <tasklistId> --title 'Replaced Task' --notes 'New notes' --status needsAction --replace
```

#### Delete Task

```bash
maton api '/google-tasks/tasks/v1/lists/{tasklistId}/tasks/{taskId}' -X DELETE
```

**Note:** `{tasklistId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

Example:

```bash
maton google-tasks task delete {taskId} -l {tasklistId}
```

**Note:** `{taskId}` and `{tasklistId}` are placeholders. Replace each of them with real values before sending the request.

#### Move Task

```bash
maton api -X POST '/google-tasks/tasks/v1/lists/{tasklistId}/tasks/{taskId}/move?previous={previousTaskId}'
```

**Note:** `{tasklistId}`, `{taskId}` and `{previousTaskId}` are placeholders. Replace each of them with real values before sending the request.

Example:

```bash
maton google-tasks task move {taskId} -l {tasklistId} --previous {siblingTaskId}
```

**Note:** `{taskId}`, `{tasklistId}` and `{siblingTaskId}` are placeholders. Replace each of them with real values before sending the request.

Make subtask:
```bash
maton api -X POST '/google-tasks/tasks/v1/lists/{tasklistId}/tasks/{taskId}/move?parent={parentTaskId}'
```

**Note:** `{tasklistId}`, `{taskId}` and `{parentTaskId}` are placeholders. Replace each of them with real values before sending the request.

Reposition a task within a task list or change its parent.

**Query parameters (optional):**
- `parent` - New parent task ID (for making it a subtask)
- `previous` - Previous sibling task ID (for positioning after this task)

Or with `maton api`:

#### Clear Completed Tasks

```bash
maton api -X POST '/google-tasks/tasks/v1/lists/{tasklistId}/clear'
```

**Note:** `{tasklistId}` is a placeholder. Replace it with a real value before sending the request.

Example:

```bash
maton google-tasks tasklist clear {tasklistId}
```

**Note:** `{tasklistId}` is a placeholder. Replace it with a real value before sending the request.

Delete all completed tasks from a task list.

### Task Resource Fields

| Field | Type | Description |
|-------|------|-------------|
| `kind` | string | Always "tasks#task" (output only) |
| `id` | string | Task identifier |
| `etag` | string | ETag of the resource |
| `title` | string | Task title (max 1024 characters) |
| `updated` | string | Last modification time (RFC 3339, output only) |
| `selfLink` | string | URL to this task (output only) |
| `parent` | string | Parent task ID (output only) |
| `position` | string | Position among siblings (output only) |
| `notes` | string | Task notes (max 8192 characters) |
| `status` | string | "needsAction" or "completed" |
| `due` | string | Due date (RFC 3339 timestamp) |
| `completed` | string | Completion date (RFC 3339, output only) |
| `deleted` | boolean | Whether task is deleted |
| `hidden` | boolean | Whether task is hidden |
| `links` | array | Collection of links (output only) |
| `webViewLink` | string | Link to task in Google Tasks UI (output only) |

### Task List Resource Fields

| Field | Type | Description |
|-------|------|-------------|
| `kind` | string | Always "tasks#taskList" (output only) |
| `id` | string | Task list identifier |
| `etag` | string | ETag of the resource |
| `title` | string | Task list title (max 1024 characters) |
| `updated` | string | Last modification time (RFC 3339, output only) |
| `selfLink` | string | URL to this task list (output only) |

### Pagination

Google Tasks uses token-based pagination. The CLI handles this automatically with `--paginate`:

```bash
maton google-tasks task list -l <tasklistId> --paginate
```

For raw HTTP requests, pass the `nextPageToken` from the previous response as the `pageToken` query parameter.

### Notes

- Task list and task IDs are opaque base64-encoded strings
- Status values: "needsAction" or "completed"
- Dates must be in RFC 3339 format (e.g., `2026-01-15T00:00:00.000Z`)
- Maximum title length: 1024 characters
- Maximum notes length: 8192 characters

### Resources

- [Google Tasks API Overview](https://developers.google.com/workspace/tasks)
- [Tasks Reference](https://developers.google.com/workspace/tasks/reference/rest/v1/tasks)
- [TaskLists Reference](https://developers.google.com/workspace/tasks/reference/rest/v1/tasklists)
- [Maton CLI Manual](https://cli.maton.ai/manual)
