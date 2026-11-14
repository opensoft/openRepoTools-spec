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

## Accepted rollout amendment — 2026-11-11

Brett now approved merging code #44 after CI, adding the general manual code
queue, and updating assembly pins and Claude instructions. The original
preparation-only limits above are historical; this amendment authorizes those
changes, not unrelated merges or live canaries. Reconciliation: spec issue #26.

- FR-006: append `openrepotools-code`: main-only, same-repository, open non-draft
  PRs; at most two members, one candidate, 60-second fill, 90-minute checks,
  zero automatic retries/splitting. Retain earlier queues and all four full gates.
- FR-007: allow known shipped commands, `repos.tsv`, `README.md` and safe paths
  under tests/src/commands/skills/features only. Refuse release-stamp edits,
  workflow/queue/pin/package-control paths and unknown/mixed changes.
- FR-008: repository writers manually request entry after checking merge
  authority, exact-head Codex review, unresolved threads and current head/base/
  paths/full CI. The command is an operator attestation, not automated Codex/SHA
  enforcement. No review label or fabricated success check substitutes for it.
- FR-009: remove only tests.yml's obsolete labeled event. Keep unfiltered PR
  opened/synchronize/reopened, draft candidates, main/release-branch pushes,
  nightly/manual runs, gate names, shard coverage and release workflows.
- FR-010: root Claude/Codex instructions point to one shared code-local workflow.
  Advance only merged leg commits, gitlinks, pin/digests and workflow SHAs in
  lockstep; validate exact assembled pins before authorized local activation.

Do not flatten dependent stacks into this main-only queue. Preserve lanes 1–5,
worktrees, bindings and running benches. No automatic enqueue, bypass, app
access, paid upgrade, release/tag or installed-session change is authorized.

## Accepted coverage-parity prerequisite — 2026-11-11

- FR-011: add only a parameterless reusable entry to the existing full workflow.
  Preserve ordinary PR/main/release-branch/nightly/manual events and all six job
  definitions: guard, five normal shards, four no-submodule shards, their two
  fail-closed aggregators and full package checks (13 expanded jobs).
- FR-012: a separate PR-only qualification caller runs the same local workflow
  at the caller commit and rejects any unsuccessful call. A reviewed inventory
  and mutation regressions prove environment/job/matrix/step parity. No selector
  or conditional skip input is exposed; discovered new tests still run.
- FR-013: this delivery opens source/governance PRs only. No merge, fast-only CI,
  authoritative classifier, queue command, live canary, ruleset/app/release change,
  assembly pin bump or lane/session modification is authorized.

## Accepted trusted shadow-classifier prerequisite — 2026-11-11

- FR-014: add only an independent trusted-main observer with read-only contents/PR
  access. Missing policy reports full fallback; no PR-owned policy execution with
  its API token. Source tests run separately and offline without that token.
- FR-015: validate live main/PR/branch identities and local immutable Git diffs for
  the fixed modified regular-file claude-current/test pair. Intent is observational,
  never a review/admission receipt. Recheck main and source identities; uncertainty
  retains full CI. Unsupported dependent topology remains full.
- FR-016: observe already-merged edited metadata only with same-head/live merge,
  parent/ancestry and landed-scope proof. Candidate and main-push observations
  remain explicitly unproved pending the later heavy/candidate identity stage.
- FR-017: all results are shadow/full/no-skip/no-admission. Preserve original pilot,
  all existing full/reusable/verify/release workflows, job baseline and queues.
  Open source/spec PRs only; no merge, canary, ruleset/app change, assembly pins,
  installed tooling or lane/session mutation.

## Full-rollout goal: fast/candidate preparation — 2026-11-11

- FR-018: add exact-checkout fast source feedback and the scoped component/guard/
  hygiene/policy tests, without replacing required full CI or authorizing admission.
- FR-019: prepare a parameterless workflow_call-only candidate verifier, no queue/
  dispatch trigger. Verify genuine Mergify context, live main/ref, one or two unique
  ordered independent main-based PRs, scoped modified regular content, exact fast
  Actions workflow, fresh clean Codex and resolved threads. Missing/ambiguous or
  stale evidence fails; all reads are bounded and metadata permissions read-only.
- FR-020: after verification reuse the same complete thirteen-job tests.yml on the
  exact candidate and require both verifier and full call success in its aggregate.
  No workflow/policy/control changes can be present in a qualifying scoped candidate.
- FR-021: existing full/reusable/verify/release/shadow workflows, coverage baseline
  and queues remain unchanged. No live canary/queue, ruleset/app/release change,
  source success-status writer, dependent-stack/wider-scope activation or lane
  modification follows from this additive preparation.

