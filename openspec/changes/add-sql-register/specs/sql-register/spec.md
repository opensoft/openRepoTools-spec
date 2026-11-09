# Spec Delta: the SQL register

## Purpose

On a workstation configured `mode=primary` the lane register is a database: each write is one transaction, the git files under `lanes/` are its rendered mirror, and nothing falls back to writing the files. This capability fixes the configuration and the estate switch, the write path and its exit codes, the journal and the renderer, readers that fail closed, import and rollback, claim order, the roles, the operator tools, and the evidence that proves them.

## ADDED Requirements

### Requirement: `mode=` selects the register's store
`lanes-index.conf` SHALL accept a `mode=` key whose value is `index` (the default when the key is absent, and today's behaviour) or `primary`, and SHALL accept the credential pair of the writer login of that workstation's engineer, a member of REGISTER-WRITER (for example `register_url=` and `register_passfile=`), and `register_label=`, the label of the database that `register_url=` reaches, beside the existing keys. `lanes-index` SHALL stay the one parser of the file: `lanes-edit.sh` SHALL ask `lanes-index`, or the register service, for the mode and SHALL NOT parse the file itself. Where the mode query fails for any reason other than the refusal of a build without the renderer's git half (a configuration it refuses, a crash, an exit 127 or a timeout), `lanes-edit.sh` SHALL run in files mode with a warning on stderr; files mode's mandatory marker check after Amendment 23's S3 fetch then refuses on a SQL estate, so no failure of the query writes the files there. The indexer's `url=` credential SHALL NOT write the register's tables. A configuration that says `mode=primary` with the SQLite store SHALL be refused, because the register is shared by workstations and SQLite is one disk. A key `lanes-index` does not know SHALL still be refused with exit 2. The file's 0600 mode, its password file, its refusals and the rule that it never lives under `~/.agents/` SHALL stand as Amendment 14(c) wrote them. A `lanes-index` older than this change refuses `mode=` with exit 2, so this change SHALL be installed before `mode=` is written anywhere.

#### Scenario: Mode absent
- **WHEN** a workstation's configuration has no `mode=` key
- **THEN** it is `mode=index` and its writers and readers behave as they did before this change

#### Scenario: Primary with SQLite
- **WHEN** the configuration says `mode=primary` and names the SQLite store
- **THEN** `lanes-index` refuses the configuration and no register write is attempted

#### Scenario: Unknown key
- **WHEN** the configuration carries a key `lanes-index` does not know
- **THEN** `lanes-index` refuses it with exit 2, as it does today

#### Scenario: The mode query fails
- **WHEN** `lanes-index mode` fails with a configuration it refuses, a crash, an exit 127 or a timeout of its 2 s client timer
- **THEN** the write runs in files mode and stderr carries a warning naming the failure
- **AND** where the fetched tree carries `lanes/STORE`, the files-mode marker check refuses the write with 69 and nothing is written

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

> Brett's answer to Q4(b): option (iii), #165 6710653292.

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
While `lanes/STORE` is absent, the output and exit codes of every `mode=index` and every unconfigured workstation SHALL be unchanged; each writer gains the configuration read and the marker check and nothing else. There SHALL be three exceptions. The first SHALL be the prompt guard's answer 2, which in every mode runs its bind detached and answers from the bind's record, as the register service's requirement on the guard's 5 s budget sets out, because today's answer 2 is cut down by the hook's timeout partway through its bind. The second SHALL be `lane-start`'s bind, whose three register writes become one write verb, `start-record`, in every mode, as the requirement "`lane-start`'s bind is one write" sets out, so that in files mode they are one register commit and one push. The third SHALL be `openRepoTools wip init`'s refusal wherever any file under `lanes/` exists, in every mode, as the requirement on `wip init` sets out. The ledger half, the handoffs, Rules 1 to 10 as rules and Amendment 7's grammar SHALL be unchanged in every mode.

#### Scenario: An unconfigured workstation writes as today
- **WHEN** a workstation with no `lanes-index.conf` makes a register write and `lanes/STORE` is absent
- **THEN** the write, its output and its exit code are those of the v1.1.0 tools

### Requirement: Every row records the mode that wrote it last
Each register row SHALL carry the mode that wrote it last, so that a mixed history stays visible.

#### Scenario: Rows read back primary
- **WHEN** any `mode=primary` write completes
- **THEN** every row it touched reads back `primary` as the mode that wrote it last

### Requirement: Readers on a workstation that is not `mode=primary` once `lanes/STORE` exists
Once `lanes/STORE` exists, the acts and the prompt guard on a workstation that is not `mode=primary` SHALL refuse, while the listings and the `SessionStart` block SHALL read the mirror and say so with the `read: mirror` line. A reader or guard there SHALL look for `lanes/STORE` on the ref it reads (`git cat-file -e <ref>:lanes/STORE`), refusing with 69, or 2 in the guard, where it is present; the check is as current as that ref, and the refusal says how old the ref is.

> Brett's answer to Q21(a): option (A), #165 6710653292.

#### Scenario: An unconfigured workstation after the cutover
- **WHEN** an unconfigured workstation runs an act or receives a prompt after `lanes/STORE` is on origin
- **THEN** the act refuses and the guard refuses with 2

#### Scenario: An unconfigured workstation lists
- **WHEN** the same workstation runs `lanes` or opens a session
- **THEN** the answer comes from the mirror and stderr carries the `read: mirror` line

### Requirement: A build without the renderer's git half refuses `mode=primary`
Until the renderer's git half is installed, `lanes-index` SHALL refuse `mode=primary` with the text `mode=primary is not available in this build: the renderer's git half is not installed, so a write could not be mirrored; nothing was written`. Its commands SHALL refuse it with exit 2, the configuration parser's code, except the mode query, `lanes-index mode`, and the read, `register read` in each of its forms, which SHALL report it with an exit code of its own, 78 (`EX_CONFIG`), never 2, so that `lanes-edit.sh`, its readers and the prompt guard can tell it from every other failure. `lanes-edit.sh`, run on a workstation whose configuration says `mode=primary`, SHALL then exit 69, repeat that text and change neither the register nor the files, and SHALL NOT write the files instead. On such a workstation the readers SHALL answer the 78 as a failed read: a read that decides SHALL refuse with 69, the listings and the `SessionStart` block SHALL read the mirror with a note, the prompt guard SHALL refuse with 2, and `managed-projection` SHALL answer 1. The design MAY reword the text and SHALL NOT weaken the refusal. The readiness check SHALL fail an environment whose installed `lanes-index` still carries this refusal.

> Brett's answer to Q6: option (c), round 1's 1A, #165 6710653292.

#### Scenario: The refusal in place (agreement test 14)
- **WHEN** the refusal's constant is set and `lanes-edit.sh` runs a write on a workstation configured `mode=primary`, using the fake `psql`
- **THEN** `lanes-index mode` and `register read` exit 78 with the text above, any other `lanes-index` command exits 2 with it, and `lanes-edit.sh` exits 69 repeating it
- **AND** neither the register nor the files changed

#### Scenario: Readers under the refusal
- **WHEN** the refusal's constant is set and, on a workstation configured `mode=primary`, a read that decides, a listing, the `SessionStart` block, the prompt guard and `managed-projection` run
- **THEN** `register read` exits 78, the read that decides exits 69, the listing and the `SessionStart` block answer from the mirror with a note, the guard exits 2, and `managed-projection` answers 1

#### Scenario: The refusal lifted
- **WHEN** the same constant is flipped and the same write runs
- **THEN** the write goes to SQL

#### Scenario: The readiness check sees the refusal
- **WHEN** the readiness check runs against a build with the refusal in place
- **THEN** the check fails that state

### Requirement: A server-side gate protects the rendered paths and `lanes/STORE`
Where the organisation's plan supports it, a push ruleset whose only bypass is a renderer identity SHALL be in place before the cutover, on the paths the renderer renders (`lanes/LANES.md`, `lanes/log/<lane>.md`, `lanes/aliases.tsv` and `lanes/archive/`) and on `lanes/STORE`. Every push that changes those paths SHALL use that identity: the renderer's, and the cutover's and the rollback's commits of `lanes/STORE`. Each such push SHALL name the repository it fetched by an explicit SSH URL and SHALL run in an environment of its own, so that no inherited git configuration, URL rewrite, hook or agent key makes it push another way or as another identity, and SHALL write nothing to the checkout's configuration. The ruleset SHALL NOT cover `lanes/repos.tsv`, `lanes/log/README.md` or any other path under `lanes/` outside those, so that they stay git files a person commits and a commit to them stays part of the tip the renderer renders onto. The identity's key SHALL NOT be in any environment a session inherits (Amendment 14(c)). Because the ruleset refuses every files-mode register write, it SHALL be switched on only inside act 4's freeze, after the lanes are drained and the last `PAUSED` write is on origin and before the import, and the rollback SHALL remove it after its render proves the mirror current and before the commit that removes `lanes/STORE` is pushed. Switching the ruleset on and off is Brett Heap's act. Where the plan does not support such a ruleset, or one that names exactly those paths, whether act 4 waits for an equal server-side gate is Brett Heap's decision.

> Brett's answer to Q21(b): option (B), #165 6710653292; scope pending Brett's word (`lanes/**` as the round-2 file words his answer, or the render set and `lanes/STORE` as this requirement has it).
> Brett's answer to Q18: option (A), #165 6710653292.

#### Scenario: A stale writer's push is refused
- **WHEN** any identity other than the renderer's pushes a commit that changes a rendered path or `lanes/STORE`
- **THEN** the ruleset rejects the push

#### Scenario: The ruleset's window
- **WHEN** the ruleset is switched on and off as the runbook orders it
- **THEN** no files-mode register write is refused by it before the freeze, and a files-mode write after the rollback succeeds

#### Scenario: A person's `repos.tsv` edit is not gated
- **WHEN** a person pushes a commit that changes only `lanes/repos.tsv`
- **THEN** the ruleset does not reject it, and the renderer renders onto it

#### Scenario: The renderer pushes under its identity
- **WHEN** the renderer pushes a render commit
- **THEN** the push is accepted under the renderer identity and the identity's key is in no session-inherited environment

#### Scenario: A host that rewrites URLs
- **WHEN** the checkout's `origin` is an HTTPS remote, and the host's global git configuration rewrites SSH URLs to HTTPS or an agent offers another key
- **THEN** the renderer's push still goes over SSH to the repository it fetched, with the renderer identity's key alone, and the checkout's configuration is unchanged

### Requirement: `wip init` and the `LANES.md` link on a SQL estate
`openRepoTools wip init` SHALL refuse once `lanes/STORE`, or any file under `lanes/`, exists, so that its seed commit is never a stray commit under `lanes/STORE`. The `~/projects/xFactory/LANES.md` link SHALL stay for reading only, as Amendment 23's S5 already rules.

> Brett's answer to Q14: option (a), #165 6710653292.

#### Scenario: `wip init` on a SQL estate
- **WHEN** `openRepoTools wip init` runs in a workspace repository whose tree carries `lanes/STORE`
- **THEN** it refuses and commits nothing

#### Scenario: `wip init` where a `lanes/` file exists
- **WHEN** it runs where any file under `lanes/` exists
- **THEN** it refuses and commits nothing

### Requirement: Every register write is one transaction
On a `mode=primary` workstation each of `claim`, `release`, `log` (every verb), `set-row-state`, `append-line`, `add-row`, `rename-lane`, `retire-rows`, `archive-rows`, `migrate-state-cells`, `append-session-id`, `replace-in-row`, `request-handoff`, `start-record` (`lane-start`'s bind as one write) and the managed projection writer SHALL run as one database transaction through one storage layer. Each `lanes-edit.sh` write SHALL open its own transaction with its own REGISTER-WRITER credential, and SHALL go on when the register service is down. A write that fails partway SHALL leave the register's rows, the journal, the line store and the head row unchanged and SHALL leave no lock behind.

> Brett's answer to Q2(a) and (b): round 1's 4A, #165 6710653292.

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
When the database is unreachable, or refuses the transaction for a reason other than the register's own rules, a register write SHALL exit 69, naming the store and the error, with nothing written anywhere and never the files instead. A refusal by the register's own rules (an object or `main` held, a write id that names another write or another generation, an expectation changed, a key that is not a scope) SHALL exit 2, writing nothing, as the requirements that set those rules say. The prompt guard, which may exit only 0 or 2, SHALL refuse with 2. Exit 69 SHALL join the exit table of `lanes-edit.sh`.

#### Scenario: The database is unreachable (agreement test 2)
- **WHEN** the database is unreachable and any listed write runs, the projection writer's storage-layer entry included
- **THEN** it exits 69 naming the store and the error
- **AND** the register's rows, the journal and git are unchanged and no lock is left

#### Scenario: The database refuses the transaction
- **WHEN** the database refuses the transaction for a reason other than a serialization failure, a lost turn or the register's own rules
- **THEN** the write exits 69 and nothing is written

#### Scenario: A refusal by the register's own rules
- **WHEN** the transaction is refused by one of the register's own rules, such as an object another lane's live claim holds
- **THEN** the write exits 2, not 69, and nothing is written

### Requirement: A serialization failure is retried, not refused
SQLSTATE 40001 and 40P01 SHALL NOT be reported as 69. A write that meets one, or whose snapshot is stale because the head moved, SHALL re-read and re-check against the committed state and try again until a deadline, so that the later of two writers sees the earlier one's committed state and no writer in a burst is lost to a fixed small count of attempts. A retry SHALL key on the SQLSTATE and not on `psql`'s exit code, and the sequence number SHALL never come from a database SEQUENCE.

> Brett's answer to Q7: option (A″), #165 6710653292.

#### Scenario: The loser re-runs
- **WHEN** two writers collide and the database reports 40001 to the later one
- **THEN** that writer re-runs from its reads and does not exit 69

#### Scenario: A burst loses no writer (agreement test 4)
- **WHEN** ten writers on distinct objects are released together, each held 200 ms between its snapshot and its COMMIT
- **THEN** all ten commit, none exits busy or 69, and their sequence numbers run with no gap from the seq after the cutover's entry, 2 to 11 after a first cutover

### Requirement: A lost race is not an error on the sequence key
The transaction SHALL update the head row before it inserts the journal row, or SHALL retry a unique-key violation on the sequence key exactly as it retries a serialization failure. A writer that loses a race SHALL NOT exit on that violation.

#### Scenario: Racing writers on the sequence key
- **WHEN** ten writers race for the next sequence number
- **THEN** none of them exits because of a unique-key violation on the sequence key

### Requirement: A write that cannot get its turn reports "register busy" with a code of its own
A write that cannot get its turn in time SHALL exit "register busy" with an exit code of its own: not 0, not 69 and not a code the exit table already carries. The report SHALL say that nothing was written and that a retry in seconds is safe. The code's number is the design's, and the exit table SHALL list it. This narrows Amendment 22's S1, which gives 69 to a transaction the database refuses: 69 stays for an unreachable database and any other refusal, and a refusal by the register's own rules exits 2. The deadline SHALL exceed the drain time of the largest burst the agreement tests inject. A write any of whose commit calls was in doubt SHALL NOT exit "register busy" without first making the lookup the next requirement sets out.

> Brett's answer to Q7: option (A″), #165 6710653292.

#### Scenario: A spent deadline
- **WHEN** a write is still losing its turn when the deadline passes
- **THEN** it exits "register busy" with its own code, says nothing was written and a retry is safe, and leaves the register unchanged

#### Scenario: Busy is not an outage
- **WHEN** a caller receives the busy code
- **THEN** the code differs from 69 and from every other code in the exit table

### Requirement: A COMMIT whose outcome the writer cannot learn is recovered by its write id
Each write SHALL carry a write id that is UNIQUE in the journal. After an outcome it cannot learn (the connection lost after COMMIT was sent, `psql` exit 2, or an exit 124 from an outer timer), the writer SHALL reconnect and look up its id, SHALL exit 0 if the write is there, and SHALL retry cleanly if it is not. The retry SHALL go through the UNIQUE write id with a `lock_timeout`, and not through a lookup followed by a blind write. A verb SHALL read the journal by write id and by sequence number. A retry of an in-doubt write SHALL reuse its write id, and the in-doubt report SHALL name the id; the report's exit code where the writer cannot reconnect is the design's. Each commit call SHALL carry a time bound, `not_after`, past which the database commits nothing from it. Once any commit call of a write was in doubt, the writer SHALL end with no code other than 0 until it has made one lookup: it SHALL wait until the database's clock has passed the largest `not_after` its commit calls carried, SHALL take the head row's lock in a try that starts within its reconnect window, and SHALL look its write id up. Where the write is there, it SHALL exit 0 and run the verb's after-COMMIT tail at most once, as below; where it is absent and the lock was taken, it SHALL exit the code its last attempt earned, whether "register busy", 69, 2 or the verb's own; and where no such try takes the lock, it SHALL exit the in-doubt code naming the write id, keeping the record of the pending write. Where that `not_after` is too far ahead for the lookup's wait, or the database's clock has not passed it once the lookup holds the head row's lock, the lookup SHALL be refused, and the writer SHALL exit the in-doubt code naming the write id, keeping the record of the pending write. Where the lookup is refused because the write id is of another generation, or because the journal holds it for another write, the writer SHALL exit 2, keeping the record of the pending write in the first case and removing it in the second. Such a write SHALL NOT exit "register busy" without first making that lookup. A plain rerun that still finds an identical write's pending record whose owner, the run that last claimed it, is still live when the deadline passes SHALL exit the in-doubt code, naming that record's write id, having sent nothing. A rerun whose snapshot or commit finds its write id in the journal SHALL exit 0 without writing the edit again, and SHALL run the verb's after-COMMIT tail at most once: in one child stopped at the seam with the entry's own change set, and only after it has marked the tail sent in the record of the pending write. A run that finds the tail already sent or done, that holds no record of the pending write, or whose replay of the verb does not reach the seam with the entry's change set SHALL run none of the tail, and SHALL name what is owed, or, where the record of its tail or the write's own workstation shows it may have run, SHALL say that it may already have run; `request-handoff`, whose tail's wait decides its code, SHALL then exit 2 in place of 0. The snapshot SHALL look the write id up before it checks the switch, so that a write already in the journal exits 0, or 2 as below, whatever the switch now reads. A reused write id whose journal entry names a different lane, verb or arguments from this run's, or that was minted against another generation of the register, SHALL be refused with 2. A plain rerun that finds a record of the pending write from another generation, however old the record is, SHALL refuse with 2, sending nothing. It SHALL name that write id and how to check the register for the write's effect, and SHALL name the after-COMMIT steps that may be owed if the effect is there.

#### Scenario: In doubt at the deadline (agreement test 12)
- **WHEN** a write's commit call is in doubt and its own orphaned transaction still holds the head row when the deadline passes
- **THEN** the writer does not exit "register busy" before its lookup: once the database's clock has passed its largest `not_after`, it takes the head row's lock and looks its id up, and exits 0 if the write is there, "register busy" if it is absent with the lock taken, or the in-doubt code naming the id if no try that starts within the reconnect window takes the lock
- **AND** a rerun after the in-doubt code reuses the id and the journal holds at most one entry for it

#### Scenario: The lookup waits past `not_after`
- **WHEN** a write's commit call is in doubt, and an orphaned copy of that call has not yet reached the head row's lock when the writer is about to end
- **THEN** the writer makes its lookup only once the database's clock has passed the largest `not_after` its commit calls carried
- **AND** no entry for its write id commits after a lookup that found the write absent

#### Scenario: A rerun finds its write and runs the tail
- **WHEN** a rerun that holds the record of the pending write, its tail not yet run, finds its write id in the journal by its snapshot or its commit, as for an in-doubt `rename-lane` whose transaction had committed
- **THEN** it exits 0, writes the edit no second time, and runs the verb's after-COMMIT tail once, for `rename-lane` the handoff file's move and its stamp

#### Scenario: A run that finds its write committed runs none of the tail
- **WHEN** a run finds its write id committed, but its record of the pending write shows the tail sent or done, or it holds no record of the pending write
- **THEN** it writes nothing, runs none of the tail, and names the tail it did not run

#### Scenario: A `request-handoff` that runs none of its tail
- **WHEN** a `request-handoff` run finds its write id committed and runs none of its tail, because its record of the pending write shows the tail sent or done, it holds no record of the pending write, or its replay of the verb does not reach the seam with the entry's change set
- **THEN** it writes nothing, names the tail it did not run, and exits 2, not 0

#### Scenario: A rerun that finds the same write still running
- **WHEN** a plain rerun finds the record of an identical pending write whose owner, the run that last claimed it, is still live when the deadline passes, counted from the rerun's own start
- **THEN** it exits the in-doubt code naming that record's write id, and sends nothing

#### Scenario: A lookup made too early
- **WHEN** a write in doubt makes its lookup before the database's clock has passed the largest `not_after` its commit calls carried, and the lookup cannot wait for it, as where that `not_after` is too far ahead or the clock stepped back before the lookup held the head row's lock
- **THEN** the lookup is refused, and the writer exits the in-doubt code naming the write id and keeps the record of the pending write

#### Scenario: A lookup refused for another generation
- **WHEN** a write in doubt makes its lookup, and the lookup is refused because the write id is of another generation of the register
- **THEN** the writer exits 2 and keeps the record of the pending write

#### Scenario: A lookup refused for another write
- **WHEN** a write in doubt makes its lookup, and the lookup is refused because the journal holds its write id for another write
- **THEN** the writer exits 2 and removes the record of the pending write

#### Scenario: An in-doubt write whose next attempt is refused for another reason
- **WHEN** a write's commit call is in doubt, and its next attempt is refused for another reason: the database refuses it, a rule of the register refuses it, or the verb itself refuses
- **THEN** the writer makes the lookup before it exits, and exits 0 if the write is there, the refusal's own code if it is absent with the lock taken, or the in-doubt code naming the id if no try that starts within the window takes the lock

#### Scenario: A committed write found after the switch has moved
- **WHEN** a write's in-doubt commit had committed, and a rollback has since set the switch to `files`
- **THEN** its rerun's snapshot finds the write id and exits 0, not 69

#### Scenario: A reused id for a different write
- **WHEN** a run reuses a write id whose journal entry names a different lane, verb or arguments
- **THEN** it is refused with 2 and nothing is written

#### Scenario: A write id from another generation
- **WHEN** a pending record or a printed rerun made against a lost database is run against its replacement, once the replacement carries a new generation: after a re-import, from its bootstrap on; after a restore, from its after-loss cutover on
- **THEN** it is refused with 2 and nothing is written
- **AND** between a restore and its after-loss cutover, and after that cutover's abort, where the restored database carries the lost database's generation, the lanes stay paused, since such a record is not refused for its generation there

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

### Requirement: `lane-start`'s bind is one write
In every mode, `lane-start`'s register writes for a bind (a new lane's row, or the row's session cell and its state, and the `STARTED` or `RESUMED` line) SHALL be one write verb, `start-record`. On `mode=primary` it SHALL run as one transaction that carries the union of the expectations those writes' deciding reads need, with one journal entry and one mirror commit; in files mode its writes SHALL be one register commit and one push. A session-cell append whose anchor no longer holds SHALL be left out and said, as today, and the state and the line SHALL still be written. The Rule 3 handoff stamp SHALL stay a git commit by the lane, made after the write, and a failed stamp SHALL never undo the bind. A `start-record` that exits 2 SHALL stop the launch with 2, whether an expectation refused it, which is a second binding, a key that is not a scope refused it, or any other refusal that exits 2. Where `start-record` makes the lane's row (`--add-row`), an exit of 69, the busy code or the in-doubt code SHALL stop the launch with that code, since the row is a write that must exist. Where it makes no row (a resume, or a lane that already has a row), an exit of 69, the busy code or the in-doubt code SHALL print a note carrying the writer's printed rerun, its generation included where the write has an id (a pending record, or `LANES_WRITE_ID`), or saying that nothing was sent where it has none; the Rule 3 stamp SHALL then be written and the launch SHALL go on, as round 1's answer on `lane-start` (2A) has it. In the prompt guard's detached bind (`lane-start --no-launch --bind`), any code but 0 SHALL end the bind's record as failed with that code, with no stamp and nothing bound, and on the in-doubt code the record SHALL keep the write id, the generation and the commit call's time bound for the next answer's lookup.

#### Scenario: Two sessions bind one lane
- **WHEN** two sessions bind the same lane at once on a `mode=primary` workstation
- **THEN** one `start-record` commits, the other is refused with 2 by its expectation and writes nothing, and that launch stops

#### Scenario: A bind is one journal entry
- **WHEN** `lane-start` resumes a lane on a `mode=primary` workstation
- **THEN** its session cell, state and `RESUMED` line are one journal entry, and its stamp is one handoff commit

#### Scenario: A bind in files mode
- **WHEN** `lane-start` resumes a lane on a workstation that is not `mode=primary`, with `lanes/STORE` absent
- **THEN** its session cell, state and `RESUMED` line are one register commit and one push, and its stamp is one handoff commit

#### Scenario: A bind refused with another 2
- **WHEN** `start-record` exits 2 for a refusal other than an expectation, such as a line that derives a key that is not a scope
- **THEN** `lane-start` stops with 2 and launches nothing

#### Scenario: A bind that makes no row, refused or in doubt
- **WHEN** `lane-start` binds a lane that already has a row, a resume among them, and `start-record` exits 69, the busy code, or the in-doubt code
- **THEN** `lane-start` prints a note with the writer's printed rerun, naming the write id and its generation where the write has an id (a pending record, or `LANES_WRITE_ID`), or saying that nothing was sent where it has none, writes the stamp, and the launch goes on

#### Scenario: A bind that makes the row, refused or in doubt
- **WHEN** `lane-start` starts a new lane, whose `start-record` makes its row (`--add-row`), and `start-record` exits 69, the busy code, or the in-doubt code
- **THEN** `lane-start` stops with that code and launches nothing

#### Scenario: The guard's detached bind fails
- **WHEN** the guard's detached bind runs `lane-start --no-launch --bind` and `start-record` exits any code but 0
- **THEN** the bind's record ends failed with that code, no stamp is written, and nothing is bound

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

> Brett's answer to Q13: option (a), #165 6710653292.

#### Scenario: The move fails after the transaction
- **WHEN** the transaction commits and the handoff move then fails
- **THEN** the register holds the new lane name and the row's handoff cell names the file not yet moved

### Requirement: A claim's comment cites the register sequence
The claim comment SHALL cite "register seq `<n>`", where `<n>` matches the `Register-Seq: <n>` trailer of the mirror commit, and the manual SHALL say how to find that commit from the sequence number.

> Brett's answer to Q19: option (A), #165 6710653292.

#### Scenario: Finding the commit
- **WHEN** a reader holds a claim comment citing register seq `n`
- **THEN** the mirror commit with `Register-Seq: n` in `git log` is that write's commit

### Requirement: The journal records each write
Each transaction SHALL append one journal entry holding a sequence number, the write's commit message exactly as today (`LANES(<lane>@<ws>): …` or `LOG(<lane>@<ws>): …`) and the line edits its mirror commit makes. The sequence number SHALL follow commit order and run without gaps. An entry SHALL be able to span more than one rendered file, with file creates, renames and removals among its edits, and SHALL replay byte-identically. An entry whose edits touch only `lanes/STORE` SHALL be an operator entry, flagged as such in the journal. Exactly two kinds of operator entry SHALL pass the switch check, each written in its command's own transaction: the cutover's entry, at the seq after the head (seq 1 at a first cutover; `m+1` after a re-import, where the cutover's after-loss form writes the marker's rewrite so that the next write is `m+2`; or `b+1` after a restore), in the transaction that sets the switch to `primary`; and the rollback's last seq, in the transaction that sets the switch to `files`. Besides the import's baseline (seq 0, or `m` after a loss), which is journalled while the switch is `files`, no other entry SHALL be journalled while the switch is not `primary`. An operator entry SHALL write no row outside `lanes/STORE`: a cutover's or rollback's entry that also writes any other row, another file's row or any structured row, SHALL be refused when it is committed, and nothing SHALL be written, so that no such row is left in the journal to make a later abort of that entry fail.

#### Scenario: An operator entry that writes outside `lanes/STORE`
- **WHEN** a cutover's or a rollback's entry also writes a row outside `lanes/STORE`
- **THEN** its commit is refused, and the journal, the head, the rows and the switch are unchanged

#### Scenario: The operator entries
- **WHEN** a cutover and then a rollback run to their end
- **THEN** the journal holds exactly two operator entries, the cutover's entry (seq 1, `m+1` after a re-import or `b+1` after a restore) and the rollback's last seq, each flagged, and nothing journalled after the switch went to `files`

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
After a transaction commits, the writer SHALL fork the renderer. The renderer SHALL commit every sequence the mirror lacks, in sequence order, one commit per write, with that write's message and a `Register-Seq: <n>` trailer in the message's final trailer block, so that `git log` of the workspace repository stays the audit trail in commit order. It SHALL render `lanes/LANES.md`, `lanes/log/<lane>.md`, `lanes/aliases.tsv` and `lanes/archive/`, and SHALL touch nothing else. Two renderers racing SHALL produce one commit per sequence, in order. The renderer SHALL NOT render an operator entry: the command that wrote it pushes its commit, with the renderer identity, and confirms it by Amendment 23's S4 read-back, and the renderer counts that seq as rendered once it is on origin. The renderer SHALL find the mirror's last sequence from the `Register-Seq` trailers on the first-parent history of the `main` it fetched, by a lookup bounded in time; where that lookup runs out of time, or the history carries no `Register-Seq`, the renderer SHALL render and push nothing, SHALL record why with the mirror's state, and `lanes-index status` SHALL show it.

> Brett's answer to Q18: option (A), #165 6710653292.

#### Scenario: An operator seq not yet on origin
- **WHEN** the next sequence the mirror lacks is an operator entry whose commit is not yet on origin
- **THEN** the renderer renders nothing for it and pushes nothing past it

#### Scenario: No `Register-Seq` found
- **WHEN** the renderer's lookup of the mirror's last sequence runs out of time, or finds no `Register-Seq` on the first-parent history
- **THEN** it renders and pushes nothing, records why, and `status` shows it

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

> Brett's answer to Q27: option (a), #165 6710653292.

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
A fork that finds a render running in the same checkout SHALL leave a rerun mark, try the render's lock once more, and exit at once; the running render, after it releases its lock, SHALL look for a mark once more and take the lock back for it, so that a write that commits between the render's last look and its release is still rendered. The lock SHALL be released by the kernel when its holder dies, so that a renderer killed while holding it blocks no later render. A pass SHALL render a bounded number of sequences and make a bounded number of push attempts. A renderer SHALL make another pass only after a pass that moved origin's last `Register-Seq` past where that pass found it, or once for each rerun mark it finds after it releases, so that a pass that cannot progress (a failed fetch or push, a divergence, an operator seq not yet on origin) ends it and never loops; after a bounded number of passes it SHALL hand over once, re-forking itself detached, only where its last pass made progress. The bounds are the design's. A render's lock SHALL NOT be the lane's write lock, so `lane-start`'s stamp and `lane-handoff`'s commit never queue behind a render.

> Brett's answer to Q27: option (a), #165 6710653292.

#### Scenario: A burst of writes from one lane
- **WHEN** one lane makes a burst of writes that arrives within one pass of the running render
- **THEN** at most two renders run and no fork waits

#### Scenario: A slower stream of writes
- **WHEN** writes arrive more slowly than one pass of the running render
- **THEN** there is at most one render per pass, never one per write, and no fork waits

#### Scenario: The last write of a burst is rendered
- **WHEN** the last write of a burst is made and no later write happens anywhere
- **THEN** it is rendered all the same

#### Scenario: A write that commits as the render lets go
- **WHEN** a write commits after the running render's last look at the journal and before it releases its lock, and its fork finds the lock held
- **THEN** that write is rendered without any later write

### Requirement: The renderer does not inherit `LANES_NO_FETCH=1`
A render forked from a verb run with `LANES_NO_FETCH=1` SHALL render, and SHALL NOT apply the variable to its own fetch.

#### Scenario: A render from a verb with the variable set
- **WHEN** a verb runs with `LANES_NO_FETCH=1` and forks a render
- **THEN** the render fetches and renders

### Requirement: A pending hand edit has a way out on `mode=primary`
On `mode=primary` a writer SHALL look for a pending hand edit of a rendered file in the writing lane's checkout, `<W>-lanes/<lane>/`, before its snapshot, since the verb itself runs on a scratch tree. Where Amendment 23's S3 would refuse a writer because a hand edit is pending, the refusal on `mode=primary` SHALL name the file and print a recovery that works: restore the file, and make the write through its verb. It SHALL NOT print `lanes-edit.sh commit` as the recovery.

#### Scenario: A pending hand edit
- **WHEN** a hand edit of a rendered file is pending and a writer runs
- **THEN** the refusal names the file and tells the lane to restore it and use the verb

### Requirement: Commits that touch none of the rendered paths are part of the tip
A commit that touches none of the four rendered paths SHALL be part of the tip the renderer renders onto and SHALL NOT stop it. That includes handoff commits, the cutover's commit adding `lanes/STORE`, rollback's commit removing it, and edits to `lanes/repos.tsv`. `lanes/repos.tsv` SHALL stay a git file that a person commits in their lane's checkout.

> Brett's answer to Q18: option (A), #165 6710653292.

#### Scenario: Unrendered commits between renders (agreement test 6)
- **WHEN** a cutover commit, a `repos.tsv` edit and a handoff commit land between two renders
- **THEN** the renderer is not stopped and renders onto the new tip

### Requirement: A commit the renderer did not make to a rendered path stops it
A commit that changes a path the renderer renders and that the renderer did not make SHALL stop the renderer. It SHALL NOT render over it. `lanes-index status` SHALL report `diverged at <sha>`, and a person's word SHALL decide whether that write is imported into SQL or reverted.

#### Scenario: A stray commit (agreement test 6)
- **WHEN** the v1.1.0 `lanes-edit.sh` pushes a commit to a rendered path of a primary fixture
- **THEN** the renderer stops, renders nothing over the commit, and `status` prints `diverged at <sha>`

### Requirement: A `Register-Seq` above the journal's head stops the renderer
Where, while the switch is at `primary`, the last `Register-Seq` the renderer finds on the first-parent history of the `main` it fetched is above the journal's head, as where a commit carries one copied or forged past the head, the renderer SHALL record a divergence at the commit that carries it, SHALL render and push nothing, and SHALL stop, and `lanes-index status` SHALL report `diverged at <sha>` naming that commit. A commit that a way out of a divergence has resolved SHALL NOT count, by any `Register-Seq` it carries, when the renderer finds the mirror's last sequence. The renderer's revert of that commit (`lanes-index render --revert <sha>`), run on a person's word, SHALL be a way out: its entry names the commit, so that the renderer finds the mirror's last sequence before that commit and renders on to the head.

#### Scenario: A `Register-Seq` above the head
- **WHEN** the switch is at `primary` and the last `Register-Seq` on the first-parent history of the `main` the renderer fetched is above the journal's head
- **THEN** the renderer records a divergence at the commit that carries it, renders and pushes nothing, and stops
- **AND** `status` prints `diverged at <sha>` naming that commit

#### Scenario: The revert of a `Register-Seq` above the head
- **WHEN** a person runs the renderer's revert on the commit that carries a `Register-Seq` above the head
- **THEN** its entry names that commit, `diverged at` clears, and the renderer no longer counts that commit's `Register-Seq`: it finds the mirror's last sequence at the render commit before it and renders on to the revert's own seq

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
On `mode=primary` every act and every guard SHALL read the database: `who`, `binding`, `live-holder`, `next-free`, `managed-projection`, the guard, `lane`, `lane-start`, `lane-end`, `lane-handoff` and `lane-rename`. Where the act writes, the reads it decides by SHALL hold at its COMMIT: they run inside the transaction that commits the write, or on a snapshot at head `n` whose SERIALIZABLE commit succeeds only while the head is still `n`. Where an act's deciding reads come before the write verb they decide (one transaction per verb), each such verb SHALL carry them as expectations: the act SHALL take a base, the journal's head sequence and the register's generation, read together, before its first deciding read, and each write it decides SHALL name the scopes its earlier reads cover (a lane's row, its lane-kind log lines, its live claims and open takers, the alias lines that name it, the archive rows that name it, and the lines that carry a session's id). Its commit SHALL check them in its own transaction, refusing with 2, and writing nothing, where a journal entry after the base, made outside the act, touched a named scope, or where the base was read in another generation of the register; the refusal for a changed scope SHALL name the scope, that entry's seq, and its lane and verb, as `expectation: <scope> changed at seq <k> by <lane>/<verb>`. The act's own earlier writes SHALL NOT refuse it. A write from an act whose base was taken where the workstation was not `mode=primary` SHALL be refused with 2 on a `mode=primary` workstation, before any database call. What each entry touched SHALL be derived from the lines it changes, never sent by the writer; a write, or an import, a line of which derives a key that is not well formed (a scope's name is lower case, not empty, and holds no white space) SHALL be refused with 2, naming the path, the line and the key, and nothing SHALL be written. An act that cannot carry its reads that way, or that carries them only under a condition Brett Heap refuses, is a narrowing of Amendment 22's S3, and is Brett Heap's to decide. `register-row-local` SHALL read the database on `mode=primary`, as `register-row` does, since no local row can be ahead of a write that is in SQL before it returns. A read that fails SHALL refuse with 69, or with 2 in the guard, except where the next requirement says otherwise; a failed read of `register-row-local` SHALL exit 69 and never 8.

> Brett's answer to Q7: option (A″), #165 6710653292; the wording holds under (A′) and (A) as well.

#### Scenario: A rival commit between an act's read and its write
- **WHEN** an act takes its base and reads a lane's row, another act's commit then changes that row, and the first act's write verb commits with the row's scope as its expectation
- **THEN** the commit refuses with 2, names the scope and the seq that changed it, and writes nothing

#### Scenario: An act's own earlier write
- **WHEN** an act's earlier write changed a scope that its later write names as an expectation, and no other act touched that scope after the act's base
- **THEN** the later write is not refused for it

#### Scenario: A base read before an after-loss cutover
- **WHEN** an act takes its base, an after-loss cutover then replaces the register with a new generation, and a write the act decides runs with that base
- **THEN** the write refuses with 2 and writes nothing

#### Scenario: An act begun off primary whose write runs on primary
- **WHEN** an act takes its base where the workstation is not `mode=primary`, so the base holds no sequence, and a write the act decides then runs on a `mode=primary` workstation
- **THEN** the write refuses with 2 before any database call, and writes nothing

#### Scenario: A line that derives a key that is not well formed
- **WHEN** a write changes a line, or an import loads one, that derives a key whose name is empty or holds white space
- **THEN** the write or the import refuses with 2, naming the path, the line and the key, and writes nothing

#### Scenario: The reader table (agreement tests 2 and 11)
- **WHEN** the database is unreachable and `who`, `binding`, `live-holder`, `next-free`, `lane`, `lane-end` or `lane-rename` runs
- **THEN** it exits 69 and prints no answer on stdout

#### Scenario: A reader never answers "no record" for a failed read
- **WHEN** the database is unreachable and `lane-last`, `register-row`, `register-row-local`, `session-lane`, `canon-lane` or `resolve-home` runs
- **THEN** it exits 69, prints no answer on stdout and never exits 8

#### Scenario: `register-row-local` on primary
- **WHEN** `register-row-local` runs on a `mode=primary` workstation whose lane checkout holds a row that differs from SQL's
- **THEN** it answers from the database, as `register-row` does, 8 included where SQL has no row

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
Only reads that decide nothing SHALL read the mirror: the `SessionStart` block and the launcher's bare window reads (offline, always exit 0), and the listings `lanes` and `history` when the database is unreachable. The two other mirror reads are the naming read of `lane-handoff` and the handoff skill, which the requirement for them sets out, and the guard's answer during an outage Brett Heap has declared, which the register service's requirement for a declared outage sets out. A mirror read SHALL print on stderr `read: mirror at <sha12>, <k> sequences behind or unknown (sql unreachable: <why>)`, SHALL keep its exit, and SHALL NOT change stdout. Where a caller acts on a listing's answer, as `lane` and `lane-end --retire-dormant` act on `lanes`' answer, that read decides and SHALL refuse with 69.

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
On `mode=primary`, `lane-start` SHALL refuse with 69 before anything is renamed or written at the reads that decide state: `managed-projection` (`lane-start:1555`), the row count (`:1609`), `lane-last` (`:2335`) and `canon-lane` (`:1154`). It SHALL refuse with 69 where the write must exist: `request-handoff` (`:2497`) and `add-row` for a new lane (`:3819`). It SHALL print a note and go on where `append-session-id` (`:3848`, `:3869`, `:3906`, `:4019`), `set-row-state LIVE` (`:3985`) and `log STARTED` or `RESUMED` (`:4178`) are refused. The Rule 3 handoff stamp and the sidecars SHALL work as they do today, and the stamp SHALL stay a git commit that never blocks the launch. Once a new lane's row or the session cell, the state and the line are one `start-record` write, its refusals SHALL follow the requirement "`lane-start`'s bind is one write", which keeps these outcomes. Line numbers are those of the code pin `2a057a6d`.

> Brett's answer to Q8(a): round 1's 2A, R3's option A with ADV-C5's `:1154`, #165 6710653292.

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

### Requirement: The busy and in-doubt codes refuse the launch at `lane-start`'s seven default read arms
At the seven read arms of `lane-start` whose default arm notes an unknown code and carries on, or passes it silently (`:1154` `canon-lane`, `:1262` `swapped`, `:1271` `lane-dir`, `:1332` `resolve-home`, `:2184–2186` `session-lane`, `:3079` `lane-transcript` for the row's session cell, `:3430` `lane-transcript` for the last `PAUSED` record's resume id), the busy code or the in-doubt code SHALL refuse the launch, naming the code and saying that nothing was written and a retry in seconds is safe. Every caller's default arm on a read SHALL refuse on the busy code. `lane-start`'s bind is not a read arm: the busy code or the in-doubt code from its `start-record` follows the requirement "`lane-start`'s bind is one write". The manual SHALL list it.

#### Scenario: One test per arm
- **WHEN** the verb at any one of the seven read arms exits the busy code or the in-doubt code
- **THEN** the launch refuses and the register's rows and log are unchanged

### Requirement: `lane-handoff` and the handoff skill with the database unreachable
On `mode=primary`, with the database unreachable, the reads that name the lane before the handoff file is published SHALL read the mirror, labelled with the `read: mirror` line, so that the handoff file is still written (`R-A11-11`). The `PAUSED` record's three writes SHALL exit 69 and then print a note, and the lane SHALL stay `SWAPPING`, an interrupted swap that the existing late `PAUSED` path completes. The handoff commit and the sidecars SHALL work as they do today. The note SHALL name how the `SWAPPING` lane's `PAUSED` record is completed later: the late `PAUSED` path, who runs it, and when.

> Brett's answer to Q8(b): round 1's 2A, R3's option A with P15, #165 6710653292.

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

> Brett's answer to Q4(c): option (i), #165 6710653292.
> Brett's answer to Q4(d): option (i), #165 6710653292.

#### Scenario: A local-only line
- **WHEN** a lane holds a record that never reached origin
- **THEN** the import does not take it

#### Scenario: The unreadable lines
- **WHEN** the import reads the 15 `WORD` lines of `lanes/log/codeXfactory-2.md`
- **THEN** they are imported byte-for-byte, flagged, and the warning stays

### Requirement: The cutover is one guarded sequence
The cutover SHALL run with `LANES_LANE` naming the operator lane, through that lane's checkout. In one short transaction it SHALL set the database switch to `primary` and write the cutover's entry, the operator entry adding `lanes/STORE`, at the seq after the head: seq 1 at a first cutover, `m+1` after a re-import, or `b+1` after a restore. No write SHALL commit until the cutover's push is confirmed and recorded. It SHALL then push that commit, with the renderer identity, as a fast-forward of origin's tip, which descends from the imported commit and whose `lanes` tree has the imported commit's id; SHALL confirm it by Amendment 23's S4 read-back; and SHALL then flip `mode=primary` on the workstations act 3 configured. It SHALL abort if `main` moved under `lanes/` since the import, in which case the import is re-run at the new tip. A push it cannot confirm SHALL take neither the success nor the abort branch: the cutover SHALL exit 3, and its rerun SHALL read origin back before it acts. The abort SHALL set the switch back, delete the cutover's entry and set the head back to the import's seq, in one transaction, and only after a read-back proves the marker absent from origin; the abort of a restore's cutover SHALL leave no seq after `b` and change nothing else. `lanes/STORE` SHALL carry the cutover's entry's `Register-Seq`, and the first write after the cutover SHALL be mirrored at the next seq (`Register-Seq: 2` after a first cutover). No operator command SHALL hold a database transaction across a git act.

> Brett's answer to Q18: option (A), #165 6710653292.

#### Scenario: A successful cutover
- **WHEN** the cutover runs after a byte-identical import
- **THEN** the switch is `primary`, one fast-forward commit adds `lanes/STORE` with no credential, and the next write goes to SQL

#### Scenario: The smoke test
- **WHEN** one `NOTED` write is made after the cutover
- **THEN** it has a SQL row, a mirror commit at the seq after the cutover's entry (`Register-Seq: 2` after a first cutover), reads back, and `status` shows a lag of 0

#### Scenario: `main` moved under `lanes/`
- **WHEN** `main` has moved under `lanes/` since the import
- **THEN** the cutover aborts, pushes no marker, leaves the switch not `primary`, and leaves no cutover entry in the journal, with the head back at the import's seq

#### Scenario: A push the cutover cannot confirm
- **WHEN** the cutover's push of `lanes/STORE` cannot be confirmed by its read-back
- **THEN** the cutover exits 3, and neither aborts nor flips any workstation until a read-back shows whether the marker is on origin

#### Scenario: A write before the cutover's push is confirmed
- **WHEN** a write is tried after the cutover's entry is written and before its push is confirmed and recorded
- **THEN** it is refused with 69 and nothing is written

#### Scenario: An abort after a restore
- **WHEN** a restore's after-loss cutover, at `b+1`, aborts
- **THEN** the journal holds no seq after `b`, and the switch and `lanes/STORE`'s rows are as the restore left them

### Requirement: The cutover takes its database label from the workstation's configuration
`lanes-index cutover`, in every form, SHALL take the database label it sends, the entry's label in the after-loss forms and `lanes/STORE`'s `database=` in every form, from the workstation's configuration, `register_label=`, which names the database that its `register_url=` reaches, and SHALL NOT take it from a flag. A cutover whose configuration carries no `register_label=` SHALL be refused with 2 before it connects, writing nothing. Where the switch it reads is at `files`, at a first cutover and after a re-import, a `register_label=` that differs from the switch's label SHALL be refused with 2 before the cutover sends anything, writing nothing. An after-loss cutover that meets the switch at `primary` SHALL be refused with 2, writing nothing, unless its label differs from the switch's. With the switch at `primary`, the readiness check SHALL fail where the configuration's `register_label=` is not the switch's label.

#### Scenario: A host still pointed at the live database
- **WHEN** `lanes-index cutover --after-loss` runs on a host whose configuration still reaches the live database
- **THEN** it sends the live database's own label, which is the switch's, is refused with 2, and nothing is written

#### Scenario: The restore route
- **WHEN** a restored backup, whose switch still holds the lost database's label, is reached through a configuration re-pointed with the replacement's own `register_label=`
- **THEN** the after-loss cutover commits, and the switch takes the replacement's label

#### Scenario: A label that is not the switch's at `primary`
- **WHEN** `lanes-index ready` runs where the switch is at `primary` and the configuration's `register_label=` is not the switch's label
- **THEN** its check 10 fails, naming both labels

### Requirement: Rollback returns the estate to the files
`lanes-index rollback` SHALL run with `LANES_LANE` naming the operator lane, through that lane's checkout, from a shell or from a `CLAUDE_NO_LANE=1` session, with no lane session and no guard. It SHALL refuse to start while `status` shows `diverged at`: S2's two ways out resolve the divergence first, and they run only while the switch is `primary`. Right before its transaction it SHALL fetch `origin main` again and look for a divergence as the renderer recognises one, a last `Register-Seq` above the journal's head included, so that a stray already on origin but not yet recorded is caught: it SHALL record one it finds and exit 2 with `diverged at <sha>`, writing nothing. Its transaction SHALL refuse it too where a divergence is recorded by then, one being recorded at that moment included: the rollback SHALL exit 2 with `diverged at <sha>` and write nothing. In one transaction it SHALL switch to `files`, so that every SQL write refuses from that transaction on, and write its last seq, the operator entry removing `lanes/STORE`; nothing SHALL be journalled after it. It SHALL render until `status` shows the mirror holding every seq before that one and byte-identical to a full render. The rollback SHALL remove the ruleset, where one is in place, as Brett Heap's act, after that render and before the next step: `lanes-index rollback --hold-marker` stops after the render, and `lanes-index rollback`, run again, resumes at the push. It SHALL push the commit removing `lanes/STORE`, with the renderer identity, and confirm it by Amendment 23's S4 read-back, exiting 3 for a push it cannot confirm, with a rerun that reads origin back first; and SHALL clear `mode=primary` on every workstation. A mirror that a divergence has stopped SHALL NOT count as current.

#### Scenario: A diverged mirror
- **WHEN** `status` shows `diverged at` and a rollback is tried
- **THEN** it refuses to start, changes neither the switch nor the marker, and names S2's two ways out

#### Scenario: A divergence recorded as the rollback starts
- **WHEN** a divergence is recorded after the rollback's check before it starts, before or while its transaction runs
- **THEN** the rollback exits 2 with `diverged at <sha>`, and the switch, the marker and the journal are unchanged

#### Scenario: A stray on origin not yet recorded
- **WHEN** a commit the renderer did not make, changing a rendered path or carrying a `Register-Seq` above the journal's head, is on origin with no divergence recorded when a rollback has passed its check before it starts
- **THEN** the rollback's fetch right before its transaction finds it and records the divergence, and the rollback exits 2 with `diverged at <sha>`, with the switch, the marker and the journal unchanged

#### Scenario: The rollback journals its own last seq
- **WHEN** the rollback sets the switch to `files`
- **THEN** its last seq, removing `lanes/STORE`, is written in that same transaction, and nothing is journalled after it

#### Scenario: A rollback held at the marker
- **WHEN** `lanes-index rollback --hold-marker` runs
- **THEN** it stops after the render's proof with the marker still on origin, and a second `lanes-index rollback` pushes the marker's removal

#### Scenario: A rollback (agreement test 8)
- **WHEN** a rollback runs to its end
- **THEN** the files are byte-identical to a full render, the marker is gone, the ruleset is removed and SQL writes are refused

#### Scenario: A files-mode write after the rollback (agreement test 13)
- **WHEN** a files-mode writer writes after the rollback has run to its end
- **THEN** it succeeds, while a workstation whose `mode=primary` was not cleared refuses

### Requirement: A rollback needs the database
A rollback SHALL set the switch to `files` in the database first. With the database unreachable, a rollback SHALL wait: it SHALL refuse and change neither the switch nor the marker. A database lost for good SHALL be a separate disaster procedure (restore, or re-import), run with every workstation's lanes paused first, and not a rollback. No `mode=primary` writer SHALL commit once a rollback has removed `lanes/STORE`.

> Brett's answer to Q4(b): option (iii), #165 6710653292.

#### Scenario: Rollback with the database stopped (agreement test 8)
- **WHEN** the database is stopped and a rollback is tried
- **THEN** it is refused and neither the switch nor the marker changed

#### Scenario: The partition (agreement test 8)
- **WHEN** the database stays up and only the rolling-back clone cannot reach it
- **THEN** the rollback is refused and removes no marker

### Requirement: A writer the rollback missed cannot write
Once a rollback has run to its end and the database is back, a `mode=primary` writer that was not cleared SHALL write nothing, and the journal's count SHALL be unchanged. That SHALL hold after a fetch and with none since the rollback's push, with `LANES_NO_FETCH=1` set, and through each of `set-row-state`, `replace-in-row`, `append-session-id`, `append-line` and `commit` (the five that make no fetch before they edit) and through one verb that does. The four SQL verbs and the verb that fetches SHALL exit 69; `commit` SHALL exit 2, because on `mode=primary` its hand-edit half refuses as a direct write and its publish half is not a SQL write. The test SHALL fail on the leak as it stands today, a primary writer that refuses only on its last-fetched tip or on a switch reset when the database returns.

> Brett's answer to Q4(b): option (iii), #165 6710653292.

#### Scenario: A missed writer after a fetch (agreement test 8)
- **WHEN** a second clone the rollback missed fetches and then runs any of the five verbs
- **THEN** it exits 69 (2 for `commit`) and the journal's count is unchanged

#### Scenario: A missed writer with no fetch (agreement test 8)
- **WHEN** the second clone has not fetched since the rollback's push and runs any of the five verbs
- **THEN** it exits 69 (2 for `commit`) and the journal's count is unchanged

#### Scenario: `LANES_NO_FETCH=1` (agreement test 8)
- **WHEN** the missed writer runs with `LANES_NO_FETCH=1` set
- **THEN** it exits 69 (2 for `commit`) and writes nothing

#### Scenario: The test must catch the leak
- **WHEN** the test runs against a writer that refuses only on its last-fetched tip
- **THEN** the test fails

### Requirement: The operator's bootstrap is idempotent and holds no secret
This change SHALL build a `lanes-index` bootstrap that makes the register's schema, the three roles and their GRANTs from one admin connection that Brett supplies, prints the password-file line of each login it creates for him to place, and stores no secret. Each engineer's two logins, a writer login and a reader login, and the dump job's login SHALL be made, their passwords rotated and their logins revoked by a separate operator command, `lanes-index login`, through the same admin service, which SHALL print each new password-file line once, store no secret, and set on each login its group's floors, since a role's settings reach no member. The admin connection SHALL be used once and held by nothing, and SHALL be named as a libpq service with its own password file: an argument that carries a connection string SHALL be refused with 2. A roles-only form SHALL make only the roles and the database-level statements that a dump does not carry, `REVOKE ALL ON DATABASE … FROM PUBLIC`, `GRANT CONNECT ON DATABASE … TO lanes_register_writer, lanes_register_reader, lanes_indexer` and `ALTER DEFAULT PRIVILEGES FOR ROLE lanes_register_owner REVOKE EXECUTE ON FUNCTIONS FROM PUBLIC`, and nothing else, for a restore from a database dump or a re-import, since roles are cluster objects that a dump does not hold; a physical backup carries them. The command names are the design's.

> The roles-only form's database-level statements: lane 3's ruling (a), #165 6727022920, carried by lane 1's T109 and its case `dump_restore_privileges`.

#### Scenario: Run twice
- **WHEN** the bootstrap runs twice against the same database
- **THEN** one schema and the same GRANTs exist

#### Scenario: No secret is stored
- **WHEN** the bootstrap completes
- **THEN** it has printed the password-file line of each login it created and written no secret to any file

#### Scenario: A connection string as an argument
- **WHEN** the bootstrap is given a connection string in an argument
- **THEN** it refuses with 2 and makes nothing

#### Scenario: An engineer's logins (real engine)
- **WHEN** `lanes-index login add` runs for an engineer, and later `lanes-index login revoke` for the same engineer
- **THEN** after the first, that engineer's writer login and reader login exist, each a member of its group and carrying its group's floors, and their password-file lines were printed once
- **AND** after the second, a new connection by either login is refused, and an open session's next statement is refused

#### Scenario: A dump restored after the roles-only form (real engine)
- **WHEN** a dump is restored after the roles-only form
- **THEN** PUBLIC holds no CONNECT on the database and no EXECUTE on its functions, and the writer and reader groups and the indexer's login hold CONNECT

### Requirement: The readiness check proves an environment before the cutover
This change SHALL build a readiness check, run in each environment that writes the register (on every engineer's workstation, Eagle and Raven first: the bench containers, the host shells, Raven's install and the Codex lane's tools), that verifies the installed tools' digests against the release, that `psql` is present, that the configuration is readable and says `mode=index` before the cutover, that each of the workstation's logins connects, carries its group's floors itself, as the login step sets them, and is refused outside its scope, that the installed `lanes-index` no longer carries the refusal of `mode=primary`, and that the tools' schema version lies inside the database's window; with `primary` expected, it SHALL also verify that the switch is `primary` with the marker `present` and that `lanes/STORE` names this database. A pass in every environment SHALL be a precondition of act 4. The check is a check at one moment: a container started after it SHALL NOT be covered, and the check SHALL say so.

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

#### Scenario: Tools outside the schema's window
- **WHEN** the installed tools' schema version is outside the database's window
- **THEN** the check fails

### Requirement: The `mode=primary` flip is a command run once per host
This change SHALL build the `mode=primary` flip as a command, run once per host where one configuration serves the host. It SHALL write only the `mode=` line and SHALL keep the file's 0600 mode. A read-only mount SHALL be flipped from the host, not from a bench. Setting `primary` SHALL refuse unless the switch is `primary` with the marker `present` and the fetched `origin/main` carries a `lanes/STORE` naming this database. Under Q20 the flip is the cutover's last step; act 3 leaves `mode=index`.

> Brett's answer to Q20: option (B), round 1's 3A, #165 6710653292.
> Brett's answer to Q23: option (a), #165 6710653292.

#### Scenario: The flip
- **WHEN** the flip runs against a configuration
- **THEN** only the `mode=` line changes and the file stays mode 0600

#### Scenario: A read-only mount
- **WHEN** a bench's configuration mount is read-only
- **THEN** the flip is run from the host and not from the bench

### Requirement: Claim order is commit order
Rule 1's race SHALL be decided by the register's commit order: of two claimants one SHALL commit, and the other SHALL see it and exit 2, writing nothing. The pre-check of the object's holders SHALL hold at the claim's COMMIT, in one of two forms: it runs inside the transaction that inserts the `CLAIMED`, SERIALIZABLE or under a lock taken at READ COMMITTED on the object or on the head row; or it runs on a snapshot at head `n`, and the SERIALIZABLE transaction that inserts the `CLAIMED` commits only while the head is still `n`. In either form that transaction SHALL also refuse, with 2, a claim whose spellings (the spelling as typed, its canonical key and the aliases its writer's `repos.tsv` maps to that key) meet those of another lane's live claim, so that no claim rests on one writer's alias table alone. `CLAIM-LOST` SHALL stay in the grammar for the history that has it and SHALL no longer be produced.

> Brett's answer to Q12: option (a), #165 6710653292.
> Brett's answer to Q7: option (A″), #165 6710653292; the wording holds under (A′) and (A) as well.

#### Scenario: Two concurrent claimants (agreement test 4)
- **WHEN** two lanes claim one object at once
- **THEN** one `CLAIMED` is committed, the other writer exits 2 and writes nothing, and no `CLAIM-LOST` is produced

#### Scenario: Claims spelled two ways
- **WHEN** two claims of one object, spelled two ways, one spelling an alias added to `repos.tsv` between their reads, run from clones at different tips
- **THEN** one `CLAIMED` exists and the other claim exits 2

### Requirement: `HOLD` and `LANDING` are ordered by commit sequence
Amendment 10's `HOLD` and `LANDING` lines on one repository's `main` SHALL be ordered by commit sequence: the earlier commit wins, and the later writer's transaction SHALL report the earlier line to it before it acts. The later writer's verb SHALL exit non-zero, with a code the design names that is neither 0 nor 69, and SHALL print the earlier line, so that a landing script that sees the exit does not merge second. The line SHALL still come before the act that moves `main`. Recognising which lines take and release `main` SHALL be done inside the transaction, by `append-line` or by a verb of the design's choosing. The repository SHALL be compared by a canonical key, so that one repository spelled two ways is one `main`. On `mode=primary` a line in a taking shape, one that matches `HOLD — ` or `LANDING — `, from which no repository can be read SHALL be refused with 2, writing nothing, since it would look like a hold to readers and order nothing. On `mode=primary` a taking line, `HOLD` or `LANDING`, whose UTC cannot be read SHALL likewise be refused with 2, writing nothing, and the refusal SHALL name the rule, since Rule 6's 30 minutes are measured from that UTC and such a line could never be released as stale; files mode, an import's included, SHALL load such a line as it stands.

#### Scenario: Two `HOLD`s (agreement test 4)
- **WHEN** two lanes write a `HOLD` for one repository
- **THEN** they land in commit order, the later writer exits the named code, and it prints the earlier line

#### Scenario: One repository spelled two ways
- **WHEN** two lanes write a `HOLD` for one repository under two spellings
- **THEN** they are ordered as one `main`, and the later writer exits the named code

#### Scenario: A script that checks the exit
- **WHEN** a landing script runs after a later `HOLD` writer exited non-zero
- **THEN** the script does not proceed to the merge

#### Scenario: A taking line that names no repository
- **WHEN** a lane on a `mode=primary` workstation writes a line that matches `HOLD — ` and from which no repository can be read
- **THEN** the write exits 2 and nothing is written

#### Scenario: A taking line with no readable UTC
- **WHEN** a lane on a `mode=primary` workstation writes a line that matches `HOLD — ` or `LANDING — ` and from which no UTC can be read
- **THEN** the write exits 2, the refusal names the rule, and nothing is written

#### Scenario: An imported taking line with no readable UTC
- **WHEN** an import loads, from the files, a `HOLD` or `LANDING` line from which no UTC can be read
- **THEN** the line loads as it stands, and the import is not refused for it

### Requirement: Which lines take and release `main`
`HOLD` and `LANDING` SHALL take `main`. `HOLD RELEASED`, `HOLD WITHDRAWN` and `HOLD EXPIRED` SHALL release a `HOLD`. The `(refresh)` and `(run id)` lines SHALL continue the original. A `LANDING` SHALL be released only by its own lane's `LANDED`, or by a `LANDING EXPIRED` that names it once it is stale, as the requirement "A `LANDING EXPIRED` releases one stale `LANDING`, or writes nothing" sets out; a stale `LANDING` that no such line releases SHALL still take `main`.

> Brett's answer to Q17: option (B), #165 6710653292.

#### Scenario: A withdrawn `HOLD`
- **WHEN** a `HOLD WITHDRAWN` line follows a `HOLD`
- **THEN** a later writer sees `main` as free and is not told of the withdrawn `HOLD`

#### Scenario: A refresh
- **WHEN** a `HOLD (refresh)` line follows a `HOLD`
- **THEN** it continues the original hold and does not take `main` a second time

#### Scenario: A `LANDING` stays until it is released
- **WHEN** a `LANDING` is open: neither its lane's `LANDED` nor a `LANDING EXPIRED` that the commit accepted for it has followed, however long ago it was written
- **THEN** `main` stays taken

### Requirement: A `LANDING EXPIRED` releases one stale `LANDING`, or writes nothing
On `mode=primary` the transaction that commits a `LANDING EXPIRED` line SHALL decide what the line releases, inside that transaction (F13's check, in `commit_write`'s step 7). Any lane MAY write the line, the holder included: it is written only on Brett's word, by the verb that the requirement on `append-line --landing-expired` sets out, and no check that verb makes first SHALL replace this one. The line SHALL name one `LANDING`: by its holder, the line's first `for lane <name>` (`for_lane`), compared in any case; by its repository's `repo_key` alone, not by D18's spellings, so the line spells the repository as that `LANDING` did; and by its PR. The `LANDING` it names SHALL be the newest of the holder's `LANDING`s before it, for that PR, that the line would close, matched as `main_slots` matches a close to its take (equal `repo_key`s or overlapping spellings), and SHALL carry the line's `repo_key`: a `LANDING` posted again for one PR is one landing, as Rule 6 reads it, and its 30 minutes run from the newest. That `LANDING` SHALL be stale only where, in the transaction that commits the `LANDING EXPIRED`, the database's clock is more than 30 minutes past the UTC that the `LANDING` line carries: strictly more, so one exactly 30 minutes old is not stale. The clock that reads the age SHALL be the database's, never a host's. Where a verb wrote the `LANDING` on `mode=primary` and stamped its UTC, as `log LANDING` does, that UTC is the database's clock at the writer's snapshot, in whole seconds, so both times are the database's; a UTC that a caller composed inside `append-line` text, or that a `LANDING` imported from the files carries, stays as written and the 30 minutes run from it; and a `LANDING` whose UTC cannot be read is never stale. On `mode=primary` such a `LANDING` is refused when it is written, so one can come only from the files. Staleness alone SHALL release nothing: a stale `LANDING` still takes `main` until a line releases it. A `LANDING EXPIRED` that the commit accepts SHALL close the `LANDING` it names, with the holder's earlier `LANDING`s for that PR into that repository, as the holder's `LANDED` would close them, and SHALL close nothing else: no `HOLD`, no other lane's line, and no `LANDING` for another PR or repository. It SHALL NOT be a `LANDED`: it records no merge and says nothing about whether one happened, and `main` is then free for that repository only where no other take on it is open. A `LANDING` already closed, by its holder's `LANDED` or by an accepted `LANDING EXPIRED`, matched to it as `main_slots` matches a close, SHALL NOT be closed again. Where the line names no `LANDING` that is open and stale, the commit SHALL refuse the line with LR005, and the refusal SHALL name which of three reasons holds. The first is that there is none: the holder has no `LANDING` before the line for that PR that the line would close, as where the line names another lane or another PR, or the newest one has a `repo_key` that is not the line's, as where the line spells the repository another way. The second is that it is already closed. The third is that it is too young: 30 minutes old or less, or its UTC cannot be read. The writer SHALL exit 2 with the server's message, writing nothing: the journal, the line store, the head row and the mirror are unchanged. A line in the `LANDING EXPIRED` shape from which the holder, the PR or the repository cannot be read SHALL be a line of no kind, which takes and releases nothing.

> Brett's ruling on the stale `LANDING`: "Adopt, on my word (Recommended)", #165 6721583757. Lane 3's ruling that the refusal names its reason (U7): #165 6722541787.

#### Scenario: A stale `LANDING` released (real engine)
- **WHEN** lane B's newest `LANDING` for PR 7 into repository R is open and, at the commit, more than 30 minutes old by the database's clock, and a `LANDING EXPIRED` that names lane B, PR 7 and R as lane B's `LANDING` spelled it commits on `mode=primary`
- **THEN** lane B's `LANDING` is closed as lane B's `LANDED` would close it, and no `LANDED` is written
- **AND** nothing else is closed: lane B's open `HOLD` on R, if it has one, every other lane's line, and lane B's `LANDING`s for other PRs or repositories stay as they were
- **AND** where no other take on R is open, `main` is free for R, and a later `LANDING` by lane C is not refused for lane B's

#### Scenario: A `LANDING` not yet stale
- **WHEN** the `LANDING` the line names is, at the commit, 30 minutes old or less by the database's clock, exactly 30 included, or carries no UTC that can be read, as only a `LANDING` loaded from the files can
- **THEN** the commit refuses the line with LR005 and names the `LANDING` as too young, the writer exits 2, and the journal, the line store, the head row and the mirror are unchanged
- **AND** that `LANDING` still takes `main`

#### Scenario: The database's clock decides
- **WHEN** the writing host's clock reads the `LANDING` as more than 30 minutes old and the database's clock, at the commit, does not
- **THEN** the commit refuses the line with LR005, names the `LANDING` as too young, and nothing is written

#### Scenario: A `LANDING` posted again
- **WHEN** lane B wrote `LANDING` twice for PR 7 into R, under one spelling or under two that D18 counts as one repository, the first more than 30 minutes before the commit and the second 30 minutes or less before it, with no close after either
- **THEN** the commit refuses the line with LR005, names the newest `LANDING` as too young, and nothing is written, since the 30 minutes run from the newest

#### Scenario: A `LANDING` already closed
- **WHEN** the `LANDING` the line names was followed by lane B's `LANDED`, under its spelling or another that D18 counts as one repository, or by a `LANDING EXPIRED` that the commit accepted
- **THEN** the commit refuses the new line with LR005 and names the `LANDING` as already closed, the writer exits 2, and nothing is written

#### Scenario: The repository spelled another way
- **WHEN** the line spells R so that its `repo_key` is not that of lane B's `LANDING`, though D18's spellings would count the two as one repository
- **THEN** the commit refuses the line with LR005, gives as its reason that there is no such `LANDING` under the line's `repo_key`, and nothing is written, and lane B's `LANDING` still takes `main`

#### Scenario: The wrong lane or PR
- **WHEN** the line names a lane that has no open `LANDING` for that PR and `repo_key`, or names lane B with a PR that lane B's open `LANDING` does not carry
- **THEN** the commit refuses the line with LR005, gives as its reason that there is no such `LANDING`, and nothing is written

#### Scenario: A line in the shape that names no holder
- **WHEN** an import loads a line in the `LANDING EXPIRED` shape from which the holder, the PR or the repository cannot be read
- **THEN** it loads as a line of no kind, releases nothing, and the import is not refused for it

### Requirement: A stale `LANDING` is released by `append-line --landing-expired`, on Brett's word
On `mode=primary` a `LANDING EXPIRED` line SHALL be written only by `append-line --landing-expired`, which composes it, and `append-line` SHALL refuse with 2, writing nothing, a text of its own that `lanes-index`'s classifier reads as `LANDING EXPIRED`. The form SHALL require Brett's word as a verbatim quote, where he gave it and when, and SHALL refuse with 2 before the lock when any of the three is missing or empty. It SHALL copy the holder's lane, the PR, the repository's spelling and the UTC from the holder's open `LANDING` as the register reads at the attempt's snapshot, and SHALL refuse with 2, sending nothing, where that `LANDING` is not open or is not past Rule 6's 30 minutes. The form's own checks SHALL NOT replace the commit's F13 check, which decides what the line releases. On success the verb SHALL print, once, the register seq, the `LANDING` it released and how far past the window it was. On `mode=primary`, `who --landing` SHALL NOT list a `LANDING` that a committed `LANDING EXPIRED` released. Off `mode=primary` the form SHALL exit 2 and write nothing, files mode's Rule 6 SHALL be unchanged, and `who --landing` SHALL list such a `LANDING` as today, so that after a rollback to files it lists again, marked STALE. Neither the client nor the database can verify the word, and the design SHALL claim no check of it: the line and its journal entry are the record.

> Brett's ruling on the stale `LANDING`: "Adopt, on my word (Recommended)", #165 6721583757.

#### Scenario: A release on Brett's word (real engine)
- **WHEN** lane B's `LANDING` for a PR into one repository's `main` has stood more than 30 minutes with no `LANDED`, and lane A, on a `mode=primary` workstation, runs `append-line --landing-expired` naming lane B, the PR and the repository, with Brett's quote and where and when he gave it
- **THEN** one `LANDING EXPIRED` line is committed that names lane A as its writer and lane B as the holder, spells the repository as lane B's `LANDING` did, and carries the quote, the place and the time
- **AND** lane B's `LANDING` no longer takes `main`, which is free for that repository where no other take on it is open, and the verb exits 0 and prints the seq, lane B's `LANDING` and the minutes past the window
- **AND** `who --landing <repo>` no longer lists lane B's `LANDING`, and prints none open where it was the only one

#### Scenario: No word, no write
- **WHEN** `append-line --landing-expired` runs without the quote, the place or the time of Brett's word, or with one of them empty
- **THEN** it exits 2 before the lock, and nothing is sent to the database

#### Scenario: The kind typed by hand
- **WHEN** a lane on a `mode=primary` workstation runs `append-line` with a text that begins `LANDING EXPIRED — `
- **THEN** it exits 2, nothing is written, and stderr names `append-line --landing-expired`

#### Scenario: Nothing stale to release
- **WHEN** at the attempt's snapshot the holder has no open `LANDING` for that PR and repository, or it is 30 minutes old or less
- **THEN** the form exits 2 and sends nothing
- **AND** where the commit's F13 check refuses the line instead (real engine), the verb exits 2, prints LR005 and its reason, and nothing is written

#### Scenario: A release in doubt
- **WHEN** the commit of a `LANDING EXPIRED` ends in doubt
- **THEN** the verb exits 3 and prints the rerun with `LANES_WRITE_ID` and `LANES_WRITE_GENERATION` once, and that rerun, with the same arguments, leaves at most one `LANDING EXPIRED` for that `LANDING`

#### Scenario: Files mode
- **WHEN** `append-line --landing-expired` runs on a workstation in files mode
- **THEN** it exits 2 and writes nothing, and `who --landing` lists the holder's `LANDING` exactly as before
- **AND** after a rollback to files, `who --landing` lists a `LANDING` that a `LANDING EXPIRED` released on primary, marked STALE, as today

### Requirement: The three roles
A REGISTER-WRITER role, scoped to the register's tables, the journal and the switch, SHALL be held by `lanes-edit.sh` and its renderer on every writing workstation through the writer login of that workstation's engineer, a member of the role, and that login's own credential pair; REGISTER-WRITER and READER SHALL be groups that cannot log in. The indexer role SHALL keep the ledger half's derived tables and SHALL NOT write the register's; the register-writer SHALL NOT write the ledger half's. A READER role SHALL serve the hooks and the listings, through the reader login of that workstation's engineer, a member of the role.

#### Scenario: Each role outside its scope (real engine)
- **WHEN** each role attempts a write outside its scope
- **THEN** the database refuses it

### Requirement: The REGISTER-WRITER secret's reach is stated plainly
The secret SHALL stay out of every environment a session inherits (Amendment 14(c)). The manual SHALL say that the REGISTER-WRITER secret, the password of the writer login of the workstation's engineer, in a 0600 file, is readable by every lane process on its host, that Rule 9(e) forbids a hand-run SQL write although nothing technical prevents one, and that the exposure matches today's git push rights, and SHALL claim no protection the design lacks.

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
`docs/README-lanes.md` SHALL gain a section, "The SQL register", beside "The derived index (Amendment 14)". It SHALL cover `mode=primary`, the estate switch and `lanes/STORE`, exit 69 and the busy code, the journal and the renderer, which reads may use the mirror, import and rollback, the three roles with their GRANTs, and the socket API; the sections that describe writes and reads SHALL say where `mode=primary` changes them. The section SHALL open with "What changes for a lane": exit 69 and what to do; the busy code, which says that nothing was written and a retry in seconds is safe, so that it is not an outage; exit 3, which on `mode=primary` means the write may not have been written, so the lane runs the printed `LANES_WRITE_ID` rerun, which writes it once or finds it; the guard during an outage and its two ways out; that `commit` and hand edits are refused; the files as a mirror that lags and the verbs that give the truth (`who`, `history`, `lanes`, `lanes-index status`); the claim comment's sequence and how to find its commit; the claim loser's 2 instead of 7; and the later `HOLD` or `LANDING` writer's code.

#### Scenario: The section opens with what changes
- **WHEN** a lane opens "The SQL register"
- **THEN** its first part is "What changes for a lane" and covers each item above

### Requirement: The runbook for acts 3 and 4 and the rollback
The manual SHALL carry a runbook for acts 3 and 4 and the rollback: every act, word, command and owner, in order. It SHALL include the readiness check in each environment, R3b's smoke test, the rollback's triggers, the estate-wide freeze (an estate-wide hold announced on each project's coordination issue and by Brett's prompt to each pane, drained to `PAUSED`, then quiet for 2 minutes, with a cutover and a rollback rehearsed first on a scratch schema), and the posting of "What changes for a lane" on each project's coordination issue before the window. It SHALL propose one bench image rebuild that carries both the re-vendor and the configuration mount, SHALL list the environments that write (the Codex lane's included) and ask Brett to confirm the list, and SHALL make act 4 wait for Amendment 23's slices 2a, 2b and 3. Act 4's steps SHALL run in this order: the drain, the last `PAUSED` write on origin, the ruleset switched on where the plan supports it, the import, the cutover, and the flips. The rollback's SHALL put the ruleset's removal after its render proves the mirror current and before the commit removing `lanes/STORE` is pushed. Switching the ruleset is Brett Heap's act.

> Brett's answer to Q4(a): option (i), #165 6710653292.
> Brett's answer to Q21(b): option (B), #165 6710653292.
> Brett's answer to Q22: option (a), #165 6710653292.
> Brett's answer to Q23: option (a), #165 6710653292.
> Brett's answer to Q24: option (a), #165 6710653292.
> Brett's answer to Q28: option (i), #165 6710653292.

#### Scenario: Act 4's preconditions
- **WHEN** a runbook step opens act 4
- **THEN** the readiness check has passed in every listed environment, the freeze is in place, and slices 2a, 2b and 3 have landed

#### Scenario: The rollback's triggers
- **WHEN** the runbook is read for the rollback
- **THEN** it names the triggers, the words and the owner of each step
