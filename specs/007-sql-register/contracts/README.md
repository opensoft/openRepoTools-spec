# Contracts: feature 007

This directory holds the contract files that plan.md lists. Each is written
by the lane that owns it, and lands before the code that rests on it.

## `register-cli.md`: lane 1's contract draft 4.4, keyed to Y

[register-cli.md](register-cli.md) is lane 1's contract draft 4.4, keyed to
Y (`a6497d00`), copied here byte for byte by lane 3 at fix round B2 on
2026-11-09. It replaces draft 4.2, which lane 3 copied in at fix round B in
place of draft 4.1, which replaced draft 4 at assembly phase A.

- **Source:** lane 1's contract draft 4.4, `contracts-register-cli.v4.4.md`,
  posted on #165 at 6742751889.
- **sha256:**
  `8600078b571b36aee39a6346ad479084797ae91bbdb3a4748f8a9c7f4104a0be`, 963
  lines. The copy here has the same digest. Drafts 4.3 and 4.4 keep every
  line number of draft 4.2.
- **What draft 4.4 changes:** it is draft 4.3 with lane 4's log set in
  §3.6's C2 text (:811) and §8's O21 row (:947): each `lanes/log/<lane>.md`,
  and 2 for `lanes/log/README.md`; and its status and history lines.
- **What draft 4.3 changes** (#165 6742023901, `2cf9fd54…`): it is draft
  4.2 with lane 5's 6741787745 taken. `reg_flush` also runs
  `lane_alias_flush` (:815); `journal --last --at <n>` requires
  `--generation <g>`, ending 64 without it and 69 for an `n` above the head
  or another generation (:792, :796); and §1 names `--store-line` (R43,
  T509) and `--decide` (R38, T502) as `lanes-edit.sh`'s own flags, outside
  the contract (:182).
- **What draft 4.2 changes** (`dea51e43…`, #165 6741494851): it is draft 4.1
  with O21 closed by lane 4's (a) (#165 6741093519). §8's O21 row reads
  closed; §3.6's "Not settled here" leaves nothing unsettled; the C2 text in
  §3.6 and the header's note on lane 5's review say O21 is closed; and the
  status line, its history and a one-row "Changes from draft 4.1" section
  record it.
- **Draft 4.1, posted:** #165 comment 6739202842, 2026-11-09T11:42Z,
  sha256 `df40136e…`, 954 lines, with lane 1's block v4.1
  (`lane1-tasks-block.v4.1.md`, `53e66ced…`, 644 lines, T101 to T122).
- **The block in force:** lane 1's block v4.2 (`cad0fa93…`, 660 lines, T101
  to T122), #165 6739714248. It follows lane 3's restore order, the ruling
  on test-plan open point 41 (#165 6739605144). Block v4.4 (`a4ce9e3b…`,
  660 lines, #165 6742356416) replaces it for 007's tasks, and keeps its
  line numbers. The contract, at draft 4.4 as at 4.1, gives no restore
  order.
- **What draft 4.1 changes:** it is draft 4 (#165 6738871108, `5d6ce7ca…`,
  939 lines) with lane 5's check of draft 4 taken (#165 6739068493). The
  quote of the guard's second read is cited as Y:4160–4161; the change list
  names draft 4's two citation-only edits, in §3.2 and §3.3; and an empty
  `--lane=` ends 64 in form 2 as well as in form 4. Block v4.1 moves form
  4's proof of "too late" to lane 5's T507, in T507's own verify, since
  only T507 has `T0`: lane 1's T113 ships the bounds, `--pid`, the cap at
  `T0` + 3.75 s and D9's guard row, and its case `guard_second_read_at`
  keeps items 1 to 8, with item 9's ends 69, 78, 1 and 64.
- **What it is:** the contract between group A (`lanes-index`, lane 1) and
  group B (the write path, lane 4), FR-013 to FR-017, and in its §3.6 the
  readers' four forms of `register read`. Lane 1 writes it, lane 4 confirms
  group B's sections, lane 5 reviewed §3.6 (#165 6738431117), and lane 3
  rules (R01, R06).
- **Its open points:** none. Its §8 closes O14 to O21. O21 is closed by
  (a), lane 4's choice (#165 6741093519): T401's `register-text` and
  `register-grep` answer `lanes/LANES.md`, `lanes/aliases.tsv` and each
  `lanes/log/<lane>.md`, and 2 for any other path, `lanes/log/README.md`
  included (draft 4.4, :811 and :947).
- **L1-D1's scope** (lane 1's own decision, 6738871108): the `=` form,
  `--lane=<key>` and `--log-key=<key>`, applies to `register read`'s
  `--lane` and `--log-key` only. Group B's `--lane <lane>`, on
  `register snapshot`, `register commit` and `journal --write-id`, keeps the
  form of the phase plan's interfaces table (Y:4834).
- **Its landing:** lane 1's T101 lands the contract's final text at this
  path. A later draft replaces this copy, with its own source and digest
  here.

## `render-commit.md`: lane 3's contract, written from Y

[render-commit.md](render-commit.md) is lane 3's own contract, tagged `[L3]`
in plan.md. Lane 3 wrote it at assembly phase B on 2026-11-09, from Y's
`design.md` and `operations.md`, with `Y:` and `ops:` citations. Its
sections 1 to 4 add nothing Y does not say.

- **What it is:** D15's render commit (its author, committer, dates, build
  and message), `Register-Seq` (its value, its trailer, which commits carry
  it, and who reads it), and the one push function that the renderer,
  `cutover` and `rollback` share (FR-044, FR-045).
- **Its last section** lists what Y leaves open or unprobed, which stays
  open for the build. It also records lane 3's PC-3 values (64 seqs a pass,
  16 passes, and the gap accepted; research.md's PC-3) and reads Y's two
  conflicts by D15, which governs both (lane 3, fix round B, 2026-11-09). T345
  marks both in the change's `design.md` before the archive.
- **Its landing:** tasks.md's T344 rides the spec PR, since its whole
  output is this file, already in the spec branch. Group C (T320, T322,
  T326 and T327) codes against it.

## The other contracts

Each owner writes its file at this directory's path. Of these, only lane 3's
`render-commit.md` is in:

| File | Owner | Task |
| --- | --- | --- |
| `exit-codes.md` | lane 1, for the `lanes-index` rows | T102 |
| `config.md` | lane 1 | T102 |
| `pending-record.md` | lane 4 (R34) | T412 |
| `bind-record.md` | lane 5 (R50) | T501 |
| `socket-api.md` | lane 5 (R50) | T511 |
| `render-commit.md` | lane 3; in at phase B, from Y (above) | T344 |
