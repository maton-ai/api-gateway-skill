# Dropbox Business

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `dropbox-business`
**Upstream base URL:** `api.dropboxapi.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.dropboxapi.com/2/team/get_info`
- Gateway: `https://api.maton.ai/dropbox-business/2/team/get_info`

**Note:** Dropbox Business API uses POST for almost all endpoints, including read operations. Request bodies should be JSON (use `null` for endpoints with no parameters).

### Team Information API

#### Get Team Info

Retrieves information about the team including license usage and policies.

```bash
maton api -X POST '/dropbox-business/2/team/get_info' -H 'Content-Type: application/json' --input - <<'JSON'
null
JSON
```

**Response:**
```json
{
  "name": "My Company",
  "team_id": "dbtid:AAC...",
  "num_licensed_users": 10,
  "num_provisioned_users": 5,
  "num_used_licenses": 5,
  "policies": {
    "sharing": {...},
    "emm_state": {".tag": "disabled"},
    "office_addin": {".tag": "enabled"}
  }
}
```

#### Get Team Features

Query team feature availability.

```bash
maton api -X POST '/dropbox-business/2/team/features/get_values' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "features": [
    {".tag": "upload_api_rate_limit"},
    {".tag": "has_team_shared_dropbox"},
    {".tag": "has_team_file_events"},
    {".tag": "has_team_selective_sync"}
  ]
}
JSON
```

**Response:**
```json
{
  "values": [
    {".tag": "upload_api_rate_limit", "upload_api_rate_limit": {".tag": "limit", "limit": 1000000000}},
    {".tag": "has_team_shared_dropbox", "has_team_shared_dropbox": {".tag": "has_team_shared_dropbox", "has_team_shared_dropbox": false}},
    {".tag": "has_team_file_events", "has_team_file_events": {".tag": "enabled", "enabled": true}},
    {".tag": "has_team_selective_sync", "has_team_selective_sync": {".tag": "has_team_selective_sync", "has_team_selective_sync": true}}
  ]
}
```

#### Get Authenticated Admin

Get info about the currently authenticated admin.

```bash
maton api -X POST '/dropbox-business/2/team/token/get_authenticated_admin' -H 'Content-Type: application/json' --input - <<'JSON'
null
JSON
```

**Response:**
```json
{
  "admin_profile": {
    "team_member_id": "dbmid:AAA...",
    "account_id": "dbid:AAC...",
    "email": "admin@company.com",
    "email_verified": true,
    "status": {".tag": "active"},
    "name": {"given_name": "Admin", "surname": "User", "display_name": "Admin User"},
    "membership_type": {".tag": "full"},
    "joined_on": "2026-02-15T08:27:35Z"
  }
}
```

#### List Member's File Requests

```bash
maton api -X POST '/dropbox-business/2/file_requests/list_v2' -H 'Dropbox-API-Select-User: dbmid:AAA...' -H 'Content-Type: application/json' --input - <<'JSON'
{}
JSON
```

**Note:** The `Dropbox-API-Select-User` header requires the `team_data.member` scope. Use this to operate on user-level endpoints (files, sharing, etc.) on behalf of team members.

### Team Members API

#### List Members

```bash
maton api -X POST '/dropbox-business/2/team/members/list' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "limit": 100
}
JSON
```

#### List Members (V2) - Recommended

Returns members with roles information (recommended).

```bash
maton api -X POST '/dropbox-business/2/team/members/list_v2' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "limit": 100,
  "include_removed": false
}
JSON
```

