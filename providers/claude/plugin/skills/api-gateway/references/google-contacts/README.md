# Google Contacts

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `google-contacts`
**Upstream base URL:** `people.googleapis.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://people.googleapis.com/v1/people:createContact`
- Gateway: `https://api.maton.ai/google-contacts/v1/people:createContact`

### Contact API

#### List Contacts

```bash
maton api '/google-contacts/v1/people/me/connections?personFields=names,emailAddresses,phoneNumbers&pageSize=100'
```

**Query parameters:**
- `personFields` (required): Comma-separated list of fields to return (see Person Fields section)
- `pageSize`: Number of contacts to return (max 1000, default 100)
- `pageToken`: Token for pagination
- `sortOrder`: `LAST_MODIFIED_ASCENDING`, `LAST_MODIFIED_DESCENDING`, `FIRST_NAME_ASCENDING`, or `LAST_NAME_ASCENDING`

**Response:**
```json
{
  "connections": [
    {
      "resourceName": "people/c1234567890",
      "names": [{"displayName": "John Doe", "givenName": "John", "familyName": "Doe"}],
      "emailAddresses": [{"value": "john@example.com"}],
      "phoneNumbers": [{"value": "+1-555-0123"}]
    }
  ],
  "totalPeople": 1,
  "totalItems": 1,
  "nextPageToken": "..."
}
```

#### Get Contact

```bash
maton api '/google-contacts/v1/people/{resourceName}?personFields=names,emailAddresses,phoneNumbers'
```

**Note:** `{resourceName}` is a placeholder. Replace it with a real value before sending the request.

Example: `GET /google-contacts/v1/people/c1234567890?personFields=names,emailAddresses`

Use the resource name from list or create operations (e.g., `people/c1234567890`).

#### Create Contact

```bash
maton api -X POST '/google-contacts/v1/people:createContact' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "names": [{"givenName": "John", "familyName": "Doe"}],
  "emailAddresses": [{"value": "john@example.com"}],
  "phoneNumbers": [{"value": "+1-555-0123"}],
  "organizations": [{"name": "Acme Corp", "title": "Engineer"}]
}
JSON
```

#### Update Contact

```bash
maton api -X PATCH '/google-contacts/v1/people/{resourceName}:updateContact?updatePersonFields=names,emailAddresses' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "etag": "%EgcBAgkLLjc9...",
  "names": [{"givenName": "John", "familyName": "Smith"}],
  "emailAddresses": [{"value": "john.smith@example.com"}]
}
JSON
```

