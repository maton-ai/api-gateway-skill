# GitHub

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `github`
**Upstream base URL:** `api.github.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.github.com/user`
- Gateway: `https://api.maton.ai/github/user`

### Users API

#### Get Authenticated User

```bash
maton github whoami
```

Or with `maton api`:

```bash
maton api '/github/user'
```

#### Get User by Username

```bash
maton api '/github/users/{username}'
```

**Note:** `{username}` is a placeholder. Replace it with a real value before sending the request.

#### List Users

```bash
maton api '/github/users?since={user_id}&per_page=30'
```

**Note:** `{user_id}` is a placeholder. Replace it with a real value before sending the request.

### Repositories API

#### List User Repositories

```bash
maton github repo list --sort updated
```

Or with `maton api`:

```bash
maton api '/github/user/repos?per_page=30&sort=updated'
```

**Query parameters:** `type` (all, owner, public, private, member), `sort` (created, updated, pushed, full_name), `direction` (asc, desc), `per_page`, `page`

#### List Organization Repositories

```bash
maton github repo list {org}
```

Or with `maton api`:

```bash
maton api '/github/orgs/{org}/repos?per_page=30'
```

**Note:** `{org}` is a placeholder. Replace it with a real value before sending the request.

#### Get Repository

```bash
maton github repo get --repo {owner}/{repo}
```

Or with `maton api`:

```bash
maton api '/github/repos/{owner}/{repo}'
```

**Note:** `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

#### Create Repository (User)

```bash
maton github repo create my-new-repo --description "A new repository" --visibility private
```

Or with `maton api`:

```bash
maton api -X POST '/github/user/repos' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "my-new-repo",
  "description": "A new repository",
  "private": true,
  "auto_init": true
}
JSON
```

#### Create Repository (Organization)

```bash
maton github repo create {org}/my-new-repo --visibility private
```

Or with `maton api`:

```bash
maton api -X POST '/github/orgs/{org}/repos' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "my-new-repo",
  "description": "A new repository",
  "private": true
}
JSON
```

**Note:** `{org}` is a placeholder. Replace it with a real value before sending the request.

#### Update Repository

```bash
maton github repo edit --repo {owner}/{repo} --description "Updated description" --enable-issues
```

Or with `maton api`:

```bash
maton api -X PATCH '/github/repos/{owner}/{repo}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "description": "Updated description",
  "has_issues": true,
  "has_wiki": false
}
JSON
```

**Note:** `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

#### List Repository Contents

```bash
maton api '/github/repos/{owner}/{repo}/contents/{path}'
```

**Note:** `{owner}`, `{repo}` and `{path}` are placeholders. Replace each of them with real values before sending the request.

### Repository Contents API

#### Get File Contents

```bash
maton api '/github/repos/{owner}/{repo}/contents/{path}?ref={branch}'
```

**Note:** `{owner}`, `{repo}`, `{path}` and `{branch}` are placeholders. Replace each of them with real values before sending the request.

#### Create or Update File

```bash
maton api -X PUT '/github/repos/{owner}/{repo}/contents/{path}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "message": "Create new file",
  "content": "SGVsbG8gV29ybGQh",
  "branch": "main"
}
JSON
```

**Note:** `{owner}`, `{repo}` and `{path}` are placeholders. Replace each of them with real values before sending the request.

Note: `content` must be Base64 encoded.

#### Delete File

```bash
maton api '/github/repos/{owner}/{repo}/contents/{path}' -X DELETE -H 'Content-Type: application/json' --input - <<'JSON'
{
  "message": "Delete file",
  "sha": "{file_sha}",
  "branch": "main"
}
JSON
```

**Note:** `{owner}`, `{repo}`, `{path}` and `{file_sha}` are placeholders. Replace each of them with real values before sending the request.

### Branches API

#### List Branches

```bash
maton api '/github/repos/{owner}/{repo}/branches?per_page=30'
```

