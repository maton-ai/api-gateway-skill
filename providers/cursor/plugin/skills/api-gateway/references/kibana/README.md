# Kibana

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `kibana`
**Upstream base URL:** the user's Kibana instance host

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://{instance-host}/api/status`
- Gateway: `https://api.maton.ai/kibana/api/status`

**Important:** All requests require `kbn-xsrf: true` header.

### Status API

#### Get Status

```bash
maton api '/kibana/api/status' -H 'kbn-xsrf: true'
```

**Response:**
```json
{
  "name": "kibana",
  "uuid": "abc123",
  "version": {
    "number": "8.15.0",
    "build_hash": "..."
  },
  "status": {
    "overall": {"level": "available"}
  }
}
```

### Features API

#### List Features

```bash
maton api '/kibana/api/features' -H 'kbn-xsrf: true'
```

Returns list of all Kibana features and their capabilities.

### Saved Objects API

#### Find Saved Objects

```bash
maton api '/kibana/api/saved_objects/_find?type={type}' -H 'kbn-xsrf: true'
```

**Note:** `{type}` is a placeholder. Replace it with a real value before sending the request.

Types: `dashboard`, `visualization`, `index-pattern`, `search`, `lens`, `map`

**Query parameters:**
- `type` - Object type: `dashboard`, `visualization`, `index-pattern`, `search`, `lens`, `map`
- `search` - Search query
- `page` - Page number
- `per_page` - Results per page (default 20, max 10000)
- `fields` - Fields to return

**Response:**

```json
{
  "page": 1,
  "per_page": 20,
  "total": 5,
  "saved_objects": [
    {
      "id": "abc123",
      "type": "dashboard",
      "attributes": {
        "title": "My Dashboard",
        "description": "Dashboard description"
      },
      "version": "1",
      "updated_at": "2024-01-01T00:00:00.000Z"
    }
  ]
}
```

#### Get Saved Object

```bash
maton api '/kibana/api/saved_objects/{type}/{id}' -H 'kbn-xsrf: true'
```

**Note:** `{type}` and `{id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Saved Object

```bash
maton api -X POST '/kibana/api/saved_objects/{type}/{id}' -H 'kbn-xsrf: true' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "attributes": {
    "title": "My Index Pattern",
    "timeFieldName": "@timestamp"
  }
}
JSON
```

**Note:** `{type}` and `{id}` are placeholders. Replace each of them with real values before sending the request.

#### Update Saved Object

```bash
maton api -X PUT '/kibana/api/saved_objects/{type}/{id}' -H 'kbn-xsrf: true' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "attributes": {
    "title": "Updated Title"
  }
}
JSON
```

**Note:** `{type}` and `{id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Saved Object

```bash
maton api '/kibana/api/saved_objects/{type}/{id}' -X DELETE -H 'kbn-xsrf: true'
```

**Note:** `{type}` and `{id}` are placeholders. Replace each of them with real values before sending the request.

#### Bulk Operations

```bash
maton api -X POST '/kibana/api/saved_objects/_bulk_get' -H 'kbn-xsrf: true' -H 'Content-Type: application/json' --input - <<'JSON'
[
  {"type": "dashboard", "id": "abc123"},
  {"type": "visualization", "id": "def456"}
]
JSON
```

### Data Views API

#### List Data Views

```bash
maton api '/kibana/api/data_views' -H 'kbn-xsrf: true'
```

**Response:**
```json
{
  "data_view": [
    {
      "id": "abc123",
      "title": "logs-*",
      "timeFieldName": "@timestamp"
    }
  ]
}
```

#### Get Data View

```bash
maton api '/kibana/api/data_views/data_view/{id}' -H 'kbn-xsrf: true'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Data View

```bash
maton api -X POST '/kibana/api/data_views/data_view' -H 'kbn-xsrf: true' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data_view": {
    "title": "logs-*",
    "timeFieldName": "@timestamp"
  }
}
JSON
```

**Response:**
```json
{
  "data_view": {
    "id": "abc123",
    "title": "logs-*",
    "timeFieldName": "@timestamp"
  }
}
```

#### Update Data View

```bash
maton api -X POST '/kibana/api/data_views/data_view/{id}' -H 'kbn-xsrf: true' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data_view": {
    "title": "updated-logs-*"
  }
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Data View

```bash
maton api '/kibana/api/data_views/data_view/{id}' -X DELETE -H 'kbn-xsrf: true'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Spaces API

#### List Spaces

```bash
maton api '/kibana/api/spaces/space' -H 'kbn-xsrf: true'
```

**Response:**
```json
[
  {
    "id": "default",
    "name": "Default",
    "description": "Default space",
    "disabledFeatures": []
  }
]
```

#### Get Space

```bash
maton api '/kibana/api/spaces/space/{id}' -H 'kbn-xsrf: true'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Space

