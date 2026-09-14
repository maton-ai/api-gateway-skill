# Buffer

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `buffer`
**Upstream base URL:** `api.buffer.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.buffer.com/`
- Gateway: `https://api.maton.ai/buffer/`

**Important:** Buffer is GraphQL-only so the path is always exactly `/buffer/` with a trailing slash.

### Account API

#### Get Account

```bash
maton api -X POST '/buffer/' -H 'Content-Type: application/json' --input - <<'JSON'
{"query": "query { account { id email name avatar timezone organizations { id name } } }"}
JSON
```

### Channels API

#### List Channels

```bash
maton api -X POST '/buffer/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "query GetChannels($organizationId: OrganizationId!) { channels(organizationId: $organizationId) { id name service displayName avatar isDisconnected } }",
  "variables": {"organizationId": "{organizationId}"}
}
JSON
```

**Note:** `{organizationId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Channel

```bash
maton api -X POST '/buffer/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "query GetChannel($channelId: ChannelId!) { channel(channelId: $channelId) { id name service postingSchedule { days times } } }",
  "variables": {"channelId": "{channelId}"}
}
JSON
```

**Note:** `{channelId}` is a placeholder. Replace it with a real value before sending the request.

### Posts API

#### List Posts

```bash
maton api -X POST '/buffer/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "query GetPosts($channelId: ChannelId!, $status: PostStatus, $first: Int) { posts(channelId: $channelId, status: $status, first: $first) { edges { node { id text status dueAt } } pageInfo { hasNextPage endCursor } } }",
  "variables": {"channelId": "{channelId}", "status": "scheduled", "first": 20}
}
JSON
```

**Note:** `{channelId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Post

```bash
maton api -X POST '/buffer/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "query GetPost($postId: PostId!) { post(id: $postId) { id text status dueAt channel { id name service } } }",
  "variables": {"postId": "{postId}"}
}
JSON
```

**Note:** `{postId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Post

```bash
maton api -X POST '/buffer/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "mutation CreatePost($input: CreatePostInput!) { createPost(input: $input) { ... on Post { id text status dueAt } ... on InvalidInputError { message } } }",
  "variables": {
    "input": {
      "channelId": "{channelId}",
      "text": "Hello from Buffer",
      "schedulingType": "draft",
      "mode": "queue"
    }
  }
}
JSON
```

**Note:** `{channelId}` is a placeholder. Replace it with a real value before sending the request.

**Input:**
- `channelId` (required): Target channel
- `text`: Post content
- `schedulingType` (required): "scheduled", "draft", "now"
- `dueAt`: ISO 8601 datetime (required when `schedulingType` is "scheduled")
- `mode` (required): "queue" or "share"
- `metadata`: Platform-specific fields (see [Platform Metadata](#platform-metadata))

### Ideas API

#### Create Idea

```bash
maton api -X POST '/buffer/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "mutation CreateIdea($input: CreateIdeaInput!) { createIdea(input: $input) { ... on Idea { id title text } ... on InvalidInputError { message } } }",
  "variables": {
    "input": {
      "organizationId": "{organizationId}",
      "content": {"title": "Campaign concept", "text": "Draft copy for launch week"}
    }
  }
}
JSON
```

**Note:** `{organizationId}` is a placeholder. Replace it with a real value before sending the request.

### Response Format

All Buffer GraphQL responses return HTTP 200 with a top-level `data` object keyed by the operation field:

```json
{
  "data": {
    "[operationField]": { }
  }
}
```

Request-level failures (invalid syntax, unknown field, auth problems) come back in a top-level `errors` array, which may accompany a partial `data`:

```json
{
  "data": null,
  "errors": [
    {
      "message": "Cannot query field \"unknownField\" on type \"Account\".",
      "locations": [{"line": 1, "column": 19}]
    }
  ]
}
```

Mutations return union types, so validation failures arrive inside `data` rather than `errors`. Always check for the `InvalidInputError` branch:

```json
{
  "data": {
    "createPost": {
      "message": "channelId is required"
    }
  }
}
```

### Pagination

Cursor-based pagination with `first`, `after`, and `pageInfo`. Pass the previous `pageInfo.endCursor` as `after` to fetch the next page:

```bash
maton api -X POST '/buffer/' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "query GetPosts($channelId: ChannelId!, $first: Int, $after: String) { posts(channelId: $channelId, first: $first, after: $after) { edges { node { id text status } } pageInfo { hasNextPage endCursor } } }",
  "variables": {"channelId": "{channelId}", "first": 20, "after": "{endCursor}"}
}
JSON
```

**Note:** `{channelId}` and `{endCursor}` are placeholders. Replace each of them with real values before sending the request.

### Review Requirements

- **Default to draft mode.** Use `schedulingType: "draft"` unless the user explicitly requests a scheduled or immediate release.
- **Confirm channel and content.** Before any mutation, show the target channel name/service, post text, timing choice, and relevant metadata for user review.
- **Use read checks first.** Retrieve the account, channel, and existing post details before changing Buffer content.
- **Small scoped changes only.** Handle one channel/post set at a time unless the user confirms a broader batch.

### Platform Metadata

#### Instagram Metadata
| Field | Type | Description |
|-------|------|-------------|
| `type` | String | post, story, reel |
| `firstComment` | String | Auto-comment after posting |
| `link` | String | Link in bio reference |
| `geolocation` | Geolocation | Location tag |
| `shouldShareToFeed` | Boolean | Share reel to feed |
| `stickerFields` | StickerFields | Story stickers |

#### Facebook Metadata
| Field | Type | Description |
|-------|------|-------------|
| `type` | String | Post type |
| `annotations` | [Annotation] | Tags and mentions |
| `linkAttachment` | LinkAttachment | Link preview (url, title, description) |
| `firstComment` | String | Auto-comment |
| `title` | String | Post title |

#### LinkedIn Metadata
| Field | Type | Description |
|-------|------|-------------|
| `annotations` | [Annotation] | Tags and mentions |
| `linkAttachment` | LinkAttachment | Link preview |
| `firstComment` | String | Auto-comment |

#### Twitter Metadata
| Field | Type | Description |
|-------|------|-------------|
| `retweet` | RetweetInput | Quote retweet settings |
| `thread` | [ThreadItem] | Thread tweets [{text}] |

#### Pinterest Metadata
| Field | Type | Description |
|-------|------|-------------|
| `title` | String | Pin title |
| `url` | String | Destination URL |
| `boardServiceId` | String | Target board ID |

#### YouTube Metadata
| Field | Type | Description |
|-------|------|-------------|
| `title` | String | Video title |
| `privacy` | String | public, unlisted, private |
| `categoryId` | String | YouTube category ID |
| `license` | String | Video license |
| `notifySubscribers` | Boolean | Send notifications |
| `embeddable` | Boolean | Allow embedding |
| `madeForKids` | Boolean | Kids content flag |

#### TikTok Metadata
| Field | Type | Description |
|-------|------|-------------|
| `title` | String | Video title |

#### Google Business Metadata
| Field | Type | Description |
|-------|------|-------------|
| `type` | String | Post type |
| `title` | String | Post title |
| `detailsOffer` | OfferDetails | Offer details |
| `detailsEvent` | EventDetails | Event details |
| `detailsWhatsNew` | WhatsNewDetails | Update details |

#### Mastodon Metadata
| Field | Type | Description |
|-------|------|-------------|
| `thread` | [ThreadItem] | Thread toots |
| `spoilerText` | String | Content warning |

#### Threads Metadata
| Field | Type | Description |
|-------|------|-------------|
| `type` | String | Post type |
| `thread` | [ThreadItem] | Thread posts |
| `linkAttachment` | LinkAttachment | Link preview |
| `topic` | String | Topic tag |
| `locationId` | String | Location ID |
| `locationName` | String | Location name |

#### Bluesky Metadata
| Field | Type | Description |
|-------|------|-------------|
| `thread` | [ThreadItem] | Thread skeets |
| `linkAttachment` | LinkAttachment | Link card |

### Key Types

#### Account Fields
| Field | Type | Description |
|-------|------|-------------|
| `id` | ID | Account identifier |
| `email` | String | Primary email |
| `backupEmail` | String | Backup email address |
| `name` | String | Display name |
| `avatar` | String | Avatar URL |
| `timezone` | String | User timezone |
| `createdAt` | DateTime | Account creation date |
| `organizations` | [Organization] | Organizations the user belongs to |
| `preferences` | Preferences | User preferences (timeFormat, startOfWeek) |
| `connectedApps` | [ConnectedApp] | Third-party app connections |

#### Organization Fields
| Field | Type | Description |
|-------|------|-------------|
| `id` | ID | Organization identifier |
| `name` | String | Organization name |
| `ownerEmail` | String | Owner's email |
| `channelCount` | Int | Number of connected channels |
| `channels` | [Channel] | Connected social channels |
| `members` | [Member] | Team members |
| `limits` | Limits | Plan limits and usage |

#### Channel Fields
| Field | Type | Description |
|-------|------|-------------|
| `id` | ID | Channel identifier |
| `name` | String | Channel name |
| `displayName` | String | Display name |
| `service` | String | Platform (twitter, instagram, etc.) |
| `serviceId` | String | Platform-specific ID |
| `type` | String | Channel type |
| `avatar` | String | Channel avatar URL |
| `timezone` | String | Channel timezone |
| `isDisconnected` | Boolean | Connection status |
| `isLocked` | Boolean | Lock status |
| `isNew` | Boolean | Recently added |
| `isQueuePaused` | Boolean | Queue paused status |
| `postingSchedule` | PostingSchedule | Scheduled posting times (days, times) |
| `postingGoal` | PostingGoal | Weekly posting goal (postsPerWeek, progress) |
| `weeklyPostingLimit` | Int | Maximum posts per week |
| `allowedActions` | [String] | Permitted actions |
| `scopes` | [String] | OAuth scopes |
| `products` | [String] | Enabled products |
| `externalLink` | String | Link to profile |
| `linkShortening` | LinkShortening | URL shortening settings |
| `hasActiveMemberDevice` | Boolean | Mobile app connected |
| `showTrendingTopicSuggestions` | Boolean | Show trending suggestions |
| `metadata` | ChannelMetadata | Platform-specific metadata |
| `organizationId` | ID | Parent organization |
| `createdAt` | DateTime | Creation date |
| `updatedAt` | DateTime | Last update |

#### Post Fields
| Field | Type | Description |
|-------|------|-------------|
| `id` | ID | Post identifier |
| `text` | String | Post content |
| `status` | PostStatus | draft, scheduled, sent, failed |
| `schedulingType` | String | scheduled, draft, now |
| `dueAt` | DateTime | Scheduled publish time |
| `sentAt` | DateTime | Actual publish time |
| `createdAt` | DateTime | Creation time |
| `updatedAt` | DateTime | Last update time |
| `author` | Author | Post creator (name, email) |
| `channel` | Channel | Target channel |
| `channelId` | ID | Channel identifier |
| `channelService` | String | Platform name |
| `ideaId` | ID | Linked idea |
| `via` | String | Creation source |
| `isCustomScheduled` | Boolean | Custom scheduled time |
| `externalLink` | String | Link to published post |
| `assets` | [Asset] | Media attachments (id, url, type) |
| `tags` | [Tag] | Content tags |
| `notes` | [Note] | Internal notes |
| `metadata` | PostMetadata | Platform-specific options |
| `notificationStatus` | String | Notification state |
| `error` | PostError | Error details if failed |
| `allowedActions` | [String] | Permitted actions |
| `sharedNow` | Boolean | Posted immediately |
| `shareMode` | String | Sharing mode |

#### Idea Fields
| Field | Type | Description |
|-------|------|-------------|
| `id` | ID | Idea identifier |
| `organizationId` | ID | Parent organization |
| `content` | IdeaContent | Title, text, services |
| `groupId` | ID | Idea group |
| `position` | Int | Order in group |
| `createdAt` | DateTime | Creation date |
| `updatedAt` | DateTime | Last update |

### Supported Services

- Instagram, Facebook, Twitter/X, LinkedIn
- Pinterest, TikTok, YouTube, Google Business
- Mastodon, Threads, Bluesky, StartPage

### Post Status Values

- `draft` - Saved as draft
- `scheduled` - Scheduled for publishing
- `sent` - Published
- `failed` - Failed to publish

### Notes

- All requests are POST with JSON body
- Use `query` field for queries and mutations, include `variables` for parameters
- Scheduling requires ISO 8601 datetime strings

### Resources

- [Buffer API Documentation](https://developers.buffer.com/reference.html)
- [Buffer Getting Started](https://developers.buffer.com/guides/getting-started.html)
- [Maton CLI Manual](https://cli.maton.ai/manual)
