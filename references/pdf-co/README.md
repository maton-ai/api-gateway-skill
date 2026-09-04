# PDF.co Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `pdf-co`
**Base URL proxied:** `api.pdf.co`

> **Privacy — document contents are processed by a third party.** PDF.co is an external service. Everything you pass to it leaves the user's environment and is processed on PDF.co's infrastructure: the `url` you supply is **fetched by PDF.co**, and raw HTML, uploaded files, and extracted text are all handled server-side by them.
> - Tell the user their document will be sent to PDF.co (a third-party processor) and get approval before submitting anything non-trivial. Documents here are often invoices, contracts, or statements containing PII and financial data.
> - **Never submit privileged or regulated material** — legal/client documents (see [clio](../clio/README.md)), health records, or credentials — to this service.
> - Any `url` you pass must be one the user provided or approved. PDF.co will retrieve it, so never pass an internal/private URL, a pre-signed link, or a URL that embeds a token or secret.
> - **PDF passwords are credentials.** `password`, `userPassword`, and `ownerPassword` are transmitted to PDF.co. Only send a password the user explicitly supplied for that document; never log or reuse it, and never guess or brute-force one.
> - Output links returned by PDF.co are publicly reachable URLs hosted by PDF.co. Treat them as unlisted-but-public, and do not share them for sensitive documents.

## API Path Pattern

```
/pdf-co/v1/{endpoint}
```

## Common Endpoints

### PDF Information

```bash
maton api -X POST '/pdf-co/v1/pdf/info' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/document.pdf"
}
EOF
```

### Convert PDF to Text

```bash
maton api -X POST '/pdf-co/v1/pdf/convert/to/text' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/document.pdf",
  "pages": "0-",
  "inline": true
}
EOF
```

### Convert PDF to CSV

```bash
maton api -X POST '/pdf-co/v1/pdf/convert/to/csv' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/document.pdf",
  "pages": "0-",
  "inline": true
}
EOF
```

### Convert PDF to JSON

```bash
maton api -X POST '/pdf-co/v1/pdf/convert/to/json' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/document.pdf",
  "inline": true
}
EOF
```

### Convert PDF to HTML

```bash
maton api -X POST '/pdf-co/v1/pdf/convert/to/html' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/document.pdf",
  "name": "output.html"
}
EOF
```

### Convert PDF to XLSX (Excel)

```bash
maton api -X POST '/pdf-co/v1/pdf/convert/to/xlsx' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/document.pdf",
  "name": "output.xlsx"
}
EOF
```

### Convert PDF to PNG

```bash
maton api -X POST '/pdf-co/v1/pdf/convert/to/png' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/document.pdf",
  "pages": "0",
  "name": "page.png"
}
EOF
```

### Convert PDF to JPG

```bash
maton api -X POST '/pdf-co/v1/pdf/convert/to/jpg' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/document.pdf",
  "pages": "0",
  "name": "page.jpg"
}
EOF
```

### Convert HTML to PDF

```bash
maton api -X POST '/pdf-co/v1/pdf/convert/from/html' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "html": "<html><body><h1>Hello World</h1></body></html>",
  "name": "output.pdf",
  "paperSize": "Letter",
  "orientation": "Portrait"
}
EOF
```

### Convert URL to PDF

```bash
maton api -X POST '/pdf-co/v1/pdf/convert/from/url' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com",
  "name": "webpage.pdf"
}
EOF
```

### Merge PDFs

```bash
maton api -X POST '/pdf-co/v1/pdf/merge' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/doc1.pdf,https://example.com/doc2.pdf",
  "name": "merged.pdf"
}
EOF
```

### Split PDF

```bash
maton api -X POST '/pdf-co/v1/pdf/split' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/document.pdf",
  "pages": "1-3,4-6,7-"
}
EOF
```

### Delete Pages

```bash
maton api -X POST '/pdf-co/v1/pdf/edit/delete-pages' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/document.pdf",
  "pages": "2,4,6"
}
EOF
```


### Add Text and Images

```bash
maton api -X POST '/pdf-co/v1/pdf/edit/add' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/document.pdf",
  "name": "annotated.pdf",
  "annotations": [
    {
      "text": "CONFIDENTIAL",
      "x": 100,
      "y": 100,
      "size": 24,
      "pages": "0-"
    }
  ]
}
EOF
```

### Search and Replace Text

```bash
maton api -X POST '/pdf-co/v1/pdf/edit/replace-text' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/document.pdf",
  "searchString": "old text",
  "replaceString": "new text"
}
EOF
```

### Search and Delete Text

```bash
maton api -X POST '/pdf-co/v1/pdf/edit/delete-text' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/document.pdf",
  "searchString": "text to remove"
}
EOF
```

### Add Password

```bash
maton api -X POST '/pdf-co/v1/pdf/security/add' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/document.pdf",
  "ownerPassword": "owner123",
  "userPassword": "user456"
}
EOF
```

### Remove Password

```bash
maton api -X POST '/pdf-co/v1/pdf/security/remove' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/document.pdf",
  "password": "currentpassword"
}
EOF
```

### AI Invoice Parser

```bash
maton api -X POST '/pdf-co/v1/ai-invoice-parser' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/invoice.pdf"
}
EOF
```

### Document Parser

```bash
maton api -X POST '/pdf-co/v1/pdf/documentparser' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/document.pdf",
  "templateId": "your-template-id"
}
EOF
```

### Generate Barcode

```bash
maton api -X POST '/pdf-co/v1/barcode/generate' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "value": "1234567890",
  "type": "QRCode",
  "name": "barcode.png"
}
EOF
```

### Read Barcode

```bash
maton api -X POST '/pdf-co/v1/barcode/read/from/url' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "url": "https://example.com/barcode.png",
  "types": "QRCode,Code128,Code39,EAN13,UPCA"
}
EOF
```

### Check Async Job Status

```bash
maton api -X POST '/pdf-co/v1/job/check' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "jobId": "abc123"
}
EOF
```

## Notes

- All file URLs must be publicly accessible or use PDF.co temporary storage
- Multiple URLs for merge operations should be comma-separated
- Page indices are 0-based (first page is `0`)
- Page ranges use format: `0-2` (pages 0,1,2), `3-` (page 3 to end), `0,2,4` (specific pages)
- Output files are stored temporarily and expire after 60 minutes by default
- Use `async: true` for large files to avoid timeout
- Use `inline: true` to get content directly in response instead of URL

## Resources

- [PDF.co API Documentation](https://docs.pdf.co)
- [PDF.co API Reference](https://docs.pdf.co/api-reference)
