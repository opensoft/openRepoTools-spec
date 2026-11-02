# Spec Delta: the register service

## Purpose

`lanes-register` is a local-socket service that holds one database connection per workstation for the register's reads and answers the prompt guard's question in milliseconds. The `UserPromptSubmit` guard and the `SessionStart` block are its clients, and the guard reads the database directly when the service is unavailable. Writes never depend on the service.

## ADDED Requirements

### Requirement: The service serves the reads and the guard, and writes do not depend on it
`lanes-register` SHALL serve this change's storage layer over a local socket. One process SHALL hold the database connection and the credential for the reads, and SHALL answer the reads every act and guard makes (`who`, `binding`, `live-holder`, `next-free`, `managed-projection` and the guard's triple check) in milliseconds. A `lanes-edit.sh` write SHALL open its own transaction with its own REGISTER-WRITER credential, and SHALL go on when the service is down.

#### Scenario: A read through the service
- **WHEN** a client asks the service `who` on a `mode=primary` workstation
- **THEN** the service answers from the database, equal to the direct read

#### Scenario: A write with the service down
- **WHEN** the service is not running and a write is made
- **THEN** the write succeeds through its own transaction and is not refused for want of the service

### Requirement: Every answer that decides state is read fresh
Every answer that decides state SHALL be read from the database for that request, or proven current against the journal's head sequence in that request. A parse the service keeps SHALL never answer alone. With the database unreachable, the service SHALL refuse as the direct read would (69, or 2 for the guard) and SHALL never answer from what it read before.

#### Scenario: A write between two requests
- **WHEN** another process writes between two requests to the service
- **THEN** the second answer includes that write and equals the direct read

#### Scenario: The database is unreachable while the service is up
- **WHEN** the service is up and the database becomes unreachable
- **THEN** the guard's answer is 2 and never a cached `ok`, and the other reads refuse with 69

### Requirement: The service reaches the database only through `psql`
The service SHALL hold its connection as a long-lived `psql` child on pipes. It SHALL import no database driver and SHALL keep `dependencies = []` in the package metadata, unless the design names a driver as a new dependency for Brett Heap's word.

#### Scenario: No driver
- **WHEN** the package metadata is read
- **THEN** it lists no runtime dependency and no source file of the service imports a database driver

### Requirement: The service starts on demand with no supervisor of its own
The service SHALL start on demand ("socket activation or first call") and SHALL need no supervisor of its own: no systemd and no launchd. How it starts on Linux and macOS, and in containers that do not share `/tmp`, is the design's.

#### Scenario: The first call starts the service
- **WHEN** a client calls a service that is not running, where the service can start
- **THEN** the service starts on that call and answers it, with no supervisor running

#### Scenario: A service that cannot start
- **WHEN** a client calls a service that is not running and cannot be started
- **THEN** the client falls back to the direct read and the call is answered

### Requirement: New code keeps to the toolchain floor
New bash SHALL keep to the hygiene suite's bash-3.2 rules. New Python SHALL be standard-library only and SHALL run on Python 3.9 or later.

#### Scenario: The hygiene suite
- **WHEN** the hygiene suite runs over the service's and the client's files
- **THEN** it passes under its bash-3.2 rules and finds only standard-library imports

### Requirement: The `UserPromptSubmit` guard is the service's client
The guard SHALL send the service what it reads today and a service cannot read for itself: the hook's `session_id`, `cwd`, `agent_id` and `prompt`; the window reference and the window name; and the live record's `name`, `nameSource` and liveness facts, which the client SHALL read from this container's session records and tmux. The service SHALL answer `ok`, `refuse <reason>` or `offer <the three Amendment 12 Addendum 1 choices>`, and the guard SHALL exit 0 or 2. The offer file, the `/rename` typing, and answer 2's `lane-start --no-launch --bind` and `set-row-state` SHALL stay client-side acts, and those two writes SHALL be transactions through the storage layer. The hook SHALL stay, because the harness's exit code is the only thing that refuses a prompt. The socket call SHALL be a standard-library Python client. The client's form (a block at the top of `lanes-edit.sh guard` that keeps the hook string, or a separate program with a new hook string) is the design's.

#### Scenario: A prompt that passes
- **WHEN** the service answers `ok`
- **THEN** the guard exits 0

#### Scenario: A prompt that is refused
- **WHEN** the service answers `refuse <reason>`
- **THEN** the guard prints the reason and exits 2

