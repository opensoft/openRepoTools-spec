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

Status: a frame with the core written, and three sections folded in: "The
register service's internals" (lane openRepoTools-5), "The schema" (lane
openRepoTools-1's part 1), and lane 1's part 2, the operations, as
`operations.md` beside this file. Two parts are still placeholders, each
marked **PLACEHOLDER** and owned by a lane under lane 3's fan-out (#165
comment 6576888068): "The phase plan" (lane openRepoTools-4) and "The test
plan" (lane openRepoTools-2). Lane 3 folds each in as it arrives. Round 1 of Brett
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
    from each guard; nothing in this design provides one. Under Q4(b)(iii),
    a rollback's start declares the outage too, marked as from a rollback;
    the rollback is Brett's act, so the declaration is his (D20; lane 3's
    ruling, #165 6578020193).
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
  migration it does not have. The DDL is lane 1's, in "The schema" below. This departs
  from Amendment 22's "Everything Amendment 14 built is reused", whose table
  makes #160's schema "the register's own tables"; #160's schema stays for the
  ledger half, and the register gets one of its own. It is listed for Brett
  below.
- **D2. One database client: `lanes-index register …` subcommands.** The write
  path calls `lanes-index register snapshot` and `lanes-index register commit`;
  readers call `lanes-index register read`, the guard once a prompt with
  `--for guard --prefetch` (SVC-8); the service is `lanes-index serve`
  (SVC-3); the renderer, import, rollback and status are `lanes-index` too.
  The alternative, `psql` driven from bash, would put the connection string in
  bash, parse SQLSTATEs in bash, and keep a second copy of the timeouts; the
  service as the client is excluded for writes by 4A. Cost: a Python start per
  call, which test 9 measures (S1).
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
  of S3 goes to Brett beside this difference from R2 (below). D5 waits for
  lane 4's phase plan (lane 3's ruling, #165 6578020193).
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
  live is waited for, not reused; and an id the journal holds for another
  lane, verb or arguments, or one minted against another generation, is
  refused with 2 (LR007, which the commit raises under the head row's lock,
  §1.3). Lane 4 owns the record's format, and the printed rerun carries the
  generation. A write any of whose commit calls was in doubt never exits 75:
  at the deadline it makes one lookup, `find_write` (§1.3.1), within the 10 s
  reconnect window, and exits 0 if the write is there, 75 if it is absent with
  the head row's lock granted, and otherwise 3 with the id, the record kept
  (S1).
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
  beside Q17 (below) (S6). The comparison is by a canonical repository key,
  held as D19 holds an object's spellings (lane 3's ruling, #165
  6579459518). v2 keys `main_slots` by `repo_key` as each line gives it and
  holds no spellings for it, so one repository spelled two ways is missed:
  an open point for lane 1 (its `repo_spellings`), and a test for lane 2.
