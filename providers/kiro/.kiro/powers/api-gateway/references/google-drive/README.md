# Google Drive

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `google-drive`
**Upstream base URL:** `www.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://www.googleapis.com/drive/v3/files`
- Gateway: `https://api.maton.ai/google-drive/drive/v3/files`

### Files API

### List Files

```bash
maton google-drive file list -L 10
```

Or with `maton api`:

```bash
maton api '/google-drive/drive/v3/files?pageSize=10'
```

**With a search query:**

```bash
maton google-drive file list -Q "name contains 'report'" -L 10
```

Or with `maton api`:

```bash
maton api "/google-drive/drive/v3/files?q=name%20contains%20'report'&pageSize=10"
```

**Only folders:**

```bash
maton google-drive file list -Q "mimeType='application/vnd.google-apps.folder'"
```

Or with `maton api`:

```bash
maton api "/google-drive/drive/v3/files?q=mimeType='application/vnd.google-apps.folder'"
```

**Files in a specific folder:**

```bash
maton google-drive file list -Q "'{folderId}' in parents"
```

Or with `maton api`:

```bash
maton api "/google-drive/drive/v3/files?q='{folderId}'+in+parents"
```

**Selected fields only:**

```bash
maton google-drive file list --fields 'files(id,name,mimeType,createdTime,modifiedTime,size)'
```

Or with `maton api`:

```bash
maton api '/google-drive/drive/v3/files?fields=files(id,name,mimeType,createdTime,modifiedTime,size)'
```

**Note:** `{folderId}` is a placeholder. Replace it with a real value before sending the request.

### Get File Metadata

```bash
maton google-drive file get {fileId} --fields 'id,name,mimeType,size,createdTime'
```

Or with `maton api`:

```bash
maton api '/google-drive/drive/v3/files/{fileId}?fields=id,name,mimeType,size,createdTime'
```

**Note:** `{fileId}` is a placeholder. Replace it with a real value before sending the request.

### Download File Content

```bash
maton google-drive file download {fileId} --output ./report.pdf
```

Or with `maton api`:

```bash
maton api '/google-drive/drive/v3/files/{fileId}?alt=media'
```

**Note:** `{fileId}` is a placeholder. Replace it with a real value before sending the request.

### Export Google Docs (to PDF, DOCX, etc.)

```bash
maton google-drive file export {fileId} --mime-type application/pdf --output ./doc.pdf
```

Or with `maton api`:

```bash
maton api '/google-drive/drive/v3/files/{fileId}/export?mimeType=application/pdf'
```

**Note:** `{fileId}` is a placeholder. Replace it with a real value before sending the request.

### Create File (metadata only)

```bash
maton google-drive file create --name 'New Document' --mime-type application/vnd.google-apps.document
```

Or with `maton api`:

```bash
maton api -X POST '/google-drive/drive/v3/files' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Document",
  "mimeType": "application/vnd.google-apps.document"
}
JSON
```

### Create Folder

```bash
maton google-drive file create --name 'New Folder' --mime-type application/vnd.google-apps.folder
```

Or with `maton api`:

```bash
maton api -X POST '/google-drive/drive/v3/files' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "New Folder",
  "mimeType": "application/vnd.google-apps.folder"
}
JSON
```

### Update File Metadata

```bash
maton google-drive file update {fileId} --name 'Renamed File'
```

Or with `maton api`:

```bash
maton api -X PATCH '/google-drive/drive/v3/files/{fileId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Renamed File"
}
JSON
```

**Note:** `{fileId}` is a placeholder. Replace it with a real value before sending the request.

### Move File to Folder

```bash
maton google-drive file update {fileId} --add-parents {newFolderId} --remove-parents {oldFolderId}
```

Or with `maton api`:

```bash
maton api -X PATCH '/google-drive/drive/v3/files/{fileId}?addParents={newFolderId}&removeParents={oldFolderId}'
```

**Note:** `{fileId}`, `{newFolderId}` and `{oldFolderId}` are placeholders. Replace each of them with real values before sending the request.

### Delete File

```bash
maton google-drive file delete {fileId}
```

Or with `maton api`:

```bash
maton api '/google-drive/drive/v3/files/{fileId}' -X DELETE
```

**Note:** `{fileId}` is a placeholder. Replace it with a real value before sending the request.

### Copy File

```bash
maton google-drive file copy {fileId} --name 'Copy of File'
```

Or with `maton api`:

```bash
maton api -X POST '/google-drive/drive/v3/files/{fileId}/copy' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Copy of File"
}
JSON
```

**Note:** `{fileId}` is a placeholder. Replace it with a real value before sending the request.

### Permissions API

#### Create Permission (Share File)

```bash
maton google-drive permission create -f {fileId} --type user --role reader --email-address user@example.com
```

Or with `maton api`:

```bash
maton api -X POST '/google-drive/drive/v3/files/{fileId}/permissions' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "role": "reader",
  "type": "user",
  "emailAddress": "user@example.com"
}
JSON
```

**Note:** `{fileId}` is a placeholder. Replace it with a real value before sending the request.

### File Uploads API

Upload endpoints use a different path pattern: `/google-drive/upload/drive/v3/files`

The CLI's `maton google-drive file upload` picks the upload type for you based on the file size and flags:

