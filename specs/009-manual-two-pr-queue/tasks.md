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
- [x] T032 [O] Qualify the exact reconciled head with focused tests, lint, strict governance, full hosted ordinary/reusable CI and local expert review; prepare guarded normal source landings — files: `specs/009-manual-two-pr-queue/tasks.md`, `specs/009-manual-two-pr-queue/plan.md` in the spec leg, PR52/33 qualification records — verify: `gh pr checks 52 --repo opensoft/openRepoTools-code --required`

Actual normal merges and landed-tree verification are separate external acts
recorded on PR52/33. T032 completion means qualification and guarded landing
preparation, not a claim that either PR has already merged.

Current-main qualification at code `94d4659`: ordinary `43321898110` passed all
15 jobs; reusable `43321898191` passed all 17, both first attempts. All four JUnit
variants reported 5,125 unique cases, with no removed original cases or changed
skip identities. Normal: 4,956 passed/169 existing skips. Deliberate no-submodule:
3,607 passed/1,518 existing skips. All 43 repair cases and the 45 inherited Sonar
additions passed in every variant. Focused QA: 836 passed/no skips. Actionlint,
strict OpenSpec, SonarCloud, Mergify configuration and local expert review passed.

Code PR52 was normally squash-merged as
`5173ad25910ca4d7acfd6052090246ccc084f1a1`, parent `b227d53`, at
2026-11-12T23:21:37Z. Its tree `0ef9640b600b1ffacc542ed40ab79db14e2bca21`
exactly matches qualified head `94d4659` and actual tested merge `684c21f`.
The final spec landing and its tree readback are recorded on PR33. No bypass,
settings, activation/canary, releases, assembly pins or lane changes follow.

## Exact-head local review receipts — 2026-11-12

LOCAL-REVIEW-10 is one narrow code PR plus a paired spec PR, governed by
`accept-exact-head-local-reviews` under issue 26. Brett authorized implementation,
regression tests and opening the PRs only. This is the sole executable checklist.
Code paths below are code-relative; governance paths are spec-relative.

- [x] T033 [O] Accept authenticated bounded exact-head local-review comments with explicit replacement/revocation, preserving hosted review and live thread rechecks — files: `.github/ci/delivery_verify.py`, `tests/test_ci_local_review.py` — verify: `bash tests/run.sh -k ci_local_review`
- [x] T034 [O] Prove local-only and mixed candidate admission, stale/forged/edited/malformed receipts, revoked/lost access and live evidence drift; adapt existing fixtures to complete comment metadata and isolate the wait-loop test clock while retaining disabled/full-CI coverage — files: `tests/test_ci_local_review.py`, `tests/test_ci_delivery.py`, `tests/test_ci_cutover.py`, `tests/test_ci_cutover_repair.py` — verify: `bash tests/run.sh -k 'ci_local_review or ci_delivery or ci_cutover or ci_candidate_shadow or full_ci_coverage_parity or tests_workflow or ci_shards'`
- [x] T035 [O] Reconcile the shared receipt procedure and governing feature contracts, validate and locally review the exact code/spec diff, then open paired PRs without merging — files: `.github/ci/delivery-cutover.md`, `.github/ci/task-set-delivery.md`, `features/009-manual-two-pr-queue/openspec/local-review-receipts.md`, spec `openspec/changes/accept-exact-head-local-reviews/`, `specs/009-manual-two-pr-queue/` — verify: focused tests, strict OpenSpec, whitespace and published OPEN/disabled readbacks

Verification at code `a9300367ba8202b17678b60648b701c8b52dfbb6`: canonical
`tests/run.sh` passed 908 focused CI/Sonar contracts, no skips (4,289 deselected).
All 72 added tests also passed in a separate deliberate no-submodule checkout,
no skips (5,125 deselected). Runs used the existing disposable bench image as
UID1000, offline, bounded to 2CPU/2GiB; no running bench was restarted or rebound.
The first new local-only candidate tests failed the old hosted-only reader.
The clock-isolation regression also failed the old shared-time mock before its
test-only correction. Review found and repaired full-history replacement/edit
and hosted-fallback races; final security/reliability/QA/API/CI/static reviews
found no remaining actionable issues. Both OpenSpec changes validate strictly.

No workflow definition, queue configuration, activation flag, full-coverage
baseline, artifact schema, dependency pin or release file changed. Hosted full
CI, source merges, live qualification and assembly adoption remain separate.