**Response:**
```json
{
  "members": [
    {
      "profile": {
        "team_member_id": "dbmid:AAA...",
        "account_id": "dbid:AAC...",
        "email": "user@company.com",
        "email_verified": true,
        "secondary_emails": [],
        "status": {".tag": "active"},
        "name": {
          "given_name": "John",
          "surname": "Doe",
          "familiar_name": "John",
          "display_name": "John Doe",
          "abbreviated_name": "JD"
        },
        "membership_type": {".tag": "full"},
        "joined_on": "2026-01-15T10:00:00Z",
        "groups": ["g:1d31f47b..."],
        "member_folder_id": "13646219987",
        "root_folder_id": "13650024947"
      },
      "roles": [
        {
          "role_id": "pid_dbtmr:...",
          "name": "Team",
          "description": "Team admin role holding all permissions"
        }
      ]
    }
  ],
  "cursor": "AAQ...",
  "has_more": false
}
```

#### Continue Listing Members

```bash
maton api -X POST '/dropbox-business/2/team/members/list/continue' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "cursor": "AAQ..."
}
JSON
```

#### Get Member Info

```bash
maton api -X POST '/dropbox-business/2/team/members/get_info' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "members": [{".tag": "email", "email": "user@company.com"}]
}
JSON
```

#### Get Member Info (V2) - Recommended

Returns member with roles information (recommended).

```bash
maton api -X POST '/dropbox-business/2/team/members/get_info_v2' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "members": [{".tag": "email", "email": "user@company.com"}]
}
JSON
```

**Response:**
```json
{
  "members_info": [
    {
      ".tag": "member_info",
      "profile": {
        "team_member_id": "dbmid:AAA...",
        "email": "user@company.com",
        "secondary_emails": [],
        "status": {".tag": "active"},
        "name": {...},
        "groups": ["g:..."]
      },
      "roles": [
        {"role_id": "...", "name": "Team", "description": "..."}
      ]
    }
  ]
}
```

**Member Selectors:**
- `{".tag": "email", "email": "user@company.com"}`
- `{".tag": "team_member_id", "team_member_id": "dbmid:AAA..."}`
- `{".tag": "external_id", "external_id": "..."}`

#### Add Member

```bash
maton api -X POST '/dropbox-business/2/team/members/add' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "new_members": [
    {
      "member_email": "newuser@company.com",
      "member_given_name": "Jane",
      "member_surname": "Smith",
      "send_welcome_email": true,
      "role": {".tag": "member_only"}
    }
  ]
}
JSON
```

#### Suspend Member

```bash
maton api -X POST '/dropbox-business/2/team/members/suspend' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "user": {".tag": "email", "email": "user@company.com"},
  "wipe_data": false
}
JSON
```

#### Unsuspend Member

```bash
maton api -X POST '/dropbox-business/2/team/members/unsuspend' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "user": {".tag": "email", "email": "user@company.com"}
}
JSON
```

#### Remove Member

```bash
maton api -X POST '/dropbox-business/2/team/members/remove' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "user": {".tag": "email", "email": "user@company.com"},
  "wipe_data": true,
  "transfer_dest_id": {".tag": "email", "email": "admin@company.com"},
  "transfer_admin_id": {".tag": "email", "email": "admin@company.com"},
  "keep_account": false
}
JSON
```

#### Check Remove Job Status

```bash
maton api -X POST '/dropbox-business/2/team/members/remove/job_status/get' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "async_job_id": "dbjid:..."
}
JSON
```

#### Send Welcome Email

Send or resend welcome email to pending members.

```bash
maton api -X POST '/dropbox-business/2/team/members/send_welcome_email' -H 'Content-Type: application/json' --input - <<'JSON'
{".tag": "email", "email": "pending@company.com"}
JSON
```

#### Set Member Profile (V2)

Update member profile information.

```bash
maton api -X POST '/dropbox-business/2/team/members/set_profile_v2' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "user": {".tag": "team_member_id", "team_member_id": "dbmid:AAA..."},
  "new_given_name": "John",
  "new_surname": "Smith",
  "new_external_id": "emp-123"
}
JSON
```

#### Delete Profile Photo (V2)

```bash
maton api -X POST '/dropbox-business/2/team/members/delete_profile_photo_v2' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "user": {".tag": "team_member_id", "team_member_id": "dbmid:AAA..."}
}
JSON
```

