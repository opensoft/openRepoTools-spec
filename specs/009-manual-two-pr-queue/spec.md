# Feature 009: Manual two-PR queue configuration

Authority: Brett requested opening the config-only PR, retaining full CI and
lanes 1–5, with no canary, bypass, app access or release changes.
Governance: `openspec/changes/manual-two-pr-queue/`.

- FR-001: one manual serial queue, maximum two PRs and one active candidate.
- FR-002: pilot label, head/base restrictions, same-repository open non-draft PRs,
  and only the two pilot Markdown references; mixed/unknown changes refuse.
- FR-003: all four existing full-CI gates from GitHub Actions on original PRs
  and the combined candidate; existing workflow files remain unchanged.
- FR-004: no auto admission, account impersonation, retries, splits or bypass;
  external merges reset candidate validation, external queue edits refuse.
- FR-005: queue preparation does not authorize merge/enqueue/canary/app/release
  operations. Label/review attestation remains explicit and head-specific.

Full CI per PR plus candidate CI is intentional. CI-saving mode is deferred.
Schema and offline policy tests are not live batching acceptance.
