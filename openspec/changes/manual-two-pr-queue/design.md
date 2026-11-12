## Context

Feature 008 supplies advisory classification only. Existing tests.yml runs four
required full-CI gates on all PR bases, including draft queue candidates. Code
PR #34 supplies targeted feedback, not integration acceptance. See proposal.md.

## Goals / Non-Goals

Prepare one manual, documentation-only queue for a separately authorized live
test. Do not implement CI efficiency, an authoritative classifier or new status
receipts, or touch the existing workflows, lane records or assembly pins.

## Decisions

- Follow the existing OpenBenches manual queue pattern: serial mode, one active
  candidate, maximum batch size two, 60-second fill window, no automatic retry
  or failure splitting. Use a 90-minute check budget for the existing full suite.
- Admit only same-repository, open, non-draft pilot branches with a manual
  `batch-pilot-reviewed` label and one or both code-local pilot reference files.
  Bases are main, or the pilot prefix with an explicit Depends-On body marker;
  Mergify must still recognize the actual chain. This supports ordinary PRs,
  not GitHub registered stack objects or a cross-repository atomic batch.
- No auto-merge setting, automation rule, or queue checkbox. Queueing requires
  an explicit queue command from a repository writer. The label is a maintainer
  attestation, not machine-verifiable Codex evidence; the operator must verify
  current heads/bases/reviews and authorization before labeling or queueing.
- Require guard-launch-mode, tests, tests-no-submodule and package from GitHub
  Actions before admission and on the combined candidate. The existing PR
  workflow provides candidate validation; no workflow edit or CI skip is needed.
- Reset on external merges and refuse externally edited queue branches. Do not
  impersonate an account or use a direct-ref/fast-forward merge method.

## Risks / Trade-offs

- Batch size is a maximum, not a promise of exactly two -> future operator
  checks actual membership and candidate identity; singleton/retry is no clean
  two-PR canary. Full per-PR CI remains in addition to batch CI.
- A label can survive pushes -> remove it on a changed head/base, verify again,
  then explicitly requeue. This stage does not claim an automated review gate.
- Service access/plan/rules may block -> stop for separate approval; a schema
  validation or green config test proves neither installation nor live queue use.

## Migration Plan

Open the code configuration PR with a spec companion; do not merge or enqueue.
If later authorized, land by ordinary checks/review, verify existing app access,
then obtain separate canary approval. No labels/branches/queue commands for a
canary are created now. Reverting the config removes the queue; no release or
ruleset configuration requires restoration. Archive only after assembly landing.

## Accepted general-code rollout — 2026-11-11

Append `openrepotools-code`, retaining earlier queues and the same two-member,
serial, full-CI settings. Only independent same-repository open non-draft PRs
targeting main and known normal code paths qualify. Control-plane, release-stamp
and unknown/mixed paths refuse. No branch rename, review label, custom success
status, autoqueue or installed agent is needed. The writer checks actual
head/base/review/CI and merge authority before the named command. That is manual
attestation, not service-enforced Codex proof; a head push requires fresh evidence.

#44 removes only label-triggered duplicate tests.yml runs, never code-update or
candidate checks. Ordinary post-merge main CI stays. The assembly points into
one shared code-local reference and advances merged gitlinks/pin digests/workflow
SHAs in lockstep through bump-leg.py. Qualify composed exact pins before local
activation. Existing lanes and installed sessions remain unchanged. Reconcile
the dated as-built evidence under issue #26; preserve original approval history.

## Coverage-parity prerequisite — 2026-11-11

Expose tests.yml through an empty workflow_call event, with no inputs, secrets,
outputs or conditions that could select reduced coverage. Keep its six job
definitions (13 expanded jobs), environment and existing events unchanged.
A local PR-only qualification workflow calls that same file at the caller's
commit, after parity tests, and fails its aggregate unless the entire call
succeeds. No queue-branch trigger, dispatch, secret inheritance or status writer
is introduced. Ordinary full CI remains independent and mandatory.

Freeze the parsed environment/job definitions from merged code baseline
d3c55e55aa3516cbaa1d68a77061ddd50191d49a in a reviewed JSON inventory. Baseline
changes require explicit review; future additions to tests are still discovered
by the existing shard planner, not an enumerated allowlist. Mutation tests reject
missing/conditional jobs, altered steps, matrices, checkout modes and aggregators.
Keep the current missing-report, failed/cancelled-shard and collection-parity
regressions. This is source/hosted reuse qualification, not a live queue test.

## Trusted shadow observer — 2026-11-11

