# Dropbox Business Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `dropbox-business`
**Base URL proxied:** `api.dropboxapi.com`

## API Path Pattern

```
/dropbox-business/2/{endpoint}
```

**Note:** Dropbox Business API uses POST for almost all endpoints, including read operations. Request bodies should be JSON (use `null` for endpoints with no parameters).

## Team Information

### Get Team Info
```bash
maton api -X POST '/dropbox-business/2/team/get_info' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
null
EOF
```

### Get Team Features
```bash
maton api -X POST '/dropbox-business/2/team/features/get_values' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "features": [
    {".tag": "upload_api_rate_limit"},
    {".tag": "has_team_shared_dropbox"},
    {".tag": "has_team_file_events"},
    {".tag": "has_team_selective_sync"}
  ]
}
EOF
```

### Get Authenticated Admin
```bash
maton api -X POST '/dropbox-business/2/team/token/get_authenticated_admin' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
null
EOF
```

## Team Members

### List Members
```bash
maton api -X POST '/dropbox-business/2/team/members/list' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"limit": 100}
EOF
```

### List Members (V2) - Recommended
```bash
maton api -X POST '/dropbox-business/2/team/members/list_v2' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"limit": 100, "include_removed": false}
EOF
```

### Continue Listing Members
```bash
maton api -X POST '/dropbox-business/2/team/members/list/continue' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"cursor": "..."}
EOF
```

### Get Member Info
```bash
maton api -X POST '/dropbox-business/2/team/members/get_info' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"members": [{".tag": "email", "email": "user@company.com"}]}
EOF
```

### Get Member Info (V2) - Recommended
```bash
maton api -X POST '/dropbox-business/2/team/members/get_info_v2' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"members": [{".tag": "email", "email": "user@company.com"}]}
EOF
```

### Add Member
```bash
maton api -X POST '/dropbox-business/2/team/members/add' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "new_members": [{
    "member_email": "user@company.com",
    "member_given_name": "John",
    "member_surname": "Doe",
    "send_welcome_email": true
  }]
}
EOF
```

### Suspend Member
```bash
maton api -X POST '/dropbox-business/2/team/members/suspend' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"user": {".tag": "email", "email": "user@company.com"}, "wipe_data": false}
EOF
```

### Unsuspend Member
```bash
maton api -X POST '/dropbox-business/2/team/members/unsuspend' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"user": {".tag": "email", "email": "user@company.com"}}
EOF
```

### Remove Member
```bash
maton api -X POST '/dropbox-business/2/team/members/remove' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "user": {".tag": "email", "email": "user@company.com"},
  "wipe_data": true,
  "transfer_dest_id": {".tag": "email", "email": "admin@company.com"},
  "transfer_admin_id": {".tag": "email", "email": "admin@company.com"},
  "keep_account": false
}
EOF
```

### Send Welcome Email
```bash
maton api -X POST '/dropbox-business/2/team/members/send_welcome_email' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{".tag": "email", "email": "pending@company.com"}
EOF
```

### Set Member Profile (V2)
```bash
maton api -X POST '/dropbox-business/2/team/members/set_profile_v2' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "user": {".tag": "team_member_id", "team_member_id": "dbmid:AAA..."},
  "new_given_name": "John",
  "new_surname": "Smith"
}
EOF
```

### Set Admin Permissions (V2)
```bash
maton api -X POST '/dropbox-business/2/team/members/set_admin_permissions_v2' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "user": {".tag": "email", "email": "user@company.com"},
  "new_roles": ["pid_dbtmr:..."]
}
EOF
```

### Delete Profile Photo (V2)
```bash
maton api -X POST '/dropbox-business/2/team/members/delete_profile_photo_v2' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"user": {".tag": "team_member_id", "team_member_id": "dbmid:AAA..."}}
EOF
```

## Secondary Emails

### Add Secondary Emails
```bash
maton api -X POST '/dropbox-business/2/team/members/secondary_emails/add' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "new_secondary_emails": [{
    "user": {".tag": "email", "email": "user@company.com"},
    "secondary_emails": ["alias@company.com"]
  }]
}
EOF
```

### Delete Secondary Emails
```bash
maton api -X POST '/dropbox-business/2/team/members/secondary_emails/delete' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "emails_to_delete": [{
    "user": {".tag": "email", "email": "user@company.com"},
    "secondary_emails": ["alias@company.com"]
  }]
}
EOF
```

