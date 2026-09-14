# SignNow

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `signnow`
**Upstream base URL:** `api.signnow.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.signnow.com/user`
- Gateway: `https://api.maton.ai/signnow/user`

### User API

#### Get Current User

```bash
maton api '/signnow/user'
```

**Response:**
```json
{
  "id": "59cce130e93a4e9488522ca67e3a6779f3e48a72",
  "first_name": "Chris",
  "last_name": "Kim",
  "active": "1",
  "verified": true,
  "emails": ["chris@example.com"],
  "primary_email": "chris@example.com",
  "document_count": 0,
  "subscriptions": [...],
  "teams": [...],
  "organization": {...}
}
```

#### Get User Documents

```bash
maton api '/signnow/user/documents'
```

**Response:**
```json
[
  {
    "id": "c63a7bc73f03449c987bf0feaa36e96212408352",
    "document_name": "Contract",
    "page_count": "3",
    "created": "1770598603",
    "updated": "1770598603",
    "original_filename": "contract.pdf",
    "owner": "chris@example.com",
    "template": false,
    "roles": [],
    "field_invites": [],
    "signatures": []
  }
]
```

### Document API

#### Upload Document

Documents must be uploaded as multipart form data with a PDF file:

`maton api` sends a body verbatim but does not build a multipart envelope, so assemble the body first and hand it to `--input`. Nothing here handles a credential — the CLI still injects it.

```bash
BOUNDARY="maton-$$"
{
  printf -- '--%s\r\nContent-Disposition: form-data; name="file"; filename="document.pdf"\r\nContent-Type: application/pdf\r\n\r\n' "$BOUNDARY"
  cat document.pdf
  printf -- '\r\n--%s--\r\n' "$BOUNDARY"
} > /tmp/signnow-document.body

maton api -X POST '/signnow/document' \
  -H "Content-Type: multipart/form-data; boundary=$BOUNDARY" \
  --input /tmp/signnow-document.body
```

**Response:**
```json
{
  "id": "c63a7bc73f03449c987bf0feaa36e96212408352"
}
```

#### Get Document

```bash
maton api '/signnow/document/{document_id}'
```

**Note:** `{document_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": "c63a7bc73f03449c987bf0feaa36e96212408352",
  "document_name": "Contract",
  "page_count": "3",
  "created": "1770598603",
  "updated": "1770598603",
  "original_filename": "contract.pdf",
  "owner": "chris@example.com",
  "template": false,
  "roles": [],
  "viewer_roles": [],
  "attachments": [],
  "fields": [],
  "signatures": [],
  "texts": [],
  "checks": []
}
```

#### Update Document

```bash
maton api -X PUT '/signnow/document/{document_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "document_name": "Updated Contract Name"
}
JSON
```

**Note:** `{document_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": "c63a7bc73f03449c987bf0feaa36e96212408352",
  "signatures": [],
  "texts": [],
  "checks": []
}
```

#### Download Document

```bash
maton api '/signnow/document/{document_id}/download?type=collapsed'
```

**Note:** `{document_id}` is a placeholder. Replace it with a real value before sending the request.

Returns the PDF file as binary data.

**Query parameters:**
- `type` - Download type: `collapsed` (flattened PDF), `zip` (all pages as images)

#### Get Document History

```bash
maton api '/signnow/document/{document_id}/historyfull'
```

**Note:** `{document_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
[
  {
    "unique_id": "c4eb89d84b2b407ba8ec1cf4d25b8b435bcef69d",
    "user_id": "59cce130e93a4e9488522ca67e3a6779f3e48a72",
    "document_id": "c63a7bc73f03449c987bf0feaa36e96212408352",
    "email": "chris@example.com",
    "created": 1770598603,
    "event": "created_document"
  }
]
```

#### Move Document to Folder

```bash
maton api -X POST '/signnow/document/{document_id}/move' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "folder_id": "5e2798bdd3d642c3aefebe333bb5b723d6db01a4"
}
JSON
```

**Note:** `{document_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "result": "success"
}
```

#### Merge Documents

Combines multiple documents into a single PDF:

```bash
maton api -X POST '/signnow/document/merge' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Merged Document",
  "document_ids": ["doc_id_1", "doc_id_2"]
}
JSON
```

Returns the merged PDF as binary data.

#### Delete Document

```bash
maton api '/signnow/document/{document_id}' -X DELETE
```

**Note:** `{document_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "status": "success"
}
```

### Template API

#### Create Template from Document

