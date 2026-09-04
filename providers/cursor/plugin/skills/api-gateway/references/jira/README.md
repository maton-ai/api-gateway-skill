# Jira Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `jira`
**Base URL proxied:** `api.atlassian.com`

## Getting Cloud ID

Jira Cloud requires a cloud ID in the API path. First, get accessible resources:

```bash
maton api '/jira/oauth/token/accessible-resources'
```

Example:

```bash
maton jira cloud list
```

Response:
```json
[{
  "id": "62909843-b784-4c35-b770-e4e2a26f024b",
  "url": "https://yoursite.atlassian.net",
  "name": "yoursite",
  "scopes": ["read:jira-user", "read:jira-work", "write:jira-work"]
}]
```

## API Path Pattern

```
/jira/ex/jira/{cloudId}/rest/api/3/{endpoint}
```

## Common Endpoints

### List Projects
```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/project'
```

Example:

```bash
maton jira project list --cloud-id abc-123
```

### Get Project
```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/project/{projectKeyOrId}'
```

Example:

```bash
maton jira project view PROJ --cloud-id abc-123
```

### Search Issues (JQL)
Note: The old `/search` endpoint is deprecated. Use `/search/jql` with a bounded query.

```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/search/jql?jql=project%3DKEY%20order%20by%20created%20DESC&maxResults=20&fields=summary,status,assignee,created,priority'
```

Example:

```bash
maton jira issue search 'project = PROJ order by created DESC' --cloud-id abc-123 --limit 20 --fields summary,status,assignee
```

### Get Issue
```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/issue/{issueIdOrKey}'
```

Example:

```bash
maton jira issue view PROJ-123 --cloud-id abc-123
```

### Create Issue
```bash
maton api -X POST '/jira/ex/jira/{cloudId}/rest/api/3/issue' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "fields": {
    "project": {"key": "PROJ"},
    "summary": "Issue summary",
    "issuetype": {"name": "Task"}
  }
}
EOF
```

Example:

```bash
maton jira issue create --cloud-id abc-123 --project PROJ --summary 'Issue summary' --type Task
```

### Update Issue
```bash
maton api -X PUT '/jira/ex/jira/{cloudId}/rest/api/3/issue/{issueIdOrKey}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "fields": {
    "summary": "Updated summary"
  }
}
EOF
```

Example:

```bash
maton jira issue update PROJ-123 --cloud-id abc-123 --summary 'Updated summary'
```

### Delete Issue
```bash
maton api -X DELETE '/jira/ex/jira/{cloudId}/rest/api/3/issue/{issueIdOrKey}'
```

Example:

```bash
maton jira issue delete PROJ-123 --cloud-id abc-123
```

### Assign Issue
```bash
maton api -X PUT '/jira/ex/jira/{cloudId}/rest/api/3/issue/{issueIdOrKey}/assignee' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "accountId": "712020:5aff718e-6fe0-4548-82f4-f44ec481e5e7"
}
EOF
```

Example:

```bash
maton jira issue update PROJ-123 --cloud-id abc-123 --assignee 712020:5aff718e-6fe0-4548-82f4-f44ec481e5e7
```

### Get Transitions
```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/issue/{issueIdOrKey}/transitions'
```

Example:

```bash
maton jira transition list PROJ-123 --cloud-id abc-123
```

### Transition Issue (change status)
```bash
maton api -X POST '/jira/ex/jira/{cloudId}/rest/api/3/issue/{issueIdOrKey}/transitions' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "transition": {"id": "31"}
}
EOF
```

Example:

```bash
maton jira transition apply PROJ-123 --cloud-id abc-123 --id 31
```

### Add Comment
```bash
maton api -X POST '/jira/ex/jira/{cloudId}/rest/api/3/issue/{issueIdOrKey}/comment' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "body": {
    "type": "doc",
    "version": 1,
    "content": [{"type": "paragraph", "content": [{"type": "text", "text": "Comment text"}]}]
  }
}
EOF
```

Example:

```bash
maton jira comment add PROJ-123 --cloud-id abc-123 --body 'Comment text'
```

### Get Comments
```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/issue/{issueIdOrKey}/comment'
```

Example:

```bash
maton jira comment list PROJ-123 --cloud-id abc-123
```

### Users

#### Get Current User
```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/myself'
```

Example:

```bash
maton jira whoami --cloud-id abc-123
```

#### Search Users
```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/user/search?query=john'
```

Example:

```bash
maton jira user search john --cloud-id abc-123
```

### Metadata

#### List Issue Types
```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/issuetype'
```

Example:

```bash
maton jira issuetype list --cloud-id abc-123
```

#### List Priorities
```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/priority'
```

Example:

```bash
maton jira priority list --cloud-id abc-123
```

#### List Statuses
```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/status'
```

Example:

```bash
maton jira status list --cloud-id abc-123
```

#### List Fields
```bash
maton api '/jira/ex/jira/{cloudId}/rest/api/3/field'
```

## Notes

- Always fetch cloud ID first using `/oauth/token/accessible-resources`
- JQL queries must be bounded (e.g., `project=KEY`) - unbounded queries are rejected
- Use URL encoding for JQL query parameters
- Update, Delete, Transition, and Assign endpoints return HTTP 204 (No Content) on success
- Agile API (`/rest/agile/1.0/...`) requires additional OAuth scopes beyond the basic Jira scopes

## Resources

- [API Introduction](https://developer.atlassian.com/cloud/jira/platform/rest/v3/intro/)
- [Search Issues (JQL)](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issue-search/#api-rest-api-3-search-jql-get)
- [Get Issue](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issues/#api-rest-api-3-issue-issueidorkey-get)
- [Create Issue](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issues/#api-rest-api-3-issue-post)
- [Update Issue](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issues/#api-rest-api-3-issue-issueidorkey-put)
- [Transition Issue](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issues/#api-rest-api-3-issue-issueidorkey-transitions-post)
- [Add Comment](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issue-comments/#api-rest-api-3-issue-issueidorkey-comment-post)
- [Get Projects](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-projects/#api-rest-api-3-project-get)
- [JQL Reference](https://support.atlassian.com/jira-service-management-cloud/docs/use-advanced-search-with-jira-query-language-jql/)
- [Maton CLI Manual](https://cli.maton.ai/manual)