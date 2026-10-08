# openRepoTools-spec

The **spec leg** of the `openrepotools` project: requirements, decisions and
acceptance criteria. The implementation lives in
[`opensoft/openRepoTools-code`](https://github.com/opensoft/openRepoTools-code).

**Clone the assembly root, not this repository.** This leg is mounted as a
submodule at `spec/` inside
[`opensoft/openRepoTools`](https://github.com/opensoft/openRepoTools), which
is what pins the commit of this repository that the project currently is:

```sh
git clone --recurse-submodules https://github.com/opensoft/openRepoTools.git
cd openRepoTools
make bootstrap
```

Working here directly is fine — it is an ordinary repository with an ordinary
branch. What advancing this leg does NOT do is advance the project: that is a
commit in the assembly root moving the gitlink, `contracts/spec-pin.yaml` and
any workflow `@<sha>` reference together.

Being the spec leg confers no authority over specifications. The split is
navigation; authority travels in grants, and a project that keeps spec and
code in one repository is reviewed identically.

Topic: `xf-project-openrepotools`.

## What is in it

`openspec/` holds the project's full OpenSpec proposals, `specs/` its Speckit
feature directories, `docs/` the manuals — `docs/README-lanes.md` for the lane
tooling and `docs/README-claude-current.md` for `claude-current` and
`claude-restart-check` — and `ideation/` the brainstorm packets the proposals
grew from. The commands, their tests and their CI are in
[`opensoft/openRepoTools-code`](https://github.com/opensoft/openRepoTools-code).
`openspec validate <change> --strict` runs from this leg's root.

Licence: Apache-2.0. See [LICENSE](LICENSE).
