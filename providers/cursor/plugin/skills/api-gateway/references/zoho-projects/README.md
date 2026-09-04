# Zoho Projects Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `zoho-projects`
**Base URL proxied:** `projectsapi.zoho.com`

## API Path Pattern

```
/zoho-projects/api/v3/portals
/zoho-projects/api/v3/portal/{portal_id}/projects
/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasks
/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasklists
/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/milestones
/zoho-projects/api/v3/portal/{portal_id}/users
```

## Important Notes

- V3 uses `/api/v3/` prefix (not `/restapi/`)
- No trailing slashes — trailing slashes return 400
- All POST/PATCH requests use `application/json` (not form-urlencoded)
- Updates use PATCH method (not POST)
- Portal ID is required for most endpoints
- Date format for milestones: `MM-dd-yyyy`
- Delete operations return 204 No Content
- Create operations return 201 Created

## Common Endpoints

### Portals

#### List Portals
```bash
maton api '/zoho-projects/api/v3/portals'
```

### Projects

#### List Projects
```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects'
```

#### Get Project
```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}'
```

#### Create Project
```bash
maton api -X POST '/zoho-projects/api/v3/portal/{portal_id}/projects' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "New Project",
  "description": "Project description"
}
EOF
```

#### Update Project
```bash
maton api -X PATCH '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Updated Name"
}
EOF
```

#### Delete Project
```bash
maton api -X DELETE '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}'
```

### Tasks

#### List Tasks
```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasks'
```

#### Get Task
```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasks/{task_id}'
```

#### Create Task
```bash
maton api -X POST '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "New Task",
  "priority": "high"
}
EOF
```

#### Update Task
```bash
maton api -X PATCH '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasks/{task_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Updated Name",
  "priority": "medium"
}
EOF
```

#### Delete Task
```bash
maton api -X DELETE '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasks/{task_id}'
```

### Task Comments

#### List Comments
```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasks/{task_id}/comments'
```

#### Add Comment
```bash
maton api -X POST '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasks/{task_id}/comments' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "comment": "Comment text"
}
EOF
```

Note: The field name is `comment`, not `content`.

#### Delete Comment
```bash
maton api -X DELETE '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasks/{task_id}/comments/{comment_id}'
```

### Tasklists

#### List Tasklists
```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasklists'
```

#### Create Tasklist
```bash
maton api -X POST '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasklists' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "New Tasklist",
  "flag": "internal"
}
EOF
```

#### Update Tasklist
```bash
maton api -X PATCH '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasklists/{tasklist_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Updated Name"
}
EOF
```

#### Delete Tasklist
```bash
maton api -X DELETE '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasklists/{tasklist_id}'
```

### Milestones

#### List Milestones
```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/milestones'
```

#### Create Milestone
```bash
maton api -X POST '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/milestones' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Phase 1",
  "start_date": "06-01-2026",
  "end_date": "06-15-2026",
  "flag": "internal",
  "owner_zpuid": "{user_zpuid}"
}
EOF
```

Required fields: `name`, `start_date`, `end_date`, `flag`, `owner_zpuid`

#### Update Milestone
```bash
maton api -X PATCH '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/milestones/{milestone_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Updated Phase"
}
EOF
```

#### Delete Milestone
```bash
maton api -X DELETE '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/milestones/{milestone_id}'
```

### Users

#### List Users
```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/users'
```

## Pagination

Page-based pagination with `page` and `per_page` parameters:
```bash
maton api '/zoho-projects/api/v3/portal/{portal_id}/projects/{project_id}/tasks?page=1&per_page=50'
```

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

## Resources

- [Zoho Projects API V3 Documentation](https://projects.zoho.com/api-docs)
- [Zoho Projects Developer Portal](https://www.zoho.com/projects/help/rest-api/zohoprojectsapi.html)
