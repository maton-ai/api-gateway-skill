# Zoho Bookings

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `zoho-bookings`
**Upstream base URL:** `www.zohoapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://www.zohoapis.com/bookings/v1/json/workspaces`
- Gateway: `https://api.maton.ai/zoho-bookings/bookings/v1/json/workspaces`

### Workspaces API

#### List Workspaces

```bash
maton api '/zoho-bookings/bookings/v1/json/workspaces'
```

**Query parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `workspace_id` | string | Filter by specific workspace ID |

**Example:**

```bash
maton api '/zoho-bookings/bookings/v1/json/workspaces'
```

**Response:**
```json
{
  "response": {
    "returnvalue": {
      "data": [
        {
          "name": "Main Office",
          "id": "4753814000000048016"
        }
      ]
    },
    "status": "success"
  }
}
```

#### Create Workspace

```bash
maton api -X POST '/zoho-bookings/bookings/v1/json/createworkspace' -H 'Content-Type: application/x-www-form-urlencoded'
```

**Form Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Workspace name (2-50 chars, no special characters) |

**Example:**

```bash
maton api -X POST '/zoho-bookings/bookings/v1/json/createworkspace' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
name=New+York+Office
BODY
```

### Services API

#### List Services

```bash
maton api '/zoho-bookings/bookings/v1/json/services?workspace_id={workspace_id}'
```

