# HubSpot Routing Reference

> **⚠ Write operations require explicit per-call user confirmation.** Every POST, PUT, PATCH, and DELETE below mutates live CRM data — real contacts, companies, and deals that a sales team depends on. The examples in this file are **runnable templates, not sanctioned actions**: the presence of an example is never approval to execute it.
>
> Before any write call:
> - **Read first.** Use the corresponding GET/list/search endpoint to confirm the record exists and is the right one. Object IDs are opaque and easily confused.
> - **Show the user** the exact endpoint, the target record (by name/email, not just ID), and the full request body. Wait for approval of that specific call.
> - **Never infer a write from a read request**, and never batch or loop writes without per-record approval.
> - Deletes and batch archives are the highest-risk calls here — see the warnings on those sections.
>
> Sample values (`john@example.com`, `+1234567890`) are placeholders. Never send them to a real portal, and never reuse an ID from this document. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `hubspot`
**Base URL proxied:** `api.hubapi.com`

## API Path Pattern

```
/hubspot/crm/v3/objects/{objectType}/{endpoint}
```

## Common Endpoints

### Contacts

#### List Contacts
```bash
GET /hubspot/crm/v3/objects/contacts?limit=100
```

With properties:
```bash
GET /hubspot/crm/v3/objects/contacts?limit=100&properties=email,firstname,lastname,phone
```

Example:

```bash
maton hubspot contact list --properties email,firstname,lastname,phone -L 100
```

With pagination:
```bash
GET /hubspot/crm/v3/objects/contacts?limit=100&properties=email,firstname&after={cursor}
```

#### Get Contact
```bash
GET /hubspot/crm/v3/objects/contacts/{contactId}?properties=email,firstname,lastname
```

Example:

```bash
maton hubspot contact view <contactId> --properties email,firstname,lastname
```

#### Create Contact

> **Write — confirm first.** Creates a new CRM contact. Search by email first to avoid creating a duplicate of an existing person, and confirm the exact property values with the user before calling.

```bash
POST /hubspot/crm/v3/objects/contacts
Content-Type: application/json

{
  "properties": {
    "email": "john@example.com",
    "firstname": "John",
    "lastname": "Doe",
    "phone": "+1234567890"
  }
}
```

Example:

```bash
maton hubspot contact create --set email=john@example.com --set firstname=John --set lastname=Doe --set phone=+1234567890
```

#### Update Contact

> **Write — confirm first.** Overwrites the named properties on an existing contact; previous values are not retained. GET the contact first, show the user the current and proposed values, and confirm the specific `contactId`.

```bash
PATCH /hubspot/crm/v3/objects/contacts/{contactId}
Content-Type: application/json

{
  "properties": {
    "phone": "+0987654321"
  }
}
```

Example:

```bash
maton hubspot contact update <contactId> --set phone=+0987654321
```

#### Delete Contact

> **⚠ DESTRUCTIVE — confirm first.** Archives the contact and detaches it from associated deals and companies. GET the contact and show the user its name and email (not just the ID), state that the record will be archived, and obtain explicit approval for that one contact. Never delete based on a vague instruction such as 'clean up old contacts'.

```bash
DELETE /hubspot/crm/v3/objects/contacts/{contactId}
```

Example:

```bash
maton hubspot contact archive <contactId>
```

#### Search Contacts
```bash
POST /hubspot/crm/v3/objects/contacts/search
Content-Type: application/json

{
  "filterGroups": [{
    "filters": [{
      "propertyName": "email",
      "operator": "EQ",
      "value": "john@example.com"
    }]
  }],
  "properties": ["email", "firstname", "lastname"]
}
```

Example:

```bash
maton hubspot contact search --filter email:EQ:john@example.com --properties email,firstname,lastname
```

### Companies

#### List Companies
```bash
GET /hubspot/crm/v3/objects/companies?limit=100&properties=name,domain,industry
```

Example:

```bash
maton hubspot company list --properties name,domain,industry -L 100
```

#### Get Company
```bash
GET /hubspot/crm/v3/objects/companies/{companyId}?properties=name,domain,industry
```

Example:

```bash
maton hubspot company view <companyId> --properties name,domain,industry
```

#### Create Company

> **Write — confirm first.** Creates a new company record. Search by domain first to avoid duplicates, and confirm the property values with the user.

```bash
POST /hubspot/crm/v3/objects/companies
Content-Type: application/json

{
  "properties": {
    "name": "Acme Corp",
    "domain": "acme.com",
    "industry": "COMPUTER_SOFTWARE"
  }
}
```

