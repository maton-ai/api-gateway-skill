# PostHog Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `posthog`
**Base URL proxied:** `{subdomain}.posthog.com`

## API Path Pattern

```
/posthog/api/{resource}
/posthog/api/projects/{project_id}/{resource}
```

## Common Endpoints

### Get Current User
```bash
maton api '/posthog/api/users/@me/'
```

### Get Current Organization
```bash
maton api '/posthog/api/organizations/@current/'
```

### List Projects
```bash
maton api '/posthog/api/projects/'
```

### Get Current Project
```bash
maton api '/posthog/api/projects/@current/'
```

### Run HogQL Query
```bash
maton api -X POST '/posthog/api/projects/{project_id}/query/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": {
    "kind": "HogQLQuery",
    "query": "SELECT event, count() FROM events GROUP BY event LIMIT 10"
  }
}
EOF
```

### List Persons
```bash
maton api '/posthog/api/projects/{project_id}/persons/?limit=10'
```

### Get Person
```bash
maton api '/posthog/api/projects/{project_id}/persons/{person_uuid}/'
```

### List Dashboards
```bash
maton api '/posthog/api/projects/{project_id}/dashboards/'
```

### Get Dashboard
```bash
maton api '/posthog/api/projects/{project_id}/dashboards/{dashboard_id}/'
```

### Create Dashboard
```bash
maton api -X POST '/posthog/api/projects/{project_id}/dashboards/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "My Dashboard",
  "description": "Analytics overview"
}
EOF
```

### List Insights
```bash
maton api '/posthog/api/projects/{project_id}/insights/?limit=10'
```

### List Feature Flags
```bash
maton api '/posthog/api/projects/{project_id}/feature_flags/'
```

### Create Feature Flag
```bash
maton api -X POST '/posthog/api/projects/{project_id}/feature_flags/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "key": "my-feature-flag",
  "name": "My Feature Flag",
  "active": true,
  "filters": {
    "groups": [{"rollout_percentage": 100}]
  }
}
EOF
```

### Delete Feature Flag
Use soft delete by setting `deleted: true`:
```bash
maton api -X PATCH '/posthog/api/projects/{project_id}/feature_flags/{flag_id}/' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "deleted": true
}
EOF
```

### List Session Recordings
```bash
maton api '/posthog/api/projects/{project_id}/session_recordings/?limit=10'
```

### List Cohorts
```bash
maton api '/posthog/api/projects/{project_id}/cohorts/'
```

### List Actions
```bash
maton api '/posthog/api/projects/{project_id}/actions/'
```

### List Experiments
```bash
maton api '/posthog/api/projects/{project_id}/experiments/'
```

### List Surveys
```bash
maton api '/posthog/api/projects/{project_id}/surveys/'
```

### List Event Definitions
```bash
maton api '/posthog/api/projects/{project_id}/event_definitions/?limit=10'
```

### List Property Definitions
```bash
maton api '/posthog/api/projects/{project_id}/property_definitions/?limit=10'
```

## Notes

- Use `@current` as a shortcut for the current project ID (e.g., `/api/projects/@current/dashboards/`)
- Project IDs are integers (e.g., `136209`)
- Person UUIDs are in standard UUID format
- The Events endpoint is deprecated; use the Query endpoint with HogQL instead
- All project-scoped endpoints require `{project_id}` or `@current`
- Pagination uses `limit` and `offset` query parameters
- PostHog uses soft delete: use `PATCH` with `{"deleted": true}` instead of HTTP DELETE

## Resources

- [PostHog API Overview](https://posthog.com/docs/api)
- [HogQL Documentation](https://posthog.com/docs/hogql)
- [Feature Flags](https://posthog.com/docs/feature-flags)
- [Session Replay](https://posthog.com/docs/session-replay)
- [Experiments](https://posthog.com/docs/experiments)
