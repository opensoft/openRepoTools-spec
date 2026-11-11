## Context

Feature 008 supplies advisory classification only. Existing tests.yml runs four
required full-CI gates on all PR bases, including draft queue candidates. Code
PR #34 supplies targeted feedback, not integration acceptance. See proposal.md.

## Goals / Non-Goals

Prepare one manual, documentation-only queue for a separately authorized live
test. Do not implement CI efficiency, an authoritative classifier or new status
receipts, or touch the existing workflows, lane records or assembly pins.

## Decisions

- Follow the existing OpenBenches manual queue pattern: serial mode, one active
  candidate, maximum batch size two, 60-second fill window, no automatic retry
  or failure splitting. Use a 90-minute check budget for the existing full suite.
- Admit only same-repository, open, non-draft pilot branches with a manual
  `batch-pilot-reviewed` label and one or both code-local pilot reference files.
  Bases are main, or the pilot prefix with an explicit Depends-On body marker;
  Mergify must still recognize the actual chain. This supports ordinary PRs,
  not GitHub registered stack objects or a cross-repository atomic batch.
- No auto-merge setting, automation rule, or queue checkbox. Queueing requires
  an explicit queue command from a repository writer. The label is a maintainer
  attestation, not machine-verifiable Codex evidence; the operator must verify
  current heads/bases/reviews and authorization before labeling or queueing.
- Require guard-launch-mode, tests, tests-no-submodule and package from GitHub
  Actions before admission and on the combined candidate. The existing PR
  workflow provides candidate validation; no workflow edit or CI skip is needed.
- Reset on external merges and refuse externally edited queue branches. Do not
  impersonate an account or use a direct-ref/fast-forward merge method.

## Risks / Trade-offs

- Batch size is a maximum, not a promise of exactly two -> future operator
  checks actual membership and candidate identity; singleton/retry is no clean
  two-PR canary. Full per-PR CI remains in addition to batch CI.
- A label can survive pushes -> remove it on a changed head/base, verify again,
  then explicitly requeue. This stage does not claim an automated review gate.
- Service access/plan/rules may block -> stop for separate approval; a schema
  validation or green config test proves neither installation nor live queue use.

## Migration Plan

Open the code configuration PR with a spec companion; do not merge or enqueue.
If later authorized, land by ordinary checks/review, verify existing app access,
then obtain separate canary approval. No labels/branches/queue commands for a
canary are created now. Reverting the config removes the queue; no release or
ruleset configuration requires restoration. Archive only after assembly landing.

## Accepted general-code rollout — 2026-11-11

Append `openrepotools-code`, retaining earlier queues and the same two-member,
serial, full-CI settings. Only independent same-repository open non-draft PRs
targeting main and known normal code paths qualify. Control-plane, release-stamp
and unknown/mixed paths refuse. No branch rename, review label, custom success
status, autoqueue or installed agent is needed. The writer checks actual
head/base/review/CI and merge authority before the named command. That is manual
attestation, not service-enforced Codex proof; a head push requires fresh evidence.

#44 removes only label-triggered duplicate tests.yml runs, never code-update or
candidate checks. Ordinary post-merge main CI stays. The assembly points into
one shared code-local reference and advances merged gitlinks/pin digests/workflow
SHAs in lockstep through bump-leg.py. Qualify composed exact pins before local
activation. Existing lanes and installed sessions remain unchanged. Reconcile
the dated as-built evidence under issue #26; preserve original approval history.

## Coverage-parity prerequisite — 2026-11-11

Expose tests.yml through an empty workflow_call event, with no inputs, secrets,
outputs or conditions that could select reduced coverage. Keep its six job
definitions (13 expanded jobs), environment and existing events unchanged.
A local PR-only qualification workflow calls that same file at the caller's
commit, after parity tests, and fails its aggregate unless the entire call
succeeds. No queue-branch trigger, dispatch, secret inheritance or status writer
is introduced. Ordinary full CI remains independent and mandatory.

Freeze the parsed environment/job definitions from merged code baseline
d3c55e55aa3516cbaa1d68a77061ddd50191d49a in a reviewed JSON inventory. Baseline
changes require explicit review; future additions to tests are still discovered
by the existing shard planner, not an enumerated allowlist. Mutation tests reject
missing/conditional jobs, altered steps, matrices, checkout modes and aggregators.
Keep the current missing-report, failed/cancelled-shard and collection-parity
regressions. This is source/hosted reuse qualification, not a live queue test.
