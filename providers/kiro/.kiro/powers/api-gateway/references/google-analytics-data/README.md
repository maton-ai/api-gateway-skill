# Google Analytics Data Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `google-analytics-data`
**Base URL proxied:** `analyticsdata.googleapis.com`

## API Path Pattern

```
/google-analytics-data/v1beta/{endpoint}
```

## Common Endpoints

### Run Report
```bash
maton api -X POST '/google-analytics-data/v1beta/properties/{propertyId}:runReport' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "dateRanges": [{"startDate": "30daysAgo", "endDate": "today"}],
  "dimensions": [{"name": "city"}],
  "metrics": [{"name": "activeUsers"}]
}
EOF
```

### Run Realtime Report
```bash
maton api -X POST '/google-analytics-data/v1beta/properties/{propertyId}:runRealtimeReport' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "dimensions": [{"name": "country"}],
  "metrics": [{"name": "activeUsers"}]
}
EOF
```

### Batch Run Reports
```bash
maton api -X POST '/google-analytics-data/v1beta/properties/{propertyId}:batchRunReports' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "requests": [
    {
      "dateRanges": [{"startDate": "7daysAgo", "endDate": "today"}],
      "dimensions": [{"name": "country"}],
      "metrics": [{"name": "sessions"}]
    },
    {
      "dateRanges": [{"startDate": "7daysAgo", "endDate": "today"}],
      "dimensions": [{"name": "deviceCategory"}],
      "metrics": [{"name": "sessions"}]
    }
  ]
}
EOF
```

### Get Metadata (available dimensions/metrics)
```bash
maton api '/google-analytics-data/v1beta/properties/{propertyId}/metadata'
```

## Common Report Examples

### Page Views by Page
```json
{
  "dateRanges": [{"startDate": "30daysAgo", "endDate": "today"}],
  "dimensions": [{"name": "pagePath"}],
  "metrics": [{"name": "screenPageViews"}],
  "orderBys": [{"metric": {"metricName": "screenPageViews"}, "desc": true}],
  "limit": 10
}
```

### Users by Country
```json
{
  "dateRanges": [{"startDate": "30daysAgo", "endDate": "today"}],
  "dimensions": [{"name": "country"}],
  "metrics": [{"name": "activeUsers"}, {"name": "sessions"}],
  "orderBys": [{"metric": {"metricName": "activeUsers"}, "desc": true}]
}
```

### Traffic Sources
```json
{
  "dateRanges": [{"startDate": "30daysAgo", "endDate": "today"}],
  "dimensions": [{"name": "sessionSource"}, {"name": "sessionMedium"}],
  "metrics": [{"name": "sessions"}, {"name": "conversions"}]
}
```

### Device Breakdown
```json
{
  "dateRanges": [{"startDate": "7daysAgo", "endDate": "today"}],
  "dimensions": [{"name": "deviceCategory"}],
  "metrics": [{"name": "activeUsers"}, {"name": "sessions"}, {"name": "bounceRate"}]
}
```

### Daily Sessions Trend
```json
{
  "dateRanges": [{"startDate": "30daysAgo", "endDate": "today"}],
  "dimensions": [{"name": "date"}],
  "metrics": [{"name": "sessions"}, {"name": "activeUsers"}],
  "orderBys": [{"dimension": {"dimensionName": "date"}}]
}
```

## Common Dimensions

- `date`, `dateHour`, `dateHourMinute`
- `country`, `city`, `region`
- `deviceCategory`, `browser`, `operatingSystem`
- `pagePath`, `pageTitle`, `landingPage`
- `sessionSource`, `sessionMedium`, `sessionCampaignName`
- `eventName`

## Common Metrics

- `activeUsers`, `newUsers`, `totalUsers`
- `sessions`, `sessionsPerUser`
- `screenPageViews`, `screenPageViewsPerSession`
- `bounceRate`, `averageSessionDuration`
- `conversions`, `eventCount`

## Date Formats

- Relative: `today`, `yesterday`, `7daysAgo`, `30daysAgo`
- Absolute: `2026-01-01`

## Notes

- Authentication is automatic - the router injects the OAuth token
- Property IDs are numeric (e.g., `521310447` from URL `p521310447`)
- GA4 properties only (Universal Analytics not supported)
- Use metadata endpoint to discover available dimensions/metrics
- Results are paginated with `limit` and `offset`
- This API is for running reports only - listing properties requires the Admin API

## Resources

- [API Overview](https://developers.google.com/analytics/devguides/reporting/data/v1)
- [Run Report](https://developers.google.com/analytics/devguides/reporting/data/v1/rest/v1beta/properties/runReport)
- [Run Realtime Report](https://developers.google.com/analytics/devguides/reporting/data/v1/rest/v1beta/properties/runRealtimeReport)
- [Batch Run Reports](https://developers.google.com/analytics/devguides/reporting/data/v1/rest/v1beta/properties/batchRunReports)
- [Get Metadata](https://developers.google.com/analytics/devguides/reporting/data/v1/rest/v1beta/properties/getMetadata)