# Vercel

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `vercel`
**Upstream base URL:** `api.vercel.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.vercel.com/v2/user`
- Gateway: `https://api.maton.ai/vercel/v2/user`

### User API

#### Get Current User

```bash
maton api '/vercel/v2/user'
```

**Response:**
```json
{
  "user": {
    "id": "srL5ucia16R88imgFgrn9XHH",
    "email": "user@example.com",
    "username": "username",
    "name": "User Name",
    "avatar": null,
    "defaultTeamId": "team_abc123",
    "billing": {
      "plan": "hobby",
      "status": "active"
    }
  }
}
```

### Teams API

#### List Teams

```bash
maton api '/vercel/v2/teams'
```

**Response:**
```json
{
  "teams": [
    {
      "id": "team_1xPDNnVvmKxzxPs2x2XQRoKu",
      "slug": "my-team",
      "name": "My Team",
      "createdAt": 1732138693523,
      "membership": {
        "role": "OWNER"
      },
      "billing": {
        "plan": "hobby",
        "status": "active"
      }
    }
  ],
  "pagination": {
    "count": 1,
    "next": null,
    "prev": null
  }
}
```

### Projects API

#### List Projects

```bash
maton api '/vercel/v9/projects?limit=20'
```

**Response:**
```json
{
  "projects": [
    {
      "id": "prj_ET9o8o6WAQTfWbtF8NeFe4XF9uYG",
      "name": "my-project",
      "accountId": "team_abc123",
      "framework": "nextjs",
      "nodeVersion": "22.x",
      "createdAt": 1733304037737,
      "updatedAt": 1766947708146,
      "targets": {},
      "latestDeployments": []
    }
  ],
  "pagination": {
    "count": 20,
    "next": 1733304037737,
    "prev": null
  }
}
```

#### Get Project

```bash
maton api '/vercel/v9/projects/{projectId}'
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": "prj_ET9o8o6WAQTfWbtF8NeFe4XF9uYG",
  "name": "my-project",
  "accountId": "team_abc123",
  "framework": "nextjs",
  "nodeVersion": "22.x",
  "createdAt": 1733304037737,
  "updatedAt": 1766947708146,
  "buildCommand": null,
  "devCommand": null,
  "installCommand": null,
  "outputDirectory": null,
  "rootDirectory": null,
  "serverlessFunctionRegion": "iad1"
}
```

#### Create Project

```bash
maton api -X POST '/vercel/v9/projects' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "my-new-project",
  "framework": "nextjs",
  "gitRepository": {
    "type": "github",
    "repo": "username/repo"
  }
}
JSON
```

#### Update Project

```bash
maton api -X PATCH '/vercel/v9/projects/{projectId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "updated-project-name",
  "buildCommand": "npm run build"
}
JSON
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Project

```bash
maton api '/vercel/v9/projects/{projectId}' -X DELETE
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

### Deployments API

#### List Deployments

```bash
maton api '/vercel/v6/deployments?limit=20'
```

**Query parameters:**
- `limit` - Number of results (default: 20)
- `projectId` - Filter by project ID
- `target` - Filter by target (`production`, `preview`)
- `state` - Filter by state (`BUILDING`, `READY`, `ERROR`, `CANCELED`)

**Response:**
```json
{
  "deployments": [
    {
      "uid": "dpl_8gFe6M8XZsQ1ohP86VWTemcBAmZJ",
      "name": "my-project",
      "url": "my-project-abc123.vercel.app",
      "created": 1759739951209,
      "state": "READY",
      "readyState": "READY",
      "target": "production",
      "source": "git",
      "creator": {
        "uid": "srL5ucia16R88imgFgrn9XHH",
        "username": "username"
      },
      "meta": {
        "githubCommitRef": "main",
        "githubCommitSha": "6e88c2d..."
      }
    }
  ],
  "pagination": {
    "count": 20,
    "next": 1759739951209,
    "prev": null
  }
}
```

#### Get Deployment

```bash
maton api '/vercel/v13/deployments/{deploymentId}'
```

