# Google Tag Manager Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `google-tag-manager`
**Base URL proxied:** `tagmanager.googleapis.com`

## API Path Pattern

```
/google-tag-manager/tagmanager/v2/{resource-path}
```

Resources follow a hierarchical pattern:
```
accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/{resource}/{resourceId}
```

## Common Endpoints

### List Accounts
```bash
maton api '/google-tag-manager/tagmanager/v2/accounts'
```

### Get Account
```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}'
```

### List Containers
```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers'
```

### List Workspaces
```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces'
```

### List Tags
```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/tags'
```

### Create Tag
```bash
maton api -X POST '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/tags' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "My Tag",
  "type": "html",
  "parameter": [{"type": "template", "key": "html", "value": "<script>...</script>"}],
  "firingTriggerId": ["{triggerId}"]
}
EOF
```

### Update Tag
```bash
maton api -X PUT '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/tags/{tagId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{...full resource body with fingerprint...}
EOF
```

### Delete Tag
```bash
maton api -X DELETE '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/tags/{tagId}'
```

### List Triggers
```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/triggers'
```

### Create Trigger
```bash
maton api -X POST '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/triggers' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"name": "Page View", "type": "pageview"}
EOF
```

### List Variables
```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/workspaces/{workspaceId}/variables'
```

### List Environments
```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/environments'
```

### List Version Headers
```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/version_headers'
```

### Publish Version
```bash
maton api -X POST '/google-tag-manager/tagmanager/v2/accounts/{accountId}/containers/{containerId}/versions/{versionId}:publish'
```

### List User Permissions
```bash
maton api '/google-tag-manager/tagmanager/v2/accounts/{accountId}/user_permissions'
```

## Notes

- All paths include the `tagmanager/v2` prefix after the app name
- Updates (PUT) require the full resource body including `fingerprint` for concurrency control
- Common tag types: `html`, `gaawc` (GA4 Config), `gaawe` (GA4 Event)
- Common trigger types: `pageview`, `domReady`, `customEvent`, `click`, `formSubmit`
- Common variable types: `v` (Data Layer), `j` (JS Variable), `c` (Constant), `k` (Cookie)
- Special actions use colon syntax: `:publish`, `:create_version`, `:revert`, `:sync`
- Built-in trigger ID `2147479553` = "All Pages"

## Resources

- [Google Tag Manager API Reference](https://developers.google.com/tag-platform/tag-manager/api/reference/rest)
- [GTM API v2 Guide](https://developers.google.com/tag-platform/tag-manager/api/v2)
