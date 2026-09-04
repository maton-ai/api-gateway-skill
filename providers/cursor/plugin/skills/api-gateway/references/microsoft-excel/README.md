# Microsoft Excel Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `microsoft-excel`
**Base URL proxied:** `graph.microsoft.com`

## API Path Pattern

```
/microsoft-excel/v1.0/me/drive/items/{file-id}/workbook/{resource}
/microsoft-excel/v1.0/me/drive/root:/{path}:/workbook/{resource}
```

## Common Endpoints

### Drive Operations

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

### Session Management

#### Create Session
```bash
maton api -X POST '/microsoft-excel/v1.0/me/drive/root:/{path}:/workbook/createSession' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "persistChanges": true
}
EOF
```

### Worksheet Operations

#### List Worksheets
```bash
maton api '/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets'
```

#### Create Worksheet
```bash
maton api -X POST '/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "NewSheet"
}
EOF
```

#### Delete Worksheet
```bash
maton api -X DELETE "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets('{id}')"
```

### Range Operations

#### Get Range
```bash
maton api "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets('Sheet1')/range(address='A1:B2')"
```

#### Update Range
```bash
maton api -X PATCH "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets('Sheet1')/range(address='A1:B2')" \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "values": [
    ["Value1", "Value2"],
    [100, 200]
  ]
}
EOF
```

#### Get Used Range
```bash
maton api "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets('Sheet1')/usedRange"
```

### Table Operations

#### List Tables
```bash
maton api "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets('Sheet1')/tables"
```

#### Create Table
```bash
maton api -X POST "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets('Sheet1')/tables/add" \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "address": "A1:C4",
  "hasHeaders": true
}
EOF
```

#### Get Table Rows
```bash
maton api "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/tables('Table1')/rows"
```

#### Add Table Row
```bash
maton api -X POST "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/tables('Table1')/rows" \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "values": [["Data1", "Data2", "Data3"]]
}
EOF
```

#### Delete Table Row
```bash
maton api -X DELETE "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/tables('Table1')/rows/itemAt(index=0)"
```

#### Get Table Columns
```bash
maton api "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/tables('Table1')/columns"
```

### Named Items

#### List Named Items
```bash
maton api '/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/names'
```

### Charts

#### List Charts
```bash
maton api "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets('Sheet1')/charts"
```

#### Add Chart
```bash
maton api -X POST "/microsoft-excel/v1.0/me/drive/root:/workbook.xlsx:/workbook/worksheets('Sheet1')/charts/add" \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "type": "ColumnClustered",
  "sourceData": "A1:C4",
  "seriesBy": "Auto"
}
EOF
```

## Notes

- Only `.xlsx` files are supported (not legacy `.xls`)
- Use path-based access (`/drive/root:/{path}:`) or ID-based access (`/drive/items/{id}`)
- Table/worksheet IDs with `{` and `}` must be URL-encoded
- Sessions improve performance for multiple operations
- Sessions expire after ~5 minutes (persistent) or ~7 minutes (non-persistent)
- Range addresses use A1 notation

## Resources

- [Microsoft Graph Excel API](https://learn.microsoft.com/en-us/graph/api/resources/excel)
- [Excel Workbook Resource](https://learn.microsoft.com/en-us/graph/api/resources/workbook)
- [Excel Worksheet Resource](https://learn.microsoft.com/en-us/graph/api/resources/worksheet)
- [Excel Range Resource](https://learn.microsoft.com/en-us/graph/api/resources/range)
