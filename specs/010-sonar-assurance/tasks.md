# Tasks

## Set S01 — Governance and preservation (spec PR)
- [ ] T001 [O] Record governance, source/service baselines and preservation receipts — files: `openspec/changes/repair-sonar-assurance/`, `specs/010-sonar-assurance/`, `README.md` — verify: `openspec validate repair-sonar-assurance --strict`
- [ ] T002 [O] Trace and document all twelve source flows and proposed dispositions — files: `specs/010-sonar-assurance/triage.md` — verify: `python3 -m json.tool specs/010-sonar-assurance/findings.json`

## Set S02 — Code and source-owner repairs (code PR; upstream PR if necessary)
- [ ] T003 [O] Implement complete analysis inventory and fail-closed configuration verification — files: `.github/ci/sonar_assurance.py`, `tests/test_sonar_assurance.py` — verify: `tests/run.sh tests/test_sonar_assurance.py`
- [ ] T004 [O] Implement provenance-checked Python/shell coverage collection and conversion — files: `.github/ci/sonar_coverage.py`, `.github/ci/sonar_coverage/`, `tests/test_sonar_coverage.py` — verify: `tests/run.sh tests/test_sonar_coverage.py`
- [ ] T005 [O] Repair substantiated findings without reducing any existing CI jobs, tests or package checks — files: `.github/workflows/tests.yml`, `.github/ci/full-ci-baseline.json`, `tests/` — verify: `tests/run.sh -k 'full_ci or workflow or sonar'`
- [ ] T006 [O] Publish reviewed code and any needed upstream fixes; reconcile exact dispositions and coverage evidence — files: `specs/010-sonar-assurance/triage.md`, `specs/010-sonar-assurance/evidence.md` — verify: `git diff --check`

## Set S03 — Assembly acceptance (root PR after S01/S02)
- [ ] T007 [O] Add the complete composed analysis workflow/configuration and bump published leg pins with the standard tooling — files: `.github/workflows/composed.yml`, `sonar-project.properties`, `code`, `spec`, `contracts/code-pin.yaml`, `contracts/spec-pin.yaml` — verify: `make validate`
- [ ] T008 [O] Switch Sonar analysis wiring and baseline only after the replacement is reviewable; read back unchanged gate thresholds — files: `specs/010-sonar-assurance/evidence.md` — verify: `python3 code/.github/ci/sonar_assurance.py verify-gate`
- [ ] T009 [O] Complete full CI, measured coverage, exact-head/main gate verification, preservation and normal project landing — files: `specs/010-sonar-assurance/evidence.md`, `specs/010-sonar-assurance/tasks.md` — verify: `make validate`

Each set has one reviewer decision and retains current full-CI acceptance. No queue is invoked. S02 depends on S01's recorded authority; S03 consumes the independently published code/spec commits. The single executable checklist is this file.