#### Set Profile Photo (V2)

```bash
maton api -X POST '/dropbox-business/2/team/members/set_profile_photo_v2' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "user": {".tag": "team_member_id", "team_member_id": "dbmid:AAA..."},
  "photo": {".tag": "base64_data", "base64_data": "<base64-encoded-image>"}
}
JSON
```

#### Set Admin Permissions (V2)

Change a member's admin role.

```bash
maton api -X POST '/dropbox-business/2/team/members/set_admin_permissions_v2' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "user": {".tag": "email", "email": "user@company.com"},
  "new_roles": ["pid_dbtmr:..."]
}
JSON
```

### Secondary Emails API

#### Add Secondary Emails

```bash
maton api -X POST '/dropbox-business/2/team/members/secondary_emails/add' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "new_secondary_emails": [
    {
      "user": {".tag": "email", "email": "user@company.com"},
      "secondary_emails": ["alias@company.com"]
    }
  ]
}
JSON
```

#### Delete Secondary Emails

```bash
maton api -X POST '/dropbox-business/2/team/members/secondary_emails/delete' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "emails_to_delete": [
    {
      "user": {".tag": "email", "email": "user@company.com"},
      "secondary_emails": ["alias@company.com"]
    }
  ]
}
JSON
```

#### Resend Verification Emails

```bash
maton api -X POST '/dropbox-business/2/team/members/secondary_emails/resend_verification_emails' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "emails_to_resend": [
    {
      "user": {".tag": "email", "email": "user@company.com"},
      "secondary_emails": ["alias@company.com"]
    }
  ]
}
JSON
```

### Groups API

#### List Groups

```bash
maton api -X POST '/dropbox-business/2/team/groups/list' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "limit": 100
}
JSON
```

**Response:**
```json
{
  "groups": [
    {
      "group_name": "Engineering",
      "group_id": "g:1d31f47b...",
      "member_count": 5,
      "group_management_type": {".tag": "company_managed"}
    }
  ],
  "cursor": "AAZ...",
  "has_more": false
}
```

#### Get Group Info

```bash
maton api -X POST '/dropbox-business/2/team/groups/get_info' -H 'Content-Type: application/json' --input - <<'JSON'
{
  ".tag": "group_ids",
  "group_ids": ["g:1d31f47b..."]
}
JSON
```

#### Create Group

```bash
maton api -X POST '/dropbox-business/2/team/groups/create' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "group_name": "Marketing Team",
  "group_management_type": {".tag": "company_managed"}
}
JSON
```

#### Add Members to Group

```bash
maton api -X POST '/dropbox-business/2/team/groups/members/add' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "group": {".tag": "group_id", "group_id": "g:1d31f47b..."},
  "members": [
    {
      "user": {".tag": "email", "email": "user@company.com"},
      "access_type": {".tag": "member"}
    }
  ],
  "return_members": true
}
JSON
```

#### Remove Members from Group

```bash
maton api -X POST '/dropbox-business/2/team/groups/members/remove' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "group": {".tag": "group_id", "group_id": "g:1d31f47b..."},
  "users": [{".tag": "email", "email": "user@company.com"}],
  "return_members": true
}
JSON
```

#### List Group Members

```bash
maton api -X POST '/dropbox-business/2/team/groups/members/list' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "group": {".tag": "group_id", "group_id": "g:1d31f47b..."},
  "limit": 100
}
JSON
```

**Response:**
```json
{
  "members": [
    {
      "profile": {
        "team_member_id": "dbmid:AAA...",
        "email": "user@company.com",
        "status": {".tag": "active"},
        "name": {...}
      },
      "access_type": {".tag": "member"}
    }
  ],
  "cursor": "...",
  "has_more": false
}
```

#### Update Group

```bash
maton api -X POST '/dropbox-business/2/team/groups/update' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "group": {".tag": "group_id", "group_id": "g:1d31f47b..."},
  "new_group_name": "Updated Name",
  "new_group_external_id": "ext-123"
}
JSON
```

