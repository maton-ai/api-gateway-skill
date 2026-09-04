# Podio Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `podio`
**Base URL proxied:** `api.podio.com`

> **Never suppress notifications or webhooks to make a change less visible.** Podio accepts `silent=true` (no notifications to collaborators) and `hook=false` (no webhook triggers) on write operations. Both remove the signals coworkers and downstream automations rely on to notice a change, so a modification made with them set is effectively invisible to everyone but the caller.
> - Default to leaving both off. An agent writing to a shared workspace should be *more* visible than a human doing the same thing, not less.
> - Set either flag only when the user explicitly asks for it, and only for the specific call they asked about — never apply it broadly or carry it over to later calls.
> - Never use them to reduce noise, avoid alarming someone, hide a mistake, or work around a failing webhook. If a webhook is misfiring, say so instead of silencing it.
> - `hook=false` can also break integrations that depend on those triggers to stay in sync; the resulting drift is silent by definition.

## API Path Pattern

```
/podio/{resource}/{id}/
```

Note: Many Podio endpoints use trailing slashes.

## Common Endpoints

### Organizations

#### List Organizations
```bash
maton api '/podio/org/'
```

#### Get Organization
```bash
maton api '/podio/org/{org_id}'
```

### Spaces (Workspaces)

#### List Spaces in Organization
```bash
maton api '/podio/space/org/{org_id}/'
```

#### Get Space
```bash
maton api '/podio/space/{space_id}'
```

#### Create Space
```bash
maton api -X POST '/podio/org/{org_id}/space/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "New Workspace",
  "privacy": "closed"
}
EOF
```

### Applications

#### List Apps in Space
```bash
maton api '/podio/app/space/{space_id}/'
```

#### Get App
```bash
maton api '/podio/app/{app_id}'
```

### Items

#### Filter Items
```bash
maton api -X POST '/podio/item/app/{app_id}/filter/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "limit": 30,
  "offset": 0,
  "sort_by": "created_on",
  "sort_desc": true,
  "filters": {
    "status": [1, 2]
  }
}
EOF
```

#### Get Item
```bash
maton api '/podio/item/{item_id}'
```

#### Create Item
```bash
maton api -X POST '/podio/item/app/{app_id}/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "fields": {
    "title": "New Item",
    "status": 1
  }
}
EOF
```

#### Update Item
```bash
maton api -X PUT '/podio/item/{item_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "fields": {
    "title": "Updated Title"
  }
}
EOF
```

#### Delete Item
```bash
maton api -X DELETE '/podio/item/{item_id}'
```

### Tasks

Tasks require at least one filter: org, space, app, responsible, reference, created_by, or completed_by.

#### List Tasks
```bash
maton api '/podio/task/?org={org_id}'
maton api '/podio/task/?space={space_id}'
maton api '/podio/task/?app={app_id}&completed=false'
```

#### Get Task
```bash
maton api '/podio/task/{task_id}'
```

#### Create Task
```bash
maton api -X POST '/podio/task/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "text": "Task description",
  "due_date": "2026-03-15",
  "ref_type": "item",
  "ref_id": 3250776079
}
EOF
```

#### Complete Task
```bash
maton api -X POST '/podio/task/{task_id}/complete'
```

#### Delete Task
```bash
maton api -X DELETE '/podio/task/{task_id}'
```

### Comments

#### Get Comments on Object
```bash
maton api '/podio/comment/{type}/{id}/'
```

Where `{type}` is: item, task, status, etc.

#### Add Comment
```bash
maton api -X POST '/podio/comment/{type}/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "value": "Comment text"
}
EOF
```

### User

#### Get User Status
```bash
maton api '/podio/user/status'
```

## Pagination

Podio uses offset-based pagination:

```json
{
  "limit": 30,
  "offset": 0
}
```

Response includes counts:
```json
{
  "total": 150,
  "filtered": 45,
  "items": [...]
}
```

## Notes

- Organization, space, app, and item IDs are integers
- Many endpoints use trailing slashes (e.g., `/org/`, `/filter/`)
- Category/status fields use option IDs (integers), not text values
- Field values can be specified by field_id or external_id
- Deleting an item cascades to associated tasks
- Tasks require at least one filter parameter
- `silent=true` suppresses notifications and `hook=false` skips webhook triggers — see the warning below before using either

## Resources

- [Podio API Documentation](https://developers.podio.com/doc)
- [Items API](https://developers.podio.com/doc/items)
- [Tasks API](https://developers.podio.com/doc/tasks)
- [Applications API](https://developers.podio.com/doc/applications)
- [Spaces API](https://developers.podio.com/doc/spaces)
