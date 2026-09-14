# Grafana

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `grafana`
**Upstream base URL:** the user's Grafana instance host

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://{instance}/api/org`
- Gateway: `https://api.maton.ai/grafana/api/org`

### Organization API

#### Get Current Organization

```bash
maton api '/grafana/api/org'
```

**Response:**
```json
{
  "id": 1,
  "name": "Main Org.",
  "address": {
    "address1": "",
    "address2": "",
    "city": "",
    "zipCode": "",
    "state": "",
    "country": ""
  }
}
```

### User API

#### Get Current User

```bash
maton api '/grafana/api/user'
```

**Response:**
```json
{
  "id": 1,
  "uid": "abc123",
  "email": "user@example.com",
  "name": "User Name",
  "login": "user",
  "orgId": 1,
  "isGrafanaAdmin": false
}
```

### Dashboards API

#### Search Dashboards

```bash
maton api '/grafana/api/search?type=dash-db'
```

**Query parameters:**
- `type` - `dash-db` for dashboards, `dash-folder` for folders
- `query` - Search query string
- `tag` - Filter by tag
- `folderIds` - Filter by folder IDs
- `limit` - Max results (default 1000)

**Response:**
```json
[
  {
    "id": 1,
    "uid": "abc123",
    "title": "My Dashboard",
    "uri": "db/my-dashboard",
    "url": "/d/abc123/my-dashboard",
    "type": "dash-db",
    "tags": ["production"],
    "isStarred": false
  }
]
```

#### Search Service Accounts

```bash
maton api '/grafana/api/serviceaccounts/search'
```

#### Get Dashboard by UID

```bash
maton api '/grafana/api/dashboards/uid/{uid}'
```

**Note:** `{uid}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "meta": {
    "type": "db",
    "canSave": true,
    "canEdit": true,
    "canAdmin": true,
    "canStar": true,
    "slug": "my-dashboard",
    "url": "/d/abc123/my-dashboard",
    "expires": "0001-01-01T00:00:00Z",
    "created": "2024-01-01T00:00:00Z",
    "updated": "2024-01-02T00:00:00Z",
    "version": 1
  },
  "dashboard": {
    "id": 1,
    "uid": "abc123",
    "title": "My Dashboard",
    "tags": ["production"],
    "panels": [...],
    "schemaVersion": 30,
    "version": 1
  }
}
```

#### Create/Update Dashboard

```bash
maton api -X POST '/grafana/api/dashboards/db' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "dashboard": {
    "title": "New Dashboard",
    "panels": [],
    "schemaVersion": 30,
    "version": 0
  },
  "folderUid": "optional-folder-uid",
  "overwrite": false
}
JSON
```

**Response:**
```json
{
  "id": 1,
  "uid": "abc123",
  "url": "/d/abc123/new-dashboard",
  "status": "success",
  "version": 1,
  "slug": "new-dashboard"
}
```

#### Delete Dashboard

```bash
maton api '/grafana/api/dashboards/uid/{uid}' -X DELETE
```

**Note:** `{uid}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "title": "My Dashboard",
  "message": "Dashboard My Dashboard deleted",
  "id": 1
}
```

#### Get Home Dashboard

```bash
maton api '/grafana/api/dashboards/home'
```

### Folders API

#### List Folders

```bash
maton api '/grafana/api/folders'
```

**Response:**
```json
[
  {
    "id": 1,
    "uid": "folder123",
    "title": "My Folder",
    "url": "/dashboards/f/folder123/my-folder",
    "hasAcl": false,
    "canSave": true,
    "canEdit": true,
    "canAdmin": true
  }
]
```

#### Get Folder by UID

```bash
maton api '/grafana/api/folders/{uid}'
```

**Note:** `{uid}` is a placeholder. Replace it with a real value before sending the request.

#### Create Folder

```bash
maton api -X POST '/grafana/api/folders' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "New Folder"
}
JSON
```

**Response:**
```json
{
  "id": 1,
  "uid": "folder123",
  "title": "New Folder",
  "url": "/dashboards/f/folder123/new-folder",
  "hasAcl": false,
  "canSave": true,
  "canEdit": true,
  "canAdmin": true,
  "version": 1
}
```

#### Update Folder

```bash
maton api -X PUT '/grafana/api/folders/{uid}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Updated Folder Name",
  "version": 1
}
JSON
```

