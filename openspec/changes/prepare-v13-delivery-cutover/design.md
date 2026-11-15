# Design

## Context

See proposal.md for motivation. Existing delivery has a trusted-main router, exact-head fast/review verification, bounded two-member candidate reconstruction, authenticated admission/qualified artifacts and a separate historical landing reader. It accepts only claude-current modifications. The v1.3 observer knows immutable P03/P05 P01 children, but intentionally grants nothing.

## Goals / Non-Goals

**Goals:** reuse existing provenance and full-suite accounting; add a tightly bounded v1.3 policy domain; keep disabled operation full-only and legacy receipts compatible.

**Non-Goals:** dependent-stack flattening, arbitrary v1.3 task admission, changing P01 or its owners' PRs, enabling a queue, changing any workflow/baseline or adopting assembly pins.

## Decisions

1. Add a small v1.3 delivery-specific validator and explicit false policy rather than promote observer outputs or widen the global claude-current allowlist. Read immutable policy projection from trusted Git and independently validate P01, foundation and source facts. The observer's disabled flag stays false permanently at this stage.
2. Reuse the existing exact-head fast/review and candidate provenance readers. Only v1.3 proofs add an explicit scope discriminator; absent scope remains the exact legacy schema. Reject unknown or mixed domains. No configurable module loading or ambient policy mutation.
3. Permit the two reviewed P01 children only after their owners independently retarget them to current main and remove Depends-On. Their immutable source commits need not contain later main policy commits; reconstruct a clean merge tree instead. Compare each original cut against P01, and each candidate prefix against its actual previous tree. Require unchanged foundation and cut paths on the initial main base.
4. Historical qualification uses the receipt's original base and explicit domain, never treats closed sources as open admission. Preserve complete first-attempt heavy-job inventory, provenance, per-prefix merge records and policy-stability checks. All API observations remain bounded and read-only.
5. Leave Mergify and integration workflow definitions untouched. Extend only the qualification caller's path filter for the new control files. Future activation needs a reviewed enablement/queue change and separately approved live validation; source tests alone cannot prove service routing or one-heavy-run acceptance.

## Risks / Trade-offs

- Exact-head bindings may go stale during owner review -> fail full and require a separately reviewed projection update, never rewrite owner history.
- P01 or a later foundation change may invalidate the frozen coverage contract -> fail full rather than rehash a changed baseline.
- Extending shared verification could regress legacy behavior -> retain all existing tests and proof schemas, add mixed-domain and disabled fixtures.
- Local simulated success is not live acceptance -> record local, hosted source CI, post-main and future activation evidence separately.

## Migration Plan

Publish checked code/spec source PRs with false policy, no queue/workflow changes. Merge normally only after full/reuse CI and exact-head review. Read back main with false policy. Later obtain activation/queue/canary approval, reconcile owner cuts and P01, qualify actual required-PostgreSQL execution, then validate fast-only members, one heavy caller and matching landed prefixes. Rollback is a reviewed false policy; settings and assembly adoption remain separate.
