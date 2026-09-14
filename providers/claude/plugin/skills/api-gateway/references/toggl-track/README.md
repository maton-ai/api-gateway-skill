# Toggl Track

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `toggl-track`
**Upstream base URL:** `api.track.toggl.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.track.toggl.com/api/v9/me`
- Gateway: `https://api.maton.ai/toggl-track/api/v9/me`

### User Info API

#### Get Current User

```bash
maton api '/toggl-track/api/v9/me'
```

**Response:**
```json
{
  "id": 12932942,
  "email": "user@example.com",
  "fullname": "John Doe",
  "timezone": "America/Los_Angeles",
  "default_workspace_id": 21180405,
  "beginning_of_week": 1,
  "image_url": "https://assets.track.toggl.com/images/profile.png"
}
```

#### List Workspaces

```bash
maton api '/toggl-track/api/v9/me/workspaces'
```

#### Get Workspace

```bash
maton api '/toggl-track/api/v9/workspaces/{workspace_id}'
```

**Note:** `{workspace_id}` is a placeholder. Replace it with a real value before sending the request.

#### List Workspace Users

```bash
maton api '/toggl-track/api/v9/workspaces/{workspace_id}/users'
```

**Note:** `{workspace_id}` is a placeholder. Replace it with a real value before sending the request.

### Time Entries API

#### List Time Entries

```bash
maton api '/toggl-track/api/v9/me/time_entries'
```

**Query parameters:**
- `since` (integer) - UNIX timestamp for entries modified after this time
- `before` (string) - Get entries before this date (RFC3339 or YYYY-MM-DD)
- `start_date` (string) - Filter start date (YYYY-MM-DD)
- `end_date` (string) - Filter end date (YYYY-MM-DD)

#### Get Current Time Entry

```bash
maton api '/toggl-track/api/v9/me/time_entries/current'
```

Returns `null` if no time entry is currently running.

#### Get Time Entry by ID

```bash
maton api '/toggl-track/api/v9/me/time_entries/{time_entry_id}'
```

**Note:** `{time_entry_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Time Entry

```bash
maton api -X POST '/toggl-track/api/v9/workspaces/{workspace_id}/time_entries' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "description": "Working on project",
  "start": "2026-02-13T10:00:00Z",
  "duration": -1,
  "workspace_id": 21180405,
  "project_id": 216896134,
  "tag_ids": [20053808],
  "created_with": "maton-api"
}
JSON
```

**Note:** `{workspace_id}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Set `duration` to `-1` to start a running timer. The `created_with` field is required.

**Response:**
```json
{
  "id": 4290254971,
  "workspace_id": 21180405,
  "project_id": null,
  "task_id": null,
  "billable": false,
  "start": "2026-02-13T19:58:43Z",
  "stop": null,
  "duration": -1,
  "description": "Working on project",
  "tags": null,
  "tag_ids": null,
  "user_id": 12932942
}
```

#### Update Time Entry

```bash
maton api -X PUT '/toggl-track/api/v9/workspaces/{workspace_id}/time_entries/{time_entry_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "description": "Updated description",
  "project_id": 216896134
}
JSON
```

**Note:** `{workspace_id}` and `{time_entry_id}` are placeholders. Replace each of them with real values before sending the request.

#### Stop Time Entry

```bash
maton api -X PATCH '/toggl-track/api/v9/workspaces/{workspace_id}/time_entries/{time_entry_id}/stop'
```

