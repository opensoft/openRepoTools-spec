# Design

## Context

See proposal.md. Code59c26e8 publishes non-gating candidate observation. The
current four source/candidate full contexts remain enforced. Spec8813fa9 also
contains the separate Sonar-assurance contract; code PR51 owns its two wheel
installation edits and reviewed coverage baseline. This feature must preserve
that independent work rather than substitute or regenerate its baseline.

## Goals / Non-Goals

Implement the real selectable routing path, holding queue and historical proof,
but ship with selection disabled. Do not add another permanently advisory-only
engine or claim source publication is efficiency acceptance. Initial scope stays
the existing two regular modified files and one/two independent main-based PRs.
Do not widen to arbitrary lane files, dependent stacks, release eligibility,
installed tools, new review trust or service permissions in this PR.

## Decisions

### Disabled flag and effective gate verification

Use a strict versioned JSON configuration containing only enabled=false.
Code constants define the scope, provider and check names. Before any omission,
read all bounded effective-rule pages for main. Require an actual required
openrepotools-delivery context bound to GitHub Actions (integration15368),
and reject activation while any old full context is still required. The public
metadata endpoint was verified anonymously; no administrative scope is needed.
Missing, changed or uncertain rule evidence returns full. No ruleset write API.

This is a guard against premature activation, not authorization to migrate.
The future operator must separately approve the flag and the semantics of the
new verification gate. It is deliberately not named after an unrun full test.

### One full suite, guarded projection and caller identity

Keep tests.yml as the ordinary and parameterless reusable entry. Add trusted
route and final delivery control jobs. Preserve all thirteen original expanded
integration jobs exactly apart from explicitly tested needs/if wiring. Keep the
original reviewed hash inventory unchanged; compare a narrowly normalized full
projection that removes only the exact approved controls. Mutation tests must
still reject changed commands, selectors, matrices, environments and masking.

Initial missing-policy bootstrap and disabled mode run all original jobs.
Root jobs depend on routing with a status-aware full-fallback condition; a failed
or missing router result cannot silently skip them. Aggregators retain their
failure-path behavior. The new final gate accepts complete full results, or a
successful omitted-work decision reverified from trusted main. Proof drift after
selection fails that gate, never converts a stale observation to acceptance.

Determine direct versus reusable invocation from the authenticated current run's
workflow path/identity, not its inherited event name or a caller-controlled input.
Calls from another workflow always force the complete suite. Nightly, manual and
release-branch runs always remain full. Direct release eligibility continues to
require its existing full jobs; historical candidate/landing evidence substitutes
for no release receipt.

### Scoped source route

Require the existing CI-Shadow marker plus a separate exact CI-Delivery:
claude-current marker so earlier observational intent cannot activate routing.
Use the existing regular-file/mode, live head/base/main and independent-ancestry
checks. The source fast workflow stays separate and unprivileged. A bounded
wait may allow its current pending run to finish; the existing exact workflow,
job/current-attempt and base-retarget-history verifier proves success. Recheck
identities and effective policy before emitting fixed mode/boolean/digest outputs.
Malformed/unknown sources, missing fast evidence and reads retain full CI.

### Immutable candidate evidence and a distinct landing reader

Keep open admission strict; do not fabricate push flags or relax it for closed
PRs. Trusted candidate admission and post-integration qualification write small
versioned JSON records into the runner's temporary directory and upload them
under exact attempt-suffixed names using the existing pinned artifact action.
The CLI takes no destination path. Only exclusive proof.json creation inside
canonical Actions runner.temp is allowed, with no overwrite or symlink following.
No source status writer or write-scoped GitHub token is introduced. Records
contain validated identities/digests and intent flags, never arbitrary PR bodies,
review text or credentials.

A read-only evidence loader binds artifact ID/name/digest/run/head/repository,
current first attempt, caller path and referenced immutable workflow revisions.
Download by numeric ID, bound archive/JSON sizes, check the ZIP byte digest,
accept one exact JSON member without extracting, then reread provenance. Missing,
expired, duplicate, oversized, replaced or malformed records do not qualify.

Candidate-PR duplicate omission requires an authentic live push run and its
verified-admission record, plus independent current immutable/scope/member checks.
Waiting for the admission record is bounded; uncertain starts remain full.
The actual push workflow still performs its own complete verification/full call.