```bash
maton api -X POST '/signnow/template' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "document_id": "c63a7bc73f03449c987bf0feaa36e96212408352",
  "document_name": "Contract Template"
}
JSON
```

**Response:**
```json
{
  "id": "47941baee4f74784bc1d37c25e88836fc38ed501"
}
```

#### Create Document from Template

```bash
maton api -X POST '/signnow/template/{template_id}/copy' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "document_name": "New Contract from Template"
}
JSON
```

**Note:** `{template_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": "08f5f4a2cc1a4d6c8a986adbf90be2308807d4ae",
  "name": "New Contract from Template"
}
```

### Signature Invite API

#### Send Freeform Invite

Send a document for signature:

```bash
maton api -X POST '/signnow/document/{document_id}/invite' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "to": "signer@example.com",
  "from": "sender@example.com"
}
JSON
```

**Note:** `{document_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "result": "success",
  "id": "c38a57f08f2e48d98b5de52f75f7b1dd0a074c00",
  "callback_url": "none"
}
```

**Note:** Custom subject and message require a paid subscription plan.

#### Create Signing Link

Create an embeddable signing link (requires document fields):

```bash
maton api -X POST '/signnow/link' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "document_id": "c63a7bc73f03449c987bf0feaa36e96212408352"
}
JSON
```

**Note:** Document must have signature fields added before creating a signing link.

### Folder API

#### Get All Folders

```bash
maton api '/signnow/folder'
```

**Response:**
```json
{
  "id": "2ea71a3a9d06470d8e5ec0df6122971f47db7706",
  "name": "Root",
  "system_folder": true,
  "folders": [
    {
      "id": "5e2798bdd3d642c3aefebe333bb5b723d6db01a4",
      "name": "Documents",
      "document_count": "5",
      "template_count": "2"
    },
    {
      "id": "fafdef6de6d947fc84627e4ddeed6987bfeee02d",
      "name": "Templates",
      "document_count": "0",
      "template_count": "3"
    },
    {
      "id": "6063688b1e724a25aa98befcc3f2cb7795be7da1",
      "name": "Trash Bin",
      "document_count": "0"
    }
  ],
  "total_documents": 0,
  "documents": []
}
```

#### Get Folder by ID

```bash
maton api '/signnow/folder/{folder_id}'
```

**Note:** `{folder_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": "5e2798bdd3d642c3aefebe333bb5b723d6db01a4",
  "name": "Documents",
  "user_id": "59cce130e93a4e9488522ca67e3a6779f3e48a72",
  "parent_id": "2ea71a3a9d06470d8e5ec0df6122971f47db7706",
  "system_folder": true,
  "folders": [],
  "total_documents": 5,
  "documents": [...]
}
```

### Webhook API

#### List Event Subscriptions

```bash
maton api '/signnow/event_subscription'
```

**Response:**
```json
{
  "subscriptions": [
    {
      "id": "b1d6700dfb0444ed9196e913b2515ae8d5f731a7",
      "event": "document.complete",
      "created": "1770598678",
      "callback_url": "https://example.com/webhook"
    }
  ]
}
```

#### Create Event Subscription

```bash
maton api -X POST '/signnow/event_subscription' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "event": "document.complete",
  "callback_url": "https://example.com/webhook"
}
JSON
```

**Response:**
```json
{
  "id": "b1d6700dfb0444ed9196e913b2515ae8d5f731a7",
  "created": 1770598678
}
```

**Available Events:**
- `document.create` - Document created
- `document.update` - Document updated
- `document.delete` - Document deleted
- `document.complete` - Document signed by all parties
- `invite.create` - Invite sent
- `invite.update` - Invite updated

#### Delete Event Subscription

```bash
maton api '/signnow/event_subscription/{subscription_id}' -X DELETE
```

**Note:** `{subscription_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": "b1d6700dfb0444ed9196e913b2515ae8d5f731a7",
  "status": "deleted"
}
```

### Notes

- Documents must be in PDF format for upload
- Supported file types: PDF, DOC, DOCX, ODT, RTF, PNG, JPG
- System folders (Documents, Templates, Archive, Trash Bin) cannot be renamed or deleted
- Creating signing links requires documents to have signature fields
- Custom invite subject/message requires a paid subscription
- Rate limit in development mode: 500 requests/hour per application

### Resources

- [SignNow API Reference](https://docs.signnow.com/docs/signnow/reference)
- [SignNow Developer Portal](https://www.signnow.com/developers)
- [Maton CLI Manual](https://cli.maton.ai/manual)
