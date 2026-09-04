# YouTube Analytics Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `youtube-analytics`
**Base URL proxied:** `youtubeanalytics.googleapis.com`

## API Path Pattern

```
/youtube-analytics/v2/{resource}
```

## Common Endpoints

### Query Reports
```bash
maton api '/youtube-analytics/v2/reports?ids=channel==MINE&startDate=2025-01-01&endDate=2025-01-31&metrics=views,likes,comments'
```

With dimensions and sorting:
```bash
maton api '/youtube-analytics/v2/reports?ids=channel==MINE&startDate=2025-01-01&endDate=2025-03-31&metrics=views,estimatedMinutesWatched&dimensions=day&sort=-views&maxResults=10'
```

Monthly aggregation (endDate must align to 1st of month):
```bash
maton api '/youtube-analytics/v2/reports?ids=channel==MINE&startDate=2024-01-01&endDate=2024-12-01&metrics=views,subscribersGained&dimensions=month'
```

### List Groups
```bash
maton api '/youtube-analytics/v2/groups?mine=true'
```

### Get Groups by ID
```bash
maton api '/youtube-analytics/v2/groups?id={group_id}'
```

### Create Group
```bash
maton api -X POST '/youtube-analytics/v2/groups' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "snippet": {"title": "My Group"},
  "contentDetails": {"itemType": "youtube#video"}
}
EOF
```

### Update Group
```bash
maton api -X PUT '/youtube-analytics/v2/groups' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "id": "{group_id}",
  "snippet": {"title": "Updated Title"},
  "contentDetails": {"itemType": "youtube#video"}
}
EOF
```

### Delete Group
```bash
maton api -X DELETE '/youtube-analytics/v2/groups?id={group_id}'
```

### List Group Items
```bash
maton api '/youtube-analytics/v2/groupItems?groupId={group_id}'
```

### Add Item to Group
```bash
maton api -X POST '/youtube-analytics/v2/groupItems' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "groupId": "{group_id}",
  "resource": {"kind": "youtube#video", "id": "{video_id}"}
}
EOF
```

### Remove Item from Group
```bash
maton api -X DELETE '/youtube-analytics/v2/groupItems?id={group_item_id}'
```

## Report Parameters

**Required:** `ids`, `startDate`, `endDate`, `metrics`

**Optional:** `dimensions`, `filters`, `sort`, `maxResults`, `startIndex`, `currency`

**Common Metrics:** `views`, `likes`, `comments`, `shares`, `estimatedMinutesWatched`, `averageViewDuration`, `subscribersGained`, `subscribersLost`

**Common Dimensions:** `day`, `month`, `country`, `video`, `deviceType`, `operatingSystem`

## Notes

- Dates use `YYYY-MM-DD` format
- `month` dimension requires `endDate` aligned to 1st of month
- `ids=channel==MINE` targets authenticated user's channel
- Groups support up to 500 items of a single type: `youtube#video`, `youtube#playlist`, `youtube#channel`, `youtubePartner#asset`
- Only group title can be updated; use groupItems methods for membership
- Groups pagination uses `pageToken`; reports use `startIndex` + `maxResults`

## Resources

- [YouTube Analytics API Reference](https://developers.google.com/youtube/analytics/reference)
- [Channel Reports](https://developers.google.com/youtube/analytics/channel_reports)
- [Metrics Reference](https://developers.google.com/youtube/analytics/metrics)
