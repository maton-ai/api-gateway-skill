# Firebase

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `firebase`
**Upstream base URL:** `firebase.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://firebase.googleapis.com/v1beta1/projects`
- Gateway: `https://api.maton.ai/firebase/v1beta1/projects`

### Project API

#### List Projects

List all Firebase projects accessible to the authenticated user.

```bash
maton api '/firebase/v1beta1/projects'
```

**Response:**
```json
{
  "results": [
    {
      "projectId": "my-firebase-project",
      "projectNumber": "123456789",
      "displayName": "My Firebase Project",
      "name": "projects/my-firebase-project",
      "resources": {
        "hostingSite": "my-firebase-project"
      },
      "state": "ACTIVE",
      "etag": "1_bc06d94f-cf77-4689-be01-576702b23f6a"
    }
  ]
}
```

#### Get Project

```bash
maton api '/firebase/v1beta1/projects/{projectId}'
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Project

```bash
maton api -X PATCH '/firebase/v1beta1/projects/{projectId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "displayName": "Updated Project Name"
}
EOF
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

#### List Available Projects

List Google Cloud projects that can have Firebase added.

```bash
maton api '/firebase/v1beta1/availableProjects'
```

#### Add Firebase to Project

Add Firebase services to an existing Google Cloud project.

```bash
maton api -X POST '/firebase/v1beta1/projects/{projectId}:addFirebase' -H 'Content-Type: application/json' --input - <<'JSON'
{}
JSON
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

This returns a long-running operation. Check the operation status with:

```bash
maton api '/firebase/v1beta1/operations/{operationId}'
```

**Note:** `{operationId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Admin SDK Config

```bash
maton api '/firebase/v1beta1/projects/{projectId}/adminSdkConfig'
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

### Web App API

#### List Web Apps

```bash
maton api '/firebase/v1beta1/projects/{projectId}/webApps'
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Web App

```bash
maton api '/firebase/v1beta1/projects/{projectId}/webApps/{appId}'
```

**Note:** `{projectId}` and `{appId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Web App

```bash
maton api -X POST '/firebase/v1beta1/projects/{projectId}/webApps' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "displayName": "My Web App"
}
JSON
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Web App

```bash
maton api -X PATCH '/firebase/v1beta1/projects/{projectId}/webApps/{appId}?updateMask=displayName' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "displayName": "Updated Web App Name"
}
JSON
```

**Note:** `{projectId}` and `{appId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Web App Config

```bash
maton api '/firebase/v1beta1/projects/{projectId}/webApps/{appId}/config'
```

**Note:** `{projectId}` and `{appId}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "projectId": "my-firebase-project",
  "appId": "1:123456789:web:abc123",
  "apiKey": "AIzaSy...",
  "authDomain": "my-firebase-project.firebaseapp.com",
  "storageBucket": "my-firebase-project.firebasestorage.app",
  "messagingSenderId": "123456789",
  "measurementId": "G-XXXXXXXXXX",
  "projectNumber": "123456789"
}
```

#### Delete Web App

```bash
maton api -X POST '/firebase/v1beta1/projects/{projectId}/webApps/{appId}:remove' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "immediate": true
}
JSON
```

**Note:** `{projectId}` and `{appId}` are placeholders. Replace each of them with real values before sending the request.

#### Undelete Web App

```bash
maton api -X POST '/firebase/v1beta1/projects/{projectId}/webApps/{appId}:undelete' -H 'Content-Type: application/json' --input - <<'JSON'
{}
JSON
```

**Note:** `{projectId}` and `{appId}` are placeholders. Replace each of them with real values before sending the request.

### Android App API

#### List Android Apps

```bash
maton api '/firebase/v1beta1/projects/{projectId}/androidApps'
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Android App

```bash
maton api '/firebase/v1beta1/projects/{projectId}/androidApps/{appId}'
```

**Note:** `{projectId}` and `{appId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Android App

```bash
maton api -X POST '/firebase/v1beta1/projects/{projectId}/androidApps' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "displayName": "My Android App",
  "packageName": "com.example.myapp"
}
JSON
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Android App

```bash
maton api -X PATCH '/firebase/v1beta1/projects/{projectId}/androidApps/{appId}?updateMask=displayName' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "displayName": "Updated Android App Name"
}
JSON
```

