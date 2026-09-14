# Dropbox

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `dropbox`
**Upstream base URLs:** `api.dropboxapi.com` (RPC), `content.dropboxapi.com` (upload/download)

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.dropboxapi.com/2/users/get_current_account`
- Gateway: `https://api.maton.ai/dropbox/2/users/get_current_account`

**Important:** All Dropbox API v2 endpoints use HTTP POST. Most endpoints use JSON request bodies, but upload/download endpoints use binary content with parameters in the `Dropbox-API-Arg` header.

### Users API

#### Get Current Account

```bash
maton api -X POST '/dropbox/2/users/get_current_account' -H 'Content-Type: application/json' --input - <<'JSON'
null
JSON
```

**Response:**
```json
{
  "account_id": "dbid:AAA-AdT84WzkyLw5s590DbYF1nGomiAoO8I",
  "name": {
    "given_name": "John",
    "surname": "Doe",
    "familiar_name": "John",
    "display_name": "John Doe",
    "abbreviated_name": "JD"
  },
  "email": "john@example.com",
  "email_verified": true,
  "disabled": false,
  "country": "US",
  "locale": "en",
  "account_type": {
    ".tag": "basic"
  },
  "root_info": {
    ".tag": "user",
    "root_namespace_id": "11989877987",
    "home_namespace_id": "11989877987"
  }
}
```

#### Get Space Usage

```bash
maton api -X POST '/dropbox/2/users/get_space_usage' -H 'Content-Type: application/json' --input - <<'JSON'
null
JSON
```

**Response:**
```json
{
  "used": 538371,
  "allocation": {
    ".tag": "individual",
    "allocated": 2147483648
  }
}
```

### Files API

#### List Folder

```bash
maton api -X POST '/dropbox/2/files/list_folder' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "path": "",
  "recursive": false,
  "include_deleted": false,
  "include_has_explicit_shared_members": false
}
JSON
```

Use empty string `""` for root folder.

Use empty string `""` for the root folder.

**Request body:**
- `recursive` (optional) - Include contents of subdirectories (default: false)
- `include_deleted` (optional) - Include deleted files (default: false)
- `include_media_info` (optional) - Include media info for photos/videos
- `limit` (optional) - Maximum entries per response (1-2000)

**Response:**

```json
{
  "entries": [
    {
      ".tag": "file",
      "name": "document.pdf",
      "path_lower": "/document.pdf",
      "path_display": "/document.pdf",
      "id": "id:Awe3Av8A8YYAAAAAAAAABQ",
      "client_modified": "2026-02-09T19:58:12Z",
      "server_modified": "2026-02-09T19:58:13Z",
      "rev": "016311c063b4f8700000002caa704e3",
      "size": 538371,
      "is_downloadable": true,
      "content_hash": "6542845d7b65ffc5358ebaa6981d991bab9fda194afa48bd727fcbe9e4a3158b"
    },
    {
      ".tag": "folder",
      "name": "Documents",
      "path_lower": "/documents",
      "path_display": "/Documents",
      "id": "id:Awe3Av8A8YYAAAAAAAAABw"
    }
  ],
  "cursor": "AAVqv-MUYFlM98b1QpFK6YaYC8L1s39lWjqbeqgWu4un...",
  "has_more": false
}
```

#### Continue Listing

```bash
maton api -X POST '/dropbox/2/files/list_folder/continue' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "cursor": "AAVqv-MUYFlM98b1QpFK6YaYC8L1s39lWjqbeqgWu4un..."
}
JSON
```

Use when `has_more` is true in the previous response.

#### Get Metadata

```bash
maton api -X POST '/dropbox/2/files/get_metadata' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "path": "/document.pdf",
  "include_media_info": false,
  "include_deleted": false,
  "include_has_explicit_shared_members": false
}
JSON
```

**Response:**
```json
{
  ".tag": "file",
  "name": "document.pdf",
  "path_lower": "/document.pdf",
  "path_display": "/document.pdf",
  "id": "id:Awe3Av8A8YYAAAAAAAAABQ",
  "client_modified": "2026-02-09T19:58:12Z",
  "server_modified": "2026-02-09T19:58:13Z",
  "rev": "016311c063b4f8700000002caa704e3",
  "size": 538371,
  "is_downloadable": true,
  "content_hash": "6542845d7b65ffc5358ebaa6981d991bab9fda194afa48bd727fcbe9e4a3158b"
}
```

#### Create Folder

