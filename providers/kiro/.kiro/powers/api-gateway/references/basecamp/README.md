# Basecamp

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `basecamp`
**Upstream base URL:** `3.basecampapi.com/{account_id}`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://3.basecampapi.com/{account_id}/my/profile.json`
- Gateway: `https://api.maton.ai/basecamp/my/profile.json`

**Important:** All paths must end with `.json`.

### Key Concepts

#### Buckets and Projects

A "bucket" is a project's content container. The bucket ID is the same as the project ID in URLs:

```
/buckets/{project_id}/todosets/{todoset_id}.json
```

#### Dock

Each project has a "dock" containing available tools. Always check that a tool is `enabled: true` before using it:

```json
{
  "dock": [
    {"name": "todoset", "id": 123, "enabled": true},
    {"name": "message_board", "id": 456, "enabled": false}
  ]
}
```

#### Recordings

All content items (todos, messages, documents, comments, etc.) are "recordings" with:
- `status`: "active", "archived", or "trashed"
- `parent`: navigation to container
- Unique IDs that can be used across endpoints

### User Info API

#### Get Current User

```bash
maton api '/basecamp/my/profile.json'
```

**Response:**
```json
{
  "id": 51197030,
  "name": "Chris Kim",
  "email_address": "chris@example.com",
  "admin": true,
  "owner": true,
  "time_zone": "America/Los_Angeles",
  "avatar_url": "https://..."
}
```

### People API

#### List People

```bash
maton api '/basecamp/people.json'
```

**Response:**
```json
[
  {
    "id": 51197030,
    "name": "Chris Kim",
    "email_address": "chris@example.com",
    "admin": true,
    "owner": true,
    "employee": true,
    "time_zone": "America/Los_Angeles"
  }
]
```

#### Get Person

```bash
maton api '/basecamp/people/{person_id}.json'
```

**Note:** `{person_id}` is a placeholder. Replace it with a real value before sending the request.

#### List Project Members

```bash
maton api '/basecamp/projects/{project_id}/people.json'
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

### Project API

#### List Projects

```bash
maton api '/basecamp/projects.json'
```

**Response:**
```json
[
  {
    "id": 46005636,
    "status": "active",
    "name": "Getting Started",
    "description": "Quickly get up to speed with everything Basecamp",
    "created_at": "2026-02-05T22:59:26.087Z",
    "url": "https://3.basecampapi.com/6153810/projects/46005636.json",
    "dock": [...]
  }
]
```

#### Get Project

```bash
maton api '/basecamp/projects/{project_id}.json'
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

Returns project with `dock` array containing tool IDs.

The project response includes a `dock` array with available tools (message_board, todoset, vault, chat, schedule, etc.). Each dock item has:
- `id`: The tool's ID
- `name`: Tool type (e.g., "todoset", "message_board")
- `enabled`: Whether the tool is active
- `url`: Direct URL to access the tool

#### Create Project

```bash
maton api -X POST '/basecamp/projects.json' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Project",
  "description": "Project description"
}
JSON
```

#### Update Project

```bash
maton api -X PUT '/basecamp/projects/{project_id}.json' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Project Name",
  "description": "Updated description"
}
JSON
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete (Trash) Project

```bash
maton api '/basecamp/projects/{project_id}.json' -X DELETE
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

### To-Do API

#### Get Todoset

First, get the todoset ID from the project's dock:

```bash
maton api '/basecamp/buckets/{project_id}/todosets/{todoset_id}.json'
```

**Note:** `{project_id}` and `{todoset_id}` are placeholders. Replace each of them with real values before sending the request.

#### List Todolists

```bash
maton api '/basecamp/buckets/{project_id}/todosets/{todoset_id}/todolists.json'
```

**Note:** `{project_id}` and `{todoset_id}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
[
  {
    "id": 9550474442,
    "title": "Basecamp essentials",
    "description": "",
    "completed": false,
    "completed_ratio": "0/5",
    "url": "https://..."
  }
]
```

#### Create Todolist

```bash
maton api -X POST '/basecamp/buckets/{project_id}/todosets/{todoset_id}/todolists.json' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Todo List",
  "description": "List description"
}
JSON
```

**Note:** `{project_id}` and `{todoset_id}` are placeholders. Replace each of them with real values before sending the request.

#### Get Todolist

```bash
maton api '/basecamp/buckets/{project_id}/todolists/{todolist_id}.json'
```

