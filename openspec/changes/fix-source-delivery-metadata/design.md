# Design

## Context

See proposal.md. The source predicate compares a strict snapshot containing the
entire body. Recorded Sourcery edits precede both initial routing jobs. Separate
fast provenance and offline reproduction succeeded; live efficiency did not.

## Goals / Non-Goals

Allow pre-routing prose edits without trusting actors or relaxing delivery
decisions. Do not change snapshot semantics globally, supported actions, queue
admission, local review, artifact schemas, full jobs or configuration settings.

## Decisions

At the source event-versus-live comparison only, substitute the event body into
a shallow copy of the current PR before comparing existing snapshots. Validate
the event's unique supported intents and dependency absence independently; keep
the existing live intent/dependency checks. The temporary copy is never passed
as live evidence, a receipt or queue authority. All other snapshot identities
stay exact except the bounded generated-hint handling below. Later PR/API
rereads remain byte-strict, including the hint and full body.

This avoids brittle tuple-index slicing and a new general normalization API.
Reject the alternatives of removing all metadata checks, trusting Sourcery,
disabling summaries, expanding permissions or altering triggers. No new output
proof schema or potentially sensitive payload logging is needed for this bounded fix.

The fresh R2 source PRs 60/61 still selected full before queue admission. Their
logs do not identify the failed predicate, so do not infer that nullable event
metadata or a fast-check startup race caused this attempt. Add stderr-only,
fixed-vocabulary verification-stage/refusal codes and source-snapshot mismatch
field names, never values. Map only known literal refusals to codes; other
exceptions receive one generic code without their text. Keep the exact routing
result, strict snapshot/live checks, pending policy and proof schema unchanged.
Qualify this diagnostic-only configuration normally before obtaining a new
live event; historical failures are not rerun into clean acceptance.

R3 diagnostic source PR65 subsequently selected full with an explicit
snapshot-mismatch on merge_commit_sha, followed by source-metadata refusal.
This proves the rejecting field, not the original raw hint's value. Fast CI
independently verified the actual tested merge and its ordered base/head parents.
Do not treat the generated REST/event hint as the tested-commit authority.

For the initial event/live comparison only, both hints must be missing/null or
equal the trusted workflow GITHUB_SHA. Only then may a shallow comparison copy
align that field. Malformed or different non-null hints retain full, even when
both API snapshots agree on them. The actual tested SHA must still be acquired
and independently checked against ordered main/head parents. Every other
identity, source intent, fast receipt and final strict reread remains mandatory.

Apply this same bounded hint validation to source and genuine candidate-PR
events, not globally to snapshot semantics. Later candidate bodies remain exact, and
candidate omission still requires authenticated real admission and independently
revalidated sources. No new retry, hint fetching, artifact or permission path.
Regressions cover supported actions, nullable/current combinations, unknown
hints, bad tested commits, changed live hints, strict later candidate bodies and
missing/corrupt admission. Fresh live acceptance remains required.

Positive regressions reproduce appended/replaced/removed unrelated prose and
both supported source bodies. Negative cross-products cover invalid event/live
intent, dependency declaration and every other snapshot field. A final-read
prose race must still retain full. Exact existing full and receipt tests remain.

R4 source PR67 passed the initial metadata/Git checks, then logged fast-reread
at 21:39:54.806Z. Its fast check/job completed at 21:39:53Z and run updated at
21:39:54Z. These timings correlate with finalization; the exact differing API
fields were not captured. Preserve that distinction and the failed attempt.

The verifier currently demands raw run/check equality before handling an
authenticated pending observation. Reproduce completion during those reads.
After every initial identity, source association, base-history, job and state
check passes, any pending component may return only PendingEvidence before the
completed-proof rereads. This produces no proof or omission, and a fresh router
poll must independently revalidate everything. All-success observations still
require the same exact raw rereads. Admission's default allow_pending=false
path remains success-only. Failed, unknown or unauthenticated initial evidence
still refuses immediately; the existing thirty ten-second waits are unchanged.

This changes pending observation ordering, not completed-proof normalization,
identity checks, retry budget, artifact/review schemas or queue authority.
Regressions must reproduce within-read completion, require a separate fresh
successful poll, retain completed-proof drift refusals and success-only admission.

The same wait-versus-proof boundary applies to a genuine candidate caller whose
admission artifact is absent. A deterministic regression reproduces refusal
when that authenticated queued caller starts or completes during the empty
artifact-list read. In this absent-artifact, explicitly pending-enabled branch,
return only the existing wait after caller authentication, not a stable receipt.
A fresh poll must reacquire the caller and independently verify the actual
artifact. Invalid callers and default readers never wait; artifact-present
proofs retain every original digest, schema and strict row/run reread. No live
candidate failure is claimed: this is a reproduced same-class boundary defect.

R5 subsequently proved both independent source PRs fast-only and real two-member
admission, but draft PR73 selected full at 2026-11-14T00:27:07.974Z, stage
candidate/incomplete-evidence. Full duplicate jobs started alongside the genuine
heavy caller, so this is failed efficiency acceptance. Both specimen runs were
cancelled and both sources explicitly dequeued before landing; main stayed908d815.

