# Unbounce

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `unbounce`
**Upstream base URL:** `api.unbounce.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.unbounce.com/accounts`
- Gateway: `https://api.maton.ai/unbounce/accounts`

### Accounts API

#### List Accounts

```bash
maton api '/unbounce/accounts'
```

**Query parameters:**
- `sort_order` - `asc` or `desc` (default: desc by creation date)

**Response:**
```json
{
  "metadata": {
    "count": 1,
    "location": ".../accounts"
  },
  "accounts": [
    {
      "id": 4967935,
      "name": "My Account",
      "createdAt": "2026-03-04T10:54:34Z",
      "state": "active",
      "options": {}
    }
  ]
}
```

#### Get Account

```bash
maton api '/unbounce/accounts/{account_id}'
```

**Note:** `{account_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": 4967935,
  "name": "My Account",
  "createdAt": "2026-03-04T10:54:34Z",
  "state": "active",
  "options": {}
}
```

#### List Account Pages

```bash
maton api '/unbounce/accounts/{account_id}/pages'
```

**Note:** `{account_id}` is a placeholder. Replace it with a real value before sending the request.

#### List Sub-Accounts

```bash
maton api '/unbounce/accounts/{account_id}/sub_accounts'
```

**Note:** `{account_id}` is a placeholder. Replace it with a real value before sending the request.

### Sub-Accounts API

#### Get Sub-Account

```bash
maton api '/unbounce/sub_accounts/{sub_account_id}'
```

**Note:** `{sub_account_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": 5699747,
  "accountId": 4967935,
  "name": "ChrisKim",
  "createdAt": "2026-03-04T10:54:35Z",
  "website": null,
  "uuid": "cf72cbb6-17fd-44d1-bbe4-d25dcad6354a",
  "domainsCount": 0
}
```

#### List Sub-Account Pages

```bash
maton api '/unbounce/sub_accounts/{sub_account_id}/pages'
```

**Note:** `{sub_account_id}` is a placeholder. Replace it with a real value before sending the request.

#### List Domains

```bash
maton api '/unbounce/sub_accounts/{sub_account_id}/domains'
```

**Note:** `{sub_account_id}` is a placeholder. Replace it with a real value before sending the request.

#### List Page Groups

```bash
maton api '/unbounce/sub_accounts/{sub_account_id}/page_groups'
```

**Note:** `{sub_account_id}` is a placeholder. Replace it with a real value before sending the request.

#### List Pages

```bash
maton api '/unbounce/pages'
```

**Query parameters:**
- `role` - Filter by user role: `viewer` or `author`
- `with_stats` - Include A/B test statistics when `true`
- `limit` - Results per page (default: 50, max: 1000)
- `offset` - Skip first N results
- `sort_order` - `asc` or `desc`
- `count` - When `true`, only return count in metadata
- `from` - Start date, RFC 5322 format (e.g. `Sat, 01 Jan 2026 00:00:00 -0000`); query filters take RFC 5322 dates while response timestamps are ISO 8601
- `to` - End date, RFC 5322 format

**Response:**
```json
{
  "metadata": {
    "count": 1,
    "location": ".../pages"
  },
  "pages": [
    {
      "id": "7cacd6d4-015a-4690-9537-68aac06bd98e",
      "subAccountId": 5699747,
      "name": "Training Template",
      "url": "http://unbouncepages.com/training-template/",
      "state": "unpublished",
      "domain": "unbouncepages.com",
      "createdAt": "2026-03-04T10:56:54Z",
      "lastPublishedAt": null,
      "variantsCount": 0,
      "integrationsCount": 0,
      "integrationsErrorsCount": 0
    }
  ]
}
```

### Pages API

#### Get Page

```bash
maton api '/unbounce/pages/{page_id}'
```

**Note:** `{page_id}` is a placeholder. Replace it with a real value before sending the request.

Includes test statistics (A/B testing data):

**Response:**
```json
{
  "id": "7cacd6d4-015a-4690-9537-68aac06bd98e",
  "name": "Training Template",
  "url": "http://unbouncepages.com/training-template/",
  "state": "unpublished",
  "tests": {
    "current": {
      "champion": "a",
      "hasResults": "false",
      "conversionRate": "0",
      "conversions": "0",
      "visitors": "0",
      "visits": "0"
    }
  }
}
```

#### List Page Form Fields

```bash
maton api '/unbounce/pages/{page_id}/form_fields'
```