Add a separate read-only ci-shadow workflow. Its observer checks out main with
complete Git history and credentials not persisted, then executes only the
main-owned shadow_scope.py. If that policy is absent during bootstrap, report
policy-unavailable/full CI; never execute the PR's classifier as a fallback.
An independent head-owned offline contract job has no API token in its test
environment. Existing ci-pilot, tests.yml, reusable qualification, verify,
Mergify configuration, full-job baseline and release workflows remain unchanged.

Port OpenBenches' conservative proof patterns, not its skip outputs or component
allowlists: fixed claude-current/test pair, modified regular files with unchanged
modes, live API head/base/ref identity, main checked before and after observation,
and immutable local Git evidence. Explicit CI-Shadow: claude-current declares
intent only. Initial open-PR scope is independent main-based work; dependencies
stay full until separately qualified. Read API errors, missing objects, stale
policy/main/branch/PR snapshots, mixed/empty/unsafe diffs and unsupported events
fall back to full. Requests and Git reads are bounded; no writes or retries.

Recognize edited already-merged metadata only from same source head, a live merged
PR, an exact two-parent merge on current main ancestry, and the allowlisted landed
diff. Ignore stale event base/test-merge metadata only in that proved observation.
Other editors gain no authority. Candidate-shaped PR/push events and main pushes
report candidate-proof-required / landing-proof-required with full scope: heavy
receipts and candidate/landing qualification belong to the next stage. Do not
invent tested-candidate evidence or broad bot/prefix exemptions in shadow mode.

All results keep mode=shadow, required_ci=full, skip_ci=false and
queue_admission=false. No GITHUB_OUTPUT skip flag, custom status, queue command or
consumer is added. First-PR bootstrap fallback and offline source tests are not
live positive-scope or candidate acceptance. PR/push metadata and error regressions
include third-party edits, closed/dequeued events and changing main identities.

## Additive fast and candidate proof — 2026-11-11

Add a read-only source PR fast workflow on opened/synchronize/reopened only.
Validate the actual test-merge checkout and both exact parents, whitespace/syntax,
the full scoped component regression plus guard/hygiene and delivery-policy tests.
Run without the candidate API token; this check is feedback/preparation, not
admission by itself, and does not replace any current full check.

Add a workflow_call-only candidate entry with no input, secret, dispatch or queue
trigger. Its verifier executes from trusted main with read-only GitHub metadata
permissions. Only a genuine same-repository Mergify push to the exact queue branch
and candidate SHA can qualify; main and branch identities must match/recheck.
Derive one or two ordered merge prefixes rooted exactly at live main. Resolve
unique open non-draft same-repository main-based source PRs from exact heads;
reject duplicates, dependent topology, stale/moved identities and mixed paths.
Each member declares the same observational scope marker but authority comes from
live evidence: exact-head GitHub Actions fast run at the expected workflow,
completed clean Codex receipt and no unresolved review threads. Bound all reads
and reject truncated/ambiguous evidence; do not post statuses or review receipts.

Reuse trusted shadow regular-file/mode/intent primitives; do not change its
shadow/full/no-skip/no-admission contract. Both member and combined diffs must be
nonempty and confined to the two approved files. This also forbids candidate
changes to CI/policy/pins/package/release control. Emit immutable base/head/member/
prefix/tree proof only after final live rechecks. Candidate/ref/main drift fails.
Each merge prefix must equal a clean deterministic merge of its previous prefix
and approved source head. Git merge-tree computes only private immutable objects
in the disposable CI checkout, never refs/index/worktree/source files. This
CI-only helper requires the Git merge-tree --write-tree support supplied by the
Ubuntu24.04 runner; it changes no installed CLI/package runtime requirement.
The verifier is not a successful heavy-run receipt until integration completes.

After verifier success the candidate entry calls the SAME tests.yml at the
candidate commit, preserving all thirteen expanded jobs. An always-running named
aggregate requires verifier and the complete call to succeed, then repeats the
trusted live verifier so source/ref/main changes during integration cannot qualify;
failure, cancellation or skip cannot qualify. No existing queue/ruleset consumes the new check. Offline
fault-injection/Git fixtures qualify preparation; no live candidate is created.

Local-review repair keeps this scope: independent heads have no shared commits
beyond current main, established from Git rather than optional dependency text.
Fast evidence has one current source PR/main association and a complete bounded
base-ref-change history. Immutable run creation must be strictly later than every
retarget event; current associations alone are mutable and cannot prove the past
base. A fresh reopened run may qualify after retargeting; a rerun retains its old
creation identity. Read and compare mutable successful fast/check/run, base-history,
Codex and thread evidence at the final source boundary. Any drift fails closed.
