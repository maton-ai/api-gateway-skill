# Google Contacts Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `google-contacts`
**Base URL proxied:** `people.googleapis.com`

## API Path Pattern

```
/google-contacts/v1/{endpoint}
```

## Common Endpoints

### List Contacts
```bash
maton api '/google-contacts/v1/people/me/connections?personFields=names,emailAddresses,phoneNumbers&pageSize=100'
```

### Get Contact
```bash
maton api '/google-contacts/v1/people/{resourceName}?personFields=names,emailAddresses,phoneNumbers'
```

Example: `GET /google-contacts/v1/people/c1234567890?personFields=names,emailAddresses`

### Create Contact
```bash
maton api -X POST '/google-contacts/v1/people:createContact' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "names": [{"givenName": "John", "familyName": "Doe"}],
  "emailAddresses": [{"value": "john@example.com"}],
  "phoneNumbers": [{"value": "+1-555-0123"}]
}
EOF
```

### Update Contact
```bash
maton api -X PATCH '/google-contacts/v1/people/{resourceName}:updateContact?updatePersonFields=names,emailAddresses' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "etag": "%EgcBAgkLLjc9...",
  "names": [{"givenName": "John", "familyName": "Smith"}]
}
EOF
```

### Delete Contact
```bash
maton api -X DELETE '/google-contacts/v1/people/{resourceName}:deleteContact'
```

### Batch Get Contacts
```bash
maton api '/google-contacts/v1/people:batchGet?resourceNames=people/c123&resourceNames=people/c456&personFields=names'
```

### Batch Create Contacts
```bash
maton api -X POST '/google-contacts/v1/people:batchCreateContacts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "contacts": [{"contactPerson": {"names": [{"givenName": "Alice"}]}}],
  "readMask": "names"
}
EOF
```

### Batch Delete Contacts
```bash
maton api -X POST '/google-contacts/v1/people:batchDeleteContacts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "resourceNames": ["people/c123", "people/c456"]
}
EOF
```

### Search Contacts
```bash
maton api '/google-contacts/v1/people:searchContacts?query=John&readMask=names,emailAddresses'
```

### List Contact Groups
```bash
maton api '/google-contacts/v1/contactGroups?pageSize=100'
```

### Get Contact Group
```bash
maton api '/google-contacts/v1/contactGroups/{resourceName}?maxMembers=100'
```

### Create Contact Group
```bash
maton api -X POST '/google-contacts/v1/contactGroups' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "contactGroup": {"name": "Work Contacts"}
}
EOF
```

### Delete Contact Group
```bash
maton api -X DELETE '/google-contacts/v1/contactGroups/{resourceName}?deleteContacts=false'
```

### Modify Group Members
```bash
maton api -X POST '/google-contacts/v1/contactGroups/{resourceName}/members:modify' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "resourceNamesToAdd": ["people/c123"],
  "resourceNamesToRemove": ["people/c456"]
}
EOF
```

### List Other Contacts
```bash
maton api '/google-contacts/v1/otherContacts?readMask=names,emailAddresses&pageSize=100'
```

## Notes

- Resource names for contacts: `people/c{id}` (e.g., `people/c1234567890`)
- Resource names for groups: `contactGroups/{id}` (e.g., `contactGroups/starred`)
- System groups: `starred`, `friends`, `family`, `coworkers`, `myContacts`, `all`, `blocked`
- `personFields` parameter is required for most read operations
- Include `etag` when updating to prevent concurrent modification issues
- Pagination uses `pageToken` parameter

## Resources

- [Google People API Overview](https://developers.google.com/people/api/rest)
- [People Resource](https://developers.google.com/people/api/rest/v1/people)
- [Contact Groups Resource](https://developers.google.com/people/api/rest/v1/contactGroups)
