# Notion Trigger Reference

> **Scope every trigger to the narrowest target.** All scoping parameters below are optional, and omitting one does not mean "no filter" — it means **match everything the connection can see**, i.e. the entire workspace. An unscoped Notion trigger continuously forwards page content and comments from every database and page the connected user has access to, including material unrelated to the task and potentially private to other workspace members.
>
> Before creating any trigger here:
> - Ask the user which specific database or page to watch and set the scoping parameter to that ID. Do not leave it blank for convenience.
> - Only create an unscoped (workspace-wide) trigger if the user explicitly asks to monitor the whole workspace — and say plainly that all pages/comments they can access will be forwarded to the destination.
> - Remember the trigger's payloads flow to its destinations, so an over-broad scope is an ongoing disclosure, not a one-time read.

## Event Types

- [`page.created`](#pagecreated)
- [`page.content_updated`](#pagecontent_updated)
- [`comment.created`](#commentcreated)

---

## `page.created`

### Parameters

- `parent_id` (string, optional): Database or page ID to scope to pages created inside it. **Set this.** Leaving it blank matches *every* parent, firing on every page created anywhere in the workspace the connection can access — confirm with the user before doing that.

### Sample Payload

```json
{
  "integration_id": "00000000-0000-0000-0000-0000000000c1",
  "data": {
    "parent": {
      "type": "page",
      "id": "00000000-0000-0000-0000-0000000000d2"
    }
  },
  "api_version": "2026-03-11",
  "type": "page.created",
  "workspace_id": "00000000-0000-0000-0000-0000000000c2",
  "subscription_id": "00000000-0000-0000-0000-0000000000c3",
  "accessible_by": [
    {
      "type": "person",
      "id": "00000000-0000-0000-0000-0000000000c4"
    },
    {
      "type": "bot",
      "id": "00000000-0000-0000-0000-0000000000c5"
    }
  ],
  "attempt_number": 1,
  "id": "00000000-0000-0000-0000-0000000000d3",
  "workspace_name": "Acme",
  "entity": {
    "type": "page",
    "id": "00000000-0000-0000-0000-0000000000d4"
  },
  "timestamp": "2026-06-24T21:44:44.441Z",
  "authors": [
    {
      "type": "person",
      "id": "00000000-0000-0000-0000-0000000000c4"
    }
  ]
}
```

---

## `page.content_updated`

### Parameters

- `page_id` (string, optional): Page ID to match only that page for block changes. **Set this.** Leaving it blank matches *all* pages, forwarding the content of every edit made anywhere in the accessible workspace — broad content monitoring that needs explicit user consent.

### Sample Payload

```json
{
  "integration_id": "00000000-0000-0000-0000-0000000000c1",
  "data": {
    "parent": {
      "type": "space",
      "id": "00000000-0000-0000-0000-0000000000c2"
    },
    "updated_blocks": [
      {
        "type": "block",
        "id": "00000000-0000-0000-0000-0000000000e1"
      },
      {
        "type": "block",
        "id": "00000000-0000-0000-0000-0000000000e2"
      },
      {
        "type": "block",
        "id": "00000000-0000-0000-0000-0000000000e3"
      },
      {
        "type": "block",
        "id": "00000000-0000-0000-0000-0000000000e4"
      }
    ]
  },
  "api_version": "2026-03-11",
  "type": "page.content_updated",
  "workspace_id": "00000000-0000-0000-0000-0000000000c2",
  "subscription_id": "00000000-0000-0000-0000-0000000000c3",
  "accessible_by": [
    {
      "type": "person",
      "id": "00000000-0000-0000-0000-0000000000c4"
    },
    {
      "type": "bot",
      "id": "00000000-0000-0000-0000-0000000000c5"
    }
  ],
  "attempt_number": 1,
  "id": "00000000-0000-0000-0000-0000000000d5",
  "workspace_name": "Acme",
  "entity": {
    "type": "page",
    "id": "00000000-0000-0000-0000-0000000000d1"
  },
  "timestamp": "2026-06-24T21:48:13.627Z",
  "authors": [
    {
      "type": "person",
      "id": "00000000-0000-0000-0000-0000000000c4"
    }
  ]
}
```

---

## `comment.created`

### Parameters

- `parent_id` (string, optional): Page ID to receive comment events only from that page. **Set this.** Leaving it blank matches *any* page, delivering every comment in the accessible workspace — including discussions by other members that may be private. Confirm before leaving it unscoped.

### Sample Payload

```json
{
  "integration_id": "00000000-0000-0000-0000-0000000000c1",
  "data": {
    "page_id": "00000000-0000-0000-0000-0000000000d1",
    "parent": {
      "type": "page",
      "id": "00000000-0000-0000-0000-0000000000d1"
    }
  },
  "api_version": "2026-03-11",
  "type": "comment.created",
  "workspace_id": "00000000-0000-0000-0000-0000000000c2",
  "subscription_id": "00000000-0000-0000-0000-0000000000c3",
  "accessible_by": [
    {
      "type": "person",
      "id": "00000000-0000-0000-0000-0000000000c4"
    },
    {
      "type": "bot",
      "id": "00000000-0000-0000-0000-0000000000c5"
    }
  ],
  "attempt_number": 1,
  "id": "00000000-0000-0000-0000-0000000000d6",
  "workspace_name": "Acme",
  "entity": {
    "type": "comment",
    "id": "00000000-0000-0000-0000-0000000000d7"
  },
  "timestamp": "2026-06-20T00:21:37.946Z",
  "authors": [
    {
      "type": "person",
      "id": "00000000-0000-0000-0000-0000000000c4"
    }
  ]
}
```