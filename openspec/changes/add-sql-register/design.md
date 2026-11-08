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

Status: a frame with the core written, and four sections folded in: "The
register service's internals" (lane openRepoTools-5), "The schema" (lane
openRepoTools-1's part 1), lane 1's part 2, the operations, as
`operations.md` beside this file, and "The phase plan" (lane
openRepoTools-4). One part is still a placeholder, marked **PLACEHOLDER** and
owned by a lane under lane 3's fan-out (#165 comment 6576888068): "The test
plan" (lane openRepoTools-2). Lane 3 folds it in when it arrives. Round 1 of Brett
Heap's questions is RULED: he confirmed all four answers on 2026-11-07 (#165
comment 6710653292). Round 2 is answered too: he took every recommendation, so
its recommended options, this design's working assumptions, are his answers.

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

### Known defects in today's code

Two findings in the guard at `2a057a6d`, lane 4's rows 34 and 35 in the phase
plan's "Differences from the frame". They are lane 3's, since lane 3 owns the
lane tools (#165 6708889286), and lane 3 filed both together, on Brett's word,
as opensoft/openRepoTools#347 (#165 6710653292).
Phase 1's first guard PR, the detached bind (the phase plan's "Answer 2's
detached bind"), fixes both, in every mode.

- **Today's answer 2 is killed partway.** It runs `lane-start --no-launch
  --bind`, about 10.47 s in files mode (lane 2, #165 6579356984), inside the
  hook's 5 s. At the timeout Claude Code sends SIGTERM to the hook's whole
  process group (lane 4's hook probe on Claude Code 2.1.319, R1 and R1b), so
  the bind and any `( … & )` child it forked, such as `index_nudge`'s, die
  partway through, which is more than the prompt passing. The probe turns lane
  2's inference into evidence; whether today's transcripts show
  `hook_cancelled` stays lane 3's check.
- **A failed bind cannot be answered again.** After a failed bind the window
  keeps `<to>`'s name, because `code/lanes-edit.sh:11155` renames it before
  `:11171` runs the bind; at the next prompt `guard_answer`'s identity test
  (`:11108`) drops the offer that said "answer `2` again" (`:11173`), and the
  table then refuses with `lane-start --no-launch <to>`, which vetoes: the #70
  loop again. The detached form renames the window back on a failure and keeps
  the offer, for at most three attempts.

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
  nothing changes but the configuration read, the marker check and three
  exceptions: the guard's answer 2, whose bind runs detached in every mode to
  fix two defects ("Known defects in today's code";
  opensoft/openRepoTools#347); `lane-start`'s bind, whose three register
  writes become one, `start-record`, with one commit and push in files mode
  (D5); and `openRepoTools wip init`'s refusal wherever any file under
  `lanes/` exists, in every mode (Q14(a)), as `specs/sql-register/spec.md`'s
  "Nothing changes where the marker is absent" has it. Lane 3 confirmed the
  first two (#165 6710770304), and the third is Brett's answer to Q14 (#165
  6710653292).

**Non-Goals:**

- Several verbs in one transaction (the design's own decision D5).
- A database driver, or the register's rules as PL/pgSQL (Q7's (B)).
- The register service as a write path or as the renderer's home.
- Building Amendment 23's act 2, or changing files mode beyond the marker check
  and the three exceptions: the guard's detached answer 2 ("Known defects in
  today's code"; opensoft/openRepoTools#347), `start-record` (D5), and
  `wip init`'s refusal wherever any file under `lanes/` exists (Q14(a)). Lane
  3 confirmed the first two (#165 6710770304); the third is Brett's answer to
  Q14 (#165 6710653292).
- Moving handoffs into SQL, or replacing `git log` as the audit trail.
- A cache that answers a deciding read on its own.
- `tasks.md` and the Speckit handoff, which follow this file.

## Decisions

### Round 1: answered, and RULED

Brett Heap, 2026-11-02T12:04:49Z, '1A 2A 3A 4A' — an accepted prompt
suggestion, held as provisional until he confirmed it (#165 comment
6576888068). Each answer is the recommended option. **RULED:** Brett confirmed
all four on 2026-11-07, "Confirm all four (Recommended)", his own selection in
a dialog in lane openRepoTools-3's session `83619980-06bb-4469-ae73-2a8c5e61981a`
(Question UTC 2026-11-07T15:22:12Z, Answer UTC 2026-11-07T15:26:24Z; #165
comment 6710653292), so round 1 is no longer provisional.

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

### Round 2: answered, every option as recommended

Each entry names its question, the option this design is written under, the
section it shapes, and what changes under each other option. The eight that
stand apart from "all as recommended" are marked so. **Answered:** on
2026-11-07 Brett took every recommendation, the eight included ("Take all
recommended (Recommended)", #165 comment 6710653292), so each option below is
his answer; `clarifications.md` records the answers, their times, and the
values still his to name.

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
   the busy outcome exits 69, and the seven `lane-start` read arms and the
   manual read 69 as "busy or unreachable".
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
    direct read stays the fallback (Q11(a)). Lane 2's figure (`MEASURE.md`,
    (14)): the guard on primary is about 0.42 to 0.47 s warm, against about
    0.55 to 0.60 s in files mode today, both an order of magnitude inside the
    hook's 5 s (SVC-9).

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
  78 (`EX_CONFIG`), never 2, which lane 4 picked within D10's map, since no
  lane tool exits 78 at `2a057a6d` (#165 6579524611); any other failure of the
  query (a configuration it refuses, a crash, 127, a timeout) runs files mode
  with a
  warning, and files mode's mandatory marker check after Amendment 23's S3
  fetch refuses on a SQL estate, so no failure of the query writes files there.
  A configuration typo on a `mode=index` host, as Eagle and Raven are between
  acts 3 and 4 (Q20(B)), then stops nothing but what it stops today (S1).
- **D4. On primary, the commit seam collects and never commits.** At
  `commit_push` (`commit_publish` after 1b) the primary branch keeps every
  content check the seam makes, then hands the staged change set and the
  message to the attempt loop, with no commit, pull or push (S1).
- **D5. One transaction per verb, not per act, with one composite verb.** S1's
  "ONE transaction" per write and S2's one mirror commit per write, with that
  write's own message, both read per verb; a transaction carrying an act's
  several verbs would merge their commits and needs Brett's reading of S1
  (R2C). The partial states an act leaves are the ones Q8's answers already
  handle, such as a lane left `SWAPPING` until the late `PAUSED` path. The
  journal keeps one entry per verb, so a later change could group them without
  a new schema. **The one composite verb is `start-record`** (lane 3's
  direction, #165 6579459518; the phase plan's "The bind as one verb"):
  `lane-start`'s three register writes for a bind, the session cell, the
  row's state and the `STARTED` or `RESUMED` line, become one write verb, one
  transaction carrying the union of their expectations; the Rule 3 stamp stays
  a git commit outside it. The verb runs in both modes: in files mode its
  three register commits and pushes become one (lane 3's ruling, #165
  6710770304). Its exits follow lane 4's corrected table (the phase plan's
  "The bind as one verb"), which agrees with round 1's 2A. R2 recommended one transaction per act; lane 4's
  phase plan withdraws that and accepts D5 with this one verb (lane 3's
  acceptance, #165 6708889286). **It also leaves an act's earlier deciding
  reads outside its writes' transactions**: `lane-start`'s reads at
  `code/lane-start:1555`, `:1609`, `:2335` and `:1154`, and those of
  `lane-end`, `lane-handoff` and `lane-rename`, are read calls of their own,
  and the writes they decide are later verbs, each with its own snapshot, so
  the head check covers a verb's own checks and not the act's earlier reads; a
  rival commit in between (a second binding) would go unseen. That is S3's
  "inside the act's transaction". **The design keeps S3 by expectations** (F5;
  the phase plan's "The acts and their expectations"): an act takes a base
  with `lanes-edit.sh act-begin` before its first deciding read,
  `<act-id>@<head seq>:<generation>`, read in one snapshot, and exports it
  as `LANES_ACT`. **`act-begin` also gives the act its generation,** the
  register generation it read that base in (D22), so that a base from a lost
  database's tail, whose seq numbers a new database reuses, is refused with
  LR007 (lane 3's ruling on lane 1's v2.4, #165 6711233420, point 1) once the
  database carries a new generation: after a re-import from its bootstrap on,
  and after a restore from its after-loss cutover on (S5's disaster path).
  Between a restore and that cutover, and after the cutover's abort, the
  restored database carries the lost database's generation, so such a base
  is refused only while it is past the head, by the client's check at the
  snapshot, with LR004 the backstop, and the lanes' pause covers the window
  (§1.3's "One gap stays"; the same ruling, point 4). The
  form is lane 4's: `<act-id>@<seq>:<generation>` on primary and
  `<act-id>@files` off it, and `act-begin --rebase` refuses with 2 where
  `LANES_ACT` is malformed, where its base is `files` on primary, or where
  the generation has changed (lane 4's check of v2.4, #165 6711545001, as
  lane 3 ruled in 6711564365). At each attempt's snapshot the client also
  refuses with 2 an act whose generation is not the switch's or whose base
  is past the head, so the commit's LR007 and LR004 for an act are
  backstops. Each write it decides
  names, in `LANES_EXPECT` on that call, the scopes its earlier reads cover
  (`row:`, `bind:`, `objects:`, `alias:`, `archive:` and `sid:`); and the
  commit checks them in its own transaction (S1's step 3.2, `commit_write`'s
  step 3b in §1.3), refusing with LR009, "expectation changed", which the
  client maps to 2,
  and writing nothing where a journal entry after the base, made outside the
  act, touched a listed scope. A digest of the row, the form first
  written here, would fail, since an act's own earlier writes change it. Every
  listed act can carry its expectations under three conditions: `lane-start`
  re-reads the row at `code/lane-start:2614` after a handoff wait, the handoff
  skill's step 4 re-derives the lane's identity, and `lane-end`'s gate
  inherits `LANES_ACT`. Each condition refused narrows S3, and the three go to
  Brett beside this difference from R2 (below). The journal's side is lane
  1's v2.4 (lane 4's row 20; lane 3's rulings, #165 6710770304 and
  6711233420): `journal.act_id`, set only on a write, from the entry's `act`
  key, and each entry's touched keys, derived inside `commit_write` and stored
  in `journal_scopes` (§1.2; §1.3's "The act").
- **D6. The head row is updated before the journal row is inserted.** The probes
  found the reverse order turns every lost race into a 23505 on the seq key.
  Retrying that 23505 as a 40001 would also work, but would read every
  uniqueness error on the journal as a race (S1).
- **D7. "Register busy" is exit 75 (`EX_TEMPFAIL`), at a 30 s deadline.** 75 is
  free (`clarifications.md`, ADV-C7), and is the product seat's suggestion; 30 s
  is lane 2's example, above the probes' worst drain of ten writers (10.2 s at a
  1 s window). An attempt that loses re-reads at once, as the probes measured.
  A write any of whose commit calls was in doubt never exits 75 without first
  making D8's lookup. This narrows Amendment 22's S1; Brett's answer to Q7
  gives "register busy" its own code, 75, so "(A″) with 69" is not taken
  (#165 6710653292) (S1).
- **D8. A write in doubt that cannot settle exits 3, naming its write id, and
  a rerun reuses the id.** 3 already means "cannot confirm whether it landed"
  (P16's precedent), but its row in the exit table (`code/lanes-edit.sh:486`)
  says "the edit is never a permanent loss", which on primary is wrong: an
  in-doubt transaction may never have committed. The row gains a primary
  clause: "may not be written; run the printed `LANES_WRITE_ID` rerun, which
  writes it once or finds it", and the phase plan lists the callers that read
  3 as "written". The id reaches a rerun two ways: the report prints the rerun
  with `LANES_WRITE_ID=<id>` and `LANES_WRITE_GENERATION=<generation>`, a new
  variable, and a pending record in the state directory lets
  a plain rerun of the same command line reuse it. The record carries the
  database generation (D22) and a maximum age of 15 minutes, and its name is a
  digest over `LANES_LANE`, `LANES_SESSION`, the verb and its arguments; a record whose pid is still
  live is waited for, up to D7's deadline, and is not reused; a run that still
  finds it live then exits 3 naming that record's write id, having sent
  nothing (lane 4's N2); and an id the journal holds for another
  lane, verb or arguments, or one minted against another generation, is
  refused with 2 (LR007, which the commit raises under the head row's lock,
  §1.3). Lane 4 owns the record's format (the phase plan's "The pending
  record"; lane 3's ruling on its row 16, #165 6710770304). A rerun that finds
  its write id committed runs the verb's after-COMMIT tail, which S1's "A write
  in doubt" lists per verb, once, in a child stopped at the seam with the entry's change set
  (lane 4's row 24, as lane 3 ruled). **Once any
  commit call of a write was in doubt, the writer ends with no code but 0
  until it has made F3's lookup:** it calls `find_write(<id>, <n>)` (§1.3.1),
  `<n>` being the largest `not_after` its commit calls carried (§1.3's step 9,
  LR008), its tries starting within the 10 s reconnect window, which starts
  when its own wait for `<n>` ends, each running to its own 8 s timer (the
  phase plan's "The in-doubt rule"). The function waits until the
  database clock has passed `<n>`, takes the head row's lock, checks the clock
  again under it, and looks the write id up (R2's S2). Found, it exits 0 and
  runs the verb's after-COMMIT tail once, in a child stopped at the seam with the entry's change set; absent, with the lock held, it exits the
  code its last attempt earned, whether 75 ("register busy"), 69, 2 or the
  verb's own; and with the lock not held within the window, or the lookup
  refused (LR011), it exits 3, naming the write id, the record kept. "Register
  busy" is never exited without that lookup. The register's own refusals exit
  2, and 69 is for an unreachable database and any other refusal (S1; the
  review of commit J, R1's S3 to S5 and R2's S1, as lane 3 ruled).
- **D9. The timeouts** in "Roles and credentials", with
  `idle_in_transaction_session_timeout` set on the REGISTER-WRITER role so that
  an orphaned transaction cannot hold the head row for long. Only the guard's
  figures are measured, with lane 1's timings of the operator calls under
  their bounds (R2's N1); the rest are the design's, for lane 2's tests to
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
  Q29(a). The in-place form, with its fence, was the fallback had lane 4's
  prototype failed the private-index form; the prototype passed, so it is not
  taken. One render runs at a time per checkout, under an `fcntl.flock` in
  `SyncLock`'s form, with both halves of its handshake, not a `mkdir` mutex: a
  renderer killed by SIGKILL leaves a `mkdir` mutex behind, and the checkout
  then renders nothing. Lane 4's prototype passes [L4-1] to [L4-5] with the
  `flock` ([L4-3] qualified: at most two renders holds for a burst that
  arrives within one pass) and fails [L4-4] with the mutex; the phase plan's
  "The renderer prototype" carries the proof (#165 6579524611, 6708867768)
  (S2).
- **D15. A render commit's author and committer are the identity the journal
  recorded** (the writing environment's `user.name` and `user.email` at write
  time), both dated by the journal entry's commit UTC, so that two renderers
  build the same commit and the history keeps today's attribution. For that to
  hold, `commit-tree` runs with `--no-gpg-sign` (git 2.43's `commit-tree`
  ignores `commit.gpgSign` and signs only with `-S`, which the flag overrides;
  the phase plan's "F29"), the encoding is pinned to UTF-8, and `lanes-index`
  composes the message itself: the journal entry's message byte for byte, with
  `Register-Seq: <n>` in its final trailer block, its last paragraph, appended
  as that paragraph's last line where the paragraph is already a trailer block,
  and otherwise after one blank line as a new last paragraph, rather than
  starting a second block. It never runs `git interpret-trailers`, whose
  parsing and `trailer.*` configuration would make a host's git version an
  input to the sha. That last paragraph is where git's `%(trailers)` reads it,
  which SVC-12's bounded lookup and the renderer's own lookup of `m` need (#165
  6579666731); the phase plan gives the mechanics, and the operator entries'
  commits follow the same rule (S2).
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
  6579459518). Lane 1's v2.3 adds `register_lines.repo_spellings` (§1.2):
  two lines name one repository where their `repo_key`s are equal or their
  `repo_spellings` overlap, in step 7's check and in `main_slots`' matching of
  a close to its take, so a `HOLD` or `LANDING` under the other spelling is
  LR005 in either order. Lane 3 accepted that pairing of closes by spellings
  (#165 6710770304, lane 1's point 3), with agreement tests against files
  mode's Rule 6 pairing (`RULE6_AWK`) on the imported history, which lane 2's
  plan has; a difference found there is a finding before the cutover.
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
  declares one too,** marked as from a rollback, with `mirror-seq=<k>`, the
  head its transaction found, not the mirror's `m` (R2's M1), so
  `outage declare --rollback` requires `--mirror-seq <k>`; it carries no
  expiry, and needs none before the rollback's end clears it. The rollback is
  Brett's act, so the declaration is his, as Q26(a′) requires, and a guard's
  read that finds the switch at `files` counts as failed (lane 3's ruling,
  #165 6578020193; `operations.md` §2.5). It is answered: Brett took Q26's
  (a′) (#165 6710653292).
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
  run again: lane 1's v2.3 records what that cutover replaces in
  `register_switch.restore_prior`, and `switch_to_files` puts it back, line
  ids included, without setting `files` (§1.3). The abort of the re-import
  form keeps the label and generation its cutover set, so every pending record
  and printed rerun made against the lost database still refuses with LR007,
  and the next cutover sets both again (lane 3's ruling, #165 6710770304,
  point 2; `operations.md` §2.3). **The rollback's last seq** is written in
  the transaction that sets the switch to `files`. Besides the import's
  baseline (seq 0, or `m` after a loss), which is journalled at `files`,
  nothing is journalled while the switch is not `primary` except these two
  entries. **An operator entry writes no row outside `lanes/STORE`:** a
  `cutover` or `rollback` entry that writes any other row, another `files` row
  or any structured row, is refused with LR004 when it is committed, rather
  than left in the journal to make a later abort fail closed with 23503 (lane
  3's ruling, #165 6710770304, lane 1's point 1). Lane 1's v2.4 delivers its
  SQL, tested on PostgreSQL 16.15: `commit_write` refuses such an entry before
  the head's lock (§1.3's kinds paragraph, §1.3.1). Every commit call carries `not_after`, the operator entries'
  included, since `cutover` and `rollback` run their commit call under a client
  timer too (lane 3's ruling, point 4; lane 4, #165 6709038351). The renderer never renders an operator entry; its command pushes it
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
departure is listed here. "Answered" names the question whose answer by Brett
Heap settles the row, and the comment that records it; "not yet routed" and
"for Brett: not yet answered" mean no question of his settles it, and lane 3
puts it to him.

| Item | Against | Status |
|---|---|---|
| D5: one transaction per verb, with one composite verb, `start-record`, for `lane-start`'s bind; R2 recommended one per act, and lane 4's phase plan withdraws that (its row 21); and its S3 consequence, that an act's earlier deciding reads sit outside its writes' transactions, kept by expectations (`act-begin` and six scopes), which every listed act can carry under the three conditions in the next row | Amendment 22's S1 and S3; R2, R2C | not yet routed |
| D5's three conditions (the phase plan's row 22): every act carries its expectations only under three conditions: `lane-start` re-reads at `code/lane-start:2614`; the skill's step 4 re-derives the identity; `lane-end`'s gate inherits `LANES_ACT`. Each refused condition narrows S3: without the first, the reads before a handoff wait are unchecked across it; without the second, step 1's reads are | Amendment 22's S3 | not yet routed; goes beside D5's row |
| `rename-lane` on primary (the phase plan's row 18): its handoff move and stamp become its after-COMMIT tail, its stamp checks before the seam, since the scratch tree has no handoff file; so Amendment 16's "one commit, refused or whole" becomes a transaction plus a git commit | Amendment 16 | not yet routed; goes beside Q13 |
| macOS without `setsid` (the phase plan's row 40): the detached bind falls back to python3's `os.setsid`, else `nohup` alone, which does not survive a group kill; `setsid` is not on macOS, and python3 always exists on primary, so the gap is a macOS host without python3 | Amendment 12 Addendum 1's choice 2, on macOS | not yet routed |
| Q21(b)'s scope: Brett's answer (#165 6710653292), as the round-2 file words it, puts the push ruleset on `lanes/**`, while D23, `operations.md` and the sql-register delta's gate requirement put it on the render set and `lanes/STORE`, leaving `lanes/repos.tsv`, `lanes/log/README.md` and the other git files under `lanes/` ungated (Q18(A)); the question is which scope he meant | Brett's Q21(b) answer, under D23 | not yet routed; goes with rows 18, 22 and 40 |
| Facts for Q21(b)(B)'s deploy key (the phase plan's row 2): the push goes to an explicit SSH URL in an isolated environment, not `git push origin`, since a deploy key is SSH-only and `insteadOf` and agent keys could defeat it; `GIT_CONFIG_GLOBAL` needs git 2.32 or later in every environment; whether `ready` can prove the key's write right (`git push --dry-run` over SSH) is open | Amendment 22's S2, under D23 | answered: Q21(b), 6710653292; the facts are still to find ("Open questions") |
| A fact for Q7's (A″) (the phase plan's row 27): whether a `psql` connection may be kept between an attempt's two calls under (A″), which saves about 13.7 ms an attempt and which (A″) excludes, since no session stays open across the shell code | Q7(A″); the Goals | answered: Q7, 6710653292: (A″), which excludes it |
| D7: "register busy" exits 75, not 69 | Amendment 22's S1 | answered: Q7, 6710653292: 75, its own code |
| Q4(b)(iii) and D22: a rollback needs the database; a database lost for good is the disaster path | Amendment 22's S5 | answered: Q4(b), 6710653292 |
| Q26(a′) and D20: the guard answers from the mirror in a declared outage, under SVC-12's three conditions; and a rollback's start declares one, marked as from a rollback, the rollback being Brett's act (`operations.md` §2.5) | Amendment 22's S3 | answered: Q26, 6710653292 |
| Q27(a): "takes S3's checks" read as S3's fetch for the private-index renderer | Amendment 23, "Relation to Amendment 22" | answered: Q27, 6710653292 |
| D18: the later `HOLD` or `LANDING` writer writes nothing | a reading of Amendment 22's S6 | not yet routed; goes beside Q17 |
| D1: the register in a schema of its own, not #160's | Amendment 22, "Everything Amendment 14 built is reused" | not yet routed |
| D25: `lanes-index cutover` as a command of its own | Amendment 22's S5 and its ratified names | not yet routed |
| A release for a stale `LANDING` (S6): lane 1's v2 carries it as the line kind `LANDING EXPIRED`, `register_lines.for_lane` and LR005 (§1.2, §1.3.1), tested on the engine and inert until a verb writes it | Amendment 10 and Rule 6, under Q17(B) | not yet routed: an open point for Brett |
| A stray landing during a rollback: the rollback refuses while a divergence is recorded, at its start, after a fresh fetch right before its transaction, and inside that transaction (LR012). So only a stray that lands after that check, once the rollback's entry has set the switch to `files`, has no journalled way out, since S2's two ways out each need `primary` (lane 1's v2; narrowed by its v2.6, R2's S5) | Amendment 22's S5, under Q4(b)(iii) | not yet routed: an open point for Brett |
| Files mode changes, against Amendment 22's scope, "a workstation with `mode=index`, or with no configuration, runs exactly as today": the guard's answer 2 runs its bind detached, and `lane-start`'s bind is one write, `start-record`, in every mode, which lane 3 ruled (#165 6710770304) and Brett was not asked; and `wip init` refuses wherever any file under `lanes/` exists, in every mode, the delta's third exception, which is his Q14(a) | Amendment 22's scope | for Brett: not yet answered |
| D8's exit 3: a write in doubt that cannot settle exits 3, naming its write id, and may have been written, where S1 has an unreachable database refuse with 69 and write "nothing anywhere"; Q16 was not in round 2 | Amendment 22's S1 | for Brett: not yet answered |
| Q18(A): the renderer renders the render set only, and a commit under `lanes/` outside it, such as `lanes/repos.tsv`, is part of the tip, where S2 has "a commit under `lanes/` that the renderer did not make" stop the renderer; the round-2 file calls (B) "as Amendment 22 literally reads" | Amendment 22's S2 | answered: Q18, 6710653292 |
| Q19(A): on primary `claim` prints "register seq `<n>`", where Amendment 7's claim "prints the log commit's sha" | Amendment 7 | answered: Q19, 6710653292 |
| Q20(B), round 1's 3A: `mode=primary` is written at the cutover, and act 3 leaves `mode=index`, where act 3 has Eagle and Raven configured together with `mode=primary` | Amendment 22's act 3 | answered: Q20, 6710653292 |
| 2A narrows S3: `lane-handoff`'s and the skill's naming reads come from the mirror (S3's table), and at four `lane-start` reads (`:1262`, `:1271`, `:3079`, `:3430`) a 69 stays a note | Amendment 22's S3 | answered: Q8, 6710653292 |
| Q21(a)(A): on a workstation not yet flipped, acts and the guard refuse while the listings and `SessionStart` read the mirror | Amendment 22's S3 | answered: Q21(a), 6710653292 |
| D24's two operator seqs, which `cutover` and `rollback` push themselves, where S2 has "the RENDERER, which commits … every sequence the mirror lacks". Q18(A) settles only that the marker is register seq 1 (`lanes/STORE` is `Register-Seq` 1), and round 1's 1A lists "rollback's push" among the phases without asking about the departure (lane 3's split of the row, commit M) | Amendment 22's S2 | for Brett: not yet answered |

## Architecture overview

### The components and their seams

| Component | Role on `mode=primary` | Credential |
|---|---|---|
| `lanes-index` | The one configuration parser and the one database client: `mode`, `register snapshot`, `register commit`, `register read`, `journal`, `render`, `import`, `cutover`, `rollback`, `status`, `reconcile`, `outage`, `serve` (the service, SVC-3), and the operator tools (bootstrap, readiness check, flip) | per subcommand: REGISTER-WRITER, READER, or the bootstrap's one-time admin connection |
| `lanes-edit.sh` | Every verb, unchanged in its logic, and three new ones from the phase plan: `act-begin`, a read verb that takes an act's base (D5); `start-record`, `lane-start`'s bind as one write (D5); and the internal `guard-bind`, the guard's detached bind (SVC-8); and one new flag, `canon-lane --walk`, which prints the names `canon-lane`'s walk visits, for an act's expectations (D5). Its three write seams: the resolver, which the write path no longer needs and the renderer does; `handle_preexisting`, where the captures refuse; and the commit seam, which hands a change set to the attempt loop (D4). Its readers enter at `register_text_checked` | none of its own: it calls `lanes-index` |
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
       b  the verb, unchanged, in a child process on a scratch tree at n;
          its commit seam collects the change set and the message, and waits
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
lane 3's rulings on v1 (#165 comment 6578020193) and on v2 (6579459518),
with v2.1's one hunk in this part, §1.4's admin row (`fix-v2.1.diff`, sha256
`7b54372637c079371777c396a1cc52c2409641aa9efcfd2865f1f8f15a8098c8`); v2.2
changes only part 2. Part 2, the operations, is `operations.md` beside this
file; a cross-reference
names it, as in "`operations.md` §2.3". Lane 1's numbering is kept: §1.1 to
§1.6 here, §2.1 to §2.9 there.

**Lane 1's v2.3** (#165 comment 6708990159) is folded on top, from
`/workspace/projects/.openrepotools-private-prep/work-20261102/165-section-v23/`:
`fix-v2.3.diff` (sha256
`24aea7d90ea22e4cfb03267bf28226e1e79f99e20b886bfb19a642bca62a45a3`, 28 hunks,
from v2.2, `d9908578c48f05772adc59b20f4a5c3ddbe58dc4877920325a5b5c5bcf060b25`),
which rebuilds `section-lane1-v2.3.md` (sha256
`fc2609e80fa8b3d6968fda822a1872f2f7765eaa3b0cf1524cfd99a78b826a28`, 1,613
lines), with `renames-v2.3.md` (sha256
`3b76b55675ec0a48d455b24199588f3370d72d5d587d46aa7a9cc7e7d3d3f011`). Its 22
part-1 hunks are applied here and its six part-2 hunks in `operations.md`, so
lane 1's text in both is v2.3's, with the same cross-reference form as before.
In this part v2.3 adds `register_switch.restore_prior`, filled by the restore
form's cutover and put back by its abort; `register_lines.repo_spellings` and
`main_slots.repo_spellings`, compared in step 7 and in `main_slots`' close
matching (D18); `not_after`, a required key of every entry, and step 9's LR008;
and it rewords the signals table's 40001, 55P03, LR006 and in-doubt rows and
the sentence after it, on lane 2's LR006 probe (6708917587). In the `cutover`
row of the kinds table, v2.3's "lane 3's ruling, 6579459518, point 1" replaces
the note the fold had put in place of v2.2's "an open point for lane 3". Lane
3's ruling on lane 1's point 1 (#165 6710770304) makes a `cutover` or
`rollback` entry that writes a row outside `lanes/STORE` an LR004 at commit
(D24); v2.4, below, delivers its SQL.
Lane 1's two statements that round 1 is provisional, the second bullet below
and the paragraph that starts "This part gives", now also say it is RULED
(#165 6710653292).

**Lane 1's v2.4** (#165 comment 6711221999) is folded on top, from
`/workspace/projects/.openrepotools-private-prep/work-20261102/165-section-v24/`:
`fix-v2.4.diff` (sha256
`b7de914be2dae6918472d991e4966d9c0c7673d8e8063fb67871d0d61b4bb29d`, 16 hunks,
from v2.3), which rebuilds `section-lane1-v2.4.md` (sha256
`4547b0354a9c0c6436a7aca878253524c70ac52f7904ccd5a0c0ccc7473a393c`, 1,791
lines), with `renames-v2.4.md` (sha256
`705c107cd6c85a5afb7e61734505d071e93a8fed6c79971215cdf18fede48f94`) and the
SQL its final run tested, `out/mig1-body-v24-final.sql` (sha256
`efdf8db9d92f918694b7e70789a8b4a63445c2c2f8b9e95e36f49b1b223290a4`). All 16
hunks are in this part and are applied here, so lane 1's text in this part is
v2.4's; `operations.md` has none. v2.4 adds lane 4's row 20:
`journal.act_id`, set only on a `write`, from the entry's new `act` key
`{id, base, generation, expect}`; and each entry's touched keys, derived inside
`commit_write` by `line_scopes` and `doc_line_scopes` and stored in
`journal_scopes (seq, scope)`. Lane 4's expectation check, S1's step 3.2, is
`commit_write`'s step 3b, with a new code, LR009, "expectation changed", which
the client maps to 2 with lane 4's `expectation:` message; lane 3 keeps that
check in SQL (#165 6710840240). v2.4 also carries ruling 1's LR004 at commit,
checked before the head's lock, and LR004 also covers a malformed act and a
base past the head; LR007 also covers an act whose base was read in another
generation. In §1.3's kinds paragraph, v2.4's sentence on ruling 1 replaces
v2.3's 23503 sentence, and the signals table's LR007 row is v2.4's; at both
places the text this fold found was v2.3's, so its hunks applied as they
stand. Lane 3's rulings on v2.4's four points (#165 6711233420) are folded in
D5 (the act's generation), the routed list at the end of this file (what lane
4 confirms), §1.3's bullet "Over-reporting only refuses", which gains one
closing sentence, lane 3's acceptance, and `operations.md` §2.3 (the lost
tail's window).

**Lane 1's v2.5** (#165 comment 6711914507) is folded on top, from
`/workspace/projects/.openrepotools-private-prep/work-20261102/165-section-v25/`:
`fix-v2.5.diff` (sha256
`684c877f3cfb50114af3d275579d7595efbb408aa22c228e47498fc808c719e8`, 10 hunks,
from v2.4), which rebuilds `section-lane1-v2.5.md` (sha256
`f7705d450f6defa3a40bf0da624feda8bddbcce4911366e0145ccc7f3164f270`, 1,848
lines), with `renames-v2.5.md` (sha256
`01b6b40d39e343187c4ba547ec2b6bda65bf8f889deba84a665ad35b8fe2772e`) and the
SQL its final run tested, `out/mig1-body-v25-final.sql` (sha256
`812f56ccdae9cdd9e225611df73f359d41251002de2f1849def1227e9484ff74`). All 10
hunks are in this part and are applied here, so lane 1's text in this part is
v2.5's; `operations.md` has none. Nine applied by `patch` at fuzz 0. The fifth, two
new bullets after "Over-reporting only refuses", went in by hand, since that
bullet now ends with lane 3's acceptance, and its "(2.2, step 8)" reads
"(`operations.md` §2.2, step 8)" here. v2.5 carries three points of lane 4's
check of v2.4 (#165 6711545001), as lane 3 asked (6711564365): lane 1's half
of S3, `line_scopes` also giving an archived row its normalized `archive:`
key by `RETIRED_ROWS_AWK`'s `key()`, trimmed as text; N1, step 8a's
`objects:` key lower-cased, through a new function, `entry_new_keys`; and N2,
a new code, LR010, "key not well formed", which `scope_checked` raises, with
`scope_fault`, where a key a line derives, at a write or at an import, is not
a scope, and which the client maps to 2. Lane 3 took option (a) on the
import's cost, keeping the check at import (#165 6711924759), so
`operations.md` §2.2's act 4 gives the import's new time. Lane 3's choice of
lane 4's S2 (#165 6711564365) changes three places of lane 1's text here:
§1.3's step 3 runs the commit call with `VERBOSITY=verbose` and
`SHOW_CONTEXT=never`, in place of `VERBOSITY=sqlstate`; LR009's bullet takes
the message from that one call, in place of the check's own query; and the
sentence after the signals table reads the SQLSTATE from the `ERROR:` line.
LR010's message is readable only under that choice. Lane 4's form of
`LANES_ACT`, `<act-id>@<seq>:<generation>` (6711545001, as lane 3 ruled in
6711564365), changes §1.3's "The act": its first sentence, the `base` and
`generation` bullets, and the files-mode bullet, whose refusal lane 4
confirmed. Where lane 1's text quotes lane 4's "S1's step 3.5", it now reads
"S1's step 3.2", where S1's step list puts the check since this fold (lane
4's S5).

**Commit L**, on lane 3's rulings on the review of commit J, changes six
places of lane 1's text here, each one a finding names: `b+1` beside `m+1` in
§1.2's departures, in `set_seq`'s paragraph and in the names table's
`set_seq` row (R1's N3); the basis bullet on round 2, which now says Brett
took those options (R1's N4); the signals paragraph's busy code, now Brett's
answer to Q7 (R1's S2); and §1.3's "One gap stays", where S1's client check
exits 2 first and LR004 is the backstop (commit J's writer's item 3).

**Lane 1's v2.6** (#165 comment 6714645316) is folded on top by commit M,
from `/workspace/projects/.openrepotools-private-prep/work-20261102/165-section-v26/`:
`fix-v2.6.diff` (sha256
`299f0db5fbec8b2ecb774f87e140377f7fb90fe596c5040002ff4e81e540aa4f`, 12 hunks,
from v2.5), which rebuilds `section-lane1-v2.6.md` (sha256
`a3e28a4bcf61376a794da5aa1be4bfc9f7e71d927f6cb1c0207a8151dfc33d8b`, 1,915
lines), with `renames-v2.6.md` (sha256
`19e025013bbaf206de7e6970c398b5d546041c1854c6e27ac04a554826ae8d48`) and
`texts-for-lane3-v2.6.md` (sha256
`b95659017379d1fc3d922f882bf251cd9d11bfa3c307c5a5cc4a98af17729ce6`), nine
texts for places outside lane 1's section. Ten hunks are in this part and two
in `operations.md` §2.3. Six applied here by `patch` at fuzz 0. Hunks 2 to 4
went in by hand, since commits J and L changed their context, with "(2.3)"
reading "(`operations.md` §2.3)". Hunk 5's bounds rows and its measurements
reach D9's table through lane 1's text 4, since commit J moved §1.3's table
there. v2.6 carries R2's S2, `find_write(p_write_id, p_not_after)`, which
waits on the server's clock and checks it again under the head row's lock,
with a new code, LR011, "lookup too early"; R2's S5, a `rollback` entry
refused while `mirror_state` records a divergence, with a new code, LR012,
"mirror diverged"; R2's N4, the after-loss cutover's label, as lane 3 ruled
(lane 1's option (a)); R2's N6, in `mirror_state`'s comment; and R2's N1, the
operator calls' bounds, measured. The signals table's in-doubt row now states
S1's rule: once a commit call was in doubt, the writer ends with no code but
0 until it has made the lookup.

**Lane 1's v2.7** (#165 comment 6714801156) is folded on top by commit N, from
`/workspace/projects/.openrepotools-private-prep/work-20261102/165-section-v27/`:
`fix-v2.7.diff` (sha256
`752bfa67bc001d3efaec85acd7d53deedba23c4e87b418596c9d06c488019cfd`, 3 hunks,
from v2.6), which rebuilds `section-lane1-v2.7.md` (sha256
`06e8bcbe4e3f1682fdbd41ba6dda30c81956f5929482c4e983453e306e7b3a77`, 1,915
lines). It changes text only, and answers lane 4's ask of lane 1 (#165
6714767210): step 9's "The wait" and the signals table's in-doubt row now say
that the lookup's tries start within the 10 s reconnect window, which starts
when the client's own wait for `n` ends, each running to its own 8 s timer,
and the in-doubt row says the tail runs once. Both applied here as they stand.
Its third hunk, the lookup's bounds row, has no place in this part, since D9's
table holds that row, and D9's row takes lane 4's words for it, by lane 4's
ask 4.

- **Basis:** the proposal at `0607526d`. A citation of `proposal.md`, `clarifications.md` or `council-review.md` is to the frozen copy, sha256 `b284a0eb2078fd24a48e833685b0073e881fdbe1625a1019d81a55a4aadaa340`, `636fb45369f825dd7f3d763bdb8d8bcb475ef4d76a3f7cf23203b907713ac2ff` and `9fa18c5ed76d88d4f25b3f8eaa37626ad0511985061f9e0649ff49d13c0fce62`.
- **Round 1, held as provisional when lane 1 wrote this, and RULED since** (Brett's confirmation of 2026-11-07, #165 6710653292): Q6 = 1A (the split by seam), Q8 = 2A, Q20 = 3A (`mode=primary` written at act 4), and Q2(a) and (b) = 4A.
- **Round 2, the recommended options, which Brett took** (#165 6710653292): Q4(a) (i), Q4(b) (iii), Q7 (A″), Q21(b) (B), Q22 (a), Q26 (a′), Q28 (i) and Q29 (a). Where a part turns on one of them, it says what changes under each other option.

**New and unproven** (lane 3's rulings): the objects this part adds that the
proposal does not name are accepted as new and unproven: `set_seq`,
`mirror_state` with `record_mirror_state`, `spellings`, LR005, and v2's
`journal.operator` flag, `generation`, `args_sha256`, LR006, LR007,
`find_write`, `record_marker` and `switch_to_files`, and v2.3's
`restore_prior` with `register_switch_check4`, `repo_spellings`, `not_after`
and LR008, and v2.4's `journal.act_id` with `journal_act_id_check` and
`journal_act_id_write`, `journal_scopes` with `journal_scopes_seq`,
`line_scopes`, `doc_line_scopes`, the entry's `act` key, the scope `*` and
LR009, and v2.5's `entry_new_keys`, `scope_fault`, `scope_checked`, the
normalized `archive:` key and LR010, and v2.6's LR011, LR012 and
`find_write`'s second argument, `p_not_after`. Lane 1's v2, v2.3, v2.4, v2.5
and v2.6 runs on PostgreSQL 16.15 exercised them (`renames.md`,
`renames-v2.3.md`, `renames-v2.4.md`, `renames-v2.5.md`,
`renames-v2.6.md`), all but LR011 from the check under the lock, which needs
the server's clock to step back; each still needs a real-engine
test in lane 2's plan before it counts as proven.

This part gives the SQL register's tables as PostgreSQL 16 DDL, the roles and their GRANTs, the version check with forward-only migrations, and what the schema itself holds of S1's transaction under Q7 (A″). It starts from R3a's sketch (`r3a/REPORT.md:156-283`) and reconciles it with the proposal at `0607526d`, the council's folds (`council-review.md`, `clarifications.md`) and the code at `2a057a6d`, cited as the proposal cites it (`code/<file>:<line>`). It assumes round 1 as answered provisionally, since RULED (#165 6710653292) (4A: each `lanes-edit.sh` write opens its own transaction with its own REGISTER-WRITER credential, and the service keeps one connection for reads) and round 2's recommended options; §1.6 says what changes under the others. Other sections of `design.md` hold the rest: the runbook is part 2's, in `operations.md`, the write path's call sites and the renderer are lane 4's, and the service is lane 5's.

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
                              -- entry's own (a cutover's 1, m+1 or b+1, a rollback's k+1), or an abort's base
  set_by_lane    text        NOT NULL,
  set_by_ws      text        NOT NULL CHECK (set_by_ws <> ''),
  restore_prior  jsonb,                        -- the restore form's cutover: the switch row and
                                               -- lanes/STORE's rows it replaced, for its abort
  CHECK (mode = 'files' OR (import_commit IS NOT NULL AND import_tree IS NOT NULL
                            AND import_seq IS NOT NULL)),
  CHECK ((mode = 'primary') = (marker_state IN ('adding', 'present'))),
  CHECK (marker_state = 'absent' OR marker_seq IS NOT NULL),
  CHECK (marker_state <> 'present' OR marker_commit IS NOT NULL),
  CHECK (restore_prior IS NULL OR marker_state = 'adding'));   -- cleared once confirmed

CREATE TABLE register_head (                   -- S1, S2, S6: its row lock orders every write.
  id  smallint PRIMARY KEY DEFAULT 1 CHECK (id = 1),   -- Never a SEQUENCE or IDENTITY:
  seq bigint   NOT NULL CHECK (seq >= -1));            -- gaps, and not commit order. -1: empty

CREATE TABLE mirror_state (                    -- D16: what the renderer reports; it never gates alone
  id             smallint    PRIMARY KEY DEFAULT 1 CHECK (id = 1),
  diverged_sha   text        CHECK (diverged_sha ~ '^[0-9a-f]{40}$'),   -- a stray commit stopped the renderer,
                                               -- or a last Register-Seq above the head (N6); a rollback
                                               -- entry is refused while it is set (S5, LR012)
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
  act_id      text        CHECK (act_id ~ '^[^@[:space:]]+$'),     -- D5, F5: the id of the act that made
                                                                   -- a write (LANES_ACT); NULL: no act
  CHECK (kind <> 'write' OR args_sha256 IS NOT NULL),
  CONSTRAINT journal_act_id_write CHECK (kind = 'write' OR act_id IS NULL),  -- none on another kind
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

CREATE TABLE journal_scopes (                  -- D5, F5: each entry's touched keys (lane 4's scopes),
  seq   bigint NOT NULL REFERENCES journal (seq) ON DELETE CASCADE,  -- derived by steps 5a and 8a,
  scope text   COLLATE "C" NOT NULL                                   -- never sent by a client
        CHECK (scope = lower(scope) AND (scope = '*'                  -- '*': every scope
               OR scope ~ '^(row|bind|objects|alias|archive):[^[:space:]]+$'
               OR scope ~ '^sid:[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}$')),
  PRIMARY KEY (scope, seq));                   -- step 3b: one index range per listed scope

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
  repo_spellings text[],                       -- D18, a slot line's: as typed, canonical key, the writer's aliases
  CHECK ((slot_role IS NULL) = (slot_kind IS NULL)),
  CHECK (slot_role IS NULL OR repo_key IS NOT NULL),
  CHECK (slot_kind IS DISTINCT FROM 'landing' OR pr IS NOT NULL),   -- RULE6_AWK pairs by PR
  CHECK ((for_lane IS NOT NULL) = (kind IS NOT DISTINCT FROM 'LANDING EXPIRED')),
  CHECK (for_lane IS NULL OR (slot_role = 'close' AND slot_kind = 'landing')),
  CHECK (mode = 'files' OR coalesce(workstation, '') <> ''));
CREATE INDEX register_lines_slots ON register_lines (repo_key, line_id) WHERE slot_role IS NOT NULL;
CREATE INDEX files_last_seq ON files (last_seq);                 -- the seven columns that reference
CREATE INDEX doc_lines_written_seq ON doc_lines (written_seq);   -- journal (seq) and lead no index,
CREATE INDEX lanes_last_seq ON lanes (last_seq);                 -- so an import's deletes do not
CREATE INDEX log_lines_seq ON log_lines (seq);                   -- scan a table per journal row
CREATE INDEX object_holds_seq ON object_holds (seq);
CREATE INDEX register_lines_seq ON register_lines (seq);
CREATE INDEX journal_scopes_seq ON journal_scopes (seq);

CREATE VIEW main_slots AS                      -- A10, Rule 6: the earliest open take wins
  SELECT t.repo_key, t.line_id, t.kind, t.slot_kind, t.lane, t.pr, t.tip, t.utc_ts, t.raw,
         CASE WHEN t.slot_kind = 'landing'
              THEN clock_timestamp() - t.utc_ts > interval '30 minutes' END AS stale,
         rank() OVER (PARTITION BY t.repo_key ORDER BY t.line_id) AS place,
         t.repo_spellings                      -- D18: one repository, however spelled
  FROM register_lines t
  WHERE t.slot_role = 'take'
    AND NOT EXISTS (SELECT 1 FROM register_lines c
                    WHERE c.slot_role = 'close' AND c.slot_kind = t.slot_kind
                      AND (c.repo_key = t.repo_key OR c.repo_spellings && t.repo_spellings)
                      AND lower(coalesce(c.for_lane, c.lane)) = lower(t.lane)
                      AND c.line_id > t.line_id AND (t.slot_kind = 'hold' OR c.pr = t.pr));

CREATE FUNCTION render_file(p_path text) RETURNS bytea LANGUAGE sql STABLE
  SET search_path = lanes_register, pg_catalog, pg_temp AS $f$
  SELECT convert_to(coalesce(string_agg(d.text, E'\n' ORDER BY d.line_no), '')
           || CASE WHEN f.final_newline AND count(d.line_id) > 0 THEN E'\n' ELSE '' END, 'UTF8')
  FROM files f LEFT JOIN doc_lines d ON d.path = f.path
  WHERE f.path = p_path GROUP BY f.final_newline $f$;

CREATE FUNCTION line_scopes(p_path text, p_text text) RETURNS SETOF text LANGUAGE sql IMMUTABLE
  SET search_path = lanes_register, pg_catalog, pg_temp AS $f$
  -- D5, F5: the scopes a line names by its text, by build_records' rules (code/lanes-index:765):
  -- a row by ROW_AWK's (row_name, :648-660), in LANES.md (:831-837) or the archive (:843-847); a
  -- lane-kind line by its verb (parse_log_line, :538-546) in the log that file_lane names
  -- (:530-532); an alias line by its first two tab fields (:849-865); a uuid by UUID_RE (:110).
  -- S3: an archived row also names its normalized key, where that differs from its own, by
  -- RETIRED_ROWS_AWK's key() (code/lanes-edit.sh:3743-3760): the name before its last '-',
  -- lower-cased, '-', and the digits after it, less one trailing letter, without leading zeros.
  WITH l AS (SELECT substring(p_text FROM '^\|[^`]*`([^`]*)`') AS rown,
               p_path ~ '^lanes/log/' AND p_text ~
                 '^[ \t]*(STARTED|RESUMED|PAUSED|ENDED|RETIRED|RENAMED)[ \t]* — ' AS lane_kind,
               lower(regexp_replace(regexp_replace(p_path, '^.*/', ''), '\.[Mm][Dd]$', '')) AS flane),
       a AS (SELECT lower(m[1]) || '-' || coalesce(nullif(ltrim(m[2], '0'), ''), '0') AS akey
             FROM l, regexp_match(rown, '^(.*)-([0-9]+)[A-Za-z]?$') AS m)   -- NULL: no key
  SELECT 'row:' || lower(rown) FROM l WHERE p_path = 'lanes/LANES.md' AND rown <> ''
  UNION SELECT 'archive:' || lower(rown) FROM l WHERE p_path ~ '^lanes/archive/' AND rown <> ''
  UNION SELECT 'archive:' || akey FROM l, a WHERE p_path ~ '^lanes/archive/' AND akey <> lower(rown)
  UNION SELECT 'bind:' || flane FROM l WHERE lane_kind
  UNION SELECT 'sid:' || u.m[1] FROM l, regexp_matches(lower(p_text),
          '([0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12})', 'g') AS u(m)
        WHERE lane_kind OR (rown <> '' AND p_path ~ '^lanes/(LANES\.md|archive/)')
  UNION SELECT 'alias:' || lower(a) FROM l, unnest(ARRAY[btrim(split_part(p_text, E'\t', 1), E' \t'),
          btrim(split_part(p_text, E'\t', 2), E' \t')]) AS a
        WHERE p_path = 'lanes/aliases.tsv' AND p_text !~ '^[ \t]*#' AND a <> '' $f$;

CREATE FUNCTION doc_line_scopes(p_line_id bigint, p_path text) RETURNS SETOF text LANGUAGE sql STABLE
  SET search_path = lanes_register, pg_catalog, pg_temp AS $f$
  -- A stored line's scopes, read at p_path: its text's, and objects: from its structured row: a
  -- readable log line on an object that is not lane-kind (parse_log, :618-645), and a slot
  -- line's lane and a LANDING EXPIRED's for_lane (parse_register_line, :683-713; D18, F13).
  SELECT line_scopes(p_path, d.text) FROM doc_lines d WHERE d.line_id = p_line_id
  UNION SELECT 'objects:' || lower(g.lane) FROM log_lines g
    WHERE g.line_id = p_line_id AND NOT g.unreadable AND g.object NOT LIKE 'lane:%'
  UNION SELECT 'objects:' || lower(x) FROM register_lines r, unnest(ARRAY[r.lane, r.for_lane]) AS x
    WHERE r.line_id = p_line_id AND r.slot_role IS NOT NULL AND x IS NOT NULL $f$;

CREATE FUNCTION entry_new_keys(p_seq bigint) RETURNS TABLE (path text, line_no integer, scope text)
  LANGUAGE sql STABLE SET search_path = lanes_register, pg_catalog, pg_temp AS $f$
  -- Step 8a's new side, each key with the line it came from (N2): every line entry p_seq appended
  -- or replaced, every line a rename moved, read at its new path, and objects: for every lane
  -- holding an object it wrote a hold for, at that lane's last line on the object.
  SELECT d.path, d.line_no, k.scope FROM doc_lines d, doc_line_scopes(d.line_id, d.path) AS k(scope)
    WHERE d.written_seq = p_seq
  UNION SELECT d.path, d.line_no, k.scope FROM journal_edits j JOIN doc_lines d ON d.path = j.to_path,
      doc_line_scopes(d.line_id, d.path) AS k(scope)
    WHERE j.seq = p_seq AND j.op = 'rename'
  UNION SELECT h.file, h.ordinal, 'objects:' || lower(h.lane_key)   -- N1: lower(), as doc_line_scopes;
    FROM object_holds n JOIN object_holds h ON h.object = n.object  -- lane_key is lower case by construction
    WHERE n.seq = p_seq AND n.object NOT LIKE 'lane:%' $f$;

CREATE FUNCTION scope_fault(p_scope text) RETURNS text LANGUAGE sql IMMUTABLE
  SET search_path = lanes_register, pg_catalog, pg_temp AS $f$
  -- N2: NULL where a derived key has a scope's form, journal_scopes' CHECK read with Unicode's
  -- White_Space (the no-break space included; the CHECK's [:space:] under "C" is ASCII's alone);
  -- else the key, each white-space character shown as [U+hhhh].
  SELECT CASE WHEN p_scope ~ '^sid:[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}$'
                OR (p_scope ~ '^(row|bind|objects|alias|archive):.' AND p_scope !~ w.re
                    AND p_scope = lower(p_scope COLLATE "C")) THEN NULL
         ELSE (SELECT string_agg(CASE WHEN c ~ w.re THEN '[U+' || lpad(upper(to_hex(ascii(c))), 4, '0')
                                      || ']' ELSE c END, '' ORDER BY n)
               FROM regexp_split_to_table(p_scope, '') WITH ORDINALITY AS t(c, n)) END
  FROM (VALUES ('[\t\n\v\f\r \u0085\u00a0\u1680\u2000-\u200a\u2028\u2029\u202f\u205f\u3000]')) AS w(re) $f$;

CREATE FUNCTION scope_checked(p_path text, p_line integer, p_scope text) RETURNS text
  LANGUAGE plpgsql IMMUTABLE SET search_path = lanes_register, pg_catalog, pg_temp AS $f$
-- N2: a derived key, returned as it is, or LR010 naming the line it came from.
DECLARE
  v_shown text := scope_fault(p_scope);
BEGIN
  IF v_shown IS NOT NULL THEN
    RAISE EXCEPTION 'key: % line % derives %, which is not a scope (a scope''s name is lower case, not empty, and holds no white space)',
      p_path, p_line, v_shown USING ERRCODE = 'LR010'; END IF;
  RETURN p_scope;
END $f$;
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
| `journal_scopes`, `line_scopes`, `doc_line_scopes`, `entry_new_keys`, `scope_fault`, `scope_checked`; `journal.act_id` | D5 and F5: each entry's touched keys and the act that made it, for step 3b's check (lane 4's row 20); S3's normalized `archive:` key; N2's LR010 | lane 4's AT-F6 (8) and (9); lane 2's plan |
| `files`, `doc_lines` | S2's truth; S5's byte-identical import; S1's mode last written | 1, 7, 11; S1's mode test |
| `lanes` | S3 (`binding`, `next-free`, `who`); S4's copy of `owner`; Amendment 15's one row | 11; S3's table |
| `log_lines`, `object_holds`, `holds`, `stale_claims`, `one_live_claim` | S3 (`who`, `history`); S6's Rule 1; 7(b) | 4, 7, 11, 12; ADV-C10's alias race (`clarifications.md:209-216`) |
| `register_lines`, `main_slots` | S6's `HOLD` and `LANDING` order, P9's report | 4, 11 |
| `render_file` | S2 and S5's proof; `reconcile` (`proposal.md:395-398`) | 1, 7 |

**Where this departs from R3a's sketch, and why.**
- `write_id`, UNIQUE in the journal: ADV-C3 (`proposal.md:253-267`; `clarifications.md:253-266`).
- Seq 0 is the import's baseline (the mirror's seq `m` after a loss, §1.3), the cutover is the next seq, writes follow, and rollback's removal of `lanes/STORE` is the last (`proposal.md:1259-1264`). `lanes/STORE` is not a rendered path. An entry that touches only it is an operator entry (`journal.operator`), and there are exactly two (D24): the cutover's, at seq 1, at `m+1` after a re-import or at `b+1` after a restore, and the rollback's last seq. Both marker commits carry `Register-Seq` (`r3b/REPORT.md:213-219`), the renderer never renders either (S2, `proposal.md:307-309`), and the command that writes the entry pushes its commit with the renderer's identity (D23) and confirms it by Amendment 23's S4 read-back (F2; `operations.md` §2.1). `stray-import` and `stray-revert` are ADV-C2's two ways out of a divergence (`proposal.md:380-389`). Each names the stray commit in `git_commit` and clears `mirror_state.diverged_sha`, and a revert is an entry with no line edits, as D16 has it. The frame's journal entry also lists `mode` (S2); here the mode is a column of the rows an entry writes (`written_mode`, `mode`), not of the entry itself.
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
3. **The commit:** `BEGIN ISOLATION LEVEL SERIALIZABLE; SELECT lanes_register.commit_write(n, write_id, REGISTER_SCHEMA_VERSION, generation, entry); COMMIT;` under `ON_ERROR_STOP`, with `VERBOSITY=verbose` and `SHOW_CONTEXT=never`, so that psql's first line on stderr is `ERROR:  <SQLSTATE>: <message>`. The writer reads every code from that one line, 40001, 55P03, 23505 and the LR codes alike, so it can tell a 40001 from every other exit 3 (`proposal.md:1442-1443`; `clarifications.md:250-252`), and LR009's and LR010's messages come back in the same call (lane 3's choice of lane 4's S2, #165 6711564365). The entry is one `jsonb` literal: the journal fields, the edits, `journal_files`' digests, the structured rows, which name lines by path and `line_no`, and `not_after`, the call's bound (step 9). A write made inside one of lane 4's acts also carries `act`, its base and expectations (step 3b, below). In files mode no entry is made, and `LANES_ACT` and `LANES_EXPECT` are ignored there, as lane 4 has it.

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
                     git_commit, git_tree, repos_blob, args_sha256, act_id)    -- 5. the entry
SELECT v_seq, nullif(v_head, -1), p_write_id, e.*, p_entry -> 'act' ->> 'id'  -- D5's act, or NULL
  FROM jsonb_to_record(p_entry -> 'journal')
  AS e(kind text, verb text, lane text, workstation text, author text, message text,
       git_commit text, git_tree text, repos_blob text, args_sha256 text);
```

Steps 6 to 9 are §1.3.1's SQL:

6. **The edits:** `journal_edits` as sent, then applied in `ord` order: `create`, `append`, `replace` and `delete` (each checking `old_sha256`, the sha256 of the line's UTF-8 text, at its `line_no`), `rename` and `remove`. A delete shifts the later lines, and their `log_lines` and `object_holds` ordinals, in one `UPDATE` each, which `doc_lines_pos` and `log_lines_pos` check at the statement's end. An edit that does not apply raises LR004. So does an operator entry that edits anything but `lanes/STORE`, or nothing, and any other entry that edits `lanes/STORE` (F2, D24).
7. **The structured rows:** each row's `raw` must equal its line's text, or LR004; they are upserted by key, each given its line's `line_id`. `superseded_by` is recomputed for every lane on each object touched (#160's rule, `code/lanes-index:1013-1036`), the stored rows first, so that no transient pair of live claims trips `one_live_claim`, whose 23505 is raised again as LR005. Two rules of the register itself raise LR005 on a write: a claim whose `spellings` overlap another lane's live claim's (D19), and a `HOLD` or `LANDING` line over another lane's open taker in `main_slots` for its repository (D18). One repository spelled two ways is one repository: the two lines' `repo_key`s are equal or their `repo_spellings` overlap, as D19 compares an object's `spellings`, and `main_slots` matches a close to its take the same way.
8. **The proof:** every edited path has a row in the entry's `files`, and each present one's `encode(sha256(render_file(path)), 'hex')` must not be distinct from it, or LR004; then `files.sha256`, `final_newline` and `last_seq` move. The deferred foreign keys check at COMMIT.
9. **The bound (F3):** last, just before it returns, `commit_write` raises LR008 unless `clock_timestamp()` is still before the entry's `not_after`. Every commit call carries it, the snapshot's database clock plus the call's client timer plus 2 s, and an entry without it is LR004. Once a commit call of a write was in doubt, the writer makes its one lookup before it ends with any code but 0 (D8, S1): `find_write(id, n)`, where `n` is the largest `not_after` its calls carried (S2). The function waits until the database clock has passed `n`, then takes the head row's lock and checks the clock again under it, so the wait rests on the server's clock and not on the client's estimate. So an orphaned attempt that had not reached the head row's lock by then can no longer commit after that lookup reports the write absent (lane 4's section, `5f26bf0a`, its ask of lane 1).
   - **Exact complements, on one clock:** step 9 commits only while `clock_timestamp() < not_after`, and `find_write` answers only where `NOT (clock_timestamp() < n)` holds under the head row's lock. An orphan that takes the lock after the lookup has released it reaches step 9 later still, so it raises LR008. One that took the lock first has ended, committed or not, before the lookup reads the journal, which it does at READ COMMITTED, after the lock.
   - **The wait:** before the lock, never while holding it, and at most 3 s: the lookup call's `statement_timeout`, 6000 ms, less its `lock_timeout`, 2000 ms, and 1 s for the lookup itself (the bounds table). A call whose `n` is further ahead is refused at once with LR011 **(new)**, and so is one whose check under the lock fails, as when the clock stepped back. The client makes the call once its own estimate, the snapshot's clock plus `SECONDS` since, puts `n` at most 2 s ahead, so the bound covers that estimate's 1 s grain. Its tries start within the 10 s reconnect window, which starts when that wait of its own ends, and each runs to its own 8 s timer (lane 4's "The in-doubt rule").
   - **The residual:** a server clock that steps back between the lookup's check and an orphan's step 9 still lets that orphan commit after the lookup has reported the write absent.

**The act (D5, F5; lane 4's row 20).** Lane 4's acts take a base with `lanes-edit.sh act-begin`, which prints `<act-id>@<head seq>:<generation>` on primary (lane 4's form, #165 6711545001), and list the scopes each write rests on in `LANES_EXPECT` (lane 4's section, "The acts and their expectations"). The schema keeps what the check needs, and `commit_write` runs the check inside the commit's transaction, where no client can skip it.

- **The entry's `act` key,** on a `write` made inside an act, and on nothing else: `{id, base, generation, expect}`.
  - `id`, required: the act's id, the part of `LANES_ACT` before the `@`, a string with no `@` and no white space. `journal.act_id` stores it as given.
  - `base`, required: the act's base seq, the part between the `@` and the `:`, a JSON number of at least 0.
  - `generation`, required: the register generation that `act-begin` read with its base, in the same snapshot (F10, F18): the part after the `:`. The last bullet of this paragraph says why.
  - `expect`, a list, absent meaning empty: `LANES_EXPECT`'s scopes, each `row:`, `bind:`, `objects:`, `alias:` or `archive:` with a lane, or `sid:` with a uuid, compared in lower case.
  - **Without an act,** an entry has no `act` key and `act_id` is NULL: an import, the two operator entries, `stray-import` and `stray-revert`, which `lanes-index` runs outside any act, and a write run outside any act. NULL is never an act's own, so such an entry counts against every act.
  - **Refusals:** an `act` key on any kind but `write`, or one that is malformed, including a scope of another form, is LR004, before the head's lock. The client checks `LANES_EXPECT` first and refuses a malformed one with 2, so the database's LR004 is a backstop.
  - **Files mode:** both variables are ignored there, and no entry is made. On primary, an act whose base is `files`, begun before its workstation's flip, has no seq for the check, so the client refuses its writes with 2 rather than send them unchecked (confirmed by lane 4's check of v2.4, #165 6711545001).
- **The touched keys,** derived by `commit_write` from the lines the entry changes, never sent, so no client can under-report them.
  - **Step 5a,** before step 6 changes anything, reads the old side: each line a `replace` or `delete` names, found by its path and `old_sha256`, and every line of a path the entry removes or renames.
  - **Step 8a,** after step 8's proof, reads the new side: every line the entry appended or replaced (its `written_seq`), and every line a rename moved, read at its new path. It adds `objects:` for every lane that holds a row on an object the entry wrote a hold for, since a `TAKEOVER` supersedes the others' claims and the taker's next line may restore them.
  - **One line's scopes** come from `doc_line_scopes(line_id, path)`: `line_scopes(path, text)` reads the text, and the line's structured row gives `objects:`.

  | Scope | Derived from | `code/lanes-index` |
  |---|---|---|
  | `row:L` | a `lanes/LANES.md` line that is a row by `ROW_AWK`'s rule: it opens with `\|`, and its first backticked token is the lane | `row_name`, `:648-660`; the rows, `:831-837` |
  | `archive:L` | the same rule, on a line under `lanes/archive/`; and, where it differs, the row's normalized key, by `RETIRED_ROWS_AWK`'s `key()` (S3, below) | `:843-847`; `key()`, `code/lanes-edit.sh:3743-3760` |
  | `bind:L` | a line of `lanes/log/L.md` whose verb, the text before its first ` — ` trimmed of spaces and tabs, is `STARTED`, `RESUMED`, `PAUSED`, `ENDED`, `RETIRED` or `RENAMED`; `L` is the file's name without `.md` | `parse_log_line`, `:538-546`; `file_lane`, `:530-532` |
  | `sid:U` | every uuid by `UUID_RE`, lower-cased, in a row line or a lane-kind line | `UUID_RE`, `:110`; the session cell, `:885-887` |
  | `alias:L` | the first two tab-separated fields of a `lanes/aliases.tsv` line that is not a comment, trimmed | `:849-865` |
  | `objects:L` | the structured rows: a readable `log_lines` row whose object is not `lane:…`, its lane; a `register_lines` slot line (D18), its lane, and a `LANDING EXPIRED`'s `for_lane` (F13); and, in step 8a, every lane holding an object the entry wrote a hold for | `parse_log`, `:618-645`; `parse_register_line`, `:683-713`; #160's `holders_of` rule, `:1013-1036` |

  - **Why the text, for five of the six:** v2.3's structured layer has no rows for `lanes/aliases.tsv`, keeps one `lanes` row per lane, so a lane with a row in both `LANES.md` and the archive has only one, and keys no line by session. Each rule above is one regular expression of the parser's own, and reading the old text in step 5a also covers a deleted line, whose structured row cascades away. `objects:` needs the object field and #160's supersession, so it comes from the rows step 7 wrote, which the register's own reads already rest on (the agreement tests check them).
  - **Over-reporting only refuses:** the text rule does not check that a lane-kind line is readable, and step 8a touches every holder of an object the entry wrote a hold for. An extra key refuses a write that the act then runs again; a missing one would let a stale read through. Lane 3 accepted both over-reports, since an extra key can only cause a refusal (#165 6711233420, point 3).
  - **The normalized `archive:` key (S3; lane 4's check, #165 6711545001):** `retired-identity` matches an archived row by its name in any case, or by `RETIRED_ROWS_AWK`'s `key()` (`code/lanes-edit.sh:3743-3760`, blob `d28ed8f2`). So `line_scopes` gives an archived row a second `archive:` key, its normalized key, where that differs from its own.
    - **The rule, as the awk has it:** `p` is the name after its last `-`, less one trailing letter, and `h` the name before that `-`. A name with no `-`, or whose `p` is not all digits, has no key. Otherwise the key is `h` lower-cased, `-`, and `p` as a number. So `foo-03`, `Foo-3b` and `foo-3` all key to `foo-3`, `foo-033` to `foo-33` and `foo-00` to `foo-0`, and `alpha` and `foo-x3` have none.
    - **The SQL strips `p`'s leading zeros as text,** keeping a lone `0`, so no digit run overflows. `check_lane_name` (`code/lanes-edit.sh:2916-2920`) limits a name's characters, not its length, so it caps no digit run. The awk's `p + 0` is a double. mawk 1.3.4 and BusyBox 1.37's awk agree with the SQL on every run of up to 15 digits. From 16 digits, a run past 2^53 can lose its last digits there (`foo-9007199254740993` keys to `foo-9007199254740992`), and a run of 30 prints with an exponent. Only on such a name can the awk match a row that no SQL key names.
    - **A read lists both forms:** `archive:` with the lower-cased name, and `archive:` with its normalized key, so that it meets every spelling `retired-identity` would match. Lane 4's text owns that read; this part states only the key a write gives.
  - **A key that is not a scope (N2):** wherever steps 5a and 8a derive a key, `scope_checked` refuses one whose name is empty or holds white space with LR010 **(new)**: `key: <path> line <n> derives <key>, which is not a scope (…)`. It names the line the key came from, and shows each white-space character in the key as `[U+hhhh]`.
    - **White space** is Unicode's White_Space, the no-break space included. `journal_scopes`' CHECK reads `[:space:]` under `COLLATE "C"`, which is ASCII's alone, so it takes a key holding a no-break space. The CHECK stays as the backstop.
    - **At a write,** the write is refused and nothing is written. The client exits 2 with the server's message, which S2's `VERBOSITY=verbose` returns in the one call (lane 3's choice, #165 6711564365). It never retries, since the same line derives the same key.
    - **At an import,** which stores only `*`, the import still derives every key its lines and holds give, and checks each in path and line order. A key that is not a scope stops it in its own transaction, before it commits, so nothing is written. `lanes-index import` exits 2 with the same message, so act 3's rehearsal (`operations.md` §2.2, step 8) shows it on a scratch schema, before act 4's import. The pass is the import's one cost: on lane 1's PostgreSQL 16.15, an import of 42,001 lines took 57.7 s with it and 7.7 s without, inside the import's 300 s bound (the bounds table, below), and the rehearsal measures it on the real register.
    - **Why not LR004:** LR004's writer reloads every line, retries and then exits 69, and the same line would only derive the same key again.
  - **An import, a cutover in any form and a rollback** store one key, `*`, which every expectation meets: each replaces or moves what every read rests on. `stray-import` is derived as a write is; `stray-revert` changes no line and touches nothing.
  - **The store:** `journal_scopes (seq, scope)`, with the primary key `(scope, seq)` and `ON DELETE CASCADE` from `journal`, so an import's reset and a cutover's abort take an entry's keys with it, and `journal_scopes_seq` serves that cascade. This part chose the table over an array with a GIN index: the check is a seq range within each listed scope, which a btree on `(scope, seq)` answers directly, and a GIN index cannot also bound the seq.
- **The check, step 3b, S1's step 3.2:** under the head's lock, after step 3's switch and generation checks and 3a, and before the seq is taken. A write with an empty `expect`, or none, skips it.
  1. The act's `generation` must be the switch's, or LR007: the base was read in another database.
  2. The act's `base` must not be past the head, or LR004.
  3. One indexed query finds the first entry after the base, in seq order, that has a key in the list, or `*`, and an `act_id` other than this act's. If there is one, the write is refused with LR009. An act's own entries never refuse it.
- **LR009 (new), "expectation changed":** its message is lane 4's, `expectation: <scope> changed at seq <k> by <lane>/<verb>`: the listed scope (the first listed, where the entry's key is `*`), the entry's seq, and its journal `lane` and `verb`. The client exits 2 with that message, and nothing is written. The commit call runs with `VERBOSITY=verbose` and `SHOW_CONTEXT=never` (step 3), so psql prints `ERROR:  LR009: expectation: …` and the client takes the message from that one line, with no second query (lane 3's choice of lane 4's S2, #165 6711564365).
- **A base taken before a generation change or a cutover:**
  - **The after-loss cutover,** in its re-import form at `m+1` and its restore form at `b+1`, sets a new generation. So every base read before it is refused with LR007, a base from the lost database's tail included, whose seq the new database reuses. The write's own `p_generation` cannot show this, since it is read at the write's own snapshot.
  - **Every cutover form, and every import,** stores `*`. So a base taken before one meets it, and the write is refused with LR009, naming it. The first cutover, at seq 1, follows only files-mode acts, which carry no seq.
  - **A rollback** stores `*` too, and from it until the next cutover every write is refused with LR002 at step 3, before step 3b. The import and the cutover that follow store `*`, so no act's base survives a rollback.
  - **The aborts:** the first and re-import forms' aborts set the switch to `files`, so writes are refused with LR002. The restore form's abort puts back the restored generation and the head `b`. A base read while its cutover stood carries that cutover's generation and is refused with LR007. A base read before the cutover carries the restored generation and is checked as any other.
  - **One gap stays, as v2.3 left it for write ids:** between a restore and its cutover, and after that cutover's abort, the database carries the lost database's generation. So a base above `b`, from the lost tail, is refused only while it is past the head: S1's client check at the snapshot exits 2 before the call, and the commit's LR004 is the backstop. The pause (2.3's step 3) keeps writes out until the cutover's new generation closes it.

All of it is one transaction, so a failure at any step leaves the journal, the line store and the head row as they were. P11's real-engine case (`proposal.md:285-290`) can inject one with no test-only code: an entry whose last structured row breaks a CHECK.

**The kinds.** A rollback starts with `commit_write` and a `rollback` entry (F1, D24). `switch_to_files(p_client, p_cutover_seq, p_lane, p_ws)` is now only the cutover's abort (the adversary seat, `proposal.md:1776-1779`; F2): it takes the same lock, refuses with LR002 unless the switch is `primary` with the marker `adding` at `p_cutover_seq` and the head is there, deletes that entry and its rows, sets the head back to `p_cutover_seq - 1`, and sets the switch to `files` with the marker `absent`. In the restore form, the after-loss cutover from `primary` at `b+1`, it sets nothing to `files`. That cutover records in `register_switch.restore_prior` the switch row and `lanes/STORE`'s `files` row and lines that it replaces, and the abort puts them back, line ids included, so it leaves no seq after `b` and changes nothing else (lane 3's ruling, 6579459518, point 1). `commit_write` refuses with LR004 an operator entry that writes anything outside `lanes/STORE`: an edit, at step 6, and, before the head's lock, a `files` entry for another path or a structured row (lane 3's ruling 1, 6710770304). So no such entry reaches the journal, and none leaves a row naming its seq for an abort to fail on with 23503. The caller runs it only after a read-back proves the entry's commit, the one carrying its `Register-Seq`, absent from origin. `record_marker(p_client, p_seq, p_commit, p_ws)` records S4's read-back of an operator entry's push (F12): `adding` becomes `present`, `removing` becomes `absent`, and `marker_commit` the confirmed commit. `record_mirror_state(p_ws, p_diverged_sha, p_error)`, `SECURITY DEFINER` like the others, is the renderer's one write: it records a divergence or a failed pass in `mirror_state` (D16), keeping the first divergence found and never clearing one, and `stray-import` and `stray-revert` clear the divergence through `commit_write`.

**The switch's states (F1).** `mode` stays the one write gate, and `marker_state` says where the marker stands, so a rollback's state is never taken for "never cut over":

| `mode`, `marker_state` | Means | Set by |
|---|---|---|
| `files`, `absent` | before any cutover; after an aborted one; after a confirmed rollback | the bootstrap; `switch_to_files`; `record_marker` after a rollback |
| `primary`, `adding` | the cutover's entry written, its push not yet confirmed: no write commits | `commit_write`, kind `cutover` |
| `primary`, `present` | the cutover confirmed: writes commit | `record_marker` after a cutover; `switch_to_files` after the restore form's cutover, which it undoes |
| `files`, `removing` | the rollback's entry written, the marker's removal not yet confirmed | `commit_write`, kind `rollback` |

**`set_seq`, lane 3's open point 23:** after a cutover it is the cutover's own seq, 1, `m+1` after a re-import, or `b+1` after a restore. After a rollback it is the rollback's own seq, `k+1`, and after an abort the abort's base, the import's seq, except after the restore form's abort, which puts back the value it found.

| `kind` | The switch must be (mode, marker), and what it leaves | What else the function does |
|---|---|---|
| `write` | `primary`, `present` | the verb's edits; `written_mode` and `mode` are `primary` |
| `import` | `files`, `absent` | With only its root in the journal, it deletes every journal and data row (head to -1) and takes seq 0 (under `--after-loss`, D22 and `operations.md` §2.3, the baseline seq `m` that the entry names in `root_seq`: the head moves from -1 to `m`, the chain's one root sits at `m`, and a `root_seq` on a journal that is not empty is LR004), so each re-import before a confirmed cutover replaces the root (`proposal.md:1259-1261`; `switch_to_files` deletes an aborted cutover's entry); otherwise it clears the data tables and takes the next seq, so a seq never repeats in the mirror's history and a later cutover never reuses a `Register-Seq` that `main` carries. Every line is a `create` or `append` edit, so the journal alone renders any seq from the import on. It never loads `lanes/STORE`, which is not a rendered path (F2). Sets `exempt`. |
| `cutover` (operator, D24) | `files`, `absent`; leaves `primary`, `adding` | base must be the latest import's seq; sets the switch's fields from it (`import_seq`, `import_commit`, `import_tree`) and `set_seq` to its own seq, 1, `m+1` after a re-import, or `b+1` after a restore; creates `lanes/STORE`'s lines, D21's fields: `store=primary`, `database=` the label, `import=` the commit, `lanes-tree=` the tree id and `utc=`. Its after-loss form, `lanes-index cutover --import <M> --after-loss` (`after_loss` in the entry; lane 3's F18 ruling), is this same entry at `m+1`, not a third operator entry; it also sets `database_label` from the entry and a new `generation`. After a restore, whose switch reads `primary`, `present`, it starts from there, with the head as its base, records in `restore_prior` what it replaces, for its abort, and replaces `lanes/STORE`'s lines whole (`operations.md` §2.3; lane 3's ruling, 6579459518, point 1). **N4:** an after-loss cutover with no `database_label` is LR004 in either form. One that meets the switch at `primary` is LR004 unless the entry's `database_label` `IS DISTINCT FROM` the switch's: the restore form, and either form run by mistake against the live database, which is at `primary`, `present` with its own label. Neither form can find a NULL label in the switch, whose column is NOT NULL (§1.2), a restored backup's included. After a re-import the switch is at `files` and holds the replacement's own label, which its bootstrap wrote and the entry names, so the label is not compared there. `lanes-index cutover` exits 2 with the message, writing nothing |
| `stray-import` | `primary`, `present` | the stray commit's edits as one entry, `written_mode` `files` |
| `stray-revert` | `primary`, `present` | no line edits; `journal_files` names the paths the renderer restores. A rollback refuses to start while a divergence is recorded (F1), so a revert is never needed at `files` |
| `rollback` (operator, D24) | `primary`, `present`; leaves `files`, `removing` | removes `lanes/STORE`; the journal's last seq, written in the transaction that sets the switch to `files`, with `set_seq` its own seq (F1); refused with LR012 **(new)** while `mirror_state` records a divergence, which it reads `FOR SHARE` under the head row's lock (S5) |

**The rollback's divergence gate (S5).**

- **The refusal:** in step 3a of a `rollback` entry, under the head row's lock, `commit_write` reads `mirror_state` `FOR SHARE` and refuses with LR012 while `diverged_sha` is set. `lanes-index rollback` prints the refusal with `diverged at <sha>` and S2's two ways out, exits 2 and changes nothing, as its check before it starts does (`operations.md` §2.3).
- **`record_mirror_state`'s lock:** its `UPDATE` takes the row's `FOR NO KEY UPDATE` lock, which conflicts with `FOR SHARE`, and that is enough. It reads nothing before it writes, since each right-hand side reads the row it has locked, so an explicit `SELECT … FOR UPDATE` would add only a lock it already takes. It waits for a rollback that holds `FOR SHARE` to end, and a rollback that reaches the row while its `UPDATE` is open waits for it.
- **The lock order:** `commit_write` takes the head row, then `mirror_state`: a rollback's `FOR SHARE`, or a stray entry's `UPDATE`. `record_mirror_state` takes `mirror_state` alone, in a transaction of its own. `switch_to_files`, `record_marker` and `find_write` take the head row alone. No function holds `mirror_state` and then waits for the head row, so no two of them can wait on each other in a cycle.
- **Under SERIALIZABLE:** where `record_mirror_state` commits a divergence after the rollback's snapshot, whether the rollback is waiting on the row or has not reached it, the `FOR SHARE` raises 40001. `rollback` treats a 40001 or 40P01 as a writer does: it runs its checks again and makes the call again (`operations.md` §2.3). The divergence is committed by then, so the retry is refused with 2, by its check or by LR012.

**The signals a writer gets.** The retry loop is the writer's; the schema fixes what each signal means. The busy code is 75 (D7), sysexits' `EX_TEMPFAIL`, the product seat's suggestion (`clarifications.md:360-373`; `proposal.md:1706-1708`); Brett's answer to Q7 gives "register busy" this code of its own, so "(A″) with 69" is not taken (#165 6710653292). The deadline is 30 s (D7), lane 2's example (`proposal.md:1470-1471`), and needs no pause between tries: with no cap, the probes' k-th writer committed on its k-th attempt (`clarifications.md:323-327`). Tests 4 and 9 confirm both.

| Signal | Means | The writer |
|---|---|---|
| 40001, 40P01, LR001 | a rival committed after the snapshot, or the head moved. An earlier attempt of this write that commits while this commit's transaction is open, waiting for the head row's lock or not, gives 40001 at the lock, not LR006 (lane 2, 6708917587) | re-reads and re-runs the verb until the deadline, then busy; a re-read that finds this write's id exits 0 |
| 55P03 | the head row's lock, not granted within `lock_timeout`, as when an earlier attempt of this write holds it (lane 2, 6708917587) | as 40001 until the deadline; a write any of whose commit calls was in doubt then makes F3's one lookup (below), and exits 75 only where that finds the write absent with the head row free |
| LR006 | this write is already in the journal: an earlier attempt committed after this attempt's snapshot read and before its commit transaction began, and step 1 finds its `write_id` under the head's lock (lane 2, 6708917587) | reads `journal` by `write_id`, exits 0 |
| LR008 | this attempt reached step 9 past its `not_after`: it wrote nothing, and the write was not in the journal while it held the head's lock | lane 4's: the attempt is absent (F3) |
| LR005 | a rule of the register: an object is held (`one_live_claim`'s 23505, raised again, or D19), or `main` is held (D18) | exits 2 with the holder or the earlier line, writing nothing (Q12 (a); S1's outcomes) |
| LR009 (new), "expectation changed" | an entry after the act's base, made outside the act, touched a scope the write lists (step 3b; D5, F5) | exits 2 with lane 4's `expectation: <scope> changed at seq <k> by <lane>/<verb>`, writing nothing |
| LR010 (new), "key not well formed" | a key that a line derives, in step 5a, step 8a or an import's check, is not a scope: its name is empty or holds white space (N2) | exits 2 with the server's message, `key: <path> line <n> derives <key>, which is not a scope (…)`, writing nothing, and never retries; `lanes-index import` exits 2 the same way |
| LR011 (new), "lookup too early" | the in-doubt lookup's `n` is more than 3 s ahead of the database clock, or the clock had not passed `n` when checked under the head row's lock, as after the clock stepped back (S2) | the lookup proves nothing: exits 3 naming the id, with the server's message, and keeps its pending record (F3's "otherwise 3"); a rerun under the same id looks again |
| LR012 (new), "mirror diverged" | a `rollback` entry while `mirror_state` records a divergence, read `FOR SHARE` under the head row's lock (S5) | `lanes-index rollback` exits 2 with `diverged at <sha>` and S2's two ways out, as its check before it starts does (`operations.md` §2.3), writing nothing |
| LR002 | the switch, or its marker, is not what the kind needs (the kinds table) | 69, with S1's text (`proposal.md:169-171`) |
| LR003 | the client is outside the schema's window (§1.5) | 69 |
| LR007 | the write id belongs to another write: minted against another generation of the register (F10, F18), or in the journal for another lane, verb or arguments (F10). In step 3b, an act whose base was read in another generation | exits 2, naming the id and what differs: check the mirror for the write before running it again without `LANES_WRITE_ID`; nothing written. For an act, exits 2 naming it, and the act runs again |
| LR004 | the edits do not apply, or the proof differs | reloads every line once and retries; then 69 |
| any other SQLSTATE | the database refused the transaction | 69, naming the store and the error (`proposal.md:213-217`) |
| `psql` exit 2, or 124 from the client timer, on the commit call | the outcome is unknown | in doubt: reconnects, reads `journal` by `write_id` (`proposal.md:253-267`), and retries under the same id (D8). From then on it ends with no code but 0 until it has made one lookup (F3; D8, S1), `find_write(id, n)` with `n` the largest `not_after` its calls carried, at READ COMMITTED in a transaction it rolls back, its tries starting within the 10 s reconnect window, which starts when its own wait for `n` ends, each running to its own 8 s timer (lane 4's "The in-doubt rule"); the function waits until the database clock has passed `n` and checks it again under the head row's lock (step 9; S2): found is 0, and the verb's after-COMMIT tail runs once (lane 4's "The tail, once"); absent, with the head row's lock granted, is the code its last attempt earned (75, 69, 2 or the verb's own); otherwise, 55P03, LR011 or no answer, it exits 3 naming the id and keeps its pending record |

The writer reads only the SQLSTATE from psql's `ERROR:` line (step 3), never the constraint's name the message may carry, so no signal rests on one. With the head updated before the journal row, a lost race is never a 23505 on `journal_pkey`. Nor does a retry reach `journal_write_id_key`'s 23505: step 1's lookup under the head row's lock finds an earlier attempt that committed before this transaction began, and one that commits while it is open fails this transaction at the lock with SERIALIZABLE's 40001, or with 55P03 once `lock_timeout` passes (lane 2, 6708917587). Either 23505 would be a bug, and is 69. `LANES_NO_FETCH=1` cannot weaken the switch check, because it is read inside the transaction, not fetched (`proposal.md:571-575`). The in-doubt report is exit 3, naming the write id, and a rerun reuses the id (D8; `proposal.md:1725` left the code open).

**Bounds, set when connecting through `options=`** (`clarifications.md:296-302`), are D9's, in the one table under "Roles and credentials" ("The timeouts (D9)"), which now carries this part's `import` row and gives `reconcile` the renderer's values (lane 3's ruling, #165 6578020193). The guard's are measured, and so are the rows for `cutover`, `switch_to_files`, `record_marker` and the commit call of `import --single` and `render --revert` that R2's N1 added (the paragraph under D9's table). The others are the design's, for tests 4 and 9 to confirm, and R1's 14.7 ms per call (`proposal.md:1219-1220`) leaves them wide.

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
--   not_after      required: the call's bound (F3), the snapshot's database clock plus the
--                  call's client timer plus 2 s; an attempt that ends past it is LR008
--   act            a write's, made inside one of lane 4's acts (D5, F5); else absent:
--                  {id, base, generation, expect}: LANES_ACT's id (journal.act_id) and base
--                  seq, the generation its base was read in, and LANES_EXPECT's scopes, a
--                  list that may be empty (step 3b). Files mode ignores both variables.
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
--                    repo_spellings, pr, tip, utc, utc_ts, session, workstation}]
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
  v_prior jsonb;                                -- the restore form's: what its cutover replaces
  v_line  doc_lines;
  ed      journal_edits;
  r       record;
  v_n     integer;
  v_sha   text;
  v_con   text;
  v_det   text;
  v_not_after timestamptz;                      -- F3: the call's bound (step 9)
  v_act   jsonb := p_entry -> 'act';            -- D5, F5: the act, or NULL (step 3b)
  v_expect text[];                              -- its scopes, lower case, as listed
  v_hit   record;                               -- the entry that changed one (LR009)
  v_mir   mirror_state;                         -- S5: a rollback's divergence gate (LR012)
BEGIN
  IF jsonb_typeof(p_entry -> 'journal') IS DISTINCT FROM 'object' THEN    -- the entry's shape
    RAISE EXCEPTION 'the entry has no journal object' USING ERRCODE = 'LR004'; END IF;
  IF EXISTS (SELECT 1 FROM jsonb_each(p_entry -> 'journal') k
             WHERE jsonb_typeof(k.value) NOT IN ('string', 'null'))
     OR EXISTS (SELECT 1 FROM unnest(ARRAY['edits', 'files', 'lanes', 'log_lines',
                                           'register_lines', 'object_holds']) k
                WHERE p_entry ? k AND jsonb_typeof(p_entry -> k) <> 'array')
     OR jsonb_typeof(p_entry -> 'not_after') IS DISTINCT FROM 'string' THEN
    RAISE EXCEPTION 'the entry is not well formed' USING ERRCODE = 'LR004'; END IF;
  v_not_after := (p_entry ->> 'not_after')::timestamptz;
  v_needs := CASE v_kind WHEN 'write' THEN 'primary' WHEN 'stray-import' THEN 'primary'
                         WHEN 'stray-revert' THEN 'primary' WHEN 'rollback' THEN 'primary'
                         WHEN 'import' THEN 'files' WHEN 'cutover' THEN 'files' END;
  IF v_needs IS NULL THEN
    RAISE EXCEPTION 'unknown kind %', v_kind USING ERRCODE = 'LR004'; END IF;
  v_wmode := CASE WHEN v_kind IN ('write', 'cutover') THEN 'primary' ELSE 'files' END;
  IF v_kind IN ('cutover', 'rollback') AND (      -- ruling 1: an operator entry writes lanes/STORE
       EXISTS (SELECT 1 FROM jsonb_array_elements(coalesce(p_entry -> 'files', '[]')) f   -- alone:
               WHERE f ->> 'path' IS DISTINCT FROM 'lanes/STORE')     -- no other files row and
       OR EXISTS (SELECT 1 FROM unnest(ARRAY['lanes', 'log_lines', 'register_lines',  -- no row
                                             'object_holds']) k
                  WHERE jsonb_array_length(coalesce(p_entry -> k, '[]')) > 0)) THEN
    RAISE EXCEPTION 'a % entry writes outside lanes/STORE (ruling 1)', v_kind
      USING ERRCODE = 'LR004'; END IF;
  IF v_act IS NOT NULL THEN                     -- the act's shape (D5, F5): a write's alone
    IF v_kind <> 'write' OR jsonb_typeof(v_act) <> 'object'
       OR jsonb_typeof(v_act -> 'id') IS DISTINCT FROM 'string'
       OR jsonb_typeof(v_act -> 'base') IS DISTINCT FROM 'number'
       OR jsonb_typeof(v_act -> 'generation') IS DISTINCT FROM 'string'
       OR jsonb_typeof(coalesce(v_act -> 'expect', '[]')) <> 'array' THEN
      RAISE EXCEPTION 'the entry''s act is not well formed' USING ERRCODE = 'LR004'; END IF;
    IF v_act ->> 'id' !~ '^[^@[:space:]]+$' OR v_act ->> 'base' !~ '^[0-9]+$'
       OR lower(v_act ->> 'generation')
          !~ '^[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}$'
       OR EXISTS (SELECT 1 FROM jsonb_array_elements(coalesce(v_act -> 'expect', '[]')) x
                  WHERE jsonb_typeof(x) <> 'string'
                     OR lower(x #>> '{}') !~ ('^((row|bind|objects|alias|archive):[^[:space:]]+'
                          || '|sid:[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12})$')) THEN
      RAISE EXCEPTION 'the entry''s act is not well formed' USING ERRCODE = 'LR004'; END IF;
    v_expect := ARRAY(SELECT lower(t.x) FROM jsonb_array_elements_text(coalesce(v_act -> 'expect',
                        '[]')) WITH ORDINALITY AS t(x, n) ORDER BY t.n);
  END IF;

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
  IF v_kind = 'cutover' AND v_after_loss                 -- N4: an after-loss form names its database
     AND p_entry -> 'switch' ->> 'database_label' IS NULL THEN
    RAISE EXCEPTION 'an after-loss cutover with no database_label (N4)' USING ERRCODE = 'LR004'; END IF;
  IF v_kind = 'rollback' THEN             -- S5: never over a recorded divergence (F1); FOR SHARE, so
    SELECT * INTO v_mir FROM mirror_state WHERE id = 1 FOR SHARE;   -- record_mirror_state's UPDATE
    IF v_mir.diverged_sha IS NOT NULL THEN                          -- waits, or this read waits for it
      RAISE EXCEPTION 'rollback refused: diverged at %, found % by %; resolve it first with import --single or render --revert (F1, S5)',
        v_mir.diverged_sha, v_mir.found_utc, v_mir.found_by_ws USING ERRCODE = 'LR012'; END IF;
  END IF;
  IF v_kind = 'cutover' AND NOT (v_after_loss AND v_sw.mode = 'primary') THEN
    SELECT * INTO v_imp FROM journal WHERE kind = 'import' ORDER BY seq DESC LIMIT 1;
    IF v_imp.seq IS DISTINCT FROM p_base THEN
      RAISE EXCEPTION 'cutover base %, but the latest import is %', p_base, v_imp.seq
        USING ERRCODE = 'LR004'; END IF;
  ELSIF v_kind = 'cutover' THEN           -- the restore form: what it replaces, for its abort
    IF NOT (p_entry -> 'switch' ->> 'database_label' IS DISTINCT FROM v_sw.database_label) THEN
      RAISE EXCEPTION 'after-loss cutover: database % is the switch''s own label, not a replacement''s (N4)',
        v_sw.database_label USING ERRCODE = 'LR004'; END IF;     -- N4: the live database, by mistake
    v_prior := jsonb_build_object('switch', to_jsonb(v_sw),
      'file', (SELECT to_jsonb(f) FROM files f WHERE f.path = 'lanes/STORE'),
      'lines', (SELECT jsonb_agg(to_jsonb(d) ORDER BY d.line_no) FROM doc_lines d
                WHERE d.path = 'lanes/STORE'));
  END IF;

  -- 3b. The act's expectations (S1's step 3.2; D5, F5), under the head's lock and
  -- before the seq is taken: refused when an entry after the act's base, made outside this act,
  -- touched a listed scope. Each entry's scopes are its steps 5a and 8a's, stored at its commit.
  IF cardinality(v_expect) > 0 THEN             -- no expectations: no check
    IF lower(v_act ->> 'generation')::uuid IS DISTINCT FROM v_sw.generation THEN
      RAISE EXCEPTION 'act % has its base in register generation %, not %', v_act ->> 'id',
        v_act ->> 'generation', v_sw.generation USING ERRCODE = 'LR007'; END IF;
    IF (v_act ->> 'base')::bigint > v_head THEN
      RAISE EXCEPTION 'act % has its base at %, past the head %', v_act ->> 'id',
        v_act ->> 'base', v_head USING ERRCODE = 'LR004'; END IF;
    SELECT s.scope, s.seq, j.lane, j.verb INTO v_hit    -- one indexed query, on (scope, seq)
      FROM journal_scopes s JOIN journal j ON j.seq = s.seq
      WHERE s.scope = ANY (v_expect || '*'::text) AND s.seq > (v_act ->> 'base')::bigint
        AND j.act_id IS DISTINCT FROM v_act ->> 'id'      -- the act's own entries never refuse it
      ORDER BY s.seq, s.scope LIMIT 1;
    IF FOUND THEN
      RAISE EXCEPTION 'expectation: % changed at seq % by %/%',
        CASE v_hit.scope WHEN '*' THEN v_expect[1] ELSE v_hit.scope END, v_hit.seq, v_hit.lane,
        v_hit.verb USING ERRCODE = 'LR009'; END IF;
  END IF;

UPDATE register_head SET seq = CASE WHEN v_head = -1                          -- 4. advance; the empty journal's
    THEN coalesce((p_entry ->> 'root_seq')::bigint, 0) ELSE seq + 1 END       -- one entry is the root, at 0 or `m`
  WHERE id = 1 RETURNING seq INTO v_seq;
INSERT INTO journal (seq, base_seq, write_id, kind, verb, lane, workstation, author, message,
                     git_commit, git_tree, repos_blob, args_sha256, act_id)    -- 5. the entry
SELECT v_seq, nullif(v_head, -1), p_write_id, e.*, p_entry -> 'act' ->> 'id'  -- D5's act, or NULL
  FROM jsonb_to_record(p_entry -> 'journal')
  AS e(kind text, verb text, lane text, workstation text, author text, message text,
       git_commit text, git_tree text, repos_blob text, args_sha256 text);

  -- 5a. The touched keys' old side (D5, F5): what each line this entry replaces or deletes
  -- (found by path and old_sha256), and every line of a path it removes or renames, said
  -- before step 6 changes it.
  IF v_kind IN ('write', 'stray-import') THEN
    INSERT INTO journal_scopes (seq, scope)
    SELECT DISTINCT v_seq, scope_checked(d.path, d.line_no, k.scope)   -- N2: LR010, not 23514
    FROM jsonb_to_recordset(coalesce(p_entry -> 'edits', '[]')) AS x(op text, path text,
         old_sha256 text)
    JOIN doc_lines d ON d.path = x.path AND (x.op IN ('remove', 'rename')
         OR (x.op IN ('replace', 'delete')
             AND encode(sha256(convert_to(d.text, 'UTF8')), 'hex') = x.old_sha256))
    CROSS JOIN LATERAL doc_line_scopes(d.line_id, d.path) AS k(scope);
  END IF;

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
                              utc, utc_ts, session, for_lane, raw, seq, mode, workstation,
                              repo_spellings)
  SELECT d.line_id, x.kind, x.slot_role, x.slot_kind, x.lane, x.repo, x.repo_key, x.pr, x.tip,
         x.utc, x.utc_ts, x.session, x.for_lane, x.raw, v_seq, v_wmode, x.workstation,
         x.repo_spellings
  FROM jsonb_to_recordset(coalesce(p_entry -> 'register_lines', '[]')) AS x(path text,
       line_no integer, raw text, kind text, slot_role text, slot_kind text, lane text,
       repo text, repo_key text, pr integer, tip text, utc text, utc_ts timestamptz,
       session text, for_lane text, workstation text, repo_spellings text[])
  JOIN doc_lines d ON d.path = x.path AND d.line_no = x.line_no
  ON CONFLICT (line_id) DO UPDATE SET kind = EXCLUDED.kind, slot_role = EXCLUDED.slot_role,
    slot_kind = EXCLUDED.slot_kind, lane = EXCLUDED.lane, repo = EXCLUDED.repo,
    repo_key = EXCLUDED.repo_key, pr = EXCLUDED.pr, tip = EXCLUDED.tip, utc = EXCLUDED.utc,
    utc_ts = EXCLUDED.utc_ts, session = EXCLUDED.session, for_lane = EXCLUDED.for_lane,
    raw = EXCLUDED.raw, seq = EXCLUDED.seq, mode = EXCLUDED.mode,
    workstation = EXCLUDED.workstation, repo_spellings = EXCLUDED.repo_spellings;
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
    IF EXISTS (SELECT 1 FROM register_lines n JOIN main_slots m      -- one repository, however
                 ON m.repo_key = n.repo_key OR m.repo_spellings && n.repo_spellings   -- spelled
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

  -- 8a. The touched keys' new side (D5, F5), from what the entry wrote, never from a list it
  -- sends: each line it appended or replaced, each line a rename moved, read at its new path,
  -- and objects: for every lane holding an object it wrote a hold for, since a TAKEOVER
  -- supersedes the others and the taker's next line may restore them (entry_new_keys). An
  -- import, a cutover and a rollback move what every read rests on: their one key is '*', which
  -- every scope meets. N2: each key is checked before it is stored, in path and line order, and
  -- an import checks every key its lines and holds derive, though it stores only '*'.
  IF v_kind IN ('import', 'cutover', 'rollback') THEN
    INSERT INTO journal_scopes (seq, scope) VALUES (v_seq, '*');
  END IF;
  IF v_kind = 'import' THEN
    PERFORM count(scope_checked(k.path, k.line_no, k.scope))
      FROM (SELECT * FROM entry_new_keys(v_seq) ORDER BY 1, 2, 3) AS k;
  ELSIF v_kind NOT IN ('cutover', 'rollback') THEN
    INSERT INTO journal_scopes (seq, scope)
    SELECT v_seq, scope_checked(k.path, k.line_no, k.scope)
      FROM (SELECT * FROM entry_new_keys(v_seq) ORDER BY 1, 2, 3) AS k
    ON CONFLICT DO NOTHING;
  END IF;

  -- The kinds table's rules that come after: D24's two operator entries move the switch.
  IF v_kind = 'cutover' THEN                    -- set_seq: its own seq, 1, m+1 after a re-import, or b+1 after a restore
    UPDATE register_switch SET mode = 'primary', marker_state = 'adding', marker_seq = v_seq,
      import_seq = coalesce(v_imp.seq, import_seq),             -- a restore's has no import entry
      import_commit = coalesce(v_imp.git_commit, import_commit),
      import_tree = coalesce(v_imp.git_tree, import_tree), set_seq = v_seq,
      database_label = CASE WHEN v_after_loss THEN p_entry -> 'switch' ->> 'database_label'
                            ELSE database_label END,
      generation = CASE WHEN v_after_loss THEN gen_random_uuid() ELSE generation END,
      set_utc = date_trunc('second', clock_timestamp()),
      set_by_lane = p_entry -> 'journal' ->> 'lane', set_by_ws = v_ws,
      restore_prior = v_prior                   -- NULL but in the restore form
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
  IF NOT (clock_timestamp() < v_not_after) THEN  -- 9. the bound (F3): past it, nothing commits
    RAISE EXCEPTION 'write % reached its end at %, past its not_after %', p_write_id,
      clock_timestamp(), v_not_after USING ERRCODE = 'LR008'; END IF;
  RETURN v_seq;                                 -- the deferred foreign keys check at COMMIT
END $f$;

CREATE FUNCTION switch_to_files(p_client integer, p_cutover_seq bigint, p_lane text, p_ws text)
  RETURNS bigint LANGUAGE plpgsql SECURITY DEFINER
  SET search_path = lanes_register, pg_catalog, pg_temp AS $f$
-- The cutover's abort (F2): run only after a read-back proves the entry's commit absent from
-- origin. In the restore form it puts back what restore_prior holds and sets nothing to files.
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
  IF v_sw.restore_prior IS NOT NULL THEN                      -- the restore form: lanes/STORE
    INSERT INTO files                                         -- as it was at b, line ids too
      SELECT * FROM jsonb_populate_record(NULL::files, v_sw.restore_prior -> 'file');
    INSERT INTO doc_lines OVERRIDING SYSTEM VALUE
      SELECT * FROM jsonb_populate_recordset(NULL::doc_lines, v_sw.restore_prior -> 'lines');
  END IF;
  DELETE FROM journal_files WHERE seq = p_cutover_seq;
  DELETE FROM journal_edits WHERE seq = p_cutover_seq;
  DELETE FROM journal       WHERE seq = p_cutover_seq;
  UPDATE register_head SET seq = p_cutover_seq - 1 WHERE id = 1;   -- a new row version: every
                                                                    -- older writer snapshot gets 40001
  IF v_sw.restore_prior IS NOT NULL THEN                      -- and the switch row as it was:
    DELETE FROM register_switch WHERE id = 1;                 -- primary, present, its label and
    INSERT INTO register_switch                               -- generation; no seq after b
      SELECT * FROM jsonb_populate_record(NULL::register_switch, v_sw.restore_prior -> 'switch');
    RETURN p_cutover_seq - 1;
  END IF;
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
    set_utc = date_trunc('second', clock_timestamp()), set_by_ws = p_ws,
    restore_prior = NULL                       -- confirmed: nothing left to put back
  WHERE id = 1;
  RETURN v_new;
END $f$;

CREATE FUNCTION find_write(p_write_id uuid, p_not_after timestamptz)
  RETURNS bigint LANGUAGE plpgsql SECURITY DEFINER
  SET search_path = lanes_register, pg_catalog, pg_temp AS $f$
-- F3's one lookup (S2; D8, S1), at READ COMMITTED in a transaction the caller rolls back.
-- p_not_after is the largest not_after the write's commit calls carried. It waits, holding no
-- lock, until the server's clock has passed p_not_after, at most 3 s (the call's statement_timeout
-- less its lock_timeout and 1 s); then it takes the head row's lock under the caller's
-- lock_timeout (55P03 while a transaction of the write may still be open), checks the clock again
-- under it, the exact complement of commit_write's step 9, and returns the write's seq, or NULL if
-- it is absent. The writer has no UPDATE, so it cannot take that lock itself.
DECLARE
  v_wait interval := p_not_after - clock_timestamp();
BEGIN
  IF p_not_after IS NULL THEN
    RAISE EXCEPTION 'lookup of write %: no not_after', p_write_id USING ERRCODE = 'LR011'; END IF;
  IF v_wait > interval '3 seconds' THEN                 -- LR011: further ahead than the wait's bound
    RAISE EXCEPTION 'lookup of write %: not_after % is % ahead of the database clock, past the 3 s wait',
      p_write_id, p_not_after, v_wait USING ERRCODE = 'LR011'; END IF;
  IF v_wait > interval '0' THEN                         -- the wait, on the server's clock, before the lock
    PERFORM pg_sleep(extract(epoch FROM v_wait) + 0.001); END IF;
  PERFORM 1 FROM register_head WHERE id = 1 FOR UPDATE;
  IF clock_timestamp() < p_not_after THEN               -- step 9 commits only while this holds
    RAISE EXCEPTION 'lookup of write %: the database clock % has not passed not_after % under the head row''s lock',
      p_write_id, clock_timestamp(), p_not_after USING ERRCODE = 'LR011'; END IF;
  RETURN (SELECT seq FROM journal WHERE write_id = p_write_id);
END $f$;

CREATE FUNCTION record_mirror_state(p_ws text, p_diverged_sha text, p_error text)
  RETURNS void LANGUAGE plpgsql SECURITY DEFINER
  SET search_path = lanes_register, pg_catalog, pg_temp AS $f$
-- S5: its UPDATE takes the row's FOR NO KEY UPDATE lock, which conflicts with a rollback's FOR
-- SHARE, so it waits for that rollback to end, and a rollback that reaches the row after it waits
-- for it. It reads nothing before it writes and takes no other lock, so no explicit lock is needed.
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
                          lanes_register.find_write(uuid, timestamptz),
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
| REGISTER-WRITER | `lanes_register_writer` | `lanes-edit.sh`'s writes, through `lanes-index register snapshot` and `register commit` (4A, D2); the projection writer's storage-layer entry (S4, `proposal.md:501-506`); the renderer, for its `mirror_state` writes (D16); `lanes-index` import, cutover (with its abort, `switch_to_files`, and `record_marker`), rollback and the two ways out of a divergence; `outage declare`'s database probe (D9; lane 5, #165 6714373660) | `register_url=` and `register_passfile=` in `lanes-index.conf` (`proposal.md:152-156`) |
| READER | `lanes_register_reader` | the service's one connection (4A); the guard's direct read (Q11 (a)); S3's readers, the listings and the `SessionStart` block; `status`, `reconcile` and the readiness check | `reader_url=` and `reader_passfile=` (`code/lanes-index:64-65`) |
| indexer | `lanes_indexer` | `lanes-index sync` and `reconcile` of the index half | `url=` and `passfile=` |
| owner | `lanes_register_owner` | nothing: the admin connection takes it by `SET ROLE` | none |
| admin | Brett's | the bootstrap and its migrations | a libpq service and 0600 `~/.pgpass` of his own, named by `--admin-service`; `psql` receives the service's name only as `PGSERVICE=<service>` in its environment (SVC-2; lane 5, 6579523948); never a password or a connection string in an argument or the environment, never stored by the tools |

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
| `import_tree`, `set_seq` | columns | **(new)**; `import_tree` is the tree id (`r3b/REPORT.md:214`), the marker's `lanes-tree=` (D21); `set_seq` is the seq of the switch's last change: an operator entry's own (a cutover's 1, `m+1` or `b+1`, a rollback's `k+1`), or an abort's base (S5; open point 23) |
| `marker_state`, `marker_seq`, `marker_commit` | columns | **(new)**; the marker as SQL knows it (F1, F2, F12) |
| `generation` | column | **(new)**; F10, F18 |
| `restore_prior` | column | **(new)**; what the restore form's cutover replaced, which its abort puts back (lane 3's ruling, 6579459518, point 1) |
| `store_blob` (under Q4(b) (iv) only) | column | **(new)** |
| `register_head` (`seq`) | table, column | `r3a/REPORT.md:187` |
| `mirror_state` (`diverged_sha`, `found_utc`, `found_by_ws`, `last_error`, `last_error_utc`) | table, columns | **(new)**; the divergence record of D16 |
| `journal` (`seq`, `base_seq`, `utc`, `kind`, `verb`, `lane`, `workstation`, `message`) | table, columns | `r3a/REPORT.md:189-195` |
| `operator`, `args_sha256` | columns | **(new)**; D24's flag (F2), and F10's digest |
| `write_id` | column | `proposal.md:253-258` |
| `journal_write_id_key` | constraint | **(new)** |
| `root_seq` (a key of the entry), `journal_one_root` | entry key, index | **(new)**; D22 |
| `switch` (`after_loss`, `database_label`), a key of the entry | entry key | **(new)**; the cutover's after-loss form (F18) |
| `not_after`, a key of the entry | entry key | **(new)**; F3's bound on a commit call, required (lane 4's section, `5f26bf0a`) |
| `act` (`id`, `base`, `generation`, `expect`), a key of the entry; `journal.act_id` | entry key, column | **(new)**; lane 4's act and expectations (D5, F5; its row 20); step 3b |
| `journal_scopes` (`seq`, `scope`), `journal_scopes_seq` | table, index | **(new)**; each entry's touched keys (lane 4's row 20), derived by steps 5a and 8a |
| `row:`, `bind:`, `objects:`, `alias:`, `archive:`, `sid:`; `*` | scope values | lane 4's six scopes (its section, `5f26bf0a`); `*`, every scope, is **(new)**, this part's, for an import or an operator entry; an archived row's second `archive:` key, its normalized key (S3), is `RETIRED_ROWS_AWK`'s `key()` (`code/lanes-edit.sh:3743-3760`) |
| `line_scopes`, `doc_line_scopes` | functions | **(new)**; steps 5a and 8a, by `build_records`' rules (`code/lanes-index:765`) |
| `entry_new_keys`, `scope_fault`, `scope_checked` | functions | **(new)**; step 8a's new side, each key with its line, and N2's check of a derived key (LR010) |
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
| `repo_spellings` | column | **(new)**; D18's, as `spellings` is D19's (lane 3's ruling, 6579459518, point 5) |
| `LANDING EXPIRED` | line kind | **(new)**; F13, if Brett takes it |
| `main_slots` | view | `r3a/REPORT.md:270-278` |
| `apply_edits` | function (now `commit_write`'s step 6) | `r3a/REPORT.md:285` |
| `commit_write`, `switch_to_files`, `render_file`, `record_mirror_state`, `record_marker`, `find_write` | functions | **(new)**; `switch_to_files` is the cutover's abort (F2), `record_marker` S4's read-back (F12), `find_write(p_write_id, p_not_after)` F3's one lookup, which waits on the server's clock past `p_not_after` (S2) |
| `lock_head` (under Q7 (A′) only) | function | **(new)** |
| SQLSTATEs `LR001`, `LR002`, `LR003`, `LR004`, `LR005`, `LR006`, `LR007`, `LR008`, `LR009`, `LR010`, `LR011`, `LR012` | error codes | **(new)**; `LR007` is F10's and F18's, and an act's base from another generation; `LR008` is F3's bound, `not_after`; `LR009`, "expectation changed", is D5's check (step 3b); `LR010`, "key not well formed", is N2's; `LR011`, "lookup too early", is S2's; `LR012`, "mirror diverged", is S5's |
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
`lanes-index mode` exits 78 (`EX_CONFIG`), never 2, with its text, for a
`mode=primary` configuration, and `lanes-edit.sh` exits 69 repeating it and
writes nothing (agreement test 14). `register read`, in both its forms,
exits 78 with it too, since readers and the guard learn the mode from
`register read --prefetch` (SVC-8; the phase plan's "D3's refusal"), and
every other `lanes-index` command refuses it with 2, as the configuration
parser does. Any other failure of the query (a
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
directory and `<digest>` is the sha256 over `LANES_LANE`, `LANES_SESSION`, the
verb and its arguments, which is the entry's `args_sha256`; `LANES_SESSION` is
in it because it is written into the event lines; it
holds the id, the UTC, the command line, the writer's pid and the database
generation (D22). The record is removed once the outcome is known. A later run
of the same command line, with no `LANES_WRITE_ID`, reuses the id, says so on
stderr, and consumes the record, so only the first rerun after an in-doubt
write is treated as its retry. So a write killed by an outer timer, which
cannot print anything, is still retried under its own id (ADV-C3; lane 2's
SHOULD e). Three limits keep a record from being reused for another write
(D8): a record past its maximum age, 15 minutes from its last update, or of
another generation, is not reused;
a record whose pid is still live is waited for, up to D7's deadline; a run
that still finds it live then exits 3 naming that record's write id, having
sent nothing (lane 4's N2), since two identical runs at once must not share
an id; and the snapshot refuses with 2 when the journal
entry an id names has a different lane, verb or arguments from this run's,
which also stops a `LANES_WRITE_ID` given to a different command. Lane 4 owns
the record's format; the age, the digest and the printed rerun's
`LANES_WRITE_GENERATION` are lane 4's row 16, as lane 3 ruled (#165
6710770304; the phase plan's "The pending record"). `lanes-index status` lists
the pending records and resolves each by looking its id up.

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
   phase 1's code. The write id is checked before the switch: a write id
   already in the journal exits 0, and runs the verb's after-COMMIT tail
   ("A write in doubt", below), where the entry's lane, verb and arguments
   are this run's, and refuses with 2 where they are not, or where the run's
   generation is not the switch's (D8), whatever the switch now reads; the
   commit makes the same checks under the head row's lock (LR006, LR007;
   §1.3). Only then does a switch that is not `primary` exit 69 with
   Amendment 22's text, so a write that is in the journal never exits 69
   because a rollback has since moved the switch (R2's S1). For a
   write made inside an act, `lanes-edit.sh` refuses with 2, sending nothing,
   before the first snapshot where `LANES_ACT` is malformed, where
   `LANES_EXPECT` is not empty and `LANES_ACT` is unset or empty, where a
   scope is outside the six forms, or where the act's base is `files`; and at
   each snapshot where the act's generation is not the switch's or its base
   is past `n`. The commit's LR007 and LR004 for an act are then backstops
   (lane 4's check of v2.4, #165 6711545001; the phase plan's "The acts and
   their expectations").
2. **The verb.** The verb runs as a child process, the script re-run with the
   original command line, on a scratch work tree whose
   one commit holds the images at `n`, as its `LANES_REPO`, so that its git
   acts (`git add`, `git mv`, the case-only rename made from the index,
   `code/lanes-edit.sh:2371`) run as they do today and its mutex is the scratch
   tree's (lane 4's row 12, as lane 3 ruled, #165 6710770304: the script's
   file-scope setup is recomputed for the scratch tree, and each attempt gets a
   fresh read family). After slices 1a and 1b, an explicit `LANES_REPO` keeps S3–S5
   applying in it (Amendment 23's S2 item 1), and a scratch tree has no origin
   to fetch, so the primary branch runs the verb under an internal flag that
   bypasses the resolver and S3 for the scratch tree, and also skips the mode
   query and D17's reads, so the verb neither recurses into the primary branch
   nor reads past its one snapshot (lane 4's row 13, as lane 3 ruled); lane 4's phase plan names
   it `LANES_REGISTER_SCRATCH`. `repos.tsv`, outside the render set, is read from the lane
   checkout's `origin/main` (D19). At the commit seam the primary branch reads
   the staged change set (paths, renames, removals and the new images) and the
   message, writes them to the attempt's directory, and suspends there until
   the parent's commit call decides: after a commit the child runs the verb's
   tail once, and otherwise it exits without it (the phase plan's "How an
   attempt runs the verb"). A verb that
   refuses exits with its own code, and nothing has been sent.

   **Output and side effects, once.** The verb runs once per attempt, 55 times
   in a burst of ten, so each attempt's stdout and stderr are buffered, and
   only the deciding attempt's are replayed: the committed one's, or the one
   whose refusal ends the write. A refusal at the commit (D18, D19) therefore
   never follows a success text. Any side effect outside the register, such as
   tmux in `request-handoff`, runs once, after COMMIT. The phase plan lists
   the arms that have any (F14).
3. **The commit.** `lanes-index register commit`, one `psql` call, runs
   `commit_write(n, write_id, REGISTER_SCHEMA_VERSION, generation, entry)` at
   SERIALIZABLE (§1.3, §1.3.1), in this order:
   1. take the head row's lock and look the `write_id` up: an entry with this
      run's lane, verb and arguments is LR006, and the write exits 0; one for
      another write, or a generation not the switch's, is LR007, and it exits
      2 (D8). LR006 is reached where an earlier attempt in doubt committed
      after this attempt's snapshot read and before this commit's transaction
      began. One that commits while this commit's transaction is open, whether
      it is waiting for the head row's lock or not, fails this commit with
      40001 at the lock, or with 55P03 once its `lock_timeout` passes; the next
      attempt's snapshot read then finds the id, and the write exits 0. So
      under the lock, either this transaction sees the earlier commit and the
      lookup finds it, or it does not and the lock fails with 40001, since
      every commit moves the head row: the journal's unique `write_id` is
      never reached, and the frame's 23505 backstop (F33) is gone. A 23505
      there is a bug, and exits 69;
   2. refuse unless the head is still `n` (LR001: the attempt has lost its
      turn, D6), the client is inside the schema's window (LR003, §1.5) and
      the switch is `primary` with the marker `present` (LR002); then, for a
      write made inside an act, check the expectations it carries for the
      act's earlier reads (D5; `commit_write`'s step 3b, §1.3), still under
      the head row's lock and before the head moves: LR007 where the act's
      base is from another generation, and LR004 where it is past the head,
      both of which the client's checks at the snapshot refuse with 2 first
      (step 1); and LR009, `expectation: <scope> changed at seq <k> by
      <lane>/<verb>`, a 2, where a journal entry after the act's `LANES_ACT`
      base, made outside the act, touched a scope named in `LANES_EXPECT`;
   3. move the head to `n+1`, then insert the journal row (D6's order);
   4. apply the line edits to `doc_lines`, checking the base digests (LR004);
   5. refill the structured layer for the rows and lines touched, and check its
      constraints: one live claim per object, spelled any way (D19), and the
      hold order (D18), each LR005 and a 2;
   6. prove the per-file digests (LR004); last, refuse with LR008 unless the
      database clock is still before the call's `not_after` (§1.3's step 9,
      lane 1's v2.3); and `COMMIT`.

   It runs with `VERBOSITY=verbose` and `SHOW_CONTEXT=never` (lane 3's choice
   of lane 4's S2, #165 6711564365), so `psql`'s first line on stderr is
   `ERROR:  <SQLSTATE>: <message>`. Every code is read from that one line,
   40001, 55P03, 23505 and the LR codes alike, so a 40001 is told from the
   other errors by its SQLSTATE and not by `psql`'s exit 3, and LR009's and
   LR010's messages come back in the same call, with nothing read again.

### The outcomes

| What happened | `lanes-index register commit` | `lanes-edit.sh` | Written |
|---|---|---|---|
| committed at `n+1` | 0, `committed <seq>` | forks the renderer; exits the verb's code; `claim` prints `register seq <n+1>` (Q19(A)) | the write |
| the head moved (LR001), 40001, 40P01, or 55P03 (`lock_timeout`) | 75 | a new attempt at once; at the deadline, exit 75, unless a commit of this write was in doubt (below) | nothing |
| `psql` exit 3 for any other SQLSTATE: the transaction was rolled back | 69, with the SQLSTATE | 69, naming the store and the error | nothing |
| LR005, a rule of the register (an object held, spelled any way, or `main` held) | 2, with the holder or the earlier line | 2 | nothing |
| LR006: this write is already in the journal | 0, with its seq | exits 0, as committed | the write, by an earlier attempt |
| LR007: the id belongs to another write, or to another generation; or an act's base was read in another generation (§1.3's step 3b), which for an act is a backstop to the client's check at the snapshot (step 1) | 2, naming the id and what differs | 2 | nothing |
| LR008: the attempt reached step 9 past its `not_after`, and its transaction rolled back | 75, a lost turn | inside D7's 30 s, a lost turn: a fresh snapshot, then a new attempt under the same write id, as 40001 is; at the deadline, F3's rule ("A write in doubt", below); never 3 or 69 by itself (lane 4, #165 6709038351, as lane 3 ruled in 6710770304) | nothing |
| LR009, "expectation changed": an entry after the act's base, made outside the act, touched a scope the write lists in `LANES_EXPECT` (§1.3's step 3b; D5) | 2, with lane 4's `expectation: <scope> changed at seq <k> by <lane>/<verb>`, the server's message, from the same `ERROR:` line as the code (step 3) | 2 (lane 1's v2.4; lane 3's rulings, #165 6710840240, 6711233420 and 6711564365) | nothing |
| LR010, "key not well formed": a key that a line of the write derives, on its old side or its new, is not a scope: its name is empty or holds white space (§1.3's "The act", steps 5a and 8a) | 2, with the server's `key: <path> line <n> derives <key>, which is not a scope (…)`, from the same `ERROR:` line; never retried, since the same line derives the same key | 2 (lane 1's v2.5; lane 3's ruling, #165 6711924759) | nothing |
| LR011, "lookup too early": the in-doubt lookup's `<n>` is missing, or more than 3 s ahead of the database clock, or the clock had not passed `<n>` when `find_write` checked it under the head row's lock, as after the clock stepped back (§1.3.1; R2's S2) | — (raised by the lookup, not by a commit call) | 3, naming the write id, with the server's message; the pending record kept, and a rerun under the same id looks again (lane 1's v2.6) | unknown |
| LR012, "mirror diverged": a `rollback` entry while `mirror_state` records a divergence, read `FOR SHARE` under the head row's lock (§1.3; R2's S5) | — (raised only by `lanes-index rollback`'s entry, which exits 2 with `diverged at <sha>` and S2's two ways out, writing nothing) | — (no write of `lanes-edit.sh` makes a `rollback` entry) | nothing |
| LR002: the switch, or its marker, is not what a write needs | 69, Amendment 22's text | 69 | nothing |
| LR003: the client is outside the schema's window | 69, naming both numbers | 69 | nothing |
| LR004: the edits do not apply, or the proof differs; or the write's act is malformed, or its base is past the head (§1.3), which for an act is a backstop: the client refuses both with 2 first (step 1) | 69 after one reload and retry | 69 | nothing |
| `psql` exit 2, or the call's client timer expired | 3 | in doubt: the next attempt's snapshot looks the id up | unknown |
| no connection at the first snapshot, before any commit was sent | — | 69 | nothing |

A `psql` exit 3 is never in doubt: under `ON_ERROR_STOP` the script stops
before its `COMMIT`, and a serialization failure raised at `COMMIT` rolls the
transaction back. Every exit 2 of a commit call is treated as in doubt,
connection failures included, since telling them apart would mean matching
`psql`'s stderr, which the probes found identical before and after a COMMIT
that landed; the next snapshot's lookup settles it. Once any commit call of
the write was in doubt, every end in this table but 0 waits for F3's lookup
first ("A write in doubt", below).

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
transaction the database refuses; Brett's answer to Q7 gives "register busy"
this code of its own, so "(A″) with 69" is not taken (#165 6710653292).

### A write in doubt

A commit call that ends with `psql`'s exit 2 or its own timer is in doubt. The
loop goes on: the next snapshot looks the `write_id` up and exits 0 if it is
there. If it is not, the next commit is safe even while the old transaction is
still open, because that transaction holds the head row's lock: the new commit
waits up to its `lock_timeout` and gets 55P03, or 40001 if the old transaction
commits meanwhile, and tries again; once the old transaction ends, the next
snapshot finds the id or the head is free (lane 2's LR006 probe, #165
6708917587). The
REGISTER-WRITER role's `idle_in_transaction_session_timeout` bounds how long a
transaction whose client vanished can hold the head row (D9). Where the
snapshot cannot reconnect, the writer keeps trying for up to 10 s, or, once a
commit call was in doubt, makes the lookup's tries instead, and then exits 3: "cannot confirm whether register write `<id>` committed; nothing else
was written", with the lookup (`lanes-index journal --write-id <id>`) and the
rerun (`LANES_WRITE_ID=<id> LANES_WRITE_GENERATION=<generation> LANES_LANE=<lane>
LANES_SESSION=<uuid> lanes-edit.sh <the same arguments>`, the phase plan's
"The pending record"). The pending
record stays until a rerun consumes it.

**A run that finds its write committed runs the verb's tail once.** A run
whose snapshot, commit (LR006) or in-doubt lookup finds its `write_id` in the
journal writes nothing again. It runs the verb's after-COMMIT tail, the side
effects outside the register that follow the commit, in one child only: one
stopped at the seam with the entry's own change set. That is either the kept
child of the call that committed, or a replay of the verb at the entry's base.
It runs the tail only after it has marked it sent in the pending record (lane
4's row 24; the phase plan's "The tail, once"). A run that finds the tail sent
or done, or that holds no pending record of the write, runs none of it and
names what is owed. Three tails are not safe to run twice: `gh_comment`,
tmux's `guard_type` and the handoff's stamp. `lane_state_follow` is safe twice
only with the committing attempt's pre-image, which a replay reuses. The
tails, from the phase plan's "Output, and the side effects that run once"
(F14):

| Verb | Its after-COMMIT tail | Twice |
|---|---|---|
| `write_event`'s verbs (`log`, `claim`, `release`, `request-handoff`) | `lane_state_follow`'s sidecars under the control root | only with the committing attempt's pre-image |
| `retire-rows` | the follow loop | only with the committing attempt's pre-image |
| `claim`, `release` | `gh_comment` | no |
| `request-handoff` | tmux's `guard_type`, and the poll | the typing, no; the poll, yes |
| `rename-lane` | the flush, `lane_state_rename`, the `gh_comment` loop, and the handoff's move and stamp | the flushes, `lane_state_rename` and the handoff's move, yes, since a second move finds nothing at the old path; the comments and the stamp, no |
| `start-record` | `lane_state_follow`, after the lock, as `write_event` runs it | only with the committing attempt's pre-image |

Each needs a test of its own, in lane 2's pass after the review (lane 3's
ruling).

**A write in doubt ends only after its lookup.** Its own orphaned transaction
can hold the head row, so its later commits get 55P03, which is otherwise a
lost turn; and an orphaned commit call that has not yet reached the head
row's lock, its query still queued at a loaded server, can commit after a
later attempt of the same write has failed fast for another reason (the
phase plan's "The in-doubt rule"; R2's S1). Were the write to
exit 75, 69, 2 or the verb's own code on such an attempt, "nothing was
written" could be false, the pending record would go, and a rerun would mint
a new id, which is ADV-C3's second `CLAIMED`. **So once any commit call of a
write was in doubt, the writer ends with no code but 0 until it has made
F3's lookup,** its tries starting within the 10 s reconnect window, which
starts when its own wait for `<n>` ends, each running to its own 8 s timer
(the phase plan's "The in-doubt rule"):

1. **It calls `find_write(<id>, <n>)`** (§1.3.1), where `<n>` is the largest
   `not_after` its commit calls carried, at READ COMMITTED, in a transaction
   it rolls back. The function waits, holding no lock, until the database's
   own clock has passed `<n>` (R2's S2). Every commit call carries
   `not_after`, the snapshot's database clock plus the call's client timer
   plus 2 s, on every kind of entry, the operator entries' included (lane 3's
   ruling, #165 6710770304, lane 1's point 4). `commit_write` raises LR008
   past it (§1.3's step 9), so an orphaned attempt that had not reached the
   head row's lock can no longer commit after the lookup reports the write
   absent. Lane 4's row 15 is closed by lane 1's v2.3, and its v2.6 puts the
   wait on the server's clock. The wait is at most 3 s: the writer calls once
   its own estimate, the snapshot's clock plus `SECONDS` since, puts `<n>` at
   most 2 s ahead, and a `<n>` further ahead is refused with LR011.
2. **It takes the head row's lock, its tries starting within the 10 s
   reconnect window, which starts when its own wait for `<n>` ends, each
   running to its own 8 s timer (the phase plan's "The in-doubt rule"),**
   inside `find_write`, under the caller's `lock_timeout`. The writer has no UPDATE,
   so its own `FOR UPDATE` would get 42501. Under the lock the function checks
   that the clock has passed `<n>`, the exact complement of step 9's test, and
   refuses with LR011 where it has not, as after the clock stepped back.
   Holding the lock proves no transaction of this write is still open.
3. **It looks its write id up:** `find_write` returns the write's seq, or
   NULL.

Its outcomes:

- **found:** exit 0, and the verb's after-COMMIT tail runs once, in a child stopped at the seam with the entry's change set,
  as for any rerun that finds its write (above);
- **absent, with the lock held:** the code its last attempt earned, whether
  75 ("register busy"), 69, 2 or the verb's own;
- **the lock not held within the window, or the lookup refused** (55P03,
  LR011, or no answer): 3, naming the write id, the pending record kept.

"Register busy" is never exited without that lookup. The register's own
refusals exit 2 (LR005, LR007, LR009 and LR010; the outcomes table), and 69
is for an unreachable database and any other refusal. Agreement test 12
gains these cases: the wait past `not_after`, a rerun that finds its id and
runs the tail, and an in-doubt write whose next attempt is refused for
another reason (the review of commit J, R1's S3 to S5 and R2's S1, as lane 3
ruled).

### What lane 4's phase plan must confirm for S1

**Answered** by the phase plan below (lane 3's acceptance, #165 6708889286);
each answer follows its item, and the phase plan's "The routed items,
answered" cites each.

- **[L4-a]** that each verb runs unchanged on a scratch tree, and which verbs
  reach the commit seam more than once in one run. One journal entry carries
  one message, "exactly as today", so such a verb needs a rule: one entry with
  a message the design names, or the verb split in two. Lane 4 picks the rule.
  **Answered:** every write verb runs unchanged on a scratch tree but
  `rename-lane`, whose handoff half becomes its after-COMMIT tail, and
  `commit`, which is refused; none can reach the seam twice on primary, and the
  rule is one run, one journal entry, with a second reach exiting 2 (the phase
  plan's "Each write verb on a scratch tree");
- **[L4-a′]** the internal flag that bypasses the resolver and S3 for the
  scratch tree; the arms with side effects outside the register, which run once
  after COMMIT; the expectations each act's write verbs carry (D5); every
  failure of the mode query and Q6(c)'s code, now 78 (D3); the
  pending record's format (D8); and the callers that read exit 3 as "written"
  (D8). **Answered:** `LANES_REGISTER_SCRATCH` ("The internal flag"); the arms
  ("Output, and the side effects that run once"); `act-begin` and six scopes
  ("The acts and their expectations"); every failure of the query, each
  running files mode with a warning but 78 ("The mode query's failures"); the
  record ("The pending record"); and five places that read 3 as written ("The
  callers that read exit 3 as "written"");
- **[L4-b]** that the staged change set gives the journal's edits for the five
  acts that change several files (`LANDING` and `LANDED`, `rename-lane`,
  `retire-rows`, `archive-rows`, `migrate-state-cells`). **Answered:** all five
  are given by `--raw` plus the images ("The change set for the five acts");
- **[L4-c]** the cost of one attempt: two Python starts, two `psql` starts, the
  scratch tree and the verb. Test 9 records it; if it is too high, one
  `lanes-index` process can serve both calls of an attempt over a pair of
  named pipes, which bash 3.2 has. **Answered:** about 0.55 s an attempt with
  the smallest verb arm, about 0.61 s with lane 2's 52.5 ms per SERIALIZABLE
  write; no frame figure is exceeded, and D7's 30 s holds about 50 attempts;
  lane 4 recommends deciding the pipe pair at phase 2, from tests 9 and 4 ("The
  cost of one attempt, [L4-c]").

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
lane's checkout, `<W>-lanes/<lane>/`, from Amendment 23's resolver. Phase 2
ships the fork and `<c>`, so phase 3 is `lanes-index` and `tests/` only (lane
4's row 10, as lane 3 ruled, #165 6710770304; the phase plan's "What phase 2
ships for phase 3").

- **One render at a time per checkout, and no fork waits.** The renderer takes
  its own lock, an `fcntl.flock` on `lanes-render.lock` in the checkout's git
  directory, in `SyncLock`'s form, never the lane's write lock. The kernel
  releases it when the renderer dies, even by SIGKILL, where a `mkdir` mutex
  would stay behind and stop the checkout's renders (D14); Python opens the
  lock file close-on-exec, so a `git push` that outlives a killed renderer does
  not hold it. **Both halves of `SyncLock`'s handshake**
  (`code/lanes-index:2225–2251`): a fork that finds the lock held leaves a
  rerun mark (`lanes-render.rerun`), tries the lock once more, and exits; the
  holder, after it releases, looks for a mark once more and takes the lock back
  for it. Without the second half, a write that commits between the holder's
  last look and its release is never rendered, as the prototype showed (the
  phase plan's "The render lock"). [L4-3], [L4-4]
- **A pass.** Clear the rerun mark; fetch `origin main`, bounded by
  `LANES_GIT_TIMEOUT`; find `m`, the last `Register-Seq` on `origin/main`'s
  first-parent history, and its commit `R` (an operator seq counts as rendered
  once its own command's read-back has put it there), by form C in pages:
  `git log --first-parent -n 200 --skip <s> --format='%H%x00%(trailers:key=Register-Seq,valueonly)'`,
  taking the first non-empty value, never `--grep`, the whole walk bounded by
  `LANES_GIT_TIMEOUT`. Where the time runs out, or the whole line carries no
  `Register-Seq`, the pass renders and pushes nothing, records why with the
  mirror state (D16), and the renderer ends; `status` shows it. A commit that
  a `stray-import` or `stray-revert` entry names in `git_commit` is resolved:
  its `Register-Seq`, if it carries one, does not count when the pass finds
  `m`. Read the journal's head `h`. Where `m` is above `h`, a `Register-Seq`
  copied or forged past the head (R2's N6), the pass records a divergence at
  `R`, the commit carrying `m`, through `record_mirror_state`; it renders and
  pushes nothing, and the renderer ends. `status` shows `diverged at <R>`.
  Check for a divergence (below); read the journal entries after `m`; take the
  target `t = min(h, m + 64, o − 1)`, where `h` is the journal's head and `o`
  the first operator entry after `m`, known by `journal.operator`, so a pass
  renders at most 64 seqs; and for each seq `k` from `m + 1` to `t` in order,
  build the images at `k`, then the commit: a private index (`GIT_INDEX_FILE`
  in the checkout's git directory), `read-tree` of the parent, the changed
  blobs written with `hash-object -w` and staged with `update-index`,
  `write-tree`, and `commit-tree --no-gpg-sign -p <parent>`, with the entry's
  message and `Register-Seq: <k>` in its final trailer block (D15), its author
  and committer and both dates set to the entry's commit UTC, in UTF-8. The
  first parent is `origin/main`'s tip; each later commit's parent is the one
  before it. Where the next seq the mirror lacks is an operator entry,
  `t ≤ m`: the pass renders nothing past it, since its command owns it (F2).
- **A fetch that fails** ends the pass with nothing pushed: SQL stays true, the
  failure is recorded with the mirror state (D16) where the database can be
  written, `status` shows the lag, and the next render catches up.
- **The push.** `<tip>:refs/heads/main`, never forced. Under Q21(b)(B) it
  pushes with the renderer identity (D23), to an explicit
  `ssh://git@github.com/<owner>/<repo>.git`, where `<owner>/<repo>` is
  `_norm_repo` of the checkout's own `origin` URL (`code/lanes-index:367–375`),
  so the push names the repository that was fetched, in the push's own
  environment: `GIT_SSH_COMMAND` naming the render key with
  `IdentitiesOnly=yes` and `BatchMode=yes`, `SSH_AUTH_SOCK` removed,
  `GIT_CONFIG_GLOBAL=/dev/null` and `GIT_CONFIG_NOSYSTEM=1`, and `--no-verify`.
  A deploy key authenticates only over SSH, and `git push origin` would use the
  checkout's HTTPS remote and the lane's own credentials, which the ruleset
  refuses; nothing is written to the checkout's configuration. A push to a URL
  moves no `refs/remotes/origin/main`, so the read-back fetches `origin` and
  checks ancestry. A non-GitHub origin with `render_key=` set is refused with
  2. Without `render_key=` (Q21(b)(A), and the bare-origin fixtures) the push
  goes to `origin` as it stands; one push function serves the renderer,
  `cutover` and `rollback` (the phase plan's "The push URL").
- **A lost race leaves nothing to discard.** A rejected push moved no ref and
  touched no HEAD; the renderer re-fetches, and where origin then carries `t`
  or more, because another renderer built the same commits, that counts as
  pushed; otherwise the pass rebuilds, six push attempts in all, as Amendment
  23 bounds a writer, and then the renderer ends with the lag shown by
  `status`; the next fork resumes. Two renderers that start from the same tip
  build the same commits, byte for byte: a second push of the same sha, one
  after the other, is a no-op, and two at the same moment end with one
  rejected, which refetches and finds nothing to push. [L4-1], [L4-2]
- **Bounded progress (F15).** Each fetch and push is bounded by
  `LANES_GIT_TIMEOUT`, and each database read by the renderer's row of the
  timeouts table. A renderer makes another pass only after a pass that moved
  origin's last `Register-Seq` past where that pass found it (its own push, or
  another renderer's push of the same commits), or once for each rerun mark it
  finds after it releases; so a pass that cannot progress (an authentication
  failure, a divergence, an operator seq its command has not yet put on origin)
  ends the renderer and never loops, and the next fork tries again. After 16
  passes it releases the lock and re-forks itself once, detached, only if its
  last pass made progress, so the last write of a burst is rendered even if no
  write follows anywhere, without a process living unbounded under a steady
  stream. A burst that arrives within one pass gives at most two renders; a
  slower stream gives one render per pass, never one per write. The 64 and the
  16 are the design's values, unmeasured. A known gap: six races lost to
  handoff pushes leave the last write unrendered until the next write
  anywhere, or `reconcile`. [L4-3]
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
same way. So is a last `Register-Seq` above the journal's head, recorded at
the commit that carries it (R2's N6). `reconcile` re-renders what is missing
and imports nothing.

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

For a `Register-Seq` above the head at `R` (R2's N6), `render --revert <R>` is
the way out. Its entry takes `h+1` and names `R`, so the next pass does not
count `R`'s trailer: it finds `m` at the last render commit before `R`,
renders on up to `h+1` on top of `R`, and from then on `m` is the revert's own
seq. `import --single <R>` would clear it the same way, but it loads `R`'s
render-set edits as a write, and a copied or forged trailer gives no reason to
keep them. Neither verb needs a change: the pass's rule that a resolved
stray's trailer does not count is what lets either finish.

### The in-place form, if the prototype fails the private-index form

Not taken: the prototype passed [L4-1] to [L4-5] with the private-index form
("What lane 4's prototype must confirm", below). The fallback is kept here as
it was written.

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

**Answered** by the phase plan's "The renderer prototype" (lane 3's
acceptance, #165 6708889286): all five pass with the `flock`. [L4-3] passes
qualified: at most two renders holds for a burst that arrives within one pass,
and a slower stream gives one render per pass, never one per write. [L4-4]
fails with a `mkdir` mutex, which is why the lock is a `flock` (D14). None of
[L4-1], [L4-2] or [L4-4] failed, so the private-index form is the design and
the in-place form is not taken.

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

In phase 2 the bind's writes, the session cell, the state and the line (with a
new lane's row), are one `start-record` call (D5), and its exits keep 2A by
lane 4's corrected table (the phase plan's "The bind as one verb"; lane 3's
ruling, #165 6710770304): any 2 stops the launch, an expectation's (a second
binding) and LR010's included, as lane 4's arms row has it for `:4181`; a 69,
75 or 3 stops the launch with that code where the verb makes the lane's row
(`--add-row`), since the row is a write that must exist; and where it makes
no row, a 69, 75 or 3 prints a note with the writer's printed rerun, the Rule
3 stamp is written, and the launch goes on.

The five `lane-start` arms that note an unknown code and carry on (`:1154`,
`:1262`, `:1271`, `:3079`, `:3430`) treat 75 as refuse-class. No read returns
75 under (A″), since only a commit can lose its turn, but the arms refuse on it
so that a later change cannot reopen the fail-open. `:1154` refuses 69 too, by
2A; at `:1262`, `:1271`, `:3079` and `:3430` a 69 stays the note it is today,
as R3's option A leaves those four reads. Lane 4's table of the callers'
default arms (the phase plan) adds two read arms the frame's list lacked,
`:1332` (`resolve-home`, silent today) and `:2184–2186` (`session-lane`),
which refuse 75 and 3 as well, `:2184–2186` 69 too; so
`specs/sql-register/spec.md` names seven read arms.

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
  names where the host cannot fetch; a rollback's declaration records the
  head instead (below). `lanes-index outage clear` removes it.
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
  client's: lane 5's question 18), found by SVC-12's bounded lookup, form C.
  It then answers from the mirror, as above,
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
  `outage declare --rollback --mirror-seq <k>` for Brett to run on each other
  host. Its `mirror-seq` is `k`, the head that the rollback's transaction
  found, not the mirror's `m`: the database is up, so `k` is known exactly,
  and seq `k+1`, the rollback's own entry, is never rendered. A guard whose
  ref carries a `Register-Seq` below `k` refuses with 2 and SVC-12's text for
  a mirror behind the declaration's seq, which tells the person to fetch it
  once origin carries that seq, or wait for the outage to end, so no guard
  answers from a mirror that lacks a write SQL committed (R2's M1). The
  rollback is Brett's act, so the declaration is
  his, as Q26(a′) requires. A
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

0. **It refuses to start while `status` shows `diverged at`** (the frame
   review's F1). S2's two ways out resolve the divergence first; each goes
   through S1's commit call, so each runs only while the switch is `primary`.
   Right before step 1 it fetches `origin main` again and checks it for a
   divergence as the renderer recognises one, a last `Register-Seq` above the
   head included (R2's N6). It records one it finds with `record_mirror_state`
   and refuses with 2. Inside step 1's transaction, `commit_write` refuses a
   `rollback` entry while `mirror_state.diverged_sha` is set, reading the row
   `FOR SHARE` (LR012, §1.3), so a divergence being recorded at that moment
   refuses it too (R2's S5). A 40001 on that call sends the rollback back to
   this check.
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
4. The new generation. On a re-import, the bootstrap writes the new
   database's switch row with a new generation id (`operations.md` §2.4), so
   the lost database's generation is refused from there on; after a restore,
   the restored switch row carries the lost database's generation until step
   6's cutover sets a new one, a window the lanes' pause covers (§1.3's "One
   gap stays"; lane 3's ruling, #165 6711233420, point 4). Once the
   generation is new, a pending record of the old generation is refused,
   since the write ids up to `m` went with the old journal, and a rerun under
   one could apply a write the mirror already holds (D8, D22).
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

`operations.md` §2.3 details these steps, including the marker's `database=`,
which the after-loss cutover's entry rewrites. Where that cutover meets the
switch at `primary`, as on a restored backup or on the live database by
mistake, it refuses a label equal to the switch's (LR004, R2's N4), so a
restore needs a replacement whose label differs from the lost database's. The
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
  happen, never a false one. The `LANDING` it names is found by `repo_key`
  alone, so a `LANDING EXPIRED` that spells the repository another way names
  no stale `LANDING` and fails closed with LR005, writing nothing (lane 3's
  ruling, #165 6710770304, lane 1's point 5); `operations.md` §2.6 tells the
  writer to spell it as the `LANDING` did.
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
| REGISTER-WRITER | `register_url=`, `register_passfile=` | `lanes-edit.sh`'s writes, through `lanes-index register commit` and `snapshot`; the renderer; `import`, `cutover` and `rollback`; `outage declare`'s database probe (D9) | the register's tables, the journal, the switch, the mirror state |
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
| the guard's direct read | 2 s | 1000 ms | 1500 ms | cut to the parse's start line, `T0 + 3.75 s`, so never above 3.75 s, or to `T0 + 3.65 s` where an unexpired declaration names this database (SVC-8, SVC-12) | 2 |
| the service's connection, per request | 2 s, in the background only, outside any share (SVC-6) | 150 ms | 180 ms | 200 ms, inside the client's 300 ms wait | `unavailable`, and the client reads directly (SVC-8); `refuse db <cause>` only where the refusal is definitive (SVC-7) |
| the guard's answer 2 | — | — | — | nothing on the hook's time: the guard records the bind in the session's offer file, starts it as a `setsid` child, `guard-bind`, and exits 2 with "BINDING"; the bind runs with a writer's bounds and D7's deadline, the whole bind, `guard-bind`'s body under one `timeout`, bounded at 430 s by the phase plan's sum (SVC-8; the phase plan's "Answer 2's detached bind") | as a writer's; the guard reports the outcome from the offer file's record, by the phase plan's eleven-case table |
| the mode query, `lanes-index mode`, which reads only the configuration (D3) | — | — | — | 2 s | the query is killed, and the verb runs in files mode with a warning naming the timeout (F8); on a SQL estate the marker check then refuses with 69 (lane 3's ruling, #165 6711028142) |
| a writer's snapshot, and `act-begin`'s (the phase plan's "The acts and their expectations") | 2 s | 2000 ms | 5000 ms | 8 s | a failed read: 69, or in doubt where an earlier commit of the same write was |
| a writer's commit | 2 s | 2000 ms | 5000 ms | 8 s | in doubt |
| the in-doubt lookup, `find_write(<id>, <n>)` (S2; D8, S1) | 2 s | 2000 ms | 6000 ms: its wait of at most 3 s, then the lock, then 1 s for the lookup | 8 s a try; the tries start within the 10 s reconnect window, which starts when the client's wait ends | 3, naming the id, the pending record kept (D8) |
| a whole write | — | — | — | 30 s before a new attempt; 10 s to reconnect in doubt; a whole write ends by about 67.5 s (the phase plan's `T_write`) | 75, or 3 |
| a reader | 2 s | 1000 ms | 3000 ms | 6 s | a failed read (S3's table) |
| the renderer's reads, and `reconcile` | 2 s | 1000 ms | 10 s | 15 s | the render ends; the lag stays |
| `status` | 2 s | 1000 ms | 5000 ms | 8 s | 69, with the lag from git where it can read it |
| `rollback`, each call | 2 s | 5000 ms | 10 s | 15 s | 69; step 1's expiry re-reads the switch |
| `import` (lane 1's row, §1.3) | 2 s | 2000 ms | 300 s, #160's `psql` bound (`code/lanes-index:1317`) | 330 s | 69; nothing is swapped in (S5) |
| a migration | 2 s | its own, retried | — | — | refused; rerun |
| `cutover`, its transaction: the switch to `primary` and its entry, in every form | 2 s | 5000 ms | 10 s | 15 s; it carries `not_after` (D24) | 69, with the switch unknown: the rerun reads the switch and the journal first, and goes on from its own entry where it finds one (F12) |
| `switch_to_files`, the cutover's abort, either form | 2 s | 5000 ms | 10 s | 15 s | 69: the rerun reads the switch. At `files`, or back at the restored generation with the head at `b`, the abort is done; otherwise it runs again |
| `record_marker`, after an operator entry's read-back | 2 s | 5000 ms | 10 s | 15 s | 69: the push stands, and the marker stays `adding` or `removing`, so writes keep refusing; the rerun reads origin back and records it again (F12) |
| `outage declare`'s database probe (`operations.md` §2.5's refusals): one read, connected as a writer's snapshot is, as REGISTER-WRITER, of the switch's `mode` and `set_seq` and the kind of the journal entry at `set_seq` | 2 s | 2000 ms | 5000 ms | 8 s | the database does not answer from this host, so the declaration is written; any other end before the read completes counts the same. At `primary`, 2. At `files`, the declaration is written only in the `--rollback` form, where the entry at `set_seq` is a `rollback` and `<k>` is `set_seq - 1`, the head before it (§1.3's `set_seq`); otherwise 2 |
| `import --single <sha>` and `render --revert <sha>`, their commit call | 2 s | 2000 ms | 5000 ms | 8 s | in doubt, as a writer's commit: each goes through S1's commit call (S2's "The two ways out"), `register commit` (the phase plan's interfaces table) |
| `import --single <sha>` and `render --revert <sha>`, their other database calls: the snapshot read, as a writer's; in doubt, the lookup's row above; the render that follows is the renderer's row | 2 s | 2000 ms | 5000 ms | 8 s | a failed read: 69, or in doubt where an earlier commit call of the same entry was |

Lane 1 measured the operator rows on PostgreSQL 16.15, after a 42,001-line
import, with each row's bounds set, timing each call with its `BEGIN` and
`COMMIT`. The figures are median and maximum: the first cutover, 6.7 and 29.4
ms over 6 runs; its abort, 1.9 and 4.8 ms; the restore form's cutover, 2.9 and
3.5 ms, and its abort, 2.0 and 2.6 ms; and `record_marker`, 0.7 and 1.2 ms.
Each `statement_timeout` is over 300 times its row's maximum, and the 5000 ms
`lock_timeout` outlasts a writer's 5000 ms statement on the head row. The
lookup's row is set by its wait, not measured (lane 1's §1.3). The commit call
of `import --single` and `render --revert` keeps a writer's commit bounds,
which lane 1 confirms: a `stray-import` of 3 lines on 3 paths, the 20,000-line
archive among them, took 22.8 ms median and 25.2 ms at most over 5 runs, a
`stray-revert` naming the same 3 paths 17.4 and 18.5 ms, and a 200-line
`stray-import` 181.4 and 184.3 ms over 3 runs, so the 5000 ms bound holds a
stray of up to about 5,000 lines (lane 1's v2.6, #165 6714645316).
`outage declare`'s probe takes a writer's snapshot bounds and credential, and
is not measured: the test plan proves it exits inside them, as for that row
(lane 5, #165 6714373660).

Only the first row's expiries are measured: `connect_timeout=2` exits 2 at
2007 ms, a `lock_timeout` of 1000 ms gives 55P03 at about 1011 ms, a
`statement_timeout` of 1500 ms gives 57014 at 1508 ms, and a 4 s client timer
is the only bound on a host that goes silent after connecting
(`clarifications.md`, ADV-C4). The other rows' bounds are the design's, and
the test plan proves each exits inside its
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
  offer file, the `/rename` typing and answer 2's writes stay client-side;
  its register writes are S1's transactions, run detached in `guard-bind` and
  recorded in the offer file (SVC-8).
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
  leaving a 0.5 s reserve for the parse, which lane 2's measurement keeps
  (lane 5's questions 7 and 14: 92.9 ms median, 156.6 ms p95 on the real
  register), and the test's assertion is the probes': exit 2 in
  under 4.5 s in every case. `CLAUDE_NO_LANE=1` still exits 0 before any input
  is read. **Answer 2 runs detached** (lane 5's question 16; SVC-8; the phase
  plan's "Answer 2's detached bind"): its writes are three writer calls:
  `start-record`, which makes `lane-start`'s three register writes in one
  transaction; the guard's `set-row-state` on the lane the window left; and
  the Rule 3 stamp, a git commit to the handoff, outside the register. Lane 2
  estimates the bind at the code pin, where `lane-start`'s three register
  writes are still separate calls, at 9.64 s median on primary, so the guard
  exits 2 with "BINDING" and the bind runs detached, off the hook's time, in a
  `setsid` child recorded in the session's offer file.
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
section. Commit L changes one place of lane 5's text, as lane 3 ruled on the
review of commit J: SVC-8's clamp refuses an elapsed time at or above the
parse's start line (R2's S4).
Commit M changes three more, from lane 5's check of commit L (#165
6714540821): SVC-8's clamp line is the direct read's cap, 3.75 s, or 3.65 s
where an unexpired declaration names this database, with the reason for the
earlier line; the exit mapping's late row says the same; and SVC-12's text for
a mirror below the declaration's seq is lane 5's. Commit N changes one, from
lane 4's texts: SVC-8's detached bind is bounded at 430 s over its whole body,
not 180 s (R2's S6). Commit O changes one: SVC-8 gives AT-F6 seventeen cases,
not fourteen.

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
- **The admin connection, too** (lane 5's change to lane 1's libpq service
  form, #165 6579523948, which lane 3 accepted): the bootstrap passes Brett's
  service to `psql` only as `PGSERVICE=<service>` in its environment, never as
  `service=` or `-d` on a command line, and drops an inherited `PGSYSCONFDIR`
  beside `PGSERVICEFILE`, `PGPASSFILE` and `PGPASSWORD`, so that only his own
  `~/.pg_service.conf` defines the service (`operations.md` §2.4).

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
   process group, so a group kill of the hook does not reach it. Claude Code
   2.1.319 ends a timed-out hook by SIGTERM to that process group and kills
   nothing on a normal exit, so a new session outlives both; only an
   escalation to SIGKILL is not shown, and it cannot reach a new session
   either (the facts table, by lane 4's hook probe).
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
  writer is lane 4's (question 13), and the phase plan answers that it does:
  content-addressed objects written by temp file and rename, per-seq
  manifests, and a `head` that moves only forward under a short `flock` make
  any writer safe, so the service may write through and may keep its images in
  memory too (the phase plan's "Question 13").

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
decision, the offer file, the `/rename` typing and answer 2's writes stay
where they are, though answer 2 now runs them detached, in `guard-bind`
(below).

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
  78, which lane 4 picked, and the guard refuses 2; and any other failure of the
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
| The parses and the decision | a reserve of 0.5 s | measured on the real register (2,590,927 bytes): 92.9 ms median, 156.6 ms p95, 99.2 ms cold (lane 2's `MEASURE.md`, (14)), so the reserve holds with 0.34 s to spare at p95 |
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
SVC-7 records, not taken). Lane 2 has measured it on the real register,
2,590,927 bytes: 92.9 ms median, 156.6 ms p95 and 99.2 ms cold, so the 0.5 s
reserve stays and the start line stays at `T0 + 3.75 s` (lane 3's ruling,
#165 6579459518; `MEASURE.md`, (14)).

**Under a declared outage** (lane 5's change, #165 6579523948, which lane 3
accepted). Where an unexpired declaration names this database, the client
reads it once, at the prefetch, before the direct read; it caps the direct
read at `T0 + 3.65 s`, 0.1 s earlier, which is then also the clamp's line
(below), so that a database that accepts the
connection and then goes silent still leaves the mirror path its start by
`T0 + 3.75 s`; and the mirror's reads and parse then use the 0.5 s reserve.
They are today's six register reads, which lane 2 measured at 308 ms median
and 435 ms p95. The 0.1 s covers SVC-12's bounded `Register-Seq` lookup,
form C, which lane 2 timed at 13 ms median and 16 ms p95 on 34,144 commits
(#165 6579627366). Without a declaration, SVC-8 is unchanged apart from one
`stat` at the prefetch.

**How the client knows `T0`.** The guard passes its own pid (`$$`, the hook
process). On Linux the client reads that process's start from
`/proc/<pid>/stat` against `/proc/uptime`. On macOS it reads `ps -o etime= -p
<pid>` and rounds up to the next whole second, which can only shorten the
direct read's timer, never lengthen it. **A clamp:** an elapsed time at or
above the direct read's cap, 3.75 s, or 3.65 s where an unexpired declaration
names this database, is a refusal: the client exits as "too
late to verify", and the guard refuses 2, since a hook that late has no time
for a read to finish, and a killed hook fails open (R2's S4). Under a
declaration, a client that starts between 3.65 s and 3.75 s would otherwise
pass the clamp with no time for its direct read, which would then count as
failed and open the mirror path while the database may still answer (lane
5's check of commit L, #165 6714540821). Only a
negative elapsed time, or one the client cannot read, is treated as unknown,
as where neither answers, and the client then assumes 1 s has passed. That
catches a `/proc/uptime` that a container virtualises (lxcfs) differently
from the process's start time, which gives a negative figure.

**`CLAUDE_NO_LANE=1`** exits at `code/lanes-edit.sh:648–654`, before anything
above runs: 3.3 ms of process wall here, with no input read, no service
contacted and nothing started.

**Answer 2's writes (F6): detached, after lane 2's timing.** When the person
answers `2` to an offer, `guard_answer` runs `lane-start --no-launch --bind`
and then `set-row-state` (`:11171`, `:11199`), and the guard exits 2 whatever
they do: it refuses that prompt either way, and the person resends. At
`2a057a6d`, before `start-record` (below), they are five writer calls, not
two: `lane-start`'s bind makes four
(`append-session-id`, `set-row-state` LIVE, `log RESUMED` and the Rule 3
stamp), and the guard's `set-row-state` on the lane it left is the fifth
(lane 2's `MEASURE.md`, sha256
`cbaa243d5bd6161f53375e1d4e44d9498ff6cf26615e420140854a914a4754d9`, (16a)).
Lane 2 estimates the bind on primary at 9.64 s median (11.51 s p95, 7.72 s at
the best of 20 runs) against about 3 s of room, so the condition of lane 3's
ruling on question 16 is met (#165 6579459518). **Decision: answer 2 exits 2
with "BINDING", and the bind runs detached,** with a writer's bounds and D7's
30 s deadline, none of it on the hook's time. **The form is the phase plan's**
("Answer 2's detached bind"; lane 3's acceptance, #165 6708889286): the guard
rewrites the session's offer file to `mode=binding`, starts `lanes-edit.sh
guard-bind` as a `setsid` child with the hook's descriptors closed, bounded at
430 s over its whole body, prints "BINDING" and exits 2. The offer file is the bind's record, and
the bind counts as live only while its pid is live with the start time the
record holds (`pid_start_of`). While it runs, and once when it ends, the guard
answers each prompt of that session from the record by an eleven-case table:
"BINDING IN PROGRESS" while it runs, "MOVED" once after it succeeds, and the
failure's why after it fails, with the window renamed back and the offer kept
for at most three attempts. That a `setsid` child outlives the hook is now
shown on Claude Code 2.1.319 (the facts table below), and lane 2's AT-F6 takes
the phase plan's seventeen cases. **The bind as one verb:** `lane-start`'s
three register writes become `start-record`, one transaction carrying the
union of their expectations (D5). The Rule 3 stamp cannot join it, since it is
no register file (Q18(A)) and Amendment 23 keeps it a git commit everywhere, so
answer 2 is three writer calls, not five: `start-record`, the guard's
`set-row-state` on the lane the window left, and the stamp.

The form first chosen here ran the writes inside the hook, with every deadline
and timer cut to `T0 + 4.25 s`, and kept the detached form as the fallback if
the bind did not fit in about 3 s on a healthy fixture. By lane 2's estimate
the bind would end near `T0 + 10.2 s`, and the 2 s connect minimum would turn
every answer 2 into "too little left", so that form is not taken. The
timeouts table (D9) carries the row: **"the guard's answer 2: nothing on the
hook's time; it exits 2 with "BINDING", and the bind runs detached in a
`setsid` child, with a writer's bounds."**

**The exit mapping.** `register read` and `register read --for guard` are the
two client forms; `unavailable` never leaves the client.

| What happened | A reader's `register read` | `--for guard` | The guard |
|---|---|---|---|
| text at `n` through the service | 0 | 0, files cached | parses; decides 0 or 2 |
| the service unavailable, the direct read succeeded | 0 | 0, files cached, `guard: direct (service unavailable)` on stderr | parses; decides 0 or 2 |
| `refuse db <cause>`, or the direct read failed | 69, as S3's table | 69 | SVC-12's declared-outage check; otherwise 2, naming the store, the cause and the two ways out |
| a read completed after the parse's start line, or an elapsed time at the start at or above the clamp's line, the direct read's cap (3.75 s, or 3.65 s where an unexpired declaration names this database) | — | 2 | 2: "too late in the hook's 5 s to verify the triple" |
| mode `index` | — (D3 asks first) | 0, `mode index` | today's files-mode guard |
| Q6(c)'s refusal of `mode=primary` | 78, `EX_CONFIG` (lane 4's code; F8) | 78 | 2 |
| any other failure of the mode question or the prefetch | — | any other code, or none | files mode with a warning (F8); under `lanes/STORE`, 2 by Q21(a)(A) |

#### SVC-9. What the service removes from the guard, and what it cannot

With the service up, part 4 shrinks to the prefetch's start (about 72 ms as
`lanes-index`, and it answers the mode too), the round trip (about 3 ms) and
today's parses on the text (about 145 ms on synthetic data, 92.9 ms median on
the real register). Without it, on primary, part 4 is
the same start, a `psql` start and connect, the head and digest query, the
image cache, and the same parses. The service removes the `psql` start, the
connect and the query: R1 measured one write at 14.7 ms through a new `psql`
process against 0.97 ms in a session; a read is not measured, and a connect
over TCP adds more. It does not remove parts 1 to 3, or the parses. That is
the honest size of Q29's question: on this host the direct guard on primary
should land near half a second warm, an order of magnitude inside its 5 s,
and the service saves tens of milliseconds of it. **Q29's figure,** as lane 2
assembled it on the real register (`MEASURE.md`, (14)): about 0.42 to 0.47 s
warm for the guard on primary through the prefetch, against about 0.55 to
0.60 s in files mode today (0.9 to 1.0 s with a cold record walk). The figure
that decides is test 10's, on the fixture.

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
  (P5's baseline and Q29's figure, which lane 2 assembled at about 0.42 to
  0.47 s warm on primary against 0.55 to 0.60 s in files mode today, SVC-9),
  with the record walk cold and warm.

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
   and names this workstation's database label; the client reads it once, at
   the prefetch, before the direct read (SVC-8);
3. the mirror it reads, `origin/main` as last fetched (the guard never
   fetches), carries a `Register-Seq` of at least the declaration's
   `mirror-seq=<m>`, the field lane 3's F11 ruling adds and `outage declare`
   records (`operations.md` §2.5): the client reads the last `Register-Seq`
   trailer on that ref's first-parent history by form C (#165 6579666731),
   exactly one
   `git log --first-parent -n 200 --format='%H%x00%(trailers:key=Register-Seq,valueonly)'`
   on `origin/main` as last fetched, with no `--grep`; it takes the first
   non-empty value, and refuses 2 if none of the 200 carries one. With `--grep`
   beside it, `-n` counts matches, not commits walked, so the walk would be
   unbounded. Form C needs every render commit to write `Register-Seq: <n>`
   in its message's final trailer block (D15).

Then the guard runs today's parser on the mirror's text and exits 0 or 2 as it
gives, with S3's `read: mirror at <sha12>, …` line and `(declared outage <utc>:
<why>)` in place of the cause. Since condition 1 holds, the mirror path is
entered only after a database read failed, so the label never stands for "the
service was down". A mirror below `m` refuses 2: "the mirror here is at seq
`<s>`, behind the declared outage's seq `<d>`; fetch it once origin carries
seq `<d>`, or wait for the outage to end" (lane 5's text, #165 6714540821).
Any other failed condition refuses 2 with S3's outage text. Every
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
| Answer 2's writes inside the 5 s, or detached (F6) | SVC-8, "Answer 2's writes": detached, after lane 2's timing, in the phase plan's form |
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
| **Shown, since lane 4's hook probe:** that a detached child started from a hook in a session of its own (`setsid`) outlives the hook under Claude Code 2.1.319, which covers the service's start (SVC-4, `start_new_session=True`) and answer 2's detached bind. At the hook's timeout Claude Code sends SIGTERM to the hook's whole process group, which a `setsid` child is outside, and a `( … & )` child in that group dies; on a normal exit it kills nothing; and it does not wait on pipes a child inherits. **Not shown:** an escalation to SIGKILL after the SIGTERM, which cannot reach a `setsid` child either | lane 4's hook probe: `claude -p` with a project-only `UserPromptSubmit` hook, five runs (`runs-165/HOOKPROBE-report.md`, sha256 `373992bb…`, R1, R1b, R2 and R3; the phase plan's "It survives the hook"), and PP-D's P1 to P4 for group and pid kills |
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
   direct read cut to `T0 + 3.75 s`, stands; lane 2's measurement keeps it
   (question 14). The timeouts table's guard row and the interfaces
   section's budget say so.
8. **The client's start.** Ruled: `lanes-index` stays the client (SVC-3).
9. **`serve` and the prefetch (a difference).** Ruled: D2's verb list gains
   `serve`, `register read` gains `--for guard` and `--prefetch`, and the
   overview's `lanes-register` row is a `lanes-index` subcommand.
10. **The new cache file.** Routed to lane 4: `$SE_CACHE_FILE.log.<lane>` for
    `lane_log_events` (`:3521`), placed in the phase plan. Answered there
    ("Question 10"): phase 1, written only on primary, with a
    `.logpath.<lane>` sidecar and one flush helper.
11. **The `psql` connection in the environment, not the argument list.**
    Ruled: no connection string in any argument list, on any `lanes-index`
    path (Roles and credentials). Routed to lane 4, which changes
    `code/lanes-index:1315`. Answered in the phase plan ("Question 11"):
    phase 1's first PR moves the URL into `PG*` variables in `_env`, for every
    connection, the admin's included.
12. **A failed start's cooldown.** Ruled: 60 s (SVC-4, item 7).
13. **The image cache's writers.** Routed to lane 4: whether the cache's form
    ([L4-c]) allows the service as a second writer, or the service keeps its
    images in memory only. Answered in the phase plan ("Question 13"): the
    form makes any writer safe, so the service may write through (SVC-7).
14. **The register read's and the parse's real cost.** Measured by lane 2
    (`MEASURE.md`): the parse is 92.9 ms median, 156.6 ms p95 and 99.2 ms cold
    on 2,590,927 bytes, and Q29's figure is about 0.42 to 0.47 s warm on
    primary against 0.55 to 0.60 s in files mode (SVC-8, SVC-9).
15. **Across containers.** Not needed by SVC-1 as ruled; the facts table keeps
    it as not shown.
16. **Answer 2's writes (F6).** Ruled: inside the hook, cut to `T0 + 4.25 s`,
    with the detached form as the fallback if lane 2's timing of the bind said
    so. Lane 2's estimate, 9.64 s median against about 3 s of room, met that
    condition, so the detached form applies (#165 6579459518): answer 2 exits
    2 with "binding in progress", the bind runs detached, and the timeouts
    table's row says so (SVC-8). The phase plan gives the form, which lane 3
    accepted (#165 6708889286): a `setsid` child, `guard-bind`; exit 2 with
    "BINDING", which replaces "binding in progress" for answer 2 itself; the
    offer file as the record, with pid plus start time as its liveness test;
    and the guard's eleven-case table.
17. **The `SessionStart` block (F24, a difference).** Ruled: it stays a mirror
    reader that only starts the service (SVC-12). The spec's requirement reads
    as SVC-12 gives it.
18. **The declared-outage exception (F7) and `mirror-seq` (F11).** Ruled:
    `mirror-seq` is a field of the declaration, which `outage declare` records
    in `operations.md` §2.5, and the guard's check of it is the client's (SVC-12).
    D20, S3's declared outage and both specs state SVC-12's three conditions.

## The phase plan

Lane openRepoTools-4's section, "the phase plan and the renderer", folded in
place of the placeholder by lane 3's writer. The source is
`/workspace/projects/.lane-worktrees/openRepoTools-4/.tmp-coordinator/runs-165/section-lane4-5f26bf0a.md`
(sha256 `5f26bf0ae1ed1ded51dcf685902a5ad2d643695a9de09f7e5d55b529ee74c4ff`,
839 lines), posted as #165 comment 6708867768 and accepted by lane 3 in
6708889286. From the next paragraph to the end of "Not established" the text is
lane 4's lines 3 to 839, byte for byte, except two changes that lane 3's
rulings of #165 6710770304 make, both in lane 4's own words: in "The bind as
one verb", lane 4's corrected `start-record` table (6709560550) replaces the
table that had `lane-start` die on 69, 75 or 3, with lane 4's paragraph on why
the stamp still runs on a resume; and "The in-doubt rule at the deadline" gains
lane 4's reading of LR008 (6709038351) as its last bullet. Commit K makes
three more, worded by its writer: lane 3's ruling on lane 1's v2.4 (#165
6711233420, point 1) adds the act's generation to `act-begin`'s output, in the
interfaces table's `act-begin` row and as a new sentence in "The acts and
their expectations"' bullet "The base."; and lane 3's D9 ruling (#165
6711028142) names the mode query's 2 s client timer in F8's "a hang" row. Commit J makes more, as lane 3 ruled (#165 6711564365). From lane 4's check of v2.4 (6711545001; its report, `V24-CHECK-report.md` in the same folder, sha256 `7ab46ad752c74e1594cbb3a8481649f047cda03b0655f9dd4117a306f09eab75`): the report's replacement text for M1 to M4 in the interfaces table's `register read` and `act-begin` rows, the bind record's `act` row, the guard's answer 2, and "The acts and their expectations"' bullets from "The base." to "Re-basing.", now six with "The form."; "The scopes." also gains the confirmation (b)'s split and refusal, and "What the journal needs" a pointer that its agreement test, S7, is lane 2's (the routed list); a new bullet after "Before a launch", the report's text for the confirmation (a), carries M4 and S1; `lane-start`'s re-read at `:2614` re-bases first (M3); and the check's place reads "S1's step 3.2" (S5). From lane 4's text of 6711623039: the paragraph "Two reads stand for more than one name." after the scope table (lane 4's half of S3, and S4), the three read rows it changes, lane-handoff's paragraph and the handoff skill's writes (N3). From lane 4's check of v2.5 (6711949237): the arms row for `lane-start`'s writes has any 2 at `:4181` die, LR010's included. Commit L makes two more, as lane 3 ruled on the review of commit J: "D3's refusal" gives the delta's fixed text in place of lane 4's (R1's M1), and the `start-record` table's row for a 2 dies on any 2, LR010's included, as the arms row does (commit J's writer's item 1). Commit N folds lane 4's forty texts, N1 to N40, as lane 3 directed (#165 6714767210; `M-TEXTS-lane4-1107-2359.md` in the same folder, sha256 `a18c36ecacc247829b5614e465c4ea7205ccc148e25d84331d593ed43640530b`, with the writers' reports `M-ATTEMPT-report.md`, `M-ATTEMPT-R2-report.md` and `M-BIND-report.md` beside it): R2's S3, with the new subsection "The tail, once"; lane 4's half of R2's S1; R2's S6, the bind's bound of 430 s over its whole body; R2's N2 and N5; lane 1's text 9, v2.6's lookup, at its ten places; a finding of lane 4's, row 43; and lane 4's own change, the `start-record` table's labels in the delta's terms (R1's N6). Its line 1 is this heading. Lane 4 wrote
it against this file at `21f0191c` and the code at `2a057a6d`, so every
`design.md` and `proposal.md` line it cites is at `21f0191c`, before this fold
moved them; the sections it names (D5, S1, S2, SVC-8, the facts table) are
where to look now. Its evidence is in the same folder, read-only:
`DESIGN-PP-A-report.md` (sha256 `eec2754eebaf017ae2cbc743024fa346b8ffc4fef7c416822faf814f36de2efa`),
`DESIGN-PP-B-report.md` (`f4b98ffaa928c5943924ac9c8ee13270fd1d7cbd525c7debdbfae4a3339df566`),
`DESIGN-PP-C-report.md` (`22ee181af8172cc41874eb980c7cfb381467615ce8c3bda1b7dc738935b78e09`),
`DESIGN-PP-D-report.md` (`0174f555987b76ae9e4a2babdadf08a283d1a7b64300f0a6d6dc0cc437d4c193`),
`DESIGN-RENDER-report.md` (`da354e04040af2ca2e4c50052603d2db2476f734421240731cebe4b9444b54e1`),
`DESIGN-COST-report.md` (`4940f12d7a24a141ccc0eefcfc20434c7191936dee8058fe82854d718ac18afa`),
`DESIGN-CHECK-report.md` (`1bd8e8357631a6873a58702749787cf22b217d70e276715c5c21e4c4a342ee85`),
`HOOKPROBE-report.md` (`373992bbf5e8091c0bf2040f73573c8b2e65a75a1cf8b6fab0bf7f63ee62b521`)
and `DESIGN-ASSEMBLE-report.md` (`45a1de380c22925eb096f718e83aa2316894c0b779afc33e37c143c5abea1e73`),
with the probes' raw output in `sandbox-RENDER`, `sandbox-PP-D` and
`sandbox-HOOK`.

**What lane 3 settled, and where it landed** (#165 6708889286):

- **The bind as one verb.** `start-record` makes `lane-start`'s three register
  writes in one transaction; the Rule 3 stamp stays a git commit to the
  handoff, outside the register; so answer 2 is three writer calls, not five.
  In D5, the interfaces section, SVC-8's "Answer 2's writes", both deltas
  (`specs/register-service/spec.md`'s guard-client requirement and
  `specs/sql-register/spec.md`'s requirement on `lane-start`'s bind) and the
  proposal's guard bullet.
- **D5 stands,** one transaction per verb, with `start-record` as its one
  composite verb, and F5's expectations are `act-begin` plus six scopes, in
  place of `--expect-row <lane> <digest>`. In D5, S1's step 3.2 and
  `specs/sql-register/spec.md`'s readers' requirement, with a scenario for an
  act's own earlier write. D5's three conditions (lane 4's row 22) join the
  table for Brett beside D5.
- **The detached bind:** a `setsid` child, `guard-bind`; exit 2 with
  "BINDING"; the session's offer file as the record, with pid plus start time
  as its liveness test; and the guard's eleven-case table. In SVC-8's "Answer
  2's writes", the interfaces section, the timeouts table's answer-2 row, lane
  5's ruling 16, SVC-4's item 2, the facts table, whose row for a child
  outliving the hook is now shown, and `specs/register-service/spec.md`'s
  requirement on the 5 s budget, with scenarios.
- **The renderer:** the `flock`'s handshake, both halves; `m` found by form C,
  paged with `--skip` and bounded by `LANES_GIT_TIMEOUT`, with nothing pushed
  and the reason recorded where no trailer is found; 64 seqs a pass, six push
  attempts, and a lifetime cap with one handover; and the deploy key's push to
  an explicit SSH URL in an isolated environment. In S2's renderer, D14, D15,
  and `specs/sql-register/spec.md`'s renderer and gate requirements.
- **[L4-c]** at about 0.55 to 0.61 s an attempt, and **[L4-1] to [L4-5]**
  passing with the `flock`. In S1's and S2's lists of what lane 4 must
  confirm, now answered, and in D14; the in-place form is not taken.
- **For Brett:** lane 4's rows 18, 22 and 40, with the facts of rows 2 and
  27, in "Differences from Amendments 22 and 23, for Brett".
- **Rows 34 and 35** are lane 3's, as lane-tools findings: "Known defects in
  today's code", under Context. Phase 1's first guard PR fixes both.

**The rest of lane 4's rows, as lane 3 ruled** (#165 6710770304). The
"Decision" column of lane 4's "Differences from the frame" table is lane 4's,
written before these rulings, so a row may still read "lane 3" or "open":
- **Row 6:** `lanes-index` composes the render commit's message itself, so the
  git version and the `trailer.*` configuration are not inputs to its sha; D15
  says so.
- **Row 8:** agreement test 3's unit form ships in phase 2, since a phase lands
  tested; the rollout summary says so.
- **Row 10:** phase 3 is `lanes-index` and `tests/` only; phase 2 ships the
  renderer fork and `<c>` (S2's renderer, and the rollout summary).
- **Row 12:** the verb runs as a child process that suspends at the seam (S1's
  step 2, and the flow of one write).
- **Row 13:** the flag also skips the mode query and D17's reads (S1's step 2).
- **Row 15:** closed by lane 1's v2.3, whose step 9 raises LR008 past
  `not_after` (§1.3; S1's "A write in doubt"; D8). Lane 4's reading of LR008
  is in "The in-doubt rule" and S1's outcomes table.
- **Row 16:** the pending record as lane 4 gives it: a 15-minute age, a digest
  over `LANES_LANE`, `LANES_SESSION`, the verb and its arguments, and a printed
  rerun carrying `LANES_WRITE_GENERATION`, a new variable (S1's "The write id"
  and "A write in doubt"; D8).
- **Row 20:** accepted, and closed by lane 1's v2.4 (#165 6711221999):
  `journal.act_id` and each entry's touched keys, in `journal_scopes`, with
  the check as `commit_write`'s step 3b and LR009 (§1.3's "The act"; D5).
- **Row 24:** a rerun that finds its write id committed runs the verb's
  after-COMMIT tail, so that `rename-lane`'s `lane_state_rename` still runs;
  every such tail must be safe to run twice; since commit N, at most once
  (lane 4's "The tail, once"), and S1's "A write in doubt" lists each verb's
  tail. Lane 2 adds a test per tail after the review.
- **Row 37:** the bind's 430 s bound over the child's whole body (R2's S6),
  with `LANES_GIT_TIMEOUT=45` in the child, accepted as arithmetic: 278 +
  2`v` + 6`N_r` s, 384 s at `v` = 2 and `N_r` = 17, under the body's 420 s;
  phase 1's tests measure the child's bind against it.
- **Rows 18, 22 and 40** are Brett's, and go to him with the spec PR (the
  differences table for Brett).
- **`start-record`'s exits** follow lane 4's corrected table, which agrees with
  round 1's 2A: only the expectation's 2 stops both paths (lane 2's open point
  18). **`start-record` runs in both modes,** as lane 4 plans it: in files mode
  its three register commits and pushes become one, which the Goals, the
  Non-Goals and both deltas now carry.

So after these rulings, lane 1's v2.4 and v2.5, and lane 4's checks of v2.4
and v2.5, the only items still open from lane 4's section are rows 18, 22 and
40, for Brett, and one change for phase 2 on lane 4's side, `key()`'s zeros
trimmed as text (#165 6711949237; the routed list at the end of this file).
The review of commit J's items for lane 4, and lane 1's text 9, are folded
by commit N (#165 6714767210).
The four points lane 3's rulings on v2.4 gave lane 4 to confirm (#165
6711233420) are confirmed (6711545001). Round 1, which lane
4's basis below holds as provisional, is RULED since (#165 6710653292).

**New and unproven:** the names this section adds that the proposal does not:
`act-begin`, `LANES_ACT`, `LANES_EXPECT` and the six scopes; `canon-lane --walk`; `start-record`;
`guard-bind`, `pid_start_of` and the offer file's modes `binding`, `bound`,
`bind-failed` and `name-drift`; `LANES_REGISTER_SCRATCH`;
`LANES_WRITE_GENERATION` and `LANES_REGISTER_PENDING_MAX_AGE_S`;
`RENDER_GIT_HALF`; `not_after`; `LANES_READ_MIRROR`; the `.logpath.<lane>`
sidecar; the image cache's `register-images/` layout; `LANES_REGISTER_SEAM_CAP_S`
(N4), `LANES_BIND_BOUND_S` (N15) and `seam.out` (N4); `parent.pid` (N4), the call directory's `snapshot`, `files` and `preimages` (N8); the pending record's `call`
lines and `tail` key (N8); the bind record's `bound_s` and `bounded` (N15);
`register snapshot --at` (N1), `journal --not-after` (N2) and `guard-bind --body`
(N13); and the renderer's 64-seq batch and 16-pass cap. As lane 3's ruling on v1
has it for every new object
(#165 6578020193), each is accepted as new and unproven, and needs a test in
lane 2's plan (AT-F6 for the bind; tests 3, 4, 5, 6 and 9 for the rest) before
it counts as proven.

Lane openRepoTools-4's section (#165 comment 6576888068). It settles, for each of the seam split's three phases, the functions, files and call sites that change, and the interface each phase leaves for the next. It answers the claims marked [L4-a] to [L4-c] in S1 and [L4-1] to [L4-5] in S2, and the items the frame review routed to lane 4.

**Its basis.** The frame at lane 3's `21f0191c` (`design.md` 3,588 lines, sha256 `df00e47f…`; `proposal.md` `e6475a46…`), which folds lane 1's v2 with its v2.1 and v2.2, lane 2's measurements and the 11-02 follow-ups (6708646285); round 1 ("1A 2A 3A 4A") held as provisional; round 2's recommended options as working assumptions; lane 3's rulings to 6579562840 (2026-11-02T14:23Z), its resume status of 2026-11-07 (6708470380) and its fold post (6708646285); lane 1's v2 section (`section-lane1-v2.md`, `dd061272…`) for the schema's names; and the code at `2a057a6d`, which stays the pin. Every `code/` line is at `2a057a6d`, and every `design.md` and `proposal.md` line at `21f0191c`. The renderer's claims rest on a prototype and the detached bind's on probes, all run in sandboxes; everything else is a static reading of the code.

### The three phases, and the interfaces between them

The frame's wording governs (Q6, 1A, `design.md:131–135`; the rollout, `:3429–3445`):

1. **Phase 1, now, on `2a057a6d`.** Everything that does not run through `lanes-edit.sh`'s three write seams: the resolver (`code/lanes-edit.sh:1053–1056`), `handle_preexisting` (`:2701`) and `commit_push` (`:2292`).
2. **Phase 2, after slices 1a (#282) and 1b (#307) land in the code leg.** The write path: S1's attempt loop at the commit seam, the refusals of direct writes, S6's checks, and the marker check after S3's fetch and in the publish act.
3. **Phase 3, then.** The renderer's git half, rollback's push, and agreement tests 3, 5 and 6. Its last PR lifts Q6(c)'s refusal.
4. **The service** (`lanes-index serve`, SVC-3) follows phase 3, as 007's last slice (Q29(a); SVC-5).

| Interface | Phase 1 ships, final | Phase 2 uses | Phase 3 uses |
|---|---|---|---|
| `lanes-index mode` | 0 with `index` or `primary`; 78 for Q6(c); 2 for a refused configuration | entry (D3) | lifts 78 |
| `register snapshot` | the write-id lookup first, checked against lane, verb and `args_sha256` (D8; LR007), returning, when found, the entry's seq and its `journal_files` rows; then the switch with its marker state and `generation` (lane 1's v2); `n`; the database clock (D13); the images of `--files` or `--all` at `n`; `--tree <dir>`, which builds the scratch tree (phase 2, below); and `--at <k>`, which builds it at seq `k` instead, with `repos.tsv` from the entry's `repos_blob`, for the replay ("The tail, once") | the attempt loop | — |
| `register commit` | the attempt directory's format (paths, renames, removals, images, base digests, message, author, the act's expectations, the `repos.tsv` blob id and alias sets, D19); `not_after` (F3); the call `commit_write(n, write_id, REGISTER_SCHEMA_VERSION, generation, entry)` (lane 1's v2); exits 0 `committed <seq>`, 75, 69, 2, 3; no `--end-line` or `--hook-pid`, since answer 2 runs detached | the commit seam (D4) | `render --revert`, `import --single` |
| `register read` | `--prefetch --cache <file>`, `--files` or `--all`, `--for guard`; text at `n` with digests, and the `n` and the register `generation` it read, in the same snapshot; 0, 69, 78 or 2 | — | — |
| `journal` | `--write-id <id> --not-after <n>`, where `<n>` is the largest `not_after` of the write's commit calls. It waits on its own estimate until `<n>` is at most 2 s ahead, then calls lane 1's v2.6 `find_write(<id>, <n>)` at READ COMMITTED, in a transaction it rolls back, with D9's lookup bounds and tries ("The in-doubt rule"). When found, it returns the seq and its `journal_files` rows. Also: `--since <m>` (message, author, commit UTC, edits, digests, `journal.operator`); `--last --path --grep` | the in-doubt lookup (S1) | the renderer |
| `render` | `--images --at <k> --out <dir>`, the pure function; the image cache | the snapshot's images | `--checkout`, `--until`, `--revert` |
| `lanes-edit.sh act-begin` | `<act-id>@<seq>:<generation>` on primary, the head seq and the register generation read in one snapshot; `<act-id>@files` off primary; `--rebase` (the acts, below) | the scripts' `LANES_ACT` | — |
| `lanes-edit.sh guard-bind`, the offer file's bind record | the record's form, `pid_start_of`, the guard's case table ("Answer 2's detached bind") | adds `act`, `write_id`, `generation` and `not_after` to the record, and the child's `find_write` lookup | — |
| `lanes-edit.sh start-record` | — (phase 2's own verb) | `lane-start`'s bind, one transaction with the union of its expectations | — |
| schema | every column phase 3 reads: author, commit UTC, `journal.operator`, the mirror state (lane 1); and lane 4's two asks, `act_id` and the touched keys (the acts, below) | — | no migration |

- **Phase 1 to phase 2:** phase 2 changes nothing in `lanes-index` if phase 1 ships the four `register` and `journal` contracts complete, with `--tree`, `not_after` and the expectations, and the dispatcher's mode arm with its "primary: exit 69" placeholder for phase 2 to replace.
- **Phase 2 to phase 3:** phase 3 changes nothing in `lanes-edit.sh` if phase 2 ships the renderer fork, `<c>`, the author identity, the suppressed register nudge and test 3's unit form (phase 2, "What phase 2 ships for phase 3").

### Phase 1, now, on `2a057a6d`

| File | Function or site | Line | What changes |
|---|---|---|---|
| `lanes-index` | `SCHEMA_VERSION` | :101 | Stays #160's; the register's version is lane 1's (D1). A constant `RENDER_GIT_HALF = False` sits beside it: D3's refusal lives here. |
| `lanes-index` | `load_config`, `known` | :248, :280, :289, :296 | Known keys gain `mode`, `register_url`, `register_passfile` and `render_key` (`design.md:2351`). `mode` is `index` or `primary`; absent is `index`. While `RENDER_GIT_HALF` is False, `mode=primary` is a `Refusal` (2) for every subcommand except `mode` and `register read`, which exit 78. The password check at :296 also covers `register_url`; `render_key` is checked with `_check_private_file` (:234). |
| `lanes-index` | `PostgresStore._env`, `_run` | :1299–1308, :1310–1317 | Question 11, below. |
| `lanes-index` | `PostgresStore.__init__` | :1280 | A third role, `register-writer`, beside `reader` and the indexer. |
| `lanes-index` | `main`, the dispatch, `USAGE` | :2618, :2666–2690, :2563 | New verbs: `mode`; `register snapshot`, `commit`, `read`; `journal`; `render --images`; `import --at`; `outage declare` and `clear`; and lane 1's operator tools, `bootstrap`, `ready` and `set-mode` (lane 1's v2, 2.4). `status` and `reconcile` are extended. `cutover` and `rollback` ship as their transactions only, runnable only against a scratch schema while `RENDER_GIT_HALF` is False; their pushes are phase 3. |
| `lanes-index` | `cmd_status` | :2338 | Adds the switch, head `n`, generation, the mirror's last `Register-Seq` and the lag, `diverged at` (D16), pending records (D8), and a declaration left in place (D20). |
| `lanes-index` | `cmd_reconcile`, `cmd_sync` | :2279, :2204 | On primary, `sync --source register` does nothing and says so (S2's nudge). `reconcile`'s mirror-equals-SQL check ships here, read-only; its re-render is phase 3. |
| `lanes-index` | `read_snapshot`, `cat_blobs`, `git` | :448, :496, :404 | Reused by `import --at <C>`. `build_records` (:765) is not the import's parser, because it drops lines (R2D I-6); the full-fidelity loader is lane 1's. |
| `lanes-index` | `REGISTER_KIND_RE` | :680 | Grows to Q17(B)'s kinds for the commit's classifier (S6). |
| `lanes-index` | `state_dir()` | :173 | Holds the image cache (question 13) and `register-pending/` (F10). |
| `lanes-edit.sh` | the dispatcher, after the workspace check | :15131–15141, before :15143 | The read door, `reg_open` (below), once, in the main shell, before the arm. The fourteen writing verbs of the write guard (:15103) ask the mode there instead, once. |
| `lanes-edit.sh` | beside `index_open` | :2585 | `reg_open`: one `lanes-index register read --prefetch` call, which also answers the mode (D3). |
| `lanes-edit.sh` | the new verb `act-begin` | — | The base of an act's expectations (the acts, below); with it, the commit's expectation check against the fixture, and the read arms of "The callers' default arms". |
| `lanes-edit.sh` | `log_sync` | :6202 | On primary with the door open: no fetch and no flush (D12). Elsewhere, after its fetch, the marker check (F17). |
| `lanes-edit.sh` | `guard_run` | :10391 | SVC-8's prefetch before :10518, its three read sites, the marker check and SVC-12's declared outage. |
| `lanes-edit.sh` | `register-row-local`, `verify-row` | :17937, :15144 | Read SQL on primary (D11). `verify-row` has no caller. |
| `lanes-edit.sh` | `guard_answer`'s choice 2; the offer site | :11128–11207; :10648–10659 | Answer 2's detached bind (ruling 16): the offer file's `mode=binding` record, the fork, the case table and the rename back on failure. |
| `lanes-edit.sh` | the new internal verb `guard-bind`; the new helper `pid_start_of` | — | `guard-bind` runs the bind in the detached child and writes nothing itself; `pid_start_of` is the liveness test shared with F10's pending record. |
| `openRepoTools` | `GUARD_COMMAND`, `GUARD_TIMEOUT`, `guard_block_text`, the conflict arm | :2343, :2344, :2346–2353, :3317–3331 | Unchanged in every phase (the guard, below). |

**The service: stop at its interface.** Phase 1's `register read` is SVC-3's client with only its direct path (`design.md:2632–2667`). The socket, the `.direct` flag (SVC-8) and the service's write-through to the image cache (SVC-7) come with `serve`, which a workstation asks only on primary (SVC-5), and none can be primary before phase 3. Phase 1 fixes what `serve` must also return: the files at `n` with per-file digests, written by the client into the cache files below.

#### D3's refusal: where it lives, and its code (F8)

- **One constant, `RENDER_GIT_HALF`, beside `SCHEMA_VERSION` (`code/lanes-index:101`).** `load_config` (:248) refuses with 2 for any subcommand but `mode` and `register read`. `lanes-index mode` and `register read [--for guard]` exit **78** with the delta's fixed text, "`mode=primary is not available in this build: the renderer's git half is not installed, so a write could not be mirrored; nothing was written`" (`specs/sql-register/spec.md`, agreement test 14). `cutover`, `rollback` and `set-mode primary`, against a schema that is not a scratch schema, exit 2.
- **Q6(c)'s code is 78, `EX_CONFIG`: lane 4's pick (D3; F8), never 2, accepted by lane 3 in 6579562840** for D3, F8's text, the proposal's test 14 and AT-F8, and folded at `21f0191c` (D3, `design.md:317–319`). It is free: `lanes-index` exits only 0, 1, 2, 3 and 64, and `/usr/bin/grep -c -w -E 'exit 78|return 78|die .* 78|78\)'` gives 0 in `lanes-edit.sh`, `lane-start`, `lane-end`, `lane-handoff`, `lane`, `lane-rename`, `lane-worktrees`, `lanes`, `lanes-index` and the three skills, `handoff`, `lane-swap` and `restart` (the only 78 in `lanes-edit.sh` is a byte count in a comment, `:1611`). Only the mode-query site sees it. Lane 1's v2 already refers to "lane 4's code" (renames line 79), and `ready`'s check 5 looks for it.
- **Not in `lanes-edit.sh`,** because the mode is asked, not parsed (D3). **Not an environment variable,** which any lane could set.
- **Lifted in phase 3's last PR,** with a test that the constant is True exactly when `render --checkout` exists.
- **Tests of the SQL path in phases 1 and 2:** bash tests use a stub `lanes-index` that answers `primary`, as `tests/test_lanes_index.py:570–576` stubs the nudge; real-engine tests run a copy of `lanes-index` with the constant flipped, made in the test's temp dir.

What `lanes-edit.sh` does with the answer in phase 1 (the full failure table is F8's, under "The acts and their expectations"):

| Answer | Writers (phase 1) | Readers |
|---|---|---|
| no configuration, and `LANES_INDEX_CONFIG` unset or empty | today; no process started | today, plus the marker check |
| `index` | today | today, plus the marker check |
| 78 | exit 69, repeating the text (agreement test 14, phase 1) | deciding reads 69; listings and `SessionStart` read the mirror with a note; the guard 2; `managed-projection` 1 |
| `primary` (only from a `lanes-index` whose constant is lifted: version skew) | exit 69, "this `lanes-edit.sh` has no SQL write path", never files; phase 2 replaces this arm | SQL, as below |
| any other failure | files mode with a warning (D3) | files mode with a warning; under `lanes/STORE` the marker check refuses |

#### The readers' SQL path, entered at `register_text_checked`

The form generalises SVC-8's prefetch from the guard to every reader (D17). Reads run inside `$( )` and pipelines, where a variable set is lost; that is why today's per-invocation caches are files beside `$SE_CACHE_FILE` (`code/lanes-edit.sh:3449`), written by temp file and `mv -f` (`:3485–3488`, `:3622–3623`). So `reg_open` runs in the dispatcher, in the main shell, before the arm: one call, `lanes-index register read --prefetch --cache "$SE_CACHE_FILE" --files <set>` or `--all`. On a configured workstation that one Python start also answers the mode; on an unconfigured one nothing starts. On primary it writes the cache family at one head `n`, so one verb reads one snapshot (R2B S-13). A failed read exits in the main shell with the arm's class, which no command substitution can swallow (R2B S-1 to S-5).

| Class | Arms | On a failed read |
|---|---|---|
| decide (default) | every D and H arm of R2B §2 not listed below | 69, nothing on stdout |
| mirror | `lanes`, `history`, `session-start`; `window-lane` and `swapped` only where the caller sets `LANES_READ_MIRROR=1` (`code/lane-handoff:1655`, `:1666`; `code/skills/handoff/SKILL.md:210`, `:218`); the variable is ignored on every other arm | `read: mirror at …` on stderr, then the mirror read |
| guard | `guard` | SVC-8's exit mapping (`design.md:3019–3030`) |
| projection | `managed-projection` | 1, UNKNOWN (S4) |
| none | the L and F arms (`workstation`, `fetch-age`, `window-session`, `transcript-holders`, …) | no door, no call |

A hygiene test asserts that every dispatcher arm is classified. On primary, a primitive reached with no prefetched file makes one more `register read`, never a git read (SVC-8's rule); it returns the paths changed in `(n, n′]`, and where any of them is already cached, the whole family is refetched at `n′`.

On primary each read site reads its cache file and never `origin/<branch>`; in files and `index` mode each is unchanged:

| Function | Line (`code/lanes-edit.sh`) | Cache file | Change |
|---|---|---|---|
| `register_text_checked` | :3597 (cache :3603; git :3612) | `.register` | a missing file never falls to `register_published` or git (:3608–3614) |
| `register_published` | :3639 | `.paths` (new: the render set's paths at `n`) | answers 0 or 8 from `.paths` |
| `archive_text` | :3671 | `.archive` (new) | uncached today; prefetched on primary |
| `lane_alias_text` | :5367 (cache :5369, `-s` test :5375, empty form :5434, git :5395) | `.aliases` | an absent table is written as `# no lane aliases` (SVC-8) |
| `lane_log_events` | :3521, before `log_path_ci` at :3522 | `.log.<lane>` (question 10) | read before `log_path_ci` |
| `log_path_ci` | :2991 (ls-tree :2999) | `.paths` | resolved case-insensitively in `.paths` |
| `lane_log_exists`, `known_lanes` | :3545, :4202 | `.paths` | |
| `remote_log_events` | :3408 (ls-tree :3431, show :3439) | `.paths`, `.log.*` | runs `parse_log_stream` (:3373) unchanged on the prefetched logs |
| `swapped_lanes` | :9074 (rank :9087–9091) | none | rank = head − `lanes-index journal --last --path <log> --grep <key>` (R2B S-14) |
| `retired_identity_hits` | :3761 | through `archive_text` | |
| local-file unions | `row_of_lane_local` :5269 via `session_ids_local_of_lane` :5689 (at :16930–16931, :7428–7429); `lanes_register_index --local` :8095 (at :8258, :8471); `managed_local_hint` :13379 (at :13365, :18478); `lane_position_rows` :8870; `row_stamp_is_unknown` :9198 (at :9326) | none | the local half is skipped: no local row can be ahead of SQL (D11) |
| `log_sync` | :6202 (drop :6207) | none | no fetch and no flush with the door open |
| writer-side reads | `first_landed_of` :11458 (:11480); :11827, :12629, :12733–12736, :12983, :13027, :15796, :15840, :15887 | none | phase 2: they run on the scratch tree |

The lifecycle scripts:

| Site | Line | Change | Phase |
|---|---|---|---|
| `lane-start`'s `rows_for_lane` and `row_names_for_lane`, awk over `$LANES_FILE` | `:1576`, `:1588` (read `:1591`, refusal `:1609`) | through a register verb | 1 |
| `lane-end`'s awk over `$LANES_FILE` | `:1729`, `:1741` (read `:1744`, refusal `:1749`) | through a register verb | 1 |
| `lane-worktrees`' `git grep` of `origin/<branch>` | `:706`, `:997`, `:5556` | through a register verb; 69; nothing deleted | 1 |
| the default arms | "The callers' default arms", below | the read arms | 1 |
| `lane-handoff`'s and the skill's naming reads | `lane-handoff:1655`, `:1666`; `SKILL.md:210`, `:218` | `LANES_READ_MIRROR=1` | 1 |
| `lane-start --bind`'s idempotent-bind read rule | `:2437`, `:2448`, `:2132–2133` | no register line again where the binding and the cell already name this uuid (answer 2's reruns) | 1 |
| `lane-start`'s three register writes | `:3848`, `:3985`, `:4178` | one `start-record` call, with its arms | 2 |
| `lane-handoff`'s `PAUSED` writes | `:2803`, `:2874`, `:2890`, `:2913` | expectations, 69 and 3 handling | 2 |
| the skill's commit block | `SKILL.md:446–450` (`pull --rebase` `:449`) | slice 3, #311, not this change, under Q28(i) | — |

**Seam drift.** Slices 2a and 2b also edit `lane-start` and `lane-handoff`. Phase 1's reader edits there are small separate PRs, re-cited at each landing.

#### The guard

- **The arm** (`code/lanes-edit.sh:16856–16882`) is unchanged: it already maps every code but 0 and 2 to 2 (`:16875–16881`). `CLAUDE_NO_LANE=1` still exits at `:648–654`.
- **`guard_run` (`:10391`), phase 1, as SVC-8 has it** (`design.md:2870–3030`): one `register read --for guard --prefetch` before `:10518`, passing `$$` for `T0`; its cache sites, `.register` for `:10518`, `:10546`, `:10585` and `:10603`, `.aliases`, and `.log.<lane>` for `lane_binding_scan` at `:10707`; the budget lines `T0 + 3.75 / 4.25 / 4.4 s`; on a failed read, SVC-12's three conditions, checked by the client; on files and `index` mode, the marker check (F17) on the ref it reads.
- **The post-bind flush at `:11064`** drops the whole read family, `.log.*` included; on primary the refill is a second `register read`, never git.
- **Answer 2 runs detached** (lane 3's ruling 16, 6579459518), replacing the in-hook cut of its writes (`:11171`, `:11199`) to `T0 + 4.25 s`, which the frame dropped at `21f0191c`. It is phase 1's first guard PR (differences row 34). Lane 2 counts four writer calls in `lane-start`'s bind at `2a057a6d` (`append-session-id`, `set-row-state` to LIVE, `log RESUMED`, the handoff's Rule 3 stamp), so five with the guard's `set-row-state` (6579356984). Phase 1 ships the guard's half: the `mode=binding` branch at the offer site (`:10648–10659`), `guard-bind`, the fork and `pid_start_of`; phase 2 ships `start-record`. Both are in "Answer 2's detached bind, and the bind as one verb".
- **The hook string is not touched in any phase,** and the detached form keeps it so. `--install`'s only idempotence is the exact command string, so a changed string is a `conflict` that dies with nothing installed (`code/openRepoTools:3317–3331`); the workBenches launcher ensures the same strings in each profile; and every profile carries `"timeout": 5` (`:2350`). So the whole budget fits `GUARD_TIMEOUT=5` (`:2344`), and a phase-1 test reads both files and asserts the guard's last line, `T0 + 4.4 s`, is below `GUARD_TIMEOUT`.

#### F17: the marker check for readers

- **What it is.** One function in `lanes-edit.sh`, run only when the mode is not `primary`: `git cat-file -e "origin/$LANES_BRANCH:lanes/STORE"` in `$LANES_REPO` where `have_remote_ref` (`:3399`) holds; under `LANES_NO_GIT=1`, or with no origin ref, a test of `$LANES_DIR/STORE` in the tree that read uses (`design.md:2028–2040`).
- **Where.** On the ref the read reads, after any fetch, in the main shell: in `reg_open`, before the arm; at the end of `log_sync` (`:6202`), once its fetch has moved the ref (all 40 `log_sync` calls are bare statements, none inside `$( )`); in `guard_run`, before `:10518`; and in `session_start_block` (`:9224`), which only labels its read.
- **Outcome by class.** decide: 69 with Amendment 22's text; guard: 2; mirror: reads on with the `read: mirror` line; projection: 1.
- The writers' check, after S3's fetch and in the publish act, is phase 2.

#### Question 10: `$SE_CACHE_FILE.log.<lane>`

- **Phase 1,** with the guard's direct prefetch; the service later changes only where its bytes come from.
- **Written** by `lanes-index register read --prefetch`, only on primary. In files and `index` mode nothing writes it, so `lane_log_events` is byte-identical there: its consult is one `-f` test.
- **Read** by `lane_log_events` (`:3521`) before `log_path_ci` (`:3522`); its ten callers read through it unchanged (`:4185`, `:4458`, `:7123`, `:7291`, `:8200`, `:9830`, `:10206`, `:12741`, `:17548`, `:17617`). `remote_log_events` (`:3408`) reads the same files on primary.
- **The key** is `$1` as received (SVC-8), used only where `$1` is in `check_lane_name`'s class (`[A-Za-z0-9._-]`, not opening with `.` or `-`, `:2916–2920`), tested without dying; any other name takes the second-read path.
- **The form.** The log's text, plus a sidecar `.logpath.<lane>` holding the resolved `lanes/log/<File>.md` at `n` (empty: no log at `n`), so `parse_log_stream "<path>"` names the file in its `unreadable:` warnings as the mirror read does. The sidecar is lane 4's addition to SVC-8.
- **The family.** `.log.<lane>` is the first per-lane sibling of `.register` (`:3603`), `.aliases` (`:5369`), `.aliaserr` (`:5344`), `.warn` (`:3466`), `.records` (`:4666`), `.forks` (`:7342`) and `.live` (`:7926`). Cleanup's glob `"$SE_CACHE_FILE".*` (`:1254`) removes it with no change. It is flushed wherever `.register` is dropped today (`log_sync` `:6207`, `index_open` `:2650`, the guard's post-bind `:11064`), by one helper that drops `.register`, `.aliases`, `.archive`, `.paths`, `.log.*` and `.logpath.*` and empties `LANES_REGISTER_CACHE` (`:3596`).

#### Question 13: the image cache, and the service as a second writer

- **Judged from the code's caches.** Both existing caches already have many writers and no lock, made safe by atomic rename: the per-invocation family (`mv -f`, `:3485–3488`, `:3622–3623`) and `lanes-index`'s state files (`os.replace`, `code/lanes-index:1534`). The per-environment image cache has many writers by design anyway (every snapshot and `register read` there), so the service is one more, not a second in kind.
- **The form, safe for any writer:** content-addressed, immutable objects `register-images/objects/<digest>`, written by temp file and rename (two writers of one digest write the same bytes); per-seq manifests `register-images/seq/<n>` (path to digest); a `head` file that moves only forward, under a short `fcntl.flock` as `SyncLock` takes one (`:1539–1606`). A reader uses an object only when its name equals the digest the database gave at `n` (D17); a missing object is fetched whole. Garbage collection, by any writer under the flock, removes objects in none of the last few manifests and older than 10 minutes; readers copy into their own per-invocation files.
- **Answer: the service may write through** (SVC-7, `design.md:2812–2816`), and may keep its images in memory too. Cost does not decide it: a hit is 3 ms and a miss about 10 ms of client work ([L4-c]).
- **Phases:** the cache lands in phase 1 with the snapshot, the readers and the import; the service's write-through with `serve`.

#### Question 11: `code/lanes-index:1315`

- **Phase 1's first PR,** because every new register subcommand reuses `_run`. `_run` (`:1310–1317`) is the only `psql` spawn: `_query_many` (`:1345`), `open_write` (`:1386`) and `apply` (`:1484`) all go through it.
- **The change:** drop `"-d", self.url`; `_env` (`:1299–1308`) maps the URL as SVC-2 fixes it (`design.md:2603–2615`), host, port, database, user and the five SSL and target parameters to `PG*` variables; `load_config` refuses any other query parameter, so the direct read and the service connect identically. Every connection goes through the environment, the admin connection too, as `PGSERVICE=<service>` (lane 1's `bootstrap --admin-service`); `_env` drops the inherited `PGPASSWORD` and `PGPASSFILE`, as today (`:1304`), and also `PGSERVICEFILE` and `PGSYSCONFDIR`, so no inherited variable points libpq at another file (lane 5's 6579523948, accepted by lane 3 in 6579562840). On register paths `PGCONNECT_TIMEOUT` is set, not `setdefault` (`:1306`), so an inherited value cannot lengthen the bound, and the 300 s cap (`:1317`) becomes each path's client timer; the index paths keep 5 s and 300 s. It is cost-neutral ([L4-c]).
- **Tests that read the argument list:** `FAKE_PSQL` takes the URL from `-d` (`tests/test_lanes_index.py:1061`) and must rebuild it from `PGHOST`, `PGPORT`, `PGDATABASE` and `PGUSER`; the role assertions on `c["url"]` at `:1163`, `:1178` (in the test at `:1140`) and `:1247` (in the test at `:1214`) keep working through it; the lexer test at `:1132` passes `-d` itself; `tests/test_lane_worktrees.py:1846`'s `-d` is `update-ref -d`. New assertion: no argument contains the URL, and no `-d` appears.

#### Phase 1 under the other options

Under Q26(b), the guard's outage branch and `outage declare` are not built. Under Q26(a), the guard reads the mirror on any failed read. Under Q29(b), phases 1 to 3 do not change; only the service's turn moves before act 4.

### Phase 2: the write path

Phase 2 starts once slices 1a and 1b have landed (`design.md:131–135`). Neither is in the code at `2a057a6d`. Besides the attempt loop below, phase 2 ships `start-record`, `lane-start`'s bind as one verb ("Answer 2's detached bind", below). After 1b, `commit_push` (`code/lanes-edit.sh:2292`) becomes `commit_publish`, its `pull --rebase` loop (`:2391–2500`) becomes Amendment 23's S4, and `handle_preexisting`'s capture (`:2701`) becomes S3's step 4. Each call site below maps to the same call in 1b's code; that mapping is an assumption until 1b's code is read.

#### How an attempt runs the verb: a child that stops at the seam

The primary branch sits at the dispatcher's write guard (`:15103`), which names all fourteen writing verbs and runs before any verb arm. For each attempt:

1. **The snapshot and the scratch tree.** `lanes-index register snapshot --tree <dir>` returns the snapshot (S1's step 1) and builds the scratch tree at `n` ("The scratch tree", below). Its checks run in L's order, the write id before the switch:
   - a write id found with this run's lane, verb and arguments ends the attempts, and the run goes to "The tail, once";
   - one found for another write is 2, with LR007's text;
   - then a switch that is not `primary` with the marker `present` is 69;
   - then the run's generation, the act's generation and the act's base are each 2 ("The acts and their expectations").

   So a later snapshot never tells a write that is in the journal 69 or 2.
2. **The verb runs as a child process:** `"$SELF"` with the original command line, saved before the option parse at `:638–646`, with the internal flag, its stdout and stderr redirected into the attempt's directory, and the real ones kept open on fds 3 and 4.
3. **The child stops at the seam.** The commit seam's primary branch writes the change set and the message, writes `ready`, and waits on fd 5. Fd 5 is a FIFO in the attempt's directory that the child opens read-write, so the open cannot block. The child holds no database session while it waits; `register commit` is the parent's own `psql` child.
   - **It waits in turns** of `read -t 1 -u 5`, since bash 3.2's `read -t` takes whole seconds. After a turn with no message it tests its parent with `pid_start_of` against `parent.pid`. It waits on while the parent lives, up to a cap.
   - **The cap is `2 × (D + 30 s)`, 120 s at D7's 30 s** (`LANES_REGISTER_SEAM_CAP_S` for tests). That is nearly twice a write's worst case ("The in-doubt rule", below).
     - A child normally waits for its own commit call: the call's client timer, 8 s with the connect inside it (D9's commit row; the timer is `subprocess.run`'s `timeout` on the whole `psql`, `code/lanes-index:1314–1317`). Add one `lanes-index` start, about 0.11 s ([L4-c]), and the parent's work before `committed`, the renderer's fork and the record's rewrite, a few ms. That is about 8.2 s.
     - A child kept after a call in doubt waits for the rest of the write, at most `T_write`, about 68 s.
     - A live parent never meets the cap. It bounds only a parent that is stuck.
   - **On a message** it writes `seam.out` in its directory, once, with noclobber: `tail` before it acts on `committed`, `none` before it acts on `abandon`.
   - **With its parent gone, or at the cap,** it writes `seam.out` as `none` and writes one line to fd 4: "attempt `<k>` of write `<id>` stopped waiting at the seam (`<why>`); nothing after the seam ran". It then exits 75 without its tail.
   - **The parent's write never blocks.** For each message it opens the FIFO read-write, writes one line shorter than `PIPE_BUF`, and closes it: `exec 6<>"$fifo"; printf 'committed %s\n' "$s" >&6; exec 6>&-`. A write-only open blocks while no reader holds the FIFO. Where the child has already exited, the line is left unread. The parent reaps the child and reads `seam.out`; `none` sends the run to a replay ("The tail, once").
4. **The parent decides,** from the commit call's answer:
   - **committed `<s>`:**
     - It forks the renderer, writes `tail=sent <s> <utc>` into the pending record, and sends `committed <s>`.
     - The child points fds 1 and 2 back at 3 and 4, then replays its own buffered stdout, then stderr. It sets `REGISTER_SEQ`, drops its whole read family (question 10's helper) so its tail reads take D17's path, and returns 0 from the seam.
     - The verb's code after the seam runs once, after COMMIT. The parent writes `tail=done`, removes the record, and exits with the child's code.
   - **a lost turn** (LR001, 40001, 40P01, 55P03 or LR008): it sends `abandon`. The child exits without its tail, and its output is discarded. The next attempt starts, or the deadline ends the run.
   - **in doubt** (`psql` exit 2, or the call's client timer): it sends nothing and sets `in_doubt=1`. The child stays at the seam, kept, until the run learns the outcome ("The tail, once"). The next attempt starts.
   - **refused at the commit (2 or 69):** it sends `abandon`. The buffered output and the refusal are the run's end.
   - **the child exits before `ready`:** the verb's own refusal, or a no-op with an empty change set. No commit call is made, and the child's output and code are the run's end.

   **The run's end.**
   - Where no commit call was in doubt, the parent replays that end's output and exits its code.
   - Once any call was in doubt, in this run or in a run whose record it took over, no end other than 0 comes before F3's lookup (L's rule; "The in-doubt rule", below). That covers:
     - a refusal at the commit (2 or 69);
     - the verb's own non-0 code before `ready`;
     - a snapshot that fails (69) or refuses (69 or 2);
     - the deadline's 75.
   - The lookup's outcomes are S1's: found is 0 plus the tail; absent with the lock held is that end, replayed then, with its code; the lock not held within the window, or the lookup refused (LR011), is 3.
   - Before any end but 0, every kept child gets `abandon`.

   The child replays its own output, so nothing the parent writes to its caller stands between the call's end and `committed`.

It is a child process, not the frame's subshell (`design.md:568`, `:1705`), because at file scope the script computes `LANES_REPO`, `LANES_DIR`, `LANES_FILE`, `LOCK` and `LANES_PATH` (`:1052–1064`, `:1100`, `:1115`) and the dispatcher is a top-level `case`: a re-exec recomputes them all for the scratch tree. It also gives each attempt a fresh read family, since `SE_CACHE_FILE` is a file-scope `mktemp` (`:3449`) that is not exported, so attempt 2 never reads attempt 1's snapshot.

#### The internal flag (F22, [L4-a′])

**`LANES_REGISTER_SCRATCH=<attempt dir>`**, honoured only where the directory is under `<state>/register-writes/`, owned by this uid with mode 0700, and its `parent.pid` holds the child's `$PPID`. The child copies the value into a shell variable and unsets the export at once, so no `git`, `gh` or `tmux` it forks inherits it, and a stray export finds no live directory. It covers more than the frame's "the resolver and S3" (`design.md:1710–1712`):

| Concern | Under the flag |
|---|---|
| D3's mode query | Not asked; asking would recurse into the primary branch. The run is files-mode code inside the scratch tree. |
| The resolver | `LANES_REPO`, `LANES_DIR` and `LANES_FILE` are the scratch tree's. At the pin the parent sets all three, since with only `LANES_REPO` set `:1053–1061` still derive `LANES_DIR` from `W`; after 1a, `LANES_REPO` alone does it, and 1a's refusal of `W` and its per-lane path are skipped. |
| S3 (1b) | Skipped whole. At the pin, `log_sync` (`:6202`) returns with `LOG_SYNC_FETCH=yes` and makes no `ls-remote` or fetch (`:6217–6227`); without that, `retire-rows` and `archive-rows` refuse 1 (`:12609–12611`, `:12979–12981`), and with them every verb that fetches (`:11825`, `:15417`, `:15617`, `:16390`, `:16507`, `:16726`, `:17113`). The files-mode marker check after S3's fetch is skipped too; the snapshot's switch check replaces it. |
| Reads | `register_text_checked` (`:3597`, origin branch `:3612`), `remote_log_events` (`:3408`) and every other reader take the files branch on the scratch tree's `origin/main`, never D17's SQL branch, so the verb's checks and its edit see one snapshot, `n`. |
| S4 (1b) and the push loop | Never reached. `LANES_INDEX_NUDGE` stays 0, since only a push sets it (`:2387`, `:2411`, `:2433`). |
| `gh_reads` (`:11402`) | Cached in the write's directory across attempts. It decides nothing (R2A §2c), and three GitHub calls per attempt, 55 attempts in a burst of ten, would spend the shared REST quota. |
| The host-local mutex | Under the flag `LOCK` is the scratch tree's, since `:1100` computes it from `LANES_DIR`. It serialises nothing on the host. The tail's sidecar acts must serialise with `set-lane-state`, which takes the lane checkout's mutex (`acquire_lock`, `:18585`). So under the flag, `lane_state_follow` (`lock_try 20`, `:13834`) and `lane_state_rename`, which runs inside the held lock (`:16273`), take the lane checkout's mutex, never the scratch tree's. The parent writes that mutex's path into the attempt directory. |

#### The scratch tree, and the files the snapshot returns ([L4-b])

- **From SQL, the render set at `n`:** `lanes/LANES.md`, every `lanes/log/<lane>.md`, `lanes/aliases.tsv` and `lanes/archive/*`.
- **From the lane checkout's `origin/main` as last fetched,** each blob id recorded in the journal entry (`repos_blob`, lane 1's v2): `lanes/repos.tsv` (read by `canon_object` `:3130` and `resolve_home` `:3232`; D19), and `lanes/log/README.md`, so the tree lists as origin's does; its readers skip it (`:3438`).
- **Not in the scratch tree:** `lanes/STORE` and the handoffs. The seam refuses any path outside the render set.
- **How it is built.** `--tree` writes these files, `git init`, adds and makes one commit, and points `refs/remotes/origin/main` at it, which `add-row`'s reads need (`rows_named_ci` `:15455` through `:3612`; `retired_identity_hits` `:15440`) and which makes the "behind" checks count 0. Built from images, it costs 162 ms ([L4-c]).
- **The scratch tree must never hardlink a work-tree file.** `register_rewrite` writes the register in place, `cat -- "$2" > "$LANES_FILE"` (`code/lanes-edit.sh:1662`, again at `:1666`), and an append in a `cp -al` copy changed the source's file ([L4-c]'s probe). A hardlinked scratch tree would therefore write into the register it was copied from. Only git objects may be shared, as `clone --local` shares them. `git worktree add` in the lane checkout is not used either: it is cheapest (42 ms) but needs the mirror at exactly `n` and writes into the lane checkout's `.git/worktrees`.

#### The commit seam's primary branch (D4)

**What stays,** the content checks: `:2293–2315`, the path list, the case-only rename's `update-index --force-remove` (`:2306–2309`), the bytecode fence (`:2310–2314`) and the `git add` (`:2315`).

**What replaces `:2335–2500`:**
1. An empty `diff --cached` is no change: the seam returns 0 with no handshake, as `:2339–2340` does today.
2. Every staged path must be in this write's pathspecs plus `LOG_RENAMED_FROM` (`:2370–2371`'s check, extended to every write) and in Q18(A)'s render set; otherwise exit 2 naming the path, nothing sent.
3. The seam writes `changes.tsv` (status, path, the base blob at `n`, the new blob, from `git diff --cached --no-renames --raw HEAD`), the new images from the index, and `msg`, holding `$msg` verbatim.
4. The handshake.

| Site | Caller | What changes at the site |
|---|---|---|
| `:6161` | `write_event`, for `log` `:16465`, `claim` `:16649`/`:16651` (CLAIM-LOST `:16676`/`:16679`/`:16682`), `release` `:16742`, `request-handoff` `:17203`/`:17232` | None. The tail at `:6162–6180` runs once, after COMMIT. |
| `:12155` | `migrate_state_cells` | None |
| `:12941` | `retire_rows` | None; the follow loop (`:12947–12952`) runs after COMMIT |
| `:13151` | `archive_rows` | None |
| `:15233` | `set-row-state` | None; a no-op stamp (`REPLACE_LINE_NOOP`, `:15223`) stages nothing, so no commit call |
| `:15267`, `:15323`, `:15381` | `replace-in-row`, `append-session-id`, `append-line` | None; D18 classifies `append-line`'s line in the commit |
| `:15479` | `add-row` | None; its `CP_AFTER_REBASE` (`:15478`) never fires |
| `:15514` | `commit` | Never reached: refused 2 before the loop (D10) |
| `:16266` | `rename-lane` | The handoff paths (`:16084–16087`) leave the change set (below) |

Two lines that read HEAD's sha after the write print `register seq <n>` instead (Q19(A)): `claim` at `:16690` and `rename-lane` at `:16277`.

**`handle_preexisting`'s eleven sites** (`:2744` inside `capture_register_edit`, which `:6134` calls; `:6135`, `:12108`, `:12914`, `:13129`, `:15206`, `:15246`, `:15311`, `:15377`, `:15472`, `:16110`) do not change. In a scratch tree just checked out, `:2705` returns at once, since the dirty tests read tracked files only (`:1934`, `:1945–1946`). Under the flag the function, or 1b's S3 step-4 check, gains one assertion: a dirty pathspec in a scratch tree is an internal error, exit 2, "nothing was sent", never a capture commit (`:2713–2714`). On primary a pending hand edit can exist only in the lane's checkout, which the parent checks before the first snapshot (`design.md:1655–1660`).

**`CP_AFTER_REBASE`** (`:1123`, called at `:2429–2430`) is never invoked on primary. Its three hooks are replaced by the verb's own pre-checks, re-run on each attempt's snapshot, plus the commit's head check: `claim_rescan_hook` (`:11485`; `return 9` `:11548`, `return 7` `:11567`) by `claim`'s checks at `:16520` and `:16537`; `add_row_retirement_rescan` (`:3804`) by the retired check at `:15440`; `cp_publish_refence` (`:2235`) is not needed, because `commit` is refused. The parent's resolver lines (`:1052–1064`) do not change: it still resolves the lane's checkout through 1a, to look for a pending hand edit and read `repos.tsv`.

#### Each write verb on a scratch tree ([L4-a]), and a second seam (F21)

| Verb | Runs unchanged? | What it needs |
|---|---|---|
| `set-row-state`, `replace-in-row`, `append-session-id`, `append-line` | Yes | Nothing; they read the local file unfetched today (`:15207`, `:15247`, `:15312`), the file at `n` on primary |
| `add-row` | Yes | The scratch tree's `origin/main` ref |
| `log` (all twelve verbs), `release` | Yes | The flag's fresh fetch; their tails run after COMMIT |
| `claim` | Yes, to the seam | `:16690` changes; CLAIM-LOST (`:16667–16686`) is unreachable; `gh_reads` is cached |
| `request-handoff` | Its write, yes | Its tail polls with `log_sync` (`:17269`) and `lane_binding_scan` through `lane_log_events` (`:9830`); after `committed` those read D17's path (question 10) |
| `retire-rows`, `archive-rows`, `migrate-state-cells` | Yes, with the flag | Their "behind" checks (`:12628–12634`, `:12982–12988`, `:11826–11832`) count 0 |
| `rename-lane` | **No** | Its handoff half (move `:16167`, stamp `:16182–16222`, paths `:16084–16087`, checks `:16065–16067`) acts on a file the scratch tree lacks. On primary (Q13(a)) the handoff paths leave the change set; the row's handoff cell is still rewritten to the new path in the transaction (`:16227–16229`, as if `rl_hdone=1`); the move and the stamp run as the tail, an Amendment 23 S3–S4 commit in the lane's checkout. The stamp checks (`:16190–16195`) read the lane checkout's file before the seam, so a handoff that cannot be stamped refuses before anything is sent. |
| `commit` | Not run | Refused 2 before the loop (D10) |

**Which verbs reach the seam twice in one run today:** `claim` through CLAIM-LOST (`:16676–16682`) after its hook returns 7 or 9; `add-row`'s compensating commit (`:3908–3912`); the captures (`:2713–2714`, `:2396`). None can fire on primary: their triggers are the rebase hooks and dirty checkouts, the loser writes nothing (Q12(a), D18), and `commit` is refused.

**The rule: one run, one journal entry.** A second reach in one run exits 2: "a second register write in one run; the first committed at seq `<s>`; nothing more was sent." No verb is split and the design names no merged message, because D5 and S2 give one entry and one mirror commit per write with its own message "exactly as today", a merged message would invent text, a split would create a partial state between two entries, and `rename-lane`'s second act is a git commit, not an entry.

#### Output, and the side effects that run once (F14)

Each attempt's stdout and stderr go to two files; only the deciding attempt's are replayed, each whole to its own descriptor, stdout first. The order between the two streams is lost, which shows only on a terminal. Output after the seam streams live, so `request-handoff`'s poll still shows progress. Every arm's side effects outside the register sit after the seam, so they run once, after COMMIT, by construction:

| Arm | Side effects |
|---|---|
| `write_event` | `lane_state_follow` (`:6179`): sidecars under the control root, outside `LANES_REPO` (`:13487–13510`) |
| `retire_rows` | the follow loop (`:12947–12952`) |
| `claim`, `release` | `gh_comment` (`:16695`, `:16744`) |
| `request-handoff` | tmux `guard_type` (`:17242`) and the poll (`:17254–17286`) |
| `rename-lane` | `lane_state_rename` (`:16273`), the `gh_comment` loop (`:16284–16303`), the handoff commit |

Before the seam, attempts make no side effect outside the register but `gh_reads`' cached network reads. Host-local reads repeat per attempt, as the frame's risk accepts (`design.md:3408`): tmux (`:17132`, `:17214–17215`), liveness (`:16537`, `:15768`, `:12653`) and sidecar preimages (`:6131`). In a replay ("The tail, once"), the clock and the sidecar pre-images are the committing call's, and tmux, liveness and `gh_reads` are read again.

#### The change set for the five acts that change several files ([L4-b])

The change set is whole new images plus each path's base blob at `n`; `lanes-index` derives the line edits, so equal lines moved within a file are never ambiguous.
1. **`LANDING` and `LANDED`** (`:6092–6094`, `:6154–6156`): the log is M with one line appended, or A with its header (`:3095`), and a case-only rename is D plus A (`:6147`); `LANES.md` is M, one line appended.
2. **`rename-lane`** (`:16072–16083`): `LANES.md` M, one row replaced (`:16248`); the old log D and the new A, a move plus a `RENAMED` line (`:16141`, `:16149`), or A only where the old lane has no log (`:16145`); `aliases.tsv` M, appended (`:16244`), or A with its header (`:16234–16243`).
3. **`retire-rows`** (`:12914–12941`): `LANES.md` M, N rows replaced; N logs M, appended, or A (`:12918–12919`).
4. **`archive-rows`** (`:13129–13151`): `LANES.md` M, rows deleted mid-file (`:13147`); `archive/LANES-retired.md` M, appended, or A with its header (`:13132–13145`).
5. **`migrate-state-cells`** (`:12119–12155`): the archive copy A, whole (`:12123`); the logs M or A (`:12131–12137`); `LANES.md` M (`:12147`).

All five are given by `--raw` plus the images.

#### The pending record (F10)

- **Where:** `<state>/register-pending/<d>`, where `<state>` is `lanes-index`'s state directory and `<d>` is the sha256 of `lanes-pending/1 NUL <LANES_LANE> NUL <LANES_SESSION> NUL <verb> NUL <arg1> NUL … <argN>`, as given on the original command line. `<d>` is the entry's `journal.args_sha256` (lane 1's v2). `LANES_SESSION` is in it because it is written into the event lines' session field (`code/lanes-edit.sh:615`, `:5763`): two runs that differ in it write different lines. The UTC and the host facts are left out.
- **Format:** mode 0600, in a 0700 directory, written by temp file and `mv`, one `key=value` per line: `format=1`, `write_id`, `generation`, `database`, `lane`, `session`, `verb`, `args_sha256`, `cmdline` (shell-quoted), `created_utc`, `updated_utc`, `pid`, `pid_start` (so a reused pid is not taken for a live writer; read by `pid_start_of`, shared with answer 2's bind record), `host`, `container`, `attempts`, `in_doubt=0|1`, the `call` lines and `tail`:
  - **`call=<base> <clock> <not_after> <files> <dir>`:** one line per commit call that may have committed. It holds the call's base seq `n`, the snapshot's database clock that its lines carry (D13), its `not_after`, the sha256 of the sorted `files` list it sent (path, present, sha256), and its attempt directory.
    - It is written before the call is sent.
    - It is dropped when the answer shows the call did not commit: a lost turn, a refusal, or LR008.
    - A call in doubt keeps it, and its directory keeps three small files: `snapshot`, `files` and `preimages`.
    - So a rerun that sends no commit call of its own, because its snapshot fails or its verb refuses before `ready`, still waits for the `not_after` of the run before it. The record carries it (lane 1's v2.6 point: "the pending record carries no `not_after`").
  - **`tail`:** absent until a run learns the write committed; then `sent <s> <utc>`, then `done <s> <rc> <utc>` ("The tail, once").
- **Written** after the first snapshot, which supplies the `generation`, and before the first commit call. `in_doubt` turns 1 when a commit call ends in doubt.
- **Removed** once the outcome is known:
  - on 0, after the tail is done or named as owed;
  - on 2, 69, 75 or the verb's own code, where `in_doubt` is 0;
  - where `in_doubt` is 1, only after F3's lookup has settled the write, found or absent with the lock held (L's rule; "The in-doubt rule", below).

  The `in_doubt` key decides, not the code.
- **Kept** on 3, with its `call` lines and their directories, so that a rerun can settle the write and run its tail. A rerun that takes the record over inherits its doubt. Its own non-0 ends then wait past the largest `not_after` among the record's `call` lines and its own.
- **The printed rerun carries the generation** (lane 1's ask; `commit_write` takes `p_generation`): `LANES_WRITE_ID=<id> LANES_WRITE_GENERATION=<generation> LANES_LANE=<lane> LANES_SESSION=<uuid> lanes-edit.sh <the same arguments>`. A rerun under an id of another generation, or whose lane, verb or arguments differ from the entry's, is refused by `commit_write` with LR007: exit 2, nothing written (lane 1's v2, renames line 72).
- **A plain rerun** with the same digest and no `LANES_WRITE_ID` reuses the id, says so on stderr, and takes the record over, with its `call` lines and its doubt. There are three exceptions:
  - **Past the maximum age** of **15 minutes** from `updated_utc` (`LANES_REGISTER_PENDING_MAX_AGE_S` for tests), it warns, prints the lookup, and does not reuse.
  - **Of another generation,** it does not reuse, since that write was made against another database. It replaces the record, names the old id and generation on stderr, and runs no tail of the old write.
  - **With a live pid** (R2's N2), it waits while that pid lives, up to D7's deadline from its own start.
    - If the pid ends first, it reads the record again and goes on as above.
    - If the pid still lives at the deadline, it exits **3**, sends nothing, and leaves the record as it is: "an identical write is still running in pid `<p>` (write `<id>`, started `<utc>`); this run sent nothing, and that write's outcome is not known. Once pid `<p>` has ended, look it up (`lanes-index journal --write-id <id>`), or run `LANES_WRITE_ID=<id> LANES_WRITE_GENERATION=<generation> LANES_LANE=<lane> LANES_SESSION=<uuid> lanes-edit.sh <the same arguments>`, which finds it or writes it once."
- **A run under `LANES_WRITE_ID`** waits for a live pid in the same way, since two live runs must never share an id.
- **A record from another host or container** counts as live for 120 s from its `updated_utc`, the seam's cap, since its pid cannot be tested here.

The age is short because a record exists only after a kill or a 3, and its rerun comes in seconds to minutes. A human gets the printed rerun, which settles the write with or without the record, and runs its tail where the record is ("The tail, once"). Past the age, a reused id would turn a new identical intent into a silent "already written".

- **Caveat:** a caller that puts its own UTC in an argument (`append-line` text, `log … --utc`) never matches its rerun and must pass the printed variables.

#### The in-doubt rule (F3, D8)

- **When it applies** (L's rule, S1's "A write in doubt ends only after its lookup"). Once any commit call of a write was in doubt, in this run or in one whose record this run took over, the writer ends with no code but 0 until it has made F3's lookup. Its non-0 ends are:
  - a refusal at the commit (2 or 69);
  - the verb's own code before `ready`;
  - a snapshot that fails (69) or refuses (69 or 2);
  - the deadline's 75.

  Each waits for the lookup, at the deadline or before it.
- **The lookup is lane 1's v2.6 `find_write(<id>, <n>)`.** It is made by `lanes-index journal --write-id <id> --not-after <n>`, at READ COMMITTED, in a transaction it rolls back. `<n>` is the largest `not_after` among the record's `call` lines.
  1. **The client waits first,** on its own estimate: the last snapshot's database clock plus `SECONDS` since. It calls once that estimate puts `<n>` at most 2 s ahead. A run with no snapshot clock calls at once.
  2. **The server waits for the rest,** on its own clock, holding no lock, for at most 3 s.
  3. **The server takes the head row's lock** under the 2000 ms `lock_timeout`. It checks again under the lock that the clock has passed `<n>`, the exact complement of step 9's test, and returns the write's seq, or NULL.
- **Its outcomes,** as S1 has them:
  - **found:** 0, and the verb's tail runs once ("The tail, once"); the record is removed;
  - **absent, with the lock held:** the code the last attempt earned, whether 75, 69, 2 or the verb's own, with that attempt's output; the record is removed;
  - **the lock not held within the window, or the lookup refused** (55P03, LR011, or no answer): 3, naming the id, with the lookup and the printed rerun; the record is kept.
- **LR011, "lookup too early,"** is v2.6's refusal. It covers an `<n>` that is missing or more than 3 s ahead, and a check that fails under the lock, as after the clock stepped back. It proves nothing, so it is 3, and a rerun under the same id looks again.
- **The window starts when the client's own wait ends.**
  - That is at once where `<n>` is already at most 2 s ahead, as after a commit call that ran to its timer. Otherwise it is at most `C + 2 s` after the last snapshot's end.
  - Every try starts inside its 10 s, and each runs to its own 8 s timer, which covers the 2 s connect, the server's wait, the lock and the lookup (D9's lookup row).
  - A try that cannot connect is made again.
  - After a doubt, a snapshot that cannot connect ends the attempts. These tries are then the writer's 10 s of reconnecting (S1's "Where the snapshot cannot reconnect").
- **Why it starts there, not at the run's end.**
  - The worst case is the same within `max(0, 2 s − v)`, below.
  - A commit call that fails at once is the case where the database is most likely down. It still gets 10 s of tries. A window started at the run's end would leave it about 2 s of tries after an 8 s wait for the clock.
- **What the lock and `not_after` prove.**
  - The lock proves that no transaction of this write holds the head row or waits for it.
  - It does not prove that none will start: an orphaned call whose query is still queued at a loaded server holds no lock.
  - Every commit call carries `not_after`, the snapshot's database clock plus its client timer plus 2 s. `commit_write` raises LR008 unless `clock_timestamp() < not_after` just before it returns (§1.3's step 9).
  - So once the lookup holds the lock with its check passed, no call of this write can still commit.
  - One residual remains, lane 1's: a server clock that steps back between the lookup's check and an orphan's step 9.
- **LR008 is never 3 or 69 by itself.** It is the database saying "not committed": a lost turn, whose `call` line is dropped. Inside D7's 30 s the loop goes on, a fresh snapshot then a new attempt under the same write id, as for 40001.
- **What it adds to a write's worst case.** The terms:
  - `D`: D7's 30 s, plus 1 s for `SECONDS`' grain;
  - `S` and `C`: the snapshot's and the commit's client timers, 8 s each, each with the connect inside (D9);
  - `v`: the verb's run to `ready`;
  - `R`: the window, 10 s;
  - `T_L`: the lookup's client timer, 8 s;
  - `p`: three `lanes-index` starts and the scratch tree, about 0.5 s ([L4-c]).

  The derivation:
  - The last attempt starts by `D + 1`, and its snapshot ends by `S` after that.
  - Its commit call ends by `v + C` after the snapshot's end, and the client's wait by `C + 2 s`. So the window starts by `C + max(v, 2 s)` after the snapshot's end.
  - The last try starts by `R` after the window's start, and ends by `T_L` after that.

  `T_write ≤ (D + 1) + S + C + max(v, 2 s) + R + T_L + p`

  - **The figure:** 31 + 8 + 8 + 2 + 10 + 8 + 0.5 = **67.5 s** for any `v` up to 2 s, and 65.5 s plus `v` above it.
  - **After the run's last call,** the lookup adds at most `R + T_L + max(0, 2 s − v)`, 18 to 20 s. With the database reachable, its first try answers within about 5 s of the window's start.
  - **`F₀`, a lookup whose `<n>` has long passed,** such as the bind child's before a rerun ("Answer 2's detached bind"), waits for nothing. It takes at most `R + T_L` = 18 s, plus one `lanes-index` start, about 0.1 s.
  - **Not in the figure:** the tail's own run, and a replay's one snapshot and one verb run (`S + v` more; "The tail, once"). They come after the write's end, as today.
- **Answer 2's writes** run in the detached bind (ruling 16) and take this rule as every writer does. The bind child makes the same lookup before a rerun ("Answer 2's detached bind", below).

#### The tail, once (S3; row 24)

**The rule.** A write's after-COMMIT tail runs in one child only: one stopped at the seam whose change set is the entry's own.
- The parent writes `tail=sent <s> <utc>` into the pending record before it sends that child `committed <s>`.
- It writes `tail=done <s> <rc> <utc>` once the child has exited.
- A run that finds `tail` sent or done runs no tail.

So a tail runs at most once per record.

**The match.** The entry's change set is its `journal_files` rows: path, present, sha256. A child matches when the sorted list it sent, or would send, has the sha256 of the entry's list. The snapshot that finds the id, LR006's seq and F3's lookup all give the seq. The client reads that seq's `journal_files` in the same transaction (the writer has `SELECT`, §1.4).

**Where a run learns its write committed, and which child takes `committed <s>`:**

| How the run learns it | The child |
|---|---|
| its commit call answers `committed <s>` | that call's own child (step 4) |
| LR006 at a commit | whichever matches: the current child, or a kept one. This path already works at J, because a child waits at the seam (R2). The match makes it exact: the current child's lines carry its own snapshot clock (D13), which differs from the committing call's once that call ran past a second |
| this run's snapshot finds the id (step 1) | the kept child of the call in doubt that committed |
| F3's lookup at a non-0 end (L's rule) | the same |
| the kept child is gone (its cap, or a signal; `seam.out` says `none`) | a replay |
| a later run under the record: a plain rerun, or the printed rerun on the same host | a replay |
| the bind child's lookup | the record's rerun, which replays ("Answer 2's detached bind") |

**A kept child** is the exact case. A call in doubt leaves its child at the seam (step 4), so the tail runs with the state the commit was made from: its clock, its sidecar pre-images and its tmux reads. Once the run's outcome is known, every other kept child gets `abandon`.

**The replay,** where no waiting child matches:
1. It takes the `call` line whose `<files>` is the entry's. Where none is, there is no replay.
2. `lanes-index register snapshot --tree <dir> --at <s−1>` builds the scratch tree at the entry's base, `s−1`, since the commit moved the head from `s−1` to `s`.
   - The images come from the renderer's pure function. The journal supports it from the latest import on: every imported line is a `create` or `append` edit, "so the journal alone renders any seq from the import on" (§1.3's kinds table).
   - `repos.tsv` is the entry's `repos_blob`.
3. The verb runs as a child under the flag, as any attempt does, with two values taken from the call's directory:
   - the clock, which the journal does not hold (`journal.utc` is the commit's; D13), so the replay's lines carry the committing call's UTC;
   - the sidecar pre-images. `lane_state_preimage` (`:13773`) records each answer in its attempt's directory, and in a replay answers from the call's. One not recorded is empty, which `lane_state_follow` reads as "write nothing" (`:13827`).
4. At `ready` the parent compares the change sets. Equal: `tail=sent`, then `committed <s>`. Otherwise: `abandon`.

A replay makes no commit call, so it skips the act's checks. It reads tmux, liveness and `gh_reads` again. It never types into a pane: `request-handoff`'s `guard_type` (`:17242`) is skipped in a replay, since the pane may hold another session by then. The request travels in the register, as it does when no pane is live (`:17236–17238`).

**When the replay differs from the attempt that committed.** The write is committed either way. The run removes the record, prints one WARNING, and exits 0. `request-handoff`, whose tail's wait decides its code, exits 2 instead: "the request is written at seq `<s>`; this run did not wait for the release: read `lanes-edit.sh binding <lane>`".
- **The replay stops before `ready`,** refused by its pre-checks or a no-op: "register write `<id>` is in the register at seq `<s>`; replayed at seq `<s−1>` it now stops before its write (exit `<rc>`: `<its last line>`), so its after-COMMIT steps did NOT run: `<owed>`."
- **It reaches the seam with another change set.** Examples: a line that a host-local read changed, or a row whose handoff cell no longer changes because the handoff has already moved (`:16227–16229`). The text is the same, naming the first path whose digest differs.
- **`<owed>`** is the verb's row of F14's table, above, in words, with the command that does each step by hand.

**The `tail` key, as a later run reads it:**

| Value | Written | A later run that finds the id |
|---|---|---|
| absent | — | replays, by the rule above |
| `sent <s> <utc>` | before `committed <s>` reaches the FIFO | runs no tail; exits as above with "its after-COMMIT steps may have run at `<utc>` in pid `<p>`, and are not run again: `<owed>`"; removes the record |
| `done <s> <rc> <utc>` | after the child that read `committed` has exited, its `seam.out` saying `tail` | runs no tail; exits as above; removes the record |

Where the child's `seam.out` says `none`, it never read `committed`: the parent removes `tail` and replays.

**No record here.** A run that finds its id with no pending record of it, such as the printed rerun on another host, runs no tail. Tails are host-local: sidecars, the lane checkout's handoff, tmux. A record from another host or container counts as none. The run exits as above: "register write `<id>` is in the register at seq `<s>`, written from `<workstation>` (`journal.workstation`); no pending record of it is here, so this run runs none of its after-COMMIT steps: `<owed>`. If its run there ended 3, rerun it there."

**A record of another generation** names a write made against another database. No tail of it runs. A plain rerun replaces the record and says so ("The pending record", above).

**Which tails are not safe to run twice,** at `2a057a6d`:

| Tail | Twice | Why |
|---|---|---|
| `gh_comment` (`claim` `:16695`, `release` `:16744`, `rename-lane`'s loop `:16284–16303`) | not safe | `gh issue comment` or `gh pr comment` posts a new comment on each call (`:11440–11441`) |
| `guard_type` (`request-handoff`, `:17242`) | not safe | `tmux send-keys … Enter` types the line again into whatever `claude` runs in that pane (`:10262`) |
| `rename-lane`'s handoff stamp (its tail on primary) | not safe | a second splice adds a second `RENAMED from` line under the header (`:16182–16222`) |
| `lane_state_follow` (`write_event` `:6179`; `retire_rows` `:12949`; `start-record`) | safe only with the committing attempt's pre-image | with it, the second run's fence (`:13848`) refuses, since the first moved `generation` (`:13855`); with a fresh pre-image the fence passes and moves `generation` again, over any act that moved the lane since |
| `request-handoff`'s poll (`:17254–17286`) | safe | reads only; it waits again, up to `--wait` |
| `rename-lane`'s flushes (`:16271`; `state_events_flush`, `:3493–3499`) | safe | they empty caches |
| `lane_state_rename` (`:16273`) | safe | a second call returns at `:13886` once the old root is gone |
| `claim`'s `register seq` line (Q19(A)), and the notes | safe | output only |

So the rule is "at most once", not "safe to run twice".

**Tests,** for lane 2, beside agreement test 12:
- a call in doubt that committed, settled by the next snapshot, by LR006 and by the lookup, each running the tail once from the kept child;
- the same with the kept child killed, so a replay runs the tail;
- a rerun after a 3 that replays;
- `tail=sent` found after a kill between `sent` and the child's exit;
- a replay that stops before `ready`, and one that differs;
- no record;
- the bind's found row.

#### What phase 2 ships for phase 3

- **The renderer fork.** In the parent, after COMMIT, `( lanes-index render --checkout <c> & )`, detached with stdin and fds 3–9 closed as `index_nudge` forks today (`:2561`), with `LANES_NO_FETCH` removed (D12). It replaces the register nudge (`:2561`; the inventory nudge at `:2564` is unchanged), and it must not sit behind `index_nudge`'s `LANES_INDEX=off` return (`:2557`), since `lane-worktrees` sets that variable on every `lanes-edit.sh` call it makes (`Helper.call`, `code/lane-worktrees:498`) and the variable must never skip the mode query or the fork. Phase 2's `lanes-index` answers "git half not installed", which a detached fork's caller never reads; a stub test asserts the argv.
- **`<c>`** from 1a's resolver (S1's `<W>-lanes/<lane>/`), computed without S3; the frame names it phase 3 (`design.md:1875`).
- **The author identity** (`user.name`, `user.email` of `<c>`), passed to `register commit` and stored in the entry (D15).
- **The register nudge suppressed on primary** (S2's nudge, `design.md:1968–1973`).
- **Agreement test 3's unit form,** with the writers' marker check (phase 3, below).

#### Phase 2 under the other options

Under Q7(A′) the verb runs once inside a held session, so buffering and the handshake go and F3 is the lock wait. Under (A), the loop also stops at six attempts with 75. Under (B), there is no scratch tree. Under "(A″) with 69", the 75 rows exit 69. Under Q13(b), `rename-lane`'s handoff commit precedes the transaction. Under Q19(B), `:16690` waits for the render and prints the sha.

### Answer 2's detached bind, and the bind as one verb

Lane 2 measured the bind (6579356984): 10.47 s median in files mode at `2a057a6d`, and an estimated 9.64 s median, 11.51 s p95 on primary, against about 3 s of room (`design.md:2998–3000`). Under lane 3's ruling 16 (6579459518), which the frame carries at `21f0191c` (SVC-8, `:2989–3017`; the timeouts row, `:2387`; the interfaces bullet, `:2441–2445`), answer 2 exits 2 with "binding in progress" and the bind runs in a detached child. Answer 2's writes take the ordinary writers' rows (D7, D8, D9), so `register commit` needs no `--end-line` or `--hook-pid`. This subsection answers the frame's two asks (`:3002–3008`): how the detached bind runs and what the guard answers meanwhile, and the bind as one verb.

#### The detached bind (F6)

- **`guard_answer`'s choice 2 starts it** (`code/lanes-edit.sh:11128–11207`). The three refusals (`:11129–11143`) and the window rename (`:11155`) stay in the hook. In place of `:11171` and `:11199` the guard rewrites the offer file (`guard_offer_file`, `:10271`) to `mode=binding` by temp file and `mv`, starts the child, prints the "BINDING" text and returns 2:
  `( umask 077; TMUX_PANE=<pane> $detach nohup "$RESOLVED" guard-bind "$G_ID" </dev/null >"<offers>/<uuid>.bind.<k>.out" 2>&1 3>&- 4>&- 5>&- 6>&- 7>&- 8>&- 9>&- & ) >/dev/null 2>&1`
- **`$detach`**, after `lane-start`'s own idiom (`code/lane-start:4616–4620`): `setsid` where it exists; else `python3 -c 'import os,sys; os.setsid(); os.execvp(sys.argv[1], sys.argv[1:])'`, which always exists on primary; else `nohup` alone, which survives a hangup but not a group kill (the one stated gap: macOS without python3).
- **`guard-bind <uuid>`,** a new internal verb, not a register writer. It refuses unless the offer file says `mode=binding`, and rewrites the record with its own `$$` and start time (bash 3.2 has no `BASHPID`, so a forked subshell could not). This process, the outer, is the pid of record. It runs the body under one bound, waits for it, and writes the outcome:
  `timeout -k 10 "$(( bound_s - 10 ))" "$RESOLVED" guard-bind --body "$G_ID" &`, then `wait`, again after a trapped signal, with a `TERM` trap that forwards the signal to that pid.
  - **The body** runs `lane-start --no-launch --bind "$G_ID" <args>`. After a 0 it runs the `set-row-state` for the lane the window left, with `LANES_ACT` from the record's `act` and `LANES_EXPECT="row:<from> bind:<from>"` on that call only (phase 2). Then it runs `guard_bound_check`'s read (`:11054–11075`). Everything the bind does is inside the one `timeout`.
  - **`guard-bind` is in the read door's `none` class.** Before `lane-start` it reads only the offer file. Its one read after `lane-start` is the post-bind refill.
  - **`timeout` signals the body's process group,** so `lane-start`, `lanes-edit.sh`, `lanes-index` and `psql` stop with it. A `git` call under its own `timeout` (`git_net`, `:2015–2027`; `hs_git_net`, `code/lane-start:4233–4246`) is in a group of its own. It outlives the body by at most `LANES_GIT_TIMEOUT` + 5 s, which is 50 s (probe P1).
  - **The outer is outside the bound,** so the record gets an outcome even when the bound stops the body. A person's `kill <p>` takes the same path: the trap forwards it, `timeout` passes it to the body's group and sends `KILL` 10 s later, and the outer writes the outcome (probe P2).
  - **Where `timeout` is missing,** as on macOS without coreutils, the body runs unbounded, as `hs_git_net`'s calls do (`code/lane-start:4226–4231`).
    - The record says `bounded=no`.
    - The trap forwards a `TERM` to the outer's own process group. The `setsid` or python3 start made the outer that group's leader.
    - Each register call is still bounded by its own timer, and each write by D7's deadline. What is left unbounded is `git`'s network and local hangs.
- **Working directory and descriptors:** the hook's own directory; stdin `/dev/null`; stdout and stderr to one 0600 file per attempt; fds 3 to 9 closed, as `index_nudge` closes them (`:2561`). These are hygiene, and they put the output in the attempt's file. A runner that waits for EOF on the hook's pipes would wait for the whole life of a child that kept them (PP-D's P3, with its own Python runner: 3.03 s, against 0.04 s with them closed); Claude Code 2.1.319 does not wait on them (the hook probe's R3, below), so that cost applies only to a runner that waits.

| Variable | In the child | Why |
|---|---|---|
| `HOME`, `PATH`, `CLAUDE_CONFIG_DIR`, `LANES_WORKSTATION`, `LANES_CONTAINER`, `LANES_REPO`, `LANES_EDIT`, `LANES_LANE_START`, `LANES_INDEX_CONFIG`, `TMUX` | kept | the resolver picks the checkout from the lane; `lane-start` dies without `TMUX` (`:1440`) |
| `TMUX_PANE` | the pane the hook resolved | with no tty, tmux finds the current pane only through it |
| `LANES_NO_FETCH`, `LANES_ACT`, `LANES_EXPECT`, `LANES_WRITE_ID`, `LANE_START_HANDOFF_ANSWER` | unset | the bind is a write act (D12); it takes its own `act-begin` base and its own write id; `LANES_ACT` is set from the record's `act` for the one `set-row-state "$from"` call only. Set to `yes`, `LANE_START_HANDOFF_ANSWER` would let `lane-start` ask the holder to hand off and wait up to 300 s (`code/lane-start:2461–2464`, `:781`). Unset, with stdin on `/dev/null`, `lane-start` refuses with 2 instead (`:2465–2471`, `:2479–2488`), so W8 never runs inside the bound |
| `LANES_GIT_TIMEOUT` | 45 | today's default is 60 (`lane-start:4234`); 45 bounds the stamp's pull and push |

**It survives the hook.** The hook normally exits about 4.5 s inside `GUARD_TIMEOUT` (it reaches `guard_answer` at about `T0 + 0.43 s`, lane 2), and the child must outlive it whether it exits or is timed out. PP-D's probes, at load 10 to 19: a pid kill, both forms survive (P4); a group `SIGKILL` (P1, P2) and a group `SIGHUP` (P2), `index_nudge`'s `( … & )` form dies and the `setsid` form survives.

**Claude Code's own hook runner, probed** (`runs-165/HOOKPROBE-report.md`: Claude Code 2.1.319, `claude -p` with a project-only `UserPromptSubmit` hook, 5 runs). Each hook runs under an `sh` that is its own session leader. At a 3 s timeout Claude Code sent SIGTERM to the hook's whole process group at 2.98 s: a `( … & )` child, already orphaned to pid 1 but in that group, was killed, and a `setsid` child completed (R1, R1b). On a normal exit it killed nothing, and both forms outlived `claude` itself by about 8 s (R2). It did not wait on pipes a child inherited (R3: `claude` exited at 4.20 s, the child 7.5 s later). So the `setsid` child outlives a hook that Claude Code times out and one that exits. Not shown: an escalation to SIGKILL, which cannot reach a `setsid` child either. The facts row (`design.md:3204`) narrows to that escalation alone.

#### The record, and its liveness test

The record is the offer file itself, `$CLAUDE_CONFIG_DIR/lanes/offers/<session uuid>` (`:10265–10273`): keyed by the transcript, read by the guard at the offer site (`:10648–10651`), and the session's own fact. The directory is a bind mount shared across containers (`design.md:3193`), so the record names its host and container, and liveness is judged only where both match. Its `key=value` lines:

| Keys | What they hold |
|---|---|
| `mode` | `binding`, `bound`, `bind-failed`, or `name-drift` once the offer is restored |
| `from`, `to`, `session`, `uuid`, `window`, `pane` | the offer's identity, as today's file has it (`:10965–10973`) |
| `bind`; `started_utc`, `bound_s`, `bounded`, `deadline_epoch` | the attempt, 1 to 3; the start; the bound, 430 s (`LANES_BIND_BOUND_S` for tests); `yes` where `timeout` exists, else `no`; the start plus `bound_s` plus 5 s, the 5 s being for the outer to write the outcome after `timeout`'s `KILL` |
| `pid`, `pidstart`; `host`, `container` | the child; `LANES_WORKSTATION` (else the hostname) and `LANES_CONTAINER` |
| `out`; `act`, `write_id`, `generation`, `not_after` | the attempt's output file; phase 2 only, `act` being the guard's base `<act-id>@<n>:<generation>`, from the `n` and `generation` of the prefetch that made the offer |
| `rc`, `why`, `note=` lines, `ended_utc` | the outcome, with one `note=` per act still owed and its command |

**Liveness:** the pid is live and its start time equals `pidstart` (Linux: field 22 of `/proc/<pid>/stat`, after the last `)`; macOS: `ps -o lstart=`). One helper, `pid_start_of`, serves this record and F10's pending record. It does not copy `lock_steal_if_dead`'s pid-only test (`:1286–1296`), under which a reused pid looks live until the 10-minute age (`:1311–1321`). P5: 200 checks take 1.42 s, about 7 ms each; a reused pid and a dead pid both read as not live.

**The bound is 430 s of wall time** from `started_utc`, over the child's whole body: `timeout -k 10 420` around the body, and `deadline_epoch` for the guard. It is arithmetic, not a measurement. It is the sum, below, of each step's own bound on primary in phase 2, with 36 s to spare, so it never stops a child whose every step stays inside its own bound. What it stops is what no step bounds: a local hang, and `git` where `timeout` is missing.

| Term | Bound | Source |
|---|---|---|
| `H′`, the healthy bind | 12 s | lane 2's 11.51 s p95 on primary, "about 12 s with the costs it leaves out" (6579356984) |
| `F₀`, the rerun's lookup, attempts 2 and 3 | `R` + `T_L` = 18 s | "Reruns", below; phase 2's "The in-doubt rule": the record's `not_after` has passed, so neither the client nor the server waits, and its tries start within the 10 s window, each under its 8 s timer |
| `act-begin`'s snapshot | 8 s | D9's "a writer's snapshot" |
| the reads, `N_r` × 6 s | 102 s at `N_r` = 17 | D9's "a reader"; each `lanes-edit.sh` read process makes one `register read` |
| W8, the holder's `PAUSED` writes | 0 | not reachable from the child (the variables, above) |
| `start-record` | `M` + `W` | below |
| the stamp's pull and push | 2 × (45 + 5) = 100 s | `hs_git_net` (`code/lane-start:4233–4246`): pull `:4422`, push `:4442`; `LANES_GIT_TIMEOUT=45` |
| `guard_bound_check`'s read | 6 s | the post-bind refill (`:11064–11065`), one `register read`; `row_of_lane` of the lane left (`:11180`) reads the refilled cache |
| `set-row-state` on the lane left | `M` + `W` | below |

- **`M`, the mode query,** is 2 s (D9). Each writer process asks it once, at the write guard.
- **`W`, one register write's worst case,** is phase 2's `T_write` ("The in-doubt rule"), less its `p`, the `lanes-index` starts, which `H′` holds: `W` = 65 s + `max(v, 2 s)`.
  - `v` is the verb's run from the snapshot to `ready`. It has no timer. It takes about 1 s healthy, and is counted as 2 s.
  - So `W` = 67 s with `v` = 2 s.
- **The sum:** 12 + 18 + 8 + 6`N_r` + 2(2 + 65 + `v`) + 100 + 6 = **278 + 2`v` + 6`N_r`**, for `v` of at least 2 s.
  - That is 384 s with `v` = 2 and `N_r` = 17: 36 s under the body's 420 s, or six more reads, before a healthy child could meet it.
  - The 10 s of `-k` makes 430 s.
- **Nothing is counted twice except the healthy share of each call.**
  - `H′` holds the processes' starts, their parses and two SQL writes at 52.5 ms each. It holds no git.
  - Each timer is counted whole on top of `H′`, which overstates the sum by under 3 s.
  - The stamp's 100 s is not in lane 2's figure, which leaves out git.
- **Each call's timer already includes its connect, the lookup's too.** `lanes-index`'s timer is `subprocess.run`'s `timeout` over the whole `psql` run (`code/lanes-index:1310–1317`). So D9's 2 s connect is inside each 8 s, and none is counted on its own.
- **The commit call and the lookup's wait overlap.**
  - `not_after` is the last snapshot's database clock plus 10 s. So the client's wait for it ends by that snapshot's end plus 10 s: at most 2 s after a commit call that ran its 8 s, and not at all after one where `v` is 2 s or more. `W` counts that once, as `max(v, 2 s)`.
  - The server's own wait, at most 3 s, is inside the lookup's 8 s timer (lane 1's v2.6).
- **In files mode, and so in all of phase 1, the sum is not a bound.**
  - Each write may wait 60 s for the lock (`lock_try`, `:1308–1309`).
  - It fetches under `git_net` (`:6222`).
  - It may run its push loop six times, each time a pull and a push, 3 s apart (`:2383–2500`).
  - A timed-out git call ends the write with 3 at once (`git_timeout_die`, `:2034–2046`). So the likely worst case is 160 s per write. If every call fails just inside its 45 s, it is 728 s.
  - In files mode the 430 s is a judgment: about 41 times lane 2's 10.47 s median.

**How it ends.**
- **On success** the record ends `mode=bound`, `rc=0`, with a `note=` for each act still owed: the stamp not written or not pushed (`lane-start:4419–4460`), the cell not ending on this uuid, or the lane left not marked.
- **On failure** it ends `mode=bind-failed`, `rc=<lane-start's code>`, with `why` = `lane-start`'s last refusal line and the output kept. The lane left is not marked, as at `:11172–11174`.
- **Stopped by its bound or by a signal,** it ends with `rc=124` or `137`, or the signal's code. Its `why` is "stopped by its bound" or "stopped by a signal", then "what it wrote is not known". `write_id`, `generation` and `not_after` are kept for the next `2`'s lookup. A git call under its own `timeout` may still land up to 50 s later.
- **If the session exits mid-bind,** the child is in its own session. Before `lane-start`'s check at `:2124–2131` the bind refuses; after it, the bind completes. The record holds the outcome either way.

#### What the guard answers

The check is a branch of its own at the offer site (`:10648–10659`), after 18(h)'s count and before the table, on `mode` in `binding`, `bound` or `bind-failed`; `guard_answer` is never called on these modes, since its identity test (`:11108`) would drop them once the window is named `to`. It costs one small file read, `kill -0` and one `/proc` read, about 7 ms, inside PP-C's `T0 + 4.4 s`. Every text goes to stderr; the hook string (`code/openRepoTools:2343–2353`) is unchanged.

| Case | Exit | Record after | Text, in short |
|---|---|---|---|
| the answer `2` itself | 2 | `binding` | "BINDING: this window is now named `<to>` … detached (pid `<p>`, bounded at `<bound_s>` s) … send your work again in about 15 s" |
| same session; child live, inside the bound | 2 | unchanged | "BINDING IN PROGRESS: … it ends by `<deadline utc>` at the latest. Your prompt was NOT sent; send it again in a few seconds"; where the record says `bounded=no`, the text leaves out "at the latest" |
| same session; child live past `deadline_epoch` | 2 | unchanged | "THE BIND HAS RUN PAST ITS `<bound_s>` s BOUND and is still running (pid `<p>`, started `<utc>`)". Where `bounded=no`: "this host has no `timeout`, so nothing stops it; it is most likely in a `git` call". Where `bounded=yes`: "its `timeout` has not ended it; it is likely stuck on the disk or the network". Then: "Waiting costs nothing, and the next prompt reads its outcome. `kill <p>` stops it, at a cost. If it is inside a register write, that write is left in doubt. On primary the record keeps its write id, and the next `2` looks it up before it binds again. In files mode a commit it made may stay local until the next write from that checkout pushes it, a push it started may still land, and the next writer takes over its lock at once. Your prompt was NOT sent." |
| another session wanting the same lane | its own table | its own record | it never sees this record. If it answers `2`, on primary `start-record`'s `bind:<to>` expectation refuses whichever bind commits second, with 2; in files mode, `lane-start`'s re-read at `:2614` |
| after success | 2, once | removed | "MOVED: this window is lane `<to>`, bound … at `<utc>`", one line per owed note, "This prompt was NOT sent"; the next prompt exits 0 |
| after failure, attempt `k` below 3 | 2, once | `name-drift`, `bind=k`; window renamed back to `<from>` | "THE BIND TO LANE `<to>` FAILED (exit `<rc>`): `<why>` … the offer is KEPT: reply `1`, `2` or `3`" |
| failure on the third attempt | 2 | `name-drift`, `bind=3` | as above, and choice 2 is no longer offered |
| does a failure restart it? | — | — | never by itself: a failed bind spends the person's yes; each `2` is one attempt, at most 3 per offer |
| stale: `binding`, same host and container, pid dead or reused | 2, once | `name-drift`, `rc=unknown`, `write_id` kept | "THE BIND STARTED AT `<utc>` … IS NO LONGER RUNNING AND LEFT NO OUTCOME … the next `2` finds out first" |
| `binding` from another host or container | 2 | unchanged until `deadline_epoch` plus 60 s, then as stale | "A BIND OF THIS TRANSCRIPT IS RUNNING ON `<host>/<container>` … Your prompt was NOT sent". The 60 s covers a `git` call that outlives the body under its own `timeout` (50 s, P1), plus 10 s between the two hosts' clocks. Where the record says `bounded=no`, the text adds that the child there may still be running, and that on primary `bind:<to>` refuses whichever bind commits second |
| an unreadable record | 2 | dropped | today's text (`:11097–11101`) |

**A finding in today's code, fixed by the rename back.** After a failed bind the window keeps `<to>`'s name (`:11155` renames before `:11171` runs), so at the next prompt `guard_answer`'s identity test (`:11108`) drops the "KEPT" offer and "answer `2` again" (`:11173`) cannot be taken; the table then refuses with `lane-start --no-launch <to>`, which vetoes: the #70 loop again.

**Reruns.** Phase 1: the bind becomes idempotent in `lane-start`. In `--bind` mode, where the binding read (`:2437`, fields at `:2448`) names this window with `b_sess` the bound uuid and the cell's last id is that uuid (`:2132–2133`), it writes no register line again, and writes the Rule 3 stamp only where the handoff has no `RESUMED by <uuid> (lane <L>)` line. Phase 2, on primary, the child makes F3's lookup before a rerun: `find_write(<write_id>, <not_after>)` on the recorded pair (lane 1's v2.6; phase 2's "The in-doubt rule"). By the next `2` the record's `not_after` has almost always passed, so neither side waits. Where it has not, the client waits until it is at most 2 s ahead, and the server waits for the rest:

| The lookup | The rerun |
|---|---|
| found | rewrites nothing in the register. `start-record`'s pending record, kept by its 3, still owes the tail: the child runs that record's `cmdline` under its `write_id`, generation, lane and session, which finds the entry and runs `lane_state_follow` once ("The tail, once") |
| absent, with the head row's lock granted | binds anew, under a new id |
| otherwise (55P03, LR011, or no answer) | `bind-failed`, `rc=3`, again |
| the record's `generation` is another database's | no lookup: the old database's transactions cannot commit into the new one, so it binds anew |

#### The bind as one verb: `start-record`

At `2a057a6d` the bind makes three register writes, each its own commit and push: `append-session-id` (`code/lane-start:3848`, or `:3869` for a restored id), `set-row-state LIVE` (`:3985`) and `log RESUMED` (`:4178`, through `write_event`, `code/lanes-edit.sh:5968`, committed at `:6161`). Beside them are the host-local sidecar (`lane_state_follow`, `:6179`) and the Rule 3 stamp (`lane-start:4288–4462`: `publish-handoff`, then `git add` `:4412`, `commit` `:4414`, `pull --rebase` `:4422`, `push` `:4442`), which Amendment 23 keeps a git commit everywhere (`inputs/amendment-23.md:343`). A new row is `add-row` (`:3819`) plus `log STARTED`, with no stamp.

On lane 3's direction (6579459518) the three become one write verb, so D5's one transaction per verb holds:

```
LANES_LANE=<L> LANES_SESSION=<sid> LANES_EXPECT="row:<L> bind:<L> alias:<L> archive:<L> sid:<sid>[ row:<window name>]" \
  lanes-edit.sh start-record <L> <STARTED|RESUMED> \
    { --add-row <row> | [--append-cell <anchor> <entry> <why>]… --state "<phrase>" } \
    --log "lane:<L>" '→' "<payload>"
```

- **The arm:** one lock, one capture and one `handle_preexisting`; the edits in today's order, the cell, the state, the log line; one `commit_push` of `lanes/LANES.md` and the lane's log, with one message; then `lane_state_follow` after the lock, as `write_event` does (`:6164–6179`).
- **On primary:** one attempt loop, one snapshot of the two files, one journal entry, one write id and one render; PP-B's expectations are the union of W2, W6 and W7, checked together against `lane-start`'s `act-begin` base.
- **The message** ([L4-a]): `LOG(<L>@<WS>): RESUMED lane:<L> + row: LIVE, session cell → <sid>`. No reader parses a register commit's subject: a grep for `--grep` and `LOG(` over `lanes-edit.sh`, `lanes-index`, `lane-start`, `lane-handoff` and `lane-end` finds only the writer's own `we_msg` (`:6159`).
- **Today's non-fatal cell append is kept** (`lane-start:3849–3852`) inside the one transaction: an anchor that does not hold is left out and said. On primary `row:<L>` already refuses a real race with 2.

| `start-record` exits | `lane-start` launching | `lane-start --no-launch --bind` (the guard's child) |
|---|---|---|
| 0 | the Rule 3 stamp, then the launch | the stamp; the record ends `bound` |
| 2: an expectation (`bind:<L>` and the rest), LR010, or any other 2 | `die 2`: any 2 at `:4181` dies, LR010's included, as the arms row in "The callers' default arms" has it; an expectation's 2 is a second binding. This is a deciding refusal, not a stamp | `bind-failed`, `rc=2` |
| 69, 75 or 3, where it makes the lane's row (`--add-row`) | dies with the code: the row is a write that must exist (2A) | `bind-failed`, `rc=<code>`; nothing is bound. The guard's offer can name a new lane: D5's "create a new lane", quoted at `code/lanes-edit.sh:10956–10957` |
| 69, 75 or 3, where it makes no row: a resume, or a lane that already has one (`--append-cell`, `--state`, `--log`) | a note with the writer's printed rerun, the generation included; the stamp; the launch goes on (2A) | `bind-failed`, `rc=<code>`. On a 3, `write_id`, `generation` and `not_after` are kept for the next `2`'s lookup |

**Why the stamp still runs on a resume's 69, 75 or 3.** The launch goes on under 2A, so the session does resume. The Rule 3 stamp then records something true, and the register's record of it is what the printed rerun completes. Lane 4's earlier "no stamp on a 3" came from the no-launch case, where nothing resumes. It stays in the guard's child, which writes no stamp after a failed bind.

| What cannot be in the transaction | Why | Where | If it fails |
|---|---|---|---|
| the stamp | not a register file (Q18(A)); Amendment 23 keeps it a git commit everywhere | after the transaction, as today's "AFTER 5b" (`lane-start:4210`) | a note, printed with its command at "MOVED"; it never undoes the bind |
| the sidecar | host-local | after COMMIT | today's note |
| the guard's `set-row-state` on the lane the window left | another lane's row, written only after the bind; each verb keeps its expectations to one lane | the child, after `lane-start` succeeds | a note in the record |

Folding the lane left into the verb (`--leave <from> <line>`) would make answer 2 one transaction; it is not proposed, and is listed for lane 3. Files mode gains the verb too: three register commits and pushes become one, and the partial states that `lane-start:3828–3833` and `:4210–4214` reason about go away (the saving is not re-measured). After the change answer 2 is two register writes (`start-record`, and `set-row-state` on the lane left) plus the git stamp: three writer calls, where the frame counts five at the pin (`design.md:2993–2997`). The frame's direction (`:3005–3008`) is the bind's four writes as one verb; the fourth, the stamp, cannot join the transaction (the table above), so `start-record` makes the other three one.

#### Phases, and AT-F6

- **Phase 1:** in the guard, `mode=binding`, `guard-bind`, the fork, the case table, the rename back on failure, and `pid_start_of`; in `lane-start`, the idempotent-bind read rule, a small separate PR re-cited at each landing, since 2a and 2b also edit it. None touches a write seam. It also fixes today's files mode: today a 10.47 s bind runs inside the 5 s hook, and at the timeout Claude Code SIGTERMs the hook's whole process group (the hook probe's R1, R1b), so the bind and any `( … & )` child it forked, such as `index_nudge`'s, die part way through, which is more than the prompt passing. So it is placed first: phase 1's first guard PR, and the fix for lane 3's lane-tools issue (differences row 34). A transcript showing `hook_cancelled` stays lane 3's check.
- **Phase 2:** `start-record` in both modes, `lane-start`'s arms on it and its union expectation, and the child's lookup, v2.6's `find_write(<write_id>, <not_after>)`, with `act`, `write_id`, `generation` and `not_after` in the record. The verb calls the commit seam, which 1b renames.
- **Phase 3:** nothing.
- **Under Q7(A′)** `start-record` runs once in the held session and the lookup becomes the lock wait; **under "(A″) with 69"** the busy `rc` is 69. **Under Q27(b)** the bound must also cover the stamp's 60 s wait for the lane checkout's lock behind a render (`lock_try`, `:1308–1309`), and rises to 490 s.

**AT-F6, for lane 2:** (1) answer `2` exits 2 with "BINDING" before `T0 + 1 s` while a stub `lane-start` sleeps 15 s, and a runner reading the hook's pipes sees EOF at once; (2) the child survives a group `SIGKILL` and `SIGHUP` and the record ends `bound`; (3) a prompt mid-bind gets "BINDING IN PROGRESS", is not passed, and the record is unchanged; (4) after `bound`, one "MOVED" with the notes, the record removed, the next prompt 0; (5) after a stub failure, the why, the rename back, the offer kept, attempt 2 on the next `2`, choice 2 withdrawn after the third failure; (6) a child killed with `-9` gives "no outcome", and an altered `pidstart` reads as dead; (7) a stub body past a short `LANES_BIND_BOUND_S` ends `rc=124`, or `137` where it ignores `TERM`; the outer writes the outcome, and no process of the body's group is left; a stub `set-row-state` that runs slowly after `lane-start` ends is still inside the bound; (8) two sessions answer `2` for one lane on the primary fixture: one `start-record` commits, the other is refused 2 by `bind:<L>`; (9) the bind is one register commit plus one handoff commit in files mode, one journal entry on primary, and a row change injected after `act-begin` refuses 2 and writes nothing; (10) an injected `psql` exit 2 gives `rc=3` with the id, and at the next `2` found writes no second `RESUMED` and absent binds anew; (11) a rerun after a failed stamp push writes no register line and exactly one stamp; (12) a fake tmux sees the child's untargeted calls with `TMUX_PANE` set and stdin on `/dev/null`; (13) a stub `lane-start` sees `LANES_NO_FETCH`, `LANES_ACT` and `LANES_EXPECT` unset and `TMUX`, `TMUX_PANE` set; (14) the hook string and `GUARD_TIMEOUT` are byte for byte unchanged; (15) `kill <p>` mid-bind ends the record with the signal's code and "what it wrote is not known"; (16) with `timeout` hidden from `PATH`, the record says `bounded=no`, and a child past `deadline_epoch` gets the past-the-bound text with its cost; (17) a counting `LANES_EDIT` stub sees at most `N_r` read calls on the `--bind` resume path, and the test fails where `N_r` must grow.

**Evidence.** PP-D's probes, in `runs-165/sandbox-PP-D/`, 2026-11-02 about 14:30Z, Linux on py-bench, load 10 to 19, no lane tool, tmux or register touched: P1 `timeout -s KILL 1` on the parent, `nudge` form "done: NONE", `setsid` form done; P2 a group `KILL` and a group `HUP`, `nudge` NONE both times, `setsid` done both times; P3 the runner saw EOF after 3.03 s with the hook's stdio inherited, 0.04 s for both other forms; P4 a pid kill, both done; P5 live 200 of 200, reused 0, dead 0.

### The acts and their expectations (D5, F5)

**The form differs from D5's example** (`--expect-row <lane> <digest>`, `design.md:345–347`): a base seq with scopes, excluding the act's own entries. A digest fails twice. An act's own earlier writes change it: `lane-start` changes the row's session cell (W2–W5) before `set-row-state LIVE` (W6); `lane-handoff` adds a lane-kind line (W9) before W11 and W12; `lane-end`'s gate sweep is a child that writes `NOTED` lines before W13; chaining it would make every write report its new digest and every script pass it on. And a digest of a read verb's answer would copy that verb's logic into SQL, against the Goals' "reused, never rewritten in SQL"; a digest of the text it read is a scope with a worse failure.

- **The base.** The act runs a new read verb, `lanes-edit.sh act-begin`, before its first deciding read.
  - On primary it makes one `lanes-index register snapshot` call. It reads the head seq `n` and the switch's `generation` in that call's one REPEATABLE READ READ ONLY transaction, and prints `<act-id>@<n>:<generation>`.
  - It exits 69 where that read fails, or where the switch is not `primary` with the marker `present`, since every write of the act would then be refused with 69 as well.
  - Off primary it prints `<act-id>@files` and touches no database.
  - `<act-id>` is 32 lower-case hex digits from 16 random bytes, new for every act, since an act's own entries never refuse it.
  - The act exports the line as `LANES_ACT` to its own verb calls and its children.
- **The form.** `LANES_ACT` is `<act-id>@files`, or `<act-id>@<seq>:<generation>`.
  - `<seq>` is 1 to 18 digits, with no leading zero except `0` itself. `<generation>` is a lower-case 8-4-4-4-12 uuid.
  - A bash 3.2 client splits it with `${LANES_ACT%%@*}`, `${LANES_ACT#*@}`, `${rest%%:*}` and `${rest#*:}`, and tests each part with a `case` pattern of spelled-out bracket lists.
  - It becomes the entry's `act`: `{id, base: <seq> as a JSON number, generation, expect}`.
- **The scopes.** Each write takes `LANES_EXPECT="<scope> …"` as a prefix on its own call, as `LANES_LANE` and `LANES_SESSION` ride today, and it is never exported.
  - The scopes are separated by white space.
  - Each scope is `row:`, `bind:`, `objects:`, `alias:` or `archive:` followed by a name that `check_lane_name` accepts, or `sid:` followed by a uuid.
  - They are sent in lower case, in the order given.
  - It is a variable, not a flag, because `log` and `append-line` take free-text positionals.
  - They are split under `set -f` (noglob), in a subshell, so that a lone `*` cannot expand to file names.
  - A token outside these forms, `*` and `row:*` included, exits 2 before any database call: `LANES_EXPECT carries "<token>", which is not row:, bind:, objects:, alias: or archive: and a lane name, or sid: and a session uuid. Nothing was sent.` It is never dropped, since dropping a token under-checks.
- **The check,** in the commit transaction (lane 1's step 3b, S1's step 3.2):
  - Refuse with 2 and `expectation: <scope> changed at seq <k> by <lane>/<verb>` (LR009) when a journal entry after the base, made outside this act (by another act or by none), touched a listed scope.
  - An import, a cutover or a rollback touches every scope (`*`), and is reported as the first listed scope.
  - A base from another generation is refused with 2 (LR007).
- **What the journal needs (lane 1, v2.4):** `journal.act_id` and `journal_scopes`, derived inside `commit_write` (steps 5a and 8a) from `build_records`' rules (`code/lanes-index:765`) and never sent by the client. An agreement test runs `line_scopes` over every line of the imported register against the same rules in Python, since a rule that drifted would under-report; it is lane 2's, in its pass after the review (S7; the routed list at the end of this file).
- **Re-basing.** `act-begin --rebase` keeps the id and takes a new seq and generation from one snapshot, only after a deliberate wait for another place's write.
  - It runs before the act re-reads the scopes it keeps. A write that lands after the new base is then checked, and the act refuses on any change it did not expect.
  - It refuses with 2 where `LANES_ACT` is malformed, where the base is `files` on primary, or where the generation has changed.
- **Before a launch** `LANES_ACT` is unset (`exec`, `respawn-pane`), or the session and its guard would inherit it. **In files mode** both variables are ignored.
- **The client's checks on primary, before the first snapshot,** in `lanes-edit.sh`'s primary branch, and again in `lanes-index register commit` before `psql`. Each exits 2, names the variable, and sends nothing:
  1. `LANES_ACT` is malformed;
  2. `LANES_EXPECT` is not empty and `LANES_ACT` is unset or empty;
  3. a scope is outside the six forms, `*` included;
  4. the base is `files`: "LANES_ACT was taken off primary (`<id>@files`) and this write runs on primary: run the act again from its start".

  **At each attempt's snapshot,** after the write id and the switch (L's order; step 1 of "How an attempt runs the verb"), it also exits 2 where:
  - the act's generation is not the switch's;
  - the act's base is past `n`;
  - `LANES_WRITE_GENERATION` is not the switch's.

  The server's LR007 and LR004 for an act are then backstops.

  A well-formed `LANES_ACT` with an empty `LANES_EXPECT` sends `act` with `expect: []`, which records `act_id` and checks nothing. Files mode reads neither variable.

| Scope | What it covers | The reads it stands for |
|---|---|---|
| `row:<L>` | the lane's row line, keyed as `row_line` keys it | `register-row`, the row count, `managed-projection`'s marker, the `RETIRED` word |
| `bind:<L>` | the lane-kind lines of `lanes/log/<L>.md`: `STARTED`, `RESUMED`, `PAUSED`, `ENDED`, `RETIRED`, `RENAMED` | `binding`, `lane-last`, `lane-agent`, `lane-transcript`, `last-session`, `lane-dir`, `window-lane` |
| `objects:<L>` | the lane's live claims and open takers in the structured layer (D18, D19) | `lane-objects` |
| `alias:<L>` | alias lines naming the lane on either side | `canon-lane` |
| `archive:<L>` | archive rows naming the lane | `retired-identity` |
| `sid:<uuid>` | any row line or lane-kind line containing the uuid | `session-lane`, and the register half of `live-holder` and `transcript-holders` |

A requester's `HANDOFF-REQUESTED` line is not a lane-kind line, so it never refuses the holder's own handoff.

**Two reads stand for more than one name.**

- **`canon-lane` stands for its whole walk.** It follows alias lines hop by hop, and a row wins at every hop (`code/lanes-edit.sh:5573–5588`, `:5621`), so its answer changes when a row or an alias line appears for any name on the walk, not only for the answer. A new flag, `canon-lane --walk <name>`, prints the names the walk visited, the typed name first and the last hop last, one per line, with `canon-lane`'s exit codes. An act that decides on `canon-lane` takes the names from `--walk` and lists `row:<n>` and `alias:<n>` for every name `<n>` on it. In the lists below, `alias:L` stands for those keys; for a lane with no alias the walk is `L` alone, and the keys are `row:L alias:L`, as before.
- **`retired-identity` stands for every spelling its `key()` folds together.** `RETIRED_ROWS_AWK` (`code/lanes-edit.sh:3743–3760`) matches an archived token whose lower-cased spelling equals the lane's, or whose `key()` equals the lane's. `key()` is the text before the last `-`, lower-cased, then `-`, then the part after it with one trailing letter dropped and its leading zeros removed (`0` stays `0`); there is no key where there is no `-` or that part is not all digits. So `foo-3` matches `foo-03` and `Foo-3b`. Lane 1's v2.5 `line_scopes` emits `archive:<key(t)>` beside `archive:<t>` for every archived token `t`, by string trimming, never a cast (lane 1's half of S3, owed by its v2.5, which this design folds: "The schema", §1.3's "The act"). An act that decides on `retired-identity` lists `archive:<key(L)>` beside `archive:L` where the two differ, and in the lists below `archive:L` stands for both.

#### `lane-start`

| Deciding read (`code/lane-start`) | Line | Scope |
|---|---|---|
| `canon-lane` | `:1129` (arms `:1133–1155`) | the walk's keys |
| `managed-projection` | `:1551`, called by `seam_refuse` at `:1561` | `row:L` |
| the row count, awk over `$LANES_FILE` (a verb read on primary) | `:1591–1593` (refusal `:1609`) | `row:L` |
| `register-row` | `:1629`; anchors `:1808–1809`, `:2136`, `:2191` | `row:L` |
| the `RETIRED` word in `$row` and `$pub_row` | `:2322` | `row:L` |
| `retired-identity` | `:2301` | `archive:L`, and `archive:<key(L)>` where it differs |
| `lane-last` | `:2329` (refusal `:2335`) | `bind:L` |
| `register-row` of the window's name | `:2086` | `row:<window name>` |
| `register-row`, `register-row-local` of the window's name, new lane only | `:2369` | `row:<window name>` |
| `session-lane` | `:2184` | `sid:<win_sid>` |
| `binding` | `:2437`, again `:2614` | `bind:L` |
| `lane-agent` | `:2522` | `bind:L` |
| `lane-transcript` | `:3075`, `:3405` | `bind:L` |
| `swapped`, `lane-dir` | `:1251`, `:1267` | `bind:L` |

Host-local reads need none: `live-holder`, `idle-holders`, `transcript-holders`, `window-session`, `restart-intent`.

| Write | Line | Expectation |
|---|---|---|
| W1 `add-row` | `:3819` | `row:L alias:L archive:L bind:L sid:<sid>`, plus `row:<window name>` where `:2369` ran |
| W8 `request-handoff` | `:2497` | `bind:L` |
| W2–W5 `append-session-id` | `:3848`, `:3869`, `:3906`, `:4019` | `row:L bind:L alias:L sid:<id appended>` |
| W6 `set-row-state LIVE` | `:3985` | `row:L bind:L alias:L archive:L` |
| W7 `log STARTED/RESUMED` | `:4178` | `row:L bind:L alias:L archive:L sid:<lane_sid>` |

In phase 2, W1 or W2–W5, with W6 and W7, become one `start-record` call carrying the union of their expectations ("Answer 2's detached bind", above). After a W8 that returns 0 or 8, the holder's own `PAUSED` writes land inside the act, so at `:2614` `lane-start` must also re-base, then re-read `register-row` and refuse on a change outside the state cell; otherwise the read at `:1629` goes unchecked across the wait, 300 s by default. W7 is the binding line: a W7 refused by its expectation is a second binding, so `:4181` must stop the launch (`die 2`), not note and carry on.

#### `lane-end`

| Read (`code/lane-end`) | Line | Scope |
|---|---|---|
| `canon-lane` | `:921`, arms `:930`, `:934`, `:938` | the walk's keys |
| the row count, awk | `:1744`, refusal `:1749` | `row:L` |
| `managed-projection` | `:951`, called at `:1765` | `row:L` |
| `lane-objects` | `:1801` | `objects:L`, `bind:L` |
| `register-row`, for a lane with no log | `:1975` | `row:L` |
| `lanes --fetch --closed --prefix` | `:748` | the rows and logs of each lane listed |

The gate at `:2127` reads `LANDED` lines, which are append-only and only relax it, so it needs no expectation; it inherits `LANES_ACT`, so its `NOTED` writes count as the act's own. W13 `set-row-state ENDED/RETIRED` (`:2439`) and W14 `log ENDED/RETIRED` (`:2487`) carry `row:L bind:L objects:L alias:L`; `retire-rows` (`:858`) carries `row:<each> bind:<each> objects:<each>`.

#### `lane-handoff`

The base is taken before the first `sread` (`code/lane-handoff:1448`). Reads: `register-row` `:1649`, `:2514` (`row:L`); `window-lane` `:1655` (`row:<window name>` and `bind:<candidate>`); `swapped` `:1666`, `last-session` `:1815`, `lane-dir` `:1862`, and `lane-last` on the late path `:1922` (`bind:L`); `canon-lane` `:1704` (the walk's keys); `managed-projection` `:1723` (`row:L`). Writes: the late `log PAUSED --utc` (`:2803`) and W9 `log PAUSED` (`:2874`), `bind:L row:L alias:L`; W11 `append-line` (`:2890`), `bind:L row:L alias:L`, since its line names the lane by the spelling `canon-lane` gave (`:1704`), as W9's and W12's do; W12 `set-row-state PAUSED` (`:2913`), `row:L bind:L alias:L`. Where the window's name is not `L`, the late `PAUSED`, W9, W11 and W12 also list `row:<window name>`. A forced release writes a `PAUSED` for the holder after re-reading its log (`code/lanes-edit.sh:17157`, `:17188`); `bind:L` then refuses the holder's own record, which is right.

#### `lane-rename`, the handoff skill, and two other acts

- **`rename-lane`** (`code/lane-rename:214`) carries none: no register read precedes it, its deciding checks are inside the verb, where the head check covers them, and the reads after it (`:202`, `:227`, `:301`) decide only tmux acts.
- **The handoff skill.** Reads: step 1's block (`code/skills/handoff/SKILL.md:143–243`: `:147`, `:210`, `:218`, `:239`), step 2's `register-row` (`:294`), `lane-dir` (`:576`). Writes: `:735`, `:743`, `:786`. Each block runs in its own shell, minutes apart, so a base cannot pass from step 1 to step 4 except through the agent. So step 4's block opens with `act-begin`, re-runs the identity's register reads, and refuses if `lane` differs from step 1's; its three writes carry `row:L bind:L alias:L` and `row:<window name>` where the window's name is not `L`, the same set as `lane-handoff`'s W9, W11 and W12, since `:743` is the same `append-line` as W11.
- **The guard's answer 2:** the bind is `start-record`'s act, with the union of W2, W6 and W7 ("Answer 2's detached bind", above). The `set-row-state "$ga_from"` that today follows it in the hook (`code/lanes-edit.sh:11199`) is run by the detached child after the bind, with `LANES_EXPECT="row:<from> bind:<from>"`. Its base is the record's `act`, `<act-id>@<n>:<generation>` from the `n` and `generation` of the prefetch that made the offer (D17), exported as `LANES_ACT` for that one call only, so a change to the lane left after the offer refuses it with 2 and puts a note in the bind's record. `start-record` runs under `lane-start`'s own `act-begin` base.
- **`lane`'s `request-handoff`** (`code/lane:1132–1139`) carries `bind:L`.

**Every listed act can carry one, under three conditions:** (1) `lane-start` re-reads the row at `:2614`; (2) the skill's step 4 re-derives the lane's identity; (3) `lane-end`'s gate inherits `LANES_ACT`. Each condition refused narrows S3: without (1), the reads before a handoff wait are unchecked across it; without (2), step 1's reads are. Host-local facts and `repos.tsv` stay outside S3 by nature, as the frame's risks say.

#### Lane 4's position on D5: accept, with one composite verb

Lane 4 withdraws R2's recommendation of one transaction per act:
- the `PAUSED` record is partial on purpose: R-A11-11 writes it even when part is refused (`SKILL.md:738–742`), and one transaction per act would let a refused `set-row-state` roll back the `PAUSED` line;
- `lane-start` keeps its compare-and-set non-fatal; R2C noted that fusing it into a group aborts the log and the stamp;
- grouping across verbs needs a held session, which the Goals exclude, or a composite verb per act, which would rewrite every act;
- expectations close R2C's races, R5 and R8.

**The one exception, on lane 3's direction (6579459518):** `lane-start`'s three register writes become one composite verb, `start-record` ("Answer 2's detached bind", above). It is still one transaction per verb, for one act only. It keeps today's non-fatal cell append inside that one transaction: an anchor that does not hold is left out and said, and the state and log line are still written. Every other act stays one transaction per verb. The partial states left elsewhere (an `ENDED` row with no `ENDED` line, a lane left `SWAPPING`) are handled by the arms below. The `act_id` column leaves further grouping open for later.

#### The callers that read exit 3 as "written" (F9)

No caller of a write verb has a `3)` arm (`lane-end:2266`'s is the close-out gate's sweep code); the reading is implicit in five places.
1. **Text that says 3 is written:** the exit table's row, `code/lanes-edit.sh:486–492`; `code/lane-rename:60–65` and its note at `:241`, "a 3 means the commit is local and may already be on origin"; `rename-lane`'s comment at `code/lanes-edit.sh:16255–16262`.
2. **Acts that carry on after a failed write,** relying on the next push to carry it, which nothing does on primary: `lane-start` `:3851`, `:3872`, `:3909`, `:3989`, `:4022`, `:4181`; `lane-handoff` `:2878–2881`, `:2890–2891`, `:2913–2914`; `lane-end` `:2490`; the skill `:738–742`. On a 3, each prints the writer's printed rerun, never its own hint.
3. **Hints that are not the same command line:** `lane-start:4182` leaves out `LANES_SESSION`; `lane-handoff:2890` and `SKILL.md:743` put a UTC of their own inside `append-line`'s text, so no rerun is the same command line; `lane-handoff:2890` and `:2913` send the writer's stderr to `/dev/null`, losing D8's rerun line, and must keep it.
4. **Callers that read 3 as "not written",** the safe direction with the wrong text: `lane-start:3823`, `lane-handoff:2817`, `lane-worktrees:4156–4166`.
5. **A side effect on a rerun.** A run that finds its write committed, by its snapshot, by LR006 or by F3's lookup, runs the verb's after-COMMIT steps once. They run in the kept child of the call that committed, or in a replay at the entry's base that reaches the seam with the entry's change set, under the pending record's `tail` key; otherwise the run names them as owed. So `rename-lane`'s `lane_state_rename` (`code/lanes-edit.sh:16273`) and the flush before it (`:16271`) run (phase 2, "The tail, once"). Closed.

#### The mode query's failures (F8, D3)

`lanes-edit.sh` tests for the file exactly as `config_path` does (`code/lanes-index:161–170`): `LANES_INDEX_CONFIG` counts only when set and non-empty; `XDG_CONFIG_HOME` falls back to `$HOME/.config`; with `HOME` unset, the home comes from the password database (`_home`, `:157–158`).

| Failure | Result |
|---|---|
| a named configuration file that is missing; a key, line or 0600 check refused; a mode value other than `index` or `primary` | `lanes-index` 2 → files mode, with a warning |
| a `lanes-index` too old to have `mode` | 64 (`:2691`) → files mode, with a warning |
| `lanes-index` or `python3` missing | 127 → files mode, with a warning |
| a crash; a signal | 1; 128+n → files mode, with a warning |
| a hang, such as a stale mount under Q23(a) | the query's client timer, 2 s (D9) → files mode, with a warning |
| exit 0 with stdout that is not exactly one line, `index` or `primary` | files mode, with a warning |
| any other code, 69, 75 and 3 included | files mode, with a warning |
| 78 | exit 69, writing nothing |

In files mode the marker check then refuses on a SQL estate (D3). `LANES_INDEX=off` must never skip the query or the renderer fork: today it skips `index_nudge` (`code/lanes-edit.sh:2557`), and `lane-worktrees` sets it on every `lanes-edit.sh` call it makes, in `Helper.call` (`code/lane-worktrees:498`).

#### The callers' default arms (Q9)

"Pass the code" means the arm exits with the verb's code, not 1.

| Where | Today | The arm needed |
|---|---|---|
| `lane-start:1154` (`canon-lane`) | notes and carries on | refuse 69, 75 and 3, passing the code |
| `lane-start:1262`, `:1271`, `:3079`, `:3430` | notes | refuse 75 and 3; 69 stays a note |
| `lane-start:1332` (`resolve-home`) | silent | refuse 75 and 3 (not in the frame's list) |
| `lane-start:2184–2186` (`session-lane`) | notes on any code | refuse 69, 75 and 3 (not in the frame's list) |
| `lane-start:1555`, `:1641–1643`, `:1661–1663`, `:2305`, `:2335`, `:2444`, `:2511–2512`, `:2534`, `:2638` | already refuse | pass the code |
| `lane-start`'s writes, `:3851` to `:4181` | note | on a 3, relay the writer's rerun; any 2 at `:4181` must die, LR010's included |
| `lane-start:1240`, `:4073` (`resolve-repo`) | `:1240` silent, `:4073` notes | none: a git read on primary (D19) |
| `lane-end:930–938` | any code but 66 and the 15(d) pair falls through silently | refuse 69, 75 and 3 |
| `lane-end:769`, `:955`, `:1806`, `:1984` | already die 1 | pass the code |
| `lane-end:2490` | notes | exit with the code |
| `lane-handoff:1463`, `SKILL.md:112` (`sread`) | die 1 | a `69)` arm that re-reads from the mirror with the `read: mirror` line |
| `lane-handoff:2817` | dies 2 | pass the code |
| `lane-rename:241` | text | new text for 3, 75 and 69 |
| `lane-worktrees:508–524` (`canon_lane`) | keeps the typed spelling | refuse 69, 75 and 3 |
| `lane:416`, `:444`, `:1058`, `:1144` | refuse | pass the code |
| the guard's dispatcher, `lanes-edit.sh:16877–16881` | maps everything to 2 | none |
| 78 | — | arms only at the mode-query site |

Two of the frame's cites read better as: `lane-start`'s reads at `:1551` and `:1591`, with `:1555` and `:1609` their refusal arms; and `lane-end:921–945` has arms, at `:930`, `:934` and `:938`.

**Phases.** Phase 1: `act-begin`, the commit's expectation check against the fixture, and the read arms; in files mode these codes are never emitted, so the read arms change nothing there. Phase 2: the scripts' `LANES_EXPECT`, the write arms and the texts for 3.

### Phase 3: the renderer's git half, rollback's push, and tests 3, 5 and 6

Under the private-index form, phase 3 changes `lanes-index` and `tests/` only. Under the in-place fallback (`design.md:1958–1966`), `lane-start`'s stamp and `lane-handoff`'s commit would change too, and phase 3 would wait for 2a and 2b.

| File | Function or site | What changes |
|---|---|---|
| `lanes-index` | `render --checkout <c>` (new) | the lock, the pass, the push, the read-back, the bound |
| `lanes-index` | `render --revert <sha>`, `import --single <sha>` (new) | the two ways out (`design.md:1940–1956`), each through `register commit` |
| `lanes-index` | `cutover`, `cutover --after-loss`, `rollback` | their pushes of the operator entry with the renderer identity, the S4 read-back, and `record_marker` (lane 1's v2) (D24; `design.md:2145–2219`) |
| `lanes-index` | `reconcile` | re-renders what is missing |
| `lanes-index` | `RENDER_GIT_HALF` | set True, last |
| `tests/` | agreement tests 3, 5 and 6 | below |

#### The render lock (S2, `design.md:1877–1883`)

Settled: lane 3 accepted the `flock` in 6579562840 and folded it at `21f0191c`, in D14 (`design.md:414–422`) and S2 (`:1877–1883`). The proof is the prototype's ([L4-4], below).

- **A `fcntl.flock`, not a `mkdir` mutex:** `flock(LOCK_EX | LOCK_NB)` on `<git-dir>/lanes-render.lock`, the form `SyncLock` uses (`code/lanes-index:1539–1606`; the call at `:1564`), never the lane's write lock. The kernel releases a `flock` when its holder dies. A renderer SIGKILLed while holding a `mkdir` mutex leaves the directory, every later fork leaves a rerun mark nothing reads, and that checkout renders nothing again; the prototype showed it ([L4-4], below). `lanes-edit.sh` records the same failure for its own `mkdir` lock (`code/lanes-edit.sh:1279–1285`), which needed a pid test and a ten-minute age-out (`:1286–1296`, `:1308–1332`).
- **Children cannot hold it:** Python opens the lock file close-on-exec, so a `git push` that outlives a killed renderer does not keep it.
- **Both halves of `SyncLock`'s handshake** (`code/lanes-index:2225–2251`), which S2 should state: a fork that finds the lock held writes `lanes-render.rerun` and tries the lock once more before it exits (`:2233–2238`); the holder, after it releases, looks for a mark once more and takes the lock back for it (`:2247–2251`). Without the second half, a write that commits between the holder's last look and its release is never rendered; the prototype showed this.

#### A pass (refines `design.md:1884–1897`)

- Fetch `origin main` under `LANES_GIT_TIMEOUT` (`git_net`'s bound, `code/lanes-edit.sh:2012–2029`); find `m`; check for a divergence; read the head `h` with the renderer's bounds (the timeouts table).
- **Finding `m`: form C, paged with `--skip`, bounded by `LANES_GIT_TIMEOUT`.** The renderer uses form C, the guard's lookup (SVC-12, `design.md:3116–3127`), in pages: `git log --first-parent -n 200 --skip <s> --format='%H%x00%(trailers:key=Register-Seq,valueonly)'`, 13 ms a page on the real register, taking the first non-empty value. Not `--grep`, which walks the whole first-parent line when nothing matches (about 300 ms on the real register's 30,006 first-parent commits, with or without `-n 200`; lane 2, 6579627366). Unlike the guard, which refuses 2 after one page, the renderer pages on until it finds one, the whole walk bounded in time by `LANES_GIT_TIMEOUT`. If the time runs out, or the whole line carries no `Register-Seq`, it renders and pushes nothing, records why with the mirror state (`record_mirror_state`), and ends; `status` shows it.
- **The target is `t = min(h, m + 64, o − 1)`,** where `o` is the first operator entry after `m`, known by `journal.operator` (lane 1's v2), never by its edits or its number.
- Under the lock, clear `lanes-render.index` and its `.lock` before each build: a renderer killed inside `read-tree` or `update-index` leaves that `.lock`, and git then refuses the next `read-tree` with "File exists".
- Build `m+1 … t` with the private index and `commit-tree --no-gpg-sign`, setting each seq's render-set entries exactly to the images at `k` (adds, changes and removals), so the commit's render-set tree equals the images whatever its parent holds; that is what makes `render --revert` work. The render set is filtered in code: on git 2.43 `git ls-tree` refuses `:(exclude)` ("pathspec magic not supported by this command"), so `lanes/log/README.md` is dropped in code.
- Every git call starts with the inherited `GIT_*` variables removed, and `LANES_NO_FETCH` dropped (D12).
- Push, then read back with `fetch` plus `merge-base --is-ancestor`. A rejected push re-fetches; where origin then carries `≥ t`, because another renderer built the same commits, that counts as pushed; otherwise the pass rebuilds, **six attempts in all** (Amendment 23 S4 item 5, and `code/lanes-edit.sh:2392`; "up to six times" at `design.md:1904` could be read as seven).

#### F15, the progress bound

- **Every act is bounded:** each fetch and push by `LANES_GIT_TIMEOUT`, each database read by the renderer's row of the timeouts table.
- **A pass** renders at most 64 seqs and makes at most six push attempts.
- **A process goes on** only after a pass that moved origin's last `Register-Seq` past where the pass found it (its own push, or another renderer's push of the same commits), or once for each rerun mark it finds after it releases. So its passes are at most the seqs pushed, plus the late marks, plus one. A pass that cannot progress (an authentication failure, a divergence, an operator seq its command has not yet put on origin) ends the renderer.
- **A lifetime cap of 16 passes hands over:** the renderer releases the lock and re-forks itself once, detached, only if its last pass made progress. `SyncLock`'s own cap (`code/lanes-index:2267–2274`) re-marks and waits for the next nudge instead, which strands the last write when no write follows; the handover keeps "the last write of a burst is rendered" without a process living unbounded under a steady stream.
- **The batch** stops a long backlog pass from losing every push race; batch boundaries do not change the commits (below, "Bursts").
- **Measured** on the prototype, which had neither the batch nor the lifetime cap: six lost races ended in 1.06 s with `exit passes=1`; a failed push and a failed fetch each ended after one pass with `last_failure` recorded; a divergence, and an operator seq not yet on origin, each ended after one pass.
- **A known gap:** six races lost to handoff pushes leave the last write unrendered until the next write anywhere, or `reconcile`. The handover does not close it, since it follows progress only.

#### F2, the stop at an operator seq

Where `m + 1` is an operator entry (the cutover's, at seq 1, `m+1` after a re-import or `b+1` after a restore, or the rollback's `k+1`), `t ≤ m`: the pass pushes nothing and the renderer ends, since that seq's command owns it. Once its read-back has put it on origin it is `m`. A pass that pushed seqs before `o` is followed by one more pass, which ends. Shown on the prototype: with seq 1 not yet on origin the renderer logged `stop-at-operator seq=1`, pushed nothing and exited after one pass, and once the cutover pushed seq 1 the next render rendered 2..3; it rendered 4..5 and stopped at the rollback's 6, and after the rollback's own push and read-back `lanes/STORE` was gone from origin and the renderer had nothing to do.

**The stop conditions,** each leaving nothing on any HEAD: nothing missing; an operator seq next; `diverged at` recorded (D16, through `record_mirror_state`, lane 1's v2); a fetch, authentication or six-rejection failure, recorded with the mirror state; a failed journal read; the cap's handover.

#### The push URL for the deploy key against origin's HTTPS remote (D23, Q21(b)(B))

- **The problem.** A deploy key authenticates only over SSH. `git push origin`, as `design.md:1901` says, would use the checkout's HTTPS remote and the lane's own credentials, which the ruleset refuses.
- **The URL.** The renderer, the cutover and the rollback push to an explicit `ssh://git@github.com/<owner>/<repo>.git`, where `<owner>/<repo>` is `_norm_repo` (`code/lanes-index:367–375`) of the checkout's own `git remote get-url origin`, so the push names the repository that was fetched; it must equal `source_of`'s (`:378–397`). A non-GitHub origin with `render_key=` set is refused with 2.
- **The push's own environment:** `GIT_SSH_COMMAND='ssh -i <render_key> -o IdentitiesOnly=yes -o BatchMode=yes'`; `SSH_AUTH_SOCK` removed, so no agent key is offered first; `GIT_CONFIG_GLOBAL=/dev/null` and `GIT_CONFIG_NOSYSTEM=1`, so a global `url.….insteadOf` cannot turn the URL back into HTTPS; `--no-verify`, so a lane's `pre-push` hook does not run. Nothing is written to the checkout's config: no `set-url`, no `pushurl`.
- **The read-back.** A push to a URL does not move `refs/remotes/origin/main`, so the read-back fetches `origin` (HTTPS, read rights) and checks ancestry.
- **Without `render_key=`** (Q21(b)(A), and the bare-origin test fixtures), the push goes to `origin` as it stands. One push function serves the renderer, `cutover`, `cutover --after-loss` and `rollback`.
- Not probed: no network was available.

#### Rollback's push

Step 2 runs the same render function in-process in the operator lane's checkout, with `--until k`, holding that checkout's render lock. Step 4 pushes the operator entry `k+1` with the push function and reads it back (`design.md:2186–2219`), then calls `record_marker` (lane 1's v2). The cutover's step 2, and the after-loss cutover, push the same way (D24).

#### Agreement tests 3, 5 and 6 (`proposal.md:838ff`)

- **Test 3, a files-mode writer refusing under the marker.** Its unit form, with a marker committed by hand on a bare origin, ships in phase 2 with the writers' marker check, since a phase must land tested (`design.md:106–108`). Its agreement form, with the marker pushed by `cutover`, is phase 3. The frame puts the test in phase 3 only; no other part of this section places it.
- **Test 5, two renderers racing, and a renderer killed between commit and push.** Phase 3; it covers [L4-1], [L4-2] and [L4-4], whose evidence is the prototype's.
- **Test 6, the v1.1.0 `lanes-edit.sh` pushing to a primary fixture, then each way out.** Phase 3; it covers [L4-5].

#### F16: where a hand edit is found

| Where the edit is | Found by | Phase |
|---|---|---|
| uncommitted in `<W>-lanes/<lane>/` | the primary writer's look before its snapshot (`design.md:1655–1660`), at act 2's S3 step-4 site | 2 |
| the same, met by the lane's next git write (stamp, handoff) | act 2's S3 fast-forward refusal | act 2 |
| committed in the lane checkout, unpushed | S3's "unpushed commits" row at the lane's next git write; the publish act refuses a `lanes/` commit on primary | 2 |
| through the estate link into `W` | act 2's S5 fast-forward report | act 2 |
| `lanes-edit.sh commit` on primary | refused with 2 (D10) | 2 |
| pushed to origin | the ruleset first, under Q21(b)(B); otherwise the renderer's divergence check (`design.md:1928–1938`) | 3 |
| in a render | cannot happen: the private index reads only origin's objects and the journal, never the checkout's tree or index | 3 |

#### Phase 3 under the other options

Under Q27(b), the renderer takes the lane checkout's write lock and S3's rows and fast-forward, phase 3 waits for 2a and 2b, and a lane's stamp and handoff can queue behind a render. Under Q21(b)(A), no key, and the push function's `origin` branch is the only one; (B′) is as (B). Under Q4(b)(iv), the rollback gains a path from the mirror with no database, and phase 2 adds an `ls-remote` to every attempt. Under Q18(B), (C) or (D), only the renderer's pathspec changes.

### The cost of one attempt, [L4-c]

**Result.** One attempt under (A″) costs about **0.55 s** on the measuring host with the smallest verb arm: 0.54 s as a sum of parts, and 0.58 to 0.61 s measured whole under load. A verb that reads the register once adds about 0.2 s. No figure in the frame is exceeded: 30 s (D7) holds about 50 attempts. The cost bears on the conflict window and on how many verb runs a burst re-does.

**Method.** Linux 6.18 WSL2, 32 CPUs, Python 3.12.3, bash 5.2.21, git 2.43.0, a load average of 10 to 24 from other lanes, so absolute figures drift about ±25% between runs; compare figures within one interleaved run. The fixture is a workspace clone with a 1.3 MB `LANES.md` (1,304,871 bytes, 6,410 lines), 38 lane logs (their sizes assumed), `aliases.tsv`, an `archive/`, `repos.tsv`, `STORE` and a `handoffs/` README: 45 files, 2.78 MB. Medians (p90) in ms of 25 to 60 repetitions after warm-ups, from `env -i` runs. `psql` was not on the host: its figures are lane 2's R1, 14.7 ms per write (p95 22.8, `proposal.md:1243–1244`) and 0.97 ms in a session.

| Part of one attempt | Median (p90) ms | n |
|---|---|---|
| Interpreter, `python3 -I -c pass` | 19.1 (25.6) | 60 |
| Python start, `lanes-index` as a script with `load_config()` and `PostgresStore` built, no store reached | **109.8 (137.0)** | 60 |
| …as a cached-`.pyc` module | 71.3 (92.0) | 60 |
| `psql` start (R1) | **14.7** (p95 22.8) | R1 |
| Scratch tree from images: write the files, `git init`, `add`, one commit | **162.0 (176.6)** | 25 |
| …`git archive HEAD` into tar, then init, add, commit | 158.6 (197.8) | 25 |
| …`cp -a` of a one-commit template | 85.9 (95.2) | 25 |
| …`cp -al` (hardlinks): unsafe, see the scratch tree | 52.8 (59.2) | 25 |
| …`git clone --local` of the template | 115.1 (131.7) | 25 |
| …`git worktree add --detach`, mirror at exactly `n` / two files behind | 42.2 (54.7) / 111.7 (138.2) | 25 |
| Verb: `bash -n lanes-edit.sh` | 42.9 (54.7) | 30 |
| Verb: no argument (exits 2 at `code/lanes-edit.sh:15072`) | 119.6 (159.9) | 30 |
| Verb: `workstation`, the smallest arm | **124.4 (144.5)** | 30 |
| Verb: `verify-row`, one register read; on a scratch tree | 318.0 (370.7); 363.7 (409.2) | 30 |
| The snapshot's cache check, 45 files read and hashed | 3.0 (3.6) | 40 |
| A cache miss, 1.3 MB through a child's pipe | 10.3 (13.2) | 40 |

| Sum, as the frame counts one attempt | ms |
|---|---|
| 2 × Python, 2 × `psql` (R1), scratch tree, smallest verb: 220 + 29 + 162 + 124 | **535** (p90s about 640) |
| with a verb that reads the register once | about 730 |
| measured whole, `psql` excluded, 2 runs × 30: 575 and 582 median | about 605 to 612 with `psql` |
| the first row with lane 2's 52.5 ms per SERIALIZABLE write (6579356984) in place of R1's 14.7 ms: 535 − 29 + 105 | about 611 |

**Where the time goes.** A Python start is about 20 ms of interpreter, +24 to 49 ms of stdlib imports, about 40 ms compiling the 2,695-line script on every start (in-process `compile()` 30.3 ms, about 11 µs a line, so every 1,000 lines the `register` subcommands add cost about 11 ms per start), and 2 to 5 ms for `load_config()` and the store (one `git rev-parse` probe, `code/lanes-index:262`). The verb's bash is mostly setup: about 20 forks before the dispatcher, and the 3,835-line `case` (`:15143` to `:18977`) parsed before any arm runs.

**The conflict window.** From the snapshot's read to the commit's `UPDATE` it is the tail of call 1 plus the scratch tree, the verb, Python start 2 and `psql` start 2: **at least 410 ms** in the script form, not R1's 14.7 ms, as the frame's risk and ADV-C7 say. At one write per 18 s on average, a 0.41 s window is crossed about 2.3% of the time (arithmetic, not a measurement). In a burst of ten there are 55 verb runs and 110 Python starts, about 28 CPU-seconds; the last writer's 10 attempts take about 6 s, inside D7's 30 s. Test 4 measures bursts.

**The named-pipe alternative.** One `lanes-index` per attempt over two FIFOs: `mkfifo` of two is 1.4 ms; a warm bash-to-Python round trip 177 µs (p90 237, n=300). Whole attempts, interleaved, N=30, `psql` excluded: 575 (644) and 582 (633) ms with two starts, against 472 (580) and 471 (507) with one server, a paired saving of 105 and 114 ms. Its total is about 427 ms by parts (500 measured whole with `psql`), 20% under 535, and about 413 ms if it also kept its `psql` connection between the calls, which (A″) excludes (`design.md:1686–1689`). The conflict window drops to about 300 ms (−27%), probably the better reason to adopt it. Its robustness costs: `printf x > fifo` with no reader hangs (probed: `timeout 2` exited 124; `exec 3<>fifo` did not hang), so every bash-side open must be read-write; bash 3.2 has no `coproc`, so the writer keeps a cleanup trap; and an orphaned server needs its own idle and lifetime cap.

**Recommendations.** Do not adopt the pipe pair in phase 1; the frame lists it as deferrable (`design.md:3521–3522`). Decide at phase 2 from tests 9 and 4. A cheaper first step is trimming the Python share (lazy imports on the `register` path; a cached module, about −38 ms a start), an install-layout decision (`INSTALLABLES`, `code/openRepoTools:1292`, and the pip package). Build the scratch tree from images. Beside these: on primary there is also one Python start per write for the mode query (D3) and one detached renderer start after COMMIT, so one process per write would save more than one per attempt (about 2.3 s for the tenth writer of a burst). Under (A′) or (C), an attempt is about 0.43 s and a burst of ten runs 10 verbs, not 55, with a stuck verb blocking everyone; under (A) with six attempts, about 3.6 s; under (B), about 125 ms of Python and `psql` (arithmetic from the table). Lane 5's "about 72 ms" for the prefetch's start (`design.md:2934`, `:3034`) agrees with this floor.

**What test 9 records,** per attempt, beside files mode: Python start to `main`'s first line, the snapshot call, the scratch tree ready, the verb's exit, and the commit call, with the host's load average and the Python version.

### The renderer prototype: [L4-1] to [L4-5], and F29

A prototype renderer in stdlib Python, the form `lanes-index render` would take, was run on git 2.43.0 and Python 3.12.3 against: a bare origin; a workspace checkout `W`; lane checkouts made as Amendment 23's S1 makes them (`clone --reference W --single-branch --branch main`); and a fake journal standing in for SQL (seq 0 the import's baseline at `C`, each entry with a fixed author, UTC and file images, operator entries flagged). Each render commit is built on `origin/main` in the lane checkout's object store: a private `GIT_INDEX_FILE` in its git directory, `read-tree <parent>`, `hash-object -w --no-filters --stdin`, `update-index -z --index-info`, `write-tree`, `commit-tree --no-gpg-sign -p <parent> -F -`, and `git push origin <sha>:refs/heads/main`, never forced.

**The private-index form passes all five, with the `flock` lock. The in-place form (`design.md:1958–1966`) is not needed.**

| Claim | Proof | Result |
|---|---|---|
| [L4-1] two renderers make identical commits | two checkouts, one with hostile config and `TZ=Asia/Kolkata`, build from one tip | **passes:** identical, byte for byte (4 commits) |
| [L4-2] a lost push leaves nothing to discard | a push rejected by another lane's handoff | **passes:** HEAD, the symbolic HEAD, the index file, the work tree and `status` byte-identical; the renderer refetched, rebuilt and pushed; S3's rows read unpushed 0, staged 0, dirty 0; the lane's handoff then fast-forwarded and pushed |
| [L4-3] a rerun mark coalesces a burst, the last write included | forks in bursts against passes of fixed length | **passes, qualified:** "at most two renders" holds for a burst that arrives within one pass; a slower stream gives one render per pass, never one per write |
| [L4-4] a renderer killed between commit and push blocks nothing | SIGKILL after `commit-tree` | **passes with `flock`; fails with `mkdir`** |
| [L4-5] each way out leaves a full render | a hand edit and a forged trailer, then each way out | **passes:** each render set's tree id equals a full render of the journal head |

**Lost races.** The lost commit is referenced by nothing and has no reflog entry. Two renderers pushing the same sha one after the other: the second push is a no-op (`=`). At the same moment: one gets `[remote rejected] (failed to update ref)`, refetches, and finds nothing to push. Neither leaves anything to discard; the frame's "a second push of the same sha is a no-op" (`design.md:1907–1908`) holds only for the first case.

**Bursts.** 10 forks in 0.71 s against a 1 s pass gave 1 push and 2 passes. 10 forks in 0.70 s against a pass of about 0.1 s gave 5 renders; 20 forks in 1.48 s against a 0.3 s pass gave 3. In every run one process held the lock, every other fork exited at once (9 of 10, 19 of 20), the last write was on origin, and no mark was left. Grouping does not change the commits: seq 11 was `313df17` in one push or in five. With the holder's post-release look switched off, the journal head was 3, `origin/main` at seq 2, and a mark was left.

**Killed between commit and push.** With `flock`: the lane checkout was unchanged, S3's rows empty; the next write's render took the lock and pushed seqs 2..4; the lane's handoff pushed and read back; the killed renderer's unreferenced seq-3 commit (`47fe5c73d840`) was rebuilt with the same sha and is now on origin. With `mkdir`, after the same kill: the next fork left its mark and exited, and the mirror stayed at seq 1 with the journal head at 4. A kill inside `read-tree` left `lanes-render.index.lock` ("File exists"), which the pass now clears under the lock.

**Divergence and the two ways out.** A hand edit of `lanes/LANES.md` or `lanes/aliases.tsv` stopped the renderer with `diverged at <sha>` recorded and nothing pushed; a stray carrying a forged `Register-Seq: 3` was caught too, since the render set at `m` did not match the images at `m`. `import --single` worked, and refused an edit that no longer applied ("patch does not apply"), after which `render --revert` cleared the divergence. One cost of the import: writes landed between the stray and the import are rendered over the stray before the import's own seq, so seq 4's commit (`LOG(...): c`) flips the stray's `PARKED` line back and seq 5 restores it. The end state is right, but that line's history toggles; the revert, or importing before the next write lands, avoids it.

**F29: `commit-tree`'s determinism.** Each input that could change the sha, its pin, and what removing that pin alone did in checkout B:

| Input | Pin | Pin removed alone |
|---|---|---|
| Blob bytes: clean filters, `core.autocrlf`, `.gitattributes` | `hash-object --no-filters --stdin`, `-c core.autocrlf=false` | sha differs |
| Replace refs and grafts (which also move `m`) | `GIT_NO_REPLACE_OBJECTS=1` | sha differs |
| Author and committer name and email | `GIT_AUTHOR_*`, `GIT_COMMITTER_*` from the journal | sha differs |
| Dates and time zone | `@<epoch> +0000`, `TZ=UTC` | sha differs (`+0530`) |
| The `encoding` header | `-c i18n.commitEncoding=UTF-8` | sha differs (`encoding ISO-8859-1`) |
| Signature | `--no-gpg-sign`, `-c commit.gpgSign=false` | **same sha:** 2.43's `commit-tree` ignores `commit.gpgSign`; only `-S` signs (probed: 0 `gpgsig` headers, and 1 with `-S`) |
| The trailer's place (`trailer.*` config) | `interpret-trailers` run outside any repository, system and global config off, `where`, `ifexists`, `ifmissing` and `separators` given | sha differs (`trailer.where=start`) |
| Inherited environment (`GIT_DIR`, `GIT_INDEX_FILE`, `GIT_CONFIG_PARAMETERS`/`COUNT`, `GIT_REPLACE_REF_BASE`, …) | all removed | not probed |
| Mode and path bytes | always `100644`; NUL-separated paths; `-c core.precomposeUnicode=false` | macOS only, not probed |
| Object format | every lane checkout clones the one origin | not probed |
| Message bytes | `-F -` (no cleanup) | — |
| Parent | `origin/main`'s tip as fetched | different tips are [L4-2]'s case |

One input remains, the git version: how `interpret-trailers` parses a message has changed between releases, and its `trailer.*` configuration moves the trailer (`trailer.where=start`, above). So `lanes-index` composes the message itself and never runs `interpret-trailers`; then no host's git version or configuration is an input. D15 at `21f0191c` still says "as `git interpret-trailers` places it" (`design.md:428–430`).

**The final-trailer mechanics** (D15, `design.md:428–432`; lane 3's ask, `:3564–3566`). The render commit's message is the journal entry's message byte for byte, with `Register-Seq: <k>` in its last paragraph:
- where the message has a body whose last paragraph is already a trailer block, every line `Token: value` with a token of letters, digits and `-`, the trailer is appended as that paragraph's last line;
- otherwise (a subject alone, or a body ending in prose), one blank line and `Register-Seq: <k>` start a new last paragraph. A subject alone never counts as trailers, since git reads the first paragraph as the title;
- the message goes to `commit-tree -F -` with no cleanup, so every renderer commits the bytes `lanes-index` composed ([L4-1]);
- that last paragraph is the only place `%(trailers:key=Register-Seq,valueonly)` reads, so form C finds it, for the guard and for the renderer's own lookup of `m` (lane 2, 6579627366). The operator entries' commits follow the same rule;
- a test renders three messages (a subject alone, a body ending in a trailer block, a body ending in prose) and asserts that form C reads `<k>` from each and that two checkouts give one sha. The git rules above are read, not probed; the test proves them.

D15's "(it honours `commit.gpgSign`)" (`design.md:427`) is false for 2.43; `--no-gpg-sign` stays, since it overrides `-S` and costs nothing.

### The routed items, answered

Every item in "What lane 4's phase plan must confirm for S1" (`design.md:1823–1841`), "What lane 4's prototype must confirm" (`:1975–1982`) and lane 4's routed part (`:3544–3567`), with lane 3's asks of 6708646285. An item listed in two places has one row.

| # | Item | Listed at | Answered in | Answer |
|---|---|---|---|---|
| 1 | [L4-a]: each verb runs unchanged on a scratch tree | `:1825` | Phase 2, "Each write verb on a scratch tree" | All but `rename-lane`, whose handoff half becomes its after-COMMIT tail; `commit` is refused |
| 2 | [L4-a], F21: a verb that reaches the seam twice | `:1825–1828`, `:3544–3545` | Phase 2, the same | None can on primary; one run, one entry, and a second reach exits 2 |
| 3 | [L4-a′], F22: the internal flag | `:1829–1830`, `:3545` | Phase 2, "The internal flag" | `LANES_REGISTER_SCRATCH=<attempt dir>`, checked and unexported; it also skips the mode query and D17's reads |
| 4 | [L4-a′], F14: output buffering and the arms with side effects | `:1830–1831`, `:3546–3547` | Phase 2, "Output, and the side effects" | Two buffers, the deciding attempt's replayed; every side effect sits after the seam, which the child reaches once, after COMMIT |
| 5 | [L4-a′], F5: the acts and their expectations (D5) | `:1831`, `:3548` | "The acts and their expectations" | `act-begin` and six scopes, per act; every act can carry one under three conditions; lane 4 accepts D5, with one composite verb, `start-record`, for `lane-start` |
| 6 | [L4-a′], F8: every failure of the mode query, and Q6(c)'s code | `:1831–1832`, `:3547–3548` | Phase 1, "D3's refusal"; the acts, "The mode query's failures" | 78, lane 4's pick, folded in D3; every other failure runs files mode with a warning |
| 7 | [L4-a′], F10: the pending record's format and age | `:1833`, `:3550` | Phase 2, "The pending record" | The format, 15 minutes, and a digest over lane, session, verb and arguments, stored as `journal.args_sha256` |
| 8 | [L4-a′], F9: the callers that read 3 as "written" | `:1833–1834`, `:3549` | The acts, "The callers that read exit 3" | Five places, listed with their lines |
| 9 | [L4-b]: the five acts that change several files | `:1835–1837` | Phase 2, "The change set" | All five are given by `--raw` plus the images |
| 10 | The files the snapshot returns, `repos.tsv` and `log/README.md` included | `:3545–3546` | Phase 2, "The scratch tree" | The render set from SQL; those two from the lane checkout's `origin/main`, with blob ids recorded; never `STORE` or handoffs |
| 11 | [L4-c]: the cost of one attempt | `:1838–1841` | "The cost of one attempt" | About 0.55 s, about 611 ms with lane 2's `psql` figure; the frame's figures hold; the pipe pair is deferred to phase 2 |
| 12 | F3: the in-doubt rule at the deadline | `:3549–3550` | Phase 2, "The in-doubt rule" | Lane 1's v2.6 `find_write(p_write_id, p_not_after)` at every non-0 end after a doubt (L's rule), with `not_after` for an orphan not yet at the lock |
| 13 | F15: the renderer's progress bound | `:3550–3551` | Phase 3, "F15" | Bounded acts; 64 seqs and six pushes a pass; progress or a late mark to go on; 16 passes, then one handover; a known gap |
| 14 | F2: the stop at an operator seq | `:3551` | Phase 3, "F2" | `t = min(h, m + 64, o − 1)` by `journal.operator`; shown on the prototype |
| 15 | F16: where a hand edit is found | `:3551–3552` | Phase 3, "F16" | Seven places, each with its finder and phase |
| 16 | F17: the marker check for readers | `:3552` | Phase 1, "F17" | One function on the read ref, after any fetch, in the main shell; outcome by class |
| 17 | [L4-1]: identical commits | `:1977`, `:3552` | The prototype | Passes |
| 18 | [L4-2]: a lost push leaves nothing to discard | `:1978`, `:3552` | The prototype | Passes |
| 19 | [L4-3]: a burst coalesces, the last write included | `:1978–1979`, `:3552` | The prototype | Passes, qualified: two renders only for a burst within one pass |
| 20 | [L4-4]: a killed renderer blocks nothing | `:1980`, `:3552` | The prototype; phase 3, "The render lock" | Passes with `flock`; fails with `mkdir` |
| 21 | [L4-5]: each way out leaves a full render | `:1981`, `:3552` | The prototype | Passes |
| 22 | F29: `commit-tree`'s determinism | `:3552–3553` | The prototype, "F29" | Every input pinned; the git version remains unless `lanes-index` composes the message |
| 23 | The push URL the deploy key needs | `:3553–3554` | Phase 3, "The push URL" | An explicit SSH URL from `origin`'s, in an isolated environment; the read-back fetches; not probed |
| 24 | Question 10: `$SE_CACHE_FILE.log.<lane>` | `:3554–3555`, `:3291–3292` | Phase 1, "Question 10" | Phase 1, written only on primary, with a `.logpath.<lane>` sidecar and one flush helper |
| 25 | Question 13: the image cache's second writer | `:3555–3557`, `:3298–3300` | Phase 1, "Question 13" | Yes: content-addressed objects and a forward-only head under a flock make any writer safe |
| 26 | Question 11: `code/lanes-index:1315` | `:3557–3559`, `:3293–3296` | Phase 1, "Question 11" | The URL moves to `PG*` variables in `_env`; every connection, the admin's too, goes through the environment; phase 1's first PR |
| 27 | `find_write` in the in-doubt lookup | `:3559–3560` | Phase 2, "The in-doubt rule"; "Answer 2's detached bind", reruns | Yes, v2.6's form, at every non-0 end after a doubt: found, 0 and the tail once; absent with the lock held, the code the last attempt earned; otherwise (55P03, LR011, or no answer) 3 |
| 28 | The generation in the printed rerun | `:3560` | Phase 2, "The pending record" | `LANES_WRITE_GENERATION=<generation>` beside `LANES_WRITE_ID`; `commit_write` refuses another generation with LR007 |
| 29 | How the detached bind runs, and what the guard answers meanwhile | `:3560–3562`; 6579459518 | "Answer 2's detached bind" | `guard-bind` in a `setsid` child with the hook's descriptors closed, recorded in the offer file and bounded at 430 s over its whole body; the guard answers 2 from an eleven-case table, "BINDING IN PROGRESS" while it runs |
| 30 | `lane-start`'s bind as one verb, one transaction carrying F5's expectations, or the reason it cannot | `:3562–3564`; 6579459518 | "Answer 2's detached bind", "The bind as one verb" | `start-record`, one transaction with the union of W2, W6 and W7; the stamp, the sidecar and the lane left stay outside, each for a stated reason |
| 31 | The final-trailer mechanics | `:3564–3566` | The prototype, "F29" | `lanes-index` composes the message: `Register-Seq: <k>` appended to a final trailer block, or as a new last paragraph; no `interpret-trailers` |
| 32 | The `flock`'s proof | `:3566–3567` | Phase 3, "The render lock"; the prototype, [L4-4] | After a SIGKILL, `flock` let the next render push seqs 2..4; the `mkdir` mutex left the mirror at seq 1 with the head at 4 |
| 33 | The renderer's own lookup of `m`, and its bound | 6708646285 | Phase 3, "A pass" | Form C paged with `--skip`, bounded by `LANES_GIT_TIMEOUT`; out of time or no trailer, it pushes nothing and records why |
| 34 | That the detached child outlives the hook | 6708646285; the facts row, `:3204` | "Answer 2's detached bind", "It survives the hook" | Shown on Claude Code 2.1.319: the `setsid` child outlives a hook timed out by a group SIGTERM and one that exits; only a SIGKILL escalation is not shown |

### Differences from the frame, and decisions for lane 3 or Brett

**Already folded by lane 3 at `21f0191c`,** each checked against the new frame:
- Q6(c)'s code 78, `EX_CONFIG` (6579562840): D3 (`design.md:317–319`) and [L4-a′] (`:1831–1832`).
- The render lock as an `fcntl.flock` in `SyncLock`'s form (6579562840): D14 (`:414–422`) and S2 (`:1877–1883`).
- Answer 2 detached, with "binding in progress" (ruling 16): SVC-8 (`:2989–3017`), the timeouts row (`:2387`), the interfaces bullet (`:2441–2445`) and ruling 16's entry (`:3307–3312`). The in-hook form, with its hook pid and end line, is gone.
- Five writer calls for answer 2 at the pin: SVC-8 (`:2993–2997`) and the interfaces bullet (`:2441–2443`).
- Form C for the guard's `Register-Seq` lookup: SVC-12's condition 3 (`:3116–3127`).
- `Register-Seq` in the render commit's final trailer block: D15 (`:428–432`) and S2's pass (`:1892–1893`).

Every row below waits on the decision it names.

| # | Difference | Reason | Decision |
|---|---|---|---|
| 1 | S2 states the handshake's second half, the holder's look after it releases (`:1909–1915`) | without it a write that commits before the release is stranded, shown | lane 3 |
| 2 | The push goes to an explicit SSH URL in an isolated environment, not `git push origin` (`:1901`) | a deploy key is SSH-only; `insteadOf` and agent keys could defeat it | lane 3; facts for Brett: `GIT_CONFIG_GLOBAL` needs git 2.32 or later in every environment; whether `ready` can prove the key's write right (`git push --dry-run` over SSH) |
| 3 | F15 adds a 64-seq batch and a 16-pass lifetime cap with one handover re-fork; no `SyncLock`-style re-mark cap | an absolute bound per process without stranding the last write; the values are unmeasured | lane 3: the two values, and whether to accept the gap after six lost races |
| 4 | [L4-3]'s "at most two renders" holds only for a burst within one pass; "a second push is a no-op" only one after the other; "up to six times" is six attempts in all; the private index's stale `.lock` is cleared under the lock | the prototype | lane 3 |
| 5 | D15's "(it honours `commit.gpgSign`)" (`:427`) is false for git 2.43 | the probe | lane 3 |
| 6 | `lanes-index` composes the render commit's message, not `interpret-trailers` as D15 still says (`:428–430`); the mechanics are in "F29" | the git version and `trailer.*` configuration are otherwise inputs to the sha | lane 3 and lane 1 |
| 7 | The import way out toggles a line's history; the revert does not | the prototype | lane 3: keep, or prefer the revert |
| 8 | Agreement test 3's unit form ships in phase 2; the frame puts the test in phase 3 | a phase must land tested (`:106–108`) | lane 3 |
| 9 | `cutover` and `rollback`'s transactions are phase-1 code, runnable only on a scratch schema; their pushes are phase 3 | they run through no write seam | lane 3 |
| 10 | Phase 3 is `lanes-index` and `tests/` only; phase 2 ships the fork and `<c>`, which the frame puts in phase 3 (`:1875`) | phase 3 then waits on nothing in `lanes-edit.sh` | lane 3 |
| 11 | The readers' path generalises SVC-8's prefetch to every reader; the mode folds into it; a `.logpath.<lane>` sidecar; `LANES_READ_MIRROR=1` lowers `window-lane` and `swapped` to a mirror read for their callers | reads in `$( )` lose variables; one snapshot per verb | lane 3 |
| 12 | The verb runs as a child process that suspends at the seam, not a subshell that returns (`:568`, `:1705`, `:1715–1717`) | run-once tails; file-scope setup; a fresh read family per attempt | lane 3 |
| 13 | The flag also skips the mode query and D17's reads (`:1710–1712` names the resolver and S3) | recursion; one snapshot per verb | lane 3 |
| 14 | The seam refuses paths outside Q18(A)'s render set; an empty change set exits 0 with no commit call and no head check, as `code/lanes-edit.sh:15223`'s no-op does today | enforces Q18(A) in code | lane 3 |
| 15 | F3 keeps `not_after` beside lane 1's `find_write`, now v2.6's `find_write(p_write_id, p_not_after)`, which waits past the largest `not_after` on the server's clock and checks it again under the head row's lock (LR011) | the lock cannot see an orphan not yet at `commit_write`'s step 1 | closed by lane 1's v2.3 (step 9) and v2.6 (the lookup) |
| 16 | The pending record: a 15-minute age; a digest over `LANES_LANE`, `LANES_SESSION`, the verb and the arguments; the printed rerun carries `LANES_WRITE_GENERATION` | `LANES_SESSION` is written into the line; lane 1's v2 refuses a foreign generation | lane 3; the variable's name is new |
| 17 | `gh_reads` is cached across attempts | three GitHub calls per attempt spend the shared quota | lane 3 |
| 18 | `rename-lane`'s handoff move and stamp become its after-COMMIT tail, its stamp checks before the seam | the scratch tree has no handoff file | lane 3; beside Q13, for Brett: Amendment 16's "one commit, refused or whole" becomes a transaction plus a git commit |
| 19 | One run, one journal entry; a second reach exits 2 | D5 and S2; no invented message, no partial state | lane 3 |
| 20 | Expectations are scopes with a base seq and an act id (`act-begin`, `LANES_ACT`, `LANES_EXPECT`), not `--expect-row <lane> <digest>` (`:345–347`) | an act's own writes change a digest; a digest of an answer copies verb logic into SQL | lane 3; asks of lane 1: an `act_id` column and each entry's touched keys, which lane 1's v2 lacks |
| 21 | Lane 4 accepts D5 and withdraws R2's per-act transaction, with one composite verb for `lane-start` (row 30) | the partial `PAUSED` record, `lane-start`'s non-fatal stamp, the Goals | lane 3 records it; D5's row stays Brett's |
| 22 | Every act carries its expectations only under three conditions (`lane-start` re-reads at `code/lane-start:2614`; the skill's step 4 re-derives the identity; `lane-end`'s gate inherits `LANES_ACT`) | each refused condition narrows S3 | Brett, beside D5's row |
| 23 | Two arms the frame does not list: `lane-start:1332` and `:2184`; and corrected cites, `lane-start:1551` and `:1591` for the reads, `lane-end:930–938` for the arms | the code at `2a057a6d` | lane 3 |
| 24 | A run that finds its write committed runs the verb's after-COMMIT tail once: in the kept child of the call that committed, or in a replay at the entry's base that reaches the seam with the entry's change set, under the pending record's `tail` key | otherwise `rename-lane`'s `lane_state_rename` never runs; `gh_comment`, `guard_type` and the handoff's stamp are not safe to run twice | lane 3: S1's tails passage changes (lane 4's ask); closed by "The tail, once" |
| 25 | `LANES_INDEX=off` must never skip the mode query or the renderer fork | `lane-worktrees` sets it on every `lanes-edit.sh` call, in `Helper.call` (`code/lane-worktrees:498`) | lane 3 |
| 26 | The frame counts Python starts per attempt; there is also one per write for the mode query and one renderer start after COMMIT | [L4-c] | lane 3 |
| 27 | The pipe pair is not adopted in phase 1, but decided at phase 2 from tests 9 and 4; a cached module or lazy imports first | no frame figure is exceeded | lane 3 (install layout); Brett or lane 3: whether a `psql` connection may be kept between the two calls under (A″), 13.7 ms an attempt |
| 28 | The scratch tree is built from images and never hardlinks a work-tree file | `register_rewrite` writes in place (`code/lanes-edit.sh:1662`, `:1666`) | lane 3 |
| 29 | The bind's three register writes become one verb; the fourth of the frame's four, the Rule 3 stamp, stays a git commit outside the transaction, so answer 2 is three writer calls, not five (`:2993–2997`, `:3005–3008`) | Amendment 23 keeps the stamp a git commit everywhere; Q18(A) | lane 3 |
| 30 | D5 gains one composite verb, `start-record`, for `lane-start` only; every other act stays one transaction per verb | lane 3's direction (6579459518) | lane 3; the name `start-record` and its message are lane 4's |
| 31 | The detached bind's record is the existing offer file in new modes, not a new state file | it is keyed by the transcript and the guard already reads it | lane 3 |
| 32 | The facts row (`:3204`) narrows to "an escalation to SIGKILL is not shown": Claude Code 2.1.319 SIGTERMs the hook's whole process group at the timeout, kills nothing on a normal exit and does not wait on inherited pipes, and a `setsid` child outlives both | the hook probe (`runs-165/HOOKPROBE-report.md`, R1, R1b, R2, R3); PP-D's P1 to P4 | a fact for lane 3 to fold |
| 33 | Today's `index_nudge` form, `( … & )`, dies under a group kill, Claude Code's own timeout included | PP-D's P1, P2; the hook probe's R1, R1b | lane 3 |
| 34 | A finding in today's code: answer 2 runs `lane-start --no-launch --bind`, about 10.47 s in files mode (6579356984), inside the hook's 5 s; at 5 s Claude Code SIGTERMs the hook's whole group, so the bind and its `( … & )` children, such as `index_nudge`'s, die part way through, which is more than the prompt passing | the hook probe's R1, R1b; it is evidence for lane 2's inference | **a lane-tools finding for lane 3**; the phase-1 detached form is the fix, placed first; a transcript showing `hook_cancelled` stays lane 3's check |
| 35 | A finding in today's code: after a failed bind, "answer `2` again" cannot be taken, because `code/lanes-edit.sh:11155` renames before `:11171` and `:11108` then drops the offer | the code at `2a057a6d` | **a lane-tools finding for lane 3**; the rename back fixes it |
| 36 | "MOVED" is shown once, consuming one prompt, rather than a silent pass that needs the record's end before `T0` and a fresh read; at most 3 binds per offer | a failed bind spends the person's yes | lane 3 |
| 37 | The bind's bound is 430 s over the child's whole body (`lane-start`, the `set-row-state` on the lane left and `guard_bound_check`'s read) under one `timeout -k 10 420`, with `LANES_GIT_TIMEOUT=45` in the child | arithmetic, not measured: 278 + 2`v` + 6`N_r` s, 384 s at `v` = 2 and `N_r` = 17 (R2's S6) | lane 3 |
| 38 | Whether `--leave <from>` joins `start-record`, making answer 2 one transaction | not proposed: each verb keeps its expectations to one lane | lane 3 |
| 39 | The detached bind in phase 1, as its first guard PR and the lane-tools fix (row 34) | it touches no write seam; today's bind is cut down by the hook's timeout | lane 3 |
| 40 | macOS without `setsid`: python3's `os.setsid`, else `nohup` alone, which does not survive a group kill | `setsid` is not on macOS | Brett |
| 41 | A plain rerun that still finds an identical write's pid live at its deadline exits 3 with that write's id, not 75 (R2's N2) | it sent nothing, and the other write's outcome is not known; 75's "a retry in seconds is safe" can double the write | lane 3: accepted (#165 6715013264) |
| 42 | A call in doubt keeps its child at the seam; the child replays its own buffered output after `committed`; it waits in 1 s turns while its parent lives, up to a 120 s cap (R2's N5) | the exact tail; no caller's slow read stands between the call and `committed` | lane 3: accepted (#165 6715013264) |
| 43 | Under the flag, the tail's sidecar acts take the lane checkout's mutex, not the scratch tree's | `LOCK` is computed from `LANES_DIR` (`code/lanes-edit.sh:1100`); `set-lane-state` takes the checkout's (`:18585`) | lane 3: accepted (#165 6715013264) |

### Not established

- **Slices 1a, 1b, 2a and 2b were not read:** no network. Phase 2's mapping onto `commit_publish` and S3's step-4 site follows Amendment 23's text, not code.
- **No PostgreSQL was run by lane 4.** Lane 1's v2.6 ran `find_write`'s wait, its refusal of a `not_after` too far ahead, and the orphan race on PostgreSQL 16.15 (#165 6714645316). LR011 under the lock was not run, since it needs a clock that steps back. Lane 4's client side is reasoned, not probed: its wait on its own estimate, the window, the tries, the kept child, the replay and the `tail` key. Only the FIFO handshake was probed (part MA's P1 to P5, 2026-11-07). No `psql` start was measured; [L4-c] uses R1's 14.7 ms, against lane 2's later 52.5 ms per SERIALIZABLE write over a Unix socket (6579356984), which [L4-c] adds as one line, about 611 ms.
- **The renderer was not run against the real `lanes-index`,** a real database (`state.json` stood in for D16), concurrent journal appends, more than two racing renderers, a kill during a push, or `gc --auto` after a fetch. The forged-trailer case with a seq beyond the head was not probed; the render-set comparison ignored file modes. The batch and lifetime cap of F15 were not in the prototype.
- **The push URL, the deploy key and `insteadOf`** are from knowledge, not probed. `plan_hook_merge`'s handling of a changed `timeout` was not checked, only the `conflict` arm.
- **Answer 2's detached bind** (PP-D and the hook probe): an escalation to SIGKILL after Claude Code's SIGTERM (it cannot reach a `setsid` child, but was not run); tmux's resolution through `TMUX_PANE` from a child with no tty (AT-F6 item 12); macOS, bash 3.2 and the python3 fallback; whether today's transcripts show `hook_cancelled` (lane 3's check); whether `lane-start:3869`'s restored append runs under `--bind` (the repeatable `--append-cell` covers it); no figure for the bind after `start-record`; the 430 s bound, `N_r` and the 7 ms check, neither measured on primary.
- **Other platforms:** only git 2.43.0, Python 3.12, bash 5.2 on Linux ext4. Not macOS, bash 3.2 (`exec 5<>fifo`, `read -t`), Python 3.9, sha256 repositories, or `flock` on drvfs, 9p or NFS.
- **Not read in full:** `proposal.md`, `clarifications.md`, `council-review.md`, the specs, SVC-4, SVC-6, SVC-9 to SVC-11 and lane 5's facts table; `lane` past `:1296`.
- **Not checked:** the tests' assertions on exit 3; `request-handoff`'s own re-read of the binding apart from `--force` (`:17188`); whether `retire-rows` re-proves dormancy (R2C's claim); whether `lane-start`'s inner write arguments are deterministic across a rerun; the real register's log sizes and the size of the new `register` code ([L4-c] assumed 38 logs and 120 commits); a verb run under the flag; ten attempts at once on one host (test 4's job); the teardown of non-worktree scratch trees.
- **Relayed, not re-printed by lane 4:** `lane-start:1262`, `:1271`, `:3079`, `:3430`; `lane-end:921–945`; `lane-worktrees:508–524`; `lanes-edit.sh:7428–7429`, `:8104–8109`, from R2B, R2V1 and R2V2.

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
  never a hang. Once any commit call of a write was in doubt, the writer ends
  with no code but 0 until it has made F3's lookup, `find_write(<id>, <n>)`:
  it waits until the database clock has passed `<n>`, the largest
  `not_after` its commit calls carried, takes the head row's lock within the
  reconnect window, checks the clock again under it, and looks its write id
  up. Found, it exits 0 and runs the verb's after-COMMIT tail once; absent, with
  the lock held, it exits the code its last attempt earned, whether 75
  ("register busy"), 69, 2 or the verb's own; with the lock not held within
  the window, or the lookup refused (LR011), it exits 3, naming the write id.
  "Register busy" is never exited without that lookup, so no exit is a false
  "nothing was written" (D8; S1).
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
  the direct read cut to the parse's start line (SVC-8), answer 2 run
  detached, and the probes' 4.5 s assertion.
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
   operator tools; and the guard's detached answer 2, as phase 1's first guard
   PR, which fixes "Known defects in today's code" in every mode.
   `lanes-index` refuses `mode=primary` throughout. The service
   and its read API sit off the seams too, but are 007's last slice (Q29(a)),
   after phase 3.
2. **Phase 2, after slices 1a and 1b land in the code leg:** the attempt loop at
   the commit seam, the refusals of direct writes, S6's checks, the marker
   check after S3's fetch and in the publish act, with agreement test 3's unit
   form, since a phase lands tested (lane 4's row 8); `start-record`,
   `lane-start`'s bind as one write, in both modes (D5); and the renderer fork
   and `<c>`, so that phase 3 changes nothing in `lanes-edit.sh` (lane 4's row
   10; lane 3's rulings, #165 6710770304). The phase plan gives each phase's
   files, functions and call sites.
3. **Phase 3,** in `lanes-index` and `tests/` only: the renderer's git half,
   rollback's push, and agreement tests 3 (its agreement form), 5 and 6; then
   the constant that lifts Q6(c)'s refusal.
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

**Round 2, by number: answered.** Each is written above under its recommended option, and
the entry under "Round 2: answered, every option as recommended" says what changes under the
others: Q4(a), Q4(b), Q4(c), Q4(d), Q7, Q11, Q12, Q13, Q14, Q17, Q18, Q19,
Q21(a), Q21(b), Q22, Q23, Q24, Q25(a), Q25(b), Q25(c), Q26, Q27, Q28 and Q29.
The eight that stand apart are Q4(a), Q4(b), Q7, Q21(b), Q22, Q26, Q28 and
Q29. "(A″) with 69" under Q7 also decides D7's number. Brett took every
recommendation on 2026-11-07, the eight included, so D7's number is 75 (#165
comment 6710653292; `clarifications.md`).

**Round 1: RULED.** Q2(a) and (b), Q6, Q8 and Q20, held as provisional until
Brett confirmed "1A 2A 3A 4A", are RULED: he confirmed all four on 2026-11-07
(#165 comment 6710653292).

**Not yet routed to Brett:** the items so marked under "Differences from
Amendments 22 and 23, for Brett": D5's per-verb transactions, with
`start-record` as its one composite verb, and D5's three conditions, each of
which, refused, narrows S3; `rename-lane`'s after-COMMIT tail against
Amendment 16's one commit, beside Q13; macOS without `setsid`; D18 as a reading of
S6, beside Q17; D1's schema of its own; D25's `lanes-index cutover`; and a
release for a stale `LANDING`, for which the recommendation is a
`LANDING EXPIRED` line naming the holder, which any lane may write after Rule
6's window on his word, tested (S6; lane 1's v2 carries it, §1.2); a
stray landing during a rollback after its last divergence check, which has
no journalled way out (narrowed by lane 1's v2.6, R2's S5); and
Q21(b)'s scope, `lanes/**` as the round-2 file words his answer, or the
render set and `lanes/STORE` as D23 has it. Three more rows read "for
Brett: not yet answered": two from the review of commit J (R1's S1), files
mode's changes against Amendment 22's scope and D8's exit 3 for a write in
doubt; and D24's two operator seqs, which `cutover` and `rollback` push
themselves, against S2's renderer that commits every sequence the mirror
lacks, which commit M splits from Q21(a)(A)'s row, since Q18(A) settles only
that the marker is register seq 1.

**Values Brett names later:** Q29's time limit for the guard; Q22's database
host, its backups and how long it may be down; Q28's two dates, one for slices
1a and 1b and one for the other four; the expiry of any outage he declares.
Lane 3 puts the first three to him with the spec PR (#165 6710653292). Lane
4's rows 18, 22 and 40 go to him with it too (#165 6710770304).

**Facts still to find:** whether the organisation's GitHub plan supports a push
ruleset with a deploy-key bypass, and whether it can name the render set
exactly (Q21(b), D23), which nobody has checked yet and on which his Q21(b)
answer rests; for the deploy key's push (the phase plan's row 2, under
Q21(b)), whether every environment has git 2.32 or later, which
`GIT_CONFIG_GLOBAL` needs, and whether `ready` can prove the key's write
right (`git push --dry-run` over SSH); whether Eagle and Raven install by
pipx or `./setup.sh`, and whether v1.2.0 carries #165 alone (Q24).

**Deferrable within the design:** the per-call process cost, which changes no
spec: [L4-c] puts an attempt at about 0.55 to 0.61 s, inside every frame
figure, and lane 4 recommends deciding the pipe pair at phase 2 from tests 9
and 4 (the image cache's form is the phase plan's question 13); the writers'
and readers' timeout values other than the guard's, which the test plan
confirms.

### Routed to the section writers by the frame review

Lane 3's independent review of this frame (`REVIEW-frame-20261102.md`, sha256
`0689dde8…`, against `c7253108`) left these points for the sections that fold
in later. Lane 5's section, lane 1's (the schema above, and
`operations.md`) and lane 4's phase plan are folded; they settle their own
points, and route the ones below marked with lane 5's question numbers,
"from lane 1's v2" or "from the phase plan". The placeholder for lane 2 is
left as it was.

- **Lane 1, the schema and the operations:** its section v2, folded above and
  in `operations.md`, takes up the points this list routed to it (the
  operator-entry flag, the rollback's switch state, the generation id, the
  arguments' digest, the runbook's order and the disaster path; `renames.md`
  lists what moved). **Answered by lane 1's v2.3** (#165 6708990159, folded
  above): the restore form's abort, through `register_switch.restore_prior`
  (D24); `register_lines.repo_spellings` for D18's comparison; and
  `commit_write`'s step 9, LR008 unless `clock_timestamp() < not_after` (lane
  4's row 15). Lane 4's row 6, the operator entries' commits putting
  `Register-Seq` in the message by D15's rule, is ruled: `lanes-index` composes
  the message itself (#165 6710770304). **Answered by lane 1's v2.4** (#165
  6711221999, folded above): `journal.act_id` and each entry's touched keys,
  derived inside `commit_write` and stored in `journal_scopes`, with F5's
  check as LR009 (D5; lane 4's row 20); and the SQL that refuses with LR004,
  at commit, a `cutover` or `rollback` entry that writes a row outside
  `lanes/STORE` (D24; lane 1's point 1). **Answered by lane 1's v2.5** (#165
  6711914507, folded above): lane 1's half of S3, and N1 and N2, from lane
  4's check of v2.4 (6711545001), as lane 3 asked (6711564365): `line_scopes`
  also gives an archived row its normalized `archive:` key, trimmed as text;
  step 8a's `objects:` key is lower-cased, through `entry_new_keys`; and a
  key a line derives that is not a scope is a new code, LR010, a 2, at a
  write and at an import, whose cost at import lane 3 accepted (option (a),
  #165 6711924759; `operations.md` §2.2, act 4's step 6). The
  stale-`LANDING` release waits on Brett (F13). **Answered by lane 1's
  v2.6** (#165 6714645316, folded above), the points the review of commit J
  routed to it: `find_write(p_write_id, p_not_after)`, which waits on the
  server's own clock and checks it again under the head row's lock, with
  LR011 (R2's S2); `commit_write` refusing a `rollback` entry with LR012
  while `mirror_state.diverged_sha` is set, read `FOR SHARE`, whose lock
  `record_mirror_state`'s `UPDATE` already conflicts with, and the rollback's
  fresh fetch and divergence check right before its step 1 (R2's S5); the
  after-loss cutover's label, refused where it is missing in either form and,
  where the switch is at `primary`, unless it differs from the switch's, as
  lane 3 ruled, lane 1's option (a), so a restore needs a replacement of
  another label (R2's N4); a renderer `m` above the head recorded as a
  divergence, and the renderer stopped (R2's N6); and the bounds of
  `cutover`, `switch_to_files`, `record_marker`, the lookup and the other
  calls of `import --single` and `render --revert`, measured where they are
  operator calls, with their commit call's row confirmed (R2's N1).
  **For commit Q, from verifier V2's review of commit O
  (`VERIFY-V2-report-20261108.md`, sha256 `1da276f9…`):** S-2, nothing
  supplies the after-loss cutover's label; S-3's half, the order of the
  generation and switch checks in `commit_write`, and LR006 before the
  generation check; NIT 1's half, `find_write`'s "found" checks no lane, verb
  or arguments; NIT 3, the rollback's retry after a 40001; NIT 4, N6 against
  (ii′)'s marker-first; and NIT 8, a rollback rerun whose first run died
  before declaring.
- **Lane 4, the phase plan: answered.** Its section, folded above, answers
  each item this list routed to it, in its "The routed items, answered" (34
  rows), and lane 3 accepted it (#165 6708889286). Each item, answered: the
  rule for a verb that reaches the commit seam twice (F21), one run and one
  journal entry; the internal flag for the scratch tree (F22),
  `LANES_REGISTER_SCRATCH`; the files the snapshot returns, `repos.tsv` and
  `log/README.md` included; output buffering and the arms with side effects
  (F14); every failure of the mode query and Q6(c)'s own code, 78 (F8); the
  acts and their expectations (F5), `act-begin` and six scopes; the callers
  that read exit 3 as "written" (F9); the in-doubt rule at the deadline (F3),
  `find_write` with `not_after`; the pending record's format and age (F10); the
  renderer's progress bound and how it stops at an operator seq (F15, F2);
  where a hand edit is found (F16); the marker check for readers (F17); [L4-1]
  to [L4-5], each passing with the `flock`; `commit-tree`'s determinism (F29);
  and the push URL the deploy key needs against origin's HTTPS remote, an
  explicit SSH URL. From lane 5's section, answered: the new cache file
  `$SE_CACHE_FILE.log.<lane>` (its question 10), with a `.logpath.<lane>`
  sidecar; the image cache's form, which any writer may fill, the service
  included (question 13, [L4-c]); and `code/lanes-index:1315`, whose connection
  string moves from the argument list into `psql`'s environment (question 11).
  From lane 1's v2, answered: the in-doubt lookup calls `find_write` (§1.3.1),
  and the printed rerun carries the generation (D8). From lane 2's
  measurements, answered: how answer 2's detached bind runs, and what the guard
  answers while it is in progress (SVC-8); and `lane-start`'s bind as one verb,
  `start-record`, one transaction carrying the union of F5's expectations for
  its three register writes, with the stamp outside it. From the follow-ups of
  11-02, answered: the mechanics of writing `Register-Seq: <n>` in each render
  commit's final trailer block (D15), which SVC-12's bounded lookup reads; and
  the proof of the renderer's `fcntl.flock` (D14). From lane 3's asks of
  6708646285, answered: the renderer's own lookup of `m`, by form C, paged and
  bounded in time; and that the detached child outlives the hook, shown on
  Claude Code 2.1.319. **Ruled after the fold** (#165 6710770304):
  `start-record`'s exits follow lane 4's corrected table (6709560550), which
  agrees with round 1's 2A as S3's table and `specs/sql-register/spec.md`'s
  requirements on `lane-start` carry it: only the expectation's 2 stops both
  paths, a new lane's 69, 75 or 3 stops its launch, and a resume's prints a
  note, writes the stamp and launches. And `start-record` runs in both modes:
  in files mode its three register commits and pushes become one, which the
  Goals, the Non-Goals, `specs/sql-register/spec.md`'s "Nothing changes where
  the marker is absent" and its requirement on `lane-start`'s bind now carry.
  Lane 4's other rows are ruled at the head of the phase plan; rows 18, 22 and
  40 are Brett's, with the spec PR. **Confirmed by lane 4's check of v2.4**
  (#165 6711545001, as lane 3 ruled in 6711564365), so none of lane 3's
  rulings on lane 1's v2.4 (6711233420) waits on lane 4: the form of
  `act-begin`'s output and of `LANES_ACT`, `<act-id>@<seq>:<generation>` on
  primary and `<act-id>@files` off it (D5); that on primary the client
  refuses with 2 a write from an act whose base is `files`; and that the
  client checks `LANES_EXPECT` before it calls. LR009's re-read is not
  needed: the commit call runs with `VERBOSITY=verbose` and
  `SHOW_CONTEXT=never`, so the message comes back in the one call (lane 3's
  choice of lane 4's S2; S1's step 3). The check's other points are folded in
  the phase plan's "The acts and their expectations" (M1 to M4, S1, lane 4's
  half of S3, S4 and N3) and in S1's step 3.2 (S5); S7 is lane 2's, below.
  **For phase 2, on lane 4's side** (its check of v2.5, #165 6711949237):
  `RETIRED_ROWS_AWK`'s `key()` (`code/lanes-edit.sh:3748`, `(p + 0)`) should
  trim the zeros as text, `sub(/^0+/, "", p); if (p == "") p = "0"`, so that
  the read and v2.5's keys agree on 16 digits or more. **From the review of
  commit J and lane 1's v2.6, answered by lane 4's texts for commit N** (#165
  6714767210, folded above): a run that finds its write committed runs the
  verb's after-COMMIT tail at most once, in one child stopped at the seam with
  the entry's change set, under the pending record's `tail` key ("The tail,
  once"; R2's S3; row 24); lane 4's half of R2's S1, the pending record's
  `in_doubt` key and its `call` lines, which carry each call's `not_after` and
  so answer lane 1's note that the record carried none; the detached bind's
  bound over the child's whole body, 430 s, with its sum (R2's S6); exit 3
  naming the write id where a pending record's pid is still live at the
  deadline (R2's N2); the child's 1 s turns at the seam and its 120 s cap
  (R2's N5); and lane 1's text 9, v2.6's `find_write(<id>, <n>)` through
  `journal --write-id <id> --not-after <n>` at its ten places, with the
  reconnect window starting when the client's own wait ends, which answers
  lane 1's other note.
  **For commit Q, from verifier V2's review of commit O:** S-1, a writer
  killed mid-call is never marked in doubt; S-3's half, the snapshot's order,
  and a plain rerun that meets another generation's record; S-5, "The tail,
  once" leaves no trace after a success, and its takeover races; NIT 1's half;
  NIT 2, the bind's 430 s sum leaves out two terms; NIT 5, the TERM trap where
  `timeout` is missing; and NIT 6, which tries are retried.
- **Lane 2, the test plan:** a test for each MUST as folded (a write in doubt at
  the deadline; a diverged rollback, and a failed seq-1 push; the ruleset, then
  a files-mode write after the rollback; a declared outage with the service and
  the database both down; answer 2 inside 5 s; two acts racing a binding, with
  the expectation refusing the second); `commit`'s 2 in test 8 (F19); a record
  reused by an unrelated identical command (F10); a guard reading a mirror older
  than `mirror-seq` (F11); the disaster path with an old record (F18); a failed
  mode query on `mode=index` and on primary (F8); output printed once in a burst
  (F14); which cases need TCP rather than the Unix socket; and no test that
  expects F33's backstop to fire. From lane 5's section: its questions 14
  and 16 are measured (`MEASURE.md`), and AT-F6 follows the detached form.
  From lane 1's v2 and lane 3's rulings: a real-engine test for each object
  marked new and unproven ("The schema"); two writers claiming one object
  under two spellings (D19); one repository spelled two ways (D18); and the
  abort of a restore-form cutover (D24). From the follow-ups of 11-02:
  AT-F7 and AT-F11 test the bounded `Register-Seq` lookup, including no
  trailer within 200, which refuses 2 (#165 6579627366); and AT-F8 takes 78.
  From the phase plan: AT-F6's seventeen cases for the detached bind and
  `start-record` ("Answer 2's detached bind"); what test 9 records per attempt
  ([L4-c]); tests 5 and 6 as the agreement form of [L4-1], [L4-2], [L4-4] and
  [L4-5]; the renderer's bounded lookup of `m`, with no trailer found; and a
  test for each name the phase plan adds, marked new and unproven at the head
  of the phase plan. From lane 3's rulings of 6710770304: a test per verb's
  after-COMMIT tail (lane 4's row 24; S1's "A write in doubt"), in the pass
  after the review; AT-11 to lane 4's corrected `start-record` table;
  and the agreement tests of `main_slots`' pairing by spellings against files
  mode's Rule 6 on the imported history (D18), which the plan already has.
  From lane 4's check of v2.4, as lane 3 ruled (#165 6711545001 and
  6711564365): S7, the agreement test of `line_scopes` against
  `build_records`' rules over the whole imported register, in the pass after
  the review. From the review of commit J (lane 3's rulings), for its pass
  after the review, the delta's new scenarios: the in-doubt lookup's wait
  past `not_after`; a rerun that finds its write and runs the tail; an
  in-doubt write whose next attempt is refused for another reason; a
  committed write found after the switch has moved; a refusal by the
  register's own rules (2, not 69); readers under the refusal of
  `mode=primary`; `register-row-local` on primary; a taking line that names
  no repository; a bind refused with another 2; a rollback's declaration
  with a ref behind its head; and test 4's sequence numbers from 2. From
  lane 1's v2.6 (#165 6714645316): an import's `not_after` is its 330 s
  timer plus 2 s, so a test harness that gives an import the default 30 s
  hits LR008; `renames-v2.6.md` (sha256 `19e02501…`), the signatures, codes,
  rules and bounds v2.6 adds or changes; and a test each for LR011, a lookup
  too early, and LR012, a rollback refused over a recorded divergence.
  From lane 4's texts for commit N (#165 6714767210), for its pass after the
  review: for "The tail, once", a call in doubt that committed, settled by the
  next snapshot, by LR006 and by the lookup, each running the tail once from
  the kept child; the same with the kept child killed, so that a replay runs
  the tail; a rerun after a 3, which replays; `tail=sent` found after a kill
  between `sent` and the child's exit; a replay that stops before `ready`, and
  one whose change set differs; a write with no pending record; and the bind's
  "found" row. And AT-F6's items (7) and (15) to (17), with a count of `N_r`,
  the read calls on the `--bind` resume path.
  From commit P (lane 4's check of commit N, #165 6715074114; commit O's
  leftovers; and verifier V1's review of commit O
  (`VERIFY-V1-report-20261108.md`, sha256 `dfefdab7…`)), for its pass after
  the review, a test for each new scenario: in the sql-register delta, a
  `request-handoff` that runs none of its tail, which exits 2; a
  `Register-Seq` above the head, recorded as a divergence at the commit that
  carries it, and the revert of that commit; and a stray on origin not yet
  recorded, which the rollback's fetch right before its transaction finds; and
  in the register-service delta, a guard that starts too late, which refuses 2
  as "too late to verify".
- **Lane 5, the service: answered.** From lane 1's v2: the libpq service
  form (`bootstrap --admin-service`, `operations.md` §2.4) against SVC-2's
  mapping, and the declared outage's mirror read within SVC-8's budget
  (`operations.md` §2.5), are lane 5's changes, recorded in SVC-2 and SVC-8
  (#165 6579523948), which lane 3 accepted. **From the review of commit J,
  answered:** the bound of `outage declare`'s database probe, D9's row, one
  read of the switch connected as a writer's snapshot is, as REGISTER-WRITER
  (R2's N1; #165 6714373660).
  **For commit Q, from verifier V2's review of commit O:** S-4, a rollback's
  declaration never expires, and the probe writes it outside a rollback, with
  verifier V1's NIT 5 (`VERIFY-V1-report-20261108.md`, sha256 `dfefdab7…`),
  SVC-12's "unexpired" for a rollback's declaration; and NIT 7, the clamp just
  below its line.
- **Lane 3, the deltas, for commit Q, with the lanes' texts:** verifier V1's
  SHOULD 1(c), N4's label rule, and SHOULD 2(a), `outage declare`'s refusals
  during a rollback, each as a delta requirement with a scenario once V2's S-2
  and S-4 are settled.
