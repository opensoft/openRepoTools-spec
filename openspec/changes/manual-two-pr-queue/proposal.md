## Why

The merged advisory pilot cannot queue changes. Prepare a bounded manual queue
for a later two-PR batching test without relaxing CI or running the test now.

## What Changes

- Add one serial Mergify queue with a maximum batch size of two, explicit
  pilot branch/label/path restrictions and no automatic admission rules.
- Require the existing four GitHub Actions full-CI gates on each original PR
  and on the combined candidate; do not change any workflow or release gate.
- Add offline configuration regression tests and a code-local operator reference.
- Preserve the shadow classifier's full-CI/no-admission semantics.

## Capabilities

### New Capabilities
- `manual-batch-queue`: bounded, explicit full-CI batching configuration.

### Modified Capabilities
None. Feature 008's classifier remains advisory; this separate configuration
never consumes its result as admission authority.

## Impact

Code: .mergify.yml, queue configuration tests and the code-local CI references.
Canonical governance and feature 009 remain in openRepoTools-spec. This follows
feature 008 (code #37, spec #24); PR #34's targeted verification is unchanged.

Authority: Brett explicitly requested opening the config-only PR for a manual
two-PR queue, keeping full CI and preserving lanes 1–5. No canary, enqueue,
merge, ruleset bypass, app access, paid upgrade, release change or assembly pin
advance is authorized. The queue becomes available only after separate merge
authorization and verified service access; configuration is not live acceptance.

## Accepted rollout amendment — 2026-11-11

The original preparation-only authority above is historical. Brett subsequently
approved the qualified docs and real-code batches (#41/#43), and now approved
finishing the rollout: merge code #44 after CI, add the general manual code
queue, and refresh assembly pins and Claude instructions. This extends the same
manual-batch-queue capability, not fast-only CI or automated review authority.
Reconcile amendments under spec issue #26, using feature 009's sole task list.
No unrelated enqueue/merge, bypass, app access, paid upgrade, release change,
native stack object or lane takeover is authorized. Archive after assembly land.

## Accepted coverage-parity preparation — 2026-11-11

Brett approved only the first CI-efficiency prerequisite: implement and open
coverage-parity PRs while ordinary full CI continues. Add a parameterless local
reusable entry point to the existing full workflow, plus a PR-only qualification
caller and an explicit job/shard/environment baseline. Execute the existing jobs,
not a second integration implementation. This narrowly extends the earlier
no-workflow-edit preparation boundary; no existing trigger or job is removed.

Fast-only classification, queue changes/admission, canaries, merges, ruleset/app
changes, releases, tags, assembly pins and active lane/session changes remain
outside this delivery. Reconcile this amendment under the existing issue #26.

## Accepted trusted shadow-classifier preparation — 2026-11-11

Brett approved the next shadow-classifier PR only. Retain ordinary full CI and
lanes 1–5; no canary, queue commands, ruleset changes/bypass, app access or release
changes. Add an independent trusted-main observer beside feature 008's unchanged
head-owned advisory pilot. It reports conservative event observations, never
skip decisions, admission, reviewed status or release eligibility. Reconcile
this bounded prerequisite under issue #26; no merge or assembly advancement.
