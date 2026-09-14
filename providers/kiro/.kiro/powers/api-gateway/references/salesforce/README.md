# Salesforce

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

> **Privacy — Contact, Lead, and Account records are personal data about real people.** Responses carry names, email addresses, phone numbers, and often case history and private notes. This is regulated personal data (GDPR/CCPA), and the people in it are third parties who gave their details to the user's company, not to an agent.
> - Sample values below (`John Doe`, `john@example.com`, `+1234567890`) are **placeholders**. Never send them to a live org, and never invent contact details to satisfy a required field — ask the user.
> - Retrieve only the records the task needs. Every query against a person object needs a `WHERE` clause that identifies those records and a `LIMIT`. Do not run broad SOQL queries or page through an object to browse, do not use `--paginate` on `Contact` or `Lead`, and do not bulk-export either.
> - Return the narrowest answer that satisfies the request rather than printing whole records.
> - **Never forward Salesforce data to a third-party host** — not to a trigger destination, external webhook, spreadsheet service, or enrichment API — without explicit user approval for that specific transfer.
> - Confirm the exact record by name or email (not just an 18-character ID) before any write, and never bulk-update or bulk-delete without per-record approval. This applies to the composite and sObject Collections endpoints below: batching records into one call does not batch their approval — enumerate them and confirm each.

**App name:** `salesforce`
**Upstream base URL:** `{instance}.salesforce.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://{instance}.salesforce.com/services/data/v63.0/sobjects`
- Gateway: `https://api.maton.ai/salesforce/services/data/v63.0/sobjects`

### Query API

#### SOQL Query

Scope every query with a `WHERE` clause and a `LIMIT`. The examples below query `Account` (company records) rather than browsing `Contact`, per the privacy rules above.

```bash
maton salesforce query "SELECT Id,Name FROM Account WHERE Name LIKE 'Acme%' LIMIT 10"
```

Or with `maton api`:

```bash
maton api "/salesforce/services/data/v63.0/query?q=SELECT+Id,Name+FROM+Account+WHERE+Name+LIKE+'Acme%25'+LIMIT+10"
```

**Note:** Querying a person object requires a filter that identifies the specific records the task needs — a named account, an email address, or a date the user gave. Select only the fields required, and never `SELECT` a person object without a `WHERE`:

```bash
maton salesforce query "SELECT Id,Name,Email FROM Contact WHERE AccountId = '001XXXXXXXXXXXXXXX' LIMIT 25"
```

**Note:** Filtering by email domain still needs a bound — an `ORDER BY` is not one:

```bash
maton salesforce query "SELECT Id,Name,Email FROM Contact WHERE Email LIKE '%example.com' ORDER BY CreatedDate DESC LIMIT 25"
```

#### Search (SOSL)

```bash
maton salesforce search 'FIND {searchTerm} IN ALL FIELDS RETURNING Contact(Id,Name)'
```

Or with `maton api`:

```bash
maton api '/salesforce/services/data/v63.0/search?q=FIND+{searchTerm}+IN+ALL+FIELDS+RETURNING+Contact(Id,Name)'
```

**Note:** `{searchTerm}` is a placeholder. Replace it with a real value before sending the request.

### Records API

#### Get Object

```bash
maton salesforce record get {recordId} --type {objectType}
```

Or with `maton api`:

```bash
maton api '/salesforce/services/data/v63.0/sobjects/{objectType}/{recordId}'
```

**Note:** `{objectType}` and `{recordId}` are placeholders. Replace each of them with real values before sending the request.

#### Create Object

```bash
maton salesforce record create --type {objectType} --data '{"FirstName":"John","LastName":"Doe","Email":"john@example.com"}'
```

Or with `maton api`:

```bash
maton api -X POST '/salesforce/services/data/v63.0/sobjects/{objectType}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "FirstName": "John",
  "LastName": "Doe",
  "Email": "john@example.com"
}
JSON
```

**Note:** `{objectType}` is a placeholder. Replace it with a real value before sending the request.

#### Update Object

```bash
maton salesforce record update {recordId} --type {objectType} --data '{"Phone":"+1234567890"}'
```

Or with `maton api`:

```bash
maton api -X PATCH '/salesforce/services/data/v63.0/sobjects/{objectType}/{recordId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "Phone": "+1234567890"
}
JSON
```

