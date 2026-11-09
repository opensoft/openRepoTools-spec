# Contracts: feature 007

This directory holds the contract files that plan.md lists. Each is written
by the lane that owns it, and lands before the code that rests on it.

## `register-cli.md`: lane 1's contract draft 4, keyed to Y

[register-cli.md](register-cli.md) is lane 1's contract draft 4, keyed to Y
(`a6497d00`), copied here byte for byte by lane 3 at assembly phase A on
2026-11-09.

- **Source:** lane 1's contract draft 4, posted on #165 at 6738871108,
  sha256 `5d6ce7ca…`.
- **sha256:**
  `5d6ce7ca7b0705f0e7a9eb70332745045d3e88d006e1affcc190434f3c2cae91`, 939
  lines. The copy here has the same digest.
- **Posted:** #165 comment 6738871108, 2026-11-09T11:25Z, with lane 1's
  block v4 (`lane1-tasks-block.v4.md`, `203794ec…`, 638 lines, T101 to
  T122).
- **What it is:** the contract between group A (`lanes-index`, lane 1) and
  group B (the write path, lane 4), FR-013 to FR-017, and in its §3.6 the
  readers' four forms of `register read`. Lane 1 writes it, lane 4 confirms
  group B's sections, lane 5 reviewed §3.6 (#165 6738431117), and lane 3
  rules (R01, R06).
- **Its open point:** its §8 closes O14 to O20. Only O21 stays open: C2's
  (a) or (b) for T401's reader of a render-set path, which is lane 4's
  choice.
- **L1-D1's scope** (lane 1's own decision, 6738871108): the `=` form,
  `--lane=<key>` and `--log-key=<key>`, applies to `register read`'s
  `--lane` and `--log-key` only. Group B's `--lane <lane>`, on
  `register snapshot`, `register commit` and `journal --write-id`, keeps the
  form of the phase plan's interfaces table (Y:4834).
- **Its landing:** lane 1's T101 lands the contract's final text at this
  path. A later draft replaces this copy, with its own source and digest
  here.

## The other contracts

None is in yet. Each owner writes its file at this directory's path:

| File | Owner | Task |
| --- | --- | --- |
| `exit-codes.md` | lane 1, for the `lanes-index` rows | T102 |
| `config.md` | lane 1 | T102 |
| `pending-record.md` | lane 4 (R34) | T412 |
| `bind-record.md` | lane 5 (R50) | T501 |
| `socket-api.md` | lane 5 (R50) | T511 |
| `render-commit.md` | lane 3 | — |
