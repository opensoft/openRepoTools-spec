# Design

## Context

See proposal.md for motivation. Code main `804a97be` contains the accepted
P05/P06 canary. The active projection has a six-path, three-commit boundary and
accepts only its reviewed pair. P07 has archived fixtures; P09 modifies
`tests/pgcluster.py` and an existing fixture module. Neither fits that boundary.
P08 remains held because its source PR includes a release-workflow change.

## Goals / Non-Goals

**Goals:** stage exact next-cohort inventories, obtain fresh owner bindings and
review references, and make the remaining activation constraints inspectable
with local Git and regression tests. Preserve every active fallback decision.

**Non-Goals:** live omission/admission, an activation toggle, queue changes,
runtime edits, a foundation transition, source-PR rewrites, P08, releases,
assembly adoption, installed tools or running-lane changes.

## Decisions

1. Keep a separate disabled preparation manifest. Do not add next-cohort heads
   to the active projection or reinterpret its bounds. This avoids an accidental
   activation through a data-only merge while preserving historical proof.
2. Bind the preview to an explicit immutable main commit and two fixed source
   PRs. A source binding may remain null while its owner finalizes fixes; that
   state reports pending. Old observed heads are not accepted replacements.
   Fresh owner/review comment references are context, not machine-authenticated
   review receipts or human approval.
   Superseded cuts are explicitly unbound: null source head, empty history and
   null review/consent, with observed hashes retained only as context. P07's
   exact inventory includes added regular file `tests/sql_register_cleanup.py`,
   per owner handoff6940832411 and lane3 agreement6940947493. This stages the
   path only; final helper ownership records and code acceptance stay separate.
3. Inspect exact ordered linear source histories, regular-file modes, empty
   added task markers and complete path/status inventories. Enforce a finite
   history from each declared source anchor, which must contain the common
   prerequisite and already be landed in frozen main. P07 starts at P01;
   P09 starts at landed P03, not at P01. Never omit intervening source commits
   or import main's control commits into a source chain. Enforce a finite
   history bound and disjoint member paths. Compute P07-then-P09 merge previews
   only when both bindings exist. Synthetic commits, if needed, are immutable
   objects in the private/CI Git object database, never branch/index changes.
   Preparation ceilings are profile-specific: P07 16 and P09 24 commits from
   their declared anchors. These match P07's owner-confirmed four-plus-nine-plus-three
   chain (issue165 comment6935365475) and P09's prior eight-plus-sixteen ceiling,
   not an arbitrary global increase. P09's observed29-commit export still needs
   its final owner handoff before another explicit reviewed update. Every SHA and
   cumulative cut remains checked. A ceiling is not an owner head binding or
   approval; changed chains above it require another reviewed update, never
   truncation, implicit acceptance or rewriting another lane's history.
   Renewed owner consent (issue165 comments6938017311/6938099557) requires
   P07 before P09. Reject the reverse order and verify that the P07 prefix
   retains the original foundation before P09's declared helper transition.
4. Always output `required_ci: full`, `skip_ci: false` and
   `queue_admission: false`, even for a valid preview or linked reviews.
   The inspector has no network, status, dispatch, queue or merge operations.
5. Report any changed frozen foundation path separately. P09's pgcluster delta
   requires a future reviewed foundation/landing contract; this preparation
   deliberately does not broaden the live foundation verifier. Future activation
   also requires fresh current-main/source/review proof and a separately approved
   live batch. A successful preview is insufficient.

## Risks / Trade-offs

- Owner fixes are not final yet -> keep the PRs draft and preserve null bindings
  or explicitly pending review evidence; update only from actual owner posts.
- A preview resembles an admission receipt -> use a distinct preparation-only
  schema and constant full-CI/no-admission outputs; production readers never
  consume it. Test that P07/P09 still refuse live delivery.
- Main changes -> refuse the stale preview rather than silently rebase its
  foundation. A reviewed refresh is needed before activation preparation.
- Git conflicts or unsupported histories -> refuse with a fixed public reason;
  do not rewrite another lane or relax inventories to make the preview pass.

## Migration Plan

Open paired preparation PRs, request fresh Codex and owner review, and leave
them open under the original preparation request. Subsequent owner authority
permits checked merges only after spec#80, fresh product-head reviews and CI
pass. Merging these additive files still activates nothing. The separately
approved narrow live rollout needs its own reviewed live contract and canary;
it is not a preparation toggle. No assembly refresh follows. Dropping these
additive preparation files leaves all production behavior unchanged.
