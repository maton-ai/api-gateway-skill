# Clockify Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `clockify`
**Base URL proxied:** `api.clockify.me`

## API Path Pattern

```
/clockify/api/v1/{resource}
```

## Common Endpoints

### Get Current User
```bash
maton api '/clockify/api/v1/user'
```

### List Workspaces
```bash
maton api '/clockify/api/v1/workspaces'
```

### Get Workspace
```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}'
```

### List Workspace Users
```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/users'
```

### List Projects
```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/projects'
```

### Get Project
```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/projects/{projectId}'
```

### Create Project
```bash
maton api -X POST '/clockify/api/v1/workspaces/{workspaceId}/projects' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "My Project",
  "isPublic": true,
  "clientId": "optional-client-id"
}
EOF
```

### Update Project
```bash
maton api -X PUT '/clockify/api/v1/workspaces/{workspaceId}/projects/{projectId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Updated Project Name",
  "archived": true
}
EOF
```

### Delete Project
```bash
maton api -X DELETE '/clockify/api/v1/workspaces/{workspaceId}/projects/{projectId}'
```

### List Clients
```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/clients'
```

### Create Client
```bash
maton api -X POST '/clockify/api/v1/workspaces/{workspaceId}/clients' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Client Name",
  "address": "123 Main St",
  "note": "Client notes"
}
EOF
```

### List Tags
```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/tags'
```

### Create Tag
```bash
maton api -X POST '/clockify/api/v1/workspaces/{workspaceId}/tags' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "urgent"
}
EOF
```

### List Tasks on Project
```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/projects/{projectId}/tasks'
```

### Create Task
```bash
maton api -X POST '/clockify/api/v1/workspaces/{workspaceId}/projects/{projectId}/tasks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Task Name",
  "assigneeIds": ["user-id"],
  "estimate": "PT2H",
  "billable": true
}
EOF
```

### Get User's Time Entries
```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/user/{userId}/time-entries'
```

### Create Time Entry
```bash
maton api -X POST '/clockify/api/v1/workspaces/{workspaceId}/time-entries' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "start": "2026-02-13T09:00:00Z",
  "end": "2026-02-13T10:00:00Z",
  "description": "Working on task",
  "projectId": "project-id",
  "taskId": "task-id",
  "tagIds": ["tag-id"],
  "billable": true
}
EOF
```

### Get Time Entry
```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/time-entries/{timeEntryId}'
```

### Update Time Entry
```bash
maton api -X PUT '/clockify/api/v1/workspaces/{workspaceId}/time-entries/{timeEntryId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "description": "Updated description",
  "end": "2026-02-13T11:00:00Z"
}
EOF
```

### Delete Time Entry
```bash
maton api -X DELETE '/clockify/api/v1/workspaces/{workspaceId}/time-entries/{timeEntryId}'
```

### Stop Running Timer
```bash
maton api -X PATCH '/clockify/api/v1/workspaces/{workspaceId}/user/{userId}/time-entries' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "end": "2026-02-13T17:00:00Z"
}
EOF
```

## Notes

- All IDs are strings
- Timestamps must be in ISO 8601 format with UTC timezone (e.g., `2026-02-13T09:00:00Z`)
- Duration format uses ISO 8601 duration (e.g., `PT1H` for 1 hour, `PT30M` for 30 minutes)
- Cannot delete active projects or tasks - must archive them first
- Page-based pagination with `page` and `page-size` query parameters
- Response includes `Last-Page` header indicating if more pages exist
- Rate limit: 50 requests per second per workspace

## Resources

- [Clockify API Documentation](https://docs.clockify.me/)
- [Time Entry API](https://docs.clockify.me/#tag/Time-entry)
- [Project API](https://docs.clockify.me/#tag/Project)
- [Workspace API](https://docs.clockify.me/#tag/Workspace)
- [User API](https://docs.clockify.me/#tag/User)
