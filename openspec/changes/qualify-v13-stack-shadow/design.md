# Design

## Context

See proposal.md. Code main3e98d7b and spec main0e74258 contain the generic
disabled observer. PR92/P05 is head3b71c7c532f0da0b6ebbe5aee1c7cc0213a0cd56;
PR93/P03 is head81483ab50d1c75b7be3c8932e00efea3d284d102. Both have the single
parent7f1141be15ac9c0d20c25c802ba9533c2fec811c (P01/PR88), use its head branch
as base, and declare Depends-On:#88. Owners plan main retargets without head
rewrites. Their path/task inventories match the disabled projection.

## Goals / Non-Goals

Goals: preserve real dependency topology; prove a narrow read-only observation
after the exact prerequisite contract lands; exercise normal merges with main
advancing independently; preserve every existing no-authority invariant.
Non-goals: general stack traversal, synthetic candidate refs, CI selection,
queue admission, successful PG qualification from metadata, source-branch edits,
settings, canaries, assembly adoption, tools or session changes.

## Decisions

1. Add reviewed source PR/head/branch bindings to each existing profile rather
   than trusting a title, label, actor or arbitrary dependency number. Read only
   main policy, reject malformed/duplicate bindings, and leave the flag false.
2. Keep the independent-current-main path unchanged. Add one exceptional
   observation path only for a pinned single-child source of the anchored P01.
   P01 must already satisfy the existing exact merged-contract check. A base
   must be its exact head branch/SHA or current main/SHA. The dependency must
   be exactly one resolved #88; a main-retargeted PR may omit the resolved line.
   Unknown, duplicated or still-unmerged dependencies remain full fallback.
3. Compare the cut against the anchored parent, not current main: current main
   legitimately contains unrelated observer changes absent from the source.
   Require main's named cut files/markers to remain identical to that parent,
   then reuse exact regular-file/empty-marker diff validation. This is a
   conservative no-overwrite observation, not a merge or tested-tree receipt.
   Do not use unrestricted merge-base diffs or flatten/rebase another lane.
4. For an exact bound source push, bind the event/ref/head to the live source
   PR and its explicit shadow intent, then use the same proof and reread that
   PR and ref. Generic pushes keep their existing current-main/commit-intent
   contract. All API and Git operations remain bounded read-only operations.
5. Exercise authentic local Git histories: old main, P01 branch, source child,
   independent main observer commit, normal P01 merge, later unrelated main
   commit and owner-retargeted source. Also merge the source only inside the
   temporary fixture to verify preservation of unrelated files and source head.
   These fixtures prove logic, not real PG execution or live acceptance.

## Risks / Trade-offs

- Source iterations invalidate the exact binding -> reviewed metadata update;
  no automatic following of mutable heads.
- Main touches a cut path after P01 -> conservative fallback even if Git might
  merge it; never infer compatibility or overwrite authority.
- Review or intent changes while reading -> reread snapshots and clear the cut.
- P01 remains open -> tests can qualify source logic, but positive real-world
  observations and PG integration evidence remain unavailable.

## Migration Plan

Open the paired source PRs with full ordinary and reusable CI and exact-head
reviews. Keep scope disabled and all active owner PRs unchanged. After P01's
separate checked landing, reconcile owners' current cuts and collect exact
PG-backed ordinary/reuse case identities/outcomes and approved skips. Later
efficiency cutover, live qualification and adoption require separate authority.
Rollback is removal of this observer-only exception; no runtime settings or
delivery service state is changed by this preparation.
