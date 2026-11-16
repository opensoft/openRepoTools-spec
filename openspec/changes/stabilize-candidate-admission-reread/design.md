# Design

## Boundary

The artifact reader validates the authentic caller, bounded artifact inventory,
repository/head/run identity, digest, ZIP member, JSON schema and proof before
its final byte-strict artifact and caller rereads. Currently both rereads share
one hard refusal. The final candidate verifier already treats authenticated
caller movement as a pending observation, but that code is never reached when
movement occurs earlier inside this reader.

## Decision

Split the final rereads without weakening either. Artifact metadata movement
always refuses. If `allow_pending` is true, phase is admission and caller bytes
changed, authenticate the current caller again at the same ID, candidate head,
queue branch, repository/workflow/event/actors and first attempt. Require valid
queued/in-progress-without-conclusion or successful completed state. Invalid
identity/state refuses immediately. An authentic moved caller raises pending,
returns no record/proof and requires a whole fresh observation. Strict equality
is still required to return a record. All non-pending and qualified/historical
reads retain immediate refusal. No cache or acceptance of stale views is added.

Use the existing30-by10-second bounded observer unchanged. Exhaustion retains
full CI. A new observation must re-read the real archive and both sources,
reviews, fast evidence, branch/main and current caller; changed proof is not
substituted at the final gate. Add fixed diagnostic names, never API values,
private body/title text or arbitrary exception messages.

## Verification and rollout

First demonstrate failing read-two movement regressions against unchanged code.
Retain existing read-three movement, artifact/identity forgery, missing receipt,
failed/rerun caller, source no-retry, original coverage and historical contracts.
Run focused canonical tests in task-only QA, exact-head expert/Codex review,
ordinary full and reusable CI, then normal paired source merges and actual-main
readback. Fresh owner consent/events and a new two-member canary follow; the
stopped specimen remains failed. No rule, release or assembly adoption follows.
