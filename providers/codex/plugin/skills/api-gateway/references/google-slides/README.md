# Google Slides Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `google-slides`
**Base URL proxied:** `slides.googleapis.com`

## API Path Pattern

```
/google-slides/v1/presentations/{presentationId}
```

## Common Endpoints

### Create Presentation
```bash
maton api -X POST '/google-slides/v1/presentations' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "My Presentation"
}
EOF
```

### Get Presentation
```bash
maton api '/google-slides/v1/presentations/{presentationId}'
```

### Get Page (Slide)
```bash
maton api '/google-slides/v1/presentations/{presentationId}/pages/{pageId}'
```

### Get Page Thumbnail
```bash
maton api '/google-slides/v1/presentations/{presentationId}/pages/{pageId}/thumbnail'
```

### Batch Update (All Modifications)
```bash
maton api -X POST '/google-slides/v1/presentations/{presentationId}:batchUpdate' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "requests": [...]
}
EOF
```

### Create Slide
```bash
maton api -X POST '/google-slides/v1/presentations/{presentationId}:batchUpdate' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
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
EOF
```

Predefined layouts: `BLANK`, `TITLE`, `TITLE_AND_BODY`, `TITLE_AND_TWO_COLUMNS`, `TITLE_ONLY`, `SECTION_HEADER`, `ONE_COLUMN_TEXT`, `MAIN_POINT`, `BIG_NUMBER`

### Insert Text
```bash
maton api -X POST '/google-slides/v1/presentations/{presentationId}:batchUpdate' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
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
EOF
```

### Create Shape (Text Box)
```bash
maton api -X POST '/google-slides/v1/presentations/{presentationId}:batchUpdate' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
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
EOF
```

### Create Image
```bash
maton api -X POST '/google-slides/v1/presentations/{presentationId}:batchUpdate' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
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
          }
        }
      }
    }
  ]
}
EOF
```

### Delete Object
```bash
maton api -X POST '/google-slides/v1/presentations/{presentationId}:batchUpdate' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "requests": [
    {
      "deleteObject": {
        "objectId": "{objectId}"
      }
    }
  ]
}
EOF
```

### Replace All Text (Template Substitution)
```bash
maton api -X POST '/google-slides/v1/presentations/{presentationId}:batchUpdate' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
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
EOF
```

### Update Text Style
```bash
maton api -X POST '/google-slides/v1/presentations/{presentationId}:batchUpdate' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "requests": [
    {
      "updateTextStyle": {
        "objectId": "{shapeId}",
        "textRange": {"type": "ALL"},
        "style": {
          "bold": true,
          "fontSize": {"magnitude": 24, "unit": "PT"}
        },
        "fields": "bold,fontSize"
      }
    }
  ]
}
EOF
```

## Notes

- Object IDs must be unique within a presentation
- Use batchUpdate for all modifications (adding slides, text, shapes, etc.)
- Multiple requests in a batchUpdate are applied atomically
- Sizes and positions use PT (points) as the unit (72 points = 1 inch)
- Use `replaceAllText` for template-based presentation generation

## Resources

- [Slides API Overview](https://developers.google.com/slides/api/reference/rest)
- [Presentations](https://developers.google.com/slides/api/reference/rest/v1/presentations)
- [Pages](https://developers.google.com/slides/api/reference/rest/v1/presentations.pages)
- [BatchUpdate Requests](https://developers.google.com/slides/api/reference/rest/v1/presentations/batchUpdate)
- [Page Layouts](https://developers.google.com/slides/api/reference/rest/v1/presentations/create#predefinedlayout)