After every full job and final live verification succeeds, upload qualified
evidence. Expose a unique top-level openrepotools-batch-heavy job in the push
caller; it requires successful complete reuse and rechecks live candidate proof.
The named queue waits for this check, not an ambiguous nested job suffix.

For a main landing, independently validate the completed push run and every
exact expected expanded job in attempt1. Require its qualified immutable record,
consistent caller/callee revisions and unchanged policy/commands. Reconstruct
the bounded candidate chain and compare every original source head, prefix
parent/tree and GitHub merged record after heavy completion to the actual main
event. Already-closed sources are historical facts here, not fresh admission.
Only this authenticated Mergify landing can omit duplicate ordinary full work.
Unknown main pushes, incomplete history, manual/lookalike merges, reruns or changed
policy cost a full run. Keep discovery and all API/Git operations bounded.

### Separate manual queue and current protection

Add openrepotools-claude-current for open non-draft same-repository main-based
independent members, fixed paths and both intent markers. Require app-qualified
fast and delivery verification on admission, and the top-level heavy result on
the combined candidate. Retain explicit/default branch-protection injection at
queue and merge; never select none or shift requirements to dodge the old checks.
Keep earlier queues byte-for-byte, one serial candidate, max2 members,60-second
fill,90-minute budget, no retries/splits, no automatic rule or queue command.

With disabled routing, delivery means the original full checks actually ran.
After an explicitly approved migration it may mean scoped fast verification;
heavy integration is enforced by the named manual Mergify queue. A collaborator
could otherwise directly merge a fast-verified source under that changed GitHub
floor. Therefore direct fast-source merges are NOT authorized by this gate.
Manual-queue-only usage is operator-enforced, as in the reference workflow,
unless separately approved service controls enforce it. Disclose this tradeoff;
do not claim this config makes every outside merge path impossible.

### Research and validation

Reference: OpenBenches5df4fa0. Its separate admission and historical readers,
policy-path comparison, exact attempt/job checks, prefix trees and held queue
are reusable proof patterns, not authority to copy its scopes or review receipts.
Source actual reusable run43115161206 confirms caller run.path and prefixed
job names. Two-level new prefixes remain a conservative schema to confirm in
a separately approved canary, never a claimed live result in this PR.

Test both disabled and hypothetical enabled routing with real temporary Git and
mocked read APIs/artifact bytes. Include forged/missing/raced evidence, scopes,
API pagination, current rules, caller confusion, duplicate jobs, failed/skipped/
cancelled calls, artifact traversal/size/digest/schema errors, prefix/merge/policy
mismatches and actual shell gates. Preserve all existing delivery/shadow/queue/
parity/shard regressions. Source config PRs stay full during bootstrap.

## Risks / Trade-offs

- Future activation changes the GitHub source merge floor -> explicit owner
  semantic approval, verified effective gate, disabled default and mandatory
  queue procedure; no bypass or hidden full-test aliases.
- Bot review quota prevents actual future candidate proof -> retain its real
  managed-review contract; restoring capacity or a different auditable trust
  contract is a separate decision, not a forged local-review status.
- Candidate/non-gating timing races -> the dedicated queue holds final heavy;
  stable exact evidence only, otherwise full/failure, not a guessed success.
- Concurrent Sonar work -> keep its baseline/instrumentation changes and qualify
  the integrated latest head before any later merge; do not misattribute batch
  coverage to separately untested source heads or substitute for Sonar acceptance.
- Historical evidence expires or policy changes -> extra full CI is safe fallback.

## Migration Plan

Prepare/open code and spec PRs only, with flag false and all existing full checks
running. Require strict OpenSpec, focused unprivileged/offline tests, workflow
lint, full hosted config CI and exact-head review before a separately authorized
source merge. Reconcile under issue26; do not archive or update assembly pins.

Later, with explicit authority: verify schema/plan/access and exact check names;
document/approve the source-floor and manual-only semantics; install the honest
required delivery context and remove old full contexts as a deliberate owner
change; separately enable the flag through a checked config PR; qualify failure/
recovery and a live two-member efficiency canary, then compare all tested/landed
trees and actual run counts. Do not claim this preparation performs those acts.

Rollback is an ordinary checked change to enabled=false: all original integration
jobs resume without changing their commands or manufacturing statuses. Restoring
legacy required contexts is a separate approved setting change. Installed tools,
lanes, releases and active plans remain untouched throughout preparation.