**Note:** `{objectType}` and `{recordId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Object

```bash
maton salesforce record delete {recordId} --type {objectType}
```

Or with `maton api`:

```bash
maton api '/salesforce/services/data/v63.0/sobjects/{objectType}/{recordId}' -X DELETE
```

**Note:** `{objectType}` and `{recordId}` are placeholders. Replace each of them with real values before sending the request.

#### Get Updated Records

```bash
maton salesforce record list --type {objectType} --start 2026-01-30T00:00:00Z --end 2026-02-01T00:00:00Z
```

Or with `maton api`:

```bash
maton api '/salesforce/services/data/v63.0/sobjects/{objectType}/updated/?start=2026-01-30T00:00:00Z&end=2026-02-01T00:00:00Z'
```

**Note:** `{objectType}` is a placeholder. Replace it with a real value before sending the request.

#### Get Deleted Records

```bash
maton salesforce record list --type {objectType} --start 2026-01-30T00:00:00Z --end 2026-02-01T00:00:00Z --changes deleted
```

Or with `maton api`:

```bash
maton api '/salesforce/services/data/v63.0/sobjects/{objectType}/deleted/?start=2026-01-30T00:00:00Z&end=2026-02-01T00:00:00Z'
```

**Note:** `{objectType}` is a placeholder. Replace it with a real value before sending the request.

### Object Metadata API

#### List Objects

```bash
maton salesforce object list
```

Or with `maton api`:

```bash
maton api '/salesforce/services/data/v63.0/sobjects'
```

#### Describe Object (get schema)

```bash
maton salesforce object describe {objectType}
```

Or with `maton api`:

```bash
maton api '/salesforce/services/data/v63.0/sobjects/{objectType}/describe'
```

**Note:** `{objectType}` is a placeholder. Replace it with a real value before sending the request.

### Composite API

#### Composite Request (batch multiple operations)

```bash
echo '{"compositeRequest":[{"method":"GET","url":"/services/data/v63.0/sobjects/Contact/003XXXXXXX","referenceId":"contact1"},{"method":"GET","url":"/services/data/v63.0/sobjects/Account/001XXXXXXX","referenceId":"account1"}]}' \
  | maton salesforce composite call -F -
```

Or with `maton api`:

```bash
maton api -X POST '/salesforce/services/data/v63.0/composite' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "compositeRequest": [
    {
      "method": "GET",
      "url": "/services/data/v63.0/sobjects/Contact/003XXXXXXX",
      "referenceId": "contact1"
    },
    {
      "method": "GET",
      "url": "/services/data/v63.0/sobjects/Account/001XXXXXXX",
      "referenceId": "account1"
    }
  ]
}
JSON
```

#### Composite Batch Request

```bash
echo '{"batchRequests":[{"method":"GET","url":"v63.0/sobjects/Contact/003XXXXXXX"},{"method":"GET","url":"v63.0/sobjects/Account/001XXXXXXX"}]}' \
  | maton salesforce composite batch -F -
