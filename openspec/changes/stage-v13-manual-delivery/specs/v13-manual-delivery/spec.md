# Spec Delta

## Purpose

Stage a manual heavy-held v1.3 test-only queue and recognize explicitly reviewed
linear fixes without granting omission, owner edits or admission from metadata.

## ADDED Requirements

### Requirement: Manual queue preserves the heavy gate

The system SHALL provide a separate manual v1.3 queue, limited to at most two
independent main-targeted same-repository ready members in the existing test-only
scope. Entry SHALL require repository-writer permission, matching v1.3 intent,
supported task-set identity and GitHub Actions fast/delivery success. Merging
THROUGH THIS MANUAL QUEUE SHALL require the authenticated complete batch-heavy
check. Fast-only sources MUST use this queue and MUST NOT be direct-merged;
that queue-only usage is an operator requirement, not a claim that the existing
delivery-only service protection prevents every outside-queue merge. This stage
SHALL NOT introduce or claim a repository-wide heavy-check requirement. No automatic entry,
retry, failure split, existing-queue edit or protection-injection weakening SHALL
be added. Eligibility SHALL NOT grant admission, review or merge authority.

#### Scenario: Fast source without heavy proof
- **WHEN** an eligible source has fast and delivery success but its candidate has no successful batch-heavy proof
- **THEN** the queue cannot merge the source

#### Scenario: Wrong domain or dependency
- **WHEN** a source changes a foundation/control/product file, comes from a fork, remains dependent, or lacks a supported opt-in
- **THEN** the manual v1.3 queue rejects it and ordinary full CI remains the fallback

### Requirement: Reviewed linear correction lineage is bounded

A trusted test-only profile MAY explicitly bind one to three unique full source
commit identities, ending at its exact registered head. The system SHALL verify
each commit has exactly the previous registered parent, starting at the frozen
P01 head, and every cumulative prefix obeys the existing cut's regular-file and
exact empty-marker inventory. Missing lineage SHALL mean the existing single
P01 child only. Unpinned, reordered, duplicate, overlong, merge or out-of-scope
histories SHALL refuse observation/proof; exact-head review and all rereads SHALL
remain mandatory. A policy edit alone SHALL NOT establish owner acceptance.
For an explicit pinned lineage, this requirement supersedes the single
"original parent" condition in `prepare-v13-delivery-cutover`'s
`v13-trusted-delivery` requirement: that original-P01-parent condition SHALL
apply to the FIRST pinned commit, not the final registered head. Every later
pinned commit SHALL have only the immediately preceding pinned commit as parent.

#### Scenario: Reviewed correction to a test gate row
- **WHEN** the trusted policy pins an original valid child and its exact linear test-only correction and the current head matches the last commit
- **THEN** the same cut can be recognized without rewriting the owner's branch or relaxing its marker, path or foundation boundaries

#### Scenario: Temporary off-scope intermediate revision
- **WHEN** an intermediate pinned commit modifies a control file and a later commit restores it
- **THEN** lineage verification refuses even if the final diff looks in scope

### Requirement: Staging does not activate delivery

This source stage SHALL leave the v1.3 delivery flag false, the observer
non-authoritative, all ordinary full CI and required-engine coverage intact, and
owner PRs untouched. Actual enablement SHALL require a later reviewed exact-head
binding and unchanged qualified foundation. A live canary SHALL require fresh
owner consent and readiness, fast-only source/candidate CI, one complete heavy
caller per accepted batch and matching tested/landed prefix and final trees.

#### Scenario: Queue configuration lands while disabled
- **WHEN** this staged configuration merges and proposed v1.3 headers are present
- **THEN** v1.3 routing still retains full CI and refuses candidate proof; no queue command or owner edit follows

#### Scenario: Owner full-CI work advances the foundation
- **WHEN** an ordinary owner batch changes the frozen database helper
- **THEN** efficiency refuses until that foundation revision is separately reviewed and qualified; no hold or implicit re-anchoring is imposed
