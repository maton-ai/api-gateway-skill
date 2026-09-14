# Wrike

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `wrike`
**Upstream base URL:** `www.wrike.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://www.wrike.com/api/v4/spaces`
- Gateway: `https://api.maton.ai/wrike/api/v4/spaces`

### Spaces API

#### List Spaces

```bash
maton api '/wrike/api/v4/spaces'
```

**Response:**
```json
{
  "kind": "spaces",
  "data": [
    {
      "id": "MQAAAAEFzzdO",
      "title": "First space",
      "avatarUrl": "https://www.wrike.com/static/spaceicons2/v3/6/6-planet.png",
      "accessType": "Public",
      "archived": false,
      "defaultProjectWorkflowId": "IEAGXR2EK77ZIOF4",
      "defaultTaskWorkflowId": "IEAGXR2EK4G2YNU4"
    }
  ]
}
```

#### Get Space

```bash
maton api '/wrike/api/v4/spaces/{spaceId}'
```

**Note:** `{spaceId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Space

```bash
maton api -X POST '/wrike/api/v4/spaces' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "New Space"
}
JSON
```

#### Update Space

```bash
maton api -X PUT '/wrike/api/v4/spaces/{spaceId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Updated Space Name"
}
JSON
```

**Note:** `{spaceId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Space

```bash
maton api '/wrike/api/v4/spaces/{spaceId}' -X DELETE
```

**Note:** `{spaceId}` is a placeholder. Replace it with a real value before sending the request.

### Folders API

Folders and projects are the main ways to organize work in Wrike. Projects are folders with additional properties (owners, dates, status).

#### Get Folder Tree

```bash
maton api '/wrike/api/v4/folders'
```

**Response:**
```json
{
  "kind": "folderTree",
  "data": [
    {
      "id": "IEAGXR2EI7777777",
      "title": "Root",
      "childIds": ["MQAAAAEFzzdO", "MQAAAAEFzzRZ"],
      "scope": "WsRoot"
    },
    {
      "id": "MQAAAAEFzzdV",
      "title": "My Project",
      "childIds": [],
      "scope": "WsFolder",
      "project": {
        "authorId": "KUAXHKXS",
        "ownerIds": ["KUAXHKXS"],
        "customStatusId": "IEAGXR2EJMG2YNA4",
        "createdDate": "2026-03-09T08:15:07Z"
      }
    }
  ]
}
```

#### Get Folders in Space

```bash
maton api '/wrike/api/v4/spaces/{spaceId}/folders'
```

**Note:** `{spaceId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Folder

```bash
maton api '/wrike/api/v4/folders/{folderId}'

maton api '/wrike/api/v4/folders/{folderId},{folderId},...'  # up to 100 IDs
```

**Note:** `{folderId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Subfolders

```bash
maton api '/wrike/api/v4/folders/{folderId}/folders'
```

**Note:** `{folderId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Folder

```bash
maton api -X POST '/wrike/api/v4/folders/{parentFolderId}/folders' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "New Folder"
}
JSON
```

**Note:** `{parentFolderId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Folder

```bash
maton api -X PUT '/wrike/api/v4/folders/{folderId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Updated Folder Name"
}
JSON
```

**Note:** `{folderId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Folder

```bash
maton api '/wrike/api/v4/folders/{folderId}' -X DELETE
```

**Note:** `{folderId}` is a placeholder. Replace it with a real value before sending the request.

#### Copy Folder

```bash
maton api -X POST '/wrike/api/v4/copy_folder/{folderId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "parent": "{destinationFolderId}",
  "title": "Copy of Folder"
}
JSON
```

**Note:** `{folderId}` and `{destinationFolderId}` are placeholders. Replace each of them with real values before sending the request.

### Tasks API

#### List Tasks

```bash
maton api '/wrike/api/v4/tasks'
```

**Response:**
```json
{
  "kind": "tasks",
  "data": [
    {
      "id": "MAAAAAEFzzde",
      "accountId": "IEAGXR2E",
      "title": "First task",
      "status": "Active",
      "importance": "Normal",
      "createdDate": "2026-03-09T08:15:07Z",
      "updatedDate": "2026-03-10T07:07:57Z",
      "dates": {
        "type": "Planned",
        "duration": 2400,
        "start": "2026-03-05T09:00:00",
        "due": "2026-03-11T17:00:00"
      },
      "scope": "WsTask",
      "customStatusId": "IEAGXR2EJMG2YNV2",
      "permalink": "https://www.wrike.com/open.htm?id=4392433502"
    }
  ]
}
```

#### List Tasks in Folder

```bash
maton api '/wrike/api/v4/folders/{folderId}/tasks'
```

**Note:** `{folderId}` is a placeholder. Replace it with a real value before sending the request.

#### List Tasks in Space

```bash
maton api '/wrike/api/v4/spaces/{spaceId}/tasks'
```

**Note:** `{spaceId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Task

```bash
maton api '/wrike/api/v4/tasks/{taskId}'

maton api '/wrike/api/v4/tasks/{taskId},{taskId},...'  # up to 100 IDs
```

**Note:** `{taskId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Task

```bash
maton api -X POST '/wrike/api/v4/folders/{folderId}/tasks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "New Task",
  "description": "Task description",
  "importance": "Normal",
  "dates": {
    "start": "2026-03-15",
    "due": "2026-03-20"
  }
}
JSON
```

**Note:** `{folderId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "kind": "tasks",
  "data": [
    {
      "id": "MAAAAAEF7ufN",
      "accountId": "IEAGXR2E",
      "title": "New Task",
      "description": "Task description",
      "status": "Active",
      "importance": "Normal",
      "createdDate": "2026-03-10T07:16:07Z",
      "scope": "WsTask",
      "customStatusId": "IEAGXR2EJMG2YNU4",
      "permalink": "https://www.wrike.com/open.htm?id=4394510285"
    }
  ]
}
```

#### Update Task

```bash
maton api -X PUT '/wrike/api/v4/tasks/{taskId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Updated Task Title",
  "importance": "High"
}
JSON
```

**Note:** `{taskId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Multiple Tasks

```bash
maton api -X PUT '/wrike/api/v4/tasks/{taskId},{taskId},...' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "status": "Completed"
}
JSON
```

**Note:** `{taskId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Task

```bash
maton api '/wrike/api/v4/tasks/{taskId}' -X DELETE
```

**Note:** `{taskId}` is a placeholder. Replace it with a real value before sending the request.

### Comments API

#### List Comments

```bash
maton api '/wrike/api/v4/comments'

maton api '/wrike/api/v4/tasks/{taskId}/comments'

maton api '/wrike/api/v4/folders/{folderId}/comments'

maton api '/wrike/api/v4/comments/{commentId},{commentId},...'  # up to 100 IDs
```

**Note:** `{taskId}`, `{folderId}` and `{commentId}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "kind": "comments",
  "data": [
    {
      "id": "IEAGXR2EIMBGYQMR",
      "authorId": "KUAXI4LC",
      "text": "This is a comment",
      "updatedDate": "2026-03-10T07:07:57Z",
      "createdDate": "2026-03-10T07:07:57Z",
      "taskId": "MAAAAAEFzzde"
    }
  ]
}
```

#### Create Comment

```bash
maton api -X POST '/wrike/api/v4/tasks/{taskId}/comments' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "text": "New comment text"
}
JSON
```

**Note:** `{taskId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Comment

```bash
maton api -X PUT '/wrike/api/v4/comments/{commentId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "text": "Updated comment text"
}
JSON
```

**Note:** `{commentId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Comment

```bash
maton api '/wrike/api/v4/comments/{commentId}' -X DELETE
```

**Note:** `{commentId}` is a placeholder. Replace it with a real value before sending the request.

### Attachments API

#### List Attachments

```bash
maton api '/wrike/api/v4/attachments'

maton api '/wrike/api/v4/tasks/{taskId}/attachments'

maton api '/wrike/api/v4/folders/{folderId}/attachments'

maton api '/wrike/api/v4/attachments/{attachmentId},{attachmentId},...'  # up to 100 IDs
```

**Note:** `{taskId}`, `{folderId}` and `{attachmentId}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "kind": "attachments",
  "data": [
    {
      "id": "IEAGXR2EIYUN54ZV",
      "authorId": "KUAXHKXS",
      "name": "document.pdf",
      "createdDate": "2026-03-09T08:15:08Z",
      "version": 1,
      "type": "Wrike",
      "contentType": "application/pdf",
      "size": 117940,
      "taskId": "MAAAAAEFzzde"
    }
  ]
}
```

#### Download Attachment

```bash
maton api '/wrike/api/v4/attachments/{attachmentId}/download'
```

**Note:** `{attachmentId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Attachment Preview

```bash
maton api '/wrike/api/v4/attachments/{attachmentId}/preview'
```

**Note:** `{attachmentId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Attachment Access URL

```bash
maton api '/wrike/api/v4/attachments/{attachmentId}/url'
```

**Note:** `{attachmentId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Attachment

```bash
maton api -X PUT '/wrike/api/v4/attachments/{attachmentId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "renamed-file.pdf"
}
JSON
```

**Note:** `{attachmentId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Attachment

```bash
maton api '/wrike/api/v4/attachments/{attachmentId}' -X DELETE
```

**Note:** `{attachmentId}` is a placeholder. Replace it with a real value before sending the request.

### Contacts API

Contacts represent users and groups in Wrike.

#### List Contacts

```bash
maton api '/wrike/api/v4/contacts'

maton api '/wrike/api/v4/contacts/{contactId},{contactId},...'  # up to 100 IDs
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "kind": "contacts",
  "data": [
    {
      "id": "KUAXHKXS",
      "firstName": "Chris",
      "lastName": "",
      "type": "Person",
      "profiles": [
        {
          "accountId": "IEAGXR2E",
          "email": "user@example.com",
          "role": "User",
          "external": false,
          "admin": false,
          "owner": true,
          "active": true
        }
      ],
      "timezone": "US/Pacific",
      "locale": "en",
      "deleted": false,
      "me": true
    }
  ]
}
```

#### Update Contact

```bash
maton api -X PUT '/wrike/api/v4/contacts/{contactId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "metadata": [{"key": "customKey", "value": "customValue"}]
}
JSON
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

