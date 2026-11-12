# Proposal

## Why

The published fast feedback and non-gating candidate caller do not reduce CI:
ordinary source, candidate-PR and main-push checks still run the complete suite.
Prepare the actual routing and qualification contract without activating it or
weakening today's required full checks.

## What Changes

- Implement a trusted-main, disabled-by-default router for the existing fixed
  claude-current/test scope and one or two independent main-based members.
- Preserve the complete full-suite entry and all thirteen original integration
  jobs, commands, environments and matrices. Add control jobs and narrowly
  checked routing dependencies/guards; do not rehash a reduced coverage baseline.
- Require both explicit future activation and actual effective enforcement of
  the app-bound new delivery check, with all four legacy required contexts
  absent, before any full work can be omitted. Unknown configuration, policy,
  rules, metadata, artifacts or provenance retains full CI.
- Add immutable, run/attempt-bound admission and completed-candidate evidence;
  independently prove main landing prefixes instead of treating now-closed PRs
  as open admission requests or inventing a full-test success status.
- Add a separate manual claude-current queue that waits for a top-level heavy
  qualification result. Existing queues and their full requirements stay intact.
- Document the exact future required-check migration, operator-only queue policy,
  review-capacity requirement, activation, fallback and rollback. Apply none of
  those service settings, dispatch no canary and leave releases untouched.

## Capabilities

### New Capabilities

- `ci-delivery-cutover`: conservative source/candidate/landing CI routing with
  disabled activation, complete heavy coverage and explicit manual admission.

### Modified Capabilities

None in the archived capability inventory. This full proposal builds on the
unarchived manual-two-pr-queue preparation without rewriting its approvals.

## Impact

Code: CI-only Python, JSON, workflow and queue configuration, offline regression
tests and shared Claude/Codex operator instructions. No installed CLI, package
dependency, shape copy, pin, active-plan or running session change.

Governance: one handoff to existing feature009's sole Speckit task list, under
reconciliation issue26. Source baselines are code59c26e8 and spec8813fa9.
Code PR51 independently owns Sonar instrumentation and the full-CI baseline;
preserve its changes and coordinate/refetch the reviewed baseline before landing.

Brett approved preparing/opening this cutover PR only. No merge, flag enablement,
live canary, rule application/bypass, app access, paid upgrade, release/tag,
assembly adoption or lane mutation is authorized by this proposal.