GitHub's complete edit history confirms a nonempty Sourcery description edit at
00:27:05Z, after creation at00:26:55Z and before fallback. The first precise
rejecting predicate and original raw event body/hints were not logged. Do not
claim this chronology proves the sole historical cause. It does expose a
necessary incompatibility: the current initial candidate snapshot includes an
exact body comparison even though the candidate description grants no authority.

At only that initial candidate event/live comparison, require both descriptions
to be strings or null and substitute the event description in the existing
temporary comparison copy. Do not parse or trust member, review or admission
claims in either description. All other snapshot fields, the bounded generated
hint rule, genuine Mergify draft/ref/main/head identity, tested Git parents,
regular modes/paths, authenticated real admission and independently verified
live source intent/reviews remain mandatory. Missing or corrupt proof cannot
be replaced by prose. Never mutate the original current PR; final raw PR,
branch, caller and artifact rereads stay strict, including the full description.

Add candidate snapshot field-name diagnostics and fixed refusal vocabulary so
another failure can be investigated without guessing. Never emit body/hint
values, arbitrary exception text or API payloads. No workflow, queue, flag,
schema, wait budget, permission, app, installed-tool or release change follows.
Regression coverage must include append/replace/remove/null prose transitions,
invalid description types, unchanged non-description identities, missing/corrupt
admission and body mutation during final rereads. Qualify the repair normally
before genuinely fresh R6 sources; never rerun R5 into successful acceptance.

### R6 final candidate consistency race — CI-METADATA-16

R6 independently proved fast-only sources75/76 and real ordered two-member
admission for draft77, then logged candidate-reread at02:51:19.803Z. Duplicate
full jobs started beside the one held heavy caller. Both owned runs were
cancelled, sources dequeued and the draft closed before landing; main stayed
a15a6b5. This is failed efficiency acceptance, not an assertion failure or a
completed heavy result. Complete edit history records a Mergify description
edit at02:51:19Z during the02:50:51Z–02:51:19Z route step. The precise initial
raw field is unknown: the diagnostic identifies the final PR/branch/caller
compound reread, not its individual comparison. Preserve that limitation.

After all initial genuine candidate identity, tested Git, scope, real admission
and independent live source/review checks pass, distinguish an authenticated
still-valid moving view from proof. A final raw candidate PR or caller change
may produce only the existing bounded no-proof wait when its current identity
still qualifies. The next observation starts the entire verification again.
Do not accept normalized or mixed snapshots, mutate current data, cache an old
receipt, or manufacture an event. Accepted proof still requires exact final raw
equality. A changed branch head, invalid/moved candidate identity, malformed
body/hint, failed/foreign/rerun caller or invalid source/artifact proof retains
immediate full refusal; generic API errors do not become retryable.

Use the existing thirty ten-second waits, not another budget or unbounded loop.
Candidate gate newly uses that same per-invocation limit: it may delay refusal
while an authenticated view settles, but raises neither attempts/delay nor job
timeout and adds no nested retry budget. Route exhaustion retains full. Omitted-work candidate gate verification may
use the same bounded fresh re-observation, but must still match the original
route mode and proof digest exactly; exhaustion or a different proof refuses
the gate. Source gate semantics remain unchanged. No missing/unstable evidence
grants omission, successful integration or merge authority.

Emit only fixed final-read mismatch categories/field names so any next failure
can be distinguished without leaking descriptions or arbitrary API data.
Regressions must reproduce body/metadata and valid caller progress during reads,
require a separately fresh stable observation, preserve hard identity/ref/proof
refusals, and cover gate re-observation, changed proof and exhaustion. Qualify
the repair normally with complete source/main coverage before genuinely fresh
R7 sources; never requeue or rerun R6 into successful acceptance. The owner's
standing directly-related canary-repair approval covers this follow-up, not
settings, app/paid access, tools, releases, assembly pins or lane changes.

## Risks / Trade-offs

Other runtime differences can still refuse: preserve and diagnose them rather
than expanding trust. Pre-routing explanatory prose is not authorization;
code/head/base, manual writer intent, review and heavy qualification still govern.
The advisory observer may conservatively report full for the old body mismatch;
it is not a gate and is deliberately outside this narrow runtime repair.

## Migration Plan

Feature 009 T044-T064 is the sole execution sequence. Qualify exact reviewed
code/spec heads and normal protected merges, verify actual main, then use fresh
main-based sources and new review receipts. Preserve old attempt records and
retire only superseded coordinator-owned PRs without deleting branches. Source
eligibility, one-heavy/no-duplicate and prefix/final-tree gates remain unchanged.
The owner pre-approved scoped fixes, checked merges and fresh canaries; no new
per-PR approval is needed within that boundary. Assembly pins, tools, sessions,
rules/app/paid access and releases remain excluded. No archive before separate
assembly adoption. An ordinary reviewed revert is available; never bypass.