**Note:** `{resourceName}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Include the `etag` from the get/list response to ensure you're updating the latest version.

#### Delete Contact

```bash
maton api '/google-contacts/v1/people/{resourceName}:deleteContact' -X DELETE
```

**Note:** `{resourceName}` is a placeholder. Replace it with a real value before sending the request.

#### Batch Get Contacts

```bash
maton api '/google-contacts/v1/people:batchGet?resourceNames=people/c123&resourceNames=people/c456&personFields=names'
```

#### Batch Create Contacts

```bash
maton api -X POST '/google-contacts/v1/people:batchCreateContacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contacts": [
    {
      "contactPerson": {
        "names": [{"givenName": "Alice", "familyName": "Smith"}],
        "emailAddresses": [{"value": "alice@example.com"}]
      }
    },
    {
      "contactPerson": {
        "names": [{"givenName": "Bob", "familyName": "Jones"}],
        "emailAddresses": [{"value": "bob@example.com"}]
      }
    }
  ],
  "readMask": "names,emailAddresses"
}
JSON
```

#### Batch Delete Contacts

```bash
maton api -X POST '/google-contacts/v1/people:batchDeleteContacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "resourceNames": ["people/c123", "people/c456"]
}
JSON
```

#### Search Contacts

```bash
maton api '/google-contacts/v1/people:searchContacts?query=John&readMask=names,emailAddresses'
```

**Note:** Search results may have a slight delay for newly created contacts due to indexing.

### Contact Group API

#### List Contact Groups

```bash
maton api '/google-contacts/v1/contactGroups?pageSize=100'
```

**Response:**
```json
{
  "contactGroups": [
    {
      "resourceName": "contactGroups/starred",
      "groupType": "SYSTEM_CONTACT_GROUP",
      "name": "starred",
      "formattedName": "Starred"
    },
    {
      "resourceName": "contactGroups/abc123",
      "groupType": "USER_CONTACT_GROUP",
      "name": "Work",
      "formattedName": "Work",
      "memberCount": 5
    }
  ],
  "totalItems": 2
}
```

#### Get Contact Group

```bash
maton api '/google-contacts/v1/contactGroups/{resourceName}?maxMembers=100'
```

**Note:** `{resourceName}` is a placeholder. Replace it with a real value before sending the request.

Use `contactGroups/starred`, `contactGroups/family`, etc. for system groups, or the resource name for user groups.

#### Create Contact Group

```bash
maton api -X POST '/google-contacts/v1/contactGroups' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contactGroup": {
    "name": "Work Contacts"
  }
}
JSON
```

#### Delete Contact Group

```bash
maton api '/google-contacts/v1/contactGroups/{resourceName}?deleteContacts=false' -X DELETE
```

**Note:** `{resourceName}` is a placeholder. Replace it with a real value before sending the request.

Set `deleteContacts=true` to also delete the contacts in the group.

#### Batch Get Contact Groups

```bash
maton api '/google-contacts/v1/contactGroups:batchGet?resourceNames=contactGroups/starred&resourceNames=contactGroups/family'
```

#### Modify Group Members

Add or remove contacts from a group:

```bash
maton api -X POST '/google-contacts/v1/contactGroups/{resourceName}/members:modify' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "resourceNamesToAdd": ["people/c123", "people/c456"],
  "resourceNamesToRemove": ["people/c789"]
}
JSON
```

**Note:** `{resourceName}` is a placeholder. Replace it with a real value before sending the request.

### Other Contacts API

#### List Other Contacts

```bash
maton api '/google-contacts/v1/otherContacts?readMask=names,emailAddresses&pageSize=100'
```

#### Copy Other Contact to My Contacts

```bash
maton api -X POST '/google-contacts/v1/{resourceName}:copyOtherContactToMyContactsGroup' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "copyMask": "names,emailAddresses,phoneNumbers"
}
JSON
```

**Note:** `{resourceName}` is a placeholder. Replace it with a real value before sending the request.

### Person Fields

Use these fields with `personFields` or `readMask` parameters:

| Field | Description |
|-------|-------------|
| `names` | Display name, given name, family name |
| `emailAddresses` | Email addresses with type |
| `phoneNumbers` | Phone numbers with type |
| `addresses` | Postal addresses |
| `organizations` | Company, title, department |
| `biographies` | Bio/notes about the person |
| `birthdays` | Birthday information |
| `urls` | Website URLs |
| `photos` | Profile photos |
| `memberships` | Contact group memberships |
| `metadata` | Source and update information |

Multiple fields: `personFields=names,emailAddresses,phoneNumbers,organizations`

### Notes

- Resource names for contacts: `people/c{id}` (e.g., `people/c1234567890`)
- Resource names for groups: `contactGroups/{id}` (e.g., `contactGroups/starred`)
- System groups: `starred`, `friends`, `family`, `coworkers`, `myContacts`, `all`, `blocked`
- `personFields` parameter is required for most read operations
- Include `etag` when updating to prevent concurrent modification issues
- Pagination uses `pageToken` parameter

### Resources

- [Google People API Overview](https://developers.google.com/people/api/rest)
- [People Resource](https://developers.google.com/people/api/rest/v1/people)
- [Contact Groups Resource](https://developers.google.com/people/api/rest/v1/contactGroups)
- [Maton CLI Manual](https://cli.maton.ai/manual)
