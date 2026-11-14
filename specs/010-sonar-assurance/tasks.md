# Tasks

## Set S01 — Governance and preservation (spec PR)
- [x] T001 [O] Record governance, source/service baselines and preservation receipts — files: `openspec/changes/repair-sonar-assurance/`, `specs/010-sonar-assurance/`, `README.md` — verify: `openspec validate repair-sonar-assurance --strict`
- [x] T002 [O] Trace and document all twelve source flows and proposed dispositions — files: `specs/010-sonar-assurance/triage.md` — verify: `python3 -m json.tool specs/010-sonar-assurance/findings.json`

## Set S02 — Code and source-owner repairs (code PR; upstream PR if necessary)
- [x] T003 [O] Implement complete analysis inventory and fail-closed configuration verification — files: `.github/ci/sonar_assurance.py`, `tests/test_sonar_assurance.py` — verify: `tests/run.sh -k sonar_assurance`
- [x] T004 [O] Implement provenance-checked Python/shell coverage collection and conversion — files: `.github/ci/sonar_coverage.py`, `.github/ci/sonar_coverage/`, `tests/test_sonar_coverage.py` — verify: `tests/run.sh -k sonar_coverage`
- [x] T005 [O] Repair substantiated findings without reducing any existing CI jobs, tests or package checks — files: `.github/workflows/tests.yml`, `.github/ci/full-ci-baseline.json`, `tests/` — verify: `tests/run.sh -k 'full_ci or workflow or sonar'`
- [x] T006 [O] Publish reviewed code and any needed upstream fixes; reconcile exact dispositions and coverage evidence — files: `specs/010-sonar-assurance/triage.md`, `specs/010-sonar-assurance/evidence.md` — verify: `git diff --check`

## Set S03 — Assembly acceptance (root PR after S01/S02)
- [x] T007 [O] Add the complete composed analysis workflow/configuration and bump published leg pins with the standard tooling — files: `.github/workflows/composed.yml`, `code`, `spec`, `contracts/code-pin.yaml`, `contracts/spec-pin.yaml` — verify: `make validate`
- [x] T008 [O] Switch Sonar analysis wiring and baseline only after the replacement is reviewable; read back unchanged gate thresholds — files: `specs/010-sonar-assurance/evidence.md` — verify: `python3 code/.github/ci/sonar_assurance.py verify-analysis`
- [x] T009 [O] Complete full CI, measured coverage, exact-head/main gate verification, preservation and normal project landing — files: `specs/010-sonar-assurance/evidence.md`, `specs/010-sonar-assurance/tasks.md` — verify: `make validate`

Each set has one reviewer decision and retains current full-CI acceptance. No queue is invoked. S02 depends on S01's recorded authority; S03 consumes the independently published code/spec commits. The single executable checklist is this file.


## Set S04 — Findings exposed by complete main analysis
- [x] T010 [O] Verify and record the 20 additional findings against main analysis d61100b9, preserving the original twelve dispositions — files: `specs/010-sonar-assurance/triage.md`, `specs/010-sonar-assurance/main-findings.json`, `specs/010-sonar-assurance/evidence.md` — verify: source-flow regression receipts and service readback
- [x] T011 [O] Enforce HTTPS on initial requests and redirects in the five implementation and three root-launcher curl calls, with real loopback transport regression tests — files: code `openRepoTools`, `tests/test_download_transport.py`; assembly `openRepoTools` — verify: `python3 -m pytest tests/test_download_transport.py -q`
- [x] T012 [O] Land the source-owner repair normally, consume its merged pin and run fresh exact-commit full composed/provider tests and actual main analysis — files: assembly `code`, `contracts/code-pin.yaml`, `openRepoTools`; this feature's evidence — verify: current-main full CI and six evaluated Sonar way conditions

S04 depends on S03's main diagnostic result. It is one bounded transport repair plus evidence-backed triage, with code-owner and root-owner PRs. Intermediate state: the gate remains red while genuine HTTPS download defects are repaired; no period, source exclusion, rule or required check changes. T009 was completed after S04 and final main/API/preservation verification; see final-acceptance.json. The preceding red intermediate state is retained as history. The current code owner's unrelated delivery activation (#56/#59) is retained; this mixed transport/CI-test work retains full CI and does not enter a manual queue.

Completion evidence: [accepted main receipt](final-acceptance.json) and the final-main section of [evidence](evidence.md). This post-acceptance Spec record does not advance the already verified assembly pins.

## Set S05 — Pin-only PR coverage eligibility compatibility

Brett approved this narrow follow-up on 2026-11-14 after assembly PR363's full
consumer/provider suites passed but independent analysis verification failed.
Sonar returned zero changed components and omitted new-code metrics, rather
than reporting explicit zero eligible lines. No prior failed result is erased.

- [x] T013 [O] Record the real absent-metric/empty-component response and define a positive exact-merge pin-only proof, retaining fail-closed real/uncertain source handling — files: this checklist, OpenSpec design and delta spec — verify: failed run43594656117 and PR363 API readbacks
- [x] T014 [O] Reproduce the compatibility failure, implement only the proven pin-only exception and add positive/negative regression tests — files: code `.github/ci/sonar_assurance.py`, `tests/test_sonar_assurance.py` — verify: canonical `tests/run.sh -k sonar` and exact-head local expert review
- [ ] T015 [O] Normally land fully qualified source/governance PRs, update PR363 using official coherent pin tooling, require fresh composed/provider/Sonar qualification and actual-main verification, then obtain fresh exact-target SAFE from every affected lane owner before one guarded shared-checkout refresh — files: source PRs, assembly pins, this feature's evidence — verify: actual checks/trees, computed main gate, owner receipts and preservation readback

S05 is one bounded verifier repair with its regressions and governance. It does
not change thresholds, Sonar settings, workflow scope, app access, paid plans,
releases/tags, installed tools or sessions. Existing running work, drafts and
worktrees are preserved. A pin-only PR exception never establishes complete
main coverage; main's complete-index, measured-coverage and exact-revision
requirements remain mandatory. The refresh is newly and separately approved,
only after all affected owners give fresh SAFE confirmation.

T014 local acceptance: code6e324e27af450de89def44ccd4f7226024af364c passes
89 Sonar-focused cases (40 new,49 retained), no skips/failures; all ten local
specialists plus the coordinator reviewed the exact head without actionable
findings. Local replay against PR363's actual base/head/test-merge proves the
intended four paths. Hosted source Sonar passes after using matched local Git
IDs, rather than event/report-file values, in actual command arguments. No issue
was dismissed. These local/source results do not complete T015's full source,
assembly/main, computed coverage or SAFE-gated operating-checkout acceptance.
