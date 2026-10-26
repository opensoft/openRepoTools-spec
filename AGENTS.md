This is the **spec leg** of openRepoTools (`openrepotools`): requirements, decisions
and acceptance criteria.

**The project's rules are not here.** They are in the assembly root
`opensoft/openRepoTools`, in `AGENTS-shape.md` — read that before touching anything
that spans the legs. This leg is mounted there at `spec/`, and the other leg,
`opensoft/openRepoTools-code`, beside it at `../code/`, once the root is cloned with
`--recurse-submodules`.

Working here is ordinary — an ordinary repository on an ordinary branch. What
advancing this leg does NOT do is advance the project: that is ONE commit in
`opensoft/openRepoTools` moving the gitlink, `contracts/spec-pin.yaml` and every
workflow `@<sha>` for this leg together.

Being the spec leg confers no authority over specifications. The split is
navigation.

## What is particular to openRepoTools' spec leg

The project's own instructions are the assembly root's `AGENTS.md`, and they
govern here. This section adds only what standing in this leg changes.

- **What is here**: `openspec/` (full OpenSpec proposals with their delta
  specs, and their archive once archived), `specs/` (the Speckit feature
  directories, `specs/<NNN-feature>/`), `docs/` (the manuals:
  `README-lanes.md` for the lane tooling, `README-claude-current.md` for
  `claude-current` and `claude-restart-check`) and `ideation/` (brainstorm
  packets).
- **Full proposals and canonical Speckit files live here**, whichever branch
  they were written on. The one exception is a genuine bounded amendment to an
  approved baseline: it is recorded in the code leg's feature worktree, under
  `features/<NNN-feature>/openspec/`, and reconciled here once, when its
  feature completes (the shared workflow's "Triad Feature Amendments").
- **`openspec` runs here**, never from the assembly root, which has no
  `openspec/`: `openspec validate <change> --strict` from this leg's root, or
  from a feature's spec worktree, `worktrees/<NNN-feature>/spec/` under the
  assembly root, for a change on that feature's branch. A local root takes
  precedence over a docs-store pointer; pass `--store` only where it resolves
  to the checkout you mean.
- **The code these documents describe is the code leg's.** A path such as
  `tests/…`, `upstream/openRepoShape` or `contracts/openreposhape-pin.yaml` in a
  document written before the split names a file of
  `opensoft/openRepoTools-code` (`../code/` when mounted); where such a path has
  been repaired it reads `code/…`, relative to the assembly root. A commit of this
  project that such a document cites is a commit of `opensoft/openRepoTools`,
  the assembly, which kept the whole history and its branches; the split
  rewrote this leg's copy of that history, so the same change has a different
  hash here.
