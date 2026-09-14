# LinkedIn

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `linkedin`
**Upstream base URL:** `api.linkedin.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.linkedin.com/rest/me`
- Gateway: `https://api.maton.ai/linkedin/rest/me`

**Important:** All requests require `LinkedIn-Version` header.

### Profile API

#### Get Current User Profile

```bash
maton api '/linkedin/rest/me' -H 'LinkedIn-Version: 202606'
```

**Example:**
```bash
maton api '/linkedin/rest/me' -H 'LinkedIn-Version: 202606'
```

**Response:**
```json
{
  "firstName": {
    "localized": {"en_US": "John"},
    "preferredLocale": {"country": "US", "language": "en"}
  },
  "localizedFirstName": "John",
  "lastName": {
    "localized": {"en_US": "Doe"},
    "preferredLocale": {"country": "US", "language": "en"}
  },
  "localizedLastName": "Doe",
  "id": "yrZCpj2Z12",
  "vanityName": "johndoe",
  "localizedHeadline": "Software Engineer at Example Corp",
  "profilePicture": {
    "displayImage": "urn:li:digitalmediaAsset:C4D00AAAAbBCDEFGhiJ"
  }
}
```

#### Create Article/URL Share

```bash
maton api -X POST '/linkedin/rest/posts' \
  -H 'Content-Type: application/json' \
  -H 'LinkedIn-Version: 202606' \
  --input - <<'EOF'
{
  "author": "urn:li:person:{personId}",
  "lifecycleState": "PUBLISHED",
  "visibility": "PUBLIC",
  "commentary": "Check this out!",
  "distribution": {
    "feedDistribution": "MAIN_FEED"
  },
  "content": {
    "article": {
      "source": "https://example.com",
      "title": "Title",
      "description": "Description"
    }
  }
}
EOF
```

**Note:** `{personId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Text Post

First, initialize the image upload, then upload the image, then create the post.

**Step 1: Initialize Image Upload**
```bash
maton api -X POST '/linkedin/rest/images?action=initializeUpload' -H 'LinkedIn-Version: 202606' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "initializeUploadRequest": {
    "owner": "urn:li:person:{personId}"
  }
}
JSON
```

**Note:** `{personId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "value": {
    "uploadUrlExpiresAt": 1770541529250,
    "uploadUrl": "https://www.linkedin.com/dms-uploads/...",
    "image": "urn:li:image:D4D10AQH4GJAjaFCkHQ"
  }
}
```

**Step 2: Upload Image Binary**
```bash
PUT {uploadUrl from step 1}
Content-Type: image/png

{binary image data}
```

**Step 3: Create Image Post**
```bash
maton api -X POST '/linkedin/rest/posts' -H 'LinkedIn-Version: 202606' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "author": "urn:li:person:{personId}",
  "lifecycleState": "PUBLISHED",
  "visibility": "PUBLIC",
  "commentary": "Check out this image!",
  "distribution": {
    "feedDistribution": "MAIN_FEED"
  },
  "content": {
    "media": {
      "id": "urn:li:image:D4D10AQH4GJAjaFCkHQ",
      "title": "Image Title"
    }
  }
}
JSON
```

**Note:** `{personId}` is a placeholder. Replace it with a real value before sending the request.

#### Ad Library - Search Ads

```bash
maton api '/linkedin/rest/adLibrary?q=criteria&keyword={keyword}' -H 'LinkedIn-Version: 202606'
```

**Note:** `{keyword}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `keyword` (string): Search ad content (multiple keywords use AND logic)
- `advertiser` (string): Search by advertiser name
- `countries` (array): Filter by ISO 3166-1 alpha-2 country codes
- `dateRange` (object): Filter by served dates
- `start` (integer): Pagination offset
- `count` (integer): Results per page (max 25)

**Example - Search ads by keyword:**
```bash
maton api '/linkedin/rest/adLibrary?q=criteria&keyword=linkedin' -H 'LinkedIn-Version: 202606'
```

