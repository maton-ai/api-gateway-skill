# Google Workspace Admin Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `google-workspace-admin`
**Base URL proxied:** `admin.googleapis.com`

## API Path Pattern

```
/google-workspace-admin/admin/directory/v1/{endpoint}
```

## Common Endpoints

### Users

#### List Users
```bash
maton api '/google-workspace-admin/admin/directory/v1/users?customer=my_customer&maxResults=100'
```

With search query:
```bash
maton api '/google-workspace-admin/admin/directory/v1/users?customer=my_customer&query=email:john*'
```

#### Get User
```bash
maton api '/google-workspace-admin/admin/directory/v1/users/{userKey}'
```

`userKey` can be the user's primary email or unique user ID.

#### Create User
```bash
maton api -X POST '/google-workspace-admin/admin/directory/v1/users' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
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
EOF
```

#### Update User
```bash
maton api -X PUT '/google-workspace-admin/admin/directory/v1/users/{userKey}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": {
    "givenName": "Jane",
    "familyName": "Smith-Johnson"
  },
  "suspended": false
}
EOF
```

#### Patch User (partial update)
```bash
maton api -X PATCH '/google-workspace-admin/admin/directory/v1/users/{userKey}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "suspended": true
}
EOF
```

#### Delete User
```bash
maton api -X DELETE '/google-workspace-admin/admin/directory/v1/users/{userKey}'
```

#### Make User Admin
```bash
maton api -X POST '/google-workspace-admin/admin/directory/v1/users/{userKey}/makeAdmin' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "status": true
}
EOF
```

### Groups

#### List Groups
```bash
maton api '/google-workspace-admin/admin/directory/v1/groups?customer=my_customer'
```

#### Get Group
```bash
maton api '/google-workspace-admin/admin/directory/v1/groups/{groupKey}'
```

#### Create Group
```bash
maton api -X POST '/google-workspace-admin/admin/directory/v1/groups' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "email": "engineering@example.com",
  "name": "Engineering Team",
  "description": "All engineering staff"
}
EOF
```

#### Update Group
```bash
maton api -X PUT '/google-workspace-admin/admin/directory/v1/groups/{groupKey}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Engineering Department",
  "description": "Updated description"
}
EOF
```

#### Delete Group
```bash
maton api -X DELETE '/google-workspace-admin/admin/directory/v1/groups/{groupKey}'
```

### Group Members

#### List Members
```bash
maton api '/google-workspace-admin/admin/directory/v1/groups/{groupKey}/members'
```

#### Add Member
```bash
maton api -X POST '/google-workspace-admin/admin/directory/v1/groups/{groupKey}/members' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "email": "user@example.com",
  "role": "MEMBER"
}
EOF
```

Roles: `OWNER`, `MANAGER`, `MEMBER`

#### Update Member Role
```bash
maton api -X PATCH '/google-workspace-admin/admin/directory/v1/groups/{groupKey}/members/{memberKey}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "role": "MANAGER"
}
EOF
```

#### Remove Member
```bash
maton api -X DELETE '/google-workspace-admin/admin/directory/v1/groups/{groupKey}/members/{memberKey}'
```

### Organizational Units

#### List Org Units
```bash
maton api '/google-workspace-admin/admin/directory/v1/customer/my_customer/orgunits'
```

#### Get Org Unit
```bash
maton api '/google-workspace-admin/admin/directory/v1/customer/my_customer/orgunits/{orgUnitPath}'
```

#### Create Org Unit
```bash
maton api -X POST '/google-workspace-admin/admin/directory/v1/customer/my_customer/orgunits' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Engineering",
  "parentOrgUnitPath": "/",
  "description": "Engineering department"
}
EOF
```

#### Delete Org Unit
```bash
maton api -X DELETE '/google-workspace-admin/admin/directory/v1/customer/my_customer/orgunits/{orgUnitPath}'
```

### Domains

#### List Domains
```bash
maton api '/google-workspace-admin/admin/directory/v1/customer/my_customer/domains'
```

#### Get Domain
```bash
maton api '/google-workspace-admin/admin/directory/v1/customer/my_customer/domains/{domainName}'
```

### Roles

#### List Roles
```bash
maton api '/google-workspace-admin/admin/directory/v1/customer/my_customer/roles'
```

#### List Role Assignments
```bash
maton api '/google-workspace-admin/admin/directory/v1/customer/my_customer/roleassignments'
```

#### Create Role Assignment
```bash
maton api -X POST '/google-workspace-admin/admin/directory/v1/customer/my_customer/roleassignments' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "roleId": "123456789",
  "assignedTo": "user_id",
  "scopeType": "CUSTOMER"
}
EOF
```

## Notes

- Use `my_customer` as the customer ID for your own domain
- User keys can be primary email or unique user ID
- Group keys can be group email or unique group ID
- Org unit paths start with `/` (e.g., `/Engineering/Frontend`)
- Admin privileges are required for most operations
- Password must meet Google's complexity requirements
