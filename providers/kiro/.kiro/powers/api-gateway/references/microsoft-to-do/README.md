# Microsoft To Do Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `microsoft-to-do`
**Base URL proxied:** `graph.microsoft.com`

## API Path Pattern

```
/microsoft-to-do/v1.0/me/todo/{resource}
```

All Microsoft To Do endpoints use the Microsoft Graph API under the `/me/todo/` path.

## Common Endpoints

### Task Lists

#### List All Task Lists
```bash
maton api '/microsoft-to-do/v1.0/me/todo/lists'
```

#### Get Task List
```bash
maton api '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}'
```

#### Create Task List
```bash
maton api -X POST '/microsoft-to-do/v1.0/me/todo/lists' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "displayName": "My New List"
}
EOF
```

#### Update Task List
```bash
maton api -X PATCH '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "displayName": "Updated List Name"
}
EOF
```

#### Delete Task List
```bash
maton api -X DELETE '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}'
```

### Tasks

#### List Tasks
```bash
maton api '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks'
```

#### Get Task
```bash
maton api '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}'
```

#### Create Task
```bash
maton api -X POST '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "New Task",
  "importance": "high",
  "status": "notStarted",
  "dueDateTime": {
    "dateTime": "2024-12-31T17:00:00",
    "timeZone": "UTC"
  }
}
EOF
```

#### Update Task
```bash
maton api -X PATCH '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "status": "completed"
}
EOF
```

#### Delete Task
```bash
maton api -X DELETE '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}'
```

### Checklist Items

#### List Checklist Items
```bash
maton api '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}/checklistItems'
```

#### Create Checklist Item
```bash
maton api -X POST '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}/checklistItems' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "displayName": "Subtask name"
}
EOF
```

#### Update Checklist Item
```bash
maton api -X PATCH '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}/checklistItems/{checklistItemId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "isChecked": true
}
EOF
```

#### Delete Checklist Item
```bash
maton api -X DELETE '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}/checklistItems/{checklistItemId}'
```

### Linked Resources

#### List Linked Resources
```bash
maton api '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}/linkedResources'
```

#### Create Linked Resource
```bash
maton api -X POST '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}/linkedResources' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "webUrl": "https://example.com/item",
  "applicationName": "MyApp",
  "displayName": "Related Item"
}
EOF
```

#### Delete Linked Resource
```bash
maton api -X DELETE '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}/linkedResources/{linkedResourceId}'
```

## Notes

- Task list IDs and task IDs are opaque base64-encoded strings
- Timestamps use ISO 8601 format in UTC by default
- The `dateTimeTimeZone` type requires both `dateTime` and `timeZone` fields
- Task `status` values: `notStarted`, `inProgress`, `completed`, `waitingOnOthers`, `deferred`
- Task `importance` values: `low`, `normal`, `high`
- Supports OData query parameters: `$select`, `$filter`, `$orderby`, `$top`, `$skip`
- Pagination uses `@odata.nextLink` for continuation

## Resources

- [Microsoft To Do API Overview](https://learn.microsoft.com/en-us/graph/api/resources/todo-overview)
- [todoTaskList Resource](https://learn.microsoft.com/en-us/graph/api/resources/todotasklist)
- [todoTask Resource](https://learn.microsoft.com/en-us/graph/api/resources/todotask)