**Note:** `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

#### Get Branch

```bash
maton api '/github/repos/{owner}/{repo}/branches/{branch}'
```

**Note:** `{owner}`, `{repo}` and `{branch}` are placeholders. Replace each of them with real values before sending the request.

#### Rename Branch

```bash
maton api -X POST '/github/repos/{owner}/{repo}/branches/{branch}/rename' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "new_name": "new-branch-name"
}
JSON
```

**Note:** `{owner}`, `{repo}` and `{branch}` are placeholders. Replace each of them with real values before sending the request.

#### Merge Branches

```bash
maton api -X POST '/github/repos/{owner}/{repo}/merges' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "base": "main",
  "head": "feature-branch",
  "commit_message": "Merge feature branch"
}
JSON
```

**Note:** `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

### Commits API

#### List Commits

```bash
maton api '/github/repos/{owner}/{repo}/commits?per_page=30'
```

**Note:** `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:** `sha` (branch name or commit SHA), `path` (file path), `author`, `committer`, `since`, `until`, `per_page`, `page`

#### Get Commit

```bash
maton api '/github/repos/{owner}/{repo}/commits/{ref}'
```

**Note:** `{owner}`, `{repo}` and `{ref}` are placeholders. Replace each of them with real values before sending the request.

#### Compare Two Commits

```bash
maton api '/github/repos/{owner}/{repo}/compare/{base}...{head}'
```

**Note:** `{owner}`, `{repo}`, `{base}` and `{head}` are placeholders. Replace each of them with real values before sending the request.

### Issues API

#### List Repository Issues

```bash
maton github issue list --repo {owner}/{repo} --state open
```

Or with `maton api`:

```bash
maton api '/github/repos/{owner}/{repo}/issues?state=open&per_page=30'
```

**Note:** `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:** `state` (open, closed, all), `labels`, `assignee`, `creator`, `mentioned`, `sort`, `direction`, `since`, `per_page`, `page`

#### Get Issue

```bash
maton github issue get {issue_number} --repo {owner}/{repo}
```

Or with `maton api`:

```bash
maton api '/github/repos/{owner}/{repo}/issues/{issue_number}'
```

**Note:** `{issue_number}`, `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

#### Create Issue

```bash
maton github issue create --repo {owner}/{repo} --title "Found a bug" --body "Bug description here" --label bug --assignee username
```

Or with `maton api`:

```bash
maton api -X POST '/github/repos/{owner}/{repo}/issues' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Found a bug",
  "body": "Bug description here",
  "labels": ["bug"],
  "assignees": ["username"]
}
JSON
```

**Note:** `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

#### Lock Issue

```bash
maton github issue lock {issue_number} --repo {owner}/{repo} --reason resolved
```

Or with `maton api`:

```bash
maton api -X PUT '/github/repos/{owner}/{repo}/issues/{issue_number}/lock' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "lock_reason": "resolved"
}
JSON
```

**Note:** `{issue_number}`, `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

#### Unlock Issue

```bash
maton github issue unlock {issue_number} --repo {owner}/{repo}
```

Or with `maton api`:

```bash
maton api '/github/repos/{owner}/{repo}/issues/{issue_number}/lock' -X DELETE
```

**Note:** `{issue_number}`, `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

### Issue Comments API

#### List Issue Comments

```bash
maton github issue get {issue_number} --repo {owner}/{repo} --comments
```

Or with `maton api`:

```bash
maton api '/github/repos/{owner}/{repo}/issues/{issue_number}/comments?per_page=30'
```

**Note:** `{issue_number}`, `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

#### Create Issue Comment

```bash
maton github issue comment {issue_number} --repo {owner}/{repo} --body "This is a comment"
```

Or with `maton api`:

```bash
maton api -X POST '/github/repos/{owner}/{repo}/issues/{issue_number}/comments' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "body": "This is a comment"
}
JSON
```

**Note:** `{issue_number}`, `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

#### Update Issue Comment

```bash
maton api -X PATCH '/github/repos/{owner}/{repo}/issues/comments/{comment_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "body": "Updated comment"
}
JSON
```

**Note:** `{owner}`, `{repo}` and `{comment_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Issue Comment

```bash
maton api '/github/repos/{owner}/{repo}/issues/comments/{comment_id}' -X DELETE
```

**Note:** `{owner}`, `{repo}` and `{comment_id}` are placeholders. Replace each of them with real values before sending the request.

#### Update / Close Issue

```bash
maton github issue close {issue_number} --repo {owner}/{repo} --reason completed
```

Or with `maton api`:

```bash
maton api -X PATCH '/github/repos/{owner}/{repo}/issues/{issue_number}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "state": "closed",
  "state_reason": "completed"
}
JSON
```

**Note:** `{issue_number}`, `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

