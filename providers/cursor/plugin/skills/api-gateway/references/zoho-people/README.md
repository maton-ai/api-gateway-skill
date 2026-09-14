# Zoho People

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `zoho-people`
**Upstream base URL:** `people.zoho.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://people.zoho.com/people/api/forms`
- Gateway: `https://api.maton.ai/zoho-people/people/api/forms`

### Forms API

#### List All Forms

Get a list of all available forms in your Zoho People account.

```bash
maton api '/zoho-people/people/api/forms'
```

**Example:**

```bash
maton api '/zoho-people/people/api/forms'
```

**Response:**
```json
{
  "response": {
    "result": [
      {
        "componentId": 943596000000035679,
        "iscustom": false,
        "displayName": "Employee",
        "formLinkName": "employee",
        "PermissionDetails": {
          "Add": 3,
          "Edit": 3,
          "View": 3
        },
        "isVisible": true,
        "viewDetails": {
          "view_Id": 943596000000035705,
          "view_Name": "P_EmployeeView"
        }
      }
    ],
    "message": "Data fetched successfully",
    "status": 0
  }
}
```

### Employee API

#### List Employees (Bulk Records)

```bash
maton api '/zoho-people/people/api/forms/employee/getRecords?sIndex={startIndex}&limit={limit}'
```

