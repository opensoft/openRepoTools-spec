# Proposal

## Why

The accepted efficiency path covers only claude-current. Real v1.3 SQL-register
cuts add tests and task markers and depend on P01's real-engine/gate foundation.
We need conservative observations and coverage qualification before considering
any omission of ordinary CI for that work.

## What Changes

- Add an isolated, trusted-main v1.3 shadow observer and disabled profile
  projection for planned P03 and P05. No output grants omission or admission.
- Observe P01 PR88 against owner-confirmed immutable head/control blobs; distinguish
  pending, changed and merged-contract observations from actual PG CI acceptance.
- Cover additions, modifications, empty task markers, topology and stale/error
  cases with local Git histories and mocked read APIs.
- Add the new control paths to the existing parameterless full-CI qualification
  caller's path filter, without changing that caller's jobs or tests.yml.
- Document the remaining case-inventory/PG qualification and activation gates.

## Capabilities

### New Capabilities

- `v13-ci-shadow`: disabled, read-only SQL-register cut observations and explicit
  prerequisite/coverage qualification boundaries.

### Modified Capabilities

None. Feature007's full-CI, real-engine and temporary landing-gate requirements
remain unchanged. The existing claude-current efficiency path is untouched.

## Impact

Code: new CI observer, disabled JSON projection, additive workflow, regression
tests and runbook; qualification caller path filter only. Spec: this canonical
OpenSpec change, with no second task ledger. Coordinate via root issue165 with
P01 owners (receipts6888099830 and the lane1 response to6888083282).
Do not change P01's branch, tests.yml, full-ci-baseline.json, conftest/fixtures,
gate, markers, queues, required checks, releases, installed tools or live lanes.
This change opens paired PRs only; no merge, activation, live canary, bypass,
app access, paid upgrade, assembly pin or shared-checkout change is authorized.