#### Scenario: An offer
- **WHEN** the service answers `offer`
- **THEN** the client writes the offer file and handles the answer to a pending offer client-side
- **AND** answer 2's two writes are register transactions

### Requirement: The `CLAUDE_NO_LANE=1` exception is kept
The guard's `CLAUDE_NO_LANE=1` exception of `skip-no-lane-guard` (the exact value, exit 0 before any input is read or any identity probed) SHALL be kept unchanged, whether the guard goes through the service or reads directly.

#### Scenario: A session with no lane
- **WHEN** a prompt arrives from a process launched with `CLAUDE_NO_LANE=1`
- **THEN** the guard exits 0 before it reads input or contacts the service or the database

### Requirement: The guard's answers through the service equal the direct read
The guard's answer through the service SHALL be byte-identical to the direct read for `ok`, `refuse` and `offer`, compared as stdout, stderr (the fallback line aside) and the exit code.

#### Scenario: The same answer both ways (agreement test 10)
- **WHEN** the same prompt is put through the service and through the direct read
- **THEN** stdout, stderr (less the fallback line) and the exit code are equal for each of `ok`, `refuse` and `offer`

### Requirement: The `SessionStart` block is a client the same way
The `SessionStart` block SHALL become a client of the service. It SHALL stay read-only, SHALL still exit 0 always, and SHALL still read offline through the mirror.

#### Scenario: A session starts with the service down
- **WHEN** the `SessionStart` block runs and the service is unavailable
- **THEN** it answers from the mirror and exits 0

### Requirement: The guard falls back to the database, never the mirror
With the service unavailable, the hook SHALL fall back to the direct read of `lanes-edit.sh guard` and SHALL print `guard: direct (service unavailable)` on stderr, with the same exit code. On `mode=primary` that direct read SHALL be of the database and never the mirror, except during a declared outage (below), and where it cannot read the database it SHALL refuse with 2. The direct read SHALL hold the READER credential itself, from the 0600 `lanes-index.conf` and its password file, so that no database URL or password enters the hook's environment. Writes SHALL never fall back to the files.

> Pending Brett's Q11: written under option (a).

#### Scenario: The service is down and the database is up (agreement test 10)
- **WHEN** the service is down and the database is reachable
- **THEN** the guard answers from the database, prints the fallback line, and exits with the code the service would have given

#### Scenario: The service is down and the database is not
- **WHEN** the service is down, the database cannot be read, and no outage is declared
- **THEN** the guard refuses with 2 and never reads the mirror

### Requirement: A service that accepts and never answers counts as unavailable
A service that accepts a connection and never answers SHALL be treated as unavailable. The client SHALL bound its connect-and-answer wait well under the hook's 5 s timeout, so that the direct read has room to finish inside it, and the service SHALL answer `refuse` before the client's own limit runs out. The wait's value is the design's.

#### Scenario: A silent service
- **WHEN** a service accepts a connection and never answers
- **THEN** the hook prints the fallback line, exits as the direct read does, and finishes inside its 5 s

### Requirement: The 5 s is one budget, spent once
The client's wait for the service, the fallback's own reads and every database access on the guard's path SHALL each get a share, and the shares SHALL add up to less than `GUARD_TIMEOUT`, with room to print the refusal. Every connection on that path, the service's and the fallback's, SHALL set its own connect timeout, `statement_timeout` and `lock_timeout` within its share, and none SHALL use `lanes-index`'s default `PGCONNECT_TIMEOUT=5` or the 300 s it allows a `psql` run. A database that does not answer SHALL be a 2 inside the hook and never a kill.

#### Scenario: A database address whose connect never completes (agreement test 10)
- **WHEN** the database address is one whose connect never completes, with the service up and with it down
- **THEN** the hook exits 2 inside its 5 s

#### Scenario: A listener that accepts and never speaks (agreement test 10)
- **WHEN** the database address is a loopback listener that accepts and never speaks, with the service up and with it down
- **THEN** the hook exits 2 inside its 5 s

#### Scenario: A read held behind a lock (agreement test 10)
- **WHEN** the guard's read is held behind a lock, with the service up and with it down
- **THEN** the hook exits 2 inside its 5 s

### Requirement: The hook's environment carries no credential
The hook's environment, and every environment a session inherits, SHALL carry no database URL and no password (Amendment 14(c)).

#### Scenario: An inherited environment
- **WHEN** the hook's environment and a session's inherited environment are inspected
- **THEN** neither carries a database URL or a password

