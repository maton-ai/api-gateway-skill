# Make

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `make`
**Upstream base URL:** `{zone}.make.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://{zone}.make.com/api/v2/users/me`
- Gateway: `https://api.maton.ai/make/api/v2/users/me`

### Users API

#### Get Current User

```bash
maton api '/make/api/v2/users/me'
```

**Response:**
```json
{
  "authUser": {
    "id": 2958000,
    "name": "John Doe",
    "email": "john@example.com",
    "language": "en",
    "timezoneId": 301,
    "timezone": "America/New_York",
    "avatar": "https://..."
  }
}
```

#### List Users

```bash
maton api '/make/api/v2/users?organizationId={organizationId}'

maton api '/make/api/v2/users?teamId={teamId}'
```

**Note:** `{organizationId}` and `{teamId}` are placeholders. Replace each of them with real values before sending the request.

#### Update User

```bash
maton api -X PATCH '/make/api/v2/users/{userId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Name",
  "language": "en",
  "timezoneId": 301
}
JSON
```

**Note:** `{userId}` is a placeholder. Replace it with a real value before sending the request.

### Organizations API

#### List Organizations

```bash
maton api '/make/api/v2/organizations'
```

**Response:**
```json
{
  "organizations": [
    {
      "id": 2767268,
      "name": "My Organization",
      "timezoneId": 301,
      "zone": "us2.make.com"
    }
  ],
  "pg": {"sortBy": "name", "limit": 10000, "sortDir": "asc", "offset": 0}
}
```

#### Get Organization

```bash
maton api '/make/api/v2/organizations/{organizationId}'
```

