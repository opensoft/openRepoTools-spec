# Design

## Context

See proposal.md for motivation. P01 is code PR88, owned by lane1, with content
owned by lane2. Both owners confirmed that its only CI change is the required-PG
workflow environment and matching baseline amendment: all job hashes and the
13 integration-job inventory stay unchanged. New tests must tolerate both sides
of that environment transition and must not modify their files.

## Goals / Non-Goals

Goals: observable future profile boundaries; explicit P01 prerequisite drift;
full ordinary/reusable coverage qualification; source-only paired PRs.
Non-goals: live PG acceptance from metadata, test omission, candidate admission,
queue changes, fast selectors, changes to007's gate or release, merges/pins,
installed tools, or any action in active lane worktrees.

## Decisions

1. Add a separate observer, not another branch in delivery_route/verify. Reuse
   existing bounded read-only shadow Git/API primitives. Every output retains
   full CI, disabled efficiency, no skip and no queue admission. No output is
   consumed by any gating workflow. A positive cut observation is not readiness.
2. Load a main-owned disabled projection of P03/P05, keyed to specc304e387 and
   the v12f planning draft. It is implementation data, not a second task ledger
   or approval of that draft. Reconcile current owner cuts before future use.
   Allow regular added/modified named test files and exactly the cut's empty
   added task markers. Reject deletion, mode/type changes, extra/missing markers,
   marker-only changes, control files, mixed paths and dependent topology.
3. P01's confirmed context anchor is head7f1141be/tree93122d48, with the owner's
   tests.yml and baseline blobs. Observe only an exact merged same-repo PR88,
   merge ancestry and those main blobs plus the required-PG baseline. A pending,
   changed or unavailable prerequisite is explicit; an observed merged contract
   still is NOT complete PG CI, artifact parity or live batch qualification.
4. The additive workflow runs token-bearing observation only from trusted main.
   Before main carries the observer it emits safe bootstrap fallback and never
   executes PR code with that token. PR-owned contract tests get no API token.
   Add the new files to full-ci-parity.yml's existing path filter only, retaining
   its parameterless full call, every original job and ordinary tests.yml.
5. Use the canonical OpenSpec tasks.md here. SpecKit's011 metadata identifies
   the isolated paired worktrees; it does not introduce another checklist.

## Risks / Trade-offs

- P01 changes after the anchor -> report drift, require a reviewed projection
  update; do not follow a mutable PR head or infer approval.
- Tests/markers can look right while code is incomplete -> never grant skips or
  qualification, retain the full suite and007's existing task-gate semantics.
- Missing objects, APIs, malformed config or moving main -> fixed-vocabulary
  full fallback; no fetch/ref/status write to recover an observation.
- Before P01 lands, ordinary/reuse CI can qualify this additive implementation
  but cannot prove PostgreSQL-backed007 acceptance. Record that distinction.

## Migration Plan

Open paired source PRs with full CI and exact-head review. Record source/reuse
case inventories when available. Keep P01 as its owner's separate normal full-CI
landing. Later PG/live qualification, v1.3 activation, queues and assembly/session
adoption need their own reviewed work and approval. No live canary runs here.
