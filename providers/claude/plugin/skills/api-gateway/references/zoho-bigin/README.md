# Zoho Bigin

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

> **Privacy — Contact and Deal records are personal data about real people.** Responses include names, email addresses, phone numbers, and call logs. This is regulated personal data (GDPR/CCPA), and these contacts are third parties who gave their details to the user's business, not to an agent.
> - Sample values below (`john@example.com`, `+1-555-1234`) are **placeholders**. Never send them to a live account, and never invent contact details to fill a required field — ask the user.
> - Retrieve only the records the task needs; do not page through modules to browse or build contact lists.
> - **Never forward Bigin contact data to a third-party host** without explicit user approval for that specific transfer.
> - Confirm the exact record by name or email (not just an ID) before any write, and never bulk-update or bulk-delete without per-record approval.

**App name:** `zoho-bigin`
**Upstream base URL:** `www.zohoapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://www.zohoapis.com/bigin/v2/Contacts`
- Gateway: `https://api.maton.ai/zoho-bigin/bigin/v2/Contacts`

### Records API

#### List Records

```bash
maton api '/zoho-bigin/bigin/v2/{module_api_name}?fields={field1},{field2}'
```

**Note:** `{module_api_name}`, `{field1}` and `{field2}` are placeholders. Replace each of them with real values before sending the request. `{module_api_name}` is one of `Contacts`, `Accounts`, `Products`, `Pipelines`, `Tasks`, `Events`, `Calls`, or a custom module's API name.

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `fields` | string | **Required.** Comma-separated field API names to retrieve (max 50 fields) |
| `sort_by` | string | Field API name to sort by |
| `sort_order` | string | `asc` or `desc`. Only applied when `sort_by` is also set |
| `page` | integer | Page number (default: 1) |
| `per_page` | integer | Records per page (default: 200, max: 200) |
| `approved` | string | Approval state to include: `true` (default), `false`, or `both` |
| `cvid` | string | Custom view ID for filtered results |
| `page_token` | string | Token from a previous response's `info.next_page_token`, used to page past the 2,000-record limit |

**Pagination:** `page`/`per_page` only reach the first 2,000 records. When the response's `info.more_records` is `true` and that limit is reached, pass `info.next_page_token` back as `page_token` (without `page`) to continue.

**Example - List Contacts:**

```bash
maton api '/zoho-bigin/bigin/v2/Contacts?fields=First_Name,Last_Name,Email,Phone'
```

**Response:**
```json
{
  "data": [
    {
      "First_Name": "Ted",
      "Email": "support@bigin.com",
      "Last_Name": "Watson",
      "id": "7255024000000596045"
    }
  ],
  "info": {
    "per_page": 200,
    "count": 1,
    "page": 1,
    "more_records": false
  }
}
```

**Example - List Companies (Accounts):**

```bash
maton api '/zoho-bigin/bigin/v2/Accounts?fields=Account_Name,Website'
```

#### Get Record

```bash
maton api '/zoho-bigin/bigin/v2/{module_api_name}/{record_id}'
```

**Note:** `{module_api_name}` and `{record_id}` are placeholders. Replace each of them with real values before sending the request.

**Example:**