**Example - Search ads by advertiser:**
```bash
maton api '/linkedin/rest/adLibrary?q=criteria&advertiser=microsoft' -H 'LinkedIn-Version: 202606'
```

**Response:**
```json
{
  "paging": {
    "start": 0,
    "count": 10,
    "total": 11619543,
    "links": [...]
  },
  "elements": [
    {
      "adUrl": "https://www.linkedin.com/ad-library/detail/...",
      "details": {
        "advertiser": {...},
        "adType": "TEXT_AD",
        "targeting": {...},
        "statistics": {
          "firstImpressionDate": 1704067200000,
          "latestImpressionDate": 1706745600000,
          "impressionsFrom": 1000,
          "impressionsTo": 5000
        }
      },
      "isRestricted": false
    }
  ]
}
```

#### Job Library - Search Jobs

```bash
maton api '/linkedin/rest/jobLibrary?q=criteria&keyword={keyword}' -H 'LinkedIn-Version: 202606'
```

**Note:** `{keyword}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Job Library requires version `202606`.

**Query parameters:**
- `keyword` (string): Search job content
- `organization` (string): Filter by company name
- `countries` (array): Filter by country codes
- `dateRange` (object): Filter by posting dates
- `start` (integer): Pagination offset
- `count` (integer): Results per page (max 24)

**Example:**
```bash
maton api '/linkedin/rest/jobLibrary?q=criteria&keyword=software&organization=google' -H 'LinkedIn-Version: 202606'
```

**Response:**
- `jobPostingUrl`: Link to job listing
- `jobDetails`: Title, location, description, salary, benefits
- `statistics`: Impression data

### Marketing API

#### List Ad Accounts

```bash
maton api '/linkedin/rest/adAccounts?q=search' -H 'LinkedIn-Version: 202606'
```

Returns all ad accounts accessible by the authenticated user.

**Response:**
```json
{
  "paging": {
    "start": 0,
    "count": 10,
    "links": []
  },
  "elements": [
    {
      "id": 123456789,
      "name": "My Ad Account",
      "status": "ACTIVE",
      "type": "BUSINESS",
      "currency": "USD",
      "reference": "urn:li:organization:12345"
    }
  ]
}
```

#### Get Ad Account

```bash
maton api '/linkedin/rest/adAccounts/{adAccountId}'
```

**Note:** `{adAccountId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Ad Account

```bash
maton api -X POST '/linkedin/rest/adAccounts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Ad Account",
  "currency": "USD",
  "reference": "urn:li:organization:{orgId}",
  "type": "BUSINESS"
}
JSON
```

**Note:** `{orgId}` is a placeholder. Replace it with a real value before sending the request.

#### Update Ad Account

```bash
maton api -X POST '/linkedin/rest/adAccounts/{adAccountId}' -H 'X-RestLi-Method: PARTIAL_UPDATE' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "patch": {
    "$set": {
      "name": "Updated Account Name"
    }
  }
}
JSON
```

**Note:** `{adAccountId}` is a placeholder. Replace it with a real value before sending the request.

#### List Campaign Groups

Campaign groups are nested under ad accounts:

```bash
maton api '/linkedin/rest/adAccounts/{adAccountId}/adCampaignGroups' -H 'LinkedIn-Version: 202606'
```

**Note:** `{adAccountId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Campaign Group

```bash
maton api -X POST '/linkedin/rest/adAccounts/{adAccountId}/adCampaignGroups' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Q1 2026 Campaigns",
  "status": "DRAFT",
  "runSchedule": {
    "start": 1704067200000,
    "end": 1711929600000
  },
  "totalBudget": {
    "amount": "10000",
    "currencyCode": "USD"
  }
}
JSON
```

**Note:** `{adAccountId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Campaign Group

```bash
maton api '/linkedin/rest/adAccounts/{adAccountId}/adCampaignGroups/{campaignGroupId}'
```

**Note:** `{adAccountId}` and `{campaignGroupId}` are placeholders. Replace each of them with real values before sending the request.

#### Update Campaign Group

```bash
maton api -X POST '/linkedin/rest/adAccounts/{adAccountId}/adCampaignGroups/{campaignGroupId}' -H 'X-RestLi-Method: PARTIAL_UPDATE' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "patch": {
    "$set": {
      "status": "ACTIVE"
    }
  }
}
JSON
```

**Note:** `{adAccountId}` and `{campaignGroupId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Campaign Group

> **Destructive operation.** Deleting a campaign group may be irreversible and will remove all associated data. Confirm the campaign group ID and that no active campaigns depend on it before proceeding.

```bash
maton api '/linkedin/rest/adAccounts/{adAccountId}/adCampaignGroups/{campaignGroupId}' -X DELETE
```

**Note:** `{adAccountId}` and `{campaignGroupId}` are placeholders. Replace each of them with real values before sending the request.

#### List Campaigns

Campaigns are also nested under ad accounts:

```bash
maton api '/linkedin/rest/adAccounts/{adAccountId}/adCampaigns' -H 'LinkedIn-Version: 202606'
```

**Note:** `{adAccountId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Campaign

```bash
maton api -X POST '/linkedin/rest/adAccounts/{adAccountId}/adCampaigns' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "campaignGroup": "urn:li:sponsoredCampaignGroup:123456",
  "name": "Brand Awareness Campaign",
  "status": "DRAFT",
  "type": "SPONSORED_UPDATES",
  "objectiveType": "BRAND_AWARENESS",
  "dailyBudget": {
    "amount": "100",
    "currencyCode": "USD"
  },
  "costType": "CPM",
  "unitCost": {
    "amount": "5",
    "currencyCode": "USD"
  },
  "locale": {
    "country": "US",
    "language": "en"
  }
}
JSON
```

**Note:** `{adAccountId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Campaign

```bash
maton api '/linkedin/rest/adAccounts/{adAccountId}/adCampaigns/{campaignId}'
```

**Note:** `{adAccountId}` and `{campaignId}` are placeholders. Replace each of them with real values before sending the request.

#### Update Campaign

```bash
maton api -X POST '/linkedin/rest/adAccounts/{adAccountId}/adCampaigns/{campaignId}' -H 'X-RestLi-Method: PARTIAL_UPDATE' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "patch": {
    "$set": {
      "status": "ACTIVE"
    }
  }
}
JSON
```

**Note:** `{adAccountId}` and `{campaignId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Campaign

> **Destructive operation.** Deleting a campaign is irreversible and will stop all ad delivery. Confirm the campaign ID and its current status with the user before proceeding.

```bash
maton api '/linkedin/rest/adAccounts/{adAccountId}/adCampaigns/{campaignId}' -X DELETE
```

**Note:** `{adAccountId}` and `{campaignId}` are placeholders. Replace each of them with real values before sending the request.

#### List Organization ACLs

Get organizations the authenticated user has access to:

```bash
maton api '/linkedin/rest/organizationAcls?q=roleAssignee' -H 'LinkedIn-Version: 202606'
```

**Response:**
```json
{
  "paging": {
    "start": 0,
    "count": 10,
    "total": 2
  },
  "elements": [
    {
      "role": "ADMINISTRATOR",
      "organization": "urn:li:organization:12345",
      "state": "APPROVED"
    }
  ]
}
```

#### Get Organization

```bash
maton api '/linkedin/rest/organizations/{organizationId}' -H 'LinkedIn-Version: 202606'
```

**Note:** `{organizationId}` is a placeholder. Replace it with a real value before sending the request.

#### Lookup Organization by Vanity Name

```bash
maton api '/linkedin/rest/organizations?q=vanityName&vanityName={vanityName}' -H 'LinkedIn-Version: 202606'
```

**Note:** `{vanityName}` is a placeholder. Replace it with a real value before sending the request.

**Example:**
```bash
maton api '/linkedin/rest/organizations?q=vanityName&vanityName=microsoft' -H 'LinkedIn-Version: 202606'
```

**Response:**
```json
{
  "elements": [
    {
      "vanityName": "microsoft",
      "localizedName": "Microsoft",
      "website": {
        "localized": {"en_US": "https://news.microsoft.com/"}
      }
    }
  ]
}
```

#### Get Organization Share Statistics

```bash
maton api '/linkedin/rest/organizationalEntityShareStatistics?q=organizationalEntity&organizationalEntity={orgUrn}' -H 'LinkedIn-Version: 202606'
```

**Note:** `{orgUrn}` is a placeholder. Replace it with a real value before sending the request.

**Example:**
```bash
maton api '/linkedin/rest/organizationalEntityShareStatistics?q=organizationalEntity&organizationalEntity=urn:li:organization:12345' -H 'LinkedIn-Version: 202606'
```

#### Get Organization Posts

```bash
maton api '/linkedin/rest/posts?q=author&author={orgUrn}' -H 'LinkedIn-Version: 202606'
```

**Note:** `{orgUrn}` is a placeholder. Replace it with a real value before sending the request.

**Example:**
```bash
maton api '/linkedin/rest/posts?q=author&author=urn:li:organization:12345' -H 'LinkedIn-Version: 202606'
```

#### Initialize Image Upload

```bash
maton api -X POST '/linkedin/rest/images?action=initializeUpload' -H 'LinkedIn-Version: 202606' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "initializeUploadRequest": {
    "owner": "urn:li:person:{personId}"
  }
}
JSON
```

**Response:**
```json
{
  "value": {
    "uploadUrlExpiresAt": 1770541529250,
    "uploadUrl": "https://www.linkedin.com/dms-uploads/...",
    "image": "urn:li:image:D4D10AQH4GJAjaFCkHQ"
  }
}
```

Use the `uploadUrl` to PUT your image binary, then use the `image` URN in your post.

#### Create a Video Post

Video uploads are a 4-step process: initialize, upload binary, finalize, then create the post.

> **CRITICAL — URL Encoding:** The upload URL returned by the initialize step contains URL-encoded characters (e.g., `%253D`) that get corrupted when passed through shell variables or `curl`. You **MUST** use Python `urllib` for the entire flow — parse the JSON response and use the URL directly in Python without passing it through the shell. This is the only reliable approach.

**Complete working example:**

```bash
python3 <<'EOF'
import json, os, subprocess, urllib.request

HEADERS = ['-H', 'LinkedIn-Version: 202606', '-H', 'X-Restli-Protocol-Version: 2.0.0']

def api(path, method=None, body=None):
    cmd = ['maton', 'api', path] + HEADERS
    if method:
        cmd += ['-X', method]
    if body is not None:
        cmd += ['-H', 'Content-Type: application/json', '--input', '-']
    p = subprocess.run(cmd, input=json.dumps(body) if body is not None else None,
                       capture_output=True, text=True, check=True)
    return json.loads(p.stdout)

# Step 0: get the person ID
owner = f"urn:li:person:{api('/linkedin/rest/me')['id']}"

# Step 1: initialize the upload through the gateway
file_path = '/path/to/video.mp4'
init = api('/linkedin/rest/videos?action=initializeUpload', 'POST', {
    'initializeUploadRequest': {
        'owner': owner,
        'fileSizeBytes': os.path.getsize(file_path),
        'uploadCaptions': False,
        'uploadThumbnail': False,
    }
})
upload_url = init['value']['uploadInstructions'][0]['uploadUrl']
video_urn = init['value']['video']

# Step 2: upload the bytes DIRECTLY to LinkedIn's pre-signed URL (not through the gateway).
# It needs no Authorization header. Use the URL exactly as returned — never via a shell string.
with open(file_path, 'rb') as f:
    upload_req = urllib.request.Request(upload_url, data=f.read(), method='PUT')
upload_req.add_header('Content-Type', 'application/octet-stream')
etag = urllib.request.urlopen(upload_req).headers['etag']

# Step 3: finalize the upload
api('/linkedin/rest/videos?action=finalizeUpload', 'POST', {
    'finalizeUploadRequest': {'video': video_urn, 'uploadToken': '', 'uploadedPartIds': [etag]}
})

# Step 4: create the post
print(api('/linkedin/rest/posts', 'POST', {
    'author': owner,
    'lifecycleState': 'PUBLISHED',
    'visibility': 'PUBLIC',
    'commentary': 'Check out this video!',
    'distribution': {'feedDistribution': 'MAIN_FEED'},
    'content': {'media': {'id': video_urn}},
}))
EOF
```

**How it works:**
- Steps 1, 3, 4 go through the gateway (`api.maton.ai/linkedin/...`) — Maton injects your OAuth token automatically.
- Step 2 goes **directly** to LinkedIn's pre-signed upload URL (`www.linkedin.com/dms-uploads/...`) — no auth header needed, no gateway.
- The `etag` from the upload response is required for the finalize step.
- For large videos (>4MB), LinkedIn returns multiple `uploadInstructions` — upload each chunk to its respective URL and collect all etags.

**Video specifications:**
- Length: 3 seconds to 30 minutes
- File size: 75KB to 500MB
- Format: MP4

##### Initialize Document Upload

```bash
maton api -X POST '/linkedin/rest/documents?action=initializeUpload' -H 'LinkedIn-Version: 202606' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "initializeUploadRequest": {
    "owner": "urn:li:person:{personId}"
  }
}
JSON
```

**Note:** `{personId}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "value": {
    "uploadUrlExpiresAt": 1770541530896,
    "uploadUrl": "https://www.linkedin.com/dms-uploads/...",
    "document": "urn:li:document:D4D10AQHr-e30QZCAjQ"
  }
}
```

### Ad Targeting API

#### Get Targeting Facets

```bash
maton api '/linkedin/rest/adTargetingFacets' \
  -H 'LinkedIn-Version: 202606'
```

Returns 31 targeting facets (skills, industries, titles, locations, etc.)

Returns all available targeting facets for ad campaigns (31 facets including employers, degrees, skills, locations, industries, etc.).

**Response:**

```json
{
  "elements": [
    {
      "facetName": "skills",
      "adTargetingFacetUrn": "urn:li:adTargetingFacet:skills",
      "entityTypes": ["SKILL"],
      "availableEntityFinders": ["AD_TARGETING_FACET", "TYPEAHEAD"]
    },
    {
      "facetName": "industries",
      "adTargetingFacetUrn": "urn:li:adTargetingFacet:industries"
    }
  ]
}
```

Available targeting facets include:
- `skills` - Member skills
- `industries` - Industry categories
- `titles` - Job titles
- `seniorities` - Seniority levels
- `degrees` - Educational degrees
- `schools` - Educational institutions
- `employers` / `employersPast` - Current/past employers
- `locations` / `geoLocations` - Geographic targeting
- `companySize` - Company size ranges
- `genders` - Gender targeting
- `ageRanges` - Age range targeting

### Little Text Format (Commentary Field)

The `commentary` field in posts uses LinkedIn's "Little Text Format". **Reserved characters must be escaped with a backslash or the post content will be truncated.**

### Reserved Characters (Must Escape)

| Character | Escape As |
|-----------|-----------|
| `\` | `\\` |
| `\|` | `\\|` |
| `{` | `\{` |
| `}` | `\}` |
| `@` | `\@` |
| `[` | `\[` |
| `]` | `\]` |
| `(` | `\(` |
| `)` | `\)` |
| `<` | `\<` |
| `>` | `\>` |
| `#` | `\#` |
| `*` | `\*` |
| `_` | `\_` |
| `~` | `\~` |

### Example

```json
{
  "commentary": "Hello\\! Check out these bullet points:\\n\\n\\* Point 1\\n\\* Point 2\\n\\* More info \\(details inside\\)"
}
```

### Mentions and Hashtags

- **Mention a person:** `@[Display Name](urn:li:person:123)`
- **Mention an organization:** `@[Company Name](urn:li:organization:456)`
- **Hashtag:** `{hashtag|\\#|MyTag}` or simply `#hashtag` for single words

### Campaign Status Values

| Status | Description |
|--------|-------------|
| `DRAFT` | Campaign is in draft mode |
| `ACTIVE` | Campaign is running |
| `PAUSED` | Campaign is paused |
| `ARCHIVED` | Campaign is archived |
| `COMPLETED` | Campaign has ended |
| `CANCELED` | Campaign was canceled |

### Campaign Objective Types

| Objective | Description |
|-----------|-------------|
| `BRAND_AWARENESS` | Increase brand visibility |
| `WEBSITE_VISITS` | Drive traffic to website |
| `ENGAGEMENT` | Increase post engagement |
| `VIDEO_VIEWS` | Maximize video views |
| `LEAD_GENERATION` | Collect leads via Lead Gen Forms |
| `WEBSITE_CONVERSIONS` | Drive website conversions |
| `JOB_APPLICANTS` | Attract job applications |

### OAuth Scopes

| Scope | Description |
|-------|-------------|
| `openid` | OpenID Connect authentication |
| `profile` | Read basic profile |
| `email` | Read email address |
| `w_member_social` | Create, modify, and delete posts |
| `r_organization_social` | Read organization posts and statistics |
| `w_organization_social` | Create and manage organization posts |
| `r_ads` | Read advertising account data |
| `rw_ads` | Create and manage ad campaigns, campaign groups, and accounts |

Note: Available scopes depend on your LinkedIn OAuth connection. Verify granted scopes at your [Maton connection settings](https://maton.ai/settings) before attempting advertising operations.

### Notes

- Include `LinkedIn-Version: 202606` header for all REST API calls
- Author URN format: `urn:li:person:{personId}`
- Get person ID from `/rest/me` endpoint
- **Commentary uses Little Text Format** — escape reserved characters (`|{}@[]()<>#\*_~`) with backslash or content will be truncated
- Image uploads are 3-step: initialize, upload binary, create post
- Video uploads are 4-step: initialize, upload binary, finalize, create post
- **Media upload URLs point to `www.linkedin.com` (not `api.linkedin.com`).** They are pre-signed — do NOT send through the gateway, do NOT add an Authorization header. MUST use Python `urllib` (not shell `curl`) due to URL encoding issues.
- Rate limits: 150 requests/day per member, 100K/day per app

### Visibility Options

- `PUBLIC` - Viewable by anyone
- `CONNECTIONS` - 1st-degree connections only

### Share Media Categories

- `NONE` - Text only
- `ARTICLE` - URL share
- `IMAGE` - Image post
- `VIDEO` - Video post

### Resources

- [LinkedIn API Overview](https://learn.microsoft.com/en-us/linkedin/)
- [Share on LinkedIn](https://learn.microsoft.com/en-us/linkedin/consumer/integrations/self-serve/share-on-linkedin)
- [Profile API](https://learn.microsoft.com/en-us/linkedin/shared/integrations/people/profile-api)
- [LinkedIn Marketing API](https://learn.microsoft.com/en-us/linkedin/marketing/)
- [Ad Accounts](https://learn.microsoft.com/en-us/linkedin/marketing/integrations/ads/account-structure/create-and-manage-accounts)
- [Campaigns](https://learn.microsoft.com/en-us/linkedin/marketing/integrations/ads/account-structure/create-and-manage-campaigns)
- [Maton CLI Manual](https://cli.maton.ai/manual)