**Note:** System-managed groups (like "Everyone at...") cannot be updated.

#### Delete Group

```bash
maton api -X POST '/dropbox-business/2/team/groups/delete' -H 'Content-Type: application/json' --input - <<'JSON'
{
  ".tag": "group_id",
  "group_id": "g:1d31f47b..."
}
JSON
```

#### Check Group Job Status

For async group operations.

```bash
maton api -X POST '/dropbox-business/2/team/groups/job_status/get' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "async_job_id": "dbjid:..."
}
JSON
```

### Team Folders API

#### List Team Folders

```bash
maton api -X POST '/dropbox-business/2/team/team_folder/list' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "limit": 100
}
JSON
```

**Response:**
```json
{
  "team_folders": [
    {
      "team_folder_id": "13646676387",
      "name": "Company Documents",
      "status": {".tag": "active"},
      "is_team_shared_dropbox": false,
      "sync_setting": {".tag": "default"}
    }
  ],
  "cursor": "AAb...",
  "has_more": false
}
```

#### Get Team Folder Info

```bash
maton api -X POST '/dropbox-business/2/team/team_folder/get_info' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "team_folder_ids": ["13646676387"]
}
JSON
```

#### Create Team Folder

```bash
maton api -X POST '/dropbox-business/2/team/team_folder/create' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Team Folder",
  "sync_setting": {".tag": "default"}
}
JSON
```

#### Rename Team Folder

```bash
maton api -X POST '/dropbox-business/2/team/team_folder/rename' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "team_folder_id": "13646676387",
  "name": "Renamed Folder"
}
JSON
```

#### Archive Team Folder

```bash
maton api -X POST '/dropbox-business/2/team/team_folder/archive' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "team_folder_id": "13646676387",
  "force_async_off": false
}
JSON
```

#### Permanently Delete Team Folder

> **IRREVERSIBLE.** This permanently destroys the folder and all its contents. The folder must be archived first. Confirm the exact folder ID and name with the user before executing.

```bash
maton api -X POST '/dropbox-business/2/team/team_folder/permanently_delete' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "team_folder_id": "13646676387"
}
JSON
```

#### Activate Team Folder

Activate an archived team folder.

```bash
maton api -X POST '/dropbox-business/2/team/team_folder/activate' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "team_folder_id": "13646676387"
}
JSON
```

#### Update Sync Settings

```bash
maton api -X POST '/dropbox-business/2/team/team_folder/update_sync_settings' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "team_folder_id": "13646676387",
  "sync_setting": {".tag": "default"}
}
JSON
```

**Response:**
```json
{
  "team_folder_id": "13646676387",
  "name": "Team Folder",
  "status": {".tag": "active"},
  "is_team_shared_dropbox": false,
  "sync_setting": {".tag": "default"},
  "content_sync_settings": []
}
```

### Namespaces API

#### List Namespaces

```bash
maton api -X POST '/dropbox-business/2/team/namespaces/list' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "limit": 100
}
JSON
```

**Response:**
```json
{
  "namespaces": [
    {
      "name": "Team Folder",
      "namespace_id": "13646676387",
      "namespace_type": {".tag": "team_folder"}
    },
    {
      "name": "Root",
      "namespace_id": "13646219987",
      "namespace_type": {".tag": "team_member_folder"},
      "team_member_id": "dbmid:AAA..."
    }
  ],
  "cursor": "AAY...",
  "has_more": false
}
```

### Devices API

#### List Members' Devices

```bash
maton api -X POST '/dropbox-business/2/team/devices/list_members_devices' -H 'Content-Type: application/json' --input - <<'JSON'
{}
JSON
```

