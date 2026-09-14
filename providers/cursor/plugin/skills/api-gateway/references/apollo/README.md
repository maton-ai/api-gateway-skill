# Apollo

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

> **⚠ Calls send data to Apollo, a third-party data broker.** Authentication is automatic — the gateway injects the user's previously connected Apollo API key — so a request here reaches an outside company under the user's account without any further prompt. Two consequences worth stating to the user before acting:
>
> - **Anything submitted leaves the user's systems.** Search terms, names, email addresses, domains, and any CRM or sales records pushed into Apollo are disclosed to Apollo and become part of that account's data. Never send a person's details to Apollo to "look them up" unless the user asked for that specific enrichment, and never relay records pulled from another connected app (a CRM, a mailbox, a spreadsheet) into Apollo without saying so first.
> - **What comes back is third-party personal data.** Contact and enrichment results are names, work emails, phone numbers, and employment details about people who did not provide them to the user. Retrieve only what the task needs, do not bulk-collect, and treat onward use as the user's compliance decision, not a default.
>
> Writes (creating or updating contacts, accounts, sequences) modify the user's real Apollo account and, for sequences, can cause outbound email to real recipients. Confirm the specific records and the intended effect first. Email enrichment also consumes paid credits.

**App name:** `apollo`
**Upstream base URL:** `api.apollo.io`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.apollo.io/v1/mixed_people/api_search`
- Gateway: `https://api.maton.ai/apollo/v1/mixed_people/api_search`

### People API

#### Search People

```bash
maton api -X POST '/apollo/v1/mixed_people/api_search' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "q_organization_name": "Google",
  "page": 1,
  "per_page": 25
}
JSON
```

#### Get Person

```bash
maton api '/apollo/v1/people/{personId}'
```

**Note:** `{personId}` is a placeholder. Replace it with a real value before sending the request.

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

### Organizations API

#### Search Organizations

```bash
maton api -X POST '/apollo/v1/organizations/search' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "q_organization_name": "Google",
  "page": 1,
  "per_page": 25
}
JSON
```

#### Enrich Organization

```bash
maton api -X POST '/apollo/v1/organizations/enrich' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "domain": "google.com"
}
JSON
```

### Contacts API

#### Search Contacts

```bash
maton api -X POST '/apollo/v1/contacts/search' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "page": 1,
  "per_page": 25
}
JSON
```

#### Create Contact

```bash
maton api -X POST '/apollo/v1/contacts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "first_name": "John",
  "last_name": "Doe",
  "email": "john@example.com",
  "organization_name": "Acme Corp"
}
JSON
```

#### Update Contact

```bash
maton api -X PUT '/apollo/v1/contacts/{contactId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "first_name": "Jane"
}
JSON
```

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

### Accounts API

#### Search Accounts

```bash
maton api -X POST '/apollo/v1/accounts/search' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "page": 1,
  "per_page": 25
}
JSON
```

#### Create Account

```bash
maton api -X POST '/apollo/v1/accounts' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Acme Corp",
  "domain": "acme.com"
}
JSON
```

### Sequences API

#### Search Sequences

```bash
maton api -X POST '/apollo/v1/emailer_campaigns/search' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "page": 1,
  "per_page": 25
}
JSON
```

#### Add Contact to Sequence

```bash
maton api -X POST '/apollo/v1/emailer_campaigns/{campaignId}/add_contact_ids' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "contact_ids": ["contact_id_1", "contact_id_2"]
}
JSON
```

**Note:** `{campaignId}` is a placeholder. Replace it with a real value before sending the request.

### Email API

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

**Note:** `{contactId}` is a placeholder. Replace it with a real value before sending the request.

### Labels API

#### List Labels

```bash
maton api '/apollo/v1/labels'
```

### Search Filters

Common search parameters:
- `q_organization_name` - Company name
- `q_person_title` - Job title
- `person_locations` - Array of locations
- `organization_num_employees_ranges` - Employee count ranges
- `q_keywords` - General keyword search

### Notes

- Pagination uses `page` and `per_page` parameters in POST body
- Most list endpoints use POST with `/search` suffix (not GET)
- Email enrichment consumes credits
- Rate limits apply per endpoint
- `people/search` and `mixed_people/search` are deprecated - use `mixed_people/api_search` instead

### Resources

- [Apollo API Overview](https://docs.apollo.io/reference)
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
- [Maton CLI Manual](https://cli.maton.ai/manual)
