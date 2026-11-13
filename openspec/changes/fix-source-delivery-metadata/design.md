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
events, not globally to snapshot semantics. Candidate bodies remain exact, and
candidate omission still requires authenticated real admission and independently
revalidated sources. No new retry, hint fetching, artifact or permission path.
Regressions cover supported actions, nullable/current combinations, unknown
hints, bad tested commits, changed live hints, strict candidate bodies and
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

## Risks / Trade-offs

Other runtime differences can still refuse: preserve and diagnose them rather
than expanding trust. Pre-routing explanatory prose is not authorization;
code/head/base, manual writer intent, review and heavy qualification still govern.
The advisory observer may conservatively report full for the old body mismatch;
it is not a gate and is deliberately outside this narrow runtime repair.

## Migration Plan

Feature 009 T044-T058 is the sole execution sequence. Qualify exact reviewed
code/spec heads and normal protected merges, verify actual main, then use fresh
main-based sources and new review receipts. Preserve old attempt records and
retire only superseded coordinator-owned PRs without deleting branches. Source
eligibility, one-heavy/no-duplicate and prefix/final-tree gates remain unchanged.
The owner pre-approved scoped fixes, checked merges and fresh canaries; no new
per-PR approval is needed within that boundary. Assembly pins, tools, sessions,
rules/app/paid access and releases remain excluded. No archive before separate
assembly adoption. An ordinary reviewed revert is available; never bypass.
