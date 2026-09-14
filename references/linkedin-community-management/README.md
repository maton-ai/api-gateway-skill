# LinkedIn Community Management

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `linkedin-community-management`
**Upstream base URL:** `api.linkedin.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.linkedin.com/rest/me`
- Gateway: `https://api.maton.ai/linkedin-community-management/rest/me`

**Important:** All requests require `LinkedIn-Version` and `X-Restli-Protocol-Version` headers.

### User Info API

#### Get Current Member

```bash
maton api '/linkedin-community-management/rest/me' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

**Response:**
```json
{
  "localizedLastName": "Smith",
  "localizedFirstName": "John",
  "id": "abc123XYZ",
  "vanityName": "john-smith",
  "localizedHeadline": "Software Engineer at Acme Corp"
}
```

### People Lookup API

#### Get Person by ID

Look up a LinkedIn member's profile by their person ID. The person ID can be obtained from `/rest/me`, `organizationAcls`, post authors, or comment actors.

```bash
maton api '/linkedin-community-management/rest/people/(id:{personId})' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

**Note:** `{personId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "localizedLastName": "Smith",
  "profilePicture": {
    "displayImage": "urn:li:digitalmediaAsset:C5603AQFWsrW4dwGzmg"
  },
  "vanityName": "john-smith",
  "lastName": {
    "localized": {"en_US": "Smith"},
    "preferredLocale": {"country": "US", "language": "en"}
  },
  "firstName": {
    "localized": {"en_US": "John"},
    "preferredLocale": {"country": "US", "language": "en"}
  },
  "localizedHeadline": "Software Engineer at Acme Corp",
  "id": "abc123XYZ",
  "headline": {
    "localized": {"en_US": "Software Engineer at Acme Corp"},
    "preferredLocale": {"country": "US", "language": "en"}
  },
  "localizedFirstName": "John"
}
```

**Available fields:** `id`, `firstName`, `lastName`, `vanityName`, `localizedFirstName`, `localizedLastName`, `localizedHeadline`, `headline`, `profilePicture`

You can request a single field with the `fields` query parameter:

```bash
maton api '/linkedin-community-management/rest/people/(id:{personId})?fields=localizedHeadline' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

**Note:**
- `{personId}` is a placeholder. Replace it with a real value before sending the request.
- The `(id:{personId})` syntax uses Rest.li composite key format — parentheses are required
- Use `curl -g` to prevent shell glob expansion of parentheses
- Non-connected members may return `{"id": "private"}` with limited data
- The person ID comes from URNs like `urn:li:person:{personId}` found in org ACLs, post authors, and comment actors

### Organization API

#### Find Organization by Vanity Name

```bash
maton api '/linkedin-community-management/rest/organizations?q=vanityName&vanityName={vanityName}' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

**Note:** `{vanityName}` is a placeholder. Replace it with a real value before sending the request.

#### Get Organization by ID (Admin Required)

