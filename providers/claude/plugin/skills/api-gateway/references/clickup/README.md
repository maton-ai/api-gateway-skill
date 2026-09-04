# ClickUp Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `clickup`
**Base URL proxied:** `api.clickup.com`

## API Path Pattern

```
/clickup/api/v2/{resource}
```

## ClickUp Hierarchy

Workspace (team) → Space → Folder → List → Task

## Common Endpoints

### Get Current User
```bash
maton api '/clickup/api/v2/user'
```

### Get Workspaces (Teams)
```bash
maton api '/clickup/api/v2/team'
```

### Get Spaces
```bash
maton api '/clickup/api/v2/team/{team_id}/space'
```

### Get Folders
```bash
maton api '/clickup/api/v2/space/{space_id}/folder'
```

### Get Lists
```bash
maton api '/clickup/api/v2/folder/{folder_id}/list'
```

### Get Folderless Lists
```bash
maton api '/clickup/api/v2/space/{space_id}/list'
```

### Get Tasks
```bash
maton api '/clickup/api/v2/list/{list_id}/task?include_closed=true'
```

### Get a Task
```bash
maton api '/clickup/api/v2/task/{task_id}'
```

### Create a Task
```bash
maton api -X POST '/clickup/api/v2/list/{list_id}/task' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Task name",
  "description": "Task description",
  "assignees": [123],
  "status": "to do",
  "priority": 2,
  "due_date": 1709251200000,
  "tags": ["api", "backend"]
}
EOF
```

### Update a Task
```bash
maton api -X PUT '/clickup/api/v2/task/{task_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "status": "complete",
  "priority": null
}
EOF
```

### Delete a Task
```bash
maton api -X DELETE '/clickup/api/v2/task/{task_id}'
```

### Get Filtered Team Tasks
```bash
maton api '/clickup/api/v2/team/{team_id}/task?statuses[]=to%20do&assignees[]=123'
```

### Create Space
```bash
maton api -X POST '/clickup/api/v2/team/{team_id}/space' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "New Space",
  "multiple_assignees": true
}
EOF
```

### Create Folder
```bash
maton api -X POST '/clickup/api/v2/space/{space_id}/folder' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"name": "New Folder"}
EOF
```

### Create List
```bash
maton api -X POST '/clickup/api/v2/folder/{folder_id}/list' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"name": "New List"}
EOF
```

### Create Webhook
```bash
maton api -X POST '/clickup/api/v2/team/{team_id}/webhook' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "endpoint": "https://example.com/webhook",
  "events": ["taskCreated", "taskUpdated", "taskDeleted"]
}
EOF
```

### Delete Webhook
```bash
maton api -X DELETE '/clickup/api/v2/webhook/{webhook_id}'
```

## Notes

- Task IDs are strings, timestamps are Unix milliseconds
- Priority values: 1=urgent, 2=high, 3=normal, 4=low, null=none
- Workspaces are called "teams" in the API
- Status values must match exact status names configured in the list
- Use page-based pagination with `page` parameter (0-indexed)
- Responses are limited to 100 items per page

## Resources

- [ClickUp API Overview](https://developer.clickup.com/docs/Getting%20Started.md)
- [Tasks](https://developer.clickup.com/reference/gettasks.md)
- [Spaces](https://developer.clickup.com/reference/getspaces.md)
- [Lists](https://developer.clickup.com/reference/getlists.md)
- [Webhooks](https://developer.clickup.com/reference/createwebhook.md)
- [Rate Limits](https://developer.clickup.com/docs/rate-limits.md)
- [LLM Reference](https://developer.clickup.com/llms.txt)
