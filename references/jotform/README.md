# JotForm Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `jotform`
**Base URL proxied:** `api.jotform.com`

## API Path Pattern

```
/jotform/{endpoint}
```

## Common Endpoints

### User

#### Get User Info
```bash
maton api '/jotform/user'
```

#### Get User Forms
```bash
maton api '/jotform/user/forms?limit=20&offset=0'
```

#### Get User Submissions
```bash
maton api '/jotform/user/submissions?limit=20&offset=0'
```

#### Get User Usage
```bash
maton api '/jotform/user/usage'
```

#### Get User History
```bash
maton api '/jotform/user/history?limit=20'
```

### Forms

#### Get Form
```bash
maton api '/jotform/form/{formId}'
```

#### Get Form Questions
```bash
maton api '/jotform/form/{formId}/questions'
```

#### Get Form Properties
```bash
maton api '/jotform/form/{formId}/properties'
```

#### Get Form Submissions
```bash
maton api '/jotform/form/{formId}/submissions?limit=20&offset=0'
```

With filter:
```bash
maton api '/jotform/form/{formId}/submissions?filter={"created_at:gt":"2024-01-01"}'
```

#### Get Form Files
```bash
maton api '/jotform/form/{formId}/files'
```

#### Create Form
```bash
maton api -X POST '/jotform/user/forms' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "properties": {
    "title": "Contact Form"
  },
  "questions": {
    "1": {
      "type": "control_textbox",
      "text": "Name",
      "name": "name"
    },
    "2": {
      "type": "control_email",
      "text": "Email",
      "name": "email"
    }
  }
}
EOF
```

#### Delete Form
```bash
maton api -X DELETE '/jotform/form/{formId}'
```

### Submissions

#### Get Submission
```bash
maton api '/jotform/submission/{submissionId}'
```

#### Update Submission
```bash
maton api -X POST '/jotform/submission/{submissionId}' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
submission[3][first]=John&submission[3][last]=Doe
EOF
```

Note: Use question IDs from the form questions endpoint. The submission field format is `submission[questionId][subfield]=value`.

#### Delete Submission
```bash
maton api -X DELETE '/jotform/submission/{submissionId}'
```

### Reports

#### Get Form Reports
```bash
maton api '/jotform/form/{formId}/reports'
```

### Webhooks

#### Get Form Webhooks
```bash
maton api '/jotform/form/{formId}/webhooks'
```

#### Create Webhook
```bash
maton api -X POST '/jotform/form/{formId}/webhooks' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
webhookURL=https://example.com/webhook
EOF
```

#### Delete Webhook
```bash
maton api -X DELETE '/jotform/form/{formId}/webhooks/{webhookIndex}'
```

## Question Types

- `control_textbox` - Single line text
- `control_textarea` - Multi-line text
- `control_email` - Email
- `control_phone` - Phone number
- `control_dropdown` - Dropdown
- `control_radio` - Radio buttons
- `control_checkbox` - Checkboxes
- `control_datetime` - Date/time picker
- `control_fileupload` - File upload
- `control_signature` - Signature

## Filter Syntax

Filters use JSON format:
- `{"field:gt":"value"}` - Greater than
- `{"field:lt":"value"}` - Less than
- `{"field:eq":"value"}` - Equal to
- `{"field:ne":"value"}` - Not equal to

## Notes

- Authentication is automatic - the router injects the `APIKEY` header
- Form IDs are numeric
- Submissions include all answers as key-value pairs
- Use `orderby` parameter to sort results (e.g., `orderby=created_at`)
- Pagination uses `limit` and `offset` parameters

## Resources

- [API Overview](https://api.jotform.com/docs/)
- [Get User Info](https://api.jotform.com/docs/#user)
- [Get User Forms](https://api.jotform.com/docs/#user-forms)
- [Get User Submissions](https://api.jotform.com/docs/#user-submissions)
- [Get Form Details](https://api.jotform.com/docs/#form-id)
- [Get Form Questions](https://api.jotform.com/docs/#form-id-questions)
- [Get Form Submissions](https://api.jotform.com/docs/#form-id-submissions)
- [Get Submission](https://api.jotform.com/docs/#submission-id)
- [Webhooks](https://api.jotform.com/docs/#form-id-webhooks)