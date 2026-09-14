# Todoist

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `todoist`
**Upstream base URL:** `api.todoist.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.todoist.com/api/v1/projects`
- Gateway: `https://api.maton.ai/todoist/api/v1/projects`

### Projects API

#### List Projects

```bash
maton api '/todoist/api/v1/projects'
```

**Response:**
```json
{
  "results": [
    {
      "id": "6fwFRqmVCFvWVX5R",
      "name": "Inbox",
      "color": "charcoal",
      "parent_id": null,
      "child_order": 0,
      "is_shared": false,
      "is_favorite": false,
      "inbox_project": true,
      "view_style": "list",
      "description": "",
      "is_archived": false
    }
  ],
  "next_cursor": null
}
```

#### Get Project

```bash
maton api '/todoist/api/v1/projects/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Project

```bash
maton api -X POST '/todoist/api/v1/projects' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "My Project",
  "color": "blue",
  "is_favorite": true,
  "view_style": "board"
}
JSON
```

**Request body:**
- `name` (required) - Project name
- `parent_id` - Parent project ID for nesting
- `color` - Project color (e.g., "red", "blue", "green")
- `is_favorite` - Boolean favorite status
- `view_style` - "list" or "board" (default: list)

**Example:**
```bash
maton api -X POST '/todoist/api/v1/projects' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "My Project",
  "color": "blue",
  "is_favorite": true,
  "view_style": "board"
}
JSON
```

#### Update Project

```bash
maton api -X POST '/todoist/api/v1/projects/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Project Name",
  "color": "red"
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Project

```bash
maton api '/todoist/api/v1/projects/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content on success.

#### Get Project Collaborators

```bash
maton api '/todoist/api/v1/projects/{id}/collaborators'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Tasks API

#### List Tasks

```bash
maton api '/todoist/api/v1/tasks'
```

**Query parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| `project_id` | string | Filter by project |
| `section_id` | string | Filter by section |
| `label` | string | Filter by label name |
| `filter` | string | Todoist filter expression |
| `ids` | string | Comma-separated task IDs |

**Response:**
```json
{
  "results": [
    {
      "id": "6fwhG9wMHr4wxgpR",
      "content": "Buy groceries",
      "description": "",
      "project_id": "6fwFRqmVCFvWVX5R",
      "section_id": null,
      "parent_id": null,
      "child_order": 1,
      "priority": 2,
      "checked": false,
      "labels": [],
      "due": {
        "date": "2026-02-07T10:00:00",
        "string": "tomorrow at 10am",
        "lang": "en",
        "is_recurring": false
      },
      "added_at": "2026-02-06T20:41:08.449320Z"
    }
  ],
  "next_cursor": null
}
```

The `due.lang` value reflects the language Todoist parsed the due-date string in and follows the account's own setting; `"en"` above is only what this example returned, not a requirement. Pass due-date strings in the language the user wrote them in.

#### Get Task

```bash
maton api '/todoist/api/v1/tasks/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Task

```bash
maton api -X POST '/todoist/api/v1/tasks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "content": "Buy groceries",
  "project_id": "2366834771",
  "priority": 2,
  "due_string": "tomorrow at 10am",
  "labels": ["shopping", "errands"]
}
JSON
```

**Request body:**
- `content` (required) - Task content/title
- `description` (optional) - Task description
- `project_id` (optional) - Project the task belongs to (defaults to Inbox)
- `section_id` (optional) - Section within project
- `parent_id` (optional) - Parent task ID for subtasks
- `labels` (optional) - Array of label names
- `priority` (optional) - 1 (normal) to 4 (urgent)
- `due_string` (optional) - Natural language due date ("tomorrow", "next Monday 3pm")
- `due_date` (optional) - ISO format YYYY-MM-DD
- `due_datetime` (optional) - RFC3339 format with timezone
- `assignee_id` (optional) - User ID to assign task
- `duration` (optional) - Task duration (integer)
- `duration_unit` (optional) - "minute" or "day"

**Example:**
```bash
maton api -X POST '/todoist/api/v1/tasks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "content": "Buy groceries",
  "project_id": "2366834771",
  "priority": 2,
  "due_string": "tomorrow at 10am",
  "labels": ["shopping", "errands"]
}
JSON
```

#### Update Task

```bash
maton api -X POST '/todoist/api/v1/tasks/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "content": "Updated task content",
  "priority": 3
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Close Task (Complete)

