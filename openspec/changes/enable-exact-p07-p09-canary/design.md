# Design

## Context

Code preparation #111 and spec preparation #78 are merged. The bound
preparation registry is not consumed by live routing. P07 and P09 retain
their original PR bodies; each has fresh preparation evidence on the old
base, which cannot qualify an event created after enablement.

## Goals / Non-Goals

**Goals:** one reviewed, finite canary, with independent proof of source
identity, ordered candidate execution and each landed prefix.

**Non-goals:** generic SQLite/PostgreSQL profile widening, policy inference
from preparation output, source rewrites, and administrative changes.

## Decisions

1. Use a separate trusted-main exact-pair contract. Pin both body digests,
   both heads and both exact ordered source-chain digests. Copy the reviewed
   path/marker inventory into this reviewed contract; production does not
   consume the disabled preparation file or its output. Continue to check
   actual open PR, current base, ref, lineage, inventory and fresh fast/review
   evidence. A new opt-in header would alter the frozen bodies; the separate
   narrowly pinned contract avoids that and leaves existing scopes alone.
2. Give the pair its own manual queue with exact PR number, branch and
   path inventories. Queue filtering is not a SHA or cardinality proof.
   The authenticated candidate verifier must reject a singleton, reverse
   order and mismatched trees before it grants access to the heavy workflow.
   Do not reuse the ordinary code queue, run a singleton or hand-merge.
3. Use the current required PostgreSQL test contract as the before-foundation.
   Require the first prefix to preserve it and the second to have exactly
   the reviewed before/after blobs for `tests/conftest.py` and
   `tests/pgcluster.py`. Recompute each merge tree; never repin an observed
   unexpected blob or weaken existing regression jobs.
4. Authenticate admission and qualification with the existing workflow and
   digest-bound artifact readers. Verify the exact pair's historical landing
   against the old base and both prefix trees, for atomic or separate events.
   Verify fresh run inventory, heavy cardinality and source fast-only run
   shapes separately; none can stand in for another.

## Risks / Trade-offs

- Changed main or an event carrying stale base → refuse; wait for lane 1's
  checked fresh event on the final enablement base. Do not borrow old green
  checks or authorize a repeat preparation fallback on a new base by default.
- Manual queue may form a singleton → the pre-heavy admission must refuse
  before the expensive suite; stop rather than count it or enable retries.
- Unknown evidence, unexpected workflow or unrelated landing → require full
  CI; preserve required checks even if that makes the canary inconclusive.

## Migration Plan

Verify post-merge CI for preparation. Review and merge the scoped code/spec
change normally with full CI and reusable qualification on its exact head.
After enablement, obtain fresh current-base owner, review, metadata and
fast-only evidence through the established owners. Request the manual pair
only from that unchanged base and audit heavy and landed trees. On failure
stop and dequeue as applicable; any disablement or fix uses a checked PR,
not a ruleset or application change.