### Groups API

#### List Groups

```bash
maton api '/wrike/api/v4/groups'

maton api '/wrike/api/v4/groups/{groupId}'
```

**Note:** `{groupId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "kind": "groups",
  "data": [
    {
      "id": "KX7XIKVN",
      "accountId": "IEAGXR2E",
      "title": "My Team",
      "memberIds": ["KUAXHKXS"],
      "childIds": [],
      "parentIds": [],
      "myTeam": true
    }
  ]
}
```

#### Create Group

```bash
maton api -X POST '/wrike/api/v4/groups' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "New Group",
  "members": ["KUAXHKXS"]
}
JSON
```

#### Update Group

```bash
maton api -X PUT '/wrike/api/v4/groups/{groupId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Updated Group Name"
}
JSON
```

**Note:** `{groupId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Group

```bash
maton api '/wrike/api/v4/groups/{groupId}' -X DELETE
```

**Note:** `{groupId}` is a placeholder. Replace it with a real value before sending the request.

### Workflows API

#### List Workflows

```bash
maton api '/wrike/api/v4/workflows'

maton api '/wrike/api/v4/spaces/{spaceId}/workflows'
```

**Note:** `{spaceId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "kind": "workflows",
  "data": [
    {
      "id": "IEAGXR2EK77ZIOF4",
      "name": "Default Workflow",
      "standard": true,
      "hidden": false,
      "customStatuses": [
        {
          "id": "IEAGXR2EJMAAAAAA",
          "name": "New",
          "color": "Blue",
          "group": "Active",
          "hidden": false
        },
        {
          "id": "IEAGXR2EJMG2YNA4",
          "name": "In Progress",
          "color": "Turquoise",
          "group": "Active",
          "hidden": false
        },
        {
          "id": "IEAGXR2EJMAAAAAB",
          "name": "Completed",
          "color": "Green",
          "group": "Completed",
          "hidden": false
        }
      ]
    }
  ]
}
```

#### Create Workflow

```bash
maton api -X POST '/wrike/api/v4/workflows' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Custom Workflow"
}
JSON
```

#### Update Workflow

```bash
maton api -X PUT '/wrike/api/v4/workflows/{workflowId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Workflow Name"
}
JSON
```

**Note:** `{workflowId}` is a placeholder. Replace it with a real value before sending the request.

### Custom Fields API

#### List Custom Fields

```bash
maton api '/wrike/api/v4/customfields'