### Labels API

#### List Labels

```bash
maton github label list --repo {owner}/{repo}
```

Or with `maton api`:

```bash
maton api '/github/repos/{owner}/{repo}/labels?per_page=30'
```

**Note:** `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

#### Create Label

```bash
maton github label create "priority:high" --repo {owner}/{repo} --color ff0000 --description "High priority issues"
```

Or with `maton api`:

```bash
maton api -X POST '/github/repos/{owner}/{repo}/labels' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "priority:high",
  "color": "ff0000",
  "description": "High priority issues"
}
JSON
```

**Note:** `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

### Milestones API

#### List Milestones

```bash
maton api '/github/repos/{owner}/{repo}/milestones?state=open&per_page=30'
```

**Note:** `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

#### Create Milestone

```bash
maton api -X POST '/github/repos/{owner}/{repo}/milestones' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "v1.0",
  "state": "open",
  "description": "First release",
  "due_on": "2026-03-01T00:00:00Z"
}
JSON
```

**Note:** `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

### Pull Requests API

#### List Pull Requests

```bash
maton github pr list --repo {owner}/{repo} --state open
```

Or with `maton api`:

```bash
maton api '/github/repos/{owner}/{repo}/pulls?state=open&per_page=30'
```

**Note:** `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:** `state` (open, closed, all), `head`, `base`, `sort`, `direction`, `per_page`, `page`

#### Get Pull Request

```bash
maton github pr get {pull_number} --repo {owner}/{repo}
```

Or with `maton api`:

```bash
maton api '/github/repos/{owner}/{repo}/pulls/{pull_number}'
```

**Note:** `{pull_number}`, `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

#### Create Pull Request

```bash
maton github pr create --repo {owner}/{repo} --base main --head feature-branch --title "New feature" --body "Description of changes"
```

Or with `maton api`:

```bash
maton api -X POST '/github/repos/{owner}/{repo}/pulls' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "New feature",
  "body": "Description of changes",
  "head": "feature-branch",
  "base": "main",
  "draft": false
}
JSON
```

**Note:** `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

#### Update Pull Request

```bash
maton github pr edit {pull_number} --repo {owner}/{repo} --title "Updated title"
```

Or with `maton api`:

```bash
maton api -X PATCH '/github/repos/{owner}/{repo}/pulls/{pull_number}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Updated title",
  "state": "closed"
}
JSON
```

**Note:** `{pull_number}`, `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

#### List Pull Request Commits

```bash
maton api '/github/repos/{owner}/{repo}/pulls/{pull_number}/commits?per_page=30'
```

**Note:** `{owner}`, `{repo}` and `{pull_number}` are placeholders. Replace each of them with real values before sending the request.

#### List Pull Request Files

```bash
maton github pr diff {pull_number} --repo {owner}/{repo}
```

Or with `maton api`:

```bash
maton api '/github/repos/{owner}/{repo}/pulls/{pull_number}/files?per_page=30'
```

**Note:** `{pull_number}`, `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

#### Check If Merged

```bash
maton api '/github/repos/{owner}/{repo}/pulls/{pull_number}/merge'
```

**Note:** `{owner}`, `{repo}` and `{pull_number}` are placeholders. Replace each of them with real values before sending the request.

#### Create Pull Request Review

```bash
maton github pr review {pull_number} --repo {owner}/{repo} --approve --body "Looks good!"
```

Or with `maton api`:

```bash
maton api -X POST '/github/repos/{owner}/{repo}/pulls/{pull_number}/reviews' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "body": "Looks good!",
  "event": "APPROVE"
}
JSON
```

**Note:** `{pull_number}`, `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

Events: `APPROVE`, `REQUEST_CHANGES`, `COMMENT`.

Note: GitHub does not allow approving your own pull requests; `--approve` returns `422 Can not approve your own pull request` in that case. Use `--comment` or `--request-changes` instead.

#### Merge Pull Request

```bash
maton github pr merge {pull_number} --repo {owner}/{repo} --squash --delete-branch
```

Or with `maton api`:

```bash
maton api -X PUT '/github/repos/{owner}/{repo}/pulls/{pull_number}/merge' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "commit_title": "Merge pull request",
  "merge_method": "squash"
}
JSON
```

**Note:** `{pull_number}`, `{owner}` and `{repo}` are placeholders. Replace each of them with real values before sending the request.

Merge methods: `merge`, `squash`, `rebase`.

### Pull Request Reviews API

#### List Reviews

```bash
maton api '/github/repos/{owner}/{repo}/pulls/{pull_number}/reviews?per_page=30'
```

**Note:** `{owner}`, `{repo}` and `{pull_number}` are placeholders. Replace each of them with real values before sending the request.

### Search API

#### Search Repositories

```bash
maton github repo search tetris --language python
```

Or with `maton api`:

```bash
maton api '/github/search/repositories?q={query}&per_page=30'
```

**Note:** `{query}` is a placeholder. Replace it with a real value before sending the request.

Example queries:
- `tetris+language:python` - Repositories with "tetris" in Python
- `react+stars:>10000` - Repositories with "react" and 10k+ stars

#### Search Issues

```bash
maton github issue search "bug" --state open
```

Or with `maton api`:

```bash
maton api '/github/search/issues?q={query}&per_page=30'
```

**Note:** `{query}` is a placeholder. Replace it with a real value before sending the request.

Example queries:
- `bug+is:open+is:issue` - Open issues containing "bug"
- `author:username+is:pr` - Pull requests by author

#### Search Code

```bash
maton api '/github/search/code?q={query}&per_page=30'
```

**Note:** `{query}` is a placeholder. Replace it with a real value before sending the request.

Example queries:
- `addClass+repo:facebook/react` - Search for "addClass" in a specific repo
- `function+extension:js` - JavaScript functions

Note: Code search may timeout (`408`) on broad queries. Always scope with `repo:`, `org:`, `user:`, or `extension:`.

#### Search Users

```bash
maton api '/github/search/users?q={query}&per_page=30'
```

**Note:** `{query}` is a placeholder. Replace it with a real value before sending the request.

### Organizations API

#### List User Organizations

```bash
maton api '/github/user/orgs?per_page=30'
```

Note: Requires `read:org` scope.

#### Get Organization

```bash
maton api '/github/orgs/{org}'
```

**Note:** `{org}` is a placeholder. Replace it with a real value before sending the request.

#### List Organization Members

```bash
maton api '/github/orgs/{org}/members?per_page=30'
```

**Note:** `{org}` is a placeholder. Replace it with a real value before sending the request.

### Rate Limit

#### Get Rate Limit

```bash
maton api '/github/rate_limit'
```

### Pagination

GitHub uses page-based pagination via the `Link` response header. The CLI handles this automatically with `--paginate`:

```bash
maton github repo list --paginate
```

For raw HTTP requests, use `per_page` (max 100, default 30) and `page` query parameters, or follow the `rel="next"` URL in the `Link` response header.

### Notes

- Repository names are case-insensitive but the API preserves case
- Issue numbers and PR numbers share the same sequence per repository — a PR is also an issue
- File `content` must be Base64 encoded when creating/updating files via the contents API
- File update/delete requires the current `sha` of the file (fetch via GET first)
- Rate limits: 5,000 requests/hour for authenticated users; search is 30 requests/minute
- Some endpoints require specific OAuth scopes (e.g., `read:org` for organization operations). If you receive a scope error, contact Maton support at support@maton.ai
- Search queries may timeout (`408`) on very broad patterns — always scope code search to a repo or org
- Cannot approve your own pull requests; use `COMMENT` or `REQUEST_CHANGES` events instead

### Resources

- [GitHub REST API Documentation](https://docs.github.com/en/rest)
- [Repositories API](https://docs.github.com/en/rest/repos/repos)
- [Issues API](https://docs.github.com/en/rest/issues/issues)
- [Pull Requests API](https://docs.github.com/en/rest/pulls/pulls)
- [Search API](https://docs.github.com/en/rest/search/search)
- [Rate Limits](https://docs.github.com/en/rest/overview/resources-in-the-rest-api#rate-limiting)
- [Maton CLI Manual](https://cli.maton.ai/manual)