```

Or with `maton api`:

```bash
maton api -X POST '/salesforce/services/data/v63.0/composite/batch' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "batchRequests": [
    {"method": "GET", "url": "v63.0/sobjects/Contact/003XXXXXXX"},
    {"method": "GET", "url": "v63.0/sobjects/Account/001XXXXXXX"}
  ]
}
JSON
```

#### sObject Collections Create (batch create)

> **⚠ Batch writes still require per-record approval.** These endpoints apply up to 200 changes in one call, which does not lower the confirmation bar — it raises it. Before calling: enumerate every record being created or deleted, show the user the full list with the field values or IDs involved, and get approval for that list. Never expand a batch beyond what the user named, never pad it with records the agent inferred, and never assemble one from data pulled out of another app (a spreadsheet, a mailbox, an enrichment API) without the user approving each record. Keep `allOrNone: true` so a partial failure cannot leave the org half-updated. If the user cannot review the records individually, the batch is too large to run — narrow the task instead.

```bash
maton salesforce record create --all-or-none --data '[{"attributes":{"type":"Contact"},"FirstName":"John","LastName":"Doe"},{"attributes":{"type":"Contact"},"FirstName":"Jane","LastName":"Smith"}]'
```

Or with `maton api`:

```bash
maton api -X POST '/salesforce/services/data/v63.0/composite/sobjects' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "allOrNone": true,
  "records": [
    {"attributes": {"type": "Contact"}, "FirstName": "John", "LastName": "Doe"},
    {"attributes": {"type": "Contact"}, "FirstName": "Jane", "LastName": "Smith"}
  ]
}
JSON
```

#### sObject Collections Delete (batch delete)

> **⚠ Irreversible, and the IDs carry no context.** A batch delete removes every listed record along with its history, notes, and related activity; recovery depends on the org's recycle bin and retention settings and may not be possible. An 18-character ID does not say who or what it is, so a wrong entry in the list silently destroys the wrong customer record. Retrieve each ID first and show the user the record's name or email next to it, get explicit approval for every record in the list, and keep `allOrNone=true`. Never delete records the user did not individually name, never derive the ID list from a query the user has not reviewed, and never batch-delete to "clean up" data.

```bash
maton salesforce record delete 003XXXXX 003YYYYY --all-or-none
```

Or with `maton api`:

```bash
maton api '/salesforce/services/data/v63.0/composite/sobjects?ids=003XXXXX,003YYYYY&allOrNone=true' -X DELETE
```

### Org Info API

#### Get API Limits

```bash
maton salesforce limit get
```

Or with `maton api`:

```bash
maton api '/salesforce/services/data/v63.0/limits'
```

#### List API Versions

```bash
maton salesforce version list
```

Or with `maton api`:

```bash
maton api '/salesforce/services/data/'
```

### Common Objects

- `Account` - Companies/Organizations
- `Contact` - People associated with accounts
- `Lead` - Potential customers
- `Opportunity` - Sales deals
- `Case` - Support cases
- `Task` - To-do items
- `Event` - Calendar events

### Pagination

Salesforce uses cursor-based pagination. The CLI handles this automatically with `--paginate`:

```bash
maton salesforce query "SELECT Id,Name,StageName FROM Opportunity WHERE CloseDate = THIS_MONTH" --paginate
```

**Do not use `--paginate` on `Contact`, `Lead`, or any other person object.** Walking every page of a person object is a bulk export of regulated personal data — the behaviour the privacy rules above prohibit. Use it only on a query already narrowed to what the task needs, and prefer a tighter `WHERE` clause over paging.

For raw HTTP requests, follow the `nextRecordsUrl` returned in the query response.

### Notes

- Use URL encoding for SOQL queries (spaces become `+`)
- Record IDs are 15 or 18 character alphanumeric strings
- API version (v63.0) can be adjusted; latest is v65.0
- Update and Delete operations return HTTP 204 (no content) on success
- Dates for updated/deleted queries use ISO 8601 format: `YYYY-MM-DDTHH:MM:SSZ`
- Use `allOrNone: true` in batch operations for atomic transactions

### Resources

- [REST API Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_rest.htm)
- [List sObjects](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_describeGlobal.htm)
- [Describe sObject](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_sobject_describe.htm)
- [Get Record](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_sobject_retrieve_get.htm)
- [Get Record by External ID](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_sobject_upsert_get.htm)
- [Create Record](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/dome_sobject_create.htm)
- [Update Record](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/dome_update_fields.htm)
- [Delete Record](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/dome_delete_record.htm)
- [Upsert Record](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/dome_upsert.htm)
- [Query Records (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_query.htm)
- [Get Updated Records](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_getupdated.htm)
- [Get Deleted Records](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_getdeleted.htm)
- [Composite Request](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_composite_composite_post.htm)
- [Composite Batch Request](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/requests_composite_batch.htm)
- [Composite Batch Response](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/responses_composite_batch.htm)
- [Composite Graph](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_composite_graph.htm)
- [sObject Collections Create](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_composite_sobjects_collections_create.htm)
- [sObject Collections Update](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_composite_sobjects_collections_update.htm)
- [sObject Collections Delete](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_composite_sobjects_collections_delete.htm)
- [SOQL Reference](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm)
- [SOSL Reference](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_sosl.htm)
- [API Resources List](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_list.htm)
- [Maton CLI Manual](https://cli.maton.ai/manual)