**Note:** `{organizationId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Organization

```bash
maton api -X POST '/make/api/v2/organizations' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Organization",
  "regionId": 2,
  "timezoneId": 301,
  "countryId": 202
}
JSON
```

#### Update Organization

```bash
maton api -X PATCH '/make/api/v2/organizations/{organizationId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Name",
  "timezoneId": 301
}
JSON
```

**Note:** `{organizationId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Organization

```bash
maton api '/make/api/v2/organizations/{organizationId}' -X DELETE
```

**Note:** `{organizationId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Organization Usage

```bash
maton api '/make/api/v2/organizations/{organizationId}/usage'
```

**Note:** `{organizationId}` is a placeholder. Replace it with a real value before sending the request.

### Teams API

#### List Teams

```bash
maton api '/make/api/v2/teams?organizationId={organizationId}'
```

**Note:** `{organizationId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "teams": [
    {
      "id": 388889,
      "name": "My Team",
      "organizationId": 2767268
    }
  ],
  "pg": {"sortBy": "name", "limit": 10000, "sortDir": "asc", "offset": 0}
}
```

#### Get Team

```bash
maton api '/make/api/v2/teams/{teamId}'
```

**Note:** `{teamId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Team

```bash
maton api -X POST '/make/api/v2/teams' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Team",
  "organizationId": 2767268
}
JSON
```

#### Delete Team

```bash
maton api '/make/api/v2/teams/{teamId}' -X DELETE
```

**Note:** `{teamId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Team Usage

```bash
maton api '/make/api/v2/teams/{teamId}/usage'
```

**Note:** `{teamId}` is a placeholder. Replace it with a real value before sending the request.

### Scenarios API

#### List Scenarios

```bash
maton api '/make/api/v2/scenarios?organizationId={organizationId}'

maton api '/make/api/v2/scenarios?teamId={teamId}'
```

**Note:** `{organizationId}` and `{teamId}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "scenarios": [
    {
      "id": 4667499,
      "name": "My Scenario",
      "teamId": 388889,
      "isActive": false,
      "isPaused": false,
      "scheduling": {"type": "indefinitely", "interval": 900},
      "lastEdit": "2026-04-07T19:41:51.801Z"
    }
  ],
  "pg": {"sortBy": "proprietal", "limit": 500, "sortDir": "desc", "offset": 0}
}
```

#### Get Scenario

```bash
maton api '/make/api/v2/scenarios/{scenarioId}'
```

**Note:** `{scenarioId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Scenario

```bash
maton api -X POST '/make/api/v2/scenarios' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "teamId": 388889,
  "name": "New Scenario",
  "blueprint": "{...}"
}
JSON
```

#### Update Scenario

```bash
maton api -X PATCH '/make/api/v2/scenarios/{scenarioId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Scenario Name"
}
JSON
```

**Note:** `{scenarioId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Scenario

```bash
maton api '/make/api/v2/scenarios/{scenarioId}' -X DELETE
```

**Note:** `{scenarioId}` is a placeholder. Replace it with a real value before sending the request.

#### Start Scenario

```bash
maton api -X POST '/make/api/v2/scenarios/{scenarioId}/start'
```

**Note:** `{scenarioId}` is a placeholder. Replace it with a real value before sending the request.

#### Stop Scenario

```bash
maton api -X POST '/make/api/v2/scenarios/{scenarioId}/stop'
```

**Note:** `{scenarioId}` is a placeholder. Replace it with a real value before sending the request.

#### Run Scenario

```bash
maton api -X POST '/make/api/v2/scenarios/{scenarioId}/run' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": {"key": "value"}
}
JSON
```

**Note:** `{scenarioId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Scenario Logs

```bash
maton api '/make/api/v2/scenarios/{scenarioId}/logs'

maton api '/make/api/v2/scenarios/{scenarioId}/logs?status=3&pg[limit]=10'
```

**Note:** `{scenarioId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `from` / `to` - Timestamp range in milliseconds
- `status` - 1=success, 2=warning, 3=error
- `pg[offset]`, `pg[limit]` - Pagination

### Connections API

#### List Connections

```bash
maton api '/make/api/v2/connections?teamId={teamId}'
```

**Note:** `{teamId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "connections": [
    {
      "id": 1353452,
      "name": "My HubSpot CRM connection",
      "accountName": "hubspotcrm",
      "accountLabel": "HubSpot CRM",
      "teamId": 388889,
      "accountType": "oauth",
      "editable": true
    }
  ]
}
```

#### Get Connection

```bash
maton api '/make/api/v2/connections/{connectionId}'
```

**Note:** `{connectionId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Connection

```bash
maton api -X POST '/make/api/v2/connections' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "accountName": "slack2",
  "accountType": "oauth",
  "teamId": 388889
}
JSON
```

#### Update Connection

```bash
maton api -X PATCH '/make/api/v2/connections/{connectionId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Connection Name"
}
JSON
```

**Note:** `{connectionId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Connection

```bash
maton api '/make/api/v2/connections/{connectionId}' -X DELETE
```

**Note:** `{connectionId}` is a placeholder. Replace it with a real value before sending the request.

#### Test Connection

```bash
maton api -X POST '/make/api/v2/connections/{connectionId}/test'
```

**Note:** `{connectionId}` is a placeholder. Replace it with a real value before sending the request.

### Data Stores API

#### List Data Stores

```bash
maton api '/make/api/v2/data-stores?teamId={teamId}'
```

**Note:** `{teamId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "dataStores": [],
  "pg": {"sortBy": "name", "limit": 10000, "sortDir": "asc", "offset": 0}
}
```

#### Get Data Store

```bash
maton api '/make/api/v2/data-stores/{dataStoreId}'
```

**Note:** `{dataStoreId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Data Store

```bash
maton api -X POST '/make/api/v2/data-stores' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "My Data Store",
  "teamId": 388889,
  "datastructureId": 12345,
  "maxSizeMB": 10
}
JSON
```

#### Update Data Store

```bash
maton api -X PATCH '/make/api/v2/data-stores/{dataStoreId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Name",
  "maxSizeMB": 20
}
JSON
```

**Note:** `{dataStoreId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Data Stores

```bash
maton api '/make/api/v2/data-stores?teamId={teamId}' -X DELETE -H 'Content-Type: application/json' --input - <<'JSON'
{
  "ids": [12345, 67890]
}
JSON
```

**Note:** `{teamId}` is a placeholder. Replace it with a real value before sending the request.

### Hooks API

#### List Hooks

```bash
maton api '/make/api/v2/hooks?teamId={teamId}'
```

**Note:** `{teamId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "hooks": [],
  "pg": {"sortBy": "name", "limit": 50, "sortDir": "asc", "offset": 0}
}
```

#### Get Hook

```bash
maton api '/make/api/v2/hooks/{hookId}'
```

**Note:** `{hookId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Hook

```bash
maton api -X POST '/make/api/v2/hooks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "My Webhook",
  "teamId": 388889,
  "typeName": "web",
  "method": "POST",
  "headers": {},
  "stringify": false
}
JSON
```

#### Update Hook

```bash
maton api -X PATCH '/make/api/v2/hooks/{hookId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Hook Name"
}
JSON
```

**Note:** `{hookId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Hook

```bash
maton api '/make/api/v2/hooks/{hookId}' -X DELETE
```

**Note:** `{hookId}` is a placeholder. Replace it with a real value before sending the request.

#### Enable/Disable Hook

```bash
maton api -X POST '/make/api/v2/hooks/{hookId}/enable'

maton api -X POST '/make/api/v2/hooks/{hookId}/disable'
```

**Note:** `{hookId}` is a placeholder. Replace it with a real value before sending the request.

#### Ping Hook

```bash
maton api '/make/api/v2/hooks/{hookId}/ping'
```

**Note:** `{hookId}` is a placeholder. Replace it with a real value before sending the request.

### Templates API

#### List Templates

```bash
maton api '/make/api/v2/templates?teamId={teamId}'
```

**Note:** `{teamId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "templates": [],
  "pg": {"sortBy": "id", "limit": 10, "sortDir": "asc", "offset": 0}
}
```

#### Get Template

```bash
maton api '/make/api/v2/templates/{templateId}'
```

**Note:** `{templateId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Template Blueprint

```bash
maton api '/make/api/v2/templates/{templateId}/blueprint'
```

**Note:** `{templateId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Template

```bash
maton api '/make/api/v2/templates/{templateId}' -X DELETE
```

**Note:** `{templateId}` is a placeholder. Replace it with a real value before sending the request.

### Incomplete Executions API

#### List Incomplete Executions

```bash
maton api '/make/api/v2/dlqs?scenarioId={scenarioId}'
```

**Note:** `{scenarioId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "dlqs": [],
  "pg": {"sortBy": "", "limit": 50, "sortDir": "asc", "offset": 0}
}
```

#### Get Incomplete Execution

```bash
maton api '/make/api/v2/dlqs/{dlqId}'
```

**Note:** `{dlqId}` is a placeholder. Replace it with a real value before sending the request.

#### Retry Incomplete Execution

```bash
maton api -X POST '/make/api/v2/dlqs/{dlqId}/retry'
```

**Note:** `{dlqId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Incomplete Executions

```bash
maton api '/make/api/v2/dlqs?scenarioId={scenarioId}' -X DELETE -H 'Content-Type: application/json' --input - <<'JSON'
{
  "ids": [12345, 67890]
}
JSON
```

**Note:** `{scenarioId}` is a placeholder. Replace it with a real value before sending the request.

### Pagination

Make uses offset-based pagination with `pg` parameters:

```bash
maton api '/make/api/v2/scenarios?organizationId=123&pg[offset]=0&pg[limit]=50&pg[sortBy]=name&pg[sortDir]=asc'
```

**Query parameters:**
- `pg[offset]` - Number of items to skip (default: 0)
- `pg[limit]` - Max items per page (varies by endpoint)
- `pg[sortBy]` - Field to sort by
- `pg[sortDir]` - Sort direction: `asc` or `desc`

**Response:**
```json
{
  "scenarios": [...],
  "pg": {
    "sortBy": "name",
    "limit": 500,
    "sortDir": "asc",
    "offset": 0,
    "returnTotalCount": false
  }
}
```

### Notes

- Make uses zone-specific URLs (e.g., `us1.make.com`, `eu1.make.com`) - Maton handles routing automatically
- Most list endpoints require either `organizationId` or `teamId` parameter
- Scenario IDs, team IDs, and organization IDs are integers
- Timestamps use ISO 8601 format
- Some operations (like getting individual scenarios) may require OAuth instead of API key authentication

### Resources

- [Make API Documentation](https://developers.make.com/api-documentation)
- [Make API Reference](https://developers.make.com/api-documentation/api-reference)
- [Maton CLI Manual](https://cli.maton.ai/manual)
