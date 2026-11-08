# The test plan

Lane openRepoTools-2's test plan for opensoft/openRepoTools#165
(`add-sql-register`), `openspec/changes/add-sql-register/test-plan.md`, beside
`design.md`, whose test-plan section is a short summary pointing here (lane 3,
#165 comments 6576888068 and 6578688322).

**The frame it is written against.** Lane 3's commit `21f0191c` on
`add-sql-register` (comment 6708646285), which folds lane 1's section (v2,
v2.1 and v2.2), lane 2's measurements, the proposal's stale passages and the
follow-ups of 2026-11-02 onto the frame review's fold (`6a5fcdc6`) and lane
5's section (`1c45123a`):
- `design.md`, 3,588 lines, sha256
  `df00e47f08606ccd013d3d3222e07b331e02e6a920c58189c2be851afb91fb9e`;
- `operations.md`, 561 lines, sha256
  `a0aa4264119c22f889982370745c060ae37a39d0c9203ab80b1f62625c241b60`;
- `proposal.md`, sha256
  `e6475a467f217b389129ad10b7d40904e03e3b008bbe5c6d7a6192657cbad63f`;
- the sql-register delta, sha256
  `36e2bf42cbcc561134aafae8607fcde10ff457d477bf818dcfbdd6c9afbe6a39`;
- the register-service delta, sha256
  `57a365c85de1c7fd9301a5d530488cba0defe04be43ea48180677c94c95f91e7`.

Lane 3's settlements this plan applies are in comments 6578020193,
6578269040 (the rulings F1 to F33), 6578688322 (this plan's open points 9, 21,
22 and 23 as first numbered), 6579459518 (lane 1's five points, and lane 2's
measurements of 6579356984), 6579562840 (lane 4's Q6(c) code and renderer
lock, lane 5's admin connection and declared-outage read), 6708646285 (the
fold at `21f0191c`) and 6708889286 (lane 4's section accepted). Lane 1's part
is in two files: part 1, "The schema", is `design.md`'s, with §1.1 to §1.6
and §1.3.1, "`commit_write` in full"; part 2 is `operations.md`, with §2.1 to
§2.9. The tests use the names of lane 1's v2.3 (#165 comment 6708990159,
`section-lane1-v2.3.md`, sha256
`fc2609e80fa8b3d6968fda822a1872f2f7765eaa3b0cf1524cfd99a78b826a28`), which
lane 3 folds into both files before this plan: `restore_prior`,
`repo_spellings`, `not_after` and LR008 among them. Lane 4's section, "The
phase plan" (#165 comment 6708867768,
`section-lane4-5f26bf0a.md`, sha256
`5f26bf0ae1ed1ded51dcf685902a5ad2d643695a9de09f7e5d55b529ee74c4ff`), which
lane 3 accepted as posted (comment 6708889286), settles the phase plan, the
detached bind and the renderer.

**Since `21f0191c`.** Brett has answered rounds 1 and 2 of #165's
questions: round 1 confirmed, and round 2 taken as recommended in every
question, both at 2026-11-07T15:26:24Z (lane 3, comment 6710653292;
section 1.4). Lane 3 has ruled on lane 1's five points of v2.3, this
plan's open points 17 to 19, LR008's reading and lane 4's open rows
(comment 6710770304), which settled every `[L3-PENDING: n]` and
`[L4-OPEN: row n]` mark this plan had carried, and has named open point
19's value (comment 6711028142).

**Which pin applies to what.** The frame above, the proposal's `P<n>`
(pinned to `proposal.md` at `21f0191c`, as "How it cites" says) and every
design and operations section the tests cite by name stay at
`21f0191c`, and each cited name was checked in commit I too (lane 2,
2026-11-07). Only section 6.2's coverage tables are re-pinned, to lane
3's commit I, `2be70f5a80cec110ee1a2a5c6910642d1854ef42` on
`add-sql-register` (comment 6711028142), which adds one requirement. Its
files:
- `design.md`, 4,938 lines, sha256
  `ba4996c6e717337c0ef535b7e3f46cd3d7f928cffb48b27a861bcf9e7d58d3f7`;
- `operations.md`, 576 lines, sha256
  `79e1ccd51b9b1423666bf20190f3a192b21bfd0ac901efd369d29068dff9851d`;
- `proposal.md`, 2,205 lines, sha256
  `01891436a6fccec6d5770fa8772e58f1ba4ba82c79d5c67d7e970894acaac0d1`;
- the sql-register delta, 903 lines, sha256
  `9924ab707635b9e380fad5a5358b40a258558341429ec8faec3b8bb53be92aa8`;
- the register-service delta, 248 lines, sha256
  `deb3c32ef2400861664fa6d7831e9cc5d26fc3449cec8c637632d9b2200af257`.

Section 6.2's tables stay pinned at commit I (`2be70f5a`). Lane 3 has
since folded commits J to T on the same branch, among them the arms
requirement renamed for the seven read arms, and LR009 and LR010 as
behaviour. Lane 2 re-pins the tables to the then-current commit in the
pass after lane 3's review, together with the tests owed there:
- LR009's and LR010's;
- v2.4's act and its generation;
- one test per after-COMMIT tail;
- S7's agreement test (lane 3, comment 6714089433).

**How it cites.**
- `D<n>` is a decision of `design.md`, `SVC-<n>` a section of its register
  service, and an S-section or a name in quotes is `design.md`'s heading. A
  bare `§1.n` is a section of `design.md`'s "The schema", and a bare `§2.n`
  a section of `operations.md`. No citation of `design.md` or
  `operations.md` is by line.
- Lane 4's section is cited by its headings, as "lane 4's section, '…'", and
  its table of differences by row, as "lane 4's row n".
- The delta specs are cited by requirement name. Section 6.2's tables
  follow commit I's 83 requirements; every other citation of one is at
  `21f0191c`, except where the text names commit I, and each such name
  is the same at commit I.
- The proposal is cited by question, test or section where it can be, and
  otherwise as `P<n>`, a line of `proposal.md` at `21f0191c`. Each `P<n>` is
  pinned to `proposal.md` at `21f0191c`, and lane 3's later proposal commits
  may move those lines. `C<n>` cites `clarifications.md` and `CR<n>`
  `council-review.md`, both unchanged since `1c45123a`.
- "R1" is lane 2's test-strategy report (#165 comment 6547987633), "the probes"
  lane 2's measured probes of 2026-10-31 (#165 comment 6549116835), "the
  measurements" lane 2's (14) and (16) of 2026-11-02 (#165 comment 6579356984),
  and "the code leg" opensoft/openRepoTools-code at `2a057a6d`, whose paths are
  written `code/…`.
- "The LR006 probe" is lane 2's probe of 2026-11-07 on PostgreSQL 16.15,
  `PROBE.md`, sha256
  `6a822999178a7ee92ba577f3591cfa8ef3923fc07e7282fac95671d3d6d2034f`.
- One mark names what a test still waits on: `[BRETT: row n]`, a row lane
  4's section puts to Brett, which goes to him with the spec PR. Two other
  marks were settled and retired (lane 3, comment 6710770304):
  `[L3-PENDING: n]` had marked lane 1's five points of v2.3, and
  `[L4-OPEN: row n]` a row lane 4's section had left to lane 3 or lane 1.
  Section 7 lists all three.