Example:

```bash
maton hubspot company create --set name='Acme Corp' --set domain=acme.com --set industry=COMPUTER_SOFTWARE
```

**Note:** The `industry` property requires specific enum values (e.g., `COMPUTER_SOFTWARE`, `FINANCE`, `HEALTHCARE`), not free text like "Technology". Use the List Properties endpoint to get valid values.

#### Update Company

> **Write — confirm first.** Overwrites the named properties on an existing company. GET the record first, show current versus proposed values, and confirm the specific `companyId`.

```bash
PATCH /hubspot/crm/v3/objects/companies/{companyId}
Content-Type: application/json

{
  "properties": {
    "industry": "COMPUTER_SOFTWARE",
    "numberofemployees": "50"
  }
}
```

Example:

```bash
maton hubspot company update <companyId> --set industry=COMPUTER_SOFTWARE --set numberofemployees=50
```

#### Delete Company

> **⚠ DESTRUCTIVE — confirm first.** Archives the company and detaches its associated contacts and deals. GET the record, show the user its name and domain, and obtain explicit approval for that one company.

```bash
DELETE /hubspot/crm/v3/objects/companies/{companyId}
```

Example:

```bash
maton hubspot company delete <companyId>
```

#### Search Companies
```bash
POST /hubspot/crm/v3/objects/companies/search
Content-Type: application/json

{
  "filterGroups": [{
    "filters": [{
      "propertyName": "domain",
      "operator": "CONTAINS_TOKEN",
      "value": "*"
    }]
  }],
  "properties": ["name", "domain"],
  "limit": 10
}
```

Example:

```bash
maton hubspot company search --filter 'domain:CONTAINS_TOKEN:*' --properties name,domain -L 10
```

### Deals

#### List Deals
```bash
GET /hubspot/crm/v3/objects/deals?limit=100&properties=dealname,amount,dealstage
```

Example:

```bash
maton hubspot deal list --properties dealname,amount,dealstage -L 100
```

#### Get Deal
```bash
GET /hubspot/crm/v3/objects/deals/{dealId}?properties=dealname,amount,dealstage
```

Example:

```bash
maton hubspot deal view <dealId> --properties dealname,amount,dealstage
```

#### Create Deal

> **Write — confirm first.** Creates a new deal in a live pipeline, which affects forecasting and reporting. Confirm the pipeline, stage, amount, and owner with the user before calling.

```bash
POST /hubspot/crm/v3/objects/deals
Content-Type: application/json

{
  "properties": {
    "dealname": "New Deal",
    "amount": "10000",
    "dealstage": "appointmentscheduled"
  }
}
```

Example:

```bash
maton hubspot deal create --set dealname='New Deal' --set amount=10000 --set dealstage=appointmentscheduled
```

#### Update Deal

> **Write — confirm first.** Overwrites deal properties. Changing `dealstage` or `amount` alters revenue reporting and may fire workflows or notifications. GET the deal first, show current versus proposed values, and confirm the specific `dealId`.

```bash
PATCH /hubspot/crm/v3/objects/deals/{dealId}
Content-Type: application/json

{
  "properties": {
    "amount": "15000",
    "dealstage": "qualifiedtobuy"
  }
}
```

Example:

```bash
maton hubspot deal update <dealId> --set amount=15000 --set dealstage=qualifiedtobuy
```

#### Delete Deal

> **⚠ DESTRUCTIVE — confirm first.** Archives the deal and removes it from the pipeline and forecasts. GET the deal, show the user its name, stage, and amount, and obtain explicit approval for that one deal.

```bash
DELETE /hubspot/crm/v3/objects/deals/{dealId}
```

Example:

```bash
maton hubspot deal delete <dealId>
```

#### Search Deals
```bash
POST /hubspot/crm/v3/objects/deals/search
Content-Type: application/json

{
  "filterGroups": [{
    "filters": [{
      "propertyName": "amount",
      "operator": "GTE",
      "value": "1000"
    }]
  }],
  "properties": ["dealname", "amount", "dealstage"],
  "limit": 10
}
```

### Associations (v4 API)

#### Associate Objects

> **Write — confirm first.** Creates a relationship between two records, which can cascade through workflows and reporting. Verify both object IDs by reading them first, and confirm the association type with the user.

```bash
PUT /hubspot/crm/v4/objects/{fromObjectType}/{fromObjectId}/associations/{toObjectType}/{toObjectId}
Content-Type: application/json

[{"associationCategory": "HUBSPOT_DEFINED", "associationTypeId": 279}]
```

