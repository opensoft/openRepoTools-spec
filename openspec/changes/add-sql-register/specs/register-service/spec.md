# Spec Delta: the register service

## Purpose

`lanes-register` is a local-socket service that holds one database connection per environment (a bench container or a host shell) for the register's reads, and answers the register's text at the journal's head in milliseconds; the reader decides. The `UserPromptSubmit` guard and the other readers are its clients, the `SessionStart` block only starts it, and the guard reads the database directly when the service is unavailable. Writes never depend on the service.

## ADDED Requirements

### Requirement: The service serves the reads and the guard, and writes do not depend on it
`lanes-register` SHALL serve this change's storage layer over a local socket. One process SHALL hold the database connection and the credential for the reads. For the reads every act and guard makes (`who`, `binding`, `live-holder`, `next-free`, `managed-projection` and the guard's triple check), the service SHALL answer the register's text at the journal's head `n` in milliseconds, and the reader SHALL decide, running today's parser on that text in its own process. A `lanes-edit.sh` write SHALL open its own transaction with its own REGISTER-WRITER credential, and SHALL go on when the service is down.

> Brett's answer to Q2(a) and (b): round 1's 4A, #165 6710653292.

#### Scenario: A read through the service
- **WHEN** a reader runs `who` through the service on a `mode=primary` workstation
- **THEN** the service answers the register's text at `n` from the database, and the reader's answer equals the direct read

#### Scenario: A write with the service down
- **WHEN** the service is not running and a write is made
- **THEN** the write succeeds through its own transaction and is not refused for want of the service

### Requirement: Every answer that decides state is read fresh
Every answer that decides state SHALL be read from the database for that request, or proven current against the journal's head sequence in that request. A parse or an image the service keeps SHALL never answer alone. With the database unreachable, the service SHALL refuse, or answer `unavailable` so that the direct read decides, and either way the reader SHALL exit as the direct read would (69, or 2 for the guard's prefetch, its first read); the service SHALL never answer from what it read before.

#### Scenario: A write between two requests
- **WHEN** another process writes between two requests to the service
- **THEN** the second answer includes that write and equals the direct read

#### Scenario: The database is unreachable while the service is up
- **WHEN** the service is up, the database becomes unreachable before the prompt, and no outage is declared
- **THEN** the guard's answer is 2 and never one from text read before, and the other reads refuse with 69

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
The guard SHALL send the service only the names of the files it reads. What it reads today and a service cannot read for itself (the hook's `session_id`, `cwd`, `agent_id` and `prompt`; the window reference and the window name; and the live record's `name`, `nameSource` and liveness facts) SHALL stay in the guard, which SHALL read them from this container's session records and tmux. The service SHALL answer the register's text at `n`, and the reader SHALL decide: the guard SHALL run today's parser on that text and SHALL exit 0 or 2 as it decides, passing the prompt, refusing it, or making the offer of the three Amendment 12 Addendum 1 choices. The offer file, the `/rename` typing, and answer 2's writes SHALL stay client-side acts, run detached from the hook. Answer 2's writes SHALL be three writer calls: `lane-start --no-launch --bind`'s register writes as one verb, `start-record`; the guard's `set-row-state` on the lane the window left; and the Rule 3 stamp, a git commit to the handoff outside the register. The two register writes SHALL be transactions through the storage layer. The hook SHALL stay, because the harness's exit code is the only thing that refuses a prompt. The socket call SHALL be a standard-library Python client. The client's form (a block at the top of `lanes-edit.sh guard` that keeps the hook string, or a separate program with a new hook string) is the design's.

#### Scenario: The request carries only file names
- **WHEN** the guard asks the service for its read
- **THEN** the request names only the files it reads, and no session, window, record or liveness fact leaves the guard

#### Scenario: A prompt that passes
- **WHEN** the service answers the text at `n` and today's parser passes the prompt on it
- **THEN** the guard exits 0

#### Scenario: A prompt that is refused
- **WHEN** the service answers the text at `n` and today's parser refuses the prompt on it
- **THEN** the guard prints the reason and exits 2

#### Scenario: An offer
- **WHEN** the service answers the text at `n` and today's parser makes the offer on it
- **THEN** the client writes the offer file and handles the answer to a pending offer client-side
- **AND** answer 2's register writes are transactions, run detached from the hook

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

### Requirement: The `SessionStart` block reads the mirror and only starts the service
The `SessionStart` block SHALL stay read-only, SHALL read the mirror offline and SHALL exit 0 always; on a `mode=primary` workstation it SHALL start the service after its answer, without waiting for it.