**Note:** `{projectId}` and `{appId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Android App Config

Returns the google-services.json configuration.

```bash
maton api '/firebase/v1beta1/projects/{projectId}/androidApps/{appId}/config'
```

**Note:** `{projectId}` and `{appId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Android App

```bash
maton api -X POST '/firebase/v1beta1/projects/{projectId}/androidApps/{appId}:remove' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "immediate": true
}
JSON
```

**Note:** `{projectId}` and `{appId}` are placeholders. Replace each of them with real values before sending the request.

#### List SHA Certificates

```bash
maton api '/firebase/v1beta1/projects/{projectId}/androidApps/{appId}/sha'
```

**Note:** `{projectId}` and `{appId}` are placeholders. Replace each of them with real values before sending the request.

#### Add SHA Certificate

```bash
maton api -X POST '/firebase/v1beta1/projects/{projectId}/androidApps/{appId}/sha' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "shaHash": "1234567890ABCDEF1234567890ABCDEF12345678",
  "certType": "SHA_1"
}
JSON
```

**Note:** `{projectId}` and `{appId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete SHA Certificate

```bash
maton api '/firebase/v1beta1/projects/{projectId}/androidApps/{appId}/sha/{shaId}' -X DELETE
```

**Note:** `{projectId}`, `{appId}` and `{shaId}` are placeholders. Replace each of them with real values before sending the request.

### iOS App API

#### List iOS Apps

```bash
maton api '/firebase/v1beta1/projects/{projectId}/iosApps'
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

#### Get iOS App

```bash
maton api '/firebase/v1beta1/projects/{projectId}/iosApps/{appId}'
```

**Note:** `{projectId}` and `{appId}` are placeholders. Replace each of them with real values before sending the request.

#### Create iOS App

```bash
maton api -X POST '/firebase/v1beta1/projects/{projectId}/iosApps' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "displayName": "My iOS App",
  "bundleId": "com.example.myapp"
}
JSON
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

#### Update iOS App

```bash
maton api -X PATCH '/firebase/v1beta1/projects/{projectId}/iosApps/{appId}?updateMask=displayName' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "displayName": "Updated iOS App Name"
}
JSON
```

**Note:** `{projectId}` and `{appId}` are placeholders. Replace each of them with real values before sending the request.

#### Get iOS App Config

Returns the `GoogleService-Info` iOS configuration file.

```bash
maton api '/firebase/v1beta1/projects/{projectId}/iosApps/{appId}/config'
```

**Note:** `{projectId}` and `{appId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete iOS App

```bash
maton api -X POST '/firebase/v1beta1/projects/{projectId}/iosApps/{appId}:remove' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "immediate": true
}
JSON
```

**Note:** `{projectId}` and `{appId}` are placeholders. Replace each of them with real values before sending the request.

### Google Analytics API

#### Get Analytics Details

```bash
maton api '/firebase/v1beta1/projects/{projectId}/analyticsDetails'
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

#### Add Google Analytics

```bash
maton api -X POST '/firebase/v1beta1/projects/{projectId}:addGoogleAnalytics' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "analyticsAccountId": "123456789"
}
JSON
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

#### Remove Google Analytics

```bash
maton api -X POST '/firebase/v1beta1/projects/{projectId}:removeAnalytics' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "analyticsPropertyId": "properties/123456789"
}
JSON
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

### Available Locations API

#### List Available Locations

```bash
maton api '/firebase/v1beta1/projects/{projectId}/availableLocations'
```

**Note:** `{projectId}` is a placeholder. Replace it with a real value before sending the request.

### Notes

- Project IDs are globally unique identifiers for Firebase projects
- App IDs follow the format `1:PROJECT_NUMBER:PLATFORM:HASH`
- Create operations are asynchronous and return an Operation object
- Deleted apps can be restored within 30 days using the undelete endpoint
- Use `availableProjects` to list GCP projects that can have Firebase added

### Resources

- [Firebase Management API Overview](https://firebase.google.com/docs/projects/api/workflow_set-up-and-manage-project)
- [Firebase Management REST API Reference](https://firebase.google.com/docs/reference/firebase-management/rest)
- [Projects Resource](https://firebase.google.com/docs/reference/firebase-management/rest/v1beta1/projects)
- [Web Apps Resource](https://firebase.google.com/docs/reference/firebase-management/rest/v1beta1/projects.webApps)
- [Android Apps Resource](https://firebase.google.com/docs/reference/firebase-management/rest/v1beta1/projects.androidApps)
- [iOS Apps Resource](https://firebase.google.com/docs/reference/firebase-management/rest/v1beta1/projects.iosApps)
- [Maton CLI Manual](https://cli.maton.ai/manual)
