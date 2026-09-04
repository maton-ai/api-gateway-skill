# Pipedrive Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `pipedrive`
**Base URL proxied:** `api.pipedrive.com`

> **Privacy — person and deal records are personal data about real people.** Persons carry names, email addresses, and phone numbers; activities and notes often add meeting context and private commentary. This is personal data under GDPR/CCPA, and the contacts themselves are third parties who gave their details to the user's company, not to an agent.
> - The sample values below (`John Doe`, `john@example.com`, `+1234567890`) are **placeholders**. Never send them to a live account, and never invent contact details to fill a required field — ask the user.
> - Retrieve only the records the task needs. Do not page through the full person or deal list to build a contact list, and do not enumerate a pipeline to browse.
> - Return the narrowest answer that satisfies the request; don't print full person records into output when the user asked one question.
> - **Never forward Pipedrive contact data to a third-party host** — not to a trigger destination, external webhook, spreadsheet service, or enrichment API — without explicit user approval for that specific transfer.
> - Confirm the exact person by name or email (not just a numeric ID) before any write, and never bulk-update or bulk-delete records without per-record approval.

## API Path Pattern

```
/pipedrive/api/v1/{resource}
```

## Common Endpoints

### List Deals
```bash
maton api '/pipedrive/api/v1/deals?status=open&limit=50'
```

### Get Deal
```bash
maton api '/pipedrive/api/v1/deals/{id}'
```

### Create Deal
```bash
maton api -X POST '/pipedrive/api/v1/deals' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "New Enterprise Deal",
  "value": 50000,
  "currency": "USD",
  "person_id": 123,
  "org_id": 456,
  "stage_id": 1,
  "expected_close_date": "2025-06-30"
}
EOF
```

### Update Deal
```bash
maton api -X PUT '/pipedrive/api/v1/deals/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "title": "Updated Deal Title",
  "value": 75000,
  "status": "won"
}
EOF
```

### Delete Deal
```bash
maton api -X DELETE '/pipedrive/api/v1/deals/{id}'
```

### Search Deals
```bash
maton api '/pipedrive/api/v1/deals/search?term=enterprise'
```

### List Persons
```bash
maton api '/pipedrive/api/v1/persons'
```

### Create Person
```bash
maton api -X POST '/pipedrive/api/v1/persons' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "John Doe",
  "email": ["john@example.com"],
  "phone": ["+1234567890"],
  "org_id": 456
}
EOF
```

### List Organizations
```bash
maton api '/pipedrive/api/v1/organizations'
```

### Create Organization
```bash
maton api -X POST '/pipedrive/api/v1/organizations' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Acme Corporation",
  "address": "123 Main St, City, Country"
}
EOF
```

### List Activities
```bash
maton api '/pipedrive/api/v1/activities?type=call&done=0'
```

### Create Activity
```bash
maton api -X POST '/pipedrive/api/v1/activities' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "subject": "Follow-up call",
  "type": "call",
  "due_date": "2025-03-15",
  "due_time": "14:00",
  "deal_id": 789,
  "person_id": 123
}
EOF
```

### List Pipelines
```bash
maton api '/pipedrive/api/v1/pipelines'
```

### List Stages
```bash
maton api '/pipedrive/api/v1/stages?pipeline_id=1'
```

### Create Note
```bash
maton api -X POST '/pipedrive/api/v1/notes' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "content": "Meeting notes: Discussed pricing and timeline",
  "deal_id": 789,
  "pinned_to_deal_flag": 1
}
EOF
```

### Get Current User
```bash
maton api '/pipedrive/api/v1/users/me'
```

## Notes

- IDs are integers
- Email and phone fields accept arrays for multiple values
- `visible_to` values: 1 (owner only), 3 (entire company), 5 (owner's visibility group), 7 (entire company and visibility group)
- Deal status: `open`, `won`, `lost`, `deleted`
- Use `start` and `limit` for pagination
- Custom fields are supported via their API key (e.g., `abc123_custom_field`)

## Resources

- [Pipedrive API Overview](https://developers.pipedrive.com/docs/api/v1)
- [Deals](https://developers.pipedrive.com/docs/api/v1/Deals)
- [Persons](https://developers.pipedrive.com/docs/api/v1/Persons)
- [Organizations](https://developers.pipedrive.com/docs/api/v1/Organizations)
- [Activities](https://developers.pipedrive.com/docs/api/v1/Activities)
- [Pipelines](https://developers.pipedrive.com/docs/api/v1/Pipelines)
- [Stages](https://developers.pipedrive.com/docs/api/v1/Stages)
- [Notes](https://developers.pipedrive.com/docs/api/v1/Notes)
