# Zoho Recruit

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `zoho-recruit`
**Upstream base URL:** `recruit.zoho.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://recruit.zoho.com/recruit/v2/settings/modules`
- Gateway: `https://api.maton.ai/zoho-recruit/recruit/v2/settings/modules`

### Modules API

#### List All Modules

Get a list of all available modules in your Zoho Recruit account.

```bash
maton api '/zoho-recruit/recruit/v2/settings/modules'
```

#### Get Module

Get metadata for a single module.

```bash
maton api '/zoho-recruit/recruit/v2/settings/modules/{module_api_name}'
```

**Note:** `{module_api_name}` is a placeholder. Replace it with a real value before sending the request.

### Candidates API

#### List Candidates

```bash
maton api '/zoho-recruit/recruit/v2/Candidates'
```

**Query parameters:**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `fields` | string | - | Comma-separated field API names |
| `sort_order` | string | - | `asc` or `desc` |
| `sort_by` | string | - | Field API name to sort by |
| `converted` | string | - | `true`, `false`, or `both` |
| `approved` | string | - | `true`, `false`, or `both` |
| `page` | integer | 1 | Page number |
| `per_page` | integer | 200 | Records per page (max 200) |

**Example:**

```bash
maton api '/zoho-recruit/recruit/v2/Candidates?per_page=10'
```

**Response:**
```json
{
  "data": [
    {
      "id": "846336000000552208",
      "First_Name": "Christina",
      "Last_Name": "Palaskas",
      "Email": "c.palaskas@example.com",
      "Candidate_Status": "Converted - Employee",
      "Current_Employer": "Chandlers",
      "Current_Job_Title": "Technical Consultant",
      "Experience_in_Years": 3,
      "Skill_Set": "Communication, Presentation, Customer service",
      "Candidate_Owner": {
        "name": "Byungkyu Park",
        "id": "846336000000549541"
      }
    }
  ],
  "info": {
    "per_page": 10,
    "count": 1,
    "page": 1,
    "more_records": false
  }
}
```

#### Get Candidate by ID

```bash
maton api '/zoho-recruit/recruit/v2/Candidates/{record_id}'
```

**Note:** `{record_id}` is a placeholder. Replace it with a real value before sending the request.

#### Search Candidates

```bash
maton api '/zoho-recruit/recruit/v2/Candidates/search?criteria={criteria}'
```

