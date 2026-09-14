# Reducto

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

> **⚠ Documents are sent to an external processor.** Reducto is a third-party service: every `document_url` you submit is fetched and read by Reducto, every file you upload is transmitted to and stored on its infrastructure, and the parsed text, tables, and extracted fields come back through its servers. The documents this is used on are rarely trivial — contracts, invoices, IDs, medical and financial records, HR files, signed forms — so the content, and whatever the extraction schema pulls out of it, is disclosed off-platform.
>
> - **Confirm the specific document and that the user accepts an external processor.** Say what is being uploaded and where it goes before doing it. Never submit a document the user did not name, and never batch a folder of them.
> - **A `document_url` discloses more than the file.** A signed S3 or Drive link, or any URL with a token in its query string, is a credential: handing it to Reducto lets that host fetch the object itself. Prefer an explicit upload of a file the user chose over passing a pre-signed URL.
> - **`schema` and `system_prompt` values are sent verbatim** and often describe exactly what the user is looking for — keep internal context and party names out of them where the extraction does not require it.
> - **Return the narrowest answer.** Extracted fields frequently contain personal data belonging to third parties (counterparties, patients, employees). Summarize rather than echoing whole documents, and do not forward results to another app or a trigger destination without approval for that transfer.
> - Uploaded files and job results persist in the user's Reducto account until deleted, and processing consumes paid credits.

**App name:** `reducto`
**Upstream base URL:** `platform.reducto.ai`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://platform.reducto.ai/parse`
- Gateway: `https://api.maton.ai/reducto/parse`

### Parse API

#### Parse Document

```bash
maton api -X POST '/reducto/parse' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "document_url": "https://example.com/document.pdf"
}
JSON
```

**Response:**
```json
{
  "job_id": "04b8aa38-7eb3-4151-98b0-dbaea71358d9",
  "duration": 17.85,
  "pdf_url": "https://...",
  "studio_link": "https://studio.reducto.ai/job/...",
  "usage": {
    "num_pages": 15,
    "credits": 15.0
  },
  "result": {
    "chunks": [
      {
        "content": "Extracted text content...",
        "blocks": [...]
      }
    ]
  }
}
```

#### Parse Document (Async)

For long documents, use async to avoid timeouts:

```bash
maton api -X POST '/reducto/parse_async' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "document_url": "https://example.com/document.pdf"
}
JSON
```

**Response:**
```json
{
  "job_id": "e234ba95-410a-4dd0-8a14-743dbfc49470"
}
```

Poll the job status with `GET /reducto/job/{job_id}`.

### Extract API

#### Extract Data

```bash
maton api -X POST '/reducto/extract' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "document_url": "https://example.com/document.pdf",
  "schema": {
    "type": "object",
    "properties": {
      "title": {"type": "string", "description": "The document title"},
      "authors": {"type": "array", "items": {"type": "string"}, "description": "List of author names"}
    }
  }
}
JSON
```

**Response:**
```json
{
  "job_id": "36f01a34-7ef6-40da-9e74-7c14902b6182",
  "usage": {
    "num_pages": 15,
    "num_fields": 9,
    "credits": 45.0
  },
  "studio_link": "https://studio.reducto.ai/job/...",
  "result": [
    {
      "title": "Document Title",
      "authors": ["Author One", "Author Two"]
    }
  ]
}
```

#### Asynchronous Extract

```bash
maton api -X POST '/reducto/extract_async' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "document_url": "https://example.com/document.pdf",
  "schema": {
    "type": "object",
    "properties": {
      "title": {"type": "string"}
    }
  }
}
JSON
```

**Response:**
```json
{
  "job_id": "0cdb6a50-df92-438b-875b-8b5c72d5b089"
}
```

### Split API

#### Split Document

```bash
maton api -X POST '/reducto/split' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "document_url": "https://example.com/document.pdf",
  "split_description": [
    {"name": "abstract", "description": "The abstract section"},
    {"name": "introduction", "description": "The introduction section"},
    {"name": "conclusion", "description": "The conclusion section"}
  ]
}
JSON
```

**Response:**
```json
{
  "usage": {
    "num_pages": 15,
    "credits": 15.0
  },
  "result": {
    "section_mapping": {
      "abstract": [1],
      "introduction": [1, 2],
      "conclusion": [14, 15]
    },
    "splits": [
      {
        "name": "abstract",
        "pages": [1],
        "conf": "high"
      }
    ]
  }
}
```

#### Asynchronous Split

