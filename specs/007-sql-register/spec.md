# Feature Specification: The SQL register under `lanes-edit.sh`

**Feature Branch**: `007-sql-register`

**Created**: 2026-11-08, as lane openRepoTools-3's draft keyed to commit W;
assembled into feature 007 on 2026-11-09, keyed to commit Y

**Status**: Assembly, phase A. The spec branch is local and unpushed. Its
first push carries the spec PR, which goes to Brett Heap with its list.

**Input**: the OpenSpec change `add-sql-register`, for
opensoft/openRepoTools#165: Amendment 22's act 2, as Amendment 23 amends its
S2. It is keyed to commit Y of the change,
`a6497d009c5b6110eb15af10c298e955275c54fe`, the change's last text commit.
The spec leg's branch `007-sql-register` merges Y whole, at merge commit
`066ad8f119a5db3517a39509fa021df1df684e31`, whose parents are the spec leg's
`main`, `aed18524b`, and Y.

- **Y on X.** Y is X's child, `97147aa8`. In `design.md` it rewrites only
  SVC-5's build order, X:3951–3955, as Y:3951–3956, so a `design.md` line
  above Y:3956 is X's line plus one. It changes the sql spec's bind
  requirement (spec:318 and spec:338, R26's point 38) and records Brett's
  "4A" in `clarifications.md`'s Q29 entry (clar:524). `operations.md` and
  the service spec are X's, byte for byte.
- **The history** W → J3 → X → Y is in the branch:
  `git diff bb08d080..a6497d00 -- openspec/changes/add-sql-register` shows
  every change since lane 3's drafts.
- **The branch after Y.** Lane 3's rulings since Y change the change's two
  spec files on this branch, at `dabd74bd`, `ce3fad83` and `ad82c2f4`, and the
  spec PR's
  list names each change. Every other file of the change is Y's.

**Citations**: `spec:<n>` is a line of `specs/sql-register/spec.md` and
`svc:<n>` of `specs/register-service/spec.md`, as this branch has them at
`ad82c2f4`, where lane 3's rulings since Y change them (research.md's ERR-03
and DEC-05). `Y:<n>` is a line of `design.md`, `ops:<n>` of `operations.md`
and `clar:<n>` of `clarifications.md`, which are unchanged from Y. Each was
checked by script against the text it stands for.

**Marks used in this file:**

- **`[PLAN CHOICE: <name>]`**: the design leaves this choice to the plan.
  plan.md's "Choices the design leaves to this plan" names each one, and
  research.md records how lane 3 took it.
- The draft's marks for commit X (the hosting, the logins and the backups)
  and for J3 are resolved: X settled those three points, and J3 carries
  lane 2's test plan v3f.

**Governance**: this is the one Speckit feature of that change. Its sources,
and the only scope it takes, are:

- [proposal.md](../../openspec/changes/add-sql-register/proposal.md). It
  carries Brett Heap's answers: round 1 RULED, and round 2 taken as
  recommended in every question, both on 2026-11-07 (#165 comment
  6710653292). It also carries his answers of 2026-11-08 to the items routed
  to him (6721583757, 6721370130).
- [design.md](../../openspec/changes/add-sql-register/design.md), 6,745 lines
  at Y, sha256 `35ca5179…`. It holds D1 to D25, S1 to S6, the register
  service's SVC-1 to SVC-12, lane 4's phase plan and lane 2's test-plan
  summary.
- [operations.md](../../openspec/changes/add-sql-register/operations.md),
  lane 1's part 2, sha256 `c94ce7d7…`: the install contract (ops:133), acts 3
  and 4, the rollback, the disaster path and the operator tools.
- [clarifications.md](../../openspec/changes/add-sql-register/clarifications.md).
- The two delta specs:
  - [sql-register](../../openspec/changes/add-sql-register/specs/sql-register/spec.md),
    with 69 requirements at Y;
  - [register-service](../../openspec/changes/add-sql-register/specs/register-service/spec.md),
    with 19.
- [test-plan.md](../../openspec/changes/add-sql-register/test-plan.md), lane
  2's v3f, sha256 `8702b70a…`, 9,145 lines, which J3 carries byte for byte.
- The rulings since W that shape this feature:
  - **Brett's hosting ruling of 2026-11-08T16:17:36Z** (#165 6723925474):
    "CNPG in the install", "Wait for the install", and "Block until link
    returns";
  - **lane 3's rulings of 2026-11-08T16:23Z** on lane 4's speed-up proposal
    (6723967774): group B runs beside group A; 007 is split by file
    ownership; the design freezes after X and J3;
  - **Brett's standing landing word,** his choice "Standing words per group
    (Recommended)" in a dialog in lane 3's session at 16:21:40Z, which lane
    3's #165 6723967774 quotes verbatim;
  - **lane 3's 007 call** (6724332510): U1 to U11 and PC-1 to PC-16 as plan.md
    proposes;
  - **Brett's choices of 19:25Z**, as Y records them (clar:495–505;
    6726078173): "Install's private CA (Recommended)", "lanes-index login
    (Recommended)" and "Yes, drill at act 3 (Recommended)"; the first is
    superseded on 2026-11-09 by Brett's "add openXPKI to prod and use that.
    both QA and Prod need openXPKI" (14:38:23Z);
  - **lane 3's rulings on the lanes' blocks** (6726556972), R01 to R61, with
    their evidence memo, and the rulings after it (6726725691, 6727022920,
    6727201960, 6738474549). research.md records each one this feature
    takes;
  - **Brett's own word on R35 and R41**, typed in lane 3's session at about
    2026-11-08T20:33Z: "4A, 5A, 1 let them stand, 3A" (#165 6726766577). 4A
    builds the service beside groups A and B, and the v1.2.0 tag waits for
    it; 5A carries the launcher's window reads in the v1.2.0 workBenches
    re-vendor.

**What this feature builds**: what lands by pull request in
opensoft/openRepoTools-code, the code leg, and, for the manual and the
release notes, in opensoft/openRepoTools-spec.

- **In the code leg:**
  - `lanes-index`: the register's schema and migrations, its subcommands,
    the renderer, import, cutover and rollback, the operator tools and the
    register service;
  - `lanes-edit.sh`: the write path on `mode=primary`, the readers, the
    guard, `act-begin` and `start-record`;
  - the lifecycle scripts' arms: `lane-start`, `lane-end`, `lane-handoff`,
    `lane`, `lane-rename` and `lane-worktrees`, and the `lanes` listing;
  - the skills' reads;
  - `openRepoTools`' `wip init` refusal;
  - the tests and their real-engine fixture;
  - `tests.yml`'s real-engine switch.
- **In the spec leg:** `docs/README-lanes.md`'s section "The SQL register",
  the v1.2.0 release notes, and this feature's directory.

No 007 code pull request lands before the spec PR does (lane 3's rule,
6726556972). A lane may build, open pull requests and run CI before then.

It builds no act outside the repositories. Brett Heap makes those acts, or a
lane makes them on his word:

- **the install:** the PostgreSQL server, its endpoint, TLS, backups, dump
  job and secrets. It is a CNPG `Cluster` in the codeXfactory prod install on
  the prod AKS cluster, in Central US, with one instance and no HA, reached
  with no VPN through a public load balancer limited to engineers' addresses
  (Y:3327–3330; Y:6380). A codexFactory lane that Brett names builds it, to
  lane 1's install contract, "What the codeXfactory prod install provides"
  (ops:133);
- **acts 3 and 4,** and a rollback or the disaster path on the real estate;
- **the push ruleset**, the bench images' PostgreSQL 16, and the workBenches
  re-vendor. The re-vendor also carries the launcher's bare window reads of
  spec:626, by Brett's "5A" (6726766577), so that half of the requirement is
  met outside 007;
- **the v1.2.0 release:** the rc1, the tag and the upload;
- **the root pull requests:** `composed.yml`'s real-engine switch, the root
  `AGENTS.md`'s account of the suite, and the pin bumps, which are lane 1's
  under Q25(b). Lane 3 reads them as feature 007's pull requests under
  Brett's standing word, and names them in the spec PR's list so that he can
  say otherwise (R13).

## User Scenarios & Testing *(mandatory)*

The terms used below:

- **primary**: a workstation whose `lanes-index.conf` says `mode=primary`,
  after the cutover.
- **The render set**: `lanes/LANES.md`, every `lanes/log/<lane>.md` except
  `lanes/log/README.md`, `lanes/aliases.tsv` and `lanes/archive/` (Q18(A)).
- **The mirror**: the render set on `origin/main` of the workspace
  repository.
- **The marker**: `lanes/STORE`.
- **The head `n`**: the journal's last seq.
- **A write**: one run of one `lanes-edit.sh` writing verb.
- **The fixture**: lane 2's disposable local PostgreSQL 16 cluster
  (`tests/pgcluster.py`), and the fake `psql`.
- **The five outcomes**: 0, 2, 3, 69 and 75. A verb's own refusals before its
  commit keep their own codes.
- **The constant**: `RENDER_GIT_HALF`, which keeps `mode=primary` refused
  until the renderer's git half is installed (D3).

### User Story 1 - Nothing changes until the estate switches (Priority: P1)

A person on a workstation with no configuration, or with `mode=index`,
installs the release that carries this feature. Everything reads and writes
as before, except the sql spec's three exceptions (spec:60), which scenario 8
names, and two refusals:

- A build without the renderer's git half refuses `mode=primary`, and says
  why.
- Once `lanes/STORE` is on origin, a workstation that is not primary refuses
  its acts and its guard, and lists from the mirror.

**Why this priority**: every pull request of groups A and B lands on the code
leg's `main`, and ships in v1.2.0, weeks before act 4. Each must leave every
estate exactly as it is.

**Independent Test**: files-mode tests on bare origins and clones, with no
database:

- test 1's files-mode twin;
- test 3's unit form, with the marker committed by hand;
- test 14's two states, with the fake `psql`.

**Acceptance Scenarios**:

1. **Given** no `lanes-index.conf` and no `LANES_INDEX_CONFIG`, **When** any
   writer or reader runs, **Then** no `lanes-index` process starts, and the
   output and exit codes are the v1.1.0 tools' ("Nothing changes where the
   marker is absent").
2. **Given** a configuration with no `mode=`, or `mode=index`, **When** a
   write runs, **Then** it writes in files mode as today, plus the marker
   check ("`mode=` selects the register's store").
3. **Given** the mode query fails, **When** a write runs, **Then** it runs
   in files mode and warns, naming the failure. Where the fetched tree
   carries `lanes/STORE`, the marker check refuses with 69 instead. The
   failures are:
   - a configuration it refuses;
   - a crash;
   - exit 127;
   - its 2 s timer.
4. **Given** a build whose constant is unset, on a workstation configured
   `mode=primary`, **When** each command runs, **Then** (agreement test 14):
   - `lanes-index mode` and `register read` exit 78 with the fixed text;
   - every other `lanes-index` command exits 2 with the same text;
   - `lanes-edit.sh` exits 69 and repeats it;
   - each reader answers as for a failed read.
5. **Given** `lanes/STORE` on the tree that a files-mode writer has fetched,
   **When** the writer runs, **Then** it exits 69 with "this estate's
   register is SQL; configure `mode=primary`", and nothing changes
   (agreement test 3).
6. **Given** `lanes/STORE` on origin and an unconfigured workstation, **When**
   each kind of read runs there, **Then**:
   - an act exits 69;
   - the guard exits 2;
   - `lanes` and the `SessionStart` block answer from the mirror with the
     `read: mirror` line.
7. **Given** any file under `lanes/`, **When** `openRepoTools wip init` runs,
   **Then** it refuses and commits nothing (Q14(a)).
8. **Given** the three exceptions, **When** they run in any mode, **Then**
   each is exactly what this feature specifies:
   - answer 2's bind runs detached (User Story 5);
   - `lane-start`'s three register writes are one, `start-record` (User
     Story 5);
   - `wip init` refuses wherever any file under `lanes/` exists (scenario 7).

