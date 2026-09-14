# Google Analytics Admin

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `google-analytics-admin`
**Upstream base URL:** `analyticsadmin.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://analyticsadmin.googleapis.com/v1beta/accounts`
- Gateway: `https://api.maton.ai/google-analytics-admin/v1beta/accounts`

### Accounts API

#### List Accounts

If there are multiple Google Analytics connections, specify which one to use so requests go to the intended account:

```bash
maton api '/google-analytics-admin/v1beta/accounts' --connection {connection_id}
```

**Note:** `{connection_id}` is a placeholder. Replace it with a real value before sending the request.

Refer to `maton api --help` for possible flags and values.

#### Get Account

```bash
maton api '/google-analytics-admin/v1beta/accounts/{accountId}'
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

### Properties API

#### List Properties

```bash
maton api '/google-analytics-admin/v1beta/properties?filter=parent:accounts/{accountId}'

maton api '/google-analytics-admin/v1beta/properties/{propertyId}'
```

**Note:** `{accountId}` and `{propertyId}` are placeholders. Replace each of them with real values before sending the request.

#### Update Property

```bash
maton api -X PATCH '/google-analytics-admin/v1beta/properties/{propertyId}?updateMask=displayName' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "displayName": "Updated Property Name"
}
EOF
```

**Note:** `{propertyId}` is a placeholder. Replace it with a real value before sending the request.

#### List Custom Metrics

```bash
maton api '/google-analytics-admin/v1beta/properties/{propertyId}/customMetrics'
```

**Note:** `{propertyId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Custom Metric

```bash
maton api -X POST '/google-analytics-admin/v1beta/properties/{propertyId}/customMetrics' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "parameterName": "points_earned",
  "displayName": "Points Earned",
  "scope": "EVENT",
  "measurementUnit": "STANDARD",
  "description": "Number of loyalty points earned"
}
EOF
```

**Note:** `{propertyId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Property

```bash
maton api -X POST '/google-analytics-admin/v1beta/properties' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "parent": "accounts/{accountId}",
  "displayName": "My New Property",
  "timeZone": "America/Los_Angeles",
  "currencyCode": "USD",
  "industryCategory": "TECHNOLOGY"
}
EOF
```

**Note:** `{accountId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Property

```bash
maton api '/google-analytics-admin/v1beta/properties/{propertyId}'
```

**Note:** `{propertyId}` is a placeholder. Replace it with a real value before sending the request.

### Data Streams API

#### List Data Streams

```bash
maton api '/google-analytics-admin/v1beta/properties/{propertyId}/dataStreams'
```

**Note:** `{propertyId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Data Stream

```bash
maton api '/google-analytics-admin/v1beta/properties/{propertyId}/dataStreams/{dataStreamId}'
```

**Note:** `{propertyId}` and `{dataStreamId}` are placeholders. Replace each of them with real values before sending the request.

#### List Measurement Protocol Secrets

```bash
maton api '/google-analytics-admin/v1beta/properties/{propertyId}/dataStreams/{dataStreamId}/measurementProtocolSecrets'
```

**Note:** `{propertyId}` and `{dataStreamId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Measurement Protocol Secret

```bash
maton api -X POST '/google-analytics-admin/v1beta/properties/{propertyId}/dataStreams/{dataStreamId}/measurementProtocolSecrets' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "displayName": "Server-side tracking"
}
EOF
```

**Note:** `{propertyId}` and `{dataStreamId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Web Data Stream

```bash
maton api -X POST '/google-analytics-admin/v1beta/properties/{propertyId}/dataStreams' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "type": "WEB_DATA_STREAM",
  "displayName": "My Website",
  "webStreamData": {
    "defaultUri": "https://example.com"
  }
}
EOF
```

**Note:** `{propertyId}` is a placeholder. Replace it with a real value before sending the request.

#### List Custom Dimensions

```bash
maton api '/google-analytics-admin/v1beta/properties/{propertyId}/customDimensions'
```

**Note:** `{propertyId}` is a placeholder. Replace it with a real value before sending the request.

### Custom Dimension API

#### Create Custom Dimension

```bash
maton api -X POST '/google-analytics-admin/v1beta/properties/{propertyId}/customDimensions' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "parameterName": "user_type",
  "displayName": "User Type",
  "scope": "USER",
  "description": "Type of user (free, premium, enterprise)"
}
EOF
```

**Note:** `{propertyId}` is a placeholder. Replace it with a real value before sending the request.

#### List Conversion Events

```bash
maton api '/google-analytics-admin/v1beta/properties/{propertyId}/conversionEvents'
```

**Note:** `{propertyId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Conversion Event

```bash
maton api -X POST '/google-analytics-admin/v1beta/properties/{propertyId}/conversionEvents' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "eventName": "purchase"
}
EOF
```

**Note:** `{propertyId}` is a placeholder. Replace it with a real value before sending the request.

### Account Summaries API

#### List Account Summaries

```bash
maton api '/google-analytics-admin/v1beta/accountSummaries'
```
Returns a lightweight summary of all accounts and properties the user has access to.

### Data Stream Types

- `WEB_DATA_STREAM` - Website tracking
- `ANDROID_APP_DATA_STREAM` - Android app
- `IOS_APP_DATA_STREAM` - iOS app

### Custom Dimension Scopes

- `EVENT` - Dimension applies to events
- `USER` - Dimension applies to users

### Custom Metric Scopes

- `EVENT` - Metric applies to events

### Measurement Units (Custom Metrics)

- `STANDARD` - Integer or decimal
- `CURRENCY` - Currency value
- `FEET`, `METERS` - Distance
- `MILES`, `KILOMETERS` - Distance
- `MILLISECONDS`, `SECONDS`, `MINUTES`, `HOURS` - Time

### Industry Categories

- `AUTOMOTIVE`, `BUSINESS_AND_INDUSTRIAL_MARKETS`, `FINANCE`, `HEALTHCARE`
- `TECHNOLOGY`, `TRAVEL`, `RETAIL`, `REAL_ESTATE`, `GAMES`
- `ARTS_AND_ENTERTAINMENT`, `BEAUTY_AND_FITNESS`, `BOOKS_AND_LITERATURE`
- `FOOD_AND_DRINK`, `HOBBIES_AND_LEISURE`, `HOME_AND_GARDEN`
- `INTERNET_AND_TELECOM`, `JOBS_AND_EDUCATION`, `LAW_AND_GOVERNMENT`
- `NEWS`, `ONLINE_COMMUNITIES`, `PEOPLE_AND_SOCIETY`, `PETS_AND_ANIMALS`
- `REFERENCE`, `SCIENCE`, `SHOPPING`, `SPORTS`

### Notes

- **Automatic auth means every call runs against the user's live Google Analytics.** There is no sandbox and no dry-run: reads return real production data, and writes take effect on real accounts, properties, data streams, and access bindings. The token carries whatever accounts the connected Google user can already reach, which may include properties belonging to clients or other teams. Resolve and name the exact account and property with `accountSummaries` before acting, show the user which one you resolved, and get explicit confirmation before any write. Treat changes to data retention, access bindings, and property or stream deletion as administrative actions with lasting effect, not routine configuration edits.
- Property IDs are numeric (e.g., `properties/521310447`)
- Account IDs are numeric (e.g., `accounts/123456789`)
- GA4 properties only (Universal Analytics not supported)
- Use `accountSummaries` endpoint to quickly list all accessible properties
- The `filter` parameter on list properties uses format: `parent:accounts/{accountId}`
- Use `updateMask` query parameter to specify which fields to update in PATCH requests
- This API is for property/account management - use the Data API for running reports

### Resources

- [Google Analytics Admin API Overview](https://developers.google.com/analytics/devguides/config/admin/v1)
- [List Accounts](https://developers.google.com/analytics/devguides/config/admin/v1/rest/v1beta/accounts/list)
- [List Properties](https://developers.google.com/analytics/devguides/config/admin/v1/rest/v1beta/properties/list)
- [Create Property](https://developers.google.com/analytics/devguides/config/admin/v1/rest/v1beta/properties/create)
- [Data Streams](https://developers.google.com/analytics/devguides/config/admin/v1/rest/v1beta/properties.dataStreams)
- [Custom Dimensions](https://developers.google.com/analytics/devguides/config/admin/v1/rest/v1beta/properties.customDimensions)
- [Custom Metrics](https://developers.google.com/analytics/devguides/config/admin/v1/rest/v1beta/properties.customMetrics)
- [Conversion Events](https://developers.google.com/analytics/devguides/config/admin/v1/rest/v1beta/properties.conversionEvents)
- [Maton CLI Manual](https://cli.maton.ai/manual)
