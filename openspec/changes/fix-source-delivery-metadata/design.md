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
as live evidence, a receipt or queue authority. All non-body snapshot fields,
including merge identity, stay exact. Later PR/API rereads remain byte-strict.

This avoids brittle tuple-index slicing and a new general normalization API.
Reject the alternatives of removing all metadata checks, trusting Sourcery,
disabling summaries, expanding permissions or altering triggers. No new output
schema or potentially sensitive payload logging is needed for this bounded fix.

Positive regressions reproduce appended/replaced/removed unrelated prose and
both supported source bodies. Negative cross-products cover invalid event/live
intent, dependency declaration and every other snapshot field. A final-read
prose race must still retain full. Exact existing full and receipt tests remain.

## Risks / Trade-offs

Other runtime differences can still refuse: preserve and diagnose them rather
than expanding trust. Pre-routing explanatory prose is not authorization;
code/head/base, manual writer intent, review and heavy qualification still govern.
The advisory observer may conservatively report full for the old body mismatch;
it is not a gate and is deliberately outside this narrow runtime repair.

## Migration Plan

Feature 009 T044-T048 is the sole new execution sequence. Qualify exact reviewed
code/spec heads and normal protected merges, verify actual main, then use fresh
main-based sources and new review receipts. Preserve old attempt records and
retire only superseded coordinator-owned PRs without deleting branches. Source
eligibility, one-heavy/no-duplicate and prefix/final-tree gates remain unchanged.
The owner pre-approved scoped fixes, checked merges and fresh canaries; no new
per-PR approval is needed within that boundary. Assembly pins, tools, sessions,
rules/app/paid access and releases remain excluded. No archive before separate
assembly adoption. An ordinary reviewed revert is available; never bypass.
