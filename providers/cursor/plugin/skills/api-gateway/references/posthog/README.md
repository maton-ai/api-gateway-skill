# PostHog

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `posthog`
**Upstream base URL:** `{subdomain}.posthog.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://{subdomain}.posthog.com/api/organizations/@current/`
- Gateway: `https://api.maton.ai/posthog/api/organizations/@current/`

### Organizations API

#### Get Current Organization

```bash
maton api '/posthog/api/organizations/@current/'
```

### Projects API

#### List Projects

```bash
maton api '/posthog/api/projects/'
```

**Response:**
```json
{
  "count": 1,
  "next": null,
  "previous": null,
  "results": [
    {
      "id": 136209,
      "uuid": "019583c6-377c-0000-e55c-8696cbc33595",
      "organization": "019583c6-3635-0000-5798-c18f20963b3b",
      "api_token": "phc_XXX",
      "name": "Default project",
      "timezone": "UTC"
    }
  ]
}
```

#### Get Current Project

```bash
maton api '/posthog/api/projects/@current/'
```

### Users API

#### Get Current User

```bash
maton api '/posthog/api/users/@me/'
```

### Query API

The query endpoint is the recommended way to retrieve events and run analytics queries.

#### Run HogQL Query

```bash
maton api -X POST '/posthog/api/projects/{project_id}/query/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": {
    "kind": "HogQLQuery",
    "query": "SELECT event, count() FROM events GROUP BY event ORDER BY count() DESC LIMIT 10"
  }
}
JSON
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "columns": ["event", "count()"],
  "results": [
    ["$pageview", 140504],
    ["$autocapture", 108691],
    ["$identify", 5455]
  ],
  "types": [
    ["event", "String"],
    ["count()", "UInt64"]
  ]
}
```

### Persons API

#### List Persons

```bash
maton api '/posthog/api/projects/{project_id}/persons/?limit=10'
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "results": [
    {
      "id": "5d79eecb-93e6-5c8b-90f9-8510ba4040b8",
      "uuid": "5d79eecb-93e6-5c8b-90f9-8510ba4040b8",
      "name": "user@example.com",
      "is_identified": true,
      "distinct_ids": ["user-uuid", "anon-uuid"],
      "properties": {
        "email": "user@example.com",
        "name": "John Doe"
      }
    }
  ],
  "next": "https://us.posthog.com/api/projects/{project_id}/persons/?limit=10&offset=10"
}
```

#### Get Person

```bash
maton api '/posthog/api/projects/{project_id}/persons/{person_uuid}/'
```

**Note:** `{project_id}` and `{person_uuid}` are placeholders. Replace each of them with real values before sending the request.

### Dashboards API

#### List Dashboards

```bash
maton api '/posthog/api/projects/{project_id}/dashboards/'
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Dashboard

```bash
maton api '/posthog/api/projects/{project_id}/dashboards/{dashboard_id}/'
```

**Note:** `{project_id}` and `{dashboard_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Dashboard

```bash
maton api -X POST '/posthog/api/projects/{project_id}/dashboards/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "My Dashboard",
  "description": "Analytics overview"
}
JSON
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Dashboard

```bash
maton api -X PATCH '/posthog/api/projects/{project_id}/dashboards/{dashboard_id}/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Dashboard Name"
}
JSON
```

**Note:** `{project_id}` and `{dashboard_id}` are placeholders. Replace each of them with real values before sending the request.

### Insights API

#### List Insights

```bash
maton api '/posthog/api/projects/{project_id}/insights/?limit=10'
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Insight

```bash
maton api '/posthog/api/projects/{project_id}/insights/{insight_id}/'
```

**Note:** `{project_id}` and `{insight_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Insight

```bash
maton api -X POST '/posthog/api/projects/{project_id}/insights/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Daily Active Users",
  "query": {
    "kind": "InsightVizNode",
    "source": {
      "kind": "TrendsQuery",
      "series": [{"kind": "EventsNode", "event": "$pageview", "math": "dau"}],
      "interval": "day",
      "dateRange": {"date_from": "-30d"}
    }
  }
}
JSON
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

### Feature Flags API

#### List Feature Flags

```bash
maton api '/posthog/api/projects/{project_id}/feature_flags/'
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Feature Flag

```bash
maton api '/posthog/api/projects/{project_id}/feature_flags/{flag_id}/'
```

**Note:** `{project_id}` and `{flag_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Feature Flag

```bash
maton api -X POST '/posthog/api/projects/{project_id}/feature_flags/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "key": "my-feature-flag",
  "name": "My Feature Flag",
  "active": true,
  "filters": {
    "groups": [{"rollout_percentage": 100}]
  }
}
JSON
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Feature Flag

```bash
maton api -X PATCH '/posthog/api/projects/{project_id}/feature_flags/{flag_id}/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "active": false
}
JSON
```

**Note:** `{project_id}` and `{flag_id}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Feature Flag

