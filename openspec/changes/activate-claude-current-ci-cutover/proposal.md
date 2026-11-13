# Proposal

## Why

The prepared cutover and exact-head local-review contract are source-qualified
but disabled. Brett Heap explicitly approved the required-check migration,
reviewed enablement PR and two-PR test on 2026-11-13, so the initial scoped
efficiency path now needs live qualification rather than another preparation.

## What Changes

- **BREAKING source merge floor:** replace the four legacy required contexts
  in repository ruleset 25966308 with GitHub Actions-bound
  `openrepotools-delivery` (integration 15368), preserving every other setting.
  Keep the flag false throughout the service migration.
- Enable only the existing `claude-current` scope through a reviewed, fully
  qualified config PR. Update its committed-state expectation without reducing
  disabled, uncertain-proof or full-fallback coverage.
- Make the shared Claude/Codex instructions describe the authorized phase and
  mandatory named manual queue. Source delivery can mean fast verification;
  queue-only usage remains operator policy, not GitHub-enforced exclusivity.
- Run scoped failure/recovery checks and one two-member independent-main-based
  live canary. Require fast-only member/candidate PR CI, one complete heavy
  integration, no duplicate full landing run, and matching prefix/final trees.
- Preserve all original full jobs, review authentication, unchanged-mode path
  restrictions, opt-ins and fallback behavior. Do not widen eligibility.

## Capabilities

### New Capabilities

- `ci-delivery-activation`: owner-authorized migration, reviewed enablement and
  evidence-based live acceptance of the prepared narrow delivery path.

### Modified Capabilities

None in the canonical capability inventory, which is empty. This phase builds
on the unarchived `prepare-claude-current-ci-cutover` and
`accept-exact-head-local-reviews` contracts without rewriting their history.

## Impact

Code scope: activation JSON, its tests, shared delivery/cutover instructions and
one feature-local phase record. Governance remains in this spec leg and hands
off to feature 009's sole executable task list under reconciliation issue 26.
Baseline: code `22aac02ba5cfcb2777e4bf05a574e26c555bd38b`, spec
`9311051d340a64b81b94f70dff4ea66809112de6`; preserve the newer Sonar work.

Authority is Brett's explicit scoped approval, not passing tests or a persistent
goal. No bypass, app access, paid upgrade, release/tag, assembly-pin, installed
tool, active-plan or running-lane change is authorized. Stop if those become
necessary. Assembly adoption and measured Sonar acceptance remain separate.
