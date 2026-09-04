# Kibana Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `kibana`
**Base URL proxied:** User's Kibana instance

## API Path Pattern

```
/kibana/api/{resource}
```

**Important:** All requests require `kbn-xsrf: true` header.

## Status & Features

### Get Status
```bash
maton api '/kibana/api/status'
```

### List Features
```bash
maton api '/kibana/api/features'
```

## Saved Objects

### Find Saved Objects
```bash
maton api '/kibana/api/saved_objects/_find?type={type}'
```

Types: `dashboard`, `visualization`, `index-pattern`, `search`, `lens`, `map`

### Get Saved Object
```bash
maton api '/kibana/api/saved_objects/{type}/{id}'
```

### Create Saved Object
```bash
maton api -X POST '/kibana/api/saved_objects/{type}/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"attributes": {"title": "Name"}}
EOF
```

### Delete Saved Object
```bash
maton api -X DELETE '/kibana/api/saved_objects/{type}/{id}'
```

## Data Views

### List Data Views
```bash
maton api '/kibana/api/data_views'
```

### Get Data View
```bash
maton api '/kibana/api/data_views/data_view/{id}'
```

### Create Data View
```bash
maton api -X POST '/kibana/api/data_views/data_view' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data_view": {
    "title": "logs-*",
    "timeFieldName": "@timestamp"
  }
}
EOF
```

### Delete Data View
```bash
maton api -X DELETE '/kibana/api/data_views/data_view/{id}'
```

## Spaces

### List Spaces
```bash
maton api '/kibana/api/spaces/space'
```

### Get Space
```bash
maton api '/kibana/api/spaces/space/{id}'
```

### Create Space
```bash
maton api -X POST '/kibana/api/spaces/space' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"id": "space-id", "name": "Space Name"}
EOF
```

### Delete Space
```bash
maton api -X DELETE '/kibana/api/spaces/space/{id}'
```

## Alerting

### Find Alert Rules
```bash
maton api '/kibana/api/alerting/rules/_find'
```

### Get Alert Rule
```bash
maton api '/kibana/api/alerting/rule/{id}'
```

### Enable/Disable Rule
```bash
maton api -X POST '/kibana/api/alerting/rule/{id}/_enable'
maton api -X POST '/kibana/api/alerting/rule/{id}/_disable'
```

## Connectors

### List Connectors
```bash
maton api '/kibana/api/actions/connectors'
```

### Get Connector
```bash
maton api '/kibana/api/actions/connector/{id}'
```

### Execute Connector
```bash
maton api -X POST '/kibana/api/actions/connector/{id}/_execute'
```

## Fleet

### List Agent Policies
```bash
maton api '/kibana/api/fleet/agent_policies'
```

### List Agents
```bash
maton api '/kibana/api/fleet/agents'
```

### List Packages
```bash
maton api '/kibana/api/fleet/epm/packages'
```

## Security

### List Roles
```bash
maton api '/kibana/api/security/role'
```

## Cases

### Find Cases
```bash
maton api '/kibana/api/cases/_find'
```

## Notes

- All requests require `kbn-xsrf: true` header
- Saved object types: dashboard, visualization, index-pattern, search, lens, map
- Data views replace index patterns in newer versions
- Fleet manages Elastic Agents

## Resources

- [Kibana REST API](https://www.elastic.co/docs/api/doc/kibana/)
