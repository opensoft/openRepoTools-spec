# Proposal

## Why

The owners selected corrected P05/P06 as the independent test-only efficiency
canary after their ordinary P02/P03 batch. That batch updates the PostgreSQL
helper, so enabling the old P01-only freeze or stale P03/P05 bindings would
either refuse all work or misrepresent what was qualified.

## What Changes

- Replace the initial unused pair with exact reviewed P05/P06 source identities,
  linear correction histories, task/marker inventories and confirmed mapping.
  Keep the same two-member, six-file-maximum, named-SQL-test-only boundary.
- Introduce an explicit reviewed foundation epoch: an immutable actual-main
  ancestor, descended from P01, with exact frozen foundation blobs/markers and
  unchanged required-engine workflow/baseline. Later drift remains full fallback.
  Never infer a new epoch from a moving branch or from a clean final diff alone.
- Publish the enablement flag only after actual epoch qualification, exact-head
  owner reviews/full CI and checked source/config publication. Before those
  facts are verified, keep the working flag false and bindings unchanged.
- Run the separately owner-consented two-member canary, preserving all complete
  heavy coverage and recording fast-only source/candidate CI, one accepted heavy
  run and matching actual landed prefix/final trees. Widening follows, not precedes,
  that live acceptance.
- Repair the reproduced reopen-only generated merge hint mismatch: accept a
  different non-null hint only when an authenticated immutable GitHub commit
  read proves its exact ordered parents and tree equal the actual tested merge.
  Keep unknown hints, real base/head changes, strict later rereads and incomplete
  admission on full fallback. This is a bounded amendment to the earlier nullable
  hint rule, not permission to ignore arbitrary metadata differences.

## Capabilities

### New Capabilities

- `v13-qualified-foundation`: explicit reviewed foundation epochs and staged
  initial P05/P06 enablement without widening the existing test-only boundary.

### Modified Capabilities

None archived. The unarchived v13-trusted-delivery and v13-manual-delivery source
contracts remain prerequisites; their historical disabled-publication evidence
is not rewritten into a live acceptance claim.

## Impact

Paired code/spec feature dependent on feature014/code#100/spec#66. Code changes
are limited to the trusted v1.3 profile/foundation validator, exact policy data,
named queue's supported task identities, regressions and shared runbook. No
workflow job/command, baseline, setting, app access, paid upgrade, provider or
assembly pin, installed tool, release, shared checkout or lane binding changes.
Owner branches, PR bodies and retargeting remain with their owners. Actual new
P06 head, owner consent and landed foundation epoch are required external facts,
not values to invent while preparation is parallelized.
