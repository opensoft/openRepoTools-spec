# Cutover records and transitions

Policy: exact schema_version1 + enabled boolean, committed false; scope/provider/
gate constants are code-owned, not selectable config. Missing/extra/malformed is full.

Routing: full/omit_full=false, or source/candidate/landing + omit_full=true +
canonical SHA256 proof digest. Outputs contain fixed vocabulary and validated
hex only. Selected omission is independently reverified by the final delivery
gate; drift fails, never manufactures an old full result.

Receipt: schema_version1, phase(admission|qualified), fixed repository+numeric ID,
run ID, attempt1, exact queue branch and proof. Proof records verified/full-required
booleans, base/candidate/tree SHA and one/two unique members(number/head/prefix/tree/
delivery boolean). No raw bodies, reviews, credentials or arbitrary URL.

Artifact envelope: exact phase/head/attempt name, numeric immutable ID, bounded ZIP
size, archive SHA256 and matching run/repository/head. Exactly proof.json, no
extraction. Re-read metadata/current run after parsing. Qualified proof also needs
first-attempt completed-success caller, immutable callees and all nineteen exact
expanded successful jobs. A receipt alone is not a successful heavy call.

Source -> live scoped fast verification -> manual operator admission -> immutable
candidate admission -> full parameterless reuse -> qualified artifact -> top-level
heavy live recheck -> actual ordered merges -> separate historical prefix proof.
Every unknown/stale/missing edge retains full or fails already-selected omission.
Full source eligibility and complete candidate/release/assembly acceptance remain
different facts. No automatic queue or state-setting API exists in the controller.
