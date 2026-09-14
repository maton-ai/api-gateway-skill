# Zoho Projects

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `zoho-projects`
**Upstream base URL:** `projectsapi.zoho.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://projectsapi.zoho.com/api/v3/portals`
- Gateway: `https://api.maton.ai/zoho-projects/api/v3/portals`

**Important:** V3 uses `/api/v3/` prefix (not `/restapi/`) and no trailing slashes (trailing slashes return 400)

### Portals API

#### List Portals

```bash
maton api '/zoho-projects/api/v3/portals'
```

**Response:**
```json
[
  {
    "id": "916020774",
    "portal_name": "mycompany",
    "org_name": "mycompany",
    "timezone": "PST",
    "project_plan": "Free",
    "owner": {
      "zpuid": "2644874000000085003",
      "name": "John Doe",
      "email": "john@example.com"
    },
    "profile": {
      "name": "Portal Owner",
      "id": 2644874000000085084
    }
  }
]
```

### Projects API

#### List Projects

```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects'
```

**Note:** `{portal_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:** `page`, `per_page`, `status` (`active`, `archived`, `template`)

**Response:**
```json
[
  {
    "id": "2644874000000089119",
    "key": "NU-1",
    "name": "My Project",
    "project_type": "active",
    "description": "Project description",
    "owner": {
      "zpuid": "2644874000000085003",
      "name": "John Doe",
      "email": "john@example.com"
    },
    "is_public_project": false,
    "created_time": "2026-02-27T10:20:22.421Z",
    "modified_time": "2026-02-27T10:20:22.421Z"
  }
]
```

#### Get Project

```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}'
```

**Note:** `{portal_id}` and `{project_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Project

```bash
maton api -X POST '/zoho-projects/api/v3/portal/{portal_id}/projects' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Project",
  "description": "Project description"
}
JSON
```

**Note:** `{portal_id}` is a placeholder. Replace it with a real value before sending the request.

**Response (201):**
```json
{
  "id": "2644874000000096003",
  "key": "NU-2",
  "name": "New Project",
  "project_type": "active",
  "description": "Project description",
  "owner": {
    "zpuid": "2644874000000085003",
    "name": "John Doe"
  },
  "created_time": "2026-05-17T22:08:52.537Z"
}
```

#### Update Project

```bash
maton api -X PATCH '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Name",
  "description": "Updated description"
}
JSON
```

**Note:** `{portal_id}` and `{project_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Project

```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}' -X DELETE
```

**Note:** `{portal_id}` and `{project_id}` are placeholders. Replace each of them with real values before sending the request.

Returns 204 No Content on success.

### Tasks API

#### List Tasks

```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasks'
```

**Note:** `{portal_id}` and `{project_id}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:** `page`, `per_page`, `owner`, `status`, `priority`, `tasklist_id`, `sort_by`

**Response:**
```json
{
  "page_info": {
    "page": 1,
    "per_page": 100,
    "page_count": 3,
    "has_next_page": false
  },
  "tasks": [
    {
      "id": "2644874000000089247",
      "prefix": "EZ1-T1",
      "name": "Task 1",
      "status": {
        "id": "2644874000000016068",
        "name": "Open",
        "is_closed_type": false
      },
      "priority": "none",
      "project": {
        "id": "2644874000000089119",
        "name": "My Project"
      },
      "tasklist": {
        "id": "2644874000000089245",
        "name": "General"
      },
      "milestone": {
        "id": "2644874000000000073",
        "name": "None"
      }
    }
  ]
}
```

#### Get Task

```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasks/{task_id}'
```