maton api '/wrike/api/v4/spaces/{spaceId}/customfields'

maton api '/wrike/api/v4/customfields/{customfieldId},{customfieldId},...'  # up to 100 IDs
```

**Note:** `{spaceId}` and `{customfieldId}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "kind": "customfields",
  "data": [
    {
      "id": "IEAGXR2EJUALBS23",
      "accountId": "IEAGXR2E",
      "title": "Impact",
      "type": "DropDown",
      "spaceId": "MQAAAAEFzzdO",
      "settings": {
        "values": ["Low", "Medium", "High"],
        "options": [
          {"value": "Low", "color": "Green"},
          {"value": "Medium", "color": "Yellow"},
          {"value": "High", "color": "Red"}
        ]
      }
    }
  ]
}
```

#### Create Custom Field

```bash
maton api -X POST '/wrike/api/v4/customfields' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Priority",
  "type": "DropDown",
  "settings": {
    "values": ["Low", "Medium", "High"]
  }
}
JSON
```

#### Update Custom Field

```bash
maton api -X PUT '/wrike/api/v4/customfields/{customfieldId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Updated Field Name"
}
JSON
```

**Note:** `{customfieldId}` is a placeholder. Replace it with a real value before sending the request.

### Timelogs API

#### List Timelogs

```bash
maton api '/wrike/api/v4/timelogs'