#### Scenario: A session starts with the service down
- **WHEN** the `SessionStart` block runs and the service is unavailable
- **THEN** it answers from the mirror and exits 0

#### Scenario: A session start warms the service
- **WHEN** the `SessionStart` block runs on a `mode=primary` workstation with no service running
- **THEN** it answers from the mirror, exits 0, and starts the service without waiting for it

### Requirement: The guard falls back to the database, never the mirror
With the service unavailable, the hook SHALL fall back to the direct read of `lanes-edit.sh guard` and SHALL print `guard: direct (service unavailable)` on stderr, with the same exit code. On `mode=primary` that direct read SHALL be of the database and never the mirror, except during a declared outage, under the declared-outage requirement, and where its prefetch, its first read, cannot read the database it SHALL refuse with 2. The direct read SHALL itself hold the credential of its engineer's reader login, a member of READER, from the 0600 `lanes-index.conf` and its password file, so that no database URL or password enters the hook's environment. Writes SHALL never fall back to the files.

> Brett's answer to Q11: option (a), #165 6710653292.

#### Scenario: The service is down and the database is up (agreement test 10)
- **WHEN** the service is down and the database is reachable
- **THEN** the guard answers from the database, prints the fallback line, and exits with the code the service would have given

#### Scenario: The service is down and the database is not
- **WHEN** the service is down, the database cannot be read, and no outage is declared
- **THEN** the guard refuses with 2 and never reads the mirror

### Requirement: A service that accepts and never answers counts as unavailable
A service that accepts a connection and never answers SHALL be treated as unavailable. The client SHALL bound its connect-and-answer wait well under the hook's 5 s timeout, so that the direct read has room to finish inside it, and the service SHALL refuse, or answer `unavailable` so that the direct read decides, before the client's own limit runs out. The wait's value is the design's.

#### Scenario: A silent service
- **WHEN** a service accepts a connection and never answers
- **THEN** the hook prints the fallback line, exits as the direct read does, and finishes inside its 5 s

### Requirement: The 5 s is one budget, spent once
The client's wait for the service, the fallback's own reads and every database access on the guard's path SHALL each get a share, and the shares SHALL add up to less than `GUARD_TIMEOUT`, with room to print the refusal. Every connection on that path, the service's and the fallback's, SHALL set its own connect timeout, `statement_timeout` and `lock_timeout`, each within its share, except the service's connect, which SHALL run only in the background, outside any share, so that no request waits for it, and the direct read's connect timeout, the workstation's own, which MAY exceed its share because the read's own timer SHALL end the read, its connect included, within that share; none SHALL use `lanes-index`'s default `PGCONNECT_TIMEOUT=5` or the 300 s it allows a `psql` run. In every mode, answer 2's writes SHALL run nowhere on the hook's time: the guard SHALL record the bind in the session's offer file, SHALL start the bind as a child in a session of its own, which outlives the hook whether the hook exits or is timed out, and SHALL exit 2 with "BINDING". The offer file SHALL be the bind's record, and the bind SHALL count as live only while its pid is live with the start time the record holds. While the bind runs, the guard SHALL refuse each prompt of that session with 2 ("BINDING IN PROGRESS") and pass none; once the bind ends, the guard SHALL report its outcome once, from the record. After a failed bind the window SHALL be renamed back and the offer kept, so that answer 2 can be given again, at most three times per offer. A database that does not answer SHALL never be a kill: for the guard's prefetch it SHALL be a 2 inside the hook, and for its second read it SHALL end that read inside the hook. A guard's client making the guard's prefetch, its first read, whose elapsed time from the hook's start is at or above the direct read's cap when it starts SHALL exit as "too late to verify", and the guard SHALL refuse with 2, even with the database healthy, since a hook that late has no time for a read to finish and a killed hook fails open; the cap's value is the design's. The guard's second read for one log at the prefetched head, started at or above `T0` + 3.75 s, where `T0` is the hook's start, whatever a declaration says, SHALL exit as "too late to verify". That second read SHALL fail open on every end but 0, which is 0 with the one log's files written: every other end (2 for too late, 69, 78, 1, 64, a crash, or a 0 that wrote no `.log.<key>`) SHALL be a log that could not be read, the read site SHALL return non-0, and the guard SHALL note it as today and SHALL NOT refuse. Under an unexpired declaration that names this workstation's database, the prefetch's direct read left less than its floor of 0.5 s before that cap when it would start SHALL NOT start: the client SHALL exit as "too late to verify", and the guard SHALL refuse with 2.