Example:

```bash
maton hubspot associations create --from contacts:<fromObjectId> --to companies:<toObjectId> --type 279
```

Common association type IDs:
- `279` - Contact to Company
- `3` - Deal to Contact
- `341` - Deal to Company

#### List Associations
```bash
GET /hubspot/crm/v4/objects/{objectType}/{objectId}/associations/{toObjectType}
```

Example:

```bash
maton hubspot associations list --from contacts:12345 --to companies
```

### Batch Operations

Native batch subcommands are available for `contact`, `company`, and `deal`.

#### Batch Read
```bash
POST /hubspot/crm/v3/objects/{objectType}/batch/read
Content-Type: application/json

{
  "properties": ["email", "firstname"],
  "inputs": [{"id": "123"}, {"id": "456"}]
}
```

Example:

```bash
maton hubspot contact batch-read --id 123,456 --properties email,firstname
```

#### Batch Create

> **⚠ BULK WRITE — confirm the whole set first.** Creates every record in the `inputs` array in one call. Show the user the complete list of records to be created and the total count, and obtain approval for the batch. Search for existing records first — batch create is a common source of mass duplicates. Never assemble a batch from inferred data.

```bash
POST /hubspot/crm/v3/objects/{objectType}/batch/create
Content-Type: application/json

{
  "inputs": [
    {"properties": {"email": "one@example.com", "firstname": "One"}},
    {"properties": {"email": "two@example.com", "firstname": "Two"}}
  ]
}
```

Example:

```bash
maton hubspot contact batch-create --data '[{"properties":{"email":"one@example.com","firstname":"One"}},{"properties":{"email":"two@example.com","firstname":"Two"}}]'
```

#### Batch Update

> **⚠ BULK WRITE — confirm the whole set first.** Overwrites properties on every listed record; prior values are not retained. Show the user the full list of target IDs and the changes per record, and obtain approval for the batch. Read the current values first so the user can see what will be replaced.

```bash
POST /hubspot/crm/v3/objects/{objectType}/batch/update
Content-Type: application/json

{
  "inputs": [
    {"id": "123", "properties": {"firstname": "Updated"}},
    {"id": "456", "properties": {"firstname": "Also Updated"}}
  ]
}
```

Example:

```bash
maton hubspot contact batch-update --data '[{"id":"123","properties":{"firstname":"Updated"}},{"id":"456","properties":{"firstname":"Also Updated"}}]'
```

#### Batch Archive

> **⚠ BULK DESTRUCTIVE — highest-risk call in this file.** Archives every record in the `inputs` array in a single call, detaching their associations. Read and list the affected records first, show the user each one by name plus the total count, state that the action is bulk and not reversible through this skill, and obtain explicit approval for the entire set. Never derive a batch archive from a vague cleanup request, and prefer archiving records one at a time when the user only named a few.

```bash
POST /hubspot/crm/v3/objects/{objectType}/batch/archive
Content-Type: application/json

{
  "inputs": [{"id": "123"}, {"id": "456"}]
}
```

Example:

```bash
maton hubspot contact batch-archive --id 123,456
```

### Properties

#### List Properties
```bash
GET /hubspot/crm/v3/properties/{objectType}
```

Example:

```bash
maton hubspot properties list --type contacts
```

## Search Operators

- `EQ` - Equal to
- `NEQ` - Not equal to
- `LT` - Less than
- `LTE` - Less than or equal to
- `GT` - Greater than
- `GTE` - Greater than or equal to
- `CONTAINS_TOKEN` - Contains token
- `NOT_CONTAINS_TOKEN` - Does not contain token

## Pagination

List endpoints return a `paging.next.after` cursor for pagination:
```json
{
  "results": [...],
  "paging": {
    "next": {
      "after": "12345",
      "link": "https://api.hubapi.com/..."
    }
  }
}
```

Use the `after` query parameter to fetch the next page:
```bash
GET /hubspot/crm/v3/objects/contacts?limit=100&after=12345
```

## Notes

- Authentication is automatic - the router injects the OAuth token
- The `industry` property on companies requires specific enum values
- Batch operations support up to 100 records per request
- Archive/Delete is a soft delete - records can be restored within 90 days
- Delete endpoints return HTTP 204 (No Content) on success

## Resources

