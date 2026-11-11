## Why

Five development lanes need smaller reviewable task sets without repeatedly
paying full integration cost for every intermediate PR. First qualify a narrow,
advisory classifier; removing checks or activating a merge queue is not approved.

## What Changes

- Add a CI-only shadow classifier for changes confined to the code leg's
  `claude-current` and `tests/test_claude_current.py`.
- Report current PR head/base identities and fail closed on mixed, missing,
  foreign, draft, closed or uncertain context. Every result still requires full CI.
- Add a shared Claude/Codex instruction reference in the code leg, retaining
  assembly ownership, canonical spec placement and the existing lane protocol.
- Coordinate with code PR #34: its targeted `verify` workflow remains separate,
  unmodified, and never substitutes for full or exact-pin composed acceptance.

## Capabilities

### New Capabilities
- `ci-shadow-scope`: advisory classification without queue admission or CI skips.

### Modified Capabilities
None. Existing execution, review, merge and release gates remain in force.

## Impact

Implementation: `opensoft/openRepoTools-code`, separate `ci-pilot.yml`, a
standard-library classifier, offline tests, and scoped agent instructions.
Governance and feature 008 live in `opensoft/openRepoTools-spec`.
No live canary, Mergify configuration, app access, ruleset bypass, release edits,
assembly pin update, session messaging, lane reassignment or running-bench change.

Authority: Brett's explicit instruction to implement the narrow CI-only pilot,
coordinate with PR #34, and add shared instructions, preserving lanes 1–5.
