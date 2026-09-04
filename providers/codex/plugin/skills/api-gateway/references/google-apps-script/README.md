# Google Apps Script Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `google-apps-script`
**Base URL proxied:** `script.googleapis.com`

## API Path Pattern

```
/google-apps-script/v1/{resource}
```

## Common Endpoints

### Create Project
```bash
maton api -X POST '/google-apps-script/v1/projects' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"title": "My Script", "parentId": "{optional_drive_file_id}"}
EOF
```

### Get Project
```bash
maton api '/google-apps-script/v1/projects/{scriptId}'
```

### Get Project Content
```bash
maton api '/google-apps-script/v1/projects/{scriptId}/content'
```

With specific version:
```bash
maton api '/google-apps-script/v1/projects/{scriptId}/content?versionNumber=1'
```

### Update Project Content
```bash
maton api -X PUT '/google-apps-script/v1/projects/{scriptId}/content' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "files": [
    {"name": "appsscript", "type": "JSON", "source": "{...manifest...}"},
    {"name": "Code", "type": "SERVER_JS", "source": "function main() {}"}
  ]
}
EOF
```

### Get Project Metrics
```bash
maton api '/google-apps-script/v1/projects/{scriptId}/metrics?metricsGranularity=DAILY'
```

### Create Version
```bash
maton api -X POST '/google-apps-script/v1/projects/{scriptId}/versions' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"description": "v1.0"}
EOF
```

### List Versions
```bash
maton api '/google-apps-script/v1/projects/{scriptId}/versions'
```

### Get Version
```bash
maton api '/google-apps-script/v1/projects/{scriptId}/versions/{versionNumber}'
```

### Create Deployment
```bash
maton api -X POST '/google-apps-script/v1/projects/{scriptId}/deployments' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"versionNumber": 1, "description": "Production"}
EOF
```

### List Deployments
```bash
maton api '/google-apps-script/v1/projects/{scriptId}/deployments'
```

### Get Deployment
```bash
maton api '/google-apps-script/v1/projects/{scriptId}/deployments/{deploymentId}'
```

### Update Deployment
```bash
maton api -X PUT '/google-apps-script/v1/projects/{scriptId}/deployments/{deploymentId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"deploymentConfig": {"scriptId": "...", "versionNumber": 2, "description": "Updated"}}
EOF
```

### Delete Deployment
```bash
maton api -X DELETE '/google-apps-script/v1/projects/{scriptId}/deployments/{deploymentId}'
```

### List Processes
```bash
maton api '/google-apps-script/v1/processes'
maton api '/google-apps-script/v1/processes?pageSize=10'
```

### List Script Processes
```bash
maton api '/google-apps-script/v1/processes:listScriptProcesses?scriptId={scriptId}'
```

### Run Function
```bash
maton api -X POST '/google-apps-script/v1/scripts/{scriptId}:run' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"function": "myFunction", "parameters": ["arg1"], "devMode": false}
EOF
```

## Notes

- `scriptId` is the Google Drive file ID of the Apps Script project
- `updateContent` replaces ALL files; always include the `appsscript` manifest
- File types: `SERVER_JS` (code), `HTML` (HTML files), `JSON` (manifest only)
- Versions are immutable; deploy a specific version number
- `scripts.run` requires an "API Executable" deployment
- Metrics require `metricsGranularity` parameter: `DAILY` or `WEEKLY`
- Pagination uses `pageSize` + `pageToken`/`nextPageToken`

## Resources

- [Apps Script API Reference](https://developers.google.com/apps-script/api/reference/rest)
- [Managing Deployments](https://developers.google.com/apps-script/api/how-tos/manage-deployments)
- [Executing Functions](https://developers.google.com/apps-script/api/how-tos/execute)
