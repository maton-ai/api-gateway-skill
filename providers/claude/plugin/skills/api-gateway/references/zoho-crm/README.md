# Zoho CRM

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `zoho-crm`
**Upstream base URL:** `www.zohoapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://www.zohoapis.com/crm/v8/Leads/7243485000000597000`
- Gateway: `https://api.maton.ai/zoho-crm/crm/v8/Leads/7243485000000597000`

### Records API

#### List Records

```bash
maton api '/zoho-crm/crm/v8/{module_api_name}?fields={field1},{field2}'
```

**Note:** `{module_api_name}`, `{field1}` and `{field2}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `fields` | string | **Required.** Comma-separated field API names (max 50) |
| `page` | integer | Page number (default: 1) |
| `per_page` | integer | Records per page (default/max: 200) |
| `sort_by` | string | Sort by: `id`, `Created_Time`, or `Modified_Time` |
| `sort_order` | string | `asc` or `desc` (default) |
| `cvid` | long | Custom view ID |
| `page_token` | string | For >2000 records pagination |

**Example - List Leads:**

```bash
maton api '/zoho-crm/crm/v8/Leads?fields=First_Name,Last_Name,Email,Phone,Company'
```

**Response:**
```json
{
  "data": [
    {
      "First_Name": "Christopher",
      "Email": "christopher-maclead@noemail.invalid",
      "Last_Name": "Maclead (Sample)",
      "Phone": "555-555-5555",
      "Company": "Rangoni Of Florence",
      "id": "7243485000000597000"
    }
  ],
  "info": {
    "per_page": 200,
    "count": 1,
    "page": 1,
    "sort_by": "id",
    "sort_order": "desc",
    "more_records": false,
    "next_page_token": null
  }
}
```

**Example - List Contacts:**

```bash
maton api '/zoho-crm/crm/v8/Contacts?fields=First_Name,Last_Name,Email,Phone'
```

**Example - List Accounts:**

```bash
maton api '/zoho-crm/crm/v8/Accounts?fields=Account_Name,Website,Phone'
```

**Example - List Deals:**

```bash
maton api '/zoho-crm/crm/v8/Deals?fields=Deal_Name,Stage,Amount'
```

#### Get Record

```bash
maton api '/zoho-crm/crm/v8/{module_api_name}/{record_id}'
```

**Note:** `{module_api_name}` and `{record_id}` are placeholders. Replace each of them with real values before sending the request.

**Example:**

```bash
maton api '/zoho-crm/crm/v8/Leads/7243485000000597000'
```

#### Create Records

```bash
maton api -X POST '/zoho-crm/crm/v8/{module_api_name}' -H 'Content-Type: application/json' --input - <<'JSON'
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
| Leads | `Last_Name` |
| Contacts | `Last_Name` |
| Accounts | `Account_Name` |
| Deals | `Deal_Name`, `Stage` |
| Tasks | `Subject` |
| Calls | `Subject`, `Call_Type`, `Call_Start_Time`, `Call_Duration` |
| Events | `Event_Title`, `Start_DateTime`, `End_DateTime` |

**Example - Create Lead:**

```bash
maton api -X POST '/zoho-crm/crm/v8/Leads' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": [
    {
      "Last_Name": "Smith",
      "First_Name": "John",
      "Email": "john.smith@example.com",
      "Company": "Acme Corp",
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
        "Modified_Time": "2026-02-06T01:10:56-08:00",
        "Modified_By": {
          "name": "User Name",
          "id": "7243485000000590001"
        },
        "Created_Time": "2026-02-06T01:10:56-08:00",
        "id": "7243485000000619001",
        "Created_By": {
          "name": "User Name",
          "id": "7243485000000590001"
        }
      },
      "message": "record added",
      "status": "success"
    }
  ]
}
```

**Example - Create Contact:**

```bash
maton api -X POST '/zoho-crm/crm/v8/Contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": [
    {
      "Last_Name": "Doe",
      "First_Name": "Jane",
      "Email": "jane.doe@example.com",
      "Phone": "+1-555-9876"
    }
  ]
}
JSON
```

**Example - Create Account:**

```bash
maton api -X POST '/zoho-crm/crm/v8/Accounts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": [
    {
      "Account_Name": "Acme Corporation",
      "Website": "https://acme.com",
      "Phone": "+1-555-1234"
    }
  ]
}
JSON
```

#### Update Records

