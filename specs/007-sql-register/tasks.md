---

description: "Tasks for 007-sql-register: the SQL register under lanes-edit.sh"
---

# Tasks: The SQL register under `lanes-edit.sh`

**Input**: [spec.md](spec.md), [plan.md](plan.md), [research.md](research.md),
[data-model.md](data-model.md), [contracts/](contracts/README.md) and
[quickstart.md](quickstart.md), on the spec branch `007-sql-register` at
`04a9de6b`, keyed to commit Y (`a6497d00`) of `add-sql-register`.

**Status**: assembly phase B, started early (2026-11-09), with fix rounds B
and B2. Lanes 1, 3, 4 and 5's tasks are in: lane 1's from its block v4.5,
and lane 4's from its revision w11, keyed to Y (#165 6742584818). Lane 2's
block is PENDING (due 2026-11-09 18:00Z). Each slot is marked where it
sits.

**Sources.** Each task names its source line as `<tag>:<line>`:

| Tag | Source | sha256 | Record |
| --- | --- | --- | --- |
| L1 | lane 1's block v4.5, T101 to T122, keyed to Y; it keeps v4.2's line numbers | `4d740972…` | #165 6745214166 |
| L5 | lane 5's block v5, T501 to T513, keyed to W `bb08d080` (its `W:` lines stand, as below), with its §3.6 review's section D (keyed to Y) (`e26a91e6…`) and lane 5's #165 6739068493 applied, then lane 5's fold-ready edits (#165 6742056462, edit 22 as #165 6742096448 corrects it); `v5:<n>` is the same block (v5 = L5) | `5d427c42…` | lane 5's review, #165 6738431117 |
| L4 | lane 4's block, revision w11, T401 to T434, keyed to Y, with its group B plan (`801274de…`) and its changes since `b95a1ff9` (`ad9a3857…`) | `a7c58603…` | #165 6742584818; its independent check, RV11, says READY |
| SK | lane 3's task skeleton, keyed to W, re-keyed here | `43b01027…` | — |
| M | lane 3's rulings memo, R01 to R61 | `b2184b1a…` | #165 6726556972 |

Where a task's text changes its block's, a **Changed at assembly** bullet
says what changed and why. Everything else under a task line is its block's
detail, as the block wrote it, and is part of the task.

**Citations** in lane 3's text are spec.md's: `spec:<n>` and `svc:<n>` are
lines of the change's two spec files on this branch, at `ad82c2f4`; `Y:<n>`,
`ops:<n>` and `clar:<n>` are lines at Y. Inside a block's detail, the
block's own citations stand: lanes 1 and 4 cite Y, and lane 5 cites W
(`W:<n>`, W being `bb08d080`), as its block's Keys define it. Lane 4 cites
lane 1's contract draft 4.2 and block v4.2, whose line numbers draft 4.5
and block v4.5 keep.

**Marks**, inside the blocks' text:

- `[J3]`: lane 2's v3f fixes a test name or prefix, and the `-k` word beside
  it is a guess until lane 2's block names it.
- `[X: hosting]`, `[X: logins]`, `[X: backups]`: the block's record that
  commit X settled that text. They are settled; the mark stays as the
  block's history.
- `(PC-n)`: a choice the design leaves to the plan, as research.md's Part 3
  takes it.
- `(Un, lane n's)`: lane 3's 007 call (#165 6724332510), plan.md's U table.

**Tests**: the spec requires them, and lane 2 writes them (lane 3's ruling).
In each phase a test task comes before the implementation task it checks,
and its tests fail before that implementation begins. Each implementation
task's `verify:` names a module that lane 2's block creates.

**Organization**: by phase, then by owning lane. 007 is split by file
ownership, so a user story spans lanes. Each task names its story.

## Format: `[ID] [P?] [Story] [Tier] Description — files: … — verify: …`

- **[ID]**: kept from its block: T1xx lane 1, T2xx lane 2, T3xx lane 3, T4xx
  lane 4, T5xx lane 5 (PC-9).
- **[P]**: the task can run in parallel with the others marked so in its
  phase. They touch different files, and depend on no incomplete task.
- **[Story]**: US1 to US10, as in spec.md.
- **[Tier]**: the protocol's "Build Tiers": `[S]` runs on `sonnet-xhigh`,
  `[O]` on `opus-xhigh`.
  - There is no `[H]`. The code leg's `AGENTS.md` at `e39395895` has no
    `## Governed invariants` section (plan.md's check), so every task is
    `[S]` at least.
  - A task is `[O]` when it touches `lanes-index`, `lanes-edit.sh`,
    `openRepoTools`, `.github/workflows/tests.yml` or `tests/fixtures/`
    (plan.md, "Governed invariants"; PC-16), makes a design choice, a
    contract, a schema or a boundary, changes two modules, or is
    security-sensitive. Otherwise it is `[S]`.
- **`files:`** lists every path the task may create, change or delete,
  relative to the code worktree, `worktrees/007-sql-register/code/`. A
  spec-leg path is written `../spec/<path>` (PC-8). A diff outside the list
  fails the task.
- **`verify:`** runs from the code worktree and exits 0 only when the task is
  done. It takes R28's anchored two-run form: lane 2's module as a file
  argument with a `-k` that names it, then a regression run, each with
  `TMPDIR=/tmp`.
  - Never wrap a verify in `flock`: `tests/run.sh` locks itself. Run it from
    a shell with no `LANES_*` or `TMUX` inherited from a lane session. A
    driver that runs many verifies pauses between them.
  - `OPENREPOTOOLS_REQUIRE_PG=1` makes a missing engine pytest's exit 4, so
    a real-engine test can never pass by skipping.
- **Each task line** is followed by:
  - **Source:** its block line;
  - **Depends on:** the tasks it waits for, with `PENDING-L2: <item>` where
    lane 2's block will name the task; `SPEC-PR` on every task but one whose
    whole output is already in this spec branch, which rides the spec PR;
    and `SLICE-1A` or `SLICE-1B` for the gates below.

## Gates and order

- **The spec PR first.** No 007 code PR lands before the spec PR
  (6726556972). Every Phase 0 PR lands after it too, the root switch R1
  included: Y lists the change's PR first and the root `composed.yml` PR
  after it (ops:603–605). Coding may start now. Every task's **Depends on**
  names `SPEC-PR`, but T344, whose whole output is already in this spec
  branch, rides the spec PR (lane 3, fix round B, 2026-11-09).
- **Groups A and B and the service run beside each other.** The v1.2.0 tag
  waits for the service: Brett's "4A" (lane 3's #165 comment 6726766577,
  which quotes his typed words "4A, 5A, 1 let them stand, 3A"; FR-058).
- **Group B** codes now, against `contracts/register-cli.md`, the fake `psql`
  and a stub `lanes-index` that answers `primary`. It lands after slices 1a
  and 1b (code#16, code#17) and after group A's subcommands, T111 to T114.
  Lane 4's spine, T412 → T413 → T414 → T415 → T417 → T418, starts when
  slices 1a and 1b land; T416 runs beside it, after T415 and T321
  (L4:421–446).
- **Group C** starts on T415 and T344, and T328 lifts Q6(c)'s refusal
  last. Lane 4's T416, the renderer's fork, lands after T321 (L4:247); the
  skeleton's order at SK:827 is superseded (lane 3, fix round B, 2026-11-09).
- **A file's pull requests** land one at a time, under Rule 6's `HOLD`. The
  code branch takes `main` by merge commit, never a rebase or a force-push.

## Before Phase 1: what every task assumes

These are not tasks with a `files:` list.

- **The branches.** Both legs' `007-sql-register` worktrees were cut from
  each leg's `main`. The code leg's `main` is `e39395895`: `577110e4` plus
  code#15. W2, code#15, and slices 1a and 1b move `main` during 007.
- **Line numbers.** The design's are at `2a057a6d`. The code leg's `main` has
  654 lines added and 69 removed in `lanes-edit.sh`, `lanes-index` and
  `lane` since. Find each site by its function's name.
- **The contract.** `contracts/register-cli.md` is lane 1's draft 4.5
  (`da5a23ce…`, 963 lines, #165 6745394391), which keeps draft 4.2's line
  numbers; it is fixed before group B codes against it.

---

## Phase 0: Setup (shared infrastructure)

**Purpose**: the contracts that code waits for, the fixture, CI's switch and
the test helpers that every real-engine test needs.

- [ ] T101 [P] [US2] [O] Land `contracts/register-cli.md`, the contract between groups A and B and the readers' forms (FR-017; L1-A1), from the other lane 1 writer's drafts, due 2026-11-09 14:00Z with the readers' §3.6, once lane 4 has confirmed it, lane 5 has reviewed §3.6, and lane 3 has ruled — files: `../spec/specs/007-sql-register/contracts/register-cli.md` — verify: `bash -c 'f=../spec/specs/007-sql-register/contracts/register-cli.md; for h in "lanes-index mode" "register snapshot" "register commit" "journal" "register read" "psql" "write id" "exit codes"; do command grep -qiE "^#{2,4} .*$h" "$f" || { echo "missing: $h"; exit 1; }; done'`
  - **Source:** L1:74.
  - **Depends on:** SPEC-PR; lane 4's confirmation of group B's sections, lane 5's review of §3.6 (given, #165 6738431117) and lane 3's rulings (L1:503). Draft 4.5 (`da5a23ce…`) is in the spec branch's `contracts/register-cli.md`; this task lands the final text.
  - **Changed at assembly:** lane 3, fix round B2, 2026-11-09, item 8: T101 records `--store-line` and `--decide` (draft 4.3:182; lane 5, #165 6741787745).
  - **Its basis** is draft 1 (lane 3's R01). Later drafts answer lane 4's CO-1 to CO-6 and its two notes, and mark CO-2 and CO-6 as lane 1's contract decisions.
  - It fixes argv, stdout, stderr and exits for `lanes-index mode`, the `register` subcommands, `journal` and `act-begin`:
    - `--tree`, `--at`, `--files`, `--all`, `--prefetch --cache`, `--for guard`; and §3.6's `--since` (form 3), `--pid`, `--repo` and `--lane=` (form 2), `--at`, `--pid`, `--cache` and `--lane=` (form 4), and `--log-key=` (O18), whose `=` form is L1-D1's;
    - `not_after`, the act's expectations, and `LANES_ACT`/`LANES_EXPECT`'s grammar as the client checks it;
    - the attempt directory's format: paths, renames, removals, images, base digests, the message, the author, the expectations, and the `repos.tsv` blob with its alias sets.
  - **`lanes-index mode`'s answers,** on which lane 4's T409 builds the writers' arm and its "primary: exit 69" placeholder (lane 3's R03).
  - **LR004 under the scratch tree** (lane 3's R02): lane 1's form, in the `register commit` section.
  - **`register classify`,** in the contract's `register classify` section (lane 3's R04), which T122 builds.
  - **The readers' forms, §3.6, which lane 5 reviewed** (lane 3's R06; lane 5's review, #165 6738431117): the cache family, `.head` with `c`, `n_a` and `w_s_ms`, `--pid`, `.outage`, the two second reads, `--files`/`--all` and `--log-key=`. The second reads are form 3, `--since <n>`, at `n′`, for every primitive on primary but the guard, and form 4, `--for guard --at <n> --pid <pid> --cache <file> --lane=<key>`, the guard's, at `n` (O17; #165 6727201960 and 6727229519, with lane 5's B1 and B2). Contract draft 4 folds form 4 and lane 5's review in. §3.6 also names the split of `--for guard`: its flags and D9's guard row, in forms 2 and 4, are T113's, and its client half is lane 5's T507 (lane 3's R07). T113 starts only after lane 5 has reviewed §3.6.
  - **`c`, `N_a` and `w_s` come from `lanes-index`,** and `lanes-edit.sh` parses no configuration (lane 3's R05).
  - It leaves T302's image-cache API to T302, and fixes only what `register snapshot --tree` puts in the tree (contract §1). T111 and T113 call T302's API.
  - It keeps `psql`'s lookup by `PATH` as the seam tests inject faults at (PC-11).
  - **`lanes-edit.sh`'s own flags,** `--store-line` (R43, lane 5's T509) and `--decide` (R38, lane 5's T502), stay outside the contract; T101 records them as the contract's §1 names them (draft 4.3:182, kept in draft 4.4).
- [ ] T102 [P] [US1] [O] Write `contracts/exit-codes.md`'s `lanes-index` rows and `contracts/config.md`'s keys and refusals (L1-A1; PC-10) `[X: hosting]` for `config.md`'s URL example — files: `../spec/specs/007-sql-register/contracts/exit-codes.md`, `../spec/specs/007-sql-register/contracts/config.md` — verify: `bash -c 'e=../spec/specs/007-sql-register/contracts/exit-codes.md; c=../spec/specs/007-sql-register/contracts/config.md; for k in "import --at" "cutover" "register commit" "register read" "register classify" "outage declare" "78" "75" "69"; do command grep -qF -- "$k" "$e" || exit 1; done; for k in mode= register_url= register_passfile= register_label= register_schema= render_key= register_addresses= register_rtt_ms= reader_url= register-outage verify-full; do command grep -qF -- "$k" "$c" || exit 1; done'`
  - **Source:** L1:87.
  - **Depends on:** SPEC-PR (L1:504).
  - **`exit-codes.md`:** one row per `lanes-index` subcommand and code, from D9's table, Y ops §2.4's four rules and F8's table. It names PC-10's two codes. Lane 1 proposes:
    - **`import --at`'s refusal: 2.** A failed proof, a bad key (LR010), a source that is not origin, or a switch not at `files`. Nothing is swapped in. A database that cannot be reached or refuses is 69, as D9's import row says.
    - **The cutover's abort: 2,** once the abort has completed. A push it cannot confirm is 3 (S5).
    - Each code is a named constant in `lanes-index`, which the tests read (test-plan open point 1).
  - **`config.md`:**
    - `lanes-index.conf`'s keys, old and new;
    - the refusals, T104's TLS refusal at load among them;
    - SVC-2's fixed URL-to-`PG*` mapping (Y:3798–3819);
    - the `register-outage` declaration's fields (D20, Y:599; Y ops §2.5), which T121 writes and lane 5's T507 reads.
    - Its URL example is Y's (Y:2326): `register_url=postgresql://lanes_w_<engineer>@<endpoint>:5432/<database>?sslmode=verify-full&sslrootcert=<root file>`, and `reader_url=` the same with `lanes_r_<engineer>` `[X: hosting]`.
- [ ] T344 [P] [US6] [O] Land `contracts/render-commit.md`, lane 3's contract for D15's render commit, `Register-Seq` and the one push function (FR-044; FR-045), written from Y, before group C codes against it — files: `../spec/specs/007-sql-register/contracts/render-commit.md` — verify: `bash -c 'f=../spec/specs/007-sql-register/contracts/render-commit.md; for h in "The render commit" "Register-Seq" "The push function" "Left open by Y"; do command grep -qE "^## [0-9]+\. $h" "$f" || { echo "missing: $h"; exit 1; }; done'`
  - **Source:** plan.md:275 (`[L3]`); phase B's brief. Its text is in the spec branch since phase B, from Y's `design.md` and `operations.md` with `Y:` and `ops:` citations; its sections 1 to 4 add nothing Y does not say.
  - **Depends on:** rides the spec PR: its whole output, `contracts/render-commit.md`, is already in this spec branch (lane 3, fix round B, 2026-11-09).
  - **Needed by:** T320, T322, T326 and T327.
  - It covers: the render commit's author, committer, dates, build and message (D15); `Register-Seq`, its value, its place in the final trailer block, which commits carry it, and how the guard, the renderer, `status`, a declaration and the disaster path read it; and the push function's destination, environment, read-back, retries, refusals and exits.
  - Its last section lists what Y leaves open or unprobed, which stays open for the build. It also records lane 3's PC-3 values, and Y's two conflicts, F29's table row against D15 and Y:6031–6032 against Y:6473–6474, which D15 governs (lane 3, fix round B, 2026-11-09); T345 marks both in `design.md` before the archive.
  - It rides the spec PR, so its text is in place before T320. A change to it follows the same rule as `contracts/register-cli.md`: the contract changes first, then the code.

- **PENDING-L2: lane 2's items L2-0a to L2-0e** (the fixture
  `tests/pgcluster.py`, the switch in `tests.yml`, `FAKE_PSQL` from `PG*`,
  the stub `lanes-index` following contract §6, the name prefixes), in
  lane 2's block. See "PENDING: lane 2's block", below.
- **The root acts R1 to R4** are lane 1's, outside the code worktree, as
  acts on Brett's standing word (lane 1's block, L1:425–441; R13). R1 lands
  after the spec PR and after lane 2's L2-0b has landed in the code leg,
  and before group A's first database PR (L1:431, :522).

**Checkpoint 0**: with `OPENREPOTOOLS_REQUIRE_PG=1` and no engine, pytest
exits 4. With the binaries present, the fixture's cases pass.

---

## Phase 1: Group A, on code `main` (phase N of the test plan)

**Goal**: everything that runs through none of `lanes-edit.sh`'s three write
seams. `lanes-index` refuses `mode=primary` throughout.

**Independent Test**: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'sql_register or lanes_index or wip_init'`,
and test 1's files-mode twin, which shows that nothing changed.

### Tests for group A (write first)

- **PENDING-L2: L2-N1 to L2-N8**, in lane 2's block.

### Implementation for group A: lane 1

- [ ] T103 [US1] [O] Question 11, as group A's first `lanes-index` pull request (FR-010; L1-A2): every `psql` gets its connection from `PG*` variables, never from `-d <url>` — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_shape.py -k 'sql_register_shape and (pg_env or no_url_in_argv or passfile or connect_timeout)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:101.
  - **Depends on:** SPEC-PR; PENDING-L2: L2-0c (`FAKE_PSQL` from `PG*`), in the same PR or before (L1:505). It is group A's first `lanes-index` PR.
  - **Sites:** `PostgresStore._env` and `PostgresStore._run`. `_run` passes `-d self.url` today, and `_env` defaults `PGCONNECT_TIMEOUT` to 5.
  - **The mapping is SVC-2's (Y:3798–3819),** in one function that the service (U5) reuses:
    - host, port, database and user to `PGHOST`, `PGPORT`, `PGDATABASE` and `PGUSER`;
    - the query parameters `sslmode`, `sslrootcert`, `sslcert`, `sslkey`, `target_session_attrs` and `connect_timeout` to their `PG*` twins;
    - any other query parameter, or a password, is refused with 2.
    - A `connect_timeout` under 2 is refused; with none, it is 2 on register paths.
  - **Dropped from the inherited environment:** `PGPASSWORD`, `PGPASSFILE`, `PGSERVICEFILE` and `PGSYSCONFDIR`. `PGPASSFILE` is the configured file or `_no_password_file()`, as today.
    - `PGSERVICE` is set only on the bootstrap's and `login`'s admin path (T109, T110).
  - **On register paths,** `PGCONNECT_TIMEOUT` is set, not defaulted. Each path gets D9's client timer, as `subprocess.run`'s `timeout`, in place of today's fixed 300 s.
  - **#160's index paths** behave as before. Lane 2's `FAKE_PSQL` rebuilds the URL from the `PG*` variables and asserts that no argument carries the URL or `-d` (L2-0c).
  - **Owner:** lane 1 (lane 3's R12). Y:3804's "lane 4 changes `:1315`" is a `research.md` erratum, and lane 3 extended it to Y:3494, which says the same (6727022920).
- [ ] T104 [US1] [O] `load_config`'s new keys and refusals, with the TLS refusal at load (FR-001, FR-011; L1-A3) `[X: hosting]` — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_shape.py -k 'sql_register_shape and (register_config or tls_at_load)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:113.
  - **Depends on:** SPEC-PR; T103 (L1:491); PENDING-L2: the place for `tls_at_load` (L1:506).
  - **The keys:** the `known` tuple gains `mode`, `register_url`, `register_passfile`, `register_label`, `register_schema` (default `lanes_register`, checked by `SCHEMA_NAME_RE`), `render_key`, `register_addresses` (default 1) and `register_rtt_ms`.
  - **The password check** extends to `register_url`.
  - **The private files:** `register_passfile` and `render_key` go through `_check_private_file`.
  - **`mode=`** is `index` or `primary`. `mode=primary` with SQLite is refused with 2.
  - **The REGISTER-WRITER pair:** `mode=primary` requires `register_url=`, and `register_url=` without `reader_url=` is refused, by the rule `PostgresStore.__init__` applies today: a read never uses the writer's credential.
  - **The TLS refusal at load** (lane 3's R09 leaves it to lane 1; lane 1 takes it):
    - `load_config` refuses, as a refused configuration, a `register_url=` or `reader_url=` whose host is a TCP host that is not loopback, unless its query carries `sslmode=verify-full` and an `sslrootcert=` that names an existing regular file.
    - `sslrootcert=system` is refused at such a host: C7 says never `system` for a private root (Y:2324).
    - **Allowed without TLS:** a Unix socket (no host, or a host that decodes to an absolute path) and a loopback host (`localhost`, `127.0.0.0/8` or `::1`). So the fixture's socket cluster and its 127.0.0.1 timeout stand-ins keep working.
    - **Its scope:** `reader_url=` is checked only where `register_url=` is set. A file with no `register_url=` is #160's and loads as today (US1). Today's tests configure `reader_url=postgresql://lanes_reader@db.invalid/qa` with no TLS.
    - The refusal names the key and what is missing, and prints no URL.
    - **Its exit code** is a refused configuration's, as the contract's §2.5 and §2.7 fix it:
      - 2, as a refused configuration;
      - 3 on `journal --write-id` (CO-3);
      - 1 under `register read --for guard`. That is O14, which lane 5's review closed (#165 6738431117): the guard reads a 1 as files mode under form 2, the prefetch, and as a log that could not be read under form 4.
    - FR-011 is still enforced by `ready`'s check 11 (T119) and the server's `hostnossl` reject (lane 3's R09).
  - **The TLS case,** `tls_at_load` `[J3]`, which lane 1 asks lane 2 to place in phase N. It drives each item through `lanes-index mode`, with `mode=index` in the file. That command exits 2 on a refused configuration (T105), so the expected 2s hold, and a file that loads prints `index` and exits 0:
    1. `register_url=` at a TCP host that is not loopback, with no `sslmode`: 2;
    2. the same with `sslmode=verify-full` and `sslrootcert=system`: 2;
    3. the same with `sslrootcert=` a missing path: 2;
    4. the same with a root file: it loads;
    5. a socket URL, and a `127.0.0.1` URL, with no TLS: each loads;
    6. a #160 file with no `register_url=`, whose `reader_url=` names a TCP host with no TLS: it loads as today.
  - **`[X: hosting]`:** every real URL names `<endpoint>`, which is not loopback, so the refusal reaches every workstation's file.
- [ ] T105 [US1] [O] The constant and `lanes-index mode` (FR-003; L1-A3's constant, L1-A4) — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_build_flip.py -k 'sql_register_build_flip and (refusing or lanes_index_mode)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:138.
  - **Depends on:** SPEC-PR; T104 (L1:491); PENDING-L2: L2-N2, test 14's refusing state (L1:508); PENDING-L2: L2-0a and L2-0b, before any real-engine run (L1:507).
  - **The constant:** `RENDER_GIT_HALF = False`, beside `SCHEMA_VERSION`. T328, lane 3's, is the only task that sets it True.
  - **While it is False,** `mode=primary` is refused:
    - `mode` and `register read` exit 78, with the fixed text;
    - every other command exits 2 (test 14's refusing state, L2-N2).
  - **`lanes-index mode`** prints exactly one line, `index` or `primary`. A refused configuration exits 2.
  - **`USAGE`** gains every new subcommand.
  - **Not here:** the writers' "primary: exit 69" arm is lane 4's T409 in `lanes-edit.sh`, which lands after T105 (lane 3's R03).
- [ ] T106 [US2] [O] `PostgresStore`'s third role, and one request per register call (FR-012; L1-A5) — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_shape.py -k 'sql_register_shape and (one_request or register_role or psql_version or pgoptions)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:146.
  - **Depends on:** SPEC-PR; T105 (L1:491); PENDING-L2: L2-0a and L2-0b, before any real-engine run (L1:507).
  - **The roles:** `PostgresStore(cfg, role)` takes `register_writer` and `register_reader` beside today's `writer` and `reader`.
    - The register writer connects with `register_url=`/`register_passfile=`.
    - The register reader connects with `reader_url=`/`reader_passfile=`, never the writer's.
    - Its `search_path` is `register_schema`.
  - **One request per call:** each register subcommand sends its statements `\;`-joined as one request. The snapshot's marker goes as a `COPY`, so `_query_many`'s parse for #160 is unchanged.
  - **`psql` 15 or later** is checked once per process (`psql --version`). An older one is 69, naming the version.
  - **Bounds:** each request carries D9's bounds in the startup `options=` (`-c lock_timeout=… -c statement_timeout=…`) through `PGOPTIONS` (Y:1573). They need a direct connection, never through a pooler: C13 (Y:1575).
- [ ] T107 [US2] [O] Migration 1's schema and functions inside `lanes-index`, applied only by the bootstrap (FR-006 to FR-008; L1-A6) — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_schema.py -k 'sql_register_schema and (ddl or migration or commit_write or find_write or switch_to_files or sqlstate or version_window)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:154.
  - **Depends on:** SPEC-PR; T103 (L1:490, :492); PENDING-L2: L2-N4 and L2-N6 (L1:510); PENDING-L2: L2-0a and L2-0b, before any real-engine run (L1:507).
  - **The DDL:** §1.2's tables, views and indexes in `lanes_register` (Y:1033).
  - **The functions:** §1.3.1's `commit_write` in full (Y:1577), with `find_write`, `switch_to_files`, `record_marker`, `record_mirror_state`, `render_file`, `line_scopes`, `doc_line_scopes`, `entry_new_keys`, `scope_fault` and `scope_checked`.
    - `commit_write` checks the generation first, then the write id, then the switch, then the act (lane 1's order).
    - It updates the head row before the journal row (D6).
    - It checks the act's expectations (LR009), refuses an operator entry that writes outside `lanes/STORE` (LR004), and raises LR008 past `not_after`.
  - **SQLSTATEs** LR001 to LR012.
  - **The version:** `REGISTER_SCHEMA_VERSION`, with `writers_from` and `readers_from` (§1.5, Y:2330). Migrations are forward-only, and an older schema is migrated forward, never down.
  - **The one-number rule:** every copy of `REGISTER_SCHEMA_VERSION` in a release is one number. Lane 2's hygiene test (L2-N6) holds it.
  - **The script's digest** is the `script_sha256` the `migrations` row records.
- [ ] T108 [US7] [O] §1.4's roles and GRANTs, as the bootstrap's role block (FR-007, FR-009; L1-A6) — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_roles.py -k 'sql_register_roles and (grants or groups or owner_role or indexer)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:164.
  - **Depends on:** SPEC-PR; T107 (L1:492); PENDING-L2: L2-N4 and L2-N6 (L1:510); PENDING-L2: L2-0a and L2-0b, before any real-engine run (L1:507).
  - **The block** is Y:2235–2278 byte for byte. It is the same at X, and byte-identical to W's 2216–2259, which lane 1's engine ran twice as a CNPG-shaped owner, with identical catalogs (#165 6724409717).
    - The owner `lanes_register_owner`, NOLOGIN.
    - The groups `lanes_register_writer` and `lanes_register_reader`, NOLOGIN.
    - The indexer's login.
    - The admin's `GRANT … WITH INHERIT TRUE, SET TRUE`.
    - CONNECT and USAGE.
    - SELECT, and SELECT on the sequences, for `pg_dump`.
    - `REVOKE EXECUTE` from PUBLIC through the global default privileges.
    - EXECUTE on the five writer functions, and `render_file` for both groups.
    - `lanes_index`'s schema, with the reader's default privileges.
  - **No floors on a group:** the floors are each login's own (T110), since a role's settings reach no member.
  - **Its order:** the `CREATE ROLE`s and the admin's GRANT first, each guarded by a `pg_roles` check, then migration 1 (T107), then the rest. The rest and the GRANT can run again.
- [ ] T109 [US7] [O] `lanes-index bootstrap` (Y ops §2.4, `:407`; FR-053, FR-054; L1-A7) `[X: hosting]` `[X: backups]` — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_operator_tools.py -k 'sql_register_operator_tools and (bootstrap or roles_only or dump_restore_privileges)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:177.
  - **Depends on:** SPEC-PR; T108 (L1:492); PENDING-L2: L2-0a and L2-0b, before any real-engine run (L1:507).
  - **Changed at assembly:** lane 3, fix round B, 2026-11-09: the dialog answer was an accepted prompt suggestion in lane 1's session, as research.md's ERR-03 says, and the ruling is lane 3's (#165 6727022920).
  - **Flags:**
    - `--admin-service '<service>'`, reaching `psql` only as `PGSERVICE=<service>` in its environment, with `PGSERVICEFILE` and `PGSYSCONFDIR` dropped;
    - `--roles-only`: what a database dump does not carry, which a restore needs before it loads. That is the `CREATE ROLE`s and the admin's GRANT (T108's first part), and the three database-level statements (option (a), below);
    - `--label`;
    - `--repository` and `--lane`;
    - `--no-passwords`, for the fixture's template (L2-0a).
  - **What it does:**
    - T108's role block;
    - T107's migration, or `unchanged`;
    - the indexer-cannot-write check;
    - the switch at `files`, with the marker `absent`, its label, `set_seq` -1, a new generation, and the head at -1;
    - each step in one transaction.
  - **Output:** one line per object, `created` or `unchanged`; the `lanes-index.conf` lines with no secret, `register_label=` among them; and the indexer's password-file line, printed once, where it creates that login.
  - **Secrets:** it refuses when stdout is not a terminal. Each password is set as a SCRAM-SHA-256 verifier computed on the client, as psql's `\password` does. Lane 1's engine set verifiers that way, and the logins logged in by them over `verify-full`.
  - **Refusals:**
    - 69: the admin connection fails;
    - 2: an argument carries a connection string or a password;
    - 2: the admin may not create roles or schemas, or does not own the database (`pg_has_role(current_user, datdba, 'USAGE')`);
    - 2: the schema is newer than this build's version;
    - 2: a role holds a privilege outside its scope;
    - 2: `store=sqlite`.
  - **With the switch at `primary`,** as on a restored backup, it verifies, applies a pending migration and writes no label. It "changes nothing else" (Y ops:434).
  - **`[X: hosting]`:** the admin is the CNPG Cluster's `initdb` owner, with `LOGIN CREATEROLE CREATEDB` from its `managed.roles` entry (Y:2283). `initdb` made the database with the admin as its owner, so the ownership check passes; a Cluster whose `initdb` owner is not the admin's login is refused with 2.
  - **`[X: backups]`, the routes lane 2's T224 tests** (Y ops §2.3, `:372`):
    - **A point-in-time restore** carries the roles, their verifiers, memberships and floors, and the database's privileges. `--roles-only` prints `unchanged` for each, and no password line. T110's `rotate` follows.
    - **The dump's route,** in lane 3's ruled order (#165 6739605144), as lane 3's `research.md` ERR-03 refines its step 1. It corrects Y ops §2.3's step 6 and §2.4, which 007's `research.md` records as lane 3's errata:
      1. the register's database exists, owned by the login Brett's admin service names: on a new CNPG Cluster, the install's `bootstrap.initdb` made it with that login as its `owner` (Y ops:141–142, :190, :371–372; Y:2283); where none exists, as on AT-LRMPG's `stoppable_cluster`, the admin creates it, as his own act, through his own maintenance connection, and so owns it. Step 2 refuses with 2 a database the admin does not own (`pg_has_role(current_user, datdba, 'USAGE')`);
      2. `bootstrap --roles-only`;
      3. T110's `login add <engineer>` for each engineer, and `login add --dump-job`;
      4. `pg_restore` into that database;
      5. `bootstrap`, which prints `unchanged`.
      - Steps 2 to 5, the `pg_restore` among them, run through one admin service, which names the register's database. `lanes-index` never picks a database.
      - **Why this order:** under option (a), `--roles-only` runs the database-level REVOKE and GRANT CONNECT, which need the database to exist, and `ALTER DEFAULT PRIVILEGES` applies only to the database it is connected to.
    - **The re-import's route** is `bootstrap`, then T115.
  - **Option (a): `--roles-only` also runs the three database-level statements no database dump carries,** as it carries no role. Lane 3 ruled it (#165 6727022920). The option "--roles-only runs them (Recommended)", answered in a dialog in lane 1's session at 21:01:25Z, was an accepted prompt suggestion in that session, not Brett's own choice, and nothing here rests on it (research.md's ERR-03). The three:
    - Y:2243's `REVOKE ALL ON DATABASE … FROM PUBLIC`;
    - Y:2244's `GRANT CONNECT`, to the writer and reader groups and the indexer's login;
    - Y:2256–2257's global `ALTER DEFAULT PRIVILEGES FOR ROLE lanes_register_owner REVOKE EXECUTE ON FUNCTIONS FROM PUBLIC`.
    - **Why:** lane 1's dump of both schemas held no `ON DATABASE` statement and no global default privilege (`165-managed-pg/out/cnpg-dump.sql`, 0 of each). Without them, PUBLIC would keep `initdb`'s default CONNECT on the new Cluster's database. Set before the restore, the default privilege also covers the functions the restore makes, and the dump's own `REVOKE ALL ON FUNCTION` lines cover them in any case (lane 3's reason).
    - Each is idempotent, and prints `created` or `unchanged` as the roles do. The later `bootstrap` re-runs none of them, since the restored switch is at `primary`.
    - `research.md` records the erratum on Y ops:411's "only the roles". That text is lane 3's.
  - **The case,** `dump_restore_privileges` `[J3]`, which lane 1 asks lane 2 to place in phase N, with `tls_at_load` and `rotate_dump_job`:
    1. dump both schemas of a bootstrapped fixture database with rows in it;
    2. into a new database the admin created first, run `--roles-only`, then T110's `login add` and `add --dump-job`, then the restore, then `bootstrap`, all through one admin service naming that database (lane 3's order, #165 6739605144);
    3. PUBLIC holds no CONNECT on the new database, and a role outside the groups cannot connect;
    4. the writer and reader groups and the indexer hold CONNECT;
    5. the owner's global default privilege is there: a function the owner makes afterwards is not executable by a role outside the groups;
    6. `bootstrap` after the restore changes nothing: it re-runs no statement and writes no label.
- [ ] T110 [US7] [O] `lanes-index login add`, `add --dump-job`, `rotate`, `rotate --dump-job` and `revoke` (Y ops §2.4, `:477`; FR-009, FR-054; L1-A8) `[X: logins]` `[X: backups]` — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_roles.py -k 'sql_register_roles and (login or revoke or floors)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_operator_tools.py -k 'sql_register_operator_tools and (login or rotate_dump_job)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:226.
  - **Depends on:** SPEC-PR; T109 (L1:492); PENDING-L2: L2-0a and L2-0b, before any real-engine run (L1:507).
  - **The login step** is Y:2288–2300.
    - `lanes_w_<engineer>` joins REGISTER-WRITER and carries the writer's floors: `lock_timeout` 2000ms, `statement_timeout` 5s and `idle_in_transaction_session_timeout` 5s.
    - `lanes_r_<engineer>` joins READER and carries `default_transaction_read_only` on, `lock_timeout` 1000ms and `statement_timeout` 1500ms.
    - `--dump-job` makes `lanes_register_dump` alone, with the reader's floors. Brett stores its password as the Key Vault secret the dump CronJob reads.
    - A rerun puts back a lost membership.
  - **Revoke** is Y:2302–2309, for each of the engineer's logins in order:
    - `NOLOGIN`;
    - the membership revoked;
    - the self-grant `WITH INHERIT TRUE, SET FALSE`, then `pg_terminate_backend` for that login's sessions, then the self-grant revoked;
    - each step's result printed.
    - No `pg_signal_backend` is needed (lane 1's engine, as the CNPG-shaped owner).
  - **`rotate <engineer>`** sets new verifiers for both logins and prints the new lines.
  - **`rotate --dump-job`** (lane 3's ruling, 6726725691) sets a new password for `lanes_register_dump` alone, as `add --dump-job` makes it alone (Y ops:482–483, :710). It prints that login's one password-file line, once, to a terminal, and Brett stores it as the Key Vault secret. Y ops:372 relies on it after a point-in-time restore. `research.md` records it as an erratum on §2.4's `rotate`.
    - **Its case,** `rotate_dump_job` `[J3]`: after `add <engineer>` and `add --dump-job`, `rotate --dump-job` changes `lanes_register_dump`'s verifier and no other. The old password is refused at login, and the new one logs in and dumps both schemas. The engineer's two verifiers are unchanged. With stdout not a terminal it refuses with 2 and changes nothing.
    - `rotate` with neither an engineer nor `--dump-job`, or with both, is 64.
  - **The rules:** the bootstrap's four rules and its secret rules. Every password is a client-side SCRAM verifier, printed once to a terminal.
  - **Refusals:**
    - 2: an engineer name that is not `^[a-z][a-z0-9_]{0,40}$`;
    - 2: an argument carrying a connection string or password;
    - 69: the admin connection fails.
  - **`[X: logins]`:** the form is `lanes-index login`, as Brett chose (above, "The marks") and Y writes (Y:2285; Y ops §2.4).
  - **`[X: backups]`, the steps lane 2's T224 tests** (Y ops:372; Y ops §2.4, "Restores"):
    - after a point-in-time restore, `rotate` for each engineer and `rotate --dump-job` give every login a new password before the re-pointing;
    - on the dump's route, `login add` for each engineer and `add --dump-job` make them again after `--roles-only` and before the restore (T109; lane 3's 6739605144);
    - after a re-import, they make them again after `bootstrap` (T109).
- [ ] T111 [US2] [O] `lanes-index register snapshot` (FR-013; L1-A9) — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_shape.py -k 'sql_register_shape and snapshot' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_schema.py -k 'sql_register_schema and snapshot' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:252.
  - **Depends on:** SPEC-PR; T106 and T107 (L1:493); T302 for `--tree` (L1:512); PENDING-L2: L2-0a and L2-0b, before any real-engine run (L1:507).
  - **Lane 1's order of checks,** as §1.3 and T101 fix it, in one read-only snapshot.
  - **It returns** `n`, the switch, the database clock (D13) and the write id's entry.
  - **`c=` and `n_a=`** in its output come from `lanes-index`'s own configuration: `connect_timeout` and `register_addresses=`. `lanes-edit.sh` parses no configuration (lane 3's R05; Y:5877's "from the configuration" is a `research.md` erratum).
  - **The images:** those of `--files` or `--all` at `n`.
  - **`--tree <dir>`** builds the scratch tree from images through T302's cache API, never hardlinking a work-tree file (FR-020).
  - **`--at <k>`** works at seq `k`, with `repos.tsv` from `repos_blob`. Under `--at`, `--generation <g>` alone is a form, and so are all five identity flags, of which only `--generation` is checked; `--at` without `--generation` ends 64 (contract §3.2; O13).
  - **Bounds:** D9's snapshot row, as the register writer.
  - **Exits** are T101's.
- [ ] T112 [US2] [O] `lanes-index register commit` (FR-014; L1-A10) — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_shape.py -k 'sql_register_shape and (register_commit or lr004_retry)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_schema.py -k 'sql_register_schema and register_commit' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:261.
  - **Depends on:** SPEC-PR; T106 and T107 (L1:493); PENDING-L2: L2-0a and L2-0b, before any real-engine run (L1:507).
  - **Input:** the attempt directory, in T101's format, with `not_after`.
  - **Before `psql`:** the client checks `LANES_ACT` and `LANES_EXPECT` again.
  - **The call:** one `commit_write` call at SERIALIZABLE, as one request.
  - **Exits** follow §1.3's signals table (Y:1553), as T101 fixes them:
    - 0 with `committed <seq>`;
    - 75 for a lost race, with nothing written, so that lane 4's attempt loop takes a new attempt until D7;
    - 69;
    - 2 for a rule of the register (LR005, LR009, LR010);
    - 3 when `psql` exits 2, or the client timer ends, on the commit call: the write is in doubt.
  - **LR004 under the scratch tree** (lane 3's R02, lane 1's form): the retry happens only where the base, re-read from SQL, equals the tree's; otherwise 69, naming the image cache. The retry's second call is read by the `register commit` section's table in full, so 69 comes only when both calls ended definitively.
  - **Bounds:** D9's commit row.
- [ ] T113 [US4] [O] `lanes-index register read`, in contract §3.6's four forms (FR-015; L1-A11) — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and (register_read or prefetch or cache_family or since or guard_second_read_at or log_key_hex_slot)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_shape.py -k 'sql_register_shape and (register_read or since)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:273.
  - **Depends on:** SPEC-PR; T106 and T107 (L1:493); T302 for `--prefetch` and form 4's images at `n`; lane 5's review of §3.6, given (L1:514); PENDING-L2: L2-0a and L2-0b, before any real-engine run (L1:507).
  - **Its start:** after lane 5 has reviewed contract §3.6 (lane 3's R06). Lane 5 reviewed it on 2026-11-09 (#165 6738431117), and this task follows lane 1's decisions on that review, which contract draft 4 takes.
  - **The four forms,** as contract §3.6 fixes them. Form 4 is lane 1's argv for O17 (#165 6727229519), after lane 3's ruling (#165 6727201960), with lane 5's B1 and B2:
    1. the plain form, the readers' prefetch: `--prefetch --cache <file>`, with `--files <set>` or `--all`;
    2. `--for guard`, the guard's prefetch, with its `--pid`, `--repo` and `--lane=`;
    3. form 3, `--since <n>`, the second read for every other primitive on primary: it returns the paths changed in `(n, n′]`, and where any of them is cached, it refetches the family at `n′` (Y:4900). It does not run under `--for guard`;
    4. form 4, the guard's second read: `register read --for guard --at <n> --pid <pid> --cache <file> --lane=<key>`. `<n>` is `.head`'s `n`, `--pid` gives the client `T0` (B1), and `--cache` names the cache, as every other form does (B2). Form 4 has no `--repo`, and `--lane=` takes any key.
  - **The `=` form** (L1-D1, lane 1's decision): `register read`'s `--lane` (forms 2 and 4) and `--log-key` take their value only as `--lane=<key>` and `--log-key=<key>`, so that a key beginning with `-`, which is outside `check_lane_name`'s class, is never read as a flag. A caller must pass the `=` form, and the parser takes everything after the first `=` verbatim.
  - **What form 4 reads and writes** (lane 5's B3 to B7):
    - it resolves `<key>`'s log path case-insensitively in the images at seq `n` (Y:4160), never in the mirror (Y:4161);
    - it reads directly, never through the service (B7, ruled by lane 3, #165 6738474549);
    - it takes the generation from `.head`'s `generation=`, and ends 69 where `.head` is missing or the database's generation differs (B3);
    - it writes that one log's `.log.<key>` and `.logpath.<key>` for a key in `check_lane_name`'s class (Y:4956), and `.log.%<hex>` and `.logpath.%<hex>` for any other key (O18, below);
    - it refetches nothing else. It writes no `.head` (lane 5's B8, declined) and no `.outage`, reads no declaration, and never opens the mirror path (B4);
    - its bounds ship in T113: `--pid`, the cap at `T0` + 3.75 s and D9's guard row. T507's client half applies `T0`, the clamp and the cap, and T507's verify proves the cut (lane 5, #165 6739068493). Having no declaration, form 4 has no declaration cap and no floor (B5);
    - its cost, on that path only, is one more `lanes-index` start, plus `psql`'s start, connect and query: about 83 ms at a round trip of 0, and about 882 ms at 100 ms (Y:4365–4366; B6).
  - **It returns** the text at `n` (at `n′` in form 3) with per-file digests, and the `n` and generation it read, in one snapshot, as the register reader. Form 4 returns only the one log at the `n` it is given, and writes no digest, `n` or generation, since `.head` stays as form 2 wrote it. Its first line is `mode index` or `mode primary`. In `mode index` it reads and writes nothing.
  - **The cache family it writes** is §3.6's: `.register`, `.paths`, `.archive`, `.aliases`, `.log.<key>` with `.logpath.<key>`, and `.head`, written last. `.head`'s `c`, `n_a` and `w_s_ms`, which is `w_s` in whole milliseconds (O15), come from `lanes-index` alone (lane 3's R05). Form 4 writes only its one log's pair, and never `.head`.
  - **A key outside `check_lane_name`'s class** (O18, in place of draft 3's one shared slot): form 3 takes it as its own argument, `--log-key=<key>`, never inside `--files`. Its log is written to `.log.%<hex>` and `.logpath.%<hex>`, where `<hex>` is the key's bytes in lowercase hex. Such a file is rewritten on each read and never counted as cached. Forms 1 and 2 fetch no log for such a key, and refuse nothing for it.
  - **Two logs that differ only by case** (C11): where two render-set logs differ from `<key>` only by case, no form writes `.log.<key>`, and forms 3 and 4 exit 69, as `log_path_ci` returns 2 today.
  - **Images** come through T302's cache. An object is used only when its name equals the digest the database gave at `n` (D17).
  - **Exits** are §3.6's: 0, 69, 78 and 64, and 2 for a refused configuration. Under `--for guard`, a refused configuration is 1 (O14), since 2 there means only "too late".
    - **O14 is closed** by lane 5's review: the guard cannot tell a 1 from a crash's 1, and does not branch on it. Under form 2, the prefetch, a 1 means files mode with a warning (Y:4347). Under form 4, it means a log that could not be read.
    - **Form 3:** any end but 0 is the class's failed read, never files mode (C3). Y gives files mode to the prefetch only (Y:4347, Y:4886).
    - **Form 4's exits are not form 2's** (B4, ruled by lane 3, #165 6738474549): 0 with the one log's files written. Every other end, 2 for too late, 69, 78, 1, 64, a crash, or a 0 that wrote no `.log.<key>`, is a log that could not be read. The read site returns non-0, and the guard notes it as today and does not refuse. `--at <n>` above the database's head ends 69, as B3's generation mismatch does. For the record, under B4: an `--at <n>` below the generation's first seq ends 69, as one above the head does, and an empty `--lane=` key ends 64, as any malformed argument does. The prefetch, form 2, keeps its refusal for too late. T113 ships the end for too late; T507's verify proves the cut that gives it (lane 5, #165 6739068493).
    - **For the record, form 2:** an empty `--lane=` ends 64 there too, as in form 4. T507 never sends one: with no window name, it leaves `--lane=` out, and form 2 fetches no log (lane 5, #165 6739068493).
    - **Under `--for guard`, on a 69,** the first line on stderr is the cause, which the guard quotes (O15).
  - **`--for guard`** ships its flags and D9's guard row, in forms 2 and 4: it connects with `c` and stops at its timer. Its client half, which fills SVC-8's budget and SVC-12's conditions, is lane 5's T507, under lane 1's review (lane 3's R07; "Lane 1's reviews", below).
  - **The case** `guard_second_read_at` `[J3]`, for form 4, which lane 1 asks lane 2 to place. After form 2 has cached the family at `n`:
    1. with the head moved past `n` by an entry that changes the key's log, form 4 at `.head`'s `n` writes the log's text at `n`, not at the head, and exits 0;
    2. a key spelled in another case than the log's `<File>` resolves case-insensitively to that `lanes/log/<File>.md`, which `.logpath.<key>` names;
    3. it writes only `.log.<key>` and `.logpath.<key>`, or, for a key outside the class, only `.log.%<hex>` and `.logpath.%<hex>`, and no other member of the family;
    4. `.head` is byte-identical after the call, and no `.outage` is written;
    5. it never opens the mirror: with the mirror's copy of the log holding another text, and the mirror's checkout unreadable, it still writes the images' text at `n`;
    6. a `.head` whose generation differs from the database's ends 69, and so do a missing `.head`, an `--at <n>` above the database's head, and an `--at <n>` below the generation's first seq;
    7. two render-set logs that differ from the key only by case end 69, with no `.log.<key>` written (C11);
    8. a key beginning with `-`, passed as `--lane=<key>`, is read as the key, not as a flag (L1-D1);
    9. each of the ends 69, 78, 1 for a refused configuration, R09's included, and 64, which an empty `--lane=` gives, is a log that could not be read, and writes no `.log.<key>` (B4).
  - **Too late is proven in T507, not here** (lane 5, #165 6739068493): only T507 has `T0`, the clamp, the cap and the floor. Form 4's end for too late, a start at or past `T0` + 3.75 s, ends 2, writes no `.log.<key>`, and is noted, not refused. Lane 5's T507 proves it in its own verify, beside its form-2 test for too late, and lane 2 picks that `[J3]` word with v3f.
  - **The case** `log_key_hex_slot` `[J3]`, for O18, which lane 1 asks lane 2 to place:
    1. form 3 with `--log-key=<key>`, for keys outside the class, one of them holding `,` and one holding `/`, writes `.log.%<hex>` and `.logpath.%<hex>`, where `<hex>` is the key's bytes in lowercase hex;
    2. two such keys each get their own files;
    3. such a file is rewritten on each read and never counted as cached;
    4. a key beginning with `-`, passed as `--log-key=<key>`, is read as the key, not as a flag (L1-D1);
    5. form 2 with an out-of-class `--lane=`, and form 1 with `--all` over a render set holding a log whose name is out of class, fetch no log for that key and refuse nothing.
  - Its `-k` words, `since`, `guard_second_read_at` and `log_key_hex_slot` among them, are `[J3]`.
- [ ] T114 [US4] [O] `lanes-index journal` (FR-016; L1-A12) — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_shape.py -k 'sql_register_shape and journal' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_schema.py -k 'sql_register_schema and (journal or clock_age or try_spacing or journal_last_pickaxe)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:319.
  - **Depends on:** SPEC-PR; T106 and T107 (L1:493); PENDING-L2: L2-0a and L2-0b, before any real-engine run (L1:507).
  - **The lookup:** `--write-id`, `--not-after`, `--generation`, `--lane`, `--verb` and `--args-sha256`, with `--clock` and `--clock-age`, as contract §3.4 fixes them.
    - It goes through `find_write`, at READ COMMITTED, in a transaction it rolls back.
    - The client's own wait comes first, on its own estimate (F3; LR011).
    - It takes D9's lookup row.
  - **Lane 4's answers, taken by lane 1** (Q-L1-1, #165 6726296119; contract §3.4):
    - **From 36:** `journal --write-id` accepts `--clock` and `--clock-age` from a pending record as well as from the run's own last snapshot. That is the record's newest `call` line's clock, aged by the whole seconds since the record's `updated_utc` on this host. It takes them unchanged. The estimate only lags the database, so an early one meets LR011: 3, with the record kept.
    - **From 40:** the tries run start to start inside a closed 10 s window. Each starts at least 1 s after the previous start, and never before the previous try ends. A try may start at the window's 10 s mark. The window is kept on a monotonic clock finer than a second.
  - **It also serves** `--since <seq>`, and `--last --path <p> --grep <key> [--at <n> --generation <g>]` (for `swapped_lanes`' rank, lane 5's T506). `journal --since` serves the journal's history. It serves neither the readers' second read, which is `register read --since` (contract §3.6, form 3; T113), nor the guard's, which is form 4 (T113).
  - **`--last`'s match** (lane 5's C7): `seq=<k>` is the newest entry at or below `n` whose edits change the number of times `<key>` occurs, as a fixed string, in `<p>`. That is git's `-S` pickaxe, as today's rank reads it (`swapped_lanes`, `code/lanes-edit.sh:9087` at pin `2a057a6d`). `<key>` is never a regex. Where no entry matches, there is no `seq=` line, and the caller keeps 999999999.
  - **`--at <n>`** (C8), optional: with it, the rank's `n` is the door's, which keeps one snapshot per verb (Y:4890). Without it, `n` is the journal's own head. With it, `--generation <g>` is required, as for `register snapshot --at` (T111): `--at` without `--generation` ends 64, and an `n` above the head, or a generation that differs, ends 69 (contract §3.6; lane 5's NIT, #165 6741787745). T506 passes `.head`'s `n=` and `generation=`.
  - **Its case,** `journal_last_pickaxe` `[J3]`, which lane 1 asks lane 2 to place: a lane name holding `.` matches only itself; a later edit that keeps the line's count of `<key>` does not match; with no match there is no `seq=` line; `--at <n>` bounds the search to entries at or below `n`; `--at` without `--generation` ends 64; and an `n` above the head, or a generation that differs, ends 69.
- [ ] T115 [US7] [O] `lanes-index import --at <C>` (FR-049, FR-052, FR-053; L1-A13) `[X: backups]` — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_import.py -k 'sql_register_import and (import_at or after_loss)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:331.
  - **Depends on:** SPEC-PR; T109 (L1:494); T301 for the scratch render (L1:516); PENDING-L2: L2-0a and L2-0b, before any real-engine run (L1:507).
  - **The load** goes into a staging schema, from origin only.
  - **The proof** is a scratch render through T301, and R3's three-part proof, byte-identical.
  - **The swap** is one short transaction.
  - **No transaction is held across a git act** (F23). It runs with `LANES_LANE=<operator lane>`, through that lane's checkout (D24, Y:653).
  - **The 15 `WORD` lines** are kept verbatim and flagged.
  - **LR010's key check** records its time (lane 3's ruling, 6711924759).
  - **A scratch schema** serves the rehearsal.
  - **The refusal** is T102's code, 2. An unreachable database is 69.
  - **`[X: backups]`:** `--after-loss` takes `M`'s newest `Register-Seq` as its baseline (D22, Y:616; Y ops §2.3), after the re-import route's `bootstrap` (T109).
- [ ] T116 [US7] [O] `lanes-index cutover`'s transaction, against a scratch schema only while the constant is unset (FR-050, FR-052; L1-A14) `[X: backups]` — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_cutover.py -k 'sql_register_cutover and scratch' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:341.
  - **Depends on:** SPEC-PR; T109 (L1:494); PENDING-L2: L2-0a and L2-0b, before any real-engine run (L1:507).
  - **The transaction:**
    - the switch to `primary`;
    - the marker `adding`;
    - the operator entry at the next seq.
  - **The label** comes from `register_label=` alone, with its two refusals (N4, S-2).
  - **The first form's abort** goes through `switch_to_files`, with T102's code, 2.
  - **Outside a scratch schema,** while the constant is unset, it exits 2.
  - **`[X: backups]`, the after-loss forms** (D24; Y ops §2.3):
    - after a re-import, `--import <M> --after-loss` at `m+1`, with a new generation and the replacement's label. Its abort keeps the label and generation it set;
    - after a restore, `--after-loss` with no import, at `b+1`, after rendering every seq the mirror lacks up to `b`. Its abort removes the entry at `b+1`, puts the head back to `b`, and restores the switch's fields with the marker `present`. It never sets `files`.
  - **T327, lane 3's,** adds the push.
- [ ] T117 [US6] [O] `status`'s new lines, `reconcile`'s read-only check, and `sync`'s register source on primary (FR-048; L1-A15) — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and (status or reconcile or sync)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_outage.py -k 'sql_register_outage and status' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:353.
  - **Depends on:** SPEC-PR; T109 (L1:494); PENDING-L2: L2-0a and L2-0b, before any real-engine run (L1:507).
  - **`cmd_status` adds:**
    - the switch;
    - `n`;
    - the generation;
    - the mirror's last `Register-Seq` and the lag;
    - `diverged at`;
    - the pending records;
    - a declaration left in place (Y ops §2.5).
  - **The service's lines are not here.** Lane 5's T512 writes them in `cmd_status` after T117, under lane 1's review (lane 3's R44). T117 does not wait for the service.
  - **`cmd_reconcile`** checks the mirror against SQL, read-only, in group A. T325, lane 3's, adds the re-render.
  - **`cmd_sync`** with a `register:<owner/repo>` source does nothing on primary, and says so.
- [ ] T118 [US3] [O] `REGISTER_KIND_RE` grows to Q17(B)'s kinds and `LANDING EXPIRED`, for the commit's one classifier (FR-030; S6, D18; L1-A16) — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_schema.py -k 'sql_register_schema and (register_kind or landing_expired)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:365.
  - **Depends on:** SPEC-PR; T103 (L1:495); PENDING-L2: L2-0a and L2-0b, before any real-engine run (L1:507).
  - Today's regex is `^(HOLD RELEASED|HOLD|LANDING|LANDED)[ \t]+(—|-)[ \t]`.
  - `parse_register_line` and the SQL classifier agree on every kind (S7's agreement test, L2-W4).
- [ ] T119 [US7] [O] `lanes-index ready`, checks 1 to 13 (Y ops §2.4, `:437`; FR-054, FR-055; L1-A17) `[X: hosting]` on checks 11 and 13 — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_operator_tools.py -k 'sql_register_operator_tools and (ready or tls_root)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:368.
  - **Depends on:** SPEC-PR; T109 (L1:494); PENDING-L2: L2-N5 (L1:520); PENDING-L2: L2-0a and L2-0b, before any real-engine run (L1:507).
  - **Flags:** `--expect index|primary`, `--release` and `--env`.
  - **The checks,** each a read that writes nothing:
    1. the digests;
    2. `psql` 16;
    3. the configuration;
    4. each role's connect within `c`, refused outside its scope with 42501 under `BEGIN READ WRITE`;
    5. Q6(c)'s refusal gone;
    6. the workstation's name;
    7. no declaration;
    8. the switch and `lanes/STORE` with `--expect primary`;
    9. the version window;
    10. the label;
    11. TLS and the primary;
    12. each login's floors from its own `pg_roles` row;
    13. the endpoint's address count against `register_addresses`, by libpq's own lookup.
  - **Output:** one line per check. **Exits:** 0, 2 or 64. It changes nothing.
  - **`[X: hosting]`:**
    - **check 11** reads `sslmode=verify-full` and an `sslrootcert=` root file, the prod OpenXPKI's root, the trust anchor under openxFactory's `trust-anchor` contract (Brett, 2026-11-09). It fails on `system` (C7, Y:2324), as T104 refuses it at load for a host that is not loopback. Each role's own `pg_stat_ssl` row must show `ssl`, and `pg_is_in_recovery()` false. A chain that does not verify against the root file fails the check, naming the role and libpq's error: in lane 1's run, a leaf sent without its intermediate failed.
    - **check 13** expects the LoadBalancer's one address, `N_a` 1.
  - **Lane 2's tests:** T224's check-11 test, with the fixture's CA standing for the prod OpenXPKI's root and a full chain; and T230, the case for "never `system`" and a `tls.crt` with the leaf and every intermediate, which lane 3 kept (6726725691).
- [ ] T120 [US7] [O] `lanes-index set-mode` and its refusals (Y ops §2.4, `:462`; FR-054; L1-A17) — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_operator_tools.py -k 'sql_register_operator_tools and set_mode' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:389.
  - **Depends on:** SPEC-PR; T109 (L1:494); PENDING-L2: L2-N5 (L1:520); PENDING-L2: L2-0a and L2-0b, before any real-engine run (L1:507).
  - **What it does:** rewrites only the `mode=` line by temp file and rename, keeping 0600.
  - **Setting `primary`,** it refuses with 2:
    - unless the switch is `primary` with the marker `present` and `origin/main` names this database;
    - while Q6(c)'s refusal stands;
    - on a read-only mount;
    - on `store=sqlite`.
  - **An unreachable database** is 69.
  - **Setting `index`** refuses only on usage, and warns where the switch still reads `primary`.
- [ ] T121 [US7] [O] `lanes-index outage declare` and `outage clear` (D20, Y:599; Y ops §2.5; FR-056; L1-A18), U4, ruled lane 1's — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_outage.py -k 'sql_register_outage and (declare or clear or rollback_form)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:398.
  - **Depends on:** SPEC-PR; T106 (L1:496); PENDING-L2: L2-N5 (L1:520); PENDING-L2: L2-0a and L2-0b, before any real-engine run (L1:507).
  - **The declaration** is `register-outage`, beside `lanes-index.conf`. Its fields:
    - the database label;
    - Brett's words verbatim (`--why`);
    - the UTC and the host;
    - the expiry (`--until`), absent in the rollback form;
    - `mirror-seq`.
  - **`mirror-seq`** is read after a fetch, as `status` reads `m`, or given by `--mirror-seq <m>` where the host cannot fetch.
  - **The `--rollback` form** requires `--mirror-seq <k>` and carries no expiry. It is used by T304, lane 3's.
  - **Refusals, each 2,** in Y ops §2.5's order:
    - the database answers from this host, except the rollback form while the switch reads `files`;
    - no `--until` on Brett's word;
    - `--rollback` without `--mirror-seq`;
    - with it, where the probe reads the switch at `files`, unless the entry at `set_seq` is a `rollback` and `<k>` is `set_seq - 1`;
    - with it, where the probe cannot read the switch;
    - Brett's word while the probe reads `files` after a rollback. That refusal prints the `--rollback --mirror-seq <k>` line to run instead.
  - **Also refused with 2:** a `--why` holding a line break, so that `.outage`'s `label=` stays on one line (lane 5's O15, #165 6738431117). Y ops §2.5 does not list it.
  - **The probe** is D9's `outage declare` row, as the REGISTER-WRITER.
  - **`outage clear`** removes the file.
  - **The guard's reading of the file** is lane 5's T507 (SVC-12), which lands after T121. The file's format is fixed in T102's `config.md`, which both sides read.
- [ ] T122 [US3] [O] `lanes-index register classify`, the one classifier for `append-line`'s refusals on primary, as the contract's `register classify` section fixes it, with a parity test against `parse_register_line` (FR-030, serving FR-032; lane 3's R04) — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_shape.py -k 'sql_register_shape and (register_classify or classify_parity)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** L1:418.
  - **Depends on:** SPEC-PR; T118 (L1:495, :521).
  - **Why:** on primary, `append-line` refuses three texts with 2, before the lock: a hand-typed `LANDING EXPIRED`, a take with no readable UTC, and a taking line that names no repository. Y names "the one classifier" for them (Y:5116), and a bash copy in `lanes-edit.sh` would be the second classifier Y:6247 warns of.
  - **What it does:** it reads register lines and prints, for each, the fields `parse_register_line` gives, in the contract's format. It makes no database call. Its argv, output and exits are the contract's.
  - **The parity test,** `classify_parity` `[J3]`: over today's kinds, Q17(B)'s kinds, `LANDING EXPIRED` and malformed lines, `register classify`'s output equals `parse_register_line`'s fields, line for line.
  - **Lands after** T118, whose kinds it prints. **Gives to** lane 4's T424, which lands after T122.
  - It is a subcommand beyond the design's interfaces table, so `research.md` records it as a decision (lane 3's R04).

### Implementation for group A: lane 5

- [ ] T501 [P] [US5] [O] Write `contracts/bind-record.md`: the offer file as the bind's record, its four modes, every key with the phase that writes it, the guard's eleven cases in short, and `pid_start_of`'s signature (FR-038; W:5372–5383, W:5442–5456) (R50: plan.md lists it, and the skeleton's lane 5 block gains it) (lands after the spec PR, and before T502) — files: `../spec/specs/007-sql-register/contracts/bind-record.md` — verify: `bash -c 'f=../spec/specs/007-sql-register/contracts/bind-record.md; for w in mode=binding mode=bound mode=bind-failed mode=name-drift bound_s bounded deadline_epoch pidstart write_lane args_sha256 ended_utc pid_start_of LANES_BIND_BOUND_S "BINDING IN PROGRESS" MOVED "THE BIND HAS RUN PAST" "A BIND OF THIS TRANSCRIPT IS RUNNING ON"; do grep -qF -- "$w" "$f" || { echo "missing: $w" >&2; exit 1; }; done'`
  - **Source:** L5:176.
  - **Depends on:** SPEC-PR (lane 3, fix round B, 2026-11-09: T501 lands after the spec PR; v5:176 (lands before T502); v5:30 and v5:674 (no wait on lane 4)).
  - **Changed at assembly:** lane 3, applying: ruling R50; section D item 9 (O15; contract draft 4.1 §2.5, O9 settled by R05); lane 3, fix round B, 2026-11-09: T501 lands after the spec PR.
  - **The record** is `$CLAUDE_CONFIG_DIR/lanes/offers/<session uuid>`, on the mount shared across containers (W:5372).
  - **The keys** (W:5374–5381):
    - `mode`;
    - the offer's identity: `from`, `to`, `session`, `uuid`, `window`, `pane`;
    - the attempt: `bind`, `started_utc`, `bound_s`, `bounded`, `deadline_epoch`;
    - the child: `pid`, `pidstart`, `host`, `container`, `out`;
    - phase 2's: `act`, `write_id`, `generation`, `not_after`, `write_lane`, `args_sha256` (T510);
    - the outcome: `rc`, `why`, one `note=` per owed act, `ended_utc`.
  - **The keys `c`, `n_a` and `w_s_ms`**, written by the guard from its prefetch's `.head`, from which `guard-bind` computes `bound_s` (R05; open point 3). `c` is `.head`'s, the larger of the two URLs' `connect_timeout=`, not the snapshot's; `w_s_ms` is `w_s` in whole milliseconds, copied from `.head`'s `w_s_ms=` (O15).
  - **Liveness** (W:5383): the pid is live and its start equals `pidstart`, by `pid_start_of` (Q-L5-6). Its exit 2 counts as live (R49). The record says who reads it: the guard, `guard-bind`, lane 4's T412 (which uses `pid_start_of`) and lane 2's AT-F6.
  - **The test seam** `LANES_BIND_BODY`, as lane 5 answers point 39(b) (routed to lanes 5 and 4 by 6726110116; lane 4's note, 6726296119, raises nothing on it).
- [ ] T502 [US5] [O] Make answer 2 detached in every mode, as phase 1's first guard pull request (#347): the offer site's branch and the eleven cases, `guard_answer`'s choice 2, the internal verb `guard-bind` with its outer and body, `pid_start_of`, the record's phase-1 keys, `bound_s`, the rename back, and the child's environment, and the `lanes` arm's acceptance of `--decide`, which it ignores until T509 (FR-038, FR-040; W:5336–5468, W:5505–5512) (covers L5-A1); its selectors are v3f's AT-F6 names `[J3: v3f]`, with three guesses `[J3]` (lane 4's confirmation of the outer's sweep is in (6726296119), for v3f's open point 39, which lane 3 routed to lanes 5 and 4 in 6726110116; STOP until lane 2 has answered A1; lands after the spec PR, T501, L2-N3 and lane 2's rewrite of `tests/test_lane_helpers.sh:7811–7830`) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_guard.py -k 'sql_register_guard and (test_answer_2_exits_2_binding_before_t0_plus_1_s_and_the_runner_sees_eof or test_the_bind_child_survives_a_group_kill_and_hangup_and_ends_bound or test_a_prompt_mid_bind_is_not_passed_and_leaves_the_record or test_after_bound_one_moved_then_the_next_prompt_passes or test_a_failed_bind_renames_back_keeps_the_offer_and_allows_three_attempts or test_a_dead_bind_outer_answers_in_progress_while_its_session_lives_then_no_outcome or test_a_reused_bind_pid_gives_no_outcome_with_no_session_test or test_a_bind_body_past_its_bound_ends_with_the_bounds_code_and_leaves_no_process or test_a_live_bind_past_its_bound_that_timeout_has_not_ended_answers_2_and_names_the_pid_to_kill or test_a_binding_from_another_host_or_container_is_not_judged_by_its_pid_until_the_deadline_plus_60_s or test_an_unreadable_bind_record_is_dropped_with_exit_2 or test_two_sessions_binding_one_lane_in_files_mode_the_second_is_refused_at_the_reread or test_the_bind_child_reaches_tmux_through_tmux_pane or test_the_bind_child_environment_is_as_lane_4_lists_it or test_the_hook_string_and_guard_timeout_are_unchanged or test_kill_of_the_bind_outer_ends_the_record_with_the_signals_code or test_a_bind_with_no_timeout_says_bounded_no_and_kill_stops_its_group_once or test_the_bind_resume_path_makes_at_most_n_r_read_calls or test_answer_2_survives_a_group_sigterm_at_the_hooks_timeout or test_a_failed_bind_can_be_answered_again) and not primary' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_guard.py -k 'sql_register_guard and (test_pid_start_of_answers_live_dead_and_unreadable or test_a_bind_stopped_by_its_bound_leaves_no_process_of_its_group_after_the_sweep)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and test_lanes_accepts_decide_and_reads_as_without_it' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'guard_launch_mode or lane_helper_suite_passes or repo_hygiene'`
  - **Source:** L5:189.
  - **Depends on:** SPEC-PR; T501; PENDING-L2: A1, the files/primary parameter ids for AT-F6 (STOP until A1); PENDING-L2: L2-N3; PENDING-L2: L2-N4, unless lane 2 places test_lanes_accepts_decide_and_reads_as_without_it in L2-N3's module; PENDING-L2: A3, the rewrite of tests/test_lane_helpers.sh:7811–7830; PENDING-L2: A2, AT-F6 case 7 stubbed through LANES_BIND_BODY, with the swept-grandchild case (the verify needs it); PENDING-L2: A5's guesses test_pid_start_of_answers_live_dead_and_unreadable and test_a_bind_stopped_by_its_bound_leaves_no_process_of_its_group_after_the_sweep [J3]; PENDING-L2: test_lanes_accepts_decide_and_reads_as_without_it [J3] (R38 as amended); PENDING-L2: L2-0a to L2-0e (cluster, switch, FAKE_PSQL, stub lanes-index, prefixes), with T107-T110 through L2-0a's template, before any real-engine run (v5:189; v5:599, v5:605, v5:609–613, v5:617–618; v5:674; R38 as amended).
  - **Changed at assembly:** lane 3, applying: ruling R38, as lane 3 amended it: T502 accepts `lanes --decide` and ignores it; ruling R38, as amended: the verify gains one [J3] guess; rule: no 007 code PR lands before the spec PR (lane 3's rule, 6726556972; brief); ruling R38, as amended: T502's verify gains test_lanes_accepts_decide_and_reads_as_without_it [J3] (module placed by lane 3's analyst; lane 3's assembly working notes, not in the branch); ruling R38, as amended (T403's and T408's lines land after T502).
  - **The offer site** (in `guard_run`; W:5442 cites `:10648–10659` in W's copy): a branch of its own, after 18(h)'s count and before the table, on `mode` in `binding`, `bound` or `bind-failed`. `guard_answer` is never called on these modes.
  - **The eleven cases** (W:5444–5456):
    - the answer `2` itself: "BINDING", exit 2;
    - the child live inside its bound: "BINDING IN PROGRESS", exit 2, record unchanged; the text drops "at the latest" where `bounded=no`;
    - the child live past `deadline_epoch`: "THE BIND HAS RUN PAST ITS `<bound_s>` s BOUND", with the `bounded=yes` and `bounded=no` texts;
    - another session wanting the lane: that session's own table;
    - after success: "MOVED" once, with one line per owed note, the record removed, the next prompt 0;
    - after a failure below the third attempt: the why, `name-drift`, `bind=k`, the window renamed back to `<from>`, the offer kept;
    - after the third failure: choice 2 no longer offered;
    - no restart by itself: each `2` is one attempt, at most three per offer;
    - stale (same host and container, the pid reused, or dead with no process left in the session it led): exit 2 once, `name-drift`, `rc=unknown`, `ended_utc` the guard's time; the five copies are T510's;
    - `binding` from another host or container: exit 2, unchanged until `deadline_epoch` plus 60 s, then stale with no copies;
    - an unreadable record: dropped, with today's text.
  - **`guard_answer`'s choice 2** (`:11286`). The three refusals and the window's rename stay in the hook. In place of today's in-hook `lane-start` and `"$RESOLVED" set-row-state` (`:11409`):
    - it rewrites the offer file to `mode=binding` by temporary file and rename;
    - it forks `guard-bind` through `$detach` (`setsid`, else python3's `os.setsid`, else `nohup` alone), with `umask 077`, `TMUX_PANE` set, stdin on `/dev/null`, fds 3 to 9 closed and the output in a 0600 file per attempt, `<offers>/<uuid>.bind.<k>.out` (W:5342–5344, W:5357);
    - it prints "BINDING … detached (pid `<p>`, bounded at `<bound_s>` s)" and exits 2. The hook makes no register call of its own for answer 2. The `<bound_s>` it prints comes from the same helper `guard-bind` runs at its start, which computes it from the record, so the two never differ (W:5446, W:5385).
  - **`guard-bind <uuid>`**, the outer (W:5345–5356). It is a new internal verb, not a register writer, so it is not in the write guard's list (`:15537`). Its read-door class is `none` (T504). Its exit codes join the file's exit table (`tests/test_repo_hygiene.py:2735`). It:
    - refuses unless the record says `mode=binding`;
    - rewrites the record with its own `$$` and `pidstart`;
    - computes `bound_s` (below);
    - starts `timeout -k 10 "$(( bound_s - 10 ))" "$RESOLVED" guard-bind --body "$G_ID" &`, or `LANES_BIND_BODY` in its place under the seam (R48), and waits, again after a trapped signal, with a `TERM` trap that forwards to that pid;
    - after `timeout` returns 124 or 137, or after its trap fired, sends `TERM` to the group `timeout` led, then `KILL` 10 s later if any member is left, before it writes the outcome (point 39(a); routed to lane 5 with lane 4 by 6726110116, and confirmed by lane 4 in 6726296119; R47); the sweep ends by `bound_s` at the latest, inside `deadline_epoch` (W:5378);
    - where `timeout` is missing: `bounded=no`, the body started under `set -m` so it leads its own group, and the trap's `kill -TERM -- -<body pid>` then `KILL` 10 s later (W:5351–5355; probe P3's lesson that a trap signalling its own group runs again).
  - **The body**, `guard-bind --body`: `lane-start --no-launch --bind "$G_ID" <args>` through `guard_lane_start` (`:10496`); after a 0, the `set-row-state` on the lane the window left; then `guard_bound_check`'s read (`:11264`).
    - The `set-row-state` goes through `LANES_EDIT` where set, else `$RESOLVED` (R48).
    - In phase 1 on primary, the writers' placeholder (T409) answers 69 there, and the record carries the note. Phase 2's `LANES_ACT` and `LANES_EXPECT` on that call are T510's.
  - **The outcome** is written by the outer (W:5434–5438): `bound` with `rc=0`; `bind-failed` with `rc` and `why` = `lane-start`'s last refusal line; `rc=124`, `137` or the signal's code, with "stopped by its bound" or "stopped by a signal" and "what it wrote is not known".
  - **`bound_s`.** One helper computes it from the record, for the hook's "BINDING" text and for `guard-bind`'s start. Here the sum's defaults give 500 s: `c` = 2, `n_a` = 1, `w_s_ms` = 0. In bash 3.2's integers, `bound_s = (437000 + 28000·n_a·c + 19·w_s_ms + 9999) / 10000 · 10`. `LANES_BIND_BOUND_S` overrides it, and `deadline_epoch` is the start plus `bound_s` plus 5 s (W:5378). T507 makes the sum read `c`, `n_a` and `w_s_ms` from the record (open point 3).
  - **`pid_start_of <pid>`** as Q-L5-6 gives it; its exit 2, an unreadable start, counts as live (R49). It never copies `lock_steal_if_dead`'s pid-only test.
  - **The stale row's session test:** no process is left in the session the dead outer led. The proposal is `pgrep -s <pid>`, as v3f's case 6 names it, else a scan of field 6 of `/proc/*/stat` where `pgrep` is missing.
  - **The child's environment** (W:5359–5364):
    - kept: `HOME`, `PATH`, `CLAUDE_CONFIG_DIR`, `LANES_WORKSTATION`, `LANES_CONTAINER`, `LANES_REPO`, `LANES_EDIT`, `LANES_LANE_START`, `LANES_INDEX_CONFIG` and `TMUX`;
    - `TMUX_PANE` set to the pane the hook resolved;
    - unset: `LANES_NO_FETCH`, `LANES_ACT`, `LANES_EXPECT`, `LANES_WRITE_ID` and `LANE_START_HANDOFF_ANSWER`, and `LANES_BIND_BODY` in the body;
    - also unset, with `LANES_WRITE_ID`: `LANES_WRITE_GENERATION` (W:5149; R52). W:5359–5364 does not say what happens to a variable it does not list, and the fork inherits the hook's whole environment, so a printed rerun's generation would otherwise reach `lane-start`;
    - `LANES_GIT_TIMEOUT=45`.
  - **The rename back** on failure fixes today's finding: the window keeps `<to>`'s name, so the "KEPT" offer is dropped (W:5458).
  - **Unchanged:** the hook string, `GUARD_COMMAND` and `GUARD_TIMEOUT` (FR-040). So is the `guard` arm (`:17290`), which maps every code but 0 and 2 to 2.
  - **`lanes --decide`, accepted and ignored** (R38, as lane 3 amended it): the `lanes` arm (`:18241`) accepts `--decide` and reads exactly as without it, so its unknown-argument `die` (`:18255`) no longer meets the flag. T509 gives the flag its meaning. Lane 4's `--decide` lines, `lane`'s in T408 and `lane-end --retire-dormant`'s in T403, land after T502, not after T509. The guess `test_lanes_accepts_decide_and_reads_as_without_it` `[J3]` is selected in a run of its own.
  - **The selection:**
    - `and not primary` keeps phase 1's files half (ask A1). The primary half of these names is selected by T510, once phase 2's writes exist (v3f:294: "N, or W for AT-F6's writes");
    - `test_the_bind_child_records_bind_failed_with_the_code_and_binds_nothing` runs the real `lane-start` through `start-record`, so it is T510's;
    - case 22 is T507's, and case 21 is T510's.
  - **Not in a verify:** M9 (`test_the_bind_and_the_guards_set_row_state_are_timed_in_both_modes`) is recorded in the PR body. So is the real-session check of v3f's AT-F6 ("Not a CI test"), run on Brett's word.
- [ ] T503 [US5] [S] Make `lane-start --bind` idempotent: where the `binding` read names this window with `b_sess` the bound uuid, and the session cell's last id is that uuid, write no register line again, and write the Rule 3 stamp only where the handoff has no `RESUMED by <uuid> (lane <L>)` line (FR-039; W:4878, W:5460) (covers L5-A2) (tier `[S]`, R42); a small pull request of its own, re-cited at each landing since slices 2a and 2b also edit `lane-start`; its selector is v3f's `[J3: v3f]` (lands after the spec PR, T502, L2-N3) — files: `lane-start` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_guard.py -k 'sql_register_guard and test_a_rerun_after_a_failed_stamp_push_writes_one_stamp' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'lane_start_row_stamp or lane_start_hardening_138 or lane_helper_suite_passes'`
  - **Source:** L5:235.
  - **Depends on:** SPEC-PR; T502; PENDING-L2: L2-N3 (v5:235; v5:599).
  - **Changed at assembly:** lane 3, applying: ruling R42; rule: no 007 code PR lands before the spec PR (lane 3's rule, 6726556972; brief).
  - **The sites:**
    - `--bind` at `lane-start:820`;
    - the checks at `:2124–2131`;
    - the cell's last id at `:2132–2133`, which already says "nothing to append" for the cell;
    - the `binding` read at `:2437`, with its fields at `:2448`.
  - **The rule:** this adds the binding row and the stamp to that test, so a rerun after a failed stamp push writes no register line and exactly one stamp (AT-F6 case 11).
  - **Files mode** is otherwise byte-identical.
  - **Not `[P]`.** Lane 4's T402 is also `[P]` on `lane-start` in phase 1 (`L4:57`), and no two tasks of one file are `[P]` (`skel:846–847`). A `[P]` task also depends on no incomplete task (`skel:40–41`), and T503 lands after T502.
- [ ] T504 [US4] [O] Add the read door, `reg_open`, in the dispatcher's main shell: one mode answer per process; on primary, one `register read --prefetch` that also answers the mode; the five classes and a sixth, `writer` (R46); each class's failure; F17 off primary; `LANES_READ_MIRROR` honoured on `window-lane` and `swapped` only; and the flag honoured by asking nothing (FR-033; W:4829–4849, W:4893–4898, W:5070) (covers L5-A5, its first part); its selectors are v3f's `[J3: v3f]`, with one guess `[J3]` (its STOP is lifted: lane 5 accepted Q-L5-1 and Q-L5-2 (L5:492–499) and agreed Q-L5-5 (L5:506–507), R39 rules open point 2, R46 open point 14 and R38 open point 1 (lane 3, fix round B, 2026-11-09); lands after the spec PR, T409, T105, T113, L2-N2, L2-N4, and lane 2's phase-N item for the reader marker cases, A8) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and (test_every_dispatcher_arm_is_classified_for_the_read_door or test_a_none_class_arm_makes_no_register_call_with_the_store_unreachable or test_managed_projection_with_the_database_unreachable_exits_1_never_legacy)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_build_flip.py -k 'sql_register_build_flip and (test_lanes_edit_asks_lanes_index_mode_only_where_a_configuration_exists or test_readers_under_the_refusal_answer_the_78_as_a_failed_read) and not guard and not session-start' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_marker.py -k 'sql_register_marker and (test_a_readers_refusal_under_the_marker_names_the_refs_age or test_a_reader_refuses_when_its_read_ref_carries_the_marker_and_its_tree_does_not)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and test_the_read_mirror_variable_lowers_only_window_lane_and_swapped_on_primary' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'repo_hygiene or named_read_unreadable_290 or subfield_misreads_301 or lane_helper_suite_passes'`
  - **Source:** L5:245.
  - **Depends on:** SPEC-PR; T409; T105; T113; PENDING-L2: L2-N2; PENDING-L2: L2-N4; PENDING-L2: A8's phase-N item for the reader marker cases; PENDING-L2: A5's guess test_the_read_mirror_variable_lowers_only_window_lane_and_swapped_on_primary [J3]; PENDING-L2: L2-0a to L2-0e (cluster, switch, FAKE_PSQL, stub lanes-index, prefixes), with T107-T110 through L2-0a's template, before any real-engine run (v5:245; v5:576, v5:581, v5:598, v5:600, v5:605, v5:648–651, v5:664, v5:675; v5:678 dropped by R45; T504's STOP lifted (lane 3, fix round B, 2026-11-09: Q-L5-1 and Q-L5-2 accepted, L5:492–499; Q-L5-5 agreed, L5:506–507; R39; R46)).
  - **Changed at assembly:** lane 3, applying: ruling R38; rule: no 007 code PR lands before the spec PR (lane 3's rule, 6726556972; brief); ruling R38, as amended; ruling R45; lane 3, fix round B, 2026-11-09: T504's STOP lifted (Q-L5-1 and Q-L5-2 accepted, L5:492–499; Q-L5-5 agreed, L5:506–507; open point 2 by R39; open point 14 by R46).
  - **Where it runs.** In the main shell, after the workspace check and before the dispatcher (`:15577`; `1b:` after `:17399` and before `:17414`), so the answer outlives the `$( )` reads (W:4849, W:5070). Where the answer is carried across 1a's `exec` is R17's: `LANES__MODE`, with the `LANES__PID` check (#165 6726831985).
  - **The mode question** is asked only where a configuration exists at `lanes-index`'s path or `LANES_INDEX_CONFIG` is set (D3, W:360–364). Its answers by row (W:4829–4835):
    - none: today, plus the marker check;
    - `index`: today, plus the marker check;
    - 78: decide 69; listings and `session-start` read the mirror with a note; the guard 2; `managed-projection` 1;
    - `primary`: SQL;
    - any other failure: files mode with a warning, and under `lanes/STORE` the marker check refuses.
  - **On primary,** one `lanes-index register read --prefetch` writes the cache family at one `n`: `--all` for decide, mirror and projection arms, nothing for `none` and `writer` arms. The `guard` arm makes its own (T507). The family's sites are T505's.
  - **The classes** (W:4841–4847), with the failed read by class:
    - decide: 69, nothing on stdout;
    - mirror: `read: mirror at …` on stderr, then the mirror read;
    - projection: 1, UNKNOWN;
    - none: no door and no call;
    - guard: T507's;
    - `writer` (R46): the write guard's verbs (`:15537`), `start-record` among them; no door, since T409's ask serves them.
  - **The class table** is one place in `lanes-edit.sh`, which both `reg_open` and v3f's `test_every_dispatcher_arm_is_classified_for_the_read_door` read. Its rows:
    - today's arms;
    - lane 4's new arms: `register-text` and `register-grep` decide, `act-begin` none, `canon-lane --walk` decide, `start-record` writer;
    - `guard-bind` none;
    - `lanes` mirror, decide under `--decide` (R38: T502 accepts the flag and ignores it, and T509 gives it its meaning);
    - `history` mirror;
    - `session-start` mirror with no register call (R39).
  - **F17** (W:4893–4898): one function, run only when the mode is not `primary`. It runs `git cat-file -e "origin/$LANES_BRANCH:lanes/STORE"` in `$LANES_REPO` where `have_remote_ref` holds, and W:4895's alternative under `LANES_NO_GIT=1` or with no origin ref. It runs on the ref the read reads, after any fetch. Its outcome by class: decide 69 with Amendment 22's text naming the ref's age; mirror reads on with its line; projection 1; guard 2 (T507).
  - **`LANES_READ_MIRROR=1`** lowers `window-lane` (`:17806`) and `swapped` (`:17228`) to a mirror read; every other arm ignores it. v3f asserts this in AT-11's `[other-arms]` case, which is phase G (v3f:2095, v3f:2222), so T504's guess asks lane 2 for a phase-N case (A5).
  - **Under `LANES_REGISTER_SCRATCH`,** where T413's test honours it, `reg_open` asks nothing, prefetches nothing and makes no marker check (W:4987, W:4990; Q-L5-1).
  - **One shared mode variable, `REG_MODE`** (R45), set by assignment from the answer and never read from the environment (Q-L5-1, Q-L5-4). Whichever of T409 and T504 lands first defines it. The one line that sets `rule6_primary_on` and T426's `key()` variable from it ships with T425 and T426, not with T504 (R45). Beside it, one variable for the read sites says which store answered: SQL at `n`, files or the mirror.
  - **`lanes-index`** is resolved by T409's `lanes_index_bin` (Q-L5-2).
  - **The selection** leaves out `[guard]` and `[session-start]`, which T507 and T508 select. `test_lanes_edit_asks_lanes_index_mode_only_where_a_configuration_exists` covers the writers' ask too, hence `lands after T409`.
- [ ] T505 [US4] [O] On primary, give each read site of the phase plan's table its cache file, write the family at one `n`, add the one flush helper `reg_flush` and `reg_image <path>`, and make a read with no prefetched file one more `register read`, never git (FR-033, FR-034; W:4849–4864, W:4900–4907) (covers L5-A5, its second part); its selectors are v3f's `[J3: v3f]`, with three guesses `[J3]` (lands after the spec PR, T101, T504, T113, T114, T115) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_import.py -k 'sql_register_import and (test_import_answers_every_read_as_the_files_do or test_the_log_path_sidecar_names_the_same_log_file_in_unreadable_warnings)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and (test_a_primary_reader_makes_one_prefetch_and_every_read_site_reads_its_cache_at_one_n or test_a_read_site_with_no_prefetched_file_makes_one_more_register_read_and_never_a_git_read or test_the_flush_helper_drops_the_whole_read_family)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'repo_hygiene or named_read_unreadable_290 or subfield_misreads_301'`
  - **Source:** L5:275.
  - **Depends on:** SPEC-PR; T101; T504; T113; T114; T115; O21 (a), #165 6741093519; PENDING-L2: A5's three guesses for T505 [J3]; PENDING-L2: L2-0a to L2-0e (cluster, switch, FAKE_PSQL, stub lanes-index, prefixes), with T107-T110 through L2-0a's template, before any real-engine run (v5:275; v5:573, v5:581–583, v5:624–626; section D item 3).
  - **Changed at assembly:** lane 3, applying: rule: no 007 code PR lands before the spec PR (lane 3's rule, 6726556972; brief); section D item 1 (v5:290); section D item 2; section D item 3 (O21 closed by (a), #165 6741093519, in draft 4.2 and later); section D item 4.
  - **The sites** (W:4853–4864, which cites W's copy):
    - `register_text_checked` (`:3648`) reads `.register`, and a missing file never falls to `register_published` or git;
    - `register_published` (`:3690`) answers 0 or 8 from `.paths`;
    - `archive_text` (`:3722`) reads `.archive`;
    - `lane_alias_text` (`:5493`) reads `.aliases`, an absent table written as `# no lane aliases`;
    - `lane_log_events` (`:3557`) reads `.log.<lane>` before `log_path_ci`;
    - `log_path_ci`, `lane_log_exists` and `known_lanes` read `.paths`, case-insensitively;
    - `remote_log_events` (`:3430`) reads `.paths` and `.log.*`, with `parse_log_stream` unchanged;
    - `retired_identity_hits` reads through `archive_text`.
  - **Files and `index` mode** are unchanged (FR-034): each consult is one `-f` or `-s` test of a file that only primary writes.
  - **The log key** is `$1` as received, only in `check_lane_name`'s class; any other name takes the second read (W:4905) as form 3's `--log-key=<key>`, in the `=` form (L1-D1) and never inside `--files`, and its log is `.log.%<hex>` and `.logpath.%<hex>`, `<hex>` the key's bytes in lowercase hex, rewritten on each read and never counted as cached (O18). Where two render-set logs differ from the key only by case, no `.log.<key>` is written and form 3 ends 69, the class's failed read, as `log_path_ci` returns 2 today (C11).
  - **The sidecar** `.logpath.<lane>` holds the resolved `lanes/log/<File>.md` at `n`, or nothing, so `unreadable:` warnings name the file as the mirror read does (W:4906).
  - **`reg_flush`** drops `.register`, `.aliases`, `.archive`, `.paths`, `.head`, `.outage`, `.log.*` and `.logpath.*` (the `.log.%<hex>` and `.logpath.%<hex>` names among them), keeps `.direct`, empties `LANES_REGISTER_CACHE`, and runs `lane_alias_flush`, which empties `LANES_ALIAS_CACHE` and drops `.aliaserr` (W:4907; contract §3.6, C6). It replaces today's register and alias drops in `log_sync`, `index_open` and the guard's post-bind site; `index_open`'s `state_events_flush` stays where it is. Lane 4's T414 calls it after `committed`, and T507 calls it for the post-bind refill. Cleanup's glob (`:1261`) removes the family unchanged.
  - **`reg_image <path>`** is lane 4's T401 primary branch (Q-L5-3), and O21 (a) gives its scope (lane 4, #165 6741093519; contract §3.6 and §8, O21 closed): it serves only `lanes/LANES.md`, `lanes/aliases.tsv` and `lanes/log/<File>.md`, from the members `register`, `aliases` and `log:<File>`; T401 answers 2 for any other path before calling it. It prints the member from the family; a member not cached takes form 3, `register read --since <n> --cache <file> --files <member>`, at `n′`, and a `<File>` outside `check_lane_name`'s class takes form 3's `--log-key=<File>` and reads `.log.%<hex>` (O18). It prints a log only where its `.logpath` names exactly `<path>`, and for `lanes/aliases.tsv` it prints the table's own bytes, nothing for an empty or absent table, never `.aliases`' `# no lane aliases` line (`code/lanes-edit.sh:5434`, pin `2a057a6d`). Any other end, two logs that differ from `<File>` only by case (C11) or a non-0 from form 3 among them, exits 69, the decide class's failed read, never an empty answer.
  - **The second read** (W:4849): one more `register read`, which returns the paths changed in `(n, n′]`; where any of them is cached, the whole family is refetched at `n′`. It is contract §3.6's form 3, `register read --since <n> --cache <file> --files <set>`, at `n′`, for every read site outside the guard; the guard's second read is form 4, at `n` (T507). H1's `journal --since` question is closed: `journal --since` does not serve the second read (R06; contract draft 4.1, §3.6).
  - **Writer-side reads** (W:4867) are phase 2's, on the scratch tree, and lane 4's.
  - **The second read's helper** picks the form by the door's class: form 4 in the `guard` class (T507), form 3 in every other. A failed second read makes the site function return non-0, never an empty answer: any end but 0 is the class's failed read, never files mode (C3, B9). No flag file is needed where the return code already leaves the `$( )`.
- [ ] T506 [US4] [O] Finish the readers on primary: `swapped_lanes`' rank through `journal --last`, the local-file unions skipped, `log_sync` with no fetch and no flush and with F17 at its end off primary, `register-row-local` reading the database (D11), `managed-projection` from SQL, and `next-free` without the local union (FR-033; W:506–512, W:4863–4866, W:4896) (covers L5-A5, its third part); its selectors are v3f's `[J3: v3f]`, with four guesses `[J3]` (lands after the spec PR, T505, T114, L2-N4, and lane 2's phase-N item for the reader marker cases, A8) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and (test_register_row_local_reads_the_database_on_primary_and_is_69_never_8 or test_managed_projection_answers_0_1_8_from_sql_as_files_mode_does)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and (test_swapped_ranks_through_journal_last_on_primary or test_log_sync_on_primary_makes_no_fetch_and_no_flush or test_the_local_file_unions_are_skipped_on_primary)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_marker.py -k 'sql_register_marker and test_the_marker_check_runs_at_the_end_of_log_sync' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'repo_hygiene or named_read_unreadable_290 or subfield_misreads_301 or register_hardening_120_122'`
  - **Source:** L5:293.
  - **Depends on:** SPEC-PR; T505; T114; PENDING-L2: L2-N4; PENDING-L2: A8's phase-N item for the reader marker cases; PENDING-L2: A5's four guesses for T506 [J3]; PENDING-L2: L2-0a to L2-0e (cluster, switch, FAKE_PSQL, stub lanes-index, prefixes), with T107-T110 through L2-0a's template, before any real-engine run (v5:293; v5:582, v5:600, v5:605, v5:627–629, v5:634).
  - **Changed at assembly:** lane 3, applying: rule: no 007 code PR lands before the spec PR (lane 3's rule, 6726556972; brief); section D item 10; lane 3, fix round B2, 2026-11-09: C8 with `--generation`, and its 64 and 69 ends (#165 6742023901, 6742096448).
  - **`swapped_lanes`** (`:9284`): its rank is the head less `lanes-index journal --last --path <log> --grep <key> --at <n> --generation <g>`, `<n>` and `<g>` being `.head`'s `n=` and `generation=` as they stand, so the rank is read in the family's one snapshot (Y:4890; C8; contract §3.6, CLI43:796) (W:4863; lane 1's T114, `L1:327–329`). Under contract draft 4.3 (:792, :796), which draft 4.4 keeps, the call ends 64 without `--generation`, and 69 for an `n` above the head or a different generation (#165 6742023901). Its key matches as a fixed string, as git's `-S` pickaxe does, never as a regex (C7; T114's case `journal_last_pickaxe`). With no `seq=` line it keeps 999999999, as today (`code/lanes-edit.sh:9085` at pin `2a057a6d`). Any end of `journal --last` but 0, a 2 for a refused configuration included, is the class's failed read.
  - **The local-file unions are skipped on primary** (W:4865): `row_of_lane_local` through `session_ids_local_of_lane`, `lanes_register_index --local`, `managed_local_hint`, `lane_position_rows` (`:9080`) and `row_stamp_is_unknown`. A primary write is in SQL before it returns, so no local row can be ahead of the truth (D11's reason).
  - **`log_sync`** (`:6328`): with the door open on primary, no fetch and no flush (W:4866). Off primary, F17 runs at its end, after its fetch has moved the ref; all forty calls are bare statements (W:4896).
  - **`register-row-local`** (`:18413`): on primary it reads the database as `register-row` does, 8 included, and exits 69, never 8, where the read fails (D11, W:506–512).
  - **`managed-projection`** (`:18943`): 0, 1 or 8 from SQL, as files mode answers; 1, UNKNOWN, on a failed read, never `legacy` (S4).
  - **`next-free`** (`:18345`): `lane_next_free` (`:9125`) takes its rows from stdin and the register at `n`, without `lane_position_rows`' local union; decide, 69.
- [ ] T507 [US5] [O] [X: hosting] [X: logins] Give `guard_run` its phase-1 SQL read: one `register read --for guard --prefetch` with `$$` for `T0`, its cache sites, its second read through form 4, SVC-8's budget lines and exit mapping, SVC-12's declared-outage path, F17 in files and `index` mode, the post-bind flush, and `c`, `n_a` and `w_s_ms` written into the bind record; and the client half of `--for guard` in `lanes-index` (FR-037; W:4072–4300, W:4389–4442, W:4885–4890) (covers L5-A3, and U4's guard side); its selectors are v3f's `[J3: v3f]`, with four guesses `[J3]` (its `lanes-index` half is lane 5's, under lane 1's review, after T113 (R07); lane 1's R07 review also covers T507's calls of `register read` (L1:467–478); lands after the spec PR, T502, T504, T505, T101, T102, T103, T104, T113, T121, T302, T304, L2-N2, L2-N3, L2-N5, L2-N8 (the PR body's figures only), and lane 2's phase-N item for the reader marker cases (A8); if lane 2 places two rollback cases in phase G, their names leave this verify instead, and T507 waits for no phase-G item (A9)) — files: `lanes-edit.sh`, `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_guard.py -k 'sql_register_guard and (test_a_database_that_does_not_answer_is_2_inside_4_5_s or test_a_guard_that_starts_too_late_refuses_2_without_a_read or test_a_connect_timeout_above_the_direct_reads_share_is_cut_at_the_parse_start_line or test_a_whole_file_fetch_the_start_line_cuts_fills_the_image_cache_for_the_next_prompt or test_the_guard_maps_69_75_and_3_to_2 or test_the_direct_read_connects_as_its_engineers_reader_login or test_claude_no_lane_still_skips_before_any_input or test_the_hook_string_and_guard_timeout_are_unchanged or test_guard_bind_computes_bound_s_from_c_n_a_and_the_service) and not service-up' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_outage.py -k 'sql_register_outage and (test_the_guards_outage_refusal_names_the_store_the_cause_and_both_ways_out or test_claude_no_lane_lets_a_session_through_before_any_input or test_a_declared_outage_lets_the_guard_answer_from_the_mirror or test_an_expired_or_foreign_declaration_changes_nothing or test_a_rollbacks_declaration_counts_only_where_the_guards_read_finds_the_switch_at_files or test_a_declared_outage_lets_the_guard_read_the_mirror_with_service_and_database_down or test_with_no_declaration_the_guard_exits_2_with_service_and_database_down or test_under_a_declaration_a_silent_database_is_capped_and_the_mirror_answers_inside_the_budget or test_the_declaration_is_read_once_at_the_prefetch or test_under_a_declaration_a_direct_read_left_under_0_5_s_does_not_start or test_without_a_declaration_the_same_age_still_starts_the_direct_read or test_the_guard_refuses_a_mirror_older_than_the_declared_seq or test_the_guard_refuses_2_when_no_commit_within_the_bound_carries_register_seq or test_the_register_seq_lookup_finds_the_trailer_at_depths_0_and_199 or test_the_guard_refuses_a_ref_behind_a_rollbacks_head)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_marker.py -k 'sql_register_marker and test_an_unconfigured_workstations_acts_refuse_and_listings_read_the_mirror' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_build_flip.py -k 'sql_register_build_flip and test_readers_under_the_refusal_answer_the_78_as_a_failed_read and not session-start' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_guard.py -k 'sql_register_guard and (test_the_guards_last_budget_line_is_below_guard_timeout or test_the_post_bind_refill_is_one_register_read_and_never_git or test_a_second_read_past_the_start_line_is_noted_not_refused)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_marker.py -k 'sql_register_marker and test_a_mode_index_guard_refuses_2_under_the_marker' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'guard_launch_mode or repo_hygiene or install_hook_gate'`
  - **Source:** L5:301.
  - **Depends on:** SPEC-PR; T502; T504; T505; T101; T102; T103; T104; T113; T121; T302; T304; PENDING-L2: L2-N2; PENDING-L2: L2-N3; PENDING-L2: L2-N5; PENDING-L2: L2-N8 (the PR body's figures only); PENDING-L2: A8's phase-N item for the reader marker cases; PENDING-L2: A9, the phase of the two rollback outage cases (changes the verify, never the gate); PENDING-L2: A5's and A7's guesses for T507, and the form-4 too-late guess test_a_second_read_past_the_start_line_is_noted_not_refused [J3]; PENDING-L1: lane 1's R07 review of T507's client half in `lanes-index` and of T507's calls of `register read` (L1:467–478); PENDING-L2: L2-0a to L2-0e (cluster, switch, FAKE_PSQL, stub lanes-index, prefixes), with T107-T110 through L2-0a's template, before any real-engine run (v5:301; v5:573–575, v5:581, v5:585, v5:598–602, v5:605, v5:652, v5:661–662; R07; comment 6739068493).
  - **Changed at assembly:** lane 3, applying: ruling R07; rule: no 007 code PR lands before the spec PR (lane 3's rule, 6726556972; brief); comment 6739068493: the too-late proof of form 4 joins T507's own verify; section D item 1 (B9: the guard's second read is form 4); comment 6739068493: lane 5's suggested word, [J3] until lane 2 picks it with v3f; section D item 8 (with L1-D1); section D item 1 (B9: v5:311); ruling B7 (lane 3, #165 6738474549); section D item 8; section D item 7 (O15, change 3); section D item 7 (.outage in §3.6's format); section D item 7 (the whole-file fetch applies to form 4); section D items 7 and 9 (O15, changes 1 and 5); section D item 6; section D item 6 (B4, as lane 3 ruled it); section D item 5 (C1); comment 6739068493 (the too-late proof is T507's, not T113's), with B5; section D item 9; lane 3, fix round B, 2026-11-09: the task line's gate matches its Depends on.
  - **The arm** (`:17290`) is unchanged. `CLAUDE_NO_LANE=1` still exits at `:655`, before any input.
  - **One prefetch** before the guard's first register read in `guard_run` (`:10601`), on a configured workstation only. It answers the mode first (W:4091–4096):
    - `index`: today's guard;
    - 78: 2;
    - any other failure: files mode with a warning, and 2 under `lanes/STORE`.
  - **The argv** (contract §3.6; L1-D1's `=` form). Form 2: `lanes-index register read --for guard --prefetch --cache "$SE_CACHE_FILE" --pid $$ --repo "$LANES_REPO" --lane="$G_WINNAME"`. `G_WINNAME` is the guard's own window-name variable (pin `:10297`, set at `:10433` from `tmux display-message -p '#W'`; `577110e4` `:10507`, `:10643`); the guard refuses 2 where it is empty (pin `:10434–10437`) before its first register read, so the prefetch always has one. T507 still leaves `--lane=` out where it is empty, since an empty `--lane=` ends 64. Form 4: `lanes-index register read --for guard --at <n> --pid $$ --cache "$SE_CACHE_FILE" --lane="$1"`, with `<n>` from `.head`'s `n=`, and no `--repo`.
  - **Its cache sites** (W:4098–4107):
    - `.register` for `register_text_checked` and the guard's register reads;
    - `.aliases` for `lane_alias_text`;
    - `.log.<lane>` for `lane_log_events`, keyed by the window's name as received;
    - a missing `.log.<key>`, or a key outside `check_lane_name`'s class, makes one form-4 read at `.head`'s `n`, never form 3, never `log_path_ci` and never the mirror (W:4113–4118; B9; O17). In `guard_run` the only cache files read are `.register`, `.aliases` and `.log.<key>` (C9).
  - **No `.direct` flag here.** Phase 1's `register read` has only its direct path; the socket, the `.direct` flag and the write-through come with `serve` (W:4817). So the flag (W:4109–4112) is T512's. Form 4 never reads it, even then: it always reads directly, never through the service (B7; lane 3, #165 6738474549).
  - **The client half, in `lanes-index register read --for guard`** (SVC-8, SVC-12; lane 5's, under lane 1's review, after T113: R07):
    - `T0` from the hook's pid, passed as `--pid $$` in forms 2 and 4 (contract §3.6; B1): Linux, field 22 of `/proc/<pid>/stat` against `/proc/uptime`; macOS, `ps -o etime=`;
    - the clamp, and the direct read's cap at `T0 + 3.75 s`, or `T0 + 3.65 s` under an unexpired declaration naming this database, with the 0.5 s floor under such a declaration;
    - "too late in the hook's 5 s to verify the triple" (W:4296);
    - the declaration read once, at the prefetch (W:4398–4401);
    - SVC-12's conditions 2 and 3: the expiry, the database label and the rollback form's switch test (W:4398–4404), and form C's one `git log --first-parent -n 200 --format='%H%x00%(trailers:key=Register-Seq,valueonly)'` in `--repo` on `origin/$LANES_BRANCH` as last fetched, the ref every read the guard makes uses (O15), with no `--grep`, taking the first non-empty value and refusing 2 where none of the 200 carries one (W:4405–4414);
    - the verdict handed to the guard in an `.outage` sidecar in contract §3.6's format: `format=1`, `verdict=open|behind|no-seq`, `label=(declared outage <utc>: <why>)` on one line, `declared_seq=`, `mirror_seq=` with `behind`, and `mirror_tip=`, the tip form C walked, which the guard's mirror read then uses. It is written only where form 2 exits 69 and an unexpired declaration names this database, and form 4 writes none (O15; B4). The guard reads `open` as the mirror path, `behind` as 2 with lane 5's text, and `no-seq` or no file as 2 with S3's outage text;
    - the detached whole-file fetch the start line cuts, started as `guard-bind` is, bounded at `N_a`·`c` + 15 s, into T302's image cache (W:4183–4186), for form 4's direct read too (B9);
    - reads, never writes, `.head`'s `c=`, `n_a=` and `w_s_ms=`: forms 1 to 3 write them (T113; contract §3.6; `L1:290`), and `guard_answer` copies them into the record for `bound_s` (below; O15; R05; open point 3);
    - on a 69 under `--for guard`, the cause as stderr's first line, which the guard quotes in AT-P3's refusal (O15).
  - **The guard's side** of the prefetch's exit mapping, form 2's (W:4291–4299):
    - 0: parse, and decide 0 or 2;
    - 69: SVC-12's check, else 2 naming the store, the cause and the two ways out (AT-P3);
    - the late row: 2, "too late";
    - `mode index`: today's guard;
    - 78: 2;
    - any other: files mode with a warning, and 2 under `lanes/STORE` (Q21(a)(A)). A 1, a refused configuration with R09's included, is in this row: it cannot be told from a crash's 1 (O14).
  - **Form 4's ends** (B4, lane 3's ruling, #165 6738474549): 0 with the one log's files written. Every other end, 2 for too late, 69, 78, 1, 64, a crash, or a 0 that wrote no `.log.<key>`, is a log that could not be read: `lane_log_events` returns non-0, and `guard_run` notes it as today (`code/lanes-edit.sh:9830–9831` and `:10707–10710` at pin `2a057a6d`) and does not refuse: the guard never answers 2 for it and never opens the mirror path. The prefetch, form 2, keeps its "too late" refusal with 2.
  - **SVC-12's mirror path:** today's parser on the mirror's text, with `read: mirror at <sha12>, …` and `(declared outage <utc>: <why>)` in place of the cause. A mirror below `m` refuses 2 with lane 5's text (W:4422–4424). A rollback's declaration counts only where condition 1's read found the switch at `files`.
  - **Two outage cases may be phase G.** `test_a_rollbacks_declaration_counts_only_where_the_guards_read_finds_the_switch_at_files` (AT-D20) and `test_the_guard_refuses_a_ref_behind_a_rollbacks_head` (AT-F11) need a database whose switch is at `files` after a `rollback` entry, and v3f says "the rollback's cases are G" (v3f:3903–3905). Lane 2 is asked which phase they are (A9). If G, they leave this verify.
  - **F17 in `guard_run`** in files and `index` mode: 2 (W:4896). No v3f test covers the `index` half; T507's marker guess asks for one (A7).
  - **The post-bind refill** (`guard_bound_check`, `:11264`) calls `reg_flush` and makes one form-1 read, `register read --prefetch --cache <file> --files register,aliases`, with a reader's row (`T_r` + `w_s`), never `--for guard` and never git (C1; W:4889). It runs in `guard-bind`'s detached child after `lane-start`, where no hook `T0` exists.
  - **The budget lines** `T0 + 3.75 / 4.25 / 4.4 s` are one set of constants, and a test holds that `T0 + 4.4 s` is below `GUARD_TIMEOUT`. The hook string is untouched. Form 4 has no declaration cap or floor: its cut is always `T0 + 3.75 s`, and it reads no declaration (B5). T507's own verify proves form 4's end for too late, beside form 2's too-late test: a form-4 read started at or past `T0 + 3.75 s` ends 2, writes no `.log.<key>`, and is noted, not refused, and its cut stays `T0 + 3.75 s` under an unexpired declaration. The guess is `test_a_second_read_past_the_start_line_is_noted_not_refused` `[J3]`; lane 2 picks the final word with v3f (lane 5, #165 6739068493).
  - **`guard_answer`** writes the prefetch's `c`, `n_a` and `w_s_ms` into the record at `mode=binding`, from `.head`: `c` is the larger of the two URLs' `connect_timeout=`, not the snapshot's, and `w_s_ms` is `w_s` in whole milliseconds (O15). `guard-bind` sums `bound_s` from them; absent, the defaults give 500 s (R05; open point 3). The larger `c` only lengthens T510's `w₀`. This is case 22's code.
  - **X:** `c` for each address (X5 texts 13 and 17); the direct read as the engineer's reader login (texts 11 and 12); never a pooler (text 15; lane 5's position, open point 20); the read's own timer ends the read within its share (text 22, `Wsvc:124`).
  - **Not in a verify:** M5b and M10 (`test_todays_direct_guard_is_timed_in_its_four_parts`, `test_the_guards_register_parse_is_timed_on_a_real_sized_register_beside_todays_six_reads_through_git`) are recorded in the PR body.
- [ ] T508 [US4] [O] Make the `SessionStart` block read the mirror offline on primary, labelled `read: mirror`, exiting 0, with no prefetch and no register call; and run F17 in `session_start_block` off primary, reading on with its line (FR-033; W:3001, W:4443–4455, W:4896; Wspec:637–639) (covers L5-A4); its selectors are v3f's `[J3: v3f]`, with two guesses `[J3]` (lands after the spec PR, T504, T507, L2-N2, and lane 2's phase-N item for the reader marker cases, A8) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_build_flip.py -k 'sql_register_build_flip and test_readers_under_the_refusal_answer_the_78_as_a_failed_read' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_guard.py -k 'sql_register_guard and test_the_session_start_block_makes_no_register_call_on_primary' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_marker.py -k 'sql_register_marker and test_the_session_start_block_reads_on_under_the_marker_with_its_mirror_line' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'install_hook_gate or install_skill_and_hook or repo_hygiene'`
  - **Source:** L5:338.
  - **Depends on:** SPEC-PR; T504; T507; PENDING-L2: L2-N2; PENDING-L2: A8's phase-N item for the reader marker cases; PENDING-L2: A5's two guesses for T508 [J3]; PENDING-L2: L2-0a to L2-0e (cluster, switch, FAKE_PSQL, stub lanes-index, prefixes), with T107-T110 through L2-0a's template, before any real-engine run (v5:338; v5:598, v5:605, v5:621, v5:635).
  - **Changed at assembly:** lane 3, applying: rule: no 007 code PR lands before the spec PR (lane 3's rule, 6726556972; brief).
  - **The arm** is `session-start` (`:17272`), and the block is `session_start_block` (`:9434`).
  - **Its class** is mirror with no register call (R39). It decides nothing, so S3 lets it read the mirror, and a database in its path would add a failure mode to a hook that must exit 0 (W:4443–4446).
  - **With 78**, it reads the mirror with a note (W:4833).
  - **It starts no service yet;** that is T513's.
- [ ] T509 [US4] [O] Give the `lanes` listing its primary behaviour: the `lanes` arm's mirror class, with `--decide` for the callers that act on it; a footer that names the store that answered and claims no local read on primary; no "NO FETCH WAS MADE" note and no index line on primary; and the readers' table across every class (FR-033, FR-036; W:3000, W:4844; Wspec:625–646) (covers the ruling's "the `lanes` script's reads", under U1); its selectors are v3f's `[J3: v3f]`, with two guesses `[J3]` (open points 1 and 11 are ruled, by R38 and R43; lands after the spec PR, T504 to T508, lane 4's T402 to T408, and L2-N4) — files: `lanes`, `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and (test_each_reader_follows_the_table_when_the_store_is_unreachable or test_each_mirror_read_prints_its_line_and_keeps_its_exit or test_a_caller_that_acts_on_a_listings_answer_refuses_69_where_the_store_is_unreachable or test_one_failed_call_anywhere_in_a_callers_read_sequence_never_fails_open or test_index_flag_on_primary_changes_nothing_and_prints_no_index_line)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and (test_the_lanes_footer_on_primary_names_the_register_and_claims_no_local_read or test_lanes_fetch_on_primary_prints_no_no_fetch_note)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'lane_helper_suite_passes or repo_hygiene or named_read_unreadable_290'`
  - **Source:** L5:344.
  - **Depends on:** SPEC-PR; T504; T505; T506; T507; T508; T402; T403; T404; T405; T406; T407; T408; PENDING-L2: L2-N4; PENDING-L2: A5's two guesses for T509 [J3]; PENDING-L2: A6, the joint tests' names kept stable through J3; PENDING-L2: L2-0a to L2-0e (cluster, switch, FAKE_PSQL, stub lanes-index, prefixes), with T107-T110 through L2-0a's template, before any real-engine run (v5:344; v5:600, v5:630–631, v5:636, v5:676; R38, R43).
  - **Changed at assembly:** lane 3, applying: ruling R38 and R43 (open points 1 and 11); rule: no 007 code PR lands before the spec PR (lane 3's rule, 6726556972; brief); ruling R38, as amended; ruling R38; ruling R43.
  - **The `lanes` arm** (`:18241`) accepts `--decide` since T502, and gives it its meaning here (R38, as lane 3 amended it): it makes the arm's class decide; without the flag it is mirror. An older `lanes-edit.sh`, without T502, refuses the flag with 64 (`:18255`). Both callers fail closed: `lane-end`'s `*)` arm (`:769`) as a read that did not answer, and `lane`'s `64)` arm (`lane:411`) with "bad arguments", whose text lane 4's T408, which carries `lane`'s `--decide`, rewords (R38).
  - **The callers** that pass `--decide` are `lane` (`:389`, `:406`) and `lane-end --retire-dormant` (`:748`). Those two lines, and the reworded `64)` text at `lane:411`, are lane 4's: `lane`'s in T408 and `lane-end`'s in T403 (R38). They land after T502, which accepts the flag, not after T509 (R38, as lane 3 amended it).
  - **On primary,** the arm makes no fetch, so `--fetch` prints no "NO FETCH WAS MADE" note (`:18279`). `--index` changes nothing and prints no index line (`:18283`; Wspec:641–646).
  - **The footer** (`lanes:813–829`):
    - on primary, where the register answered, it says the listing is as of the register at seq `<n>`;
    - where the mirror answered, the arm's `read: mirror` line is shown, and the footer says so;
    - files and `index` mode are byte-identical.
    - On primary only, and only where the `lanes` script passes `--store-line`, the arm prints `read: register at seq <n>` on stderr, and the script matches it as it matches the fallback sentence today; off primary the flag prints nothing. Both of the arm's usage texts (`:18245`, `:18255`) name it. Where an older `lanes-edit.sh` refuses the flag with 64, the script runs the arm once more without it. `lane`'s `read_rows` passes no such flag, so it stays quiet (R43; Wspec:642).
  - **The script's other reads:**
    - `lanes --closed` (`:631`) is mirror;
    - `next-free` (`:608`, `:802`) decides, and its 69 is already treated as no answer (`:609`, `:808`);
    - `fetch-age` (`:818`, `:828`) is `none`, and is not called on primary;
    - `worktrees` (`:346`, `:357`) keeps the class T504's table gives it.
  - **The cross-class tests** sit here because this is lane 5's last group A task. Their script rows are lane 4's arms (T402 to T408), hence the gate.

### Implementation for group A: lane 4 (U2 and U3)

- [ ] T401 [US4] [O] Add the two read verbs that the lifecycle scripts' direct reads move behind (FR-036; Y:4920–4926), `register-text` and `register-grep`, as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-A1, its verbs) (lands after L2-N4; its primary branch lands after T505) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and (register_text or register_grep)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'repo_hygiene'`
  - **Source:** L4:82.
  - **Depends on:** SPEC-PR; PENDING-L2: L2-N4; T505 (its primary branch) (its gate, L4:82).
  - **The set both verbs answer** (O21, closed by lane 4's decision, option (a); lane 5's C2, #165 6738431117; lane 1's preference, #165 6738871108; the log set by lane 4's ruling, lane 4's coordinator, 2026-11-09): `lanes/LANES.md`, `lanes/aliases.tsv` and each `lanes/log/<lane>.md`, which is Y's Q18 (A) render set without `lanes/archive/` (Y:258–259). Any other path, `lanes/log/README.md` included, exits 2, in every mode, files, `index` and primary alike, so that no caller comes to depend on files mode reading more. No reader form is added, so lane 1 needs no draft 5.
  - **`register-text <path>`** prints one file of that set: in files and `index` mode, the bytes the scripts read today (`$LANES_FILE` for `lanes/LANES.md`); on primary, `reg_image <path>`, lane 5's door's image of that path (T505; Q-L5-3, answered). Under option (a) the door serves it from the cache family's `.register`, `.aliases` or `.log.<key>`, through a second read where it is not cached (contract §3.6; lane 5's section D, item 3).
  - **`register-grep [-h] -E <ERE> -- <path>…`** accepts `lanes/LANES.md`, `lanes/aliases.tsv`, `lanes/log` and any one `lanes/log/<lane>.md`. `lanes/log` stands for exactly the `lanes/log/<lane>.md` files, in every mode: in files and `index` mode the verb greps `lanes/log/*.md` without `lanes/log/README.md`, by the pathspec `':(glob)lanes/log/*.md' ':(exclude)lanes/log/README.md'` (with `glob` magic, `*` does not cross `/`), which is exactly Y's Q18 (A) set of lane logs and the same files the primary image holds. It prints what `git grep` prints today on `origin/<branch>` in the workspace root: `lanes/log/README.md` holds no line that T404's three greps match (lane 4's check on the register at 14:12Z: 0 matching lines), so their output stays byte-identical (spec:59). On primary it greps `reg_image`'s images, one per path:
    - `lanes/log` stands for each `lanes/log/<lane>.md` path in the render set at the door's snapshot (`.paths`, contract §3.6, C10), and the render set holds no `lanes/log/README.md` (Y:258–259);
    - a log whose name is outside `check_lane_name`'s class is read through form 3's `--log-key=`, by lane 5's door, since `--all` fetches none (O18; lane 5's section D, item 4);
    - where the door cannot give a path's image, as for two logs that differ only by case (C11) or a failed second read, the verb exits 69 and prints no partial answer. For a case pair that is lane 4's decision (Q-W11-4 (A), lane 4's coordinator, 2026-11-09): the sweep deletes nothing until the pair is fixed, where files mode greps both, and it stays inside O21 (a).
  - **Every reader in lane 4's tasks stays inside the set.** T402 and T403 read `lanes/LANES.md`. T404's greps read `lanes/log` (`lane-worktrees:711`, `:5596`), and `lanes/LANES.md` with `lanes/log` (`:1002`).
  - **Exits:** 0, or 1 for `register-grep` with no match. 69 when the read fails, in every mode, so that `lane-worktrees` deletes nothing (Y:4926). 2 for a usage error, or a path outside the set, `lanes/log/README.md` among them.
  - **Its tests** (lane 2's, L2-N4, named with v3f): beside the byte-identical cases, lane 2's cases for each verb's exit 2 on a path outside the set, `lanes/repos.tsv` and `lanes/log/README.md`, in files mode and on primary. Lane 2 writes and places them (lane 3's PB5, #165 6741485907); the `lanes/log/README.md` case is lane 4's ruling on the log set. T401's words `register_text` and `register_grep` select them.
  - **Wiring:** both verbs join the unknown-subcommand list (`:21428`). Their dispatcher class is `decide`, a row in T504's table (Q-L5-5, accepted), so a failed second read is the class's failed read, 69, never files mode (contract §3.6, C3). The verb `register-row` (`:20276–20283`) is the pattern to follow.
  - **The 69 row:** the first task to exit 69, in any lane, adds the exit table's 69 row (R21). T401 adds it if no earlier task has.
- [ ] T402 [P] [US4] [O] Move `lane-start`'s reads of `$LANES_FILE` behind `register-text`, keeping files mode byte-identical, and set its read arms as "The callers' default arms" give them (Y:5720–5747; FR-036), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-A1) (lands after T401, L2-N4) — files: `lane-start` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and lane_start' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'lane_start_hardening_138 or a23_lane_start_read or lane_start_row_stamp'`
  - **Source:** L4:94.
  - **Depends on:** SPEC-PR; T401; PENDING-L2: L2-N4 (its gate, L4:94).
  - **The reads:** `rows_for_lane` (`lane-start:1623`) and `row_names_for_lane` (`:1634`), used at `:1644–1650`, keep their awk and read `register-text lanes/LANES.md`. A non-zero answer refuses with that code; it is never an empty register.
  - **The arms** (Y:5726–5732, pinned at `2a057a6d`; re-cite each by function):
    - `canon-lane` (`:1136–1161`) refuses 69, 75 and 3, passing the code, where today it notes and carries on;
    - `session-lane` (`:2237–2239`) refuses 69, 75 and 3;
    - `resolve-home`, and the four lane-transcript and lane-dir reads, refuse 75 and 3;
    - the arms that already refuse pass the code.
  - **The requirements:** spec:663–690 states these arms. The reads that decide state refuse 69 before anything is renamed or written, and the seven default read arms refuse the busy and in-doubt codes.
  - **Files mode:** none of these codes is emitted in files mode (Y:5746).
- [ ] T403 [P] [US4] [S] Move `lane-end`'s awk over `$LANES_FILE` behind `register-text`, keeping files mode byte-identical, set its read arms (Y:5733–5735), and pass `lanes --decide` in `--retire-dormant` (R38), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-A1) (lands after T401, T502, L2-N4) — files: `lane-end` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and lane_end' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'lifecycle_fail_open_113_114_115'`
  - **Source:** L4:103.
  - **Depends on:** SPEC-PR; T401; T502; PENDING-L2: L2-N4 (its gate, L4:103).
  - **Lane 3 accepts lane 4's answer** (#165 6742584818; lane 3, fix round B2, 2026-11-09): this task's verify keeps its stub test. T509's verify runs the joint test, parametrized over `lane` and `lane-end --retire-dormant` (Y test-plan.md:2933); selecting it here would gate this task on T509 and make a cycle.
  - **The reads:** `rows_for_lane` and `row_names_for_lane` (`lane-end:1776–1794`) read `register-text lanes/LANES.md`. A non-zero answer refuses with that code.
  - **The arms:** the `canon-lane` arms refuse 69, 75 and 3. The arms that already die pass the code.
  - **`--retire-dormant`** (`lane-end:755`, `rd_rows`) acts on its `lanes --fetch --closed --prefix` answer, so it passes `--decide` and refuses 69 where the database is unreachable (spec:633–635; Y:3022; R38).
    - **Why after T502:** a `lanes-edit.sh` without lane 5's T502 refuses `--decide` with 64 (the `lanes)` arm's `*)`, `lanes-edit.sh:20130` at `34799b74`). That fails closed, but it would also stop the sweep in files mode, so the task lands after T502, which makes the arm accept `--decide` and ignore it (#165 6726857112, taking lane 3's preference in 6726831985). The task needs no split.
    - **The 69-refusal test** drives a stub `lanes-edit.sh` whose `lanes --decide` exits 69, in the pattern of v3f's stub `lanes-index` (`test-plan.md:623` at Y). The stub reaches `lane-end` through `$LANES_EDIT` (`lane-end:547`, which `--retire-dormant` calls at `:755`), so the verify needs no T509; the real 69 is T509's own test. Lane 2 confirms the stub in ask A1's round, when it names the selectors.
    - **Why the verify keeps the stub** (lane 4's decision on lane 3's review of phase B, #165 6741485907, item 1, answered and not applied): this verify proves the caller's side with the stub, passing `--decide` and failing closed on 64 and on 69. R38's joint behaviour is proven by the joint test `test_a_caller_that_acts_on_a_listings_answer_refuses_69_where_the_store_is_unreachable` in T509's verify, and T509 lands after T402 to T408 (lane 5's v5 `:344`), so the pair is proven when it becomes whole. Gating this task on T509 would contradict R38 as lane 3 amended it, under which the callers' lines "land after T502, which accepts the flag, not after T509" (lane 3's phase-B `tasks.md` `:759`, at `8b06dd3d`), and would make a cycle with T509's gate.
- [ ] T404 [P] [US4] [O] Move `lane-worktrees`' three `git grep` reads of `origin/<branch>` behind `register-grep`, and make `canon_lane`'s arm refuse (Y:4926, Y:5739), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-A1) (lands after T401, L2-N4) — files: `lane-worktrees` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and worktrees' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'lane_worktrees'`
  - **Source:** L4:110.
  - **Depends on:** SPEC-PR; T401; PENDING-L2: L2-N4 (its gate, L4:110).
  - **The reads:** the lifecycle events (`lane-worktrees:711`, `:5596`) and the `LANDED` lines (`:1002`) are read through `register-grep`.
  - **On 69,** the sweep deletes nothing, and names the register as unreadable. That is the `returncode not in (0, 1)` branch each site already has.
  - **`canon_lane`** (`:513–524`) refuses 69, 75 and 3, where today it keeps the typed spelling.
- [ ] T405 [P] [US4] [S] Give `lane-handoff`'s `sread` a `69)` arm, and set `LANES_READ_MIRROR=1` on its two naming reads (Y:5736, Y:4928), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-A1) (lands after L2-N4) — files: `lane-handoff` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and lane_handoff' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'triad_lane_tooling_handoff'`
  - **Source:** L4:114.
  - **Depends on:** SPEC-PR; PENDING-L2: L2-N4 (its gate, L4:114).
  - **`sread`** (`lane-handoff:1448`): its `69)` arm re-reads from the mirror and prints the `read: mirror` line.
  - **The naming reads:** `window-lane` (`:1655`) and `swapped` (`:1666`).
- [ ] T406 [P] [US4] [S] Give the handoff skill's `sread` the same `69)` arm, and set `LANES_READ_MIRROR=1` on its two naming reads (Y:5736, Y:4928), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-A1) (lands after L2-N4) — files: `skills/handoff/SKILL.md` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and handoff_skill' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'skill_shell_blocks or handoff_skill'`
  - **Source:** L4:117.
  - **Depends on:** SPEC-PR; PENDING-L2: L2-N4 (its gate, L4:117).
  - **`sread`** is at `skills/handoff/SKILL.md:97`, and its naming reads are at `:210` (`window-lane`) and `:218` (`swapped`). All three are in the skill's step 1.
  - **Its scope** (R25): T406 may edit the skill's step-1 reads. Lane 3 widens the placeholder's "at its step-4 writes only" (skeleton `:618–619`) to the step-1 reads and the step-4 writes, as L4-A1's "the skills' `sread`" and Y:5678's step-1 reads (`SKILL.md:143–243`) need. The skill's commit block stays slice 3's.
- [ ] T407 [P] [US4] [S] Give the restart skill's `lread` its 69 arm under S3 (FR-036), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-A1) (lands after L2-N4) — files: `skills/restart/SKILL.md` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and restart_skill' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'skill_shell_blocks'`
  - **Source:** L4:120.
  - **Depends on:** SPEC-PR; PENDING-L2: L2-N4 (its gate, L4:120).
  - **`lread`** is at `skills/restart/SKILL.md:48`; its calls are at `:117`, `:130` and `:140`. A 69 stops the skill with the read's text. It is never taken as "no lane".
- [ ] T408 [P] [US4] [S] Make `lane`'s default arms pass the code instead of dying 1 (Y:5740), and pass `lanes --decide` in its two `lanes` reads (R38), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-A1) (lands after T502, L2-N4) — files: `lane` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and lane_arms' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'park_resume_commands or openrepotools_command'`
  - **Source:** L4:122.
  - **Depends on:** SPEC-PR; T502; PENDING-L2: L2-N4 (its gate, L4:122).
  - **Lane 3 accepts lane 4's answer** (#165 6742584818; lane 3, fix round B2, 2026-11-09): this task's verify keeps its stub test. T509's verify runs the joint test, parametrized over `lane` and `lane-end --retire-dormant` (Y test-plan.md:2933); selecting it here would gate this task on T509 and make a cycle.
  - **The arms:** the `lanes` reads (`lane:389`, `:406`), `lane-groups` (`:434`), `binding` (`:1072`) and `canon-lane` (`:1318`).
  - **Not here:** `request-handoff`'s arm (`lane:1170–1175`) is a write's arm, so T432 changes it, and only T432.
  - **`lanes --decide`** (R38): `read_rows`' two calls (`lane:389`, `:406`) pass `--decide`, since `lane` acts on the listing and must refuse 69 (Y:3022).
    - **The 64 text:** `lane:411` reads 64 as "bad arguments for the lane read". It is reworded to name a `lanes-edit.sh` too old for `--decide` as well.
    - **Why after T502:** a `lanes-edit.sh` without lane 5's T502 refuses `--decide` with 64 (`lanes-edit.sh:20130` at `34799b74`), so every `lane` listing would die until T502 lands. T502 makes the arm accept `--decide` and ignore it (#165 6726857112, taking lane 3's preference in 6726831985), so the task needs no split.
    - **The 69-refusal test** drives a stub `lanes-edit.sh` whose `lanes --decide` exits 69, in the pattern of v3f's stub `lanes-index` (`test-plan.md:623` at Y). The stub reaches `lane` through `$LANES_EDIT` (`lane:191–198`, which `read_rows` calls at `:389` and `:406`), so the verify needs no T509; the real 69 is T509's own test. Lane 2 confirms the stub in ask A1's round, when it names the selectors.
    - **Why the verify keeps the stub** (lane 4's decision on lane 3's review of phase B, #165 6741485907, item 1, answered and not applied): this verify proves the caller's side with the stub, passing `--decide` and failing closed on 64 and on 69. R38's joint behaviour is proven by the joint test `test_a_caller_that_acts_on_a_listings_answer_refuses_69_where_the_store_is_unreachable` in T509's verify, and T509 lands after T402 to T408 (lane 5's v5 `:344`), so the pair is proven when it becomes whole. Gating this task on T509 would contradict R38 as lane 3 amended it, under which the callers' lines "land after T502, which accepts the flag, not after T509" (lane 3's phase-B `tasks.md` `:759`, at `8b06dd3d`), and would make a cycle with T509's gate.
  - **The `lanes` script** is lane 5's, in T509 (R27).
- [ ] T409 [US1] [O] Add the writers' mode ask and phase 1's mode arm, with F8's table and the "primary: exit 69" placeholder (FR-002, FR-003; Y:4858, Y:4885, Y:4841, Y:5703–5719), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B1, its first half; the writers' arm, lane 4's under R03) (lands after T105, L2-N2, slice 1a) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_build_flip.py -k 'sql_register_build_flip and writer' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'repo_hygiene'`
  - **Source:** L4:131.
  - **Depends on:** SPEC-PR; T105; PENDING-L2: L2-N2; SLICE-1A (its gate, L4:131).
  - **Where the ask runs** (R17): once, for the write guard's verbs, after the workspace check and before 1a's routing (`:17408–17412`). Y:4858 puts it at the dispatcher, after the workspace check, and Y:4983 and Y:5452 say "at the write guard"; at `34799b74` 1a's routing sits between the two.
  - **One variable** (R45; Q-L5-1, accepted): the answer is held in one main-shell variable, `REG_MODE`, set by assignment from the one answer and never read from the environment. `reg_open` (T504) sets the same variable for a reader, since there is one answer per process (Y:5121). Whichever of T409 and T504 lands first defines it.
  - **The carry** (R17; #165 6726831985): T409 adds it, across 1a's `exec` (`:17142`), and slice 1a does not change. The answer travels as `LANES__MODE`, with the `LANES__PID` check that 1a's own re-exec uses, so that a child of another process never inherits it. The routed child sets `REG_MODE` from it only where that check passes, and asks nothing. T409 lands after 1a, which its gate says, since it carries the mode across 1a's `exec`; no 007 code PR lands before the spec PR, and 1a lands before it.
  - **When it is asked.** Once per writer process, and only where `config_path`'s file exists or `LANES_INDEX_CONFIG` is non-empty (Y:5705).
  - **F8's table.** 78 exits 69 with the text. Every other failure runs files mode with a warning. The query's timer is 2 s, and it is `lanes-edit.sh`'s own (contract §3.1, §2.4).
  - **The placeholder.** Primary exits 69 at the ask, before any routing: "this `lanes-edit.sh` has no SQL write path" (Y:4885). T415 replaces it.
  - **`LANES_INDEX=off`** never skips the ask. Under `LANES_REGISTER_SCRATCH` nothing is asked (Y:5038; review M4), and `reg_open` asks nothing there either (Q-L5-1, accepted).
  - **One resolver.** `lanes_index_bin` resolves every `lanes-index` call in `lanes-edit.sh`: `$SCRIPT_DIR/lanes-index` first, then `PATH`, as `index_open` does (`:3750–3752`; review M9). It replaces `index_nudge`'s PATH-only call (`:3714`). Lane 5 accepts it for `reg_open` and the guard's prefetch too, and whichever of T409 and T504 lands first defines it (Q-L5-2, accepted). The contract records it as lane 4's resolver, and closes its O11 so (contract §2.1, §6; #165 6727185616).
  - **The tests' copy rule** (plan §4.1; Q-L2-1): a test that needs the stub copies `lanes-edit.sh` beside it, with the other files `lanes-edit.sh` reads from `$SCRIPT_DIR`: `repos.tsv` (`:3892`) and `lane-start` (`:11498`). Without them it would read no `repos.tsv`, and it would run the `lane-start` on `PATH`, which is the installed one.
  - **The 69 row.** Add the exit table's 69 row, unless an earlier task has landed it (R21).
- [ ] T410 [US4] [O] Add the read verb `act-begin`, with `--rebase` and the client's checks on `LANES_ACT` and `LANES_EXPECT` (FR-035; Y:5567–5609), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-A2, bullets 1 and 2) (STOP until T101; lands after T409, T111, L2-N4) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and act_begin' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'repo_hygiene'`
  - **Source:** L4:142.
  - **Depends on:** SPEC-PR; T101 (STOP until it lands); T409; T111; PENDING-L2: L2-N4 (its gate, L4:142).
  - **On primary,** as T409's ask answers: one `register snapshot` with no identity flags and no `--tree` (contract §3.2), then print `<act-id>@<n>:<generation>` from its `n=` and `generation=`. Exit 69 when the read fails, or when the switch is not `primary` with the marker `present`, which the snapshot's check 4 refuses with 69.
  - **The act id** is 32 lower-case hex digits from 16 random bytes, minted by `act-begin` itself (Y:5575). The snapshot mints none.
  - **Off primary:** print `<id>@files`, touching no database (review M10).
  - **`--rebase`** keeps the id. It refuses with 2 a malformed `LANES_ACT`, a `files` base on primary, and a changed generation.
  - **The helpers** `act_parse` and `expect_parse` split values the bash 3.2 way (Y:5579), under noglob in a subshell (Y:5586). They make the client checks 1–4, with Y:5587's and Y:5601's texts.
  - **Wiring:** the verb joins the unknown-subcommand list (`:21428`). Its dispatcher class is `none`, since it makes its own snapshot: a row in T504's table (Q-L5-5, accepted).
- [ ] T411 [US4] [O] Add `canon-lane --walk <name>`, printing the walk's names (FR-035; Y:5624), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-A2, bullet 3) (lands after L2-N4) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and walk' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'repo_hygiene'`
  - **Source:** L4:149.
  - **Depends on:** SPEC-PR; PENDING-L2: L2-N4 (its gate, L4:149).
  - **Where:** the `canon-lane` arm (`:20367`), over `canon_lane` (`:6735`).
  - **Output:** one name per line, the typed name first and the last hop last.
  - **Exits:** `canon-lane`'s own codes.
  - **Its class:** `decide`, as `canon-lane`'s is: a row lane 5 adds to T504's table (Q-L5-5).

### Implementation for group A: lane 3

- [ ] T301 [US6] [O] Add the renderer's pure function, `lanes-index render --images --at <k> --out <dir>` (S2's journal paragraph; FR-042), as detailed below; its `-k` selector waits for v3f `[J3]` (placement: lane 2's) — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_render.py -k 'sql_register_render and not image_cache' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** SK:134; verify in R28's two-run form.
  - **Depends on:** SPEC-PR; T107 (lane 1's table).
  - It builds every render-set file's image at seq `k`, from the journal's entries, from the latest import's baseline on (§1.3's kinds table: every imported line is a `create` or an `append`). It uses no git and no checkout.
  - The render set is Q18(A)'s: `lanes/LANES.md`, every `lanes/log/<lane>.md` but `lanes/log/README.md`, `lanes/aliases.tsv` and `lanes/archive/*`. `lanes/repos.tsv` and `lanes/STORE` are never images.
  - Each image is byte-identical to a full render, final newline included (`files.final_newline`), as agreement test 1's phase-N part compares against an import.
  - It needs only `SELECT` and `render_file`, which both groups hold (§1.4), and reads with the renderer's row of D9's bounds. A `k` it cannot render, above the head or before the latest import, is refused with the code that `contracts/exit-codes.md` gives it (T102).
  - It writes each file whole, by temp file and rename, into `<dir>`.
  - It runs while the constant is unset: it decides nothing and writes no register.
- [ ] T302 [US4] [O] Add the image cache, which User Story 2's snapshots read too, under `lanes-index`'s state directory, `register-images/` (U8, lane 3's; PC-1, as research.md takes it), as detailed below; its `-k` selector waits for v3f `[J3]` — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_render.py -k 'sql_register_render and image_cache' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** SK:141; verify in R28's two-run form.
  - **Depends on:** SPEC-PR; T301.
  - It holds:
    - `objects/<digest>`, content-addressed and immutable, written by temp file and `os.replace`;
    - `seq/<n>` manifests, path to digest;
    - a `head` file that moves only forward, under a short `fcntl.flock` in `SyncLock`'s form.
  - A reader uses an object only when its name equals the digest the database gave at `n` (D17). A missing object is fetched whole, and a reader copies into its own per-invocation file.
  - Collection runs under the flock. It removes objects that are in none of the last N manifests and are older than 10 minutes (PC-1). Y gives N only as "the last few manifests" (Y:4963), and neither research.md's PC-1 nor any block sets it: PENDING-L3, N's value.
  - It offers one API, which this task fixes: the contract's §1 leaves the image cache's API out of scope, and fixes only what `register snapshot --tree` puts in the tree (L1:85, :503). Lane 1's `register snapshot --tree` (T111) and `register read --prefetch` and form 4 (T113) call it. The service's write-through (T512) uses it too.
- [ ] T303 [P] [US1] [O] Make `openRepoTools wip init` refuse wherever `lanes/STORE`, or any file under `lanes/`, exists, in every mode (Q14(a); FR-005) (U9, lane 3's), as detailed below; its `-k` selector waits for v3f `[J3]` — files: `openRepoTools` — verify: `TMPDIR=/tmp bash tests/run.sh tests/test_wip_init_command.py -k 'wip_init_command' && TMPDIR=/tmp bash tests/run.sh -k 'test_openrepotools_command or repo_hygiene'`
  - **Source:** SK:150; verify in R28's two-run form.
  - **Depends on:** SPEC-PR; it can be coded at once.
  - The check goes in `wip_init`, before its seed commit and before any other write to the workspace repository. It reads the tree that the seed commit would build on.
  - The refusal names the path it found and says that nothing was committed.
  - `GUARD_COMMAND`, `GUARD_TIMEOUT`, `guard_block_text` and the hook entries stay byte for byte (plan.md, "Governed invariants").
  - Lane 2's two cases, from the requirement's two scenarios, are in `tests/test_wip_init_command.py` (placement: lane 2's).
- [ ] T304 [US7] [O] Add `lanes-index rollback`'s transaction, runnable only against a scratch schema while the constant is unset (S5's steps 0 and 1; lane 4's row 9; FR-051), as detailed below; its `-k` selector waits for v3f `[J3]` — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_rollback.py -k 'sql_register_rollback and scratch' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** SK:155; verify in R28's two-run form.
  - **Depends on:** SPEC-PR; T107 (lane 1's functions) and T121 (`outage declare --rollback`).
  - It runs with `LANES_LANE=<operator lane>`, from a shell or a `CLAUDE_NO_LANE=1` session, through that lane's checkout (D24).
  - **Step 0's database half.** It refuses with 2 while `mirror_state.diverged_sha` is set. Inside step 1, `commit_write` refuses a `rollback` entry with LR012. T326 adds the git half: the fresh fetch, and the renderer's check.
  - **Step 1** is one short transaction taking the head row. It sets the switch to `files`, moves the head from `k` to `k+1`, writes the operator entry removing `lanes/STORE`, and leaves the marker `removing`.
    - A 40001, 40P01, 55P03 or LR001 sends it back to step 0, with the head read afresh as its `k`.
    - At D7's deadline it exits 75, with nothing written.
  - **Its start declares the outage,** marked as from a rollback, with `--mirror-seq <k>`, through `outage declare --rollback` (lane 1's T121).
  - **With the database unreachable,** it exits 69 and changes neither the switch nor the marker.
  - **A rerun** that finds the switch at `files`, the marker `removing` and its own entry last declares first, and goes on from step 2.
  - **Against a schema that is not a scratch schema,** while the constant is unset, it exits 2.
  - It calls lane 1's SQL functions, and adds none.

**Checkpoint A** (lane 3's integration):

- Group A has landed.
- `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -rs` passes with no real-engine skip.
- Test 14's refusing state holds.
- Test 1's files-mode twin shows that an unconfigured workstation is unchanged.

---

## Phase 2: Group B, beside group A (phase W of the test plan)

**Goal**: S1's write path at the commit seam, `start-record`, S6's checks,
the direct writes' refusals, the writers' marker check, and what group C
needs from `lanes-edit.sh`.

**Independent Test**: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_writes.py tests/test_sql_register_order.py tests/test_sql_register_in_doubt.py tests/test_sql_register_marker.py -k 'sql_register_writes or sql_register_order or sql_register_in_doubt or sql_register_marker'`
`[J3]`, on a copy of `lanes-index` with the constant flipped (PC-5).

### Tests for group B (write first)

- **PENDING-L2: L2-W1 to L2-W6**, in lane 2's block.

### Implementation for group B: lane 4

- [ ] T412 [US2] [O] [X: backups] Add the write id and the pending-record library (F10; FR-021; Y:5169–5224, Y:2466–2502), and write `contracts/pending-record.md`, as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B2; `contracts/pending-record.md` is lane 4's under R34) (STOP until T101; lands after 1a, 1b, T502, L2-W3) — files: `lanes-edit.sh`, `../spec/specs/007-sql-register/contracts/pending-record.md` — verify: `test -s ../spec/specs/007-sql-register/contracts/pending-record.md && command grep -q 'call=' ../spec/specs/007-sql-register/contracts/pending-record.md && command grep -q 'register-done/' ../spec/specs/007-sql-register/contracts/pending-record.md && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_in_doubt.py -k 'sql_register_in_doubt and (pending_record or claim_chain)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'repo_hygiene'`
  - **Source:** L4:157.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T101 (STOP until it lands); T502; PENDING-L2: L2-W3 (its gate, L4:157; group B's gate, L1:531).
  - **`wr_state_dir`** is defined above the file-scope resolver, before `:1160` (review m3). It mirrors `state_dir()` (`lanes-index:173–175`), with a hygiene test that the two agree; lane 1 adds that test's `lanes-index` side (contract §7, Q5).
  - **The id:** a new write's first snapshot passes `--write-id new` and takes the minted id from `write_id=` (contract §3.2, §4; review M13). The claim file is taken before that snapshot, and the record is written after it, once the generation is known (Y:5192).
  - **`LANES_WRITE_ID` without `LANES_WRITE_GENERATION`** (the contract's O4): the generation is taken from the pending record that holds that id for this command line. With no such record, the run refuses with 2 before any call, naming the printed rerun's form (Y:5200). So `lanes-index`'s 64 for an id without a generation is never reached from `lanes-edit.sh`.
  - **The digest:** `wr_digest` is the sha256 of `lanes-pending/1 NUL lane NUL session NUL verb NUL args…`, over `ORIG_ARGV` (`:741`).
  - **The record:** written by temp file and `mv`, mode 0600 in a 0700 directory, with Y:5172's keys and the `call=` lines.
  - **The claims:** a claim chain of `<d>.take.<k>` files. Another host's claim gets 120 s.
    - **The owner's liveness** is `pid_start_of <pid>`, lane 5's T502 (L5-A1; Q-L5-6, answered). Exit 0 is live, with the start token on stdout, compared as a string on the same host and container. Exit 1 is not live. Exit 2, a start that cannot be read, counts as live (R49), so a wrong "dead" never lets a claim be stolen.
    - Each claim holds the run's pid, start time, host, container and UTC (Y:5184). The 3 for a claim with no record beside it prints them (T417; open point 42, taken under R26).
    - **The selector:** T412's `-k` word is `claim_chain`, not `claim`, so that it does not select AT-F10's record-less test, which needs T417. That test is T417's, through `no_record`. L2-0e's names replace both.
  - **`updated_utc`** is rewritten with every `call=` line, before the call is sent. It is always the host's clock (`date -u`), never the snapshot's, even though `utc_now` answers the snapshot's clock in the child (T413). A takeover ages the newest line's clock by it (T417; open point 36, taken under R26).
  - **The age** (`LANES_REGISTER_PENDING_MAX_AGE_S`, 15 minutes):
    - a record of this generation past its age is not reused: the run warns, prints the lookup, and writes anew (Y:5207);
    - a record of another generation is refused at any age, until `LANES_WRITE_ID=new` (Y:2487);
    - only done marks are removed by age (Y:5183). No record is purged.
  - **Reruns:** `LANES_WRITE_ID=new`, and another generation's refusal text with `LANES_WRITE_GENERATION` (Y:5209–5215).
  - **Not here:** the live-owner exit 3 (Y:5218) moves to T417, with the exit-3 rule (review M8).
  - **After 1a and 1b** (R23): T412 touches no 1b function, but it is inert until T415, and landing it early would move `main` under 1a and 1b.
- [ ] T413 [US2] [O] (1b) Honour `LANES_REGISTER_SCRATCH=<attempt dir>`, the internal flag (F22; Y:5032–5044), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B3, the child; L4-B7, `gh_reads`; L4-B11, D12 and D13) (STOP until T101; lands after T412, T504, L2-W1) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_writes.py -k 'sql_register_writes and scratch' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'a23_write_routing'`
  - **Source:** L4:175.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T101 (STOP until it lands); T412; T504; PENDING-L2: L2-W1 (its gate, L4:175; group B's gate, L1:531).
  - **When it is honoured:** only for a directory under `<state>/register-writes/`, owned by this uid with mode 0700, whose `parent.pid` names `$PPID`. The value is copied, then unset.
  - **The directory's name** is `<state>/register-writes/<d>/<run>/<k>/`. `<d>` is the record's digest, `<run>` is 32 lower hex from 16 random bytes minted when the run starts, and `<k>` is the attempt. All three are known before the first snapshot, which needs `--tree <dir>/tree` and mints the write id. The `call` line names its directory (Y:5173), so nothing reads the id from the path.
  - **The resolver:** `LANES_DIR` and `LANES_FILE` are unset, so `LANES_REPO=<dir>/tree` derives both (`:1226–1234`). `<dir>/tree` is the tree `register snapshot --tree` built, which `register commit` also reads (contract §3.3).
  - **The directory's own files:** `parent.pid`, `seam.fifo`, `ready`, `out.1`, `out.2`, `seam.out`, `lock` and `preimages/` sit beside the files `register commit` reads. The contract reads only §3.3's names and ignores every other, so it never refuses them as "extra" (CO-1, taken in draft 2; contract §3.3).
  - **What the flag skips:**
    - the routing re-exec (`:17408–17412`);
    - the mode ask, and the read door, which ask nothing and start no `lanes-index` (Y:5038, Y:5041; review M4). `reg_open` asks nothing, prefetches nothing and makes no marker check under the flag (T504; Q-L5-1, accepted);
    - `checkout_sync` (`:2528`), which returns 0, so S3 and `cs_recheck` never run. A dirty pathspec exits 2, "nothing was sent" (Y:5078);
    - `log_sync` (`:7326`), which returns with `LOG_SYNC_FETCH=yes`.
  - **One clock:** `utc_now` (`:3956`), `older_than_minutes` (`:5407`) and `older_than_threshold` (`:5412–5415`) all read the snapshot's clock, through one `now_epoch` (D13, Y:528–534; review m6).
  - **The host clock stays where it decides no register state:**
    - `age_of` (`:5311–5314`) prints an age in a listing;
    - `:9467` dates a listing's partition, in a read;
    - `:10342` is `fetch-age`, a local git fact;
    - `:19605–19608` is `request-handoff`'s poll deadline, a local wait that runs in the tail, after the commit.
  - **Caches and locks:**
    - `gh_reads` (`:12612`) is cached in the write's directory (Y:5043);
    - `sidecar_lock_dir` (`:1500`) returns the attempt directory's `lock`, which holds the path of the workstation's sidecar lock (F-B1). Row 43's lock is that lock, as at 1b, and X's lines are `research.md` errata, so this text is unchanged (R19);
    - `REG_MODE` is `primary` in the child (Y:5121; R45). T425's and T426's one line sets `rule6_primary_on` and `key()`'s variable from it, in the child as in the parent (Q-L5-4).
- [ ] T414 [US2] [O] (1b) Add `commit_publish`'s primary branch, the seam, with the FIFO handshake, its cap and `seam.out` (D4; FR-019; Y:5054–5097), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B4; L4-B3, the handshake, cap and `seam.out`; L4-B8, one run, one entry) (STOP until T101; lands after T413, T112, T505, L2-W1) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_writes.py -k 'sql_register_writes and seam' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'repo_hygiene'`
  - **Source:** L4:195.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T101 (STOP until it lands); T413; T505; PENDING-L2: L2-W1 (its gate, L4:195; group B's gate, L1:531).
  - **Kept:** `commit_publish` (`:3092`) up to its `git add` (`:3115`).
  - **Replaced** (`:3122` onwards, under the flag):
    - an empty `diff --cached` returns 0, with no handshake;
    - a staged path outside the pathspecs (plus `LOG_RENAMED_FROM`), or outside the render set, exits 2;
    - the seam writes the files of contract §3.3, then `ready`:
      - `changes.tsv`, as `<status>` TAB `<path>` TAB `<base blob>` TAB `<new blob>`, from `git diff --cached --no-renames --raw --no-abbrev HEAD`. `--no-abbrev` is added because Y:5061's form abbreviates the ids. A status other than `A`, `M` or `D` exits 2;
      - `renames.tsv`, one `<from>` TAB `<to>` line, where `LOG_RENAMED_FROM` (`:3100`) is set (the contract's O5);
      - `images/<path>` for every `A` and `M` path, from the index;
      - `msg`, holding `$msg` verbatim;
      - `files`, the canonical rows, `<path>` TAB `1`/`0` TAB sha256 or `-`, sorted with `LC_ALL=C`. Its sha256 over the LF-ended rows is the `call` line's `<files>` (contract §3.2, the one digest rule).
  - **The handshake:**
    - the FIFO on fd 5, opened read-write;
    - `read -t 1 -u 5` turns, each testing `pid_start_of` against `parent.pid`, whose exit 2 counts as live (R49);
    - a cap of 2×(D+30 s), set by `LANES_REGISTER_SEAM_CAP_S`;
    - `seam.out`, written under noclobber;
    - at the cap, 75, with Y:5006's line on fd 4.
  - **On `committed <s>`:** restore fds 1 and 2, replay the output, set `REGISTER_SEQ`, flush the read family with lane 5's `reg_flush` (T505; Q-L5-4, answered), and return 0. On `abandon`, no tail runs.
  - **One run, one entry:** a second register reach exits 2 (Y:5097). T422's handoff commit is not one: the parent makes it after `committed`, outside the flag, as a git commit that is not a register write (R18).
  - **Each verb:** every verb of Y:5084–5091 reaches `ready` on a scratch tree, except `rename-lane` and `commit` (review m4).
  - **The exit table** gains its 75 row, which is T414's (R21).
- [ ] T415 [US2] [O] (1b) Replace T409's "primary: exit 69" with the primary branch, once per writer process: S1's attempt loop until D7's deadline (FR-018; Y:4981–5030, Y:2504–2646), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B1, the branch; L4-B3, the parent's decision, the run's end and D7's 75; L4-B7, the replay; L4-B13, the author; L4-A2, bullet 4) (STOP until T101; lands after T414, T111, T112, T114, L2-W1, L2-W2, L2-W4) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_writes.py tests/test_sql_register_order.py -k '(sql_register_writes or sql_register_order) and (attempt or first_snapshot)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'repo_hygiene'`
  - **Source:** L4:216.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T101 (STOP until it lands); T414; PENDING-L2: L2-W1; PENDING-L2: L2-W2; PENDING-L2: L2-W4 (its gate, L4:216; group B's gate, L1:531).
  - **`commit` stays out of the loop.** On primary, `commit` keeps T409's "exit 69" until T420 lands, so no state between T415 and T420 runs it in the loop.
  - **The parent's steps on primary** (R17; FR-018, lane 3's 007 `spec.md:1104–1107` at `8b06dd3d`, where FR-018 starts at `:1095`, as #165 6741485907 cites it): no `lane_checkout_enter`. `<c>` comes from `lane_checkout_path` (`:16649`), with no S3 work. Where `<c>` does not exist, the run refuses with 2, naming `lane-worktrees`, since a clone there would be D12's fetch. `commit`'s publish form is the one exception: it keeps 1a's routing (T420; R16 (c)).
  - **Before the first attempt:**
    - a pending hand edit refuses 2, with Y:2459–2464's recovery. It is looked for in the lane checkout, with `cs_pending_of` (`:2417`);
    - the client checks 1–4 run (T410);
    - the write id and the record are made (T412).
  - **Each attempt:**
    - its directory, `<state>/register-writes/<d>/<run>/<k>/` (T413), and `parent.pid`;
    - `register snapshot --tree <dir>/tree --checkout <c>` with the write's identity, or `--write-id new` on a new write's first. It returns `write_id=` and `not_after=` (contract §3.2; review M13). A `format=` other than 1 is a failed read, 69;
    - the act's checks at the snapshot (Y:5603–5605);
    - the files the parent writes for `register commit` (contract §3.3): `snapshot` (the stdout, verbatim), `not_after`, `author`, and `act` (`id=`, `base=`, `generation=`, one `expect=` per scope) inside an act. The `act` file makes an act's write that a rival changed refuse with 2 and LR009, against the fixture (L4-A2, bullet 4);
    - `author` is `<c>`'s identity, as `git -C <c> var GIT_AUTHOR_IDENT` gives it without its date, `Name <email>` (Y:5379; D15). Where git can give none, the run refuses with 2 before the first snapshot, as a files-mode commit would fail;
    - the child, `"${BASH:-bash}" "$RESOLVED"` with `ORIG_ARGV`, under the flag;
    - the call: `register commit --write-id --generation --lane --verb --args-sha256 --workstation <dir>`. `--workstation` is the name the lines are stamped with (`LANES_WORKSTATION`, else `:891`'s fallback). `--lane <lane>` keeps its space form: L1-D1's `=` form is `register read`'s alone, and a lane on the write path is always in `check_lane_name`'s class (contract §3.6, L1-D1; #165 6738871108).
  - **The decision** follows the contract's caller map (§3.3; Y:5008–5027):
    - 0 with `committed <s>`, or `already <s>` with its rows;
    - 75 abandons and drops the `call` line;
    - 2 (LR005 with its token and any `held-by=` line, LR007, LR009, LR010) and 69 end the run, relaying stderr's `ERROR:` and `DETAIL:` lines;
    - 3, a crash, or any code outside the contract's set is in doubt (contract §2.7, O8). At the commit, 69 means only a definitive refusal; a connect failure is `psql`'s exit 2, so 3 (Y:2620–2621; review M7). `psql`'s exit 1, its death by a signal, and an exit 0 whose answer cannot be read reach group B as 3 as well: that is `lanes-index`'s mapping, as CO-6 decides it (contract §3.3, with §2.2 and §2.7; R01; lane 1's wording, #165 6741670709);
    - 64 means an internal error with nothing sent (CO-5, taken in draft 2; contract §2.7): the run exits 2.
  - **A 69 at the first snapshot** (open point 38, taken under R26; Y's spec:318 and :338 name the generation where the write has an id, and say that nothing was sent where it has none). What the note says depends on whether the run had an id:
    - **A run that passed `--write-id new`,** having been given no `LANES_WRITE_ID` id and having taken over no record:
      - no id was minted, since the mint is at that snapshot;
      - no record was written, since it comes only after that snapshot (Y:5192).
      - Its printed rerun is the command as it was given: no `LANES_WRITE_ID` or `LANES_WRITE_GENERATION`, or `LANES_WRITE_ID=new` again where that was given, since the record it is to replace is still there.
      - The note says that nothing was sent (spec:318).
    - **A run that had an id** prints it again with its generation: `LANES_WRITE_ID=<id> LANES_WRITE_GENERATION=<g> …`. That covers an id given in `LANES_WRITE_ID`, as by an earlier printed rerun, which "settles the write with or without the record" (Y:5219, Y:5222), and an id from a record it took over. A plain rerun there could mint a new id and make a committed write twice.
    - **The claim file:** this 69 also removes the run's own claim file (`<d>.take.<k>`, taken before the snapshot; T412). So no claim is left with no record and a dead owner.
  - **The deadline:** D7's 30 s (`LANES_REGISTER_DEADLINE_S`), ending in "register busy", 75 (Y:2639–2641).
  - **The output:** only the deciding attempt's is replayed (F14; FR-024).
- [ ] T416 [US6] [O] After `committed`, fork the renderer and suppress the register's nudge (FR-027; Y:5375–5381; contract §3.7), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B13, the fork, `<c>` and the nudge; L4-B1, the fork) (STOP until T101; lands after T415, T321, L2-W1) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_writes.py -k 'sql_register_writes and render_fork' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'lanes_index'`
  - **Source:** L4:247.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T101 (STOP until it lands); T415; T321; PENDING-L2: L2-W1 (its gate, L4:247; group B's gate, L1:531).
  - **The fork:** `( "$(lanes_index_bin)" render --checkout <c> & )`, detached, with stdin and fds 3–9 closed, and `LANES_NO_FETCH` removed. `LANES_INDEX=off` (`:3713`) never skips it.
  - **`<c>`** is the one T415 takes from 1a's `lane_checkout_path` (`:16649`), without S3 (R17).
  - **The author** is not here: `register commit` reads it, so T415 writes it before the call (Y:5379: "passed to `register commit` and stored in the entry").
  - **The nudge:** `index_nudge`'s (`:3710`) register `sync` is suppressed on primary. Its inventory nudge stays.
  - **Its order** (lane 3's PB4, #165 6741485907): T416 lands after T321, as w10 has it. The fork does nothing until T320's push and T321's lock exist, and there is no cycle: T320 waits for T415, T321 for T320, and T416 for T321. Group C starts on T415 and T344.
- [ ] T417 [US2] [O] [X: backups] Add the in-doubt rule, F3 through `journal`, with exit 3's primary clause (F3, D8; FR-022, FR-028; Y:5226–5287, Y:2648–2748), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B5, L4-B15) (STOP until T101; lands after T415, T114, L2-W3) — files: `lanes-edit.sh`, `tests/test_repo_hygiene.py`, `../spec/docs/README-lanes.md` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_in_doubt.py -k 'sql_register_in_doubt and (lookup or call_line or exit_3 or takeover or no_record)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'repo_hygiene'`
  - **Source:** L4:253.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T101 (STOP until it lands); T415; PENDING-L2: L2-W3 (its gate, L4:253; group B's gate, L1:531).
  - **The call line:** `call=` is written before each commit call, and dropped only on 75, a definitive 69, or 2.
  - **A taken record:** a run that takes a record holding `call` lines is in doubt from its start.
  - **At every end but 0:** `journal --write-id --not-after --generation --lane --verb --args-sha256 --clock --clock-age` (contract §3.4). `--not-after` is the largest of the `call` lines' and `--clock` is the last snapshot's `clock=`, with `--clock-age` from bash's `SECONDS`. `journal` makes the wait and the tries itself, and the run sets no timer around it. `--lane <lane>` keeps its space form, as in T415 (contract §3.6, L1-D1; #165 6738871108).
  - **A takeover whose own snapshot failed** (open point 36, taken under R26) has no snapshot clock of its own. It takes the record's:
    - `--clock` is the clock of the `call` line with the largest `not_after`;
    - `--clock-age` is the whole seconds since the record's `updated_utc`, on this host.
    - That estimate can only lag the database: the line was written after its snapshot ended. So `journal` waits until `<n>` is at most 2 s ahead, LR011 is not reached, and the wait the record exists for (Y:5178, Y:5199) takes place.
    - Only a run with neither clock calls at once (Y:5236): a record written on another host, whose `host` is not this one. LR011 then gives 3, with the record kept.
  - **The tries are `journal`'s** (contract §3.4; lane 1's T114), and T417 sets no timer around them. Lane 1 took open point 40's reading, which lane 3 took and which needs no text (R26), for T114 and contract §3.4 (Q-L1-1, taken in #165 6727185616): each try starts at least 1 s after the last try's start, and never before it has ended; any try may start up to and including the window's 10 s mark; the window is kept on a monotonic clock finer than a second (plan §11). Lane 1 took open point 36's `--clock` and `--clock-age` from the record the same way (contract §3.4; v4.2 T114).
  - **A claim with no record beside it** (open point 42, taken under R26), whose owner still lives at D7's deadline, ends with 3, as for a live owner (Y:5218). It has no write id to name, so it names the claim and gives Y's own effect check (Y:5211–5213):
    - **The text:** "an identical write is being started in pid `<p>` on `<host>`/`<container>` (claimed `<utc>`); its record is not yet written, so its write id is not yet known; this run sent nothing. Once pid `<p>` has ended, look for its effect in the register: `lanes-edit.sh history <lane> --since <utc>` for a log line, or `lanes-edit.sh register-row <lane>` for a row. If it is there, that write was made: do not run this command again. If it is not, run the same command again; it takes over that write's record if one was left, or writes anew."
    - **Why not a plain rerun:** if the other run finished with 0, its record is gone and its done mark is keyed by write id, so a plain rerun would write anew (Y:5182).
    - **Why not the done mark:** finding the other write's id from its done mark would rest on a pid with no start time (Y:5181 records pid, host and container), and the mark carries no digest, so it could not show the mark is this command's.
    - **A run that was itself under `LANES_WRITE_ID`** prints its own id again with its generation, not the plain command.
  - **Reading its answer:** `found <seq>` with rows, or `absent`, exit 0. A 2 counts as LR007 only with stderr's `ERROR:  LR007:` and a `DETAIL:` token; any other 2, a crash, or a code outside the contract's set is in doubt, 3, with the record kept (CO-2, CO-3). Draft 2 took both (contract §2.7, §3.4), but for Q6(c)'s 2 on `journal --write-id`, which no primary write can precede, and which this rule reads as 3 in any case.
  - **The outcomes:**
    - found: 0;
    - absent with the lock held: the last attempt's code;
    - LR007 `generation`: 2, keeping the record;
    - LR007 `another-write`: 2, removing it;
    - LR011, or no lock: 3, naming the id and printing the rerun.
  - **Exit 3:**
    - the live-owner 3 (Y:5218, moved here from T412);
    - exit 3's text (Y:2661–2665);
    - the exit table's row 3, its primary clause, which is T417's (R21; Y:468–469; the row's header is at `lanes-edit.sh:524`);
    - each new `die … 3` mapped in `_EXIT_3_CAUSES` (`tests/test_repo_hygiene.py:2794`), in the same PR as that `die`, a hunk lane 2 reviews (R22; Q-L2-3 is (a), #165 6726725691).
  - **The manual:** its exit-3 row (`../spec/docs/README-lanes.md`) changes in the same landing. The exit-3 test reads it in a composed run (`doc()`, `tests/test_repo_hygiene.py:35–39`; `conftest.py:268–275`). T417 lands that row as a spec-leg PR beside its code PR, and T340 keeps it when it assembles the manual (R21).
- [ ] T418 [US2] [O] Add the tail, once: the kept child, the replay at `s−1`, the `tail` key and the owed texts (S3, row 24; FR-023; Y:5289–5373), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B6) (STOP until T101; lands after T417, T111, T112, L2-W3) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_in_doubt.py -k 'sql_register_in_doubt and tail' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'repo_hygiene'`
  - **Source:** L4:281.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T101 (STOP until it lands); T417; PENDING-L2: L2-W3 (its gate, L4:281; group B's gate, L1:531).
  - **The kept child** is matched by the `files` digest (Y:5300; contract §3.2's one digest rule), computed over the `file=` rows that come with `already <s>` (contract §3.3), `found=` (§3.2) or `found <s>` (§3.4). `already <s>` with no rows means no match, so the tail replays.
  - **The replay:** `register snapshot --tree --at <s−1>` with the write's five identity flags, of which only the generation is checked under `--at` (contract §3.2). It runs with the committing call's clock and `preimages/` (`lane_state_preimage`, `:15131`). A replay runs no `guard_type`.
  - **The `tail` key:** `sent`, `done` or `owed`.
  - **The texts:** the owed text, and the no-record text (Y:5328–5347).
- [ ] T419 [US1] [O] (1b) Add the writers' marker check after S3's fetch and in the publish act, with agreement test 3's unit form (FR-004; spec:41–57), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B12) (lands after 1b, T409, L2-W1) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_marker.py -k 'sql_register_marker and writer' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'a23_publish_replay or repo_hygiene'`
  - **Source:** L4:286.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T409; PENDING-L2: L2-W1 (its gate, L4:286; group B's gate, L1:531).
  - **Only off primary.** The check runs only where T409's mode ask did not answer `primary`: with no configuration, in files mode, and in `index` mode. spec:41 heads the requirement "A writer that is not `mode=primary` refuses under the marker", FR-004 says "A files-mode writer", and F17 runs "only when the mode is not `primary`" (Y:4946). On primary `lanes/STORE` is always on origin, so an unconditional check would refuse the publish act that T420 keeps, and T422's handoff move.
  - **Where:** in `checkout_sync`, after step 1's fetch (`:2558–2603`) and before step 2 (`:2604`), it tests the fetched tip for `lanes/STORE`. `cp_publish_act` (`:3577`) makes the same check.
  - **The 69 row:** T419 adds the exit table's 69 row if T401 and T409 have not landed it (R21), since `test_repo_hygiene.py:2735` fails on a `die … 69` with no row.
  - **Under `LANES_NO_GIT=1`:** it tests `$LANES_DIR/STORE` in the working tree, as F17 does (Y:4946). spec:42 asks every act-2 writer to read `lanes/STORE` in every mode; the refusal is for writers off primary (review m7).
  - **Under the marker:** 69, with spec:42's text, writing nothing.
  - **The test:** test 3's unit form commits the marker by hand on a bare origin.
- [ ] T420 [US2] [O] (1b) Add D10's refusals on primary, keeping the publish of queued commits with its fence narrowed to `handoffs/` (FR-026; Y:508–514; spec:169–185), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B11, D10) (lands after T415, L2-W1) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_writes.py -k 'sql_register_writes and direct' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'commit_push_published_edit_194 or a23_publish_replay'`
  - **Source:** L4:293.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T415; PENDING-L2: L2-W1 (its gate, L4:293; group B's gate, L1:531).
  - **The ruling** (R16): FR-026 follows spec:169. On primary `commit` refuses only its carry form; the publish act stays, fenced to `handoffs/`, and a queued commit under `lanes/` is refused as a divergence.
  - **Refused:** `commit`'s carry form (`:17774–17776`) refuses 2.
  - **Kept:** the publish form (`:17777–17778`) runs outside the attempt loop. `cp_publish_fence` (`:2970`) is narrowed to `handoffs/`, and a queued commit under `lanes/` refuses as a divergence. The primary branch exempts `commit` from the loop (review M2).
  - **Its fetch** (R16 (b)): D12 governs register writes. The publish act is a git act whose paths are all under `handoffs/`, so it keeps S3's fetch.
  - **Its checkout** (R16 (c)): on primary, `commit`'s publish form keeps 1a's routing, `lane_checkout_enter` included, since it needs `LANES_LANE`'s `<c>`. T415's skip of `lane_checkout_enter` (R17) does not apply to it.
  - **The branch-wide push** (spec:183–185; R16 (d)). At 1b, no write but the publish act can push a queued commit:
    - S3's step 5 refuses every mode except `publish` over unpublished commits (`:2649–2656`).
    - So `commit_publish`'s own push, `refs/heads/<b>:refs/heads/<b>` (`:3160–3165`), sends exactly the write's one commit.
    - The publish act, `cp_publish_act` (`:3577`), runs only when nothing is staged and `CP_PUBLISH_AHEAD=1` (`:3122–3125`), and only `commit` sets that (`:17801`). It pushes only its fenced tip, `CP_FENCE_TIP` (`:3597`).
  - **What reaches `:3165` on primary.** Verbs stop at the seam (T414), and the carry form is refused above. The one commit that still pushes at `:3165` on primary is T422's handoff move, which `commit_publish` makes with staged paths, outside the flag.
  - **The backstop:** at `:3160`, on primary and outside the flag, T420 refuses the push with 2 unless every path the pushed commit changes is under `handoffs/` (`git diff --name-only` from the origin tip to the commit). T422's move passes, and anything else is refused.
  - **Nothing to refuse:** `add-row`'s compensating commit and the captures do not exist at 1b ("compensat" matches nothing in `lanes-edit.sh` at `34799b74`).
- [ ] T421 [US3] [O] (1b) `claim` and `release` on primary: `register seq`, and CLAIM-LOST unreachable (FR-031; Y:5066–5076), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B8, `claim`'s seq) (STOP until T101; lands after T418, T112, L2-W2) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_order.py -k 'sql_register_order and (claim or release)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'repo_hygiene'`
  - **Source:** L4:306.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T101 (STOP until it lands); T418; PENDING-L2: L2-W2 (its gate, L4:306; group B's gate, L1:531).
  - **`claim`'s sha line** (`:18999`) prints `register seq <s>` from `REGISTER_SEQ` (Q19(A)).
  - **CLAIM-LOST** is asserted unreachable under the flag: `claim_rescan_hook` (`:12695`, called at `:18796–18797`) and `claim_lost_write` (`:12792`) fire only through 1b's re-run.
  - **The comments:** `claim`'s `gh_comment` (`:19004`) and `release`'s (`:19053`) run only in the tail.
- [ ] T422 [US2] [O] (1b) `rename-lane` on primary under Q13(a): the transaction first, then the handoff's move and stamp outside the flag (Y:3444–3451, Y:5092; spec:368–376), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B8, `rename-lane`'s tail and its seq) (STOP until T101; lands after T418, T112, L2-W1, L2-W3) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_writes.py -k 'sql_register_writes and rename' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'rename_publication_atomic_158'`
  - **Source:** L4:310.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T101 (STOP until it lands); T418; PENDING-L2: L2-W1; PENDING-L2: L2-W3 (its gate, L4:310; group B's gate, L1:531).
  - **The transaction:** the handoff paths leave the change set. The row's handoff cell is rewritten as if `rl_hdone=1`, and the stamp's checks run before the seam.
  - **After COMMIT** (R18): the parent makes the handoff's move and stamp after `committed`, outside the flag, as an Amendment 23 S3–S4 commit in `<c>` that is not a register write (review M5). It is made as `rename-lane` makes its commit today, by `commit_publish` with its paths staged (`:18540`). So it pushes at `:3165`, and the push carries only this one commit, since S3's step 5 refuses any queue under it (`:2649–2656`). T420's backstop lets it through, because all its paths are under `handoffs/`. For the same reason it keeps S3's fetch on primary, since D12 governs register writes (R16 (b)).
  - **The lock:** `lane_state_rename` (`:18547`) runs under `sidecar_try`. It takes no lock of its own, and `:18548` releases the scratch tree's (review m2). That lock is the workstation's sidecar lock, as at 1b (R19).
  - **The output:** `rl_sha` (`:18551`) prints `register seq <s>`. The in-file text for 3 becomes D8's "may not be written".
- [ ] T423 [US2] [O] `request-handoff` on primary: its tail after `committed`, with no `guard_type` in a replay (Y:5090; spec:257–259), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B7, F14's tails) (lands after T418, T504, T506, L2-W3) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_in_doubt.py -k 'sql_register_in_doubt and request_handoff' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'repo_hygiene'`
  - **Source:** L4:315.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T418; T504; T506; PENDING-L2: L2-W3 (its gate, L4:315; group B's gate, L1:531).
  - **`guard_type`** (`:19591`) runs only in the tail.
  - **The poll** (`log_sync`, `:19618`) reads D17's path after `committed`, through lane 5's read door: `reg_open` (T504), and `log_sync` with no fetch on primary (T506). Without T506 the poll would fetch on primary, which D12 forbids, so T423 lands after T506 (#165 6726831985, 6726857112).
  - **Exit 2** where none of its tail ran.
- [ ] T424 [US3] [O] On primary only, `append-line` refuses three texts with 2, before the lock, as classified by `lanes-index`'s one classifier (Y:5114–5116, Y:3396–3400), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B10, the refusals) (lands after T415, T122, L2-W2) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_order.py -k 'sql_register_order and append_line' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'repo_hygiene'`
  - **Source:** L4:319.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T415; T122; PENDING-L2: L2-W2 (its gate, L4:319; group B's gate, L1:531).
  - **The three texts:**
    - a taking line that names no repository;
    - a take with no readable UTC;
    - a `LANDING EXPIRED` typed by hand.
  - **The classifier:** `register classify` (contract §3.5): stdin lines in, `<kind>` TAB `<slot_role>` TAB `<repo_key>` TAB `<utc_ts>` out, `-` for NULL. No database call. It is lane 1's new T122, `[O]`, in group A, with a parity test against `parse_register_line`, and this task lands after it (R04).
  - **Where:** in `append-line` (`:17597`), beside its newline (`:17607`) and managed-vocabulary (`:17613`) refusals.
  - **Files mode** is byte-identical.
- [ ] T425 [US3] [O] Add `append-line --landing-expired <holder> --pr --repo --word --word-where --word-utc`, with `RULE6_PRIMARY` and `RULE6_EXPIRED_AWK` (F13; FR-032; Y:5099–5142), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B10) (STOP until T101; lands after T424, T415, T112, T118, T504, L2-W5; the `who --landing` test lands after T505) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_order.py -k 'sql_register_order and landing_expired' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'repo_hygiene'`
  - **Source:** L4:327.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T101 (STOP until it lands); T424; T415; T118; T504; PENDING-L2: L2-W5; T505 (the `who --landing` test) (its gate, L4:327; group B's gate, L1:531).
  - **Refusals:** before the lock, as Y:5114 lists.
  - **The line:** its fields are copied from the holder's newest open `LANDING` at `n`. Its age is dated by `utc_now` against a 30-minute window.
  - **Output:** it prints after the seam. A refusal relays LR005's `DETAIL:` token, `expired-none`, `expired-closed` or `expired-young` (Y:1464, Y:1559; contract §2.6), and branches on the token alone.
  - **Off primary** it exits 2 (Y:5140).
  - **The awk:** `RULE6_PRIMARY`, `rule6_primary_on` and `RULE6_EXPIRED_AWK` (Y:5120–5138) go beside `RULE6_AWK` (`:7638`).
  - **The one line** (R45; Q-L5-4, answered): `reg_open` sets only `REG_MODE`. One line after both doors and before the arm, where Y:5121 places it, sets `rule6_primary_on` and T426's `key()` variable from `REG_MODE`, so that the writers' branch, the child and a reader all take it. It ships with T425 and T426, which own those variables (review m5). **T425 adds it,** setting `rule6_primary_on`, and T426 adds `key()`'s variable to the same line, so T426 lands after T425 (lane 4's decision, which is lane 3's default in its review of phase B, #165 6741485907).
- [ ] T426 [US4] [O] On primary only, `RETIRED_ROWS_AWK`'s `key()` trims a number's leading zeros as text (Y:6574–6577), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers the routed item at Y:6574–6577, lane 4's on primary only under R31) (lands after T409, T504, T425, L2-N4) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_readers.py -k 'sql_register_readers and retired' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'repo_hygiene'`
  - **Source:** L4:334.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T409; T504; T425; PENDING-L2: L2-N4 (its gate, L4:334; group B's gate, L1:531).
  - **On primary:** `key()` (`:4907–4912`) trims with `sub(/^0+/, "", p); if (p == "") p = "0"`, through an awk variable that R45's one line sets from `REG_MODE` (R45; Q-L5-4, answered). T425 adds that line, and T426 adds this variable to it, so T426 lands after T425 (lane 4's decision; #165 6741485907). So the writers' branch, the child, and a reader whose `reg_open` (T504) answered `primary` all trim.
  - **The readers it reaches:** besides the writers, `retired-identity` (`:20299–20305`) reaches `RETIRED_ROWS_AWK` through `retired_identity_hits` (`:4924–4932`). Y:6575–6577 asks that the read and v2.5's keys agree, so that read trims too.
  - **Files mode** keeps `(p + 0)` (`:4911`), so nothing changes where the marker is absent (spec:59; review m7).
- [ ] T427 [US2] [O] (1b) Add the write verb `start-record` in both modes: its arm, its message and its exits table (FR-025; Y:5521–5555), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B9, the verb) (lands after 1b, T410, L2-W1; its primary test after T415) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_writes.py -k 'sql_register_writes and start_record' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'a23_write_routing or repo_hygiene'`
  - **Source:** L4:338.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T410; PENDING-L2: L2-W1; T415 (its primary test) (its gate, L4:338; group B's gate, L1:531).
  - **The call:** `start-record <L> <STARTED|RESUMED> { --add-row <row> | [--append-cell <anchor> <entry> <why>]… --state "<phrase>" } --log "lane:<L>" '→' "<payload>"`.
  - **The body:**
    - one lock, and one `checkout_sync … write`;
    - the edits in today's order, leaving out (and saying so) an anchor that does not hold;
    - one `commit_publish` of `LANES.md` and the log, with Y:5536's message;
    - then `lane_state_follow` (`:7287`), after the lock.
  - **Wiring:**
    - the verb joins the write-guard list (`:17357`) and the unknown-subcommand list (`:21428`);
    - a `start-record)` arm in `write_lane_of` (`:16860–16943`) prints its first positional, so `LANES_ROUTE_WRITES=1` routes it (review M6);
    - its dispatcher class is `writer`, the sixth class, which holds the write guard's verbs (R46): a row in T504's table (Q-L5-5).
- [ ] T428 [P] [US2] [O] Move `lane-start` onto `start-record`, with its act base, its re-base at the second `binding` read and its write arms (FR-025, FR-029; Y:5627–5657, Y:5539–5544), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B9, `lane-start`; L4-B14, `lane-start`) (lands after T410, T417, T427, L2-W4) — files: `lane-start` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_writes.py -k 'sql_register_writes and lane_start' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'lane_start_row_stamp or lane_helper_suite_passes'`
  - **Source:** L4:349.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T410; T417; T427; PENDING-L2: L2-W4 (its gate, L4:349; group B's gate, L1:531).
  - **The lane-helper suite** is in this task's regression run: about 45 minutes holding the shared `/tmp` lock (the format section). This task changes the files-mode writes that only that suite drives end to end.
  - **The base:** `act-begin` runs before `canon-lane` (`lane-start:1136`).
  - **One call:** W1 (`:3872`), or W2 to W5 (`:3901`, `:3922`, `:3959`, `:4072`), together with W6 (`:4038`) and W7 (`:4231`), become one `start-record` call, carrying the union of their `LANES_EXPECT` (Y:5648–5656).
  - **The arms:**
    - any 2 dies, LR010's included (`:4231–4236`);
    - a new row's 69, 75 or 3 dies with the code;
    - a resume's 69, 75 or 3 prints the writer's rerun, with its generation where the write has an id (spec:318, :338), writes the stamp, and launches.
    - After a 69 at `start-record`'s first snapshot, the note relays the writer's rerun as printed (T415). Where the run passed `--write-id new`, that rerun names no id or generation and says nothing was sent (open point 38, taken under R26; AT-11). Where it had an id, it names the id and its generation.
    - A record-less 3 (open point 42, taken under R26; T417) is relayed as printed, with its effect check.
  - **The re-base:** after the W8 wait, re-base and re-read at the second `binding` read (`:2667`).
  - **The texts for 3:** `:3876`'s "was NOT added" becomes D8's text for 3 (review M11). The writer's rerun replaces `:4235`'s hint, which leaves out `LANES_SESSION` (Y:5699).
  - **The scenarios:** spec:672–678's scenarios hold under the one call. A write that must exist (a new row, or `request-handoff`) refuses with its code; a write that only prints a note lets the launch go on.
  - **Before the launch,** `LANES_ACT` is unset (Y:5596).
- [ ] T429 [P] [US4] [S] Give `lane-handoff`'s writes their expectations and write arms, and unset `LANES_ACT` before its respawn (FR-029; Y:5671–5674, Y:5737), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B14, `lane-handoff`) (lands after T410, T417, L2-W4) — files: `lane-handoff` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_writes.py -k 'sql_register_writes and lane_handoff' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'triad_lane_tooling_handoff'`
  - **Source:** L4:363.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T410; T417; PENDING-L2: L2-W4 (its gate, L4:363; group B's gate, L1:531).
  - **The base:** `act-begin` runs before the first `sread` (`:1448`).
  - **The expectations:** the late `PAUSED` (`:2803`), W9 (`:2874`), W11 (`:2890`) and W12 (`:2913`) carry `row:L bind:L alias:L`, plus `row:<window name>` where that name differs.
  - **The arms:**
    - the writer's stderr is kept at `:2890` and `:2913`;
    - a 3 relays the writer's rerun (Y:5698);
    - `:2817` passes the code, where today it is `die … 2` (Y:5737; review M11).
  - **Before `respawn-pane`** (`:3129`), `LANES_ACT` is unset (Y:5596; review m11).
- [ ] T430 [P] [US4] [S] Give `lane-end`'s writes their expectations, with the gate inheriting `LANES_ACT`, and make W14's note exit with the writer's code (FR-029; Y:5658–5670, Y:5735), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B14, `lane-end`) (lands after T410, T417, L2-W4) — files: `lane-end` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_writes.py -k 'sql_register_writes and lane_end' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'lifecycle_fail_open_113_114_115 or lane_helper_suite_passes'`
  - **Source:** L4:371.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T410; T417; PENDING-L2: L2-W4 (its gate, L4:371; group B's gate, L1:531).
  - **The lane-helper suite** is in this task's regression run: about 45 minutes holding the shared `/tmp` lock (the format section). This task changes the files-mode writes that only that suite drives end to end.
  - **The base:** `act-begin`.
  - **The expectations:** W13 (`:2492`) and W14 (`:2540`) carry `row:L bind:L objects:L alias:L`. `retire-rows` (`:851`) carries `row:<each> bind:<each> objects:<each>`. The gate inherits `LANES_ACT`.
  - **W14's note arm** (`:2542–2545`) exits with the writer's code. W13 is already covered by `set -euo pipefail` (`:269`).
- [ ] T431 [P] [US4] [S] Make the handoff skill's step 4 open with `act-begin`, re-derive the identity, and give its writes their expectations (FR-029; Y:5678), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B14, the skill) (lands after T410, T417, L2-W4) — files: `skills/handoff/SKILL.md` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_writes.py -k 'sql_register_writes and handoff_skill' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'skill_shell_blocks'`
  - **Source:** L4:376.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T410; T417; PENDING-L2: L2-W4 (its gate, L4:376; group B's gate, L1:531).
  - **The identity:** step 4 re-runs the identity's register reads, and refuses where `lane` differs from step 1's.
  - **The expectations:** its `log PAUSED` (`skills/handoff/SKILL.md:735`), `append-line` (`:743`) and `set-row-state` (`:786`) carry `row:L bind:L alias:L`, plus `row:<window name>` where that name differs.
  - **Before any launch,** `LANES_ACT` is unset.
- [ ] T432 [P] [US4] [S] Give `lane`'s `request-handoff` its act base and `bind:<L>`, pass the code, and unset `LANES_ACT` before a launch (Y:5680, Y:5740), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B14, `lane`) (lands after T410, T417, L2-W4) — files: `lane` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_writes.py -k 'sql_register_writes and lane_request_handoff' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'park_resume_commands'`
  - **Source:** L4:380.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T410; T417; PENDING-L2: L2-W4 (its gate, L4:380; group B's gate, L1:531).
  - **The call** (`lane:1162`) takes an `act-begin` base and carries `LANES_EXPECT="bind:<L>"`.
  - **Its arm** (`:1170–1175`) passes the code, not 1 (Y:5740; review M11). A 3 relays the writer's rerun.
  - **Before `lane` launches or attaches,** `LANES_ACT` is unset (Y:5596; review m11).
- [ ] T433 [P] [US4] [S] Give `lane-rename`'s header and default arm D8's text for 3, 75 and 69 (Y:5738, Y:468–469), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B14, `lane-rename`) (lands after T417, L2-W3) — files: `lane-rename` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_in_doubt.py -k 'sql_register_in_doubt and lane_rename' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'rename_publication_atomic_158'`
  - **Source:** L4:384.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T417; PENDING-L2: L2-W3 (its gate, L4:384; group B's gate, L1:531).
  - **The sites:** the header (`lane-rename:62`) and the default arm (`:241`).
  - **The text:** "may not be written; run the printed `LANES_WRITE_ID` rerun, which writes it once or finds it".
- [ ] T434 [P] [US4] [S] Replace `lane-worktrees`' "was NOT written" for 3 with D8's text (F9; Y:5700, Y:468–469), as detailed below; its `-k` selectors wait for v3f `[J3]` (covers L4-B14, F9's `lane-worktrees` site) (lands after T417, L2-W3) — files: `lane-worktrees` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_in_doubt.py -k 'sql_register_in_doubt and texts_for_3' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'lane_worktrees'`
  - **Source:** L4:387.
  - **Depends on:** SPEC-PR; SLICE-1A; SLICE-1B; T111; T112; T113; T114; T417; PENDING-L2: L2-W3 (its gate, L4:387; group B's gate, L1:531).
  - **The sites:** `lane-worktrees:4162`'s `NOT WRITTEN (lanes-edit.sh log exited …)`, and the note at `:4170–4183`, both read 3 as "not written". The new text relays the printed rerun where the code is 3.
  - **Why it is its own task:** it is `[S]` alone. Joined to T433 it would change two modules, which makes it `[O]` (review m9).

### Implementation for group B: lane 5

- [ ] T510 [US5] [O] Give the bind its phase-2 half: the record's `act` and the five copies from `start-record`'s pending record, the stale row's copies, the child's `find_write` lookup before a rerun with its `w₀` wait and the reruns table, and the `set-row-state` on the lane left with `LANES_ACT` from the record and `LANES_EXPECT="row:<from> bind:<from>"` (FR-041; W:5347, W:5374–5381, W:5446–5468, W:5505–5512) (covers L5-B1); its selectors are v3f's `[J3: v3f]` (STOP until T101; lands after the spec PR, T502, T507, T409, T410, T412, T413, T414, T415, T417, T418, T427, T428, T112, T114, L2-W5; case 21 runs through the delayed-loopback relay of lane 3's ruling on the test plan's open point 23, option B (#165 6741861590)) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_guard.py -k 'sql_register_guard and (test_two_sessions_binding_one_lane_one_start_record_commits or test_the_bind_is_one_register_write_and_refuses_a_changed_row or test_a_bind_in_doubt_looks_its_write_up_before_a_rerun or test_the_bind_child_records_bind_failed_with_the_code_and_binds_nothing or test_a_bind_killed_mid_commit_waits_for_ended_utc_plus_c_before_its_lookup or test_the_bind_childs_lookup_sends_the_five_copied_values or test_the_stale_row_copies_a_dead_outers_pending_call_and_the_next_2_looks_it_up or test_a_bind_over_a_delayed_tls_link_at_c_3_ends_bound_inside_530_s)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_guard.py -k 'sql_register_guard and (test_answer_2_exits_2_binding_before_t0_plus_1_s_and_the_runner_sees_eof or test_the_bind_child_survives_a_group_kill_and_hangup_and_ends_bound or test_a_prompt_mid_bind_is_not_passed_and_leaves_the_record or test_after_bound_one_moved_then_the_next_prompt_passes or test_a_failed_bind_renames_back_keeps_the_offer_and_allows_three_attempts or test_a_dead_bind_outer_answers_in_progress_while_its_session_lives_then_no_outcome or test_a_reused_bind_pid_gives_no_outcome_with_no_session_test or test_a_bind_body_past_its_bound_ends_with_the_bounds_code_and_leaves_no_process or test_a_live_bind_past_its_bound_that_timeout_has_not_ended_answers_2_and_names_the_pid_to_kill or test_a_binding_from_another_host_or_container_is_not_judged_by_its_pid_until_the_deadline_plus_60_s or test_an_unreadable_bind_record_is_dropped_with_exit_2 or test_two_sessions_binding_one_lane_in_files_mode_the_second_is_refused_at_the_reread or test_the_bind_child_reaches_tmux_through_tmux_pane or test_the_bind_child_environment_is_as_lane_4_lists_it or test_the_hook_string_and_guard_timeout_are_unchanged or test_kill_of_the_bind_outer_ends_the_record_with_the_signals_code or test_a_bind_with_no_timeout_says_bounded_no_and_kill_stops_its_group_once or test_the_bind_resume_path_makes_at_most_n_r_read_calls or test_answer_2_survives_a_group_sigterm_at_the_hooks_timeout or test_a_failed_bind_can_be_answered_again)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'guard_launch_mode or repo_hygiene'`
  - **Source:** L5:362.
  - **Depends on:** SPEC-PR; T101; T502; T507; T409; T410; T412; T413; T414; T415; T417; T418; T427; T428; T112; T114; PENDING-L2: L2-W5; PENDING-L2: L2-0a to L2-0e (cluster, switch, FAKE_PSQL, stub lanes-index, prefixes), with T107-T110 through L2-0a's template, before any real-engine run (v5:362; T502 for the `guard-bind` and record it extends, and whose names its second run reselects (v5:378); T507 for the record's `c` and `n_a` that `w₀` reads (v5:369); v5:573, v5:580, v5:582, v5:603, v5:664, v5:677; case 21's STOP lifted by lane 3's ruling on the delayed loopback, option B (#165 6741861590)).
  - **Changed at assembly:** lane 3, applying: rule: no 007 code PR lands before the spec PR (lane 3's rule, 6726556972; brief); section D item 9; lane 3, fix round B, 2026-11-09: the task line's gate matches its Depends on, and case 21's STOP is lifted; lane 3, fix round B2, 2026-11-09: case 21 cites lane 3's ruling on the delayed loopback, option B (#165 6741861590), in place of round B's citation, which ruled lane 5's own point 23.
  - **Ownership.** Lane 4 agrees that this half is lane 5's (OB-8; GB:450, GB:642), and its draft's GB-14 and GB028 leave lane 4's block.
  - **`act`** is written at the offer: the guard's base `<act-id>@<n>:<generation>`, from the `n` and generation of the prefetch that made the offer (W:5378; T410's grammar).
  - **The five copies.** When the outer writes an outcome other than 0, it copies `write_id`, `generation`, `not_after`, `write_lane` and `args_sha256` from this session's `start-record` pending record, where that record holds a `call` line (T412's `contracts/pending-record.md`).
    - The stale row takes the same copies, with `not_after` the largest among its `call` lines (v3f's case 20).
    - A record from another host or container gets none.
  - **The lookup before a rerun** (W:5460): `lanes-index journal --write-id <id> --not-after <t> --generation <g> --lane <write_lane> --verb start-record --args-sha256 <h>` (T114). A record with no `write_id`, or one whose `write_id` no pending record still holds, makes no lookup: it drops its copies and binds anew.
  - **`w₀`.** Where the record's `not_after` may not have passed, the child waits on its own clock until `ended_utc` plus `C`, then calls. `C` is `N_a`·`c` + 6 s from the record's `c` and `n_a` (R05; open point 3), not a fixed 8 s. The record's `c` is `.head`'s, the larger of the two URLs' (O15), so `w₀` waits no shorter for it.
  - **The reruns table** (W:5462–5468):
    - found: run the pending record's `cmdline` under its `write_id`, generation, lane and session, and the tail runs (T418);
    - absent, with the head row's lock granted: bind anew under a new id;
    - no try within the window, or LR011: `bind-failed`, `rc=3`, copies kept;
    - LR007 `another-write`: `rc=2`, the five copies dropped;
    - LR007 `generation`: `rc=2` with a plain rerun's refusal, copies kept.
  - **The `set-row-state` on the lane left**, after `lane-start`'s 0, with `LANES_ACT` from the record's `act` and `LANES_EXPECT="row:<from> bind:<from>"` on that call only (W:5347). Its 2 or 3 becomes a `note=`.
  - **The record's code** (v3f, "The record's code"): `start-record`'s 69, 75, 3 or 2 ends `bind-failed` with that `rc`; only a 3 keeps copies.
  - **The second run** reselects T502's names with no mode filter, so their primary half is verified once phase 2's writes exist.

**Checkpoint A+B** (lane 3's integration): group B's branch, with group A
landed on code `main`, passes these before group B's last pull request
lands:

- every phase-W test, against the real `lanes-index` with its constant
  flipped in a copy;
- `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -rs`.

---

## Phase 3: Group C (phase G of the test plan)

**Goal**: the renderer's git half, the one push function, the ways out of a
divergence, rollback's and the cutover's pushes, then the constant lifted. It
changes `lanes-index` and `tests/` only (lane 4's row 10).

**Independent Test**: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_renderer_git.py tests/test_sql_register_divergence.py tests/test_sql_register_rollback.py tests/test_sql_register_cutover.py tests/test_sql_register_lane_day.py tests/test_sql_register_drill.py -k 'sql_register_renderer_git or sql_register_divergence or sql_register_rollback or sql_register_cutover or sql_register_lane_day or sql_register_drill'` `[J3]`.

### Tests for group C (write first)

- **PENDING-L2: L2-G1 to L2-G6**, in lane 2's block.

### Implementation for group C: lane 3

- [ ] T320 [US6] [O] Add the one push function that the renderer, `cutover` and `rollback` share (D23; S2's "The push"; the phase plan's "The push URL"; FR-045; `contracts/render-commit.md`), as detailed below; its `-k` selector waits for v3f `[J3]` — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_renderer_git.py -k 'sql_register_renderer_git and push' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** SK:223; verify in R28's two-run form.
  - **Depends on:** SPEC-PR; T344 (the contract); T415, group B's commit with the author that `register commit` stores (L4:216).
  - **Order:** lane 4's T416, the renderer's fork, lands after T321, as lane 4's block says (L4:247). The skeleton's order, which put it before group C (SK:827), is superseded (lane 3, fix round B, 2026-11-09).
  - **With `render_key=`:**
    - The URL is `ssh://git@github.com/<owner>/<repo>.git`, where `<owner>/<repo>` is `_norm_repo` of the checkout's own `git remote get-url origin`, and equals `source_of`'s.
    - The push runs in an environment of its own:
      - `GIT_SSH_COMMAND` naming the key, with `IdentitiesOnly=yes` and `BatchMode=yes`;
      - `SSH_AUTH_SOCK` removed;
      - `GIT_CONFIG_GLOBAL=/dev/null` and `GIT_CONFIG_NOSYSTEM=1`;
      - every inherited `GIT_*` variable removed;
      - `--no-verify`.
    - A non-GitHub origin is refused with 2.
  - **Without `render_key=`:** the push goes to `origin` as it stands, as under Q21(b)(A) and in the bare-origin fixtures.
  - **Both forms:**
    - The push is `<tip>:refs/heads/main`, never forced, bounded by `LANES_GIT_TIMEOUT`.
    - Nothing is written to the checkout's configuration: no `set-url` and no `pushurl`.
  - **The read-back** fetches `origin` and checks `merge-base --is-ancestor`, since a push to a URL moves no `refs/remotes/origin/main`.
  - It is security-sensitive: the key bypasses the ruleset, so the key stays in no environment a session inherits ("A server-side gate protects the rendered paths and `lanes/STORE`").
- [ ] T321 [US6] [O] Add `lanes-index render --checkout <c>`'s lock and its handshake (D14; S2's first bullet; FR-044), as detailed below; its `-k` selector waits for v3f `[J3]` — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_renderer_git.py -k 'sql_register_renderer_git and lock' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** SK:239; verify in R28's two-run form.
  - **Depends on:** SPEC-PR; T320.
  - It takes `fcntl.flock(LOCK_EX | LOCK_NB)` on `<git-dir>/lanes-render.lock`, opened close-on-exec. It never takes the lane's write lock, and never a `mkdir` mutex.
  - **Both halves of `SyncLock`'s handshake:**
    - a fork that finds the lock held writes `lanes-render.rerun`, tries once more, and exits;
    - the holder, after it releases, looks for a mark once more, and takes the lock back for it.
  - It removes `LANES_NO_FETCH` from every child's environment, and never consults it (D12).
- [ ] T322 [US6] [O] Add the renderer's pass (S2's "A pass"; D15; D16; FR-044; `contracts/render-commit.md`), as detailed below; its `-k` selector waits for v3f `[J3]` (placement: lane 2's) — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_renderer_git.py -k 'sql_register_renderer_git and pass' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** SK:245; verify in R28's two-run form.
  - **Depends on:** SPEC-PR; T321, T301 and T344.
  - **Fetch.** It clears the rerun mark, and fetches `origin main` under `LANES_GIT_TIMEOUT`. A failed fetch is recorded with `record_mirror_state`, and nothing is pushed.
  - **Find `m`.**
    - It uses form C, paged with `--skip`, 200 commits a page, and takes the first non-empty `Register-Seq`. It never uses `--grep`. The whole walk is bounded by `LANES_GIT_TIMEOUT`.
    - Where it runs out of time, or finds no trailer, it records why, pushes nothing, and ends.
    - The trailer of a stray that a `stray-import` or `stray-revert` entry names does not count.
  - **Read the head.** It reads `h` and the switch in one read. At `primary`, an `m` above `h` is a divergence at the commit `R` that carries it (R2's N6). At `files` it is not.
  - **Check for a divergence.** Any commit after `R` that changes a render-set path and is not a render commit is recorded as `diverged at <sha>`, and the renderer ends. So is a render set at `m` that differs from the images at `m`.
  - **The target** is `t = min(h, m + 64, o − 1)`, where `o` is the first operator entry after `m`, by `journal.operator` (PC-3). Where `m + 1` is an operator entry, it renders nothing (F2).
  - **Build.** Under the lock, it clears `lanes-render.index` and its `.lock`. Then, for each `k` from `m + 1` to `t`:
    - it builds the images at `k` with T301's function;
    - it reads the parent's tree into the private index (`GIT_INDEX_FILE`), writes the changed blobs with `hash-object -w` and stages them with `update-index`, setting the render set exactly to the images;
    - it runs `write-tree`, then `commit-tree --no-gpg-sign -p <parent>`.
  - **The message** is the entry's message byte for byte, with `Register-Seq: <k>` appended to its final trailer block, or as a new last paragraph after one blank line. It never runs `git interpret-trailers`. The author and committer are the entry's, both dated by its commit UTC, with the encoding UTF-8 (D15; `contracts/render-commit.md`).
  - **The render set** is filtered in code, since git 2.43's `ls-tree` refuses `:(exclude)`.
  - **Push** through T320. A rejection refetches. Where origin then carries `t` or more, that counts as pushed. Otherwise the pass rebuilds, six push attempts in all.
  - It never touches a HEAD, an index or a working tree of the checkout.
- [ ] T323 [US6] [O] Bound the renderer's progress (F15; FR-046), as detailed below (PC-3, as research.md takes it); its `-k` selector waits for v3f `[J3]` — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_renderer_git.py -k 'sql_register_renderer_git and progress' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** SK:262; verify in R28's two-run form.
  - **Depends on:** SPEC-PR; T322.
  - It makes another pass only after a pass that moved origin's last `Register-Seq` past where that pass found it, or once for each rerun mark it finds after it releases.
  - After 16 passes it releases the lock and re-forks itself once, detached, only if its last pass made progress.
  - Each stop condition ends the renderer with nothing on any HEAD. Those are:
    - nothing missing;
    - an operator seq next;
    - `diverged at`;
    - a fetch, authentication or six-rejection failure;
    - a failed journal read;
    - the handover.
- [ ] T324 [US6] [O] Add the two ways out of a divergence (S2's "The two ways out"; FR-047), as detailed below; its `-k` selector waits for v3f `[J3]` — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_divergence.py -k 'sql_register_divergence' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** SK:273; verify in R28's two-run form.
  - **Depends on:** SPEC-PR; T323; T115 (lane 1's loader, for `import --single`).
  - **`render --revert <sha>`** journals an entry with no edits, whose message names the stray, through `register commit`. For a `Register-Seq` above the head at `R`, its entry takes `h+1` and names `R`.
  - **`import --single <sha>`** (U7, lane 3's) loads the stray commit's line edits on the render set as one write, through `register commit`, reusing lane 1's loader. It refuses where they no longer apply to SQL's images.
  - Each records the stray as resolved, which clears `diverged at`. Each runs only while the switch is `primary`. Each is in doubt, with D8's rerun, as any write's commit call is.
  - The runbook prefers the revert (PC-4).
- [ ] T325 [US6] [O] Make `lanes-index reconcile` re-render what the mirror lacks, through T322's pass in the given checkout (FR-048). It imports nothing; its `-k` selector waits for v3f `[J3]` — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_renderer_git.py -k 'sql_register_renderer_git and reconcile' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** SK:278; verify in R28's two-run form. The skeleton's `tests/test_lanes_index.py -k reconcile` part moves into the regression run.
  - **Depends on:** SPEC-PR; T323; T117 (lane 1's read-only check in `reconcile`).
- [ ] T326 [US7] [O] Add `rollback`'s render and push, steps 2 to 4 and `--hold-marker` (S5's rollback; operations §2.3; FR-051; `contracts/render-commit.md`), as detailed below; its `-k` selector waits for v3f `[J3]` — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_rollback.py tests/test_sql_register_drill.py -k 'sql_register_rollback or sql_register_drill' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** SK:279; verify in R28's two-run form.
  - **Depends on:** SPEC-PR; T304 and T323.
  - **Step 0's git half.** Right before step 1, it fetches `origin main` again and checks it as T322 recognises a divergence, a `Register-Seq` above the head included. It records a divergence it finds, and refuses with 2.
  - **Step 2.** It renders in-process, in the operator lane's checkout, with `--until k`, under that checkout's render lock, until the mirror holds `k` and its render set's tree id equals a full render's.
  - **Step 3.** `rollback --hold-marker` stops after step 2, for Brett to remove the ruleset. Run again, `rollback` resumes at step 4.
  - **Step 4.** It pushes `k+1` through T320, reads it back, and records it with `record_marker`, setting the marker `absent`. A push it cannot confirm exits 3, and the rerun reads origin back before it acts.
  - It holds no database transaction across a git act (F23; FR-052).
- [ ] T327 [US7] [O] Add `cutover`'s push, in its first and after-loss forms (S5's cutover, steps 2 and 3; D21; D24; FR-050) (U6, lane 3's; the transaction stays lane 1's T116), as detailed below; its `-k` selector waits for v3f `[J3]` — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_cutover.py tests/test_sql_register_marker.py -k 'sql_register_cutover or sql_register_marker' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'test_lanes_index or repo_hygiene'`
  - **Source:** SK:286; verify in R28's two-run form.
  - **Depends on:** SPEC-PR; T323, T116 and T344.
  - **Step 2.**
    - It builds the marker commit on origin's tip. D21's fields come from the switch, with the label from `register_label=` alone, and the commit carries the entry's `Register-Seq`.
    - It checks that the tip's `lanes` tree id equals `C`'s.
    - It pushes through T320 as a fast-forward, reads it back, and records it with `record_marker`, setting the marker `present`.
    - A push it cannot confirm exits 3.
  - **Step 3, the abort.**
    - Where the tree has moved, it pushes nothing.
    - Where a push was made, it first proves by read-back that the marker is absent.
    - It then calls lane 1's `switch_to_files`.
  - **The after-loss forms** (`--import <M> --after-loss`, and `--after-loss` after a restore) follow D24. Their seqs are `m+1` or `b+1` (operations §2.3's disaster path).
  - Lane 2's test 3 agreement form runs here (PC-6).
- [ ] T328 [US1] [O] Lift Q6(c)'s refusal: set `RENDER_GIT_HALF = True`, as group C's last pull request (D3; FR-003) — files: `lanes-index` — verify: `command grep -qF 'RENDER_GIT_HALF = True' lanes-index && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -rs`
  - **Source:** SK:298. The whole suite is its own regression run.
  - **Depends on:** SPEC-PR; T320 to T327, and Checkpoint A+B.
  - Lane 2's test holds the constant True exactly when `render --checkout` exists, and test 14's flipped state passes.

**Checkpoint C**:

- Tests 5, 6, 8, 11 and 13 pass.
- The constant is True.
- A full render equals the mirror after the lane's day.

---

## Phase 4: The register service, beside groups A and B; the v1.2.0 tag waits for it

**Goal**: `lanes-index serve`, SVC-1 to SVC-12, built beside groups A and B,
not after them, and the v1.2.0 tag waits for it: Brett's "4A" (FR-058; R35;
lane 3's #165 comment 6726766577). Act 4 waits for it too, since the guard
measured without it from Eagle is over his 1 s (Y:4826). No service pull
request lands before the spec PR. Lane 5 codes it against the fixture's
configuration while Q6(c)'s refusal stands (Y:3951–3956).

### Tests for the service

- **PENDING-L2: L2-S1**, test 10 through the service, in lane 2's block.

### Implementation for the service: lane 5 (U5, under lane 1's review)

- [ ] T511 [P] [US8] [O] [X: hosting] Write `contracts/socket-api.md`: the socket's path and its checks; who may connect; the request, file names only; the answers (the text at `n` with digests, `n` and generation; `refuse db <cause>`; `unavailable`); the 16 MB and 300 ms limits; the start on demand, its lock, stamp and namespace checks, and the service's connect, at `c` for each address (X5 text 13; W:3857); and the start-only form `lanes-index serve --start` (R50) (SVC-2 to SVC-8, SVC-11; W:3716–4300, W:4373–4388) (covers L5-S1's `contracts/socket-api.md`) (under lane 1's review, U5; built beside groups A and B, and the v1.2.0 tag waits for it, by Brett's "4A" (#165 6726766577); can start now; lands no earlier than the spec PR, since no service PR lands before it, and before T512) — files: `../spec/specs/007-sql-register/contracts/socket-api.md` — verify: `bash -c 'f=../spec/specs/007-sql-register/contracts/socket-api.md; for w in "refuse db" unavailable "guard: direct (service unavailable)" SO_PEERCRED LOCAL_PEERCRED boot_id register.lock 0700 "16 MB" "300 ms" "REPEATABLE READ" generation LANES_REGISTER_SERVE_IDLE_CHECK_S LANES_REGISTER_SERVE_IDLE_STOP_S; do grep -qF -- "$w" "$f" || { echo "missing: $w" >&2; exit 1; }; done'`
  - **Source:** L5:424.
  - **Depends on:** SPEC-PR; PENDING-L1: lane 1's U5 review (v5:424; v5:416, v5:586; no service PR before the spec PR (brief; lane 3, fix round B, 2026-11-09: T511 lands after the spec PR)).
  - **Changed at assembly:** lane 3, applying: ruling R50; Brett's 4A (#165 6726766577) and the rule that no service PR lands before the spec PR (brief); section D item 11 (v5:424; B7).
  - **It is the source** for lane 3's T341, "the socket API" in the manual's part 2.
  - **`[X: hosting]`:** its start path names the service's connect, which W gives as "overrides with 2" (W:3857), X5 text 13 makes `c` for each address, and text 8″, taken into X, gives the service's timer the p90 round trip.
  - **Form 4 never uses the socket** (B7, lane 3's ruling, #165 6738474549): the request carries file names only, and register-service spec:59 stands, so the guard's second read at `n` always reads directly and never reads `.direct`. If T511's and T512's measurements show that path often running out of budget, lane 5 brings the point back; a request naming `n` would be a spec change, for Brett.
  - **The two test-only variables** (R37): the socket API names `LANES_REGISTER_SERVE_IDLE_CHECK_S` and `LANES_REGISTER_SERVE_IDLE_STOP_S` as test-only, with their 1 s floor, the idle check's cap below 240 s, and the starter's reduced environment keeping both (T512).
- [ ] T512 [US8] [O] [X: hosting] [X: logins] Add `lanes-index serve` and make `register read` its client: one service per environment; the socket and who may connect; start on demand with no supervisor; only on primary; one `psql` child on pipes with a background connect and per-request bounds; every answer fresh at `n`; the client's share, the `.direct` flag and the fallback line; host-local facts kept client-side; the write-through to T302's cache; and `status`'s service lines (FR-057; FR-058: built beside groups A and B, and the v1.2.0 tag waits for it, by Brett's "4A" (#165 6726766577); SVC-1 to SVC-8, SVC-11; W:3667–4300, W:4373–4388) (covers L5-S1); its selectors are v3f's `[J3: v3f]`, with eight guesses `[J3]` (under lane 1's review, U5; STOP until T511 has landed; lands after T101, T103, T104, T105, T106, T111, T112 if AT-10's fresh write uses it (A10), T113, T115, T117, T107 to T110 through lane 2's template, T302, T504, T505, T507, L2-0d's flipped copy, L2-S1, A7, A10, and lane 1's review gate, `L1:452–465` (U5) and `:480–483` (R44); lands after the spec PR, since no service PR lands before it (lane 3's rule, 6726556972); not after group B or group C) — files: `lanes-index` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_guard.py -k 'sql_register_guard and (test_the_guard_through_the_service_answers_byte_identical_to_the_direct_read or test_with_the_service_down_the_hook_falls_back_and_says_so or test_a_service_that_never_answers_falls_back_inside_5_s or test_the_database_unreachable_with_the_service_up_is_2_never_a_cached_ok or test_a_database_that_does_not_answer_is_2_inside_4_5_s or test_a_connect_timeout_above_the_direct_reads_share_is_cut_at_the_parse_start_line or test_the_services_request_timer_follows_register_rtt_ms or test_each_service_request_is_one_query or test_the_services_idle_check_replaces_a_dropped_connection_in_the_background or test_a_cache_miss_at_the_service_answers_unavailable_and_fetches_in_the_background or test_a_write_between_two_requests_is_in_the_next_answer or test_the_service_reaches_the_database_only_through_psql or test_the_first_call_starts_the_service_with_no_supervisor or test_a_service_that_cannot_start_falls_back_to_the_direct_read or test_no_session_environment_carries_a_database_url_or_password or test_the_guards_request_carries_only_file_names or test_guard_bind_computes_bound_s_from_c_n_a_and_the_service)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_guard.py -k 'sql_register_guard and (test_status_prints_the_services_lines_and_its_failure_stamp or test_the_socket_directory_is_a_0700_directory_of_this_uid_and_never_a_symlink or test_a_client_of_another_uid_is_closed_by_so_peercred or test_an_owner_file_from_another_namespace_or_boot_is_no_service or test_serve_refuses_unless_the_mode_answer_is_primary or test_one_instance_holds_register_lock_and_a_second_starter_exits_0 or test_a_stale_socket_is_unlinked_under_the_lock_and_rebound or test_the_service_stops_after_its_idle_time_and_unlinks_its_socket)' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k '(lanes_index and not sql_register) or repo_hygiene'`
  - **Source:** L5:428.
  - **Depends on:** SPEC-PR; T511; T101; T103; T104; T105; T106; T107; T108; T109; T110; T111; T112; T113; T115; T117; T302; T504; T505; T507; PENDING-L2: L2-0d's flipped copy of lanes-index; PENDING-L2: L2-S1; PENDING-L2: A7's seven guesses and A5's status guess [J3]; PENDING-L2: A10, T512's guesses in phase N and AT-10's fresh write through T112 or commit_write; PENDING-L1: lane 1's U5 review gate; PENDING-L2: L2-0a to L2-0e (cluster, switch, FAKE_PSQL, stub lanes-index, prefixes), with T107-T110 through L2-0a's template, before any real-engine run (v5:428 (T112 only if AT-10's fresh write uses it, A10); v5:573–584, v5:586, v5:597, v5:604–605, v5:653–657, v5:661; R36, R44).
  - **Changed at assembly:** lane 3, applying: Brett's 4A (#165 6726766577); spec.md's FR-058 now says so; rule: no service PR lands before the spec PR (brief); comment 6739068493: section D item 11 also covers v5:433 (SVC-3); comment 6739068493: section D item 11 also covers v5:452 (SVC-8); section D item 11 (O16); section D item 11 (v5:455; B7); ruling R50; ruling R36; ruling R44; ruling R37 (floor 1 s; the idle check capped below 240 s; both kept in the starter's environment).
  - **SVC-1:** one service per environment, a bench container or a host shell. It connects as the engineer's reader login, `lanes_r_<engineer>`, which the environment's `lanes-index.conf` names (X5 texts 11, 12, 14).
  - **SVC-2:** the socket under `${XDG_STATE_HOME:-~/.local/state}/openRepoTools/run/`, a 0700 directory owned by this uid, not a symlink, checked on every use.
    - Peers: `SO_PEERCRED`, or `LOCAL_PEERCRED` on macOS.
    - The owner file records the pid namespace and the boot id; a client whose own differ treats the service as absent.
  - **SVC-3:** `register read` asks the socket first and reads directly when the service is unavailable, but form 4, the guard's second read at `n`, always reads directly, never asks the socket and never reads `.direct` (B7, lane 3's ruling, #165 6738474549; register-service spec:59 stands). The guard's fallback line, from form 2, is `guard: direct (service unavailable)`.
  - **SVC-4:**
    - the client starts `serve` detached from Python, with `start_new_session=True`, stdin and stdout on `/dev/null`, stderr to `register.log`, working directory `/`, and a reduced environment with no URL or password (W:3849–3858);
    - one instance by `flock` on `register.lock`;
    - a stale socket unlinked under the lock;
    - a stop after 15 minutes idle, or when the install changes;
    - a failure stamp that holds back restarts for 60 s.
    - `serve --start` (R50) runs the starter and returns at once, so the bash `SessionStart` block detaches nothing itself (W:3857–3858: "No bash code detaches anything").
  - **SVC-5:** `serve` refuses unless T105's mode answer is `primary`: with 2 for files and `index` mode (W:3913).
    - Under Q6(c)'s refusal, W:4821 has `load_config` refuse it with 2 ("refuses with 2 for any subcommand but `mode` and `register read`"). So the refusing build's `serve` exits 2, as W has it, with no departure (R36; FR-003); lane 5's proposal of 78 is not taken.
    - Before T328 that is every real host, so the shipped `serve` is dormant there; its behaviour is tested on L2-0d's flipped copy (the section's opening).
  - **SVC-6:**
    - one long-lived `psql` child on pipes, connecting to the primary's port 5432 through the install's load balancer, never through a pooler (X5 text 15; lane 3 ruled that it holds, 6726110116);
    - a connect at `c` per address, in the background only (texts 13 and 17);
    - per-request bounds: 150 ms `lock_timeout`, 180 ms `statement_timeout`, a 200 ms timer, or the round trip plus 100 ms over 100 ms (W:3488). The round trip is `register_rtt_ms=`, which text 8″, now taken into X (6726078173), makes the p90 of act 3's `select 1` round trips in one session, taken while the workstation is at work. The timer's rule does not change, only how act 3 measures its input, so `test_the_services_request_timer_follows_register_rtt_ms` reads `register_rtt_ms=` as that p90 and keeps its fixed cases (none, and 150);
    - `SELECT 1` every 60 s, inside the load balancer's 4-minute idle reset (text 16).
  - **SVC-7:** one REPEATABLE READ READ ONLY transaction per request; images brought to `n` and checked against the digests, else from the image cache at the digest, else `unavailable`.
  - **SVC-8, the client's part:**
    - a 300 ms wait, or SVC-6's timer plus 100 ms;
    - the `.direct` flag, which form 4 never reads (B7);
    - answers over 16 MB, or incomplete within the share, are refused;
    - `w_s` reported for `bound_s`, as `.head`'s `w_s_ms=` in whole milliseconds, wherever this `lanes-index` has `serve`, on primary, whether or not the service answered the read (O16; O15).
  - **SVC-11:** the request carries file names only, and the service reads no pid, no `/proc` entry of a lane, no session record and no tmux. So it cannot be asked for a log at an older `n`, and form 4 never asks it (B7).
  - **No test yet** for SVC-2's directory, `SO_PEERCRED` and owner-file checks, SVC-5's refusal, or SVC-4's lock, stale socket and idle stop. The second run's seven new guesses ask lane 2 for them (A7).
  - **The write-through** to T302's image cache (Question 13; FR-057).
  - **`status`'s lines** for the service and its failure stamp (W:3731, W:3904) sit in lane 1's `cmd_status` (T117). They are written here, in T117's `cmd_status`, under lane 1's review, after T117 has landed; T117 does not wait for the service (R44).
  - **Two test-only variables (R37, lane 3, 6726556972; open points 24 and 25).** v3f's open point 35 asks this, and 6726110116 routed it to lane 5:
    - `LANES_REGISTER_SERVE_IDLE_CHECK_S`, the interval of SVC-6's `SELECT 1` (W:3956–3957), default 60;
    - `LANES_REGISTER_SERVE_IDLE_STOP_S`, SVC-4's idle stop (W:3877), default 900.
    - **Each is honoured only where it is set.** Each names its interval in whole seconds, and a value that is not a positive whole number is ignored, with the default kept. `LANES_REGISTER_SERVE_IDLE_CHECK_S` keeps a 1 s floor and is capped below 240 s (R37; L1:462): a value of 240 or more is ignored, with 60 kept.
    - **The precedent:**
      - in W, the register's seconds seams are named `LANES_REGISTER_*_S`: `LANES_REGISTER_DEADLINE_S` sets D7's deadline (W:2624; v3f's open point 35 names it), `LANES_REGISTER_SEAM_CAP_S` the seam's cap (W:4950) and `LANES_REGISTER_PENDING_MAX_AGE_S` the record's maximum age (W:5132); `LANES_BIND_BOUND_S` sets the bind's bound (W:5378);
      - in today's code, `LANES_POLL_SECONDS` is an interval seam of the same kind: "the interval `request-handoff`'s wait polls the published log at (default 15). The test seam, and only that" (`lanes-edit.sh:610–611`), which the suite sets to 1 (`tests/test_lane_helpers.sh:14370`).
    - **The starter's reduced environment** (SVC-4 item 2, W:3849–3858) keeps both where they are set, or the service never sees them. W's list, "`PATH`, `HOME`, `LANG`, the `XDG_*` variables and `LANES_INDEX_CONFIG`" (W:3852–3853), names them too (R37).
    - **They shorten** AT-10's `test_the_services_idle_check_replaces_a_dropped_connection_in_the_background` and A7's idle-stop guess.
    - **Recorded in** 007's `research.md`, as R37's decision: the starter's environment gains two test-only variables.
  - **Recorded in the PR body, never asserted:** the answer time against 50 ms (`test_the_guard_answer_time_is_recorded_beside_todays_direct_guard`).
  - **Not covered by a test** (v3f §6.3): one service across containers.
- [ ] T513 [US8] [O] Make the `SessionStart` block start the service after its own answer, without waiting, on primary only, through `lanes-index serve --start` (R50) (SVC-4 item 1, SVC-12; W:3843–3848, W:4443–4455) (covers L5-S1's SVC-12); its selector is v3f's `[J3: v3f]` (under lane 1's review, U5; built beside groups A and B, and the v1.2.0 tag waits for it, by Brett's "4A" (#165 6726766577); lands after the spec PR, since no service PR lands before it, and after T508, T511, T512, L2-S1 and L2-0d's flipped copy; not after group C) — files: `lanes-edit.sh` — verify: `TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh tests/test_sql_register_guard.py -k 'sql_register_guard and test_the_session_start_block_reads_the_mirror_and_only_starts_the_service' && TMPDIR=/tmp OPENREPOTOOLS_REQUIRE_PG=1 bash tests/run.sh -k 'install_hook_gate or install_skill_and_hook or repo_hygiene'`
  - **Source:** L5:472.
  - **Depends on:** SPEC-PR; T508; T511; T512; PENDING-L2: L2-S1; PENDING-L2: L2-0d's flipped copy (its test bed before group C); PENDING-L1: lane 1's U5 review (v5:472; v5:418, v5:474, v5:586, v5:604).
  - **Changed at assembly:** lane 3, applying: ruling R50; Brett's 4A (#165 6726766577) and the rule that no service PR lands before the spec PR (brief).
  - **The block** still asks the service nothing and exits 0 (W:4443–4455). The next guard request finds the service running (v3f's AT-10, lane 5's question 17 as lane 3 ruled).
  - **Before group C** no real host is `primary`, so the block's start never fires there. T513's test runs on L2-0d's flipped copy, as T512's do.

**Checkpoint S**: test 10 through the service shows that:

- its answers are byte-identical to the direct read;
- the fallback line appears;
- the answer takes under 50 ms on the fixture (SC-005).

---

## Phase 5: The release (lane 3)

**Goal**: the record, and v1.2.0's notes. The acts on Brett's own word are
outside the task list.

- [ ] T340 [P] [US10] [S] Write "The SQL register" in the manual, part 1: "What changes for a lane" and the mechanisms (U10, lane 3's, assembling each lane's texts; FR-062), as detailed below — files: `../spec/docs/README-lanes.md` — verify: `command grep -q '^## The SQL register' ../spec/docs/README-lanes.md && command grep -q '^### What changes for a lane' ../spec/docs/README-lanes.md && ! command grep -q 'No test creates a real database' ../spec/docs/README-lanes.md`
  - **Source:** SK:331.
  - **Depends on:** SPEC-PR; Checkpoint C, since it describes each mechanism as built; each lane's paragraphs for its own part.
  - **The section** sits beside "The derived index (Amendment 14)", and opens with "What changes for a lane". That part covers:
    - 69, and what to do;
    - 75: nothing was written, and a retry in seconds is safe;
    - 3 and its printed `LANES_WRITE_ID` rerun;
    - the guard during an outage, and its two ways out;
    - `commit` and hand edits refused;
    - the files as a mirror that lags, and the verbs that give the truth;
    - the claim's seq;
    - the claim loser's 2 instead of 7;
    - the later `HOLD` or `LANDING` writer's code.
  - **Then:**
    - `mode=primary`;
    - the switch and `lanes/STORE`;
    - the journal and the renderer;
    - which reads may use the mirror;
    - import and rollback.
  - **Elsewhere in the manual:**
    - the sections that describe writes and reads say where `mode=primary` changes them;
    - the derived-index section's "No test creates a real database" is corrected.
  - Each lane supplies the paragraphs for its own part, and lane 3 assembles them.
  - Any line cap the spec leg's checks hold moves only by a dated entry.
- [ ] T341 [US10] [O] Write "The SQL register", part 2, for User Stories 7, 8 and 10: the roles, the secret's reach, the socket API and the runbook (U10, lane 3's; FR-062), as detailed below — files: `../spec/docs/README-lanes.md` — verify: `command grep -q 'socket API' ../spec/docs/README-lanes.md && command grep -q 'readable by every lane process' ../spec/docs/README-lanes.md && command grep -q 'rollback --hold-marker' ../spec/docs/README-lanes.md`
  - **Source:** SK:353.
  - **Depends on:** SPEC-PR; T340, in the same file; lane 5's T511 for the socket API.
  - **The roles** with their GRANTs (data-model.md), and each engineer's two logins, made by `lanes-index login`.
  - **The REGISTER-WRITER secret's reach, plainly:** readable by every lane process on its host; Rule 9(e) alone forbids a hand-run write; the same exposure as today's push rights. It claims no protection the design lacks.
  - **The socket API:** the service answers the text at `n`, and the reader decides (SVC-3, SVC-7), as `contracts/socket-api.md` fixes it.
  - **The runbook for acts 3 and 4 and the rollback,** from operations.md: every act, word, command and owner, in order; the readiness check in each environment; the freeze; R3b's smoke test; the rollback's triggers; act 4's wait for slices 2a, 2b and 3. Its server, backup and restore steps follow operations.md §2.2 and §2.3 at Y, with research.md's ERR-03 for the restore from a dump.
  - It is `[O]`: it states security limits and an operator procedure.
- [ ] T342 [P] [US10] [S] Draft `docs/release-notes/v1.2.0.md` in the spec leg, in `v1.1.0.md`'s form, as detailed below — files: `../spec/docs/release-notes/v1.2.0.md` — verify: `command grep -q '^## Before the tag' ../spec/docs/release-notes/v1.2.0.md && tail -n 1 ../spec/docs/release-notes/v1.2.0.md | command grep -q 'openRepoTools-spec.*docs/release-notes/v1.2.0.md'`
  - **Source:** SK:360.
  - **Depends on:** SPEC-PR; it can be written at any time.
  - What v1.2.0 brings: #165's SQL register, which a workstation uses once act 4's cutover and its own flip have run; the register service, which the tag waits for (Brett's "4A"); answer 2's detached bind and `start-record` in every mode; `wip init`'s refusal; T017's five fixes, W2 and code#15.
  - Each value not yet known as a named `<placeholder>`, every one listed in a closing `## Before the tag` checklist. The workBenches re-vendor for the launcher's window reads (DEC-02, R41) is one of its items.
  - A last line naming the file's own path in `opensoft/openRepoTools-spec`.
- [ ] T343 [US10] [O] Run quickstart.md's steps against a scratch database on a workstation with PostgreSQL 16, write the run's record, and correct quickstart.md where a step's text is wrong, as detailed below — files: `../spec/specs/007-sql-register/quickstart.md`, `../spec/specs/007-sql-register/quickstart-run.md` — verify: `bash -c 'r=../spec/specs/007-sql-register/quickstart-run.md; command grep -qE "^- date: [0-9]{4}-[0-9]{2}-[0-9]{2}T[0-9]{2}:[0-9]{2}Z$" "$r" && command grep -qE "^- postgresql: 16(\.[0-9]+)*$" "$r" && for s in "bootstrap" "login add" "login add --dump-job" "status" "ready --expect index" "way back"; do command grep -qE "^- step $s: exit [0-9]+, expected [0-9]+$" "$r" || { echo "missing: $s"; exit 1; }; done && command grep -E "^- step " "$r" | sed -E "s/.*: exit ([0-9]+), expected ([0-9]+)$/\1 \2/" | while read -r a b; do [ "$a" = "$b" ] || { echo "exit $a, expected $b"; exit 1; }; done'`
  - **Source:** SK:364, re-scoped: quickstart.md is now the operator's and an engineer's first steps (phase A's fix round), not a run on the fixture.
  - **Depends on:** SPEC-PR; T328, T513 and T341.
  - The steps: the schema and the roles (`bootstrap`); the logins (`login add`, `login add --dump-job`); an engineer's file; `lanes-index status`; `ready --expect index`; and the way back.
  - **The run record,** `quickstart-run.md`, one line each: `- date: <UTC, as YYYY-MM-DDTHH:MMZ>`; `- postgresql: <the server's version>`; and for each step `- step <name>: exit <code>, expected <code>`, with the names `bootstrap`, `login add`, `login add --dump-job`, `status`, `ready --expect index` and `way back`. The pull request links it.
- [ ] T345 [US10] [S] Fix R61's wording in the change's `design.md`, before the change is archived: Y:4975's Q29 clause, which reads as though Q29 were open; T1's and T5's line folds (Y:489, 116 columns, and Y:2724, 84); F29's table row at Y:5954, marked superseded by D15; and rows 5 and 6 at Y:6031–6032, marked applied in D15 — files: `../spec/openspec/changes/add-sql-register/design.md` — verify: `bash -c 'f=../spec/openspec/changes/add-sql-register/design.md; ! command grep -qF "Under Q29(b), phases 1 to 3 do not change" "$f" && ! command grep -qE "^  when its own wait for .<n>. ends, each running to its own timer, .T_L. = .N_a.·.c. \+ 7 s \(9 s at .c. = 2, D9; the$" "$f" && ! command grep -qE "^   running to its own timer, .T_L. = .N_a.·.c. \+ 7 s, 9 s at .c. = 2 \(D9; the phase$" "$f" && command grep -F "outside any repository, system and global config off" "$f" | command grep -qF "superseded by D15" && command grep -F "is false for git 2.43" "$f" | command grep -qF "applied in D15" && command grep -F "as D15 still says" "$f" | command grep -qF "applied in D15"'`
  - **Source:** memo R61 (:795–806), items 1 and 2; plan.md's open points; the two marks, lane 3, fix round B, 2026-11-09.
  - **Depends on:** SPEC-PR; it runs before the change is archived.
  - The edit changes no SHALL and no requirement: it rewords the one stale clause and folds the two long lines, which lane 4's check of X found (memo R61's sources).
  - **The folds:** T1 at Y:489 and T5 at Y:2724 are each folded at a space to their paragraph's width, word for word.
  - **F29's table row** at Y:5954, the pin that runs `interpret-trailers`, gains "superseded by D15" in its last cell. D15 never runs `git interpret-trailers` (Y:559), as F29's own conclusion says (Y:5961), ruled on #165 6710770304. D15 governs (lane 3, fix round B, 2026-11-09).
  - **Rows 5 and 6** at Y:6031–6032, on D15's `commit.gpgSign` and `interpret-trailers` texts, each gain "applied in D15" in their last cell: D15 now carries both fixes (Y:552–559). D15 governs (lane 3, fix round B, 2026-11-09).

---

## PENDING: lane 2's block (due 2026-11-09 18:00Z)

Lane 2 writes the tests, from v3f, as T2xx task lines. Until its block is in,
these are the items the other blocks and the memo name. Each is
**placement: lane 2's**: lane 2's block gives its task, module and `-k`
word.

- **The skeleton's items:** L2-0a to L2-0e (Phase 0), L2-N1 to L2-N8
  (Phase 1), L2-W1 to L2-W6 (Phase 2), L2-G1 to L2-G6 (Phase 3) and L2-S1
  (the service), as the skeleton lists them (SK:516–607), with R29's
  additions: L2-0d follows contract §6 (with §3.5 and §3.7); L2-W1 to L2-W6
  name lane 4's group B plan §4.3's cases or say which item holds each; and
  a new L2-N item covers the bash suite's answer-2 cases (lane 5's A3)
  before T502.
- **T224**, the restore routes (lane 1's T109 and T110 name them), in lane
  3's restore order (research.md's ERR-03). Placement: lane 2's.
- **T230** stays (6726725691): the case for "never `system` for a private
  root, and `tls.crt` holds the leaf and every intermediate", at the tier
  lane 2 gives it. Placement: lane 2's.
- **Q-L2-3 is (a)** (6726708085, 6726725691): lane 4's T417 lists
  `tests/test_repo_hygiene.py` and adds each `_EXIT_3_CAUSES` entry in the
  same pull request as its `die … 3`, and lane 2 reviews that hunk.
  Placement: lane 2's review.
- **The new cases** (placement: lane 2's, each `[J3]`):
  - `tls_at_load` (T104);
  - `dump_restore_privileges` (T109);
  - `rotate_dump_job` (T110);
  - `guard_second_read_at` (T113), with an entry committed after form 2's
    read, so that the head has moved past `n`; its items 1 to 8, and item 9's
    ends 69, 78, 1 and 64;
  - `log_key_hex_slot` (T113);
  - `journal_last_pickaxe` (T114);
  - T507's own proof that form 4 started at or past `T0` + 3.75 s ends 2,
    writes no `.log.<key>`, and is noted, not refused: lane 5 suggests
    `test_a_second_read_past_the_start_line_is_noted_not_refused`
    (#165 6739068493);
  - T502's `test_lanes_accepts_decide_and_reads_as_without_it` (R38, as
    amended);
  - T401's three cases (lane 4's w11, #165 6742584818): each verb's exit 2
    on another path, such as `lanes/repos.tsv`, and the exit 2 on
    `lanes/log/README.md`, in files mode and on primary. The set is
    `lanes/LANES.md`, `lanes/aliases.tsv` and each `lanes/log/<lane>.md`
    (O21 (a), #165 6741093519, as lane 4 reads its log set; lane 3, fix
    round B, 2026-11-09).
- **Lane 4's `-k` selectors** (T401 to T434), each marked `[J3]`: they
  wait for lane 2's `[J3]` names, which come with lane 2's block at 18:00Z
  (#165 6741093519). Placement: lane 2's.
- **Fake-`psql` configurations** that set `register_url=` need a socket or
  a loopback host, or `verify-full` with a root file, since T104 refuses any
  other at load (L1:525). Placement: lane 2's.
- **The delayed-loopback relay:** a stdlib TCP relay with a named delay,
  before the primary fixture's TLS port, through which AT-D9c (d) and
  AT-F6's case 21 (T510) run, with the delay and TLS they name (lane 3's
  ruling on the test plan's open point 23, option B, #165 6741861590).
  Placement: lane 2's.
- **With v3f:** proving that each `[J3]` word ran, by one `-k` run per word
  or a check on the collected count, since inside an `or` group a misnamed
  word is skipped silently (lane 1's block, "Not settled in v4", item 3).
- **FR-059 and FR-060** (the tests, and `tests.yml`'s switch) are lane 2's
  block's to cover.

## Dependencies & Execution Order

Each task's own **Depends on** bullet is the authority. This section
summarises them by phase and by lane.

### Phase dependencies

- **The spec PR** gates every landing below (6726556972). Coding starts now.
  Every task's Depends on names `SPEC-PR`, but T344 rides the spec PR.
- **Phase 0** has no task dependency beyond the spec PR. Lane 2's fixture
  and switch (L2-0a, L2-0b) block every real-engine verify (L1:507). T101 lands lane 1's contract before
  group B codes against it, and T344 lane 3's before group C.
- **Group A** depends on Phase 0.
  - T103 comes first in `lanes-index` (lane 1's Question 11 PR); T502, the
    detached bind, is phase 1's first guard PR (#347).
  - T301 waits for T107; T302 for T301; T303 can start at once; T304 waits
    for T107 and T121.
  - Lane 5's T507 writes `register read --for guard`'s client half after
    T113, under lane 1's review (R07).
- **Group B** codes beside group A, against `contracts/register-cli.md` and
  the fake `psql`. It lands after slices 1a and 1b and after T111 to T114
  (L1:531). Its spine, T412 → T413 → T414 → T415 → T417 → T418, starts
  when slices 1a and 1b land; T416 runs beside it, after T415 and T321.
- **Checkpoint A+B** depends on groups A and B.
- **Group C** starts on T415 and T344. Lane 4's T416, the renderer's
  fork, lands after T321 (L4:247; SK:827 superseded, lane 3, fix round B,
  2026-11-09). Within group C: T320 comes first; T321 to T323 run in order; T324 to T327 follow T323;
  T328 comes last, after Checkpoint A+B.
- **The service** (T511 to T513) is built beside groups A and B, not after
  group C (Brett's "4A"; R35). It is coded and tested against the fixture's
  configuration while Q6(c)'s refusal stands, and no service PR lands before
  the spec PR. T512 waits on lane 1's T102 (met through T507), T103 to
  T106, T111, T113, T115 and T117; T112 where AT-10's fresh write uses it;
  T107 to T110 through lane 2's template; lane 3's T302; and lane 5's T504,
  T505 and T507. It does not wait on T328 or on group B (L1:449).
- **The release:** T342 can start at any time; T340 and T341 wait until each
  mechanism they describe exists; T343 waits for everything; T345 runs
  before the change is archived.
- **The v1.2.0 tag** waits for group C and the service (Brett's "4A").
- **Acts 3 and 4** are outside 007. They wait on the install, and act 4 also
  on slices 2a, 2b and 3, and on the service.

### Across lanes: the orders the rulings fixed

- **T409 lands after T105** (R03), and adds R17's mode carry across slice
  1a's `exec` (6726831985).
- **T403's and T408's `--decide` lines land after T502**, not after T509
  (R38, as amended, 6726831985 and 6726857112).
- **T423 lands after T506** (6726831985, 6726857112).
- **T424 lands after T122** (R04).
- **`REG_MODE`** is T409's, and T504, which lands after T409, uses it (R45;
  Q-L5-2; L4's T409).
- **T416 lands after T321** (lane 3's PB4; L4:247).
- **T426 lands after T425,** which adds R45's shared line (lane 4's decision;
  #165 6742584818).
- **T512's `status` lines** follow T117 (R44).
- **T501 lands before T502** (R50).

### Lane 4's order (L4:421–446)

- **Phase 1:** after the spec PR, T401 to T411 but T409 may land ahead of
  slices 1a and 1b (R23); T409 carries the mode across 1a's `exec`, so it
  lands after 1a. T401 comes first, then T402, T403 and T404, which use
  its verbs. T405 to T408 wait for no lane 4 task; T403 and T408 wait for
  lane 5's T502, and nothing waits for T509. T409 → T410 → T411 run in
  `lanes-edit.sh`, one after the other with T401. Every phase 1 task lands
  after its lane 2 module (L2-N4; L2-N2 for T409).
- **Phase 2:** the spine T412 → T413 → T414 → T415 → T417 → T418, after 1a
  and 1b. T416 runs beside it after T415 and T321, and no task's gate
  names it. T419 after T409 and 1b; T427 after T410 and 1b; T420, and
  T424 → T425, after T415 (T424 also after T122); T421 → T422 → T423 after
  T418; T426 after T409, T504 and T425; T429 to T432 after T410 and T417;
  T428 after T427 too; T433 and T434 after T417.
- **Gates every group B task carries here:** `SPEC-PR`, `SLICE-1A`,
  `SLICE-1B` and lane 1's T111 to T114 (L1:531). `STOP until T101` stays on
  the 11 tasks whose lines name it.
- **Lane 5's gates:** T401's primary branch waits for T505; T412 for T502;
  T413 for T504; T414 for T505; T423 for T504 and T506; T425 and T426 for
  T504, and T425's `who --landing` test for T505 too; T403 and T408 for
  T502.

### Lane 5's order (L5:569–689, with section D and the rulings)

- **T501 lands after the spec PR and before T502** (R50; lane 3, fix round B, 2026-11-09). T502
  is phase 1's first guard PR.
- **T503** after T502. **T504** after T409, whose `REG_MODE` and `lanes_index_bin` it uses (R45; Q-L5-2),
  T105 and T113. **T505** after T101, T504, T113 to T115. **T506** after
  T505 and T114. **T507** after T502, T504, T505, T101 to T104, T113, T121,
  T302 and T304. **T508** after T504 and T507. **T509** after T504 to T508
  and lane 4's T402 to T408.
- **T510** (group B) after T101, T502, T507, lane 4's T409, T410, T412 to
  T415, T417, T418, T427 and T428, and lane 1's T112 and T114.
- **T511 to T513** (the service) after the spec PR; T512 after T511 and
  the lane 1, lane 3 and lane 5 tasks its line names; T513 after T508,
  T511 and T512. Lane 1 reviews U5 before each lands.

### Within each phase

- The tests come first, and fail.
- Then the implementation, then the checkpoint.
- Each task is committed on its own, with explicit pathspecs.
- A file's pull requests land one at a time under Rule 6's `HOLD`. Two
  `lanes-edit.sh` tasks built in separate worktrees are rebased at landing
  one at a time (lane 4's order).

### Parallel opportunities

- T101 ∥ T102, in the spec leg.
- T303 ∥ T301: they touch different files.
- T340 ∥ T342.
- Lane 4's T402 to T408 are `[P]` with each other, and so are T428 to T434,
  since each touches only its own file (L4:421–446).
- Across lanes, group A's blocks run in parallel by function, but no two
  tasks of one file are `[P]`.

## Coverage: every FR and SC against the tasks

Built by script from each task's text, the blocks' own coverage lists and
lane 3's reading. A PENDING slot is named where lane 2's block will add
tasks.

| Requirement | Tasks | Also |
| --- | --- | --- |
| FR-001 | T104 |  |
| FR-002 | T409, T504, T507 |  |
| FR-003 | T105, T328, T409, T512 |  |
| FR-004 | T419, T504, T506, T507, T508 |  |
| FR-005 | T303 |  |
| FR-006 | T107 |  |
| FR-007 | T107, T108 |  |
| FR-008 | T107 |  |
| FR-009 | T108, T110 |  |
| FR-010 | T103 |  |
| FR-011 | T103, T104, T119 |  |
| FR-012 | T106 |  |
| FR-013 | T111 |  |
| FR-014 | T112 |  |
| FR-015 | T113, T507 |  |
| FR-016 | T114 |  |
| FR-017 | T101 |  |
| FR-018 | T409, T413, T415 |  |
| FR-019 | T414 |  |
| FR-020 | T111 |  |
| FR-021 | T412, T415 |  |
| FR-022 | T417 |  |
| FR-023 | T418, T422, T423 |  |
| FR-024 | T413, T415, T423 |  |
| FR-025 | T427, T428 |  |
| FR-026 | T420, T422 |  |
| FR-027 | T416 |  |
| FR-028 | T417, T428, T429, T432, T433, T434 |  |
| FR-029 | T428, T429, T430, T431, T432 |  |
| FR-030 | T118, T122, T424 |  |
| FR-031 | T421 |  |
| FR-032 | T122, T424, T425 |  |
| FR-033 | T401, T403, T408, T504, T505, T506, T508, T509, T513 |  |
| FR-034 | T505, T506 |  |
| FR-035 | T410, T411 |  |
| FR-036 | T401, T402, T403, T404, T405, T406, T407, T408, T509 |  |
| FR-037 | T507 |  |
| FR-038 | T501, T502, T507, T510 |  |
| FR-039 | T503 |  |
| FR-040 | T502, T507 |  |
| FR-041 | T501, T510 |  |
| FR-042 | T301 |  |
| FR-043 | T302 |  |
| FR-044 | T321, T322, T344 |  |
| FR-045 | T320, T344 |  |
| FR-046 | T323 |  |
| FR-047 | T324 |  |
| FR-048 | T117, T325 |  |
| FR-049 | T115 |  |
| FR-050 | T116, T327 |  |
| FR-051 | T304, T326 |  |
| FR-052 | T115, T116, T326 |  |
| FR-053 | T109, T115 |  |
| FR-054 | T109, T110, T119, T120 |  |
| FR-055 | T119 |  |
| FR-056 | T121 |  |
| FR-057 | T511, T512, T513 |  |
| FR-058 | T512, T513 |  |
| FR-059 | — | PENDING-L2: lane 2's whole block |
| FR-060 | — | PENDING-L2: L2-0b, the switch in `tests.yml` |
| FR-061 | T512 | PENDING-L2: test 9's and test 10's figures in the PR bodies |
| FR-062 | T340, T341 |  |
| FR-063 | T303, T512 | every task; PENDING-L2: the hygiene suite |
| SC-001 | T328, T409, T415, T419, T425 | PENDING-L2: every agreement test |
| SC-002 | T507, T512 | PENDING-L2: test 10's figures |
| SC-003 | T502, T507 |  |
| SC-004 | T502 |  |
| SC-005 | T512 |  |
| SC-006 | T412, T415, T417, T418, T503, T510 | PENDING-L2: tests 4 and 12 |
| SC-007 | T322, T328, T416 |  |
| SC-008 | T105, T303, T401, T402, T403, T404, T405, T408, T409, T419, T424, T426, T502, T503, T504, T505, T506, T508, T509 |  |
| SC-009 | T304, T326, T419 | PENDING-L2: L2-G4, test 8 |
| SC-010 | T103, T507, T512 |  |
| SC-011 | T415, T507, T512 | PENDING-L2: tests 9 and 10 |
| SC-012 | T303, T401, T409, T410, T411, T412, T414, T415, T417, T418, T419, T421, T423, T424, T425, T426, T427, T502, T504, T505, T506, T507, T508, T509, T510, T512, T513 | every task's regression run (`repo_hygiene`); PENDING-L2: the hygiene rules |

**Gaps**, with no task yet: FR-059, FR-060.

## Coverage outside this task list

These are acts, not build tasks. Each is Brett Heap's, or a lane's on his
word.

| Item | Met by | When |
| --- | --- | --- |
| The spec PR | lane 3, on Brett's own word naming its list | before any 007 code PR |
| The root switch R1 and the root `AGENTS.md`'s suite account R2 | lane 1 (U11), on the standing word | R1 after the spec PR and after lane 2's L2-0b has landed in the code leg, and before group A's first database PR (L1:431, :522) |
| Each group's composed rehearsal R3, and the final root bump R4, by squash | lane 1 (PC-7) | before each group lands; after the legs land |
| PostgreSQL 16 in the bench images, and the image rebuild | lane 2's openBenches PR; Brett rebuilds | before bench runs need it |
| v1.2.0's rc1 on TestPyPI, the tag, `release/v1.2`, PyPI and the GitHub Release | lane 3, on Brett's own word | after group C and the service |
| The workBenches re-vendor, which carries the launcher's window reads (Brett's "5A"; DEC-02) | lane 3 drafts; applied once the code is on `main` | with the release |
| The codeXfactory prod install, and the prod OpenXPKI that issues its certificate (Brett, 2026-11-09) | opsXfactory-6's change `add-codexfactory-prod-register-database`, in Opensoft-Tenant's own `codexfactory` folder, with its own Flux sync entries and reconciler, in the namespace `codexfactory-register` (#165 6743069107); for the prod OpenXPKI, opsXfactory-6 or the lane it names as `OpenXPKI-Install`'s owner (#165 6742844912, 6743069107, 6744967970) | before act 3, which waits until that change has landed and the prod OpenXPKI issues the certificate |
| The push ruleset | Brett | inside act 4's freeze |
| Acts 3 and 4, and a real rollback or disaster path | Brett | after the install |
| The change's archive | lane 3 | after the root bump that carries 007 lands, and after T345 |

## Notes

**Tasks by lane and tier** (lane 2's block is PENDING):

| Lane | `[O]` | `[S]` | `[H]` | All |
| --- | --- | --- | --- | --- |
| L1 | 22 | 0 | 0 | 22 |
| L3 | 16 | 3 | 0 | 19 |
| L4 | 23 | 11 | 0 | 34 |
| L5 | 12 | 1 | 0 | 13 |
| All | 73 | 15 | 0 | 88 |

- **Every task** carries one tier tag, a `files:` list and a `verify:`
  command, and names its source line.
- **Network:** every verify command is offline. T320's tests use bare-origin
  fixtures.
- **When lane 2's block comes in,** lane 3 replaces the PENDING section and
  checks the coverage table again.