```bash
maton api -X POST '/todoist/api/v1/tasks/{id}/close'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content. For recurring tasks, this schedules the next occurrence.

#### Reopen Task

```bash
maton api -X POST '/todoist/api/v1/tasks/{id}/reopen'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content.

#### Delete Task

```bash
maton api '/todoist/api/v1/tasks/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content.

### Sections API

#### List Sections

```bash
maton api '/todoist/api/v1/sections'

maton api '/todoist/api/v1/sections?project_id={project_id}'
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "results": [
    {
      "id": "6g424m6CQm47v7mm",
      "project_id": "6g424jv8X52hP7qF",
      "section_order": 1,
      "name": "To Do",
      "added_at": "2026-02-20T22:25:04.203675Z",
      "is_archived": false,
      "is_collapsed": false
    }
  ],
  "next_cursor": null
}
```

#### Get Section

```bash
maton api '/todoist/api/v1/sections/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Section

```bash
maton api -X POST '/todoist/api/v1/sections' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "In Progress",
  "project_id": "2366834771",
  "order": 2
}
JSON
```

**Request body:**
- `name` (required) - Section name
- `project_id` (required) - Parent project ID

#### Update Section

```bash
maton api -X POST '/todoist/api/v1/sections/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Section Name"
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Section

```bash
maton api '/todoist/api/v1/sections/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content.

### Labels API

#### List Labels

```bash
maton api '/todoist/api/v1/labels'
```

**Response:**
```json
{
  "results": [
    {
      "id": "2182980313",
      "name": "urgent",
      "color": "red",
      "order": 1,
      "is_favorite": false
    }
  ],
  "next_cursor": null
}
```

#### Get Label

```bash
maton api '/todoist/api/v1/labels/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Label

```bash
maton api -X POST '/todoist/api/v1/labels' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "work",
  "color": "blue",
  "is_favorite": true
}
JSON
```

**Request body:**
- `name` (required) - Label name
- `color` - Label color
- `order` - Sort order
- `is_favorite` - Boolean favorite status

#### Update Label

```bash
maton api -X POST '/todoist/api/v1/labels/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "updated-label",
  "color": "green"
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Label

```bash
maton api '/todoist/api/v1/labels/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content.

### Comments API

#### List Comments

```bash
maton api '/todoist/api/v1/comments?task_id={task_id}'

maton api '/todoist/api/v1/comments?project_id={project_id}'
```

**Note:** `{task_id}` and `{project_id}` are placeholders. Replace each of them with real values before sending the request.

**Note:** Either `task_id` or `project_id` is required.

**Response:**
```json
{
  "results": [
    {
      "id": "6g424pWVXPpwW7hR",
      "item_id": "6g424pQr2xfCcFr2",
      "content": "This is a comment",
      "posted_at": "2026-02-20T22:25:20.045703Z",
      "posted_uid": "57402826",
      "file_attachment": null,
      "reactions": null
    }
  ],
  "next_cursor": null
}
```

#### Get Comment

```bash
maton api '/todoist/api/v1/comments/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Comment

```bash
maton api -X POST '/todoist/api/v1/comments' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "task_id": "9993408170",
  "content": "Don't forget to check the budget"
}
JSON
```

**Request body:**
- `content` (required) - Comment text
- `task_id` (required) OR `project_id` - Where to attach the comment

#### Update Comment

```bash
maton api -X POST '/todoist/api/v1/comments/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "content": "Updated comment text"
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Comment

```bash
maton api '/todoist/api/v1/comments/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content.

### Notes

- Task and Project IDs are strings
- Priority values: 1 (normal) to 4 (urgent)
- Use only one due date format per request: `due_string`, `due_date`, or `due_datetime`
- Comments require either `task_id` or `project_id`
- Close/reopen/delete operations return 204 No Content

### Resources

- [Todoist API v1 Documentation](https://developer.todoist.com/api/v1)
- [Filter Syntax](https://todoist.com/help/articles/introduction-to-filters)
- [Maton CLI Manual](https://cli.maton.ai/manual)