```bash
maton api -X PUT '/zoho-crm/crm/v8/{module_api_name}' -H 'Content-Type: application/json' --input - <<'JSON'
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
maton api -X PUT '/zoho-crm/crm/v8/Leads' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": [
    {
      "id": "7243485000000619001",
      "Phone": "+1-555-9999",
      "Company": "Updated Company Name"
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
        "Modified_Time": "2026-02-06T01:11:01-08:00",
        "Modified_By": {
          "name": "User Name",
          "id": "7243485000000590001"
        },
        "Created_Time": "2026-02-06T01:10:56-08:00",
        "id": "7243485000000619001",
        "Created_By": {
          "name": "User Name",
          "id": "7243485000000590001"
        }
      },
      "message": "record updated",
      "status": "success"
    }
  ]
}
```

#### Delete Records

```bash
maton api '/zoho-crm/crm/v8/{module_api_name}?ids={record_id1},{record_id2}' -X DELETE
```

**Note:** `{module_api_name}`, `{record_id1}` and `{record_id2}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `ids` | string | Comma-separated record IDs (required, max 100) |
| `wf_trigger` | boolean | Execute workflows (default: true) |

**Example:**

```bash
maton api '/zoho-crm/crm/v8/Leads?ids=7243485000000619001' -X DELETE
```

**Response:**
```json
{
  "data": [
    {
      "code": "SUCCESS",
      "details": {
        "id": "7243485000000619001"
      },
      "message": "record deleted",
      "status": "success"
    }
  ]
}
```

#### List Users

Retrieve users in your Zoho CRM organization.

```bash
maton api '/zoho-crm/crm/v8/users'
```

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `type` | string | Filter by user type: `AllUsers`, `ActiveUsers`, `DeactiveUsers`, `ConfirmedUsers`, `NotConfirmedUsers`, `DeletedUsers`, `ActiveConfirmedUsers`, `AdminUsers`, `ActiveConfirmedAdmins`, `CurrentUser` |
| `page` | integer | Page number (default: 1) |
| `per_page` | integer | Records per page (default/max: 200) |
| `ids` | string | Comma-separated user IDs (max 100) |

**Example - List all users:**

```bash
maton api '/zoho-crm/crm/v8/users?type=AllUsers'
```

**Response:**
```json
{
  "users": [
    {
      "id": "7243485000000590001",
      "first_name": "John",
      "last_name": "Doe",
      "full_name": "John Doe",
      "email": "john.doe@example.com",
      "status": "active",
      "confirm": true,
      "role": {
        "name": "CEO",
        "id": "7243485000000026005"
      },
      "profile": {
        "name": "Administrator",
        "id": "7243485000000026011"
      },
      "time_zone": "PST",
      "country": "US",
      "locale": "en_US"
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

**Example - Get specific user:**

```bash
maton api '/zoho-crm/crm/v8/users/{user_id}'
```

**Note:** `{user_id}` is a placeholder. Replace it with a real value before sending the request.

### Search API

#### Search Records

```bash
maton api '/zoho-crm/crm/v8/{module_api_name}/search'
```

**Note:** `{module_api_name}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters (one required):**

| Parameter | Type | Description |
|-----------|------|-------------|
| `criteria` | string | Search criteria (e.g., `(Last_Name:equals:Smith)`) |
| `email` | string | Search by email address |
| `phone` | string | Search by phone number |
| `word` | string | Global text search |
| `page` | integer | Page number |
| `per_page` | integer | Records per page (max 200) |

**Criteria Format:** `((field_api_name:operator:value) and/or (...))`

**Operators:**
- Text fields: `equals`, `not_equal`, `starts_with`, `in`
- Date/Number fields: `equals`, `not_equal`, `greater_than`, `less_than`, `between`, `in`
- Boolean fields: `equals`, `not_equal`

**Examples:**

```bash
# Search by criteria
maton api '/zoho-crm/crm/v8/{module_api_name}/search?criteria={criteria}'
maton api '/zoho-crm/crm/v8/{module_api_name}/search?criteria=(Last_Name:equals:Smith)'
maton api '/zoho-crm/crm/v8/Leads/search?criteria=(Last_Name:equals:Smith)'

# Search by email
maton api '/zoho-crm/crm/v8/{module_api_name}/search?email=user@example.com'
maton api '/zoho-crm/crm/v8/Leads/search?email=christopher-maclead@noemail.invalid'

# Search by phone
maton api '/zoho-crm/crm/v8/{module_api_name}/search?phone=555-1234'

# Global text search
maton api '/zoho-crm/crm/v8/{module_api_name}/search?word=searchterm'
```

**Note:** `{module_api_name}` and `{criteria}` are placeholders. Replace each of them with real values before sending the request.

**Response:**
```json
{
  "data": [
    {
      "First_Name": "Christopher",
      "Email": "christopher-maclead@noemail.invalid",
      "Last_Name": "Maclead (Sample)",
      "id": "7243485000000597000"
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

### Organization API

#### Get Organization

Retrieve your Zoho CRM organization details.

```bash
maton api '/zoho-crm/crm/v8/org'
```

**Response:**
```json
{
  "org": [
    {
      "id": "7243485000000020005",
      "company_name": "Acme Corp",
      "domain_name": "org123456789",
      "primary_email": "admin@example.com",
      "phone": "555-555-5555",
      "currency": "US Dollar - USD",
      "currency_symbol": "$",
      "iso_code": "USD",
      "time_zone": "PST",
      "country_code": "US",
      "zgid": "123456789",
      "type": "production",
      "mc_status": false,
      "license_details": {
        "paid": true,
        "paid_type": "enterprise",
        "users_license_purchased": 10,
        "trial_expiry": null
      }
    }
  ]
}
```

### Settings API

#### List Modules Metadata

Retrieve metadata about all available CRM modules.

```bash
maton api '/zoho-crm/crm/v8/settings/modules'
```

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `status` | string | Filter by status: `user_hidden`, `system_hidden`, `scheduled_for_deletion`, `visible` |

**Response:**
```json
{
  "modules": [
    {
      "api_name": "Leads",
      "module_name": "Leads",
      "singular_label": "Lead",
      "plural_label": "Leads",
      "api_supported": true,
      "creatable": true,
      "editable": true,
      "deletable": true,
      "viewable": true,
      "status": "visible",
      "generated_type": "default",
      "id": "7243485000000002175",
      "profiles": [
        {"name": "Administrator", "id": "7243485000000026011"}
      ]
    }
  ]
}
```

#### List Fields Metadata

Retrieve field metadata for a specific module.

```bash
maton api '/zoho-crm/crm/v8/settings/fields?module={module_api_name}'
```

**Note:** `{module_api_name}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `module` | string | **Required.** API name of the module (e.g., `Leads`, `Contacts`) |
| `type` | string | `all` for all fields, `unused` for unused fields only |

**Example:**

```bash
maton api '/zoho-crm/crm/v8/settings/fields?module=Leads'
```

**Response:**
```json
{
  "fields": [
    {
      "api_name": "Last_Name",
      "field_label": "Last Name",
      "data_type": "text",
      "system_mandatory": true,
      "custom_field": false,
      "visible": true,
      "searchable": true,
      "sortable": true,
      "id": "7243485000000002613"
    }
  ]
}
```

#### List Layouts Metadata

Retrieve layout metadata for a specific module.

```bash
maton api '/zoho-crm/crm/v8/settings/layouts?module={module_api_name}'
```

**Note:** `{module_api_name}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `module` | string | **Required.** API name of the module (e.g., `Leads`, `Contacts`) |

**Example:**

```bash
maton api '/zoho-crm/crm/v8/settings/layouts?module=Leads'
```

**Response:**
```json
{
  "layouts": [
    {
      "id": "7243485000000091055",
      "name": "Standard",
      "api_name": "Standard",
      "status": "active",
      "visible": true,
      "profiles": [
        {"name": "Administrator", "id": "7243485000000026011"}
      ],
      "sections": [
        {
          "display_label": "Lead Information",
          "api_name": "Lead_Information",
          "sequence_number": 1,
          "fields": [...]
        }
      ]
    }
  ]
}
```

#### List Roles

Retrieve roles in your Zoho CRM organization.

```bash
maton api '/zoho-crm/crm/v8/settings/roles'
```

**Response:**
```json
{
  "roles": [
    {
      "id": "7243485000000026005",
      "name": "CEO",
      "display_label": "CEO",
      "share_with_peers": true,
      "description": null,
      "reporting_to": null
    },
    {
      "id": "7243485000000026008",
      "name": "Manager",
      "display_label": "Manager",
      "share_with_peers": false,
      "reporting_to": {
        "name": "CEO",
        "id": "7243485000000026005"
      }
    }
  ]
}
```

**Example - Get specific role:**

```bash
maton api '/zoho-crm/crm/v8/settings/roles/{role_id}'
```

**Note:** `{role_id}` is a placeholder. Replace it with a real value before sending the request.

#### List Profiles

Retrieve profiles (permission sets) in your Zoho CRM organization.

```bash
maton api '/zoho-crm/crm/v8/settings/profiles'
```

**Response:**
```json
{
  "profiles": [
    {
      "id": "7243485000000026011",
      "name": "Administrator",
      "display_label": "Administrator",
      "type": "normal_profile",
      "custom": false,
      "description": null
    },
    {
      "id": "7243485000000026014",
      "name": "Standard",
      "display_label": "Standard",
      "type": "normal_profile",
      "custom": false,
      "description": null
    }
  ]
}
```

**Example - Get specific profile:**

```bash
maton api '/zoho-crm/crm/v8/settings/profiles/{profile_id}'
```

**Note:** `{profile_id}` is a placeholder. Replace it with a real value before sending the request.

### Available Modules

| Module | API Name | Description |
|--------|----------|-------------|
| Leads | `Leads` | Potential customers |
| Contacts | `Contacts` | Individual people |
| Accounts | `Accounts` | Organizations/companies |
| Deals | `Deals` | Sales opportunities |
| Campaigns | `Campaigns` | Marketing campaigns |
| Tasks | `Tasks` | To-do items |
| Calls | `Calls` | Phone call logs |
| Events | `Events` | Calendar appointments |
| Products | `Products` | Items for sale |

### Mandatory Fields

| Module | Required Fields |
|--------|-----------------|
| Leads | `Last_Name` |
| Contacts | `Last_Name` |
| Accounts | `Account_Name` |
| Deals | `Deal_Name`, `Stage` |
| Tasks | `Subject` |

### Search Operators

- Text: `equals`, `not_equal`, `starts_with`, `in`
- Date/Number: `equals`, `not_equal`, `greater_than`, `less_than`, `between`, `in`
- Boolean: `equals`, `not_equal`

### Pagination

Zoho CRM uses page-based pagination with optional page tokens for large datasets:

```bash
maton api '/zoho-crm/crm/v8/{module_api_name}?fields=First_Name,Last_Name&page=1&per_page=50'
```

**Note:** `{module_api_name}` is a placeholder. Replace it with a real value before sending the request.

Response includes pagination info:

```json
{
  "data": [...],
  "info": {
    "per_page": 50,
    "count": 50,
    "page": 1,
    "sort_by": "id",
    "sort_order": "desc",
    "more_records": true,
    "next_page_token": "token_value",
    "page_token_expiry": "2026-02-07T01:10:56-08:00"
  }
}
```

- For up to 2,000 records: Use `page` parameter (increment each request)
- For 2,000+ records: Use `page_token` from previous response
- Page tokens expire after 24 hours

### Notes

- The `fields` parameter is **required** for list operations (max 50 fields)
- Module API names are case-sensitive (e.g., `Leads`, not `leads`)
- Maximum 100 records per create/update/delete request
- Maximum 200 records returned per GET request
- Maximum 2,000 records without `page_token`; up to 100,000 with `page_token`
- Use `page_token` for >2,000 records (expires after 24 hours)
- Use field API names (not display names) in requests
- If you receive a scope error, contact Maton support at support@maton.ai with the specific operations/APIs you need and your use-case
- Empty datasets return HTTP 204 (No Content) with empty body

### Resources

- [Zoho CRM API v8 Documentation](https://www.zoho.com/crm/developer/docs/api/v8/)
- [Get Records API](https://www.zoho.com/crm/developer/docs/api/v8/get-records.html)
- [Search Records API](https://www.zoho.com/crm/developer/docs/api/v8/search-records.html)
- [Organization API](https://www.zoho.com/crm/developer/docs/api/v8/get-org-data.html)
- [Users API](https://www.zoho.com/crm/developer/docs/api/v8/get-users.html)
- [Modules API](https://www.zoho.com/crm/developer/docs/api/v8/modules-api.html)
- [Fields API](https://www.zoho.com/crm/developer/docs/api/v8/field-meta.html)
- [Roles API](https://www.zoho.com/crm/developer/docs/api/v8/get-roles.html)
- [Profiles API](https://www.zoho.com/crm/developer/docs/api/v8/get-profiles.html)
- [Maton CLI Manual](https://cli.maton.ai/manual)
