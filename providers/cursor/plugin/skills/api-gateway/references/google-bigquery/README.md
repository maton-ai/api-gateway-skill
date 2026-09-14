# Google BigQuery

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `google-bigquery`
**Upstream base URL:** `bigquery.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://bigquery.googleapis.com/bigquery/v2/projects`
- Gateway: `https://api.maton.ai/google-bigquery/bigquery/v2/projects`

### Projects API

#### List Projects

List all projects accessible to the authenticated user.

```bash
maton api '/google-bigquery/bigquery/v2/projects'
```

**Response:**
```json
{
  "kind": "bigquery#projectList",
  "projects": [
    {
      "id": "my-project-123",
      "numericId": "822245862053",
      "projectReference": {
        "projectId": "my-project-123"
      },
      "friendlyName": "My Project"
    }
  ],
  "totalItems": 1
}
```

### Datasets API

#### List Datasets

BigQuery uses token-based pagination. List responses include a `pageToken` when more results exist:

```bash
maton api '/google-bigquery/bigquery/v2/projects/{projectId}/datasets?maxResults=10&pageToken={token}'
```

**Note:** `{projectId}` and `{token}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**
- `maxResults` - Maximum number of results to return
- `pageToken` - Token for pagination
- `all` - Include hidden datasets if true

**Response:**
```json
{
  "datasets": [...],
  "nextPageToken": "eyJvZmZzZXQiOjEwfQ=="
}
```

Use the `nextPageToken` value as `pageToken` in subsequent requests.

#### Get Dataset

```bash
maton api '/google-bigquery/bigquery/v2/projects/{projectId}/datasets/{datasetId}'
```

**Note:** `{projectId}` and `{datasetId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Dataset

```bash
maton api -X POST '/google-bigquery/bigquery/v2/projects/{projectId}/datasets' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "datasetReference": {
    "datasetId": "my_dataset",
    "projectId": "{projectId}"
  },
  "description": "My dataset description",
  "location": "US"
}
JSON
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "kind": "bigquery#dataset",
  "id": "my-project:my_dataset",
  "datasetReference": {
    "datasetId": "my_dataset",
    "projectId": "my-project"
  },
  "location": "US",
  "creationTime": "1771059780773"
}
```

#### Update Dataset (PATCH)

```bash
maton api -X PATCH '/google-bigquery/bigquery/v2/projects/{projectId}/datasets/{datasetId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "description": "Updated description"
}
JSON
```

**Note:** `{projectId}` and `{datasetId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Dataset

```bash
maton api '/google-bigquery/bigquery/v2/projects/{projectId}/datasets/{datasetId}' -X DELETE
```

**Note:** `{projectId}` and `{datasetId}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**
- `deleteContents` - If true, delete all tables in the dataset (default: false)

### Tables API

#### List Tables

```bash
maton api '/google-bigquery/bigquery/v2/projects/{projectId}/datasets/{datasetId}/tables'
```

**Note:** `{projectId}` and `{datasetId}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**
- `maxResults` - Maximum number of results to return
- `pageToken` - Token for pagination

#### Get Table

```bash
maton api '/google-bigquery/bigquery/v2/projects/{projectId}/datasets/{datasetId}/tables/{tableId}'
```

**Note:** `{projectId}`, `{datasetId}` and `{tableId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Table

```bash
maton api -X POST '/google-bigquery/bigquery/v2/projects/{projectId}/datasets/{datasetId}/tables' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "tableReference": {
    "projectId": "{projectId}",
    "datasetId": "{datasetId}",
    "tableId": "my_table"
  },
  "schema": {
    "fields": [
      {"name": "id", "type": "INTEGER", "mode": "REQUIRED"},
      {"name": "name", "type": "STRING", "mode": "NULLABLE"},
      {"name": "created_at", "type": "TIMESTAMP", "mode": "NULLABLE"}
    ]
  }
}
JSON
```

**Note:** `{projectId}` and `{datasetId}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "kind": "bigquery#table",
  "id": "my-project:my_dataset.my_table",
  "tableReference": {
    "projectId": "my-project",
    "datasetId": "my_dataset",
    "tableId": "my_table"
  },
  "schema": {
    "fields": [
      {"name": "id", "type": "INTEGER", "mode": "REQUIRED"},
      {"name": "name", "type": "STRING", "mode": "NULLABLE"},
      {"name": "created_at", "type": "TIMESTAMP", "mode": "NULLABLE"}
    ]
  },
  "numRows": "0",
  "type": "TABLE"
}
```

#### Update Table (PATCH)

```bash
maton api -X PATCH '/google-bigquery/bigquery/v2/projects/{projectId}/datasets/{datasetId}/tables/{tableId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "description": "Updated table description"
}
JSON
```

**Note:** `{projectId}`, `{datasetId}` and `{tableId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Table

```bash
maton api '/google-bigquery/bigquery/v2/projects/{projectId}/datasets/{datasetId}/tables/{tableId}' -X DELETE
```

**Note:** `{projectId}`, `{datasetId}` and `{tableId}` are placeholders. Replace each of them with real values before sending the request.

### Table Data API

#### List Table Data

Retrieve rows from a table.

```bash
maton api '/google-bigquery/bigquery/v2/projects/{projectId}/datasets/{datasetId}/tables/{tableId}/data'
```

**Note:** `{projectId}`, `{datasetId}` and `{tableId}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**
- `maxResults` - Maximum number of results to return
- `pageToken` - Token for pagination
- `startIndex` - Zero-based index of the starting row

**Response:**
```json
{
  "kind": "bigquery#tableDataList",
  "totalRows": "100",
  "rows": [
    {
      "f": [
        {"v": "1"},
        {"v": "Alice"},
        {"v": "1.7710597807E9"}
      ]
    }
  ],
  "pageToken": "..."
}
```

#### Insert Table Data (Streaming)

Insert rows into a table using streaming insert. Note: Requires BigQuery paid tier.

```bash
maton api -X POST '/google-bigquery/bigquery/v2/projects/{projectId}/datasets/{datasetId}/tables/{tableId}/insertAll' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "rows": [
    {"json": {"id": 1, "name": "Alice"}},
    {"json": {"id": 2, "name": "Bob"}}
  ]
}
JSON
```

**Note:** `{projectId}`, `{datasetId}` and `{tableId}` are placeholders. Replace each of them with real values before sending the request.

### Queries API

#### Run Query (Synchronous)

Execute a SQL query and return results directly.

```bash
maton api -X POST '/google-bigquery/bigquery/v2/projects/{projectId}/queries' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "SELECT * FROM `my_dataset.my_table` LIMIT 10",
  "useLegacySql": false,
  "maxResults": 100
}
JSON
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

**Request body:**
- `useLegacySql` - Use legacy SQL syntax (default: false for GoogleSQL)
- `maxResults` - Maximum results per page
- `timeoutMs` - Query timeout in milliseconds

**Response:**
```json
{
  "kind": "bigquery#queryResponse",
  "schema": {
    "fields": [
      {"name": "id", "type": "INTEGER"},
      {"name": "name", "type": "STRING"}
    ]
  },
  "jobReference": {
    "projectId": "my-project",
    "jobId": "job_abc123",
    "location": "US"
  },
  "totalRows": "2",
  "rows": [
    {"f": [{"v": "1"}, {"v": "Alice"}]},
    {"f": [{"v": "2"}, {"v": "Bob"}]}
  ],
  "jobComplete": true,
  "totalBytesProcessed": "1024"
}
```

### Jobs API

#### Create Job (Asynchronous)

Submit a job for asynchronous execution.

```bash
maton api -X POST '/google-bigquery/bigquery/v2/projects/{projectId}/jobs' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "configuration": {
    "query": {
      "query": "SELECT * FROM `my_dataset.my_table`",
      "useLegacySql": false,
      "destinationTable": {
        "projectId": "{projectId}",
        "datasetId": "{datasetId}",
        "tableId": "results_table"
      },
      "writeDisposition": "WRITE_TRUNCATE"
    }
  }
}
JSON
```

**Note:** `{projectId}` and `{datasetId}` are placeholders. Replace each of them with real values before sending the request.

#### List Jobs

```bash
maton api '/google-bigquery/bigquery/v2/projects/{projectId}/jobs'
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `maxResults` - Maximum number of results to return
- `pageToken` - Token for pagination
- `stateFilter` - Filter by job state: `done`, `pending`, `running`
- `projection` - `full` or `minimal`

**Response:**
```json
{
  "kind": "bigquery#jobList",
  "jobs": [
    {
      "id": "my-project:US.job_abc123",
      "jobReference": {
        "projectId": "my-project",
        "jobId": "job_abc123",
        "location": "US"
      },
      "state": "DONE",
      "statistics": {
        "creationTime": "1771059781456",
        "startTime": "1771059782203",
        "endTime": "1771059782324"
      }
    }
  ]
}
```

#### Get Job

```bash
maton api '/google-bigquery/bigquery/v2/projects/{projectId}/jobs/{jobId}'
```

**Note:** `{projectId}` and `{jobId}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**
- `location` - Job location (e.g., "US", "EU")

#### Get Query Results

Retrieve results from a completed query job.

```bash
maton api '/google-bigquery/bigquery/v2/projects/{projectId}/queries/{jobId}'
```

**Note:** `{projectId}` and `{jobId}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**
- `location` - Job location
- `maxResults` - Maximum results per page
- `pageToken` - Token for pagination
- `startIndex` - Zero-based starting row

#### Cancel Job

```bash
maton api -X POST '/google-bigquery/bigquery/v2/projects/{projectId}/jobs/{jobId}/cancel'
```

**Note:** `{projectId}` and `{jobId}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**
- `location` - Job location

### Query Examples

#### Simple Query

```json
{
  "query": "SELECT 1 as test",
  "useLegacySql": false
}
```

#### Query with Parameters

```json
{
  "query": "SELECT * FROM `dataset.table` WHERE id = @id",
  "useLegacySql": false,
  "queryParameters": [
    {
      "name": "id",
      "parameterType": {"type": "INT64"},
      "parameterValue": {"value": "123"}
    }
  ]
}
```

#### Query to Destination Table

> **Caution:** `WRITE_TRUNCATE` overwrites the destination table entirely. Use `WRITE_APPEND` to add rows without destroying existing data. Confirm the disposition with the user before executing.

```json
{
  "query": "SELECT * FROM `source_dataset.source_table`",
  "useLegacySql": false,
  "destinationTable": {
    "projectId": "my-project",
    "datasetId": "dest_dataset",
    "tableId": "dest_table"
  },
  "writeDisposition": "WRITE_TRUNCATE"
}
```

### Common Schema Types

| Type | Description |
|------|-------------|
| `STRING` | Variable-length character data |
| `INTEGER` | 64-bit signed integer |
| `FLOAT` | 64-bit IEEE floating point |
| `BOOLEAN` | True or false |
| `TIMESTAMP` | Absolute point in time |
| `DATE` | Calendar date |
| `TIME` | Time of day |
| `DATETIME` | Date and time |
| `BYTES` | Variable-length binary data |
| `NUMERIC` | Exact numeric value with 38 digits of precision |
| `BIGNUMERIC` | Exact numeric value with 76+ digits of precision |
| `GEOGRAPHY` | Geographic data |
| `JSON` | JSON data |
| `RECORD` | Nested fields (also called STRUCT) |

**Field Modes:**
- `NULLABLE` - Field can be null (default)
- `REQUIRED` - Field cannot be null
- `REPEATED` - Field is an array

### Notes

- Project IDs are strings like `my-project-123`
- Dataset and table IDs: letters, numbers, underscores only
- Query results use `f` (fields) and `v` (value) structure
- Use `useLegacySql: false` for standard SQL
- Streaming inserts require BigQuery paid tier
- Jobs include location in their reference (US, EU, etc.)
- Use `maxResults` and `pageToken` for pagination

### Resources

- [BigQuery API Overview](https://cloud.google.com/bigquery/docs/reference/rest)
- [Datasets](https://cloud.google.com/bigquery/docs/reference/rest/v2/datasets)
- [Tables](https://cloud.google.com/bigquery/docs/reference/rest/v2/tables)
- [Jobs](https://cloud.google.com/bigquery/docs/reference/rest/v2/jobs)
- [Standard SQL Reference](https://cloud.google.com/bigquery/docs/reference/standard-sql/query-syntax)
- [Maton CLI Manual](https://cli.maton.ai/manual)