**Note:** `{criteria}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `criteria` | string | Search criteria (e.g., `(Last_Name:contains:Smith)`) |
| `email` | string | Search by email |
| `phone` | string | Search by phone |
| `word` | string | Global word search |
| `page` | integer | Page number |
| `per_page` | integer | Records per page |

**Search Operators:**
- Text: `equals`, `not_equal`, `starts_with`, `ends_with`, `contains`, `not_contains`, `in`
- Date/Number: `equals`, `not_equal`, `greater_than`, `less_than`, `greater_equal`, `less_equal`, `between`

#### Create Candidate

```bash
maton api -X POST '/zoho-recruit/recruit/v2/Candidates' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": [
    {
      "First_Name": "John",
      "Last_Name": "Doe",
      "Email": "john.doe@example.com",
      "Phone": "555-123-4567",
      "Current_Job_Title": "Software Engineer"
    }
  ]
}
JSON
```

**Example:**

```bash
maton api -X POST '/zoho-recruit/recruit/v2/Candidates' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": [
    {
      "First_Name": "John",
      "Last_Name": "Doe",
      "Email": "john.doe@example.com",
      "Phone": "555-123-4567",
      "Current_Job_Title": "Software Engineer"
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
      "status": "success",
      "message": "record added",
      "details": {
        "id": "846336000000600001",
        "Created_Time": "2026-02-06T10:00:00-08:00",
        "Created_By": {
          "name": "User Name",
          "id": "846336000000549541"
        }
      }
    }
  ]
}
```

#### Update Candidate

```bash
maton api -X PUT '/zoho-recruit/recruit/v2/Candidates/{record_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": [
    {
      "Current_Job_Title": "Senior Software Engineer"
    }
  ]
}
JSON
```

**Note:** `{record_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Candidates

```bash
maton api '/zoho-recruit/recruit/v2/Candidates?ids={record_id1},{record_id2}' -X DELETE
```

**Note:** `{record_id1}` and `{record_id2}` are placeholders. Replace each of them with real values before sending the request.

### Job Openings API

#### List Job Openings

```bash
maton api '/zoho-recruit/recruit/v2/Job_Openings'
```

**Example:**

```bash
maton api '/zoho-recruit/recruit/v2/Job_Openings?per_page=10'
```

**Response:**
```json
{
  "data": [
    {
      "id": "846336000000552093",
      "Posting_Title": "Senior Accountant (Sample)",
      "Job_Opening_Status": "Waiting for approval",
      "Date_Opened": "2026-01-21",
      "Target_Date": "2026-02-20",
      "Industry": "Accounting",
      "City": "Tallahassee",
      "No_of_Candidates_Hired": 0,
      "No_of_Candidates_Associated": 0
    }
  ],
  "info": {
    "per_page": 10,
    "count": 1,
    "page": 1,
    "more_records": false
  }
}
```

#### Get Job Opening by ID

```bash
maton api '/zoho-recruit/recruit/v2/Job_Openings/{record_id}'
```

**Note:** `{record_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Job Opening

```bash
maton api -X POST '/zoho-recruit/recruit/v2/Job_Openings' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": [
    {
      "Posting_Title": "Software Engineer",
      "Job_Opening_Status": "In-progress",
      "Date_Opened": "2026-02-01",
      "Target_Date": "2026-03-01"
    }
  ]
}
JSON
```

#### Update Job Opening

```bash
maton api -X PUT '/zoho-recruit/recruit/v2/Job_Openings/{record_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": [
    {
      "id": "{record_id}",
      "Job_Opening_Name": "Senior Engineer",
      "Job_Opening_Status": "In-progress"
    }
  ]
}
JSON
```

**Note:** `{record_id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Job Openings

```bash
maton api '/zoho-recruit/recruit/v2/Job_Openings?ids={record_id1},{record_id2}' -X DELETE
```

**Note:** `{record_id1}` and `{record_id2}` are placeholders. Replace each of them with real values before sending the request.

### Interviews API

#### List Interviews

```bash
maton api '/zoho-recruit/recruit/v2/Interviews'
```

**Example:**

```bash
maton api '/zoho-recruit/recruit/v2/Interviews?per_page=10'
```

#### Get Interview by ID

```bash
maton api '/zoho-recruit/recruit/v2/Interviews/{record_id}'
```

**Note:** `{record_id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Interview

```bash
maton api -X POST '/zoho-recruit/recruit/v2/Interviews' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "data": [
    {
      "Interview_Name": "Technical Interview",
      "Candidate_Name": {"id": "846336000000552208"},
      "Posting_Title": {"id": "846336000000552093"},
      "Start_DateTime": "2026-02-10T10:00:00-08:00",
      "End_DateTime": "2026-02-10T11:00:00-08:00"
    }
  ]
}
JSON
```

### Departments API

#### List Departments

```bash
maton api '/zoho-recruit/recruit/v2/Departments'
```

**Example:**

```bash
maton api '/zoho-recruit/recruit/v2/Departments?per_page=10'
```

### Applications API

#### List Applications

```bash
maton api '/zoho-recruit/recruit/v2/Applications'
```

### Records API

All modules support the same CRUD operations. Replace `{module_api_name}` with an API name from [Available Modules](#available-modules).

#### List Records

```bash
maton api '/zoho-recruit/recruit/v2/{module_api_name}?page=1&per_page=200'
```

**Note:** `{module_api_name}` is a placeholder. Replace it with a real value before sending the request.

#### Get Record by ID

```bash
maton api '/zoho-recruit/recruit/v2/{module_api_name}/{record_id}'
```

**Note:** `{module_api_name}` and `{record_id}` are placeholders. Replace each of them with real values before sending the request.

#### Create Records

Maximum 100 records per request.

```bash
maton api -X POST '/zoho-recruit/recruit/v2/{module_api_name}' -H 'Content-Type: application/json' --input - <<'JSON'
{"data": [{"Field_Name": "value"}]}
JSON
```

**Note:** `{module_api_name}` is a placeholder. Replace it with a real value before sending the request.

#### Update Records

Update a single record by ID:

```bash
maton api -X PUT '/zoho-recruit/recruit/v2/{module_api_name}/{record_id}' -H 'Content-Type: application/json' --input - <<'JSON'
{"data": [{"Field_Name": "new_value"}]}
JSON
```

Update multiple records in one call (maximum 100), passing each `id` in the body:

```bash
maton api -X PUT '/zoho-recruit/recruit/v2/{module_api_name}' -H 'Content-Type: application/json' --input - <<'JSON'
{"data": [{"id": "record_id", "Field_Name": "value"}]}
JSON
```

**Note:** `{module_api_name}` and `{record_id}` are placeholders. Replace each of them with real values before sending the request.

`PUT` overwrites the fields you send, so retrieve the record first and confirm the exact before-and-after rather than assuming a field is empty.

#### Delete Records

```bash
maton api '/zoho-recruit/recruit/v2/{module_api_name}?ids={id1},{id2}' -X DELETE
```

**Note:** `{module_api_name}`, `{id1}` and `{id2}` are placeholders. Replace each of them with real values before sending the request. Maximum 100 records per request.

> **⚠ `DELETE ...?ids=` is a bulk, irreversible operation — the comma is the whole risk.** Every ID in that list is deleted in one call, and a record takes its notes, attachments, interview history, and application trail with it. Recovery depends on the account's recycle-bin retention and may not be possible. Two things make it easy to get wrong: the IDs are opaque numbers that say nothing about who they belong to, and `{module_api_name}` means the same URL shape deletes candidates, clients, or job openings depending on one path segment.
>
> Before calling it: `GET` each record and show the user its name and module alongside its ID, state that the deletion is bulk and irreversible, and get explicit approval **for every ID in the list**. Never delete a record the user did not individually name, never widen a list beyond what they approved, and never build the ID list from a search the user has not reviewed — a `criteria` query that matches more than expected turns directly into a mass deletion. If the user cannot review the records one by one, the batch is too large to run: narrow the task instead.

### Search API

#### Search Records

```bash
maton api '/zoho-recruit/recruit/v2/{module_api_name}/search'
```

**Note:** `{module_api_name}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters (one of `criteria`, `email`, `phone` or `word` required):**

