# Google Tag Manager

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `google-tag-manager`
**Upstream base URL:** `tagmanager.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://tagmanager.googleapis.com/tagmanager/v2/accounts`
- Gateway: `https://api.maton.ai/google-tag-manager/tagmanager/v2/accounts`

**Important:** Resources follow a hierarchical pattern: `accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/{resource}/{resourceId}`

### Accounts API

#### List Accounts

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts'
```

**Response:**
```json
{
  "account": [
    {
      "path": "accounts/6353461358",
      "accountId": "6353461358",
      "name": "My Company",
      "features": {
        "supportUserPermissions": true,
        "supportMultipleContainers": true
      }
    }
  ]
}
```

#### Get Account

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

### Containers API

#### List Containers

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "container": [
    {
      "path": "accounts/6353461358/containers/251407136",
      "accountId": "6353461358",
      "containerId": "251407136",
      "name": "example.com",
      "publicId": "GTM-XXXXXXX",
      "usageContext": ["web"],
      "tagIds": ["GTM-XXXXXXX"],
      "features": {
        "supportTags": true,
        "supportTriggers": true,
        "supportVariables": true,
        "supportVersions": true,
        "supportEnvironments": true,
        "supportWorkspaces": true,
        "supportFolders": true,
        "supportTemplates": true,
        "supportBuiltInVariables": true,
        "supportZones": true
      }
    }
  ]
}
```

#### Create Container

```bash
maton api -X POST '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Container",
  "usageContext": ["web"]
}
JSON
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

**Valid usage contexts:** `web`, `android`, `ios`, `amp`

#### Delete Container

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}' -X DELETE
```

**Note:** `{accountId}` and `{containerId}` are placeholders. Replace each of them with real values before sending the request.

### Workspaces API

