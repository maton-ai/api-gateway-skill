# Gmail Trigger Reference

> **Email events forward private correspondence.** `email.received` payloads include sender and recipient addresses, subject lines, and message snippets — and the trigger fires on mail the user did not individually review. Correspondence routinely contains credentials, password resets, financial details, health information, and third-party confidences.
>
> Before attaching a destination:
> - Scope the trigger as narrowly as the task allows (a specific label or query, not all of `INBOX`) so unrelated mail is not swept in.
> - Understand that every matching message will be forwarded automatically and continuously to the destination URL. This is persistent disclosure of the user's mailbox, not a one-time read.
> - **Strongly prefer `https://api.maton.ai/` destinations.** Sending mail contents to a third-party host requires explicit, informed user approval — state the exact host and what will be transmitted.
> - Use `body_template` to forward the minimum needed (e.g. subject only, not the snippet) and never relay the full payload by default.
> - Never place credentials in destination headers or body templates.

## Event Types

- [`email.received`](#emailreceived)

---

## `email.received`

### Parameters

- `labels` (string[], optional, default `["INBOX"]`): Label IDs that must all be present for the trigger to fire.

System label IDs:

| Label ID | Meaning |
|----------|---------|
| `INBOX` | In the inbox |
| `DRAFT` | Unsent draft |
| `UNREAD` | Not yet read |
| `STARRED` | Starred |
| `IMPORTANT` | Marked important |
| `SPAM` | In spam |

Other system labels may exist; list the account's labels to see all label IDs (system and custom).

### Sample Payload

```json
{
  "threadId": "19ee22f009a785f5",
  "snippet": "Delivered in as little as 25 minutes.",
  "labelIds": [
    "CATEGORY_PROMOTIONS",
    "UNREAD",
    "INBOX"
  ],
  "payload": {
    "headers": [
      {
        "name": "Delivered-To",
        "value": "recipient@example.com"
      }
    ],
    "mimeType": "text/html"
  },
  "historyId": "14215811",
  "id": "19ee22f009a785f5",
  "sizeEstimate": 131819,
  "internalDate": "1781911191000"
}
```