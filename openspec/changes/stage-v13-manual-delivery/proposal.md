# Proposal

## Why

The disabled v1.3 delivery proof has landed and P01's required-engine foundation
has real PostgreSQL smoke evidence. The next safe step is a separate manual
heavy-held queue, plus support for an explicitly reviewed linear correction to
an existing test-only cut, without delaying the owners' full-CI product batches.

## What Changes

- Add `openrepotools-sql-register-v13`, a writer-requested, at-most-two-member
  queue with app-bound fast/delivery entry and complete heavy qualification
  before merging. Keep all existing queues unchanged and automatic entry absent.
- Permit a trusted profile to pin up to three exact linear source commits,
  checking every prefix from P01 against the same narrow test/marker boundary.
  Preserve existing single-child profiles and reject unpinned fixes or merges.
- Keep the actual v1.3 delivery flag false and the observer non-authoritative.
  No owner head, body, base, lane or running checkout is changed here.
- Record separate enablement and canary gates, including current owner consent,
  exact-head review, main CI, unchanged foundation and matching landed trees.

## Capabilities

### New Capabilities

- `v13-manual-delivery`: manual heavy-held queue staging and bounded reviewed
  correction lineage for the existing v1.3 test-only delivery profiles.

### Modified Capabilities

None. There are no archived canonical capabilities; this change builds on the
unarchived `prepare-v13-delivery-cutover` contract without rewriting its evidence.

## Impact

Code leg: `.mergify.yml`, v1.3 profile/proof validators, queue and delivery
regressions, and the shared Claude/Codex instruction pointer/runbook. Spec leg:
this proposal, design, requirements, tasks and validation evidence. No test job,
coverage baseline, repository setting, provider/assembly pin, installed tool,
release workflow or active lane changes. Actual enablement and live acceptance
remain subsequent reviewed stages, not consequences of this disabled merge.