**Note:** `{workspace_id}` and `{time_entry_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Time Entry

```bash
maton api '/toggl-track/api/v9/workspaces/{workspace_id}/time_entries/{time_entry_id}' -X DELETE
```

**Note:** `{workspace_id}` and `{time_entry_id}` are placeholders. Replace each of them with real values before sending the request.

### Projects API

#### List Projects

```bash
maton api '/toggl-track/api/v9/workspaces/{workspace_id}/projects'
```

**Note:** `{workspace_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `active` (boolean) - Filter by active status
- `since` (integer) - UNIX timestamp for modification filter
- `name` (string) - Filter by project name
- `page` (integer) - Page number
- `per_page` (integer) - Items per page (max 200)

#### Get Project

```bash
maton api '/toggl-track/api/v9/workspaces/{workspace_id}/projects/{project_id}'
```

**Note:** `{workspace_id}` and `{project_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Project

```bash
maton api -X POST '/toggl-track/api/v9/workspaces/{workspace_id}/projects' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Project",
  "active": true,
  "is_private": true,
  "client_id": 68493239,
  "color": "#0b83d9",
  "billable": true
}
JSON
```

**Note:** `{workspace_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": 216896134,
  "workspace_id": 21180405,
  "client_id": null,
  "name": "New Project",
  "is_private": true,
  "active": true,
  "color": "#0b83d9",
  "billable": true,
  "created_at": "2026-02-13T19:58:36+00:00"
}
```

#### Update Project

```bash
maton api -X PUT '/toggl-track/api/v9/workspaces/{workspace_id}/projects/{project_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Project Name",
  "color": "#ff0000"
}
JSON
```

**Note:** `{workspace_id}` and `{project_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Project

```bash
maton api '/toggl-track/api/v9/workspaces/{workspace_id}/projects/{project_id}' -X DELETE
```

**Note:** `{workspace_id}` and `{project_id}` are placeholders. Replace each of them with real values before sending the request.

### Clients API

#### List Clients

```bash
maton api '/toggl-track/api/v9/workspaces/{workspace_id}/clients'
```

**Note:** `{workspace_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `status` (string) - Filter: `active`, `archived`, or `both`
- `name` (string) - Case-insensitive name filter

#### Get Client

```bash
maton api '/toggl-track/api/v9/workspaces/{workspace_id}/clients/{client_id}'
```

**Note:** `{workspace_id}` and `{client_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Client

```bash
maton api -X POST '/toggl-track/api/v9/workspaces/{workspace_id}/clients' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Client",
  "notes": "Client notes here"
}
JSON
```

**Note:** `{workspace_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": 68493239,
  "wid": 21180405,
  "archived": false,
  "name": "New Client",
  "at": "2026-02-13T19:58:36+00:00",
  "creator_id": 12932942
}
```

#### Update Client

```bash
maton api -X PUT '/toggl-track/api/v9/workspaces/{workspace_id}/clients/{client_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Client Name"
}
JSON
```

**Note:** `{workspace_id}` and `{client_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Client

```bash
maton api '/toggl-track/api/v9/workspaces/{workspace_id}/clients/{client_id}' -X DELETE
```

**Note:** `{workspace_id}` and `{client_id}` are placeholders. Replace each of them with real values before sending the request.

#### Archive Client

```bash
maton api -X POST '/toggl-track/api/v9/workspaces/{workspace_id}/clients/{client_id}/archive'
```

**Note:** `{workspace_id}` and `{client_id}` are placeholders. Replace each of them with real values before sending the request.

#### Restore Client

```bash
maton api -X POST '/toggl-track/api/v9/workspaces/{workspace_id}/clients/{client_id}/restore' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "restore_all_projects": true
}
JSON
```

**Note:** `{workspace_id}` and `{client_id}` are placeholders. Replace each of them with real values before sending the request.

### Tags API

#### List Tags

```bash
maton api '/toggl-track/api/v9/workspaces/{workspace_id}/tags'
```

**Note:** `{workspace_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `page` (integer) - Page number
- `per_page` (integer) - Items per page

#### Create Tag

```bash
maton api -X POST '/toggl-track/api/v9/workspaces/{workspace_id}/tags' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Tag"
}
JSON
```

**Note:** `{workspace_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": 20053808,
  "workspace_id": 21180405,
  "name": "New Tag",
  "at": "2026-02-13T19:58:37.115714Z",
  "creator_id": 12932942
}
```

#### Update Tag

```bash
maton api -X PUT '/toggl-track/api/v9/workspaces/{workspace_id}/tags/{tag_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Tag Name"
}
JSON
```

**Note:** `{workspace_id}` and `{tag_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Tag

```bash
maton api '/toggl-track/api/v9/workspaces/{workspace_id}/tags/{tag_id}' -X DELETE
```

**Note:** `{workspace_id}` and `{tag_id}` are placeholders. Replace each of them with real values before sending the request.

### Notes

- Workspace IDs and time entry IDs are integers
- Duration is in seconds; use `-1` to start a running timer
- Timestamps use ISO 8601 format (e.g., `2026-02-13T19:58:43Z`)
- The `created_with` field is required when creating time entries
- Pagination uses `page` and `per_page` query parameters
- Time entries list supports `since`, `start_date`, and `end_date` filters

### Resources

- [Toggl Track API Documentation](https://engineering.toggl.com/docs/)
- [Time Entries API](https://engineering.toggl.com/docs/api/time_entries)
- [Projects API](https://engineering.toggl.com/docs/api/projects)
- [Clients API](https://engineering.toggl.com/docs/api/clients)
- [Tags API](https://engineering.toggl.com/docs/api/tags)
- [Maton CLI Manual](https://cli.maton.ai/manual)
