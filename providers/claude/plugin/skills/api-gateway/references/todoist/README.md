# Todoist Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `todoist`
**Base URL proxied:** `api.todoist.com`

## API Path Pattern

```
/todoist/api/v1/{resource}
```

## Common Endpoints

### List Projects
```bash
maton api '/todoist/api/v1/projects'
```

### Get Project
```bash
maton api '/todoist/api/v1/projects/{id}'
```

### Create Project
```bash
maton api -X POST '/todoist/api/v1/projects' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "My Project",
  "color": "blue"
}
EOF
```

### Update Project
```bash
maton api -X POST '/todoist/api/v1/projects/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Updated Name"
}
EOF
```

### Delete Project
```bash
maton api -X DELETE '/todoist/api/v1/projects/{id}'
```

### List Tasks
```bash
maton api '/todoist/api/v1/tasks'
maton api '/todoist/api/v1/tasks?project_id={project_id}'
maton api '/todoist/api/v1/tasks?filter={filter}'
```

### Get Task
```bash
maton api '/todoist/api/v1/tasks/{id}'
```

### Create Task
```bash
maton api -X POST '/todoist/api/v1/tasks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "content": "Buy groceries",
  "priority": 2,
  "due_string": "tomorrow"
}
EOF
```

### Update Task
```bash
maton api -X POST '/todoist/api/v1/tasks/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "content": "Updated content",
  "priority": 4
}
EOF
```

### Close Task (Complete)
```bash
maton api -X POST '/todoist/api/v1/tasks/{id}/close'
```

### Reopen Task
```bash
maton api -X POST '/todoist/api/v1/tasks/{id}/reopen'
```

### Delete Task
```bash
maton api -X DELETE '/todoist/api/v1/tasks/{id}'
```

### List Sections
```bash
maton api '/todoist/api/v1/sections'
maton api '/todoist/api/v1/sections?project_id={project_id}'
```

### Create Section
```bash
maton api -X POST '/todoist/api/v1/sections' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "In Progress",
  "project_id": "123456"
}
EOF
```

### Delete Section
```bash
maton api -X DELETE '/todoist/api/v1/sections/{id}'
```

### List Labels
```bash
maton api '/todoist/api/v1/labels'
```

### Create Label
```bash
maton api -X POST '/todoist/api/v1/labels' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "urgent",
  "color": "red"
}
EOF
```

### Delete Label
```bash
maton api -X DELETE '/todoist/api/v1/labels/{id}'
```

### List Comments
```bash
maton api '/todoist/api/v1/comments?task_id={task_id}'
maton api '/todoist/api/v1/comments?project_id={project_id}'
```

### Create Comment
```bash
maton api -X POST '/todoist/api/v1/comments' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "task_id": "123456",
  "content": "This is a comment"
}
EOF
```

### Delete Comment
```bash
maton api -X DELETE '/todoist/api/v1/comments/{id}'
```

## Notes

- Task and Project IDs are strings
- Priority values: 1 (normal) to 4 (urgent)
- Use only one due date format per request: `due_string`, `due_date`, or `due_datetime`
- Comments require either `task_id` or `project_id`
- Close/reopen/delete operations return 204 No Content

## Resources

- [Todoist API v1 Documentation](https://developer.todoist.com/api/v1)
- [Todoist Filter Syntax](https://todoist.com/help/articles/introduction-to-filters)