```bash
maton api -X POST '/reducto/split_async' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "document_url": "https://example.com/document.pdf",
  "split_description": [
    {"name": "abstract", "description": "The abstract section"}
  ]
}
JSON
```

**Response:**
```json
{
  "job_id": "381de5fe-e162-4039-9ef9-8522fb34056b"
}
```

### Edit API

#### Edit Document

```bash
maton api -X POST '/reducto/edit' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "document_url": "https://example.com/form.pdf",
  "edit_instructions": "Fill in the name field with 'John Doe' and check the consent box"
}
JSON
```

**Response:**
```json
{
  "document_url": "https://presigned-url.s3.amazonaws.com/...",
  "form_schema": [...],
  "usage": {
    "num_pages": 2,
    "credits": 2.0
  }
}
```

#### Asynchronous Edit

```bash
maton api -X POST '/reducto/edit_async' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "document_url": "https://example.com/form.pdf",
  "edit_instructions": "Highlight all mentions of 'important' in red"
}
JSON
```

**Response:**
```json
{
  "job_id": "575189cb-8732-429a-ba8a-06de8ee03208"
}
```

### Upload API

#### Upload File

Upload a document to Reducto and get a presigned URL for processing.

```bash
maton api -X POST '/reducto/upload' -H 'Content-Type: application/json' --input - <<'JSON'
{}
JSON
```

**Response:**
```json
{
  "file_id": "reducto://18d574c7-4144-4f50-b7af-b8aba83ada5d",
  "presigned_url": "https://prod-storage.s3.amazonaws.com/...?AWSAccessKeyId=...&Signature=...&Expires=..."
}
```

Upload your file to the `presigned_url` using a PUT request, then use the `file_id` as `document_url` in parse/extract/split/edit requests.

### Pipeline API

#### Run Pipeline

Execute pre-configured processing pipelines.

```bash
maton api -X POST '/reducto/pipeline' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "input": "https://example.com/document.pdf",
  "pipeline_id": "your-pipeline-id"
}
JSON
```

**Note:** `pipeline_id` must be a valid pipeline ID configured in your Reducto account via the Reducto Studio.

**Response:**
```json
{
  "job_id": "...",
  "usage": {
    "num_pages": 15,
    "credits": 15.0
  },
  "result": {
    "parse": {...},
    "extract": {...},
    "split": {...},
    "edit": {...}
  }
}
```

### Jobs API

#### List Jobs

```bash
maton api '/reducto/jobs'
```

**Response:**
```json
{
  "jobs": [
    {
      "job_id": "8c25561f-247a-4843-b561-1eb94c3792d1",
      "status": "Completed",
      "type": "Parse",
      "created_at": "2026-02-27T23:11:39.787917",
      "num_pages": 15,
      "duration": 6.62
    }
  ],
  "next_cursor": null
}
```

#### Get Job Status

```bash
maton api '/reducto/job/{job_id}'
```

**Note:** `{job_id}` is a placeholder. Replace it with a real value before sending the request.

**Response (Pending):**
```json
{
  "status": "Pending",
  "result": null,
  "progress": 0.5,
  "reason": null
}
```

**Response (Completed):**
```json
{
  "status": "Completed",
  "result": {
    "job_id": "...",
    "duration": 17.85,
    "usage": {...},
    "result": {...}
  },
  "progress": null,
  "reason": null
}
```

Job status values: `Pending`, `InProgress`, `Completed`, `Failed`

### Version API

#### Get Version

```bash
maton api '/reducto/version'
```

**Response:**
```json
"VERSION_GOES_HERE"
```

### Job Status Values

- `Pending`: Job is queued or processing
- `InProgress`: Job is actively processing
- `Completed`: Job finished successfully
- `Failed`: Job failed

### Document URL Formats

- Public URL: `https://example.com/document.pdf`
- Presigned S3: `https://bucket.s3.amazonaws.com/key?...`
- Upload result: `reducto://file-id`
- Previous job: `jobid://job-id`

### Notes

- Connection uses API_KEY authentication method (not OAuth)
- Use async endpoints for large documents to avoid timeouts
- Upload presigned URLs expire quickly
- Use `reducto://` URLs from upload in subsequent requests
- Use `jobid://` to reuse parsed content from previous jobs

### Resources

- [Reducto Documentation](https://docs.reducto.ai)
- [Reducto API Reference](https://docs.reducto.ai/api-reference)
- [Reducto Studio](https://studio.reducto.ai)
- [Maton CLI Manual](https://cli.maton.ai/manual)
