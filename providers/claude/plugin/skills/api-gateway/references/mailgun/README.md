# Mailgun Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `mailgun`
**Base URL proxied:** `api.mailgun.net`

## API Path Pattern

```
/mailgun/v3/{resource}
```

## Common Endpoints

### Domains

#### List Domains
```bash
maton api '/mailgun/v3/domains'
```

#### Get Domain
```bash
maton api '/mailgun/v3/domains/{domain_name}'
```

#### Create Domain
```bash
maton api -X POST '/mailgun/v3/domains'
```

#### Delete Domain
```bash
maton api -X DELETE '/mailgun/v3/domains/{domain_name}'
```

### Messages

#### Send Message
```bash
maton api -X POST '/mailgun/v3/{domain_name}/messages'
```

#### Send MIME Message
```bash
maton api -X POST '/mailgun/v3/{domain_name}/messages.mime'
```

### Events

#### List Events
```bash
maton api '/mailgun/v3/{domain_name}/events'
```

### Routes

#### List Routes
```bash
maton api '/mailgun/v3/routes'
```

#### Create Route
```bash
maton api -X POST '/mailgun/v3/routes'
```

#### Get Route
```bash
maton api '/mailgun/v3/routes/{route_id}'
```

#### Update Route
```bash
maton api -X PUT '/mailgun/v3/routes/{route_id}'
```

#### Delete Route
```bash
maton api -X DELETE '/mailgun/v3/routes/{route_id}'
```

### Webhooks

#### List Webhooks
```bash
maton api '/mailgun/v3/domains/{domain_name}/webhooks'
```

#### Create Webhook
```bash
maton api -X POST '/mailgun/v3/domains/{domain_name}/webhooks'
```

#### Get Webhook
```bash
maton api '/mailgun/v3/domains/{domain_name}/webhooks/{webhook_type}'
```

#### Update Webhook
```bash
maton api -X PUT '/mailgun/v3/domains/{domain_name}/webhooks/{webhook_type}'
```

#### Delete Webhook
```bash
maton api -X DELETE '/mailgun/v3/domains/{domain_name}/webhooks/{webhook_type}'
```

### Templates

#### List Templates
```bash
maton api '/mailgun/v3/{domain_name}/templates'
```

#### Create Template
```bash
maton api -X POST '/mailgun/v3/{domain_name}/templates'
```

#### Get Template
```bash
maton api '/mailgun/v3/{domain_name}/templates/{template_name}'
```

#### Delete Template
```bash
maton api -X DELETE '/mailgun/v3/{domain_name}/templates/{template_name}'
```

### Mailing Lists

#### List Mailing Lists
```bash
maton api '/mailgun/v3/lists/pages'
```

#### Create Mailing List
```bash
maton api -X POST '/mailgun/v3/lists'
```

#### Get Mailing List
```bash
maton api '/mailgun/v3/lists/{list_address}'
```

#### Update Mailing List
```bash
maton api -X PUT '/mailgun/v3/lists/{list_address}'
```

#### Delete Mailing List
```bash
maton api -X DELETE '/mailgun/v3/lists/{list_address}'
```

### Mailing List Members

#### List Members
```bash
maton api '/mailgun/v3/lists/{list_address}/members/pages'
```

#### Add Member
```bash
maton api -X POST '/mailgun/v3/lists/{list_address}/members'
```

#### Get Member
```bash
maton api '/mailgun/v3/lists/{list_address}/members/{member_address}'
```

#### Update Member
```bash
maton api -X PUT '/mailgun/v3/lists/{list_address}/members/{member_address}'
```

#### Delete Member
```bash
maton api -X DELETE '/mailgun/v3/lists/{list_address}/members/{member_address}'
```

### Suppressions

#### Bounces
```bash
maton api '/mailgun/v3/{domain_name}/bounces'
maton api -X POST '/mailgun/v3/{domain_name}/bounces'
maton api '/mailgun/v3/{domain_name}/bounces/{address}'
maton api -X DELETE '/mailgun/v3/{domain_name}/bounces/{address}'
```

#### Unsubscribes
```bash
maton api '/mailgun/v3/{domain_name}/unsubscribes'
maton api -X POST '/mailgun/v3/{domain_name}/unsubscribes'
maton api -X DELETE '/mailgun/v3/{domain_name}/unsubscribes/{address}'
```

#### Complaints
```bash
maton api '/mailgun/v3/{domain_name}/complaints'
maton api -X POST '/mailgun/v3/{domain_name}/complaints'
maton api -X DELETE '/mailgun/v3/{domain_name}/complaints/{address}'
```

#### Whitelists
```bash
maton api '/mailgun/v3/{domain_name}/whitelists'
maton api -X POST '/mailgun/v3/{domain_name}/whitelists'
maton api -X DELETE '/mailgun/v3/{domain_name}/whitelists/{address}'
```

### Statistics

#### Get Stats
```bash
maton api '/mailgun/v3/{domain_name}/stats/total?event=delivered'
```

### Tags

#### List Tags
```bash
maton api '/mailgun/v3/{domain_name}/tags'
```

#### Get Tag
```bash
maton api '/mailgun/v3/{domain_name}/tags/{tag_name}'
```

#### Delete Tag
```bash
maton api -X DELETE '/mailgun/v3/{domain_name}/tags/{tag_name}'
```

### IPs

#### List IPs
```bash
maton api '/mailgun/v3/ips'
```

#### Get IP
```bash
maton api '/mailgun/v3/ips/{ip_address}'
```

### Domain Tracking

#### Get Tracking Settings
```bash
maton api '/mailgun/v3/domains/{domain_name}/tracking'
```

#### Update Tracking
```bash
maton api -X PUT '/mailgun/v3/domains/{domain_name}/tracking/open'
maton api -X PUT '/mailgun/v3/domains/{domain_name}/tracking/click'
maton api -X PUT '/mailgun/v3/domains/{domain_name}/tracking/unsubscribe'
```

### Credentials

#### List Credentials
```bash
maton api '/mailgun/v3/domains/{domain_name}/credentials'
```

#### Create Credential
```bash
maton api -X POST '/mailgun/v3/domains/{domain_name}/credentials'
```

#### Delete Credential
```bash
maton api -X DELETE '/mailgun/v3/domains/{domain_name}/credentials/{login}'
```

## Notes

- Mailgun uses `application/x-www-form-urlencoded` for POST/PUT requests, not JSON
- Routes are global (per account), not per domain
- Sandbox domains require authorized recipients
- Event logs stored for at least 3 days
- Stats require at least one `event` parameter
- US region: api.mailgun.net, EU region: api.eu.mailgun.net

## Resources

- [Mailgun API Documentation](https://documentation.mailgun.com/docs/mailgun/api-reference/api-overview)
