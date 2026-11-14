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
  remain mandatory; other non-null hints retain full. Later candidate bodies stay exact.
- Fresh PR67 reached fast evidence but refused its reread during fast-workflow
  finalization. Reproduce completion during authenticated pending reads and
  return only the existing bounded wait before completed-proof stability checks.
  Fresh successful stable evidence remains mandatory; admission stays success-only.
- Apply the same no-proof waiting boundary to an authenticated candidate caller
  with no admission artifact yet. Reproduce queued-caller evolution during the
  empty-list read; preserve default-reader refusal and strict real-artifact proofs.
- R5 sources 71/72 qualified fast-only and entered one genuine two-member batch,
  but draft 73 selected full fallback. Edit history confirms a description edit
  before routing; the generic log does not identify the exact first predicate.
  Reproduce initial candidate-description differences and permit only descriptive
  string/null prose to differ at that first comparison. Keep genuine queue
  identity, real admission, independent source reviews and all final rereads.
  Add fixed-vocabulary candidate mismatch field diagnostics, never body values.
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

Code: delivery_route.py, delivery_verify.py, delivery_evidence.py, focused routing regressions and the
existing cutover runbook. Spec: this change and feature 009's sole executable task file under
issue 26. No queue/workflow definition, policy flag, artifact/review schema,
baseline, dependency, release, tool, assembly-pin or lane change. Scope remains
independent main-based unchanged-mode claude-current/test edits, not all work.