```bash
maton api -X POST '/dropbox/2/files/create_folder_v2' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "path": "/New Folder",
  "autorename": false
}
JSON
```

**Response:**
```json
{
  "metadata": {
    "name": "New Folder",
    "path_lower": "/new folder",
    "path_display": "/New Folder",
    "id": "id:Awe3Av8A8YYAAAAAAAAABw"
  }
}
```

#### Copy File

```bash
maton api -X POST '/dropbox/2/files/copy_v2' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "from_path": "/source/file.pdf",
  "to_path": "/destination/file.pdf",
  "autorename": false
}
JSON
```

**Response:**
```json
{
  "metadata": {
    ".tag": "file",
    "name": "file.pdf",
    "path_lower": "/destination/file.pdf",
    "path_display": "/destination/file.pdf",
    "id": "id:Awe3Av8A8YYAAAAAAAAACA"
  }
}
```

#### Move File

```bash
maton api -X POST '/dropbox/2/files/move_v2' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "from_path": "/old/location/file.pdf",
  "to_path": "/new/location/file.pdf",
  "autorename": false
}
JSON
```

**Response:**
```json
{
  "metadata": {
    ".tag": "file",
    "name": "file.pdf",
    "path_lower": "/new/location/file.pdf",
    "path_display": "/new/location/file.pdf",
    "id": "id:Awe3Av8A8YYAAAAAAAAACA"
  }
}
```

#### Delete File

```bash
maton api -X POST '/dropbox/2/files/delete_v2' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "path": "/file-to-delete.pdf"
}
JSON
```

**Response:**
```json
{
  "metadata": {
    ".tag": "file",
    "name": "file-to-delete.pdf",
    "path_lower": "/file-to-delete.pdf",
    "path_display": "/file-to-delete.pdf",
    "id": "id:Awe3Av8A8YYAAAAAAAAABQ"
  }
}
```

#### Get Temporary Link

```bash
maton api -X POST '/dropbox/2/files/get_temporary_link' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "path": "/document.pdf"
}
JSON
```

**Response:**
```json
{
  "metadata": {
    "name": "document.pdf",
    "path_lower": "/document.pdf",
    "path_display": "/document.pdf",
    "id": "id:Awe3Av8A8YYAAAAAAAAABQ",
    "size": 538371,
    "is_downloadable": true
  },
  "link": "https://uc785ee484c03b6556c091ea4491.dl.dropboxusercontent.com/cd/0/get/..."
}
```

The link is valid for 4 hours.

### Upload API

Content endpoints use `Content-Type: application/octet-stream` with parameters in the `Dropbox-API-Arg` header.

#### Upload File (up to 150 MB)

```bash
maton api -X POST '/dropbox/2/files/upload' -H 'Content-Type: application/octet-stream' \
  -H 'Dropbox-API-Arg: {"path": "/test.txt", "mode": "add", "autorename": true, "mute": false}' \
  --input ./test.txt
```

**Parameters (in Dropbox-API-Arg header):**
- `path` (required) - Path in Dropbox where the file will be saved
- `mode` - Write mode: `add` (default), `overwrite`, or `update` with rev
- `autorename` - If true, rename file if there's a conflict (default: false)
- `mute` - If true, don't notify desktop app (default: false)
- `strict_conflict` - If true, be more strict about conflicts (default: false)

**Response:**
```json
{
  "name": "test.txt",
  "path_lower": "/test.txt",
  "path_display": "/test.txt",
  "id": "id:Awe3Av8A8YYAAAAAAAAABw",
  "client_modified": "2026-04-14T10:00:00Z",
  "server_modified": "2026-04-14T10:00:01Z",
  "rev": "016311c063b4f8700000002caa704e4",
  "size": 1024,
  "is_downloadable": true,
  "content_hash": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"
}
```

#### Upload Sessions (Large Files)

For files larger than 150 MB, use upload sessions. Files can be up to 350 GB.

**Step 1: Start Session**

```bash
maton api -X POST '/dropbox/2/files/upload_session/start' -H 'Content-Type: application/octet-stream' \
  -H 'Dropbox-API-Arg: {"close": false}' \
  --input ./chunk.bin
```

**Response:**
```json
{
  "session_id": "AAAAAAAAAAFxxxxxxxxxxxxxxx"
}
```

**Step 2: Append Data (repeat as needed)**

```bash
maton api -X POST '/dropbox/2/files/upload_session/append_v2' -H 'Content-Type: application/octet-stream' \
  -H 'Dropbox-API-Arg: {"cursor": {"session_id": "AAAAAAAAAAFxxxxxxxxxxxxxxx", "offset": 10000000}, "close": false}' \
  --input ./chunk.bin
```

