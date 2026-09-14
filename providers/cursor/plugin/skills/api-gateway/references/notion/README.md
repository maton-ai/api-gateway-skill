# Notion

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `notion`
**Upstream base URL:** `api.notion.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.notion.com/v1/search`
- Gateway: `https://api.maton.ai/notion/v1/search`

**Important:** All requests require `Notion-Version` header.

### Key Concept: Databases vs Data Sources

In API version 2025-09-03, databases and data sources are separate concepts:

| Concept | Description | Use For |
|---------|-------------|---------|
| **Database** | Container that can hold multiple data sources | Creating databases, getting data_source IDs |
| **Data Source** | Schema and data within a database | Querying, updating schema, updating properties |

Most existing databases have one data source. Use `GET /databases/{id}` to get the `data_source_id`, then use `/data_sources/` endpoints for all operations.

### Search API

#### Search Pages

```bash
maton notion search 'meeting notes' --filter page
```

Or with `maton api`:

```bash
maton api -X POST '/notion/v1/search' -H 'Notion-Version: 2025-09-03' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "meeting notes",
  "filter": {"property": "object", "value": "page"}
}
JSON
```

#### Search Data Sources

```bash
maton notion search --filter data_source
```

Or with `maton api`:

```bash
maton api -X POST '/notion/v1/search' -H 'Notion-Version: 2025-09-03' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "filter": {"property": "object", "value": "data_source"}
}
JSON
```

### Data Sources API

#### Get Data Source

```bash
maton notion data-source get {dataSourceId}
```

Or with `maton api`:

```bash
maton api '/notion/v1/data_sources/{dataSourceId}' -H 'Notion-Version: 2025-09-03'
```

**Note:** `{dataSourceId}` is a placeholder. Replace it with a real value before sending the request.

#### Query Data Sources

```bash
maton notion data-source query <dataSourceId> \
  --filter '{"property":"Status","select":{"equals":"Active"}}' \
  --sorts '[{"property":"Created","direction":"descending"}]' \
  --page-size 100
```

Or with `maton api`:

```bash
maton api -X POST '/notion/v1/data_sources/{dataSourceId}/query' -H 'Notion-Version: 2025-09-03' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "filter": {
    "property": "Status",
    "select": {"equals": "Active"}
  },
  "sorts": [
    {"property": "Created", "direction": "descending"}
  ],
  "page_size": 100
}
JSON
```

**Note:** `{dataSourceId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Data Source

```bash
maton notion data-source update <dataSourceId> \
  --body '{"title":[{"type":"text","text":{"content":"Updated Title"}}],"properties":{"NewColumn":{"rich_text":{}}}}'
