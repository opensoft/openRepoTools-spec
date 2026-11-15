# Validation and stage boundaries

This is a source-stage record, not activation, admission, release or complete
deployment acceptance. The v1.3 flag remains false. Existing queues, workflow
jobs/commands, coverage baseline, profile bindings and provider/assembly pins
are unchanged; lanes and owner PRs remain untouched.

## Foundation prerequisite already observed

- P01 source7f1141be landed by its owner asf3576b95; exact tested integration
  treecf57119f matched the landing. Earlier main45 full43717443361 and staged
  P01 integration43718145060 each passed all15jobs.
- Actual real PostgreSQL smoke43722592020 passed on8d3b3528, one added driver
  test on reviewed7f1141be. The unchanged pgcluster helper blob14eb9360 matches
  source, smoke and actual landed main. Real start/clone/SELECT1, stop/restart
  and SELECT1 output was independently read. No smoke ran on the user benches.
- P01 main43721893119 is now COMPLETE SUCCESS, all15jobs; it was still running
  at the initial preparation checkpoint.

## Local source validation

- Isolated QA, non-root, private PID/tmp namespaces and task-only bind mount.
  Provider initialized at unchanged4bf963f0ca416b142652bb608cafe5c76d893faa.
- Initial targeted attempt stopped during collection with OSError12, cannot
  allocate memory; no tests passed in that attempt. No bench restart or resource
  limit change was made. Retried canonical code/tests/run.sh (assembly-relative;
  tests/run.sh from the paired code worktree) passed149tests,
  5648deselected, zero skips,53.00seconds. Selector: ci_v13_delivery or
  ci_v13_shadow or manual_mergify_queue or ci_cutover_manual_queue. The
  ci_v13_shadow term does not select test_v13_shadow; the broader run below
  explicitly selects v13_shadow.
- The broader run first passed845tests and failed one old blanket prohibition
  against the v1.3 scope name anywhere in Mergify configuration. That assertion
  was updated to permit staged queue intent while explicitly forbidding observer
  module/output consumption. Other control checks and the actual false-flag and
  heavy-gate tests remain intact; no job, baseline or coverage was removed.
- Final code436e2dd12c6070c882aac2730f4b9a207cfbda11 passed846tests,
  4951deselected, zero skips,204.90seconds through canonical code/tests/run.sh
  (assembly-relative; tests/run.sh from the paired code worktree):
  `-k 'ci_v13_delivery or ci_delivery or ci_cutover or full_ci_coverage_parity or v13_shadow or ci_shadow or manual_mergify_queue'`.
- Strict OpenSpec validation and code/spec diff checks passed. Local exact-head
  root plus nine-specialist review is clean: architecture, performance, security,
  QA, static/Sonar, contracts, data integrity, reliability and simplicity. This
  is local review, not a hosted-bot, service or live-canary receipt.

## Publication and live follow-through

At source preparation, paired publication and hosted gates were pending. Code
PR100 now has exact-head ordinary full43725295085 SUCCESS15/15 and reusable
qualification43725295022 SUCCESS17/17 at436e2dd, and hosted Codex summary6896204960
is complete with no findings/unresolved findings. SpecPR66's prior34ba8b8 review
was likewise clean; this narrow documentation correction requires fresh review.
Normal source merges and actual landed-main verification are not yet complete.
Record actual handles/results before claiming them. No live queue command was
issued. Owner full-CI run1 P02/P03 is separate; P02's helper change is excluded
from the present efficiency profiles. P05's corrected head2a02cd9e is published
and task-confirmed by its owner; fresh review and canary consent remain pending.
The owners selected corrected P05/P06 conditionally in record6896263828.
Future enablement must reconcile both the owner pair and
the exact foundation, with no silent freeze relaxation or unilateral hold.

## Parallel integrated qualification and corrected dispatch mode

Owner full-CI run1 candidate#101 is34e2311b. A separate coordinator staging
commit8872d255 merges that unchanged candidate with reviewed source436e2dd,
producing treecdf132930263ac906433a9d05e37860dae7e1d11. It is NEVER a merge or
admission target. Targeted verify43727181668 passed846tests with zero skips.

The first additional full dispatch43727216258 was mistakenly made on the
coordinator's verify/ ref. Existing T280 deliberately leaves all verify/ refs
ungated, even for tests.yml, so it ran future unlanded SQL commands and failed.
Only that coordinator run was cancelled. It is not a normal integration receipt
and is not attributed to the owner's candidate. No test, gate, override variable
or workflow was changed. The identical commit/tree is now undergoing normal-mode
full dispatch43729267642 on qualify/coord/v14-on-run1-34e2311b; that result is
still pending, not claimed successful. Its evidence can be used only after actual
owner/config landing-tree reconstruction matches exactly. The running owner
candidate is not reset, cancelled, edited or held by this source stage.