**Note:** `{portal_id}`, `{project_id}` and `{task_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Task

```bash
maton api -X POST '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Task",
  "priority": "high",
  "description": "Task description",
  "tasklist_id": "{tasklist_id}"
}
JSON
```

**Note:** `{portal_id}`, `{project_id}` and `{tasklist_id}` are placeholders. Replace each of them with real values before sending the request.

Optional fields: `person_responsible`, `tasklist_id`, `start_date`, `end_date`, `priority`, `description`

**Response (201):** Returns the created task object.

#### Update Task

```bash
maton api -X PATCH '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasks/{task_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Task Name",
  "priority": "medium"
}
JSON
```

**Note:** `{portal_id}`, `{project_id}` and `{task_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Task

```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasks/{task_id}' -X DELETE
```

**Note:** `{portal_id}`, `{project_id}` and `{task_id}` are placeholders. Replace each of them with real values before sending the request.

Returns 204 No Content on success.

### Task Comments API

#### List Comments

```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasks/{task_id}/comments'
```

**Note:** `{portal_id}`, `{project_id}` and `{task_id}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "page_info": {
    "per_page": 100,
    "has_next_page": false,
    "count": 1,
    "page": 1
  },
  "comments": [
    {
      "id": "2644874000000094015",
      "comment": "This is a comment",
      "created_time": "2026-05-17T22:08:51.264Z",
      "created_by": {
        "zpuid": "2644874000000085003",
        "name": "John Doe"
      }
    }
  ]
}
```

#### Add Comment

```bash
maton api -X POST '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasks/{task_id}/comments' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "comment": "This is a comment"
}
JSON
```

**Note:** `{portal_id}`, `{project_id}` and `{task_id}` are placeholders. Replace each of them with real values before sending the request.

Note: The field name is `comment`, not `content`.

**Note:** The field name is `comment`, not `content`.

**Response (201):** Returns the created comment object.

#### Delete Comment

```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasks/{task_id}/comments/{comment_id}' -X DELETE
```

**Note:** `{portal_id}`, `{project_id}`, `{task_id}` and `{comment_id}` are placeholders. Replace each of them with real values before sending the request.

Returns 204 No Content on success.

### Tasklists API

#### List Tasklists

```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasklists'
```

**Note:** `{portal_id}` and `{project_id}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "page_info": {
    "page": 1,
    "per_page": 200,
    "page_count": 1,
    "has_next_page": false
  },
  "tasklists": [
    {
      "id": "2644874000000089245",
      "name": "General",
      "flag": "internal",
      "status": "active",
      "milestone": {
        "id": "2644874000000000073",
        "name": "None"
      },
      "created_time": "2026-02-27T10:20:24.426Z"
    }
  ]
}
```

#### Create Tasklist

```bash
maton api -X POST '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasklists' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Tasklist",
  "flag": "internal"
}
JSON
```

**Note:** `{portal_id}` and `{project_id}` are placeholders. Replace each of them with real values before sending the request.

Optional fields: `milestone_id`, `flag` (`internal` or `external`)

**Response (201):** Returns the created tasklist object.

#### Update Tasklist

```bash
maton api -X PATCH '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasklists/{tasklist_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Tasklist Name"
}
JSON
```

**Note:** `{portal_id}`, `{project_id}` and `{tasklist_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Tasklist

```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasklists/{tasklist_id}' -X DELETE
```

**Note:** `{portal_id}`, `{project_id}` and `{tasklist_id}` are placeholders. Replace each of them with real values before sending the request.

Returns 204 No Content on success.

### Milestones API

#### List Milestones

```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/milestones'
```

**Note:** `{portal_id}` and `{project_id}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "page_info": [
    {
      "per_page": 100,
      "has_next_page": false,
      "page": 1
    }
  ],
  "milestones": [
    {
      "id": "2644874000000096133",
      "name": "Phase 1",
      "start_date": "2026-05-17",
      "end_date": "2026-06-01",
      "flag": "internal",
      "owner": {
        "zpuid": "2644874000000085003",
        "name": "John Doe"
      },
      "created_time": "2026-05-17T22:09:13.771Z"
    }
  ]
}
```

#### Create Milestone

```bash
maton api -X POST '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/milestones' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Phase 1",
  "start_date": "06-01-2026",
  "end_date": "06-15-2026",
  "flag": "internal",
  "owner_zpuid": "{user_zpuid}"
}
JSON
```

**Note:** `{portal_id}`, `{project_id}` and `{user_zpuid}` are placeholders. Replace each of them with real values before sending the request.

**Note:** Date format for creating milestones is `MM-dd-yyyy`.

**Request body:**
- `name` (required)
- `start_date` (required)
- `end_date` (required)
- `flag` (required)
- `owner_zpuid` (required)

**Response (201):** Returns the created milestone object.

#### Update Milestone

```bash
maton api -X PATCH '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/milestones/{milestone_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Phase",
  "end_date": "06-20-2026"
}
JSON
```

**Note:** `{portal_id}`, `{project_id}` and `{milestone_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Milestone

```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/milestones/{milestone_id}' -X DELETE
```

**Note:** `{portal_id}`, `{project_id}` and `{milestone_id}` are placeholders. Replace each of them with real values before sending the request.

Returns 204 No Content on success.

### Users API

#### List Users

```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/users'
```

**Note:** `{portal_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "page_info": {
    "per_page": 100,
    "has_next_page": false,
    "count": 1,
    "page": 1
  },
  "users": [
    {
      "zpuid": "2644874000000085003",
      "name": "John Doe",
      "email": "john@example.com",
      "is_active": true,
      "role": {
        "name": "Administrator",
        "id": "2644874000000085005"
      },
      "added_time": "2026-02-27T10:19:11.719Z"
    }
  ]
}
```

### Pagination

Page-based pagination with `page` and `per_page` parameters:
```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasks?page=1&per_page=50'
```

**Note:** `{portal_id}` and `{project_id}` are placeholders. Replace each of them with real values before sending the request.

Response includes `page_info`:
```json
{
  "page_info": {
    "page": 1,
    "per_page": 50,
    "has_next_page": true
  },
  "tasks": [...]
}
```

When `has_next_page` is `true`, increment `page` to get the next batch.

### Notes

- All POST/PATCH requests use `application/json` (not form-urlencoded)
- Updates use PATCH method (not POST)
- Portal ID is required for most endpoints
- Date format for milestones: `MM-dd-yyyy`
- Delete operations return 204 No Content
- Create operations return 201 Created

### Resources

- [Zoho Projects API V3 Documentation](https://projects.zoho.com/api-docs)
- [Zoho Projects Developer Portal](https://www.zoho.com/projects/help/rest-api/zohoprojectsapi.html)
- [Maton CLI Manual](https://cli.maton.ai/manual)
