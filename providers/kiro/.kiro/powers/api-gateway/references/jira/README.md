# Jira

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `jira`
**Upstream base URL:** `api.atlassian.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.atlassian.com/oauth/token/accessible-resources`
- Gateway: `https://api.maton.ai/jira/oauth/token/accessible-resources`

### Getting Cloud ID

Jira Cloud requires a cloud ID in the API path. First, get accessible resources:

```bash
maton jira cloud list
```

Or with `maton api`:

```bash
maton api '/jira/oauth/token/accessible-resources'
```

**Response:**
```json
[{
  "id": "62909843-b784-4c35-b770-e4e2a26f024b",
  "url": "https://yoursite.atlassian.net",
  "name": "yoursite",
  "scopes": ["read:jira-user", "read:jira-work", "write:jira-work"]
}]
```

### Projects API

#### List Projects

```bash
maton jira project list --cloud-id {cloudId}
```

Or with `maton api`:

```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/project'
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Project

```bash
maton jira project get {projectKeyOrId} --cloud-id {cloudId}
```

Or with `maton api`:

```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/project/{projectKeyOrId}'
```

**Note:** `{cloudId}` and `{projectKeyOrId}` are placeholders. Replace each of them with real values before sending the request.

### Metadata API

#### List Fields

```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/field'
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

#### List Issue Types

```bash
maton jira issuetype list --cloud-id {cloudId}
```

Or with `maton api`:

```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/issuetype'
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

#### List Priorities

```bash
maton jira priority list --cloud-id {cloudId}
```

Or with `maton api`:

```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/priority'
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

#### List Statuses

```bash
maton jira status list --cloud-id {cloudId}
```

Or with `maton api`:

```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/status'
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

### Issues API

#### Search Issues (JQL)

```bash
maton jira issue search 'project = PROJ order by created DESC' --cloud-id {cloudId} --limit 20 --fields summary,status,assignee
```

Or with `maton api`:

```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/search/jql?jql=project%3DKEY%20order%20by%20created%20DESC&maxResults=20&fields=summary,status,assignee,created,priority'
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** The old `/search` endpoint is deprecated. Use `/search/jql` with a bounded query.

#### Get Issue

```bash
maton jira issue get {issueIdOrKey} --cloud-id {cloudId}
```

Or with `maton api`:

```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/issue/{issueIdOrKey}'
```

**Note:** `{cloudId}` and `{issueIdOrKey}` are placeholders. Replace each of them with real values before sending the request.

#### Create Issue

```bash
maton jira issue create --cloud-id {cloudId} --project PROJ --summary 'Fix login' --type Task
```

Or with `maton api`:

```bash
maton api -X POST '/jira/ex/jira/{cloudId}/rest/api/3/issue' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "fields": {
    "project": {"key": "PROJ"},
    "summary": "Fix login",
    "issuetype": {"name": "Task"}
  }
}
JSON
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Issue

```bash
maton jira issue update {issueIdOrKey} --cloud-id {cloudId} --summary 'Updated summary'
```

Or with `maton api`:

```bash
maton api -X PUT '/jira/ex/jira/{cloudId}/rest/api/3/issue/{issueIdOrKey}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "fields": {
    "summary": "Updated summary"
  }
}
JSON
```

**Note:** `{cloudId}` and `{issueIdOrKey}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Issue

```bash
maton jira issue delete {issueIdOrKey} --cloud-id {cloudId}
```

Or with `maton api`:

```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/issue/{issueIdOrKey}' -X DELETE
```

**Note:** `{cloudId}` and `{issueIdOrKey}` are placeholders. Replace each of them with real values before sending the request.

#### Assign Issue

```bash
maton jira issue update {issueIdOrKey} --cloud-id {cloudId} --assignee {accountId}
```

Or with `maton api`:

```bash
maton api -X PUT '/jira/ex/jira/{cloudId}/rest/api/3/issue/{issueIdOrKey}/assignee' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "accountId": "{accountId}"
}
JSON
```

**Note:** `{cloudId}`, `{issueIdOrKey}` and `{accountId}` are placeholders. Replace each of them with real values before sending the request.

**Note:** Use `--unassign` (CLI) or `"accountId": null` (API) to clear the assignee.

### Transitions API

#### Get Transitions

```bash
maton jira transition list {issueIdOrKey} --cloud-id {cloudId}
```

Or with `maton api`:

```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/issue/{issueIdOrKey}/transitions'
```

**Note:** `{cloudId}` and `{issueIdOrKey}` are placeholders. Replace each of them with real values before sending the request.

#### Transition Issue (change status)

```bash
maton jira transition apply {issueIdOrKey} --cloud-id {cloudId} --id 31
```

Or with `maton api`:

```bash
maton api -X POST '/jira/ex/jira/{cloudId}/rest/api/3/issue/{issueIdOrKey}/transitions' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "transition": {"id": "31"}
}
JSON
```

**Note:** `{cloudId}` and `{issueIdOrKey}` are placeholders. Replace each of them with real values before sending the request.

### Comments API

#### Get Comments

```bash
maton jira comment list {issueIdOrKey} --cloud-id {cloudId}
```

Or with `maton api`:

```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/issue/{issueIdOrKey}/comment'
```

**Note:** `{cloudId}` and `{issueIdOrKey}` are placeholders. Replace each of them with real values before sending the request.

#### Add Comment

```bash
maton jira comment add {issueIdOrKey} --cloud-id {cloudId} --body 'Comment text'
```

Or with `maton api`:

```bash
maton api -X POST '/jira/ex/jira/{cloudId}/rest/api/3/issue/{issueIdOrKey}/comment' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "body": {
    "type": "doc",
    "version": 1,
    "content": [{"type": "paragraph", "content": [{"type": "text", "text": "Comment text"}]}]
  }
}
JSON
```

**Note:** `{cloudId}` and `{issueIdOrKey}` are placeholders. Replace each of them with real values before sending the request.

### Users API

#### Get Current User

```bash
maton jira whoami --cloud-id {cloudId}
```

Or with `maton api`:

```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/myself'
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

#### Search Users

```bash
maton jira user search john --cloud-id {cloudId}
```

Or with `maton api`:

```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/user/search?query=john'
```

**Note:** `{cloudId}` is a placeholder. Replace it with a real value before sending the request.

### Notes

- Always fetch cloud ID first using `/oauth/token/accessible-resources`
- JQL queries must be bounded (e.g., `project=KEY`) - unbounded queries are rejected
- Use URL encoding for JQL query parameters
- Update, Delete, Transition, and Assign endpoints return HTTP 204 (No Content) on success
- Agile API (`/rest/agile/1.0/...`) requires additional OAuth scopes beyond the basic Jira scopes

### Resources

- [Jira API Introduction](https://developer.atlassian.com/cloud/jira/platform/rest/v3/intro/)
- [Search Issues (JQL)](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issue-search/#api-rest-api-3-search-jql-get)
- [Get Issue](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issues/#api-rest-api-3-issue-issueidorkey-get)
- [Create Issue](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issues/#api-rest-api-3-issue-post)
- [Update Issue](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issues/#api-rest-api-3-issue-issueidorkey-put)
- [Transition Issue](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issues/#api-rest-api-3-issue-issueidorkey-transitions-post)
- [Add Comment](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issue-comments/#api-rest-api-3-issue-issueidorkey-comment-post)
- [Get Projects](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-projects/#api-rest-api-3-project-get)
- [JQL Reference](https://support.atlassian.com/jira-service-management-cloud/docs/use-advanced-search-with-jira-query-language-jql/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