FR-019 clarification after local review: establish member independence from
Git history, not absence of dependency text. Bind a fast run to its unique source
PR and current main base with complete base-change history: its immutable creation
time must be strictly after every retarget event. Current run PR associations alone
are mutable and insufficient. Reopening after a retarget may produce fresh fast
evidence; rerunning a historical run does not change its original creation time.
Recheck successful fast run/job/check, base history, Codex and threads at the final
source consistency boundary and refuse evidence drift, not just head/ref drift.

## Candidate-shadow caller preparation — 2026-11-12

- FR-022: add only a non-gating push caller filtered to Mergify queue branches.
  Probe with read-only metadata access and main-owned policy; missing policy or
  any refusal/unknown/error schedules no additional qualification. No dispatch,
  inherited secret, source-payload shell interpolation or write grant is allowed.
- FR-023: only explicit probe success may call the existing reusable entry,
  which independently proves the candidate and runs/rechecks all full jobs.
  The fixed two-file, one/two-independent-member constraints stay unchanged.
  Scheduling output is not admission, review evidence or a completed heavy run.
- FR-024: preserve current full PR/candidate CI, its inventory, queues, required
  checks, releases and active lanes byte-for-byte where applicable. Open code/spec
  PRs only; no merge, live canary, queue command, ruleset/app change, bypass,
  release/tag or assembly/runtime adoption. A positive future observation adds
  full integration, not CI savings. A later cutover requires separate approval.

## Approved cutover preparation — 2026-11-12

Full governing change: `prepare-claude-current-ci-cutover`, capability
`ci-delivery-cutover`. This amendment supersedes earlier source-preparation-only
shape constraints only for the following new, disabled implementation:

- FR-025: prepare default-off trusted routing with effective app-bound delivery
  protection verification; legacy requirements or incomplete reads retain full.
- FR-026: scoped fast sources require both explicit markers, immutable test-merge
  identity and genuine fresh fast provenance. Reusable/release/nightly/manual
  execution stays full. No ordinary event filters or fake full-test green aliases.
- FR-027: retain all thirteen full jobs through exact reviewed needs/if projection
  and strict baseline/mutation checks; do not regenerate the separate Sonar baseline.
- FR-028: bind admission/qualified artifacts to authentic candidate run/repository/
  SHA/first attempt with bounded archive digest/schema checks and provenance rechecks.
  Candidate-PR omission requires real live admission and independent live sources.
- FR-029: add a separate manual scoped queue with fast + delivery source conditions
  and one top-level heavy result holding integration; preserve old queues, review
  trust, protection injection, zero retries/splits and serial max2 behavior.
- FR-030: use a separate historical reader for matching ordered closed/merged
  source records, tested prefix trees, complete successful jobs and unchanged
  policy. Missing/uncertain/rerun/lookalike main landings stay full.
- FR-031: prepare/open only. Keep flag false and current full requirements in force.
  Document—not apply—migration and its changed GitHub source floor/manual-queue-only
  operator policy. No merge, live canary, settings/bypass, app/paid access, release/
  tag, assembly/runtime adoption or lane mutation. Local review of preparation is
  not a forged managed receipt for later candidate members.

## Accepted local-review contract — 2026-11-12

The owner explicitly authorized `accept-exact-head-local-reviews` as the narrow
successor to the managed-only interpretation in FR-019/FR-031 and the earlier
cutover proposal. Preserve their approval history; a local review is never
relabeled as a hosted-bot review.

- FR-032: the prepared candidate verifier accepts either the existing genuine
  hosted review path or a new exact-head/base local Codex review receipt
  authenticated to a current repository writer through GitHub comment identity
  and live permission reads. Local acceptance records the review skill, findings,
  resolution, validation and zero unresolved findings.
- FR-033: local receipts are bounded, strict-schema, unedited top-level comments.
  The newest authorized local record controls acceptance; revocation, unsupported
  or stale evidence cannot fall back to old approvals. Complete resolved threads
  and final source/receipt/permission rereads remain mandatory.
- FR-034: local-review publication is operator attestation, not proof of local
  model execution, independent human approval or merge authority. Keep activation
  false, existing full CI/coverage and all live lanes/settings/releases unchanged.

## Owner-approved activation phase — 2026-11-13

`activate-claude-current-ci-cutover` supersedes only the preparation-phase
false/no-settings/no-canary boundary in FR-031/FR-034 for this explicit approval.
It does not rewrite those historical decisions or expand routing/review trust.

