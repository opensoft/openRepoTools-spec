# Tasks

| Set | Outcome | Task IDs | Predecessor | Fast verification (code root) | Safe intermediate state |
| --- | --- | --- | --- | --- | --- |
| CI-PILOT-01 | Advisory scope and shared instructions | T001–T003 | None | `bash tests/run.sh -k ci_pilot -q` | Full checks still run; no queue activation |

- [x] T001 [O] Implement fail-closed advisory classification and offline git fixtures — files: `.github/ci/pilot_scope.py`, `tests/test_ci_pilot.py` — verify: `bash tests/run.sh -k ci_pilot -q`
- [x] T002 [O] Add the separate read-only pilot workflow without editing existing gates or PR 34 — files: `.github/workflows/ci-pilot.yml`, `tests/test_ci_pilot.py` — verify: `bash tests/run.sh -k ci_pilot -q`
- [x] T003 [S] Add one shared Claude/Codex reference retaining lane and triad ownership — files: `AGENTS.md`, `.github/ci/task-set-delivery.md`, `tests/test_ci_pilot.py` — verify: `bash tests/run.sh -k ci_pilot -q`

Publication, hosted CI, exact-head Codex review and later landing are evidence
states, not permission to merge. No live canary is part of this task set.