**Note:** `{startIndex}` and `{limit}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `sIndex` | integer | 1 | Starting index (1-based) |
| `limit` | integer | 200 | Number of records (max 200) |
| `SearchColumn` | string | - | `EMPLOYEEID` or `EMPLOYEEMAILALIAS` |
| `SearchValue` | string | - | Value to search for |
| `modifiedtime` | long | - | Timestamp in milliseconds for modified records |

**Example:**

```bash
maton api '/zoho-people/people/api/forms/employee/getRecords?sIndex=1&limit=10'
```

**Response:**
```json
{
  "response": {
    "result": [
      {
        "943596000000294355": [
          {
            "FirstName": "Christopher",
            "LastName": "Brown",
            "EmailID": "christopherbrown@zylker.com",
            "EmployeeID": "S20",
            "Department": "Management",
            "Designation": "Administration",
            "Employeestatus": "Active",
            "Gender": "Male",
            "Date_of_birth": "02-Feb-1987",
            "Zoho_ID": 943596000000294355
          }
        ]
      }
    ],
    "message": "Data fetched successfully",
    "status": 0
  }
}
```

#### List Employees (View-based)

```bash
maton api '/zoho-people/api/forms/{viewName}/records?rec_limit={limit}'
```

**Note:** `{viewName}` and `{limit}` are placeholders. Replace each of them with real values before sending the request.

**Example:**

```bash
maton api '/zoho-people/api/forms/P_EmployeeView/records?rec_limit=10'
```

#### Search Employee by ID

```bash
maton api '/zoho-people/people/api/forms/employee/getRecords?SearchColumn=EMPLOYEEID&SearchValue={employeeId}'
```

**Note:** `{employeeId}` is a placeholder. Replace it with a real value before sending the request.

**Example:**

```bash
maton api '/zoho-people/people/api/forms/employee/getRecords?SearchColumn=EMPLOYEEID&SearchValue=S20'
```

#### Search Employee by Email

```bash
maton api '/zoho-people/people/api/forms/employee/getRecords?SearchColumn=EMPLOYEEMAILALIAS&SearchValue={email}'
```

**Note:** `{email}` is a placeholder. Replace it with a real value before sending the request.

### Department API

#### List Departments

```bash
maton api '/zoho-people/people/api/forms/department/getRecords?sIndex={startIndex}&limit={limit}'
```

**Note:** `{startIndex}` and `{limit}` are placeholders. Replace each of them with real values before sending the request.

**Example:**

```bash
maton api '/zoho-people/people/api/forms/department/getRecords?sIndex=1&limit=50'
```

**Response:**
```json
{
  "response": {
    "result": [
      {
        "943596000000294315": [
          {
            "Department": "IT",
            "Department_Lead": "",
            "Parent_Department": "",
            "Zoho_ID": 943596000000294315
          }
        ]
      }
    ],
    "message": "Data fetched successfully",
    "status": 0
  }
}
```

### Designation API

#### List Designations

```bash
maton api '/zoho-people/people/api/forms/designation/getRecords?sIndex={startIndex}&limit={limit}'
```

**Note:** `{startIndex}` and `{limit}` are placeholders. Replace each of them with real values before sending the request.

**Example:**

```bash
maton api '/zoho-people/people/api/forms/designation/getRecords?sIndex=1&limit=50'
```

**Response:**
```json
{
  "response": {
    "result": [
      {
        "943596000000294399": [
          {
            "Designation": "Team Member",
            "EEO_Category": "Professionals",
            "Zoho_ID": 943596000000294399
          }
        ]
      }
    ],
    "message": "Data fetched successfully",
    "status": 0
  }
}
```

### Records API

#### Insert Record

Add a new record to any form.

```bash
maton api -X POST '/zoho-people/people/api/forms/json/{formLinkName}/insertRecord' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
inputData={field1:'value1',field2:'value2'}
BODY
```

**Note:** `{formLinkName}` is a placeholder. Replace it with a real value before sending the request.

**Example - Create Department:**

```bash
maton api -X POST '/zoho-people/people/api/forms/json/department/insertRecord' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'BODY'
inputData=%7B%22Department%22%3A+%22Engineering%22%7D
BODY
```

**Response:**
```json
{
  "response": {
    "result": {
      "pkId": "943596000000300001",
      "message": "Successfully Added"
    },
    "message": "Data added successfully",
    "status": 0
  }
}
```

#### Update Record

Modify an existing record.

```bash
maton api -X POST '/zoho-people/people/api/forms/json/{formLinkName}/updateRecord' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
inputData={field1:'newValue'}&recordId={recordId}
BODY
```

**Note:** `{formLinkName}` and `{recordId}` are placeholders. Replace each of them with real values before sending the request.

**Example - Update Employee:**

```bash
maton api -X POST '/zoho-people/people/api/forms/json/employee/updateRecord' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'BODY'
inputData=%7B%22Department%22%3A+%22Engineering%22%7D&recordId=943596000000294355
BODY
```

### Leave API

#### List Leave Records

```bash
maton api '/zoho-people/people/api/forms/leave/getRecords?sIndex={startIndex}&limit={limit}'
```

**Note:** `{startIndex}` and `{limit}` are placeholders. Replace each of them with real values before sending the request.

#### Add Leave

```bash
maton api -X POST '/zoho-people/people/api/forms/json/leave/insertRecord' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
inputData={Employee_ID:'EMP001',Leavetype:'123456',From:'01-Feb-2026',To:'02-Feb-2026'}
BODY
```

### Attendance API

Note: Attendance endpoints require additional OAuth scopes.

#### Get Attendance Entries

```bash
maton api '/zoho-people/people/api/attendance/getAttendanceEntries?date={date}&dateFormat={format}'
```

**Note:** `{date}` and `{format}` are placeholders. Replace each of them with real values before sending the request.

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `date` | string | Date in organization format |
| `dateFormat` | string | Date format (e.g., `dd-MMM-yyyy`) |
| `empId` | string | Employee ID (optional) |
| `emailId` | string | Employee email (optional) |

#### Check-In/Check-Out

```bash
maton api -X POST '/zoho-people/people/api/attendance' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
dateFormat=dd/MM/yyyy HH:mm:ss&checkIn={datetime}&checkOut={datetime}&empId={empId}
BODY
```

**Note:** `{datetime}` and `{empId}` are placeholders. Replace each of them with real values before sending the request.

### Common Form Link Names

| Form | formLinkName | Description |
|------|--------------|-------------|
| Employee | `employee` | Employee records |
| Department | `department` | Departments |
| Designation | `designation` | Job titles |
| Leave | `leave` | Leave requests |
| Clients | `P_ClientDetails` | Client information |

### Pagination

Zoho People uses index-based pagination:

```bash
maton api '/zoho-people/people/api/forms/{formLinkName}/getRecords?sIndex=1&limit=200'
```

**Note:** `{formLinkName}` is a placeholder. Replace it with a real value before sending the request.

- `sIndex`: Starting index (1-based)
- `limit`: Number of records per request (max 200)

For subsequent pages:
- Page 1: `sIndex=1&limit=200`
- Page 2: `sIndex=201&limit=200`
- Page 3: `sIndex=401&limit=200`

### Notes

- Record IDs are numeric strings (e.g., `943596000000294355`)
- The `Zoho_ID` field in responses contains the record ID
- Maximum 200 records per GET request
- Insert/Update operations use form-urlencoded data with `inputData` JSON
- Date format varies by field and organization settings
- Some endpoints (attendance, leave) require additional OAuth scopes. If you receive an `INVALID_OAUTHSCOPE` error, contact Maton support at support@maton.ai with the specific operations/APIs you need and your use-case
- Response structure wraps data in `response.result[]` array

### Resources

- [Zoho People API Overview](https://www.zoho.com/people/api/overview.html)
- [Get Bulk Records API](https://www.zoho.com/people/api/bulk-records.html)
- [Insert Record API](https://www.zoho.com/people/api/insert-records.html)
- [Update Record API](https://www.zoho.com/people/api/update-records.html)
- [Maton CLI Manual](https://cli.maton.ai/manual)