Published preparation: code PR53 and spec PR36, both OPEN against main. Code
head is `a9300367ba8202b17678b60648b701c8b52dfbb6`. The master plus nine standard
expert roles and CI specialist completed local review; the security, reliability,
QA, API, static, CI, architecture, persistence, performance and simplicity final
passes acknowledged the repaired union without actionable findings. Subsequent
spec-only task/count notes are coordinator-reviewed publication records, not a
new contract. Ordinary full CI and reuse qualification are running on GitHub,
not yet claimed passed. The flag remains false; source merges are not approved.

Both shared py-bench and cloud-bench assembly checkouts remain `4b3c3f3`; no
lane/session/tool/pin change, settings or queue write, canary or release act was
performed. This completes T033–T035 as source preparation/publication only.

## CI-ACTIVATION-11 — owner-approved scoped activation (2026-11-13)

Governing change: `activate-claude-current-ci-cutover`. Brett explicitly approved
the required-check migration, reviewed enablement PR and two-PR test, preserving
lanes 1-5 and excluding bypass, app/paid access, releases and assembly pins.
Earlier preparation-only and local-review records above remain historical.
Code-relative paths below name implementation; OpenSpec/Speckit paths are
spec-relative. This is the sole executable checklist, not proof of completion.
Commands below are individual gates; actual remote/tree comparisons and live
acceptance evidence are additionally required before marking their task done.

- [x] T036 [O] Record the approved activation phase and current source baseline — files: `openspec/changes/activate-claude-current-ci-cutover/`, `specs/009-manual-two-pr-queue/`, code `features/009-manual-two-pr-queue/openspec/activation.md` — verify: `openspec validate activate-claude-current-ci-cutover --strict`
- [x] T037 [O] Migrate only ruleset 25966308's required contexts with live flag false, preserving all other fields and recording target/effective readbacks — files: `features/009-manual-two-pr-queue/openspec/activation.md` — verify: `gh api repos/opensoft/openRepoTools-code/rules/branches/main | python3 -c 'import json,sys; checks=[c for r in json.load(sys.stdin) if r["type"]=="required_status_checks" for c in r["parameters"]["required_status_checks"]]; assert {"context":"openrepotools-delivery","integration_id":15368} in checks; assert not ({"guard-launch-mode","tests","tests-no-submodule","package"} & {c["context"] for c in checks})'`
- [x] T038 [O] Enable only the prepared flag, preserve explicit disabled/fallback tests, update shared instructions and open locally reviewed paired PRs — files: `.github/ci/delivery-policy.json`, `.github/ci/delivery-cutover.md`, `.github/ci/task-set-delivery.md`, `tests/test_ci_cutover.py`, `tests/test_ci_local_review.py`, `tests/test_ci_pilot_queue.py`, `features/009-manual-two-pr-queue/openspec/activation.md` — verify: `bash tests/run.sh -k 'ci_ or full_ci_coverage_parity or tests_workflow or ci_shards'`
- [x] T039 [O] Qualify exact-head ordinary/reuse CI and review, normally merge the enablement/governance PRs and verify landed trees plus actual main CI before queue use — files: `features/009-manual-two-pr-queue/openspec/activation.md`, spec `specs/009-manual-two-pr-queue/activation-evidence.md` — verify: `test -n "${ENABLEMENT_MAIN_RUN_ID:-}" && gh run watch "$ENABLEMENT_MAIN_RUN_ID" --repo opensoft/openRepoTools-code --exit-status`
- [ ] T040 [O] Run refusal/recovery fixtures, then the authorized two-member independent-main canary with explicit local-review acceptance; verify actual fast-only source/candidate runs, one complete heavy call and matching prefix/final trees — files: `claude-current`, `tests/test_claude_current.py`, `features/009-manual-two-pr-queue/openspec/activation.md`, spec `specs/009-manual-two-pr-queue/activation-evidence.md` — verify: `test -n "${CANARY_HEAVY_RUN_ID:-}" && gh run watch "$CANARY_HEAVY_RUN_ID" --repo opensoft/openRepoTools-code --exit-status`
- [ ] T041 [O] Reconcile actual settings/run/artifact/tree evidence and remaining adoption boundaries without advancing assembly pins or archiving — files: `features/009-manual-two-pr-queue/openspec/activation.md`, `specs/009-manual-two-pr-queue/activation-evidence.md`, `specs/009-manual-two-pr-queue/tasks.md` — verify: `openspec validate activate-claude-current-ci-cutover --strict`