```

Or with `maton api`:

```bash
maton api -X PATCH '/notion/v1/data_sources/{dataSourceId}' -H 'Notion-Version: 2025-09-03' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": [{"type": "text", "text": {"content": "Updated Title"}}],
  "properties": {
    "NewColumn": {"rich_text": {}}
  }
}
JSON
```

**Note:** `{dataSourceId}` is a placeholder. Replace it with a real value before sending the request.

### Databases API

#### Get Database

```bash
maton notion database get {databaseId}
```

Or with `maton api`:

```bash
maton api '/notion/v1/databases/{databaseId}' -H 'Notion-Version: 2025-09-03'
```

**Note:** `{databaseId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Database

```bash
maton notion database create --parent-page PARENT_PAGE_ID --title 'New Database'
```

Or with `maton api`:

```bash
maton api -X POST '/notion/v1/databases' -H 'Notion-Version: 2025-09-03' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "parent": {"type": "page_id", "page_id": "PARENT_PAGE_ID"},
  "title": [{"type": "text", "text": {"content": "New Database"}}],
  "properties": {
    "Name": {"title": {}}
  }
}
JSON
```

In API version 2025-09-03, `POST /databases` only accepts the title property — any other entries in `properties` are silently dropped. To define a schema, follow up with `PATCH /data_sources/{dataSourceId}` (see [Update Data Source](#update-data-source)) using the `data_sources[0].id` returned by the create call.

### Pages API

#### Get Page

```bash
maton notion page get {pageId}
```

Or with `maton api`:

```bash
maton api '/notion/v1/pages/{pageId}' -H 'Notion-Version: 2025-09-03'
```

**Note:** `{pageId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Page

```bash
maton notion page create --parent-page PARENT_PAGE_ID --title 'New Page'
```

Or with `maton api`:

```bash
maton api -X POST '/notion/v1/pages' -H 'Notion-Version: 2025-09-03' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "parent": {"page_id": "PARENT_PAGE_ID"},
  "properties": {
    "title": {"title": [{"text": {"content": "New Page"}}]}
  }
}
JSON
```

#### Create Page in Data Source

```bash
maton notion page create --data-source DATA_SOURCE_ID --title 'New Page' \
  --properties '{"Status":{"select":{"name":"Active"}}}'
```

Or with `maton api`:

```bash
maton api -X POST '/notion/v1/pages' -H 'Notion-Version: 2025-09-03' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "parent": {"data_source_id": "DATA_SOURCE_ID"},
  "properties": {
    "Name": {"title": [{"text": {"content": "New Page"}}]},
    "Status": {"select": {"name": "Active"}}
  }
}
JSON
```

#### Update Page Properties

```bash
maton notion page update {pageId} --properties '{"Status":{"select":{"name":"Done"}}}'
```

Or with `maton api`:

```bash
maton api -X PATCH '/notion/v1/pages/{pageId}' -H 'Notion-Version: 2025-09-03' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "properties": {
    "Status": {"select": {"name": "Done"}}
  }
}
JSON
```

**Note:** `{pageId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Page Icon

```bash
maton notion page update {pageId} --icon 🚀
```

Or with `maton api`:

```bash
maton api -X PATCH '/notion/v1/pages/{pageId}' -H 'Notion-Version: 2025-09-03' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "icon": {"type": "emoji", "emoji": "🚀"}
}
JSON
```

**With an image URL:**

```bash
maton notion page update {pageId} --icon https://example.com/icon.png
```

**Note:** `{pageId}` is a placeholder. Replace it with a real value before sending the request.

#### Archive Page

```bash
maton notion page archive {pageId}
```

Or with `maton api`:

```bash
maton api -X PATCH '/notion/v1/pages/{pageId}' -H 'Notion-Version: 2025-09-03' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "archived": true
}
JSON
```

**Note:** `{pageId}` is a placeholder. Replace it with a real value before sending the request.

### Blocks API

#### Get Block Children

```bash
maton notion block children {blockId}
```

Or with `maton api`:

```bash
maton api '/notion/v1/blocks/{blockId}/children' -H 'Notion-Version: 2025-09-03'
```

**Note:** `{blockId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Block

```bash
maton api '/notion/v1/blocks/{blockId}' \
  -H 'Notion-Version: 2025-09-03'
```

**Note:** `{blockId}` is a placeholder. Replace it with a real value before sending the request.

#### Append Block Children

```bash
maton notion block append <blockId> \
  --children '[{"object":"block","type":"paragraph","paragraph":{"rich_text":[{"type":"text","text":{"content":"New paragraph"}}]}}]'
```

Or with `maton api`:

```bash
maton api -X PATCH '/notion/v1/blocks/{blockId}/children' -H 'Notion-Version: 2025-09-03' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "children": [
    {
      "object": "block",
      "type": "paragraph",
      "paragraph": {
        "rich_text": [{"type": "text", "text": {"content": "New paragraph"}}]
      }
    }
  ]
}
JSON
```

**Note:** `{blockId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Block

```bash
maton api -X PATCH '/notion/v1/blocks/{blockId}' \
  -H 'Content-Type: application/json' \
  -H 'Notion-Version: 2025-09-03' \
  --input - <<'EOF'
{
  "paragraph": {
    "rich_text": [{"text": {"content": "Updated text"}}]
  }
}
EOF
```

**Note:** `{blockId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Block

```bash
maton notion block delete {blockId}
```

Or with `maton api`:

```bash
maton api '/notion/v1/blocks/{blockId}' -X DELETE -H 'Notion-Version: 2025-09-03'
```

**Note:** `{blockId}` is a placeholder. Replace it with a real value before sending the request.

### Users API

#### List Users

```bash
maton notion user list
```

Or with `maton api`:

```bash
maton api '/notion/v1/users' -H 'Notion-Version: 2025-09-03'
```

#### Get User

```bash
maton api '/notion/v1/users/{userId}' \
  -H 'Notion-Version: 2025-09-03'
```

**Note:** `{userId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Current User

```bash
maton notion whoami
```

Or with `maton api`:

```bash
maton api '/notion/v1/users/me' -H 'Notion-Version: 2025-09-03'
```

### Filter Operators

- `equals`, `does_not_equal`
- `contains`, `does_not_contain`
- `starts_with`, `ends_with`
- `is_empty`, `is_not_empty`
- `greater_than`, `less_than`

### Block Types

- `paragraph`, `heading_1`, `heading_2`, `heading_3`
- `bulleted_list_item`, `numbered_list_item`
- `to_do`, `code`, `quote`, `divider`

### Pagination

Notion uses cursor-based pagination. The CLI automatically paginates with '--paginate'.

Example:

```bash
maton notion data-source query <dataSourceId> --paginate
```

### Examples

```bash
# Search for pages matching a query
maton notion search 'roadmap'

# View a specific page
maton notion page get 0123456789abcdef0123456789abcdef

# Query a data source with a filter
maton notion data-source query <dataSourceId> --filter '{"property":"Status","select":{"equals":"Active"}}'

# Filter with jq — e.g., only pages (responses are wrapped in {"results": [...]})
# Note: --jq requires --json
maton notion search 'roadmap' --json --jq '.results | map(select(.object == "page"))'
```

### Notes

- All IDs are UUIDs (with or without hyphens)
- Use `GET /databases/{id}` to discover `data_source_id`, then use `/data_sources/` for all operations
- Creating databases requires `POST /databases` endpoint
- Parent objects for create database require `type` field: `{"type": "page_id", "page_id": "..."}`
- Delete blocks returns the block with `archived: true`

### Resources

- [Notion API Introduction](https://developers.notion.com/reference/intro)
- [Search](https://developers.notion.com/reference/post-search.md)
- [Query Database](https://developers.notion.com/reference/post-database-query.md)
- [Get Database](https://developers.notion.com/reference/retrieve-a-database.md)
- [Create Database](https://developers.notion.com/reference/create-a-database.md)
- [Get Page](https://developers.notion.com/reference/retrieve-a-page.md)
- [Create Page](https://developers.notion.com/reference/post-page.md)
- [Update Page](https://developers.notion.com/reference/patch-page.md)
- [Get Block Children](https://developers.notion.com/reference/get-block-children.md)
- [Append Block Children](https://developers.notion.com/reference/patch-block-children.md)
- [List Users](https://developers.notion.com/reference/get-users.md)
- [Filter Reference](https://developers.notion.com/reference/post-database-query-filter.md)
- [LLM Reference](https://developers.notion.com/llms.txt)
- [Version Reference](https://developers.notion.com/guides/get-started/upgrade-guide-2025-09-03)
- [Maton CLI Manual](https://cli.maton.ai/manual)
