## Context

See proposal.md. The root owns the assembled gate, the code leg owns reusable analysis helpers, and spec owns policy/evidence. Main currently reports NONE and automatic scanning excludes coverage. The existing full tests and packaging checks remain authoritative.

## Goals / Non-Goals

Goals: complete source and coverage inventories, evaluated gates, and evidenced triage of twelve findings.
Non-goals: queue activation, CI skip optimization, required-check migration, releases, deployments, installed-command changes or session refreshes.

## Decisions

1. Use CI-based SonarScanner with explicit source/test sets and language patterns. Keep the real command names; renaming products to satisfy suffix detection would break existing interfaces.
2. Collect Python coverage across subprocesses and shell coverage using executed tests. Normalize reports against exact-source hashes and line bounds; never infer hits from test names or fabricate missing coverage.
3. Preserve full existing CI and retain the required exact-pins context. Add measured collectors around its existing canonical test entry point and submit the scanner only after successful tests and report validation. Instrument the existing complete composed run; if measurement requires parallel collection, retain its exact collection/count checks. No selected subset substitutes for current full gates. The root workflow runs the unchanged complete composed and provider suites in separate parallel jobs before scanner submission; an immutable artifact ID carries the reports between jobs. The scanner job checks all repository commits, source hashes, report paths and line bounds against its fresh checkout of the identical github.sha. The retained exact-pins context requires both complete suites and analysis to succeed; skipped/cancelled jobs fail acceptance. A retry reuses evidence only within that exact workflow run and commit.
4. Keep Sonar way's existing conditions. Set the oldest retained main analysis as baseline, retaining its analysis ID and rollback values. The repair's own merge is not the baseline.
5. Use the existing organization SONAR_TOKEN secret; no additional app grants or required-check changes are needed. Project automatic analysis is disabled only when the replacement CI configuration and credentials are verified.
6. Classify each security flow using its actual source, guard, caller authority and meaningful tests. Fix wheel-only installation controls at their owner. Never edit pinned standard copies or accept unverified findings.
7. Keep all work in feature 010's isolated worktrees and a separate assembly verification checkout; preserve all active sessions and shared checkouts.

## Risks / Trade-offs

- Coverage instrumentation may affect child environments: prove smoke and integration behavior in isolated, unprivileged processes before full CI.
- Tests copy commands into fixtures: map hits only when source bytes match a committed file exactly.
- New analysis scope can expose real pre-existing problems: retain failing results and repair or explicitly triage them; keep thresholds unchanged.
- Baseline/method changes are live Sonar settings: capture before state and read back every change, after the replacement is reviewable.

## Migration Plan

Capture preservation/settings baselines; implement and verify inventories, coverage and triage; publish reviewed code/spec changes; advance coherent assembly pins and root workflow; switch the project analysis method and baseline; verify exact-head and published-main analysis, computed conditions, coverage metrics and all existing checks. Reverting analysis method/configuration restores service wiring without deleting analysis history or changing running benches.
