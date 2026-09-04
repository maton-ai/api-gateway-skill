# SignNow Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `signnow`
**Base URL proxied:** `api.signnow.com`

## API Path Pattern

```
/signnow/{resource}
```

## Common Endpoints

### User

```bash
maton api '/signnow/user'
maton api '/signnow/user/documents'
```

### Documents

```bash
# Upload document: multipart/form-data is not expressible with `maton api`; see the Python snippet below.

# Get document
maton api '/signnow/document/{document_id}'

# Update document
maton api -X PUT '/signnow/document/{document_id}'

# Download document
maton api '/signnow/document/{document_id}/download?type=collapsed'

# Get document history
maton api '/signnow/document/{document_id}/historyfull'

# Move document to folder
maton api -X POST '/signnow/document/{document_id}/move'

# Merge documents (returns PDF)
maton api -X POST '/signnow/document/merge'

# Delete document
maton api -X DELETE '/signnow/document/{document_id}'
```

Upload document:

```bash
# multipart/form-data is not expressible with `maton api`; call the gateway directly with `MATON_API_KEY` (see SKILL.md appendix).
python <<'EOF'
import json, os, urllib.request, uuid

# Maton API key from the environment; never print, log, or persist it.
TOKEN = os.environ["MATON_API_KEY"]

# Exactly the path the user gave — never a discovered or inferred one.
file_path = '/path/to/document.pdf'

boundary = uuid.uuid4().hex
with open(file_path, 'rb') as f:
    body = (f'--{boundary}\r\nContent-Disposition: form-data; name="file"; filename="{os.path.basename(file_path)}"\r\n'
            f'Content-Type: application/pdf\r\n\r\n').encode() + f.read() + f'\r\n--{boundary}--\r\n'.encode()

req = urllib.request.Request('https://api.maton.ai/signnow/document', data=body, method='POST')
req.add_header('Authorization', f'Bearer {TOKEN}')
req.add_header('User-Agent', 'maton-gateway-skill/1.2')
req.add_header('Content-Type', f'multipart/form-data; boundary={boundary}')
print(json.dumps(json.load(urllib.request.urlopen(req)), indent=2))
EOF
```

### Templates

```bash
# Create template from document
maton api -X POST '/signnow/template'

# Create document from template
maton api -X POST '/signnow/template/{template_id}/copy'
```

### Invites

```bash
# Send freeform invite
maton api -X POST '/signnow/document/{document_id}/invite'

# Create signing link (requires document fields)
maton api -X POST '/signnow/link'
```

### Folders

```bash
maton api '/signnow/folder'
maton api '/signnow/folder/{folder_id}'
```

### Webhooks (Event Subscriptions)

```bash
maton api '/signnow/event_subscription'
maton api -X POST '/signnow/event_subscription'
maton api -X DELETE '/signnow/event_subscription/{subscription_id}'
```

## Notes

- Documents must be uploaded as multipart form data with PDF file
- Supported file types: PDF, DOC, DOCX, ODT, RTF, PNG, JPG
- System folders cannot be renamed or deleted
- Creating signing links requires documents to have signature fields
- Custom invite subject/message requires paid subscription
- Rate limit in development mode: 500 requests/hour per application

## Resources

- [SignNow API Reference](https://docs.signnow.com/docs/signnow/reference)
- [SignNow Developer Portal](https://www.signnow.com/developers)
