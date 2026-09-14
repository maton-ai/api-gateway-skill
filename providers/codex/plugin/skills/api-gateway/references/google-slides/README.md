# Google Slides

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `google-slides`
**Upstream base URL:** `slides.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://slides.googleapis.com/v1/presentations`
- Gateway: `https://api.maton.ai/google-slides/v1/presentations`

### Presentations API

#### Create Presentation

```bash
maton api -X POST '/google-slides/v1/presentations' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "My Presentation"
}
JSON
```

#### Get Presentation

```bash
maton api '/google-slides/v1/presentations/{presentationId}'
```

**Note:** `{presentationId}` is a placeholder. Replace it with a real value before sending the request.

### Pages API

#### Get Page

```bash
maton api '/google-slides/v1/presentations/{presentationId}/pages/{pageId}'
```

**Note:** `{presentationId}` and `{pageId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Page Thumbnail

```bash
maton api '/google-slides/v1/presentations/{presentationId}/pages/{pageId}/thumbnail'
```

**Note:** `{presentationId}` and `{pageId}` are placeholders. Replace each of them with real values before sending the request.

With custom size:

```bash
maton api '/google-slides/v1/presentations/{presentationId}/pages/{pageId}/thumbnail?thumbnailProperties.mimeType=PNG&thumbnailProperties.thumbnailSize=LARGE'
```

**Note:** `{presentationId}` and `{pageId}` are placeholders. Replace each of them with real values before sending the request.

### Batch Updates API

The batchUpdate endpoint is used for most modifications. It accepts an array of requests that are applied atomically.

```bash
maton api -X POST '/google-slides/v1/presentations/{presentationId}:batchUpdate' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "requests": [...]
}
JSON
```

**Note:** `{presentationId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Slide

```bash
maton api -X POST '/google-slides/v1/presentations/{presentationId}:batchUpdate' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "requests": [
    {
      "createSlide": {
        "objectId": "slide_001",
        "slideLayoutReference": {
          "predefinedLayout": "TITLE_AND_BODY"
        }
      }
    }
  ]
}
JSON
```

**Note:** `{presentationId}` is a placeholder. Replace it with a real value before sending the request.

Available predefined layouts:
- `BLANK`
- `TITLE`
- `TITLE_AND_BODY`
- `TITLE_AND_TWO_COLUMNS`
- `TITLE_ONLY`
- `SECTION_HEADER`
- `ONE_COLUMN_TEXT`
- `MAIN_POINT`
- `BIG_NUMBER`

#### Insert Text

```bash
maton api -X POST '/google-slides/v1/presentations/{presentationId}:batchUpdate' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "requests": [
    {
      "insertText": {
        "objectId": "{shapeId}",
        "text": "Hello, World!",
        "insertionIndex": 0
      }
    }
  ]
}
JSON
```

**Note:** `{presentationId}` and `{shapeId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Text

```bash
maton api -X POST '/google-slides/v1/presentations/{presentationId}:batchUpdate' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "requests": [
    {
      "deleteText": {
        "objectId": "{shapeId}",
        "textRange": {
          "type": "ALL"
        }
      }
    }
  ]
}
JSON
```

**Note:** `{presentationId}` and `{shapeId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Shape

```bash
maton api -X POST '/google-slides/v1/presentations/{presentationId}:batchUpdate' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "requests": [
    {
      "createShape": {
        "objectId": "shape_001",
        "shapeType": "TEXT_BOX",
        "elementProperties": {
          "pageObjectId": "{slideId}",
          "size": {
            "width": {"magnitude": 300, "unit": "PT"},
            "height": {"magnitude": 100, "unit": "PT"}
          },
          "transform": {
            "scaleX": 1,
            "scaleY": 1,
            "translateX": 100,
            "translateY": 100,
            "unit": "PT"
          }
        }
      }
    }
  ]
}
JSON
```

**Note:** `{presentationId}` and `{slideId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Image

```bash
maton api -X POST '/google-slides/v1/presentations/{presentationId}:batchUpdate' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "requests": [
    {
      "createImage": {
        "objectId": "image_001",
        "url": "https://example.com/image.png",
        "elementProperties": {
          "pageObjectId": "{slideId}",
          "size": {
            "width": {"magnitude": 200, "unit": "PT"},
            "height": {"magnitude": 200, "unit": "PT"}
          },
          "transform": {
            "scaleX": 1,
            "scaleY": 1,
            "translateX": 200,
            "translateY": 200,
            "unit": "PT"
          }
        }
      }
    }
  ]
}
JSON
```

**Note:** `{presentationId}` and `{slideId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Object

```bash
maton api -X POST '/google-slides/v1/presentations/{presentationId}:batchUpdate' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "requests": [
    {
      "deleteObject": {
        "objectId": "{objectId}"
      }
    }
  ]
}
JSON
```

**Note:** `{presentationId}` and `{objectId}` are placeholders. Replace each of them with real values before sending the request.

#### Update Text Style

```bash
maton api -X POST '/google-slides/v1/presentations/{presentationId}:batchUpdate' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "requests": [
    {
      "updateTextStyle": {
        "objectId": "{shapeId}",
        "textRange": {
          "type": "ALL"
        },
        "style": {
          "bold": true,
          "fontSize": {"magnitude": 24, "unit": "PT"},
          "foregroundColor": {
            "opaqueColor": {
              "rgbColor": {"red": 0.2, "green": 0.4, "blue": 0.8}
            }
          }
        },
        "fields": "bold,fontSize,foregroundColor"
      }
    }
  ]
}
JSON
```

**Note:** `{presentationId}` and `{shapeId}` are placeholders. Replace each of them with real values before sending the request.

#### Replace All Text

```bash
maton api -X POST '/google-slides/v1/presentations/{presentationId}:batchUpdate' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "requests": [
    {
      "replaceAllText": {
        "containsText": {
          "text": "{{placeholder}}",
          "matchCase": true
        },
        "replaceText": "Actual Value"
      }
    }
  ]
}
JSON
```

**Note:** `{presentationId}` is a placeholder. Replace it with a real value before sending the request.

### Notes

- Object IDs must be unique within a presentation
- Use batchUpdate for all modifications (adding slides, text, shapes, etc.)
- Multiple requests in a batchUpdate are applied atomically
- Sizes and positions use PT (points) as the unit (72 points = 1 inch)
- Use `replaceAllText` for template-based presentation generation

### Resources

- [Google Slides API Overview](https://developers.google.com/slides/api/reference/rest)
- [Presentations](https://developers.google.com/slides/api/reference/rest/v1/presentations)
- [Pages](https://developers.google.com/slides/api/reference/rest/v1/presentations.pages)
- [BatchUpdate Requests](https://developers.google.com/slides/api/reference/rest/v1/presentations/batchUpdate)
- [Page Layouts](https://developers.google.com/slides/api/reference/rest/v1/presentations/create#predefinedlayout)
- [Maton CLI Manual](https://cli.maton.ai/manual)