### Resend Verification Emails
```bash
maton api -X POST '/dropbox-business/2/team/members/secondary_emails/resend_verification_emails' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "emails_to_resend": [{
    "user": {".tag": "email", "email": "user@company.com"},
    "secondary_emails": ["alias@company.com"]
  }]
}
EOF
```

## Groups

### List Groups
```bash
maton api -X POST '/dropbox-business/2/team/groups/list' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"limit": 100}
EOF
```

### Get Group Info
```bash
maton api -X POST '/dropbox-business/2/team/groups/get_info' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{".tag": "group_ids", "group_ids": ["g:1d31f47b..."]}
EOF
```

### List Group Members
```bash
maton api -X POST '/dropbox-business/2/team/groups/members/list' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "group": {".tag": "group_id", "group_id": "g:1d31f47b..."},
  "limit": 100
}
EOF
```

### Create Group
```bash
maton api -X POST '/dropbox-business/2/team/groups/create' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "group_name": "Team Name",
  "group_management_type": {".tag": "company_managed"}
}
EOF
```

### Update Group
```bash
maton api -X POST '/dropbox-business/2/team/groups/update' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "group": {".tag": "group_id", "group_id": "g:1d31f47b..."},
  "new_group_name": "New Name"
}
EOF
```

### Add Members to Group
```bash
maton api -X POST '/dropbox-business/2/team/groups/members/add' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "group": {".tag": "group_id", "group_id": "g:1d31f47b..."},
  "members": [{"user": {".tag": "email", "email": "user@company.com"}, "access_type": {".tag": "member"}}],
  "return_members": true
}
EOF
```

### Remove Members from Group
```bash
maton api -X POST '/dropbox-business/2/team/groups/members/remove' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "group": {".tag": "group_id", "group_id": "g:1d31f47b..."},
  "users": [{".tag": "email", "email": "user@company.com"}],
  "return_members": true
}
EOF
```

### Delete Group
```bash
maton api -X POST '/dropbox-business/2/team/groups/delete' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{".tag": "group_id", "group_id": "g:1d31f47b..."}
EOF
```

### Check Group Job Status
```bash
maton api -X POST '/dropbox-business/2/team/groups/job_status/get' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"async_job_id": "dbjid:..."}
EOF
```

## Team Folders

### List Team Folders
```bash
maton api -X POST '/dropbox-business/2/team/team_folder/list' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"limit": 100}
EOF
```

### Get Team Folder Info
```bash
maton api -X POST '/dropbox-business/2/team/team_folder/get_info' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"team_folder_ids": ["13646676387"]}
EOF
```

### Create Team Folder
```bash
maton api -X POST '/dropbox-business/2/team/team_folder/create' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"name": "Folder Name", "sync_setting": {".tag": "default"}}
EOF
```

### Rename Team Folder
```bash
maton api -X POST '/dropbox-business/2/team/team_folder/rename' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"team_folder_id": "13646676387", "name": "New Name"}
EOF
```

### Archive Team Folder
```bash
maton api -X POST '/dropbox-business/2/team/team_folder/archive' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"team_folder_id": "13646676387", "force_async_off": false}
EOF
```

### Activate Team Folder
```bash
maton api -X POST '/dropbox-business/2/team/team_folder/activate' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"team_folder_id": "13646676387"}
EOF
```

### Update Sync Settings
```bash
maton api -X POST '/dropbox-business/2/team/team_folder/update_sync_settings' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"team_folder_id": "13646676387", "sync_setting": {".tag": "default"}}
EOF
```

### Permanently Delete Team Folder

> **IRREVERSIBLE.** This permanently destroys the folder and all its contents. The folder must be archived first. Confirm the exact folder ID and name with the user before executing.

```bash
maton api -X POST '/dropbox-business/2/team/team_folder/permanently_delete' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"team_folder_id": "{team_folder_id}"}
EOF
```

## Namespaces

### List Namespaces
```bash
maton api -X POST '/dropbox-business/2/team/namespaces/list' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"limit": 100}
EOF
```

## Devices

### List Members' Devices
```bash
maton api -X POST '/dropbox-business/2/team/devices/list_members_devices' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{}
EOF
```

### List Member Devices
```bash
maton api -X POST '/dropbox-business/2/team/devices/list_member_devices' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"team_member_id": "dbmid:AAA..."}
EOF
```

### Revoke Device Session
```bash
maton api -X POST '/dropbox-business/2/team/devices/revoke_device_session' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{".tag": "web_session", "session_id": "dbwsid:...", "team_member_id": "dbmid:AAA..."}
EOF
```

