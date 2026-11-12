# Tasks

## 1. Canonical implementation handoff

- [x] 1.1 Hand off to `specs/009-manual-two-pr-queue/tasks.md`, CI-CUTOVER-09;
  verify that the sole executable checklist retains disabled routing and excludes
  merging, activation, live queue, settings, releases and running-lane changes.

Implementation is owned only by `specs/009-manual-two-pr-queue/tasks.md`, task
set CI-CUTOVER-09 (T022–T032, including approved local-review repairs and current-main reconciliation). Follow that checklist and its verification commands;
do not create a second executable checklist here. Preparation/opening is the
current authority boundary. Merge, activation, migration, canary and assembly
adoption require later approval and are not completion claims of this handoff.

2026-11-12 subsequent authority: reconcile code PR52 with landed Sonar PR51,
rerun exact-head CI/local review, then normally merge PR52/33 if green. The sole
executable checklist continues at T031–T032. Preparation-only text above records
the original approval, not a prohibition on this later approved source landing.
Activation/canary/settings/release, assembly adoption and lanes remain excluded.

Code PR52 subsequently qualified at `94d4659` and normally landed as
`5173ad25910ca4d7acfd6052090246ccc084f1a1` with identical tested/landed trees.
T031–T032 record completed source reconciliation/qualification. Actual final
spec landing is a PR33 receipt; no archive or operational activation is inferred.
