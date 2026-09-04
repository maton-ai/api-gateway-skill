# Firebase Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `firebase`
**Base URL proxied:** `firebase.googleapis.com`

## API Path Pattern

```
/firebase/v1beta1/{resource}
```

## Common Endpoints

### List Projects
```bash
maton api '/firebase/v1beta1/projects'
```

### Get Project
```bash
maton api '/firebase/v1beta1/projects/{projectId}'
```

### Update Project
```bash
maton api -X PATCH '/firebase/v1beta1/projects/{projectId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "displayName": "Updated Project Name"
}
EOF
```

### List Available Projects
```bash
maton api '/firebase/v1beta1/availableProjects'
```

### Add Firebase to Project
```bash
maton api -X POST '/firebase/v1beta1/projects/{projectId}:addFirebase' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{}
EOF
```

### Get Admin SDK Config
```bash
maton api '/firebase/v1beta1/projects/{projectId}/adminSdkConfig'
```

### List Web Apps
```bash
maton api '/firebase/v1beta1/projects/{projectId}/webApps'
```

### Get Web App
```bash
maton api '/firebase/v1beta1/projects/{projectId}/webApps/{appId}'
```

### Create Web App
```bash
maton api -X POST '/firebase/v1beta1/projects/{projectId}/webApps' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "displayName": "My Web App"
}
EOF
```

### Get Web App Config
```bash
maton api '/firebase/v1beta1/projects/{projectId}/webApps/{appId}/config'
```

### List Android Apps
```bash
maton api '/firebase/v1beta1/projects/{projectId}/androidApps'
```

### Create Android App
```bash
maton api -X POST '/firebase/v1beta1/projects/{projectId}/androidApps' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "displayName": "My Android App",
  "packageName": "com.example.myapp"
}
EOF
```

### Get Android App Config
```bash
maton api '/firebase/v1beta1/projects/{projectId}/androidApps/{appId}/config'
```

### List iOS Apps
```bash
maton api '/firebase/v1beta1/projects/{projectId}/iosApps'
```

### Create iOS App
```bash
maton api -X POST '/firebase/v1beta1/projects/{projectId}/iosApps' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "displayName": "My iOS App",
  "bundleId": "com.example.myapp"
}
EOF
```

### Get iOS App Config
```bash
maton api '/firebase/v1beta1/projects/{projectId}/iosApps/{appId}/config'
```

### Check Operation Status
```bash
maton api '/firebase/v1beta1/operations/{operationId}'
```

## Notes

- Project IDs are globally unique identifiers for Firebase projects
- App IDs follow the format `1:PROJECT_NUMBER:PLATFORM:HASH`
- Create operations are asynchronous and return an Operation object
- Deleted apps can be restored within 30 days using the undelete endpoint
- Use `availableProjects` to list GCP projects that can have Firebase added

## Resources

- [Firebase Management API Overview](https://firebase.google.com/docs/projects/api/workflow_set-up-and-manage-project)
- [Firebase Management REST API Reference](https://firebase.google.com/docs/reference/firebase-management/rest)
- [Projects Resource](https://firebase.google.com/docs/reference/firebase-management/rest/v1beta1/projects)
- [Web Apps Resource](https://firebase.google.com/docs/reference/firebase-management/rest/v1beta1/projects.webApps)
- [Android Apps Resource](https://firebase.google.com/docs/reference/firebase-management/rest/v1beta1/projects.androidApps)
- [iOS Apps Resource](https://firebase.google.com/docs/reference/firebase-management/rest/v1beta1/projects.iosApps)
