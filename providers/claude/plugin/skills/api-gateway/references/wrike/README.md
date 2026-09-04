# Wrike Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `wrike`
**Base URL proxied:** `www.wrike.com`

## API Path Pattern

```
/wrike/api/v4/{resource}
```

## Common Endpoints

### List Spaces
```bash
maton api '/wrike/api/v4/spaces'
```

### Get Space
```bash
maton api '/wrike/api/v4/spaces/{spaceId}'
```

### Get Folder Tree
```bash
maton api '/wrike/api/v4/folders'
```

### Get Folders in Space
```bash
maton api '/wrike/api/v4/spaces/{spaceId}/folders'
```

### Get Folder
```bash
maton api '/wrike/api/v4/folders/{folderId}'
```

### Create Folder
```bash
maton api -X POST '/wrike/api/v4/folders/{parentFolderId}/folders' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "New Folder"
}
EOF
```

### List Tasks
```bash
maton api '/wrike/api/v4/tasks'
maton api '/wrike/api/v4/folders/{folderId}/tasks'
maton api '/wrike/api/v4/spaces/{spaceId}/tasks'
```

### Get Task
```bash
maton api '/wrike/api/v4/tasks/{taskId}'
maton api '/wrike/api/v4/tasks/{taskId},{taskId},...'  # (up to 100 IDs)
```

### Create Task
```bash
maton api -X POST '/wrike/api/v4/folders/{folderId}/tasks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "New Task",
  "description": "Task description",
  "importance": "Normal"
}
EOF
```

### Update Task
```bash
maton api -X PUT '/wrike/api/v4/tasks/{taskId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "Updated Title",
  "importance": "High"
}
EOF
```

### Delete Task
```bash
maton api -X DELETE '/wrike/api/v4/tasks/{taskId}'
```

### List Comments
```bash
maton api '/wrike/api/v4/comments'
maton api '/wrike/api/v4/tasks/{taskId}/comments'
maton api '/wrike/api/v4/folders/{folderId}/comments'
```

### Create Comment
```bash
maton api -X POST '/wrike/api/v4/tasks/{taskId}/comments' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "text": "Comment text"
}
EOF
```

### List Attachments
```bash
maton api '/wrike/api/v4/attachments'
maton api '/wrike/api/v4/tasks/{taskId}/attachments'
```

### Download Attachment
```bash
maton api '/wrike/api/v4/attachments/{attachmentId}/download'
```

### List Contacts
```bash
maton api '/wrike/api/v4/contacts'
```

### List Groups
```bash
maton api '/wrike/api/v4/groups'
```

### Create Group
```bash
maton api -X POST '/wrike/api/v4/groups' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "New Group",
  "members": ["contactId"]
}
EOF
```

### List Workflows
```bash
maton api '/wrike/api/v4/workflows'
```

### List Custom Fields
```bash
maton api '/wrike/api/v4/customfields'
maton api '/wrike/api/v4/spaces/{spaceId}/customfields'
```

### List Timelogs
```bash
maton api '/wrike/api/v4/timelogs'
maton api '/wrike/api/v4/tasks/{taskId}/timelogs'
```

### Create Timelog
```bash
maton api -X POST '/wrike/api/v4/tasks/{taskId}/timelogs' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "hours": 2,
  "trackedDate": "2026-03-10",
  "comment": "Work description"
}
EOF
```

### List Dependencies
```bash
maton api '/wrike/api/v4/tasks/{taskId}/dependencies'
```

### List Approvals
```bash
maton api '/wrike/api/v4/approvals'
maton api '/wrike/api/v4/tasks/{taskId}/approvals'
```

### List Invitations
```bash
maton api '/wrike/api/v4/invitations'
```

### List Work Schedules
```bash
maton api '/wrike/api/v4/workschedules'
```

### Get User (Admin)
```bash
maton api '/wrike/api/v4/users/{userId}'
```

### List Access Roles (Admin)
```bash
maton api '/wrike/api/v4/access_roles'
```

### Get Audit Log (Admin)
```bash
maton api '/wrike/api/v4/audit_log'
```

### Get Data Export (Admin)
```bash
maton api '/wrike/api/v4/data_export'
```

## Response Format

All Wrike API responses follow this structure:

```json
{
  "kind": "[resource_type]",
  "data": [...]
}
```

## Notes

- Resource IDs are base64-encoded strings
- Many endpoints support batch operations with up to 100 comma-separated IDs
- Tasks use `customStatusId` to reference workflow statuses
- Projects are folders with additional properties (owners, dates, status)

## Resources

- [Wrike API Documentation](https://developers.wrike.com/)
- [API Overview](https://developers.wrike.com/overview/)
- [OAuth 2.0 Authorization](https://developers.wrike.com/oauth-20-authorization/)
