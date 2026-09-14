# Sentry

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `sentry`
**Upstream base URL:** `{subdomain}.sentry.io`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://{subdomain}.sentry.io/api/0/organizations/`
- Gateway: `https://api.maton.ai/sentry/api/0/organizations/`

**Important:** Sentry API uses version `0` prefix in all paths.

### Organization API

#### List Organizations

```bash
maton api '/sentry/api/0/organizations/'
```

#### Retrieve Organization

```bash
maton api '/sentry/api/0/organizations/{organization_slug}/'
```

**Note:** `{organization_slug}` is a placeholder. Replace it with a real value before sending the request.

#### Update Organization

```bash
maton api -X PUT '/sentry/api/0/organizations/{organization_slug}/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Organization Name"
}
JSON
```

**Note:** `{organization_slug}` is a placeholder. Replace it with a real value before sending the request.

#### List Organization Projects

```bash
maton api '/sentry/api/0/organizations/{organization_slug}/projects/'
```

**Note:** `{organization_slug}` is a placeholder. Replace it with a real value before sending the request.

#### List Organization Members

```bash
maton api '/sentry/api/0/organizations/{organization_slug}/members/'
```

**Note:** `{organization_slug}` is a placeholder. Replace it with a real value before sending the request.

#### Retrieve Project

```bash
maton api '/sentry/api/0/projects/{organization_slug}/{project_slug}/'
```

**Note:** `{organization_slug}` and `{project_slug}` are placeholders. Replace each of them with real values before sending the request.

### Project API

#### Update Project

```bash
maton api -X PUT '/sentry/api/0/projects/{organization_slug}/{project_slug}/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Project Name",
  "slug": "updated-project-slug"
}
JSON
```

**Note:** `{organization_slug}` and `{project_slug}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Project

```bash
maton api '/sentry/api/0/projects/{organization_slug}/{project_slug}/' -X DELETE
```

**Note:** `{organization_slug}` and `{project_slug}` are placeholders. Replace each of them with real values before sending the request.

#### Create New Project

```bash
maton api -X POST '/sentry/api/0/teams/{organization_slug}/{team_slug}/projects/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Project",
  "slug": "new-project"
}
JSON
```

**Note:** `{organization_slug}` and `{team_slug}` are placeholders. Replace each of them with real values before sending the request.

### Issue API

#### List Project Issues

```bash
maton api '/sentry/api/0/projects/{organization_slug}/{project_slug}/issues/'
```

**Note:** `{organization_slug}` and `{project_slug}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**
- `statsPeriod` - Stats period: `24h`, `14d`, or empty
- `shortIdLookup` - Enable short ID lookup (set to `1`)
- `query` - Sentry search query (default: `is:unresolved`)
- `cursor` - Pagination cursor

#### List Organization Issues

```bash
maton api '/sentry/api/0/organizations/{organization_slug}/issues/'
```

**Note:** `{organization_slug}` is a placeholder. Replace it with a real value before sending the request.

#### Retrieve Issue

```bash
maton api '/sentry/api/0/issues/{issue_id}/'
```