- FR-035: migrate only repository ruleset 25966308's four legacy required
  contexts to delivery bound to GitHub Actions integration 15368, while the live
  flag stays false. Preserve and read back every unrelated protection.
- FR-036: enable only through an exact-head reviewed, full/reuse-qualified PR;
  retain explicit disabled/no-network, unknown-proof and full-fallback coverage.
  Qualify actual main CI before queue use. No original full job or baseline edit.
- FR-037: both Claude and Codex use the named manual held-heavy queue for an
  opted-in fast source, never direct merge. The owner accepts operator-policy
  queue-only usage; no GitHub-enforced exclusivity is claimed.
- FR-038: accept the two-member canary only on exact member/order/head/base,
  fast-only source/candidate PR runs, one complete heavy call, no duplicate full
  main run and matching tested/landed prefix/final trees. Distinguish local
  refusal/recovery fixtures from live queue evidence. Stop on excluded authority.

Bypass, app/paid access, releases/tags, assembly pins, installed tools, active-plan
selection and running-lane changes remain excluded. Current scope is unchanged:
one or two independent main-based PRs modifying only unchanged-mode regular
claude-current/test files with both explicit opt-ins. Other work retains full CI.

## Approved source-description repair — 2026-11-13

The owner approved fix-source-delivery-metadata after the documented initial
source-routing refusal, including scoped fixes, checked source merges and fresh
canaries without per-PR approval stops. Earlier failed evidence stays failed.

- FR-039: pre-routing explanatory prose may differ only when event and live
  descriptions independently contain both unique supported opt-ins and neither
  declares a dependency. Actor identity grants no exemption or authority.
- FR-040: every other event/live snapshot field, existing provenance requirement
  and subsequent strict full-body live reread stays enforced; uncertainty is full.
- FR-041: require red-first regressions, reviewed/full-qualified checked source
  landings and actual main qualification before a fresh two-member canary. Do
  not reinterpret the failed initial attempt as acceptance. All prior coverage,
  queue/review trust and explicit excluded-resource boundaries remain unchanged.
- FR-042: log fixed-vocabulary verification-stage/refusal codes and snapshot
  mismatch field names only, never their values, bodies, tokens, exception text
  or raw API responses. Preserve every fallback/waiting/proof/authority decision;
  investigate the fresh R2 failure before speculatively relaxing any check.
- FR-043: the R3 generated-hint repair permits only missing/null or trusted
  GITHUB_SHA hints at initial source/candidate event comparisons. All other
  identities, actual tested-merge parent checks, source intent, fast/review and
  genuine candidate-admission proofs remain mandatory; initial candidate
  descriptions follow FR-045 and later live rereads stay strict. Unknown non-null hints retain full even when
  both snapshots agree. No new retry, schema, permission or hint-fetching path.
- FR-044: after all initial fast identity, source/base-history, job and state
  checks pass, a source's authenticated pending observation returns only the
  existing bounded wait before completed-proof rereads. It grants no proof or
  omission. A fresh all-success observation must pass the same exact rereads;
  admission stays success-only, initial failed/unknown evidence retains full,
  and no retry budget, schema, permission or queue authority changes.
  The genuine candidate duplicate's explicitly pending-enabled absent-admission
  observation follows the same no-proof boundary; caller evolution requires a
  fresh poll. Invalid/default readers refuse and artifact-present proofs remain strict.
- FR-045: at the initial genuine candidate event/live comparison only, string or
  null descriptive prose may differ. It grants no member/review/admission or
  merge authority. Keep every other identity, bounded hint, tested-parent,
  genuine queue, fixed-scope, real-artifact and independent live-source/review
  check. Preserve original current PR data and strict final body/branch/caller/
  artifact rereads. Invalid body types, uncertain proof and later races retain
  full. Candidate snapshot diagnostics use fixed field names/codes, never values.
- FR-046: after complete initial candidate authentication/proof, a final raw PR
  or caller change may yield only the existing bounded no-proof fresh retry when
  current identity still qualifies. Revalidate everything on each observation;
  accepted proof still requires exact final raw equality. Changed branch heads,
  invalid/moved identities, malformed body/hints, failed/foreign/rerun callers,
  invalid source/artifact proof and generic API failures retain hard full refusal.
  No retry-budget, schema, permission or workflow change. Candidate gate may
  re-observe within the same bound but must match original route mode/digest;
  exhaustion or changed proof refuses. Source gate semantics remain unchanged.
  Fixed final-read mismatch diagnostics never expose values. R6 stays failed;
  reviewed/full-qualified repair and actual main precede genuinely fresh R7.