> The second read fails open on every end but 0, and its cut is `T0` + 3.75 s whatever a declaration says: lane 3's ruling B4, #165 6738474549, in lane 5's words, with its B5, as lane 1's contract draft 4.1 carries them (#165 6739202842).

#### Scenario: A database address whose connect never completes (agreement test 10)
- **WHEN** the database address is one whose connect never completes, with the service up and with it down
- **THEN** the hook exits 2 inside its 5 s

#### Scenario: A listener that accepts and never speaks (agreement test 10)
- **WHEN** the database address is a loopback listener that accepts and never speaks, with the service up and with it down
- **THEN** the hook exits 2 inside its 5 s

#### Scenario: A read held behind a lock (agreement test 10)
- **WHEN** the guard's prefetch is held behind a lock, with the service up and with it down
- **THEN** the hook exits 2 inside its 5 s

#### Scenario: Answer 2 runs detached
- **WHEN** a person answers `2` to an offer
- **THEN** the hook exits 2 inside its 5 s with "BINDING", the offer file records the bind with the child's pid and start time, and the bind is not ended with the hook, whether the hook exits or is timed out

#### Scenario: A prompt while the bind runs
- **WHEN** a prompt arrives from the same session while its bind is live
- **THEN** the guard exits 2 with "BINDING IN PROGRESS", the prompt is not passed, and the record is unchanged

#### Scenario: The bind succeeds
- **WHEN** the bind has ended with success and the next prompt arrives
- **THEN** the guard reports the move once, with any act still owed, exits 2 and removes the record, and the prompt after it exits 0

#### Scenario: The bind fails
- **WHEN** the bind has ended with a failure and the next prompt arrives
- **THEN** the guard reports the failure's reason, the window is renamed back, the offer is kept, and a further answer `2` starts the next attempt, up to the third

#### Scenario: A bind that left no outcome
- **WHEN** the record says the bind is running on this host and container, and its pid now belongs to another process, or is dead with no process of the bind's session left
- **THEN** the guard reports once that the bind left no outcome and keeps the offer, and the next answer `2` first looks up whether the earlier bind's write committed, where a call of it may have been sent

#### Scenario: A bind whose outer died
- **WHEN** the record's pid is dead but a process of the bind's session is still running
- **THEN** the guard answers as for a bind in progress, and no second bind starts

#### Scenario: A guard that starts too late
- **WHEN** the database is healthy and the guard's prefetch starts with an elapsed time from the hook's start at or above the direct read's cap, or, under an unexpired declaration that names this workstation's database, its direct read would start with less than 0.5 s before that cap
- **THEN** the client exits as "too late to verify" and the guard refuses with 2 inside its 5 s

#### Scenario: A second read that starts too late
- **WHEN** the guard's prefetch has answered and its second read, for one log at the prefetched head, would start at or above `T0` + 3.75 s, whatever a declaration says
- **THEN** the client exits as "too late to verify", the guard notes that log as unread, and the prompt is not refused for it

#### Scenario: A second read that cannot read the database
- **WHEN** the guard's prefetch has answered and the database then stops answering, so that the second read, for one log at the prefetched head, ends 69, or the client crashes
- **THEN** the guard notes that log as unread and the prompt is not refused for it

### Requirement: The hook's environment carries no credential
The hook's environment, and every environment a session inherits, SHALL carry no database URL and no password (Amendment 14(c)).

#### Scenario: An inherited environment
- **WHEN** the hook's environment and a session's inherited environment are inspected
- **THEN** neither carries a database URL or a password

### Requirement: The guard fails closed during a database outage
On `mode=primary`, before an outage is declared and outside one, the guard SHALL refuse with 2 while the database is unreachable to its prefetch, its first read. Its refusal SHALL name the store, the cause and the two ways out: wait for the database, or run `lanes-index status` and `lanes-index rollback` from a shell or from a `CLAUDE_NO_LANE=1` session. Because a rollback needs the database, with the database lost for good the second way out SHALL be the disaster procedure and not a rollback, and the manual SHALL say the same. `lanes-index status` and `lanes-index rollback` SHALL run with no lane session and no guard.

> Brett's answer to Q4(b): option (iii), #165 6710653292.
> Brett's answer to Q26: option (a′), #165 6710653292.

