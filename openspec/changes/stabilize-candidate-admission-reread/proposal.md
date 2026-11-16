# Proposal

## Why

The approved P05/P06 specimen, candidate108 / caller43889273596, authenticated
admission and the exact expected merge trees. Candidate-PR tests43889288858
nevertheless retained full CI at the candidate boundary with generic
`incomplete-evidence`. It was normally withdrawn and its two runs cancelled;
neither source landed. The historical log does not identify the exact rejected
read. Artifact publication at14:47:21Z and caller job transition at14:47:23Z,
before refusal at14:47:26Z, are consistent with a caller-read race, not proof of it.

The existing final candidate reread already supports fresh bounded observation,
but the same authenticated movement inside the artifact reader refuses without
that observation path. Reproduce and repair this specific inconsistency.

## What Changes

- On admission-only pending-enabled reads, discard an observation whose caller
  changed only after independently authenticating that same first-attempt caller.
- Reuse the existing bounded observation loop; never accept the changed view,
  change the proof, extend the wait budget or retry a candidate/heavy workflow.
- Preserve immediate refusal for artifact mutation, invalid/failed/foreign/rerun
  callers, forged/incomplete evidence and all qualified/historical reads.
- Add fixed-vocabulary diagnostics and regressions for both positive and refusal
  paths, then qualify normal source merges before a fresh owner-consented canary.

## Impact

Paired code/spec repair only. No workflow, job, coverage baseline, policy flag,
profile registry, queue, engine/helper, settings/app access, paid upgrade, tool,
release, assembly pin, shared checkout or running lane change. Preserve lanes1–5.
