# Tasks

## 1. Isolation and owner coordination

- [x] 1.1 Revalidate main CI, P01 and actual P03/P05 heads/parents/paths; verify live GitHub readbacks and coordination comment6889436296 without changing owner PRs.
- [x] 1.2 Create paired012 worktrees through the official helper and canonical artifacts; verify bases3e98d7b/0e74258, unchanged provider4bf963f and OpenSpec apply readiness.

## 2. Known-stack observation

- [x] 2.1 Add strict source bindings and bounded prerequisite-child proof for PR and source-push observations; verify disabled/full/no-authority invariants and positive known-stack fixtures.
- [x] 2.2 Add drift, pending, unknown dependency, stale snapshot and main cut-path collision regressions plus realistic normal-merge preservation checks; verify focused canonical tests with and without the dependency.
- [x] 2.3 Update the runbook's exact supported topology and remaining PG/activation boundaries; verify workflow/parity contracts and untouched live delivery/queue/P01 control paths.

## 3. Validation and source publication

- [x] 3.1 Run strict spec validation, focused source regressions and exact-head expert review; verify results and resolve actionable findings without weakening tests.
- [x] 3.2 Publish paired code/spec PRs, request exact-head Codex review and inspect ordinary/reusable full CI; record source results separately from P01/PG or live rollout acceptance and leave scope disabled.

This is one source-preparation step toward the full rollout, not completion of
the overarching deployment goal. P01's owner-controlled landing, exact real-PG
case parity and later separately authorized activation/live qualification and
assembly/session adoption remain outside this source change.

## Local source evidence

- Code head50e6df154483f15c48d85c532afd9abf37711782, base3e98d7b03129799dbb413c34fa9b527310d98939.
- Canonical focused v13_shadow run:150 passed. Expanded v13_shadow,
  full_ci_coverage_parity, ci_shadow and ci_cutover run:632 passed,
  5054 deselected. Separate standalone no-submodule clone:147 passed,
  3 dependency-bound YAML/workflow contract skips,5536 deselected.
- Strict OpenSpec validation:valid. Coordinator plus nine pre-PR specialist
  roles: no actionable findings. This is local review, not a hosted receipt.
- Four code paths only; tests.yml/baseline/P01 controls, delivery policy/router/
  verifier/evidence, Mergify and provider pin unchanged. No live grant/queue,
  settings, tools, release, assembly or shared-checkout/session changes.

## Publication and prerequisite-source evidence

- Code PR96: https://github.com/opensoft/openRepoTools-code/pull/96;
  spec PR61: https://github.com/opensoft/openRepoTools-spec/pull/61.
- Ordinary full CI43680300360 and reusable qualification43680300452 started
  on code50e6df1; final results were still pending at publication. Both PRs
  received explicit `@codex review` requests; hosted results are independent
  of the local panel and must be rechecked at the latest published heads.
- P01 remains open, independently owned, at7f1141be. Its existing source
  ordinary/reuse artifacts were audited separately: nine shard reports per
  run, all83 committed Python test modules in both variants,5564 case IDs per
  variant, no missing/duplicate module coverage, and exact matching outcomes
  and skip types/reasons between the calls. See evidence/p01-source-parity.json.
- That P01 source audit is not post-merge/current-main integration qualification,
  approval of every skip, assembly coverage or permission to activate. The
 10 fixture cases passed without skips in each variant; standalone wheel,
  upstream, zsh, root/spec and composed skips still have their separate gates.
