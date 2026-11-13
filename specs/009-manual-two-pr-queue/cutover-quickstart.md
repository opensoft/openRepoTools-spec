# Preparation verification and future operation

From the isolated code worktree in the owning Linux QA environment, run:

```bash
bash tests/run.sh -k 'ci_local_review or ci_cutover or ci_candidate_shadow or ci_delivery or ci_shadow or ci_pilot or full_ci_coverage_parity or tests_workflow or ci_shards'
actionlint .github/workflows/tests.yml .github/workflows/full-ci-parity.yml .github/workflows/task-set-candidate.yml .github/workflows/task-set-candidate-shadow.yml
git diff --check
```

From the isolated spec worktree run strict OpenSpec validation of
prepare-claude-current-ci-cutover and accept-exact-head-local-reviews. Publish only
code/spec PRs and locally review the exact heads through the named skill. For a
future scoped candidate, an authorized writer's explicit exact-head/base local
receipt can satisfy review instead of a genuine hosted summary; quota replies
and generic summaries do not. Local checks are not hosted/reuse/live acceptance
or merge authority.
Do not refresh shared checkouts, select a new active plan or alter running lanes.

The code runbook `.github/ci/delivery-cutover.md` is the shared Claude/Codex
operator source: required-check proposal, changed source floor/manual-only caveat,
hosted/local review receipt prerequisites, disabled flag, future separately approved activation/canary,
failure/recovery, exact tested/landed prefix trees and rollback. No actual setting,
queue command or live canary belongs in this preparation checklist.
