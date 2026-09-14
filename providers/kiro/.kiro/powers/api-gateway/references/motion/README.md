# Motion

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `motion`
**Upstream base URL:** `api.usemotion.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.usemotion.com/v1/tasks`
- Gateway: `https://api.maton.ai/motion/v1/tasks`

### Tasks API

#### List Tasks

```bash
maton api '/motion/v1/tasks'
```

**Query parameters:**
- `workspaceId` (string) - Filter by workspace
- `projectId` (string) - Filter by project
- `assigneeId` (string) - Filter by assignee
- `status` (array) - Filter by status (cannot combine with `includeAllStatuses`)
- `includeAllStatuses` (boolean) - Return tasks across all statuses
- `label` (string) - Filter by label
- `name` (string) - Search task names (case-insensitive)
- `cursor` (string) - Pagination cursor

**Example:**
```bash
maton api '/motion/v1/tasks?workspaceId=WORKSPACE_ID'
```

#### Get Task

```bash
maton api '/motion/v1/tasks/{taskId}'
```

**Note:** `{taskId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Task

```bash
maton api -X POST '/motion/v1/tasks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Task name",
  "workspaceId": "WORKSPACE_ID",
  "dueDate": "2024-03-15T10:00:00Z",
  "duration": 60,
  "priority": "HIGH",
  "description": "Task description in markdown",
  "projectId": "PROJECT_ID",
  "assigneeId": "USER_ID",
  "labels": ["label1", "label2"],
  "autoScheduled": {
    "startDate": "2024-03-14T09:00:00Z",
    "deadlineType": "SOFT",
    "schedule": "Work Hours"
  }
}
JSON
```

**Request body:**
- `name` (string, required) - Task title
- `workspaceId` (string, required) - Workspace ID
- `dueDate` (datetime, ISO 8601, optional) - Task deadline (required for scheduled tasks)
- `duration` (string | number, optional) - "NONE", "REMINDER", or minutes (integer > 0)
- `status` (string, optional) - Defaults to workspace default status
- `projectId` (string, optional) - Associated project
- `description` (string, optional) - GitHub Flavored Markdown supported
- `priority` (string, optional) - ASAP, HIGH, MEDIUM, or LOW
- `labels` (array, optional) - Label names to add
- `assigneeId` (string, optional) - User ID for task assignment
- `autoScheduled` (object, optional) - Auto-scheduling settings with `startDate`, `deadlineType` (HARD, SOFT, NONE), and `schedule`

**Example:**
```bash
maton api -X POST '/motion/v1/tasks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Task name",
  "workspaceId": "WORKSPACE_ID",
  "dueDate": "2024-03-15T10:00:00Z",
  "duration": 60,
  "priority": "HIGH",
  "description": "Task description in markdown",
  "projectId": "PROJECT_ID",
  "assigneeId": "USER_ID",
  "labels": ["label1", "label2"],
  "autoScheduled": {
    "startDate": "2024-03-14T09:00:00Z",
    "deadlineType": "SOFT",
    "schedule": "Work Hours"
  }
}
JSON
```

#### Update Task

```bash
maton api -X PATCH '/motion/v1/tasks/{taskId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated task name",
  "status": "Completed",
  "priority": "LOW"
}
JSON
```

**Note:** `{taskId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Task

```bash
maton api '/motion/v1/tasks/{taskId}' -X DELETE
```

**Note:** `{taskId}` is a placeholder. Replace it with a real value before sending the request.

#### Move Task

```bash
maton api -X POST '/motion/v1/tasks/{taskId}/move' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "workspaceId": "NEW_WORKSPACE_ID"
}
JSON
```

**Note:** `{taskId}` is a placeholder. Replace it with a real value before sending the request.

#### Unassign Task

```bash
maton api -X POST '/motion/v1/tasks/{taskId}/unassign'
```

**Note:** `{taskId}` is a placeholder. Replace it with a real value before sending the request.

#### List Projects

```bash
maton api '/motion/v1/projects?workspaceId={workspaceId}'
```

**Note:** `{workspaceId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `workspaceId` (required, string) - Workspace ID
- `cursor` (string) - Pagination cursor

#### Get Project

```bash
maton api '/motion/v1/projects/{projectId}'
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Project

```bash
maton api -X POST '/motion/v1/projects' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Project name",
  "workspaceId": "WORKSPACE_ID",
  "description": "Project description",
  "dueDate": "2024-06-30T00:00:00Z",
  "priority": "HIGH",
  "labels": ["label1"]
}
JSON
```

**Request body:**
- `name` (string, required) - Project name
- `workspaceId` (string, required) - Workspace ID
- `dueDate` (datetime, ISO 8601, optional) - Project deadline
- `description` (string, optional) - HTML input accepted
- `labels` (array, optional) - Label names
- `priority` (string, optional) - ASAP, HIGH, MEDIUM (default), or LOW
- `projectDefinitionId` (string, optional) - Template ID (requires `stages` array if provided)
- `stages` (array, optional) - Stage objects for project templates

### Workspaces API

#### List Workspaces

```bash
maton api '/motion/v1/workspaces'
```

### Users API

#### List Users

```bash
maton api '/motion/v1/users?workspaceId={workspaceId}'
```

**Note:** `{workspaceId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `workspaceId` (string) - Workspace ID (required if no teamId)
- `teamId` (string) - Team ID (required if no workspaceId)

#### Get Current User

```bash
maton api '/motion/v1/users/me'
```

### Comments API

#### List Comments

```bash
maton api '/motion/v1/comments?taskId={taskId}'
```

**Note:** `{taskId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `taskId` (string, **required**) - Filter comments by task
- `cursor` (string) - Pagination cursor

#### Create Comment

```bash
maton api -X POST '/motion/v1/comments' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "taskId": "TASK_ID",
  "content": "Comment in GitHub Flavored Markdown"
}
JSON
```

**Request body:**
- `taskId` (string, required) - Task to comment on
- `content` (string, optional) - Comment content in GitHub Flavored Markdown

### Recurring Tasks API

#### List Recurring Tasks

```bash
maton api '/motion/v1/recurring-tasks?workspaceId={workspaceId}'
```

**Note:** `{workspaceId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `workspaceId` (string, **required**) - Filter by workspace
- `cursor` (string) - Pagination cursor

#### Create Recurring Task

```bash
maton api -X POST '/motion/v1/recurring-tasks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Weekly review",
  "workspaceId": "WORKSPACE_ID",
  "frequency": "weekly"
}
JSON
```

#### Delete Recurring Task

```bash
maton api '/motion/v1/recurring-tasks/{recurringTaskId}' -X DELETE
```

**Note:** `{recurringTaskId}` is a placeholder. Replace it with a real value before sending the request.

### Schedules API

#### List Schedules

```bash
maton api '/motion/v1/schedules'
```

### Statuses API

#### List Statuses

```bash
maton api '/motion/v1/statuses?workspaceId={workspaceId}'
```

**Note:** `{workspaceId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `workspaceId` (string, **required**) - Filter by workspace

### Custom Fields API

#### List Custom Fields

```bash
maton api '/motion/v1/custom-fields'
```

#### Create Custom Field

```bash
maton api -X POST '/motion/v1/custom-fields' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Field name",
  "type": "text"
}
JSON
```

#### Delete Custom Field

```bash
maton api '/motion/v1/custom-fields/{customFieldId}' -X DELETE
```

**Note:** `{customFieldId}` is a placeholder. Replace it with a real value before sending the request.

#### Add Custom Field to Project

```bash
maton api -X POST '/motion/v1/custom-fields/{customFieldId}/project' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "projectId": "PROJECT_ID"
}
JSON
```

**Note:** `{customFieldId}` is a placeholder. Replace it with a real value before sending the request.

#### Add Custom Field to Task

```bash
maton api -X POST '/motion/v1/custom-fields/{customFieldId}/task' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "taskId": "TASK_ID"
}
JSON
```

**Note:** `{customFieldId}` is a placeholder. Replace it with a real value before sending the request.

#### Remove Custom Field from Project

```bash
maton api '/motion/v1/custom-fields/{customFieldId}/project' -X DELETE
```

**Note:** `{customFieldId}` is a placeholder. Replace it with a real value before sending the request.

#### Remove Custom Field from Task

```bash
maton api '/motion/v1/custom-fields/{customFieldId}/task' -X DELETE
```

**Note:** `{customFieldId}` is a placeholder. Replace it with a real value before sending the request.

### Notes

- Workspace IDs start with `ws_`
- Task IDs start with `tk_`
- Project IDs start with `pr_`
- Timestamps are in ISO 8601 format
- Priority values: ASAP, HIGH, MEDIUM, LOW
- Deadline types: HARD, SOFT, NONE
- Cursor-based pagination with `cursor` query parameter
- `workspaceId` is required for listing projects, users, recurring tasks, and statuses

### Resources

- [Motion API Documentation](https://docs.usemotion.com/)
- [Motion API Reference](https://docs.usemotion.com/api-reference)
- [Cookbooks](https://docs.usemotion.com/cookbooks/getting-started)
- [Maton CLI Manual](https://cli.maton.ai/manual)
