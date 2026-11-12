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

## Additive fast/candidate preparation — 2026-11-11

DELIVERY-PROOF-07 is one coupled code PR plus a paired spec reconciliation under
issue #26. No activation task is approved by completing this preparation set.
Implementation paths below are code-relative.

- [x] T015 [O] Add exact-source fast validation and immutable read-only candidate/member/review proof — files: `.github/ci/delivery_verify.py` — verify: `bash tests/run.sh -k ci_delivery`
- [x] T016 [O] Define additive fast and workflow_call-only full candidate qualification without queue wiring — files: `.github/workflows/task-set-fast.yml`, `.github/workflows/task-set-candidate.yml` — verify: `bash tests/run.sh -k ci_delivery`
- [x] T017 [O] Prove failure, stale/forged evidence and full-coverage workflow boundaries — files: `tests/test_ci_delivery.py` — verify: `bash tests/run.sh -k 'ci_delivery or ci_shadow or ci_pilot or full_ci_coverage_parity or tests_workflow or ci_shards'`
- [x] T018 [S] Document prepared-only semantics and the explicit activation authority boundary — files: `.github/ci/delivery-preparation.md`, `.github/ci/task-set-delivery.md`, `features/009-manual-two-pr-queue/openspec/delivery-proof.md` — verify: `bash tests/run.sh -k ci_delivery`

Local completion is source preparation, not hosted review/full-CI qualification,
candidate acceptance, assembly adoption or queue/ruleset activation.

Reopened T015/T017 after local nine-role review reproduced dependent ancestry,
alternate-base fast evidence and mutable fast/review evidence drift. Repair and
regression qualification stay in this same bounded task set and PR pair.

Repair locally qualified at a644168: 561 committed focused regression cases plus
four review reproductions passed (565 total, no skips), with workflow lint.
Current-head hosted CI and local re-review remain separate landing gates.

## Candidate-shadow caller preparation — 2026-11-12

One code PR owns CANDIDATE-SHADOW-08 (T019–T021), with a paired spec proposal
under issue #26. Scope is prepare/open only. No merge or live canary is approved.
All paths below are code-relative; this is the only executable checklist.

- [x] T019 [O] Prepare non-gating queue-push scheduling with trusted-main probe and the unchanged complete candidate entry — files: `.github/workflows/task-set-candidate-shadow.yml` — verify: `bash tests/run.sh -k ci_candidate_shadow`
- [x] T020 [O] Prove caller trust, literal outputs, graph and full fallback without reducing any existing coverage — files: `tests/test_ci_candidate_shadow.py` — verify: `bash tests/run.sh -k 'ci_candidate_shadow or ci_delivery or ci_shadow or ci_pilot or full_ci_coverage_parity or tests_workflow or ci_shards'`
- [x] T021 [S] Record non-gating observation and explicit cutover/authority boundaries — files: `.github/ci/delivery-preparation.md`, `.github/ci/task-set-delivery.md`, `features/009-manual-two-pr-queue/openspec/candidate-shadow-caller.md` — verify: `bash tests/run.sh -k ci_candidate_shadow`

Publication and exact-head hosted checks/review are evidence, not merge authority
or proof of live batching/efficiency. No new source green aliases, settings,
queues, releases, pins or installed sessions belong to this task set.

Local evidence: 577 focused regressions passed with zero skips, including 16
new caller vectors; initial red run could not load the absent caller. Tests ran
through tests/run.sh as UID1000 in an offline disposable 2CPU/2GiB bench image.
Workflow actionlint, strict OpenSpec and whitespace checks passed. Hosted full
CI/reuse qualification, exact-head review and any merge remain separate pending
gates; no live observation or one-heavy-run acceptance is claimed.

## Disabled CI-efficiency cutover preparation — 2026-11-12

CI-CUTOVER-09 is one coupled CI-only code PR with a paired governance PR under
issue26 and `prepare-claude-current-ci-cutover`. Prepare/open only. Paths are
code-relative except this reconciliation. Preserve Sonar PR51's baseline work.

- [x] T022 [O] Implement strict disabled activation, effective app-bound protection and trusted scoped source/full routing — files: `.github/ci/delivery-policy.json`, `.github/ci/delivery_route.py` — verify: `bash tests/run.sh -k ci_cutover`
- [x] T023 [O] Bind immutable admission/qualified artifacts, authentic candidate runs and historical ordered landing proofs — files: `.github/ci/delivery_verify.py`, `.github/ci/delivery_evidence.py` — verify: `bash tests/run.sh -k 'ci_cutover or ci_delivery'`
- [x] T024 [O] Wire exact full-job guards, final delivery verification, held manual queue and top-level heavy check without enabling omissions — files: `.github/workflows/tests.yml`, `.github/workflows/task-set-candidate*.yml`, `.mergify.yml` — verify: `bash tests/run.sh -k 'ci_cutover or ci_candidate_shadow or full_ci_coverage_parity or tests_workflow or ci_pilot_queue'`
- [x] T025 [O] Prove complete coverage projection and refusal behavior with real Git/mocked read APIs/artifact bytes and actual shell gates — files: `tests/test_ci_cutover.py`, existing CI contract tests — verify: `bash tests/run.sh -k 'ci_cutover or ci_candidate_shadow or ci_delivery or ci_shadow or ci_pilot or full_ci_coverage_parity or tests_workflow or ci_shards'`
- [x] T026 [S] Document future migration, changed source floor, operator-only queue semantics, rollback and separate acceptance gates; open code/spec PRs and request exact-head reviews — files: `.github/ci/task-set-delivery.md`, `.github/ci/delivery-cutover.md`, `features/009-manual-two-pr-queue/openspec/ci-cutover.md`, this feature and OpenSpec change — verify: workflow lint, strict OpenSpec, whitespace and published PR readback

