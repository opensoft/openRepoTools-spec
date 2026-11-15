# Local validation

Initial local runtime qualification commit: `e8fca0213f092ebdb4046d319e8e42011b7a980d`.

- All 20 new grouped-lookup cases pass on the source and the installed candidate.
- All 280 existing selected estate and lane regression cases pass on the installed candidate. Four initial new-fixture setup failures were corrected and the complete new-case set rerun successfully; product bytes did not change after the broad regression run.
- Bash syntax and OpenSpec strict validation pass. Discovery uses the same self-contained helper in all four commands.
- A read-only alias-free workspace simulation discovers all 53 canonical targets previously held by tooling defaults: 52 names resolve uniquely; one genuine duplicate requires explicit selection.
- The lookup-only local overlay is installed and hash-verified in managed bench and native Linux locations. Native execution additionally verifies executable modes, ownership and canonical lookups for five development projects.
- Preservation readback confirms 24 assembly pins, 10 SDK links, 27 independent register Git directories, 62 root aliases and 367 scoped lane-state entries remain unchanged. Original assembly and pinned-leg HEADs, branches, indexes and tracked changes match the initial snapshot. Bench identities and start times are unchanged.

The original primary checkouts and assembly pins remain at their existing revisions. This is local source and runtime qualification; hosted CI, source landing, release and assembly adoption are separate gates. A later install from an older pinned payload can replace the local overlay, so durable adoption is required before relying on alias retirement across rebuilds.

Machine-specific receipts, backups and operational logs remain outside the maintained source trees.


## Codex compatibility follow-up

Code PR #104 adds correction `9d462a0` for logical family aliases whose physical container has a different basename. Eight new cases pass for named/all park and status, resume by name/family ID, origin lookup, duplicate aliases and an invalid alias preceding a valid one. Bash syntax checks pass and there are no new ShellCheck warnings. The existing source and hosted CI gates remain required at the corrected head.

Logical paths now drive family recognition and dispatch; physical identity is the deduplication key after recognition. Lane defaults retain their canonical-path behavior. This PR completion does not refresh the earlier installed overlay or advance pins; the initial runtime receipt above describes that earlier overlay.
