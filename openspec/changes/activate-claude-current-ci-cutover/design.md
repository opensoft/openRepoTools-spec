# Design

## Context

See proposal.md. The prepared code and local-review contracts have landed;
newer Sonar repairs are inherited from code 22aac02 and spec 9311051. At the
approval baseline the flag was false and ruleset 25966308 required four legacy
contexts. Brett's 2026-11-13 approval authorizes this operational phase only;
the dated activation record distinguishes actual operations from this design.

## Goals / Non-Goals

Qualify the already implemented narrow route against the real service. Do not
redesign routing, generalize its scope, change artifact/review trust or substitute
source tests for live acceptance. No assembly pin, installed tool, active plan,
session, release, app-access, paid-plan or bypass change belongs to this phase.

## Decisions

### Migrate with the flag off

Read and retain the complete target ruleset plus effective/classic branch
metadata. Guard its unchanged ID, active/main-only condition, empty bypass list
and exact legacy contexts. Replace only that rule's required-status-check list
with delivery bound to integration 15368. Preserve strictness, conditions, name
and every unrelated rule/field; re-read both the target and effective rules.
Do not disable enforcement or add a bypass. A true flag before migration would
still retain full work, but the explicit off-first order makes the act auditable.

### Enable through a narrow reviewed source PR

Change the existing policy boolean and its committed-state expectation. Keep
disabled/no-network and invalid/missing-evidence tests independent of the live
committed default. Retain the original full-job baseline, workflows, candidate
and review readers, every test command, dependency pin and release file.
Update shared agent instructions and one feature-local activation record.

The configuration PR is outside the efficient path and must run full ordinary
and reusable qualification. Actual main CI must also pass before the canary.
Review the exact current code/spec pair using the local review skill; request
hosted review too, but report quota replies honestly. No forged managed receipt.

### Exercise failure/recovery before the successful live batch

Run the existing refusal/revocation/provenance and successful-recovery regression
fixtures offline, non-root, through tests/run.sh in a disposable bounded existing
bench image. These are local failure/recovery evidence, not live queue evidence.
Keep the successful live canary separate: two independent main-based PRs only,
one modifying claude-current and one adding its focused regression coverage.
Each change must be safe alone, exact reviewed heads remain fixed while queued,
and each receives an explicitly accepted current-head/base local review receipt.

Wait for real fast and delivery success, then request the named manual queue
on both members close together. Prove actual membership/order and one complete
heavy candidate, including live final review rechecks. Compare candidate prefix
trees and actual landed prefixes/final tree, then inspect every ordinary source,
candidate-PR and main run. Missing, failed, duplicate, reset or full-fallback work
is a failed efficiency acceptance, not a clean result. Never rerun solely because
observation expires or edit a live queue branch to force success.

## Risks / Trade-offs

- Delivery can mean fast source verification, not heavy integration: the owner
  accepts manual named-queue-only operator policy. Direct fast-source merges
  are forbidden by instructions, not claimed impossible in GitHub settings.
- Concurrent main changes or ambiguous provenance: stop qualification, preserve
  lane work and report invalidation rather than claim one heavy run.
- Unknown live job names/timing, permissions, service/billing constraints or
  missing artifacts: retain full/fail closed and stop for any new authority.
- Sonar's measured assembly acceptance is separate work. Do not alter PR361,
  update pins or label a neutral quality gate as passing.

## Migration Plan

The sole executable checklist is feature 009 T036-T041. Record actual settings,
heads, CI URLs and tested/landed trees as evidence, not executable instructions.
On uncertainty, stop and retain evidence. A reviewed policy-false PR resumes
full work under the honest delivery gate; restoring legacy required contexts
requires its own explicit setting authority. Do not archive before coherent
assembly adoption, which is excluded from this approval.
