# Motion Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `motion`
**Base URL proxied:** `api.usemotion.com`

## API Path Pattern

```
/motion/v1/{resource}
```

## Common Endpoints

### Get Current User
```bash
maton api '/motion/v1/users/me'
```

### List Workspaces
```bash
maton api '/motion/v1/workspaces'
```

### List Tasks
```bash
maton api '/motion/v1/tasks'
maton api '/motion/v1/tasks?workspaceId={workspaceId}'
maton api '/motion/v1/tasks?projectId={projectId}'
```

### Get Task
```bash
maton api '/motion/v1/tasks/{taskId}'
```

### Create Task
```bash
maton api -X POST '/motion/v1/tasks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Task name",
  "workspaceId": "ws_xxx",
  "priority": "HIGH",
  "duration": 30
}
EOF
```

### Update Task
```bash
maton api -X PATCH '/motion/v1/tasks/{taskId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Updated name",
  "priority": "LOW"
}
EOF
```

### Delete Task
```bash
maton api -X DELETE '/motion/v1/tasks/{taskId}'
```

### Move Task
```bash
maton api -X POST '/motion/v1/tasks/{taskId}/move' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "workspaceId": "ws_new"
}
EOF
```

### Unassign Task
```bash
maton api -X POST '/motion/v1/tasks/{taskId}/unassign'
```

### List Projects
```bash
maton api '/motion/v1/projects?workspaceId={workspaceId}'
```

### Get Project
```bash
maton api '/motion/v1/projects/{projectId}'
```

### Create Project
```bash
maton api -X POST '/motion/v1/projects' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Project name",
  "workspaceId": "ws_xxx",
  "priority": "HIGH"
}
EOF
```

### List Users
```bash
maton api '/motion/v1/users?workspaceId={workspaceId}'
```

### List Comments
```bash
maton api '/motion/v1/comments?taskId={taskId}'
```

### Create Comment
```bash
maton api -X POST '/motion/v1/comments' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "taskId": "tk_xxx",
  "content": "Comment text"
}
EOF
```

### List Recurring Tasks
```bash
maton api '/motion/v1/recurring-tasks?workspaceId={workspaceId}'
```

### Create Recurring Task
```bash
maton api -X POST '/motion/v1/recurring-tasks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Weekly review",
  "workspaceId": "ws_xxx",
  "frequency": "weekly"
}
EOF
```

### Delete Recurring Task
```bash
maton api -X DELETE '/motion/v1/recurring-tasks/{recurringTaskId}'
```

### List Schedules
```bash
maton api '/motion/v1/schedules'
```

### List Statuses
```bash
maton api '/motion/v1/statuses?workspaceId={workspaceId}'
```

## Notes

- Workspace IDs start with `ws_`
- Task IDs start with `tk_`
- Project IDs start with `pr_`
- Timestamps are in ISO 8601 format
- Priority values: ASAP, HIGH, MEDIUM, LOW
- Deadline types: HARD, SOFT, NONE
- Cursor-based pagination with `cursor` query parameter
- `workspaceId` is required for listing projects, users, recurring tasks, and statuses

## Resources

- [Motion API Documentation](https://docs.usemotion.com/)
- [Motion API Reference](https://docs.usemotion.com/api-reference)
- [Motion Cookbooks](https://docs.usemotion.com/cookbooks/getting-started)
