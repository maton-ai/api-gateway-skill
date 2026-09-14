# Microsoft To Do

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `microsoft-to-do`
**Upstream base URL:** `graph.microsoft.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://graph.microsoft.com/v1.0/me/todo/lists`
- Gateway: `https://api.maton.ai/microsoft-to-do/v1.0/me/todo/lists`

**Important:** All Microsoft To Do endpoints use the Microsoft Graph API under the `/me/todo/` path.

### Task Lists API

#### List All Task Lists

```bash
maton api '/microsoft-to-do/v1.0/me/todo/lists'
```

**Response:**
```json
{
  "value": [
    {
      "id": "AAMkADIyAAAhrbPWAAA=",
      "displayName": "Tasks",
      "isOwner": true,
      "isShared": false,
      "wellknownListName": "defaultList"
    }
  ]
}
```

#### Get Task List

```bash
maton api '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}'
```

**Note:** `{todoTaskListId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Task List

```bash
maton api -X POST '/microsoft-to-do/v1.0/me/todo/lists' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "displayName": "Travel items"
}
JSON
```

**Response (201 Created):**
```json
{
  "id": "AAMkADIyAAAhrbPWAAA=",
  "displayName": "Travel items",
  "isOwner": true,
  "isShared": false,
  "wellknownListName": "none"
}
```

#### Update Task List

```bash
maton api -X PATCH '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "displayName": "Vacation Plan"
}
JSON
```

**Note:** `{todoTaskListId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Task List

```bash
maton api '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}' -X DELETE
```

**Note:** `{todoTaskListId}` is a placeholder. Replace it with a real value before sending the request.

Returns `204 No Content` on success.

### Tasks API

#### List Tasks

```bash
maton api '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks'
```

**Note:** `{todoTaskListId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "value": [
    {
      "id": "AlMKXwbQAAAJws6wcAAAA=",
      "title": "Buy groceries",
      "status": "notStarted",
      "importance": "normal",
      "isReminderOn": false,
      "createdDateTime": "2024-01-15T10:00:00Z",
      "lastModifiedDateTime": "2024-01-15T10:00:00Z",
      "body": {
        "content": "",
        "contentType": "text"
      },
      "categories": []
    }
  ]
}
```

#### Get Task

```bash
maton api '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}'
```

**Note:** `{todoTaskListId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Task

```bash
maton api -X POST '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "A new task",
  "importance": "high",
  "status": "notStarted",
  "categories": ["Important"],
  "dueDateTime": {
    "dateTime": "2024-12-31T17:00:00",
    "timeZone": "Eastern Standard Time"
  },
  "startDateTime": {
    "dateTime": "2024-12-01T08:00:00",
    "timeZone": "Eastern Standard Time"
  },
  "isReminderOn": true,
  "reminderDateTime": {
    "dateTime": "2024-12-01T09:00:00",
    "timeZone": "Eastern Standard Time"
  },
  "body": {
    "content": "Task details here",
    "contentType": "text"
  }
}
JSON
```

**Note:** `{todoTaskListId}` is a placeholder. Replace it with a real value before sending the request.

**Task Fields:**

| Field | Type | Description |
|-------|------|-------------|
| `title` | String | Brief description of the task |
| `body` | itemBody | Task body with content and contentType (text/html) |
| `importance` | String | `low`, `normal`, or `high` |
| `status` | String | `notStarted`, `inProgress`, `completed`, `waitingOnOthers`, `deferred` |
| `categories` | String[] | Associated category names |
| `dueDateTime` | dateTimeTimeZone | Due date and time |
| `startDateTime` | dateTimeTimeZone | Start date and time |
| `completedDateTime` | dateTimeTimeZone | Completion date and time |
| `reminderDateTime` | dateTimeTimeZone | Reminder date and time |
| `isReminderOn` | Boolean | Whether reminder is enabled |
| `recurrence` | patternedRecurrence | Recurrence pattern |

#### Update Task

```bash
maton api -X PATCH '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "status": "completed",
  "completedDateTime": {
    "dateTime": "2024-01-20T15:00:00",
    "timeZone": "UTC"
  }
}
JSON
```

**Note:** `{todoTaskListId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Task

```bash
maton api '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}' -X DELETE
```

**Note:** `{todoTaskListId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

Returns `204 No Content` on success.

### Checklist Items API

#### List Checklist Items

```bash
maton api '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}/checklistItems'
```

**Note:** `{todoTaskListId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "value": [
    {
      "id": "51d8a471-2e9d-4f53-9937-c33a8742d28f",
      "displayName": "Create draft",
      "createdDateTime": "2024-01-17T05:22:14Z",
      "isChecked": false
    }
  ]
}
```

#### Create Checklist Item

```bash
maton api -X POST '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}/checklistItems' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "displayName": "Final sign-off from the team"
}
JSON
```

**Note:** `{todoTaskListId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

#### Update Checklist Item

```bash
maton api -X PATCH '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}/checklistItems/{checklistItemId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "isChecked": true
}
JSON
```

**Note:** `{todoTaskListId}`, `{taskId}` and `{checklistItemId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Checklist Item

```bash
maton api '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}/checklistItems/{checklistItemId}' -X DELETE
```

**Note:** `{todoTaskListId}`, `{taskId}` and `{checklistItemId}` are placeholders. Replace each of them with real values before sending the request.

Returns `204 No Content` on success.

### Linked Resources API

#### List Linked Resources

```bash
maton api '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}/linkedResources'
```

**Note:** `{todoTaskListId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "value": [
    {
      "id": "f9cddce2-dce2-f9cd-e2dc-cdf9e2dccdf9",
      "webUrl": "https://example.com/item",
      "applicationName": "MyApp",
      "displayName": "Related Document",
      "externalId": "external-123"
    }
  ]
}
```

#### Create Linked Resource

```bash
maton api -X POST '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}/linkedResources' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "webUrl": "https://example.com/item",
  "applicationName": "MyApp",
  "displayName": "Related Document",
  "externalId": "external-123"
}
JSON
```

**Note:** `{todoTaskListId}` and `{taskId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Linked Resource

```bash
maton api '/microsoft-to-do/v1.0/me/todo/lists/{todoTaskListId}/tasks/{taskId}/linkedResources/{linkedResourceId}' -X DELETE
```

**Note:** `{todoTaskListId}`, `{taskId}` and `{linkedResourceId}` are placeholders. Replace each of them with real values before sending the request.

Returns `204 No Content` on success.

### Notes

- Task list IDs and task IDs are opaque base64-encoded strings
- Timestamps use ISO 8601 format in UTC by default
- The `dateTimeTimeZone` type requires both `dateTime` and `timeZone` fields
- Task `status` values: `notStarted`, `inProgress`, `completed`, `waitingOnOthers`, `deferred`
- Task `importance` values: `low`, `normal`, `high`
- Supports OData query parameters: `$select`, `$filter`, `$orderby`, `$top`, `$skip`
- Pagination uses `@odata.nextLink` for continuation

### Resources

- [Microsoft To Do API Overview](https://learn.microsoft.com/en-us/graph/api/resources/todo-overview)
- [todoTaskList Resource](https://learn.microsoft.com/en-us/graph/api/resources/todotasklist)
- [todoTask Resource](https://learn.microsoft.com/en-us/graph/api/resources/todotask)
- [Maton CLI Manual](https://cli.maton.ai/manual)