### Requirement: The guard fails closed during a database outage
On `mode=primary`, before an outage is declared and outside one, the guard SHALL refuse with 2 while the database is unreachable. Its refusal SHALL name the store, the cause and the two ways out: wait for the database, or run `lanes-index status` and `lanes-index rollback` from a shell or from a `CLAUDE_NO_LANE=1` session. Because a rollback needs the database, with the database lost for good the second way out SHALL be the disaster procedure and not a rollback, and the manual SHALL say the same. `lanes-index status` and `lanes-index rollback` SHALL run with no lane session and no guard.

> Pending Brett's Q4(b): written under option (iii).
> Pending Brett's Q26: written under option (a′).

#### Scenario: A prompt during an outage (agreement test 10)
- **WHEN** the database is unreachable and a prompt is submitted
- **THEN** the guard exits 2 and its refusal names the store, the cause and both ways out

#### Scenario: The way out with no lane
- **WHEN** a person runs `lanes-index status` from a shell or from a `CLAUDE_NO_LANE=1` session during the outage
- **THEN** it runs with no lane session and no guard

### Requirement: A declared outage lets the guard answer from the mirror
During an outage Brett Heap declares on his word, when he knows that no workstation can still write, and until the expiry he names when he declares it, the guard SHALL answer from the mirror, labelled `read: mirror`, while every write and every other deciding read still refuses. Before a declaration, after its expiry, and outside one, the guard SHALL refuse with 2. Each guard SHALL be able to read the declaration. The declaration SHALL NOT be a row in the database that is down; where it lives is the design's. It SHALL record the mirror's `Register-Seq` when it is declared, and the guard SHALL answer from the mirror only where the tip it reads carries a `Register-Seq` at least that high. `lanes-index status` SHALL report a declaration still in place while the database answers.

> Pending Brett's Q26: written under option (a′).
> Pending Brett's Q4(b): written under option (iii). Under (i), (ii) or (iv), a rollback's start may also declare it.

#### Scenario: A mirror older than the declaration
- **WHEN** an outage is declared and the tip the guard reads carries a `Register-Seq` below the one the declaration records
- **THEN** the guard refuses with 2

#### Scenario: Service and database both down in a declared outage
- **WHEN** the service is down, the database cannot be read, and an unexpired outage is declared
- **THEN** the guard answers from the mirror, labelled `read: mirror`, and exits as that answer gives

#### Scenario: An expired declaration
- **WHEN** the database is unreachable and the declaration's expiry has passed
- **THEN** the guard refuses with 2

#### Scenario: Before a declaration
- **WHEN** the database is unreachable and no outage has been declared
- **THEN** the guard refuses with 2

#### Scenario: During a declared outage
- **WHEN** Brett has declared an outage and a prompt is submitted
- **THEN** the guard answers from the mirror, labelled `read: mirror`, and every write and every other deciding read still refuses

#### Scenario: The declaration does not need the database
- **WHEN** an outage is declared while the database is down
- **THEN** each guard can read the declaration without the database

### Requirement: The service-backed guard is fast, and the cost is recorded
The service-backed guard SHALL answer in under 50 ms on the suite's fixture register, measured as Q10 settles. The PR body SHALL record that figure with today's direct guard recorded beside it. Before the client's form is settled, the design SHALL measure today's guard in four parts: reaching the guard arm, tmux, the scan of the session records, and the register read, and SHALL say which parts the service removes.

#### Scenario: The recorded guard time (agreement test 10)
- **WHEN** the PR is opened
- **THEN** its body records the service-backed guard's time and today's direct guard's time beside it

### Requirement: The socket API is documented
The socket API SHALL be documented in `docs/README-lanes.md`. It SHALL be the surface that the 001 feature's lanes service (T055) adopts or wraps, so that Codex and the managed path ask the same question of the same process.

#### Scenario: The manual
- **WHEN** `docs/README-lanes.md` is read
- **THEN** its "The SQL register" section documents the socket API

### Requirement: The cutover waits for the service only if the guard needs it
`lanes-register` SHALL be the feature's last slice. Act 4 SHALL wait for it only if the guard, measured without it, is over a budget that Brett Heap names once the measurement is in. The service SHALL stay in #165 either way.

> Pending Brett's Q29: written under option (a).

#### Scenario: The guard is within the budget
- **WHEN** the measured direct guard is within the budget Brett names
- **THEN** act 4 does not wait for the service

#### Scenario: The guard is over the budget
- **WHEN** the measured direct guard is over that budget
- **THEN** act 4 waits for the service
