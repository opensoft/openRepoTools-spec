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
3. Inspect exact ordered linear source histories, regular-file modes, empty
   added task markers and complete path/status inventories. Enforce a finite
   history from each declared source anchor, which must contain the common
   prerequisite and already be landed in frozen main. P07 starts at P01;
   P09 starts at landed P03, not at P01. Never omit intervening source commits
   or import main's control commits into a source chain. Enforce a finite
   history bound and disjoint member paths. Compute P09-then-P07 merge previews
   only when both bindings exist. Synthetic commits, if needed, are immutable
   objects in the private/CI Git object database, never branch/index changes.
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
them open. No activation, merge, live batch or assembly refresh follows from
this request. Dropping these additive preparation files leaves all production
behavior unchanged.