#### Scenario: A prompt during an outage (agreement test 10)
- **WHEN** the database is unreachable and a prompt is submitted
- **THEN** the guard exits 2 and its refusal names the store, the cause and both ways out

#### Scenario: The way out with no lane
- **WHEN** a person runs `lanes-index status` from a shell or from a `CLAUDE_NO_LANE=1` session during the outage
- **THEN** it runs with no lane session and no guard

### Requirement: A declared outage lets the guard answer from the mirror
During an outage Brett Heap declares on his word, when he knows that no workstation can still write, or that a rollback's start declares on his act, and until the expiry he names or the rollback's end, the guard SHALL answer from the mirror, labelled `read: mirror`, while every write and every other deciding read still refuses. A declaration on Brett Heap's word SHALL record the mirror's `Register-Seq` when it is declared. A rollback's start SHALL write the declaration, marked as from a rollback, wherever its configuration directory is writable, and SHALL record in it the head its transaction found, the seq before the rollback's own entry, not the mirror's; the declaration the rollback prints for other hosts SHALL carry that seq too. The guard's client, and never the service, SHALL check three conditions in this order, offline, and the guard SHALL answer from the mirror only when all three hold: its prefetch's database read failed, or found the switch at `files`, as at a rollback's start; a declaration is present, unexpired, and names this workstation's database, where a declaration marked as from a rollback carries no expiry, counts as unexpired until it is cleared, and SHALL count only where the guard's own database read found the switch at `files`; and the mirror it reads carries a `Register-Seq` at least as high as the declaration records. The client SHALL read the mirror's `Register-Seq` by a lookup bounded to the 200 most recent first-parent commits of the ref it reads, and SHALL refuse with 2 where none of them carries one. Where its prefetch's database read failed and either other condition fails, the guard SHALL refuse with 2. Each guard SHALL be able to read the declaration. The declaration SHALL NOT be a row in the database that is down; where it lives is the design's. `lanes-index status` SHALL report a declaration still in place while the database answers.

> Brett's answer to Q26: option (a′), #165 6710653292.
> Brett's answer to Q4(b): option (iii), #165 6710653292; a rollback's start declares the outage (design D20).

#### Scenario: A mirror older than the declaration
- **WHEN** an outage is declared and the tip the guard reads carries a `Register-Seq` below the one the declaration records
- **THEN** the guard refuses with 2

#### Scenario: Service and database both down in a declared outage
- **WHEN** the service is down, the database cannot be read, and an unexpired outage is declared
- **THEN** the guard answers from the mirror, labelled `read: mirror`, and exits as that answer gives

#### Scenario: A declaration that names another database
- **WHEN** the database is unreachable and the declaration names a database other than this workstation's
- **THEN** the guard refuses with 2

#### Scenario: A database that answers during a declaration
- **WHEN** an unexpired outage is declared and the guard's database read succeeds
- **THEN** the guard answers from the database and does not read the mirror

#### Scenario: No `Register-Seq` within the bound
- **WHEN** an outage is declared, the database cannot be read, and none of the 200 most recent first-parent commits of the mirror carries a `Register-Seq`
- **THEN** the guard refuses with 2

#### Scenario: An expired declaration
- **WHEN** the database is unreachable and the declaration's expiry has passed
- **THEN** the guard refuses with 2

#### Scenario: Before a declaration
- **WHEN** the database is unreachable and no outage has been declared
- **THEN** the guard refuses with 2

#### Scenario: During a declared outage
- **WHEN** Brett has declared an outage and a prompt is submitted
- **THEN** the guard answers from the mirror, labelled `read: mirror`, and every write and every other deciding read still refuses

#### Scenario: A rollback's start
- **WHEN** a rollback has set the switch to `files` at head `k`, and a prompt is submitted on a workstation whose ref carries a `Register-Seq` of at least `k`
- **THEN** the guard answers from the mirror under the rollback's declaration, and every write still refuses

#### Scenario: A ref behind a rollback's head
- **WHEN** a rollback has set the switch to `files` at head `k`, and the ref a guard reads carries a `Register-Seq` below `k`
- **THEN** the guard refuses with 2, telling the person that the mirror there is behind the declared outage's seq, to fetch it once origin carries that seq or wait for the outage to end
- **AND** it does not answer from that mirror

#### Scenario: A rollback's declaration once the switch is primary again
- **WHEN** a declaration marked as from a rollback is still present, a cutover has set the switch to `primary`, and the guard's database read fails
- **THEN** the guard refuses with 2
- **AND** it does not answer from the mirror

