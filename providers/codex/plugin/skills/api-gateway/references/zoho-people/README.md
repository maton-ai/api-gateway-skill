# Zoho People Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `zoho-people`
**Base URL proxied:** `people.zoho.com`

## API Path Pattern

```
/zoho-people/people/api/{resource}
```

or for view-based endpoints:

```
/zoho-people/api/forms/{viewName}/records
```

## Common Endpoints

### Forms

```bash
# List all forms
maton api '/zoho-people/people/api/forms'
```

### Records (Bulk)

```bash
# Get records from any form
maton api '/zoho-people/people/api/forms/{formLinkName}/getRecords?sIndex=1&limit=200'

# Common form link names:
# - employee
# - department
# - designation
# - leave
# - P_ClientDetails
```

### Records (View-based)

```bash
# Get records using a view
maton api '/zoho-people/api/forms/{viewName}/records?rec_limit=200'

# Common view names:
# - P_EmployeeView
# - P_DepartmentView
# - P_DesignationView
```

### Search

```bash
# Search by Employee ID
maton api '/zoho-people/people/api/forms/employee/getRecords?SearchColumn=EMPLOYEEID&SearchValue={empId}'

# Search by Email
maton api '/zoho-people/people/api/forms/employee/getRecords?SearchColumn=EMPLOYEEMAILALIAS&SearchValue={email}'

# Get modified records
maton api '/zoho-people/people/api/forms/{formLinkName}/getRecords?modifiedtime={timestamp_ms}'
```

### Insert Record

```bash
maton api -X POST '/zoho-people/people/api/forms/json/{formLinkName}/insertRecord' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
inputData={"field1":"value1","field2":"value2"}
EOF
```

### Update Record

```bash
maton api -X POST '/zoho-people/people/api/forms/json/{formLinkName}/updateRecord' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
inputData={"field1":"newValue"}&recordId={recordId}
EOF
```

### Attendance

```bash
# Get attendance entries (requires additional scope)
maton api '/zoho-people/people/api/attendance/getAttendanceEntries?date={date}&dateFormat={format}'

# Check-in/Check-out (requires additional scope)
maton api -X POST '/zoho-people/people/api/attendance' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
dateFormat=dd/MM/yyyy HH:mm:ss&checkIn={datetime}&checkOut={datetime}&empId={empId}
EOF
```

### Leave

```bash
# Get leave records
maton api '/zoho-people/people/api/forms/leave/getRecords?sIndex=1&limit=200'

# Add leave
maton api -X POST '/zoho-people/people/api/forms/json/leave/insertRecord' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'EOF'
inputData={"Employee_ID":"EMP001","Leavetype":"123456","From":"01-Feb-2026","To":"02-Feb-2026"}
EOF
```

## Common Form Link Names

| Form | formLinkName |
|------|--------------|
| Employee | `employee` |
| Department | `department` |
| Designation | `designation` |
| Leave | `leave` |
| Clients | `P_ClientDetails` |

## Pagination

Uses index-based pagination:
- `sIndex`: Starting index (1-based)
- `limit`: Max records per request (max 200)

For page 2: `sIndex=201&limit=200`

## Notes

- Record IDs are numeric strings (e.g., `943596000000294355`)
- Insert/Update use `application/x-www-form-urlencoded` content type
- `inputData` parameter contains JSON object as string
- Attendance endpoints require additional OAuth scopes
- Maximum 200 records per request
- Response wraps data in `response.result[]` array

## Error Codes

| Code | Description |
|------|-------------|
| 7011 | Invalid form name |
| 7012 | Invalid view name |
| 7021 | Max limit exceeded (200) |
| 7024 | No records found |
| 7042 | Invalid search value |
| 7218 | Invalid OAuth scope |

## Resources

- [Zoho People API Overview](https://www.zoho.com/people/api/overview.html)
- [Get Bulk Records API](https://www.zoho.com/people/api/bulk-records.html)
- [Insert Record API](https://www.zoho.com/people/api/insert-records.html)
- [Update Record API](https://www.zoho.com/people/api/update-records.html)
