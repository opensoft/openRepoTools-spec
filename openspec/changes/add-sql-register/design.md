# Design: the SQL register under `lanes-edit.sh` (Amendment 22, act 2)

Governing issue: opensoft/openRepoTools#165. Change: `add-sql-register`, in the
spec leg, opensoft/openRepoTools-spec. Phase 6 of `/opsx:propose`, written by
lane openRepoTools-3's design writer from `proposal.md` at `2c7e2f03` (the
commit that records round 1), `clarifications.md`, `council-review.md` and
`alignment-review.md`. The specs beside it, `specs/sql-register/spec.md` and
`specs/register-service/spec.md`, say what must hold; this file says how. Where
it chooses something no input decides, the choice is listed under "The design's
own decisions" with its reason and the alternative.

Conventions are the proposal's: `code/…` is the code leg at its pin `2a057a6d`,
and every line cited here was re-read there for this file; a bare `#N` is
opensoft/openRepoTools' issue or pull request `#N`; "the proposal" is
`proposal.md` beside this file.

Status: a frame with the core written. Four parts are placeholders, each marked
**PLACEHOLDER** and owned by a lane under lane 3's fan-out (#165 comment
6576888068): "The schema" and "Operations: acts 3 and 4" (lane
openRepoTools-1), "The phase plan" (lane openRepoTools-4), "The test plan" (lane
openRepoTools-2) and "The register service's internals" (lane openRepoTools-5,
comment 6576934803). Lane 3 folds each in as it arrives. Round 1 of Brett
Heap's questions is answered and held as provisional; round 2 is unanswered,
and its recommended options are this design's working assumptions.

## Context

### What exists at the code pin

