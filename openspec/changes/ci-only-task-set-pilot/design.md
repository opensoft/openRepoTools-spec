## Context

See proposal.md. The code leg has standalone sharded CI; the assembly has a
separate exact-pin composed gate. Code PR #34 owns targeted branch verification.

## Goals / Non-Goals

Qualify classification and shared agent guidance without changing any existing
gate or enabling a queue. Do not duplicate #34's selector/engine workflow,
change claims, bootstrap a running lane, or advance assembly pins.

## Decisions

- A separate read-only `ci-pilot.yml` reports shadow results and runs its own
  offline classifier tests. Leave `tests.yml` and `release.yml` byte-identical.
  A new job inside tests.yml was rejected because that file's job set is a
  release contract. No result from this workflow can satisfy those gate names.
- Evaluate the current PR's immediate base and head, not a hard-coded main diff.
  Use merge-base diff with rename detection disabled; anything except regular
  added/modified files in the two-file allowlist falls back to full scope.
- Every result is `required_ci=full`, `queue_admission=false`. A shadow candidate
  is not an eligibility receipt. Head-owned classifier code is acceptable ONLY
  because it has no skip/admission authority. A future authoritative classifier
  must be loaded from trusted default-branch policy and separately qualified.
- Metadata changes to an open, readable same-repository PR may be observed;
  closed/dequeued/draft/unknown events and pushes remain full-scope. No PR input
  is interpolated into shell source. JSON is data, never a command or selector.
- Put code-local CI instructions in one `.github/ci/task-set-delivery.md` and
  link it from the leg's existing AGENTS.md. CLAUDE.md already reads AGENTS.md.
  Do not copy global skills into a leg or replace the assembly's instructions.

## Risks / Trade-offs

- No immediate CI saving -> intentional shadow stage; ordinary checks remain.
- Cross-leg compatibility -> retain exact-pin composed acceptance and ordinary
  pin tooling; no cross-repository atomic-stack claim.
- False confidence in classification -> report mode and full-CI requirement in
  every result; test real git histories, renames, unknown paths and metadata.

## Migration Plan

Review and land the spec/code PRs normally when separately authorized. Observe
ordinary CI only; no live canary. Assembly adoption needs a later authorized pin
update. Future queue/app/ruleset/fast-only activation requires its own decision.
Rollback is reverting the additive pilot and its instruction pointer; no existing
workflow, branch protection or release gate needs restoration.
