# Lemlist

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `lemlist`
**Upstream base URL:** `api.lemlist.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.lemlist.com/api/team`
- Gateway: `https://api.maton.ai/lemlist/api/team`

### Team API

#### Get Team

```bash
maton api '/lemlist/api/team'
```

Returns team information including user IDs and settings.

#### Get Team Credits

```bash
maton api '/lemlist/api/team/credits'
```

Returns remaining credits balance.

#### Get Team Senders

```bash
maton api '/lemlist/api/team/senders'
```

Returns all team members and their associated campaigns.

### Campaigns API

#### List Campaigns

```bash
maton api '/lemlist/api/campaigns'
```

#### Create Campaign

```bash
maton api -X POST '/lemlist/api/campaigns' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "My Campaign"
}
JSON
```

Creates a new campaign with an empty sequence and default schedule automatically added.

#### Get Campaign

```bash
maton api '/lemlist/api/campaigns/{campaignId}'
```

**Note:** `{campaignId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Campaign

```bash
maton api -X PATCH '/lemlist/api/campaigns/{campaignId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Campaign Name"
}
JSON
```

**Note:** `{campaignId}` is a placeholder. Replace it with a real value before sending the request.

#### Pause Campaign

```bash
maton api -X POST '/lemlist/api/campaigns/{campaignId}/pause'
```

**Note:** `{campaignId}` is a placeholder. Replace it with a real value before sending the request.

Pauses a running campaign.

### Campaign Sequences API

#### Get Campaign Sequences

```bash
maton api '/lemlist/api/campaigns/{campaignId}/sequences'
```

**Note:** `{campaignId}` is a placeholder. Replace it with a real value before sending the request.

Returns all sequences and steps for a campaign.

### Campaign Schedules API

#### Get Campaign Schedules

```bash
maton api '/lemlist/api/campaigns/{campaignId}/schedules'
```

**Note:** `{campaignId}` is a placeholder. Replace it with a real value before sending the request.

Returns all schedules associated with a campaign.

### Leads API

#### Add Lead to Campaign

```bash
maton api -X POST '/lemlist/api/campaigns/{campaignId}/leads' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "email": "lead@example.com",
  "firstName": "John",
  "lastName": "Doe",
  "companyName": "Acme Inc"
}
JSON
```

**Note:** `{campaignId}` is a placeholder. Replace it with a real value before sending the request.

Creates a new lead and adds it to the campaign. If the lead already exists, it will be inserted into the campaign.

#### Get Lead by Email

```bash
maton api '/lemlist/api/leads/{email}'
```

**Note:** `{email}` is a placeholder. Replace it with a real value before sending the request.

#### Update Lead in Campaign

```bash
maton api -X PATCH '/lemlist/api/campaigns/{campaignId}/leads/{email}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "firstName": "Jane",
  "lastName": "Smith"
}
JSON
```

**Note:** `{campaignId}` and `{email}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Lead from Campaign

```bash
maton api '/lemlist/api/campaigns/{campaignId}/leads/{email}' -X DELETE
```

**Note:** `{campaignId}` and `{email}` are placeholders. Replace each of them with real values before sending the request.

### Activities API

#### List Activities

```bash
maton api '/lemlist/api/activities'
```

**Query parameters:**
- `campaignId` - Filter by campaign
- `type` - Filter by activity type (emailsSent, emailsOpened, emailsClicked, etc.)

Returns the history of campaign activities (last 100 activities).

### Schedules API

#### List Schedules

```bash
maton api '/lemlist/api/schedules'
```

Returns all schedules with pagination.

Response:
```json
{
  "schedules": [...],
  "pagination": {
    "totalRecords": 10,
    "currentPage": 1,
    "nextPage": 2,
    "totalPage": 2
  }
}
```

#### Create Schedule

```bash
maton api -X POST '/lemlist/api/schedules' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Business Hours",
  "timezone": "America/New_York",
  "start": "09:00",
  "end": "17:00",
  "weekdays": [1, 2, 3, 4, 5]
}
JSON
```

Weekdays: 0 = Sunday, 1 = Monday, ..., 6 = Saturday

#### Get Schedule

```bash
maton api '/lemlist/api/schedules/{scheduleId}'
```

**Note:** `{scheduleId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Schedule

```bash
maton api -X PATCH '/lemlist/api/schedules/{scheduleId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated Schedule",
  "start": "08:00",
  "end": "18:00"
}
JSON
```

**Note:** `{scheduleId}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Schedule

```bash
maton api '/lemlist/api/schedules/{scheduleId}' -X DELETE
```

**Note:** `{scheduleId}` is a placeholder. Replace it with a real value before sending the request.

### Companies API

#### List Companies

```bash
maton api '/lemlist/api/companies'
```

Returns companies with pagination.

Response:
```json
{
  "data": [...],
  "total": 100
}
```

### Unsubscribes API

#### List Unsubscribes

```bash
maton api '/lemlist/api/unsubscribes'
```

Returns all unsubscribed emails and domains.

#### Add Unsubscribe

```bash
maton api -X POST '/lemlist/api/unsubscribes' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "email": "unsubscribe@example.com"
}
JSON
```

Can also add domains by using a domain value.

### Inbox Labels API

#### List Labels

```bash
maton api '/lemlist/api/inbox/labels'
```

Returns all labels available to the team.

### Notes

- Campaign IDs start with `cam_`
- Lead IDs start with `lea_`
- Schedule IDs start with `skd_`
- Campaigns cannot be deleted via API (only paused)
- Lead emails are used as identifiers for lead operations
- Rate limit: 20 requests per 2 seconds per API key

### Resources

- [Lemlist API Documentation](https://developer.lemlist.com/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