#### List Workspaces

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces'
```

**Note:** `{accountId}` and `{containerId}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "workspace": [
    {
      "path": "accounts/6353461358/containers/251407136/workspaces/2",
      "accountId": "6353461358",
      "containerId": "251407136",
      "workspaceId": "2",
      "name": "Default Workspace"
    }
  ]
}
```

#### Create Workspace

```bash
maton api -X POST '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "My Feature Workspace",
  "description": "Working on new tracking features"
}
JSON
```

**Note:** `{accountId}` and `{containerId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Workspace Status

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/status'
```

**Note:** `{accountId}`, `{containerId}` and `{workspaceId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Version from Workspace

```bash
maton api -X POST '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}:create_version' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "v2.0",
  "notes": "Added new tracking tags"
}
JSON
```

**Note:** `{accountId}`, `{containerId}` and `{workspaceId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Workspace

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}' -X DELETE
```

**Note:** `{accountId}`, `{containerId}` and `{workspaceId}` are placeholders. Replace each of them with real values before sending the request.

### Tags API

#### List Tags

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/tags'
```

**Note:** `{accountId}`, `{containerId}` and `{workspaceId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Tag

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/tags/{tagId}'
```

**Note:** `{accountId}`, `{containerId}`, `{workspaceId}` and `{tagId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Tag

```bash
maton api -X POST '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/tags' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Custom HTML Tag",
  "type": "html",
  "parameter": [
    {
      "type": "template",
      "key": "html",
      "value": "<script>console.log('hello');</script>"
    }
  ],
  "firingTriggerId": ["{triggerId}"]
}
JSON
```

**Note:** `{accountId}`, `{containerId}`, `{workspaceId}` and `{triggerId}` are placeholders. Replace each of them with real values before sending the request.

**Common tag types:** `html` (Custom HTML), `ua` (Universal Analytics), `gaawc` (GA4 Config), `gaawe` (GA4 Event), `gclidw` (Conversion Linker), `img` (Custom Image)

**Example:**

```bash
maton api -X POST '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/tags' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Custom HTML Tag",
  "type": "html",
  "parameter": [
    {
      "type": "template",
      "key": "html",
      "value": "<script>console.log('hello');</script>"
    }
  ],
  "firingTriggerId": ["{triggerId}"]
}
JSON
```

**Note:** `{accountId}`, `{containerId}`, `{workspaceId}` and `{triggerId}` are placeholders. Replace each of them with real values before sending the request.

#### Update Tag

```bash
maton api -X PUT '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/tags/{tagId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Tag Name",
  "type": "html",
  "parameter": [...],
  "firingTriggerId": ["{triggerId}"],
  "fingerprint": "{current_fingerprint}"
}
JSON
```

**Note:** `{accountId}`, `{containerId}`, `{workspaceId}`, `{tagId}`, `{triggerId}` and `{current_fingerprint}` are placeholders. Replace each of them with real values before sending the request.

Include the current `fingerprint` value to ensure you're updating the latest version.

#### Delete Tag

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/tags/{tagId}' -X DELETE
```

**Note:** `{accountId}`, `{containerId}`, `{workspaceId}` and `{tagId}` are placeholders. Replace each of them with real values before sending the request.

### Triggers API

#### List Triggers

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/triggers'
```

**Note:** `{accountId}`, `{containerId}` and `{workspaceId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Trigger

```bash
maton api -X POST '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/triggers' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Click on CTA Button",
  "type": "click",
  "filter": [
    {
      "type": "equals",
      "parameter": [
        {
          "type": "template",
          "key": "arg0",
          "value": "{{Click Classes}}"
        },
        {
          "type": "template",
          "key": "arg1",
          "value": "cta-button"
        }
      ]
    }
  ]
}
JSON
```

**Note:** `{accountId}`, `{containerId}` and `{workspaceId}` are placeholders. Replace each of them with real values before sending the request.

**Common trigger types:** `pageview`, `domReady`, `windowLoaded`, `customEvent`, `click`, `linkClick`, `formSubmit`, `timer`, `scrollDepth`

**Example with filter:**

```bash
maton api -X POST '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/triggers' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Click on CTA Button",
  "type": "click",
  "filter": [
    {
      "type": "equals",
      "parameter": [
        {
          "type": "template",
          "key": "arg0",
          "value": "{{Click Classes}}"
        },
        {
          "type": "template",
          "key": "arg1",
          "value": "cta-button"
        }
      ]
    }
  ]
}
JSON
```

**Note:** `{accountId}`, `{containerId}` and `{workspaceId}` are placeholders. Replace each of them with real values before sending the request.

#### Update Trigger

```bash
maton api -X PUT '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/triggers/{triggerId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Trigger",
  "type": "pageview",
  "fingerprint": "{current_fingerprint}"
}
JSON
```

**Note:** `{accountId}`, `{containerId}`, `{workspaceId}`, `{triggerId}` and `{current_fingerprint}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Trigger

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/triggers/{triggerId}' -X DELETE
```

**Note:** `{accountId}`, `{containerId}`, `{workspaceId}` and `{triggerId}` are placeholders. Replace each of them with real values before sending the request.

### Variables API

#### List Variables

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/variables'
```

**Note:** `{accountId}`, `{containerId}` and `{workspaceId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Variable

```bash
maton api -X POST '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/variables' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Data Layer Variable",
  "type": "v",
  "parameter": [
    {"type": "integer", "key": "dataLayerVersion", "value": "2"},
    {"type": "template", "key": "name", "value": "myDataLayerVar"}
  ]
}
JSON
```

**Note:** `{accountId}`, `{containerId}` and `{workspaceId}` are placeholders. Replace each of them with real values before sending the request.

**Common variable types:** `v` (Data Layer), `j` (JavaScript Variable), `jsm` (Custom JavaScript), `c` (Constant), `k` (Cookie), `u` (URL), `f` (DOM Element)

#### Update Variable

```bash
maton api -X PUT '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/variables/{variableId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Variable",
  "type": "v",
  "parameter": [...],
  "fingerprint": "{current_fingerprint}"
}
JSON
```

**Note:** `{accountId}`, `{containerId}`, `{workspaceId}`, `{variableId}` and `{current_fingerprint}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Variable

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/variables/{variableId}' -X DELETE
```

**Note:** `{accountId}`, `{containerId}`, `{workspaceId}` and `{variableId}` are placeholders. Replace each of them with real values before sending the request.

### Built-In Variables API

#### List Built-In Variables

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/built_in_variables'
```

