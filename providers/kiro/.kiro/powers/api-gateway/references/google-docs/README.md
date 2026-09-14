# Google Docs

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `google-docs`
**Upstream base URL:** `docs.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://docs.googleapis.com/v1/documents`
- Gateway: `https://api.maton.ai/google-docs/v1/documents`

### Documents API

#### Get Document

```bash
maton google-docs document get {documentId}            # human-readable summary (id, title, revision)
maton google-docs document get {documentId} --json     # full document payload (body, styles, etc.)
```

Or with `maton api`:

```bash
maton api '/google-docs/v1/documents/{documentId}'
```

**Note:** `{documentId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Document

```bash
maton google-docs document create --title 'New Document'
```

Or with `maton api`:

```bash
maton api -X POST '/google-docs/v1/documents' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "New Document"
}
JSON
```

#### Batch Update Document

```bash
maton google-docs document write {documentId} --text 'Hello, World!'
```

Or with `maton api`:

```bash
maton api -X POST '/google-docs/v1/documents/{documentId}:batchUpdate' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "requests": [
    {
      "insertText": {
        "location": {"index": 1},
        "text": "Hello, World!"
      }
    }
  ]
}
JSON
```

**Note:** `{documentId}` is a placeholder. Replace it with a real value before sending the request.

### Common BatchUpdate Requests

#### Insert Text

```json
{
  "insertText": {
    "location": {"index": 1},
    "text": "Text to insert"
  }
}
```

#### Delete Content

```json
{
  "deleteContentRange": {
    "range": {
      "startIndex": 1,
      "endIndex": 10
    }
  }
}
```

#### Replace All Text

```json
{
  "replaceAllText": {
    "containsText": {
      "text": "{{placeholder}}",
      "matchCase": true
    },
    "replaceText": "replacement value"
  }
}
```

#### Insert Table

```json
{
  "insertTable": {
    "location": {"index": 1},
    "rows": 3,
    "columns": 3
  }
}
```

#### Insert Inline Image

```json
{
  "insertInlineImage": {
    "location": {"index": 1},
    "uri": "https://example.com/image.png",
    "objectSize": {
      "height": {"magnitude": 100, "unit": "PT"},
      "width": {"magnitude": 100, "unit": "PT"}
    }
  }
}
```

#### Update Text Style

```json
{
  "updateTextStyle": {
    "range": {
      "startIndex": 1,
      "endIndex": 10
    },
    "textStyle": {
      "bold": true,
      "fontSize": {"magnitude": 14, "unit": "PT"}
    },
    "fields": "bold,fontSize"
  }
}
```

#### Insert Page Break

```json
{
  "insertPageBreak": {
    "location": {"index": 1}
  }
}
```

### Document Structure

The document body contains:
- `content` - Array of structural elements
- `body.content[].paragraph` - Paragraph element
- `body.content[].table` - Table element
- `body.content[].sectionBreak` - Section break

### Notes

- Index positions are 1-based (document starts at index 1)
- Use `endOfSegmentLocation` to append at end
- Multiple requests in batchUpdate are applied atomically
- Get document first to find correct indices for updates
- The `fields` parameter in style updates uses field mask syntax

### Resources

- [Google Docs API Overview](https://developers.google.com/docs/api/how-tos/overview)
- [Get Document](https://developers.google.com/docs/api/reference/rest/v1/documents/get)
- [Create Document](https://developers.google.com/docs/api/reference/rest/v1/documents/create)
- [Batch Update](https://developers.google.com/docs/api/reference/rest/v1/documents/batchUpdate)
- [Request Types Reference](https://developers.google.com/docs/api/reference/rest/v1/documents/request)
- [Document Structure Guide](https://developers.google.com/docs/api/concepts/structure)
- [Maton CLI Manual](https://cli.maton.ai/manual)
