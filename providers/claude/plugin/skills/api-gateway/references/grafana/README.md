# Grafana Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `grafana`
**Base URL proxied:** User's Grafana instance

## API Path Pattern

```
/grafana/api/{resource}
```

## Organization & User

### Get Current Organization
```bash
maton api '/grafana/api/org'
```

### Get Current User
```bash
maton api '/grafana/api/user'
```

## Dashboards

### Search Dashboards
```bash
maton api '/grafana/api/search?type=dash-db'
```

Query params: `type`, `query`, `tag`, `folderIds`, `limit`

### Get Dashboard by UID
```bash
maton api '/grafana/api/dashboards/uid/{uid}'
```

### Create/Update Dashboard
```bash
maton api -X POST '/grafana/api/dashboards/db' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "dashboard": {
    "title": "Dashboard Title",
    "panels": [],
    "schemaVersion": 30
  },
  "overwrite": false
}
EOF
```

### Delete Dashboard
```bash
maton api -X DELETE '/grafana/api/dashboards/uid/{uid}'
```

## Folders

### List Folders
```bash
maton api '/grafana/api/folders'
```

### Get Folder
```bash
maton api '/grafana/api/folders/{uid}'
```

### Create Folder
```bash
maton api -X POST '/grafana/api/folders' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"title": "Folder Name"}
EOF
```

### Delete Folder
```bash
maton api -X DELETE '/grafana/api/folders/{uid}'
```

## Data Sources

### List Data Sources
```bash
maton api '/grafana/api/datasources'
```

### Get Data Source
```bash
maton api '/grafana/api/datasources/{id}'
maton api '/grafana/api/datasources/uid/{uid}'
maton api '/grafana/api/datasources/name/{name}'
```

### Create Data Source
```bash
maton api -X POST '/grafana/api/datasources' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Prometheus",
  "type": "prometheus",
  "url": "http://prometheus:9090",
  "access": "proxy"
}
EOF
```

### Delete Data Source
```bash
maton api -X DELETE '/grafana/api/datasources/{id}'
```

## Annotations

### List Annotations
```bash
maton api '/grafana/api/annotations'
```

Query params: `from`, `to`, `dashboardUID`, `tags`, `limit`

### Create Annotation
```bash
maton api -X POST '/grafana/api/annotations' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "dashboardUID": "abc123",
  "time": 1609459200000,
  "text": "Annotation text",
  "tags": ["tag1"]
}
EOF
```

### Delete Annotation
```bash
maton api -X DELETE '/grafana/api/annotations/{id}'
```

## Teams

### Search Teams
```bash
maton api '/grafana/api/teams/search'
```

### Create Team
```bash
maton api -X POST '/grafana/api/teams' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"name": "Team Name"}
EOF
```

## Alert Rules

### List Alert Rules
```bash
maton api '/grafana/api/v1/provisioning/alert-rules'
maton api '/grafana/api/ruler/grafana/api/v1/rules'
```

## Other Endpoints

### Service Accounts
```bash
maton api '/grafana/api/serviceaccounts/search'
```

### Plugins
```bash
maton api '/grafana/api/plugins'
```

## Notes

- Dashboard UIDs are unique identifiers
- Annotations use epoch timestamps in milliseconds
- Admin operations may require elevated permissions
- Alert rules use provisioning API (`/api/v1/provisioning/...`)

## Resources

- [Grafana HTTP API](https://grafana.com/docs/grafana/latest/developers/http_api/)