| Parameter | Type | Description |
|-----------|------|-------------|
| `criteria` | string | Search criteria (e.g., `(Last_Name:contains:Smith)`) |
| `email` | string | Search by email address |
| `phone` | string | Search by phone number |
| `word` | string | Global word search |
| `page` | integer | Page number |
| `per_page` | integer | Records per page (max 200) |

**Examples:**

```bash
# Search by criteria
maton api '/zoho-recruit/recruit/v2/{module_api_name}/search?criteria={criteria}'
maton api '/zoho-recruit/recruit/v2/Candidates/search?criteria=(Last_Name:contains:Smith)'

# Search by email
maton api '/zoho-recruit/recruit/v2/{module_api_name}/search?email=user@example.com'
maton api '/zoho-recruit/recruit/v2/Candidates/search?email=jane.doe@example.com'

# Search by phone
maton api '/zoho-recruit/recruit/v2/{module_api_name}/search?phone=555-1234'

# Global word search
maton api '/zoho-recruit/recruit/v2/{module_api_name}/search?word=keyword'
maton api '/zoho-recruit/recruit/v2/Candidates/search?word=engineer'
```

**Note:** `{module_api_name}` and `{criteria}` are placeholders. Replace each of them with real values before sending the request.

### Available Modules

| Module | API Name |
|--------|----------|
| Candidates | `Candidates` |
| Job Openings | `Job_Openings` |
| Applications | `Applications` |
| Interviews | `Interviews` |
| Departments | `Departments` |
| Clients | `Clients` |
| Contacts | `Contacts` |
| Campaigns | `Campaigns` |
| Referrals | `Referrals` |
| Tasks | `Tasks` |
| Events | `Events` |
| Vendors | `Vendors` |

### Query parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `fields` | string | Comma-separated field API names |
| `sort_order` | string | `asc` or `desc` |
| `sort_by` | string | Field API name |
| `converted` | string | `true`, `false`, or `both` |
| `approved` | string | `true`, `false`, or `both` |
| `page` | integer | Page number (default: 1) |
| `per_page` | integer | Records per page (max 200) |

### Search Operators

**Text fields:**
- `equals`, `not_equal`, `starts_with`, `ends_with`, `contains`, `not_contains`, `in`

**Date/Number fields:**
- `equals`, `not_equal`, `greater_than`, `less_than`, `greater_equal`, `less_equal`, `between`

### Pagination

Uses page-based pagination:
- `page`: Page number (default: 1)
- `per_page`: Records per page (max: 200)

Response includes:
```json
{
  "data": [...],
  "info": {
    "per_page": 200,
    "count": 50,
    "page": 1,
    "more_records": false
  }
}
```

### Notes

- Module API names are case-sensitive (e.g., `Job_Openings`)
- Maximum 200 records per GET request
- Maximum 100 records per POST/PUT/DELETE request
- `Last_Name` is mandatory for Candidates
- Date format: `yyyy-MM-dd`
- DateTime format: `yyyy-MM-ddTHH:mm:ss±HH:mm` (ISO 8601)
- Lookup fields use JSON objects with `id`

### Resources

- [Zoho Recruit API v2 Overview](https://www.zoho.com/recruit/developer-guide/apiv2/)
- [Get Records API](https://www.zoho.com/recruit/developer-guide/apiv2/get-records.html)
- [Search Records API](https://www.zoho.com/recruit/developer-guide/apiv2/search-records.html)
- [Maton CLI Manual](https://cli.maton.ai/manual)
