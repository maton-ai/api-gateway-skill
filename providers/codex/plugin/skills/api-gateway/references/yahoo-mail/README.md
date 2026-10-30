# Yahoo Mail

## API Reference

> **Safety:** All write operations (send, reply, forward, draft send, modify, trash, untrash, folder create/rename/delete, draft delete) require explicit user confirmation before execution. Verify the target message or folder and the intended effect with the user first. See [Security & Permissions](../../SKILL.md#security--permissions) for full security policy.

**App name:** `yahoo-mail`
**Upstream base URLs:** `imap.mail.yahoo.com` (IMAP), `smtp.mail.yahoo.com` (SMTP)

Raw requests go to a single /commands endpoint, which takes the IMAP or SMTP commands as base64 in the request body. The app password is stored in the Maton connection, and the gateway logs in before the stream runs, so requests never carry it. For example:

- Upstream: imap.mail.yahoo.com ← a1 SELECT INBOX
- Gateway: https://api.maton.ai/yahoo-mail/commands ← {"commands": "<base64 of a1 SELECT INBOX>"}

**Note:** Prefer the `maton yahoo-mail` commands over calling /commands directly. Raw IMAP and SMTP are error-prone: you handle quoting, encoding and threading yourself without sanity checks. Use /commands only for gaps in the CLI following the [appendix](#appendix-raw-imap-and-smtp).

### Message ID

- **A message ID is Yahoo's `EMAILID`**, from the ID column of `message list`. It stays the same when the message is moved, trashed or its folder is renamed. A copy gets a new ID.
- **Commands look in one folder only:** `--folder`, which defaults to INBOX (Trash for `message untrash`, Draft for the `draft` commands). If the message is in another folder, the command fails and asks for `--folder`. Searching every folder takes Yahoo 10+ seconds so the CLI doesn't do it.
- **Keep the folder with the ID.** `message list` shows a FOLDER column, and `trash`, `untrash` and `modify --move-to` print the message's new folder.
- **A thread ID is Yahoo's numeric `THREADID`**, from the THREAD column of `message list` or `thread_id` in `message get --json`. `thread get` refuses message IDs.
- **The output has no `Message-ID` header.** The sending software sets it, it isn't unique, and no command accepts it.

### Mailbox API

#### Get Mailbox

```bash
maton yahoo-mail whoami
```

### Folders API

#### List Folders

```bash
maton yahoo-mail folder list
```

**Note:** `--folder` takes `NAME` (eg. `INBOX`, `Sent`, `Draft`, `Trash`, `Bulk`, `Archive`, `Work/Clients`).

#### Create Folder

```bash
maton yahoo-mail folder create Receipts
maton yahoo-mail folder create Work/Clients
```

#### Rename Folder

```bash
maton yahoo-mail folder rename Receipts "Receipts 2026"
```

**Note:** INBOX and the special folders can't be renamed.

#### Delete Folder

```bash
maton yahoo-mail folder delete "Receipts 2026"
```

**Note:** Permanently deletes the folder and all its messages. Messages skip `Trash` and can't be recovered. `INBOX` and special folders can't be deleted. Confirm with the user before proceeding.

### Messages API

#### List Messages

```bash
maton yahoo-mail message list
```

**Note:** Returns up to 20 unread `INBOX` messages, newest first. Listing never marks anything read.

**All messages:**

```bash
maton yahoo-mail message list --all -L 50
```

**Another folder:**

```bash
maton yahoo-mail message list --folder Sent --all
```

**With filters (all must match):**

```bash
maton yahoo-mail message list --all --from boss@example.com --since 2026-10-01
maton yahoo-mail message list --all --subject invoice --before 2026-10-15
maton yahoo-mail message list --all --text "quarterly report"
```

**Note:** `--from`, `--to`, `--subject` and `--text` match text anywhere in the field (`--text` covers headers and body); `--since` and `--before` take `YYYY-MM-DD`. There is no next-page cursor: `-L/--max` caps the results so narrow with filters instead of paging. Yahoo does not index Bulk (spam) for text searches so `--subject` and `--text` find nothing there.

#### Get Message

```bash
maton yahoo-mail message get {messageId}
maton yahoo-mail message get {messageId} --headers
maton yahoo-mail message get {messageId} --folder Sent --raw
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Prints the plaintext body without marking the message as read. `--headers` adds From/To/Cc/Subject/Date, `--html` prints the HTML part, and `--raw` prints the original source. `--json` adds the folder, flags, and `thread_id`.

#### Send Message

```bash
maton yahoo-mail message send --to alice@example.com --subject "Hello" --body "Hi there!"
maton yahoo-mail message send --to alice@example.com --cc bob@example.com --subject "Report" --body "Attached." -a report.pdf
```

**Note:** Yahoo files its own copy in `Sent`, usually within seconds but sometimes up to 30 seconds later (forwards especially). The output contains no message ID (`--json` prints `{}`), so to act on the sent message, find it with `message list --folder Sent --all --subject "…"`, and retry shortly if it isn't there yet. `--html` sends `--body` as HTML. `reply`, `reply-all`, `forward` and `draft send` work the same way.

#### Reply to Message

```bash
maton yahoo-mail message reply {messageId} --body "Thanks!"
maton yahoo-mail message reply {messageId} --body "Draft reply" --draft
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** The reply goes to the original sender (or their `Reply-To` address, if set) and includes the threading headers and quoted original so mail apps keep the conversation together. `--draft` saves the reply to `Draft` instead of sending it and prints the draft ID.

#### Reply All to Message

```bash
maton yahoo-mail message reply-all {messageId} --body "Sounds good"
maton yahoo-mail message reply-all {messageId} --body "Updated" --remove bob@example.com
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** The reply goes to everyone in `To` and `Cc`, except the user's own address and any address passed with `--remove`. When replying to a message the user sent, it goes to that message's original recipients.

#### Forward Message

```bash
maton yahoo-mail message forward {messageId} --to dave@example.com --body "FYI"
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** The original's header block and body are included. Its attachments are not re-attached; attach files with `-a`.

#### Modify Message

```bash
maton yahoo-mail message modify {messageId} --read
maton yahoo-mail message modify {messageId} --unread --flag
maton yahoo-mail message modify {messageId} --move-to Archive
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** IMAP uses flags and folders rather than labels. The message keeps its ID; the output names the folder it is now in, which later commands need as `--folder`.

#### Trash / Untrash Message

```bash
maton yahoo-mail message trash {messageId}
maton yahoo-mail message untrash {messageId}
maton yahoo-mail message untrash {messageId} --to Archive
```

**Note:** `{messageId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** `trash` moves the message to `Trash` where it stays until the provider empties it. `untrash` moves it back to `INBOX` or to `--to` if given.

### Drafts API

#### Create Draft

```bash
maton yahoo-mail draft create --to alice@example.com --subject "Hello" --body "Draft body"
```

#### List Drafts

```bash
maton yahoo-mail draft list
```

#### Get Draft

```bash
maton yahoo-mail draft get {draftId}
```

**Note:** `{draftId}` is a placeholder. Replace it with a real value before sending the request.

#### Send Draft

```bash
maton yahoo-mail draft send {draftId}
```

**Note:** `{draftId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Sends the draft as saved, then deletes it from `Draft`. BCC recipients receive it but aren't shown in the delivered message's headers. Yahoo files its own copy in `Sent`, usually within seconds but sometimes up to 30 seconds later (forwards especially). The output has no ID (`--json` prints `{}`): to act on the sent message, find it with `message list --folder Sent --all --subject "…"`, and retry shortly if it isn't there yet.

#### Delete Draft

```bash
maton yahoo-mail draft delete {draftId}
```

**Note:** `{draftId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Permanently deletes the draft; it doesn't go to `Trash`.

### Threads API

#### Get Thread

```bash
maton yahoo-mail thread get {threadId}
maton yahoo-mail thread get {threadId} --json
```

**Note:** `{threadId}` is a placeholder. Replace it with a real value before sending the request.

**Note:** Lists every message in the conversation across all folders including `Sent` and `Trash`, oldest first, as summaries; use `message get` for bodies.

### Pagination

There is no next-page cursor, so `--paginate` does nothing here. `-L/--max` caps the results (default 20), newest first; to reach older mail, narrow the window with `--since` and `--before` instead of paging.

### Notes

- **Removal actions require review.** `folder delete` and `draft delete` are permanent; list or get the target first so the user can verify it.
- **Reading never marks mail read.** `message list`, `message get` and `thread get` leave flags alone; mark a message with `message modify --read`.
- **`--since` and `--before` work by day,** on the date the server received the message; the time of day is ignored.
- **"Not found" means another folder, or gone.** An EMAILID never changes, so `message … not found in INBOX` means the message is in a different folder or was deleted.
- **Replies can land in Bulk.** Yahoo's spam filter sometimes files replies there, and Bulk isn't indexed for `--subject` or `--text`; list it with `message list --folder Bulk --all`, or find the reply with `thread get`.
- **Folder names:** non-ASCII names (`Café`) are converted to and from IMAP's encoding, and names with control characters are refused. Creating `Work/Clients` also lists a parent `Work` that can't hold messages; Yahoo removes it with its last child.
- **Yahoo decides what is in a thread.** It groups by its own `THREADID`, which also pulls in a forward with the same subject, and ignores a `Message-ID` copied from another message.
- **A copy is a separate message.** Mail sent to yourself is stored twice, in INBOX and in Sent, each with its own EMAILID; `thread get` lists both.
- **Forwards stay in the thread:** `forward` sets `References` (but not `In-Reply-To`) to the original.
- **Only Yahoo addresses can be connected.** A Yahoo account that signs in with another provider's address, such as Gmail, has no Yahoo address to send from: Yahoo refuses its sends, so the connect form refuses it. Yahoo Japan (`@yahoo.co.jp`) is a separate service and isn't supported.

### Error Handling

| Status | Meaning |
|--------|---------|
| 400 | Missing Yahoo Mail connection; the connection isn't ready (the message names its `status_reason`); the app password was refused; or a request the mail service rejected (the message says why) |
| 401 | Invalid, missing, or expired Maton credential |
| 429 | A limit was hit: the account's request rate, its mail requests in progress, the mailbox's requests in flight or logins, or the daily send allowance. Wait for the `Retry-After` interval (see [Rate Limits](#rate-limits)) |
| 502 | The mail server connection dropped mid-request. For a read, retry. For a write or send, the outcome is unknown: check the mailbox (or Sent) before repeating it |
| 503 | Temporary: the provider is throttling, the mail session dropped before anything changed, or the mail service is at capacity. Nothing was changed; retry after the `Retry-After` interval |
| 504 | The request ran past 60 seconds. A read is safe to retry; for a write or send, check first |
| 500 | Internal error, or a connection lost mid-stream (`Commands already sent may have taken effect`, `A message already acknowledged with 250 may still be delivered`). Check before repeating a write or send |

**Before retrying, tell reads from writes.** Lists, gets and thread lookups are safe to repeat. A send, reply, forward, move, flag change, or delete that failed with 500, 502 or 504 may already have happened: check the folder (and Sent) first, and never resend just because the command errored.

Common command errors:

| Error | What to do |
|-------|-----------|
| `message … not found in INBOX; if it's in another folder, pass --folder` | The ID is fine; the message is in another folder or was deleted. Find it with `message list --folder <name> --all`, then pass that folder as `--folder`. |
| `message … is no longer in <folder>` | It moved or was deleted between the lookup and the change. List again before retrying. |
| `Login with this app password failed recently …` | The app password was refused, and Maton waits before trying it again (5 minutes, doubling up to 6 hours), because repeated failures can lock it. Have the user enter a corrected app-specific password, through the connection's `url` while it is still valid or in a new connection; a corrected password is checked right away. |
| `Several logins to this mailbox with app passwords that have not worked before failed recently …` | Too many new passwords failed for this mailbox. Don't create more connections: new passwords aren't tried until the time in the message. Ask the user to check the address and password, then retry after that time. |

### Troubleshooting: Connection Status Reason

```bash
maton connection get {connection_id} --json connection_id,status,status_reason
```

| Reason | Next step |
|--------|-----------|
| (none, `PENDING`) | Connect page not finished. Open the connection's `url`. |
| `login_verifying` | Password is being checked (next check at `metadata.verification.retry_at`, if set). Wait. |
| `login_refused`, `login_revoked` | Yahoo refused the app password. Re-enter it at the connection's `url` (valid ~30 minutes), or create a new connection. |
| `login_unverified` | Yahoo was unreachable for about an hour. Create a new connection; the same password may work. |

### Rate Limits

- **Requests:** 10 per second per Maton account, with bursts up to 50.
- **Mail requests in progress:** at most 4 at a time per Maton account, across all of its mailboxes; more get 429 for a second.
- **One session per mailbox:** Maton keeps a single IMAP session per mailbox, shared by every connection to that address. Yahoo answers on it one command at a time: about 0.6 s each, 2–3 s for a command that takes a message ID, longer for `thread get`. So at most 2 requests are in flight per mailbox, one on the session and one waiting up to 10 s for it; a third is refused at once with 429. Send requests one after another, under one per second.
- **Logins per mailbox:** 4 IMAP logins per 8 s and 10 SMTP logins per 20 s; each send logs in to SMTP. Maton reuses IMAP sessions, so this is only reached by reconnect-heavy raw streams or rapid sends.
- **Failed logins:** a refused app password is retried only after a pause (5 minutes, doubling up to 6 hours), and two refusals in a row mark the connection `FAILED` (`login_revoked`).
- **Sending:** 500 messages and 500 recipients per mailbox per day, across all its connections, reset at 00:00 UTC. A send over the allowance gets 429 before anything goes out. Leave a minute between sends: each is a separate SMTP login, and about ten within a few minutes have made Yahoo refuse the app password.
- **Throttling:** bursts of failing requests or reconnects can make Yahoo refuse the mailbox with `[SERVERBUG] … try again later` or `[UNAVAILABLE] LOGIN Server error`. Maton returns 503 with `Retry-After: 60` and holds off logging in for 60 s; Yahoo itself has kept a mailbox locked for 45 minutes to 2 hours. Back off rather than retrying in a loop.

### Appendix: Raw IMAP and SMTP

```bash
stream=$(printf 'M1 EXAMINE INBOX\r\nM2 UID SEARCH UNSEEN\r\n' | base64)
maton api -X POST '/yahoo-mail/commands' -f commands="$stream" -q '.data' | base64 --decode
```

The request body is exactly `{"commands": "<base64 of the stream>"}` (any other field is refused), and the response is `{"data": "<base64 of the server's reply>"}`. A stream whose first word is an SMTP verb (`EHLO`, `MAIL`, …) goes to SMTP; anything else goes to IMAP.

**Caveats:**

- **You own the protocol.** Every IMAP command needs a unique tag; strings need quoting or literals (use `{n+}`); non-ASCII folder names must be in modified UTF-7 (`Café` is `Caf&AOk-`); every line ends with CRLF, and a bare CR or LF is refused. Mistakes come back as `BAD` replies, each costing about 4 seconds (including `UNSELECT` with no folder selected), or, worse, as a valid command that does something else.
- **No folder is selected when a stream starts.** Run `SELECT` or `EXAMINE` first, in the same stream.
- **Writes to a missing UID succeed silently.** `STORE`, `MOVE`, `COPY` and `EXPUNGE` on a UID that no longer exists report success. Check with `UID FETCH <uid> (UID)` in the same stream, and use `UID EXPUNGE <uid>`, never plain `EXPUNGE`, which removes every message marked deleted in the folder.
- **Fetching with `BODY[]` marks a message read.** Use `BODY.PEEK[]`.
- **`UID SEARCH EMAILID x` on its own ignores the selected folder** and can return the UID from another folder. Add any other criterion, e.g. `UID SEARCH ALL EMAILID x`, to keep it in the selected folder.
- **No search-and-fetch in one step.** `SEARCHRES` (`RETURN (SAVE)`), `MULTISEARCH` and fetching by EMAILID are refused: search first, then fetch the UIDs it returned.
- **Finding a message by `Message-ID`** with `HEADER Message-ID "<id>"` needs the full ID, angle brackets included.
- **Threads:** `SEARCH THREADID n` works per folder, and the ID must be a number (anything else gets `NO [SERVERBUG]`). `HEADER References` searches get `BAD [CANNOT]` and make an `OR` return nothing.
- **Some commands make Maton discard the session,** so the next request logs in again: `LOGIN`, `AUTHENTICATE`, `LOGOUT`, `ENABLE`, `IDLE`, `NOTIFY`, `COMPRESS`, CONDSTORE/QRESYNC options, synchronizing literals `{n}`, and `SEARCH … RETURN` options other than `MIN`, `MAX`, `COUNT` and `ALL`.
- **A batch stops at the first failure,** and a lost connection can leave earlier commands applied: check the mailbox before repeating a write (see the 500/502/504 rows in [Error Handling](#error-handling)).
- **Sizes:** a stream can be up to 20 MB and a reply up to 25 MB, and a request has 60 seconds. For a reply over the limit, check sizes with `RFC822.SIZE` or `BODYSTRUCTURE`, then fetch one part (`BODY.PEEK[2]`) or a byte range (`BODY.PEEK[]<0.1000000>`).
- **SMTP is all yours:** the `MAIL FROM`/`RCPT TO`/`DATA` sequence (`BDAT` is refused), dot-stuffing, the `Message-ID` and `Date` headers, stripping `Bcc` from the message, and threading headers on replies. One stream can carry several messages over one login. Read every reply code: a stream cut off at the time limit returns what it got, and a message whose final `.` was acknowledged may be delivered even if later commands failed, so check Sent before sending again. The daily send allowance in [Rate Limits](#rate-limits) still applies.

### Resources

- [Yahoo: generate and manage app passwords](https://help.yahoo.com/kb/SLN15241.html)
- [Yahoo: IMAP server settings](https://help.yahoo.com/kb/SLN4075.html)
- [RFC 3501: IMAP4rev1](https://www.rfc-editor.org/rfc/rfc3501)
- [RFC 8474: IMAP extension for object identifiers (EMAILID, THREADID)](https://www.rfc-editor.org/rfc/rfc8474)
- [RFC 5321: SMTP](https://www.rfc-editor.org/rfc/rfc5321)
- [Maton CLI Manual](https://cli.maton.ai/manual)
