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

**Since `21f0191c`.**
- Brett answered rounds 1 and 2 of #165's questions at 2026-11-07T15:26:24Z:
  round 1 confirmed, and round 2 taken as recommended in every question
  (lane 3, comment 6710653292; section 1.4).
- Lane 3 ruled on lane 1's five points of v2.3, this plan's open points 17
  to 19, LR008's reading and lane 4's open rows (comment 6710770304), which
  settled every L3-PENDING and L4-OPEN mark this plan had carried. It named
  open point 19's value (comment 6711028142), and ruled that the commit call
  runs with `VERBOSITY=verbose` and `SHOW_CONTEXT=never` (comment
  6711564365).
- Lane 3 folded this plan's v3e, byte for byte, at commit J2 (`2ff4612b`).
  Its reviewer R3 read it and found one MUST, eight SHOULDs and seven NITs,
  and its fold report lists every test the design routes to lane 2 and
  eleven further gaps. This version, v3f, answers both and is keyed to
  commit W, below.
- On 2026-11-08 Brett ruled on the design's departures A, B and C (comment
  6721370130), on the ten that had not been routed (comment 6721583757) and
  on the values the design left to him: Q22's host, backups and downtime,
  and Q29's limit (comment 6722025863). Section 1.4 records each.
- Lane 3 then folded commit W, `bb08d0803dcad66052d5e275d9d75f431063b73c`,
  on V. W records the values Brett named on 2026-11-08: Q22's host and its
  access, backups and downtime limit, two logins per engineer, Q28's dates
  and Q29's statistic. It adds the two requirements for the release of a
  stale `LANDING` and its verb, `append-line --landing-expired`, with lane
  1's v3.1 refusals and lane 3's U3 and U7 (comment 6722541787); the
  `lanes-index login` command and `ready`'s checks 11 to 13; and lane 4's
  and lane 5's texts for a remote database. This version, v3f, is keyed to
  W, and section 1.4 records what W carries.

**Which pin applies to what.** The frame above, the proposal's `P<n>`
(pinned to `proposal.md` at `21f0191c`, as "How it cites" says) and every
design and operations section the tests cite by name stay at `21f0191c`, and
each cited name was checked in commit I too (lane 2, 2026-11-07) and in W
(lane 2, 2026-11-08). W renamed no heading of `design.md`, `operations.md`,
`proposal.md` or `clarifications.md`; where V renamed one, an entry cites
V's name, which W keeps. This plan is keyed to lane 3's commit W,
`bb08d0803dcad66052d5e275d9d75f431063b73c` on `add-sql-register`, and
section 6's coverage tables are pinned to it. W follows V,
`8b5d5bb79b09d7dcaecb0c369a736028e564618e`, which records Brett's rulings of
2026-11-08 on the ten departures (comment 6721583757), and V follows J2
(`2ff4612b`), which folded this plan's v3e. W's files:
- `design.md`, 6,636 lines, sha256
  `76d6a4a030e0ddc4b26a663cb963c87c638f244a00ef777d2d6ffe16340d32c9`;
- `operations.md`, 679 lines, sha256
  `f0f5ff81e0eddc3f2989b8f3174646e70fb8d06939218bce889b4cae9ecc2097`;
- `proposal.md`, 2,259 lines, sha256
  `a948f98b511fff8e58b461dcd974553b8aa2619ce582d15ffa482e6632871ac8`;
- the sql-register delta, 1,107 lines, sha256
  `a80dbfc5587d26b99af87961ec57c3be2b5ac3748de9fa06a0637178741d5027`;
- the register-service delta, 293 lines, sha256
  `11a6ee5886c11e6fab2b549009107296a392b865787fe83770784d3d6988cffd`.

`C<n>` is a line of `clarifications.md` at `1c45123a` (414 lines, sha256
`9e78a943c811f0ed59b4cb9390374b5d5d1ddf36303a97c98abccb85f199a067`). At W
the file, sha256
`9cc50ae616179625cd9c8f4a9b13e730bfe2e4c6920256cf97f387cbfcd756ff`, has 510
lines (483 at V, sha256
`4069c763b1a70bc84a7bd6183ccab01a4ef0d280fca831544d5a7abc3dbe56f4`): three
were inserted after its line 36 and its answers were appended after its line
414, so a `C<n>` from 37 to 414 is line `n + 3` at V and at W, which changed
only lines from V's line 459 on.
`council-review.md`, which `CR<n>` cites, is unchanged since `1c45123a`.

Lane 3 folded commits J to W on the same branch. Section 6.2 says which
requirements changed or were added since commit I, where the v3e plan had
pinned the tables, and, by its last mark, since V, and which tests this pass
added for them.

**How it cites.**
- `D<n>` is a decision of `design.md`, `SVC-<n>` a section of its register
  service, and an S-section or a name in quotes is `design.md`'s heading. A
  bare `§1.n` is a section of `design.md`'s "The schema", and a bare `§2.n`
  a section of `operations.md`. No citation of `design.md` or
  `operations.md` is by line.
- Lane 4's section is cited by its headings, as "lane 4's section, '…'", and
  its table of differences by row, as "lane 4's row n".
- The delta specs are cited by requirement name. Section 6.2's tables
  follow W's 88 requirements; every other citation of one is at
  `21f0191c`, except where the text names commit I, and each such name
  is the same at commit I.
- The proposal is cited by question, test or section where it can be, and
  otherwise as `P<n>`, a line of `proposal.md` at `21f0191c`. Each `P<n>` is
  pinned to `proposal.md` at `21f0191c`, and lane 3's later proposal commits
  may move those lines. `C<n>` cites `clarifications.md` as it was at
  `1c45123a`, which V and W changed (the pin paragraph gives the shift),
  and `CR<n>` cites `council-review.md`, unchanged since `1c45123a`.
- "R1" is lane 2's test-strategy report (#165 comment 6547987633), "the probes"
  lane 2's measured probes of 2026-10-31 (#165 comment 6549116835), "the
  measurements" lane 2's (14) and (16) of 2026-11-02 (#165 comment 6579356984),
  and "the code leg" opensoft/openRepoTools-code at `2a057a6d`, whose paths are
  written `code/…`.
- "The LR006 probe" is lane 2's probe of 2026-11-07 on PostgreSQL 16.15,
  `PROBE.md` (275 lines), at
  `/workspace/projects/.openrepotools-private-prep/work-20261022/lane2-165-lr006-1107/PROBE.md`,
  sha256 `6a822999178a7ee92ba577f3591cfa8ef3923fc07e7282fac95671d3d6d2034f`.
- "The fixture probe" is lane 2's probe of R1's fixture of 2026-11-02 on
  PostgreSQL 16.15, runs a1 to s7. Section 3's opening pins its report,
  its prototype and its `SHA256SUMS` by path and sha256.
- No mark is left. The BRETT marks labelled rows that lane 4's section
  put to Brett, and he settled rows 18, 22 and 40 on 2026-11-08 (lane 3,
  comment 6721583757). The L3-PENDING marks had labelled lane 1's five
  points of v2.3, and the L4-OPEN marks rows lane 4's section had left to
  lane 3 or lane 1; lane 3 settled both (comment 6710770304). Section 7
  lists all three.

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
runs the RE modules. A new test's name must not contain `guard_launch_mode`
or `repo_hygiene`, or the `guard-launch-mode` job would select it. Its
`-k` matches a substring of every part of a test's id, so the rule
covers the function's name, its class's, its module's and each
parametrize id, and a hygiene rule scans all four, case-insensitively,
its own test's name included (section 3.3).

**The timing rule.** CI asserts times in three ways only:
1. **The guard's 4.5 s** is asserted as stated.
2. **Every other upper bound the design states** (D9's timers, `bound_s`,
   D7's 30 s, the try windows, AT-F6 case 1's `T0 + 1 s`, AT-F8's "under
   3 s", and the like) is asserted at **twice its value**. This still
   catches a missing or wrong timer, because the faults the tests use would
   otherwise hang far longer. The measured time against the design's own
   bound is **recorded**, with its margin, in the test's output.
3. **Lower bounds and orderings** the design states ("not before", "after",
   "in this order") are asserted as stated.

Every other time is recorded, not asserted. Section 1.2, section 4's opening
and section 7's open point 34 state this rule in the same words, and every
test that asserts a time says which of the three it is.

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
| 2 | W; N for the storage-layer entry's case | RE and FP |
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
| C5, the callers' default arms | N | FP, and RE for `managed-projection`'s three cases |
| P3, the outage's ways out | N; the `rollback` case G | FP and RE |
| P8, the operator tools | N | RE, FP and FM |
| C8, `LANES_NO_FETCH=1` | G | RE |
| C10, the alias race | W | RE |
| D8, the pending record | W | RE |
| D9, the timeouts table | N; W for the writer's rows; G for `rollback`'s | RE |
| AT-D9c, D9 over a remote database | W; (e) is act 3's | FP and RE |
| D19, two spellings at once | W | RE |
| D20, the declared outage | N | FP and FM |
| D22, the disaster path | G | RE |
| AT-SET, AT-MIR, AT-SPL, AT-LR5, AT-VER, AT-LR4, AT-LR006, AT-F33, lane 1's objects | AT-MIR and AT-VER N, AT-MIR's gating case G; AT-SET, AT-SPL, AT-LR5, AT-LR4, AT-LR006 and AT-F33 W, but for AT-LR5's F13 and U3 cases, which are N | RE |
| AT-LR007, AT-LR010, AT-LR011 | N | RE |
| AT-LR009 | W | RE |
| AT-LRLABEL | N for the key; G for `cutover`'s refusals | FP and RE |
| AT-LRMPG | N; W for the logins' writes and reads | RE |
| AT-F1, AT-F2, AT-F4, AT-F18 | G | RE |
| AT-F3, AT-F5, AT-F10, AT-F14, AT-F34 | W | RE |
| AT-F6, AT-F7, AT-F11 | N, or W for AT-F6's writes | RE and FP |
| AT-F8 | W | FP and FM |
| AT-F13 | W, with (h)'s rollback in G | FP for (a) to (g) and (k) to (m); RE for (h) to (j) |

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
- **2026-11-08, the design's departures A, B and C, each taken as
  recommended** (lane 3, comment 6721370130; Brett answered at 12:08:48Z):
  - **A, files mode:** "Accept both (Recommended)". The guard's detached
    bind and `lane-start`'s `start-record` run in every mode, and AT-F6
    tests both in files mode. The delta's third exception, `wip init`, is
    his Q14 (a) (AT-3).
  - **B, exit 3:** "Accept exit 3 (Recommended)". D8's exit 3 for a write in
    doubt (test 12, AT-D8, AT-F3).
  - **C, the marker pushes:** "Accept (Recommended)". `cutover` and
    `rollback` push their own commits of `lanes/STORE` (AT-F2, test 7, test
    8).
- **2026-11-08, the ten departures not yet routed, each taken as
  recommended** (lane 3, comment 6721583757; asked at 12:28Z to 12:37Z):
  - **D5, one transaction per verb,** and **D5's three conditions** (lane
    4's row 22): `lane-start`'s re-read after a handoff wait, the skill's
    step 4 identity and `lane-end`'s gate inheriting `LANES_ACT` are now
    requirements (AT-F5). Row 22's BRETT mark is retired.
  - **`rename-lane` on primary** (row 18): a transaction plus a git commit,
    files mode unchanged (test 1). Row 18's BRETT mark is retired. **macOS
    without `setsid`** (row 40): the gap accepted (AT-F6). Row 40's BRETT
    mark is retired.
  - **D18,** the later `HOLD` or `LANDING` writer exits 2, writes nothing
    and prints the earlier line (test 4, AT-LR5); **D1,** a schema of its
    own; **D25,** `lanes-index cutover` (test 7, AT-F2).
  - **Q21(b)'s scope: "Render set + STORE",** as D23 has it, not `lanes/**`.
    AT-6's and AT-F4's pre-receive model guards exactly the render set and
    `lanes/STORE`. Whether the organisation's GitHub plan can name the
    render set is still a fact to find.
  - **A stale `LANDING`: "Adopt, on my word".** After Rule 6's window a lane
    writes `LANDING EXPIRED` naming the holder, only on Brett's word, and
    the design gives that line kind a verb. Commit W records the two
    requirements and the verb, `append-line --landing-expired`. The
    engine's half is tested in AT-LR5's F13 cases, and the verb's half in
    AT-F13 (section 6.3).
  - **A stray landing during a rollback:** the gap accepted as S5's
    remaining gap. No test (section 6.3).
- **2026-11-08, the values the design left to him** (lane 3, comment
  6722025863; two dialogs, 13:08Z to 13:13Z):
  - **Q22, the host:** "A separate server", then "Managed cloud Postgres".
    Brett then named the provider (lane 3, comment 6722593397): Azure
    Database for PostgreSQL Flexible Server, version 16, in opensoft's prod
    subscription. W records the rest, and the design's "Open questions"
    has each under "Values Brett named on 2026-11-08":
    - **Region:** expected to be Central US, where opensoft's prod
      platform runs. Act 3 confirms it when it creates the server.
    - **Access, TLS and the admin:** "Public + firewall (Recommended)",
      with one firewall rule per workstation address and the setting that
      allows access from any Azure service off; `sslmode=verify-full`, with
      `require_secure_transport` on. Microsoft Entra ID is an option only,
      and unused (lane 3, comment 6723413756).
    - **Logins:** "Two logins each (Recommended)" (comment 6723413756).
      Each engineer, human or AI host, has a writer login and a reader
      login, each with its own
      0600 password file on that engineer's workstation, and the dump job
      has a reader login of its own, `lanes_register_dump`. Brett had
      first answered "One login per engineer" (comment 6722593397).
    - **Scope:** every engineer's workstation, human or AI host. Act 3
      configures Eagle and Raven first, and each later workstation joins by
      the same steps, with its own logins, `c`, `register_addresses=` and
      `register_rtt_ms=`, and its own Q29 figure.
    - **`max_connections`:** the server's compute tier sets it. It must
      hold one service connection per environment of every engineer, beside
      the direct reads and the writers' connections. No test reads it.
    - **Backups:** "Daily pg_dump + mirror (Recommended)". A scheduled job
      in the prod subscription dumps both schemas into a storage container
      there, with soft delete and 30-day retention. The provider's own
      backups and point-in-time restore count as a restore source too
      ("Count both", comment 6722541787). If the subscription is lost, the
      GitHub mirror is the copy outside Azure ("Accept; mirror is the
      copy", comment 6723413756; D22).
    - **Downtime:** "4 hours (Recommended)", how long the database may be
      down before he chooses between waiting and the disaster path (D22),
      since a rollback needs the database.
    - **Commit X:** commit X moves the server to CNPG on the prod AKS
      cluster, and lane 3 routes its tests after X (#165 comment
      6723925474); v3f stays keyed to W, whose Azure texts stand.
  - **Q29, the guard's limit:** "1 s (Recommended)". Act 4 waits for the
    service when the guard, measured without it, is over 1 s (Q29 (a)). Lane
    3 ruled that it is judged on the warm p95 of the guard without the
    service, with the cold record walk recorded beside it and not judged
    against the limit (the design's "Open questions", "Values Brett named
    on 2026-11-08"; lane 5's suggestion, comment 6722703424). A managed
    cloud database changes the figure: every guard read crosses the
    internet, so lane 2's local figures, about 0.46 to 0.51 s on a Unix
    socket, are floors. Lane 2's figures on lane 5's netem run (SVC-9; lane 2,
    comment 6722518182, on lane 5's read times, comment 6722703424) put the
    direct guard at about 0.65 to 0.70 s at an RTT of 20 ms and 1.30 to 1.35 s
    at 100 ms, over 1 s above an RTT of about 57 to 63 ms (section 5.6).
    Test 10's
    `test_the_guard_answer_time_is_recorded_beside_todays_direct_guard`, on
    the fixture, is such a floor and decides nothing for Q29. The figure
    that decides is taken at act 3, from Eagle and Raven against the
    server, and again by each workstation that joins later (SVC-9).
  - **Q28:** "Ask openRepoShape-2 first (Recommended)", on #198, where lane
    openRepoShape-2's row has been `PAUSED` since 2026-10-24. Brett then
    named the two dates with "Confirm both (Recommended)" (#198, comment
    6722244541), which no test reads:
    - #282 (1a) and #307 (1b) as draft PRs by 2026-11-10, ready by
      2026-11-12;
    - #312, #316, #311 and #308 open by 2026-11-12, ready by 2026-11-17.
  - **Q24, the release:** v1.2.0 carries #165 and T017's five fixes, #290,
    #291, #295, #301 and #302, and the other issues milestoned v1.2.0 move
    to v1.2.1 ("set 1.2.0 for 165 alone, 1.2.1 for the rest", then "let
    T017 ride in 1.2.0, retitle #343": comments 6721648587 and
    6721870837). Whether Eagle and Raven install by pipx or by `./setup.sh`
    stays open.
- **Still open** (the design's "Open questions" at W):
  - The expiry of any outage Brett declares, which he names later. No test
    reads it: AT-D20 expires a declaration with a UTC already past.
  - Whether Eagle and Raven install by pipx or by `./setup.sh` (Q24).
  - Whether the organisation's GitHub plan supports the ruleset, and whether
    it can name the render set exactly (Q21(b), D23). Nobody has checked.
  - Whether every environment has git 2.32 or later, which the deploy key's
    push needs for `GIT_CONFIG_GLOBAL`, and whether `ready` can prove the
    render key's write right, by `git push --dry-run` over SSH (D23; the
    phase plan's row 2).
  - The link to the server, which the design routes to Brett (open point
    31).

A test that turns on round 1 or round 2 is written under Brett's answer.
Where it still rests on one of these open points, its entry says so, in
its options. An entry's other options are the frame's or the design's.

### 1.5 The frame's decisions, and where they change an oracle

Where the frame decides what the proposal left open, the frame wins, and the
entry says so in one line. The decisions that set an oracle:

| Decision | What the tests assert |
|---|---|
| D2: `lanes-index` is the one database client | Faults are injected at its `psql`, through `PATH` (section 3.2). The commands are `mode`, `register snapshot`, `register commit`, `register read`, `journal`, `render`, `import --single`, `render --revert`, `outage declare`, `outage clear`, `cutover`, `serve`, and §2.4's `bootstrap`, `ready`, `set-mode` and `login`. |
| D3: the mode is asked only where a configuration exists | test 14 |
| D4: on primary, the commit seam collects and never commits | test 2's "git unchanged", and test 1 |
| D5: one transaction per verb, with `start-record` as `lane-start`'s one composite verb, and F5's expectations as `act-begin` plus six scopes, `LANES_ACT` being `<act-id>@<seq>:<generation>` on primary and `<act-id>@files` off it (lane 4's section, accepted in comment 6708889286; lane 3, comment 6711564365). Brett accepted per verb and the three conditions (comment 6721583757) | one journal entry per verb (tests 1 and 11); AT-F5; AT-F6 |
| D6: the head row before the journal row | test 4 and section 4.1: no exit on a 23505 |
| D7: "register busy" is 75, at a 30 s deadline, set for tests by `LANES_REGISTER_DEADLINE_S`, and kept at 30 s over the remote database, where a far workstation's burst may still end in 75 with nothing written (the phase plan's "Over a remote database") | tests 4 and C5, section 4.1 |
| D8: a write in doubt ends with no code but 0 until F3's lookup, `find_write`; it exits 3, naming the id, where no try that starts within the 10 s reconnect window takes the lock or LR011 refuses the lookup. The window opens when the writer's wait for `not_after` ends, each try runs to its own timer, `T_L`, 9 s at `c` = 2, and a whole write ends by about 62.5 + 3`N_a`·`c` s, 68.5 s at `c` = 2 with one address. A rerun reuses the id through `LANES_WRITE_ID` and `LANES_WRITE_GENERATION` or the pending record. Brett accepted exit 3 (comment 6721370130). Where the managed cloud PostgreSQL's link changes a timer or a healthy call's time, the figures are lane 4's (comment 6722143023; the phase plan's "Over a remote database") | test 12, AT-D8, AT-F3, AT-F10 |
| D9: the timeouts table, whose `connect_timeout` is each workstation's own `c`, its client timers counting `N_a`·`c` for the `N_a` addresses its endpoint resolves to (`register_addresses=`, default 1); a figure at `c` = 2 or 3 is for one address (the phase plan's "Over a remote database") | AT-D9, one case per row; section 4.4 |
| D10: a refused direct write is 2; the guard maps 69, 75 and 3 to 2; `lanes-index`'s register subcommands use 69 | tests 2, 8 and 10 |
| D11: `register-row-local` reads the database on primary | test C5 |
| D12: `LANES_NO_FETCH` has no effect on a primary write, and the renderer is forked without it | tests 8 and C8 |
| D13: a line's UTC is the database's clock at the snapshot | test 1 |
| D14: the renderer is the writer's fork, with `lanes-render.lock`, an `fcntl.flock` in `SyncLock`'s form with both halves of its handshake, and `lanes-render.rerun` in the checkout's git directory (lane 4, comment 6579524611; lane 3, 6579562840) | test 5 (with [L4-4]'s kill), and the render wait (open point 8) |
| D15: a render commit carries the journal's recorded identity, and `Register-Seq: <k>` in its message's last paragraph, which `lanes-index` composes (lane 4's section, "F29") | test 5 |
| D16: the divergence record is in the database, and a revert is an empty journal entry | test 6; AT-MIR |
| D17: readers go through the service, then a direct READER read | tests 10 and C5 |
| D18: the later `HOLD` or `LANDING` writer exits 2; Brett accepted it as a reading of S6 (comment 6721583757) | test 4; AT-LR5 |
| D19: a claim's spellings are checked in the transaction | tests C10 and D19; AT-SPL; AT-LR5 |
| D20: a declared outage is a file beside `lanes-index.conf`, with an expiry; a rollback's start declares one too, marked, with no expiry and the head `k` the rollback found as its `mirror-seq` | AT-D20; AT-F7; AT-F11 |
| D21: `lanes/STORE`'s fields, `lanes-tree=` among them | test 7 |
| D22: the disaster path re-imports at the mirror's tip, and the after-loss cutover takes its label from `register_label=`, never a flag. Two backups count, the daily dump of both schemas and the provider's own copy; if the subscription is lost, the mirror is the copy outside Azure | AT-D22; AT-F18; AT-LRMPG (the restores) |
| D23: the renderer pushes with a deploy key named by `render_key=`, and so do the cutover's and the rollback's commits of `lanes/STORE`; the ruleset names the render set and `lanes/STORE` | test 5; test 6; AT-F2 |
| D24: exactly two operator entries, the cutover's and the rollback's last, flagged and never rendered; besides the import's baseline, nothing else is journaled while the switch is not `primary`; an operator entry writes no row outside `lanes/STORE` (LR004 at commit); Brett accepted the marker pushes (comment 6721370130) | AT-F1; AT-F2; AT-SET; AT-LR4 |
| D25: `lanes-index cutover` is a command of its own; Brett accepted it (comment 6721583757) | test 7; AT-F2 |

The guard's bound is the frame's too: exit 2 in under 4.5 s in every case
("One budget"), and it stays (lane 3, comment 6578688322).
Q29's limit, 1 s, is another figure: the whole guard's warm p95, measured
without the service, past which act 4 waits for the service (section 1.4;
section 5.6).

## 2. The agreement tests

A heading's `AT-n` is the text's "test n": AT-4 is test 4, AT-C10 is test
C10, and AT-D9 is test D9, with AT-D9c, lane 4's name for D9's cases over
a remote database. Lane 1's schema objects keep their full labels,
AT-SET, AT-MIR, AT-SPL, AT-LR5, AT-VER, AT-LR4 and AT-LR006 (with AT-LR006-1
to -8), AT-LR007, AT-LR009, AT-LR010, AT-LR011, AT-LRLABEL and AT-LRMPG, and
so do the frame review's: AT-F1, AT-F2, AT-F3, AT-F4, AT-F5, AT-F6, AT-F7,
AT-F8, AT-F10, AT-F11, AT-F13, AT-F14, AT-F18, AT-F33 and AT-F34 (the
tail, once),
where AT-F33 sits beside AT-LR006, with F19 and F33 in a closing note.

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
    (W), Brett's row 18, accepted in comment 6721583757: the handoff paths
    leave the change set, the row's
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
    on primary;
  - `test_the_render_of_one_entry_and_of_a_full_render_is_timed_and_recorded`
    (N; M6, section 5.1): the pure renderer's time for one journal entry's
    image and for a full render of the fixture register, each recorded with
    section 5.3's statistics and its n, never asserted. Correctness alongside:
    the one entry's render equals the full render's bytes for the same paths,
    so a measurement that rendered wrongly fails, whatever its time.
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
  - **Unreachable (RE), the storage-layer entry.** The managed projection
    writer's storage-layer entry, called directly as AT-1's part N calls
    it, with the same fresh, empty socket directory: the delta's "The
    database is unreachable" names it among the writes.
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
    on stdout. Where the switch is not `primary`, stderr carries AT-3's
    line, "this estate's register is SQL; configure `mode=primary`", the
    same text a files-mode writer prints (the delta's "A primary writer
    meets a switch that is not primary").
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
    - Once the fault is removed, a second write made at once exits 0, which
      shows no lock was left.
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
  - `test_the_storage_layer_entry_exits_69_when_the_database_is_unreachable`
    (N): it exits 69 naming the store and the error, and the register's
    rows and the journal are unchanged;
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
  - **`wip init` where a `lanes/` file exists, with no marker.** This is the
    third exception to the delta's "Nothing changes where the marker is
    absent", and the delta's scenario "`wip init` where a `lanes/` file
    exists". It needs a fixture of its own, since no cutover has run: a
    files-mode workspace repository on a bare origin, with `lanes/STORE`
    absent and one file under `lanes/`. In one case the file is
    `lanes/LANES.md`, with `lanes/log/README.md` missing, the case where
    `wip init` commits the file it finds owed today
    (`code/openRepoTools:5026–5080`); in the other it is only
    `lanes/repos.tsv`. Each runs once per configuration: none, and
    `mode=index`.
- **Oracle.**
  - The writer exits 69, and stderr carries the line quoted above. `wip init`
    exits 2 and names the path it found, with no `mode=primary` line.
  - These are unchanged: the clone's HEAD, index, working tree and files under
    `lanes/`, and origin's refs.
  - No local commit is queued for the publish act.
  - With `LANES_NO_FETCH=1` and the marker already in its tip, the writer
    still exits 69.
  - **An unconfigured workstation's readers (Q21(a) (A)).** `who` exits 69,
    the guard 2, and `lanes` prints its `read: mirror` line.
  - **`wip init` where a `lanes/` file exists, with no marker.** It refuses
    with exit 2 and commits nothing: the clone's HEAD is
    unchanged, no commit is queued for the publish act, and origin's refs
    are unchanged. The refusal is the same in both configurations, and the
    same for either file.
- **Options.**
  - If act 2 leaves any verb without S3's fetch, that verb sees the marker
    only once it has fetched. Its case is then written against its last
    fetch, and the gap would be listed in section 6.3.
- **Tests.** In `code/tests/test_sql_register_marker.py`:
  - `test_a_files_mode_writer_refuses_under_the_marker[<verb>]`;
  - `test_a_mode_index_writer_refuses_under_the_marker[<verb>]`;
  - `test_a_writer_with_no_fetch_refuses_when_its_tip_carries_the_marker`;
  - `test_wip_init_refuses_under_the_marker`;
  - `test_wip_init_refuses_where_a_lanes_file_exists_with_no_marker[<configuration>-<file>]`
    (the third exception);
  - `test_a_readers_refusal_under_the_marker_names_the_refs_age`: an
    unconfigured reader refuses with 69, and its text says how old the ref
    it read is;
  - `test_a_reader_refuses_when_its_read_ref_carries_the_marker_and_its_tree_does_not`:
    the marker check runs `git cat-file -e` on the ref the read uses, not
    the working tree;
  - `test_an_unconfigured_workstations_acts_refuse_and_listings_read_the_mirror`.
- **Run time.** About 32 cases at 0.2–0.4 s, so 6–13 s.
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
    not 69 (P224–234, P292–295), and with `LANES_REGISTER_DEADLINE_S`
    unset its deadline is D7's 30 s (S1's section on the deadline and
    "register busy");
  - no writer exits on a 23505 from the statement order (P235–239).
- **Phase and kind.** W. RE: no fake can produce a 40001 (R1, section 3).
- **Fixture.**
  - The imported register after a first cutover: the switch at `primary`,
    the marker `present`, the import's baseline at seq 0 and the cutover's
    entry at seq 1, so the first write is seq 2 (the delta's "A burst loses
    no writer"; S5, "Cutover", step 4). The objects are free.
  - One workstation configuration, and a distinct lane name for each writer.
    The ten writers share one `lanes-index` state directory, so one image
    cache, `register-images/`.
  - A fake `gh` on `PATH`, as AT-D8's, logging each call with its body, for
    the claim comment.
  - **A start barrier.** Each writer is launched through a small launcher
    that blocks on a FIFO the test opens only once all N launchers are
    waiting.
  - **The window.** A `psql` shim (section 3.2) sleeps W ms before it hands
    on the call that carries the attempt's COMMIT. That is the second
    `psql` call of each attempt (Q7 (A″)), and the shim knows it by its
    call of `lanes_register.commit_write`, since the snapshot's request
    ends in a `COMMIT` too.
  - **The deadline (A″).** D7 sets it at 30 s, and
    `LANES_REGISTER_DEADLINE_S` sets it for tests, in whole seconds
    (`design.md` S1). The burst, case (c), runs with the variable unset, at
    D7's default; the busy case (d) sets it to 1; and case (h) leaves it
    unset, to assert the 30 s default once.
- **Oracle.**
  - **(a) Two claimants, W = 200 ms.**
    - Exactly one `CLAIMED` line for the object, in the journal and in the
      line store.
    - The exit codes are {0, 2}.
    - The journal grew by one.
    - The loser's write id is absent.
    - No `CLAIM-LOST` line is produced (`design.md` S6).
    - The winner prints `register seq <n>` (Q19 (A)), `<n>` being its
      entry's seq, and the claim comment its tail posts through the fake
      `gh` cites `register seq <n>`, the same `<n>` (the delta's "A claim's
      comment cites the register sequence").
  - **(b) Two `HOLD`s.**
    - The journal grew by one: the first writer's `HOLD`, whose commit took
      the head row first.
    - The first writer exits 0.
    - The second exits 2 and writes nothing (D18). Its stderr carries the
      earlier line verbatim, with its register seq.
  - **(c) The burst.** Section 4.1's oracle, with the seqs counted from the
    cutover's entry: the ten writes take seqs 2 to 11, with no gap and no
    repeat (the delta's "A burst loses no writer"). It runs with
    `LANES_REGISTER_DEADLINE_S` unset, at D7's 30 s default.
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
    - A take whose UTC cannot be read (U3) is refused on primary with 2 the
      same way: its client half is AT-F13 (b), and the commit's LR005
      `take-no-utc` is AT-LR5's.
  - **(g) The image cache, `register-images/`** (the phase plan's "Question
    13: the image cache, and the service as a second writer"). In the state
    directory the ten writers shared, after case (c):
    - every file under `register-images/objects/` is named by the sha256 of
      its bytes, and no temporary file is left;
    - `register-images/head`, which moves only forward, never decreases in
      the samples the test takes while the burst runs, and ends at a seq no
      higher than the journal's head. The manifest
      `register-images/seq/<n>` of that seq exists and lists only objects
      that are present;
    - a corrupted object, its bytes changed under its name, is not used: the
      next write's snapshot finds the digest mismatch and fetches the files
      whole, exits 0, and its render is byte-identical to a full render. The
      cache is advisory, and correctness rests on the digests (S1, "One
      attempt, under (A″)", step 1).
  - **(h) The default deadline** (D7). With `LANES_REGISTER_DEADLINE_S`
    unset, one writer whose every attempt loses its turn: the test's admin
    session holds the head row `FOR UPDATE` for the whole case, as AT-D9's
    commit F3 row does, so each commit call gets 55P03 at its 2000 ms
    `lock_timeout`, a lost turn.
    - The writer exits 75, its write id absent from the journal, and its
      stderr carries the busy text with "attempts in 30 s".
    - It exits no earlier than 29 s after the test launched it, the
      deadline less `SECONDS`' 1 s grain (timing rule 3), so a shorter
      default fails.
    - From its launch, it exits under twice the design's bound, the
      deadline plus one attempt's `S` and `C`, 2 × (30 + 8 + 8) = 92 s, and
      no snapshot call starts more than twice the deadline, 60 s, after the
      first (timing rule 2). Both times are recorded against the design's
      46 s and 30 s, with their margins.
- **Tests.** In `code/tests/test_sql_register_order.py`:
  - `test_two_claimants_one_claimed_and_the_other_exits_2`;
  - `test_the_later_hold_exits_2_and_prints_the_earlier_line`;
  - `test_ten_writers_released_together_all_commit_in_order`, defined here
    once; section 4.1 gives its oracle;
  - `test_a_write_that_cannot_get_its_turn_exits_75_register_busy`;
  - `test_the_default_deadline_is_30_s_for_a_write_that_never_gets_its_turn`,
    case (h);
  - `test_a_hold_line_that_names_no_repository_is_refused_with_2`;
  - `test_each_line_kind_takes_or_releases_main_as_q17_b_says[<kind>]`: after
    each kind's line, `main_slots` shows `main` taken or released as Q17
    (B)'s table says, and another lane's `HOLD` then exits 0 or 2 to match.
    `LANDING EXPIRED` is among the kinds, written by AT-F13's form over a
    `LANDING` composed with a UTC 31 minutes back (the delta's "Which lines
    take and release `main`");
  - `test_claim_prints_its_register_seq`;
  - `test_a_serialization_failure_is_retried_never_reported_as_69`;
  - `test_the_image_cache_stays_content_addressed_under_the_burst_and_a_corrupt_object_is_refetched`.
- **Run time.** An attempt is a snapshot, a verb run and a commit, about
  0.55–0.61 s (lane 4's section, "The cost of one attempt, [L4-c]"); the
  probes' 0.5 s and 2.3 s were `psql` alone. Cases (a), (b) and (e) take
  about 1.5 s each, since the loser makes a second attempt. Case (c) takes
  about 6–8 s, the tenth writer's ten attempts. Case (d) takes about 2.5 s.
  Case (g) reads the cache the burst left and runs one more write, about
  1 s. Case (h) waits out D7's 30 s default, about 31–34 s. That is
  45–50 s in all, and case (h) is a long row (open point 9).
- **Flakiness.**
  - The barrier, not a sleep, makes the writers simultaneous.
  - The window, 200 ms or more, is far above scheduling jitter.
  - Every assertion is about counts, codes and order, except the two below.
  - The burst runs at D7's 30 s default. Its outcome, every writer committed
    and none exiting 75 (the delta's "A burst loses no writer"), depends on
    time through that deadline alone. The probes' 2.26 s drain was
    `psql` alone. A real attempt costs about 0.55–0.61 s (lane 4's section,
    "The cost of one attempt, [L4-c]"), so the tenth writer's ten attempts
    take about 6 s on an idle host, or 6–8 s with the window (case (c)'s
    run time above), a margin of about 4 to 5 against the 30 s.
    The burst's 55 verb runs and 110 Python starts are about 28 CPU-seconds
    on the measuring host, so a runner with c cores needs at least 28/c s of
    wall time, and its margin is less. The burst's drain, from the release
    to the last exit, is asserted under twice the deadline, 60 s (timing
    rule 2), and recorded against the 30 s with its margin, beside each
    writer's attempts, counted as snapshot calls in the shim's log and not
    asserted. A runner that cannot drain the burst inside the deadline is a
    finding on D7's value, which the delta requires to exceed the largest
    burst's drain, and not flake (open point 32).
  - Case (h) asserts the default by a lower bound, 29 s from the launch
    (timing rule 3), which load only lengthens, and by an upper bound at
    twice the design's (timing rule 2).

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
  - **A `Register-Seq` above the head, with the switch not at `primary`**
    (R2's N6, which the renderer applies at `primary` only; S2, "The
    renderer, in the private-index form", "A pass"). The switch reads
    `files`, and the journal's head is `h`. Origin's `main` carries a
    commit whose last `Register-Seq` is above `h`, in one of two forms:
    - the marker commit `S` with `Register-Seq: 1`, pushed ahead of the
      import as (ii′)'s marker-first window has it (`operations.md` §2.2,
      "How (ii′) differs"), while `h` is -1 or 0;
    - a forged one: a commit under `handoffs/` that carries
      `Register-Seq: <h+10>`, pushed after the import.

    A pass run by `lanes-index render --checkout <c>`, as a fork runs it:
    - records no divergence: `mirror_state.diverged_sha` is NULL, and
      `status` prints no `diverged at`;
    - renders and pushes nothing, since its target `t` is at most `h`;
    - exits 0.

    The forged commit, once the switch reads `primary`, is AT-6's case.
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
    two checkouts give one sha for each, and form C reads `<k>` from each;
  - `test_a_register_seq_above_the_head_with_the_switch_at_files_is_no_divergence_and_renders_nothing[marker-first-head-minus-1|marker-first-head-0|forged]`
    (R2's N6, with the switch not at `primary`);
  - `test_a_render_commit_with_a_private_index_and_its_push_are_timed_and_recorded`
    (G; M6, section 5.1): the render commit made through the private index,
    and its push to the local origin, each timed, the commit and then the
    push, which the `git` wrapper's log tells apart. They are recorded with
    section 5.3's statistics and their n, never asserted. Correctness
    alongside: origin gains the commit that carries its `Register-Seq`, as
    the race's oracle has it.
- **Run time.** About 28 cases at 1–3 s each, so 30–85 s; the 16-pass
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
    - `reconcile` exits non-zero, the journal's count is unchanged, and
      `status` still prints `diverged at <stray sha>` after it.
    - A later primary write still commits to SQL, since the mirror is
      write-behind.
  - **The single-write import.**
    - `lanes-index import --single <sha>` adds exactly one journal entry,
      through S1's commit call. Its line edits equal the stray commit's diff.
    - It refuses where the edits no longer apply to SQL's images, which is a
      case of its own (T271's `[stray-no-longer-applies]`).
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
  - **A `Register-Seq` above the head** (the delta's requirement of that
    name; S2, "A pass" and "The two ways out of a divergence"; R2's N6).
    The estate is at `primary`. A commit that changes no rendered path, a
    file under `handoffs/`, carries `Register-Seq: <h+10>` in its final
    trailer block, where `h` is the journal's head when it is pushed. Then a
    primary write commits at `h+1` and forks a render.
    - The pass records a divergence at that commit, renders and pushes
      nothing, and the renderer exits. `mirror_state.diverged_sha` is the
      commit's sha.
    - `status` prints `diverged at <sha>`, with the full sha, naming that
      commit, and no lag: its `m` is past `n` (Y:2850), as `status` says.
    - `lanes-index render --revert <sha>`, on a person's word, journals one
      entry with no edits whose message names the commit. `diverged at`
      clears.
    - The renderer no longer counts that commit's trailer. It finds `m` at
      the last render commit before the commit, and renders on, on top of
      it, to the revert's own seq. Origin's rendered paths then equal a full
      render, and `reconcile` exits 0.
    - A rollback afterwards works, as for each way out above.
- **Options.**
  - **Q21(b) (B), Brett's answer,** puts a push ruleset in place before the
    cutover where the org's GitHub plan supports it. Brett has ruled its
    scope: the render set and `lanes/STORE`, as D23 and `operations.md` have
    it (lane 3, comment 6721583757, "Render set + STORE"). One fact is
    still to find: whether the org's plan can name exactly those paths,
    which nobody has yet checked ("Open questions"). Where the ruleset is in
    place, GitHub refuses the stale push. The suite cannot reach GitHub, so
    a pre-receive hook in the bare origin models it, in the added cases
    below, which are labelled a model. The hook guards exactly the render
    set and `lanes/STORE`: `lanes/LANES.md`, `lanes/log/<lane>.md`,
    `lanes/aliases.tsv`, `lanes/archive/` and `lanes/STORE`. It guards no
    other path under `lanes/`, `lanes/repos.tsv` and `lanes/log/README.md`
    among them, which the delta's gate requirement leaves to a person's
    commit.
    - `test_a_server_side_gate_refuses_the_stale_writers_push[lanes-md|log-lane|aliases-tsv|archive|store]`, for
      each guarded path: it shows the stale writer's exit and an unchanged
      origin.
    - `test_the_gate_model_lets_a_push_to_an_unguarded_path_through[repos-tsv|log-readme]`:
      the push is accepted (the delta's "A person's `repos.tsv` edit is not
      gated").
  - **D2 and D16** name the two commands and keep the divergence record in
    the database. Each renderer still re-detects the divergence from git, so
    the row reports and never gates alone.
- **Tests.** In `code/tests/test_sql_register_divergence.py`:
  - `test_the_v1_1_0_writer_pushing_to_a_primary_estate_stops_the_renderer`;
  - `test_status_prints_diverged_at_the_stray_sha_and_reconcile_imports_nothing`;
  - `test_the_single_write_import_resumes_the_renderer_and_reconcile_passes`;
  - `test_the_renderers_revert_resumes_it_and_reconcile_passes`;
  - `test_rollback_works_after_each_way_out[import|revert]`;
  - `test_commits_outside_the_render_set_do_not_stop_the_renderer[cutover-store|repos-tsv|handoff]`;
  - `test_a_register_seq_above_the_head_stops_the_renderer_at_primary_and_the_revert_resumes_it`
    (R2's N6, at `primary`; its twin at `files` is AT-5's).
- **Run time.** Eight cases at 2–4 s each, so 16–32 s, and the gate model's
  seven at about 1 s each. A v1.1.0 verb run takes under a second.
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
    - a `HOLD` and a `LANDING` with no UTC, and a `LANDING` whose
      UTC-shaped stamp does not parse, `2026-13-45T99:99Z` (U3), each on a
      repository of its own that no other case takes;
    - a line in the `LANDING EXPIRED` shape with no `for lane`;
    - an archived lane;
    - aliases;
    - `lanes/repos.tsv` and `lanes/log/README.md`, outside the render set
      (Q18 (A));
    - a lane whose log file's name differs from the lane's in case, and a
      lane with no log file, for the sidecar `.logpath.<lane>`.
  - **The difference** C' adds, its own case: a line ending the renderer
    cannot reproduce (CRLF), since a line's text holds no `\r` (Y:1181).
  - **Two lines that are no difference,** each a case of the success
    oracle: a trailing blank in a table row, and a line of a kind the parser
    does not know. Each is stored as it stands (Y:1176, Y:1467) and rendered
    back unchanged (Y:1300–1304), so the import exits 0 and the proof holds
    (lane 3's batches 9 and 11).
  - **Q4(c)(i)'s case.** The operator's clone holds one local, unpushed line.
  - **The cutover's abort.** Another clone pushes a commit under `lanes/` after
    C. For the tree-id rule, a third clone pushes a commit under `handoffs/`.
  - **The freeze,** Q4(a) (i), is the runbook's and not the suite's.
  - **The operator's configuration** carries `register_label=`, the label
    the template's bootstrap gave the database (`--label`). `cutover` takes
    the label it sends from that key, and refuses with 2, before it
    connects, where the key is absent (S5, "Cutover"; `operations.md` §2.3).
    The label refusals are tested with lane 1's v2.8 objects.
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
    - The `unreadable:` warnings are part of that stderr. A read of a log
      names its file in the warning by the path in the sidecar
      `.logpath.<lane>`, beside the cached log text (the phase plan's
      "Question 10: `$SE_CACHE_FILE.log.<lane>`"). The lane with unreadable
      `WORD` lines gets the same warning, naming the same
      `lanes/log/<File>.md`, from SQL as from the files, and so does the
      lane whose log file's spelling differs in case. The lane with no log
      has an empty sidecar and no warning.
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
  - **Takes with no UTC, and a release with no holder.** The import loads
    each as it stands and is not refused for it (U3; the delta's "An
    imported taking line with no readable UTC"). Each take's
    `register_lines` row is a take with a NULL `utc_ts`. The line in the
    `LANDING EXPIRED` shape is a line of no kind, with no slot role, that
    releases nothing (§1.2, "A `LANDING EXPIRED` line's fields"; the
    delta's "A line in the shape that names no holder"). A re-import loads
    them the same way.
  - **The cutover (G).** The operator lane runs it, with `LANES_LANE=` its
    name, through its checkout (F12, §2.1).
    - The switch reads `primary`, and after the read-back, the marker
      `present`.
    - Origin's `main` gains exactly one commit, a fast-forward of C, carrying
      `Register-Seq: c`, the cutover entry's own seq: 1 at this first
      cutover, and the seq after the import's baseline at any other (§1.3's
      kinds table; lane 3 words the marker's seq for every cutover form,
      comment 6579459518). It adds `lanes/STORE` with D21's fields:
      `store=primary`, `database=<label>` (the configuration's
      `register_label=`), `import=<C>`, `lanes-tree=<tree id of C:lanes>`
      and `utc=`.
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
  - **S7's agreement test** (lane 3, comment 6711564365; "The acts and their
    expectations", "What the journal needs"). On the imported fixture
    register, for every line of `lanes/LANES.md`, `lanes/log/*.md`,
    `lanes/archive/**` and `lanes/aliases.tsv`, the scopes that
    `line_scopes(<path>, <text>)` returns are compared with the scopes
    `build_records`' own reads give in Python (`code/lanes-index:765`), with
    `RETIRED_ROWS_AWK`'s `key()` for an archived row's normalized key
    (`code/lanes-edit.sh:3743–3760`) and `UUID_RE`.
    - No scope the Python side gives is missing from `line_scopes`'s. That
      is the whole assertion, since a rule that drifted would under-report
      and let an act's expectation miss a rival's write. The test counts
      the lines it compared, which is not 0.
    - The extras are recorded, by kind, and not asserted. `line_scopes` is
      wider than `build_records` by design (§1.3, "The touched keys" and
      "Over-reporting only refuses"; lane 3 accepted the over-reports,
      since an extra key only causes a refusal): it gives `sid:` for every
      uuid in a row line, where `build_records` reads the session cell
      alone; it gives each non-empty field of an alias line, where
      `build_records` keeps a line with both; and it gives `bind:` for a
      lane-kind line it cannot read.
    - On the real register's copy the rehearsal runs the same comparison.
      A scope missing there is a finding before the cutover.
- **Options.**
  - D21 settles the marker's fields, which P588–590 left to the design.
- **Tests.** In `code/tests/test_sql_register_import.py`:
  - `test_import_at_a_commit_proves_a_byte_identical_render[at-c|trailing-blank|unknown-kind]`;
  - `test_import_refuses_on_a_difference_and_loads_nothing[crlf]`;
  - `test_import_answers_every_read_as_the_files_do[<read>]`;
  - `test_a_reimport_replaces_seq_0`;
  - `test_word_lines_with_milliseconds_import_verbatim_and_flagged`;
  - `test_import_reads_origin_only_and_never_a_local_line`;
  - `test_import_loads_takes_with_no_readable_utc_and_a_holderless_landing_expired_as_they_stand[hold-no-utc|landing-no-utc|bad-stamp|expired-no-holder]`;
  - `test_a_cutover_pushes_one_fast_forward_marker_commit_with_no_credential`;
  - `test_the_first_write_after_cutover_is_the_cutovers_seq_plus_1_with_lag_0`;
  - `test_the_cutover_aborts_when_main_moved_under_lanes`;
  - `test_a_handoff_commit_on_the_tip_does_not_abort_the_cutover`;
  - `test_a_first_cutover_over_a_hold_with_no_readable_time_is_refused_2_and_sets_nothing`
    (new since v3f, T211n): over the fixture's `HOLD` with no readable time,
    left open, the first-form cutover exits 2 before its step 1, lists the
    take with "time unreadable", and sets nothing (`contracts/exit-codes.md`'s
    `cutover` 2; lane 3, #165 6813195977). Every other cutover case on the
    fixture register first has the take's holder, the operator lane, write
    its `HOLD RELEASED`, as act 4's holders do (§2.2);
  - `test_main_slots_pairing_equals_files_mode_rule_6_on_imported_history`:
    on the imported fixture register, whose `HOLD`, `LANDING` and `LANDED`
    lines name one repository under two spellings and close takes out of
    order, the open takes `main_slots` lists, with their places, equal those
    files mode's Rule 6 pairing leaves open on the same files; on the real
    register's copy the rehearsal runs the same comparison. Lane 3 accepted
    the pairing by spellings with these tests (lane 1's point 3, comment
    6710770304), and a difference found here is a finding before the
    cutover;
  - `test_line_scopes_agrees_with_build_records_rules_over_every_line_of_the_imported_register`
    (S7);
  - `test_the_log_path_sidecar_names_the_same_log_file_in_unreadable_warnings[unreadable-words|case-differs|no-log]`
    (the phase plan's "Question 10");
  - `test_the_imports_proof_is_timed_and_recorded` (N; M6, section 5.1): the
    time of `import --at`'s render and proof over the fixture register,
    recorded with section 5.3's statistics and its n, never asserted. The
    correctness alongside is the import's own oracle: the proof passes and
    the counts equal the files'.
- **Run time.**
  - An import of the fixture register takes well under a second. R3 measured
    the real register at under a second in memory.
  - The cases take 1–2 s each. The read comparison is 3 lanes × 10 reads × 2
    stores at 0.1–0.3 s.
  - That is 15–35 s in all.
- **Flakiness.** No time is asserted: the proof's time is recorded (M6), never
  compared. "Main moved" is a push made, and finished,
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
  - **(f) A stray on origin, not yet recorded,** is caught by the
    rollback's fetch right before its transaction: it records the
    divergence and exits 2, writing nothing (the delta's "A stray on origin
    not yet recorded").
  - **(g) A divergence recorded as the rollback starts** sends the command
    back to its check, which refuses it; a `last_error` recorded alone does
    not (the delta's "A divergence recorded as the rollback starts"; §1.3,
    "The rollback's divergence gate"). LR012 in the transaction is AT-F1's.
  - **(h) A lost turn.** A 40001, 40P01, 55P03 or LR001 on the rollback's
    call sends it back to its check with the head read afresh (S5,
    "Rollback, under Q4(b)(iii)"). The exit 75 at D7's deadline, with
    nothing written, is AT-D9's `rollback` row.
  - **(i) A write that committed before the rollback** is found by its
    rerun after it, and exits 0, not 69 (the delta's "A committed write
    found after the switch has moved").
- **Phase and kind.** G, since it needs rollback's push. RE, with git, and
  FP for (h)'s lost-turn codes.
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
  - **(f) to (i)'s fixtures.**
    - The held session of test 12: a session as `lanes_register_writer`
      that replays a commit call's script without its `COMMIT`, so it holds
      the head row. The test waits for the rollback's call to show
      `wait_event_type = 'Lock'` in `pg_stat_activity` (section 3.1), then
      acts.
    - `record_mirror_state(<ws>, <sha or NULL>, <error or NULL>)`, which the
      test calls in a session of its own as `lanes_register_writer`, the
      role that holds it (§1.4).
    - For (f), a stray pushed to origin with no write after it, so that no
      render forks and `mirror_state.diverged_sha` is NULL when the rollback
      starts, which the test reads first through the admin connection.
    - For (h)'s FP cases, a fake `psql` that answers the rollback's reads
      from a canned register and its step 1 with exit 3 and
      `ERROR:  <code>: <message>`.
    - For (h), test 5's `git` wrapper on ws1's `PATH`, which logs each call
      with its start time on the clock the shim's and the fake's logs use,
      so that the order of a fetch and a `psql` call reads from the two
      logs together.
    - For (i), the shim's `after` and `no-reconnect` modes (test 12) on
      ws2's write.
- **Oracle.**
  - **(a)**
    - The rollback exits 0.
    - The switch reads `files` with the marker `absent`, after
      `record_marker` confirmed the removal.
    - Origin's last commit removes `lanes/STORE` and carries the rollback
      entry's seq, `k+1`, the journal's last.
    - Origin's rendered paths equal a full render from SQL, byte for byte.
    - A `mode=primary` write from ws1 then exits 69.
    - The test then runs `lanes-index set-mode index` on ws1, as Brett runs
      it on each host at the rollback's step 5 (§2.3), and ws1's
      configuration no longer says `mode=primary`.
  - **(b)**
    - The rollback exits 69, by D10 and the timeouts table's `rollback` row,
      and stops at its step 1 (`design.md` S5).
    - After the restart, the switch still reads `primary`.
    - `lanes/STORE` is still on origin.
    - ws1 is still `mode=primary`.
    - No `register-outage` file is beside ws1's `lanes-index.conf`: a
      rollback refused at its step 1 declares nothing (§2.3; §2.5).
  - **(c)** For each of 6 verbs × {fetched, not fetched} × {variable unset,
    `LANES_NO_FETCH=1`}:
    - ws2 exits 69, and its stderr carries AT-3's line, "this estate's
      register is SQL; configure `mode=primary`" (`commit`: 2, D10);
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
  - **(f)** Two forms of stray: a commit by the v1.1.0 writer to a rendered
    path, and a commit that changes none and carries `Register-Seq: <h+10>`
    (`h` the head when it is pushed). With `diverged_sha` NULL, the
    rollback:
    - exits 2, naming `diverged at <sha>` and S2's two ways out;
    - has recorded the divergence: `mirror_state.diverged_sha` is the
      stray's sha;
    - leaves the switch at `primary` with the marker `present`, the
      journal's count and head unchanged, and `lanes/STORE` on origin.
  - **(g)** With the held session on the head row and the rollback's call
    waiting on it, the test records with `record_mirror_state`, then ends
    the held session by `ROLLBACK`. The call's `FOR SHARE` on `mirror_state`
    raises 40001, since the row was updated after the call's snapshot, and
    the command goes back to its check. LR012 in the transaction, by direct
    calls and through the command with the shim's `hold`, is AT-F1's, and
    is not repeated here.
    - With a divergence recorded, the check finds it: the rollback exits 2
      with `diverged at <sha>`, and the switch, the marker and the journal
      are unchanged.
    - With only a `last_error` recorded (`p_diverged_sha` NULL), the check
      finds no divergence and the retry commits: the switch reads `files`
      with the marker `removing`, and the rollback's entry is the
      journal's last seq, `k+1`.
  - **(h)** The retry takes a fresh `k`, in two parts:
    - **RE.** The shim holds the rollback's step-1 call for a window, and
      ws2 commits one write in it. The call gets LR001, the rollback reads
      the head afresh, runs its check again and commits on the new head:
      its entry is seq `k+2`, the journal's last, and the shim's log shows
      two step-1 calls with a head read between them, and the `git`
      wrapper's log a fetch of `origin main` between them.
    - **FP.** The fake answers every step-1 call with exit 3 and
      `ERROR:  <code>: <message>`, for each of 40001, 40P01, 55P03 and
      LR001, and `LANES_REGISTER_DEADLINE_S=6` ends the tries, as AT-D9's
      `rollback` row has it. For each code the fake's log shows at least two
      step-1 calls, each after the first preceded by a head read, and the
      `git` wrapper's log a fetch of `origin main` between each two, which
      is the check. The exit 75, the nothing written and the bound on the
      time are AT-D9's row.
  - **(i)** ws2's write is lost after it lands: the shim's `after` mode
    loses the first commit call and `no-reconnect` refuses every later
    call, so ws2 exits 3 with its pending record. The rollback then runs to
    its end from ws1. The test removes the shim's second control file and
    runs the printed rerun on ws2:
    - the snapshot finds the write id, and the rerun exits 0, not 69, since
      the write id is looked up before the switch is checked (S1, "One
      attempt, under (A″)", step 1);
    - the journal's count is unchanged by the rerun, and holds one entry
      for the id;
    - ws2's next new write exits 69, as (c) has it.
- **Tests.** In `code/tests/test_sql_register_rollback.py`:
  - `test_rollback_leaves_files_byte_identical_marker_gone_and_sql_writes_refused`;
  - `test_rollback_with_the_database_stopped_is_refused_and_changes_neither_switch_nor_marker`;
  - `test_a_missed_primary_writer_is_refused_after_rollback[<verb>-<fetch>-<no_fetch>]`,
    which expects 69 (`commit`: 2, D10);
  - `test_a_files_mode_claim_after_rollback_leaves_one_holder`;
  - case (d)'s two tests are section 4.2's, defined there once;
  - `test_the_rollback_oracle_fails_on_a_last_fetched_tip_writer`;
  - `test_the_rollback_oracle_fails_on_a_switch_reset_when_the_database_returns`;
  - `test_a_rollback_finds_a_stray_not_yet_recorded_records_it_and_exits_2[rendered-path|register-seq-above-head]`;
  - `test_a_rollback_goes_back_to_its_check_past_a_last_error_and_refuses_a_divergence_recorded_meanwhile[recorded|last-error-only]`;
  - `test_a_rollback_retries_a_lost_turn_on_a_fresh_k_through_its_check` (RE,
    LR001);
  - `test_a_rollback_goes_back_to_its_check_on_each_lost_turn_code[40001|40P01|55P03|LR001]`
    (FP);
  - `test_a_write_that_committed_before_the_rollback_is_found_by_its_rerun_after_it`.
- **Run time.**
  - A stop or a start of the fixture's `Popen` cluster takes about
    0.03–0.06 s (the fixture probe: start to ready 30–37 ms, stop and remove
    60 ms). R1's 116 ms and 102 ms were `pg_ctl`'s.
  - The 24 missed-writer cases take about 0.3 s each.
  - Each rollback takes 1–2 s.
  - Cases (f) to (h) add about 3 s each, except the four FP cases, which
    run to their 6 s deadline: nine cases, about 40 s. Case (i) adds about
    22 s: ws2's write waits about 18 s for its lookup's window.
  - That is about 80–100 s in all.
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
    store and path (P892–894, Q10 at P1667–1679). Against Brett's managed
    cloud PostgreSQL, Azure Database for PostgreSQL Flexible Server (lane 3,
    #165 6722025863 and 6722593397), CI's figure is the local share and a
    floor; the production figure is act 3's (section 5.6).
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
  - For M12 and M13, section 5.6's two clusters: the TLS cluster, on
    loopback TCP with TLS and SCRAM, and the counting cluster, which logs
    each connection and each Query message.
- **Oracle.**
  - Every timed write committed: the journal grew by exactly the number of
    writes, and each `write_id` appears once.
  - Every verb exited 0.
  - The figures are written as section 5 sets out, each with its host, store,
    path, statistic and n, and with its link: the RTT, TLS, and the path's
    connections and Query messages (M13; section 5.4).
  - One write is the snapshot call, the verb's bash run and the
    compare-and-swap COMMIT call (Q7 (A″)). That is what the verb's
    end-to-end figure carries, and it is recorded.
  - **Per attempt,** beside files mode, it records what lane 4's section,
    "The cost of one attempt, [L4-c]", names, each part stamped on one
    monotonic clock:
    - Python start to `main`'s first line;
    - the snapshot call;
    - the snapshot's image-cache check, and a miss where there is one,
      beside lane 4's figures of about 3 ms for the check and about 10 ms
      for a miss (lane 4's section, "The cost of one attempt, [L4-c]");
      AT-4 (g) proves the cache correct;
    - the scratch tree ready;
    - the verb's exit;
    - the commit call;
    - the host's load average and the Python version;
    - at act 3, against the named provider, the path's RTT and each call's
      connect, warm and cold, on each workstation as it joins (the phase
      plan's "What test 9 records"; section 5.6).

    The `psql` shim stamps the two calls. The design names no seam for the
    other stamps: lane 2 proposes them in a test copy of `lanes-index`,
    made as open point 3's copies are, and lane 3 rules.
- **Options.**
  - **Q29 (a), Brett's answer, at his 1 s** (lane 3, #165 6722025863): the
    guard measured without the service decides whether act 4 waits for it.
    The deciding figure is the warm p95 of test 10's whole guard through the
    direct read, taken at act 3 and by each workstation as it joins, with
    the cold walk recorded beside it and not judged (lane 3's ruling, in
    "Values Brett named on 2026-11-08"; section 5.6). This test's write
    figures sit beside it.
- **Tests.** In `code/tests/test_sql_register_measure.py`:
  - `test_one_write_cost_is_recorded_and_every_write_commits[<path>-<store>]`;
  - `test_each_verb_is_timed_end_to_end_in_both_modes[<verb>]`;
  - `test_each_paths_connections_and_queries_are_counted[<path>]` (M13);
  - `test_a_tls_and_scram_connect_on_loopback_is_timed_and_verified` (M12's
    CI row);
  - `test_the_image_cache_check_and_a_miss_are_timed_per_attempt`;
  - `test_each_shape_on_a_delayed_relay_is_recorded[<shape>-<rtt>]`, only
    under section 5.6's option B.
- **Run time.**
  - About 12 s for 200 writes in each of the four path-and-store pairs.
  - About 60–120 s for 21 runs (one cold, 20 warm) of the four verb sets in
    both modes.
  - M12's TLS cluster and M13's counting cluster cost about 0.9 s each,
    and M13 runs each path once more (section 5.6).
  - Whether all of it runs on every CI run is open point 9.
- **Flakiness.** No time is asserted, so a loaded host changes the figure and
  never the verdict. Each record names its host, so a bench figure is never
  read as CI's, and its link, so a local figure is never read as
  production's.

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
  - the direct read's connect timeout is the workstation's `c`, which may
    exceed its share, since the read's own timer ends the read inside it
    (the register-service delta's "The 5 s is one budget, spent once";
    section 4.4's case 5);
  - the service's per-request timer is 200 ms, or the workstation's round
    trip plus 100 ms where `register_rtt_ms=` is over 100, and the client
    waits that timer plus 100 ms (SVC-6, SVC-8);
  - each request is one query, its statements joined with `\;`, and an
    idle held connection is checked with `SELECT 1` every 60 s (SVC-6);
  - a digest mismatch that the image cache cannot fill is answered
    `unavailable`, and the service fetches the file whole in the
    background, never on a request's time (SVC-7);
  - a whole file the direct read must fetch is fetched by a detached child
    that fills the image cache, so a fetch the start line cuts is kept for
    the next prompt (SVC-8, "A whole file is never lost to the line";
    section 4.4's case 6);
  - a write by another process between two requests is in the next answer;
  - no environment a session inherits carries a database URL or password
    (Amendment 14(c));
  - the service's answer time is recorded against SVC-10's 50 ms on the
    fixture register, with today's direct guard beside it (P5). Every
    answer crosses to the database (SVC-7), so act 3 records it against
    Brett's managed PostgreSQL with its RTT and Query count (section 5.6);
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
  - **The measurement's register** (M5b's register read and M10's parse,
    section 5.1). The synthetic register that section 5.3 builds in CI, since
    a CI checkout has no bench input: about 2.6 MB with 73 lanes, shaped as
    the real one is, with that lane's log, about 90 KB. The test writes it
    twice, as SVC-8's cache files, for the parse alone, and as the files of a
    git checkout, for today's six reads through git. Its build time is
    recorded beside each figure. On the bench the 2026-11-02 inputs that
    section 5.3 pins by path and sha256 stand in its place.
- **Oracle.**
  - **Byte-identical.** On one state, the service path and the direct path
    (with no service) give equal stdout bytes, equal stderr bytes once the
    one fallback line is removed, and equal exit codes.
  - **The fallback.** Stderr carries exactly that line, once, and the exit
    equals the direct read's.
  - **Never cached.** The first request answers 0. The database is stopped,
    and the second request exits 2.
  - **Inside the budget.** The hook exits 2, never killed, in under 4.5 s,
    the frame's assertion (`design.md`, "One budget"; timing rule 1). It
    does so with `connect_timeout=3` too, where the read's own timer, not
    the connect
    bound, ends the read (section 4.4's case 5).
  - **Too late to verify** (the register-service delta's "A guard that
    starts too late", its base case, with no declaration). The database is
    healthy, and the fake `tmux` holds the hook's fixed parts until 3.8 s
    after the hook's start, past the direct read's cap of 3.75 s (SVC-8's
    clamp). The client exits as "too late to verify", and the hook exits
    2, never killed, in under 4.5 s. The `psql` shim's log shows no call,
    and the service's recording stand-in no request. AT-F7 holds the
    declaration's floor.
  - **A fresh write.** Request, a write by another process, request: the
    second answer equals the direct read made after the write.
  - **The environment.** No variable carries the register password, the
    password file's path, `register_url=`'s value or any `postgresql://`
    string. That covers the hook's own environment, the service's children,
    and the fake `claude`'s recorded environment. The fake `psql`'s log shows
    `PGPASSWORD` never set.
  - **The answer time.** It is recorded as section 5 sets out, never asserted.
    On CI's fixture it is the local share; act 3's row is the production
    figure.
  - **M5b, and M10's parse** (section 5.1), recorded and never asserted, 30
    warm runs and the first each (section 5.3). Today's direct guard is timed
    in the four parts the design measured, on copies of `code/lanes-edit.sh`
    in a sandbox under `env -i`, with a sandbox `HOME`, a synthetic workspace
    and no `tmux` server (`design.md`, "What today's guard costs, in four
    parts"): reaching the arm at `code/lanes-edit.sh:16856`, the `tmux` read,
    `transcript_holders`, and the register read with its parses, which reads
    the measurement's register. The guard's parse alone is timed on the cache
    files, beside today's six reads through git on the checkout, and each is
    recorded with the parse's 0.5 s reserve (section 5.1's M10). Correctness
    alongside: for each of the six reads, the parse of the cache files and
    today's read through git give the same answer.
  - **The codes the guard maps (D10).** Answer 2's bind runs detached
    (AT-F6). When it is made to exit 69, 75 or 3 (test 12's shim, or a
    `LANES_EDIT` wrapper), the child writes `mode=bind-failed` and `rc=` that
    code to the offer file, and the next prompt's hook exits 2 with "THE BIND
    TO LANE `<to>` FAILED (exit `<rc>`)", the why and, for 3, the write id
    (lane 4's section, "What the guard answers").
  - **The fallback's credential (Q11 (a)).** The fallback holds its
    engineer's reader login's credential itself, `lanes_r_fx` in the
    fixture, a member of READER, from the 0600 configuration (the
    register-service delta's "The guard falls back to the database, never
    the mirror"): the fake `psql`'s log shows the direct read's `PGUSER` as
    that login, and the environment check above covers the rest.
  - **The service's timer** (SVC-6, SVC-8). Through test D9's F2 relay,
    which goes silent after the startup, the service answers
    `unavailable slow` no earlier than 200 ms after the request reached it
    with no `register_rtt_ms=`, and no earlier than 250 ms with
    `register_rtt_ms=150`; the client's wait ends no earlier than 300 ms
    and 350 ms. These are lower bounds, which load only lengthens (timing
    rule 3); the upper bounds are AT-D9's service row.
  - **One query per request.** The fake `psql` behind the service logs each
    request's statements, from `BEGIN ISOLATION LEVEL REPEATABLE READ READ
    ONLY` to `COMMIT`, as one request joined with `\;`, asking for the
    journal entries after the seq of the images it holds.
  - **The idle check.** With no request, the service sends `SELECT 1` on its
    held connection every 60 s. After the test's admin session ends that
    backend with `pg_terminate_backend`, the next check fails, the service
    starts a new `psql` in the background, and the next request is answered
    from the new connection, not `unavailable`.
  - **A cache miss.** A service that holds no image yet, connected, with the
    per-environment image cache empty, answers its first request
    `unavailable`; the client reads directly, and the service fetches the
    files whole in a transaction of its own. Once they are in the cache,
    the next request is answered by the service. With the cache already
    holding the files at `n`, the first request is answered from it.
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
  - **Q29 (a), Brett's answer, at his 1 s** (lane 3, #165 6722025863): act
    4 may come before the service, unless the guard measured without it is
    slower than 1 s. The direct-read cases are then the guard act 4 relies
    on (P4). The figure that decides is the warm p95 of
    `test_the_guard_answer_time_is_recorded_beside_todays_direct_guard`'s
    whole hook through the direct read, taken at act 3 and by each
    workstation as it joins, with the cold walk recorded beside it and not
    judged (lane 3's ruling, in "Values Brett named on 2026-11-08"; section
    5.6). CI's figure is local, and a floor: SVC-9 says it decides nothing
    for Q29.
- **Tests.** In `code/tests/test_sql_register_guard.py`:
  - `test_the_guard_through_the_service_answers_byte_identical_to_the_direct_read[ok|refuse|offer]`;
  - `test_with_the_service_down_the_hook_falls_back_and_says_so`;
  - `test_a_service_that_never_answers_falls_back_inside_5_s`;
  - `test_the_database_unreachable_with_the_service_up_is_2_never_a_cached_ok`;
  - `test_a_database_that_does_not_answer_is_2_inside_4_5_s[<case>-<service>]`;
  - `test_a_guard_that_starts_too_late_refuses_2_without_a_read`;
  - `test_a_connect_timeout_above_the_direct_reads_share_is_cut_at_the_parse_start_line[service-up|service-down]`
    (section 4.4's case 5; the delta's "A database address whose connect
    never completes" at `connect_timeout=3`);
  - `test_a_whole_file_fetch_the_start_line_cuts_fills_the_image_cache_for_the_next_prompt`
    (section 4.4's case 6);
  - `test_the_services_request_timer_follows_register_rtt_ms[none|rtt-150]`;
  - `test_each_service_request_is_one_query`;
  - `test_the_services_idle_check_replaces_a_dropped_connection_in_the_background`;
  - `test_a_cache_miss_at_the_service_answers_unavailable_and_fetches_in_the_background[empty-cache|cache-holds-n]`;
  - `test_the_direct_read_connects_as_its_engineers_reader_login`;
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
  - `test_the_guard_answer_time_is_recorded_beside_todays_direct_guard`;
  - `test_todays_direct_guard_is_timed_in_its_four_parts` (M5b);
  - `test_the_guards_register_parse_is_timed_on_a_real_sized_register_beside_todays_six_reads_through_git`
    (M10's parse).
- **Run time.**
  - The byte-identical and fallback cases take about 10–20 s. Today's guard
    took about 2 s wall in its register read on 2026-09-14 (C158–159); the
    measurements put today's read and parse at 0.31 s median (M10).
  - Section 4.4's cases are bounded by their timers, 30–60 s at worst.
  - That is 1–1.5 min in all, before the two measurement cases (30 warm
    runs and the first each) and the idle check's 60 s, which are long rows
    (open point 9).
- **Flakiness.**
  - The assertion is the frame's 4.5 s. Its tightest case is a host silent
    after connecting: the probes' 4 s timer exited at 4006 ms, and SVC-8 now
    cuts the direct read at the parse's start line, `T0 + 3.75 s`, so the
    margin is about 0.75 s less the guard's own start. If CI's runner misses
    4.5 s, the shares are cut, in SVC-8's budget and the register-service
    spec's "The 5 s is one budget, spent once"; the bound is never relaxed,
    because a guard past 5 s is killed and the prompt passes (lane 3,
    comment 6578688322).
  - These cases run on loopback, with no RTT. How a healthy remote
    database's direct read fits SVC-8's shares is SVC-8's "Over a remote
    database" and "A whole file is never lost to the line", and act 3
    records it (section 5.6).
  - The service's timer is asserted from below only, and the idle check
    waits for the design's 60 s interval, with no knob of its own.
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
    (lane 4's section, "The bind as one verb"). The shim acts only on calls
    made under `lanes-edit.sh start-record`, which it finds among its
    ancestors' command lines (`ps -o ppid=,args=`), so `act-begin`'s
    snapshot and the deciding reads, which come before it, reach the
    database and pass.
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
    - the shim fails every lookup call too, so no try that starts within the
      window takes the lock: `start-record` exits 3, about 18 s after its
      snapshot, a time recorded and not asserted (lane 4's section, "The
      in-doubt rule (F3, D8)": the client's wait of about 8 s, then the 10 s
      window);
    - `[69-at-the-commit]`: the shim's `edit-entry` breaks a CHECK in the
      commit call's entry instead (test 2's late failure), so the database
      refuses it with 23514, nothing is in doubt, and `start-record` exits
      69 after a snapshot that gave it the generation;
    - `[69-at-the-snapshot]`: the shim fails `start-record`'s first snapshot
      instead, and it exits 69 with no snapshot to give it a generation
      (open point 38).

    In each:
    - `lane-start` prints a note with the writer's printed rerun, naming the
      write id and its generation (the delta's "A bind that makes no row,
      refused or in doubt"); at `[69-at-the-snapshot]` the test asserts the
      note and the rerun, and no generation, which open point 38 leaves to
      lane 3;
    - the Rule 3 stamp commit is made;
    - the launch goes on, so the fake `claude` runs;
    - the register holds none of the three lines: one transaction, all or
      none.
  - **The stamp shim's new lane,** as the same table gives it. The same shim
    fails `start-record --add-row` as for the resume's 75, 3 and
    `[69-at-the-snapshot]`, and `lane-start` dies with the code, 75, 3 or
    69; on a 3, its stderr carries the
    writer's printed rerun line, AT-C5's rule for every act. The row is a
    write that must exist (2A). In each, there is no row and no stamp
    commit, and the fake `claude` does not run.
  - **The expectation's 2,** a second binding, dies on both paths, and has
    no case here. AT-F5 asserts `lane-start`'s `die 2`, and AT-F6's record
    case that the guard's child records `bind-failed` with `rc=<code>`, for
    2, 69, 75 and 3.
  - **Any other 2** dies on both paths too: a refusal that is not the
    expectation's, such as LR010, a line that derives a key that is not a
    scope (the delta's "A bind refused with another 2"; lane 4's section,
    "The bind as one verb: `start-record`", the table's 2 row). A fake
    `psql` answers `start-record`'s commit call with exit 3 and
    `ERROR:  LR010: key: <path> line <n> derives <key>, which is not a scope
    (…)`:
    - `lane-start` dies with 2 and launches nothing: the fake `claude` does
      not run;
    - the fake's log holds one commit call and no call after it, since the
      same line derives the same key and LR010 is never retried;
    - this holds for a resume and for a new lane (`--add-row`).
  - **`lane-handoff` with the cluster stopped:**
    - the handoff file is published;
    - stderr carries `read: mirror at <sha12>, <k> sequences behind or unknown
      (sql unreachable: <why>)`;
    - the two reads that name the lane, `window-lane` and `swapped`, are the
      ones `lane-handoff` and the handoff skill run with
      `LANES_READ_MIRROR=1` (`code/lane-handoff:1655`, `:1666`;
      `code/skills/handoff/SKILL.md:210`, `:218`), and with it they answer
      from the mirror. The same two reads without the variable exit 69 with
      nothing on stdout, and the variable changes no other arm: `who` and
      `canon-lane` with it set still exit 69 (the phase plan's "The readers'
      SQL path, entered at `register_text_checked`");
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
  - `test_lane_start_resume_launches_with_a_note_and_the_stamp_and_writes_none_of_its_lines[75|3|69-at-the-commit|69-at-the-snapshot]`;
  - `test_lane_start_for_a_new_lane_dies_with_start_records_code_and_writes_no_row_and_no_stamp[75|3|69]`;
  - `test_lane_start_dies_with_2_on_any_2_from_start_record_not_only_the_expectations[resume|new-lane]`;
  - `test_request_handoff_and_claim_refuse_69_with_the_database_down`;
  - `test_lane_handoff_writes_the_handoff_from_a_labelled_mirror_read_and_leaves_the_lane_swapping`;
  - `test_the_read_mirror_variable_lowers_only_window_lane_and_swapped_to_a_mirror_read[window-lane|swapped|other-arms]`;
  - `test_the_late_paused_path_completes_the_swap_once_the_database_returns`;
  - `test_lane_end_refuses_69_with_the_database_down`.
- **Run time.** The day's nine acts at 1–3 s each with their renders, and
  six outage cases at about 2 s, with one more for the new lane's `add-row`
  at 69. The four stamp-shim cases that reach the deadline's lookup (the
  resume's and the new lane's, at 75 and 3, with
  `LANES_REGISTER_DEADLINE_S=1`) are the exception: the lookup waits until
  the database clock passes the largest `not_after` (AT-F3), the snapshot's
  clock plus the 8 s call timer plus 2 s, so the two 75 cases exit about
  10 s after their snapshot. The two 3 cases exit about 18 s after it: the
  shim fails every lookup try, and the 10 s window runs out after the
  client's wait of about 8 s. The two 69 cases that fail at the snapshot,
  and the resume's `[69-at-the-commit]`, have no such wait. The four resume
  cases now also write the stamp and run the fake `claude`, about 1–3 s
  each, and the two "any other 2" cases take about 1 s each. That is
  80–110 s.
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
    ends with one entry;
  - an in-doubt write whose next attempt is refused for another reason makes
    its lookup before it exits (the delta's scenario of that name);
  - a `request-handoff` that finds its write committed and runs none of its
    tail exits 2, not 0 (the delta's "A `request-handoff` that runs none of
    its tail").

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
    The shim's `hold`, armed for the writer's next snapshot, keeps that call
    on its FIFO until the replayed `commit_write` call has returned in the
    test's session, so no retry reaches the head row before the session
    holds it. In the cases that roll back or commit, the session first runs
    `SET idle_in_transaction_session_timeout = 0`, as AT-F3's held session
    does, so the login's 5 s idle bound cannot end it first; the idle case
    keeps the bound.
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
  - **The tail.** Where a case ends with the writer finding its write
    committed (`after`, `timer`, and the half-open case's commit), the
    verb's after-COMMIT tail runs once, from the kept child. That
    assertion is AT-F34's `[snapshot]`, and is not repeated here.
  - **`no-reconnect`:**
    - the writer exits 3, about 18 s after its snapshot.
      The first commit call is lost at once, as `before` or `after` loses
      it, so the write is in doubt, and the writer makes the lookup's tries
      in place of reconnecting (S1, "A write in doubt"; lane 4's section,
      "The in-doubt rule (F3, D8)"):
      - the client waits until its estimate puts `<n>`, the largest
        `not_after` its commit calls carried, at most 2 s ahead. `<n>` is
        the snapshot's clock plus the call's 8 s timer plus 2 s, so that is
        about 8 s after the snapshot. The next attempt's snapshot, refused
        at once, ends the attempts, and the shim's log shows no lookup call
        before the wait ends;
      - the 10 s reconnect window opens when that wait ends. Each try the
        shim refuses with exit 2 is made again, at least 1 s after the
        last, while a new try can still start inside the window. The log
        shows at least two lookup tries, each starting at least 1 s after
        the last (timing rule 3), and none starting more than twice the
        window, 20 s, after the first (timing rule 2), the spread recorded
        against the 10 s;
      - the last try ends at once, so the writer exits 3 no earlier than
        about 17 s after its snapshot (8 s of waiting, and 10 s of window
        less up to 1 s, since a window kept in whole seconds may close
        early; timing rule 3), and about 18 s on an idle host, the figure
        AT-D8's exit-3 cases have from the same derivation. The design's
        upper bound, from the snapshot call's end, is the client's wait,
        `C`, then the window and a last try, `R` + `T_L`: 8 + 10 + 9 = 27 s
        (D9's lookup row). The exit is asserted under twice it, 54 s, and
        its time recorded against 27 s (timing rule 2);

      The frame wins here: D8 names the code the proposal left open. Its
      report names the write id,
      the lookup `lanes-index journal --write-id <id>`, and the rerun
      `LANES_WRITE_ID=<id> LANES_WRITE_GENERATION=<generation> LANES_LANE=<lane> LANES_SESSION=<uuid> lanes-edit.sh <the same arguments>`,
      which carries the register's generation as the pending record does
      (lane 4's section, "The pending record");
    - the id is read from the report, and no outcome is judged from stderr;
    - after the control file is removed, a rerun exits 0 both ways D8
      gives, each a case of its own from its own exit 3, since the first
      rerun to exit 0 removes the record: `[write-id]`, with
      `LANES_WRITE_ID=<id>`, and `[plain]`, a plain rerun of the same command
      line, which finds the pending record (AT-D8);
    - the journal holds one row for the id;
    - the writer never exits 69, and never leaves two `CLAIMED` lines.
  - **A next attempt refused for another reason** (the delta's scenario of
    that name; S1, "A write in doubt", "A write in doubt ends only after its
    lookup"). The shim, armed per call as `fail-call` is, loses the first
    commit call before it lands, as `before` does, so the write is in doubt
    with no row. The second attempt is then refused:
    - **database:** `claim`. The shim's `edit-entry` breaks a CHECK in the
      second commit call's entry (test 2's late failure), so the database
      refuses it with 23514, which is 69 for a write with nothing in doubt;
    - **register:** `append-line` of a `HOLD` line for one repository. The
      shim's `hold` keeps the second snapshot on its FIFO while another lane
      commits a `HOLD` for the same repository, so the second commit is
      refused with LR005 (D18), which is 2;
    - **verb:** `claim`. The same hold, while another lane claims the
      object, so the verb's own pre-check refuses at the second snapshot,
      with 2, before it reaches `ready`.

    In each:
    - the writer does not exit at the refusal: the shim's log holds a
      lookup call, `find_write`, after the refused attempt and before the
      exit;
    - the lookup is made only once the database's clock has passed the
      first call's `not_after`, which the test reads from that call's entry
      as the shim logged it, so the writer exits no earlier than that
      (timing rule 3);
    - the lookup finds the write absent with the lock held, and the writer
      exits with the refusal's own code: 69, 2 and 2;
    - the journal holds no row for the write id, and the pending record is
      removed.

    The outcome where the lookup finds the write, and the writer exits 0, is
    AT-F3's `[found]`.
  - **A `request-handoff` that runs none of its tail** (the delta's scenario
    of that name; lane 4's section, "The tail, once"). The writer is
    `request-handoff`, with test 11's fake `tmux` on `PATH` and a target
    lane in a window. The first run is killed as AT-D8's killed write is,
    after its commit call has returned, so the row has landed and the
    pending record holds its `call` line and no `tail` key. The test then
    reruns the same command line, with the record in one of three states:
    - **tail-sent** and **tail-done:** the test writes the key a dead owner
      would have left, `tail=sent <s> <utc>` or `tail=done <s> <rc> <utc>`;
    - **no-record:** the test reads the write id from the record, removes
      the record and its claim files, and reruns under
      `LANES_WRITE_ID=<id>`, since a plain rerun would mint a new id.

    In each:
    - the rerun finds its write id, and exits 2, not 0;
    - it writes nothing: the journal holds one entry for the id, and the
      fake `tmux`'s log holds no `send-keys` from the rerun;
    - stderr names the tail it did not run.

    The same three states for `claim`, which exit 0, and the rest of "The
    tail, once" are AT-F34's and AT-D8's. The replay that differs, which
    exits 2 for `request-handoff` too, is AT-D8's.
- **Options.**
  - **Under Q16 (a) alone,** a lookup by seq, the test fails by design. The
    probes found the lookup picks a rival's row 3 times out of 3 (P1769–1772).
- **Tests.** In `code/tests/test_sql_register_in_doubt.py`:
  - `test_a_commit_that_landed_before_the_connection_was_lost_ends_with_one_entry_and_exit_0`;
  - `test_a_commit_lost_before_it_landed_is_retried_cleanly_and_ends_with_one_entry`;
  - `test_an_outer_timer_expiry_is_in_doubt_like_exit_2`;
  - `test_a_retry_with_the_old_connection_still_open_writes_no_second_row`;
  - `test_a_callers_rerun_after_a_failed_reconnect_reuses_the_write_id[write-id|plain]`;
  - `test_an_in_doubt_claim_never_leaves_two_claimed_lines_or_a_false_69`;
  - `test_an_in_doubt_write_whose_next_attempt_is_refused_makes_its_lookup_before_it_exits[database|register|verb]`;
  - `test_a_request_handoff_that_runs_none_of_its_tail_exits_2_and_writes_nothing[tail-sent|tail-done|no-record]`.
- **Run time.**
  - The terminate pair took 11–19 ms in the probes.
  - The half-open case waits about 2 s on the writer's `lock_timeout` of
    2000 ms (D9).
  - The timer case waits 8 s.
  - `no-reconnect` waits about 18 s a run, in two runs, one per rerun
    case: the client's wait of about 8 s for `<n>`, then the 10 s window
    (the design's figures, not measured).
  - A next attempt refused for another reason waits about 10 s for its
    lookup, since the database clock must pass the first call's
    `not_after`, the snapshot's clock plus 8 s plus 2 s: three cases.
  - The `request-handoff` cases wait for no lookup: about 3 s each.
  - That is about 110–130 s in all.
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
  - Section 5.6's TLS cluster, a stoppable cluster on loopback TCP,
    bootstrapped by P8's bootstrap through the admin service, as AT-P8's
    ready environment makes it: step 1's readiness check needs check 11,
    which always fails on a Unix socket (AT-LRMPG).
  - A bare origin with the fixture register.
  - Two workstations, ws1 (the operator's) and ws2, each with its own `$HOME`,
    configuration and register checkout. Both start at `mode=index`, under
    round 1's 3A. Each configuration carries `register_label=`, the label
    the bootstrap's `--label` gave the database, since the cutover takes
    its label from that key (S5, "Cutover").
  - **Two engineers' logins,** one per workstation, made by `lanes-index
    login add` after the bootstrap (`operations.md` act 3's step 2, "Then
    the logins"). Each configuration names only its own engineer's two
    logins and password files (§1.4, "One engineer's file").
    Each configuration's URLs carry `sslmode=verify-full` and `sslrootcert=`
    naming the test CA, with `register_addresses=` as AT-P8's check 13
    sets it, and each workstation's installed-tools directory holds the
    release's digest list (check 1).
  - **The freeze** (Q4(a)(i)): no other writer runs during steps 2–3,
    which the drill models by running none.
  - **The ruleset's model** (step 8; AT-F4) rests on Q21(b)'s one open
    point, the org's plan. Brett has named the paths: the render set and
    `lanes/STORE` (comment 6721583757; AT-6).
- **The sequence, and its oracle at each step.**
  1. The readiness check, `ready --expect index`, passes in ws1 and ws2,
     every check `ok` (P612–619).
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
     - `lanes-index status` answers inside twice its bound, AT-D9's
       `status` row (timing rule 2), from a `CLAUDE_NO_LANE=1` context.
     - then an outage declaration is added, after which the guard answers
       from the mirror (Q26 (a′); AT-D20).
  7. A rollback attempted while the cluster is down is refused, and changes
     nothing (Q4(b)(iii)). The cluster is then restarted, and `lanes-index
     outage clear` removes step 6's declaration on both workstations, since
     whether a rollback replaces an earlier declaration is still open
     (section 7, lane 2's question 7 of v3e).
  8. Rollback from ws1, run by the operator lane with `LANES_LANE=` its name
     (F12), declares the outage at its start (AT-D20), stops at
     `--hold-marker` while the ruleset's model is switched off (AT-F4), then
     runs to its end. The flip command, `set-mode index`, then clears ws1
     alone, as Brett does at the rollback's step 5 (§2.3).
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
    - `lanes-index mode` and `lanes-index register read`, in each of its
      forms, exit 78, Q6(c)'s own code, never 2 (D3, F8), and every other
      `lanes-index` command exits 2, each with the text
      `mode=primary is not available in this build: the renderer's git half
      is not installed, so a write could not be mirrored; nothing was
      written`;
    - `lanes-edit.sh`, on a workstation configured `mode=primary`, exits 69,
      repeats that text, and changes neither the register nor the files;
    - the readers answer the 78 as a failed read: a read that decides exits
      69, a listing and the `SessionStart` block answer from the mirror, the
      prompt guard exits 2, and `managed-projection` answers 1 (the delta's
      "Readers under the refusal"; the phase plan's "D3's refusal").
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
  - A files-mode estate on a bare origin, to show the files untouched, and
    to be the mirror the listings and the `SessionStart` block read, with
    the lane's register checkout, `<W>-lanes/<lane>/`, which the render
    fork's `<c>` names.
- **Oracle.**
  - **Refusing.**
    - `lanes-index mode` exits 78, `EX_CONFIG`, Q6(c)'s own code (lane 4,
      comment 6579524611, accepted by lane 3 in 6579562840; D3; `design.md`
      S1, "Entry and mode"; §2.7), and its stderr carries the text exactly,
      or the design's rewording (P1413: reworded but not weakened).
      `lanes-index status`, run on the same configuration, exits 2 with the
      same text.
    - `lanes-index register read`, in each of its forms (the readers'
      `--prefetch` and `--for guard`), exits 78 with the same text, never 2:
      readers and the guard learn the mode from it (S1, "Entry and mode").
    - **The readers,** run from the same estate under the refusal, with the
      fake `psql`'s log empty:
      - a read that decides (`who`, `binding`, `live-holder`, `next-free`,
        `lane-last`, `register-row`, `session-lane`, `canon-lane`,
        `resolve-home`) exits 69 with nothing on stdout;
      - a listing (`lanes`, `history`) and the `SessionStart` block answer
        from the mirror, exit 0, and carry the `read: mirror` line;
      - the prompt guard exits 2;
      - `managed-projection` answers 1, UNKNOWN, never 8.
    - `lanes-edit.sh log NOTED`, and each of S1's verbs, exits 69, and its
      stderr repeats the text.
    - The fake `psql`'s log is empty, so no connection was made.
    - The files, the HEAD and origin are unchanged.
    - `lanes-index cutover`, `rollback` and `set-mode primary`, run against a
      schema that is not a scratch schema, exit 2 (the phase plan's "D3's
      refusal").
  - **Flipped.**
    - The same `log NOTED` exits 0, and the journal gains one entry.
    - The shim's log holds one attempt, two calls (Q7 (A″)): the snapshot
      and the commit, sent with `register_url=` and `register_passfile=` and
      no `PGPASSWORD`.
    - The files are unchanged, since there is no git half in this build.
  - **The constant** (the phase plan's "D3's refusal" and "Phase 3").
    `RENDER_GIT_HALF` sits beside `SCHEMA_VERSION` in the checkout's
    `lanes-index`, and phase 3's last PR lifts it. It is True exactly when
    `lanes-index render --checkout` exists: the test reads the constant
    from the source, and asks the same copy whether it knows that command.
  - **The render fork** (phase W's stub test; lane 4's section, "What phase 2
    ships for phase 3", "The renderer fork"). In the flipped state the
    `lanes-index` the verb finds is a logging wrapper that records each
    call's argv, environment and open descriptors, and then runs the
    flipped copy. After a primary write commits, the wrapper's log holds
    one `render` call, made by a detached fork:
    - its argv is `render --checkout <c>`, `<c>` being the writing lane's
      checkout, `<W>-lanes/<lane>/`, from the resolver and computed without
      S3;
    - its environment has no `LANES_NO_FETCH`, though the verb ran with it
      set in `[no-fetch-set]` (D12), and the fork is made though
      `LANES_INDEX=off` is set in `[index-off-set]`, since `lane-worktrees`
      sets that variable on every call and it must never skip the fork;
    - its stdin is closed or on `/dev/null`, its descriptors 3 to 9 are
      closed, as `index_nudge`'s fork closes them;
    - the writer exits with the verb's own code while the wrapper holds the
      `render` call on a FIFO, so no fork waits, and the flipped copy's
      answer to it, "git half not installed", is never read.
- **Options.**
  - **The text** is the design's to reword (P1413). The test pins whatever the
    design prints.
- **Tests.** In `code/tests/test_sql_register_build_flip.py`:
  - `test_mode_primary_is_refused_until_the_git_half_and_goes_to_sql_once_flipped[refusing|flipped]`;
  - `test_lanes_edit_asks_lanes_index_mode_only_where_a_configuration_exists`
    (D3): with no configuration file and `LANES_INDEX_CONFIG` unset, a logging
    stand-in at `lanes-index`'s path records no call, and the verb writes in
    files mode;
  - `test_mode_primary_with_sqlite_is_refused` (P162–163);
  - `test_register_read_exits_78_under_the_refusal[readers|for-guard]`;
  - `test_the_refusal_constant_is_true_exactly_when_render_checkout_exists`;
  - `test_a_primary_write_forks_render_checkout_on_the_lane_checkout_detached[plain|no-fetch-set|index-off-set]`
    (the phase-W stub test of the render fork's argv);
  - `test_readers_under_the_refusal_answer_the_78_as_a_failed_read[<read>|listing|session-start|guard|projection]`.
  - The readiness item is in P8's test (below).
- **Run time.** About 1–3 s for 14 verbs in the refusing state and one flipped
  write, about 1–2 s more for the readers' cases, and about 1 s for each
  of the render fork's three.
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
  - each role is refused outside its scope;
  - REGISTER-WRITER and READER are groups that cannot log in: each
    engineer holds them through a writer login and a reader login of their
    own, and the dump job through `lanes_register_dump`, a member of READER
    alone (§1.4, "One engineer's logins"; the delta's "The three roles").

  The bootstrap's admin connection is used once and held by nothing, and no
  role can grant, create or act as superuser (P607–611).
- **Phase and kind.** N. RE: "a fake `psql` cannot enforce a GRANT" (P692–693).
- **Fixture.**
  - The session cluster, whose template database was bootstrapped once.
    Roles are global to a cluster, so they are made once per session, and the
    GRANTs travel with each database cloned from the template (section 3.1).
  - Each login connects with `psql -U <login>` over the socket: the
    fixture engineer's `lanes_w_fx` and `lanes_r_fx`, the dump job's
    `lanes_register_dump`, and the indexer, made as section 3.1 makes them.
    The two groups cannot log in, so each group's row of the matrix is read
    through its logins.
  - Authorization is what is tested. Authentication is open point 6.
- **Oracle.**
  - **The matrix.** For every role × object × privilege, an allowed statement
    succeeds and a refused one fails with SQLSTATE 42501, read with
    `-v VERBOSITY=sqlstate`.
    - The objects: each table and view of `lanes_register`, the journal, the
      switch row, each table of `lanes_index`, the six functions a role may
      hold (`commit_write`, `switch_to_files`, `record_marker`, `find_write`,
      `record_mirror_state` and `render_file`), the five scope functions that
      no role may execute (`line_scopes`, `doc_line_scopes`,
      `entry_new_keys`, `scope_fault` and `scope_checked`), each sequence
      of both schemas, and each schema (CREATE).
      `find_write` takes the head row `FOR UPDATE` as SECURITY DEFINER, so a
      READER that could execute it could lock the head.
    - The privileges, by object kind, since PostgreSQL refuses some pairs
      before it checks a privilege: SELECT, INSERT, UPDATE, DELETE and
      TRUNCATE on each table; SELECT on each view, and INSERT, UPDATE and
      DELETE only on a view `information_schema.views` reports updatable,
      so none on `main_slots`, whose window function makes a write to it
      fail with 55000 first; no TRUNCATE on a view or a sequence, which
      fails with 42809 first; SELECT on each sequence, and `nextval()`,
      which needs USAGE or UPDATE; EXECUTE on each function; and CREATE on
      each schema.
  - **The expected matrix** is read from `design.md`'s "The schema", §1.4
    ("Roles, GRANTs and credentials"): its GRANT block and its table of
    roles, never from the bootstrap's own output, so the test does not check
    the bootstrap against itself. There:
    - REGISTER-WRITER has SELECT on `lanes_register`'s tables, EXECUTE on
      `commit_write`, `switch_to_files`, `record_marker`, `find_write`,
      `record_mirror_state` and `render_file`, no EXECUTE on the five scope
      functions, no INSERT, UPDATE, DELETE or DDL anywhere, and nothing in
      `lanes_index`. The `find_write` case calls its six-argument form, the
      write id, `not_after`, the generation, the lane, the verb and
      `args_sha256`, so a grant left on an older signature fails the test;
    - READER has SELECT on both schemas' tables and on `lanes_register`'s
      sequences, and EXECUTE on `render_file` only;
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
    test revokes CONNECT from PUBLIC, which a new database grants it, and
    from READER: the reader login's connection to it is
    refused with 42501, since its CONNECT comes through the group, and its
    connection to the first still succeeds. No
    role is changed on the session cluster.
  - **The role flags.** `pg_roles` shows none of the bootstrap's roles or
    the logins with `rolsuper`, `rolcreaterole` or `rolcreatedb`.
    `rolcanlogin` is false for the owner, `lanes_register_owner`, and the
    two groups, and true for the indexer and each login. A connection as
    either group is refused, FATAL 28000. The bootstrap creates no other
    role, and no engineer's login, which is `lanes-index login`'s (§1.4;
    `operations.md` §2.4).
  - **The floors** (§1.4's login step), exactly. Each reader login's own
    `rolconfig` holds `default_transaction_read_only=on`,
    `lock_timeout=1000ms` and `statement_timeout=1500ms`, the dump job's
    login's among them, and each writer login's holds `lock_timeout=2000ms`,
    `statement_timeout=5s` and `idle_in_transaction_session_timeout=5s`
    (D9). The two groups, the owner and the indexer carry none, since a
    role's settings reach no member. `SHOW` in each login's own session
    gives the same values.
  - **The admin connection.** After the bootstrap exits, `pg_stat_activity`
    shows no session for the admin role, the test's own excluded by
    `pid <> pg_backend_pid()`. The admin holds the owner and the
    indexer `WITH INHERIT TRUE, SET TRUE` (§1.4), and migrations run as the
    admin under `SET ROLE` (F23), so no new role exists for them.
  - **The holders.** Writers present their engineer's writer login, and
    the service and the guard's fallback their engineer's reader login
    (round 1's 4A; Q11 (a); §1.4's roles table). So the matrix is as above.
- **Options.**
  - Q3, the schema against #160's, names the tables. The matrix's rows
    follow it.
- **Tests.** In `code/tests/test_sql_register_roles.py`:
  - `test_each_role_is_refused_outside_its_scope[<role>]`, one `psql` session
    per login checking its group's whole row, where `<role>` is
    `writer-login`, `reader-login`, `dump-login` or `indexer`, since the two
    groups cannot log in (§1.4);
  - `test_no_register_role_can_grant_create_or_act_as_superuser[<role>]`,
    with the same four, and the owner and the two groups read from
    `pg_roles`;
  - `test_the_writer_and_reader_groups_cannot_log_in[writer|reader]`;
  - `test_a_login_in_a_group_role_keeps_the_floors_of_its_role[writer-login|reader-login|dump-login]`
    (§1.4's login step): its own `rolconfig`, and `SHOW` in its session,
    give the floors above, and the groups' `rolconfig` is empty;
  - `test_the_bootstrap_holds_no_admin_session_once_it_exits`;
  - `test_a_readers_plain_write_fails_25006_and_a_read_write_one_42501`;
  - `test_each_login_role_connects_only_where_granted_connect`.
- **Run time.** About 2 s for four sessions of about 60 statements each,
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
  - each dispatcher arm's class at the read door, on a failed read (lane 4's
    section, "The readers' SQL path, entered at `register_text_checked`"):
    `decide` is 69 with nothing on stdout; `mirror` (`lanes`, `history`,
    `session-start`, and `window-lane` and `swapped` only where the caller
    sets `LANES_READ_MIRROR=1`, which every other arm ignores) reads the
    mirror and prints the `read: mirror` line; `guard` follows SVC-8's
    mapping; `projection` is 1; `none` (the L and F arms) has no door and
    makes no call. A test asserts that every dispatcher arm is classified;
  - a caller that acts on a listing's answer (`lane`, and `lane-end
    --retire-dormant`) refuses with 69 where the database is unreachable
    (`design.md` S3's table of readers; the delta's "A listing a caller acts
    on");
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
      `lane-rename:241` has new text for 3, 75 and 69; the restart skill's
      `lread` (`code/skills/restart/SKILL.md:48`, run with
      `LANES_NO_FETCH=1`) refuses on 69, 75 and 3 through its `*)` arm
      (`:70`), so the skill binds no window, writes no stamp and prints no
      resume command (`design.md` S3's table of readers; the delta's
      "Callers' own reads of the register go through verbs");
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
  - `lane-worktrees`' three direct reads of the register go through a verb;
    on the verb's 69 nothing is deleted and the register is named as
    unreadable, and `lane-worktrees` ends with that site's own code, 0 under
    `--report` (`design.md` S3's table; T404; lane 3, #165 6812295982).
- **Phase and kind.** N: the readers' SQL path and the callers' arms. FP,
  except `managed-projection`'s equality with files mode, which is RE (R3's
  N7: a fake cannot fabricate a register's replies faithfully, as AT-14
  says). The arms' case swaps one verb through `LANES_EDIT`, which
  `lane-start` honours (`code/lane-start:622`).
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
  - For `managed-projection`'s equality, no fake: the session cluster's
    `tmpl_imported` database, and a files-mode estate holding the same
    register. Three lanes are set up in both to give each of the verb's
    answers, 0, 1 and 8.
- **Oracle.**
  - **Each reader in the table,** with the store unreachable:
    - the table's exit (69, 1 for `managed-projection`, or 2 for the guard);
    - nothing on stdout where the table says "no answer";
    - never 8 for a read that failed.
  - **The listings and the offline reads** (`lanes`, `history`,
    `session-start`, the launcher's bare window reads) print `read: mirror at
    <sha12>, …` on stderr and exit with their own code; `session-start` and
    the bare reads exit 0.
  - **The classes.**
    - For each `none` arm (`workstation`, `fetch-age`, `window-session` and
      `transcript-holders`), the fake `psql`'s log is empty with the store
      unreachable, and the arm answers as it does today.
    - `window-lane` and `swapped` under `LANES_READ_MIRROR=1`, and the
      variable's being ignored on every other arm, are AT-11's
      `lane-handoff` case.
    - The classification test lists the dispatcher's arms and asserts that
      each is in exactly one class.
  - **A caller that acts on a listing's answer,** `lane` and `lane-end
    --retire-dormant`, exits 69 with nothing acted on, not the mirror's
    answer.
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
    - `lane-end:930–938` refuses 69, 75 and 3 with the verb's own code, and
      `lane-worktrees:508–524` refuses them with 2, naming the code; each
      writes or deletes nothing; `lane-end:2490`
      exits with the code; `lane-handoff:2817` and each arm that already
      refuses pass the code, never 1; the `69)` arm of `lane-handoff:1463`
      and `SKILL.md:112` re-reads from the mirror and prints the
      `read: mirror` line; `lane-rename:241` prints new text for 3, 75 and
      69, and no longer the note that a 3 means the commit is local; the
      restart skill's `lread` exits 1 with its REFUSED line for each of 69,
      75 and 3, so it binds no window, writes no stamp and prints no resume
      command.
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
  - `test_each_mirror_read_prints_its_line_and_keeps_its_exit[<listing>]`,
    where `<listing>` is `lanes`, `history`, `session-start` or the
    launcher's bare window reads;
  - `test_a_none_class_arm_makes_no_register_call_with_the_store_unreachable[workstation|fetch-age|window-session|transcript-holders]`;
  - `test_every_dispatcher_arm_is_classified_for_the_read_door`;
  - `test_a_caller_that_acts_on_a_listings_answer_refuses_69_where_the_store_is_unreachable[lane|lane-end-retire-dormant]`;
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
    `skill-112`, `skill-restart-70` (the restart skill's `lread`),
    `lane-rename-241`, or an arm that already refuses
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
  - `test_managed_projection_answers_0_1_8_from_sql_as_files_mode_does[0|1|8]`
    (RE, on `tmpl_imported`): for a lane set up to give each answer, the
    answer and the exit equal files mode's on the same register (D10, S4);
  - `test_managed_projection_with_the_database_unreachable_exits_1_never_legacy`
    (FP): the fake `psql` exits 2, and the verb exits 1, UNKNOWN, with
    nothing on stdout (D10, S4).
- **Run time.** One run per call in each sequence: a few dozen `lane-start`
  runs at 0.3–1 s, so 30–60 s. The arms' cases and the rerun cases add about
  50 more runs at the same cost, so 15–50 s. The three real-engine
  `managed-projection` cases add about 1 s, and the class cases about 2 s.
  This is the largest FP test,
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
  - The guard's refusal has a `tmux` window and a session record: without
    them the guard refuses with 2 before any read (lane 3's T507 ruling (1)).
    The skip, `status` and `rollback` have neither: there is no lane session.
- **Oracle.**
  - The guard exits 2. Stderr names the database, without credentials, and
    the cause, and carries both commands and `CLAUDE_NO_LANE=1`.
  - With `CLAUDE_NO_LANE=1`, the hook exits 0 at once, without reading its
    blocking stdin.
  - `lanes-index status` answers inside twice its bound, AT-D9's `status`
    row (timing rule 2), with its own exit, naming the unreachable store.
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
  - it prints the password-file line of each login it creates, the
    indexer's, only to a terminal, and stores no secret; each engineer's
    lines are `lanes-index login`'s (AT-LRMPG);
  - it prints the `lanes-index.conf` lines, `register_label=` among them,
    holding the `--label` that the switch took (§2.4);
  - it refuses with 2 an argument that carries a connection string;
  - the readiness check fails on each of its faults: a stale digest, a
    missing `psql`, an early `mode=primary`, a role with a GRANT outside its
    scope, an installed `lanes-index` that still refuses `mode=primary`
    (check 5; Q6(c): `lanes-index mode` exits 78), the workstation's name
    unset (check 6), an outage declaration recorded (check 7), and, with
    `--expect primary`, a switch that is not `primary` with the marker
    `present` or an `origin/main` whose `lanes/STORE` names another database
    (check 8), and tools outside the schema's version window (check 9,
    folded at `21f0191c` from lane 3's ruling 6578020193; §2.4), and, with
    the switch at `primary`, whatever `--expect` says, a `register_label=`
    that is not the switch's label (check 10, §2.4, folded at commit Q from
    V2's S-2 and lane 1's v2.8), and a host its URLs name that resolves to
    more addresses than `register_addresses=` (check 13, §2.4);
  - the flip writes only `mode=` and keeps the file's 0600, and setting
    `primary` refuses with 2 unless the switch is `primary`, the marker
    `present`, and the fetched `lanes/STORE` names this database.

  `bootstrap`'s run by an admin that is not a superuser (§1.4, "On a
  managed PostgreSQL"), its refusal of a database its admin does not own
  (§2.4's refusals), `ready`'s checks 11 and 12, and `lanes-index login`
  are AT-LRMPG's. This test does not repeat their cases: its bootstrap runs
  as the cluster's superuser, and its ready environment makes its logins by
  `lanes-index login add` only as a fixture.
- **Phase and kind.** N. RE for the bootstrap, the ready environment, the
  GRANT check and check 10, which reads the switch, FP for the digest,
  `psql` and refusing-build
  checks, and FM for the flip.
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
    accepted by lane 3 in 6579562840).
  - The four inputs the switch's NOT NULL columns need: `--admin-service`,
    `--label`, `--repository` and `--lane`.
  - Every run on the socket cluster but one passes `--no-passwords`, since
    that cluster trusts its socket. The one that prints password lines runs
    under a pty (`pty.openpty()`), because the bootstrap refuses when stdout
    is not a terminal.
  - An installed-tools directory with the release's digest list (its form is
    the design's).
  - A `PATH` with `psql` and one without.
  - Configurations at `mode=index` and `mode=primary`.
  - An extra GRANT the test adds.
  - Test 14's refusing copy of `lanes-index`.
  - **The ready environment,** for the readiness check: section 5.6's TLS
    cluster, since check 11 always fails on a Unix socket, where libpq
    ignores `sslmode` (AT-LRMPG's check 11). The bootstrap runs there under
    the pty, through an admin service of its own. One engineer's two
    logins are made by `lanes-index login add` under the pty, as AT-LRMPG
    makes them, so check 12 finds their floors. The configuration names
    only those two logins and their password files, with
    `sslmode=verify-full` and `sslrootcert=` naming the test CA in each URL
    and `register_addresses=` set to the count of `localhost`'s addresses
    (check 13). The installed-tools directory holds the release's digest
    list (check 1).
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
    - Under the pty, it prints one password-file line, the indexer's, the
      one login it creates; the owner and the two groups cannot log in and
      get none (§2.4's output; the delta's "No secret is stored"). Through a
      pipe, without `--no-passwords`, it refuses and creates nothing.
    - No new file under the test's `$HOME` or working directory holds a
      secret.
    - The `lanes-index.conf` lines it prints hold `register_label=<label>`,
      the first run's `--label`, which the switch now names, and no secret.
    - `--admin-service 'host=… user=…'`, a connection string, exits 2.
    - In the FP case, the fake `psql`'s log shows no service, `-d` or
      connection string in any argv, `PGSERVICE` set in each call's
      environment, and none of the four dropped variables, though the test
      set all four before the run.
  - **The readiness check.**
    - Each fault case is the ready environment with that one fault added. It
      exits 2 and names that fault on its own `FAIL` line, and in the RE
      cases no other line is `FAIL`.
    - The ready environment itself exits 0, with one `ok` line per check.
    - **Check 10,** whatever `--expect` says (§2.4, check 10; V2's S-2). The
      fixture's configuration holds a `register_label=` equal to the label the
      bootstrap gave the switch. The test sets each switch through the admin
      connection under `SET ROLE lanes_register_owner`: to `primary` with
      the marker `present`, giving fixed values to the columns the switch's
      CHECKs then require (`import_commit`, `import_tree`, `import_seq`,
      `marker_seq` and `marker_commit`), since check 10 reads only the
      switch's mode and label. The `files` case keeps the bootstrap's
      switch, and the second database, bootstrapped with another `--label`,
      has its switch set to `primary` the same way. Then:
      - with the switch at `primary`, check 10 is `ok`;
      - with the label changed and `register_url=` left, or with
        `register_url=` re-pointed at a second bootstrapped database whose
        switch names another label and the label left, check 10 fails, the
        run exits 2, and the line reads `register_label=<x>, but the switch
        names <y>`, naming both labels;
      - with the switch at `files`, check 10 does not fail, whatever
        `register_label=` says;
      - on a restored backup's switch, which still names the lost database's
        label, check 10 fails by design on a correctly re-pointed host until
        the cutover sets the replacement's label: AT-D22's restore case.
    - **Check 13,** the address count (§2.4, check 13; D9's `N_a`). The URLs
      name `localhost`. The test counts its distinct addresses itself, by
      the call libpq makes, `getaddrinfo` with `SOCK_STREAM` and any family,
      and sets `register_addresses=` to that count: check 13 is `ok` and
      prints the count and `N_a`. Set one lower, where the count is 2 or
      more, it fails, naming the host, the count, `N_a` and the addresses,
      and the run exits 2. A URL naming `127.0.0.1` counts 1 and passes with
      no `register_addresses=`. The fake `psql`'s log is empty: the check
      reads only the resolver. Where the runner resolves `localhost` to one
      address, the failing case is recorded as not run, with the count.
  - **The flip.**
    - A diff of the configuration file shows only the `mode=` line.
    - The file stays 0600.
    - The password file is unchanged.
    - A configuration the flip cannot write is refused, with nothing
      written. A read-only mount is flipped from the host (P620–622).
- **Tests.** In `code/tests/test_sql_register_operator_tools.py`:
  - `test_bootstrap_run_twice_leaves_one_schema_and_the_same_grants`;
  - `test_bootstrap_prints_password_file_lines_and_stores_no_secret`, under
    a pty, which also reads the printed `lanes-index.conf` lines for
    `register_label=`;
  - `test_the_bootstrap_refuses_when_stdout_is_not_a_terminal`;
  - `test_the_bootstrap_refuses_a_connection_string_argument_with_2`;
  - `test_the_bootstrap_passes_the_admin_service_only_as_pgservice` (FP);
  - `test_set_mode_primary_refuses_before_the_marker_is_present_or_names_another_database`;
  - `test_the_readiness_check_fails_on_each_fault[<fault>]`;
  - `test_the_readiness_check_passes_a_ready_environment`;
  - `test_the_readiness_check_10_compares_register_label_with_the_switch_label[equal|label-only|url-only|files]`;
  - `test_the_readiness_check_13_compares_each_hosts_address_count_with_register_addresses[equal|above|ip-literal]`;
  - `test_the_flip_writes_only_mode_and_keeps_0600`;
  - `test_the_flip_refuses_a_configuration_it_cannot_write`.
- **Run time.** About 5–10 s. Check 10's cases add about 3 s: a second
  bootstrapped database and a few `ready` runs; check 13's, under 1 s. The
  ready environment's TLS cluster and its two `login add` runs add about
  2 s.
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
    claims' reads of it;
  - it holds in both orders: the clone that has the alias claims first, or
    the clone behind it does.

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
    `origin/main` at T0 (D19; `code/lanes-edit.sh:16509–16510`:
    `resolve_home`, then `canon_object`'s alias read). T1 is then
    pushed.
  - B then claims X, and A is released: the case `[alias-holder-first]`.
  - **`[behind-the-alias-first]`,** the order AT-D19 does not stage: A, at
    T0, claims X, which at T0 is no alias, and commits. T1 is then pushed,
    and B, which fetches it, claims O.
- **Oracle.**
  - **`[alias-holder-first]`** (S6, "Claims"):
    - exactly one `CLAIMED` holds O in SQL, B's;
    - the exit codes are {0, 2};
    - no line holds X as an object of its own;
    - A's held commit call loses its turn, with 40001 or LR001 (§1.3, "The
      signals a writer gets"), and its next snapshot shows B's `CLAIMED` on
      O, so the verb's own pre-check refuses it with 2 before `ready`: the
      shim's log holds no second commit call from A. D19's spelling check
      is not reached in this order.
  - **`[behind-the-alias-first]`** (D19):
    - A exits 0, and B exits 2 and writes nothing, its write id absent
      from the journal;
    - B's verb sees no hold on O, since it compares an event's object as
      written (`code/lanes-edit.sh:3939`) and A's line holds X, and
      `one_live_claim` compares one object column. So the refusal must be
      LR005 from the commit's spelling check, with the DETAIL token
      `claim-spellings`, on B's commit call in the shim's log: B's set of
      spellings, O and X, meets A's live claim's;
    - exactly one live `CLAIMED` holds O or X: A's, on X.
- **Tests.** In `code/tests/test_sql_register_order.py`:
  - `test_an_alias_edit_racing_a_claim_cannot_let_two_claims_of_one_object_pass[alias-holder-first|behind-the-alias-first]`.
- **Run time.** About 2–3 s a case.
- **Flakiness.** The race is staged by holds, not by timing.

### The frame's own choices

The frame asks for a test of each of its own choices. Four are
inside entries above:
- `register-row-local` on primary (D11) is in test C5;
- the marker's fields (D21) are in test 7's cutover;
- the refused hold line that names no repository is test 4's case (f);
- the declared outage's use by the guard is in test 10.

The five below have entries of their own, and AT-D9c, beside AT-D9, holds
D9's cases over a remote database (lane 4's, #165 6722839180). Test D19 is
lane 3's ruling 6 (#165 comment 6578020193).

#### AT-D8. The pending record's rerun

- **Proves.** D8, and `design.md` S1's "The write id" and "A write in doubt":
  - before the first commit call, `lanes-edit.sh` writes
    `<state>/register-pending/<digest>`, which holds the id, the generation,
    the UTC, the command line and one `call` line for each commit call that
    may have committed (lane 4's section, "The pending record");
  - the record is removed once the outcome is known: on 0 after the tail is
    done, its `tail` value first going into the done mark; and, where it
    holds a `call` line, only after F3's lookup has settled the write;
  - a later run of the same command line, with no `LANES_WRITE_ID`, reuses
    the id, says so on stderr, and consumes the record, so only the first
    rerun is the retry;
  - a write killed by an outer timer, which can print nothing, is still
    retried under its id;
  - `lanes-index status` lists the pending records and resolves each by
    looking its id up;
  - a run that finds its write committed runs the verb's after-COMMIT tail
    once, in the kept child of the call that committed or in a replay at the
    entry's base, and runs none of it where the record shows the tail sent
    or done or where there is no record (lane 4's section, "The tail,
    once"; `design.md` S1's "A write in doubt", with its table of tails).
    AT-F34 tests the rule itself. Here each verb that has a tail has a test
    of its own, as lane 3 ruled (comment 6710770304), and a rerun after a 3
    replays, which AT-F34 leaves to this test;
  - a run killed after its `call` line is written, or mid-call, is taken
    over by a next run that is in doubt from its start. Where its snapshot
    passes and its verb refuses before `ready`, it waits past that call's
    `not_after` before it ends with any code but 0. Where its snapshot
    fails, it has no clock and calls the lookup at once, and LR011's 3 is
    AT-F3's `[too-early]` (open point 36).
- **Phase and kind.** W. RE, with test 12's shim, and a fake `gh` and
  `tmux`.
- **Fixture.**
  - The imported register.
  - `lanes-index`'s state directory, under the test's `$HOME`.
  - The shim's `no-reconnect` mode.
  - For the killed write, the shim's `timer` mode, joined to a FIFO: once the
    real `psql` has returned, the shim writes to the FIFO and blocks. The test
    reads the FIFO and then sends SIGKILL to `lanes-edit.sh`'s process group.
    The row has then landed, and the writer cannot print. No wall-clock timer
    decides the order. The kept child at the seam dies with the group, so its
    `seam.out` is never written, and a rerun replays (lane 4's section,
    "The tail, once").
  - For the tails:
    - a fake `gh` and a fake `tmux`, each logging every call with its pid;
    - a legacy lane with a control root and a `lane-state.yaml`, the only
      kind `lane_state_follow` moves (`code/lanes-edit.sh:13817–13880`);
    - for `request-handoff`, a holder lane bound to a pane of the fake
      `tmux`, run with `--wait 2` and `LANES_POLL_SECONDS=1`;
    - for `rename-lane`, the lane's handoff file in its checkout.
  - For a kill before the send or mid-call:
    - before the send, the shim holds the commit call on a FIFO before it
      starts the real `psql`; mid-call, the test's admin session holds the
      head row `FOR UPDATE` while the shim logs that the real `psql` has
      started;
    - the test reads the FIFO, then sends SIGKILL to the process group;
    - mid-call, the admin session then rolls back; in both cases another
      lane then claims the same object, the writer being `claim`, before
      the rerun starts;
    - whether a mid-call kill's call lands is not asserted: the rerun
      settles it.
- **Oracle.**
  - **After a committed write,** no pending record and no claim file remains,
    and the done mark `<state>/register-done/<write_id>` does: mode 0600 in a
    0700 directory, holding the write id, the seq and the `tail` value,
    `done`.
  - **After exit 3,** the record exists, names the id, and holds the `call`
    line of each call that may have committed: its base `n`, the snapshot's
    clock, its `not_after`, the sha256 of its `files` list and its attempt
    directory. No other key says the write is in doubt.
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
    tail, once, and writes nothing again. Lane 3 ruled it so (lane 4's row
    24, comment 6710770304). The design's table of tails, in `design.md`
    S1's "A write in doubt" and in lane 4's section, "The tail, once", says
    which steps are safe twice and which are not, so the rule is "at most
    once". For every verb below the oracle is the same:
    - before the rerun, no effect of the tail has happened;
    - after it, each effect has happened exactly once, and the journal holds
      one row for the id;
    - the owner wrote `tail=sent <s> <utc>` into the record before the tail
      ran, and `tail=done <s> <rc> <utc>` once it ended; the record is then
      removed, and the done mark holds `done`;
    - a second rerun under the same id finds the mark, runs none of the
      tail, and the effects stay at one.
  - **Each verb's tail.** The verbs with no tail (`set-row-state`,
    `replace-in-row`, `append-session-id`, `append-line`, `add-row`,
    `archive-rows` and `migrate-state-cells`) have none of their own: the
    killed write's rerun above shows an exit 0 and one row.
    - `log`, for the four verbs `lane_state_follow` moves, `STARTED`,
      `RESUMED`, `ENDED` and `RETIRED`: the lane's `lane-state.yaml` goes
      from generation g to g+1 once, to `RUNNING` or `CLOSED`, using the
      committing attempt's pre-image from the `call`'s `preimages` file.
      Where another act has moved the lane since the kill, the replay's fence
      refuses (`code/lanes-edit.sh:13848`): the snapshot stays as that act
      left it, a note says the lifecycle moved under the line, and the rerun
      still exits 0;
    - `claim`: AT-F34's verb. Its tail is `lane_state_follow` and
      `gh_comment` (`:16695`), which is not safe twice, and AT-F34's oracle,
      one fake `gh` call from the kept child or from the replay, is its test;
    - `release`: the same, for its one comment (`:16744`);
    - `request-handoff`: the kept child ran the tail, so the fake `tmux` logs
      one `send-keys` (`guard_type`, `:17242`) and the poll reads and ends the
      run (`:17254–17286`). The request is in the register once. A replay's
      typing, which is none, is AT-F34's
      `test_a_replay_of_request_handoff_types_into_no_pane`;
    - `retire-rows`: each retired lane's sidecar goes through its follow
      once (`:12947–12952`), with its own pre-image from `preimages`;
    - `rename-lane`, the existing test: `lane_state_rename` moves the old
      control root to the new name once, and a second call finds it gone
      (`:16273`, `:13886`). The handoff's move and stamp run once, so the
      handoff file at the new path holds one `RENAMED from <old>` line under
      its header, never two. The fake `gh` logs one comment per held claim
      (`:16284–16303`);
    - `start-record`: `lane_state_follow` runs once after the lock, as for
      `write_event` (lane 4's section, "The bind as one verb:
      `start-record`"): one move of the lane's sidecar.
  - **A call in doubt that committed,** settled by the snapshot, by LR006 or
    by F3's lookup, with the kept child alive or gone, is AT-F34's: its two
    tests hold the kept child's tail and the replay at `s−1`.
  - **A rerun after a 3 replays.** The shim's `no-reconnect` mode ends the
    first run with 3, which abandons its kept child, and no comment has been
    made. After the control file is removed, a plain rerun finds the record
    with its `call` line, its snapshot finds the id, and it replays at `s−1`
    (one `--at` call in the log) and runs the tail: one comment, exit 0.
  - **A sent record whose child never read `committed`, and an owed mark.**
    `tail=sent` found after a kill, and the done mark of a run that read
    `sent` or `done`, are AT-F34's. These two states are not:
    - `[sent-child-never-read]`, a record that holds `tail=sent` while the
      dead child's `seam.out` says `none`, so it never read `committed`: the
      test builds the state from a killed run's own files. The owner removes
      `tail` and replays: one comment, exit 0;
    - `[owed-mark]`: after the `[rename-lane-stops]` replay below, the mark
      holds `owed <utc>`. A rerun under `LANES_WRITE_ID=<id>` runs no tail,
      adds no comment, and names the steps as owed again.
  - **No pending record,** whether the done mark is there, or the mark is
    gone and `journal.workstation` is this host or another, is AT-F34's.
    `request-handoff`'s exit 2 for the state is AT-12's.
  - **A replay that stops or differs.** AT-F34's `claim` copy holds the
    general case. These are the two verbs' own. The write is committed
    either way. The rerun removes the record after writing the done mark
    with `owed <utc>`, prints one WARNING, and exits 0, with no tail run.
    For `rename-lane`:
    - `[rename-lane-stops]`, the handoff file is removed from the lane's
      checkout, so the replay's stamp checks refuse before the seam
      (`code/lanes-edit.sh:16190–16195`): the text says that, replayed at
      seq `<s−1>`, the write now stops before its write, with its exit and
      last line, and names the steps that did NOT run;
    - `[rename-lane-differs]`, the handoff file is moved to the new path by
      hand, so the row's handoff cell no longer changes (`:16227–16229`) and
      the replay's change set differs from the entry's: the text names the
      first path whose digest differs.

    `[request-handoff-differs]`: the fake `tmux` now reports the holder's
    window as gone, so the replay's request line differs. The run exits 2
    instead, saying the request is written at seq `<s>` and the run did not
    wait for the release.
  - **A run killed after its `call` line.** The record holds the `call` line
    and its `not_after` as soon as the kill ends the run.
    - A next run, a plain rerun of the same `claim`, takes the record over
      and is in doubt from its start.
    - Where the killed call did not land (before the send, or a mid-call
      call that did not), the other lane's claim has committed. The rerun's
      snapshot passes, and its verb's own pre-check refuses at it with 2,
      before `ready` (S6, "Claims"), so it sends no commit call of its own.
      It has that snapshot's clock, so it waits on its own estimate, then
      makes the lookup with `<n>` equal to the record's `not_after`, which
      finds the write absent with the lock held. It exits 2, its last
      attempt's code, only after the database's clock has passed that
      `not_after` (timing rule 3): the admin session reads
      `clock_timestamp()` after the exit, and the value is past it. The
      record goes.
    - Where the killed call landed (a mid-call kill), the other lane's claim
      is refused with 2, and the rerun's snapshot finds the id: it exits 0
      and runs the tail once.
    - The journal never holds two rows for the id.
    - A next run whose snapshot fails has no snapshot clock, so it calls the
      lookup at once (the phase plan's "The in-doubt rule (F3, D8)", step
      1). With `<n>` more than 3 s ahead, LR011 refuses it, and the run
      exits 3, naming the id, and keeps the record. That is AT-F3's
      `[too-early]`, not repeated here; open point 36 asks lane 3 about the
      phase plan's "The pending record", which says such a run still waits.
  - **The `call` lines** (lane 4's section, "The pending record"). One line,
    `call=<base> <clock> <not_after> <files> <dir>`, stands for each commit
    call that may have committed.
    - It is written, by temp file and `mv`, before the call is sent: while the
      shim holds the call on its FIFO, the record already holds it.
    - It is dropped only where an answer shows the call did not commit, a
      lost turn or a refusal. After a 40001 from a rival write's commit, the
      record read while the shim holds the next call holds one line only,
      the next call's, with base `n+1`. After a refusal (AT-D19's
      `[behind-the-alias]` case) the record is gone.
    - After a call in doubt the record keeps the line, and the attempt
      directory it names keeps `snapshot`, `files` and `preimages`.
  - **The claim files.** A run owns the record through a claim file beside
    it, `<digest>.take.<k>`, created with noclobber and holding the run's pid,
    start time, host, container and UTC. The owner is the run in the
    highest-numbered claim.
    - A run with no record creates `<digest>.take.0`: the test reads it while
      the shim holds the first commit call.
    - After a kill, a rerun finds the highest claim's owner dead and creates
      `<digest>.take.1`, and is the owner.
    - Two printed reruns under `LANES_WRITE_ID` are released together from a
      barrier against a dead owner, in 10 rounds. In each round exactly one
      create of `<digest>.take.1` succeeds. That run replays and runs the
      tail. The other waits while the highest claim's owner lives, then finds
      the write by its snapshot and the done mark, and runs no tail. Each
      round leaves one comment and one journal row, and both runs exit 0.
    - After a run's end, no record and no claim file remains.
- **Options.** Under Q16 (a) alone there is no record, and the test is not
  written.
- **Tests.** In `code/tests/test_sql_register_in_doubt.py`:
  - `test_a_committed_write_leaves_no_pending_record`;
  - `test_a_plain_rerun_reuses_the_pending_write_id_once_and_says_so`;
  - `test_a_write_killed_by_an_outer_timer_is_retried_under_its_own_id`;
  - `test_a_rerun_with_other_arguments_does_not_take_the_pending_id`;
  - `test_lanes_write_id_wins_over_a_pending_record`;
  - `test_status_lists_and_resolves_pending_records`;
  - `test_a_rerun_that_finds_its_write_committed_runs_the_verbs_after_commit_tail`:
    `rename-lane`'s tail;
  - `test_the_after_commit_tail_of_log_runs_once_when_a_rerun_finds_the_write[RESUMED|ENDED|RESUMED-moved-since]`;
  - `test_the_after_commit_tail_of_release_runs_once_when_a_rerun_finds_the_write`;
  - `test_the_after_commit_tail_of_request_handoff_types_once_in_the_kept_child_and_polls`;
  - `test_the_after_commit_tail_of_retire_rows_runs_once_when_a_rerun_finds_the_write`;
  - `test_the_after_commit_tail_of_start_record_runs_once_when_a_rerun_finds_the_write`;
  - `test_a_rerun_after_a_3_replays_the_verbs_tail_once`;
  - `test_a_sent_record_whose_child_never_read_committed_replays_and_an_owed_mark_runs_no_tail[sent-child-never-read|owed-mark]`;
  - `test_a_replay_of_rename_lane_or_request_handoff_that_stops_or_differs_runs_none_of_the_tail[rename-lane-stops|rename-lane-differs|request-handoff-differs]`;
  - `test_a_run_killed_after_its_call_line_or_mid_call_is_taken_over_in_doubt_and_waits_past_not_after[before-the-send|mid-call]`;
  - `test_the_pending_records_call_lines_are_written_before_the_send_and_dropped_only_for_a_call_that_did_not_commit[before-the-send|lost-turn|refusal|in-doubt]`;
  - `test_a_pending_record_is_owned_through_numbered_claim_files_and_two_reruns_never_both_take_it[take-0|take-1|two-reruns]`.
- **Run time.** About 2.5–3 min on loopback.
  - Each case that needs an exit 3 pays about 18 s: the client waits until
    `<n>` is within 2 s, about 8 s after a call that fails at once, and
    then the 10 s window of tries, each failing at once (lane 4's section,
    "The in-doubt rule (F3, D8)"). Five of the tests need one: the plain
    rerun's, the other-arguments one, the `LANES_WRITE_ID` one and `status`'s,
    and the replay's after a 3.
  - The two cases that wait for a `not_after` (`before-the-send` and
    `mid-call`) take about 10 s each, as AT-F3's cases do.
  - The kill and replay cases take 1–3 s each.
  - The two-reruns case makes 10 rounds of about 3 s, so about 30 s.
  - Over a remote database the timers are D9's in `c` and `N_a`, and these
    figures follow them (AT-D9c).
- **Flakiness.** Every kill is sent on a FIFO signal, never after a sleep:
  - after the real `psql` has returned, so the row is known to have landed
    and neither the 8 s timer nor the kill can fire first on a loaded host;
  - from inside the tail, where a fake `gh` blocks on a FIFO;
  - before the send, from the shim's hold.

  The wait past `not_after` is read from the database's clock after the
  run's exit, as an inequality on observed values, never from a sleep.

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
  configuration leaves `connect_timeout=` and `register_addresses=` unset,
  so `c` is 2 s and `N_a` is 1 (section 3.1). The rows are the timeouts
  table's ("The
  timeouts (D9)"), at least one test row for each of its twenty rows, with
  lane 1's `import` row and `reconcile` beside the renderer's reads (§1.3;
  lane 3, comment 6578020193); the commit row has one test row for each of
  F2 and F3. Lane 1's `renames-v2.6.md` (sha256 `19e02501…`) gives six of the
  rows' bounds: the lookup, `cutover`, `switch_to_files`, `record_marker`,
  and the two stray ways out's. The bounds are sums of the design's own
  timers on loopback.
  Each client timer that holds a connect is `N_a`·`c` plus its statement
  bound plus 1 s, never below the row's figure (D9), so at these values
  only the lookup's moves, from S1's 8 s to D9's 9 s (open point 37). The
  rows below are at them;
  other values, and a delayed link, are AT-D9c's. There are four faults:
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
- **Oracle.** The path exits with its row's expiry outcome. The row's last
  column is the design's bound on the elapsed time, and the timing rule
  (section 1.2) says how each part is asserted: the guard's two rows under
  4.5 s, strictly (timing rule 1); every other upper bound at twice its
  value, with the measured time recorded against the design's bound and its
  margin (timing rule 2); and each "at least" as stated (timing rule 3).
  - **A simple row,** one call under one timer, is bounded by its client
    timer, from the call's start in the shim's log.
  - **A composite row** chains timers, so its bound is the sum of the parts
    that can run in sequence, from the faulted call's start. The 10 s
    reconnect window bounds when a lookup try may *start*, not when it ends:
    a try begun
    inside it runs its own timer, `T_L`, to its end, and so does a snapshot. And
    an attempt begun before the deadline may finish after it. What the design
    forbids is an attempt that *starts* after the deadline, or a lookup try
    that starts after its window, so the shim's log of each call's start time
    is asserted too, at twice the deadline or the window (timing rule 2).
  - **A row that follows a healthy call** (a writer's commit, the lookup, a
    cutover's abort) meets its fault only from that call on. The test arms the
    fault once the shim's log shows the earlier call's end, so the call
    before it reaches the cluster.

  | Row | Its timer | Expected | The design's bound on the elapsed time |
  |---|---|---|---|
  | the guard's direct read | its connect `c`, which may exceed its share; the read cut to the parse's start line, `T0 + 3.75 s`; `T0 + 3.65 s` under a declaration naming this database (SVC-8) | 2, for F1 to F3, and for F1 at `connect_timeout=3` (section 4.4's case 5) | under 4.5 s, strictly |
  | the service's connection, per request | 200 ms, or the workstation's round trip plus 100 ms where `register_rtt_ms=` is over 100 (SVC-6), inside the client's wait, the timer plus 100 ms: 300 ms here, and 350 ms at `register_rtt_ms=150`; 150 ms and 180 ms at the server | `unavailable`, then the client's direct read; `refuse db` only where the refusal is definitive (SVC-7); the timer's lower bound is AT-10's | the client's wait: 300 ms here, and 350 ms at `register_rtt_ms=150` |
  | the guard's answer 2 | none of its own: the bind runs detached in `guard-bind`, under a writer's bounds and a bound of its own, `bound_s`, which `guard-bind` computes from `c` and `N_a`: 500 s here, with `timeout -k 10 490` around the body (`LANES_BIND_BOUND_S` for tests; AT-F6's cases 1 and 22) | 2, "BINDING" | under 4.5 s, strictly |
  | a writer's snapshot, and `act-begin`'s | 8 s | F1 and F2: 69, since no commit was sent, and the same for `act-begin`. F3: a failed read, 69, as the table's snapshot row says. Where an earlier commit call of the same write was in doubt, the same failure is in doubt, not 69: the writer goes to its lookup (the commit's F2 row) | `S`, 8 s; where a call was in doubt, the commit's F2 row |
  | a writer's commit, F2 | 8 s; then the next attempt's snapshot, 8 s; then the lookup's tries, each to its own timer, `T_L`, 9 s here, started within a 10 s window that opens at once, since the commit ran its timer | in doubt, then 3, naming the id, the record kept | from the commit call's start in the shim's log: at least `C` + `S` + `T_L` = 8 + 8 + 9 = 25 s, since the first lookup try runs to its timer; at most `C` + `S` + `R` + `T_L` + `p` = 8 + 8 + 10 + 9 + 0.5 = 35.5 s, where the last 9 s is a lookup try begun at the window's end that meets the silent relay, and `p` is the design's 0.5 s for the `lanes-index` starts |
  | a writer's commit, F3 | 2000 ms `lock_timeout` per attempt | 55P03 at about 2 s, a new attempt, then 75 at the deadline (`LANES_REGISTER_DEADLINE_S=3` here) | from the first snapshot call's start: at most the deadline, then one attempt's `S` and `C`, 3 + 8 + 8 = 19 s; no snapshot call starts after the deadline, 3 s |
  | the in-doubt lookup, `find_write` (D9's lookup row) | `c`, 2 s here, the connect; 2000 ms `lock_timeout`; 6000 ms `statement_timeout`; `T_L`, `N_a`·`c` + 7 s, 9 s here, a try, the tries starting within the 10 s window | 3, naming the id, the record kept, since no try took the lock and answered. A failed connect (F1), a try cut by its timer (F2) and a 55P03 (F3) are each tried again at least 1 s after the last, while a try can still start inside the window | from the first lookup try's start: at most `R` + `T_L` = 10 + 9 = 19 s, and at least one try; no try starts after the window, 10 s from the first's start |
  | a whole write | 30 s before a new attempt; 10 s to reconnect in doubt; at most `T_write` in all | 75 (test 4's case (d)); 3 (test 12) | for 75 with no call in doubt, from the first attempt's start, at most the deadline + `S` + `C`, and no attempt starts after the deadline; for 75 after a lookup that answers, only after the database's clock passes `<n>`, the snapshot's clock plus 10 s (AT-F3, AT-11); for 3, the commit's F2 row; in no case over the design's `T_write`, 62.5 + 3`N_a`·`c` s, 68.5 s here (lane 4's section, "The in-doubt rule (F3, D8)") |
  | a reader | `N_a`·`c` + 4 s: 6 s here | 69, with nothing on stdout past its first line, `mode primary` (contract §3.6) | 6 s |
  | the renderer's reads, and `reconcile` | 15 s | the render ends, and `status` then shows the lag; `reconcile` exits 69 | 15 s |
  | `import` | 330 s | FP only, since a 330 s timer is not run: the call carries `PGCONNECT_TIMEOUT=2`, `c` here, and sets `lock_timeout=2000` and `statement_timeout=300s` at connect, and the client timer is 330 s; on expiry, 69 and nothing is swapped in | none run |
  | `status` | 8 s | 69, with the lag from git | 8 s |
  | `rollback`, each call | 15 s | 69. Where step 1's call expires, the rollback reads the switch again and never goes on to step 2 on an unknown switch. Where step 1's call ends in a lost turn (40001, 40P01, 55P03 or LR001), it reads the head afresh and tries again, and at D7's deadline across its tries it exits 75, with nothing written and the switch still `primary` (V3's NIT 2) | one call, 15 s. Where step 1's call and the switch's re-read both expire, 15 + 15 = 30 s. For 75 under F3 (`lock_timeout` 5000 ms, `LANES_REGISTER_DEADLINE_S=6` here), from the rollback's start: at most the deadline, then one more try, its check and its call's 5 s, so 6 s plus one check plus 5 s, where the check is the fetch of `origin main` and the head read, timed from the `git` wrapper's and the shim's logs; no check starts after the deadline |
  | a migration | its own `lock_timeout` | refused, and a rerun succeeds once the reader's transaction ends. A new plain read is not held longer than that `lock_timeout` | the migration's `lock_timeout` |
  | `cutover`, its transaction | 2 s; 5000 ms `lock_timeout`; 10 s `statement_timeout`; 15 s, carrying `not_after` (D24) | 69, with the switch unknown and nothing pushed. A rerun after the fault is removed reads the switch and the journal first, and goes on from its own entry where it finds one (F12) | one call, 15 s; under F3, its 5000 ms `lock_timeout`, 5 s |
  | `switch_to_files`, the cutover's abort, either form | 2 s; 5000 ms; 10 s; 15 s | 69. The rerun reads the switch: at `files`, or back at the restored generation with the head at `b`, the abort is done, and it does not run that abort again; otherwise it runs again | 15 s |
  | `record_marker`, after an operator entry's read-back | 2 s; 5000 ms; 10 s; 15 s | 69. The push stands and the marker stays `adding` or `removing`, so a writer's commit still refuses; the rerun reads origin back and records it again (F12) | 15 s |
  | `outage declare`'s database probe (§2.5) | 2 s; 2000 ms; 5000 ms; 8 s | on a person's word, the declaration is written, since the database does not answer from this host. In the `--rollback` form, 2 and no file, the switch being unread. Where the probe does read the switch, AT-D20 owns the rules | 8 s |
  | `import --single <sha>` and `render --revert <sha>`, their commit call | 2 s; 2000 ms; 5000 ms; 8 s a call, under D7's 30 s deadline | in doubt, as a writer's commit: the lookup, then 3 | the commit's F2 row, and no call starts after the deadline |
  | the same two, their other database calls | 2 s; 2000 ms; 5000 ms; 8 s a call, under D7's 30 s deadline | the snapshot read: 69, or in doubt where an earlier commit call of the same entry was; the lookup: its row above; the render that follows: the renderer's row | 8 s for the snapshot |
  | the mode query, `lanes-index mode` (lane 3, comments 6710770304 and 6711028142) | a 2 s client timer; no `connect_timeout`, `lock_timeout` or `statement_timeout`, since the query reads only the configuration | the query killed; files mode, with a warning naming the timeout (F8's table); on a SQL estate the marker check after S3's fetch then refuses with 69, and nothing is written (D3; the delta's "The mode query fails") | the query's 2 s timer. Its fault is AT-F8's hanging stand-in, not one of F1 to F4, and AT-F8's test owns the case and asserts its bound |

  F4, on every row that sets a `statement_timeout`: the shim's log shows
  the call ending with 57014, the server's bound and not the client timer,
  no earlier than the path's `statement_timeout` after the call's start
  (timing rule 3) and under twice it (timing rule 2), and the path exits
  with its row's outcome. For a
  writer's commit that outcome is 69, a refusal the database made (lane 1's
  "The signals a writer gets"). The slow statement sits where the row's call
  works:
  - a trigger on `journal` for a writer's commit, the cutover and its abort,
    the rollback's step 1, and the two ways out of a divergence;
  - a trigger on `register_switch` for `record_marker`;
  - a policy on `register_head` for the readers, and on `register_switch` for
    the probe's REGISTER-WRITER;
  - for the lookup, a policy forced on `journal` for its owner, since
    `find_write` runs as the owner. The writer then exits 3 naming the id,
    and whether a further try follows a 57014 is not asserted.

  The operator rows run the real commands:
  - **`cutover`:** on `tmpl_imported` and a bare origin with the import
    commit, as test 7's cutover has them. The fault takes the transaction's
    call, so no marker is pushed.
  - **`switch_to_files`:** reached by a cutover whose push the `git` wrapper
    fails. Its call meets the fault. Once the fault is removed, after the
    shim's `before` mode (not landed) the rerun runs the abort again, and
    after its `timer` mode (landed) the rerun reads the switch at `files` and
    runs no abort for that entry: at `files` with the marker `absent` it goes on
    as a first cutover would (Y:1531), so its first write call is `commit_write`,
    never `switch_to_files` (lane 3's batch 11 (7)).
  - **`record_marker`:** reached after the cutover's push and read-back. The
    fault takes its call. The marker reads `adding`, a writer's commit
    refuses with 69, and the rerun records the marker once.
  - **`import --single` and `render --revert`:** on test 6's stray commit.

  Beside the table: a session as `lanes_register_writer` that replays a
  commit call's script without its `COMMIT` (test 12's held session) holds
  the head row and idles. `idle_in_transaction_session_timeout` ends it no
  earlier than 5 s after it idles (timing rule 3) and under twice that
  (timing rule 2), after which a waiting write commits.
- **Tests.** In `code/tests/test_sql_register_bounds.py`:
  - `test_each_database_path_exits_inside_its_bound[<row>-<fault>]`, which
    owns test 10's per-path cases;
  - `test_a_slow_statement_meets_57014_before_the_client_timer[<row>]`;
  - `test_an_orphaned_writer_transaction_is_ended_by_the_idle_bound`;
  - `test_a_rollback_whose_first_call_expires_rereads_the_switch`;
  - `test_a_rollback_that_keeps_losing_its_turn_exits_75_at_the_deadline_with_nothing_written`;
  - `test_the_lookups_tries_start_inside_the_window_and_end_in_3[F1|F2|F3|F4]`;
  - `test_a_cutover_whose_call_expires_leaves_the_switch_unknown_and_its_rerun_reads_it_first[F1|F2|F3|F4]`;
  - `test_the_aborts_call_that_expires_is_run_again_or_found_done_by_its_rerun[not-landed|landed]`;
  - `test_a_record_marker_that_expires_leaves_the_marker_adding_and_writes_refusing`;
  - `test_the_outage_declare_probe_ends_inside_its_bound_and_the_declaration_follows_the_rule[<fault>-<form>]`;
  - `test_a_stray_ways_out_commit_call_is_in_doubt_like_a_writers[import-single|render-revert]`;
  - `test_a_waiting_migration_gives_up_at_its_own_lock_timeout`;
  - `test_import_and_reconcile_set_their_bounds_at_connect` (FP).
- **Run time.** About 8–9 min on loopback, F4's rows adding about 90 s. The
  rollback's two 15 s calls, the commit's F2 row and each stray way out's, up
  to 36 s, and the operator rows' F2 cases, 15 s each, dominate. Open point
  9 asks whether the long rows run on every trigger. The figures are the
  timers' sums at `c` = 2 and one address.
- **Flakiness.**
  - Every bound is the design's, the path's own timers summed for a
    composite row. It is asserted at twice its value, and the measured time
    is recorded with its margin (timing rule 2); the guard's 4.5 s is
    asserted as stated (timing rule 1).
  - Listeners and relays are per test, on port 0.
  - Locks are the test's own sessions.

#### AT-D9c. D9 over a remote database: one request per call, `c` and `N_a`, and a burst on a delayed link

- **Proves.** D9 and the phase plan's "Over a remote database", lane 4's
  texts over a remote database (#165 6722839180), whose section 3 names
  these tests:
  - each register call sends its statements as one request, so the
    snapshot, the commit and the lookup each cost the connect and one round
    trip;
  - `c`, the URLs' `connect_timeout=`, and `N_a`, `register_addresses=` in
    `lanes-index.conf`, default 1, set every timer that holds a connect:
    `S` = `C` = `N_a`·`c` + 6 s, `T_L` = `N_a`·`c` + 7 s and a reader's
    `N_a`·`c` + 4 s, never below the row's figure; a `c` under 2 is refused
    (SVC-2);
  - the client's wait for `<n>` counts from the snapshot call's end, so a
    slow call never brings LR011;
  - over a delayed link with TLS, no healthy call meets its timer, and a
    burst of ten ends with each writer committed once or out with 75 and
    nothing written. D7 stays 30 s, and act 3's test 4 decides it (the
    phase plan's "D7 stays 30 s");
  - act 3 takes each workstation's own measurements as it joins.
- **Phase and kind.** W. FP for (a)'s request shape and for (b); RE for
  (a)'s engine half, (c) and (d); (e) is act 3's.
- **Fixture.**
  - The fake `psql` (section 3.2), which records each call's stdin,
    environment and argv; the session cluster through the shim.
  - For (a)'s engine half, section 5.6's counting cluster.
  - For (d), section 5.6's TLS cluster behind the delayed loopback lane 3
    picks for lane 2's question 3 (#165 6722518182; section 7's point 23),
    with 75 ms added each way; until lane 3 picks, (d) does not run. The
    configuration carries `connect_timeout=3` and one address, as a
    workstation at that round trip sets `c`, and two engineers' writer
    logins, made there as AT-LRMPG makes them.
- **Oracle.**
  - **(a) One request per register call.** In the fake's log the snapshot's
    script is one request: #160's statements joined with `\;`, its marker
    sent as `COPY (SELECT E'\x1eLANES-INDEX-NEXT') TO STDOUT`. The commit's
    is `BEGIN ISOLATION LEVEL SERIALIZABLE \; SELECT
    lanes_register.commit_write(…) \; COMMIT;`, and the lookup's is
    `BEGIN \; SELECT lanes_register.find_write(…) \; ROLLBACK;` (§1.3's
    steps 1 and 3; the phase plan's "One request per call"). A statement
    left to its own semicolon fails the case. With the fake's
    `psql --version` at 14, the snapshot is one `COPY` of tagged rows, with
    no `BEGIN`. On the counting cluster each of the three calls logs one
    `statement:` line, and the joined snapshot's stdout equals, byte for
    byte, the same statements sent one by one, so #160's parse is
    unchanged.
  - **(b) `c` and `N_a` set the timers.** `connect_timeout=3` in the URLs
    gives `PGCONNECT_TIMEOUT=3` in each call's environment. The fake answers
    the snapshot and never answers the commit or the lookup, under
    `LANES_REGISTER_DEADLINE_S=1`: the commit call ends at `C`, 9 s, and the
    lookup's try at `T_L`, 10 s; a second case never answers the snapshot,
    which ends at `S`, 9 s. With `register_addresses=2` they are 12 s, 13 s
    and 12 s, and with neither key 8 s, 9 s and 8 s. Each call ends no
    earlier than its timer (timing rule 3) and under twice it (timing rule
    2), and its time is recorded against the timer with its margin.
    `connect_timeout=1` and `connect_timeout=0` are refused at
    `load_config`, before any `psql` call (SVC-2; the phase plan's "`c` and
    `N_a`, the connect bound").
  - **(c) The wait counts from the call's end.** The shim holds the snapshot
    call 2.5 s before it hands it on, so the database reads its clock 2.5 s
    after the call started, and the first commit call is lost as `before`
    loses it, under `LANES_REGISTER_DEADLINE_S=1`. The lookup's first try
    gets no LR011, and the shim's log shows it starting no earlier than the
    snapshot call's end plus `C` less 1 s (timing rule 3), and under twice
    `C` plus 1 s, 18 s, after that end (timing rule 2), `C` being 8 s here
    (lane 4's section, "The in-doubt rule (F3, D8)":
    "the last snapshot's database clock plus `SECONDS` since that snapshot
    call ended"). A client that counted from the call's start would call
    2.5 s early and meet LR011.
  - **(d) A delayed link, with TLS.** Test 9's per-call runs and test 4's
    burst of ten, on the delayed loopback:
    - no healthy call meets its timer: in the shim's log every call ends
      with `psql`'s own exit, none cut by its client timer, and each call's
      time is recorded against its timer, `S` = `C` = 9 s, `T_L` = 10 s and
      a reader's 7 s, with its margin;
    - the burst, one request per call, under D7's 30 s, with
      `LANES_REGISTER_DEADLINE_S` unset as in AT-4 (c): every writer commits
      once, or exits
      75 with nothing written, its write id absent from the journal, and no
      writer ends any other way. The new seqs have no gap and no repeat.
      Each writer's attempts, counted as snapshot calls in the shim's log,
      and its time from the release to its exit are recorded, never
      asserted: the phase plan puts the last writer at D7's edge at 150 ms,
      and act 3's test 4 decides D7;
    - the same holds with the ten writers split across the two writer
      logins, five from each, since the head row serialises every login's
      commits.
  - **(e) Act 3, on each workstation as it joins.** The phase plan's "What
    act 3 measures": the endpoint's address count by `getent ahosts`, which
    sets `N_a`; the TLS version; `psql --version`, 15 or later; the RTT;
    `t_c`'s 50 cold and 50 warm runs, whose median sets `c`; where libpq
    cuts the connect; test 9's per-call figures; test 4's burst of ten at
    that workstation's own RTT; and one run with an empty state directory.
    Section 5.6's act-3 rows carry them; no CI test runs them.
- **Tests.** In `code/tests/test_sql_register_bounds.py`:
  - `test_each_register_call_is_one_request[snapshot|commit|lookup|snapshot-psql-14]`
    (FP);
  - `test_the_joined_snapshot_logs_one_statement_and_reads_byte_identical_to_the_one_by_one_form`
    (RE);
  - `test_connect_timeout_and_register_addresses_set_each_calls_timer[default|c3|c3-two-addresses]`
    (FP);
  - `test_a_connect_timeout_under_2_is_refused_at_load_config[0|1]` (FP);
  - `test_the_lookups_wait_counts_from_the_snapshot_calls_end_and_gets_no_lr011`
    (RE);
  - `test_over_a_delayed_tls_link_no_healthy_call_meets_its_timer` (RE);
  - `test_a_burst_of_ten_over_a_delayed_tls_link_commits_each_once_or_exits_75_with_nothing_written[one-login|two-logins]`
    (RE).
- **Run time.** (a) and (b) about 1.5 min, mostly (b)'s timers; (c) about
  12 s; (d) about 2–4 min on the delayed link, where an attempt takes about
  3.1 s at 150 ms and the burst's last writer about 31 s (the phase plan's
  "The figures by RTT"). (b) and (d) are long rows (open point 9).
- **Flakiness.**
  - Each timer is asserted from below at its own figure, which load only
    lengthens (timing rule 3), and from above at twice it (timing rule 2).
  - (d)'s burst asserts outcomes, never its drain: a 75 there is the
    design's answer at D7, not a flake.
  - The relay binds 127.0.0.1 on port 0, per test.

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

    Both writers connect through the fixture's writer login, and
    `lanes-index` gives each backend the same `application_name`,
    `lanes-index`, through `PGAPPNAME` (`code/lanes-index:1307`), so the
    test tells their backends apart by `query LIKE '%<write_id>%'`, with
    each write id read from the shim's log:
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
  - it refuses with 2 while its probe reads the switch at `primary`, in
    either form, and with 2 when `--until` is missing on Brett's word. The
    probe is one read, as REGISTER-WRITER, of the switch's `mode` and
    `set_seq` and the kind of the journal entry at `set_seq` (D9's row for
    `outage declare`);
  - where the probe reads the switch at `files` after a rollback, Brett's
    word is refused with 2, writes nothing and prints `outage declare
    --rollback --mirror-seq <k>`, with `k` as `set_seq - 1`. The `--rollback`
    form needs `--mirror-seq <k>` and is refused with 2 unless the entry at
    `set_seq` is a `rollback` and `<k>` is `set_seq - 1`, or where the probe
    cannot read the switch. Where the probe cannot read it, Brett's word
    writes the declaration, since the database does not answer from this host
    (§2.5, "Refusals"; the delta's "`outage declare` during a rollback
    records the rollback's head");
  - the guard reads the file once, at the prefetch, and uses it only when its
    database read fails (SVC-12's first condition);
  - where the file is present, unexpired, and names this workstation's
    database, and the mirror is at `mirror-seq` or above, the guard answers
    from the mirror, with `(declared outage <utc>: <why>)` in place of the
    cause;
  - a rollback's start declares the outage, marked as from a rollback (D20;
    lane 3, comment 6579459518; §2.3): `rollback`
    writes the record where its configuration directory is writable, and on a
    read-only mount prints `outage declare --rollback --mirror-seq <k>`,
    where `k` is the head its transaction found, not the mirror's `m`. The
    record carries `mirror-seq=<k>` and no expiry. It counts as unexpired
    until it is cleared, and only where the guard's own read finds the switch
    at `files`: once a cutover sets `primary` again, or in a partition, it
    opens nothing (V2's S-4; SVC-12's second condition). A rerun of the
    rollback declares first, then goes on from the render (§2.3, "A rerun");
  - every write and every other deciding read still refuses with 69;
  - `status` reports a declaration left in place while the database answers.
- **Phase and kind.** N. FP for the unreachable database, FM for the mirror,
  RE for the probe's reads of the switch; the rollback's cases are G, with
  test 8's estate.
- **Fixture.**
  - A configuration with `mode=primary`, and a fake `psql` that exits 2.
  - A clone whose `origin/main`, at or above `mirror-seq`, carries a
    register in which this session's lane is bound (`ok`) and, in a second
    case, held by another session (`refuse`).
  - The hook's input.
  - For the rollback's cases, test 8's estate, its rollback held at its push
    by the `git` wrapper; once with a writable configuration directory, and
    once with it read-only. For the rerun, the rollback is killed on the
    shim's FIFO signal right after its transaction returns, so no
    declaration was written.
  - For the probe's reads: the session cluster's `tmpl_imported`, with its
    switch at `primary` after the cutover, and a second database whose switch
    is at `files` with a `rollback` entry at `set_seq`, made by the real
    commands.
  - The floor under a declaration is AT-F7's (SVC-8; V2's NIT 7), which sets
    the guard's age directly with a stand-in pid. This entry holds no case of
    it: a stall calibrated to within 0.1 s of a 0.5 s window would be a clock
    race.
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
  - **`outage declare` where the probe reads `primary`,** in either form, the
    `--rollback --mirror-seq <k>` form included: exit 2 and no file.
  - **`outage declare` during a rollback,** the probe reading the switch at
    `files` with a `rollback` entry at `set_seq`:
    - on Brett's word it exits 2, writes no file, and prints `outage declare
      --rollback --mirror-seq <k>`, with `k` as `set_seq - 1`;
    - that command, run as printed, writes the declaration, marked as from a
      rollback, with `mirror-seq=<k>` and no expiry;
    - `--mirror-seq` with another value (a wrong `<k>`), the `--rollback` form
      with no `--mirror-seq`, and the `--rollback` form where the entry at
      `set_seq` is not a `rollback` each exit 2 and write no file.
  - **Where the probe cannot read the switch** (the fake `psql` exits 2; F1,
    F2 and F3): `--rollback --mirror-seq <k>` exits 2 and writes no file, and
    Brett's word writes the declaration.
  - **A rollback's declaration, by what the guard's read finds.** The file is
    marked as from a rollback:
    - the read finds the switch at `files`: the guard answers from the mirror
      (the case below);
    - the read finds the switch at `primary`, after a cutover has set it
      again: the guard answers from the database, and `status` reports the
      file;
    - the read fails, whether the switch is `primary` again or the clone is
      cut off in a partition: the guard exits 2 with the ordinary outage
      refusal, and never answers from the mirror.
  - **The floor** (SVC-8; V2's NIT 7) is AT-F7's, with its control:
    `test_under_a_declaration_a_direct_read_left_under_0_5_s_does_not_start[floor|clamp|room]`
    and `test_without_a_declaration_the_same_age_still_starts_the_direct_read`.
    They set the age with a stand-in pid, so no margin rests on the length of
    a stall. This entry has no case of the floor.
  - **The rollback's start.** While the rollback is held, the record exists,
    marked as from a rollback, with `mirror-seq=<k>`, the head its transaction
    found, and no expiry. The guard, whose read finds the switch at `files`,
    answers from the mirror. On the read-only mount no file is written, and
    stdout carries `outage declare --rollback --mirror-seq <k>`. The
    rollback's step 6 ends with `outage clear`.
  - **The rollback's rerun** (§2.3, step 2, "A rerun"). After a rollback
    killed right after its transaction, before it declared, the rerun finds
    the switch at `files`, the marker `removing` and its own entry as the last
    seq. It declares first, with the `git` wrapper holding it at its first
    render call: where the directory is writable the file exists by then,
    with `mirror-seq=<k>` for `k = set_seq - 1`; on the read-only mount stdout
    carries `outage declare --rollback --mirror-seq <k>`. Then it goes on from
    the render.
- **Tests.** In `code/tests/test_sql_register_outage.py`:
  - `test_outage_declare_writes_its_fields_and_clear_removes_it`;
  - `test_outage_declare_refuses_while_the_database_answers_or_without_until`;
  - `test_a_declared_outage_lets_the_guard_answer_from_the_mirror[ok|refuse]`;
  - `test_an_expired_or_foreign_declaration_changes_nothing[expired|other_database]`;
  - `test_writes_and_deciding_reads_still_refuse_during_a_declared_outage`;
  - `test_status_reports_a_declaration_left_in_place`;
  - `test_a_rollbacks_start_declares_the_outage[writable|read-only]`;
  - `test_outage_declare_is_refused_where_the_probe_reads_primary[word|rollback]`;
  - `test_outage_declare_on_a_persons_word_during_a_rollback_is_refused_and_prints_the_rollback_form`;
  - `test_outage_declare_rollback_is_refused_for_a_wrong_k_a_missing_mirror_seq_or_a_switch_it_cannot_read[wrong-k|no-mirror-seq|not-a-rollback-entry|unreadable]`;
  - `test_a_rollbacks_declaration_counts_only_where_the_guards_read_finds_the_switch_at_files[found-files|found-primary|failed-primary-again|failed-partition]`;
  - `test_a_rollback_rerun_declares_before_it_goes_on[writable|read-only]`.
- **Run time.** About 15–25 s: the new real-engine and rollback cases take
  2–4 s each.
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

  The cutover, in every form, takes its database label from the host's
  `register_label=` and from nothing else, and an after-loss cutover that
  meets the switch at `primary` is refused with 2 unless its label differs
  from the switch's (§2.3, the re-pointing and the label; V2's S-2 and N4;
  the delta's "The cutover takes its database label from the workstation's
  configuration").
- **Phase and kind.** G. RE, with git.
- **Fixture.**
  - Stoppable cluster A, written through seq `m` with its renders done (a lag
    of 0). Test 5's `git` wrapper is then armed to fail every `fetch`, and A
    is written through seq `k > m`. The test waits for each renderer's exit
    (open point 8's second wait), and `status` shows a lag of `k − m`.
  - For the restore branch, a backup of A taken at head `b`, with
    `m < b ≤ k`, in the daily dump's form (`operations.md` act 3's step 1):
    `pg_dump --format=custom` of both schemas, run as `lanes_register_dump`,
    made on A by `lanes-index login add --dump-job`. The test reads `b`
    from the file alone, with `pg_restore --data-only
    --schema=lanes_register --table=register_head --file=- <file>`, and
    asserts that it equals A's head when the dump was taken. `pg_dump` and
    `pg_restore` come from the directory the fixture chose, as in AT-LRMPG,
    whose cases also hold the provider's point-in-time restore.
  - A is then destroyed, and stoppable cluster B is new. `lanes-index
    bootstrap --roles-only` runs on B first (F18). For the restore branch
    the backup is restored next, and then `bootstrap` runs again; for the
    re-import, `bootstrap` follows at once (§2.3, the disaster path's
    step 6). On either branch `lanes-index login add` then makes the
    engineers' logins on B again, with new passwords, since B is a new
    server and a dump holds no login (§1.4, "What a revoked login sees";
    `operations.md` §2.4's restores), and the re-pointing below writes
    their lines.
  - Every operator command runs with `LANES_LANE=` the operator lane, through
    its checkout (F12).
  - Each host's configuration is re-pointed at B before any command reaches
    it, in one edit: `register_url=`, `reader_url=`, their password files and
    `register_label=`, B's own (§2.3's re-pointing; F18). For the restore
    branch B's label differs from A's, since B's `bootstrap` took a new
    `--label` (N4). A second configuration still reaches A and keeps A's
    `register_label=`, for the host-still-on-the-live-database case.
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
    - Before B exists, `lanes-index status`, which cannot reach A, prints
      `m`, the mirror tip's `Register-Seq`, and `h`, the last head it
      recorded, `k`: the loss is `m+1` to `k` (§2.3, the disaster's step 4).
      N8, the coordinator's notice at the resumption (§2.6), is a post, not
      a tool's output, and no test reads it.
  - **Restore.**
    - B's journal holds `m+1` to `b`, and its switch reads `primary` with the
      marker `present`, as the backup had it.
    - B's second `bootstrap` carries B's `--label` and writes no label: the
      switch is at `primary`, so `database_label` still names A (§2.4).
    - On the host re-pointed at B, `lanes-index ready` fails check 10 by
      design, naming B's label and A's, since the restored switch still names
      A's.
    - `lanes-index cutover --after-loss`, with no import, renders every seq
      the mirror lacks, up to `b`, in order, then writes the cutover's entry
      in its restore form at `b+1`, rewriting `lanes/STORE` to name B (lane 3,
      comment 6579459518). The label it sends is the host's `register_label=`,
      B's, which differs from the switch's, so it commits: `database_label`
      and `lanes/STORE`'s `database=` then name B, and check 10 is `ok` on the
      same host.
    - The next write is `b+2`, and `status`'s `m` and `h`, read before B
      existed, put the loss at `b+1` to `k`, since the restore keeps every
      seq up to `b`.
  - **A host still on the live database,** run before A is destroyed, with the
    configuration that still reaches A and carries A's `register_label=`.
    `lanes-index cutover --after-loss` sends A's own label, which is the
    switch's, and A's switch is at `primary`: it exits 2 and writes nothing
    (§2.3; the delta's "A host still pointed at the live database"). A's head,
    its switch row and `lanes/STORE` on origin are as they were, and no commit
    is pushed.
  - **An abort after a restore-form cutover** (lane 3's ruling, comment
    6579459518, point 1; lane 1's v2.3, comment 6708990159). The restore
    form's cutover records what it replaces in
    `register_switch.restore_prior`: the switch row, and `lanes/STORE`'s
    `files` row and lines. The `git` wrapper fails only the push of the
    commit that carries `Register-Seq: b+1`, the marker's, so the renders
    of `m+1` to `b` reach origin first, and the read-back proves the
    entry's commit absent from origin. The abort, `switch_to_files` at
    `b+1`:
    - deletes the entry at `b+1` with its `journal_edits` and
      `journal_files`, puts the head back to `b`, and returns `b`;
    - puts `lanes/STORE`'s `files` row and lines back with their old
      `line_id`s, and the switch row back whole: `primary`, `present`, with
      the old `marker_seq`, `marker_commit`, `generation`,
      `database_label`, `set_seq`, `set_utc`, `set_by_lane` and
      `set_by_ws`, and `restore_prior` NULL. It never sets `files`;
    - so a dump of every table of §1.2 except `schema_version`,
      `migrations` and `mirror_state` (twelve of the fifteen, with
      `journal_scopes`, which deletes with its journal row; R3's N2; lane 1's
      comment 6708990159), each ordered by its key, taken by the admin
      connection at head `b` before the cutover equals the same dump after
      the abort, byte for byte. `mirror_state` is left out: the cutover's
      render records through `record_mirror_state` before its transaction,
      and the abort does not name that row among what it puts back. The dump
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
  - `test_status_prints_the_mirror_tip_and_the_last_head_that_bound_the_loss`;
  - `test_the_restore_route_commits_under_the_replacements_register_label_and_check_10_fails_until_it_does`;
  - `test_an_after_loss_cutover_from_a_host_still_on_the_live_database_is_refused_with_2_and_writes_nothing`.
- **Run time.** 10–25 s.
- **Flakiness.** The lag is made by failing the renderer's fetch, and read
  after the renderer's exit, never by timing.

### Lane 1's schema objects

Lane 3's ruling 5 (#165 comment 6578020193) asks for a real-engine test of
each object lane 1's part adds. These entries are written against
`design.md`'s "The schema", §1.2 to §1.5. Each runs on the session cluster, in
a database cloned from the template (section 3.1), as the role named, and
reads back through the admin connection. Their phases are in section 1.3's
table.

AT-LR007, AT-LR009, AT-LR010, AT-LR011 and AT-LRLABEL, after AT-F33, test
the objects of lane 1's v2.4 to v2.9 that "The schema" marks new and
unproven, as the design has them at lane 3's commit W (`bb08d080`), on the
engine's side. Their other halves are tested where the plan tests the
verb that meets them: LR012 in AT-F1; LR011's refusals at once and the
client's lookup in AT-F3; the act's two backstops and LR010 at a write in
AT-F5; N4's LR004s in AT-F18; and S7's agreement test of `line_scopes` in
AT-7. AT-LRMPG tests what commit W folded for Brett's managed PostgreSQL:
the logins and their floors, `lanes-index login`, the dump, the restores,
and `ready`'s checks 11 and 12.

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
  database for the abort, and a third for the after-loss form. The
  cutover's own cases run `lanes-index cutover`'s whole row, T116's
  transaction and T327's push, against a scratch schema, from the operator
  lane's own checkout: `LANES_REPO` names a clone whose origin is a local
  bare repository, and `LANES_WORKSPACE_ROOT` a `W` of its own. Origin's
  `main` is a real commit C, which the import names, and origin's
  `pre-receive` hook refuses a push where a case asks (lane 3's ruling
  (c), comment 6828023371).
- **Oracle.**
  - After the bootstrap: `mode = 'files'`, the marker `absent`,
    `set_seq = -1`, and the head -1.
  - After the import (seq 0) and the cutover's entry, `commit_write` with a
    `cutover` entry: the head is 1, `set_seq = 1`, `mode = 'primary'`, the
    marker `adding` at `marker_seq = 1`, and `journal.operator` is true for
    seq 1 only. A write then fails with LR002, so 69. After `record_marker`
    at seq 1, the marker reads `present`, `marker_commit` holds the
    confirmed commit, and a write commits.
  - After the import at C (seq 0) and `lanes-index cutover --import <C>`,
    whose 0 is T327's whole row: the head is 1, `set_seq = 1`,
    `mode = 'primary'`, the marker `present` at `marker_seq = 1`,
    `marker_commit` origin's new tip, its one push, a child of C carrying
    `Register-Seq: 1`, and `journal.operator` is true for seq 1 only.
  - After an after-loss import at `m`, its entry naming C, and
    `lanes-index cutover --import <C> --after-loss`, whose 0 is T327's
    whole row: `set_seq = m+1`, the marker `present` at `m+1`, and the
    generation differs from the bootstrap's.
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
  - **The re-import form's abort** (the third database), through the
    cutover's own step 3: origin refuses the marker's push, the read-back
    proves it absent, and `cutover` exits 2 with its `ABORTED` line. While
    the push is held, the switch reads `primary` with the marker `adding`,
    `set_seq` `m+1` and a generation other than the lost database's. After
    it, `switch_to_files` at `m+1` has set `files` with the marker
    `absent`, `set_seq` `m` and `lanes/STORE` gone, and left the
    `database_label` and `generation` its cutover set, as lane 3 ruled lane
    1's point 2 (comment 6710770304); origin's `main` is still C. So a
    commit call carrying another generation, as a pending record made
    against the lost database would, still fails with LR007.
  - **The restore form** (a fourth database, restored at `b` as AT-D22
    makes it), each cutover its whole row: `cutover --after-loss`'s 0
    leaves `primary` with the marker `present`, `set_seq` `b+1` and its
    label, and its own `record_marker` has cleared `restore_prior`. A
    second restore form, whose marker's push origin refuses, aborts at its
    step 3, exit 2, and its `switch_to_files` puts back what
    `restore_prior` held: `lanes/STORE`'s `files` row and lines with their
    old `line_id`s, the switch row whole, and `set_seq` the value before
    the cutover (AT-D22's dump); origin's `main` stays where that cutover
    found it. As the admin under `SET ROLE lanes_register_owner`, setting
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
- **Fixture.** AT-LR006's register, with aliases X and Y mapping to O in
  `lanes/repos.tsv`. Claims through the real writer.
- **Oracle.** `spellings` is a `text[]` whose order §1.2 does not fix, so it
  is compared as a set.
  - L1's claim typed as X, which `claim` writes as O (Y:3402–3403): its
    `object_holds` row has `object = O`, and `spellings` holds the set {X, O, Y}.
    Where `register commit` takes a line naming X as written (phase N, T231),
    the row keeps its line's object, `object = X` (Y:1224, Y:1252), with the
    same set (lane 3's batch 11 (2)).
  - L1's claim typed as O: the same set.
  - After L1 releases O, L2's claim of X commits, and its row holds the same
    set.
- **Tests.** In `code/tests/test_sql_register_schema.py`:
  - `test_a_claim_records_its_spelling_its_key_and_its_aliases[as-alias|as-key]`;
  - `test_a_released_claims_spellings_block_nothing`.

#### AT-LR5. LR005, the register's rules in the transaction, each named by its DETAIL token

- **Proves.** `commit_write`'s structured rows (§1.3.1) raise LR005 for a
  claim whose spellings meet another lane's live claim (D19), for a claim
  that trips `one_live_claim`, whose 23505 is raised again as LR005, and for
  a `HOLD` or `LANDING` line over another lane's open taker in `main_slots`
  (D18). D18 compares a canonical repository key, held as D19 holds an
  object's spellings (lane 3, comment 6579459518), so one repository spelled
  two ways is one taker. The writer maps LR005 to exit 2 with the holder or
  the earlier line. And because the structured rows run after the head
  moved, LR005 proves that the whole transaction rolls back.

  The same function raises LR005 on primary for a `LANDING EXPIRED` that
  names no open, stale `LANDING` (F13), and for a take, a `HOLD` or a
  `LANDING`, whose UTC cannot be read (U3). A `LANDING EXPIRED` that names
  one is what frees `main` when a lander died holding it: the engine's half
  of Brett's release for a stale `LANDING` (`design.md`, "Differences from
  Amendments 22 and 23, for Brett", the row "A release for a stale
  `LANDING`", which W says is written only on Brett's word by
  `append-line --landing-expired`). F13's `LANDING` is the holder's newest
  for the line's PR that the line would close, matched as `main_slots`
  matches a close to its take. It must carry the line's `repo_key`, no
  close that `main_slots` would match to it may follow it, and it must be
  more than 30 minutes old by `clock_timestamp()` (§1.3's step 7; lane 1's
  v3.1, tested in that order: none, closed, young). Each LR005 names its
  rule in its message and in a `DETAIL:` that is one fixed token (U7):
  `one-live-claim`, `claim-spellings`, `main-held`, `expired-none`,
  `expired-closed`, `expired-young` or `take-no-utc`. These are direct
  `commit_write` calls, which need no verb; the verb is AT-F13.
- **Fixture.** The runs of test D19's [behind-the-alias] case and test 4's
  case (b), with the shim's log, which records each call's stderr, whose
  first line under the commit call's `VERBOSITY=verbose` and
  `SHOW_CONTEXT=never` is `ERROR:  <SQLSTATE>: <message>` (§1.3's step 3).
  A third case: L1 claims O and releases it, then L2's claim of X commits.
  A fourth case, as lane 1's v2.3 ran it, in four forms:
  `lanes/repos.tsv` maps `r9` to `opensoft/r9`, and one lane takes one
  spelling before another lane takes the other. The forms are `HOLD`
  key-first (L2 takes `opensoft/r9`, then L4 takes `r9`), `LANDING`
  key-first, `HOLD` alias-first (L2 takes `r9`, then L4 takes
  `opensoft/r9`) and `LANDING` alias-first, the test's
  `[hold|landing]-[key-first|alias-first]`.

  The `LANDING EXPIRED` cases (F13) are direct `commit_write` calls, phase N,
  with no verb: as `lanes_register_writer`, in the commit call's own `psql`
  form (AT-LR006's fixture), on a clone of `tmpl_imported` brought to
  `primary` with the marker `present`, one clone per case. The test builds
  each entry as AT-LR006's does: an append of one line to `lanes/LANES.md`,
  its `files` digest and its `register_lines` row, with `lanes/repos.tsv`
  mapping `r9` to `opensoft/r9` as the fourth case has it. L1's `LANDING`
  take names `opensoft/r9` and one PR. Its `utc_ts` is set by the test past
  Rule 6's window by the database's clock at the snapshot, so no case waits
  for the window; a fresh one's is that clock itself. The `LANDING EXPIRED`
  row is a close of kind `landing`, L2's own line, whose `for_lane` names the
  holder and whose `repo_key` and PR are the take's. For U3, a take row's
  `utc_ts` is NULL, its line carrying no UTC, or `2026-13-45T99:99Z`, as the
  client sends a UTC it cannot read (lane 1's v3.1, U3c).
- **Oracle.**
  - The loser's last commit call ended with LR005: the claim in test D19,
    the later `HOLD` in test 4 (b), where D18's classifier runs inside the
    commit, and the second take of `r9` or `opensoft/r9` in either order,
    `HOLD` or `LANDING`, since the line's `register_lines.repo_spellings`
    holds the spelling as typed, its canonical key and the writer's
    aliases, and step 7's check and `main_slots` compare the key or any
    overlapping spelling (D18; lane 1's v2.3). A line whose
    `repo_spellings` is NULL is compared by `repo_key` alone. The second
    stderr line is exactly `DETAIL:  claim-spellings` in test D19, and
    `DETAIL:  main-held` in test 4 (b) and for either take of `r9` (U7).
  - Read after LR005: `register_head.seq`, the journal, `journal_edits`,
    `doc_lines` and `object_holds` equal their values before the call. The
    loser's write id is absent.
  - The loser exits 2, never 69, and its stderr carries the holder or the
    earlier line.
  - The released case raises nothing, and L2's claim commits.
  - **[one-live-claim].** A direct commit call whose `CLAIMED` row meets
    another lane's live claim of the same object trips `one_live_claim`:
    LR005, `DETAIL:  one-live-claim`, and nothing written.
  - **`LANDING EXPIRED`** (F13; §1.3's step 7), each case on a clone of
    its own:
    - **[stale].** L1's `LANDING` has committed, its `utc_ts` past the
      window. L2's `HOLD` on that repository is refused LR005 (D18), with
      `DETAIL:  main-held`: Rule 6's window is a duration, and a stale
      `LANDING` still takes `main`. L2's `LANDING EXPIRED` for L1 then
      commits at the next seq, the head moves by one, `main_slots` no
      longer lists L1's `LANDING`, and the row holds `for_lane` L1. L2's
      `HOLD`, sent again, commits.
    - **[closes-only-its-own].** L1 holds a `HOLD` on `opensoft/r9`, its
      `LANDING` for PR p posted twice, both past the window, and a
      `LANDING` for PR q; L3 holds a `LANDING` on another repository. L2's
      `LANDING EXPIRED` for L1's PR p commits and closes both of PR p's
      posts, as L1's `LANDED` would. L1's `HOLD`, its PR q `LANDING` and
      L3's `LANDING` stay open in `main_slots`, and no `LANDED` is written.
    - **[holder-writes].** L1's own `LANDING EXPIRED`, `for_lane` L1,
      commits: a close is matched by `coalesce(for_lane, lane)`.
    - **LR005, with its reason's token, and nothing written.** In each case
      the call's `psql` exits 3; its first stderr line is
      `ERROR:  LR005: <message>`, the message naming F13 and the reason in
      lane 1's v3.1 form, and its second is exactly `DETAIL:  <token>`; the
      head, the journal and `register_lines` equal their values before it:
      - `expired-young`: [fresh], the `LANDING`'s `utc_ts` the snapshot's
        clock, the message giving its age by the database's clock;
        [posted-again], L1's `LANDING` for the PR posted 40 and 10 minutes
        before by the database's clock, so the newest post dates it;
        [posted-again-two-spellings], the older post keyed `opensoft/r9`,
        40 minutes before, and the newer keyed `r9`, 10 minutes before, the
        line keyed `r9`, each of the three rows with `repo_spellings`
        `{opensoft/r9,r9}`; and
        [unreadable-utc], on a clone of `tmpl_imported` brought to
        `primary`, over test 7's imported `LANDING` whose UTC cannot be
        read, the message saying its age by the database's clock is unknown;
      - `expired-none`: [wrong-lane] (`for_lane` names L3, who holds none),
        [wrong-pr], [other-spelling] (the row's `repo_key` is `r9`, with
        `repo_spellings` holding both, against a take keyed `opensoft/r9`:
        F13 compares `repo_key` alone, as lane 3 ruled lane 1's point 5,
        comment 6710770304), and [newest-keyed-another-way]
        ([posted-again-two-spellings]'s two takes, with their ages and
        spellings, the line keyed `opensoft/r9` with the same spellings),
        whose message names the newest take's key, `r9`: the newer take's
        spellings overlap the line's, so F13 finds it the newest, and its
        `repo_key` is not the line's. Were they not to overlap, F13 would
        date the older post, 40 minutes old, and the line would commit;
      - `expired-closed`: [already-closed] (L1's `LANDED` for that PR is in
        the journal), [closed-under-another-spelling] (L1's `LANDED` spells
        the repository `r9`, which `main_slots` matches to the take keyed
        `opensoft/r9`), and [already-released] (a second `LANDING EXPIRED`
        after [stale]'s commits).
    - **The table's checks.** A row with `for_lane` on a line of another
      kind, a `LANDING EXPIRED` row with none, and a `LANDING EXPIRED` that
      closes a `HOLD` are each refused with 23514 at the insert, and the
      whole entry rolls back.
  - **A take with no readable UTC** (U3; §1.3's step 7), each on a clone of
    its own:
    - on primary, [hold], [landing] and [bad-stamp], the last a `LANDING`
      whose line carries `2026-13-45T99:99Z` with a NULL `utc_ts`: LR005,
      the message quoting the line ("a HOLD take with no readable UTC (U3):
      …"), and exactly `DETAIL:  take-no-utc`; nothing is written;
    - on primary, [behind-d18]: such a take on a repository another lane
      holds gets `take-no-utc`, not `main-held`, since U3's test comes
      first;
    - not refused: a `HOLD (refresh)` row with a NULL `utc_ts`, which takes
      nothing, and a line of no kind with no UTC, each commit on primary;
    - at `files`, on a clone of `tmpl_imported` before its cutover: an
      `import` entry carrying the same take rows commits, each line loaded
      as it stands, since the rule binds only a write on primary.
- **Tests.** In `code/tests/test_sql_register_order.py`:
  - `test_lr005_refuses_a_claim_whose_spellings_meet_another_lanes_live_claim`;
  - `test_lr005_refuses_a_hold_over_another_lanes_open_taker`;
  - `test_two_spellings_of_one_repository_hold_one_main[hold|landing]-[key-first|alias-first]`;
  - `test_lr005_rolls_back_the_head_and_every_row`;
  - `test_lr005_does_not_fire_on_a_released_claims_spellings`;
  - `test_each_claim_and_main_rule_lr005_carries_its_detail_token[one-live-claim|claim-spellings|main-held]`;
  - `test_a_landing_expired_for_a_stale_landing_commits_and_frees_main_in_main_slots`;
  - `test_a_landing_expired_closes_only_the_holders_landings_for_that_pr_and_repository`;
  - `test_the_holder_may_write_its_own_landing_expired`;
  - `test_a_landing_expired_that_names_no_stale_landing_is_lr005_and_writes_nothing[<case>]`,
    where `<case>` is each of the eleven above, from `fresh` to
    `already-released`, each asserting its reason's DETAIL token;
  - `test_on_primary_a_take_with_no_readable_utc_is_lr005_take_no_utc_and_writes_nothing[hold|landing|bad-stamp|behind-d18]`;
  - `test_a_refresh_or_a_line_of_no_kind_with_no_utc_commits_and_a_take_with_none_loads_at_files[hold-refresh|no-kind|import-at-files]`;
  - `test_the_for_lane_checks_refuse_a_for_lane_off_a_landing_expired_and_a_landing_expired_that_closes_no_landing[for-lane-on-a-hold|no-for-lane|closes-a-hold]`.
- **Run time.** The four entries take 10–20 s in all, mostly test D19's and
  test 4's runs, which they share. The F13 and U3 cases, about 25, are
  direct calls with no verb run: a clone each and a few short calls, about
  3–5 s in all.
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
    nothing on stdout past its first line, `mode primary` (contract §3.6);
    the guard exits 2, and `managed-projection` 1.
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
- **Fixture.** AT-LR006's register, and `log NOTED` as the write. Per case,
  the shim rewrites: one `journal_files` digest to another 64-hex value; an
  edit's `old_sha256`, so the edit does not apply; an edited path left out of
  the entry's `files`; a `write` entry that edits `lanes/STORE`; an operator
  entry with no edit, written as a `rollback` entry, since a `cutover` meets
  LR002 first on a `primary`/`present` database, and sent by a direct call
  with no `files` row and no structured row, since ruling 1 refuses first,
  before the head's lock, a rewritten write entry that keeps its own (§1.3's
  kinds paragraph; lane 1's v2.4); an entry with no `journal`
  object; one with a value in `journal` that is neither a string nor NULL;
  one whose list key is not an array; a structured row whose `raw` is not
  its line's text; a `root_seq` on a journal that is not empty; and a
  `write` entry with no `args_sha256`, which fails
  its CHECK with 23514. Lane 1's v2.3 adds three: an entry with no
  `not_after`, and one whose `not_after` is not a string, each LR004; and
  one whose `not_after` is not a time, 22007, so 69 at once. Every entry a
  test builds carries `not_after`, on every kind, the operator entries
  included, as lane 1's v2.3 has it and as lane 3 kept it, lane 4's
  argument (lane 1's point 4; comments 6710770304 and 6709038351). An
  import's `not_after` is its 330 s client timer plus 2 s (D9's `import`
  row; lane 1's v2.6, as the routed list has it), so an import entry a
  test builds by hand carries at least that: a harness that gives an import
  a writer's bound, or 30 s, can meet LR008 on a large import. A file with
  no final newline is among the fixture's files.
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
  - **An import's `not_after`** (phase N): `lanes-index import --at <C>` of
    test 7's fixture register, through the shim, whose log holds its commit
    call's entry: that entry's `not_after` is more than 300 s and at most
    332 s after the import's journal row's `utc`, both read on the
    database's clock. A writer's bound would put it at most 10 s after.
- **Tests.** In `code/tests/test_sql_register_schema.py`:
  - `test_lr004_on_an_edit_or_proof_that_does_not_apply_leaves_everything_unchanged[<case>]`,
    where `<case>` is one of the rewrites above, named:
    - `digest-not-hex-equal` (one `journal_files` digest changed to another
      64-hex value);
    - `edit-old-sha256` (an edit's `old_sha256`, so the edit does not
      apply);
    - `path-not-in-files` (an edited path left out of the entry's `files`);
    - `write-edits-store` (a `write` entry that edits `lanes/STORE`);
    - `operator-no-edit` (an operator entry with no edit and no other row,
      as a `rollback` entry by a direct call);
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
  - `test_an_import_carries_not_after_of_its_330_s_timer_plus_2_s`, on lane
    1's v2.6;
  - `test_the_operator_flag_rules_refuse_lr004[write-edits-store|operator-edits-other|operator-edits-nothing]`;
  - `test_a_file_without_a_final_newline_renders_back_byte_identical`.
- **Run time.** AT-VER and AT-LR4 take 10–20 s together.
- **Flakiness.** None: each case is one write with a fixed rewrite, or a
  fixed client copy.

#### AT-LR006. Two attempts of one write meeting at the commit (S1's step 1)

- **Proves.** S1's commit, step 1 (§1.3.1), in the rewording lane 3 took
  from lane 2's LR006 probe (comment 6710770304): `commit_write` takes the
  head row's lock, checks the generation first (V2's S-3, lane 1's v2.8;
  AT-LR007), and then looks the `write_id` up, so an
  earlier attempt of the same write that committed after this attempt's
  snapshot read and before this commit's transaction began is LR006,
  before LR001. With SERIALIZABLE's 40001 at that lock, no interleaving of
  two attempts reaches the journal's unique `write_id`, so F33's 23505
  backstop is never reached (AT-F33). The LR006 probe ran each case below
  twice, with the same codes.
- **Phase and kind.** W. RE, on the session cluster, with the SQL as
  `design.md` prints it; the client's attempt loop and exit mapping are the
  design's outcomes table, read against the SQLSTATE.
- **Fixture,** shared by AT-LR006-1 to -8 and AT-F33:
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
    lanes_register.commit_write(base, W, …); COMMIT;`, sent by the test's
    own `psql -X -q -v ON_ERROR_STOP=1 -v VERBOSITY=verbose
    -v SHOW_CONTEXT=never`, the commit call's settings (§1.3's step 3; S1,
    "One attempt, under (A″)"; lane 3's ruling 6711564365), so `psql`'s
    first line on stderr is `ERROR:  <SQLSTATE>: <message>`;
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
    - B's `psql` exits 3, and its first stderr line is exactly
      `ERROR:  LR006: write W is in the journal at seq n+1`, with W and
      `n+1` as their values (`commit_write`'s message, §1.3.1);
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
    - (b) A's `psql` exits 2, with SQLSTATE 25P03 on its `FATAL:` line, so
      it is in doubt, since the client reads no code from an exit 2 (S1,
      "The outcomes"); B's snapshot finds W absent, and B commits W at
      `n+1`.
    - In both, one row for W, and no 23505.
  - **AT-LR006-7, another write's entry under W's id, committed between
    this attempt's snapshot read and its commit, gives LR007.** A carries
    W's id with other arguments, as a `LANES_WRITE_ID` given to a different
    command would (S1, "The write id"). B's snapshot read finds W absent;
    A commits; then B's commit call.
    - B's `psql` exits 3. Its first stderr line is `ERROR:  LR007: write W
      is seq n+1, another write's (F10)`, and the next is exactly
      `DETAIL:  another-write`. `register commit` exits 2 naming the id,
      and the write exits 2 (S1, "The outcomes").
    - The journal holds A's one row for W, and nothing of B's.
    - The control: under `VERBOSITY=sqlstate` the same call prints
      `ERROR:  LR007` alone, with no `DETAIL:` line, so the token is read
      only under the commit call's settings.
  - **AT-LR006-8, a generation change between this attempt's snapshot read
    and its commit gives LR007.** B's snapshot read; then the restore
    form's after-loss cutover, an entry whose label is not the switch's,
    commits at `n+1`, which sets a new generation and the marker `adding`;
    then B's commit call, with the generation its snapshot read.
    - B gets LR007 with `DETAIL:  generation`: not LR002 for the marker,
      and not LR001 for the moved head, since the generation is checked
      first under the lock (V2's S-3; AT-LR007).
    - `register commit` exits 2, and the write exits 2; the journal holds
      no row for W.
- **Tests.** In `code/tests/test_sql_register_in_doubt.py`:
  - `test_an_attempt_committed_after_the_snapshot_read_gives_lr006[held|whole]`;
  - `test_lr006_comes_before_lr001[stale-base|fresh-base]`;
  - `test_an_attempt_committed_while_the_commit_waits_gives_40001_then_exit_0[serializable|repeatable-read]`;
  - `test_an_attempt_committed_after_the_commits_snapshot_gives_40001_without_a_wait`;
  - `test_an_attempt_holding_past_lock_timeout_gives_55p03_then_exit_0`;
  - `test_an_attempt_that_never_commits_leaves_the_write_to_the_retry[rollback|idle-bound]`;
  - `test_the_no_lookup_control_gives_lr001_where_the_lookup_gives_lr006`;
  - `test_another_writes_entry_committed_after_the_snapshot_read_gives_lr007_another_write`;
  - `test_a_generation_change_after_the_snapshot_read_gives_lr007_generation`.
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
  made anyway gets LR006. In AT-LR006-3 at both levels, -4 and -5, once A
  has committed, the matrix likewise sends B's commit call once more with
  the fresh base, `n+1`, which the write itself never sends, since its
  next snapshot finds W; under `commit_write` that call gets LR006. The
  whole matrix, case 0 and AT-LR006-1 to -6, runs twice.
- **Oracle.**
  - Every B commit call ends in committed, LR006, 40001 or 55P03, and A's
    own calls in committed, its rollback, or 25P03 for a dead session.
  - SQLSTATE 23505 occurs in no session's output.
  - After each case, `SELECT count(*) FROM journal WHERE write_id = W` is 1.
  - At the end, `count(*) = count(DISTINCT write_id)` over the journal,
    with one root.
  - **The control must fail it.** Run against the no-lookup control, the
    same matrix shows 23505 on `journal_write_id_key` in the fresh-base
    calls: 6 a run here, case 0's, AT-LR006-2's, and the one sent after
    each 40001 or 55P03 (AT-LR006-3 at both levels, -4 and -5). The
    probe's 6 came from its own matrix, one of them READ COMMITTED's retry,
    a case this matrix does not run, so the two counts are not compared. If
    the control shows none, at least 1 being the bar, the test fails, since
    its oracle could not have caught the branch.
- **Tests.** In `code/tests/test_sql_register_in_doubt.py`:
  - `test_no_interleaving_of_two_attempts_of_one_write_reaches_23505`;
  - `test_the_no_lookup_control_reaches_23505_so_the_oracle_can_fail`.
- **Run time.** About 25 s for the two passes and the control's.

#### AT-LR007. The order of `commit_write`'s first checks: the generation, then the write id

- **Proves.** Lane 1's v2.8 (#165 6715661308), as §1.3 and §1.3.1 have it:
  `commit_write` takes the head row's lock, then checks the generation
  (LR007), then the write id (LR006, or LR007 for another write's entry),
  and only then the head (LR001), the version (LR003) and the switch
  (LR002), so that a write id of another generation never ends in 69 or 0
  (V2's S-3). These are the cases lane 1's v2.8 ran. The requirement is "A
  COMMIT whose outcome the writer cannot learn is recovered by its write
  id": a write id minted against another generation is refused with 2. The
  `DETAIL:` tokens are read in AT-LR006-7 and -8, in AT-F3 for the lookup
  and in AT-F5 for an act; the client's refusal at the snapshot is AT-F18's.
- **Phase and kind.** N. RE, on the session cluster.
- **Fixture.**
  - Direct calls as `lanes_register_writer`, in the commit call's own `psql`
    form (AT-LR006's fixture).
  - **Database R, between a re-import's bootstrap and its cutover:** a clone
    of `tmpl_imported`, at `files` with the marker `absent`. Its generation
    is `g1`; `g0`, the lost database's, is a uuid of the test's own.
  - **Database S, a restore:** a clone of `tmpl_imported` brought to
    `primary` with the marker `present`, as AT-LR006's is, standing in for a
    restored backup. Writes W0 and W1 commit under its generation `g0`. Then
    its restore-form cutover, an after-loss `cutover` entry whose label is
    not the switch's, at `b+1`, which sets a new generation `g1`, and
    `record_marker`. Then write W2 commits under `g1`.
- **Oracle.** Each refused call writes nothing: the head, the journal and
  the switch equal their values before.
  - **At `files`, in R:**
    - a write of `g0` with the current base: LR007, not LR002;
    - the same with a stale base: LR007, not LR001;
    - a write of `g1`, the switch's own: LR002.
  - **After the restore form's cutover, in S:**
    - W0's id with `g0`, found in the restored journal: LR007, not LR006,
      with the base current and with it stale;
    - W2's id with `g1` and W2's own lane, verb and `args_sha256`: LR006,
      with the base current and with it stale;
    - W2's id with `g1` and another lane: LR007.
- **Tests.** In `code/tests/test_sql_register_in_doubt.py`:
  - `test_a_write_of_another_generation_at_files_gets_lr007_not_lr002_or_lr001[current-base|stale-base]`;
  - `test_the_switchs_own_generation_at_files_still_gets_lr002`;
  - `test_an_old_generation_id_after_a_restore_form_cutover_gets_lr007_not_lr006[current-base|stale-base]`;
  - `test_a_same_generation_id_after_a_restore_form_cutover_gets_lr006[current-base|stale-base]`;
  - `test_an_id_in_the_journal_for_another_lane_gets_lr007`.
- **Run time.** About 3 s: two clones and some 15 short calls.
- **Flakiness.** None: each case is one call with fixed inputs, read by its
  SQLSTATE after it ends.

#### AT-LR009. The act's key, `act_id`, the touched keys, `*`, and LR009

- **Proves.** `commit_write`'s side of D5 and F5, lane 1's v2.4 (#165
  6711221999), as §1.3's "The act" and §1.3.1's steps 3b, 5a and 8a have
  it:
  - the entry's `act` key, `{id, base, generation, expect}`, on a `write`
    alone, refused with LR004 before the head's lock where it is malformed;
  - `journal.act_id`, with its two checks;
  - each entry's touched keys, derived inside the transaction from the
    lines it changes, never sent, and stored in `journal_scopes`; and `*`,
    the one key of an import, a cutover in any form and a rollback;
  - step 3b's LR009, "expectation changed", for an entry after the base,
    made outside the act, that touched a listed scope or stored `*`.

  The requirement is "Readers read the database and fail closed", its act
  scenarios at the engine. AT-F5 holds the client's half, through real
  acts, and the commit's two backstops for an act, LR007 `act-generation`
  and LR004 for a base past the head.
- **Phase and kind.** W. RE, with the shim's `edit-entry` mode, as AT-LR4.
- **Fixture.**
  - The imported register on `primary`, through the flipped test copy of
    `lanes-index` (test 14's second state), with lanes L1, L2 and L3, each
    with a row holding a session uuid and a log, an alias line, a row named
    `foo-03`, and an object L2 claimed more than four hours before.
  - Writes through the real `lanes-edit.sh`, with `LANES_ACT` from
    `act-begin` and `LANES_EXPECT` where a case names them.
  - For the codes the client refuses first, the shim's `edit-entry`
    rewrites the commit call's `act` key, as it rewrites AT-LR4's fields.
    The few cases the client cannot reach at all are direct calls.
  - The scopes table's rules in Python, as AT-7's agreement test (S7) has
    them, which compute each entry's keys from its `journal_edits` and the
    line store before and after it.
- **Oracle.** Every refused write writes nothing: the head, the journal,
  `journal_scopes` and the lines equal their values before.
  - **The `act` key's shape:** LR004, `the entry's act is not well formed`,
    before the head's lock. A test session holds the head row throughout,
    and each case still ends with LR004, never 55P03. The rewrites: an `id`
    with `@`, with a space, or not a string; a `base` that is a string,
    negative or not whole; a `generation` that is not a uuid; an `expect`
    that is not a list; an element that is not a string, is `*`, is `row:`
    with no name, or holds white space; and an `act` on an import entry,
    by a direct call.
  - **`act_id`:** a write made in an act stores the act's id as given; a
    write outside any act, an import, a cutover, a rollback, a
    `stray-import` and a `stray-revert` store NULL. As the admin under
    `SET ROLE lanes_register_owner`, setting `act_id` on the cutover's row
    fails with 23514 on `journal_act_id_write`, and setting a write's to
    `a@b` or `a b` fails with 23514 on `journal_act_id_check`.
  - **The touched keys.** For each committed entry, `journal_scopes` holds
    exactly the keys the rules give: step 5a's old side, each line the entry
    replaces or deletes and every line of a path it removes or renames, read
    before the edit; and step 8a's new side, each line it appends or
    replaces, each line a rename moved, read at its new path, and `objects:`
    for every lane holding an object it wrote a hold for. The writes, one
    case each, with a key each must hold among them:
    - `set-row-state` on L1: `row:l1`, and `sid:` for the uuid in the row;
    - `log PAUSED --utc` for L1, a lane-kind line: `bind:l1`;
    - a `claim` of a free object by L1: `objects:l1`, and no `bind:`;
    - a `claim` by L3 of L2's stale claim, a `TAKEOVER`: `objects:l3` and
      `objects:l2`, every holder of the object;
    - `rename-lane` from L2: the keys the rules give for the lines it
      moves and writes, `alias:` among them;
    - `archive-rows` of `foo-03`: `row:foo-03`, `archive:foo-03` and
      `archive:foo-3`.
  - **`*`:** an import, a cutover and a rollback each store exactly one key,
    `*`; a `stray-import` stores its lines' keys as a write does; a
    `stray-revert` stores none.
  - **The cascade:** a cutover aborted by `switch_to_files`, and an import's
    reset before a confirmed cutover, each succeed and leave no
    `journal_scopes` row at a deleted seq.
  - **Step 3b,** for an act with base `b` and `expect` listing `row:l1`:
    - an entry after `b` made outside any act (`act_id` NULL) that touched
      `row:l1` refuses the act's write with LR009. Its first stderr line is
      `ERROR:  LR009: expectation: row:l1 changed at seq <k> by
      <lane>/<verb>`, with that entry's seq and its journal `lane` and
      `verb`;
    - with two such entries, at `k1 < k2`, the message names `k1`; with one
      entry touching two listed scopes, it names the first in byte order;
    - an `expect` rewritten as `ROW:L1` is compared in lower case, and
      refused the same way;
    - **`*` after the base:** rewritten to base 0, before the cutover's `*`
      at seq 1, the write is refused with LR009 naming its first listed
      scope, at seq 1, by the cutover's lane and verb;
    - an empty `LANES_EXPECT`, sent as `expect: []`, records the act's
      `act_id` and checks nothing, so the write commits after such an
      entry;
    - a direct call after a rollback's entry has set the switch to `files`:
      LR002, not LR009, since step 3 comes before step 3b.
- **Tests.** In `code/tests/test_sql_register_order.py`:
  - `test_a_malformed_act_is_refused_lr004_before_the_head_lock[<case>]`, one
    case per rewrite above;
  - `test_act_id_is_the_acts_id_on_a_write_and_null_on_every_other_entry`;
  - `test_the_act_id_checks_refuse_an_act_id_on_another_kind_and_a_malformed_one`;
  - `test_each_entry_stores_the_keys_its_lines_derive[set-row-state|lane-kind-line|claim|takeover|rename-lane|archive-rows]`;
  - `test_an_import_a_cutover_and_a_rollback_store_only_the_star_key`;
  - `test_a_stray_import_stores_its_keys_and_a_stray_revert_none`;
  - `test_an_aborted_cutover_and_a_reimport_leave_no_scopes_at_a_deleted_seq`;
  - `test_lr009_names_the_first_changed_scope_its_seq_lane_and_verb`;
  - `test_an_entry_outside_any_act_refuses_an_acts_write_lr009`;
  - `test_a_star_key_after_the_base_is_reported_as_the_first_listed_scope`;
  - `test_an_empty_expectation_records_act_id_and_checks_nothing`;
  - `test_lr002_comes_before_lr009_after_a_rollback`.
- **Run time.** 20–40 s: about 30 writes through `lanes-edit.sh`, at about
  0.6 s each, and direct calls.
- **Flakiness.** None: the writes run one at a time, and the one held head
  row is confirmed in `pg_stat_activity`, as AT-LR006's barriers are.

#### AT-LR010. The derived keys, the normalized `archive:` key, and LR010

- **Proves.** Lane 1's v2.5 (#165 6711914507), as §1.2's functions and
  §1.3's "The act" have them: `line_scopes` and `doc_line_scopes` by the
  scopes table's rules; S3's normalized `archive:` key, by
  `RETIRED_ROWS_AWK`'s `key()`; `entry_new_keys`; and N2's `scope_fault`
  and `scope_checked`, whose LR010, "key not well formed", refuses a write
  or an import a line of which derives a key that is empty or holds white
  space. The requirement is "Readers read the database and fail closed",
  its scenario "A line that derives a key that is not well formed". A
  write's LR010 through `append-line` is AT-F5's, and S7's agreement test
  over the whole imported register is AT-7's.
- **Phase and kind.** N. RE.
- **Fixture.**
  - The five functions are called by the admin under `SET ROLE
    lanes_register_owner`, since §1.4 grants no role EXECUTE on them.
  - A clone of `tmpl_imported`, and for the old side a clone brought to
    `primary` with the marker `present`, as AT-LR006's.
  - For the import: commits of test 7's fixture register with one or two
    lines changed, imported by the real `lanes-index import --at <C>`.
- **Oracle.**
  - **`line_scopes`, compared as sets, one case per rule:**
    - `lanes/LANES.md`, a row whose first backticked token is `Lane-A`,
      with a uuid `u` in it: `row:lane-a` and `sid:u`. A line that does not
      open with `|`, holds no backticked token, or whose token is empty,
      gives no `row:` key;
    - `lanes/archive/<f>.md`, rows named `foo-03`, `Foo-3b`, `foo-3`,
      `foo-033`, `foo-00`, `alpha` and `foo-x3`: {`archive:foo-03`,
      `archive:foo-3`}, {`archive:foo-3b`, `archive:foo-3`},
      {`archive:foo-3`}, {`archive:foo-033`, `archive:foo-33`},
      {`archive:foo-00`, `archive:foo-0`}, {`archive:alpha`} and
      {`archive:foo-x3`};
    - a long digit run: `foo-09007199254740993` gives
      `archive:foo-9007199254740993` beside its own key, with no digit
      lost, and a run of 30 digits keys with no exponent.
      `RETIRED_ROWS_AWK`'s `key()` gives `foo-9007199254740992` for the
      first under mawk and BusyBox awk: recorded, not asserted, since the
      awk's fix is lane 4's, for phase 2 (the routed list);
    - `lanes/log/Lane-B.md`, a line whose verb before ` — ` is `STARTED`,
      `RESUMED`, `PAUSED`, `ENDED`, `RETIRED` or `RENAMED`, with spaces or
      tabs around it: `bind:lane-b`, and `sid:` for each uuid in it. Such a
      line that the parser cannot read, with no `lane <name>` field, gives
      `bind:lane-b` too, the over-report the design takes. A `CLAIMED` or
      `HANDOFF-REQUESTED` line gives no `bind:` and no `sid:`;
    - `lanes/aliases.tsv`, `X`, a tab, `O`, a tab and a UTC: `alias:x` and
      `alias:o`. A line opening with `#`, or a field of spaces and tabs
      alone, gives no key;
    - a `lanes/STORE` line gives none.
  - **`doc_line_scopes`** adds `objects:` from a line's structured row: a
    readable `log_lines` row on an object that is not `lane:…` gives
    `objects:` and its lane, lower-cased; an unreadable one, or one on
    `lane:<x>`, gives none; a `register_lines` slot line gives `objects:`
    and its lane, and a `LANDING EXPIRED` also its `for_lane`.
  - **`entry_new_keys(seq)`,** for a write's seq: each appended or replaced
    line's keys, with its path and `line_no`; each line a rename moved, at
    its new path; and `objects:` with `lower(lane_key)` for every holder of
    an object the write wrote a hold for, at that holder's file and ordinal
    (N1).
  - **`scope_fault`:** NULL for each well-formed key above. Otherwise the
    key, with each white-space character shown as `[U+hhhh]`: `row:x y`
    gives `row:x[U+0020]y`, and each of Unicode's 25 White_Space code
    points, U+00A0 among them, gives its own `[U+hhhh]`. `row:`, with no
    name, and `row:X`, in upper case, are returned as they are.
  - **`scope_checked(path, line, key)`** returns a well-formed key as it is,
    and otherwise raises LR010 with `key: <path> line <n> derives <shown>,
    which is not a scope (a scope's name is lower case, not empty, and holds
    no white space)`.
  - **The CHECK is a backstop only:** as the owner, in a transaction the
    test rolls back, a `journal_scopes` row `row:x y` fails with 23514 on
    `journal_scopes_scope_check`, but the same key with U+00A0 in place of
    the space is stored, since `[:space:]` under `COLLATE "C"` is ASCII's
    alone. Only `scope_checked` stops it.
  - **At a write's old side:** a stored line that derives such a key, put
    in place by the admin as the owner, since no write or import can store
    one, and a direct call whose entry deletes it: LR010 from step 5a,
    naming that path and line, and nothing written.
  - **At an import:** a commit whose `lanes/LANES.md` holds the row-shaped
    line `` | `x y` | ``: `lanes-index import` exits 2 with the message
    naming `lanes/LANES.md`, that line's number and the key, and the head,
    the journal and every table of §1.2 equal their values before. With two
    such lines, the message names the first in path and line order. The
    same with U+00A0.
- **Tests.** In `code/tests/test_sql_register_schema.py`:
  - `test_line_scopes_derives_each_scope_by_its_rule[row|archive|archive-normalized|long-digit-run|bind|bind-unreadable|not-lane-kind|alias|alias-comment|store]`;
  - `test_doc_line_scopes_adds_objects_from_the_structured_rows[log-line|unreadable|lane-object|slot-line|landing-expired]`;
  - `test_entry_new_keys_reads_the_new_side_and_every_holder`;
  - `test_scope_fault_shows_each_white_space_character[<code point>]`, one
    case per code point, and a case each for `row:` and `row:X`;
  - `test_scope_checked_raises_lr010_naming_the_path_the_line_and_the_key`;
  - `test_the_journal_scopes_check_takes_a_no_break_space`;
  - `test_a_write_that_deletes_a_line_with_a_bad_key_is_refused_lr010`;
  - `test_an_import_whose_line_derives_a_bad_key_is_refused_lr010_and_writes_nothing[space|no-break-space|two-lines]`.
- **Run time.** 10–20 s: the import cases run one import each.
- **Flakiness.** None: the functions are IMMUTABLE or STABLE, and every
  case has fixed inputs.

#### AT-LR011. `find_write`: its six arguments, its wait, and its order

- **Proves.** `find_write(p_write_id, p_not_after, p_generation, p_lane,
  p_verb, p_args_sha256)` as §1.3.1 has it, lane 1's v2.6 and v2.8 (#165
  6714645316 and 6715661308), at the engine: it waits on the server's
  clock, holding no lock, until the clock has passed `p_not_after`, for at
  most 3 s; then it takes the head row's lock, checks the clock again under
  it, then the generation, then the entry, so that it finds only this
  write's own entry, in this generation (§1.3's step 9 and its signals
  table). The requirement is "A COMMIT whose outcome the writer cannot
  learn is recovered by its write id", its scenarios "The lookup waits past
  `not_after`", "A lookup refused for another generation" and "A lookup
  refused for another write". AT-F3 holds the client's lookup, its window,
  its exits and its record, and LR011's refusals at once.
- **Phase and kind.** N. RE, on the session cluster.
- **Fixture.** A clone of `tmpl_imported` on `primary`, as AT-LR006's, with
  write W committed (lane L1, verb V, `args_sha256` a). Calls as
  `lanes_register_writer`, each in a transaction at READ COMMITTED that the
  test rolls back, with the lookup's bounds from D9's table
  (`lock_timeout=2000ms`, `statement_timeout=6000ms`). Any holder of the
  head row is a test session held through a FIFO, and every barrier is a
  state polled in `pg_stat_activity`, as AT-LR006's are.
- **Oracle.**
  - **Found and absent:** with `p_not_after` passed, W's id with its own
    generation, lane, verb and `args_sha256` returns W's seq; an id not in
    the journal returns NULL.
  - **Another write's entry,** LR007: W's id with another lane, another
    verb, another `args_sha256`, or a NULL `p_args_sha256`, which never
    matches a write's entry.
  - **Another generation,** LR007: W's id, and an id not in the journal,
    each with a generation not the switch's.
  - **LR011 before LR007:** with `p_not_after` 4 s ahead and a generation
    not the switch's, the code is LR011.
  - **The wait:** with `p_not_after` 2 s ahead and the head row held by a
    test session, the call's backend is seen first with `wait_event` =
    `PgSleep`, holding no lock, and then with `wait_event_type` = `Lock`.
    Once the holder ends, it returns, and `clock_timestamp()` read next in
    the same transaction is not before `p_not_after`.
  - **The bound:** with `p_not_after` 2.9 s ahead and the head row held
    throughout, the call ends with 55P03 from its `lock_timeout`, not with
    57014: its wait and its lock fit its `statement_timeout`.
  - **The exact complement:** a commit call O of write X, whose entry's
    `not_after` is 2 s ahead, waits on the head row behind a test session.
    O's transaction first runs `SET LOCAL lock_timeout = '5s'`, a
    user-settable parameter, since under the writer's 2000 ms O would get
    55P03 at about the moment `not_after` passes and never reach step 9.
    The lookup for X with that `not_after` waits past it, then queues for
    the lock under its own 2000 ms. The holder rolls back once the admin
    session reads `clock_timestamp()` past `not_after` and sees both O and
    the lookup waiting with `wait_event_type` = `Lock`, and the test rolls
    the lookup's transaction back as soon as its call returns. Whichever of
    the two takes the lock first, O ends with LR008, the lookup returns
    NULL, and no journal row holds X's id.
  - **Not run here:** the check under the lock fails only where the
    server's clock steps back after the wait, which a shared host cannot
    make ("The schema" says the same in "New and unproven"). Section 7
    holds it.
- **Tests.** In `code/tests/test_sql_register_in_doubt.py`:
  - `test_find_write_returns_this_writes_seq_or_null[found|absent]`;
  - `test_find_write_refuses_another_writes_entry_lr007[lane|verb|args|null-args]`;
  - `test_find_write_refuses_another_generation_lr007[found|absent]`;
  - `test_find_write_gives_lr011_before_lr007`;
  - `test_find_write_waits_on_the_server_clock_holding_no_lock`;
  - `test_a_lookup_held_at_the_head_row_ends_55p03_inside_its_statement_timeout`;
  - `test_an_orphan_that_takes_the_lock_past_its_not_after_gets_lr008_and_the_lookup_finds_it_absent`.
- **Run time.** About 15 s: the waits of 2 to 5 s dominate.
- **Flakiness.** The waits are on the server's clock and the holds on FIFOs,
  confirmed in `pg_stat_activity`. No elapsed time is asserted: the one
  time asserted, `clock_timestamp()` not before `p_not_after`, is a lower
  bound on the server's own clock (section 4's rule 3).

#### AT-LRLABEL. `register_label=`, and the cutover's two refusals

- **Proves.** The requirement "The cutover takes its database label from
  the workstation's configuration", in lane 1's v2.8 S-2 (#165 6715661308):
  `cutover`, in every form, takes the label it sends from `register_label=`
  and from nothing else (§1.4's "The file"; §2.3), and refuses with 2 where
  that key is missing, and where the switch reads `files` and the key is not
  the switch's label. "The schema" marks these client rules untested. N4's
  LR004s in the transaction are AT-F18's; the restore route and a host
  still pointed at the live database are AT-D22's; `ready`'s check 10 is
  AT-P8's.
- **Phase and kind.** N for the configuration key; G for `cutover`'s
  refusals and its label, since the cutover's cases are G (section 1.3's
  table, test 7). FP for the refusal before `cutover` connects, RE for the
  rest.
- **Fixture.**
  - `lanes-index.conf` files the test writes, with `register_label=` absent,
    equal to the switch's `database_label`, or another label.
  - For `cutover`: test 7's import on a clone of the template, the switch at
    `files`, run by the operator lane (F12), through the shim.
- **Oracle.** Each refusal writes nothing: the head, the journal, the switch
  and `lanes/STORE`'s rows equal their values before.
  - **The key:** a file carrying `register_label=` beside `register_url=` is
    not refused as an unknown key: `lanes-index mode` reads it and exits 0
    (the requirement "`mode=` selects the register's store").
  - **No `register_label=`:** `cutover` exits 2 with `cutover:
    lanes-index.conf has no register_label= (S-2)`, and the fake `psql`'s
    log holds no call: it refused before it connected.
  - **A label not the switch's, at `files`:** at a first cutover, and in the
    after-loss form after a re-import, `cutover` exits 2 with `cutover:
    register_label=<x> is not the switch's label <y>; set register_url=
    and register_label= together (S-2)`, and the shim's log holds no commit
    call.
  - **The label it sends:** at a first cutover with `register_label=` the
    switch's label, the cutover's `lanes/STORE` line `database=` carries
    that value.
- **Tests.** In `code/tests/test_sql_register_cutover.py`:
  - `test_lanes_index_accepts_the_register_label_key`;
  - `test_cutover_without_register_label_is_refused_2_before_it_connects`;
  - `test_cutover_at_files_with_a_label_not_the_switchs_is_refused_2_before_it_sends[first|after-loss]`;
  - `test_a_first_cutovers_store_names_the_register_label`.
- **Run time.** 3–6 s.
- **Flakiness.** None: each case is one command with fixed inputs.

#### AT-LRMPG. A managed PostgreSQL: the bootstrap without a superuser, the logins and `lanes-index login`, the dump, the restore, checks 11 and 12, and `target_session_attrs`

Every case here tests what commit W folded into the design for Brett's
managed PostgreSQL, and each test names its section:
- §1.3's "The connection these bounds need";
- §1.4's "On a managed PostgreSQL", "One engineer's logins", the login
  step and its revoke, "What a revoked login sees", the roles table's dump
  row, "TLS and the endpoint" and "One engineer's file";
- `operations.md` act 3's step 1, the daily `pg_dump`; §2.3's restores;
  and §2.4's `lanes-index bootstrap`, `ready` (checks 11 and 12) and
  `lanes-index login`;
- the sql-register delta's "The operator's bootstrap is idempotent and
  holds no secret", with its scenario "An engineer's logins (real
  engine)", and "The readiness check proves an environment before the
  cutover".

Brett's rulings behind them are lane 3's 6722541787 (the backups),
6722593397 (the provider, the dump job and its storage) and 6723413756
(two logins each, after "One login per engineer"; public access;
`verify-full`). Lane 1's labels below, M1 to M6 and O1 to O8, name its
texts that W folded (#165 6722314667 and 6722693446).

- **Proves.** Lane 1's section holds on that service:
  - the bootstrap runs as an admin with `LOGIN CREATEROLE CREATEDB` and no
    `SUPERUSER`, as `azure_pg_admin`'s members are, twice with one catalog,
    and refuses with 2 a database that admin does not own, where its
    database-level `REVOKE` and `GRANT` would only warn (M4, O7);
  - `lanes-index login add <engineer>` makes that engineer's two logins,
    `lanes_w_<engineer>` and `lanes_r_<engineer>`, each a member of its
    group and carrying its group's floors, and prints their password-file
    lines once; `add --dump-job` makes `lanes_register_dump`, a member of
    READER alone; a rerun puts back a lost membership and changes nothing
    else; `rotate` and `revoke` act as §2.4 has them; and a member made
    without the login step carries none of the floors;
  - two engineers' writer logins write alike, and the server's log names
    the login that wrote; a revoked login is refused at connect and in its
    open session, and its writes and reads exit 69;
  - the dump job's login dumps both schemas and still cannot write (M3, M6,
    O2);
  - a restore from the dump, and one from the provider's own copy, each
    followed by new passwords, give the source's catalog and rows (O5);
  - `ready`'s check 11 confirms TLS and the primary on each connection
    (O8), and check 12 each login's own floors;
  - `target_session_attrs=primary` on `reader_url=` lets the reader login
    in, and `read-write` keeps it out (M5).
- **Phase and kind.** N, but the logins' writes and reads, which run through
  the flipped test copy of `lanes-index` (test 14's second state), are W.
  RE.
- **Fixture.**
  - A `stoppable_cluster` for each case that makes roles, since no test
    changes a role's attributes on the session cluster (section 3.1). Its
    superuser stands for the provider: it makes one admin role, `LOGIN
    CREATEROLE CREATEDB NOSUPERUSER`, for `azure_pg_admin`'s member, with a
    libpq service of its own for `--admin-service`. That admin creates the
    register's database, and so owns it.
  - Logins, made through the admin's service by `lanes-index login add e1`,
    `login add e2` and `login add --dump-job`, each under the pty:
    `lanes_w_e1`, `lanes_r_e1`, `lanes_w_e2`, `lanes_r_e2` and
    `lanes_register_dump`. E1 and E2 below are the two writer logins. Two
    bare members, `lanes_w_bare` and `lanes_r_bare`, are made by the test's
    own `CREATE ROLE … LOGIN` and a `GRANT` of the group, without the login
    step.
  - For password cases, a `pg_hba.conf` that takes `scram-sha-256` on the
    cluster's socket; for check 11, section 5.6's TLS cluster (section 3.1).
  - **A terminal, or `--no-passwords`.** `bootstrap` prints a role's
    password-file line only to a terminal, and refuses off one unless it is
    given `--no-passwords` (§2.4, "The secret"), as AT-P8 shows. The design
    supports both for `bootstrap`, and this entry uses both. A run whose
    roles need no
    password here, the bootstrap without a superuser and the database the
    admin does not own, passes `--no-passwords`. A run that must show a
    password line, or show that it prints none, runs under a pty
    (`pty.openpty()`), as AT-P8's printing run does, and the test reads its
    output: each restore's `bootstrap --roles-only` and the `bootstrap`
    after it, and every `lanes-index login` run, which also refuses off a
    terminal (§2.4).
  - **The logins' password files** are 0600 files under the test's own
    temporary `$HOME`: `$HOME/passfiles/<login>` for each login, holding the
    line `login add` printed for it, or, for a bare member, the password
    the test set with `ALTER ROLE … PASSWORD`; and
    `$HOME/passfiles-new/<role>` for the lines a restore prints and the new
    passwords the provider's-copy case sets. Each engineer's
    `register_passfile=` and `reader_passfile=` name only that engineer's
    two (§1.4, "One engineer's file"), and the dump's `PGPASSFILE` names
    the dump login's.
  - `pg_dump` and `pg_restore` from the directory the fixture chose, and
    `openssl`, which check 11's TLS cluster needs for its test CA (section
    5.6, M12's CI row), each of which the test checks for itself, since the
    fixture's check names four tools.
  - For the logins' cases and the restore cases, the cluster logs as section
    5.6's counting cluster does: `log_connections=on`, `log_statement=all`,
    and each line prefixed with its backend's pid.
- **Oracle.**
  - **The bootstrap without a superuser** (M4): as that admin, `bootstrap`
    exits 0, run twice. The catalog of `lanes_register` (its objects,
    owners, ACLs and default ACLs) and the roles' attributes and
    memberships are equal after the first run and after the second. A
    `commit_write` call with an import entry, as a writer login, commits: a
    `SECURITY DEFINER` function owned by a role that cannot log in works.
    AT-P8's bootstrap runs as the cluster's superuser, so this is the one
    test of the non-superuser admin.
  - **A database the admin does not own** (O7): one made by the cluster's
    superuser. `bootstrap` exits 2 and makes no schema in it. The control:
    there, as that admin, `REVOKE ALL ON DATABASE` and `GRANT CONNECT` only
    warn, with 01006 and 01007, and `pg_database.datacl` is unchanged.
  - **Two writer logins** (§1.4, "One engineer's logins"): E1 and E2,
    each with its own
    `register_url=` and password file, each make one write, and both commit
    as one login does: one journal row each, at consecutive seqs. The log
    ties each commit call, by its backend's pid, to its `connection
    authorized` line, which names E1 or E2.
  - **A revoked login** (§1.4, "What a revoked login sees"; §2.4's
    `revoke`, lane 3's ask). Each case runs on a `stoppable_cluster` of its
    own, with the fixture's logins, while a session of `lanes_w_e2` is open
    and idle, held by the test through a FIFO:
    - **[revoke]:** `lanes-index login revoke e2` runs and prints each
      step's result. A new connection by either of e2's logins is refused,
      FATAL 28000, so e2's write exits 69 with nothing written, since the
      snapshot sent nothing that writes (§1.3's step 1; S1, "The
      outcomes"), and e2's read exits 69 (S3's table). `revoke` runs the
      block's three steps in one `psql` script per login, with no point
      between them for another statement (§1.4's revoke block), so the
      open session was ended before `revoke` returned: its next statement,
      a write or a read, gets FATAL 57P01, "terminating connection due to
      administrator command", or a lost connection, and never 42501 or an
      answer. The admin, with no superuser, needed no
      `pg_signal_backend`. Both roles still exist. E1 still writes;
    - **[steps-by-hand]:** the 42501 between the steps. The admin runs the
      revoke block's steps 1 and 2 for `lanes_w_e2` by hand, through his
      service. The open session's next statement, a write, and then a read,
      each gets 42501, "permission denied for schema lanes_register", since
      the schema's privileges came through the group. The admin then runs
      step 3 by hand, and the session's next statement gets FATAL 57P01;
    - **[membership-only]:** the admin runs the revoke block's step 2 alone
      for `lanes_w_e2`, by hand, so that it keeps `LOGIN` and loses its
      membership: a new connection by it is refused, FATAL 42501,
      "permission denied for database", since its `CONNECT` came through
      the group;
    - **[mid-commit]:** the shim's `hold` blocks e2's commit call while
      `revoke` runs, then releases it: its `psql` is refused at login and
      exits 2, which §1.3's signals table makes in doubt for a commit call;
      its lookup's tries are refused at login too, and it exits 3 naming
      the id, the pending record kept. The journal, read by the admin,
      holds no entry for the id, and so answers it (§2.4's `revoke`).
  - **The login step** (§1.4's login step; the delta's "An engineer's
    logins (real engine)"). After `login add e1`, `lanes_w_e1` and
    `lanes_r_e1` exist, each a member of its group, and their two
    password-file lines and the `register_url=` and `reader_url=` lines for
    e1's file were printed once. The server's log holds each password only
    as a SCRAM verifier, never as clear text. A rerun, after the test has
    revoked `lanes_w_e1`'s membership by hand, puts it back and leaves every
    other attribute and floor as it was. `login add --dump-job` makes
    `lanes_register_dump`, a member of READER alone, with the reader's
    floors.
  - **The floors, and a bare member.** `SHOW` in a session of each login
    the step made gives its group's floors. `lanes_w_bare` and
    `lanes_r_bare` show none: `lock_timeout`, `statement_timeout` and
    `idle_in_transaction_session_timeout` 0, and
    `default_transaction_read_only` off (lane 1's run).
  - **Check 12** (§2.4). With e1's logins in the configuration, check 12
    passes, `check 12: ok`. With `lanes_w_bare` as
    `register_url=`'s user, it fails, naming the login and each floor it
    lacks, and the run exits 2.
  - **`rotate`** (§2.4). `lanes-index login rotate e1` prints new lines. A
    session of `lanes_w_e1` already open goes on. A new connection with the
    old password file is refused at login, and e1's write exits 69; with
    the new lines written, e1 writes.
  - **`login`'s refusals** (§2.4). An engineer name that is not
    `^[a-z][a-z0-9_]{0,40}$` (`E1`, `1e`, and one of 42 characters), and an
    argument that carries a connection string, each exit 2 and make
    nothing. Run through a pipe, `login add` refuses and makes nothing.
  - **The dump** (M3, M6, O2; 6722541787): on a clone of `tmpl_imported`
    with writes after its import, where `lanes_indexer` has made #160's
    store with `lanes-index`'s own scripts and written its rows, O2's
    command, `umask 077 && pg_dump --no-password --schema=lanes_register
    --schema=lanes_index --format=custom --file=<name>`, run as
    `lanes_register_dump`, the dump job's reader login, with its connection
    from its environment and `PGPASSFILE` naming its password file:
    - exits 0, and writes a file of mode 0600 that holds both schemas: each
      table of §1.2 with its sequence and views, and #160's tables and its
      view, `holds`;
    - `pg_restore --data-only --schema=lanes_register --table=register_head
      --file=- <file>` on the file alone gives the database's head `b`;
    - after `lanes_indexer` makes a sequence in `lanes_index`, the dump still
      exits 0, through M6's default privilege. The control: with that
      default privilege revoked by the admin before the sequence is made,
      the dump stops with 42501 at that sequence; and with the reader role's
      `SELECT` on `doc_lines_line_id_seq` revoked, it stops at that one
      (M3);
    - inside `BEGIN READ WRITE; … ROLLBACK;`, as AT-R tries a refused write,
      the dump login still gets 42501 on `nextval` of
      `doc_lines_line_id_seq` and on an `UPDATE` in `lanes_register`, and on
      `nextval`, `setval`, `INSERT`, `UPDATE`, `DELETE` and `TRUNCATE` in
      `lanes_index`.
  - **The restore from the dump** (O5; 6722541787): on a new
    `stoppable_cluster` with the admin role. The admin service reaches
    `psql` only as `PGSERVICE`, so the service names the database, and the
    test's `~/.pg_service.conf` holds two of the admin's: one naming the
    cluster's `postgres` database, for the steps before the register's
    database exists, and one naming that database, for the steps after
    (section 7 asks lane 3 which the design means). Through the first,
    `bootstrap --roles-only`, run under the pty, makes the groups and the
    indexer's login and prints only the indexer's line, since a dump holds
    no login and no membership (§1.4); `login add e1` and `login add
    --dump-job` then make those logins again and print their lines, which
    the test writes into new 0600 files; and the admin creates the
    database. Through the second, he restores both schemas into it with
    `pg_restore --exit-on-error --no-password --dbname=<database> <file>`,
    and runs `bootstrap`, which prints `unchanged`. Both schemas' catalogs,
    their rows and `holds` equal the source's, each ACL compared as a set of
    its elements. The source's password file is refused there, and the
    printed lines connect. The new cluster's log holds each printed password
    only as a SCRAM verifier, never as clear text, since `bootstrap` sets it
    on the client (O6).
  - **The restore from the provider's copy** (O5; 6722541787): a copy of the
    source cluster's stopped data directory, started as a new server,
    stands for a provider's snapshot or point-in-time restore, which keeps
    the roles and their passwords. `bootstrap --roles-only`, run under the
    pty, prints `unchanged` and no password line. The admin then sets a new
    password for each login, as psql's `\password` does, and the test writes
    each into its login's password file: the old password is refused, the
    new one connects, and the server's log holds each new password only as
    a SCRAM verifier, never as clear text. That is `\password`'s doing, not
    `bootstrap`'s: O6's rule for `bootstrap`'s own statements is checked in
    the restore from the dump, above.
  - **Check 11** (O8), on the TLS cluster: with each connection's URL
    carrying `sslmode=verify-full` and `sslrootcert=` naming the test CA,
    which stands for the provider's, check 11 passes. It fails for a URL
    with `sslmode=prefer`, and for one with no `sslrootcert=`. It fails on
    the session cluster's Unix socket, where libpq ignores `sslmode` and
    `pg_stat_ssl` shows `ssl` false. And it fails against the TLS cluster
    restarted in recovery, with a `standby.signal` file in its data
    directory, where `pg_is_in_recovery()` is true; that start polls for
    `ready`, as every start does, then fails unless `pg_is_in_recovery()`
    is true: a hot standby writes `ready` to `postmaster.pid`, and
    `standby` only with `hot_standby` off, where no session connects
    (PostgreSQL 16; lane 3's batch 11 (1)).
  - **`target_session_attrs`** (M5): `lanes_r_e1`'s `psql` with
    `target_session_attrs=primary` in its URL connects and reads
    `register_head`; with `read-write`, the connection is refused with
    libpq's `session is read-only`, since the login step made its sessions
    read-only by default (§1.4). `lanes_r_bare`, a member made without the
    step, is let in by `read-write`: the floor is the login's own, since
    `ALTER ROLE … SET` binds the role it names and not its members.
- **Tests.** In `code/tests/test_sql_register_managed.py`:
  - `test_bootstrap_without_a_superuser_runs_twice_with_one_catalog` (§1.4,
    "On a managed PostgreSQL");
  - `test_bootstrap_refuses_a_database_its_admin_does_not_own` (§2.4's
    refusals);
  - `test_login_add_makes_an_engineers_two_logins_with_their_floors_and_prints_their_lines_once`
    (§1.4's login step; the delta's "An engineer's logins (real engine)");
  - `test_login_add_run_again_puts_back_a_lost_membership_and_changes_nothing_else`
    (§2.4);
  - `test_a_member_made_without_the_login_step_carries_no_floor_and_fails_ready_check_12`
    (§1.4, "One engineer's logins"; §2.4, check 12);
  - `test_ready_check_12_passes_the_logins_the_step_made` (§2.4, check 12);
  - `test_login_rotate_keeps_an_open_session_and_refuses_the_old_password`
    (§2.4);
  - `test_login_refuses_a_bad_name_a_connection_string_and_a_pipe[name|connection-string|pipe]`
    (§2.4);
  - `test_two_writer_logins_write_alike_and_the_log_names_each` (§1.4, "One
    engineer's logins"; `operations.md` act 3's step 1, "Who connected");
  - `test_a_revoked_login_is_refused_69_and_writes_nothing[revoke|membership-only]`
    (§1.4, "What a revoked login sees"; §2.4's `revoke`);
  - `test_an_open_session_gets_42501_between_the_revokes_steps_then_57p01`
    ([steps-by-hand]; §1.4, "What a revoked login sees");
  - `test_a_write_cut_by_a_revoke_mid_commit_is_in_doubt_and_ends_3_with_nothing_written`
    (§2.4's `revoke`);
  - `test_the_reader_login_dumps_both_schemas_and_still_cannot_write`, as
    `lanes_register_dump` (`operations.md` act 3's step 1; §1.4's dump row);
  - `test_a_restore_from_the_dump_equals_its_source_with_new_passwords`
    (§2.3, the restore from the daily `pg_dump`);
  - `test_a_restore_from_the_providers_copy_keeps_the_roles_and_takes_new_passwords`
    (§2.3; D22);
  - `test_ready_check_11_needs_tls_and_the_primary[passes|sslmode-prefer|no-sslrootcert|unix-socket|in-recovery]`
    (§2.4, check 11; §1.4, "TLS and the endpoint");
  - `test_reader_url_takes_target_session_attrs_primary_not_read_write[primary|read-write|read-write-bare-member]`
    (§1.4, "TLS and the endpoint").
- **Run time.** 30–50 s: five or six stoppable clusters at about 0.9 s
  each, two dumps, two restores, the `login` runs under the pty, and a few
  writes; the mid-commit case waits about 18 s for its lookup's window.
- **Flakiness.** None: each case is a command's exit and a catalog, row or
  log comparison, and the recovery case waits on the server's own `ready`
  state, then reads `pg_is_in_recovery()`.

### Tests for the frame review's findings

Written against lane 3's rulings in #165 comment 6578269040, on lane 3's review
of the frame (F-numbers are that review's), as folded at `6a5fcdc6` and
`1c45123a`, and at `21f0191c`, and as §1.3 and §2.1 to §2.5 carry them. Each
entry is short: the
ruling, its fixture, its oracle, and its test names. AT-F34, the last, is
not a finding of that review: it is lane 4's section's "The tail, once",
routed to this plan with lane 4's texts for commit N (#165 6714767210) and
lane 3's ruling of lane 4's row 24 (comment 6710770304).

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
    entry as the last seq, declares first (§2.3, step 2, "A rerun"; AT-D20
    holds that case), and goes on from the render.
  - A re-import after the rollback continues the journal: its baseline takes
    `k+2`, with base `k+1`, and a later cutover takes `k+3`, so no
    `Register-Seq` repeats on `main` (§1.3's kinds table).
  - Over the life of one database, a cutover and then a rollback leave
    exactly two journal rows with `operator` true: the cutover's and the
    rollback's.
  - **LR012 in the transaction** (§1.3, "The rollback's divergence gate
    (S5)"; lane 1's v2.6, behaviour 4 of `renames-v2.6.md`), by direct
    calls as `lanes_register_writer`, with the switch at `primary` and the
    marker `present`:
    - with a divergence recorded by `record_mirror_state`, a `rollback`
      entry gets LR012, and the head, the switch, the journal and
      `mirror_state` are unchanged. The call's first stderr line is
      `ERROR:  LR012: rollback refused: diverged at <sha>, found <utc> by
      <ws>; resolve it first with import --single or render --revert (F1,
      S5)`, with the `<sha>`, `<utc>` and `<ws>` that `mirror_state` holds;
    - with `record_mirror_state`'s transaction open first, recording a
      divergence, the rollback's call waits on `mirror_state`, as
      `pg_stat_activity` shows, and gets 40001 once that transaction
      commits; its retry, on the head read afresh, gets LR012;
    - the same where that transaction recorded only `last_error`: the
      retry commits;
    - with the rollback's call open first, held before its `COMMIT`,
      `record_mirror_state` waits on the row, as `pg_stat_activity` shows,
      until the rollback's transaction ends;
    - in no case does any session get 40P01: `commit_write` takes the head
      row, then `mirror_state`, and `record_mirror_state` takes
      `mirror_state` alone, so no two of them wait on each other.
  - **LR012 through the command:** the shim holds `lanes-index rollback`'s
    call after its divergence check has passed, and the test's session
    records a divergence with `record_mirror_state` meanwhile. On release
    the call gets LR012, and `rollback` exits 2 with `diverged at <sha>`
    and S2's two ways out, writing nothing: the switch still reads
    `primary` with the marker `present`.
- **Tests.** In `code/tests/test_sql_register_rollback.py`:
  - `test_rollback_refuses_to_start_while_the_mirror_is_diverged`;
  - `test_the_marker_removal_entry_is_written_with_the_switch_in_step_1`;
  - `test_a_rollback_rerun_resumes_after_its_own_entry`;
  - `test_a_reimport_after_a_rollback_continues_the_journal`;
  - `test_a_cutover_and_a_rollback_leave_exactly_two_operator_entries`;
  - `test_a_rollback_entry_over_a_recorded_divergence_is_lr012[recorded|recorder-first|last-error-only|rollback-first]`;
  - `test_rollback_exits_2_on_lr012_naming_the_divergence_and_both_ways_out`.

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
    LR002. Its rerun reads origin back first: a marker found there completes
    the cutover, as in "Pushed, reported failed"; a marker proven absent has the rerun push again, as a rerun that finds its own entry goes on to the cutover's step 2, and step 2's own ends follow, its abort among them (`operations.md` §2.2, act 4's step 7; lane 3, #165 6770600106, T116's point 1). The same for the rollback's removal push: `rollback`
    exits 3, the marker stays `removing`, and its rerun reads origin back,
    then records the marker `absent` or pushes the removal again (§2.3,
    step 4). A files-mode write that lands on origin after the 3, once the
    removal is there (§2.3, step 3), changes neither: the rerun finds its
    removal, pushes nothing, ends 0 with the marker `absent`, and records
    no divergence (lane 3, #165 6814260122, T326's review MUST-1).
  - Held across git: with the push held on the wrapper's FIFO for 6 s, past
    the writer role's 5 s idle bound, the cutover still completes, since its
    transaction committed before any git step (F23, §2.1).
- **Tests.** In `code/tests/test_sql_register_cutover.py`:
  - `test_a_marker_only_entry_is_an_operator_entry_the_renderer_skips`;
  - `test_the_operator_entry_is_pushed_with_the_renderer_identity_and_read_back`;
  - `test_a_cutover_abort_deletes_seq_1_only_when_the_marker_is_proved_absent[not-pushed|pushed]`;
  - `test_an_unconfirmed_operator_push_exits_3_and_its_rerun_reads_origin_first[cutover|rollback]`;
  - `test_no_operator_command_holds_a_transaction_across_git`;
  - `test_a_render_stops_before_an_operator_seq_not_yet_on_origin[cutover|rollback]`;
  - and, in `code/tests/test_sql_register_rollback.py` (T273),
    `test_a_rerun_after_an_unconfirmed_removal_and_a_files_mode_write_ends_0_with_the_marker_absent`.

#### AT-F3. A write in doubt at the deadline

- **Ruling.** Once any commit call of a write was in doubt, the write ends
  with no code but 0 until it has made F3's lookup. A refusal at the
  commit, the verb's own code before `ready`, a snapshot that fails or
  refuses, and the deadline's 75 each wait for it (§1.3's signals; S1, "A
  write in doubt"; lane 4's section, "The in-doubt rule (F3, D8)"). The
  lookup is lane 1's v2.8
  `find_write(<id>, <n>, <g>, <lane>, <verb>, <a>)`, made by
  `lanes-index journal --write-id <id> --not-after <n>` at READ COMMITTED
  in a transaction it rolls back. `<n>` is the largest `not_after` among
  the pending record's `call` lines:
  - the client calls once its own estimate, the last snapshot's database
    clock plus `SECONDS` since that snapshot call ended, puts `<n>` at most
    2 s ahead (AT-D9c (c));
  - `find_write` waits for the rest on the database's clock, holding no
    lock, at most 3 s. It then takes the head row's lock under the 2000 ms
    `lock_timeout`, checks again that the clock has passed `<n>`, and
    checks the generation, then the write id, in `commit_write`'s order;
  - found, this write's own entry, is 0, and the tail runs once (AT-F34).
    Absent, with the lock held, is the code the last attempt earned: 75,
    69, 2 or the verb's own. LR007 is 2: the record is kept where its
    DETAIL token is `generation`, and removed where it is `another-write`.
    LR011, "lookup too early", is 3, and the record is kept;
  - the tries start inside a 10 s window, which opens when the client's
    own wait ends, each under its own timer, `T_L`, `N_a`·`c` + 7 s: 9 s at
    the fixture's `c` = 2. A try that ends in 55P03,
    in a failed connect or with no answer within its timer is made again,
    at least 1 s after the one before, while a new try can still start
    inside the window. LR011 and LR007 are answers, and are not tried
    again. Where no try takes the lock, the exit is 3 naming the id, and
    the record is kept.

  Lane 4's section adds `not_after`: each commit call carries the
  snapshot's database clock plus its call timer plus 2 s, and
  `commit_write` raises unless `clock_timestamp() < not_after` just before
  it returns. Lane 1's v2.3 puts the check in `commit_write` (comment
  6708990159): `not_after` is a required key of every entry, and step 9,
  the last statement before `RETURN`, raises LR008 unless
  `clock_timestamp() < not_after`, so the whole transaction rolls back.
  Lane 3 ruled LR008 as lane 4 reads it, "not committed" (comment
  6710770304, of 6709038351), for "The in-doubt rule (F3, D8)" and the
  outcomes table's LR row: inside D7's 30 s deadline the
  attempt loop treats it as a lost turn, as it routes 40001, with a fresh
  snapshot and a new attempt under the same write id; at the deadline F3's
  rule above applies; and LR008 by itself is never 3 or 69.
- **Fixture.** Test 12's half-open case: the shim's `before` mode makes the
  first commit call in doubt before it lands, and the old connection is the
  test's session as
  `lanes_register_writer`, which replayed that call's script without its
  `COMMIT` (test 12), so it holds the head row with the write's journal row
  inserted and not committed. As in test 12, the shim's `hold`, armed for
  the writer's next snapshot, keeps that call on its FIFO until the
  replayed `commit_write` call has returned in the test's session, so no
  retry reaches the head row before the session holds it.
  `LANES_REGISTER_DEADLINE_S=1`, under the
  2000 ms `lock_timeout`, so at most one retry starts before the deadline,
  and one that starts ends with 55P03, a lost turn that keeps no `call`
  line; no oracle below counts the retries, and `<n>` stays the first
  call's. The test's session first runs
  `SET idle_in_transaction_session_timeout = 0` (a user-settable parameter),
  before it opens its transaction. The in-doubt call's `not_after`, `<n>`,
  is the first snapshot's clock plus the commit call's 8 s timer plus 2 s.
  The client makes the lookup's first try once its estimate puts `<n>` at
  most 2 s ahead, about 8 s after that snapshot, and `find_write`'s own
  wait ends about 10 s after it. The writer role's own
  `idle_in_transaction_session_timeout` is 5 s, and would end the held
  session before the lookup reaches the head row: [found] would then
  commit into a dead session (25P03), [held] would meet a free head row
  and exit 75, not 3, and [absent] would pass by accident. The shim holds
  the lookup's first try on a FIFO. While it is held, the test's session:
  - **[found]** commits;
  - **[absent]** rolls back;
  - **[held]** stays open until the writer exits;
  - **[no-answer]:** the shim fails every try of the lookup with exit 2,
    as a failed connect does.

  The same estate gives six more cases (lane 4's section, "The in-doubt
  rule (F3, D8)", its tries; the delta's "A lookup made too early", "A
  lookup refused for another generation" and "A lookup refused for
  another write"):
  - **[timer]:** the shim holds the first try past its timer, `T_L`, and the
    test's session rolls back meanwhile;
  - **[55p03]:** the test's session holds the head row until the shim's
    log shows the first try's 55P03, then rolls back;
  - **[connect]:** the shim fails the first try with exit 2, and the
    test's session rolls back;
  - **[generation]:** while the first try is held, the test's admin
    session sets the switch row's `generation` to a new uuid, as a
    re-import's bootstrap does, and the test's session rolls back;
  - **[another-write]:** the test's session replays the call's script with
    the entry's arguments and `args_sha256` changed, so that it is another
    write's entry under the same id, and commits while the first try is
    held;
  - **[too-early]:** no session is held. The shim blocks once the first
    run's in-doubt call has returned, and the test kills that run's
    process group, so its record keeps the `call` line. A plain rerun,
    whose snapshot the shim fails with exit 2, takes the record over. It
    has no snapshot clock, so it calls at once, about 9 s before `<n>`.
- **Oracle.**
  - [found]: exit 0, one journal row for the id, and the record removed;
    the tail runs once, from the kept child of the call in doubt (AT-F34).
  - [absent]: exit 75 with the busy text, no row for the id, the record
    removed.
  - [held]: every try meets 55P03 at the 2000 ms `lock_timeout`. The
    shim's log shows at least two tries, each starting at least 1 s after
    the one before (section 4's rule 3), and every one starting inside the
    10 s window from the first's start, asserted at twice it, 20 s (rule
    2), with the last try's start recorded beside the 10 s with its margin.
    Then exit 3 naming the id, never 75, and the record kept. After the
    test rolls back, a plain rerun reuses the id and writes it once.
  - [no-answer]: the same tries, each a failed connect; exit 3 naming the
    id, never 75, and the record kept.
  - [timer], [55p03] and [connect]: the first try ends with no answer, and
    a second, which the shim's log shows starting at least 1 s after the
    first (rule 3) and inside the window (rule 2, as in [held]), takes the
    lock and finds the write absent: exit 75, and the record removed.
    [timer]'s first try is cut by `T_L`, 9 s, so its second starts in the
    window's last second, and the case rests on two readings lane 3 has
    not stated (section 7): the 1 s counts from the last try's start, as
    [held] counts it, since counted from its end a try cut by its timer
    could never be made again inside the window; and a try may start until
    the window has run its whole 10 s, kept finer than a whole second, or
    in whole seconds with its end inclusive. Under either other reading the
    second try cannot start, and the exit is 3.
  - [generation]: one try. `find_write` raises LR007 with the DETAIL token
    `generation`, and the writer exits 2 with the server's message and
    keeps the record. It prints a plain rerun's refusal for a record of
    another generation (AT-F10), and a plain rerun then refuses with 2 at
    its first snapshot, sending nothing.
  - [another-write]: one try. LR007 with the DETAIL token
    `another-write`; exit 2, and the record removed. The journal holds
    only the entry the test's session committed for the id.
  - [too-early]: one try. `find_write` refuses it at once with LR011, and
    the writer exits 3 naming the id, with the server's message, and keeps
    the record; no second try follows. Once the shim passes snapshots
    again, a plain rerun takes the record over, commits under the same id,
    and exits 0 with one row.
  - In [found], [absent] and [held], the shim's log shows the first try
    starting no earlier than when the last snapshot's clock, plus the time
    since that snapshot, reaches `<n>` less 2 s (whole seconds, so up to
    1 s early; section 4's rule 3). In [held], `pg_stat_activity` shows the
    try in `PgSleep`
    until the database clock passes `<n>`, then waiting on the head row's
    lock (lane 1's v2.6, behaviour 1). Each try's script in the shim's log
    calls `find_write` with the write id, `<n>`, and the generation, lane,
    verb and `args_sha256` that the commit calls sent, which
    `lanes-index journal` takes as `--generation`, `--lane`, `--verb` and
    `--args-sha256`. The exit comes about 10 s after the first snapshot in
    [found], [absent], [generation] and [another-write], and about
    18–20 s after it in [held] and [no-answer], so the test's session must
    outlive that, which the `SET` above allows.
  - **The orphan, LR008.** Before it holds one commit call of the write, the
    shim forks the real `psql` call into its own session (`setsid`, as
    `guard-bind` does), and that call waits on a FIFO before it reaches
    `psql`. The writer's 8 s timer kills the shim, the call's client, and
    does not reach the held call, which holds no lock and outlives its
    client, as a statement still queued at a loaded server after its client's
    timer killed `psql` would (lane 4's section, "The in-doubt rule (F3,
    D8)"). Without the fork the timer would kill the call at 8 s, there
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
  - **LR008, the client's half** (lane 4, comment 6709038351). The shim's
    `edit-entry` mode sets a held call's `not_after` to the snapshot's
    clock plus 2 s, and the shim holds the call 3 s before it reaches
    `psql`, as in the engine's half, so that the call answers LR008 inside
    its 8 s timer:
    - inside the deadline: the writer's first commit call ends with LR008;
      the writer drops that call's `call` line, takes a fresh snapshot,
      makes a new attempt under the same write id, commits, and exits 0,
      with one journal row for the id;
    - at the deadline, with the first call in doubt (the `before` mode and
      the held session above) and each later call ending with LR008: the
      writer makes F3's lookup, and exits 0, 75 or 3 as above;
    - at the deadline, with every call ending with LR008: each answer
      dropped its `call` line, so the write was never in doubt, and it
      exits 75 with no lookup in the shim's log (lane 4's section, "The
      pending record");
    - in no case does an LR008 alone give 3 or 69: the writer's exit is
      decided by a later attempt or by the lookup.
- **Tests.** In `code/tests/test_sql_register_in_doubt.py`:
  - `test_a_write_in_doubt_at_the_deadline_makes_one_lookup[found|absent|held|no-answer]`;
  - `test_the_deadlines_lookup_waits_past_the_largest_not_after`;
  - `test_a_lookup_try_without_an_answer_is_made_again_inside_the_window[timer|55p03|connect]`;
  - `test_a_lookup_refused_for_another_generation_exits_2_and_keeps_the_record`;
  - `test_a_lookup_refused_for_another_write_exits_2_and_removes_the_record`;
  - `test_a_lookup_made_too_early_exits_3_and_keeps_the_record`;
  - `test_find_write_refuses_lr011_at_once[missing|ahead]`, by direct calls
    whose `p_not_after` is NULL, or the clock plus 5 s, under a
    `statement_timeout` of 2 s, so that a 3 s wait would end in 57014
    instead (lane 1's v2.6, behaviour 2);
  - `test_an_orphaned_commit_call_released_after_its_not_after_writes_nothing`;
  - `test_a_commit_past_its_not_after_raises_lr008_and_one_within_it_commits[past|within]`;
  - `test_lr008_inside_the_deadline_is_a_lost_turn_and_the_write_exits_0`;
  - `test_lr008_at_the_deadline_leaves_the_exit_to_find_write[found|absent|held]`;
  - `test_a_write_whose_every_call_ended_lr008_exits_75_with_no_lookup`;
  - `test_lr008_alone_never_gives_3_or_69`.
- **Run time.** The lookup's first try waits for `<n>`, about 8–10 s after
  the first snapshot:
  - [found], [absent], [generation] and [another-write], about 10 s each;
    [held] and [no-answer], about 18–20 s each, the 10 s window after the
    client's wait; [timer] about 19 s, [55p03] about 14 s, [connect] about
    11 s; [too-early] about 2 s, with its rerun;
  - the LR008 cases that reach a lookup (the orphan's, and the three at the
    deadline) add about 10 s each; the engine's half, the
    inside-the-deadline case and the LR008-only case 3–5 s each; LR011's
    direct calls under 1 s;
  - that is about 3–4 min in all.

#### AT-F4. The ruleset inside the freeze, and off before the removal

- **Ruling.** The order is drain, the last `PAUSED` write, the ruleset on, the
  import, the cutover, then the flips. The rollback turns the ruleset off once
  the mirror is proved current, before the removal of `lanes/STORE` is pushed:
  `lanes-index rollback --hold-marker` stops after the proof, and a second
  `lanes-index rollback` resumes at the marker's push (§2.3, step 3, accepted
  by lane 3 in comment 6579459518).
  Brett has named the ruleset's paths: the render set and `lanes/STORE`
  ("Render set + STORE", comment 6721583757), as D23 and the delta's gate
  requirement have them. Whether the org's GitHub plan can name the render
  set is still a fact to find (AT-6's options; section 1.4).
- **Fixture.** Test 13's drill, with the ruleset modelled by test 6's
  pre-receive hook, switched by a file the drill's runbook steps set. The
  hook guards exactly the render set (`lanes/LANES.md`, every
  `lanes/log/<lane>.md`, `lanes/aliases.tsv` and `lanes/archive/`) and
  `lanes/STORE`, and lets the renderer's identity through.
- **Oracle.**
  - The last `PAUSED` write is pushed before the hook is on, and no files-mode
    write is refused by it.
  - With the hook on, a files-mode push to a rendered path is refused, and
    so is one to `lanes/STORE` from an identity other than the renderer's
    (the model works). A push that changes only `lanes/repos.tsv`, or only
    `lanes/log/README.md`, is not refused (the delta's "A person's
    `repos.tsv` edit is not gated"). The import, the cutover and the flips
    succeed.
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
  - an act runs `lanes-edit.sh act-begin` before its first deciding read.
    On primary it reads the head seq `n` and the switch's `generation` in
    one snapshot and prints `<act-id>@<n>:<generation>`; it exits 69 where
    that read fails, or where the switch is not `primary` with the marker
    `present`. Off primary it prints `<act-id>@files` and touches no
    database (lane 4's form, comment 6711545001, as lane 3 ruled in
    6711564365). The act exports the line as `LANES_ACT`;
  - each write carries `LANES_EXPECT="<scope> …"` on its own call, from six
    scopes: `row:<L>`, `bind:<L>`, `objects:<L>`, `alias:<L>`,
    `archive:<L>` and `sid:<uuid>`;
  - the commit refuses with 2, `expectation: <scope> changed at seq <k> by
    <lane>/<verb>`, when a journal entry after the base touched a listed
    scope and carries another `act_id`; the act's own entries never refuse
    it. Lane 3 accepted lane 4's row 20 (comment 6710770304): the `act_id`
    column and each entry's touched keys come with lane 1's v2.4 (#165
    6711221999), which lane 3 folds as commit K (6711233420). The refusal
    is LR009, "expectation changed", whose message the client reads from
    the commit call's one `ERROR:` line (S1's step 3). A base of another
    generation is LR007, whose DETAIL token is `act-generation`, and a
    base past the head is LR004: both are backstops to the client's checks
    at the snapshot (§1.3, "The act");
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
  - **The three conditions,** which Brett accepted ("Accept all three",
    comment 6721583757, row 22): `lane-start` re-bases with
    `act-begin --rebase`, then re-reads the row at its second `binding`
    read after a W8 wait, and refuses on a change
    outside the state cell; the skill's step 4 re-derives the lane's
    identity and refuses if it differs from step 1's; `lane-end`'s gate
    inherits `LANES_ACT` and, being a dry run, writes nothing; a `NOTED`
    line written after the base yields only `sid:`, so it refuses neither
    W13 nor W14. One case each.
  - **Re-basing** (lane 4's section, "The acts and their expectations"):
    `act-begin --rebase` keeps the id and takes a new seq and generation
    from one snapshot, only after a deliberate wait for another place's
    write. The test runs `act-begin`, a rival writes touching one of the
    act's scopes, and the act re-bases with `act-begin --rebase`, which
    prints the same act id with the head seq then and the switch's
    generation. The act's write on that scope commits, since the rival's
    entry is at or before the new base; a second rival entry after the new
    base on the same scope still refuses it, with 2. `act-begin --rebase`
    refuses with 2 where `LANES_ACT` is malformed, where its base is
    `files` on primary, and where its generation is not the switch's.
  - **Before a launch,** `LANES_ACT` is unset (`exec`, `respawn-pane`), so
    the session and its guard do not inherit it: the fake `claude` started by
    `exec`, and the command the fake `tmux` logs for `respawn-pane`, see no
    `LANES_ACT`, in a run where `act-begin` had exported one.
  - **`canon-lane --walk <name>`** (lane 4's section, "Two reads stand for
    more than one name"): on a register whose alias lines lead `a` to `b`
    and `b` to `c`, which has a row, it prints `a`, `b` and `c`, one per
    line, the typed name first, and exits as `canon-lane a` does. For a
    lane with no alias it prints the lane alone, and where `canon-lane`
    refuses, it exits with the same code. An act that decided on
    `canon-lane a` lists `row:` and `alias:` for each of the three, so a
    rival `add-row` for `b` after the base, which changes `canon-lane a`'s
    answer, refuses the act's write with LR009 naming `row:b`.
  - **The form:** on primary, `act-begin` prints 32 lower-case hex digits,
    `@`, the head seq with no leading zero, `:`, and the switch's
    generation as a lower-case uuid, and the shim's log holds its one
    snapshot call. With the switch at `files`, or its snapshot failing, it
    exits 69 and prints nothing on stdout.
  - **The client's checks before the first snapshot,** each an exit 2
    naming the variable, with the shim's log empty and nothing written:
    - `LANES_ACT` malformed: the old form `<id>@<seq>`, an upper-case
      uuid, a seq with a leading zero;
    - `LANES_EXPECT` set with `LANES_ACT` unset or empty;
    - a scope outside the six forms, `*` and `row:*` among them;
    - a base of `files` on primary: an act begun before the flip whose
      write runs after it (the delta's "An act begun off primary whose
      write runs on primary"), the text naming `<id>@files`.
  - **The client's checks at each attempt's snapshot,** each an exit 2
    with no commit call in the shim's log and nothing written:
    - the act's generation is not the switch's: a base taken on A before
      AT-F18's after-loss cutover, whose write runs on B after it (the
      delta's "A base read before an after-loss cutover");
    - the act's base is past `n`: a `LANES_ACT` the test writes with a seq
      above the head, as a base from a lost tail is after a restore (§1.3,
      "The act").
  - **The server's backstops,** with the client's checks bypassed by a
    direct commit call: an act of another generation gets LR007 whose
    DETAIL token is `act-generation`, and a base past the head gets LR004.
    Nothing is written.
  - **A key that is not a scope** (the delta's "A line that derives a key
    that is not well formed"; lane 1's v2.5): `append-line` with a
    row-shaped line whose first backticked token holds a space,
    `` | `x y` | ``, or a no-break space. The commit refuses it with LR010,
    and the writer exits 2 with the server's message naming the path, the
    line and the key, `row:x[U+0020]y` or `row:x[U+00A0]y`. The shim's log
    holds one commit call: the write is never retried, and nothing is
    written.
- **Tests.** In `code/tests/test_sql_register_order.py`:
  - `test_act_begin_prints_its_base_and_touches_no_database_off_primary`;
  - `test_an_act_whose_scope_changed_before_its_commit_writes_nothing[<write>]`,
    on the columns of lane 1's v2.4 (commit K);
  - `test_an_act_whose_scopes_held_commits[<write>]`;
  - `test_an_acts_own_earlier_write_never_refuses_its_later_one`, on the
    columns of lane 1's v2.4 (commit K);
  - `test_a_handoff_request_never_refuses_the_holders_own_handoff`;
  - `test_act_variables_change_nothing_in_files_mode`;
  - `test_each_of_the_three_conditions_holds[lane-start-reread|skill-step-4|lane-end-gate]`;
  - `test_act_begin_rebase_keeps_the_id_and_takes_the_new_base`;
  - `test_act_begin_rebase_refuses_with_2[malformed|files-base|generation-changed]`;
  - `test_lanes_act_is_unset_before_a_launch[exec|respawn-pane]`;
  - `test_canon_lane_walk_prints_each_hop_with_canon_lanes_exit_code[two-hops|no-alias|refused]`;
  - `test_an_act_that_decided_on_canon_lane_is_refused_by_a_row_added_on_its_walk`;
  - `test_act_begin_on_primary_prints_id_seq_and_generation[present|switch-at-files|snapshot-fails]`;
  - `test_the_client_refuses_a_bad_act_with_2_before_any_call[malformed|expect-without-act|bad-scope|files-base]`;
  - `test_the_client_refuses_an_acts_stale_base_with_2_at_the_snapshot[after-loss-cutover|base-past-n]`;
  - `test_the_commits_act_backstops_are_lr007_act_generation_and_lr004[generation|base-past-head]`;
  - `test_a_line_deriving_a_key_that_is_not_a_scope_exits_2_on_lr010_once[space|no-break-space]`.

#### AT-F6. Answer 2's bind, detached

- **Ruling.** Ruling 16's fallback applies (lane 3, comment 6579459518): the
  measurements put the bind on primary at 9.64 s median, a floor, local
  with no TLS (the remote figure is act 3's), against about 3 s of room
  (section 5.1, M9). So answer 2 exits 2, and the bind runs
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
    attempt (1 to 3), its start, its bound, `bound_s`, which `guard-bind`
    computes at the start from `c` and `N_a`: 500 s at `c` = 2 and 530 s at
    `c` = 3 with one address (`LANES_BIND_BOUND_S` for tests), `bounded`
    (`yes` where `timeout`
    exists) and `deadline_epoch` (the start plus `bound_s` plus 5 s),
    and, on primary, `act`. The outer, when it writes an outcome other
    than 0, or the guard at the stale row, copies `write_id`,
    `generation`, `not_after`, `write_lane` and `args_sha256` from this
    session's `start-record` pending record, where that record holds a
    `call` line;
  - the child, `guard-bind`'s outer, runs its body, `guard-bind --body`,
    under one `timeout -k 10 "$(( bound_s - 10 ))"`, 490 s at `c` = 2:
    `lane-start --no-launch --bind`, then
    the `set-row-state` on the lane the window left, then
    `guard_bound_check`'s read. The outer stays outside the bound and
    writes the outcome: `bound`, `bind-failed` with `rc` and `why`, or
    `rc=124` or `137`, or a signal's code, when its bound or a signal
    stops the body, with "what it wrote is not known";
  - lane 4's eleven-row table gives what the guard answers meanwhile. Nine
    rows answer 2: the answer `2` itself, a child live inside its bound, a
    child live past it, after success (once, "MOVED"), after a failure below
    the third attempt (once), after the third failure, stale (its pid
    reused, or dead with no process left in the session it led; once),
    `binding` from another host or container, and an unreadable record.
    The row for another session wanting the same lane answers by that
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
  mode on a bare origin. A stub `lane-start` that sleeps 15 s (60 s in
  case 16, so that it still sleeps past `deadline_epoch`), or exits a
  given code, stands in where the case says so, and so do: a stub
  `lane-start` that writes a `start-record` pending record with a `call`
  line and then sleeps; a `LANES_EDIT` stub that logs and counts each call
  and runs the checkout's `lanes-edit.sh`, or first sleeps on
  `set-row-state`, or, for case 7, answers `guard-bind --body` itself
  with a stub body that ignores `TERM` in its own process and sleeps 60 s;
  and `LANES_BIND_BOUND_S=15`, under which the body runs under
  `timeout -k 10 5` and `deadline_epoch` is the start plus 20 s. The
  bind's own calls go
  through the shim, which can hold `start-record`'s commit on a FIFO, so a
  second prompt arrives while it is in progress. The hook runs under a
  runner that, like Claude Code 2.1.319, sends SIGTERM to the hook's whole
  process group at its timeout and does not wait on pipes a child inherits.
  A fake `tmux` logs its calls.
- **Oracle,** lane 4's twenty-two cases ("Phases, and AT-F6"):
  1. answer `2` exits 2 with "BINDING" within 0.1 s after the guard's
     register read returns, and so before `T0 + 1 s` against the local
     fixture, while the stub `lane-start` sleeps 15 s, and a runner
     reading the hook's pipes sees
     EOF at once. The hook's own process makes no register write: the
     window's rename and the offer file are its only acts. The 0.1 s and
     the 1 s are the design's bounds, not a probe's times, and section 4's
     rule 2 asserts each at twice its value: the exit within 0.2 s of the
     read's end, from the shim's log of the read's end to the runner's log
     of the exit, and before `T0 + 2 s`. Each measured time is recorded
     beside the design's bound with its margin; the one margin measured
     locally is that the hook reaches `guard_answer` at about
     `T0 + 0.43 s` (lane 2, in lane 4's section, "It survives the hook").
     The order is rule 3's, and does not rest on the clock: the runner logs
     the hook's exit and its EOF before the stub logs the end of its sleep.
     Under the default bound, `c` = 2 with one address, the record holds
     `bound_s=500`, `bounded=yes` and a `deadline_epoch` 505 s after
     `started_utc`, "BINDING" says "bounded at 500 s" and to send the work
     again in a minute or so, and the body runs as
     `timeout -k 10 490 … guard-bind --body <uuid>`;
  2. the child survives a group `SIGKILL` and a group `SIGHUP`, and the
     record ends `bound`. Its `ended_utc` less `started_utc` is recorded
     beside `bound_s`, never asserted (lane 4's row 37);
  3. a prompt mid-bind gets "BINDING IN PROGRESS", is not passed, and the
     record is unchanged;
  4. after `bound`, one "MOVED" with the owed notes, the record removed,
     and the next prompt exits 0;
  5. after a stub failure: the why, the window renamed back to `<from>`,
     the offer kept, attempt 2 on the next `2`, and choice 2 withdrawn after
     the third failure;
  6. the outer killed with `-9` while the stub `lane-start` sleeps: its
     pid is dead, but the body, its `timeout` and the stub are still in
     the session it led (`pgrep -s <pid>`), so a prompt, `2` among them,
     gets "BINDING IN PROGRESS", the record unchanged, and no second
     `guard-bind` starts (the scenario "A bind whose outer died"). Once
     the stub has exited and no process of that session is left, the next
     prompt gets "no outcome" once: exit 2, `name-drift`, `rc=unknown`,
     `ended_utc` the guard's time, and the offer kept. The stub made no
     `start-record` pending record, so none of the five values is copied,
     and the next `2` binds with no lookup (the scenario "A bind that
     left no outcome"). An altered `pidstart` reads as dead with no
     session test: "no outcome" at once, while the outer and its session
     still live;
  7. under `LANES_BIND_BOUND_S=15`, the body runs under `timeout -k 10 5`:
     [term], the stub `lane-start` sleeping past the bound, ends `rc=124`
     at `timeout`'s `TERM`; [ignores-term], the stub body that ignores
     `TERM`, ends `rc=137` at its `KILL` 10 s later. Each ends with the
     `why` "stopped by its bound" and "what it wrote is not known"; the
     outer writes the outcome, and no process of the body's group is left.
     The process that ignores `TERM` is the body itself, `timeout`'s own
     child: GNU `timeout` exits 124 once its child has died, and then sends
     no `KILL`, so a grandchild that ignores `TERM`, such as a stub
     `lane-start`, outlives it (lane 2's run on coreutils 9.4, 2026-11-08:
     124 at 1.0 s, the grandchild still alive; section 7). With the stub
     `lane-start` ending 0 at once and a `set-row-state` that sleeps past
     the bound, the record still ends `rc=124`: the bound covers that call
     too;
  8. two sessions answer `2` for one lane on the primary fixture: one
     `start-record` commits, and the other is refused 2 by `bind:<L>`;
  9. the bind is one register commit plus one handoff commit in files mode,
     and one journal entry on primary; a row change injected after
     `act-begin` refuses it with 2 and writes nothing. With the
     `set-row-state` on the lane left, answer 2 journals two entries;
  10. an injected `psql` exit 2 gives `rc=3` with the id: [absent], exit 2
      on every call of the bind's write from its commit call on, its
      deadline's lookup included; [found], the shim's `after` mode on that
      commit call, which commits, then exit 2 on every later call. At the
      next `2` the child's `find_write` lookup runs first. Its tries, here
      and in cases 18 to 20, follow AT-F3's rule, and are asserted as
      AT-F3's are (section 4's rules 2 and 3): they start inside the
      10 s window; a 55P03, a failed connect or a try cut by its timer,
      `T_L`, 9 s at `c` = 2,
      is made again, and LR011 and LR007 are not; where no try takes the
      lock, the record ends `bind-failed`, `rc=3`, again, with the copies
      kept. Absent binds anew under a new id. Found is the bind's "found"
      row, and writes no second `RESUMED`: the child runs `start-record`'s
      pending record's `cmdline` under its `write_id`, generation, lane and
      session, which finds the entry and replays (lane 4's section,
      "The tail, once").
      The shim's log shows `register snapshot --tree <dir> --at <s−1>`;
      the replay reads the `call` line's directory, `snapshot`, `files`
      and `preimages`; its child's `seam.out` says `tail`;
      `lane_state_follow` moves the lane's `lane-state.yaml` `generation`
      once (`code/lanes-edit.sh:13855`); the done mark
      `<state>/register-done/<write_id>` says `done`; and the record ends
      `bound`;
  11. a rerun after a failed stamp push writes no register line and exactly
      one stamp;
  12. a fake `tmux` sees the child's untargeted calls with `TMUX_PANE` set
      and stdin on `/dev/null`;
  13. a stub `lane-start` sees `LANES_NO_FETCH`, `LANES_ACT` and
      `LANES_EXPECT` unset, and `TMUX` and `TMUX_PANE` set; also
      `LANES_WRITE_ID` and `LANE_START_HANDOFF_ANSWER` unset and
      `LANES_GIT_TIMEOUT=45`, as lane 4's table of the child's variables
      has them. On primary, the `LANES_EDIT` stub sees `LANES_ACT` equal
      to the record's `act`, and `LANES_EXPECT="row:<from> bind:<from>"`,
      on the `set-row-state` for the lane left; and, in a run with the
      real `lane-start --no-launch --bind`, as the record's code below runs
      it, it sees `lane-start`'s own calls carry the id of its own
      `act-begin`, never the record's;
  14. the hook string and `GUARD_TIMEOUT` are byte for byte unchanged;
  15. `kill <p>` on the outer mid-bind, while the stub `lane-start` sleeps,
      ends the record with the signal's code, the `why` "stopped by a
      signal" and "what it wrote is not known": the outer's `TERM` trap
      forwards the signal to `timeout`, which passes it to the body's
      group, and the outer writes the outcome;
  16. with `timeout` hidden from `PATH` and `LANES_BIND_BOUND_S=15`, the
      record says `bounded=no`, and the body leads a process group of its
      own, outside the outer's. A prompt after `deadline_epoch`, the start
      plus 20 s, while the 60 s stub `lane-start` still sleeps, gets exit
      2, the record unchanged,
      and the past-the-bound text with its cost: "THE BIND HAS RUN PAST
      ITS 15 s BOUND and is still running (pid `<p>`, started `<utc>`)",
      "this host has no `timeout`, so nothing stops it", what `kill <p>`
      costs, and "Your prompt was NOT sent". Then `kill <p>` stops the
      body's process group, with `KILL` 10 s after `TERM` if the group is
      still there; the outer's trap runs once, and the outer writes the
      outcome. Case (a) is the same row with `bounded=yes`;
  17. a counting `LANES_EDIT` stub sees at most `N_r` = 17 read calls on
      `lane-start --no-launch --bind`'s resume path, each a `lanes-edit.sh`
      process that makes one `register read`, and the test fails where
      `N_r` must grow: `bound_s`'s sum counts `T_r` + `w_s` per read, 6 s
      at `c` = 2 with no service (lane 4's
      section, "The record, and its liveness test");
  18. on the primary fixture, a bind stopped by `kill <p>` while the shim
      holds `start-record`'s commit call on a FIFO, and answered `2` at
      once: the outer copies the five values; the child's lookup starts,
      in the shim's log, no earlier than the record's `ended_utc` plus `C`,
      `N_a`·`c` + 6 s, 8 s at `c` = 2 (section 4's rule 3), and gets no
      LR011; and the bind
      [found] writes no second
      `RESUMED`, where the shim held the call after the real `psql` had
      returned, or [absent] binds anew, where it held it before;
  19. on the primary fixture, with the window's name spelled differently
      from the lane's canonical name, after case 10's [found] `rc=3`: the
      child's lookup is `lanes-index journal --write-id <id> --not-after
      <n> --generation <g> --lane <write_lane> --verb start-record
      --args-sha256 <h>`, with the five values copied from
      `start-record`'s pending record, never derived from `to`, and the
      found entry writes no second `RESUMED`. An LR007 `another-write`,
      raised by `find_write` where the test has changed the record's
      `args_sha256` copy, ends `rc=2` with the server's message and the
      five copies dropped, and the next `2` binds anew. An LR007
      `generation`, raised where it has changed the `generation` copy,
      ends `rc=2` with a plain rerun's refusal, its effect check
      `lanes-edit.sh binding <to>`, and the copies kept; a `2` after that
      pending record is replaced with `LANES_WRITE_ID=new` binds anew (the
      replacement is AT-F18's one test of it);
  20. on the primary fixture, the outer killed with `-9` while a stub
      `lane-start` has written a `start-record` pending record of this
      session with a `call` line: once no process of the outer's session
      is left, the stale row copies the five values from that record,
      `not_after` the largest among its `call` lines, and the next `2`
      makes the lookup, in the shim's log, before any `start-record` call;
  21. on the primary fixture behind the delayed loopback lane 3 picks for
      lane 2's question 3 (#165 6722518182; section 7's point 23), with
      75 ms added each way and TLS on (AT-D9c (d)'s link), and
      `connect_timeout=3` with one address in the configuration:
      `guard-bind` records `bound_s=530`, the bind ends `bound`, which its
      `timeout` allows only inside it, with its `ended_utc` less
      `started_utc` recorded beside the 530 s, and the shim's log shows no
      step at its timer. The guard's register read is the hook's last
      database call in the shim's log, so answer 2 adds none, and the
      exit's lag after the read is asserted and recorded as case 1's is;
  22. `bound_s` as `guard-bind` computes it from the configuration, with a
      stub body that exits 0 (FM): 500 at `c` = 2 and 530 at `c` = 3 with
      one address, with the service's socket configured or not; 550 and
      610 with `register_addresses=2`, and 560 and 620 with the socket;
      still 500 at `c` = 2 with `register_rtt_ms=120`, and 530 at `c` = 3
      with `register_rtt_ms=270`, where SVC-6 raises the service's timer.
      Each record's `deadline_epoch` is its start plus `bound_s` plus 5 s,
      and its body runs under `timeout -k 10` at `bound_s` − 10.
      `LANES_BIND_BOUND_S` overrides the sum.
- **Four rows of lane 4's guard table that none of the twenty-two cases
  reaches.** Each is a case of its own, with the oracle the table gives for
  its row (lane 4's section, "What the guard answers"). Cases (a) to (c)
  write the offer file's record directly, with a sleeping process standing in
  for the child where one is needed; case (d) runs two real binds:
  - **(a) A child live past the bound that its `timeout` has not ended.**
    The record says `bounded=yes` and `bound_s=500`, its `deadline_epoch`,
    `started_utc` plus 505 s, is already past, and its pid and `pidstart`
    are those of a live process on this host, as for a child stuck on the
    disk or the network. The answer is exit 2, the record unchanged, and
    the text "THE BIND HAS RUN PAST ITS 500 s BOUND and is still running
    (pid `<p>`, started `<utc>`)", then "its `timeout` has not ended it;
    it is likely stuck on the disk or the network", and what `kill <p>`
    costs, with the child's pid. Case 16 is the `bounded=no` half, and
    case 7 the one where `timeout` ends the body;
  - **(b) `binding` from another host or container.** The record's `host` or
    `container` differs from the test's (`LANES_WORKSTATION`,
    `LANES_CONTAINER`), with a pid that is dead on this host, so a guard
    that judged the pid would call it stale. Until the record's deadline plus
    60 s has passed, the answer is exit 2, the record unchanged, and the text
    "A BIND OF THIS TRANSCRIPT IS RUNNING ON `<host>/<container>` … Your
    prompt was NOT sent". Once the deadline plus 60 s has passed, it is
    answered as stale but with no copies: exit 2 once, `name-drift`,
    `rc=unknown`, and none of the five values copied, since the pending
    record is that host's. With `bounded=no` in the record, the text adds
    that the child there may still be running, and that on primary
    `bind:<to>` refuses whichever bind commits second. The test runs once
    with another host and once with another container;
  - **(c) An unreadable record.** An offer file the guard cannot read, such
    as a truncated one. The answer is exit 2 with today's text (lane 4's
    table cites `:11097–11101`), and the record dropped, so the next prompt
    meets the ordinary table;
  - **(d) Another session wanting the same lane, in files mode.** Two
    sessions answer `2` for one lane on the files-mode fixture, the bare
    origin (case 8 is the primary half). Two binds that both read the lane
    free both land, as files mode does: each reads `binding` once, and the
    two, answered back to back, read it before either writes. `lane-start`
    reads `binding` again only after a handoff wait or a Claude resolution
    (`:2611` at `2a057a6d`, set at `:2509–2510` and `:2606`), and neither
    runs in the bind: with `LANE_START_HANDOFF_ANSWER` unset, a binding
    elsewhere is a refusal, not a wait, and the resolution never runs under
    `--no-launch` (`:689`). So the race is one read wide, and the lane's
    reservation is #84's (`:2582–2588`). Both records end `bound`, and the
    lane's log holds two `RESUMED` lines, one for each bind; the test
    asserts both and records each record's code and notes.
- **The record's code,** under lane 4's corrected table (comment
  6709560550). The child runs the real `lane-start --no-launch --bind`
  through AT-11's stamp shim, for a resume and for a new lane that the offer
  names. On any non-zero code of `start-record`, the record ends
  `bind-failed` with `rc` that code, and nothing is bound: the register
  holds none of the bind's lines, and the child writes no stamp. The codes
  are 69, 75, 3 and 2, the design's table giving `rc=2` for any 2 of
  `start-record`: for a resume, case 9's row change injected after
  `act-begin`, and for a new lane, a rival's row for that lane committed
  after its `act-begin` (`row:<L>`). On a 3 the pending record is kept
  with its `call`
  lines, and the outer copies `write_id`, `generation`, `not_after`,
  `write_lane` and `args_sha256` from it, for the next `2`'s lookup (cases
  10 and 19); on 69, 75 and 2 it copies none, since that record is gone or
  holds no `call` line. The 75 case's lookup answers at about its
  `not_after`, 10 s after the last snapshot; the 3 case's tries run on to
  the end of the 10 s window that opens 8 s after it, about 18 s after the
  snapshot (lane 4's section, "The in-doubt rule (F3, D8)").
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
- **Options.** Lane 4's row 40, which Brett accepted as a gap (lane 3,
  comment 6721583757): on a host without `setsid`,
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
- **The measurement, M9** (section 5.1), recorded and never asserted. The
  real `lane-start --no-launch --bind` and the guard's `set-row-state` on
  the lane the window left, with no stub, each timed from exec to exit
  through the guard's own detached path, warm and cold, in 20 runs, each on a
  fresh estate with a local bare origin (section 5.3). It runs in files mode,
  as today's bind is, and on `mode=primary`, where the bind is
  `start-record`, once that exists in both modes. The child's `ended_utc` less
  `started_utc` is recorded beside `bound_s`, as case 2's is.
  Correctness alongside, as section 5.3 asks: every timed run's writes
  committed, one register commit and push for the bind in files mode, and on
  primary one journal entry for the bind and a second for `set-row-state`; a
  run that wrote wrongly fails, whatever its time.
- **Tests.** In `code/tests/test_sql_register_guard.py`:
  - `test_answer_2_exits_2_binding_before_t0_plus_1_s_and_the_runner_sees_eof`;
  - `test_the_bind_child_survives_a_group_kill_and_hangup_and_ends_bound`;
  - `test_a_prompt_mid_bind_is_not_passed_and_leaves_the_record`;
  - `test_after_bound_one_moved_then_the_next_prompt_passes`;
  - `test_a_failed_bind_renames_back_keeps_the_offer_and_allows_three_attempts`;
  - `test_a_dead_bind_outer_answers_in_progress_while_its_session_lives_then_no_outcome`;
  - `test_a_reused_bind_pid_gives_no_outcome_with_no_session_test`;
  - `test_a_bind_body_past_its_bound_ends_with_the_bounds_code_and_leaves_no_process[term|ignores-term|slow-set-row-state]`;
  - `test_a_live_bind_past_its_bound_that_timeout_has_not_ended_answers_2_and_names_the_pid_to_kill`;
  - `test_a_binding_from_another_host_or_container_is_not_judged_by_its_pid_until_the_deadline_plus_60_s[host|container]`;
  - `test_an_unreadable_bind_record_is_dropped_with_exit_2`;
  - `test_two_sessions_binding_one_lane_one_start_record_commits`;
  - `test_two_sessions_binding_one_lane_in_files_mode_both_binds_land`;
  - `test_the_bind_is_one_register_write_and_refuses_a_changed_row`;
  - `test_a_bind_in_doubt_looks_its_write_up_before_a_rerun[found|absent]`;
  - `test_the_bind_child_records_bind_failed_with_the_code_and_binds_nothing[<kind>-<code>]`,
    where `<kind>` is `resume` or `new`, and `<code>` is 2, 75, 3 or 69 for
    each;
  - `test_a_rerun_after_a_failed_stamp_push_writes_one_stamp`;
  - `test_the_bind_child_reaches_tmux_through_tmux_pane`;
  - `test_the_bind_child_environment_is_as_lane_4_lists_it`;
  - `test_the_hook_string_and_guard_timeout_are_unchanged`;
  - `test_kill_of_the_bind_outer_ends_the_record_with_the_signals_code`;
  - `test_a_bind_with_no_timeout_says_bounded_no_and_kill_stops_its_group_once`;
  - `test_the_bind_resume_path_makes_at_most_n_r_read_calls`;
  - `test_a_bind_killed_mid_commit_waits_for_ended_utc_plus_c_before_its_lookup[found|absent]`;
  - `test_the_bind_childs_lookup_sends_the_five_copied_values[found|another-write|generation]`;
  - `test_the_stale_row_copies_a_dead_outers_pending_call_and_the_next_2_looks_it_up`;
  - `test_guard_bind_computes_bound_s_from_c_n_a_and_the_service[<configuration>]`
    (case 22);
  - `test_a_bind_over_a_delayed_tls_link_at_c_3_ends_bound_inside_530_s`
    (case 21, once lane 3 picks the loopback);
  - `test_answer_2_survives_a_group_sigterm_at_the_hooks_timeout` (row 34);
  - `test_a_failed_bind_can_be_answered_again` (row 35);

  and, in `code/tests/test_sql_register_measure.py`, M9's measurement:
  - `test_the_bind_and_the_guards_set_row_state_are_timed_in_both_modes[files|primary]`.

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
    - the shim logs the client timer the direct read was given, which is
      at most `3.65 s` less the time the guard had spent before the call,
      measured from `T0`: the cut, `T0 + 3.65 s`, is the design's bound,
      and section 4's rule 2 asserts the call's start in the shim's log
      plus that timer at twice it, `T0 + 7.3 s`, which the 4.5 s end to
      end below (rule 1) already holds, and records the sum beside
      `T0 + 3.65 s` with its margin;
    - the order, from the logs (section 4's rule 3): the shim's log shows
      the direct read ended by its timer, and the `git` wrapper's log shows
      the mirror path's first `git` call after that end. The start line,
      `T0 + 3.75 s`, is the design's bound (SVC-8): rule 2 would assert it
      at twice its value, which the 4.5 s end to end below (rule 1) already
      holds, and the test records the time from `T0` to that call beside
      the 3.75 s with its margin;
    - end to end, the hook answers from the mirror in under 4.5 s, never
      killed (rule 1).
  - The declaration is read once, at the prefetch, which a behaviour shows:
    the shim holds the direct read on a FIFO, the test removes
    `register-outage`, then releases the read to fail; the guard still
    answers from the mirror, with the declared line.
  - **The floor under a declaration** (SVC-8, "How the client knows
    `T0`"): the test runs the guard's client with the pid of a stand-in
    process it started earlier, in place of the hook's, so that the client
    reads an age of about 3.3 s, 3.7 s and 2.0 s. At 3.3 s, less than
    0.5 s before the 3.65 s cap, and at 3.7 s, past it, the client starts
    no direct read and exits as "too late to verify", and the guard
    refuses 2: the shim's log holds no direct read, and the `git`
    wrapper's log no mirror call. At 2.0 s the read starts, with a timer
    of at most 1.65 s, and the mirror answers as above: a value the client
    sets from the stand-in's age, which can only have grown, so it is
    asserted as stated and is not a time the runner measures. The control is a
    fourth run, at an age of about 3.3 s at the client's check with no
    declaration (the stand-in's launch age plus the client's own start): the
    cap is then `T0 + 3.75 s` and no floor applies, so the read starts and
    the guard answers. AT-D20 holds no case of the floor.
- **Tests.** In `code/tests/test_sql_register_outage.py`:
  - `test_a_declared_outage_lets_the_guard_read_the_mirror_with_service_and_database_down`;
  - `test_with_no_declaration_the_guard_exits_2_with_service_and_database_down`;
  - `test_under_a_declaration_a_silent_database_is_capped_and_the_mirror_answers_inside_the_budget`;
  - `test_the_declaration_is_read_once_at_the_prefetch`;
  - `test_under_a_declaration_a_direct_read_left_under_0_5_s_does_not_start[floor|clamp|room]`;
  - `test_without_a_declaration_the_same_age_still_starts_the_direct_read`.

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
    and 6711028142). The query's client timer, 2 s, kills the stand-in,
    which would otherwise sleep 60 s: its process is gone once the verb
    returns, and the query ends under 4 s, twice D9's 2 s timer, the
    design's bound (section 4's rule 2); the query's measured end is
    recorded beside the 2 s with its margin. The verb then runs in files
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
  line, its UTCs, copies of the owner's pid and start time, host,
  container, attempts, the `call` lines and `tail`. A write is in doubt
  while its record holds a `call` line, one per commit call that may have
  committed; no other key says so.
  - **The owner** is the run in the highest-numbered claim file beside the
    record, `<d>.take.<k>`, created with noclobber. Its liveness is tested
    on that claim's pid and start time, never on the record's `pid`. A run
    that finds that owner dead, or its pid reused, creates
    `<d>.take.<k+1>`, and only the run whose create succeeds owns the
    record.
  - **A plain rerun** with the same digest reuses the id, says so on
    stderr, and takes the record over, with its `call` lines and its doubt,
    but for three cases:
    - a record of this generation past its maximum age, 15 minutes from
      `updated_utc` (`LANES_REGISTER_PENDING_MAX_AGE_S` for tests), is not
      reused: the run warns, prints the lookup, and writes anew;
    - a record of another generation, whatever its age, is neither reused
      nor replaced. The rerun refuses with 2 at its first snapshot, sending
      nothing, and names the record's write id, its `created_utc`, both
      generations, the effect check and the after-COMMIT steps that may be
      owed. Only `LANES_WRITE_ID=new` mints a new id and replaces it;
    - with a live owner it waits while that owner lives, up to D7's
      deadline counted from its own start. An owner still live then makes
      it exit 3, sending nothing, naming the record's write id and the
      owner's pid, and leaving the record as it is.
  - **The snapshot settles a write id** before any commit call, in lane
    1's order: an id of another generation than the switch's is 2,
    whatever the switch reads; then an id the journal holds with this
    run's lane, verb and arguments is 0, and the verb's tail runs once
    (AT-F34); one it holds for another write is 2, naming the id and what
    differs. The commit's LR007 and LR006 are the same checks under the
    head row's lock, reached only where the journal changes between the
    snapshot and the commit (AT-LR006's interleavings). LR007 is accepted
    by lane 3 (comment 6579459518).
- **Fixture.** Test D8's records: one older than the maximum age, from a
  write that committed; one whose highest claim's pid is live; one whose
  highest claim's pid is live but whose start time differs (a reused
  pid); one whose `pid` field names a live process while its highest
  claim's owner is dead, and the converse (V3's race of a run that reads
  the record's old pid); a `LANES_WRITE_ID` naming an entry of other
  arguments; one naming an entry of another verb; a record of another
  generation than the switch's, once fresh and once with `updated_utc`
  past the maximum age; a printed rerun whose `LANES_WRITE_GENERATION` is
  not the switch's; and two runs that differ only in `LANES_SESSION`.
  `LANES_REGISTER_PENDING_MAX_AGE_S=2` for the age cases. Three more, whose
  claim files the test writes, each claim's owner a live stand-in where it
  is on this host: a record whose highest claim names another host, or
  another container, with a pid dead on this host, once with the claim's
  UTC now, once with its UTC 121 s back and the record's `updated_utc`
  now, and once with both 121 s back; a record whose highest claim's owner
  is live, met by the printed rerun under `LANES_WRITE_ID`; and a claim
  `<d>.take.0` with no record beside it.
- **Oracle.**
  - The old record is not reused: the identical command warns, prints the
    lookup, mints a new id and writes.
  - The second of two concurrent identical runs waits while the first's
    owner, the pid in the highest claim, is live. Where the first ends
    inside the deadline, its record is gone and the second writes under
    its own id: two entries. Where the owner is still live at the
    deadline, counted from the second's own start
    (`LANES_REGISTER_DEADLINE_S=1`), the second exits 3, never 75, with
    "an identical write is still running in pid `<p>` (write `<id>`,
    started `<utc>`)", the lookup and the printed rerun. The shim's log
    holds no commit call of the second's, and the record and its claims
    are as the first left them (commit O's "A rerun that finds the same
    write still running").
  - A record whose highest claim's pid was reused reads as not live: the
    run creates `<d>.take.<k+1>` and takes the record over.
  - Liveness is the highest claim's, never the record's `pid`: the record
    whose `pid` is live but whose highest claim's owner is dead is taken
    over, and the converse is waited for.
  - Two runs differing only in `LANES_SESSION` have different digests, and
    neither takes the other's record.
  - The other-arguments and other-verb runs each exit 2 at the snapshot,
    naming the id and what differs: the shim's log holds the snapshot and
    no commit call, and nothing is written.
  - The printed rerun of another generation exits 2 at its snapshot,
    whatever the switch reads, with no commit call.
  - A plain rerun that meets the record of another generation, fresh or
    past the maximum age, refuses with 2 at its first snapshot, with no
    commit call. Its text names the write id, `created_utc` and both
    generations; the effect check, either
    `lanes-edit.sh history <lane> --since <created_utc>` or
    `lanes-edit.sh register-row <lane>`; the steps that may be owed for
    the record's verb; and `LANES_WRITE_ID=new`. The record is left as it
    is, and `lanes-index status` lists it. A second plain rerun refuses the
    same way. The replacement itself, the same command with
    `LANES_WRITE_ID=new` minting a new id, replacing the record and writing
    once, is AT-F18's one test of it,
    `test_lanes_write_id_new_on_the_replacement_replaces_the_old_record_and_writes_once`;
    AT-F6's case 19 uses it.
  - A plain rerun, or a `LANES_WRITE_ID` rerun, of a write already in the
    journal with the same arguments exits 0 at the snapshot, with no
    commit call, and runs the tail as AT-F34 says.
  - **A claim from another host or container** counts as live for 120 s,
    the seam's cap, from the later of its own UTC and the record's
    `updated_utc`, since its pid cannot be tested here. With the claim's
    UTC now, and with it 121 s back but `updated_utc` now, a plain rerun
    waits, and at the deadline (`LANES_REGISTER_DEADLINE_S=1`) exits 3,
    sending nothing, naming the claim's pid. With both 121 s back, it
    creates `<d>.take.<k+1>` and takes the record over. A client that
    judged the other host's pid here would take it over in every case.
  - **A run under `LANES_WRITE_ID`** waits for a live owner as a plain
    rerun does, since two live runs must never share an id: with the owner
    live at the deadline (`LANES_REGISTER_DEADLINE_S=1`) it exits 3, with
    no commit call in the shim's log and the record and its claims
    unchanged; with the owner ended inside the deadline it goes on, its one
    commit call in the shim's log after the owner's end (section 4's rule
    3), and the write commits once under that id.
  - **A claim with no record beside it** is a record being made, and its
    owner is waited for in the same way: an identical run, with the owner
    live at the deadline, exits 3 with no commit call and creates no claim
    of its own, and its text is recorded, since the design words that text
    only for a record's owner (section 7); with the owner ended inside the
    deadline, it creates `<d>.take.1`, writes, and the write commits once.
  - The record's mode is 0600, its directory's 0700, and its name equals
    the entry's `args_sha256`.
- **Tests.** In `code/tests/test_sql_register_in_doubt.py`:
  - `test_an_expired_pending_record_is_not_reused`;
  - `test_a_live_pending_record_is_waited_for_not_shared[ends-in-time|live-at-deadline]`;
  - `test_a_claim_from_another_host_or_container_counts_as_live_for_120_s[host-now|container-now|claim-old-record-fresh|both-old]`;
  - `test_a_lanes_write_id_run_waits_for_a_live_owner[ends-in-time|live-at-deadline]`;
  - `test_a_claim_with_no_record_beside_it_is_waited_for[ends-in-time|live-at-deadline]`;
  - `test_a_reused_pid_in_a_pending_record_reads_as_dead`;
  - `test_liveness_is_the_highest_claims_never_the_records_pid[record-pid-live|record-pid-dead]`;
  - `test_the_pending_digest_covers_the_session_and_equals_args_sha256`;
  - `test_a_write_id_of_other_arguments_verb_or_generation_is_refused_2_at_the_snapshot[arguments|verb|generation]`;
  - `test_a_record_of_another_generation_is_refused_2_until_lanes_write_id_new[fresh|past-max-age]`;
  - `test_a_write_id_already_journaled_for_the_same_arguments_exits_0_at_the_snapshot`.

#### AT-F11. A guard reading a stale mirror

- **Ruling** (D20, SVC-12, §2.5): `outage declare` records `mirror-seq=<m>`,
  read from `origin/main` after a fetch, or named by `--mirror-seq <m>` where
  the host cannot fetch; with neither, it refuses with 2. The guard answers
  from the mirror only when `origin/main`, as last fetched, carries a
  `Register-Seq` of at least `m`. A rollback's start writes a declaration
  of its own, marked as from a rollback, recording `k`, the head its
  transaction found, and that declaration counts only where the guard's
  own database read finds the switch at `files` (SVC-12's condition 2).
- **Fixture.** Test D20's declared outage at `m`, with the guard's checkout
  last fetched at `m − 1`, and then at `m`. A host whose fetch fails, for the
  declaration's own cases. A rollback's declaration at `k`, with the
  database up and its switch at `files`, and the guard's checkout last
  fetched at `k − 1`, and then at `k`.
  - **The measurement's checkout** (M11, section 5.1). The history that
    section 5.3 builds in CI, since a CI checkout has none: at least 30,000
    first-parent commits made by `git fast-import`, with a commit-graph from
    `git commit-graph write --reachable`, and `Register-Seq` trailers on its
    render commits, the newest within the first 200. Its build time is
    recorded beside the figure. On the bench, the 2026-11-02 checkout that
    section 5.3 pins by path and sha256 stands in its place.
- **Oracle.**
  - At `m − 1` the guard exits 2 with "the mirror here is at seq `m − 1`,
    behind the declared outage's seq `m`; fetch it once origin carries seq
    `m`, or wait for the outage to end" (SVC-12, lane 5's text). At `m` it
    answers from the mirror.
  - **A ref behind a rollback's head** (the delta's scenario of that
    name): under the rollback's declaration, with the guard's read finding
    the switch at `files`, the guard last fetched at `k − 1` exits 2 with
    the same text for seqs `k − 1` and `k`, and does not answer from the
    mirror. At `k` it answers from the mirror, labelled `read: mirror`.
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
  - **M11** (section 5.1), recorded and never asserted. On the measurement's
    checkout the guard's one bounded `git log` is timed beside the unbounded
    form and the `--grep` form, 30 warm runs and the first (section 5.3). The
    three forms are shown to find the same `Register-Seq`, and the bounded
    form is the one the guard runs.
  - With the fetch failing, `outage declare --mirror-seq <m>` writes the
    field, and `outage declare` with neither exits 2 and writes no file.
- **Tests.** In `code/tests/test_sql_register_outage.py`:
  - `test_the_guard_refuses_a_mirror_older_than_the_declared_seq`;
  - `test_the_guard_refuses_2_when_no_commit_within_the_bound_carries_register_seq[depth-200|depth-250]`;
  - `test_the_register_seq_lookup_finds_the_trailer_at_depths_0_and_199`;
  - `test_outage_declare_takes_mirror_seq_from_a_fetch_or_the_flag_and_refuses_with_neither`;
  - `test_the_guard_refuses_a_ref_behind_a_rollbacks_head`;
  - `test_the_register_seq_lookup_is_timed_bounded_beside_the_unbounded_and_grep_forms`
    (M11).

#### AT-F13. `LANDING EXPIRED`'s verb, `append-line --landing-expired`, and a take with no UTC

- **Ruling** (F13, Brett's "Adopt, on my word (Recommended)", #165
  6721583757; U3 and U7, lane 3's ruling, #165 6722541787; lane 4's
  section, "`LANDING EXPIRED`'s verb"). On primary a `LANDING EXPIRED` line
  is written only by `append-line --landing-expired <holder> --pr <n>
  --repo <repo>`, with Brett's word as `--word`, `--word-where` and
  `--word-utc` (the delta's "A stale `LANDING` is released by
  `append-line --landing-expired`, on Brett's word"):
  - the form copies the holder, the PR, the repository's token and the UTC
    from the holder's open `LANDING` in the file at `n`, byte for byte, the
    newest post where there are several;
  - it refuses with 2, sending nothing, without the word, off primary, and
    where that `LANDING` is not open or is 30 minutes old or less by the
    snapshot's clock;
  - on primary `append-line` refuses, before the lock, the kind typed by
    hand, and a take, a `HOLD` or a `LANDING`, whose UTC it cannot read
    (U3);
  - F13's check in the commit decides what the line releases; its LR005
    exits 2 with the server's message and its DETAIL token, and a caller
    that branches reads the token, never the message (U7);
  - on primary `who --landing` no longer lists a released `LANDING`,
    through `RULE6_PRIMARY`, `rule6_primary_on` and `RULE6_EXPIRED_AWK`;
    off primary `RULE6_AWK` is byte-identical to today's.

  Cases (a) to (l) are the ones lane 4 wrote for this plan (lane 4's
  `LANDING EXPIRED` texts v2.1, #165 6722891546), and (m) is the token's.
  The engine's half of F13 and U3 is AT-LR5's.
- **Phase and kind.** W, with (h)'s rollback in G. FP for (a) to (g) and
  (k) to (m), with the fake `psql` and a fake `register read` prefetch; RE
  for (h) to (j), on the session cluster and a bare origin.
- **Fixture.**
  - A workstation configured `mode=primary`, with the flipped build (open
    point 3); for (f) and (l), the same in files mode, in `index` mode
    (`--index`), unconfigured, and with the mode query made to fall to
    files mode (F8).
  - The fake's snapshot answers a register at `n` whose `lanes/LANES.md`
    holds the holder's `LANDING` lines, with the snapshot's database clock
    set by the test, far from `date`, so the age the form reads is the
    snapshot's (D13). `lanes/repos.tsv` maps `r9` to `opensoft/r9`.
  - For (h) to (j): `tmpl_imported` brought to `primary` with the marker
    `present`, and lane B's `LANDING` composed by `append-line` with a UTC
    31 minutes or more back, from which Rule 6's 30 minutes run (U2; S6,
    "UTC").
- **Oracle,** each case on its own estate:
  - **(a) Compose.** The exact line of lane 4's section is sent: `lane
    <writer>` first, as `canon_lane` gives `LANES_LANE`; the session; the
    D13 UTC; `for lane <holder>`; Brett's quote last. The commit message is
    `LANES(<writer>@<ws>): append line — …`. The holder, the PR, the
    repository token and the UTC are copied byte for byte, also where
    `--repo` is a `repos.tsv` alias, and where the `LANDING`'s lane is an
    old name or in another case. A writer who is also the holder is
    accepted.
  - **(b) The word, and a take with no UTC.** Each of these exits 2, the
    lock not taken and no `psql` call: any of the three word arguments
    missing or empty; `--word-utc` not a UTC; a newline or a CR in any
    argument; `LANES_LANE` or `LANES_SESSION` unset. On primary,
    `append-line` with a text the one classifier reads as a `HOLD` with no
    UTC exits 2 before the lock, names the rule, and writes nothing; so do a
    `LANDING` with no UTC, and a take whose UTC-shaped stamp does not parse,
    `2026-13-45T99:99Z`. A `HOLD (refresh)` text with no UTC is not refused
    by this rule, and is written. Off primary the same texts are written as
    today.
  - **(c) The kind typed by hand, on primary.** `LANDING EXPIRED — `,
    `LANDING EXPIRED - ` and their tab forms each exit 2 with no `psql`
    call, and stderr names `append-line --landing-expired`. The rule does
    not refuse `LANDING — `, `LANDED — ` or `LANDING EXPIREDX — `. The
    client's pattern and the grown `REGISTER_KIND_RE` agree on every row of
    the case table.
  - **(d) Nothing stale at the snapshot.** These exit 2 with no commit
    call: no `LANDING`; one closed by `LANDED`; one already released by an
    earlier `LANDING EXPIRED` (the attempt's child has the flag); the
    holder's `LANDING` for another PR or repository; ages of 29 and 30
    minutes. At 31 minutes the line is composed. A `LANDING` posted again,
    40 and 10 minutes before: the form takes the newest post, its pre-check
    exits 2 since that post is not past 30 minutes, no commit call is made,
    and nothing is written. The age is the snapshot's time less the
    `LANDING`'s UTC, with the snapshot's clock far from `date`.
    `LANES_LANDING_MINUTES=5` changes nothing.
  - **(e) What it prints.** Success prints one line, `register seq <s>:
    released lane <holder>'s LANDING of <UTC> (PR #<n> into <repo> main),
    <m> min past Rule 6's 30`, and exits 0. A fake LR005 exits 2, and
    stderr has LR005, its reason and that nothing was written. The fake
    LR005 carries v3.1's message and the DETAIL token `expired-young`, and
    stderr prints the token. A fake in-doubt call exits 3 and prints the
    rerun once; the rerun with the same arguments matches the pending
    record's digest.
  - **(f) Files mode.** The form exits 2 with "the release of a stale
    `LANDING` exists only on `mode=primary`; nothing was written", and so
    it does after the mode query's fall to files mode. The register file,
    `git log` and the output of `who --landing` are byte-identical before
    and after. Plain `append-line "LANDING EXPIRED — …"` is written as
    today.
  - **(g) Parse.** Brett's quote carries `lane x`, `PR #9`, `into a/b
    main`, a UTC and `session s@w`. `parse_register_line` still reads the
    writer, the PR, the repository, the UTC and the session from before the
    word clause, and `for_lane` is the holder.
  - **(h) RE, end to end.** Lane A's form commits over lane B's `LANDING`,
    dated 31 minutes or more back. `main_slots` no longer shows lane B's
    `LANDING` as a take for that `repo_key`, and, no other take on it being
    open, a later `LANDING` by lane C is accepted, not refused by D18. On
    primary `who --landing` no longer lists lane B's `LANDING`. After a
    rollback to files, it lists it again, marked STALE. AT-LR5's engine
    cases run beside it.
  - **(i) RE, the race.** Lane B's `LANDED` commits between the form's
    snapshot and its commit. The commit fails with LR001, the retry exits 2
    with "no open `LANDING`", and nothing is written.
  - **(j) RE, D18's agreement tests on imported history.** The primary
    program, `RULE6_AWK` with `RULE6_EXPIRED_AWK`, and `main_slots` show no
    difference on each of three histories: today's, with no `LANDING
    EXPIRED`; a re-import after a rollback that holds a primary-written
    `LANDING EXPIRED`; and a `LANDING EXPIRED` typed in files mode that
    meets F13's condition by its own UTC and its `LANDING`'s (the holder,
    the PR and `repo_key` equal, more than 30 minutes apart, no close
    between) and carries the word clause. Each of these is a finding, and
    fails the test: a typed one written 10 minutes after its `LANDING`; one
    with no word clause; one naming another PR; one whose holder is in
    another case, which `main_slots` closes and the awk does not. One with
    no `for lane` imports as a line of no kind (§1.2, "A `LANDING EXPIRED`
    line's fields").
  - **(k) `who --landing` on primary.** The fake is a `register read`
    prefetch whose text at `n` holds the lines. After a `LANDING EXPIRED`,
    the holder's `LANDING` is not listed, and `none open — …` is printed
    where it was the only one. Another lane's `LANDING` on that repository
    is still listed. A `LANDING EXPIRED` naming another PR, another
    repository, or the holder in another case closes nothing. The holder's
    later `LANDING` on that PR and repository is listed again. Brett's
    quote naming another lane, PR or repository changes nothing. No empty
    row reaches `who_landing`.
  - **(l) Off primary, and the flag.** The same register text, read in
    files mode, in `index` mode, on an unconfigured workstation and after
    F8's fall to files mode, lists the holder's `LANDING`, marked STALE,
    byte-identical to today's output. In each, `RULE6_PRIMARY` is empty and
    the sha256 of `RULE6_AWK` equals the pin's. A caller's environment
    carrying `RULE6_PRIMARY=1`, `RULE6_AWK` or `RULE6_EXPIRED_AWK` changes
    nothing. On primary `RULE6_AWK` is exactly the pin plus the branch,
    appended once. A source check asserts that `rule6_primary_on` is called
    only at the dispatcher's primary answer and at the honoured
    `LANES_REGISTER_SCRATCH`, that the pinned `who_landing` line is
    unchanged, and that the program runs on every awk CI runs, including
    under `tests/test_lane_helpers.sh`'s proxy.
  - **(m) The token decides, never the message** (U7; the outcomes table's
    LR005 row). The fake answers the commit call with exit 3,
    `ERROR:  LR005: <message>` and `DETAIL:  <token>`:
    - for each of `expired-none`, `expired-closed`, `expired-young` and
      `take-no-utc`, with lane 1's v3.1 message for that rule, the writer
      exits 2, and stderr carries the server's message, which names the
      rule, and the token, and says that nothing was written;
    - for `one-live-claim`, `claim-spellings` and `main-held`, it exits 2
      as AT-4's losers do, with the holder or the earlier line;
    - with F13's message under `main-held`, stderr carries no F13 token,
      and with D18's message under `expired-young`, it carries the server's
      message and `expired-young`: the writer follows the token.
- **Tests.** In `code/tests/test_sql_register_landing_expired.py`:
  - `test_the_landing_expired_form_composes_its_line_from_the_open_landing[<case>]`
    (a);
  - `test_the_landing_expired_form_refuses_with_2_before_the_lock_without_the_word[<argument>]`
    (b);
  - `test_on_primary_a_take_with_no_readable_utc_is_refused_before_the_lock_and_written_off_primary[hold|landing|bad-stamp|hold-refresh|off-primary]`
    (b, U3);
  - `test_on_primary_the_landing_expired_kind_typed_by_hand_is_refused_naming_the_form[<text>]`
    (c);
  - `test_the_landing_expired_form_sends_nothing_where_no_landing_is_stale_at_the_snapshot[<case>]`
    (d), its cases among them `posted-again-40-and-10`;
  - `test_the_landing_expired_form_prints_its_seq_once_and_an_lr005_with_its_detail_token[success|lr005-expired-young|in-doubt]`
    (e);
  - `test_in_files_mode_the_landing_expired_form_exits_2_and_changes_nothing[files|fall-to-files]`
    (f);
  - `test_parse_register_line_reads_the_landing_expired_fields_before_the_word_clause`
    (g);
  - `test_a_landing_expired_on_primary_frees_main_and_who_landing_until_a_rollback_to_files`
    (h);
  - `test_a_landed_between_the_forms_snapshot_and_commit_leaves_the_retry_nothing_to_release`
    (i);
  - `test_rule6_with_the_expired_branch_agrees_with_main_slots_on_imported_history[<history>]`
    (j);
  - `test_who_landing_on_primary_drops_only_the_released_landing[<case>]`
    (k);
  - `test_off_primary_rule6_awk_is_the_pin_and_the_flag_cannot_be_inherited[<mode>]`
    (l);
  - `test_rule6_primary_on_is_called_only_where_lane_4_places_it_and_who_landing_stays_pinned`
    (l);
  - `test_the_writer_reads_lr005s_reason_from_its_detail_token_never_its_message[expired-none|expired-closed|expired-young|take-no-utc|one-live-claim|claim-spellings|main-held|swapped-message]`
    (m).
- **Run time.** The FP cases are about 70 runs at 0.2–0.4 s, so 15–30 s;
  (h) with its rollback about 5 s, (i) about 2 s, and (j)'s seven histories
  about 10 s. That is 30–50 s.
- **Flakiness.** Every age is read from a clock the test sets or reads,
  the snapshot's or the database's, never `date`. The race in (i) is
  staged by the shim's `hold`, not by timing.

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
  comment; and a verb arm the test copy makes reach the seam twice. The
  fakes and test 5's `git` wrapper log their environment.
  `LANES_REGISTER_SEAM_CAP_S=3` for the cap's case.
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
  - **The child at the seam** (lane 4's section, "How an attempt runs the
    verb" and "The internal flag"):
    - the child honours `LANES_REGISTER_SCRATCH=<attempt dir>` only where
      the directory is under `<state>/register-writes/`, owned by this uid
      with mode 0700, with a `parent.pid` that holds the child's `$PPID`.
      It unsets the export at once, so the logged environments of the
      `git` wrapper, the fake `gh` and the fake `tmux` never hold it;
    - a write run with `LANES_REGISTER_SCRATCH` exported, naming a
      directory outside `<state>/register-writes/`, one of mode 0755, or
      one whose `parent.pid` is not the run's parent, does not take it as
      the flag: the shim's log shows the run's own snapshot and commit
      calls, as for any write;
    - with its parent killed while it waits, the child writes `seam.out`
      as `none`, writes one line to fd 4, "attempt `<k>` of write `<id>`
      stopped waiting at the seam (`<why>`); nothing after the seam ran",
      and exits 75 without its tail: the fake `gh` makes no comment call;
    - with its parent stopped by `SIGSTOP`, the same at the cap;
    - `seam.out` is written once, with noclobber: `tail` before the child
      acts on `committed`, `none` before it acts on `abandon`.
- **Tests.** In `code/tests/test_sql_register_order.py`:
  - `test_a_writer_prints_its_output_once_however_many_attempts_it_made`;
  - `test_a_refused_writer_replays_its_output_then_the_refusal`;
  - `test_a_side_effect_runs_once_after_commit_and_never_for_a_refusal[tmux|gh]`;
  - `test_a_second_reach_of_the_seam_in_one_run_exits_2`;
  - `test_the_scratch_flag_is_honoured_only_for_its_own_attempt_directory[outside-register-writes|wrong-mode|other-parent]`;
  - `test_the_scratch_flag_never_reaches_git_gh_or_tmux`;
  - `test_a_child_at_the_seam_stops_with_none_and_runs_no_tail[parent-gone|cap]`;
  - `test_seam_out_is_written_once_before_the_child_acts[committed|abandon]`.

#### AT-F18. The disaster path with an old record

- **Ruling** (D22, D24, §2.3, lane 3's F18 ruling): a generation id in the
  switch row and in every pending record and printed rerun, a mismatch
  refused with 2 at the snapshot, before any commit call and before the
  switch is read, the commit's LR007 its backstop (S1's step 1); the
  hosts re-pointed before any write; `bootstrap
  --roles-only` before a restore, since no database backup carries roles;
  and the marker rewritten by the cutover's own entry in its after-loss
  form, at `m+1`, never a third operator entry.
- **Fixture.** Test D22's re-import, with a pending record left from cluster
  A for a write at a seq up to `m`, and a printed rerun line from A. The
  printed rerun also runs between B's bootstrap and its cutover, with B's
  switch at `files`. For the label rules, direct `commit_write` calls with
  after-loss `cutover` entries.
- **Oracle.**
  - A plain rerun that meets A's record refuses with 2 at its first
    snapshot, with the effect check and the steps that may be owed, and
    leaves the record (AT-F10). The printed rerun, with its
    `LANES_WRITE_GENERATION=` naming A's generation (lane 4's section, "The
    pending record"), exits 2 at its snapshot with LR007's text. The shim's
    log holds no commit call for either, and nothing is re-applied.
  - Between B's bootstrap and its cutover, the printed rerun exits 2, not
    69, since the generation is checked before the switch.
  - Once the effect check finds the write absent on B, the same command
    with `LANES_WRITE_ID=new` mints a new id, replaces A's record with one
    of B's generation, and writes once; a plain rerun after it finds no
    record of A's. This is the one test of the replacement: AT-F10's
    refusals of a record of another generation end at the refusal, and
    AT-F6's case 19 uses the replacement for a bind.
  - B's roles exist before its restore, and `bootstrap --roles-only` creates
    no schema.
  - `lanes/STORE`'s `database=` names B. The journal holds exactly two
    operator entries in its life on B: the after-loss cutover's at `m+1`,
    and, after a later rollback, the rollback's.
  - **The label rules in the transaction** (§1.3's kinds table, N4; lane
    1's v2.6, behaviour 5 of `renames-v2.6.md`): an after-loss `cutover`
    entry with no `database_label` is LR004, in the re-import form and in
    the restore form; one that meets the switch at `primary` with the
    switch's own label is LR004; each writes nothing. In the re-import form
    at `files`, where the switch holds the replacement's own label, the
    label is not compared, and the entry commits.
- **Tests.** In `code/tests/test_sql_register_disaster.py`:
  - `test_a_pending_record_from_the_lost_database_is_refused_2_at_the_snapshot`;
  - `test_the_printed_rerun_between_a_reimports_bootstrap_and_its_cutover_exits_2_not_69`;
  - `test_lanes_write_id_new_on_the_replacement_replaces_the_old_record_and_writes_once`;
  - `test_the_after_loss_cutovers_label_rules_in_the_transaction[no-label-reimport|no-label-restore|own-label-at-primary|reimport-at-files]`;
  - `test_bootstrap_roles_only_makes_the_roles_and_no_schema`;
  - `test_the_disaster_path_rewrites_the_marker_by_the_after_loss_cutover`.

#### AT-F34. The tail, once

- **Ruling** (S1, "A write in doubt"; lane 4's section, "The tail, once";
  lane 3's ruling of lane 4's row 24, comment 6710770304). A run that
  learns its write committed, by its commit's answer, its snapshot, LR006
  or F3's lookup, runs the verb's after-COMMIT tail in one child only, one
  stopped at the seam whose change set is the entry's own: the kept child
  of the call that committed, or a replay of the verb at the entry's base,
  `s−1`, with that call's clock and sidecar pre-images. Only the pending
  record's owner sends it, after writing `tail=sent <s> <utc>` into the
  record; `tail=done` follows the child's exit, and the done mark,
  `<state>/register-done/<write_id>`, keeps the value for 15 minutes once
  the record goes. A run that finds `tail` sent or done, or that holds no
  record of the write, runs none of it and names what is owed. A replay
  never types into a pane. A replay that stops before `ready`, or reaches
  the seam with another change set, runs none of the tail, and the run
  exits 0 with one WARNING.
- **Fixture.**
  - Test 12's shim on the imported register; `claim` as the verb, whose
    tail is `lane_state_follow` and `gh_comment` (S1's table of tails),
    with a logging fake `gh` that can hold a call on a FIFO; and
    `request-handoff`, with a logging fake `tmux`, for the replay's
    typing.
  - `LANES_REGISTER_SEAM_CAP_S=3` where the kept child must be gone first:
    the shim holds the call that learns the outcome until the kept child's
    `seam.out` says `none`.
  - A test copy whose `claim` arm reads a control file that the test writes
    once the committing attempt has reached its seam: with it, the arm
    exits 1 before the seam, or appends one more line, so that a replay
    stops or differs.
  - Two workstation configurations, ws1 and ws2, as test 13 has them, so
    that `journal.workstation` can name another host.
- **Oracle.**
  - **The kept child** (lane 4's first test of "The tail, once"): the call
    in doubt committed, and the run learns it one of three ways:
    - [snapshot]: the shim's `after` mode, and the next attempt's snapshot
      finds the id;
    - [lr006]: the shim's `fork` mode, armed with `exit`, forks the first
      commit call into its own session, held before `psql` as AT-F3's
      orphan is, and exits 2 at once (section 3.2). It holds the
      next attempt's snapshot until the database clock's second has moved
      past the first snapshot's, then holds that attempt's commit call,
      releases the orphan, which commits, and then the call, which gets
      LR006;
    - [lookup]: AT-F3's [found].

    In each, the kept child's `seam.out` says `tail` and every other
    child's says `none`; the fake `gh` is called once, after the COMMIT;
    the record's `tail` reads `sent`, then `done`; the done mark keeps
    `done`; the run exits 0, with one journal row.
  - **The kept child gone** (the second): the same three, with the cap
    ending the kept child first, its line on fd 4 naming the attempt. The
    run replays: the shim's log shows one
    `register snapshot --tree <dir> --at <s−1>` and no commit call after
    it; the replay's change set has the sha256 of the entry's
    `journal_files`; the fake `gh` is called once; and the sidecar's
    pre-image is the one the committing call recorded. For
    `request-handoff`, the fake `tmux` logs no `send-keys`, and the poll
    runs.
  - **`tail=sent` found** (the fourth): the fake `gh` holds the tail's
    call, and the test kills the run's process group there, after `sent`
    and before the child's exit. A plain rerun takes the record over,
    finds its id at the snapshot and `tail` sent, and runs no tail: the
    fake `gh` gets no second call. It writes the done mark with `sent`,
    removes the record, and exits 0, saying that the steps may have run at
    the record's `<utc>` in its pid, are not run again, and are these.
  - **A replay that stops, or differs** (the fifth): with the kept child
    gone and the control file written, the replay exits 1 before `ready`,
    or reaches the seam with one more line. The run exits 0 with one
    WARNING naming the seq, then the replay's exit and last line, or the
    first path whose digest differs, and the steps that did not run. The
    fake `gh` is not called, and the record is removed.
  - **No record** (the sixth): a rerun under
    `LANES_WRITE_ID=<id>` of a write that committed and was settled, with
    no record of it here, exits 0 and runs no tail:
    - [done-mark]: within 15 minutes of the write, it says what the mark's
      `tail` says, that the steps ran at `<utc>` in pid `<p>`;
    - [this-host]: with the mark removed and `journal.workstation` naming
      ws1, it says the steps may already have run here, and names them;
    - [other-host]: with the write made from ws2, it names ws2 and the
      steps, and says to rerun there if that run ended 3.
  - Lane 4's third test, a rerun after a 3 that replays, is AT-D8's, and
    its seventh, the bind's found row, is AT-F6's.
- **Tests.** In `code/tests/test_sql_register_in_doubt.py`:
  - `test_a_call_in_doubt_that_committed_runs_its_tail_once_from_the_kept_child[snapshot|lr006|lookup]`;
  - `test_with_the_kept_child_gone_a_replay_runs_the_tail_once[snapshot|lr006|lookup]`;
  - `test_a_replay_of_request_handoff_types_into_no_pane`;
  - `test_a_rerun_that_finds_tail_sent_runs_no_tail_and_writes_the_done_mark`;
  - `test_a_replay_that_stops_or_differs_runs_no_tail_and_warns[stops|differs]`;
  - `test_a_run_with_no_record_runs_no_tail_and_names_it[done-mark|this-host|other-host]`.
- **Run time.** About 1–1.5 min: the [lookup] cases wait about 10 s each for
  `<n>`, and the others take 2–5 s each.
- **Flakiness.** Every order is a FIFO hold, or the cap's end, which the
  test waits for through `seam.out`; no sleep decides an order.

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
ids below cite that evidence. The probe was not posted, so it has no comment
id. Its folder, read only, is
`/workspace/projects/.openrepotools-private-prep/work-20261022/lane2-r1-fixture-1102/`:
- its report, `FIXTURE-PROBE-reply.md`, sha256
  `439ba2a1b62d478f8b432cd8707b6a07a1a6f9170415ef5813d3aca21b12184d`, whose
  sections 4 to 7 hold the runs;
- its prototype, `proto/tests/pgcluster.py`, 16,180 bytes, sha256
  `5f20d742434ad58424e018c7632855a35bc46557cab3eb16b9cb524b15ed87bf`;
- its `SHA256SUMS`, sha256
  `b026d1b254cee853f62dc98511f9fe484bca67e2ebf538b56a4fa2938742b1e1`, whose
  179 entries, the runs' output under `runs/` among them, all verified on
  2026-11-08.

Two system paths are named because the fixture needs them: the package
directory of PostgreSQL 16 on Debian and Ubuntu, and the system temporary
root that holds the socket. R1 and the fixture probe name them, not the
design. No other host path appears in a test.

### 3.1 R1's real-engine fixture

- **Where it lives.**
  - The module is `code/tests/pgcluster.py`, which the code leg at
    `2a057a6d` does not have yet. The probe's prototype, pinned in section
    3's opening, is its first form: Python 3.9 or later, standard library
    only. It is registered as a pytest plugin from
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
    (the owner and the groups REGISTER-WRITER and READER, which cannot log
    in, and the indexer's login) and their GRANTs. The template holds no
    test-only object. It is then marked
    `IS_TEMPLATE true ALLOW_CONNECTIONS false`, so no session can hold it
    open.
  - **The fixture's logins** (§1.4's login step). Once per session, after
    the bootstrap, `lanes-index login add fx` and `login add --dump-job`,
    run under a pty since `login` refuses off a terminal (`operations.md`
    §2.4), make the fixture engineer's `lanes_w_fx` and `lanes_r_fx`, with
    their floors, and `lanes_register_dump`; the trust socket ignores their
    passwords. The groups cannot log in, so wherever this plan has a
    session or a call run as `lanes_register_writer`, as REGISTER-WRITER or
    as READER, it logs in as `lanes_w_fx` or `lanes_r_fx`, whose privileges
    come through the group and whose floors are its own. A test that needs
    a second engineer's login, or a member made without the step, makes it
    on a `stoppable_cluster` (AT-LRMPG; AT-D9c (d)).
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
  - Section 5.6 builds two more, for AT-9's M12 and M13: the TLS cluster, on
    loopback TCP at a free port with TLS and SCRAM, and the counting
    cluster, which logs each connection and each Query message. AT-LRMPG's
    check 11 cases use the TLS cluster, and so do AT-P8's `ready`
    environment and AT-13's drill, each on an instance of its own, and
    AT-D9c (d) and AT-F6's case 21, behind the delayed loopback (section
    5.6); AT-D9c (a) uses the counting cluster.
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
    ran at once with no clash (s2). The session cluster has no TCP: the
    port number only names the socket file, and each session has its own
    directory. Section 5.6's TLS cluster alone listens on TCP, on
    127.0.0.1 at a free port.
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
  - the whole argv, per call (P1475; the probes):
    - `register commit`'s call: `-X`, `-q`, `-v ON_ERROR_STOP=1`,
      `-v VERBOSITY=verbose` and `-v SHOW_CONTEXT=never`, and never
      `VERBOSITY=sqlstate` (§1.3's step 3; S1, "One attempt, under (A″)";
      lane 3's ruling 6711564365);
    - the service's `psql` child: `-X -q -A -t -v ON_ERROR_STOP=0
      -v VERBOSITY=sqlstate` (SVC-6);
    - every other call: `-X`, `-q` and `-v ON_ERROR_STOP=1`. The design
      names no `VERBOSITY` for them, though the lookup, `import` and
      `cutover` must show a server's message or LR007's `DETAIL:` token,
      which `VERBOSITY=sqlstate` hides, so none is asserted until lane 3
      names it (section 7);
  - the connect bound, `PGCONNECT_TIMEOUT` set to the workstation's `c`, 2
    where the URL carries no `connect_timeout=` (SVC-2; D9), and each
    path's own
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
  - **The 69 refusal.** The fake exits 2 on the first snapshot, before any
    commit call was sent, and the writer exits 69 with no second call. An
    exit 2 of a commit call is in doubt instead (S1, "The outcomes"), which
    test 12 covers.
  - **The retry's shape.** The fake exits 3 with the commit call's first
    stderr line, `ERROR:  40001: could not serialize access due to
    concurrent update`, and the writer retries with the same `write_id` and
    never exits 69. Taught `ERROR:  23514: <message>` instead, any other
    SQLSTATE, the writer exits 69 naming the store and the error, with no
    second commit call. So a client that misreads the line fails one of
    the two. The shape is all it shows; that a 40001 occurs is test 4's.
  - **The line it is taught.** Every code the fake answers with exit 3 is
    taught in that form, `ERROR:  <SQLSTATE>: <message>`, and an LR007 or
    an LR005 with its `DETAIL:  <token>` line after it, as AT-8's, AT-11's
    and AT-F13's FP cases use it.
  - **The SQL's shape.**
    - One transaction per write.
    - SERIALIZABLE at the commit call, as Q7 (A″) sets it.
    - The commit call's `commit_write(…)` arguments: the snapshot's base
      `n`, the write id, `REGISTER_SCHEMA_VERSION`, the generation the
      snapshot read, and one `jsonb` entry carrying `not_after` (§1.3's
      step 3). The head row's update before the journal row (P235–236),
      and `write_id` in the journal insert, are now inside `commit_write`,
      where the fake sees neither; section 4.1's statement-order case and
      AT-LR006 show them on the engine.
    - The timeouts set at connect.
    - Reads in one `REPEATABLE READ READ ONLY` snapshot, as today.
    - One request per register call (§1.3's steps 1 and 3; the phase
      plan's "One request per call"): the snapshot's statements joined with
      `\;`, its marker sent as `COPY (SELECT E'\x1eLANES-INDEX-NEXT') TO
      STDOUT`; the commit as `BEGIN ISOLATION LEVEL SERIALIZABLE \; SELECT
      lanes_register.commit_write(…) \; COMMIT;` on stdin; the lookup as
      `BEGIN \; SELECT lanes_register.find_write(…) \; ROLLBACK;`. The fake
      answers each such script as one call, and AT-D9c (a) asserts the
      shape.
  - **Test 14's two states.**
- **The shim, an RE helper.** It is one Python script in front of the real
  `psql`, run as `python3 -I`, standard library only. A control file arms it
  per test. Each arm names its mode and the calls it applies to: call k
  alone, every call from call k on, every call, or a call whose script
  carries a given text, such as one lookup try or a rerun's snapshot. One
  test may arm several modes, each on its own calls, as AT-12's cases arm
  `before` on the first commit call, `hold` on the second snapshot and
  `edit-entry` on the second commit call. Its modes:
  - `window`: sleep W ms before handing on a call whose script commits;
  - `before` and `after`: a `pg_terminate_backend` line before or after the
    script's `COMMIT`, joined with `\;`, so the call stays one request;
  - `timer`: run the real `psql`, then sleep;
  - `fail-call` and `fail-match`: exit 2, sending nothing, on the calls the
    arm names, from call k on as AT-F6's cases 10 and 18 need, or on a
    script carrying a given text;
  - `edit-entry`: rewrite one field of a commit call's entry before handing
    it on, its `not_after` among them, for test 2's late failure, AT-LR4's
    cases, AT-LR009's act cases and AT-F3's LR008;
  - `no-reconnect`: every call after the first armed commit call exits 2,
    as from a host that cannot reconnect. That first call is lost as
    `before` or `after` loses it, whichever the arm names (test 12;
    AT-8 (i));
  - `hold`: block on a FIFO;
  - `fork`: start the call in a session of its own (`setsid`), where it
    blocks on a FIFO before it runs the real `psql`, and wait for it, so
    that the writer's timer, which kills the shim, never reaches the call
    (AT-F3's orphan). Armed with `exit`, it exits 2 once the fork is made,
    instead of waiting, so that the call is in doubt at once while it
    stays held (AT-F34's [lr006]).

  `after` and `timer` can each be joined to a FIFO: `after` blocks on it
  once the call has landed and before the shim exits 2, and `timer` blocks
  on it in place of its sleep, so the test lets the client go once its own
  barrier holds. `hold` is a FIFO by itself.
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
    (`:836–866`: the four job ids at `:836–845`, and the rest at
    `:848–866`, checked at `2a057a6d`). The probe's grep
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
  - `test_the_engine_switch_refuses_a_value_that_is_not_0_or_1`;
  - `test_no_real_engine_test_is_selected_by_the_launch_jobs_filter`, a
    collection with that job's `-k` expression. pytest's `-k` matches a
    test's whole id, its parametrize ids included, so the collection finds
    any RE test the job would select, however its id spells the two
    words. Its own name holds neither word, so the job never selects it.

## 4. The probes' cases written as tests

The probes ran each case on PostgreSQL 16.15 in a `postgres:16` container
(lane 2's probes, 2026-10-31). Each case below is a test on R1's fixture,
and the probes' numbers are recorded beside its result, never asserted.

**The timing rule.** CI asserts times in three ways only:
1. **The guard's 4.5 s** is asserted as stated.
2. **Every other upper bound the design states** (D9's timers, `bound_s`,
   D7's 30 s, the try windows, AT-F6 case 1's `T0 + 1 s`, AT-F8's "under
   3 s", and the like) is asserted at **twice its value**. This still
   catches a missing or wrong timer, because the faults the tests use would
   otherwise hang far longer. The measured time against the design's own
   bound is **recorded**, with its margin, in the test's output.
3. **Lower bounds and orderings** the design states ("not before", "after",
   "in this order") are asserted as stated.

Every other time is recorded, not asserted. Section 1.2, section 4's opening
and section 7's open point 34 state this rule in the same words, and every
test that asserts a time says which of the three it is.

### 4.1 The burst

- **Extends:** test 4, case (c); S6's tests (P678–679); ADV-C7.
- **Parameters.**
  - Ten writers, each claiming a distinct free object, released together by
    the barrier.
  - A window of W = 200 ms, injected by the shim's `window` mode between each
    writer's snapshot and its COMMIT.
  - A deadline of 30 s (Q7 (A″); D7), D7's default: the burst runs with
    `LANES_REGISTER_DEADLINE_S` unset, as AT-4 (c) does. The companion
    case below keeps its variable.
- **Oracle.**
  - All ten exit 0, and none exits 75 or 69.
  - The journal holds exactly ten new rows, with seqs 2 to 11 after a first
    cutover at seq 1 (AT-4 (c)), no gap and no repeat.
  - There are ten distinct `write_id`s.
  - Each object has one `CLAIMED`.
  - The head row reads 11.
- **The probes' numbers.** Recorded, not asserted:
  - under (A″), with no cap, the k-th writer needs k attempts (mean 5.5, most
    10). That is about k(k+1)/2 = 55 verb runs in all;
  - the drain took 2259 ms (2207–2296) at 200 ms;
  - under (A′), the lock, it took 2067 ms, with 0 aborts in 120 writes;
  - under (A), six attempts, 4 of 10 writers exhausted them in every burst
    from 40 ms up, which is why (A) fails this test (P1540–1547).

  The asserted bound is the deadline itself: no writer exits busy (the
  delta's "A burst loses no writer"). The drain is asserted under 60 s,
  twice D7's 30 s (section 4's rule 2), and recorded against the 30 s with
  its margin.
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
  - A loaded host only stretches the drain, and the companion case's "at
    least one busy" only gets likelier when the host is slow.
  - **The margin is the design's, and local.** The probes' 2.26 s drain
    was `psql` alone. A real attempt is a snapshot, a verb run and a
    commit, about 0.55–0.61 s, and "the last writer's 10 attempts take
    about 6 s, inside D7's 30 s" (lane 4's section, "The cost of one
    attempt, [L4-c]"): a margin of about 4 to 5 on an idle host, as AT-4's
    flakiness has it, and less on a shared runner. The 200 ms window adds
    up to 2 s. That figure is local, on a Unix socket with no TLS. Against
    a remote database each attempt adds its round trips: the phase plan
    puts the last writer's ten attempts at about 10 s at 20 ms, about 31 s
    at 150 ms, at D7's 30 s, and about 55 s at 300 ms ("Over a remote
    database"). D7 stays 30 s, and act 3's test 4 decides it; AT-D9c (d)
    runs this burst on a delayed link, where each writer commits once or
    exits 75 with nothing written.
  - **The drain is recorded beside D7's 30 s, and asserted only under
    twice it, 60 s (section 4's rule 2):** the time from the barrier's
    release to the last writer's exit, and each writer's attempts, counted
    as snapshot calls in the shim's log, are printed beside the result.
- **Tests.** AT-4 owns `test_ten_writers_released_together_all_commit_in_order`,
  whose oracle section 4.1 gives. Section 4.1 adds
  `test_the_burst_window_is_effective_a_short_deadline_sends_writers_busy`.
  Each run takes about 6–8 s on an idle host, the tenth writer's ten
  attempts; the probes' 2.3 s was `psql` alone.

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
  - The writer's `lock_timeout`, 2000 ms (D9), on the retry that waits for
    the head row's lock while the held session holds it (§1.3.1's step 1);
    the probes, P258–261, measured that wait at the UNIQUE `write_id`,
    before the lookup moved under the head row's lock.
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
  - Stderr is never matched to tell a loss before COMMIT from one after
    it: every exit 2 of a commit call is in doubt (S1, "The outcomes").
- **Options.** Under Q16 (a) alone, the rival case fails. D8 makes the
  report's code 3, which settles the proposal's open question.
- **Tests.** Test 12's eight, and
  `test_a_lookup_never_mistakes_a_rivals_row_for_the_lost_write`.

### 4.4 The guard's timing

- **Extends:** test 10; the guard's budget (P781–812); concern 1; ADV-C4.
- **Parameters.**
  - The hook runs under a 5 s kill timer, the harness's `GUARD_TIMEOUT`
    (`code/openRepoTools:2344`).
  - The design's shares are taken as the timeouts table and SVC-8 set them
    at W:
    - `connect_timeout` at the workstation's `c`: 2 s, the default, with
      one address, and 3 s in case 5, where the connect may exceed its
      share, since the read's own timer ends the read (the
      register-service delta's "The 5 s is one budget, spent once");
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
     names one host, the primary's own endpoint, which may resolve to `N_a`
     addresses (D9), so the case asserts that a configuration naming
     several hosts is refused, or caught by the client timer, and in either
     case exits 2 inside the budget. With `register_addresses=2` it also
     stands for one name that resolves to two addresses: the cut still ends
     the read at `T0 + 3.75 s`, never at `N_a`·`c`.
  5. **A connect bound above the share** (the register-service delta's "The
     5 s is one budget, spent once", and its scenario "A database address
     whose connect never completes", which case 1 stands for).
     `reader_url=` carries `connect_timeout=3`, and the fake `tmux` holds
     the hook's fixed parts until about `T0 + 1.0 s`, SVC-8's worst case,
     so the connect alone would end at about `T0 + 4.0 s`. Against case 1's
     listener the read's own timer ends it at `T0 + 3.75 s`, and the hook
     exits 2 in under 4.5 s, with the service up and down. The direct
     read's call carries `PGCONNECT_TIMEOUT=3`.
  6. **A whole file the start line cuts** (SVC-8, "A whole file is never
     lost to the line"), with the service down. The image cache lacks
     `lanes/LANES.md` at `n`, and the shim holds the direct read's
     whole-file fetch on a FIFO. The hook exits 2 at the cut, inside the
     budget. The fetch runs in a detached child, in a session of its own
     with the hook's descriptors closed, under its bound, `N_a`·`c` + 15 s;
     once the test releases it, the child writes the file to the
     per-environment image cache at the digest at `n`. The next prompt's
     read, in the shim's log, fetches no whole file, only the journal
     entries since, and answers.
- **Oracle.**
  - The hook exits 2, was not killed, and finished in under 4.5 s, the
    frame's assertion (`design.md`, "One budget"; section 4's rule 1). Its
    refusal names the store.
  - The elapsed time is recorded beside the probes' figure.
  - In the same cases, each asserted at twice its own bound by rule 2, as
    AT-D9's rows have them, with its elapsed time recorded beside that
    bound:
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
  - **Against a remote database.** These cases fix each share's expiry on
    loopback, with no RTT. A healthy database across the internet spends
    part of the same shares: lane 5's netem run measured the direct read
    at about 8 round trips, 1,283 ms median at 150 ms (#165 6722703424;
    section 5.6), and a lost SYN adds 1 s. SVC-8 says how the shares hold
    it ("Over a remote database"): the direct read takes `c`, and its
    timer, not the connect bound, keeps the hook's budget; a whole-file
    fetch meets the start line past about 165 ms of RTT, and case 6's
    detached fetch keeps it for the next prompt. Under section 5.6's option
    A or B, the same cases also run on a healthy delayed link, so that no
    share expires on a link that answers.
- **Tests.** `test_a_database_that_does_not_answer_is_2_inside_4_5_s[<case>-<service>]`
  for cases 1 to 4, and AT-10's
  `test_a_connect_timeout_above_the_direct_reads_share_is_cut_at_the_parse_start_line[service-up|service-down]`
  and
  `test_a_whole_file_fetch_the_start_line_cuts_fills_the_image_cache_for_the_next_prompt`
  for cases 5 and 6, all in test 10's module. The other paths' cases are
  AT-D9's `test_each_database_path_exits_inside_its_bound[<row>-<fault>]`.

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

**Brett's host.** Q10 asked "local or remote". Brett has named a managed
cloud PostgreSQL on a separate server (lane 3, #165 6722025863): Azure
Database for PostgreSQL Flexible Server, version 16, in opensoft's prod
subscription (lane 3, #165 6722593397). Every production `psql` call then
crosses the internet with TCP, TLS and SCRAM, directly at the primary's own
endpoint, or through a session-mode pooler that passes `options=` on, never
a transaction-mode one (§1.3, "The connection these bounds need"; lane 1,
#165 6722314667). Section 5.6 says what CI's figures
still stand for, gives each path's round trips and lane 5's measured
figures, and adds the rows taken at act 3.

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
| M11 | the `Register-Seq` lookup, bounded | one `git log --first-parent -n 200` with the trailer in `--format`, on a lane's register checkout, beside the unbounded and `--grep` forms | AT-F11 |
| M12 | the link and the connect | in CI, a TLS and SCRAM connect on loopback, its local share; at act 3, the RTT, and `psql -c 'SELECT 1'` with lane 1's conninfo less one query in a held session (section 5.6) | 9 |
| M13 | each path's round trips | the connections and the Query messages of M1, M2, M5, M6's render, M7, M9 and M10's part 4, counted in the server's log (section 5.6) | 9 |

M1 and M2 are each taken twice in CI: at the fixture's settings (`fsync`,
`synchronous_commit` and `full_page_writes` off), and durable, on a
`stoppable_cluster` started with the defaults; act 3 takes them a third
time (section 5.6). M5 answers Q10's question about
the 50 ms by recording each part apart: the client's process start, an
on-demand start of the service, and the answer itself.

M5b, M6, M9, M10's parse and M11 are each recorded by a test in the entry the
table names, a measurement case that asserts correctness only (section 5.4):
- **M5b:** AT-10's `test_todays_direct_guard_is_timed_in_its_four_parts`;
- **M6:** AT-1's
  `test_the_render_of_one_entry_and_of_a_full_render_is_timed_and_recorded`,
  AT-5's
  `test_a_render_commit_with_a_private_index_and_its_push_are_timed_and_recorded`
  and AT-7's `test_the_imports_proof_is_timed_and_recorded`;
- **M9:** AT-F6's
  `test_the_bind_and_the_guards_set_row_state_are_timed_in_both_modes[<mode>]`;
- **M10:** AT-10's
  `test_the_guards_register_parse_is_timed_on_a_real_sized_register_beside_todays_six_reads_through_git`
  for the parse, and
  `test_the_guard_answer_time_is_recorded_beside_todays_direct_guard` for the
  four parts with Q29's figure;
- **M11:** AT-F11's
  `test_the_register_seq_lookup_is_timed_bounded_beside_the_unbounded_and_grep_forms`.

The decision rules, and what the measurements of 2026-11-02 decided:
- **M9.** If the bind's warm p95, with the guard's fixed costs before
  `guard_answer`, does not fit before `T0 + 4.25 s`, or the bind does not fit
  in about 3 s on a healthy fixture, the detached form applies (ruling 16).
  It did not fit: 9.64 s median on primary by estimate, and 10.47 s in files
  mode today, both local with no TLS, so floors (section 5.6). Lane 3
  applied the detached form (comment 6579459518), in every mode (comment
  6710770304), so M9 is recorded, never asserted, and is taken again in
  both modes once the bind is `start-record`, which exists in both. It is
  recorded beside the child's bound, `bound_s`, which `guard-bind` computes
  from the configuration: 500 s at `c` = 2 and 530 s at `c` = 3 with one
  address, with `timeout -k 10 "$(( bound_s - 10 ))"` around the body and
  `LANES_BIND_BOUND_S` for tests (lane 4's section, "The record, and its
  liveness test"), which lane 3 accepted as arithmetic, with
  `LANES_GIT_TIMEOUT=45` in the child (lane 4's row 37), and which phase
  1's tests measure the child's bind against. Its terms are D9's timers in
  `c` and `N_a` (the phase plan's "Over a remote database"; AT-F6's case
  22).
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
- **Q29, at Brett's 1 s** (lane 3, #165 6722025863). Under Q29 (a), act 4
  waits for the service when the guard measured without it is over 1 s,
  judged on its warm p95, with the cold record walk recorded beside it and
  not judged (lane 3's ruling, in "Values Brett named on 2026-11-08").
  That figure is test 10's whole hook through the direct read, M5 with the
  service down, in M10's four parts. CI's figure is local and decides
  nothing alone. The deciding rows are act 3's, and each joining
  workstation's own (section 5.6; SVC-9).
- **M12 and M13** set no rule of their own. They give the link's RTT, the
  connect, and each path's n, which section 5.6's model multiplies.

### 5.2 Where

- **CI's `ubuntu-24.04` `tests` job** gives the gate's figure (R1). Its store
  is the fixture's cluster on the runner's disk.
- **py-bench** gives the bench figure beside it, once Q1's 1A image rebuild
  gives the bench its binaries. Until then, it is taken in a
  container with PostgreSQL 16, as R1 and the fixture probe did, and labelled
  so. The figure names the data directory's filesystem: overlay, tmpfs or the
  host's disk. R1 measured overlay and tmpfs, and the probe overlay only.
- **Act 3, from Eagle and Raven, and each later workstation as it
  joins,** gives the production figures: each bench container that runs
  lanes, against a scratch database on the Azure server, with lane 1's
  conninfo and the provider's CA (section 5.6). No test runs against the
  shared database itself (section 6.3).
- **A delayed loopback,** in CI or on a bench, only if lane 3 takes section
  5.6's option A or B.

### 5.3 How

- **Timing.** `time.monotonic_ns()` brackets each subprocess, exec to exit,
  except M2, which is `\timing` inside one session, as R1 did. M13 is a
  count, not a time (section 5.6).
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
  - M10 and M11: 30 warm runs and the first.
    - **In CI,** on inputs the test builds, since a CI checkout has neither
      bench input:
      - M10 on a synthetic register of about 2.6 MB with 73 lanes, shaped
        as the real one is: 73 rows, about 8 % of its bytes in the table, a
        longest row of about 70 KB, and the rest notes and Rule 6 lines (the
        measurements, (14a)); with that lane's log, about 90 KB. AT-10's
        fixture builds it, for M5b's register read and M10's parse;
      - M11 on a checkout of at least 30,000 first-parent commits, made by
        `git fast-import`, with a commit-graph from `git commit-graph write
        --reachable`. AT-F11's fixture builds it, for M11's test.

      Each input's build time is recorded beside its figure.
    - **On the bench,** on the 2026-11-02 inputs, by path under
      `/workspace/projects/.openrepotools-private-prep/` and sha256:
      - M10: `work-20261022/lane2-165-measure-1102/real/LANES.md`, 2,590,927
        bytes, sha256
        `337862d24be7e0dd0a3cd39447d0f75d155932c1247a994fded94d954ea5e7a1`;
        the log beside it, `real/log/openRepoTools-2.md`, sha256
        `41762765174d3faf36e652947429ef48a5259c012846225ffd66cbfb2dce0027`;
        and the record, `MEASURE.md` in the same folder, sha256
        `cbaa243d5bd6161f53375e1d4e44d9498ff6cf26615e420140854a914a4754d9`;
      - M11: lane 2's register checkout,
        `/workspace/projects/brett-wip-lanes/openRepoTools-2`, at `2ab7303e3`
        on 2026-11-02: 34,144 commits, 30,006 of them first-parent, with a
        commit-graph, as
        `work-20261022/lane2-165-seqlookup-1102/seqlookup.out` records it,
        sha256
        `e231054d42bc55a6d489bc1e9cdc40e160d96f945aaad2c457b17bd90e143179`;
        its script, `seqlookup.sh` there, sha256
        `7824f0f2eda5b26a0be62de2f45e0b81e96cc8e49cb6f3f4355beca16c9807e5`.
        The checkout moves, so a rerun names its own HEAD.
  - M4: 50 (R1, the probe).
  - M3: 10 cycles. The p95 of 10 samples is their maximum, which is said
    beside it.
  - M12's CI row: 20 warm runs and the first. M13: each path once, since a
    count does not vary with load.
  - Act 3's rows: 20 warm runs and the first each (the QA seat), not R1's
    200 writes. M1 in the design's form, one request per call, is about
    3.1 s an attempt at 150 ms (the phase plan's "The figures by RTT";
    section 5.6).
- **Statistics.** Median and p95 by nearest rank, then min–max and n.
- **Correctness alongside.** Every timed write must have committed: the
  journal grows by exactly n, and each `write_id` appears once. A measurement
  that wrote wrongly fails, whatever its time. At act 3 the scratch
  database's journal is checked the same way.

### 5.4 Where each figure is recorded

The proposal's recommendation (Q10; #165's body) is a recorded number in the
PR body, not a CI assertion on a shared runner. This plan follows it, as
follows.

- **The record.**
  - Test 9's and test 10's measurement cases, and AT-1's, AT-5's, AT-7's,
    AT-F6's and AT-F11's (M6, M9 and M11), run in the `tests` job.
  - They assert correctness only.
  - They write their table to the job's log, and to `$GITHUB_STEP_SUMMARY`
    where that is set, so every CI run shows the figures.
  - The PR body copies the table from a named CI run, with its run id, and the
    py-bench table beside it. Each row names the host, the store, the path
    and the statistic, and the link: the RTT (0 on the fixture's Unix
    socket), TLS (none there), and the path's connections and Query
    messages from M13.
  - Act 3's rows come after the PR merges, so they are not in its body.
    They are recorded where lane 3 places them, and Q29's decision cites
    them (section 5.6).
- **The 50 ms.** SVC-10 states it on the suite's fixture register. There it
  is recorded, and the test prints a warning when the median exceeds it,
  without failing, and it stands there, since the spec names that
  register. Against the Azure server every answer is at least one round
  trip to the database (SVC-7), and lane 5's netem run puts it over 50 ms
  above an RTT of about 40 ms (#165 6722703424). Act 3 records it beside
  Q29's figure, with its RTT and Query count.
- **Q29's limit, 1 s** (lane 3, #165 6722025863). Whether act 4 waits for
  the service (Q29 (a), Brett's answer) is decided by reading the warm p95
  of act 3's whole guard through the direct read against it, with the cold
  walk recorded beside it and not judged. CI's figure is a floor.
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
  - **M12 and M13** add two clusters, about 0.9 s each, and one more run
    of each path. **A delayed loopback,** section 5.6's option A or B, adds
    about 4–7 min, by lane 5's medians and the model for M1. Act 3's rows
    cost the job nothing.

### 5.5 The baseline

**Every figure in this section is local:** client and server on one host,
over a Unix socket or inside one container, with no TLS. None crosses a
network. Against Brett's managed PostgreSQL each is a floor: the production
figure is at least this plus n × RTT (section 5.6). Each still bounds the
local share of its cost from below, and shows regressions in it. None
bounds a production total, the 50 ms as a production figure, or Q29's
decision at 1 s.

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
`2a057a6d`, the real register's copy; PostgreSQL 16.15 in a `postgres:16`
container with no network, reached on its Unix socket with trust and no
TLS; "first" is the first process in a fresh estate, not a cold disk):

| Cost | Figure |
|---|---|
| M9, the bind in files mode today, against a local bare origin | 10.47 s median, 12.40 s p95; 87 % of it is not git; the guard's `set-row-state` 0.83 s median |
| M9, the bind and `set-row-state` on primary, estimated from measured parts | 9.64 s median, 11.51 s p95, 7.72 s at best; about 12 s with the costs it leaves out; local, no TLS, so a floor (section 5.6) |
| one SERIALIZABLE write, a new `psql` on PostgreSQL 16.15's Unix socket | 52.5 ms median, 68.7 ms p95, 53.3 ms first |
| `lanes-index`'s start | 76.8 ms median, 143.7 ms p95 |
| M10, the parse alone, on 2,590,927 bytes and 73 lanes | 92.9 ms median, 156.6 ms p95, 99.2 ms first |
| M10, today's six register reads through git | 307.8 ms median, 434.9 ms p95, 422.2 ms first |
| Q29, the whole guard in the budget's four parts | files mode today about 0.55–0.60 s warm, 0.9–1.0 s with a cold record walk; on primary, through the prefetch, about 0.42–0.47 s warm, local with no TLS, so a floor (section 5.6). Part 1 measured at 112 ms median; parts 2 and 3 (tmux, the record walk) are lane 5's figures |
| M11, the `Register-Seq` lookup, bounded, on 30,006 first-parent commits with a commit-graph | 13.2 ms median, 16.1 ms p95, 14.4 ms first; the `--grep` form A 300.0 ms median and 392.1 ms p95, the unbounded form B 302.9 ms and 411.5 ms |

Not measured anywhere yet:
- today's files-mode write through git, which R1 skipped (M7 gives it);
- a durable write on a host's own disk;
- GitHub's runner itself;
- the guard's parts 2 and 3, the `tmux` read and the record walk, on this
  plan's hosts (M5b; lane 5's figures stand in);
- the bind on primary as code runs it (M9 above is an estimate);
- any figure against the Azure server: the RTT, a connect with TLS and
  SCRAM, and every row of section 5.6's act 3;
- a TLS and SCRAM connect in CI (M12's CI row). Lane 5's netem run has one
  on its own harness: its shape A, a new `psql` and one query, at RTT 0,
  83 ms median (#165 6722703424).

### 5.6 Against a remote database

Brett's host is a managed cloud PostgreSQL on a separate server (lane 3,
#165 6722025863): Azure Database for PostgreSQL Flexible Server, version
16, in opensoft's prod subscription (lane 3, #165 6722593397). Every
production `psql` call crosses the internet with TCP, TLS and SCRAM,
directly at the primary's own endpoint, or through a session-mode pooler
that passes `options=` on, never a transaction-mode one (§1.3, "The
connection these bounds need"; lane 1, #165 6722314667, its condition 2;
§1.4's "TLS and the endpoint" says never a pooler's endpoint, which
section 7 asks lane 3 to reconcile). Per-engineer logins mean more
concurrent writers, and so more
contention on the head row; the bounds are D9's, in each workstation's
`c` and `N_a` (AT-D9c). The fixture's clusters listen on a Unix socket,
with trust and no TLS (section 3.1).

**What CI's clusters still stand for:**
- correctness: every oracle of sections 2 to 4, which no round-trip time
  (RTT) changes;
- the local share of each cost: process starts, the parse, the server's
  work and the client's own;
- regressions in that share, which the step summary shows on every run;
- each path's count of connections and Query messages (M13), which no load
  moves.

**What they no longer stand for:**
- a production total of any row;
- the 50 ms as a production figure (section 5.4);
- Q29's decision at 1 s;
- margins built on local figures, such as §4.1's margin of about 4 to 5 for
  the burst and M9's "about 12 s with the costs it leaves out".

**Lane 5's netem run** (#165 6722703424) measures the shapes this section
needs: PostgreSQL 16.15, OpenSSL 3.5.7, TLS 1.3 and SCRAM, behind a
`tc netem` delay, with 30 timed runs after 3 warm-ups for each of A, B1
and B2, and 10 after 1 for C. Under
`/workspace/projects/.openrepotools-private-prep/work-20261021/lane5/`:
its scripts and raw timings, the folder its report names,
`w-165-remote/netem/`, which no longer exists, are in
`records-20261108/netem-scripts-raw-20261108.tar.gz`, sha256
`5bf262322f7829360411316df4aef3cc39ffd887d2f606ac9d5ff5d9b4dad58d`, under
`netem/`; and its report is
`w-165-remote/REPORT-NETEM-reply-20261108T1425Z.md`, sha256
`055088c7eb8b578d38759024612f6f68c0ef78c0f1db32c016a5d19a218bdb68`. Its
shapes:
- **A,** the direct read: a new `psql` per read, one query through `-c`;
- **B1,** the service's request on its held connection, its statements
  sent one by one;
- **B2,** the same request as one query, joined with `\;`;
- **C,** the 2,590,927-byte whole file on the held connection after 10 s
  idle.

Lane 5 takes one query for both forms: the service's request joined with
`\;`, and the direct read through `-c`. Its medians, in ms:

| Shape | RTT 0 | 20 ms | 50 ms | 100 ms | 150 ms | Round trips |
|---|---|---|---|---|---|---|
| A, the direct read | 83 | 239 | 491 | 882 | 1,283 | 7.8 to 8.2 |
| B1, the service's request, one by one | 6 | 107 | 257 | 506 | 757 | 5.0 |
| B2, the service's request, one query | 5 | 27 | 56 | 106 | 156 | 1.0 |
| C, the whole file after 10 s idle | 27 | 175 | 417 | 818 | 1,219 | about 8, from slow start |

**The model, where lane 5 measured nothing.** A path's time is its local
share plus n × RTT, where n = c × n_c + q. Its counts rest on public
sources, cited by name, and on lane 5's run where it measured them.
- **c,** the path's new connections, one per `psql` process.
- **n_c,** the round trips of one connect before its first query:
  - TCP's three-way handshake: 1 (RFC 9293, §3.5);
  - TLS, with its SSLRequest: 3 under OpenSSL 3.5's default key shares, or
    2 where the client offers P-256 first (lane 5's netem run):
    - the SSLRequest and its one-byte answer: 1 (PostgreSQL 16's
      documentation, §55.2.10, "SSL Session Encryption"); libpq 16 always
      sends it;
    - the TLS 1.3 handshake: 1 (RFC 8446, §2), and 1 more where the
      server's group, PostgreSQL 16's `ssl_ecdh_curve` prime256v1, is not
      the client's first key share, since the server then sends a
      HelloRetryRequest (RFC 8446, §4.1.4). TLS 1.2's handshake is 2 (RFC
      5246, §7.3). A new process has no session to resume;
  - the StartupMessage, answered by AuthenticationSASL: 1;
  - SCRAM-SHA-256's two exchanges, the client-first message to the
    server-first, then the client-final to AuthenticationSASLFinal,
    AuthenticationOk and ReadyForQuery: 2 (§55.2.1, "Start-up"; RFC 5802,
    §3; RFC 7677).

  So n_c is 7 under the default key shares, and 6 with P-256 offered
  first. Lane 5's A, one connect and one query, measured 7.8 to 8.2 round
  trips, and 4.4 to 5.1 with `sslmode=disable`. Terminate costs none: the
  client sends it and closes (§55.2.9).
- **q,** the Query messages the client waits on, one round trip each
  (§55.2.2, "Simple Query"). `psql` reading a script sends each statement at
  its semicolon and waits for its results; `\;`, or one `-c` string, joins
  statements into one Query (`psql`'s documentation, `\;` and `-c`).
  `lanes-index`'s reader today feeds `psql` a script on stdin
  (`code/lanes-index:1310–1318`): `SET search_path`, `BEGIN`, one `COPY` per
  read and `COMMIT` (`:1331–1344`), so k reads cost k + 3 one by one.
- **Not in n:**
  - the host name's lookup, which act 3's M12 includes;
  - a lost SYN: 1 s more, the initial retransmission timeout (RFC 6298, §2
    and §5.7), which `c`'s 1 s over `t_c` holds (D9);
  - a second address for one host name, which every D9 timer counts as
    `N_a`·`c` (`register_addresses=`);
  - a large answer after an idle connection: about 8 round trips from slow
    start, lane 5's C.

**Each path, and its total at lane 4's RTTs.** Lane 5's figure where it ran
the shape; the model only for the writer's two calls and what is built of
them. Those totals use A's measured network share, its median less its
83 ms at RTT 0: 156 ms at 20 ms and 1,200 ms at 150 ms per call.

| Path | Source | At 20 ms | At 150 ms |
|---|---|---|---|
| M5, the service's request, warm: one REPEATABLE READ READ ONLY transaction on its held connection (SVC-6, SVC-7) | lane 5's B2, one query; B1, one by one | 27 ms; 107 ms | 156 ms; 757 ms |
| M5's direct read, the service down: the head and digest query (SVC-9) | lane 5's A | 239 ms | 1,283 ms |
| M1, the design's writer under Q7 (A″): the snapshot call, then the commit call (§1.3) | the model: two of A's shape, 16 round trips, one request per call as the phase plan sends it ("One request per call"), over about 0.55 s an attempt (lane 4's section, "The cost of one attempt, [L4-c]"); "The figures by RTT" gives 1.03 s and 3.11 s, counting `t_c`'s local share | 0.86 s | 2.95 s |
| M7, a landing's five writes | the model: five of M1, joined | 1.56 s over their local share | 12.0 s over their local share |
| M9, answer 2's bind: `start-record` and `set-row-state`, two writes, and the 11-02 estimate's 19 register reads (the measurements, (16c)) | M1's model for the writes; B2 or A for each read | 1.0 s through the service, 3.6 s by direct reads, over the 9.64 s estimate | 7.7 s through the service, 27.6 s by direct reads, over the 9.64 s estimate |
| M5b, today's direct guard; M10's parse and today's reads through git; M11 | no database | unchanged | unchanged |

- **M2 and R1's one-call write** are no production path under Q7 (A″).
  M2's held session is B1's shape.
- **M5's on-demand start** connects in the background, never on a
  request's time (SVC-6), so a cold request is answered by the direct read.
- **M7:** a write verb on primary makes M1's two calls per write, and each
  register read is M5's request or, with the service down, a direct read.
  M13 counts each verb's reads.
- **M9:** the Rule 3 stamp is a git push, not the database. Every total is
  inside `bound_s`, 500 s at `c` = 2 and 530 s at `c` = 3 with one address,
  and the phase plan puts the healthy bind at about 18 s at 20 ms, 44 s at
  150 ms and 75 s at 300 ms ("Answer 2's detached bind").
  `start-record` may change the read count; M13 counts it.
- **M6's render** reads the journal once a pass: one connection, and q by
  its reads (M13).
- **M10's part 4 on primary,** in Q29's figure, is M5's request through the
  service, or the direct read without it.
- **M3 and M4** are the fixture's own costs, with no production path.

**Q29 (a) at 1 s.** Act 4 waits for the service when the guard measured
without it is over 1 s (lane 3, #165 6722025863), judged on its warm p95.
- **What decides:** the warm p95 of test 10's
  `test_the_guard_answer_time_is_recorded_beside_todays_direct_guard`, its
  whole hook through the direct read with the service down, in M10's four
  parts, taken at act 3 on Eagle's and Raven's benches, and by each later
  workstation as it joins, which records its own figure (SVC-9).
- **CI's figure is local,** a floor: about 0.46–0.51 s for the direct guard
  on a Unix socket (SVC-9; lane 2, #165 6722518182), beside section 5.5's
  0.42–0.47 s through the prefetch.
- **Lane 2's figures on lane 5's netem run** (SVC-9; lane 2, #165
  6722518182, on lane 5's read times, #165 6722703424): the direct guard is
  about 0.65–0.70 s at 20 ms, 0.90–0.95 s at 50 ms and 1.30–1.35 s at
  100 ms. It passes 1 s above an RTT of about 57 to 63 ms, and after any
  lost SYN. Through the service, as one query, it is 0.44–0.49 s at 20 ms
  and 0.57–0.62 s at 150 ms. W gives no figure for the direct guard at
  150 ms.
- **The statistic** is the warm p95 against the 1 s, as lane 5 suggested
  and lane 3 ruled ("Values Brett named on 2026-11-08"): the cold record
  walk, about 0.35–0.40 s more by W's figures (SVC-9), is recorded beside
  it and not judged
  against the limit.
- **The guard's cost against a remote database** is SVC-8's and SVC-9's,
  which this plan cites and does not restate. Its direct read takes `c`,
  which may exceed its share, since the read's timer ends it by
  `T0 + 3.75 s` (the register-service delta's "The 5 s is one budget,
  spent once"; section 4.4's case 5; AT-10). Where the workstation's round
  trip is over 100 ms, the service's timer is that round trip plus 100 ms
  (SVC-6, `register_rtt_ms=`), so its request is not cut at 200 ms.

**Act 3's rows: where and how the production figures are taken.**
- **Where:** from Eagle and Raven, which act 3 configures first (lane 3,
  #165 6722593397), and from each later workstation as it joins, before it
  writes, in each bench container that runs lanes, against a
  scratch database on the Azure server. The conninfo is lane 1's:
  `sslmode=verify-full`, with the provider's CA as `sslrootcert=`, on the
  primary's own endpoint (lane 1, #165 6722314667, its M5). No test runs
  against the shared database (section 6.3). The scratch database shares
  its server, network path and TLS, and its writes never reach the
  register.
- **The rows:**
  - **The RTT:** 20 TCP connects to the server's port, each timed from
    `connect()` to its return by a standard-library probe; and `SELECT 1`
    in one held session under `\timing`, which adds the server's own time.
  - **M12, the connect:** `psql -X -A -t -c 'SELECT 1'` with lane 1's
    conninfo, exec to exit, less the held session's `SELECT 1`. That leaves
    the process start, the name lookup, TCP, the SSLRequest, TLS, the
    startup and SCRAM. The workstation's `c` is `max(2, ⌈t_c + 1 s⌉)`,
    `t_c` being the median of 50 such cold runs (D9; the phase plan's "What
    act 3 measures"). The same run reads `pg_stat_ssl` for its own backend, so
    the row names the TLS version. Against the RTT, M12 also shows whether
    the Azure server meets the client's first key share, so whether TLS is
    2 round trips or 3.
  - **M1 in the design's form, M2, M5** (the service's request, and the
    whole hook through the service and through the direct read) **and Q29's
    whole guard** in M10's four parts, each with its c and q from M13
    beside it. Each row checks the model and lane 5's run: the figure less
    its local share should be about n × RTT.
  - **M9,** recorded once, beside its `bound_s`.
  - **The rest of the phase plan's list** ("What act 3 measures"; AT-D9c
    (e)): the endpoint's address count by `getent ahosts`, which sets
    `N_a`; `psql --version`, 15 or later; 50 warm connects beside the 50
    cold; where libpq cuts the connect, against an address that drops SYNs;
    test 4's burst of ten at the workstation's own RTT, each writer's
    attempts and time recorded, which decides D7 there; one run with an
    empty state directory; and `register_rtt_ms=`, the median of ten
    `SELECT 1` round trips on one connection (SVC-6).
- **How:** section 5.3's timing, statistics and correctness, with 20 warm
  runs and the first for each row.

**What tests 9 and 10 record, and the acceptance numbers.**
- **Test 9** records in the PR body the host, the store, the path and the
  statistic (section 5.4), and now the link: the RTT, TLS, c and q, and the
  connect's share. On CI's fixture those are 0, none, M13's counts and 0.
- **Its acceptance number** for the cost of one write against the Azure
  server is act 3's M1 in the design's form: the warm median and p95 from
  Eagle and from Raven, with the RTT, the TLS version, M12 and n beside
  them. CI's figure is the gate's: the local share, and a floor.
- **Test 10** asserts no time in CI. The 50 ms is recorded on the fixture
  with a warning (section 5.4), and its fault cases assert the frame's
  4.5 s on loopback (section 4.4). At act 3 it records the service's answer
  time, and the whole hook through the service and through the direct
  read, each with its RTT and n.
- **The service answers from the database each time.** Under SVC-7, each
  request runs one REPEATABLE READ READ ONLY transaction on the held
  connection, reading the head `n` and the digests at `n`, and the service
  sends the images it holds only where their digests match the database's
  at `n`. So each answer is at least one round trip; lane 5's B2 measured
  1.0. The register-service spec's 50 ms names the suite's fixture
  register, so it stands there. Against the Azure server the answer is
  over 50 ms above an RTT of about 40 ms; act 3 records it beside Q29's
  figure, and SVC-9 says that test 10's figure on the fixture is a floor
  that decides nothing for Q29.

**A delayed loopback: options for lane 3.** Lane 5's netem run already
times A, B1, B2 and C over `tc netem` at RTTs of 0, 20, 50, 100 and 150 ms.
These options reuse its shapes, RTT set and scripts, cited and not
reworded, and add M1 in the design's form.
- **A, lane 5's harness as a CI step.** `tc qdisc add dev lo root netem
  delay <RTT/2>` needs root; GitHub's hosted runners give passwordless
  `sudo` (GitHub's documentation, "About GitHub-hosted runners"). netem on
  `lo` delays every loopback packet, so the step runs after the suite,
  never during it, and removes the qdisc in a trap. Whether the runner's
  kernel loads `sch_netem` is not checked; the step's first run prints it.
  It also makes one lost SYN possible on loopback, through netem's `loss`,
  which open point 11 could not.
- **B, a delaying relay in the suite.** A standard-library relay on
  127.0.0.1, as AT-D9's F2 relay is, holds each chunk RTT/2 in each
  direction and delays its upstream connect by one RTT. It needs no root
  and runs in pytest on any host, as
  `test_each_shape_on_a_delayed_relay_is_recorded[<shape>-<rtt>]` (AT-9).
  It models no slow start, loss or kernel timer, so it checks n for small
  messages, not C.
- **C, neither.** M13's counts, lane 5's run and act 3's rows carry the
  model.
- **Two cases need one at W:** AT-F6's case 21 and AT-D9c (d) run through
  the delayed loopback lane 3 picks, with 75 ms added each way and TLS on
  (lane 4's texts, #165 6722839180). Option B in front of the TLS cluster
  gives both without root; option A gives them with netem's loss as well.
  Until lane 3 picks, neither runs.
- **The cost of A or B,** at lane 5's 30 runs after 3 warm-ups, from its
  medians: about 2.7 min for A, B1 and B2 over the five RTTs, or 1.5 min
  over 0, 20 and 150 ms only; M1 in the design's form, by the model, about
  4.3 or 2.4 min more. C's 10 s of idle per run makes it about 10 min over
  the five RTTs at lane 5's 10 runs after 1, and about 29 min at 30 after
  3, so C stays on the bench.

**M12 and M13 in CI.**
- **M12's CI row, the TLS cluster.**
  - A `stoppable_cluster` that listens on 127.0.0.1 at a free port, with
    `ssl=on`, a certificate from a test CA made by one `openssl req` call,
    and one `pg_hba.conf` line, `hostssl` with `scram-sha-256`. The roles
    get SCRAM passwords.
  - The client uses lane 1's conninfo form, `sslmode=verify-full` and
    `sslrootcert=` (lane 1's M5, #165 6722314667), with `host=localhost`
    for the name check and `hostaddr=127.0.0.1`, so it makes one connect
    (libpq's documentation, `hostaddr`).
  - It times the local share of a TLS and SCRAM connect on CI's host,
    beside lane 5's A at RTT 0, 83 ms, and it is the TLS path open point 6
    asks about.
  - It costs one more cluster, about 0.9 s (section 3.1), in each test
    that starts one: AT-9's M12, AT-LRMPG's check 11, AT-P8's `ready`
    environment and AT-13's drill. It adds `openssl` to those tests'
    tools.
- **M13, the count.**
  - A `stoppable_cluster` started with `log_connections=on` and
    `log_statement=all`, its log on the postmaster's stderr.
  - Each path runs alone, between two marker statements. The test counts
    its `connection authorized` lines, c, and its `statement:` lines, q.
  - The server logs one `statement:` line per Query message, with its whole
    string (PostgreSQL's `exec_simple_query`), so a joined call counts
    once.
  - The counts are recorded beside each figure. A ceiling per path would
    catch a statement added to a path, one more round trip on every
    production call, and no load moves a count. For the writer's
    snapshot, commit and lookup the design sets it, one Query each (the
    phase plan's "One request per call"), which AT-D9c (a) asserts. Whether
    to assert the other paths' is lane 3's to say.

## 6. Coverage

### 6.1 By the proposal's requirements

The proposal has no requirement headings. Its "What Changes" has twelve
subsections at V and W, under the headings it had at `21f0191c`. Ten are rows
below. The other two, the agreement tests and the manual, are this plan's
tests and documentation, and the last five rows are Brett's answers of round
1 and Q1's. The `P<n>` ranges stay at `21f0191c`, as "How it cites" says. W
changed the proposal's test 10, where the 50 ms is now the fixture's, and its
answers to Q22, Q24, Q28 and Q29. Its twelve subsections keep their names.

| Requirement | Tests |
|---|---|
| S1, the configuration and the switch (P147–188) | 3; 2 (switch not `primary`); 1 (the mode that wrote a row last); 14 (`mode`, D3; SQLite refused); P8 (the flip) |
| S1, one transaction per write and exit 69 (P190–295) | 1; 2; 4 (40001, busy 75, the statement order); 12; D8; D9 (writers' rows); AT-F3 (with LR008); AT-LR006; AT-F33; AT-F5 (the expectations); AT-F10 (the pending record); AT-F14; AT-F34 (the tail, once) |
| S2, the journal and the renderer (P297–413) | 1; 5; 6; C8; D9 (the renderer's row) |
| S3, readers fail closed (P415–493) | C5; P3; 10; 11; D9 (the reader's row) |
| S4, the managed seam (P495–514) | 1 (the storage-layer entry); C5 (`managed-projection`: 0, 1 and 8 from SQL, `test_managed_projection_answers_0_1_8_from_sql_as_files_mode_does[<answer>]`, and 1 when unreachable, `test_managed_projection_with_the_database_unreachable_exits_1_never_legacy`); the existing verbatim-block test against `3c26041`, kept |
| S5, import and rollback (P516–598) | 7; 8; 13; D22; section 4.2 |
| The operator tools (P600–627) | P8; 14 (the readiness item) |
| S6, claim order (P629–679) | 4; C10; section 4.1; 1 (UTC) |
| The roles, clause (b) (P681–693) | R |
| The service and the guard (the proposal's "The register service, and the guard as its client") | 10; P3; D20; D9; AT-F6; AT-F7; section 4.4; 9 and section 5 (M5, M9 to M13, section 5.6: `test_each_paths_connections_and_queries_are_counted[<path>]`, `test_a_tls_and_scram_connect_on_loopback_is_timed_and_verified`) |
| Round 1's 2A: `lane-start` and `lane-handoff` (P1558–1631) | 11; C5 |
| Round 1's 1A: the refusal of `mode=primary` (P1404–1418) | 14; P8 |
| Round 1's 3A: `mode=index` until the flip (Q20(B)) | P8 (an early `mode=primary` is a fault); 13 |
| Round 1's 4A: writes with the service down (Q2) | 2 (`test_a_write_goes_on_with_the_service_down`); 10; R |
| Q1's 2A: CI refuses (P1139–1144) | section 3.3's five tests |

### 6.2 By the delta specs' requirements, at commit W

These tables are written against the delta specs at lane 3's commit W,
`bb08d0803dcad66052d5e275d9d75f431063b73c`: 88 requirements, 69 in the
sql-register delta and 19 in the register-service delta, by name and in W's
order. A script counted them three ways, by their headings, by the blocks
that hold a SHALL and by the heading lines, and each way gave 69 and 19. The
scenarios number 202 and 53, against 185 and 53 at commit V and 160 and 44
at commit I. Four marks say what changed:
- A † marks a requirement whose text changed between `1c45123a` and
  `21f0191c`: 13 of the 82 then, 10 in the sql-register delta and 3 in the
  register-service delta, none renamed.
- A ‡ marks one whose text changed between `21f0191c` and commit I
  (`2be70f5a`): 11 of the 83, 9 in the sql-register delta and 2 in the
  register-service delta, one of them added ("`lane-start`'s bind is one
  write") and none renamed or removed.
- A ¶ marks one whose text changed, or that was added, between commit I and
  V, not counting a change to a "Brett's answer" note: 18 of V's 86, 15 in
  the sql-register delta and 3 in the register-service delta. Three were
  added ("A `Register-Seq` above the journal's head stops the renderer",
  "The cutover takes its database label from the workstation's
  configuration" and "`outage declare` during a rollback records the
  rollback's head") and one was renamed (the arms requirement, now "...seven
  default read arms"). No requirement was removed. 13 requirements of the
  first delta and 3 of the second changed since commit I only in such a
  note, and carry no mark.
- A ◊ marks one whose text changed, or that was added, between V and W,
  not counting a change to a "Brett's answer" note: 9 of the 88, 7 in the
  sql-register delta and 2 in the register-service delta. Two were added
  ("A `LANDING EXPIRED` releases one stale `LANDING`, or writes nothing" and
  "A stale `LANDING` is released by `append-line --landing-expired`, on
  Brett's word"). Seven changed: five in the sql-register delta (the
  bootstrap, the readiness check, the order of `HOLD` and `LANDING`, the
  lines that take and release `main`, and the three roles) and two in the
  register-service delta (the guard's fallback and the 5 s budget). None was
  renamed or removed.

Each row's tests are written against the text at W. 79 of the 88 have tests;
nine have none or only a partial one (below).

`specs/sql-register/spec.md`, 69 requirements:

| Requirement | Tests |
|---|---|
| `mode=` selects the register's store ¶ | 14 (`mode`, 78 for Q6(c), D3; SQLite refused); P8 (the flip; `test_the_readiness_check_10_compares_register_label_with_the_switch_label[<case>]`, and the `register_label=` line the bootstrap prints); AT-F8 (every failure of the mode query, and the marker check's 69 on a SQL estate); AT-LRLABEL (the key's parse: `test_lanes_index_accepts_the_register_label_key`, and `cutover`'s two refusals: `test_cutover_without_register_label_is_refused_2_before_it_connects`, `test_cutover_at_files_with_a_label_not_the_switchs_is_refused_2_before_it_sends[<case>]`) |
| The estate switch is a database row and the marker `lanes/STORE` | 2; 7 (the cutover, D21); 8; AT-SET (the marker's states); AT-F2 (the operator entries) |
| A writer that is not `mode=primary` refuses under the marker | 3 (a files-mode writer, and a `mode=index` one between the marker and the flip); 2 (a primary writer that meets a switch not `primary`, with the same text: `test_each_write_exits_69_when_the_switch_is_not_primary[<verb>]`); 8 (c) (the writes that make no fetch, stopped by the switch whatever they fetched, with the same text: `test_a_missed_primary_writer_is_refused_after_rollback[<case>]`); AT-F8 (a failed mode query, refused on a SQL estate) |
| Nothing changes where the marker is absent ‡ ¶ | the existing files-mode suite, unchanged but for the delta's three exceptions: answer 2's bind detached in every mode, files mode included, and `lane-start`'s bind as one `start-record` write, one register commit and one push in files mode, both in AT-F6 (Brett's A, comment 6721370130); and `wip init`'s refusal under `lanes/STORE`, and where its seed would write under `lanes/` beside another file there, in a tree that lacks `lanes/LANES.md` or `lanes/log/README.md` (FR-005, narrowed by Brett's "Narrow it (Recommended)", #165 6773442813), in AT-3 (`test_wip_init_refuses_where_a_lanes_file_exists_with_no_marker[<configuration>-<file>]`, with no configuration and with `mode=index`); 14 (D3: no configuration, no `lanes-index` call) |
| Every row records the mode that wrote it last | 1 |
| Readers on a workstation that is not `mode=primary` once `lanes/STORE` exists | 3 (`test_an_unconfigured_workstations_acts_refuse_and_listings_read_the_mirror`; the refusal names the ref's age; a marker on the read ref alone) |
| A build without the renderer's git half refuses `mode=primary` † ¶ | 14 (the refusal in place and lifted; `test_register_read_exits_78_under_the_refusal[<form>]`; `test_the_refusal_constant_is_true_exactly_when_render_checkout_exists`); P8; readers under the refusal, where `register read` exits 78 and a read that decides 69, the listings and the `SessionStart` block read the mirror with a note, the guard exits 2 and `managed-projection` answers 1: `test_readers_under_the_refusal_answer_the_78_as_a_failed_read[<read>]` |
| A server-side gate protects the rendered paths and `lanes/STORE` ‡ | 5 (D23: the deploy key's push to an explicit SSH URL, in its own environment); 6 (a pre-receive hook as a model that guards exactly the render set and `lanes/STORE`: `test_a_server_side_gate_refuses_the_stale_writers_push[<path>]`, `test_the_gate_model_lets_a_push_to_an_unguarded_path_through[<path>]`); AT-F4 (the model's order in the drill, with `--hold-marker`; its hook guards exactly the same paths). The ruleset itself: **not covered** (section 6.3) |
| `wip init` and the `LANES.md` link on a SQL estate | 3 (`wip init` under the marker: `test_wip_init_refuses_under_the_marker`; where a `lanes/` file exists with no marker: `test_wip_init_refuses_where_a_lanes_file_exists_with_no_marker[<configuration>-<file>]`). The link: **not covered** |
| Every register write is one transaction ‡ | 1; 2 (the partial failure); AT-LR5 (the head rolled back); AT-LR4; AT-F3 (LR008 rolls back); the FP shape tests; AT-F6 (`test_the_bind_is_one_register_write_and_refuses_a_changed_row`) |
| Direct writes to the files refuse on `mode=primary` | 2 (`test_each_direct_write_refuses_with_2_on_primary`, the queued `lanes/` commit, the queued handoff) |
| Unreachable or refused is exit 69 with nothing written ¶ | 2 (the projection writer's storage-layer entry among the writes: `test_the_storage_layer_entry_exits_69_when_the_database_is_unreachable`); AT-VER (LR003); AT-LR4 (LR004 twice); a refusal by the register's own rules exits 2, not 69, and writes nothing: 4 (a), AT-LR5 (LR005), AT-D19, AT-F5 (LR009's `expectation:` 2), AT-F10 (LR007), 11 (`test_lane_start_dies_with_2_on_any_2_from_start_record_not_only_the_expectations[<case>]`, with a key that is not a scope), AT-F5 (`test_a_line_deriving_a_key_that_is_not_a_scope_exits_2_on_lr010_once[<case>]`, at a write); AT-LR010 (the engine's refusal of a bad key at a write that deletes its line: `test_a_write_that_deletes_a_line_with_a_bad_key_is_refused_lr010`, and at an import: `test_an_import_whose_line_derives_a_bad_key_is_refused_lr010_and_writes_nothing[<case>]`) |
| A serialization failure is retried, not refused ¶ | 4 (e); AT-F14 (output once per write); the FP retry shape; 4 (c) and section 4.1 (`test_ten_writers_released_together_all_commit_in_order`: the seqs run 2 to 11 after a first cutover) |
| A lost race is not an error on the sequence key | 4 (c); section 4.1; the FP shape (D6) |
| A write that cannot get its turn reports "register busy" with a code of its own ¶ | 4 (d); 4 (h) (D7's 30 s default: `test_the_default_deadline_is_30_s_for_a_write_that_never_gets_its_turn`); 4 (c) (the burst at that default, whose drain the deadline must exceed); section 4.1's companion; C5; AT-F3 (never before the lookup for a write in doubt; LR008 is a lost turn); test 12 (the in-doubt timings); a refusal by the register's own rules exits 2 (see the exit 69 row) |
| A COMMIT whose outcome the writer cannot learn is recovered by its write id † ¶ | 12 (an in-doubt write whose next attempt is refused for another reason: `test_an_in_doubt_write_whose_next_attempt_is_refused_makes_its_lookup_before_it_exits[<refusal>]`; a `request-handoff` that runs none of its tail, which exits 2: `test_a_request_handoff_that_runs_none_of_its_tail_exits_2_and_writes_nothing[<state>]`); D8 (a rerun finds its write and runs the tail: `test_a_rerun_that_finds_its_write_committed_runs_the_verbs_after_commit_tail`, `test_a_rerun_after_a_3_replays_the_verbs_tail_once` and one test for each verb's tail, from `log` to `start-record` (`claim`'s is AT-F34's); a run that finds its write committed runs none of the tail: `test_a_sent_record_whose_child_never_read_committed_replays_and_an_owed_mark_runs_no_tail[<case>]`, `test_a_replay_of_rename_lane_or_request_handoff_that_stops_or_differs_runs_none_of_the_tail[<case>]`, and AT-F34's cases below; a run killed after its call line or mid-call: `test_a_run_killed_after_its_call_line_or_mid_call_is_taken_over_in_doubt_and_waits_past_not_after[<when>]`; the record's `call` lines and claim files); section 4.3; AT-F3 (in doubt at the deadline: `test_a_write_in_doubt_at_the_deadline_makes_one_lookup[<case>]`; the lookup waits past `not_after`; a try with no answer is made again: `test_a_lookup_try_without_an_answer_is_made_again_inside_the_window[<case>]`; refused for another generation: `test_a_lookup_refused_for_another_generation_exits_2_and_keeps_the_record`, and for another write: `test_a_lookup_refused_for_another_write_exits_2_and_removes_the_record`; made too early, LR011: `test_a_lookup_made_too_early_exits_3_and_keeps_the_record`, `test_find_write_refuses_lr011_at_once[<case>]`; LR008's 75 with no lookup: `test_a_write_whose_every_call_ended_lr008_exits_75_with_no_lookup`); AT-F34 (the tail, once: `test_a_call_in_doubt_that_committed_runs_its_tail_once_from_the_kept_child[<settled-by>]`, `test_with_the_kept_child_gone_a_replay_runs_the_tail_once[<settled-by>]`, `test_a_rerun_that_finds_tail_sent_runs_no_tail_and_writes_the_done_mark`, `test_a_replay_that_stops_or_differs_runs_no_tail_and_warns[<case>]`, `test_a_run_with_no_record_runs_no_tail_and_names_it[<case>]`, `test_a_replay_of_request_handoff_types_into_no_pane`); AT-F10 (a rerun that finds the same write still running, which exits 3: `test_a_live_pending_record_is_waited_for_not_shared[<case>]`; a claim from another host or container, live for 120 s: `test_a_claim_from_another_host_or_container_counts_as_live_for_120_s[host-now|container-now|claim-old-record-fresh|both-old]`; a run under `LANES_WRITE_ID` that waits for a live owner: `test_a_lanes_write_id_run_waits_for_a_live_owner[ends-in-time|live-at-deadline]`; a claim with no record beside it: `test_a_claim_with_no_record_beside_it_is_waited_for[ends-in-time|live-at-deadline]`; liveness from the highest claim: `test_liveness_is_the_highest_claims_never_the_records_pid[<case>]`; a write id of other arguments, verb or generation, refused 2 at the snapshot: `test_a_write_id_of_other_arguments_verb_or_generation_is_refused_2_at_the_snapshot[<case>]`; a record of another generation, whatever its age: `test_a_record_of_another_generation_is_refused_2_until_lanes_write_id_new[<case>]`; a write id already journaled for the same arguments: `test_a_write_id_already_journaled_for_the_same_arguments_exits_0_at_the_snapshot`); AT-F18 (another generation); AT-LR006 (cases -7 and -8, the two LR007 interleavings: `test_another_writes_entry_committed_after_the_snapshot_read_gives_lr007_another_write`, `test_a_generation_change_after_the_snapshot_read_gives_lr007_generation`); AT-LR007 (the order of the commit's first checks: `test_a_write_of_another_generation_at_files_gets_lr007_not_lr002_or_lr001[<case>]`, `test_the_switchs_own_generation_at_files_still_gets_lr002`, `test_an_old_generation_id_after_a_restore_form_cutover_gets_lr007_not_lr006[<case>]`, `test_a_same_generation_id_after_a_restore_form_cutover_gets_lr006[<case>]`, `test_an_id_in_the_journal_for_another_lane_gets_lr007`); AT-LR011 (the lookup at the engine: `test_find_write_returns_this_writes_seq_or_null[<case>]`, `test_find_write_refuses_another_writes_entry_lr007[<case>]`, `test_find_write_refuses_another_generation_lr007[<case>]`, `test_find_write_gives_lr011_before_lr007`, `test_find_write_waits_on_the_server_clock_holding_no_lock`, `test_a_lookup_held_at_the_head_row_ends_55p03_inside_its_statement_timeout`, `test_an_orphan_that_takes_the_lock_past_its_not_after_gets_lr008_and_the_lookup_finds_it_absent`); AT-F33; AT-8 (a committed write found after the switch has moved: `test_a_write_that_committed_before_the_rollback_is_found_by_its_rerun_after_it`) |
| Handoffs stay git files written by the lane | 11; 2 (the queued handoff) |
| `lane-start`'s bind is one write ‡ ¶ | AT-F6 (one register commit and push in files mode and one journal entry on primary; two sessions on one lane in both modes; a refused or in-doubt bind's `bind-failed` record; the bind child's lookup with the five copied values: `test_the_bind_childs_lookup_sends_the_five_copied_values[<case>]`); 11 (a resume's note, stamp and launch, and a new lane's death with the code, on `start-record`'s 75, 3 or 69); AT-F5 (`start-record`'s union expectation); a bind refused with another 2: `test_lane_start_dies_with_2_on_any_2_from_start_record_not_only_the_expectations[<case>]` (AT-11) |
| Every database path is bounded | D9; section 4.4; 10; AT-F6 (answer 2, detached, inside the budget; the body past its bound: `test_a_bind_body_past_its_bound_ends_with_the_bounds_code_and_leaves_no_process[<case>]`); AT-F7 (the 3.65 s cap under a declaration); AT-F8 (the mode query's hang); AT-LR4 (an import's `not_after`: `test_an_import_carries_not_after_of_its_330_s_timer_plus_2_s`) |
| `rename-lane` commits the transaction before moving the handoff | 1 (`test_rename_lane_commits_the_transaction_then_a_rerun_makes_only_the_move`, and the `rename-lane` case) |
| A claim's comment cites the register sequence | 4 (`test_claim_prints_its_register_seq`: the printed `register seq <n>` is the entry's seq, and the claim comment, through a fake `gh`, cites the same `<n>`); 1 (part G: the mirror commit on origin whose `Register-Seq:` trailer is the entry's seq, found by `git log`'s trailer format, which is the scenario "Finding the commit": `test_the_mirror_on_origin_equals_a_full_render_after_each_write[<verb>]`). The manual's text: **not covered** |
| The journal records each write † ‡ | 1; 5; 12 (`lanes-index journal --write-id`); AT-F10 (`args_sha256`); AT-LR4 (the operator flag); AT-F1 (exactly two operator entries); AT-F2; AT-SET (no kind commits in a state its row refuses, and an operator entry writing outside `lanes/STORE` is refused with LR004); AT-F18; AT-LR009 (`test_act_id_is_the_acts_id_on_a_write_and_null_on_every_other_entry`, `test_the_act_id_checks_refuse_an_act_id_on_another_kind_and_a_malformed_one`, `test_each_entry_stores_the_keys_its_lines_derive[<kind>]`, `test_an_import_a_cutover_and_a_rollback_store_only_the_star_key`, `test_a_stray_import_stores_its_keys_and_a_stray_revert_none`, `test_an_aborted_cutover_and_a_reimport_leave_no_scopes_at_a_deleted_seq`) |
| UTC comes from the database's clock in whole seconds | 1 (with `test_a_caller_composed_utc_and_log_paused_utc_stay_as_written`); 7 (the `WORD` lines whose UTC carries milliseconds, kept as they are: `test_word_lines_with_milliseconds_import_verbatim_and_flagged`) |
| The renderer commits one mirror commit per sequence † ‡ | 5 (the trailer in each message shape; 64 seqs a pass); 1 (part G, the trailer in the last paragraph); AT-F2 (operator entries skipped); 5 (no `Register-Seq` found: nothing pushed, and why recorded) |
| The renderer commits in the writing lane's checkout under S3's fetch | 5; C8 |
| The mirror is write-behind | 5 (held and failed renders) |
| A renderer that loses a push race discards and re-renders | 5 |
| The renderer leaves nothing on a lane's HEAD | 5 (the kill); 1 (part G) |
| One render at a time per checkout, and no fork waits ‡ | 5 (the `flock`, and [L4-4]'s kill); 5 (a write that commits as the render lets go); 14 (the writer's fork of the render, which it does not wait for: `test_a_primary_write_forks_render_checkout_on_the_lane_checkout_detached[<case>]`) |
| The renderer does not inherit `LANES_NO_FETCH=1` | C8 |
| A pending hand edit has a way out on `mode=primary` | 2 (`test_a_pending_hand_edit_names_the_file_and_a_recovery_that_works`, with the shim's log empty) |
| Commits that touch none of the rendered paths are part of the tip | 6 (the negative cases) |
| A commit the renderer did not make to a rendered path stops it | 6; AT-MIR (the row never gates alone) |
| A `Register-Seq` above the journal's head stops the renderer ¶ | 6 (`test_a_register_seq_above_the_head_stops_the_renderer_at_primary_and_the_revert_resumes_it`: a divergence recorded at the commit that carries it, and the revert of that commit); 5 (`test_a_register_seq_above_the_head_with_the_switch_at_files_is_no_divergence_and_renders_nothing[<case>]`: the rule holds only while the switch is at `primary`) |
| Each way out of a divergence has a tool | 6; AT-MIR (only the two ways out clear the row); AT-F1 (needed before a rollback) |
| `status` and `reconcile` prove the mirror | 5; 6; AT-MIR |
| The post-write nudge does not re-index the mirror into its own source | 1 (`test_on_primary_a_write_does_not_nudge_a_register_sync` and `test_sync_source_register_does_nothing_on_primary_and_says_so`, FP) |
| Readers read the database and fail closed ‡ ¶ | C5 (`register-row-local` on primary: `test_register_row_local_reads_the_database_on_primary_and_is_69_never_8`); 10; D9; AT-F5 (an act's deciding reads hold at its commit, the refusal naming the scope, the seq, the lane and the verb; an act's own earlier write never refuses its later one); AT-7 (`test_line_scopes_agrees_with_build_records_rules_over_every_line_of_the_imported_register`: what each entry touched is derived from its lines); a base read before an after-loss cutover, an act begun off primary whose write runs on primary, and a line that derives a key that is not well formed: AT-F5 (`test_act_begin_on_primary_prints_id_seq_and_generation[<case>]`, `test_the_client_refuses_a_bad_act_with_2_before_any_call[<case>]`, `test_the_client_refuses_an_acts_stale_base_with_2_at_the_snapshot[<case>]`, `test_the_commits_act_backstops_are_lr007_act_generation_and_lr004[<case>]`, `test_act_begin_rebase_refuses_with_2[<case>]`, `test_canon_lane_walk_prints_each_hop_with_canon_lanes_exit_code[<case>]`, `test_an_act_that_decided_on_canon_lane_is_refused_by_a_row_added_on_its_walk`, and for LR010 `test_a_line_deriving_a_key_that_is_not_a_scope_exits_2_on_lr010_once[<case>]`); AT-LR009 (the act at the engine: `test_a_malformed_act_is_refused_lr004_before_the_head_lock[<case>]`, `test_an_entry_outside_any_act_refuses_an_acts_write_lr009`, `test_a_star_key_after_the_base_is_reported_as_the_first_listed_scope`, `test_an_empty_expectation_records_act_id_and_checks_nothing`, `test_lr009_names_the_first_changed_scope_its_seq_lane_and_verb`, `test_lr002_comes_before_lr009_after_a_rollback`); AT-LR010 (the derived keys: `test_line_scopes_derives_each_scope_by_its_rule[<case>]`, `test_doc_line_scopes_adds_objects_from_the_structured_rows[<case>]`, `test_entry_new_keys_reads_the_new_side_and_every_holder`, `test_scope_fault_shows_each_white_space_character[<code point>]`, `test_scope_checked_raises_lr010_naming_the_path_the_line_and_the_key`, `test_the_journal_scopes_check_takes_a_no_break_space`) |
| Callers' own reads of the register go through verbs | C5 (`lane-worktrees`' three reads, and the restart skill's `lread`: `test_the_other_callers_arms_pass_the_code_or_refuse_as_lane_4s_table_gives[skill-restart-70-<code>]`) |
| Reads that decide nothing may read the mirror | C5 (`test_a_caller_that_acts_on_a_listings_answer_refuses_69_where_the_store_is_unreachable[<caller>]`); 11 (`test_the_read_mirror_variable_lowers_only_window_lane_and_swapped_to_a_mirror_read[<case>]`) |
| A reader says which store answered | C5 (the `read: mirror` line; `--index` on primary) |
| `managed-projection` answers from the database and never says `legacy` for an outage | C5 (`test_managed_projection_answers_0_1_8_from_sql_as_files_mode_does[<answer>]`, `test_managed_projection_with_the_database_unreachable_exits_1_never_legacy`) |
| `lane-start` with the database unreachable ‡ | 11; C5 |
| The busy and in-doubt codes refuse the launch at `lane-start`'s seven default read arms ¶ | C5 (the seven read arms, lane 4's row 23: `test_75_and_3_refuse_the_launch_at_each_lane_start_arm`) |
| `lane-handoff` and the handoff skill with the database unreachable | 11 |
| The managed seam stays the ledger's | 1 (the storage-layer entry); the existing verbatim-block test |
| `lanes-index import --at` proves the render before it loads | 7 |
| The import takes origin only and keeps the unreadable lines verbatim | 7 (and the warning's file name: `test_the_log_path_sidecar_names_the_same_log_file_in_unreadable_warnings[<case>]`) |
| The cutover is one guarded sequence † | 7 (part G); 13; AT-F2 (the abort, the unconfirmed push); AT-F4 (the order); AT-SET (a write before the push is confirmed); AT-D22 (the restore form's abort) |
| The cutover takes its database label from the workstation's configuration ¶ | AT-D22 (`test_an_after_loss_cutover_from_a_host_still_on_the_live_database_is_refused_with_2_and_writes_nothing`: a host still pointed at the live database; `test_the_restore_route_commits_under_the_replacements_register_label_and_check_10_fails_until_it_does`: the restore route); AT-P8 (`test_the_readiness_check_10_compares_register_label_with_the_switch_label[<case>]`: a label that is not the switch's at `primary`); AT-F18 (`test_the_after_loss_cutovers_label_rules_in_the_transaction[<case>]`, the engine half); AT-LRLABEL (`cutover`'s two refusals, a file with no `register_label=`: `test_cutover_without_register_label_is_refused_2_before_it_connects`, and a label that differs from the switch's at `files`: `test_cutover_at_files_with_a_label_not_the_switchs_is_refused_2_before_it_sends[<case>]`; and a first cutover's store: `test_a_first_cutovers_store_names_the_register_label`) |
| Rollback returns the estate to the files † ¶ | 8 (a); 13; AT-SET; AT-F1; AT-F4 (`--hold-marker`); AT-F1 (LR012 over a recorded divergence: `test_a_rollback_entry_over_a_recorded_divergence_is_lr012[<case>]`, `test_rollback_exits_2_on_lr012_naming_the_divergence_and_both_ways_out`); AT-8 (a divergence recorded as the rollback starts: `test_a_rollback_goes_back_to_its_check_past_a_last_error_and_refuses_a_divergence_recorded_meanwhile[<case>]`; a stray on origin not yet recorded: `test_a_rollback_finds_a_stray_not_yet_recorded_records_it_and_exits_2[<case>]`; the retry through its check: `test_a_rollback_retries_a_lost_turn_on_a_fresh_k_through_its_check`, `test_a_rollback_goes_back_to_its_check_on_each_lost_turn_code[<code>]`); D9 (`test_a_rollback_that_keeps_losing_its_turn_exits_75_at_the_deadline_with_nothing_written`) |
| A rollback needs the database | 8 (b); section 4.2; D22; AT-F18 (the disaster path); AT-LRMPG (the restore from the dump and from the provider's own copy: `test_a_restore_from_the_dump_equals_its_source_with_new_passwords`, `test_a_restore_from_the_providers_copy_keeps_the_roles_and_takes_new_passwords`) |
| A writer the rollback missed cannot write | 8 (c); section 4.2; AT-SET (an older snapshot fails 40001, then 69) |
| The operator's bootstrap is idempotent and holds no secret † ◊ | P8 (a connection string refused with 2; `PGSERVICE` only in the environment); AT-VER; AT-LRMPG (the bootstrap without a superuser: `test_bootstrap_without_a_superuser_runs_twice_with_one_catalog`, `test_bootstrap_refuses_a_database_its_admin_does_not_own`; each engineer's two logins and the dump job's, made, rotated and revoked by `lanes-index login`, each with its group's floors, and the delta's "An engineer's logins" scenario: `test_two_writer_logins_write_alike_and_the_log_names_each`, `test_a_revoked_login_is_refused_69_and_writes_nothing[<case>]`, `test_an_open_session_gets_42501_between_the_revokes_steps_then_57p01`, `test_a_write_cut_by_a_revoke_mid_commit_is_in_doubt_and_ends_3_with_nothing_written`, `test_login_add_makes_an_engineers_two_logins_with_their_floors_and_prints_their_lines_once`, `test_login_add_run_again_puts_back_a_lost_membership_and_changes_nothing_else`, `test_login_rotate_keeps_an_open_session_and_refuses_the_old_password`, `test_login_refuses_a_bad_name_a_connection_string_and_a_pipe[<case>]`, `test_a_member_made_without_the_login_step_carries_no_floor_and_fails_ready_check_12`) |
| The readiness check proves an environment before the cutover † ◊ | P8 (every check, the schema's window among them; the endpoint's address count, check 13: `test_the_readiness_check_13_compares_each_hosts_address_count_with_register_addresses[<case>]`); 14; AT-VER; AT-LRMPG (checks 11 to 12: `test_ready_check_11_needs_tls_and_the_primary[<case>]`, `test_ready_check_12_passes_the_logins_the_step_made`, `test_a_member_made_without_the_login_step_carries_no_floor_and_fails_ready_check_12`; the reader's `target_session_attrs`: `test_reader_url_takes_target_session_attrs_primary_not_read_write[<case>]`) |
| The `mode=primary` flip is a command run once per host † | P8 |
| Claim order is commit order | 4; C10; D19; AT-SPL; AT-LR5; section 4.1 |
| `HOLD` and `LANDING` are ordered by commit sequence † ¶ ◊ | 4 (b); 4 (f) (a taking line that names no repository: `test_a_hold_line_that_names_no_repository_is_refused_with_2`); a taking line with no readable UTC, refused with 2 on primary naming the rule, `take-no-utc` at the commit (U3), and loaded as it stands by an import: AT-F13 (`test_on_primary_a_take_with_no_readable_utc_is_refused_before_the_lock_and_written_off_primary[<case>]`), AT-LR5 (`test_on_primary_a_take_with_no_readable_utc_is_lr005_take_no_utc_and_writes_nothing[<case>]`, `test_a_refresh_or_a_line_of_no_kind_with_no_utc_commits_and_a_take_with_none_loads_at_files[<case>]`) and AT-7 (`test_import_loads_takes_with_no_readable_utc_and_a_holderless_landing_expired_as_they_stand[<case>]`); AT-LR5 (one repository under two spellings, `HOLD` or `LANDING`, either order; each rule's DETAIL token: `test_each_claim_and_main_rule_lr005_carries_its_detail_token[<case>]`) |
| Which lines take and release `main` ◊ | 4 (`test_each_line_kind_takes_or_releases_main_as_q17_b_says`; case (f)); a `LANDING` released only by its lane's `LANDED` or by a `LANDING EXPIRED` that names it, and a stale one that no line releases still taking `main`, which is the scenario "A `LANDING` stays until it is released": AT-LR5's F13 cases (`test_a_landing_expired_for_a_stale_landing_commits_and_frees_main_in_main_slots`, `test_a_landing_expired_closes_only_the_holders_landings_for_that_pr_and_repository`, `test_the_holder_may_write_its_own_landing_expired`) and AT-F13 (`test_a_landing_expired_on_primary_frees_main_and_who_landing_until_a_rollback_to_files`) |
| A `LANDING EXPIRED` releases one stale `LANDING`, or writes nothing ◊ | AT-LR5's F13 cases at the engine, as direct `commit_write` calls (`test_a_landing_expired_for_a_stale_landing_commits_and_frees_main_in_main_slots`, `test_a_landing_expired_that_names_no_stale_landing_is_lr005_and_writes_nothing[<case>]`, `test_the_for_lane_checks_refuse_a_for_lane_off_a_landing_expired_and_a_landing_expired_that_closes_no_landing[<case>]`); the three reasons of LR005, each named by its DETAIL token, `expired-none`, `expired-closed` and `expired-young` (U7): AT-LR5 (the cases of `test_a_landing_expired_that_names_no_stale_landing_is_lr005_and_writes_nothing[<case>]`, one or more for each token, among them the `posted-again` cases and `fresh`, which reads the database's clock) and AT-F13 (`test_the_writer_reads_lr005s_reason_from_its_detail_token_never_its_message[<case>]`, `test_the_landing_expired_form_sends_nothing_where_no_landing_is_stale_at_the_snapshot[<case>]`); the holder's own line and the closes it makes: AT-LR5 (`test_a_landing_expired_closes_only_the_holders_landings_for_that_pr_and_repository`, `test_the_holder_may_write_its_own_landing_expired`); and an import's line in the shape that names no holder: AT-7 (the `expired-no-holder` case of `test_import_loads_takes_with_no_readable_utc_and_a_holderless_landing_expired_as_they_stand[<case>]`) and AT-F13 (`test_rule6_with_the_expired_branch_agrees_with_main_slots_on_imported_history[<case>]`) |
| A stale `LANDING` is released by `append-line --landing-expired`, on Brett's word ◊ | AT-F13: the release on Brett's word, with the quote, where and when he gave it (`test_the_landing_expired_form_composes_its_line_from_the_open_landing[<case>]`, `test_the_landing_expired_form_prints_its_seq_once_and_an_lr005_with_its_detail_token[<case>]`, `test_a_landing_expired_on_primary_frees_main_and_who_landing_until_a_rollback_to_files`); no word, no write (`test_the_landing_expired_form_refuses_with_2_before_the_lock_without_the_word[<case>]`); the kind typed by hand, refused on primary (`test_on_primary_the_landing_expired_kind_typed_by_hand_is_refused_naming_the_form[<case>]`); nothing stale to release, refused by the form (`test_the_landing_expired_form_sends_nothing_where_no_landing_is_stale_at_the_snapshot[<case>]`) and by the commit's LR005 (`test_the_writer_reads_lr005s_reason_from_its_detail_token_never_its_message[<case>]`); a release in doubt, where the verb's exit code is 3: the `in-doubt` case of the prints-its-seq test; files mode, where `who --landing` lists the `LANDING` as before, and after a rollback, marked STALE (`test_in_files_mode_the_landing_expired_form_exits_2_and_changes_nothing[<case>]`, `test_who_landing_on_primary_drops_only_the_released_landing[<case>]`); and the form's parse, race and agreement cases (`test_parse_register_line_reads_the_landing_expired_fields_before_the_word_clause`, `test_a_landed_between_the_forms_snapshot_and_commit_leaves_the_retry_nothing_to_release`, `test_off_primary_rule6_awk_is_the_pin_and_the_flag_cannot_be_inherited[<case>]`, `test_rule6_primary_on_is_called_only_where_lane_4_places_it_and_who_landing_stays_pinned`). The word itself is not verified, by design (section 6.3) |
| The three roles ◊ | R (the matrix, a login in a group role and the groups that cannot log in: `test_a_login_in_a_group_role_keeps_the_floors_of_its_role[<case>]`, `test_the_writer_and_reader_groups_cannot_log_in[<case>]`); AT-MIR (`record_mirror_state`'s EXECUTE); AT-LRMPG (each engineer's two logins and a revoked login: `test_two_writer_logins_write_alike_and_the_log_names_each`, `test_a_revoked_login_is_refused_69_and_writes_nothing[<case>]`, `test_an_open_session_gets_42501_between_the_revokes_steps_then_57p01`, `test_the_reader_login_dumps_both_schemas_and_still_cannot_write`) |
| The REGISTER-WRITER secret's reach is stated plainly | 10 (the scenario "No credential in an inherited environment": `test_no_session_environment_carries_a_database_url_or_password`); the manual's statement: **not covered**, documentation, which no test reads |
| Agreement tests run on a real engine in CI | section 3.3's five tests; the fixture |
| The cost of a write is measured and recorded | 9 (`test_each_paths_connections_and_queries_are_counted[<path>]`, M13); section 5 (`test_a_tls_and_scram_connect_on_loopback_is_timed_and_verified`, M12's TLS cluster); AT-4 (g) (the snapshot's image cache, `register-images/`: `test_the_image_cache_stays_content_addressed_under_the_burst_and_a_corrupt_object_is_refetched`; its cost, test 9's: `test_the_image_cache_check_and_a_miss_are_timed_per_attempt`) |
| A lane's day on `mode=primary` is tested end to end | 11 |
| The drill covers the outage and the way back | 13 |
| The manual says what changes for a lane ¶ | **not covered**: documentation (exit 3's text is among the items the manual lists) |
| The runbook for acts 3 and 4 and the rollback | 13 is its drill, with AT-F4's order. The runbook's text: **not covered** |

`specs/register-service/spec.md`, 19 requirements:

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
| The guard falls back to the database, never the mirror ◊ | 10; AT-F7 (the declared-outage exception); the direct read holds its engineer's reader login's credential from the 0600 `lanes-index.conf` and its password file, and no URL or password enters the hook's environment: 10 (`test_the_direct_read_connects_as_its_engineers_reader_login`) |
| A service that accepts and never answers counts as unavailable | 10; D9 (the service's 300 ms wait) |
| The 5 s is one budget, spent once † ‡ ¶ ◊ | section 4.4; D9 (the guard's row); 10; AT-F6 (answer 2, detached; a bind that left no outcome: `test_a_reused_bind_pid_gives_no_outcome_with_no_session_test`, `test_the_stale_row_copies_a_dead_outers_pending_call_and_the_next_2_looks_it_up`; a bind whose outer died: `test_a_dead_bind_outer_answers_in_progress_while_its_session_lives_then_no_outcome`); AT-F7; a guard that starts too late, which refuses 2: under a declaration, AT-F7 (`test_under_a_declaration_a_direct_read_left_under_0_5_s_does_not_start[<case>]`, with its control, `test_without_a_declaration_the_same_age_still_starts_the_direct_read`); with none, at or above the direct read's cap: AT-10 (`test_a_guard_that_starts_too_late_refuses_2_without_a_read`); the direct read's connect timeout, the workstation's own `c`, which may exceed its share where the read's own timer ends the read within it: `test_a_connect_timeout_above_the_direct_reads_share_is_cut_at_the_parse_start_line[<case>]`, and a whole file that the start line cuts and the detached fetch finishes: `test_a_whole_file_fetch_the_start_line_cuts_fills_the_image_cache_for_the_next_prompt` |
| The hook's environment carries no credential | 10 |
| The guard fails closed during a database outage | P3; 10 |
| A declared outage lets the guard answer from the mirror † ¶ | D20 (with the rollback's start: `test_a_rollbacks_start_declares_the_outage[<mode>]`); 10; AT-F7; AT-F11 (no `Register-Seq` within the bound; a ref behind a rollback's head: `test_the_guard_refuses_a_ref_behind_a_rollbacks_head`); AT-D20 (a rollback's declaration counts only where the guard's own read finds the switch at `files`, so not once the switch is primary again, and not in a partition: `test_a_rollbacks_declaration_counts_only_where_the_guards_read_finds_the_switch_at_files[<case>]`; a rollback rerun's declaration: `test_a_rollback_rerun_declares_before_it_goes_on[<case>]`; the floor is AT-F7's) |
| `outage declare` during a rollback records the rollback's head ¶ | D20 (`test_outage_declare_is_refused_where_the_probe_reads_primary[<form>]`; Brett's word during a rollback: `test_outage_declare_on_a_persons_word_during_a_rollback_is_refused_and_prints_the_rollback_form`; a wrong `<k>` and a switch it cannot read: `test_outage_declare_rollback_is_refused_for_a_wrong_k_a_missing_mirror_seq_or_a_switch_it_cannot_read[<case>]`); D9 (the probe's row: `test_the_outage_declare_probe_ends_inside_its_bound_and_the_declaration_follows_the_rule[<fault>-<form>]`) |
| The service answers the guard's read fast, and the cost is recorded | 10 (M5 and M10's four parts: `test_the_guard_answer_time_is_recorded_beside_todays_direct_guard`; M5b: `test_todays_direct_guard_is_timed_in_its_four_parts`; M10's parse: `test_the_guards_register_parse_is_timed_on_a_real_sized_register_beside_todays_six_reads_through_git`); section 5 (M13); the 50 ms is the suite's fixture register's, and against the Azure server the service's answer is over 50 ms above an RTT of about 40 ms, which act 3 records beside Q29's figure (section 5.6; lane 5's netem run, comment 6722703424) |
| The socket API is documented | **not covered**: documentation |
| The cutover waits for the service only if the guard needs it | **not covered** by a test: Brett decides, from act 3's figure, the warm p95 of the guard without the service, against the limit he named, 1 s (comment 6722025863; section 5.6, where lane 5's netem run, comment 6722703424, puts the direct guard over 1 s above an RTT of about 57 to 63 ms) |

Nine requirements have no test, or only a partial one:
- in the register spec: the server-side gate's ruleset (modelled only); the
  `LANES.md` link (`wip init` is tested); the claim comment's manual text (its
  citation is test 4's); the secret's reach (its scenario is test 10's, and
  the manual's statement has no test); the manual; the runbook's text (its
  drill is test 13);
- in the service spec: the toolchain floor on Python 3.9 (partial); the socket
  API's documentation; the cutover's wait for the service.

Six of them are documentation or a decision of Brett's, which a test does not
check. The service's start on demand is test 10's, since SVC-4 says how the
service starts.

### 6.3 Gaps

- **The ruleset** (Q21(b), D23) is GitHub's, on the render set and
  `lanes/STORE`: the scope Brett chose on 2026-11-08, "Render set + STORE"
  (lane 3, comment 6721583757), and not `lanes/**`. The suite reaches no
  network, so only a pre-receive hook models it (test 6, AT-F4), and the
  model guards exactly those paths. Nobody has checked whether the
  organisation's plan supports the ruleset, or can name the render set
  exactly.
- **The `LANES.md` link** that `link-estates` places (Q14's second half). No
  test.
- **Three frame points with no test of their own,** none asked for by the
  frame: D10's "exit 9's re-check stays local" and "local sidecar reads keep
  1 and 10", and D12's deciding reads, unchanged by `LANES_NO_FETCH`. D16's
  "the row never gates alone" is AT-MIR's.
- **TCP across hosts.** Every case runs on one host. The cluster listens on
  its Unix socket only, and only the timeout stand-ins use the loopback
  address. The probes did not measure across hosts either.
  Brett's database is Azure Database for PostgreSQL Flexible Server 16, a
  managed cloud PostgreSQL on a separate server (Q22, comments 6722025863
  and 6722593397), so every production call crosses the internet over TCP
  with TLS and SCRAM. No test reaches such a host. Section 5.6 records lane
  5's netem run, models what that run did not measure, counts the round
  trips (M13) and times a TLS and SCRAM connect on loopback (M12), and act 3
  takes the production figures on a scratch database on the provider's
  server.
- **Which cases need TCP rather than the Unix socket** (the frame review's
  item routed to lane 2): only AT-D9's F1 and F2 stand-ins, section 4.4's
  cases 1, 2 and 4, and AT-F7's silent host, which reuses F2's relay. Each
  binds 127.0.0.1 on port 0. Every other case uses the cluster's socket.
  Section 5.6's TLS cluster (M12) listens on 127.0.0.1 at a free port too,
  and so would its delayed relay if lane 3 takes option B.
- **F13's `LANDING EXPIRED`.** Brett's ruling of 2026-11-08 is "Adopt, on my
  word" (lane 3, comment 6721583757), and W carries both halves: the
  requirement "A `LANDING EXPIRED` releases one stale `LANDING`, or writes
  nothing", lane 4's verb, `append-line --landing-expired` (E1 to E4), and
  lane 1's v3.1 refusals. The engine's half is tested in AT-LR5's F13 cases
  and the verb's in AT-F13. The tests lanes 1 and 4 named are U3's cases, a
  take with no readable UTC refused with 2 on primary and loaded as it
  stands in files mode (AT-LR5's
  `test_on_primary_a_take_with_no_readable_utc_is_lr005_take_no_utc_and_writes_nothing[<case>]`
  and
  `test_a_refresh_or_a_line_of_no_kind_with_no_utc_commits_and_a_take_with_none_loads_at_files[<case>]`,
  AT-F13's
  `test_on_primary_a_take_with_no_readable_utc_is_refused_before_the_lock_and_written_off_primary[<case>]`
  and AT-7's
  `test_import_loads_takes_with_no_readable_utc_and_a_holderless_landing_expired_as_they_stand[<case>]`);
  each LR005 reason by its DETAIL token,
  `expired-none`, `expired-closed`, `expired-young` and `take-no-utc`
  (`test_a_landing_expired_that_names_no_stale_landing_is_lr005_and_writes_nothing[<case>]`,
  `test_each_claim_and_main_rule_lr005_carries_its_detail_token[<case>]` and AT-F13's
  `test_the_writer_reads_lr005s_reason_from_its_detail_token_never_its_message[<case>]`);
  and lane 4's tests (b) and (e) of its texts v2.1, where (b) adds the stamp
  `2026-13-45T99:99Z` and a `HOLD (refresh)` that is not refused (AT-F13's
  `test_the_landing_expired_form_refuses_with_2_before_the_lock_without_the_word[<case>]`
  and
  `test_on_primary_a_take_with_no_readable_utc_is_refused_before_the_lock_and_written_off_primary[<case>]`),
  and (e) checks that `expired-young` is printed (AT-F13's
  `test_the_landing_expired_form_prints_its_seq_once_and_an_lr005_with_its_detail_token[<case>]`).
  A stray landing
  during a rollback has no journaled way out once the rollback's entry has
  set the switch to `files`; Brett accepted that as S5's remaining gap
  (comment 6721583757), and no test is written for it.
- **A dead holder's `HOLD` (U6).** No verb writes `HOLD EXPIRED` today, in
  files mode or in the register (lane 1, comment 6722618324), and releasing a
  dead holder's `HOLD` is outside Brett's ruling on `LANDING`s. Lane 3
  routes it as its own issue after #165 (comment 6722541787). No test.
- **Brett's word.** Neither the client nor the database can verify it, and
  the design claims no check: the line and its journal entry are the
  record. The tests assert that the line carries the quote, where he gave
  it and when, and never that he gave it.
- **The provider's own settings.** `max_connections`, the firewall rules,
  `require_secure_transport` and the provider's backups are the server's,
  and the suite reaches no network. No test reads them.
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
- **The shared database of act 3** (Q22: Azure Database for PostgreSQL
  Flexible Server 16, in opensoft's prod subscription). No test runs against
  it, by design (P840–841). The runbook's rehearsal into a scratch schema
  covers it, and section 5.6's act-3 rows are taken on a scratch database on
  the provider's server.
- **The real register's bytes.** The fixture register is synthetic. R3's
  byte-identical import of the real register's 43 paths is the rehearsal's,
  not the suite's.
- **A cluster that will not start, with its binaries present,** was not
  driven through pytest (the fixture probe, d4).

## 7. Open points

**Settled.** The tests above are written to these answers:
- "Register busy" is 75, at a 30 s deadline (D7). Brett took Q7 (A″) with
  75, not "(A″) with 69" (round 2; lane 3, comment 6710653292).
- A write in doubt ends with no code but 0 until it has made F3's one
  lookup, `find_write`: 0 where the write is found, the code its last
  attempt earned where it is absent with the lock taken, 2 where LR007
  refuses it, and 3, naming the id, where no try that starts within the 10 s
  reconnect window takes the lock or LR011 refuses the lookup. The window
  opens when the writer's wait for `not_after` ends and each try runs to its
  own timer, `T_L`, 9 s at `c` = 2, so a whole write ends by about 62.5 +
  3`N_a`·`c` s, 68.5 s at `c` = 2 with one address, and not 10 s after
  the snapshot (D8, D9; the phase plan's "The in-doubt rule"). A rerun
  reuses the id through `LANES_WRITE_ID` and `LANES_WRITE_GENERATION` or the
  pending record. Brett accepted exit 3 (comment 6721370130). Where the
  managed cloud PostgreSQL's link changes a timer or a healthy call's time,
  the figures are lane 4's (comment 6722143023; the phase plan's "Over a
  remote database").
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
  in S1's step 1; and lane 4's rows 6, 8, 10, 12, 13, 15, 16, 20, 24 and 37.
  Rows 18, 22 and 40 went to Brett, who settled them on 2026-11-08
  (comment 6721583757).
- The commit call runs with `VERBOSITY=verbose` and `SHOW_CONTEXT=never`, so
  `psql`'s first line on stderr is `ERROR:  <SQLSTATE>: <message>`, and
  every code, LR009's message included, is read from that one line. A call
  the design keeps at `VERBOSITY=sqlstate`, as the reader's and the
  service's child are, keeps it (lane 3, comment 6711564365). The design
  names no `VERBOSITY` for its other calls, though the lookup, `import` and
  `cutover` must show a server's message or LR007's `DETAIL:` token, so
  section 3.2 asserts none for them until lane 3 names one (open point 29).
- Brett accepted the design's departures A, B and C (comment 6721370130):
  files mode's two changes, the guard's detached bind and `start-record`, in
  every mode; D8's exit 3; and the pushes of `lanes/STORE` that `cutover`
  and `rollback` make themselves.
- Brett accepted the ten departures not yet routed, each as recommended
  (comment 6721583757): D5 per verb and its three conditions,
  `rename-lane`'s tail, macOS's gap, D18, D1, D25, Q21(b)'s scope (the
  render set and `lanes/STORE`), the stale-`LANDING` release on his word,
  whose engine half AT-LR5 tests and whose verb W carries, with AT-F13's
  tests, and the stray landing's gap.
- Q22's host is Azure Database for PostgreSQL Flexible Server, version 16,
  in opensoft's prod subscription, expected in Central US, with public
  access and one firewall rule per workstation address, TLS `verify-full`,
  and two logins per engineer. A daily `pg_dump` runs there as a job into a
  storage container with soft delete and 30-day retention, beside the
  mirror and the provider's own backups, and the limit before the disaster
  path is 4 hours. Q29's limit is 1 s, judged on the warm p95 of the guard
  without the service. Q28's dates: slices 1a and 1b as draft PRs by
  2026-11-10 and ready by 2026-11-12, and the other four open by
  2026-11-12 and ready by 2026-11-17, for his word (comments 6722025863,
  6722593397 and 6723413756, and #198 comment 6722244541).
- A `LANDING EXPIRED` is written only by `append-line --landing-expired`,
  on Brett's word, and the commit refuses it with LR005 unless it names one
  open, stale `LANDING`, naming the reason in a DETAIL token (W; lane 3's
  U7, comment 6722541787). On primary a taking line with no readable UTC is
  refused with 2 (U3).
- Each engineer's login carries its group's floors itself, since a role's
  settings reach no member, and `ready`'s check 12 reads them (W).
- D7 stays 30 s over the remote database (W; the phase plan's "Over a
  remote database").

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
   FP tests exercise the password-file path, and the production host is
   reached over TLS with SCRAM (Q22). Section 5.6's TLS cluster answers it:
   one RE test,
   `test_a_tls_and_scram_connect_on_loopback_is_timed_and_verified`, runs a
   database under `hostssl` with `scram-sha-256` and a client with
   `sslmode=verify-full`, the mode W fixes for every register URL (the
   design's "TLS and the endpoint", in §1.4) and that `ready`'s check 11
   proves. Lane 3 may still say it is not wanted.
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
   job, and the measurement cases of AT-1, AT-5, AT-7, AT-F6 and AT-F11 (M6,
   M9 and M11) are long rows of the same kind, whose cost nobody has
   estimated. M12 and M13 add two clusters of about 0.9 s each and one more run of
   each path (section 5.6), and the delayed loopback, if lane 3 takes its
   option A or B, about 4 to 7 min more. AT-D9c's (b) and (d), about 1.5 min
   with (a) and 2 to 4 min, are long rows of the same kind, and so are
   AT-10's idle check, which waits the service's 60 s (open point 35), and
   AT-4's case (h), which waits D7's 30 s default. Should the
   long rows run on every
   trigger, or only on the `schedule` trigger `tests.yml` already has? And
   may a test read the trigger (`GITHUB_EVENT_NAME`) to choose?
10. **Under Q4(b)(iv) only,** where "the mirror was last shown current" is
    recorded. Settled: Brett took Q4(b) (iii), so no test needs the record
    (round 2; lane 3, comment 6710653292).
11. **"An address whose connect never completes"** (P823) cannot be made on
    loopback without root, since the probes' attempt to drop SYNs failed.
    The never-speaking listener stands for it. An off-host documentation
    address would break the root `AGENTS.md`'s "reaches no network". Under
    option A of section 5.6's delayed loopback, a step with root makes one
    lost SYN possible on loopback, which the suite itself does not.
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
15. **The disaster path's backup.** The frame restores a backup "only where
    it is newer than the mirror". Brett's Q22 answer names the backup: a
    daily `pg_dump` beside the mirror, run as a job in Azure into a storage
    container with soft delete and 30-day retention (comments 6722025863 and
    6722593397). Answered by W: `operations.md` §2.2, act 3's step 1
    ("The daily `pg_dump`"), reads `b` from the dump alone, by `pg_restore
    --data-only` with `--schema=lanes_register --table=register_head
    --file=-`, and §2.3's disaster path restores with `pg_restore
    --exit-on-error`. AT-D22 reads `b` that way. `pg_dump` and `pg_restore`
    are not among the fixture's four required tools, so AT-D22 and AT-LRMPG
    take them from the directory the fixture chose and check for them
    themselves.
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
       spelled the other way fails closed with LR005 (AT-LR5's
       [other-spelling], under `expired-none`; section 6.3).
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
20. **Test 3's G form.** Still open at W. Lane 4's row 8, as lane 3 ruled it
    (comment 6710770304: "agreement test 3's unit form ships in phase 2"),
    leaves the agreement form, with the marker pushed by `cutover`, in phase
    G, and the phase plan's "Agreement tests 3, 5 and 6" gives it no oracle.
    AT-3 has no case for it. Lane 3 says whether one is wanted, and what it
    asserts.
21. **Where act 3's rows are recorded.** The PR body comes before act 3, and
    Q29's decision cites the rows (section 5.6). On #165, in the runbook's
    act-3 record (`operations.md`), or in the cutover's checklist? W adds that
    each workstation that joins later records its own Q29 figure as it joins
    (SVC-9), so the rows are recorded more than once.
22. **Which statistic Q29's 1 s is read against.** Settled by W: the warm
    p95 of the guard without the service, with the cold record walk recorded
    beside it and not judged against the limit (lane 3's ruling on lane 2's
    question 2, in the design's "Open questions", "Values Brett named on
    2026-11-08"; lane 5's suggestion, comment 6722703424). Section 5.6
    records both figures.
23. **The delayed loopback** (section 5.6): option A, B or C. Lane 5's netem
    run already times the shapes, and the options reuse them. A needs root
    and a `sch_netem` nobody has checked on GitHub's runner. B needs no
    root, and models no slow start or loss. C relies on M13's counts, lane
    5's run and act 3's rows. AT-D9c (d) and AT-F6 (21) wait on it: they name
    the delay and TLS and run through the loopback lane 3 picks (lane 2's
    question 3, #165 6722518182, as lane 4 answers it), and do not run until
    it picks.
24. **M13: record the counts, or assert a ceiling per path?** A count does
    not move with load. If one is asserted, whose ceilings: lane 4's bounds,
    or numbers the design names? AT-D9c (a) already asserts one Query for
    each of the writer's snapshot, commit and lookup, so those three counts
    are asserted; the question stays for the other paths.
25. **The per-attempt stamps** test 9 should record, "Python start to
    `main`'s first line" and "the scratch tree ready". The design names no
    seam for them. Lane 2 proposes stamps in a test copy of `lanes-index`,
    made as open point 3's copies are, in place of a production variable; or
    the design names a seam.
26. **A session that exits mid-bind** (the phase plan's "Answer 2's detached
    bind"): before `lane-start`'s check the bind refuses, and after it the
    bind completes. It is not among AT-F6's twenty-two cases, and no test covers
    it. It would be AT-F6's case (e). Lane 3 routes it, or says it is not
    wanted.
27. **`cutover --marker-first`.** (ii′)'s mode of the cutover
    (`operations.md` §2.2, "How (ii′) differs") pushes the marker before the
    import. Brett took Q4(a) (i), which is not (ii′), and no test exercises
    the flag; AT-5's `files` case pushes the marker commit by hand and needs
    only the renderer's side. If the build keeps the flag, lane 3 says
    whether it needs a case.
28. **LR011 under the lock, after a clock step-back.** The server's clock
    stepping back between the lookup's check and an orphan's step 9 is the
    one residual of the in-doubt rule, and no test can make a database clock
    step; lane 1 did not run it either. AT-F3 tests LR011 where `<n>` is
    missing or too far ahead, and AT-LR011 tests the engine's side of it,
    and nothing tests the residual. Lane 3 says whether to record it as
    untested, or to allow a stoppable cluster under a clock shim.
29. **`VERBOSITY` for the calls the design leaves unnamed.** The design
    gives the commit call `VERBOSITY=verbose` and the service's child
    `sqlstate`, and names none for the lookup, `import`, `cutover` or the
    rest. The lookup, `import` and `cutover` must show a server's message or
    LR007's `DETAIL:` token, which `sqlstate` hides, so section 3.2 asserts
    none until lane 3 says which each call runs with.
30. **The per-engineer logins' floors.** Settled by W. `ALTER ROLE ... SET`
    binds the role it names and not its members, so the design's login step
    (§1.4) makes `lanes_w_<engineer>` and `lanes_r_<engineer>` with their
    group's floors set on each login, the delta's bootstrap requirement says
    so ("set on each login its group's floors, since a role's settings reach
    no member"), and `ready`'s check 12 reads them. AT-R's login test and
    AT-LRMPG's reader cases are written on it.
31. **The link to the server.** On primary each prompt's guard needs the
    workstation's network path to the server, and while that path is down it
    answers 2 until an outage is declared (the design's S5, "The link to the
    server"; SVC-12; lane 5, comment 6722703424). The design routes it to
    Brett on 2026-11-08 and has no answer yet. The tests assert today's
    rule: the guard refuses with 2 where the database is unreachable (AT-P3,
    AT-10) and answers from the mirror only under a declaration (AT-F7). A
    different ruling would change them.
32. **D7 per workstation.** The design keeps D7 at 30 s and says act 3's
    test 4 decides, at each joining workstation's own RTT: where its burst
    of ten gives 75s, lane 4 proposes to lane 3 a D7 of `max(30 s, 10 ×
    that workstation's attempt p95)`, with `T_write` and `bound_s`
    recomputed (the phase plan's "Over a remote database"). The tests set
    D7 by `LANES_REGISTER_DEADLINE_S` for their short cases, run test 4's
    burst with it unset, and assert the 30 s default once (AT-4 (h)). A
    per-workstation D7 would add a configured value for them to read.
33. **`max_connections` against the connections the register opens.** The
    design says the server's `max_connections` must hold one service
    connection per environment of every engineer, beside the direct reads and
    the writers (SVC-1; "Values Brett named on 2026-11-08"). No test can
    read the provider's setting, and M13 counts the connections of one path
    (section 5.6). Lane 3 says whether act 3's checklist records `SHOW
    max_connections` against that sum.
34. **The timing rule.** CI asserts times in three ways only:
    1. **The guard's 4.5 s** is asserted as stated.
    2. **Every other upper bound the design states** (D9's timers,
       `bound_s`, D7's 30 s, the try windows, AT-F6 case 1's `T0 + 1 s`,
       AT-F8's "under 3 s", and the like) is asserted at **twice its
       value**. This still catches a missing or wrong timer, because the
       faults the tests use would otherwise hang far longer. The measured
       time against the design's own bound is **recorded**, with its
       margin, in the test's output.
    3. **Lower bounds and orderings** the design states ("not before",
       "after", "in this order") are asserted as stated.

    Every other time is recorded, not asserted. Section 1.2, section 4's
    opening and section 7's open point 34 state this rule in the same words,
    and every test that asserts a time says which of the three it is. So the
    design's 0.1 s between the guard's register read and answer 2's exit 2
    (SVC-8; lane 4's AT-F6 items (1) and (21)), an upper bound it states, is
    asserted at 0.2 s and recorded against 0.1 s with its margin, and case
    1's `T0 + 1 s` at `T0 + 2 s` (AT-F6). Lane 3 says whether any bound must
    be held tighter than twice its value.
35. **The service's idle check.** SVC-6 has the service send `SELECT 1` on
    its held connection every 60 s, and restart `psql` in the background
    where that fails. The design gives the interval no knob, so AT-10's case
    waits the 60 s, a long row (open point 9). May the service take a test
    variable for the interval, as `LANES_REGISTER_DEADLINE_S` sets D7's?
    Lane 3 says.
36. **A takeover whose snapshot fails.** The phase plan's "The pending
    record" says that a rerun that sends no commit call of its own "still
    waits for the `not_after` of the run before it, because its snapshot
    fails or its verb refuses before `ready`", and that a takeover's own
    non-0 ends "wait past the largest `not_after`". Its "The in-doubt rule
    (F3, D8)" says that "a run with no snapshot clock calls at once", and
    LR011 refuses an `<n>` more than 3 s ahead, which is 3 with the record
    kept. So a takeover whose snapshot fails right after a kill exits 3 and
    does not wait. The tests follow the in-doubt rule: AT-F3's
    `[too-early]` expects that 3, and AT-D8's takeover waits only where its
    snapshot passes and its verb refuses before `ready`. Lane 3 says which
    text holds.
37. **The lookup's timer: 8 s or 9 s.** D8 and S1's "A write in doubt" give
    each lookup try "its own 8 s timer". D9's lookup row and the phase
    plan's "The in-doubt rule (F3, D8)" give `T_L`, `N_a`·`c` + 7 s, 9 s at
    `c` = 2, which holds the connect, the 6 s statement bound and 1 s. The
    tests use D9's 9 s (AT-D9, AT-D9c (b), AT-12, AT-F3). Lane 3 says which
    holds.
38. **A 69 with no generation.** The delta's "A bind that makes no row,
    refused or in doubt" has `lane-start` print a note "naming the write id
    and its generation" on a 69, the busy code or the in-doubt code. A
    `start-record` whose first snapshot fails exits 69 before any snapshot
    has given it the register's generation, and its pending record is
    written only after that snapshot. AT-11 asserts the generation on the
    75, the 3 and a 69 at the commit, and none on a 69 at the first
    snapshot. Lane 3 says what the note names there.
39. **`timeout` and a grandchild.** The design's "The detached bind" says a
    `TERM` the outer forwards goes by `timeout`, which "passes it to the
    body's group and sends `KILL` 10 s later". GNU `timeout` exits as soon as
    its direct child has died, and then sends no `KILL` (coreutils 9.4, run by
    lane 2 on 2026-11-08), so the claim holds only while the body itself
    survives `TERM`. A grandchild that ignores `TERM`, or one in a group of
    its own, outlives both the bound and the outer's outcome. Should the outer
    signal the body's group after `timeout` returns, as the path with no
    `timeout` does (`kill -TERM -- -<pid>`, then `KILL` 10 s later)? And how
    does a test put a stub body where the outer runs
    `"$RESOLVED" guard-bind --body`? AT-F6 assumes the `LANES_EDIT` stub. Lane
    3 says.
40. **The lookup's tries.** The phase plan's "The in-doubt rule (F3, D8)"
    starts each try "at least 1 s after the last". Is that counted from the
    last try's start or its end? May a try start at the window's 10 s mark,
    and is the window kept finer than a whole second? `T_L`, 9 s, is 1 s short
    of the window's 10 s, so counted from a try's end a try cut by its timer
    could never be made again inside the window. AT-F3's `[timer]` assumes
    start to start and a full 10 s, and exits 3 under either other reading.
    Lane 3 says.
41. **The admin service's database on a restore from a dump.** On a restore
    from a dump (`operations.md` §2.3 step 6, §2.4), `--roles-only` and
    `login add` run before the register's database exists, and `bootstrap`
    after it. The admin service reaches `psql` only as `PGSERVICE`, so the
    service names the database. Which database does it name at each step: two
    services, one service edited between the steps, or does `lanes-index`
    choose the maintenance database for the role-only steps? AT-LRMPG's
    restore test holds two admin services, the cluster's `postgres` database
    for the steps before and the register's for the steps after. Lane 3 says.
42. **A claim with no record beside it.** The phase plan's "The pending
    record" says that "a claim with no record beside it is a record being
    made, and its owner is waited for in the same way" as a record's owner.
    The 3 a run exits with after waiting out a live owner names the record's
    write id, and a record being made has no write id yet. What does the 3
    print? AT-F10's case records the text and asserts no wording. Lane 3 says.
43. **A pooler.** The design's §1.3, "The connection these bounds need",
    admits a session-mode pooler that passes `options=` on. Its §1.4, "TLS and
    the endpoint", says every URL names the primary's own endpoint, "never a
    pooler's endpoint or port". Which holds? The plan's sections 5 and 5.6
    follow §1.3, and no test names a pooler. Lane 3 says.

**Lane 2's questions of v3e.** v3e's NOTES put thirteen questions to lane 3,
and J2's fold left the twelve that stood open out of its gap list. Each is
marked against V:
1. **Test 3's G form.** Still open: open point 20.
2. **D9's mode-query row.** Answered by lane 3 (comment 6711028142): a 2 s
   client timer, no database timeouts, the query killed, files mode with a
   warning, and 69 on a SQL estate. D9 has the row, and AT-D9 and AT-F8
   assert it.
3. **The LR006 rewording.** Answered: S1's "A write in doubt" carries it and
   cites lane 2's LR006 probe (comment 6708917587).
4. **#347's scope.** Answered as to behaviour: the delta's "The bind fails"
   scenario keeps the offer and allows answer 2 again, up to the third time,
   and AT-F6 tests it. Which pull request carries it is lane 3's, and no
   test depends on that.
5. **Row 37.** Answered by the design's table of the bind child's variables,
   in lane 4's section ("Answer 2's detached bind"): `LANES_GIT_TIMEOUT` is
   45, beside the bind's bound, `bound_s`: 500 s at `c` = 2 and 530 s at `c`
   = 3, with one address (W). AT-F6's case 13 asserts it.
6. **Row 10's fork in phase W.** Answered yes: the phase plan says a stub
   test asserts the fork's argv. AT-14 has it,
   `test_a_primary_write_forks_render_checkout_on_the_lane_checkout_detached[<case>]`,
   phase W.
7. **Declarations in a row.** Answered in part. A rollback's start writes
   its own declaration, marked, with the head `k` its transaction found, so
   a rollback refused at its step 1 writes none (`operations.md` §2.3 and
   §2.5). Brett's word during a rollback is refused with 2, and the refusal
   prints the `--rollback` form (the delta's "`outage declare` during a
   rollback records the rollback's head"). A rollback's declaration counts
   only where the guard's own read finds the switch at `files`, so in a
   partition the guard refuses 2. Still open: whether a rollback replaces a
   declaration Brett made earlier.
8. **AT-F4 without the ruleset.** Still open. The delta leaves to Brett
   whether act 4 waits for an equal gate where the organisation's plan has
   no such ruleset (Q21(b)), and nobody has checked the plan.
9. **AT-1's rename rerun.** Answered by lane 4's row 24 (comment 6710770304)
   and D8: a rerun that finds its write id committed runs the verb's
   after-COMMIT tail, so `rename-lane`'s `lane_state_rename` still runs. The
   delta's "A rerun finds its write and runs the tail" is that case.
10. **Point 2's LR007 case.** Still open. AT-SET has no second database, and
    the plan asserts LR007 for a commit call carrying another generation,
    which stands for a pending record made against the lost one. Lane 3 has
    not said whether that is enough, and R3 did not object. AT-LR007 now
    makes the generation change at the engine, by a restore-form cutover in
    one database.
11. **M8.** Still open. M8 was dropped with Q4(b)(iv), and section 5.1's ids
    still skip it.
12. **"Round 1's image rebuild".** Settled in v3e: the plan says "Q1's 1A
    image rebuild".
13. **Row 18 and Q13 (a).** Answered: Brett took row 18 on 2026-11-08
    (comment 6721583757).

**What the tests still wait on.** No BRETT, L3-PENDING or L4-OPEN mark is
left: lane 3 settled the second and third, and Brett settled rows 18, 22
and 40. Commit W has landed, so nothing waits on its texts now. What waits
is:
- commit X, which moves the server to CNPG on the prod AKS cluster (comment
  6723925474): lane 3 routes the tests it changes after X, and v3f stays
  keyed to W;
- the expiry of any outage Brett declares, which no test reads;
- lane 3's answers to open points 20 to 43 that stay open, and to the
  questions above that stay open.
