# mailclient

IMAP and POP3 client library for Nim. Supports both synchronous and asynchronous APIs via `{.multisync.}`. No external dependencies — stdlib only.

## Installation

```
nimble install mailclient
```

Or add to your `.nimble` file:

```nim
requires "mailclient"
```

## Compilation with SSL

To use SSL/TLS connections (POP3S on port 995, IMAPS on port 993), compile with the `-d:ssl` flag:

```
nim c -d:ssl your_app.nim
```

Without this flag, SSL-related parameters are ignored and only plain-text connections are available.

## POP3 Client

POP3 (Post Office Protocol v3, [RFC 1939](https://www.rfc-editor.org/rfc/rfc1939.html)) is a simple protocol for downloading email from a server. The client supports all standard POP3 commands.

### Quick Start (Synchronous)

```nim
import mailclient

# Create client (plain text, port 110)
let client = newPop3Client("mail.example.com")
client.connect()

# Authenticate
client.login("user@example.com", "password")

# Check mailbox
let stats = client.stat()
echo "Messages: ", stats.count, ", Total size: ", stats.size, " bytes"

# List all messages
let messages = client.list()
for msg in messages:
  echo "Message #", msg.id, ": ", msg.size, " bytes"

# Retrieve a message
let content = client.retr(1)
echo content

# Delete a message
client.dele(1)

# Disconnect (sends QUIT, applies deletions)
client.quit()
```

### Quick Start (Asynchronous)

```nim
import mailclient
import std/asyncdispatch

proc main() {.async.} =
  let client = newAsyncPop3Client("mail.example.com")
  await client.connect()
  await client.login("user@example.com", "password")

  let stats = await client.stat()
  echo "Messages: ", stats.count

  let messages = await client.list()
  for msg in messages:
    echo "Message #", msg.id, ": ", msg.size, " bytes"

  let content = await client.retr(1)
  echo content

  await client.quit()

waitFor main()
```

### SSL/TLS Connection

```nim
# POP3S (implicit TLS, port 995)
let client = newPop3Client("mail.example.com", port = Port(995), useSsl = true)
client.connect()
client.login("user@example.com", "password")
# ...
```

### POP3 API Reference

#### Connection

| Proc | Description |
|---|---|
| `newPop3Client(host, port = Port(110), useSsl = false): Pop3Client` | Create synchronous POP3 client |
| `newAsyncPop3Client(host, port = Port(110), useSsl = false): AsyncPop3Client` | Create asynchronous POP3 client |
| `connect(client)` | Connect to server and read greeting |
| `close(client)` | Gracefully disconnect (sends QUIT if connected) |

#### Authentication

| Proc | Description |
|---|---|
| `login(client, username, password)` | Authenticate using USER + PASS |
| `user(client, username)` | Send USER command |
| `pass(client, password)` | Send PASS command |
| `apop(client, username, digest)` | Authenticate using APOP (MD5 challenge-response) |

#### Mailbox Operations

| Proc | Returns | Description |
|---|---|---|
| `stat(client)` | `MessageStat` | Get message count and total size |
| `list(client)` | `seq[MessageInfo]` | List all messages with sizes |
| `list(client, msgNum)` | `MessageInfo` | Get info for a specific message |
| `retr(client, msgNum)` | `string` | Retrieve full message content |
| `top(client, msgNum, lines)` | `string` | Retrieve headers + first N lines of body |
| `dele(client, msgNum)` | - | Mark message for deletion |
| `rset(client)` | - | Unmark all messages marked for deletion |
| `uidl(client)` | `seq[MessageUid]` | Get unique IDs for all messages |
| `uidl(client, msgNum)` | `MessageUid` | Get unique ID for a specific message |
| `noop(client)` | - | Keep-alive (no operation) |
| `quit(client)` | - | Send QUIT and close connection (deletions take effect) |

### POP3 Types

```nim
type
  Pop3Response = object
    ok: bool            # true for +OK, false for -ERR
    message: string     # response text

  MessageStat = object
    count: int          # number of messages in mailbox
    size: int           # total size in octets

  MessageInfo = object
    id: int             # message number (1-based)
    size: int           # message size in octets

  MessageUid = object
    id: int             # message number
    uid: string         # unique identifier (persistent across sessions)
```

---

## IMAP Client

IMAP (Internet Message Access Protocol v4rev1, [RFC 3501](https://www.rfc-editor.org/rfc/rfc3501.html)) provides full mailbox management — folders, flags, search, and server-side message storage.

### Quick Start (Synchronous)

```nim
import mailclient

let client = newImapClient("imap.example.com")
client.connect()
client.login("user@example.com", "password")

# List mailboxes
let mailboxes = client.list()
for mb in mailboxes:
  echo mb.name, " (", mb.flags.join(", "), ")"

# Select a mailbox
let status = client.select("INBOX")
echo "Messages: ", status.exists
echo "Recent: ", status.recent
echo "Unseen: ", status.unseen

# Search for unseen messages
let unseenIds = client.search("UNSEEN")
echo "Unseen message IDs: ", unseenIds

# Fetch message flags and sizes
let resp = client.fetch("1:*", "(FLAGS RFC822.SIZE)")
for line in resp.untagged:
  echo line

# Mark messages as seen
discard client.store("1:3", "+FLAGS", @["\\Seen"])

# Copy messages to another folder
client.copy("1:3", "Archive")

# Delete and expunge
discard client.store("1", "+FLAGS", @["\\Deleted"])
client.expunge()

# Disconnect
client.logout()
```

### Quick Start (Asynchronous)

```nim
import mailclient
import std/asyncdispatch

proc main() {.async.} =
  let client = newAsyncImapClient("imap.example.com")
  await client.connect()
  await client.login("user@example.com", "password")

  let status = await client.select("INBOX")
  echo "Messages: ", status.exists

  let unseenIds = await client.search("UNSEEN")
  echo "Unseen: ", unseenIds

  let resp = await client.fetch("1:10", "(FLAGS UID RFC822.SIZE)")
  for line in resp.untagged:
    echo line

  await client.logout()

waitFor main()
```

### SSL/TLS Connection

```nim
# IMAPS (implicit TLS, port 993)
let client = newImapClient("imap.example.com", port = Port(993), useSsl = true)
client.connect()
client.login("user@example.com", "password")
# ...
```

### IMAP API Reference

#### Connection

| Proc | Description |
|---|---|
| `newImapClient(host, port = Port(143), useSsl = false): ImapClient` | Create synchronous IMAP client |
| `newAsyncImapClient(host, port = Port(143), useSsl = false): AsyncImapClient` | Create asynchronous IMAP client |
| `connect(client)` | Connect to server, read greeting, extract capabilities |
| `close(client)` | Gracefully disconnect (sends LOGOUT if connected) |

#### Authentication

| Proc | Returns | Description |
|---|---|---|
| `login(client, username, password)` | - | Authenticate with LOGIN command |
| `capability(client)` | `seq[string]` | Query server capabilities |
| `logout(client)` | - | Send LOGOUT and close connection |

#### Mailbox Management

| Proc | Returns | Description |
|---|---|---|
| `select(client, mailbox)` | `MailboxStatus` | Open mailbox for read/write |
| `examine(client, mailbox)` | `MailboxStatus` | Open mailbox read-only |
| `list(client, reference, pattern)` | `seq[MailboxInfo]` | List mailboxes matching pattern |
| `lsub(client, reference, pattern)` | `seq[MailboxInfo]` | List subscribed mailboxes |
| `status(client, mailbox, items)` | `MailboxStatus` | Get mailbox status without selecting |
| `create(client, mailbox)` | - | Create a new mailbox |
| `delete(client, mailbox)` | - | Delete a mailbox |
| `rename(client, oldName, newName)` | - | Rename a mailbox |
| `subscribe(client, mailbox)` | - | Subscribe to a mailbox |
| `unsubscribe(client, mailbox)` | - | Unsubscribe from a mailbox |

#### Message Operations

| Proc | Returns | Description |
|---|---|---|
| `fetch(client, sequence, items)` | `ImapResponse` | Fetch message data (FLAGS, BODY, etc.) |
| `search(client, criteria)` | `SearchResult` | Search messages by criteria |
| `store(client, sequence, action, flags)` | `ImapResponse` | Modify message flags |
| `copy(client, sequence, mailbox)` | - | Copy messages to another mailbox |
| `expunge(client)` | - | Permanently remove messages marked \Deleted |
| `append(client, mailbox, message, flags)` | - | Append a message to a mailbox |
| `closeMailbox(client)` | - | Close selected mailbox and expunge \Deleted messages |
| `noop(client)` | - | Keep-alive / poll for server updates |

#### UID Commands

UID variants use persistent unique identifiers instead of sequence numbers:

| Proc | Returns | Description |
|---|---|---|
| `uidFetch(client, sequence, items)` | `ImapResponse` | Fetch by UID |
| `uidSearch(client, criteria)` | `SearchResult` | Search by UID |
| `uidStore(client, sequence, action, flags)` | `ImapResponse` | Modify flags by UID |
| `uidCopy(client, sequence, mailbox)` | - | Copy by UID |

#### FETCH Items

The `items` parameter in `fetch`/`uidFetch` is a standard IMAP fetch data items string:

| Item | Description |
|---|---|
| `FLAGS` | Message flags (\Seen, \Answered, etc.) |
| `UID` | Unique identifier |
| `RFC822.SIZE` | Message size in bytes |
| `RFC822` | Full message (headers + body) |
| `RFC822.HEADER` | Message headers only |
| `BODY[]` | Full message content |
| `BODY[HEADER]` | Headers only |
| `BODY[TEXT]` | Body only |
| `BODY.PEEK[]` | Full message without setting \Seen |
| `BODY.PEEK[HEADER]` | Headers without setting \Seen |
| `ENVELOPE` | Parsed envelope (from, to, subject, date) |
| `INTERNALDATE` | Server-assigned date |

Combine multiple items in parentheses: `"(FLAGS UID RFC822.SIZE)"`

#### SEARCH Criteria

The `criteria` parameter in `search`/`uidSearch` uses standard IMAP search syntax:

```nim
# Simple searches
let unseen = client.search("UNSEEN")
let flagged = client.search("FLAGGED")
let recent = client.search("RECENT")

# Search by sender/subject
let fromBoss = client.search("FROM \"boss@company.com\"")
let important = client.search("SUBJECT \"urgent\"")

# Date-based searches
let thisWeek = client.search("SINCE 20-Mar-2026")
let oldMail = client.search("BEFORE 01-Jan-2026")

# Combined criteria (AND is implicit)
let unreadFromBoss = client.search("UNSEEN FROM \"boss@company.com\"")

# OR searches
let orSearch = client.search("OR FROM \"alice\" FROM \"bob\"")

# Size-based
let largeMails = client.search("LARGER 1000000")
```

#### STORE Actions

The `action` parameter in `store`/`uidStore`:

| Action | Description |
|---|---|
| `+FLAGS` | Add flags to existing flags |
| `-FLAGS` | Remove specified flags |
| `FLAGS` | Replace all flags |
| `+FLAGS.SILENT` | Add flags, don't return updated flags |
| `-FLAGS.SILENT` | Remove flags, don't return updated flags |
| `FLAGS.SILENT` | Replace flags, don't return updated flags |

### IMAP Types

```nim
type
  ImapResponseKind = enum
    irkOk, irkNo, irkBad

  ImapResponse = object
    kind: ImapResponseKind      # OK, NO, or BAD
    tag: string                 # command tag (e.g., "A1")
    text: string                # status text
    untagged: seq[string]       # untagged data lines from server

  MailboxInfo = object
    name: string                # mailbox name (e.g., "INBOX")
    flags: seq[string]          # mailbox flags (e.g., \HasNoChildren)
    delimiter: string           # hierarchy delimiter (e.g., ".")

  MailboxStatus = object
    name: string                # mailbox name
    exists: int                 # total messages
    recent: int                 # recent (new) messages
    unseen: int                 # first unseen message number
    uidNext: int                # next UID to be assigned
    uidValidity: int            # UID validity value
    flags: seq[string]          # defined flags
    permanentFlags: seq[string] # flags that can be permanently set

  FetchResult = object
    id: int                     # sequence number
    uid: int                    # unique identifier
    flags: seq[string]          # message flags
    size: int                   # RFC822.SIZE
    headers: string             # message headers
    body: string                # message body

  SearchResult = seq[int]       # sequence numbers or UIDs
```

---

## Error Handling

All errors are exceptions inheriting from `IOError`:

```nim
try:
  client.connect()
  client.login("user", "wrong_password")
except AuthenticationError as e:
  echo "Login failed: ", e.msg
except Pop3Error as e:
  echo "POP3 protocol error: ", e.msg
except ImapError as e:
  echo "IMAP protocol error: ", e.msg
except MailError as e:
  echo "General mail error: ", e.msg
```

Exception hierarchy:

```
IOError
  └── MailError
        ├── Pop3Error
        ├── ImapError
        └── AuthenticationError
```

## Common Patterns

### Download All Messages (POP3)

```nim
let client = newPop3Client("mail.example.com", Port(995), useSsl = true)
client.connect()
client.login("user@example.com", "password")

let stats = client.stat()
for i in 1..stats.count:
  let content = client.retr(i)
  writeFile("message_" & $i & ".eml", content)

client.quit()
```

### Monitor Inbox for New Messages (IMAP)

```nim
let client = newImapClient("imap.example.com", Port(993), useSsl = true)
client.connect()
client.login("user@example.com", "password")
let status = client.select("INBOX")

let unseen = client.search("UNSEEN")
if unseen.len > 0:
  let resp = client.fetch($unseen[0], "(BODY.PEEK[HEADER] FLAGS)")
  for line in resp.untagged:
    echo line

client.logout()
```

### Move Messages Between Folders (IMAP)

```nim
# Copy to destination, then delete from source
client.copy("1:5", "Archive")
discard client.store("1:5", "+FLAGS", @["\\Deleted"])
client.expunge()
```

## Requirements

- Nim >= 2.0.0
- No external dependencies
- For SSL/TLS: compile with `-d:ssl` (requires OpenSSL)

## License

MIT
