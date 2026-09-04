# Reducto Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `reducto`
**Base URL proxied:** `platform.reducto.ai`

## API Path Pattern

```
/reducto/parse
/reducto/parse_async
/reducto/extract
/reducto/extract_async
/reducto/split
/reducto/split_async
/reducto/edit
/reducto/edit_async
/reducto/upload
/reducto/pipeline
/reducto/jobs
/reducto/job/{job_id}
/reducto/version
```

## Important Notes

- Connection uses API_KEY authentication method (not OAuth)
- Use async endpoints for large documents to avoid timeouts
- Upload presigned URLs expire quickly
- Use `reducto://` URLs from upload in subsequent requests
- Use `jobid://` to reuse parsed content from previous jobs

## Common Endpoints

### Parse Document

```bash
maton api -X POST '/reducto/parse' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "document_url": "https://example.com/document.pdf"
}
EOF
```

### Parse Document (Async)

```bash
maton api -X POST '/reducto/parse_async' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "document_url": "https://example.com/document.pdf"
}
EOF
```

### Extract Data

```bash
maton api -X POST '/reducto/extract' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "document_url": "https://example.com/document.pdf",
  "schema": {
    "type": "object",
    "properties": {
      "title": {"type": "string"},
      "date": {"type": "string"}
    }
  }
}
EOF
```

### Split Document

```bash
maton api -X POST '/reducto/split' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "document_url": "https://example.com/document.pdf",
  "split_description": [
    {"name": "section1", "description": "First section"}
  ]
}
EOF
```

### Edit Document

```bash
maton api -X POST '/reducto/edit' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "document_url": "https://example.com/form.pdf",
  "edit_instructions": "Fill the name field with 'John Doe'"
}
EOF
```

### Upload File

```bash
maton api -X POST '/reducto/upload' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{}
EOF
```

### List Jobs

```bash
maton api '/reducto/jobs'
```

### Get Job Status

```bash
maton api '/reducto/job/{job_id}'
```

### Get Version

```bash
maton api '/reducto/version'
```

## Job Status Values

- `Pending`: Job is queued or processing
- `InProgress`: Job is actively processing
- `Completed`: Job finished successfully
- `Failed`: Job failed

## Document URL Formats

- Public URL: `https://example.com/document.pdf`
- Presigned S3: `https://bucket.s3.amazonaws.com/key?...`
- Upload result: `reducto://file-id`
- Previous job: `jobid://job-id`

## Resources

- [Reducto Documentation](https://docs.reducto.ai)
- [Reducto API Reference](https://docs.reducto.ai/api-reference)
- [Reducto Studio](https://studio.reducto.ai)