**OpenSpec requirements**:

- "`mode=` selects the register's store";
- "The estate switch is a database row and the marker `lanes/STORE`";
- "A writer that is not `mode=primary` refuses under the marker";
- "Nothing changes where the marker is absent";
- "Readers on a workstation that is not `mode=primary` once `lanes/STORE`
  exists";
- "A build without the renderer's git half refuses `mode=primary`";
- "`wip init` and the `LANES.md` link on a SQL estate".

---

### User Story 2 - One write is one transaction, with an honest exit code (Priority: P1)

On a primary workstation, a lane runs any writing verb.

- The verb's own code runs unchanged, on a scratch tree built at head `n`.
- Its change set commits in one SERIALIZABLE transaction, or not at all.
- The run ends in exactly one of the five outcomes.
- A write whose COMMIT went unanswered is settled by its write id. Its rerun
  writes it once, or finds it. The after-COMMIT tail runs at most once.

**Why this priority**: this is #165's core. Claim order, the readers'
snapshot and the guard all rest on it.

**Independent Test**: real-engine tests on the fixture, run with a copy of
`lanes-index` whose constant is flipped (test 14's second state). They are:

- test 1's per-verb part;
- tests 2, 4 and 12;
- AT-LR006 and AT-F33;
- the tests of D8, F3, F10 and F14.

**Acceptance Scenarios**:

1. **Given** each writing verb, `start-record` and the managed projection
   writer included, **When** it commits, **Then**:
   - it writes one journal entry at one new seq;
   - every row it touched reads back `primary`;
   - the images at the new seq equal a full render (agreement test 1).
2. **Given** an unreachable database, **When** any write runs, **Then** it
   exits 69, leaves the rows, the journal and git unchanged, and leaves no
   lock. A multi-file write that fails partway leaves nothing (agreement
   test 2).
3. **Given** two writers on one head, **When** both commit, **Then** the
   loser re-reads at once, and no 23505 ever reaches a writer.
4. **Given** a burst of ten writers on distinct objects, released together
   with 200 ms injected between each snapshot and its COMMIT, **When** they
   run, **Then** all ten commit, none exits 75 or 69, and the seqs run 1 to
   10 with no gap (agreement test 4).
5. **Given** a writer that cannot get its turn within 30 s, **When** its
   deadline passes, **Then** it exits 75, "register busy", having written
   nothing. Where any of its commit calls was in doubt, it first makes the
   in-doubt lookup (D7).
6. **Given** a COMMIT in doubt, where `psql` exits 2 after committing or
   before, **When** the run ends, **Then** the journal holds exactly one
   entry for the write id (agreement test 12). Either:
   - the run exits 0, after a clean retry where the first COMMIT never
     landed;
   - or, unsettled within the window, it exits 3 and prints the rerun with
     `LANES_WRITE_ID` and `LANES_WRITE_GENERATION`, and that rerun writes the
     write once, or finds it.
7. **Given** `lanes-edit.sh commit` on primary, **When** it runs, **Then**
   (spec:169; R16):
   - its carry form, the hand edit of Rule 9(d), is refused with 2, and
     neither the register nor the files change (D10);
   - its publish form, the publish of queued commits that Amendment 23's S4
     keeps, still runs, with its fence narrowed to `handoffs/`;
   - a queued commit under `lanes/` is refused as a divergence;
   - a push whose commit changes a path outside `handoffs/` is refused with 2
     at the push.
8. **Given** every database path, **When** it runs, **Then**:
   - it carries D9's bounds;
   - a line's UTC is the database's clock at the snapshot, in whole seconds
     (D13).
9. **Given** `claim` and `rename-lane` on primary, **When** each commits,
   **Then**:
   - `claim` prints `register seq <n>` (Q19(A));
   - `rename-lane` commits its transaction, then moves and stamps the handoff
     as its tail (Q13(a));
   - handoffs stay git files.

**OpenSpec requirements**:

- "Every register write is one transaction";
- "Direct writes to the files refuse on `mode=primary`";
- "Unreachable or refused is exit 69 with nothing written";
- "A serialization failure is retried, not refused";
- "A lost race is not an error on the sequence key";
- "A write that cannot get its turn reports "register busy" with a code of
  its own";
- "A COMMIT whose outcome the writer cannot learn is recovered by its write
  id";
- "Handoffs stay git files written by the lane";
- "Every database path is bounded";
- "`rename-lane` commits the transaction before moving the handoff";
- "A claim's comment cites the register sequence";
- "The journal records each write";
- "UTC comes from the database's clock in whole seconds";
- "Every row records the mode that wrote it last".

---

### User Story 3 - Claim and hold order is commit order (Priority: P1)

Two lanes reach for one object, or for `main`, at once. The database decides,
in one transaction:

- the earlier commit wins;
- the later writer writes nothing and exits 2, naming the earlier line;
- a stale `LANDING` is released only by one line, on Brett Heap's word.

**Why this priority**: claim and hold order decided in one transaction is
what only SQL adds (design, "What the case rests on").

**Independent Test**: test 4's pairs, and the C10 alias race, on the fixture.
D18's agreement tests compare the transaction's classifier with `RULE6_AWK`
on the imported history.

**Acceptance Scenarios**:

1. **Given** two `claim`s of one object, **When** both commit, **Then** one
   writes `CLAIMED`, and the other exits 2 and writes nothing (Q12(a)).
2. **Given** two claims whose spellings name one object, through
   `repos.tsv`'s aliases, **When** the second commits, **Then** it is refused
   with LR005 (D19).
3. **Given** two `HOLD`s, or two `LANDING`s, on one repository's `main`,
   **When** the later commits, **Then** it exits 2, writes nothing and prints
   the earlier line (D18, under Q17(B)).
4. **Given** a `LANDING` more than 30 minutes old, **When**
   `append-line --landing-expired` runs on Brett's word, **Then**
   `LANDING EXPIRED` releases that one `LANDING`, and `who --landing` stops
   listing it (F13).
5. **Given** a typed `LANDING EXPIRED` line, a take with no readable UTC, or
   any mode but primary, **When** `append-line` runs, **Then** it refuses
   with 2 before the lock.
6. **Given** the imported history's 4,465 hold-shaped lines, **When** both
   classifiers read them, **Then** they agree. A difference is a finding
   before the cutover.

**OpenSpec requirements**:

- "Claim order is commit order";
- "`HOLD` and `LANDING` are ordered by commit sequence";
- "Which lines take and release `main`";
- "A `LANDING EXPIRED` releases one stale `LANDING`, or writes nothing";
- "A stale `LANDING` is released by `append-line --landing-expired`, on
  Brett's word".

---

### User Story 4 - Readers decide from the database, and fail closed (Priority: P1)

Every read that decides something reads the register's text at the head,
proven by per-file digests in the same request, and runs today's parser on
it (D17). One verb reads one snapshot. A read that fails refuses by its class:

- a deciding read with 69;
- the guard with 2;
- `managed-projection` with 1.

Only the listings and the `SessionStart` block may fall back to the mirror,
and they say so. An act takes a base with `act-begin`, and its later writes
carry the scopes its reads covered.

**Why this priority**: S3. A stale or wrong read on primary would undo what
User Story 2 secures.

**Independent Test**:

- fake-`psql` tests of each class and each default arm (C5);
- real-engine tests of the read path, `act-begin` and the expectation check
  (AT-F5);
- test 11's mid-day stop, in phase G.

**Acceptance Scenarios**:

1. **Given** primary, **When** a verb reads, **Then** it makes one
   `register read --prefetch`, and every read site reads that cache at one
   head `n`, never `origin/<branch>`.
2. **Given** a failed read, **When** a deciding read, a listing, the guard
   and `managed-projection` each run, **Then**:
   - the deciding read exits 69, with nothing on stdout;
   - the listing reads the mirror and prints `read: mirror`;
   - the guard exits 2;
   - `managed-projection` answers 1 and never `legacy`.
3. **Given** `lane-start`, `lane-end` and `lane-worktrees`, **When** they read
   the register, **Then** they do it through a register verb, never with awk
   over `$LANES_FILE` or `git grep` of `origin/<branch>`.
4. **Given** a 69, 75 or 3 at a default arm, **When** it is returned,
   **Then** the arm refuses or passes the code, as "The callers' default
   arms" lists. At `lane-start`'s seven read arms a 75 or a 3 refuses the
   launch.
5. **Given** an act with a base from `act-begin`, **When** its later write
   commits, **Then**:
   - a journal entry made after the base, outside the act, that touched a
     listed scope refuses the write with 2 (LR009);
   - the act's own earlier writes never refuse it.
6. **Given** the database unreachable, **When** `lane-start`,
   `lane-handoff` or the handoff skill runs, **Then** it ends as its
   requirement says. `lane-handoff` and the skill take their naming reads
   from the mirror, with `LANES_READ_MIRROR=1`.
7. **Given** the database unreachable, **When** `lane` or
   `lane-end --retire-dormant` reads `lanes` to act on its answer, **Then**
   the read decides, because the caller passes `lanes --decide`, and it
   refuses with 69 (spec:634; R38).
8. **Given** primary and a read site with no prefetched file, **When** a
   verb reaches it, **Then** it makes one more `register read`, never a git
   read: at the current head `n′`, with the family refetched there, for
   every primitive but the guard; at the prefetched head `n`, for the guard
   (Y:4900; Y:4159–4161; O17).

**OpenSpec requirements**:

- "Readers read the database and fail closed";
- "Callers' own reads of the register go through verbs";
- "Reads that decide nothing may read the mirror";
- "A reader says which store answered";
- "`managed-projection` answers from the database and never says `legacy` for
  an outage";
- "`lane-start` with the database unreachable";
- "The busy and in-doubt codes refuse the launch at `lane-start`'s seven
  default read arms";
- "`lane-handoff` and the handoff skill with the database unreachable";
- "The managed seam stays the ledger's".

---

### User Story 5 - The guard answers within its 5 s, and answer 2 binds in the background (Priority: P1)

The `UserPromptSubmit` guard decides every prompt inside its 5 s hook,
whatever the database does. The person's answer 2, "bind this window to that
lane", returns at once with "BINDING". The bind runs detached, as one
`start-record` write, and the next prompts report how it went.

This fixes opensoft/openRepoTools#347's two defects in every mode:

- today's bind is cut down by the hook's timeout partway;
- a failed bind cannot be answered again.

**Why this priority**: a killed hook passes the prompt, so the guard must
never overrun. Today's answer 2 already does, so its fix is phase 1's first
guard pull request.

**Independent Test**:

- test 10's three failure shapes, with the service down (the service is
  built beside groups A and B, and these hold without it);
