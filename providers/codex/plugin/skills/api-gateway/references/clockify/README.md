# Clockify

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `clockify`
**Upstream base URL:** `api.clockify.me`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.clockify.me/api/v1/user`
- Gateway: `https://api.maton.ai/clockify/api/v1/user`

### User API

#### Get Current User

```bash
maton api '/clockify/api/v1/user'
```

**Response:**
```json
{
  "id": "698eeb9f5cd3a921db12069f",
  "email": "user@example.com",
  "name": "John Doe",
  "activeWorkspace": "698eeb9e5cd3a921db120693",
  "defaultWorkspace": "698eeb9e5cd3a921db120693",
  "status": "ACTIVE"
}
```

### Workspace API

#### List Workspaces

```bash
maton api '/clockify/api/v1/workspaces'
```

#### Get Workspace

```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}'
```

**Note:** `{workspaceId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Workspace

```bash
maton api -X POST '/clockify/api/v1/workspaces' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "My Workspace"
}
JSON
```

#### List Workspace Users

```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/users'
```

**Note:** `{workspaceId}` is a placeholder. Replace it with a real value before sending the request.

### Project API

#### List Projects

Clockify uses page-based pagination:

```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/projects?page=1&page-size=50'
```

**Note:** `{workspaceId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `page` - Page number (1-indexed, default: 1)
- `page-size` - Items per page (default varies by endpoint)

Response includes a `Last-Page` header indicating if there are more pages.

#### Get Project

```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/projects/{projectId}'
```

**Note:** `{workspaceId}` and `{projectId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Project

```bash
maton api -X POST '/clockify/api/v1/workspaces/{workspaceId}/projects' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "My Project",
  "isPublic": true,
  "clientId": "optional-client-id"
}
JSON
```

**Note:** `{workspaceId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": "698f7cba4f748f6209ea8995",
  "name": "My Project",
  "clientId": "",
  "workspaceId": "698eeb9e5cd3a921db120693",
  "billable": true,
  "color": "#1976D2",
  "archived": false,
  "public": true
}
```

#### Update Project

```bash
maton api -X PUT '/clockify/api/v1/workspaces/{workspaceId}/projects/{projectId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Project Name",
  "archived": true
}
JSON
```

**Note:** `{workspaceId}` and `{projectId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Project

```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/projects/{projectId}' -X DELETE
```

**Note:** `{workspaceId}` and `{projectId}` are placeholders. Replace each of them with real values before sending the request.

**Note:** You cannot delete active projects. Set `archived: true` first.

### Client API

#### List Clients

```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/clients'
```

**Note:** `{workspaceId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Client

```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/clients/{clientId}'
```

**Note:** `{workspaceId}` and `{clientId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Client

```bash
maton api -X POST '/clockify/api/v1/workspaces/{workspaceId}/clients' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Acme Corp",
  "address": "123 Main St",
  "note": "Important client"
}
JSON
```

**Note:** `{workspaceId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": "698f7cba0705b7d880830262",
  "name": "Acme Corp",
  "workspaceId": "698eeb9e5cd3a921db120693",
  "archived": false,
  "address": "123 Main St",
  "note": "Important client"
}
```

#### Update Client

```bash
maton api -X PUT '/clockify/api/v1/workspaces/{workspaceId}/clients/{clientId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Acme Corporation"
}
JSON
```

**Note:** `{workspaceId}` and `{clientId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Client

```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/clients/{clientId}' -X DELETE
```

**Note:** `{workspaceId}` and `{clientId}` are placeholders. Replace each of them with real values before sending the request.

### Tag API

#### List Tags

```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/tags'
```

**Note:** `{workspaceId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Tag

```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/tags/{tagId}'
```

**Note:** `{workspaceId}` and `{tagId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Tag

```bash
maton api -X POST '/clockify/api/v1/workspaces/{workspaceId}/tags' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "urgent"
}
JSON
```

**Note:** `{workspaceId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": "698f7cbbaa9e9f33e5fc0126",
  "name": "urgent",
  "workspaceId": "698eeb9e5cd3a921db120693",
  "archived": false
}
```

#### Update Tag

```bash
maton api -X PUT '/clockify/api/v1/workspaces/{workspaceId}/tags/{tagId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "high-priority"
}
JSON
```

**Note:** `{workspaceId}` and `{tagId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Tag

```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/tags/{tagId}' -X DELETE
```

**Note:** `{workspaceId}` and `{tagId}` are placeholders. Replace each of them with real values before sending the request.

### Task API

#### List Tasks on Project

```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/projects/{projectId}/tasks'
```

**Note:** `{workspaceId}` and `{projectId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Task

```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/projects/{projectId}/tasks/{taskId}'
```

**Note:** `{workspaceId}`, `{projectId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Task

```bash
maton api -X POST '/clockify/api/v1/workspaces/{workspaceId}/projects/{projectId}/tasks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Implement feature",
  "assigneeIds": ["user-id-1"],
  "estimate": "PT2H",
  "billable": true
}
JSON
```

**Note:** `{workspaceId}` and `{projectId}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "id": "698f7cc4aa9e9f33e5fc017b",
  "name": "Implement feature",
  "projectId": "698f7cba4f748f6209ea8995",
  "assigneeIds": [],
  "estimate": "PT0S",
  "status": "ACTIVE",
  "billable": true
}
```

#### Update Task

```bash
maton api -X PUT '/clockify/api/v1/workspaces/{workspaceId}/projects/{projectId}/tasks/{taskId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated task name",
  "status": "DONE"
}
JSON
```

**Note:** `{workspaceId}`, `{projectId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Task

```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/projects/{projectId}/tasks/{taskId}' -X DELETE
```

**Note:** `{workspaceId}`, `{projectId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

**Note:** You cannot delete active tasks. Set `status: "DONE"` first.

### Time Entry API

#### Get User's Time Entries

```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/user/{userId}/time-entries'
```

**Note:** `{workspaceId}` and `{userId}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
[
  {
    "id": "698f7cc4aa9e9f33e5fc0180",
    "description": "Working on project",
    "userId": "698eeb9f5cd3a921db12069f",
    "billable": true,
    "projectId": "698f7cba4f748f6209ea8995",
    "taskId": null,
    "workspaceId": "698eeb9e5cd3a921db120693",
    "timeInterval": {
      "start": "2026-02-13T18:34:28Z",
      "end": "2026-02-13T19:34:28Z",
      "duration": "PT1H"
    }
  }
]
```

#### Create Time Entry

```bash
maton api -X POST '/clockify/api/v1/workspaces/{workspaceId}/time-entries' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "start": "2026-02-13T09:00:00Z",
  "end": "2026-02-13T10:00:00Z",
  "description": "Team meeting",
  "projectId": "project-id",
  "taskId": "task-id",
  "tagIds": ["tag-id-1", "tag-id-2"],
  "billable": true
}
JSON
```

**Note:** `{workspaceId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Time Entry for Another User

```bash
maton api -X POST '/clockify/api/v1/workspaces/{workspaceId}/user/{userId}/time-entries' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "start": "2026-02-13T09:00:00Z",
  "end": "2026-02-13T10:00:00Z",
  "description": "Team meeting"
}
JSON
```

**Note:** `{workspaceId}` and `{userId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Time Entry

```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/time-entries/{timeEntryId}'
```

**Note:** `{workspaceId}` and `{timeEntryId}` are placeholders. Replace each of them with real values before sending the request.

#### Update Time Entry

```bash
maton api -X PUT '/clockify/api/v1/workspaces/{workspaceId}/time-entries/{timeEntryId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "start": "2026-02-13T09:00:00Z",
  "end": "2026-02-13T11:00:00Z",
  "description": "Extended meeting"
}
JSON
```

**Note:** `{workspaceId}` and `{timeEntryId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Time Entry

```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/time-entries/{timeEntryId}' -X DELETE
```

**Note:** `{workspaceId}` and `{timeEntryId}` are placeholders. Replace each of them with real values before sending the request.

#### Stop Running Timer

```bash
maton api -X PATCH '/clockify/api/v1/workspaces/{workspaceId}/user/{userId}/time-entries' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "end": "2026-02-13T17:00:00Z"
}
JSON
```

**Note:** `{workspaceId}` and `{userId}` are placeholders. Replace each of them with real values before sending the request.

#### Get In-Progress Time Entries

```bash
maton api '/clockify/api/v1/workspaces/{workspaceId}/time-entries'
```

**Note:** `{workspaceId}` is a placeholder. Replace it with a real value before sending the request.

### Notes

- All IDs are strings
- Timestamps must be in ISO 8601 format with UTC timezone (e.g., `2026-02-13T09:00:00Z`)
- Duration format uses ISO 8601 duration (e.g., `PT1H` for 1 hour, `PT30M` for 30 minutes)
- Cannot delete active projects or tasks - must archive them first
- Page-based pagination with `page` and `page-size` query parameters
- Response includes `Last-Page` header indicating if more pages exist
- Rate limit: 50 requests per second per workspace

### Resources

- [Clockify API Documentation](https://docs.clockify.me/)
- [Time Entry API](https://docs.clockify.me/#tag/Time-entry)
- [Project API](https://docs.clockify.me/#tag/Project)
- [Workspace API](https://docs.clockify.me/#tag/Workspace)
- [User API](https://docs.clockify.me/#tag/User)
- [Maton CLI Manual](https://cli.maton.ai/manual)
