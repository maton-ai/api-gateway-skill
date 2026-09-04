# Klaviyo Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `klaviyo`
**Base URL proxied:** `a.klaviyo.com`

## API Path Pattern

```
/klaviyo/api/{resource}
```

## API Versioning

Include the `revision` header in all requests:

```
revision: 2026-01-15
```

## Common Endpoints

### Get Profiles
```bash
maton api '/klaviyo/api/profiles'
```

Query parameters:
- `filter` - Filter profiles (e.g., `filter=equals(email,"test@example.com")`)
- `fields[profile]` - Comma-separated list of fields to include
- `page[size]` - Number of results per page (max 100)

### Get a Profile
```bash
maton api '/klaviyo/api/profiles/{profile_id}'
```

### Create a Profile
```bash
maton api -X POST '/klaviyo/api/profiles' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "type": "profile",
    "attributes": {
      "email": "newuser@example.com",
      "first_name": "John",
      "last_name": "Doe"
    }
  }
}
EOF
```

### Update a Profile
```bash
maton api -X PATCH '/klaviyo/api/profiles/{profile_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "type": "profile",
    "id": "PROFILE_ID",
    "attributes": {
      "first_name": "Jane"
    }
  }
}
EOF
```

### Get Lists
```bash
maton api '/klaviyo/api/lists'
```

### Create a List
```bash
maton api -X POST '/klaviyo/api/lists' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "type": "list",
    "attributes": {
      "name": "VIP Customers"
    }
  }
}
EOF
```

### Add Profiles to List
```bash
maton api -X POST '/klaviyo/api/lists/{list_id}/relationships/profiles' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": [
    {"type": "profile", "id": "PROFILE_ID"}
  ]
}
EOF
```

### Get Segments
```bash
maton api '/klaviyo/api/segments'
```

### Get Campaigns
```bash
maton api '/klaviyo/api/campaigns?filter=equals(messages.channel,"email")'
```

> **Note:** A channel filter is required (email or sms).

### Create a Campaign
```bash
maton api -X POST '/klaviyo/api/campaigns' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "type": "campaign",
    "attributes": {
      "name": "Summer Newsletter",
      "audiences": {
        "included": ["LIST_ID"]
      }
    }
  }
}
EOF
```

### Get Flows
```bash
maton api '/klaviyo/api/flows'
```

### Update Flow Status
```bash
maton api -X PATCH '/klaviyo/api/flows/{flow_id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "type": "flow",
    "id": "FLOW_ID",
    "attributes": {
      "status": "live"
    }
  }
}
EOF
```

### Create an Event
```bash
maton api -X POST '/klaviyo/api/events' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "type": "event",
    "attributes": {
      "profile": {
        "data": {
          "type": "profile",
          "attributes": {
            "email": "customer@example.com"
          }
        }
      },
      "metric": {
        "data": {
          "type": "metric",
          "attributes": {
            "name": "Viewed Product"
          }
        }
      },
      "properties": {
        "product_id": "SKU123",
        "product_name": "Blue T-Shirt"
      }
    }
  }
}
EOF
```

### Get Metrics
```bash
maton api '/klaviyo/api/metrics'
```

### Get Templates
```bash
maton api '/klaviyo/api/templates'
```

### Create Webhook
```bash
maton api -X POST '/klaviyo/api/webhooks' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "type": "webhook",
    "attributes": {
      "name": "Order Placed Webhook",
      "endpoint_url": "https://example.com/webhooks/klaviyo",
      "enabled": true
    },
    "relationships": {
      "webhook-topics": {
        "data": [
          {"type": "webhook-topic", "id": "campaign:sent"}
        ]
      }
    }
  }
}
EOF
```

### Delete Webhook
```bash
maton api -X DELETE '/klaviyo/api/webhooks/{webhook_id}'
```

### Get Webhook Topics
```bash
maton api '/klaviyo/api/webhook-topics'
```

### Get Images
```bash
maton api '/klaviyo/api/images'
```

### Get Forms
```bash
maton api '/klaviyo/api/forms'
```

### Get Reviews
```bash
maton api '/klaviyo/api/reviews'
```

### Get Tag Groups
```bash
maton api '/klaviyo/api/tag-groups'
```

### Get Universal Content
```bash
maton api '/klaviyo/api/template-universal-content'
```

### Bulk Subscribe Profiles
```bash
maton api -X POST '/klaviyo/api/profile-subscription-bulk-create-jobs' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "data": {
    "type": "profile-subscription-bulk-create-job",
    "attributes": {
      "profiles": {
        "data": [{
          "type": "profile",
          "attributes": {
            "email": "user@example.com",
            "subscriptions": {
              "email": {"marketing": {"consent": "SUBSCRIBED"}}
            }
          }
        }]
      }
    },
    "relationships": {
      "list": {"data": {"type": "list", "id": "LIST_ID"}}
    }
  }
}
EOF
```

### Get Bulk Import Jobs
```bash
maton api '/klaviyo/api/profile-bulk-import-jobs'
```

## Notes

- All requests use JSON:API specification
- Timestamps are in ISO 8601 RFC 3339 format
- Resource IDs are strings (often base64-encoded)
- Use sparse fieldsets to optimize response size (e.g., `fields[profile]=email,first_name`)
- Include `revision` header for API versioning
- Use cursor-based pagination with `page[cursor]` parameter

## Resources

- [Klaviyo API Documentation](https://developers.klaviyo.com)
- [API Reference](https://developers.klaviyo.com/en/reference/api_overview)
- [Klaviyo Developer Portal](https://developers.klaviyo.com/en)