The `offset` must match the total bytes uploaded so far.

**Step 3: Finish Session**

```bash
maton api -X POST '/dropbox/2/files/upload_session/finish' -H 'Content-Type: application/octet-stream' \
  -H 'Dropbox-API-Arg: {"cursor": {"session_id": "AAAAAAAAAAFxxxxxxxxxxxxxxx", "offset": 50000000}, "commit": {"path": "/large_file.zip", "mode": "add", "autorename": true}}' \
  --input ./chunk.bin
```

**Response:** Same as regular upload endpoint.

#### Finish Batch Upload Sessions

Complete multiple upload sessions in one call:

```bash
maton api -X POST '/dropbox/2/files/upload_session/finish_batch' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "entries": [
    {
      "cursor": {
        "session_id": "AAAAAAAAAAFxxxxxxxxxxxxxxx",
        "offset": 50000000
      },
      "commit": {
        "path": "/file1.zip",
        "mode": "add",
        "autorename": true
      }
    },
    {
      "cursor": {
        "session_id": "AAAAAAAAAAFyyyyyyyyyyyyyyy",
        "offset": 30000000
      },
      "commit": {
        "path": "/file2.zip",
        "mode": "add",
        "autorename": true
      }
    }
  ]
}
JSON
```

**Response (async job):**
```json
{
  ".tag": "async_job_id",
  "async_job_id": "dbjid:AAAAAAAAAA..."
}
```

Check status with `/files/upload_session/finish_batch/check`.

#### Check Batch Status

```bash
maton api -X POST '/dropbox/2/files/upload_session/finish_batch/check' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "async_job_id": "dbjid:AAAAAAAAAA..."
}
JSON
```

**Response (in progress):**
```json
{
  ".tag": "in_progress"
}
```

**Response (complete):**
```json
{
  ".tag": "complete",
  "entries": [
    {
      ".tag": "success",
      "name": "file1.zip",
      "path_lower": "/file1.zip",
      "path_display": "/file1.zip",
      "id": "id:Awe3Av8A8YYAAAAAAAAABw"
    },
    {
      ".tag": "success",
      "name": "file2.zip",
      "path_lower": "/file2.zip",
      "path_display": "/file2.zip",
      "id": "id:Awe3Av8A8YYAAAAAAAAABx"
    }
  ]
}
```

### Download API

#### Download File

```bash
maton api -X POST '/dropbox/2/files/download' -H 'Dropbox-API-Arg: {"path": "/document.pdf"}'
```

**Response:** Raw file contents with metadata in `Dropbox-API-Result` response header.

#### Download ZIP

```bash
maton api -X POST '/dropbox/2/files/download_zip' -H 'Dropbox-API-Arg: {"path": "/folder"}'
```

**Response:** ZIP file contents. Note: folders larger than 20 GB or with more than 10,000 files cannot be downloaded as ZIP.

#### Export

Export a file from Dropbox (e.g., Paper docs to markdown):

```bash
maton api -X POST '/dropbox/2/files/export' -H 'Dropbox-API-Arg: {"path": "/document.paper"}'
```

#### Get Preview

```bash
maton api -X POST '/dropbox/2/files/get_preview' -H 'Dropbox-API-Arg: {"path": "/document.docx"}'
```

**Response:** PDF preview of the file.

#### Get Thumbnail

```bash
maton api -X POST '/dropbox/2/files/get_thumbnail_v2' -H 'Dropbox-API-Arg: {"resource": {".tag": "path", "path": "/photo.jpg"}, "format": "jpeg", "size": "w128h128"}'
```

**Thumbnail Sizes:**
- `w32h32`, `w64h64`, `w128h128`, `w256h256`, `w480h320`, `w640h480`, `w960h640`, `w1024h768`, `w2048h1536`

### Search API

#### Search Files

```bash
maton api -X POST '/dropbox/2/files/search_v2' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "query": "document",
  "options": {
    "path": "",
    "max_results": 100,
    "file_status": "active",
    "filename_only": false
  }
}
JSON
```

**Response:**
```json
{
  "has_more": false,
  "matches": [
    {
      "highlight_spans": [],
      "match_type": {
        ".tag": "filename"
      },
      "metadata": {
        ".tag": "metadata",
        "metadata": {
          ".tag": "file",
          "name": "document.pdf",
          "path_display": "/document.pdf",
          "path_lower": "/document.pdf",
          "id": "id:Awe3Av8A8YYAAAAAAAAABw"
        }
      }
    }
  ]
}
```

