# Dropbox Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `dropbox`
**Base URLs proxied:**
- `api.dropboxapi.com` - Standard RPC endpoints (metadata, search, etc.)
- `content.dropboxapi.com` - Content endpoints (upload, download)

Maton automatically routes to the correct host based on the endpoint path.

## API Path Pattern

```
/dropbox/2/{endpoint}
```

**Important:** All Dropbox API v2 endpoints use HTTP POST. Most endpoints use JSON request bodies, but upload/download endpoints use binary content with parameters in the `Dropbox-API-Arg` header.

## Common Endpoints

### Users

#### Get Current Account
```bash
maton api -X POST '/dropbox/2/users/get_current_account' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
null
EOF
```

#### Get Space Usage
```bash
maton api -X POST '/dropbox/2/users/get_space_usage' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
null
EOF
```

### Files

#### List Folder
```bash
maton api -X POST '/dropbox/2/files/list_folder' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "path": ""
}
EOF
```

Use empty string `""` for root folder.

#### Continue Listing
```bash
maton api -X POST '/dropbox/2/files/list_folder/continue' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "cursor": "..."
}
EOF
```

#### Get Metadata
```bash
maton api -X POST '/dropbox/2/files/get_metadata' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "path": "/document.pdf"
}
EOF
```

#### Create Folder
```bash
maton api -X POST '/dropbox/2/files/create_folder_v2' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "path": "/New Folder",
  "autorename": false
}
EOF
```

#### Copy
```bash
maton api -X POST '/dropbox/2/files/copy_v2' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "from_path": "/source/file.pdf",
  "to_path": "/destination/file.pdf"
}
EOF
```

#### Move
```bash
maton api -X POST '/dropbox/2/files/move_v2' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "from_path": "/old/file.pdf",
  "to_path": "/new/file.pdf"
}
EOF
```

#### Delete
```bash
maton api -X POST '/dropbox/2/files/delete_v2' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "path": "/file-to-delete.pdf"
}
EOF
```

#### Get Temporary Link
```bash
maton api -X POST '/dropbox/2/files/get_temporary_link' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "path": "/document.pdf"
}
EOF
```

### Upload (Content Endpoints)

Content endpoints use `Content-Type: application/octet-stream` with parameters in the `Dropbox-API-Arg` header.

#### Upload File (up to 150 MB)
```bash
maton api -X POST '/dropbox/2/files/upload' \
  -H 'Content-Type: application/octet-stream' \
  -H 'Dropbox-API-Arg: {"path": "/test.txt", "mode": "add", "autorename": true}' \
  --input '{file_path}'  # <file contents>
```

#### Upload Session Start
```bash
maton api -X POST '/dropbox/2/files/upload_session/start' \
  -H 'Content-Type: application/octet-stream' \
  -H 'Dropbox-API-Arg: {"close": false}' \
  --input '{file_path}'  # <first chunk>
```

#### Upload Session Append
```bash
maton api -X POST '/dropbox/2/files/upload_session/append_v2' \
  -H 'Content-Type: application/octet-stream' \
  -H 'Dropbox-API-Arg: {"cursor": {"session_id": "...", "offset": 10000000}, "close": false}' \
  --input '{file_path}'  # <next chunk>
```

#### Upload Session Finish
```bash
maton api -X POST '/dropbox/2/files/upload_session/finish' \
  -H 'Content-Type: application/octet-stream' \
  -H 'Dropbox-API-Arg: {"cursor": {"session_id": "...", "offset": 50000000}, "commit": {"path": "/file.zip", "mode": "add"}}' \
  --input '{file_path}'  # <final chunk>
```

#### Finish Batch Upload Sessions
```bash
maton api -X POST '/dropbox/2/files/upload_session/finish_batch' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "entries": [
    {
      "cursor": {"session_id": "...", "offset": 50000000},
      "commit": {"path": "/file1.zip", "mode": "add"}
    }
  ]
}
EOF
```

#### Check Batch Status
```bash
maton api -X POST '/dropbox/2/files/upload_session/finish_batch/check' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "async_job_id": "dbjid:..."
}
EOF
```

### Download (Content Endpoints)

#### Download File
```bash
maton api -X POST '/dropbox/2/files/download' \
  -H 'Dropbox-API-Arg: {"path": "/document.pdf"}'
```

#### Download ZIP
```bash
maton api -X POST '/dropbox/2/files/download_zip' \
  -H 'Dropbox-API-Arg: {"path": "/folder"}'
```

#### Export
```bash
maton api -X POST '/dropbox/2/files/export' \
  -H 'Dropbox-API-Arg: {"path": "/document.paper"}'
```

#### Get Preview
```bash
maton api -X POST '/dropbox/2/files/get_preview' \
  -H 'Dropbox-API-Arg: {"path": "/document.docx"}'
```

#### Get Thumbnail
```bash
maton api -X POST '/dropbox/2/files/get_thumbnail_v2' \
  -H 'Dropbox-API-Arg: {"resource": {".tag": "path", "path": "/photo.jpg"}, "format": "jpeg", "size": "w128h128"}'
```

### Search

#### Search Files
```bash
maton api -X POST '/dropbox/2/files/search_v2' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "query": "document"
}
EOF
```

### Revisions

#### List Revisions
```bash
maton api -X POST '/dropbox/2/files/list_revisions' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "path": "/document.pdf"
}
EOF
```

### Tags

#### Get Tags
```bash
maton api -X POST '/dropbox/2/files/tags/get' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "paths": ["/document.pdf"]
}
EOF
```

#### Add Tag
```bash
maton api -X POST '/dropbox/2/files/tags/add' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "path": "/document.pdf",
  "tag_text": "important"
}
EOF
```

#### Remove Tag
```bash
maton api -X POST '/dropbox/2/files/tags/remove' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "path": "/document.pdf",
  "tag_text": "important"
}
EOF
```

## Pagination

Dropbox uses cursor-based pagination:

```bash
maton api -X POST '/dropbox/2/files/list_folder'
# Response includes "cursor" and "has_more": true/false

maton api -X POST '/dropbox/2/files/list_folder/continue'
# Use cursor from previous response
```

## Notes

- All endpoints use POST method
- Standard endpoints use JSON request bodies (`Content-Type: application/json`)
- Content endpoints (upload/download) use binary content (`Content-Type: application/octet-stream`) with params in `Dropbox-API-Arg` header
- Gateway automatically routes content endpoints to `content.dropboxapi.com`
- Use empty string `""` for root folder path
- Paths are case-insensitive but case-preserving
- Tag text must match pattern `[\w]+` (alphanumeric and underscores)
- Temporary links expire after 4 hours
- Max single upload: 150 MB (use upload sessions for up to 350 GB)

## Content Endpoints (routed to content.dropboxapi.com)

The following endpoints are automatically routed to `content.dropboxapi.com`:
- `/2/files/upload`
- `/2/files/upload_session/start`
- `/2/files/upload_session/append_v2`
- `/2/files/upload_session/finish`
- `/2/files/download`
- `/2/files/download_zip`
- `/2/files/export`
- `/2/files/get_preview`
- `/2/files/get_thumbnail`
- `/2/files/get_thumbnail_v2`
- `/2/paper/docs/download`

## Resources

- [Dropbox HTTP API Overview](https://www.dropbox.com/developers/documentation/http/overview)
- [Dropbox API Explorer](https://dropbox.github.io/dropbox-api-v2-explorer/)
- [DBX File Access Guide](https://developers.dropbox.com/dbx-file-access-guide)
