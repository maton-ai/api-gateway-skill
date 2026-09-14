# Kaggle

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `kaggle`
**Upstream base URL:** `api.kaggle.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.kaggle.com/v1/datasets.DatasetApiService/ListDatasets`
- Gateway: `https://api.maton.ai/kaggle/v1/datasets.DatasetApiService/ListDatasets`

**Important:** Kaggle uses an RPC-style API: every call is a `POST` to `/kaggle/v1/{ServiceName}/{MethodName}` with a JSON body.

### Datasets API

#### List Datasets

```bash
maton api -X POST '/kaggle/v1/datasets.DatasetApiService/ListDatasets' -H 'Content-Type: application/json' --input - <<'JSON'
{}
JSON
```

**Request body:**
- `search` (optional) - Search term
- `user` (optional) - Filter by username
- `pageSize` (optional) - Results per page
- `pageToken` (optional) - Pagination token

**Example:**
```json
{
  "search": "covid"
}
```

**Response:**
```json
{
  "datasets": [
    {
      "id": 9481458,
      "ref": "amar5693/screen-time-sleep-and-stress-analysis-dataset",
      "title": "Screen Time, Sleep & Stress Analysis Dataset",
      "subtitle": "ML-ready dataset analyzing smartphone usage and productivity.",
      "totalBytes": 787136,
      "downloadCount": 11659,
      "voteCount": 236,
      "usabilityRating": 1,
      "licenseName": "CC0: Public Domain",
      "ownerName": "Amar Tiwari",
      "tags": [...]
    }
  ]
}
```

#### Get Dataset

```bash
maton api -X POST '/kaggle/v1/datasets.DatasetApiService/GetDataset' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "ownerSlug": "amar5693",
  "datasetSlug": "screen-time-sleep-and-stress-analysis-dataset"
}
JSON
```

**Response:**
```json
{
  "id": 9481458,
  "title": "Screen Time, Sleep & Stress Analysis Dataset",
  "subtitle": "ML-ready dataset analyzing smartphone usage and productivity.",
  "totalBytes": 787136,
  "downloadCount": 11659,
  "usabilityRating": 1
}
```

#### List Dataset Files

```bash
maton api -X POST '/kaggle/v1/datasets.DatasetApiService/ListDatasetFiles' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "ownerSlug": "amar5693",
  "datasetSlug": "screen-time-sleep-and-stress-analysis-dataset"
}
JSON
```

**Response:**
```json
{
  "datasetFiles": [
    {
      "name": "Smartphone_Usage_Productivity_Dataset_50000.csv",
      "creationDate": "2026-02-13T06:56:19.803Z",
      "totalBytes": 2958561
    }
  ]
}
```

#### Get Dataset Metadata

```bash
maton api -X POST '/kaggle/v1/datasets.DatasetApiService/GetDatasetMetadata' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "ownerSlug": "amar5693",
  "datasetSlug": "screen-time-sleep-and-stress-analysis-dataset"
}
JSON
```

**Response:**
```json
{
  "info": {
    "datasetId": 9481458,
    "datasetSlug": "screen-time-sleep-and-stress-analysis-dataset",
    "ownerUser": "amar5693",
    "title": "Screen Time, Sleep & Stress Analysis Dataset",
    "description": "...",
    "totalViews": 44291,
    "totalVotes": 236,
    "totalDownloads": 11661
  }
}
```

#### Download Dataset

```bash
maton api -X POST '/kaggle/v1/datasets.DatasetApiService/DownloadDataset' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "ownerSlug": "amar5693",
  "datasetSlug": "screen-time-sleep-and-stress-analysis-dataset"
}
JSON
```

Returns binary data (ZIP file). Response headers:
- `Content-Type: application/zip`
- `Content-Length: <size in bytes>`

### Models API

#### List Models

```bash
maton api -X POST '/kaggle/v1/models.ModelApiService/ListModels' -H 'Content-Type: application/json' --input - <<'JSON'
{}
JSON
```

