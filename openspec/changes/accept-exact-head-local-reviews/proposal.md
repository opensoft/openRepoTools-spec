# Proposal

## Why

The prepared candidate verifier accepts only hosted Codex summaries, while the
owner uses Codex's local review skills. Local review needs an explicit auditable
receipt rather than a quota workaround or a fabricated hosted-bot approval.

## What Changes

- Add an alternative local-review receipt, bound to repository, PR, exact head
  and reviewed base, published as a new top-level PR comment by a repository
  writer accepting the local review report.
- Authenticate the comment's author against live GitHub permissions, reject
  edited/stale/malformed receipts, and retain complete resolved-thread checks.
- Let the newest authorized local receipt supersede earlier local decisions;
  an explicit revocation or invalid newer local receipt cannot fall back to an
  old hosted approval. Preserve hosted review when no authorized local receipt
  exists.
- Add regression coverage and one shared operator procedure. Keep activation
  false, all ordinary full CI and existing queue/service/settings unchanged.

## Capabilities

### New Capabilities

- `exact-head-local-review`: authenticated local-review acceptance and revocation
  for the prepared claude-current candidate path.

### Modified Capabilities

None in the canonical capability store (currently empty). This change explicitly
supersedes the managed-only admission clause of
`prepare-claude-current-ci-cutover`; the older approval remains historical.

## Impact

Code: `.github/ci/delivery_verify.py`, focused tests and shared CI instructions.
Governance: feature 009 and this full spec-leg proposal, reconciled under issue
26. GitHub reads gain only the existing Metadata:read permission lookup; no
workflow permission, app access, settings or secret changes. The owner authorized
implementation, tests and opening the paired PRs, not merges or activation.