```bash
maton api '/linkedin-community-management/rest/organizations/{organizationId}' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

**Note:** `{organizationId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Organization Follower Count

```bash
maton api '/linkedin-community-management/rest/networkSizes/urn%3Ali%3Aorganization%3A{orgId}?edgeType=COMPANY_FOLLOWED_BY_MEMBER' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

**Response:**
```json
{
  "firstDegreeSize": 33634367
}
```

#### Find Administered Organizations

```bash
maton api '/linkedin-community-management/rest/organizationAcls?q=roleAssignee&role=ADMINISTRATOR&state=APPROVED' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

#### Find Child Organizations (Brands)

```bash
maton api '/linkedin-community-management/rest/organizations?q=parentOrganization&parent=urn%3Ali%3Aorganization%3A{orgId}' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

### Posts API

#### Create Post

```bash
maton api -X POST '/linkedin-community-management/rest/posts' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "author": "urn:li:organization:{orgId}",
  "commentary": "Your post text here",
  "visibility": "PUBLIC",
  "distribution": {
    "feedDistribution": "MAIN_FEED",
    "targetEntities": [],
    "thirdPartyDistributionChannels": []
  },
  "lifecycleState": "PUBLISHED",
  "isReshareDisabledByAuthor": false
}
JSON
```

**Note:** `{orgId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Author can be `urn:li:person:{personId}` for member posts or `urn:li:organization:{orgId}` for organization posts.

Returns `201` with `x-restli-id` header containing the post URN (e.g., `urn:li:share:123456`).

#### Create Post with Media

```bash
maton api -X POST '/linkedin-community-management/rest/posts' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "author": "urn:li:organization:{orgId}",
  "commentary": "Check out this video!",
  "visibility": "PUBLIC",
  "distribution": {
    "feedDistribution": "MAIN_FEED",
    "targetEntities": [],
    "thirdPartyDistributionChannels": []
  },
  "content": {
    "media": {
      "title": "Video title",
      "id": "urn:li:video:{videoId}"
    }
  },
  "lifecycleState": "PUBLISHED",
  "isReshareDisabledByAuthor": false
}
JSON
```

**Note:** `{orgId}` and `{videoId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Article Post

```bash
maton api -X POST '/linkedin-community-management/rest/posts' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "author": "urn:li:organization:{orgId}",
  "commentary": "Great article on AI",
  "visibility": "PUBLIC",
  "distribution": {
    "feedDistribution": "MAIN_FEED",
    "targetEntities": [],
    "thirdPartyDistributionChannels": []
  },
  "content": {
    "article": {
      "source": "https://example.com/article",
      "thumbnail": "urn:li:image:{imageId}",
      "title": "Article Title",
      "description": "Article description"
    }
  },
  "lifecycleState": "PUBLISHED",
  "isReshareDisabledByAuthor": false
}
JSON
```

**Note:** `{orgId}` and `{imageId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Post by URN

```bash
maton api '/linkedin-community-management/rest/posts/{encoded_postUrn}' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

**Note:** `{encoded_postUrn}` is a placeholder. Replace it with a real value before sending the request.

**Note:** URNs must be URL-encoded: `urn:li:share:123` becomes `urn%3Ali%3Ashare%3A123`.

#### Find Posts by Author (Organization)

```bash
maton api '/linkedin-community-management/rest/posts?author=urn%3Ali%3Aorganization%3A{orgId}&q=author&count=10&sortBy=LAST_MODIFIED' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0' -H 'X-RestLi-Method: FINDER'
```

**Query parameters:**

| Field | Description | Required |
|-------|-------------|----------|
| author | Organization or Person URN (URL-encoded) | Yes |
| q | Must be `author` | Yes |
| count | Number of results (max 100, default 10) | No |
| start | Offset for pagination (default 0) | No |
| sortBy | `LAST_MODIFIED` or `CREATED` | No |

#### Update Post

```bash
maton api -X POST '/linkedin-community-management/rest/posts/{encoded_postUrn}' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0' -H 'X-RestLi-Method: PARTIAL_UPDATE' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "patch": {
    "$set": {
      "commentary": "Updated post text"
    }
  }
}
JSON
```

**Note:** `{encoded_postUrn}` is a placeholder. Replace it with a real value before sending the request.

Returns `204` on success. Only `commentary`, `contentCallToActionLabel`, `contentLandingPage`, and `lifecycleState` can be updated.

#### Delete Post

```bash
maton api '/linkedin-community-management/rest/posts/{encoded_postUrn}' -X DELETE -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0' -H 'X-RestLi-Method: DELETE'
```

**Note:** `{encoded_postUrn}` is a placeholder. Replace it with a real value before sending the request.

Returns `204` on success.

#### Reshare Post

```bash
maton api -X POST '/linkedin-community-management/rest/posts' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "author": "urn:li:organization:{orgId}",
  "commentary": "Great insights!",
  "visibility": "PUBLIC",
  "distribution": {
    "feedDistribution": "MAIN_FEED",
    "targetEntities": [],
    "thirdPartyDistributionChannels": []
  },
  "lifecycleState": "PUBLISHED",
  "isReshareDisabledByAuthor": false,
  "reshareContext": {
    "parent": "urn:li:share:{originalPostId}"
  }
}
JSON
```

**Note:** `{orgId}` and `{originalPostId}` are placeholders. Replace each of them with real values before sending the request.

### Comments API

#### Get Comments on Post

```bash
maton api '/linkedin-community-management/rest/socialActions/{encoded_postUrn}/comments' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

**Note:** `{encoded_postUrn}` is a placeholder. Replace it with a real value before sending the request.

#### Get Specific Comment

