# Plan

Implement governance from ../../openspec/changes/repair-sonar-assurance/. The root is the elected triad; code owns analysis/coverage helpers and regression tests, spec owns requirements and evidence, and root owns the assembled workflow and pins.

## Architecture

Inventory tracked files at exact Git commits. Classify executable shebangs and language suffixes, with explicit tests and exclusions that never hide product/upstream source. Generate deterministic scanner properties and a coverage/source receipt. Run full composed tests in isolated CI processes, reusing the existing shard collection verifier where parallelism is needed. Python subprocess and shell tracing collectors produce actual coverage. Normalize temporary copies only against committed-byte hashes and validate line bounds.

The composed-tests job retains all existing composed tests. A parallel provider-tests job runs the provider complete suite, keeping both measured suites within the hosted per-job time limit. Each uploads its own measured reports and source-copy probe under an immutable artifact ID. The sonar job checks out the same github.sha, downloads both exact IDs, independently rechecks each full commit/source inventory and coverage structure, combines observed hits with a boolean union while retaining all unexecuted lines, then regenerates machine-local report paths. The existing required exact-pins context requires both complete suites and analysis to succeed. A failed scanner retry never selects a branch-latest, different-commit or partial-test artifact.

Publish complete coverage and scan artifacts; fail on incomplete collection, tests, report provenance or NONE/ERROR. Use the existing Sonar token and exact SonarSource scanner revision. The main migration preserves Sonar way and selects the earliest retained analysis, with before/after settings receipts.

Review each original security flow before deciding its disposition. Pipeline-only wheel hardening preserves the full CI job graph, event set, package checks and parity contract. Any upstream change lands in openRepoShape before a normal consumer pin advance.

## Governed invariants

- `.github/workflows/` — full checks, credential scopes and gate reporting.
- `.github/ci/full-ci-baseline.json` — complete job parity; changes require explicit before/after coverage review.
- `contracts/` and submodule gitlinks — coherent immutable provider selection.
- `openspec/` — authorized quality policy and preservation boundaries.
- `.github/ci/sonar*` — measured coverage provenance and fail-closed gate verification.

## Verification

Use focused unit tests for inventory, classification, source-copy matching, malformed/foreign/out-of-range coverage, failed test results and NONE/ERROR rejection. Verify actual collector smoke cases in an isolated unprivileged container before full CI. Preserve all existing full CI and packaging gates. Verify Sonar's exact revision, indexed command inventory, coverage metrics, original issue dispositions and evaluated main gate. Compare all captured primary checkout/pane receipts before closure.