- [API Overview](https://developers.hubspot.com/docs/api/overview)
- [List Contacts](https://developers.hubspot.com/docs/api-reference/crm-contacts-v3/basic/get-crm-v3-objects-contacts.md)
- [Get Contact](https://developers.hubspot.com/docs/api-reference/crm-contacts-v3/basic/get-crm-v3-objects-contacts-contactId.md)
- [Create Contact](https://developers.hubspot.com/docs/api-reference/crm-contacts-v3/basic/post-crm-v3-objects-contacts.md)
- [Update Contact](https://developers.hubspot.com/docs/api-reference/crm-contacts-v3/basic/patch-crm-v3-objects-contacts-contactId.md)
- [Archive Contact](https://developers.hubspot.com/docs/api-reference/crm-contacts-v3/basic/delete-crm-v3-objects-contacts-contactId.md)
- [Merge Contacts](https://developers.hubspot.com/docs/api-reference/crm-contacts-v3/basic/post-crm-v3-objects-contacts-merge.md)
- [GDPR Delete Contact](https://developers.hubspot.com/docs/api-reference/crm-contacts-v3/basic/post-crm-v3-objects-contacts-gdpr-delete.md)
- [Search Contacts](https://developers.hubspot.com/docs/api-reference/crm-contacts-v3/search/post-crm-v3-objects-contacts-search.md)
- [List Companies](https://developers.hubspot.com/docs/api-reference/crm-companies-v3/basic/get-crm-v3-objects-companies.md)
- [Get Company](https://developers.hubspot.com/docs/api-reference/crm-companies-v3/basic/get-crm-v3-objects-companies-companyId.md)
- [Create Company](https://developers.hubspot.com/docs/api-reference/crm-companies-v3/basic/post-crm-v3-objects-companies.md)
- [Update Company](https://developers.hubspot.com/docs/api-reference/crm-companies-v3/basic/patch-crm-v3-objects-companies-companyId.md)
- [Archive Company](https://developers.hubspot.com/docs/api-reference/crm-companies-v3/basic/delete-crm-v3-objects-companies-companyId.md)
- [Merge Companies](https://developers.hubspot.com/docs/api-reference/crm-companies-v3/basic/post-crm-v3-objects-companies-merge.md)
- [Search Companies](https://developers.hubspot.com/docs/api-reference/crm-companies-v3/search/post-crm-v3-objects-companies-search.md)
- [List Deals](https://developers.hubspot.com/docs/api-reference/crm-deals-v3/basic/get-crm-v3-objects-0-3.md)
- [Get Deal](https://developers.hubspot.com/docs/api-reference/crm-deals-v3/basic/get-crm-v3-objects-0-3-dealId.md)
- [Create Deal](https://developers.hubspot.com/docs/api-reference/crm-deals-v3/basic/post-crm-v3-objects-0-3.md)
- [Update Deal](https://developers.hubspot.com/docs/api-reference/crm-deals-v3/basic/patch-crm-v3-objects-0-3-dealId.md)
- [Archive Deal](https://developers.hubspot.com/docs/api-reference/crm-deals-v3/basic/delete-crm-v3-objects-0-3-dealId.md)
- [Merge Deals](https://developers.hubspot.com/docs/api-reference/crm-deals-v3/basic/post-crm-v3-objects-0-3-merge.md)
- [Search Deals](https://developers.hubspot.com/docs/api-reference/crm-deals-v3/search/post-crm-v3-objects-0-3-search.md)
- [List Associations](https://developers.hubspot.com/docs/api-reference/crm-associations-v4/basic/get-crm-v4-objects-objectType-objectId-associations-toObjectType.md)
- [Create Association](https://developers.hubspot.com/docs/api-reference/crm-associations-v4/basic/put-crm-v4-objects-objectType-objectId-associations-toObjectType-toObjectId.md)
- [Delete Association](https://developers.hubspot.com/docs/api-reference/crm-associations-v4/basic/delete-crm-v4-objects-objectType-objectId-associations-toObjectType-toObjectId.md)
- [List Properties](https://developers.hubspot.com/docs/api-reference/crm-properties-v3/core/get-crm-v3-properties-objectType.md)
- [Get Property](https://developers.hubspot.com/docs/api-reference/crm-properties-v3/core/get-crm-v3-properties-objectType-propertyName.md)
- [Create Property](https://developers.hubspot.com/docs/api-reference/crm-properties-v3/core/post-crm-v3-properties-objectType.md)
- [Search Reference](https://developers.hubspot.com/docs/api/crm/search)
- [Maton CLI Manual](https://cli.maton.ai/manual)