- AT-F6's twenty-two items;
- the hook string read byte for byte.

**Acceptance Scenarios**:

1. **Given** a database address whose connect never completes, a listener
   that never speaks, or a read held behind a lock, **When** a prompt
   arrives, **Then** the hook exits 2 inside its 5 s (agreement test 10).
2. **Given** the guard's budget, **When** its lines are read, **Then** its
   last line, `T0 + 4.4 s`, is below `GUARD_TIMEOUT`, and the hook string
   and `GUARD_TIMEOUT` are unchanged.
3. **Given** answer 2, **When** it is given, **Then**:
   - it exits 2 with "BINDING" within 0.1 s after the guard's read;
   - the `setsid` child survives a group SIGKILL and SIGHUP;
   - each prompt meanwhile gets "BINDING IN PROGRESS" and is not passed.
4. **Given** the bind fails, **When** the next prompt arrives, **Then**:
   - the window is renamed back;
   - the offer is kept;
   - choice 2 is withdrawn after the third failure.
5. **Given** a bind on primary, **When** it commits, **Then** it is one
   journal entry. In files mode it is one register commit and one handoff
   commit. A row change injected after `act-begin` refuses it with 2.
6. **Given** the database down and no declaration, **When** a prompt
   arrives, **Then** the guard exits 2.
7. **Given** a declaration on Brett's word, **When** a prompt arrives,
   **Then** the guard answers from the mirror only where SVC-12's three
   conditions hold (D20).
8. **Given** a rollback, **When** its start declares the outage, **Then**
   the declaration records the rollback's head `k`.
9. **Given** `CLAUDE_NO_LANE=1`, **When** a prompt arrives, **Then** it is
   passed as today.
10. **Given** a hook's inherited environment, **When** it is read, **Then**
    it carries no database URL and no password.
11. **Given** a workstation whose link to the server is down, **When** a
    prompt arrives, **Then** the guard answers 2 until the link returns or
    an outage is declared. This is Brett's "Block until link returns", with
    no design change.

**OpenSpec requirements**:

- register-service:
  - "The `UserPromptSubmit` guard is the service's client";
  - "The `CLAUDE_NO_LANE=1` exception is kept";
  - "The guard falls back to the database, never the mirror";
  - "The 5 s is one budget, spent once";
  - "The hook's environment carries no credential";
  - "The guard fails closed during a database outage";
  - "A declared outage lets the guard answer from the mirror";
  - "`outage declare` during a rollback records the rollback's head";
- sql-register:
  - "`lane-start`'s bind is one write".

---

### User Story 6 - The mirror is a faithful, write-behind render (Priority: P1)

After each committed write, a detached renderer in the writing lane's
checkout builds one mirror commit per seq on `origin/main`. It builds them
with a private index, never touching a HEAD, an index or a working tree, and
pushes them.

- A lost race leaves nothing to discard.
- A killed renderer blocks nothing.
- A commit that the renderer did not make, to a rendered path, stops it.
  Each way out of that divergence has a tool.

**Why this priority**: the files stay the estate's readable record, and the
way back from SQL. No estate can switch until this half exists: the constant
of User Story 1 lifts only with it.

**Independent Test**: in phase G, on the fixture with a bare-repo origin:

- tests 5 and 6;
- test 1's mirror part;
- AT-F1, AT-F2 and AT-F4;
- C8.

**Acceptance Scenarios**:

1. **Given** writes at seqs `m+1` to `t`, **When** the renderer runs,
   **Then** origin carries one commit per seq, each with its entry's message
   and `Register-Seq: <k>` in its final trailer block, dated and authored as
   the journal recorded (D15).
2. **Given** two renderers from one tip, **When** both run, **Then** they
   build identical commits, and the second push is a no-op or a refetch
   (agreement test 5).
3. **Given** a renderer killed between `commit-tree` and its push, **When**
   the next fork runs, **Then** it renders on, and no lock is left behind
   (D14).
4. **Given** a burst within one pass, **When** it is rendered, **Then** it
   takes at most two renders, the last write included.
5. **Given** a pass, **When** it runs, **Then** it renders at most 64 seqs
   and makes at most six push attempts. The renderer re-forks once after 16
   passes, and only after progress (F15) `[PLAN CHOICE: the renderer's batch
   and cap]`.
6. **Given** a stray commit to a rendered path, or a `Register-Seq` above the
   head, **When** the renderer meets it, **Then** it stops and records
   `diverged at <sha>`. Then `import --single <sha>` or
   `render --revert <sha>` resumes it, and the mirror is byte-identical to a
   full render after it (agreement test 6).
7. **Given** a commit outside the render set, such as a handoff or
   `lanes/repos.tsv`, **When** the renderer meets it, **Then** it is part of
   the tip.
8. **Given** an operator seq next, **When** the renderer reaches it,
   **Then** it renders nothing past it (F2).
9. **Given** the renderer's push, **When** it is made, **Then**:
   - it goes to an explicit SSH URL in an environment of its own, under the
     renderer identity where `render_key=` is set (D23);
   - nothing is written to the checkout's configuration;
   - the read-back proves it.
10. **Given** a write on primary, **When** it commits, **Then** the register's
    post-write nudge is suppressed, and the renderer runs without
    `LANES_NO_FETCH`.

**OpenSpec requirements**:

- "The renderer commits one mirror commit per sequence";
- "The renderer commits in the writing lane's checkout under S3's fetch";
- "The mirror is write-behind";
- "A renderer that loses a push race discards and re-renders";
- "The renderer leaves nothing on a lane's HEAD";
- "One render at a time per checkout, and no fork waits";
- "The renderer does not inherit `LANES_NO_FETCH=1`";
- "A pending hand edit has a way out on `mode=primary`";
- "Commits that touch none of the rendered paths are part of the tip";
- "A commit the renderer did not make to a rendered path stops it";
- "A `Register-Seq` above the journal's head stops the renderer";
- "Each way out of a divergence has a tool";
- "`status` and `reconcile` prove the mirror";
- "The post-write nudge does not re-index the mirror into its own source";
- "A server-side gate protects the rendered paths and `lanes/STORE`".

---

### User Story 7 - The operator moves the estate in and out (Priority: P2)

The operator, Brett Heap or a lane on his word, has a tool for each step of
acts 3 and 4, the rollback and the disaster path:

- `bootstrap`, `login`, `ready` and `set-mode`;
- `import --at` and `cutover`;
- `rollback`;
- `outage declare` and `outage clear`.

Each tool:

- holds no secret;
- is idempotent where it can be;
- proves before it switches;
- refuses rather than guess.

**Why this priority**: 007 builds and proves these tools on fixtures. Running
them on the real estate is acts 3 and 4, which are outside this feature and
wait on the install.

**Independent Test**: real-engine, fake-`psql` and files-mode tests on the
fixture: tests 7, 8 and 13, P8, D22, and AT-F18.

**Acceptance Scenarios**:

1. **Given** the register at commit `C`, **When** `import --at C` runs,
   **Then** the scratch render is byte-identical to `C`'s render set before
   anything is swapped in. Otherwise it refuses, and the 15 unreadable
   `WORD` lines are kept verbatim and flagged (agreement test 7).
2. **Given** an import, **When** `cutover` runs, **Then**:
   - one short transaction sets the switch to `primary` and writes the
     cutover's entry at the next seq;
   - outside any transaction, the marker commit is pushed and read back.

   Where the `lanes` tree has moved, it aborts, setting the switch back to
   `files`.