**Note:** `{uid}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Folder

```bash
maton api '/grafana/api/folders/{uid}' -X DELETE
```

**Note:** `{uid}` is a placeholder. Replace it with a real value before sending the request.

### Data Sources API

#### List Data Sources

```bash
maton api '/grafana/api/datasources'
```

**Response:**
```json
[
  {
    "id": 1,
    "uid": "ds123",
    "orgId": 1,
    "name": "Prometheus",
    "type": "prometheus",
    "access": "proxy",
    "url": "http://prometheus:9090",
    "isDefault": true,
    "readOnly": false
  }
]
```

#### Get Data Source by ID

```bash
maton api '/grafana/api/datasources/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Data Source by UID

```bash
maton api '/grafana/api/datasources/uid/{uid}'
```

**Note:** `{uid}` is a placeholder. Replace it with a real value before sending the request.

#### Get Data Source by Name

```bash
maton api '/grafana/api/datasources/name/{name}'
```

**Note:** `{name}` is a placeholder. Replace it with a real value before sending the request.

#### Create Data Source

```bash
maton api -X POST '/grafana/api/datasources' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Prometheus",
  "type": "prometheus",
  "url": "http://prometheus:9090",
  "access": "proxy",
  "isDefault": false
}
JSON
```

#### Update Data Source

```bash
maton api -X PUT '/grafana/api/datasources/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Prometheus",
  "type": "prometheus",
  "url": "http://prometheus:9090",
  "access": "proxy"
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Data Source

```bash
maton api '/grafana/api/datasources/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Annotations API

#### List Annotations

```bash
maton api '/grafana/api/annotations'
```

**Query parameters:**
- `from` - Epoch timestamp (ms)
- `to` - Epoch timestamp (ms)
- `dashboardId` - Filter by dashboard ID
- `dashboardUID` - Filter by dashboard UID
- `panelId` - Filter by panel ID
- `tags` - Filter by tags (comma-separated)
- `limit` - Max results

#### Create Annotation

```bash
maton api -X POST '/grafana/api/annotations' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "dashboardUID": "abc123",
  "time": 1609459200000,
  "text": "Deployment completed",
  "tags": ["deployment", "production"]
}
JSON
```

**Response:**
```json
{
  "message": "Annotation added",
  "id": 1
}
```

#### Update Annotation

```bash
maton api -X PUT '/grafana/api/annotations/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "text": "Updated annotation text",
  "tags": ["updated"]
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Annotation

```bash
maton api '/grafana/api/annotations/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Teams API

#### Search Teams

```bash
maton api '/grafana/api/teams/search'
```

**Query parameters:**
- `query` - Search query
- `page` - Page number
- `perpage` - Results per page

**Response:**
```json
{
  "totalCount": 1,
  "teams": [
    {
      "id": 1,
      "orgId": 1,
      "name": "Engineering",
      "email": "engineering@example.com",
      "memberCount": 5
    }
  ],
  "page": 1,
  "perPage": 1000
}
```

#### Get Team by ID

```bash
maton api '/grafana/api/teams/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Team

```bash
maton api -X POST '/grafana/api/teams' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Team",
  "email": "team@example.com"
}
JSON
```

#### Update Team

```bash
maton api -X PUT '/grafana/api/teams/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Team Name"
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Team

```bash
maton api '/grafana/api/teams/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Alert Rules API

Only the read endpoints of the provisioning namespace are documented here. That namespace is write-capable in Grafana: a `POST`, `PUT`, or `DELETE` under `/api/v1/provisioning/` changes alerting configuration for the whole instance. Do not issue one unless the user has asked for that specific change and confirmed the rule and folder — it is an administrative change, not a dashboard edit.

#### List Alert Rules

```bash
maton api '/grafana/api/v1/provisioning/alert-rules'
```

#### Get Alert Rule

```bash
maton api '/grafana/api/v1/provisioning/alert-rules/{uid}'
```

**Note:** `{uid}` is a placeholder. Replace it with a real value before sending the request.

#### List Alert Rules by Folder

```bash
maton api '/grafana/api/ruler/grafana/api/v1/rules'
```

### Plugins API

#### List Plugins

```bash
maton api '/grafana/api/plugins'
```

### Notes

- Dashboard UIDs are unique identifiers used in most operations
- Use `/api/search?type=dash-db` to find dashboard UIDs
- Folder operations require folder UIDs
- Some admin operations (list all users, orgs) require elevated permissions
- Alert rules use the provisioning API (`/api/v1/provisioning/...`)
- Annotations require epoch timestamps in milliseconds

### Resources

- [Grafana HTTP API](https://grafana.com/docs/grafana/latest/developers/http_api/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
