# Airtable

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `airtable`
**Upstream base URL:** `api.airtable.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.airtable.com/v0/meta/bases`
- Gateway: `https://api.maton.ai/airtable/v0/meta/bases`

### Bases API

#### List Bases

```bash
maton api '/airtable/v0/meta/bases'
```

#### Get Base Schema

```bash
maton api '/airtable/v0/meta/bases/{baseId}/tables'
```

**Note:** `{baseId}` is a placeholder. Replace it with a real value before sending the request.

### Records API

#### List Records

```bash
maton api '/airtable/v0/{baseId}/{tableIdOrName}?maxRecords=100'
```

**Note:** `{baseId}` and `{tableIdOrName}` are placeholders. Replace each of them with real values before sending the request.

With view:
```bash
maton api '/airtable/v0/{baseId}/{tableIdOrName}?view=Grid%20view&maxRecords=100'
```

**Note:** `{baseId}` and `{tableIdOrName}` are placeholders. Replace each of them with real values before sending the request.

With filter formula:
```bash
maton api '/airtable/v0/{baseId}/{tableIdOrName}?filterByFormula=%7BStatus%7D%3D%27Active%27'
```

**Note:** `{baseId}` and `{tableIdOrName}` are placeholders. Replace each of them with real values before sending the request. The formula is `{Status}='Active'` URL-encoded — `maton api` does not encode for you, and an unencoded space returns `400`. `%7B`/`%7D` are Airtable's field-reference braces: replace `Status`, keep them.

**Example:** `{Deal Stage}='Active'` (space becomes `%20`):
```bash
maton api '/airtable/v0/appXXXXXXXXXXXXXX/Deals?filterByFormula=%7BDeal%20Stage%7D%3D%27Active%27'
```

With field selection:
```bash
maton api '/airtable/v0/{baseId}/{tableIdOrName}?fields[]=Name&fields[]=Status&fields[]=Email'
```

**Note:** `{baseId}` and `{tableIdOrName}` are placeholders. Replace each of them with real values before sending the request.

With sorting:
```bash
maton api '/airtable/v0/{baseId}/{tableIdOrName}?sort[0][field]=Created&sort[0][direction]=desc'
```

**Note:** `{baseId}` and `{tableIdOrName}` are placeholders. Replace each of them with real values before sending the request.

#### Get Record

```bash
maton api '/airtable/v0/{baseId}/{tableIdOrName}/{recordId}'
```

**Note:** `{baseId}`, `{tableIdOrName}` and `{recordId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Records

```bash
maton api -X POST '/airtable/v0/{baseId}/{tableIdOrName}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "records": [
    {
      "fields": {
        "Name": "New Record",
        "Status": "Active",
        "Email": "test@example.com"
      }
    }
  ]
}
JSON
```

**Note:** `{baseId}` and `{tableIdOrName}` are placeholders. Replace each of them with real values before sending the request.

#### Update Records (PATCH - partial update)

```bash
maton api -X PATCH '/airtable/v0/{baseId}/{tableIdOrName}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "records": [
    {
      "id": "recXXXXXXXXXXXXXX",
      "fields": {
        "Status": "Completed"
      }
    }
  ]
}
JSON
```

**Note:** `{baseId}` and `{tableIdOrName}` are placeholders. Replace each of them with real values before sending the request.

#### Update Records (PUT - full replace)

```bash
maton api -X PUT '/airtable/v0/{baseId}/{tableIdOrName}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "records": [
    {
      "id": "recXXXXXXXXXXXXXX",
      "fields": {
        "Name": "Updated Name",
        "Status": "Active"
      }
    }
  ]
}
JSON
```

**Note:** `{baseId}` and `{tableIdOrName}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Records

```bash
maton api '/airtable/v0/{baseId}/{tableIdOrName}?records[]=recXXXXX&records[]=recYYYYY' -X DELETE
```

**Note:** `{baseId}` and `{tableIdOrName}` are placeholders. Replace each of them with real values before sending the request.

### Pagination

**Query parameters:**
- `pageSize` - Number of records per request (max 100, default 100)
- `maxRecords` - Maximum total records across all pages
- `offset` - Cursor for next page (returned in response)

Response includes `offset` when more records exist:
```json
{
  "records": [...],
  "offset": "itrXXXXXXXXXXX"
}
```

Use offset for next page:
```bash
maton api '/airtable/v0/{baseId}/{tableIdOrName}?pageSize=50&offset=itrXXXXXXXXXXX'
```

**Note:** `{baseId}` and `{tableIdOrName}` are placeholders. Replace each of them with real values before sending the request.

### Notes

- Base IDs start with `app`
- Table IDs start with `tbl` (can also use table name)
- Record IDs start with `rec`
- Maximum 10 records per request for create/update/delete — batch larger sets into chunks of 10 or the request fails with 422
- Maximum `pageSize` of 100 records per list request
- Filter formulas use Airtable formula syntax

### Resources

- [Airtable API Overview](https://airtable.com/developers/web/api/introduction)
- [List Records](https://airtable.com/developers/web/api/list-records)
- [Create Records](https://airtable.com/developers/web/api/create-records)
- [Update Records](https://airtable.com/developers/web/api/update-record)
- [Delete Records](https://airtable.com/developers/web/api/delete-record)
- [Formula Reference](https://support.airtable.com/docs/formula-field-reference)
- [Maton CLI Manual](https://cli.maton.ai/manual)