**Note:** `{deploymentId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": "dpl_8gFe6M8XZsQ1ohP86VWTemcBAmZJ",
  "name": "my-project",
  "url": "my-project-abc123.vercel.app",
  "created": 1759739951209,
  "buildingAt": 1759739952144,
  "ready": 1759740085170,
  "state": "READY",
  "readyState": "READY",
  "target": "production",
  "source": "git",
  "creator": {
    "uid": "srL5ucia16R88imgFgrn9XHH",
    "username": "username"
  }
}
```

#### Get Deployment Build Logs

```bash
maton api '/vercel/v3/deployments/{deploymentId}/events'
```

**Note:** `{deploymentId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
[
  {
    "created": 1759739951860,
    "deploymentId": "dpl_8gFe6M8XZsQ1ohP86VWTemcBAmZJ",
    "text": "Running build in Washington, D.C., USA (East) – iad1",
    "type": "stdout",
    "info": {
      "type": "build",
      "name": "bld_b3go7zd2k"
    }
  }
]
```

#### Cancel Deployment

```bash
maton api -X PATCH '/vercel/v12/deployments/{deploymentId}/cancel'
```

**Note:** `{deploymentId}` is a placeholder. Replace it with a real value before sending the request.

### Environment Variables API

#### List Environment Variables

```bash
maton api '/vercel/v10/projects/{projectId}/env'
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "envs": [
    {
      "id": "6EwQRCd32PVNHORP",
      "key": "API_KEY",
      "value": "...",
      "type": "encrypted",
      "target": ["production", "preview", "development"],
      "createdAt": 1732148489672,
      "updatedAt": 1745542152381
    }
  ]
}
```

#### Create Environment Variable

```bash
maton api -X POST '/vercel/v10/projects/{projectId}/env' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "key": "MY_ENV_VAR",
  "value": "my-value",
  "type": "encrypted",
  "target": ["production", "preview"]
}
JSON
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Environment Variable

```bash
maton api -X PATCH '/vercel/v10/projects/{projectId}/env/{envId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "value": "updated-value"
}
JSON
```

**Note:** `{projectId}` and `{envId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Environment Variable

```bash
maton api '/vercel/v10/projects/{projectId}/env/{envId}' -X DELETE
```

**Note:** `{projectId}` and `{envId}` are placeholders. Replace each of them with real values before sending the request.

### Domains API

#### List Domains

```bash
maton api '/vercel/v5/domains'
```

**Response:**
```json
{
  "domains": [
    {
      "name": "example.com",
      "apexName": "example.com",
      "projectId": "prj_abc123",
      "verified": true,
      "createdAt": 1732138693523
    }
  ],
  "pagination": {
    "count": 10,
    "next": null,
    "prev": null
  }
}
```

#### Get Domain

```bash
maton api '/vercel/v5/domains/{domain}'
```

**Note:** `{domain}` is a placeholder. Replace it with a real value before sending the request.

#### Add Domain

```bash
maton api -X POST '/vercel/v5/domains' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "example.com"
}
JSON
```

#### Remove Domain

```bash
maton api '/vercel/v6/domains/{domain}' -X DELETE
```

**Note:** `{domain}` is a placeholder. Replace it with a real value before sending the request.

### Remote Caching API

#### Get Artifacts Status

```bash
maton api '/vercel/v8/artifacts/status'
```

**Response:**
```json
{
  "status": "enabled"
}
```

### Pagination

Cursor-based pagination:

```bash
maton api '/vercel/v9/projects?limit=20&until={next}'
```

**Note:** `{next}` is a placeholder. Replace it with a real value before sending the request.

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

### Notes

- API versions vary by endpoint (v2, v5, v6, v9, v10, v13)
- Timestamps are in milliseconds since Unix epoch
- Project IDs start with `prj_`
- Deployment IDs start with `dpl_`
- Team IDs start with `team_`
- Deployment states: `BUILDING`, `READY`, `ERROR`, `CANCELED`, `QUEUED`
- Environment variable types: `plain`, `encrypted`, `secret`, `sensitive`
- Environment targets: `production`, `preview`, `development`

### Resources

- [Vercel REST API Documentation](https://vercel.com/docs/rest-api)
- [Vercel API Reference](https://vercel.com/docs/rest-api/endpoints)
- [Maton CLI Manual](https://cli.maton.ai/manual)