- **D19. A claim's spellings are checked in the transaction** (ADV-C10). Each live
  claim records its spelling, its canonical key and the aliases its writer's
  `repos.tsv` mapped to that key, and the commit refuses a claim whose set of
  spellings meets another lane's live claim's (S6). The refusal rests on the
  head row's serialization, which orders every write, plus `spellings`
  (`object_holds.spellings`, §1.2), not on an index (LR005; lane 3's ruling,
  #165 6578020193).
- **D20. A declared outage is a file beside `lanes-index.conf`,** written by
  `lanes-index outage declare` on Brett's word, with an expiry he names and the
  mirror's `Register-Seq` at the moment he declares it, `mirror-seq=<m>`
  (`--mirror-seq <m>` where the host cannot fetch); the guard answers from the
  mirror only where SVC-12's three conditions hold: its database read failed,
  an unexpired declaration names this database, and the tip it reads carries a
  `Register-Seq` of at least `m` (Q26's (a′); S3). **A rollback's start
  declares one too,** marked as from a rollback: the rollback is Brett's act,
  so the declaration is his, as Q26(a′) requires, and a guard's read that
  finds the switch at `files` counts as failed (lane 3's ruling, #165
  6578020193; `operations.md` §2.5). It goes to Brett under Q26.
- **D21. `lanes/STORE`'s fields:** `store=primary`, `database=<label>`,
  `import=<sha>`, `lanes-tree=<tree id>` and `utc=<UTC>`, in a commit carrying
  the cutover's entry's `Register-Seq` (D24) (S5).
- **D22. The disaster path re-imports at the mirror's tip by default,** continuing
  the sequence, and restores a backup only where it is newer than the mirror.
  A generation id, in the switch row and in every pending record, changes with
  each new database, and a record of another generation is refused, since the
  ids up to the mirror's seq are lost with the old journal. The bootstrap runs
  before the restore, as `bootstrap --roles-only`, because roles are cluster
  objects a database backup does not hold; every host is re-pointed at the new
  database. The re-import rewrites the marker as the cutover's entry, at
  `m+1`, so the next write is `m+2` (`b+1` after a restore; D24), and `ready`
  and `set-mode` check that `lanes/STORE` names this database (lane 3's
  ruling, #165 6578020193). `operations.md` §2.3 details the steps (S5).
- **D23. The renderer identity, under Q21(b)(B), is a deploy key** in a 0600 file
  named by `lanes-index.conf`, used only in the push's own environment; the
  ruleset names the render set and `lanes/STORE`. Every push of those paths uses
  it: the renderer's, and the cutover's and the rollback's commits of
  `lanes/STORE`, which their commands push themselves (D24). The ruleset goes on
  inside the freeze and comes off in the rollback (Q21(b) above; S5) (S2).
- **D24. Two operator entries, and the operator lane** (the frame review's F1,
  F2, F12). An entry whose edits touch only `lanes/STORE` is an operator entry,
  flagged in the journal (`journal.operator`, §1.2). Exactly two kinds pass the
  switch check, each in its command's own transaction. **The cutover's entry**
  is written in the transaction that sets the switch to `primary`, at the seq
  after the head: seq 1 at a first cutover, `m+1` after a re-import (`m` the
  import's own seq, the mirror's after a loss), or `b+1` after a restore (lane
  3's rulings, #165 6578020193 and 6579459518). After a loss its after-loss
  form, `lanes-index cutover --import <M> --after-loss`, writes the marker's
  rewrite as that entry, so the next write is `m+2`; after a restore,
  `lanes-index cutover --after-loss` with no import runs at `b+1`
  (`operations.md` §2.3). An abort of the restore form leaves no seq after `b`
  and changes nothing else, so the estate stays stopped until the cutover is
  run again; v2's `switch_to_files` would set `files` and drop `lanes/STORE`'s
  rows instead, which lane 1 changes. **The rollback's last seq** is written in
  the transaction that sets the switch to `files`. Besides the import's
  baseline (seq 0, or `m` after a loss), which is journalled at `files`,
  nothing is journalled while the switch is not `primary` except these two
  entries. The renderer never renders an operator entry; its command pushes it
  with the renderer identity, confirms it by Amendment 23's S4 read-back and
  records that with `record_marker`, and a push it cannot confirm exits 3
  (`operations.md` §2.1). The operator commands (import, cutover, rollback,
  the disaster path) run with `LANES_LANE=<operator lane>` through that lane's
  checkout, as Amendment 23's S2 item 3 asks of every write, and none holds a
  database transaction across a git act (S2, S5).
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
| Q26(a′) and D20: the guard answers from the mirror in a declared outage, under SVC-12's three conditions; and a rollback's start declares one, marked as from a rollback, the rollback being Brett's act (`operations.md` §2.5) | Amendment 22's S3 | routed: Q26 |
| Q27(a): "takes S3's checks" read as S3's fetch for the private-index renderer | Amendment 23, "Relation to Amendment 22" | routed: Q27 |
| D18: the later `HOLD` or `LANDING` writer writes nothing | a reading of Amendment 22's S6 | not yet routed; goes beside Q17 |
| D1: the register in a schema of its own, not #160's | Amendment 22, "Everything Amendment 14 built is reused" | not yet routed |
| D25: `lanes-index cutover` as a command of its own | Amendment 22's S5 and its ratified names | not yet routed |
| A release for a stale `LANDING` (S6): lane 1's v2 carries it as the line kind `LANDING EXPIRED`, `register_lines.for_lane` and LR005 (§1.2, §1.3.1), tested on the engine and inert until a verb writes it | Amendment 10 and Rule 6, under Q17(B) | not yet routed: an open point for Brett |
| A stray landing during a rollback: once the rollback's entry sets the switch to `files`, S2's two ways out, each a journal entry that needs `primary`, cannot run, so a stray commit that lands on the rendered paths before the marker's removal has no journalled way out (lane 1's v2) | Amendment 22's S5, under Q4(b)(iii) | not yet routed: an open point for Brett |

## Architecture overview

### The components and their seams

| Component | Role on `mode=primary` | Credential |
|---|---|---|
| `lanes-index` | The one configuration parser and the one database client: `mode`, `register snapshot`, `register commit`, `register read`, `journal`, `render`, `import`, `cutover`, `rollback`, `status`, `reconcile`, `outage`, `serve` (the service, SVC-3), and the operator tools (bootstrap, readiness check, flip) | per subcommand: REGISTER-WRITER, READER, or the bootstrap's one-time admin connection |
| `lanes-edit.sh` | Every verb, unchanged in its logic. Its three write seams: the resolver, which the write path no longer needs and the renderer does; `handle_preexisting`, where the captures refuse; and the commit seam, which hands a change set to the attempt loop (D4). Its readers enter at `register_text_checked` | none of its own: it calls `lanes-index` |
| The renderer | `lanes-index render`, forked by a writer after its COMMIT, in that lane's checkout; builds one commit per sequence on `origin/main` with a private index and pushes it | REGISTER-WRITER; the renderer identity's key under Q21(b)(B) |
| `lanes-register`, the `lanes-index serve` subcommand (SVC-3) | The local-socket service, one per environment (SVC-1): one READER connection per process, answering the register's text at `n` for the guard and the other readers, which decide (4A; D17; SVC-7). Last slice (Q29(a)); its internals are lane 5's section | READER |
| The guard | `lanes-edit.sh guard`, the hook: the service's client through one `lanes-index register read --for guard --prefetch` a prompt (SVC-8), falling back to a direct read (Q11(a)), and deciding by today's parser | READER, through `lanes-index` |
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

## The schema

Lane openRepoTools-1's part 1, folded from its section v2,
`/workspace/projects/.openrepotools-private-prep/work-20261102/165-section-v2/section-lane1-v2.md`
(sha256 `dd061272306f23f4b09d8cab0c5096df22a1bc2005bdf3951d3170ca89dab965`), with
`fix-v2.diff` (sha256 `d1e05586cc3a22c6f9c7d33259e045040c16fd5cdedb8ea6740861304e8cb12a`, from
`d36a4232` to v2) and `renames.md` (sha256
`e0087c0ff5f8fd19425df75224617d540391819115614c6a5ad3541d37ff3f33`) beside it, under
lane 3's rulings on v1 (#165 comment 6578020193) and on v2 (6579459518). Part
2, the operations, is `operations.md` beside this file; a cross-reference
names it, as in "`operations.md` §2.3". Lane 1's numbering is kept: §1.1 to
§1.6 here, §2.1 to §2.9 there.

- **Basis:** the proposal at `0607526d`. A citation of `proposal.md`, `clarifications.md` or `council-review.md` is to the frozen copy, sha256 `b284a0eb2078fd24a48e833685b0073e881fdbe1625a1019d81a55a4aadaa340`, `636fb45369f825dd7f3d763bdb8d8bcb475ef4d76a3f7cf23203b907713ac2ff` and `9fa18c5ed76d88d4f25b3f8eaa37626ad0511985061f9e0649ff49d13c0fce62`.
- **Round 1, held as provisional:** Q6 = 1A (the split by seam), Q8 = 2A, Q20 = 3A (`mode=primary` written at act 4), and Q2(a) and (b) = 4A.
- **Round 2, the recommended options:** Q4(a) (i), Q4(b) (iii), Q7 (A″), Q21(b) (B), Q22 (a), Q26 (a′), Q28 (i) and Q29 (a). Where a part turns on one of them, it says what changes under each other option.

**New and unproven** (lane 3's rulings): the objects this part adds that the
proposal does not name are accepted as new and unproven: `set_seq`,
`mirror_state` with `record_mirror_state`, `spellings`, LR005, and v2's
`journal.operator` flag, `generation`, `args_sha256`, LR006, LR007,
`find_write`, `record_marker` and `switch_to_files`. Lane 1's v2 run on
PostgreSQL 16.15 exercised them (`renames.md`); each still needs a real-engine
test in lane 2's plan before it counts as proven.

This part gives the SQL register's tables as PostgreSQL 16 DDL, the roles and their GRANTs, the version check with forward-only migrations, and what the schema itself holds of S1's transaction under Q7 (A″). It starts from R3a's sketch (`r3a/REPORT.md:156-283`) and reconciles it with the proposal at `0607526d`, the council's folds (`council-review.md`, `clarifications.md`) and the code at `2a057a6d`, cited as the proposal cites it (`code/<file>:<line>`). It assumes round 1 as answered provisionally (4A: each `lanes-edit.sh` write opens its own transaction with its own REGISTER-WRITER credential, and the service keeps one connection for reads) and round 2's recommended options; §1.6 says what changes under the others. Other sections of `design.md` hold the rest: the runbook is part 2's, in `operations.md`, the write path's call sites and the renderer are lane 4's, and the service is lane 5's.

### 1.1 Where the register lives (Q3)

- **A schema of its own,** `lanes_register`, beside #160's `lanes_index`, in one PostgreSQL 16 database (Q1's 1A, `proposal.md:1121-1122`; CI's 16.15, `:1136-1139`). Q3 is the design's (`proposal.md:1202-1240`). This takes R3's answer (`r3a/REPORT.md:335`): #160's tables drop 38% of `LANES.md`'s bytes (`r3a/REPORT.md:42-58`), and #160 has no migration path (`:29-33`).
- **`psql` only,** no driver (`code/lanes-index:1272-1276`; `proposal.md:719-723`). Values are quoted as `_pg_quote` quotes them (`code/lanes-index:1227-1232`), with `standard_conforming_strings` on (`:1450`).
- **Bytes.** The database is `ENCODING 'UTF8'`; every path column is `COLLATE "C"`, because a file order is byte order, as #160 already has it (`code/lanes-index:1018-1024`). Every connection sets `TimeZone=UTC`. A line holds no LF or CR, and Postgres `text` holds no NUL (`r3b/REPORT.md:211`).
- **Not in the schema:** handoffs, `lanes/repos.tsv`, `lanes/README-lanes.md`, `lanes/.gitignore`, `lanes/log/README.md`, the SWAPPING and SWAPPED sidecars and #161's `worktrees` (`r3a/REPORT.md:297-302`; Q18 (A), `proposal.md:1752-1759`).
- **#160 on a primary workstation:** `lanes_index` keeps its tables and its own `SCHEMA_VERSION` (`code/lanes-index:101`). `sync --source register` does nothing and says so, and the post-write nudge never re-indexes the mirror (`proposal.md:1237-1239`, `:727-729`). The indexer keeps the inventory and ledger sources.

### 1.2 The tables

Migration 1, run by the bootstrap (P8, `proposal.md:607-611`) as the owner role (§1.4). The comments name the requirements the constraints carry. It is one transaction: it runs after §1.4's `CREATE ROLE`s and before §1.4's other statements, and §1.3.1's block ends it. Outside a transaction the two `SET LOCAL`s only warn (25P01), and every table lands in `public`, owned by the admin.

```sql
BEGIN;                                         -- one transaction (§1.5): SET LOCAL needs one
CREATE SCHEMA lanes_register AUTHORIZATION lanes_register_owner;
SET LOCAL ROLE lanes_register_owner;           -- so the owner owns every table and function
SET LOCAL search_path TO lanes_register, pg_catalog;

CREATE TABLE schema_version (                  -- §1.5: every client checks it first
  id           smallint    PRIMARY KEY DEFAULT 1 CHECK (id = 1),
  version      integer     NOT NULL CHECK (version >= 1),
  writers_from integer     NOT NULL,           -- oldest client version that may write
  readers_from integer     NOT NULL,           -- oldest client version that may read
  applied_utc  timestamptz NOT NULL,
  CHECK (1 <= readers_from AND readers_from <= writers_from AND writers_from <= version));

CREATE TABLE migrations (                      -- forward only: one row per script applied
  version       integer     PRIMARY KEY CHECK (version >= 1),
  script_sha256 text        NOT NULL CHECK (script_sha256 ~ '^[0-9a-f]{64}$'),
  applied_utc   timestamptz NOT NULL DEFAULT date_trunc('second', clock_timestamp()),
  by_lane       text        NOT NULL,
  by_ws         text        NOT NULL CHECK (by_ws <> ''));

CREATE TABLE register_switch (                 -- S1: the database half; lanes/STORE mirrors it (D24)
  id             smallint    PRIMARY KEY DEFAULT 1 CHECK (id = 1),
  mode           text        NOT NULL CHECK (mode IN ('files', 'primary')),
  marker_state   text        NOT NULL CHECK (marker_state IN ('absent', 'adding', 'present', 'removing')),
                                               -- F1, F2, F12: the marker as SQL knows it; adding and
                                               -- removing wait for S4's read-back (record_marker)
  marker_seq     bigint,                       -- the operator entry that last changed the marker
  marker_commit  text        CHECK (marker_commit ~ '^[0-9a-f]{40}$'),  -- the commit the read-back confirmed
  generation     uuid        NOT NULL,         -- F10, F18: the bootstrap's, or an after-loss cutover's
  repository     text        NOT NULL,                       -- owner/name
  database_label text        NOT NULL CHECK (strpos(database_label, '@') = 0), -- never a credential
  import_commit  text        CHECK (import_commit ~ '^[0-9a-f]{40}$'),
  import_tree    text        CHECK (import_tree ~ '^[0-9a-f]{40}$'),           -- C's lanes tree id
  import_seq     bigint,
  set_utc        timestamptz NOT NULL,
  set_seq        bigint      NOT NULL CHECK (set_seq >= -1),  -- the seq of the last change: an operator
                              -- entry's own (a cutover's 1 or m+1, a rollback's k+1), or an abort's base
  set_by_lane    text        NOT NULL,
  set_by_ws      text        NOT NULL CHECK (set_by_ws <> ''),
  CHECK (mode = 'files' OR (import_commit IS NOT NULL AND import_tree IS NOT NULL
                            AND import_seq IS NOT NULL)),
  CHECK ((mode = 'primary') = (marker_state IN ('adding', 'present'))),
  CHECK (marker_state = 'absent' OR marker_seq IS NOT NULL),
  CHECK (marker_state <> 'present' OR marker_commit IS NOT NULL));

CREATE TABLE register_head (                   -- S1, S2, S6: its row lock orders every write.
  id  smallint PRIMARY KEY DEFAULT 1 CHECK (id = 1),   -- Never a SEQUENCE or IDENTITY:
  seq bigint   NOT NULL CHECK (seq >= -1));            -- gaps, and not commit order. -1: empty

CREATE TABLE mirror_state (                    -- D16: what the renderer reports; it never gates alone
  id             smallint    PRIMARY KEY DEFAULT 1 CHECK (id = 1),
  diverged_sha   text        CHECK (diverged_sha ~ '^[0-9a-f]{40}$'),   -- a stray commit stopped the renderer
  found_utc      timestamptz,
  found_by_ws    text        CHECK (found_by_ws <> ''),
  last_error     text,                         -- the last failed pass, for status
  last_error_utc timestamptz,
  CHECK ((diverged_sha IS NULL) = (found_utc IS NULL)),
  CHECK ((last_error IS NULL) = (last_error_utc IS NULL)));

CREATE TABLE journal (                         -- S2: one entry per write, gapless, commit order
  seq         bigint      PRIMARY KEY CHECK (seq >= 0),         -- = Register-Seq
  base_seq    bigint      UNIQUE REFERENCES journal (seq),      -- one chain, no gap
  write_id    uuid        NOT NULL CONSTRAINT journal_write_id_key UNIQUE,  -- ADV-C3
  utc         timestamptz NOT NULL DEFAULT date_trunc('second', clock_timestamp()), -- S6
  kind        text        NOT NULL CHECK (kind IN ('import', 'cutover', 'write',
                                         'stray-import', 'stray-revert', 'rollback')),
  operator    boolean     GENERATED ALWAYS AS (kind IN ('cutover', 'rollback')) STORED,
                                               -- D24, F2: touches lanes/STORE alone; never rendered;
                                               -- the command that writes it pushes it
  verb        text        NOT NULL,
  lane        text        NOT NULL,            -- 'unknown' where today's subject says it
  workstation text        NOT NULL CHECK (workstation <> ''),   -- Rule 10
  author      text        NOT NULL CHECK (author ~ '^[^<>]+ <[^<>]*>$'
                                          AND strpos(author, E'\n') = 0),
  message     text        NOT NULL CHECK (message <> '' AND strpos(message, E'\r') = 0),
  git_commit  text        CHECK (git_commit ~ '^[0-9a-f]{40}$'),
  git_tree    text        CHECK (git_tree ~ '^[0-9a-f]{40}$'),
  repos_blob  text        CHECK (repos_blob ~ '^[0-9a-f]{40}$'),
  args_sha256 text        CHECK (args_sha256 ~ '^[0-9a-f]{64}$'),  -- F10: the lane, verb and arguments,
                                                                   -- as the pending record's digest
  CHECK (kind <> 'write' OR args_sha256 IS NOT NULL),
  CHECK (base_seq IS NULL OR base_seq = seq - 1),   -- the one root (journal_one_root) may sit at any seq
  CHECK (utc = date_trunc('second', utc)),     -- whole seconds, as utc_now writes them
  CHECK (kind <> 'import' OR (git_commit IS NOT NULL AND git_tree IS NOT NULL)),
  CHECK (kind NOT IN ('stray-import', 'stray-revert') OR git_commit IS NOT NULL));
CREATE UNIQUE INDEX journal_one_root ON journal ((base_seq IS NULL)) WHERE base_seq IS NULL;  -- one chain, one root

CREATE TABLE journal_edits (                   -- Rule 9(d): a known, finite set of line edits
  seq        bigint  NOT NULL REFERENCES journal (seq),
  ord        integer NOT NULL CHECK (ord >= 1),   -- applied in this order
  op         text    NOT NULL CHECK (op IN ('create', 'append', 'replace', 'delete',
                                            'rename', 'remove')),
  path       text    COLLATE "C" NOT NULL,
  line_no    integer CHECK (line_no >= 1),        -- in the image the earlier edits left
  old_sha256 text    CHECK (old_sha256 ~ '^[0-9a-f]{64}$'),
  new_text   text    CHECK (strpos(new_text, E'\n') = 0 AND strpos(new_text, E'\r') = 0),
  to_path    text    COLLATE "C" CHECK (to_path ~ '^lanes/(archive|log)/[^/]+\.md$'
                                        AND to_path <> 'lanes/log/README.md'),
  PRIMARY KEY (seq, ord),
  CHECK (path ~ '^lanes/(LANES\.md|STORE|aliases\.tsv|archive/[^/]+\.md|log/[^/]+\.md)$'
         AND path <> 'lanes/log/README.md'),      -- Q18 (A): the rendered paths, and lanes/STORE for the operator entries
  CHECK ((op IN ('replace', 'delete')) = (line_no IS NOT NULL AND old_sha256 IS NOT NULL)),
  CHECK ((op IN ('append', 'replace')) = (new_text IS NOT NULL)),
  CHECK ((op = 'rename') = (to_path IS NOT NULL)));

CREATE TABLE journal_files (                   -- S2: each touched file's bytes after the write
  seq     bigint  NOT NULL REFERENCES journal (seq),
  path    text    COLLATE "C" NOT NULL,
  present boolean NOT NULL,                    -- false: removed or renamed away
  sha256  text    CHECK (sha256 ~ '^[0-9a-f]{64}$'),
  final_newline boolean,                       -- files.final_newline after the write
  PRIMARY KEY (seq, path),
  CHECK (present = (sha256 IS NOT NULL) AND present = (final_newline IS NOT NULL)));

CREATE TABLE files (                           -- the rendered files as they stand
  path          text    COLLATE "C" PRIMARY KEY
                CHECK (path ~ '^lanes/(LANES\.md|STORE|aliases\.tsv|archive/[^/]+\.md|log/[^/]+\.md)$'
                       AND path <> 'lanes/log/README.md'),
  final_newline boolean NOT NULL,
  sha256        text    NOT NULL CHECK (sha256 ~ '^[0-9a-f]{64}$'),
  last_seq      bigint  NOT NULL REFERENCES journal (seq));

CREATE TABLE doc_lines (                       -- the truth: a file is its lines in line_no order
  line_id      bigint  GENERATED ALWAYS AS IDENTITY PRIMARY KEY, -- stable; writes are serial under
                                                                  -- the head lock: ascending = append order
  path         text    COLLATE "C" NOT NULL REFERENCES files (path) ON UPDATE CASCADE,
  line_no      integer NOT NULL CHECK (line_no >= 1),
  text         text    NOT NULL CHECK (strpos(text, E'\n') = 0 AND strpos(text, E'\r') = 0),
  written_seq  bigint  NOT NULL REFERENCES journal (seq),
  written_mode text    NOT NULL CHECK (written_mode IN ('files', 'primary')), -- S1: mode last written
  written_ws   text,
  CONSTRAINT doc_lines_pos UNIQUE (path, line_no) DEFERRABLE INITIALLY IMMEDIATE, -- a delete shifts lines
  CHECK (written_mode = 'files' OR coalesce(written_ws, '') <> ''));          -- Rule 10, new writes

-- The structured layer: today's parsers' rows, filled in the same transaction. A constraint
-- that history breaks binds only mode = 'primary' (r3a/REPORT.md:348).
CREATE TABLE lanes (                           -- one row per lane, LANES.md and archive/ together
  lane_key    text    COLLATE "C" PRIMARY KEY,
  lane        text    COLLATE "C" NOT NULL CHECK (lower(lane) = lane_key),   -- Amendment 15
  row_line_id bigint  UNIQUE REFERENCES doc_lines (line_id) DEFERRABLE INITIALLY DEFERRED,
  aliases jsonb, home text, workstation text, started_utc text, handoff_path text,
  state_phrase text, objects_cell text, session_ids jsonb, last_session text,
  archived    boolean NOT NULL,
  owner       text,                            -- a copy; S4 answers from the row's own text
  last_seq    bigint  NOT NULL REFERENCES journal (seq),
  last_mode   text    NOT NULL CHECK (last_mode IN ('files', 'primary')));
CREATE INDEX lanes_by_last_session ON lanes (last_session);    -- binding; the guard's triple

CREATE TABLE log_lines (                       -- every log line; unreadable ones kept (7(b))
  line_id        bigint  PRIMARY KEY REFERENCES doc_lines (line_id) ON DELETE CASCADE
                         DEFERRABLE INITIALLY DEFERRED,
  file           text    COLLATE "C" NOT NULL,
  ordinal        integer NOT NULL,
  lane           text    NOT NULL,
  verb text, session text, workstation text, utc text, utc_ts timestamptz, object text,
  ref text, payload text, payload_fields jsonb, free_text text, written_lane text,
  unreadable     boolean NOT NULL,
  unreadable_why text,
  raw            text    NOT NULL,
  seq            bigint  NOT NULL REFERENCES journal (seq),
  mode           text    NOT NULL CHECK (mode IN ('files', 'primary')),
  CONSTRAINT log_lines_pos UNIQUE (file, ordinal) DEFERRABLE INITIALLY IMMEDIATE, -- shifts too
  CHECK (unreadable = (unreadable_why IS NOT NULL)),
  CHECK (mode = 'files' OR (NOT unreadable AND coalesce(workstation, '') <> ''   -- no CHECK on
         AND utc ~ '^[0-9]{4}-[0-9]{2}-[0-9]{2}T[0-9]{2}:[0-9]{2}:[0-9]{2}Z$'     -- verb: WORD
         AND utc_ts IS NOT NULL)));                                             -- (S6, 7(b))
CREATE INDEX log_lines_by_object ON log_lines (object) WHERE NOT unreadable;

CREATE TABLE object_holds (                    -- each lane's LAST line per object, file order
  object        text    NOT NULL,
  lane_key      text    COLLATE "C" NOT NULL,
  lane          text    NOT NULL,
  verb          text    NOT NULL,
  utc_ts        timestamptz,
  file          text    COLLATE "C" NOT NULL,
  ordinal       integer NOT NULL,
  superseded_by text,                          -- another lane whose last line on it is TAKEOVER
  spellings     text[],                        -- D19, a live claim's: as typed, canonical key, the writer's aliases
  exempt        boolean NOT NULL DEFAULT false, -- set only by an import, on a pair already at C
  seq           bigint  NOT NULL REFERENCES journal (seq),
  mode          text    NOT NULL CHECK (mode IN ('files', 'primary')),
  PRIMARY KEY (object, lane_key),
  CHECK (NOT exempt OR mode = 'files'));
CREATE UNIQUE INDEX one_live_claim ON object_holds (object)   -- Rule 1's net under claim's own check
  WHERE NOT exempt AND superseded_by IS NULL AND verb IN ('CLAIMED', 'TAKEOVER');

CREATE VIEW holds AS                           -- holders_of (code/lanes-index:1013-1036)
  SELECT object, lane_key, lane, verb, utc_ts, file, ordinal, seq, mode FROM object_holds
  WHERE superseded_by IS NULL AND object NOT LIKE 'lane:%'   -- lane-kind objects hold nothing
    AND verb IN ('CLAIMED', 'TAKEOVER', 'OPENED', 'LANDING', 'WITHDRAWN');

CREATE VIEW stale_claims AS                    -- claim_is_stale (code/lanes-edit.sh:4318) on the DB clock
  SELECT h.* FROM object_holds h
  WHERE h.verb = 'CLAIMED' AND h.superseded_by IS NULL
    AND clock_timestamp() - h.utc_ts > interval '4 hours'
    AND NOT EXISTS (SELECT 1 FROM log_lines o WHERE o.object = h.object AND o.verb = 'OPENED')
    AND NOT EXISTS (SELECT 1 FROM log_lines o WHERE lower(o.lane) = h.lane_key
                    AND o.file = h.file AND o.ordinal > h.ordinal AND o.verb = 'OPENED'
                    AND h.object = ANY (string_to_array(o.payload, ' ')));

CREATE TABLE register_lines (                  -- every non-row LANES.md line, parsed best effort
  line_id     bigint  PRIMARY KEY REFERENCES doc_lines (line_id) ON DELETE CASCADE
                      DEFERRABLE INITIALLY DEFERRED,
  kind        text,
  slot_role   text    CHECK (slot_role IN ('take', 'close')),  -- NULL decides nothing
  slot_kind   text    CHECK (slot_kind IN ('hold', 'landing')),
  lane text, repo text, repo_key text, pr integer, tip text, utc text, utc_ts timestamptz,
  session text,
  for_lane    text,                            -- F13: the lane whose LANDING a LANDING EXPIRED closes
  raw         text    NOT NULL,
  seq         bigint  NOT NULL REFERENCES journal (seq),
  mode        text    NOT NULL CHECK (mode IN ('files', 'primary')),
  workstation text,
  CHECK ((slot_role IS NULL) = (slot_kind IS NULL)),
  CHECK (slot_role IS NULL OR repo_key IS NOT NULL),
  CHECK (slot_kind IS DISTINCT FROM 'landing' OR pr IS NOT NULL),   -- RULE6_AWK pairs by PR
  CHECK ((for_lane IS NOT NULL) = (kind IS NOT DISTINCT FROM 'LANDING EXPIRED')),
  CHECK (for_lane IS NULL OR (slot_role = 'close' AND slot_kind = 'landing')),
  CHECK (mode = 'files' OR coalesce(workstation, '') <> ''));
CREATE INDEX register_lines_slots ON register_lines (repo_key, line_id) WHERE slot_role IS NOT NULL;
CREATE INDEX files_last_seq ON files (last_seq);                 -- the six columns that reference
CREATE INDEX doc_lines_written_seq ON doc_lines (written_seq);   -- journal (seq) and lead no index,
CREATE INDEX lanes_last_seq ON lanes (last_seq);                 -- so an import's deletes do not
CREATE INDEX log_lines_seq ON log_lines (seq);                   -- scan a table per journal row
CREATE INDEX object_holds_seq ON object_holds (seq);
CREATE INDEX register_lines_seq ON register_lines (seq);

CREATE VIEW main_slots AS                      -- A10, Rule 6: the earliest open take wins
  SELECT t.repo_key, t.line_id, t.kind, t.slot_kind, t.lane, t.pr, t.tip, t.utc_ts, t.raw,
         CASE WHEN t.slot_kind = 'landing'
              THEN clock_timestamp() - t.utc_ts > interval '30 minutes' END AS stale,
         rank() OVER (PARTITION BY t.repo_key ORDER BY t.line_id) AS place
  FROM register_lines t
  WHERE t.slot_role = 'take'
    AND NOT EXISTS (SELECT 1 FROM register_lines c
                    WHERE c.slot_role = 'close' AND c.slot_kind = t.slot_kind
                      AND c.repo_key = t.repo_key AND lower(coalesce(c.for_lane, c.lane)) = lower(t.lane)
                      AND c.line_id > t.line_id AND (t.slot_kind = 'hold' OR c.pr = t.pr));

CREATE FUNCTION render_file(p_path text) RETURNS bytea LANGUAGE sql STABLE
  SET search_path = lanes_register, pg_catalog, pg_temp AS $f$
  SELECT convert_to(coalesce(string_agg(d.text, E'\n' ORDER BY d.line_no), '')
           || CASE WHEN f.final_newline AND count(d.line_id) > 0 THEN E'\n' ELSE '' END, 'UTF8')
  FROM files f LEFT JOIN doc_lines d ON d.path = f.path
  WHERE f.path = p_path GROUP BY f.final_newline $f$;
-- commit_write, switch_to_files, record_marker, find_write and record_mirror_state follow in §1.3.1, in this transaction;
-- its block ends migration 1: schema_version (1, 1, 1), register_head (-1), an empty
-- mirror_state row, the switch at 'files' (set_seq -1), the migrations row, then COMMIT.
```

**What each table serves.** "Tests" are the agreement tests (`proposal.md:846-921`), the roles test (`:692-693`), the bootstrap's (`:623-627`) and S1's mode test (`:185-188`).

| Table or view | Requirements | Tests |
|---|---|---|
| `schema_version`, `migrations` | the version check (Q3), every S | roles, bootstrap twice; 10 (the service checks per request) |
| `register_switch` | S1's database half; S5 (`lanes/STORE` mirrors it, through D24's two operator entries); the rollback's state and the generation (F1, F10, F18) | 3, 8, 13 |
| `register_head` | S1's one transaction; S2's gapless order; S6's commit order | 2, 4, 8, 12 |
| `mirror_state` | S2's divergence record and a failed pass (D16) | 5, 6 |
| `journal` | S2's journal; S5's seq 0, the cutover's seq and rollback's last; S1's in-doubt write (`write_id`); S6's clock | 1, 4, 5, 6, 7, 8, 12 |
| `journal_edits`, `journal_files` | S2's replay and per-write byte proof; S5's rollback proof | 1, 5, 6, 8 |
| `files`, `doc_lines` | S2's truth; S5's byte-identical import; S1's mode last written | 1, 7, 11; S1's mode test |
| `lanes` | S3 (`binding`, `next-free`, `who`); S4's copy of `owner`; Amendment 15's one row | 11; S3's table |
| `log_lines`, `object_holds`, `holds`, `stale_claims`, `one_live_claim` | S3 (`who`, `history`); S6's Rule 1; 7(b) | 4, 7, 11, 12; ADV-C10's alias race (`clarifications.md:209-216`) |
| `register_lines`, `main_slots` | S6's `HOLD` and `LANDING` order, P9's report | 4, 11 |
| `render_file` | S2 and S5's proof; `reconcile` (`proposal.md:395-398`) | 1, 7 |

**Where this departs from R3a's sketch, and why.**
- `write_id`, UNIQUE in the journal: ADV-C3 (`proposal.md:253-267`; `clarifications.md:253-266`).
- Seq 0 is the import's baseline (the mirror's seq `m` after a loss, §1.3), the cutover is the next seq, writes follow, and rollback's removal of `lanes/STORE` is the last (`proposal.md:1259-1264`). `lanes/STORE` is not a rendered path. An entry that touches only it is an operator entry (`journal.operator`), and there are exactly two (D24): the cutover's, at seq 1 or at `m+1` after a loss, and the rollback's last seq. Both marker commits carry `Register-Seq` (`r3b/REPORT.md:213-219`), the renderer never renders either (S2, `proposal.md:307-309`), and the command that writes the entry pushes its commit with the renderer's identity (D23) and confirms it by Amendment 23's S4 read-back (F2; `operations.md` §2.1). `stray-import` and `stray-revert` are ADV-C2's two ways out of a divergence (`proposal.md:380-389`). Each names the stray commit in `git_commit` and clears `mirror_state.diverged_sha`, and a revert is an entry with no line edits, as D16 has it. The frame's journal entry also lists `mode` (S2); here the mode is a column of the rows an entry writes (`written_mode`, `mode`), not of the entry itself.
- The journal carries the commit's `author`, so every renderer makes the same commit with "the journal's fixed author, committer and dates" (`proposal.md:327-329`), and `utc` in whole seconds (`:665-674`; `r3b/REPORT.md:85-91`).
- `base_seq` references `journal (seq)` and is UNIQUE, so the journal is one chain with no gap.
- `object_holds.exempt` replaces R3a's `post_cutover` in `one_live_claim`: a new claim over a live imported claim is caught too, and only a pair already at C is exempt. At `527f3c7` there is none, since #198's pair is `OPENED` and `CLAIMED` (`r3a/REPORT.md:79-80`). The index covers `CLAIMED` and `TAKEOVER` only, as R3a's inference about `OPENED` requires (`r3a/REPORT.md:409`); `claim` refuses over any open holder (`code/lanes-edit.sh:16527`) and takes over only a stale `CLAIMED` (`:16578`).
- **Order-blind supersession, kept at parity.** Under #160's rule, which ignores order, a new `CLAIMED` after another lane's `TAKEOVER` on the same object is born superseded, so neither `one_live_claim` nor D19 refuses it; `claim`'s own check does, as in files mode. A stricter SQL rule would answer differently from the files and break the agreement tests on imported history, so the schema keeps parity.
- `register_lines.slot_kind` makes each close match its own kind, as Q17 (B) has it: `HOLD RELEASED`, `HOLD WITHDRAWN` and `HOLD EXPIRED` close a `HOLD`; `LANDED` closes its own lane's `LANDING`, and, if Brett takes F13's release, `LANDING EXPIRED`, on a person's word, closes another lane's `LANDING` once Rule 6's 30 minutes have passed (`for_lane` names that lane); `(refresh)`, `(run id)`, `QUEUED-HOLD` and `LANDING WITHDRAWN` take and close nothing (`proposal.md:1736-1743`).
- `doc_lines.kind` is dropped: it decided nothing, and the structured layer types each line. R3a's `exists` is `present`, because `EXISTS` is an SQL keyword.
- The schema's owner is a role that cannot log in, and writers change it only through `commit_write` (§1.4), not R3a's `AUTHORIZATION lanes_register_writer` (`r3a/REPORT.md:172`).

### 1.3 The commit transaction: what the schema holds of S1 and Q7 (A″)

Under (A″) each attempt is two `psql` calls, made by `lanes-index register snapshot` and `lanes-index register commit` (D2: `lanes-index` is the one database client, so `lanes-edit.sh` runs no `psql`; `proposal.md:1458-1471`; R3a's steps, `r3a/REPORT.md:159-163`):

1. **The snapshot read:** one `BEGIN ISOLATION LEVEL REPEATABLE READ READ ONLY` transaction, as #160 reads (`code/lanes-index:1331-1344`). It reads `schema_version`, `register_switch` (with its `generation`, which the commit call sends back, F10), `register_head` (the base `n`), the database clock in whole seconds (`r3b/REPORT.md:220`), `files`, the journal's edits since the writer's cached seq (or every line, when the cache fails its `files.sha256` check), what the verb decides on (`holds`, `stale_claims`, `main_slots` for its repository) and, when it retries a write in doubt, `journal` by `write_id`. A failure here sent nothing that writes: 69.
2. **The verb** runs unchanged on the scratch copy, outside SQL, and its lines carry the clock read in step 1.
3. **The commit:** `BEGIN ISOLATION LEVEL SERIALIZABLE; SELECT lanes_register.commit_write(n, write_id, REGISTER_SCHEMA_VERSION, generation, entry); COMMIT;` under `ON_ERROR_STOP` and `VERBOSITY=sqlstate`, so the writer can tell a 40001 from every other exit 3 (`proposal.md:1442-1443`; `clarifications.md:250-252`). The entry is one `jsonb` literal: the journal fields, the edits, `journal_files`' digests, and the structured rows, which name lines by path and `line_no`.

`commit_write` and `switch_to_files` are owned by `lanes_register_owner`, `SECURITY DEFINER`, with `SET search_path = lanes_register, pg_catalog, pg_temp`. Neither holds a verb's rule: R3a's generic `apply_edits` (`r3a/REPORT.md:285`) becomes step 6. The head comes before the journal (`proposal.md:235-239`), and the version and switch are read under the head's lock. Steps 1 to 5, as §1.3.1 has them; an import's reset, between steps 3 and 4, sets `v_head` to -1, so steps 4 and 5 read `v_head`, not `p_base`:

```sql
SELECT seq INTO v_head FROM register_head WHERE id = 1 FOR UPDATE;            -- 1. the lock
SELECT * INTO v_old FROM journal WHERE write_id = p_write_id;                 -- a write in doubt
IF FOUND THEN                                                                 -- that committed
  IF v_old.lane IS DISTINCT FROM p_entry -> 'journal' ->> 'lane'
     OR v_old.verb IS DISTINCT FROM p_entry -> 'journal' ->> 'verb'
     OR v_old.args_sha256 IS DISTINCT FROM p_entry -> 'journal' ->> 'args_sha256' THEN
    RAISE EXCEPTION 'write % is seq %, another write''s (F10)', p_write_id, v_old.seq
      USING ERRCODE = 'LR007'; END IF;
  RAISE EXCEPTION 'write % is in the journal at seq %', p_write_id, v_old.seq USING ERRCODE = 'LR006'; END IF;
IF v_head IS DISTINCT FROM p_base THEN
  RAISE EXCEPTION 'register head is %, not %', v_head, p_base USING ERRCODE = 'LR001'; END IF;
SELECT * INTO v_ver FROM schema_version WHERE id = 1;                         -- 2. the version
IF p_client > v_ver.version OR p_client < v_ver.writers_from THEN
  RAISE EXCEPTION 'register schema % (writers from %); this client is %', v_ver.version,
    v_ver.writers_from, p_client USING ERRCODE = 'LR003'; END IF;
SELECT * INTO v_sw FROM register_switch WHERE id = 1;                         -- 3. the switch
IF NOT (CASE WHEN v_kind = 'cutover' AND v_after_loss AND v_sw.mode = 'primary'
             THEN v_sw.marker_state = 'present'             -- a restore's after-loss cutover
             ELSE v_sw.mode = v_needs AND v_sw.marker_state =
                    CASE v_needs WHEN 'primary' THEN 'present' ELSE 'absent' END
        END) THEN
  RAISE EXCEPTION 'register switch is % with the marker %', v_sw.mode, v_sw.marker_state
    USING ERRCODE = 'LR002'; END IF;
IF p_generation IS DISTINCT FROM v_sw.generation THEN       -- F10, F18: an id from another database
  RAISE EXCEPTION 'write % belongs to register generation %, not %', p_write_id, p_generation,
    v_sw.generation USING ERRCODE = 'LR007'; END IF;
UPDATE register_head SET seq = CASE WHEN v_head = -1                          -- 4. advance; the empty journal's
    THEN coalesce((p_entry ->> 'root_seq')::bigint, 0) ELSE seq + 1 END       -- one entry is the root, at 0 or `m`
  WHERE id = 1 RETURNING seq INTO v_seq;
INSERT INTO journal (seq, base_seq, write_id, kind, verb, lane, workstation, author, message,
                     git_commit, git_tree, repos_blob, args_sha256)            -- 5. the entry
SELECT v_seq, nullif(v_head, -1), p_write_id, e.* FROM jsonb_to_record(p_entry -> 'journal')
  AS e(kind text, verb text, lane text, workstation text, author text, message text,
       git_commit text, git_tree text, repos_blob text, args_sha256 text);
```

Steps 6 to 8 are §1.3.1's SQL:

6. **The edits:** `journal_edits` as sent, then applied in `ord` order: `create`, `append`, `replace` and `delete` (each checking `old_sha256`, the sha256 of the line's UTF-8 text, at its `line_no`), `rename` and `remove`. A delete shifts the later lines, and their `log_lines` and `object_holds` ordinals, in one `UPDATE` each, which `doc_lines_pos` and `log_lines_pos` check at the statement's end. An edit that does not apply raises LR004. So does an operator entry that edits anything but `lanes/STORE`, or nothing, and any other entry that edits `lanes/STORE` (F2, D24).
7. **The structured rows:** each row's `raw` must equal its line's text, or LR004; they are upserted by key, each given its line's `line_id`. `superseded_by` is recomputed for every lane on each object touched (#160's rule, `code/lanes-index:1013-1036`), the stored rows first, so that no transient pair of live claims trips `one_live_claim`, whose 23505 is raised again as LR005. Two rules of the register itself raise LR005 on a write: a claim whose `spellings` overlap another lane's live claim's (D19), and a `HOLD` or `LANDING` line over another lane's open taker in `main_slots` for its repository (D18).
8. **The proof:** every edited path has a row in the entry's `files`, and each present one's `encode(sha256(render_file(path)), 'hex')` must not be distinct from it, or LR004; then `files.sha256`, `final_newline` and `last_seq` move. The deferred foreign keys check at COMMIT.

All of it is one transaction, so a failure at any step leaves the journal, the line store and the head row as they were. P11's real-engine case (`proposal.md:285-290`) can inject one with no test-only code: an entry whose last structured row breaks a CHECK.

**The kinds.** A rollback starts with `commit_write` and a `rollback` entry (F1, D24). `switch_to_files(p_client, p_cutover_seq, p_lane, p_ws)` is now only the cutover's abort (the adversary seat, `proposal.md:1776-1779`; F2): it takes the same lock, refuses with LR002 unless the switch is `primary` with the marker `adding` at `p_cutover_seq` and the head is there, deletes that entry and its rows, sets the head back to `p_cutover_seq - 1`, and sets the switch to `files` with the marker `absent`. The caller runs it only after a read-back proves the marker absent from origin. `record_marker(p_client, p_seq, p_commit, p_ws)` records S4's read-back of an operator entry's push (F12): `adding` becomes `present`, `removing` becomes `absent`, and `marker_commit` the confirmed commit. `record_mirror_state(p_ws, p_diverged_sha, p_error)`, `SECURITY DEFINER` like the others, is the renderer's one write: it records a divergence or a failed pass in `mirror_state` (D16), keeping the first divergence found and never clearing one, and `stray-import` and `stray-revert` clear the divergence through `commit_write`.

**The switch's states (F1).** `mode` stays the one write gate, and `marker_state` says where the marker stands, so a rollback's state is never taken for "never cut over":

| `mode`, `marker_state` | Means | Set by |
|---|---|---|
| `files`, `absent` | before any cutover; after an aborted one; after a confirmed rollback | the bootstrap; `switch_to_files`; `record_marker` after a rollback |
| `primary`, `adding` | the cutover's entry written, its push not yet confirmed: no write commits | `commit_write`, kind `cutover` |
| `primary`, `present` | the cutover confirmed: writes commit | `record_marker` after a cutover |
| `files`, `removing` | the rollback's entry written, the marker's removal not yet confirmed | `commit_write`, kind `rollback` |

**`set_seq`, lane 3's open point 23:** after a cutover it is the cutover's own seq, 1, or `m+1` after a loss. After a rollback it is the rollback's own seq, `k+1`, and after an abort the abort's base, the import's seq.

| `kind` | The switch must be (mode, marker), and what it leaves | What else the function does |
|---|---|---|
| `write` | `primary`, `present` | the verb's edits; `written_mode` and `mode` are `primary` |
| `import` | `files`, `absent` | With only its root in the journal, it deletes every journal and data row (head to -1) and takes seq 0 (under `--after-loss`, D22 and `operations.md` §2.3, the baseline seq `m` that the entry names in `root_seq`: the head moves from -1 to `m`, the chain's one root sits at `m`, and a `root_seq` on a journal that is not empty is LR004), so each re-import before a confirmed cutover replaces the root (`proposal.md:1259-1261`; `switch_to_files` deletes an aborted cutover's entry); otherwise it clears the data tables and takes the next seq, so a seq never repeats in the mirror's history and a later cutover never reuses a `Register-Seq` that `main` carries. Every line is a `create` or `append` edit, so the journal alone renders any seq from the import on. It never loads `lanes/STORE`, which is not a rendered path (F2). Sets `exempt`. |
| `cutover` (operator, D24) | `files`, `absent`; leaves `primary`, `adding` | base must be the latest import's seq; sets the switch's fields from it (`import_seq`, `import_commit`, `import_tree`) and `set_seq` to its own seq, 1 or `m+1` after a loss; creates `lanes/STORE`'s lines, D21's fields: `store=primary`, `database=` the label, `import=` the commit, `lanes-tree=` the tree id and `utc=`. Its after-loss form, `lanes-index cutover --import <M> --after-loss` (`after_loss` in the entry; lane 3's F18 ruling), is this same entry at `m+1`, not a third operator entry; it also sets `database_label` from the entry and a new `generation`. After a restore, whose switch reads `primary`, `present`, it starts from there, with the head as its base, and replaces `lanes/STORE`'s lines whole (`operations.md` §2.3; D24 words the restore form, as lane 3 ruled) |
| `stray-import` | `primary`, `present` | the stray commit's edits as one entry, `written_mode` `files` |
| `stray-revert` | `primary`, `present` | no line edits; `journal_files` names the paths the renderer restores. A rollback refuses to start while a divergence is recorded (F1), so a revert is never needed at `files` |
| `rollback` (operator, D24) | `primary`, `present`; leaves `files`, `removing` | removes `lanes/STORE`; the journal's last seq, written in the transaction that sets the switch to `files`, with `set_seq` its own seq (F1) |

**The signals a writer gets.** The retry loop is the writer's; the schema fixes what each signal means. The busy code is 75 (D7), sysexits' `EX_TEMPFAIL`, the product seat's suggestion (`clarifications.md:360-373`; `proposal.md:1706-1708`), unless Brett says "(A″) with 69", which decides D7's number. The deadline is 30 s (D7), lane 2's example (`proposal.md:1470-1471`), and needs no pause between tries: with no cap, the probes' k-th writer committed on its k-th attempt (`clarifications.md:323-327`). Tests 4 and 9 confirm both.

| Signal | Means | The writer |
|---|---|---|
| 40001, 40P01, LR001 | a rival committed after the snapshot, or the head moved | re-reads and re-runs the verb until the deadline, then busy |
| 55P03 | the head row's lock, not granted within `lock_timeout` | as 40001 until the deadline; a write any of whose commit calls was in doubt then makes F3's one lookup (below), and exits 75 only where that finds the write absent with the head row free |
| LR006 | this write is already in the journal: step 1 looks its `write_id` up under the head's lock | reads `journal` by `write_id`, exits 0 |
| LR005 | a rule of the register: an object is held (`one_live_claim`'s 23505, raised again, or D19), or `main` is held (D18) | exits 2 with the holder or the earlier line, writing nothing (Q12 (a); S1's outcomes) |
| LR002 | the switch, or its marker, is not what the kind needs (the kinds table) | 69, with S1's text (`proposal.md:169-171`) |
| LR003 | the client is outside the schema's window (§1.5) | 69 |
| LR007 | the write id belongs to another write: minted against another generation of the register (F10, F18), or in the journal for another lane, verb or arguments (F10) | exits 2, naming the id and what differs: check the mirror for the write before running it again without `LANES_WRITE_ID`; nothing written |
| LR004 | the edits do not apply, or the proof differs | reloads every line once and retries; then 69 |
| any other SQLSTATE | the database refused the transaction | 69, naming the store and the error (`proposal.md:213-217`) |
| `psql` exit 2, or 124 from the client timer, on the commit call | the outcome is unknown | in doubt: reconnects, reads `journal` by `write_id` (`proposal.md:253-267`), and retries under the same id (D8). At the deadline it makes one lookup (F3), `find_write` in a transaction it rolls back, within the 10 s reconnect window: found is 0; absent, with the head row's lock granted, is 75; otherwise, 55P03 or no answer, it exits 3 naming the id and keeps its pending record |

Under `VERBOSITY=sqlstate` psql prints the code alone, so no signal rests on a constraint's name. With the head updated before the journal row, a lost race is never a 23505 on `journal_pkey`; one would be a bug, and is 69. `LANES_NO_FETCH=1` cannot weaken the switch check, because it is read inside the transaction, not fetched (`proposal.md:571-575`). The in-doubt report is exit 3, naming the write id, and a rerun reuses the id (D8; `proposal.md:1725` left the code open).

**Bounds, set when connecting through `options=`** (`clarifications.md:296-302`), are D9's, in the one table under "Roles and credentials" ("The timeouts (D9)"), which now carries this part's `import` row and gives `reconcile` the renderer's values (lane 3's ruling, #165 6578020193). Only the guard's are measured; the others are the design's, for tests 4 and 9 to confirm, and R1's 14.7 ms per call (`proposal.md:1219-1220`) leaves them wide.

### 1.3.1 `commit_write` in full

Migration 1's functions, run in its transaction after §1.2's block, as the owner, and its rows. The entry's shape is the comment at the function's head. `record_mirror_state` keeps the first divergence found, as D16 has it. Lane 1's real-engine runs on PostgreSQL 16.15 ran this block as printed, the second with the frame review's changes.

```sql
CREATE FUNCTION commit_write(p_base bigint, p_write_id uuid, p_client integer, p_generation uuid,
                             p_entry jsonb)
  RETURNS bigint LANGUAGE plpgsql SECURITY DEFINER
  SET search_path = lanes_register, pg_catalog, pg_temp AS $f$
-- The entry is one jsonb object; an absent key is an empty list.
--   journal        {kind, verb, lane, workstation, author, message, git_commit, git_tree,
--                   repos_blob, args_sha256}: step 5's columns; args_sha256 (F10) is the
--                   pending record's digest of the lane, verb and arguments
--   root_seq       an import's root on an empty journal (--after-loss's m); else absent
--   switch         {after_loss, database_label}: a cutover's after-loss form (F18)
--   edits          [{ord, op, path, line_no, old_sha256, new_text, to_path}], journal_edits'
--                  columns; old_sha256 is the sha256 of the line's UTF-8 text, without its LF
--   files          [{path, present, sha256, final_newline}]: every path an edit touches, as
--                  it stands after the write (a rename's old path is present = false)
--   lanes          [{path, line_no, raw, lane_key, lane, aliases, home, workstation,
--                    started_utc, handoff_path, state_phrase, objects_cell, session_ids,
--                    last_session, archived, owner}]
--   log_lines      [{path, line_no, raw, lane, verb, session, workstation, utc, utc_ts,
--                    object, ref, payload, payload_fields, free_text, written_lane,
--                    unreadable, unreadable_why}]
--   register_lines [{path, line_no, raw, kind, slot_role, slot_kind, lane, repo, repo_key,
--                    pr, tip, utc, utc_ts, session, workstation}]
--   object_holds   [{object, lane_key, lane, verb, utc_ts, file, ordinal, spellings, exempt}]:
--                  each lane's last line per object; superseded_by is computed here
-- A structured row names its line by path and line_no, and its raw is that line's text.
-- Step 7 applies lanes, log_lines, register_lines, then object_holds, in that order.
DECLARE
  v_head  bigint;
  v_ver   schema_version;
  v_sw    register_switch;
  v_old   journal;                              -- F10: a write id already in the journal
  v_needs text;
  v_after_loss boolean := coalesce((p_entry -> 'switch' ->> 'after_loss')::boolean, false);
  v_seq   bigint;
  v_kind  text := p_entry -> 'journal' ->> 'kind';
  v_ws    text := p_entry -> 'journal' ->> 'workstation';
  v_wmode text;                                 -- written_mode and mode of the rows written
  v_holds jsonb := coalesce(p_entry -> 'object_holds', '[]');
  v_imp   journal;
  v_line  doc_lines;
  ed      journal_edits;
  r       record;
  v_n     integer;
  v_sha   text;
  v_con   text;
  v_det   text;
BEGIN
  IF jsonb_typeof(p_entry -> 'journal') IS DISTINCT FROM 'object' THEN    -- the entry's shape
    RAISE EXCEPTION 'the entry has no journal object' USING ERRCODE = 'LR004'; END IF;
  IF EXISTS (SELECT 1 FROM jsonb_each(p_entry -> 'journal') k
             WHERE jsonb_typeof(k.value) NOT IN ('string', 'null'))
     OR EXISTS (SELECT 1 FROM unnest(ARRAY['edits', 'files', 'lanes', 'log_lines',
                                           'register_lines', 'object_holds']) k
                WHERE p_entry ? k AND jsonb_typeof(p_entry -> k) <> 'array') THEN
    RAISE EXCEPTION 'the entry is not well formed' USING ERRCODE = 'LR004'; END IF;
  v_needs := CASE v_kind WHEN 'write' THEN 'primary' WHEN 'stray-import' THEN 'primary'
                         WHEN 'stray-revert' THEN 'primary' WHEN 'rollback' THEN 'primary'
                         WHEN 'import' THEN 'files' WHEN 'cutover' THEN 'files' END;
  IF v_needs IS NULL THEN
    RAISE EXCEPTION 'unknown kind %', v_kind USING ERRCODE = 'LR004'; END IF;
  v_wmode := CASE WHEN v_kind IN ('write', 'cutover') THEN 'primary' ELSE 'files' END;

SELECT seq INTO v_head FROM register_head WHERE id = 1 FOR UPDATE;            -- 1. the lock
SELECT * INTO v_old FROM journal WHERE write_id = p_write_id;                 -- a write in doubt
IF FOUND THEN                                                                 -- that committed
  IF v_old.lane IS DISTINCT FROM p_entry -> 'journal' ->> 'lane'
     OR v_old.verb IS DISTINCT FROM p_entry -> 'journal' ->> 'verb'
     OR v_old.args_sha256 IS DISTINCT FROM p_entry -> 'journal' ->> 'args_sha256' THEN
    RAISE EXCEPTION 'write % is seq %, another write''s (F10)', p_write_id, v_old.seq
      USING ERRCODE = 'LR007'; END IF;
  RAISE EXCEPTION 'write % is in the journal at seq %', p_write_id, v_old.seq USING ERRCODE = 'LR006'; END IF;
IF v_head IS DISTINCT FROM p_base THEN
  RAISE EXCEPTION 'register head is %, not %', v_head, p_base USING ERRCODE = 'LR001'; END IF;
SELECT * INTO v_ver FROM schema_version WHERE id = 1;                         -- 2. the version
IF p_client > v_ver.version OR p_client < v_ver.writers_from THEN
  RAISE EXCEPTION 'register schema % (writers from %); this client is %', v_ver.version,
    v_ver.writers_from, p_client USING ERRCODE = 'LR003'; END IF;
SELECT * INTO v_sw FROM register_switch WHERE id = 1;                         -- 3. the switch
IF NOT (CASE WHEN v_kind = 'cutover' AND v_after_loss AND v_sw.mode = 'primary'
             THEN v_sw.marker_state = 'present'             -- a restore's after-loss cutover
             ELSE v_sw.mode = v_needs AND v_sw.marker_state =
                    CASE v_needs WHEN 'primary' THEN 'present' ELSE 'absent' END
        END) THEN
  RAISE EXCEPTION 'register switch is % with the marker %', v_sw.mode, v_sw.marker_state
    USING ERRCODE = 'LR002'; END IF;
IF p_generation IS DISTINCT FROM v_sw.generation THEN       -- F10, F18: an id from another database
  RAISE EXCEPTION 'write % belongs to register generation %, not %', p_write_id, p_generation,
    v_sw.generation USING ERRCODE = 'LR007'; END IF;

  -- 3a. The kinds table's rules that come before the seq is taken.
  IF v_kind = 'import' THEN               -- no 'write' entry: start the journal again (head -1)
    DELETE FROM register_lines; DELETE FROM log_lines; DELETE FROM lanes;
    DELETE FROM object_holds; DELETE FROM doc_lines; DELETE FROM files;
    IF NOT EXISTS (SELECT 1 FROM journal WHERE base_seq IS NOT NULL) THEN   -- only its root; after a rollback, continue
      DELETE FROM journal_files; DELETE FROM journal_edits; DELETE FROM journal;
      UPDATE register_head SET seq = -1 WHERE id = 1;
      v_head := -1;
    END IF;
  END IF;
  IF p_entry ? 'root_seq' AND (v_kind <> 'import' OR v_head <> -1) THEN
    RAISE EXCEPTION 'root_seq % on a journal that is not empty', p_entry ->> 'root_seq'
      USING ERRCODE = 'LR004'; END IF;
  IF v_kind = 'cutover' AND NOT (v_after_loss AND v_sw.mode = 'primary') THEN
    SELECT * INTO v_imp FROM journal WHERE kind = 'import' ORDER BY seq DESC LIMIT 1;
    IF v_imp.seq IS DISTINCT FROM p_base THEN
      RAISE EXCEPTION 'cutover base %, but the latest import is %', p_base, v_imp.seq
        USING ERRCODE = 'LR004'; END IF;
  END IF;

UPDATE register_head SET seq = CASE WHEN v_head = -1                          -- 4. advance; the empty journal's
    THEN coalesce((p_entry ->> 'root_seq')::bigint, 0) ELSE seq + 1 END       -- one entry is the root, at 0 or `m`
  WHERE id = 1 RETURNING seq INTO v_seq;
INSERT INTO journal (seq, base_seq, write_id, kind, verb, lane, workstation, author, message,
                     git_commit, git_tree, repos_blob, args_sha256)            -- 5. the entry
SELECT v_seq, nullif(v_head, -1), p_write_id, e.* FROM jsonb_to_record(p_entry -> 'journal')
  AS e(kind text, verb text, lane text, workstation text, author text, message text,
       git_commit text, git_tree text, repos_blob text, args_sha256 text);

  -- 6. The edits: journal_edits as sent, then applied in ord order.
  INSERT INTO journal_edits (seq, ord, op, path, line_no, old_sha256, new_text, to_path)
  SELECT v_seq, x.ord, x.op, x.path, x.line_no, x.old_sha256, x.new_text, x.to_path
  FROM jsonb_to_recordset(coalesce(p_entry -> 'edits', '[]')) AS x(ord integer, op text,
       path text, line_no integer, old_sha256 text, new_text text, to_path text);
  IF v_kind IN ('cutover', 'rollback')                     -- D24, F2: an operator entry has edits
     AND NOT EXISTS (SELECT 1 FROM journal_edits WHERE seq = v_seq) THEN
    RAISE EXCEPTION 'a % entry with no edit', v_kind USING ERRCODE = 'LR004'; END IF;
  FOR ed IN SELECT * FROM journal_edits WHERE seq = v_seq ORDER BY ord LOOP
    IF (ed.path = 'lanes/STORE') <> (v_kind IN ('cutover', 'rollback')) THEN  -- and edits
      RAISE EXCEPTION 'edit %: a % edits lanes/STORE alone, or never', ed.ord, v_kind  -- STORE alone
        USING ERRCODE = 'LR004'; END IF;
    IF (v_kind = 'import' AND ed.op NOT IN ('create', 'append')) OR v_kind = 'stray-revert' THEN
      RAISE EXCEPTION 'edit %: a % may not %', ed.ord, v_kind, ed.op USING ERRCODE = 'LR004';
    END IF;
    IF (ed.op = 'create') = EXISTS (SELECT 1 FROM files WHERE path = ed.path) THEN
      RAISE EXCEPTION 'edit %: % % %', ed.ord, ed.op, ed.path,
        CASE WHEN ed.op = 'create' THEN 'exists' ELSE 'does not exist' END
        USING ERRCODE = 'LR004'; END IF;
    CASE ed.op
    WHEN 'create' THEN
      INSERT INTO files (path, final_newline, sha256, last_seq)     -- step 8 sets the last two
      VALUES (ed.path, true, encode(sha256(''::bytea), 'hex'), v_seq);
    WHEN 'append' THEN
      SELECT coalesce(max(line_no), 0) + 1 INTO v_n FROM doc_lines WHERE path = ed.path;
      IF ed.line_no IS DISTINCT FROM NULL AND ed.line_no <> v_n THEN
        RAISE EXCEPTION 'edit %: append at %, but % has % lines', ed.ord, ed.line_no, ed.path,
          v_n - 1 USING ERRCODE = 'LR004'; END IF;
      INSERT INTO doc_lines (path, line_no, text, written_seq, written_mode, written_ws)
      VALUES (ed.path, v_n, ed.new_text, v_seq, v_wmode, v_ws);
    WHEN 'replace', 'delete' THEN
      SELECT * INTO v_line FROM doc_lines WHERE path = ed.path AND line_no = ed.line_no;
      IF NOT FOUND OR encode(sha256(convert_to(v_line.text, 'UTF8')), 'hex') <> ed.old_sha256 THEN
        RAISE EXCEPTION 'edit %: % line % is not %', ed.ord, ed.path, ed.line_no, ed.old_sha256
          USING ERRCODE = 'LR004'; END IF;
      IF ed.op = 'replace' THEN
        UPDATE doc_lines SET text = ed.new_text, written_seq = v_seq, written_mode = v_wmode,
                             written_ws = v_ws
        WHERE line_id = v_line.line_id;
      ELSE                                      -- doc_lines_pos checks at the statement's end
        DELETE FROM doc_lines WHERE line_id = v_line.line_id;    -- its log or register row cascades
        UPDATE doc_lines SET line_no = line_no - 1 WHERE path = ed.path AND line_no > ed.line_no;
        UPDATE log_lines SET ordinal = ordinal - 1 WHERE file = ed.path AND ordinal > ed.line_no;
        UPDATE object_holds SET ordinal = ordinal - 1 WHERE file = ed.path AND ordinal > ed.line_no;
      END IF;
    WHEN 'rename' THEN
      IF EXISTS (SELECT 1 FROM files WHERE path = ed.to_path) THEN
        RAISE EXCEPTION 'edit %: % exists', ed.ord, ed.to_path USING ERRCODE = 'LR004'; END IF;
      UPDATE files SET path = ed.to_path WHERE path = ed.path;   -- cascades to doc_lines
      UPDATE log_lines SET file = ed.to_path WHERE file = ed.path;
      UPDATE object_holds SET file = ed.to_path WHERE file = ed.path;
    WHEN 'remove' THEN
      DELETE FROM doc_lines WHERE path = ed.path;
      DELETE FROM files WHERE path = ed.path;
    END CASE;
  END LOOP;

  -- 7. The structured rows, upserted by key, each given its line's line_id.
  FOR r IN
    SELECT x.t, x.path, x.line_no, x.raw, d.line_id, d.text FROM (
      SELECT 'lanes' AS t, path, line_no, raw FROM jsonb_to_recordset(coalesce(p_entry -> 'lanes',
        '[]')) AS y(path text, line_no integer, raw text)
      UNION ALL SELECT 'log_lines', path, line_no, raw FROM jsonb_to_recordset(coalesce(
        p_entry -> 'log_lines', '[]')) AS y(path text, line_no integer, raw text)
      UNION ALL SELECT 'register_lines', path, line_no, raw FROM jsonb_to_recordset(coalesce(
        p_entry -> 'register_lines', '[]')) AS y(path text, line_no integer, raw text)) x
    LEFT JOIN doc_lines d ON d.path = x.path AND d.line_no = x.line_no
  LOOP
    IF r.line_id IS NULL OR r.text IS DISTINCT FROM r.raw THEN
      RAISE EXCEPTION '% row at % line %: no such line, or raw is not its text', r.t, r.path,
        r.line_no
        USING ERRCODE = 'LR004'; END IF;
  END LOOP;
  INSERT INTO lanes AS o (lane_key, lane, row_line_id, aliases, home, workstation, started_utc,
                          handoff_path, state_phrase, objects_cell, session_ids, last_session,
                          archived, owner, last_seq, last_mode)
  SELECT x.lane_key, x.lane, d.line_id, x.aliases, x.home, x.workstation, x.started_utc,
         x.handoff_path, x.state_phrase, x.objects_cell, x.session_ids, x.last_session,
         x.archived, x.owner, v_seq, v_wmode
  FROM jsonb_to_recordset(coalesce(p_entry -> 'lanes', '[]')) AS x(path text, line_no integer,
       lane_key text, lane text, aliases jsonb, home text, workstation text, started_utc text,
       handoff_path text, state_phrase text, objects_cell text, session_ids jsonb,
       last_session text, archived boolean, owner text)
  JOIN doc_lines d ON d.path = x.path AND d.line_no = x.line_no
  ON CONFLICT (lane_key) DO UPDATE SET lane = EXCLUDED.lane, row_line_id = EXCLUDED.row_line_id,
    aliases = EXCLUDED.aliases, home = EXCLUDED.home, workstation = EXCLUDED.workstation,
    started_utc = EXCLUDED.started_utc, handoff_path = EXCLUDED.handoff_path,
    state_phrase = EXCLUDED.state_phrase, objects_cell = EXCLUDED.objects_cell,
    session_ids = EXCLUDED.session_ids, last_session = EXCLUDED.last_session,
    archived = EXCLUDED.archived, owner = EXCLUDED.owner, last_seq = EXCLUDED.last_seq,
    last_mode = EXCLUDED.last_mode;
  INSERT INTO log_lines (line_id, file, ordinal, lane, verb, session, workstation, utc, utc_ts,
                         object, ref, payload, payload_fields, free_text, written_lane,
                         unreadable, unreadable_why, raw, seq, mode)
  SELECT d.line_id, x.path, x.line_no, x.lane, x.verb, x.session, x.workstation, x.utc,
         x.utc_ts, x.object, x.ref, x.payload, x.payload_fields, x.free_text, x.written_lane,
         x.unreadable, x.unreadable_why, x.raw, v_seq, v_wmode
  FROM jsonb_to_recordset(coalesce(p_entry -> 'log_lines', '[]')) AS x(path text,
       line_no integer, raw text, lane text, verb text, session text, workstation text,
       utc text, utc_ts timestamptz, object text, ref text, payload text, payload_fields jsonb,
       free_text text, written_lane text, unreadable boolean, unreadable_why text)
  JOIN doc_lines d ON d.path = x.path AND d.line_no = x.line_no
  ON CONFLICT (line_id) DO UPDATE SET file = EXCLUDED.file, ordinal = EXCLUDED.ordinal,
    lane = EXCLUDED.lane, verb = EXCLUDED.verb, session = EXCLUDED.session,
    workstation = EXCLUDED.workstation, utc = EXCLUDED.utc, utc_ts = EXCLUDED.utc_ts,
    object = EXCLUDED.object, ref = EXCLUDED.ref, payload = EXCLUDED.payload,
    payload_fields = EXCLUDED.payload_fields, free_text = EXCLUDED.free_text,
    written_lane = EXCLUDED.written_lane, unreadable = EXCLUDED.unreadable,
    unreadable_why = EXCLUDED.unreadable_why, raw = EXCLUDED.raw, seq = EXCLUDED.seq,
    mode = EXCLUDED.mode;
  INSERT INTO register_lines (line_id, kind, slot_role, slot_kind, lane, repo, repo_key, pr, tip,
                              utc, utc_ts, session, for_lane, raw, seq, mode, workstation)
  SELECT d.line_id, x.kind, x.slot_role, x.slot_kind, x.lane, x.repo, x.repo_key, x.pr, x.tip,
         x.utc, x.utc_ts, x.session, x.for_lane, x.raw, v_seq, v_wmode, x.workstation
  FROM jsonb_to_recordset(coalesce(p_entry -> 'register_lines', '[]')) AS x(path text,
       line_no integer, raw text, kind text, slot_role text, slot_kind text, lane text,
       repo text, repo_key text, pr integer, tip text, utc text, utc_ts timestamptz,
       session text, for_lane text, workstation text)
  JOIN doc_lines d ON d.path = x.path AND d.line_no = x.line_no
  ON CONFLICT (line_id) DO UPDATE SET kind = EXCLUDED.kind, slot_role = EXCLUDED.slot_role,
    slot_kind = EXCLUDED.slot_kind, lane = EXCLUDED.lane, repo = EXCLUDED.repo,
    repo_key = EXCLUDED.repo_key, pr = EXCLUDED.pr, tip = EXCLUDED.tip, utc = EXCLUDED.utc,
    utc_ts = EXCLUDED.utc_ts, session = EXCLUDED.session, for_lane = EXCLUDED.for_lane,
    raw = EXCLUDED.raw, seq = EXCLUDED.seq, mode = EXCLUDED.mode,
    workstation = EXCLUDED.workstation;
  -- superseded_by, #160's rule (code/lanes-index:1013-1036): a lane's state on an object is
  -- its own last line on it, and another lane whose own last line on it is a TAKEOVER
  -- supersedes it. The stored rows of each touched object are re-marked first, against the
  -- incoming rows, so no transient pair of live claims trips one_live_claim.
  BEGIN                                         -- a 23505 on one_live_claim is LR005
  UPDATE object_holds h SET superseded_by = (
    SELECT min(e.lane_key) FROM (
      SELECT i.object, i.lane_key, i.verb
      FROM jsonb_to_recordset(v_holds) AS i(object text, lane_key text, verb text)
      UNION ALL
      SELECT s.object, s.lane_key, s.verb FROM object_holds s
      WHERE NOT v_holds @> jsonb_build_array(jsonb_build_object('object', s.object,
                                                                'lane_key', s.lane_key))) e
    WHERE e.object = h.object AND e.lane_key <> h.lane_key AND e.verb = 'TAKEOVER')
  WHERE v_holds @> jsonb_build_array(jsonb_build_object('object', h.object));
  INSERT INTO object_holds (object, lane_key, lane, verb, utc_ts, file, ordinal, superseded_by,
                            spellings, exempt, seq, mode)
  SELECT x.object, x.lane_key, x.lane, x.verb, x.utc_ts, x.file, x.ordinal,
         (SELECT min(e.lane_key) FROM (
            SELECT i.object, i.lane_key, i.verb
            FROM jsonb_to_recordset(v_holds) AS i(object text, lane_key text, verb text)
            UNION ALL
            SELECT s.object, s.lane_key, s.verb FROM object_holds s
            WHERE NOT v_holds @> jsonb_build_array(jsonb_build_object('object', s.object,
                                                                      'lane_key', s.lane_key))) e
          WHERE e.object = x.object AND e.lane_key <> x.lane_key AND e.verb = 'TAKEOVER'),
         x.spellings, v_kind = 'import' AND coalesce(x.exempt, false), v_seq, v_wmode
  FROM jsonb_to_recordset(v_holds) AS x(object text, lane_key text, lane text, verb text,
       utc_ts timestamptz, file text, ordinal integer, spellings text[], exempt boolean)
  ON CONFLICT (object, lane_key) DO UPDATE SET lane = EXCLUDED.lane, verb = EXCLUDED.verb,
    utc_ts = EXCLUDED.utc_ts, file = EXCLUDED.file, ordinal = EXCLUDED.ordinal,
    superseded_by = EXCLUDED.superseded_by, spellings = EXCLUDED.spellings,
    exempt = EXCLUDED.exempt, seq = EXCLUDED.seq, mode = EXCLUDED.mode;
  EXCEPTION WHEN unique_violation THEN
    GET STACKED DIAGNOSTICS v_con = CONSTRAINT_NAME, v_det = PG_EXCEPTION_DETAIL;
    IF v_con = 'one_live_claim' THEN
      RAISE EXCEPTION 'the object is held (one_live_claim): %', v_det USING ERRCODE = 'LR005';
    END IF;
    RAISE;
  END;
  IF v_wmode = 'primary' THEN                   -- the register's own rules bind new writes
    IF EXISTS (SELECT 1 FROM object_holds n JOIN object_holds o ON o.lane_key <> n.lane_key
               WHERE n.seq = v_seq AND n.verb IN ('CLAIMED', 'TAKEOVER')
                 AND n.superseded_by IS NULL AND NOT n.exempt
                 AND o.verb IN ('CLAIMED', 'TAKEOVER') AND o.superseded_by IS NULL
                 AND n.spellings && o.spellings) THEN
      RAISE EXCEPTION 'a claim''s spellings overlap another lane''s live claim (D19)'
        USING ERRCODE = 'LR005'; END IF;
    IF EXISTS (SELECT 1 FROM register_lines n JOIN main_slots m ON m.repo_key = n.repo_key
               WHERE n.seq = v_seq AND n.slot_role = 'take' AND m.line_id < n.line_id
                 AND lower(m.lane) <> lower(n.lane)) THEN
      RAISE EXCEPTION 'another lane holds main for this repository first (D18)'
        USING ERRCODE = 'LR005'; END IF;
    IF EXISTS (SELECT 1 FROM register_lines n                 -- F13: LANDING EXPIRED closes
               WHERE n.seq = v_seq AND n.kind = 'LANDING EXPIRED'   -- another lane's stale LANDING
                 AND NOT EXISTS (
                   SELECT 1 FROM register_lines t
                   WHERE t.slot_role = 'take' AND t.slot_kind = 'landing'
                     AND t.repo_key = n.repo_key AND t.pr = n.pr AND t.line_id < n.line_id
                     AND lower(t.lane) = lower(n.for_lane)
                     AND clock_timestamp() - t.utc_ts > interval '30 minutes'
                     AND NOT EXISTS (SELECT 1 FROM register_lines c
                                     WHERE c.slot_role = 'close' AND c.slot_kind = 'landing'
                                       AND c.repo_key = t.repo_key AND c.pr = t.pr
                                       AND c.line_id > t.line_id AND c.line_id <> n.line_id
                                       AND lower(coalesce(c.for_lane, c.lane)) = lower(t.lane)))) THEN
      RAISE EXCEPTION 'a LANDING EXPIRED names no stale LANDING (F13)' USING ERRCODE = 'LR005';
    END IF;
  END IF;

  -- 8. The proof: every touched path is in the entry's files, and renders to its digest.
  IF EXISTS (SELECT 1 FROM journal_edits j, LATERAL (VALUES (j.path), (j.to_path)) t(p)
             WHERE j.seq = v_seq AND t.p IS NOT NULL
               AND NOT coalesce(p_entry -> 'files', '[]') @> jsonb_build_array(
                         jsonb_build_object('path', t.p))) THEN
    RAISE EXCEPTION 'an edited path has no files row in the entry' USING ERRCODE = 'LR004';
  END IF;
  FOR r IN SELECT * FROM jsonb_to_recordset(coalesce(p_entry -> 'files', '[]'))
             AS x(path text, present boolean, sha256 text, final_newline boolean) LOOP
    INSERT INTO journal_files (seq, path, present, sha256, final_newline)
    VALUES (v_seq, r.path, r.present, r.sha256, r.final_newline);
    IF r.present THEN
      UPDATE files SET final_newline = coalesce(r.final_newline, final_newline)
      WHERE path = r.path;
      v_sha := encode(sha256(render_file(r.path)), 'hex');
      IF v_sha IS DISTINCT FROM r.sha256 THEN
        RAISE EXCEPTION 'proof: % renders %, the entry says %', r.path, v_sha, r.sha256
          USING ERRCODE = 'LR004'; END IF;
      UPDATE files SET sha256 = r.sha256, last_seq = v_seq WHERE path = r.path;
    ELSIF EXISTS (SELECT 1 FROM files WHERE path = r.path) THEN
      RAISE EXCEPTION 'proof: % is present', r.path USING ERRCODE = 'LR004';
    END IF;
  END LOOP;

  -- The kinds table's rules that come after: D24's two operator entries move the switch.
  IF v_kind = 'cutover' THEN                    -- set_seq: its own seq, 1, or m+1 after a loss
    UPDATE register_switch SET mode = 'primary', marker_state = 'adding', marker_seq = v_seq,
      import_seq = coalesce(v_imp.seq, import_seq),             -- a restore's has no import entry
      import_commit = coalesce(v_imp.git_commit, import_commit),
      import_tree = coalesce(v_imp.git_tree, import_tree), set_seq = v_seq,
      database_label = CASE WHEN v_after_loss THEN p_entry -> 'switch' ->> 'database_label'
                            ELSE database_label END,
      generation = CASE WHEN v_after_loss THEN gen_random_uuid() ELSE generation END,
      set_utc = date_trunc('second', clock_timestamp()),
      set_by_lane = p_entry -> 'journal' ->> 'lane', set_by_ws = v_ws
    WHERE id = 1;
  ELSIF v_kind = 'rollback' THEN                -- set_seq: its own entry, the last seq, k+1
    UPDATE register_switch SET mode = 'files', marker_state = 'removing', marker_seq = v_seq,
      set_seq = v_seq, set_utc = date_trunc('second', clock_timestamp()),
      set_by_lane = p_entry -> 'journal' ->> 'lane', set_by_ws = v_ws
    WHERE id = 1;
  ELSIF v_kind IN ('stray-import', 'stray-revert') THEN
    UPDATE mirror_state SET diverged_sha = NULL, found_utc = NULL, found_by_ws = NULL
    WHERE id = 1;
  END IF;
  RETURN v_seq;                                 -- the deferred foreign keys check at COMMIT
END $f$;

CREATE FUNCTION switch_to_files(p_client integer, p_cutover_seq bigint, p_lane text, p_ws text)
  RETURNS bigint LANGUAGE plpgsql SECURITY DEFINER
  SET search_path = lanes_register, pg_catalog, pg_temp AS $f$
-- The cutover's abort (F2): run only after a read-back proves the marker absent from origin.
DECLARE
  v_head bigint;
  v_ver  schema_version;
  v_sw   register_switch;
BEGIN
  SELECT seq INTO v_head FROM register_head WHERE id = 1 FOR UPDATE;         -- the same lock
  SELECT * INTO v_ver FROM schema_version WHERE id = 1;
  IF p_client > v_ver.version OR p_client < v_ver.writers_from THEN
    RAISE EXCEPTION 'register schema % (writers from %); this client is %', v_ver.version,
      v_ver.writers_from, p_client USING ERRCODE = 'LR003'; END IF;
  SELECT * INTO v_sw FROM register_switch WHERE id = 1;       -- an unconfirmed cutover only
  IF v_sw.mode <> 'primary' OR v_sw.marker_state <> 'adding'
     OR v_sw.marker_seq IS DISTINCT FROM p_cutover_seq OR v_head <> p_cutover_seq THEN
    RAISE EXCEPTION 'no unconfirmed cutover at seq %', p_cutover_seq USING ERRCODE = 'LR002'; END IF;
  DELETE FROM doc_lines     WHERE path = 'lanes/STORE';       -- its rows, children first
  DELETE FROM files         WHERE path = 'lanes/STORE';
  DELETE FROM journal_files WHERE seq = p_cutover_seq;
  DELETE FROM journal_edits WHERE seq = p_cutover_seq;
  DELETE FROM journal       WHERE seq = p_cutover_seq;
  UPDATE register_head SET seq = p_cutover_seq - 1 WHERE id = 1;   -- a new row version: every
                                                                    -- older writer snapshot gets 40001
  UPDATE register_switch SET mode = 'files', marker_state = 'absent', marker_seq = NULL,
    set_seq = p_cutover_seq - 1,
    set_utc = date_trunc('second', clock_timestamp()), set_by_lane = p_lane, set_by_ws = p_ws
  WHERE id = 1;
  RETURN p_cutover_seq - 1;
END $f$;

CREATE FUNCTION record_marker(p_client integer, p_seq bigint, p_commit text, p_ws text)
  RETURNS text LANGUAGE plpgsql SECURITY DEFINER
  SET search_path = lanes_register, pg_catalog, pg_temp AS $f$
-- S4's read-back of an operator entry's push, recorded (F12).
DECLARE
  v_ver schema_version;
  v_sw  register_switch;
  v_new text;
BEGIN
  PERFORM 1 FROM register_head WHERE id = 1 FOR UPDATE;      -- serialises with the abort
  SELECT * INTO v_ver FROM schema_version WHERE id = 1;
  IF p_client > v_ver.version OR p_client < v_ver.writers_from THEN
    RAISE EXCEPTION 'register schema % (writers from %); this client is %', v_ver.version,
      v_ver.writers_from, p_client USING ERRCODE = 'LR003'; END IF;
  SELECT * INTO v_sw FROM register_switch WHERE id = 1;
  IF v_sw.marker_state NOT IN ('adding', 'removing') OR v_sw.marker_seq IS DISTINCT FROM p_seq THEN
    RAISE EXCEPTION 'no unconfirmed operator entry at seq %', p_seq USING ERRCODE = 'LR002'; END IF;
  v_new := CASE v_sw.marker_state WHEN 'adding' THEN 'present' ELSE 'absent' END;
  UPDATE register_switch SET marker_state = v_new, marker_commit = p_commit,
    set_utc = date_trunc('second', clock_timestamp()), set_by_ws = p_ws
  WHERE id = 1;
  RETURN v_new;
END $f$;

CREATE FUNCTION find_write(p_write_id uuid)
  RETURNS bigint LANGUAGE plpgsql SECURITY DEFINER
  SET search_path = lanes_register, pg_catalog, pg_temp AS $f$
-- F3's one lookup at the deadline: the head row's lock under the caller's lock_timeout (55P03
-- while a transaction of the write may still be open), then the write's seq, or NULL if it is
-- absent. The writer has no UPDATE, so it cannot take that lock itself; the caller rolls back.
BEGIN
  PERFORM 1 FROM register_head WHERE id = 1 FOR UPDATE;
  RETURN (SELECT seq FROM journal WHERE write_id = p_write_id);
END $f$;

CREATE FUNCTION record_mirror_state(p_ws text, p_diverged_sha text, p_error text)
  RETURNS void LANGUAGE plpgsql SECURITY DEFINER
  SET search_path = lanes_register, pg_catalog, pg_temp AS $f$
BEGIN
  UPDATE mirror_state SET                      -- each right-hand side reads the old row
    found_utc   = CASE WHEN diverged_sha IS NOT NULL OR p_diverged_sha IS NULL THEN found_utc
                       ELSE date_trunc('second', clock_timestamp()) END,
    found_by_ws = CASE WHEN diverged_sha IS NOT NULL OR p_diverged_sha IS NULL THEN found_by_ws
                       ELSE p_ws END,
    diverged_sha = coalesce(diverged_sha, p_diverged_sha),   -- D16: the first finder's; only a
                                                             -- stray entry clears it
    last_error = p_error,
    last_error_utc = CASE WHEN p_error IS NULL THEN NULL
                          ELSE date_trunc('second', clock_timestamp()) END
  WHERE id = 1;
END $f$;

-- Migration 1's rows, from operations.md §2.4's bootstrap inputs (psql -v repo=… label=… lane=… ws=… script_sha256=…).
INSERT INTO schema_version (version, writers_from, readers_from, applied_utc)
  VALUES (1, 1, 1, date_trunc('second', clock_timestamp()));
INSERT INTO register_head (seq) VALUES (-1);
INSERT INTO mirror_state DEFAULT VALUES;
INSERT INTO register_switch (mode, marker_state, generation, repository, database_label, set_utc,
                             set_seq, set_by_lane, set_by_ws)
  VALUES ('files', 'absent', gen_random_uuid(), :'repo', :'label',
          date_trunc('second', clock_timestamp()), -1, :'lane', :'ws');
INSERT INTO migrations (version, script_sha256, by_lane, by_ws)
  VALUES (1, :'script_sha256', :'lane', :'ws');
COMMIT;                                        -- then 1.4's statements after its CREATE ROLEs
```

### 1.4 Roles, GRANTs and credentials (clause (b))

Clause (b)'s three roles (`proposal.md:683-693`) log in. A fourth owns the schema and cannot log in, so the three remain "the only ones the register's tools hold" (`proposal.md:609-611`). The bootstrap runs this through Brett's admin connection; each `CREATE ROLE` is guarded by a `pg_roles` check, and the bootstrap prints password-file lines and stores no secret (`proposal.md:607-611`). The `CREATE ROLE`s and the admin's GRANT come first, then migration 1, then the rest; that rest, and the GRANT, can run again.

```sql
CREATE ROLE lanes_register_owner NOLOGIN;      -- owns lanes_register and its functions
CREATE ROLE lanes_register_writer LOGIN;       -- REGISTER-WRITER
CREATE ROLE lanes_register_reader LOGIN;       -- READER
CREATE ROLE lanes_indexer LOGIN;               -- Amendment 14's indexer
GRANT lanes_register_owner, lanes_indexer TO CURRENT_USER   -- PG16 gives a CREATEROLE admin
  WITH INHERIT TRUE, SET TRUE;                 -- ADMIN only: SET for 1.2's AUTHORIZATION and
                                               -- SET ROLE, INHERIT for the GRANTs below
-- Migration 1 (§1.2, §1.3.1) runs here, and commits. Then:
REVOKE ALL ON DATABASE :"db" FROM PUBLIC;      -- the database Q22 names (psql -v db=…)
GRANT CONNECT ON DATABASE :"db" TO lanes_register_writer, lanes_register_reader, lanes_indexer;

REVOKE ALL ON SCHEMA lanes_register FROM PUBLIC;
GRANT USAGE ON SCHEMA lanes_register TO lanes_register_writer, lanes_register_reader;
REVOKE ALL ON ALL TABLES IN SCHEMA lanes_register FROM PUBLIC;
GRANT SELECT ON ALL TABLES IN SCHEMA lanes_register TO lanes_register_writer, lanes_register_reader;
ALTER DEFAULT PRIVILEGES FOR ROLE lanes_register_owner IN SCHEMA lanes_register   -- and a later
  GRANT SELECT ON TABLES TO lanes_register_writer, lanes_register_reader;          -- migration's
REVOKE ALL ON ALL FUNCTIONS IN SCHEMA lanes_register FROM PUBLIC;
ALTER DEFAULT PRIVILEGES FOR ROLE lanes_register_owner         -- global form: a per-schema REVOKE
  REVOKE EXECUTE ON FUNCTIONS FROM PUBLIC;                      -- cannot undo PUBLIC's default
GRANT EXECUTE ON FUNCTION lanes_register.commit_write(bigint, uuid, integer, uuid, jsonb),
                          lanes_register.switch_to_files(integer, bigint, text, text),
                          lanes_register.record_marker(integer, bigint, text, text),
                          lanes_register.find_write(uuid),
                          lanes_register.record_mirror_state(text, text, text)
  TO lanes_register_writer;                    -- no INSERT, UPDATE, DELETE or DDL anywhere
GRANT EXECUTE ON FUNCTION lanes_register.render_file(text)
  TO lanes_register_writer, lanes_register_reader;

-- #160's schema: the indexer owns it and its open_write fills it; the reader reads it.
-- The register writer gets nothing in lanes_index, and the indexer nothing in lanes_register.
CREATE SCHEMA IF NOT EXISTS lanes_index AUTHORIZATION lanes_indexer;
REVOKE ALL ON SCHEMA lanes_index FROM PUBLIC;
GRANT USAGE ON SCHEMA lanes_index TO lanes_register_reader;
GRANT SELECT ON ALL TABLES IN SCHEMA lanes_index TO lanes_register_reader;   -- any already there
ALTER DEFAULT PRIVILEGES FOR ROLE lanes_indexer IN SCHEMA lanes_index
  GRANT SELECT ON TABLES TO lanes_register_reader;

ALTER ROLE lanes_register_reader SET default_transaction_read_only = on;   -- floors for a client
ALTER ROLE lanes_register_reader SET lock_timeout = '1000ms';              -- that forgets its own;
ALTER ROLE lanes_register_reader SET statement_timeout = '1500ms';         -- each path still sets
ALTER ROLE lanes_register_writer SET lock_timeout = '2000ms';              -- its own (§1.3)
ALTER ROLE lanes_register_writer SET statement_timeout = '5s';
ALTER ROLE lanes_register_writer SET idle_in_transaction_session_timeout = '5s';
```

The writer's idle-in-transaction bound ends a half-open writer's transaction on the server within 5 s, which bounds the in-doubt retry's wait (the probes waited 1583 ms for one, `clarifications.md:245-248`). Under (A″) no live writer idles inside a transaction. Nor does an operator command: `import`, `cutover`, `rollback` and their records each send one `BEGIN; SELECT …; COMMIT;` script and hold no transaction across git or a render (F23, D24). The bound does not reach the bootstrap, which logs in as Brett's admin role and only sets the owner role.

| Role | SQL role | Held by | Where its credential lives |
|---|---|---|---|
| REGISTER-WRITER | `lanes_register_writer` | `lanes-edit.sh`'s writes, through `lanes-index register snapshot` and `register commit` (4A, D2); the projection writer's storage-layer entry (S4, `proposal.md:501-506`); the renderer, for its `mirror_state` writes (D16); `lanes-index` import, cutover (with its abort, `switch_to_files`, and `record_marker`), rollback and the two ways out of a divergence | `register_url=` and `register_passfile=` in `lanes-index.conf` (`proposal.md:152-156`) |
| READER | `lanes_register_reader` | the service's one connection (4A); the guard's direct read (Q11 (a)); S3's readers, the listings and the `SessionStart` block; `status`, `reconcile` and the readiness check | `reader_url=` and `reader_passfile=` (`code/lanes-index:64-65`) |
| indexer | `lanes_indexer` | `lanes-index sync` and `reconcile` of the index half | `url=` and `passfile=` |
| owner | `lanes_register_owner` | nothing: the admin connection takes it by `SET ROLE` | none |
| admin | Brett's | the bootstrap and its migrations | a libpq service and 0600 `~/.pgpass` of his own, named by `--admin-service`; never an argument, never in the environment, never stored by the tools |

- **The file:** `${XDG_CONFIG_HOME:-$HOME/.config}/openRepoTools/lanes-index.conf`, or the file `$LANES_INDEX_CONFIG` names. It is refused when group- or world-readable, inside a git work tree or under `~/.agents/`, or when a URL carries a password (`code/lanes-index:62-68`, `:296-301`); `register_url=` joins that check. Passwords live only in 0600 libpq password files, and an inherited `PGPASSWORD` or `PGPASSFILE` is dropped (`code/lanes-index:1300-1305`). `lanes-index` stays the one parser (`proposal.md:157-160`); R3a's `register_schema=` (default `lanes_register`) names a rehearsal's scratch schema (`r3a/REPORT.md:323`). On benches the file sits on Q23 (a)'s read-only mount (`proposal.md:1837-1839`). No URL or password enters an environment a session inherits (Amendment 14(c); `proposal.md:826-827`).
- **The renderer holds REGISTER-WRITER** (clause (b), `proposal.md:683-685`; the frame's roles table), because D16 has it record a divergence or a failed pass in `mirror_state`, through `record_mirror_state` and nothing else: the records that clear a divergence, the cutover and the rollback are `lanes-index`'s, as REGISTER-WRITER.
- **P20, plainly** (`clarifications.md:135-146`): the REGISTER-WRITER secret is readable by every lane process on its host, the same exposure as today's push rights. Function-only writes mean a hand-run write is still journaled, rendered, constrained and proved; they do not prevent one, which Rule 9(e) alone forbids. A holder can also call `commit_write` with a `rollback` entry, which stops every write with 69 until an operator acts; `switch_to_files` now only undoes a cutover not yet confirmed.

### 1.5 The version check and forward-only migrations

- **The row and the constant.** `schema_version` holds `version`, the last migration applied, and two floors, `writers_from` and `readers_from`. Every program that opens the register carries one constant, R3a's `REGISTER_SCHEMA_VERSION` (`r3a/REPORT.md:326`), apart from #160's `SCHEMA_VERSION`; a hygiene test asserts that every copy in a release is one number.
- **How each client checks it:**
  - a reader: the first SELECT of its snapshot, as #160's `open_read` does (`code/lanes-index:1357-1370`); it goes on only when `readers_from` ≤ its number ≤ `version`;
  - a writer: in its snapshot read and again in `commit_write`'s step 2, against `writers_from`. A migration that commits between them has touched the head row, so the commit gets 40001 and the re-read refuses;
  - the service: in every request's own snapshot, never from a cached answer (`proposal.md:712-718`);
  - the renderer: at each run, before it renders.
- **Too old** (below the floor) **or too new** (above `version`): nothing is read or written, and the refusal names both numbers and the remedy, "upgrade the tools" or "the database is behind: run the bootstrap". The code is the reader's own for an unreachable database: 69 for writers, S3's readers and `lanes-index`'s register commands (R3's proposal under Q9, `proposal.md:1610-1611`); 2 for the guard, which maps 69, 75 and 3 to 2 (D10); 1 for `managed-projection` (S4, `proposal.md:507-509`). The `SessionStart` block reads the mirror and says so, exit 0 (`proposal.md:438-444`). The renderer stops, and `status` says why. #160's index keeps its own rule (`unknown-schema`, `code/lanes-index:1093-1109`).
- **Applying a migration:**
  1. Migrations are numbered scripts shipped in `lanes-index` (`r3a/REPORT.md:327`). There is no down-migration: a bad one is followed by a forward fix.
  2. Only the bootstrap applies them, through Brett's admin connection, as `lanes_register_owner`. The bootstrap means "migrate to `REGISTER_SCHEMA_VERSION`", so a second run changes nothing (`proposal.md:623-624`). No client role owns anything, so no writer can apply one.
  3. Each is one transaction: `SET LOCAL lock_timeout = '2000ms'`; `UPDATE register_head SET seq = seq WHERE id = 1`, so every older writer snapshot gets 40001; a check that `version` is one below its own, else LR003; its DDL and GRANTs; the new `version` and floors; a `migrations` row with the script's sha256 and who ran it. A 55P03 is retried, never queued, since an `ALTER` waiting for its lock already blocks new reads (`clarifications.md:285-290`, `:301-302`).
- **The floors.** By default a migration sets both to its own number: R3a's exact match (`r3a/REPORT.md:326`; `proposal.md:1223-1225`). One that leaves older clients correct (an index, a view, a column with a default no client writes) may keep them, and says so in its header; one that adds a rule older writers would not keep raises `writers_from`. The order is always migrate, then install, so no client meets a database older than itself.
- **The cost.** Raised floors refuse every write from an environment not yet upgraded, with 69, until it is. So such a migration runs in a window like act 4's, after the readiness check (`proposal.md:612-619`) shows every writing environment ready (P19, `clarifications.md:122-133`). During the migration's own transaction, reads wait up to their 1 s `lock_timeout`, so the guard may refuse a prompt for that long.

### 1.6 The round-2 questions this part turns on

- **Q7, written under (A″).** Under the others:
  - **(A′):** a write takes the head row's lock first and holds it across the verb. The writer has no UPDATE privilege to take a `FOR UPDATE` lock, so the schema adds `lock_head()`, `SECURITY DEFINER`, at READ COMMITTED; `commit_write`'s step 1 re-takes the same lock, and its version and switch reads then see the latest commit. LR001 cannot occur; the lock's `lock_timeout` is the busy signal; the writer's idle-in-transaction bound rises to its deadline.
  - **(A):** as (A″), bounded at six attempts; the same schema. Test 4 fails it at six (`proposal.md:858-860`).
  - **(B):** a function per verb, a second copy of every rule (`proposal.md:1490`). The tables stay; each function gets its own GRANT.
  - **(C):** a SERIALIZABLE session held across the bash code. The schema is as under (A′), but without `lock_head()`, and 40001 arrives at `commit_write`.
  - **"(A″) with 69":** the schema is the same; each signal that means busy exits 69.
- **Q4(a), written under (i).** **(ii′):** `lanes/STORE` is pushed before the import, which runs at its parent. The `cutover` entry then records that commit in `git_commit` rather than being rendered, and its `journal_files` digest must be the pushed file's. The database still takes the import, then the switch. The marker's `Register-Seq` is the seq the `cutover` entry will take, the import's plus 1 (so 1 on a first cutover), and nothing in the journal ties a seq to a commit but that trailer, so the commit may be pushed before the import that makes seq 0. **(ii)** is (ii′) without the readiness gate: the schema is the same. **(iii):** no freeze; the cutover's tree-id abort fires and the import re-runs, which the `import` row's replacement rule already allows.
- **Q4(b), written under (iii).** **(iv):** `register_switch` gains `store_blob`, the pushed `lanes/STORE`'s blob id (`clarifications.md:393-398`), and `commit_write` takes the blob the writer read from origin inside the same write; a mismatch is LR002. **(ii):** the spools and R3b's `Register-Head` live outside the database (`r3b/REPORT.md:298-303`); a spool entry carries the `write_id`. **(i):** no change.
- **Q29, written under (a).** Nothing in the schema waits for the service, which is a READER client like the guard's direct read. Under (b) the schema is the same, and act 4 waits. If round 1's 4A were not confirmed and writes went through the service, REGISTER-WRITER's credential would move into the service and `lanes-index` would hold none; the functions and GRANTs would not change.
- **Q21(b), written under (B).** The schema does not turn on it. The renderer's push identity is a GitHub credential, not a database role: a deploy key in a 0600 file that `render_key=` in `lanes-index.conf` names (D23), used only in the push's own environment, so the configuration holds no secret and the key never enters an environment a session inherits (`clarifications.md:118-120`); the commit's author stays the journal's `author`, so renderers still make identical commits whoever pushes. Under (A) and (B′) the schema is the same, and `stray-import` and `stray-revert` stay under all three, since a ruleset's bypass actor can still push.
- **Q26 (a′)** needs a declaration that "cannot be a row in the database that is down" (`proposal.md:1876-1878`), so the schema holds none of it.

### Names this part defines or relies on

| Name | Kind | Defined |
|---|---|---|
| `lanes_register` | schema | `r3a/REPORT.md:172` |
| `lanes_index` | schema | `r3a/REPORT.md:169` (#160's) |
| `schema_version` (`version`, `applied_utc`) | table, columns | `r3a/REPORT.md:175` |
| `writers_from`, `readers_from` | columns | **(new)** |
| `migrations` (`version`, `applied_utc`, `by_lane`, `by_ws`) | table, columns | `r3a/REPORT.md:176-177` |
| `script_sha256` | column | **(new)** |
| `register_switch` (`mode`, `repository`, `database_label`, `import_commit`, `import_seq`, `set_utc`, `set_by_lane`, `set_by_ws`) | table, columns | `r3a/REPORT.md:179-185`; the switch, `proposal.md:166-171` |
| `import_tree`, `set_seq` | columns | **(new)**; `import_tree` is the tree id (`r3b/REPORT.md:214`), the marker's `lanes-tree=` (D21); `set_seq` is the seq of the switch's last change: an operator entry's own (a cutover's 1 or `m+1`, a rollback's `k+1`), or an abort's base (S5; open point 23) |
| `marker_state`, `marker_seq`, `marker_commit` | columns | **(new)**; the marker as SQL knows it (F1, F2, F12) |
| `generation` | column | **(new)**; F10, F18 |
| `store_blob` (under Q4(b) (iv) only) | column | **(new)** |
| `register_head` (`seq`) | table, column | `r3a/REPORT.md:187` |
| `mirror_state` (`diverged_sha`, `found_utc`, `found_by_ws`, `last_error`, `last_error_utc`) | table, columns | **(new)**; the divergence record of D16 |
| `journal` (`seq`, `base_seq`, `utc`, `kind`, `verb`, `lane`, `workstation`, `message`) | table, columns | `r3a/REPORT.md:189-195` |
| `operator`, `args_sha256` | columns | **(new)**; D24's flag (F2), and F10's digest |
| `write_id` | column | `proposal.md:253-258` |
| `journal_write_id_key` | constraint | **(new)** |
| `root_seq` (a key of the entry), `journal_one_root` | entry key, index | **(new)**; D22 |
| `switch` (`after_loss`, `database_label`), a key of the entry | entry key | **(new)**; the cutover's after-loss form (F18) |
| `author`, `git_commit`, `git_tree`, `repos_blob` | columns | **(new)**; `repos_blob` is R3a's risk 9 (`r3a/REPORT.md:353`) |
| kinds `import`, `write`, `rollback` | column values | `r3a/REPORT.md:192` |
| kinds `cutover`, `stray-import`, `stray-revert` | column values | **(new)** |
| `journal_edits` (`seq`, `ord`, `op`, `path`, `line_no`, `old_sha256`, `new_text`, `to_path`) | table, columns | `r3a/REPORT.md:197-205` |
| `journal_files` (`seq`, `path`, `sha256`) | table, columns | `r3a/REPORT.md:207-208` |
| `present` | column | **(new)**; R3a's `exists` |
| `files` (`path`, `final_newline`, `sha256`, `last_seq`) | table, columns | `r3a/REPORT.md:210-211` |
| `doc_lines` (`line_id`, `path`, `line_no`, `text`, `written_seq`, `written_mode`, `written_ws`), `doc_lines_pos` | table, columns, constraint | `r3a/REPORT.md:213-222` |
| `lanes` and its columns, `lanes_by_last_session` | table, index | `r3a/REPORT.md:226-232` |
| `log_lines` and its columns, `log_lines_by_object` | table, index | `r3a/REPORT.md:234-242` |
| `object_holds`, `superseded_by`, `one_live_claim` | table, column, index | `r3a/REPORT.md:244-250` |
| `mode` on `object_holds` | column | R3a's `post_cutover` (`r3a/REPORT.md:248`), renamed to match the other tables |
| `exempt`, `spellings` | columns | **(new)**; `spellings` is D19's |
| `holds` | view | `r3a/REPORT.md:27` (#160's) |
| `stale_claims` | view | `r3a/REPORT.md:252-259` |
| `register_lines`, `slot_role`, `register_lines_slots` | table, column, index | `r3a/REPORT.md:261-268` |
| `slot_kind`, `for_lane` | columns | **(new)**; `for_lane` is F13's |
| `LANDING EXPIRED` | line kind | **(new)**; F13, if Brett takes it |
| `main_slots` | view | `r3a/REPORT.md:270-278` |
| `apply_edits` | function (now `commit_write`'s step 6) | `r3a/REPORT.md:285` |
| `commit_write`, `switch_to_files`, `render_file`, `record_mirror_state`, `record_marker`, `find_write` | functions | **(new)**; `switch_to_files` is the cutover's abort (F2), `record_marker` S4's read-back (F12), `find_write` F3's one lookup |
| `lock_head` (under Q7 (A′) only) | function | **(new)** |
| SQLSTATEs `LR001`, `LR002`, `LR003`, `LR004`, `LR005`, `LR006`, `LR007` | error codes | **(new)**; `LR007` is F10's and F18's |
| `journal_files.final_newline`, `log_lines_pos`, the six `*_seq` indexes | column, constraint, indexes | **(new)**; lane 1's real-engine run |
| REGISTER-WRITER, READER, indexer | roles | `proposal.md:683-691` |
| `lanes_register_writer`, `lanes_register_reader` | roles | `r3a/REPORT.md:172`, `:281` |
| `lanes_register_owner`, `lanes_indexer` | roles | **(new)** |
| `REGISTER_SCHEMA_VERSION` | constant | `r3a/REPORT.md:326` |
| `mode=`, `register_url=`, `register_passfile=` | config keys | `proposal.md:149-156` |
| `render_key=` (under Q21(b) (B)) | config key | the frame's D23; it names a key file and holds no secret |
| `register_schema=` | config key | `r3a/REPORT.md:323` |
| `reader_url=`, `reader_passfile=`, `url=`, `passfile=` | config keys | `proposal.md:149-151` (#160's) |
| `lanes-index.conf` | file | `proposal.md:149` |
| `lanes/STORE` | file | `proposal.md:166-168` |
| `Register-Seq` | trailer | `proposal.md:304-306` |
| `lanes-index import`, `rollback` | verbs | `proposal.md:518`, `:527` |
| `lanes-index register snapshot`, `register commit`, `register read` | verbs | the frame's D2 |
| `lanes-index cutover` | verb | `proposal.md:1254` (R3's) |
| 69 | exit code | `proposal.md:213-217` |
| 75, "register busy" | exit code | `proposal.md:1706-1708` (the product seat's suggestion); the frame's D7 |
| 3, a write in doubt | exit code | the frame's D8 |
| 2 (the guard), 1 (`managed-projection`) | exit codes | `proposal.md:216-217`, `:507-509` |

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
   verb and arguments are this run's, and refuses with 2 where they are not,
   or where the run's generation is not the switch's (D8); the commit makes
   the same checks under the head row's lock (LR006, LR007; §1.3).
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
3. **The commit.** `lanes-index register commit`, one `psql` call, runs
   `commit_write(n, write_id, REGISTER_SCHEMA_VERSION, generation, entry)` at
   SERIALIZABLE (§1.3, §1.3.1), in this order:
   1. take the head row's lock and look the `write_id` up: an entry with this
      run's lane, verb and arguments is LR006, and the write exits 0; one for
      another write, or a generation not the switch's, is LR007, and it exits
      2 (D8). LR006 is reached where an earlier attempt in doubt committed
      after this attempt's snapshot, so it replaces the frame's 23505 backstop
      (F33);
   2. refuse unless the head is still `n` (LR001: the attempt has lost its
      turn, D6), the client is inside the schema's window (LR003, §1.5) and
      the switch is `primary` with the marker `present` (LR002);
   3. move the head to `n+1`, then insert the journal row (D6's order);
   4. apply the line edits to `doc_lines`, checking the base digests (LR004);
   5. refill the structured layer for the rows and lines touched, and check its
      constraints: one live claim per object, spelled any way (D19), the hold
      order (D18), each LR005, and the expectations the verb carries for the
      act's earlier reads (D5), each refusal a 2;
   6. prove the per-file digests (LR004), and `COMMIT`.

   It runs with `VERBOSITY=sqlstate`, so a 40001 is told from the other errors
   by its SQLSTATE and not by `psql`'s exit 3.

### The outcomes

| What happened | `lanes-index register commit` | `lanes-edit.sh` | Written |
|---|---|---|---|
| committed at `n+1` | 0, `committed <seq>` | forks the renderer; exits the verb's code; `claim` prints `register seq <n+1>` (Q19(A)) | the write |
| the head moved (LR001), 40001, 40P01, or 55P03 (`lock_timeout`) | 75 | a new attempt at once; at the deadline, exit 75, unless a commit of this write was in doubt (below) | nothing |
| `psql` exit 3 for any other SQLSTATE: the transaction was rolled back | 69, with the SQLSTATE | 69, naming the store and the error | nothing |
| LR005, a rule of the register (an object held, spelled any way, or `main` held) | 2, with the holder or the earlier line | 2 | nothing |
| LR006: this write is already in the journal | 0, with its seq | exits 0, as committed | the write, by an earlier attempt |
| LR007: the id belongs to another write, or to another generation | 2, naming the id and what differs | 2 | nothing |
| LR002: the switch, or its marker, is not what a write needs | 69, Amendment 22's text | 69 | nothing |
| LR003: the client is outside the schema's window | 69, naming both numbers | 69 | nothing |
| LR004: the edits do not apply, or the proof differs | 69 after one reload and retry | 69 | nothing |
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
10 s reconnect window. The lookup is `find_write(<id>)` (§1.3.1), in a
transaction it rolls back: it takes the head row's lock under the caller's
`lock_timeout` (the writer has no UPDATE, so its own `FOR UPDATE` would get
42501) and returns the write's seq, or NULL; holding the lock proves no
transaction of this write is still open. It exits 0 if the write is there, 75
if it is absent with the lock granted, and otherwise (55P03, or no answer) 3
with the id, the pending record kept. Agreement test 12 gains
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
seq, `write_id`, the database UTC of its commit, verb, lane, workstation, the
author identity (D15), the digest of the verb's arguments (`args_sha256`,
F10), today's commit message exactly (`LANES(<lane>@<ws>): …` or
`LOG(<lane>@<ws>): …`), the line edits across files with creates, renames and
removals, and the per-file digests after it. The mode is not on the entry:
each line it writes records the mode in `doc_lines.written_mode`, and each
structured row in its own `mode` (§1.2; lane 3's ruling, #165 6578020193).
Replaying the entries from the import's baseline gives every rendered file
byte for byte; that function has no git in it, and is phase 1's (Q6(c)). Its
form is the schema's (§1.2, §1.3.1).

**Operator entries** (D24). An entry whose edits touch only `lanes/STORE` is an
operator entry, flagged in the journal (`journal.operator`, §1.2). There are
exactly two kinds, the cutover's entry (seq 1, `m+1` after a re-import, or
`b+1` after a restore; D24) and the rollback's last seq, each written in its
command's own transaction, the one that changes the switch, so each passes the
switch check by construction. Besides the import's baseline (seq 0, or `m`
after a loss), which is journalled at `files`, nothing else is journalled while
the switch is not `primary` (S5). The renderer never renders one: the command
that wrote it pushes it with the renderer identity (D23), confirms it by
Amendment 23's S4 read-back and records it with `record_marker` (§1.3).

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
  last `Register-Seq` on origin when he declares it, which `--mirror-seq <m>`
  names where the host cannot fetch; `lanes-index outage clear` removes it.
  Under Q23(a) the configuration mount is read-only, so both run on
  the host, once per host, as the flip does. It is not a row in the database
  that is down, and it needs no fetch: each guard reads it offline in the time
  it takes to open a file.
- **The guard** answers from the mirror only when SVC-12's three conditions
  hold, checked in this order and offline by its client, never by the
  service: its database read failed (the service answered `refuse db <cause>`,
  or it was unavailable and the direct read then failed, or, at a rollback's
  start, the read found the switch at `files`); the file is present,
  unexpired and names this workstation's database label; and the tip
  the guard reads, `origin/main` as last fetched, carries a `Register-Seq` of
  at least `m` (`mirror-seq` is the declaration's field, and its check is the
  client's: lane 5's question 18). It then answers from the mirror, as above,
  with `(declared outage <utc>: <why>)` in place of the cause, and exits 0 or
  2 as that answer gives. A tip older than `m` is a refusal, 2: Brett's word
  covers writes during the outage, not how current a guard's last fetch is.
  Where the first condition holds and either other fails, the guard refuses
  with 2 and S3's outage text (SVC-12). Every write and every other deciding
  read still refuses with 69.
- **Why an expiry:** a declaration left in place could later let a guard read
  the mirror in an outage that is not estate-wide, the case (a′) exists to
  exclude. `lanes-index status` reports a declaration still in place while the
  database answers.
- **A rollback's start declares one too** (lane 3's ruling, #165 6578020193).
  From the transaction that sets the switch to `files`, no SQL write can commit
  anywhere, so `lanes-index rollback` writes the declaration, marked as from a
  rollback, where its configuration directory is writable, and prints
  `outage declare --rollback` for Brett to run on each other host. The
  rollback is Brett's act, so the declaration is his, as Q26(a′) requires. A
  guard's read that finds the switch at `files` counts as failed (condition
  1); otherwise the guard would refuse every prompt while the rollback still
  needs a session. The rollback's last step clears it (`operations.md` §2.3,
  §2.5).

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

The runbook is `operations.md` (lane 1's part 2). This section fixes what the
commands do.

**Every operator command writes for a lane** (D24; the frame review's F12).
Import, cutover, rollback and the disaster path run with
`LANES_LANE=<operator lane>` and go through that lane's checkout,
`<W>-lanes/<operator lane>/`, as Amendment 23's S2 item 3 asks of every write
("no lane, no write"); a shell or a `CLAUDE_NO_LANE=1` session names the lane
that way. Each `lanes/STORE` commit follows Amendment 23's S3–S4: the fetch and
checks first, then the push with the renderer identity (D23), then the
read-back. A push its read-back cannot confirm takes neither the success nor
the abort branch until a later read-back settles it: the command exits 3,
and its rerun reads origin back before it acts. **No operator command
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
a scratch schema for the rehearsal. A re-import before a confirmed cutover
replaces the root; after a rollback it continues the chain at the next seq,
so a later cutover never reuses a `Register-Seq` that `main` carries (§1.3).
Under `--after-loss` its baseline is the mirror's `m` (D22). The import is
journalled while the switch is `files` (D24).

### Cutover

`lanes-index cutover` (D25), a one-shot command on Brett's word:

1. In one short transaction taking the head row, set the switch to `primary`
   with the marker `adding`, recording the import commit, and write the
   cutover's entry, the operator entry adding `lanes/STORE` (D21, D24), at the
   seq after the import's: seq 1 at a first cutover. No write commits until
   the marker is `present`.
2. Build that commit on origin's tip, which descends from `C`, and check that
   the tip's `lanes` tree has the same id as `C`'s (R3's abort, which is S5's
   "moved under `lanes/`"), so a handoff commit on the tip only makes it
   rebuild. Push it, as a fast-forward of the tip, with the renderer identity,
   and confirm it by read-back, which `record_marker` records, setting the
   marker `present` (§1.3). It carries the entry's `Register-Seq`. A push it
   cannot confirm exits 3, and the rerun reads origin back first.
3. **The abort.** Where the tip's `lanes` tree has moved, the cutover pushes
   nothing; where a push was made, it first proves by read-back that the marker
   is absent from origin. Then `switch_to_files`, in one transaction, sets the
   switch back to `files` with the marker `absent`, deletes the cutover's entry
   and sets the head back to the import's seq (the adversary seat's note under
   Q20; §1.3), and the import is rerun at the new tip. An abort of a restore's
   cutover leaves no seq after `b` and changes nothing else (D24).
4. Then the flip, `mode=primary`, once per host (Q20(B); Q23(a)), with every
   write refused with 69 between the marker and a host's flip. Writes start at
   the seq after the cutover's entry, 2 after a first cutover; R3b's smoke test
   is one `NOTED`, its row, its mirror commit at that seq, the read-back and a
   lag of 0.

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
   removing `lanes/STORE` (D24), leaving the marker `removing`; its start
   declares the outage, marked as from a rollback (D20). Nothing is journalled
   after it: every later
   SQL write refuses with 69, everywhere, whatever it has fetched. With the
   database unreachable, the rollback stops here, changing neither the switch
   nor the marker; in the partition, where the database is up and only this
   clone cannot reach it, the same.
2. Render until the mirror holds seq `k` and its render set is byte-identical
   to a full render, proven by tree id.
3. Brett removes the ruleset (Q21(b)(B)), now that the mirror is proven current
   and before the marker's removal is pushed, so that files mode can write
   again once the marker is gone (F4): `lanes-index rollback --hold-marker`
   stops after step 2, and `lanes-index rollback`, run again, resumes at
   step 4.
4. Push the commit removing `lanes/STORE`, seq `k+1`, with the renderer
   identity, and confirm it by read-back, which `record_marker` records,
   setting the marker `absent`; a push it cannot confirm exits 3.
5. Clear `mode=primary` on every host, with the flip's reverse; once a
   files-mode write succeeds, Brett clears the declaration (D20).

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
2. Run `lanes-index bootstrap --roles-only` against the new database server
   first: roles are cluster objects, and a database backup does not hold them
   (F18).
3. If `b > m`, restore that backup into a new database; its journal already
   holds `m+1 … b`, and the renderer catches up. Otherwise re-import at
   origin's tip into a new database, with the head set to `m`, so
   `Register-Seq` stays monotonic in `git log` (D22).
4. Give the new database a new generation id, in its switch row. A pending
   record of the old generation is refused, since the write ids up to `m` went
   with the old journal, and a rerun under one could apply a write the mirror
   already holds (D8, D22).
5. Re-point every host's `register_url` and `reader_url` at the new database,
   from the host as the flip is (Q23(a)).
6. Run the cutover in its after-loss form, `lanes-index cutover --import <M>
   --after-loss` after a re-import, or `lanes-index cutover --after-loss` with
   no import after a restore: in one transaction it sets the switch to
   `primary` with a new generation id and writes the marker's rewrite as the
   cutover's entry, the seq after the head (`m+1` after a re-import, so the
   next write is `m+2`; `b+1` after a restore), which it pushes, reads back
   and records (D24). `status` shows a lag of 0.
7. Clear the declaration; the lanes resume; the notice names the seqs lost
   after `m` (or `b`), so each lane checks its last writes against the mirror.

`operations.md` §2.3 details these steps, including the marker's `database=`
where the new database's label differs, which the after-loss cutover's entry
rewrites. The
loss is bounded by the renderer's lag at the moment of loss, which `status`
shows (P14; the product seat, 6549261651). The database's host, its backups
and its downtime limit are Brett's (Q22); when the limit is reached, Brett
chooses between waiting and this path, and the operator lane runs it.

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
the push's own environment (D23). No `lanes-index` path puts a connection
string in an argument list, where `/proc` shows it for the process's life:
each passes it to `psql` through the child's environment, as SVC-2 maps it
(lane 3's ruling on lane 5's question 11; lane 4 changes the `-d <url>` at
`code/lanes-index:1315`). **The render key's reach is the same, and matters
more:** under Q21(b)(B) it is the ruleset's only bypass, and it sits in
a 0600 file that every lane process on its host can read, so any of them could
push to the rendered paths past the gate. The gate stops a stale or careless
writer, which uses the lane's own push rights; it does not stop a process that
reads the key on purpose, and the manual says so.

### The timeouts (D9)

Every connection names one address, since `connect_timeout` applies to each,
and sets its bounds when it connects (`options=-c lock_timeout=… -c
statement_timeout=…`; the probes found that form and `PGOPTIONS` both work).
No register path uses `lanes-index`'s `PGCONNECT_TIMEOUT=5` (`:1306`), and
only the import keeps its 300 s bound (`:1317`), as its statement timeout.

| Path | `connect_timeout` | `lock_timeout` | `statement_timeout` | Client timer | On expiry |
|---|---|---|---|---|---|
| the guard's direct read | 2 s | 1000 ms | 1500 ms | cut to the parse's start line, `T0 + 3.75 s`, so never above 3.75 s (SVC-8) | 2 |
| the service's connection, per request | 2 s, in the background only, outside any share (SVC-6) | 150 ms | 180 ms | 200 ms, inside the client's 300 ms wait | `unavailable`, and the client reads directly (SVC-8); `refuse db <cause>` only where the refusal is definitive (SVC-7) |
| the guard's answer-2 writes | as a writer's | as a writer's | as a writer's | the deadline and each call's timer cut to what the hook has left, `T0 + 4.25 s`; no call starts with less than its minimum left (SVC-8) | busy, 69 or in doubt exits 2, naming the write id |
| a writer's snapshot | 2 s | 2000 ms | 5000 ms | 8 s | a failed read: 69, or in doubt where an earlier commit of the same write was |
| a writer's commit | 2 s | 2000 ms | 5000 ms | 8 s | in doubt |
| a whole write | — | — | — | 30 s before a new attempt; 10 s to reconnect in doubt | 75, or 3 |
| a reader | 2 s | 1000 ms | 3000 ms | 6 s | a failed read (S3's table) |
| the renderer's reads, and `reconcile` | 2 s | 1000 ms | 10 s | 15 s | the render ends; the lag stays |
| `status` | 2 s | 1000 ms | 5000 ms | 8 s | 69, with the lag from git where it can read it |
| `rollback`, each call | 2 s | 5000 ms | 10 s | 15 s | 69; step 1's expiry re-reads the switch |
| `import` (lane 1's row, §1.3) | 2 s | 2000 ms | 300 s, #160's `psql` bound (`code/lanes-index:1317`) | 330 s | 69; nothing is swapped in (S5) |
| a migration | 2 s | its own, retried | — | — | refused; rerun |

Only the first row is measured: `connect_timeout=2` exits 2 at 2007 ms, a
`lock_timeout` of 1000 ms gives 55P03 at about 1011 ms, a `statement_timeout`
of 1500 ms gives 57014 at 1508 ms, and a 4 s client timer is the only bound on
a host that goes silent after connecting (`clarifications.md`, ADV-C4). The
other rows are the design's, and the test plan proves each exits inside its
bound. The REGISTER-WRITER role's `idle_in_transaction_session_timeout` is 5 s,
unmeasured; since it binds every session of the role, no operator command holds
a transaction across a git act or a render (S5), and DDL and migrations run as
`lanes_register_owner` through the bootstrap's admin connection, which that
limit does not bind (§1.4, §1.5). A migration sets its own
`lock_timeout`, because an `ALTER` only waiting for its lock already blocks new
plain reads.

## The guard and the service: the interfaces

The service's internals are lane 5's section, below. This frame fixes only what
the rest of the design relies on.

- **The guard's request** carries only the names of the files it reads (lane
  5's question 3; SVC-8, SVC-11): `lanes/LANES.md`, `lanes/aliases.tsv` and
  `lanes/log/<lane>.md`. What it reads today and a service cannot read for
  itself stays in the guard, read in its own container: the hook's
  `session_id`, `cwd`, `agent_id` and `prompt`; the window reference and name;
  and the live record's `name`, `nameSource` and liveness facts. **The service
  answers the register's text at `n`, and the reader decides** (lane 5's
  question 1; D17; SVC-7, SVC-8): the guard runs today's parser on that text,
  passes the prompt, refuses it or makes the offer, and exits 0 or 2. The
  offer file, the `/rename` typing and answer 2's two writes stay client-side;
  those writes are S1's transactions.
- **The fallback.** A service that is absent, refuses the connection, answers
  `unavailable` or does not answer within its share is unavailable, and the
  guard reads directly with the READER credential (Q11(a)), printing
  `guard: direct (service unavailable)` on stderr. It never reads the mirror,
  except during a declared outage, under SVC-12's three conditions (S3).
- **One budget.** The hook's fixed costs (bash's start and parse to the arm at
  `code/lanes-edit.sh:16856`, the tmux read, the scan of the session records),
  the wait for the service, the direct read, the parse and printing the
  refusal fit inside `GUARD_TIMEOUT`, 5 s, together (SVC-8's table). The
  direct read is therefore cut to the parse's start line, `T0 + 3.75 s`,
  leaving a 0.5 s reserve for the parse until lane 2 measures the real one
  (lane 5's question 7), and the test's assertion is the probes': exit 2 in
  under 4.5 s in every case. `CLAUDE_NO_LANE=1` still exits 0 before any input
  is read. **Answer 2's two writes are on the guard's path too**
  (`lane-start --no-launch --bind` and `set-row-state`). They run inside the
  hook (lane 5's question 16; SVC-8): the deadline and each call's timer are
  cut to what the hook has left, `T0 + 4.25 s`, and busy, 69 or in doubt exits
  2, naming the write id (the timeouts table). The detached form, exit 2 with
  "binding in progress", is the fallback if lane 2's timing of the bind says
  so.
- **The read API: the service answers the register's text at `n`, and the
  reader decides** (lane 5's question 1; D17; SVC-7). `who`, `binding`,
  `live-holder`, `next-free`, `managed-projection` and the guard's triple
  check reach the service as the files their parser reads, proven at the
  journal's head in that request, and each reader runs today's parser on them
  in its own process. Where the database refuses definitively the service
  answers `refuse db <cause>`, and on a timeout `unavailable`, so that the
  direct read decides; either way the reader exits 69, or 2 for the guard.
  **The cost is T055's:** Codex and the managed path run today's parser
  through `lanes-edit.sh`, whose read verbs ask the service, rather than ask
  the service a question.
- **What the service never does:** write, render, re-index, or read the mirror
  or the outage declaration. Writes keep their own connections (4A), the
  renderer is the writer's fork (D14), the nudge does not re-index the
  register on primary (S2), and the client reads the declaration (SVC-6,
  SVC-12).
- **Without the service.** Under Q29(a) the service is the last slice, so every
  interface above has a direct path that works before it exists; the cutover
  waits for it only if the measured direct guard is over Brett's budget.

### The register service's internals

Lane openRepoTools-5's section (#165 comments 6576888068 and 6577521499),
folded in place of the placeholder with lane 3's rulings on its eighteen
questions applied (#165 comment 6578521506). Lane 5's draft is
`/workspace/projects/.openrepotools-private-prep/work-20261021/lane5/d165/register-service.md`
(sha256 `529b3c7b0bfcf38284f27782388bcbf9f03c3717ba271d1fdb1da7fe6c7bd509`),
written against `design.md` at `b936ccd3` and `specs/register-service/spec.md`
at `c725310`; the fold reconciles it with the frame review's fold at
`6a5fcdc6`. It covers Q2(c) to (h) under 4A, built under "The guard and the
service: the interfaces" above and under D2, D17 and D20. Its own decisions
are SVC-1 to SVC-12, kept apart from D1 to D25 and S1 to S6. `code/…` is the
code leg at `2a057a6d`, every line re-read there. "This host" is Eagle's
py-bench container, 2026-11-02; every figure is from a probe in lane 5's
sandbox, recorded in
`/workspace/projects/.openrepotools-private-prep/work-20261021/lane5/d165/NOTES.md`
(sha256 `3cd78dd2639059d4728c3510d721979cd6d73ab555a4fabf56f28fba943324fc`),
on synthetic data where it touches records or the register. Where lane 5's
draft differed from the frame or the spec, lane 3's ruling settled it and the
frame or the spec now says the same; the questions and their rulings close the
section.

#### What today's guard costs, in four parts (the architecture seat's concern 4)

Measured on this host with copies of `code/lanes-edit.sh` in a sandbox, under
`env -i`, a sandbox `HOME`, a synthetic workspace and no tmux server, never with
hook input and never against live state. Process wall time, 30 runs each.

| Part | What runs | Median (min–max) | What the service removes |
|---|---|---|---|
| 1. Reaching the arm (`code/lanes-edit.sh:16856`) | bash's start, the parse of the whole dispatch `case` (`:15143`–`:18977`, one compound command, so the arm runs only after all of it is parsed), and the top level before it: the workspace resolver's `git` reads (`:1054`), the binding probe's `hostname`, `uname` and `/proc` reads (`:1098`), two `rev-parse --show-prefix` (`:1115`, `:2777`) and a `mktemp` (`:3449`) | 69.5 ms (60.5–147.8); of it, bash's start 2.7 ms and the parse alone (`bash -n`) 21.5 ms | nothing |
| 2. tmux | `here_context`'s two reads (`:4831–4832`, made once a process: `:4827`), `#W` (`:10433`), one per record in a window (`record_window_state`, `:9514`) and one per binding checked (`binding_window_state`, `:10054`, called at `:10731`) | 4.3 ms for one `display-message -p` against this container's server; by a static count, at least four calls a prompt, and five or more when a binding names another session (`binding_window_state` runs only where `gb_mine` is 0, `:10729–10731`): about 20–30 ms | nothing |
| 3. `transcript_holders` (`:9664`) | `session_files`' `find` over every profile (`:4569`), then `grep -l` over the records (`:9704`) and a parse of each match | about 48 ms over 522 synthetic records in a small tree. Over this container's real `~/.claude-profiles/profiles` (96,885 entries, 515 session records, plus 7 in `~/.claude/sessions`): the first walk, a counting `find` of every entry, took 410 ms, and the same `find` as `session_files` then took 58–96 ms in three runs. That the 410 ms is the cold cost is inferred from that drop, not measured as such | nothing |
| 4. The register read | `register_text` (`:3628`, through `register_text_checked`, `:3597`, `git show` at `:3612`) and the parses the guard runs on it: `rows_named_ci_alias` twice (`:10546`, `:10585`), `row_of_lane` (`:10603`) and `lane_binding_scan` (`:10707`) | the text alone about 58 ms; text and parses about 202 ms, so the parses about 145 ms, on a synthetic 6,410-line, 1.35 MB register and a 300-line log | the transport and the database connection; not the parses (SVC-9) |

So today's direct guard costs about 0.45 s here with a warm record walk, and
about 0.8 s with a cold one: about 70 ms to reach the arm, 20–30 ms of tmux,
110–150 ms of record scan warm (about 460 ms cold, inferred), and about 200 ms
of register read and parse. The first walk's 410 ms matches concern 4's
"about 0.4 s" for `transcript_holders` and the source's own "~0.4 s" for the
`grep -l` over every profile's records (`:9437`); the warm figure is what a
later prompt pays. Every "cold" figure below rests on that inference. The
source's "~2 s wall" for the register read (`:9437`, measured 2026-09-14) is
not reproduced on synthetic data; whether the real register's shape makes
the difference is lane 2's to measure (question 14, routed to lane 2).
Parts 1 to 3 are bash and the client's own container, and no form of the
service removes them. That decides test 10's 50 ms (SVC-10).

#### SVC-1. One service per environment, not one per workstation (Q2(c))

An environment is a bench container or a host shell, as the readiness check
already counts them (item 6 of "Migration and rollout"). On Eagle that is the
host shell, py-bench and cloud-bench: three services, three READER
connections.

Why not one per workstation:

- **The installs differ.** Each bench re-installs its vendored tools at start
  (item 6 of "Migration and rollout"), so py-bench and cloud-bench can carry
  different `lanes-index` builds. A shared service answers every container
  with one container's build.
- **Its life would be one container's.** A service started in py-bench dies
  when py-bench stops, and cloud-bench's lanes lose it with no say.
- **The state is already per environment.** `lanes-index`'s state directory
  (`code/lanes-index:173–174`) is under `~/.local/state`, which is per
  container here (the container's own overlay; only
  `~/.local/state/workbenches` and one log directory are bind mounts), and
  the image cache D17 shares with the writers lives there.
- **Sharing would buy nothing for the host-local facts.** Each container has
  its own pid namespace (py-bench's pid 1 is `docker-init`), and a pid is
  pronounced only inside its own namespace (Amendment 18(b)). Under SVC-11 the
  service reads no pid either way, so this is not a reason against sharing on
  its own; it is why a shared service could never take those reads over.

The cost is one READER connection per environment instead of one per
workstation, a handful in all. The spec's Purpose line, which said "one
database connection per workstation", now says one per environment (question
2); its requirements say "one process", which SVC-1 keeps per environment.

**Under one per workstation instead:** the socket and its lock would live under
`/workspace` (a Unix socket binds and connects there: probe A), the lock would
be a `flock` on the same shared disk, and the readiness check would have to pin
every environment of a workstation to one build. Whether a socket on the
shared mount accepts a client from another container is not shown: lane 5
cannot test across containers. A probe that binds in py-bench and connects
from cloud-bench would show it.

#### SVC-2. Where the socket lives, and who may connect

- **One path, no choice.** `run/` under `lanes-index`'s state directory,
  `${XDG_STATE_HOME:-~/.local/state}/openRepoTools/run/`: here
  `/home/brett/.local/state/openRepoTools/run/`. `XDG_RUNTIME_DIR` is not
  consulted, which removes one way two processes in one environment could
  disagree on the path. `XDG_STATE_HOME` can still differ per process, as it
  can for all of `lanes-index`'s state today; a process with another value
  finds no service, starts its own at its own path, and is answered correctly
  by it, at the cost of a second connection.
- **The directory is checked on every use,** by the service before it binds
  and by the client before it connects or starts one: it must be a directory,
  not a symlink (`lstat`), owned by this uid, with mode 0700. `lanes-index`
  creates it so when it is absent. One that fails the check is never used and
  never repaired: the client reads directly, the service exits 2 naming it,
  and `lanes-index status` reports it.
- **The files:** `register.sock`, `register.lock` (SVC-4), `register.owner`,
  `register.failed` (SVC-4) and `register.log`. The service binds under `umask
  077`; the probe's socket came out mode 0700, and the directory's check is
  what stops another user.
- **Why not `/tmp`:** it is per container here, which would do, but it is
  world-writable, so a name there can be taken first by another user, and
  `tmp` reapers delete sockets in it.
- **The length.** A Unix socket path may be 107 bytes on Linux (the probe
  bound 107 and refused 108; the check's re-run agrees) and 103 on macOS (R1,
  not shown here). Here the path is 56 bytes;
  `/Users/<name>/.local/state/openRepoTools/run/register.sock` is about 60. The
  client checks the length before it connects or starts anything; over 103
  bytes, it reads directly and starts nothing, and `lanes-index serve` refuses
  the same path with exit 2 and a stamp (SVC-4). Nothing shortens a path
  silently.
- **The owner file** holds the service's pid, its start UTC, its install's
  identity (SVC-4), and on Linux its pid namespace (`readlink
  /proc/self/ns/pid`) and the boot id (`/proc/sys/kernel/random/boot_id`). A
  client whose own namespace or boot id differs treats the service as
  unavailable. So if a bench ever bind-mounts `~/.local/state` across
  containers, SVC-1 still holds: each container's client refuses the other's
  service and starts none.
- **Both ends check the other's uid on Linux.** The service reads
  `SO_PEERCRED` on each connection and closes any whose uid is not its own;
  the probe's service makes that check, and a second uid was not available to
  show a refusal. The client reads `SO_PEERCRED` on its connected socket and
  treats a server of another uid as unavailable, so a socket planted in a
  directory that somehow passed the check still answers nothing. Where
  `SO_PEERCRED` is not available (macOS offers `LOCAL_PEERCRED` instead,
  untested here), the directory's owner and mode are the whole protection.
- **Nothing secret reaches a log or a refusal.** `register.log` and every
  `refuse db <cause>` carry fixed phrases and the SQLSTATE only, never
  `psql`'s message text and never a URL. The service passes its connection to
  its `psql` child through that child's environment, not as `-d <url>` in the
  argument list as `lanes-index` runs `psql` today (`code/lanes-index:1315`),
  because a long-lived argument list is readable in `/proc` for the process's
  whole life; lane 3's ruling on question 11 makes that so on every
  `lanes-index` path (Roles and credentials), and lane 4 changes `:1315`. The
  mapping is fixed: the URL's host to `PGHOST`, port to
  `PGPORT`, database to `PGDATABASE`, user to `PGUSER`, and the query
  parameters `sslmode`, `sslrootcert`, `sslcert`, `sslkey` and
  `target_session_attrs` to `PGSSLMODE`, `PGSSLROOTCERT`, `PGSSLCERT`,
  `PGSSLKEY` and `PGTARGETSESSIONATTRS`; `PGPASSFILE`, `PGCONNECT_TIMEOUT=2`
  and `PGOPTIONS` are the service's own. A password in the URL, or any other
  query parameter, is refused: `serve` exits with the stamp `config-refused`
  (SVC-4), and clients read directly. So the service never connects with a
  parameter the direct read's `-d <url>` would apply and it would not. The
  label it may name is `where` (`:1296`) with any userinfo removed, since
  `where` strips the scheme and the query but not a user name.

**The clients**, each through one implementation, `lanes-index`'s reader path
(SVC-3): the guard, at its register reads (SVC-8), and every reader D17
names. The `SessionStart` block only starts the service and asks it nothing
(SVC-12). No writer is a client (4A): a write's snapshot and
commit open their own connections with the REGISTER-WRITER credential, and
never wait for or start the service.

#### SVC-3. The client is `lanes-index`'s reader path, and the service is `lanes-index serve` (Q2(f))

- **The service** is a subcommand, `lanes-index serve`, not an eighteenth
  installable (`code/openRepoTools:1292` counts seventeen). `lanes-index`
  already holds the configuration parser, the READER credential, the `psql`
  path, the timeouts and the image cache (D2, D17), so the service reuses them
  rather than copying them. It adds nothing to `code/openRepoTools`'s
  `INSTALLABLES`, `code/pyproject.toml`'s `force-include`, the receipt counts
  in `tests.yml` and `release.yml`, or #237's gate. The documentation and the
  log still call it `lanes-register`.
- **The client** is `lanes-index register read`, which D17 already makes every
  reader's path: it asks the socket first and reads directly when the service
  is unavailable. The guard calls it once a prompt, as a prefetch, with
  `--for guard` (SVC-8), which also sets the guard's budget and its fallback
  line, `guard: direct (service unavailable)`. One socket client, one
  fallback, one set of timeouts.
- **Its cost:** a `lanes-index` process start is 72 ms here (`--help`, 15
  runs), against 32–44 ms for a minimal standard-library client doing a whole
  round trip (probe B3): about 30–40 ms a prompt, the price of not adding an
  installable. If test 10 shows it matters, a small client module can be split
  out later, which is Q2(f)'s other answer.
- **The toolchain floor** (the spec's fifth requirement). The service and the
  client are standard-library Python that runs on 3.9 (`code/pyproject.toml:45`)
  with `dependencies = []` (`:51`): `socket`, `fcntl`, `subprocess`, `select`,
  `hashlib`, `os` and `time`, and nothing newer than 3.9 offers. The new bash
  in `lanes-edit.sh` (SVC-8's prefetch call, its flag file and the three read
  sites) keeps the hygiene suite's bash-3.2 rules: no `coproc`, no
  `EPOCHREALTIME`, no `${var,,}`, no `mapfile`, no associative arrays.
- **D2 and the overview follow it** (question 9). D2's list of `lanes-index`
  verbs gains `serve`, `register read` gains `--for guard` and `--prefetch`,
  and the overview's `lanes-register` row is a `lanes-index` subcommand.

**Under a new installable instead:** `lanes-register` joins `INSTALLABLES`, the
two `force-include` lists in one commit, the receipt rows and "16 of 17"
counts, and the macOS install leg (the proposal's "Impact"), and saves about
35 ms a prompt.

#### SVC-4. How it starts, with no supervisor (Q2(d))

Nothing in the code leg starts a long-lived process today. No supervisor runs
in py-bench (pid 1 is `docker-init`; the host shell and cloud-bench are not
shown), and the spec rules out systemd and launchd as the service's
supervisor wherever they run, macOS's launchd included. The service starts on
first call and needs nothing else, on Linux and macOS alike:

1. **Who starts it.** The `SessionStart` block, at every session start on a
   primary workstation, after its own answer, without waiting (SVC-12: it reads
   the mirror and asks the service nothing, so the start only warms the service
   before the first prompt); and any client that finds no socket (`ENOENT`), a
   dead one (`ECONNREFUSED`), or an owner file from another namespace or
   another install, unless a failure stamp is fresh (item 7).
2. **How.** The client runs `lanes-index serve` detached, from Python:
   `subprocess.Popen(..., start_new_session=True)`, which is `setsid`; standard
   input and output on `/dev/null`, standard error to `register.log`; working
   directory `/`; and an environment reduced to `PATH`, `HOME`, `LANG`, the
   `XDG_*` variables and `LANES_INDEX_CONFIG`. No password and no URL is in it
   (Amendment 14(c)); the credential is read from the 0600 configuration, as
   `lanes-index` reads it today (the READER pair at `code/lanes-index:1292`,
   the password file set by `_env` at `:1299–1307`). `_env` also defaults
   `PGCONNECT_TIMEOUT` to 5, which the service overrides with 2. No bash code
   detaches anything. A new session also puts the service outside the hook's
   process group, so a group kill of the hook does not reach it; whether
   Claude Code ends a hook's descendants some other way is not shown (the
   facts table).
3. **One instance.** `lanes-index serve` takes `flock` on `register.lock`
   without waiting, and holds it for its whole life. A starter that finds it
   held exits 0 at once. Holding it, the starter tries the socket: one that
   answers means a live service, and it exits 0; `ECONNREFUSED` means a stale
   socket, which it unlinks; then it binds and listens. Probe B1, run in the
   probe client's wait form (the guard never waits): ten clients racing with
   no service started ten starters; one bound, nine exited on the held lock,
   and all ten clients were answered, in 16–62 ms from the client's own start.
4. **A stale socket.** A service killed by `SIGKILL` leaves its socket file.
   The next client gets `ECONNREFUSED` (3.9 ms in probe B4) and starts one,
   which unlinks the file under the lock and binds; that client was answered
   at 34.7 ms. A container restart leaves the same stale file on the
   container's overlay, and the same path recovers it.
5. **When it stops.** After 15 minutes with no request, it unlinks its socket
   under its lock and exits (probe B5 at 3 s idle, and the clean re-run at 2 s:
   the socket gone, the process gone by `kill -0`, in `NOTES.md`). It also
   exits when its install changes: it records the size, mtime and inode of its
   own `lanes-index` file at start and checks them on each request (one
   `stat`), answering `unavailable restarting` and exiting when they differ,
   so an upgrade never leaves an old service answering. It re-reads
   `lanes-index.conf` and its password file when their mtime changes, and
   exits if the mode is no longer `primary` (SVC-5).
6. **The guard never waits for a start.** A guard that finds no service starts
   one and takes the direct read for that prompt (probe B7: the decision in
   3.7 ms, 37 ms of process wall). Waiting for a new service's `psql` to
   connect could take its 2 s `connect_timeout`, which the budget cannot give.
   Readers do the same.
7. **A start that fails is not retried on every prompt.** Only the causes
   that make `serve` exit before it listens are stamped: `path-too-long`,
   `dir-check`, `config-refused` (a refused configuration, a mode other than
   `primary`, or a URL parameter SVC-2 does not map) and `no-psql`. `serve`
   writes `register.failed` with the UTC and that fixed cause, and no other
   text, then exits. A database that refuses or does not answer (`auth`,
   `unreachable`, a refused schema version) does not make `serve` exit: it
   listens, answers `refuse db <cause>` or `unavailable` (SVC-7), and
   reconnects in the background (SVC-6), so it writes no stamp. A client reads
   the stamp only after `ENOENT` or `ECONNREFUSED`; one younger than 60 s means
   it starts nothing and reads directly, and one stat is its whole cost. The
   service removes any stamp once it listens, and again at its first
   successful connect, so a stamp never sits beside a healthy service.
   `lanes-index status` reports a stamp and its cause. Without this, a service
   that cannot start would cost a Python start on every prompt, forever.

**Under socket activation instead** (#165's body offers it): socket activation
needs systemd or launchd as the activator, which the spec excludes as a
supervisor and which py-bench does not run, so "first call" is the form.

#### SVC-5. Only on `mode=primary` (Q2(e))

`lanes-index serve` refuses with exit 2 unless the configuration says
`primary`, and clients ask the socket only on a primary workstation (D3: no
configuration, no question). On files mode and `mode=index` the guard and the
readers stay exactly as today. In the frame's order, item 3 of "Migration and
rollout" lifts Q6(c)'s refusal of `mode=primary` before item 4 builds the
service, so the service is built against a `lanes-index` that accepts
`primary`; until a host's flip, it runs only against the fixture's
configuration, which is where test 10 runs it.

Reason: P4's "no second outage mode" applies to reads too. In files mode the
service would add a process that can be down, for a read (`git show`) that is
already local, and the measured part it would remove there, the text, is
about 58 ms of about 0.45 s. The product seat has this decided after the
measurement (Q29); the measurement above is the reason.

**Under "every mode":** the service would answer files-mode reads from
`origin/<branch>` as today's direct read does, and its start, stale socket and
version check would become a precondition of every estate's prompts, not only
primary ones.

#### SVC-6. The connection: one `psql` child on pipes

- One long-lived `psql` child with the READER credential, started in the
  background at the service's first request and restarted lazily after any
  failure: `psql -X -q -A -t -v ON_ERROR_STOP=0 -v VERBOSITY=sqlstate`, its
  connection in its environment (SVC-2), `PGCONNECT_TIMEOUT=2`, one address,
  and the server bounds in `PGOPTIONS`. No driver, and `dependencies = []`
  stays (the spec's third requirement).
- **One request at a time on it.** The service handles connections
  concurrently but sends one query at a time; each query ends with an `\echo`
  of a per-request marker, and the service reads `psql`'s output until it.
- **The per-request timer** is 200 ms. On expiry the service sends `SIGINT`
  to `psql`, which cancels the query, answers `unavailable slow`, and, if
  `psql` has not printed the marker within 100 ms more, kills it; the next
  request starts a new one in the background and is answered `unavailable`
  until it connects. A reconnect never runs on a request's time.
- **The timeouts row** for "the service's connection, per request", which
  the timeouts table (D9) now carries: `connect_timeout` 2 s, in the
  background only; `lock_timeout` 150 ms and `statement_timeout` 180 ms, so
  that the server cancels its own query just inside the 200 ms timer; on
  expiry, `unavailable`, and the client reads directly (SVC-8). It replaces
  the frame's 1000 ms and 1500 ms and "on expiry `refuse`, so 2", since a
  share of 300 ms cannot hold a 1.5 s statement (question 4). The spec's
  twelfth requirement now puts the service's 2 s connect outside any share:
  it is never on a request's path, so no share waits for it (question 5).
- None of this is shown: py-bench has no PostgreSQL binaries until 1A lands,
  so the `psql`-on-pipes framing, the `SIGINT` cancel and the 150, 180, 200
  and 100 ms values are the design's, for lane 2's fixture to prove (the
  facts table).
- **What the service never does,** as the frame fixes it: write, render or
  re-index. It never reads the mirror, and it never reads the outage
  declaration (SVC-12).

#### SVC-7. Every answer is read fresh: the text at head `n` (D17)

The request names the files it wants (`lanes/LANES.md`, `lanes/aliases.tsv`,
`lanes/log/<lane>.md`, and the rest of the render set) and nothing else. For
each request the service runs **one transaction at REPEATABLE READ READ
ONLY**, as the frame's snapshot is one such call (S1): the journal's head
`n`, the requested files' digests at `n`, and, in the same transaction, any
journal entries or whole files it needs. Then:

- if the images it holds in memory have the digests at `n`, it answers them;
- otherwise it brings them to `n` with the journal entries read in that
  transaction, by the same function the writers' snapshot uses, checks them
  against the digests, and takes a file whole from that transaction on any
  mismatch;
- it writes what it fetched to the per-environment image cache that S1's
  snapshot reads, so the cache is one cache, as D17 asks: a file is replaced by
  write and rename, and the cache's seq only moves forward, under a short
  `flock` of its own; whether the cache's form ([L4-c]) allows this second
  writer is lane 4's (question 13).

It answers `text <n>` only for that transaction's `n`, so the files of one
answer are never from two seqs. The answer is `text <n>`, then for each file
its path, its byte count, its digest and its bytes. A 1.35 MB answer, digest
checked, took 2.35 ms median from connect to the last byte (5.75 ms cold;
probe X).

**What the digest proves, and what it does not.** The service checks its
images against the database's digests at `n`, so it never sends an image the
database did not hold at `n`. The client checks each file against the digest
in the same answer, which proves only that the bytes arrived as sent: it
catches a cut or corrupted transfer, not a wrong service. Trust in the service
rests on SVC-2: the checked directory, the owner file and the uid check at
both ends. **A mismatch or a malformed answer** (a bad header, a byte count
that does not match, a digest that does not match, a file it did not ask for,
an answer over 16 MB, or one not complete within the 300 ms share) is
`unavailable`: the client discards all of it and reads directly. The client
reads at most 16 MB and stops at its share, whichever comes first.

**With the database unreachable,** the service answers `refuse db <cause>`
only where the failure is definitive in this request (the connection refused,
an authentication error, the switch not `primary`, the schema version
refused), with a fixed cause and the SQLSTATE (SVC-2); the client then
refuses exactly as the direct read would, 69, or 2 for the guard. Any timeout
is `unavailable`, and the client reads directly within what its budget has
left (SVC-8). It never answers from images it read before. The spec's second
requirement says so: the service refuses, or answers `unavailable` so that
the direct read decides; the outcome is the same 69 or 2, reached on a
timeout by the direct read rather than by the service (question 4).

**The read API's other answers: text, not by key** (question 1). Under D17
the service answers no question by key. `who`, `binding`, `live-holder`,
`next-free` and `managed-projection` reach it as the files their parser reads,
and today's parser runs on them in the reader's own process. Lane 3's ruling
takes this form, so "the service answers the register's text at `n`, and the
reader decides" is now the wording of the interfaces section's read-API
bullet, of the spec's first requirement (whose scenario is a reader's `who`
through the service), and of its sixth and seventeenth requirements. **The
cost is T055's:** today's parser lives in `lanes-edit.sh`'s bash and awk, so
Codex and the managed path run today's parser through `lanes-edit.sh`; T055
calls `lanes-edit.sh`'s read verbs, which ask the service, rather than ask the
service a question.

The reason is D17's: one parser, today's, and no second copy to drift. **Under
answers by key instead** (not taken): the service would answer those five
reads from the structured layer (R3a: one row per lane, the log lines, the
live claims) in the same REPEATABLE READ transaction, those answers would need
an agreement test of their own against today's parser (the import's "the same
S3 answers from the files and from SQL for every lane" is its model), T055
would adopt the keyed API directly, and the guard would still read tmux for
`binding_window_state` itself, so its answer could be keyed data with the
decision left client-side (SVC-8).

#### SVC-8. The guard's client, its share, and the one budget (Q2(g))

**The form: no new hook string, and no block at the top of the arm.** The hook
stays `~/projects/xFactory/lanes-edit.sh guard` (`code/openRepoTools:2343`), so
the installer's idempotence, the workBenches launcher's copies and #237's
probe are untouched. `guard_run` (`code/lanes-edit.sh:10391`) changes only
around its register reads. The window reads, the record scan, the parses, the
decision, the offer file, the `/rename` typing and answer 2's two writes stay
where they are.

**One prefetch, before the first read.** The guard's reads run in `$( )`
substitutions and pipelines (`:10518`, `:10546`, `:10585`, `:10603`,
`:10707`), so a shell variable set inside one is lost to the rest; that is why
today's per-invocation caches are files beside `$SE_CACHE_FILE`
(`:3444–3449`, and `:3460–3463` on a variable a subshell sets only in
itself). So on a configured workstation, `guard_run` makes **one** call,
`lanes-index register read --for guard --prefetch`, before `:10518`. It:

- answers the mode first, which folds D3's `lanes-index mode` into it, so a
  prompt pays one `lanes-index` start, not two. It follows lane 3's F8 ruling:
  `index` and the guard goes on as today; Q6(c)'s refusal with its own code,
  which lane 4 picks, and the guard refuses 2; and any other failure of the
  call (a refused configuration, a crash, exit 127, its timer) is files mode
  with a warning on stderr, after which a workstation under `lanes/STORE`
  refuses by Q21(a)(A), as the marker check does for a writer;
- on primary, asks the service (or reads directly) for `lanes/LANES.md`,
  `lanes/aliases.tsv` and `lanes/log/<window name>.md`, the lane a window is
  normally bound to, and writes them to the three cache files the three read
  sites use, in the forms those sites already expect:

| Read site | Cache file | Form |
|---|---|---|
| `register_text_checked` (`:3597`) | `$SE_CACHE_FILE.register`, which it already reads (`:3603`) | the text, as `register_text_checked` writes it |
| `lane_alias_text` (`:5367`) | `$SE_CACHE_FILE.aliases`, which it already reads (`:5369`) | the table; for an empty or absent table, the one line `# no lane aliases`, which is how the function caches one (`:5434`), because it tests the file with `-s` (`:5375`) and an empty file would send it to the mirror's `git cat-file` and `git show` (`:5395`) |
| `lane_log_events` (`:3521`) | `$SE_CACHE_FILE.log.<lane>`, new: it has no cache today | the log's text, keyed by the lane exactly as `lane_log_events` receives it in `$1`; the function consults it **before** `log_path_ci` (`:2991`, called at `:3522`), whose `git ls-tree` of `origin/<branch>` is a mirror read |

- **The service-down flag is a file,** `$SE_CACHE_FILE.direct`, written by the
  first fallback in the invocation. Every later read in that invocation,
  inside any subshell, finds it and goes straight to the direct path, so a
  silent service costs a prompt one 300 ms wait, never one per read.
- A read whose cache file is missing (a binding that names another lane than
  the window's, or the same lane spelled in another case, since `G_LANE` is
  the register's spelling) makes one more `register read`, direct if the flag
  file exists, which resolves the log's path case-insensitively in the images
  at `n`, never in the mirror. That is a second `lanes-index` start, about
  72 ms, on that path only.
- The new bash is a dozen lines at those sites, under the bash-3.2 rules
  (SVC-3).

**Why the decision stays in `guard_run`:** after the register is read, the
guard checks the bound window's liveness in tmux (`binding_window_state`,
`:10054`, called at `:10731`), and which window it checks comes from the
register's answer. A service that decided would need that tmux read sent to
it ahead of time (`tmux list-windows -a`, one more call), and a second copy of
the decision and of every refusal's text, which test 10 compares byte for
byte. Under D17 the reader runs today's parser, so there is one copy, and
the interfaces section now says the same (question 1).

**The budget,** all against the hook's own start, `T0`, aiming to exit by
`T0 + 4.4 s`, 0.1 s inside the test's "under 4.5 s":

| Share | Bound | How |
|---|---|---|
| Fixed parts, before the prefetch | measured about 0.25 s warm, about 0.6 s with a cold record walk (parts 1–3) | not bounded; spent first |
| The prefetch's `lanes-index` start | about 72 ms | spent once; it is also D3's mode question |
| The wait for the service | 300 ms, connect and answer together | the client's socket timer; a service that accepts and never answers costs 300 ms (probe B6: 348 ms of process wall for a 300 ms deadline) |
| The service's own work | 200 ms | its per-request timer, inside the client's 300 ms, so it answers `unavailable` before the client gives up |
| The direct read | `T0 + 3.75 s − now`, with `connect_timeout` 2 s, `lock_timeout` 1000 ms, `statement_timeout` 1500 ms | the timeouts table's guard row, which the frame had at "4 s, cut to what the hook has left"; this section cuts earlier, to the parse's start line, to leave the parse and the print their time, and the row now says so (question 7). The timer is never above 3.75 s |
| **The parse's start line** | `T0 + 3.75 s` | a read, through the service or direct, that completes after it is a refusal: the client exits as "too late to verify", and the guard refuses 2 without parsing |
| The parses and the decision | a reserve of 0.5 s | about 145 ms measured on synthetic data, so about three times that; lane 2's real-size measurement sets it (question 14) |
| Printing the refusal and exiting | 0.15 s | reserved |

So the last moment a read may finish is `T0 + 3.75 s`, the parses then have
until `T0 + 4.25 s`, and the guard exits by `T0 + 4.4 s`. **The worst case where
nothing answers:** about 0.6 s of fixed parts with a cold walk, 0.07 s of
`lanes-index` start and 0.3 s waiting for the service bring `now` to about
`T0 + 1.0 s`; the direct read's timer is then about 2.75 s. A database whose
connect never completes gives 2 at its 2 s `connect_timeout`, about
`T0 + 3.0 s`; one that goes silent after connecting gives 2 when the timer
expires, about `T0 + 3.75 s`; either refusal is printed by about
`T0 + 3.9 s`. A connect that completes at 2 s and a statement that runs to
its 1.5 s bound would end at about `T0 + 4.5 s`, which the timer cuts: in this
case that database answers 2, not 0, by design. **The late success** that the
reserve covers: a read that finishes just before `T0 + 3.75 s` leaves 0.5 s for
the parses and 0.15 s to print. The reserve is a measured share, not a bound
bash 3.2 can enforce (it has no clock finer than `SECONDS`); if lane 2 finds
the real parse above about 0.5 s, the reserve grows and the start line moves
earlier by the same amount, or the parse must leave the guard (the keyed form
SVC-7 records, not taken). Lane 3's ruling keeps the 0.5 s reserve until lane
2 measures the real parse (question 7).

**How the client knows `T0`.** The guard passes its own pid (`$$`, the hook
process). On Linux the client reads that process's start from
`/proc/<pid>/stat` against `/proc/uptime`. On macOS it reads `ps -o etime= -p
<pid>` and rounds up to the next whole second, which can only shorten the
direct read's timer, never lengthen it. **A clamp:** an elapsed time below 0
or above 4 s is treated as unknown, as where neither answers, and the client
then assumes 1 s has passed. That catches a `/proc/uptime` that a container
virtualises (lxcfs) differently from the process's start time, which would
otherwise give a plausible wrong figure.

**`CLAUDE_NO_LANE=1`** exits at `code/lanes-edit.sh:648–654`, before anything
above runs: 3.3 ms of process wall here, with no input read, no service
contacted and nothing started.

**Answer 2's writes (F6): inside the hook, cut to what it has left.** When
the person answers `2` to an offer, `guard_answer` runs `lane-start --no-launch
--bind` and then `set-row-state` (`:11171`, `:11199`), two S1 writes, and the
guard exits 2 whatever they do: it refuses that prompt either way, and the
person resends. **Decision: they run inside the hook, with every deadline and
timer cut to the hook's remaining time; they are not detached.** The guard
passes its own pid and an end line of `T0 + 4.25 s` to both commands in their
environment (two variables, which carry no credential). Every `lanes-index`
call they make (their reads, `register snapshot` and `register commit`) cuts
its client timer to what is left before that line, and starts nothing with
less than its minimum left (2 s, one `connect_timeout`, for a call that must
connect); the attempt loop's 30 s deadline (D7) becomes that line. Under lane
3's F3 ruling:

| The write's outcome | The writer exits | The guard |
|---|---|---|
| committed | 0 | 2, as today after a bind |
| busy: every attempt lost its turn, and none was in doubt | 75 | 2, naming the write id: "answer `2` again" |
| unreachable or refused | 69 | 2, naming the store and the cause |
| in doubt: at the line, one lookup does not find the id, and the head is not free (F3) | 3 | 2, naming the write id; D8's pending record stays, so answering `2` again reruns the same command line under the same id |
| not started: less than the minimum left | — | 2: "too little of the hook's 5 s was left to bind; answer `2` again" |

The offer is kept on every row but the first, as today's refusal keeps it
("The offer is KEPT, so answer `2` again", `:11173`). The timeouts table (D9)
carries its row: **"the guard's answer-2 writes: the deadline and each call's
timer cut to `T0 + 4.25 s`; busy, 69 or in doubt exits 2, naming the write
id."** Reason: the outcome is known before the hook exits, so the person
reads it at once; D8's pending record makes "answer `2` again" the same write;
and the detached form needs a state file for the next prompt to report, and
rests on a detached child outliving the hook, which is not shown (the facts
table). Its cost is that a slow database turns answer 2 into "answer `2`
again" more often. Lane 2 should time `lane-start --no-launch --bind` on
primary: if it does not fit in about 3 s on a healthy fixture, the detached
form, exit 2 with "binding in progress", is the fallback (question 16, routed
to lane 2).

**The exit mapping.** `register read` and `register read --for guard` are the
two client forms; `unavailable` never leaves the client.

| What happened | A reader's `register read` | `--for guard` | The guard |
|---|---|---|---|
| text at `n` through the service | 0 | 0, files cached | parses; decides 0 or 2 |
| the service unavailable, the direct read succeeded | 0 | 0, files cached, `guard: direct (service unavailable)` on stderr | parses; decides 0 or 2 |
| `refuse db <cause>`, or the direct read failed | 69, as S3's table | 69 | SVC-12's declared-outage check; otherwise 2, naming the store, the cause and the two ways out |
| a read completed after the parse's start line | — | 2 | 2: "too late in the hook's 5 s to verify the triple" |
| mode `index` | — (D3 asks first) | 0, `mode index` | today's files-mode guard |
| Q6(c)'s refusal of `mode=primary` | lane 4's code (F8) | the same code | 2 |
| any other failure of the mode question or the prefetch | — | any other code, or none | files mode with a warning (F8); under `lanes/STORE`, 2 by Q21(a)(A) |

#### SVC-9. What the service removes from the guard, and what it cannot

With the service up, part 4 shrinks to the prefetch's start (about 72 ms as
`lanes-index`, and it answers the mode too), the round trip (about 3 ms) and
today's parses on the text (about 145 ms). Without it, on primary, part 4 is
the same start, a `psql` start and connect, the head and digest query, the
image cache, and the same parses. The service removes the `psql` start, the
connect and the query: R1 measured one write at 14.7 ms through a new `psql`
process against 0.97 ms in a session; a read is not measured, and a connect
over TCP adds more. It does not remove parts 1 to 3, or the parses. That is
the honest size of Q29's question: on this host the direct guard on primary
should land near half a second warm, an order of magnitude inside its 5 s,
and the service saves tens of milliseconds of it. The figure that decides is
test 10's, on the fixture.

**Under the interfaces section's form, where the service decides,** or under
keyed answers (SVC-7's last paragraph): the parses would leave the guard, and
part 4 would be about 75 ms, the whole hook about 0.3 s warm here. The cost is
a second copy of the decision, or an agreement test of the keyed answers, plus
the tmux table in the request where the service decides.

#### SVC-10. Test 10's 50 ms, stated against this form

Concern 4 gives two ways to state it. Under SVC-8 the client runs after parts
1 to 3, so the first way (a client before the parse) is not this design, and
the 50 ms is **the service's answer time**:

- **What is timed:** inside the client, from the `connect()` call to the last
  byte of a digest-checked answer, for one `register read --for guard
  --prefetch` of the three files, against a warm service on the suite's
  fixture register.
- **Not included,** and recorded beside it: the client's process start, the
  hook's fixed parts, today's parses, and a cold start of the service, which
  the guard never waits for (SVC-4).
- **The statistic,** as Q10 settles: the median and p95 over at least 20 warm
  requests, with the cold first request beside them (the QA seat), recorded in
  the PR body, not asserted on shared runners (#165's body: "recorded in the
  PR body").
- **Beside it,** the whole hook's wall time through the service and through
  the direct read, and today's files-mode guard, each in the four parts above
  (P5's baseline and Q29's figure), with the record walk cold and warm.

The probes' toy service answered a warm request in about 3 to 5 ms (3.8 ms
median in `run/b2.out`; 4.5 ms in the check's re-run, `run/check-b2-rerun.out`)
and a 1.35 MB answer in 2.35 ms; the real service adds one transaction on a
held connection, about a millisecond by R1's session figure. The 50 ms should
hold with room. The spec's sixteenth requirement, which read "the
service-backed guard SHALL answer in under 50 ms", now reads "the service
SHALL answer the guard's read in under 50 ms", with the whole hook recorded
beside it; the whole guard's figure is Q29's (question 6).

#### SVC-11. Host-local facts (Q2(h))

- **For writes,** 4A answers it: a write never goes through the service, so
  liveness for `claim --force`, `rename-lane` and `retire-rows`, and tmux for
  `request-handoff`, are read by the writer in its own container, as today and
  as the frame's risk "the scratch tree reads host-local facts at the
  snapshot" says.
- **For the guard,** the client reads them, as the interfaces section has it,
  in its own container: the window reference and name, the live records, and
  the bound window's state after the register answer. Under SVC-8 it never
  sends them: the service needs only the file names, and the interfaces
  section and the spec's sixth requirement now say the request carries only
  those (question 3).
- **The service** reads no pid, no `/proc` entry of a lane, no session record
  and no tmux. It has nothing to refuse for want of them.

#### SVC-12. The declared outage, and the `SessionStart` block

**What the guard's mirror read requires (F7, F11).** The client reads the
declaration, never the service, and the guard answers from the mirror only
when all three of these hold, checked in this order, offline:

1. its database read has failed: the service answered `refuse db …`, or it
   was unavailable and the direct read then failed (`register read` exit 69);
2. `register-outage` (D20) is present beside `lanes-index.conf`, unexpired,
   and names this workstation's database label;
3. the mirror it reads, `origin/main` as last fetched (the guard never
   fetches), carries a `Register-Seq` of at least the declaration's
   `mirror-seq=<m>`, the field lane 3's F11 ruling adds and `outage declare`
   records (`operations.md` §2.5): the client reads the last `Register-Seq`
   trailer on that ref's first-parent history with one `git log -1
   --first-parent --grep='^Register-Seq: '` and compares.

Then the guard runs today's parser on the mirror's text and exits 0 or 2 as it
gives, with S3's `read: mirror at <sha12>, …` line and `(declared outage <utc>:
<why>)` in place of the cause. Since condition 1 holds, the mirror path is
entered only after a database read failed, so the label never stands for "the
service was down". A mirror below `m` refuses 2: "the mirror here is at seq
`<k>`, behind the declared outage's seq `<m>`; fetch it, or wait for the
database". Any other failed condition refuses 2 with S3's outage text. Every
write and every other deciding read still refuse with 69, and the service never
reads the mirror or the declaration, so a service left running through an
outage cannot carry a declaration past its expiry.

**Both specs carry it (F7).** `specs/register-service/spec.md`'s "The guard
falls back to the database, never the mirror" says "except during a declared
outage, under the declared-outage requirement", and its scenario "The service
is down and the database is not" says "and no outage is declared"; the
declared-outage requirement states the three conditions above.
`specs/sql-register/spec.md`'s requirement on mirror reads names the guard's
declared-outage read beside the naming read.

**Its reach per environment.** The declaration sits beside
`lanes-index.conf`, so each environment sees it through that environment's
configuration: under Q23(a) a read-only mount, which py-bench does not have
yet (`~/.config/openRepoTools` is the container's own overlay here).

**The `SessionStart` block (F24): not a client for its answer.** It reads the
mirror offline, as Amendment 22's S3 has it and as it does today, labelled
`read: mirror`, and exits 0. It decides nothing, so S3 lets it read the
mirror, and a service in its path would add a failure mode to a hook that
must never fail. Its one contact with the service is the start (SVC-4 item 1):
on a primary workstation, after its answer, it runs the start a client runs on
`ENOENT`, without waiting, so the first prompt finds the service warm. **The
spec's requirement, which made the block "a client the same way", now reads**
(question 17): "The `SessionStart` block SHALL stay
read-only, SHALL read the mirror offline and SHALL exit 0 always; on a
`mode=primary` workstation it SHALL start the service after its answer,
without waiting for it." Its scenario, the service down, an answer from the
mirror and exit 0, holds unchanged.

#### The review's list for lane 5, and where each is answered

The frame review (`REVIEW-frame-20261102.md`, sha256 `0689dde8…`, section
(c)):

| Item | Answered in |
|---|---|
| Whether the service or the client reads the declaration (F7, F11) | SVC-12: the client, with `mirror-seq` |
| Answer 2's writes inside the 5 s, or detached (F6) | SVC-8, "Answer 2's writes": inside, cut |
| One service per workstation or per container, and the socket's place when `/tmp` is not shared (Q2(c)) | SVC-1 and SVC-2 |
| Starting with no supervisor | SVC-4 |
| The `psql` child's timeouts and reconnect | SVC-6: 150, 180 and 200 ms per request, a 2 s connect in the background only, `SIGINT` then kill, a lazy restart |
| A cheap freshness proof for each request | SVC-7: one REPEATABLE READ READ ONLY transaction on the held connection returns the head `n` and the files' digests; images in memory are reused when the digests match, so a request at an unchanged head costs that one query, about a millisecond by R1's session figure |
| The `SessionStart` block's request (F24) | SVC-12: none; it reads the mirror and only starts the service |
| The host-local facts (Q2(h)) | SVC-11 |
| The client's form and hook string (Q2(g)) | SVC-8 |
| Whether it runs on `mode=index` (Q2(e)) | SVC-5: no |
| The exit mapping | SVC-8, "The exit mapping" |
| The four-part measurement | the first table |

#### The facts this section rests on, and how each was shown

| Fact | Shown by |
|---|---|
| Lanes run in containers on one WSL2 host, Eagle; this one is py-bench, with `LANES_WORKSTATION=Eagle` and `LANES_CONTAINER=py-bench` exported | probe F (`uname -r` names WSL2; the two variables tested by name, never printed with the environment); cloud-bench and the host shell are the coordinator's word, not shown |
| No supervisor runs in py-bench: pid 1 is `docker-init` | probe F, **for py-bench only**; the host shell, cloud-bench and macOS are not shown |
| `/workspace/projects`, `~/.claude`, `~/.claude-profiles` and `~/.agents` are bind mounts of one disk; `/home/brett`, `~/.local/state` (apart from two bind-mounted subdirectories), `~/.config/openRepoTools` and `/tmp` are the container's own overlay | probe F, from `/proc/self/mountinfo` |
| `/tmp` differs between py-bench and cloud-bench, and `/workspace` is shared | the coordinator's brief; not shown from one container |
| This container's tmux server's socket is under `/tmp` | probe F (the path's prefix only) |
| A Unix socket binds and connects on the shared disk; 107 bytes binds and 108 is refused | probe A, and the check's re-run (`run/check-probeA-rerun.out`) |
| macOS allows 103 bytes | R1 (the proposal's Q1); not shown here |
| `flock` gives one instance among racing starters; a `SIGKILL`ed service leaves a stale socket that `ECONNREFUSED` finds; idle exit unlinks | probes B1, B4, B5 |
| A silent service costs the client its deadline and no more | probe B6 |
| The record walk: 410 ms cold, 58–96 ms warm | probe F, by count only |
| The four parts' figures | probes C, C2 and C3, on synthetic records and a synthetic register |
| R1's 14.7 ms and 0.97 ms (one write); ADV-C4's timeouts | the proposal and `clarifications.md`, not re-measured |
| **Not shown:** the whole of SVC-6: `psql` on pipes, the `\echo` framing, the `SIGINT` cancel, and the 150, 180, 200 and 100 ms values | no PostgreSQL on py-bench until 1A; lane 2's fixture |
| **Not shown:** that a detached child started from a hook outlives the hook under Claude Code | a hook that starts `lanes-index serve` and a check of the child after the hook returns, in a real session |
| **Not shown:** a socket on the shared disk reached from another container; `flock` on it from two containers | a bind in one container and a connect from the other |
| **Not shown:** `SO_PEERCRED` with a second uid or across pid namespaces; macOS's `LOCAL_PEERCRED` | one uid and one namespace here; SVC-2's owner file does not rely on them |

#### What each other round-2 option would change here

- **Q29 (b), act 4 waits for the service:** the service stays 007's last
  slice, and act 4 waits for it; the readiness check also proves that
  `lanes-index serve` starts, binds within 103 bytes and answers in every
  environment. SVC-1 to SVC-12 are unchanged; the direct read stays the
  fallback (Q11(a)).
- **Q11 (b), the fallback refuses whenever the service is down:** the service
  becomes a precondition of every prompt, and the guard holds no credential;
  SVC-6's connection is the only READER holder in each environment. With no
  direct read to leave room for, the service's share becomes what the hook
  has left before the parse's start line, about 3 s, and the guard waits for
  a cold start inside it (a Python start, a `psql` start and its connect), so
  SVC-4's "the guard never waits for a start" goes. The 15-minute idle exit and
  the exit on an upgrade then cost the next prompt that cold start, and a
  service that cannot start refuses every prompt in its environment until
  someone fixes it; SVC-4's failure stamp would then refuse rather than read
  directly.
- **Q26 (b), no declared outage:** SVC-12's first two items go, and the client
  refuses 2 on any database failure. **Q26 (a), the mirror at any outage:**
  the client would read the mirror wherever both the service and the direct
  read fail, with no file to check; nothing changes in the service.
- **Q21(a) (B), everything reads the mirror on an unconfigured workstation
  under the marker:** nothing changes here, because an unconfigured
  workstation runs no service (SVC-5: no configuration, no question) under
  either answer. Under (A), as recommended, its guard refuses on the direct
  path and its `SessionStart` reads the mirror; under (B) its guard reads the
  mirror too. Neither asks a socket.
- **Q22 (b) or (c), Raven not configured:** Raven runs no service, because it
  is not `mode=primary` (SVC-5). Under (c) the database is on Eagle, so the
  connection is local and the 2 s `connect_timeout` is rarely spent.
- **Q23 (b) or (c), the configuration's place:** only the path the service
  re-reads, and the declaration's place, change. Under (c) a container started
  before a flip carries no `mode=primary`, so it starts no service until it is
  relaunched, and a container started before a declaration does not see the
  declaration either.
- **Q24, how #165 reaches the hosts:** under (a), the re-vendor and one image
  rebuild, each bench's `lanes-index` changes at its rebuild or re-install, and
  SVC-4's exit on an install change ends an old service at its next request;
  the readiness check's digest of `lanes-index` covers the service too, since
  it is the same file (SVC-3). Under (b) or (c), only the route changes; a
  host installed by `pipx` and a bench by re-vendor can carry different
  builds, which SVC-1's per-environment service already allows.
- **Q4(a) and Q4(b), Q7, Q21(b), Q28:** they shape the writers, the cutover and
  rollback, none of which touches the service, since writes never use it.
  Under Q4(b)'s (iv), each primary write adds an `ls-remote`; the service does
  not, since it serves reads.

#### Lane 5's eighteen questions, and lane 3's rulings

Lane 5 put these to lane 3, nine of them as differences from the frame or the
spec. Lane 3 accepted all nine and lane 5's other choices (#165 comment
6578521506). Each ruling, and where it landed:

1. **The service's answers: text, not by key (a difference).** Ruled: text at
   `n` (D17). The interfaces section's guard-request and read-API bullets, and
   the spec's first, sixth and seventeenth requirements, say "the service
   answers the register's text at `n`, and the reader decides". T055's cost,
   that Codex and the managed path run today's parser through
   `lanes-edit.sh`, is recorded in SVC-7 and in the read-API bullet.
2. **One per environment (a difference).** Ruled: one service per environment
   (SVC-1). The spec's Purpose says so.
3. **The request (a difference).** Ruled: the request carries only file names
   (SVC-8, SVC-11). The interfaces section and the spec's sixth requirement
   say so.
4. **`unavailable` on a timeout, and the service's timeouts row (a
   difference).** Ruled: SVC-6's 150, 180 and 200 ms row replaces the frame's
   1000 and 1500 ms service row in the timeouts table, with `unavailable` and
   a direct read on expiry. The spec's second and eleventh requirements say
   "refuse, or answer `unavailable` so that the direct read decides".
5. **The service's 2 s connect (a difference).** Ruled: it runs in the
   background, outside any share. The spec's twelfth requirement says so.
6. **Test 10's 50 ms (a difference).** Ruled: the 50 ms is the service's
   answer time (SVC-10), and the whole guard's figure is Q29's. The spec's
   sixteenth requirement says so.
7. **The parse reserve (a difference).** Ruled: the 0.5 s reserve, with the
   direct read cut to `T0 + 3.75 s`, stands until lane 2 measures the real
   parse (question 14). The timeouts table's guard row and the interfaces
   section's budget say so.
8. **The client's start.** Ruled: `lanes-index` stays the client (SVC-3).
9. **`serve` and the prefetch (a difference).** Ruled: D2's verb list gains
   `serve`, `register read` gains `--for guard` and `--prefetch`, and the
   overview's `lanes-register` row is a `lanes-index` subcommand.
10. **The new cache file.** Routed to lane 4: `$SE_CACHE_FILE.log.<lane>` for
    `lane_log_events` (`:3521`), placed in the phase plan.
11. **The `psql` connection in the environment, not the argument list.**
    Ruled: no connection string in any argument list, on any `lanes-index`
    path (Roles and credentials). Routed to lane 4, which changes
    `code/lanes-index:1315`.
12. **A failed start's cooldown.** Ruled: 60 s (SVC-4, item 7).
13. **The image cache's writers.** Routed to lane 4: whether the cache's form
    ([L4-c]) allows the service as a second writer, or the service keeps its
    images in memory only.
14. **The register read's and the parse's real cost.** Routed to lane 2: a
    measurement on a real-sized copy decides Q29's figure and SVC-8's reserve.
15. **Across containers.** Not needed by SVC-1 as ruled; the facts table keeps
    it as not shown.
16. **Answer 2's writes (F6).** Ruled: inside the hook, cut to `T0 + 4.25 s`
    (SVC-8). The timeouts table carries the frame's row, "the guard's answer-2
    writes: the deadline and each call's timer cut to what the hook has left;
    busy, 69 or in doubt exits 2, naming the write id". The detached form is
    the fallback if lane 2's timing of the bind says so; the timing is routed
    to lane 2.
17. **The `SessionStart` block (F24, a difference).** Ruled: it stays a mirror
    reader that only starts the service (SVC-12). The spec's requirement reads
    as SVC-12 gives it.
18. **The declared-outage exception (F7) and `mirror-seq` (F11).** Ruled:
    `mirror-seq` is a field of the declaration, which `outage declare` records
    in `operations.md` §2.5, and the guard's check of it is the client's (SVC-12).
    D20, S3's declared outage and both specs state SVC-12's three conditions.

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
  the direct read cut to the parse's start line (SVC-8), answer 2's writes cut
  to what the hook has left, and the probes' 4.5 s assertion.
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

The runbook is `operations.md`, summarised below. In order:

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

### Operations: acts 3 and 4

The runbook is lane openRepoTools-1's part 2, folded as a file of its own,
`operations.md`, beside this one. It holds:

- **§2.1, who acts:** Brett's words, the operator lane (`LANES_LANE=`, D24),
  its two operator entries and their read-back, and the coordinator's record.
- **§2.2, acts 3 and 4:** act 3 under Q20(B), and act 4 under Q4(a)(i): the
  drain, the last `PAUSED` write, the ruleset (F4), the import, the cutover,
  the flips on Eagle and then Raven, the smoke test and the thaw; and how
  (ii′) differs, with the ruleset's place there.
- **§2.3, the rollback and the disaster path:** the rollback under Q4(b)(iii),
  with `rollback --hold-marker` for the ruleset and exit 3 for a push it
  cannot confirm; and the disaster path's restore or re-import, with
  `bootstrap --roles-only`, the re-pointing and the after-loss cutover (D22,
  D24).
- **§2.4, the three operator tools:** `lanes-index bootstrap`
  (`--admin-service`, `--roles-only`), `ready` and `set-mode`.
- **§2.5, the declared outage:** by Brett's word or from a rollback's start,
  with `mirror-seq` and SVC-12's three conditions (D20).
- **§2.6, the notices:** P10's notices to the lanes, N1 to N8.
- **§2.7, R3c's landing path,** under 1A's split by seam.
- **§2.8 and §2.9:** the round-2 questions it turns on, and the names it adds.

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
6's window on his word, tested (S6; lane 1's v2 carries it, §1.2); and a
stray landing during a rollback, which has no journalled way out.

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
in later. Lane 5's section and lane 1's (the schema above, and
`operations.md`) are folded; they settle their own points, and route the
ones below marked with lane 5's question numbers or "from lane 1's v2".
The placeholders for lanes 2 and 4 are left as they were.

- **Lane 1, the schema and the operations:** its section v2, folded above and
  in `operations.md`, takes up the points this list routed to it (the
  operator-entry flag, the rollback's switch state, the generation id, the
  arguments' digest, the runbook's order and the disaster path; `renames.md`
  lists what moved). Still open for lane 1: the restore form's abort, which
  leaves no seq after `b` and changes nothing else, where v2's
  `switch_to_files` would set `files` and drop `lanes/STORE`'s rows (D24); a
  canonical repository key held with its spellings, as `spellings` is for
  D19, for D18's comparison, where v2 has `repo_key` alone (its
  `repo_spellings`); and the stale-`LANDING` release, if Brett takes it (F13).
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
  HTTPS remote. From lane 5's section: the new cache file
  `$SE_CACHE_FILE.log.<lane>` (its question 10); whether the image cache's
  form allows the service as a second writer, or the service keeps its images
  in memory only (question 13, [L4-c]); and `code/lanes-index:1315`, whose
  connection string moves from the argument list into `psql`'s environment
  (question 11). From lane 1's v2: the in-doubt lookup calls `find_write`
  (§1.3.1), and the printed rerun carries the generation (D8).
- **Lane 2, the test plan:** a test for each MUST as folded (a write in doubt at
  the deadline; a diverged rollback, and a failed seq-1 push; the ruleset, then
  a files-mode write after the rollback; a declared outage with the service and
  the database both down; answer 2 inside 5 s; two acts racing a binding, with
  the expectation refusing the second); `commit`'s 2 in test 8 (F19); a record
  reused by an unrelated identical command (F10); a guard reading a mirror older
  than `mirror-seq` (F11); the disaster path with an old record (F18); a failed
  mode query on `mode=index` and on primary (F8); output printed once in a burst
  (F14); which cases need TCP rather than the Unix socket; and no test that
  expects F33's backstop to fire. From lane 5's section: the real register's
  and the parse's cost on a real-sized copy, which set Q29's figure and
  SVC-8's parse reserve (its question 14); and the time of
  `lane-start --no-launch --bind` on primary, which keeps answer 2 inside the
  hook or moves it to the detached form (question 16). From lane 1's v2 and
  lane 3's rulings: a real-engine test for each object marked new and
  unproven ("The schema"); two writers claiming one object under two
  spellings (D19); one repository spelled two ways (D18); and the abort of a
  restore-form cutover (D24).
- **Lane 5, the service:** from lane 1's v2, confirm the libpq service form
  (`bootstrap --admin-service`, `operations.md` §2.4) against SVC-2's
  mapping, and that the declared outage's mirror read fits SVC-8's budget
  (`operations.md` §2.5).
