# Design

## Context

See proposal.md. Disabled delivery from code#99 is on main, P01 is merged as
f3576b95 and real smoke43722592020 passed on an unchanged helper. The observer
and delivery verifier share immutable profile parsing, not observation output.
Existing P03/P05 profiles assume a single P01 child; P05 needs a reviewed fix.
Owners are independently landing P02/P03 with full CI. P02 changes pgcluster.py.

## Goals / Non-Goals

Goals: preserve conservative proof while staging a named manual queue and exact
linear correction history. Non-goals: enable routing, change the current binding,
admit a canary, broaden to product/foundation files or freeze owner development.
No CI job, baseline, provider/assembly pin, setting, tool or release change.

## Decisions

1. Add a fifth queue rather than modify any existing queue. Mirror the existing
   queue-only protection injection, two-member serial/no-retry configuration and
   app-qualified fast/delivery/heavy gates. Narrow metadata/path conditions are
   preliminary filtering; immutable source/review/candidate proof remains the
   authority. Queue controls do not independently enforce one exact two-member
   operator selection or queue-only use of fast sources; the runbook states both.
2. Add optional `source.commits` to the existing strict profile schema. Without
   it, normalize to `[source.head]`. With it, accept only one to three unique SHA
   strings, last equal to source.head. A shared lineage predicate verifies every
   exact parent and cumulative scoped diff; both observer and delivery call it.
   Checking only final ancestry/diff was rejected: it hides intervening off-scope
   changes. No permissive ancestor query or blanket multi-commit approval.
3. Keep profile keys, paths, P01/foundation/control freeze and actual false flag
   unchanged. Do not add the stale P05 fix SHA or a speculative replacement pair.
   Owner full-CI run1 proceeds. Future foundation changes deliberately fail full
   until a distinct reviewed qualification epoch, not an opportunistic rehash.

## Risks / Trade-offs

- Queue exists while flag false -> manual-only; disabled proof refuses and heavy
  cannot qualify. No automatic admission rules or queue comments in this stage.
- Owner PRs may land before enablement -> reconcile fresh open cuts with owners;
  no unilateral hold, body edit, retarget or borrowed consent.
- Config simulator is not live Mergify -> require genuine config validation after
  publication, then actual live canary evidence under the staged approval.
- Per-PR bindings are not a scalable final rollout -> retain explicit narrow
  initial boundary; widen through reviewed task-set profiles only after canary.

## Migration Plan

Run offline refusal/queue/source/candidate/historical regressions, strict spec
validation and exact-head expert/bot review. Normally merge paired source PRs
only with fresh full/reuse CI. Verify main. Then select owner-consented current
cuts, qualify the exact unchanged foundation, prepare reviewed enablement and
run the staged canary. Rollback is a reviewed false flag; no bypass or settings
change. Assembly adoption and shared-checkout refresh stay excluded.
