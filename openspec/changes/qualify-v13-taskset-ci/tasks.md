# Tasks

## 1. Scope and coordination

- [x] 1.1 Confirm P01 ownership and immutable anchors without editing its files; verify rootissue165 receipts6888099830 and the lane1 response to6888083282, and PR88's file blobs.
- [x] 1.2 Create the canonical proposal/design/delta and isolated paired011 worktrees; verify OpenSpec planning status and official SpecKit helper JSON.

## 2. Implementation

- [x] 2.1 Add the disabled projection and read-only observer; verify positive/negative local Git/mock-API cases and fixed full-CI output invariants.
- [x] 2.2 Add trusted-main/bootstrap and token-free contract workflow wiring, qualification path filters and runbook; verify workflow contracts plus existing full-CI parity tests with no P01/control drift.

## 3. Validation and publication

- [x] 3.1 Validate the canonical change and review exact code/spec diffs; verify focused tests, strict OpenSpec validation and resolved actionable review findings.
- [x] 3.2 Open paired PRs and explicitly request exact-head Codex review; verify remote heads, ordinary full/reusable qualification runs and disabled v1.3 scope. Leave PRs open; no merge, activation or canary.

Full PG-backed007 qualification is a later acceptance gate after P01's actual
main landing, not an unfinished implementation task disguised as a green check.

## Source validation evidence

- Code head `1b17f9f9f90af8e415ec536453deccc1b351a297` on base
  `c9c65cc14695732c9badf9f1f7cac795f661d524`.
- Canonical `tests/run.sh -k 'v13_shadow or full_ci_coverage_parity or ci_shadow or ci_cutover'`:
  571 passed, 5054 deselected, on the final code head in task-owned isolated QA.
- Separate standalone clone, no submodule: `tests/run.sh -k v13_shadow`:
  86 passed, 3 skipped, 5536 deselected. Only the three explicitly dependency-bound
  YAML/workflow contracts skip; no PostgreSQL eligibility claim is made.
- `openspec validate qualify-v13-taskset-ci --strict`: valid.
- Local pre-PR panel: coordinator plus nine standard specialists and a CI
  specialist; no actionable findings. Initial reviewers rechecked the final
  shared-helper watch-path addendum. This is not a hosted Codex review receipt.
- Exact diff: seven code paths, no overlap with P01's 18 paths. Workflow/baseline,
  delivery router/verifier/evidence/policy, Mergify, conftest and provider pin
  unchanged. No settings, queue, lane/session, tool, release or assembly changes.

## Publication evidence

- Code PR90: https://github.com/opensoft/openRepoTools-code/pull/90;
  spec PR59: https://github.com/opensoft/openRepoTools-spec/pull/59.
- P01 handoff: assembly issue165 comment6888437385. Owners' prerequisite
  anchors: comments6888099830 and6888103864. Their PR88 remains independent.
- Code ordinary full CI run43671516871 and reusable qualification43671516855
  started on the final code head. Parity passed and all nine normal/no-submodule
  shard jobs started in both calls; full results were still pending at
  publication. No source/full-suite success or real-engine acceptance is
  inferred from these started jobs.
- Explicit `@codex review` requests on both PRs. Hosted bot review status is
  separate from the completed local panel; no waiver or merge is implied.
- Completion of task3.2 means published/read-back source preparation only, not
  full hosted qualification, activation, live canary or assembly adoption.