T036–T038 preparation/publication receipt: code PR56 is open at
`d4d42f936eb1c3f0cd416112fca014c829e4ae19`; spec PR39 carries this governance.
The master, nine standard expert roles and CI specialist found no actionable
issues at code c2e2225/spec 5cf876f; the master reviewed the final documentation
clarification and this publication-only note. Local CI contracts passed 867
cases with no skips; six phase cases passed without the submodule, also no skips.
The tested source patch bytes match the reviewed implementation exactly. All
three related OpenSpec changes validate strictly. Hosted full/reuse CI and
review requests are live, not yet claimed passed. Live main policy remains false.
T039–T041 still require actual source/main and live-canary evidence; no queue,
assembly, release, app/paid access, bypass or running-lane action occurred.

### Independent canary task sets

T040 is the orchestration/acceptance gate, not an implementation task assigned
to both source PRs. These two implementation tasks each belong to exactly one
independent main-based PR. Neither depends on the other; no Depends-On line or
invented stacked dependency is appropriate. PR identities and actual results
will be recorded in activation-evidence.md after publication and qualification.

| Set | Task | Outcome and safe intermediate state | Immediate predecessor | Fast verification |
| --- | --- | --- | --- | --- |
| CI-ACTIVATION-11-A | T042 | Clarify resolver stdout/stderr in help; no execution/install change, safe alone | None; qualified enablement main | bash -n claude-current; bash tests/run.sh -k 'claude_current and (help or malformed or repositorys_bash_rules)' |
| CI-ACTIVATION-11-B | T043 | Cover both help aliases before malformed timeout validation, with zero fake npm/node/Claude calls; tests pass without A | None; same qualified enablement main | bash tests/run.sh -k 'claude_current and (help or malformed)' |

- [ ] T042 [O] Implement CI-ACTIVATION-11-A in one source PR; retain executable mode, resolve exact-head review and publish a fresh writer-accepted local receipt — files: code `claude-current` — verify: set A commands above plus actual hosted fast/delivery checks
- [ ] T043 [O] Implement CI-ACTIVATION-11-B in a separate source PR; use the existing hermetic Sandbox without depending on A, resolve exact-head review and publish a fresh writer-accepted local receipt — files: code `tests/test_claude_current.py` — verify: set B command above plus actual hosted fast/delivery checks

## CI-METADATA-12 — approved source-description repair (2026-11-13)

The owner approved the narrow metadata fix, regression tests, checked code/spec
merges and a fresh two-PR canary. The follow-up approval bundle covers directly
related fixes, reviews, full validation, checked normal merges and fresh canaries
without per-PR approval stops. Bypass, app/paid access, releases, tools, assembly
pins and running-lane changes remain excluded. Governing change:
fix-source-delivery-metadata. The initial attempt remains failed evidence.

One configuration task set CI-METADATA-12 contains T044-T046; T047/T048 are
external integration/acceptance gates, not duplicate implementation PRs. Fresh
independent PRs retry the same separate T042/T043 outcomes after qualified main.

- [x] T044 [O] Record approved metadata decision contract and handoff without a duplicate checklist — files: `openspec/changes/fix-source-delivery-metadata/`, this task file, feature009 `spec.md` — verify: `openspec validate fix-source-delivery-metadata --strict`
- [x] T045 [O] Add red-first prose-edit, intent/dependency/identity and strict-live-race regressions; normalize only the first source-event body comparison — files: code `.github/ci/delivery_route.py`, `tests/test_ci_cutover.py`, `.github/ci/delivery-cutover.md` — verify: `bash tests/run.sh -k 'ci_cutover or ci_local_review or ci_delivery'`
- [x] T046 [O] Complete exact-head local expert review and full source/reuse qualification; open paired code/spec PRs — files: code/spec PR records and `activation-evidence.md` — verify: exact head/base checks, complete ordinary/reuse JUnit inventories and strict OpenSpec
- [x] T047 [O] Normally merge checked code/spec heads, compare actual landed trees and qualify actual code main before fresh canary — files: `activation-evidence.md`, PR receipts — verify: actual main full CI and test-inventory parity
- [ ] T048 [O] Publish fresh independent T042/T043 PRs and accepted exact-head reviews; verify fast-only source/candidate CI, one heavy run, matching prefixes/final tree and no duplicate full main run; reconcile evidence — files: source PRs, `activation-evidence.md`, this checklist — verify: immutable run/artifact/member/tree readbacks and strict OpenSpec; no archive or assembly adoption

### R2 refusal observability follow-up

PR60/61 are a second failed source-efficiency attempt, not queue acceptance.
The pre-approved directly related repair bundle covers this diagnostic-only
task set CI-METADATA-12-D. Do not relax provenance based on an unproven cause.

