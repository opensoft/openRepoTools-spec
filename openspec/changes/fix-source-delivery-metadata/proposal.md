# Proposal

## Why

The first scoped canary PRs 57/58 fell back to full CI when Sourcery appended
description prose before routing. Full event/live body equality rejects those
otherwise unchanged decisions. The owner approved this narrow repair, checked
code/spec merges and fresh canaries on 2026-11-13; assembly pins stay excluded.

## What Changes

- Compare source-event/live metadata exactly except unrelated description prose.
  Independently require both descriptions to contain the unique supported shadow
  and delivery intents and no dependency declaration.
- Preserve every other snapshot identity, authenticated fast check, Git topology,
  effective protection, final strict live reread and conservative full fallback.
- Add red-first positive description-edit and negative decision/identity/race
  regressions. Do not trust an editing actor or change any workflow permissions.
- After fresh PR60/61 also refused before queueing, add fixed-vocabulary stage
  diagnostics and mismatch field names only. Do not log bodies, exception text
  or API payloads, or relax gates on an unproven explanation.
- Fresh PR65 then proved that the initial snapshot differs on merge_commit_sha.
  Permit only missing/null or the trusted GITHUB_SHA as generated hints at the
  initial source/candidate event comparison. All actual Git/provenance proofs
  remain mandatory; other non-null hints retain full. Candidate bodies stay exact.
- Fresh PR67 reached fast evidence but refused its reread during fast-workflow
  finalization. Reproduce completion during authenticated pending reads and
  return only the existing bounded wait before completed-proof stability checks.
  Fresh successful stable evidence remains mandatory; admission stays success-only.
- Review and qualify the code/spec PRs with full ordinary/reuse CI, verify the
  actual main result, then run a fresh independent two-member canary. Preserve
  the failed initial attempt as failed evidence; never rerun it into acceptance.

## Capabilities

### New Capabilities

- `source-delivery-metadata`: decision-preserving event/live description handling
  for the fixed source scope, with unchanged strict subsequent live checks.

### Modified Capabilities

None in the canonical inventory, which is empty. Existing unarchived cutover,
local-review and activation contracts remain governing context.

## Impact

Code: delivery_route.py, delivery_verify.py, focused routing regressions and the
existing cutover runbook. Spec: this change and feature 009's sole executable task file under
issue 26. No queue/workflow definition, policy flag, artifact/review schema,
baseline, dependency, release, tool, assembly-pin or lane change. Scope remains
independent main-based unchanged-mode claude-current/test edits, not all work.
