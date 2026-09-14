# Microsoft Excel

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `microsoft-excel`
**Upstream base URL:** `graph.microsoft.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://graph.microsoft.com/v1.0/me/drive`
- Gateway: `https://api.maton.ai/microsoft-excel/v1.0/me/drive`

### Drive API

#### Get Drive Info

```bash
maton api '/microsoft-excel/v1.0/me/drive'
```

#### List Root Files

```bash
maton api '/microsoft-excel/v1.0/me/drive/root/children'
```

#### Search Files

```bash
maton api "/microsoft-excel/v1.0/me/drive/root/search(q='.xlsx')"
```

#### Upload Excel File

```bash
maton api -X PUT '/microsoft-excel/v1.0/me/drive/root:/{filename}.xlsx:/content' \
  -H 'Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{binary xlsx content}
EOF
```

**Note:** `{filename}` is a placeholder. Replace it with a real value before sending the request.

### Sessions API

#### Create Session

```bash
maton api -X POST '/microsoft-excel/v1.0/me/drive/root:/{path}:/workbook/createSession' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "persistChanges": true
}
JSON
```

**Note:** `{path}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "persistChanges": true,
  "id": "cluster=PUS7&session=..."
}
```

Use the session ID in subsequent requests:
```
workbook-session-id: {session-id}
```

#### Close Session

```bash
maton api -X POST '/microsoft-excel/v1.0/me/drive/root:/{path}:/workbook/closeSession' -H 'workbook-session-id: {session-id}'
```

**Note:** `{path}` and `{session-id}` are placeholders. Replace each of them with real values before sending the request.

### Worksheets API

#### List Worksheets

```bash
maton api '/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets'
```

**Response:**
```json
{
  "value": [
    {
      "id": "{00000000-0001-0000-0000-000000000000}",
      "name": "Sheet1",
      "position": 0,
      "visibility": "Visible"
    }
  ]
}
```

#### Create Worksheet

```bash
maton api -X POST '/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "NewSheet"
}
JSON
```

### Ranges API

#### Get Worksheet

```bash
maton api "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets('Sheet1')"
```

#### Update Worksheet

```bash
maton api -X PATCH "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets('Sheet1')" -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "RenamedSheet",
  "position": 2
}
JSON
```

#### Get Range

```bash
maton api "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets('Sheet1')/range(address='A1:B2')"
```

**Response:**
```json
{
  "address": "Sheet1!A1:B2",
  "values": [
    ["Hello", "World"],
    [1, 2]
  ],
  "formulas": [
    ["Hello", "World"],
    [1, 2]
  ],
  "text": [
    ["Hello", "World"],
    ["1", "2"]
  ],
  "numberFormat": [
    ["General", "General"],
    ["General", "General"]
  ],
  "rowCount": 2,
  "columnCount": 2
}
```

#### Get Used Range

```bash
maton api "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets('Sheet1')/usedRange"
```

#### Update Range

```bash
maton api -X PATCH "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets('Sheet1')/range(address='A1:B2')" -H 'Content-Type: application/json' --input - <<'JSON'
{
  "values": [
    ["Updated", "Values"],
    [100, 200]
  ]
}
JSON
```

#### Clear Range

```bash
maton api -X POST "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets('Sheet1')/range(address='A1:B2')/clear" -H 'Content-Type: application/json' --input - <<'JSON'
{
  "applyTo": "All"
}
JSON
```

Options: `All`, `Formats`, `Contents`

### Tables API

#### Delete Worksheet

```bash
maton api "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets('{worksheet-id}')" -X DELETE
```

**Note:** `{worksheet-id}` is a placeholder. Replace it with a real value before sending the request.

Returns 204 No Content on success.

#### Create Table

```bash
maton api -X POST "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets('Sheet1')/tables/add" -H 'Content-Type: application/json' --input - <<'JSON'
{
  "address": "A1:C4",
  "hasHeaders": true
}
JSON
```

**Response:**
```json
{
  "id": "{6D182180-5F5F-448B-9E9C-377A5251CFC5}",
  "name": "Table1",
  "showHeaders": true,
  "showTotals": false,
  "style": "TableStyleMedium2"
}
```

#### List Tables

```bash
maton api "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets('Sheet1')/tables"
```

#### Get Table

```bash
maton api "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/tables('Table1')"
```

#### Update Table

```bash
maton api -X PATCH "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/tables('Table1')" -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "PeopleTable",
  "showTotals": true
}
JSON
```

#### Get Table Rows

```bash
maton api "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/tables('Table1')/rows"
```

**Response:**
```json
{
  "value": [
    {
      "index": 0,
      "values": [["Alice", 30, "NYC"]]
    },
    {
      "index": 1,
      "values": [["Bob", 25, "LA"]]
    }
  ]
}
```

#### Add Table Row

```bash
maton api -X POST "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/tables('Table1')/rows" -H 'Content-Type: application/json' --input - <<'JSON'
{
  "values": [["Carol", 35, "Chicago"]]
}
JSON
```

#### Delete Table Row

```bash
maton api "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/tables('Table1')/rows/itemAt(index=0)" -X DELETE
```

Returns 204 No Content on success.

#### Get Table Columns

```bash
maton api "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/tables('Table1')/columns"
```

#### Add Table Column

```bash
maton api -X POST "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/tables('Table1')/columns" -H 'Content-Type: application/json' --input - <<'JSON'
{
  "values": [["Email"], ["alice@example.com"], ["bob@example.com"]]
}
JSON
```

### Named Items API

#### List Named Items

```bash
maton api '/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/names'
```

### Charts API

#### List Charts

```bash
maton api "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets('Sheet1')/charts"
```

#### Add Chart

```bash
maton api -X POST "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets('Sheet1')/charts/add" -H 'Content-Type: application/json' --input - <<'JSON'
{
  "type": "ColumnClustered",
  "sourceData": "A1:C4",
  "seriesBy": "Auto"
}
JSON
```

### Notes

- Only `.xlsx` files are supported (not legacy `.xls`)
- Use path-based access (`/drive/root:/{path}:`) or ID-based access (`/drive/items/{id}`)
- Table/worksheet IDs with `{` and `}` must be URL-encoded
- Sessions improve performance for multiple operations
- Sessions expire after ~5 minutes (persistent) or ~7 minutes (non-persistent)
- Range addresses use A1 notation

### Resources

- [Microsoft Graph Excel API](https://learn.microsoft.com/en-us/graph/api/resources/excel)
- [Workbook Resource](https://learn.microsoft.com/en-us/graph/api/resources/workbook)
- [Worksheet Resource](https://learn.microsoft.com/en-us/graph/api/resources/worksheet)
- [Range Resource](https://learn.microsoft.com/en-us/graph/api/resources/range)
- [Maton CLI Manual](https://cli.maton.ai/manual)