The flag stays false. No settings write, direct fast-source merge, live queue,
canary, bypass, app/paid access, release/tag, assembly pin or lane change follows.

Published preparation: code PR52 and spec PR33. All five implementation tasks
are complete as source preparation; exact-head review/hosted CI and any later
source merge, service migration, live canary or assembly adoption remain distinct.

## Approved local-review repairs — 2026-11-12

The local panel reproduced three defects at code d4b3ae1/spec d3f297a. Brett
approved repairing them inside PR52/33, revalidating and rerunning local review,
but leaving both PRs open. These tasks extend the same CI-CUTOVER-09 set, not a
second executable checklist. Earlier completion records are historical, not
acceptance of the defective enabled path.

- [x] T027 [O] Acquire the validated synthetic PR merge object independently in both trusted controls, without checkout/ref/credential mutation — files: `.github/ci/delivery_route.py`, `.github/ci/delivery_evidence.py`, `tests/test_ci_cutover_repair.py` — verify: `bash tests/run.sh -k ci_cutover_repair`
- [x] T028 [O] Include classic branch protection in activation verification, retaining full on malformed/incomplete/raced evidence with existing read access — files: `.github/ci/delivery_route.py`, `tests/test_ci_cutover.py`, `tests/test_ci_cutover_repair.py` — verify: `bash tests/run.sh -k 'ci_cutover or ci_delivery'`
- [x] T029 [O] Wait only for authenticated pending fast/admission evidence; return definitive full-only cases immediately and preserve strict admission — files: `.github/ci/delivery_verify.py`, `.github/ci/delivery_evidence.py`, `.github/ci/delivery_route.py`, `tests/test_ci_cutover_repair.py` — verify: `bash tests/run.sh -k 'ci_cutover or ci_delivery'`
- [x] T030 [S] Update matching contracts/runbook, repeat complete focused and hosted ordinary/reuse validation and latest-head local panel, publish fixes in existing PR52/33 without merging — files: this feature, OpenSpec change, `.github/ci/delivery-cutover.md`, PR receipts — verify: focused CI regressions, strict OpenSpec, actionlint, whitespace, exact-head CI/review and OPEN readbacks

Repair acceptance at code2c6eed1: 791 focused passed/no skips; fresh ordinary
run43309287917 passed all15 jobs and reusable43309287854 passed all17 jobs.
Both normal variants: 5,080 items reported once,4,911 passed/169 skipped.
Both deliberate no-submodule variants: 5,080 once,3,562 passed/1,518 skipped.
All43 new repair cases passed in every variant; no old case removed or skip
identity changed from qualified d4b3ae1. The local master, nine standard expert
roles and CI specialist found no remaining actionable issue. GitHub connector
review requests remained quota-blocked, not approved. PR52/33 remain open and
disabled; this completes the approved repair, not merge or live acceptance.

No archive, activation/canary/settings/app/release/tag, assembly pin, installed
tool or running-lane changes are authorized by these repairs.

## Approved current-main reconciliation and source landing — 2026-11-12

Brett approved updating code PR52 for current main after Sonar PR51 landed as
`b227d53c42449eeafbd4a57979cfbd541f74fbd5`, rerunning CI/local review and merging
PR52/33 only once green. This supersedes the earlier leave-open boundary for
these two source PRs only. It does not authorize activation, canary, settings,
release/tag, assembly pins, checkout refresh or changes to lanes 1–5.

- [x] T031 [O] Reconcile code PR52 with current main while preserving landed Sonar collectors, reviewed baseline and dependency pin — files: code `.github/workflows/tests.yml`, `tests/test_tests_workflow.py`, related CI contract tests if required; spec `specs/009-manual-two-pr-queue/`, `openspec/changes/prepare-claude-current-ci-cutover/` — verify: `bash tests/run.sh -k 'ci_cutover or ci_candidate_shadow or ci_delivery or ci_shadow or ci_pilot or full_ci_coverage_parity or tests_workflow or ci_shards or sonar'`
- [ ] T032 [O] Qualify the exact reconciled head with focused tests, lint, strict governance, full hosted ordinary/reusable CI and local expert review; prepare guarded normal source landings — files: PR52/33 and qualification records — verify: exact-head CI/review and pre-merge identity/policy guards with policy disabled and lane mounts unchanged

Actual normal merges and landed-tree verification are separate external acts
recorded on PR52/33. T032 completion means qualification and guarded landing
preparation, not a claim that either PR has already merged.