**Note:** `{issue_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Issue

```bash
maton api -X PUT '/sentry/api/0/issues/{issue_id}/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "status": "resolved"
}
EOF
```

**Note:** `{issue_id}` is a placeholder. Replace it with a real value before sending the request.

Status values: `resolved`, `unresolved`, `ignored`

**Status values:** `resolved`, `unresolved`, `ignored`

#### Delete Issue

```bash
maton api '/sentry/api/0/issues/{issue_id}/' -X DELETE
```

**Note:** `{issue_id}` is a placeholder. Replace it with a real value before sending the request.

#### List Issue Events

```bash
maton api '/sentry/api/0/issues/{issue_id}/events/'
```

**Note:** `{issue_id}` is a placeholder. Replace it with a real value before sending the request.

#### List Issue Hashes

```bash
maton api '/sentry/api/0/issues/{issue_id}/hashes/'
```

**Note:** `{issue_id}` is a placeholder. Replace it with a real value before sending the request.

### Event API

#### List Project Events

```bash
maton api '/sentry/api/0/projects/{organization_slug}/{project_slug}/events/'
```

**Note:** `{organization_slug}` and `{project_slug}` are placeholders. Replace each of them with real values before sending the request.

#### Retrieve Event

```bash
maton api '/sentry/api/0/projects/{organization_slug}/{project_slug}/events/{event_id}/'
```

**Note:** `{organization_slug}`, `{project_slug}` and `{event_id}` are placeholders. Replace each of them with real values before sending the request.

### Team API

#### List Organization Teams

```bash
maton api '/sentry/api/0/organizations/{organization_slug}/teams/'
```

**Note:** `{organization_slug}` is a placeholder. Replace it with a real value before sending the request.

#### Create Team

```bash
maton api -X POST '/sentry/api/0/organizations/{organization_slug}/teams/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "New Team",
  "slug": "new-team"
}
EOF
```

**Note:** `{organization_slug}` is a placeholder. Replace it with a real value before sending the request.

#### Retrieve Team

```bash
maton api '/sentry/api/0/teams/{organization_slug}/{team_slug}/'
```

**Note:** `{organization_slug}` and `{team_slug}` are placeholders. Replace each of them with real values before sending the request.

#### Update Team

```bash
maton api -X PUT '/sentry/api/0/teams/{organization_slug}/{team_slug}/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Team Name"
}
JSON
```

**Note:** `{organization_slug}` and `{team_slug}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Team

```bash
maton api '/sentry/api/0/teams/{organization_slug}/{team_slug}/' -X DELETE
```

**Note:** `{organization_slug}` and `{team_slug}` are placeholders. Replace each of them with real values before sending the request.

#### List Team Projects

```bash
maton api '/sentry/api/0/teams/{organization_slug}/{team_slug}/projects/'
```

**Note:** `{organization_slug}` and `{team_slug}` are placeholders. Replace each of them with real values before sending the request.

### Release API

#### List Organization Releases

```bash
maton api '/sentry/api/0/organizations/{organization_slug}/releases/'
```

**Note:** `{organization_slug}` is a placeholder. Replace it with a real value before sending the request.

#### Create Release

```bash
maton api -X POST '/sentry/api/0/organizations/{organization_slug}/releases/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "version": "1.0.0",
  "projects": ["project-slug"]
}
EOF
```

**Note:** `{organization_slug}` is a placeholder. Replace it with a real value before sending the request.

#### Retrieve Release

```bash
maton api '/sentry/api/0/organizations/{organization_slug}/releases/{version}/'
```

**Note:** `{organization_slug}` and `{version}` are placeholders. Replace each of them with real values before sending the request.

#### Update Release

```bash
maton api -X PUT '/sentry/api/0/organizations/{organization_slug}/releases/{version}/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "ref": "main",
  "commits": [
    {
      "id": "abc123",
      "message": "Fix bug"
    }
  ]
}
JSON
```

**Note:** `{organization_slug}` and `{version}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Release

```bash
maton api '/sentry/api/0/organizations/{organization_slug}/releases/{version}/' -X DELETE
```

**Note:** `{organization_slug}` and `{version}` are placeholders. Replace each of them with real values before sending the request.

#### List Release Deploys

```bash
maton api '/sentry/api/0/organizations/{organization_slug}/releases/{version}/deploys/'
```

**Note:** `{organization_slug}` and `{version}` are placeholders. Replace each of them with real values before sending the request.

#### Create Deploy

```bash
maton api -X POST '/sentry/api/0/organizations/{organization_slug}/releases/{version}/deploys/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "environment": "production"
}
EOF
```

**Note:** `{organization_slug}` and `{version}` are placeholders. Replace each of them with real values before sending the request.

### Notes

- Organization and project identifiers use slugs (lowercase, hyphenated)
- Issue IDs are numeric
- Release versions can contain special characters (URL encode as needed)
- Uses cursor-based pagination via Link header
- Most endpoints require OAuth scopes like `event:read`, `project:read`, `org:read`

### Resources

- [Sentry API Documentation](https://docs.sentry.io/api/)
- [Events API](https://docs.sentry.io/api/events/)
- [Projects API](https://docs.sentry.io/api/projects/)
- [Organizations API](https://docs.sentry.io/api/organizations/)
- [Teams API](https://docs.sentry.io/api/teams/)
- [Releases API](https://docs.sentry.io/api/releases/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
