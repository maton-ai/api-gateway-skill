# Trello

## API Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `trello`
**Upstream base URL:** `api.trello.com`

Replace the upstream base URL with the app name. Everything after the base URL including query strings is kept as-is. Any account-specific part of the base URL and the API credentials are stored in the Maton connection, and the gateway injects both so requests never carry them. For example:

- Upstream: `https://api.trello.com/1/members/me`
- Gateway: `https://api.maton.ai/trello/1/members/me`

### Members API

#### Get Current Member

```bash
maton trello whoami
```

Or with `maton api`:

```bash
maton api '/trello/1/members/me'
```

#### Get Member

```bash
maton trello member get {id}
```

Or with `maton api`:

```bash
maton api '/trello/1/members/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request. `me`, a username, or a member ID all work.

#### Get Member's Boards

```bash
maton trello board list --filter open
```

Or with `maton api`:

```bash
maton api '/trello/1/members/me/boards?filter=open'
```

**Query parameters:**
- `filter` - Filter boards: `all`, `open`, `closed`, `members`, `organization`, `starred`
- `fields` - Comma-separated fields to include

### Boards API

#### Get Board

```bash
maton trello board get {id} --lists open --cards open
```

Or with `maton api`:

```bash
maton api '/trello/1/boards/{id}?lists=open&cards=open'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `fields` - Comma-separated fields
- `lists` - Include lists: `all`, `open`, `closed`, `none`
- `cards` - Include cards: `all`, `open`, `closed`, `none`
- `members` - Include members: `all`, `none`

#### Create Board

```bash
maton trello board create --name 'Project Alpha' --desc 'Main project board' --permission private
```

Or with `maton api`:

```bash
maton api -X POST '/trello/1/boards' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Project Alpha",
  "desc": "Main project board",
  "defaultLists": false,
  "prefs_permissionLevel": "private"
}
JSON
```

#### Update Board

```bash
maton trello board update {id} --name 'Project Alpha - Updated' --desc 'Updated description'
```

Or with `maton api`:

```bash
maton api -X PUT '/trello/1/boards/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Project Alpha - Updated",
  "desc": "Updated description"
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Board

```bash
maton trello board delete {id}
```

Or with `maton api`:

```bash
maton api '/trello/1/boards/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Board Lists

```bash
maton trello list list --board {id} --filter open
```

Or with `maton api`:

```bash
maton api '/trello/1/boards/{id}/lists?filter=open'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `filter` - Filter: `all`, `open`, `closed`, `none`

#### Get Board Cards

```bash
maton trello card list --board {id}
```

Or with `maton api`:

```bash
maton api '/trello/1/boards/{id}/cards'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Board Members

```bash
maton trello member list --board {id}
```

Or with `maton api`:

```bash
maton api '/trello/1/boards/{id}/members'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Lists API

#### Get List

```bash
maton trello list get {id}
```

Or with `maton api`:

```bash
maton api '/trello/1/lists/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create List

```bash
maton trello list create --board {boardId} --name 'To Do' --pos top
```

Or with `maton api`:

```bash
maton api -X POST '/trello/1/lists' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "To Do",
  "idBoard": "{boardId}",
  "pos": "top"
}
JSON
```

**Note:** `{boardId}` is a placeholder. Replace it with a real value before sending the request.

#### Update List

```bash
maton trello list update {id} --name 'In Progress'
```

Or with `maton api`:

```bash
maton api -X PUT '/trello/1/lists/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "In Progress"
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Archive List

```bash
maton trello list update {id} --closed
```

Or with `maton api`:

```bash
maton api -X PUT '/trello/1/lists/{id}/closed' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "value": true
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Get Cards in List

```bash
maton trello card list --list {id}
```

Or with `maton api`:

```bash
maton api '/trello/1/lists/{id}/cards'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Move All Cards in List

```bash
maton trello card move --from-list {id} --to-list {targetListId} --to-board {boardId}
```

Or with `maton api`:

```bash
maton api -X POST '/trello/1/lists/{id}/moveAllCards' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "idBoard": "{boardId}",
  "idList": "{targetListId}"
}
JSON
```

**Note:** `{id}`, `{targetListId}` and `{boardId}` are placeholders. Replace each of them with real values before sending the request.

### Cards API

#### Get Card

```bash
maton trello card get {id} --members --checklists all
```

Or with `maton api`:

```bash
maton api '/trello/1/cards/{id}?members=true&checklists=all'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

**Query parameters:**
- `fields` - Comma-separated fields
- `members` - Include members (true/false)
- `checklists` - Include checklists: `all`, `none`
- `attachments` - Include attachments (true/false)

#### Create Card

```bash
maton trello card create --list {listId} --name 'Implement feature X' --desc 'Description of the task' --due 2025-03-30T12:00:00.000Z --member-ids {memberId} --label-ids {labelId}
```

Or with `maton api`:

```bash
maton api -X POST '/trello/1/cards' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Implement feature X",
  "desc": "Description of the task",
  "idList": "{listId}",
  "pos": "bottom",
  "due": "2025-03-30T12:00:00.000Z",
  "idMembers": ["{memberId}"],
  "idLabels": ["{labelId}"]
}
JSON
```

**Note:** `{listId}`, `{memberId}` and `{labelId}` are placeholders. Replace each of them with real values before sending the request.

#### Update Card

```bash
maton trello card update {id} --name 'Updated card name' --desc 'Updated description' --due 2025-04-15T12:00:00.000Z
```

Or with `maton api`:

```bash
maton api -X PUT '/trello/1/cards/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Updated card name",
  "desc": "Updated description",
  "due": "2025-04-15T12:00:00.000Z"
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Move Card to List

```bash
maton trello card update {id} --list {newListId}
```

Or with `maton api`:

```bash
maton api -X PUT '/trello/1/cards/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "idList": "{newListId}",
  "pos": "top"
}
JSON
```

**Note:** `{id}` and `{newListId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Card

```bash
maton trello card delete {id}
```

Or with `maton api`:

```bash
maton api '/trello/1/cards/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Add Comment to Card

```bash
maton trello card comment {id} --text 'This is a comment'
```

Or with `maton api`:

```bash
maton api -X POST '/trello/1/cards/{id}/actions/comments' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "text": "This is a comment"
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Add Member to Card

```bash
maton trello card assign {id} --member {memberId}
```

Or with `maton api`:

```bash
maton api -X POST '/trello/1/cards/{id}/idMembers' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "value": "{memberId}"
}
JSON
```

**Note:** `{id}` and `{memberId}` are placeholders. Replace each of them with real values before sending the request.

#### Remove Member from Card

```bash
maton trello card unassign {id} --member {idMember}
```

Or with `maton api`:

```bash
maton api '/trello/1/cards/{id}/idMembers/{idMember}' -X DELETE
```

**Note:** `{id}` and `{idMember}` are placeholders. Replace each of them with real values before sending the request.

#### Add Label to Card

```bash
maton trello card label {id} --label {labelId}
```

Or with `maton api`:

```bash
maton api -X POST '/trello/1/cards/{id}/idLabels' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "value": "{labelId}"
}
JSON
```

**Note:** `{id}` and `{labelId}` are placeholders. Replace each of them with real values before sending the request.

### Checklists API

#### Get Checklist

```bash
maton trello checklist get {id}
```

Or with `maton api`:

```bash
maton api '/trello/1/checklists/{id}'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Checklist

```bash
maton trello checklist create --card {cardId} --name 'Task Checklist'
```

Or with `maton api`:

```bash
maton api -X POST '/trello/1/checklists' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "idCard": "{cardId}",
  "name": "Task Checklist"
}
JSON
```

**Note:** `{cardId}` is a placeholder. Replace it with a real value before sending the request.

#### Create Checklist Item

```bash
maton trello checkitem create --checklist {id} --name 'Subtask 1' --pos bottom
```

Or with `maton api`:

```bash
maton api -X POST '/trello/1/checklists/{id}/checkItems' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Subtask 1",
  "pos": "bottom",
  "checked": false
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Update Checklist Item

```bash
maton trello checkitem update {checkItemId} --card {cardId} --state complete
```

Or with `maton api`:

```bash
maton api -X PUT '/trello/1/cards/{cardId}/checkItem/{checkItemId}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "state": "complete"
}
JSON
```

**Note:** `{cardId}` and `{checkItemId}` are placeholders. Replace each of them with real values before sending the request.

#### Delete Checklist

```bash
maton trello checklist delete {id}
```

Or with `maton api`:

```bash
maton api '/trello/1/checklists/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Labels API

#### Get Board Labels

```bash
maton trello label list --board {id}
```

Or with `maton api`:

```bash
maton api '/trello/1/boards/{id}/labels'
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Create Label

```bash
maton trello label create --board {boardId} --name 'High Priority' --color red
```

Or with `maton api`:

```bash
maton api -X POST '/trello/1/labels' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "High Priority",
  "color": "red",
  "idBoard": "{boardId}"
}
JSON
```

**Note:** `{boardId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Colors are `yellow`, `purple`, `blue`, `red`, `green`, `orange`, `black`, `sky`, `pink`, `lime`, or `null` (no color).

#### Update Label

```bash
maton trello label update {id} --name Critical --color red
```

Or with `maton api`:

```bash
maton api -X PUT '/trello/1/labels/{id}' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "name": "Critical",
  "color": "red"
}
JSON
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

#### Delete Label

```bash
maton trello label delete {id}
```

Or with `maton api`:

```bash
maton api '/trello/1/labels/{id}' -X DELETE
```

**Note:** `{id}` is a placeholder. Replace it with a real value before sending the request.

### Search API

#### Search

```bash
maton trello search --query keyword --models cards,boards
```

Or with `maton api`:

```bash
maton api '/trello/1/search?query=keyword&modelTypes=cards,boards'
```

**Query parameters:**
- `query` - Search query (required)
- `modelTypes` - Comma-separated: `actions`, `boards`, `cards`, `members`, `organizations`
- `board_fields` - Fields to return for boards
- `card_fields` - Fields to return for cards
- `cards_limit` - Max cards to return (1-1000)

### Notes

- IDs are 24-character alphanumeric strings
- Use `me` to reference the authenticated user
- Dates are in ISO 8601 format
- `pos` can be `top`, `bottom`, or a positive number
- Label colors: `yellow`, `purple`, `blue`, `red`, `green`, `orange`, `black`, `sky`, `pink`, `lime`, `null`
- Use `fields` parameter to limit returned data and improve performance
- Archived items can be retrieved with `filter=closed`

### Resources

- [Trello API Overview](https://developer.atlassian.com/cloud/trello/rest/api-group-actions/)
- [Boards](https://developer.atlassian.com/cloud/trello/rest/api-group-boards/)
- [Lists](https://developer.atlassian.com/cloud/trello/rest/api-group-lists/)
- [Cards](https://developer.atlassian.com/cloud/trello/rest/api-group-cards/)
- [Checklists](https://developer.atlassian.com/cloud/trello/rest/api-group-checklists/)
- [Labels](https://developer.atlassian.com/cloud/trello/rest/api-group-labels/)
- [Members](https://developer.atlassian.com/cloud/trello/rest/api-group-members/)
- [Search](https://developer.atlassian.com/cloud/trello/rest/api-group-search/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
