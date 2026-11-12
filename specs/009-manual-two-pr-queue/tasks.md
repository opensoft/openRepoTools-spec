# Tasks

| Set | Outcome | Tasks | Predecessor | Fast verification (code root) | Safe state |
| --- | --- | --- | --- | --- | --- |
| QUEUE-CONFIG-01 | Manual full-CI pilot queue configuration | T001–T003 | Landed feature 008 | `bash tests/run.sh -k manual_mergify_queue` | No automatic admission or live canary |

- [x] T001 [O] Define one bounded manual queue retaining full CI — files: `.mergify.yml` — verify: `bash tests/run.sh -k manual_mergify_queue`
- [x] T002 [O] Test parsed configuration, admission refusals and unchanged CI contracts — files: `tests/test_ci_pilot_queue.py` — verify: `bash tests/run.sh -k 'manual_mergify_queue or tests_workflow or ci_pilot'`
- [x] T003 [S] Document operator boundaries and link shared agent guidance — files: `.github/ci/batching-pilot.md`, `.github/ci/task-set-delivery.md` — verify: `bash tests/run.sh -k manual_mergify_queue`

Only this file owns executable implementation tasks. Publication, reviews,
future canary qualification and eventual assembly landing remain distinct gates.

## Accepted rollout tasks — 2026-11-11

| Set | Outcome | Tasks | Predecessor | Fast verification | Safe state |
| --- | --- | --- | --- | --- | --- |
| CODE-QUEUE-02 | General manual full-CI queue and shared instructions; code PR #45 | T004–T006 | Code #44 | `bash tests/run.sh -k 'ci_pilot_queue or tests_workflow'` | No automatic enqueue or canary |
| SPEC-RECON-03 | Dated governance reconciliation; spec PR #27 | T007 | Working CODE-QUEUE-02 and prior batch evidence | `openspec validate manual-two-pr-queue --strict` | No archive or source activation |
| ASSEMBLY-04 | Root pointers and coherent project landing; assembly PR | T008 | Landed code and reconciled spec | `make validate` | Lanes 1–5 unchanged |

T004–T006 paths are code-relative; T007 spec-relative; T008 assembly-relative.

- [x] T004 [O] Add conservative main-only manual code queue retaining all checks — files: `.mergify.yml` — verify: `bash tests/run.sh -k general_code_queue`
- [x] T005 [O] Test accepted code and state/base/check/path refusals — files: `tests/test_ci_pilot_queue.py` — verify: `bash tests/run.sh -k ci_pilot_queue`
- [x] T006 [S] Update shared instructions, operator procedure and amendment — files: `AGENTS.md`, `.github/ci/task-set-delivery.md`, `.github/ci/batching-pilot.md`, `features/009-manual-two-pr-queue/openspec/general-code-queue.md` — verify: `bash tests/run.sh -k ci_pilot_queue`
- [x] T007 [O] Reconcile current behavior and evidence while preserving original decisions — files: `specs/009-manual-two-pr-queue/`, `openspec/changes/manual-two-pr-queue/` — verify: `openspec validate manual-two-pr-queue --strict`
- [x] T008 [O] Add root pointers to the one pinned shared workflow and retain the lockstep landing procedure — files: `AGENTS.md`, `CLAUDE.md` — verify: `make validate`

Exact-head review, full required CI, normal merges and composed CI are separate
landing evidence. No source change is complete merely because local tests pass.

Assembly activation is the landing gate after these implementation tasks: use
bump-leg.py on merged leg commits, qualify exact pins and then refresh only
authorized clean mounts. Its merged SHA, composed CI and local readback belong
on the assembly PR and reconciliation issue #26; no duplicate checklist is made.

## Coverage-parity preparation — 2026-11-11

One code PR owns task set COVERAGE-PARITY-05 (T009–T011); a paired spec PR
reconciles this approved bounded amendment under issue #26. No later efficiency
stage is approved by checking these tasks. Paths below are code-relative.

- [x] T009 [O] Make the unchanged full workflow locally reusable and qualify it on CI-related PRs — files: `.github/workflows/tests.yml`, `.github/workflows/full-ci-parity.yml`, `tests/test_tests_workflow.py` — verify: `bash tests/run.sh -k 'tests_workflow or full_ci_coverage_parity'`
- [x] T010 [O] Freeze complete job/environment coverage and reject reduced/unsuccessful execution — files: `.github/ci/full-ci-baseline.json`, `tests/test_full_ci_coverage_parity.py` — verify: `bash tests/run.sh -k 'full_ci_coverage_parity or ci_shards'`
- [x] T011 [S] Record the prerequisite and retained boundaries — files: `.github/ci/batching-pilot.md`, `features/009-manual-two-pr-queue/openspec/coverage-parity.md` — verify: `bash tests/run.sh -k full_ci_coverage_parity`

Publication, current-head cloud review/full CI/reuse qualification and subsequent
merge/assembly landing are separate evidence, not implied by local task completion.

## Trusted shadow-classifier preparation — 2026-11-11

Task set SHADOW-CLASSIFIER-06 (T012–T014) is one code PR with a paired spec
reconciliation under issue #26. All implementation paths below are code-relative.

- [x] T012 [O] Add bounded trusted-main read-only event observations with full fallback — files: `.github/ci/shadow_scope.py`, `.github/workflows/ci-shadow.yml` — verify: `bash tests/run.sh -k ci_shadow`
- [x] T013 [O] Prove scope, identity, metadata, workflow trust and unchanged full-CI boundaries — files: `tests/test_ci_shadow.py` — verify: `bash tests/run.sh -k 'ci_shadow or ci_pilot or full_ci_coverage_parity or tests_workflow or ci_shards'`
- [x] T014 [S] Record observational-only authority and later qualification boundaries — files: `.github/ci/shadow-classifier.md`, `.github/ci/task-set-delivery.md`, `features/009-manual-two-pr-queue/openspec/shadow-classifier.md` — verify: `bash tests/run.sh -k ci_shadow`

Source task completion does not imply hosted positive-scope observation, candidate
acceptance, review, merge, assembly delivery or CI-efficiency activation.
