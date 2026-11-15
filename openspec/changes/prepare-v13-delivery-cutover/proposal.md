# Proposal

## Why

The v1.3 observer cannot authorize omission or candidate qualification. Prepare a separately disabled trusted delivery path for the first two immutable task sets, reusing the proven review, fast-run, heavy-run and historical landing checks without reducing coverage.

## What Changes

- Add an independently disabled v1.3 delivery policy; the existing claude-current policy stays enabled and unchanged.
- Bind eligible v1.3 delivery to P01's merged required-PostgreSQL contract and the reviewed P03/P05 source heads, paths and exact empty task markers.
- Extend trusted source, candidate and historical landing verification with scope-bound proof records. Unknown, mixed, dependent or stale sources retain full CI.
- Keep all ordinary integration jobs, reusable qualification jobs and Mergify configuration unchanged. Add only the new control files to the existing qualification caller's PR path filter. Document the separate future activation and manual queue approval gates.
- Add offline positive/negative regressions, including simulated enablement only in disposable fixture repositories.

## Capabilities

### New Capabilities

- `v13-trusted-delivery`: Disabled-by-default, scope-bound delivery verification for the first reviewed v1.3 cuts.

### Modified Capabilities

None. There are no published OpenSpec capability specs in this leg; existing delivery behavior remains a compatibility requirement.

## Impact

Code-local trusted CI Python helpers, one disabled policy, regression tests, shared instructions and additive qualification-trigger paths; canonical proposal/spec/design/tasks in this spec leg. No integration workflow commands/jobs, baseline, Mergify queue, settings, app access, paid upgrade, release, provider pin, assembly pin, lane/session or shared-checkout changes. Source merges require latest-head full CI, reusable qualification and exact-head review; source publication is not activation or live acceptance.
