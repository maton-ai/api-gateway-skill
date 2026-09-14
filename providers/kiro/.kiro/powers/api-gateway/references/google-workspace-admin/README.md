# Google Workspace Admin

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

> **⚠ Tenant-wide identity and access administration.** This is not an ordinary app integration. The connection carries Google Workspace *super-admin* authority over the whole organization: creating and deleting users, resetting passwords, suspending accounts, changing group membership, and assigning admin roles. Those are account-takeover and privilege-escalation primitives — a single call can hand someone administrative control of the tenant or lock a real employee out of their work account and mail.
>
> - **Confirm the human, not the identifier.** Resolve the user first and show their full name and primary email before any change. `{userKey}` accepts an email, an alias, or an opaque ID, so a near-miss silently targets the wrong employee.
> - **Role assignment and group membership are privilege changes.** Adding someone to an admin role or a privileged group grants standing access to everyone's data. Never do it as a convenience step, never infer it from a request like "give them access", and state exactly what the role grants before asking for approval.
> - **Deletion and suspension are disruptive and, for deletion, effectively irreversible** — Google's recovery window is short and does not restore everything. Prefer suspension over deletion, and require the user to name the account explicitly.
> - **Password resets and 2SV changes are credential operations.** Never generate or set a password and never disable two-step verification unless the user asked for that specific account; deliver any secret to the user directly and never echo it into shared output.
> - **Never act across users in bulk.** No looping over a list to change settings, no org-unit-wide edits, and no "apply to everyone" — each affected account needs its own approval.
> - **Reads are sensitive too.** Listing users, groups, org units, and audit logs exposes the organization's staff directory and activity. Retrieve the narrowest scope the task needs rather than enumerating the tenant.

**App name:** `google-workspace-admin`
**Upstream base URL:** `admin.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://admin.googleapis.com/admin/directory/v1/users`
- Gateway: `https://api.maton.ai/google-workspace-admin/admin/directory/v1/users`

### Users API

#### List Users

```bash
maton api '/google-workspace-admin/admin/directory/v1/users?customer=my_customer&maxResults=100'
```

With search query:
```bash
maton api '/google-workspace-admin/admin/directory/v1/users?customer=my_customer&query=email:john*'
```

**Query parameters:**
- `customer` - Customer ID or `my_customer` for your domain (required)
- `domain` - Filter by specific domain
- `maxResults` - Maximum results per page (1-500, default 100)
- `orderBy` - Sort by `email`, `familyName`, or `givenName`
- `query` - Search query (e.g., `email:john*`, `name:John*`)
- `pageToken` - Token for pagination

**Example:**

**Response:**

```json
{
  "kind": "admin#directory#users",
  "users": [
    {
      "id": "123456789",
      "primaryEmail": "john@example.com",
      "name": {
        "givenName": "John",
        "familyName": "Doe",
        "fullName": "John Doe"
      },
      "isAdmin": false,
      "isDelegatedAdmin": false,
      "suspended": false,
      "creationTime": "2024-01-15T10:30:00.000Z",
      "lastLoginTime": "2025-02-01T08:00:00.000Z",
      "orgUnitPath": "/Sales"
    }
  ],
  "nextPageToken": "..."
}
```

#### Get User

```bash
maton api '/google-workspace-admin/admin/directory/v1/users/{userKey}'
```

**Note:** `{userKey}` is a placeholder. Replace it with a real value before sending the request.

`userKey` can be the user's primary email or unique user ID.

#### Create User

```bash
maton api -X POST '/google-workspace-admin/admin/directory/v1/users' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "primaryEmail": "newuser@example.com",
  "name": {
    "givenName": "Jane",
    "familyName": "Smith"
  },
  "password": "temporaryPassword123!",
  "changePasswordAtNextLogin": true,
  "orgUnitPath": "/Engineering"
}
JSON
```

#### Update User

```bash
maton api -X PUT '/google-workspace-admin/admin/directory/v1/users/{userKey}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": {
    "givenName": "Jane",
    "familyName": "Smith-Johnson"
  },
  "suspended": false,
  "orgUnitPath": "/Sales"
}
JSON
```

**Note:** `{userKey}` is a placeholder. Replace it with a real value before sending the request.

#### Patch User (partial update)

```bash
maton api -X PATCH '/google-workspace-admin/admin/directory/v1/users/{userKey}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "suspended": true
}
JSON
```

**Note:** `{userKey}` is a placeholder. Replace it with a real value before sending the request.

#### Delete User

```bash
maton api '/google-workspace-admin/admin/directory/v1/users/{userKey}' -X DELETE
```

**Note:** `{userKey}` is a placeholder. Replace it with a real value before sending the request.

#### Make User Admin

```bash
maton api -X POST '/google-workspace-admin/admin/directory/v1/users/{userKey}/makeAdmin' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "status": true
}
JSON
```

**Note:** `{userKey}` is a placeholder. Replace it with a real value before sending the request.

### Groups API

#### List Groups

```bash
maton api '/google-workspace-admin/admin/directory/v1/groups?customer=my_customer'
```