**Note:** `{project_id}` and `{todolist_id}` are placeholders. Replace each of them with real values before sending the request.

#### List Todos

```bash
maton api '/basecamp/buckets/{project_id}/todolists/{todolist_id}/todos.json'
```

**Note:** `{project_id}` and `{todolist_id}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
[
  {
    "id": 9550474446,
    "content": "Start here",
    "description": "",
    "completed": false,
    "due_on": null,
    "assignees": []
  }
]
```

#### Create Todo

```bash
maton api -X POST '/basecamp/buckets/{project_id}/todolists/{todolist_id}/todos.json' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "content": "New todo item",
  "description": "Todo description",
  "due_on": "2026-02-15",
  "assignee_ids": [51197030]
}
JSON
```

**Note:** `{project_id}` and `{todolist_id}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "id": 9555973289,
  "content": "New todo item",
  "completed": false
}
```

#### Update Todo

```bash
maton api -X PUT '/basecamp/buckets/{project_id}/todos/{todo_id}.json' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "content": "Updated todo",
  "description": "Updated description"
}
JSON
```

**Note:** `{project_id}` and `{todo_id}` are placeholders. Replace each of them with real values before sending the request.

#### Complete Todo

```bash
maton api -X POST '/basecamp/buckets/{project_id}/todos/{todo_id}/completion.json'
```

**Note:** `{project_id}` and `{todo_id}` are placeholders. Replace each of them with real values before sending the request.

Returns 204 on success.

#### Uncomplete Todo

```bash
maton api '/basecamp/buckets/{project_id}/todos/{todo_id}/completion.json' -X DELETE
```

**Note:** `{project_id}` and `{todo_id}` are placeholders. Replace each of them with real values before sending the request.

### Message Board API

#### Get Message Board

```bash
maton api '/basecamp/buckets/{project_id}/message_boards/{message_board_id}.json'
```

**Note:** `{project_id}` and `{message_board_id}` are placeholders. Replace each of them with real values before sending the request.

#### List Messages

```bash
maton api '/basecamp/buckets/{project_id}/message_boards/{message_board_id}/messages.json'
```

