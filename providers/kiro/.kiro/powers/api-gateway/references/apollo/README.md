# Apollo Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `apollo`
**Base URL proxied:** `api.apollo.io`

## API Path Pattern

```
/apollo/v1/{endpoint}
```

## Common Endpoints

### People

#### Search People
```bash
maton api -X POST '/apollo/v1/mixed_people/api_search' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "q_organization_name": "Google",
  "page": 1,
  "per_page": 25
}
EOF
```

#### Get Person
```bash
maton api '/apollo/v1/people/{personId}'
```

#### Enrich Person
```bash
maton api -X POST '/apollo/v1/people/match' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "email": "john@example.com"
}
EOF
```

Or by LinkedIn:
```bash
maton api -X POST '/apollo/v1/people/match' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "linkedin_url": "https://linkedin.com/in/johndoe"
}
EOF
```

### Organizations

#### Search Organizations
```bash
maton api -X POST '/apollo/v1/organizations/search' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "q_organization_name": "Google",
  "page": 1,
  "per_page": 25
}
EOF
```

#### Enrich Organization
```bash
maton api -X POST '/apollo/v1/organizations/enrich' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "domain": "google.com"
}
EOF
```

### Contacts

#### Search Contacts
```bash
maton api -X POST '/apollo/v1/contacts/search' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "page": 1,
  "per_page": 25
}
EOF
```

#### Create Contact
```bash
maton api -X POST '/apollo/v1/contacts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "first_name": "John",
  "last_name": "Doe",
  "email": "john@example.com",
  "organization_name": "Acme Corp"
}
EOF
```

#### Update Contact
```bash
maton api -X PUT '/apollo/v1/contacts/{contactId}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "first_name": "Jane"
}
EOF
```

### Accounts

#### Search Accounts
```bash
maton api -X POST '/apollo/v1/accounts/search' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "page": 1,
  "per_page": 25
}
EOF
```

#### Create Account
```bash
maton api -X POST '/apollo/v1/accounts' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Acme Corp",
  "domain": "acme.com"
}
EOF
```

### Sequences

#### Search Sequences
```bash
maton api -X POST '/apollo/v1/emailer_campaigns/search' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "page": 1,
  "per_page": 25
}
EOF
```

#### Add Contact to Sequence
```bash
maton api -X POST '/apollo/v1/emailer_campaigns/{campaignId}/add_contact_ids' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "contact_ids": ["contact_id_1", "contact_id_2"]
}
EOF
```

### Email

#### Search Email Messages
```bash
maton api -X POST '/apollo/v1/emailer_messages/search' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "contact_id": "{contactId}"
}
EOF
```

### Labels

#### List Labels
```bash
maton api '/apollo/v1/labels'
```

## Search Filters

Common search parameters:
- `q_organization_name` - Company name
- `q_person_title` - Job title
- `person_locations` - Array of locations
- `organization_num_employees_ranges` - Employee count ranges
- `q_keywords` - General keyword search

## Notes

- Authentication is automatic - the router injects the API key
- Pagination uses `page` and `per_page` parameters in POST body
- Most list endpoints use POST with `/search` suffix (not GET)
- Email enrichment consumes credits
- Rate limits apply per endpoint
- `people/search` and `mixed_people/search` are deprecated - use `mixed_people/api_search` instead

## Resources

- [API Overview](https://docs.apollo.io/reference)
- [Search People](https://docs.apollo.io/reference/people-api-search.md)
- [Enrich Person](https://docs.apollo.io/reference/people-enrichment.md)
- [Search Organizations](https://docs.apollo.io/reference/organization-search.md)
- [Enrich Organization](https://docs.apollo.io/reference/organization-enrichment.md)
- [Search Contacts](https://docs.apollo.io/reference/search-for-contacts.md)
- [Create Contact](https://docs.apollo.io/reference/create-a-contact.md)
- [Update Contact](https://docs.apollo.io/reference/update-a-contact.md)
- [Search Accounts](https://docs.apollo.io/reference/search-for-accounts.md)
- [Create Account](https://docs.apollo.io/reference/create-an-account.md)
- [Search Sequences](https://docs.apollo.io/reference/search-for-sequences.md)
- [Add Contacts to Sequence](https://docs.apollo.io/reference/add-contacts-to-sequence.md)
- [Search Email Messages](https://docs.apollo.io/reference/search-for-outreach-emails.md)
- [List Labels](https://docs.apollo.io/reference/get-a-list-of-all-lists.md)
- [LLM Reference](https://docs.apollo.io/llms.txt)