maton api '/wrike/api/v4/tasks/{taskId}/timelogs'

maton api '/wrike/api/v4/folders/{folderId}/timelogs'

maton api '/wrike/api/v4/contacts/{contactId}/timelogs'

maton api '/wrike/api/v4/timelogs/{timelogId},{timelogId},...'  # up to 100 IDs
```

**Note:** `{taskId}`, `{folderId}`, `{contactId}` and `{timelogId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Timelog

```bash
maton api -X POST '/wrike/api/v4/tasks/{taskId}/timelogs' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "hours": 2,
  "trackedDate": "2026-03-10",
  "comment": "Worked on implementation"
}
JSON
```

**Note:** `{taskId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Timelog

```bash
maton api -X PUT '/wrike/api/v4/timelogs/{timelogId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "hours": 3,
  "comment": "Updated time entry"
}
JSON
```

**Note:** `{timelogId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Timelog

```bash
maton api '/wrike/api/v4/timelogs/{timelogId}' -X DELETE
```

**Note:** `{timelogId}` is a placeholder. Replace it with a real value before sending the request.

### Timelog Categories API

```bash
maton api '/wrike/api/v4/timelog_categories'
```

### Dependencies API

#### List Dependencies

```bash
maton api '/wrike/api/v4/tasks/{taskId}/dependencies'

maton api '/wrike/api/v4/dependencies/{dependencyId},{dependencyId},...'  # up to 100 IDs
```

**Note:** `{taskId}` and `{dependencyId}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "kind": "dependencies",
  "data": [
    {
      "id": "MgAAAAEFzzdeMwAAAAEFzzdb",
      "predecessorId": "MAAAAAEFzzde",
      "successorId": "MAAAAAEFzzdb",
      "relationType": "FinishToStart",
      "lagTime": 0
    }
  ]
}
```

#### Create Dependency

```bash
maton api -X POST '/wrike/api/v4/tasks/{taskId}/dependencies' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "predecessorId": "{taskId}",
  "relationType": "FinishToStart"
}
JSON
```

**Note:** `{taskId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Dependency

```bash
maton api -X PUT '/wrike/api/v4/dependencies/{dependencyId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "relationType": "StartToStart"
}
JSON
```

**Note:** `{dependencyId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Dependency

```bash
maton api '/wrike/api/v4/dependencies/{dependencyId}' -X DELETE
```

**Note:** `{dependencyId}` is a placeholder. Replace it with a real value before sending the request.

### Approvals API

#### List Approvals

```bash
maton api '/wrike/api/v4/approvals'

maton api '/wrike/api/v4/tasks/{taskId}/approvals'

maton api '/wrike/api/v4/folders/{folderId}/approvals'

maton api '/wrike/api/v4/approvals/{approvalId},{approvalId},...'  # up to 100 IDs
```

**Note:** `{taskId}`, `{folderId}` and `{approvalId}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "kind": "approvals",
  "data": [
    {
      "id": "IEAGXR2EMEB33OQA",
      "taskId": "MAAAAAEFzzde",
      "authorId": "KUAXHKXS",
      "dueDate": "2026-03-12",
      "decisions": [
        {
          "approverId": "KUAXHKXS",
          "status": "Pending",
          "updatedDate": "2026-03-09T08:15:08Z"
        }
      ],
      "status": "Pending",
      "finished": false
    }
  ]
}
```

#### Create Approval

```bash
maton api -X POST '/wrike/api/v4/tasks/{taskId}/approvals' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "approvers": ["KUAXHKXS"],
  "dueDate": "2026-03-15"
}
JSON
```

**Note:** `{taskId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Approval

```bash
maton api -X PUT '/wrike/api/v4/approvals/{approvalId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "status": "Approved"
}
JSON
```

**Note:** `{approvalId}` is a placeholder. Replace it with a real value before sending the request.

#### Cancel Approval

```bash
maton api '/wrike/api/v4/approvals/{approvalId}' -X DELETE
```

**Note:** `{approvalId}` is a placeholder. Replace it with a real value before sending the request.

### Invitations API

> **Admin scope.** Invitations affect account membership and governance. Creating an invitation grants a new user access to the Wrike account. Confirm the email, role, and intent with the user before executing.

#### List Invitations

```bash
maton api '/wrike/api/v4/invitations'
```

**Response:**
```json
{
  "kind": "invitations",
  "data": [
    {
      "id": "IEAGXR2EJEAVFLCG",
      "accountId": "IEAGXR2E",
      "firstName": "John",
      "email": "john@example.com",
      "status": "Accepted",
      "inviterUserId": "KUAXHKXS",
      "invitationDate": "2026-03-09T08:14:04Z",
      "role": "User",
      "external": false
    }
  ]
}
```

#### Create Invitation

```bash
maton api -X POST '/wrike/api/v4/invitations' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "email": "newuser@example.com",
  "firstName": "New",
  "lastName": "User",
  "role": "User"
}
JSON
```

#### Update Invitation

```bash
maton api -X PUT '/wrike/api/v4/invitations/{invitationId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "role": "User",
  "resend": true
}
JSON
```

**Note:** `{invitationId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Invitation

```bash
maton api '/wrike/api/v4/invitations/{invitationId}' -X DELETE
```

**Note:** `{invitationId}` is a placeholder. Replace it with a real value before sending the request.

### Work Schedules API

#### List Work Schedules

```bash
maton api '/wrike/api/v4/workschedules'

maton api '/wrike/api/v4/workschedules/{workscheduleId}'
```

**Note:** `{workscheduleId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "kind": "workschedules",
  "data": [
    {
      "id": "IEAGXR2EML7ZIOF4",
      "scheduleType": "Default",
      "title": "Default Schedule",
      "workweek": [
        {
          "workDays": ["Mon", "Tue", "Wed", "Thu", "Fri"],
          "capacityMinutes": 480
        }
      ]
    }
  ]
}
```

#### Create Work Schedule

```bash
maton api -X POST '/wrike/api/v4/workschedules' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Custom Schedule"
}
JSON
```

#### Update Work Schedule

```bash
maton api -X PUT '/wrike/api/v4/workschedules/{workscheduleId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Updated Schedule",
  "workweek": {"mon": true, "tue": true, "wed": true, "thu": true, "fri": true, "sat": false, "sun": false}
}
JSON
```

**Note:** `{workscheduleId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Work Schedule

```bash
maton api '/wrike/api/v4/workschedules/{workscheduleId}' -X DELETE
```

**Note:** `{workscheduleId}` is a placeholder. Replace it with a real value before sending the request.

### Users API

> **Admin scope.** User management operations affect account membership and access. Confirm the target user and intended change with the user before executing.

#### Get User

```bash
maton api '/wrike/api/v4/users/{userId}'
```

**Note:** `{userId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "kind": "users",
  "data": [
    {
      "id": "KUAXHKXS",
      "firstName": "Chris",
      "lastName": "",
      "type": "Person",
      "profiles": [
        {
          "accountId": "IEAGXR2E",
          "email": "user@example.com",
          "role": "User",
          "external": false,
          "admin": false,
          "owner": true,
          "active": true
        }
      ],
      "timezone": "US/Pacific",
      "locale": "en",
      "deleted": false,
      "me": true,
      "title": "Engineer",
      "companyName": "Company",
      "primaryEmail": "user@example.com",
      "userTypeId": "IEAGXR2ENH777777"
    }
  ]
}
```

#### Update User

```bash
maton api -X PUT '/wrike/api/v4/users/{userId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "profile": {
    "accountId": "{accountId}",
    "role": "User"
  }
}
JSON
```

The same body applies when updating up to 100 users at once:

```bash
maton api -X PUT '/wrike/api/v4/users/{userId},{userId},...' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "profile": {
    "accountId": "{accountId}",
    "role": "User"
  }
}
JSON
```

**Note:** `{userId}` is a placeholder. Replace it with a real value before sending the request.

### Access Roles API

> **Admin scope.** Access roles define permission levels across the account. Modifying roles changes what users can do across all shared resources.

#### List Access Roles

```bash
maton api '/wrike/api/v4/access_roles'
```

**Response:**
```json
{
  "kind": "accessRoles",
  "data": [
    {
      "id": "IEAGXR2END777777",
      "title": "Full",
      "description": "Can edit"
    },
    {
      "id": "IEAGXR2END777776",
      "title": "Editor",
      "description": "Can edit, but can't share or delete"
    },
    {
      "id": "IEAGXR2END777775",
      "title": "Limited",
      "description": "Can comment, change statuses, attach files, and start approvals"
    },
    {
      "id": "IEAGXR2END777774",
      "title": "Read Only",
      "description": "Can view"
    }
  ]
}
```

### Audit Log API

> **Privacy-sensitive.** The audit log exposes login events, IP addresses, user emails, and operational history. Only access when the user explicitly requests compliance or security auditing. Do not retrieve proactively.

#### Get Audit Log

```bash
maton api '/wrike/api/v4/audit_log'
```

**Response:**
```json
{
  "kind": "auditLog",
  "data": [
    {
      "id": "IEAGXR2ENQAAAAABMUI3U3A",
      "operation": "UserLoggedIn",
      "userId": "KUAXHKXS",
      "userEmail": "user@example.com",
      "eventDate": "2026-03-10T07:24:24Z",
      "ipAddress": "35.84.133.252",
      "objectType": "User",
      "objectName": "user@example.com",
      "objectId": "KUAXHKXS",
      "details": {
        "Login Type": "Oauth2",
        "User Agent": "Nango"
      }
    }
  ]
}
```

**Common Operations:**
- `UserLoggedIn` - User login events
- `Oauth2AccessGranted` - OAuth authorization events
- `TaskCreated`, `TaskDeleted`, `TaskModified` - Task operations
- `FolderCreated`, `FolderDeleted` - Folder operations
- `CommentAdded` - Comment events

### Data Export API

> **Bulk data extraction.** Data export generates a full organizational export (tasks, projects, users, timelogs, etc.). This enables large-scale data extraction well beyond normal task queries. Only invoke when the user explicitly requests a data export and confirms the intent. The first GET request triggers export generation automatically.

#### Get Data Export

```bash
maton api '/wrike/api/v4/data_export'