**Request body:**
- `owner` (optional) - Filter by owner
- `search` (optional) - Search term
- `pageSize` (optional) - Results per page

**Example:**
```json
{
  "owner": "google"
}
```

**Response:**
```json
{
  "models": [
    {
      "id": 1,
      "owner": "google",
      "slug": "gemma",
      "title": "Gemma",
      "subtitle": "Gemma is a family of lightweight, state-of-the-art models",
      "instanceCount": 16,
      "framework": "transformers"
    }
  ]
}
```

#### Get Model

```bash
maton api -X POST '/kaggle/v1/models.ModelApiService/GetModel' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "ownerSlug": "google",
  "modelSlug": "gemma"
}
JSON
```

**Response:**
```json
{
  "id": 1,
  "title": "Gemma",
  "slug": "gemma",
  "owner": "google",
  "subtitle": "Gemma is a family of lightweight, state-of-the-art models",
  "publishTime": "2024-02-21T16:00:00Z",
  "instanceCount": 16
}
```

### Competitions API

#### List Competitions

```bash
maton api -X POST '/kaggle/v1/competitions.CompetitionApiService/ListCompetitions' -H 'Content-Type: application/json' --input - <<'JSON'
{}
JSON
```

**Request body:**
- `search` (optional) - Search term
- `category` (optional) - Filter by category
- `pageSize` (optional) - Results per page

**Example:**
```json
{
  "search": "nlp"
}
```

**Response:**
```json
{
  "competitions": [
    {
      "id": 118448,
      "ref": "https://www.kaggle.com/competitions/ai-mathematical-olympiad-progress-prize-3",
      "title": "AI Mathematical Olympiad - Progress Prize 3",
      "url": "https://www.kaggle.com/competitions/ai-mathematical-olympiad-progress-prize-3",
      "deadline": "2026-06-06T23:59:00Z",
      "category": "Featured",
      "reward": "$1,048,576",
      "teamCount": 1234,
      "userHasEntered": false
    }
  ]
}
```

### Kernels API

#### List Kernels

```bash
maton api -X POST '/kaggle/v1/kernels.KernelsApiService/ListKernels' -H 'Content-Type: application/json' --input - <<'JSON'
{}
JSON
```

**Request body:**
- `search` (optional) - Search term
- `user` (optional) - Filter by username
- `language` (optional) - Filter by language: `python`, `r`, etc.
- `pageSize` (optional) - Results per page

**Example:**
```json
{
  "search": "titanic"
}
```

**Response:**
```json
{
  "kernels": [
    {
      "id": 5660537,
      "ref": "alexisbcook/titanic-tutorial",
      "title": "Titanic Tutorial",
      "author": "alexisbcook",
      "language": "Python",
      "totalVotes": 1234,
      "totalViews": 56789
    }
  ]
}
```

#### Get Kernel

```bash
maton api -X POST '/kaggle/v1/kernels.KernelsApiService/GetKernel' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "userName": "alexisbcook",
  "kernelSlug": "titanic-tutorial"
}
JSON
```

**Response:**
```json
{
  "metadata": {
    "id": 5660537,
    "ref": "alexisbcook/titanic-tutorial",
    "title": "Titanic Tutorial",
    "author": "alexisbcook",
    "language": "Python"
  }
}
```

### Notes

- All requests use POST method with JSON body
- API follows RPC pattern: `/v1/{ServiceName}/{MethodName}`
- Dataset refs: `{owner}/{dataset-slug}`
- Model refs: `{owner}/{model-slug}`
- Kernel refs: `{user}/{kernel-slug}`
- Download endpoints return binary ZIP files
- Authentication uses Kaggle API key (managed via Maton connection)

### Resources

- [Kaggle API Documentation](https://www.kaggle.com/docs/api)
- [Maton CLI Manual](https://cli.maton.ai/manual)