**Note:** `{page_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `include_sub_pages` - Include sub-page form fields when `true`
- `sort_order` - `asc` or `desc`
- `count` - When `true`, only return count

**Response:**
```json
{
  "metadata": {
    "count": 3
  },
  "formFields": [
    {
      "id": "name",
      "name": "Name",
      "type": "text",
      "validations": {
        "required": false
      }
    },
    {
      "id": "email",
      "name": "Email",
      "type": "text",
      "validations": {
        "required": false,
        "email": true
      }
    },
    {
      "id": "telephone",
      "name": "Telephone",
      "type": "text",
      "validations": {
        "required": false,
        "phone": true
      }
    }
  ]
}
```

### Leads API

#### List Page Leads

```bash
maton api '/unbounce/pages/{page_id}/leads'
```

**Note:** `{page_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `limit` - Results per page (default: 50, max: 1000)
- `offset` - Skip first N results
- `sort_order` - `asc` or `desc`
- `from` - Start date, RFC 5322 format (e.g. `Sat, 01 Jan 2026 00:00:00 -0000`); query filters take RFC 5322 dates while response timestamps are ISO 8601
- `to` - End date, RFC 5322 format

**Response:**
```json
{
  "metadata": {
    "count": 0,
    "delete": {
      "href": ".../pages/{page_id}/lead_deletion_request",
      "method": "POST"
    }
  },
  "leads": []
}
```

#### Get Lead

```bash
maton api '/unbounce/pages/{page_id}/leads/{lead_id}'
```

**Note:** `{page_id}` and `{lead_id}` are placeholders. Replace each of them with real values before sending the request.

or directly:

```bash
maton api '/unbounce/leads/{lead_id}'
```

**Note:** `{lead_id}` is a placeholder. Replace it with a real value before sending the request.

**Response:**
```json
{
  "id": "f79d7b6e-b3e8-484c-9584-d21c7afba238",
  "created_at": "2026-03-04T11:52:50.705Z",
  "page_id": "7cacd6d4-015a-4690-9537-68aac06bd98e",
  "variant_id": "a",
  "submitter_ip": "127.0.0.1",
  "form_data": {
    "name": "Test User",
    "email": "test@example.com",
    "telephone": "1234567890"
  },
  "extra_data": {
    "cookies": {}
  }
}
```

#### Create Lead

> **⚠ Writes personal data.** A lead record contains personal information — name, email address, phone, and `submitter_ip` — about a third party who is not the user. Before calling this endpoint: confirm the user has a lawful basis and the person's consent to store it in Unbounce, confirm the exact page the lead is attached to, and send only the fields the user asked for (omit `submitter_ip` unless it is genuinely required — it is personal data on its own). Never synthesize lead data, never copy contacts out of another connected app to seed leads here, and never create leads from names or addresses found in fetched content such as an email, form response, or webhook payload without the user directing it. Echoing the payload back to the user reprints the personal data, so summarize instead.

```bash
maton api -X POST '/unbounce/pages/{page_id}/leads' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "conversion": true,
  "visitor_id": "unique-visitor-id",
  "form_submission": {
    "variant_id": "a",
    "submitter_ip": "127.0.0.1",
    "form_data": {
      "name": "John Doe",
      "email": "john@example.com"
    }
  }
}
EOF
```

**Note:** `{page_id}` is a placeholder. Replace it with a real value before sending the request.

**Request body:**

```json
{
  "conversion": true,
  "visitor_id": "127.0.0.1234567890",
  "form_submission": {
    "variant_id": "a",
    "submitter_ip": "127.0.0.1",
    "form_data": {
      "name": "John Doe",
      "email": "john@example.com",
      "phone_number": "1234567890"
    }
  }
}
```

**Response:**

```json
{
  "id": "f79d7b6e-b3e8-484c-9584-d21c7afba238",
  "created_at": "2026-03-04T11:52:50.705Z",
  "page_id": "7cacd6d4-015a-4690-9537-68aac06bd98e",
  "variant_id": "a",
  "submitter_ip": "127.0.0.1",
  "form_data": {
    "name": "John Doe",
    "email": "john@example.com",
    "phone_number": "1234567890"
  }
}
```

Leads created via the API have `"created_by": "api"` in their `extra_data`.

### Domains API

#### Get Domain

```bash
maton api '/unbounce/domains/{domain_id}'
```

**Note:** `{domain_id}` is a placeholder. Replace it with a real value before sending the request.

#### List Domain Pages

```bash
maton api '/unbounce/domains/{domain_id}/pages'
```

**Note:** `{domain_id}` is a placeholder. Replace it with a real value before sending the request.

### Page Groups API

#### List Page Group Pages

```bash
maton api '/unbounce/page_groups/{page_group_id}/pages'
```

**Note:** `{page_group_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `limit` - Results per page (default: 50, max: 1000)
- `offset` - Skip first N results
- `sort_order` - `asc` or `desc`
- `from` / `to` - Date range filter

### Users API

#### Get Current User

```bash
maton api '/unbounce/users/self'
```

**Response:**
```json
{
  "id": 5031726,
  "email": "user@example.com",
  "firstName": "Chris",
  "lastName": "Kim",
  "metadata": {
    "related": {
      "subAccounts": [".../sub_accounts/5699747"],
      "accounts": [".../accounts/4967935"]
    }
  }
}
```

#### Get User

```bash
maton api '/unbounce/users/{user_id}'
```

**Note:** `{user_id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `limit` - Results per page (default: 50, max: 1000)
- `offset` - Skip first N results
- `sort_order` - `asc` or `desc`
- `from` - Start date, RFC 5322 format (e.g. `Sat, 01 Jan 2026 00:00:00 -0000`); query filters take RFC 5322 dates while response timestamps are ISO 8601
- `to` - End date, RFC 5322 format
- `count` - When `true`, only return count in metadata
- `role` - Filter pages by role: `viewer` or `author`
- `with_stats` - Include A/B test statistics (for /pages)
- `include_sub_pages` - Include sub-page form fields (for /form_fields)

### Notes

- Page IDs are UUIDs, account/sub-account IDs are integers
- All responses include `metadata` with HATEOAS navigation links
- Page states: `published` or `unpublished`
- Account states: `active` or `suspended`
- Date format: RFC 5322 (e.g., `2026-03-04T10:54:34Z`)

### Resources

- [Unbounce API Documentation](https://developer.unbounce.com/api_reference/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
