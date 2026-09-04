# Sentry Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `sentry`
**Base URL proxied:** `{subdomain}.sentry.io`

## API Path Pattern

```
/sentry/api/0/{resource}
```

Sentry API uses version `0` prefix in all paths.

## Common Endpoints

### List Organizations
```bash
maton api '/sentry/api/0/organizations/'
```

### Retrieve Organization
```bash
maton api '/sentry/api/0/organizations/{organization_slug}/'
```

### List Organization Projects
```bash
maton api '/sentry/api/0/organizations/{organization_slug}/projects/'
```

### List Organization Members
```bash
maton api '/sentry/api/0/organizations/{organization_slug}/members/'
```

### Retrieve Project
```bash
maton api '/sentry/api/0/projects/{organization_slug}/{project_slug}/'
```

### List Project Issues
```bash
maton api '/sentry/api/0/projects/{organization_slug}/{project_slug}/issues/'
```

Query parameters:
- `statsPeriod` - Stats period: `24h`, `14d`, or empty
- `query` - Sentry search query (default: `is:unresolved`)
- `cursor` - Pagination cursor

### List Organization Issues
```bash
maton api '/sentry/api/0/organizations/{organization_slug}/issues/'
```

### Retrieve Issue
```bash
maton api '/sentry/api/0/issues/{issue_id}/'
```

### Update Issue
```bash
maton api -X PUT '/sentry/api/0/issues/{issue_id}/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "status": "resolved"
}
EOF
```

Status values: `resolved`, `unresolved`, `ignored`

### Delete Issue
```bash
maton api -X DELETE '/sentry/api/0/issues/{issue_id}/'
```

### List Issue Events
```bash
maton api '/sentry/api/0/issues/{issue_id}/events/'
```

### List Project Events
```bash
maton api '/sentry/api/0/projects/{organization_slug}/{project_slug}/events/'
```

### List Organization Teams
```bash
maton api '/sentry/api/0/organizations/{organization_slug}/teams/'
```

### Create Team
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

### Retrieve Team
```bash
maton api '/sentry/api/0/teams/{organization_slug}/{team_slug}/'
```

### List Organization Releases
```bash
maton api '/sentry/api/0/organizations/{organization_slug}/releases/'
```

### Create Release
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

### Retrieve Release
```bash
maton api '/sentry/api/0/organizations/{organization_slug}/releases/{version}/'
```

### Create Deploy
```bash
maton api -X POST '/sentry/api/0/organizations/{organization_slug}/releases/{version}/deploys/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "environment": "production"
}
EOF
```

## Notes

- Organization and project identifiers use slugs (lowercase, hyphenated)
- Issue IDs are numeric
- Release versions can contain special characters (URL encode as needed)
- Uses cursor-based pagination via Link header
- Most endpoints require OAuth scopes like `event:read`, `project:read`, `org:read`

## Resources

- [Sentry API Documentation](https://docs.sentry.io/api/)
- [Events API](https://docs.sentry.io/api/events/)
- [Projects API](https://docs.sentry.io/api/projects/)
- [Organizations API](https://docs.sentry.io/api/organizations/)
- [Teams API](https://docs.sentry.io/api/teams/)
- [Releases API](https://docs.sentry.io/api/releases/)
