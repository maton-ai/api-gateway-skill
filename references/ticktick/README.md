# TickTick Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `ticktick`
**Base URL proxied:** `api.ticktick.com`

## API Path Pattern

```
/ticktick/open/v1/{resource}
```

## Common Endpoints

### List Projects
```bash
maton api '/ticktick/open/v1/project'
```

### Get Project with Tasks
```bash
maton api '/ticktick/open/v1/project/{projectId}/data'
```

Returns project details along with tasks and columns.

### Create Project
```bash
maton api -X POST '/ticktick/open/v1/project' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "My Project",
  "viewMode": "list"
}
EOF
```

**viewMode options:** `list`, `kanban`, `timeline`

### Delete Project
```bash
maton api -X DELETE '/ticktick/open/v1/project/{projectId}'
```

### Get Task
```bash
maton api '/ticktick/open/v1/project/{projectId}/task/{taskId}'
```

### Create Task
```bash
maton api -X POST '/ticktick/open/v1/task' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "New task",
  "projectId": "PROJECT_ID",
  "content": "Task description",
  "priority": 0,
  "dueDate": "2026-02-15T10:00:00+0000",
  "isAllDay": false
}
EOF
```

**Priority values:** 0=None, 1=Low, 3=Medium, 5=High

### Update Task
```bash
maton api -X POST '/ticktick/open/v1/task/{taskId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "id": "TASK_ID",
  "projectId": "PROJECT_ID",
  "title": "Updated title",
  "priority": 1
}
EOF
```

### Complete Task
```bash
maton api -X POST '/ticktick/open/v1/project/{projectId}/task/{taskId}/complete'
```

### Delete Task
```bash
maton api -X DELETE '/ticktick/open/v1/project/{projectId}/task/{taskId}'
```

## Task Fields

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

## Notes

- The Open API provides access to tasks and projects only
- Habits, focus/pomodoro, and tags endpoints are not available through the Open API
- Task `status` values: 0 = Active, 2 = Completed
- Dates use ISO 8601 format with timezone offset (e.g., `2026-02-15T10:00:00+0000`)
- The `columns` field in project data is used for Kanban board columns

## Resources

- [TickTick Developer Portal](https://developer.ticktick.com/)
- [TickTick Help Center](https://help.ticktick.com/)