#### Scenario: A rollback's declaration in a partition
- **WHEN** a rollback has set the switch to `files`, its declaration is present, and the guard's database read fails without reading the switch
- **THEN** the guard refuses with 2
- **AND** it does not answer from the mirror

#### Scenario: The declaration does not need the database
- **WHEN** an outage is declared while the database is down
- **THEN** each guard can read the declaration without the database

### Requirement: `outage declare` during a rollback records the rollback's head
While `lanes-index outage declare`'s database probe reads the switch at `files` after a rollback, a declaration on Brett Heap's word SHALL be refused with 2, writing nothing, and the refusal SHALL print the `outage declare --rollback --mirror-seq <k>` command to run instead, with `k` as `set_seq - 1`, so that no declaration made during a rollback records the mirror's `Register-Seq`. The `--rollback` form SHALL require `--mirror-seq <k>`, and SHALL be refused with 2 unless the journal entry at the switch's `set_seq` is a `rollback` and `<k>` is `set_seq - 1`, the head before that entry. Where its probe cannot read the switch, the `--rollback` form SHALL be refused with 2, and SHALL write no declaration. In either form, `outage declare` SHALL be refused with 2 where its probe reads the switch at `primary`, since the database then answers from this host.

#### Scenario: Brett's word during a rollback
- **WHEN** `outage declare` runs on Brett's word while its probe reads the switch at `files` after a rollback
- **THEN** it refuses with 2 and writes no declaration
- **AND** it prints `outage declare --rollback --mirror-seq <k>`, with `k` as `set_seq - 1`, to run instead

#### Scenario: A wrong `<k>`
- **WHEN** `outage declare --rollback --mirror-seq <k>` runs while its probe reads the switch at `files` after a rollback, with `<k>` other than `set_seq - 1`
- **THEN** it refuses with 2 and writes no declaration

#### Scenario: A rollback's declaration where the switch cannot be read
- **WHEN** `outage declare --rollback --mirror-seq <k>` runs where its probe cannot read the switch
- **THEN** it refuses with 2 and writes no declaration

#### Scenario: A declaration while the switch is at `primary`
- **WHEN** `outage declare` runs, in either form, where its probe reads the switch at `primary`
- **THEN** it is refused with 2 and writes no declaration

### Requirement: The service answers the guard's read fast, and the cost is recorded
The service SHALL answer the guard's read in under 50 ms on the suite's fixture register, measured as Q10 settles, from the client's connect to the last byte of a digest-checked answer. The PR body SHALL record that figure with the whole guard's wall time, through the service and through the direct read, and today's direct guard recorded beside it; the whole guard's figure is Q29's. Before the client's form is settled, the design SHALL measure today's guard in four parts: reaching the guard arm, tmux, the scan of the session records, and the register read, and SHALL say which parts the service removes.

#### Scenario: The recorded guard time (agreement test 10)
- **WHEN** the PR is opened
- **THEN** its body records the service's answer time for the guard's read, with the whole guard's time through the service and through the direct read, and today's direct guard's time, beside it

### Requirement: The socket API is documented
The socket API SHALL be documented in `docs/README-lanes.md`: the service answers the register's text at `n`, and the reader decides. The 001 feature's lanes service (T055) SHALL adopt it through `lanes-edit.sh`'s read verbs, which ask the service and run today's parser, so that Codex and the managed path read the same text from the same process and decide by the same parser.

#### Scenario: The manual
- **WHEN** `docs/README-lanes.md` is read
- **THEN** its "The SQL register" section documents the socket API

### Requirement: The cutover waits for the service only if the guard needs it
`lanes-register` SHALL be built beside groups A and B of the feature's landing path, not as its last slice. Act 4 SHALL wait for it only if the guard, measured without it, is over Brett Heap's budget of 1 s, judged on the warm p95; the measurement from Eagle puts that guard over it, so act 4 waits for the service. The service SHALL stay in #165 either way.

> Brett's answer to Q29: option (a), #165 6710653292; his budget, "1 s (Recommended)", #165 6722025863; lane 5's measurement, #165 6725361742; lane 3's ruling, #165 6724332510 and 6726078173.

#### Scenario: The guard is within the budget
- **WHEN** the measured direct guard is within Brett's budget of 1 s
- **THEN** act 4 does not wait for the service

#### Scenario: The guard is over the budget
- **WHEN** the measured direct guard is over that budget
- **THEN** act 4 waits for the service
