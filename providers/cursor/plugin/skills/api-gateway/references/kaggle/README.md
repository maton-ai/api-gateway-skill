# Kaggle Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `kaggle`
**Base URL proxied:** `api.kaggle.com`

## API Path Pattern

Kaggle uses an RPC-style API with POST requests:

```
/kaggle/v1/{ServiceName}/{MethodName}
```

All requests are POST with JSON body.

## Datasets

### List Datasets
```bash
maton api -X POST '/kaggle/v1/datasets.DatasetApiService/ListDatasets' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{}
EOF
```

Optional parameters: `search`, `user`, `pageSize`, `pageToken`

### Get Dataset
```bash
maton api -X POST '/kaggle/v1/datasets.DatasetApiService/GetDataset' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "ownerSlug": "username",
  "datasetSlug": "dataset-name"
}
EOF
```

### List Dataset Files
```bash
maton api -X POST '/kaggle/v1/datasets.DatasetApiService/ListDatasetFiles' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "ownerSlug": "username",
  "datasetSlug": "dataset-name"
}
EOF
```

### Get Dataset Metadata
```bash
maton api -X POST '/kaggle/v1/datasets.DatasetApiService/GetDatasetMetadata' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "ownerSlug": "username",
  "datasetSlug": "dataset-name"
}
EOF
```

### Download Dataset
```bash
maton api -X POST '/kaggle/v1/datasets.DatasetApiService/DownloadDataset' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "ownerSlug": "username",
  "datasetSlug": "dataset-name"
}
EOF
```

Returns binary ZIP file.

## Models

### List Models
```bash
maton api -X POST '/kaggle/v1/models.ModelApiService/ListModels' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{}
EOF
```

Optional parameters: `owner`, `search`, `pageSize`

### Get Model
```bash
maton api -X POST '/kaggle/v1/models.ModelApiService/GetModel' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "ownerSlug": "google",
  "modelSlug": "gemma"
}
EOF
```

## Competitions

### List Competitions
```bash
maton api -X POST '/kaggle/v1/competitions.CompetitionApiService/ListCompetitions' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{}
EOF
```

Optional parameters: `search`, `category`, `pageSize`

## Kernels (Notebooks)

### List Kernels
```bash
maton api -X POST '/kaggle/v1/kernels.KernelsApiService/ListKernels' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{}
EOF
```

Optional parameters: `search`, `user`, `language`, `pageSize`

### Get Kernel
```bash
maton api -X POST '/kaggle/v1/kernels.KernelsApiService/GetKernel' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "userName": "username",
  "kernelSlug": "kernel-slug"
}
EOF
```

## Notes

- All requests use POST method with JSON body
- API follows RPC pattern: `/v1/{ServiceName}/{MethodName}`
- Dataset refs: `{owner}/{dataset-slug}`
- Model refs: `{owner}/{model-slug}`
- Kernel refs: `{user}/{kernel-slug}`
- Download endpoints return binary ZIP files
- Authentication uses Kaggle API key (managed via Maton connection)

## Resources

- [Kaggle API Documentation](https://www.kaggle.com/docs/api)
