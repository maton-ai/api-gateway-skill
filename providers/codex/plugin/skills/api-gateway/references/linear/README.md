# Linear Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `linear`
**Base URL proxied:** `api.linear.app`

## API Type

Linear uses a GraphQL API exclusively. All requests are POST requests to the `/graphql` endpoint.

## API Path Pattern

```
/linear/graphql
```

All operations use POST with a JSON body containing the `query` field.

## Common Operations

### Get Current User (Viewer)
```bash
maton api -X POST '/linear/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "{ viewer { id name email } }"
}
EOF
```

Example:

```bash
maton linear whoami
```

### Get Organization
```bash
maton api -X POST '/linear/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "{ organization { id name urlKey } }"
}
EOF
```

Example:

```bash
maton linear org view
```

### List Teams
```bash
maton api -X POST '/linear/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "{ teams { nodes { id name key } } }"
}
EOF
```

Example:

```bash
maton linear team list
```

### List Issues
```bash
maton api -X POST '/linear/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "{ issues(first: 20) { nodes { id identifier title state { name } priority } pageInfo { hasNextPage endCursor } } }"
}
EOF
```

Example:

```bash
maton linear issue list -c ABC -L 20
```

### Get Issue by Identifier
```bash
maton api -X POST '/linear/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "{ issue(id: \"MTN-527\") { id identifier title description state { name } priority assignee { name } team { key } createdAt } }"
}
EOF
```

Example:

```bash
maton linear issue view MTN-527
```

### Filter Issues by State
```bash
maton api -X POST '/linear/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "{ issues(first: 20, filter: { state: { type: { eq: \"started\" } } }) { nodes { id identifier title state { name } } } }"
}
EOF
```

Example:

```bash
maton linear issue list --state started -L 20
```

### Search Issues
```bash
maton api -X POST '/linear/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "{ searchIssues(first: 20, term: \"search term\") { nodes { id identifier title } } }"
}
EOF
```

Example:

```bash
maton linear issue search 'search term' -L 20
```

### Create Issue
```bash
maton api -X POST '/linear/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "mutation { issueCreate(input: { teamId: \"TEAM_ID\", title: \"Issue title\", description: \"Description\" }) { success issue { id identifier title } } }"
}
EOF
```

Example:

```bash
maton linear issue create --team-id TEAM_ID -t 'Issue title'
```

### Update Issue
```bash
maton api -X POST '/linear/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "mutation { issueUpdate(id: \"ISSUE_ID\", input: { title: \"Updated title\", priority: 2 }) { success issue { id identifier title priority } } }"
}
EOF
```

Example:

```bash
maton linear issue update ISSUE_ID -t 'Updated title' --priority 2
```

### Create Comment
```bash
maton api -X POST '/linear/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "mutation { commentCreate(input: { issueId: \"ISSUE_ID\", body: \"Comment text\" }) { success comment { id body } } }"
}
EOF
```

Example:

```bash
maton linear comment create --issue ISSUE_ID -b 'Comment text'
```

### List Projects
```bash
maton api -X POST '/linear/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "{ projects(first: 20) { nodes { id name state createdAt } } }"
}
EOF
```

Example:

```bash
maton linear project list
```

### List Labels
```bash
maton api -X POST '/linear/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "{ issueLabels(first: 50) { nodes { id name color } } }"
}
EOF
```

Example:

```bash
maton linear label list
```

### List Workflow States
```bash
maton api -X POST '/linear/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "{ workflowStates(first: 50) { nodes { id name type team { key } } } }"
}
EOF
```

Example:

```bash
maton linear state list
```

### List Users
```bash
maton api -X POST '/linear/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "{ users(first: 50) { nodes { id name email active } } }"
}
EOF
```

Example:

```bash
maton linear user list
```

### List Cycles
```bash
maton api -X POST '/linear/graphql' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "{ cycles(first: 20) { nodes { id name number startsAt endsAt } } }"
}
EOF
```

Example:

```bash
maton linear cycle list
```

## Pagination

Linear uses Relay-style cursor-based pagination. The CLI handles this automatically with `--paginate`:

```bash
maton linear issue list -c ABC --paginate
```

For raw GraphQL requests, supply an `after: "CURSOR_VALUE"` argument with the `endCursor` from the previous response's `pageInfo`:

```bash
# First page
maton api -X POST '/linear/graphql' \
  --input - <<'EOF'
{
  "query": "{ issues(first: 20) { nodes { id identifier title } pageInfo { hasNextPage endCursor } } }"
}
EOF

# Next page
maton api -X POST '/linear/graphql' \
  --input - <<'EOF'
{
  "query": "{ issues(first: 20, after: \"CURSOR_VALUE\") { nodes { id identifier title } pageInfo { hasNextPage endCursor } } }"
}
EOF
```

## Notes

- Linear uses GraphQL exclusively (no REST API)
- Issue identifiers (e.g., `MTN-527`) can be used in place of UUIDs for the `id` parameter
- Priority values: 0 = No priority, 1 = Urgent, 2 = High, 3 = Medium, 4 = Low
- Workflow state types: `backlog`, `unstarted`, `started`, `completed`, `canceled`
- Some mutations (delete, create labels/projects) may require additional OAuth scopes
- Use `searchIssues(term: "...")` for full-text search
- Filter operators: `eq`, `neq`, `in`, `nin`, `containsIgnoreCase`, etc.

## Resources

- [Linear API Overview](https://linear.app/developers)
- [Linear GraphQL Getting Started](https://linear.app/developers/graphql)
- [Linear GraphQL Schema (Apollo Studio)](https://studio.apollographql.com/public/Linear-API/schema/reference?variant=current)
- [Linear API and Webhooks](https://linear.app/docs/api-and-webhooks)
- [Maton CLI Manual](https://cli.maton.ai/manual)