#### Continue Search

```bash
maton api -X POST '/dropbox/2/files/search/continue_v2' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "cursor": "..."
}
JSON
```

### Revisions API

#### List Revisions

```bash
maton api -X POST '/dropbox/2/files/list_revisions' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "path": "/document.pdf",
  "mode": "path",
  "limit": 10
}
JSON
```

**Response:**
```json
{
  "is_deleted": false,
  "entries": [
    {
      "name": "document.pdf",
      "path_lower": "/document.pdf",
      "path_display": "/document.pdf",
      "id": "id:Awe3Av8A8YYAAAAAAAAABQ",
      "client_modified": "2026-02-09T19:58:12Z",
      "server_modified": "2026-02-09T19:58:13Z",
      "rev": "016311c063b4f8700000002caa704e3",
      "size": 538371,
      "is_downloadable": true
    }
  ],
  "has_more": false
}
```

#### Restore File

```bash
maton api -X POST '/dropbox/2/files/restore' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "path": "/document.pdf",
  "rev": "016311c063b4f8700000002caa704e3"
}
JSON
```

#### Delete Batch

```bash
maton api -X POST '/dropbox/2/files/delete_batch' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "entries": [
    {"path": "/file1.pdf"},
    {"path": "/file2.pdf"}
  ]
}
JSON
```

Returns async job ID. Check status with `/files/delete_batch/check`.

#### Copy Batch

```bash
maton api -X POST '/dropbox/2/files/copy_batch_v2' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "entries": [
    {"from_path": "/source/file1.pdf", "to_path": "/dest/file1.pdf"},
    {"from_path": "/source/file2.pdf", "to_path": "/dest/file2.pdf"}
  ],
  "autorename": false
}
JSON
```

#### Move Batch

```bash
maton api -X POST '/dropbox/2/files/move_batch_v2' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "entries": [
    {"from_path": "/old/file1.pdf", "to_path": "/new/file1.pdf"},
    {"from_path": "/old/file2.pdf", "to_path": "/new/file2.pdf"}
  ],
  "autorename": false
}
JSON
```

### Tags API

#### Get Tags

```bash
maton api -X POST '/dropbox/2/files/tags/get' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "paths": ["/document.pdf", "/folder"]
}
JSON
```

**Response:**
```json
{
  "paths_to_tags": [
    {
      "path": "/document.pdf",
      "tags": [
        {
          ".tag": "user_generated_tag",
          "tag_text": "important"
        }
      ]
    },
    {
      "path": "/folder",
      "tags": []
    }
  ]
}
```

#### Add Tag

```bash
maton api -X POST '/dropbox/2/files/tags/add' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "path": "/document.pdf",
  "tag_text": "important"
}
JSON
```

Returns `null` on success.

**Note:** Tag text must match pattern `[\w]+` (alphanumeric and underscores only, no hyphens or spaces).

#### Remove Tag

```bash
maton api -X POST '/dropbox/2/files/tags/remove' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "path": "/document.pdf",
  "tag_text": "important"
}
JSON
```

Returns `null` on success.

### Pagination

Dropbox uses cursor-based pagination:

```bash
maton api -X POST '/dropbox/2/files/list_folder'
# Response includes "cursor" and "has_more": true/false

maton api -X POST '/dropbox/2/files/list_folder/continue'
# Use cursor from previous response
```

### Content Endpoints (routed to content.dropboxapi.com)

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

### Notes

- All endpoints use POST method
- Standard endpoints use JSON request bodies (`Content-Type: application/json`)
- Content endpoints (upload/download) use binary content (`Content-Type: application/octet-stream`) with params in `Dropbox-API-Arg` header
- Gateway automatically routes content endpoints to `content.dropboxapi.com`
- Use empty string `""` for root folder path
- Paths are case-insensitive but case-preserving
- Tag text must match pattern `[\w]+` (alphanumeric and underscores)
- Temporary links expire after 4 hours
- Max single upload: 150 MB (use upload sessions for up to 350 GB)

### Resources

- [Dropbox HTTP API Overview](https://www.dropbox.com/developers/documentation/http/overview)
- [Dropbox API Explorer](https://dropbox.github.io/dropbox-api-v2-explorer/)
- [DBX File Access Guide](https://developers.dropbox.com/dbx-file-access-guide)
- [Maton CLI Manual](https://cli.maton.ai/manual)
