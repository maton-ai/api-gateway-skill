# Lemlist Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `lemlist`
**Base URL proxied:** `api.lemlist.com`

## API Path Pattern

```
/lemlist/api/{resource}
```

## Common Endpoints

### Team

#### Get Team
```bash
maton api '/lemlist/api/team'
```

#### Get Team Credits
```bash
maton api '/lemlist/api/team/credits'
```

#### Get Team Senders
```bash
maton api '/lemlist/api/team/senders'
```

### Campaigns

#### List Campaigns
```bash
maton api '/lemlist/api/campaigns'
```

#### Create Campaign
```bash
maton api -X POST '/lemlist/api/campaigns'
```

#### Get Campaign
```bash
maton api '/lemlist/api/campaigns/{campaignId}'
```

#### Update Campaign
```bash
maton api -X PATCH '/lemlist/api/campaigns/{campaignId}'
```

#### Pause Campaign
```bash
maton api -X POST '/lemlist/api/campaigns/{campaignId}/pause'
```

### Campaign Sequences

#### Get Campaign Sequences
```bash
maton api '/lemlist/api/campaigns/{campaignId}/sequences'
```

### Campaign Schedules

#### Get Campaign Schedules
```bash
maton api '/lemlist/api/campaigns/{campaignId}/schedules'
```

### Leads

#### Add Lead to Campaign
```bash
maton api -X POST '/lemlist/api/campaigns/{campaignId}/leads'
```

#### Get Lead by Email
```bash
maton api '/lemlist/api/leads/{email}'
```

#### Update Lead in Campaign
```bash
maton api -X PATCH '/lemlist/api/campaigns/{campaignId}/leads/{email}'
```

#### Delete Lead from Campaign
```bash
maton api -X DELETE '/lemlist/api/campaigns/{campaignId}/leads/{email}'
```

### Activities

#### List Activities
```bash
maton api '/lemlist/api/activities'
```

Query parameters:
- `campaignId` - Filter by campaign
- `type` - Filter by activity type

### Schedules

#### List Schedules
```bash
maton api '/lemlist/api/schedules'
```

#### Create Schedule
```bash
maton api -X POST '/lemlist/api/schedules'
```

#### Get Schedule
```bash
maton api '/lemlist/api/schedules/{scheduleId}'
```

#### Update Schedule
```bash
maton api -X PATCH '/lemlist/api/schedules/{scheduleId}'
```

#### Delete Schedule
```bash
maton api -X DELETE '/lemlist/api/schedules/{scheduleId}'
```

### Companies

#### List Companies
```bash
maton api '/lemlist/api/companies'
```

### Unsubscribes

#### List Unsubscribes
```bash
maton api '/lemlist/api/unsubscribes'
```

#### Add Unsubscribe
```bash
maton api -X POST '/lemlist/api/unsubscribes'
```

### Inbox Labels

#### List Labels
```bash
maton api '/lemlist/api/inbox/labels'
```

## Notes

- Campaign IDs start with `cam_`
- Lead IDs start with `lea_`
- Schedule IDs start with `skd_`
- Campaigns cannot be deleted via API (only paused)
- Lead emails are used as identifiers for lead operations
- Rate limit: 20 requests per 2 seconds per API key

## Resources

- [Lemlist API Documentation](https://developer.lemlist.com/)
