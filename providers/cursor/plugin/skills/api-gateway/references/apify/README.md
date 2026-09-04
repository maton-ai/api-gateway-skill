# Apify Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `apify`
**Base URL proxied:** `api.apify.com`

## API Path Pattern

```
/apify/v2/{resource}
```

## Common Endpoints

### Users

#### Get Current User
```bash
maton api '/apify/v2/users/me'
```

### Actors

#### List Actors
```bash
maton api '/apify/v2/acts'
maton api '/apify/v2/acts?my=true'
```

#### Get Actor
```bash
maton api '/apify/v2/acts/{actorId}'
```

#### Run Actor
```bash
maton api -X POST '/apify/v2/acts/{actorId}/runs' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "startUrls": [{"url": "https://example.com"}],
  "maxItems": 100
}
EOF
```

### Actor Runs

#### List Runs
```bash
maton api '/apify/v2/actor-runs'
maton api '/apify/v2/actor-runs?status=SUCCEEDED'
```

#### Get Run
```bash
maton api '/apify/v2/actor-runs/{runId}'
```

#### Abort Run
```bash
maton api -X POST '/apify/v2/actor-runs/{runId}/abort'
```

### Actor Tasks

#### List Tasks
```bash
maton api '/apify/v2/actor-tasks'
```

#### Get Task
```bash
maton api '/apify/v2/actor-tasks/{actorTaskId}'
```

#### Run Task
```bash
maton api -X POST '/apify/v2/actor-tasks/{actorTaskId}/runs'
```

### Datasets

#### List Datasets
```bash
maton api '/apify/v2/datasets'
```

#### Get Dataset
```bash
maton api '/apify/v2/datasets/{datasetId}'
```

#### Get Dataset Items
```bash
maton api '/apify/v2/datasets/{datasetId}/items'
maton api '/apify/v2/datasets/{datasetId}/items?format=json&clean=true'
```

#### Put Items
```bash
maton api -X POST '/apify/v2/datasets/{datasetId}/items' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
[{"field1": "value1"}, {"field2": "value2"}]
EOF
```

### Key-Value Stores

#### List Stores
```bash
maton api '/apify/v2/key-value-stores'
```

#### Get Store
```bash
maton api '/apify/v2/key-value-stores/{storeId}'
```

#### Get Record
```bash
maton api '/apify/v2/key-value-stores/{storeId}/records/{key}'
```

#### Set Record
```bash
maton api -X PUT '/apify/v2/key-value-stores/{storeId}/records/{key}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"data": "value"}
EOF
```

### Request Queues

#### List Queues
```bash
maton api '/apify/v2/request-queues'
```

#### Get Queue
```bash
maton api '/apify/v2/request-queues/{queueId}'
```

#### Add Request
```bash
maton api -X POST '/apify/v2/request-queues/{queueId}/requests' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com",
  "uniqueKey": "unique-key"
}
EOF
```

### Schedules

#### List Schedules
```bash
maton api '/apify/v2/schedules'
```

#### Get Schedule
```bash
maton api '/apify/v2/schedules/{scheduleId}'
```

#### Create Schedule
```bash
maton api -X POST '/apify/v2/schedules' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "My Schedule",
  "cronExpression": "0 0 * * *",
  "actorId": "actor-id"
}
EOF
```

### Webhooks

#### List Webhooks
```bash
maton api '/apify/v2/webhooks'
```

#### Get Webhook
```bash
maton api '/apify/v2/webhooks/{webhookId}'
```

#### Create Webhook
```bash
maton api -X POST '/apify/v2/webhooks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "eventTypes": ["ACTOR.RUN.SUCCEEDED"],
  "requestUrl": "https://example.com/webhook"
}
EOF
```

## Pagination

Offset-based pagination:

```bash
maton api '/apify/v2/acts?offset=0&limit=100'
```

Response includes:
```json
{
  "data": {
    "total": 150,
    "offset": 0,
    "limit": 100,
    "count": 100,
    "items": [...]
  }
}
```

## Query Parameters

Common parameters:
- `offset` - Number of items to skip (default: 0)
- `limit` - Max items to return (default: varies, max: 1000)
- `desc` - Sort descending by creation date (boolean)

For dataset items:
- `format` - Response format (json, csv, xlsx, xml, rss)
- `clean` - Remove empty fields (boolean)
- `fields` - Comma-separated field names to include

## Notes

- All endpoints use the `/v2/` prefix
- Actor IDs can be `username/actor-name` or unique IDs
- Timestamps are ISO 8601 format
- Default response format is JSON
- Rate limits apply per account

## Resources

- [Apify API Reference](https://docs.apify.com/api/v2)
- [Apify Platform Documentation](https://docs.apify.com/platform)
