# Design

## Context

See proposal.md for motivation. The existing candidate verifier rereads live
source PRs, fast evidence and review/thread snapshots at admission and at heavy
completion. Routing is disabled; this change does not alter any CI job graph.

## Goals / Non-Goals

Accept auditable exact-head local skill review without hosted quota dependence.
Do not grant merge authority, claim independent human approval, change GitHub
required checks, activate routing, enqueue anything or refresh a lane checkout.

## Decisions

1. Use a new top-level PR issue comment, not a label, PR body, committed receipt
   or custom success status. GitHub authenticates the publishing user; the live
   collaborator-permission endpoint must confirm that same numeric user ID and
   login has legacy permission `write` or `admin` (maintain maps to write).
   Authoring a report is not sufficient without that writer's acceptance.
2. The complete comment is an exact marker followed by one JSON fenced block.
   Schema v1 fixes repository, PR, head/base SHA, `reviewer: codex-local`, named
   review skill, accepted/revoked status, unresolved-findings count, and nonempty
   findings, resolution and validation reports. Reject duplicate/unknown keys,
   wrong types, unsupported schema/skill and bodies over 16 KiB. No URLs are
   fetched from report text. Never execute or interpolate report content.
3. Comment numeric ID, issue URL and permalink must identify this PR. Require
   unedited server timestamps (`created_at == updated_at`) plus complete GraphQL
   comment metadata with `lastEditedAt: null`; timestamps alone miss same-second
   edits. Replacements and
   revocations are new comments, not edits. At most ten local-marker comments
   and fewer than 100 total comments are considered; saturation refuses.
4. Ignore bot/non-writer local lookalikes. Order authorized local comments by
   server creation time then numeric ID; the newest controls the decision.
   Invalid, stale or revoked newest local evidence refuses even if a hosted
   approval exists. With no authorized local receipt retain the hosted path.
5. Preserve resolved-thread completeness and compare the normalized local
   receipt identity/body digest/permission plus threads on the existing live
   recheck. New edits, deletion, revocation, changed writer access or source
   identity during verification refuse. An API read failure never authorizes.
6. Keep existing immutable candidate artifacts and historical reader schemas
   unchanged. A successful heavy verifier attests the then-current review;
   historical landed-tree proof does not reopen now-closed source admission.

## Risks / Trade-offs

- A writer could attest a review that did not occur -> document that this is
  authenticated operator attestation, not cryptographic proof of local model
  execution or independent human approval. Existing GitHub controls still apply.
- Writer access changes or permission reads fail -> refuse candidate admission;
  never add permissions, grant access or silently weaken the test requirement.
- Bounded history can refuse a busy PR -> use normal full-CI delivery rather
  than truncate evidence or treat an old label as acceptance.
- A reviewer discovers a problem after acceptance -> publish a new revoked
  receipt and stop queue operations. Resolved threads remain mandatory.

## Migration Plan

Open paired code/spec PRs with ordinary full CI. Keep `enabled: false`, workflow
definitions, queue configuration, legacy required checks and assembly pins byte
identical. Source merges, settings migration, enablement, live qualification and
session adoption require later authority. No archive in this preparation act.

Rollback of the source contract uses an ordinary reviewed revert; no runtime
or settings rollback is needed while disabled.