```bash
maton api '/zoho-bigin/bigin/v2/Contacts/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Records

```bash
maton api -X POST '/zoho-bigin/bigin/v2/{module_api_name}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": [
    {
      "field_api_name": "value"
    }
  ]
}
JSON
```

**Note:** `{module_api_name}` is a placeholder. Replace it with a real value before sending the request.

**Mandatory Fields by Module:**

| Module | Required Fields |
|--------|-----------------|
| Contacts | `Last_Name` |
| Accounts | `Account_Name` |
| Pipelines | `Pipeline_Name`, `Stage` |
| Products | `Product_Name` |

**Example - Create Contact:**

```bash
maton api -X POST '/zoho-bigin/bigin/v2/Contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": [
    {
      "Last_Name": "Smith",
      "First_Name": "John",
      "Email": "john.smith@example.com",
      "Phone": "+1-555-0123"
    }
  ]
}
JSON
```

**Response:**
```json
{
  "data": [
    {
      "code": "SUCCESS",
      "details": {
        "Modified_Time": "2026-02-06T00:28:53-08:00",
        "Modified_By": {
          "name": "User Name",
          "id": "7255024000000590001"
        },
        "Created_Time": "2026-02-06T00:28:53-08:00",
        "id": "7255024000000605002",
        "Created_By": {
          "name": "User Name",
          "id": "7255024000000590001"
        }
      },
      "message": "record added",
      "status": "success"
    }
  ]
}
```

**Example - Create Company (Account):**

```bash
maton api -X POST '/zoho-bigin/bigin/v2/Accounts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": [
    {
      "Account_Name": "Acme Corporation",
      "Website": "https://acme.com"
    }
  ]
}
JSON
```

#### Update Records

```bash
maton api -X PUT '/zoho-bigin/bigin/v2/{module_api_name}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": [
    {
      "id": "record_id",
      "field_api_name": "updated_value"
    }
  ]
}
JSON
```

**Note:** `{module_api_name}` is a placeholder. Replace it with a real value before sending the request.

**Example:**

```bash
maton api -X PUT '/zoho-bigin/bigin/v2/Contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": [
    {
      "id": "7255024000000605002",
      "Phone": "+1-555-9999"
    }
  ]
}
JSON
```

**Response:**
```json
{
  "data": [
    {
      "code": "SUCCESS",
      "details": {
        "Modified_Time": "2026-02-06T00:29:07-08:00",
        "id": "7255024000000605002"
      },
      "message": "record updated",
      "status": "success"
    }
  ]
}
```

#### Delete Records

```bash
maton api '/zoho-bigin/bigin/v2/{module_api_name}?ids={record_id1},{record_id2}' -X DELETE
```

**Note:** `{module_api_name}`, `{record_id1}` and `{record_id2}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `ids` | string | Comma-separated record IDs (required, max 100) |
| `wf_trigger` | boolean | Execute workflows (default: true) |

**Example:**

```bash
maton api '/zoho-bigin/bigin/v2/Contacts?ids=7255024000000605002' -X DELETE
```

**Response:**
```json
{
  "data": [
    {
      "code": "SUCCESS",
      "details": {
        "id": "7255024000000605002"
      },
      "message": "record deleted",
      "status": "success"
    }
  ]
}
```

#### Search Records

```bash
maton api '/zoho-bigin/bigin/v2/{module_api_name}/search'
```

**Note:** `{module_api_name}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `criteria` | string | Search criteria (e.g., `(Last_Name:equals:Smith)`) |
| `email` | string | Search by email address |
| `phone` | string | Search by phone number |
| `word` | string | Global text search |
| `page` | integer | Page number |
| `per_page` | integer | Records per page (max 200) |

**Criteria Format:** `((field_api_name:operator:value)and/or(...))`

**Operators:** `equals`, `starts_with`

**Example - Search by email:**

```bash
maton api '/zoho-bigin/bigin/v2/Contacts/search?email=support@bigin.com'
```

**Example - Search by criteria:**

```bash
maton api '/zoho-bigin/bigin/v2/Contacts/search?criteria={criteria}'
```

**Note:** `{criteria}` is a placeholder. Replace it with a real value before sending the request.

### Modules API

#### Get Modules

```bash
maton api '/zoho-bigin/bigin/v2/settings/modules'
```

### Users API

#### Get Users

```bash
maton api '/zoho-bigin/bigin/v2/users'
```

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `type` | string | `AllUsers`, `ActiveUsers`, `AdminUsers`, `CurrentUser` |
| `page` | integer | Page number |
| `per_page` | integer | Users per page (max 200) |

**Example:**

```bash
maton api '/zoho-bigin/bigin/v2/users?type=ActiveUsers'
```

### Available Modules

| Module | API Name | Description |
|--------|----------|-------------|
| Contacts | `Contacts` | Individual people |
| Companies | `Accounts` | Organizations/businesses |
| Pipelines | `Pipelines` | Sales opportunities/deals |
| Products | `Products` | Items you sell |
| Tasks | `Tasks` | To-do items |
| Events | `Events` | Calendar appointments |
| Calls | `Calls` | Phone call logs |
| Notes | `Notes` | Notes attached to records |

### Notes

- The `fields` query parameter is **required** for list operations
- Module API names are case-sensitive (e.g., `Contacts`, not `contacts`)
- Companies are accessed via the `Accounts` module
- Sales opportunities are in the `Pipelines` module (not `Deals`)
- Record IDs are numeric strings (e.g., `7255024000000596045`)
- Maximum 200 records per page, 100 per create/update/delete
- Some modules (Tasks, Events, Calls, Notes) require additional OAuth scopes

### Resources

- [Zoho Bigin API Overview](https://www.bigin.com/developer/docs/apis/v2/)
- [Zoho Bigin REST API Documentation](https://www.bigin.com/developer/docs/apis/)
- [Modules API](https://www.bigin.com/developer/docs/apis/modules-api.html)
- [Maton CLI Manual](https://cli.maton.ai/manual)