**Note:** `{project_id}` and `{message_board_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Message

```bash
maton api -X POST '/basecamp/buckets/{project_id}/message_boards/{message_board_id}/messages.json' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "subject": "Message Subject",
  "content": "<p>Message body with HTML</p>",
  "category_id": 123
}
JSON
```

**Note:** `{project_id}` and `{message_board_id}` are placeholders. Replace each of them with real values before sending the request.

#### Get Message

```bash
maton api '/basecamp/buckets/{project_id}/messages/{message_id}.json'
```

**Note:** `{project_id}` and `{message_id}` are placeholders. Replace each of them with real values before sending the request.

#### Update Message

```bash
maton api -X PUT '/basecamp/buckets/{project_id}/messages/{message_id}.json' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "subject": "Updated Subject",
  "content": "<p>Updated content</p>"
}
JSON
```

**Note:** `{project_id}` and `{message_id}` are placeholders. Replace each of them with real values before sending the request.

### Schedule API

#### Get Schedule

```bash
maton api '/basecamp/buckets/{project_id}/schedules/{schedule_id}.json'
```

**Note:** `{project_id}` and `{schedule_id}` are placeholders. Replace each of them with real values before sending the request.

#### List Schedule Entries

```bash
maton api '/basecamp/buckets/{project_id}/schedules/{schedule_id}/entries.json'
```

**Note:** `{project_id}` and `{schedule_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Schedule Entry

```bash
maton api -X POST '/basecamp/buckets/{project_id}/schedules/{schedule_id}/entries.json' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "summary": "Team Meeting",
  "description": "Weekly sync",
  "starts_at": "2026-02-15T14:00:00Z",
  "ends_at": "2026-02-15T15:00:00Z",
  "all_day": false,
  "participant_ids": [51197030]
}
JSON
```

**Note:** `{project_id}` and `{schedule_id}` are placeholders. Replace each of them with real values before sending the request.

#### Update Schedule Entry

```bash
maton api -X PUT '/basecamp/buckets/{project_id}/schedule_entries/{entry_id}.json' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "summary": "Updated Meeting",
  "starts_at": "2026-02-15T15:00:00Z",
  "ends_at": "2026-02-15T16:00:00Z"
}
JSON
```

**Note:** `{project_id}` and `{entry_id}` are placeholders. Replace each of them with real values before sending the request.

### Vault API

#### Get Vault

```bash
maton api '/basecamp/buckets/{project_id}/vaults/{vault_id}.json'
```

**Note:** `{project_id}` and `{vault_id}` are placeholders. Replace each of them with real values before sending the request.

#### List Documents in Vault

```bash
maton api '/basecamp/buckets/{project_id}/vaults/{vault_id}/documents.json'
```

**Note:** `{project_id}` and `{vault_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Document

```bash
maton api -X POST '/basecamp/buckets/{project_id}/vaults/{vault_id}/documents.json' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Document Title",
  "content": "<p>Document content with HTML</p>"
}
JSON
```

**Note:** `{project_id}` and `{vault_id}` are placeholders. Replace each of them with real values before sending the request.

#### List Uploads in Vault

```bash
maton api '/basecamp/buckets/{project_id}/vaults/{vault_id}/uploads.json'
```

**Note:** `{project_id}` and `{vault_id}` are placeholders. Replace each of them with real values before sending the request.

### Campfire API

#### List Campfires

```bash
maton api '/basecamp/chats.json'
```

#### Get Campfire

```bash
maton api '/basecamp/buckets/{project_id}/chats/{chat_id}.json'
```

**Note:** `{project_id}` and `{chat_id}` are placeholders. Replace each of them with real values before sending the request.

#### List Campfire Lines (Messages)

```bash
maton api '/basecamp/buckets/{project_id}/chats/{chat_id}/lines.json'
```

**Note:** `{project_id}` and `{chat_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Campfire Line

```bash
maton api -X POST '/basecamp/buckets/{project_id}/chats/{chat_id}/lines.json' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "content": "Hello from the API!"
}
JSON
```

**Note:** `{project_id}` and `{chat_id}` are placeholders. Replace each of them with real values before sending the request.

### Comments API

#### List Comments on Recording

```bash
maton api '/basecamp/buckets/{project_id}/recordings/{recording_id}/comments.json'
```

**Note:** `{project_id}` and `{recording_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Comment

```bash
maton api -X POST '/basecamp/buckets/{project_id}/recordings/{recording_id}/comments.json' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "content": "<p>Comment text</p>"
}
JSON
```

**Note:** `{project_id}` and `{recording_id}` are placeholders. Replace each of them with real values before sending the request.

### Recording Status API

#### Trash Recording

```bash
maton api -X PUT '/basecamp/buckets/{project_id}/recordings/{recording_id}/status/trashed.json'
```

**Note:** `{project_id}` and `{recording_id}` are placeholders. Replace each of them with real values before sending the request.

#### Archive Recording

```bash
maton api -X PUT '/basecamp/buckets/{project_id}/recordings/{recording_id}/status/archived.json'
```

**Note:** `{project_id}` and `{recording_id}` are placeholders. Replace each of them with real values before sending the request.

#### Unarchive Recording

```bash
maton api -X PUT '/basecamp/buckets/{project_id}/recordings/{recording_id}/status/active.json'
```

**Note:** `{project_id}` and `{recording_id}` are placeholders. Replace each of them with real values before sending the request.

### Templates API

#### List Templates

```bash
maton api '/basecamp/templates.json'
```

#### Create Project from Template

```bash
maton api -X POST '/basecamp/templates/{template_id}/project_constructions.json' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Project from Template",
  "description": "Description"
}
JSON
```

**Note:** `{template_id}` is a placeholder. Replace it with a real value before sending the request.

### Pagination

Basecamp uses Link header pagination with `rel="next"`:

**Response Headers:**
```
Link: <https://3.basecampapi.com/.../page=2>; rel="next"
X-Total-Count: 150
```

Follow the `Link` header URL for the next page. When `next` is absent, you've reached the last page.

**Important:** Do not construct pagination URLs manually. Always use the URL provided in the `Link` header.

### Notes

- All API paths must end with `.json`
- Maton automatically injects the account ID
- Uses Basecamp 4 API (bc3-api)
- Timestamps are in ISO 8601 format
- HTML content uses `<div>`, `<p>`, `<strong>`, `<em>`, `<a>`, `<ul>`, `<ol>`, `<li>` tags
- Rate limit: ~50 requests per 10 seconds per IP
- Check `enabled: true` in dock before using tools

### Resources

- [Basecamp API Documentation](https://github.com/basecamp/bc3-api)
- [Basecamp API Endpoints](https://github.com/basecamp/bc3-api#endpoints)
- [Maton CLI Manual](https://cli.maton.ai/manual)