```bash
maton api '/linkedin-community-management/rest/socialActions/{encoded_postUrn}/comments/{commentId}' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

**Note:** `{encoded_postUrn}` and `{commentId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Comment

```bash
maton api -X POST '/linkedin-community-management/rest/socialActions/{encoded_postUrn}/comments' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "actor": "urn:li:organization:{orgId}",
  "object": "urn:li:activity:{activityId}",
  "message": {
    "text": "Your comment text"
  }
}
JSON
```

**Note:** `{encoded_postUrn}`, `{orgId}` and `{activityId}` are placeholders. Replace each of them with real values before sending the request.

Returns `201` with `x-restli-id` header containing the comment ID.

#### Create Nested Comment (Reply)

```bash
maton api -X POST '/linkedin-community-management/rest/socialActions/{encoded_commentUrn}/comments' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "actor": "urn:li:organization:{orgId}",
  "object": "urn:li:share:{shareId}",
  "message": {
    "text": "Reply to comment"
  },
  "parentComment": "urn:li:comment:(urn:li:activity:{activityId},{commentId})"
}
JSON
```

**Note:** `{encoded_commentUrn}`, `{orgId}`, `{shareId}`, `{activityId}` and `{commentId}` are placeholders. Replace each of them with real values before sending the request.

#### Edit Comment

```bash
maton api -X POST '/linkedin-community-management/rest/socialActions/{encoded_postUrn}/comments/{commentId}?actor=urn%3Ali%3Aorganization%3A{orgId}' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0' -H 'X-RestLi-Method: PARTIAL_UPDATE' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "patch": {
    "message": {
      "$set": {
        "text": "Updated comment text"
      }
    }
  }
}
JSON
```

**Note:** `{encoded_postUrn}` and `{commentId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Comment

```bash
maton api '/linkedin-community-management/rest/socialActions/{encoded_postUrn}/comments/{commentId}?actor=urn%3Ali%3Aorganization%3A{orgId}' -X DELETE -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

**Note:** `{encoded_postUrn}` and `{commentId}` are placeholders. Replace each of them with real values before sending the request.

### Reactions API

#### Create Reaction

```bash
maton api -X POST '/linkedin-community-management/rest/reactions?actor=urn%3Ali%3Aorganization%3A{orgId}' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "root": "urn:li:activity:{activityId}",
  "reactionType": "LIKE"
}
JSON
```

**Note:** `{activityId}` is a placeholder. Replace it with a real value before sending the request.

**Reaction types:** `LIKE`, `PRAISE` (Celebrate), `EMPATHY` (Love), `INTEREST` (Insightful), `APPRECIATION` (Support), `ENTERTAINMENT` (Funny).

#### Get Reactions on Post

```bash
maton api '/linkedin-community-management/rest/reactions/(entity:{encoded_entityUrn})?q=entity' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

**Note:** `{encoded_entityUrn}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Reaction

```bash
maton api '/linkedin-community-management/rest/reactions/(actor:{encoded_actorUrn},entity:{encoded_entityUrn})' -X DELETE -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

**Note:** `{encoded_actorUrn}` and `{encoded_entityUrn}` are placeholders. Replace each of them with real values before sending the request.

Returns `204` on success.

### Statistics API

These endpoints require the authenticated member to be an `ADMINISTRATOR` of the organization.

#### Organization Follower Statistics (Lifetime)

```bash
maton api '/linkedin-community-management/rest/organizationalEntityFollowerStatistics?q=organizationalEntity&organizationalEntity=urn%3Ali%3Aorganization%3A{orgId}' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

Returns follower counts segmented by geo, function, industry, seniority, and staff count range.

#### Organization Follower Statistics (Time-Bound)

```bash
maton api '/linkedin-community-management/rest/organizationalEntityFollowerStatistics?q=organizationalEntity&organizationalEntity=urn%3Ali%3Aorganization%3A{orgId}&timeIntervals.timeGranularityType=DAY&timeIntervals.timeRange.start={startMs}&timeIntervals.timeRange.end={endMs}' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

**Note:** `{startMs}` and `{endMs}` are placeholders. Replace each of them with real values before sending the request.

`timeGranularityType` can be `DAY`, `WEEK`, or `MONTH`. Timestamps are milliseconds since epoch.

#### Organization Page Statistics (Lifetime)

```bash
maton api '/linkedin-community-management/rest/organizationPageStatistics?q=organization&organization=urn%3Ali%3Aorganization%3A{orgId}' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

#### Organization Page Statistics (Time-Bound)

```bash
maton api '/linkedin-community-management/rest/organizationPageStatistics?q=organization&organization=urn%3Ali%3Aorganization%3A{orgId}&timeIntervals.timeGranularityType=DAY&timeIntervals.timeRange.start={startMs}&timeIntervals.timeRange.end={endMs}' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

**Note:** `{startMs}` and `{endMs}` are placeholders. Replace each of them with real values before sending the request.

#### Organization Share Statistics (Lifetime)

```bash
maton api '/linkedin-community-management/rest/organizationalEntityShareStatistics?q=organizationalEntity&organizationalEntity=urn%3Ali%3Aorganization%3A{orgId}' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

**Response:**
```json
{
  "elements": [{
    "totalShareStatistics": {
      "uniqueImpressionsCount": 36430528,
      "shareCount": 0,
      "engagement": 0.029,
      "clickCount": 1999920,
      "likeCount": 0,
      "impressionCount": 67703905,
      "commentCount": 0
    },
    "organizationalEntity": "urn:li:organization:1337"
  }]
}
```

#### Organization Share Statistics (Time-Bound)

```bash
maton api '/linkedin-community-management/rest/organizationalEntityShareStatistics?q=organizationalEntity&organizationalEntity=urn%3Ali%3Aorganization%3A{orgId}&timeIntervals.timeGranularityType=DAY&timeIntervals.timeRange.start={startMs}&timeIntervals.timeRange.end={endMs}' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

**Note:** `{startMs}` and `{endMs}` are placeholders. Replace each of them with real values before sending the request.

#### Share Statistics for Specific Posts

```bash
maton api '/linkedin-community-management/rest/organizationalEntityShareStatistics?q=organizationalEntity&organizationalEntity=urn%3Ali%3Aorganization%3A{orgId}&shares=List(urn%3Ali%3Ashare%3A{shareId1},urn%3Ali%3Ashare%3A{shareId2})' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

### Mentioning an Organization

Use `@[Display Name](urn:li:organization:{orgId})` syntax in `commentary`:

```json
{
  "commentary": "Congrats to @[LinkedIn](urn:li:organization:1337) on the milestone!"
}
```

### Hashtags

Use `#keyword` syntax in `commentary`:

```json
{
  "commentary": "Follow best practices #coding #engineering"
}
```

### Pagination

LinkedIn uses offset-based pagination with `start` and `count` parameters:

```bash
maton api '/linkedin-community-management/rest/posts?author=...&q=author&count=10&start=0' -H 'Linkedin-Version: 202606' -H 'X-Restli-Protocol-Version: 2.0.0'
```

Response includes pagination info:

```json
{
  "paging": {
    "start": 0,
    "count": 10,
    "links": [
      {
        "rel": "next",
        "href": "/rest/posts?q=author&author=...&count=10&start=10"
      }
    ],
    "total": 500
  },
  "elements": [...]
}
```

Use the `links[].href` with `rel: "next"` for the next page, or increment `start` by `count`.

### Notes

- All URNs in URL path segments and query parameters must be URL-encoded (`:` -> `%3A`)
- Organization posts require `w_organization_social` permission and an admin role on the org
- Member posts require `w_member_social` permission
- Reading member posts requires `r_member_social` (restricted permission)
- The `Linkedin-Version` header is required on all requests (format: `YYYYMM`, e.g., `202606`). LinkedIn keeps roughly the last ~12 monthly versions active and returns HTTP 426 `NONEXISTENT_VERSION` for retired or future-dated versions — pin to a recent month and bump periodically
- Post content types: text-only, image (`urn:li:image:{id}`), video (`urn:li:video:{id}`), document (`urn:li:document:{id}`), article
- Statistics endpoints return data only for administered organizations
- Share statistics only cover the past 12 months (rolling window)
- The `MAYBE` (Curious) reaction type is deprecated since version 202307

### Resources

- [LinkedIn Community Management API](https://learn.microsoft.com/en-us/linkedin/marketing/community-management/community-management-overview)
- [Posts API Reference](https://learn.microsoft.com/en-us/linkedin/marketing/community-management/shares/posts-api)
- [Organization Lookup](https://learn.microsoft.com/en-us/linkedin/marketing/community-management/organizations/organization-lookup-api)
- [Maton CLI Manual](https://cli.maton.ai/manual)
