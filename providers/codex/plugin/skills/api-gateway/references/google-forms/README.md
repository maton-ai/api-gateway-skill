# Google Forms

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `google-forms`
**Upstream base URL:** `forms.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://forms.googleapis.com/v1/forms`
- Gateway: `https://api.maton.ai/google-forms/v1/forms`

### Forms API

#### Get Form

```bash
maton api '/google-forms/v1/forms/{formId}'
```

**Note:** `{formId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Form

```bash
maton api -X POST '/google-forms/v1/forms' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "info": {
    "title": "Customer Feedback Survey"
  }
}
JSON
```

#### Batch Update Form

```bash
maton api -X POST '/google-forms/v1/forms/{formId}:batchUpdate' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "requests": [
    {
      "createItem": {
        "item": {
          "title": "What is your name?",
          "questionItem": {
            "question": {
              "required": true,
              "textQuestion": {"paragraph": false}
            }
          }
        },
        "location": {"index": 0}
      }
    }
  ]
}
JSON
```

**Note:** `{formId}` is a placeholder. Replace it with a real value before sending the request.

#### List Responses

```bash
maton api '/google-forms/v1/forms/{formId}/responses'
```

**Note:** `{formId}` is a placeholder. Replace it with a real value before sending the request.

#### Get Response

```bash
maton api '/google-forms/v1/forms/{formId}/responses/{responseId}'
```

**Note:** `{formId}` and `{responseId}` are placeholders. Replace each of them with real values before sending the request.

### Common Requests for batchUpdate

#### Create Text Question

```json
{
  "createItem": {
    "item": {
      "title": "Question text",
      "questionItem": {
        "question": {
          "required": true,
          "textQuestion": {"paragraph": false}
        }
      }
    },
    "location": {"index": 0}
  }
}
```

#### Create Multiple Choice Question

```json
{
  "createItem": {
    "item": {
      "title": "Select an option",
      "questionItem": {
        "question": {
          "required": true,
          "choiceQuestion": {
            "type": "RADIO",
            "options": [
              {"value": "Option A"},
              {"value": "Option B"},
              {"value": "Option C"}
            ]
          }
        }
      }
    },
    "location": {"index": 0}
  }
}
```

#### Create Checkbox Question

```json
{
  "createItem": {
    "item": {
      "title": "Select all that apply",
      "questionItem": {
        "question": {
          "choiceQuestion": {
            "type": "CHECKBOX",
            "options": [
              {"value": "Option 1"},
              {"value": "Option 2"}
            ]
          }
        }
      }
    },
    "location": {"index": 0}
  }
}
```

#### Create Scale Question

```json
{
  "createItem": {
    "item": {
      "title": "Rate your experience",
      "questionItem": {
        "question": {
          "scaleQuestion": {
            "low": 1,
            "high": 5,
            "lowLabel": "Poor",
            "highLabel": "Excellent"
          }
        }
      }
    },
    "location": {"index": 0}
  }
}
```

#### Update Form Info

```json
{
  "updateFormInfo": {
    "info": {
      "title": "New Form Title",
      "description": "Form description"
    },
    "updateMask": "title,description"
  }
}
```

#### Delete Item

```json
{
  "deleteItem": {
    "location": {"index": 0}
  }
}
```

### Question Types

- `textQuestion` - Short or paragraph text
- `choiceQuestion` - Radio, checkbox, or dropdown
- `scaleQuestion` - Linear scale
- `dateQuestion` - Date picker
- `timeQuestion` - Time picker
- `fileUploadQuestion` - File upload

### Notes

- Form IDs can be found in the form URL
- Responses include `answers` keyed by question ID
- Use `updateMask` to specify which fields to update
- Location index is 0-based for item positioning

### Resources

- [Google Forms API Overview](https://developers.google.com/workspace/forms/api/reference/rest)
- [Get Form](https://developers.google.com/workspace/forms/api/reference/rest/v1/forms/get)
- [Create Form](https://developers.google.com/workspace/forms/api/reference/rest/v1/forms/create)
- [Batch Update Form](https://developers.google.com/workspace/forms/api/reference/rest/v1/forms/batchUpdate)
- [Batch Update Request Types](https://developers.google.com/workspace/forms/api/reference/rest/v1/forms/batchUpdate#request)
- [List Responses](https://developers.google.com/workspace/forms/api/reference/rest/v1/forms.responses/list)
- [Get Response](https://developers.google.com/workspace/forms/api/reference/rest/v1/forms.responses/get)
- [Form Resource](https://developers.google.com/workspace/forms/api/reference/rest/v1/forms)
- [Maton CLI Manual](https://cli.maton.ai/manual)