**Note:** `{workspace_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `workspace_id` | string | Yes | Workspace ID |
| `service_id` | string | No | Filter by specific service ID |
| `staff_id` | string | No | Filter by staff ID |

**Example:**

```bash
maton api '/zoho-bookings/bookings/v1/json/services?workspace_id=4753814000000048016'
```

**Response:**
```json
{
  "response": {
    "returnvalue": {
      "data": [
        {
          "id": "4753814000000048054",
          "name": "Product Demo",
          "duration": "30 mins",
          "service_type": "APPOINTMENT",
          "price": 0,
          "currency": "USD",
          "assigned_staffs": ["4753814000000048014"],
          "assigned_workspace": "4753814000000048016",
          "embed_url": "https://example.zohobookings.com/portal-embed#/4753814000000048054",
          "let_customer_select_staff": true
        }
      ],
      "next_page_available": false,
      "page": 1
    },
    "status": "success"
  }
}
```

#### Create Service

```bash
maton api -X POST '/zoho-bookings/bookings/v1/json/createservice' -H 'Content-Type: application/x-www-form-urlencoded'
```

**Form Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Service name |
| `workspace_id` | string | Yes | Workspace ID |
| `duration` | integer | No | Duration in minutes |
| `cost` | number | No | Service price |
| `pre_buffer` | integer | No | Buffer time before (minutes) |
| `post_buffer` | integer | No | Buffer time after (minutes) |
| `description` | string | No | Service description |
| `assigned_staffs` | string | No | JSON array of staff IDs |

**Example:**

```bash
maton api -X POST '/zoho-bookings/bookings/v1/json/createservice' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
name=Consultation&workspace_id=4753814000000048016&duration=60
BODY
```

### Staff API

#### List Staff

```bash
maton api '/zoho-bookings/bookings/v1/json/staffs?workspace_id={workspace_id}'
```

**Note:** `{workspace_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `workspace_id` | string | Yes | Workspace ID |
| `staff_id` | string | No | Filter by specific staff ID |
| `service_id` | string | No | Filter by service ID |
| `staff_email` | string | No | Filter by email (partial match) |

**Example:**

```bash
maton api '/zoho-bookings/bookings/v1/json/staffs?workspace_id=4753814000000048016'
```

**Response:**
```json
{
  "response": {
    "returnvalue": {
      "data": [
        {
          "id": "4753814000000048014",
          "name": "John Doe",
          "email": "john@example.com",
          "designation": "Consultant",
          "assigned_services": ["4753814000000048054"],
          "assigned_workspaces": ["4753814000000048016"],
          "embed_url": "https://example.zohobookings.com/portal-embed#/4753814000000048014"
        }
      ]
    },
    "status": "success"
  }
}
```

### Appointments API

#### Book Appointment

```bash
maton api -X POST '/zoho-bookings/bookings/v1/json/appointment' -H 'Content-Type: application/x-www-form-urlencoded'
```

**Form Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `service_id` | string | Yes | Service ID |
| `staff_id` | string | Yes* | Staff ID (*or resource_id/group_id) |
| `from_time` | string | Yes | Start time: `dd-MMM-yyyy HH:mm:ss` (24-hour) |
| `timezone` | string | No | Timezone (e.g., `America/Los_Angeles`) |
| `customer_details` | string | Yes | JSON string with `name`, `email`, `phone_number` |
| `notes` | string | No | Appointment notes |
| `additional_fields` | string | No | JSON string with custom fields |

**Example:**

```bash
maton api -X POST '/zoho-bookings/bookings/v1/json/appointment' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'BODY'
service_id=4753814000000048054&staff_id=4753814000000048014&from_time=20-Feb-2026+10%3A00%3A00&timezone=America%2FLos_Angeles&customer_details=%7B%22name%22%3A+%22Jane+Smith%22%2C+%22email%22%3A+%22jane%40example.com%22%2C+%22phone_number%22%3A+%22%2B15551234567%22%7D
BODY
```

**Response:**
```json
{
  "response": {
    "returnvalue": {
      "booking_id": "#NU-00001",
      "service_name": "Product Demo",
      "staff_name": "John Doe",
      "start_time": "20-Feb-2026 10:00:00",
      "end_time": "20-Feb-2026 10:30:00",
      "duration": "30 mins",
      "customer_name": "Jane Smith",
      "customer_email": "jane@example.com",
      "status": "upcoming",
      "time_zone": "America/Los_Angeles"
    },
    "status": "success"
  }
}
```

#### Get Appointment

```bash
maton api '/zoho-bookings/bookings/v1/json/getappointment?booking_id={booking_id}'
```

**Note:** `{booking_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `booking_id` | string | Yes | Booking ID (URL-encoded, e.g., `%23NU-00001`) |

**Example:**

```bash
maton api '/zoho-bookings/bookings/v1/json/getappointment?booking_id=%23NU-00001'
```

#### List Appointments

```bash
maton api -X POST '/zoho-bookings/bookings/v1/json/fetchappointment' -H 'Content-Type: application/x-www-form-urlencoded'
```

**Form Parameters:**

Send parameters wrapped in a `data` field as JSON:

| Parameter | Type | Description |
|-----------|------|-------------|
| `from_time` | string | Start date: `dd-MMM-yyyy HH:mm:ss` |
| `to_time` | string | End date: `dd-MMM-yyyy HH:mm:ss` |
| `status` | string | `UPCOMING`, `CANCEL`, `COMPLETED`, `NO_SHOW`, `PENDING` |
| `service_id` | string | Filter by service |
| `staff_id` | string | Filter by staff |
| `customer_name` | string | Filter by customer name (partial match) |
| `customer_email` | string | Filter by email (partial match) |
| `page` | integer | Page number |
| `per_page` | integer | Results per page (max 100) |

**Example:**

```bash
maton api -X POST '/zoho-bookings/bookings/v1/json/fetchappointment' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --input - <<'BODY'
data=%7B%22from_time%22%3A+%2217-Feb-2026+00%3A00%3A00%22%2C+%22to_time%22%3A+%2220-Feb-2026+23%3A59%3A59%22%7D
BODY
```

**Response:**
```json
{
  "response": {
    "returnvalue": {
      "response": [
        {
          "booking_id": "#NU-00001",
          "service_name": "Product Demo",
          "staff_name": "John Doe",
          "start_time": "20-Feb-2026 10:00:00",
          "customer_name": "Jane Smith",
          "status": "upcoming"
        }
      ],
      "next_page_available": false,
      "page": 1
    },
    "status": "success"
  }
}
```

#### Update Appointment

```bash
maton api -X POST '/zoho-bookings/bookings/v1/json/updateappointment' -H 'Content-Type: application/x-www-form-urlencoded'
```

**Form Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `booking_id` | string | Yes | Booking ID |
| `action` | string | Yes | `completed`, `cancel`, or `noshow` |

**Example - Cancel Appointment:**

```bash
maton api -X POST '/zoho-bookings/bookings/v1/json/updateappointment' -H 'Content-Type: application/x-www-form-urlencoded' --input - <<'BODY'
booking_id=%23NU-00001&action=cancel
BODY
```

### Request Format Notes

- **GET endpoints**: Use query parameters
- **POST endpoints**: Use `application/x-www-form-urlencoded`
- **fetchappointment**: Requires parameters wrapped in `data` field as JSON string
- **customer_details**: Must be a JSON string, not an object
- **Date format**: `dd-MMM-yyyy HH:mm:ss` (e.g., `20-Feb-2026 10:00:00`)

### Service Types

- `APPOINTMENT` - One-on-one appointments
- `RESOURCE` - Resource bookings
- `CLASS` - Group classes
- `COLLECTIVE` - Collective bookings with multiple staff

### Appointment Status Values

- `UPCOMING` - Future appointments
- `CANCEL` - Cancelled appointments
- `COMPLETED` - Completed appointments
- `NO_SHOW` - Customer no-shows
- `PENDING` - Pending confirmation
- `ONGOING` - Currently in progress

### Notes

- Booking IDs include `#` prefix (URL-encode as `%23` in GET requests)
- The `workspace_id` is required for services and staff endpoints
- POST endpoints use form-urlencoded, not JSON body
- Daily API limits: Free (250), Basic (1,000), Premium/Zoho One (3,000) per user

### Resources

- [Zoho Bookings API Documentation](https://www.zoho.com/bookings/help/api/v1/oauthauthentication.html)
- [Book Appointment API](https://www.zoho.com/bookings/help/api/v1/book-appointment.html)
- [Fetch Services API](https://www.zoho.com/bookings/help/api/v1/fetch-services.html)
- [Maton CLI Manual](https://cli.maton.ai/manual)