3. **Given** a rollback, **When** it runs, **Then**:
   - it refuses while a divergence is recorded;
   - it needs the database;
   - the files end byte-identical;
   - the marker is gone;
   - a writer the rollback missed exits 69, the journal unchanged (agreement
     test 8).
4. **Given** the bootstrap run twice through the admin service, as the
   database's owner that CNPG's `initdb` made (Y:2283), **When** the second
   run ends, **Then** one schema and the same GRANTs are left. It prints no
   secret unless stdout is a terminal. Each engineer's two logins, and the
   dump job's, are made, rotated and revoked by `lanes-index login`, never
   by CNPG's declared roles (Y:2285; ops:477).
5. **Given** each failure in `ready`'s list, **When** `ready` runs, **Then**
   it fails it, among them:
   - a stale digest;
   - a missing `psql`;
   - an early `mode=primary`;
   - a GRANT out of scope;
   - the constant still in place;
   - tools outside the schema's window;
   - check 11: a URL without `sslmode=verify-full` and the root file of the
     issuer, the prod OpenXPKI, or with `sslrootcert=system`, or a chain that
     does not verify against that root (ops:454; Y:2324);
   - check 13: an endpoint whose address count differs from
     `register_addresses=`, which the install's one A record makes 1
     (ops:456; ops:151).
6. **Given** a dump of both schemas restored into a new database, **When**
   `bootstrap --roles-only` has run before the restore, **Then** PUBLIC holds
   no CONNECT on it, the writer and reader groups and the indexer's login
   do, and the owner's global default privilege revokes EXECUTE from PUBLIC
   (the dump route's (a); Y:2243–2244; Y:2256–2257).
7. **Given** each role, **When** it writes outside its scope, **Then** the
   database refuses it.
8. **Given** the manual, **When** it is read, **Then** it states the
   REGISTER-WRITER secret's reach plainly.

**OpenSpec requirements**:

- "`lanes-index import --at` proves the render before it loads";
- "The import takes origin only and keeps the unreadable lines verbatim";
- "The cutover is one guarded sequence";
- "The cutover takes its database label from the workstation's
  configuration";
- "Rollback returns the estate to the files";
- "A rollback needs the database";
- "A writer the rollback missed cannot write";
- "The operator's bootstrap is idempotent and holds no secret";
- "The readiness check proves an environment before the cutover";
- "The `mode=primary` flip is a command run once per host";
- "The three roles";
- "The REGISTER-WRITER secret's reach is stated plainly".

---

### User Story 8 - The register service answers reads in milliseconds (Priority: P2)

One `lanes-index serve` process per environment holds one READER connection.
It answers the register's text at `n`, proven by digests, and the reader
decides. The guard and the readers ask it first, and read directly when it
does not answer. No write depends on it.

**Why this priority**: act 4 waits for the service, since from Eagle the
guard without it takes about 1.2 to 2.1 s, which misses Brett's 1 s
(Y:335–336; Y:4826). So the service is built beside groups A and B, and
the v1.2.0 tag waits for it: Brett's "4A" (6726766577; svc:293;
Y:3951–3956). It is P2 because no write depends on it, and every reader
still works without it.

**Independent Test**: test 10 through the service:

- byte-identical answers;
- the fallback line;
- the service's answer under 50 ms on the fixture register.

**Acceptance Scenarios**:

1. **Given** the service up, **When** `who` runs, **Then** its answer equals
   the direct read.
2. **Given** a write between two requests, **When** the second is answered,
   **Then** it includes that write.
3. **Given** the service down, or accepting and never answering, **When** a
   client calls, **Then** the client reads directly, and writes go on.
4. **Given** a first call where the service can start, **When** it is made,
   **Then** the service starts with no supervisor, and answers.
5. **Given** the package metadata, **When** it is read, **Then** it lists no
   runtime dependency and no database driver.
6. **Given** the `SessionStart` block, **When** it runs, **Then** it reads
   the mirror and only starts the service.
7. **Given** the PR body, **When** it is read, **Then** it records the
   service's answer time with the whole guard's time, through the service
   and directly, beside today's direct guard.

**OpenSpec requirements**:

- "The service serves the reads and the guard, and writes do not depend on
  it";
- "Every answer that decides state is read fresh";
- "The service reaches the database only through `psql`";
- "The service starts on demand with no supervisor of its own";
- "The guard's answers through the service equal the direct read";
- "The `SessionStart` block reads the mirror and only starts the service";
- "A service that accepts and never answers counts as unavailable";
- "The service answers the guard's read fast, and the cost is recorded";
- "The cutover waits for the service only if the guard needs it".

---

### User Story 9 - The evidence holds on a real engine (Priority: P2)

The agreement tests that only an engine proves run on a disposable local
PostgreSQL 16 cluster, in CI, which refuses rather than skips them. The cost
of a write, and what a lane feels, are measured and recorded beside files
mode. A lane's day and the drill run end to end.

**Why this priority**: every story above names its tests. This story is the
fixture, CI's refusal and the end-to-end runs that hold them together.

**Independent Test**:

- with `OPENREPOTOOLS_REQUIRE_PG=1` and no engine, pytest exits 4;
- without the switch, the real-engine tests skip, naming `PG_BIN` and
  `postgresql-16`;
- tests 11 and 13 pass on the fixture.

**Acceptance Scenarios**:

1. **Given** a CI job with the PostgreSQL binaries that cannot start the
   cluster, **When** it runs, **Then** it fails, and does not skip.
2. **Given** a host without PostgreSQL 16, **When** the suite runs, **Then**
   the real-engine tests skip with that reason, and the fake-`psql` tests
   still run.
3. **Given** the pull requests that change the cost, **When** each is
   opened, **Then** its body records the medians and p95s of test 9, warm
   and cold, on a named host, store and path, beside files mode.
4. **Given** a lane's day, **When** it runs, **Then** after each act the
   mirror equals a full render, with one commit per seq (agreement
   test 11).
5. **Given** the drill, **When** it runs, **Then** every step ends as
   stated, and a workstation not yet cleared exits 69 (agreement test 13).
6. **Given** the hygiene suite, **When** it runs over the new code, **Then**
   it passes under its bash-3.2 rules and standard-library-only imports.

**OpenSpec requirements**:

- sql-register:
  - "Agreement tests run on a real engine in CI";
  - "The cost of a write is measured and recorded";
  - "A lane's day on `mode=primary` is tested end to end";
  - "The drill covers the outage and the way back";
- register-service:
  - "New code keeps to the toolchain floor".

---

### User Story 10 - The record says what changes (Priority: P3)

A lane, or Brett, opens `docs/README-lanes.md` and finds "The SQL register",
beside "The derived index (Amendment 14)". It opens with "What changes for a
lane", and holds the socket API and the runbook for acts 3 and 4 and the
rollback.

**Why this priority**: the manual follows the mechanisms, and must not run
ahead of them.

**Independent Test**: read the section for each item that its requirement
lists. The runbook's server, backup and restore steps follow the install
contract (ops:133) and the disaster path (ops:355) at Y.

**Acceptance Scenarios**:

1. **Given** the section, **When** a lane opens it, **Then** its first part
   is "What changes for a lane", covering each of these:
   - 69;
   - 75 as "nothing was written, and a retry in seconds is safe";
   - 3 and its printed rerun;
   - the guard in an outage;
   - `commit` and hand edits refused;
   - the mirror's lag;
   - the claim's seq;
   - the loser's 2.
2. **Given** the manual, **When** it is read, **Then** it documents the
   socket API: the service answers the text at `n`, and the reader decides.
3. **Given** the runbook, **When** it is read, **Then** it names every act,
   word, command and owner, in order, the rollback's triggers among them.
4. **Given** the derived-index section, **When** it is read, **Then** its
   "No test creates a real database" (`docs/README-lanes.md:3981` at W's
   spec leg) is corrected.

**OpenSpec requirements**:

- sql-register:
  - "The manual says what changes for a lane";
  - "The runbook for acts 3 and 4 and the rollback";
- register-service:
  - "The socket API is documented".

---

### Edge Cases

- **A COMMIT in doubt that commits late.** An orphaned call still queued at
  the server could commit after the writer gave up. Every commit call carries
  `not_after`, and `commit_write` refuses past it (LR008). So once
  `find_write` holds the head row's lock with its clock check passed, no call
  of the write can still commit.
  - One residual stays: a server clock that steps back between the lookup's
    check and an orphan's step 9.
- **A restart of the server's one instance, longer than the 10 s reconnect
  window.** The write ends with 3, the pending record kept, and its rerun
  writes it once or finds it. With one instance and no standby, a restart
  in the node's maintenance window, a node drain or a parameter that needs a
  restart is such a case, and the guard answers 2 meanwhile (Y:5907;
  Y:3333–3335; ops:145).
- **A rerun after the database was replaced.** A write id, or a pending
  record, from another generation is refused with 2. The refusal prints the
  effect check, the steps that may be owed, and `LANES_WRITE_ID=new`. It is
  never applied twice.
- **A stale writer**: an old install that does not know the marker.
  - Its push to a rendered path stops the renderer, and S2's two ways out
    resume it.
  - Under Q21(b)(B) the ruleset refuses it first, where the organisation's
    plan supports such a ruleset (a fact still to find).
- **A `Register-Seq` copied or forged past the head.** It is a divergence at
  the commit that carries it. `render --revert` is the way out.
- **`LANES_INDEX=off`.** `lane-worktrees` sets it on every `lanes-edit.sh`
  call it makes. It never skips the mode query or the renderer's fork.
- **`LANES_NO_FETCH=1` on primary.** It skips nothing on a write, weakens no
  switch check, and is removed from the renderer's environment (D12).
- **A host without `setsid`, such as macOS.** The detached bind falls back to
  python3's `os.setsid`, then to `nohup` alone. Brett accepted the gap, which
  is macOS without python3 (lane 4's row 40), as recommended (#165
  6721583757; Y:6364–6372).
