# Spec Delta: the SQL register

## Purpose

On a workstation configured `mode=primary` the lane register is a database: each write is one transaction, the git files under `lanes/` are its rendered mirror, and nothing falls back to writing the files. This capability fixes the configuration and the estate switch, the write path and its exit codes, the journal and the renderer, readers that fail closed, import and rollback, claim order, the roles, the operator tools, and the evidence that proves them.

## ADDED Requirements

### Requirement: `mode=` selects the register's store
`lanes-index.conf` SHALL accept a `mode=` key whose value is `index` (the default when the key is absent, and today's behaviour) or `primary`, and SHALL accept the REGISTER-WRITER's own credential pair (for example `register_url=` and `register_passfile=`) beside the existing keys. `lanes-index` SHALL stay the one parser of the file: `lanes-edit.sh` SHALL ask `lanes-index`, or the register service, for the mode and SHALL NOT parse the file itself. The indexer's `url=` credential SHALL NOT write the register's tables. A configuration that says `mode=primary` with the SQLite store SHALL be refused, because the register is shared by workstations and SQLite is one disk. A key `lanes-index` does not know SHALL still be refused with exit 2. The file's 0600 mode, its password file, its refusals and the rule that it never lives under `~/.agents/` SHALL stand as Amendment 14(c) wrote them. A `lanes-index` older than this change refuses `mode=` with exit 2, so this change SHALL be installed before `mode=` is written anywhere.

#### Scenario: Mode absent
- **WHEN** a workstation's configuration has no `mode=` key
- **THEN** it is `mode=index` and its writers and readers behave as they did before this change

#### Scenario: Primary with SQLite
- **WHEN** the configuration says `mode=primary` and names the SQLite store
- **THEN** `lanes-index` refuses the configuration and no register write is attempted

#### Scenario: Unknown key
- **WHEN** the configuration carries a key `lanes-index` does not know
- **THEN** `lanes-index` refuses it with exit 2, as it does today

#### Scenario: The register writes with its own pair
- **WHEN** `lanes-edit.sh` writes the register on a `mode=primary` workstation
- **THEN** it connects with the REGISTER-WRITER pair and never with the indexer's `url=` credential

### Requirement: The estate switch is a database row and the marker `lanes/STORE`
The estate's mode SHALL be recorded in two places: a row in the database, and the marker `lanes/STORE` in the workspace repository. The marker SHALL hold `primary`, the database's name without credentials, the import commit and the UTC, and SHALL hold no credential. The estate's rule SHALL be that every writing workstation is `mode=primary` or none is.

#### Scenario: The marker carries no secret
- **WHEN** the cutover pushes `lanes/STORE`
- **THEN** the file holds `primary`, the database's name, the import commit and the UTC
- **AND** it holds no URL with a password, no password and no passfile path

### Requirement: A writer that is not `mode=primary` refuses under the marker
Every act-2 writer, in every mode, SHALL read `lanes/STORE`; a files-mode writer SHALL make the check on the tree its Amendment 23 S3 fetch returns, and the publish act SHALL make it too. A files-mode writer whose fetched tree carries the marker SHALL refuse with exit 69 and the text "this estate's register is SQL; configure `mode=primary`", writing nothing. A `mode=primary` writer whose transaction finds the switch not `primary` SHALL refuse with exit 69 and the same text, writing nothing. On the writes that make no fetch before they edit, the switch inside the transaction SHALL be what stops a `mode=primary` writer, whatever that writer has fetched.

> Pending Brett's Q4(b): written under option (iii).

#### Scenario: A files-mode writer meets the marker (agreement test 3)
- **WHEN** a files-mode writer fetches a tree that carries `lanes/STORE` and tries a write
- **THEN** it exits 69 with "this estate's register is SQL; configure `mode=primary`"
- **AND** the register's rows, the journal and git are unchanged

#### Scenario: A primary writer meets a switch that is not primary
- **WHEN** a `mode=primary` writer's transaction finds the database switch set to `files`
- **THEN** it exits 69 with the same text and writes nothing

#### Scenario: Between the marker and the flip
- **WHEN** `lanes/STORE` is on origin and a workstation's configuration still says `mode=index`
- **THEN** every write that workstation tries after its fetch exits 69 and writes nothing

### Requirement: Nothing changes where the marker is absent
While `lanes/STORE` is absent, the output and exit codes of every `mode=index` and every unconfigured workstation SHALL be unchanged; each writer gains the configuration read and the marker check and nothing else. The ledger half, the handoffs, Rules 1 to 10 as rules and Amendment 7's grammar SHALL be unchanged in every mode.

#### Scenario: An unconfigured workstation writes as today
- **WHEN** a workstation with no `lanes-index.conf` makes a register write and `lanes/STORE` is absent
- **THEN** the write, its output and its exit code are those of the v1.1.0 tools

### Requirement: Every row records the mode that wrote it last
Each register row SHALL carry the mode that wrote it last, so that a mixed history stays visible.

#### Scenario: Rows read back primary
- **WHEN** any `mode=primary` write completes
- **THEN** every row it touched reads back `primary` as the mode that wrote it last

### Requirement: Readers on a workstation that is not `mode=primary` once `lanes/STORE` exists
Once `lanes/STORE` exists, the acts and the prompt guard on a workstation that is not `mode=primary` SHALL refuse, while the listings and the `SessionStart` block SHALL read the mirror and say so with the `read: mirror` line.

> Pending Brett's Q21(a): written under option (A).

#### Scenario: An unconfigured workstation after the cutover
- **WHEN** an unconfigured workstation runs an act or receives a prompt after `lanes/STORE` is on origin
- **THEN** the act refuses and the guard refuses with 2

#### Scenario: An unconfigured workstation lists
- **WHEN** the same workstation runs `lanes` or opens a session
- **THEN** the answer comes from the mirror and stderr carries the `read: mirror` line

### Requirement: A build without the renderer's git half refuses `mode=primary`
Until the renderer's git half is installed, `lanes-index` SHALL refuse `mode=primary` with exit 2 and the text `mode=primary is not available in this build: the renderer's git half is not installed, so a write could not be mirrored; nothing was written`. `lanes-edit.sh`, run on a workstation whose configuration says `mode=primary`, SHALL then exit 69, repeat that text and change neither the register nor the files, and SHALL NOT write the files instead. The design MAY reword the text and SHALL NOT weaken the refusal. The readiness check SHALL fail an environment whose installed `lanes-index` still carries this refusal.

#### Scenario: The refusal in place (agreement test 14)
- **WHEN** the refusal's constant is set and `lanes-edit.sh` runs a write on a workstation configured `mode=primary`, using the fake `psql`
- **THEN** `lanes-index` exits 2 with the text above and `lanes-edit.sh` exits 69 repeating it
- **AND** neither the register nor the files changed

#### Scenario: The refusal lifted
- **WHEN** the same constant is flipped and the same write runs
- **THEN** the write goes to SQL

#### Scenario: The readiness check sees the refusal
- **WHEN** the readiness check runs against a build with the refusal in place
- **THEN** the check fails that state

### Requirement: A server-side gate protects the rendered paths and `lanes/STORE`
Where the organisation's plan supports it, a push ruleset whose only bypass is a renderer identity SHALL be in place before the cutover, on the paths the renderer renders (`lanes/LANES.md`, `lanes/log/<lane>.md`, `lanes/aliases.tsv` and `lanes/archive/`) and on `lanes/STORE`. Every push that changes those paths SHALL use that identity: the renderer's, and the cutover's and the rollback's commits of `lanes/STORE`. The ruleset SHALL NOT cover `lanes/repos.tsv`, `lanes/log/README.md` or any other path under `lanes/` outside those, so that they stay git files a person commits and a commit to them stays part of the tip the renderer renders onto. The identity's key SHALL NOT be in any environment a session inherits (Amendment 14(c)). Where the plan does not support such a ruleset, or one that names exactly those paths, whether act 4 waits for an equal server-side gate is Brett Heap's decision.

> Pending Brett's Q21(b): written under option (B).
> Pending Brett's Q18: written under option (A).

#### Scenario: A stale writer's push is refused
- **WHEN** any identity other than the renderer's pushes a commit that changes a rendered path or `lanes/STORE`
- **THEN** the ruleset rejects the push

#### Scenario: A person's `repos.tsv` edit is not gated
- **WHEN** a person pushes a commit that changes only `lanes/repos.tsv`
- **THEN** the ruleset does not reject it, and the renderer renders onto it

#### Scenario: The renderer pushes under its identity
- **WHEN** the renderer pushes a render commit
- **THEN** the push is accepted under the renderer identity and the identity's key is in no session-inherited environment

### Requirement: `wip init` and the `LANES.md` link on a SQL estate
`openRepoTools wip init` SHALL refuse once `lanes/STORE`, or any file under `lanes/`, exists, so that its seed commit is never a stray commit under `lanes/STORE`. The `~/projects/xFactory/LANES.md` link SHALL stay for reading only, as Amendment 23's S5 already rules.

> Pending Brett's Q14: written under option (a).

#### Scenario: `wip init` on a SQL estate
- **WHEN** `openRepoTools wip init` runs in a workspace repository whose tree carries `lanes/STORE`
- **THEN** it refuses and commits nothing

#### Scenario: `wip init` where a `lanes/` file exists
- **WHEN** it runs where any file under `lanes/` exists
- **THEN** it refuses and commits nothing

### Requirement: Every register write is one transaction
On a `mode=primary` workstation each of `claim`, `release`, `log` (every verb), `set-row-state`, `append-line`, `add-row`, `rename-lane`, `retire-rows`, `archive-rows`, `migrate-state-cells`, `append-session-id`, `replace-in-row`, `request-handoff` and the managed projection writer SHALL run as one database transaction through one storage layer. Each `lanes-edit.sh` write SHALL open its own transaction with its own REGISTER-WRITER credential, and SHALL go on when the register service is down. A write that fails partway SHALL leave the register's rows, the journal, the line store and the head row unchanged and SHALL leave no lock behind.

#### Scenario: Each write is one transaction
- **WHEN** any of the listed writes completes
- **THEN** exactly one journal entry exists for it and the register's rows reflect the whole write

#### Scenario: A failure in a late statement (agreement test 2)
- **WHEN**, on the real engine, a failure is injected in a late statement of `LANDING` and `LANDED`, `rename-lane`, `retire-rows`, `archive-rows` or `migrate-state-cells`
- **THEN** the journal, the line store and the head row are unchanged and no lock is left

#### Scenario: The service is down
- **WHEN** the register service is not running and a `lanes-edit.sh` write runs on a `mode=primary` workstation
- **THEN** the write opens its own transaction and succeeds, and it never writes the files instead

#### Scenario: The projection writer's entry (agreement test 1)
- **WHEN** the managed projection writer's storage-layer entry is driven by a fake writer
- **THEN** the projection lands in SQL first and the mirror, once rendered, is byte-identical to a full render from SQL

### Requirement: Direct writes to the files refuse on `mode=primary`
On `mode=primary` the hand-edit `commit` of Rule 9(d) SHALL refuse, with every other direct write to the files: the pre-existing and concurrent captures, `add-row`'s compensating commit, and the branch-wide push that publishes any queued local commit. The publish of queued commits that Amendment 23's S4 keeps SHALL stay, with its fence narrowed to `handoffs/`, and a queued commit under `lanes/` SHALL be refused as a divergence. Each refusal SHALL change neither the register nor the files; the code each refuses with is the design's.

#### Scenario: A hand edit through `commit`
- **WHEN** a lane runs `commit` over a hand edit of a file under `lanes/`
- **THEN** it refuses and nothing is written

#### Scenario: A queued handoff commit
- **WHEN** a handoff commit is queued under `handoffs/`
- **THEN** the publish act still publishes it

#### Scenario: A queued commit under `lanes/`
- **WHEN** a queued local commit changes a path under `lanes/`
- **THEN** the publish act refuses it as a divergence

#### Scenario: A branch-wide push
- **WHEN** a direct write would push the whole branch, publishing a queued local commit
- **THEN** it refuses on `mode=primary`

### Requirement: Unreachable or refused is exit 69 with nothing written
When the database is unreachable or refuses the transaction, a register write SHALL exit 69, naming the store and the error, with nothing written anywhere and never the files instead. The prompt guard, which may exit only 0 or 2, SHALL refuse with 2. Exit 69 SHALL join the exit table of `lanes-edit.sh`.

#### Scenario: The database is unreachable (agreement test 2)
- **WHEN** the database is unreachable and any listed write runs, the projection writer's storage-layer entry included
- **THEN** it exits 69 naming the store and the error
- **AND** the register's rows, the journal and git are unchanged and no lock is left

#### Scenario: The database refuses the transaction
- **WHEN** the database refuses the transaction for a reason other than a serialization failure or a lost turn
- **THEN** the write exits 69 and nothing is written

### Requirement: A serialization failure is retried, not refused
SQLSTATE 40001 and 40P01 SHALL NOT be reported as 69. A write that meets one, or whose snapshot is stale because the head moved, SHALL re-read and re-check against the committed state and try again until a deadline, so that the later of two writers sees the earlier one's committed state and no writer in a burst is lost to a fixed small count of attempts. A retry SHALL key on the SQLSTATE and not on `psql`'s exit code, and the sequence number SHALL never come from a database SEQUENCE.

> Pending Brett's Q7: written under option (A″).

#### Scenario: The loser re-runs
- **WHEN** two writers collide and the database reports 40001 to the later one
- **THEN** that writer re-runs from its reads and does not exit 69

#### Scenario: A burst loses no writer (agreement test 4)
- **WHEN** ten writers on distinct objects are released together, each held 200 ms between its snapshot and its COMMIT
- **THEN** all ten commit, none exits busy or 69, and the sequence numbers run 1 to 10 with no gap

### Requirement: A lost race is not an error on the sequence key
The transaction SHALL update the head row before it inserts the journal row, or SHALL retry a unique-key violation on the sequence key exactly as it retries a serialization failure. A writer that loses a race SHALL NOT exit on that violation.

#### Scenario: Racing writers on the sequence key
- **WHEN** ten writers race for the next sequence number
- **THEN** none of them exits because of a unique-key violation on the sequence key

### Requirement: A write that cannot get its turn reports "register busy" with a code of its own
A write that cannot get its turn in time SHALL exit "register busy" with an exit code of its own: not 0, not 69 and not a code the exit table already carries. The report SHALL say that nothing was written and that a retry in seconds is safe. The code's number is the design's, and the exit table SHALL list it. This narrows Amendment 22's S1, which gives 69 to a transaction the database refuses: 69 stays for an unreachable database and any other refusal. The deadline SHALL exceed the drain time of the largest burst the agreement tests inject.

> Pending Brett's Q7: written under option (A″).

#### Scenario: A spent deadline
- **WHEN** a write is still losing its turn when the deadline passes
- **THEN** it exits "register busy" with its own code, says nothing was written and a retry is safe, and leaves the register unchanged

#### Scenario: Busy is not an outage
- **WHEN** a caller receives the busy code
- **THEN** the code differs from 69 and from every other code in the exit table

### Requirement: A COMMIT whose outcome the writer cannot learn is recovered by its write id
Each write SHALL carry a write id that is UNIQUE in the journal. After an outcome it cannot learn (the connection lost after COMMIT was sent, `psql` exit 2, or an exit 124 from an outer timer), the writer SHALL reconnect and look up its id, SHALL exit 0 if the write is there, and SHALL retry cleanly if it is not. The retry SHALL go through the UNIQUE write id with a `lock_timeout`, and not through a lookup followed by a blind write. A verb SHALL read the journal by write id and by sequence number. A retry of an in-doubt write SHALL reuse its write id, and the in-doubt report SHALL name the id; the report's exit code where the writer cannot reconnect is the design's.

#### Scenario: COMMIT landed, then exit 2 (agreement test 12)
- **WHEN** a `psql` wrapper commits and then exits 2
- **THEN** the writer exits 0 and exactly one journal row exists for its write id

#### Scenario: Connection lost before COMMIT (agreement test 12)
- **WHEN** a `psql` wrapper exits 2 without committing
- **THEN** the writer retries cleanly, exits 0, and exactly one journal row exists for its write id

#### Scenario: A rerun after a failed reconnect
- **WHEN** a caller reruns a write after the writer could not reconnect
- **THEN** the rerun reuses the write id and the journal still holds one entry for it

#### Scenario: A duplicate claim is not made
- **WHEN** a `claim` is retried after an in-doubt COMMIT that had landed
- **THEN** no second `CLAIMED` row exists and the retry does not exit 69 for a claim that is held

### Requirement: Handoffs stay git files written by the lane
Handoffs, `lane-start`'s stamp, `lane-handoff`'s `PAUSED` commit and the handoff skill's commit SHALL stay git commits written by the lane itself, and SHALL NOT be register writes or be written by the renderer. Every register write the lifecycle scripts make SHALL go through a `lanes-edit.sh` verb.

#### Scenario: A lifecycle act on `mode=primary`
- **WHEN** `lane-start` or `lane-handoff` runs on a `mode=primary` workstation
- **THEN** its register writes are `lanes-edit.sh` verbs landing in SQL and its handoff file is committed by the lane as a git file

### Requirement: Every database path is bounded
The readers, the renderer, `lanes-index status` and `lanes-index rollback` SHALL each set their own connect, lock and statement timeouts and run under a client timer, so that a hung database gives them an answer and not a hang. Writers SHALL have limits of their own, so that a hung database gives 69 and not a hang. A client-timer expiry SHALL be a failed read for a reader. For a writer, an expiry of a call that sends or may send COMMIT (its commit call) SHALL be an in-doubt outcome, as an exit 124 from an outer timer is; an expiry of its read-only snapshot SHALL be a failed read, exit 69 with nothing written, unless an earlier commit call of the same write is in doubt, in which case the write stays in doubt.

#### Scenario: A writer's snapshot times out
- **WHEN** a writer's read-only snapshot call reaches its client timer and no earlier commit call of that write was sent
- **THEN** the writer exits 69 and nothing is written

#### Scenario: A writer's commit times out
- **WHEN** a writer's commit call reaches its client timer
- **THEN** the write is in doubt and is recovered by its write id

#### Scenario: A silent listener (agreement test 2)
- **WHEN** the database address is a listener on the loopback address that accepts and never speaks
- **THEN** a writer exits 69 inside its own limit
- **AND** a reader, the renderer, `status` and `rollback` each exit inside their own bound

#### Scenario: A read behind a lock
- **WHEN** a read is held behind a lock a migration holds
- **THEN** the reader, the renderer, `status` and `rollback` each exit inside their own bound

### Requirement: `rename-lane` commits the transaction before moving the handoff
`rename-lane` on `mode=primary` SHALL commit its register transaction first and SHALL then move the handoff file and its stamp in the lane's checkout, as a separate git commit. A failed move SHALL leave the row's handoff cell naming a file not yet moved. The renderer SHALL NOT move the handoff file.

> Pending Brett's Q13: written under option (a).

#### Scenario: The move fails after the transaction
- **WHEN** the transaction commits and the handoff move then fails
- **THEN** the register holds the new lane name and the row's handoff cell names the file not yet moved

### Requirement: A claim's comment cites the register sequence
The claim comment SHALL cite "register seq `<n>`", where `<n>` matches the `Register-Seq: <n>` trailer of the mirror commit, and the manual SHALL say how to find that commit from the sequence number.

> Pending Brett's Q19: written under option (A).

#### Scenario: Finding the commit
- **WHEN** a reader holds a claim comment citing register seq `n`
- **THEN** the mirror commit with `Register-Seq: n` in `git log` is that write's commit

### Requirement: The journal records each write
Each transaction SHALL append one journal entry holding a sequence number, the write's commit message exactly as today (`LANES(<lane>@<ws>): …` or `LOG(<lane>@<ws>): …`) and the line edits its mirror commit makes. The sequence number SHALL follow commit order and run without gaps. An entry SHALL be able to span more than one rendered file, with file creates, renames and removals among its edits, and SHALL replay byte-identically.

#### Scenario: Sequence follows commit order
- **WHEN** two writers commit in a known order
- **THEN** the earlier commit holds the lower sequence number and no number is skipped

#### Scenario: An entry spans files
- **WHEN** `rename-lane`, `retire-rows`, `archive-rows`, `migrate-state-cells` or a `LANDING` and `LANDED` set changes two or more rendered files
- **THEN** one journal entry holds all their line edits

### Requirement: UTC comes from the database's clock in whole seconds
The database's clock SHALL stamp each row's UTC, written in whole seconds as `utc_now` writes it today, so that Rule 1's four hours and Rule 6's thirty minutes are measured on one clock. A UTC a caller composed inside `append-line` text, and `log PAUSED --utc`, SHALL stay as written. The 15 `WORD` lines whose UTC carries milliseconds SHALL be kept as they are, as Amendment 7(b) keeps them.

#### Scenario: Whole seconds
- **WHEN** a `mode=primary` write stamps a row
- **THEN** the UTC is the database's clock to the whole second

#### Scenario: A caller's UTC stands
- **WHEN** `append-line` carries a UTC composed in its text, or `log PAUSED --utc` is given one
- **THEN** that UTC is stored as written

### Requirement: The renderer commits one mirror commit per sequence
After a transaction commits, the writer SHALL fork the renderer. The renderer SHALL commit every sequence the mirror lacks, in sequence order, one commit per write, with that write's message and a `Register-Seq: <n>` trailer, so that `git log` of the workspace repository stays the audit trail in commit order. It SHALL render `lanes/LANES.md`, `lanes/log/<lane>.md`, `lanes/aliases.tsv` and `lanes/archive/`, and SHALL touch nothing else. Two renderers racing SHALL produce one commit per sequence, in order.

> Pending Brett's Q18: written under option (A).

#### Scenario: The mirror equals a full render (agreement test 1)
- **WHEN** any listed write has committed and its render has run
- **THEN** the mirror is byte-identical to a full render from SQL

#### Scenario: Two renderers race (agreement test 5)
- **WHEN** two renderers run at once
- **THEN** the mirror holds one commit per sequence, in sequence order

#### Scenario: Nothing else is touched
- **WHEN** the renderer runs
- **THEN** it changes no path outside the four rendered paths

### Requirement: The renderer commits in the writing lane's checkout under S3's fetch
The renderer SHALL commit in the checkout of the lane whose write forked it, `<W>-lanes/<lane>/`, and never in the workspace checkout `W`. For a renderer that never touches the lane's HEAD, index or working tree, Amendment 23's "takes S3's checks" SHALL mean S3's fetch, so that the renderer needs neither the lane's lock nor a clean checkout, and a lane's own stranded commit or staged entry never stops the mirror. The render SHALL be reported, not lost, when S3's fetch is refused: SQL stays true.

> Pending Brett's Q27: written under option (a).

#### Scenario: A lane checkout with a stranded commit
- **WHEN** the lane's checkout holds a stranded local commit or a staged entry and its write forks a render
- **THEN** the render still commits and pushes the mirror

#### Scenario: A render refused by S3's fetch (agreement test 5)
- **WHEN** a render is refused because S3's fetch fails
- **THEN** SQL stays true, the refusal is reported, and a later render catches up

### Requirement: The mirror is write-behind
No writer SHALL wait on the renderer beyond the fork. A failed render SHALL leave SQL true and the mirror behind, `lanes-index status` SHALL say by how many sequences, and the next render SHALL catch up in order.

#### Scenario: A hung renderer delays no writer
- **WHEN** the renderer hangs or fails after a write's fork
- **THEN** that write and the writes after it return without waiting for it

#### Scenario: A failed render shows its lag
- **WHEN** a render has failed
- **THEN** `status` prints the number of sequences the mirror is behind
- **AND** the next render commits them in order

### Requirement: A renderer that loses a push race discards and re-renders
A renderer that loses a push race SHALL discard its unpushed render commits, fetch, and render what is still missing onto the new tip. It SHALL NOT rebase a render.

#### Scenario: A lost race
- **WHEN** a render's push is rejected because `main` moved
- **THEN** its unpushed render commits are discarded and the missing sequences are rendered onto the new tip with no rebase

### Requirement: The renderer leaves nothing on a lane's HEAD
A renderer killed between its commit and its push, or stopped at its retry limit, SHALL leave no `lanes/` commit that refuses the lane checkout's next render or its next handoff and stamp commits, and SHALL need no manual step to recover. Only a render commit is disposable, never a lane's own commit under `lanes/`, which is a divergence.

#### Scenario: A renderer killed between commit and push (agreement test 5)
- **WHEN** a render is killed between its commit and its push
- **THEN** the lane's next handoff commit and the next render are not refused, and no manual step is taken

#### Scenario: A lane's own commit under `lanes/`
- **WHEN** a lane's own commit under `lanes/` sits unpushed
- **THEN** it is treated as a divergence and is not discarded as a render commit

### Requirement: One render at a time per checkout, and no fork waits
A fork that finds a render running in the same checkout SHALL leave a rerun mark and exit at once. The running render SHALL read the journal head again before it lets go and SHALL render again while the mirror lacks a sequence. A render's lock SHALL NOT be the lane's write lock, so `lane-start`'s stamp and `lane-handoff`'s commit never queue behind a render.

> Pending Brett's Q27: written under option (a).

#### Scenario: A burst of writes from one lane
- **WHEN** one lane makes a burst of writes
- **THEN** at most two renders run and no fork waits

#### Scenario: The last write of a burst is rendered
- **WHEN** the last write of a burst is made and no later write happens anywhere
- **THEN** it is rendered all the same

### Requirement: The renderer does not inherit `LANES_NO_FETCH=1`
A render forked from a verb run with `LANES_NO_FETCH=1` SHALL render, and SHALL NOT apply the variable to its own fetch.

#### Scenario: A render from a verb with the variable set
- **WHEN** a verb runs with `LANES_NO_FETCH=1` and forks a render
- **THEN** the render fetches and renders

### Requirement: A pending hand edit has a way out on `mode=primary`
Where Amendment 23's S3 would refuse a writer because a hand edit is pending, the refusal on `mode=primary` SHALL name the file and print a recovery that works: restore the file, and make the write through its verb. It SHALL NOT print `lanes-edit.sh commit` as the recovery.

#### Scenario: A pending hand edit
- **WHEN** a hand edit of a rendered file is pending and a writer runs
- **THEN** the refusal names the file and tells the lane to restore it and use the verb

### Requirement: Commits that touch none of the rendered paths are part of the tip
A commit that touches none of the four rendered paths SHALL be part of the tip the renderer renders onto and SHALL NOT stop it. That includes handoff commits, the cutover's commit adding `lanes/STORE`, rollback's commit removing it, and edits to `lanes/repos.tsv`. `lanes/repos.tsv` SHALL stay a git file that a person commits in their lane's checkout.

> Pending Brett's Q18: written under option (A).

#### Scenario: Unrendered commits between renders (agreement test 6)
- **WHEN** a cutover commit, a `repos.tsv` edit and a handoff commit land between two renders
- **THEN** the renderer is not stopped and renders onto the new tip

### Requirement: A commit the renderer did not make to a rendered path stops it
A commit that changes a path the renderer renders and that the renderer did not make SHALL stop the renderer. It SHALL NOT render over it. `lanes-index status` SHALL report `diverged at <sha>`, and a person's word SHALL decide whether that write is imported into SQL or reverted.

#### Scenario: A stray commit (agreement test 6)
- **WHEN** the v1.1.0 `lanes-edit.sh` pushes a commit to a rendered path of a primary fixture
- **THEN** the renderer stops, renders nothing over the commit, and `status` prints `diverged at <sha>`

### Requirement: Each way out of a divergence has a tool
This change SHALL provide a single-write import, which loads the stray commit's line edits into SQL as one journal entry, and a revert the renderer makes, which renders SQL's state over the stray commit. Each SHALL run on a person's word and SHALL write the record that clears `diverged at`.

#### Scenario: The stray write is imported (agreement test 6)
- **WHEN** a person runs the single-write import on the stray commit
- **THEN** its line edits become one journal entry, `diverged at` clears, and the renderer resumes

#### Scenario: The stray write is reverted by the renderer (agreement test 6)
- **WHEN** a person asks the renderer to revert the stray commit
- **THEN** the renderer renders SQL's state over it, `diverged at` clears, and the renderer resumes
- **AND** `reconcile` passes and rollback works afterwards

### Requirement: `status` and `reconcile` prove the mirror
`lanes-index status` SHALL report the mirror's lag and any divergence. `lanes-index reconcile` SHALL be the proof that the mirror equals SQL: it SHALL re-render what is missing and SHALL import nothing silently.

#### Scenario: Reconcile on a diverged mirror
- **WHEN** the mirror is diverged and `reconcile` runs
- **THEN** it imports nothing and `status` still prints `diverged at <sha>`

### Requirement: The post-write nudge does not re-index the mirror into its own source
On `mode=primary` the post-write index nudge SHALL NOT re-index the mirror into the database it came from.

#### Scenario: A write on primary
- **WHEN** a write completes on a `mode=primary` workstation
- **THEN** no re-index of the mirror runs against the register's database

### Requirement: Readers read the database and fail closed
On `mode=primary` every act and every guard SHALL read the database: `who`, `binding`, `live-holder`, `next-free`, `managed-projection`, the guard, `lane`, `lane-start`, `lane-end`, `lane-handoff` and `lane-rename`. Where the act writes, the reads it decides by SHALL hold at its COMMIT: they run inside the transaction that commits the write, or on a snapshot at head `n` whose SERIALIZABLE commit succeeds only while the head is still `n`. A read that fails SHALL refuse with 69, or with 2 in the guard, except where the next requirement says otherwise.

> Pending Brett's Q7: written under option (A″); the wording holds under (A′) and (A) as well.

#### Scenario: The reader table (agreement tests 2 and 11)
- **WHEN** the database is unreachable and `who`, `binding`, `live-holder`, `next-free`, `lane`, `lane-end` or `lane-rename` runs
- **THEN** it exits 69 and prints no answer on stdout

#### Scenario: A reader never answers "no record" for a failed read
- **WHEN** the database is unreachable and `lane-last`, `register-row`, `session-lane`, `canon-lane` or `resolve-home` runs
- **THEN** it exits 69, prints no answer on stdout and never exits 8

#### Scenario: A fake `psql` that fails one call
- **WHEN** a fake `psql` fails exactly one call and a caller's whole read sequence runs
- **THEN** no caller treats the failed call as "no record" (8) or as a success, and each acts as its own requirement says

### Requirement: Callers' own reads of the register go through verbs
`lane-worktrees`' three direct `git grep` reads of `origin/<branch>`, which decide `--yes`'s deletions by a `LANDED` line, SHALL move behind a register verb. The handoff skill's and the restart skill's reads (`sread` and `lread`, both run with `LANES_NO_FETCH=1`) SHALL read under the same rules as the readers above. A deletion decided by a read that failed SHALL NOT go ahead.

#### Scenario: A sweep with the database down
- **WHEN** `lane-worktrees` would decide a deletion by a `LANDED` line and the database is unreachable
- **THEN** the read refuses and nothing is deleted

#### Scenario: A sweep reads through a verb
- **WHEN** `lane-worktrees` decides a deletion by a `LANDED` line on a `mode=primary` workstation
- **THEN** it reads the line through a register verb and not by `git grep` of `origin/<branch>`

### Requirement: Reads that decide nothing may read the mirror
Only reads that decide nothing SHALL read the mirror: the `SessionStart` block and the launcher's bare window reads (offline, always exit 0), and the listings `lanes` and `history` when the database is unreachable. The one other mirror read is the naming read of `lane-handoff` and the handoff skill, which the requirement for them sets out. A mirror read SHALL print on stderr `read: mirror at <sha12>, <k> sequences behind or unknown (sql unreachable: <why>)`, SHALL keep its exit, and SHALL NOT change stdout. Where a caller acts on a listing's answer, as `lane` and `lane-end --retire-dormant` act on `lanes`' answer, that read decides and SHALL refuse with 69.

#### Scenario: A listing with the database down
- **WHEN** the database is unreachable and `lanes` or `history` runs
- **THEN** the answer is the mirror's and stderr carries the `read: mirror` line
- **AND** the listing's exit is its own

#### Scenario: A listing a caller acts on
- **WHEN** `lane-end --retire-dormant` needs `lanes`' answer and the database is unreachable
- **THEN** the read refuses with 69

#### Scenario: A session opens with the database down
- **WHEN** the `SessionStart` block runs with the database unreachable
- **THEN** it answers from the mirror and exits 0

### Requirement: A reader says which store answered
A reader SHALL say which store answered whenever it is not that workstation's default. On `mode=primary`, `--index` SHALL change nothing and print no `read: index` line, because the default read is already the database.

#### Scenario: `--index` on primary
- **WHEN** a reader runs with `--index` on a `mode=primary` workstation
- **THEN** it answers from the database and prints no `read: index` line

### Requirement: `managed-projection` answers from the database and never says `legacy` for an outage
`managed-projection` SHALL answer 0, 8 or 1 from the database. An unreachable database SHALL answer 1, UNKNOWN, and never `legacy` or 8. The database-backed answer SHALL wrap `lane_is_managed_owned` from outside the verbatim block ported from `3c26041`, which SHALL stay byte-identical.

#### Scenario: Valid, malformed and legacy rows
- **WHEN** `managed-projection` reads a valid, a malformed and a legacy row from SQL
- **THEN** it answers 0, 1 and 8 respectively

#### Scenario: Database unreachable
- **WHEN** the database is unreachable
- **THEN** it answers 1, UNKNOWN, and never 8

#### Scenario: The verbatim block
- **WHEN** the verbatim block is diffed against `3c26041`
- **THEN** the diff is empty

### Requirement: `lane-start` with the database unreachable
On `mode=primary`, `lane-start` SHALL refuse with 69 before anything is renamed or written at the reads that decide state: `managed-projection` (`lane-start:1555`), the row count (`:1609`), `lane-last` (`:2335`) and `canon-lane` (`:1154`). It SHALL refuse with 69 where the write must exist: `request-handoff` (`:2497`) and `add-row` for a new lane (`:3819`). It SHALL print a note and go on where `append-session-id` (`:3848`, `:3869`, `:3906`, `:4019`), `set-row-state LIVE` (`:3985`) and `log STARTED` or `RESUMED` (`:4178`) are refused. The Rule 3 handoff stamp and the sidecars SHALL work as they do today, and the stamp SHALL stay a git commit that never blocks the launch. Line numbers are those of the code pin `2a057a6d`.

#### Scenario: A deciding read fails (agreement test 11)
- **WHEN** the cluster is stopped and `lane-start` reaches any of the reads above
- **THEN** it refuses with 69 before any rename or write

#### Scenario: A write that must exist
- **WHEN** the cluster is stopped and `request-handoff` or `add-row` for a new lane is refused
- **THEN** `lane-start` refuses with 69

#### Scenario: A write that only prints a note
- **WHEN** `append-session-id`, `set-row-state LIVE` or `log STARTED` is refused with the database down
- **THEN** `lane-start` prints a note and the launch goes on

#### Scenario: `canon-lane` blips for one call
- **WHEN** a fake `psql` fails only the `canon-lane` call at `lane-start:1154`
- **THEN** the launch refuses and no second row is made for a lane already running

### Requirement: The busy code refuses the launch at `lane-start`'s five default arms
At the five arms of `lane-start` that note an unknown code and carry on (`:1154` `canon-lane`, `:1262` `swapped`, `:1271` `lane-dir`, `:3079` `lane-transcript` for the row's session cell, `:3430` `lane-transcript` for the last `PAUSED` record's resume id), the busy code SHALL refuse the launch, naming the code and saying that nothing was written and a retry in seconds is safe. Every caller's default arm SHALL refuse on the busy code. The manual SHALL list it.

#### Scenario: One test per arm
- **WHEN** the verb at any one of the five arms exits the busy code
- **THEN** the launch refuses and the register's rows and log are unchanged

### Requirement: `lane-handoff` and the handoff skill with the database unreachable
On `mode=primary`, with the database unreachable, the reads that name the lane before the handoff file is published SHALL read the mirror, labelled with the `read: mirror` line, so that the handoff file is still written (`R-A11-11`). The `PAUSED` record's three writes SHALL exit 69 and then print a note, and the lane SHALL stay `SWAPPING`, an interrupted swap that the existing late `PAUSED` path completes. The handoff commit and the sidecars SHALL work as they do today. The note SHALL name how the `SWAPPING` lane's `PAUSED` record is completed later: the late `PAUSED` path, who runs it, and when.

#### Scenario: A swap with the database down (agreement test 11)
- **WHEN** the cluster is stopped and a lane runs `lane-handoff`
- **THEN** the handoff file is written, the `PAUSED` writes exit 69 with a note, and the lane stays `SWAPPING`

#### Scenario: The note names the completion
- **WHEN** the note is printed
- **THEN** it names the late `PAUSED` path, who runs it and when

### Requirement: The managed seam stays the ledger's
An enrolled lane's truth SHALL stay its local JSON ledger under flock. Its projection into the register row SHALL be written by the ledger's own projection writer through the same storage layer as every register write, so that on `mode=primary` it lands in SQL first. This change SHALL provide the storage-layer entry the projection writer will call, and SHALL test that entry with a fake.

#### Scenario: The ledger stays the truth
- **WHEN** a managed lane's projection is written on `mode=primary`
- **THEN** the local ledger is unchanged and the register row in SQL carries the projection

### Requirement: `lanes-index import --at` proves the render before it loads
`lanes-index import --at <commit>` SHALL read the workspace repository at the named commit, SHALL load the register's tables and the journal's first sequence, SHALL render the mirror from SQL into a scratch tree, and SHALL refuse unless `git diff --quiet` holds between the commit's `lanes/` and the render. It SHALL be repeatable and SHALL be able to run into a scratch schema as a rehearsal.

#### Scenario: A byte-identical import (agreement test 7)
- **WHEN** the import's render equals the commit's `lanes/`
- **THEN** the import succeeds

#### Scenario: A difference refuses (agreement test 7)
- **WHEN** the render differs from the commit's `lanes/` by one byte
- **THEN** the import refuses and loads nothing the estate reads

### Requirement: The import takes origin only and keeps the unreadable lines verbatim
The import SHALL take origin's records only; lanes publish or archive their own unpublished records before the window. The 15 `WORD` lines whose UTC carries milliseconds SHALL be imported verbatim and flagged, and the import SHALL keep warning about them.

> Pending Brett's Q4(c): written under option (i).
> Pending Brett's Q4(d): written under option (i).

#### Scenario: A local-only line
- **WHEN** a lane holds a record that never reached origin
- **THEN** the import does not take it

#### Scenario: The unreadable lines
- **WHEN** the import reads the 15 `WORD` lines of `lanes/log/codeXfactory-2.md`
- **THEN** they are imported byte-for-byte, flagged, and the warning stays

### Requirement: The cutover is one guarded sequence
The cutover SHALL set the database switch to `primary`, SHALL push one commit adding `lanes/STORE` as a fast-forward of the imported commit, and SHALL then flip `mode=primary` on the workstations act 3 configured. It SHALL abort if `main` moved under `lanes/` since the import, in which case the import is re-run at the new tip, and the abort path SHALL set the switch back. `lanes/STORE` SHALL carry `Register-Seq` 1, and the first write after the cutover SHALL be mirrored as `Register-Seq: 2`.

> Pending Brett's Q18: written under option (A).

#### Scenario: A successful cutover
- **WHEN** the cutover runs after a byte-identical import
- **THEN** the switch is `primary`, one fast-forward commit adds `lanes/STORE` with no credential, and the next write goes to SQL

#### Scenario: The smoke test
- **WHEN** one `NOTED` write is made after the cutover
- **THEN** it has a SQL row, a mirror commit with `Register-Seq: 2`, reads back, and `status` shows a lag of 0

#### Scenario: `main` moved under `lanes/`
- **WHEN** `main` has moved under `lanes/` since the import
- **THEN** the cutover aborts, pushes no marker, and leaves the switch not `primary`

### Requirement: Rollback returns the estate to the files
`lanes-index rollback` SHALL switch to `files`, so that every SQL write refuses from that transaction on; SHALL render until `status` shows the mirror current and byte-identical to a full render; SHALL push one commit removing `lanes/STORE`; and SHALL clear `mode=primary` on every workstation. It SHALL run from a shell or from a `CLAUDE_NO_LANE=1` session, with no lane session and no guard. A mirror that a divergence has stopped SHALL NOT count as current.

#### Scenario: A stopped renderer
- **WHEN** the mirror is diverged and a rollback runs
- **THEN** it does not push the commit that removes `lanes/STORE` until the divergence is cleared

#### Scenario: A rollback (agreement test 8)
- **WHEN** a rollback runs to its end
- **THEN** the files are byte-identical to a full render, the marker is gone and SQL writes are refused

#### Scenario: A files-mode write after the rollback (agreement test 13)
- **WHEN** a files-mode writer writes after the rollback has run to its end
- **THEN** it succeeds, while a workstation whose `mode=primary` was not cleared refuses

### Requirement: A rollback needs the database
A rollback SHALL set the switch to `files` in the database first. With the database unreachable, a rollback SHALL wait: it SHALL refuse and change neither the switch nor the marker. A database lost for good SHALL be a separate disaster procedure (restore, or re-import), run with every workstation's lanes paused first, and not a rollback. No `mode=primary` writer SHALL commit once a rollback has removed `lanes/STORE`.

> Pending Brett's Q4(b): written under option (iii).

#### Scenario: Rollback with the database stopped (agreement test 8)
- **WHEN** the database is stopped and a rollback is tried
- **THEN** it is refused and neither the switch nor the marker changed

#### Scenario: The partition (agreement test 8)
- **WHEN** the database stays up and only the rolling-back clone cannot reach it
- **THEN** the rollback is refused and removes no marker

### Requirement: A writer the rollback missed cannot write
Once a rollback has run to its end and the database is back, a `mode=primary` writer that was not cleared SHALL exit 69 and write nothing, and the journal's count SHALL be unchanged. That SHALL hold after a fetch and with none since the rollback's push, with `LANES_NO_FETCH=1` set, and through each of `set-row-state`, `replace-in-row`, `append-session-id`, `append-line` and `commit` (the five that make no fetch before they edit) and through one verb that does. The test SHALL fail on the leak as it stands today, a primary writer that refuses only on its last-fetched tip or on a switch reset when the database returns.

> Pending Brett's Q4(b): written under option (iii).

#### Scenario: A missed writer after a fetch (agreement test 8)
- **WHEN** a second clone the rollback missed fetches and then runs any of the five verbs
- **THEN** it exits 69 and the journal's count is unchanged

#### Scenario: A missed writer with no fetch (agreement test 8)
- **WHEN** the second clone has not fetched since the rollback's push and runs any of the five verbs
- **THEN** it exits 69 and the journal's count is unchanged

#### Scenario: `LANES_NO_FETCH=1` (agreement test 8)
- **WHEN** the missed writer runs with `LANES_NO_FETCH=1` set
- **THEN** it exits 69 and writes nothing

#### Scenario: The test must catch the leak
- **WHEN** the test runs against a writer that refuses only on its last-fetched tip
- **THEN** the test fails

### Requirement: The operator's bootstrap is idempotent and holds no secret
This change SHALL build a `lanes-index` bootstrap that makes the register's schema, the three roles and their GRANTs from one admin connection that Brett supplies, prints the password-file lines for him to place, and stores no secret. The admin connection SHALL be used once and held by nothing. The command names are the design's.

#### Scenario: Run twice
- **WHEN** the bootstrap runs twice against the same database
- **THEN** one schema and the same GRANTs exist

#### Scenario: No secret is stored
- **WHEN** the bootstrap completes
- **THEN** it has printed the password-file lines and written no secret to any file

### Requirement: The readiness check proves an environment before the cutover
This change SHALL build a readiness check, run in each environment that writes the register (the bench containers, the host shells, Raven's install and the Codex lane's tools), that verifies the installed tools' digests against the release, that `psql` is present, that the configuration is readable and says `mode=index` before the cutover, that each role connects and is refused outside its scope, and that the installed `lanes-index` no longer carries the refusal of `mode=primary`. A pass in every environment SHALL be a precondition of act 4. The check is a check at one moment: a container started after it SHALL NOT be covered, and the check SHALL say so.

#### Scenario: A stale digest
- **WHEN** the installed tools' digests differ from the release
- **THEN** the check fails

#### Scenario: A missing `psql` or an early `mode=primary`
- **WHEN** `psql` is missing, or the configuration says `mode=primary` before the cutover
- **THEN** the check fails

#### Scenario: A role outside its scope
- **WHEN** a role holds a GRANT outside its scope
- **THEN** the check fails

#### Scenario: An old `lanes-index`
- **WHEN** the installed `lanes-index` still refuses `mode=primary`
- **THEN** the check fails

### Requirement: The `mode=primary` flip is a command run once per host
This change SHALL build the `mode=primary` flip as a command, run once per host where one configuration serves the host. It SHALL write only the `mode=` line and SHALL keep the file's 0600 mode. A read-only mount SHALL be flipped from the host, not from a bench. Under Q20 the flip is the cutover's last step; act 3 leaves `mode=index`.

> Pending Brett's Q23: written under option (a).

#### Scenario: The flip
- **WHEN** the flip runs against a configuration
- **THEN** only the `mode=` line changes and the file stays mode 0600

#### Scenario: A read-only mount
- **WHEN** a bench's configuration mount is read-only
- **THEN** the flip is run from the host and not from the bench

### Requirement: Claim order is commit order
Rule 1's race SHALL be decided by the register's commit order: of two claimants one SHALL commit, and the other SHALL see it and exit 2, writing nothing. The pre-check of the object's holders SHALL hold at the claim's COMMIT, in one of two forms: it runs inside the transaction that inserts the `CLAIMED`, SERIALIZABLE or under a lock taken at READ COMMITTED on the object or on the head row; or it runs on a snapshot at head `n`, and the SERIALIZABLE transaction that inserts the `CLAIMED` commits only while the head is still `n`. In either form that transaction SHALL also refuse, with 2, a claim whose spellings (the spelling as typed, its canonical key and the aliases its writer's `repos.tsv` maps to that key) meet those of another lane's live claim, so that no claim rests on one writer's alias table alone. `CLAIM-LOST` SHALL stay in the grammar for the history that has it and SHALL no longer be produced.

> Pending Brett's Q12: written under option (a).
> Pending Brett's Q7: written under option (A″); the wording holds under (A′) and (A) as well.

#### Scenario: Two concurrent claimants (agreement test 4)
- **WHEN** two lanes claim one object at once
- **THEN** one `CLAIMED` is committed, the other writer exits 2 and writes nothing, and no `CLAIM-LOST` is produced

#### Scenario: Claims spelled two ways
- **WHEN** two claims of one object, spelled two ways, one spelling an alias added to `repos.tsv` between their reads, run from clones at different tips
- **THEN** one `CLAIMED` exists and the other claim exits 2

### Requirement: `HOLD` and `LANDING` are ordered by commit sequence
Amendment 10's `HOLD` and `LANDING` lines on one repository's `main` SHALL be ordered by commit sequence: the earlier commit wins, and the later writer's transaction SHALL report the earlier line to it before it acts. The later writer's verb SHALL exit non-zero, with a code the design names that is neither 0 nor 69, and SHALL print the earlier line, so that a landing script that sees the exit does not merge second. The line SHALL still come before the act that moves `main`. Recognising which lines take and release `main` SHALL be done inside the transaction, by `append-line` or by a verb of the design's choosing.

#### Scenario: Two `HOLD`s (agreement test 4)
- **WHEN** two lanes write a `HOLD` for one repository
- **THEN** they land in commit order, the later writer exits the named code, and it prints the earlier line

#### Scenario: A script that checks the exit
- **WHEN** a landing script runs after a later `HOLD` writer exited non-zero
- **THEN** the script does not proceed to the merge

### Requirement: Which lines take and release `main`
`HOLD` and `LANDING` SHALL take `main`. `HOLD RELEASED`, `HOLD WITHDRAWN` and `HOLD EXPIRED` SHALL release a `HOLD`. The `(refresh)` and `(run id)` lines SHALL continue the original. Only `LANDED` SHALL release a `LANDING`.

> Pending Brett's Q17: written under option (B).

#### Scenario: A withdrawn `HOLD`
- **WHEN** a `HOLD WITHDRAWN` line follows a `HOLD`
- **THEN** a later writer sees `main` as free and is not told of the withdrawn `HOLD`

#### Scenario: A refresh
- **WHEN** a `HOLD (refresh)` line follows a `HOLD`
- **THEN** it continues the original hold and does not take `main` a second time

#### Scenario: A `LANDING` stays until `LANDED`
- **WHEN** a `LANDING` is open and no `LANDED` has followed
- **THEN** `main` stays taken

### Requirement: The three roles
A REGISTER-WRITER role, scoped to the register's tables, the journal and the switch, SHALL be held by `lanes-edit.sh` and its renderer on every writing workstation through its own credential pair. The indexer role SHALL keep the ledger half's derived tables and SHALL NOT write the register's; the register-writer SHALL NOT write the ledger half's. A READER role SHALL serve the hooks and the listings.

#### Scenario: Each role outside its scope (real engine)
- **WHEN** each role attempts a write outside its scope
- **THEN** the database refuses it

### Requirement: The REGISTER-WRITER secret's reach is stated plainly
The secret SHALL stay out of every environment a session inherits (Amendment 14(c)). The manual SHALL say that the REGISTER-WRITER secret, in a 0600 file, is readable by every lane process on its host, that Rule 9(e) forbids a hand-run SQL write although nothing technical prevents one, and that the exposure matches today's git push rights, and SHALL claim no protection the design lacks.

#### Scenario: No credential in an inherited environment
- **WHEN** a session's inherited environment is inspected on a `mode=primary` workstation
- **THEN** it carries no database URL and no password

### Requirement: Agreement tests run on a real engine in CI
The agreement tests that only a real engine proves SHALL run against a disposable local PostgreSQL 16 cluster, one per suite run, listening on a Unix socket only, with a database per test. At least tests 1, 4, 7, 8 and 9, the roles' test and the failure partway through a multi-file write SHALL be among them. In CI they SHALL be required, so that CI refuses rather than skips them, and the switch that makes CI refuse SHALL apply only to the `ubuntu-24.04` jobs that have the PostgreSQL binaries. Elsewhere they SHALL skip loudly, with a reason naming `PG_BIN` and `postgresql-16`. The fake-`psql` tests (argv, connection strings and credentials, the 69 refusal, the SQL's shape) SHALL always run. The bench images SHALL gain PostgreSQL 16.

#### Scenario: A CI job without the real engine
- **WHEN** a CI job that has the PostgreSQL binaries cannot start the cluster
- **THEN** the job fails and does not skip

#### Scenario: A host without Postgres
- **WHEN** the suite runs where `PG_BIN` finds no PostgreSQL 16
- **THEN** the real-engine tests skip with a reason naming `PG_BIN` and `postgresql-16`
- **AND** the fake-`psql` tests still run

#### Scenario: A leg without a server
- **WHEN** a leg without the binaries, such as `macos-latest`, runs the suite
- **THEN** it does not refuse for want of Postgres

### Requirement: The cost of a write is measured and recorded
The PR body SHALL record the measured cost of one write: a median and a p95 over warm runs, with the cold first call beside them, on a named host, store and path. It SHALL also record what a lane feels: each verb timed end to end, from exec to exit, warm and cold, for `log NOTED`, `claim`, a landing's five writes and `lane-start --dry-run`, beside the same verbs in files mode on the same host.

#### Scenario: The figures are recorded (agreement test 9)
- **WHEN** the PR is opened
- **THEN** its body names the host, the store, the path and the statistic for each figure, and shows the files-mode figures beside them

### Requirement: A lane's day on `mode=primary` is tested end to end
On a local cluster with a bare-repo origin, a lane's day SHALL be tested: `lane-start` for a new lane, then a resume; `claim`; a `HOLD`, `LANDING` and `LANDED` set; `lane-handoff`; and `lane-end`. After each act the mirror SHALL be byte-identical, with one commit per sequence, and no handoff commit SHALL have rewritten a render commit. With the cluster stopped mid-day, the outcomes SHALL be those the requirements for `lane-start`, `lane-handoff` and the readers set.

#### Scenario: The day (agreement test 11)
- **WHEN** the day's acts run in order
- **THEN** after each act the mirror equals a full render and holds one commit per sequence

### Requirement: The drill covers the outage and the way back
The drill (agreement test 13) SHALL run import, cutover, writes, an outage of the database with its refusals, rollback, and then a files-mode write that succeeds while a workstation not yet cleared refuses. The runbook SHALL also rehearse the real register into a scratch schema, an outage included, before act 4.

#### Scenario: The drill
- **WHEN** the drill runs
- **THEN** every step ends as stated and the not-yet-cleared workstation exits 69

### Requirement: The manual says what changes for a lane
`docs/README-lanes.md` SHALL gain a section, "The SQL register", beside "The derived index (Amendment 14)". It SHALL cover `mode=primary`, the estate switch and `lanes/STORE`, exit 69 and the busy code, the journal and the renderer, which reads may use the mirror, import and rollback, the three roles with their GRANTs, and the socket API; the sections that describe writes and reads SHALL say where `mode=primary` changes them. The section SHALL open with "What changes for a lane": exit 69 and what to do; the busy code, which says that nothing was written and a retry in seconds is safe, so that it is not an outage; the guard during an outage and its two ways out; that `commit` and hand edits are refused; the files as a mirror that lags and the verbs that give the truth (`who`, `history`, `lanes`, `lanes-index status`); the claim comment's sequence and how to find its commit; the claim loser's 2 instead of 7; and the later `HOLD` or `LANDING` writer's code.

#### Scenario: The section opens with what changes
- **WHEN** a lane opens "The SQL register"
- **THEN** its first part is "What changes for a lane" and covers each item above

### Requirement: The runbook for acts 3 and 4 and the rollback
The manual SHALL carry a runbook for acts 3 and 4 and the rollback: every act, word, command and owner, in order. It SHALL include the readiness check in each environment, R3b's smoke test, the rollback's triggers, the estate-wide freeze (an estate-wide hold announced on each project's coordination issue and by Brett's prompt to each pane, drained to `PAUSED`, then quiet for 2 minutes, with a cutover and a rollback rehearsed first on a scratch schema), and the posting of "What changes for a lane" on each project's coordination issue before the window. It SHALL propose one bench image rebuild that carries both the re-vendor and the configuration mount, SHALL list the environments that write (the Codex lane's included) and ask Brett to confirm the list, and SHALL make act 4 wait for Amendment 23's slices 2a, 2b and 3.

> Pending Brett's Q4(a): written under option (i).
> Pending Brett's Q22: written under option (a).
> Pending Brett's Q23: written under option (a).
> Pending Brett's Q24: written under option (a).
> Pending Brett's Q28: written under option (i).

#### Scenario: Act 4's preconditions
- **WHEN** a runbook step opens act 4
- **THEN** the readiness check has passed in every listed environment, the freeze is in place, and slices 2a, 2b and 3 have landed

#### Scenario: The rollback's triggers
- **WHEN** the runbook is read for the rollback
- **THEN** it names the triggers, the words and the owner of each step
