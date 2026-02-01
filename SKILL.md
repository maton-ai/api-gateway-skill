---
name: api-gateway
description: |
  API gateway for calling third-party APIs with managed OAuth. Invoke this skill when users want to interact with external services like Slack, HubSpot, Salesforce, Google Workspace, Stripe, Shopify, and more.

  INVOKE THIS SKILL WHEN THE USER:
  - Asks to send, post, read, create, update, delete, list, get, or query data in any supported service
  - Mentions any of these services by name: Slack, HubSpot, Salesforce, Gmail, Google Sheets, Google Calendar, Google Drive, Google Docs, Google Forms, Google Analytics, Google Ads, Search Console, Notion, Jira, Stripe, Shopify, QuickBooks, Xero, Airtable, Typeform, JotForm, Apollo, Chargebee
  - Wants to automate or integrate with third-party platforms
  - Needs to access external APIs with OAuth authentication

  COMMON USER REQUESTS (trigger immediately):
  - "Send a message to Slack" / "Post to #general" / "DM someone on Slack" / "Message my team"
  - "Send an email" / "Check my inbox" / "Read my Gmail" / "Email this to..." / "What's in my email?"
  - "Create a calendar event" / "Schedule a meeting" / "What's on my calendar?" / "Add to my calendar"
  - "Read the spreadsheet" / "Update the Google Sheet" / "Add a row to the sheet" / "Get data from sheets"
  - "Upload to Drive" / "List my Google Drive files" / "Share a document" / "Find files in Drive"
  - "Create a contact in HubSpot" / "Add a lead" / "Update the deal" / "Search HubSpot"
  - "Query Salesforce" / "Run a SOQL query" / "Get contacts from Salesforce" / "Create a lead in SF"
  - "Create a Notion page" / "Query the Notion database" / "Update my Notion" / "Search Notion"
  - "Create a Jira ticket" / "File a bug" / "Search Jira issues" / "Update the issue" / "Add a comment to Jira"
  - "List Stripe customers" / "Get subscriptions" / "Create a charge" / "Refund a payment"
  - "Get Shopify orders" / "List products" / "Update inventory" / "Create a discount"
  - "Create a QuickBooks invoice" / "List customers in QB" / "Get the P&L report"
  - "Create a Xero invoice" / "Get contacts from Xero" / "Check balance sheet"
  - "Get Airtable records" / "Update the base" / "Query Airtable" / "Add a record"
  - "Get Typeform responses" / "Check form submissions" / "List JotForm entries"
  - "Search contacts in Apollo" / "Enrich this lead" / "Find company info"
  - "Check Google Analytics" / "Get website traffic" / "GA4 report" / "How's my site doing?"
  - "List my ad campaigns" / "Check Google Ads performance" / "Get ad metrics"
  - "Get Search Console data" / "Check search rankings" / "SEO performance"

  QUESTION-FORM TRIGGERS (also invoke this skill):
  - "Can you send a Slack message?" / "How do I post to Slack?"
  - "Can you check my email?" / "What emails do I have?"
  - "What's on my calendar today?" / "Do I have any meetings?"
  - "What's in this spreadsheet?" / "Can you read this Google Sheet?"
  - "Can you create a HubSpot contact?" / "How do I add a lead?"
  - "Can you query Salesforce?" / "What contacts are in Salesforce?"
  - "Can you help me with Jira?" / "How do I create a ticket?"
  - "What are my Stripe customers?" / "How many subscriptions do I have?"
  - "What orders came in on Shopify?" / "Can you check my store?"

  SUPPORTED SERVICES AND ACTIONS:

  MESSAGING & COLLABORATION:
  - Slack: send message, post to channel, DM user, list channels, get channel history, upload file, add reaction, create channel, invite user, set status, search messages
  - Notion: query database, create page, update page, search, add block, create database, get page content, archive page
  - Jira: create issue, update issue, search issues, JQL query, add comment, transition issue, get project, assign issue, create sprint, log work

  CRM & SALES:
  - HubSpot: create contact, update contact, create company, create deal, list contacts, search CRM, add note, create task, update deal stage, get pipeline
  - Salesforce: SOQL query, create record, update record, delete record, get object, describe object, search, create lead, update opportunity, get account

  GOOGLE WORKSPACE:
  - Gmail (Google Mail): send email, read inbox, get message, search emails, list labels, create draft, reply to email, add label, delete message, get attachments
  - Google Sheets (Spreadsheets): read spreadsheet, write cells, update range, append rows, create spreadsheet, get values, clear range, format cells, add sheet, delete rows
  - Google Calendar: create event, list events, update event, delete event, get calendar, list calendars, add attendee, set reminder, find free time, create recurring event
  - Google Drive: list files, upload file, download file, create folder, share file, move file, copy file, delete file, search files, get file metadata
  - Google Docs: create document, read document, update document, insert text, batch update, get content, replace text, add heading, insert image
  - Google Forms: get form, list responses, get response, create form, update form, add question

  ANALYTICS & MARKETING:
  - Google Analytics (GA4): run report, get metrics, get dimensions, list properties, get realtime data, create audience, get conversions, analyze traffic
  - Google Ads: list campaigns, get ad groups, create campaign, update campaign, get keywords, get ad performance, search ads, manage budgets
  - Google Search Console: get search analytics, list sitemaps, submit URL, get crawl errors, inspect URL, get performance data

  E-COMMERCE & PAYMENTS:
  - Stripe: list customers, create customer, get subscription, create payment, list invoices, refund payment, get balance, create product, update subscription, get charges
  - Shopify: list products, create product, get orders, update order, list customers, create customer, get inventory, update product, fulfill order, create discount
  - QuickBooks: create invoice, list customers, get reports, create customer, list invoices, get account balance, create payment, get profit and loss, list items, create estimate
  - Xero: create invoice, list contacts, get reports, create contact, list invoices, get balance sheet, create payment, reconcile, list accounts, create bill
  - Chargebee: list subscriptions, create subscription, get customer, cancel subscription, update subscription, list invoices, create customer, add addon

  DATA & PRODUCTIVITY:
  - Airtable: list records, create record, update record, delete record, get record, list tables, get base schema, search records, batch update, upload attachment
  - Typeform: list forms, get responses, get form, create form, get insights, list workspaces, update form, get response count
  - JotForm: list forms, get submissions, get form, create form, get form questions, delete submission, get reports

  PROSPECTING:
  - Apollo: search people, enrich contact, get person, list contacts, search companies, add to sequence, get company, bulk enrich, save contact

  Alternative names: Gmail=google-mail, Google Sheets=google-sheets, Spreadsheet=google-sheets,
  Google Calendar=google-calendar, Google Drive=google-drive, Google Docs=google-docs,
  Google Forms=google-forms, Google Ads=google-ads, Google Analytics=google-analytics-data,
  GA4=google-analytics-data, Search Console=google-search-console, GSC=google-search-console,
  QB=quickbooks, Intuit=quickbooks

  OAuth tokens are automatically injected. Refer to each service's official API documentation.
