# Trello Routing Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../SKILL.md#security--permissions) for full security policy.

**App name:** `trello`
**Base URL proxied:** `api.trello.com`

## API Path Pattern

```
/trello/1/{resource}
```

## Common Endpoints

### Get Current Member
```bash
maton api '/trello/1/members/me'
```

Example:

```bash
maton trello whoami
```

### Get Member's Boards
```bash
maton api '/trello/1/members/me/boards?filter=open'
```

Example:

```bash
maton trello board list --filter open
```

### Get Board
```bash
maton api '/trello/1/boards/{id}?lists=open&cards=open'
```

Example:

```bash
maton trello board view {id} --lists open --cards open
```

### Create Board
```bash
maton api -X POST '/trello/1/boards' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Project Alpha",
  "desc": "Main project board",
  "defaultLists": false,
  "prefs_permissionLevel": "private"
}
EOF
```

Example:

```bash
maton trello board create --name 'Project Alpha' --desc 'Main project board' --permission private
```

### Get Board Lists
```bash
maton api '/trello/1/boards/{id}/lists?filter=open'
```

Example:

```bash
maton trello list list --board {id} --filter open
```

### Get Board Cards
```bash
maton api '/trello/1/boards/{id}/cards'
```

Example:

```bash
maton trello card list --board {id}
```

### Create List
```bash
maton api -X POST '/trello/1/lists' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "To Do",
  "idBoard": "BOARD_ID",
  "pos": "top"
}
EOF
```

Example:

```bash
maton trello list create --board BOARD_ID --name 'To Do' --pos top
```

### Get Cards in List
```bash
maton api '/trello/1/lists/{id}/cards'
```

Example:

```bash
maton trello card list --list {id}
```

### Get Card
```bash
maton api '/trello/1/cards/{id}?members=true&checklists=all'
```

Example:

```bash
maton trello card view {id} --members --checklists all
```

### Create Card
```bash
maton api -X POST '/trello/1/cards' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Implement feature X",
  "desc": "Description of the task",
  "idList": "LIST_ID",
  "pos": "bottom",
  "due": "2025-03-30T12:00:00.000Z",
  "idMembers": ["MEMBER_ID"],
  "idLabels": ["LABEL_ID"]
}
EOF
```

Example:

```bash
maton trello card create --list LIST_ID --name 'Implement feature X' --desc 'Description of the task' --due 2025-03-30T12:00:00.000Z --member-ids MEMBER_ID --label-ids LABEL_ID
```

### Update Card
```bash
maton api -X PUT '/trello/1/cards/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Updated card name",
  "desc": "Updated description",
  "due": "2025-04-15T12:00:00.000Z"
}
EOF
```

Example:

```bash
maton trello card update {id} --name 'Updated card name' --desc 'Updated description' --due 2025-04-15T12:00:00.000Z
```

### Move Card to List
```bash
maton api -X PUT '/trello/1/cards/{id}' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "idList": "NEW_LIST_ID",
  "pos": "top"
}
EOF
```

Example:

```bash
maton trello card update {id} --list NEW_LIST_ID
```

### Delete Card
```bash
maton api -X DELETE '/trello/1/cards/{id}'
```

Example:

```bash
maton trello card delete {id}
```

### Add Comment to Card
```bash
maton api -X POST '/trello/1/cards/{id}/actions/comments' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "text": "This is a comment"
}
EOF
```

Example:

```bash
maton trello card comment {id} --text 'This is a comment'
```

### Create Checklist
```bash
maton api -X POST '/trello/1/checklists' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "idCard": "CARD_ID",
  "name": "Task Checklist"
}
EOF
```

Example:

```bash
maton trello checklist create --card CARD_ID --name 'Task Checklist'
```

### Create Checklist Item
```bash
maton api -X POST '/trello/1/checklists/{id}/checkItems' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "Subtask 1",
  "pos": "bottom",
  "checked": false
}
EOF
```

Example:

```bash
maton trello checkitem create --checklist {id} --name 'Subtask 1' --pos bottom
```

### Get Board Labels
```bash
maton api '/trello/1/boards/{id}/labels'
```

Example:

```bash
maton trello label list --board {id}
```

### Create Label
```bash
maton api -X POST '/trello/1/labels' \
  -H 'Content-Type: application/json' \
  --input - <<'EOF'
{
  "name": "High Priority",
  "color": "red",
  "idBoard": "BOARD_ID"
}
EOF
```

Example:

```bash
maton trello label create --board BOARD_ID --name 'High Priority' --color red
```

### Search
```bash
maton api '/trello/1/search?query=keyword&modelTypes=cards,boards'
```

Example:

```bash
maton trello search --query keyword --models cards,boards
```

## Notes

- IDs are 24-character alphanumeric strings
- Use `me` to reference the authenticated user
- Dates are in ISO 8601 format
- `pos` can be `top`, `bottom`, or a positive number
- Label colors: `yellow`, `purple`, `blue`, `red`, `green`, `orange`, `black`, `sky`, `pink`, `lime`, `null`
- Use `fields` parameter to limit returned data and improve performance
- Archived items can be retrieved with `filter=closed`

## Resources

- [Trello API Overview](https://developer.atlassian.com/cloud/trello/rest/api-group-actions/)
- [Boards](https://developer.atlassian.com/cloud/trello/rest/api-group-boards/)
- [Lists](https://developer.atlassian.com/cloud/trello/rest/api-group-lists/)
- [Cards](https://developer.atlassian.com/cloud/trello/rest/api-group-cards/)
- [Checklists](https://developer.atlassian.com/cloud/trello/rest/api-group-checklists/)
- [Labels](https://developer.atlassian.com/cloud/trello/rest/api-group-labels/)
- [Members](https://developer.atlassian.com/cloud/trello/rest/api-group-members/)
- [Search](https://developer.atlassian.com/cloud/trello/rest/api-group-search/)
- [Maton CLI Manual](https://cli.maton.ai/manual)
