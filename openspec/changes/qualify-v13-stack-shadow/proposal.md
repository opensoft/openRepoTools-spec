# Proposal

## Why

Real P03/P05 task cuts are now PR93/92, each a single child of P01's exact
head rather than current main. The disabled observer cannot inspect that
topology, and owners must not flatten or rewrite their work to fit a pilot.

## What Changes

- Bind the disabled projections to the actual source PRs, immutable heads and
  branches, alongside the existing P01 anchor.
- After P01's exact merged contract is on main, observe only its known child
  cuts on their original prerequisite base or owner-retargeted main base.
- Accept only a resolved, exact P01 dependency and unchanged cut paths on main;
  reject arbitrary stacks, anchor drift, context collisions and moving inputs.
- Cover PR and source-push observations with realistic normal-merge Git
  histories and mocked bounded reads. Retain all full-CI/no-authority invariants.
- Update the runbook and canonical qualification evidence boundaries, without
  changing ordinary/reusable full CI, queues or any active lane.

## Capabilities

### New Capabilities

- `v13-stack-shadow`: read-only observations of exact prerequisite-anchored
  source cuts, complementing the generic disabled v13-ci-shadow change.

### Modified Capabilities

None in the main capability inventory. The earlier unarchived
qualify-v13-taskset-ci change remains the no-authority baseline. Its full-CI,
disabled-efficiency and independent-source behavior are preserved.

## Impact

Code: v13_shadow.py, its disabled JSON projection, focused regressions and the
existing v1.3 runbook. Spec: this canonical OpenSpec change only. Coordination:
assembly issue165 comment6889436296, with P01/P03/P05 owners. Preserve their
heads/bases/metadata and all lane1–5 work. No P01 merge or retarget, canary,
activation, bypass/settings, app access, paid upgrade, release/tool change,
assembly pin adoption or shared-checkout refresh is included. PostgreSQL-backed
integration qualification still requires P01's actual owner-controlled landing.
