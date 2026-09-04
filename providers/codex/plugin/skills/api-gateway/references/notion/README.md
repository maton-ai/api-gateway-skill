# Notion Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `notion`
**Base URL proxied:** `api.notion.com`

## Required Headers

All Notion API requests require:
```
Notion-Version: 2025-09-03
```

## API Path Pattern

```
/notion/v1/{endpoint}
```

## Key Concept: Databases vs Data Sources

In API version 2025-09-03, databases and data sources are separate concepts:

| Concept | Description | Use For |
|---------|-------------|---------|
| **Database** | Container that can hold multiple data sources | Creating databases, getting data_source IDs |
| **Data Source** | Schema and data within a database | Querying, updating schema, updating properties |

Most existing databases have one data source. Use `GET /databases/{id}` to get the `data_source_id`, then use `/data_sources/` endpoints for all operations.

## Common Endpoints

### Search

Search for pages:
```bash
maton api -X POST '/notion/v1/search' \
  -H 'Content-Type: application/json' \
  -H 'Notion-Version: 2025-09-03' \
  --input - <<'EOF'
{
  "query": "meeting notes",
  "filter": {"property": "object", "value": "page"}
}
EOF
```

Example:

```bash
maton notion search 'meeting notes' --filter page
```

Search for data sources:
```bash
maton api -X POST '/notion/v1/search' \
  -H 'Content-Type: application/json' \
  -H 'Notion-Version: 2025-09-03' \
  --input - <<'EOF'
{
  "filter": {"property": "object", "value": "data_source"}
}
EOF
```

Example:

```bash
maton notion search --filter data_source
```

With pagination:
```bash
maton api -X POST '/notion/v1/search' \
  -H 'Content-Type: application/json' \
  -H 'Notion-Version: 2025-09-03' \
  --input - <<'EOF'
{
  "page_size": 10,
  "start_cursor": "CURSOR_FROM_PREVIOUS_RESPONSE"
}
EOF
```

### Data Sources

Use data source endpoints for querying, getting schema, and updates.

#### Get Data Source
```bash
maton api '/notion/v1/data_sources/{dataSourceId}' \
  -H 'Notion-Version: 2025-09-03'
```

Returns full schema with `properties` field.

Example:

```bash
maton notion data-source view {dataSourceId}
```

#### Query Data Source
```bash
maton api -X POST '/notion/v1/data_sources/{dataSourceId}/query' \
  -H 'Content-Type: application/json' \
  -H 'Notion-Version: 2025-09-03' \
  --input - <<'EOF'
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
EOF
```

Example:

```bash
maton notion data-source query {dataSourceId} \
  --filter '{"property":"Status","select":{"equals":"Active"}}' \
  --sorts '[{"property":"Created","direction":"descending"}]' \
  --page-size 100
```

#### Update Data Source (title, schema, properties)
```bash
maton api -X PATCH '/notion/v1/data_sources/{dataSourceId}' \
  -H 'Content-Type: application/json' \
  -H 'Notion-Version: 2025-09-03' \
  --input - <<'EOF'
{
  "title": [{"type": "text", "text": {"content": "Updated Title"}}],
  "properties": {
    "NewColumn": {"rich_text": {}}
  }
}
EOF
```

Example:

```bash
maton notion data-source update {dataSourceId} \
  --body '{"title":[{"type":"text","text":{"content":"Updated Title"}}],"properties":{"NewColumn":{"rich_text":{}}}}'
```

### Databases

Database endpoints are only needed for **creating** databases and **discovering** data source IDs.

#### Get Database (to find data_source_id)
```bash
maton api '/notion/v1/databases/{databaseId}' \
  -H 'Notion-Version: 2025-09-03'
```

Response includes `data_sources` array:
```json
{
  "id": "database-id",
  "object": "database",
  "data_sources": [{"id": "data-source-id", "name": "Database Name"}]
}
```

**Note:** This endpoint returns `properties: null`. Use `GET /data_sources/{id}` to get the schema.

Example:

```bash
maton notion database view {databaseId}
```

#### Create Database
```bash
maton api -X POST '/notion/v1/databases' \
  -H 'Content-Type: application/json' \
  -H 'Notion-Version: 2025-09-03' \
  --input - <<'EOF'
{
  "parent": {"type": "page_id", "page_id": "PARENT_PAGE_ID"},
  "title": [{"type": "text", "text": {"content": "New Database"}}],
  "properties": {
    "Name": {"title": {}},
    "Status": {"select": {"options": [{"name": "Active"}, {"name": "Done"}]}}
  }
}
EOF
```

**Important:** Cannot create databases via `/data_sources` endpoint. In API version 2025-09-03, `POST /databases` only accepts the title property — define schema afterward with `PATCH /data_sources/{dataSourceId}`.

Example:

```bash
maton notion database create --parent-page PARENT_PAGE_ID --title 'New Database'
```

### Pages

#### Get Page
```bash
maton api '/notion/v1/pages/{pageId}' \
  -H 'Notion-Version: 2025-09-03'
```

Example:

```bash
maton notion page view {pageId}
```

#### Create Page in Data Source
Use `data_source_id` (not `database_id`) as parent:
```bash
maton api -X POST '/notion/v1/pages' \
  -H 'Content-Type: application/json' \
  -H 'Notion-Version: 2025-09-03' \
  --input - <<'EOF'
{
  "parent": {"data_source_id": "DATA_SOURCE_ID"},
  "properties": {
    "Name": {"title": [{"text": {"content": "New Page"}}]},
    "Status": {"select": {"name": "Active"}}
  }
}
EOF
```

Example:

```bash
maton notion page create --data-source DATA_SOURCE_ID --title 'New Page' \
  --properties '{"Status":{"select":{"name":"Active"}}}'
```

#### Create Child Page (under another page)
```bash
maton api -X POST '/notion/v1/pages' \
  -H 'Content-Type: application/json' \
  -H 'Notion-Version: 2025-09-03' \
  --input - <<'EOF'
{
  "parent": {"page_id": "PARENT_PAGE_ID"},
  "properties": {
    "title": {"title": [{"text": {"content": "Child Page"}}]}
  }
}
EOF
```

Example:

```bash
maton notion page create --parent-page PARENT_PAGE_ID --title 'Child Page'
```

#### Update Page Properties
```bash
maton api -X PATCH '/notion/v1/pages/{pageId}' \
  -H 'Content-Type: application/json' \
  -H 'Notion-Version: 2025-09-03' \
  --input - <<'EOF'
{
  "properties": {
    "Status": {"select": {"name": "Done"}}
  }
}
EOF
```

Example:

```bash
maton notion page update {pageId} --properties '{"Status":{"select":{"name":"Done"}}}'
```

#### Archive Page
```bash
maton api -X PATCH '/notion/v1/pages/{pageId}' \
  -H 'Content-Type: application/json' \
  -H 'Notion-Version: 2025-09-03' \
  --input - <<'EOF'
{
  "archived": true
}
EOF
```

Example:

```bash
maton notion page archive {pageId}
```

### Blocks

#### Get Block
```bash
maton api '/notion/v1/blocks/{blockId}' \
  -H 'Notion-Version: 2025-09-03'
```

#### Get Block Children
```bash
maton api '/notion/v1/blocks/{blockId}/children' \
  -H 'Notion-Version: 2025-09-03'
```

Example:

```bash
maton notion block children {blockId}
```

#### Append Block Children
```bash
maton api -X PATCH '/notion/v1/blocks/{blockId}/children' \
  -H 'Content-Type: application/json' \
  -H 'Notion-Version: 2025-09-03' \
  --input - <<'EOF'
{
  "children": [
    {
      "object": "block",
      "type": "paragraph",
      "paragraph": {
        "rich_text": [{"type": "text", "text": {"content": "New paragraph"}}]
      }
    },
    {
      "object": "block",
      "type": "heading_2",
      "heading_2": {
        "rich_text": [{"type": "text", "text": {"content": "Heading"}}]
      }
    }
  ]
}
EOF
```

Example:

```bash
maton notion block append {blockId} \
  --children '[{"object":"block","type":"paragraph","paragraph":{"rich_text":[{"type":"text","text":{"content":"New paragraph"}}]}}]'
```

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

#### Delete Block
```bash
maton api -X DELETE '/notion/v1/blocks/{blockId}' \
  -H 'Notion-Version: 2025-09-03'
```

Example:

```bash
maton notion block delete {blockId}
```

### Users

> **Privacy — this is a workspace directory.** These endpoints enumerate every member and guest, returning names, email addresses, and avatars. The result is effectively an org roster: useful for resolving one person, but also a ready-made contact list.
> - Query for the specific person the task needs (prefer Get User by ID, or filter the result) rather than listing everyone.
> - Do not print the full member list into output, save it to a file, or forward it to any third-party host unless the user explicitly asked for a roster.
> - Member email addresses are personal data — don't reuse them for outreach, enrichment, or any purpose outside the stated task.

#### List Users
```bash
maton api '/notion/v1/users' \
  -H 'Notion-Version: 2025-09-03'
```

Example:

```bash
maton notion user list
```

#### Get User by ID
```bash
maton api '/notion/v1/users/{userId}' \
  -H 'Notion-Version: 2025-09-03'
```

#### Get Current User (Bot)
```bash
maton api '/notion/v1/users/me' \
  -H 'Notion-Version: 2025-09-03'
```

Example:

```bash
maton notion whoami
```

## Filter Operators

- `equals`, `does_not_equal`
- `contains`, `does_not_contain`
- `starts_with`, `ends_with`
- `is_empty`, `is_not_empty`
- `greater_than`, `less_than`, `greater_than_or_equal_to`, `less_than_or_equal_to`

## Block Types

Common block types for appending:
- `paragraph` - Text paragraph
- `heading_1`, `heading_2`, `heading_3` - Headings
- `bulleted_list_item`, `numbered_list_item` - List items
- `to_do` - Checkbox item
- `code` - Code block
- `quote` - Quote block
- `divider` - Horizontal divider

## Migration from Older API Versions

| Old (2022-06-28) | New (2025-09-03) |
|------------------|------------------|
| `POST /databases/{id}/query` | `POST /data_sources/{id}/query` |
| `GET /databases/{id}` for schema | `GET /data_sources/{id}` for schema |
| `PATCH /databases/{id}` for schema | `PATCH /data_sources/{id}` for schema |
| Parent: `{"database_id": "..."}` | Parent: `{"data_source_id": "..."}` |
| Search filter: `"database"` | Search filter: `"data_source"` |

## Pagination

Notion uses cursor-based pagination. The CLI handles this automatically with `--paginate`:

```bash
maton notion data-source query {dataSourceId} --paginate
```

For raw HTTP requests, pass the `next_cursor` from the previous response as `start_cursor` in the next request.

## Notes

- Use `GET /databases/{id}` to discover `data_source_id`, then use `/data_sources/` for all operations
- Creating databases still requires `POST /databases` endpoint
- Parent objects for create database require `type` field: `{"type": "page_id", "page_id": "..."}`
- All IDs are UUIDs (with or without hyphens)
- Delete blocks returns the block with `archived: true`

## Resources

- [API Introduction](https://developers.notion.com/reference/intro)
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