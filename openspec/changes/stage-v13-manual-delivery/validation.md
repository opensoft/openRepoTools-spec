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
- New P01 main43721893119 remains running at the preparation checkpoint; it is
  not claimed complete by this record.

## Local source validation

- Isolated QA, non-root, private PID/tmp namespaces and task-only bind mount.
  Provider initialized at unchanged4bf963f0ca416b142652bb608cafe5c76d893faa.
- Initial targeted attempt stopped during collection with OSError12, cannot
  allocate memory; no tests passed in that attempt. No bench restart or resource
  limit change was made. Retried canonical tests/run.sh passed149tests,
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
  4951deselected, zero skips,204.90seconds through canonical tests/run.sh:
  `-k 'ci_v13_delivery or ci_delivery or ci_cutover or full_ci_coverage_parity or v13_shadow or ci_shadow or manual_mergify_queue'`.
- Strict OpenSpec validation and code/spec diff checks passed. Local exact-head
  root plus nine-specialist review is clean: architecture, performance, security,
  QA, static/Sonar, contracts, data integrity, reliability and simplicity. This
  is local review, not a hosted-bot, service or live-canary receipt.

## Publication and live follow-through

Paired PRs, exact-head hosted review, ordinary full CI, reusable full
qualification, normal merges and actual main verification are not yet complete.
Record actual handles/results before claiming them. No live queue command was
issued. Owner full-CI run1 P02/P03 is separate; P02's helper change is excluded
from the present efficiency profiles. P05's corrected head, review and fresh
consent are pending. Future enablement must reconcile both the owner pair and
the exact foundation, with no silent freeze relaxation or unilateral hold.
