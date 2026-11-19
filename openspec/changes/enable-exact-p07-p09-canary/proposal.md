# Proposal

## Why

P07 and P09 have reviewed immutable source histories and green preparation
checks, but the live test-only queue admits only P05/P06. The next approved
canary needs an exact-pair path that preserves both source heads and bodies.

## What Changes

- Add a separate manual queue for code PRs #97 and #103 only. Trusted-main
  admission must prove the exact P07-before-P09 pair before heavy CI starts.
- Reuse the verified full-suite workflow and exact-head review checks. A
  candidate with a singleton, unknown member, stale evidence or different
  prefix tree cannot qualify for the two-member canary.
- For this pair only, verify P07 preserves the frozen test foundation and
  P09 makes only the two explicitly reviewed foundation changes. Verify
  matching landed prefixes, whether GitHub delivers one push or two.
- Record the actual fresh fast-only, combined heavy and landed-tree evidence.
  Preserve full fallback and the existing P05/P06 and other queues.

## Capabilities

### New Capabilities

- `exact-p07-p09-canary`: trusted exact-pair source, candidate and landing
  qualification for the P07/P09 v1.3 canary.

### Modified Capabilities

None.

## Impact

Limited to the code-leg CI classifier, verifier, proof reader, narrow Mergify
queue and tests, with this contract in the spec leg. No new permissions, app
access, ruleset changes, paid features, workflow coverage changes, release
changes, assembly pins, installed tools, or lane/session changes. P08 remains
held.
