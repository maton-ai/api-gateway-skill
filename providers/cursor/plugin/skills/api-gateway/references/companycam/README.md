# CompanyCam Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `companycam`
**Base URL proxied:** `api.companycam.com`

## API Path Pattern

```
/companycam/v2/{resource}
```

## Common Endpoints

### Company

#### Get Company
```bash
maton api '/companycam/v2/company'
```

### Users

#### Get Current User
```bash
maton api '/companycam/v2/users/current'
```

#### List Users
```bash
maton api '/companycam/v2/users'
```

#### Create User
```bash
maton api -X POST '/companycam/v2/users'
```

#### Get User
```bash
maton api '/companycam/v2/users/{id}'
```

#### Update User
```bash
maton api -X PUT '/companycam/v2/users/{id}'
```

#### Delete User
```bash
maton api -X DELETE '/companycam/v2/users/{id}'
```

### Projects

#### List Projects
```bash
maton api '/companycam/v2/projects'
```

#### Create Project
```bash
maton api -X POST '/companycam/v2/projects'
```

#### Get Project
```bash
maton api '/companycam/v2/projects/{id}'
```

#### Update Project
```bash
maton api -X PUT '/companycam/v2/projects/{id}'
```

#### Delete Project
```bash
maton api -X DELETE '/companycam/v2/projects/{id}'
```

#### Archive Project
```bash
maton api -X PATCH '/companycam/v2/projects/{id}/archive'
```

#### Restore Project
```bash
maton api -X PUT '/companycam/v2/projects/{id}/restore'
```

### Project Photos

#### List Project Photos
```bash
maton api '/companycam/v2/projects/{project_id}/photos'
```

#### Add Photo to Project
```bash
maton api -X POST '/companycam/v2/projects/{project_id}/photos'
```

### Project Comments

#### List Project Comments
```bash
maton api '/companycam/v2/projects/{project_id}/comments'
```

#### Add Project Comment
```bash
maton api -X POST '/companycam/v2/projects/{project_id}/comments'
```

### Project Labels

#### List Project Labels
```bash
maton api '/companycam/v2/projects/{project_id}/labels'
```

#### Add Labels
```bash
maton api -X POST '/companycam/v2/projects/{project_id}/labels'
```

### Project Documents

#### List Documents
```bash
maton api '/companycam/v2/projects/{project_id}/documents'
```

#### Upload Document
```bash
maton api -X POST '/companycam/v2/projects/{project_id}/documents'
```

### Photos

#### List All Photos
```bash
maton api '/companycam/v2/photos'
```

#### Get Photo
```bash
maton api '/companycam/v2/photos/{id}'
```

#### Update Photo
```bash
maton api -X PUT '/companycam/v2/photos/{id}'
```

#### Delete Photo
```bash
maton api -X DELETE '/companycam/v2/photos/{id}'
```

### Tags

#### List Tags
```bash
maton api '/companycam/v2/tags'
```

#### Create Tag
```bash
maton api -X POST '/companycam/v2/tags'
```

#### Get Tag
```bash
maton api '/companycam/v2/tags/{id}'
```

#### Update Tag
```bash
maton api -X PUT '/companycam/v2/tags/{id}'
```

#### Delete Tag
```bash
maton api -X DELETE '/companycam/v2/tags/{id}'
```

### Groups

#### List Groups
```bash
maton api '/companycam/v2/groups'
```

#### Create Group
```bash
maton api -X POST '/companycam/v2/groups'
```

#### Get Group
```bash
maton api '/companycam/v2/groups/{id}'
```

#### Update Group
```bash
maton api -X PUT '/companycam/v2/groups/{id}'
```

#### Delete Group
```bash
maton api -X DELETE '/companycam/v2/groups/{id}'
```

### Checklists

#### List Checklists
```bash
maton api '/companycam/v2/checklists'
```

### Webhooks

#### List Webhooks
```bash
maton api '/companycam/v2/webhooks'
```

#### Create Webhook
```bash
maton api -X POST '/companycam/v2/webhooks'
```

#### Get Webhook
```bash
maton api '/companycam/v2/webhooks/{id}'
```

#### Update Webhook
```bash
maton api -X PUT '/companycam/v2/webhooks/{id}'
```

#### Delete Webhook
```bash
maton api -X DELETE '/companycam/v2/webhooks/{id}'
```

## Query Parameters

- `page` - Page number (default: 1)
- `per_page` - Results per page (default: 25)
- `query` - Search query (projects)
- `status` - Filter by status
- `modified_since` - Unix timestamp for filtering

## Notes

- IDs are returned as strings
- Timestamps are Unix timestamps (seconds since epoch)
- Comments must be wrapped in a `comment` object
- Webhooks use `scopes` parameter (not `events`)
- Rate limits: 240 GET/min, 100 POST/PUT/DELETE/min

## Resources

- [CompanyCam API Documentation](https://docs.companycam.com)