- **A host without `timeout`.** The bind's body leads its own process group,
  and the record says `bounded=no`. Each register call is still bounded by
  its own timer.
- **A bench container started after the readiness check.** It is not
  covered, and the check says so. The runbook covers it.
- **A burst at a far workstation.** At 300 ms of RTT, a burst of ten passes
  D7's 30 s. Eagle's round trip to Central US measured 456 ms at the p90
  (Y:334–335).
  - Act 3's test 4 at each workstation's own RTT decides.
  - A per-workstation D7 is lane 4's proposal, and goes to 007's tasks or
    to v1.2.1 after the freeze.
- **A `LANDING EXPIRED` typed by hand in files mode, then imported.** Lane
  2's AT-F13 (j) holds each imported line to F13's condition, or reports it
  as a finding before the cutover.
- **A stray landing during a rollback,** after its last divergence check.
  Brett accepted it as S5's remaining gap, as recommended (#165 6721583757;
  Y:6364–6372). No journalled way out exists.
- **The workstation's link down.** The guard answers 2 until the link
  returns or an outage is declared. This is Brett's "Block until link
  returns".
- **A second register write in one run.** It exits 2, naming the first
  write's seq. One run makes one journal entry.

## Requirements *(mandatory)*

### Functional Requirements

Each requirement cites the OpenSpec requirement, decision or section that
fixes it, by name and by its line at Y. It does not restate them. Where the
OpenSpec texts and this file differ, the OpenSpec texts govern until a
feature amendment says otherwise. Where a ruling of lane 3's or Brett's shapes
a requirement, research.md records it under the ruling's number (R01 to R61
are lane 3's rulings memo's).

#### Configuration and the mode

- **FR-001**: `lanes-index` MUST stay the one parser of `lanes-index.conf`.
  - Its known keys MUST gain:
    - `mode`;
    - `register_url` and `register_passfile`;
    - `register_label` and `register_schema`;
    - `render_key`;
    - `register_addresses` and `register_rtt_ms`.
  - A password in `register_url` MUST be refused, as it is in `url` today.
  - `mode=primary` with SQLite MUST be refused.
  - Sources: "`mode=` selects the register's store" (spec:9); the phase
    plan's `load_config` row (Y:4849); "Over a remote database" (Y:5870).
- **FR-002**: `lanes-edit.sh` MUST ask `lanes-index mode` only where a
  configuration file exists, or `LANES_INDEX_CONFIG` is set and not empty.
  - Every failure but 78 MUST run files mode with a warning.
  - The query's client timer is 2 s.
  - `c`, `N_a` and `w_s` MUST come from `lanes-index`'s reports of its own
    configuration: `lanes-edit.sh` parses none of it (R05).
  - Sources: D3 (Y:368); F8's table (Y:5703); spec:9.
- **FR-003**: Until the constant is set, `lanes-index` MUST refuse
  `mode=primary`:
  - `mode` and `register read` with 78 and the fixed text;
  - every other command with 2, `serve` among them (R36).

  The constant MUST be set only by group C's last pull request. Sources: "A
  build without the renderer's git half refuses `mode=primary`" (spec:86);
  D3's refusal (Y:4872).
- **FR-004**: Every writer that is not on `mode=primary` MUST check for
  `lanes/STORE` on the tree that its fetch returned, and so MUST the publish
  act. A files-mode writer under the marker MUST exit 69, writing nothing.
  - The readers' marker check (F17) MUST run on the ref that each read
    reads, in the main shell.
  - Sources: "A writer that is not `mode=primary` refuses under the marker"
    (spec:41); F17 (Y:4944).
- **FR-005**: `openRepoTools wip init` MUST refuse wherever `lanes/STORE`, or
  any file under `lanes/`, exists, in every mode (Q14(a)). Source: "`wip init`
  and the `LANES.md` link on a SQL estate" (spec:134).

#### The schema and the database contract

- **FR-006**: The register MUST live in a schema of its own,
  `lanes_register`, beside #160's `lanes_index`:
  - its own `REGISTER_SCHEMA_VERSION`;
  - floors `writers_from` and `readers_from`;
  - forward-only migrations that only the bootstrap applies.

  Sources: D1 (Y:350); §1.5 (Y:2330).
- **FR-007**: The tables, views, functions and SQLSTATEs LR001 to LR012
  MUST be §1.2's, §1.3's and §1.3.1's.
  - Writers MUST change the register only through
    `commit_write(n, write_id, REGISTER_SCHEMA_VERSION, generation, entry)`,
    `switch_to_files`, `record_marker` and `record_mirror_state`.
  - Writers MUST NOT hold INSERT, UPDATE, DELETE or DDL anywhere.
  - Sources: §1.2 (Y:1033); §1.3 (Y:1413); §1.3.1 (Y:1577); §1.4 (Y:2230).