**Response:**
```json
{
  "devices": [
    {
      "team_member_id": "dbmid:AAA...",
      "web_sessions": [
        {
          "session_id": "dbwsid:...",
          "ip_address": "192.168.1.1",
          "country": "United States",
          "created": "2026-02-15T08:26:33Z",
          "user_agent": "Mozilla/5.0...",
          "os": "Mac OS X",
          "browser": "Chrome"
        }
      ],
      "desktop_clients": [],
      "mobile_clients": []
    }
  ],
  "has_more": false
}
```

#### List Member Devices

```bash
maton api -X POST '/dropbox-business/2/team/devices/list_member_devices' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "team_member_id": "dbmid:AAA..."
}
JSON
```

#### Revoke Device Session

```bash
maton api -X POST '/dropbox-business/2/team/devices/revoke_device_session' -H 'Content-Type: application/json' --input - <<'JSON'
{
  ".tag": "web_session",
  "session_id": "dbwsid:...",
  "team_member_id": "dbmid:AAA..."
}
JSON
```

#### Revoke Device Sessions (Batch)

```bash
maton api -X POST '/dropbox-business/2/team/devices/revoke_device_session_batch' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "revoke_devices": [
    {".tag": "web_session", "session_id": "dbwsid:...", "team_member_id": "dbmid:AAA..."}
  ]
}
JSON
```

### Linked Apps API

#### List Members' Linked Apps

```bash
maton api -X POST '/dropbox-business/2/team/linked_apps/list_members_linked_apps' -H 'Content-Type: application/json' --input - <<'JSON'
{}
JSON
```

**Response:**
```json
{
  "apps": [
    {
      "team_member_id": "dbmid:AAA...",
      "linked_api_apps": [
        {
          "app_id": "...",
          "app_name": "Third Party App",
          "linked": "2026-01-15T10:00:00Z"
        }
      ]
    }
  ],
  "has_more": false
}
```

#### List Team Linked Apps

```bash
maton api -X POST '/dropbox-business/2/team/linked_apps/list_team_linked_apps' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{}
EOF
```

#### Revoke Linked App

```bash
maton api -X POST '/dropbox-business/2/team/linked_apps/revoke_linked_app' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "app_id": "...",
  "team_member_id": "dbmid:AAA..."
}
JSON
```

### Member Space Limits API

#### Get Custom Quotas

```bash
maton api -X POST '/dropbox-business/2/team/member_space_limits/get_custom_quota' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "users": [{".tag": "email", "email": "user@company.com"}]
}
JSON
```

#### Set Custom Quotas

```bash
maton api -X POST '/dropbox-business/2/team/member_space_limits/set_custom_quota' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "users_and_quotas": [
    {
      "user": {".tag": "email", "email": "user@company.com"},
      "quota_gb": 100
    }
  ]
}
JSON
```

#### List Excluded Users

List users excluded from automatic backup.

```bash
maton api -X POST '/dropbox-business/2/team/member_space_limits/excluded_users/list' -H 'Content-Type: application/json' --input - <<'JSON'
{}
JSON
```

### Sharing Allowlist API

#### List Sharing Allowlist

```bash
maton api -X POST '/dropbox-business/2/team/sharing_allowlist/list' -H 'Content-Type: application/json' --input - <<'JSON'
{}
JSON
```

**Response:**
```json
{
  "domains": [],
  "emails": [],
  "cursor": "...",
  "has_more": false
}
```

#### Add to Sharing Allowlist

```bash
maton api -X POST '/dropbox-business/2/team/sharing_allowlist/add' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "domains": ["partner.com"],
  "emails": ["external@client.com"]
}
JSON
```

#### Continue Listing Allowlist

```bash
maton api -X POST '/dropbox-business/2/team/sharing_allowlist/list/continue' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "cursor": "..."
}
JSON
```

### Audit Log API

#### Get Events

```bash
maton api -X POST '/dropbox-business/2/team_log/get_events' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "limit": 100,
  "category": {".tag": "members"}
}
JSON
```

