# Sunsama MCP

## MCP Reference

> **Safety:** All write operations (POST, PUT, PATCH, DELETE) require explicit user confirmation before execution. Verify the target resource and intended effect with the user first. See the main [SKILL.md](../../SKILL.md#security--permissions) for full security policy.

**App name:** `sunsama`
**Upstream base URL:** the Sunsama MCP server

This app is reached over MCP so there is no upstream REST path to rewrite. Each MCP tool is a `POST` to the app name followed by the tool name; the arguments go in the JSON body. The MCP credentials are stored in the Maton connection, and the gateway injects them so requests never carry them. For example: `https://api.maton.ai/sunsama/search_tasks`

All MCP tools use `POST` method:

| Tool | Description | Schema |
|------|-------------|--------|
| `search_tasks` | Search tasks by term | [schema](schemas/search_tasks.json) |
| `create_task` | Create a new task | [schema](schemas/create_task.json) |
| `edit_task_title` | Update task title | [schema](schemas/edit_task_title.json) |
| `delete_task` | Delete a task | [schema](schemas/delete_task.json) |
| `mark_task_as_completed` | Mark task complete | [schema](schemas/mark_task_as_completed.json) |
| `mark_task_as_incomplete` | Mark task incomplete | [schema](schemas/mark_task_as_incomplete.json) |
| `append_task_notes` | Add notes to task | [schema](schemas/append_task_notes.json) |
| `edit_task_time_estimate` | Set time estimate | [schema](schemas/edit_task_time_estimate.json) |
| `edit_task_recurrence_rule` | Set recurrence | [schema](schemas/edit_task_recurrence_rule.json) |
| `get_task_time_estimate` | Get AI time estimate | [schema](schemas/get_task_time_estimate.json) |
| `restore_task` | Restore deleted task | [schema](schemas/restore_task.json) |
| `add_subtasks_to_task` | Add subtasks | [schema](schemas/add_subtasks_to_task.json) |
| `edit_subtask_title` | Update subtask title | [schema](schemas/edit_subtask_title.json) |
| `mark_subtask_as_completed` | Mark subtask complete | [schema](schemas/mark_subtask_as_completed.json) |
| `mark_subtask_as_incomplete` | Mark subtask incomplete | [schema](schemas/mark_subtask_as_incomplete.json) |
| `get_backlog_tasks` | List backlog tasks | [schema](schemas/get_backlog_tasks.json) |
| `move_task_to_backlog` | Move task to backlog | [schema](schemas/move_task_to_backlog.json) |
| `move_task_from_backlog` | Move from backlog to day | [schema](schemas/move_task_from_backlog.json) |
| `reposition_task_in_backlog` | Reorder backlog task | [schema](schemas/reposition_task_in_backlog.json) |
| `change_backlog_folder` | Change task folder | [schema](schemas/change_backlog_folder.json) |
| `create_braindump_task` | Create backlog task | [schema](schemas/create_braindump_task.json) |
| `move_task_to_day` | Reschedule task | [schema](schemas/move_task_to_day.json) |
| `reorder_tasks` | Reorder day's tasks | [schema](schemas/reorder_tasks.json) |
| `timebox_a_task_to_calendar` | Block time for task | [schema](schemas/timebox_a_task_to_calendar.json) |
| `set_shutdown_time` | Set daily end time | [schema](schemas/set_shutdown_time.json) |
| `create_calendar_event` | Create calendar event | [schema](schemas/create_calendar_event.json) |
| `delete_calendar_event` | Delete calendar event | [schema](schemas/delete_calendar_event.json) |
| `move_calendar_event` | Reschedule event | [schema](schemas/move_calendar_event.json) |
| `import_task_from_calendar_event` | Import event as task | [schema](schemas/import_task_from_calendar_event.json) |
| `set_calendar_event_allow_task_projections` | Toggle task overlap | [schema](schemas/set_calendar_event_allow_task_projections.json) |
| `accept_meeting_invite` | RSVP yes to a meeting — **visible to organizer and attendees, confirm first** | [schema](schemas/accept_meeting_invite.json) |
| `decline_meeting_invite` | RSVP no to a meeting — **visible to organizer and attendees, confirm first** | [schema](schemas/decline_meeting_invite.json) |
| `start_task_timer` | Start timer | [schema](schemas/start_task_timer.json) |
| `stop_task_timer` | Stop timer | [schema](schemas/stop_task_timer.json) |
| `create_channel` | Create channel/context | [schema](schemas/create_channel.json) |
| `add_task_to_channel` | Assign task to channel | [schema](schemas/add_task_to_channel.json) |
| `create_weekly_objective` | Create weekly goal | [schema](schemas/create_weekly_objective.json) |
| `align_task_with_objective` | Link task to objective | [schema](schemas/align_task_with_objective.json) |
| `get_archived_tasks` | List archived tasks | [schema](schemas/get_archived_tasks.json) |
| `unarchive_task` | Restore archived task | [schema](schemas/unarchive_task.json) |
| `list_email_threads` | List email threads | [schema](schemas/list_email_threads.json) |
| `create_follow_up_task_from_email` | Create task from email | [schema](schemas/create_follow_up_task_from_email.json) |
| `delete_email_thread` | Delete email thread | [schema](schemas/delete_email_thread.json) |
| `mark_email_thread_as_read` | Mark email as read | [schema](schemas/mark_email_thread_as_read.json) |
| `delete_all_incomplete_recurring_task_instances` | Delete future recurrences | [schema](schemas/delete_all_incomplete_recurring_task_instances.json) |
| `update_all_incomplete_recurring_task_instances` | Update future recurrences | [schema](schemas/update_all_incomplete_recurring_task_instances.json) |
| `toggle_auto_import_events` | Toggle event auto-import | [schema](schemas/toggle_auto_import_events.json) |
| `update_calendar_preferences` | Update calendar settings | [schema](schemas/update_calendar_preferences.json) |
| `update_import_event_filters` | Set event filters | [schema](schemas/update_import_event_filters.json) |
| `log_user_feedback` | Send feedback to Sunsama — **retained externally, confirm wording** | [schema](schemas/log_user_feedback.json) |

### Common Tools

#### Search Tasks Tool

Search for tasks by keyword:
```bash
maton api -X POST '/sunsama/search_tasks' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "searchTerm": "meeting"
}
JSON
```

**Response:**
```json
{
  "content": [
    {
      "type": "text",
      "text": "{\"tasks\":[{\"_id\":\"69a6bf3a04d3cd0001595308\",\"title\":\"Team meeting prep\",\"scheduledDate\":\"2026-03-03\",\"completed\":false}]}"
    }
  ],
  "isError": false
}
```

#### Create Task Tool

Create a new task scheduled for a specific day:
```bash
maton api -X POST '/sunsama/create_task' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Review quarterly report",
  "day": "2026-03-03",
  "alreadyInTaskList": false
}
JSON
```

**Response:**
```json
{
  "content": [
    {
      "type": "text",
      "text": "{\"success\":true,\"task\":{\"_id\":\"69a6bf3a04d3cd0001595308\",\"title\":\"Review quarterly report\",\"notes\":\"\",\"timeEstimate\":\"20 minutes\",\"sortOrder\":-1772535610535,\"isPersonal\":false,\"isWork\":true,\"isPrivate\":false,\"isArchived\":false,\"completed\":false,\"isBacklogged\":false,\"scheduledDate\":\"2026-03-03\",\"subtasks\":[],\"channel\":\"work\",\"folder\":null,\"timeboxEventIds\":[]}}"
    }
  ],
  "isError": false
}
```

#### Get Backlog Tasks Tool

List all tasks in the backlog:
```bash
maton api -X POST '/sunsama/get_backlog_tasks' -H 'Content-Type: application/json' --input - <<'JSON'
{}
JSON
```

**Response:**
```json
{
  "content": [
    {
      "type": "text",
      "text": "{\"tasks\":[],\"queryId\":\"bb7d004a-0b29-49d9-8345-6d9037786fbb\",\"totalPages\":1}"
    }
  ],
  "isError": false
}
```

#### Mark Task as Completed Tool

```bash
maton api -X POST '/sunsama/mark_task_as_completed' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "taskId": "69a6bf3a04d3cd0001595308",
  "finishedDay": "2026-03-03"
}
JSON
```

#### Add Subtasks to Task Tool

```bash
maton api -X POST '/sunsama/add_subtasks_to_task' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "taskId": "69a6bf3a04d3cd0001595308",
  "subtasks": [
    {"title": "Step 1: Research"},
    {"title": "Step 2: Draft outline"},
    {"title": "Step 3: Review"}
  ]
}
JSON
```

#### Create Calendar Event Tool

```bash
maton api -X POST '/sunsama/create_calendar_event' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Team standup",
  "startDate": "2026-03-03T09:00:00"
}
JSON
```

#### Move Task to Day Tool

Reschedule a task to a different day:
```bash
maton api -X POST '/sunsama/move_task_to_day' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "taskId": "69a6bf3a04d3cd0001595308",
  "calendarDay": "2026-03-04"
}
JSON
```

#### Timebox Task to Calendar Tool

Block time for a task on your calendar:
```bash
maton api -X POST '/sunsama/timebox_a_task_to_calendar' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "taskId": "69a6bf3a04d3cd0001595308",
  "startDate": "2026-03-03",
  "startTime": "14:00"
}
JSON
```

#### Create Weekly Objective Tool

```bash
maton api -X POST '/sunsama/create_weekly_objective' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Complete Q1 planning",
  "weekStartDay": "2026-03-03"
}
JSON
```

#### Create Braindump Task Tool

Create a backlog task with a time bucket:
```bash
maton api -X POST '/sunsama/create_braindump_task' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "title": "Research new tools",
  "timeBucket": "in the next month"
}
JSON
```

**Time bucket options:**
- `"in the next two weeks"`
- `"in the next month"`
- `"in the next quarter"`
- `"in the next year"`
- `"someday"`
- `"never"`

#### Start/Stop Task Timer Tools

```bash
maton api -X POST '/sunsama/start_task_timer' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "taskId": "69a6bf3a04d3cd0001595308"
}
JSON
```

```bash
maton api -X POST '/sunsama/stop_task_timer' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "taskId": "69a6bf3a04d3cd0001595308"
}
JSON
```

#### Set Shutdown Time Tool

Set when your workday ends:
```bash
maton api -X POST '/sunsama/set_shutdown_time' -H 'Content-Type: application/json' --input - <<'JSON'
{
  "calendarDay": "2026-03-03",
  "hour": 18,
  "minute": 0
}
JSON
```

### Response Format

All MCP tool responses wrap content in a `content` array of typed blocks alongside an `isError` flag. For Sunsama the `text` field holds JSON-stringified data that has to be parsed:

```json
{
  "content": [
    {
      "type": "text",
      "text": "{\"success\":true,\"task\":{\"_id\":\"...\"}}"
    }
  ],
  "isError": false
}
```

Tool-level failures return HTTP 200 with `isError` set to `true` and the message in the same `content` array, so check `isError` rather than the HTTP status:

```json
{
  "content": [
    {
      "type": "text",
      "text": "<error message>"
    }
  ],
  "isError": true
}
```

### Notes

- All task IDs are MongoDB ObjectIds (24-character hex strings)
- Date format: `YYYY-MM-DD` for days, ISO 8601 for datetimes
- Time estimates are returned as human-readable strings (e.g., "20 minutes")
- If multiple Sunsama connections exist, specify which to use with `Maton-Connection` header

### Resources

- [Sunsama](https://sunsama.com)
- [Maton Community](https://discord.com/invite/dBfFAcefs2)
- [Maton Support](mailto:support@maton.ai)
- [Maton CLI Manual](https://cli.maton.ai/manual)
