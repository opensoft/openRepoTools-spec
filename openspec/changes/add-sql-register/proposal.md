# Proposal: the SQL register under `lanes-edit.sh` (Amendment 22, act 2)

Governing issue: opensoft/openRepoTools#165, milestone `v1.2.0`, claimed by lane
openRepoTools-3 on 2026-10-05. Sole implementation feature: Speckit
`007-sql-register`, not yet created (see "Speckit handoff"). This change lives in
the spec leg, opensoft/openRepoTools-spec. The code it governs is
opensoft/openRepoTools-code's, named here as `code/…` relative to the assembly
root, with line numbers at the code pin `2a057a6d` (the `v1.1.0` tag). A bare
`#N` below is opensoft/openRepoTools' issue or pull request `#N`.

Status: proposal only. The alignment review, the council debate, `design.md`, the
delta specs and `tasks.md` follow, run across lanes as lane 3's note on #165 of
2026-10-31T18:43Z sets out. The questions under "Open design questions" are for
that design phase; nothing here decides them.

## Why

The lane register is a set of tail-appended text files under git, so the unit of
conflict is a file and every write is a fetch, a rebase and a push that can lose
to another lane's. Two lanes appending Rule 6 lines at once conflict at the end
of `lanes/LANES.md` (#145). On 2026-10-09 all ten writes of two landings failed
that way, and the recovery dropped another lane's line. Rule 1's race is decided
by push order, and Amendment 10's `HOLD` by two clocks that disagree.

Brett Heap ruled on 2026-10-05, in Amendment 22 ("ratify 50"), that on a
workstation configured `mode=primary` the register is a Postgres database: each
write is one transaction, the git files are its rendered mirror, and nothing
falls back to writing the files. Act 2 of that amendment, the tooling, is this
change. It was scheduled for v1.2.0 after the release work; v1.1.0 is tagged, and
Brett started it on 2026-10-31.

## The ruling, the evidence and the start

### Amendment 22, ratified 2026-10-05

- **The text:** brettheap/new-workstation
  `home/.agents/protocols/lane-collision-protocol-amendment-22.md`, governing
  issue brettheap/new-workstation#49, ratified on brettheap/new-workstation#50.
- **Brett Heap's words that started it,** verbatim and in order: "can we keep
  both and then use the sql if available? so sql is the primary and the LANES
  files follow? if we loose network, we will also loose our ai connection so the
  next model turn will also fail and everything mostly stops anyway", then "can
  we draft it now?".
- **His ratification,** verbatim: "ratify 50" (lane openRepoTools-3's log,
  `RULED` 2026-10-05T13:08:56Z). It ratified decisions S1–S6, clauses (a) and
  (b), and the names `mode=primary`, exit 69, `lanes/STORE`, `Register-Seq:`,
  `lanes-index import` and `rollback`.
- **The two arguments it records.** Availability: for an AI-driven lane,
  "register unreachable" and "lane stopped" coincide, so a refusal costs nothing
  that was not already lost, and a fallback write would cost the one truth.
  Ordering: a database transaction orders both workstations' writes strictly,
  with no rebase, and the second writer sees the first before it acts.
- **Its measurement on Eagle, 2026-10-05:** each of a write's three git round
  trips took 1.15–1.28 s, so a write's network floor is about 3.5 s before any
  race. It expects a transaction on a reachable Postgres in tens of
  milliseconds, and says act 2 measures that rather than assuming it.
- **Scope:** only a workstation whose `lanes-index.conf` says `mode=primary`.
  `mode=index` is Amendment 14 unchanged, and no configuration is the files, as
  today.

### Amendment 23, ratified 2026-10-17, moved where the mirror is committed

brettheap/new-workstation `home/.agents/protocols/lane-collision-protocol-amendment-23.md`
(governing issue brettheap/new-workstation#60; Brett Heap's words "ratify 61" at
2026-10-17T20:23:32Z and "apply the edits, 7-9 as recommended" at 20:36:04Z)
gives every lane its own register checkout, `<W>-lanes/<lane>/`, and makes the
workspace checkout `W` read-only. It amends Amendment 22's S2 and nothing else of
it, as Amendment 22's own pointer says: "S2's renderer commits the mirror in the
writing lane's own checkout, never the workspace checkout; S1's transactions and
the SQL mode are unchanged". The renderer takes Amendment 23's S3 checks before
it renders, and on a lost push keeps Amendment 22's discard-and-re-render rule.

### The field evidence of 2026-10-09

Lane openXwallet-2 recorded it on #165 on Brett Heap's word, verbatim: "this …
is another reason to use the sql. note that in OpenRepoTools". In a register
checkout shared by three lanes on one host:

- two Rule 6 landings each issued five `lanes-edit.sh` writes in about 40 s, and
  in both landings all five reported `rebase conflict on origin/main — not
  pushed by this attempt`, because every lane appends at the tail of
  `lanes/LANES.md`;
- a failed write stayed committed, so the next one stacked on it; the checkout
  ended `ahead 6, behind 56`, then `ahead 5, behind 8`;
- one stacked commit was another lane's, so a reset to recover dropped it, and
  that lane replayed it from a backup branch.

The comment's conclusion is this change's: with `mode=primary` writers in one
transaction each and a renderer producing the mirror commit, an append is a row
insert and the conflict disappears, and a SERIALIZABLE claim gives Rule 1 the race
it was meant to have.

### The schedule and the start

- **Scheduled** on Brett Heap's word in lane 3's session on 2026-10-17, verbatim
  "yes, schedule #165 after the release work", then "agreed. lets put in
  v1.2.0" (#165, lane 3's comment of 2026-10-17T22:13Z). The same comment's
  trunk rule, that this work does not merge to `main` until v1.1.0 is tagged, is
  met: `v1.1.0` is tagged on opensoft/openRepoTools-code at `2a057a6d`.
- **Started** on his word in lane 3's session at 2026-10-31T18:38:32Z,
  verbatim: "yes, start 165. have lanes 1, 2 and 4 help and havet then fanout."
  Lane 3 leads under its claim; lanes 1, 2 and 4 research the design questions
  (R3, R1 and R2 below) and sit on the review gates.
- **Not covered by that word:** landing. Each PR lands only on his word (S17,
  as lane 3's note of 2026-10-31 records it). Acts 3 and 4 stay his.

## What Changes

Everything below applies on a `mode=primary` workstation and on no other.
`mode=index` and unconfigured workstations run exactly as today. Each item names
the test that checks it; the agreement tests are listed together at the end.

### The configuration and the estate switch (S1)

- **`mode=` in `lanes-index.conf`.** The parser knows `store`, `url`,
  `passfile`, `reader_url`, `reader_passfile`, `sqlite` and `schema` today
  (`code/lanes-index:280`). It gains `mode=`: `index`, today's behaviour, or
  `primary`. `mode=primary` with SQLite is refused: the register is shared by
  workstations, and SQLite is one disk. The file's 0600 mode, its password file,
  its refusals and "never under `~/.agents/`" stand as Amendment 14(c) wrote
  them.
- **The estate switch,** in two places: a row in the database, and the marker
  `lanes/STORE` in the workspace repository (`primary`, the database's name
  without credentials, the import commit, the UTC). A files-mode writer whose
  fetched tree carries the marker refuses with 69 and "this estate's register is
  SQL; configure `mode=primary`". A `mode=primary` writer whose transaction finds
  the switch not `primary` refuses the same way. So the estate's rule, every
  writing workstation is `mode=primary` or none is, is enforced.
- **Every row carries the mode that wrote it last,** so a mixed history stays
  visible.
- Tests: a files-mode writer refusing under the marker; a `mode=primary`
  writer refusing when the switch is not `primary`; `mode=primary` with SQLite
  refused.

### One transaction per write, and exit 69 (S1)

- **Every register write is one transaction.** The writes are `claim`,
  `release`, `log` (every verb), `set-row-state`, `append-line`, `add-row`,
  `rename-lane`, `retire-rows`, `archive-rows`, `migrate-state-cells`,
  `append-session-id`, `replace-in-row`, `request-handoff`, and the managed
  projection writer (S4). The workstation guard that every writer passes names
  those thirteen verbs and `commit` today (`code/lanes-edit.sh:15103`).
- **`commit`, which publishes a hand edit of the files, refuses** on
  `mode=primary`, with every other direct write to the files (S2). #165's body
  lists `commit` among S1's transactions and also among S2's refusals; this
  proposal follows the amendment, under which it refuses.
- **Unreachable or refused is exit 69** (sysexits' `EX_UNAVAILABLE`), naming the
  store and the error, with nothing written anywhere and never the files
  instead. 69 is free: the exit table (`code/lanes-edit.sh:434`) carries 0–10, 64
  and 66, and no command in the code leg exits 69. The prompt guard, which may
  exit only 0 or 2, refuses with 2, as Amendment 12's D3 already does.
- **Handoffs stay git files** written by the lane itself (Amendment 22, "What
  this amendment does not change"). In Amendment 23's words, "the commits that
  stay git commits everywhere — handoffs, `lane-start`'s stamp, `lane-handoff`'s
  `PAUSED` commit, the skill's commit — follow S1–S5 exactly". Which of those
  acts' writes are register writes, and so go to SQL, is R2's inventory (Q7).
- Tests: refusal with 69 and no file touched when the database is unreachable;
  the same when it refuses the transaction; `commit` refused.

### The write journal and the renderer (S2, as Amendment 23 amends it)

- **The journal.** Each transaction appends a sequence number (commit order),
  the write's commit message exactly as today (`LANES(<lane>@<ws>): …`,
  `LOG(<lane>@<ws>): …`), and the line edits its mirror commit makes. Rule 9(d)'s
  discipline, under which every write's file change is a known, finite set of
  line edits, is what makes it replayable.
- **The renderer.** After the transaction commits, the writer forks it. It
  commits every sequence the mirror lacks, in sequence order, one commit per
  write, with that write's message and a `Register-Seq: <n>` trailer, so the
  workspace repository's `git log` stays the audit trail in commit order. It
  renders `lanes/LANES.md`, `lanes/log/<lane>.md`, `lanes/aliases.tsv` and
  `lanes/archive/`, and touches nothing else.
- **Where it commits.** In the checkout of the lane whose write forked it,
  `<W>-lanes/<lane>/`, never `W`, after Amendment 23's S3 checks (Amendment 23,
  "Relation to Amendment 22").
- **Write-behind.** No writer waits on it beyond the fork. A failed render
  leaves SQL true and the mirror behind; `lanes-index status` says by how many
  sequences, and the next render catches up. A renderer that loses a push race
  discards its unpushed render commits, fetches and renders what is still
  missing onto the new tip: never a rebase of a render.
- **A commit under `lanes/` that the renderer did not make stops it.** It does
  not render over it; `status` reports `diverged at <sha>`; a person's word
  decides whether that write is imported into SQL or reverted.
- **`lanes-index status`** adds the mirror's lag and any divergence, and
  **`reconcile`** becomes the proof that the mirror equals the SQL: it
  re-renders what is missing and imports nothing silently (Amendment 22,
  "Relation to Amendment 14").
- Tests: the mirror byte-identical to a full render from SQL after every write;
  two renderers racing produce one commit per sequence, in order; a stray commit
  under `lanes/` stops the renderer.

### Readers fail closed (S3)

- **Every act and every guard reads the database,** inside the act's
  transaction where the act writes: `who`, `binding`, `live-holder`,
  `next-free`, `managed-projection`, the guard, `lane`, `lane-start`,
  `lane-end`, `lane-handoff` and `lane-rename`. A read that fails refuses with
  69, or 2 in the guard.
- **Reads that decide nothing may read the mirror** and say so: the
  `SessionStart` block and the launcher's bare window reads (Amendment 8,
  offline, always exit 0), and the listings `lanes` and `history` when the
  database is unreachable, with this line:
  `read: mirror at <sha12>, <k> sequences behind or unknown (sql unreachable: <why>)`.
- **A reader says which store answered** whenever it is not that workstation's
  default. On `mode=primary`, `--index` changes nothing: the default read is
  already the database.
- Tests: each state-deciding reader refuses with 69 (the guard with 2) when the
  database is unreachable; each mirror read prints its line and keeps its exit.

### The managed seam (S4)

- An enrolled lane's truth stays its local JSON ledger under flock (the ruling of
  2026-10-04; the 001 feature's T058). Its projection into the register row is
  written by the ledger's own projection writer, through the same storage layer
  as every register write, so on `mode=primary` it lands in SQL first.
- `managed-projection` (`code/lanes-edit.sh:18467`) answers 0, 8 or 1 from the
  database; an unreachable database is 1, UNKNOWN, never `legacy`.
- Tests: valid, malformed and legacy rows answer 0, 1 and 8 from SQL; an
  unreachable database answers 1.

### Import and rollback (S5)

- **`lanes-index import --at <commit>`** reads the workspace repository at a
  named commit, loads the register's tables and the journal's first sequence,
  renders the mirror from SQL into a scratch tree, and refuses unless
  `git diff --quiet` holds between the commit's `lanes/` and the render.
- **The cutover**, in one window Brett Heap names (act 4): set the database
  switch to `primary`; push one commit adding `lanes/STORE` as a
  fast-forward of the imported commit, aborting if `main` moved under `lanes/`
  since (the import is then re-run at the new tip); flip `mode=primary` on the
  workstations act 3 configured.
- **`lanes-index rollback`** is the reverse: switch to `files`, so every SQL
  write refuses from that transaction on; render until `status` shows the
  mirror current and byte-identical to a full render; push one commit removing
  `lanes/STORE`; clear `mode=primary` on every workstation. With the database
  unreachable it proceeds only from a mirror `status` last showed current.
- Tests: the import's byte-identical proof, and its refusal on a difference; the
  cutover's abort when `main` moved under `lanes/`; rollback leaves the files
  byte-identical, the marker gone and SQL writes refused; rollback refused with
  the database unreachable and the mirror not last shown current.

### Claim order is commit order (S6)

- Rule 1's race is one transaction: the pre-check of the object's holders and
  the `CLAIMED` insert run SERIALIZABLE, or under a lock on the object. Of two
  claimants one commits, and the other sees it and exits 2, writing nothing.
  `CLAIM-LOST` stays in the grammar for the history that has it and is no longer
  produced.
- Amendment 10's `HOLD` and `LANDING` lines on one repository's `main` are
  ordered by commit sequence: the earlier commit wins, and the later writer's
  transaction reports the earlier line to it before it acts. The line still
  comes before the act that moves `main`.
- UTC is still recorded on every line, from the database's clock, so Rule 1's
  four hours and Rule 6's thirty minutes are measured on one clock. It decides
  nothing but durations (Amendment 7(b)).
- Tests: two concurrent claimants give one `CLAIMED` and one exit 2; two `HOLD`s
  land in commit order.

### Roles (clause (b))

- A **REGISTER-WRITER** role, scoped to the register's tables, the journal and
  the switch, is held by `lanes-edit.sh` and its renderer on every writing
  workstation. It is the one change to Amendment 14's "no session holds a
  writer role".
- Amendment 14's **indexer** role keeps the ledger half's derived tables and
  cannot write the register's; the register-writer cannot write the ledger
  half's.
- The **READER** role serves the hooks and the listings.
- Tests: each role refused outside its scope.

### The register service, and the guard as its client

Folded into #165 on Brett Heap's word of 2026-10-05, "fold the guard into 165",
which answered his question "is the guard running in the model cli? if so, can
we move this to LS?". Today `openRepoTools --install` writes `lanes-edit.sh
guard` as a `UserPromptSubmit` hook, so every prompt forks the helper (18,977
lines at the pin) and reads the register and tmux; under Amendment 22 it would
also open a database connection per prompt.

- **`lanes-register`,** this change's storage layer served over a local socket:
  one process per workstation holds the database connection and the credential
  (one holder instead of every session) and the parsed register, and answers the
  reads every act and guard makes (`who`, `binding`, `live-holder`, `next-free`,
  `managed-projection`, the guard's triple check) in milliseconds. It is the
  natural home for S2's renderer and Amendment 14's `lanes-index sync`. It starts
  on demand and needs no supervisor of its own.
- **The `UserPromptSubmit` guard becomes a thin client.** It sends the session
  uuid, the window id, the session name and the workstation; it gets `ok`,
  `refuse <reason>` or `offer <the three A12 Addendum 1 choices>`; it exits 0
  or 2. The hook stays, because the harness's exit code is the only thing that
  refuses a prompt, but it no longer forks bash or reads files. The
  `CLAUDE_NO_LANE=1` exception of `skip-no-lane-guard` (exact value, exit 0
  before any input or identity probe) is kept.
- **The `SessionStart` block becomes a client the same way.** It is read-only,
  still exits 0 always, and still reads offline through the mirror.
- **With the service unavailable,** the hook runs the direct read itself
  (`lanes-edit.sh guard`, as today). That is a READ fallback to the same truth,
  which Amendment 22 permits; writes never fall back (S1), and an unreadable
  source still refuses (Amendment 12's D3).
- **The socket API is documented** in `docs/README-lanes.md`. It is the surface
  the 001 feature's lanes service (T055) adopts or wraps, so Codex and the
  managed path ask the same question of the same process.
- Tests: the guard's answer through the service byte-identical to the direct
  read for `ok`, `refuse` and `offer`; with the service down, the hook falls back
  and says `guard: direct (service unavailable)` on stderr, with the same exit
  code; the hook's environment carries no database URL or password; the
  service-backed guard answers in under 50 ms on the suite's fixture register,
  recorded in the PR body.

### The agreement tests

Amendment 22's act 2 names them, and #165 adds the service's. None runs against a
real shared database; the fixture is Q1's to decide.

1. The mirror byte-identical to a full render from SQL after every write.
2. Refusal, 69 and no file touched, when the database is unreachable.
3. A files-mode writer refusing under the marker.
4. Two concurrent writers: one `CLAIMED`, the other exit 2; two `HOLD`s in commit
   order.
5. Two renderers racing: one commit per sequence, in order.
6. A stray commit under `lanes/` stopping the renderer.
7. The import's byte-identical proof, and its refusal on a difference.
8. Rollback: files byte-identical, marker gone, SQL writes refused.
9. The measured cost of one write, recorded in the PR body.
10. The guard through the service: byte-identical answers, the fallback line, no
    credential in the hook's environment, and under 50 ms.

### The manual

- `docs/README-lanes.md` (this leg) gains a section, "The SQL register":
  `mode=primary`, the estate switch and `lanes/STORE`, exit 69, the journal and
  the renderer, which reads may use the mirror, import and rollback, the three
  roles with their GRANTs, and the socket API.
- The new section sits beside "The derived index (Amendment 14)"
  (`docs/README-lanes.md:3647`), and the sections that describe writes and
  reads say where `mode=primary` changes them.

## Not in this change

- **Act 3, Brett Heap's:** Eagle and Raven configured together, with
  `mode=primary`, `store=postgres`, the register-writer and reader roles, and act
  2 installed on both. Not done: at 2026-10-31T18:51Z the bench this was drafted
  on had no `lanes-index.conf` and `LANES_INDEX_CONFIG` unset, as Amendment 23
  measured on Eagle on 2026-10-17.
- **Act 4, Brett Heap's:** the cutover in the window he names, and rollback the
  same way. This change builds `import` and `rollback` and proves them on
  fixtures. Running them on opensoft/brett-wip is act 4; its `main`
  (`527f3c7b1fd6` at 2026-10-31T18:51Z) carries no `lanes/STORE`.
- **Amendment 23's act 2,** lane openRepoShape-2's, folding #198 and #197: the
  per-lane checkout's resolver and creation (its S1), every writer through it
  (S2), the fetch, refusals and fast-forward before an edit (S3), the
  pathspec commit, push, read-back and replay after it (S4; `commit_push`
  becoming `commit_publish`), the read-side verb that fast-forwards `W` and
  `lane-start`'s two fast-forwards (S5), and its sweep (S6). This change uses
  them for the renderer and builds none of them (see "Dependencies").
- **#181's two `UserPromptSubmit` guard checks** and the guard's ignored-size
  threshold, from #163's fold. #163 runs them "through the register service of
  #165 once it exists, directly until then": this change builds the service, and
  #181 builds the checks.
- **Amendment 14's index half:** the managed ledger and the 001 feature's T058
  tables (`swap_states`, `job_summaries`, `ledger:<Workstation>`), and #161's
  `worktrees` table under `inventory:<Workstation>`, stay derived on every
  workstation.
- **The 001 feature adopting the socket API** (T055, on opensoft/openRepoTools'
  branch `001-separate-swap-ctx-handoff` at `3c26041`), and telling the Codex
  lane, which goes through Brett.
- **#145 on a files-mode estate.** S6 removes the end-of-file conflict only where
  the register is SQL.
- **The protocol texts.** Amendments 22 and 23 live in brettheap/new-workstation;
  this change adds no rule and edits no amendment.

## Dependencies

### Landed

- **#160**, Amendment 14's act 2, by PR #164 (opensoft/openRepoTools-code
  `6760edc`, 2026-10-05): `lanes-index` with `sync`, `reconcile` and `status`;
  the configuration and its two roles; the versioned schema
  (`SCHEMA_VERSION = 1`, `code/lanes-index:101`) with the `owner` column read
  from `managed-projection`; a Postgres path reached only through `psql`, with
  no driver (`code/lanes-index:1273`); and the detached post-write nudge
  (`code/lanes-edit.sh:1263`).
- **#161** by PR #171 (`af25d6d`, 2026-10-06): the `worktrees` table.
- **#162** by PRs #168 and #169 (`3332694`, `33891a5`, 2026-10-06), with #174
  and #179 after them: `lane-worktrees sweep` and its daily report.
- **The triad cutover (#186):** the assembly root `2a99dd4` pins the code leg at
  `2a057a6d` and the spec leg at `64a1a989`; this change is cut from the spec
  leg's `main`, `9f8b21a2`.
- **v1.1.0,** tagged on opensoft/openRepoTools-code at `2a057a6d` (tag object
  `36d8d514`).

### Not landed: Amendment 23's act 2

- It is six draft PRs by lane openRepoShape-2 on opensoft/openRepoTools, stacked
  on one another and last updated between 2026-10-20 and 2026-10-26: #282 (slice
  1a, routing), #307 (1b, the guard and publish), #312 (2a, the handoff verbs),
  #316 (2b, `lane-start` and `lane-handoff`), #311 (3, the skill) and #308 (4,
  `lane-end`'s sweep).
- They were opened before the cutover. opensoft/openRepoTools' `main` (`2a99dd4`)
  no longer carries `lanes-edit.sh` or `lanes-index`; the code is in
  opensoft/openRepoTools-code, which has no open PR. At the code pin
  `commit_push` exists (`code/lanes-edit.sh:2292`) and `commit_publish` does not.
- Amendment 22's S2, as Amendment 23 amends it, puts the renderer in the writing
  lane's checkout under Amendment 23's S3 checks. So the renderer's git half
  depends on that act landing, or on this change carrying its own form of those
  parts. Q6 asks which.
- #198 is open, and folded into that act. #197 was closed on 2026-10-17.

### Order of landing (the triad)

Every PR below lands only on Brett Heap's word.

1. This change, by PR in opensoft/openRepoTools-spec, once its gates and
   `design.md` are done and the handoff names `007-sql-register`.
2. The Speckit feature `007-sql-register`, created by `/speckit.specify` from an
   assembly-root clone, with its worktrees at
   `worktrees/007-sql-register/{spec,code}/`.
3. The code, by PR in opensoft/openRepoTools-code; the feature's spec files and
   `docs/README-lanes.md`, by PR in opensoft/openRepoTools-spec.
4. The root pin bump last, moving both legs' gitlinks, pins and workflow `@<sha>`
   together; then this change is archived.
5. Acts 3 and 4, on his word.

## Open design questions

Questions, not decisions. The lanes' research of 2026-10-31 feeds them: R1 (lane
openRepoTools-2, the test strategy), R2 (lane openRepoTools-4, the writer and
reader inventory) and R3 (lane openRepoTools-1, the schema, the cutover and the
landing path).

1. **The test strategy (R1).** Which fixture proves the agreement tests: a
   disposable local `postgres` per suite run, a GitHub Actions `services:
   postgres` container, a fake libpq or `psql` that proves the SQL's shape, or a
   mix? What stands today: `code/tests/test_lanes_index.py` uses SQLite under
   `tmp_path` and a fake `psql` ("NO REAL DATABASE AND NO NETWORK"), and skips
   nothing on a missing database; `code/.github/workflows/tests.yml` runs four
   jobs on `ubuntu-24.04` with no service and no Postgres. Can a fake prove S6's
   two concurrent claimants and the two-renderer race? What do py-bench,
   cloud-bench and the CI runners provide? May any case skip where no Postgres
   exists? The `tests` job accepts a skip that names its reason, and the
   `package` job fails when any of its three test modules skips one. What does a
   cold start cost, and one write?
2. **The register service or a connection per process (R2).** Does
   `lanes-register` hold the only connection and credential, with every writer
   and reader as its client, or does each `lanes-edit.sh` write open its own
   transaction and the service serve only the reads and the guard? With the
   service down, may a write open its own connection (still SQL, so no store
   fallback), or does it refuse with 69? Is it one process per workstation or per
   bench container? Containers on one host share the projects mount (Amendment
   23's S1), but on 2026-10-23 lanes found that py-bench and cloud-bench do not
   share `/tmp`. Where does the socket live, and how does it know its clients?
   Does it run on files-mode and `mode=index` workstations too, serving the
   guard's file reads, or only on `mode=primary`? Is it a new installable (there
   are seventeen, `code/openRepoTools:1292`) or a subcommand of one?
3. **The schema's relation to #160's (R3).** Does the register live in #160's
   store and schema, moving `SCHEMA_VERSION` from 1 to 2, or in a schema of its
   own with its own version? #160's tables are derived records keyed by (source,
   record id) and versioned by a content digest, with `owner` filled from
   `managed-projection`. When they become the truth, do they keep that keying,
   and how are the journal and the switch stored? Do the indexer and
   register-writer roles split by schema or by table GRANT? What do `sync` and
   `reconcile` do on a primary workstation? Does a writer that needs SERIALIZABLE
   retries keep #160's `psql`-only path?
4. **Import and rollback against opensoft/brett-wip (R3).** Which checkout runs
   the import and pushes the one `lanes/STORE` commit, now that Amendment 23 makes
   `W` read-only and gives each lane its own checkout? How long is the window
   between import and cutover on a busy estate, given that the cutover aborts when
   `main` moves under `lanes/`? Where is "the mirror was last shown current"
   recorded, so a rollback with the database unreachable can prove it? Is the
   cutover a step of `import` or a command of its own? How do a
   lane checkout holding unpublished commits (Amendment 23's S6.3) and #198's
   fail-closed writers meet the cutover commit? What, exactly, do acts 3 and 4
   need from Brett?
5. **The renderer's home.** Is it started by each writer after its commit, as
   Amendment 22's S2 says, or run inside `lanes-register`, which the fold calls
   its natural home? With renderers in several lanes' checkouts on several
   workstations, how do they converge on one commit per sequence, and in what
   form is a journal entry's line edits stored so that it replays byte-identically?
6. **The order against Amendment 23's act 2.** Does this change build on that
   act's `commit_publish` and `<W>-lanes/<lane>/` after its six PRs are re-homed
   to opensoft/openRepoTools-code and land, or carry only what the renderer needs?
   Who re-homes those PRs, and does this change wait for them? That is for Brett
   and lane openRepoShape-2, with lane 3.
7. **How a writer runs one transaction (R2).** Through `psql` with one
   `BEGIN … COMMIT` session and `ON_ERROR_STOP`, through a `lanes-index`
   subcommand, or through the service? Where do each verb's existing checks run
   (the one-match guard, the `numstat` refusal, Amendment 7(f)'s rescan): in SQL,
   or on a rendered image inside the transaction? Which writes in `lane-start`,
   `lane-handoff` and the handoff skill are register writes, and which are handoff
   commits that stay git?
8. **`lane-start` with the database unreachable.** Amendment 22's S3 makes
   `lane-start` a reader that refuses with 69, while Amendment 8(d) and Amendment
   23's S3 say a stamp it cannot write never blocks the launch. Which of its reads
   decide state and refuse the launch, and which of its writes only print a note?
9. **Exit codes.** 69 joins `lanes-edit.sh`'s table. Which code does each reader
   return on `mode=primary` where a read fails with 1 or 10 today? S4 keeps
   `managed-projection`'s 1 for an unreachable database where S3 gives the other
   readers 69: are there other such contracts? Does `lanes-index`, whose codes
   are 0, 1, 2, 3 and 64, use 69 too? How do the callers that branch on today's
   codes (`lane-start`, `lane-end`, `lane`, `lane-handoff`) treat it?
10. **The measurements.** On which host and against which Postgres, local or
    remote, is "the measured cost of one write" taken, and the guard's 50 ms
    through the service?

## Speckit handoff

- **The one feature:** `007-sql-register`, branch `007-sql-register` in both legs,
  feature directory `specs/007-sql-register/` in the spec leg, tasks at
  `specs/007-sql-register/tasks.md`.
- **The number is free.** Checked between 2026-10-31T18:56:59Z and 18:57:32Z:
  `specs/` on the spec leg's `main` (`9f8b21a2`) holds 002 to 006, ending at
  `006-release-pypi`; no path naming `007` exists on the `main` of the spec leg,
  the code leg (`2a057a6d`) or the root (`2a99dd4`); and no branch name of the
  spec leg (four besides `main`), of the code leg (three besides `main`) or of
  the root contains `007`. `/speckit.specify` checks again when it creates the
  feature.
- **Not created yet.** It is created after this change's gates and `design.md`,
  and this section then records its branch and tasks path as created.

## Capabilities

### New Capabilities

- `sql-register`: the `mode=primary` register: the configuration and the estate
  switch, one transaction per write and exit 69, the write journal and the
  renderer, readers that fail closed and the reads that may use the mirror, the
  managed seam, import and rollback, claim order by commit, the roles, and the
  agreement tests.
- `register-service`: the `lanes-register` local-socket service, its read API,
  the `UserPromptSubmit` guard and the `SessionStart` block as its clients, and
  the guard's read fallback when the service is unavailable.

### Modified Capabilities

None. `openspec list --specs` reports no canonical capability on the spec leg's
`main`. The guard's `CLAUDE_NO_LANE=1` exception (`profile-only-lane-guard`, in the
change `skip-no-lane-guard`, not yet archived) is kept unchanged, not modified.

## Impact

- **The code leg,** opensoft/openRepoTools-code:
  - `code/lanes-edit.sh`: every writing verb on `mode=primary`; the exit table
    gains 69; the readers (`who` at `:16751`, `guard` at `:16856`,
    `live-holder` at `:16916`, `binding` at `:17030`, `history` at `:17512`,
    `lanes` at `:17765`, `next-free` at `:17869`, `managed-projection` at
    `:18467`); the `SessionStart` block; `commit`'s refusal; the starting of the
    renderer.
  - `code/lanes-index`: `mode=` in the configuration, the register's tables, the
    journal and the switch, `import --at` and `rollback`, and `status` and
    `reconcile` for the mirror.
  - `code/lane-start`, `code/lane-handoff`, `code/lane-end`, `code/lane`,
    `code/lane-rename` and `code/lanes`: their reads under S3.
  - `lanes-register`, new in a form Q2 decides; `code/openRepoTools`'s
    `INSTALLABLES` and the hook entries `--install` merges, with #237's gate on
    them, if the service or the guard's command changes what is placed.
  - Tests: new agreement tests; `code/tests/test_lanes_index.py`;
    `code/tests/test_lane_helpers.sh`; `code/tests/test_repo_hygiene.py`, whose
    rules read the exit table and `commit_push`; `code/tests/test_package.py`, if
    the payload changes; and `code/.github/workflows/tests.yml` if Q1 picks a
    services container.
- **The spec leg,** opensoft/openRepoTools-spec: this change;
  `specs/007-sql-register/`; `docs/README-lanes.md`'s new section and the notes
  beside its existing ones.
- **The assembly root,** opensoft/openRepoTools: the pin bump only.
- **Governed files:** `openspec/` text, `.github/workflows/`, and every file that
  records a pin, a digest or a measured value (the protocol's "Governed
  invariants").
- **Outside the repositories,** all at acts 3 and 4: a Postgres database and its
  roles; `mode=primary` in `lanes-index.conf` on Eagle and Raven; `lanes/STORE`
  in opensoft/brett-wip.
- **Unchanged:** every `mode=index` and unconfigured workstation, byte for byte;
  the ledger half; the handoffs; Rules 1–10 as rules; Amendment 7's grammar.