| Flags | File size | Upload type used |
|---|---|---|
| `--no-metadata` | any | `uploadType=media` |
| (default, with metadata) | < 5 MiB | `uploadType=multipart` |
| (default, with metadata) | ≥ 5 MiB | `uploadType=resumable` (chunked, auto-resumes on transient errors) |

#### Simple Upload (up to 5MB)

```bash
maton google-drive file upload ./hello.txt --no-metadata
```

Or with `maton api`:

```bash
maton api -X POST '/google-drive/upload/drive/v3/files?uploadType=media' \
  -H 'Content-Type: text/plain' \
  --input '{file_path}'  # <file content>
```

**Note:** `{file_path}` is a placeholder. Replace it with a real value before sending the request.

#### Multipart Upload (with metadata, up to 5MB)

```bash
maton google-drive file upload ./myfile.txt
```

Or with `maton api`:

```bash
maton api -X POST '/google-drive/upload/drive/v3/files?uploadType=multipart' \
  -H 'Content-Type: multipart/related; boundary=boundary' \
  --input - <<'EOF'
--boundary
Content-Type: application/json

{"name": "myfile.txt"}
--boundary
Content-Type: text/plain

<file content>
--boundary--
EOF
```

**Note:** Use this for files up to 5MB when you need to include metadata (name, description, etc.). `maton api` sends a body verbatim but does not build a multipart envelope, so assemble the body first and hand it to `--input`. Nothing here handles a credential — the CLI still injects it.

#### Resumable Upload (for large files)

```bash
maton google-drive file upload ./large_file.bin
```

Or with `maton api`:

```bash
# Step 1: Initiate session
maton api -X POST '/google-drive/upload/drive/v3/files?uploadType=resumable' \
  -H 'Content-Type: application/json' \
  -H 'X-Upload-Content-Type: application/octet-stream' \
  -H 'X-Upload-Content-Length: {file_size}' \
  --input - <<'JSON'
{"name": "large_file.bin"}
JSON

# Response includes Location header with upload URI
# Step 2: Upload content to that URI
```

**Note:** `{file_size}` is a placeholder. Replace it with a real value before sending the request.

**Note:** The CLI automatically resumes from the last persisted offset if interrupted — re-run the same command.

#### Upload to Specific Folder

```bash
maton google-drive file upload ./myfile.txt --parent {folderId}
```

**Note:** `{folderId}` is a placeholder. Replace it with a real value before sending the request.

#### Update File Content

```bash
maton google-drive file update {fileId} --file ./updated.txt
```

Or with `maton api`:

```bash
maton api -X PATCH '/google-drive/upload/drive/v3/files/{fileId}?uploadType=media' \
  -H 'Content-Type: text/plain' \
  --input '{file_path}'  # <new file content>
```

**Note:** `{fileId}` and `{file_path}` are placeholders. Replace each of them with real values before sending the request.

### Filtering

Use in the `q` parameter:
- `name = 'exact name'`
- `name contains 'partial'`
- `mimeType = 'application/pdf'`
- `'folderId' in parents`
- `trashed = false`
- `modifiedTime > '2024-01-01T00:00:00'`

Combine with `and`:
```
name contains 'report' and mimeType = 'application/pdf'
```

### Common MIME Types

- `application/vnd.google-apps.document` - Google Docs
- `application/vnd.google-apps.spreadsheet` - Google Sheets
- `application/vnd.google-apps.presentation` - Google Slides
- `application/vnd.google-apps.folder` - Folder
- `application/pdf` - PDF

### Pagination

Google Drive uses token-based pagination. The CLI handles this automatically with `--paginate`:

```bash
maton google-drive file list --paginate
```

For raw HTTP requests, pass the `nextPageToken` from the previous response as the `pageToken` query parameter.

### Notes

- Use `fields` parameter to limit response data
- Pagination uses `pageToken` from previous response's `nextPageToken`
- Upload endpoints use `/upload/drive/v3/files` path (note the `/upload` prefix)
- Use `uploadType=resumable` for files larger than 5MB
- Resumable uploads support chunking (256KB minimum, 5MB recommended)

### Resources

- [Google Drive API Overview](https://developers.google.com/workspace/drive/api/reference/rest/v3#rest-resource:-v3.about)
- [List Files](https://developers.google.com/drive/api/reference/rest/v3/files/list)
- [Get File](https://developers.google.com/drive/api/reference/rest/v3/files/get)
- [Create File](https://developers.google.com/drive/api/reference/rest/v3/files/create)
- [Update File](https://developers.google.com/drive/api/reference/rest/v3/files/update)
- [Delete File](https://developers.google.com/drive/api/reference/rest/v3/files/delete)
- [Copy File](https://developers.google.com/drive/api/reference/rest/v3/files/copy)
- [Export File](https://developers.google.com/drive/api/reference/rest/v3/files/export)
- [Upload Files](https://developers.google.com/drive/api/guides/manage-uploads)
- [Resumable Uploads](https://developers.google.com/drive/api/guides/manage-uploads#resumable)
- [Create Permission](https://developers.google.com/workspace/drive/api/reference/rest/v3/permissions/create)
- [Search Query Syntax](https://developers.google.com/drive/api/guides/search-files)
- [Maton CLI Manual](https://cli.maton.ai/manual)