- [x] T049 [O] Add secret-safe fixed-vocabulary stage/refusal diagnostics and snapshot mismatch field names, retaining exact route results and all gates — files: code `.github/ci/delivery_route.py`, `tests/test_ci_cutover.py`, `.github/ci/delivery-cutover.md` — verify: red-first diagnostic tests and full existing routing/review contracts
- [x] T050 [O] Review exact code/spec refs, strictly validate, open paired PRs and fully qualify checked normal landings plus actual main — files: paired PRs, OpenSpec artifacts and `activation-evidence.md` — verify: full ordinary/reuse job and test inventory parity, reviewed/landed trees, actual-main results
- [ ] T051 [O] Capture a genuinely new source event with diagnostic evidence; implement only a proven necessary repair under the same checked gates, then complete fresh T048 acceptance — files: source PRs, narrow directly related repair if needed and evidence — verify: fixed stage/reason plus actual fast-only/heavy/member/tree evidence; never reinterpret failed runs

### R3 generated-merge-hint repair — CI-METADATA-13

PR65 supplied the new diagnostic evidence: initial event/live merge_commit_sha
differs while fast CI proves the actual tested merge. The raw original hint
value was not logged. Preserve this failed attempt and qualify only the bounded
nullable/current compatibility rule, not arbitrary stale hints or weaker proof.

- [x] T052 [O] Record the observed R3 refusal and bounded source/candidate hint contract, preserving the sole task store and exclusions — files: OpenSpec fix-source-delivery-metadata artifacts, feature009 spec/tasks/evidence — verify: strict OpenSpec and exact live log/commit readbacks
- [x] T053 [O] Add red-first nullable/current and invalid-hint tests, repair only initial event comparisons, and retain tested-parent, strict-live/body and admission refusals — files: code `.github/ci/delivery_route.py`, `tests/test_ci_cutover.py`, `.github/ci/delivery-cutover.md` — verify: canonical CI contracts and deliberate no-submodule regressions
- [x] T054 [O] Review exact heads, open paired PRs and qualify complete ordinary/reusable full CI with exact test identities and skip accounting — files: code/spec PRs and evidence — verify: exact-head local panel, full job/report inventories and strict OpenSpec
- [x] T055 [O] Normally merge checked heads, verify landed trees and actual main, then resume fresh T051/T048 sources without reusing failed attempts — files: PR/actual-main/evidence records — verify: normal merge readbacks, actual-main full CI and complete inventory parity; no archive or assembly adoption

### R4 pending-fast completion race — CI-METADATA-14

Fresh PR67 reached fast-reread after initial metadata/Git validation. Its exact
before/after fields were not logged; fast completion and refusal timestamps
coincide. Reproduce the concrete within-read pending-completion race, preserve
completed-proof strictness and all existing exclusion boundaries.

- [x] T056 [O] Record observed R4 failure and pending-observation contract without duplicating tasks — files: fix-source-delivery-metadata OpenSpec artifacts, feature009 spec/tasks/evidence — verify: strict OpenSpec, exact run/job/timing readbacks and failed-attempt preservation
- [x] T057 [O] Reproduce within-read completion red-first, repair only pending wait ordering and preserve completed/admission refusals — files: code delivery_verify.py, delivery_evidence.py, focused repair regressions, cutover runbook and dated activation-record pointer — verify: canonical CI contracts, no-submodule new cases, exact-head local review and full ordinary/reuse inventories
- [ ] T058 [O] Open and normally merge fully checked code/spec repair PRs, verify landed trees/actual main, then resume genuinely fresh source events and complete T051/T048 — files: paired PRs and evidence — verify: full source/main parity and actual member/heavy/tree/no-duplicate proof; no archive or assembly adoption

T057 qualification is complete at code `c97a6e8` / spec `16d7ecf`: latest-head
ordinary `43521046751` passed 15/15 jobs, reusable `43521046869` 17/17 and fast
`43521046787` 1/1, all first attempts. All four complete inventories contain
5,365 unique cases and pass all 23 new regressions without removed cases,
duplicates, failures or skip-identity drift. Normal variants pass 5,192 with
173 existing skips; deliberate no-submodule variants pass 3,843 with 1,522.
Exact-head local expert review is clean; hosted requests received quota replies,
not completed reviews. Strict OpenSpec and the recorded local red/green QA pass.

The checked code/spec merges landed as `908d815` / `d266136`, with exact reviewed
trees and sole expected parents. Actual-main run `43526748400` is still pending.
Consequently T058 and every fresh live-acceptance task remain open. No fresh R5
source event, PR, acceptance receipt, queue request or live qualification is
recorded by this update. Subsequent local-only R5 commits and their exact-bundle
QA are distinguished from publication in activation-evidence.md, alongside the
full dated source/landing facts.
