# Toggl Track Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `toggl-track`
**Base URL proxied:** `api.track.toggl.com`

## API Path Pattern

```
/toggl-track/api/v9/{resource}
```

## Common Endpoints

### Get Current User
```bash
maton api '/toggl-track/api/v9/me'
```

### List Workspaces
```bash
maton api '/toggl-track/api/v9/me/workspaces'
```

### Get Workspace
```bash
maton api '/toggl-track/api/v9/workspaces/{workspace_id}'
```

### List Workspace Users
```bash
maton api '/toggl-track/api/v9/workspaces/{workspace_id}/users'
```

### List Time Entries
```bash
maton api '/toggl-track/api/v9/me/time_entries'
maton api '/toggl-track/api/v9/me/time_entries?start_date=2026-02-01&end_date=2026-02-28'
```

### Get Current Time Entry
```bash
maton api '/toggl-track/api/v9/me/time_entries/current'
```

### Create Time Entry
```bash
maton api -X POST '/toggl-track/api/v9/workspaces/{workspace_id}/time_entries' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "description": "Working on task",
  "start": "2026-02-13T10:00:00Z",
  "duration": -1,
  "workspace_id": 21180405,
  "created_with": "maton-api"
}
EOF
```

### Stop Time Entry
```bash
maton api -X PATCH '/toggl-track/api/v9/workspaces/{workspace_id}/time_entries/{time_entry_id}/stop'
```

### Update Time Entry
```bash
maton api -X PUT '/toggl-track/api/v9/workspaces/{workspace_id}/time_entries/{time_entry_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "description": "Updated description"
}
EOF
```

### Delete Time Entry
```bash
maton api -X DELETE '/toggl-track/api/v9/workspaces/{workspace_id}/time_entries/{time_entry_id}'
```

### List Projects
```bash
maton api '/toggl-track/api/v9/workspaces/{workspace_id}/projects'
maton api '/toggl-track/api/v9/workspaces/{workspace_id}/projects?active=true'
```

### Create Project
```bash
maton api -X POST '/toggl-track/api/v9/workspaces/{workspace_id}/projects' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "New Project",
  "active": true,
  "color": "#0b83d9"
}
EOF
```

### Update Project
```bash
maton api -X PUT '/toggl-track/api/v9/workspaces/{workspace_id}/projects/{project_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Updated Project"
}
EOF
```

### Delete Project
```bash
maton api -X DELETE '/toggl-track/api/v9/workspaces/{workspace_id}/projects/{project_id}'
```

### List Clients
```bash
maton api '/toggl-track/api/v9/workspaces/{workspace_id}/clients'
```

### Create Client
```bash
maton api -X POST '/toggl-track/api/v9/workspaces/{workspace_id}/clients' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "New Client"
}
EOF
```

### Update Client
```bash
maton api -X PUT '/toggl-track/api/v9/workspaces/{workspace_id}/clients/{client_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Updated Client"
}
EOF
```

### Delete Client
```bash
maton api -X DELETE '/toggl-track/api/v9/workspaces/{workspace_id}/clients/{client_id}'
```

### List Tags
```bash
maton api '/toggl-track/api/v9/workspaces/{workspace_id}/tags'
```

### Create Tag
```bash
maton api -X POST '/toggl-track/api/v9/workspaces/{workspace_id}/tags' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "New Tag"
}
EOF
```

### Update Tag
```bash
maton api -X PUT '/toggl-track/api/v9/workspaces/{workspace_id}/tags/{tag_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Updated Tag"
}
EOF
```

### Delete Tag
```bash
maton api -X DELETE '/toggl-track/api/v9/workspaces/{workspace_id}/tags/{tag_id}'
```

## Notes

- Workspace IDs and time entry IDs are integers
- Duration is in seconds; use `-1` to start a running timer
- Timestamps use ISO 8601 format (e.g., `2026-02-13T19:58:43Z`)
- The `created_with` field is required when creating time entries
- Pagination uses `page` and `per_page` query parameters
- Time entries list supports `since`, `start_date`, and `end_date` filters

## Resources

- [Toggl Track API Documentation](https://engineering.toggl.com/docs/)
- [Time Entries API](https://engineering.toggl.com/docs/api/time_entries)
- [Projects API](https://engineering.toggl.com/docs/api/projects)
- [Clients API](https://engineering.toggl.com/docs/api/clients)
- [Tags API](https://engineering.toggl.com/docs/api/tags)
