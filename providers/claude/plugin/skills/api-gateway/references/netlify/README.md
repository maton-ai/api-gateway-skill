# Netlify Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `netlify`
**Base URL proxied:** `api.netlify.com`

## API Path Pattern

```
/netlify/api/v1/{resource}
```

## Common Endpoints

### User

```bash
maton api '/netlify/api/v1/user'
```

### Accounts

```bash
maton api '/netlify/api/v1/accounts'
maton api '/netlify/api/v1/accounts/{account_id}'
maton api -X POST '/netlify/api/v1/accounts'
maton api -X PUT '/netlify/api/v1/accounts/{account_id}'
```

### Sites

```bash
maton api '/netlify/api/v1/sites'
maton api '/netlify/api/v1/sites/{site_id}'
maton api -X POST '/netlify/api/v1/sites'
maton api -X PUT '/netlify/api/v1/sites/{site_id}'
maton api -X DELETE '/netlify/api/v1/sites/{site_id}'
maton api -X PUT '/netlify/api/v1/sites/{site_id}/disable'
maton api -X PUT '/netlify/api/v1/sites/{site_id}/enable'
maton api '/netlify/api/v1/{account_slug}/sites'
maton api -X POST '/netlify/api/v1/{account_slug}/sites'
```

### Deploys

```bash
maton api '/netlify/api/v1/sites/{site_id}/deploys'
maton api '/netlify/api/v1/deploys/{deploy_id}'
maton api -X POST '/netlify/api/v1/sites/{site_id}/deploys'
maton api -X POST '/netlify/api/v1/sites/{site_id}/deploys/{deploy_id}/cancel'
maton api -X POST '/netlify/api/v1/sites/{site_id}/deploys/{deploy_id}/restore'
maton api -X POST '/netlify/api/v1/deploys/{deploy_id}/lock'
maton api -X POST '/netlify/api/v1/deploys/{deploy_id}/unlock'
```

### Builds

```bash
maton api '/netlify/api/v1/sites/{site_id}/builds'
maton api '/netlify/api/v1/builds/{build_id}'
maton api -X POST '/netlify/api/v1/sites/{site_id}/builds'
```

### Environment Variables

Environment variables are managed at the account level with optional site scope.

```bash
maton api '/netlify/api/v1/accounts/{account_id}/env?site_id={site_id}'
maton api -X POST '/netlify/api/v1/accounts/{account_id}/env?site_id={site_id}'
maton api -X PUT '/netlify/api/v1/accounts/{account_id}/env/{key}?site_id={site_id}'
maton api -X DELETE '/netlify/api/v1/accounts/{account_id}/env/{key}?site_id={site_id}'
```

### DNS Zones

```bash
maton api '/netlify/api/v1/dns_zones'
maton api '/netlify/api/v1/dns_zones/{zone_id}'
maton api -X POST '/netlify/api/v1/dns_zones'
maton api -X DELETE '/netlify/api/v1/dns_zones/{zone_id}'
```

### DNS Records

```bash
maton api '/netlify/api/v1/dns_zones/{zone_id}/dns_records'
maton api -X POST '/netlify/api/v1/dns_zones/{zone_id}/dns_records'
maton api -X DELETE '/netlify/api/v1/dns_zones/{zone_id}/dns_records/{record_id}'
```

### Build Hooks

```bash
maton api '/netlify/api/v1/sites/{site_id}/build_hooks'
maton api -X POST '/netlify/api/v1/sites/{site_id}/build_hooks'
maton api -X DELETE '/netlify/api/v1/hooks/{hook_id}'
```

### Webhooks

```bash
maton api '/netlify/api/v1/hooks?site_id={site_id}'
maton api -X POST '/netlify/api/v1/hooks?site_id={site_id}'
maton api -X PUT '/netlify/api/v1/hooks/{hook_id}'
maton api -X DELETE '/netlify/api/v1/hooks/{hook_id}'
```

### Forms & Submissions

```bash
maton api '/netlify/api/v1/sites/{site_id}/forms'
maton api '/netlify/api/v1/forms/{form_id}/submissions'
maton api -X DELETE '/netlify/api/v1/submissions/{submission_id}'
```

### Team Members

```bash
maton api '/netlify/api/v1/{account_slug}/members'
maton api -X POST '/netlify/api/v1/{account_slug}/members'
maton api '/netlify/api/v1/{account_slug}/members/{member_id}'
maton api -X PUT '/netlify/api/v1/{account_slug}/members/{member_id}'
maton api -X DELETE '/netlify/api/v1/{account_slug}/members/{member_id}'
```

### SSL/TLS

```bash
maton api '/netlify/api/v1/sites/{site_id}/ssl'
maton api -X POST '/netlify/api/v1/sites/{site_id}/ssl'
```

### Functions

```bash
maton api '/netlify/api/v1/sites/{site_id}/functions'
```

### Services

```bash
maton api '/netlify/api/v1/services'
maton api '/netlify/api/v1/services/{service_id}'
```

## Notes

- All endpoints use the `/api/v1/` prefix
- Site IDs are UUIDs (e.g., `d37d1ce4-5444-40f5-a4ca-a2c40a8b6835`)
- Account slugs are URL-friendly team names (e.g., `my-team-slug`)
- Pagination via `page` and `per_page` query parameters
- Environment variable contexts: `all`, `production`, `deploy-preview`, `branch-deploy`, `dev`
- Build hooks return a URL that can be POSTed to trigger builds externally

## Resources

- [Netlify API Documentation](https://docs.netlify.com/api/get-started/)
- [Netlify OpenAPI Spec](https://open-api.netlify.com)