maton api '/wrike/api/v4/data_export/{data_exportId}'
```

**Note:** `{data_exportId}` is a placeholder. Replace it with a real value before sending the request.

Returns 202 on first request (export generation starts automatically). Subsequent calls return available daily-updated exports.

#### Refresh Data Export

```bash
maton api -X POST '/wrike/api/v4/data_export'
```

Triggers a new data export refresh.

#### Get Data Export Schema

```bash
maton api '/wrike/api/v4/data_export_schema'
```

Retrieves the schema documentation for export tables.

### Response Format

All Wrike API responses follow a standardized JSON structure:

```json
{
  "kind": "[resource_type]",
  "data": [...]
}
```

### Pagination

Some endpoints support pagination with `nextPageToken`:

```json
{
  "kind": "timelogs",
  "nextPageToken": "AFZ2V4QAAAAA6AAAAAAAAAAAAAAAAAAA22NEEX6HNLKBU",
  "responseSize": 100,
  "data": [...]
}
```

Use `pageToken` parameter for subsequent requests:

```bash
maton api '/wrike/api/v4/timelogs?pageToken={nextPageToken}'
```

**Note:** `{nextPageToken}` is a placeholder. Replace it with a real value before sending the request.

### Notes

- **Batch Operations**: Many endpoints support up to 100 IDs in a single request (comma-separated)
- **Custom Status IDs**: Tasks use `customStatusId` to reference workflow statuses
- **Projects vs Folders**: Projects are folders with additional properties (owners, dates, status)

### Resources

- [Wrike API Documentation](https://developers.wrike.com/)
- [Wrike API Overview](https://developers.wrike.com/overview/)
- [OAuth 2.0 Authorization](https://developers.wrike.com/oauth-20-authorization/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
