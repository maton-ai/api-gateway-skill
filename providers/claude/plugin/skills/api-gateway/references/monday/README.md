# Monday.com

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `monday`
**Upstream base URL:** `api.monday.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.monday.com/v2`
- Gateway: `https://api.maton.ai/monday/v2`

**Important:** Monday.com uses a GraphQL API. All requests are POST requests to the `/v2` endpoint with a JSON body containing the `query` field.

### GraphQL API

#### Get Current User

```bash
maton api -X POST '/monday/v2' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ me { id name email } }"}
JSON
```

**Response:**
```json
{
  "data": {
    "me": {
      "id": "72989582",
      "name": "Chris",
      "email": "chris.kim.2332@gmail.com"
    }
  }
}
```

#### List Users

```bash
maton api -X POST '/monday/v2' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ users(limit: 20) { id name email } }"}
JSON
```

#### List Workspaces

```bash
maton api -X POST '/monday/v2' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ workspaces(limit: 10) { id name kind } }"}
JSON
```

**Response:**
```json
{
  "data": {
    "workspaces": [
      { "id": "10136488", "name": "Main workspace", "kind": "open" }
    ]
  }
}
```

#### List Boards

```bash
maton api -X POST '/monday/v2' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ boards(limit: 10) { id name state board_kind workspace { id name } } }"}
JSON
```

**Response:**
```json
{
  "data": {
    "boards": [
      {
        "id": "8614733398",
        "name": "Welcome to your developer account",
        "state": "active",
        "board_kind": "public",
        "workspace": { "id": "10136488", "name": "Main workspace" }
      }
    ]
  }
}
```

#### Get Board with Columns, Groups, and Items

```bash
maton api -X POST '/monday/v2' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ boards(ids: [BOARD_ID]) { id name columns { id title type } groups { id title } items_page(limit: 20) { cursor items { id name state } } } }"}
JSON
```

#### Create Board

```bash
maton api -X POST '/monday/v2' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "mutation { create_board(board_name: \"New Board\", board_kind: public) { id name } }"}
JSON
```

**Response:**
```json
{
  "data": {
    "create_board": {
      "id": "18398921201",
      "name": "New Board"
    }
  }
}
```

#### Update Board

```bash
maton api -X POST '/monday/v2' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "mutation { update_board(board_id: BOARD_ID, board_attribute: description, new_value: \"Board description\") }"}
JSON
```

#### Delete Board

```bash
maton api -X POST '/monday/v2' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "mutation { delete_board(board_id: BOARD_ID) { id } }"}
JSON
```

#### Get Items by ID

```bash
maton api -X POST '/monday/v2' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ items(ids: [ITEM_ID]) { id name created_at updated_at state board { id name } group { id title } column_values { id text value } } }"}
JSON
```

**Response:**
```json
{
  "data": {
    "items": [
      {
        "id": "11200791874",
        "name": "Test item",
        "created_at": "2026-02-05T20:12:42Z",
        "updated_at": "2026-02-05T20:12:42Z",
        "state": "active",
        "board": { "id": "8614733398", "name": "Welcome to your developer account" },
        "group": { "id": "topics", "title": "Group Title" }
      }
    ]
  }
}
```

#### Create Item

```bash
maton api -X POST '/monday/v2' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "mutation { create_item(board_id: BOARD_ID, group_id: \"GROUP_ID\", item_name: \"New item\") { id name } }"}
JSON
```

#### Create Item with Column Values

```bash
maton api -X POST '/monday/v2' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "mutation { create_item(board_id: BOARD_ID, group_id: \"GROUP_ID\", item_name: \"New task\", column_values: \"{\\\"status\\\": {\\\"label\\\": \\\"Working on it\\\"}}\") { id name column_values { id text } } }"}
JSON
```

#### Update Item Name

```bash
maton api -X POST '/monday/v2' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "mutation { change_simple_column_value(board_id: BOARD_ID, item_id: ITEM_ID, column_id: \"name\", value: \"Updated name\") { id name } }"}
JSON
```

#### Update Column Value

```bash
maton api -X POST '/monday/v2' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "mutation { change_column_value(board_id: BOARD_ID, item_id: ITEM_ID, column_id: \"status\", value: \"{\\\"label\\\": \\\"Done\\\"}\") { id name } }"}
JSON
```

#### Delete Item

```bash
maton api -X POST '/monday/v2' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "mutation { delete_item(item_id: ITEM_ID) { id } }"}
JSON
```

#### Create Column

```bash
maton api -X POST '/monday/v2' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "mutation { create_column(board_id: BOARD_ID, title: \"Status\", column_type: status) { id title type } }"}
JSON
```

**Response:**
```json
{
  "data": {
    "create_column": {
      "id": "color_mm09e48w",
      "title": "Status",
      "type": "status"
    }
  }
}
```

**Common column types:** `status`, `text`, `numbers`, `date`, `people`, `dropdown`, `checkbox`, `email`, `phone`, `link`, `timeline`, `tags`, `rating`

#### Create Group

```bash
maton api -X POST '/monday/v2' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "mutation { create_group(board_id: BOARD_ID, group_name: \"New Group\") { id title } }"}
JSON
```

**Response:**
```json
{
  "data": {
    "create_group": {
      "id": "group_mm0939df",
      "title": "New Group"
    }
  }
}
```

### Pagination

Monday.com uses cursor-based pagination for items with `items_page` and `next_items_page`.

```bash
# First page
maton api -X POST '/monday/v2' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ boards(ids: [BOARD_ID]) { items_page(limit: 50) { cursor items { id name } } } }"}
JSON

# Next page using cursor
maton api -X POST '/monday/v2' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ next_items_page(cursor: \"CURSOR_VALUE\", limit: 50) { cursor items { id name } } }"}
JSON
```

Response includes `cursor` when more items exist (null when no more pages):

```json
{
  "data": {
    "boards": [{
      "items_page": {
        "cursor": "MSw5NzI4...",
        "items": [...]
      }
    }]
  }
}
```

### Notes

- Monday.com uses GraphQL exclusively (no REST API)
- Board IDs, item IDs, and user IDs are numeric strings
- Column IDs are alphanumeric strings (e.g., `color_mm09e48w`)
- Group IDs are alphanumeric strings (e.g., `group_mm0939df`, `topics`)
- Column values must be passed as JSON strings when creating/updating items
- The `account` query may require additional OAuth scopes. If you receive a scope error, contact Maton support at support@maton.ai with the specific operations/APIs you need and your use-case
- Board kinds: `public`, `private`, `share`
- Board states: `active`, `archived`, `deleted`, `all`
- Each cursor is valid for 60 minutes after the initial request
- Default limit is 25, maximum is 100 for most queries

### Resources

- [Monday.com API Basics](https://developer.monday.com/api-reference/docs/basics)
- [GraphQL Overview](https://developer.monday.com/api-reference/docs/introduction-to-graphql)
- [Boards Reference](https://developer.monday.com/api-reference/reference/boards)
- [Items Reference](https://developer.monday.com/api-reference/reference/items)
- [Columns Reference](https://developer.monday.com/api-reference/reference/columns)
- [Maton CLI Manual](https://cli.maton.ai/manual)