- **FR-008**: `commit_write` MUST:
  - update the head row before the journal row (D6);
  - check the generation first, then the write id, then the switch, then the
    act (lane 1's order);
  - check the act's expectations (LR009);
  - refuse an operator entry that writes outside `lanes/STORE` (LR004);
  - raise LR008 past `not_after`.

  Sources: D6 (Y:451); §1.3.1 (Y:1577).
- **FR-009**: The roles MUST be §1.4's:
  - REGISTER-WRITER and READER are groups that cannot log in;
  - each engineer, human or AI host, has two logins, `lanes_w_<engineer>`
    and `lanes_r_<engineer>`, each a member of one group and each carrying
    that group's floors itself;
  - an owner role, `lanes_register_owner`, that cannot log in;
  - the indexer;
  - the dump job's READER login, `lanes_register_dump`.

  Each engineer's two logins and the dump job's login MUST be made, rotated
  and revoked by `lanes-index login`, through the admin service: Brett's
  "lanes-index login (Recommended)" (clar:497–498; #165 6726078173).
  `bootstrap` MUST make the two groups, the owner and the indexer's login
  (Y:2235–2238). CNPG's `initdb` makes the database's owner, Brett's admin,
  a role apart from `lanes_register_owner` (Y:2283). The dump job's
  password MUST be rotated alone by `login rotate --dump-job` (lane 3's
  ruling, 6726725691). Sources: "The three roles" (spec:1039);
  "The operator's bootstrap is idempotent and holds no secret" (spec:845–846);
  "One engineer's logins" (Y:2285); ops:142; ops:477.
- **FR-010**: Every connection MUST reach `psql` through `PG*` variables set
  in `_env`, never a `-d` URL in an argument list (Question 11).
  - Inherited `PGPASSWORD`, `PGPASSFILE`, `PGSERVICEFILE` and `PGSYSCONFDIR`
    MUST be dropped.
  - On register paths, `PGCONNECT_TIMEOUT` MUST be set, not merely
    defaulted.
  - Sources: Question 11 (Y:4967); SVC-2's mapping (Y:3751).
- **FR-011**: Every register and reader URL, and the admin's own service, MUST
  name the install's endpoint and carry `sslmode=verify-full` and an
  `sslrootcert=` that names the root of the issuer that signs the server's
  certificate, the prod OpenXPKI (Brett, 2026-11-09 14:38:23Z: "add openXPKI
  to prod and use that. both QA and Prod need openXPKI"), and never `system`
  (Y:2324; ops:153). It MUST be enforced in three places (R09):
  - `load_config` refuses, as a refused configuration, a `register_url=` or
    `reader_url=` at a TCP host that is not loopback, unless it carries
    `sslmode=verify-full` and an `sslrootcert=` that names an existing file;
    `system` is refused there. A Unix socket and a loopback host are
    allowed, and `reader_url=` is checked only where `register_url=` is set,
    so #160's files load as today;
  - `ready`'s check 11 (ops:454);
  - the server's `hostnossl all all all reject` (ops:144).
- **FR-012**: Each register subcommand MUST send its statements as one
  request: `\;`-joined, with the snapshot's marker sent as a `COPY`. `psql`
  15 or later is required. Every register connection MUST reach the
  primary's own endpoint directly, never through a pooler, since CNPG's
  PgBouncer drops the startup `options` that carry the bounds. Sources: "Over
  a remote database" (Y:5870); the connection the bounds need (Y:1575).

#### The register subcommands: the contract between groups A and B

- **FR-013**: `lanes-index register snapshot` MUST:
  - make lane 1's checks in order;
  - return `n`, the switch with its marker state and generation, the
    database clock, and the write id's entry when found;
  - return the images of `--files` or `--all` at `n`;
  - build the scratch tree with `--tree <dir>`, or at seq `k` with
    `--at <k>`.

  Source: the phase plan's interfaces table (Y:4831).
- **FR-014**: `lanes-index register commit` MUST:
  - take the attempt directory's format: paths, renames, removals, images,
    base digests, the message, the author, the act's expectations, and the
    `repos.tsv` blob id with its alias sets;
  - carry `not_after`;
  - exit 0 with `committed <seq>`, or 75, 69, 2 or 3;
  - on LR004, retry once only where each path's base, re-read from SQL,
    equals the scratch tree's, and otherwise exit 69 naming the image cache;
    the retry's second call is read by the commit's exit table in full (R02).

  Sources: the interfaces table (Y:4832); LR004's row (Y:1567; Y:2614).
- **FR-015**: `lanes-index register read` MUST return the text at a head
  with per-file digests, and the `n` and generation it read in that
  snapshot, in four forms (R06; O17; lane 1's contract draft 4.1, §3.6):
  1. the readers' prefetch, `--prefetch --cache <file>` with `--files` or
     `--all`, at `n`;
  2. the guard's prefetch, `--for guard` with `--pid`, `--repo` and
     `--lane=<key>`, at `n`;
  3. the second read of a primitive with no prefetched file,
     `--since <n>`, which returns the paths changed in `(n, n′]` and
     refetches the cache family at `n′` (Y:4900);
  4. the guard's second read,
     `--for guard --at <n> --pid <pid> --cache <file> --lane=<key>`, which
     reads one log at the prefetched `n`, takes the generation from `.head`,
     and refetches nothing (Y:4159–4161).

  Form 4 MUST always read directly, never through the service, and MUST
  treat every end but 0 as a log that could not be read, which the guard
  notes and does not refuse; the prefetch, form 2, keeps its "too late"
  refusal with 2 (B4 and B7, lane 3's rulings, 6738474549; Y:4269–4271).
  `register read`'s `--lane` and `--log-key` take their value only in the
  `=` form, so that a key opening with `-` is never read as a flag (L1-D1,
  lane 1's decision); group B's `--lane <lane>`, on `register snapshot`,
  `register commit` and `journal --write-id`, keeps the interfaces table's
  form (Y:4834). Exits: 0, 69, 78 or 2, and 1 under `--for guard` for a
  refused configuration, as the contract fixes them per form. Source: the
  interfaces table (Y:4833).
- **FR-016**: `lanes-index journal` MUST:
  - look a write up by `--write-id`, `--not-after`, `--generation`,
    `--lane`, `--verb` and `--args-sha256`, through `find_write`, taking
    `--clock` and `--clock-age` from a pending record as well as from the
    run's own last snapshot (R26's point 36);
  - wait first on its own estimate;
  - take D9's lookup bounds, its tries spaced start to start inside a
    closed 10 s window (R26's point 40);
  - also serve `--since`, for the journal's history, and
    `--last --path <log> --grep <key> [--at <n>]`, whose key matches as a
    fixed string, as git's `-S` pickaxe does, never as a regex (lane 1's
    contract draft 4, on lane 5's C7 and C8).

  Source: the interfaces table (Y:4834).
- **FR-017**: The contract of FR-013 to FR-016 MUST be fixed in a contract
  file of this feature, `contracts/register-cli.md`, before group B codes
  against it. Lane 1 writes it, lane 4 confirms group B's sections, lane 5
  reviews the readers' forms, and lane 3 rules (R01, R06). Group B's tests
  use the fake `psql`, and a stub `lanes-index` that implements the
  contract's group B commands exactly. Source: the interfaces between the
  phases (Y:4841).

#### The write path

- **FR-018**: On primary, the fourteen writing verbs of the write guard, and
  `start-record`, MUST run S1's attempt loop until D7's 30 s deadline. Each
  attempt:
  1. takes the snapshot and builds the scratch tree;
  2. runs the verb as a child process under `LANES_REGISTER_SCRATCH`, which
     stops at the commit seam with the change set and the message;
  3. makes the commit call;
  4. decides from its answer.

  Each writer process MUST ask the mode once, before slice 1a's routing. On
  primary it MUST NOT enter the lane checkout by 1a's routing: it takes `<c>`
  from `lane_checkout_path`, with no S3 work, and refuses with 2, naming
  `lane-worktrees`, where that checkout does not exist (R17). Sources: "How
  an attempt runs the verb" (Y:4981); the primary branch (Y:4983); D7
  (Y:455); D12 (Y:522).
- **FR-019**: The commit seam's primary branch MUST keep every content check,
  and MUST refuse with 2 any staged path outside the write's pathspecs or
  the render set. It MUST collect the change set and never commit, pull or
  push. It sits on slice 1b's `commit_publish`. Sources: D4 (Y:383); the
  seam's primary branch (Y:5054).
- **FR-020**: The scratch tree MUST be built from images, and MUST never
  hardlink a work-tree file. `lanes/repos.tsv` and `lanes/log/README.md` MUST
  come from the lane checkout's `origin/main`, by recorded blob ids. Source:
  the scratch tree (Y:5046).
- **FR-021**: A write MUST keep a pending record from its first snapshot
  until its outcome is known:
  - its owner by claim files;
  - a 15-minute age for its generation;
  - a done mark;
  - `call` lines that alone make the write "in doubt".

  The printed rerun MUST carry `LANES_WRITE_ID` and `LANES_WRITE_GENERATION`
  where the write has an id, a pending record or `LANES_WRITE_ID`. A new
  write whose first snapshot ends 69 has none: it MUST say that nothing was
  sent, and remove its own claim (R26's point 38; spec:318). Source: F10
  (Y:5169).
- **FR-022**: A write in doubt MUST end with no code but 0 until F3's lookup.
  - Found: it exits 0 and runs the tail once.
  - Absent with the lock held: the code its last attempt earned.
  - LR007: 2.
  - No lock within the window, or LR011: 3, naming the write id (D8).
  - A takeover whose own snapshot failed MUST wait on the record's clock
    before its lookup (R26's point 36). A claim with no record beside it,
    whose owner still lives at the deadline, MUST exit 3 naming the claim
    and giving the effect check (R26's point 42).

  Sources: the in-doubt rule (Y:5226); D8 (Y:463).
- **FR-023**: Each verb's after-COMMIT tail MUST run at most once per write
  id, in the kept child or in a replay. A tail that cannot run MUST be named
  as owed, with its command. Source: "The tail, once" (Y:5289).
- **FR-024**: Only the deciding attempt's output MUST be replayed. Every side
  effect outside the register MUST run after the seam (F14). `gh_reads` MUST
  be cached across attempts. Source: F14 (Y:5144).
- **FR-025**: `start-record` MUST make `lane-start`'s three register writes
  one write verb in both modes: one transaction on primary, and one commit
  and push in files mode. Its exits MUST follow "`lane-start`'s bind is one
  write" (spec:317–318): where it makes no row, a 69, a 75 or a 3 prints a
  note carrying the writer's printed rerun, its generation included where
  the write has an id, or saying that nothing was sent where it has none.
  Source: "The bind as one verb" (Y:5521).
- **FR-026**: On primary, `lanes-edit.sh commit`'s carry form, the hand edit
  of Rule 9(d), MUST be refused with 2, as every other direct write to the
  files is (D10). The publish of queued commits MUST stay, outside the
  attempt loop, with its fence narrowed to `handoffs/`; a queued commit under
  `lanes/` MUST be refused as a divergence; and a push whose commit changes a
  path outside `handoffs/` MUST be refused with 2 at the push (R16).
  - A git act whose paths are all under `handoffs/` keeps S3's fetch: D12
    governs register writes (R16).
  - `register-row-local` MUST read the database (D11).
  - Sources: "Direct writes to the files refuse on `mode=primary`"
    (spec:168–169); D10 (Y:508); D11 (Y:515).
- **FR-027**: After COMMIT, the parent MUST:
  - fork `lanes-index render --checkout <c>`, detached, without
    `LANES_NO_FETCH`;
  - pass the author identity;
  - suppress the register's nudge.

  `LANES_INDEX=off` MUST NOT skip the fork or the mode query. Source: "What
  phase 2 ships for phase 3" (Y:5375).
- **FR-028**: The exit table's row for 3 MUST gain its primary clause (D8),
  and the manual's copy of that row MUST change in the same landing (R21).
  Every caller that reads 3 as "written" MUST relay the writer's printed
  rerun (F9). Sources: D8 (Y:463); F9 (Y:5694).
- **FR-029**: The scripts' writes MUST carry `LANES_EXPECT` under their act's
  `LANES_ACT`. D5's three conditions are requirements:
  - `lane-start` re-bases and re-reads at its second `binding` read, and any
    2 at W7's `log` arm dies (cited by function, not by line: R32);
  - the skill's step 4 re-derives the identity;
  - `lane-end`'s gate inherits `LANES_ACT`.

  Sources: D5 (Y:387); the acts and their expectations (Y:5567); `lane-start`
  (Y:5627).

#### Claim and hold order

- **FR-030**: `REGISTER_KIND_RE` MUST grow to Q17(B)'s kinds and
  `LANDING EXPIRED`, as the one classifier inside the commit. `append-line`'s
  refusals on primary MUST classify through `lanes-index register classify`,
  that same classifier, and never through a copy in bash (R04). Sources: S6
  (Y:3345); D18 (Y:574); "the one classifier" (Y:5116); the second
  classifier's risk (Y:6247).
- **FR-031**: `claim` on primary MUST print `register seq <n>` (Q19(A)).
  `CLAIM-LOST` is unreachable on primary. Source: "A claim's comment cites
  the register sequence" (spec:377).
- **FR-032**: `append-line --landing-expired` MUST copy the holder's
  `LANDING` fields byte for byte, and MUST refuse with 2 before the lock in
  every case F13 lists.
  - On primary, `RULE6_PRIMARY` MUST add `RULE6_EXPIRED_AWK` to `RULE6_AWK`
    once, in the main shell.
  - Off primary, `RULE6_AWK` MUST stay byte-identical.
  - Sources: "A stale `LANDING` is released by
    `append-line --landing-expired`, on Brett's word" (spec:1006); F13
    (Y:5099).

#### The readers

- **FR-033**: The read door, `reg_open`, MUST run once in the dispatcher's
  main shell, before the arm. It MUST make one `register read --prefetch`
  that also answers the mode.
  - It MUST write the cache family at one `n`: `.register`, `.paths`,
    `.archive`, `.aliases`, `.log.<lane>` and `.logpath.<lane>`.
  - Every dispatcher arm MUST be in exactly one of six classes, and a
    hygiene test MUST hold that: decide, mirror, guard, projection and none
    (Y:4892–4898), and `writer`, for the write guard's verbs, `start-record`
    among them (R46).
  - `lanes` is a mirror read, and decides under `lanes --decide`, which the
    callers that act on its answer pass (R38; spec:626).
  - The `SessionStart` block is a mirror read with no register call (R39).
  - Source: the readers' SQL path (Y:4888).
- **FR-034**: Each read site in the phase plan's table MUST read its cache
  file on primary, and MUST be unchanged in files and `index` mode. Sources:
  the readers' SQL path (Y:4888); "Readers read the database and fail
  closed" (spec:573).
- **FR-035**: `act-begin` MUST print `<act-id>@<n>:<generation>` on primary
  and `<act-id>@files` off it, with `--rebase`.
  - The client's checks on `LANES_ACT` and `LANES_EXPECT` MUST refuse with 2
    before any database call.
  - `canon-lane --walk` MUST print the walk's names.
  - Source: the acts and their expectations (Y:5567).
- **FR-036**: The lifecycle scripts' direct reads MUST move behind register
  verbs, as must the skills' reads under S3:
  - `lane-start`'s and `lane-end`'s awk over `$LANES_FILE`;
  - `lane-worktrees`' three `git grep` reads.

  Every default arm MUST be as "The callers' default arms" lists. Sources:
  "Callers' own reads of the register go through verbs" (spec:614); the
  callers' default arms (Y:5720).

#### The guard and the bind

- **FR-037**: `guard_run` MUST make one `register read --for guard --prefetch`
  a prompt, within SVC-8's budget lines (`T0 + 3.75 / 4.25 / 4.4 s`). On a
  failed read it applies SVC-12's three conditions. A miss on a log is form
  4 of FR-015. In files and `index` mode it makes the marker check. Sources:
  SVC-8 (Y:4116); SVC-12 (Y:4439); the guard's phase-1 row (Y:4936).
- **FR-038**: Answer 2 MUST run detached in every mode, as phase 1's first
  guard pull request:
  - the offer file's `mode=binding` record;
  - `guard-bind` in a `setsid` child, under its computed `bound_s`, which it
    sums from the `c`, `N_a` and `w_s` that `lanes-index` reported (R05);
  - `pid_start_of` as the liveness test, an unreadable start counting as
    live (R49);
  - the eleven-case table;
  - the rename back on failure, at most three attempts.

  Sources: the detached bind (Y:5391); "The 5 s is one budget, spent once"
  (svc:123).
- **FR-039**: `lane-start --bind` MUST be idempotent: it writes no register
  line again where the binding and the cell already name this uuid. Source:
  "Reruns" (Y:5511).
- **FR-040**: The hook string, `GUARD_COMMAND`, `GUARD_TIMEOUT` and the
  `timeout` that each profile carries MUST be byte-unchanged in every phase.
  Source: Y:4942.
- **FR-041**: On primary, the bind child MUST make F3's lookup with the
  record's five copies before a rerun (phase 2). Source: AT-F6's phases
  (Y:5559).

#### The renderer

- **FR-042**: `lanes-index render --images --at <k> --out <dir>` MUST build
  the render set's images at any seq from the latest import on, with no git.
  The images MUST be byte-identical to a full render. Sources: S2's journal
  (Y:2791); "The renderer commits one mirror commit per sequence"
  (spec:416).
- **FR-043**: The image cache MUST be safe for any writer `[PLAN CHOICE: the
  image cache's form]`. Source: Question 13 (Y:4960).
- **FR-044**: `lanes-index render --checkout <c>` MUST:
  - take an `fcntl.flock` on `<git-dir>/lanes-render.lock`, with both halves
    of `SyncLock`'s handshake (D14);
  - run S2's pass: fetch; find `m` by form C, paged and bounded; check for a
    divergence; take `t = min(h, m + 64, o − 1)`; build with a private index
    and `commit-tree --no-gpg-sign`; push and read back; six attempts;
  - compose each message itself (D15).

  Sources: D14 (Y:535); the renderer (Y:2816); the render lock (Y:5761).
- **FR-045**: The renderer, `cutover` and `rollback` MUST push through one
  push function:
  - an explicit SSH URL from `_norm_repo` of `origin`;
  - an isolated environment;
  - the render key where `render_key=` is set;
  - a read-back by fetch and ancestry.

  Sources: D23 (Y:647); the push URL (Y:5795).
- **FR-046**: A pass MUST end, leaving nothing on any HEAD, at each of F2's
  stop conditions. F15's bounds hold `[PLAN CHOICE: the renderer's batch and
  cap]`. Sources: F15 (Y:5779); F2 (Y:5789).
- **FR-047**: `render --revert <sha>` and `import --single <sha>` MUST each
  journal one entry through the commit call, and clear `diverged at`.
  Sources: the two ways out (Y:2933); "Each way out of a divergence has a
  tool" (spec:547).
- **FR-048**: `reconcile` MUST check the mirror against SQL, read-only, in
  group A, and MUST re-render what is missing in group C. `status` MUST add:
  - the switch;
  - `n`;
  - the generation;
  - the mirror's last `Register-Seq` and the lag;
  - `diverged at`;
  - pending records;
  - a declaration left in place.

  The service's own `status` lines are the service's (FR-057). Source:
  "`status` and `reconcile` prove the mirror" (spec:559).

#### Import, cutover, rollback and the disaster path

- **FR-049**: `import --at <C>` MUST load into a staging schema, prove the
  render byte-identical by R3's three-part proof, and only then swap it in.
  It takes origin only, and keeps the 15 `WORD` lines verbatim and flagged.
  Sources: import (Y:3164); spec:711; spec:722.
- **FR-050**: `cutover` MUST be its own command (D25). In group A it is its
  transaction only, against a scratch schema; group C adds the push.
  - Its label MUST come only from `register_label=`.
  - Its after-loss forms MUST follow D24.
  - Sources: D25 (Y:691); D24 (Y:653); spec:736; spec:765.
- **FR-051**: `rollback` MUST:
  - refuse while a divergence is recorded, checking again right before step
    1 and inside it (LR012);
  - declare the outage from its start, with `mirror-seq=<k>`;
  - render to `k`;
  - stop for the ruleset with `--hold-marker`;
  - push `k+1` with its read-back;
  - exit 3 on a push it cannot confirm.

  Its transaction is group A's, against a scratch schema; its render and
  push are group C's. Sources: rollback (Y:3222); spec:780; spec:811.
- **FR-052**: No operator command MUST hold a database transaction across a
  git act (F23). Each MUST run with `LANES_LANE=<operator lane>` through that
  lane's checkout (D24). Sources: F23 (Y:3160); D24 (Y:653).
- **FR-053**: The disaster path's tools MUST support D22:
  - a re-import at the mirror's tip by default;
  - a restore only where it is newer;
  - `bootstrap --roles-only`, before a restore from the daily dump or a
    re-import, making the roles and also the three database-level
    statements a dump does not carry: `REVOKE ALL ON DATABASE … FROM
    PUBLIC`, `GRANT CONNECT` to the writer and reader groups and the
    indexer's login, and the owner's global `REVOKE EXECUTE ON FUNCTIONS
    FROM PUBLIC` (the dump route's (a), lane 3's ruling, 6727022920;
    Y:2243–2244; Y:2256–2257). `bootstrap` itself still changes nothing else
    with the switch at `primary` (ops:434);
  - after a CNPG point-in-time restore, which carries the roles, their
    verifiers, memberships and floors, no roles-only step, and
    `lanes-index login rotate` for each engineer and the dump job (ops:372);
  - the after-loss cutover;
  - a new generation.

  The restore sources are CNPG's Barman Cloud physical backups, kept 30
  days, with point-in-time restore, and the daily `pg_dump` of both
  schemas, kept 30 days, both counted (Y:619–627). Sources: D22 (Y:616);
  the disaster path (ops:355); spec:846.
- **FR-054**: `bootstrap`, `ready`, `set-mode` and `login` MUST follow
  operations.md §2.4's four rules: exits, secrets, connections and bounds.
  - The admin connection is a libpq service named by `--admin-service`, and
    the admin is the database's owner that CNPG's `initdb` made, with
    `LOGIN CREATEROLE CREATEDB` and no `SUPERUSER` (Y:2283).
  - A secret is printed only to a terminal.
  - Passwords are SCRAM verifiers computed on the client.
  - Sources: ops:398; ops:407; ops:437; ops:462; ops:477.
- **FR-055**: `ready` MUST make §2.4's checks 1 to 13 (ops:444–456). Check 11
  reads `verify-full` and the prod OpenXPKI's root file, and fails on `system`
  (ops:454); check 13 expects the install's one address, `N_a` 1 (ops:456;
  ops:151).
- **FR-056**: `outage declare` and `outage clear` MUST write and remove the
  declaration beside `lanes-index.conf` (D20). The `--rollback` form needs
  `--mirror-seq <k>`, and refuses where its probe cannot read the switch.
  `outage declare` refuses with 2 a `--why` that holds a line break, so that
  the declaration's label stays on one line (lane 1's block v4, T121, on
  lane 5's O15).
  Sources: D20 (Y:599); svc:197; svc:258; ops:490.

#### The register service

- **FR-057**: `lanes-index serve` MUST be SVC-1 to SVC-12's service:
  - one per environment, connecting as its engineer's reader login;
  - a socket with its access rules (SVC-2);
  - started on demand with no supervisor (SVC-4);
  - only on primary (SVC-5);
  - one `psql` child on pipes (SVC-6);
  - every answer read fresh at `n` (SVC-7);
  - the client's share and fallback (SVC-8);
  - the image cache's write-through (Question 13);
  - its own lines in `lanes-index status` (R44).

  Its request MUST carry only file names (svc:59): the guard's second read
  never goes through it (B7). Sources: SVC-1 (Y:3701); "The service serves
  the reads and the guard, and writes do not depend on it" (svc:9).
- **FR-058**: The service MUST be built beside groups A and B, not after
  them, and the v1.2.0 tag MUST wait for it. This is Brett's own word,
  "4A", typed in lane 3's session at about 2026-11-08T20:33Z (lane 3's #165
  comment 6726766577, which quotes his typed words "4A, 5A, 1 let them
  stand, 3A"), for "Build it beside groups A and B, and the tag waits for
  it" (clar:524–525). It changes his Q29 (a) answer, "the service is built
  last" (clar:473). Act 4 waits for it too, since the guard measured
  without it from Eagle is over his 1 s. Sources: svc:293; Y:4826;
  Y:3951–3956.

#### Evidence, CI and the record

- **FR-059**: The tests MUST be test-plan.md's, lane 2's v3f:
  - the fixture `tests/pgcluster.py`;
  - real-engine, fake-`psql` and files-mode kinds;
  - agreement tests 1 to 14, the council's and the frame's;
  - lane 1's schema objects;
  - each new name of "New and unproven".

  Each is written in the phase that builds what it checks, and lands with
  that phase. Source: the test plan (Y:6084).
- **FR-060**: `tests.yml` MUST set `OPENREPOTOOLS_REQUIRE_PG: '1'` where the
  binaries exist. It MUST keep the four required check names:
  `guard-launch-mode`, `tests`, `tests-no-submodule` and `package`. Source:
  "Agreement tests run on a real engine in CI" (spec:1053).
- **FR-061**: The cost of a write (test 9) and the guard's figures (test 10)
  MUST be recorded in the pull-request bodies, never as CI thresholds.
  Sources: "The cost of a write is measured and recorded" (spec:1069);
  svc:278.
- **FR-062**: `docs/README-lanes.md` MUST gain "The SQL register", as the
  manual's requirement lists. Its server, backup and restore steps follow
  the install contract and the disaster path at Y (ops:133; ops:355).
  Sources: spec:1090; spec:1097; svc:285.
- **FR-063**: New bash MUST hold to bash 3.2, and new Python to the standard
  library on Python 3.9 or later. `dependencies = []` MUST stay. Source:
  "New code keeps to the toolchain floor" (svc:51).

### Key Entities

- **The journal entry**: seq, `write_id`, the commit's UTC, verb, lane,
  workstation, author, `args_sha256`, the message exactly, the line edits,
  per-file digests, `act_id` and its touched scopes. An operator entry
  touches only `lanes/STORE`.
- **The switch** (`register_switch`): the mode (`files` or `primary`), the
  marker's state (`absent`, `adding`, `present` or `removing`), the label,
  the import commit and tree, `set_seq`, the generation and `restore_prior`.
- **The marker**, `lanes/STORE`: `store=primary`, `database=<label>`,
  `import=<sha>`, `lanes-tree=<tree id>` and `utc=`. It holds no secret.
- **The pending record and the done mark**: under `lanes-index`'s state
  directory.
  - The record is `register-pending/<d>`, with its claim files, `call` lines
    and `tail`.
  - The mark is `register-done/<write_id>`.
- **The attempt directory**: under `<state>/register-writes/`. It holds
  `changes.tsv`, the images, `msg`, `parent.pid`, `seam.out`, and the call's
  `snapshot`, `files` and `preimages`.
- **The act base and its scopes**: `LANES_ACT` is
  `<act-id>@<seq>:<generation>` or `<act-id>@files`. `LANES_EXPECT` holds
  `row:`, `bind:`, `objects:`, `alias:`, `archive:` and `sid:` scopes.
- **The bind record**: the session's offer file, in modes `binding`,
  `bound`, `bind-failed` and `name-drift`, with `bound_s`, the pid and its
  start time, and the five copies.
- **The image cache**: `register-images/`, with content-addressed objects,
  per-seq manifests and a forward-only head `[PLAN CHOICE: the image cache's
  form]`.
- **The declaration**: a file beside `lanes-index.conf`, with an expiry or
  the rollback's mark, and `mirror-seq`.
- **A render commit**: `Register-Seq: <k>` in its final trailer block. It
  changes only the render set, which equals the images at `k`.
- **The roles**: REGISTER-WRITER and READER, groups that cannot log in, the
  owner, `lanes_register_owner`, which cannot log in, and the indexer's
  login, all made by `bootstrap` (Y:2235–2238); each engineer's writer and
  reader logins and the dump job's READER login, made by `lanes-index login`
  (Y:2285); and the database's owner, Brett's admin, whom CNPG's `initdb`
  makes (Y:2283).

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Agreement tests 1 to 14 pass, and so do the council's, the
  frame's and the schema objects' tests (spec:1053).
  - In CI's `tests` and `tests-no-submodule` jobs, and in the root's
    `exact-pins`, no real-engine test is skipped: the switch turns a missing
    engine into a failure.
  - The fake-`psql` tests run everywhere.
- **SC-002**: Q29's figure is measured on the warm p95 of the guard's whole
  wall time on primary, without the service and through it (svc:293).
  - The cold walk is recorded beside it, and not judged.
  - Within 007, it is recorded in the PR body on the fixture, both local and
    at the emulated delay of AT-F6 (21).
  - From Eagle the guard without the service already misses Brett's 1 s, at
    about 1.2 to 2.1 s, and takes about 0.5 to 0.7 s through it
    (Y:335–337; Y:6286–6287).
    So the v1.2.0 tag and act 4 wait for the service. The figure at each
    workstation as it joins is act 3's, outside 007.
- **SC-003**: The hook exits 2 inside its 5 s for each of test 10's three
  failure shapes. The budget's last line, `T0 + 4.4 s`, is below
  `GUARD_TIMEOUT`. The hook string is byte-unchanged (svc:123; Y:4942).
- **SC-004**: Answer 2 exits 2 with "BINDING" within 0.1 s after the guard's
  read returns, while a stub bind sleeps 15 s (AT-F6 (1); Y:5563).
- **SC-005**: The service answers the guard's read in under 50 ms on the
  fixture register, from the client's connect to the last byte of a
  digest-checked answer (svc:278; SVC-10, Y:4387).
- **SC-006**: No write is lost or doubled in the tests (spec:236).
  - Test 12: exactly one journal entry per `write_id`, and exit 0 after a
    clean retry.
  - Test 4: ten concurrent writers all commit, seqs 1 to 10 with no gap,
    and none exits 75 or 69.
  - A rerun runs each tail at most once.
- **SC-007**: After every write in tests 1, 5, 6 and 11, the mirror's render
  set is byte-identical to a full render, with one commit per seq
  (spec:416).
- **SC-008**: On an unconfigured or `mode=index` workstation, the output and
  exit codes of every writer and reader equal the v1.1.0 tools', apart from
  the three named exceptions. Test 1's files-mode twin shows it (spec:59).
- **SC-009**: Test 8's leak test fails on today's leak, and passes on this
  build. A writer the rollback missed exits 69 and the journal's count is
  unchanged, in each of test 8's cases (spec:824).
- **SC-010**: No environment that a session inherits carries a database URL
  or a password, on a primary workstation (svc:176).
- **SC-011**: The PR bodies record test 9's write cost and test 10's guard
  figures, beside files mode, on a named host, store and path (spec:1069).
- **SC-012**: The hygiene suite passes over every new and changed file, under
  its bash-3.2 and standard-library rules (svc:51).

## Assumptions

- **The code leg's `main` is `e39395895`,** where both legs' `007-sql-register`
  branches were cut. It is `577110e4` plus code#15, lane 1's openRepoShape
  pin v1.1.1, which changes none of `lanes-edit.sh`, `lanes-index` and
  `lane`. `577110e4` is the design's pin `2a057a6d` plus T017's five fixes,
  code#3 to code#7, which moved those three files: 654 lines added and 69
  removed. The design cites lines at `2a057a6d`, so every task finds its
  function by name.
- **v1.2.0 carries #165, T017's five fixes, W2 and code#15,** on Brett's
  "Land them now, in v1.2.0 (Recommended)", whose option read "v1.2.0 then
  carries #165, T017's five fixes and these" (lane 3's #165 6723967774):
  - W2 is lane 2's seven fixes, code#8 to code#14;
  - code#15 is lane 1's openRepoShape pin, now on code `main`;
  - on the code leg, whichever is ready first lands first, under Rule 6's
    `HOLD`.
- **Slices 1a and 1b,** code#16 and code#17, are ready for Brett's word by
  2026-11-12 (Q28's dates). Group B integrates on them.
- **The server is PostgreSQL 16,** a CNPG `Cluster` that only the
  codeXfactory prod install creates, on the prod AKS cluster in Central US,
  with one instance and no HA (Y:3327–3330). Act 3 waits for the install
  ("Wait for the install").
- **`psql` 15 or later** is on every workstation, and PostgreSQL 16's
  binaries are in CI's `ubuntu-24.04` jobs and, after the image rebuild, in
  the bench images.
- **The design is complete for coding,** and frozen at Y. Its "New and
  unproven" names count as proven once lane 2's tests hold them. An error
  found in its text since is an erratum in research.md, not an edit.
- **Brett's standing word lands 007's pull requests and the root bumps:**
  he chose "Standing words per group (Recommended)", whose option gave the
  word "land each once its CI is green and lane 3's review passes, by merge
  commit" (lane 3's #165 6723967774). Root bumps land by squash.
  - Every landing rule still holds.
  - The spec PR lands only on his own word, which names the items of its
    list.
  - The tag, the PyPI release, and acts 3 and 4 each need his own word.
  - So does lane 4's parallel CI.
- **Windows** is reached through WSL2, as for every bash command here.

## Out of scope

- **The install**: the server, its endpoint, TLS, backups, dump job and
  secrets. A codexFactory lane builds it to lane 1's install contract
  (ops:133).
- **Acts 3 and 4**, Brett's: configuring workstations, the measurements
  against the server, the restore drill, the cutover window, the flips, and
  a real rollback or disaster path. They follow 007, and wait on the install
  and on slices 2a, 2b and 3 (Q28(i)).
- **The launcher's bare window reads** (spec:626): a change in workBenches,
  carried by the v1.2.0 workBenches re-vendor, by Brett's "5A" (6726766577).
- **v1.2.1's items**:
  - the 34 issues of milestone v1.2.1;
  - lane 5's v1.2 chain, after v1.2.0's tag;
  - findings routed there after the freeze, such as lane 4's per-workstation
    D7, if lane 3 routes it there.
- **Amendment 23's act 2**: slices 1a, 1b, 2a, 2b, 3 and 4. They are lane
  openRepoShape-2's, and this feature uses them.
- **#181's two guard checks,** the 001 feature's adoption of the socket API
  (T055), and a release for a dead holder's `HOLD` (U6).
- **Code-leg CI as parallel jobs** (lane 4's point 8). It needs Brett's own
  word, and is not one of this feature's tasks.
- **Several verbs in one transaction**, a database driver, the register's
  rules as PL/pgSQL, and the service as a write path (the design's
  Non-Goals).
