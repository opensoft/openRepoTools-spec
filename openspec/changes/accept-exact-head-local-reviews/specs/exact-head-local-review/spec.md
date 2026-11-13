# Exact-head local review

## Purpose

Let the prepared candidate path authenticate an explicit local Codex skill
review acceptance without claiming that GitHub-hosted Codex performed it.

## ADDED Requirements

### Requirement: Authenticated exact-head local acceptance

The verifier SHALL accept a new unedited top-level PR comment containing the
versioned local-review record only when the live publishing user's numeric
identity matches GitHub's permission response and that user currently has write
or admin access. The record MUST bind the fixed repository, exact PR, current
head and reviewed base; name a supported local review skill and Codex-local
reviewer; accept the review with zero unresolved findings; and include findings,
resolution and validation reports. The verifier SHALL NOT infer review from
labels, PR body text, arbitrary report URLs or a quota response.

#### Scenario: Local acceptance without hosted review capacity

- **WHEN** a current repository writer publishes a valid receipt for the exact
  current head/base after running the local review skill
- **THEN** the candidate can satisfy its review condition without a hosted bot
  summary, but still requires all other admission, thread and heavy-CI evidence

#### Scenario: Lookalike or stale acceptance

- **WHEN** evidence names another repository, PR, head/base, edited comment,
  bot, non-writer or mismatched numeric author identity
- **THEN** it SHALL NOT authorize local-review acceptance

### Requirement: Explicit supersession and revocation

The newest authorized local receipt by server creation time and comment ID SHALL
control local acceptance. A stale, invalid, unresolved or revoked newer local
receipt MUST refuse admission rather than falling back to an earlier local or
hosted approval. With no authorized local receipt, the existing hosted Codex
summary path SHALL remain supported. All review threads MUST be fully enumerated
and resolved for either path.

#### Scenario: Revocation supersedes a clean review

- **WHEN** a writer posts a new revoked receipt after a clean local or hosted
  review
- **THEN** admission SHALL refuse until a new valid local acceptance is posted

#### Scenario: Hosted-only compatibility

- **WHEN** no authorized local receipt exists and a genuine clean exact-head
  hosted summary and complete resolved threads exist
- **THEN** the original hosted-review path SHALL continue to qualify

### Requirement: Bounded fail-closed live evidence

Local receipt JSON SHALL have exact keys and types, no duplicate keys, bounded
body size and supported schema/skill. Truncated, saturated, malformed or failed
API reads SHALL refuse. Local identity, report digest, writer permissions and
threads MUST be revalidated with the source at the existing final live checks.
Complete REST and GraphQL comment-ID sets MUST agree; equal counts alone SHALL
NOT establish a consistent history.
Untrusted report text SHALL NOT be executed or fetched.

#### Scenario: Receipt or permission changes during verification

- **WHEN** the accepted receipt is edited, deleted, replaced, revoked, or its
  publishing user loses access during the live verification pass
- **THEN** that verification SHALL refuse; no success alias or skip is granted

#### Scenario: Same-count comment replacement

- **WHEN** REST and GraphQL contain equal numbers of comments and the selected
  acceptance ID, but a different comment was replaced between the reads
- **THEN** the inconsistent history SHALL refuse rather than hide a potentially
  newer local revocation

### Requirement: Preparation is not activation

This delivery SHALL keep the activation flag false, ordinary full CI and all
coverage intact, and leave settings, queue configuration, canaries, releases,
assembly pins and lanes 1–5 unchanged. Publication SHALL leave both PRs open.

#### Scenario: Local tests pass

- **WHEN** receipt regression tests and governance validation pass
- **THEN** this proves source preparation only, not merge, activation, live
  batching, release or installed-session acceptance