compatibility: Requires network access and valid Maton API key
metadata:
  author: maton
  version: "1.0"
---

# API Gateway

Passthrough proxy for direct access to third-party APIs using managed OAuth connections. The API gateway lets you call native API endpoints directly.

## Quick Start

```bash
# Native Slack API call
curl -s -X POST 'https://gateway.maton.ai/slack/api/chat.postMessage' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer YOUR_API_KEY' \
  -d '{"channel": "C0123456", "text": "Hello from gateway!"}'
```

## Base URL

```
https://gateway.maton.ai/{app}/{native-api-path}
```

Replace `{app}` with the service name and `{native-api-path}` with the actual API endpoint path.

## Authentication

All requests require the Maton API key in the Authorization header:

```
Authorization: Bearer YOUR_API_KEY
```

The API gateway automatically injects the appropriate OAuth token for the target service.

**Environment Variable:** You can set your API key as the `MATON_API_KEY` environment variable:

```bash
export MATON_API_KEY="YOUR_API_KEY"
```

## Getting Your API Key

1. Sign in or create an account at [maton.ai](https://maton.ai)
2. Go to [maton.ai/settings](https://maton.ai/settings)
3. Click the copy button on the right side of API Key section to copy it

## Connection Management

Connection management uses a separate base URL: `https://ctrl.maton.ai`

### List Connections

```bash
curl -s -X GET 'https://ctrl.maton.ai/connections?app=slack&status=ACTIVE' \
  -H 'Authorization: Bearer YOUR_API_KEY'
```

**Query Parameters (optional):**
- `app` - Filter by service name (e.g., `slack`, `hubspot`, `salesforce`)
- `status` - Filter by connection status (`ACTIVE`, `PENDING`, `FAILED`)

**Response:**
```json
{
  "connections": [
    {
      "connection_id": "21fd90f9-5935-43cd-b6c8-bde9d915ca80",
      "status": "ACTIVE",
      "creation_time": "2025-12-08T07:20:53.488460Z",
      "last_updated_time": "2026-01-31T20:03:32.593153Z",
      "url": "https://connect.maton.ai/?session_token=5e9...",
      "app": "slack",
      "metadata": {}
    }
  ]
}
```

### Create Connection

```bash
curl -s -X POST 'https://ctrl.maton.ai/connections' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer YOUR_API_KEY' \
  -d '{"app": "slack"}'
```

### Get Connection

```bash
curl -s -X GET 'https://ctrl.maton.ai/connections/21fd90f9-5935-43cd-b6c8-bde9d915ca80' \
  -H 'Authorization: Bearer YOUR_API_KEY'
```

**Response:**
```json
{
  "connection": {
    "connection_id": "21fd90f9-5935-43cd-b6c8-bde9d915ca80",
    "status": "ACTIVE",
    "creation_time": "2025-12-08T07:20:53.488460Z",
    "last_updated_time": "2026-01-31T20:03:32.593153Z",
    "url": "https://connect.maton.ai/?session_token=5e9...",
    "app": "slack",
    "metadata": {}
  }
}
```

Open the returned URL in a browser to complete OAuth.

### Delete Connection

```bash
curl -s -X DELETE 'https://ctrl.maton.ai/connections/21fd90f9-5935-43cd-b6c8-bde9d915ca80' \
  -H 'Authorization: Bearer YOUR_API_KEY'
```

### Specifying Connection

If you have multiple connections for the same app, you can specify which connection to use by adding the `Maton-Connection` header with the connection ID:

```bash
curl -s -X POST 'https://gateway.maton.ai/slack/api/chat.postMessage' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer YOUR_API_KEY' \
  -H 'Maton-Connection: 21fd90f9-5935-43cd-b6c8-bde9d915ca80' \
  -d '{"channel": "C0123456", "text": "Hello!"}'
```

If omitted, the gateway uses the default (oldest) active connection for that app.

## Supported Services

| Service | App Name | Base URL Proxied |
|---------|----------|------------------|
| Airtable | `airtable` | `api.airtable.com` |
| Apollo | `apollo` | `api.apollo.io` |
| Chargebee | `chargebee` | `{subdomain}.chargebee.com` |
| Google Ads | `google-ads` | `googleads.googleapis.com` |
| Google Analytics Admin | `google-analytics-admin` | `analyticsadmin.googleapis.com` |
| Google Analytics Data | `google-analytics-data` | `analyticsdata.googleapis.com` |
| Google Calendar | `google-calendar` | `www.googleapis.com` |
| Google Docs | `google-docs` | `docs.googleapis.com` |
| Google Drive | `google-drive` | `www.googleapis.com` |
| Google Forms | `google-forms` | `forms.googleapis.com` |
| Gmail | `google-mail` | `gmail.googleapis.com` |
| Google Search Console | `google-search-console` | `www.googleapis.com` |
| Google Sheets | `google-sheets` | `sheets.googleapis.com` |
| HubSpot | `hubspot` | `api.hubapi.com` |
| Jira | `jira` | `api.atlassian.com` |
| JotForm | `jotform` | `api.jotform.com` |
| Notion | `notion` | `api.notion.com` |
| QuickBooks | `quickbooks` | `quickbooks.api.intuit.com` |
| Salesforce | `salesforce` | `{instance}.salesforce.com` |
| Shopify | `shopify` | `{subdomain}.myshopify.com` (GraphQL API) |
| Slack | `slack` | `slack.com` |
| Stripe | `stripe` | `api.stripe.com` |
| Typeform | `typeform` | `api.typeform.com` |
| Xero | `xero` | `api.xero.com` |

See [references/](references/) for detailed routing guides per provider:
- [Airtable](references/airtable.md) - Records, bases, tables
- [Apollo](references/apollo.md) - People search, enrichment, contacts
- [Chargebee](references/chargebee.md) - Subscriptions, customers, invoices
- [Google Ads](references/google-ads.md) - Campaigns, ad groups, GAQL queries
- [Google Analytics Admin](references/google-analytics-admin.md) - Reports, dimensions, metrics
- [Google Analytics Data](references/google-analytics-data.md) - Reports, dimensions, metrics
- [Google Calendar](references/google-calendar.md) - Events, calendars, free/busy
- [Google Docs](references/google-docs.md) - Document creation, batch updates
- [Google Drive](references/google-drive.md) - Files, folders, permissions
- [Google Forms](references/google-forms.md) - Forms, questions, responses
- [Gmail](references/google-mail.md) - Messages, threads, labels
- [Google Search Console](references/google-search-console.md) - Search analytics, sitemaps
- [Google Sheets](references/google-sheets.md) - Values, ranges, formatting
- [HubSpot](references/hubspot.md) - Contacts, companies, deals
- [Jira](references/jira.md) - Issues, projects, JQL queries
- [JotForm](references/jotform.md) - Forms, submissions, webhooks
- [Notion](references/notion.md) - Pages, databases, blocks
- [QuickBooks](references/quickbooks.md) - Customers, invoices, reports
- [Salesforce](references/salesforce.md) - SOQL, sObjects, CRUD
- [Shopify](references/shopify.md) - **Uses GraphQL API (read this first)**, products, orders, customers
- [Slack](references/slack.md) - Messages, channels, users
- [Stripe](references/stripe.md) - Customers, subscriptions, payments
- [Typeform](references/typeform.md) - Forms, responses, insights
- [Xero](references/xero.md) - Contacts, invoices, reports

## Examples

### Slack - Post Message (Native API)

```bash
# Native Slack API: POST https://slack.com/api/chat.postMessage
curl -s -X POST 'https://gateway.maton.ai/slack/api/chat.postMessage' \
  -H 'Content-Type: application/json; charset=utf-8' \
  -H 'Authorization: Bearer YOUR_API_KEY' \
  -d '{"channel": "C0123456", "text": "Hello!"}'
```

### HubSpot - Create Contact (Native API)

```bash
# Native HubSpot API: POST https://api.hubapi.com/crm/v3/objects/contacts
curl -s -X POST 'https://gateway.maton.ai/hubspot/crm/v3/objects/contacts' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer YOUR_API_KEY' \
  -d '{
    "properties": {
      "email": "john@example.com",
      "firstname": "John",
      "lastname": "Doe"
    }
  }'
```

### Google Sheets - Get Spreadsheet Values (Native API)

```bash
# Native Sheets API: GET https://sheets.googleapis.com/v4/spreadsheets/{id}/values/{range}
curl -s -X GET 'https://gateway.maton.ai/google-sheets/v4/spreadsheets/122BS1sFN2RKL8AOUQjkLdubzOwgqzPT64KfZ2rvYI4M/values/Sheet1!A1:B2' \
  -H 'Authorization: Bearer YOUR_API_KEY'
```

### Salesforce - SOQL Query (Native API)

```bash
# Native Salesforce API: GET https://{instance}.salesforce.com/services/data/v64.0/query?q=...
curl -s -X GET 'https://gateway.maton.ai/salesforce/services/data/v64.0/query?q=SELECT+Id,Name+FROM+Contact+LIMIT+10' \
  -H 'Authorization: Bearer YOUR_API_KEY'
```

### Airtable - List Tables (Native API)

```bash
# Native Airtable API: GET https://api.airtable.com/v0/meta/bases/{id}/tables
curl -s -X GET 'https://gateway.maton.ai/airtable/v0/meta/bases/appgqan2NzWGP5sBK/tables' \
  -H 'Authorization: Bearer YOUR_API_KEY'
```

### Notion - Query Database (Native API)

```bash
# Native Notion API: POST https://api.notion.com/v1/data_sources/{id}/query
curl -s -X POST 'https://gateway.maton.ai/notion/v1/data_sources/23702dc5-9a3b-8001-9e1c-000b5af0a980/query' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer YOUR_API_KEY' \
  -H 'Notion-Version: 2025-09-03' \
  -d '{}'
```

### Stripe - List Customers (Native API)

```bash
# Native Stripe API: GET https://api.stripe.com/v1/customers
curl -s -X GET 'https://gateway.maton.ai/stripe/v1/customers?limit=10' \
  -H 'Authorization: Bearer YOUR_API_KEY'
```

## Code Examples

### JavaScript (Node.js)

```javascript
const response = await fetch('https://gateway.maton.ai/slack/api/chat.postMessage', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': `Bearer ${process.env.MATON_API_KEY}`
  },
  body: JSON.stringify({ channel: 'C0123456', text: 'Hello!' })
});
```

### Python

```python
import os
import requests

response = requests.post(
    'https://gateway.maton.ai/slack/api/chat.postMessage',
    headers={'Authorization': f'Bearer {os.environ["MATON_API_KEY"]}'},
    json={'channel': 'C0123456', 'text': 'Hello!'}
)
```

## Error Handling

| Status | Meaning |
|--------|---------|
| 400 | Missing connection for the requested app |
| 401 | Invalid or missing Maton API key |
| 429 | Rate limited (10 requests/second per account) |
| 4xx/5xx | Passthrough error from the target API |

Errors from the target API are passed through with their original status codes and response bodies.

## Rate Limits

- 10 requests per second per account
- Target API rate limits also apply

## Tips

1. **Use native API docs**: Refer to each service's official API documentation for endpoint paths and parameters.

2. **Headers are forwarded**: Custom headers (except `Host` and `Authorization`) are forwarded to the target API.

3. **Query params work**: URL query parameters are passed through to the target API.

4. **All HTTP methods supported**: GET, POST, PUT, PATCH, DELETE are all supported.

5. **QuickBooks special case**: Use `:realmId` in the path and it will be replaced with the connected realm ID.

## Optional

- [Documentation](https://www.maton.ai/docs/api-reference)
- [Community](https://discord.com/invite/dBfFAcefs2)
