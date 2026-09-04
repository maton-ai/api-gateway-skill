# Vercel Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `vercel`
**Base URL proxied:** `api.vercel.com`

## API Path Pattern

```
/vercel/{api-version}/{resource}
```

Note: API versions vary by endpoint (v2, v5, v6, v9, v10, v13, etc.)

## Common Endpoints

### User

#### Get Current User
```bash
maton api '/vercel/v2/user'
```

### Teams

#### List Teams
```bash
maton api '/vercel/v2/teams'
```

### Projects

#### List Projects
```bash
maton api '/vercel/v9/projects?limit=20'
```

#### Get Project
```bash
maton api '/vercel/v9/projects/{projectId}'
```

#### Create Project
```bash
maton api -X POST '/vercel/v9/projects' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "my-project",
  "framework": "nextjs",
  "gitRepository": {
    "type": "github",
    "repo": "username/repo"
  }
}
EOF
```

#### Update Project
```bash
maton api -X PATCH '/vercel/v9/projects/{projectId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "updated-name"
}
EOF
```

#### Delete Project
```bash
maton api -X DELETE '/vercel/v9/projects/{projectId}'
```

### Deployments

#### List Deployments
```bash
maton api '/vercel/v6/deployments?limit=20'
maton api '/vercel/v6/deployments?projectId={projectId}&limit=20'
```

#### Get Deployment
```bash
maton api '/vercel/v13/deployments/{deploymentId}'
```

#### Get Deployment Build Logs
```bash
maton api '/vercel/v3/deployments/{deploymentId}/events'
```

#### Cancel Deployment
```bash
maton api -X PATCH '/vercel/v12/deployments/{deploymentId}/cancel'
```

### Environment Variables

#### List Environment Variables
```bash
maton api '/vercel/v10/projects/{projectId}/env'
```

#### Create Environment Variable
```bash
maton api -X POST '/vercel/v10/projects/{projectId}/env' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "key": "API_KEY",
  "value": "secret-value",
  "type": "encrypted",
  "target": ["production", "preview"]
}
EOF
```

#### Update Environment Variable
```bash
maton api -X PATCH '/vercel/v10/projects/{projectId}/env/{envId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "value": "new-value"
}
EOF
```

#### Delete Environment Variable
```bash
maton api -X DELETE '/vercel/v10/projects/{projectId}/env/{envId}'
```

### Domains

#### List Domains
```bash
maton api '/vercel/v5/domains'
```

#### Get Domain
```bash
maton api '/vercel/v5/domains/{domain}'
```

#### Add Domain
```bash
maton api -X POST '/vercel/v5/domains' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "example.com"
}
EOF
```

#### Remove Domain
```bash
maton api -X DELETE '/vercel/v6/domains/{domain}'
```

### Remote Caching (Artifacts)

#### Get Artifacts Status
```bash
maton api '/vercel/v8/artifacts/status'
```

## Pagination

Cursor-based pagination:

```bash
maton api '/vercel/v9/projects?limit=20&until={next}'
```

Parameters:
- `limit` - Results per page (max varies by endpoint, typically 100)
- `until` - Cursor for next page
- `since` - Cursor for previous page

Response pagination:
```json
{
  "pagination": {
    "count": 20,
    "next": 1733304037737,
    "prev": 1759739951209
  }
}
```

## Notes

- API versions vary by endpoint (v2, v5, v6, v9, v10, v13)
- Timestamps are in milliseconds since Unix epoch
- Project IDs start with `prj_`
- Deployment IDs start with `dpl_`
- Team IDs start with `team_`
- Deployment states: `BUILDING`, `READY`, `ERROR`, `CANCELED`, `QUEUED`
- Environment variable types: `plain`, `encrypted`, `secret`, `sensitive`
- Environment targets: `production`, `preview`, `development`

## Resources

- [Vercel REST API Documentation](https://vercel.com/docs/rest-api)
- [Vercel API Reference](https://vercel.com/docs/rest-api/endpoints)