**Note:** `{accountId}`, `{containerId}` and `{workspaceId}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "builtInVariable": [
    {
      "path": "accounts/6353461358/containers/251407136/workspaces/2/built_in_variables",
      "type": "pageUrl",
      "name": "Page URL"
    },
    {
      "type": "pageHostname",
      "name": "Page Hostname"
    },
    {
      "type": "pagePath",
      "name": "Page Path"
    },
    {
      "type": "referrer",
      "name": "Referrer"
    },
    {
      "type": "event",
      "name": "Event"
    }
  ]
}
```

### Environments API

#### List Environments

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/environments'
```

**Note:** `{accountId}` and `{containerId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Environment

```bash
maton api -X POST '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/environments' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Staging",
  "description": "Staging environment for testing"
}
JSON
```

**Note:** `{accountId}` and `{containerId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Environment

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/environments/{environmentId}' -X DELETE
```

**Note:** `{accountId}`, `{containerId}` and `{environmentId}` are placeholders. Replace each of them with real values before sending the request.

### Container Versions API

#### List Version Headers

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/version_headers'
```

**Note:** `{accountId}` and `{containerId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Version

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/versions/{versionId}'
```

**Note:** `{accountId}`, `{containerId}` and `{versionId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Live Version

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/versions:live'
```

**Note:** `{accountId}` and `{containerId}` are placeholders. Replace each of them with real values before sending the request.

#### Publish Version

```bash
maton api -X POST '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/versions/{versionId}:publish'
```

**Note:** `{accountId}`, `{containerId}` and `{versionId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Version

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/versions/{versionId}' -X DELETE
```

**Note:** `{accountId}`, `{containerId}` and `{versionId}` are placeholders. Replace each of them with real values before sending the request.

### User Permissions API

#### List User Permissions

```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/user_permissions'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "userPermission": [
    {
      "path": "accounts/6353461358/user_permissions/05842032124443686272",
      "accountId": "6353461358",
      "emailAddress": "user@example.com",
      "accountAccess": {
        "permission": "admin"
      },
      "containerAccess": [
        {
          "containerId": "251407136",
          "permission": "publish"
        }
      ]
    }
  ]
}
```

#### Create User Permission

```bash
maton api -X POST '/google-tag-manager/tagmanager/v2/accounts/{accountId}/user_permissions' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "emailAddress": "newuser@example.com",
  "accountAccess": {
    "permission": "user"
  },
  "containerAccess": [
    {
      "containerId": "{containerId}",
      "permission": "read"
    }
  ]
}
JSON
```

**Note:** `{accountId}` and `{containerId}` are placeholders. Replace each of them with real values before sending the request.

**Permission levels:** `noAccess`, `read`, `edit`, `approve`, `publish` (container); `noAccess`, `user`, `admin` (account)

### Notes

- Updates (PUT) require the full resource body including `fingerprint` for concurrency control
- Common tag types: `html`, `gaawc` (GA4 Config), `gaawe` (GA4 Event)
- Common trigger types: `pageview`, `domReady`, `customEvent`, `click`, `formSubmit`
- Common variable types: `v` (Data Layer), `j` (JS Variable), `c` (Constant), `k` (Cookie)
- Special actions use colon syntax: `:publish`, `:create_version`, `:revert`, `:sync`
- Built-in trigger ID `2147479553` = "All Pages"

### Resources

- [Google Tag Manager API Reference](https://developers.google.com/tag-platform/tag-manager/api/reference/rest)
- [GTM API v2 Guide](https://developers.google.com/tag-platform/tag-manager/api/v2)
- [Maton CLI Manual](https://cli.maton.ai/manual)