### Revoke Device Sessions (Batch)
```bash
maton api -X POST '/dropbox-business/2/team/devices/revoke_device_session_batch' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "revoke_devices": [
    {".tag": "web_session", "session_id": "dbwsid:...", "team_member_id": "dbmid:AAA..."}
  ]
}
EOF
```

## Linked Apps

### List Members' Linked Apps
```bash
maton api -X POST '/dropbox-business/2/team/linked_apps/list_members_linked_apps' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{}
EOF
```

### List Team Linked Apps
```bash
maton api -X POST '/dropbox-business/2/team/linked_apps/list_team_linked_apps' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{}
EOF
```

### Revoke Linked App
```bash
maton api -X POST '/dropbox-business/2/team/linked_apps/revoke_linked_app' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"app_id": "...", "team_member_id": "dbmid:AAA..."}
EOF
```

## Member Space Limits

### Get Custom Quotas
```bash
maton api -X POST '/dropbox-business/2/team/member_space_limits/get_custom_quota' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"users": [{".tag": "email", "email": "user@company.com"}]}
EOF
```

### Set Custom Quotas
```bash
maton api -X POST '/dropbox-business/2/team/member_space_limits/set_custom_quota' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "users_and_quotas": [{
    "user": {".tag": "email", "email": "user@company.com"},
    "quota_gb": 100
  }]
}
EOF
```

### List Excluded Users
```bash
maton api -X POST '/dropbox-business/2/team/member_space_limits/excluded_users/list' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{}
EOF
```

## Sharing Allowlist

### List Sharing Allowlist
```bash
maton api -X POST '/dropbox-business/2/team/sharing_allowlist/list' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{}
EOF
```

### Add to Sharing Allowlist
```bash
maton api -X POST '/dropbox-business/2/team/sharing_allowlist/add' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"domains": ["partner.com"], "emails": ["external@client.com"]}
EOF
```

## Audit Log (Team Log)

### Get Events
```bash
maton api -X POST '/dropbox-business/2/team_log/get_events' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"limit": 100, "category": {".tag": "members"}}
EOF
```

### Continue Getting Events
```bash
maton api -X POST '/dropbox-business/2/team_log/get_events/continue' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{"cursor": "..."}
EOF
```

## Member File Access

Use the `Dropbox-API-Select-User` header with a team_member_id to access files on behalf of a member.

> **Privacy — this reads another person's files, not the operator's.** `Dropbox-API-Select-User` uses team-admin authority to impersonate a specific member and browse their Dropbox, including private, non-shared content. The member is not notified and has not consented to this particular access.
> - Confirm with the operator **which member** (by name/email, not just an opaque `dbmid:`) and **what specific files or folders** are needed, before sending the header.
> - Access only what the stated task requires. Do not enumerate a member's whole drive to "see what's there", and do not iterate across multiple members without per-member justification.
> - Surface only the information the task needs. Do not dump file listings, contents, or paths from a member's private folders into output beyond what was asked.
> - Do not retain, cache, or copy another member's file contents elsewhere once the task is done.
> - Impersonating a member for anything beyond an explicitly requested administrative task — monitoring, performance review, or investigation the operator has not stated — is out of scope for this skill. If the intent is unclear, ask.

### List Member's Files
```bash
maton api -X POST '/dropbox-business/2/files/list_folder' \
  -H 'Content-Type: application/json' \
  -H 'Dropbox-API-Select-User: dbmid:AAA...' \
  --input - <<'EOF'
{"path": ""}
EOF
```

### List Member's Shared Folders
```bash
maton api -X POST '/dropbox-business/2/sharing/list_folders' \
  -H 'Content-Type: application/json' \
  -H 'Dropbox-API-Select-User: dbmid:AAA...' \
  --input - <<'EOF'
{}
EOF
```

## Notes

- All endpoints use POST method (even read operations)
- Request bodies must be JSON (use `null` for no-parameter endpoints)
- Many fields use `.tag` format for type indication
- Pagination uses `cursor` and `has_more` fields
- Use V2 endpoints for enhanced responses with roles info
- `Dropbox-API-Select-User` header enables member file access
- System-managed groups cannot be modified
- Reports endpoints (`team/reports/*`) are deprecated

## OAuth Scopes

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

## Resources

- [Dropbox Business API Documentation](https://www.dropbox.com/developers/documentation/http/teams)
- [Team Administration Guide](https://developers.dropbox.com/dbx-team-administration-guide)
- [Team Files Guide](https://developers.dropbox.com/dbx-team-files-guide)
