# Linear

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `linear`
**Upstream base URL:** `api.linear.app`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.linear.app/graphql`
- Gateway: `https://api.maton.ai/linear/graphql`

**Important:** Linear uses a GraphQL API. All operations are sent as POST requests with a JSON body containing the `query` field.

### User Info API

#### Get Current User

```bash
maton linear whoami
```

Or with `maton api`:

```bash
maton api -X POST '/linear/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ viewer { id name email } }"}
JSON
```

### Organization API

#### Get Organization

```bash
maton linear org get
```

Or with `maton api`:

```bash
maton api -X POST '/linear/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ organization { id name urlKey } }"}
JSON
```

### Teams API

#### List Teams

```bash
maton linear team list
```

Or with `maton api`:

```bash
maton api -X POST '/linear/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ teams { nodes { id name key } } }"}
JSON
```

#### Get Team

```bash
maton linear team get ABC
```

Or with `maton api`:

```bash
maton api -X POST '/linear/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ team(id: \"ABC\") { id name key issues { nodes { id identifier title } } } }"}
JSON
```

### Issues API

#### List Issues

```bash
maton linear issue list -c ABC -L 10
```

Or with `maton api`:

```bash
maton api -X POST '/linear/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ issues(first: 10, filter: { team: { key: { eq: \"ABC\" } } }) { nodes { id identifier title state { name } priority createdAt } pageInfo { hasNextPage endCursor } } }"}
JSON
```

#### Get Issue by ID or Identifier

```bash
maton linear issue get ABC-123
```

Or with `maton api`:

```bash
maton api -X POST '/linear/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ issue(id: \"ABC-123\") { id identifier title description state { name } priority assignee { name } team { key name } createdAt updatedAt } }"}
JSON
```

#### Filter Issues

Filter by state type:

```bash
maton linear issue list --state started -L 10
```

Or with `maton api`:

```bash
maton api -X POST '/linear/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ issues(first: 10, filter: { state: { type: { eq: \"started\" } } }) { nodes { id identifier title state { name type } } } }"}
JSON
```

Filter by title:

```bash
maton linear issue list --title bug -L 10
```

Or with `maton api`:

```bash
maton api -X POST '/linear/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ issues(first: 10, filter: { title: { containsIgnoreCase: \"bug\" } }) { nodes { id identifier title } } }"}
JSON
```

#### Search Issues

```bash
maton linear issue search shopify -L 10
```

Or with `maton api`:

```bash
maton api -X POST '/linear/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ searchIssues(first: 10, term: \"shopify\") { nodes { id identifier title } } }"}
JSON
```

#### Create Issue

```bash
maton linear issue create --team-id TEAM_ID -t 'New issue title'
```

Or with `maton api`:

```bash
maton api -X POST '/linear/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "mutation { issueCreate(input: { teamId: \"TEAM_ID\", title: \"New issue title\" }) { success issue { id identifier title state { name } } } }"}
JSON
```

#### Update Issue

```bash
maton linear issue update ABC-123 -t 'Updated title' --priority 2
```

Or with `maton api`:

```bash
maton api -X POST '/linear/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "mutation { issueUpdate(id: \"ABC-123\", input: { title: \"Updated title\", priority: 2 }) { success issue { id identifier title priority } } }"}
JSON
```

### Projects API

#### List Projects

```bash
maton linear project list
```

Or with `maton api`:

```bash
maton api -X POST '/linear/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ projects(first: 10) { nodes { id name state createdAt } } }"}
JSON
```

### Cycles API

#### List Cycles

```bash
maton linear cycle list
```

Or with `maton api`:

```bash
maton api -X POST '/linear/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ cycles(first: 10) { nodes { id name number startsAt endsAt } } }"}
JSON
```

### Labels API

#### List Labels

```bash
maton linear label list
```

Or with `maton api`:

```bash
maton api -X POST '/linear/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ issueLabels(first: 20) { nodes { id name color } } }"}
JSON
```

### Workflow States API

```bash
maton linear state list
```

Or with `maton api`:

```bash
maton api -X POST '/linear/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ workflowStates(first: 20) { nodes { id name type team { key } } } }"}
JSON
```

### Users API

```bash
maton linear user list
```

Or with `maton api`:

```bash
maton api -X POST '/linear/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ users(first: 20) { nodes { id name email active } } }"}
JSON
```

### Comments API

#### List Comments

```bash
maton linear comment list --issue ABC-123 -L 10
```

Or with `maton api`:

```bash
maton api -X POST '/linear/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "{ issue(id: \"ABC-123\") { comments(first: 10) { nodes { id body createdAt user { name } } } } }"}
JSON
```

#### Create Comment

```bash
maton linear comment create --issue ABC-123 -b 'Looking into this'
```

Or with `maton api`:

```bash
maton api -X POST '/linear/graphql' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "mutation { commentCreate(input: { issueId: \"ABC-123\", body: \"Looking into this\" }) { success comment { id body } } }"}
JSON
```

### Pagination

Linear uses Relay-style cursor-based pagination. The CLI automatically paginates with '--paginate'.

```bash
maton linear issue list -c ABC --paginate
```

### Examples

```bash
# List issues for a team
maton linear issue list -c ABC -L 10

# View a specific issue
maton linear issue get ABC-123

# Create a new issue
maton linear issue create --team-id TEAM_ID -t 'Fix login'

# Add a comment
maton linear comment create --issue ABC-123 -b 'Looking into this'
```

### Notes

- Linear uses GraphQL exclusively (no REST API)
- Issue identifiers like `ABC-123` can be used in place of UUIDs for the `id` parameter
- Priority values: 0 = No priority, 1 = Urgent, 2 = High, 3 = Medium, 4 = Low
- Workflow state types: `backlog`, `unstarted`, `started`, `completed`, `canceled`
- The GraphQL schema is introspectable at the `api.linear.app/graphql` endpoint
- Use `searchIssues(term: "...")` for full-text search across issues
- Some mutations (delete, create labels/projects) may require additional OAuth scopes. If you receive a scope error, contact Maton support at support@maton.ai with the specific operations/APIs you need and your use-case

### Resources

- [Linear API Overview](https://linear.app/developers)
- [GraphQL Getting Started](https://linear.app/developers/graphql)
- [GraphQL Schema (Apollo Studio)](https://studio.apollographql.com/public/Linear-API/schema/reference?variant=current)
- [Linear API and Webhooks](https://linear.app/docs/api-and-webhooks)
- [Maton CLI Manual](https://cli.maton.ai/manual)