Two kinds of label look like line citations and are not. A bare P1 to P20, as
in "(P3)", is a council finding id (`council-review.md`'s product findings),
never proposal line 3. And C5, C8 and C10, as in test C5, are the council's
ADV-C5, ADV-C8 and ADV-C10, never `clarifications.md` lines.

This plan adds no requirement. Every test it names checks a requirement the
proposal, the frame or the delta specs state, and where a test needs a
decision none of them makes, section 7 lists it.

## 1. Test kinds and assumptions

### 1.1 Three kinds of test

- **Real-engine (RE).** The test runs the real `psql` client against the suite's
  own disposable PostgreSQL cluster (section 3.1; R1's option (a), P1145–1150).
  It proves what only an engine proves (P842–845): a byte-identical render from
  real tables, SERIALIZABLE order and its 40001, real GRANTs, import and
  rollback round trips, atomicity of a write that fails partway (P11), and the
  measured cost. A `psql` wrapper placed in front of the real `psql` (a "shim")
  keeps a test RE: the shim injects the fault, and the engine decides the
  outcome. Tests 4 (the window) and 12 (the in-doubt COMMIT) are built that
  way. Test 2's partial failure needs no helper at all: its entry's last
  structured row breaks a CHECK, as §1.3 proposes.
- **Fake-psql (FP).** A fake `psql` first on `PATH`, in the pattern of
  `code/tests/test_lanes_index.py`'s `FAKE_PSQL`. It records each call's
  argv, connection string, `PGPASSFILE`, whether `PGPASSWORD` is set, and its
  stdin, as JSON lines, and answers only the few lines it is taught. It proves
  argv, connection strings and credentials, the 69 refusal, and the SQL's shape
  (P850–852). It proves nothing an engine decides. No FP test asserts an
  ordering, a constraint, a GRANT or a row count.
- **Files-mode (FM).** No database is involved. The workspace repository is a bare
  origin with clones under the test's temporary directory, the way the suite
  builds estates today (`code/tests/conftest.py`'s "BARE REPOSITORIES IN A
  TEMPORARY DIRECTORY"). It proves the files-mode side: the marker's refusal
  (test 3), the files-mode twin that is test 1's second oracle, and the
  files-mode writes after a rollback (tests 8 and 13).

A test that drives `lanes-edit.sh`, `lanes-index` or a lifecycle script runs
the checkout's own copy, never an installed one: `test_lanes_index.py` already
takes the user's installed-tools directory off `PATH` where it carries a
`lanes-index`, and every
module here does the same.

### 1.2 Where each kind runs, and what CI refuses

| Place | RE | FP and FM |
|---|---|---|
| CI's `tests` and `tests-no-submodule` jobs (`ubuntu-24.04`, `python3 -m pytest tests`) | run; a missing engine is a failure under the switch (section 3.3) | run |
| CI's `guard-launch-mode` job (`-k 'guard_launch_mode or repo_hygiene'`) | not selected | not selected |
| CI's `package` job (three named modules) | none among its modules; with no engine, the eager switch refuses its run, so it goes red too (section 3.3) | not run |
| the root's `composed.yml` job `exact-pins` (`code/tests/run.sh -rs`, `ubuntu-24.04`) | run; refuses under the switch, which the root sets itself, since `OPENREPOTOOLS_COMPOSED=1` does not imply it (P2128–2133) | run |
| py-bench and cloud-bench before Q1's 1A image rebuild (P1137–1138) | skip loudly, naming `PG_BIN` and `postgresql-16` | run |
| the benches after that rebuild | run | run |
| a Mac | skip loudly unless `PG_BIN` names a server's binaries; cleanup is the sweep alone, since `PR_SET_PDEATHSIG` is Linux-only | run |
| a self-hosted runner whose image lacks the binaries, such as the Eagle runner's `eagle-fleet-worker:ubuntu24` | not refused: the variable moves to the jobs that have the binaries (section 3.3) | run |

No CI job runs the suite on macOS. `release.yml`'s `macos-latest` leg runs
`--install` only (R1, section 1). So the switch, scoped to the jobs that have
the binaries as the product seat asks (P17, C92–103), covers every job that
runs the RE modules. A new RE test's name must not contain `guard_launch_mode`
or `repo_hygiene`, or the `guard-launch-mode` job would select it, and a
hygiene rule checks this (section 3.3).

### 1.3 The phase split, and which tests belong to which phase

Round 1's 1A (Q6(c), split by seam; P1392–1418) gives three phases:

- **Phase N, now, on code `2a057a6d`.** This is everything off `lanes-edit.sh`'s
  write seams:
  - `lanes-index`'s schema, journal, switch and roles;
  - `import --at`, with its scratch render and proof;
  - `status` and `reconcile`;
  - the renderer as a pure function from the journal to file images, with no
    git;
  - the service and its read API;
  - the readers' SQL path.
- **Phase W, after slices 1a (#282) and 1b (#307) land.** This is the write
  path: S1's transaction at the commit seam, the refusals, S6's pre-check and
  hold order, and the marker check after S3's fetch. It also ships the
  renderer's fork and `<c>`, and test 3's unit form, with the marker
  committed by hand (lane 4's rows 10 and 8, which lane 3 ruled in comment
  6710770304).
- **Phase G, then.** This is the renderer's commit and push, rollback's push,
  and agreement tests 3, 5 and 6, test 3 with the marker pushed by
  `cutover`, which has no case yet (open point 20). Its code is
  `lanes-index` and `tests/` only (row 10).

Until phase G is installed, `lanes-index` refuses `mode=primary`
(P1404–1412). So a phase-W test that writes through `lanes-edit.sh` on
`mode=primary` runs against a test copy of `lanes-index` with the build
constant flipped, the second state of test 14. How a test flips it is open
point 3.

| Test | Phase | Kind |
|---|---|---|
| 1 | N (the pure renderer against an import); W (each verb, scratch mirror); G (the mirror on origin) | RE, with an FM twin |
| 2 | W | RE and FP |
| 3 | W, its unit form (lane 4's row 8); G, with the marker pushed by `cutover`, no case yet (open point 20) | FM |
| 4 | W | RE |
| 5 | G | RE |
| 6 | G | RE, with an FM stale writer |
| 7 | N; the cutover's cases G | RE |
| 8 | G | RE |
| 9 | W (writes and verbs); N (the guard's parts) | RE, measurement |
| 10 | N | RE and FP |
| 11 | G | RE |
| 12 | W | RE |
| 13 | G | RE |
| 14 | N and W, and kept after G | FP for the refusing state; RE for the flipped one |
| R, the roles | N | RE |
| C5, the callers' default arms | N | FP |
| P3, the outage's ways out | N; the `rollback` case G | FP and RE |
| P8, the operator tools | N | RE, FP and FM |
| C8, `LANES_NO_FETCH=1` | G | RE |
| C10, the alias race | W | RE |
| D8, the pending record | W | RE |
| D9, the timeouts table | N; W for the writer's rows; G for `rollback`'s | RE |
| D19, two spellings at once | W | RE |
| D20, the declared outage | N | FP and FM |
| D22, the disaster path | G | RE |
| AT-SET, AT-MIR, AT-SPL, AT-LR5, AT-VER, AT-LR4, AT-LR006, AT-F33, lane 1's objects | AT-MIR and AT-VER N, AT-MIR's gating case G; AT-SET, AT-SPL, AT-LR5, AT-LR4, AT-LR006 and AT-F33 W | RE |
| AT-F1, AT-F2, AT-F4, AT-F18 | G | RE |
| AT-F3, AT-F5, AT-F10, AT-F14 | W | RE |
| AT-F6, AT-F7, AT-F11 | N, or W for AT-F6's writes | RE and FP |
| AT-F8 | W | FP and FM |

A test is written in the phase that builds what it checks, and lands with that
phase's code. Test 1's phase-N and phase-W parts stay in the suite after phase
G, because they check the pure renderer and the transaction without git.

### 1.4 Brett's answers, and what is still open

Two answers share the labels 1A and 2A. Q1's 1A and 2A were decided on
2026-10-31: the bench images gain PostgreSQL 16, and the real-engine tests are
required in CI (P1134–1171). Round 1's 1A to 4A are a different set of answers,
written out below. This plan always says which set it means.

- **Decided (Q1, P1134–1171).**
  - The fixture is R1's option (a).
  - The real-engine tests are required in CI, and CI refuses rather than skips
    them.
  - Elsewhere they skip loudly.
  - The fake-`psql` tests always run.
  - CI adds no `services:` container and no new job.
- **Round 1, confirmed by Brett** at 2026-11-07T15:26:24Z, "Confirm all
  four (Recommended)" (lane 3, comment 6710653292). Lane 3 had held his
  answer of 2026-11-02 provisional until he confirmed it (P1127–1132). The
  answers are recorded at P1195–1202 for 4A, P1462–1470 for 1A, P1599–1605
  and P1632–1637 for 2A, and P1828–1832 for 3A:
  - **1A, Q6(c):** the split by seam above;
  - **2A, Q8 option A, with ADV-C5 and P15:**
    - in `lane-start`, the deciding reads refuse with 69 before anything is
      written, and so do `request-handoff` and a new lane's `add-row`; the
      later stamps print a note, and the launch goes on;
    - in `lane-handoff` and the skill, the handoff file is still written, from
      a mirror read that says so; the `PAUSED` record refuses with 69, and the
      lane stays `SWAPPING` until the late path completes it;
  - **3A, Q20(B):** `mode=primary` is written at the cutover, so before it every
    workstation is `mode=index`;
  - **4A, Q2(a) and (b):**
    - each `lanes-edit.sh` write opens its own transaction, with its own
      REGISTER-WRITER credential;
    - the service holds one connection, for the guard's reads and the other
      reads;
    - with the service down, writes go on, and never fall back to the files.
- **Round 2, answered by Brett: every question as recommended,** at
  2026-11-07T15:26:24Z, "Take all recommended (Recommended)" (lane 3,
  comment 6710653292; the questions are lane 3's `brett-165-round2.md`,
  sha256
  `79730d13123b94791c3bfcb7c2fd89212c40ada18e69c554c50cd2a0211ffe21`).
  - **The eight it set apart** go as lane 3 recommended (P1119–1126): Q4(a)
    (i); Q4(b) (iii); Q7 (A″), with "register busy" as exit 75; Q21(b)
    (B); Q22 (a); Q26 (a′), with the outage also declared at a rollback's
    start; Q28 (i) and (a); and Q29 (a).
  - **The rest:** Q4(c) (i), Q4(d) (i), Q11 (a), Q12 (a), Q13 (a), Q14 (a)
    for both halves (`wip init` refused under `lanes/STORE`, and the
    `LANES.md` link kept for reading only), Q17 (B) with P9's non-zero exit,
    Q18 (A), Q19 (A), Q21(a) (A), Q23 (a), Q24 (a), Q25(a) (i), Q25(b) (a),
    Q25(c) (a) and Q27 (a). Under Q27 (a), the renderer takes S2's
    preferred private-index form (P326–331).
  - **The design's, beside them:** Q15 follows Q7. Q16 takes (b), the write
    id (P253–267), with the report's code 3 (D8).
- **Still open** (comments 6710653292 and 6711028142): Q29's time limit;
  Q22's database host, its backups and how long it may be down; Q28's two
  dates, one for 1a and 1b and one for the other four; and Q21(b)'s
  ruleset paths, `lanes/**` as the round-2 file words his answer, or the
  render set and `lanes/STORE` as D23 and `operations.md` have it. Those
  are Brett's to name. Whether the org's GitHub plan supports the ruleset
  is not yet checked.

A test that turns on round 1 or round 2 is written under Brett's answer.
Where it still rests on one of these open points, its entry says so, in
its options. An entry's other options are the frame's or the design's.

### 1.5 The frame's decisions, and where they change an oracle

Where the frame decides what the proposal left open, the frame wins, and the
entry says so in one line. The decisions that set an oracle:

| Decision | What the tests assert |
|---|---|
| D2: `lanes-index` is the one database client | Faults are injected at its `psql`, through `PATH` (section 3.2). The commands are `mode`, `register snapshot`, `register commit`, `register read`, `journal`, `render`, `import --single`, `render --revert`, `outage declare`, `outage clear`, `cutover`, `serve`, and §2.4's `bootstrap`, `ready` and `set-mode`. |
| D3: the mode is asked only where a configuration exists | test 14 |
| D4: on primary, the commit seam collects and never commits | test 2's "git unchanged", and test 1 |
| D5: one transaction per verb, with `start-record` as `lane-start`'s one composite verb, and F5's expectations as `act-begin` plus six scopes (lane 4's section, accepted in comment 6708889286) | one journal entry per verb (tests 1 and 11); AT-F5; AT-F6 |
| D6: the head row before the journal row | test 4 and section 4.1: no exit on a 23505 |
| D7: "register busy" is 75, at a 30 s deadline, set for tests by `LANES_REGISTER_DEADLINE_S` | tests 4 and C5, section 4.1 |
| D8: in doubt, 3 after 10 s of trying to reconnect, naming the id; a rerun reuses it through `LANES_WRITE_ID` or the pending record | test 12, AT-D8 |
| D9: the timeouts table | AT-D9, one case per row; section 4.4 |
| D10: a refused direct write is 2; the guard maps 69, 75 and 3 to 2; `lanes-index`'s register subcommands use 69 | tests 2, 8 and 10 |
| D11: `register-row-local` reads the database on primary | test C5 |
| D12: `LANES_NO_FETCH` has no effect on a primary write, and the renderer is forked without it | tests 8 and C8 |
| D13: a line's UTC is the database's clock at the snapshot | test 1 |
| D14: the renderer is the writer's fork, with `lanes-render.lock`, an `fcntl.flock` in `SyncLock`'s form with both halves of its handshake, and `lanes-render.rerun` in the checkout's git directory (lane 4, comment 6579524611; lane 3, 6579562840) | test 5 (with [L4-4]'s kill), and the render wait (open point 8) |
| D15: a render commit carries the journal's recorded identity, and `Register-Seq: <k>` in its message's last paragraph, which `lanes-index` composes (lane 4's section, "F29") | test 5 |
| D16: the divergence record is in the database, and a revert is an empty journal entry | test 6; AT-MIR |
| D17: readers go through the service, then a direct READER read | tests 10 and C5 |
| D18: the later `HOLD` or `LANDING` writer exits 2 | test 4; AT-LR5 |
| D19: a claim's spellings are checked in the transaction | tests C10 and D19; AT-SPL; AT-LR5 |
| D20: a declared outage is a file beside `lanes-index.conf`, with an expiry | AT-D20 |
| D21: `lanes/STORE`'s fields, `lanes-tree=` among them | test 7 |
| D22: the disaster path re-imports at the mirror's tip | AT-D22 |
| D23: the renderer pushes with a deploy key named by `render_key=` | test 5 |
| D24: exactly two operator entries, the cutover's and the rollback's last, flagged and never rendered; besides the import's baseline, nothing else is journaled while the switch is not `primary` | AT-F1; AT-F2; AT-SET; AT-LR4 |
| D25: `lanes-index cutover` is a command of its own | test 7; AT-F2 |

The guard's bound is the frame's too: exit 2 in under 4.5 s in every case
("One budget"), and it stays (lane 3, comment 6578688322).

## 2. The agreement tests

A heading's `AT-n` is the text's "test n": AT-4 is test 4, AT-C10 is test
C10, and AT-D9 is test D9. Lane 1's schema objects keep their full labels,
AT-SET, AT-MIR, AT-SPL, AT-LR5, AT-VER, AT-LR4 and AT-LR006 (with AT-LR006-1
to -6), and so do the frame review's: AT-F1, AT-F2, AT-F3, AT-F4, AT-F5,
AT-F6, AT-F7, AT-F8, AT-F10, AT-F11, AT-F14, AT-F18 and AT-F33, which sits
beside AT-LR006, with F19 and F33 in a closing note.

Each entry gives what the test proves, its phase and kind, its fixture, its
oracle, the options left open, its test names, its rough run time and what
could make it flaky. The fixture's common parts, the cluster, the database per
test and the fake `psql`, are in section 3, and each entry names only what it
adds. "The imported register" is test 7's fixture register after a successful
`import --at`, cloned per test from the template database (section 3.1). The
test modules are new files under `code/tests/`, named `test_sql_register_*.py`
in the code leg's style. Their names are proposals for the design to keep or
rename.

### AT-1. The mirror byte-identical to a full render after each of S1's writes

- **Proves.** After each of S1's writes, the mirror is byte-identical to a full
  render from SQL (P853–854; S2's tests, P399–400). The writes are the thirteen
  verbs of P192–196 (`log` once per verb of its grammar), and the managed
  projection writer's storage-layer entry, driven by a fake writer (S4,
  P501–506). With it, the test checks four more things:
  - every row the write touched reads back `primary` as the mode that wrote it
    last (P183–188);
  - the journal entry carries the write's commit message exactly as today
    (P299–303);
  - the line's UTC is the database's, in whole seconds (P665–674);
  - the renderer touches nothing outside its four paths (P307–309).
- **Phase and kind.** The test has three parts, all RE.
  - **N:** the storage layer's write entry, called directly as the projection
    writer will call it, then the pure renderer.
  - **W:** each verb through `lanes-edit.sh` on `mode=primary` (the flipped
    build, open point 3), with the pure renderer writing a scratch mirror.
  - **G:** the same verbs, with the mirror read from the bare origin's `main`
    after the forked render.
  - Part W adds an FM twin.
- **Fixture.**
  - The cluster's database for the test, holding the imported register.
  - A workstation configuration with `mode=primary`, `register_url=` naming
    the cluster's socket, and a 0600 `register_passfile=`.
  - In part G, a bare origin and the lane's register checkout,
    `<W>-lanes/<lane>/` (Amendment 23).
  - The twin: a files-mode clone at the import commit, under its own `$HOME`.
  - The fixture register holds:
    - three lanes, one of them archived;
    - `aliases.tsv` entries;
    - a lane with `HOLD`, `LANDING` and `LANDED` lines;
    - a log with `WORD` lines whose UTC carries milliseconds (Q4(d)(i)).
  - Every verb runs from the same starting state. Each case is parametrized by
    verb and gets a database of its own.
- **Oracle.**
  - **The mirror.** Over `lanes/LANES.md`, `lanes/log/*.md`,
    `lanes/aliases.tsv` and `lanes/archive/**`:
    - the mirror's path set equals the full render's;
    - every file's bytes are equal;
    - every path outside those four is unchanged.
  - **The journal.**
    - It gains exactly one entry, at the old head plus one.
    - Its message equals the twin's commit message, byte for byte.
    - It carries a `write_id`, unique in the journal.
  - **The rows.** Every row the entry touched reads `primary` as its last
    writer's mode.
  - **The UTC.** The new line's UTC matches `YYYY-MM-DDTHH:MM:SSZ`, and lies
    between the database's clock read before the write and after it, with
    both reads floored to whole seconds, since the line's UTC is (S6). It is
    the database's clock at the attempt's snapshot (D13).
  - **The twin (part W).** The mirror equals the twin's files after the twin
    makes the same verb, with each line's UTC field masked in both. Open
    point 4 asks for a clock the test can fix instead.
  - **Part G.** The bytes are read from `origin/main` by object.
    - The lane checkout's HEAD, index and working tree are unchanged (Q27(a),
      the private-index form).
    - Origin gains exactly one commit, whose `Register-Seq:` trailer is the
      entry's seq, in the message's last paragraph in `Key: value` form, so
      that `git log --format='%(trailers:key=Register-Seq,valueonly)'` reads
      it, as the guard's bounded lookup does (AT-F11).
- **Tests.** In `code/tests/test_sql_register_render.py`:
  - `test_a_storage_layer_entry_renders_byte_identical_to_a_full_render[<entry>]`
    (N, the managed projection entry among the cases);
  - `test_each_write_leaves_the_mirror_byte_identical_to_a_full_render[<verb>]`
    (W);
  - `test_each_write_matches_its_files_mode_twin_byte_for_byte[<verb>]` (W);
  - `test_each_write_reads_back_primary_as_the_mode_that_wrote_it_last[<verb>]`
    (W);
  - `test_the_mirror_on_origin_equals_a_full_render_after_each_write[<verb>]`
    (G);
  - `test_rename_lane_commits_the_transaction_then_a_rerun_makes_only_the_move`
    (W) [BRETT: row 18]: the handoff paths leave the change set, the row's
    handoff cell is rewritten in the transaction, and the move and the
    stamp run as `rename-lane`'s after-COMMIT tail (lane 4's section, "Each
    write verb on a scratch tree"). With the move made to fail after the
    commit (its target directory read-only), the journal holds the rename
    once, and a rerun adds no entry and makes the move; a handoff that
    cannot be stamped refuses before anything is sent. A rerun that finds
    its write id committed runs the tail (lane 4's row 24, as lane 3 ruled
    it in comment 6710770304), which AT-D8 asserts;
  - `test_a_caller_composed_utc_and_log_paused_utc_stay_as_written` (W): a
    UTC the caller wrote into the line, and `log PAUSED`'s, is stored and
    rendered byte for byte, not replaced by the database's clock;
  - `test_on_primary_a_write_does_not_nudge_a_register_sync` (FP): after a
    primary write, the log of a stub `lanes-index` holds no
    `sync --source register` call, and the inventory nudge still runs;
  - `test_sync_source_register_does_nothing_on_primary_and_says_so` (FP): it
    makes no `psql` call, writes nothing, and its output says it does nothing
    on primary.
- **Run time.** There are about 25 cases per part. Each case costs a database
  (16–62 ms, R1), one `lanes-edit.sh` run and its twin's, and a render.
  `lanes-edit.sh`'s run is unmeasured; test 9 measures it. Estimated at
  0.3–0.8 s a case, the test takes 20–60 s for all three parts.
- **Flakiness.**
  - A second boundary between the twin's clock and the database's is masked.
  - The order of the archive's directory listing is removed by comparing
    sorted path sets.
  - Locale is fixed with `LC_ALL=C` in every subprocess.
  - In part G, the commit's dates come from the journal (P1365–1367), so the
    sha does not depend on the wall clock.

### AT-2. Refusal, 69, when the database is unreachable; failure partway through a multi-file write (P11)

- **Proves.** This test covers P855–858 and S1's tests (P282–295):
  - each of S1's writes exits 69 when the database is unreachable, and when it
    refuses the transaction;
  - the register's rows, the journal and git are unchanged, and no lock is
    left;
  - on the real engine, a failure injected in a late statement of each write
    that changes several files leaves the journal, the line store and the head
    row unchanged. The writes are `LANDING`, `LANDED`, `rename-lane`,
    `retire-rows`, `archive-rows` and `migrate-state-cells`, which is P11.

  S1's tests add:
  - a hand-edit `commit` refused;
  - a queued handoff commit still published;
  - a queued `lanes/` commit refused (P290–291);
  - nothing ever written to the files instead (P213–215).
- **Phase and kind.** W. RE for the refused transaction, the partial failure,
  and the unchanged rows. FP for the refusal's argv, credentials and text.
- **Fixture.** Test 1's workstation and imported register. The delta spec's
  "Every database path is bounded" names a silent listener under this test.
  That case is AT-D9's writer rows, so this entry keeps the refusals that
  answer at once. Each fault is injected as follows.
  - **Unreachable (RE).** The writer's `register_url=` names a fresh, empty
    socket directory, so the real `psql` exits 2 at once (about 5 ms in the
    probes). The test reads the live database through its own connection.
  - **Unreachable (FP).** A fake `psql` exits 2 with `psql`'s own "connection
    to server … failed" text.
  - **Refused: the switch.** The switch row is set to `files` (P170–171).
  - **Refused: a GRANT.** The REGISTER-WRITER holds no INSERT anywhere, and
    writes only through `commit_write` (§1.4), so the fault takes the
    function away: the test's admin connection runs
    `REVOKE EXECUTE ON FUNCTION lanes_register.commit_write(bigint, uuid, integer, uuid, jsonb) FROM lanes_register_writer;`
    in the test's database. The commit call then fails with 42501 on the
    function. A second case revokes the writer's SELECT on `register_head`,
    so the snapshot is refused instead.
  - **Partway.** No helper is installed. The `psql` shim (section 3.2,
    `fail-match` mode's sibling `edit-entry`) rewrites the commit call's entry
    so that its last structured row breaks a CHECK, as §1.3 proposes. For
    each multi-file write, the line edits and the earlier rows of the entry
    have changed inside the transaction when the CHECK fails with 23514.
- **Oracle.**
  - **The exit and stderr.** The writer exits 69. Stderr names the store,
    without credentials, and the error (P213–214). A read verb prints nothing
    on stdout.
  - **The frame's outcome table** fixes each fault's code (`design.md` S1,
    "The outcomes"):
    - no connection at the first snapshot is 69;
    - the switch not `primary` is 69;
    - a refused GRANT (42501, on the function or the table) and the late
      row's 23514 are a `psql` exit 3 with another SQLSTATE, so 69 (§1.3,
      "The signals a writer gets");
    - a register rule's refusal, an object held, is 2;
    - LR008, a commit call that reached its last step past its `not_after`
      (lane 1's v2.3), writes nothing. Inside D7's deadline it is a lost
      turn, a fresh snapshot, then a new attempt under the same write id;
      at the deadline F3's rule applies; and by itself it is never 3 or 69
      (lane 3's ruling of lane 4's reading, comment 6710770304; AT-F3).
  - **The database.** Before and after, each register table, the journal and
    the head row are copied out ordered by key and digested, and the digests
    are equal.
  - **git.** These are equal before and after:
    - the lane checkout's HEAD;
    - `git status --porcelain=v1 -uall`;
    - the index's digest;
    - the files under `lanes/`;
    - origin's refs.
  - **The lock.**
    - No mutex is left. On primary the verb runs on a scratch work tree, whose
      mutex is the scratch tree's (`design.md` S1, step 2), so neither that
      mutex nor the lane checkout's remains, and the scratch tree is gone.
    - A second write made at once exits 0, which shows no lock was left.
    - `pg_locks` shows no lock held by any backend other than the test's own
      sessions.
  - **The fake's log (FP).**
    - The writer connected with `register_url=` and `register_passfile=`.
    - `PGPASSWORD` was never set.
    - No `psql` call follows the failed one, so there was no retry on 2 that
      reads as a write and no fallback.
  - **S1's other tests.**
    - The hand-edit `commit`, the captures, `add-row`'s compensating commit
      and the branch-wide push each exit 2, and leave the files as they were.
      The frame wins here: D10 settles the "69 or 2" the proposal left to
      Q9.
    - A queued commit under `handoffs/` is published by the publish act.
    - A queued commit under `lanes/` is refused as a divergence.
  - **The service down** (round 1's 4A). With no service started, a write
    goes on, through its own transaction, and never falls back to the
    files.
- **Options.**
  - D10 settles Q9: a refused direct write is 2. Under the other reading it
    would be 69, and those cases flip.
  - D6 settles P239's choice: the head row is updated first. "Late" means the
    commit call's structured rows, after the line edits and the first file's
    rows have changed.
- **Tests.** In `code/tests/test_sql_register_writes.py`, where `<verb>`
  ranges over S1's writes except `commit`, whose direct write is 2
  (`test_each_direct_write_refuses_with_2_on_primary`):
  - `test_each_write_exits_69_when_the_database_is_unreachable[<verb>]`;
  - `test_each_write_exits_69_when_the_switch_is_not_primary[<verb>]`;
  - `test_each_write_exits_69_when_the_database_refuses_the_transaction[<verb>]`;
  - `test_a_failure_in_a_late_statement_leaves_journal_lines_and_head_unchanged[<write>]`;
  - `test_a_refused_write_leaves_no_lock[<verb>]`;
  - `test_a_hand_edit_commit_is_refused_on_primary`;
  - `test_a_queued_handoff_commit_is_still_published`;
  - `test_a_queued_lanes_commit_is_refused_as_a_divergence`;
  - `test_each_direct_write_refuses_with_2_on_primary[<write>]`;
  - `test_a_pending_hand_edit_names_the_file_and_a_recovery_that_works`
    (P361–365): the refusal comes before the write's snapshot, in the
    writing lane's checkout, so the shim's log is empty; it names the file
    holding the hand edit and one recovery command; running that command
    leaves the checkout's `status --porcelain` empty, and the next write
    exits 0;
  - `test_a_write_goes_on_with_the_service_down`.

  In `code/tests/test_sql_register_shape.py`:
  - `test_an_unreachable_store_is_69_naming_the_store_and_never_the_files[<verb>]`.
- **Run time.** About 14 verbs × 3 refusals × 0.3–0.5 s, plus 6 partial
  failures. That is about 15–30 s, and the FP cases add about 5 s.
- **Flakiness.**
  - Each test's dead socket directory is new and empty, so no stray server can
    answer.
  - The broken row is in the one entry the shim rewrites, so no other test
    inherits it, and the template holds no test-only object.
  - Snapshots are ordered by primary key, never by physical order.

### AT-3. A files-mode writer refusing under the marker

- **Proves.** A writer that is not `mode=primary`, whose fetched tree carries
  `lanes/STORE`, refuses with 69 and the text "this estate's register is SQL;
  configure `mode=primary`", and writes nothing (P141–143, P168–172, P859). It
  holds for unconfigured and `mode=index` workstations alike. The latter is
  every workstation act 3 configured, under round 1's 3A (Q20(B)).
- **Phase and kind.** W for this entry's unit form, whose marker is
  committed by hand on a bare origin: lane 4's row 8, which lane 3 ruled
  (comment 6710770304), ships it with the writers' marker check, since a
  phase must land tested. Its form with the marker pushed by `cutover` is
  G, where 1A puts tests 3, 5 and 6 (P1401–1402), and it has no case yet
  (open point 20). The marker check sits after S3's fetch (P1399–1400).
  FM, with no database.
- **Fixture.**
  - A bare origin whose `main` carries the cutover's `lanes/STORE`, with the
    fields the design settles (D21): `primary`, the database's name with no
    credentials, the import commit, `lanes-tree=` and the UTC (P166–168,
    P588–590).
  - A files-mode clone one commit behind it, so the writer's own fetch brings
    the marker.
  - A second clone at the marker's commit, run with `LANES_NO_FETCH=1`.
  - Each of S1's verbs runs once per configuration: none, and `mode=index`.
    So does `openRepoTools wip init` (Q14 (a)).
- **Oracle.**
  - The writer exits 69, and stderr carries the line quoted above.
  - These are unchanged: the clone's HEAD, index, working tree and files under
    `lanes/`, and origin's refs.
  - No local commit is queued for the publish act.
  - With `LANES_NO_FETCH=1` and the marker already in its tip, the writer
    still exits 69.
  - **An unconfigured workstation's readers (Q21(a) (A)).** `who` exits 69,
    the guard 2, and `lanes` prints its `read: mirror` line.
- **Options.**
  - If act 2 leaves any verb without S3's fetch, that verb sees the marker
    only once it has fetched. Its case is then written against its last
    fetch, and the gap would be listed in section 6.3.
- **Tests.** In `code/tests/test_sql_register_marker.py`:
  - `test_a_files_mode_writer_refuses_under_the_marker[<verb>]`;
  - `test_a_mode_index_writer_refuses_under_the_marker[<verb>]`;
  - `test_a_writer_with_no_fetch_refuses_when_its_tip_carries_the_marker`;
  - `test_wip_init_refuses_under_the_marker`;
  - `test_a_readers_refusal_under_the_marker_names_the_refs_age`: an
    unconfigured reader refuses with 69, and its text says how old the ref
    it read is;
  - `test_a_reader_refuses_when_its_read_ref_carries_the_marker_and_its_tree_does_not`:
    the marker check runs `git cat-file -e` on the ref the read uses, not
    the working tree;
  - `test_an_unconfigured_workstations_acts_refuse_and_listings_read_the_mirror`.
- **Run time.** About 28 cases at 0.2–0.4 s, so 6–12 s.
- **Flakiness.** Nothing here is timed. The fetch is a real fetch from a local
  bare origin, and each clone has its own `$HOME`, so no user configuration
  leaks in.

### AT-4. Concurrent writers: CLAIMED and exit 2, HOLD order, the burst of ten

- **Proves.** Claim order is commit order (S6, P631–633; S6's tests,
  P675–679; P860–867).
  - Two claimants on one object: one `CLAIMED`, and the other exits 2,
    writing nothing (Q12 (a)).
  - Two `HOLD`s on one repository's `main` land in commit order. The later
    writer exits 2, writes nothing, and prints the earlier line (P645–664,
    P9). The frame wins here: D18 names the code the proposal left to the
    design.
  - Ten writers on distinct objects, released together with a 200 ms window,
    all commit (ADV-C7; section 4.1).

  It also carries three of S1's tests:
  - a 40001 is retried and never reported as 69 (P218–223, P291–292);
  - a write that cannot get its turn in time exits "register busy", 75 (D7),
    not 69 (P224–234, P292–295);
  - no writer exits on a 23505 from the statement order (P235–239).
- **Phase and kind.** W. RE: no fake can produce a 40001 (R1, section 3).
- **Fixture.**
  - The imported register, with the objects free.
  - One workstation configuration, and a distinct lane name for each writer.
  - **A start barrier.** Each writer is launched through a small launcher
    that blocks on a FIFO the test opens only once all N launchers are
    waiting.
  - **The window.** A `psql` shim (section 3.2) sleeps W ms before it hands
    on the call that carries the attempt's COMMIT. That is the second
    `psql` call of each attempt (Q7 (A″)), and the shim knows it by the
    script's own `COMMIT`.
  - **The deadline (A″).** D7 sets it at 30 s. The test sets it through
    `LANES_REGISTER_DEADLINE_S` (`design.md` S1): 30 for the burst, and 1 for
    the busy case, since the variable counts whole seconds.
- **Oracle.**
  - **(a) Two claimants, W = 200 ms.**
    - Exactly one `CLAIMED` line for the object, in the journal and in the
      line store.
    - The exit codes are {0, 2}.
    - The journal grew by one.
    - The loser's write id is absent.
    - No `CLAIM-LOST` line is produced (`design.md` S6).
    - The winner prints `register seq <n>` (Q19 (A)).
  - **(b) Two `HOLD`s.**
    - The journal order equals the order of the two COMMITs.
    - The first writer exits 0.
    - The second exits 2 and writes nothing (D18). Its stderr carries the
      earlier line verbatim, with its register seq.
  - **(c) The burst.** Section 4.1's oracle.
  - **(d) Busy.** Two writers on distinct objects, W = 2 s, deadline 1 s.
    - One exits 0. The other exits 75. The frame wins here: D7 names the
      code the proposal left to the design.
    - The busy writer's write id is absent from the journal.
    - Its stderr says that nothing was written and a retry in seconds is safe
      (P231–233).
  - **(e) Serialization.** In case (a), the loser's exit is 2, never 69 or 75.
    That is the re-run from the pre-check (P221–223).
  - **(f) A hold line that names no repository** (`design.md` S6).
    - `append-line` with a line matching `HOLD — ` or `LANDING — `, from
      which no repository can be read, exits 2 on primary and writes nothing.
    - A line of another shape is written as free text.
- **Tests.** In `code/tests/test_sql_register_order.py`:
  - `test_two_claimants_one_claimed_and_the_other_exits_2`;
  - `test_the_later_hold_exits_2_and_prints_the_earlier_line`;
  - `test_ten_writers_released_together_all_commit_in_order`, defined here
    once; section 4.1 gives its oracle;
  - `test_a_write_that_cannot_get_its_turn_exits_75_register_busy`;
  - `test_a_hold_line_that_names_no_repository_is_refused_with_2`;
  - `test_each_line_kind_takes_or_releases_main_as_q17_b_says[<kind>]`: after
    each kind's line, `main_slots` shows `main` taken or released as Q17
    (B)'s table says, and another lane's `HOLD` then exits 0 or 2 to match;
  - `test_claim_prints_its_register_seq`;
  - `test_a_serialization_failure_is_retried_never_reported_as_69`.
- **Run time.** Cases (a), (b) and (e) take about 0.5 s each, (c) about 2.3 s
  plus ten process starts (the probes), and (d) about 2.5 s. That is 7–9 s in
  all.
- **Flakiness.**
  - The barrier, not a sleep, makes the writers simultaneous.
  - The window, 200 ms or more, is far above scheduling jitter.
  - Every assertion is about counts, codes and order, never time.
  - The burst's 30 s deadline is about 13 times the probes' 2.26 s drain, so a
    loaded host is slower, not wrong.

### AT-5. Two renderers racing; a renderer killed between commit and push

- **Proves.** P868–870, and S2's tests (P399–413):
  - two renderers racing give one commit per sequence, in order;
  - a renderer killed between its commit and its push blocks nothing: the
    lane's next handoff commit and the next render go through unrefused, with
    no manual step (P319–346; concern 3, ADV-C6).

  S2's tests add:
  - a hung or failing renderer delays no writer beyond the fork;
  - a failed render shows its lag in `status`, and the next render catches up
    in order;
  - a burst of writes from one lane that arrives within one pass gives at
    most two renders, and no fork waits (lane 4's [L4-3], qualified: a
    slower stream gives one render per pass, never one per write);
  - the last write of a burst is rendered even if no later write happens;
  - a write that commits as the render lets go is rendered with no later
    write (commit I's delta, "A write that commits as the render lets go");
  - a lost push race never rebases a render (P314–318);
  - two renderers make identical commits for one seq, since the author, the
    committer and the dates are fixed to the journal's (P1363–1367).

  Lane 4's section ("Phase 3", accepted in comment 6708889286) adds the
  renderer's own bounds and its push:
  - it finds `m` by form C, paged: `git log --first-parent -n 200 --skip <s>`
    with the trailer in `--format`, the whole walk bounded by
    `LANES_GIT_TIMEOUT`; with no trailer found, or out of time, it pushes
    nothing and records why through `record_mirror_state`;
  - a pass renders at most 64 seqs, `t = min(h, m + 64, o − 1)`, and makes
    at most six push attempts in all;
  - a process goes on only after a pass that moved origin's last
    `Register-Seq`, or once for each rerun mark it finds after it releases;
    after 16 passes it releases the lock and re-forks itself once, detached,
    if its last pass made progress;
  - the push goes to an explicit `ssh://git@github.com/<owner>/<repo>.git`
    from `origin`'s own URL, in an environment of its own, when
    `render_key=` is set.
- **Phase and kind.** G. RE, with git.
- **Fixture.**
  - The imported register at head `n`, with entries `n+1` to `n+5` written
    through the write path.
  - Two lane register checkouts, `<W>-lanes/L1/` and `<W>-lanes/L2/`, of one
    bare origin.
  - A `git` wrapper on the renderer's `PATH`, which logs each subcommand and
    can be armed through a control file to do one of these things:
    - stop at `push` on a FIFO until the test releases it (the "held"
      renderer). The hold re-arms for each pass: every pass's `push` is held
      in turn until the test disarms it, which the 16-pass case needs;
    - send SIGKILL to the renderer's process group just before `push`;
    - stop on a FIFO, or send SIGKILL to the renderer's process group, inside
      `read-tree` (or `update-index`), which leaves
      `lanes-render.index.lock` behind;
    - fail every `fetch`, or every `push`, with exit 1 until the test
      releases it. A single failed push would not do: the frame's renderer
      fetches and retries a rejected push in the same render, up to six
      times, and makes another pass while the mirror lacks a seq ("The
      renderer, in the private-index form"), so one failure leaves no lag. A
      pass that pushed nothing ends the renderer (F15);
    - reject a `push` as a non-fast-forward, N times in a row (N = 6 for the
      six-lost-races case), with git's rejection on stderr and exit 1, then
      disarm itself. The renderer re-fetches and retries a rejection within
      its pass, so this arm gives the six pushes. The arm above that fails
      every push is lane 4's plain failed push, which ended after one pass
      with `last_failure` recorded (lane 4's section, "F15, the progress
      bound", "Measured");
    - stall `log` past `LANES_GIT_TIMEOUT`, which the case sets to 2 s, so
      that form C's paged walk runs out of time.
  - The race is started through test 4's barrier.
  - For the lets-go case, the `psql` shim (section 3.2) on the renderer's
    `PATH`, in its `timer` mode joined to a FIFO, as AT-D8's killed write
    uses it. L1's first write forks a render, and the shim holds that
    render's second pass: once the real `psql` has returned the pass's
    journal read, which holds no entry after `m`, the shim writes to the
    FIFO and blocks. The hold is one-shot: the shim disarms when the test
    releases it, so the pass after the release reads the journal unheld.
- **Oracle.**
  - **The race.**
    - Origin's `main` gains exactly five commits.
    - In `git log --reverse`, their `Register-Seq:` trailers run `n+1` to
      `n+5` with none repeated.
    - Each message equals its journal entry's.
    - Author, committer and dates equal the journal's.
    - Both renderers exit 0.
    - The wrapper's log shows no `rebase` and no `pull --rebase`.
  - **Identical commits.** The sha L1's renderer computes for seq `n+1` equals
    the one L2's computes. Author and committer are the identity the journal
    recorded, both dated by the entry's commit UTC (D15).
  - **The kill.**
    - Read after the killed renderer's exit (open point 8's second wait): L1's
      HEAD, index digest and `status --porcelain` are as they were before the
      render (the private-index form).
    - **[L4-4]:** the SIGKILL released the lock. A non-blocking `flock` on
      `lanes-render.lock` succeeds at once, no lock directory is left, and no
      hand removal is needed before the next render.
    - The next write in L1 forks a render that pushes every missing seq in
      order.
    - L1's next handoff commit then exits 0 and is pushed. That is
      `lane-handoff`'s commit block, and in a second case `lane-start`'s
      stamp.
    - No other command runs between the kill and those two.
  - **Held.** With L1's render held at `push`, four more writes from L1 each
    exit 0 while it is held, so no fork waited. Each fork that found the lock
    held, a non-blocking `flock` on `lanes-render.lock` failing, left
    `lanes-render.rerun` in the checkout's git directory and exited at once
    (D14). On release, once the lag is 0 and
    the renderer has exited (both of open point 8's waits, since a count of
    pushes needs the last render ended):
    - at most two renders made commits (the wrapper's log counts `push`
      calls);
    - origin holds the burst's last seq without any further write.
  - **Lets go** (the delta's "A write that commits as the render lets go",
    commit I; S2's two halves of `SyncLock`'s handshake). With L1's
    renderer held by the shim after its last look at the journal, which
    found nothing after `m`, a second write from L1 commits and exits 0; its
    fork finds the lock held, leaves `lanes-render.rerun`, and exits. The
    test releases the shim and makes no later write. Then the write's seq
    reaches origin, `status` shows a lag of 0, and no `lanes-render.rerun`
    is left.
  - **Failed.** Two cases: every `fetch` fails (the frame's own lag example,
    "A fetch that fails"), and every `push` fails.
    - After the write, the test waits for the renderer's exit (open point 8's
      second wait), not for a lag of 0.
    - SQL holds the seq, and `lanes-index status` reports a lag of 1.
    - `mirror_state.last_error` and `last_error_utc` are set by the failed
      pass, through `record_mirror_state` (D16; the frame's "A fetch that
      fails"), and `diverged_sha` stays NULL.
    - In the push case, the wrapper's log shows a fetch before each new push,
      and at most six `push` calls in all (lane 4's section, "A pass").
    - No pass follows a failed one: after the renderer's exit, the wrapper's
      log shows no further `fetch` or `push` until the next write's fork
      (F15's ruling: a pass that pushed nothing ends the renderer, and the
      next write's fork starts a fresh one).
    - The test releases the wrapper. After the next write's render, origin
      holds both seqs in order, and `status` reports a lag of 0.
- **Options.**
  - **Under Q5's alternative,** the renderer inside `lanes-register`, the race
    is between two services' renderers, and the oracle is unchanged.
- **Tests.** In `code/tests/test_sql_register_renderer_git.py`:
  - `test_two_renderers_racing_push_one_commit_per_seq_in_order`;
  - `test_renderers_make_identical_commits_for_one_seq`;
  - `test_a_renderer_killed_between_commit_and_push_blocks_nothing`;
  - `test_a_renderer_killed_by_sigkill_leaves_the_checkout_able_to_render_at_once`
    ([L4-4]);
  - `test_a_held_renderer_delays_no_writer_beyond_the_fork`;
  - `test_a_burst_from_one_lane_gives_at_most_two_renders`;
  - `test_the_last_write_of_a_burst_is_rendered_with_no_later_write`;
  - `test_a_failed_render_shows_its_lag_and_the_next_render_catches_up[fetch|push]`;
  - `test_a_lost_push_race_never_rebases_a_render`;
  - `test_the_render_push_alone_carries_the_deploy_key` (D23; lane 4's
    section, "The push URL"): with `render_key=` set, the origin named by a
    GitHub HTTPS URL, and a global `url.*.insteadOf` rewrite of SSH URLs to
    HTTPS and `SSH_AUTH_SOCK` set in the test's own environment (commit I's
    "A host that rewrites URLs"), the `git` wrapper records the push and
    makes it to the bare origin in its place. The push names
    `ssh://git@github.com/<owner>/<repo>.git` from the origin's own URL; its
    environment alone carries `GIT_SSH_COMMAND` with `-i` the key file,
    `IdentitiesOnly=yes` and `BatchMode=yes`, `GIT_CONFIG_GLOBAL=/dev/null`
    and `GIT_CONFIG_NOSYSTEM=1`, and no `SSH_AUTH_SOCK`; it runs with
    `--no-verify`; the checkout's config is unchanged, with no `pushurl`;
    and the read-back fetches `origin`;
  - `test_a_non_github_origin_with_render_key_is_refused_with_2`;
  - `test_without_render_key_the_push_goes_to_origin`;
  - `test_the_renderer_finds_m_by_paging_form_c[depth-0|depth-250]`:
    - at depth 0, the newest render commit is the tip: the renderer finds it
      in one page, and the wrapper's log shows one `log` call with `-n 200`,
      no `--skip` and no `--grep`;
    - at depth 250, the newest render commit is 250 first-parent commits
      below the tip: the renderer, unlike the guard, pages with `--skip` and
      finds it, and the wrapper's log shows `-n 200`, the `--skip` pages, and
      no `--grep`;
  - `test_with_no_register_seq_found_the_renderer_pushes_nothing_and_records_why[none|out-of-time]`:
    `mirror_state.last_error` is set, `status` shows it, and origin is
    unchanged;
  - `test_a_pass_renders_at_most_64_seqs`: a backlog of 70 seqs reaches
    origin as `m+1` to `m+64` in one pass and the rest in the next, with the
    same commits as any other batching;
  - `test_a_renderer_hands_over_once_after_16_passes`: with a stream of 17
    writes, one per pass, each made while that pass is held at its push (the
    hold re-arms for each pass), no renderer process makes more than 16
    passes, a second process follows once, and the last write reaches origin.
    A stream of 17 to 32 passes is the length for which one handover is
    right; a longer one hands over again;
  - `test_six_lost_races_leave_the_last_write_for_the_next_write_or_reconcile`:
    the wrapper rejects six pushes in a row; the renderer ends, `status`
    shows a lag of 1, and `reconcile` renders it (lane 4's known gap);
  - `test_no_rerun_mark_is_left_and_the_last_write_is_rendered_after_a_burst`:
    after every burst of 10 forks, both halves of the `flock` handshake
    leave no `lanes-render.rerun` and the last seq on origin;
  - `test_a_write_that_commits_as_the_render_lets_go_is_rendered_with_no_later_write`;
  - `test_a_renderer_killed_inside_read_tree_does_not_block_the_next_render`:
    the stale `lanes-render.index.lock` is cleared under the lock;
  - `test_form_c_reads_the_trailer_from_each_message_shape[subject-only|trailer-block|prose]`:
    two checkouts give one sha for each, and form C reads `<k>` from each.
- **Run time.** About 25 cases at 1–3 s each, so 30–75 s; the 16-pass
  handover case is the longest.
- **Flakiness.**
  - The hold points are FIFOs in the wrapper or the shim, never sleeps.
  - "No fork waits" is asserted while the render is held indefinitely, so a
    writer's prompt exit is not a race.
  - "At most two renders" is an upper bound, and holds for any interleaving.
  - Every render wait is one of open point 8's two: a bounded poll of
    `status` until its lag is 0, or, where the lag must stay above 0 (the kill
    and the failed render), a wait for the renderer's exit through the
    wrapper's log.

### AT-6. A stray commit stopping the renderer, and each way out

- **Proves.** P871–873, and S2's tests (P399–413; ADV-C2):
  - a commit the renderer did not make, to a path it renders, stops it
    (P375–379);
  - `status` prints `diverged at <sha>`, and `reconcile` imports nothing
    (P395–398);
  - each way out, on a person's word, resumes the renderer, after which
    `reconcile` passes and rollback works. The ways out are the single-write
    import and the renderer's revert (P380–394).

  The negative cases: a cutover commit, a `lanes/repos.tsv` edit and a handoff
  commit between renders do not stop it (P366–374).
- **Phase and kind.** G. RE, with an FM stale writer.
- **Fixture.**
  - A primary estate: the imported register, the cutover done (the switch at
    `primary`, and `lanes/STORE` on a bare origin).
  - A stale workstation: a clone with no configuration, running the v1.1.0
    `lanes-edit.sh`, which knows no marker (P389–393). Its bytes come from a
    digest-pinned fixture (open point 5).
  - The stale writer makes `log NOTED`, which pushes a commit changing
    `lanes/log/<lane>.md`. Then a primary write forks a render.
  - Test 5's `git` wrapper on the renderer's `PATH`, for the render wait.
  - The negative cases push instead:
    - the cutover's own `lanes/STORE` commit (at the start);
    - a `lanes/repos.tsv` edit;
    - a commit under `handoffs/`.
  - The ways out, the single-write import and the revert, run on a person's
    word from the operator lane's checkout, with `LANES_LANE=` that lane
    (F12, §2.1).
- **Oracle.**
  - **The stop.** Read after the renderer's exit (open point 8's second
    wait), since the lag stays above 0:
    - The render makes no commit, and origin's tip is still the stray commit.
    - `lanes-index status` prints `diverged at <stray sha>`, with the full
      sha, and a lag of at least 1.
    - `reconcile` exits non-zero, and the journal's count is unchanged.
    - A later primary write still commits to SQL, since the mirror is
      write-behind.
  - **The single-write import.**
    - `lanes-index import --single <sha>` adds exactly one journal entry,
      through S1's commit call. Its line edits equal the stray commit's diff.
    - It refuses where the edits no longer apply to SQL's images, which is a
      case of its own.
    - The database's mirror-state row no longer records `diverged at` (D16).
    - The renderer resumes, and the pending seqs reach origin in order.
    - `status` then shows a lag of 0 and no divergence.
    - Origin's rendered paths equal a full render from SQL, byte for byte.
    - `reconcile` exits 0.
    - A rollback, with the database up, ends with the files byte-identical,
      the marker gone and SQL writes refused (test 8's first oracle).
  - **The renderer's revert.**
    - `lanes-index render --revert <sha>` journals one entry with no edits,
      whose message names the stray commit (D16).
    - Its render commit puts SQL's state back over the stray commit: its diff
      reverses the stray commit's changes to the rendered paths.
    - Then the same resume, `reconcile` and rollback assertions as above.
  - **The negative cases.** The next render renders onto that tip, and
    `status` shows no divergence.
- **Options.**
  - **Q21(b) (B), Brett's answer,** puts a push ruleset in place before the
    cutover where the org's GitHub plan supports it. This case waits on two
    things: whether the plan supports it, which nobody has yet checked, and
    the ruleset's paths, which are Brett's to say: `lanes/**`, as the
    round-2 file words his answer, or the render set and `lanes/STORE`, as
    D23 and `operations.md` have it (lane 3, comment 6711028142). Where the
    ruleset is in place, GitHub refuses the stale push. The suite cannot
    reach GitHub, so a pre-receive hook in the bare origin models it in one
    added case: `test_a_server_side_gate_refuses_the_stale_writers_push`.
    That case shows the stale writer's exit and an unchanged origin, and is
    labelled a model.
  - **D2 and D16** name the two commands and keep the divergence record in
    the database. Each renderer still re-detects the divergence from git, so
    the row reports and never gates alone.
- **Tests.** In `code/tests/test_sql_register_divergence.py`:
  - `test_the_v1_1_0_writer_pushing_to_a_primary_estate_stops_the_renderer`;
  - `test_status_prints_diverged_at_the_stray_sha_and_reconcile_imports_nothing`;
  - `test_the_single_write_import_resumes_the_renderer_and_reconcile_passes`;
  - `test_the_renderers_revert_resumes_it_and_reconcile_passes`;
  - `test_rollback_works_after_each_way_out[import|revert]`;
  - `test_commits_outside_the_render_set_do_not_stop_the_renderer[<commit>]`.
- **Run time.** Seven cases at 2–4 s each, so 15–30 s. A v1.1.0 verb run takes
  under a second.
- **Flakiness.**
  - The stale push completes before the primary write starts, so there is no
    race to lose.
  - The stop waits for the renderer's exit. Every other wait for a forked
    render is open point 8's bounded `status` poll until the lag is 0.
  - The v1.1.0 writer's bytes are pinned by digest, so a drift in that fixture
    fails loudly instead of testing something else.

### AT-7. The import's byte-identical proof, and its refusal on a difference

- **Proves.**
  - `lanes-index import --at <C>` loads the register and the journal's first
    sequence, renders into a scratch tree, and refuses unless the render is
    byte-identical to C's `lanes/` (P518–521, P587, P874).
  - R3's three-part proof holds: a blob id for each path, the counts, and the
    same S3 answers from the files and from SQL for every lane, "which R1 finds
    only a real cluster proves" (P1281–1283).
  - Seq 0 is the import's baseline and is replaced by each re-import
    (P1283–1286).
  - `WORD` lines import verbatim and flagged (Q4(d)(i)), and only origin is
    imported (Q4(c)(i)).
  - S5's cutover tests (P587–591):
    - a successful cutover, with R3b step 7's oracle (P1289–1291);
    - the abort when `main` moved under `lanes/`.
- **Phase and kind.** N for the import and its proof, and G for the cutover's
  push. RE.
- **Fixture.**
  - **The fixture register.** A files-mode workspace repository at commit C, on
    a bare origin. It is built by running the files-mode verbs, so its bytes
    are the ones `lanes-edit.sh` writes. To that the test adds:
    - `WORD` lines whose UTC carries milliseconds;
    - the six HOLD-shaped kinds R3 counted;
    - an archived lane;
    - aliases;
    - `lanes/repos.tsv` and `lanes/log/README.md`, outside the render set
      (Q18 (A)).
  - **The differences** C' adds, each its own case:
    - a line ending the renderer cannot reproduce (CRLF);
    - a trailing blank in a table row;
    - a line of a kind the parser does not know.
  - **Q4(c)(i)'s case.** The operator's clone holds one local, unpushed line.
  - **The cutover's abort.** Another clone pushes a commit under `lanes/` after
    C. For the tree-id rule, a third clone pushes a commit under `handoffs/`.
  - **The freeze,** Q4(a) (i), is the runbook's and not the suite's.
- **Oracle.**
  - **Success.**
    - The import exits 0.
    - Seq 0 holds C's sha, C's `lanes` tree id, and the digests.
    - Each table's count equals the count parsed from the files.
    - Every rendered path's blob id equals C's.
    - For every lane, each S3 read answers the same from the files as from
      SQL: stdout, the exit code, and stderr less its `read:` lines. The reads
      are `who`, `binding`, `live-holder`, `next-free`, `lane-last`,
      `register-row`, `session-lane`, `canon-lane`, `resolve-home` and
      `managed-projection`.
  - **Refusal.**
    - The import exits non-zero, with the design's code.
    - Stderr names the first differing path.
    - Nothing is loaded: no seq 0, or the previous import's seq 0 is
      unchanged, and the tables are as before.
    - After C' is fixed, a re-run succeeds.
  - **Re-import.** A second import replaces seq 0, and the counts are
    unchanged.
  - **`WORD` lines.** They carry the design's flag, and render byte-identically.
  - **Origin only.** The local line is absent from SQL.
  - **The cutover (G).** The operator lane runs it, with `LANES_LANE=` its
    name, through its checkout (F12, §2.1).
    - The switch reads `primary`, and after the read-back, the marker
      `present`.
    - Origin's `main` gains exactly one commit, a fast-forward of C, carrying
      `Register-Seq: c`, the cutover entry's own seq: 1 at this first
      cutover, and the seq after the import's baseline at any other (§1.3's
      kinds table; lane 3 words the marker's seq for every cutover form,
      comment 6579459518). It adds `lanes/STORE` with D21's fields:
      `store=primary`, `database=<label>`, `import=<C>`,
      `lanes-tree=<tree id of C:lanes>` and `utc=`.
    - A scan of that commit finds no password, no password-file path and no
      URL userinfo.
    - Then `log NOTED`, the smoke test, gives its SQL row, a mirror commit
      with `Register-Seq: c+1` (2 at a first cutover), a read-back equal to
      the row, and a lag of 0.
  - **The abort.**
    - With a `lanes/` commit after C, the cutover exits non-zero and pushes
      nothing.
    - Once a read-back proves the cutover entry's commit, the one carrying
      its `Register-Seq`, absent from origin (lane 1's v2.3), `switch_to_files` deletes
      seq `c`, the head reads the import's seq, and the switch reads `files`
      with the marker `absent`, as the adversary seat asks (P1824–1826;
      AT-F2, AT-SET).
    - With only the `handoffs/` commit after C, the `lanes` tree id is
      unchanged, so the cutover goes ahead (P1279–1281).
- **Options.**
  - D21 settles the marker's fields, which P588–590 left to the design.
- **Tests.** In `code/tests/test_sql_register_import.py`:
  - `test_import_at_a_commit_proves_a_byte_identical_render`;
  - `test_import_refuses_on_a_difference_and_loads_nothing[<difference>]`;
  - `test_import_answers_every_read_as_the_files_do[<read>]`;
  - `test_a_reimport_replaces_seq_0`;
  - `test_word_lines_with_milliseconds_import_verbatim_and_flagged`;
  - `test_import_reads_origin_only_and_never_a_local_line`;
  - `test_a_cutover_pushes_one_fast_forward_marker_commit_with_no_credential`;
  - `test_the_first_write_after_cutover_is_the_cutovers_seq_plus_1_with_lag_0`;
  - `test_the_cutover_aborts_when_main_moved_under_lanes`;
  - `test_a_handoff_commit_on_the_tip_does_not_abort_the_cutover`;
  - `test_main_slots_pairing_equals_files_mode_rule_6_on_imported_history`:
    on the imported fixture register, whose `HOLD`, `LANDING` and `LANDED`
    lines name one repository under two spellings and close takes out of
    order, the open takes `main_slots` lists, with their places, equal those
    files mode's Rule 6 pairing leaves open on the same files; on the real
    register's copy the rehearsal runs the same comparison. Lane 3 accepted
    the pairing by spellings with these tests (lane 1's point 3, comment
    6710770304), and a difference found here is a finding before the
    cutover.
- **Run time.**
  - An import of the fixture register takes well under a second. R3 measured
    the real register at under a second in memory.
  - The cases take 1–2 s each. The read comparison is 3 lanes × 10 reads × 2
    stores at 0.1–0.3 s.
  - That is 15–35 s in all.
- **Flakiness.** Nothing is timed. "Main moved" is a push made, and finished,
  before the cutover starts.

### AT-8. Rollback, and the leak of ADV-C1

- **Proves.** P875–891; S5's tests (P591–598); S5's rule (P534–578, ADV-C1).
  - **(a) Rollback with the database up** leaves the files byte-identical to a
    full render, the marker gone, and SQL writes refused.
  - **(b) Rollback with the database stopped** is refused (Q4(b)(iii)), and
    changes neither the switch nor the marker.
  - **(c) The missed writer.** Once the database is back and the rollback has
    run to its end, a `mode=primary` writer that was not cleared exits 69
    (`commit`: 2, D10), and the journal's count is unchanged. This holds:
    - for each of the five verbs that make no fetch before they edit
      (`set-row-state`, `replace-in-row`, `append-session-id`, `append-line`,
      `commit`; P172–176), and for `claim`, which fetches before it edits in
      files mode;
    - after a fetch, and with none since the rollback's push;
    - with `LANES_NO_FETCH=1` set (P571–578).
  - **(d) The partition.** The database stays up, and only the rolling-back
    clone cannot reach it (section 4.2).
  - **(e) The test fails on the leak as it stands today:**
    - a primary writer that refuses only on its last-fetched tip;
    - a switch reset when the database returns.
- **Phase and kind.** G, since it needs rollback's push. RE, with git.
- **Fixture.**
  - **A stoppable cluster for this test.** The session's cluster cannot be
    stopped, so each test starts a `stoppable_cluster` of its own (section
    3.1), at about 0.9 s. It is stopped by a fast shutdown, SIGINT to its
    postmaster and a wait, and started again on the same socket directory.
  - **A bare origin and two workstations.** Each has its own `$HOME`,
    `lanes-index.conf` and register checkout:
    - ws1 is the operator lane's checkout, and runs the rollback with
      `LANES_LANE=` that lane (F12);
    - ws2 is the missed workstation, whose `mode=primary` is not cleared.
  - **The partition.** ws1's configuration names a fresh, empty socket
    directory, and ws2's the live one. This is deterministic, with no network
    trick.
  - **(e)'s two stub writers.** They are written in the test, not in the
    product:
    - one refuses only when its last-fetched tip lacks `lanes/STORE`;
    - one checks the switch only when it first reconnects.

    Each writes a journal row through the real `psql` as the admin under
    `SET ROLE lanes_register_owner`. The REGISTER-WRITER has no INSERT, and
    `commit_write` refuses at the switch with LR002 (§1.3), so a stub writing
    as the writer would be refused rather than leak, and the two meta-tests
    would fail for the wrong reason. The stubs model the writers of
    Q4(b)(i) and (ii), not the product.
- **Oracle.**
  - **(a)**
    - The rollback exits 0.
    - The switch reads `files` with the marker `absent`, after
      `record_marker` confirmed the removal.
    - Origin's last commit removes `lanes/STORE` and carries the rollback
      entry's seq, `k+1`, the journal's last.
    - Origin's rendered paths equal a full render from SQL, byte for byte.
    - ws1's configuration no longer says `mode=primary`.
    - A `mode=primary` write then exits 69.
  - **(b)**
    - The rollback exits 69, by D10 and the timeouts table's `rollback` row,
      and stops at its step 1 (`design.md` S5).
    - After the restart, the switch still reads `primary`.
    - `lanes/STORE` is still on origin.
    - ws1 is still `mode=primary`.
  - **(c)** For each of 6 verbs × {fetched, not fetched} × {variable unset,
    `LANES_NO_FETCH=1`}:
    - ws2 exits 69 (`commit`: 2, D10);
    - the journal's row count is unchanged;
    - ws2's HEAD, index and files under `lanes/` are unchanged.

    `commit`'s hand-edit half refuses on `mode=primary` in any case (S1), so its
    case asserts that nothing reaches SQL or `lanes/`, and exit 2 (D10).

    A primary write makes no git fetch (D12). So the fetched and unfetched
    cases, and the variable's two values, must give the same outcome, and
    the test asserts that they do.

    Then a files-mode `claim` of the same object on ws1 exits 0, and the
    object has exactly one holder across SQL and the files.
  - **(d)** Section 4.2's oracle.
  - **(e)** The same oracle, run against each stub, reports a failure: an exit
    0, or a journal that grew. This proves the test would have caught the
    leak; a passing oracle against either stub fails the test.
- **Tests.** In `code/tests/test_sql_register_rollback.py`:
  - `test_rollback_leaves_files_byte_identical_marker_gone_and_sql_writes_refused`;
  - `test_rollback_with_the_database_stopped_is_refused_and_changes_neither_switch_nor_marker`;
  - `test_a_missed_primary_writer_is_refused_after_rollback[<verb>-<fetch>-<no_fetch>]`,
    which expects 69 (`commit`: 2, D10);
  - `test_a_files_mode_claim_after_rollback_leaves_one_holder`;
  - case (d)'s two tests are section 4.2's, defined there once;
  - `test_the_rollback_oracle_fails_on_a_last_fetched_tip_writer`;
  - `test_the_rollback_oracle_fails_on_a_switch_reset_when_the_database_returns`.
- **Run time.**
  - A stop or a start of the fixture's `Popen` cluster takes about
    0.03–0.06 s (the fixture probe: start to ready 30–37 ms, stop and remove
    60 ms). R1's 116 ms and 102 ms were `pg_ctl`'s.
  - The 24 missed-writer cases take about 0.3 s each.
  - Each rollback takes 1–2 s.
  - That is 20–40 s in all.
- **Flakiness.**
  - A start waits on the pid-file poll, and a stop on the postmaster's exit,
    never on a sleep (section 3.1).
  - The partition is a difference in configuration, not a network fault.
  - Each test's stoppable cluster has its own socket directory, so stopping
    it cannot touch the session's cluster.

### AT-9. The measured cost of one write, and what a lane feels

- **Proves.**
  - **The cost of one write,** recorded in the PR body: a median and a p95
    over warm runs, with the cold first call beside them, on a named host,
    store and path (P892–894, Q10 at P1667–1679).
  - **What a lane feels (P5).** These verbs are timed end to end, from exec to
    exit, warm and cold, beside the same verbs in files mode on the same host
    (P894–897):
    - `log NOTED`;
    - `claim`;
    - a landing's five writes;
    - `lane-start --dry-run`.
- **Phase and kind.** W for the verbs, and N for the storage layer's cost and
  the guard's parts. RE, as a measurement: it asserts correctness and records
  numbers, and asserts no time (section 5).
- **Fixture.**
  - The session cluster, at the fixture's settings (`fsync`,
    `synchronous_commit` and `full_page_writes` off; R1).
  - For the durable figure, a stoppable cluster as in test 8, started with
    the defaults.
  - A files-mode twin estate on a local bare origin. Its push goes to the
    local origin, not to GitHub, so the files-mode figure is labelled a lower
    bound of today's cost.
- **Oracle.**
  - Every timed write committed: the journal grew by exactly the number of
    writes, and each `write_id` appears once.
  - Every verb exited 0.
  - The figures are written as section 5 sets out, each with its host, store,
    path, statistic and n.
  - One write is the snapshot call, the verb's bash run and the
    compare-and-swap COMMIT call (Q7 (A″)). That is what the verb's
    end-to-end figure carries, and it is recorded.
- **Options.**
  - **Q29 (a), Brett's answer:** this figure and test 10's together decide
    whether act 4 waits for the service, against a time limit Brett has
    yet to name.
- **Tests.** In `code/tests/test_sql_register_measure.py`:
  - `test_one_write_cost_is_recorded_and_every_write_commits[<path>-<store>]`;
  - `test_each_verb_is_timed_end_to_end_in_both_modes[<verb>]`.
- **Run time.**
  - About 12 s for 200 writes in each of the four path-and-store pairs.
  - About 60–120 s for 21 runs (one cold, 20 warm) of the four verb sets in
    both modes.
  - Whether all of it runs on every CI run is open point 9.
- **Flakiness.** No time is asserted, so a loaded host changes the figure and
  never the verdict. Each record names its host, so a bench figure is never
  read as CI's.

### AT-10. The guard through the service

- **Proves.** The proposal's test 10, and the service's tests in its "The
  register service, and the guard as its client":
  - the guard's answer through the service is byte-identical to the direct
    read for `ok`, `refuse` and `offer`, compared as stdout, stderr less the
    fallback line, and the exit code;
  - with the service down, the hook falls back and prints
    `guard: direct (service unavailable)` on stderr, with the same exit;
  - a service that accepts and never answers gives the fallback line, the
    same exit, and the whole hook inside its 5 s;
  - with the database unreachable and the service up, the answer is 2, never
    a cached `ok` (P713–719);
  - ADV-C4's cases inside the budget, with the service up and down
    (section 4.4);
  - a write by another process between two requests is in the next answer;
  - no environment a session inherits carries a database URL or password
    (Amendment 14(c));
  - the answer time is recorded against 50 ms, with today's direct guard
    beside it (P5);
  - the `CLAUDE_NO_LANE=1` exception still exits 0 before any input
    (P758–760).
- **Phase and kind.** N: the service and its read API. RE for the service
  against the cluster. FP for the credential and environment checks. A
  loopback listener for ADV-C4.
- **Fixture.**
  - **The imported register,** arranged so the three answers each arise:
    - `ok`: this session's lane is bound to this window;
    - `refuse`: another session holds the lane, and is live;
    - `offer`: an unbound window.
  - **The hook's input:** JSON with `session_id`, `cwd`, `agent_id` and
    `prompt`.
  - **Window and liveness facts:** a fake `tmux` on `PATH` answers `#W`. The
    live records sit under a temporary `$HOME`.
  - **The service** is started by the test on a socket in a short directory
    (where the socket lives is Q2(c)'s).
  - **The stand-ins for a service that will not answer:**
    - "service down": no socket file, then a socket file with no listener;
    - "never answers": a Python stand-in that accepts and then blocks on a
      read that never completes, with no timer of its own.
  - **"Database unreachable, service up":** the service's database is a
    stoppable cluster (test 8's), stopped between two requests.
  - **The hook** runs as the harness runs it: the command string
    `--install` writes, under a 5 s kill timer, timed with a monotonic clock.
  - **A fake `claude` on `PATH`** records the environment `lane-start` hands
    a session.
- **Oracle.**
  - **Byte-identical.** On one state, the service path and the direct path
    (with no service) give equal stdout bytes, equal stderr bytes once the
    one fallback line is removed, and equal exit codes.
  - **The fallback.** Stderr carries exactly that line, once, and the exit
    equals the direct read's.
  - **Never cached.** The first request answers 0. The database is stopped,
    and the second request exits 2.
  - **Inside the budget.** The hook exits 2, never killed, in under 4.5 s,
    the frame's assertion (`design.md`, "One budget").
  - **A fresh write.** Request, a write by another process, request: the
    second answer equals the direct read made after the write.
  - **The environment.** No variable carries the register password, the
    password file's path, `register_url=`'s value or any `postgresql://`
    string. That covers the hook's own environment, the service's children,
    and the fake `claude`'s recorded environment. The fake `psql`'s log shows
    `PGPASSWORD` never set.
  - **The answer time.** It is recorded as section 5 sets out, never asserted.
  - **The codes the guard maps (D10).** Answer 2's bind runs detached
    (AT-F6). When it is made to exit 69, 75 or 3 (test 12's shim, or a
    `LANES_EDIT` wrapper), the child writes `mode=bind-failed` and `rc=` that
    code to the offer file, and the next prompt's hook exits 2 with "THE BIND
    TO LANE `<to>` FAILED (exit `<rc>`)", the why and, for 3, the write id
    (lane 4's section, "What the guard answers").
  - **The fallback's credential (Q11 (a)).** The fallback holds the READER
    credential itself, from the 0600 configuration, and the environment
    check above covers it.
  - **A declared outage (Q26 (a′)).** With the database down and an outage
    declaration the guard can read, the guard answers from the mirror with
    its `read: mirror` line. Without a declaration it answers 2. D20 puts
    the declaration in a file beside `lanes-index.conf`, and its cases are
    AT-D20's.
- **Options.**
  - **Under Q2(g),** the client is a block at the top of
    `lanes-edit.sh guard`, or a separate program with a new hook string. The
    byte-identical cases hold under either. The 50 ms target is restated
    against the form chosen, as concern 4 asks (C148–170).
  - **Q29 (a), Brett's answer:** act 4 may come before the service, unless
    the guard measured without it is slower than a time limit Brett has
    yet to name. The direct-read cases are then the guard act 4 relies on
    (P4).
- **Tests.** In `code/tests/test_sql_register_guard.py`:
  - `test_the_guard_through_the_service_answers_byte_identical_to_the_direct_read[ok|refuse|offer]`;
  - `test_with_the_service_down_the_hook_falls_back_and_says_so`;
  - `test_a_service_that_never_answers_falls_back_inside_5_s`;
  - `test_the_database_unreachable_with_the_service_up_is_2_never_a_cached_ok`;
  - `test_a_database_that_does_not_answer_is_2_inside_4_5_s[<case>-<service>]`;
  - the per-path cases of section 4.4 are owned by AT-D9's
    `test_each_database_path_exits_inside_its_bound`;
  - `test_a_write_between_two_requests_is_in_the_next_answer`;
  - `test_the_service_reaches_the_database_only_through_psql` (FP): every
    request the service answers from the database appears as a call in the
    fake's log, and with no `psql` on its `PATH` it answers `refuse`;
  - `test_the_session_start_block_reads_the_mirror_and_only_starts_the_service`:
    the block answers from the mirror offline and exits 0, with the service
    down and up; on `mode=primary` with no service running it starts one
    after its answer without waiting for it, and the next guard request
    finds it running (lane 5's question 17, as lane 3 ruled);
  - `test_the_first_call_starts_the_service_with_no_supervisor` (SVC-4): with
    no service running, the client starts `lanes-index serve` detached, its
    request is answered, and no supervisor process exists;
  - `test_a_service_that_cannot_start_falls_back_to_the_direct_read`: with
    `lanes-index serve` made to fail, the request is answered by the direct
    read, and no second start is tried within SVC-4's 60 s cooldown;
  - `test_no_session_environment_carries_a_database_url_or_password`;
  - `test_claude_no_lane_still_skips_before_any_input`;
  - `test_the_guard_maps_69_75_and_3_to_2[<code>]`;
  - `test_the_guards_request_carries_only_file_names`: a recording stand-in
    on the service's socket reads each request, and finds file names only:
    no session id, `cwd`, agent id, prompt, window, live record or
    liveness fact;
  - `test_the_guard_answer_time_is_recorded_beside_todays_direct_guard`.
- **Run time.**
  - The byte-identical and fallback cases take about 10–20 s. Today's guard
    took about 2 s wall in its register read on 2026-09-14 (C158–159); the
    measurements put today's read and parse at 0.31 s median (M10).
  - Section 4.4's cases are bounded by their timers, 30–60 s at worst.
  - That is 1–1.5 min in all.
- **Flakiness.**
  - The assertion is the frame's 4.5 s. Its tightest case is a host silent
    after connecting: the probes' 4 s timer exited at 4006 ms, and SVC-8 now
    cuts the direct read at the parse's start line, `T0 + 3.75 s`, so the
    margin is about 0.75 s less the guard's own start. If CI's runner misses
    4.5 s, the shares are cut, in SVC-8's budget and the register-service
    spec's "The 5 s is one budget, spent once"; the bound is never relaxed,
    because a guard past 5 s is killed and the prompt passes (lane 3,
    comment 6578688322).
  - Each listener binds port 0 on 127.0.0.1 per test.
  - No ordering relies on a sleep. The stand-in's "never answers" is a
    blocking read, not a timed one.

### AT-11. A lane's day on mode=primary (P6)

- **Proves.** P906–911, the product seat's P6, and Q8(a)'s note (P1597–1598).
  A lane's whole day runs on `mode=primary`. After each act:
  - the mirror is byte-identical to a full render;
  - origin has one commit per seq;
  - no handoff commit rewrote a render commit.

  With the cluster stopped mid-day, the outcomes are Q8's, under round 1's 2A:
  - `lane-start`'s deciding reads refuse with 69 before anything is written,
    and so do `request-handoff` and a new lane's `add-row`;
  - the later stamps print a note, and the launch goes on;
  - `lane-handoff` writes the handoff file from a mirror read that says so;
  - the `PAUSED` record refuses with 69, with P15's note, and the lane stays
    `SWAPPING` until the late path completes it (P1565–1631).
- **Phase and kind.** G, after Amendment 23's slices 2a, 2b and 3
  (Q28 (i)). RE, with git.
- **Fixture.**
  - A stoppable cluster (test 8's), holding the imported register after a
    cutover.
  - A bare origin.
  - One workstation: a `$HOME`, a configuration with `mode=primary`, the
    read-only `<W>`, and `<W>-lanes/<lane>/`.
  - The fake `claude` and fake `tmux` the code leg's `lane-start` tests
    already use.
  - A lane name not yet in the register.
  - For "the later stamps" and a new lane's row, a `psql` shim (section 3.2)
    that fails, with exit 2, the commit call of `start-record`. For a
    resume, that is the one verb that makes `lane-start`'s three register
    writes (`append-session-id`, `set-row-state LIVE` and `log RESUMED`) in
    one transaction; for a new lane, it is the same verb with `--add-row`
    (lane 4's section, "The bind as one verb"). The deciding reads pass.
- **The day.** Each act waits for its forked render (open point 8's poll of
  `status` until the lag is 0) before the snapshot:
  1. `lane-start` for the new lane;
  2. `lane-handoff`;
  3. `lane-start` resuming the lane;
  4. `claim`;
  5. `HOLD`;
  6. `LANDING`;
  7. `LANDED`;
  8. `lane-handoff`;
  9. `lane-end`.
- **Oracle.**
  - **After each act:**
    - the act exits 0;
    - origin's rendered paths equal a full render;
    - each `Register-Seq` since the cutover appears exactly once, in order;
    - every (seq, sha) pair recorded after earlier acts is still on
      `origin/main` with the same sha, so no render commit was rewritten;
    - each handoff commit changes nothing under the rendered paths.
  - **With the cluster stopped, for a new lane:**
    - `lane-start` exits 69 before anything is written: no row, no `tmux`
      rename in the fake's log, the fake `claude` not run, and no stamp
      commit;
    - the same holds for a canonical-name read failing alone, at
      `code/lane-start:1154` (ADV-C5).
  - **`request-handoff` and `claim`** each exit 69.
  - **The stamp shim's resume,** as lane 4's corrected table of
    `start-record`'s exits gives it (comment 6709560550), with
    `LANES_REGISTER_DEADLINE_S=1`:
    - the shim fails every commit call of `start-record` with exit 2, so
      each is in doubt; at the deadline the lookup finds the write absent
      with the head free, and `start-record` exits 75;
    - the shim fails the deadline's lookup too: `start-record` exits 3;
    - the shim fails the snapshot instead: `start-record` exits 69.

    In each:
    - `lane-start` prints a note with the writer's printed rerun, the
      generation included (on a 3, the write id too);
    - the Rule 3 stamp commit is made;
    - the launch goes on, so the fake `claude` runs;
    - the register holds none of the three lines: one transaction, all or
      none.
  - **The stamp shim's new lane,** as the same table gives it. The same shim
    fails `start-record --add-row` in the same three ways, and `lane-start`
    dies with the code, 75, 3 or 69; on a 3, its stderr carries the
    writer's printed rerun line, AT-C5's rule for every act. The row is a
    write that must exist (2A). In each, there is no row and no stamp
    commit, and the fake `claude` does not run.
  - **The expectation's 2,** a second binding, dies on both paths, and has
    no case here. AT-F5 asserts `lane-start`'s `die 2`, and AT-F6's record
    case that the guard's child records `bind-failed` with `rc=<code>`, for
    2, 69, 75 and 3.
  - **`lane-handoff` with the cluster stopped:**
    - the handoff file is published;
    - stderr carries `read: mirror at <sha12>, <k> sequences behind or unknown
      (sql unreachable: <why>)`;
    - the `PAUSED` writes exit 69, and the note names how the record is
      completed later (P15);
    - after a restart, the row still reads `SWAPPING`.
  - **After the restart:**
    - the late `PAUSED` path completes the record: one journal entry, and its
      render on origin;
    - `lane-end` with the cluster stopped exits 69.
- **Tests.** In `code/tests/test_sql_register_lane_day.py`:
  - `test_a_lanes_day_keeps_the_mirror_byte_identical_after_each_act`;
  - `test_no_handoff_commit_rewrites_a_render_commit`;
  - `test_lane_start_for_a_new_lane_refuses_69_before_anything_is_written`;
  - `test_lane_start_resume_launches_with_a_note_and_the_stamp_and_writes_none_of_its_lines[75|3|69]`;
  - `test_lane_start_for_a_new_lane_dies_with_start_records_code_and_writes_no_row_and_no_stamp[75|3|69]`;
  - `test_request_handoff_and_claim_refuse_69_with_the_database_down`;
  - `test_lane_handoff_writes_the_handoff_from_a_labelled_mirror_read_and_leaves_the_lane_swapping`;
  - `test_the_late_paused_path_completes_the_swap_once_the_database_returns`;
  - `test_lane_end_refuses_69_with_the_database_down`.
- **Run time.** The day's nine acts at 1–3 s each with their renders, and
  six outage cases at about 2 s, with one more for the new lane's `add-row`
  at 69. The four stamp-shim cases that reach the deadline's lookup (the
  resume's and the new lane's, at 75 and 3, with
  `LANES_REGISTER_DEADLINE_S=1`) are the exception: the lookup waits until
  the database clock passes the largest `not_after` (AT-F3), the snapshot's
  clock plus the 8 s call timer plus 2 s, so each exits about 10 s after its
  snapshot. The two 69 cases, which fail at the snapshot, have no such wait.
  The three resume cases now also write the stamp and run the fake `claude`,
  about 1–3 s each. That is 65–90 s.
- **Flakiness.**
  - Every snapshot waits for the render, through open point 8's bounded
    poll of `status` until the lag is 0. No act snapshots with a lag above 0.
  - The cluster stops between acts, never during one.
  - The stamp shim keys on the statement, not on timing.

### AT-12. A COMMIT in doubt (ADV-C3)

- **Proves.** P912–918, the recovery by write id (P240–267), S1's test
  (P295), and Q16 (b). Through a `psql` wrapper that commits and then exits 2,
  and one that exits 2 without committing:
  - each ends with exactly one journal entry for the write;
  - the writer exits 0, after a clean retry where the first COMMIT never
    landed;
  - a caller's rerun after a failed reconnect reuses the write id, and still
    ends with one entry.

  The probes' three rules (P257–267) are cases of their own:
  - the retry goes through the UNIQUE `write_id`, with a `lock_timeout`
    (its lookup by the id; AT-F33 asserts that the 23505 on it never fires,
    the frame review's F33);
  - an exit 124 from an outer timer is in doubt;
  - a rerun never mints a new id.
- **Phase and kind.** W. RE, with a shim in front of the real `psql`
  (section 4.3).
- **Fixture.**
  - The imported register.
  - The shim, armed for the writer's first COMMIT call only, through a
    control file. Its modes:
    - `after`: `SELECT pg_terminate_backend(pg_backend_pid());` after the
      script's `COMMIT`;
    - `before`: the same line before it;
    - `timer`: run the real `psql`, then sleep past the writer's outer timer;
    - `no-reconnect`: every later call exits 2 until the test removes a
      second control file.
  - **The half-open case.** The shim's `before` mode makes the first commit
    call in doubt before it lands. The old connection, still open, is a
    session the test opens as `lanes_register_writer`, which replays that
    call's script, as the shim saw it, without its `COMMIT`:
    `BEGIN ISOLATION LEVEL SERIALIZABLE; SELECT lanes_register.commit_write(n, id, version, generation, entry);`.
    That session holds the head row, has advanced it and inserted the
    journal row, and has not committed, which is the state a lost commit
    leaves. The REGISTER-WRITER has no INSERT and cannot lock the head row by
    itself (§1.4), so no other construction gives this state. The `after`
    mode is the landed-then-lost case, which this construction cannot use:
    its row has committed, so the replay meets LR006 at its first statement.
- **Oracle.** The test never matches on stderr: the shim's `FATAL` line is one
  a real loss does not print (the probes).
  - **`after` and `before`:**
    - the journal grew by exactly one;
    - the new row's `write_id` appears once;
    - for `claim`, the object has exactly one `CLAIMED` line;
    - the writer exits 0.
  - **`timer`:** the same, which shows that a timer's expiry is handled as in
    doubt, the row having landed (4 of 4 in the probes). The commit call's
    client timer is 8 s (D9), so this case takes about 8 s.
  - **Half-open,** as the frame has it (`design.md` S1, "A write in doubt"):
    - the old transaction holds the head row, so each retry waits up to its
      `lock_timeout`, gets 55P03, and tries again;
    - while the test's session stays open, no second row exists;
    - when the test rolls its session back, the retry inserts once, and the
      writer exits 0;
    - when it commits instead, the waiting retry gets 40001 at the head
      row's `FOR UPDATE` (AT-LR006-3), the next attempt's snapshot finds the
      id, and the writer exits 0 with no further commit call (§1.3, "The
      signals a writer gets");
    - a second case leaves the held session idle. The writer role's 5 s idle
      bound ends it, and the writer then commits once (D9);
    - in every case, one row.
  - **`no-reconnect`:**
    - the writer keeps trying for 10 s, then exits 3. The frame wins here:
      D8 names the code the proposal left open. Its report names the write id,
      the lookup `lanes-index journal --write-id <id>`, and the rerun
      `LANES_WRITE_ID=<id> LANES_WRITE_GENERATION=<generation> LANES_LANE=<lane> LANES_SESSION=<uuid> lanes-edit.sh <the same arguments>`,
      which carries the register's generation as the pending record does
      (lane 4's section, "The pending record");
    - the id is read from the report, and no outcome is judged from stderr;
    - after the control file is removed, a rerun exits 0 both ways D8 gives:
      with `LANES_WRITE_ID=<id>`, and as a plain rerun of the same command
      line, which finds the pending record (AT-D8);
    - the journal holds one row for the id;
    - the writer never exits 69, and never leaves two `CLAIMED` lines.
- **Options.**
  - **Under Q16 (a) alone,** a lookup by seq, the test fails by design. The
    probes found the lookup picks a rival's row 3 times out of 3 (P1769–1772).
- **Tests.** In `code/tests/test_sql_register_in_doubt.py`:
  - `test_a_commit_that_landed_before_the_connection_was_lost_ends_with_one_entry_and_exit_0`;
  - `test_a_commit_lost_before_it_landed_is_retried_cleanly_and_ends_with_one_entry`;
  - `test_an_outer_timer_expiry_is_in_doubt_like_exit_2`;
  - `test_a_retry_with_the_old_connection_still_open_writes_no_second_row`;
  - `test_a_callers_rerun_after_a_failed_reconnect_reuses_the_write_id`;
  - `test_an_in_doubt_claim_never_leaves_two_claimed_lines_or_a_false_69`.
- **Run time.**
  - The terminate pair took 11–19 ms in the probes.
  - The half-open case waits about 2 s on the writer's `lock_timeout` of
    2000 ms (D9).
  - The timer case waits 8 s.
  - `no-reconnect` waits 10 s.
  - That is about 25–30 s in all.
- **Flakiness.**
  - `pg_terminate_backend` is deterministic (3 of 3 each way in the probes).
  - No proxy is used.
  - In the timer case the COMMIT completes before the shim's sleep starts, so
    the order is fixed.

### AT-13. The drill (P7, ADV-C10)

- **Proves.** P919–921, P7 and ADV-C10's outage, as the runbook's drill (the
  proposal's "The manual"). The sequence runs import, cutover, writes, an
  outage with its refusals, and rollback. Then a files-mode write succeeds,
  while a workstation not yet cleared refuses.
- **Phase and kind.** G. RE, with git, on two workstations.
- **Fixture.**
  - A stoppable cluster, bootstrapped by P8's bootstrap through the admin
    service, as AT-P8's fixture makes it.
  - A bare origin with the fixture register.
  - Two workstations, ws1 (the operator's) and ws2, each with its own `$HOME`,
    configuration and register checkout. Both start at `mode=index`, under
    round 1's 3A.
  - **The freeze** (Q4(a)(i)): no other writer runs during steps 2–3,
    which the drill models by running none.
  - **The ruleset's model** (step 8; AT-F4) rests on Q21(b)'s two open
    points: the org's plan, and the paths Brett is to name (AT-6).
- **The sequence, and its oracle at each step.**
  1. The readiness check passes in ws1 and ws2 (P612–619).
  2. `import --at C` proves its render (test 7).
  3. The cutover sets the switch and pushes the marker. The flip command then
     sets `mode=primary` in each workstation: only that line changes, and the
     file stays 0600.
  4. R3b's smoke test: `log NOTED` gives its row, the `Register-Seq` one above
     the cutover's (2 at this first cutover) and a lag
     of 0 (P1289–1291).
  5. `claim` and `log` from both workstations. After each, the mirror is
     byte-identical and origin has one commit per seq.
  6. The cluster is stopped, and in both workstations:
     - writes exit 69;
     - the guard exits 2, naming the store, the cause and both ways out (P3);
     - `lanes` prints its `read: mirror` line;
     - `lanes-index status` answers inside its bound from a `CLAUDE_NO_LANE=1`
       context.
     - then an outage declaration is added, after which the guard answers
       from the mirror (Q26 (a′); AT-D20).
  7. A rollback attempted while the cluster is down is refused, and changes
     nothing (Q4(b)(iii)). The cluster is then restarted.
  8. Rollback from ws1, run by the operator lane with `LANES_LANE=` its name
     (F12), declares the outage at its start (AT-D20), stops at
     `--hold-marker` while the ruleset's model is switched off (AT-F4), then
     runs to its end, and ws1 alone is cleared.
  9. A files-mode `claim` on ws1 exits 0 and is pushed. A write on ws2, not
     yet cleared, exits 69. Once the flip command clears ws2, its files-mode
     write exits 0.
  10. At the end:
      - origin carries no `lanes/STORE`;
      - its files equal the rollback's last render plus the files-mode
        commits after it;
      - every object has at most one holder.
- **Tests.** In `code/tests/test_sql_register_drill.py`:
  - `test_the_drill_import_cutover_outage_rollback_and_files_mode_write`, one
    test with a labelled assertion at each step;
  - `test_a_workstation_not_yet_cleared_refuses_after_rollback`.
- **Run time.** 20–40 s.
- **Flakiness.**
  - The steps run in sequence.
  - Renders are waited for through open point 8's poll of `status` until
    the lag is 0.
  - The stop and the start wait on the postmaster's exit and the pid-file
    poll (section 3.1).

### AT-14. The build before the renderer's git half

- **Proves.** The proposal's test 14, and P1404–1418. One test covers both
  states, since a single constant is the flip.
  - **With the refusal in place:**
    - `lanes-index mode` exits 78, Q6(c)'s own code, never 2 (D3, F8), and
      every other `lanes-index` command exits 2, each with the text
      `mode=primary is not available in this build: the renderer's git half
      is not installed, so a write could not be mirrored; nothing was
      written`;
    - `lanes-edit.sh`, on a workstation configured `mode=primary`, exits 69,
      repeats that text, and changes neither the register nor the files.
  - **With the constant flipped,** the same write goes to SQL.

  The readiness check's item fails the first state (P612–617, P626).
- **Phase and kind.** N and W. It stays in the suite after G, which builds the
  refusing state from the flipped one. The refusing state is FP, so it always
  runs. The flipped state is RE: its write is a `register snapshot` reply
  (the switch, head, clock, write-id lookup and file images with digests,
  `design.md` S1) and a commit, which a fake cannot fabricate faithfully.
- **Fixture.**
  - Two test copies of `lanes-index` under the test's temporary directory:
    one with the constant refusing, one with it flipped (open point 3).
  - The checkout's `lanes-edit.sh`, pointed at each copy by the means it
    already uses to find the `lanes-index` executable
    (`code/lanes-edit.sh:2554–2568`).
  - A configuration with `mode=primary` and a 0600 password file. For the
    refusing state, `register_url=` is on `db.invalid`; for the flipped
    state, it names the session cluster's socket, on an imported register
    after the cutover.
  - For the refusing state, a fake `psql` that logs every call. For the
    flipped state, test 4's shim in front of the real `psql`, logging every
    call.
  - A files-mode estate on a bare origin, to show the files untouched.
- **Oracle.**
  - **Refusing.**
    - `lanes-index mode` exits 78, `EX_CONFIG`, Q6(c)'s own code (lane 4,
      comment 6579524611, accepted by lane 3 in 6579562840; D3; `design.md`
      S1, "Entry and mode"; §2.7), and its stderr carries the text exactly,
      or the design's rewording (P1413: reworded but not weakened).
      `lanes-index status`, run on the same configuration, exits 2 with the
      same text.
    - `lanes-edit.sh log NOTED`, and each of S1's verbs, exits 69, and its
      stderr repeats the text.
    - The fake `psql`'s log is empty, so no connection was made.
    - The files, the HEAD and origin are unchanged.
  - **Flipped.**
    - The same `log NOTED` exits 0, and the journal gains one entry.
    - The shim's log holds one attempt, two calls (Q7 (A″)): the snapshot
      and the commit, sent with `register_url=` and `register_passfile=` and
      no `PGPASSWORD`.
    - The files are unchanged, since there is no git half in this build.
- **Options.**
  - **The text** is the design's to reword (P1413). The test pins whatever the
    design prints.
- **Tests.** In `code/tests/test_sql_register_build_flip.py`:
  - `test_mode_primary_is_refused_until_the_git_half_and_goes_to_sql_once_flipped[refusing|flipped]`;
  - `test_lanes_edit_asks_lanes_index_mode_only_where_a_configuration_exists`
    (D3): with no configuration file and `LANES_INDEX_CONFIG` unset, a logging
    stand-in at `lanes-index`'s path records no call, and the verb writes in
    files mode;
  - `test_mode_primary_with_sqlite_is_refused` (P162–163).
  - The readiness item is in P8's test (below).
- **Run time.** About 1–3 s for 14 verbs in the refusing state and one flipped
  write.
- **Flakiness.** None. The refusing state is FP, with no clock and no engine;
  the flipped state is one RE write with nothing timed.

### The council's additions not among 1 to 14

The proposal's preamble names the roles' test among the real-engine ones
(P842–845). The council's P3, P8, ADV-C5, ADV-C8 and ADV-C10 each add a test
that tests 1 to 14 do not carry (CR194–225; C172–184, C201–216). P6, P7, P9,
P11 and ADV-C1 to C4, C6 and C7 are inside tests 2, 4, 5, 6, 8, 10, 11, 12 and
13 above. Each addition is labelled by its source.

#### AT-R. The roles: each refused outside its scope

- **Proves.** Clause (b)'s three roles, P681–693:
  - the REGISTER-WRITER writes the register's tables, the journal and the
    switch, only through the schema's functions, and not the ledger half's;
  - the indexer keeps the ledger half's derived tables, and cannot write the
    register's (P154–157);
  - the READER serves the hooks and the listings;
  - each role is refused outside its scope.

  The bootstrap's admin connection is used once and held by nothing, and no
  role can grant, create or act as superuser (P607–611).
- **Phase and kind.** N. RE: "a fake `psql` cannot enforce a GRANT" (P692–693).
- **Fixture.**
  - The session cluster, whose template database was bootstrapped once.
    Roles are global to a cluster, so they are made once per session, and the
    GRANTs travel with each database cloned from the template (section 3.1).
  - Each role connects with `psql -U <role>` over the socket.
  - Authorization is what is tested. Authentication is open point 6.
- **Oracle.**
  - **The matrix.** For every role × object × privilege, an allowed statement
    succeeds and a refused one fails with SQLSTATE 42501, read with
    `-v VERBOSITY=sqlstate`.
    - The objects: each table and view of `lanes_register`, the journal, the
      switch row, each table of `lanes_index`, the six functions
      (`commit_write`, `switch_to_files`, `record_marker`, `find_write`,
      `record_mirror_state` and `render_file`), and each schema (CREATE).
      `find_write` takes the head row `FOR UPDATE` as SECURITY DEFINER, so a
      READER that could execute it could lock the head.
    - The privileges: SELECT, INSERT, UPDATE, DELETE, TRUNCATE and EXECUTE.
  - **The expected matrix** is read from `design.md`'s "The schema", §1.4
    ("Roles, GRANTs and credentials"): its GRANT block and its table of
    roles, never from the bootstrap's own output, so the test does not check
    the bootstrap against itself. There:
    - REGISTER-WRITER has SELECT on `lanes_register`'s tables, EXECUTE on
      `commit_write`, `switch_to_files`, `record_marker`, `find_write`,
      `record_mirror_state` and `render_file`, no INSERT, UPDATE, DELETE or
      DDL anywhere, and nothing in `lanes_index`;
    - READER has SELECT on both schemas' tables and EXECUTE on `render_file`
      only;
    - the indexer owns `lanes_index` and has nothing in `lanes_register`.
  - **How a refused write is tried.** Every write the matrix expects refused
    runs inside `BEGIN READ WRITE; … ROLLBACK;`, and only 42501 passes. The
    READER's role default is read-only, so a plain write would fail with
    25006 before any privilege is checked, and an over-granted READER would
    pass (§2.4, `ready`'s check 4). One extra case asserts that default: the
    READER's plain `INSERT` fails with 25006.
  - **CONNECT.** Each login role connects to the test's database (§1.4's
    database-level statements, which `pg` re-applies after each clone:
    section 3.1). The refusal is shown in a `stoppable_cluster` of the
    test's own, bootstrapped there, with a second database from which the
    test revokes the READER's CONNECT: the READER's connection to it is
    refused with 42501, and its connection to the first still succeeds. No
    role is changed on the session cluster.
  - **The role flags.** `pg_roles` shows none of the three with `rolsuper`,
    `rolcreaterole` or `rolcreatedb`. The bootstrap creates one more role,
    the owner, `lanes_register_owner`, with `rolcanlogin` false, and no
    other.
  - **The floors** (§1.4), exactly. READER's `rolconfig` holds
    `default_transaction_read_only=on`, `lock_timeout=1000ms` and
    `statement_timeout=1500ms`. REGISTER-WRITER's holds
    `lock_timeout=2000ms`, `statement_timeout=5s` and
    `idle_in_transaction_session_timeout=5s` (D9). The owner and the indexer
    carry none.
  - **The admin connection.** After the bootstrap exits, `pg_stat_activity`
    shows no session for the admin role. The admin holds the owner and the
    indexer `WITH INHERIT TRUE, SET TRUE` (§1.4), and migrations run as the
    admin under `SET ROLE` (F23), so no new role exists for them.
  - **The holders.** Writers present their own REGISTER-WRITER credential,
    and the service holds a READER connection (round 1's 4A), and so does
    the guard's fallback (Q11 (a)). So the matrix is as above.
- **Options.**
  - Q3, the schema against #160's, names the tables. The matrix's rows
    follow it.
- **Tests.** In `code/tests/test_sql_register_roles.py`:
  - `test_each_role_is_refused_outside_its_scope[<role>]`, one `psql` session
    per role checking its whole row;
  - `test_no_register_role_can_grant_create_or_act_as_superuser[<role>]`;
  - `test_the_bootstrap_holds_no_admin_session_once_it_exits`;
  - `test_a_readers_plain_write_fails_25006_and_a_read_write_one_42501`;
  - `test_each_login_role_connects_only_where_granted_connect`.
- **Run time.** About 2 s for three sessions of about 60 statements each,
  plus the CONNECT case, which starts a `stoppable_cluster` of its own, about
  0.9 s to start (section 3.1), and bootstraps it there, so a few seconds
  more.
- **Flakiness.** None.

#### AT-C5. The readers' table, the one-call failure, and the callers' default arms (ADV-C5)

- **Proves.** S3's tests (P484–493):
  - each row of the readers' table, for each reader in it (P455–465, and
    `design.md` S3's table). The rows marked "per Q8" are written under round
    1's 2A. `register-row-local` reads the database, 8 included, and exits
    69, never 8, where the read fails (D11);
  - each mirror read prints its `read: mirror` line on stderr and keeps its
    own exit (P438–447);
  - a fake `psql` that fails exactly one call, run across each caller's read
    sequence (ADV-C5);
  - for each arm of lane 4's table of the callers' default arms (lane 4's
    section, "The callers' default arms (Q9)", accepted in comment
    6708889286), with the verb exiting 75, 3 or 69. "Pass the code" there
    means the arm exits with the verb's code, not 1:
    - `lane-start`'s seven read arms, at `code/lane-start:1154`
      (`canon-lane`), `:1262` (`swapped`), `:1271` (`lane-dir`), `:1332`
      (`resolve-home`), `:2184-2186` (`session-lane`), `:3079` and `:3430`
      (`lane-transcript`): the verb exits 75 or 3, and the launch refuses,
      with the register's rows and log unchanged (P475–483; `design.md` S3).
      A 69 refuses at `:1154` and `:2184-2186`, by 2A, and stays a note at
      `:1262`, `:1271`, `:3079` and `:3430`. Lane 4's table gives `:1332` no
      69 arm, so no 69 case is written there. `:1332` and `:2184-2186` are
      the two arms not in the frame's list (lane 4's row 23);
    - the other callers' arms: `lane-end:930–938` and
      `lane-worktrees:508–524` (`canon_lane`) refuse 69, 75 and 3;
      `lane-end:2490` exits with the code; `lane-handoff:2817` passes the
      code; `lane-handoff:1463` and `SKILL.md:112` (`sread`) have a `69)` arm
      that re-reads from the mirror and prints the `read: mirror` line;
      `lane-rename:241` has new text for 3, 75 and 69;
    - the arms that already refuse, which pass the code: `lane-start:1555`,
      `:1641–1643`, `:1661–1663`, `:2305`, `:2335`, `:2444`, `:2511–2512`,
      `:2534` and `:2638`; `lane-end:769`, `:955`, `:1806` and `:1984`;
      `lane:416`, `:444`, `:1058` and `:1144`;
  - every act that carries on after a failed write relays the writer's
    printed rerun on a 3, never its own hint (lane 4's section, "The callers
    that read exit 3 as 'written' (F9)", item 2): `lane-start`'s writes
    (`:3851`, `:3872`, `:3909`, `:3989`, `:4022` and `:4181`),
    `lane-handoff` (`:2878–2881`, `:2890–2891` and `:2913–2914`),
    `lane-end:2490`, and the skill (`:738–742`). An `expectation:` 2 at
    `lane-start:4181` dies;
  - `lane-worktrees`' three direct reads of the register go through a verb,
    and exit 69 with nothing deleted (`design.md` S3's table).
- **Phase and kind.** N: the readers' SQL path and the callers' arms. FP. The
  arms' case swaps one verb through `LANES_EDIT`, which `lane-start` honours
  (`code/lane-start:622`).
- **Fixture.**
  - A workstation configured `mode=primary`, with the flipped build (open
    point 3).
  - A fake `psql` with a call counter, which exits 2 on call k only, for k
    from 1 to the sequence's length.
  - For the arms, a wrapper named by `LANES_EDIT`. It runs the checkout's
    `lanes-edit.sh` for every verb but the one under test, which exits the
    code under test, 75, 3 or 69, and prints nothing on stdout. The same
    wrapper serves each other caller that reads `LANES_EDIT` as `lane-start`
    does; one that does not is a finding of the test's first run, and goes
    into section 6.3 then. For the rerun cases, the verb under test exits 3
    and prints the writer's rerun line on stderr, `LANES_WRITE_ID=<id>
    LANES_WRITE_GENERATION=<generation> LANES_LANE=<lane>
    LANES_SESSION=<uuid> lanes-edit.sh <the same arguments>`.
  - The fake `claude` and `tmux`.
- **Oracle.**
  - **Each reader in the table,** with the store unreachable:
    - the table's exit (69, 1 for `managed-projection`, or 2 for the guard);
    - nothing on stdout where the table says "no answer";
    - never 8 for a read that failed.
  - **The listings** print `read: mirror at <sha12>, …` on stderr and exit
    with their own code.
  - **One failed call.** For each caller (`lane-start`, `lane-end`, `lane`,
    `lane-handoff`, `lane-rename`) and each k:
    - the caller refuses, or follows round 1's 2A where a note is allowed;
    - it never makes a second row, which is the `:1154` hazard;
    - the register's rows and log are unchanged wherever it refused.
  - **The arms.**
    - `lane-start`, at each of its seven read arms, exits non-zero before the
      launch, and the fake `claude` does not run.
    - Stderr names the code, and says that nothing was written and a retry in
      seconds is safe.
    - The rows and logs are unchanged.
    - `lane-end:930–938` and `lane-worktrees:508–524` refuse 69, 75 and 3
      with the verb's own code, and write or delete nothing; `lane-end:2490`
      exits with the code; `lane-handoff:2817` and each arm that already
      refuses pass the code, never 1; the `69)` arm of `lane-handoff:1463`
      and `SKILL.md:112` re-reads from the mirror and prints the
      `read: mirror` line; `lane-rename:241` prints new text for 3, 75 and
      69, and no longer the note that a 3 means the commit is local.
  - **The rerun on a 3** (F9, item 2). For each carry-on act, with the verb
    under test exiting 3 and printing the writer's rerun line:
    - the act's stderr carries that line as printed, and none of the act's
      own hint (so no hint that reads a 3 as written, and none that leaves
      out `LANES_SESSION`, as `lane-start:4182` does today);
    - `lane-handoff:2890–2891` and `:2913–2914`, which send the writer's
      stderr to `/dev/null` today, keep the rerun line;
    - the act carries on where it carried on before, and dies where lane 4's
      table says, on an `expectation:` 2 at `lane-start:4181`;
    - after phase 2, a `lane-start` write that `start-record` absorbs is
      AT-11's `start-record` case: a resume's note relays the write id and
      its printed rerun, and the launch goes on; a new lane dies, its
      stderr carrying the writer's rerun line as every act's does.
  - **The busy code** is 75 (D7), as Brett's answer to Q7, (A″) with
    "register busy" at 75, has it.
- **Tests.** In `code/tests/test_sql_register_readers.py`:
  - `test_each_reader_follows_the_table_when_the_store_is_unreachable[<reader>]`;
  - `test_each_mirror_read_prints_its_line_and_keeps_its_exit[<listing>]`;
  - `test_one_failed_call_anywhere_in_a_callers_read_sequence_never_fails_open[<caller>-<k>]`;
  - `test_75_and_3_refuse_the_launch_at_each_lane_start_arm[<arm>-<code>]`,
    where `<arm>` is `1154`, `1262`, `1271`, `1332`, `2184-2186`, `3079` or
    `3430`;
  - `test_69_refuses_at_canon_lane_and_session_lane_and_stays_a_note_at_the_other_four_arms[<arm>]`,
    where `<arm>` is `1154` and `2184-2186` (refused) or `1262`, `1271`,
    `3079` and `3430` (a note);
  - `test_the_other_callers_arms_pass_the_code_or_refuse_as_lane_4s_table_gives[<arm>-<code>]`,
    where `<arm>` is `lane-end-930-938`, `lane-end-2490`,
    `lane-worktrees-508-524`, `lane-handoff-1463`, `lane-handoff-2817`,
    `skill-112`, `lane-rename-241`, or an arm that already refuses
    (`lane-start-1555` to `lane-start-2638`, `lane-end-769`, `lane-end-955`,
    `lane-end-1806`, `lane-end-1984`, `lane-416`, `lane-444`, `lane-1058`,
    `lane-1144`), and `<code>` each of 75, 3 and 69 that the arm's row names;
  - `test_a_carry_on_act_relays_the_writers_printed_rerun_on_a_3[<site>]`,
    where `<site>` is `lane-start-3851`, `lane-start-3872`,
    `lane-start-3909`, `lane-start-3989`, `lane-start-4022`,
    `lane-start-4181`, `lane-handoff-2878`, `lane-handoff-2890`,
    `lane-handoff-2913`, `lane-end-2490` or `skill-738`;
  - `test_an_expectation_2_at_lane_starts_last_write_dies`;
  - `test_register_row_local_reads_the_database_on_primary_and_is_69_never_8`;
  - `test_lane_worktrees_register_reads_go_through_a_verb_and_refuse_69[<read>]`;
  - `test_index_flag_on_primary_changes_nothing_and_prints_no_index_line`:
    with and without `--index`, stdout and the exit are equal, and stderr
    has no index line;
  - `test_managed_projection_answers_0_1_8_from_sql`: for a register set up
    to give each answer, the answer equals files mode's on the same register,
    and with the database unreachable it exits 1, never `legacy` (D10, S4).
- **Run time.** One run per call in each sequence: a few dozen `lane-start`
  runs at 0.3–1 s, so 30–60 s. The arms' cases and the rerun cases add about
  50 more runs at the same cost, so 15–50 s. This is the largest FP test,
  and section 7 asks whether k should be sampled instead.
- **Flakiness.** None is timed. The call counter is in the fake's log file, so
  it does not depend on process order.

#### AT-P3. The outage's refusal text and its two ways out (P3)

- **Proves.** P422–437 and P491–493:
  - the guard's refusal on an unreachable database names the store, the cause
    and the two ways out: wait, or run `lanes-index status` and
    `lanes-index rollback` from a shell or a `CLAUDE_NO_LANE=1` session;
  - `skip-no-lane-guard` lets such a session through before any input is
    read;
  - `status` and `rollback` run with no lane session and no guard.
- **Phase and kind.** N, with `rollback`'s case in G. FP for the guard, and RE
  (a stopped cluster) for `status` and `rollback`.
- **Fixture.**
  - A workstation configured `mode=primary`.
  - A fake `psql` exiting 2, or the stoppable cluster, stopped.
  - The hook's JSON input. For the skip, a stdin that blocks if it is read.
  - No `tmux` window and no session record, so there is no lane session.
- **Oracle.**
  - The guard exits 2. Stderr names the database, without credentials, and
    the cause, and carries both commands and `CLAUDE_NO_LANE=1`.
  - With `CLAUDE_NO_LANE=1`, the hook exits 0 at once, without reading its
    blocking stdin.
  - `lanes-index status` answers inside its bound with its own exit, naming
    the unreachable store.
  - `rollback` runs, and is refused with the database down (Q4(b)(iii)),
    rather than blocked by a guard.
  - With the database lost for good, the second way out is S5's disaster
    procedure (Q4(b)(iii); P433–435), and the test pins the text the
    design prints for it.
  - A declared outage changes the guard's answer (Q26 (a′); test 10), not
    this text.
- **Tests.** In `code/tests/test_sql_register_outage.py`:
  - `test_the_guards_outage_refusal_names_the_store_the_cause_and_both_ways_out`;
  - `test_claude_no_lane_lets_a_session_through_before_any_input`;
  - `test_status_and_rollback_run_with_no_lane_session_and_no_guard[status|rollback]`.
- **Run time.** About 3–5 s.
- **Flakiness.** None.

#### AT-P8. The operator tools: bootstrap, readiness check, flip (P8)

- **Proves.** P600–627, as §2.4 names the three tools (`lanes-index
  bootstrap`, `ready` and `set-mode`):
  - the bootstrap, run twice, leaves one schema and the same GRANTs, and its
    second run prints `unchanged` for every object;
  - it prints the password-file lines only to a terminal, and stores no
    secret;
  - it refuses with 2 an argument that carries a connection string;
  - the readiness check fails on each of its faults: a stale digest, a
    missing `psql`, an early `mode=primary`, a role with a GRANT outside its
    scope, an installed `lanes-index` that still refuses `mode=primary`
    (check 5; Q6(c): `lanes-index mode` exits 78), the workstation's name
    unset (check 6), an outage declaration recorded (check 7), and, with
    `--expect primary`, a switch that is not `primary` with the marker
    `present` or an `origin/main` whose `lanes/STORE` names another database
    (check 8), and tools outside the schema's version window (check 9,
    folded at `21f0191c` from lane 3's ruling 6578020193; §2.4);
  - the flip writes only `mode=` and keeps the file's 0600, and setting
    `primary` refuses with 2 unless the switch is `primary`, the marker
    `present`, and the fetched `lanes/STORE` names this database.
- **Phase and kind.** N. RE for the bootstrap and the GRANT check, FP for the
  digest, `psql` and refusing-build checks, and FM for the flip.
- **Fixture.**
  - A `stoppable_cluster` of the test's own (section 3.1), since the
    bootstrap creates roles, which are global to a cluster, and no test
    changes a role on the session cluster. In it, a database cloned from
    `template0`, not from a bootstrapped template.
  - The admin connection as §2.4 takes it: `--admin-service` names a service
    in the test's own `$HOME/.pg_service.conf`, with a 0600 `~/.pgpass`
    beside it, so no connection string is ever an argument. The service names
    the cluster's socket and its bootstrap superuser. `lanes-index` hands it
    to `psql` only as `PGSERVICE` in the environment, never as `service=` or
    `-d`, and drops an inherited `PGSERVICEFILE`, `PGSYSCONFDIR`,
    `PGPASSFILE` or `PGPASSWORD` (SVC-2, lane 5's comment 6579523948,
    accepted by lane 3 in 6579562840). A second case uses a non-superuser
    `CREATEROLE` admin, as lane 1's own runs did.
  - The four inputs the switch's NOT NULL columns need: `--admin-service`,
    `--label`, `--repository` and `--lane`.
  - Every run but one passes `--no-passwords`, since the cluster trusts its
    socket. The one that prints password lines runs under a pty
    (`pty.openpty()`), because the bootstrap refuses when stdout is not a
    terminal.
  - An installed-tools directory with the release's digest list (its form is
    the design's).
  - A `PATH` with `psql` and one without.
  - Configurations at `mode=index` and `mode=primary`.
  - An extra GRANT the test adds.
  - Test 14's refusing copy of `lanes-index`.
- **Oracle.**
  - **The bootstrap, run twice.**
    - The first run prints `created` for each object, and the second exits 0
      and prints `unchanged` for each.
    - A catalog dump is identical before and after the second: tables,
      columns, constraints, `information_schema.role_table_grants` and the
      routines' privileges.
    - After the first, the switch reads `files` with the marker `absent`,
      `set_seq` -1 and a generation id, the head reads -1, and the journal is
      empty (§2.4).
    - Under the pty, it prints one password-file line per role it created.
      Through a pipe, without `--no-passwords`, it refuses and creates
      nothing.
    - No new file under the test's `$HOME` or working directory holds a
      secret.
    - `--admin-service 'host=… user=…'`, a connection string, exits 2.
    - In the FP case, the fake `psql`'s log shows no service, `-d` or
      connection string in any argv, `PGSERVICE` set in each call's
      environment, and none of the four dropped variables, though the test
      set all four before the run.
  - **The readiness check.**
    - Each fault exits 2 and names that fault on its own `FAIL` line.
    - A ready environment exits 0, with one `ok` line per check.
  - **The flip.**
    - A diff of the configuration file shows only the `mode=` line.
    - The file stays 0600.
    - The password file is unchanged.
    - A configuration the flip cannot write is refused, with nothing
      written. A read-only mount is flipped from the host (P620–622).
- **Tests.** In `code/tests/test_sql_register_operator_tools.py`:
  - `test_bootstrap_run_twice_leaves_one_schema_and_the_same_grants`;
  - `test_bootstrap_prints_password_file_lines_and_stores_no_secret`, under
    a pty;
  - `test_the_bootstrap_refuses_when_stdout_is_not_a_terminal`;
  - `test_the_bootstrap_refuses_a_connection_string_argument_with_2`;
  - `test_the_bootstrap_passes_the_admin_service_only_as_pgservice` (FP);
  - `test_the_bootstrap_runs_as_a_non_superuser_createrole_admin`;
  - `test_set_mode_primary_refuses_before_the_marker_is_present_or_names_another_database`;
  - `test_the_readiness_check_fails_on_each_fault[<fault>]`;
  - `test_the_readiness_check_passes_a_ready_environment`;
  - `test_the_flip_writes_only_mode_and_keeps_0600`;
  - `test_the_flip_refuses_a_configuration_it_cannot_write`.
- **Run time.** About 5–10 s.
- **Flakiness.** None.

#### AT-C8. A render forked under `LANES_NO_FETCH=1` (ADV-C8)

- **Proves.** C172–184. A render forked from a verb run with
  `LANES_NO_FETCH=1`, as 61 caller calls run them, still renders. D12 forks
  the renderer with the variable removed from its environment. This holds
  under Q27 (a), which keeps S3's fetch.
- **Phase and kind.** G. RE, with git.
- **Fixture.**
  - A primary estate after the cutover, and a lane checkout.
  - `log NOTED` run with `LANES_NO_FETCH=1`.
  - Test 5's `git` wrapper, logging each call and whether the variable was
    set in its environment.
- **Oracle.**
  - Origin gains the write's render commit, and `status` reports a lag of 0.
  - The wrapper's log shows the renderer's own fetch, or the design's
    equivalent, made with the variable absent from its environment (D12).
- **Options.** What the variable means for a `mode=primary` writer is
  settled by D12: it has no effect on a primary write. Test 8 covers the
  writer's half, and this test only the renderer's.
- **Tests.** In `code/tests/test_sql_register_renderer_git.py`:
  - `test_a_render_forked_from_a_no_fetch_verb_still_renders`.
- **Run time.** About 2 s.
- **Flakiness.** The wait for the render is open point 8's poll of `status`
  until the lag is 0.

#### AT-C10. An alias edit racing a claim (ADV-C10)

- **Proves.** C201–216. An alias edit racing a claim cannot let two claims of
  one object pass:
  - two claims of one object, spelled two ways, from clones at different
    tips, end with one `CLAIMED` and one exit 2;
  - one spelling is an alias added to `lanes/repos.tsv` between the two
    claims' reads of it.

  "The test is the proof", since the text does not assert that the
  pre-check compares the canonical key (C213–216).
- **Phase and kind.** W. RE, with git.
- **Fixture.**
  - The imported register and a bare origin.
  - Clone A at T0, without the alias.
  - A person's commit T1, adding alias X for object O to `repos.tsv` and
    pushed by a third clone.
  - Clone B, which fetches T1.
  - A's claim of O, spelled canonically, is held by test 4's shim at its
    commit call. That is after its verb read `repos.tsv` from A's checkout's
    `origin/main` at T0 (D19; `code/lanes-edit.sh:16509`). T1 is then
    pushed.
  - B then claims X, and A is released.
- **Oracle.**
  - Exactly one `CLAIMED` holds O in SQL.
  - The exit codes are {0, 2}.
  - No line holds X as an object of its own.
  - The refusal is the commit's spelling check, which refuses with 2 a claim
    whose set of spellings meets another lane's live claim (D19).
- **Tests.** In `code/tests/test_sql_register_order.py`:
  - `test_an_alias_edit_racing_a_claim_cannot_let_two_claims_of_one_object_pass`.
- **Run time.** About 2–3 s.
- **Flakiness.** The race is staged by holds, not by timing.

### The frame's own choices

The frame asks for a test of each of its own choices. Four are
inside entries above:
- `register-row-local` on primary (D11) is in test C5;
- the marker's fields (D21) are in test 7's cutover;
- the refused hold line that names no repository is test 4's case (f);
- the declared outage's use by the guard is in test 10.

The five below have entries of their own. Test D19 is lane 3's ruling 6
(#165 comment 6578020193).

#### AT-D8. The pending record's rerun

- **Proves.** D8, and `design.md` S1's "The write id" and "A write in doubt":
  - before the first commit call, `lanes-edit.sh` writes
    `<state>/register-pending/<digest>`, which holds the id, the UTC and the
    command line;
  - the record is removed once the outcome is known;
  - a later run of the same command line, with no `LANES_WRITE_ID`, reuses
    the id, says so on stderr, and consumes the record, so only the first
    rerun is the retry;
  - a write killed by an outer timer, which can print nothing, is still
    retried under its id;
  - `lanes-index status` lists the pending records and resolves each by
    looking its id up.
- **Phase and kind.** W. RE, with test 12's shim.
- **Fixture.**
  - The imported register.
  - `lanes-index`'s state directory, under the test's `$HOME`.
  - The shim's `no-reconnect` mode.
  - For the killed write, the shim's `timer` mode, joined to a FIFO: once the
    real `psql` has returned, the shim writes to the FIFO and blocks. The test
    reads the FIFO and then sends SIGKILL to `lanes-edit.sh`'s process group.
    The row has then landed, and the writer cannot print. No wall-clock timer
    decides the order.
- **Oracle.**
  - **After a committed write,** no pending record remains.
  - **After exit 3,** the record exists and names the id.
  - **A plain rerun of the same command line:**
    - says on stderr that it reuses the id;
    - exits 0;
    - leaves one journal row for the id;
    - removes the record.
  - **A second plain rerun** is a new write, under a new id.
  - **A rerun with other arguments** does not take the id.
  - **`LANES_WRITE_ID`,** set explicitly, wins over a record.
  - **The killed write's rerun** reuses the id, exits 0, and leaves one row.
  - **`lanes-index status`** lists a pending record, and reports whether its id
    is in the journal.
  - **A rerun that finds its id committed** runs the verb's after-COMMIT
    tail: `rename-lane`'s `lane_state_rename` runs once, on the rerun. Lane
    3 ruled it so (lane 4's row 24, comment 6710770304): every such tail
    must be safe to run twice, and the design lists each verb's tail.
- **Options.** Under Q16 (a) alone there is no record, and the test is not
  written.
- **Tests.** In `code/tests/test_sql_register_in_doubt.py`:
  - `test_a_committed_write_leaves_no_pending_record`;
  - `test_a_plain_rerun_reuses_the_pending_write_id_once_and_says_so`;
  - `test_a_write_killed_by_an_outer_timer_is_retried_under_its_own_id`;
  - `test_a_rerun_with_other_arguments_does_not_take_the_pending_id`;
  - `test_lanes_write_id_wins_over_a_pending_record`;
  - `test_status_lists_and_resolves_pending_records`;
  - `test_a_rerun_that_finds_its_write_committed_runs_the_verbs_after_commit_tail`.

  A test per verb's tail comes in the pass after lane 3's review, once the
  design lists each verb's tail (lane 3, comment 6710770304).
- **Run time.** 15–25 s. The 10 s reconnect window and the 8 s timers dominate.
- **Flakiness.** The kill is sent on the shim's FIFO signal, after the real
  `psql` has returned, so the row is known to have landed and neither the 8 s
  timer nor the kill can fire first on a loaded host.

#### AT-D9. Every row of the timeouts table

- **Proves.** D9: each path of the timeouts table ("The timeouts (D9)")
  exits inside its bound, with the outcome the table names on expiry. The
  delta spec's "Every database path is bounded" asks the same. Only the
  guard's row is measured (the probes). The other rows are the design's, "for
  lane 2's tests to confirm", and this test confirms them.
- **Phase and kind.** N for the guard, the service, a reader, `status` and the
  renderer's reads. W for the writer's rows, and G for `rollback`'s. RE, with
  loopback stand-ins.
- **Fixture.** The session cluster, with a database per test: the faults are
  stand-ins, locks and hooks, so nothing stops the cluster. Each path's
  configuration names one address. The rows are the timeouts table's at
  `21f0191c`, with lane 1's `import` row and `reconcile` beside the
  renderer's reads (§1.3; lane 3, comment 6578020193). There are four faults:
  - **F1:** a listener that accepts and never speaks;
  - **F2:** a relay that forwards to the cluster's socket until the startup
    completes, then goes silent;
  - **F3:** a lock held by the test's own session: an `ALTER TABLE` in an
    open transaction on the table the path reads, or for a writer's commit,
    the head row held `FOR UPDATE`;
  - **F4:** a slow statement, through a sleep the test creates in its own
    database after the clone, never in the template: a row-level security
    policy on `register_head` for the reading roles, or a trigger on
    `journal` for the commit, that sleeps past the path's
    `statement_timeout`. It is the only
    fault that reaches `statement_timeout`; without it, a design that left
    the setting out would pass, caught by the client timer.
- **Oracle.** The path exits with its row's expiry outcome, inside the bound
  in the row's last column.
  - **A simple row,** one call under one timer, is bounded by its client timer
    plus a 1 s allowance for process start, stated in the test. The guard's is
    under 4.5 s, strictly.
  - **A composite row** chains timers, so it has its own bound, the sum of the
    parts that can run in sequence plus the same 1 s. The 10 s of reconnecting
    is a floor, not a ceiling: a reconnect begun inside the window runs its own
    snapshot timer to its end. And an attempt begun before the deadline may
    finish after it. What the design forbids is an attempt that *starts* after
    the deadline, so the shim's log of each call's start time is asserted too.

  | Row | Its timer | Expected | Bound on the elapsed time |
  |---|---|---|---|
  | the guard's direct read | cut to the parse's start line, `T0 + 3.75 s`; `T0 + 3.65 s` under a declaration naming this database (SVC-8) | 2, for F1 to F3 | under 4.5 s, strictly |
  | the service's connection, per request | 200 ms, inside the client's 300 ms wait; 150 ms and 180 ms at the server | `unavailable`, then the client's direct read; `refuse db` only where the refusal is definitive (SVC-7) | the client's 300 ms wait + 1 s |
  | the guard's answer 2 | none of its own: the bind runs detached in `guard-bind`, under a writer's bounds and a 180 s bound of its own (AT-F6) | 2, "BINDING" | under 4.5 s, strictly |
  | a writer's snapshot | 8 s | F1 and F2: 69, since no commit was sent. F3: a failed read, 69, as the table's snapshot row says | 9 s |
  | a writer's commit, F2 | 8 s, then 10 s of reconnecting | in doubt, then 3 | at least 17 s (the commit's 8 s, and the 10 s window less one second, since a window kept in whole seconds, as the deadline's `SECONDS` is, may close early by up to one); under 8 + 10 + 8 + 1 = 27 s, where the last 8 s is a reconnect begun inside the window that meets the silent relay |
  | a writer's commit, F3 | 2000 ms `lock_timeout` per attempt | 55P03 at about 2 s, a new attempt, then 75 at the deadline (`LANES_REGISTER_DEADLINE_S=3` here) | under 3 + 8 + 8 + 1 = 20 s: the deadline, then one attempt's snapshot and commit timers. No snapshot call starts after 3 s |
  | a whole write | 30 s; 10 s | 75 (test 4's case (d)); 3 (test 12) | for 75, under the deadline + 8 + 8 + 1 s, and no attempt starts after the deadline; for 3, the bound of the commit's F2 row |
  | a reader | 6 s | 69, with nothing on stdout | 7 s |
  | the renderer's reads, and `reconcile` | 15 s | the render ends, and `status` then shows the lag; `reconcile` exits 69 | 16 s |
  | `import` | 330 s | FP only, since a 330 s timer is not run: the argv carries `connect_timeout=2` and `options=-c lock_timeout=2000 -c statement_timeout=300s`, and the client timer is 330 s; on expiry, 69 and nothing is swapped in | none run |
  | `status` | 8 s | 69, with the lag from git | 9 s |
  | `rollback`, each call | 15 s | 69. Where step 1's call expires, the rollback reads the switch again and never goes on to step 2 on an unknown switch | one call, 16 s. Where step 1's call and the switch's re-read both expire, 15 + 15 + 1 = 31 s |
  | a migration | its own `lock_timeout` | refused, and a rerun succeeds once the reader's transaction ends. A new plain read is not held longer than that `lock_timeout` | the migration's `lock_timeout` + 1 s |
  | the mode query, `lanes-index mode` (lane 3, comments 6710770304 and 6711028142) | a 2 s client timer; no `connect_timeout`, `lock_timeout` or `statement_timeout`, since the query reads only the configuration | the query killed; files mode, with a warning naming the timeout (F8's table); on a SQL estate the marker check after S3's fetch then refuses with 69, and nothing is written (D3; the delta's "The mode query fails") | the query's end under 3 s (this plan's 1 s allowance beside lane 3's 2 s timer). Its fault is AT-F8's hanging stand-in, not one of F1 to F4, and AT-F8's test owns the case |

  F4, on every row that sets a `statement_timeout`: the shim's log shows
  57014 at the path's `statement_timeout`, plus the 1 s allowance, and before
  its client timer, and the path exits with its row's outcome. For a
  writer's commit that outcome is 69, a refusal the database made (lane 1's
  "The signals a writer gets").

  Beside the table: a session as `lanes_register_writer` that replays a
  commit call's script without its `COMMIT` (test 12's held session) holds
  the head row and idles. `idle_in_transaction_session_timeout` ends it at
  about 5 s, after which a waiting write commits.
- **Tests.** In `code/tests/test_sql_register_bounds.py`:
  - `test_each_database_path_exits_inside_its_bound[<row>-<fault>]`, which
    owns test 10's per-path cases;
  - `test_a_slow_statement_meets_57014_before_the_client_timer[<row>]`;
  - `test_an_orphaned_writer_transaction_is_ended_by_the_idle_bound`;
  - `test_a_rollback_whose_first_call_expires_rereads_the_switch`;
  - `test_a_waiting_migration_gives_up_at_its_own_lock_timeout`;
  - `test_import_and_reconcile_set_their_bounds_at_connect` (FP).
- **Run time.** About 3–4 min, F4's rows adding about 40 s. The rollback's
  two 15 s calls and the commit's F2 row, up to 27 s, dominate. Open point
  9 asks whether the long rows run on every trigger.
- **Flakiness.**
  - Every bound is the path's own timers, summed for a composite row, plus a
    stated allowance.
  - Listeners and relays are per test, on port 0.
  - Locks are the test's own sessions.

#### AT-D19. Two writers claim one object under two spellings at once

- **Proves.** D19, lane 3's ruling 6 (#165 comment 6578020193): two claims
  of one object, spelled two ways and committed at once, end with one
  `CLAIMED` and one exit 2. It extends test 4's case (a), whose claimants use
  one spelling, and test C10, whose clones sit at different tips and race in
  turn. Here both commits meet at the head row together.
- **Phase and kind.** W. RE, with git.
- **Fixture.**
  - The imported register, with object O free, and alias X mapping to O in
    `lanes/repos.tsv`.
  - Two lanes, L1 and L2, on two clones.
    - **[same-tip]:** both clones at the tip with the alias, so both
      canonicalize X to O.
    - **[behind-the-alias]:** L2's clone at the tip before the alias, so L2's
      key for X is X itself.
  - L1 claims O and L2 claims X. Test 4's shim holds each at its commit call,
    after both snapshots have read base `n`.
  - **The barrier,** from `pg_stat_activity` (section 3.1), never a sleep:
    1. the test's admin session takes the head row `FOR UPDATE` in an open
       transaction, as test D9's F3 does;
    2. the test releases L1's commit call, and polls until L1's backend shows
       `wait_event_type = 'Lock'`;
    3. it releases L2's commit call, and polls until L2's backend waits too;
    4. it ends its own transaction with `ROLLBACK`, so the head row is
       unchanged and L1, first in the lock's queue, takes it.

    Both writers connect as `lanes_register_writer` and set no
    `application_name`, so the test tells their backends apart by
    `query LIKE '%<write_id>%'`, with each write id read from the shim's log:
    the id is `commit_write`'s second argument, well inside the default
    1024-byte `track_activity_query_size`. Each poll is bounded at 0.1 s
    steps under one 1.5 s ceiling, inside the commit call's 2000 ms
    `lock_timeout`; a 55P03 in the shim's log fails the test rather than
    retrying it.
- **Oracle.** `design.md`'s "The schema", §1.3 ("The signals a writer gets"),
  gives the outcome:
  - **The winner is L1,** the commit that took the head row first. It exits
    0, and prints `register seq <n+1>`.
  - **L2's first commit fails with 40001** on the head row L1 updated: both
    snapshots predate L1's commit. L2 re-reads at `n+1` and re-runs its
    verb. It never exits 69 or 75.
  - **L2 exits 2,** writes nothing, and its stderr names L1's claim, the
    holder (Q12 (a); D10, under which `claim` no longer spends 7; the same
    shape as D18's later `HOLD`).
    - [same-tip]: the refusal is the verb's own check on the canonical key,
      or LR005, into which `commit_write` raises `one_live_claim`'s 23505
      again (§1.3). Either is right, and both exit 2.
    - [behind-the-alias]: L2's object column is X, so neither the verb's
      check nor `one_live_claim` can see L1's claim. The refusal must be
      LR005 from the spelling check (D19), and the shim's log shows it on
      L2's last commit call.
  - **The journal grew by one:** seq `n+1`, L1's claim, whose `object_holds`
    row carries `spellings` with O as typed, the key O, and the alias X. L2's
    write id is absent. Exactly one live `CLAIMED` holds O, and no line holds
    X as an object of its own.
- **Tests.** In `code/tests/test_sql_register_order.py`:
  - `test_two_spellings_of_one_object_claimed_at_once_leave_one_claimed_and_exit_2[same-tip|behind-the-alias]`;
  - `test_the_claim_behind_the_alias_is_refused_by_lr005_on_its_spellings`.
- **Run time.** About 1–2 s each.
- **Flakiness.** The order is fixed by the head row's lock queue, staged
  through `pg_stat_activity`, not by timing.

#### AT-D20. The declared outage and its expiry

- **Proves.** D20 and `design.md` S3's "The declared outage (Q26's (a′))",
  with SVC-12 and §2.5:
  - `lanes-index outage declare --until <UTC> --why "<words>"` writes
    `register-outage` beside `lanes-index.conf`, holding the database's
    label, the words verbatim, the UTC, the host, the expiry,
    `mirror-seq=<m>` (F11) and the last head `status` recorded;
    `lanes-index outage clear` removes it;
  - it refuses with 2 while the database answers from this host, unless the
    switch reads `files`, and with 2 when `--until` is missing;
  - the guard reads the file once, at the prefetch, and uses it only when its
    database read fails (SVC-12's first condition);
  - where the file is present, unexpired, and names this workstation's
    database, and the mirror is at `mirror-seq` or above, the guard answers
    from the mirror, with `(declared outage <utc>: <why>)` in place of the
    cause;
  - a rollback's start declares the outage, marked as from a rollback (D20;
    lane 3, comment 6579459518; §2.3): `rollback`
    writes the record where its configuration directory is writable, and on a
    read-only mount prints the `outage declare --rollback` command;
  - every write and every other deciding read still refuses with 69;
  - `status` reports a declaration left in place while the database answers.
- **Phase and kind.** N. FP for the unreachable database, FM for the mirror;
  the rollback's case is G, with test 8's estate.
- **Fixture.**
  - A configuration with `mode=primary`, and a fake `psql` that exits 2.
  - A clone whose `origin/main`, at or above `mirror-seq`, carries a
    register in which this session's lane is bound (`ok`) and, in a second
    case, held by another session (`refuse`).
  - The hook's input.
  - For the rollback's case, test 8's estate, its rollback held at its push
    by the `git` wrapper; once with a writable configuration directory, and
    once with it read-only.
- **Oracle.**
  - The file's fields, `mirror-seq` and the last head among them, and its
    removal by `clear`.
  - `outage declare` with the database answering and the switch at
    `primary` exits 2 and writes no file; with no `--until`, 2.
  - With a valid declaration:
    - the guard exits 0 for `ok` and 2 for `refuse`, printing the declared
      line;
    - `claim` and `who` exit 69.
  - With an expired declaration, or one naming another database: the guard
    exits 2 with the ordinary outage refusal.
  - With the database answering and the switch at `primary`, the file is
    ignored, and `status` reports it.
  - **The rollback's start.** While the rollback is held, the record exists,
    marked as from a rollback, and the guard, whose read finds the switch at
    `files`, answers from the mirror. On the read-only mount no file is
    written, and stdout carries the `outage declare --rollback` command. The
    rollback's step 6 ends with `outage clear`.
- **Tests.** In `code/tests/test_sql_register_outage.py`:
  - `test_outage_declare_writes_its_fields_and_clear_removes_it`;
  - `test_outage_declare_refuses_while_the_database_answers_or_without_until`;
  - `test_a_declared_outage_lets_the_guard_answer_from_the_mirror[ok|refuse]`;
  - `test_an_expired_or_foreign_declaration_changes_nothing[expired|other_database]`;
  - `test_writes_and_deciding_reads_still_refuse_during_a_declared_outage`;
  - `test_status_reports_a_declaration_left_in_place`;
  - `test_a_rollbacks_start_declares_the_outage[writable|read-only]`.
- **Run time.** 3–5 s.
- **Flakiness.** Expiry is tested with a UTC already past, never by waiting.

#### AT-D22. The disaster path

- **Proves.** D22 and `design.md` S5's "The disaster path", which is
  Q4(b)(iii)'s path. With the lanes paused and the outage declared:
  - take `m`, the mirror's last `Register-Seq`, and `b`, the newest backup's
    head;
  - **if `b > m`,** restore that backup, and the cutover's entry in its
    restore form, at `b+1`, renders the mirror up to `b` and rewrites the
    marker;
  - **otherwise,** re-import at origin's tip into a new database with the head
    at `m`, and the cutover's after-loss entry at `m+1` rewrites the marker,
    so the next write is `m+2` and `Register-Seq` stays monotonic (D22, D24,
    §2.3; lane 3's F18 ruling and comment 6579459518);
  - clear the declaration, and name the lost seqs.
- **Phase and kind.** G. RE, with git.
- **Fixture.**
  - Stoppable cluster A, written through seq `m` with its renders done (a lag
    of 0). Test 5's `git` wrapper is then armed to fail every `fetch`, and A
    is written through seq `k > m`. The test waits for each renderer's exit
    (open point 8's second wait), and `status` shows a lag of `k − m`.
  - For the restore branch, a backup of A taken at head `b`, with
    `m < b ≤ k`. The backup tool is open point 15.
  - A is then destroyed, and stoppable cluster B is new. `lanes-index
    bootstrap --roles-only` runs on B first (F18). For the restore branch
    the backup is restored next, and then `bootstrap` runs again; for the
    re-import, `bootstrap` follows at once (§2.3, the disaster path's
    step 6).
  - Every operator command runs with `LANES_LANE=` the operator lane, through
    its checkout (F12).
- **Oracle.**
  - **Re-import** (§2.3, step 6).
    - After `lanes-index import --at <M> --after-loss`, B's head is `m`, and
      the journal's one root sits at `m`.
    - `lanes-index cutover --import <M> --after-loss` then writes the
      cutover's own entry at `m+1`, an operator entry, in the transaction
      that sets the switch to `primary` with B's label, a new generation id
      and `set_seq` `m+1`. Its command pushes the rewritten `lanes/STORE`,
      whose `database=` names B and whose commit carries
      `Register-Seq: m+1`, confirms it by the read-back, and the marker reads
      `present`.
    - The next write is seq `m+2`, and its render commit follows the marker
      commit on origin.
    - `status` shows a lag of 0, and the declaration is gone.
    - The mirror equals a full render from B.
    - The notice names `m+1` to `k` as lost in the old database.
  - **Restore.**
    - B's journal holds `m+1` to `b`, and its switch reads `primary` with the
      marker `present`, as the backup had it.
    - `lanes-index cutover --after-loss`, with no import, renders every seq
      the mirror lacks, up to `b`, in order, then writes the cutover's entry
      in its restore form at `b+1`, rewriting `lanes/STORE` to name B (lane 3,
      comment 6579459518).
    - The next write is `b+2`, and the notice names `b+1` to `k` as lost in
      the old database.
  - **An abort after a restore-form cutover** (lane 3's ruling, comment
    6579459518, point 1; lane 1's v2.3, comment 6708990159). The restore
    form's cutover records what it replaces in
    `register_switch.restore_prior`: the switch row, and `lanes/STORE`'s
    `files` row and lines. The `git` wrapper makes the cutover's push fail,
    and the read-back proves the entry's commit, the one carrying
    `Register-Seq: b+1`, absent from origin. The abort, `switch_to_files` at
    `b+1`:
    - deletes the entry at `b+1` with its `journal_edits` and
      `journal_files`, puts the head back to `b`, and returns `b`;
    - puts `lanes/STORE`'s `files` row and lines back with their old
      `line_id`s, and the switch row back whole: `primary`, `present`, with
      the old `marker_seq`, `marker_commit`, `generation`,
      `database_label`, `set_seq`, `set_utc`, `set_by_lane` and
      `set_by_ws`, and `restore_prior` NULL. It never sets `files`;
    - so a dump of every table of §1.2 except `schema_version` and
      `migrations` (twelve of the fourteen; lane 1's comment 6708990159), each
      ordered by its key, taken by the admin connection at head `b` before the
      cutover equals the same dump after the abort, byte for byte. The dump
      leaves out identity sequences: lane 1's v2.3 (the renames, step 9)
      warns that an identity value taken by a refused attempt is not reused,
      so a gap can appear in `doc_lines.line_id`.

    The estate stays stopped, its lanes paused and the outage declared,
    until the cutover runs again.
- **Tests.** In `code/tests/test_sql_register_disaster.py`:
  - `test_the_disaster_path_reimports_at_the_mirrors_tip_and_continues_the_sequence`:
    the after-loss cutover at `m+1`, the next write at `m+2`;
  - `test_a_backup_newer_than_the_mirror_is_restored_and_rendered`: the
    restore-form cutover at `b+1`, the next write at `b+2`;
  - `test_an_abort_after_a_restore_form_cutover_restores_every_table_byte_for_byte`;
  - `test_the_disaster_notice_names_the_lost_seqs`.
- **Run time.** 10–20 s.
- **Flakiness.** The lag is made by failing the renderer's fetch, and read
  after the renderer's exit, never by timing.

### Lane 1's schema objects

Lane 3's ruling 5 (#165 comment 6578020193) asks for a real-engine test of
each object lane 1's part adds. These entries are written against
`design.md`'s "The schema", §1.2 to §1.5. Each runs on the session cluster, in
a database cloned from the template (section 3.1), as the role named, and
reads back through the admin connection. Their phases are in section 1.3's
table.

#### AT-SET. `register_switch.set_seq`, and the marker's states

- **Proves.** `set_seq` is the seq of the switch's last change (§1.2; §1.3,
  "The switch's states"): an operator entry's own seq, the cutover's 1, `m+1`
  after a re-import or `b+1` after a restore, or the rollback's `k+1`; and
  after the first or re-import form's abort, the abort's base, and after
  the restore form's, the value it held before that cutover (lane 1's
  v2.3). That is lane 3's expectation in comment 6578688322, which §1.3
  states. With it, the marker's four states move only
  as §1.3's kinds table says, and a rollback's entry makes every older writer
  snapshot stale.
- **Fixture.** A bootstrapped database; the import, the cutover and three
  writes, through the real `lanes-index`; `record_marker` as each read-back
  would call it; then a rollback's first transaction (Q4(b)(iii)),
  `commit_write` with a `rollback` entry, as REGISTER-WRITER. A second
  database for the abort, and a third for the after-loss form.
- **Oracle.**
  - After the bootstrap: `mode = 'files'`, the marker `absent`,
    `set_seq = -1`, and the head -1.
  - After the import (seq 0) and the cutover: the head is 1,
    `set_seq = 1`, `mode = 'primary'`, the marker `adding` at
    `marker_seq = 1`, and `journal.operator` is true for seq 1 only. A write
    then fails with LR002, so 69. After `record_marker` at seq 1, the marker
    reads `present`, `marker_commit` holds the confirmed commit, and a write
    commits.
  - After `import --after-loss` at `m` and `cutover --after-loss`:
    `set_seq = m+1`, and the generation differs from the bootstrap's.
  - After the rollback's first transaction, read in one snapshot:
    `mode = 'files'`, the marker `removing`, the head `k+1`,
    `set_seq = k+1`, and the entry at `k+1` is the operator entry that
    removes `lanes/STORE`. After `record_marker` at `k+1`, the marker reads
    `absent`.
  - The abort: with the cutover at seq 1 not yet confirmed,
    `switch_to_files(version, 1, lane, ws)` deletes seq 1 and its rows, sets
    the head to 0 and the switch to `files` with the marker `absent`, and
    `set_seq` to 0, the abort's base. With the marker `present`, it fails
    with LR002 and changes nothing.
  - **The re-import form's abort** (the third database): `switch_to_files`
    at `m+1` sets `files` with the marker `absent`, `set_seq` `m` and
    `lanes/STORE` gone, and leaves the `database_label` and `generation`
    its cutover set, as lane 3 ruled lane 1's point 2 (comment
    6710770304). So a commit call carrying another generation, as a
    pending record made against the lost database would, still fails with
    LR007.
  - **The restore form** (a fourth database, restored at `b` as AT-D22
    makes it): after its cutover, `restore_prior` holds the switch row and
    `lanes/STORE`'s `files` row and lines as they were; `record_marker`
    clears it. Its abort puts them back with their old `line_id`s, the
    switch row whole, and `set_seq` the value before the cutover (AT-D22's
    dump). As the admin under `SET ROLE lanes_register_owner`, setting
    `restore_prior` while the marker is not `adding` fails with 23514
    (`register_switch_check4`).
  - **An operator entry with a row outside `lanes/STORE`**, as lane 3 ruled
    lane 1's point 1 (comment 6710770304): a cutover entry, and a rollback
    entry, to which the shim adds an extra `files` row, are each refused at
    their commit with LR004. The journal's count and the head are
    unchanged, so no such entry stays for a later abort to meet with 23503.
  - **An older writer snapshot**, in two cases (§1.3, "The signals a writer
    gets"; the LR006 probe's case 2 shows the same lock):
    - **held:** the rollback's first transaction holds the head row
      uncommitted, the writer's commit call starts, the test polls until its
      backend shows `wait_event_type = 'Lock'`, as AT-D19 stages it, and the
      rollback's transaction commits. The commit gets 40001, the writer
      re-reads, and exits 69 on the switch its snapshot reads, or on LR002;
    - **in turn:** the writer's snapshot is read before the rollback's
      transaction, and its commit call starts after it commits. The commit
      gets LR001, since the head moved to `k+1`; the new attempt's snapshot
      reads the switch at `files`, and the writer exits 69.
  - **No kind commits in a state its kinds-table row refuses.** For each
    kind, `import`, `cutover`, `write`, `stray-import`, `stray-revert` and
    `rollback`, and each state of the switch, `files`/`absent`,
    `primary`/`adding`, `primary`/`present` and `files`/`removing`, a
    commit call commits only where §1.3's kinds table allows it, and
    otherwise raises LR002 with the journal's count and the head unchanged.
    So besides the import's baseline, nothing is journaled while the switch
    is not `primary` except the two operator entries (D24).
  - As the admin under `SET ROLE lanes_register_owner`, an `UPDATE` setting
    `set_seq` to -2 fails with 23514, and so does one setting `primary` with
    the marker `absent` (the switch's checks).
- **Tests.** In `code/tests/test_sql_register_schema.py`:
  - `test_set_seq_is_minus_1_after_the_bootstrap`;
  - `test_set_seq_after_a_cutover_is_the_cutovers_own_seq[first|after-loss]`;
  - `test_the_marker_moves_only_through_commit_write_and_record_marker`;
  - `test_a_write_is_refused_lr002_while_the_marker_is_adding_or_removing`;
  - `test_the_rollbacks_entry_sets_files_removing_and_its_own_seq`;
  - `test_the_rollbacks_entry_makes_an_older_writer_snapshot_fail_lr001_or_40001_then_69[held|in-turn]`;
  - `test_no_kind_commits_in_a_state_its_kinds_table_row_refuses[<kind>-<state>]`;
  - `test_switch_to_files_aborts_only_an_unconfirmed_cutover`;
  - `test_the_reimport_forms_abort_leaves_label_and_generation`;
  - `test_restore_prior_is_set_by_the_restore_form_and_cleared_by_record_marker`;
  - `test_an_operator_entry_with_a_row_outside_lanes_store_is_refused_lr004_at_commit[cutover|rollback]`;
  - `test_the_switch_checks_refuse_a_bad_set_seq_and_a_mode_marker_mismatch`.

#### AT-MIR. `mirror_state` and `record_mirror_state` (D16)

- **Proves.** The one-row divergence record holds only well-formed reports,
  the renderer writes it through `record_mirror_state` and nothing else, only
  the two ways out clear a divergence, and the row never gates alone (D16).
- **Fixture.** A bootstrapped database. Direct calls as REGISTER-WRITER and
  READER. For the last case, G phase: the primary estate of test 6, with
  test 5's `git` wrapper.
- **Oracle.**
  - After the bootstrap, exactly one row, every column but `id` NULL.
  - `record_mirror_state(ws, <40-hex sha>, NULL)` sets `diverged_sha`,
    `found_utc` and `found_by_ws`. A second call with another sha leaves the
    first divergence as it was: the function keeps the first one found and
    never clears one (§1.3). `record_mirror_state(ws, NULL, '<error>')`
    sets `last_error` and `last_error_utc`, and leaves the divergence as it
    was: only the single-write import and the revert clear it (D16).
  - A sha that is not 40 hex digits fails with 23514, and the row is
    unchanged.
  - READER's call fails with 42501. REGISTER-WRITER's direct `UPDATE` or
    `DELETE` of `mirror_state`, inside `BEGIN READ WRITE`, fails with 42501
    (§1.4: SELECT on the tables, EXECUTE on the functions).
  - A `stray-import` entry, and in a second case a `stray-revert` entry,
    through `commit_write`, leaves `diverged_sha` and `found_utc` NULL.
  - **Never gates alone (G):** with the row set and git holding no stray
    commit, the next render renders and `status` shows a lag of 0. With a
    stray commit on origin and the row empty, the renderer still stops
    (test 6's oracle).
- **Options.** None beyond test 6's.
- **Tests.** In `code/tests/test_sql_register_schema.py`:
  - `test_mirror_state_starts_as_one_empty_row`;
  - `test_record_mirror_state_records_a_divergence_and_a_failed_pass`;
  - `test_a_failed_pass_record_leaves_the_divergence_as_it_was`;
  - `test_a_second_divergence_report_keeps_the_first`;
  - `test_mirror_state_refuses_a_malformed_sha`;
  - `test_only_register_writer_executes_record_mirror_state_and_none_updates_the_table`;
  - `test_a_stray_import_or_revert_clears_the_divergence[import|revert]`;

  in `code/tests/test_sql_register_divergence.py`:
  - `test_the_mirror_state_row_never_gates_alone[row-only|git-only]`.

#### AT-SPL. `object_holds.spellings` (D19)

- **Proves.** A live claim records its spelling as typed, its canonical key,
  and the aliases its writer's `repos.tsv` maps to that key, and a released
  claim's spellings block nothing.
- **Fixture.** The imported register, with aliases X and Y mapping to O in
  `lanes/repos.tsv`. Claims through the real writer.
- **Oracle.** `spellings` is a `text[]` whose order §1.2 does not fix, so it
  is compared as a set.
  - L1's claim typed as X: its `object_holds` row has `object = O`, and
    `spellings` holds the set {X, O, Y}.
  - L1's claim typed as O: the same set.
  - After L1 releases O, L2's claim of X commits, and its row holds the same
    set.
- **Tests.** In `code/tests/test_sql_register_schema.py`:
  - `test_a_claim_records_its_spelling_its_key_and_its_aliases[as-alias|as-key]`;
  - `test_a_released_claims_spellings_block_nothing`.

#### AT-LR5. LR005, the register's two rules in the transaction

- **Proves.** `commit_write`'s structured rows (§1.3.1) raise LR005 for a
  claim whose spellings meet another lane's live claim (D19), for a claim
  that trips `one_live_claim`, whose 23505 is raised again as LR005, and for
  a `HOLD` or `LANDING` line over another lane's open taker in `main_slots`
  (D18). D18 compares a canonical repository key, held as D19 holds an
  object's spellings (lane 3, comment 6579459518), so one repository spelled
  two ways is one taker. The writer maps LR005 to exit 2 with the holder or
  the earlier line. And because the structured rows run after the head
  moved, LR005 proves that the whole transaction rolls back.
- **Fixture.** The runs of test D19's [behind-the-alias] case and test 4's
  case (b), with the shim's log, which records each call's stderr under
  `VERBOSITY=sqlstate`. A third case: L1 claims O and releases it, then L2's
  claim of X commits. A fourth case, as lane 1's v2.3 ran it, in four forms:
  `lanes/repos.tsv` maps `r9` to `opensoft/r9`, and one lane takes one
  spelling before another lane takes the other. The forms are `HOLD`
  key-first (L2 takes `opensoft/r9`, then L4 takes `r9`), `LANDING`
  key-first, `HOLD` alias-first (L2 takes `r9`, then L4 takes
  `opensoft/r9`) and `LANDING` alias-first, the test's
  `[hold|landing]-[key-first|alias-first]`.
- **Oracle.**
  - The loser's last commit call ended with LR005: the claim in test D19,
    the later `HOLD` in test 4 (b), where D18's classifier runs inside the
    commit, and the second take of `r9` or `opensoft/r9` in either order,
    `HOLD` or `LANDING`, since the line's `register_lines.repo_spellings`
    holds the spelling as typed, its canonical key and the writer's
    aliases, and step 7's check and `main_slots` compare the key or any
    overlapping spelling (D18; lane 1's v2.3). A line whose
    `repo_spellings` is NULL is compared by `repo_key` alone.
  - Read after LR005: `register_head.seq`, the journal, `journal_edits`,
    `doc_lines` and `object_holds` equal their values before the call. The
    loser's write id is absent.
  - The loser exits 2, never 69, and its stderr carries the holder or the
    earlier line.
  - The released case raises nothing, and L2's claim commits.
- **Tests.** In `code/tests/test_sql_register_order.py`:
  - `test_lr005_refuses_a_claim_whose_spellings_meet_another_lanes_live_claim`;
  - `test_lr005_refuses_a_hold_over_another_lanes_open_taker`;
  - `test_two_spellings_of_one_repository_hold_one_main[hold|landing]-[key-first|alias-first]`;
  - `test_lr005_rolls_back_the_head_and_every_row`;
  - `test_lr005_does_not_fire_on_a_released_claims_spellings`.
- **Run time.** The four entries take 10–20 s in all, mostly test D19's and
  test 4's runs, which they share.
- **Flakiness.** None beyond test D19's and test 4's: every assertion reads
  rows and codes after the writers exit.

#### AT-VER. The version window and forward-only migrations

- **Proves.** D1's "an exact match or 69, and forward-only migrations", as
  §1.5 gives them: `schema_version`'s `version`, `writers_from` and
  `readers_from`, the `migrations` table, and LR003.
- **Phase and kind.** N. RE.
- **Fixture.** A bootstrapped database. Test copies of `lanes-index` whose
  `REGISTER_SCHEMA_VERSION` is one below `writers_from` and one above
  `version`, made as test 14's copies are (open point 3). A second
  migration script, shipped only in the test's copy, which touches the head
  row and raises `version`.
- **Oracle.**
  - A writer below `writers_from`, or above `version`, gets LR003 on its
    commit and exits 69, naming both numbers and the remedy, with nothing
    written. A reader outside `readers_from` to `version` exits 69 with
    nothing on stdout; the guard exits 2, and `managed-projection` 1.
  - **A migration and an older writer snapshot,** in two cases. Held: the
    migration's transaction holds the head row (`UPDATE register_head SET
    seq = seq`) uncommitted, the writer's commit call is seen waiting on it,
    and the migration commits: 40001, a re-read, then LR003, so 69. In
    turn: the migration commits before the commit call starts; the head's
    seq is unchanged, so there is no LR001, and step 2 raises LR003, so 69.
  - `bootstrap` run again at the same version prints `unchanged` and adds no
    `migrations` row. A migration whose number is not one above `version`
    fails with LR003 and changes nothing. Each `migrations` row carries its
    script's sha256, `by_lane` and `by_ws`.
  - `ready` fails a client outside the window, naming both numbers: its
    check 9 (§2.4, folded at `21f0191c` from lane 3's ruling 6578020193).
- **Tests.** In `code/tests/test_sql_register_schema.py`:
  - `test_a_client_outside_the_version_window_is_refused_lr003[writer-old|writer-new|reader-old|reader-new]`;
  - `test_a_migration_against_an_older_snapshot_ends_69[held-40001|in-turn-lr003]`;
  - `test_a_second_bootstrap_at_the_same_version_adds_no_migration`;
  - `test_a_migration_out_of_order_is_refused_lr003`;
  - `test_ready_fails_a_client_outside_the_version_window`.

#### AT-LR4. LR004: the edits, the proof and the operator flag

- **Proves.** `commit_write`'s edits and proof (§1.3.1). An entry whose edits
  do not apply, or whose proof differs, fails with LR004; the writer reloads
  every line once and retries, then exits 69; the head, the journal and the
  lines are unchanged. The proof compares hex with hex,
  `encode(sha256(render_file(path)), 'hex')` against the entry's digest. The
  operator flag's rules raise LR004 too: an operator entry that edits
  anything but `lanes/STORE`, or nothing, and any other entry that edits
  `lanes/STORE` (F2, D24).
- **Phase and kind.** W. RE, with the shim's `edit-entry` mode, which
  rewrites one field of the commit call's entry.
- **Fixture.** The imported register, and `log NOTED` as the write. Per case,
  the shim rewrites: one `journal_files` digest to another 64-hex value; an
  edit's `old_sha256`, so the edit does not apply; an edited path left out of
  the entry's `files`; a `write` entry that edits `lanes/STORE`; an operator
  entry with no edit, written as a `rollback` entry, since a `cutover` meets
  LR002 first on a `primary`/`present` database; an entry with no `journal`
  object; one with a value in `journal` that is neither a string nor NULL;
  one whose list key is not an array; a structured row whose `raw` is not
  its line's text; a `root_seq` on a journal that is not empty; and a
  `write` entry with no `args_sha256`, which fails
  its CHECK with 23514. Lane 1's v2.3 adds three: an entry with no
  `not_after`, and one whose `not_after` is not a string, each LR004; and
  one whose `not_after` is not a time, 22007, so 69 at once. Every entry a
  test builds carries `not_after`, on every kind, the operator entries
  included, as lane 1's v2.3 has it and as lane 3 kept it, lane 4's
  argument (lane 1's point 4; comments 6710770304 and 6709038351). A file
  with no final newline is among the fixture's files.
- **Oracle.**
  - Each rewritten entry ends with LR004 in the shim's log (23514 for the
    missing digest, so 69 at once). `register_head.seq`, `journal`,
    `journal_edits`, `journal_files`, `files` and `doc_lines` equal their
    values before.
  - Armed for the first commit call only: the writer reloads every line,
    its second commit commits, and it exits 0 with one journal entry.
  - Armed for every call: two LR004s, then exit 69 naming the store, and
    nothing written.
  - The file with no final newline renders back without one
    (`journal_files.final_newline`).
- **Tests.** In `code/tests/test_sql_register_schema.py`:
  - `test_lr004_on_an_edit_or_proof_that_does_not_apply_leaves_everything_unchanged[<case>]`,
    where `<case>` is one of the rewrites above, named:
    - `digest-not-hex-equal` (one `journal_files` digest changed to another
      64-hex value);
    - `edit-old-sha256` (an edit's `old_sha256`, so the edit does not
      apply);
    - `path-not-in-files` (an edited path left out of the entry's `files`);
    - `write-edits-store` (a `write` entry that edits `lanes/STORE`);
    - `operator-no-edit` (an operator entry with no edit, as a `rollback`
      entry);
    - `no-journal-object` (an entry with no `journal` object);
    - `journal-value-not-string-or-null` (a value in `journal` that is
      neither a string nor NULL);
    - `list-key-not-array` (a list key that is not an array);
    - `raw-not-the-lines-text` (a structured row whose `raw` is not its
      line's text);
    - `root-seq-on-a-non-empty-journal` (a `root_seq` on a journal that is
      not empty);
    - `write-without-args-sha256`, which fails its CHECK with 23514 and so
      exits 69 at once, not LR004;
  - `test_lr004_reloads_once_then_exits_69`;
  - `test_an_entry_without_a_valid_not_after_is_refused[missing|not-a-string|not-a-time]`,
    on a `write` entry and on an operator entry;
  - `test_the_operator_flag_rules_refuse_lr004[write-edits-store|operator-edits-other|operator-edits-nothing]`;
  - `test_a_file_without_a_final_newline_renders_back_byte_identical`.
- **Run time.** AT-VER and AT-LR4 take 10–20 s together.
- **Flakiness.** None: each case is one write with a fixed rewrite, or a
  fixed client copy.

#### AT-LR006. Two attempts of one write meeting at the commit (S1's step 1)

- **Proves.** S1's commit, step 1 (§1.3.1), in the rewording lane 3 took
  from lane 2's LR006 probe (comment 6710770304): `commit_write` takes the
  head row's lock and looks the `write_id` up before anything else, so an
  earlier attempt of the same write that committed after this attempt's
  snapshot read and before this commit's transaction began is LR006,
  before LR001. With SERIALIZABLE's 40001 at that lock, no interleaving of
  two attempts reaches the journal's unique `write_id`, so F33's 23505
  backstop is never reached (AT-F33). The LR006 probe ran each case below
  twice, with the same codes.
- **Phase and kind.** W. RE, on the session cluster, with the SQL as
  `design.md` prints it; the client's attempt loop and exit mapping are the
  design's outcomes table, read against the SQLSTATE.
- **Fixture,** shared by AT-LR006-1 to -6 and AT-F33:
  - a database cloned from the template, brought to `primary` with the marker
    `present` by the documented functions as `lanes_register_writer`:
    `commit_write` with an `import` entry, then with a `cutover` entry, then
    `record_marker`;
  - two attempts, A and B, of one write: the same `write_id` W, lane, verb
    and `args_sha256`, and each entry with a `not_after` well past the case
    (lane 1's v2.3). Each is its own `psql` process as the writer role,
    keeping the role's timeouts (`lock_timeout=2000ms`,
    `statement_timeout=5s`, `idle_in_transaction_session_timeout=5s`), and
    each commit call is `BEGIN ISOLATION LEVEL SERIALIZABLE; SELECT
    lanes_register.commit_write(base, W, …); COMMIT;` under
    `ON_ERROR_STOP` and `VERBOSITY=sqlstate`, which is Q7 (A″)'s commit;
  - B's snapshot read stands where `lanes-index register snapshot` will:
    one `REPEATABLE READ READ ONLY` transaction as the writer, returning the
    head, the switch, the database's clock, W's seq if any, and the digest
    the entry's proof needs;
  - A's transaction is held open through a FIFO. Every barrier is a state
    polled in `pg_stat_activity` by `application_name` from the admin
    session, bounded at 0.1 s steps under a 1.5 s ceiling: A
    `idle in transaction`, or B with `wait_event_type = 'Lock'` and
    `pg_blocking_pids` naming A. No sleep stands in for a barrier;
  - the no-lookup control: a copy of `commit_write` with step 1's lookup
    deleted and nothing else changed, created in the test's own database
    after the clone, never in the template. It is created as the schema's
    owner (`SET ROLE lanes_register_owner`), `SECURITY DEFINER` with the
    original's `search_path` setting, and `GRANT EXECUTE` on it to
    `lanes_register_writer`, so the writer role calls it as it calls
    `commit_write` (the probe's `commit_write_nolookup`).
- **The cases and their oracles.** n is the head before W.
  - **AT-LR006-1, an earlier attempt that commits between this attempt's
    snapshot read and its commit gives LR006.** A runs `commit_write(n, W)`
    and stays idle in transaction. B's snapshot read returns at once, with
    head `n` and W absent. A commits. Then B's commit call. Variant: A runs
    and commits whole after B's snapshot read.
    - B's `psql` exits 3, and its stderr line is exactly `ERROR:  LR006`;
      `register commit` exits 0 with the seq, and the write exits 0.
    - The journal has one row for W, at `n+1`, and the head is `n+1`.
    - The control gives LR001 here, so the test detects the lookup's
      absence.
  - **AT-LR006-2, LR006 comes before LR001.** W is committed at `n+1`. One
    commit call with the stale base `n`, and one with the fresh base `n+1`.
    - Both give LR006, never LR001, and nothing is written.
    - The control gives LR001 for the stale base and 23505 on
      `journal_write_id_key` for the fresh one.
  - **AT-LR006-3, an earlier attempt that commits while this commit waits
    for the head row gives 40001.** A holds. B's snapshot read. B's commit
    call is seen waiting on a lock A blocks. A commits.
    - B gets 40001 at the head row's `FOR UPDATE`, never LR006 or 23505, and
      `register commit` exits 75 for this attempt.
    - The next attempt's snapshot finds W at `n+1`, and the write exits 0
      with no further commit call. One row for W.
    - The same at REPEATABLE READ. At READ COMMITTED, not the design's
      level, the probe found LR006 after the wait: recorded, not required.
  - **AT-LR006-4, an earlier attempt that commits after this commit's
    transaction took its snapshot, with no wait, gives 40001.** A holds. B
    begins SERIALIZABLE and takes its snapshot with a first statement,
    `SELECT pg_current_snapshot()`, which excludes A's transaction. A
    commits. Then B calls `commit_write`.
    - 40001 at the `FOR UPDATE`, with no lock wait seen in
      `pg_stat_activity`. The next snapshot finds W, and the write exits 0.
      One row for W.
    - The case stands for the short window between the commit statement's
      snapshot and its lock.
  - **AT-LR006-5, an earlier attempt that holds the head row past
    `lock_timeout` gives 55P03.** A holds. B's commit call is seen waiting.
    A neither commits nor rolls back.
    - B gets 55P03 on `register_head` at about 2 s (the probe: 2.02 and
      2.03 s), and `register commit` exits 75 for this attempt.
    - After A commits, the next snapshot finds W, and the write exits 0. One
      row for W.
    - If the deadline passes while A still holds, AT-F3's one lookup
      applies, never 75.
  - **AT-LR006-6, an earlier attempt that never commits leaves the write to
    the retry.** (a) A holds, B is seen waiting, and A rolls back. (b) A
    holds and goes idle until the role's 5 s
    `idle_in_transaction_session_timeout` ends its session; then A sends its
    `COMMIT`.
    - (a) B commits W at `n+1`.
    - (b) A's `psql` prints `FATAL:  25P03` and exits 2, so it is in doubt;
      B's snapshot finds W absent, and B commits W at `n+1`.
    - In both, one row for W, and no 23505.
- **Tests.** In `code/tests/test_sql_register_in_doubt.py`:
  - `test_an_attempt_committed_after_the_snapshot_read_gives_lr006[held|whole]`;
  - `test_lr006_comes_before_lr001[stale-base|fresh-base]`;
  - `test_an_attempt_committed_while_the_commit_waits_gives_40001_then_exit_0[serializable|repeatable-read]`;
  - `test_an_attempt_committed_after_the_commits_snapshot_gives_40001_without_a_wait`;
  - `test_an_attempt_holding_past_lock_timeout_gives_55p03_then_exit_0`;
  - `test_an_attempt_that_never_commits_leaves_the_write_to_the_retry[rollback|idle-bound]`;
  - `test_the_no_lookup_control_gives_lr001_where_the_lookup_gives_lr006`.
- **Run time.** About 10 s: AT-LR006-5's 2 s lock wait and AT-LR006-6 (b)'s
  5 s idle bound dominate.
- **Flakiness.** Every interleaving is held by a FIFO and confirmed in
  `pg_stat_activity`, and every assertion is a SQLSTATE or a row count, read
  after the sessions end. AT-LR006-4's snapshot is taken by a statement, not
  by timing.
- **The design's sentence, and the probe's verdict.** S1's commit, step 1,
  says LR006 is reached where an earlier attempt in doubt committed after
  this attempt's snapshot, so it replaces F33's backstop. On PostgreSQL
  16.15, as the writer role at SERIALIZABLE, that holds for AT-LR006-1's
  case only: an earlier attempt that commits during this commit's
  transaction gives 40001, or 55P03 once `lock_timeout` passes, and the
  next attempt's snapshot finds the id. The conclusion holds and the reason
  is wider: the lookup under the lock and SERIALIZABLE's 40001 at that lock
  together keep every attempt off the unique `write_id`. Lane 1's v2.3
  rewrites §1.3's signals table to match (comment 6708990159): LR006 only
  for an attempt committed between the snapshot read and the commit
  transaction, and 40001 or 55P03 for one committed while that transaction
  is open, after which a re-read that finds the id exits 0. Lane 3 has
  taken lane 2's rewording into S1's step 1 (comment 6710770304), and it
  matches those rows, so the sentence quoted above is `21f0191c`'s. The
  tests above assert the new wording.

#### AT-F33. No interleaving of two attempts of one write reaches 23505

- **Ruling.** F33: no test expects the 23505 on `write_id` to fire. This
  test asserts that it never does, and it counts only if the no-lookup
  control fails it.
- **Fixture.** AT-LR006's, with case 0 added: A commits before B's snapshot
  read, so B's snapshot finds W and the write exits 0, and a commit call
  made anyway gets LR006. The whole matrix, case 0 and AT-LR006-1 to -6,
  runs twice.
- **Oracle.**
  - Every B commit call ends in committed, LR006, 40001 or 55P03, and A's
    own calls in committed, its rollback, or 25P03 for a dead session.
  - SQLSTATE 23505 occurs in no session's output.
  - After each case, `SELECT count(*) FROM journal WHERE write_id = W` is 1.
  - At the end, `count(*) = count(DISTINCT write_id)` over the journal,
    with one root.
  - **The control must fail it.** Run against the no-lookup control, the
    same matrix shows 23505 on `journal_write_id_key` in the fresh-base
    calls: 5 a run here (case 0, and each retry after a 40001 or 55P03:
    AT-LR006-3 at both levels, -4 and -5), against 6 in the probe, whose
    sixth was READ COMMITTED's retry, a case this matrix does not run. If the
    control shows none, at least 1 being the bar, the test fails, since its
    oracle could not have caught the branch.
- **Tests.** In `code/tests/test_sql_register_in_doubt.py`:
  - `test_no_interleaving_of_two_attempts_of_one_write_reaches_23505`;
  - `test_the_no_lookup_control_reaches_23505_so_the_oracle_can_fail`.
- **Run time.** About 25 s for the two passes and the control's.

### Tests for the frame review's findings

Written against lane 3's rulings in #165 comment 6578269040, on lane 3's review
of the frame (F-numbers are that review's), as folded at `6a5fcdc6` and
`1c45123a`, and at `21f0191c`, and as §1.3 and §2.1 to §2.5 carry them. Each
entry is short: the
ruling, its fixture, its oracle, and its test names.

#### AT-F1. A diverged rollback, and the removal entry in step 1

- **Ruling.** The rollback refuses to start while the mirror is diverged. The
  entry that removes `lanes/STORE` is written in the switch's transaction,
  the rollback's first, with the switch to `files` (§2.3). Besides the
  import's baseline, nothing is journaled while the switch is not `primary`
  except the two operator entries (D24; lane 3, comment 6579459518).
- **Fixture.** Test 6's diverged estate (a stray commit on origin); then the
  same estate after the single-write import. Test 5's `git` wrapper holds the
  rollback at its push on a FIFO. The operator lane runs it, with
  `LANES_LANE=` its name (F12).
- **Oracle.**
  - Diverged: `rollback` exits 2 before step 1, naming the divergence and
    S2's two tools. The switch reads `primary` with the marker `present`, the
    journal and head are unchanged, and `lanes/STORE` is still on origin.
  - After the import: the rollback runs. While it is held at its push, one
    snapshot through the admin connection shows the switch at `files` with
    the marker `removing`, and the removal entry, an operator entry, as the
    journal's last seq `k+1`: both or neither. A write tried meanwhile fails
    with LR002 and journals nothing. On release, the read-back confirms the
    push, `record_marker` sets the marker `absent`, and the rollback ends
    with test 8 (a)'s oracle.
  - A rerun after a kill at the push finds the marker `removing` and its own
    entry as the last seq, and goes on from the render (§2.3, step 2).
  - A re-import after the rollback continues the journal: its baseline takes
    `k+2`, with base `k+1`, and a later cutover takes `k+3`, so no
    `Register-Seq` repeats on `main` (§1.3's kinds table).
  - Over the life of one database, a cutover and then a rollback leave
    exactly two journal rows with `operator` true: the cutover's and the
    rollback's.
- **Tests.** In `code/tests/test_sql_register_rollback.py`:
  - `test_rollback_refuses_to_start_while_the_mirror_is_diverged`;
  - `test_the_marker_removal_entry_is_written_with_the_switch_in_step_1`;
  - `test_a_rollback_rerun_resumes_after_its_own_entry`;
  - `test_a_reimport_after_a_rollback_continues_the_journal`;
  - `test_a_cutover_and_a_rollback_leave_exactly_two_operator_entries`.

#### AT-F2. Operator entries, and a failed seq-1 push

- **Ruling.** An entry that touches only `lanes/STORE` is an operator entry,
  flagged in the journal (`journal.operator`, §1.2), and the renderer skips
  it. There are exactly two: the cutover's, and the rollback's last seq. Its
  own command pushes it with the renderer's identity (D23), confirms it by
  Amendment 23's S4 read-back, and records it with `record_marker`. A push it
  cannot confirm exits 3, and the rerun reads origin back first (§2.1,
  accepted by lane 3 in comment 6579459518). The cutover's abort,
  `switch_to_files`, deletes seq 1, and only after a read-back proves the
  entry's commit, the one carrying `Register-Seq: 1`, absent from origin
  (lane 1's v2.3).
- **Fixture.** Test 7's cutover on a bare origin, with test 5's `git`
  wrapper, run by the operator lane with `LANES_LANE=` its name (F12). Four
  cases: a clean cutover; the cutover's push failed (exit 1, nothing
  pushed); the push made, then reported failed (the wrapper pushes and exits
  1); and the push made with the read-back's fetch failing, so the push can
  be neither confirmed nor disproved.
- **Oracle.**
  - Clean: seq 1 carries the operator flag, and no write entry does. The
    push's environment carries the deploy key and the renderer's identity.
    The marker reads `present` after the read-back. A render forked after it
    pushes no commit for seq 1, and `status` shows no lag.
  - Push failed: no renderer renders seq 1, and a render ends rather than
    retry it: its target is `t ≤ m`, so it pushes nothing and exits after one
    pass (lane 4's section, "F2, the stop at an operator seq"). The abort's
    read-back finds the entry's commit, the one carrying `Register-Seq: 1`,
    absent from origin; seq 1 is deleted, the head reads the import's seq,
    0, and the switch reads `files` with the marker `absent`.
  - A rollback's entry at `k+1`, with seqs up to `k` not yet on origin: a
    render pushes them, stops before `k+1`, and ends.
  - Pushed, reported failed: the read-back finds the marker, so the cutover
    completes: `record_marker` sets it `present`, and seq 1 stays.
  - Unconfirmed: `cutover` exits 3 and changes nothing more; the switch
    stays `primary` with the marker `adding`, and every write fails with
    LR002. Its rerun reads origin back first, and completes or aborts as the
    two cases above. The same for the rollback's removal push: `rollback`
    exits 3, the marker stays `removing`, and its rerun reads origin back,
    then records the marker `absent` or pushes the removal again (§2.3,
    step 4).
  - Held across git: with the push held on the wrapper's FIFO for 6 s, past
    the writer role's 5 s idle bound, the cutover still completes, since its
    transaction committed before any git step (F23, §2.1).
- **Tests.** In `code/tests/test_sql_register_cutover.py`:
  - `test_a_marker_only_entry_is_an_operator_entry_the_renderer_skips`;
  - `test_the_operator_entry_is_pushed_with_the_renderer_identity_and_read_back`;
  - `test_a_cutover_abort_deletes_seq_1_only_when_the_marker_is_proved_absent[not-pushed|pushed]`;
  - `test_an_unconfirmed_operator_push_exits_3_and_its_rerun_reads_origin_first[cutover|rollback]`;
  - `test_no_operator_command_holds_a_transaction_across_git`;
  - `test_a_render_stops_before_an_operator_seq_not_yet_on_origin[cutover|rollback]`.

#### AT-F3. A write in doubt at the deadline

- **Ruling.** A write in doubt never exits 75. At the deadline it makes one
  lookup, `find_write` in a transaction it rolls back: found is 0; absent,
  with the head row's lock granted, is 75; otherwise, 55P03 or no answer, it
  exits 3 and keeps its record (§1.3's signals; S1, "A write in doubt").
  Lane 4's section ("The in-doubt rule at the deadline") adds `not_after`:
  each commit call carries the snapshot's database clock plus its call timer
  plus 2 s, `commit_write` raises unless `clock_timestamp() < not_after`
  just before it returns, and the deadline's lookup waits until the
  database clock passes the largest `not_after` of the write's calls in
  doubt. Lane 1's v2.3 puts the check in `commit_write` (comment
  6708990159): `not_after` is a required key of every entry, and step 9,
  the last statement before `RETURN`, raises LR008 unless
  `clock_timestamp() < not_after`, so the whole transaction rolls back.
  Lane 3 ruled LR008 as lane 4 reads it, "not committed" (comment
  6710770304, of 6709038351), for "The in-doubt rule at the deadline (F3,
  D8)" and the outcomes table's LR row: inside D7's 30 s deadline the
  attempt loop treats it as a lost turn, as it routes 40001, with a fresh
  snapshot and a new attempt under the same write id; at the deadline F3's
  rule above applies; and LR008 by itself is never 3 or 69.
- **Fixture.** Test 12's half-open case: the shim's `before` mode makes the
  first commit call in doubt before it lands, and the old connection is the
  test's session as
  `lanes_register_writer`, which replayed that call's script without its
  `COMMIT` (test 12), so it holds the head row with the write's journal row
  inserted and not committed. `LANES_REGISTER_DEADLINE_S=1`, under the
  2000 ms `lock_timeout`, so exactly one retry starts before the deadline and
  ends with 55P03. The test's session first runs
  `SET idle_in_transaction_session_timeout = 0` (a user-settable parameter),
  before it opens its transaction. The deadline's lookup waits until the
  database clock passes the largest `not_after`, which is the snapshot's clock
  plus the commit call's 8 s timer plus 2 s, so the lookup comes about 10 s
  after the snapshot. The writer role's own
  `idle_in_transaction_session_timeout` is 5 s, and would end the held session
  about 5 s before the lookup reaches the shim: [found] would then commit into
  a dead session (25P03), [held] would meet a free head row and exit 75, not
  3, and [absent] would pass by accident. The exit comes about 10 s after the
  snapshot in every case. The shim holds the next call, the deadline's lookup,
  on a FIFO. While it is held, the test's session:
  - **[found]** commits;
  - **[absent]** rolls back;
  - **[held]** stays open until the writer exits;
  - **[no-answer]:** the shim fails the lookup's call with exit 2.
- **Oracle.**
  - [found]: exit 0, one journal row for the id, the record removed.
  - [absent]: exit 75 with the busy text, no row for the id, the record
    removed.
  - [held]: `find_write` meets 55P03, so exit 3 naming the id, never 75, and
    the record kept. After the test rolls back, a plain rerun reuses the id
    and writes it once.
  - [no-answer]: exit 3 naming the id, never 75, and the record kept.
  - In every case the shim's log shows the lookup's call starting only after
    the database clock passed the in-doubt call's `not_after`, and the exit
    comes about 10 s after the snapshot (the 8 s timer plus 2 s), so the
    test's session must outlive that time, which the `SET` above allows.
  - **The orphan, LR008.** Before it holds one commit call of the write, the
    shim forks the real `psql` call into its own session (`setsid`, as
    `guard-bind` does), and that call waits on a FIFO before it reaches
    `psql`. The writer's 8 s timer kills the shim, the call's client, and
    does not reach the held call, which holds no lock and outlives its
    client, as a statement still queued at a loaded server after its client's
    timer killed `psql` would (lane 4's section, "The in-doubt rule at the
    deadline"). Without the fork the timer would kill the call at 8 s, there
    would be nothing to release at about 10 s, and `commit_write` would never
    reach step 9. After the lookup has exited 75, the shim releases the held
    call: `commit_write` reaches step 9 past `not_after` and raises LR008, and
    the head, the journal and the lines equal their values before; the write
    id stays absent.
  - **LR008, the engine's half.** A direct commit call
    whose entry's `not_after` is the clock plus 2 s, held by the shim for 3 s
    before it reaches `psql`, gets LR008, and the head, the journal,
    `journal_edits`, `journal_files`, `files` and `doc_lines` are unchanged.
    The same call with the clock plus 8 s commits.
  - **LR008, the client's half** (lane 4, comment 6709038351):
    - inside the deadline: the shim holds the writer's first commit call
      past its `not_after`, so it ends with LR008; the writer takes a fresh
      snapshot, makes a new attempt under the same write id, commits, and
      exits 0, with one journal row for the id;
    - at the deadline: with every commit call held past its `not_after`, the
      writer waits until the database clock passes the largest of them, then
      makes one `find_write` lookup, and exits 0, 75 or 3 as above;
    - in no case does an LR008 alone give 3 or 69: the writer's exit is
      decided by a later attempt or by the lookup.
- **Tests.** In `code/tests/test_sql_register_in_doubt.py`:
  - `test_a_write_in_doubt_at_the_deadline_makes_one_lookup[found|absent|held|no-answer]`;
  - `test_the_deadlines_lookup_waits_past_the_largest_not_after`;
  - `test_an_orphaned_commit_call_released_after_its_not_after_writes_nothing`;
  - `test_a_commit_past_its_not_after_raises_lr008_and_one_within_it_commits[past|within]`;
  - `test_lr008_inside_the_deadline_is_a_lost_turn_and_the_write_exits_0`;
  - `test_lr008_at_the_deadline_leaves_the_exit_to_find_write[found|absent|held]`;
  - `test_lr008_alone_never_gives_3_or_69`.
- **Run time.** About 10 s a case, because the lookup waits for `not_after`,
  which is about 10 s after the snapshot (the 8 s timer plus 2 s):
  - the four `[found|absent|held|no-answer]` cases take about 40 s;
  - the LR008 cases that reach a lookup (the orphan's, and the three at the
    deadline) add about 10 s each, and the engine's half and the
    inside-the-deadline case 3–5 s each.
  - That is about 60–110 s in all.

#### AT-F4. The ruleset inside the freeze, and off before the removal

- **Ruling.** The order is drain, the last `PAUSED` write, the ruleset on, the
  import, the cutover, then the flips. The rollback turns the ruleset off once
  the mirror is proved current, before the removal of `lanes/STORE` is pushed:
  `lanes-index rollback --hold-marker` stops after the proof, and a second
  `lanes-index rollback` resumes at the marker's push (§2.3, step 3, accepted
  by lane 3 in comment 6579459518).
  The ruleset rests on Q21(b)'s two open points: the org's plan, and the
  paths Brett is to name (AT-6's options; section 1.4).
- **Fixture.** Test 13's drill, with the ruleset modelled by test 6's
  pre-receive hook, switched by a file the drill's runbook steps set.
- **Oracle.**
  - The last `PAUSED` write is pushed before the hook is on, and no files-mode
    write is refused by it.
  - With the hook on, a files-mode push to a rendered path is refused (the
    model works), and the import, the cutover and the flips succeed.
  - In the rollback, `--hold-marker` exits 0 with the proof made and the
    marker still on origin, the switch at `files` and the marker
    `removing`. The hook is then switched off, and the rerun pushes the
    removal and ends with the marker `absent`. A files-mode write after the
    rollback exits 0 and is pushed.
- **Tests.** In `code/tests/test_sql_register_drill.py`:
  - `test_the_ruleset_goes_on_after_the_last_paused_write_and_before_the_import`;
  - `test_the_rollback_turns_the_ruleset_off_before_the_marker_removal_is_pushed`,
    through `--hold-marker` and its rerun;
  - `test_a_files_mode_write_after_the_rollback_is_pushed`.

#### AT-F5. Two acts racing a binding

- **Ruling.** Each write verb whose act made earlier deciding reads carries an
  expectation that the commit checks (F5; D5, one transaction per verb).
  Lane 4's section, "The acts and their expectations", settles the form, and
  lane 3 accepted it (comment 6708889286):
  - an act runs `lanes-edit.sh act-begin` before its first deciding read,
    which prints `<act-id>@<head seq>` (`<id>@files` off primary), and
    exports it as `LANES_ACT`;
  - each write carries `LANES_EXPECT="<scope> …"` on its own call, from six
    scopes: `row:<L>`, `bind:<L>`, `objects:<L>`, `alias:<L>`,
    `archive:<L>` and `sid:<uuid>`;
  - the commit refuses with 2, `expectation: <scope> changed at seq <k> by
    <lane>/<verb>`, when a journal entry after the base touched a listed
    scope and carries another `act_id`; the act's own entries never refuse
    it. Lane 3 accepted lane 4's row 20 (comment 6710770304): the `act_id`
    column and each entry's touched keys come with lane 1's v2.4 (#165
    6711221999), which lane 3 folds as commit K (6711233420). Their tests,
    and LR009's, "expectation changed", exit 2, come in the pass after
    lane 3's review;
  - `lane-start`'s three register writes are one composite verb,
    `start-record`, carrying the union of their expectations; every other
    act stays one transaction per verb.
- **Fixture.** For each write of lane 4's tables, parametrised: `lane-start`'s
  W1 (`add-row`), W8 (`request-handoff`) and `start-record` (W2 to W7);
  `lane-handoff`'s late `log PAUSED --utc`, W9, W11 and W12; `lane-end`'s
  W13, W14 and `retire-rows`; the handoff skill's step-4 writes; the guard's
  answer 2, whose child runs `set-row-state` on the lane left with
  `LANES_EXPECT="row:<from> bind:<from>"`; and `lane`'s `request-handoff`.
  The act runs to its write, and the shim holds that write's commit call. A
  rival write that touches one of its scopes, from a second session, then
  commits. In files mode both variables are set and ignored.
- **Oracle.**
  - On release, the act's commit refuses with 2 and the `expectation:` text
    naming the scope, the rival's seq and its lane and verb, and writes
    nothing: the journal grew only by the rival's entry.
  - The act exits with the code lane 4's arms give: `start-record`'s 2 makes
    `lane-start` die 2 (a second binding); W7's refusal stops the launch;
    the guard's child, refused 2 on its `set-row-state` of the lane left,
    records a note in the bind's record; never 0.
  - Without the rival, or with a rival that touches no listed scope, or with
    an earlier write of the same act (the same `act_id`), the write commits.
  - A `HANDOFF-REQUESTED` line is not a lane-kind line, so a requester's
    line never refuses the holder's own handoff.
  - In files mode, `LANES_ACT` and `LANES_EXPECT` change nothing.
  - **The three conditions** [BRETT: row 22]: `lane-start` re-reads the row
    at its second `binding` read after a W8 wait and refuses on a change
    outside the state cell; the skill's step 4 re-derives the lane's
    identity and refuses if it differs from step 1's; `lane-end`'s gate
    inherits `LANES_ACT`, so its `NOTED` writes do not refuse W13. One case
    each.
  - **Re-basing** (lane 4's section, "The acts and their expectations"):
    `act-begin --rebase` keeps the id and takes a new base, only after a
    deliberate wait for another place's write. The test runs `act-begin`, a
    rival writes touching one of the act's scopes, and the act re-bases with
    `act-begin --rebase`, which prints the same act id with a new base, the
    head seq then. The act's write on that scope commits, since the rival's
    entry is at or before the new base; a second rival entry after the new
    base on the same scope still refuses it, with 2.
  - **Before a launch,** `LANES_ACT` is unset (`exec`, `respawn-pane`), so
    the session and its guard do not inherit it: the fake `claude` started by
    `exec`, and the command the fake `tmux` logs for `respawn-pane`, see no
    `LANES_ACT`, in a run where `act-begin` had exported one.
- **Tests.** In `code/tests/test_sql_register_order.py`:
  - `test_act_begin_prints_its_base_and_touches_no_database_off_primary`;
  - `test_an_act_whose_scope_changed_before_its_commit_writes_nothing[<write>]`,
    on the columns of lane 1's v2.4 (commit K);
  - `test_an_act_whose_scopes_held_commits[<write>]`;
  - `test_an_acts_own_earlier_write_never_refuses_its_later_one`, on the
    columns of lane 1's v2.4 (commit K);
  - `test_a_handoff_request_never_refuses_the_holders_own_handoff`;
  - `test_act_variables_change_nothing_in_files_mode`;
  - `test_each_of_the_three_conditions_holds[lane-start-reread|skill-step-4|lane-end-gate]`
    [BRETT: row 22];
  - `test_act_begin_rebase_keeps_the_id_and_takes_the_new_base`;
  - `test_lanes_act_is_unset_before_a_launch[exec|respawn-pane]`.

#### AT-F6. Answer 2's bind, detached

- **Ruling.** Ruling 16's fallback applies (lane 3, comment 6579459518): the
  measurements put the bind on primary at 9.64 s median, against about 3 s
  of room (section 5.1, M9). So answer 2 exits 2, and the bind runs
  detached (SVC-8; the timeouts table's answer-2 row), in every mode, files
  mode included: that is the fix of opensoft/openRepoTools#347, phase 1's
  first guard PR (lane 3, comment 6710770304). Lane 4's section,
  "Answer 2's detached bind, and the bind as one verb", settles how, and
  lane 3 accepted it (comment 6708889286):
  - the guard rewrites the offer file to `mode=binding`, starts the internal
    verb `guard-bind` as a `setsid` child with the hook's descriptors 3 to 9
    closed, stdin on `/dev/null` and its output in a 0600 file per attempt,
    prints "BINDING" and exits 2;
  - the offer file is the record, with the child's pid and start time
    (`pid_start_of`) as its liveness test, its host and container, the
    attempt (1 to 3), a 180 s bound and, on primary, `act`, `write_id`,
    `generation` and `not_after`;
  - the child runs `lane-start --no-launch --bind` under
    `timeout -k 10 170`, then the `set-row-state` on the lane the window
    left, and writes the outcome: `bound`, `bind-failed` with `rc` and
    `why`, or `rc=124` or `137` when its bound stops it;
  - lane 4's eleven-row table gives what the guard answers meanwhile. Nine
    rows answer 2: the answer `2` itself, a child live inside its bound, a
    child live past it (no `timeout`), after success (once, "MOVED"), after a
    failure below the third attempt (once), after the third failure, stale
    (once), `binding` from another host or container, and an unreadable
    record. The row for another session wanting the same lane answers by that
    session's own table. The row "does a failure restart it?" is not an
    answer: it states that a failure never restarts a bind by itself, a
    failed bind spends the person's yes, each `2` is one attempt, and an offer
    allows at most three (case 5 shows it). The prompt after "MOVED" passes
    with 0;
  - `lane-start`'s three register writes are one verb, `start-record`, in
    both modes: one transaction on primary, and in files mode one register
    commit and push in place of three (lane 3, comment 6710770304); the
    Rule 3 stamp stays a git commit outside it, so answer 2 is three writer
    calls: `start-record`, the `set-row-state` on the lane left, and the
    stamp.
- **Fixture.** The guard's answer-2 path on an offer, with the destination
  lane free, on `mode=primary` against the session cluster, and in files
  mode on a bare origin. A stub `lane-start` that sleeps 15 s, or exits a
  given code, stands in where the case says so. The bind's own calls go
  through the shim, which can hold `start-record`'s commit on a FIFO, so a
  second prompt arrives while it is in progress. The hook runs under a
  runner that, like Claude Code 2.1.319, sends SIGTERM to the hook's whole
  process group at its timeout and does not wait on pipes a child inherits.
  A fake `tmux` logs its calls.
- **Oracle,** lane 4's fourteen cases ("Phases, and AT-F6"):
  1. answer `2` exits 2 with "BINDING" before `T0 + 1 s` while the stub
     `lane-start` sleeps 15 s, and a runner reading the hook's pipes sees
     EOF at once. The hook's own process makes no register write: the
     window's rename and the offer file are its only acts;
  2. the child survives a group `SIGKILL` and a group `SIGHUP`, and the
     record ends `bound`;
  3. a prompt mid-bind gets "BINDING IN PROGRESS", is not passed, and the
     record is unchanged;
  4. after `bound`, one "MOVED" with the owed notes, the record removed,
     and the next prompt exits 0;
  5. after a stub failure: the why, the window renamed back to `<from>`,
     the offer kept, attempt 2 on the next `2`, and choice 2 withdrawn after
     the third failure;
  6. a child killed with `-9` gives "no outcome", and an altered `pidstart`
     reads as dead;
  7. a stub past the bound ends `rc=124`;
  8. two sessions answer `2` for one lane on the primary fixture: one
     `start-record` commits, and the other is refused 2 by `bind:<L>`;
  9. the bind is one register commit plus one handoff commit in files mode,
     and one journal entry on primary; a row change injected after
     `act-begin` refuses it with 2 and writes nothing. With the
     `set-row-state` on the lane left, answer 2 journals two entries;
  10. an injected `psql` exit 2 on every call of the bind's write, its
      deadline's lookup included, gives `rc=3` with the id; at the next `2`
      the child's `find_write` lookup runs first, and found writes no
      second `RESUMED`, while absent binds anew under a new id;
  11. a rerun after a failed stamp push writes no register line and exactly
      one stamp;
  12. a fake `tmux` sees the child's untargeted calls with `TMUX_PANE` set
      and stdin on `/dev/null`;
  13. a stub `lane-start` sees `LANES_NO_FETCH`, `LANES_ACT` and
      `LANES_EXPECT` unset, and `TMUX` and `TMUX_PANE` set;
  14. the hook string and `GUARD_TIMEOUT` are byte for byte unchanged.
- **Four rows of lane 4's guard table that none of the fourteen cases
  reaches.** Each is a case of its own, with the oracle the table gives for
  its row (lane 4's section, "What the guard answers"). Cases (a) to (c)
  write the offer file's record directly, with a sleeping process standing in
  for the child where one is needed; case (d) runs two real binds:
  - **(a) A child live past the bound, with no `timeout`.** The fixture's
    `PATH` has no `timeout`, so `guard-bind` ran the child unbounded, and the
    record's `deadline_epoch` is already past with its pid and `pidstart`
    those of a live process on this host. The answer is exit 2, the record
    unchanged, and the text "THE BIND HAS RUN PAST ITS 180 s BOUND … wait, or
    `kill <p>`" with the child's pid. Case 7 is the other half, where
    `timeout` exists and the child ends `rc=124`;
  - **(b) `binding` from another host or container.** The record's `host` or
    `container` differs from the test's (`LANES_WORKSTATION`,
    `LANES_CONTAINER`), with a pid that is dead on this host, so a guard
    that judged the pid would call it stale. Until the record's deadline plus
    60 s has passed, the answer is exit 2, the record unchanged, and the text
    "A BIND OF THIS TRANSCRIPT IS RUNNING ON `<host>/<container>` … Your
    prompt was NOT sent". Once the deadline plus 60 s has passed, it is
    answered as stale: exit 2 once, `name-drift`, `rc=unknown`, `write_id`
    kept. The test runs once with another host and once with another
    container;
  - **(c) An unreadable record.** An offer file the guard cannot read, such
    as a truncated one. The answer is exit 2 with today's text (lane 4's
    table cites `:11097–11101`), and the record dropped, so the next prompt
    meets the ordinary table;
  - **(d) Another session wanting the same lane, in files mode.** Two
    sessions answer `2` for one lane on the files-mode fixture, the bare
    origin (case 8 is the primary half). The bind that reaches `lane-start`'s
    re-read of `binding` at `:2614` second is refused there, the other record
    ends `bound`, and there is one `RESUMED` line and one stamp for the lane.
    Lane 4's table names the re-read but gives no exit code for it, so the
    test asserts a refusal and the absence of a second line and records the
    code it sees.
- **The record's code,** under lane 4's corrected table (comment
  6709560550). The child runs the real `lane-start --no-launch --bind`
  through AT-11's stamp shim, for a resume and for a new lane that the offer
  names. On any non-zero code of `start-record`, the record ends
  `bind-failed` with `rc` that code, and nothing is bound: the register
  holds none of the bind's lines, and the child writes no stamp. The codes
  are 69, 75 and 3, and a resume's 2, from case 9's row change injected
  after `act-begin`. On a resume's 3 the record keeps `write_id`,
  `generation` and `not_after`, for the next `2`'s lookup (case 10). The 75
  and 3 cases each wait for the deadline's lookup, about 10 s (AT-11's run
  time).
- **The regression tests of today's two defects** (lane 4's rows 34 and 35,
  which lane 3 files against the lane tools; the hook issue, row 34's
  in-hook cut, is opensoft/openRepoTools#347). A suite test cannot run
  today's form, since phase 1's PR removes it. So the defect is shown once,
  on the pinned `2a057a6d`, and recorded in the PR, and each of the two tests
  asserts only that the detached form passes:
  - **Row 34, answer 2 killed partway.** A stub `lane-start` that takes
    10.5 s, today's measured bind, and forks a `( … & )` child as
    `index_nudge` does. Under the runner's group SIGTERM at 5 s, the detached
    form's hook has exited 2 before the timer, and its child completes, with
    the record ending `bound`. The defect, shown once on `2a057a6d`: the
    in-hook bind is cut part way and its child dies;
  - **Row 35, a failed bind answered again.** After the stub fails once,
    the detached form renames the window back to `<from>`, keeps the offer,
    and the next `2` starts attempt 2. The defect, shown once on `2a057a6d`:
    today's code keeps the window named `<to>`, so the next prompt drops the
    "KEPT" offer and "answer `2` again" cannot be taken.
- **Options.** Lane 4's row 40 [BRETT: row 40]: on a host without `setsid`,
  the child is detached by python3's `os.setsid`, else by `nohup` alone,
  which survives a hangup but not a group kill. Case 2 is then run with the
  python3 form, and the `nohup`-only form is a stated gap.
- **Not a CI test: the detached child outliving the hook under Claude Code
  itself.** Lane 4's hook probe showed it on Claude Code 2.1.319 (lane 4's
  section, "It survives the hook"; `HOOKPROBE-report.md`, R1, R1b, R2 and
  R3): at a hook's timeout Claude Code sends SIGTERM to the hook's whole
  process group, so a `( … & )` child died and a `setsid` child completed;
  on a normal exit it killed nothing; and it did not wait on pipes a child
  inherited. Only an escalation to SIGKILL is not shown, and it cannot reach
  a `setsid` child either. CI runs no Claude Code session, and the suite's
  runner above models that behaviour rather than proving it. So the check
  stays a real-session one, recorded beside section 5's figures: in a
  session whose hook starts the detached child, the child is still alive,
  with the same start time, 10 s after the hook returned, and the session's
  transcript shows the hook completed, not `hook_cancelled`. It is run once
  per host kind (py-bench and cloud-bench), on Brett's word, from a lane
  sandbox, and again on a Claude Code release that changes how hooks end.
- **Tests.** In `code/tests/test_sql_register_guard.py`:
  - `test_answer_2_exits_2_binding_before_t0_plus_1_s_and_the_runner_sees_eof`;
  - `test_the_bind_child_survives_a_group_kill_and_hangup_and_ends_bound`;
  - `test_a_prompt_mid_bind_is_not_passed_and_leaves_the_record`;
  - `test_after_bound_one_moved_then_the_next_prompt_passes`;
  - `test_a_failed_bind_renames_back_keeps_the_offer_and_allows_three_attempts`;
  - `test_a_dead_or_reused_bind_pid_gives_no_outcome`;
  - `test_a_bind_past_its_bound_ends_rc_124`;
  - `test_a_live_bind_past_its_bound_with_no_timeout_answers_2_and_names_the_pid_to_kill`;
  - `test_a_binding_from_another_host_or_container_is_not_judged_by_its_pid_until_the_deadline_plus_60_s[host|container]`;
  - `test_an_unreadable_bind_record_is_dropped_with_exit_2`;
  - `test_two_sessions_binding_one_lane_one_start_record_commits`;
  - `test_two_sessions_binding_one_lane_in_files_mode_the_second_is_refused_at_the_reread`;
  - `test_the_bind_is_one_register_write_and_refuses_a_changed_row`;
  - `test_a_bind_in_doubt_looks_its_write_up_before_a_rerun`;
  - `test_the_bind_child_records_bind_failed_with_the_code_and_binds_nothing[<kind>-<code>]`,
    where `<kind>` is `resume` or `new`, and `<code>` is 2, 75, 3 or 69 for
    a resume and 75, 3 or 69 for a new lane;
  - `test_a_rerun_after_a_failed_stamp_push_writes_one_stamp`;
  - `test_the_bind_child_reaches_tmux_through_tmux_pane`;
  - `test_the_bind_child_environment_is_as_lane_4_lists_it`;
  - `test_the_hook_string_and_guard_timeout_are_unchanged`;
  - `test_answer_2_survives_a_group_sigterm_at_the_hooks_timeout` (row 34);
  - `test_a_failed_bind_can_be_answered_again` (row 35).

#### AT-F7. A declared outage with the service and the database down

- **Ruling.** Both specs gain the declared-outage exception for the guard's
  read, with SVC-12's three conditions checked by the client, in order,
  offline. When an unexpired declaration names this database, the client
  reads it once, at the prefetch, before the direct read; caps the direct
  read at `T0 + 3.65 s`; and starts the mirror path by `T0 + 3.75 s`.
  Without one, SVC-8 is unchanged but for one `stat` at the prefetch (lane
  5, comment 6579523948, accepted by lane 3 in 6579562840).
- **Fixture.** Test D20's declared outage, with the service's socket absent
  and the database stopped; then the same with test D9's F2 relay in place
  of the stopped database, so the direct read meets a silent host and runs
  to its cap. The hook runs under its 5 s kill timer.
- **Oracle.**
  - With the outage declared and the database stopped, the guard answers
    from the mirror, says so, and exits as the mirror's answer gives. With
    none declared, it exits 2 and never reads the mirror.
  - With the silent relay and the declaration:
    - the shim logs the client timer the direct read was given, which is at
      most `3.65 s` less the time the guard had spent before the call,
      measured from `T0`;
    - the `git` wrapper's log stamps the mirror path's first `git` call no
      later than `T0 + 3.75 s`, plus a stated scheduling margin of 0.1 s;
    - end to end, the hook answers from the mirror in under 4.5 s, never
      killed.
  - The declaration is read once, at the prefetch, which a behaviour shows:
    the shim holds the direct read on a FIFO, the test removes
    `register-outage`, then releases the read to fail; the guard still
    answers from the mirror, with the declared line.
- **Tests.** In `code/tests/test_sql_register_outage.py`:
  - `test_a_declared_outage_lets_the_guard_read_the_mirror_with_service_and_database_down`;
  - `test_with_no_declaration_the_guard_exits_2_with_service_and_database_down`;
  - `test_under_a_declaration_a_silent_database_is_capped_and_the_mirror_answers_inside_the_budget`;
  - `test_the_declaration_is_read_once_at_the_prefetch`.

#### AT-F8. Every failure of the mode query

- **Ruling.** Q6(c) gets its own exit code from `lanes-index mode`, never 2:
  78, `EX_CONFIG`, which no lane tool exits at `2a057a6d` (lane 4, comment
  6579524611; lane 3, 6579562840). Any other failure of the mode query runs
  files mode with a warning, and the marker check then refuses it on a SQL
  estate.
- **Fixture.** A stand-in at `lanes-index`'s path, run on `mode=index` and on
  `mode=primary`, on a files estate and on a SQL estate (`lanes/STORE` on
  origin). Its answers are the rows of lane 4's table ("The mode query's
  failures"): 78; 2 for a refused configuration; 64, a `lanes-index` too old
  to have `mode`; 1; 127 (removed); killed by a signal; exit 0 with stdout
  that is not exactly one line, `index` or `primary`; 69, 75 and 3; and a
  hang, which the query's client timer ends. Each case runs once more with
  `LANES_INDEX=off` set, as `lane-worktrees` sets it.
- **Oracle.**
  - 78: the verb exits 69 with the build's refusal text, writing nothing.
  - Every other failure: one warning on stderr, then files mode. On a files
    estate the write exits 0 in files mode. On a SQL estate the marker check
    after S3's fetch refuses it, as test 3 does, and no file is written.
  - **The hang** (D9's row for the mode query; lane 3, comments 6710770304
    and 6711028142). The query's client timer, 2 s, kills the stand-in:
    its process is gone once the verb returns, and the query ends under
    3 s, the timer plus AT-D9's 1 s allowance. The verb then runs in files
    mode with one warning on stderr naming the timeout. On a files estate
    the write exits 0; on a SQL estate the marker check refuses it with 69,
    and no file is written.
  - `LANES_INDEX=off` never skips the query: the stand-in's log shows the
    call in every case (lane 4's row 25).
- **Tests.** In `code/tests/test_sql_register_marker.py`:
  - `test_the_q6c_refusal_has_its_own_code_and_the_writer_exits_69`;
  - `test_any_other_mode_query_failure_runs_files_mode_with_a_warning[<failure>-<mode>-<estate>]`.

#### AT-F10. A pending record and an unrelated identical command

- **Ruling** (`design.md` S1, "The write id"; §1.3; lane 4's section, "The
  pending record"): the record is `<state>/register-pending/<d>`, mode 0600
  in a 0700 directory, where `<d>`, the entry's `journal.args_sha256`, is
  the sha256 of `lanes-pending/1`, `LANES_LANE`, `LANES_SESSION`, the verb
  and each argument, NUL-separated. It holds `format=1`, the write id, the
  generation, the database, lane, session, verb, the digest, the command
  line, its UTCs, the pid and its start time, host, container, attempts and
  `in_doubt`. Its maximum age is 15 minutes from `updated_utc`
  (`LANES_REGISTER_PENDING_MAX_AGE_S` for tests). A plain rerun with a live
  pid, judged by pid and start time, waits until the deadline, then exits
  75, "an identical write (pid) is still running". A
  write id already in the journal for another lane, verb or arguments
  (`journal.args_sha256`), or minted against another generation, fails with
  LR007 inside the transaction, and the writer exits 2, naming the id and
  what differs, with nothing written. LR007 is accepted by lane 3 (comment
  6579459518).
- **Fixture.** Test D8's records: one older than the maximum age, from a
  write that committed; one whose pid is live; one whose pid is live but
  whose start time differs (a reused pid); a `LANES_WRITE_ID` naming an
  entry of other arguments; one naming an entry of another verb; a record
  whose generation is not the switch's; and two runs that differ only in
  `LANES_SESSION`. `LANES_REGISTER_PENDING_MAX_AGE_S=2` for the age case.
- **Oracle.**
  - The old record is not reused: the identical command warns, prints the
    lookup, mints a new id and writes.
  - The second of two concurrent identical runs waits while the first's pid
    is live. Where the first ends inside the deadline, its record is gone
    and the second writes under its own id: two entries. Where it is still
    live at the deadline (`LANES_REGISTER_DEADLINE_S=1`), the second exits
    75 with "an identical write (pid) is still running", and writes
    nothing.
  - A record whose pid was reused reads as not live, and is taken over.
  - Two runs differing only in `LANES_SESSION` have different digests, and
    neither takes the other's record.
  - The other-arguments, other-verb and other-generation runs each end with
    LR007 in the shim's log, exit 2, and write nothing; a record of another
    generation is not reused at all.
  - A rerun with the same arguments of a write already in the journal ends
    with LR006 and exits 0.
  - The record's mode is 0600, its directory's 0700, and its name equals
    the entry's `args_sha256`.
- **Tests.** In `code/tests/test_sql_register_in_doubt.py`:
  - `test_an_expired_pending_record_is_not_reused`;
  - `test_a_live_pending_record_is_waited_for_not_shared[ends-in-time|live-at-deadline]`;
  - `test_a_reused_pid_in_a_pending_record_reads_as_dead`;
  - `test_the_pending_digest_covers_the_session_and_equals_args_sha256`;
  - `test_a_write_id_of_other_arguments_verb_or_generation_is_refused_lr007_with_2[arguments|verb|generation]`;
  - `test_a_write_id_already_journaled_for_the_same_arguments_exits_0_on_lr006`.

#### AT-F11. A guard reading a stale mirror

- **Ruling** (D20, SVC-12, §2.5): `outage declare` records `mirror-seq=<m>`,
  read from `origin/main` after a fetch, or named by `--mirror-seq <m>` where
  the host cannot fetch; with neither, it refuses with 2. The guard answers
  from the mirror only when `origin/main`, as last fetched, carries a
  `Register-Seq` of at least `m`.
- **Fixture.** Test D20's declared outage at `m`, with the guard's checkout
  last fetched at `m − 1`, and then at `m`. A host whose fetch fails, for the
  declaration's own cases.
- **Oracle.**
  - At `m − 1` the guard exits 2 with "the mirror here is at seq `m − 1`,
    behind the declared outage's seq `m`; fetch it, or wait for the
    database". At `m` it answers from the mirror.
  - **The bounded lookup** (lane 2's timing, comment 6579627366; lane 5's
    adoption of form C, comment 6579666731). The client
    runs one `git log --first-parent -n 200` with
    `--format='%H%x00%(trailers:key=Register-Seq,valueonly)'` and no
    `--grep`, takes the first non-empty value, and refuses 2 where none is
    within 200. A `-n 200` with `--grep` does not bound the walk, since `-n`
    limits the output after the filter. With the tip at depth 0, one case
    puts `origin/main`'s newest render commit at depth 200, the first depth
    outside the bound, under 200 handoff commits, and another at depth 250,
    under 250: the guard must exit 2 in both, and a client that finds the
    deeper trailer is wrong. The wrapper's log shows one `git log` call, with
    `-n 200` and no `--grep`. Two more cases put the render commit at depths
    0 and 199, where the guard finds it.
  - With the fetch failing, `outage declare --mirror-seq <m>` writes the
    field, and `outage declare` with neither exits 2 and writes no file.
- **Tests.** In `code/tests/test_sql_register_outage.py`:
  - `test_the_guard_refuses_a_mirror_older_than_the_declared_seq`;
  - `test_the_guard_refuses_2_when_no_commit_within_the_bound_carries_register_seq[depth-200|depth-250]`;
  - `test_the_register_seq_lookup_finds_the_trailer_at_depths_0_and_199`;
  - `test_outage_declare_takes_mirror_seq_from_a_fetch_or_the_flag_and_refuses_with_neither`.

#### AT-F14. Output printed once in a burst

- **Ruling** (F14; S1, "Output and side effects, once"; lane 4's section,
  "Output, and the side effects that run once"): the verb runs as a child
  that stops at the seam; each attempt's stdout and stderr go to two files,
  and only the deciding attempt's are replayed, each whole to its own
  descriptor, stdout first. Output after the seam streams live. Every arm's
  side effect outside the register sits after the seam, so it runs once,
  after COMMIT: `write_event`'s `lane_state_follow`, `retire-rows`' follow
  loop, `claim`'s and `release`'s `gh_comment`, `request-handoff`'s tmux
  `guard_type` and poll, and `rename-lane`'s `lane_state_rename`, comment
  loop and handoff commit. One run makes one journal entry: a second reach of
  the seam exits 2, "a second register write in one run".
- **Fixture.** Test 4's burst of ten and its case (a); a logging fake `tmux`
  for `request-handoff`'s side effect; a logging fake `gh` for `claim`'s
  comment; and a verb arm the test copy makes reach the seam twice.
- **Oracle.**
  - Each committed writer's stdout carries its success text exactly once,
    however many attempts it made, and its stderr only the deciding
    attempt's lines.
  - A writer refused at its commit (D18, D19) prints its buffered output,
    then the refusal, and no success text.
  - The fake `tmux` is called once per committed `request-handoff`, after
    its COMMIT, and never for a refused one; the fake `gh` once per
    committed `claim`.
  - The arm that reaches the seam twice exits 2 naming the first seq, and
    the journal holds that one entry.
- **Tests.** In `code/tests/test_sql_register_order.py`:
  - `test_a_writer_prints_its_output_once_however_many_attempts_it_made`;
  - `test_a_refused_writer_replays_its_output_then_the_refusal`;
  - `test_a_side_effect_runs_once_after_commit_and_never_for_a_refusal[tmux|gh]`;
  - `test_a_second_reach_of_the_seam_in_one_run_exits_2`.

#### AT-F18. The disaster path with an old record

- **Ruling** (D22, D24, §2.3, lane 3's F18 ruling): a generation id in the
  switch row and in every pending record and printed rerun, a mismatch
  failing with LR007; the hosts re-pointed before any write; `bootstrap
  --roles-only` before a restore, since no database backup carries roles;
  and the marker rewritten by the cutover's own entry in its after-loss
  form, at `m+1`, never a third operator entry.
- **Fixture.** Test D22's re-import, with a pending record left from cluster
  A for a write at a seq up to `m`, and a printed rerun line from A.
- **Oracle.**
  - The record's rerun, and the printed rerun with its
    `LANES_WRITE_GENERATION=` naming A's generation (lane 4's section, "The
    pending record"), each end with LR007 and exit 2; nothing is
    re-applied.
  - B's roles exist before its restore, and `bootstrap --roles-only` creates
    no schema.
  - `lanes/STORE`'s `database=` names B. The journal holds exactly two
    operator entries in its life on B: the after-loss cutover's at `m+1`,
    and, after a later rollback, the rollback's.
- **Tests.** In `code/tests/test_sql_register_disaster.py`:
  - `test_a_pending_record_from_the_lost_database_is_refused_lr007`;
  - `test_bootstrap_roles_only_makes_the_roles_and_no_schema`;
  - `test_the_disaster_path_rewrites_the_marker_by_the_after_loss_cutover`.

#### F19 and F33

- **F19,** test 8's `commit`: applied in test 8 and section 4.2, which expect
  69, and 2 for `commit` (D10).
- **F33:** AT-F33 asserts that the 23505 on `write_id` never fires, and its
  no-lookup control proves the oracle can fail. Test 12's "through the
  UNIQUE `write_id`" is the lookup by it.

## 3. The fixture

The fixture is R1's option (a) (R1, section 4; P1145–1162). Lane 2's fixture
probe of 2026-11-02 settled its details with a working prototype. The probe ran
on py-bench, where the fixture skips (run a1) or refuses (b1 to b10), and as an
ordinary user in a container with PostgreSQL 16.15 installed by the runner
image's own steps, and with no network (c1 to c6, d1 to d4, s1 to s7). The run
ids below cite that evidence. Two system paths are named because the fixture
needs them: the package directory of PostgreSQL 16 on Debian and Ubuntu, and
the system temporary root that holds the socket. R1 and the fixture probe name
them, not the design. No other host path appears.

### 3.1 R1's real-engine fixture

- **Where it lives.**
  - The prototype's module is `code/tests/pgcluster.py`: Python 3.9 or later,
    standard library only. It is registered as a pytest plugin from
    `code/tests/conftest.py`'s `pytest_configure`, a historic hook, so the
    refusal still runs.
  - Its fixtures:
    - `pg_cluster`, session-scoped;
    - `pg`, one database per test;
    - `pg_template_sql`, session-scoped and overridable, which builds the
      template's schema.

    This plan adds `stoppable_cluster`, a cluster of a test's own, built on the
    same `Cluster` class, for the tests that stop the database.
- **Finding the binaries.** The probe settled the order (s7, b6 to b10):
  - `PG_BIN` is final. A `PG_BIN` that fails is reported, never replaced, as
    `conftest.py`'s named roots are.
  - Then `pg_config --bindir`. An answer that fails the check falls through.
  - Then `/usr/lib/postgresql/16/bin`.

  The check:
  - A candidate counts only if all four tools, `initdb`, `pg_ctl`, `postgres`
    and `psql`, are executable and say `(PostgreSQL) 16` to `--version`.
  - On the runner's packages, the `pg_config` on `PATH` is
    `postgresql-common`'s wrapper. It runs the newest installed major's
    `pg_config`, so it names 16 today even with `libpq-dev` 18.6
    installed. It would name a newer server major installed beside 16,
    which the check then rejects (s7).
  - The plan takes the probe's exact major, 16, rather than R1's "14 or
    later". Open point 12 records the cost: `ubuntu-26.04` ships
    PostgreSQL 18, so a runner move refuses until the major is changed or 16
    is installed.
  - The chosen directory goes first on `PATH` for every subprocess, so the
    code under test runs the `psql` of the server it talks to.
  - The first CI run prints what it found. The prototype prints that only on
    the skip path or under `-v`, which open point 12 asks to change.
- **One cluster per session.**
  - **Creation.** `Cluster.start()` first sweeps stale clusters (cleanup,
    below). Then it makes the directory and runs `initdb -D <dir>/data -N -A
    trust -U lanes_test -E UTF8 --locale=C`, with `LC_ALL=C`, `TZ=UTC` and no
    `PG*` variable.
  - **The start.** It starts `postgres` itself, by `Popen`, with
    `-c listen_addresses=`, `-c unix_socket_directories=<dir>`,
    `fsync=off`, `synchronous_commit=off` and `full_page_writes=off`. It does
    not use `pg_ctl`:
    - `pg_ctl`'s postmaster runs in a session and process group of its own,
      so `tests/run.sh`'s TERM-then-KILL of the suite's group cannot reach
      it;
    - a `Popen` postmaster stays in pytest's group, and the group was gone
      within 200 ms (s5).

    R1's line that the group kill "backs it up" holds only in this form.
  - **The cost** (median, then p95). `initdb -N` takes 660 / 775 ms. Start to
    ready takes 37 ms inside the session fixture; the 10-run comparison
    below gives the pid-file poll alone 30.0 ms. The roles, template and
    schema take 111 ms. Stop and
    remove take 60 ms. The session total is 878 / 1108 ms (section 5 of the
    probe). That is below R1's 1.1–1.3 s for `pg_ctl`'s cycle.
- **Readiness.**
  - The fixture polls `postmaster.pid` every 5 ms, until line 1 is its own
    postmaster's pid and line 8 reads `ready`. It has a 30 s deadline, and
    `Popen.poll()` fails it at once if the server has exited.
  - Building the template is the first real connection, so it confirms the
    server is up.
  - Measured over 10 runs each, as medians:
    - `pg_ctl -w`: 111.5 ms;
    - `pg_isready` every 5 ms: 42.1 ms;
    - `psql SELECT 1` every 5 ms: 47.8 ms;
    - the pid-file poll: 30.0 ms.
  - No first `SELECT 1` failed after any method.
  - Polling `pg_isready` is the accepted alternative. It is a public
    interface, about 12 ms slower per session, and adds `pg_isready` to the
    required tools.
- **The templates.**
  - The template database, `tmpl`, is built once per session by
    `pg_template_sql`: P8's bootstrap, run with `--no-passwords` through the
    admin service (AT-P8's fixture), which makes the schema, the four roles
    (the owner, REGISTER-WRITER, READER and the indexer) and their GRANTs.
    The template holds no test-only object. It is then marked
    `IS_TEMPLATE true ALLOW_CONNECTIONS false`, so no session can hold it
    open.
  - A second template, `tmpl_imported`, is cloned from it, with test 7's
    fixture register imported.
  - Roles are global to a cluster, so they are made once, in the template
    step. Schema, table and function GRANTs live in each database's
    catalogue and travel with every clone. The database-level statements of
    §1.4, `REVOKE ALL ON DATABASE … FROM PUBLIC` and `GRANT CONNECT`, set
    `pg_database.datacl`, which `CREATE DATABASE … TEMPLATE` does not copy
    (PostgreSQL's `createdb()`, not yet run here), so `pg` runs those two
    statements, with `-v db=` the clone's name, after each clone. No test
    changes a role's attributes on the session cluster. A test that must, uses
    a `stoppable_cluster`.
- **One database per test.**
  - `pg` runs `CREATE DATABASE t_<n> TEMPLATE <template>`, and
    `DROP DATABASE … WITH (FORCE)` at teardown. That is about 56 ms to create
    and 21–24 ms to drop, about 80 ms per test, through a new `psql` each
    time.
  - FILE_COPY against WAL_LOG made no difference worth measuring, so the
    default is used.
  - Every clone is dropped, since each is about 7.3 MiB on disk.
  - The probe ruled out the cheaper forms:
    - a schema per test (about 26 ms) shares database-level facts, and needs
      `search_path` to reach the code under test's own URL;
    - a rolled-back transaction (about 10 ms) cannot hold anything that
      commits, nor the code under test's own `psql` process, nor
      SERIALIZABLE's 40001, which needs two committing sessions.
- **A test's own cluster** (`stoppable_cluster`).
  - It is built like the session's, with its own directory and socket.
  - It is stopped by a fast shutdown, SIGINT to the postmaster it started,
    and a wait, matching the probes' `pg_ctl stop -m fast`. It is started
    again by `Popen` on the same data and socket directories.
  - A fresh one costs about the session total, 0.9 s.
  - Copying a pre-made data directory instead was not probed (open point 13).
  - Section 5's durable figure uses one started with the default settings.
- **Socket placement.**
  - The directory is `mkdtemp(prefix="orpg-", dir="/tmp")`, mode 0700. It
    holds the socket and the data directory. The fixture checks the socket
    path is at most 100 bytes before the start.
  - The socket's path is 32 bytes whatever `TMPDIR` or `XDG_STATE_HOME` is.
    A run with a 205-character `TMPDIR` still passed (s6).
  - PostgreSQL's limit, measured: a 107-byte path starts, and 108 fails with
    "too long (maximum 107 bytes)".
  - A test's `tmp_path` under `tests/run.sh` would give 120–123 bytes, and a
    lane sandbox's state home 157–187, both too long (section 6 of the
    probe).
  - With trust authentication, the 0700 directory is what keeps other local
    users off the server.
  - The cost: the directory is outside `tests/run.sh`'s run root, so the run
    root's removal does not reach it. The fixture's teardown and sweep must.
- **Cleanup.**
  - **The session's finalizer** sends SIGQUIT, waits (SIGKILL after 30 s), and
    removes the directory. It runs after a failing test too (s1, c1).
  - **On Linux,** `PR_SET_PDEATHSIG(SIGQUIT)` takes the postmaster down with a
    SIGKILLed pytest, within 1 s (s3). Without it, the orphan kept answering
    `SELECT 1` on its socket.
  - **The next session's start sweeps.** An `owner` file holds the pid and its
    start ticks. The sweep stops a dead owner's postmaster, after checking the
    data directory named in `postmaster.pid` and the process's command line.
    Then it removes the directory, and prints what it removed (s3, s5).
  - **A zombie counts as dead.**
  - **The sweep touches only this user's `orpg-` directories,** so it never
    stops another lane's live cluster.
- **Root.**
  - `initdb` refuses to run as root (d1).
  - The fixture checks `geteuid()` first. It skips, with a reason saying the
    run is root (d2). Under the switch it refuses, with exit 4 (d3).
  - Without that check, every RE test would error with `initdb`'s text
    instead (d4).
- **Two lanes at once.**
  - Two sessions of one user, with the same `/tmp` and the same port 5432,
    ran at once with no clash (s2). There is no TCP: the port number only
    names the socket file, and each session has its own directory.
  - The whole example suite twice at once passed both times.
- **The environment.** The fixture builds each subprocess's environment with
  no inherited `PG*` variable. That covers `PGHOST`, `PGPORT`, `PGUSER`,
  `PGDATABASE`, `PGSERVICE`, `PGSERVICEFILE`, `PGSYSCONFDIR`, `PGPASSFILE`,
  `PGPASSWORD`, `PGOPTIONS` and `PGCONNECT_TIMEOUT`. So a person's settings
  never steer a test, and it never sets `PGPASSWORD`.
- **The skip.**
  - Where no engine is found and the switch is off, every RE test skips.
    The reason lists each place tried and why it failed, and names `PG_BIN`
    and `postgresql-16` (a1, b10).
  - The plugin prints a `!!! N real-engine test(s) SKIPPED !!!` banner with
    the reason even under `-q`, so the `tests` job's log shows it without
    `-rs`.
  - The skip path costs about 0.05 s (a1, c6).
- **A dependent wait, never a sleep.** Where one session must be waiting on
  another, as in test 4's two claimants, the test polls `pg_stat_activity`
  for that backend's `wait_event_type = 'Lock'`. The probe's 40001 example
  does so, and took 0.04–0.05 s.

### 3.2 What the fake psql keeps covering

- **The fake.** It extends `test_lanes_index.py`'s `FAKE_PSQL`. That records
  the URL, `PGPASSFILE`, whether `PGPASSWORD` is set, and stdin. The
  extension also records:
  - the whole argv, to check `-X`, `-q`, `-v ON_ERROR_STOP=1` and
    `-v VERBOSITY=sqlstate` (P1475; the probes);
  - the conninfo's options: `connect_timeout=2`, and each path's own
    `options=-c lock_timeout=… -c statement_timeout=…` from D9's table
    (P804–805), one case per path: 1000 and 1500 ms for the guard's direct
    read, 150 and 180 ms for the service's connection, 2000 and 5000 ms for a
    writer's snapshot and commit, 1000 and 3000 ms for a reader, 1000 ms and
    10 s for the renderer's reads and `reconcile`, 1000 and 5000 ms for
    `status`, 5000 ms and 10 s for each `rollback` call, and 2000 ms and
    300 s for `import`;
  - the environment of each call: `PGSERVICE` where the admin service is
    used, and never `PGSERVICEFILE`, `PGSYSCONFDIR`, `PGPASSFILE` or
    `PGPASSWORD` (SVC-2).
- **What it covers, and always runs:**
  - **argv.**
  - **Connection strings.**
    - `register_url=` for writes, and `reader_url=` for reads.
    - Never the writer's URL for a read, as `test_lanes_index.py` already
      asserts for the index.
    - Never the indexer's `url=` for a register write (P154–157).
  - **Credentials.**
    - The 0600 password file, with a looser one refused, as today.
    - `PGPASSWORD` never set.
    - No credential in argv, and no connection string, `service=` or `-d`
      for the admin service, which reaches `psql` only as `PGSERVICE` in its
      environment (SVC-2).
  - **The 69 refusal.** The fake exits 2, and the writer exits 69 with no
    second call.
  - **The retry's shape.** The fake exits 3 with `ERROR:  40001`, and the
    writer retries with the same `write_id` and never exits 69. The shape is
    all it shows; that a 40001 occurs is test 4's.
  - **The SQL's shape.**
    - One transaction per write.
    - SERIALIZABLE at the commit call, as Q7 (A″) sets it.
    - The head row updated before the journal row (P235–236).
    - `write_id` in the journal insert.
    - The timeouts set at connect.
    - Reads in one `REPEATABLE READ READ ONLY` snapshot, as today.
  - **Test 14's two states.**
- **The shim, an RE helper.** It is one Python script in front of the real
  `psql`, run as `python3 -I`, standard library only. A control file arms it
  per test. Its modes:
  - `window`: sleep W ms before handing on a call whose script commits;
  - `before` and `after`: a `pg_terminate_backend` line before or after the
    script's `COMMIT`;
  - `timer`: run the real `psql`, then sleep;
  - `fail-call` and `fail-match`: exit 2 on call k, or on a script carrying a
    given text;
  - `edit-entry`: rewrite one field of a commit call's entry before handing
    it on, for test 2's late failure and AT-LR4's cases;
  - `no-reconnect`;
  - `hold`: block on a FIFO.
- **How the code finds `psql`.** Through `PATH`: `lanes-index` reaches Postgres
  "ONLY through `psql`" (`code/lanes-index:1272–1276`). The fixture puts the
  fake's or the shim's directory first. If the design's writer calls `psql`
  by an absolute path, open point 2 asks for a seam.
- **What it never does** is assert an outcome an engine decides (section 1.1).

### 3.3 CI's refusal switch (Q1's 2A)

- **Today.**
  - **`tests`** runs `python3 -m pytest tests -q`
    (`code/.github/workflows/tests.yml:157`).
    `pytest -q` exits 0 on a skip, so the job accepts a skip that names its
    reason (P1142–1143).
  - **`tests-no-submodule`** runs the same with `-rs` (`:196`), printing every
    reason, and also exits 0 on a skip.
  - **`package`** runs three modules, `tests/test_package.py`,
    `tests/test_package_stamp.py` and `tests/test_install_pipx.py`, against
    the built wheel with `--junitxml` (`:542–543`). Its next step fails the
    job on any skip, failure or error, or when no test ran (`:545–575`). That
    check is Decision 9 of #228's `design.md`, `specs/006-release-pypi/design.md`
    in the spec leg, which `code/.github/workflows/tests.yml:210` and `:545`
    and `code/tests/test_tests_workflow.py:1198` cite at `2a057a6d`; it is not
    #165's D9.
  - **The root's `exact-pins`** runs `code/tests/run.sh -rs` with
    `OPENREPOTOOLS_COMPOSED=1`. That refuses an absent root, and nothing
    else (`code/tests/conftest.py`'s docstring), so an RE skip would pass
    there today.
- **The switch.**
  - **What it is.** The probe settled it as `OPENREPOTOOLS_REQUIRE_PG`, in the
    pattern of `OPENREPOTOOLS_COMPOSED`, with the same three values: unset,
    "0" or "1". Any other value is a usage error (b3), as
    `test_test_roots.py` refuses a `OPENREPOTOOLS_COMPOSED` that "is neither
    1" nor 0 or unset.
  - **How it refuses.** At "1", the plugin's `pytest_configure` raises
    `pytest.UsageError` when no usable engine is found, or the run is root.
    pytest exits 4 before any test runs (b1, b8, b9, d3).
  - **When the binaries are present but the cluster will not start,** the
    tests fail in either mode, and never skip. The probe did not drive this
    path through pytest. d4 shows the message it would carry (open point 12).
  - **Where it is set.**
    - In `tests.yml`'s workflow-level `env:`, as
      `OPENREPOTOOLS_REQUIRE_PG: '1'` beside `OPENREPOTOOLS_COMPOSED: '0'`.
      That reaches all four jobs, and `tests/run.sh` passes it through
      unchanged.
    - In the root's `composed.yml`, by a root PR that lands before the first
      code PR that needs a database (P1166–1168, P2131–2133).
      `OPENREPOTOOLS_COMPOSED=1` does not imply it (lane 3, comment
      6578688322); the root sets it by its own line.
  - **No pinned line changes.** `test_tests_workflow.py` pins each existing
    job's runner, checkout, Python and `run:` lines, and the four job ids
    (`:836–866`, unverified here: P1150 says `:836–842`). The probe's grep
    found no test that pins the workflow-level `env:`.
  - **The trigger the probe rejected** is R1's `GITHUB_ACTIONS=true`. It is
    implicit. It is true on self-hosted runners such as Eagle's, and true in
    forks. On its own it changes nothing: b4 skipped. That also meets the
    product seat's concern that a job without the binaries must never refuse
    by accident (P17, C92–103).
- **A missing engine, red in both jobs.** The refusal is eager: it refuses a
  run even when the run selects no RE test.
  - **In `tests` and `tests-no-submodule`,** pytest exits 4, and the job is
    red (b1).
  - **In `package`,** the step's pytest exits 4 under `set -euo pipefail`. No
    JUnit file is written, so that check also fails, on a missing
    file, and the job is red (b2).
  - **With PostgreSQL present,** both forms are green: c3 passed all five
    tests, and c4's check reported `{'tests': 5, 'skipped': 0, 'failures': 0,
    'errors': 0}`.
  - **In `guard-launch-mode`,** the eager refusal also turns a missing engine
    red, though that job selects no RE test.
  - **The lazy alternative,** checking in `pytest_collection_finish`, refuses
    only runs that select an RE test. It would leave `package` and
    `guard-launch-mode` green with no engine. The plan takes the eager form,
    since Q1's 2A asks for the engine wherever CI runs the suite.
- **A self-hosted runner.**
  - The Eagle runner's image, `eagle-fleet-worker:ubuntu24`, has no
    PostgreSQL and no pytest (the probe's run c).
  - Every code-leg job runs on GitHub's `ubuntu-24.04` today. If any job ever
    runs on the self-hosted Eagle runner instead, the variable moves from the
    workflow's `env:` to each job that has the binaries, and the Eagle-runner
    job holds none, so it is not refused (lane 3, comment 6578688322).
- **The tests that pin it.** In `code/tests/test_tests_workflow.py`:
  - `test_tests_yml_requires_the_real_engine_in_every_job_that_runs_it`: it
    resolves each job's effective `env:`, the workflow level and then the
    job level, and requires `OPENREPOTOOLS_REQUIRE_PG` to be `1` in every job
    that runs the suite or an RE module, whichever level holds it.

  In `code/tests/test_sql_register_fixture.py`, each case running a child
  pytest:
  - `test_the_engine_switch_turns_a_missing_engine_into_a_failure`, with
    `PG_BIN` naming an empty directory;
  - `test_without_the_switch_a_missing_engine_skips_naming_pg_bin_and_postgresql_16`;
  - `test_the_engine_switch_refuses_a_value_that_is_not_1`;
  - `test_no_real_engine_test_is_selected_by_guard_launch_modes_filter`, a
    collection with that job's `-k` expression.

## 4. The probes' cases written as tests

The probes ran each case on PostgreSQL 16.15 in a `postgres:16` container
(lane 2's probes, 2026-10-31). Each case below is a test on R1's fixture. The
probes' numbers are bounds where the requirement is a bound, and recorded
beside the result where it is not. No test asserts a probe's time as a
pass mark on a shared host, except the guard's 4.5 s bound inside the hook's
5 s budget.

### 4.1 The burst

- **Extends:** test 4, case (c); S6's tests (P678–679); ADV-C7.
- **Parameters.**
  - Ten writers, each claiming a distinct free object, released together by
    the barrier.
  - A window of W = 200 ms, injected by the shim's `window` mode between each
    writer's snapshot and its COMMIT.
  - A deadline of 30 s (Q7 (A″); D7), set with
    `LANES_REGISTER_DEADLINE_S=30`.
- **Oracle.**
  - All ten exit 0, and none exits 75 or 69.
  - The journal holds exactly ten new rows, with seqs `n+1` to `n+10`, no gap
    and no repeat.
  - There are ten distinct `write_id`s.
  - Each object has one `CLAIMED`.
  - The head row reads `n+10`.
- **The probes' numbers.** Recorded, not asserted:
  - under (A″), with no cap, the k-th writer needs k attempts (mean 5.5, most
    10). That is about k(k+1)/2 = 55 verb runs in all;
  - the drain took 2259 ms (2207–2296) at 200 ms;
  - under (A′), the lock, it took 2067 ms, with 0 aborts in 120 writes;
  - under (A), six attempts, 4 of 10 writers exhausted them in every burst
    from 40 ms up, which is why (A) fails this test (P1540–1547).

  The asserted bound is the deadline itself: no writer exits busy.
- **Two companion cases.**
  - **The window is effective.** The same burst with a 1 s deadline: at least
    one writer exits 75, none exits 69, and each busy writer's `write_id`
    is absent from the journal. Without a window, the test passes trivially
    (3 of 30 first attempts conflict at W = 0, the probes), and this case
    proves the window is there.
  - **The statement order.** Were the journal row inserted before the head
    update, every lost race would be a 23505, which the probes measured as an
    instant exit for 27 of 30 writers at 200 ms (P235–239). The main case
    catches it, since none may exit 69 or busy.
- **Deterministic on a shared workstation.**
  - The barrier makes the release simultaneous.
  - The window is 200 ms per round, far above scheduling noise.
  - The oracle is counts and order.
  - A loaded host only stretches the drain. The 30 s deadline is about 13
    times the probes' drain, and the companion case's "at least one busy"
    only gets likelier when the host is slow.
- **Tests.** AT-4 owns `test_ten_writers_released_together_all_commit_in_order`,
  whose oracle section 4.1 gives. Section 4.1 adds
  `test_the_burst_window_is_effective_a_short_deadline_sends_writers_busy`.
  Each run takes about 2.3 s in the probes, plus process starts.

### 4.2 The partition

- **Extends:** test 8, case (d); S5's rule (P541–550); ADV-C1 as the probes
  widened it.
- **Parameters.**
  - Two workstations on one bare origin. The database stays up throughout.
  - ws1, which rolls back, names a fresh, empty socket directory in its
    configuration.
  - ws2, the missed workstation, names the live one.
  - The verbs are the five no-fetch verbs and `claim`, each fetched and not
    fetched, with `LANES_NO_FETCH=1` unset and set.
- **Oracle** (Q4(b)(iii)).
  - While partitioned:
    - ws1's rollback exits 69 (D10);
    - the switch reads `primary`;
    - `lanes/STORE` is still on origin's `main`;
    - ws2's writes exit 0, rightly, since the estate is still primary and
      SQL holds them.
  - ws1's configuration is then restored, and the rollback runs to its end.
    After that:
    - every ws2 case exits 69 (`commit`: 2, D10);
    - the journal's count no longer changes;
    - the files equal a full render at the rollback's last seq;
    - every object has one holder.
- **The probes' numbers.** Recorded, not asserted:
  - without a fetch, fixes (i) and (ii) each leaked to an exit 0, and with a
    fetch each gave 69;
  - a `git cat-file` check cost 1.78 ms;
  - a fetch or `ls-remote` against a local origin cost 9–18 ms. GitHub was
    not measured.
- **Deterministic on a shared workstation.**
  - The partition is a difference between two configurations. It needs no
    firewall and no root.
  - The database never stops in this case.
- **The declared outage (Q26 (a′)).** A declaration is Brett's word,
  made by him or at a rollback's start, and not a check the guard makes.
  This case shows why: ws1's mirror, "last shown current", cannot see ws2's
  writes during the partition (P1938–1945).
- **Tests.**
  - `test_rollback_from_a_partitioned_clone_is_refused_and_removes_no_marker`;
  - `test_a_missed_writer_is_refused_once_the_partitioned_rollback_completes[<verb>-<fetch>-<no_fetch>]`,
    which expects 69 (`commit`: 2, D10).

### 4.3 The in-doubt commit

- **Extends:** test 12; S1's recovery by write id (P240–267); ADV-C3.
- **Parameters.**
  - One writer, `claim` of a free object, and `log NOTED`.
  - The shim's modes `after`, `before`, `timer` and `no-reconnect`.
  - Test 12's held session for the half-open case: the shim's `before` mode
    loses the first commit call before it lands, and a session as
    `lanes_register_writer` replays that call's script without its `COMMIT`.
    The `after` mode cannot build it, since its row has already landed.
  - The writer's `lock_timeout`, 2000 ms (D9), on the retry that goes
    through the UNIQUE `write_id` (P258–261).
  - The writer's outer timer at the design's value, D9's 8 s for the commit
    call. The only knob a test may set is the deadline,
    `LANES_REGISTER_DEADLINE_S` (D7).
- **Oracle.**
  - Test 12's oracle: one row per `write_id`, and exit 0. Where the writer
    cannot reconnect, the in-doubt code with the id named, then a rerun that
    reuses it.
  - **A case the probes add.** A lookup never takes a rival's row for the
    lost write. The writer is lost before COMMIT, and the shim's `hold`
    keeps it lost while a rival's write takes seq `n+1`. On release:
    - the writer's own row is written once;
    - the rival's row is untouched;
    - the writer exits 0.

    A lookup of `n+1` picked the rival's row 3 times out of 3 in the probes.
- **The probes' numbers.** Recorded, not asserted:
  - the terminate pair took 11–19 ms;
  - with the old transaction open, the retry got 55P03 at about 1010 ms
    under `lock_timeout=1000`;
  - with no bound, it waited 1583 ms and inserted once;
  - an outer timer turned the case into 124 at 5002 ms under `timeout 5`,
    with the row landed 4 times of 4.
- **Deterministic on a shared workstation.**
  - The terminate's place in the script, before or after `COMMIT`, fixes the
    outcome.
  - The half-open case's old transaction is the test's own, so the test
    decides when it ends.
  - The timer's sleep starts only after the real `psql` returns.
  - No proxy is needed: the terminate pair gives the same exit as a real
    loss and byte-identical stderr (the probes).
  - Stderr is never matched.
- **Options.** Under Q16 (a) alone, the rival case fails. D8 makes the
  report's code 3, which settles the proposal's open question.
- **Tests.** Test 12's six, and
  `test_a_lookup_never_mistakes_a_rivals_row_for_the_lost_write`.

### 4.4 The guard's timing

- **Extends:** test 10; the guard's budget (P781–812); concern 1; ADV-C4.
- **Parameters.**
  - The hook runs under a 5 s kill timer, the harness's `GUARD_TIMEOUT`
    (`code/openRepoTools:2344`).
  - The design's shares are taken as the timeouts table at `21f0191c` and
    SVC-8 set them:
    - `connect_timeout=2`, with one address;
    - `lock_timeout=1000` and `statement_timeout=1500`, set at connect;
    - a client timer cut to the parse's start line, `T0 + 3.75 s`, or
      `T0 + 3.65 s` under a declaration naming this database, which then
      starts the mirror path by `T0 + 3.75 s`.
  - Each case runs with the service up, where the service's connection meets
    the fault, and with it down, where the fallback's direct read does.
- **The cases:**
  1. **A loopback listener that accepts and never speaks.** The probes:
     exit 2 at 2007 ms under `connect_timeout=2`. It also stands for "an
     address whose connect never completes" (open point 11).
  2. **A host silent after connecting.** A loopback relay forwards to the
     cluster's socket until the startup completes, then forwards nothing.
     The probes: 124 at 5002 ms with only server-side bounds, and exit 2 at
     about 4006 ms with a 4 s client timer. The cut timer ends it at
     `T0 + 3.75 s`. Under a declaration, the same case answers from the
     mirror, inside the budget (AT-F7).
  3. **A read behind a migration's lock.** The test's own session holds an
     `ALTER TABLE` on the table the guard reads; and, separately, an `ALTER`
     queued behind an open reader. The probes: 55P03 at about 1011 ms and
     1009 ms.
  4. **A conninfo with several hosts.** The probes: two hosts at 2 s each
     exit at 4009 ms, and three at 6008 ms, which fails open. The design
     takes one address, so the case asserts that a configuration naming
     several is refused, or caught by the client timer, and in either case
     exits 2 inside the budget.
- **Oracle.**
  - The hook exits 2, was not killed, and finished in under 4.5 s, the
    frame's assertion (`design.md`, "One budget"). Its refusal names the
    store.
  - The elapsed time is recorded beside the probes' figure.
  - In the same cases:
    - a writer exits 69 inside its own, longer limit;
    - a reader, the renderer, `status` and `rollback` each exit inside their
      own bounds (P795–800).
- **Deterministic on a shared workstation.**
  - Listeners and the relay bind 127.0.0.1 on port 0, per test.
  - The lock is held by the test's own session.
  - The smallest margin is case 2's: the cut timer ends the read at
    `T0 + 3.75 s`, about 0.75 s before the 4.5 s assertion, and SVC-8 keeps
    the shares' sum under the budget "with room to print the refusal"
    (P784–785). If CI's runner misses 4.5 s, the shares are cut, in SVC-8's
    budget; the bound is never relaxed, because a guard past 5 s is killed
    and the prompt passes (lane 3, comment 6578688322).
  - The loopback address keeps the root `AGENTS.md`'s "reaches no network"
    true (P2128–2131). The Unix socket alone never exercises TCP (P824).
- **Options.**
  - **Under Q2(g),** the cases are the same for either client form.
- **Tests.** `test_a_database_that_does_not_answer_is_2_inside_4_5_s[<case>-<service>]`,
  in test 10's module. The other paths' cases are AT-D9's
  `test_each_database_path_exits_inside_its_bound[<row>-<fault>]`.

## 5. The measurement plan (Q10)

Q10 (P1667–1679) asks four things:
- on which host and store the cost of one write and the guard's 50 ms are
  taken;
- whether the 50 ms includes the client's process start and an on-demand
  start of the service;
- whether each figure is a recorded acceptance number or a CI assertion;
- what the PR body names. Q10 says the path alone moves the figure fifteenfold,
  so the PR body names the host, the store, the path and the statistic.

R1 recommends measuring where the gate runs, with at least 200 writes, at the
test settings and durable, and a bench figure beside it. The QA seat asks for
at least 20 warm runs, with the cold first call beside them. #165's body says
"recorded in the PR body".

### 5.1 What is measured

| Id | Cost | Path | Test |
|---|---|---|---|
| M1 | one write, through a new `psql` process | as R1 measured it (one call), and as the design's writer makes it (Q7 (A″)): a snapshot call and a commit call, each a `lanes-index` call with its own Python start (D2, [L4-c]) | 9 |
| M2 | one write in a persistent session | `\timing` inside one session (it leaves out the client) | 9 |
| M3 | the cluster's start and stop | `initdb -N`, start to ready, template, stop and remove | the fixture |
| M4 | a database per test | create from the template, and drop `WITH (FORCE)` | the fixture |
| M5 | the guard's round trip through the service | client start to answer, with the service warm and with an on-demand start; and the whole hook, exec to exit | 10 |
| M5b | today's direct guard, in four parts | reaching `code/lanes-edit.sh:16856`, the `tmux` read, `transcript_holders`, and the register read (concern 4, C148–170) | 10 |
| M6 | render time | one journal entry rendered; a full render of the fixture register; a render commit with a private index, and its push to the local origin; the import's proof | 1, 5, 7 |
| M7 | what a lane feels (P5) | `log NOTED`, `claim`, a landing's five writes and `lane-start --dry-run`, exec to exit, in both modes | 9 |
| M9 | answer 2's bind | `lane-start --no-launch --bind` and the guard's `set-row-state` on the lane it leaves, exec to exit, warm and cold, in the guard's own path; files mode today, and primary once it exists | AT-F6 |
| M10 | the guard's register read and parse on a real-sized register | the guard's six register reads on SVC-8's cache files (the parse alone), and today's six reads through git; with Q29's figure, the whole guard in the budget's four parts | 10 |
| M11 | the `Register-Seq` lookup, bounded | one `git log --first-parent -n 200` with the trailer in `--format`, on a lane's register checkout, beside the unbounded and `--grep` forms | AT-F7, AT-F11 |

M1 and M2 are each taken twice: at the fixture's settings (`fsync`,
`synchronous_commit` and `full_page_writes` off), and durable, on a
`stoppable_cluster` started with the defaults. M5 answers Q10's question about
the 50 ms by recording each part apart: the client's process start, an
on-demand start of the service, and the answer itself.

The decision rules, and what the measurements of 2026-11-02 decided:
- **M9.** If the bind's warm p95, with the guard's fixed costs before
  `guard_answer`, does not fit before `T0 + 4.25 s`, or the bind does not fit
  in about 3 s on a healthy fixture, the detached form applies (ruling 16).
  It did not fit: 9.64 s median on primary by estimate, and 10.47 s in files
  mode today. Lane 3 applied the detached form (comment 6579459518), in
  every mode (comment 6710770304), so M9 is recorded, never asserted, and
  is taken again in both modes once the bind is `start-record`, which
  exists in both. It is recorded beside the child's 180 s bound, which
  lane 3 accepted as arithmetic, with `LANES_GIT_TIMEOUT=45` in the child
  (lane 4's row 37), and which phase 1's tests measure the child's bind
  against.
- **The count of writes.** Today's bind is four writer calls, each its own
  commit and push: `append-session-id`, `set-row-state` to LIVE,
  `log RESUMED` and the handoff's Rule 3 stamp. With the guard's
  `set-row-state` on the lane it leaves, answer 2 makes five at the pin.
  Under lane 4's `start-record`, accepted by lane 3 (comment 6708889286), the
  three register writes are one transaction on primary and one commit and
  push in files mode (comment 6710770304), and the stamp stays a git
  commit, so answer 2 is three writer calls in both modes; AT-F6 expects
  one journal entry for the bind and two for answer 2 on primary.
- **M10.** The parse's 0.5 s reserve holds while the parse's p95 stays under
  it. On the real register it is 156.6 ms, so the reserve stays, with the
  start line at `T0 + 3.75 s` (lane 3, comment 6579459518).
- **M11.** Lane 5's 0.1 s estimate for the lookup holds for the bounded form
  only: 13.2 ms median, against about 300 ms for a `--grep` form, which is not
  bounded.

### 5.2 Where

- **CI's `ubuntu-24.04` `tests` job** gives the gate's figure (R1). Its store
  is the fixture's cluster on the runner's disk.
- **py-bench** gives the bench figure beside it, once Q1's 1A image rebuild
  gives the bench its binaries. Until then, it is taken in a
  container with PostgreSQL 16, as R1 and the fixture probe did, and labelled
  so. The figure names the data directory's filesystem: overlay, tmpfs or the
  host's disk. R1 measured overlay and tmpfs, and the probe overlay only.

### 5.3 How

- **Timing.** `time.monotonic_ns()` brackets each subprocess, exec to exit,
  except M2, which is `\timing` inside one session, as R1 did.
- **Cold, then warm.**
  - The first call after the cluster's start, or after the service's start
    for M5, is recorded on its own as "cold". The page cache is not dropped,
    since that needs root. So "cold" means a cold process and a cold server,
    not a cold disk.
  - Every later call is warm, and none is discarded.
- **Repetitions.**
  - M1 and M2: at least 200 writes (R1).
  - M5 and M7: 20 warm runs and one cold (the QA seat).
  - M9: 20 runs, each on a fresh estate with a local bare origin, as the
    measurements took it.
  - M10 and M11: 30 warm runs and the first; M10 on the real register's file
    copy, M11 on lane 2's register checkout (34,144 commits, 30,006 of them
    first-parent).
  - M4: 50 (R1, the probe).
  - M3: 10 cycles. The p95 of 10 samples is their maximum, which is said
    beside it.
- **Statistics.** Median and p95 by nearest rank, then min–max and n.
- **Correctness alongside.** Every timed write must have committed: the
  journal grows by exactly n, and each `write_id` appears once. A measurement
  that wrote wrongly fails, whatever its time.

### 5.4 Where each figure is recorded

The proposal's recommendation (Q10; #165's body) is a recorded number in the
PR body, not a CI assertion on a shared runner. This plan follows it, as
follows.

- **The record.**
  - Test 9's and test 10's measurement cases run in the `tests` job.
  - They assert correctness only.
  - They write their table to the job's log, and to `$GITHUB_STEP_SUMMARY`
    where that is set, so every CI run shows the figures.
  - The PR body copies the table from a named CI run, with its run id, and the
    py-bench table beside it. Each row names the host, the store, the path
    and the statistic.
- **The 50 ms.** It is recorded, and the test prints a warning when the median
  exceeds it, without failing. Whether act 4 waits for the service (Q29
  (a), Brett's answer) is decided by reading the recorded figure against a
  time limit he has yet to name.
- **What each choice costs.**
  - **A CI assertion** catches a regression with no reader. But a shared
    runner's neighbours move the figure, R1 found 54–58 min jobs whose host
    the suite does not control, and a threshold set from one run is a guess.
    So it would turn the gate red for reasons outside the change.
  - **A PR-body record** never turns CI red falsely. But a later regression is
    seen only by someone who reads the figures.
  - **The step summary** puts the figure in every run at no cost to the gate.
    It is the middle course this plan takes.
  - **The measurement's own cost** is about 2–3 min added to a 54–58 min job
    (tests 9 and 10). Whether the full set runs on every trigger or only the
    nightly one is open point 9.

### 5.5 The baseline

R1's figures (py-bench, PostgreSQL 16.15 inside a `postgres:16` container,
median with p95 in brackets) and the fixture probe's (an `ubuntu24` runner
image with PostgreSQL 16.15, no network, overlay):

| Cost | R1 | The fixture probe |
|---|---|---|
| a write, new `psql`, fast settings | 14.7 ms (22.8) overlay; 15.5 (23.5) tmpfs | not measured |
| a write, new `psql`, durable | 19.0 ms (28.5) overlay | not measured |
| a write with `-f` and `-v` variables | 16.4 ms fast; 20.4 durable | not measured |
| a write in a session, fast | 0.97 ms (1.96) overlay; 0.74 (1.13) tmpfs | not measured |
| a write in a session, durable | 2.71 ms (3.99) overlay | not measured |
| a new `psql` running `SELECT 1` | 9.3 ms (11.5) overlay | 6.8 ms (9.0) |
| the cluster per session | 1309 ms (1627) with `pg_ctl`, overlay | 878 ms (1108) with `Popen` |
| start to ready | 116 ms with `pg_ctl -w` | 30.0 ms with the pid-file poll (37 ms inside the session fixture) |
| a database per test | 62.0 ms (77.6) WAL_LOG; 53.4 FILE_COPY; 89.5 from a new `psql`, overlay | 56 ms to create and 21–24 ms to drop, about 80 ms |
| a fake `psql` call | 4.88 ms (7.43) | – |
| two SERIALIZABLE writers on one row | the loser blocked about 1017 ms, then 40001, `psql` exit 3 | the loser exited 3 with 40001; the test took 0.04–0.05 s with no sleep |

The probes add these:
- the burst drains (section 4.1);
- the timeout figures (section 4.4);
- a `git cat-file` check, 1.78 ms;
- a fetch or `ls-remote` against a local origin, 9–18 ms.

The measurements of 2026-11-02 (lane 2, #165 comments 6579356984 and
6579627366; py-bench at a load average of 10–19 on 32 CPUs, the code at
`2a057a6d`, the real register's copy; "first" is the first process in a fresh
estate, not a cold disk):

| Cost | Figure |
|---|---|
| M9, the bind in files mode today, against a local bare origin | 10.47 s median, 12.40 s p95; 87 % of it is not git; the guard's `set-row-state` 0.83 s median |
| M9, the bind and `set-row-state` on primary, estimated from measured parts | 9.64 s median, 11.51 s p95, 7.72 s at best; about 12 s with the costs it leaves out |
| one SERIALIZABLE write, a new `psql` on PostgreSQL 16.15's Unix socket | 52.5 ms median, 68.7 ms p95, 53.3 ms first |
| `lanes-index`'s start | 76.8 ms median, 143.7 ms p95 |
| M10, the parse alone, on 2,590,927 bytes and 73 lanes | 92.9 ms median, 156.6 ms p95, 99.2 ms first |
| M10, today's six register reads through git | 307.8 ms median, 434.9 ms p95, 422.2 ms first |
| Q29, the whole guard in the budget's four parts | files mode today about 0.55–0.60 s warm, 0.9–1.0 s with a cold record walk; on primary, through the prefetch, about 0.42–0.47 s warm. Part 1 measured at 112 ms median; parts 2 and 3 (tmux, the record walk) are lane 5's figures |
| M11, the `Register-Seq` lookup, bounded, on 30,006 first-parent commits with a commit-graph | 13.2 ms median, 16.1 ms p95, 14.4 ms first; the `--grep` form A 300.0 ms median and 392.1 ms p95, the unbounded form B 302.9 ms and 411.5 ms |

Not measured anywhere yet:
- today's files-mode write through git, which R1 skipped (M7 gives it);
- a durable write on a host's own disk;
- GitHub's runner itself;
- the guard's parts 2 and 3, the `tmux` read and the record walk, on this
  plan's hosts (M5b; lane 5's figures stand in);
- the bind on primary as code runs it (M9 above is an estimate).

## 6. Coverage

### 6.1 By the proposal's requirements

| Requirement | Tests |
|---|---|
| S1, the configuration and the switch (P147–188) | 3; 2 (switch not `primary`); 1 (the mode that wrote a row last); 14 (`mode`, D3; SQLite refused); P8 (the flip) |
| S1, one transaction per write and exit 69 (P190–295) | 1; 2; 4 (40001, busy 75, the statement order); 12; D8; D9 (writers' rows); AT-F3 (with LR008); AT-LR006; AT-F33; AT-F5 (the expectations); AT-F14 |
| S2, the journal and the renderer (P297–413) | 1; 5; 6; C8; D9 (the renderer's row) |
| S3, readers fail closed (P415–493) | C5; P3; 10; 11; D9 (the reader's row) |
| S4, the managed seam (P495–514) | 1 (the storage-layer entry); C5 (`managed-projection`: 0, 1 and 8 from SQL, and 1 when unreachable); the existing verbatim-block test against `3c26041`, kept |
| S5, import and rollback (P516–598) | 7; 8; 13; D22; section 4.2 |
| The operator tools (P600–627) | P8; 14 (the readiness item) |
| S6, claim order (P629–679) | 4; C10; section 4.1; 1 (UTC) |
| The roles, clause (b) (P681–693) | R |
| The service and the guard (the proposal's "The register service, and the guard as its client") | 10; P3; D20; D9; AT-F6; AT-F7; section 4.4; 9 and section 5 (M5, M9 to M11) |
| Round 1's 2A: `lane-start` and `lane-handoff` (P1558–1631) | 11; C5 |
| Round 1's 1A: the refusal of `mode=primary` (P1404–1418) | 14; P8 |
| Round 1's 3A: `mode=index` until the flip (Q20(B)) | P8 (an early `mode=primary` is a fault); 13 |
| Round 1's 4A: writes with the service down (Q2) | 2 (`test_a_write_goes_on_with_the_service_down`); 10; R |
| Q1's 2A: CI refuses (P1139–1144) | section 3.3's five tests |

### 6.2 By the delta specs' requirements, at commit I

These tables are written against the delta specs at lane 3's commit I,
`2be70f5a80cec110ee1a2a5c6910642d1854ef42` (comment 6711028142): 83
requirements, 65 in the sql-register delta and 18 in the
register-service delta, by name and in commit I's order. A † marks a
requirement whose text changed between `1c45123a` and `21f0191c`: 13 of
the 82 then, 10 in the sql-register delta and 3 in the register-service
delta, none renamed. A ‡ marks one whose text changed between
`21f0191c` and commit I: 11 of the 83, 9 in the sql-register delta and 2
in the register-service delta, one of them added ("`lane-start`'s bind is
one write") and none renamed or removed. Each row's tests are written
against the text at commit I. 75 of the 83 have tests; eight have none or
only a partial one (below).

`specs/sql-register/spec.md`, 65 requirements:

| Requirement | Tests |
|---|---|
| `mode=` selects the register's store | 14 (`mode`, 78 for Q6(c), D3; SQLite refused); P8 (the flip); AT-F8 (every failure of the mode query) |
| The estate switch is a database row and the marker `lanes/STORE` | 2; 7 (the cutover, D21); 8; AT-SET (the marker's states); AT-F2 (the operator entries) |
| A writer that is not `mode=primary` refuses under the marker | 3; AT-F8 (a failed mode query, refused on a SQL estate) |
| Nothing changes where the marker is absent ‡ | the existing files-mode suite, unchanged but for commit I's two exceptions, both in AT-F6: answer 2's bind detached in every mode, files mode included; and `lane-start`'s bind as one `start-record` write, one register commit and one push in files mode; 14 (D3: no configuration, no `lanes-index` call) |
| Every row records the mode that wrote it last | 1 |
| Readers on a workstation that is not `mode=primary` once `lanes/STORE` exists | 3 (`test_an_unconfigured_workstations_acts_refuse_and_listings_read_the_mirror`; the refusal names the ref's age; a marker on the read ref alone) |
| A build without the renderer's git half refuses `mode=primary` † | 14; P8 |
| A server-side gate protects the rendered paths and `lanes/STORE` ‡ | 5 (D23: the deploy key's push to an explicit SSH URL, in its own environment); 6 (a pre-receive hook as a model); AT-F4 (the model's order in the drill, with `--hold-marker`). The ruleset itself: **not covered** (section 6.3) |
| `wip init` and the `LANES.md` link on a SQL estate | 3 (`wip init`). The link: **not covered** |
| Every register write is one transaction ‡ | 1; 2 (the partial failure); AT-LR5 (the head rolled back); AT-LR4; AT-F3 (LR008 rolls back); the FP shape tests; AT-F6 (`test_the_bind_is_one_register_write_and_refuses_a_changed_row`) |
| Direct writes to the files refuse on `mode=primary` | 2 (`test_each_direct_write_refuses_with_2_on_primary`, the queued `lanes/` commit, the queued handoff) |
| Unreachable or refused is exit 69 with nothing written | 2; AT-VER (LR003); AT-LR4 (LR004 twice) |
| A serialization failure is retried, not refused | 4 (e); AT-F14 (output once per write); the FP retry shape |
| A lost race is not an error on the sequence key | 4 (c); section 4.1; the FP shape (D6) |
| A write that cannot get its turn reports "register busy" with a code of its own | 4 (d); section 4.1's companion; C5; AT-F3 (never for a write in doubt; LR008 is a lost turn) |
| A COMMIT whose outcome the writer cannot learn is recovered by its write id † | 12; D8; section 4.3; AT-F3 (with LR008); AT-F10; AT-F18 (another generation); AT-LR006; AT-F33 |
| Handoffs stay git files written by the lane | 11; 2 (the queued handoff) |
| `lane-start`'s bind is one write ‡ | AT-F6 (one register commit and push in files mode and one journal entry on primary; two sessions on one lane in both modes; a refused or in-doubt bind's `bind-failed` record); 11 (a resume's note, stamp and launch, and a new lane's death with the code, on `start-record`'s 75, 3 or 69); AT-F5 (`start-record`'s union expectation) |
| Every database path is bounded | D9; section 4.4; 10; AT-F6 (answer 2, detached, inside the budget); AT-F7 (the 3.65 s cap under a declaration); AT-F8 (the mode query's hang) |
| `rename-lane` commits the transaction before moving the handoff | 1 (`test_rename_lane_commits_the_transaction_then_a_rerun_makes_only_the_move`, and the `rename-lane` case) |
| A claim's comment cites the register sequence | 4 (`test_claim_prints_its_register_seq`) |
| The journal records each write † ‡ | 1; 5; 12 (`lanes-index journal --write-id`); AT-F10 (`args_sha256`); AT-LR4 (the operator flag); AT-F1 (exactly two operator entries); AT-F2; AT-SET (no kind commits in a state its row refuses); AT-F18; AT-SET (an operator entry writing outside `lanes/STORE`, refused with LR004) |
| UTC comes from the database's clock in whole seconds | 1 (with `test_a_caller_composed_utc_and_log_paused_utc_stay_as_written`) |
| The renderer commits one mirror commit per sequence † ‡ | 5 (the trailer in each message shape; 64 seqs a pass); 1 (part G, the trailer in the last paragraph); AT-F2 (operator entries skipped); 5 (no `Register-Seq` found: nothing pushed, and why recorded) |
| The renderer commits in the writing lane's checkout under S3's fetch | 5; C8 |
| The mirror is write-behind | 5 (held and failed renders) |
| A renderer that loses a push race discards and re-renders | 5 |
| The renderer leaves nothing on a lane's HEAD | 5 (the kill); 1 (part G) |
| One render at a time per checkout, and no fork waits ‡ | 5 (the `flock`, and [L4-4]'s kill); 5 (a write that commits as the render lets go) |
| The renderer does not inherit `LANES_NO_FETCH=1` | C8 |
| A pending hand edit has a way out on `mode=primary` | 2 (`test_a_pending_hand_edit_names_the_file_and_a_recovery_that_works`, with the shim's log empty) |
| Commits that touch none of the rendered paths are part of the tip | 6 (the negative cases) |
| A commit the renderer did not make to a rendered path stops it | 6; AT-MIR (the row never gates alone) |
| Each way out of a divergence has a tool | 6; AT-MIR (only the two ways out clear the row); AT-F1 (needed before a rollback) |
| `status` and `reconcile` prove the mirror | 5; 6; AT-MIR |
| The post-write nudge does not re-index the mirror into its own source | 1 (`test_on_primary_a_write_does_not_nudge_a_register_sync` and `test_sync_source_register_does_nothing_on_primary_and_says_so`, FP) |
| Readers read the database and fail closed ‡ | C5; 10; D9; AT-F5 (an act's deciding reads hold at its commit); AT-F5 (an act's own earlier write never refuses its later one) |
| Callers' own reads of the register go through verbs | C5 (`lane-worktrees`' three reads) |
| Reads that decide nothing may read the mirror | C5; 11 |
| A reader says which store answered | C5 (the `read: mirror` line; `--index` on primary) |
| `managed-projection` answers from the database and never says `legacy` for an outage | C5 |
| `lane-start` with the database unreachable ‡ | 11; C5 |
| The busy code refuses the launch at `lane-start`'s five default arms | C5 (seven arms, lane 4's row 23; the requirement's name says five) |
| `lane-handoff` and the handoff skill with the database unreachable | 11 |
| The managed seam stays the ledger's | 1 (the storage-layer entry); the existing verbatim-block test |
| `lanes-index import --at` proves the render before it loads | 7 |
| The import takes origin only and keeps the unreadable lines verbatim | 7 |
| The cutover is one guarded sequence † | 7 (part G); 13; AT-F2 (the abort, the unconfirmed push); AT-F4 (the order); AT-SET (a write before the push is confirmed); AT-D22 (the restore form's abort) |
| Rollback returns the estate to the files † | 8 (a); 13; AT-SET; AT-F1; AT-F4 (`--hold-marker`) |
| A rollback needs the database | 8 (b); section 4.2; D22; AT-F18 (the disaster path) |
| A writer the rollback missed cannot write | 8 (c); section 4.2; AT-SET (an older snapshot fails 40001, then 69) |
| The operator's bootstrap is idempotent and holds no secret † | P8 (a connection string refused with 2; `PGSERVICE` only in the environment); AT-VER |
| The readiness check proves an environment before the cutover † | P8 (every check, the schema's window among them); 14; AT-VER |
| The `mode=primary` flip is a command run once per host † | P8 |
| Claim order is commit order | 4; C10; D19; AT-SPL; AT-LR5; section 4.1 |
| `HOLD` and `LANDING` are ordered by commit sequence † | 4 (b); AT-LR5 (one repository under two spellings, `HOLD` or `LANDING`, either order) |
| Which lines take and release `main` | 4 (`test_each_line_kind_takes_or_releases_main_as_q17_b_says`; case (f)) |
| The three roles | R; AT-MIR (`record_mirror_state`'s EXECUTE) |
| The REGISTER-WRITER secret's reach is stated plainly | **not covered**: a statement in the manual, which no test reads |
| Agreement tests run on a real engine in CI | section 3.3's five tests; the fixture |
| The cost of a write is measured and recorded | 9; section 5 |
| A lane's day on `mode=primary` is tested end to end | 11 |
| The drill covers the outage and the way back | 13 |
| The manual says what changes for a lane | **not covered**: documentation |
| The runbook for acts 3 and 4 and the rollback | 13 is its drill, with AT-F4's order. The runbook's text: **not covered** |

`specs/register-service/spec.md`, 18 requirements:

| Requirement | Tests |
|---|---|
| The service serves the reads and the guard, and writes do not depend on it | 10; 2 (`test_a_write_goes_on_with_the_service_down`) |
| Every answer that decides state is read fresh | 10 (the fresh write; never a cached `ok`) |
| The service reaches the database only through `psql` | 10 (`test_the_service_reaches_the_database_only_through_psql`, FP) |
| The service starts on demand with no supervisor of its own | 10 (`test_the_first_call_starts_the_service_with_no_supervisor`, `test_a_service_that_cannot_start_falls_back_to_the_direct_read`) |
| New code keeps to the toolchain floor | the existing hygiene suite (bash 3.2's rules, Python 3.9 and the standard library); partial, since nothing runs on 3.9 but the `package` job |
| The `UserPromptSubmit` guard is the service's client † ‡ | 10 (the request carries only file names); AT-F6 (answer 2, detached) |
| The `CLAUDE_NO_LANE=1` exception is kept | 10; P3 |
| The guard's answers through the service equal the direct read | 10 |
| The `SessionStart` block reads the mirror and only starts the service | 10 (`test_the_session_start_block_reads_the_mirror_and_only_starts_the_service`); C5 |
| The guard falls back to the database, never the mirror | 10; AT-F7 (the declared-outage exception) |
| A service that accepts and never answers counts as unavailable | 10; D9 (the service's 300 ms wait) |
| The 5 s is one budget, spent once † ‡ | section 4.4; D9 (the guard's row); 10; AT-F6 (answer 2, detached); AT-F7 |
| The hook's environment carries no credential | 10 |
| The guard fails closed during a database outage | P3; 10 |
| A declared outage lets the guard answer from the mirror † | D20 (with the rollback's start); 10; AT-F7; AT-F11 (no `Register-Seq` within the bound) |
| The service answers the guard's read fast, and the cost is recorded | 10; section 5 (M5, M5b, M10) |
| The socket API is documented | **not covered**: documentation |
| The cutover waits for the service only if the guard needs it | **not covered** by a test: Brett decides, from section 5's recorded figure |

Eight requirements have no test, or only a partial one:
- in the register spec: the server-side gate's ruleset (modelled only); the
  `LANES.md` link (`wip init` is tested); the secret's reach; the manual; the
  runbook's text (its drill is test 13);
- in the service spec: the toolchain floor on Python 3.9 (partial); the socket
  API's documentation; the cutover's wait for the service.

Five of them are documentation or a decision of Brett's, which a test does not
check. The service's start on demand is test 10's, since SVC-4 says how the
service starts.

### 6.3 Gaps

- **The ruleset** (Q21(b), D23) is GitHub's, on paths Brett is still to
  name: `lanes/**`, as the round-2 file words his answer, or the render set
  and `lanes/STORE`, as D23 has it (lane 3, comment 6711028142). The suite
  reaches no network, so only a pre-receive hook models it (test 6).
- **The `LANES.md` link** that `link-estates` places (Q14's second half). No
  test.
- **Three frame points with no test of their own,** none asked for by the
  frame: D10's "exit 9's re-check stays local" and "local sidecar reads keep
  1 and 10", and D12's deciding reads, unchanged by `LANES_NO_FETCH`. D16's
  "the row never gates alone" is AT-MIR's.
- **TCP across hosts.** Every case runs on one host. The cluster listens on
  its Unix socket only, and only the timeout stand-ins use the loopback
  address. The probes did not measure across hosts either.
- **Which cases need TCP rather than the Unix socket** (the frame review's
  item routed to lane 2): only AT-D9's F1 and F2 stand-ins, section 4.4's
  cases 1, 2 and 4, and AT-F7's silent host, which reuses F2's relay. Each
  binds 127.0.0.1 on port 0. Every other case uses the cluster's socket.
- **F13's `LANDING EXPIRED`.** §1.2 carries it, inert until Brett takes the
  release, and lane 1's runs exercised it. No test here until Brett rules;
  then one RE case: a `LANDING EXPIRED` for a stale `LANDING` frees `main` in
  `main_slots`, and LR005 refuses one naming a fresh `LANDING` or the wrong
  lane. The `LANDING` is found by `repo_key` alone, so one spelled the other
  way fails closed with LR005, as lane 3 ruled lane 1's point 5 (comment
  6710770304); `operations.md` tells the operator to spell it as the
  `LANDING` did. A stray landing during a rollback has no journaled way out,
  which lane 3 puts to Brett; no test is written for it.
- **GitHub itself.** Its runner is inferred from its package steps, not
  observed (the fixture probe).
- **macOS.** No CI job runs the suite there. `PR_SET_PDEATHSIG` is Linux-only,
  so only the sweep cleans up there. The socket arithmetic for macOS is
  assumed (the fixture probe).
- **Python 3.9.** The `package` job's Python. The fixture was only parsed
  under 3.9, and the RE tests run on the `tests` job's 3.x.
- **tmpfs, and a host's own disk.** The probe measured overlay only, and R1
  never a durable write on a host's own disk.
- **The service's start and its socket across containers** (Q2(c) to (h)).
  A one-host suite cannot show one process across containers. These wait for
  lane 5's section.
- **The shared database of act 3** (Q22). No test runs against it, by design
  (P840–841). The runbook's rehearsal into a scratch schema covers it.
- **The real register's bytes.** The fixture register is synthetic. R3's
  byte-identical import of the real register's 43 paths is the rehearsal's,
  not the suite's.
- **A cluster that will not start, with its binaries present,** was not
  driven through pytest (the fixture probe, d4).

## 7. Open points

**Settled.** The tests above are written to these answers:
- "Register busy" is 75, at a 30 s deadline (D7). Brett took Q7 (A″) with
  75, not "(A″) with 69" (round 2; lane 3, comment 6710653292).
- A write in doubt is 3, after 10 s of trying to reconnect, and a rerun
  reuses the id through `LANES_WRITE_ID` or the pending record (D8). At the
  deadline, F3's one lookup, `find_write`, gives 0, 75 or 3 (§1.3).
- A refused direct write is 2, and so is the later `HOLD` or `LANDING`
  writer; the register subcommands use 69 (D10, D18).
- A rollback refused with the database unreachable exits 69 (D10).
- The outage declaration is a file beside `lanes-index.conf` (D20), and a
  rollback's start writes one too (lane 3, comment 6579459518). Brett's
  Q26 (a′) has both (round 2; comment 6710653292).
- `OPENREPOTOOLS_REQUIRE_PG` is independent of `OPENREPOTOOLS_COMPOSED`, and
  is scoped per job where a job lacks the binaries (lane 3, comment
  6578688322).
- The guard's 4.5 s bound stays. If CI's runner misses it, the shares are
  cut, in SVC-8's budget (lane 3, comment 6578688322).
- The renderer's re-check starts no pass after a failed one: a pass that
  pushed nothing ends the renderer (F15; lane 3, comment 6578688322).
- `set_seq` after a cutover is the cutover's own seq: 1, `m+1` after a
  re-import, or `b+1` after a restore (§1.3; lane 3, comments 6578688322 and
  6579459518).
- Q6(c)'s code is 78, `EX_CONFIG` (lane 4, comment 6579524611; lane 3,
  6579562840).
- The renderer's lock is an `fcntl.flock`, in `SyncLock`'s form (lane 4,
  comment 6579524611; lane 3, 6579562840).
- Answer 2's bind runs detached, in every mode, files mode included, and
  exits 2 with "binding in progress" (ruling 16; lane 3, comments
  6579459518 and 6710770304). It is the fix of opensoft/openRepoTools#347,
  phase 1's first guard PR.
- The bootstrap takes the role names as inputs (§2.4), so a test's own roles
  could sit beside the session's.
- Test 2's late failure needs no helper in the database: an entry whose last
  structured row breaks a CHECK (§1.3).
- A writer's snapshot behind a lock is a failed read, 69 (the timeouts
  table's snapshot row).
- The restore form's abort puts back what its cutover replaced, recorded in
  `register_switch.restore_prior`, and never sets `files` (lane 1's v2.3,
  comment 6708990159; lane 3's ruling 6579459518, point 1).
- LR008: a commit call past its `not_after` writes nothing (lane 1's v2.3;
  lane 4's row 15, which it closes). Inside D7's deadline it is a lost
  turn, a fresh snapshot and a new attempt under the same write id; at the
  deadline F3's rule applies; by itself it is never 3 or 69 (lane 3,
  comment 6710770304).
- Answer 2 is three writer calls, the bind one `start-record` transaction;
  F5's expectations are `act-begin` and six scopes; the renderer finds `m`
  by form C, paged, and pushes nothing when it finds none (lane 4's
  section; lane 3, comment 6708889286). `start-record` exists in both
  modes: in files mode its three register commits and pushes become one
  (lane 3, comment 6710770304).
- Rounds 1 and 2 of #165's questions are Brett's: round 1 confirmed, and
  round 2 taken as recommended in every question (lane 3, comment
  6710653292; section 1.4, which lists what stays his to name).
- Lane 3's rulings of comment 6710770304: lane 1's five points (open point
  17); lane 4's corrected `start-record` table (open point 18); a D9 row
  for the mode query, 2 s (open point 19; its value in comment
  6711028142); LR008's reading; the LR006 rewording
  in S1's step 1; and lane 4's rows 6, 8, 10, 12, 13, 15, 16, 20, 24 and
  37. Rows 18, 22 and 40 go to Brett with the spec PR.

**Still open.** Each is a decision the tests need, and the proposal and the
frame do not yet make it. None adds a requirement.

1. **Exit codes still unnamed:** that of `import --at`'s refusal, and that of
   the cutover's abort. The tests read the design's constant.
2. **The `psql` seam.** Under D2, `lanes-index` is the one client, and
   reaches `psql` through `PATH` (`code/lanes-index:1272–1276`). Test 4's
   window, test 12's shim and test C10's hold all depend on that lookup, with
   the shim's directory ahead of the binaries the fixture puts first. The
   design should keep the lookup by `PATH`, or name a seam a test may set.
3. **How a test builds test 14's two states,** and how phase-W tests run on
   the flipped build.
   - Proposed: copy `lanes-index` and rewrite the constant in the copy.
     AT-VER's version-window copies are made the same way.
   - There should be no environment override, which would be a production
     bypass of the refusal.
   - The design names the constant.
4. **A fixed clock for test 1's twin.** Under D13, `utc_now` answers the
   database's clock at the snapshot, while the files-mode twin uses
   `date -u`. Unless the snapshot reads the time through one SQL function a
   test database may redefine, the twin masks the UTC fields.
5. **Test 6's v1.1.0 `lanes-edit.sh`.** CI checks out at depth 1 with no tags
   (pinned by `test_tests_workflow.py`). The choice:
   - a digest-pinned copy under `code/tests/fixtures/`, about the size of
     `lanes-edit.sh`;
   - or a stand-in stray commit, which does not prove the real old writer.
6. **Authentication.** The fixture uses trust over a 0700 directory, so only
   FP tests exercise the password-file path. Should one RE test run a
   database under `scram-sha-256`, through a `pg_hba.conf` line, to prove that
   path end to end?
7. **The `package` job.** The eager switch already turns it red on a missing
   engine. Should it also run one RE test against the built wheel's
   `lanes-index`? That would be a fourth module there, which changes
   `contracts/workflows.md` and a pinned step.
8. **Waiting for a forked render.** The render is a detached fork. Right
   after the verb returns, the renderer may not yet have started, so a free
   lock proves nothing alone. Every test uses one of two waits, and no other:
   - **Where the lag is expected to reach 0,** a poll of `lanes-index status`
     every 100 ms until its lag is 0, with a 30 s ceiling.
   - **Where the lag is expected to stay above 0** (test 5's kill and failed
     render, test 6's stop, test D22's lag), a wait for the renderer's exit.
     Test 5's `git` wrapper logs, at each call, the pid of the
     `lanes-index render` process above it and that process's start time as
     `ps -o lstart=` reports it. The test waits, with a 30 s ceiling, first
     for the wrapper to log a render's call made after the verb started
     (every pass begins with a fetch), then for every logged renderer pid to
     exit: through `os.pidfd_open` where it exists (Linux 5.3 and later), and
     elsewhere, as on a Mac with `PG_BIN`, by polling `os.kill(pid, 0)`
     beside the recorded start time. A pid whose start time no longer
     matches counts as exited. Then it reads `status` once.
   - **The lock, as a secondary check.** After either wait, a test that next
     takes the checkout checks that the renderer's lock is free: a
     non-blocking `fcntl.flock` on `lanes-render.lock` succeeds, under the
     same ceiling. With the `flock` form a killed renderer releases its lock,
     so no lock directory is ever left to wait on (D14).

   Every pass begins with its fetch of `origin main` (lane 4's section, "A
   pass"), so the exit wait always has a pid to find.
9. **The cost in CI.** Tests 9, 10 and D9 add about 4–6 min to a 54–58 min
   job. Should the long rows run on every trigger, or only on the `schedule`
   trigger `tests.yml` already has? And may a test read the trigger
   (`GITHUB_EVENT_NAME`) to choose?
10. **Under Q4(b)(iv) only,** where "the mirror was last shown current" is
    recorded. Settled: Brett took Q4(b) (iii), so no test needs the record
    (round 2; lane 3, comment 6710653292).
11. **"An address whose connect never completes"** (P823) cannot be made on
    loopback without root, since the probes' attempt to drop SYNs failed. The
    never-speaking listener stands for it. An off-host documentation address
    would break the root `AGENTS.md`'s "reaches no network".
12. **The fixture probe's leftovers.**
    - The exact major, 16, against R1's 14 or later. `ubuntu-26.04` ships
      PostgreSQL 18.
    - Printing the binaries found on every CI run. The prototype prints them
      only on the skip path or under `-v`.
    - A cluster that will not start with its binaries present, which was
      never driven through pytest.
    - GitHub's own runner, Python 3.9, macOS and tmpfs, all unobserved.
    - Whether a clone's `pg_database.datacl` is empty, as section 3.1
      expects from PostgreSQL's `createdb()`: the fixture's first run prints
      it.
13. **A `stoppable_cluster` per test** costs about 0.9 s fresh. Copying a
    pre-made data directory was not probed. Tests 8, 10, 11, 13, P3, P8 and
    D22 use one, and so does the durable figure.
14. **Test C5's one-call failure:** every k, which is a few dozen
    `lane-start` runs at 30–60 s, or a sample of them.
15. **The disaster path's backup.** The frame restores a backup "only where it
    is newer than the mirror". It names neither the tool (`pg_dump` and
    `pg_restore`, or a copy of the files) nor how `b` is read from it.
    `pg_dump` is not among the fixture's four required tools.
16. **Round 1 is provisional.** Settled: Brett confirmed round 1 at
    2026-11-07T15:26:24Z (lane 3, comment 6710653292). Tests 11, 14, C5 and
    P8's readiness items are written under its answers (2A, 1A and 3A), and
    no entry carries another option for them now.
17. **Lane 1's five points of v2.3 for lane 3** (comment 6708990159).
    Settled by lane 3's rulings (comment 6710770304), and the
    `[L3-PENDING: n]` marks are gone:
    1. a `cutover` or `rollback` entry that writes rows outside
       `lanes/STORE` is refused with LR004 at its commit, and never stays
       for an abort to meet with 23503 (AT-SET);
    2. the re-import form's abort keeps the label and generation its
       cutover set, so a pending record or printed rerun made against the
       lost database still refuses with LR007 (AT-SET);
    3. `main_slots`'s pairing by spellings is accepted, with the agreement
       tests against files mode's Rule 6 on imported history; a difference
       is a finding before the cutover (AT-7);
    4. `not_after` stays on every kind, the operator entries included
       (AT-LR4);
    5. F13's `LANDING` is found by `repo_key` alone, so a `LANDING EXPIRED`
       spelled the other way fails closed with LR005 (section 6.3).
18. **`start-record`'s refusal against round 1's 2A.** Settled by lane 4's
    correction (comment 6709560550) and lane 3's ruling (comment
    6710770304), which makes lane 4's corrected table the rule. Test 11
    asserts it: a resume's 69, 75 or 3 is a note, the Rule 3 stamp and a
    launch; a new lane's dies with the code. AT-F6 asserts that the guard's
    child records `bind-failed` with the code, and AT-F5 and AT-F6 hold the
    expectation's 2, the one code that stops both paths.
19. **The mode query's timer.** D3 and the sql-register spec's "The mode
    query fails" name a timeout, and lane 4's failure table a client timer
    that ends a hang in files mode, but the timeouts table (D9) has no row
    for `lanes-index mode`. Settled by lane 3's 6710770304 and 6711028142:
    D9 gains a row for it, with a 2 s client timer and no connect, lock or
    statement timeout, since the query reads only the configuration. On
    expiry the query is killed, and the verb runs in files mode with a
    warning naming the timeout; on a SQL estate the marker check then
    refuses with 69. AT-D9's table carries the row, and AT-F8's hang case
    asserts it.
20. **Test 3's G form.** Lane 4's row 8, as lane 3 ruled it (comment
    6710770304: "agreement test 3's unit form ships in phase 2"), leaves the
    agreement form, with the marker pushed by `cutover`, in phase G. AT-3
    has no case for it yet. Lane 3 says whether one is wanted.

**What the tests still wait on,** each marked where a test rests on it.
Lane 3 settled the `[L3-PENDING: n]` marks (open point 17) and the two
`[L4-OPEN: row n]` marks (comment 6710770304). Row 20 is accepted: its
columns come with lane 1's v2.4, folded as commit K, and their tests and
LR009's come in the pass after lane 3's review (AT-F5). Row 24 is ruled
(AT-D8). Three rows stay, and go to Brett with the spec PR:
- `[BRETT: row 18]`: `rename-lane`'s handoff move and stamp as its
  after-COMMIT tail, beside Q13 (AT-1);
- `[BRETT: row 22]`: the three conditions under which every act carries its
  expectations (AT-F5);
- `[BRETT: row 40]`: a host without `setsid` (AT-F6).