Use soft delete by setting `deleted: true`:

```bash
maton api -X PATCH '/posthog/api/projects/{project_id}/feature_flags/{flag_id}/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "deleted": true
}
JSON
```

**Note:** `{project_id}` and `{flag_id}` are placeholders. Replace each of them with real values before sending the request.

### Cohorts API

#### List Cohorts

```bash
maton api '/posthog/api/projects/{project_id}/cohorts/'
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Cohort

```bash
maton api '/posthog/api/projects/{project_id}/cohorts/{cohort_id}/'
```

**Note:** `{project_id}` and `{cohort_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Cohort

```bash
maton api -X POST '/posthog/api/projects/{project_id}/cohorts/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Active Users",
  "groups": [
    {
      "properties": [
        {"key": "$pageview", "type": "event", "value": "performed_event"}
      ]
    }
  ]
}
JSON
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

### Actions API

#### List Actions

```bash
maton api '/posthog/api/projects/{project_id}/actions/'
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Action

```bash
maton api -X POST '/posthog/api/projects/{project_id}/actions/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Signed Up",
  "steps": [{"event": "$identify"}]
}
JSON
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

### Session Recordings API

#### List Session Recordings

```bash
maton api '/posthog/api/projects/{project_id}/session_recordings/?limit=10'
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "results": [
    {
      "id": "019c8795-79e3-7a05-ac56-597b102f1960",
      "distinct_id": "user-uuid",
      "recording_duration": 1807,
      "start_time": "2026-02-22T23:00:46.389000Z",
      "end_time": "2026-02-22T23:30:53.297000Z",
      "click_count": 0,
      "keypress_count": 0,
      "start_url": "https://example.com/register"
    }
  ],
  "has_next": false
}
```

#### Get Session Recording

```bash
maton api '/posthog/api/projects/{project_id}/session_recordings/{recording_id}/'
```

**Note:** `{project_id}` and `{recording_id}` are placeholders. Replace each of them with real values before sending the request.

### Annotations API

#### List Annotations

```bash
maton api '/posthog/api/projects/{project_id}/annotations/'
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Annotation

```bash
maton api -X POST '/posthog/api/projects/{project_id}/annotations/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "content": "New feature launched",
  "date_marker": "2026-02-23T00:00:00Z",
  "scope": "project"
}
JSON
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

### Surveys API

#### List Surveys

```bash
maton api '/posthog/api/projects/{project_id}/surveys/'
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Survey

```bash
maton api -X POST '/posthog/api/projects/{project_id}/surveys/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "NPS Survey",
  "type": "popover",
  "questions": [
    {
      "type": "rating",
      "question": "How likely are you to recommend us?"
    }
  ]
}
JSON
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

### Experiments API

#### List Experiments

```bash
maton api '/posthog/api/projects/{project_id}/experiments/'
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Experiment

```bash
maton api -X POST '/posthog/api/projects/{project_id}/experiments/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Button Color Test",
  "feature_flag_key": "button-color-test"
}
JSON
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

### Event Definitions API

#### List Event Definitions

```bash
maton api '/posthog/api/projects/{project_id}/event_definitions/?limit=10'
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

### Property Definitions API

#### List Property Definitions

```bash
maton api '/posthog/api/projects/{project_id}/property_definitions/?limit=10'
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

### Pagination

PostHog uses offset-based pagination:

```bash
maton api '/posthog/api/projects/{project_id}/persons/?limit=10&offset=20'
```

**Note:** `{project_id}` is a placeholder. Replace it with a real value before sending the request.

Response includes pagination info:

```json
{
  "count": 100,
  "next": "https://us.posthog.com/api/projects/{project_id}/persons/?limit=10&offset=30",
  "previous": "https://us.posthog.com/api/projects/{project_id}/persons/?limit=10&offset=10",
  "results": [...]
}
```

For session recordings, use `has_next` boolean:

```json
{
  "results": [...],
  "has_next": true
}
```

### Notes

- Use `@current` as a shortcut for the current project ID (e.g., `/api/projects/@current/dashboards/`)
- Project IDs are integers (e.g., `136209`)
- Person UUIDs are in standard UUID format
- The Events endpoint is deprecated; use the Query endpoint with HogQL instead
- Session recordings include activity metrics like click_count, keypress_count
- PostHog uses soft delete: use `PATCH` with `{"deleted": true}` instead of HTTP DELETE

### Resources

- [PostHog API Overview](https://posthog.com/docs/api)
- [HogQL Documentation](https://posthog.com/docs/hogql)
- [Feature Flags](https://posthog.com/docs/feature-flags)
- [Session Replay](https://posthog.com/docs/session-replay)
- [Experiments](https://posthog.com/docs/experiments)
- [Maton CLI Manual](https://cli.maton.ai/manual)
