# Proposal: the SQL register under `lanes-edit.sh` (Amendment 22, act 2)

Governing issue: opensoft/openRepoTools#165, milestone `v1.2.0`, claimed by lane
openRepoTools-3 on 2026-10-05. Sole implementation feature: Speckit
`007-sql-register`, not yet created (see "Speckit handoff"). This change lives in
the spec leg, opensoft/openRepoTools-spec. The code it governs is
opensoft/openRepoTools-code's, named here as `code/…` relative to the assembly
root, with line numbers at the code pin `2a057a6d` (the `v1.1.0` tag). A bare
`#N` below is opensoft/openRepoTools' issue or pull request `#N`.

Status: proposal, with the alignment review's fixes applied. `alignment-review.md`
beside this file records each finding of the two seats (lane openRepoTools-4's
Stack Architect, #165 comment 6548171116; lane openRepoTools-2's QA Lead, comment
6548063141) and what became of it. The council debate, `design.md`, the delta
specs and `tasks.md` follow, run across lanes as lane 3's note on #165 of
2026-10-31T18:43Z sets out. The questions under "Open design questions" are for
that design phase or for Brett Heap; nothing here decides them. R1 and R2 are in;
R3 (the schema, the cutover and the landing) is not yet.

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

Everything below applies on a `mode=primary` workstation, except the estate
marker. Every act-2 writer, in every mode, reads `lanes/STORE` and refuses with 69
where the marker is present and the workstation is not `mode=primary` (Amendment
22's S1: "Every act-2 writer reads it"). Where the marker is absent, `mode=index`
and unconfigured workstations write and read as today. Each item names the test
that checks it; the agreement tests are listed together at the end.

### The configuration and the estate switch (S1)

- **`mode=` in `lanes-index.conf`.** The parser knows `store`, `url`,
  `passfile`, `reader_url`, `reader_passfile`, `sqlite` and `schema` today
  (`code/lanes-index:280`), and refuses any other key with exit 2 (`:289`). It
  gains `mode=` (`index`, the default when absent and today's behaviour, or
  `primary`) and the REGISTER-WRITER's own pair, for example `register_url=` and
  `register_passfile=`: today there are two roles, `url=` ("the WRITER role —
  sync and reconcile", `docs/README-lanes.md:3752`) and `reader_url=`
  (`code/lanes-index:1280–1295`), and under clause (b) the indexer's `url=` must
  not write the register's tables. `lanes-index` stays the one parser:
  `lanes-edit.sh`, which reaches the index only through the `lanes-index`
  executable today (`code/lanes-edit.sh:2554–2568`), asks it, or the service,
  for the mode, and never parses the file in bash. A `lanes-index` older than
  this change refuses `mode=` with exit 2, so act 3 installs this change before
  `mode=` is written anywhere. `mode=primary` with SQLite is refused: the
  register is shared by workstations, and SQLite is one disk. The file's 0600
  mode, its password file, its refusals and "never under `~/.agents/`" stand as
  Amendment 14(c) wrote them.
- **The estate switch,** in two places: a row in the database, and the marker
  `lanes/STORE` in the workspace repository (`primary`, the database's name
  without credentials, the import commit, the UTC). A files-mode writer whose
  fetched tree carries the marker refuses with 69 and "this estate's register is
  SQL; configure `mode=primary`". A `mode=primary` writer whose transaction finds
  the switch not `primary` refuses the same way. So the estate's rule, every
  writing workstation is `mode=primary` or none is, is enforced. At the pin,
  `set-row-state`, `replace-in-row`, `append-session-id`, `append-line` and
  `commit` make no fetch before they edit (R2), so a files-mode writer has no
  fetched tree to find the marker in before its local commit exists. Where that
  check sits, built here or on Amendment 23's act 2, is Q6's.
- **Every row carries the mode that wrote it last,** so a mixed history stays
  visible.
- Tests: a files-mode writer refusing under the marker; a `mode=primary`
  writer refusing when the switch is not `primary`; `mode=primary` with SQLite
  refused; after each of S1's writes, every row it touched reads back
  `primary` as the mode that wrote it last.

### One transaction per write, and exit 69 (S1)

- **Every register write is one transaction.** The writes are `claim`,
  `release`, `log` (every verb), `set-row-state`, `append-line`, `add-row`,
  `rename-lane`, `retire-rows`, `archive-rows`, `migrate-state-cells`,
  `append-session-id`, `replace-in-row`, `request-handoff`, and the managed
  projection writer (S4). The workstation guard that every writer passes names
  those thirteen verbs and `commit` today (`code/lanes-edit.sh:15103`).
- **`commit` has two halves at the pin** (`code/lanes-edit.sh:15485–15515` and
  `:2330–2352`):
  - the hand-edit commit of Rule 9(d), which refuses on `mode=primary` with every
    other direct write to the files (Amendment 22's S2). #165's body lists
    `commit` among S1's transactions and also among S2's refusals; this proposal
    follows the amendment, under which the hand-edit commit refuses;
  - #194's publish of queued commits (`:15512–15513`, fenced by
    `cp_publish_fence` at `:2174`), which Amendment 23's S4 keeps as "the
    publish act" for the commits that stay git commits. On `mode=primary` it
    stays, with its fence narrowed to `handoffs/`; a queued commit under `lanes/`
    is a divergence and is refused.
- **The other direct writes that refuse** on `mode=primary`: the pre-existing and
  concurrent captures (`:2701–2721`, `:2394–2398`), `add-row`'s compensating
  commit (`:3908–3912`), and the branch-wide push (`:2410`, `:2432`), which
  publishes any queued local commit. Which code they refuse with is Q9's.
- **Unreachable or refused is exit 69** (sysexits' `EX_UNAVAILABLE`), naming the
  store and the error, with nothing written anywhere and never the files
  instead. 69 is free: the exit table (`code/lanes-edit.sh:434`) carries 0–10, 64
  and 66, and no command in the code leg exits 69. The prompt guard, which may
  exit only 0 or 2, refuses with 2, as Amendment 12's D3 already does.
- **A serialization failure is not a refusal.** SQLSTATE 40001 (and a deadlock,
  40P01) is how SERIALIZABLE tells the later of two writers that the earlier one
  committed: R1 reproduced it on PostgreSQL 16.15, where the second writer
  blocked about a second and then failed, and its `psql` exited 3. The
  transaction re-runs from its reads, so S6's losing claimant sees the winner
  and exits 2. Exit 69 stays for an unreachable database, any other refusal,
  and a retry bound spent; the bound is Q15.
- **A COMMIT whose outcome the writer cannot learn** (the connection lost after
  COMMIT was sent) is neither a success nor "nothing written": the row may have
  committed with no render forked. Amendment 23's S4 gives `lanes-edit.sh`'s
  exit 3 to the git analogue, "cannot confirm whether it also reached origin".
  How this case is reported and recovered is Q16.
- **Handoffs stay git files** written by the lane itself (Amendment 22, "What
  this amendment does not change"). In Amendment 23's words, "the commits that
  stay git commits everywhere — handoffs, `lane-start`'s stamp, `lane-handoff`'s
  `PAUSED` commit, the skill's commit — follow S1–S5 exactly". R2 found that the
  lifecycle scripts make every register write through a `lanes-edit.sh` verb (17
  call sites in `lane-start`, `lane-handoff`, `lane-end`, `lane` and
  `lane-rename`), and that the git commits are the three handoff blocks:
  `lane-start`'s stamp (`code/lane-start:4412–4442`), `lane-handoff`'s commit
  (`code/lane-handoff:2673–2684`) and the skill's (`code/skills/handoff/SKILL.md:424–457`).
  Each runs `pull --rebase` and a push with no lock, so on `mode=primary` each
  would rebase and publish the renderer's unpushed commits (Q6).
- Tests: for each of S1's writes, the projection writer's storage-layer entry
  included (S4), refusal with 69 when the database is unreachable and when it
  refuses the transaction, with the register's rows, the journal and git
  unchanged and no lock left; the same for a failure partway through the
  transaction; a hand edit through `commit` refused; a queued handoff commit
  still published; a queued `lanes/` commit refused; a SERIALIZABLE conflict
  retried, not reported as 69.

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
- **Commits that touch none of the paths it renders are part of the tip it
  renders onto.** Amendment 22's S2 says it of handoffs: "Commits that touch
  nothing under `lanes/` (handoffs, manifests) are simply part of the tip it
  renders onto." The same holds for the files under `lanes/` it never renders:
  the cutover's commit adding `lanes/STORE` and rollback's removing it (S5), and
  `lanes/repos.tsv`, the alias override a person keeps there
  (`docs/README-lanes.md:74`, `:685`). Where `repos.tsv` lives on a primary
  estate is Q18's.
- **A commit that changes a path the renderer renders** (`lanes/LANES.md`,
  `lanes/log/<lane>.md`, `lanes/aliases.tsv`, `lanes/archive/`) **and that the
  renderer did not make stops it.** It does not render over it; `status`
  reports `diverged at <sha>`; a person's word decides whether that write is
  imported into SQL or reverted.
- **`lanes-index status`** adds the mirror's lag and any divergence, and
  **`reconcile`** becomes the proof that the mirror equals the SQL: it
  re-renders what is missing and imports nothing silently (Amendment 22,
  "Relation to Amendment 14").
- Tests: the mirror byte-identical to a full render from SQL after each of S1's
  writes; two renderers racing produce one commit per sequence, in order; a
  stray commit to a rendered path stops the renderer, while a cutover commit, a
  `repos.tsv` edit and a handoff commit between renders do not; a hung or
  failing renderer delays no writer beyond the fork; a failed render shows its
  lag in `status`, and the next render catches up in order; `status` prints
  `diverged at <sha>` and `reconcile` imports nothing; a render refused by
  Amendment 23's S3 checks leaves SQL true and is reported.

### Readers fail closed (S3)

- **Every act and every guard reads the database,** inside the act's
  transaction where the act writes: `who`, `binding`, `live-holder`,
  `next-free`, `managed-projection`, the guard, `lane`, `lane-start`,
  `lane-end`, `lane-handoff` and `lane-rename`. A read that fails refuses with
  69, or 2 in the guard, except where the table below says otherwise.
- **Reads that decide nothing may read the mirror** and say so: the
  `SessionStart` block and the launcher's bare window reads (Amendment 8,
  offline, always exit 0), and the listings `lanes` and `history` when the
  database is unreachable, with this line on stderr, the third of the `read:`
  family beside `read: index (…)` and `read: sources (…)` (`code/lanes-edit.sh:2569–2573`:
  "Both lines go to stderr: stdout is the read's own"):
  `read: mirror at <sha12>, <k> sequences behind or unknown (sql unreachable: <why>)`.
  S3's test is whether the read decides: where a caller acts on a listing's
  answer, as `lane` and `lane-end --retire-dormant` act on `lanes`' (R2), that
  read decides.
- **A reader says which store answered** whenever it is not that workstation's
  default. On `mode=primary`, `--index` changes nothing and prints no
  `read: index` line: the default read is already the database.
- **The readers' exits, the tests' oracle.** Where the database answers, each
  reader's codes are its own today (`code/lanes-edit.sh:434`); the table adds
  what happens where it does not.

  | Reader | Database unreachable, or the read refused | Source |
  |---|---|---|
  | `who`, `binding`, `live-holder`, `next-free`, `lane`, `lane-end`, `lane-rename` | 69; no answer on stdout | S3 |
  | the `UserPromptSubmit` guard | 2 | S1, S3; Amendment 12's D3 |
  | `managed-projection` | 1, UNKNOWN, never 8 | S4 |
  | `lane-start` | per Q8: refuse 69 before the launch, or launch with a note | S3 against Amendment 8(d) and Amendment 23's S3 |
  | `lane-handoff` and the handoff skill | per Q8 | S3 against R-A11-11 |
  | the listings `lanes` and `history` | the mirror's answer, the `read: mirror` line, and the listing's own exit | S3 |
  | the `SessionStart` block and the launcher's bare window reads | the mirror, offline; exit 0 | S3; Amendment 8 |

- Tests: each row of the table, for each reader in it, with the rows marked
  "per Q8" written once Q8 is answered; each mirror read prints its line on
  stderr and keeps its exit.

### The managed seam (S4)

- An enrolled lane's truth stays its local JSON ledger under flock (the ruling of
  2026-10-04; the 001 feature's T058). Its projection into the register row is
  written by the ledger's own projection writer, through the same storage layer
  as every register write, so on `mode=primary` it lands in SQL first.
- **The managed projection writer is not in the code leg at `2a057a6d`.** Only
  its reader is, ported from `3c26041` "VERBATIM: not one byte of it differs, so
  that when that branch's T024 lands, the merge of the two is 'keep either'"
  (`code/lanes-edit.sh:13178–13183`). The writer arrives with the 001 feature's
  T024, so this change provides the storage-layer entry it will call and tests
  that entry with a fake.
- `managed-projection` (`code/lanes-edit.sh:18467`) answers 0, 8 or 1 from the
  database; an unreachable database is 1, UNKNOWN, never `legacy`. The
  database-backed answer wraps `lane_is_managed_owned` (`:13324`) from outside
  the verbatim block (`:13188–13312`), which stays byte-identical.
- Tests: valid, malformed and legacy rows answer 0, 1 and 8 from SQL; an
  unreachable database answers 1; the verbatim block's empty diff against
  `3c26041` still holds; the storage-layer entry for the projection writer,
  driven by a fake writer, lands in SQL first.

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
- Tests: the import's byte-identical proof, and its refusal on a difference; a
  successful cutover: the switch set to `primary`, one fast-forward commit adding
  `lanes/STORE` with its four fields and no credential, and the next write going
  to SQL; the cutover's abort when `main` moved under `lanes/`; rollback leaves
  the files byte-identical, the marker gone and SQL writes refused; rollback with
  the database unreachable proceeding from a mirror last shown current, and
  refused where the mirror was not last shown current.

### Claim order is commit order (S6)

- Rule 1's race is one transaction: the pre-check of the object's holders and
  the `CLAIMED` insert run SERIALIZABLE, or under a lock on the object. Of two
  claimants one commits, and the other sees it and exits 2, writing nothing.
  Under SERIALIZABLE the loser's first attempt fails with 40001 and re-runs from
  the pre-check (S1, "A serialization failure is not a refusal"); whether S6
  uses that or a lock on the object, and the retry bound, are Q15's. Whether the
  loser's 2 stands against the exit table's 7 is Q12. `CLAIM-LOST` stays in the
  grammar for the history that has it and is no longer produced. Today the race
  is decided by push order: a losing claim's `CLAIMED` commit is rebased, a
  `CLAIM-LOST` is stacked on it, and both are pushed together
  (`code/lanes-edit.sh:16667–16682`, R2).
- Amendment 10's `HOLD` and `LANDING` lines on one repository's `main` are
  ordered by commit sequence: the earlier commit wins, and the later writer's
  transaction reports the earlier line to it before it acts. The line still
  comes before the act that moves `main`. **This is new behaviour, not a
  port.** At the pin, no writer reads another lane's open `LANDING` or `HOLD`
  before it writes: `HOLD` lines are free text through `append-line`, whose arm
  has no content pre-check (`code/lanes-edit.sh:15326–15383`), the merge hold is
  decided from LANES.md's Rule 6 lines (`:6423`), and only `lanes-index`'s
  `REGISTER_KIND_RE` knows the `HOLD` line's shape (`code/lanes-index:680`). The
  report needs `append-line` to recognise the four Rule 6 and Amendment 10
  shapes, or a verb of their own; which, and what the later writer then sees,
  are Q17's.
- UTC is still recorded on every line, from the database's clock, so Rule 1's
  four hours and Rule 6's thirty minutes are measured on one clock. It decides
  nothing but durations (Amendment 7(b)). The database's clock stamps the row; a
  UTC a caller composed inside `append-line` text (`code/skills/handoff/SKILL.md:743`,
  `code/lane-handoff:2890`), and `log PAUSED --utc`, stay as written.
- Tests, on a real engine (Q1): two concurrent claimants give one `CLAIMED` and
  one exit 2; two `HOLD`s land in commit order, and the later writer sees the
  earlier line as Q17 decides.

### Roles (clause (b))

- A **REGISTER-WRITER** role, scoped to the register's tables, the journal and
  the switch, is held by `lanes-edit.sh` and its renderer on every writing
  workstation, through its own credential pair in `lanes-index.conf` (the
  configuration, above). It is the one change to Amendment 14's "no session
  holds a writer role". Whether `lanes-register` is its only holder is Q2's.
- Amendment 14's **indexer** role keeps the ledger half's derived tables and
  cannot write the register's; the register-writer cannot write the ledger
  half's.
- The **READER** role serves the hooks and the listings.
- Tests, on a real engine (Q1), since a fake `psql` cannot enforce a GRANT: each
  role refused outside its scope.

### The register service, and the guard as its client

Folded into #165 on Brett Heap's word of 2026-10-05, "fold the guard into 165",
which answered his question "is the guard running in the model cli? if so, can
we move this to LS?". Today `openRepoTools --install` writes `lanes-edit.sh
guard` as a `UserPromptSubmit` hook, so every prompt forks the helper (18,977
lines at the pin) and reads the register and tmux; under Amendment 22 it would
also open a database connection per prompt.

- **`lanes-register`,** this change's storage layer served over a local socket:
  one process per workstation holds the database connection and the credential,
  and answers the reads every act and guard makes (`who`, `binding`,
  `live-holder`, `next-free`, `managed-projection`, the guard's triple check) in
  milliseconds. #165's body has it hold the REGISTER-WRITER and READER roles,
  "one holder instead of every session"; Amendment 22's clause (b) gives the
  REGISTER-WRITER role to `lanes-edit.sh` and its renderer. So "one holder"
  holds only if Q2 makes every writer the service's client.
- **Every answer that decides state is read fresh** (S3): from the database for
  that request, or proven current against the journal's head sequence in that
  request. A parse the service keeps never answers alone, and with the database
  unreachable the service refuses as the direct read would (69, or 2 for the
  guard), never answering from what it read before.
- **It holds the connection as a long-lived `psql` child on pipes,** keeping
  #160's no-driver rule (`code/lanes-index:1272–1276`: "reached ONLY through
  `psql` … Nothing here imports a driver") and `dependencies = []`
  (`code/pyproject.toml:51`), unless the design names a driver as a new
  dependency for Brett's word. R1 measured one write at 14.7 ms through a new
  `psql` process and 0.97 ms in a persistent session.
- **It is a candidate home for S2's renderer (Q5)** and for Amendment 14's
  `lanes-index sync`. Amendment 22's S2, as Amendment 23 amends it, has the
  writer fork the renderer in the writing lane's checkout. On `mode=primary` the
  post-write nudge (`code/lanes-edit.sh:1263`, `:2554`) must not re-index the
  mirror into the database it came from (Q3).
- **It starts on demand** (#165's body: "socket activation or first call") and
  needs no supervisor of its own. How it starts on Linux and macOS, with no
  systemd or launchd, and in containers that do not share `/tmp`, is Q2's.
  Nothing in the code leg starts a long-lived process today. New bash keeps to
  the hygiene suite's bash-3.2 rules; new Python is standard-library only,
  Python 3.9 or later (`code/pyproject.toml:45`).
- **The `UserPromptSubmit` guard becomes the service's client.** The client
  sends what the guard reads today and a service cannot read for itself:
  - the hook's `session_id`, `cwd`, `agent_id` and `prompt`; the prompt answers
    a pending offer (`code/lanes-edit.sh:10399–10412`);
  - the window reference and the window name (`#W`, `:10432–10433`);
  - the live record's `name`, `nameSource` and liveness facts (`:10455`,
    `:10483–10485`). The client reads these from this container's session
    records and tmux, because a pid is pronounced only inside its own namespace
    (Amendment 18(b)).

  It gets back `ok`, `refuse <reason>` or `offer <the three A12 Addendum 1
  choices>`, and exits 0 or 2. The offer file, the `/rename` typing, and answer
  2's `lane-start --no-launch --bind` and `set-row-state` (`:11171`, `:11199`)
  stay client-side acts, and those two writes are transactions through the
  storage layer. The hook stays, because the harness's exit code is the only
  thing that refuses a prompt. Q2 decides whether the client is a block at the
  top of `lanes-edit.sh guard`, keeping the hook string and #237's probe, or a
  separate program with a new hook string. Either way the socket call is a
  standard-library Python client, because bash has no AF_UNIX and the code leg
  has no `nc -U` or `socat`. The `CLAUDE_NO_LANE=1` exception of
  `skip-no-lane-guard` (exact value, exit 0 before any input or identity probe)
  is kept.
- **The `SessionStart` block becomes a client the same way.** It is read-only,
  still exits 0 always, and still reads offline through the mirror.
- **With the service unavailable,** the hook falls back to `lanes-edit.sh
  guard`'s direct read. On `mode=primary` that read is of the database, never
  the mirror: S3 names the guard among the readers that fail closed and lets
  only reads that decide nothing read the mirror, while today's direct read
  prefers `origin/<branch>` and falls back to the checkout's file
  (`code/lanes-edit.sh:10500–10501`). Where it cannot read the database it
  refuses with 2 (Amendment 12's D3). Whether that direct read holds the READER
  credential itself, from `lanes-index.conf`, or the fallback refuses 2 whenever
  the service is down, is Q11; under the first, "one credential holder" holds for
  the REGISTER-WRITER role only. Writes never fall back (S1).
- **"Unavailable" includes a service that accepts and never answers.** A hook
  killed at its timeout does not exit 2, so the prompt passes
  (`code/lanes-edit.sh:9439–9441`); the guard's timeout is 5 s
  (`code/openRepoTools:2344`). The client bounds its connect-and-answer wait well
  under that, leaving the direct read room to finish inside it; the bound's
  value is Q2's.
- **The socket API is documented** in `docs/README-lanes.md`. It is the surface
  the 001 feature's lanes service (T055) adopts or wraps, so Codex and the
  managed path ask the same question of the same process.
- Tests: the guard's answer through the service byte-identical to the direct
  read for `ok`, `refuse` and `offer`, compared as stdout, stderr (the fallback
  line aside) and the exit code; with the service down, the hook falls back and
  says `guard: direct (service unavailable)` on stderr, with the same exit
  code; a service that accepts and never answers gives the fallback line, the
  same exit, and the whole hook inside its 5 s; the database unreachable while
  the service is up gives 2, never a cached `ok`; a write by another process
  between two requests is in the next answer, equal to the direct read; the
  hook's environment, and every environment a session inherits, carries no
  database URL or password (Amendment 14(c)); the service-backed guard answers in
  under 50 ms on the suite's fixture register, measured as Q10 settles and
  recorded in the PR body.

### The agreement tests

Amendment 22's act 2 names them, and #165 adds the service's. None runs against a
real shared database. The fixture is decided (Q1): a disposable local cluster per
suite run, and the existing fake `psql`. R1 lists what only a real engine proves:
a byte-identical render from real tables, SERIALIZABLE ordering with its 40001,
real GRANTs, import and rollback round trips, and the measured cost; so at least
tests 1, 4, 7, 8 and 9 below, and the roles' test. Those are required in CI,
which refuses rather than skips them; elsewhere they skip loudly, naming
`PG_BIN` and `postgresql-16`. The fake-`psql` tests (argv, connection strings
and credentials, the 69 refusal, the SQL's shape) always run.

1. The mirror byte-identical to a full render from SQL after each of S1's
   writes, the projection writer's storage-layer entry included.
2. Refusal, 69, when the database is unreachable, for each of S1's writes:
   the register's rows, the journal and git unchanged, and no lock left.
3. A files-mode writer refusing under the marker.
4. Two concurrent writers: one `CLAIMED`, the other exit 2; two `HOLD`s in commit
   order.
5. Two renderers racing: one commit per sequence, in order.
6. A stray commit to a rendered path stopping the renderer.
7. The import's byte-identical proof, and its refusal on a difference.
8. Rollback: files byte-identical, marker gone, SQL writes refused.
9. The measured cost of one write, recorded in the PR body: a median and a p95
   over warm runs, with the cold first call beside them, on a named host, store
   and path (Q10).
10. The guard through the service: byte-identical answers (stdout, stderr less
    the fallback line, and the exit code), the fallback line, no credential in
    any environment a session inherits, and under 50 ms, recorded in the PR body
    (Q10).

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
  pathspec commit, push, read-back and replay after it (S4: the publish act; in
  the act-2 drafts, and in `code/tests/test_repo_hygiene.py:2769`'s
  anticipation, `commit_push` becomes `commit_publish`), the read-side verb that
  fast-forwards `W` and
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
openRepoTools-2, the test strategy; #165 comment 6547987633), R2 (lane
openRepoTools-4, the writer and reader inventory; comment 6548160647, whose two
checks' corrections are used here) and R3 (lane openRepoTools-1, the schema, the
cutover and the landing path), which is not in yet, so Q3 and Q4 stay open for
it. Q1 is answered. Q2(b), Q6, Q8 and Q11–Q14 carry R2's questions for Brett,
B1–B8; Q15–Q17 carry the QA seat's. Where the reports favour an option, the
question says which; that is evidence, not a decision.

1. **The test strategy: answered.** Brett Heap, in lane 3's session at
   2026-10-31T19:32:08Z, verbatim: "1A and 2A" (#165 comment 6548141614), on
   R1's two questions:
   - **1A:** PostgreSQL 16, server and client, is added to the bench images;
     lane openRepoTools-2 makes that PR in opensoft/openBenches.
   - **2A:** the real-engine tests are required in CI, which refuses rather than
     skips them (the pattern of `OPENREPOTOOLS_COMPOSED=1`, root
     `AGENTS.md:354–355`); elsewhere they skip loudly, with a reason naming
     `PG_BIN` and `postgresql-16`; the fake-`psql` tests always run. Today the
     `tests` job accepts a skip that names its reason, and the `package` job
     fails when any of its three test modules skips one.
   - **So the fixture is R1's option (a):** a disposable local `postgres` cluster
     per suite run (`initdb -N`, a Unix socket only with `listen_addresses=''`,
     a database per test from a template), on the binaries CI's `ubuntu-24.04`
     image already ships, plus the existing fake `psql` for shape and refusal
     tests. No `services:` container (option (b)), and no change to the four-job
     gate `code/tests/test_tests_workflow.py` pins (`:836–842`).
   - **The facts R1 found:** `code/.github/workflows/tests.yml` runs four jobs on
     `ubuntu-24.04` (`:94`, `:114`, `:174`, `:219`), with no `services:`
     container and no running database. The image ships the PostgreSQL 16.15
     server binaries with the service disabled, so a suite can start its own
     cluster. py-bench and cloud-bench have no Postgres binaries until 1A lands,
     and `macos-latest` has no Postgres server. On py-bench a cluster cost about
     1.1–1.3 s per suite (`initdb -N`, start and stop) and a database per test
     16–62 ms. Two SERIALIZABLE writers on one row gave the loser SQLSTATE
     40001, which no fake can produce. A Unix socket path is limited to about
     107 bytes on Linux and 103 on macOS, so the fixture keeps its socket
     directory short. `code/tests/test_lanes_index.py`'s SQLite and fake `psql`
     ("NO REAL DATABASE AND NO NETWORK", `:26–29`) stay.
   - **Still the design's:** the fixture's details (R1's section 4: finding the
     binaries, how CI's refusal is triggered, the readiness checks) and the
     measurements (Q10).
2. **The register service or a connection per process (R2).**
   - (a) Does `lanes-register` hold the only connection and credential, with
     every writer and reader as its client, or does each `lanes-edit.sh` write
     open its own transaction and the service serve only the reads and the
     guard? Amendment 22's clause (b) gives the REGISTER-WRITER role to
     `lanes-edit.sh` and its renderer; #165's body gives both roles to the
     service.
   - (b) **For Brett (R2's B6):** with the service down, may a write open its own
     connection with its own REGISTER-WRITER credential (still SQL, so no store
     fallback), or does it refuse with 69? Clause (b)'s wording supports the
     first, the fold's "one holder instead of every session" the second; neither
     report favours one.
   - (c) Is it one process per workstation or per bench container? Containers on
     one host share the projects mount (Amendment 23's S1), but on 2026-10-23
     lanes found that py-bench and cloud-bench do not share `/tmp`, and
     `lanes-index`'s state directory is per environment. Where does the socket
     live, and how does it know its clients?
   - (d) How does it start on demand on Linux and macOS, with no systemd or
     launchd, and how is it one process across containers?
   - (e) Does it run on files-mode and `mode=index` workstations too, serving the
     guard's file reads, or only on `mode=primary`?
   - (f) Is it a new installable (there are seventeen, `code/openRepoTools:1292`)
     or a subcommand of one?
   - (g) Is the guard's client a block at the top of `lanes-edit.sh guard`,
     keeping the hook string, the workBenches launcher's copies of it and #237's
     probe, or a separate program with a new hook string, which `--install`
     refuses on every workstation that carries today's
     (`code/openRepoTools:3317–3331`)? What is the client's deadline under the
     hook's 5 s? R2 favours the first: the exact string is the installer's only
     idempotence.
   - (h) Host-local facts that decide writes (liveness for `claim --force`,
     `rename-lane` and `retire-rows`; tmux for `request-handoff`): does the
     client read and pass them, or does the service refuse what its container
     cannot see (Amendment 18(b))?
3. **The schema's relation to #160's (R3, pending).** Does the register live in
   #160's store and schema, moving `SCHEMA_VERSION` from 1 to 2, or in a schema of
   its own with its own version? #160's tables are derived records keyed by
   (source, record id) and versioned by a content digest, with `owner` filled
   from `managed-projection`. R2 adds two facts. #160's `build_records`
   (`code/lanes-index:765`) keeps rows, Rule 6 and `HOLD` lines, aliases and log
   lines, and drops LANES.md's header and every other file-level line, so
   neither its tables nor `export` can prove S5's byte identity as they stand.
   And LANES.md is one ordered list: rows are replaced in place, new rows and
   free lines are both appended at the end, and `archive-rows` deletes from the
   middle, so the schema needs one order key over rows and free lines. When the
   tables become the truth, do they keep that keying, and how are the journal
   and the switch stored? Do the indexer and register-writer roles split by
   schema or by table GRANT? What do `sync` and `reconcile` do on a primary
   workstation, and does the post-write nudge (`code/lanes-edit.sh:2554–2568`,
   which has no mode check) stop there, so it never re-indexes the mirror into
   the database it came from? Does a writer that needs SERIALIZABLE retries keep
   #160's `psql`-only path? R1's figures bear on it: one write cost 14.7 ms (p95
   22.8) through a new `psql` process and 0.97 ms in a persistent session.
4. **Import and rollback against opensoft/brett-wip (R3, pending).** Which
   checkout runs the import and pushes the one `lanes/STORE` commit, now that
   Amendment 23 makes
   `W` read-only and gives each lane its own checkout? How long is the window
   between import and cutover on a busy estate, given that the cutover aborts when
   `main` moves under `lanes/`? Where is "the mirror was last shown current"
   recorded, so a rollback with the database unreachable can prove it? Is the
   cutover a step of `import` or a command of its own? How do a
   lane checkout holding unpublished commits (Amendment 23's S6.3) and #198's
   fail-closed writers meet the cutover commit? What, exactly, do acts 3 and 4
   need from Brett?
5. **The renderer's home.** Is it started by each writer after its commit, as
   Amendment 22's S2 says, or run inside `lanes-register`, which #165's fold
   calls its natural home? With renderers in several lanes' checkouts on several
   workstations, how do they converge on one commit per sequence, and in what
   form is a journal entry's line edits stored so that it replays
   byte-identically? R2 found five acts that change two or more rendered files
   in one commit today (`LANDING` and `LANDED`, `rename-lane`, `retire-rows`,
   `archive-rows`, `migrate-state-cells`), with file creates, renames and whole
   copies among their edits, so one entry spans files.
6. **The order against Amendment 23's act 2 (R2's B1, for Brett).** Does this
   change build on that act's publish act (`commit_publish` in its drafts) and
   `<W>-lanes/<lane>/` after its six PRs are re-homed to
   opensoft/openRepoTools-code and land, or carry only what the renderer needs?
   Who re-homes those PRs, and does this change wait for them? That is for Brett
   and lane openRepoShape-2, with lane 3.
   - **Options:** (a) build all of #165 on today's `commit_push`; (b) wait for
     act 2 to land, then build all of #165; (c) split #165: its SQL half now, on
     `2a057a6d` (the storage layer, the journal, S1's transactions and 69, S3's
     readers, S4, S5's import with its scratch-tree render and proof, S6, the
     roles and the service), and its git half after act 2 lands (the renderer's
     commit and push, the files-mode marker refusal, rollback's push, and
     agreement tests 3, 5 and 6).
   - **The evidence favours (c),** which lane 4's R2 (section 6) and its
     architect seat both recommend. On today's code the renderer would commit
     in `W`, which `LANES_REPO` defaults to (`code/lanes-edit.sh:1053–1056`) and
     Amendment 23 forbids; `commit_push` rebases (`:2425`) and pushes the whole
     branch (`:2410`, `:2432`), the opposite of S2's discard and re-render; the
     three handoff commit blocks run `pull --rebase` with no lock; and the five
     verbs that do not fetch before they edit give the marker check nowhere to
     sit. Act 4 cannot run before both halves exist, so the split loses nothing.
     Its cost: the git half waits on another lane's six stacked drafts, and
     every line number cited here moves.
7. **How a writer runs one transaction (R2).** Through `psql` with one
   `BEGIN … COMMIT` session and `ON_ERROR_STOP`, through a `lanes-index`
   subcommand, or through the service? R1 measured 14.7 ms per write through a
   new `psql` process and 0.97 ms in a persistent session. A writer run through
   `psql` must tell a 40001 from any other error, since under `ON_ERROR_STOP`
   `psql` exits 3 for any error in its script (R1's loser exited 3). Where do
   each verb's existing checks run (the one-match guard, the `numstat` refusal,
   Amendment 7(f)'s rescan): in SQL, or on a rendered image inside the
   transaction? May one transaction carry the several verbs of one act, so that
   an act's partial states disappear (R2: the swap record's `log PAUSED`,
   `append-line` and `set-row-state`; `lane-end`'s `ENDED` row and line; a new
   lane's row and `STARTED` line; a resume's `LIVE` and `RESUMED`), or is it one
   transaction per verb, as S1's "ONE transaction" per write reads? Lane 4
   recommends the first, and R2C asks whether it needs Brett's reading of S1.
   What do `LANES_NO_FETCH=1` (61 verb calls in the caller scripts pass it) and
   `register-row-local` mean on primary? Which of the lifecycle acts' writes are
   register writes is answered by R2 (S1 above, "Handoffs stay git files").
8. **`lane-start`, `lane-handoff` and the handoff skill with the database
   unreachable (R2's B2 and B3, for Brett).** Amendment 22's S3 makes all three
   readers that refuse with 69. Amendment 8(d) and Amendment 23's S3 say a stamp
   `lane-start` cannot write "never blocks the launch", and that `lane-handoff`
   and the skill "write the handoff file (a swap is never left unwritten,
   `R-A11-11`)"; Amendment 23's act-2 tests include "every launch still exiting
   0".
   - (a) **`lane-start` (B2).** Which of its reads decide state and refuse the
     launch, and which of its writes only print a note? Options: (i) its
     state-deciding reads refuse 69 before the launch, before any stamp is
     tried; once they pass, a refused register write stays a note, but for
     `add-row`, which already passes its code through
     (`code/lane-start:3819–3824`); the Rule 3 handoff stamp stays a git commit
     that never blocks; (ii) as (i), but the register writes `add-row`,
     `set-row-state LIVE` and `log STARTED`/`RESUMED` also refuse 69 and stop the
     launch; (iii) the launch never refuses on the database, and every refused
     read or write is a note. The evidence favours (i) or (ii): lane 4's
     architect seat and R2 both read the two texts as about different things,
     reads that decide against stamps that could not be written, and both keep
     the handoff stamp non-blocking. The seat reads it as (i); R2 offers (ii).
   - (b) **`lane-handoff` and the skill (B3).** The reads that name the lane come
     before the handoff file is published (`code/lane-handoff:1448–1463`, reads
     at `:1649–1815`, `publish-handoff` at `:2639`;
     `code/skills/handoff/SKILL.md:97–118`, reads at `:147–239`, the publish at
     `:276`), and `sread` turns any other code into exit 1, so a 69 there leaves
     the swap unwritten on every database outage. Does `R-A11-11` survive S1?
     Options: (i) the handoff file may be written from a mirror read that says
     so, which S3 does not list among the mirror reads, while the `PAUSED`
     record refuses with 69; (ii) the whole swap refuses with 69, handoff file
     included. Today a plain handoff exits 0 after a failed `PAUSED` write
     (`code/lane-handoff:2874–2881`), and it already refuses before the file
     where ownership is unknown (`:1727`). Neither report favours one.
9. **Exit codes.** 69 joins `lanes-edit.sh`'s table. Which code does each reader
   return on `mode=primary` where a read fails with 1 or 10 today? S4 keeps
   `managed-projection`'s 1 for an unreachable database where S3 gives the other
   readers 69: are there other such contracts? Does `lanes-index`, whose codes
   are 0, 1, 2, 3 and 64, use 69 too? Does a refused direct write (the hand-edit
   `commit`, the captures) exit 69, or 2 as a refusal? On `mode=primary` S6 has
   the losing claimant exit 2, the table's "the object is held", so `claim` no
   longer spends 7 (Q12 asks Brett to confirm it). Code 9's re-check reads local
   session records, which no transaction can hold: where does it run? How do
   the callers that branch on today's codes (`lane-start`, `lane-end`, `lane`,
   `lane-handoff`) treat 69? R2 found default arms that let an unknown code
   through (`code/lane-start:1154`, `:1262`, `:1271`, `:3430`;
   `code/lane-end:921–945`; `code/lane-worktrees:508–524`) and reads that answer
   "no record" when the read failed (`canon_lane`, `live-holder`,
   `register-row`, `session-lane`, `binding`), so a 69 from a verb is not safe
   until those callers and reads change.
10. **The measurements.** On which host and against which Postgres, local or
    remote, is "the measured cost of one write" taken, and the guard's 50 ms
    through the service? The path alone moves the figure fifteenfold (R1:
    14.7 ms per new `psql` process against 0.97 ms in a session), so the PR body
    names the host, the store, the path and the statistic. Does the 50 ms
    include the client's process start and an on-demand start of the service,
    and is it a recorded acceptance number or a CI assertion? R1 recommends
    measuring where the gate runs, in the `ubuntu-24.04` `tests` job on the
    fixture's cluster: the median and p95 over at least 200 writes, once with
    test settings and once durable, with a bench figure beside it. The QA seat
    asks for at least 20 warm runs with the cold first call beside them. #165's
    body says "recorded in the PR body", which favours a recorded number over an
    assertion on shared runners.
11. **The guard's fallback credential (R2's B4, for Brett).** With the service
    down, the fallback must read the database, since S3 does not let the guard
    read the mirror. Options: (a) the direct read holds the READER credential
    itself, from the 0600 `lanes-index.conf` and its password file, so no
    database URL or password enters the hook's environment, and "one credential
    holder" holds for the REGISTER-WRITER role only; (b) the fallback refuses 2
    whenever the service is down. The evidence favours (a): #165's body calls the
    fallback "a READ fallback to the same truth", lane 4's architect seat writes
    it that way, and (b) would block every prompt of every lane on a service
    outage while the database is up.
12. **A claim loser's exit (R2's B5, for Brett).** Amendment 22's S6 says the
    loser "sees it and exits 2, writing nothing", as #165's body does. The exit
    table gives 7 to "ANOTHER ACT GOT THERE FIRST, and this one wrote nothing.
    `claim`: CLAIM-LOST" (`code/lanes-edit.sh:505–506`), and Amendment 23 keeps
    `CLAIM-LOST` and 7 on a files-mode estate. Options: (a) 2, as S6 says; (b) 7.
    The evidence favours (a): it is the ratified text, and on `mode=primary` the
    loser's pre-check sees the holder inside its own transaction, which is the
    table's 2, "the object is held". Today's 7 is a race found after the loser's
    own commit existed, which no longer happens.
13. **`rename-lane`'s order (R2's B7, for Brett).** Today it is one commit over
    the row's key cell, the log's move and `RENAMED` line, `aliases.tsv`, and the
    handoff file's move and Rule 3 stamp: Amendment 16's "ONE commit, refused or
    whole". On `mode=primary` the handoff move is a git file outside S2's
    register and cannot sit in the transaction. Which goes first, what does a
    failure between them leave, and what repairs it? Options: (a) the
    transaction first, so a failed move leaves the row's handoff cell naming a
    file not yet moved; (b) the handoff move and stamp first, as a commit in the
    lane's checkout, so a refused transaction leaves the handoff moved while the
    register still names the old lane and path. Neither report favours one.
14. **`wip init` and the `LANES.md` link on a primary estate (R2's B8, for
    Brett).** `openRepoTools wip init` commits its seed under `lanes/`
    (`lanes/LANES.md` and `lanes/log/README.md`, `code/openRepoTools:5026–5080`)
    on first creation, or again when a template file is owed; under
    `lanes/STORE` that is a stray commit that stops the renderer.
    `~/projects/xFactory/LANES.md`, placed by `link-estates`, is a hand-write
    path into `W`, which Amendment 23's S5 already makes "for reading". Options
    for each: refuse on a primary estate (for `wip init`, once `lanes/STORE` or
    any `lanes/` file exists), or stay as today. Lane 4's architect seat favours
    refusing `wip init` there ("must not run under `lanes/STORE`"); R2 calls both
    policy, not code.
15. **The serialization retry (the QA seat's QA-1).** S6 allows SERIALIZABLE or
    a lock on the object. Options: (a) SERIALIZABLE, with a bounded retry on
    40001 and 40P01 that re-runs the pre-check; (b) a transaction-scoped lock on
    the object first (for example `pg_advisory_xact_lock`), so the loser blocks,
    then reads the committed `CLAIMED` and exits 2 with no 40001 at all. Under
    (a), what is the bound, and the pause between tries? R2 calls (b) the
    simplest form for a claim; the QA seat's fix is (a). No text sets a bound;
    the nearest is `lanes-edit.sh`'s six attempts for a lost push race
    (Amendment 23's S4).
16. **A COMMIT whose outcome is unknown (the QA seat's QA-5).** Options: (a) an
    exit-3 analogue, "cannot confirm whether it committed", telling the caller
    to look by journal sequence before retrying, as `lanes-edit.sh`'s 3 does for
    a push (Amendment 23's S4); (b) an idempotency key in the journal, so a
    retried write is recognised and not applied twice; or both. No text decides
    it. The precedent favours (a) for the report; only (b) makes a blind retry
    safe.
17. **The later `HOLD` or `LANDING` writer (the QA seat's QA-1; R2's design
    questions).** S6 says the later writer's transaction "reports the earlier
    line to it before it acts". Is that a refusal, and with which code, or a
    report with exit 0, and is the later writer's own line still written? Does
    `append-line` parse the four Rule 6 and Amendment 10 shapes inside the
    transaction, as `lanes-index`'s `REGISTER_KIND_RE` does, or do `HOLD` and
    `HOLD RELEASED` get a verb of their own? No text decides either.
18. **`lanes/repos.tsv` on a primary estate (R2's design questions; the QA
    seat's QA-2).** It is read by `canon_object`, `resolve-home` and
    `who --landing`, written by no verb, and kept by a person as the alias
    override (`docs/README-lanes.md:74`). Outside the render set it no longer
    stops the renderer (S2 above). Does it stay a git file a person commits in
    their lane's checkout, or is it imported into SQL and rendered like
    `aliases.tsv`? The same question covers `lanes/log/README.md`, which
    `lanes-index` skips (`code/lanes-index:475`).

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
    `:18467`); the `SessionStart` block; `commit`'s two halves; the other direct
    writes' refusals (the captures, `add-row`'s compensating commit, the
    branch-wide push); the starting of the renderer; and, in every mode, the
    configuration read and the marker check.
  - `code/lanes-index`: `mode=` and the REGISTER-WRITER's pair in the
    configuration, the register's tables, the journal and the switch,
    `import --at` and `rollback`, and `status` and `reconcile` for the mirror.
  - `code/lane-start`, `code/lane-handoff`, `code/lane-end`, `code/lane`,
    `code/lane-rename` and `code/lanes`: their reads under S3, and the default
    arms that let an unknown exit code through (Q9).
  - `code/lane-worktrees`: its three direct `git grep` reads of
    `origin/<branch>` (`:706`, `:997`, `:5556`), which decide `--yes`'s
    deletions by a `LANDED` line (`:974`), move behind a verb under S3.
  - `code/skills/handoff/SKILL.md` and `code/skills/restart/SKILL.md`: their
    reads under S3 (`sread` and `lread`, both `LANES_NO_FETCH=1`); and the
    handoff skill's commit block (`:424–457`), whose `pull --rebase` (`:449`)
    would rebase unpushed render commits.
  - `code/openRepoTools`'s `wip init` (`:5026–5080`): its seed commit under
    `lanes/` would be a stray commit under `lanes/STORE` (Q14).
  - `lanes-register`, new in a form Q2 decides; `code/openRepoTools`'s
    `INSTALLABLES` and the hook entries `--install` merges, with #237's gate on
    them, if the service or the guard's command changes what is placed.
  - Whenever an installable is added or `tests.yml` changes:
    `code/pyproject.toml`'s `force-include` ("A payload file joins both lists in
    one commit", `:68–75`); the count lines in `code/.github/workflows/tests.yml`
    (28 receipt rows, `:524`) and `code/.github/workflows/release.yml` ("16 of 17
    placed", `:1428`, and the install legs to `:1510`, macOS's among them); and
    `code/tests/test_tests_workflow.py` (`:156`, `:836`, `:1019`).
  - Tests: new agreement tests; `code/tests/test_lanes_index.py`;
    `code/tests/test_lane_helpers.sh`; `code/tests/test_repo_hygiene.py`, whose
    rules read the exit table and `commit_push`; `code/tests/test_package.py`, if
    the payload changes; and `code/.github/workflows/tests.yml` with
    `code/tests/test_tests_workflow.py`, which pins its four jobs (`:836–842`),
    only if the CI refusal of Q1's real-engine tests needs a step or a variable
    there. Q1 adds no `services:` container.
- **The spec leg,** opensoft/openRepoTools-spec: this change;
  `specs/007-sql-register/`; `docs/README-lanes.md`'s new section and the notes
  beside its existing ones, among them the derived-index section's "No test
  creates a real database" (`:3981`), which the local cluster ends.
- **The assembly root,** opensoft/openRepoTools: the pin bump; and `AGENTS.md`'s
  account of the suite (`:341–346`: "The suite creates no real repository and
  reaches no network"), which a cluster listening only on a Unix socket keeps
  true and which then names it.
- **Governed files:** `openspec/` text, `.github/workflows/`, and every file that
  records a pin, a digest or a measured value (the protocol's "Governed
  invariants").
- **Outside the repositories:**
  - at acts 3 and 4: a Postgres database and its roles; `mode=primary` in
    `lanes-index.conf` on Eagle and Raven; `lanes/STORE` in opensoft/brett-wip;
  - under 1A, PostgreSQL 16 in the bench images (opensoft/openBenches, lane
    openRepoTools-2's PR);
  - if the guard's hook command or its target changes, opensoft/workBenches'
    `claude-profile` launcher, which writes the same two strings into every
    profile (`code/openRepoTools:2306–2309`) and probes
    `~/projects/xFactory/lanes-edit.sh` for a `guard)` arm (`:2360–2366`).
    Every workstation whose settings carry today's string is refused by
    `--install` until that entry is removed (`:3317–3331`).
- **Unchanged while `lanes/STORE` is absent:** the output and exit codes of every
  `mode=index` and unconfigured workstation; each writer gains the configuration
  read and the marker check. **Unchanged everywhere:** the ledger half; the
  handoffs; Rules 1–10 as rules; Amendment 7's grammar.