**Response:**
```json
{
  "events": [
    {
      "timestamp": "2026-02-15T08:27:36Z",
      "event_category": {".tag": "members"},
      "actor": {
        ".tag": "admin",
        "admin": {
          "account_id": "dbid:AAC...",
          "display_name": "Admin User",
          "email": "admin@company.com"
        }
      },
      "event_type": {
        ".tag": "member_add_name",
        "description": "Added team member name"
      },
      "details": {...}
    }
  ],
  "cursor": "...",
  "has_more": false
}
```

**Event Categories:**
- `apps` - Third-party app events
- `comments` - Comment events
- `devices` - Device events
- `domains` - Domain events
- `file_operations` - File and folder events
- `file_requests` - File request events
- `groups` - Group events
- `logins` - Login events
- `members` - Member events
- `paper` - Paper events
- `passwords` - Password events
- `reports` - Report events
- `sharing` - Sharing events
- `showcase` - Showcase events
- `sso` - SSO events
- `team_folders` - Team folder events
- `team_policies` - Policy events
- `team_profile` - Team profile events
- `tfa` - Two-factor auth events

#### Continue Getting Events

```bash
maton api -X POST '/dropbox-business/2/team_log/get_events/continue' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "cursor": "..."
}
JSON
```

### Member File Access API

Use the `Dropbox-API-Select-User` header with a team_member_id to access files on behalf of a member.

> **Privacy — this reads another person's files, not the operator's.** `Dropbox-API-Select-User` uses team-admin authority to impersonate a specific member and browse their Dropbox, including private, non-shared content. The member is not notified and has not consented to this particular access.
> - Confirm with the operator **which member** (by name/email, not just an opaque `dbmid:`) and **what specific files or folders** are needed, before sending the header.
> - Access only what the stated task requires. Do not enumerate a member's whole drive to "see what's there", and do not iterate across multiple members without per-member justification.
> - Surface only the information the task needs. Do not dump file listings, contents, or paths from a member's private folders into output beyond what was asked.
> - Do not retain, cache, or copy another member's file contents elsewhere once the task is done.
> - Impersonating a member for anything beyond an explicitly requested administrative task — monitoring, performance review, or investigation the operator has not stated — is out of scope for this skill. If the intent is unclear, ask.

#### List Member's Files

```bash
maton api -X POST '/dropbox-business/2/files/list_folder' -H 'Dropbox-API-Select-User: dbmid:AAA...' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "path": ""
}
JSON
```

#### List Member's Shared Folders

```bash
maton api -X POST '/dropbox-business/2/sharing/list_folders' -H 'Dropbox-API-Select-User: dbmid:AAA...' -H 'Content-Type: application/json' --input - <<'JSON'
{}
JSON
```

### OAuth Scopes

| Scope | Usage |
|-------|-------|
| `team_info.read` | Team info, features |
| `members.read` | List/get members |
| `members.write` | Add/modify members |
| `members.delete` | Remove members |
| `groups.read` | List/get groups |
| `groups.write` | Create/modify groups |
| `sessions.list` | List devices/sessions |
| `sessions.modify` | Revoke sessions |
| `events.read` | Team audit log |
| `team_data.member` | Select-User header |

### Notes

- All endpoints use POST method (even read operations)
- Request bodies must be JSON (use `null` for no-parameter endpoints)
- Many fields use `.tag` format for type indication
- Pagination uses `cursor` and `has_more` fields
- Use V2 endpoints for enhanced responses with roles info
- `Dropbox-API-Select-User` header enables member file access
- System-managed groups cannot be modified
- Reports endpoints (`team/reports/*`) are deprecated

### Resources

- [Dropbox Business API Documentation](https://www.dropbox.com/developers/documentation/http/teams)
- [Team Administration Guide](https://developers.dropbox.com/dbx-team-administration-guide)
- [Team Files Guide](https://developers.dropbox.com/dbx-team-files-guide)
- [Maton CLI Manual](https://cli.maton.ai/manual)