```bash
maton api -X POST '/kibana/api/spaces/space' -H 'kbn-xsrf: true' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "id": "marketing",
  "name": "Marketing",
  "description": "Marketing team space",
  "disabledFeatures": []
}
JSON
```

#### Update Space

```bash
maton api -X PUT '/kibana/api/spaces/space/{id}' -H 'kbn-xsrf: true' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "id": "marketing",
  "name": "Marketing Team",
  "description": "Updated description"
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Space

```bash
maton api '/kibana/api/spaces/space/{id}' -X DELETE -H 'kbn-xsrf: true'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Alerting API

#### Find Alert Rules

```bash
maton api '/kibana/api/alerting/rules/_find' -H 'kbn-xsrf: true'
```

**Query parameters:**
- `search` - Search query
- `page` - Page number
- `per_page` - Results per page

**Response:**
```json
{
  "page": 1,
  "per_page": 10,
  "total": 5,
  "data": [
    {
      "id": "abc123",
      "name": "CPU Alert",
      "consumer": "alerts",
      "enabled": true,
      "rule_type_id": "metrics.alert.threshold"
    }
  ]
}
```

#### Get Alert Rule

```bash
maton api '/kibana/api/alerting/rule/{id}' -H 'kbn-xsrf: true'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Enable/Disable Rule

```bash
maton api -X POST '/kibana/api/alerting/rule/{id}/_enable' -H 'kbn-xsrf: true'
maton api -X POST '/kibana/api/alerting/rule/{id}/_disable' -H 'kbn-xsrf: true'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Mute Rule

```bash
maton api -X POST '/kibana/api/alerting/rule/{id}/_mute_all' -H 'kbn-xsrf: true'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Unmute Rule

```bash
maton api -X POST '/kibana/api/alerting/rule/{id}/_unmute_all' -H 'kbn-xsrf: true'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Alerting Health

```bash
maton api '/kibana/api/alerting/_health' -H 'kbn-xsrf: true'
```

### Connectors API

#### List Connectors

```bash
maton api '/kibana/api/actions/connectors' -H 'kbn-xsrf: true'
```

**Response:**
```json
[
  {
    "id": "abc123",
    "name": "Email Connector",
    "connector_type_id": ".email",
    "is_preconfigured": false,
    "is_deprecated": false
  }
]
```

#### Get Connector

```bash
maton api '/kibana/api/actions/connector/{id}' -H 'kbn-xsrf: true'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### List Connector Types

```bash
maton api '/kibana/api/actions/connector_types' -H 'kbn-xsrf: true'
```

#### Execute Connector

```bash
maton api -X POST '/kibana/api/actions/connector/{id}/_execute' -H 'kbn-xsrf: true' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "params": {
    "to": ["user@example.com"],
    "subject": "Alert",
    "message": "Alert triggered"
  }
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Fleet API

#### List Agent Policies

```bash
maton api '/kibana/api/fleet/agent_policies' -H 'kbn-xsrf: true'
```

**Response:**
```json
{
  "items": [
    {
      "id": "abc123",
      "name": "Default policy",
      "namespace": "default",
      "status": "active"
    }
  ],
  "total": 1,
  "page": 1,
  "perPage": 20
}
```

#### List Agents

```bash
maton api '/kibana/api/fleet/agents' -H 'kbn-xsrf: true'
```

#### List Packages

```bash
maton api '/kibana/api/fleet/epm/packages' -H 'kbn-xsrf: true'
```

Returns all available integrations/packages.

### Security API

#### List Roles

```bash
maton api '/kibana/api/security/role' -H 'kbn-xsrf: true'
```

**Response:**
```json
[
  {
    "name": "admin",
    "metadata": {},
    "elasticsearch": {
      "cluster": ["all"],
      "indices": [...]
    },
    "kibana": [...]
  }
]
```

#### Get Role

```bash
maton api '/kibana/api/security/role/{name}' -H 'kbn-xsrf: true'
```

**Note:** `{name}` is a placeholder. Replace it with a real value before sending the request.

### Cases API

#### Find Cases

```bash
maton api '/kibana/api/cases/_find' -H 'kbn-xsrf: true'
```

**Query parameters:**
- `status` - `open`, `in-progress`, `closed`
- `severity` - `low`, `medium`, `high`, `critical`
- `page` - Page number
- `perPage` - Results per page

**Response:**
```json
{
  "cases": [],
  "page": 1,
  "per_page": 20,
  "total": 0
}
```

### Notes

- All requests require `kbn-xsrf: true` header
- Saved object types: dashboard, visualization, index-pattern, search, lens, map
- Data views replace index patterns in newer versions
- Fleet manages Elastic Agents

### Resources

- [Kibana REST API](https://www.elastic.co/docs/api/doc/kibana/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
