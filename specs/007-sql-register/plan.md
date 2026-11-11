# Implementation Plan: The SQL register under `lanes-edit.sh`

**Branch**: `007-sql-register` | **Date**: 2026-11-09 | **Spec**: [spec.md](spec.md)

**Input**: Feature specification from `specs/007-sql-register/spec.md`, keyed
to commit Y (`a6497d00`) of the change `add-sql-register`, which the spec
branch merges whole at `066ad8f1`. Citations are spec.md's: `spec:<n>`
and `svc:<n>` are lines of the change's two spec files on this branch, at
`ad82c2f4`; `Y:<n>`, `ops:<n>` and `clar:<n>` are lines of `design.md`,
`operations.md` and `clarifications.md`, which are unchanged from Y.

> **Assembly phase A.** This plan, spec.md and research.md are keyed to Y,
> with lane 3's rulings memo (R01 to R61), lane 3's assembly notes and the
> rulings since. `tasks.md` is phase B, assembled from the lanes' blocks:
> lane 1's v4.2, out at 12:10Z (#165 6739714248), lane 5's v5 with its
> review's section D, lane 2's at 18:00Z, and lane 4's revision on
> 2026-11-09.
>
> **Marks:**
>
> - `[PLAN CHOICE: <name>]`: the design leaves it to this plan. "Choices the
>   design leaves to this plan" names each one, and research.md records how
>   lane 3 took it.
> - `[L1]` to `[L5]`: the lane that owns the item, by lane 3's ownership
>   ruling of 2026-11-08T16:23Z and its 007 call (#165 6724332510), which
>   gave U1 to U11 their lanes.

**Governance**: the OpenSpec change
[`add-sql-register`](../../openspec/changes/add-sql-register/proposal.md),
opensoft/openRepoTools#165, frozen at Y.

- Its [design.md](../../openspec/changes/add-sql-register/design.md) holds
  D1 to D25, S1 to S6, SVC-1 to SVC-12, lane 4's phase plan, and lane 2's
  test-plan summary.
- Its [operations.md](../../openspec/changes/add-sql-register/operations.md)
  holds the install contract (ops:133), the runbook and the operator tools.
- Brett Heap answered round 1 (RULED) and round 2 (as recommended) on
  2026-11-07, the items routed to him on 2026-11-08, and R35 and R41 at
  about 20:33Z: "4A, 5A, 1 let them stand, 3A" (#165 6726766577).
- An error found in the change's text since Y is an erratum in
  [research.md](research.md). A point that contradicts one of its SHALLs is
  a spec delta for Brett, never an edit here.

This plan implements the change and reopens nothing it settles. Lane 3's
rulings set its phases and ownership. Brett's hosting ruling of 16:17:36Z
puts the server outside it.

## Summary

The lane register becomes a PostgreSQL database on workstations configured
`mode=primary`, and the git files under `lanes/` become its rendered mirror.
Everything is built through one client, `lanes-index`, and `lanes-edit.sh`
never runs `psql`.

- **The database side** `[L1]`: in `lanes-index`.
  - The schema `lanes_register` with its migrations and roles.
  - `register snapshot`, `register commit`, `register read`, `journal` and
    `register classify`.
  - `import --at`, `cutover`'s transaction, `status` and `reconcile`'s
    check.
  - The operator tools: `bootstrap`, `ready`, `set-mode`, `login`, and
    `outage declare` and `outage clear` (U4).
- **The write path** `[L4]`: in `lanes-edit.sh`, on slices 1a and 1b.
  - Each writing verb runs unchanged on a scratch tree at head `n`. It stops
    at the commit seam, and its change set commits in one SERIALIZABLE
    transaction.
  - Each write ends in 0, 2, 3, 69 or 75. A write in doubt is settled by its
    write id.
  - `lane-start`'s bind becomes one verb, `start-record`.
  - The lifecycle scripts' read arms (U2), and `act-begin` with
    `canon-lane --walk` (U3).
- **The guard, the bind, the readers and the service** `[L5]`:
  - the guard reads once a prompt within its 5 s;
  - answer 2 binds detached, in every mode, which fixes #347;
  - every deciding read reads the text at the head, through one read door
    (U1), with the `lanes` script's reads;
  - the register service, `lanes-index serve` (U5), under lane 1's review,
    built beside groups A and B: Brett's "4A".
- **The renderer, rollback, the integration and the release** `[L3]`:
  - the renderer's pure function and the image cache (U8), and its git half
    in the private-index form;
  - the one push function, with `cutover`'s pushes (U6) and
    `import --single` (U7);
  - rollback;
  - `wip init`'s refusal (U9);
  - the constant's flip, last in group C;
  - the manual and the runbook (U10), and v1.3.0.
- **The tests** `[L2]`, from v3f:
  - the disposable PostgreSQL 16 fixture;
  - the real-engine, fake-`psql` and files-mode tests;
  - CI's real-engine switch.

## Technical Context

**Language/Version**: each part of the feature is written in one of these:

- bash, written for bash 3.2 and up: `lanes-edit.sh`, the lifecycle
  scripts, the skills' blocks and `openRepoTools`. It uses no `coproc` and no
  `EPOCHREALTIME` (design, "What constrains the approach").
- Python 3.9 or later, standard library only: `lanes-index`, its service
  and the tests.
- SQL for PostgreSQL 16: the schema and migrations, shipped inside
  `lanes-index` (§1.5).
- GitHub Actions YAML: `tests.yml`'s switch.
- Markdown: the manual and the notes, in the spec leg.

**Primary Dependencies**:

- **At run time**: no Python dependency (`dependencies = []`).
  - `psql` 15 or later is the only database client. Each register call sends
    one request ("Over a remote database", Y:5870).
  - git, as today; the renderer's push needs git 2.32 or later for
    `GIT_CONFIG_GLOBAL` (a fact still to find).
  - `setsid`, else python3's `os.setsid`, else `nohup` (the detached bind).
  - `timeout`, else the body's own process group.
- **The server**: PostgreSQL 16, a CNPG `Cluster` that only the codeXfactory
  prod install creates, on the prod AKS cluster in Central US, with one
  instance and no HA (Y:3327–3330). It is reached through a public
  `LoadBalancer` limited to engineers' addresses, with `verify-full` against
  the install's private CA, until the prod OpenXPKI replaces it (#165 6742844912, 6746095035), never through a
  pooler (Y:2324; ops:133). This feature builds none of the install's parts.
- **In CI**: the PostgreSQL 16 binaries on `ubuntu-24.04`, found by
  `PG_BIN`, then `pg_config`, then the Debian path.

**Storage**:

- PostgreSQL schema `lanes_register` (D1), beside #160's `lanes_index`.
- `lanes-index.conf`, which gains `mode`, `register_url`,
  `register_passfile`, `register_label`, `register_schema`, `render_key`,
  `register_addresses` and `register_rtt_ms`.
- Files under `lanes-index`'s state directory:
  - `register-pending/` and `register-done/`;
  - `register-writes/`, the attempt directories;
  - `register-images/`, the image cache.
- The declaration beside `lanes-index.conf` (D20).
- The session's offer file, which is the bind record.
- `lanes/STORE` on origin.

**Testing**:

- pytest through `tests/run.sh`, which locks itself. Never wrap it in a
  `flock`.
- **Every verify that runs the suite** takes lane 4's anchored two-run form
  (R28): lane 2's module as a file argument with a `-k` keyed to it, then a
  separate regression run, each with `TMPDIR=/tmp` and
  `OPENREPOTOOLS_REQUIRE_PG=1`, from a shell with no `LANES_*` or `TMUX`
  inherited.
- **Real-engine tests** run on `tests/pgcluster.py`'s cluster:
  - one cluster per session, on a Unix socket in a 0700 directory;
  - a template built by the bootstrap with `--no-passwords`;
  - one database per test.
- **Fake-`psql` and files-mode tests** run everywhere.
- **The switch**: `OPENREPOTOOLS_REQUIRE_PG=1` turns a missing engine into
  pytest's exit 4.
- **Phase-W tests** that write on primary run a copy of `lanes-index` with
  the constant flipped, in the test's temporary directory
  `[PLAN CHOICE: test 14's two states]`.

**Target Platform**:

- Linux and macOS, and WSL2 on Windows: every engineer's workstation, human
  or AI host, and each bench container as its own environment.
- CI: GitHub's `ubuntu-24.04`. No CI job runs the suite on macOS.

**Project Type**: a command-line toolset, the lane tools, with a database
back end reached by `psql`. It ships as the `openrepotools` wheel and the
one-line install.

**Performance Goals** (the design's figures; the measured ones are marked):

- **Q29**: the guard on primary at a warm p95 of 1 s or less, with the cold
  walk recorded and not judged (Brett, "1 s (Recommended)", #165
  6722025863; the warm p95, by lane 3's ruling, clar:514–516).
  - Measured: from Eagle, whose round trip to Central US is about 86 ms at
    best, 205 ms at the median and 456 ms at the p90, the guard without the
    service takes about 1.2 to 2.1 s, and about 0.5 to 0.7 s through it
    (lane 5, #165 6725361742; Y:334–337). So the service is on the cutover's
    path, and the release tag waits for it.
  - Lane 2's local figure is about 0.42 to 0.47 s warm (MEASURE.md, (14)).
- **The hook**: 5 s, with budget lines at `T0 + 3.75 s`, `4.25 s` and
  `4.4 s`.
- **Answer 2**: exits within 0.1 s after the guard's read.
- **The service**: under 50 ms for the guard's read, on the fixture.
- **One attempt**: about 0.55 to 0.61 s locally (lane 4's [L4-c]). It is
  about 1.03 s at 20 ms of RTT, and about 3.11 s at 150 ms (arithmetic).
- **Deadlines**:
  - D7's 30 s;
  - `T_write` = 62.5 + 3·`N_a`·`c` s, which is 68.5 s at `c` = 2;
  - the bind's `bound_s` is 500 s at `c` = 2 and 530 s at `c` = 3, with one
    address, and 590 s at Eagle's p90 (Y:5459).
- **The renderer**: at most 64 seqs a pass, six push attempts, and 16
  passes, then one handover `[PLAN CHOICE: the renderer's batch and cap]`.

**Constraints**:

- One parser, `lanes-index`, and one database client.
- No session is held open across shell code.
- No credential is in any environment that a session inherits. Secrets
  live in 0600 files that `lanes-index.conf` names.
- Every database path is bounded (D9).
- Each phase lands as a working, tested state, with `mode=primary` refused
  until group C's last pull request.
- Nothing changes where the marker is absent, except the three exceptions.
- The hook string and `GUARD_TIMEOUT` are byte-unchanged.
- The suite reaches no network and creates no real repository. The cluster
  is local, and only the timeout stand-ins bind 127.0.0.1.
- Line numbers in the design are at `2a057a6d`. The code leg's `main` is
  `e39395895`, so every task finds its sites by function name.
- No 007 code pull request lands before the spec PR (lane 3's rule,
  #165 6726556972).

**Scale/Scope**:

- **The register**: `LANES.md` is 6,410 lines, and the register is about
  2.6 MB, on 30,006 first-parent commits. It has 38 logs, 4,465 hold-shaped
  lines, 1,228 `PAUSED` lines, and 15 unreadable `WORD` lines.
- **The OpenSpec requirements**: 88 at Y, 69 in sql-register and 19 in
  register-service.
- **The verbs**: 15 write verbs, the 14 of the write guard and
  `start-record`; three new `lanes-edit.sh` verbs, `act-begin`,
  `start-record` and `guard-bind`, with lane 4's read verbs
  `register-text` and `register-grep`.
- **`lanes-index`**: the subcommands of the design's components table, from
  `mode` to `serve`, with `login` and `register classify`.
- **The people**: many engineers, human and AI hosts. Each has two logins
  and one service per environment (Y:2285).

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

`.specify/memory/constitution.md` states no principle, as 006 found. The
gates are the rules this repository and the change state. The second column
is filled once Phase 1's contracts have landed.

| # | Gate (source) | Before Phase 0 | After Phase 1 |
| --- | --- | --- | --- |
| G1 | New bash holds to bash 3.2 (register-service, "New code keeps to the toolchain floor") | PASS on the design's reading. The FIFO handshake's `exec 5<>`, `read -t 1 -u 5` and `SECONDS` are unprobed on bash 3.2 (the phase plan's "Not established") | to check |
| G2 | New Python is standard-library only, on 3.9; `dependencies = []` | PASS: `psql` is the only client, and the service is `lanes-index serve` | to check |
| G3 | One parser and one client, `lanes-index`; `lanes-edit.sh` never runs `psql` (Goals; D2, D3) | PASS: `c`, `N_a` and `w_s` come from `lanes-index`'s reports (R05) | to check |
| G4 | No credential in an inherited environment (Amendment 14(c)) | PASS: 0600 files; the URL-to-`PG*` mapping; the admin connection by `PGSERVICE` alone | to check |
| G5 | The suite reaches no network and creates no real repository (root `AGENTS.md`) | PASS, with the root `AGENTS.md` text naming the cluster and the loopback listener, by lane 1's root act R2 | to check |
| G6 | Tests skip loudly without their prerequisite, and CI refuses where it has the binaries | PASS: `OPENREPOTOOLS_REQUIRE_PG` (test plan §3.3) | to check |
| G7 | Nothing changes where the marker is absent, except three exceptions | PASS | to check |
| G8 | Governed files are `[O]` work (protocol, Build Tiers; "Governed invariants" below) | PASS | to check |
| G9 | Nothing under `upstream/openRepoShape` is edited, and the pin does not move in 007 (code#15 moved it to v1.1.1 before 007's branch was cut) | PASS | to check |
| G10 | Brett decides every act: the tag, the uploads, acts 3 and 4, the ruleset and the install. Pull requests land on his standing word; the spec PR on his own word | PASS: no act is a build task | to check |
| G11 | No host path in a tracked code file | PASS | to check |
| G12 | Each phase lands working and tested; `mode=primary` is refused until group C's last pull request (Goals) | PASS: the constant | to check |
| G13 | The hook string is unchanged (`openRepoTools`' conflict arm) | PASS: AT-F6 (14) | to check |
| G14 | Line caps move only by dated entries | PASS | to check |
| G15 | No 007 code PR lands before the spec PR (lane 3's rule, 6726556972) | PASS: the spec PR is this branch's first push | to check |

## Project Structure

### Documentation (this feature)

```text
specs/007-sql-register/
├── spec.md              # /speckit.specify, keyed to Y
├── plan.md              # this file
├── research.md          # Phase 0: every decision and erratum since Y, and PC-1 to PC-16
├── data-model.md        # Phase 1: the register's schemas, tables, sequences, roles
│                        #   and grants, from Y; the records are the contracts'
├── quickstart.md        # Phase 1: the operator's and an engineer's first steps
├── contracts/
│   ├── README.md        # the copy's source and digest, and each contract's owner
│   ├── register-cli.md  # [L1] T101; now lane 1's draft 4.19, copied in at round B15.
│   │                    #   THE CONTRACT BETWEEN GROUPS A AND B (FR-013 to FR-017),
│   │                    #   and the readers' four forms (§3.6, which lane 5 reviews)
│   ├── exit-codes.md    # [L1] T102: the lanes-index rows; each lane adds its callers' arms
│   ├── config.md        # [L1] T102: lanes-index.conf's keys, and the declaration's file
│   ├── pending-record.md  # [L4] T412: F10's record, the claims, the done mark (R34)
│   ├── bind-record.md   # [L5] T501: the offer file's modes and keys, the case table (R50)
│   ├── render-commit.md # [L3]: D15's message, Register-Seq, the push function
│   └── socket-api.md    # [L5] T511: SVC-3 and SVC-7, the service's requests and answers (R50)
├── checklists/
└── tasks.md             # /speckit.tasks, phase B, from the lanes' blocks
```

`contracts/register-cli.md` is fixed before group B codes against it. Lane 1
writes it, lane 4 confirms group B's sections, lane 5 reviews §3.6, and lane
3 rules (R01, R06). Lane 1's draft 4.19, keyed to Y (`43f16aa2…`, 1023 lines;
#165 6798746256), is draft 4.2 (`dea51e43…`, 6741494851) with O21 closed by
lane 4's (a) (6741093519), then lane 5's 6741787745, lane 4's log set, the
endpoint's name, lane 1's T511 answers and U5 fixes, B6's, B9's, B10's, B11's, B12b's and B13's, and lane 3's batches 7 and 9 (#165 6780364102, 6781033556). Draft 4.1 is
draft 4 (6738871108), with form 4, lane 5's review of §3.6, lane 3's B4 and
B7 and lane 5's check (6739068493). Lane 3 copied draft 4.19 into the path
byte for byte, and [contracts/README.md](contracts/README.md) names its
source and digest. Its §8 closes O14 to O21. T101 rides the spec PR (B4).

### Source Code (the code leg, `opensoft/openRepoTools-code` at `e39395895`)

Each file below names its owners by function, the group each part lands in
(A, B, C, or S for the service), and the tasks of the lanes' blocks that
carry it. Lane 2's T2xx numbers come with its block, due 2026-11-09 18:00Z.

```text
lanes-index                     # CHANGED. The one client. Governed.
│   A [L1]  T103 _env and _run, Question 11, group A's first lanes-index PR;
│           T104 load_config's keys and the TLS refusal at load (R09);
│           T105 RENDER_GIT_HALF and mode (78); T106 PostgresStore's register roles,
│           one request per call; T107 migration 1; T108 §1.4's roles and GRANTs;
│           T109 bootstrap, with --roles-only's dump route (a); T110 login, with
│           rotate --dump-job; T111 register snapshot; T112 register commit (R02);
│           T113 register read, its four forms; T114 journal; T115 import --at;
│           T116 cutover's transaction; T117 status, reconcile's check, sync;
│           T118 REGISTER_KIND_RE; T119 ready; T120 set-mode; T121 outage declare
│           and clear (U4); T122 register classify (R04)
│   A [L3]  T301 render --images; T302 the image cache (U8); T304 rollback's
│           transaction, on a scratch schema only
│   A [L5]  T507 register read --for guard's client half, under lane 1's review (R07)
│   C [L3]  T320 the push function; T321 render --checkout's lock; T322 the pass;
│           T323 F15; T324 render --revert and import --single (U7); T325
│           reconcile's re-render; T326 rollback's render and push; T327 cutover's
│           push (U6); T328 RENDER_GIT_HALF = True, last
│   S [L5]  T512 serve, the register service (U5), with its status lines in
│           cmd_status after T117 (R44), under lane 1's review
lanes-edit.sh                   # CHANGED. Governed whole (PC-16).
│   A [L5]  T502 the detached bind: guard_answer's choice 2, guard-bind, pid_start_of;
│           T504 reg_open and the read door's six classes (R46); T505 the read sites,
│           the cache family, reg_flush, the second read; T506 the readers' rest,
│           register-row-local (D11); T507 guard_run's phase-1 read; T508 the
│           SessionStart block's mirror read; T509 lanes' arm and --decide (R38)
│   A [L4]  T401 register-text and register-grep; T409 the writers' mode ask and
│           its "primary: exit 69" arm (R03, R17); T410 act-begin; T411
│           canon-lane --walk (U3)
│   B [L4]  T412 the write id and pending record; T413 LANES_REGISTER_SCRATCH;
│           T414 the seam on commit_publish (1b); T415 the attempt loop; T416 the
│           renderer fork and the nudge; T417 the in-doubt rule and exit 3's clause;
│           T418 the tail, once; T419 the writers' marker check; T420 D10's
│           refusals (R16); T421 claim and release; T422 rename-lane's tail (R18);
│           T423 request-handoff; T424 append-line's refusals (R04); T425
│           --landing-expired; T426 key()'s trim (R31); T427 start-record
│   B [L5]  T510 the bind's phase-2 half (L5-B1; R33)
│   S [L5]  T513 the SessionStart block starts the service
lane-start                      # A [L4] T402 reads through register-text; read arms
│                               # A [L5] T503 --bind's idempotent read rule
│                               # B [L4] T428 start-record, its act base, its re-base at
│                               #        the second binding read (R32)
lane-end                        # A [L4] T403 the awk through a verb | B [L4] T430 writes
lane-handoff                    # A [L4] T405 sread's 69 arm, LANES_READ_MIRROR=1
│                               # B [L4] T429 PAUSED writes, LANES_ACT unset
lane                            # A [L4] T408 arms pass the code, lanes --decide (R38)
│                               # B [L4] T432 request-handoff's bind:<L>
lane-rename                     # B [L4] T433 texts for 3, 75 and 69
lane-worktrees                  # A [L4] T404 three git grep reads through a verb
│                               # B [L4] T434 the text for 3
lanes                           # A [L5] T509 the footer's store line and --decide (U1; R27,
                                #        R43). Not lane 4's: struck from U2
skills/handoff/SKILL.md         # A [L4] T406 step 1's reads (R25) | B [L4] T431 step 4
                                #   (its commit block is slice 3's, #311, not 007's)
skills/restart/SKILL.md         # A [L4] T407 lread's 69 arm under S3
openRepoTools                   # A [L3] T303 wip_init's refusal under lanes/ (U9);
                                #   GUARD_COMMAND, GUARD_TIMEOUT and the hook text unchanged
                                # [L3] T346 --install places check 1's list beside lanes-index
.github/workflows/release.yml   # [L3] T346 publishes check 1's list as a release asset
.github/workflows/tests.yml     # 0 [L2] OPENREPOTOOLS_REQUIRE_PG; the four check names kept
tests/                          # [L2] pgcluster.py, conftest.py, test_sql_register_*.py,
                                #   test_lanes_index.py (FAKE_PSQL from PG* variables),
                                #   test_lane_helpers.sh, test_tests_workflow.py,
                                #   test_wip_init_command.py,
                                #   fixtures/ [PLAN CHOICE: test 6's v1.1.0 writer]
tests/test_repo_hygiene.py      # [L2]; T417 adds each _EXIT_3_CAUSES entry with its
                                #   die … 3, and lane 2 reviews that hunk (R22; Q-L2-3 (a))
pyproject.toml                  # UNCHANGED: no installable is added
```

**In the spec leg** `[L3]`:

- `docs/README-lanes.md`'s "The SQL register" (U10, T340 and T341). Lane 4's
  T417 lands the manual's exit-3 row beside its code PR, and T340 keeps it
  (R21);
- `docs/release-notes/v1.3.0.md` (T342);
- `specs/006-release-pypi/contracts/workflows.md`'s `release.yml` list (T346);
- this feature's directory, with each lane's contract file.

**In the root**, lane 1's acts on the standing word (U11; R13):

- R1, `composed.yml`'s `OPENREPOTOOLS_REQUIRE_PG`, before group A's first
  pull request that needs a database;
- R2, the root `AGENTS.md`'s account of the suite;
- R3 and R4, each group's rehearsal bump and the final bump (Q25(b)).

**Structure Decision**: the Triad.

- The code goes to the code leg's worktree,
  `worktrees/007-sql-register/code/`.
- The spec files and the manual go to the spec leg's worktree,
  `worktrees/007-sql-register/spec/`.
- Both worktrees are on the branch `007-sql-register`, cut from each leg's
  `main`: spec `aed18524b`, code `e39395895`. The spec branch merges Y at
  `066ad8f1`.
- The root gets a branch only for the pin bump.

### Ownership by lane

These are lane 3's rulings of 2026-11-08T16:23Z, verbatim in scope: "lane 1:
the SQL, and `lanes-index`'s database side; lane 4: the write path; lane 5:
the guard and the bind; lane 2: the tests, from v3f; lane 3: the
integration, the renderer, `rollback` and the release. Lane 3 tags each
task's tier."

The split is by file ownership, but three files have more than one owner.
Ownership inside them is by function, as the tree above marks it:

- `lanes-index`: lanes 1 and 3, and lane 5's client half and service;
- `lanes-edit.sh`: lanes 4 and 5;
- `lane-start`: lanes 4 and 5.

The items the first ruling named no lane for were ruled in lane 3's 007
call (#165 6724332510), "as `plan.md` proposes":

| # | Item | Owner |
| --- | --- | --- |
| U1 | The readers' SQL path in `lanes-edit.sh`: the read door, its classes and the hygiene test; the cache family; the read sites; F17; D11; with the `lanes` script's reads | lane 5 |
| U2 | The lifecycle scripts' and skills' reads, moved behind verbs, and their read arms (phase 1) | lane 4 |
| U3 | `act-begin` and `canon-lane --walk` | lane 4 |
| U4 | `outage declare` and `outage clear` | lane 1, with the guard's reading of them as lane 5's |
| U5 | The register service, `lanes-index serve` | lane 5, editing `lanes-index` under lane 1's review |
| U6 | `cutover`'s pushes, in both forms (group C) | lane 3; the transaction stays lane 1's |
| U7 | `import --single <sha>` | lane 3, reusing lane 1's loader |
| U8 | The image cache | lane 3 |
| U9 | `wip init`'s refusal in `openRepoTools` | lane 3 |
| U10 | The manual and the runbook in `docs/README-lanes.md` | lane 3, assembling each lane's texts |
| U11 | The root pull requests: `composed.yml`'s switch and `AGENTS.md`'s suite account | lane 1, with the root bumps |

Later rulings that move a line between lanes:

- **R03:** the writers' "primary: exit 69" arm is lane 4's T409, in
  `lanes-edit.sh`, not `lanes-index`'s.
- **R07:** the client half of `register read --for guard`, in `lanes-index`,
  is lane 5's T507, under lane 1's review, after T113.
- **R27:** `lanes` is lane 5's, in T509. It is struck from lane 4's L4-A1
  and from this plan's U2 tag.
- **R34:** `contracts/pending-record.md` is lane 4's, in T412.
- **R44:** the service's `status` lines are lane 5's T512, in `cmd_status`,
  under lane 1's review, after T117.
- **R50:** `contracts/bind-record.md` and `contracts/socket-api.md` are lane
  5's.

## The phases

```text
Phase 0  contracts/register-cli.md [L1, L4 confirms, L5 reviews §3.6]; the fixture and
         the CI switch [L2]; the root switch R1 [L1]
         Coded now. Each of its pull requests lands after the spec PR, R1 too:
         Y lists the change's PR first and the root composed.yml PR after it (ops:603–605)
   |
   +--> The spec PR, on Brett's own word, naming its list. No 007 code PR lands before it.
   |
   +--> Group A, phase 1, on code main ............. [L1] [L3] [L4: U2, U3] [L5: U1] [L2: tests N]
   |       Question 11 is its first lanes-index PR; the detached bind its first guard PR
   |
   +--> Group B, phase 2, beside A .................. [L4] [L5: the bind's half] [L2: tests W]
   |       codes now against register-cli.md and the fake psql; lands after
   |       slices 1a and 1b (code#16, code#17) and group A's subcommands
   |
   +--> Phase 4, the service (U5), beside groups A and B: Brett's "4A" ... [L5, L1 reviews]
   |       [L2: test 10 through it]; built and tested on the fixture's flipped copy,
   |       dormant on real hosts until T328 lifts Q6(c)'s refusal
   |
Checkpoint A+B [L3]: group B's tests pass against the real lanes-index (a copy
   |        with the constant flipped), with OPENREPOTOOLS_REQUIRE_PG=1
   v
Group C, phase 3 ............................... [L3] [L2: tests G]
   |       lanes-index and tests/ only; its last PR flips RENDER_GIT_HALF
   v
The tag, v1.3.0, on Brett's own word, after group C and the service ... [L3]:
   :       rc1 on TestPyPI, the tag, release/v1.3, PyPI, the notes, the workBenches
   :       re-vendor, which also carries the launcher's window reads ("5A")
   :
   :  outside 007: the install (a codexFactory lane) --> act 3 --> act 4,
   :  which also waits for slices 2a, 2b and 3 (Q28(i)), and for the service
```

- **Phase 0, before any group's code lands:**
  - `contracts/register-cli.md` is fixed;
  - lane 2's fixture, `tests/pgcluster.py`, and its plugin hook land with
    the first real-engine tests;
  - `tests.yml`'s switch lands with them;
  - the root's `composed.yml` switch lands before group A's first pull
    request that needs a database (operations §2.7, step 3).
- **The spec PR** lands before any 007 code PR (lane 3's rule,
  6726556972). A lane may build, open pull requests and run CI before then.
- **Group A** is everything that runs through none of `lanes-edit.sh`'s
  three write seams. Its pull requests start on code `main` as it stands, at
  `e39395895` today.
  - Question 11's `_env` change, T103, is its first `lanes-index` PR.
  - The detached bind, T502, is its first guard PR (#347).
  - The lifecycle scripts' reader edits are small separate PRs. Slices 2a
    and 2b also edit those scripts, so each is re-cited at its landing.
  - Lane 4's T401 to T411 may land ahead of slices 1a and 1b, once the spec
    PR has landed; T412 waits for 1a and 1b (R23).
- **Group B** runs beside group A (lane 3's ruling on point 6).
  - Lane 4 codes now, against `contracts/register-cli.md`, with the fake
    `psql` and a stub `lanes-index` that answers `primary`.
  - Its pull requests land once slices 1a and 1b are on code `main` (Q28:
    ready by 2026-11-12) and group A's subcommands have landed.
  - "Group B: lane 4's plan", below, summarises lane 4's order.
- **Phase 4, the service,** is built beside groups A and B, and the release
  tag waits for it. This is Brett's own word, "4A", typed in lane 3's
  session at about 2026-11-08T20:33Z (lane 3's #165 comment 6726766577,
  which quotes his typed words "4A, 5A, 1 let them stand, 3A"), for lane
  3's option "Build it beside groups A and B, and the tag waits for it". It
  changes his Q29 (a) answer, "the service is built last" (clar:473), and Y
  records it (clar:524–525; svc:293; Y:4826; Y:3951–3956).
  - Lane 5's T511 can start at once. T512 is coded beside group A and lands
    near its end, after lane 1's T103 to T106, T111, T113, T115 and T117,
    lane 3's T302, and lane 5's T504, T505 and T507. It does not wait for
    group B, group C or T328. T513 follows T508 and T512.
  - Until T328 lifts Q6(c)'s refusal, `serve` refuses on every real host
    (Y:3951–3956), so it is built and tested on lane 2's flipped copy.
  - Under the refusing build, `serve` exits 2 (R36).
- **Checkpoint A+B** is lane 3's integration. Group B's branch, on code
  `main` with group A landed, passes every phase-W test against the real
  `lanes-index` before group B's last pull request lands.
- **Group C** is `lanes-index` and `tests/` only (lane 4's row 10).
  - It needs neither slice 2a nor 2b, under the private-index form.
  - Its last pull request flips the constant, with test 14's flipped state
    and the test that the constant is True exactly when
    `render --checkout` exists.
- **The tag** waits for group C and the service. It needs Brett's own word,
  as do the rc1 and the upload.
- **Acts 3 and 4** follow the tag, outside 007. They wait on the install,
  which Brett has said comes first ("Wait for the install", #165
  6723925474). Act 4 also
  waits on slices 2a, 2b and 3.

### The interfaces between the groups

These are fixed by the phase plan's interfaces table (Y:4831–4834), and
written into `contracts/register-cli.md`.

- **From A to B:** group B changes nothing in `lanes-index` if group A ships
  these complete (Y:4841):
  - the four `register` and `journal` contracts, with `--tree`, `--at`,
    `not_after` and the expectations;
  - `register classify`, the one classifier for `append-line`'s refusals on
    primary (R04).

  The writers' mode arm, with its "primary: exit 69" placeholder, is not
  group A's: it is `lanes-edit.sh`'s, in lane 4's T409, which lands after
  lane 1's T105, and T415 replaces it (R03).
- **From A to the readers and the guard:** `register read`'s four forms
  (FR-015), the cache family, `.head` with `c`, `n_a` and `w_s`, and
  `.outage`. `lanes-edit.sh` reads no configuration (R05). Lane 5 writes
  the client half of the guard's form (R07).
- **L1-D1's scope** (lane 1's decision, 6738871108): the `=` form,
  `--lane=<key>` and `--log-key=<key>`, applies to `register read`'s
  `--lane` and `--log-key` only. Group B's `--lane <lane>`, on
  `register snapshot`, `register commit` and `journal --write-id`, keeps
  the interfaces table's form (Y:4834).
- **From B to C:** group C changes nothing in `lanes-edit.sh` if group B
  ships these:
  - the renderer fork, with its argv;
  - `<c>`;
  - the author identity;
  - the suppressed register nudge;
  - test 3's unit form.
- **The schema:** group A ships every column that groups B and C read, so
  group C needs no migration.

## Group B: lane 4's plan

Lane 4's group B plan is this section's source, summarised here and cited by
its sections, never copied: lane 4's `group-B-plan.md`, revision w11,
sha256 `801274de…`, 795 lines, with its block `lane4-block-T4xx.md`,
`a7c58603…`, 502 lines (T401 to T434), and its changes since `b95a1ff9`
(`ad9a3857…`) (#165 6742584818). Both are keyed to Y, lane 1's contract
draft 4.2 and slice 1b `34799b74`. Group B's sections, §3.1 to §3.5, were
unchanged from draft 3 to draft 4.2 but for two citation-only edits; lane 4
confirmed draft 4.5's for group B (6746695468), and draft 4.10 changes
§3.5's texts and fields (T424).

- **Scope** (GB §1): U2's read verbs and arms (T401 to T408), U3's
  `act-begin` and `--walk` (T410, T411), and group B (T409, T412 to T434),
  with `contracts/pending-record.md` (T412). It leaves the fakes and the
  tests to lane 2, `pid_start_of` and the bind's phase-2 half to lane 5, and
  D11's `register-row-local` to U1. It adds T401's read verbs, T409's
  writers' mode arm and T426's `key()` trim, which no skeleton item named.
- **The contract as group B uses it** (GB §2): the nine calls group B makes
  (GB §2.1); the attempt directory,
  `<state>/register-writes/<d>/<run>/<k>/`, and its files (GB §2.2); and the
  parent's decision on each answer of `register commit` and `journal`
  (GB §2.3), which follows the design's signals table and lane 4's CO-1 to
  CO-6.
- **The design, task by task** (GB §3):
  - the read verbs keep files mode byte-identical (GB §3.1);
  - the writers' mode ask runs once, before slice 1a's routing, and carries
    its answer across 1a's `exec` (GB §3.2; R17);
  - the flag's table, the seam's FIFO handshake, and the loop once per
    writer process (GB §3.5 to §3.7);
  - after COMMIT, the renderer fork; in doubt, F3 through `journal`, with
    exit 3's primary clause and its manual row; the tail, once (GB §3.8 to
    §3.10);
  - the writers' marker check, off primary only (GB §3.11);
  - D10 on primary: only `commit`'s carry form is refused, and the publish
    act stays, fenced to `handoffs/` (GB §3.12; R16);
  - `rename-lane`'s handoff move and stamp in the parent after `committed`,
    outside the flag (GB §3.13; R18);
  - `append-line`'s refusals through `register classify` (GB §3.14; R04);
  - `start-record` and the scripts' writes (GB §3.16).
- **The fakes and the tests** (GB §4): lane 2's stub `lanes-index`
  implements the contract's group B commands exactly; each task's verify is
  the anchored two-run form (R28); GB §4.3 lists the cases lane 2's items do
  not name yet (R29).
- **The order** (GB §6): phase 1 is about 8.5 writer-days and phase 2 about
  30. T412 comes first, once 1a and 1b have landed; then T413 → T414 → T415
  → T417 → T418 → T421 → T422 → T423 is about 14 writer-days after slice 1b
  is ready on 2026-11-12, and about 22 days with one review day per spine
  PR. T416 is not on the spine: it lands after T415 and T321 (PB4).
- **The code at `34799b74`** (GB §7): the tail's lock is the workstation's
  sidecar lock (F-B1; R19); routing on primary (F-B2; R17); the captures and
  `add-row`'s compensating commit are gone at 1b (F-B5).
- **Lane 2's points 36, 38, 40 and 42** (GB §11): a takeover whose snapshot
  failed waits on the record's clock; a first-snapshot 69 names no id; the
  lookup's tries run start to start; a claim with no record is a 3 with the
  effect check. Lane 3 took all four (R26), and Y carries 38's spec change
  (spec:318, spec:338).
- **Rulings lane 3 applies at phase B** (the assembly notes): R38's
  amendment (T502 accepts `lanes --decide` and ignores it until T509; T403's
  and T408's lines land after T502); T423 lands after T506; T409 adds R17's
  mode carry across 1a's `exec`; Q-L2-3 (a) for T417; and O21, closed by
  lane 4's (a) (6741093519) for T401's reader.

## Governed invariants

**The check.** The code leg's `AGENTS.md` at `e39395895`, the commit both
the code branch and its worktree sit at, has no `## Governed invariants`
section: `git show e39395895:AGENTS.md | grep -n -i governed` matches no
line, in a file of 40 lines (run on 2026-11-09). So the protocol allows this
feature no `[H]` task, and every task is `[S]` at least.

This plan adds its own entries, as the protocol allows a feature's plan.md.
Every task that touches one of them is `[O]`, as feature 006 ruled for its
own governed files.

- `lanes-index` — the register's schema, its migrations and
  `REGISTER_SCHEMA_VERSION` with its floors, the roles' GRANTs,
  `RENDER_GIT_HALF`, D9's bounds and the register subcommands' exit codes,
  and the credential handling that Amendment 14(c) requires: the
  URL-to-`PG*` mapping, which keeps every URL and password out of argument
  lists and inherited environments, and the 0600 credential files that
  `lanes-index.conf` names (R14): an edit moves the database contract that
  every workstation's tools and the one server share, or a secret's reach.
- `lanes-edit.sh` — the whole file (PC-16): the exit table, the write
  guard's verb list, the pending record's format, the grammar of
  `LANES_ACT` and `LANES_EXPECT`, the guard's budget lines and the bind
  record among it: an edit moves the contract with every caller, every
  rerun and the hook.
- `openRepoTools` — `GUARD_COMMAND`, `GUARD_TIMEOUT`, the hook text and the
  hook entries that `--install` merges: an edit moves the contract with
  workBenches' launcher and every profile, which this feature keeps
  byte-unchanged.
- `.github/workflows/tests.yml` — the real-engine switch and the four
  required check names: an edit moves what CI refuses and what the code
  leg's rulesets require.
- `tests/fixtures/` — the digest-pinned v1.1.0 `lanes-edit.sh` (PC-12): an
  edit moves a recorded digest.

Governed already, and unchanged by this feature, which adds no installable:

- `pyproject.toml`'s `force-include`;
- the count lines in `tests.yml`, `release.yml` and
  `tests/test_tests_workflow.py`.

## Dependencies

| # | Dependency | What 007 needs from it | State at Y | Needed by |
| --- | --- | --- | --- | --- |
| DP-1 | Slice 1a, code#16: every write through its lane's checkout | `<c>`, from its resolver; `LANES_REPO` alone routes | open; ready by 2026-11-12 (Q28) | group B's landing; the renderer fork |
| DP-2 | Slice 1b, code#17: S3 and S4, `commit_publish` | the commit seam and S3's step-4 site; the publish act's marker check | open; ready by 2026-11-12 | group B's landing |
| DP-3 | W2, code#8 to code#14: lane 2's seven fixes | it lands under runbook v9, and edits `tests.yml`, `release.yml` and their tests | open; in v1.2.0 on Brett's word | the CI switch's PR rebuilds on it |
| DP-4 | code#15: openRepoShape pin v1.1.1, lane 1's | nothing directly; it moved `main` | landed: code `main` `e39395895` | — |
| DP-5 | The install contract, lane 1's | the server's facts the tools assume | in Y: ops:133, items 1 to 9 | FR-009, FR-011, FR-053 to FR-055, the runbook |
| DP-6 | v3f, lane 2's test plan | test names and prefixes for every `verify:` | in J3, and so in Y | every task's `verify:` |
| DP-7 | Commits X, J3 and Y, and the freeze | this feature keyed to Y | done: frozen at Y | — |
| DP-8 | The change's spec PR | 007's branches pushed, and its code PRs allowed to land | 007 exists locally; the PR goes to Brett with its list | every 007 code PR |
| DP-9 | The lanes' blocks: lane 1's v4.2 (`cad0fa93…`, 660 lines, T101 to T122), lane 2's (18:00Z), lane 4's revision (2026-11-09); lane 5's v5 with its review's section D | tasks.md | lane 1's v4.2 is out (6739714248); the others are due today | phase B |
| DP-10 | The root's `composed.yml` switch, lane 1's R1 | `exact-pins` refuses a real-engine skip | to open | group A's first PR that needs a database |
| DP-11 | PostgreSQL 16 in the bench images (openBenches, lane 2's PR) and the image rebuild (Brett's) | real-engine tests run on the benches | open | bench runs only; CI does not wait |
| DP-12 | Slices 2a, 2b and 3 | act 4 waits for them (Q28(i)) | 2a is code#18; ready by 2026-11-17 | act 4, not 007 |
| DP-13 | The GitHub plan's facts: a push ruleset with a deploy-key bypass on the render set and `lanes/STORE`; git 2.32 or later everywhere; whether `ready` can prove the key's right | the push function's `render_key=` branch, and the ruleset | facts still to find; Q21(b)'s scope is on the spec PR's list | group C's push function, which works without `render_key=` |
| DP-14 | The install itself, by a codexFactory lane Brett names | act 3, the restore drill, and Q29's figure on each workstation | lane 3's kickoff for opsXfactory-6 awaits Brett's word | acts 3 and 4, not 007 |
| DP-15 | The v1.3.0 workBenches re-vendor | the launcher's bare window reads, spec:626 (R41) | lane 3 drafts it | the tag, not 007's code |

## Risks

- **R1. Shared files.** `lanes-index`, `lanes-edit.sh` and `lane-start` each
  have two or three owners.
  - Mitigation: ownership by function, as above, and small pull requests.
  - Each file's pull requests land one at a time under Rule 6's `HOLD`, the
    second merging the new `main` in place and running again.
  - `contracts/register-cli.md` is fixed before group B codes.
- **R2. Seam drift.** The design cites `2a057a6d`. Code `main` is
  `e39395895`, 654 lines added and 69 removed in `lanes-edit.sh`,
  `lanes-index` and `lane` since, and it moves again with W2, 1a and 1b.
  - Mitigation: every task finds sites by function name. The phase plan's
    sites are re-cited at each landing, and lane 4's (1b) marks are a
    re-cite rule at landing (R20).
- **R3. Contract drift between A and B.**
  - Mitigation: one contract file, with each draft's changes listed.
  - Lane 2's fake-`psql` fixtures and stub serve both sides.
  - Checkpoint A+B runs B's tests on real A before B lands.
- **R4. CI time.** A code PR's CI takes 58 to 81 minutes, and `exact-pins`
  64 to 87 minutes per group's rehearsal.
  - Mitigation: one composed rehearsal per group `[PLAN CHOICE: the
    rehearsals]`.
  - Lane 4's parallel CI jobs, on Brett's own word, outside 007.
- **R5. `tests.yml` contention.** W2's picks, the real-engine switch and lane
  4's parallel jobs all edit `tests.yml` and
  `tests/test_tests_workflow.py`.
  - Mitigation: one at a time, and the four check names kept.
- **R6. Errors in the frozen text.** The change is frozen at Y.
  - Mitigation: an erratum goes into research.md and leaves the change's
    text alone. A point that contradicts a SHALL is a spec delta: on lane
    3's ruling it changes the change's spec files on this branch, as
    `dabd74bd`, `ce3fad83`, `ad82c2f4` and `04a9de6b` do, and goes on the spec PR's list
    to Brett.
    Round B8's deltas, spec:649, :664, :670, :685 and :692, carry his
    approval (research.md's DEC-27 and ERR-34).
- **R7. The install's schedule.** Acts 3 and 4, and Q29's figure on each
  workstation, wait on it, and the cutover may slip (Brett's "Wait for the
  install", #165 6723925474).
  - 007 itself does not wait: its tests run on the fixture.
- **R8. Far workstations.** From Eagle the guard without the service misses
  Brett's 1 s, so the service is on act 4's path (Y:4826).
  - At Central US's round trip, a burst of ten may pass D7's 30 s and exit
    75. A per-workstation D7 is lane 4's proposal, after act 3's test 4.
  - Form 4, the guard's second read, always reads directly, at a cost of one
    `psql` start on that path (B7). T511's and T512's measurements decide
    whether it comes back to lane 3.
- **R9. Unprobed platforms.** macOS, bash 3.2's FIFO and `read -t`, Python
  3.9 for the real-engine tests, `pgrep -s` on macOS, and `flock` on drvfs,
  9p or NFS.
  - Mitigation: lane 2's tests where a runner exists; otherwise the gap is
    stated.
- **R10. "New and unproven".** Each name on the phase plan's list of that
  title counts as proven only once lane 2's tests hold it.
- **R11. A Python start per call.** An attempt makes two `lanes-index`
  starts, and each read one.
  - Test 9 measures them. The pipe pair is decided at phase 2 `[PLAN CHOICE:
    the pipe pair]`.
- **R12. The shared GitHub REST quota.** `gh_reads` is cached per write
  (F14). The renderer makes no API call.
- **R13. The service on the tag's path.** T512 waits on most of lane 1's
  `lanes-index` tasks, so it merges near group A's end, and the tag waits
  for it.
  - Mitigation: T511 starts at once; T512 is coded beside group A against
    the flipped copy.
- **R14. Spec-leg paths in task lines.** The protocol's `files:` is relative
  to the code tree. Lane 3 took PC-8: `../spec/<path>`.

## Choices the design leaves to this plan

Lane 3 took each as proposed, in its 007 call (#165 6724332510); any lane
could object in its block. research.md records each with its source at Y.

| # | Choice | Where the design leaves it | Taken |
| --- | --- | --- | --- |
| PC-1 | The image cache's form | "Deferrable within the design"; the phase plan's Question 13 | Question 13's form: content-addressed objects under `register-images/objects/`, per-seq manifests, and a `head` that moves only forward, under a short `fcntl.flock`; collection of objects in no recent manifest and older than 10 minutes; readers copy into their own files. The service writes through |
| PC-2 | The pipe pair: one `psql` kept for an attempt's two calls | lane 4's row 27; (A″) excludes a session held across the verb | not in phase 1. Decided at phase 2 from tests 9 and 4 (R24). A cached module or lazy imports come first |
| PC-3 | The renderer's batch of 64 and cap of 16 passes, and whether to accept the gap after six lost races | lane 4's row 3, "lane 3: the two values" | take 64 and 16, record test 5's figures, and accept the stated gap, which `reconcile` closes |
| PC-4 | Which way out the runbook prefers | lane 4's row 7: the import toggles a line's history, and the revert does not | ship both (the requirement needs a tool for each); the runbook prefers `render --revert` |
| PC-5 | How a test builds test 14's two states | test plan, open point 3 | a copy of `lanes-index` with the constant rewritten, in the test's temporary directory. No environment override |
| PC-6 | Test 3's agreement form, with the marker pushed by `cutover` | test plan, open point 20 | yes, in group C, with `cutover`'s push |
| PC-7 | The rehearsals | operations §2.7: one per group, against R3c's one | one composed `exact-pins` run per group |
| PC-8 | Spec-leg paths in task lines | the protocol names code-tree paths only | `../spec/<path>`, relative to the code worktree, since both legs' worktrees sit in `worktrees/007-sql-register/` |
| PC-9 | Task IDs while five lanes write blocks | — | ranges per lane, T1xx to T5xx, which `/speckit.tasks` renumbers in phase order |
| PC-10 | The exit codes of `import --at`'s refusal and of the cutover's abort | test plan, open point 1 | lane 1 names them in `contracts/exit-codes.md` (T102 proposes 2 for each) |
| PC-11 | The `psql` seam that tests inject faults at | test plan, open point 2 | keep the lookup by `PATH`, and say so in `contracts/register-cli.md` |
| PC-12 | Test 6's v1.1.0 `lanes-edit.sh` | test plan, open point 5 | a digest-pinned copy under `tests/fixtures/`, governed (above) |
| PC-13 | Whether `--leave <from>` joins `start-record` | lane 4's row 38 | no: each verb keeps its expectations to one lane |
| PC-14 | D9's values other than the guard's | "Deferrable within the design" | D9's table as written. Lane 2's tests confirm each bound |
| PC-15 | The CI cost of tests 9 and 10 and D9's long rows | test plan, open point 9 | every trigger until measured, then lane 3 decides |
| PC-16 | Governing the whole of `lanes-edit.sh` | this plan | yes, for 007, which makes its edits there `[O]` |

## Open points

The draft's O1 to O5 are closed: U1 to U11 and PC-1 to PC-16 by lane 3's
007 call (6724332510); O3, whether the tag waits for the service, by Brett's
"4A" (6726766577); O4 by the install contract at Y (ops:133); O5 by this
re-keying. O21, C2's (a) or (b) for T401's reader of a render-set path, is
closed by lane 4's (a) (6741093519), in contract draft 4.2 and later:
`register-text` and `register-grep` answer `lanes/LANES.md`,
`lanes/aliases.tsv` and each `lanes/log/<lane>.md`, and 2 for any other
path. These stay open, each with its owner:

- **Lane 2's:** where the new cases go: `tls_at_load`,
  `dump_restore_privileges` and `rotate_dump_job` (lane 1's T104, T109 and
  T110), and `guard_second_read_at`, `log_key_hex_slot` and
  `journal_last_pickaxe` (lane 1's T113 and T114); and, with v3f, the
  `[J3]` word for form 4's proof of "too late", which lane 5's T507 carries
  in its own verify, beside its form-2 test for "too late", since only T507
  has `T0` (lane 1's block v4.1; lane 5, #165 6739068493). T113's case
  does not prove it.
- **Brett's, on the spec PR's list:** Q21(b)'s ruleset scope, and whether
  the org's GitHub plan supports it; the design's differences from
  Amendments 22 and 23; lane 1's root acts R1 to R4 as 007's PRs under his
  standing word.
- **Lane 3's, before the change is archived:** R61's wording task on the
  change's `design.md` (Y:4975's Q29 clause; T1 and T5's line lengths).
- **A per-workstation D7:** after act 3's test 4, to 007's tasks or to
  v1.2.1.

## The test layout

These are lane 2's modules, as the lanes' blocks name them. Lane 2's block,
due 2026-11-09 18:00Z, fixes the names and the prefixes that each task's
`verify:` selects with `-k`.

| Module | Phase | Holds |
| --- | --- | --- |
| `tests/pgcluster.py`, `tests/conftest.py` | 0 | the fixture and the switch |
| `test_sql_register_fixture.py` | 0 | the switch's four cases |
| `test_sql_register_schema.py`, `_roles.py`, `_shape.py` | N | §1.2 to §1.5, the roles, and the SQL's shape (fake `psql`), with `tls_at_load` |
| `test_sql_register_render.py` | N | the pure renderer, and test 1's phase-N part |
| `test_sql_register_import.py` | N | test 7 |
| `test_sql_register_readers.py` | N | D17, the read door, the arms (C5), F17, `act-begin`, and the four read forms |
| `test_sql_register_guard.py` | N | test 10's direct part and the service's (AT-10), and AT-F6 |
| `test_sql_register_outage.py` | N | D20 and P3 |
| `test_sql_register_operator_tools.py` | N | P8, with `dump_restore_privileges` and `rotate_dump_job` |
| `test_sql_register_bounds.py` | N, W, G | D9 |
| `test_sql_register_build_flip.py` | N, W, G | test 14 |
| `test_sql_register_writes.py` | W | tests 1 and 2 per verb |
| `test_sql_register_order.py` | W | test 4, C10 and D19 |
| `test_sql_register_in_doubt.py` | W | test 12, D8, F3, F10 and the tails |
| `test_sql_register_marker.py` | W, G | test 3 |
| `test_sql_register_measure.py` | W, N | test 9 |
| `test_sql_register_renderer_git.py` | G | test 5, AT-F1, AT-F2 and the push function |
| `test_sql_register_divergence.py` | G | test 6 |
| `test_sql_register_cutover.py` | G | the cutover's push, and test 3's agreement form |
| `test_sql_register_rollback.py` | G | test 8 |
| `test_sql_register_lane_day.py` | G | test 11 |
| `test_sql_register_drill.py` | G | test 13 |
| `test_sql_register_disaster.py` | G | D22: the re-import, the dump's route and the point-in-time route |

The tests come first in each phase, and fail before that phase's
implementation. Every verify command takes the anchored two-run form, with
`TMPDIR=/tmp` and `OPENREPOTOOLS_REQUIRE_PG=1`, so that a real-engine test
cannot pass by skipping (R28).

## Delivery: pull requests by group

Each lands once its CI is green and lane 3's review passes, by merge commit,
on Brett's standing word, and none before the spec PR. A root bump lands by
squash.

0. **The spec PR,** on Brett's own word, naming its list.
1. **Group A,** as several small PRs: Question 11's `_env` first; the
   detached bind as the first guard PR; the schema and the bootstrap; the
   subcommands; the readers; the scripts' read arms, one script at a time;
   the renderer's pure function and the cache; the operator tools; and
   `wip init`.
2. **Group B,** in lane 4's order from its plan.
3. **The service,** beside groups A and B, landing when its waits have.
4. **Group C,** with the flip last.
5. **The spec leg:** the contracts, the manual and the notes, beside each
   group.
6. **The root:** a draft bump to the legs' PR heads for each group's
   rehearsal, then the final bump (lane 1).

## Complexity Tracking

No gate fails, so there is nothing to justify. The shared files of R1 are a
cost of the split by file ownership that lane 3 ruled, not a gate.
