# iCloud Mail

## API Reference

> **Safety:** All write operations (send, reply, forward, draft send, modify, trash, untrash, folder create/rename/delete, draft delete) require explicit user confirmation before execution. Verify the target message or folder and the intended effect with the user first. See [Security & Permissions](../../SKILL.md#security--permissions) for full security policy.

**App name:** `icloud-mail`
**Upstream base URLs:** `imap.mail.me.com` (IMAP), `smtp.mail.me.com` (SMTP)

Raw requests go to a single /commands endpoint, which takes the IMAP or SMTP commands as base64 in the request body. The app password is stored in the Maton connection, and the gateway logs in before the stream runs, so requests never carry it. For example:

- Upstream: imap.mail.me.com ← a1 SELECT INBOX
- Gateway: https://api.maton.ai/icloud-mail/commands ← {"commands": "<base64 of a1 SELECT INBOX>"}

**Note:** Prefer the `maton icloud-mail` commands over calling /commands directly. Raw IMAP and SMTP are error-prone: you handle quoting, encoding and threading yourself without sanity checks. Use /commands only for gaps in the CLI following the [appendix](#appendix-raw-imap-and-smtp).

### Message ID

- **A message ID is `<folder>/<uid>`**, e.g. `INBOX/{uid}` or `Work/Clients/{uid}`; it is split at the last `/`, so nested folders work. Quote IDs whose folder has a space: `"Sent Messages/{uid}"`.
- **The ID changes when the message moves.** iCloud has no server-side message ID, and a UID only means something within its folder. `trash`, `untrash` and `modify --move-to` print the new ID; use that one from then on. Renaming a folder changes the IDs of the messages in it.
- **A stale ID is an error,** never a silent no-op: `message … not found`.
- **Thread IDs are a `Message-ID`.** iCloud assigns none, so a thread ID is the `Message-ID` of the conversation's first message, as replies name it in their `References` header. It is in the THREAD column of `message list` and `thread_id` in `message get --json`. `thread get` also accepts the `message_id` of any message in the conversation.
- **`message_id` in JSON is the `Message-ID` header,** written by whoever sent the message. It is not guaranteed unique, and some messages have none.

### Mailbox API

#### Get Mailbox

```bash
maton icloud-mail whoami
```

### Folders API

#### List Folders

```bash
maton icloud-mail folder list
```

**Note:** `--folder` takes `NAME` (eg. `INBOX`, `Sent Messages`, `Drafts`, `Deleted Messages`, `Junk`, `Archive`, `Work/Clients`). iCloud flags only `Sent Messages` and `Deleted Messages` so the CLI recognises the others by name.

#### Create Folder

```bash
maton icloud-mail folder create Receipts
maton icloud-mail folder create Work/Clients
```

#### Rename Folder

```bash
maton icloud-mail folder rename Receipts "Receipts 2026"
```

**Note:** INBOX and the special folders can't be renamed. Renaming changes the IDs of the messages inside.

#### Delete Folder

```bash
maton icloud-mail folder delete "Receipts 2026"
```

**Note:** Permanently deletes the folder and all its messages. Messages skip `Trash` and can't be recovered. `INBOX` and special folders can't be deleted. Confirm with the user before proceeding.

### Messages API

#### List Messages

```bash
maton icloud-mail message list
```

**Note:** Returns up to 20 unread `INBOX` messages, newest first. Listing never marks anything read.

**All messages:**

```bash
maton icloud-mail message list --all -L 50
```

**Another folder:**

```bash
maton icloud-mail message list --folder 'Sent Messages' --all
```

**With filters (all must match):**

```bash
maton icloud-mail message list --all --from boss@example.com --since 2026-10-01
maton icloud-mail message list --all --subject invoice --before 2026-10-15
maton icloud-mail message list --all --text "quarterly report"
```

**Note:** `--from`, `--to`, `--subject` and `--text` match text anywhere in the field (`--text` covers headers and body); `--since` and `--before` take `YYYY-MM-DD`. There is no next-page cursor: `-L/--max` caps the results, so narrow with filters instead of paging.

#### Get Message

```bash
maton icloud-mail message get {messageId}
maton icloud-mail message get {messageId} --headers
maton icloud-mail message get {messageId} --raw
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Prints the plaintext body without marking the message read. `--headers` adds From/To/Cc/Subject/Date, `--html` prints the HTML part, `--raw` the original source. `--json` adds the folder, flags, `message_id` and `thread_id`.

#### Send Message

```bash
maton icloud-mail message send --to alice@example.com --subject "Hello" --body "Hi there!"
maton icloud-mail message send --to alice@example.com --cc bob@example.com --subject "Report" --body "Attached." -a report.pdf
```

**Note:** iCloud Mail keeps no copy of what it sends so the CLI saves one in `Sent Messages` and prints its ID (`sent_id` in JSON, alongside `message_id`). `--html` sends `--body` as HTML. `reply`, `reply-all`, `forward` and `draft send` work the same way.

#### Reply to Message

```bash
maton icloud-mail message reply {messageId} --body "Thanks!"
maton icloud-mail message reply {messageId} --body "Draft reply" --draft
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** The reply goes to the sender (or their `Reply-To` address, if set) and includes the threading headers and the quoted original so mail apps keep the conversation together. `--draft` saves the reply in `Drafts` instead of sending it and prints its draft ID.

#### Reply All to Message

```bash
maton icloud-mail message reply-all {messageId} --body "Sounds good"
maton icloud-mail message reply-all {messageId} --body "Updated" --remove bob@example.com
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** The reply goes to everyone in `To` and `Cc`, except the user's own address and any address passed with `--remove`. When replying to a message the user sent, it goes to that message's original recipients.

#### Forward Message

```bash
maton icloud-mail message forward {messageId} --to dave@example.com --body "FYI"
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** The original's header block and body are included. Its attachments are not re-attached; attach files with `-a`.

#### Modify Message

```bash
maton icloud-mail message modify {messageId} --read
maton icloud-mail message modify {messageId} --unread --flag
maton icloud-mail message modify {messageId} --move-to Archive
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** IMAP uses flags and folders rather than labels. The message gets a new ID in its new folder; the output prints it.

#### Trash / Untrash Message

```bash
maton icloud-mail message trash {messageId}
maton icloud-mail message untrash {messageId}
maton icloud-mail message untrash {messageId} --to Archive
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** `trash` moves the message to `Deleted Messages` where it stays until the provider empties it. `untrash` moves it back to `INBOX` or to `--to` if given.

### Drafts API

#### Create Draft

```bash
maton icloud-mail draft create --to alice@example.com --subject "Hello" --body "Draft body"
```

#### List Drafts

```bash
maton icloud-mail draft list
```

#### Get Draft

```bash
maton icloud-mail draft get {draftId} --headers
```

**Note:** `{draftId}` is a placeholder. Replace it with a real value before sending the request.

#### Send Draft

```bash
maton icloud-mail draft send {draftId}
```

**Note:** `{draftId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Sends the draft as saved, then deletes it from `Drafts`. BCC recipients receive it but aren't shown in the delivered message's headers. iCloud Mail keeps no copy of what it sends so the CLI saves one in `Sent Messages` and prints its ID (`sent_id` in JSON, alongside `message_id`).

#### Delete Draft

```bash
maton icloud-mail draft delete {draftId}
```

**Note:** Permanently deletes the draft; it doesn't go to `Trash`.

### Threads API

#### Get Thread

```bash
maton icloud-mail thread get {threadId}
maton icloud-mail thread get {threadId} --json
```

**Note:** `{threadId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Lists every message in the conversation across all folders including `Sent` and `Trash`, oldest first, as summaries; use `message get` for bodies.

### Pagination

There is no next-page cursor, so `--paginate` does nothing here. `-L/--max` caps the results (default 20), newest first; to reach older mail, narrow the window with `--since` and `--before` instead of paging.

### Notes

- **Removal actions require review.** `folder delete` and `draft delete` are permanent; list or get the target first so the user can verify it.
- **Reading never marks mail read.** `message list`, `message get` and `thread get` leave flags alone; mark a message with `message modify --read`.
- **`--since` and `--before` work by day,** on the date the server received the message; the time of day is ignored.
- **`message_id` doesn't identify a message.** The `Message-ID` header is written by the sending software: it can be missing, repeated, or copied from another message, and both copies of a message sent to yourself carry the same one. Act on messages by `id`; use `message_id` only with `thread get`.
- **iCloud needs the Maton bridge** for reading and sending: Apple refuses mail from cloud servers, so Maton connects through the bridge on the user's own computer. See [Bridges](../../SKILL.md#bridges).
- **Moves change the ID.** UIDs are per folder, so `trash`, `untrash` and `modify --move-to` give the message a new ID; use the one printed. (iCloud doesn't advertise IMAP `MOVE`, so the CLI copies the message and expunges the original.)
- **Sent copies are written by the CLI.** iCloud keeps no copy of what it sends, so the CLI saves one in `Sent Messages`. Bcc recipients appear only in that copy. If saving it fails, the message was still sent and the CLI says so: don't send it again.
- **Threads come from reply headers.** A thread ID is the `Message-ID` of the conversation's first message, found through `References`, else `In-Reply-To`, else the message's own; it matches how iCloud Mail on the web groups conversations, never by subject. A message without a valid `Message-ID` gets no `thread_id`, and replies to it start a new thread.
- **A thread ID may belong to no stored message.** When a conversation started from Yahoo, Yahoo's server added a `References` entry like `<id.ref@yahoo.com>`, which becomes the thread ID. `thread get` on it, or on any message's `message_id`, still returns the whole thread.
- **`thread get` can miss a deep branch:** a reply that carries only `In-Reply-To`, two or more replies down, isn't found from the root. It is found from its own branch's `message_id`.
- **Self-sent mail appears twice** in `thread get` and searches: the delivered copy in INBOX and the CLI's copy in `Sent Messages`, with the same `Message-ID`.
- **Forwards stay in the thread:** `forward` sets `References` (but not `In-Reply-To`) to the original.
- **`draft send` fills in missing headers.** A draft without a `Message-ID` or `Date` gets them before sending, so the delivered copy and the Sent copy match.
- **Large messages are slow through the bridge,** at about the user's upload speed (around 1 MB/s on a typical connection). Fetch big messages one at a time.
- **Folder names:** non-ASCII names (`Café`) are converted to and from IMAP's encoding, and names with control characters are refused. `Work/Clients` can be created without creating `Work` first.

### Error Handling

| Status | Meaning |
|--------|---------|
| 400 | Missing iCloud Mail connection; the connection isn't ready (the message names its `status_reason`); the app password was refused; or a request the mail service rejected (the message says why) |
| 401 | Invalid, missing, or expired Maton credential |
| 429 | A limit was hit: the account's request rate, its mail requests in progress, the mailbox's requests in flight or logins, or the daily send allowance. Wait for the `Retry-After` interval (see [Rate Limits](#rate-limits)) |
| 502 | The mail server or bridge connection dropped mid-request. For a read, retry. For a write or send, the outcome is unknown: check the mailbox (or Sent) before repeating it |
| 503 | Temporary: the provider is throttling, the mail session dropped before anything changed, the bridge is busy, or the mail service is at capacity. Nothing was changed; retry after the `Retry-After` interval |
| 504 | The request ran past 60 seconds. A read is safe to retry; for a write or send, check first |
| 500 | Internal error, or a connection lost mid-stream (`Commands already sent may have taken effect`, `A message already acknowledged with 250 may still be delivered`). Check before repeating a write or send |

**Before retrying, tell reads from writes.** Lists, gets and thread lookups are safe to repeat. A send, reply, forward, move, flag change, or delete that failed with 500, 502 or 504 may already have happened: check the folder (and Sent) first, and never resend just because the command errored.

Common command errors:

| Error | What to do |
|-------|-----------|
| `message … not found` | The ID is stale: the message moved (its ID changed) or was deleted. List the folder again to get its current ID. |
| `Login with this app password failed recently …` | The app password was refused, and Maton waits before trying it again (5 minutes, doubling up to 6 hours), because repeated failures can lock it. Have the user enter a corrected app-specific password, through the connection's `url` while it is still valid or in a new connection; a corrected password is checked right away. |
| `Several logins to this mailbox with app passwords that have not worked before failed recently …` | Too many new passwords failed for this mailbox. Don't create more connections: new passwords aren't tried until the time in the message. Ask the user to check the address and password, then retry after that time. |
| `this mailbox sends through the Maton bridge, which isn't installed` | No bridge is registered. Ask the user to run `maton bridge install` on their own computer. Don't create a new connection. |
| `this mailbox sends through the Maton bridge, which isn't running` | A bridge is registered but none is connected. See [Bridges](../../SKILL.md#bridges). Don't create a new connection. |
| `The Maton bridge … is busy` / `disconnected before anything was sent` | Temporary (503). Retry after the `Retry-After` interval. |
| `Your computer couldn't reach … through the Maton bridge` | The bridge's network blocks outbound mail ports, or the computer just went to sleep. Retry once; if it persists, the user needs another network. |

### Troubleshooting: Connection Status Reason

```bash
maton connection get {connection_id} --json connection_id,status,status_reason
```

| Reason | Next step |
|--------|-----------|
| (none, `PENDING`) | Connect page not finished. Open the connection's `url`. |
| `login_verifying` | Password is being checked (next check at `metadata.verification.retry_at`, if set). Wait. |
| `route_verifying` | Sending route is being set up. Wait a minute and check again. |
| `bridge_missing` | No Maton bridge installed. Ask the user to run `maton bridge install` on their own computer. |
| `bridge_disconnected` | Bridge computer is asleep, offline, or logged out. Turns `ACTIVE` again when the bridge reconnects. |
| `login_refused`, `login_revoked` | Apple refused the app-specific password. Re-enter it at the connection's `url` (valid ~30 minutes), or create a new connection. |
| `login_unverified` | Apple was unreachable for about an hour. Create a new connection; the same password may work. |

### Rate Limits

- **Requests:** 10 per second per Maton account, with bursts up to 50.
- **Mail requests in progress:** at most 4 at a time per Maton account, across all of its mailboxes; more get 429 for a second.
- **Per mailbox:** 4 requests in flight. More workers only add 429s: iCloud reaches the account's request rate at about 4 at a time.
- **Logins per mailbox:** 4 IMAP logins per 8 s and 10 SMTP logins per 20 s; each send logs in to SMTP. Maton reuses IMAP sessions, so this is only reached by reconnect-heavy raw streams or rapid sends.
- **Sending:** 1000 messages and 1000 recipients per mailbox per day, across all its connections, reset at 00:00 UTC (Apple's published limit for iCloud Mail). A send over the allowance gets 429 before anything goes out.
- **Failed logins:** a refused app password is retried only after a pause (5 minutes, doubling up to 6 hours).
- **Each bridge** carries up to 8 IMAP and 4 SMTP connections at once, shared by all of the account's iCloud mailboxes; when full it answers 503 for a few seconds.

### Appendix: Raw IMAP and SMTP

```bash
stream=$(printf 'M1 EXAMINE INBOX\r\nM2 UID SEARCH UNSEEN\r\n' | base64)
maton api -X POST '/icloud-mail/commands' -f commands="$stream" -q '.data' | base64 --decode
```

The request body is exactly `{"commands": "<base64 of the stream>"}` (any other field is refused), and the response is `{"data": "<base64 of the server's reply>"}`. A stream whose first word is an SMTP verb (`EHLO`, `MAIL`, …) goes to SMTP; anything else goes to IMAP.

**Caveats — read all of them before sending a raw stream:**

- **You own the protocol.** Every IMAP command needs a unique tag; strings need quoting or literals (use `{n+}`); folder names with non-ASCII characters must be in modified UTF-7 (`Café` is `Caf&AOk-`); every line ends with CRLF, and a bare CR or LF is refused. Mistakes come back as `BAD` replies or, worse, as a valid command that does something else.
- **No folder is selected when a stream starts.** `SELECT` or `EXAMINE` first, in the same stream.
- **Writes to a missing UID succeed silently.** IMAP treats `STORE`, `MOVE`, `COPY` and `EXPUNGE` on a UID that no longer exists as success. Check with `UID FETCH <uid> (UID)` in the same stream, and use `UID EXPUNGE <uid>`, never plain `EXPUNGE`, which removes every message marked deleted in the folder.
- **Fetching with `BODY[]` marks a message read.** Use `BODY.PEEK[]`.
- **Finding a message by `Message-ID`:** `HEADER Message-ID "<id>"` needs the full ID with its angle brackets; partial values never match, and a message without the header can't be found this way.
- **Keep the session reusable.** `LOGIN`, `AUTHENTICATE`, `LOGOUT`, `ENABLE`, `IDLE`, `NOTIFY`, `COMPRESS`, CONDSTORE/QRESYNC options, synchronizing literals `{n}`, and `SEARCH … RETURN` options other than `MIN`, `MAX`, `COUNT` and `ALL` make Maton discard the session afterwards, so the next request logs in again, which is slower and counts against the login limits.
- **A batch stops at the first failure,** and a lost connection can leave earlier commands applied: check the mailbox before repeating a write (see the 500/502/504 rows in [Error Handling](#error-handling)).
- **Sizes:** a stream can be up to 20 MB and a reply up to 25 MB, and a request has 60 seconds. A reply over the limit is refused: fetch fewer messages, check sizes with `RFC822.SIZE` or `BODYSTRUCTURE`, then fetch one part (`BODY.PEEK[2]`) or a byte range (`BODY.PEEK[]<0.1000000>`).
- **SMTP is all yours:** the `MAIL FROM`/`RCPT TO`/`DATA` sequence, dot-stuffing, the `Message-ID` and `Date` headers, stripping `Bcc` from the message, and threading headers on replies. One stream can carry several messages over one login. Read every reply code: a stream cut off at the time limit returns what it got, and a message whose final `.` was acknowledged may be delivered even if later commands failed, so check Sent before sending again. The daily send allowance in [Rate Limits](#rate-limits) still applies.
- **`MOVE` works though it isn't advertised.** `UID COPY`, `UID STORE … +FLAGS.SILENT (\Deleted)`, `UID EXPUNGE <uid>` is the portable form.
- **iCloud's SMTP server files no Sent copy.** `APPEND` one to `Sent Messages` yourself, with the same `Message-ID`.
- **`EXAMINE` is read-write on iCloud:** a plain `FETCH … BODY[]` marks the message read even in an examined folder.
- **Search quirks:** `HEADER` search only works for From, To, Cc, Bcc, Reply-To, Sender, Subject, Message-ID, In-Reply-To and References; `LARGER`/`SMALLER` don't filter (fetch `RFC822.SIZE` instead); an empty result has no `* SEARCH` line at all; `SORT` ignores its keys and returns UID order.
- **Only Trash (`Deleted Messages`) and Sent are flagged** as special-use folders; find `Drafts`, `Junk` and `Archive` by name.
- **Choosing a bridge:** with more than one, `-H 'Maton-Bridge: <bridge_id>'` (an ID from `maton bridge list`) pins the request to that computer.

### Resources

- [Apple: app-specific passwords](https://support.apple.com/en-us/102654)
- [Apple: iCloud Mail server settings](https://support.apple.com/en-us/102525)
- [Apple: iCloud Mail sending limits](https://support.apple.com/en-us/102198)
- [RFC 3501: IMAP4rev1](https://www.rfc-editor.org/rfc/rfc3501)
- [RFC 8474: IMAP extension for object identifiers (EMAILID, THREADID)](https://www.rfc-editor.org/rfc/rfc8474)
- [RFC 5321: SMTP](https://www.rfc-editor.org/rfc/rfc5321)
- [Maton CLI Manual](https://cli.maton.ai/manual)