**Query parameters:**
- `customer` - Customer ID or `my_customer` (required)
- `domain` - Filter by domain
- `maxResults` - Maximum results (1-200)
- `userKey` - List groups for a specific user

#### Get Group

```bash
maton api '/google-workspace-admin/admin/directory/v1/groups/{groupKey}'
```

**Note:** `{groupKey}` is a placeholder. Replace it with a real value before sending the request.

`groupKey` can be the group's email or unique ID.

#### Create Group

```bash
maton api -X POST '/google-workspace-admin/admin/directory/v1/groups' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "email": "engineering@example.com",
  "name": "Engineering Team",
  "description": "All engineering staff"
}
JSON
```

#### Update Group

```bash
maton api -X PUT '/google-workspace-admin/admin/directory/v1/groups/{groupKey}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Engineering Department",
  "description": "Updated description"
}
JSON
```

**Note:** `{groupKey}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Group

```bash
maton api '/google-workspace-admin/admin/directory/v1/groups/{groupKey}' -X DELETE
```

**Note:** `{groupKey}` is a placeholder. Replace it with a real value before sending the request.

### Group Members API

#### List Members

```bash
maton api '/google-workspace-admin/admin/directory/v1/groups/{groupKey}/members'
```

**Note:** `{groupKey}` is a placeholder. Replace it with a real value before sending the request.

#### Add Member

```bash
maton api -X POST '/google-workspace-admin/admin/directory/v1/groups/{groupKey}/members' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "email": "user@example.com",
  "role": "MEMBER"
}
JSON
```

**Note:** `{groupKey}` is a placeholder. Replace it with a real value before sending the request.

Roles: `OWNER`, `MANAGER`, `MEMBER`

#### Update Member Role

```bash
maton api -X PATCH '/google-workspace-admin/admin/directory/v1/groups/{groupKey}/members/{memberKey}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "role": "MANAGER"
}
JSON
```

**Note:** `{groupKey}` and `{memberKey}` are placeholders. Replace each of them with real values before sending the request.

#### Remove Member

```bash
maton api '/google-workspace-admin/admin/directory/v1/groups/{groupKey}/members/{memberKey}' -X DELETE
```

**Note:** `{groupKey}` and `{memberKey}` are placeholders. Replace each of them with real values before sending the request.

### Organizational Units API

#### List Org Units

```bash
maton api '/google-workspace-admin/admin/directory/v1/customer/my_customer/orgunits'
```

**Query parameters:**
- `type` - `all` (default) or `children`
- `orgUnitPath` - Parent org unit path

#### Get Org Unit

```bash
maton api '/google-workspace-admin/admin/directory/v1/customer/my_customer/orgunits/{orgUnitPath}'
```

**Note:** `{orgUnitPath}` is a placeholder. Replace it with a real value before sending the request.

#### Create Org Unit

```bash
maton api -X POST '/google-workspace-admin/admin/directory/v1/customer/my_customer/orgunits' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Engineering",
  "parentOrgUnitPath": "/",
  "description": "Engineering department"
}
JSON
```

#### Update Org Unit

```bash
maton api -X PUT '/google-workspace-admin/admin/directory/v1/customer/my_customer/orgunits/{orgUnitPath}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "description": "Updated description"
}
JSON
```

**Note:** `{orgUnitPath}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Org Unit

```bash
maton api '/google-workspace-admin/admin/directory/v1/customer/my_customer/orgunits/{orgUnitPath}' -X DELETE
```

**Note:** `{orgUnitPath}` is a placeholder. Replace it with a real value before sending the request.

### Domains API

#### List Domains

```bash
maton api '/google-workspace-admin/admin/directory/v1/customer/my_customer/domains'
```

#### Get Domain

```bash
maton api '/google-workspace-admin/admin/directory/v1/customer/my_customer/domains/{domainName}'
```

**Note:** `{domainName}` is a placeholder. Replace it with a real value before sending the request.

### Roles API

#### List Roles

```bash
maton api '/google-workspace-admin/admin/directory/v1/customer/my_customer/roles'
```

#### List Role Assignments

```bash
maton api '/google-workspace-admin/admin/directory/v1/customer/my_customer/roleassignments'
```

**Query parameters:**
- `userKey` - Filter by user
- `roleId` - Filter by role

#### Create Role Assignment

```bash
maton api -X POST '/google-workspace-admin/admin/directory/v1/customer/my_customer/roleassignments' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "roleId": "123456789",
  "assignedTo": "user_id",
  "scopeType": "CUSTOMER"
}
JSON
```

### Notes

- Use `my_customer` as the customer ID for your own domain
- User keys can be primary email or unique user ID
- Group keys can be group email or unique group ID
- Org unit paths start with `/` (e.g., `/Engineering/Frontend`)
- Admin privileges are required for most operations
- Password must meet Google's complexity requirements

### Resources

- [Google Workspace Admin API Overview](https://developers.google.com/workspace/admin/reference-overview)
- [Maton CLI Manual](https://cli.maton.ai/manual)