- **`lanes-index`** (#160) is the one parser of `lanes-index.conf`: it reads
  `store`, `url`, `passfile`, `reader_url`, `reader_passfile`, `sqlite` and
  `schema` (`code/lanes-index:280`) and refuses any other key (`:289`). The file
  is `$LANES_INDEX_CONFIG` or `${XDG_CONFIG_HOME:-~/.config}/openRepoTools/lanes-index.conf`
  (`:166–169`); its state lives in `${XDG_STATE_HOME:-~/.local/state}/openRepoTools`
  (`:173`), which is per environment. It reaches Postgres only through `psql`,
  with no driver (`:1272–1276`), with `PGCONNECT_TIMEOUT` defaulting to 5
  (`:1306`) and a 300 s cap on a `psql` run (`:1317`). Its writes already use
  an optimistic compare-and-swap, first in the transaction, on a provenance row
  (`:1452–1467`), and one sync at a time with a rerun mark (`SyncLock`,
  `:1539–1543`). `SCHEMA_VERSION = 1` (`:101`) is an exact match with no
  migration.
- **`lanes-edit.sh`** writes through three seams that Amendment 23's slices 1a
  and 1b rewrite (the architecture seat's concern 2): the checkout a write goes
  through, `LANES_REPO` defaulting to the workspace root (`code/lanes-edit.sh:1053–1056`);
  `handle_preexisting` before each writing verb's edit (`:2701`); and
  `commit_push` at the end of each (`:2292`), where `claim`'s race is decided
  in the `pull --rebase` branch (`CP_AFTER_REBASE`, `:2429–2430`). Its readers
  enter the register at `register_text_checked` (`:3597`). After a write it
  forks `lanes-index sync`, detached (`index_nudge`, `:2554–2568`, called from
  the exit trap at `:1263`), with no mode check.
- **The exit table** (`code/lanes-edit.sh:434–545`) carries 0 to 10, 64 and 66.
  3 is "cannot confirm whether it also reached origin" (`:486`); 7 is "another
  act got there first, and this one wrote nothing" (`:505`). No command exits 69
  or 75.
- **The guard** is `lanes-edit.sh guard` (`:16856`), a `UserPromptSubmit` hook
  under `GUARD_TIMEOUT=5` (`code/openRepoTools:2344`). A hook killed at its
  timeout does not exit 2, so the timeout is the one path on which it fails
  open (`code/lanes-edit.sh:9439–9441`); today its register read is about 2 s
  wall and its scan of the session records about 0.4 s (`:9437–9438`).
- **No database path is bounded**: `statement_timeout` and `lock_timeout` appear
  nowhere in `code/lanes-index`, `code/lanes-edit.sh` or `code/openRepoTools`.
- **Not landed:** Amendment 23's act 2, six draft PRs on opensoft/openRepoTools
  (the proposal, "Dependencies"). The write path waits for its slices 1a and 1b
  (Q6(c), below).

### What constrains the approach

- Amendment 22's S1–S6 and clauses (a) and (b), as Amendment 23 amends S2: the
  renderer commits in the writing lane's checkout, `<W>-lanes/<lane>/`, never
  `W`, after S3's checks, and on a lost push keeps S2's discard-and-re-render
  rule. "A transaction has no checkout to bring up to date" (Amendment 23,
  "Relation to Amendment 22"): S3 and S4 govern the git commits that stay.
- The toolchain: bash 3.2 (no `coproc`, no `EPOCHREALTIME`), standard-library
  Python 3.9 or later (`code/pyproject.toml:45`), `dependencies = []` (`:51`),
  and `psql` as the only database client.
- Amendment 14(c), with clause (b)'s changes: credentials in 0600 files named by
  `lanes-index.conf`, never in an environment a session inherits.
- The hook's 5 s, spent once (the architecture seat's concern 1, ADV-C4).

### What the case rests on

Amendment 23's act 2 already ends the tail-append conflict and the stacked
commits in files mode (the proposal, "What Amendment 23's act 2 leaves for this
change"). So each mechanism below is argued from what only SQL adds (ADV-C9):
claim and hold order decided in one transaction; a write in milliseconds
rather than three git round trips of about 1.2 s each; deciding reads that need
no fetch; and a cheaper guard. Agreement tests 9 and 10 measure the second and
the fourth beside files mode (P5).

## Goals / Non-Goals

**Goals:**

- One storage layer and one database client, `lanes-index`, for every write,
  read, render and operator act. `lanes-edit.sh` never runs `psql` itself.
- Today's verb code runs unchanged on `mode=primary`. Its checks (the one-match
  guard, the `numstat` refusal, Amendment 7(f)'s rescan, the holders'
  pre-check) are reused, never rewritten in SQL.
- No database session is held open across shell code.
- Every database path is bounded, and every write that reaches the database
  ends in exactly one of five outcomes: committed (0), refused by the
  register's own rules (2), unreachable or refused by the database (69), busy
  (75) or in doubt (3). A verb's own refusals before that keep their codes.
- The renderer never touches a lane checkout's HEAD, index or working tree.
- Each phase of the seam split lands as a working, tested state, with
  `lanes-index` refusing `mode=primary` until the renderer's git half is
  installed.
- Where `lanes/STORE` is absent and no configuration says `mode=primary`,
  nothing changes but the configuration read and the marker check.

**Non-Goals:**

- Several verbs in one transaction (the design's own decision D5).
- A database driver, or the register's rules as PL/pgSQL (Q7's (B)).
- The register service as a write path or as the renderer's home.
- Building Amendment 23's act 2, or changing files mode beyond the marker check.
- Moving handoffs into SQL, or replacing `git log` as the audit trail.
- A cache that answers a deciding read on its own.
- `tasks.md` and the Speckit handoff, which follow this file.

## Decisions

### Round 1: answered, and held as provisional

Brett Heap, 2026-11-02T12:04:49Z, '1A 2A 3A 4A' — an accepted prompt
suggestion, held as provisional until he confirms it by typing (#165 comment
6576888068). Each answer is the recommended option, so the design would assume
it anyway, and nothing irreversible rests on it.

- **Q6, 1A: (c), split by seam, with R3's (a′) order for slices 1a and 1b.** Now,
  on `2a057a6d`: everything that does not run through `lanes-edit.sh`'s write
  seams. After 1a (#282) and 1b (#307) land in the code leg: the write path
  (S1). Then: the renderer's git half (S2), rollback's push and agreement tests
  3, 5 and 6. The phase plan (lane 4) gives each phase's call sites.
- **Q8, 2A: R3's option A, with ADV-C5's `:1154` and P15.** S3's reader table
  carries it.
- **Q20, 3A: (B).** `mode=primary` is written at the cutover; act 3 leaves
  `mode=index`. S5 and the flip.
- **Q2(a) and (b), 4A.** Each `lanes-edit.sh` write opens its own transaction with
  its own REGISTER-WRITER credential; the service holds one connection for the
  guard's reads and the other reads; with the service down, writes go on and
  never fall back to the files. S1, and "The guard and the service".

### Round 2: the working assumptions

Each entry names its question, the option this design is written under, the
section it shapes, and what changes under each other option. The eight that
stand apart from "all as recommended" are marked so.

1. **Q4(a), the freeze (stands apart): (i).** An estate-wide hold, announced
   on each coordination issue and by Brett's prompt to each pane, drained to
   `PAUSED` and quiet for 2 minutes, after a cutover and a rollback rehearsed on
   a scratch schema with an outage drilled (ADV-C10). Lane 1's operations
   section. Under (ii′), S5's order becomes: rehearsal, a readiness pass in
   every environment, the marker's push as the freeze, the import at its parent,
   the switch, the flip; writers that know the marker exit 69 meanwhile, and the
   cutover's tree-id check finds a stray commit from one that does not, for S2's
   two tools to resolve before a re-run. Under (ii), the marker is pushed first
   and the import is taken at it, with no readiness gate, which reorders
   Amendment 22's S5 and needs his word. Under (iii), no freeze: the cutover
   aborts and re-imports whenever `main` moves under `lanes/`, which at an 18 s
   median gap is most windows.
2. **Q4(b), a rollback with the database down (stands apart): (iii).** A
   rollback needs the database (S5). Under (iv), the allowance stays and every
   primary write checks `lanes/STORE` on origin, by `ls-remote` inside the
   attempt, refusing 69 where it cannot; that adds a round trip to GitHub to
   every attempt, and `LANES_NO_FETCH=1` cannot skip it. Under (ii), each
   writing workstation keeps a spool, written before COMMIT, in a place the
   operator can list, and the rollback proves "current" from them; under (i),
   the ratified text alone. The probes found that (i) and (ii) leak, so either
   leaves S5's requirement unmet, and Brett takes that risk.
3. **Q4(c), stranded records: (i), origin only.** Under (ii), the import
   harvests local-only lines, which their lanes never confirmed, and its proof
   compares against a tree that is not origin's.
4. **Q4(d), the 15 unreadable `WORD` lines: (i), verbatim and flagged.** Under
   (ii), the byte proof fails at those lines unless both sides are normalised.
   (iii) changes nothing here: the lane appends a correcting line first.
5. **Q7, how a write runs (stands apart): (A″).** S1. Under (A′), an attempt is
   one session holding the head-row lock across the verb; that needs a helper
   holding `psql` open, since bash 3.2 has no `coproc`, and a stuck verb blocks
   every writer, so "busy" becomes a lock not granted in the writer's limit.
   Under (A), S1 as written with a cap of six attempts, which fails test 4's
   burst (4 of 10 writers in the probes) unless the cap is raised to at least
   the largest burst. Under (B), every verb's rules become PL/pgSQL, a second
   copy. Under (C), (A′)'s held session without the lock. Under "(A″) with 69",
   the busy outcome exits 69, and the five `lane-start` arms and the manual
   read 69 as "busy or unreachable".
6. **Q11, the guard's credential with the service down: (a).** The guard's
   direct read holds the READER credential from the 0600 configuration ("Roles
   and credentials"). Under (b), the fallback refuses 2 whenever the service is
   down, so the service becomes a precondition of every prompt and Q29's (a) no
   longer holds.
7. **Q12, the claim loser: (a), exit 2.** S6. Under (b), the loser exits 7, and
   so does the later `HOLD` or `LANDING` writer (D18), so that the two stay
   paired; the manual's "2 instead of 7" line goes.
8. **Q13, `rename-lane`'s order: (a), the transaction first.** S6's note on
   `rename-lane`. Under (b), the handoff move and stamp commit first, and a
   refused transaction leaves the handoff moved while the register names the
   old lane; the repair then reruns only the transaction.
9. **Q14: (a) for `wip init` and (a) for the link.** `wip init` refuses once
   `lanes/STORE`, or any file under `lanes/`, exists; the
   `~/projects/xFactory/LANES.md` link stays, for reading. Under (b) for
   `wip init`, its seed commit is a stray commit that stops the renderer; under
   (b) for the link, `link-estates` stops placing it on a primary estate.
10. **Q17, which lines take and release `main`: (B).** S6. Under (A), only
    `HOLD RELEASED` and `LANDED` release, so a withdrawn `HOLD` keeps `main`
    taken. Under (C), `append-line` also refuses every HOLD- or QUEUED-shaped
    line outside the recognised kinds.
11. **Q18, the render set: (A).** `lanes/LANES.md`, `lanes/log/<lane>.md` (not
    `lanes/log/README.md`), `lanes/aliases.tsv` and `lanes/archive/`;
    `lanes/STORE` is `Register-Seq` 1; `lanes/repos.tsv` stays a git file. Under
    (B), all of `lanes/` is rendered, so every hand edit of `repos.tsv`,
    `log/README.md` or `.gitignore` stops the renderer. Under (C), those files
    move out of `lanes/` before the cutover. Under (D), they are imported as
    opaque files written by a verb, and D19's alias rule reads `repos.tsv` from
    SQL inside the transaction.
12. **Q19, the claim comment: (A), "register seq `<n>`".** On primary, `claim`
    prints `register seq <n>` where it printed the log commit's sha. Under (B),
    `claim` waits for its render and prints the sha, so a write waits on the
    renderer, against S2's write-behind.
13. **Q21(a), readers on an unconfigured workstation under the marker: (A).**
    Acts and the guard refuse; the listings and `SessionStart` read the mirror
    and say so. Under (B), they all read the mirror, and a workstation not yet
    flipped can act on a stale mirror.
14. **Q21(b), the push ruleset (stands apart): (B).** A ruleset whose only bypass
    is a renderer identity, before the cutover where the organisation's plan
    supports it (D23). Since it refuses every files-mode register write, it is
    switched on inside the freeze, after the drain and the last `PAUSED` write
    and before the import, and the rollback removes it after its render proves
    the mirror current and before it pushes the commit removing `lanes/STORE`;
    switching it is Brett's act (S5). Under (B′), act 4 also waits for an
    equal server-side gate where the plan has no ruleset. Under (A), there is
    no identity and no key, and the renderer's stop with S2's two ways out is
    the whole answer to a stale writer, found by `status`'s `diverged at` and a
    second readiness run (P18).
15. **Q22, the database and Raven (stands apart): (a).** Raven is configured
    with Eagle at act 3; the database's host, its backups and its downtime limit
    are Brett's to name. Under (b), Raven is not a writing workstation, and its
    installed tools refuse under the marker. Under (c), the database runs on
    Eagle and Raven is upgraded but not configured, so the readiness check
    covers it and the marker refuses its writes; the recovery is S5's disaster
    path, since under Q4(b)(iii) there is no rollback from a mirror with the
    database unreachable.
16. **Q23, the configuration on benches: (a), a read-only mount.** The flip and
    the outage declaration (D20) run from the host. Under (b),
    `LANES_INDEX_CONFIG` names a file under an existing mount, and the
    declaration sits beside it. Under (c), provisioned at launch: a container
    started before a flip or a declaration does not see it until it is
    relaunched.
17. **Q24, how #165 reaches the hosts: (a).** The workBenches re-vendor, v1.2.0
    with a TestPyPI rc1, the one-liner as a stopgap, and one image rebuild.
    Under (b) or (c), only the route changes; the readiness check's digests
    follow whichever artifact is installed.
18. **Q25(a), the merge method: (i), merge commits.** Under (ii) or (iii), the
    sha that lands differs from the rehearsed head, so the composed run is
    repeated.
19. **Q25(b), the root bumps: (a), lane openRepoTools-1.** Under (b), lane 3.
20. **Q25(c), the feature number: (a), 007.** Under (b), 034, and the handoff's
    paths change.
21. **Q26, the guard while the database is down (stands apart): (a′).** S3's
    declared outage (D20). Under (b), the guard refuses 2 throughout every
    outage, and the declaration and its command are not built. Under (a), the
    guard answers from the mirror at any outage, which is safe only with a check
    each guard can make, such as every writing environment's spool readable
    from each guard; nothing in this design provides one.
22. **Q27, what "takes S3's checks" means: (a), the fetch.** S2. Under (b), the
    renderer takes the lane checkout's lock and runs S3's rows and fast-forward
    there, so a lane's own stranded commit or staged entry stops that
    checkout's renders, and `lane-start`'s stamp and `lane-handoff`'s commit
    can queue behind a render.
23. **Q28, act 2's slices (stands apart): (i), and (a) for the owner.** Act 4
    waits for 2a (#312), 2b (#316) and 3 (#311); lane openRepoShape-2 re-homes
    them. Under (ii), act 4 waits only for 1a and 1b, #311 trails, and the
    skill's commit block keeps its `pull --rebase` (`code/skills/handoff/SKILL.md:449`,
    the proposal's "Impact"), which this change would then have to edit itself.
    Under (b) or (c) for the owner, the schedule moves and the design does not.
24. **Q29, the cutover and the service (stands apart): (a).** The service is 007's
    last slice, and act 4 waits for it only if the guard measured without it is
    over a budget Brett names. Under (b), act 4 waits for the service, and the
    direct read stays the fallback (Q11(a)).

### The design's own decisions

No input decides these, or the proposal leaves them to the design. Each names
the section that carries it.

- **D1. The schema is R3's: a schema `lanes_register` of its own beside #160's
  `lanes_index`** (Q3), with its own version, an exact match or 69, and
  forward-only migrations. #160's tables drop 1,872 of `LANES.md`'s 6,410 lines,
  so they cannot be the truth, and moving its exact-match version to 2 needs a
  migration it does not have. The DDL is lane 1's ("The schema"). This departs
  from Amendment 22's "Everything Amendment 14 built is reused", whose table
  makes #160's schema "the register's own tables"; #160's schema stays for the
  ledger half, and the register gets one of its own. It is listed for Brett
  below.
- **D2. One database client: `lanes-index register …` subcommands.** The write
  path calls `lanes-index register snapshot` and `lanes-index register commit`;
  readers call `lanes-index register read`; the renderer, import, rollback and
  status are `lanes-index` too. The alternative, `psql` driven from bash, would
  put the connection string in bash, parse SQLSTATEs in bash, and keep a second
  copy of the timeouts; the service as the client is excluded for writes by
  4A. Cost: a Python start per call, which test 9 measures (S1).
- **D3. The mode is asked, not parsed.** `lanes-edit.sh` asks `lanes-index mode`
  only where a configuration file exists at `lanes-index`'s path, or where
  `LANES_INDEX_CONFIG` is set; otherwise the workstation is not primary and no
  process is started. Testing that a file exists is not parsing it, so
  `lanes-index` stays the one parser, and an unconfigured workstation pays
  nothing. Q6(c)'s refusal has an exit code of its own from `lanes-index mode`,
  never 2, which lane 4 picks within D10's map; any other failure of the query
  (a configuration it refuses, a crash, 127, a timeout) runs files mode with a
  warning, and files mode's mandatory marker check after Amendment 23's S3
  fetch refuses on a SQL estate, so no failure of the query writes files there.
  A configuration typo on a `mode=index` host, as Eagle and Raven are between
  acts 3 and 4 (Q20(B)), then stops nothing but what it stops today (S1).
- **D4. On primary, the commit seam collects and never commits.** At
  `commit_push` (`commit_publish` after 1b) the primary branch keeps every
  content check the seam makes, then hands the staged change set and the
  message to the attempt loop, with no commit, pull or push (S1).
- **D5. One transaction per verb, not per act.** S1's "ONE transaction" per
  write and S2's one mirror commit per write, with that write's own message,
  both read per verb; a transaction carrying an act's several verbs would merge
  their commits and needs Brett's reading of S1 (R2C). The partial states an act
  leaves are the ones Q8's answers already handle, such as a lane left
  `SWAPPING` until the late `PAUSED` path. The journal keeps one entry per
  verb, so a later change could group them without a new schema. This differs
  from R2, where lane 4 recommends one transaction per act. **It also leaves an
  act's earlier deciding reads outside its writes' transactions**:
  `lane-start`'s reads at `code/lane-start:1555`, `:1609`, `:2335` and
  `:1154`, and those of `lane-end`, `lane-handoff` and `lane-rename`, are read
  calls of their own, and the writes they decide are later verbs, each with
  its own snapshot, so the head check covers a verb's own checks and not the
  act's earlier reads; a rival commit in between (a second binding) would go
  unseen. That is S3's "inside the act's transaction". **The design keeps S3
  by expectations:** each write verb that an act's earlier reads decide carries
  them as an expectation, for example `--expect-row <lane> <digest>`, which the
  commit checks in its own transaction (S1's step 3.5), refusing with 2 and
  writing nothing when it no longer holds. Lane 4's phase plan lists the acts
  and their expectations. Where an act cannot carry one, that act's narrowing
  of S3 goes to Brett beside this difference from R2 (below).
- **D6. The head row is updated before the journal row is inserted.** The probes
  found the reverse order turns every lost race into a 23505 on the seq key.
  Retrying that 23505 as a 40001 would also work, but would read every
  uniqueness error on the journal as a race (S1).
- **D7. "Register busy" is exit 75 (`EX_TEMPFAIL`), at a 30 s deadline.** 75 is
  free (`clarifications.md`, ADV-C7), and is the product seat's suggestion; 30 s
  is lane 2's example, above the probes' worst drain of ten writers (10.2 s at a
  1 s window). An attempt that loses re-reads at once, as the probes measured.
  A write any of whose commit calls was in doubt never exits 75 (D8). This
  narrows Amendment 22's S1, and Brett may keep 69 ("(A″) with 69") (S1).
- **D8. A write in doubt that cannot settle exits 3, naming its write id, and
  a rerun reuses the id.** 3 already means "cannot confirm whether it landed"
  (P16's precedent), but its row in the exit table (`code/lanes-edit.sh:486`)
  says "the edit is never a permanent loss", which on primary is wrong: an
  in-doubt transaction may never have committed. The row gains a primary
  clause: "may not be written; run the printed `LANES_WRITE_ID` rerun, which
  writes it once or finds it", and the phase plan lists the callers that read
  3 as "written". The id reaches a rerun two ways: the report prints the rerun
  with `LANES_WRITE_ID=<id>`, and a pending record in the state directory lets
  a plain rerun of the same command line reuse it. The record carries the
  database generation (D22) and a maximum age; a record whose pid is still
  live is waited for, not reused; and the snapshot refuses with 2 when the
  journal entry an id names has a different lane, verb or arguments from this
  run's. Lane 4 owns the record's format. A write any of whose commit calls was
  in doubt never exits 75: at the deadline it makes one lookup once the head
  row is free, within the 10 s reconnect window, and exits 0 if the write is
  there, 75 if it is absent with the head row free, and otherwise 3 with the
  id, the record kept (S1).
- **D9. The timeouts** in "Roles and credentials", with
  `idle_in_transaction_session_timeout` set on the REGISTER-WRITER role so that
  an orphaned transaction cannot hold the head row for long. Only the guard's
  figures are measured; the rest are the design's, for lane 2's tests to
  confirm.
- **D10. Exit codes on primary (Q9).** A refused direct write (the hand-edit
  `commit`, the captures, `add-row`'s compensating commit, the branch-wide
  push) exits 2, since it is the lane's act to redo through a verb, while 69
  tells a lane to stop and tell Brett. `lanes-index`'s register subcommands use
  69 for an unreachable or refusing database; local sidecar reads keep 1 and 10;
  `managed-projection` keeps 1 (S4); the guard maps 69, 75 and 3 to 2; `claim`
  no longer spends 7; exit 9's re-check stays local (S1's outcome table).
- **D11. `register-row-local` reads the database on primary,** exactly as
  `register-row` does, 8 included, and exits 69 (never 8) where the read fails.
  It exists because "this checkout's row can be ahead of origin after a local
  transcript stamp" (`code/lanes-edit.sh:17935`); a primary write is in SQL
  before it returns, so no local row can be ahead of the truth. Its three
  callers (`code/lane-start:2367`, `code/lane:818`, `:881`) already fail closed
  on anything but 0 and 8 (S3).
- **D12. `LANES_NO_FETCH=1` on primary.** A primary write makes no git fetch, so
  the variable has nothing to skip there, and it cannot weaken the switch check,
  which is inside the transaction. Deciding reads read the database, so it
  changes nothing for them. Mirror reads keep today's meaning. The renderer is
  forked with the variable removed from its environment and never consults it
  (ADV-C8) (S1, S2).
- **D13. The line's UTC is the database's clock at the attempt's snapshot.** The
  snapshot returns the database's time in whole seconds, and on primary
  `utc_now` (`code/lanes-edit.sh:2861`) answers it for the attempt, so the line
  a verb composes and the durations it computes are on one clock. The journal
  entry also records the commit's `now()`, which dates the render commit. The
  alternative, a placeholder replaced inside SQL at commit, would rewrite verb
  output in SQL, a second copy of the line grammar (S6).
- **D14. The renderer is the writer's fork, in `lanes-index`, in the
  private-index form** (Q5): R3's recommendation, and the service is last under
  Q29(a). The in-place form, with its fence, is the fallback if lane 4's
  prototype fails the private-index form (S2).
- **D15. A render commit's author and committer are the identity the journal
  recorded** (the writing environment's `user.name` and `user.email` at write
  time), both dated by the journal entry's commit UTC, so that two renderers
  build the same commit and the history keeps today's attribution. For that to
  hold, `commit-tree` runs with `--no-gpg-sign` (it honours `commit.gpgSign`),
  the encoding is pinned to UTF-8, and `Register-Seq` joins the message's
  existing trailer block, as `git interpret-trailers` places it, rather than
  starting a second one (S2).
- **D16. A divergence is recorded in the database,** as the register's mirror
  state, by the first renderer that finds it, and cleared by the single-write
  import or the renderer's revert. Each renderer still re-detects it from git,
  so the row reports and never gates alone (S2).
- **D17. Every reader reads "the register's text at the journal's head" and runs
  today's parser on it**, through the service where one answers, otherwise
  directly with the READER credential. This extends Q11(a) from the guard to
  every reader, as P4's "no second outage mode" asks, and it keeps the readers'
  logic as unchanged as the writers' (S3).
- **D18. The later `HOLD` or `LANDING` writer exits 2, writes nothing, and prints
  the earlier line,** paired with Q12: `main` is the object, and it is held. The
  line is classified inside the commit transaction by `lanes-index`'s one
  classifier, so `append-line` stays the one verb and no caller changes. This
  is a reading of Amendment 22's S6, not its text: S6 says the claim loser
  "writes nothing", but of `HOLD` and `LANDING` it says only that the later
  writer's transaction "reports the earlier line to it before it acts", and a
  line Amendment 10 would have recorded is now not written. It goes to Brett
  beside Q17 (below) (S6).
- **D19. A claim's spellings are checked in the transaction** (ADV-C10). Each live
  claim records its spelling, its canonical key and the aliases its writer's
  `repos.tsv` mapped to that key, and the commit refuses a claim whose set of
  spellings meets another lane's live claim's (S6).
- **D20. A declared outage is a file beside `lanes-index.conf`,** written by
  `lanes-index outage declare` on Brett's word, with an expiry he names and the
  mirror's `Register-Seq` at the moment he declares it, `mirror-seq=<m>`; the
  guard answers from the mirror only where the tip it reads carries a
  `Register-Seq` of at least `m` (Q26's (a′); S3).
- **D21. `lanes/STORE`'s fields:** `store=primary`, `database=<label>`,
  `import=<sha>`, `lanes-tree=<tree id>` and `utc=<UTC>`, in a commit carrying
  `Register-Seq: 1` (S5).
- **D22. The disaster path re-imports at the mirror's tip by default,** continuing
  the sequence, and restores a backup only where it is newer than the mirror.
  A generation id, in the switch row and in every pending record, changes with
  each new database, and a record of another generation is refused, since the
  ids up to the mirror's seq are lost with the old journal. The bootstrap runs
  before the restore, because roles are cluster objects a database backup does
  not hold; every host is re-pointed at the new database. Lane 1 details the
  steps (S5).
- **D23. The renderer identity, under Q21(b)(B), is a deploy key** in a 0600 file
  named by `lanes-index.conf`, used only in the push's own environment; the
  ruleset names the render set and `lanes/STORE`. Every push of those paths uses
  it: the renderer's, and the cutover's and the rollback's commits of
  `lanes/STORE`, which their commands push themselves (D24). The ruleset goes on
  inside the freeze and comes off in the rollback (Q21(b) above; S5) (S2).
- **D24. Two operator entries, and the operator lane** (the frame review's F1,
  F2, F12). An entry whose edits touch only `lanes/STORE` is an operator entry,
  flagged in the journal (lane 1 adds the column). Exactly two kinds pass the
  switch check, each in its command's own transaction: the cutover's seq 1, in
  the transaction that sets the switch to `primary`; and the rollback's last
  seq, in the transaction that sets it to `files`. Nothing else is journalled
  while the switch is not `primary`. The renderer never renders an operator
  entry; its command pushes it with the renderer identity and confirms it by
  Amendment 23's S4 read-back. The operator commands (import, cutover,
  rollback, the disaster path) run with `LANES_LANE=<operator lane>` through
  that lane's checkout, as Amendment 23's S2 item 3 asks of every write, and
  none holds a database transaction across a git act (S2, S5).
- **D25. `lanes-index cutover` is a command of its own** (R3's split of Q4), not a
  step of `import`. The name is the design's, not one Amendment 22 ratified: its
  S5 has the import refuse the cutover and the cutover follow, with no command
  named. It is listed for Brett below (S5).

### Differences from Amendments 22 and 23, for Brett

Where this design departs from, narrows, or reads the ratified texts, the
departure is listed here. "Routed" names the round-2 question that already puts
it to Brett Heap; "not yet routed" means no question does, and lane 3 puts it
to him.

| Item | Against | Status |
|---|---|---|
| D5: one transaction per verb, where lane 4's R2 recommends one per act; and its S3 consequence, that an act's earlier deciding reads sit outside its writes' transactions, kept by expectations, with any act that cannot carry one narrowing S3 | Amendment 22's S1 and S3; R2, R2C | not yet routed |
| D7: "register busy" exits 75, not 69 | Amendment 22's S1 | routed: Q7, "(A″) with 69" |
| Q4(b)(iii) and D22: a rollback needs the database; a database lost for good is the disaster path | Amendment 22's S5 | routed: Q4(b) |
| Q26(a′) and D20: the guard answers from the mirror in a declared outage | Amendment 22's S3 | routed: Q26 |
| Q27(a): "takes S3's checks" read as S3's fetch for the private-index renderer | Amendment 23, "Relation to Amendment 22" | routed: Q27 |
| D18: the later `HOLD` or `LANDING` writer writes nothing | a reading of Amendment 22's S6 | not yet routed; goes beside Q17 |
| D1: the register in a schema of its own, not #160's | Amendment 22, "Everything Amendment 14 built is reused" | not yet routed |
| D25: `lanes-index cutover` as a command of its own | Amendment 22's S5 and its ratified names | not yet routed |
| A release for a stale `LANDING` (S6) | Amendment 10 and Rule 6, under Q17(B) | not yet routed: an open point for Brett |

## Architecture overview

### The components and their seams

| Component | Role on `mode=primary` | Credential |
|---|---|---|
| `lanes-index` | The one configuration parser and the one database client: `mode`, `register snapshot`, `register commit`, `register read`, `journal`, `render`, `import`, `cutover`, `rollback`, `status`, `reconcile`, `outage`, and the operator tools (bootstrap, readiness check, flip) | per subcommand: REGISTER-WRITER, READER, or the bootstrap's one-time admin connection |
| `lanes-edit.sh` | Every verb, unchanged in its logic. Its three write seams: the resolver, which the write path no longer needs and the renderer does; `handle_preexisting`, where the captures refuse; and the commit seam, which hands a change set to the attempt loop (D4). Its readers enter at `register_text_checked` | none of its own: it calls `lanes-index` |
| The renderer | `lanes-index render`, forked by a writer after its COMMIT, in that lane's checkout; builds one commit per sequence on `origin/main` with a private index and pushes it | REGISTER-WRITER; the renderer identity's key under Q21(b)(B) |
| `lanes-register` | The local-socket service: one READER connection per process, the guard's and the other reads (4A). Last slice (Q29(a)); its internals are lane 5's | READER |
| The guard | `lanes-edit.sh guard`, the hook: the service's client, falling back to a direct read (Q11(a)) | READER, through `lanes-index` |
| The readers | Read verbs and the lifecycle scripts (`lane-start`, `lane-handoff`, `lane-end`, `lane`, `lane-rename`, the skills, `lane-worktrees`) | READER, through the service or `lanes-index` |
| PostgreSQL | Schema `lanes_register` (D1) beside `lanes_index` | — |
| git | `origin/main` of the workspace repository: the mirror and the handoffs. Lane checkouts `<W>-lanes/<lane>/`: render targets and handoff writers. `W`: read and fast-forwarded only | — |

### The flow of one write

```
lanes-edit.sh <verb> …
  1  workstation guard (:15103); mode: no config → files, as today
                                 config → `lanes-index mode` → index | primary
  2  write id: LANES_WRITE_ID, else a pending record, else a new one
  3  attempt loop, until the deadline (30 s)
       a  lanes-index register snapshot ──psql── head n, switch, db clock,
                                                 write id seen?, images at n
       b  the verb, unchanged, in a subshell on a scratch tree at n;
          its commit seam collects the change set and the message
       c  lanes-index register commit ──psql── SERIALIZABLE: switch, head n→n+1,
                                               journal row, line store, layer,
                                               constraints; COMMIT
          lost its turn → back to a;  in doubt → back to a (the lookup decides)
  4  committed: fork `lanes-index render` (detached, lane checkout)
  5  exit 0 | 2 | 3 | 69 | 75
```

The verb's own refusals happen at step 3b, on the snapshot, before any commit
is sent, and exit with the verb's code. The head check at step 3c makes the
verb's checks hold at COMMIT: if any write committed since the snapshot, the
commit does nothing and the attempt starts again. R3 holds this equal to S3's
"inside the act's transaction" for the verb's own reads. An act's earlier
reads, made by other calls before the verb, reach the commit as the verb's
expectations, which step 3c checks (D5).

### The flow of one read

A reader on primary asks for the register's text at the journal's head (D17):
through the service's socket if it answers within its share, otherwise
`lanes-index register read` with the READER credential. The answer is the text
of the files it asks for, proven at head `n` by per-file digests in the same
request, and today's parser runs on it. With the database unreachable, the
reader exits as S3's table says.

## The schema — **PLACEHOLDER: lane openRepoTools-1's section**

> **PLACEHOLDER.** Lane openRepoTools-1 writes this section under lane 3's
> fan-out (#165 comment 6576888068). Its scope, copied from that post: "The
> DDL, from R3a: the switch, the head row, the journal with a UNIQUE
> `write_id`, `doc_lines`, the structured layer, the roles and GRANTs, and the
> version check with forward-only migrations."
>
> **What the frame needs from it** (the names are the frame's working names,
> for lane 1 to keep or replace):
>
> - the switch row, with the mode, the database's label, the import commit and
>   its seq, who set it, and the seq at which it last changed (S5);
> - the head row, holding the gapless `Register-Seq`, never a SEQUENCE (S1);
> - a journal entry per write: seq, `write_id` UNIQUE, the base seq, the
>   commit's database UTC, verb, lane, workstation, mode, the author identity
>   (D15), today's commit message, its line edits across files (creates,
>   renames, removals) and the per-file digests after it (S1, S2);
> - `doc_lines`, every line of every rendered file, with one order key over
>   rows and free lines (R2's design question 6);
> - the structured layer: one row per lane with the mode that wrote it last,
>   the live claims with their spelling sets (D19), the lines that take and
>   release `main` (Q17(B), D18) and R3's two views;
> - the mirror state: the divergence record and the strays it resolved (D16);
> - the three roles, their GRANTs, and the REGISTER-WRITER role's
>   `idle_in_transaction_session_timeout` (D9).

## S1: the write path

### Entry and mode

Every writer passes the workstation guard (`code/lanes-edit.sh:15103`) as
today. Then the mode (D3): with no configuration file at `lanes-index`'s path
and `LANES_INDEX_CONFIG` unset, the workstation is not primary and the verb runs
in files mode, as today, with the marker check after Amendment 23's S3 fetch
(phase 2). Otherwise `lanes-edit.sh` runs `lanes-index mode`, which prints
`index` or `primary` and exits 0. While Q6(c)'s refusal is built in,
`lanes-index mode` exits a code of its own, never 2, with its text, for a
`mode=primary` configuration, and `lanes-edit.sh` exits 69 repeating it and
writes nothing (agreement test 14); the other `lanes-index` commands refuse
it with 2, as the configuration parser does. Any other failure of the query (a
configuration it refuses, a crash, 127, a timeout) runs files mode with a
warning on stderr naming the failure; files mode's mandatory marker check after
Amendment 23's S3 fetch then refuses with 69 on a SQL estate, so no failure of
the query writes files there (D3).

On primary, before the snapshot, the writer looks in the writing lane's
checkout, `<W>-lanes/<lane>/`, for a pending hand edit of a rendered file
(Amendment 23's S3 step 4). The verb itself runs on a scratch tree, so this is
the only place it can be seen. Where one is pending, the writer refuses with 2,
naming the file and the recovery that works on primary: restore the file, and
make the write through its verb.

### The write id

Each write carries a `write_id`, UNIQUE in the journal, that every attempt of
that write reuses. It is `LANES_WRITE_ID` where the caller set it; else the id
in a pending record for the same command line (below); else a new random
128-bit id that `lanes-index` mints. Before the first commit call,
`lanes-edit.sh` writes a pending record,
`<state>/register-pending/<digest>`, where `<state>` is `lanes-index`'s state
directory and `<digest>` is a hash of the lane, the verb and its arguments; it
holds the id, the UTC, the command line, the writer's pid and the database
generation (D22). The record is removed once the outcome is known. A later run
of the same command line, with no `LANES_WRITE_ID`, reuses the id, says so on
stderr, and consumes the record, so only the first rerun after an in-doubt
write is treated as its retry. So a write killed by an outer timer, which
cannot print anything, is still retried under its own id (ADV-C3; lane 2's
SHOULD e). Three limits keep a record from being reused for another write
(D8): a record past its maximum age, or of another generation, is not reused;
a record whose pid is still live is waited for, since two identical runs at
once must not share an id; and the snapshot refuses with 2 when the journal
entry an id names has a different lane, verb or arguments from this run's,
which also stops a `LANES_WRITE_ID` given to a different command. Lane 4 owns
the record's format. `lanes-index status` lists the pending records and
resolves each by looking its id up.

### One attempt, under (A″)

An attempt is two short `psql` calls with the verb between them; no session
stays open across the shell code.

1. **The snapshot.** `lanes-index register snapshot`, one `psql` call at
   REPEATABLE READ READ ONLY, returns: the switch; the head seq `n`; the
   database's clock in whole seconds (D13); whether this `write_id` is in the
   journal, and at which seq; and the images of the files the verb reads, at
   `n`. The images come from a per-environment cache of the rendered files,
   brought to `n` by the journal entries since the cache's seq and checked
   against the digests at `n`; on any mismatch the call fetches the files
   whole. The cache is advisory: correctness rests on the digests and on the
   head check. This is the renderer's pure function (journal to file images),
   phase 1's code. A switch that is not `primary` exits 69 with Amendment 22's
   text. A write id already in the journal exits 0 where the entry's lane,
   verb and arguments are this run's, and refuses with 2 where they are not
   (D8).
2. **The verb.** The verb's arm runs in a subshell on a scratch work tree whose
   one commit holds the images at `n`, as its `LANES_REPO`, so that its git
   acts (`git add`, `git mv`, the case-only rename made from the index,
   `code/lanes-edit.sh:2371`) run as they do today and its mutex is the scratch
   tree's. After slices 1a and 1b, an explicit `LANES_REPO` keeps S3–S5
   applying in it (Amendment 23's S2 item 1), and a scratch tree has no origin
   to fetch, so the primary branch runs the verb under an internal flag that
   bypasses the resolver and S3 for the scratch tree; lane 4 confirms the flag
   and names it. `repos.tsv`, outside the render set, is read from the lane
   checkout's `origin/main` (D19). At the commit seam the primary branch reads
   the staged change set (paths, renames, removals and the new images) and the
   message, writes them to the attempt's directory, and returns. A verb that
   refuses exits with its own code, and nothing has been sent.

   **Output and side effects, once.** The verb runs once per attempt, 55 times
   in a burst of ten, so each attempt's stdout and stderr are buffered, and
   only the deciding attempt's are replayed: the committed one's, or the one
   whose refusal ends the write. A refusal at the commit (D18, D19) therefore
   never follows a success text. Any side effect outside the register, such as
   tmux in `request-handoff`, runs once, after COMMIT. Lane 4 lists the arms
   that have any.
3. **The commit.** `lanes-index register commit`, one `psql` call, runs at
   SERIALIZABLE, in this order:
   1. read the switch, and refuse unless it is `primary`;
   2. `UPDATE` the head row from `n` to `n+1` `WHERE` it is still `n`; no row
      updated means the head moved, and the attempt has lost its turn (D6);
   3. insert the journal row at `n+1` with the `write_id`. A 23505 on the
      `write_id` would mean an earlier attempt of this write committed, and the
      write would exit 0; under step 2's order that cannot happen, since an
      earlier attempt that committed has moved the head, so this branch is a
      backstop, and no test expects it to fire;
   4. apply the line edits to `doc_lines`, checking the base digests;
   5. refill the structured layer for the rows and lines touched, and check its
      constraints: one live claim per object, spelled any way (D19), the hold
      order (D18), and the expectations the verb carries for the act's earlier
      reads (D5), each refusal a 2;
   6. `COMMIT`.

   It runs with `VERBOSITY=sqlstate`, so a 40001 is told from the other errors
   by its SQLSTATE and not by `psql`'s exit 3.

### The outcomes

| What happened | `lanes-index register commit` | `lanes-edit.sh` | Written |
|---|---|---|---|
| committed at `n+1` | 0, `committed <seq>` | forks the renderer; exits the verb's code; `claim` prints `register seq <n+1>` (Q19(A)) | the write |
| the head moved, 40001, 40P01, or 55P03 (`lock_timeout`) | 75 | a new attempt at once; at the deadline, exit 75, unless a commit of this write was in doubt (below) | nothing |
| `psql` exit 3 for any other SQLSTATE: the transaction was rolled back | 69, with the SQLSTATE | 69, naming the store and the error | nothing |
| a constraint of the register's rules (an object held, `main` held) | 2, with the holder or the earlier line | 2 | nothing |
| switch not `primary` | 69, Amendment 22's text | 69 | nothing |
| `psql` exit 2, or the call's client timer expired | 3 | in doubt: the next attempt's snapshot looks the id up | unknown |
| no connection at the first snapshot, before any commit was sent | — | 69 | nothing |

A `psql` exit 3 is never in doubt: under `ON_ERROR_STOP` the script stops
before its `COMMIT`, and a serialization failure raised at `COMMIT` rolls the
transaction back. Every exit 2 of a commit call is treated as in doubt,
connection failures included, since telling them apart would mean matching
`psql`'s stderr, which the probes found identical before and after a COMMIT
that landed; the next snapshot's lookup settles it.

**55P03 means different things at the two calls.** At the commit it is a wait
for the head row that another write's transaction holds: a lost turn, retried
like a 40001. The snapshot is a plain read that takes no row lock, and a
`FOR UPDATE` does not block a plain read (`clarifications.md`, ADV-C4), so a
55P03 there can only be a lock a migration or other DDL holds: a refused read,
69, naming the lock (the timeouts table).

### The deadline and "register busy"

The deadline is 30 s from the first attempt's start (D7), kept in bash with
`SECONDS`, whose one-second grain is enough. No attempt starts after it. A write
none of whose commit calls was in doubt, and whose attempts all lost their
turn, exits 75: "register busy: <k> attempts in
30 s, each beaten by another write; nothing was written; a retry in seconds is
safe". With no cap on attempts, the k-th writer of a burst commits on its k-th
attempt; the cost is about k(k+1)/2 verb runs in a burst of k, 55 for ten, and
test 4 injects that burst. The value is configurable for tests through
`LANES_REGISTER_DEADLINE_S`. 75 narrows Amendment 22's S1, which gives 69 to a
transaction the database refuses; under "(A″) with 69", this row exits 69.

### A write in doubt

A commit call that ends with `psql`'s exit 2 or its own timer is in doubt. The
loop goes on: the next snapshot looks the `write_id` up and exits 0 if it is
there. If it is not, the next commit is safe even while the old transaction is
still open, because that transaction holds the head row's lock: the new commit
waits up to its `lock_timeout`, gets 55P03 and tries again, and once the old
transaction ends, either the lookup finds the id or the head is free. The
REGISTER-WRITER role's `idle_in_transaction_session_timeout` bounds how long a
transaction whose client vanished can hold the head row (D9). Where the
snapshot cannot reconnect, the writer keeps trying for up to 10 s and then
exits 3: "cannot confirm whether register write `<id>` committed; nothing else
was written", with the lookup (`lanes-index journal --write-id <id>`) and the
rerun (`LANES_WRITE_ID=<id> lanes-edit.sh <the same arguments>`). The pending
record stays until a rerun consumes it.

**A write in doubt never exits 75.** Its own orphaned transaction can hold the
head row, so its later commits get 55P03, which is otherwise a lost turn; were
it to exit 75 at the deadline, "nothing was written" could be false, the
pending record would go, and a rerun would mint a new id, which is ADV-C3's
second `CLAIMED`. So a write any of whose commit calls was in doubt, on
reaching the deadline, makes one lookup once the head row is free, within the
10 s reconnect window. The lookup's call takes the head row with
`FOR UPDATE` under its `lock_timeout`, reads the journal by the id, and rolls
back; holding the lock proves no transaction of this write is still open. It
exits 0 if the write is there, 75 if it is absent and the head row was free,
and otherwise 3 with the id, the pending record kept. Agreement test 12 gains
this case.

### What lane 4's phase plan must confirm for S1

- **[L4-a]** that each verb runs unchanged on a scratch tree, and which verbs
  reach the commit seam more than once in one run. One journal entry carries
  one message, "exactly as today", so such a verb needs a rule: one entry with
  a message the design names, or the verb split in two. Lane 4 picks the rule;
- **[L4-a′]** the internal flag that bypasses the resolver and S3 for the
  scratch tree; the arms with side effects outside the register, which run once
  after COMMIT; the expectations each act's write verbs carry (D5); every
  failure of the mode query and the exit code for Q6(c)'s refusal (D3); the
  pending record's format (D8); and the callers that read exit 3 as "written"
  (D8);
- **[L4-b]** that the staged change set gives the journal's edits for the five
  acts that change several files (`LANDING` and `LANDED`, `rename-lane`,
  `retire-rows`, `archive-rows`, `migrate-state-cells`);
- **[L4-c]** the cost of one attempt: two Python starts, two `psql` starts, the
  scratch tree and the verb. Test 9 records it; if it is too high, one
  `lanes-index` process can serve both calls of an attempt over a pair of
  named pipes, which bash 3.2 has.

## S2: the journal and the renderer

### The journal

Each committed write is one journal entry, the interface between S1 and S2:
seq, `write_id`, the database UTC of its commit, verb, lane, workstation, mode,
author identity (D15), today's commit message exactly (`LANES(<lane>@<ws>): …`
or `LOG(<lane>@<ws>): …`), the line edits across files with creates, renames
and removals, and the per-file digests after it. Replaying the entries from
the import's baseline gives every rendered file byte for byte; that function
has no git in it, and is phase 1's (Q6(c)). Its form is lane 1's ("The
schema").

**Operator entries** (D24). An entry whose edits touch only `lanes/STORE` is an
operator entry, flagged in the journal; lane 1 adds the column in its section.
There are exactly two kinds, the cutover's seq 1 and the rollback's last seq,
each written in its command's own transaction, the one that changes the
switch, so each passes the switch check by construction and nothing else is
journalled while the switch is not `primary` (S5). The renderer never renders
one: the command that wrote it pushes it with the renderer identity (D23) and
confirms it by Amendment 23's S4 read-back.

### The renderer, in the private-index form

After a write commits, `lanes-edit.sh` forks `lanes-index render --checkout <c>`,
detached as `index_nudge` forks today (`code/lanes-edit.sh:2561`), with
`LANES_NO_FETCH` removed from its environment (D12). `<c>` is the writing
lane's checkout, `<W>-lanes/<lane>/`, from Amendment 23's resolver (phase 3).

- **One render at a time per checkout, and no fork waits.** The renderer takes
  its own lock, a `mkdir` mutex in the checkout's git directory
  (`lanes-render.lock`), never the lane's write lock. A fork that finds it held
  leaves a rerun mark (`lanes-render.rerun`) and exits at once, as `SyncLock`
  does (`code/lanes-index:1539–1543`). [L4-3]
- **A pass.** Clear the rerun mark; fetch `origin main`, bounded by
  `LANES_GIT_TIMEOUT`; find `m`, the last `Register-Seq` on `origin/main`'s
  first-parent history, and its commit `R` (an operator seq counts as rendered
  once its own command's read-back has put it there); check for a divergence
  (below); read the journal entries after `m`; and for each seq `k` in order,
  build the images at `k`, then the commit: a private index (`GIT_INDEX_FILE`
  in the checkout's git directory), `read-tree` of the parent, the changed
  blobs written with `hash-object -w` and staged with `update-index`,
  `write-tree`, and `commit-tree --no-gpg-sign -p <parent>`, with the entry's
  message and `Register-Seq: <k>` in its trailer block (D15), its author and
  committer and both dates set to the entry's commit UTC, in UTF-8. The first
  parent is `origin/main`'s tip; each later commit's parent is the one before
  it. Where the next seq the mirror lacks is an operator entry, the pass stops
  there and renders nothing past it: its command owns it.
- **A fetch that fails** ends the pass with nothing pushed: SQL stays true, the
  failure is recorded with the mirror state (D16) where the database can be
  written, `status` shows the lag, and the next render catches up.
- **The push.** `git push origin <tip>:refs/heads/main`, never forced. Under
  Q21(b)(B) it pushes with the renderer identity (D23).
- **A lost race leaves nothing to discard.** A rejected push moved no ref and
  touched no HEAD; the renderer fetches and starts the pass again, up to six
  times, as R3 proposes and as Amendment 23 bounds a writer, and then ends with
  the lag shown by `status`; the next fork resumes. Two renderers that start
  from the same tip build the same commits, byte for byte, so a second push of
  the same sha is a no-op. [L4-1], [L4-2]
- **Before it lets go,** the renderer reads the journal head again; while the
  mirror lacks a seq, or a rerun mark is set, it makes another pass. So a burst
  gives at most two renders, and the last write of a burst is rendered even if
  no write follows anywhere. [L4-3] **A pass that pushed nothing ends the
  renderer,** so that a pass that cannot progress (an authentication failure, a
  divergence, an operator seq its command has not yet put on origin) never
  loops; the next fork tries again.
- **Killed between `commit-tree` and the push,** it leaves only unreferenced
  objects: no ref moved, nothing on the lane's HEAD, nothing for the next
  handoff commit or the next render to refuse (ADV-C6; the architecture seat's
  concern 3). [L4-4]
- **Its database reads** carry their own timeouts ("Roles and credentials").
  A render that cannot read the journal ends, and the lag stays until the next
  render.

Under Q27(a) the renderer takes S3's fetch and nothing else. Under (b) it would
also take the lane checkout's lock, run S3's rows and the fast-forward there,
and refuse to render while that checkout is itself refused.

### Recognising a render commit, and a divergence

A render commit is one that carries `Register-Seq: <k>`, changes only the
render set, and whose render-set tree equals the images at `k`. Any commit
after `R` on `origin/main` that changes a render-set path (pathspec: the four
paths, with `lanes/log/README.md` excluded under Q18(A)) and is not a render
commit stops the renderer: it renders nothing over it, records `diverged at
<sha>` in the database (D16), and `status` prints it. Commits outside the
render set (handoffs, `lanes/STORE`, `lanes/repos.tsv`) are part of the tip. A
mirror whose render set at `m` differs from the images at `m` is reported the
same way. `reconcile` re-renders what is missing and imports nothing.

### The two ways out of a divergence

Each runs on a person's word, as one journal entry, so the mirror keeps one
commit per seq (ADV-C2). Each goes through S1's commit call, so each runs only
while the switch is `primary`; that is why the rollback refuses to start on a
diverged mirror (S5):

- **the single-write import,** `lanes-index import --single <sha>`, loads the
  stray commit's line edits on the render set as one write, through S1's commit
  call, and refuses where they no longer apply to SQL's current images;
- **the renderer's revert,** `lanes-index render --revert <sha>`, journals an
  entry with no edits whose message names the stray commit, so that its render
  puts SQL's state back over it.

Either records the stray sha as resolved, which clears `diverged at`, and the
renderer then renders every seq after `m` onto `origin/main`'s tip, leaving a
tree byte-identical to a full render. [L4-5]

### The in-place form, if the prototype fails the private-index form

The renderer then commits on the lane checkout's HEAD. The next render in that
checkout discards an unpushed render commit before S3's checks, and S3's
"unpushed commits" row, at `lane-start`'s stamp and `lane-handoff`'s commit,
discards a render commit first, SQL holding it, rather than refuse the checkout
(lane 2's SHOULD f). Only a render commit is disposable, never a lane's own
commit under `lanes/`. Its lock is then the lane's, and the renderer's git half
waits for slices 2a and 2b (Q6).

### The post-write nudge

On primary a register write does not nudge `lanes-index sync` for the register,
and `lanes-index sync --source register` does nothing there and says so (R3), so
the mirror is never re-indexed into the database it came from. The inventory
nudge, the ledger half's, is unchanged.

### What lane 4's prototype must confirm

Marked in the text above: **[L4-1]** two renderers make identical commits;
**[L4-2]** a lost push leaves nothing to discard; **[L4-3]** a rerun mark
coalesces a burst into at most two renders, the last write included;
**[L4-4]** a renderer killed between its commit and its push blocks nothing;
**[L4-5]** each way out of a divergence leaves a full render. If [L4-1],
[L4-2] or [L4-4] fails, the in-place form above is the design.

## S3: the readers

### How a reader reads

Every reader on primary reads the register's text at the journal's head and
runs today's parser on it (D17): through the service where its socket answers
within its share; otherwise through `lanes-index register read` with the READER
credential and its own timeouts. A read never answers from a cache alone: the
images are proven at `n` by the digests in the same request. The image cache is
S1's. Through the service, the same holds per request ("Every answer that
decides state is read fresh", `specs/register-service/spec.md`).

### Each reader with the database unreachable

| Reader | Database unreachable, or the read refused |
|---|---|
| `who`, `binding`, `live-holder`, `next-free`, `lane`, `lane-end`, `lane-rename` | 69; no answer on stdout |
| `lane-last`, `register-row`, `session-lane`, `canon-lane`, `resolve-home` | 69; no answer on stdout; never 8 |
| `register-row-local` | 69; never 8 (D11: it reads the database on primary) |
| the `UserPromptSubmit` guard | 2, naming the store, the cause and the two ways out; during a declared outage, the mirror's answer (below) |
| `managed-projection` | 1, UNKNOWN; never 8 (S4) |
| `lane-start` (2A) | 69 before anything is renamed or written at `managed-projection` (`code/lane-start:1555`), the row count (`:1609`), `lane-last` (`:2335`) and `canon-lane` (`:1154`); 69 where the write must exist, `request-handoff` (`:2497`) and a new lane's `add-row` (`:3819`); a note and the launch goes on at `append-session-id` (`:3848`, `:3869`, `:3906`, `:4019`), `set-row-state LIVE` (`:3985`) and `log STARTED` or `RESUMED` (`:4178`); the Rule 3 stamp stays a git commit that never blocks |
| `lane-handoff` and the skill (2A) | the naming reads from the mirror, with the `read: mirror` line, so the handoff file is written; the `PAUSED` record's three writes (`code/lane-handoff:2874`, the late path `:2803`; `:2890`; `:2913`) exit 69, then a note naming the late `PAUSED` path, who runs it and when (P15); the lane stays `SWAPPING` |
| the restart skill's `lread` (`code/skills/restart/SKILL.md:48`), run with `LANES_NO_FETCH=1` | 69 from the verb; its `*)` arm already refuses on any code but 0, 8 and 2, so the skill binds no window, writes no stamp and prints no resume command |
| the listings `lanes` and `history` | the mirror's answer, the `read: mirror` line, the listing's own exit; 69 where a caller acts on the answer (`lane`, `lane-end --retire-dormant`) |
| the `SessionStart` block, the launcher's bare window reads | the mirror, offline; exit 0 |
| `lane-worktrees`' three direct `git grep` reads of `origin/<branch>` (`code/lane-worktrees:706`, `:997`, `:5556`), the `LANDED` read at `:997` among them | through a register verb; 69; nothing deleted |

The five `lane-start` arms that note an unknown code and carry on (`:1154`,
`:1262`, `:1271`, `:3079`, `:3430`) treat 75 as refuse-class. No read returns
75 under (A″), since only a commit can lose its turn, but the arms refuse on it
so that a later change cannot reopen the fail-open. `:1154` refuses 69 too, by
2A; at `:1262`, `:1271`, `:3079` and `:3430` a 69 stays the note it is today,
as R3's option A leaves those four reads.

### The mirror reads

A mirror read prints on stderr, never stdout, the third line of the `read:`
family (`code/lanes-edit.sh:2569–2573`): `read: mirror at <sha12>, <k> sequences
behind or unknown (sql unreachable: <why>)`. It reads `origin/main` as last
fetched, in `W` or the lane's checkout (Amendment 23's S5), and fetches only
where the reader fetches today. On primary, `--index` changes nothing and
prints no `read: index` line.

### The marker check on a workstation that is not primary (Q21(a)(A))

A reader or guard on a workstation whose mode is not `primary` reads the files
as today, so it checks for the marker on the same ref it reads:
`git cat-file -e <ref>:lanes/STORE`, where `<ref>` is the `origin/<branch>` or
the checkout's tree its read uses. Where the marker is present, an act refuses
with 69 and the guard with 2, both with Amendment 22's text ("this estate's
register is SQL; configure `mode=primary`"); the listings and the `SessionStart`
block read on and print the `read: mirror` line. Readers do not fetch, so the
check is as current as that ref, and the refusal says how old it is: a
workstation that has not fetched since the cutover reads the files until it
does, which is the mirror, and writes nothing, because its writers refuse after
their own fetch.

### The declared outage (Q26's (a′))

- **The declaration** is a file, `register-outage`, beside `lanes-index.conf`
  (D20). `lanes-index outage declare --until <UTC> --why "<Brett's words>"`
  writes it on Brett's word, with the database's label, his words verbatim,
  the UTC, the host, the expiry he names, and `mirror-seq=<m>`, the mirror's
  last `Register-Seq` on origin when he declares it; `lanes-index outage clear`
  removes it. Under Q23(a) the configuration mount is read-only, so both run on
  the host, once per host, as the flip does. It is not a row in the database
  that is down, and it needs no fetch: each guard reads it offline in the time
  it takes to open a file.
- **The guard** consults it only when its database read fails. Where the file
  is present, unexpired and names this workstation's database, and the tip the
  guard reads carries a `Register-Seq` of at least `m`, the guard answers from
  the mirror, as above, with `(declared outage <utc>: <why>)` in place of the
  cause, and exits 0 or 2 as that answer gives. A tip older than `m` is a
  refusal, 2: Brett's word covers writes during the outage, not how current a
  guard's last fetch is. Every write and every other deciding read still
  refuses with 69.
- **Why an expiry:** a declaration left in place could later let a guard read
  the mirror in an outage that is not estate-wide, the case (a′) exists to
  exclude. `lanes-index status` reports a declaration still in place while the
  database answers.
- Under Q4(b)(iii) a rollback runs only with the database up, so its start
  needs no declaration; the declaration is Brett's word only. Under (i), (ii) or
  (iv), a rollback's start may also declare it. The runbook (lane 1) places
  both commands.

### Callers' default arms (Q9)

69 and 75 are new to every caller, and 3 gains a meaning: on primary a write
that exits 3 may not have been written (D8), so a caller that reads 3 as
"written" changes too; the phase plan lists them. R2 found default arms that let an
unknown code through (`code/lane-start:1154`, `:1262`, `:1271`, `:3430`, with
`:3079` added; `code/lane-end:921–945`; `code/lane-worktrees:508–524`) and reads
that answer "no record" when they failed. Each arm refuses on 75 and 3, refuses
on 69 where S3's table says the read decides, and names the code. The phase
plan lists each arm with its file and line.

## S4: the managed seam

An enrolled lane's truth stays its JSON ledger under flock. This change adds
one storage-layer entry for the projection writer: the lane, the row's
managed-owner cell and the commit message, run through S1's attempt loop as the
verb `managed-projection`, so that on primary the projection lands in SQL first
and its render follows. The writer itself arrives with the 001 feature's T024,
and this change tests the entry with a fake writer. `managed-projection`
(`code/lanes-edit.sh:18467`) answers 0, 8 or 1 from the database through D17's
read, and 1, UNKNOWN, where the read fails. The database-backed answer wraps
`lane_is_managed_owned` (`:13324`) from outside the block ported verbatim from
`3c26041` (`:13188–13312`), which stays byte-identical.

## S5: import, cutover, rollback, and the disaster path

The runbook is lane 1's ("Operations: acts 3 and 4"). This section fixes what
the commands do.

**Every operator command writes for a lane** (D24; the frame review's F12).
Import, cutover, rollback and the disaster path run with
`LANES_LANE=<operator lane>` and go through that lane's checkout,
`<W>-lanes/<operator lane>/`, as Amendment 23's S2 item 3 asks of every write
("no lane, no write"); a shell or a `CLAUDE_NO_LANE=1` session names the lane
that way. Each `lanes/STORE` commit follows Amendment 23's S3–S4: the fetch and
checks first, then the push with the renderer identity (D23), then the
read-back. A push its read-back cannot confirm takes neither the success nor
the abort branch until a later read-back settles it. **No operator command
holds a database transaction across a git act** (F23): each stages and proves
outside any transaction, then makes its change in one short transaction, so the
REGISTER-WRITER role's 5 s idle-in-transaction limit (D9) never cuts one off.

### Import

`lanes-index import --at <C>` reads origin's objects in the operator lane's
checkout and never `W`, loads the register's tables and the journal's baseline,
seq 0 (`C`, the tree id of `C:lanes` and the digests), into a staging schema,
and renders from it into a scratch tree. It refuses unless the render is
byte-identical to `C`'s render set (`git diff --quiet`, and R3's three-part
proof: a blob id per path, the counts, and the same S3 answers from the files
and from SQL for every lane), and only then swaps the staged tables in, in one
short transaction. It takes origin only (Q4(c)(i)), keeps the 15 `WORD` lines
verbatim, flagged, with the warning (Q4(d)(i)), and can be rerun, and run into
a scratch schema for the rehearsal. A re-import replaces seq 0.

### Cutover

`lanes-index cutover` (D25), a one-shot command on Brett's word:

1. In one short transaction taking the head row, set the switch to `primary`,
   recording the import commit, and write seq 1, the operator entry adding
   `lanes/STORE` (D21, D24); the head goes from 0 to 1.
2. Build that commit on origin's tip, which descends from `C`, and check that
   the tip's `lanes` tree has the same id as `C`'s (R3's abort, which is S5's
   "moved under `lanes/`"), so a handoff commit on the tip only makes it
   rebuild. Push it, as a fast-forward of the tip, with the renderer identity,
   and confirm it by read-back. It carries `Register-Seq: 1`.
3. **The abort.** Where the tip's `lanes` tree has moved, the cutover pushes
   nothing; where a push was made, it first proves by read-back that the marker
   is absent from origin. Then, in one transaction, it sets the switch back,
   deletes seq 1 and sets the head back to 0 (the adversary seat's note under
   Q20), and the import is rerun at the new tip.
4. Then the flip, `mode=primary`, once per host (Q20(B); Q23(a)), with every
   write refused with 69 between the marker and a host's flip. Writes start at
   seq 2; R3b's smoke test is one `NOTED`, its row, `Register-Seq: 2`, the
   read-back and a lag of 0.

The ruleset (Q21(b)(B)) is already on: act 4's order is the drain, the last
`PAUSED` write on origin, the ruleset switched on by Brett, the import, the
cutover and the flips (F4). Switched on earlier, it would refuse every
files-mode register write, and under Amendment 23's S4 each refused writer
would keep its commit on HEAD and refuse its later writes.

`lanes/STORE` holds `store=primary`, `database=` the database's label in
`lanes-index`'s credential-free form (the `where` it already derives,
`code/lanes-index:1296`), `import=` the imported commit, `lanes-tree=` the tree
id of `C:lanes`, and `utc=`. It holds no URL with a password, no password and
no password-file path.

### Rollback, under Q4(b)(iii)

`lanes-index rollback`, on Brett's word, with `LANES_LANE=<operator lane>`,
from a shell or a `CLAUDE_NO_LANE=1` session:

0. **It refuses to start while `status` shows `diverged at`** (the frame review's
   F1). S2's two ways out resolve the divergence first; each goes through S1's
   commit call, so each runs only while the switch is `primary`.
1. In one short transaction taking the head row, set the switch to `files`,
   move the head from `k` to `k+1`, and write seq `k+1`, the operator entry
   removing `lanes/STORE` (D24). Nothing is journalled after it: every later
   SQL write refuses with 69, everywhere, whatever it has fetched. With the
   database unreachable, the rollback stops here, changing neither the switch
   nor the marker; in the partition, where the database is up and only this
   clone cannot reach it, the same.
2. Render until the mirror holds seq `k` and its render set is byte-identical
   to a full render, proven by tree id.
3. Brett removes the ruleset (Q21(b)(B)), now that the mirror is proven current
   and before the marker's removal is pushed, so that files mode can write
   again once the marker is gone (F4).
4. Push the commit removing `lanes/STORE`, seq `k+1`, with the renderer
   identity, and confirm it by read-back.
5. Clear `mode=primary` on every host, with the flip's reverse.

Step 1's call, if its timer expires, leaves the switch unknown, so the rollback
reads the switch and the journal again before step 2. `LANES_NO_FETCH=1`
cannot weaken the rule, because the check is the switch inside each write's
transaction (D12).

### The disaster path

A database lost for good is not a rollback (Q4(b)(iii)). With every
workstation's lanes paused first, as for the freeze, and the outage declared
(D20), and with `LANES_LANE=<operator lane>`:

1. Take `m`, the mirror's last `Register-Seq` on origin, and `b`, the newest
   backup's head seq, if there is one.
2. Run the bootstrap against the new database server first: roles are cluster
   objects, and a database backup does not hold them (F18).
3. If `b > m`, restore that backup into a new database; its journal already
   holds `m+1 … b`, and the renderer catches up. Otherwise re-import at
   origin's tip into a new database, with the head set to `m`, so the next
   write is `m+1` and `Register-Seq` stays monotonic in `git log` (D22).
4. Give the new database a new generation id, in its switch row. A pending
   record of the old generation is refused, since the write ids up to `m` went
   with the old journal, and a rerun under one could apply a write the mirror
   already holds (D8, D22).
5. Re-point every host's `register_url` and `reader_url` at the new database,
   from the host as the flip is (Q23(a)).
6. Set the switch to `primary` in the new database. `status` shows a lag of 0.
7. Clear the declaration; the lanes resume; the notice names the seqs lost
   after `m` (or `b`), so each lane checks its last writes against the mirror.

Lane 1 details these steps, including the marker's `database=` where the new
database's label differs. The loss is bounded by the renderer's lag at the
moment of loss, which `status` shows (P14; the product seat, 6549261651). The
database's host, its backups and its downtime limit are Brett's (Q22); when the
limit is reached, Brett chooses between waiting and this path, and the operator
lane runs it.

## S6: claims, and the order of `HOLD` and `LANDING`

### Claims

Rule 1's race is S1's commit. The loser's attempt finds the head moved, its
next snapshot shows the winner's `CLAIMED`, and the verb's own pre-check
(`rows_not_named "$lane"`, `code/lanes-edit.sh:16520`) refuses it with 2,
writing nothing (Q12(a)). `CLAIM-LOST` stays in the grammar and is no longer
produced on primary. A claim retried after an in-doubt COMMIT finds its own
`write_id` and exits 0, so no second `CLAIMED` is written (S1).

**The spellings of one object (D19; ADV-C10).** `claim` canonicalises the object
through `resolve_home` and `canon_object` (`:16509–16510`), which read
`lanes/repos.tsv`. Under Q18(A) that file stays in git, and on primary it is
read from the lane checkout's `origin/main` as last fetched, with no fetch on
the write path; its blob id is recorded in the journal entry. Two writers can
hold different tips, so the transaction does not trust one writer's key. Each
live claim records its set of spellings: the spelling as typed, the canonical
key, and every alias its writer's table maps to that key. The commit refuses
with 2 a claim whose set meets the set of another lane's live claim. Checked by
hand for every case in which one writer's table has the alias `X → Y` and the
other's does not, each claiming `X` or `Y`, in either order: the later claim is
refused in each. Two writers whose tables both lack the alias claim two
objects, as today: no transaction can know an alias no writer has read. A
removed alias stays in the sets of the claims that recorded it, so the rule
errs toward refusing. `claim --force` names the holder it takes over,
which the check then leaves out. The test is the clarification's: two claims of
one object, spelled two ways, one spelling an alias added between their reads,
from clones at different tips, end with one `CLAIMED` and one exit 2.

### `HOLD` and `LANDING`, under Q17(B)

| Line, on repository `R` | Effect on `R`'s `main` |
|---|---|
| `HOLD` | takes it, unless the writer's lane already holds it |
| `HOLD (refresh)`, `HOLD (run id)` | continues the lane's open `HOLD`; takes nothing new |
| `HOLD RELEASED`, `HOLD WITHDRAWN`, `HOLD EXPIRED` | releases the lane's `HOLD` |
| `LANDING` | takes it |
| `LANDED` | releases the lane's `LANDING`, paired by lane, PR and repository as `RULE6_AWK` pairs them (`code/lanes-edit.sh:6512`) |
| `QUEUED-HOLD` and every other line | takes and releases nothing |

- **The check is in the transaction** (D18). `append-line` stays the one verb;
  the commit call classifies the new line with `lanes-index`'s one classifier,
  `REGISTER_KIND_RE` (`code/lanes-index:680`) grown to Q17(B)'s kinds, and
  consults the structured layer's open takers at `n`.
- **The later writer.** A `HOLD` or `LANDING` on `R` while another lane's taker
  is open is refused: exit 2, nothing written, and stderr prints the earlier
  line verbatim with its register seq, so a landing script stops (P9). A lane's
  own `LANDING` after its own `HOLD` is the ordinary sequence and is written.
  "Nothing written" is D18's reading of S6, which says it of the claim loser
  only; it goes to Brett beside Q17 ("Differences from Amendments 22 and 23").
- **A line in a taking shape that names no repository** (it matches `HOLD — `
  or `LANDING — ` and no repository can be read from it) is refused with 2 on
  primary, since it would look like a hold to readers and order nothing. Lines
  of other shapes are written as free text, as (B) leaves them; refusing those
  too would be (C).
- **Rule 6's thirty minutes stay a duration.** A `LANDING` past the window with
  no `LANDED` still takes `main`, as `RULE6_AWK` still lists it and marks it
  (`:6551–6555`); the later writer's refusal says how far past the window it
  is.
- **A stale `LANDING` needs a release (open, for Brett).** Under Q17(B) only the
  holder's own `LANDED` releases a `LANDING`, and D18 refuses every other lane,
  so a lander that died between the two blocks that repository's `main` with no
  way out. The recommendation: a `LANDING EXPIRED` line, naming the holder,
  which any lane may write after Rule 6's window on Brett's word, and which
  releases the `LANDING` it names; tested like the other kinds. Until he
  answers, a person writes the holder's `LANDED` only where the landing did
  happen, never a false one.
- **UTC.** The line's UTC is the database's clock at the snapshot, in whole
  seconds (D13). A UTC a caller composed inside `append-line` text, and
  `log PAUSED --utc`, stay as written.

### `rename-lane`, under Q13(a)

The transaction first: the row's key cell, the log's move and `RENAMED` line,
and `aliases.tsv`, as one journal entry spanning files. Then the handoff file's
move and Rule 3 stamp, as a git commit in the lane's checkout under Amendment
23's S3–S4; the renderer never moves it. A failed move leaves the row's handoff
cell naming the file not yet moved, and rerunning `lane-rename` finds the
register already renamed and makes only the move and the stamp.

## Roles and credentials

### The three roles, and who holds which

| Role | Configuration keys | Held by | Scope |
|---|---|---|---|
| REGISTER-WRITER | `register_url=`, `register_passfile=` | `lanes-edit.sh`'s writes, through `lanes-index register commit` and `snapshot`; the renderer; `import`, `cutover` and `rollback` | the register's tables, the journal, the switch, the mirror state |
| READER | `reader_url=`, `reader_passfile=` | the guard's direct read (Q11(a)), every reader (D17), the service, `status` | SELECT on both halves |
| indexer | `url=`, `passfile=` | `lanes-index sync` and `reconcile` for the ledger half | the ledger half's derived tables only |

`lanes-index`'s known keys (`code/lanes-index:280`) gain `mode`,
`register_url`, `register_passfile`, and, under Q21(b)(B), `render_key`. The
bootstrap makes the roles and GRANTs from one admin connection Brett supplies,
used once and held by nothing, and sets the REGISTER-WRITER role's
`idle_in_transaction_session_timeout`.

**The REGISTER-WRITER secret's reach, plainly** (P20). It sits in a 0600 file
that every lane process on its host can read. Rule 9(e) forbids a hand-run SQL
write, and nothing technical prevents one; the exposure is that of today's git
push rights. Under Q11(a) the READER secret is in the same position. Neither is
ever in an environment a session inherits: `lanes-index` hands the password
file to its own `psql` child through `PGPASSFILE` and drops any inherited one
(`code/lanes-index:1299–1307`), and the renderer identity's key is used only in
the push's own environment (D23). **The render key's reach is the same, and
matters more:** under Q21(b)(B) it is the ruleset's only bypass, and it sits in
a 0600 file that every lane process on its host can read, so any of them could
push to the rendered paths past the gate. The gate stops a stale or careless
writer, which uses the lane's own push rights; it does not stop a process that
reads the key on purpose, and the manual says so.

### The timeouts (D9)

Every connection names one address, since `connect_timeout` applies to each,
and sets its bounds when it connects (`options=-c lock_timeout=… -c
statement_timeout=…`; the probes found that form and `PGOPTIONS` both work).
No register path uses `lanes-index`'s `PGCONNECT_TIMEOUT=5` (`:1306`) or its
300 s cap (`:1317`).

| Path | `connect_timeout` | `lock_timeout` | `statement_timeout` | Client timer | On expiry |
|---|---|---|---|---|---|
| the guard's direct read | 2 s | 1000 ms | 1500 ms | 4 s, cut to what the hook has left | 2 |
| the service's connection, per request | 2 s at each connect | 1000 ms | 1500 ms | lane 5's, under the client's wait | `refuse`, so 2 |
| a writer's snapshot | 2 s | 2000 ms | 5000 ms | 8 s | a failed read: 69, or in doubt where an earlier commit of the same write was |
| a writer's commit | 2 s | 2000 ms | 5000 ms | 8 s | in doubt |
| a whole write | — | — | — | 30 s before a new attempt; 10 s to reconnect in doubt | 75, or 3 |
| a reader | 2 s | 1000 ms | 3000 ms | 6 s | a failed read (S3's table) |
| the renderer's reads | 2 s | 1000 ms | 10 s | 15 s | the render ends; the lag stays |
| `status` | 2 s | 1000 ms | 5000 ms | 8 s | 69, with the lag from git where it can read it |
| `rollback`, each call | 2 s | 5000 ms | 10 s | 15 s | 69; step 1's expiry re-reads the switch |
| a migration | 2 s | its own, retried | — | — | refused; rerun |

Only the first row is measured: `connect_timeout=2` exits 2 at 2007 ms, a
`lock_timeout` of 1000 ms gives 55P03 at about 1011 ms, a `statement_timeout`
of 1500 ms gives 57014 at 1508 ms, and a 4 s client timer is the only bound on
a host that goes silent after connecting (`clarifications.md`, ADV-C4). The
other rows are the design's, and the test plan proves each exits inside its
bound. The REGISTER-WRITER role's `idle_in_transaction_session_timeout` is 5 s,
unmeasured; since it binds every session of the role, no operator command holds
a transaction across a git act or a render (S5), and which role runs DDL and
migrations, given that limit, is lane 1's. A migration sets its own
`lock_timeout`, because an `ALTER` only waiting for its lock already blocks new
plain reads.

## The guard and the service: the interfaces

The service's internals are lane 5's section, below. This frame fixes only what
the rest of the design relies on.

- **The guard's request** carries what it reads today and a service cannot read
  for itself: the hook's `session_id`, `cwd`, `agent_id` and `prompt`; the
  window reference and name; and the live record's `name`, `nameSource` and
  liveness facts, read in the guard's own container. **The answer** is `ok`,
  `refuse <reason>` or `offer <the three choices>`, and the guard exits 0 or 2.
  The offer file, the `/rename` typing and answer 2's two writes stay
  client-side; those writes are S1's transactions.
- **The fallback.** A service that is absent, refuses the connection or does
  not answer within its share is unavailable, and the guard reads directly with
  the READER credential (Q11(a)), printing `guard: direct (service unavailable)`
  on stderr. It never reads the mirror, except during a declared outage (S3).
- **One budget.** The hook's fixed costs (bash's start and parse to the arm at
  `code/lanes-edit.sh:16856`, the tmux read, the scan of the session records),
  the wait for the service, the direct read and printing the refusal fit inside
  `GUARD_TIMEOUT`, 5 s, together. The direct read's 4 s timer is therefore cut
  to what the hook has left, and the test's assertion is the probes': exit 2 in
  under 4.5 s in every case. `CLAUDE_NO_LANE=1` still exits 0 before any input
  is read. **Answer 2's two writes are on the guard's path too**
  (`lane-start --no-launch --bind` and `set-row-state`), and S1's 8 s call
  timers and 30 s deadline would outlast the hook, which a kill at 5 s turns
  into a passed prompt mid-act. How they fit the budget is lane 5's to decide,
  below.
- **The read API** answers `who`, `binding`, `live-holder`, `next-free`,
  `managed-projection` and the guard's triple check, each proven at the
  journal's head in that request (D17), and refuses with 69, or 2 for the guard,
  where the database cannot be read.
- **What the service never does:** write, render, or re-index. Writes keep their
  own connections (4A), the renderer is the writer's fork (D14), and the nudge
  does not re-index the register on primary (S2).
- **Without the service.** Under Q29(a) the service is the last slice, so every
  interface above has a direct path that works before it exists; the cutover
  waits for it only if the measured direct guard is over Brett's budget.

### The register service's internals — **PLACEHOLDER: lane 5's section**

> **PLACEHOLDER: lane 5's section.** Lane openRepoTools-5 has taken it (#165
> comment 6576934803). Its scope, copied from lane 3's fan-out post (#165
> comment 6576888068): "Lane 5, if it has room: the register service, Q2(c)
> to (h), under 4A. Where the socket lives and who its clients are; one per
> workstation or one per container; and how it starts on demand with no
> systemd. The guard's client inside `lanes-edit.sh guard`, with its deadline
> under 5 s, and the host-local facts."
>
> **Inputs it must account for:** the architecture seat's concern 4
> (`clarifications.md`): today's guard measured in four parts (reaching
> `:16856`, tmux, `transcript_holders`, the register read) before the client's
> form is settled, and test 10's 50 ms stated against that form; the budget
> above; and the interfaces above.
>
> **Also lane 5's to decide, from the frame review (REVIEW-frame-20261102):**
>
> - **Answer 2's writes inside the 5 s (F6).** Lane 3 leans to a row of the
>   timeouts table for them: each deadline and call timer is cut to what the
>   hook has left, and a busy, 69 or in-doubt outcome exits 2, naming the
>   write id. The alternative is to detach them and exit 2 with "binding in
>   progress".
> - **The `SessionStart` block's request (F24):** the block is to be the
>   service's client while it still reads offline through the mirror
>   (Amendment 22's S3), so its request is lane 5's to define.
> - Whether the service or the client reads the outage declaration, with its
>   expiry and `mirror-seq` (D20; F7, F11).

## The phase plan — **PLACEHOLDER: lane openRepoTools-4's section**

> **PLACEHOLDER.** Lane openRepoTools-4 writes this section under lane 3's
> fan-out (#165 comment 6576888068). Its scope, copied from that post: "For
> each of the seam split's three phases, list the functions, files and call
> sites that change, at `2a057a6d`. Cover `lanes-edit.sh`, `lanes-index`,
> `lane-start`, `lane-handoff`, the handoff skill and the guard, and give the
> interface each phase leaves for the next. Prove the private-index renderer of
> your concern 3 in a sandbox, against a local bare origin: two renderers make
> identical commits, a lost push leaves nothing to discard, and a rerun mark
> coalesces a burst."
>
> **What the frame fixes for it:** the interfaces `lanes-index mode`,
> `register snapshot`, `register commit`, `register read`, `journal` and
> `render` (S1, S2, S3); the commit seam's primary branch (D4); and the claims
> marked [L4-a] to [L4-c] in S1 and [L4-1] to [L4-5] in S2.

## The test plan — **PLACEHOLDER: lane openRepoTools-2's section**

> **PLACEHOLDER.** Lane openRepoTools-2 writes this section under lane 3's
> fan-out (#165 comment 6576888068). Its scope, copied from that post: "Each
> agreement test (1 to 14, and the council's additions), with its fixture, its
> oracle and the option it assumes. R1's fixture and the fake `psql`; CI's
> refusal switch, per 2A; the probes' burst, partition, in-doubt and
> guard-timing cases written as tests; and the measurement plan (Q10)."
>
> **Inputs it must account for:** P17 (the refusal switch scoped to the
> `ubuntu-24.04` jobs with the binaries, and every job that runs the
> real-engine modules named); the probe numbers for ADV-C1, ADV-C3, ADV-C4 and
> ADV-C7 (`clarifications.md`); ADV-C8's render from a verb run with
> `LANES_NO_FETCH=1`; ADV-C10's two spellings of one object; concern 4's
> four-part measurement. **And the frame's own choices,** each needing a test:
> the pending record's rerun (D8), `register-row-local` on primary (D11), every
> row of the timeouts table (D9), the declared outage and its expiry (D20), the
> marker's fields (D21), the refused hold line that names no repository (S6),
> and the disaster path's re-import (D22).

## Risks / Trade-offs

- **[A burst re-runs verbs]** In a burst of k writers, about k(k+1)/2 verb runs →
  the 30 s deadline outlasts the probes' worst drain; test 4 injects the burst;
  test 9 records the cost; a shared process per attempt ([L4-c]) if it is high.
- **[An orphaned transaction holds the head row]** A writer whose client vanished
  mid-transaction blocks every writer → `idle_in_transaction_session_timeout` on
  the role, `lock_timeout` on each commit, retries until the deadline, and 75,
  never a hang; a write whose own commit was in doubt makes one lookup once the
  head row is free and exits 0, 75 or 3, never a false "nothing was written"
  (D8).
- **[A rollback on a diverged mirror]** S2's two ways out need the switch at
  `primary`, and the rollback's last seq would be journalled after the flip →
  the rollback refuses to start while `status` shows `diverged at`, and writes
  its last seq in the transaction that flips the switch (D24; S5).
- **[The ruleset freezes files mode]** Switched on before the drain, it refuses
  every files-mode register write, and each refused writer keeps its commit on
  HEAD; left on after a rollback, it refuses files mode for good → it goes on
  after the drain and the last `PAUSED` write, and the rollback removes it
  before the marker's removal is pushed; Brett switches it (S5).
- **[The render key's reach]** Any lane process on a host can read the key that
  bypasses the ruleset → stated plainly in the manual (Roles and credentials);
  the gate stops stale writers, not deliberate ones.
- **[A database outage stops every prompt]** S3 makes the guard fail closed →
  the refusal names two ways out; the declared outage (D20) under Q26(a′); the
  host and downtime limit are Brett's (Q22).
- **[A stale declaration]** A declaration left in place could let a guard read
  the mirror in a partial outage → it expires at Brett's time; the guard
  consults it only when its read fails; `status` reports one left in place.
- **[A stale writer pushes files]** An old install that does not know the marker
  stops the renderer for every lane → the renderer's stop, S2's two ways out,
  the readiness check in every environment, and the ruleset under Q21(b)(B).
- **[The ruleset's paths]** A ruleset on all of `lanes/**` would also refuse a
  person's `repos.tsv` and `log/README.md` commits, which Q18(A) keeps in git →
  the ruleset names the render set and `lanes/STORE` (D23); whether the plan
  supports a ruleset that can say so is a fact still to find.
- **[The disaster loses unrendered writes]** Writes the mirror had not rendered
  exist only in SQL → the loss is bounded by the lag `status` shows; backups and
  their schedule are Brett's (Q22); D22's path restores what the newer copy
  holds.
- **[A rerun mints a second id]** A rerun in another environment misses the
  pending record → the report prints the rerun with `LANES_WRITE_ID`; the
  UNIQUE `write_id` stops a duplicate only when the id is reused, so the manual
  says so.
- **[New codes meet old default arms]** 69, 75 and 3 pass a default arm as
  "carry on" → each arm refuses and names the code (S3); one test per
  `lane-start` arm.
- **[The guard exceeds its 5 s]** A killed hook passes the prompt → one budget,
  the direct read's timer cut to what is left, and the probes' 4.5 s assertion.
- **[The scratch tree reads host-local facts at the snapshot]** Liveness for
  `claim --force`, `rename-lane` and `retire-rows`, and tmux for
  `request-handoff`, are read outside the transaction → the head check covers
  the register; the host-local facts are as current as today's, and exit 9's
  re-check stays local (Amendment 18(b)).
- **[`repos.tsv` is outside the transaction]** Two tips give two keys → D19's
  spelling sets, and the test.
- **[A second classifier]** The transaction's classifier and `RULE6_AWK` could
  disagree → both classify the imported history's 4,465 hold-shaped lines
  identically, as a test.
- **[Seam drift]** The write path waits on act 2's drafts, which `lanes-edit.sh`
  has moved 664 diff lines past → phase 1 goes on now; Q6(c)'s refusal guards a
  build with no mirror; the phase plan re-cites its lines at each landing.
- **[A Python start per call]** Two `lanes-index` starts per attempt, and one per
  read → test 9 measures them beside files mode; the service removes the reads'
  share where it runs.

## Migration and rollout (summary)

The runbook is lane 1's, in the placeholder below. In order:

1. **Phase 1, now, on `2a057a6d`:** `lanes-index`'s schema, journal, switch and
   roles; `register snapshot`, `commit`, `read` and `journal` against the
   fixture; `import --at` with its proof; `status` and `reconcile`; the
   renderer's pure function and the image cache; the readers' SQL path; the
   operator tools. `lanes-index` refuses `mode=primary` throughout. The service
   and its read API sit off the seams too, but are 007's last slice (Q29(a)),
   after phase 3.
2. **Phase 2, after slices 1a and 1b land in the code leg:** the attempt loop at
   the commit seam, the refusals of direct writes, S6's checks, and the marker
   check after S3's fetch and in the publish act.
3. **Phase 3:** the renderer's git half, rollback's push, and agreement tests 3,
   5 and 6; then the constant that lifts Q6(c)'s refusal.
4. **The service,** 007's last slice (Q29(a)), with the guard as its client.
   Tests 9 and 10 measure the direct guard first; act 4 waits for the service
   only if that guard is over the budget Brett names.
5. **The release** (Q24(a)): v1.2.0 with an rc1, the workBenches re-vendor and
   one image rebuild carrying the configuration mount (Q23(a)) and PostgreSQL
   16 (1A).
6. **The readiness check** in every environment that writes the register
   (P19): on Eagle, the host shell and each bench container (py-bench,
   cloud-bench and any other bench image, each a separate environment because
   it re-installs its vendored tools at start); on Raven, the same (Q22(a));
   the Codex lane's tools, through Brett; and dormant projects' lanes, which
   resume on whatever those environments carry. The list is confirmed in act
   3's runbook, Brett's act. A container started after the check is not
   covered, and the check says so.
7. **Act 3** (Brett's): Eagle and Raven configured on `mode=index` with the
   three roles (Q20(B)); the database on the host he names (Q22).
8. **Act 4** (Brett's word, his window): after slices 2a, 2b and 3 (Q28(i)) and
   a rehearsal on a scratch schema with an outage drilled (ADV-C10), and with
   P10's notice posted on each project's coordination issue before the window.
   Then, in this order: the drain (Q4(a)(i)), the last `PAUSED` write on
   origin, the ruleset switched on by Brett where the plan supports it
   (Q21(b)(B)), the import, the cutover, the flips, and the smoke test.
9. **Rollback** under Q4(b)(iii), after any divergence is resolved: the switch
   and the last seq, the render to current, the ruleset removed by Brett, the
   marker's removal pushed, the flips back. The disaster path where the
   database is lost (S5).

### Operations: acts 3 and 4 — **PLACEHOLDER: lane openRepoTools-1's section**

> **PLACEHOLDER.** Lane openRepoTools-1 writes this section under lane 3's
> fan-out (#165 comment 6576888068). Its scope, copied from that post: "Acts 3
> and 4 as a runbook, under Q4(a) (i), with how (ii′) differs. Also the
> rollback under Q4(b) (iii) and the disaster path; P8's three operator tools;
> the declared outage of Q26 (a′); P10's notice to the lanes; and R3c's landing
> path."
>
> **Inputs it must account for:** P13's (ii′) order (`clarifications.md`);
> P14's points under Q22's answer; P19's environments (item 6 above); ADV-C10's
> outage in the rehearsal. **What the frame fixes for it:** S5's commands and
> their order, the marker's fields (D21), the declaration's command and its
> expiry (D20), the disaster path (D22), and the flip run from the host under
> Q23(a).

## Open questions

**Round 2, by number.** Each is written above under its recommended option, and
the entry under "Round 2: the working assumptions" says what changes under the
others: Q4(a), Q4(b), Q4(c), Q4(d), Q7, Q11, Q12, Q13, Q14, Q17, Q18, Q19,
Q21(a), Q21(b), Q22, Q23, Q24, Q25(a), Q25(b), Q25(c), Q26, Q27, Q28 and Q29.
The eight that stand apart are Q4(a), Q4(b), Q7, Q21(b), Q22, Q26, Q28 and
Q29. "(A″) with 69" under Q7 also decides D7's number.

**Round 1's typed confirmation.** Q2(a) and (b), Q6, Q8 and Q20 are provisional
until Brett confirms "1A 2A 3A 4A" by typing.

**Not yet routed to Brett:** the items so marked under "Differences from
Amendments 22 and 23, for Brett": D5's per-verb transactions and the S3
narrowing of any act that cannot carry its expectations; D18 as a reading of
S6, beside Q17; D1's schema of its own; D25's `lanes-index cutover`; and a
release for a stale `LANDING`, for which the recommendation is a
`LANDING EXPIRED` line naming the holder, which any lane may write after Rule
6's window on his word, tested (S6).

**Values Brett names later:** Q29's budget for the guard; Q22's database host,
its backups and its downtime limit; the expiry of any outage he declares.

**Facts still to find:** whether the organisation's GitHub plan supports a push
ruleset with a deploy-key bypass, and whether it can name the render set
exactly (Q21(b), D23); whether Eagle and Raven install by pipx or `./setup.sh`,
and whether v1.2.0 carries #165 alone (Q24).

**Deferrable within the design:** the image cache's form and the per-call
process cost ([L4-c]), which change no spec; the writers' and readers' timeout
values other than the guard's, which the test plan confirms.

### Routed to the section writers by the frame review

Lane 3's independent review of this frame (`REVIEW-frame-20261102.md`, sha256
`0689dde8…`, against `c7253108`) left these points for the sections that fold
in later. The placeholders above are left as they were; lane 5's carries its
own points.

- **Lane 1, the schema and the operations:** the operator-entry flag, seq 1
  and the rollback's last seq (F1, F2); the switch state the rollback leaves;
  the generation id in the switch row (F10, F18); the arguments each journal
  entry records, for the reused-id refusal (F10); the open takers per
  repository, and the stale-`LANDING` release if Brett takes it (F13); which
  role runs DDL and migrations under the 5 s idle limit (F23), and which writes
  the mirror state; whether a re-import after a rollback resets the journal or
  continues it; the commands for the bootstrap, the readiness check and the
  flip; and in the runbook, the ruleset inside the freeze and its removal in
  the rollback (F4), the operator lane and the read-back (F12), the divergence
  resolved before a rollback (F1), `mirror-seq` in the declaration (F11), the
  disaster path's re-pointing, roles and marker (F18), and the per-host flip
  and declaration on Eagle and Raven (Q22(a)).
- **Lane 4, the phase plan:** the rule for a verb that reaches the commit seam
  twice (F21); the internal flag for the scratch tree (F22); the files the
  snapshot returns, `repos.tsv` and `log/README.md` included; output buffering
  and the arms with side effects (F14); every failure of the mode query and
  Q6(c)'s own code (F8); the acts and their expectations (F5); the callers that
  read exit 3 as "written" (F9); the in-doubt rule at the deadline (F3); the
  pending record's format and age (F10); the renderer's progress bound and how
  it stops at an operator seq (F15, F2); where a hand edit is found (F16); the
  marker check for readers (F17); [L4-1] to [L4-5]; `commit-tree`'s
  determinism (F29); and the push URL the deploy key needs against origin's
  HTTPS remote.
- **Lane 2, the test plan:** a test for each MUST as folded (a write in doubt at
  the deadline; a diverged rollback, and a failed seq-1 push; the ruleset, then
  a files-mode write after the rollback; a declared outage with the service and
  the database both down; answer 2 inside 5 s; two acts racing a binding, with
  the expectation refusing the second); `commit`'s 2 in test 8 (F19); a record
  reused by an unrelated identical command (F10); a guard reading a mirror older
  than `mirror-seq` (F11); the disaster path with an old record (F18); a failed
  mode query on `mode=index` and on primary (F8); output printed once in a burst
  (F14); which cases need TCP rather than the Unix socket; and no test that
  expects F33's backstop to fire.
