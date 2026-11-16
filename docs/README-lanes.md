# `lanes/LANES.md` — how the estate lane register is stored, and the commands that write it

**THE TOOLING LIVES HERE; THE DATA LIVES IN YOUR WORKSPACE REPOSITORY.**
Amendment 9, 2026-09-13. This manual and the commands it then described —
`lanes-edit.sh`, `lane-start`, `lane-end` and `link-estates` — came to
`opensoft/openRepoTools` from `opensoft/brett-wip` with their history, because
a `<user>-wip` is a person's DATA and its own rules file says so: "No secrets
and no code". They are INSTALLED COMMANDS now, placed by
`openRepoTools --install`, and none of them is a file in anybody's workspace
repository any more.

**So every command below is typed by bare name**, and every one of them finds
your register the same way: through `$AGENT_PROTOCOL_ROOT/workspace.yaml`
(default `~/.agents/workspace.yaml`), which names your workspace repository and
where it is checked out. `openRepoTools wip init` is the one command that
creates that repository and writes that file. A helper that cannot find it
refuses, exit 1, naming that command — it never guesses, and it never falls
back to a path derived from its own location.

**Moved 2026-09-10 (Amendment 5).** The register lived on the orphan `lanes`
branch of `opensoft/xFactory` from 2026-09-09; it moved to `main` of the
person's workspace repository — for Brett, `opensoft/brett-wip` — ratified by
Brett Heap 2026-09-10 ("since we now have the user-wip repo this is a better place
to store our lanes", "create opensoft/brett-wip and move it all there"),
implemented by lane `openRepoShape-2`, session
`8fa66b30-4cf5-4b80-b40c-ac3640cf45ab`, on workstation **Eagle**. All 1242
register commits came across with it as a `git subtree`; the orphan branch is
retired behind a pointer. Rules 9 and 10 are unchanged in every mechanic:
every write is still one commit, made by `lanes-edit.sh`, and every row still
names its workstation.

Status of the original ruling: in force — ratified by Brett Heap 2026-09-09 (in-session, verbatim
"track LANES.md in git. but we need to make sure we have a workstation
designation too in the rows right? so that Raven/opsXfactory-2 and
Eagle/opsXfactory-2 are not confused."). Implemented by lane
`provenance-autonomous-merge`, session `f1356e27-665d-4119-b47e-a5e66efdce00`,
window `codeXfactory-3`, on workstation **Eagle**.

Governing protocol: `~/.agents/protocols/lane-collision-protocol.md` — Rule 9
(register in git) and Rule 10 (workstation designation), Amendment 3,
2026-09-09; **Amendment 5, 2026-09-10** (the register lives in the person's
workspace repository).

## Why

`LANES.md` is the estate's live-lane register (protocol Rule 4). Many
concurrent lanes on more than one workstation write to it. It used to be a
single untracked file with no history:

- **2026-09-08T23:46:33Z** — one session wrote the whole file from memory. That
  write dropped two other lanes' rows (`browser-ui-repair`,
  `doxbench-stewardship`) and a day of Rule 6 LANDING/LANDED lines. There was
  **no history to recover from**; peers rebuilt rows by hand from their
  handoffs and a 2026-09-04 `.bak`, and edits made in between are simply lost.

Git makes that recovery a `git show` instead of an archaeology exercise, and
the commit log makes every registry change attributable to a lane **and a
workstation**.

## Layout

| what | where |
|---|---|
| repository | `<org>/<login>-wip` — the person's own workspace repository, private, org-owned (the `<user>-wip` form, `openRepoShape#81`); named by `repository:` in `~/.agents/workspace.yaml`. Brett's is `opensoft/brett-wip`; Scott's would be `opensoft/scott-wip`, and the commands below are the same commands |
| branch | `main`. Direct commits are the norm there **by design**: the repository is excluded from the organisation's PR-only ruleset precisely so a per-edit register commit can land |
| checkout | `~/projects/<login>-wip/`, named by `path:` in `~/.agents/workspace.yaml` and created by `openRepoTools wip init` |
| how every command finds it | `$AGENT_PROTOCOL_ROOT/workspace.yaml` — `repository:` and `path:`, and nothing else. Never from the command's own location (Amendment 9(a)) |
| the register | `<the checkout>/lanes/LANES.md` |
| the path every lane already uses | `~/projects/xFactory/LANES.md` — a **symlink** to `<the checkout>/lanes/LANES.md` |
| the writer | `lanes-edit.sh`, an installed command in `~/.local/bin` (still symlinked as `~/projects/xFactory/lanes-edit.sh`, which is what Amendment 8(e)'s `SessionStart` hook names) |
| the two ends of a lane | `lane-start` and `lane-end`, installed commands — **regular files, not symlinks any more** |
| their tests | `tests/test_lane_helpers.sh` in `opensoft/openRepoTools` — run it from a checkout of that repository; it touches nothing real |
| the object logs | `<the checkout>/lanes/log/<lane>.md` — one per lane (Amendment 7) |
| the alias table | two layers: `repos.tsv` shipped by openRepoTools and installed beside the commands, then `<the checkout>/lanes/repos.tsv` where you keep an override (Amendment 9(b)) |
| who places the symlinks | `link-estates`, an installed command. It places the handoffs links, the register link and the `lanes-edit.sh` link — **and no longer the two `~/.local/bin` ones**, which `--install` owns |
| who installs all of it | `openRepoTools --install` (every command and the alias table, idempotent, all-or-nothing) |
| the lane alias table | `<the checkout>/lanes/aliases.tsv` — `<old><TAB><new><TAB><UTC>`, written by `lane-rename` and by nothing else, resolved by every reader that takes a lane name (Amendment 16(e)) |

Why `main` of the workspace repository and not the aggregation's: the
aggregation repo's `main` is PR-only (org rulesets `xFactory Tier-1 main
protection`, `required-checks-main`, `Require Code Owner Review`, all scoped
to `~DEFAULT_BRANCH`), so a per-edit commit cannot land there — which is why
the register spent 2026-09-09 to 2026-09-10 on an **orphan `lanes` branch** of
that repo instead. The workspace repository has no such gate and no product
code at all, so the register can sit on its `main` next to the handoffs it
cross-references. Nothing here is ever released, pinned, swept or bumped.

**The register no longer owns its whole checkout.** The workspace checkout
also holds `handoffs/` and `workspaces/`, which other lanes write. That is why
the writer uses a pathspec on every `git add` and `git commit`, so a peer's
half-written handoff can never ride along in a register commit. Since
Amendment 23 S3–S4 (#198) no writer pulls. Every write first fetches, under its
checkout's lock, and refuses (2, nothing written) a checkout it cannot bring to
origin's tip: a fetch that failed, an operation in progress, HEAD off `main`,
staged entries that are not its own, a commit origin lacks by content, a
diverged branch, a pending change to a path it commits (a hand edit of the
register refuses every writer but `commit`), or a fast-forward git refuses.
Otherwise it fast-forwards, makes its edit on origin's tip, commits it by
pathspec, pushes and reads origin back. A peer's unstaged change to another
file does not refuse it, unless the fast-forward would overwrite that file.

A local commit origin lacks — one kept on HEAD after six lost push races (exit
6), or one a hand made — is published by **the publish act**: `lanes-edit.sh
commit` with nothing staged and `lanes/LANES.md` clean. It writes nothing new,
lists the commits origin lacks (`git cherry -v origin/main HEAD`) before it
pushes, and pushes them only as a fast-forward of origin. Where origin has
moved past them it refuses (2), naming the recovery: archive them on origin,
then `git -C <the checkout> reset --keep origin/main`, then make again each edit
still wanted. It reads that queue on `refs/heads/main`, the branch the push
sends, with HEAD required to be on it, and pushes exactly the commit it read.
It refuses with 2 and pushes nothing unless every queued commit is a non-merge
commit whose every change is an added or modified regular file under `lanes/`
or `handoffs/`, and never bytecode, a cache or an environment. That refuses a
merge, a deletion (a lane rename's included), a type change, a symlink, a
gitlink and any other path. It is a path filter, not an ownership check:
another lane's register write passes it, so read the listing before you
publish a shared queue. Only `commit` publishes that queue: every other write
refuses over it, and with nothing ahead `commit` is the no-op it always was.

**On `mode=primary` the register is a database** (feature 007, v1.3.0).
`lanes/LANES.md`, the object logs, `lanes/aliases.tsv` and `lanes/archive/` are
then its rendered mirror. A write is one database transaction and makes no
fetch, commit or push of its own, and the publish act sends only queued commits
under `handoffs/` and a person's own commits of `lanes/repos.tsv` or
`lanes/log/README.md`. See "The SQL register".

## THE ONE HAZARD — `sed -i` DESTROYS THE SYMLINK

`~/projects/xFactory/LANES.md` is a symlink. GNU `sed -i` writes a temp file
and **renames it over the path**, which unlinks the symlink and leaves a
regular file. The registry then silently detaches from git: your edit is
invisible to `git status` in `.lanes/`, and the next lane's helper run commits
the *old* content.

Verified on Eagle, 2026-09-09:

```console
$ ls -la LANES.md
lrwxrwxrwx  LANES.md -> .lanes/LANES.md
$ sed -i 's/x/y/' LANES.md
$ ls -la LANES.md
-rw-r--r--  LANES.md            # <-- the symlink is GONE
```

Safe ways to write:

| operation | safe? |
|---|---|
| `lanes-edit.sh …` | **yes — use this** |
| `sed -i --follow-symlinks …` | yes (verified: link preserved) |
| `cat tmp > LANES.md` (any `>` redirect) | yes |
| `printf '…\n' >> LANES.md` (`>>` append) | yes |
| `python3` `open(path,'a')` / `open(path,'w')` | yes |
| **plain `sed -i`** | **NO — replaces the symlink** |
| `mv tmp LANES.md`, `cp tmp LANES.md` (no `--no-dereference`… `cp` is fine, `mv` is not) | `mv` **NO** |

If it happens: `rm LANES.md && ln -s .lanes/LANES.md LANES.md` after copying
your content into `.lanes/LANES.md`, then commit with `lanes-edit.sh commit`.

**On `mode=primary` only the link is mended.** `lanes/LANES.md` is then a
rendering of the register, and `commit` refuses a pending hand edit of it with
2, so of the table's rows only `lanes-edit.sh …` changes it there. Put the link
back without copying anything into the checkout, and make the change through
its verb. See "Hand edits" and "The SQL register".

## How to write to the registry

```bash
cd ~/projects/xFactory

# read (unchanged — every existing path and habit still works)
grep -n 'my-lane' LANES.md
./lanes-edit.sh verify-row my-lane          # first 200 / last 200 chars of the row

# SET a row's state cell — the common case, and it REPLACES the cell
# (Amendment 13(a)). The UTC is stamped by the writer.
lanes-edit.sh set-row-state my-lane "LANDED · PR #123 merged as abc1234"

# the NARRATIVE — what the lane did, found, launched, left — goes to the
# lane's own log, never to the row (Amendment 13(b))
LANES_LANE=my-lane lanes-edit.sh log NOTED lane:my-lane "rebased on main; the flake is the fixture's clock"
LANES_LANE=my-lane lanes-edit.sh log RULED lane:my-lane → opensoft/openRepoTools#29 "Ratify as drafted"
lanes-edit.sh history my-lane --since 2026-09-13T00:00Z

# change one exact substring inside a row (must match exactly once).
# The optional 4th argument becomes the commit subject — use it to record WHY.
./lanes-edit.sh replace-in-row my-lane "ACTIVE" "PARKED" "parked pending Brett's OQ-1 ruling"

# a Rule 6 LANDING / LANDED line (these are file-level lines, not rows)
./lanes-edit.sh append-line "LANDING — lane my-lane, session <uuid>@Eagle, 2026-09-09T01:00:00Z, PR #123 into openxFactory main"

# register a new lane
./lanes-edit.sh add-row "| \`my-lane\` | \`<uuid>\` / \`session_01…\` / window \`my-lane\` | Eagle / WSL2 / brett | 2026-09-09T01:00Z | <objects> | <handoff path> | ACTIVE |"

# you hand-edited .lanes/LANES.md with an allowed tool and want it committed
LANES_LANE=my-lane ./lanes-edit.sh commit "row rewritten by hand"
```

Every mutating subcommand FIRST resolves its `<lane>` — and `LANES_LANE` — to
the register row's own spelling, case-insensitively, before it reads or writes
anything, so a name typed in another case edits the row that is there instead of
creating a second one; two rows differing only by case are a refusal naming both
(Amendment 15). Then it does the same five things:

1. takes a `mkdir` lock in its checkout's `lanes/` (so two helper runs never
   interleave);
2. **S3, before the edit:** fetches `origin main` and brings the checkout to
   origin's tip, or refuses with 2 and nothing written, naming the checkout and
   the recovery (the Layout note above lists the rows). `LANES_NO_FETCH=1` is a
   fetch that failed, for a writer. A pending hand edit of `lanes/LANES.md`
   refuses every writer but `commit`, which carries it across the
   fast-forward and commits it as its own act. A row edit carries even where
   origin changed the row beside it, where every line it replaces occurs
   once in origin's version and once in the version it was made on (a
   repeated or blank line is no anchor, so such an edit is refused, kept and
   named), and anything that no longer applies is
   kept in a file under the checkout's `.git/lanes-edit/` and named. The old
   `LANES(pre-existing@…)` capture and `--sweep` are withdrawn (2). For
   `claim` and `add-row`, whose own checks ran before the lock, if S3's fetch
   finds origin moved since those checks read it, nothing is written and the
   verb runs again from the top with the lock held (within S4's six attempts;
   2 at the sixth). The re-run makes no GitHub read: Rule 1's three reads
   made at attempt 1 are printed and cited as they were;
3. re-reads the file, edits **exactly one line** in place, and refuses if
   `git diff --numstat` says more than one line moved;
4. commits as `LANES(<lane>@<workstation>): <what>` — workstation from
   `hostname -s` — by pathspec;
5. **S4, after the edit:** pushes `main` with no pull and reads origin back.
   Confirmed is 0 and `pushed`. A push that went, or timed out, and cannot be
   confirmed is **3, `pushed, not confirmed: <sha>`**, never success: the commit
   stays on HEAD (unless another writer in the checkout rewrote it while its
   push was in flight, which the words then say), and the next write's S3
   finds it by content (published,
   nothing to do; not, every write refuses until the publish act sends it). A
   push refused because origin moved drops this write's commit (only while
   the branch still holds it and it sits on origin's history, then the tree,
   as `reset --keep` moves it) and
   runs the same write again on origin's new tip, so its
   own checks re-run on the new content — `claim`'s rescan included, which
   ends a lost race as `CLAIM-LOST` (7). `commit`'s carry form drops to its
   own commit's parent instead, by design: the change goes back to the
   working tree as the hand edit it was, and the re-run carries it again,
   or, where a commit under it is not on origin (an unlocked writer's),
   refuses at S3 (2) with the edit in the working tree and its copy kept.
   After six attempts, or a refusal with origin unmoved, the commit is kept
   on HEAD and named (6).

**On `mode=primary` none of these five things happens.** The verb runs its own
code on a scratch tree that the register builds at its head, and sends its
change set as one database transaction, which commits whole or changes nothing:
no `mkdir` lock, no fetch, no commit and no push of its own, and no S3 or S4. A
write ends 0, 2, 3, 69 or 75 (a verb's own refusals before its commit keep their
own codes, and on primary `request-handoff`'s refusals that row 64 lists, before
its claim, are 64);
it ends 1, with nothing written, where its environment or its own working files
cannot be had. 75 wrote nothing, and so did 69 but for `request-handoff`'s wait,
whose request stands, and for one internal error that cannot say whether the
write reached the register; 3 means the write **may not be written**, and prints
the `LANES_WRITE_ID` rerun that writes it once or finds it, or means that an
identical write still holds it, this run sent nothing, and the message says
what to do (the exit table's row 3);
2 is a refusal, the register's own or the verb's, and wrote nothing, as the
rows of the exit table below say. The files in the checkout are then the
register's mirror. See "The SQL register".

**No write rebases, and no recovery resets with `--hard`.** If a write refuses
over commits origin lacks, list them first — `git -C <the checkout> cherry -v
origin/main HEAD` — because in a shared checkout not every one is yours. If any
`+` line is not your own write, STOP and take it to the lane its subject names.
Never reset, stash or overwrite a peer's work. Publish your own with the
publish act; where origin has moved past them, archive them
(`git -C <the checkout> push origin HEAD:refs/heads/archive/<lane>-stranded-<UTC>`),
then `git -C <the checkout> reset --keep origin/main`, which keeps every
uncommitted change, and make each edit again on the register as it now is.

The script spells no path of its own: it finds the checkout root with `git
rev-parse --show-toplevel` from its own directory and derives the pathspec
`lanes/LANES.md` with `--show-prefix`, so the clone may live anywhere. The
overrides `LANES_REPO`, `LANES_PATH` and `LANES_BRANCH` exist for tests.

## Starting and ending a lane

**`lane-start` and `lane-end` read the register through verbs in every mode**
(`register-text`, `register-grep`, `canon-lane` and the rest), and
`register-text` and `register-grep` end 69 where a read fails (the exit table's
row 69). **On `mode=primary`** they never read it by awk over the file or
`git grep` of the ref, and `lane-start` refuses the launch, with the verb's own
code, where one of seven reads answers 75 or 3 (or 69, at two of them); its
three register writes (the row or the session cell, the state and the `STARTED`
or `RESUMED` line) are one write, `start-record`, in every mode; and
`lane-end`'s sweeps hold what they could not read and delete nothing on it. See
"The SQL register".

### One word: `lane`

```console
$ lane                          # the lanes, numbered, and ONE question: which?
$ lane openRepoShape-2          # straight to that one, asking nothing
$ lane openRepoShape-2 team-05e # …under another account (the late swap)
$ lane --all                    # every repository, grouped
```

**This is the word, and it is Brett Heap's own** (lane-collision-protocol
**Amendment 18 Addendum 1**, ratified 2026-09-14T14:05:54Z verbatim *"ratify the
addendum"*), out of his two messages that morning, verbatim: *"i dont understand
in another window i want to attach to that lane. what command do i use to do
that?"* and *"this is too hard for users. we need simple way to list the lanes
and then pick one to bind."*

A bare `lane` prints clause (i)'s listing as a **numbered pick** — available
lanes first (PARKED, or a binding this host proves dead), lanes **LIVE HERE**
next with where each one is (`window <n> of this session`, `detached session
<name>`), lanes **bound elsewhere** last with the workstation holding them;
closed and dormant lanes hidden (Amendment 19) — and asks **one** question:
`which? [1-N, f = a new lane at the next free position (<repo>-<n>), q]`. Inside
a checkout it is that repository's lanes; outside one, or with `--all`, every
repository, grouped.

**Three branches, one per state** (clause (i-2)):

| the lane is | `lane` does |
|---|---|
| **available** — PARKED, or a binding this host proves dead | `cd` to its **own recorded directory** and relaunch it **through the launcher** under its **own recorded profile**: `pclaude --lane <lane> <profile>`. Never a bare `lane-start` under whatever profile the shell carries, which outside a launcher session is the default config directory and therefore the wrong account. `lane <name> <profile>` names another one — the late swap |
| **LIVE HERE** | **attaches, and never starts a second process** (clause (h)). Inside tmux: `tmux move-window -a -s <session>:<@id>` then `tmux select-window -t <session>:<@id>` — the `@id` on both, so tmux resolves the window inside the session the record named or not at all — and where the window is already in this session, only the select. With `--switch`: the same select first, and `tmux switch-client` only after it worked. Outside tmux: `tmux attach -t <session>:<@id>` — the window named in the attach itself, which tmux resolves in that session or refuses — with that window selected first |
| **bound elsewhere** | clause (c)'s question, the **handoff request**. That request is `brettheap/new-workstation#38`'s act 4 and does not exist yet, so until it does `lane` **refuses and names where the lane is bound**. It never offers a launch: a second session on a lane another workstation holds is the collision |

A row whose `STATE` is read from its own state cell — `PAUSED (row)`, because
its log has no lane-kind line — is in **none** of the three: `lane` does not
number it, and `lane <name>` refuses it, because there is no log to resume it
from (`openRepoTools#89`; Amendment 19 below).

**The window is matched by its id AND its session name.** tmux reissues `@` ids
from `@0` when its server is replaced — measured here on 2026-09-13, where 0 of
5 recorded windows resolved — so an id alone would attach to whatever pane has
since been given it. Where the two disagree `lane` refuses and names both.
Before attaching a LIVE lane, `lane` also checks that the window's live
transcript is the row's latest UUID and is named for that lane. It checks all
tmux windows for another window using the same lane name (ignoring multiple
links to the same window ID), then renames the selected window to the lane when
needed. A window already named for a different registered lane is not renamed.
If the transcript or row is behind, it refuses instead of disguising
the mismatch; run `lane-start --no-launch <lane>` in that window to record the
new transcript. `lane-start` applies the same name-uniqueness check before a
NEW lane's launch renames its window; an existing lane's own window in another
container keeps its name until it hands off (Amendment 18), so it is not asked there.

**With no terminal on stdin it lists, suggests and asks nothing** (clause
(i-3)): an agent's stdin is not a terminal. **Bare `lane-start` is `lane`**
(clause (i-4)) — one implementation, and clause (i)'s ruled behaviour stands.

`lanes` is the same rows as a **read**: every column, closed lanes included, no
question. `/restart` is the act from **inside** a running session. And `restart`
is gone from the PATH — the section below.

### `lane-start` and `lane-end`

The lane rule, ruled by Brett Heap 2026-09-10: **lane = tmux window name =
Claude session name**, in the form `<repo>-<position across, left to right>`.
The working directory confers NO lane — two lanes may share one; a window
still called `claude` has no lane at all; and whether a name is free is
decided by whether the row's recorded session id is **live**, never by whether
a row exists.

**A lane name is ONE name under any case, and the register row's spelling is
canonical** (Amendment 15, in force 2026-09-14T00:07:18Z). `lane-start
openxfactory 2` and `lane-start openXfactory 2` are the same lane: every lookup
of a name — the row, the object log, the tmux window, the session name,
`LANES_LANE`, and every `<lane>` argument of the commands here — compares
case-insensitively, and the row's own spelling is what the window is renamed to,
what the launch is `--name`d with, what the log file is called, and what every
line and stamp writes. A name typed in another case RESOLVES to it and is never
written as typed. A lane with no row yet takes the checkout's REAL directory
name for its `<repo>` half, resolved under the projects root case-insensitively;
two directories differing only by case are a refusal naming both, because
choosing one of them is choosing which repository the lane is in. Where the
register already holds two rows differing only by case, **every writer refuses,
naming both**, until they are merged by hand — 15(d): the newer row's session
id(s) appended to the older row's session cell, in order, and the newer row
removed in the SAME commit, whose message names both spellings. `lanes-edit.sh
canon-lane <name>` is that resolver as a read, and the one the commands share
rather than each keeping its own: **0** with the canonical spelling (the typed
one where no row matches, so `add-row` can still create a lane), **2** for the
pair, **64** usage — and that
2 is RELAYED by `lane-start`, `lane-end` and `lane` rather than carried past,
because the pair may be on `origin/<branch>` while this checkout still holds one
row. `add-row` asks the published register as well as this copy of it for the
same reason: a row a peer pushed is a refusal here, naming it and the row verbs
(`set-row-state`, `replace-in-row`) that act on it, each of which fast-forwards
this checkout to it first (Amendment 23 S3), and never a second row. The LOG is
held to the same rule — two files under `lanes/log/` whose names differ only by
case are two logs for one lane, and every read and every write under that name
refuses, naming both, until they are merged.

Honouring that by hand is five acts at one end of a lane and one at the other,
and this register records where they get dropped: rows added hours late (`ROW
ADDED LATE 2026-09-09T18:05Z — the lane worked from 00:10Z without a row, a
Rule 4 gap this row closes`), windows left named `claude`, `(2)`-suffixed
session names minted when a rename collided with a title that was still held,
and lanes that stopped without ever updating their row. `lane-start` and
`lane-end` are those acts, one command at each boundary — never mid-session,
because renaming a running window's session is exactly what mints the `(2)`.

```sh
lane-start openRepoShape 2          # in the tmux window that will carry the lane
lane-start openRepoShape-2          # the full lane name works too
lane-start 2                        # <repo> from the cwd's git root

lane-end openRepoShape-2            # the window is closing
lane-end openRepoShape-2 --retire   # …and the lane is not coming back
lane-end openRepoShape-2 --inventory-only  # …past the close-out gate (#163), trees left recorded
```

Both print every step to **stderr** and refuse with a message that names the
fix. Exit codes: **0** done, **1** environment, **2** refusal — and under
Amendment 8 (R-A8-3) `lane-start`'s **2 is for ARGUMENT ERRORS and nothing
else**: a name that is not a lane, a position that is not a position, a name a
window cannot take, a lane whose register holds two rows, a lane live in
another window. **A person's answer is never an argument error.** `--confirm`
answered `N`, and `--confirm` with no terminal and no `--yes`, rename nothing,
write nothing, print one notice and `exec` Claude **bare** with the
pass-through arguments, **exit 0**; under `--no-launch` that bare command is
printed on stdout so a launcher can exec it. `claude-profile` **`exec`s**
`lane-start`, so this script's status *is* the launcher's, and a refusal here
would be an exited pane with no Claude in it — the one failure the change
exists to prevent. `--help` prints the file's own header, which is the whole
manual.

### `lane-start <repo> <n>`

1. derives `LANE=<repo>-<n>` and the lane's directory — `~/projects/<repo>`,
   or `--dir <path>`. Both halves resolve case-insensitively under Amendment
   15: `<repo>` to the checkout's real directory name, and the lane to the
   register row's own spelling, which then wins over everything typed;
2. **refuses unless it is already inside tmux.** It renames the window it is
   run in and never creates a tmux session: the window is the lane;
3. **liveness check, before it takes the name — and it is always asked.** It
   reads the session id(s) out of the row's *session cell* — not the whole row,
   which quotes other lanes' ids all the time — and looks for a live record in
   every profile's `sessions/*.json`. The row it reads is the PUBLISHED one,
   fetched, unioned with this checkout's (see `live-holder` below), because a
   gate that runs only when THIS checkout already names an id skips exactly when
   the answer matters: a uuid another workstation has stamped and pushed reads
   here as "no ids, nothing to check". `lane-start` also asks the published
   register whether the lane already HAS a row before it adds one, and refuses
   rather than writing a second — two rows for one lane is a register
   `lanes-edit.sh` cannot edit at all, because `row_line` requires exactly one. A record is
   live when its pid is alive **and** `/proc/<pid>/stat`'s start time still
   matches what the record wrote down, so a recycled pid cannot lock a name.
   A live holder in **another** tmux window is a refusal that names the next
   position; a row whose recorded sessions have all ended is a lane you are
   **resuming**, which is allowed and expected. A record whose session *name*
   is some other lane is not a holder either: this register carries rows whose
   session retired from the lane and was renamed, and refusing on those would
   lock free names forever;
3b. **the live session in THIS window** (Amendment 8), which is not step 3's
   question. Step 3 asks whether any session the *row* names is alive; this
   asks what is alive in this *window*, through `lanes-edit.sh window-session`.
   The harness mints a new transcript uuid with nobody acting — a `/clear` does
   it, and so did the 2026-09-12 usage reset that carried this lane's
   conversation into `4a91f1dc…` while its row still ended on `09dd34d1…`. An
   id in no row is an id `live-holder` cannot look up: it answers 8, *this lane
   is parked*, about the conversation sitting in the very window it was asked
   about, and `lane-start` would then resume the lane's **previous**
   conversation and write that uuid on to an append-only line. Read off the
   record instead, the id is appended to the session cell in step 5 — before
   the stamp — and it is what this run resumes and stamps. There is no flag for
   it and there could not be one: the id does not exist until the session does.
   The act is `lane-start --no-launch <repo> <n>` run **in** that window, which
   is what the SessionStart hook's block asks the new session to do. (The one
   caller already *inside* that session, the name guard, names it with `--bind
   <uuid>` — its binding mode, below — and that flag is the guard's alone);
3c. `--confirm`, when it was passed and this window is not already named for
   the lane: the **triple** — the window, the session live in it, the row — and
   the question `Take lane <lane> in this window? [y/N]`. Before the rename and
   before any write, so a No costs nothing. `--yes` answers it for a caller
   that has already asked. **Every answer leads somewhere and no answer
   exits.** An explicit `N` — and no terminal with no `--yes`, which is the
   same answer with nobody there to give it — renames nothing, writes nothing,
   prints **one** notice naming the lane it declined, and `exec`s Claude bare
   with the pass-through arguments, **exit 0**: `N` means *not this lane*, not
   *not at all*, and an unattended launch must still not take a lane on an
   inference nobody confirmed. The launcher passes `--confirm` exactly when the
   lane came from the swap *record* rather than from the window name or a flag;
4. `tmux rename-window <LANE>` — which also turns automatic-rename **off** for
   that window, so the name survives the next command it runs;
5. the row, through `lanes-edit.sh`: `add-row` when there is none under ANY
   case — it refuses one that exists under another and names it, and the
   liveness check above reaches the RESUME branch for that row rather than this
   one (Amendment 15(a)) — state `LIVE · <UTC> · <verb> by <uuid> (lane <lane>)
   — lane-start on <ws>: row created`, which is Amendment 13(a)'s phrase from
   birth (it was the bare word `STARTING` while the cell was a diary), session
   id `pending — set by the session's first act`,
   `<workstation> / <profile> / <user>` per Rule 10, handoff path
   `handoffs/<estate>/session-handoff-<date>-lane-<LANE>.md` — and
   `set-row-state` when there is one. Never a hand edit. **That state is
   Amendment 6(c)'s stamp**, written here rather than owed to the session
   afterwards — and since Amendment 13(a) it REPLACES the cell rather than
   being appended to it, with the UTC stamped by the writer:

   ```text
   LIVE · <UTC> · RESUMED by <uuid> (lane <lane>) — lane-start on <ws>: window renamed, launching <the command>; dir <dir>; window <ref>
   ```

   **The launch clause comes first and clause (c)'s `dir` and `window` follow
   it**, because the line is capped at 240 characters (Amendment 13, ratified
   decision O1) and a line long enough to be cut must lose what a reader can
   look up elsewhere: `dir` and `window` are sub-fields of this same act's
   `STARTED`/`RESUMED` line in the lane's own log and columns of `lanes`, while
   the exact command this run launched is written down nowhere else.

   The **tail states what happened**, so a `--no-launch` run ends `window
   renamed, no launch` and never claims to have launched anything.
   `STARTED` where a new session takes the name, and the uuid is the one this
   run actually resumes or mints, read from the **published** row. Under
   Amendment 6(b) that stamp is load-bearing — it is what the next `lane-start`
   resumes — so a stamp that is owed to a session which dies at a usage reset
   before its first act is a lost lane, which is the shape this estate hits
   daily. A brand-new row needs no stamp: it is created with that uuid already
   in its session cell;
6. the launch — decided *before* the row is written, because a brand-new
   session's id is minted there and the row must record it. **Resume beats
   new**, because a rename into a title that is still held is exactly what mints
   `<LANE> (2)` and costs the lane its address (Amendment 2: the session name
   *is* the messaging address). Four cases, in order: `exec claude --resume
   <id>`, where `<id>` is the **last** uuid in the row's session cell and this
   directory has its transcript (Amendment 6 — a lane is resumed by the id its
   row records, never by its title); the same exact command using the same
   agent's last `PAUSED … transcript <id>` when the row-last UUID has no
   transcript, restoring that exact ID to the end of the session cell first;
   `exec claude --resume <LANE>` when neither exact recorded id resolves but a
   transcript here carries the custom title `<LANE>`, which is the fallback and
   only filters the picker; and `exec claude --name <LANE> --session-id <fresh
   uuid>` for a lane with no history here. A live session in the current window
   remains authoritative over the PAUSED fallback. Anything after `--` is
   passed through to `claude`.

   **And the id it is about to resume is counted first, under two rules and not
   one.** Amendment 18(h)'s is this tooling's: a second LIVE PROCESS on one
   transcript is a refusal naming the pid and the retire act, and the harness
   permits that state — measured 2026-09-15 against `claude` 2.1.270, a
   `--resume` whose id a second *interactive* process holds starts anyway. The
   other rule is the HARNESS'S OWN, asked here because it decides whether the
   `exec` can happen at all: a `--resume <id>` whose id is carried by a live
   record whose `kind` is anything but `interactive` — a `--bg` job, a
   `bg-pty-host` child, the `bg` companion of Amendment 8 ruling (g) — prints
   `Session <id> is running as a background session … Add --fork-session to
   branch off a copy instead` and **exits 1 without starting**. Everything this
   command writes — the row, the object log, the handoff stamp — is written
   BEFORE the `exec`, so a launch the harness refuses leaves a lane recorded
   `RESUMED` by a session that never existed, and in a window `pclaude --lane`
   has just made, tmux prints `[exited]` over the message and closes it. So the
   count is asked on both sides of the current-window test and the refusal names
   where that session is (`tmux switch-client -t <window>`) and how to open it
   (`claude agents`, `claude attach <id>`, `claude stop <id>`) beside
   `lane-end <lane> --retire <pid>`.

   **The session cell it reads is the PUBLISHED one**, fetched, through the same
   `register-row` step 3 asks — like every other state read in this tooling. A
   `git fetch` does not move a working tree, so a uuid a peer stamped from
   another clone and pushed is absent from this checkout's copy of the row until
   something pulls: read from there, a cell published as `<U1> → <U2>` still
   ends at `<U1>`, and the lane resumes its **previous** conversation, appends a
   status naming that id, and writes a `RESUMED` line into `lanes/log/<lane>.md`
   carrying a session uuid that is not the lane's current one — in a file
   nothing ever rewrites. A row this checkout has and `origin/main` has not is
   an unpushed commit: `lane-start` says so, names the ids only this copy
   carries, and takes a new session rather than resuming one of them.

`--dry-run` prints all six and writes nothing. `--no-launch` does the first
five and prints the `claude` command on **stdout** instead of running it —
that is the hook a launcher uses, and it is the only thing either script ever
writes to stdout.

**`--bind <uuid>` is the name guard's binding mode, and nobody else's**
(openRepoTools#70). The guard passes it on a person's choice `2` at its offer
(see *The offer* below), naming the transcript of the session live in that
window. Step 3b then takes that session even where ANOTHER row records its uuid
— veto 1 answered by the person's `2`, which is the yes clause (h) rule 4 asks
for — and appends it to the session cell (anchored on the published last id, or
on `none recorded` / `pending — set by the session's first act` where the cell
has no uuid) before the stamp. The row's stamp, the lane's `RESUMED` log line
and the handoff's Rule 3 stamp all name **that** uuid, never the id the row
would otherwise have resumed. It binds or it refuses, exit **2**, before
anything is written: it is given only with `--no-launch`, never with `--fresh`
or `--agent`, its `<uuid>` must be a transcript uuid and the session live in
this window, and a window still named for another lane is refused — veto 2
stands. No command printed for a person to type carries it.

The env column records the Claude **profile** (`team-05b`), which is the thing
that actually varies between lanes on one workstation; `WSL2` in the older
rows is the same column.

### `lane-end <lane>`

Sets the row's state cell to `ENDED · <UTC> · lane-end on <workstation>:
window closing; NOTHING IN FLIGHT` — `RETIRED` under `--retire`, which since
Amendment 13(a) is the same write and not a second one — and **refuses (exit 2)
while the row says something is still open**: if the last `LANDING` or
`CLAIMED` in the state cell has no `LANDED`
or `RELEASED` after it, the lane still owns something and Rule 6 is holding
other lanes' merges. It prints the offending excerpt and the two ways out —
post the `LANDED`/`RELEASED` first, or `--force`, which ends the lane and says
in the register that it did.

The phrases that *say* nothing is open — `NOTHING CLAIMED`, `no LANDING open`,
`LANDING WITHDRAWN`, `UNCLAIMED` and their kin — are removed
case-insensitively before that scan. Without that, a substring match on
`CLAIMED` inside `NOTHING CLAIMED` would refuse on every healthy lane in the
register.

`--retire` is **the same single write**, carrying `RETIRED` instead of `ENDED`
— since Amendment 13(a) the state cell is one phrase that `set-row-state`
REPLACES, so there is no second act, no word to find inside the cell and no
history in it to preserve. `--state-was "<text>"` named the exact text the old
two-step had to replace; it is **accepted and ignored**, and a re-run that
passes it does the same thing a re-run without it does.

**It refuses (exit 2) while anything of the lane's is still on disk** — the
close-out gate of opensoft/openRepoTools#163, asked after the in-flight guard
and before any write, `--dry-run` included. The question is #162's sweep as a
dry run, `lane-worktrees sweep <lane> --branches --include-scratch
--include-caches --dry-run --porcelain` ([its exit
contract](#the-exit-contract-lane-ends-gate-163)): **3** (something to retire)
and **2** (the sweep refused the lane, or a read failed) are a refusal that
names every tree, branch, scratch directory and cache the table gives the lane,
relays the sweep's own reason where it refused, and prints the table's command
and the `--yes` that would retire them — which removes only what is on origin
or in a bundle, never a live writer's tree. **Any other exit, or
no `lane-worktrees` beside the command, is a gate nobody read: exit 1, never a
pass.** Beside the table the gate makes two reads of its own. Every tree of the
lane's is read with `git status --porcelain --ignored`, so a tree holding only
an `.env` or a `node_modules` is named as holding them and never as clean. And
every entry directly under the lane's own root, `.lane-worktrees/<lane>/`, that
no tree, scratch or cache row names is **residue** — the ignored leftovers no
row of the sweep reaches — and it refuses even where the sweep answered 0, as
a cache row does; a root that cannot be listed is the gate unread (exit 1,
whatever the sweep answered), never an empty one. Other lanes' trees in a shared checkout are FOREIGN, are
counted for nobody, and never hold this lane — but a FOREIGN row names nothing
for the residue read either, so a standalone clone directly under the lane's own
root, which the sweep keeps FOREIGN, is residue and holds it.

`--inventory-only` is **the one door past the gate**: the lane ends although the
gate found something, refused, or could not be read, nothing on disk is touched,
and the trees stay in the lane's #97 inventory, where the daily report lists them
as an ended lane's. The reason it was let past is **written down**: the row's
closing phrase gains `ENDED WITH --inventory-only: <what the gate found>; they stay
in its #97 inventory`, and the lane's own `ENDED` (or `RETIRED`) line carries the
same words in full. It does not pass the in-flight guard — that is `--force`'s,
and a lane with both wants both. **`--sweep` is not built**: retiring the trees
in the same run as the ending is not one act, so the word is refused by name
rather than read as an unknown option; `lane-worktrees sweep <lane> --yes`
retires them first, on its own.

`lane-end --retire-dormant <repo>` is a **different act again** and ends no
lane: it is Amendment 19(c)'s sweep of the rows that have no lane left to end.
It is described under [A closed or dormant lane leaves the
listing](#a-closed-or-dormant-lane-leaves-the-listing-amendment-19) below.

It **never renames the window.** The name is the lane's history until the next
`lane-start` takes that window, and a window renamed back to `claude` is a
window nobody can attribute.

One argument is taken verbatim, so a lane named before the `<repo>-<n>` rule
(`browser-ui-repair`, `hermes-wallet-exercise`) can be ended by its own name.

### The tests

```sh
# from a checkout of opensoft/openRepoTools, where the suite lives now:
bash tests/test_lane_helpers.sh     # the shell suite alone, touches nothing real
tests/run.sh                        # the same suite under pytest, SERIALIZED
```

`tests/run.sh` is the way to run it wherever lanes share a workstation
(opensoft/openRepoTools#51): it waits for any live run, takes
`${TMPDIR:-/tmp}/openrepotools-pytest.lock` — `flock` where there is one, a
`mkdir` lock on macOS, which has none — and only then runs `python3 -m pytest
tests -q`. Four suites ran at once on Eagle on 2026-09-14 past a guard written
`pgrep -af '…' | grep -v pgrep >/dev/null`, which never waits: `grep` there is
a shell function whose status is 1 when its stdout is `/dev/null`. The guard
that does wait is anchored, split so it cannot match its own command line, and
followed by the lock:

```sh
pat='^python3 -m pyt'"est"
while [ "$(pgrep -f "$pat" | awk 'END { print NR + 0 }')" -gt 0 ]; do sleep 20; done
PYTHONPYCACHEPREFIX="${XDG_CACHE_HOME:-$HOME/.cache}/openRepoTools/pycache" flock "${TMPDIR:-/tmp}/openrepotools-pytest.lock" python3 -m pytest tests -q -p no:cacheprovider
```

`awk` and not `pgrep -fc`: `-c` is not in every `pgrep` this toolset runs
under, and it is the COUNT and never `pgrep`'s exit status that decides.
`tests/run.sh` is the canonical implementation of this guard — the lines above
are it in one place, for a person with no checkout in front of them.

The suite copies the lane commands and the shipped alias table into a sandbox
BIN DIRECTORY, seeds a workspace repository with nothing but data in it, and
writes that sandbox's own `workspace.yaml` to join the two — which is the
world after Amendment 9, exactly. It unsets every `LANES_*` seam first, so
what every case exercises is the DEFAULT resolution and not an override.

A temp `HOME`, a bare repo and a clone seeded with a header and twenty-seven
rows, a fake `tmux` that logs its renames and a fake `claude` that logs its
argv. Amendment 7 adds a SECOND clone — the other workstation — so the race a
claim can lose is run for real rather than described, and `project.yaml` /
`family.yaml` fixtures for the leg detection. **No test may reach GitHub, and
what guarantees that is `LANES_NO_GITHUB=1`**, exported over every case,
plus `--no-github` on each: with it set, `gh_reads`, `gh_comment` and
`gh_stale_claim_url` all return before any `gh` or `git ls-remote` call. The
fake bin is *prepended* to `PATH`, not substituted for it, so a real `gh` on
this workstation is still reachable and is not what stops the call. The last
two assertions check that the real register and the real `lanes/log/` of the
clone the suite was run from are untouched — the log check compares the
directory listing taken before the first case with the one after the last,
because `lanes/log/` carries real lanes' records now and *empty* was never the
property being asserted.
`lanes-edit.sh` runs **for real** against the sandbox register, so the commit,
pull and push path is covered rather than stubbed, and liveness is a real
`sleep` process and a reaped pid rather than a mocked `/proc`.

## The object log (Amendment 7)

**DRAFT, 2026-09-11, awaiting Brett Heap's word.** The register says where a
lane *is*. It does not say what a lane *holds*: Rule 1 claims live in GitHub
comments, Rule 6 landings live in `LANES.md` as free text, and "does anyone own
this issue" has no answer short of reading 607 appended lines and 44 handoffs.
This is the tooling half of that answer.

### Layout

| what | where |
|---|---|
| a lane's object log | `lanes/log/<lane>.md` — one file per lane, append-only. `<lane>` is the REGISTER ROW'S spelling (Amendment 15); a file left under another case is found by every reader and RENAMED to that spelling once, by the first write that touches it, inside that write's own commit — so git records the rename beside the line appended. Two plain `mv`s through a temporary name, because on a case-insensitive filesystem `mv a A` answers *identical* and renames nothing |
| line 1 of each | `# lane <lane> — object log (lane-collision-protocol Amendment 7)` |
| every other line | one EVENT, in the grammar below |
| the alias table | two layers (Amendment 9(b)): the shipped `repos.tsv` installed beside the commands, then `<the checkout>/lanes/repos.tsv` where you keep an override. `alias<TAB>owner/repo`, case-insensitive. An override row replaces the shipped row for the same alias and adds rows it does not carry; an alias in neither is still a refusal saying to spell it `owner/repo` |
| the only writer | `lanes-edit.sh` (`log`, `claim`, `release`), as for `LANES.md` |
| the reader | `lanes-edit.sh who` |

One file per lane is what makes this safe from two workstations at once: two
lanes writing at the same moment touch two different files, so what a shared
file would turn into a conflict is a fast-forward here. **There is no index.**
A read fetches and greps; 45 lanes' logs together are smaller than one of the
register's rows.

**Every state read is of `origin/main`, after a `git fetch` — never of the
working tree.** What has LANDED is what other lanes can see, and a working
tree can be behind by a peer's whole day: `who`, `claim`'s pre-check, the
staleness test, the superseded filter, the crossing warning, `lane-end`'s
refusal and `lane-start`'s home lookup all read the same source, so they
cannot disagree with each other either. `LANES_NO_FETCH=1` (and `who
--no-fetch`) skips the fetch for an offline lane and for the tests; it does
not change what is read, only how current the ref is.

**On `mode=primary` that read is the database's,** at one head, and
`lanes/log/<lane>.md` on `origin/main` is the mirror, which lags: `who`,
`claim`'s pre-check, `lane-end`'s refusal and the rest decide from the register.
A `claim` there prints `register seq <n>` where it printed a commit sha, and the
later of two claimants exits 2, not 7. See "The SQL register".

`LANES.md` is unchanged. Its rows are still its rows, and its Rule 6 `LANDING`
/ `LANDED` lines still land in it, because every lane already reads them there
as the merge hold — which is why `who --landing` reads *those* and not the
logs. A `LANDING` or a `LANDED` therefore writes **both files in one commit,
with two pathspecs** (on `mode=primary`, both in one register transaction, one
journal entry): there is no moment at which only one of them has been written.

### The grammar

```text
<VERB> — lane <lane>, session <transcript-uuid>@<workstation>, <UTC>, <object>[ → <payload> | ← <payload>][ — <free text>]
```

The verb is **one token**. The first four fields are separated by `, `. The
**object token contains no space and no comma**, so the payload begins
unambiguously at the first space after it; payload sub-fields are separated by
`; ` and several refs are space-separated.

| kind | verbs |
|---|---|
| issue | `CLAIMED`, `RELEASED`, `TAKEOVER ← <stale claim URL>`, `CLOSED → owner/repo#pr` |
| PR | `OPENED ← owner/repo#i [owner/repo#j …]`, `LANDING`, `LANDED → <sha>`, `WITHDRAWN`, `CLOSED` |
| issue or PR | `TAKEOVER ← lane:<dispossessed lane>` |
| lane | `STARTED`, `PAUSED`, `RESUMED`, `ENDED`, `RETIRED` |
| narrative | `NOTED`, `RULED [→ <object>]` (Amendment 13(b)) |
| race | `CLAIM-LOST → lane:<other>` |

There is no `MERGED`: Rule 6's `LANDED → <sha>` is already the post-merge line.

**Every line has an object**, lane-kind lines included — theirs is the lane:

```text
STARTED — lane openRepoProject-1, session 09dd34d1-3afe-43a1-88fc-c33c92c08088@Eagle, 2026-09-11T19:08:12Z, lane:openRepoProject-1 → home opensoft/openRepoProject; estate openRepoProject
```

`PAUSED`, `RESUMED`, `ENDED` and `RETIRED` carry the same `lane:<name>` object
and no payload. So do Amendment 13(b)'s `NOTED` and `RULED` — and `RULED` takes
one optional payload, the object the ruling bears on: `RULED — lane <lane>,
session <uuid>@<ws>, <UTC>, lane:<lane> → opensoft/openRepoTools#29 — <the
words, verbatim>`. **Neither is a transition**, so neither ever changes a lane's
state on any object or on itself: `who`, `lane-end`, `lanes` and `swapped` skip
them exactly as they skip `STARTED` and `RESUMED`.

**The `<lane>` field and the `lane:<name>` object are matched
case-insensitively, and a writer writes the register row's spelling into both**
(Amendment 15). A log is append-only and its lines carry whatever was typed on
the day it was written: `lanes/log/openxfactory-2.md` holds `lane
openXfactory-2` lines from 2026-09-02 beside the `lane openxfactory-2` lines of
the 23:51Z incident, in one file, about one lane. `who --lane`, `lane-end`'s
refusal and the `lanes` listing read both as that lane's; the old lines are
never rewritten.

**The object kinds in scope** are an issue or PR (`owner/repo#<n>`), an
OpenSpec change directory (`owner/repo:openspec/changes/<name>`), and — for
the lane-kind lines only — `lane:<name>`. `#<n>` is shorthand for the lane's
**own** home repo; a foreign repo is always spelled, so that crossing is
explicit at the moment it is typed. Human acts, realization groups, contract
cuts, file surfaces and Amendment 1's substrates stay where they are today, on
their governing records, and are an explicit non-goal; a later amendment may
add keys.

The alias table resolves an omitted owner and the register's legacy
spellings — all four spellings of `codexFactory` resolve to
`codeXfactory/codexFactory`, which is the redirect GitHub itself answers with —
and an alias that is **not** in the table is refused, with the hint to spell
`owner/repo`. Guessing is how a fifth spelling of one repository gets created,
so spellings that could not be resolved (`oxF`, `xF`, `OMI`, `OWI`, and the
bare `InkRouter` and `IRSS`, which are ambiguous between two orgs) are left out
deliberately rather than guessed at.

### State is per lane

**A lane's state on an object is that lane's own LAST line naming it.** The
lane HOLDS the object while that verb is open; the object is HELD when any lane
holds it.

- **open** — `CLAIMED`, `TAKEOVER`, `OPENED`, `LANDING`, `WITHDRAWN`. A
  withdrawn landing leaves the PR held by the lane that withdrew it.
- **closed** — `RELEASED`, `CLAIM-LOST`, `CLOSED`, `LANDED`. **A closing verb
  closes only the writing lane's hold**, never another lane's.

That last sentence has a consequence worth knowing before it bites: after
another lane's `TAKEOVER`, the dispossessed lane's own last line is still its
`CLAIMED`, so `who` marks it `superseded by TAKEOVER (lane:<X>)` rather than
counting it as a holder, and `lane-end` — which would otherwise refuse for
ever — names the one act that frees it:

```text
CLAIMED opensoft/repoG#8 (…) — taken over by lane:repoG-2 at 2026-09-11T20:00:51Z;
  run: lanes-edit.sh release opensoft/repoG#8 "taken over by repoG-2"
```

**A `TAKEOVER` supersedes only while it is the taker's own last line on the
object.** Per-lane last-line semantics apply to the taker too: once lane B
writes `RELEASED`, `CLOSED` or `LANDED` on the object, B's `TAKEOVER` supersedes
nothing, and a later `CLAIMED` by the lane it dispossessed holds normally. A
`TAKEOVER` is never removed from an append-only log, so treating one as
superseding *for ever* made a legitimate re-claim invisible: `who` called a held
object FREE, `claim` did not refuse the next lane — the one outcome Rule 1
exists to prevent — and `who --lane` and `lane-end` disagreed about the same
object. No clock and no cross-file order is needed to ask this: both halves are
"what is this lane's last line", read off two append-only files.

**A chain of takeovers resolves to its last link** (opensoft/openRepoTools#74).
The dead-lane exception below lets a `TAKEOVER` displace an earlier `TAKEOVER`
— `A` claims; `B` takes over from `A`; `C` takes over from `B` once `B` is
dead — and every link that displaces a `TAKEOVER` names the lane it displaced
as `TAKEOVER ← lane:<x>` (a stale claim's comment URL is only ever the payload
for a `CLAIMED` taken over). So a lane whose own last line is a `TAKEOVER` is
displaced only by another lane's standing `TAKEOVER` that **names it**: `B` by
`C`, and `C` by nothing, so `C` alone holds the object. Any other open verb is
displaced by any standing `TAKEOVER`, as above, and the taker named is one no
other standing `TAKEOVER` names: `A`'s line reads `superseded by TAKEOVER
(lane:C)`. Read one hop at a time — "does some other lane's last line here
read `TAKEOVER`" — `B`'s own stale `TAKEOVER` was proof that `C` was displaced
too: `who` reported **zero** holders, `who --lane C` told `C` to release an
object it held, and `claim` granted the next lane a plain `CLAIMED` over it.

*Known, deliberate.* Two lanes that each end on a `TAKEOVER` of one object,
**neither naming the other** (two takers of one stale claim), are **both**
reported as holders; so are two whose `TAKEOVER`s name *each other*, which no
file order can put in order. That is a visible conflict rather than a silent
one, and it is not reachable through the helpers in the ordinary case — a live
lane's `claim --force` takes over a stale `CLAIMED` and nothing else (or, per
the dead-lane exception below, a **dead** lane's hold of any verb, an earlier
`TAKEOVER` included), and refuses outright when more than one lane holds the
object.

**State is read in FILE ORDER, never by comparing timestamps.** A lane's log is
append-only and single-writer, so its line order *is* that lane's write order,
and "its last line naming the object" means the last such line **in the file** —
a `LANDING` and its `LANDED` are often written in the same second, and on a
workstation whose clock steps backwards the second of them is stamped earlier.
The UTC stays on every line as information and is compared only where a
**duration** is the rule — Rule 1's four hours and Rule 6's thirty minutes —
because no two workstations share a clock (this one was observed jumping ±25s
on 2026-09-11, and a log commit landed carrying a UTC 26s after its own
committer date), and which lane got somewhere first is landing order on `main`
rather than a stamp.

**No line is ever dropped silently.** A line the parser cannot read is
reported as `unreadable: lanes/log/<lane>.md:<n> (<why>)` and counted, because
in an append-only log that nothing rewrites, a dropped line is a hold no tool
will mention again — `who` would call the object free, and it is not. A UTC
without seconds (`2026-09-10T20:31Z`) is read, not dropped: the register has
been writing that form since before this helper existed.

### `log` — the generic append

```sh
LANES_LANE=openRepoProject-1 lanes-edit.sh log OPENED opensoft/brett-wip#14 ← brettheap/new-workstation#11
```

Validates the verb and the object key, appends one line, and commits it as
`LOG(<lane>@<workstation>): <VERB> <object>`. A lane verb takes a `lane:<name>`
object and an object verb refuses one.

It **refuses the four verbs that have a guarded subcommand of their own** —
`CLAIMED` and `TAKEOVER` (use `claim`, `claim --force`), `RELEASED` (use
`release`), and `CLAIM-LOST`, which only a lost race writes — and names the
subcommand in the refusal. `log CLAIMED` would otherwise skip the pre-check,
the race and the rescan while `who` reported the result identically to a real
claim, and `log TAKEOVER` would skip the staleness test that is the only thing
making a takeover legitimate. `OPENED`, `LANDING`, `LANDED`, `WITHDRAWN`,
`CLOSED` and the lane verbs are exactly what `log` is for. `--text "<t>"` sets the free text when
there is no payload to put it after. The session id is `$LANES_SESSION`, else
the **last** transcript uuid in the lane's row — the lane's current session
under Amendment 6(b).

### `claim` — Rule 1 as one act

```sh
LANES_LANE=openRepoProject-1 lanes-edit.sh claim brettheap/new-workstation#11
```

**The race is decided by which `CLAIMED` LANDS on `main` first — never by
comparing timestamps.** Two workstations' clocks are not a shared order; git's
push serialization is, and the `mkdir` mutex in this helper only serializes one
workstation. So:

1. `git fetch origin`, and pre-check against **`origin/main`** — what has
   landed is what other lanes can see, and a working tree can be anything.
2. If another lane holds it, print `who` and **exit 2**: Rule 1 says the lane
   stops and reports, it does not author a successor. Nothing is written.
3. Run Rule 1's three reads and print them; warn if this is a crossing.
4. Append the `CLAIMED` line, commit, push (Amendment 23 S3–S4: fetched and
   fast-forwarded first, no pull). If S3's fetch finds origin moved since
   steps 1–3 read it, nothing is written and the claim runs again from the
   top with the lock held (within the six attempts; 2 at the sixth), and its
   rescan comes first, as below; the re-run makes no GitHub read, and prints
   and cites the three reads attempt 1 made. A push refused because origin moved drops
   the `CLAIMED` commit and runs the claim again on origin's new tip, and the
   re-run rescans first: if another lane's `CLAIMED` or `TAKEOVER` is there,
   **theirs landed first** — it appends `CLAIM-LOST → lane:<winner>`, pushes
   that line alone, and **exits 7**. The claim is *abandoned*, not queued;
   Rule 7's queueing is for substrates, which this amendment does not touch.
5. Post the GitHub `CLAIMED —` comment **after** the push, citing the commit
   that carries the line. The line is never rewritten to cite the comment.

A claim used to need a checkout it could rebase, and refused one with any
other tracked file dirty. Amendment 23 S3 narrows that (ratified choice 8): no
writer pulls, so a peer's unstaged change elsewhere refuses a `claim`, `log` or
`release` only where the fast-forward would overwrite it. A change to a path
the write commits, or a pending hand edit of `lanes/LANES.md`, still refuses
(2), with nothing written: the `LANES(pre-existing@<workstation>)` capture
these three took for the register is withdrawn (ratified choice 9), and
`commit` is the one act that commits such an edit.

**Reads 2 and 3 name the object, not its digits** (Amendment 8). They searched
for the bare slug as a *substring*, and a number is a substring of almost
everything: claiming `brettheap/new-workstation#15` printed somebody else's
PR #10 — GitHub's own search had matched `15` in its text — and a 40-character
commit sha out of `ls-remote`, both under the heading *existing work on this
object*. Evidence that is not evidence is how a lane talks itself out of a claim
it should make, or into one it should not. **Two reads, two tests**, because the two
inputs are not the same kind of text. `gh pr list`'s rows are **prose**, so a
reference is `#<n>` — the hash required, its left side unbounded, because the
canonical spelling is `owner/repo#15` and the character before the `#` is a
letter — or the row's own number column, which is the PR the object *is*.
`ls-remote`'s refs are **names**: `issue-15-fix` carries no hash, so there the
test is the bare number as a whole token, after the leading sha has been
stripped, so a branch is matched by its name and never by the digits of the
commit it points at. An OpenSpec change directory has a word for a slug and
takes the whole-token test in both.

**Read 2 filters over the body.** `gh pr list`'s columns are number, title,
branch and state; the reference that makes a PR a sibling is almost always in
its **body**, which is what GitHub's own search matched and what the columns do
not carry. Filtering the columns alone dropped the real PR for this very
issue — whose body says `brettheap/new-workstation#15` twice — while a filter
with the hash optional kept the wrong one instead. So the body is fetched,
flattened to one line, used for the test, and cut off again before anything is
printed: under-reporting a sibling is the direction that lets a lane claim what
somebody else is already working on, and it is the one direction Rule 1 cannot
afford.

The filter is the `sibling-filter <object> [--branch]` subcommand, which both
reads pipe through and which can be held to account on its own:

```console
$ printf '10\tbump the pin to 1150\n15\tthe amendment 8 work\n' | lanes-edit.sh sibling-filter opensoft/repoE#15
15	the amendment 8 work
```

`--no-github` skips every `gh` call, for the tests and for an offline lane. A
line written that way is **not yet a Rule 1 claim**, because the comment a
person outside this estate reads does not exist, so it carries the free text
`no-github` and `who` prints it as `local only — no GitHub claim comment`.
Otherwise **the log is authoritative for a claim made through `claim`** — a log
line *is* a live claim for Rule 1 purposes — and the GitHub comment is the copy
another person reads.

**Stale**, made checkable from Rule 1's own words: a lane's `CLAIMED` on an
**issue** is stale when it is older than four hours *and* that lane has written
no later `OPENED` whose `←` payload names the issue. A PR is never stale by
this rule — Rule 6 governs PRs, with its own thirty minutes. An object is taken
to be a PR once some lane has written `OPENED` on it, which is the only
offline evidence there is and exactly the evidence Rule 1 cares about.
`--force` takes over a stale claim — or, per the dead-lane exception just
below, a dead lane's hold of any verb — and nothing else, writing one of TWO
payload forms for either reason (Copilot round 9, PR #61: this sentence
named only one). `TAKEOVER ← <the stale claim's comment URL>` when the hold
taken over was itself a `CLAIMED` posted as a GitHub comment and that
comment can still be found; `TAKEOVER ← lane:<the dispossessed lane>`
whenever it cannot — always true for a dead lane's own `OPENED`, `LANDING`,
`WITHDRAWN` or earlier `TAKEOVER` (none of those was ever a `CLAIMED`
comment to find), and also true under `--no-github` or a search that simply
comes up empty. `TAKEOVER` is itself an open verb, so no second line is
needed either way to say the taker holds it.

**Or the holder's LANE is dead, whatever the verb** (opensoft/openRepoTools#30).
Staleness answers Rule 1's own question about one claim; it says nothing about
a lane that went silent and was retired, leaving an `OPENED` PR or a fresh
`CLAIMED` issue behind that neither gate above will ever call takeable — the
verb-check refuses the PR outright, and a `CLAIMED` lane that later `OPENED` a
PR naming it is deliberately never stale, which is exactly backwards once the
lane itself is gone. `--force` also takes over a hold of ANY open verb when the
holder's own object log ends its lane-kind lines (`STARTED`/`PAUSED`/
`RESUMED`/`ENDED`/`RETIRED`) on `ENDED` or `RETIRED` — a swap (`PAUSED`) is
deliberately not dead — **and** no live session for it is found on this
workstation (`live_holder`, the one liveness implementation this file has,
never a second one): a `RETIRED` register line is not proof by itself that
nothing is still running under that name (opensoft/openRepoTools#39), so the
register's verdict is checked before it is trusted. **That check is made only
from inside the lane's last binding** (Amendment 18(b)): the `host` and
`container` of its last `STARTED`/`RESUMED` before the terminal line must be
this place's — a line from before Amendment 18(a) is matched on its own
workstation — or the binding's window must be gone from a tmux server this
host shares with it. A lane last bound anywhere else is UNKNOWN, never dead,
and `--force` refuses it, naming that binding. The session ids that check asks
about are the row's, published and this checkout's own, so a register that
could not be read is refused as well: it never confirms a lane dead
(opensoft/openRepoTools#67). Before this the only path
was a `release` of the dead lane's held objects by hand, one at a time, run
under its own name on the taker's word.

**A crossing warns; it never refuses** (Brett Heap, 2026-09-11: "yes just
warn"). Crossing is routine — `opsXfactory-3` landed **32 distinct PRs** into
`opensoft/Omnigent-Install`, every one of them foreign to its home, and
`openXfactory-3` landed 23 foreign of 31 — so a refusal here would be a new
gate nobody asked for. `claim` names every lane homed on the target repo that
is live (or on another workstation), with its address and last transcript uuid:

```text
WARNING: CROSS-REPO — opensoft/workBenches is not lane openRepoProject-1's home (opensoft/openRepoProject).
  LIVE lane homed on opensoft/workBenches: @workBenches-2 — last transcript uuid 4f2c8e10-...
```

Repos that are **legs of one `project.yaml`** are not a crossing: the manifest
is looked for one and two levels under `~/projects`, and only
`legs[].repository` is read — the manifest says of itself that it confers
nothing, and `role:` is never consulted. A `family.yaml`'s `members:` is
deliberately *not* read: a family is broader than a project, and two of its
members are two projects with two lanes.

### `release`

```sh
LANES_LANE=openRepoProject-1 lanes-edit.sh release brettheap/new-workstation#11 "superseded by #12"
```

A `RELEASED` line and the matching GitHub comment (`--no-github` skips it). It
never refuses: releasing something this lane does not hold is a note, not an
error, and it is also the act that closes a dispossessed lane's own line after
someone else's takeover.

### `who` — the read

```console
$ lanes-edit.sh who brettheap/new-workstation#11
object   brettheap/new-workstation#11
HOLDS    lane openRepoProject-1        CLAIMED   2026-09-11T19:08:12Z (2h 14m ago)
state    HELD
holder   lane openRepoProject-1 (CLAIMED, 2026-09-11T19:08:12Z)
  stale    no — 2h 14m old, threshold 4h
  home     opensoft/openRepoProject
  holder   lane openRepoProject-1 — LIVE (session 09dd34d1-3afe-43a1-88fc-c33c92c08088, pid 779114, tmux claude-team-05f-20260911150206-778763:@460.%460)
  address  @openRepoProject-1 · claude --resume 09dd34d1-3afe-43a1-88fc-c33c92c08088
  handoff  handoffs/openRepoProject/session-handoff-2026-09-11-lane-openRepoProject-1.md
  log      lanes/log/openRepoProject-1.md
```

```sh
lanes-edit.sh who --lane openRepoProject-1     # what this lane holds open
lanes-edit.sh who --landing opensoft/brett-wip # the Rule 6 merge hold on a repo
```

`who --landing` reads **LANES.md's own Rule 6 lines**, not the logs, because
those are written for every lane, before this amendment and after it. Landings
are counted as distinct `(lane, repository, PR)` **triples**, never as raw
`LANDING` lines: a retry re-posts `LANDING` for the same PR, and the register
holds 571 such lines for far fewer landings. The repository is part of the key
because one lane landing into several repositories is this estate's norm —
keyed on `(lane, PR)` alone, a lane's `LANDED` on one repository closed its own
still-open `LANDING` on another whenever the two PR numbers matched, and the
merge hold went invisible. The repository is lower-cased in the key only, so a
`LANDED` spelled `OpsxFactory` still closes a `LANDING` spelled `opsXfactory`.
Both sides are resolved through `repos.tsv` before they are compared, so a
`LANDED` into `opensoft/OpsxFactory` closes a `LANDING` into `OpsxFactory` —
one repository the register writes both ways, 70 Rule 6 lines against 23 at
`8f9c04d` — the triple's repository is the canonical `owner/repo`, and an
alias the table does not know keeps its own spelling.

**A `LANDED` closes the `LANDING` it answers, paired by `(lane, PR)` in file
order.** Every `LANDING` in the register carries `into <repo> main`; most
`LANDED` lines do not — 300 of 329 on 2026-09-12 — so keying a `LANDED` on the
repository *it* names keyed those 300 on the empty string, where they could
never close the repository-qualified `LANDING` they belonged to. Measured over
the real register at `ee0e07c`, that reported **232 open merge holds** where
the genuinely open count was **one**: on cutover day `who --landing` would have
told the first lane to ask that essentially every repository it wanted to land
into was held. So the *pairing* is by `(lane, PR)` — a `LANDED` closes the most
recent still-unmatched `LANDING` of that lane and PR, and the pair's repository
is the **LANDING's** — while the *result* still keys on the triple, which is
what keeps a `LANDED` on one repository from closing a `LANDING` on another.

A `LANDED` that *does* name a repository must name that `LANDING`'s. If it does
not, it closes nothing and is printed against the repository it names as

```text
unpaired LANDED  opensoft/repoOTHER#32  lane repoT-1, … — the open LANDING for
  (lane repoT-1, PR #32) is on opensoft/repoT, so this closes nothing
```

rather than being dropped: an append-only register is never rewritten, so a
line no tool mentions again is a line nobody will reconcile. One consequence is
worth knowing: when a lane has two `LANDING`s open on one PR number in two
repositories and lands the **earlier** one while naming its repository, the
`LANDED` reads as unpaired and both holds stay open until the second lands.
Two holds reported where one is real is the safe direction, and the shape is
rare; a `LANDED` naming a repository at all is one line in eleven.

A `LANDING` past **Rule 6's thirty minutes** with no `LANDED` is marked
`STALE (Rule 6: >30 min)` and still listed — the line is reported, the reader
is told it is past the window. After the register's holds, the lane logs are
reported under `from lane logs (secondary):`: they are evidence beside the
register, never instead of it.

**An empty answer is an answer.** `who --lane <lane that holds nothing>` and
`who --landing <repo with no hold>` print `none open …` and exit **0**; 8 is
reserved for genuine absence — a lane with no log file, or an object no line
anywhere names. A script that gates on `if who --landing <repo>` must be able
to read the healthy case as success.

`--home owner/repo` supplies the home repo for a lane whose log has no
`STARTED` line yet, which is what `#<n>` needs to expand. It is an option of
`claim`, `release`, `who`, `log` and `lane-end` — **five**. It is resolved through `repos.tsv` and
then **validated** against `owner/repo`, and it is **refused (exit 2) where the
lane's own `STARTED` line already answers the question**, so the log and the
flag can never disagree. All five resolve it **after the fetch**, because the
`STARTED` line that refuses it may be a peer's and live only on `origin/main`.
Unvalidated it was a hole straight through the alias discipline: `--home 'not
a repo'` wrote `not a repo#42` into the log — a line the log's own parser
cannot read back, in a file nothing ever rewrites.
`lane-start` checks its own answer the same way, so a checkout whose `origin`
is a path or a mirror records `home unknown` rather than something every `#n`
in that lane would then inherit.

### Liveness

**One implementation**, in `lanes-edit.sh`, which `lane-start` asks for through
its internal `live-holder` subcommand. Two copies would drift, and this is the
check that decides whether a lane name is free.

Amendment 8 adds a second read over the same records, keyed on the **window**
rather than on a lane: `window-session <tmux session>:<window id>` prints the
live record whose own `tmux` field names that window —
`<uuid> <tmux> <name> <pid> <profile>`, `\037`-separated, **0** with the
record, **8** when the records were read and none is in that window, **1** when
they could not be read at all. Where several live records name one window — a
session that outlives the window it started in keeps the `tmux` value it was
born with — the most recently updated one wins, so the answer does not depend on
which the filesystem listed first. The three live tests are shared with
`live-holder`; the two that are about a *lane* — the row's ids and the
`nameSource` rule — are not applied, because a window holds one interactive
session whatever it is called, and the record's name is reported rather than
believed. The profile is read off the record's own path, never from the
environment of whoever is asking.

A lane is LIVE when a session record **on this workstation** carries one of the
row's transcript uuids, its `status` is not one of `ended`, `exited`, `dead`,
`stopped`, its pid is alive, and `/proc/<pid>/stat` field 22 still equals the
`procStart` the record wrote down. Every uuid in the row's session cell is
tried, not only the last: a lane whose earlier lineage is still running is
still held.

A record's `name` is evidence about *another* lane only when a person or a
`--name` set it. **The rule is the SET: EXPLICIT is `{user, peer}`, and
everything else — `derived`, `auto`, any other value, and no field at all — is
MACHINE**; a record is skipped for a differing name only when its `nameSource`
is EXPLICIT. The census is evidence for the set, not the rule, and it moves:
measured across this workstation's records at 2026-09-11T21:04Z, `derived`
273, `user` 31, `peer` 9, `auto` **0**, and 26 records carrying no
`nameSource` at all — which is why "no field" has to be named in the rule
rather than left to a census that happens to contain a value today.

This is a fix, not a preference. On 2026-09-11 this orchestrator's own record
read `name: openrepoproject-63, nameSource: derived` while it held lane
`openRepoProject-1` — pid alive, `procStart` matching, status `busy` — and the
old filter skipped it on the name alone, so `lane-start --dry-run` reported *no
live session holds openRepoProject-1* and a second window would not have been
refused the lane. After the operator ran `/rename openRepoProject-1` the same
record read `openRepoProject-1` / `user`, and the check reported the holder.
Records are enumerated with `find … -path '*/sessions/*.json'`, never a
one-level glob: team profiles live two levels down.

A holder recorded on **another workstation** reads
`UNKNOWN (records are local to <ws>; ask @<lane> or read its handoff)`, never
`NOT LIVE` — session records are local files, and this machine has no way to
make that claim about another.

**And so does a read this workstation could not perform.** `live-holder` has
three answers and never two of them conflated: **0** with the holder, **8** for
"the records were read and nothing live holds this lane", and any other non-zero
for "I could not read them" — an unreadable tree, an unreadable record, a
permissions or IO fault. `lane-start` renames a window on 8, so mapping a failed
read to 8 as well is exactly how a running lane loses its address: the rename
mints `<lane> (2)`. `who` prints that third answer as
`UNKNOWN (this workstation's session records could not be read…)`, for the same
reason it never says `NOT LIVE` about another machine.

**Every row `who` reads comes from `origin/main`, like the logs.** The session
cell, the handoff path and the workstation column are read with
`git show origin/main:lanes/LANES.md` after the fetch — nothing in `who` reads
the working tree. A row that existed only on `origin/main` used to lose the
resume uuid and the handoff path, which are the two things `who` exists to hand
over; and a peer's *uncommitted* edit to column 3 turned a lane on Raven into
`NOT LIVE`, which is the confident false report that sends a reader to
`claim --force` against a live lane.

**The one read that also consults this checkout is the `live-holder`
subcommand, and only its id SET** — the union of the published row's transcript
uuids with this checkout's row's. A session stamps its own uuid on the row with
`replace-in-row`, and that write is a local commit for as long as its push takes
(and longer, whenever a peer's file is blocking the rebase): read from
`origin/main` alone, the rename gate would not see the uuid of the session it is
being asked to rename over, would answer 8, and `lane-start` would take the name
— which is exactly what mints `<lane> (2)` and costs a running lane its address
(AGENTS.md rule 7). Liveness fails closed, so an id `origin/main` has not seen
yet is one more reason to REFUSE a rename and never a reason to allow one, and
nothing that PRINTS uses the union: `who` reads the published row and nothing
else.

### Exit codes

| code | meaning |
|---|---|
| 0 | done — and for a write, **confirmed**: the commit was read back on origin before the writer said `pushed` (Amendment 23 S4) |
| 1 | environment — no register, no writer; and, feature 007, on primary and with nothing sent, a write's own working files that cannot be made: a host with neither `sha256sum` nor `shasum` to make the write's digest, a `register-pending/` directory under the state directory (or a claim in it) that cannot be made or written whole, and an attempt's directory under `register-writes/`, or a file the parent writes there |
| 2 | refusal — bad arguments, the object is held, an unknown alias, or `commit` refusing to publish local commits that are not register or handoff writes, from a checkout whose HEAD is off the branch, or that it cannot read (#194; it pushes none of them); and, Amendment 23 S3, every writer with nothing written, where its checkout cannot be brought to origin's tip: a fetch that failed (`LANES_NO_FETCH=1` is one, for a writer), an operation in progress or unmerged entries, HEAD off the branch, staged entries that are not the write's own, unpushed commits (the publish act excepted), a diverged branch, a pending change to a path the write commits, or a fast-forward git refused; a `LANES_REPO` naming a directory below its checkout's top level, from which git would read the write's pathspecs, before any edit; the withdrawn `--sweep`; the publish act over a stack origin has moved past (S4); and `claim` or `add-row` meeting origin moved under its own checks on each of six attempts, before any edit (S3, S4); and `publish-handoff`'s restart fence on its bound file or pre-image (`write-handoff` answers those 7, so its 2 is never a restart fence's); and, feature 007, on primary, each with nothing written ("The SQL register"): a `LANES_WRITE_ID` that is neither a write id nor `new`, a `LANES_WRITE_GENERATION` that is not a register generation, a `LANES_WRITE_ID` with no generation that no pending record here holds, a pending record of another register generation (at any age, until `LANES_WRITE_ID=new`) or one that cannot be read, and an id the journal holds for another write; a lane whose own register checkout is not there, an unset or out-of-class `LANES_LANE`, `LANES_NO_GIT=1`, a hand edit of a rendered file pending in that checkout or a listing of it that git cannot make, and no author identity there (each of these 64 for `request-handoff`), and a second register write in one run; `commit` over a hand edit of the register or of any file it renders, and the publish act over a queued commit that changes a path under `lanes/` other than `lanes/repos.tsv` and `lanes/log/README.md`; the register's own refusals, relayed with its `ERROR:` and `DETAIL:` lines, which are an object another lane's live claim holds and `main` another lane holds first (each with a `held-by=` line where the register can be read again at once and shows the holder: the later `claim`, `HOLD` or `LANDING`, never 7), a claim whose spellings meet another lane's live claim, an act's expectation that changed (`expectation: <scope> changed at seq <k> by <lane>/<verb>`) and a key that is not a scope; `append-line`'s refusal of a hand-typed `LANDING EXPIRED`, of a take that names no repository or has no readable UTC, and of `--landing-expired` where its `LANDING` is not open or is not past Rule 6's 30 minutes; and `act-begin --rebase` and the checks on `LANES_ACT` and `LANES_EXPECT` |
| 3 | **pushed, not confirmed: `<sha>`** (Amendment 23 S4). The write committed and pushed — or its push timed out, and a hung push often lands — and the read-back could not confirm the commit on origin: the fetch failed or timed out, or origin does not hold it. Never success. The commit stays on HEAD — unless another writer in the checkout **rewrote** it while its push was in flight (an unlocked writer's rebase), which the words then say: it is not on HEAD at all, and what reached origin is read by content — and the next write's S3 finds it by content either way: published, there is nothing to do; not, every write refuses until the publish act sends it or an archive (S6.3) takes it. **On primary** (feature 007; "The SQL register"), a register write in doubt: may not be written; run the printed `LANES_WRITE_ID` rerun, which writes it once or finds it. "cannot confirm whether register write `<id>` committed; nothing else was written": a commit call of the write may have committed (its pending record holds a `call` line, this run's or one a record taken over carried in), and the lookup by write id, `lanes-index journal --write-id <id> …`, could not settle it: no try within its window took the head row's lock, the lookup was refused as too early (LR011), or its answer settles nothing (any other code, a crash). The message gives the lookup and the rerun, `LANES_WRITE_ID=<id> LANES_WRITE_GENERATION=<generation> LANES_LANE=<lane> LANES_SESSION=<the value the digest took> lanes-edit.sh <the same arguments>`, and the pending record is kept, so the rerun settles the write and never writes it twice. On primary, an identical write's owner still live at this write's deadline: this run sent nothing, that write's outcome is not known, and its record and claims are left as they are. With a pending record: "an identical write is still running in pid `<p>` (write `<id>`, started `<utc>`)", with its lookup where the record holds a `call` line, and its printed rerun. With none yet: "an identical write is being started in pid `<p>` on `<host>/<container>` (claimed `<utc>`)", with the effect check, `history <lane> --since <utc>` or `register-row <lane>`. And where that owner cannot be tested here (the claim names no pid or cannot be read, its pid lives with no start time, or it is from another host or container), "an identical write holds the claim `<state>/register-pending/<d>.take.<k>`, whose owner cannot be tested here", with the pid, host and container a claim from another host or container names, and the UTC after which a run may take it |
| 4 | the mutex could not be taken within 60s |
| 5 | an edit moved more than one line and was refused — or a register rewrite whose copy, write or result could not be proved (#120 item 1). The copy every proof is made against is compared with the register, and what lands with what was built; where a write cut the register short, its earlier bytes are put back and proved first, and the refusal says which of the two the register holds — never "unchanged" about a register the write truncated |
| 6 | `git add` or `commit` failed — or the commit is **kept on HEAD and named** (sha, subject, `git show`): a sixth push refused because origin moved (Amendment 23 S4's bound; where S3 finds the sixth move before any edit, nothing is committed and it is 2), a replay's drop refused over an uncommitted change, a commit another writer made on top of this one in the checkout before the replay drops it, or under it (this write's commit is known by what it is, never by HEAD read after the fact, and the drop moves the branch only from that commit and only where it sits on origin's history, so it never takes another; `commit`'s carry form drops to its own commit's parent by design, so one under it stays, the hand edit goes back to the working tree with its copy kept, and the re-run's S3 refuses on it: 2, not 6), a commit made in the same moment as this write's that cannot be told from it (nothing is pushed), a push (a write's, or the publish act's) refused with origin unmoved, which no replay can mend, or a whole-file `write-handoff` whose copy origin changed meanwhile, which no replay may overwrite (S4.4), or a `--stamp` or `--prepend` one whose handoff origin deleted meanwhile, which a replay would find nothing to write into (one moved with its row's cell, to a file under `handoffs/` that origin has, is replayed into the moved file), or an Amendment 8(d) handoff `write-handoff` wrote whose commit or push in its own repository failed (the command that finishes it is said) (that commit is in that repository; no write here refuses over it). Or another writer in the checkout **rewrote** this write's commit (a `pull --rebase` with no lock): it is not on HEAD at all, and what reached origin is read by content (`git cherry -v refs/remotes/origin/main HEAD`). Or, where the drop's tree step was refused and another writer committed on the branch in between, the commit is named off HEAD, with `reflog -5`. Every later write from that checkout refuses until the lane publishes or archives it |
| 7 | another act got there first and this one wrote nothing: `claim`'s `CLAIM-LOST` (another lane's claim landed first; **in files mode only**, since on primary the later claimant is the register's refusal, 2), and `set-lane-state` / `set-lane-tree`'s lifecycle fence (openRepoTools#91, below), and `publish-handoff` / `write-handoff`'s restart fence — `write-handoff` answers its bound file and pre-image 7 too, where `publish-handoff` keeps its 2 (Amendment 23, slice 2a) |
| 9 | `CLAIM-LOST` — issue #30's own dead-lane verdict could not be reconfirmed before a `--force` takeover's push landed: the source lane resumed, a live session now backs it up, or that could not be read at all (`claim` only, and never on primary, where the recheck is not made). Never 7 — that code is a RIVAL's claim, and this is the same lane the takeover was granted over |
| 10 | `lane-state`: the lane's lifecycle snapshot is there and could not be read, or is of this schema and not a whole record, so nothing can be read out of it as a snapshot (openRepoTools#91 and #115, below), or — where no `LANES_LANE_STATE_ROOT` names the control root — the lane's object log could not be read, so where its snapshot is kept is not known (openRepoTools#290, below) — never the 8 that means it has none. It was 9 until #61 spent 9 on `claim`. `lane-trees`: the lane's `trees` directory is there and could not be listed (#157 item 4e) — never the 8 that means it recorded no tree. Both spend it, too, where the record could not even be looked for: its control root, or a directory above it, cannot be searched, or a link on the way cannot be followed (#291) |
| 8 | no record — and no other meaning |
| 64 | `swapped`'s own usage error — never the dispatcher's 2 — and the lifecycle arms' own: `set-lane-tree`'s partial observation, a count that is not one, or a field that is only blanks (below); and, feature 007, on primary, `request-handoff`'s refusals that row 2 lists as 64 for it (its missing lane or checkout, `LANES_LANE`, `LANES_NO_GIT=1`, a pending hand edit or a listing git cannot make, no author identity), which are 2 for every other writer ("The SQL register") |
| 69 | **this could not be done with the register now; nothing was written but by `request-handoff`'s wait, whose request stands, and but for one internal error** (feature 007; `EX_UNAVAILABLE`; "The SQL register"). A **write** exits it when its mode question answered 78, a build whose `lanes-index` refuses `mode=primary` (the fixed text is repeated); when the marker check finds `lanes/STORE` on the tip a files-mode writer fetched, or a primary writer's transaction finds the switch not `primary` (``this estate's register is SQL; configure `mode=primary` ``); and, on primary, when a write's snapshot could not be taken, or its commit was refused for a reason other than the register's own rules (`psql`'s exit 3: LR002, LR003, LR004 after its one retry, any other SQLSTATE), each printing the write's rerun, by its id and generation where it has one, or the command as given with a note that nothing was sent. One internal error ends a write 69 as well: `lanes-index mode` answered primary, and the write left the register's attempt loop without ending, so whether it reached the register is not known, and a write already made reads as found when the command is run again. A **read that decides** exits it, with nothing on stdout, where its own read of the register failed: the read door's `register read --prefetch`, a read site's second read, `act-begin`, `canon-lane --walk`, and `register-text` and `register-grep`, the two verbs the lifecycle scripts' reads moved behind, in every mode (a file that could not be read, `git grep` on `origin/<branch>` ending other than 0 or 1, no workspace repository found, and on primary an image the read door could not give), never `register-grep`'s 1, "no match", and never 8. It is never the files read instead. `request-handoff`'s wait exits it, with nothing on stdout, where its last poll could not read the register: the one 69 after a write, since the request stands in the register and whether the lane is still bound is not known. `append-line` exits it, before the lock, where its classifier gives no answer. The guard may exit only 0 or 2, so it refuses with 2; `lane-start` passes it on at two of its seven read arms, `canon-lane` and `session-lane`, at every other read that already refused, and where its base cannot be read |
| 75 | **register busy** (feature 007; `EX_TEMPFAIL`; "The SQL register"): `register busy: <k> attempts in <D> s, each beaten by another write; nothing was written; a retry in seconds is safe`, where every attempt lost its turn by the deadline, 30 s from the first attempt's start, with the write's rerun printed. Also an attempt whose child did not reach the commit seam within its bound, which the run stops, with nothing sent by that attempt. A write that was in doubt ends 75 only after its lookup by write id found it absent. It is not an outage and it is not 69. `lane-start` passes it on at its seven read arms, and `lane-handoff` where every write that stopped its `PAUSED` record gave it |

3 to 6 are the codes this helper already used; 7 and 8 were free.

**69 and 75 are feature 007's** (`sysexits.h`'s `EX_UNAVAILABLE` and
`EX_TEMPFAIL`). Neither is a code of any older arm. 69 says this could not be
done with the register now, and nothing was written but by `request-handoff`'s
wait, whose request stands, and but for one internal error that cannot say
whether the write reached the register (row 69): do what it prints, a write's
rerun or the read again once the register answers; a build's refusal clears
only with the toolset installed whole, and a mode that disagrees is Brett's;
tell Brett if it keeps coming. 75 comes only from a register that is a
database, means **nothing was written**, and is "run it again in seconds".
Exit 3 on primary is the third: "may not be written; run the printed
`LANES_WRITE_ID` rerun", or an identical write's end, where this run sent
nothing and the message says what to do. The
guard is no writer, and none of the three is its code: it may exit only 0 or 2,
and it refuses with 2 where its own read of the register fails within its 5 s,
or answers from the mirror under a declared outage. See "The SQL register",
"What changes for a lane".

**8 means "there is no such record", and nothing else.** `who <object>` exits 8
when no line anywhere names the object; `who --lane <lane>` exits 8 when the
lane has no log file; the internal reads `lane-start` and `lane-end` use follow
the same rule — `lane-objects` exits 8 for a lane with no log, `live-holder`
exits 8 when the session records were READ and no live one holds the lane
(the ordinary answer for a parked lane) and never when it could not read them,
`register-row` exits 8 when the PUBLISHED register has no row for the lane
(and **2**, not 8, for a name that is not lane-shaped at all — a malformed
argument is a refusal, never an absence), `resolve-home` exits 8 when the lane
has no home on record and none was passed, `swapped` exits 8 when no lane is
swapped on the workstation, `idle-holders` exits 8 when no live but idle
session holds one of the row's earlier ids, and `window-session` exits 8 when
no live session is in the window.

**`swapped` exits 64 on a usage error of its own, never 2.** The launcher gates
its whole Amendment 8 degrade on a `2` from `swapped` meaning one thing —
*this `lanes-edit.sh` predates Amendment 8 and has no such subcommand*, which
is the dispatcher's unknown-subcommand code. A caller's own bug must not be
indistinguishable from an old helper at the one place the degrade is decided,
so `swapped` answers `0` rows, `8` none swapped, `64` a bad call, and leaves
`2` to mean *no such subcommand* alone. `session-start` is the one subcommand that never exits
anything but **0**: it is a hook.
Every other non-zero code from any of them is a FAILURE, and `lane-start`
and `lane-end` treat only 0 and 8 as answers: anything else is a refusal
(exit 1) that prints the helper's own stderr and renames and writes nothing.
They used to return 1 for both, and both scripts read 1 as an answer — a
broken helper made `lane-end` end a lane that was holding an open claim while
telling the next session it was pre-cutover, and made `lane-start` announce
`no live session holds <lane>` about a session that was live in another
window.

### Before the cutover

A lane with **no log file** has not written a line since this amendment landed,
and nothing pretends otherwise:

- `who --lane <lane>` says `no log for <lane>; see its row's state cell` and
  exits 8;
- `lane-end` falls back to the old substring scan of the state cell, and says
  that is what it is doing — over BOTH this checkout's row and the published
  one (`register-row`), either of which showing a hold being a refusal: a fetch
  does not move a working tree, so a peer's `LANDING` appended to the row and
  pushed was invisible to the one path that still decides a merge hold from
  prose;
- `who --landing` still sees that lane, because it reads `LANES.md`;
- existing rows keep their state cells untouched. Nothing is migrated and no
  history is rewritten.

### `lane-start` and `lane-end` under this amendment

`lane-start` appends `STARTED` (or `RESUMED`, following its launch decision)
**after** the row write, carrying `home owner/repo` taken from
`git remote get-url origin` of the lane's own directory. `<repo>-<n>` is a
label, not a scope; this line is the only thing that says where a lane actually
lives. A checkout with no `origin` records `home unknown` rather than a guess,
and says so.

**The home is canonical.** That spelling is resolved through the alias table
— both layers, the shipped `repos.tsv` and the checkout's override (Amendment
9(b)) — *before* the line is written, and every comparison of an object's
repository against a home resolves both sides — because a home is inherited by every `#<n>`
that lane ever writes. A checkout whose `origin` still said
`opensoft/codexFactory` wrote object keys that could never collide with another
lane's `codeXfactory/codexFactory#<n>`, so two lanes held one GitHub issue under
two keys and nothing detected it. A spelling the table has no row for is
recorded exactly as `origin` gives it, with a note asking for a row — guessing
is how the fifth spelling of one repository got into the register.

`lane-end`'s refusal is now **exact**: every object whose last line in this
lane's own log is open is named, with the takeover case naming its own fix, and
the recovery it prints is in the helper's real CLI grammar — one of

```sh
LANES_LANE=<lane> lanes-edit.sh log LANDED <object> → <merge sha>    # the PR merged
LANES_LANE=<lane> lanes-edit.sh release <object> "<why>"             # the claim is given up
LANES_LANE=<lane> lanes-edit.sh log CLOSED <object>                  # closed without merging
```

The arrow is its **own argument**. Quoted into the free-text slot
(`log LANDED <obj> "→ <sha>"`) the sha lands *after* the ` — `, the line stops
matching `LANDED → <sha>`, and the register's Rule 6 line loses the merge sha
for good in a file nothing rewrites. There is no `MERGED` verb at all, and
`log RELEASED` is refused — `release` is the subcommand, and it posts the Rule 1
comment too.

`--force` records what it overrode, in the grammar's own shape:

```text
ENDED — lane X, session <uuid>@<ws>, <UTC>, lane:X — forced; open: opensoft/repoX#11, opensoft/repoX#12
```

` — ` occurs exactly **twice**, which is the guarantee that lets one parser read
every line, and the free text names the objects the lane's own log showed open
rather than blaming a state cell the script never read. **The writer enforces
that**: it refuses any payload or free text containing ` — `, or a newline,
before the lock and before anything is created. It
also prints a generated handoff fragment on **stdout** — `## Done (RELEASED,
CLAIM-LOST, CLOSED, LANDED)` and `## Open (CLAIMED, TAKEOVER, OPENED, LANDING,
WITHDRAWN)`, from the log, each header naming the set it partitioned by — to be
pasted into the handoff the row points at,
because a handoff written from memory is how an object goes missing between two
sessions of one lane. That fragment is the only thing `lane-end` writes to
stdout.

A `CLAIMED` another lane has taken over is listed under `## Open` as
`superseded by TAKEOVER (lane:<X>) — release it`. It belongs there because it is
still this lane's last line on the object and only this lane's `release` closes
it; what it is not is work the next session still holds. The fragment is the one
surface here that becomes a **durable document**, so an object `who` gives to
another lane must not be pasted into the next handoff as simply open — the
refusal, `who <object>` and `who --lane` all carry the takeover already.

### Archivability

A lane's log is archivable when the lane is `RETIRED` and every object in it is
`CLOSED`, `LANDED`, `RELEASED` or `CLAIM-LOST`. **Nothing prunes
automatically.** The amendment states the rule and stops there.

## Swap and restart (Amendment 8)

**DRAFT, 2026-09-12, awaiting Brett Heap's word** (`brettheap/new-workstation#15`).
A **swap** is a planned stop — a usage reset, a profile switch. A **restart** is
the launch that follows it. The lane has to survive the gap, and on
2026-09-12 it twice did not: the restart opened a **new tmux session**, which
loses the window name that carries the lane, and the reset minted a **new
transcript uuid** while the row's session cell still ended on the old one.

Neither half adds a file. The **record** a swap leaves is the lane's own
`PAUSED` line in `lanes/log/<lane>.md`, payload
`swap; window <tmux session>:<index>; workstation <ws>` — an ordinary event line
under Amendment 7's grammar, not a new kind of thing:

```text
PAUSED — lane openRepoProject-1, session 09dd34d1-…@Eagle, 2026-09-12T16:52:39Z, lane:openRepoProject-1 → swap; window claude-team-05b-20260912102132-2699:0; workstation Eagle — on Brett Heap's word: shutdown and i will reset the usage
```

A lane is **swapped** on a workstation when that `PAUSED` is its **last
lane-kind line** — no later `RESUMED`, `STARTED`, `ENDED` or `RETIRED`. The line
above is real, and that lane is *not* swapped today, because a `RESUMED` follows
it seven minutes later.

Two read-only subcommands. **Neither ever writes**, and both honour
`LANES_NO_FETCH=1` — the launcher passes it, because a record this workstation
wrote is already in its own clone and a restart must not wait on the network.
In that mode they still read `origin/main`, exactly as they do after a fetch;
what is skipped is the fetch, never the ref.

**On `mode=primary`** `swapped` is a deciding read of the register, and the
`SessionStart` block (`session-start`) reads the mirror and makes no register
call, so it still answers offline and still exits 0, with a `read: mirror at …`
line. See "The SQL register".

### `swapped [<workstation>]`

The lanes a swap paused on a workstation and nothing has resumed. Default
workstation is `hostname -s`. One row per lane, **tab-separated, lane first**:

```console
$ lanes-edit.sh swapped Eagle
repoSW-1	2026-09-12T10:00:00Z	claude-team-05b-20260912102132-2699:0 @71	~/projects/repoSW	team-05b
repoSW-5	2026-09-12T11:00:00Z	sess-five:5
```

**Amendment 11 gives the row a fourth field, `<dir>`, and a fifth,
`<profile>`, and the `<window>` field a second ref, `<@id>`** — and the second
row above is what every record written before that looks like: the fields are
EMPTY rather than guessed (Amendment 7(i) cuts each lane over at its own next
start, and nothing is backfilled). **The first field before the first tab is
still the lane**, which is the contract both readers of this output take, and
it is what makes a fourth and a fifth field safe to add at all.

`<lane>`, `<UTC>`, `<window>` — the window being the `window <s>:<i>` the
`/swap` recorded, which is where that lane was. **The lane is the register
row's spelling** (Amendment 15), as `window-lane`'s answer is, and not the
spelling of the lane's log file: a log written before that amendment is still
`lanes/log/openxfactory-4.md` beside the row `openXfactory-4`, and `swapped`
lists that lane as `openXfactory-4`, so a reader may hand the field straight to
`tmux rename-window` or to a read of the register. **Exit 0** with
rows, **8** with none (and the reason on stderr, so stdout is only ever rows). A
launcher takes the first row's lane as its default and hands it to
`lane-start --confirm`.

**The rows are in LANDING ORDER — most recently landed first — and not in UTC
order.** Amendment 7's file-order rule orders one lane's own lines and does not
reach across two lanes' files, and two lanes' UTCs are two clocks: they may be
two workstations' (which share no order at all) or one workstation's mid-jump —
this one was observed stepping ±25 s in bursts, and a log commit landed whose
content carried a UTC 26 s after its own committer date. Git's push
serialization is the one order both lanes really share, and it is the same
arbiter `claim` already uses to decide a race. So the key is the position of the
commit that **added** each lane's `PAUSED` line, measured as its distance from
the tip of `origin/main` — history, not a committer date either. The UTC stays
in the output as **information**, and decides nothing. The example above is the
test's own: `repoSW-5` carries the later UTC and landed first, so it is second.

### `session-start`

The whole output of Claude Code's **SessionStart hook**. It reads the hook's
JSON on stdin (`session_id`, `source`, `cwd`) and the tmux window name, resolves
the lane — **the window name first, then the session id against every row's
session cell** — and prints ONE block. The working directory is in the payload
and is deliberately *not* read as a lane: the working directory confers no lane
(Amendment 6(a)), two lanes share one all day, and every nested checkout in the
xFactory estate shares its parent's.

**It never writes, it never touches the network, and it always exits 0** —
including on garbage stdin and on no register at all, which every other
subcommand refuses. A hook that fails is a hook that breaks the session it was
meant to orient; a hook that fetches puts the network in front of every session
start on the workstation, and R-A8-1 rules it out for exactly that reason.

So every line of the block is **this checkout as it last stood**, and the block
says how long ago that was — the line `ssb_tail` prints **unconditionally**, as
the last line of every block whichever branch wrote the rest (Amendment 8(e)):

```text
as of 4m ago (no fetch)
```

The age is `.git/FETCH_HEAD`'s mtime — rewritten by every fetch and by nothing
else — rendered `<n>s`/`<n>m`/`<n>h`/`<n>d ago`; `never` where this checkout has
never fetched, `unknown` where it cannot be read. Neither is a failure and
neither costs a network call. A block that says `as of 4d ago (no fetch)` is a
block a reader knows not to trust about another workstation's lane, which is the
whole of what makes an unfetched read honest.

The lane's session is the one the row records, and the hook says so:

```console
$ lanes-edit.sh session-start   # stdin: {"session_id":"4a91f1dc-…","source":"resume","cwd":"…"}
LANE openRepoProject-1 — handoff handoffs/openRepoProject/session-handoff-2026-09-11-lane-openRepoProject-1.md — row session 4a91f1dc-46dc-48be-a75a-1789fab038d0
stamp the handoff RESUMED (Rule 3) and follow its top block
open: CLAIMED brettheap/new-workstation#15; OPENED brettheap/new-workstation#16; OPENED opensoft/workBenches#63
as of 4m ago (no fetch)
```

The first line's last field reads `row session none recorded` where the row
carries no id this hook can read — the rows that hold only `session_…` footer
ids, which have no transcript uuid to mismatch *against* and are therefore not
reported as a mismatch at all.

The second line is one of three, and it is **derived** rather than assumed from
the ids agreeing: the handoff the ROW names is read and grepped for the line
this session would owe, because a window started by a bare `claude` has no stamp
and does still owe one. Already stamped by the launcher → `handoff already
stamped by lane-start — follow its top block`; no handoff in the row at all →
`the row records no handoff path — there is none to stamp or to follow`.

The third line is the lane's open objects, `who --lane`'s answer on one line;
`open: none open` when it holds nothing, and
`open: no log for <lane> (pre-cutover lane) — see its row's state cell` when it
has no log at all.

**Two different mismatches, and the block says which.** An id the cell *carries*
but does not end on is a superseded transcript of this lane — the 2026-09-11
wrong-lineage resume. The conversation in this window is not the lane's, so it
is left rather than carried on with:

```text
WARNING: this is a superseded transcript of lane repoSS-1; the live one is <U>; you resumed <V> — exit this session and run: lane-start repoSS 1
as of 4m ago (no fetch)
```

That mismatch has one **sub-branch**: where the lane's newest row stamp says
`unknown`, the cell's last id names a transcript this directory does not have, a
relaunch would take the same title fallback again and record nothing either — so
the cure there is the recording act, not a relaunch:

```text
WARNING: this is a superseded transcript of lane repoSS-1; the live one is <U>, which the row records as `unknown` — a relaunch would record nothing either, so run: lane-start --no-launch repoSS 1
as of 4m ago (no fetch)
```

An id the cell does not carry **at all** is the harness having minted a new
transcript with nobody acting. The lane is right and the row is simply behind,
so the cure is the act that reads the live record in this window and appends
that uuid to the cell:

```text
WARNING: this window is lane repoSS-1 whose current session is <U>; this session <V> is in no row — the harness minted a new transcript — run: lane-start --no-launch repoSS 1
as of 4m ago (no fetch)
```

There is no third mismatch, and **no branch names `/resume` or the picker**:
neither is a lane surface, and a reader sent back through the picker performs
the very failure this block exists to catch. Every command is printed **filled
in** — the hook holds the lane, so `<repo> <n>` reads `repoSS 1` by the time
anyone sees it, and a lane named before the `<repo>-<n>` rule gets
`--dir <path> browser-ui-repair` rather than the bad split `browser-ui repair`.

And when neither the window nor the register answers — the one branch that keeps
its placeholders, because no lane is known there to fill them in with, unless
the window name itself parses as `<repo>-<n>`:

```text
no lane bound to this window — run: lane-start <repo> <n>
as of 4m ago (no fetch)
```

`source` is one of `startup`, `resume`, `clear`, `compact`, `fork`. A
**compact** prints **nothing**: the same conversation carries on in the same
window under the same id, and a block there would announce a lane's identity
into the middle of a session that already has it. Everything else gets the
block — a **fork** included, which is a new attachment to the lane's
conversation — and an unknown or missing `source` is treated as a `startup`
rather than dropped.

Installing it is user-local configuration, not part of this repository:
`hooks.SessionStart` in `~/.claude/settings.json`, command
`~/projects/xFactory/lanes-edit.sh session-start`.

### What Amendment 8 does not add

**No new script and no new path.** `swapped`, `session-start`,
`window-session` and `sibling-filter` are subcommands of the writer that already
exists, and every one of them is a **read**: AGENTS.md rule 1's list of writers
(`set-row-state`, `replace-in-row`, `append-line`, `add-row`, `log`,
`claim`, `release`, `commit`) and rule 8's list of tooling paths are both
unchanged by this amendment.

## Bind and list (Amendment 11, amended by Amendment 18)

**DRAFT, 2026-09-13, awaiting Brett Heap's word on the amendment itself**
(`brettheap/new-workstation#21`) — but **eight decisions inside it are
RATIFIED**, verbatim, on 2026-09-13T18:05:29Z: *"Ratify all four"*, *"Yes, build
`lanes` in A11"*, *"Ratify the bundle"*, *"Hotfix brett-wip now, move it with
history"*. Everything below rests on one of those eight.

Amendment 8 made a swap leave a record. Amendment 11 is what makes that record
enough to **restart from**, and it exists because on 2026-09-13 a restart of a
real lane printed `[exited]` and a second one came up in the wrong directory
without the repository's `CLAUDE.md` or the lane's memory, silently.

**On `mode=primary`** `lanes` answers from the register and, only where it
cannot, from the mirror, with `read: mirror at …` on stderr; a caller that acts
on its answer (`lane`, `lane-end --retire-dormant`) passes `lanes --decide`,
which is a deciding read and refuses with 69. `lane-start`'s bind is one
`start-record` write. See "The SQL register".

### The two words

```console
$ lane openRepoProject-1        # cd to its directory, relaunch through the launcher
$ lane                          # the lanes of this checkout, numbered — pick one
$ lanes                         # inside a checkout: ITS lanes, and the next free one
$ lanes --all                   # every lane the register and the logs know
$ lanes --fetch                 # any of them, after refreshing from origin
```

**`lane <name>` is Amendment 11's OUTSIDE half and `restart` is retired**
(**Amendment 18 Addendum 2**, ratified 2026-09-14T16:50:32Z verbatim *"ratify"*,
on Brett Heap's ruling *"i think we can drop restart as a cli command and keep
it inside a claude session with /restart. if we need it for ctx, keep it for
that, but I do not see any reason to expose this to the user. lane does all the
things a user wants"*). `openRepoTools --install` no longer places `restart`
**and REMOVES the copy an earlier install placed** — printing `restart: RETIRED`
beside the file it took away — because a word that merely stops being written
stays on the PATH of every workstation that already took it. It removes only
what it wrote: a file of that name carrying this installer's own header goes,
and one that does not is NAMED, left exactly as it is, and the `rm` that removes
it printed for the person to run. Everything decision 7 ratified about that act
is true of `lane <name>`, which does it and three things more. Everything below
is about `lane <name>` and was written about `restart <lane>`.

`lane <name>` needs **no window, no record of a window and no guess** — only
the lane's name, its `dir` and its `profile`, both of which the record now
carries. That is the whole point: measured on this estate after the tmux server
was replaced, each of the two surfaces the direction names met **one prompt
offering two choices, and the lane it offered was the wrong one**. Declining was
the only correct answer, and declining left the operator where they started.

**It never calls `claude` itself.** Every path goes through the launcher and
therefore through `lane-start`, which renames the window, writes the row stamp,
extends the session cell, writes the `RESUMED` object line, stamps the handoff —
and passes `--name <lane>`, without which the harness derives a record name like
`openrepoproject-b9` and that derived name is what the statusline and
`ListAgents` show.

**It `cd`s into the lane's directory before it launches**, and that is not a
convenience: the harness keys a session to the directory it was started in, so a
restart from the wrong one loads neither the repository's `CLAUDE.md` nor the
lane's memory, while `--resume <uuid>` goes on continuing the right transcript —
which is why the loss is silent.

**`lanes` is never a picker, and `lane` asks exactly one question.** Ratified
decision 3 refused a picker for the words Amendment 11 added, and Amendment 18
Addendum 1 is the later word on a question that decision was not asked: a person
who has to type a lane name has to know one. So `lanes` still asks nothing at
all — it is a read — and `lane` prints the listing, asks `which?` **once**, acts
on the one answer, and stops; with no terminal on stdin it asks nothing either.

`/restart [<lane>]` is the same act from **inside** a running session, and it is
a skill rather than a command because a session cannot `exec` a launcher over
itself: it binds the window through `lane-start --no-launch`, and where this
session is not the lane's conversation it prints exactly `claude --resume <uuid>`
and stops. It is unchanged by Addendum 2: what left the PATH is the outside
half, and `/restart` was always the inside one.

### The bare word narrows inside a checkout

**Brett Heap settled clause (j)'s default on 2026-09-13T20:38:11Z, verbatim
*"Narrow inside a checkout (Recommended)"*, and `lane-start <repo>` with no
position in the same breath, verbatim *"Yes, list and suggest (Recommended)"*.**

A bare `lanes` INSIDE a lane checkout lists **that repository's lanes** — the
checkout around the cwd as clause (i)'s `--dir`, and that checkout's `origin` as
`--repo`, which are an **OR and not an AND**, because a lane's home repository
and the checkout it sits in are not the same fact — and it ends with the **next
free position** and the exact `lane-start <repo> <n>` that takes it, filled in.
The position offered is the **lowest** one no lane of that repository holds,
never the highest plus one: positions come back as lanes end, and `lane-start`
refuses one that is taken, so this is a suggestion with a guard behind it rather
than an assertion.

**The repository is the one `origin` names, and the directory's name is only the
fallback.** Inside a worktree, `basename $(git rev-parse --show-toplevel)` is the
WORKTREE's name: run in a worktree of `openRepoTools` called `ort-a11` the
listing narrowed to a repository called `ort-a11`, found none, and offered
`lane-start ort-a11 1` — a lane whose `$PROJECTS_ROOT/<repo>` cannot exist. A
checkout with no `origin` keeps the directory name, because there is nothing
else to have.

`lanes --all`, and a bare `lanes` outside every checkout, are clause (j)'s
every-lane listing **unchanged**; any explicit narrowing — `--repo`, `--dir`,
`--ws`, `--here` — is the caller saying which lanes they mean and suppresses it
too. `--here` is the pre-Amendment-11 default, only the asking workstation's
lanes, kept as an option: "list the lanes" on a two-workstation estate means
both.

`lane-start <repo>` with **no position** prints that same listing and that same
next free position, and **launches nothing**. It still exits **2** — a
repository is not a lane, and a caller that scripted `lane-start <repo>`
expecting a session must not read 0 from a run that started none — and the
refusal names the position it has just given the reader a listing for. It
renders nothing of its own: it runs `lanes --prefix <repo>`, so the two surfaces
cannot offer different positions.

### What the record carries now

Two payload sub-fields are added to the lane's `STARTED`, `RESUMED` and swap
`PAUSED` lines, and the `window` sub-field gains a second ref:

```text
STARTED — lane openRepoProject-1, session a3ab3df2-…@Eagle, 2026-09-13T04:30:00Z, lane:openRepoProject-1 → home opensoft/openRepoProject; estate openRepoProject; dir ~/projects/openRepoProject; profile team-05a; window claude-team-05a-20260912213845-570498:0 @71
PAUSED — lane openRepoProject-1, session a3ab3df2-…@Eagle, 2026-09-13T04:32:00Z, lane:openRepoProject-1 → swap; window claude-team-05a-20260912213845-570498:0 @71; dir ~/projects/openRepoProject; profile team-05a; workstation Eagle
```

- **`dir <path>`** — the lane's checkout. Two writers put it there: `lane-start`
  from the directory it is about to `cd` into, and the swap from the live
  session's own record. A path containing `, `, ` — ` or `"` is **refused**,
  naming the path; a path containing a **space is written quoted**, which is
  what makes it one ref under Amendment 7(b)'s grammar, and every reader strips
  the quotes.
- **`profile <name>`** — from `$CLAUDE_PROFILE_NAME`, which the launcher exports
  into every session it starts. Without it a restart typed anywhere but in the
  lane's surviving window cannot name the profile the launcher needs.
- **`window <session>:<index> <@id>`** — two space-separated refs in one
  sub-field. **The name is the key and the id is information**: a tmux server
  restart reissues every id from `@0`, so a binding keyed on the id would lose
  the lane where one keyed on the name does not.

**A line written before this carries none of them, and a reader that finds none
says so rather than assuming one.** Nothing is backfilled.

### The lane's directory, in six rungs

`--dir <path>` → the **swap record's** `dir` → the **lane's log** (`lane-dir`) →
`$PROJECTS_ROOT/<repo>` → the estate's **`project.yaml` legs** → **a checkout
named for the home's repository** under `$PROJECTS_ROOT`. First answer wins, and
**the cwd's own checkout is ruled out by name**: deriving the directory from
wherever you happen to be standing is an inference, and `lane-start` writes the
lane's HOME from that directory's `origin`, which every `#n` the lane afterwards
writes inherits. A recorded directory that no longer exists is a **refusal
naming the path**, never a silent re-home.

**Rungs 5 and 6 were four until Evidence 7** (`708395e`), where a lane whose
checkout is NESTED — `~/projects/xFactory/xFactories/OpsxFactory`, not
`$PROJECTS_ROOT/opsXfactory` — met an exit 1 behind an `exec` and a pane that
said `[exited]`. Neither is a search for a directory that looks right:

- **Rung 5 is a DECLARATION.** openRepoShape's manifest carries a top-level
  `legs:` list of `repository:`/`path:`, and Amendment 7 decision 3 treats a leg
  as home. Where a manifest names the lane's recorded home as a leg, that leg's
  `path`, resolved against the manifest's own directory, IS the checkout.
- **Rung 6 is a NAME, and the name is the repository's.** One or two levels
  under `$PROJECTS_ROOT`, a directory named for the home's repository —
  `resolve-repo`'s canonical spelling, because `opsXfactory` the lane label and
  `OpsxFactory` the repository are not the same string.

**Both are proved by `origin` and both are skipped entirely where the lane has
no recorded home**, because there is then nothing to prove a candidate against
and a match would be the guess rung 4 already refuses to make. A candidate whose
`origin` is not the recorded home is passed over, not taken. And when all six
answer nothing the end is still a **refusal, exit 2**, one line a person can
type — never the exit 1 Evidence 7 met.

### The new reads

All read-only, all out of `origin/<branch>`, all following Amendment 7(d)'s
fail-closed convention — **0** an answer, **8** *no answer*, **64** a usage error
of their own — with one exception that every un-upgraded workstation is in:
**`2` is a helper predating Amendment 11**, expected and silent, and a caller
falls to its next rung there rather than refusing.

| read | what it answers |
|---|---|
| `lanes-edit.sh lane-dir <lane>` | the `dir ` of the lane's **last** lane-kind line carrying one, unquoted where it was written quoted |
| `lanes-edit.sh lane-profile <lane>` | the same read one sub-field along — the `profile ` of that last line. An addition to the amendment's own table, so that `lane <name>` can learn a profile with one `git show` instead of a listing that reads every log on the workstation |
| `lanes-edit.sh window-lane [<ws>] <@id>\|<session>:<index>` | the lane bound to a window **of the asking workstation** — the register row whose name is the window's name, else that workstation's swap record naming that ref. Both rungs answer in the **register row's own spelling** (Amendment 15): what this prints is renamed to, `--name`d with and typed as `lane <name>` |
| `lanes-edit.sh session-lane <uuid>` | the lane whose register row's **session cell** names that transcript uuid. Adoption act 0's; it is the read the `SessionStart` hook already made. **0** the lane · **8** no row's cell names it · **64** usage — like every other read in this table, by A11 Addendum 4 ruling 1, which corrects act 0's `2` · **2** a helper predating the read |
| `lanes-edit.sh last-session <lane>` | the lane's resume target: the last uuid in the published cell **whatever shape it is in**, and failing that the session of its last `PAUSED`/`RESUMED`. This is what `/restart`'s step 4 reads, by A11 Addendum 4 ruling 14, rather than clause (f)'s `register-row` alone — so a lane whose row was never stamped but whose log records the session it paused in still has a resume target |
| `lanes-edit.sh forks <lane>` | the **live forks** of the lane's transcript — never holders, and a defect to retire |
| `lanes-edit.sh lanes [--repo\|--dir\|--prefix\|--ws\|--lane\|--here\|--all\|--closed\|--fetch]` | every lane, newest write first, tab-separated: clause (j)'s **ten columns in clause (j)'s order** — name, state, workstation, profile, window, last transcript uuid, directory, held objects, age, the line that binds it — then the read's own three, `home`, the count of live forks, and — since Amendment 18(b) — whether THIS place may pronounce on that binding (`here`, `elsewhere`, or `none` for a row with no binding line at all), which is what `lane-groups` files an `IDLE` row by — then Amendment 19's four: the row's **class** (`closed`, `dormant` or `none`), its `started` cell, its state cell's **head** and the **flag words** in that cell. The last two are carried **for a row with no object log** — 19(a)'s dormant row, whose cell is all this read has to show — and are `none` for a row that has one, which has its log instead: they are free text of up to 280 characters per row and the table the listing joins on is walked once per lane, so carrying them for every row cost the whole estate 3x (42 s against 14 on a register of 132 rows, measured). **No column is ever empty**, because three readers split the row with a tab-IFS `read`, to which an empty field between two tabs is no field at all. **Column 7, the directory, is escaped** the way `lane-worktrees`' porcelain escapes a field (#301): a backslash is written `\\`, a TAB `\t`, a newline `\n` and a carriage return `\r`, so a directory holding a TAB is still one column and the row still seventeen. `lanes`, `lane` and `worktrees` decode it before they show it or take it for a path, and a directory holding none of the four is printed as it is. **CLOSED and DORMANT rows are left out unless `--closed` is passed** (19(b)); `--lane <name>` is never filtered, because its caller has named the lane. `lanes` and `lane` each render the subset their surface needs (A11 Addendum 4 ruling 7), and column 10 is the read's so the two cannot offer different commands; since Amendment 18 Addendum 2 the word it names is `lane <name>`. **The one read whose default is local**, and `--lane <lane>` answers about one without walking the estate. `--prefix <repo>` is the LABEL fallback the checkout narrowing and `lane-start <repo>` both ask for, used only where a lane has neither a home nor a `dir` |
| `lanes-edit.sh lane-groups [<ws>]` | **the rows on STDIN**, each with the group the pick puts it in: `available` (PARKED, or a binding that workstation **and container** prove dead), `live`, `elsewhere` — an `IDLE` row whose binding is another container on this same host is **elsewhere**, because IDLE means this host saw no live record and across that seam the pid it would have read is in another namespace (Amendment 18(b)). Closed and dormant rows are DROPPED (Amendment 19). It reads nothing itself — the caller has already paid for `lanes`, and two surfaces computing one partition is how they come to disagree |
| `lanes-edit.sh next-free <repo>` | **the rows on STDIN plus every row of this checkout's register and of `lanes/archive/LANES-retired.md`** (Amendment 19(b) and (d): the rows the listing hides and the rows `archive-rows` has moved out still hold their positions), and the LOWEST position no lane of that repository HAS EVER held — `ENDED` and `RETIRED` rows reserve theirs, because a lane's identity is its name and its object log `lanes/log/<lane>.md` is append-only, so a second lane at a retired position would write its life into the first one's file. The name before the position is compared whole, so `repo-foo-1` is no lane of `repo`. `lanes`'s footer and `lane`'s `f` answer both read it, so the two cannot offer different positions |
| `lanes-edit.sh binding <lane>` | where the lane is bound, and whether this place may pronounce on it: `<host> <container> <window> <utc> <session> <os> <here\|elsewhere> <live\|gone\|unknown>` (Amendment 18(b)). **0** bound · **8** free · **1** the log could not be read, which is never "this lane is free" |
| `lanes-edit.sh workstation` | `<workstation> <source> <host> <source> <os> <source> <container> <source>` — eight tab-separated fields since Amendment 18(a), the first two unchanged. A source is `seam`, `hostname`, `workstation`, `kernel`, `outside`, or, for the workstation itself, `container-unset` |
| `lanes-edit.sh fetch-age` | how old this checkout's answer is |

**`request-handoff` is the one WRITE in that family** and is not a read at all:
`lanes-edit.sh request-handoff <lane> [--wait <s>] [--session <uuid>]` writes
clause (c)'s `HANDOFF-REQUESTED`, pushes it, types `/handoff --exit requested by
…` into the bound pane where that pane is on this tmux server, and waits;
`--force "<why>"` is clause (e)'s second invocation and releases the binding on
its holder's behalf. **0** the lane is free — bind it · **2** the wait ended
empty, or the request was refused · **8** the lane was already free · **1** a
read failed.

**`window-lane` owns the agreement rule, and it owns it once.** Existing-now is
not enough on its own: a record naming `claude-y:0 @97`, met from the window that
has since taken `claude-y:0` as `@200`, passes the existing-now test on its ref
and would bind the lane to a **stranger's pane** with no question. So a record
that carries an `<@id>` is matched on its `<session>:<index>` **only where the
window now holding that ref reports that same id**; a record with no id is
matched on the ref alone. Three callers share that one implementation — the
launcher's precedence 3, `/restart`'s step 2(c) and `/lane-swap`'s step 1 —
because three implementations of one rule is how they would come to disagree.

### Two things that are never taken

**A window named for another lane never lends its session.** `lane-start`'s step
3b learns a harness-minted uuid from the session live in the window it was typed
in — which is how a `/clear`, a usage reset and a profile switch stay recorded.
Its fence is **two vetoes and no permission**: the **register veto** (a uuid that
belongs to another row is never taken, read through `session-lane` and
fail-closed) and the **window-name veto** (a window named for another lane is
never taken). Neither the window's name being the lane nor `live-holder`
answering `here` is a *condition for taking* — at step 3b the window is usually
still called `claude` and there is no holder here, which is exactly the case the
mechanism exists for.

**A fork of a lane's transcript is never a holder and never writes the
register.** A `--fork-session` copies the parent's title and gets a **new id**,
so lineage is never the test: the holder is the session whose id the published
cell names. `lanes`, `/restart`, `who --lane` and the `SessionStart` hook all
show a live fork as a **defect to retire**, and none of them kills anything.
**Retiring it is `lane-end <lane> --retire <pid|uuid>`** — the one act, ratified
decision 8(e) and clause (k) rule (e). It is the **DOOR** to Amendment 6(d) and
not a record of one: it proves the pid or uuid is this lane's live fork and
prints 6(d) filled in. **It writes nothing** — a `RETIRED` carrying a payload
would be a seventh edit to in-force text, and Amendment 7(b) gives that verb
none — so every read goes on naming the fork until the person takes the printed
act. And it **kills nothing either**: stopping the process is a separate act and
it stays the person's.

**A duplicate holder of a lane's OWN transcript is a different thing, and it IS
killed** (opensoft/openRepoTools#39, Amendment 18(h)). A cross-profile resume
can leave a `bg-pty-host` running `claude --fork-session --resume
<path>/<uuid>.jsonl` behind; once a later `lane-start --no-launch` binds that
forked id as the row's own session, the id is no longer one `forks` (decision
8(e)) will ever report — it is designed to stay silent about an id the row
DOES record — while a second live process now holds the one transcript
Amendment 18(h) says exactly one may. `lanes-edit.sh duplicate-holder <lane>`
finds it in the PROCESS TABLE instead (`pgrep -f` for a live `--fork-session`,
then `ps -o pid=,ppid=,args= -p` per candidate — portable across GNU and
BSD/macOS), matched against every id this lane's row has ever carried by the
`--resume` value read whole, a directory with a space in it included
(opensoft/openRepoTools#76: `ps` joins the argv with spaces and quotes nothing,
so the value runs to the first `.jsonl` that ends an argument, and a value with
a space in it counts only as one absolute path, starting with `/` and holding
no ` /`, by Brett Heap's ruling of 2026-10-18 on PR #205), and
excludes the lane's own live holder (`live_holder`, the same implementation
`live-holder` already calls) **and the harness's own companion of it** —
Amendment 8 ruling (g)'s `kind: bg` record carrying the same id in the same
profile as an interactive record of it, which is one session and not a rival
holder, together with any wrapper whose child is that companion. A records
tree that cannot be read for that test is a read that failed, never "no
companion", and a register that cannot be read is never "no duplicate": the
row's ids are the whole search, so it exits 1 and names the read
(opensoft/openRepoTools#67). `lane-end <lane> --retire <pid>` tries `forks`
first and this second, and on a match here it TERMs the `bg-pty-host` parent
and EVERY child of it that resumes the same transcript, not only the first
(opensoft/openRepoTools#86), reports each pid it signalled, and says RETIRED
only once every one of them is gone; it refuses, naming the reason, when the
pid given is the lane's own live session rather than the duplicate. Before this
the retirement was a bare `kill -TERM`, outside every tool this estate has.

### The workstation's name

`$LANES_WORKSTATION`, exported by the workBenches launcher from the host into
every session and container it starts. Outside a container it still defaults to
`hostname -s`. **Inside a container with no value, every writer refuses, exit 2,
naming the variable** — because a container's `hostname` is the container's id,
not a workstation, a row on a workstation that does not exist is a row no reader
can match, and the log is never rewritten. `lanes-edit.sh` already carries six
such lines in one lane's log, and they stay where they are.

## One binding per lane (Amendment 18)

**A lane has ONE binding. The record says where it is — host, OS, container,
window — and a second place ASKS the first to hand off before it binds; nothing
is taken silently.** Ratified by Brett Heap 2026-09-14T13:15:18Z, verbatim
*"merge 37 and 78, ratify revision 4"*, on his observation of the same day:

> we are working on lanes tooling … if I run a lane, then i am on a host running
> macOS or linux or windows … and in all 3, i might be in a container. we should
> probably note the host name, OS type and container name if there is one. so if
> I am on wsl and I am in cloudBench and then move to pyBench, when I try to
> restart in pyBench, it should ask me if I want to pause the lane on cloudBench.
> we shoudl not allow two bindings to a lane … we need to send a signal to that
> session to shutdown. once it is shutdown, then we alow the new connection to
> bind

### Why — a pid does not cross a pid namespace

A lane's liveness is read from the harness's session records and a `kill -0` of
the recorded pid. Both are **local** facts. Across workstations the tooling
already said so — another machine's row is UNKNOWN and `who` says to ask that
lane. Across **containers on one machine** it did not: two bench containers on
Eagle share the profile directory, so pyBench can read a record cloudBench
wrote and then `kill -0` a pid **in cloudBench's namespace**, which is either
nothing or some other process. A lane live and writing in cloudBench read NOT
LIVE from pyBench, and `lane-start` there took the name.

### The three sub-fields

`STARTED`, `RESUMED` and `PAUSED` carry three more, after Amendment 11(c)'s
`dir`/`profile`/`window` and Amendment 17(b)'s `agent`/`transcript`:

```text
STARTED — lane openRepoTools-3, session d1ac715c-…@Eagle, 2026-09-15T12:38:51Z, lane:openRepoTools-3 → home opensoft/openRepoTools; estate openRepoTools; dir ~/projects/openRepoTools; profile team-05c; window claude-team-05c-…:0 @71; host eagle; os wsl; container cloud-bench
```

- **`host <name>`** — the MACHINE's short hostname **as it reads outside any
  container**. The launcher exports it (`LANES_HOST`); a writer outside a
  container reads `hostname -s`; a writer **inside** one with no export writes
  the **Rule 10 workstation name**, because in this estate a workstation is one
  host and a container's own `hostname` is its id.
- **`os <linux|macos|wsl|windows>`** — the HOST's operating system as the person
  means it. The launcher exports it (`LANES_OS`); with no export the kernel is
  probed: `Darwin` → `macos`; `Linux` whose `/proc/version` names `microsoft` →
  `wsl`; one naming `linuxkit` → `macos` (Docker Desktop's VM); any other
  `Linux` → `linux`. **`windows` is written only by a launcher on the Windows
  host itself** — this toolset is bash and runs in WSL2 there, so its own probe
  never says it.
- **`container <name|none>`** — the bench's name as the launcher exports it
  (`LANES_CONTAINER`: `py-bench`, `cloud-bench`, …); inside a container with no
  export, **that container's own `hostname`**, which for Docker's default is its
  id and is the one thing that tells two unnamed containers apart; outside every
  container, the word `none`.

They are written by **`write_event`**, the one writer every caller goes through
— `lane-start`'s `STARTED`/`RESUMED`, the handoff's `PAUSED`, and the forced
release below — so no boundary script carries a copy of the probe. **A value is
read live at every write and never carried across a run**: measured
2026-09-15T12:17Z, when a bench container on this estate was recreated with a
new hostname and its `~/.local/bin` went with it. A container's `hostname` names
that container for as long as it exists and names nothing afterwards.

**`ENDED` and `RETIRED` carry none of them.** Amendment 11(c) keeps those two
payload-free — edit 1 of six, `R-A11-15` — and a verb that RELEASES a binding
has no binding to describe. (Amendment 18's Adoption list names them in passing;
the RULE, clause (a), names `STARTED`, `RESUMED` and `PAUSED`, and the rule
governs.)

**A line written before this amendment carries none of the three and stays
valid**: readers treat its binding as *the window on the row's workstation*,
which is what they read before. Nothing is backfilled.

**"None of the three" means all three, not `host` alone.** The writer drops an
offending sub-field on its own and keeps the line, so a line carrying a
`container` and an `os` and no `host` is a MODERN line that lost one field — and
reading it as pre-amendment would ignore the container it does carry. All three
absent is the only shape that is really from before this clause; anything less
is matched on host **and** container like every other modern line.

### What the launcher must export

`opensoft/workBenches#77` — `claude-profile` and the bench shells export
`LANES_HOST`, `LANES_OS` and `LANES_CONTAINER` into **every session and every
container they start**, beside the `LANES_WORKSTATION` they already export. The
per-host act is nothing more than that export; this toolset only ever READS
them, exactly as it reads `LANES_WORKSTATION`, and its fallbacks above are what
answer until the export arrives.

### The binding, and who may pronounce it dead

A lane's **binding** is the `host`, `container` and `window` of its last
lane-kind line that is `STARTED` or `RESUMED` **with no `PAUSED`, `ENDED` or
`RETIRED` after it**. A lane whose last lane-kind line is one of those three, or
which has none, is **FREE**.

```sh
lanes-edit.sh binding <lane>
# <host> <container> <window> <utc> <session> <os> <here|elsewhere> <live|gone|unknown>
# 0 bound · 8 free · 1 the log could not be read · 64 usage
```

**Liveness is pronounced only from INSIDE the binding's own host and container**,
where the pid namespace is the record's. From anywhere else a binding is
**UNKNOWN, never dead**, whatever `kill -0` says — that is the seventh column.

A **modern** record is matched on the machine's own hostname AND the container;
the Rule 10 workstation name is accepted as the host only for a **pre-amendment**
line, which has nothing else to be matched on. Clause (a)'s own fallback still
works through that without a second rule: a writer inside a container with no
`$LANES_HOST` writes the workstation name into `host`, and a reader in that same
container computes the same name the same way.

That column is also what the pick reads. `lane_groups` asks it **before** the
state, so a row that reads `LIVE` because a live session record here names one
of its ids — the records are shared between containers, and the pid that record
names is in another namespace — is still filed **BOUND ELSEWHERE** when its
binding is another place's. The act there is clause (c)'s request; and because
watching a lane and taking it are two acts of which only the second is the
collision, that branch also names the attach filled in wherever the window is
live on this tmux server. A **parked** lane of another container is untouched by
all of it: column 13 carries the locality of a binding that STANDS, and parking
IS the handoff.

**The one exception is the window**, and it is the eighth column. One host's
launcher mounts ONE tmux socket into every container it starts, so where the
asker and the binding share a tmux server and the binding's window **no longer
exists there**, the binding is **DEAD** — the pane a session must live in is
gone. The window is matched by Amendment 11(h)'s agreement rule, the `<@id>`
TOGETHER with its `<session>`, because tmux reuses ids once a window is gone.
Amendment 6(d)'s retire act and openRepoTools#30's takeover path apply to that
case as they stand. A window on another **host** is `unknown` and never `gone`:
an id from another machine resolving here would be a coincidence.

**And so is a window this place cannot ASK about** — no tmux binary, no server
running, a socket this user cannot read, a `$TMUX_TMPDIR` that differs. `tmux`
answers an id that resolves nowhere and a server that is not there with the SAME
empty line, and only one of the two is a dead pane; so the server is asked a
question that does not mention the id, and a server that does not answer leaves
the binding `unknown`. The alternative is a container the host's socket was
never mounted into pronouncing every other container's lane DEAD, which is this
clause's own collision arriving through its own exception.

### The second place asks

`lane-start <repo> <n>` and `lane <name>` run from a place that is not the
binding — a different `host`, `container` or `window` — **REFUSE to bind**.
Where stdin is a terminal they first ask, naming the binding:

```text
lane openRepoTools-3 is bound to window cloudsess:4 @62 in container cloud-bench
on host eagle (session d1ac715c-…, last line 2026-09-15T12:39:07Z); this tmux
server says that window is live.
ask it to hand off, then bind here? [y/N]
```

A `y`, or the flag **`--request-handoff`** from a caller with no terminal (an
agent's stdin is not one, and the refusal names the flag), is **the request**:

```sh
lanes-edit.sh request-handoff <lane> [--wait <s>] [--session <uuid>] [--no-wait]
lanes-edit.sh request-handoff <lane> --force "<why>"
# 0 the lane is free — bind it · 2 the wait ended empty, or refused
# 8 the lane was already free · 1 a read failed · 64 usage
```

In the clause's own order, and the order is load-bearing:

1. **The line is written and PUSHED first** (on `mode=primary`, committed to the
   register first: below), so the bound session can read it from anywhere:
   `HANDOFF-REQUESTED — lane <l>, session <requester uuid>@<ws>, <UTC>, lane:<l> → by host <h>; container <c>; window <w>; wait <n>s`.
   `HANDOFF-REQUESTED` is a lane-kind verb that **changes no state**: every
   last-line reader skips it, and `lane-last` after a request still answers
   `STARTED`.
2. **Then, and only where the bound pane is on THIS tmux server**, the requester
   types `/handoff --exit requested by <uuid>@<host>/<container>` into it —
   Amendment 12's M1, the one mechanism a running session has, and only while
   that pane's current command is `claude`. It is an optimisation of the
   same-host case and never the request: a session that never reads it answers
   at its hook's next read of the pushed line.
3. **Then it waits** — `--wait <s>`, default **300** — polling the published log
   every fifteen seconds (`$LANES_POLL_SECONDS` is the test seam) for a
   `PAUSED`, `ENDED` or `RETIRED` newer than the request. When one arrives the
   lane is free and the requester binds as it would have.

**On `mode=primary`** the request is one register write, committed before the
pane is typed into, and the wait polls the register, not the published log,
which is the mirror; `binding` is a deciding read of the register. A wait whose
last poll could not read the register exits 69: the request stands, whether the
lane is still bound is not known, and nothing here bound it. A refusal that
primary adds before its claim (no register checkout, say) is 64 for
`request-handoff`, where other writers give 2; a lane name out of class, a
malformed `LANES_WRITE_ID` or `LANES_WRITE_GENERATION`, and the act's checks
keep 2. See "The SQL register".

The **session field is the requester's own transcript uuid** and Amendment 7(b)
admits nothing else there. Where none is knowable — a bare shell with no
`$CLAUDE_CODE_SESSION_ID` and no live record for its window — the request is
**refused** and names `--session <uuid>`, rather than writing the literal
`unknown` into a log no later line can correct.

### The bound session answers at its own prompt

The prompt guard (Amendment 12) makes two more reads of the lane's own log:

- **(d)** a `HANDOFF-REQUESTED` newer than this session's binding and not yet
  answered → it **refuses that one prompt**, names who asked and from where, and
  types `/handoff --exit requested by …` into its own pane. The handoff is
  Amendment 17(a)'s, unchanged in its steps, and under `--exit` the session
  **ENDS** — a handoff to another place is a handoff and not a restart. Nothing
  the person typed is lost: the refused prompt is theirs to type again in the
  new place.
- **(e)** a `PAUSED` newer than the binding that **this session did not write**
  → it refuses **every prompt from then on**, naming the line and who forced it,
  until the person there hands off or ends the session.

**A working session is never interrupted** (clause (f)): no `Escape`, no signal
to the process, no `respawn-pane` from a requester. A hook fires at a prompt
boundary; a session inside a long turn answers when that turn ends.

### The wait ends with nothing — refuse, and only a word forces

A wait that ends with no release is a **refusal, exit 2**, printing the facts a
person needs: the binding (host, container, window, the line and its UTC), the
request and its UTC, whether the pane was reachable and typed into, and the one
word that overrides.

**`--force` is a second invocation, never automatic**, and it takes a WHY. It
writes

```text
PAUSED — lane <l>, session <requester uuid>@<ws>, <UTC>, lane:<l> → on behalf of <bound uuid>; forced by <uuid>@<host>/<container>; why <why>; host …; os …; container …
```

— the **writer's own session in the session field**, so it is not impersonation
(the retired-lane release of 2026-09-13, openRepoTools#30, is the precedent) —
and then binds. The why is written as a NAMED sub-field, `why <text>`, which
clause (e) does not spell and which the log needs: every reader here matches a
sub-field by the word it OPENS with, so a why beginning `host is unreachable`
would be read as that line's `host`, refused by the writer's own check, and the
person would be told about a field they did not write. The line's own three
separators — `, `, `; `, ` — ` — are folded to a middle dot inside it rather
than refused, because the person typed a sentence and not a grammar. The bound session, if it is alive after all, reads that line at
its next prompt and stops, loudly. **Two places never both write a lane in
silence.**

### A live lane's one act is the attach

A lane that is LIVE is never started a second time — clause (h) is one live
process per transcript, and a `lane-start` or a `pclaude` on a running lane is
exactly the second one. So `lanes` prints the act for it, filled in:

```text
openRepoTools-3            LIVE     Eagle      team-05c     bindsess:0 @61
                           attach: tmux switch-client -t bindsess:@61   (it is LIVE — never a second lane-start on a running lane)
```

`tmux switch-client` inside tmux, `tmux attach` outside it. The target names the
session **and** the window, because `attach -t <session>` alone lands on
whatever that session has since made current — another lane. The choice between
the two is made in the READ (`lanes_rows`, column 10), so `lanes` and `lane`
cannot come to offer different acts for one row.

### What `workstation` prints now

```sh
lanes-edit.sh workstation
# <workstation> <source> <host> <source> <os> <source> <container> <source>
```

Eight tab-separated fields; the first two are unchanged, which is the contract
`lanes`, `lane` and `lane-handoff` already read with `cut -f1`/`cut -f2`. Each
source is `seam` (the launcher exported it), `hostname` (probed), `workstation`
(a container with no export, writing the Rule 10 name for its host), `kernel`
(the `uname` / `/proc/version` probe) or `outside` (no container) — **and, for
the WORKSTATION field alone, `container-unset`**, which is Rule 10's own answer
where this IS a container and nothing exported `$LANES_WORKSTATION`: the NAME
still answers there, because a read in front of every launch may not refuse,
while every WRITER refuses on it by name. It predates this clause and the reads
table above has carried it all along; a reader holding this paragraph to five
words would reject a valid eight-field line from exactly the containers this
amendment is about.

## The name guard and the lock (Amendment 12)

**A lane session normally works while three names are one.** A readable,
recoverable lane/session mismatch now pauses for an explicit choice; unreadable,
ambiguous, duplicate, or superseded identity still refuses the prompt. Ratified
by Brett Heap 2026-09-13T18:20:44Z — *"Ratify revision 2"*, with M1 *"Type
/rename into the pane"*.

The three:

1. the tmux **WINDOW** it runs in is named for a lane;
2. the **SESSION's own name** — the live record's `name` — is that same name;
3. the register **ROW** keyed by that name records this session's id as the
   **LAST** id of its session cell (Amendment 6(b)).

`lanes-edit.sh guard` is the `UserPromptSubmit` hook that reads them. Where they
agree it is **silent and exits 0**; where they do not it **exits 2**, which is
what blocks a prompt, and prints the triple as it stands and the ONE command
that cures it, filled in. It is the only subcommand in that file that refuses a
person's work.

```console
$ # what the person sees on a blocked prompt
lanes-edit: THE NAME GUARD REFUSES THIS PROMPT (lane-collision-protocol Amendment 12). The three names:
lanes-edit:   window   eagle:@12 'claude'
lanes-edit:   session  d1ac715c-… 'openRepoTools-3' (nameSource user) pid 1924546 profile team-05f
lanes-edit:   row      this window names no lane, so no row is keyed by it
lanes-edit: this window is named 'claude', which is no lane, while the SESSION is named for lane openRepoTools-3 — …
lanes-edit: run: lane-start --no-launch openRepoTools 3
```

**On `mode=primary` the guard reads the database,** inside the same 5 s and with
the hook string unchanged. Where that read fails it refuses with 2 and names two
ways out, and a declared outage lets it answer from the mirror. Its answer 2,
"adjust the lane to session …", runs detached and returns "BINDING" at once, in
every mode. See "The SQL register".

### The table — one row per state, one cure each

Every message prints the register's own spelling of the lane (Amendment 15) and
every command is filled in, never `<repo> <n>`.

| what it finds | what it does |
|---|---|
| the window is not a lane, the session name parses as `<repo>-<n>` | `lane-start --no-launch <repo> <n>` — **the 2026-09-10 case**, which ran for three days unrecorded |
| neither name is a lane | refuses and says so: WHICH lane this work is is yours to name, and a guard that guessed would bind a window to a row nobody chose |
| the window is a lane, this uuid is the row's last id, the session name differs **only by case** | the lock renames it to the ROW's spelling — a lane name is ONE name under any case (Amendment 15), so there is nothing here for a person to decide |
| … and the session name is a **FORMER name of this lane** | the lock renames it to the row's spelling — the alias table makes that name this lane for ever (Amendment 16(e)), so a rename run from another window is drift to finish, not a choice to put to anyone (Amendment 16(f)) |
| … and the session name differs by more — a non-lane word, a `<lane> (N)` title, ANOTHER lane's name, no name at all | **THE THREE-CHOICE OFFER** (below), and NOTHING is typed until a person picks `3` |
| the window is a lane, this uuid is IN the cell but not last | a SUPERSEDED transcript: exit, `lane-start <repo> <n>`, which resumes the id the row ends on |
| the window is a lane, this uuid is in NO row | the harness minted a transcript with nobody acting: `lane-start --no-launch <repo> <n>`, the recording act, no relaunch |
| the window's name matches TWO rows differing only by case | refuses naming both spellings and the merge, which is a person's act (Amendment 15(d)) |
| not inside tmux at all | refuses: a lane RUNS in a tmux window named for it (Rule 4, Amendment 2) |

**A lane named before Rule 4's `<repo>-<n>` form has no `<repo> <n>` to fill
in**, and nothing in the register, the window or the session says where its
checkout is. Rows 1, 5 and 6 then say so in the offer's own words (below) and
print the offer's own line, `lane-start [--no-launch] --dir <that lane's
checkout> <lane>`: the checkout is yours to supply, and a bare `--dir <path>` is
never printed as though it were a command (openRepoTools#73).

### The lock

Under the projects root the session name is **not the person's to set freely; it
is the lane's** (clause (h), Brett Heap's D5). `lane-start` sets it at every
launch (`--name "$LANE"` on all three branches), and **the lock is what answers
the two drifts that are not decisions**:

* a name that differs from the row's only **by CASE**, which is the same lane
  under Amendment 15; and
* a name that is a **FORMER name of the row's own lane**, which is the same lane
  under Amendment 16(e) — the alias table resolves it to this row for ever, so
  a session still called `repoRen-1` on a lane the register now spells
  `repoRen-7` is not a lane anybody is moving to. It is what a `lane-rename` run
  from ANOTHER window leaves behind, and finishing it is clause (f)'s whole job.

There the guard renames it
for you — it types `/rename <lane>` into this session's own tmux pane, which is
the only path a running session's name has (M1 — the docs name `--name` at
launch, `/rename`, and `Ctrl+R` in the picker, and nothing else). That one
prompt is refused so the rename lands first; send it again. (The WINDOW's half
of the same drift needs nobody: a window's name is one tmux call, so the guard
simply makes it — Amendment 16(f).)

**Every wider mismatch is the offer below and nothing is typed into the pane
until a person picks `3`.** A session called `openrepotools-b9`, a
`<lane> (2)` title, ANOTHER lane's name, no name at all: which lane this
conversation is is a decision, and the guard asks rather than renaming on the
person's behalf — and two of the three answers would be wrong for a former
name, which is why that one is the lock's and not the offer's. What follows about the PANE — which one, and whether it may be
typed into at all — is the mechanism both the lock and choice `3` use.

The pane's **current command is asked first** and nothing is typed into a pane
running anything else — `claude` and nothing else, which is M1's own word:
`/rename openRepoTools-3` typed at a shell is a command that does not exist,
typed into an editor it is text nobody wrote, and typed into a Node REPL it is
input somebody has to clear. A pane whose command cannot be read is not typed
into either — fail closed. Every outcome but a successful typing prints the line
for you to type yourself, which is the cure and not a failure: on Eagle today
two panes report `bash` (a session under a launcher wrapper), and those are
exactly the windows where you will read it.

**Which pane**: the one the live record names, and where the record names none
— the harness has been seen to write no `tmux` field for a process plainly in a
window — the pane this hook is itself running in, `tmux display-message -p
'#{pane_id}'`. Both hooks run inside the session's own pane, so that is the same
pane, asked of tmux rather than guessed.

A `<lane> (N)` title is a **mismatch** (ratified decision D4) and the suffix is
evidence: it is exactly what a rename into a title something else still holds
mints, so another holder of that name was live. That reading is printed **with
the offer's three choices**, where this state now arrives, and it names
`lanes-edit.sh forks <lane>` and `lane-end <lane> --retire <pid>` — never a
`kill`, because stopping a process is the person's act and stays theirs
(Amendment 11 clause (k) rule (e)).

### The offer

For a readable current lane/session mismatch, the guard now presents three
numbered choices rather than choosing a repair automatically:

1. **Allow this lane** — explicitly allow the current lane/session pair for
   this transcript. Later prompts warn without blocking until either name
   changes.
2. **Adjust lane to session** — move the window and register binding to the
   session's lane through the existing lane-start and UUID-anchor path.
3. **Adjust session to lane** — type /rename <lane> into the verified Claude
   pane. This choice never changes the register.

The answer is the next prompt and is consumed by the guard. Invalid answers
keep the offer pending. If the pane cannot accept choice 3, the guard prints
the manual /rename command and keeps the offer. Choice 1 is stored per
transcript under the session's offer directory and is invalidated when the
lane, session name, or transcript changes.

**And choice 1 is an answer to ONE row of the table above, honoured there and
nowhere earlier.** It is read at the row it was given for — a readable lane
whose uuid is the row's LAST id and whose session name is not the lane's — so
an unreadable register, an ambiguous one, a duplicate live process, a
SUPERSEDED transcript and a uuid the row does not name at all are refused
through it, exactly as they are without it: an allowance is not a bypass. The
three names agreeing again ends it too — that is one of them having changed —
so the file is dropped and a later drift is a fresh question rather than a
silent pass on an old answer.

*"if the user does a rename, then we should offer to move to that lane or create
a new lane if we do not have one as that name"* (D5, verbatim). A record whose
`nameSource` is `user`, whose `name` is another lane's, and whose `nameSince` is
later than this window's binding is a person saying **"this is that lane now"**
— so the guard **asks** rather than guessing:

```console
lanes-edit: WARNING: the lane and Claude session names disagree.
lanes-edit: 1) explicitly allow this lane for this transcript
lanes-edit: 2) adjust the lane to session openRepoShape-2
lanes-edit: 3) adjust the session to lane openRepoTools-3 (types `/rename openRepoTools-3`)
lanes-edit: Reply `1`, `2` or `3`.
```

The numbered answer is the **next prompt**; it is consumed by the guard and
never reaches the model. Anything other than `1`, `2` or `3` asks again.
The pending offer is held per session id under
`$CLAUDE_CONFIG_DIR/lanes/offers/<uuid>`. **An answer is a prompt**, so it
waits behind the duplicate read below: while a second live process carries this
transcript the answer is refused with that, and the offer is kept for the first
prompt after the other process is retired.

**A lane named before Rule 4's `<repo>-<n>` form is offered, not run.** The
register carries 22 of them, and `lane-start` needs such a lane's DIRECTORY,
which nothing in the register, the window or the session says. So the offer
says what it cannot fill in and choice `2` refuses rather than running a command
with a `<path>` placeholder in it: the move is
`lane-start --no-launch --dir <that lane's checkout> <lane>`, yours to run with
the path filled in, and the offer is kept. On choice `2` the **window is renamed first** and that order is
load-bearing: `lane-start`'s step 3b veto 2 refuses to take the session live in
a window named for another lane, which after choice `2` is exactly what this
window is — without the rename `lane-start --bind` refuses, because its binding
mode answers veto 1 and never veto 2.

**And choice `2` runs `lane-start` in its binding mode, `--no-launch --bind
<this uuid>`** (openRepoTools#70). Its step 3b **veto 1** never takes a uuid
that belongs to ANOTHER row (Amendment 11 clause (d) rule 1), and after the
rename this uuid still belongs to the lane being left. Without an answer to that
veto `lane-start` left the person's own transcript out of the new lane's cell —
and the next prompt then found a lane window whose row did not name this
transcript and refused, with a cure that vetoed for the same reason and changed
nothing. A blocking hook that refuses for ever, on a state it created by obeying
the person, is the worst outcome this surface has. So the guard used to append
this uuid to the cell itself after `lane-start` returned — by which time
`lane-start` had taken the destination row's ordinary resume path and stamped
the row, the lane's log and the handoff `RESUMED` by the id **that row already
had**, under `--no-launch`, about a conversation nobody was in. `--bind` is the
person's `2` handed to `lane-start`: it appends this uuid to the cell first,
with its own anchor discipline (the PUBLISHED last id, so a stale copy of the
row refuses rather than writing a cell that no longer matches), and every stamp
it writes names this uuid. The guard makes no register write of its own for it
any more: it reads the cell back and says it ends on this uuid, or prints the
act that puts it there where `lane-start`'s append could not land — a copy of
the row older than the published one, or a cell carrying no uuid and neither
`none recorded` nor the `pending` line. Its old write is not kept behind that as
a fallback, because it used the same anchor and would have refused in exactly
the same places. That is not a hole in veto 1: the veto exists for the take
nobody asked for, and clause (h) rule 4's limit is that the lock never moves a
uuid between rows **without the person saying so** — here they have, in the
answer given at this very prompt.

### The duplicate read (Amendment 18(h))

**A transcript is held by ONE live process.** The harness can fork one
(`--fork-session`, minting a new id in a background pty host) or resume one
twice, and the tooling refuses to build on either. Measured four times on
2026-09-14, the last at 18:14Z: a `bg` record in one profile's `sessions/`
beside the interactive record in ANOTHER's, both live, both carrying one
`sessionId` — so the sweep is of **every** profile's directory, not the asking
session's.

The one live record this read passes over in silence is the harness's own
**companion**: `kind: bg`, beside this window's own record, in the same
profile's `sessions/` (Amendment 8, ruling (g) — *"records that share a
`sessionId` are one session, not a queue of rival holders"*). The **kind** is
what tells it from a second live process, not the profile: one profile can
resume one transcript twice — no move, no swap, one command — and the
interactive record that makes is 18(h)'s own case.

**The companion is the harness's relation, not the asker's.** Where the asking
window carries no record of the transcript at all — `lane-end --retire` typed
in a bare shell or in another lane's window — it is read off the **lane's live
window record** instead: the interactive record in a window tmux resolves and
agrees with, and a `kind: bg` record in that record's profile is its companion
from any window. Only one profile may answer; two live windows in two profiles
on one transcript leave every row `unrelated`, as before (openRepoTools#73).

`lanes-edit.sh transcript-holders <uuid>` is the read, and three surfaces share
it rather than implementing the rule three times:

* **the guard** refuses every prompt in a process that shares its id with
  another live one, naming the pid, its window or `bg`, its profile, and the
  retire act `lane-end <lane> --retire <pid>`;
* **`lane-start`** counts the holders of the id it is about to resume or bind —
  at the window-binding step and in the row-resume branch — and refuses rather
  than appending such an id to the row or launching a second resume of it;
* **`lane-end <lane> --retire <pid>`** retires a duplicate whose id **IS** the
  row's own, which `forks` cannot see because its criterion is "an id the row
  does NOT record". A duplicate is named by its **pid** and never by a uuid —
  its uuid is the row's own and could not pick between the processes carrying
  it; the uuid form is for a fork. The window's own session is refused **by
  name**: retiring the session that IS the lane is how a lane loses the
  conversation it is, and ending the lane is the bare `lane-end <lane>`. The
  harness's own **companion** of that session is refused by name too: it is one
  half of the live session, not a rival to it.

A window is matched by **id AND session name** (Amendment 11(h)'s agreement
rule) because tmux reuses window ids once a window is gone. **A silent tmux is
not a dead window**: where that read comes back empty, a second, untargeted
question (`tmux display-message -p '#{session_name}'`) asks whether a server
answers here at all. Only a window a server that answers does not have is
`(gone)`; one tmux did not answer about is `(no answer)`, and like `(not
asked)` it **protects** the process in it, so `--retire` refuses the lane's own
pid while tmux is down or unreachable rather than handing Amendment 6(d)'s
retitle to the conversation that IS the lane (openRepoTools#73).

### The `SessionStart` block's own line (clause (f))

The `SessionStart` hook gains one line — `session name '<x>' was not the lane
'<y>' — renamed` — and types the rename itself, so the first prompt already
finds the three agreeing. It types over DRIFT only: a name a PERSON set to
another lane's, newer than this window's binding, is clause (h) rule 2's
instruction and this hook leaves it alone, says so, and lets the guard put the
offer at the next prompt — otherwise a rename followed by a `/clear` would be
undone before anyone was asked. It is that hook's **one act on a pane and its only act
of any kind**: it stays read-only against the register and **always exits 0**,
because a hook that fails is a hook that breaks the session it was meant to
orient (R-A8-1). Where another live process carries this session id it **SAYS
so** and names the retire act; the refusal is the guard's.

### Fail closed for lane sessions, and safe-mode recovery

A mismatch refuses, and so does an **indeterminate read** — session records
unreadable, tmux not answering, the register unreadable, a payload naming no
`cwd` — naming the read, because a triple that cannot be verified is not a
triple that agrees (clause (d)). The row is read from `origin/<branch>` as this
checkout last had it (R19) and **the guard never fetches**: this hook runs at
every prompt, and putting the network there would be R-A8-1's objection several
times over.

Profile-only launches with exact `CLAUDE_NO_LANE=1` are exempt, as described
under Scope below. For a session subject to the guard, `claude --safe-mode` runs
with every hook disabled — deliberate, visible in the
prompt box, and **a session started that way is not a lane session**: it may not
write the register or claim an object. The hook's `timeout 5` is the other way
it can fail open, and it is the amendment's own number: a hook that exceeds its
timeout is killed, and a killed hook does not exit 2.

### Scope

Sessions launched with `CLAUDE_NO_LANE=1` skip the prompt guard, silently. The
workBenches profile launcher supplies this marker for a launch without a lane
and clears it for an explicit lane launch. Explicit `--no-lane` wins over lane
options. Other marker values keep the normal checks. The hook stays installed
in profile settings so a concurrent lane session using the same profile still
receives enforcement. Existing sessions need a relaunch to change launch mode.

The guard applies to every session whose `cwd` is under `$PROJECTS_ROOT`
(default `~/projects`), which is every estate session, and is **silent
elsewhere** (D1): a name there is a title and nothing more. A **subagent's**
prompt — hook input carrying `agent_id` — is exempt, because subagents do not
submit prompts and the lane that runs them has already passed.

Installing it is `openRepoTools --install`'s: the entry
`~/projects/xFactory/lanes-edit.sh guard`, timeout 5, under
`hooks.UserPromptSubmit` in `~/.claude/settings.json`, beside the `SessionStart`
one. It carries **no `|| true`** — on `SessionStart` that suffix is the whole
safety property, and here it would be the opposite of the mechanism, since
`cmd || true` exits 0 for every code the command can produce and a guard that
refuses nothing silently is the exact state this amendment exists to end.

## The row is current state; the log is history (Amendment 13)

**In force — ratified by Brett Heap 2026-09-13T21:08:36Z, verbatim "Ratify as
drafted (Recommended)".** The `state` column was defined as a cell and used as
a diary. Measured the day the amendment was drafted: the register was
1,141,283 bytes over 46 rows, its longest row 96,228 characters, and one lane's
cell 7,016 characters after a single day of appending — three different things
in one column (the lane's CURRENT state, which Rule 6 reads; a running
narrative; and rulings quoted verbatim), appended forever with ` · `, never
replaced, in a table nobody can read in the table.

### The cell is one phrase, and every write replaces it

```text
<STATE> · <UTC> · <one line>
```

`STATE` is one of `LIVE`, `PAUSED`, `LANDING #<n>`, `LANDED`, `ENDED`,
`RETIRED`, `HANDED OFF`. **Rule 6 is unchanged** and is now the whole of what
the cell says at that moment. The line is at most **240 characters** (ratified
decision O1) and carries neither a `|` (it would forge a cell boundary) nor a
second ` · ` (it would read back as a fourth part of the phrase).

```sh
lanes-edit.sh set-row-state my-lane "LANDING #123 · CI green; waiting on review"
```

The UTC is stamped by the writer, never passed in. `append-row-status` is
**RETIRED** and refuses, naming this and the two verbs below: the cell can
never grow again because the act that grew it is gone, not because every caller
remembered.

**Which text is the state cell** is decided by splitting the row on ` | `, and
a seven-column row has exactly six of those separators. A row with more has a
` | ` inside one of its cells, and the two readings of it — count six from the
left, take the last cell from the right — disagree about which text to
overwrite, so **that row is refused by name** rather than written at a guess.
Measured on the live register 2026-09-15 (52 rows): 50 split cleanly, and the
two that do not are cured by escaping the literal pipe as `\|`, the way a
Markdown table carries one, in a hand edit wrapped by `lanes-edit.sh commit`.
A bare `|` that is *not* spelled ` | ` is harmless and stays inside its cell.

### The narrative goes to the lane's own log

Two verbs join Amendment 7's, in the same file, with the same grammar:

```text
NOTED — lane <lane>, session <uuid>@<ws>, <UTC>, lane:<lane> — <what the lane did, found, launched, left>
RULED — lane <lane>, session <uuid>@<ws>, <UTC>, lane:<lane>[ → <object>] — <Brett Heap's words, verbatim>
```

```sh
LANES_LANE=my-lane lanes-edit.sh log NOTED lane:my-lane "rebased on main; the flake is the fixture's clock"
LANES_LANE=my-lane lanes-edit.sh log RULED lane:my-lane → opensoft/openRepoTools#29 "Ratify as drafted (Recommended)"
lanes-edit.sh history my-lane [--since <UTC>]
lanes-edit.sh history --all [--since <UTC>]          # every lane, on one timeline (Amendment 14)
lanes-edit.sh history --repo <owner/repo>           # the lanes whose home it is
```

`NOTED` takes no payload; `RULED` takes one, the object it bears on, and that
object is canonicalised through `repos.tsv` like every other object this
tooling writes. **Neither is a transition of anything**, so neither ever moves
a lane's state: `who`, `lane-end`, Amendment 11's `lanes` and `swapped` skip
them exactly as they skip `STARTED` and `RESUMED`. `history` prints them in
**file order**, which is the order the lane wrote them (R14), and exits 8 for a
lane that has written none.

### The migration — one act, on Brett Heap's word

```sh
lanes-edit.sh migrate-state-cells            # DRY RUN: reads everything, writes nothing
lanes-edit.sh migrate-state-cells --yes      # the one act
```

Each row's cell is split on ` · ` into entries, oldest first. Each becomes one
log line: `RULED` where the entry begins with `RULING`, `RULINGS` or
`RATIFIED`, else `NOTED`; the entry's own leading timestamp becomes the line's
UTC (a row's `started` where it carries none), the session is the **last uuid**
of the row's session cell, and the workstation is the first `/`-separated part
of the row's own column. The cell is then replaced by the state the **last**
entry's leading verb names, else by `MIGRATED · <UTC> · history in
lanes/log/<lane>.md`.

**One commit** carries the archive `lanes/archive/LANES-pre-amendment-13-<UTC>.md`,
every log it appended to, and the register — so the narrative exists three
times over (git history, the archive, the logs), which is the posture
Amendment 3 took after the 2026-09-08 loss. It **refuses** when no cell holds a
` · ` entry it can migrate, so a second run is a no-op that says so.

**On `mode=primary`** that one commit is one register transaction, refused or
whole, and the renderer writes the archive, the logs and the register to the
files after it. See "The SQL register".

A row it cannot take is **skipped and named**, never half-written, and its cell
keeps every character so that a re-run after the row is settled migrates
exactly it. Five reasons: the row is ambiguous (above); its session cell holds
no transcript uuid, so the line could only name `unknown` in the one field an
append-only log must never carry it in; two rows differ only by case
(Amendment 15(d)); the lane's log is two files or is published under a spelling
this checkout does not have; or the first cell is not a lane name.

## Renaming a lane (Amendment 16)

**In force 2026-09-14T09:24:35Z**, ratified verbatim *"Ratify as drafted
(Recommended)"* on Brett Heap's request of the same day, verbatim: *"we need the
ability to rename a lane. maybe lanes --rename <current lane name> <new lane
name>."* **A LANE IS RENAMED BY ONE WORD, IN ONE COMMIT, AND ITS OLD NAME KEEPS
RESOLVING FOR EVER.** (On `mode=primary` the commit is a register transaction
and a handoff commit: see "What one command does".)

Why it exists: on 2026-09-14 two lanes renamed themselves **by hand**, each as
one `RENAMED` line appended to `LANES.md`. The lines are honest and they are all
there is — the rows still carried the old keys, the logs and the handoffs the old
names, and a reader of either lane's history had to know the rename to follow it.

```sh
lane-rename openxfactory-4-opendox-extraction openxfactory-4 "Brett Heap's word: shorten it"
lane-rename hermes-wallet-exercise codeXfactory-2 --no-github
lanes --rename a b        # REFUSED, and it names `lane-rename`
```

The first two lines above are the amendment's own **adoption act**, and they
have not been run: clause (i) says the two hand renames of 2026-09-14 are *regularised by the
adoption act, not re-done* — their rows, logs and handoffs moved by this command
**on Brett Heap's word**, with their existing `RENAMED` lines in `LANES.md` left
exactly where they are.

### What one command does

| # | clause | the move |
|---|---|---|
| 1 | (b) | the register row's key cell becomes `` `<new>` ``, and the text beside it gains `` *(ex `<old>`, renamed <UTC>)* `` — the form the register already uses for a lane named later than it started. Nothing else in the row changes; **the session cell keeps its history** (Amendment 6(b)) |
| 2 | (c) | `lanes/log/<old>.md` → `lanes/log/<new>.md`, plus one line: `RENAMED — lane <new>, session <uuid>@<ws>, <UTC>, lane:<new> ← lane:<old> — <why>` |
| 3 | (d) | the handoff the row names → the same date with `lane-<new>`, plus a Rule 3 stamp under its header block: `RENAMED from <old> by <uuid> (lane <new>) at <UTC>`. The row's handoff column follows. A handoff not named `…-lane-<old>.md` has no lane in its name to rename, so it is **stamped where it is and not moved**: the rename is still committed, the row's handoff column still names that file, and `rename-lane` (the verb `lane-rename` runs) prints the note `` the handoff <cell> is not named `…-lane-<old>.md`, so it has no lane in its name to rename; it is stamped where it is (Amendment 16(d)) `` (`<cell>` is the column's value). |
| 4 | (e) | `lanes/aliases.tsv` gains one TAB-separated record, `<old><TAB><new><TAB><UTC>` |
| 5 | (g) | **after** the commit lands, one GitHub comment per object the lane HOLDS, citing its sha — the same partition `who --lane` reports and `lane-end` refuses on. `--no-github` skips it |
| 6 | (f) | the tmux window is renamed, and `/rename <new>` is typed into the lane's own pane (Amendment 12(h)'s M1 — the only path a running session's name has) |

**The first four are ONE commit, refused or whole.** Any two of them apart is a
state no reader can read: a row under `<new>` whose log is still `<old>.md` is a
lane whose history `who --lane` cannot find, and an alias without the row it
points at is a name that resolves to nothing.

**On `mode=primary` the first four are not one commit.** Moves 1, 2 and 4, the
row, the log and the alias record, are one register transaction, refused or
whole, which the renderer writes to the files later. Move 3, the handoff's move
and stamp, is then a git commit of the lane's own under `handoffs/`, made after
that transaction commits; and move 5's comments cite `register seq <n>` where
they cited a sha. See "The SQL register".

### The refusals — clause (a), before anything is written

| the state | why |
|---|---|
| no row under `<old>` in any case, and none through an alias | a rename moves a row that exists; `lanes --all` lists the ones that do |
| a row under `<new>` in any case | two rows for one name is what every writer here refuses (Amendment 15(a)) |
| `<old>` and `<new>` are one name under any case | that is not a rename but a change to the row's own SPELLING, which every reader already resolves to — Amendment 15(d)'s hand act |
| `<new>` is not `<repo>-<n>` and no `--verbatim` | Rule 4's form is what `lanes --prefix`, the next free position and `lane-start <repo> <n>` are computed from |
| a **LIVE** session holds `<old>` in another window | its own name is locked to the lane and only it can change that (Amendment 12(h)); renaming from elsewhere would leave a running conversation named for a lane the register no longer has |
| two rows under `<old>` differing only by case | there is no ONE row to rename (Amendment 15(d) merges them first) |
| the alias table would gain a **cycle** | a chain that returns to its own start has no end to resolve to |
| `<new>` is already an alias **key** | a row wins over an alias, so a row under a name some other lane was renamed away from would END that lane's old name resolving |
| `lanes/log/<new>.md` exists in **any case**, here or on `origin` | one lane's history appended to another's is the one thing an append-only log cannot be walked back from |
| the row's handoff is **outside the workspace repository** | a commit cannot carry a file outside its own checkout, so the rename would be three moves and a note |
| an unreadable register, log, handoff or alias table | fail closed, naming the read — an alias table that cannot be READ is not an estate with no renames |

Each of them says **"Nothing was written."** The command moves four files, and a
half-done rename is not something a second run can finish.

And a filesystem can still refuse the third of four moves. Every write between
the first byte and the commit is made against a **snapshot** taken before it, and
every exit path — a `die`, a signal, the shell — restores it: the log, the
handoff and `lanes/aliases.tsv` go back to what they were and nothing is
committed. The register is not in the snapshot and does not need to be:
`replace_line` proves the new file before it writes a byte of `LANES.md`.

### The alias table, and the one seat every reader shares

`lanes/aliases.tsv` lives beside the register — `<old><TAB><new><TAB><UTC>`,
comments on `#` lines — and `lane-rename` is its only writer. It has **one
layer**, unlike `repos.tsv`: a repository alias is an organisation's fact and a
lane rename is one person's register moving, so openRepoTools ships none.

Clause (e) lists the readers that resolve through it — `who`, `lanes`,
`live-holder`, `history`, Rule 6 attribution, `lane-start`'s row lookup, the
`SessionStart` block and Amendment 12's guard — and **that list is not built as a
list.** Amendment 15 already put `canon_lane` in front of every one of them, so
the resolution is hooked there, once:

* **a name with no row** is looked up in the table, case-insensitively, and the
  chain (`a→b`, `b→c`) is walked to its end — stopping at **the first name along
  it that has a row**, because **a row wins over an alias at every hop**, not only
  at the start. That is what makes renaming a lane back to an old name readable,
  and it is what keeps a lane legitimately minted under a freed name (`a→b` frees
  `a`, and the next free position hands it out) as its own lane rather than a
  second reading of `b`. The walk is bounded by the table's own size, never by a
  fixed number of hops: a chain longer than the cap would otherwise resolve to an
  intermediate name and clause (e)'s "for ever" would quietly end there;
* **the lane field of every old log line** is resolved *on the way in*, in
  `LOG_AWK`. Clause (c) is explicit that the lines above the `RENAMED` are never
  rewritten, so a renamed lane's log holds its history under two names in one
  file; read byte for byte, half of that lane's holds would be invisible to
  `who --lane` and to `lane-end`'s refusal. One rule in the parse serves every
  reader behind it;
* **the tmux window's name.** A rename run from another window leaves that
  window carrying the old name — and the name guard resolves it, **renames the
  window itself** (one tmux call, unlike a session name), and types the
  `/rename`. So a rename made from anywhere is finished by the lane at its next
  prompt, with nobody typing anything.

The old name resolves **for ever**: nothing in this toolset ever removes a row
from that table.

### `RENAMED` is a lane-kind verb that changes no state

It joins Amendment 7's verb list as a lane verb — its object is `lane:<name>` —
and it is outside the last-line rule **by construction**: every state read here
enumerates the five verbs that do change state (`STARTED PAUSED RESUMED ENDED
RETIRED`), so a lane's last `STARTED` or `PAUSED` is still its last one after a
rename, which is what decides whether it is running, swapped or closed. It is not
written by hand: `lanes-edit.sh log RENAMED …` is refused and names `lane-rename`,
because a line on its own is exactly the 2026-09-14 hand rename this amendment
replaces.

### `lanes` stays read-only

Amendment 11(j)'s *"it writes nothing"* is untouched (clause (h)). `lanes
--rename` is refused with the one word to type — the flag is answered rather than
left unknown, because the request that produced this amendment spelled it that
way.


## The handoff (Amendment 17)

**In force 2026-09-14T09:45:33Z** (revision 2, `/ctx` included), with Amendment
18(d)'s `--exit` and Addendum 2's respawn line. **A HANDOFF IS THE SWAP, UNDER
ONE NAME; A LANE RESUMES FROM ANY AGENT THAT CAN READ IT; AND `/ctx` IS THE
WHOLE ACT IN ONE WORD.**

### One act, three names

| where you are | the word |
|---|---|
| in the lane's session | `/handoff [why]` — and `/swap` and `/lane-swap` are its aliases, kept so that nothing written about them stops working |
| in a shell in the lane's window | `lane-handoff [why]`, placed on `PATH` by `openRepoTools --install` |
| clearing the context | `/ctx`, which is `/handoff --restart` |

The steps are Amendment 8(a)'s and Amendment 11's, unchanged: the identity
triple fixed, the handoff file refreshed with a fresh Rule 3 top block, the
writers polled, `PAUSED` written, the restart line printed. What differs between
a context clear, a usage reset, a profile switch and handing the lane to someone
else is only **why**, and the record's free text has always carried that. The
word was wrong, not the act.

The skill `skills/handoff/SKILL.md` is the single source of the act; the files at
`skills/lane-swap/SKILL.md`, `commands/handoff.md`, `commands/ctx.md` and
`commands/swap.md` name it and add nothing — two copies of one procedure that
must stay byte-equal is the alternative the amendment rejects by name.

**On `mode=primary`** the handoff file is still written when the register cannot
be read: the reads that name the lane read the mirror, labelled, and the
`PAUSED` record's writes exit 69 and print a note, so that the lane stays
`SWAPPING` until `lane-handoff --late --at <UTC> --lane <lane>` completes it. See
"The SQL register".

### The record's two new sub-fields

The `PAUSED` payload gains two beside Amendment 11(c)'s `window`, `dir` and
`profile`:

```text
PAUSED — lane repoHF-1, session a17a0001-…@Eagle, 2026-09-14T17:05:11Z, lane:repoHF-1 → swap; window hfsess:0 @21; dir ~/projects/repoHF; profile team-05a; workstation Eagle; agent claude; transcript a17a0001-… — clear
```

- **`agent <name>`** — `claude`, `codex`, or the launcher's name for whatever
  wrote the line. A **manifest key**: letters, digits, `.`, `_`, `-`. The
  pattern is also how this sub-field gets the `; ` rule without a token of its
  own, and `lane-start --agent` reads it back to choose a launcher.
- **`transcript <id|none>`** — the agent's own resumable id where it has one.
  `none` is an **answer**, not a gap.

And the tooling writes a third beside them, `kind <in-process|respawn|unknown>`,
because it is the one fact about the act that FOLLOWS the record and no later
reader can infer it: **an in-process clear does not kill this lane's writers**
(**Amendment 17 Addendum 1**, clauses (h)–(k), in force 2026-09-14T20:59:31Z).
Measured here on 2026-09-14 — a harness `/clear` mints a new transcript id in the
SAME process, so every subagent survives it and only its in-flight tool calls die
(a `Bash` killed that way exits 137); the handoff written a minute earlier said
*relaunch every writer below*, and the live count a minute later showed all five
alive on their worktrees.

| kind | what happened to the process | written by |
|---|---|---|
| `in-process` | it keeps running, and every writer with it | `lane-handoff --in-process` |
| `respawn` | it is replaced or ended — the pane respawned, the session exited, a profile switch, a usage-reset relaunch | `--restart`, `--exit`, `--late` |
| `unknown` | not this command's to know: a plain handoff may be followed by a `/clear` or by a relaunch | a plain `lane-handoff` |

The same word is in the line's FREE TEXT after the why — `clear in-process`,
`clear respawn`, `kind unknown` (clause (h)) — so the person reading the record
reads it too. **THE KIND SAYS WHAT TO EXPECT AND NEVER WHAT TO DO** (j). What
says what to do is clause (i), and it is the same in every kind: the `WRITERS`
section is **a list to COUNT, not a list to relaunch** — `ListAgents` in Claude,
the agent's equivalent elsewhere; a writer still live OWNS its worktree and is
sent one message rather than relaunched; only a writer that is NOT live is
relaunched, from where it stands. Where the count and the block disagree, **the
count wins**: the block is what the paused session expected and the count is what
is true. And one worktree is one writer's for as long as that writer is live (k).

**The writer validates both** (`pause_subfields_check`, called from
`write_event`) and refuses the line rather than writing a sub-field no reader can
use: this log is append-only, and `lane-start` launches on what it reads back.

Three reads answer for them, all read-only: `lanes-edit.sh lane-agent <lane>`,
`lanes-edit.sh lane-transcript <lane>` and `lanes-edit.sh lane-last <lane>` (the
lane's last lane-kind line as `<verb>	<utc>	<session>	<payload>`). `swapped`
gains a sixth and a seventh field for the same two:

```console
$ lanes-edit.sh swapped Eagle
repoHF-1	2026-09-14T17:05:11Z	hfsess:0 @21	~/projects/repoHF	team-05a	claude	a17a0001-…
repoSW-1	2026-09-12T10:00:00Z	claude-team-05b-20260912102132-2699:0
```

The second row is every record written before an amendment: the fields are
**empty rather than guessed**, and the two reads answer **8**. Nothing is
backfilled (Amendment 7(i)); each lane cuts over at its own next handoff. **The
first field before the first tab is still the lane**, which is the contract every
reader of this output takes and what makes a sixth and a seventh safe to add.

### The launcher table — `lane-start --agent <name>`

`lane-start <repo> <n>` reads the row and the last `PAUSED`. With no `--agent`
the default is **the agent that paused**; with no `PAUSED`, `claude`.

| agent | what is launched |
|---|---|
| `claude`, its transcript here | `claude --name <lane> --resume <id>` — Amendment 6's case (a), unchanged |
| `claude`, no transcript here (or `--agent claude` named, or `LANE_START_FRESH=1`) | `claude --name <lane> --session-id <fresh uuid> "<the handoff's top block>"` |
| `codex` | `codex "<the handoff's top block>"`; its resumable id is read back where it prints one |
| anything else | **a refusal naming the two it knows** — a lane is resumed by an agent whose launch has been taught and tested, never by a guessed argv |

**The top block is the head of the handoff down to its first `---` rule** (200
lines at the outside), and it is delivered **after** the Rule 3 stamp is written,
so the session reads a prompt that already carries the record of its own resume.
The stamp names the agent where the agent is not Claude — `RESUMED by codex
<id> (lane <lane>) at <UTC>` — and the row's session cell is appended in that
agent's own spelling, `harness <uuid>` / `Codex <id>` / `<agent> <id>`, which is
how the register has spelled Codex sessions since 2026-09-05.

### `/ctx` — one word, and everything after it is automatic

`/ctx` (`/handoff --restart`) performs the handoff and then **restarts in
place**: `tmux respawn-pane -k` on the lane's own pane, and what comes up is a
NEW session of the same agent whose **first prompt is the handoff's top block**.
What tmux starts is not that session but a SUPERVISOR that launches it — the two
subsections below are why, and they are `openRepoTools#94` — so the sentence
holds end to end and no longer holds only as far as tmux accepting a command.
That block's
`WRITERS` section lists every worktree the lane had running: its branch, its last
commit, what it was holding, and the brief it was given, so the new session finds
them rather than discovering them — and its first line is Addendum 1 (i)'s, the
COUNT. **`/ctx` says which it did** (j): this one respawns, so the record says
`kind respawn` and the count then finds none, which is what makes relaunching
each one right. A `/ctx` that clears IN PLACE is the other kind and must say so
(`--in-process`, `kind in-process`): the process survives, its writers survive
with it, and the block says to EXPECT every writer below live.

**The record comes first, always, and the record is FOUR writes** (it was three
until `openRepoTools#94`). A `/ctx` **refuses before it kills anything** where the
`PAUSED` line did not land, where the row was not flipped (the register would say
RUNNING about a session that has just been replaced), where the handoff could not
be refreshed (its top block is literally the new session's first prompt, and a
stale one hands over the instructions of another act), **or where the restart
intent could not be written and read back**. A pane is never respawned over an
unrecorded lane, and a pane respawned with no intent is a pane whose supervisor
has nothing to launch from — the same unrecorded restart one step along.

#### The restart intent — what makes the new session a FRESH one

`opensoft/openRepoTools#94`, measured on Eagle at 2026-09-15T20:59Z: `/ctx` wrote
its record, respawned its own pane with `LANE_START_FRESH=1 … lane <lane>`, and
what came up was `claude --name <lane> --resume <the uuid it had just paused>` —
the same conversation, not a fresh one. `lane-start` was right (both of its
`(( ! fresh ))` gates were in the installed copy), `lane` was right (its available
branch ends in a plain `exec`), and the variable simply never arrived. The
launcher re-creates its child through tmux, and **a command tmux starts gets the
tmux SERVER's environment, not the caller's** — `claude-profile`'s own code says
so, which is why it writes six values into the command string explicitly. The
seventh was never written, so it was never there.

An environment cannot cross a boundary another repository owns. **A file can.**
Before anything is killed, `/ctx` writes a RESTART INTENT under the lane's own
legacy control root, independently of lifecycle snapshots,
using an explicit `$LANES_LANE_STATE_ROOT` when configured. Otherwise, an existing
intent under the recorded checkout's parent or `$PROJECTS_ROOT/.lane-state/<lane>`
retains its location through reservation, preservation, cleanup and later reads.
Distinct occupied intent locations or unreadable candidate ancestry refuse.
Aliases of the same control-root directory count once; separate leaf symlink
or hardlink entries refuse. With no existing intent, the recorded-parent rung precedes
the projects-root fallback, with a checkout hint only when neither answers.
Root selection shares the writer mutex with CAS. Recording a nested checkout
cannot move this operation; PR #97 diagnostics retain their independent root
selection and may reside elsewhere:

```
$ lanes-edit.sh restart-intent openRepoTools-3
state            pending
generation       7
operation        ctx-20260915T210412Z-41233-1187
mode             fresh-from-handoff
agent            claude
profile          max-001
dir              /…/openRepoTools
pane             claude-…:@6.%6
handoff          /…/handoffs/openRepoTools/…md
digest           9f2c…
old_transcript   4135b2c9-…
new_transcript   none
attempt          0
```

It carries no credential and there is no field for one. `lane-start` reads it back
and **the intent is the authority**: with a `pending` or `starting` intent whose
mode is `fresh-from-handoff`, the launch is a new session named for the lane and
primed by the handoff's top block, whatever the row's last uuid or the lane's last
`PAUSED` record name and whether or not any environment variable survived. A
`ready` intent is history and authorises nothing, which is what makes the next
`lane <name>` an ordinary resume. `LANE_START_FRESH=1` still works and is no longer
the authority; `lane-start --fresh` is the same thing as an argument, for a caller
with no launcher between it and there.

Two refusals rather than substitutions: an intent naming another checkout, or an
`--operation` that is not the lane's current one, is a refusal that renames
nothing and starts nothing — and a handoff whose **digest** has changed since the
intent was written blocks the launch, because that file's top block IS the first
prompt.

#### Legacy compatibility and preparation recovery

Supervised manual `/ctx` is a legacy compatibility path for Claude. Readiness
uses trusted Claude-native interactive session records for agent provenance.
Before replacing the old pane, the backend verifies the complete pending launch,
an existing absolute checkout and the canonical handoff checksum. Equivalent
handoff symlink paths retain their filesystem identity. Intent records reject
unknown keys, malformed lines and control characters in launch facts; those facts
are never flattened into different paths. Lane rename refuses unfinished or
failed restart ownership under the shared writer mutex. Observer cleanup uses
private cancellation and owned-child waiting, never a saved numeric PID signal.

Supervised manual `/ctx` is a legacy compatibility path. Run
`lanes-edit.sh legacy-restart-check <lane>` before any preservation writes;
managed lanes and unreadable managed ownership refuse. The check uses feature
001's installed read-only `legacy-check`, or conservatively refuses any matching
managed storage when that reader is absent. It never edits JSON ownership.

The backend reserves `preparing` before changing a handoff, register, log or
window. A normal error or catchable signal changes only that exact reservation
to `failed`; it never respawns the old pane. If an uncatchable termination leaves
`preparing`, first verify the original pane/session is still alive and no
replacement was launched. Read operation, generation and attempt with
`lanes-edit.sh restart-intent <lane>`, then reconcile that exact reservation:

```sh
lanes-edit.sh set-restart-intent <lane> failed --expect preparing \
  --expect-operation <operation> --expect-generation <generation> \
  --expect-attempt <attempt> --reason 'preparation abandoned; old pane confirmed alive'
```

A new `/ctx` can then refresh the preservation record. This command authorizes
no child launch from a partially prepared record.

If preparation failed before `pending`, the failed intent keeps mode
`preservation-only`, attempt 0 and no new transcript. It is not a failed launch
and cannot be given to `--supervise`. Once every holder is confirmed absent,
`lane <name>` may use ordinary resume without restart operation/attempt tokens or fresh-session selectors.
It rechecks the same failed preparation before binding changes and before
launch, leaves the intent unchanged, and retains the normal binding safeguards.
A live or unknown holder still refuses. Failed `fresh-from-handoff` launches
remain restricted to their same-operation supervisor.

The first launch reserves `new_transcript` only while it is `none`. Every later
write expects its exact operation, generation, attempt and transcript. The
verified Rule 3 stamp is persisted with both `digest` and `prepared_digest`
before `lanes-edit.sh publish-handoff` checks the expected digest and exact
attempt under the workspace writer mutex, then atomically replaces the resolved
handoff target. Both backend refresh and resume stamps use this publisher; manual
semantic edits are staged in temporary files and use the same command. Interruption leaves
either complete version valid for retry; unrelated changed prose refuses.
Historical `ready` intents do not control an ordinary resume.

#### The supervisor — what tmux actually starts

The pane is respawned with `lane-handoff --supervise --lane <lane> --operation
<id>`, **by absolute path**, because a respawned pane's `PATH` is whatever the
person's shell profile makes of it. It is a MODE of a command that is already
installed and not one more word: Amendment 18 Addendum 2 (i-8) is explicit
that *"no word is kept on `PATH` for it alone"*, which is the ground on which
`restart` was taken off `PATH` in the first place.

The legacy supervisor requires both the recorded pane and current TMUX_PANE to
be known and agree before claiming an attempt. A respawn forwards the configured
control/protocol roots and the launcher's LANES_HOST, LANES_OS and
LANES_CONTAINER identity alongside LANES_WORKSTATION. An existing unusable
control-root directory or ancestor is a read failure; only genuinely absent
storage permits an ordinary first launch. Readiness checks the child process
state and refuses an exited child awaiting reap.

What the supervisor does, in order:

1. **claims** the intent — `pending`/`failed` → `starting`, compare-and-swap on
   the operation, legacy generation and attempt, so a stale supervisor writes nothing;
2. refuses before launching anything on a stale operation, a `starting` or
   `ready` one, a pane the intent does not name, a changed handoff digest, or a
   live holder of the lane (Amendment 18(h): a second session of one lane is the
   collision the whole protocol is about — and the act it prints is
   `lane-end <lane> --retire <pid>`, never a kill);
3. **runs the launch as its CHILD** — `pclaude --lane <lane> <profile>`, which is
   (i-8)'s second door, falling to `lane-start --fresh --operation <id>` where
   there is no launcher or the record names no profile. Never `lane <name>`: that
   is the human dispatcher, and attaching to a live session is one of its valid
   outcomes;
4. **confirms readiness** from a read-only predicate — the child alive, exactly
   one live holder of this lane, its transcript the new one and not the paused
   one, in the pane the intent names — and only then marks the legacy intent `ready`.
   It never uses PR #97 diagnostic lifecycle state to decide restart readiness or writes managed JSON state. Ordinary STARTED/RESUMED events may update diagnostics independently. Tmux accepting a command is **not** a started session, and nothing
   here treats it as one;
5. **stays in the pane**, whatever happens. Amendment 11's own invariant is *"No
   path the launcher opened exits the pane"*, and `/ctx` was the one path that
   did: what tmux used to start was the launch itself, so a launch that failed
   took the pane with it.

#### The retry surface

A launch that never reaches readiness leaves the intent `failed` with a bounded
reason, and the supervisor prints the stage, the fact that the handoff is intact,
and two lines — retry and read:

```
RESTART FAILED — lane openRepoTools-3, operation ctx-20260915T210412Z-41233-1187
  the launch ended with status 127 before readiness was confirmed

  retry:   lane-handoff --supervise --lane openRepoTools-3 --operation ctx-…
  read it: lane-handoff --restart-status --lane openRepoTools-3
```

Where there is a terminal it then asks once — `[r = retry, q = leave it]` — and a
retry is **the same operation**: the same generation, digest, directory, profile
and launch mode, with the attempt count incremented. Where there is no terminal
it exits 3 — or **4**, where the launch ran past the readiness deadline still
alive and only then ended, which the record says as well.

**A signal reconciles only a successfully claimed attempt.** Before claim,
`SIGTERM`/`SIGHUP` leaves the intent unchanged. After claim, a live or unknown
child leaves `starting` with `INDETERMINATE` and blocks retry; a proven ended
child may leave `failed`. Neither handler kills the child. Failure after the
launcher exits also requires an affirmative empty all-holder read before retry
is offered, because a descendant may still own the lane.

Ctrl-C in that pane belongs to the **session**, not to the supervisor: the
supervisor ignores `SIGINT` so that interrupting Claude cannot tear the pane down
under it, and the launch is started with `INT` and `QUIT` put back to their
defaults so the keystroke reaches the session itself. A deadline that expires with the child still ALIVE is a
third answer, `INDETERMINATE`: nothing is killed (Amendment 8(f) — ending
somebody's process is not a boundary script's act), nothing is retried, the lane
is not marked running, and the reason goes into the record, because an interactive
child owns the pane's screen.

`lane-handoff --restart-status --lane <lane>` reads all of it from any window —
the lane, the intent and its attempt, the operation and generation, the mode, the
agent and profile, the checkout, the pane, the handoff and its digest, both
transcripts, the bounded failure reason, and what act is open on it.

**A second `/ctx` never supersedes a restart in flight.** With the intent
`preparing`, `pending` or `starting`, an ordinary `/ctx` refuses before preservation writes and the kill and names
the status command. Taking over an abandoned operation is an explicit act and not
an automatic one.

`/handoff --exit requested by <uuid>@<host>/<container>` is the other end
(Amendment 18(d)): after the record, `/exit` is typed into this lane's own pane
and the session **ends**, because a handoff to another place is a handoff and
not a restart.

### `--late` — the record a swap never left

A session that died at a usage limit wrote no `PAUSED`, and the lane's log still
reads as running. `lane-handoff --late --at <UTC> [why]` writes that record after
the fact — and **it is written from a shell BEFORE the relaunch**:

```sh
lane-handoff --late --at 2026-09-14T12:02:27Z "late; usage limit hit before the swap"
lclaude <profile>      # and only then; use pclaude --lane <lane> if lclaude is unavailable
```

**The rule, and it is the whole of why `--at` is required.** A lane's state is
its log's last lane-kind line **in file order** (R14), and the log is
append-only. A late `PAUSED` appended after the `RESUMED` that `lane-start`
writes at the relaunch would make a lane that is **running** read as paused, in a
file nothing rewrites. So a late line is **dated by its own field** — the moment
the old session ended, which the caller names — and it is written only where the
lane's last lane-kind line is a `STARTED` or `RESUMED` **older** than that
instant. A last line that is already a `PAUSED`, an `ENDED`/`RETIRED`, or a
`STARTED`/`RESUMED` at or after it is a **refusal that writes nothing** and names
the act to take instead. Run from inside the session that has already been
resumed, `--late` refuses: the honest record of the state that session holds is
its own next `/handoff`.

### The transcript follows the lane

`lane-start`'s resume case (a) looked for `<the row's last id>.jsonl` only under
the **current profile's** projects directory, so a lane restarted under a new
profile after a usage limit never resumed its conversation.

**Measured first, and the fact shrank the act** (2026-09-14T12:35Z): on a
launcher-configured workstation every profile's `projects/` is ONE shared
directory — `readlink -f ~/.claude-profiles/profiles/<org>/<team>/<any>/projects`
→ `~/.claude-profiles/state/opensoft/projects`. So a transcript needs **no move**
to be resumed under another profile. `lane-start` therefore **says where it found
the transcript**, and says when that directory is shared:

```text
lane-start: transcript a17a…f3 found in this profile's projects directory
(~/.claude/projects), which IS the directory profile(s) t3 read too (one
shared directory: a profile switch resumes this lane by id and moves nothing)
```

Where the two profiles' `projects/` really are two directories — a workstation
without the launcher's layout — the transcript is **moved** into this profile's
(the `<uuid>.jsonl` and its sibling `<uuid>/` directory), a pointer
`<uuid>.jsonl.moved-to-<profile>-<UTC>` is left where it was, and the move is
said on one line. A **copy** would leave two live transcripts of one uuid to
diverge, and nothing merges them.

**A uuid whose holder is LIVE in the other profile is a refusal, never a move**
(Amendment 18(h): a transcript is held by ONE live process). The refusal names
the pid, where that process is (its window, or `bg`), the profile it is under,
and the retire act — `lane-end <lane> --retire <pid>` — and nothing is moved and
nothing is launched.

## A closed or dormant lane leaves the listing (Amendment 19)

**In force — ratified by Brett Heap 2026-09-14T13:46:16Z, verbatim *"ratify
19"*** (`brettheap/new-workstation#36`, text `#37`). It amends what `lanes`
lists (11(j), 18(i)), gives Amendment 6(d)'s retire act a **sweep** for rows
that predate the object log, and uses Amendment 13's `RETIRED` as a row's
current state.

It exists because of a measurement. On 2026-09-14 `lanes` inside the
openxFactory checkout listed **nineteen rows for five lanes**: the five live
under `team-01b`, and **fourteen marked `NO LOG`** — lanes that ran between
2026-08-27 and 2026-09-02, before Amendment 7 gave every lane a log, and that
were never ended under any rule that could end them. Their rows carry their own
last words (`ended`, `dormant`, `CLOSED 2026-09-04`, one `LIVE` that is not, and
five saying `ENDED WITHOUT PUSHING — loss risk`, `work unpushed` or `owes …`),
and **no writer today can close them**: `lane-end` wants a live session or a log
to write into, and they have neither.

**On `mode=primary`** `lanes` and `lanes --closed` read the register, and the
mirror, labelled, only where the register cannot answer.
`lane-end --retire-dormant` acts on the listing through `lanes --decide`, a
deciding read that refuses with 69 rather than act on the mirror, and the
sweep's `retire-rows` and the archive's `archive-rows` are each one register
transaction where this section says one commit, which the renderer writes to
the files later. See "The SQL register".

### The two states below PARKED

| state | what it is | how it is read |
|---|---|---|
| **CLOSED** | the lane's own log has finished | the **last lane-kind line** of `lanes/log/<lane>.md` is `ENDED` or `RETIRED` |
| **DORMANT** | a row with **no object log** and no live session on this workstation | no lane-kind line anywhere in the logs for that name, and no live session record naming any of the row's transcript ids |

Neither is **bound** (18(b)) and neither is **available to bind** (18(i)): a
closed lane's name is finished, and a dormant row's is not known to be. Both
already left the numbered pick; this is what takes them out of the read.

**One row with no log is NOT dormant:** one whose state cell is Amendment
13(a)'s phrase and whose state word is something other than `ENDED`, `RETIRED`
or the migration's `MIGRATED`. The row says the lane is somewhere — `PAUSED`,
`LANDING #<n>`, `HANDED OFF` — and hiding it would hide a lane a person can
still pick up.

**Its `STATE` is that word with `(row)` after it** — `PAUSED (row)`, `LANDING
#12 (row)` — and not `NO LOG` (`openRepoTools#89`): the cell is the lane's
current state, and a listing that printed `NO LOG` over it was not reading the
one column Amendment 13 made authoritative. The mark keeps it a **read**. A row
whose log has no lane-kind line records no directory, no profile and no
binding, so there is nothing to resume: column 10 offers no act, `lane-groups`
files the row `rowonly`, `lane` never numbers it and refuses it by name, and
the lane is picked up by hand in its own checkout — `pclaude --lane <lane>
<profile>` — where `lane-start` records its first lane-kind line. A cell saying
**`LIVE`** stays `NO LOG`: a cell cannot prove a session is running, only a
live record can, and the row is flagged for still saying it.

### The listing

```console
$ lanes                      # CLOSED and DORMANT rows are not here
$ lanes --closed             # …and now they are, each with a second line
$ lanes --closed --all       # every repository's
$ lanes --closed --here      # this workstation's
```

A CLOSED lane is shown with the closing line's **verb** (the `STATE` column) and
its **time** (the `AGE` column). A DORMANT row is shown as `NO LOG` with its
**start date** and its **state cell's head** — the row's own last words, which
are the only thing it has left to say — and with **FLAGS** where that cell says
the work was `unpushed`, `lost`, a `loss` or `owed`, or still says `LIVE`.

**An empty listing is not an empty register.** A repository whose every row is
closed or dormant lists nothing, so `lanes --prefix <repo>` says that in those
terms — *"no lane of `<repo>` is LISTED — and that is not the same as none being
recorded"* — points at `--closed`, and offers the position it read from the
register and the archive rather than assuming 1. And a closed or dormant row
never reaches the numbered pick even when it is asked for by name: `lane
<name>`'s read is deliberately unfiltered, so `lane-groups` drops it on its
CLASS rather than on its state word.

**The next free position is computed over EVERY row** — hidden rows and the
archive of the next section included — so a retired `<repo>-<n>` is **never
reissued**. A lane's identity is its name, its log is `lanes/log/<lane>.md` and
that file is append-only: a second lane at a retired position would write its
life into the first one's file, and every read of that log would answer for two
lanes at once. Positions are cheap; identities are not.

### The sweep — one act, on a word

```console
$ lane-end --retire-dormant openxFactory                      # DRY RUN
$ LANES_LANE=<your lane> lane-end --retire-dormant openxFactory \
      --reason "pre-Amendment-7 rows; no log, no session" --yes
```

Without `--yes` it **writes nothing** and prints every dormant row of that
repository with its start, its workstation and the head of its state cell,
flagging the ones above — *so the person sees what the sweep will close before
it closes it; an agent's stdin is not a terminal, so the word is `--yes` typed
by a person.*

With `--yes` it is **ONE commit** (`lanes-edit.sh retire-rows`, the lock held
once). Per dormant row:

* `lanes/log/<lane>.md` is created with a single line —
  `RETIRED — lane <lane>, session <the sweeping session's uuid>@<ws>, <UTC>,
  lane:<lane> → retired-dormant by lane <writer>; reason <why>; was: <the state
  cell's head, verbatim, capped at 240>`. The session field is the **writer's**
  transcript uuid (7(b)): `$LANES_SESSION`, else the last id of **this
  checkout's** row for the writer, else the published row's, so a transcript
  stamped here and not yet pushed is the one written. The row's own last words are carried into the log
  so that nothing the row said is lost when the row stops saying it;
* the row's state cell becomes `RETIRED · <UTC> · <why>; was: <head>`. Clause
  (c) writes that cell as `RETIRED <UTC> · <why> · was: <head>`, which is four
  parts where 13(a)'s cell has three and whose first word would be a state no
  reader knows; the phrase above carries the same four facts under the grammar
  every other writer obeys, and goes through the same `row_state_check` — so the
  240-character cap, the `|` and the second ` · ` are refused here exactly as
  they are for `set-row-state`. A `|` a legacy cell carries becomes `/` in the
  row (it would forge a cell boundary) and stays **verbatim** in the log line,
  which is not a table.

It **refuses**: a lane a live session holds on this workstation; a row whose log
already carries a lane-kind line other than `ENDED`/`RETIRED` (**a parked lane
is not dormant**); and a repository with no dormant row — *nothing to do is
said, not done* (exit **8**). Any one of them refuses the **whole run**, before
a byte is written, because it is one commit and therefore one decision. It also
refuses a session-record read that failed: *"a read that failed is not 'nothing
is live'"*. So does a listing that **withheld the class** (exit **1**). The
listing withholds it where it could not list this checkout's log directory, or
had nowhere to keep its warnings about published logs it could not read, and it
says so. Counting zero there would be "nothing to do" about rows nobody could
class.

A log whose **last** lane-kind line is `ENDED` or `RETIRED` is refused too,
since a `RETIRED` after it would be one more line in an append-only log. The
refusal says the lane is **already CLOSED** and already hidden (19(b)). For
`RETIRED` it names `lanes-edit.sh archive-rows <repo>` as the act that takes the
row out of the register. For `ENDED` it says nothing more is owed: the row stays
as the record, because `archive-rows` moves only rows whose cell says `RETIRED`.
A log whose last lane-kind line is `STARTED`, `PAUSED` or `RESUMED` is sent to
`lane-end <lane>` instead (#120 item 3). The lane is called CLOSED only when
origin's copy of its log ends on that line. A closing line only this checkout's
log carries, left by an earlier act that stopped part-way or never pushed, is
refused as exactly that.

It refuses six more things for the reason every writer here refuses them — a
half-written act is worse than no act: a checkout **behind** `origin` or dirty
in anything but the register; a row whose ` | ` count makes **which text is the
state cell** unknowable; a lane whose object log would need Amendment 15's
**case-only rename**, which is a write of its own and not one this commit
smuggles — or whose log `origin` still publishes under another case while this
checkout already has the row's spelling, **a case-only rename whose commit
never landed**, which the scan refuses in `ensure_log`'s own words rather than
leaving it to `ensure_log` half-way through the write (#120 item 2); an object
log that exists here and is **NOT TRACKED**, which is
somebody's uncommitted work and would land inside this commit under this act's
message; a lane **named twice**, whose log would take the same line twice in a
file nothing can correct; and a `--reason` carrying a `|`, a second ` · ` or a
newline, which is refused as the **argument** it is (capped at 180 characters,
because the row's own last words go in beside it).

It also refuses what a write would go **through** (exit **2**): a lane's log
path that is a symlink, or is there and not a regular file. The append's `>>`
would follow the link and put the RETIRED line outside the repository while the
commit records only the link. And it refuses the reads that **failed**
(exit **1**), because a failed read is not "it has none":
* a published log that the tree names and git cannot read;
* a log directory that cannot be listed;
* a published register that cannot be read (the seam's read, before every row).

It touches **no file outside the register and the logs**. A retired row records
that a lane is finished; worktrees, branches and handoffs stay exactly where
they are.

**Nothing is half-PUBLISHED, because the commit is the only thing that
publishes.** Every refusal above is made in the scan, before a byte is written.
A peer's uncommitted edit to a path the act commits is one of them: Amendment 23
S3 refuses the act over it (2) and leaves it as it was, for its author to commit
by its own act, `commit`; no write captures it any more. A failure in the writes
themselves — a log whose append could not be proved, a row whose rewrite touched
more than one line or could not be proved (the register is put back where the
write cut it short, #120 item 1) — leaves this checkout DIRTY and the register
unpublished: that is what `git status` then shows, `git checkout -- lanes` undoes
it whole, and the next run is REFUSED by S3, which refuses a write over a pending
change to a path it commits, rather than made twice. `migrate-state-cells` has the
same shape for the same reason. Such a refusal says this act has committed
nothing, and lists what the act had already written in this checkout. Where the
register may be truncated, it also names where the exact copy of the register
from before the edit is kept: in the checkout's own git directory, as
`lanes-edit-register-before-edit.<UTC>.<pid>` with mode 0600, where a restart
that empties the temporary directory leaves it.

### The archive — a second act on a second word

```console
$ lanes-edit.sh archive-rows openxFactory                     # DRY RUN
$ lanes-edit.sh archive-rows openxFactory --yes               # ONE commit
```

It moves every **`RETIRED`** row of that repository out of `lanes/LANES.md` and
into **`lanes/archive/LANES-retired.md`**, in one commit whose message names
each lane moved. The archive is written **first** and the rows removed second,
so a refusal between the two leaves a copy in the archive and the register
whole, never a row in neither file.

Before anything is written, `--yes` refuses (exit **2**) in two cases:
* an archive, or archive directory, that is a **symlink**, or an archive path
  that is there and **not a regular file**. The append would follow the link,
  and the rows would leave the repository while the commit removed them from
  the register.
* a **published archive this checkout does not have**, because it is outside a
  sparse checkout or marked skip-worktree. A new archive made here would hold
  only these rows, and its commit would drop every position the published one
  reserves.

As in the sweep, a peer's register edit is committed only after these checks.

Nothing reads a row differently for having moved: the listing reads the archive
beside the register (so `lanes --closed` still shows those rows) and so does
every reader of the next free position — and a published archive that cannot be
*rendered* is a REFUSAL that says so, never a fallback to this checkout's copy,
which may lack a retired position the published one holds: read as absent, or
from that copy, it would put a retired position back on offer. Six more reads
fail closed rather than answer, and for the same reason. The first three refuse
(exit **1**); the listing withholds the class for the last three:
* a local archive path that is there and not a regular file;
* a register path that renders empty (a zero-byte register, or a broken
  symlink);
* a published register whose blob git cannot read. Whether the register or a
  log is published is the TREE's answer, never `cat-file -e`'s, which fails for
  an unreadable blob as for an absent path.
* a published log git cannot read: the listing names it and never classes that
  lane DORMANT;
* a log directory it cannot list: the listing classes no row DORMANT at all;
* the events' own list of the published logs, which their lines are read
  through, where it could not be made: the listing says that no lane's log is
  read in it, and classes no row DORMANT at all (#290).

The listing's other list of the logs, the one that names a lane with a log and
no row, is said where it could not be made: such a lane is missing from the
listing. And a listing that names no lane where the register, the archive or
that list could not be read is exit **1**, the listing could not be read,
after each read that failed is said — never the **8** of no lane, which
`lanes` printed as *no lane is recorded* (#290).

`add-row` refuses a lane name the archive holds, so `lane-start <repo> <n>`
cannot reissue one by hand either. It also refuses (exit **1**) where the
published register cannot be read, rather than read that as "no such row":
otherwise a row this checkout has not pulled would be added a second time.
And it refuses (exit **1**) an archive that carries a `|` line it cannot take
apart, naming each such line by its number and the copy it is in, published
or this checkout's (#122). A line counts only if it is
the table's header (its first cell is `lane`), its `|---|` rule, or a row whose
first cell holds its lane in backticks; any other line may be the very lane
being added. The header is found by that first cell, because a ` | ` count
cannot tell it from a row (both carry six), and a hand edit can part it from its
rule. An archive scan that **failed** is a refusal too, never "no match". So is
an archive with CRLF line endings, or a table written without the spaces around
its `|`: `archive-rows` writes neither, and nothing in this tool reads CRLF.
A peer's archive line, or a peer's retirement or row, that lands while add-row
is publishing is met by the same checks: the push is refused, and Amendment 23
S4's replay drops add-row's commit by what it is and runs them again on
origin's new tip, so any of these refusals leaves nothing of add-row's to
publish, and nothing to take back. (#263's rescan after a `pull --rebase`, and
its take-back of the row in a compensating local commit, went with the pull in
#198 slice 1b.)

**One such line refuses every lane, not only its own.** `lanes-edit.sh
retired-identity <lane>`, the check `lane-start` makes before it launches,
exits 1 on it as well, so every `lane-start` refuses while the line is there.
`lane-start` hides the reason: it reads that check with its stderr discarded and
says only that the archive could not be checked for the lane. Run
`lanes-edit.sh retired-identity <lane>` to see the line, its number and its
copy.

**Rule 9 holds either way** — a row is
one `git show` away — so the archive is for a register a person wants shorter,
and never a requirement.

## Crash-consistent lane recovery (openRepoTools#91)

**A lane is `RUNNING`, `SWAPPING`, `SWAPPED` or `CLOSED`, and which of those it
is WITH NO LIVE HOLDER is what says where its session stopped.** A session can
run out of tokens before the handoff, after the handoff began and before it
finished, or after it finished — and until this capability all three left the
same evidence: a last lane-kind line that is a `STARTED`/`RESUMED` (which is
also what a running lane looks like) or a `PAUSED` (which is also what a clean
swap looks like). The two crash kinds had no word.

Governed by `openspec/changes/add-crash-consistent-lane-worktree-recovery/` and
tracked on [opensoft/openRepoTools#91](https://github.com/opensoft/openRepoTools/issues/91).
It amends no protocol: **it adds no lane-kind verb to the append-only log**.
Amendment 7's five state verbs, `STARTED`, `PAUSED`, `RESUMED`, `ENDED` and
`RETIRED`, stand, and every reader of them — `swapped`, `lane-last`,
`lane-dir`, `who`, `lane-end` — is untouched. (Amendment 18(g) has since added a
sixth lane-kind verb, `HANDOFF-REQUESTED`, which changes no state; this change
adds none.) What is new is a SNAPSHOT beside that history.

### The four words, and the two crashes

```text
RUNNING --/handoff begins--> SWAPPING --record + row + handoff all landed--> SWAPPED
   ^                                                                          |
   +---------------- the act that confirms the next binding --------------—---+
```

| the snapshot says | a live holder? | what it means |
|---|---|---|
| `RUNNING` | yes | the lane is running — do not launch a second coordinator or a second writer |
| `RUNNING` | **no** | **ungraceful stop**: the session died before any handoff began, so nothing was polled, refreshed or recorded |
| `SWAPPING` | yes | a handoff is in flight — do not compete with it |
| `SWAPPING` | **no** | **interrupted swap**: the handoff began and did not finish, so the record, the row and the handoff file may each be half done |
| `SWAPPED` | no | the swap completed; the lane is resumable once its trees are read |
| `SWAPPED` | yes | inconsistent — a swapped lane has no holder, and neither side is overwritten |
| `CLOSED` | — | the lane is finished; a dirty or unpushed tree under it is a closure inconsistency and no cleanup is made |
| any | **unreadable** | `indeterminate`. A holder that could not be established is NOT "no holder" (`R22`, Amendment 7(d)), and no crash is pronounced on a read nobody got. That includes the ids the holder is looked up by: a register row that could not be read — the published one, or this checkout's copy — is never a row naming no session (opensoft/openRepoTools#113). |
| **unreadable** | — | `indeterminate` again, and for the same rule read one file earlier: a snapshot that IS THERE and cannot be opened is not a lane that has none, and nor is one that could not even be looked for — its control root, or a directory above it, cannot be searched, or a link on the way cannot be followed (#291). `lane-state` exits **10** for both, never the **8** that means *this lane has no snapshot, go on*. |
| **`MALFORMED`** | — | `indeterminate`: a snapshot of this schema that is not a WHOLE record — cut short, or edited by hand (opensoft/openRepoTools#115). `lane-state` exits **10** for it too, and every writer refuses to replace it. |
| any | — and a worktree read **failed** | `indeterminate`, whatever the snapshot and the holder say: an inventory directory, the checkout's registrations or a lane root that could not be listed hides trees the report never saw (opensoft/openRepoTools#113). |
| any | — and the lane is **bound elsewhere** | `indeterminate`. Amendment 18(b): liveness is pronounced only from inside the binding's own host and container, and from anywhere else a binding is UNKNOWN, never dead — and this workstation's snapshot is this workstation's alone. The one exception is clause (b)'s own: a binding whose window is gone from this host's tmux is dead, and the local read decides. |
| — | — and the lane is **managed-owned** | `managed-owned`, and nothing else is read or pronounced: see *Managed-owned lanes* below. |

### The fence

Every transition carries a monotonic **generation** and a unique **operation
id**, and `set-lane-state --expect …` is the compare-and-swap:

```sh
lanes-edit.sh lane-state <lane>                     # state, generation, operation, owner, updated
lanes-edit.sh set-lane-state <lane> SWAPPING --expect RUNNING
lanes-edit.sh set-lane-state <lane> SWAPPED  --expect SWAPPING \
              --expect-generation <n> --expect-operation <id>
```

A finalizer whose state, generation or operation no longer matches writes
NOTHING and exits **7** — the number this file already spends on `claim`'s
CLAIM-LOST, and one meaning on it: *you lost the race*. That is what stops a
`/handoff` that stalled for an hour from marking a lane `SWAPPED` after somebody
has recovered and resumed it. A handoff that finds the lane still `SWAPPING`
**takes it over** with a new generation and names the operation that never
finished; nothing of that operation is undone.

`SWAPPED` keeps the generation and the operation — it is the same operation
reaching its commit point — so for an inventory write the pair alone cannot
tell a poll in flight from one delayed past its own finalizer. A
`set-lane-tree … --generation/--operation` is therefore also refused with **7**
where the lane reads `SWAPPED`: the operation it names is finished and its
inventory is closed (opensoft/openRepoTools#114). Every other transition moves
the generation, so this is the one state in which a matching pair names a
finished operation. An observation filed under no transition is a
`set-lane-tree` with neither flag.

`lane-state` finds the snapshot under the lane's control root, which its log's
`dir` places, and reads that log once, with its status: a log git could not
read is **10** as well — where the snapshot is kept is not known, which is not
a lane that has none — and `lanes-index` holds such a lane's rows for that
reason, in `lane-state`'s own words (opensoft/openRepoTools#290).

`RUNNING` is written by the act that CONFIRMS the binding and never by the
SessionStart hook: `session-start` never writes, never touches the network and
always exits 0 (Amendment 8, `R-A8-1`), and a hook that writes is a hook that
can break the session it was meant to orient. So the snapshot follows the
`STARTED`/`RESUMED` line `lane-start` writes, in `lanes-edit.sh`'s own
`write_event`; `ENDED`/`RETIRED` become `CLOSED` there too.

### Where it lives

A LOCAL control root beside the lane's own checkouts — not the register (every
write of that is a commit, a pull and a push, and a transition happens three
times per handoff with no network), and not inside a git worktree (metadata
there dirties a checkout and disappears with the very directory whose loss it
explains). Three rungs, and never the caller's current directory:

1. `$LANES_LANE_STATE_ROOT/<lane>` — the explicit override and the suite's seam;
2. `<parent of the lane's recorded `dir`>/.lane-state/<lane>` — the same parent
   the lane's own `.lane-worktrees/<lane>` root sits in, and `dir` is Amendment
   11(c)'s recorded field rather than a guess;
3. `$PROJECTS_ROOT/.lane-state/<lane>`.

No rung answering is **8**, *this lane has no control root* — the ordinary
answer for a lane that has not started under Amendment 11(c), and not a failure.
Nothing is backfilled (Amendment 7(i)).

A rung is passed over only where its directory is established absent. Where it
could not be looked for (#291) — a directory on the way cannot be searched, or a
link on the way cannot be followed — its root is the answer: `lane-state` and
`lane-trees` exit **10** there, never the **8** that means *none*; the listing
names the lane `UNREAD`; `lane-reconcile` pronounces `indeterminate`; and every
write there fails rather than filing under the next rung.

Because the snapshot is filed under NOTHING — never committed, never leaving the
machine that wrote it — `R-A11-14` does not reach it: a container with no
`$LANES_WORKSTATION` still keeps a lifecycle it can recover itself from, while
the register and object-log writes stop there exactly as they did.

### The worktree inventory

Every handoff polls the lane's writers in the two places a lane keeps them —
`<checkout>/.claude/worktrees/<name>` and
`<projects>/.lane-worktrees/<lane>/<name>` — and now records each one MACHINE
READABLY beside the lane as well as in the handoff's WRITERS section:

```sh
lanes-edit.sh lane-trees <lane>
# <id> <path> <branch> <head> <upstream> <dirty> <unpushed> <writer> <observed> <checkout> <generation> <operation> <schema>
lanes-edit.sh lane-tree-now <worktree path>
# <branch> <head> <upstream> <dirty> <unpushed>   — what git says about one tree NOW
```

The handoff's READY block counts the records it WROTE:
`inventory: N worktree(s) recorded` where every polled tree was recorded, and
`inventory: N of M polled worktree(s) recorded` where an observation refused,
`set-lane-tree` refused or the handoff withheld a tree
(opensoft/openRepoTools#117), so the number is the one `lane-trees` holds for it.
The `inventory:` line is printed where the lane has a lifecycle, under the READY
line.

A `trees` directory that is there and cannot be listed is `lane-trees`' exit
**10**, never the **8** of a lane that recorded no tree (opensoft/openRepoTools#157
item 4e). So is one that could not even be looked for (#291): under a directory
this account cannot search, stat fails as it does for a name that is gone, so
the read walks up to the deepest name that is there and answers **8** only where
that name could answer for the one below it. `lane-state` answers for the
snapshot the same way, and a recorded `dir` whose parent could not be looked for
keeps its own control root rather than falling to `$PROJECTS_ROOT`'s. Every
lane's records on this workstation, one row per tree, are `lanes --worktrees`;
the derived index keeps them as its `worktrees` table, which no act reads (*The
derived index*, below).

The full `head` and the `upstream` are why this is not the prose section one
more time: `%h` is an abbreviation that lengthens as a repository grows, and
`0 unpushed` cannot be told from *this branch tracks nothing at all* without the
upstream. Every field is an OBSERVATION and none of them is truth about git.

**`lane-tree-now` is the one implementation of that observation**, and the
handoff, the sidecar and the reconciliation all go through it, so the WRITERS
section a person reads and the record a recovery reads can never be two
different readings. It does not convert a git read that FAILED into a
clean-looking value: `unknown`/`none`/`0` are answers, and a read that could not
be made exits **1** and prints nothing (`R22`, Amendment 7(d)). Two states are
answers rather than failures and are spelled as such — a branch with no commit
yet has `unborn` for its head, and a branch whose upstream is configured but
whose remote-tracking ref is not in this checkout (the ordinary state after a
merged branch is deleted) records that configured upstream with `unknown`
unpushed, never the `0` that reads as *everything here is published*. Both
halves of that configuration are reads with a status: a `branch.<b>.merge` or
`branch.<b>.remote` that git could not read is a **1**, never an empty remote
spelled as the local upstream `./<merge>` (opensoft/openRepoTools#113).

**A sidecar this helper cannot read is one it will not replace.** A snapshot or
a tree record carrying a schema this version does not write is reported as
`UNKNOWN-SCHEMA` by every reader and REFUSED by every writer (exit 1), rather
than overwritten by a record an older helper can understand — the one act no
later reader can undo. And `set-lane-tree --generation/--operation` is COMPARED
with the lane's own snapshot under the mutex before the record is filed, so a
poll taken under an operation a recovery has since superseded — or one the
lane has already finalized — is refused with **7** instead of being filed over
the current inventory.

**And a record of THIS schema is read only when it is whole**
(opensoft/openRepoTools#115). The `schema:` line alone used to pass, so a file
cut short after it was read as empty fields and replaced by every writer. Now
every key its writer writes must be there and not empty — `lane`, `state`,
`generation`, `operation`, `owner`, `agent`, `profile`, `workstation`, `kind`
and `updated` for a snapshot; `lane`, `tree`, `path`, `checkout`, `branch`,
`head`, `upstream`, `dirty`, `unpushed`, `writer`, `generation`, `operation` and
`observed` for a tree — with a snapshot's `state` one of the four words, every
`generation` a number, a tree's `dirty` a number and its `unpushed` a number or
`unknown`, its `path` absolute, and its `tree` the id its file is named for.
Both writers have written these keys since #97 landed, and a tree record may
also carry `repo`, its path's git common directory
(opensoft/openRepoTools#116). It is optional, so a record without one is still
whole. A record that fails is taken the way an unknown schema is: every writer
refuses it (exit **1**, nothing written), `lane-state` answers **10** and
`lane-reconcile` reads
the snapshot as `MALFORMED`, and `lane-trees` carries a tree record as a row of
its own named by its file under the schema `<malformed>`, keeping the path the
record names — so the tree is still named once, though that path is the record's
own word and may be relative — and every other field empty.
The writers ask the same question of their own temporary file before the
rename, so a record cut short as it was written is removed rather than renamed
over a whole one.

**One path, one record.** A tree's record is named from its path and from
nothing else: `c<cksum>-<folded path>`, a `cksum` of the WHOLE absolute path
and then the path with every character outside the file-name alphabet folded to
`-`. The fold is for a person reading the directory; the checksum is what keeps
`…/a+b` and `…/a-b`, which fold alike, two records rather than one replacing the
other. And `set-lane-tree` takes a caller's observation **whole** — `--branch`,
`--head`, `--upstream`, `--dirty` and `--unpushed` together — or reads the tree
itself; a partial one is refused with **64** and nothing is written, because the
fields it lacks would be filed as a `dirty 0, unpushed 0` nobody observed. So is
a `--dirty` that is not a count, an `--unpushed` that is neither a count nor
`unknown`, or a `--branch`, `--head`, `--upstream`, `--checkout` or `--writer`
that is only blanks (a reader trims a value, so that field would be empty): a
record carrying one could be neither read nor replaced.

### The reconciliation, which resets nothing

```sh
lanes-edit.sh lane-reconcile <lane>
```

It recomputes branch, HEAD, upstream, dirty and unpushed for every inventoried
tree, compares the repository a record names with the one git answers for at its
path now, reads `git worktree list --porcelain` in the lane's checkout and the
directories under both lane roots, and prints one `TREE` line per tree with a
classification: `ok`, `dirty`, `unpushed`, `unpushed-unknown`,
`dirty+unpushed`, `dirty+unpushed-unknown`, `missing`, `possible-loss`,
`not-a-checkout`, `wrong-repository`, `unreadable`, `unreadable-sidecar`, `malformed-sidecar`,
`unknown-schema`, `unmanaged`, `stale-registration`. Every one of those reads has
a status, and a read that FAILED is never an empty answer (opensoft/openRepoTools
#113): an inventory directory that cannot be listed (`lane-trees`' **10**), a
`git worktree list --porcelain` that fails in a checkout with a `.git` of its
own, and a lane root that cannot be listed or searched each make the verdict
`indeterminate` and are named in it — a recorded directory that is no
repository at all registers nothing, and that alone is an absence, as
`lane-worktrees` reads it. A `BINDING` line says where the lane is bound — `here`,
`free`, `gone` (bound on this host's tmux and its window is gone), `elsewhere`
(and where), or `unknown` (its log could not be read) — and `elsewhere` or
`unknown` turns every verdict into `indeterminate` (Amendment 18(b)). The last
line is the `VERDICT`. `lane-start` prints the report before it writes
anything, for any verdict that is not `running` or `closed` — and for
`resumable` as well whenever its `TREES` line counts a tree that is dirty or
unpushed, requires recovery, or is unmanaged or stale: `resumable` says the swap
completed, not that every tree it left is clean, published and where it was.

A **renamed** lane keeps its snapshot and inventory: `rename-lane` moves its
control root to the new name once the rename's commit has landed, and never
over something already there (Amendment 16). Its `.lane-worktrees/<old>` root
holds real git worktrees and is not moved — that is a `git worktree move`, and a
person's. A lane retired by Amendment 19's sweep is taken to `CLOSED` exactly as
a lane that ended itself is.

**It reads the published register first**, as every read in `lanes-edit.sh`
does: the lane's name resolves through the register and Amendment 16's alias
table, and its binding and any managed-owner marker are read from them, so a
stale ref would pronounce from a binding that has since moved. The fetch goes
into the register checkout and touches no lane tree; it is bounded by
`LANES_GIT_TIMEOUT` and, when it fails, the ref is read as it stands.
`LANES_NO_FETCH=1` skips it — which is how `lane-start` calls it, having fetched
in its step 3 — at the cost of reading what was last fetched. Whether a recovery
read should be local by default is opensoft/openRepoTools#157.

**On `mode=primary`** the register this reads is the database: the lane's name,
its binding and any managed-owner marker come from the register through the
read door, never from `origin/main` or this checkout's copy; a read of the name
or the binding that fails is 69, a deciding read's, and the marker's is 1,
UNKNOWN (below), and neither is answered from the mirror. See "The SQL
register".

**It reports and it resets nothing.** `park` CREATES NOTHING and `resume` RESETS
NOTHING (`AGENTS.md` rule 1), so this read runs `git status`, `git log @{u}..`,
`git rev-parse` and `git worktree list --porcelain` and nothing else:

* a **missing** tree that was clean and published names the estate's own
  `resume <Name>` as the only rebuild — and NAMES it rather than running it,
  because that verb runs `make resume` across a whole estate and a report may
  not do that as a side effect;
* a missing tree whose last observation held dirty or unpushed work is
  **possible-loss** and is never claimed to be reconstructable — no metadata
  reconstructs a file's contents;
* an **unmanaged** tree — one git registers, or one sitting under a lane root,
  that no sidecar names — is reported and never deleted, adopted or overwritten:
  which lane a tree belongs to is a person's to say. One tree is named ONCE
  however many spellings of its path reach the report: a sidecar holds the path
  its poll was given, `git worktree list --porcelain` answers with the physical
  path, and the on-disk sweep walks the recorded `dir`, so every comparison
  resolves both sides — without which every tree of an estate that reaches its
  checkouts through a `projects` symlink is reported twice, the second time as a
  tree nobody manages;
* a **stale-registration** names the `git worktree prune` that clears it, and
  prunes nothing itself. A tree a sidecar records whose directory is gone while
  its recorded checkout or the lane's `dir` still registers it is ONE row, its
  `missing` or `possible-loss`, and that row names the same prune
  (opensoft/openRepoTools#117). The registrations of its recorded checkout,
  then of the lane's `dir`, are asked for its path under the recorded spelling
  and its parent's physical one: the remedy is named where one of those reads
  finds it, a failed read names no prune (the sweep reads again and says so),
  and a tree one of them finds is never named twice through a symlinked
  spelling. A gone tree that only another repository registers, in a lane with
  no legs (whose recorded `checkout` is the lane's `dir`, even for a tree of
  another repository), is plain `missing` or `possible-loss`, with no prune
  named and no stale-registration row beside it: neither of those reads finds
  it, and the sweep asks only the lane's `dir` and its legs;
* an **unreadable** tree is one git answers in and cannot be read through —
  nothing is assumed about it, in either direction, and the line names the
  `git -C <path> status` a person runs;
* a **wrong-repository** tree is one whose path git now answers for a different
  repository than the one its record names (`repo`, the path's git common
  directory, which `set-lane-tree` records). An independent clone, a checkout
  moved onto the path or a restored backup, on the same branch at the same
  commit, used to read `ok` (opensoft/openRepoTools#116) and now reads
  `wrong-repository` only where the common directory git answers for differs
  from the recorded one. A recorded tree that is itself a clone records
  `<path>/.git`, which its path alone decides, so another clone put at that
  path, or a restored backup of one, on the same branch at the same commit,
  still reads `ok`. Nothing else of the record is compared against it, it counts
  toward recovery, and it is left exactly as it is. A record with no `repo`, as
  every one written before this has, is not compared, and an identity git cannot
  give now is `unreadable`, never a match. The recorded `checkout` is not
  compared: for a lane with no legs it is the lane's `dir`, even for a tree of
  another repository;
* an **unreadable-sidecar** is a tree record that IS there and could not be
  read — a permission, an I/O error, a dangling link. The tree it recorded is
  NOT known, in any field; it counts toward recovery, the line names the file a
  person reads by hand, and it is never skipped as though the lane owned one
  tree fewer (`lane-trees` carries it as a row of its own whose schema is
  `<unreadable>`);
* a **malformed-sidecar** is a tree record of this schema that is not whole —
  cut short, a key missing or out of shape, or an id that is not its file's
  (opensoft/openRepoTools#115). It is reported as the unreadable one is: no
  field of it is read, it counts toward recovery, the line says what is wrong
  with it and names the file, and no writer replaces it. Before this, a record
  cut after its `path:` came out with an empty dirty and unpushed, and a vanished
  tree that held work read `missing`, clean and published;
* an **unknown-schema** tree is a sidecar written by a newer tooling: it is
  named, and not one field of it is read, because a value taken out of a record
  whose shape this reader is guessing at is worse than no value.

### What a resumed session does with it

Read the verdict first, then the trees, then `ListAgents` — the count is still
what decides whether a writer is live (Amendment 17 Addendum 1 (i), and (k):
one worktree, one writer). `ungraceful-stop` and `interrupted-swap` both mean
**inspect every tree before relaunching anything**; the difference is that under
`interrupted-swap` the record, the row and the handoff file may each be half
written, so check all three rather than trusting the handoff's top block.

## Managed-owned lanes

**Brett Heap's ruling of 2026-10-04, verbatim: *"managed ledger owns enrolled lanes; #97 owns legacy — rework both"*.**
A lane the managed ledger has enrolled carries a **managed-owner marker** in its
register row's state cell, written by that ledger's own writer:

```text
LIVE · <UTC> · managed-owner mode=managed daemon=<id> generation=<n> bound-lane=<lane>
MANAGED OWNER · <token>        (the historical shorthand)
```

Every lane without one is a **legacy** lane — every lane in the register today —
and the lane tooling here answers for legacy lanes only. The marker is read by
one reader, ported byte for byte from branch `001-separate-swap-ctx-handoff`
(`3c26041:lanes-edit.sh:776-898`), and asked through one read:

```sh
lanes-edit.sh managed-projection <lane>
```

| exit | meaning | what every legacy act does |
|---|---|---|
| 0 | a valid marker; the owner is printed | **refuses with 2**, names the owner, writes nothing |
| 8 | no marker and no managed-owner vocabulary, in the published row or this checkout's — whatever the row's shape: a row with none cannot be a marker, and a writer refuses a row it cannot split on its own (2) | runs exactly as it always has |
| 1 | managed-owner vocabulary that does not parse (a `generation=0`, an empty daemon, a `bound-lane` that is not this lane, an empty shorthand token, a `managed:` anywhere in the row), a row carrying the vocabulary that is not seven columns, vocabulary that only this checkout's register file carries, whether it is ahead of origin (a change committed here and not pushed, or not committed) or behind it (origin dropped the vocabulary after this checkout last moved; the remedy is a fast-forward), or a register that could not be read. The refusal names the file it read, which under a runtime-clone `LANES_REPO` is workspace.yaml's tree, not the clone | **refuses with 1**: ownership is UNKNOWN, and an ownership nobody could establish is never read as legacy (Amendment 7(d)) |
| 64 | usage | — |

The acts that ask, each before its first write: `lane-start` (at the head of its
section 3, before Amendment 18's binding gate and `--request-handoff`),
`lane-handoff` (before the window is renamed — so `--late`, `--restart` and
`--exit` never reach `SWAPPING` or `SWAPPED`), `lane-end` (the ending,
`--retire` and `--retire <pid>`), the object log's `STARTED`, `RESUMED`, `ENDED`
and `RETIRED`, a swap's `PAUSED`, `request-handoff` (its `--dry-run` too),
`set-lane-state`, `set-lane-tree`, `retire-rows` (one managed or unknown lane
refuses the whole sweep), `migrate-state-cells` (one managed or unknown row it
would rewrite refuses the whole migration), `archive-rows` (one such RETIRED
row refuses the whole move), `append-session-id`, `add-row` for a lane that
already has a row, `set-row-state`, `replace-in-row` and `rename-lane`. Each
reads this checkout's row as well as the published one, because this
checkout's row is the one a legacy writer rewrites: vocabulary in one copy and
not the other is UNKNOWN (1). `managed-projection` reads both copies the same
way (opensoft/openRepoTools#157 item 4a), so the gates of `lane-start`,
`lane-handoff` and `lane-end`, which ask it, refuse what the writers refuse —
before a launch moves a transcript rather than after. A claim or a release is
about the object, not the lane, and is not refused. `lane-reconcile` reads nothing of a managed lane and prints
`VERDICT managed-owned` (and `indeterminate` where ownership is unknown). And no
legacy writer — `set-row-state`, `add-row`, `replace-in-row`, `append-line`,
`append-session-id`, `rename-lane`'s new name, the sweep — may write the
marker's vocabulary into the register at all, so a marker is never forged.

**On `mode=primary`** the marker is read from the register's row alone: this
checkout's copy is the mirror and is not read, so exit 1's rule of two copies
does not arise there, and a read of the register that fails is 1, UNKNOWN,
never `legacy` and never 8. The ledger's projection into the row goes in
through the register, as every write does. See "The SQL register".

What the managed ledger itself does with an enrolled lane is not this manual's:
branch `001-separate-swap-ctx-handoff`'s governance review is **PROPOSED — NOT
APPROVED**, and nothing here asserts that it supersedes Amendment 17.

## The derived index (Amendment 14)

**In force — ratified by Brett Heap 2026-10-05, verbatim *"ratify 48"***
(brettheap/new-workstation#48; the tooling is opensoft/openRepoTools#160).
**The register's truth stays where it is.** `lanes/LANES.md`, `lanes/log/`,
`lanes/aliases.tsv` and `lanes/archive/` are the record, written only by
`lanes-edit.sh`. `lanes-index` keeps a **derived index** of them — SQLite on
local disk, or QA Postgres where this workstation is configured for it —
written behind the sources, read only by tooling that decides nothing, and
never a gate. Where the index and the source disagree, the source is right and
the index is behind.

**On a `mode=primary` workstation that paragraph changes.** The register's truth
is then the database, and `lanes/LANES.md`, `lanes/log/`, `lanes/aliases.tsv` and
`lanes/archive/` are its rendered mirror. The index no longer follows the
register there: `lanes-index sync` of the register's source does nothing and
says so, and the mirror is never indexed back into the database it came from.
`reconcile` there checks that the mirror equals the register and re-renders
what it lacks; it does not rebuild the index. The post-write nudge of the
register's index is not made, and `--index` changes nothing and prints no
`read: index` line, since the default read is already the database. The
inventory's source is indexed as before. The configuration file gains keys.
See "The SQL register".

### What it is for, and what it never does

It is for the questions no act asks: every repository's rows at once, every
lane's history on one timeline, a dashboard, Eagle and Raven asking one store.
It is **never read by an act**: no `claim`, `release`, `log` verb or LANES
line; not the name guard or the `SessionStart` block; not `lane`, `lane-start`,
`lane-end`, `lane-handoff`, `lane-rename`, `/restart`, `/handoff`, `/ctx`; no
row writer, sweep, archive or migration; and none of the reads an act makes for
itself (`who`, `binding`, `live-holder`, `next-free`, `managed-projection`,
`lane-reconcile`). An index that is missing, stale, unreachable, wiped or wrong
changes no exit code and no output of any of them. It never writes back:
nothing in any source is ever written from it.

### The three words, and the one step `lanes-edit.sh` gained

```sh
lanes-index sync      [--source <s>]              # write behind what LANDED
lanes-index reconcile [--source <s>] [--dry-run]  # read the source whole; put the index right
lanes-index status                                 # per source: store, provenance, lag, last error
```

- **`sync`** reads the register as `origin/<branch>` of the workspace checkout
  stands — what the push left, never the working tree — and upserts every
  record whose content differs, keyed by **(source, record id)** and versioned
  by a **digest of its content**: an upsert bearing the stored digest changes
  nothing, so a replay or a crash is harmless. The source's indexed commit is
  compared and swapped **in the same transaction** as the rows it covers, and
  a sync from a commit that does not descend from it **writes nothing** — so
  two workstations syncing into one Postgres, even at once, cannot roll it
  back. A record its source no longer holds leaves in the same sync. One sync
  at a time per workstation: a second finds the lock held, leaves the holder a
  mark to sync again, and returns at once.
- **`reconcile`** reads the source whole, compares every stored row by what it
  **says** (not by the digest it carries, so a row changed by hand is found),
  upserts what differs and removes what is gone: on a wiped index, the
  rebuild. `--dry-run` prints the counts and writes nothing.
- **`status`** prints the store, each source's provenance against its tip, the
  lag in commits, and this workstation's last sync and last error.

**The nudge.** After every write whose push **landed**, and after its mutex is
released, `lanes-edit.sh` starts **one detached `lanes-index sync`**: stdin
closed, stdout and stderr on `/dev/null`, never waited, its status never read.
It carries no data. It is skipped where `lanes-index` is not on `PATH` (one
`command -v`, nothing printed) and wherever `LANES_INDEX=off`. A slow, failing
or absent indexer delays and fails no commit, push or swap.

### Reading it — `--index`, only when typed

```sh
lanes --index                       # any listing, out of the index
lanes-edit.sh history <lane> --index
lanes-edit.sh history --all  [--since <UTC>] [--index]      # every lane, one timeline
lanes-edit.sh history --repo <owner/repo> [--index]         # the lanes whose HOME it is
```

The flag is typed per invocation — **never** taken from the environment or a
configuration. It replaces the **published** reads (the register, the archive,
the alias table, the logs) and nothing else, so the live session records, tmux
and this checkout's own files are read as they are without it. Where the index
is current the answer is **byte for byte the source read's**, on stderr as on
stdout — the warnings for a log's unreadable lines and the count that closes
them included (opensoft/openRepoTools#166) — the clock's columns aside (the
listing's AGE and its "last heard from origin" age, computed the same way at
the moment of the read). Stderr carries the flag's OWN lines besides: the
`read:` line below, and for `history` the `LANES_NO_FETCH=1 — not fetching …`
note, because where the index answers that arm fetches nothing in front of it.
And it is **one commit's image**: `lanes-index export` reads the provenance and
every table in ONE read snapshot — a SQLite read transaction, a Postgres
`REPEATABLE READ READ ONLY` one — so a sync landing mid-read is in the answer
whole or not at all (#166). One line on stderr says which was read:

```text
read: index (sqlite) at register@1a2b3c4d5e6f; 0 commits behind
read: sources (index unreachable: psql: error: connection to server … failed)
```

Lag is said, never refused. Where the index cannot answer — `lanes-index` not
installed, the store `missing`, `unreachable`, `wiped` (no provenance for this
register), of an `unknown-schema`, a configuration it `refused`, or
`LANES_INDEX=off` — the line says so and the **sources** answer, with the
source read's own exit. `history --all` and `--repo` read the sources without
the flag; `history` over two lanes used to be refused.

### The store and its configuration

`${XDG_CONFIG_HOME:-~/.config}/openRepoTools/lanes-index.conf`, or the file
`$LANES_INDEX_CONFIG` names — per workstation, **never committed**, never under
`~/.agents/` (which on a workstation built from the workspace repository *is*
that repository):

```text
store=sqlite|postgres     default: postgres where url= is set, else sqlite
sqlite=<path>             default ${XDG_STATE_HOME:-~/.local/state}/openRepoTools/lanes-index.sqlite
url=<libpq URL>           the WRITER role — sync and reconcile
passfile=<path>           the libpq password file for it, with no group or other bit
reader_url=<libpq URL>    the SELECT-only READER role — status and the --index reads;
                          REQUIRED beside url=, or those reads are refused
reader_passfile=<path>    its password file, where it is not the same one
schema=<name>             the Postgres schema (default lanes_index)
```

Refused (exit 2, nothing read or written): a configuration that is not a regular
file, or that group or others can reach, or that is inside a git work tree or
under `~/.agents/`; a URL that
carries a password in any spelling; a password file that is not a regular
file, or that group or others can reach (0600 and 0400 both pass); a SQLite
path inside a git work tree. A Postgres configuration with no `reader_url=` is
refused for `status` and the read flags (`read: sources (index refused …)`):
**a read never connects with the writer's credential**, and there is no
fallback to it. Postgres is reached through `psql` and only where
`url=` is set — nothing imports a driver — with `PGPASSFILE` naming the
password file and an inherited `PGPASSWORD` or `PGPASSFILE` removed; with
no `passfile=` (a passwordless role), `PGPASSFILE` names an empty 0600 file of
the indexer's own, so libpq never falls back to `~/.pgpass`. No configuration at all
is SQLite at its default path, created 0600.

**The two roles are Postgres's to hold** (act 3, Brett Heap's on Eagle and
Raven — or nothing, and SQLite) on a store of the index's own, which a file
with no `register_url=` names. Where `register_url=` is set, `reader_url=` is
the engineer's READER login on the register's database, so a Postgres index
half is on that database: `url=` names `lanes_indexer`, which the register's
bootstrap makes and which owns `lanes_index`, and neither role below is made
there ("Roles and logins"):

```sql
CREATE ROLE lanes_index_writer LOGIN;   -- the password lives in the 0600 passfile only
CREATE ROLE lanes_index_reader LOGIN;
CREATE SCHEMA lanes_index AUTHORIZATION lanes_index_writer;
GRANT USAGE ON SCHEMA lanes_index TO lanes_index_reader;
ALTER DEFAULT PRIVILEGES FOR ROLE lanes_index_writer IN SCHEMA lanes_index
  GRANT SELECT ON TABLES TO lanes_index_reader;
```

The writer is confined to the index's schema and held by the indexer alone —
by no session, supervisor, launcher or ledger authority; a leaked writer can
damage only what a `reconcile` rebuilds. The reader may only `SELECT`.

### What is indexed (schema 1)

One versioned schema (`schema_version`); every row carries its `provenance`
(the register commit it was last written at), `indexed_utc` and
`schema_version`, and its `digest`.

| table | one row per | what it holds |
|---|---|---|
| `lanes` | lane | canonical name, `aliases` (Amendment 16(e)), `home`, the `workstation / env / user` cell, `started_utc`, `handoff_path` (a pointer), `state_phrase`, `objects_cell`, `session_ids` in order, `last_session` (the resume target), `archived`, the row's own text — and **`owner`**: `legacy`, `managed <owner>` or `unknown`, from `lanes-edit.sh managed-projection` (0, 8, 1) and never from a parser of the indexer's, so a malformed marker is never downgraded to `legacy` |
| `log_lines` | log line, by lane and file ordinal | verb, session, workstation, UTC as written, object, payload, its sub-fields as JSON, free text; a line the grammar cannot read is kept as `unreadable` with its file, line and reason, never dropped |
| `register_lines` | register line, by ordinal | Rule 6's `LANDING`/`LANDED` and Amendment 10's `HOLD`/`HOLD RELEASED`: lane, repository, PR, tip, UTC, the line itself |
| `lane_aliases` | alias-table line | `old_name`, `new_name`, UTC |
| `transcript_pointers` | pointer | a row's session-cell ids and each `transcript` a `PAUSED` names, with its `agent` — pointers only |
| `source_files` | file of the source | kind, blob, line counts — what the read flags need to answer as `origin` would |
| `holds` (a VIEW) | open hold | `holders_of`'s answer, which is `who`'s: per lane (case-insensitively), its last line on each object **in file order**, never by UTC, where its verb is open — and not while another lane's own last line there is a `TAKEOVER`. **Not yet where one `TAKEOVER` displaced another**: `holders_of` displaces a `TAKEOVER` only by one that names its lane (`← lane:<x>`, opensoft/openRepoTools#74), and this view still reads any other lane's `TAKEOVER` as displacing it, so for a chain of takeovers it holds no row where `who` names the last link, and for two takers neither naming the other no row where `who` names both. The object is keyed AS WRITTEN: a line naming a repository by a spelling `lanes/repos.tsv` has since renamed (R20) is its own key here, where `who` folds its argument into the canonical one. Computed, never stored |
| `provenance` | source | the indexed commit (or generation), UTC, schema version |

**Created empty and written by nothing here**: `swap_states` and
`job_summaries` — the managed ledger's half, `ledger:<Workstation>`, which the
001 feature's T058 writes (act 4). `worktrees` is the inventory's, below — the
same schema 1, whose table was created with #161's columns before a row was
written into it. Not indexed at all: transcript and handoff bodies,
credentials, the ledger's claims, fences, releases and exit witnesses.

### The `worktrees` table — every lane's #97 inventory (opensoft/openRepoTools#161)

**What it is for: the next account's first query.** A session that resumes a
lane — or that sits down at Raven after a day on Eagle — wants every open
worktree before it decides anything: whose it is, which branch, whether the
lane stopped `RUNNING` or `SWAPPED`, what was dirty or unpushed, when it was
last seen and whether a writer is still live. Each lane's handoff already
records that beside the lane (*The worktree inventory*, above); this table is
the same records in one place, under the source `inventory:<Workstation>` — one
per workstation, because the sidecars live on that workstation's disk and
nowhere else — so a store two workstations sync into answers for both.

```sh
lanes --worktrees [<lane> | --all]            # this workstation's sidecars
lanes --index --worktrees [<lane> | --all]    # the table: every workstation it holds
lanes-index sync      --source inventory      # what every inventory write nudges
lanes-index reconcile --source inventory [--dry-run]
```

| column | from |
|---|---|
| `workstation`, `lane` | the workstation's name (`lanes-edit.sh workstation`) and the lane's canonical one |
| `owner` | `legacy`, `managed <owner>` or `unknown` — the read `managed-projection` makes, never a parser of the indexer's |
| `path`, `branch`, `dirty_count`, `unpushed_count`, `last_seen_utc` | the tree's sidecar: the last observation, `detached <sha>` for a detached head, an `unknown` count as NULL. A sidecar that could not be read is `unreadable sidecar`, one of another schema `unknown schema <n>`, and one of this schema that is not whole `malformed sidecar` (#115); each keeps the path it names, and has the record's id where it names none. The lane of a record that could not be read, or that was read and names no absolute path (none, an empty one, or a relative one), is `UNREAD` beside its row, so a sync keeps that lane's rows rather than replace the tree's row with one keyed by the record's id, or by the relative text (#291, #302). One such record holds EVERY row of its lane — the lane's other trees, and any new ones, stop reaching the index until it is put right — while one that names an absolute path keeps its key, is rewritten in place and holds nothing |
| `base`, `last_commit` | two git reads of the RECORDED head, in the tree or, once it is gone, its recorded checkout: its merge-base with `origin/main` (else `main`, else `origin/HEAD`), and `<sha> <subject>` |
| `lifecycle`, `generation`, `operation` | the lane's snapshot (`lane-state`): `RUNNING`, `SWAPPING`, `SWAPPED` or `CLOSED` for a **legacy** lane; `INDETERMINATE` for a managed or unknown one, whose lifecycle this tooling does not pronounce on, and for a snapshot that is missing, unreadable or of another schema |
| `writer_live` | the listing's own LIVE — a session record on this workstation naming one of the row's ids — and NULL where those records could not be read, the lane is bound on another host, or the register's ids or the lane's log — its binding — could not be read, from where liveness is unknown and never dead (Amendment 18(b), #290) |
| `pr_number`, `pr_state` | `gh` at sync, best effort, the newest pull request whose head is the branch; NULL wherever it was not asked (`LANES_NO_GITHUB=1`) or did not answer. A MERGED or CLOSED answer is not asked again while the branch and its last commit stand |
| `provenance`, `indexed_utc` | the generation the TREE'S OWN record was filed under — behind `generation` exactly when the observation predates the lane's current transition — and the sync |

**Read through the helper, never around it.** The indexer reads `lanes-edit.sh
worktrees --all`, which lists each lane's sidecars through `lane-trees`' own
reader, its snapshot through `lane-state`'s and its owner through
`managed-projection`'s, and walks no directory for a tree no sidecar names —
that is `lane-reconcile`'s act. Nothing is recomputed: `dirty` and `unpushed`
are what the handoff saw, and `LAST SEEN` says when.

**Rows follow the inventory.** Keyed by (source, `<lane>:<path>`) — a second
tree of one lane with the same path (a record that is not whole and names a
whole one's path, say) as
`<lane>:<path><US><n>`, numbered from 2 in the read's sorted order, and a lane's
unread marker as `<lane>:<US>unread` — and versioned by a digest of the row and
its provenance, so a replay writes nothing (a change that reorders two trees of
one path moves both keys, and that sync upserts both); a tree
the inventory no longer names — removed at landing, or by a sweep's disposition
— leaves in the same sync. The provenance moves only forward **per lane**: a
`sync` that reads a lane's lifecycle generation below the one its rows were
written at (a snapshot moved aside and begun again) writes nothing for that lane
and says so, and `reconcile --source inventory` is the act that takes the lane
as it now stands. A lane whose `trees` directory is there and cannot be listed,
could not even be looked for, holds a record that could not be read (#291), or
holds one that was read and names no absolute path (#302: no `path:` line, an
empty one or a relative one, in a record that is not whole or is of another
schema — its row is keyed by the record's id or by the relative text, and a
sync took that for the tree) keeps its rows — it is not a lane with no
worktrees — and gets ONE MARKER ROW,
keyed `<lane>:<US>unread`, with no `path` and the `branch` `unreadable inventory
(<why>)` — the key is what makes it one, because a hand-made sidecar's branch
can read the same, and so can an older helper's row for a sidecar with no
`path:`, which has an empty path —
which the `--index` read prints as the same `inventory NOT READ` line the sidecars
give (opensoft/openRepoTools#173): a FIRST sync that cannot read a lane has no
rows to keep and is still not an empty answer, and rows held over from an
earlier sync are said to be, on that line. The marker leaves with the first
sync that reads the lane, and `lanes-index status` counts it apart from the
worktrees. THE HOLD IS THE WHOLE LANE'S: one record that could not be read, or
that names no absolute path, keeps every row of its lane as the last sync that
read it left them, so the lane's other trees, and any new ones, stop reaching
the index until that record is put right. A record that is not whole or is of
another schema but names an ABSOLUTE path keeps its key and holds nothing: its
row is rewritten in place as `malformed sidecar` or `unknown schema <n>`, with
its counts empty. The schema stays 1, so an indexer from before #173 sharing a
Postgres store reads a marker as a tree: its `export --worktrees` prints it as
a `TREE` line with an empty path and that branch, and its `status` counts it as
a worktree. An older and a newer indexer syncing ONE store each converge to their
own view of the same inventory — no duplicate rows and no growth — and they
rewrite it on every sync only where a lane has two trees with the same path,
which the older one keys as one; with none, an unreadable lane included, a sync
after the first writes nothing. A store written by #219's unlanded heads
(`f00ee58`, `ab853b1`) keeps their marker, keyed `<lane>:`, as a kept row while
that lane stays unreadable, and it clears at the first sync that reads the lane.
The source's own row carries a fingerprint of the
whole table as its compare-and-swap token and a sync count that only goes up. A
container with no `LANES_WORKSTATION` syncs nothing (exit 2): a source named for
a container's own id is one no restart finds again.

**A lane listing that is not whole writes nothing** (opensoft/openRepoTools#173
item c1). The inventory is read through the lane listing, whose lanes are the
names three reads give — the register's rows, the archive's and the object
logs' — and each lane's trees are under the control root its log's `dir`
places. Where the register, the archive, the list of the logs or any one
published log could not be read — or whether one could not is NOT KNOWN,
because no temporary file could keep the warnings of their read —
`lanes-edit.sh worktrees --all` prints the rows it did reach and one line more,
`PARTIAL <workstation> <why>`; over a listing those reads left empty it is that
line alone, never the **8** of "none". A lane such a read could not name is not
a lane with no worktrees, and which lanes those are is exactly what it does not
know, so `sync` and `reconcile --source inventory` write NOTHING over it — not
the lanes it did list either — and exit **1**, `unread: the lane listing …
was not whole - <why> - …`, which `status` shows as the last error and the
`--index` read beside the source's `synced <UTC>` (below). The table
stays the last whole read, dated by its `synced <UTC>`, and the first sync over
a whole listing writes what moved meanwhile. Holding only the lanes the read
could not name would have needed a marker for each of them, one for the listing
itself — a first sync has no rows to hold — and a one-lane `--index` read that
knew of it. An indexer from before #173's item c1 refuses the line too, as one
it does not read. A read of one NAMED lane goes by that name, which no listing
gives, and never prints `PARTIAL`. Nor does a lane the listing DID name and
could not read — its `trees` directory, its control root or a directory above
it cannot be listed or searched, a record of its trees cannot be read, or one
that was read names no absolute path: that is the lane's own `UNREAD` line, and
only that lane's rows are held (#291, #302).
The named read reads the lane's log once, and where
that read fails and no `LANES_LANE_STATE_ROOT` names the root, the lane is one
`UNREAD <ws> <lane> its object log <path> could not be read, …` line and exit
**0**, never the **8** of "none"; so is a name whose register read failed
where no tree was found under it, because the register is what spells it
(opensoft/openRepoTools#290). `lanes --worktrees <lane>` prints either as
`inventory NOT READ`.

Where those reads succeed, the listing makes each of them once, except the list
of the logs, which it makes twice (the review of #289). The register, the
archive and the list of the logs are asked before the listing is made, and
where one succeeds the listing reads that very text — the register out of its
cache, the other two pinned for that listing alone — so a read of them that
fails after the check cannot thin a listing taken as whole; where one fails,
the read is `PARTIAL` whatever the listing's own reads of it then give. The
second list of the logs is the events', which read every log once per process,
and where it could not be made the events say so: that is `PARTIAL` too,
because every lane's `dir` comes out of their read. A lane's `dir` is not read
again: its control root, the list of its trees and its lifecycle snapshot all
take the listing's own. A second read of the lane's log that failed used to
send the root to `$PROJECTS_ROOT/.lane-state`, and a lane with trees read as
one with none (review r2 of #289). What is still read twice, besides the list
of the logs: with no `origin` (`LANES_NO_GIT=1`, or a checkout with no
`origin/<branch>`), the log directory is asked — there and not a directory this
account can list and search is `PARTIAL` — and is then listed again for the
names and again for the events; a log file in it that will not open is named,
as a published log git could not read is, and the logs after it are still
read (#290); and the events ask for themselves whether
`origin/<branch>` is there, so a `rev-parse` that failed in that one read alone
reads this checkout's logs in place of origin's.

**The nudge.** Every inventory write — `set-lane-state`, `set-lane-tree`, the
lifecycle follow after a `STARTED`, `RESUMED`, `ENDED` or `RETIRED`, and a
rename's move of the control root — starts the same detached `lanes-index sync`
a landed push does, as `sync --source inventory`, skipped where `lanes-index` is
not on `PATH` or `LANES_INDEX=off`. One sync at a time per workstation: a nudge
that finds the lock held leaves a mark NAMING ITS SOURCE, and the holder syncs
every source a mark names before it lets go.

**The read.** `lanes --worktrees` reads this workstation's sidecars; with
`--index` it reads the table — every `inventory:*` source the store holds, so
Raven sees Eagle's leftovers — and stderr says `read: index (<store>) at
inventory:<ws> synced <UTC>`, or `read: sources (index <why>)` and the sidecars
answer. Where this workstation recorded an error for a source — a `sync` or a
`reconcile` of it that refused or failed, a `--dry-run` included: a lane listing
that was not whole, a store it could not reach — that source's `synced <UTC>` is
followed by `(last error: <UTC> <why>)`, the last error `status` shows, until a
`sync` or a `reconcile` that is not a dry run succeeds; another workstation's is
in its own record, not this one's.
Where the table is current, this workstation's rows are the same bytes
either way — and a store only this workstation syncs into (SQLite, or a
Postgres nobody else writes) prints exactly the source read, while a shared one
adds the other workstations' rows among them. Every row is a last observation: the current truth of a lane is `lanes-edit.sh lane-reconcile
<lane>`, which reads the disk — and the sidecars — of the workstation it runs
on and of no other, so the footer says to run it ON THE WORKSTATION the lane's
rows name (#173): Raven's listing of Eagle's rows is answered on Eagle. A
`PARTIAL` line prints as `(every lane)` and `lane listing NOT WHOLE: <why>`,
and the footer says that a lane missing from such a listing is NOT a lane with
no worktrees — the listing's own form of an `UNREAD` lane's `inventory NOT
READ` line.

**What never reads it.** No act: not `lane-reconcile` — there is no
`lane-reconcile --index`, by Amendment 14(b) — not `lane-start`, `lane-end`,
`lane-handoff` or the worktree sweep (opensoft/openRepoTools#162). Each of them
reads the sidecars and the disk, exactly as before this table existed; an
inventory index that is missing, stale, unreachable, wiped or wrong changes no
answer of theirs.

### The tests' promise

`tests/test_lanes_index.py`, and a section of `tests/test_lane_helpers.sh` for
the acts only that suite drives, hold the index — the inventory's table
included, whose writers and `lane-reconcile` are among the acts — to Amendment
14's eight:
**offline no-read** (every act byte-identical against an unreachable store),
**poisoned index** (wrong rows change no act's answer), **wiped-index
reconcile** (the rebuild equals a synced index row for row), **replay**,
**monotonic provenance**, **a hung indexer** delaying no writer beyond the fork,
**the managed seam** (valid, malformed and plain rows as `managed`, `unknown`,
`legacy`, all through `managed-projection`), and **fallback** (each failure
says `read: sources` and gives the source read's answer). The parser is held
to `LOG_AWK`'s output line for line. No test of the index creates a Postgres
database: the stores of `tests/test_lanes_index.py` are SQLite files in a
temporary directory, and its Postgres path is a stand-in `psql` that records
what it was asked. A real engine is for the SQL register's own tests.
In the v1.3.0 suite, `tests.yml` sets `OPENREPOTOOLS_REQUIRE_PG` to `1` for
every job, and the fixture, `tests/pgcluster.py`, starts a disposable cluster
from the PostgreSQL 16 binaries it finds: `PG_BIN` first, which is final where
it is named, then `pg_config --bindir`, then `/usr/lib/postgresql/16/bin`. Where
it finds none, a run with the switch at `1` fails before any test, pytest's exit
4, and does not skip; with the switch at `0` or unset the real-engine tests
skip, naming what was tried. The `verify` workflow, asked for PostgreSQL
(`Verify-Postgres: 1`), installs PostgreSQL 16, proves that a cluster of its own
starts and answers, and runs the selected tests with `PG_BIN` set to its
binaries and the switch at `1`; unasked, it sets the switch to `0` and `PG_BIN`
to an empty directory, so the real-engine tests skip.

## The SQL register

**Feature 007, for opensoft/openRepoTools#165** (Amendment 22's act 2, as
Amendment 23 amends its S2), which ships in v1.3.0: the toolset before v1.3.0
has none of it, and a workstation uses the database only once the estate's
cutover and its own flip have run. **On a workstation whose `lanes-index.conf` says
`mode=primary`, the lane register is a database.** Each write is one
transaction. The files under `lanes/` are its rendered mirror, written behind
it. And nothing falls back to writing the files. Everything else in this manual
describes the register as files, and stays true for every workstation that is
not `mode=primary` (a failed register read's 69 and the three exceptions are
under "The switch and `lanes/STORE`"). The sections that describe a write or a
read say where `mode=primary` changes them, and point here.

Four things stay what they were on a `mode=primary` workstation:

- **The grammar.** Amendment 7's lines, Rules 1 to 10 as rules, and every
  command's arguments.
- **The handoffs.** A handoff, `lane-start`'s Rule 3 stamp, `lane-handoff`'s
  `PAUSED` commit and the handoff skill's commit stay git commits that the lane
  makes itself. They are no register write, and the renderer never writes them.
- **`lanes/repos.tsv` and `lanes/log/README.md`.** They stay git files that a
  person commits, and the renderer renders onto a commit that changes them.
- **The managed ledger.** An enrolled lane's truth stays its local ledger. Its
  projection into the register row goes in through the same storage layer as
  every write, so on `mode=primary` it lands in SQL first.

### What changes for a lane

This part is for a lane, and for whoever runs one. It says what a lane sees on
a `mode=primary` workstation that it did not see before, in the order a lane
meets it. Where a sentence says "on primary" it means that workstation. A lane
on any other workstation meets this section only as the refusals under "The
switch and `lanes/STORE`".

| you see | it means | you do |
|---|---|---|
| exit **69** | the register could not be read, or refused the write for a reason that is not its own rules, or this build refuses `mode=primary`, or this workstation's mode and the estate's disagree; **nothing was written**, but for `request-handoff`'s request when its wait ends 69, and for one internal error that cannot say whether the write reached the register | do what it prints: a write's rerun, or the read again, once the register answers; for a build's refusal, the whole toolset installed; a mode that disagrees is Brett's; after `request-handoff`'s wait, read the lane before you ask again. If it keeps coming, stop and tell Brett |
| exit **75**, "register busy" | other writes took every turn for 30 s, or an attempt's child did not reach its commit within its bound; **nothing was written, and a retry in seconds is safe** | run it again; it is no outage |
| exit **3** | the write **may not be written**: a commit call of it may have committed, and the writer could not find out; or an identical write is still running, or holds a claim whose owner cannot be tested here, and this run sent nothing | do what it prints, and never run a write again without the id it printed, where it printed one: that is the `LANES_WRITE_ID` rerun, which writes it once or finds it; where an identical write is running, wait for its process to end first; where one is still being started, check for its effect first, write nothing if it is there, and run the command again if it is not; where a claim's owner cannot be tested here, wait for the UTC it prints |
| the guard refuses a prompt, exit 2, "the register's SQL store did not answer" | the guard's read of the database failed | take one of the two ways out below: wait, or roll back |
| `commit` or a write refuses over a hand edit | the files are a rendering now | restore the file and make the change through its verb |
| exit **2** from `claim`, `HOLD` or `LANDING` | the register refused it and wrote nothing: most often an earlier write holds the object, or `main` | read the line it prints, which says which; it is not exit 7 |

**69 — the register could not be read, or refused. Nothing was written, but
by `request-handoff`'s wait, and but for one internal error (below).** On
primary a write whose snapshot cannot be taken (the database cannot be reached,
say), or whose commit the database refuses for a reason other than the
register's own rules, exits 69 (`EX_UNAVAILABLE`), naming the cause. It has
written nothing: not in the register, not in the journal, not in git, and it
leaves no lock. A failure after its commit call was sent is not 69: that write
is in doubt, and exits 3 (below). **It never writes the files instead**, and
neither a hand edit of the files nor a hand-run SQL write is the way round it.
Its last lines say how to run it again:

```text
the register gave this write no snapshot at this write's first snapshot: <the cause>. No write id was minted and no pending record of this run was written: nothing was sent. Run it again as it was given: LANES_LANE=<lane> LANES_SESSION=<uuid> lanes-edit.sh <the same arguments>
```

Where the write already had an id (a printed rerun, or a record kept from an
earlier run), the line says so instead, and the rerun carries it:
`LANES_WRITE_ID=<id> LANES_WRITE_GENERATION=<generation> …`, which settles the
write with or without its record and never writes it twice. **What to do:** run
exactly the command it printed, once the register can be reached; that is safe
each time. A 69 that does not clear is a stop: do not look for another way to
write, run `lanes-index status` from a shell (it needs no lane session), and
tell Brett. 69 is also the code of two refusals that are not an outage, of a
read, and of the classifier `append-line` asks, each with nothing written:

- a build without the renderer's git half: `mode=primary is not available in
  this build: the renderer's git half is not installed, so a write could not be
  mirrored; nothing was written`. It is no fault of the configuration: it
  clears only with a toolset whose `lanes-index` has the renderer's git half,
  installed whole by its host's route (`openRepoTools --install`, or on a
  `./setup.sh` host the package re-vendor), never `lanes-edit.sh` or
  `lanes-index` alone, and `lanes-index ready` fails an install that still
  carries it;
- the workstation and the estate disagree about the mode:
  ``this estate's register is SQL; configure `mode=primary` ``, from a files-mode
  write whose fetched tip carries `lanes/STORE`, and from a primary write whose
  transaction finds the switch not `primary`, as it is after a rollback. It is
  not yours to fix: tell Brett, whose act sets and clears `mode=`;
- a **read that decides**, whose own read of the register failed, whether for an
  outage or for either refusal above: it prints
  nothing on stdout, and **a read that failed is never "no record"** (never 8)
  and never answered from the files;
- `append-line`'s classifier: on primary `append-line` asks
  `lanes-index register classify` whether its text is one of the four it
  refuses, before it takes its lock; where the classifier gives no answer it can
  read, the text is not written and the lock is not taken. Install the
  `lanes-index` that ships with that `lanes-edit.sh`, or run the text through
  `lanes-index register classify` by hand to see why.

One 69 follows a write: `request-handoff`'s wait, where its last poll could not
read the register. Its request stands in the register and is not written again,
whether the lane is still bound is not known, and nothing here bound it; it
prints how to read the lane, and nothing on stdout.

One internal error ends a write 69 without that certainty: `lanes-index mode`
answered primary, and the write left the register's attempt loop without
ending, which is an internal error of `lanes-edit.sh`. Whether the write
reached the register is not known, but a write already made reads as found
when the command is run again, so run it again; it wrote no files instead.

**75 — register busy. Nothing was written, and a retry in seconds is safe.**
Exit 75 (`EX_TEMPFAIL`) is a write that could not get its turn: another write
committed first at every attempt, until a deadline of 30 s from the first
attempt. The database answered, so it is **not an outage**, and it is not 69:

```text
register busy: <k> attempts in <D> s, each beaten by another write; nothing was written; a retry in seconds is safe. To run it again, by its write id: LANES_WRITE_ID=<id> LANES_WRITE_GENERATION=<generation> …
```

Run it again, with the id and generation where it prints them. A write that was
in doubt at an earlier attempt does not end 75 until its lookup (below) has
found it absent. A 75 that repeats for minutes together is a question for
`lanes-index status`, never a reason to write anything by hand. 75 has one
other cause, which also sent nothing: an attempt whose child did not reach the
point where it would commit within its bound. The run stops that child and says
so, and nothing was sent by that attempt.

**3 — the write may not be written. Run the printed rerun.** In files mode, 3
is `pushed, not confirmed: <sha>`, and the commit stays on HEAD, unless another
writer in the checkout rewrote it while its push was in flight, which the words
then say; what reached origin is then read by content (the exit table's row 3).
**On primary it
means something else:** a commit call of the write may have committed, and the
writer could not find out in time. The write may exist in the register, and may
not.

```text
cannot confirm whether register write <id> committed; nothing else was written. A commit call of it may have committed, and F3's lookup could not settle it: <why>. On primary this write may not be written; run the printed `LANES_WRITE_ID` rerun, which writes it once or finds it. Its pending record, <state>/register-pending/<d>, is kept. … Look it up: lanes-index journal --write-id <id> --not-after <n> --generation <g> … Or run it again: LANES_WRITE_ID=<id> LANES_WRITE_GENERATION=<generation> LANES_LANE=<lane> LANES_SESSION=<…> lanes-edit.sh <the same arguments>
```

(F3 is the design's name for the lookup of a write by its id,
`lanes-index journal --write-id`.)

**What to do:** run the printed rerun, character for character. It carries the
write's id, so the register writes the edit once, or finds that it is already
there and writes nothing a second time. **Never** run the command again without
the id: a plain rerun can mint a new id and make a committed write twice. And
never read a 3 as "written", as a script that takes 3 for success does: every
script of this toolset relays the writer's printed rerun, and none prints a hint
of its own but one: where the NOTED line that `lane-end --sweep-checkouts`
writes after its sweep ends 3, `lane-end` prints the writer's words, then files
mode's words and the publish act, which sends nothing for a register write, and
exits 1; settle that line by what the writer printed. Two other ends are 3 too,
and each sent nothing:

- an identical write is still running in another process: its outcome is not
  known and it is left as it is, and what to do depends on what the message
  says of it:
  - `an identical write is still running in pid <p>`, with its write id: wait
    for that process to end, then run the printed rerun, which finds it or
    writes it once; where the message prints a lookup (only where the record
    holds a `call` line), the lookup settles it too. Where its pending record
    cannot be read, its id is not known, and the message sends you to
    `lanes-index status`, which lists the pending records;
  - `is being started in pid <p> on <host>/<container>`: it has no record and
    no id yet. Once that process has ended, look for its effect, as printed
    (`lanes-edit.sh history <lane> --since <utc>`, or `register-row <lane>`);
    if it is there, the write was made, so do not run the command again; if
    it is not, run the same command again, which takes over that write's
    record if one was left, or writes anew, or run the rerun by its own
    write id, where this run had one and the message prints it;
- a claim of an identical write whose owner cannot be tested here (the claim
  names no pid or cannot be read, its pid lives with no start time, or it is
  from another host or container): the message names the claim file and the
  UTC after which a run may take it; run it again then, as it prints.

A rerun is refused with 2 when the register has been replaced since: an id from
another generation is never applied twice, and the refusal prints the effect
check to make, the steps that may be owed, and `LANES_WRITE_ID=new`. A pending
record that is more than 15 minutes old is not reused: the run says so, prints
the lookup and writes anew.

**The guard during an outage.** On primary the `UserPromptSubmit` guard reads
the database inside its own 5 s, and the hook string and its timeout are
unchanged. Where that read fails (the database is down, or this workstation's
link to it is), the guard **refuses with 2** and does not read the files in its
place. This is Brett Heap's *"Block until link returns"* (2026-11-08T16:17:36Z,
opensoft/openRepoTools#165): until the link returns or an outage is declared,
every prompt is refused, and the next one is read afresh. The refusal names the
store, the cause and the two ways out:

```text
lanes-edit: … the register's SQL store did not answer the guard's read, so the ROW cannot be read and the triple cannot be verified, and an indeterminate read refuses … The two ways out, each from a shell or a `CLAUDE_NO_LANE=1` session: `lanes-index status`, which says whether this workstation reaches the database and what is in place for it; or, on Brett's word, `lanes-index rollback`, which takes the estate back to the files. …
```

- **Wait, and run `lanes-index status`.** It needs no lane session and no
  guard, and says whether this workstation reaches the database; send the
  prompt again once it shows that it does. Waiting costs nothing: no write is
  lost, since none is made.
- **On Brett's word, `lanes-index rollback`.** It takes the estate back to the
  files (see "Import and rollback"), run with `LANES_LANE` naming the operator
  lane and through that lane's own checkout. **It needs the database.** With
  the database lost for good the second way out is the disaster procedure (a
  restore, or a re-import, with every workstation's lanes paused), which the
  runbook below gives ("The database lost for good"), and not a rollback.

**A declared outage** is a third thing, and Brett's: when he knows that no
workstation can still write, `lanes-index outage declare --until <UTC> --why
<words>` makes the guard answer from the mirror until that time. That form
refuses, with 2 and nothing written, on a host from which the database answers.
A rollback declares one itself at its start. The guard then prints `read: mirror
at <sha12>, unknown (declared outage <utc>: <why>)`, and runs today's parser on
the mirror's text. It does that only where the mirror holds a `Register-Seq` at
least as high as the declaration recorded; a mirror behind it is refused with 2,
naming both seqs, and a mirror with no `Register-Seq` among its 200 newest
commits is refused too. **Every write, and every other deciding read, still
refuses.** `CLAUDE_NO_LANE=1` passes a prompt as it always did, and a session
started with `claude --safe-mode` is still no lane session.

**`commit` and hand edits are refused.** On primary `lanes/LANES.md`,
`lanes/log/`, `lanes/aliases.tsv` and `lanes/archive/` are a rendering of the
database, and a hand edit of them is never committed, kept or published. `commit`
**refuses with 2, writing nothing,** over a hand edit of the register or of any
file it renders: its carry form, the hand edit of Rule 9(d), is gone on
primary. So does every other write (`request-handoff` with 64), before it takes
its snapshot, while such an edit is pending in the writing lane's own checkout.
Each refusal names the file and prints a recovery that works; `commit`'s is
below, and a write's is the same but for its restore line, printed in
backticks, and its last line, "then make the change through its verb, and run
this write again":

```text
RECOVERY, in <the checkout>:
  read it:      git -C <the checkout> diff HEAD -- <the files>
  restore it:   git -C <the checkout> restore --source=HEAD --staged --worktree -- <the files>
  then make the change through its verb (set-row-state, replace-in-row, append-line, log …), which writes it through the register
```

It never names `commit` as the way out. `commit` still runs as the **publish
act**: for the queued commits that Amendment 23's S4 keeps, with its fence
narrowed to `handoffs/`, and for a person's own commits of `lanes/repos.tsv` or
`lanes/log/README.md` (a commit whose every path is one of the two, added or
modified as a regular file; one that holds either beside any other path is
refused, 2). A queued commit that changes any other path under `lanes/` is
refused as a divergence, and a push whose commit changes a path outside
`handoffs/` is refused at the push. Every refusal changes neither the register
nor the files.

**The files are a mirror, and it lags.** The renderer writes the files after
the write has committed, and no writer waits for it. So `lanes/LANES.md` and
`lanes/log/<lane>.md` on `origin/main` are behind the register by the writes the
renderer has not yet made: usually seconds, longer where a render failed or
stopped. **Decide nothing from them.** The verbs that give the truth are these;
a read that the mirror answered says so on stderr:

- `lanes-edit.sh who` and `who --landing`;
- `lanes-edit.sh history`, and `lanes` (both read the register where it answers,
  and the mirror, labelled, only where it does not);
- `lanes-index status`, which prints the switch, `n`, the generation, the
  mirror's last `Register-Seq` and its lag, any `diverged at`, the pending
  records and any declaration still in place.

A script of a lane that needs a register fact asks a verb (`register-row`,
`register-text`, `register-grep`, `who`), and never `grep`s the working tree or
runs `git grep` on `origin/<branch>`: on primary that copy may be behind.

**A claim's register seq.** A `claim` on primary has no commit of its own to
cite when it posts, since the mirror makes it later. It says
`lane <lane> now holds <object> (register seq <n>)`, and the Rule 1 comment says
`Logged in <the log> at register seq <n>` where it used to give a sha. `<n>` is
the write's sequence number in the journal, and it is the `Register-Seq: <n>`
trailer of the mirror commit that renders the write. To find that commit:

```sh
git -C <the checkout> log --first-parent origin/main --grep='^Register-Seq: <n>$' --format='%H %s'
```

It finds nothing while the mirror is behind the seq: `lanes-index status` says
by how many, and `lanes-edit.sh who <object>` already shows the claim.
`rename-lane` cites its seq the same way, where it cited a sha.

**The loser of a claim exits 2, not 7.** In files mode `claim` loses a race with
7 and writes `CLAIM-LOST`. On primary two claimants of one object cannot both
commit: the register decides, in one transaction, and the second **exits 2 and
writes nothing**. `CLAIM-LOST` is no longer produced (it stays in the grammar
for the history that has it), and nor is exit 9, whose recheck files mode makes.
The refusal prints the register's own words and, where the register can be read
again at once and shows the holder, the holder's line:

```text
ERROR:  LR005: the object is held (one_live_claim): …
DETAIL:  one-live-claim
held-by=<seq><TAB><path><TAB><the holder's line>
```

A script that tests `claim`'s 7 for "I lost" must read a 2 from `claim` on
primary as that refusal and take the holder's line from what is printed. Exit 7
is still the lifecycle and restart fences'. The same refusal ends a claim whose
spelling of the object meets another lane's live claim through any alias
(`LR005`, `claim-spellings`), so that no claim rests on one writer's
`repos.tsv` alone.

**The later `HOLD` or `LANDING` writer exits 2 too.** Amendment 10's `HOLD` and
Rule 6's `LANDING` on one repository's `main` are ordered by commit sequence:
the earlier wins, and the later writer's verb **exits 2, writes nothing and
prints, where the register can be read again at once and shows it, the earlier
line**, so that a landing script that checks the exit does not merge second.
One repository spelled two ways is one `main`. It is neither 0 nor 69, and it is
not the claim's 7:

```text
ERROR:  LR005: another lane holds main for this repository first (D18)
DETAIL:  main-held
held-by=<seq><TAB><path><TAB><the earlier line>
```

On primary a line in a taking shape (`HOLD — ` or `LANDING — `) is refused with
2 before anything is written where no repository can be read from it, or where
no UTC can be read from it, since Rule 6's 30 minutes are measured from that
UTC. `HOLD RELEASED`, `HOLD WITHDRAWN` and `HOLD EXPIRED` release a `HOLD`;
`(refresh)` and `(run id)` lines continue the original; a `LANDING` is released
by its own lane's `LANDED`, or, on Brett's word and once it is more than 30
minutes old by the **database's** clock, by one line:

```sh
LANES_LANE=<writer> LANES_SESSION=<uuid> lanes-edit.sh append-line --landing-expired <holder> --pr <n> --repo <owner/repo> --word '<his words>' --word-where '<where he gave them>' --word-utc <UTC>
```

It copies the holder's `LANDING`, refuses with 2 and writes nothing where that
`LANDING` is not open or is not past the 30 minutes, and prints `register seq
<n>: released lane <holder>'s LANDING of …`. A `LANDING EXPIRED` typed by hand
through `append-line` is refused on primary, naming this form, and the form
itself refuses with 2 off primary. A stale `LANDING` that no line releases still
takes `main`, and on primary `who --landing` stops listing one that a committed
`LANDING EXPIRED` released.

**`lane-start`'s seven read arms.** `lane-start` makes seven reads of the
register whose old arm noted a code it did not know and carried on. On primary
each of them can answer 69, 75 or 3, none of which is an answer about the lane.
Where the table gives the code, **`lane-start` refuses the launch with that
same code**, naming the read, before it writes a row, a stamp or a line:

| the read | what it would have told | refuses on |
|---|---|---|
| `canon-lane` | the register's spelling of the lane | 69, 75, 3 |
| `swapped` | the lane's directory from its swap record | 75, 3 (69 is a note) |
| `lane-dir` | the lane's directory from its log | 75, 3 (69 is a note) |
| `resolve-home` | the lane's recorded home | 75, 3 (69 is silent) |
| `session-lane` | whose session is live in this window | 69, 75, 3 |
| `lane-transcript` | the agent's own id in the lane's last handoff, read only for a launch of an agent other than `claude`, and not where that handoff names another agent | 75, 3 (69 is a note) |
| `lane-transcript` | the resume id in the lane's last `PAUSED` record | 75, 3 (69 is a note) |

```text
`<the read, as run>` exited 75: the register is busy, so <what it would have told> is NOT known, and that is not an answer. This launch refuses rather than carry on without it. Nothing was launched, renamed or written. A retry in seconds is safe.
```

For a 3 the words are "the register's answer is in doubt", and for a 69 "the
register could not be read" and "A retry is safe once the register can be read."
The two `lane-transcript` arms come after the window was renamed. Their refusal
gives the window back its name, says that this launch wrote no row, line or
stamp, and, where `request-handoff` answered 0 before it, names the line it
wrote, the `HANDOFF-REQUESTED` line or the forced `PAUSED`, which stands.
Every other arm of `lane-start` that already refused on a read passes the
code, but for the ownership read, `managed-projection`, whose refusal is 1,
UNKNOWN: an ownership nobody established is never read as `legacy`. And
`lane-start` refuses with 69, 75 or 3 where its base, `act-begin`, cannot be
read. The launch's own writes are different: `start-record` makes the row, the
session cell, the state and the `STARTED` or `RESUMED` line as one write, and
where it makes the lane's row (a new lane) a 69, a 75 or a 3 stops the launch with that
code. Where it makes no row (a resume, or a lane that already has one) it
prints a note, writes the Rule 3 stamp, and the launch goes on: a 3's note
points to the writer's printed `LANES_WRITE_ID` rerun, and on primary a 69's or
a 75's prints no rerun and names the same `lane-start` with `--no-launch`, to
run in the launched session's window as its first act. Off primary none of the
seven reads answers 75 or 3, and each answers 69 only where the tip it reads,
origin's as this checkout last fetched it, carries `lanes/STORE` or cannot be
read. A files-mode write's 3 is still "pushed, not confirmed", and takes the
same arms. And the name guard's detached bind (answer 2, "The switch and
`lanes/STORE`") stops on any code but 0 from `start-record`, whatever row it
makes: it writes no stamp, and the bind's record ends failed with that code.

**A keyword-form `url=` is refused.** `url=` and `reader_url=` have always been
a libpq URL, and #160's contract said so: it begins `postgresql://` or
`postgres://`. A value in libpq's keyword form (`host=… dbname=…`) is now
refused at load, naming the form it wants (the exit is 2 for most commands, and
differs for three, below):

```text
lanes-index: REFUSED: `url=` in <the file> is not a `postgresql://` URL. Every `psql` takes its connection from `PG*` variables mapped from a URL's parts (SVC-2), never from a connection string in its arguments
```

Every `lanes-index` command that reads the file refuses it, and the exit is 2
but for three commands, which follow their own contracts:

- `lanes-index journal --write-id`, the lookup of a write in doubt, exits **3**,
  since it looked nothing up: the write stays in doubt, and its pending record
  is kept;
- `lanes-index register read --for guard`, the guard's read, exits **1**,
  since 2 means only "too late to verify" there. `lanes-edit.sh` takes that,
  and the read door's 2, as a failed question and not a refusal: one warning,
  and files mode, where the marker check refuses on a SQL estate;
- `lanes-index set-mode index`, which a rollback's last steps run on each
  workstation, never refuses on the file's other keys: it writes the `mode=`
  line, exits 0, and then notes that the file is still refused, and why.

`lanes-edit.sh`'s mode query ends 2 as well, so a write runs in files mode with
a warning naming the failure; where the estate's register is SQL, the marker
check then refuses that write with 69. Rewrite the line as a URL. A URL that
carries a password in any spelling, or a query parameter other than `sslmode`,
`sslrootcert`, `sslcert`, `sslkey`, `target_session_attrs` and
`connect_timeout`, is refused the same way, and `connect_timeout` is at least 2.

**The exit table has the new rows.** "Exit codes", under "The object log
(Amendment 7)", is the one table of every code, and it takes feature 007's:
69 and 75 as rows of their own, and a clause each in the rows for 1, 2, 3, 7
and 9. It is there and not here so that one table says every code.

### `mode=primary`

`lanes-index.conf` (the file "The store and its configuration" describes, never
committed, a regular file with no group or other permission bit (0600 and 0400
both pass), never under `~/.agents/`) gains keys beside the old ones:

```text
mode=index or primary     default index: today's behaviour
register_url=<libpq URL>  this engineer's own writer URL for the register
register_passfile=<path>  its password file, with no group or other bit (0600 or 0400)
register_label=<label>    the label of the database register_url= reaches
register_schema=<name>    default lanes_register
render_key=<path>         the renderer identity's key file, with no group or other bit, where one is used
register_addresses=<n>    how many addresses the endpoint resolves to (default 1)
register_rtt_ms=<ms>      the measured round trip, for the timers
```

`url=`, `passfile=`, `reader_url=` and `reader_passfile=` keep their meaning for
the index. **The register writes with its own pair** and never with the
indexer's `url=`; `reader_url=` is required beside `register_url=`, since a read
never connects with a writer's credential. `lanes-index` is the one parser of
the file: `lanes-edit.sh` reads no key. Refused at load (2, nothing read or
written): `mode=primary` with SQLite; `mode=primary` with no `register_url=`;
`register_url=` with no `reader_url=`; a password in a URL; a `register_url=`,
or the `reader_url=` beside it, at a TCP host that is not loopback unless it
carries `sslmode=verify-full` and an `sslrootcert=` that names an existing file
(never `system`); a password file or key that is not a regular file, or that
group or others can reach; a query parameter outside the six. While
`RENDER_GIT_HALF` is `False` (below), the build's refusal of `mode=primary`
comes after the checks of a URL's form, password and query parameters and of a
password file or key, and before the rest of this list: a `mode=primary` file
with SQLite, with no `register_url=`, with no `reader_url=` beside it, or with a
URL that fails the TLS rule is refused for the build's reason, with 78 from
`mode` and `register read` and 2 from every other command. Every connection is
`psql` 15 or later, with its parameters in `PG*` variables in that child's own
environment and no URL in an argument list, straight to the primary's own
endpoint and never through a pooler.

**Where the mode is asked.** `lanes-edit.sh` asks `lanes-index mode` once per
writer, before it routes, clones, fetches or locks, and **only where a
configuration exists**: the file at the default path, or `LANES_INDEX_CONFIG`
set and not empty. With none, no `lanes-index` process starts, and the writers
and readers are the v1.1.0 tools' but for the marker check and the four
exceptions every mode has, the fourth a failed register read's 69 ("The
switch and `lanes/STORE`"). The answer is read with a 2 s timer, which
`timeout` holds where it is installed; without `timeout` the question
has no bound:

| the query ends | the writer does |
|---|---|
| `primary` | the primary branch below (a dry run, which writes nothing, makes one attempt that reads the register at its head and sends no commit) |
| `index` | files mode, as today, plus the marker check |
| 78, the build's own refusal of `mode=primary` | exits **69**, repeats its text, writes nothing |
| a configuration `lanes-index` refuses (2), a `lanes-index` too old to know `mode` (64), none to run (127), a crash, its 2 s timer, or an answer that is not exactly one line | files mode, with one warning naming the failure; the marker check then refuses on a SQL estate |

**A build can refuse `mode=primary`.** `lanes-index` carries a constant,
`RENDER_GIT_HALF`, which stays `False` until the renderer's git half is
installed, so that no write can reach a register this build could not mirror.
While it is `False`, `mode` and `register read` exit **78** (`EX_CONFIG`) with
the fixed text above, every other `lanes-index` command refuses a `mode=primary`
configuration with 2 and the same text, `lanes-edit.sh` exits 69 and repeats it,
a read answers as for a failed read (a read that decides answers 69, a listing
reads the mirror, the guard exits 2 and `managed-projection` answers 1), nothing
is written, and the files are never written instead. While it is `False`,
`cutover` and `rollback` also run only against a scratch schema (a rehearsal
names one in `register_schema=`, and the real `lanes_register` is refused, 2) and
only against an origin that is a local path or a `file://` URL, and `set-mode
primary` refuses. One change lifts it, and `lanes-index ready` fails an
environment that still carries it.

**A primary write.** On primary the writing verbs (`claim`, `release`, every
`log` verb, `set-row-state`, `append-line`, `add-row`, `rename-lane`,
`retire-rows`, `archive-rows`, `migrate-state-cells`, `append-session-id`,
`replace-in-row`, `request-handoff` and `start-record`) run their own code on a
scratch tree the register builds at its head, stop at the point where they would
commit, and send the change set as one transaction. They make no fetch, commit,
pull or push. (`commit` is not one of them: see "What changes for a lane".) They
read three things from the lane's own register checkout, `<W>-lanes/<lane>/`:
the author identity, any pending hand edit, and
`lanes/repos.tsv` and `lanes/log/README.md`. **A write makes no clone**, so
where that checkout is not there it refuses with 2 (64 for `request-handoff`),
nothing sent, naming the command that makes it:

```text
lanes-edit.sh lane-checkout <lane> --create
```

`LANES_NO_GIT=1` is refused on primary, since the write is its commit.
`LANES_NO_FETCH=1` skips nothing and weakens no switch check. `LANES_INDEX=off`
never skips the mode query or the renderer. A second register write in one run
exits 2, naming the first write's seq: one run makes one journal entry.

### The switch and `lanes/STORE`

The estate's mode is recorded twice: **a row in the database, the switch**, and
**a file in the workspace repository, the marker `lanes/STORE`**. The estate's
rule is that every writing workstation is `mode=primary`, or none is. The
switch holds the mode (`files` or `primary`), the marker's state (`absent`,
`adding`, `present` or `removing`), the database's label, the import commit and
its tree, the seq at which it was set and the register's **generation**. The
generation names one database for good: a replacement database after a loss
starts a new one (from its bootstrap or, for a restore, from its after-loss
cutover), and an id or a pending record from another generation is refused. The
marker is five lines, and holds no secret, no password and no password file's
path:

```text
store=primary
database=<label>
import=<the imported commit>
lanes-tree=<the tree id of its lanes tree>
utc=<UTC>
```

**Once `lanes/STORE` is on origin**, a workstation that is not `mode=primary`
refuses by what it does:

| the workstation runs | it does |
|---|---|
| a write, any writer, the publish act included | exits **69**, ``this estate's register is SQL; configure `mode=primary` ``, nothing written. The marker is read on the tree its fetch returned (under `LANES_NO_FETCH=1`, on origin's tip as last fetched; under `LANES_NO_GIT=1`, the working tree) |
| an act, or any read that decides | exits 69 the same way, naming how old the ref it read is |
| the guard | exits 2 with the same text |
| `lanes`, `history`, the `SessionStart` block | answer from the mirror, with `read: mirror at …` on stderr |
| `openRepoTools wip init` | refuses, commits nothing: wherever `lanes/STORE` exists, in every mode; and where a tree carries another file under `lanes/` and lacks `lanes/LANES.md` or `lanes/log/README.md` |

Between the marker and a workstation's own flip, every write that workstation
tries after its fetch exits 69.

**Where the marker is absent nothing changes.** The output and the exit codes of
every `mode=index` and every unconfigured workstation are unchanged, but for a
register read that fails and the three exceptions below; each writer gains the
configuration read and the marker check. The lifecycle scripts read the register
through `register-text` and `register-grep` in every mode, so a read through
them that fails ends 69 in every mode (the exit table's row 69): `lane-start`
and `lane-end` refuse with it, and the sweep deletes nothing on it, where
`lane-end` once read the file with awk and took a register it could not read
for no row. There are **three exceptions, in every mode**, and a lane meets them
before any cutover:

- **answer 2 of the name guard binds detached.** It returns at once with
  "BINDING", the bind runs in a detached child (`setsid`) as
  `lane-start --no-launch --bind`, one `start-record` write, and then a
  `set-row-state` that marks the row of the lane the window left `PAUSED`, and
  the next prompts say how it went (`BINDING IN PROGRESS` while it runs). A bind
  that failed renames the window back and keeps the offer; choice 2 is withdrawn
  after the third failure. This fixes opensoft/openRepoTools#347: a bind cut down
  by the hook's timeout partway, and a failed bind that could not be answered
  again;
- **`lane-start`'s three register writes are one**, `start-record`: in files
  mode one register commit and one push, on primary one transaction;
- **`wip init` refuses** in the cases above.

Every row records the mode that wrote it last, so a mixed history stays visible.

### The journal and the renderer

**One write, one journal entry.** On primary each write opens its own
transaction with its own writer login, needs no process but its own, and either
commits whole or changes nothing. The
transaction is serializable. A writer that loses its turn re-reads the committed
state and checks it again until its deadline; no writer in a burst is lost to a
fixed count of attempts, and the sequence number comes from the head row inside
the transaction, never from a database SEQUENCE. The journal holds, for each
write: its **seq**, its **write id**, the commit's UTC, the verb, the lane, the
workstation, the author, the digest of its arguments, **the commit message
exactly as files mode makes it** (`LANES(<lane>@<ws>): …` or `LOG(<lane>@<ws>):
…`), the line edits, a digest of each file it changed, and the act it belonged
to. Seqs follow commit order and run without a gap, and an entry can span
several files (`rename-lane`, `retire-rows`, a `LANDING` with its `LANDED`).
**Every UTC the register stamps is the database's clock, in whole seconds**, so
that Rule 1's four hours and Rule 6's thirty minutes are measured on one
clock; a UTC a caller composed inside `append-line`'s text, and `log PAUSED
--utc`, stay as written.

**Acts check what they read.** An act that decides by a read before its write
takes a base first, `act-begin`, which prints `<act-id>@<seq>:<generation>` on
primary and `<act-id>@files` elsewhere, and each write it makes names the
scopes its reads covered: a lane's row, its lane-kind log lines, its live
claims, the alias lines and the archive rows that name it, and the lines that
carry a session's id. A journal entry made after the base, outside the act, that
touched a named scope **refuses the write with 2, writing nothing**:
`expectation: <scope> changed at seq <k> by <lane>/<verb>`. The act's own earlier
writes never refuse it. A base taken off primary refuses before any database
call, and a base from another generation refuses at the write's snapshot,
before anything is sent. Run the act again from its start.

**The renderer.** After a write commits, its process starts `lanes-index render
--checkout <the lane's checkout>`, detached, without waiting, and the write
returns. The renderer commits **every seq the mirror lacks, in order, one commit
per write**: with that write's message, `Register-Seq: <n>` in the message's
final trailer block, and the author, committer and dates the journal recorded,
so that two renderers build identical commits. It renders only the **render
set**, `lanes/LANES.md`, `lanes/log/<lane>.md` (never `lanes/log/README.md`),
`lanes/aliases.tsv` and `lanes/archive/`, and `git log` of the workspace
repository stays the audit trail, in commit order.

- **It touches no HEAD, index or working tree.** It builds with a private index,
  in the checkout of the lane whose write started it, under Amendment 23's S3
  fetch, so it needs neither the lane's lock nor a clean checkout, and a lane's
  own stranded commit never stops the mirror. A render killed between its commit
  and its push leaves nothing on any HEAD.
- **A lost push race rebuilds; it never rebases.** The renderer discards its
  unpushed commits, fetches and renders what is still missing onto the new tip.
  A pass renders at most 64 seqs and makes at most six push attempts, and
  another pass follows only after one that moved origin's last `Register-Seq`, or
  for a rerun mark left while it ran, so a pass that cannot progress ends it.
- **One render at a time per checkout,** by an `flock` the kernel releases when
  its holder dies. A write that starts a render while one runs leaves a mark,
  and exits; the running render looks for a mark after it lets go, so the last
  write of a burst is rendered all the same.
- **It pushes by an explicit SSH URL in an environment of its own,** under the
  renderer identity where `render_key=` is set, writes nothing to the checkout's
  configuration, and proves the push by a fetch. `LANES_NO_FETCH=1` in the
  verb that started it is dropped.
- **An operator entry is not rendered by it.** The cutover's and the rollback's
  entries touch only `lanes/STORE`, and the command that wrote one pushes its
  commit; the renderer counts that seq as rendered once it is on origin.
- **A commit that touches none of the render set is part of the tip,** and
  stops nothing: a handoff, the cutover's commit that adds `lanes/STORE`, the
  rollback's that removes it, an edit of `lanes/repos.tsv`.
- **The index's own nudge is not made.** On primary a write does not re-index the
  mirror into the database it came from.

**The mirror is write-behind, and the lag is said.** A failed render leaves the
register true and the mirror behind; the next render catches up in order.
`lanes-index status` prints, for the register:

```text
register: <store> (mode=primary; schema lanes_register)
  switch:      primary, marker present (seq <n>, commit <sha12>); set at seq <k> on <UTC> by <lane> on <workstation>
  database:    <label>, repository <owner/repo>
  n:           <the journal's head>
  generation:  <the generation>
  mirror:      <what the mirror holds, and why not, where it is not read>
  lag:         <k> seq(s): n <n> less the mirror's <m>
  divergence:  none recorded
  renderer:    no failed pass recorded
  pending:     none in <the state directory>
  outage:      <a declaration still in place, or none>
```

`lanes-index reconcile` is the proof that the mirror equals the register: it
re-renders what is missing and imports nothing silently, and `reconcile
--dry-run` only reports. Without `--dry-run` it re-renders through a lane's own
register checkout, so `LANES_LANE` names that lane.

**A stray commit stops the renderer.** A commit that changes a path of the render
set and that the renderer did not make (a v1.1.0 `lanes-edit.sh` pushing to a
rendered file, say), or a `Register-Seq` above the journal's head (copied or
forged), is a **divergence**. The renderer renders nothing over it and stops,
and `status` prints `divergence:  diverged at <sha>`. **A person's word decides**
what becomes of that commit, and each way out is a command that journals one
entry and clears `diverged at`, then renders. Where that render ends well the
mirror is byte-identical to a full render; where it fails, the entry stands and
the command still exits 0, saying so, and `lanes-index status` shows the lag,
which the next write's render or `lanes-index reconcile` renders:

```sh
lanes-index import --single <sha>     # the stray's line edits become one journal entry
lanes-index render --revert <sha>     # the register's state is rendered over the stray
```

`import --single` refuses, naming `render --revert <sha>`, where the stray's
edits no longer apply to the register as it stands. A rollback refuses to start
while a divergence is recorded.

**The server-side gate.** It is optional, and provisioned outside this toolset:
no command of it installs, switches or reads the ruleset. Where the
organisation's plan supports it, a push ruleset whose only bypass is the
renderer identity protects the render set and
`lanes/STORE`, so that a stale install's push to a rendered path is refused
before it can stop the renderer. It does not cover `lanes/repos.tsv`,
`lanes/log/README.md` or any other path under `lanes/`. It is switched on only
inside the cutover's freeze, since it refuses every files-mode write, and taken
off during a rollback, after its render and before its push. Switching it,
either way, is Brett Heap's act. This manual does not establish that one is
installed: confirm it with Brett before relying on it.

### Which reads may use the mirror

On primary a read is one of six classes, and a hygiene test holds every
dispatcher arm of `lanes-edit.sh` to exactly one. **Only a read that decides
nothing may read the mirror.**

| class | the arms | when the register's read fails |
|---|---|---|
| **decide** | every arm that answers from the register and is not below: `who`, `binding`, `live-holder`, `next-free`, `register-row`, `register-text`, `register-grep`, `canon-lane`, the `lane-*` reads, `resolve-home` and the rest | exit **69**, nothing on stdout; never 8, never the files |
| **mirror** | `lanes` and `history`; the `SessionStart` block | `read: mirror at <sha12>, unknown (<why>)` on stderr, then the mirror's answer with the arm's own exit |
| **projection** | `managed-projection` | exit 1, UNKNOWN: **never `legacy`, and never 8**, for an outage |
| **guard** | `guard` | its own read; exit 2, or the mirror under a declared outage |
| **writer** | the write guard's verbs and `start-record` | their own transaction's codes (69, 75, 3, 2) |
| **none** | host-local facts, the filters that read stdin; `act-begin` and `guard-bind`, which the read door does not open for | no door read: `act-begin`'s own register snapshot that fails is 69, with nothing on stdout; `guard-bind` makes at most one journal lookup of its own, where its record names a write an earlier attempt left in doubt |

**A listing that a caller acts on is a deciding read.** `lane`,
`lane-end --retire-dormant` and `lane-end --sweep-checkouts` act on what `lanes`
answers, so they pass `lanes --decide`, and where the register cannot be read
they refuse with 69 and never act on the mirror's answer.

Three reads outside the mirror class read the mirror too, and each is labelled:

- **`lane-handoff` and the handoff skill** read the mirror for the reads that
  name the lane, with `LANES_READ_MIRROR=1`, so that the handoff file is still
  written during an outage. The `PAUSED` record's writes then exit 69 and print
  a note, and the lane **stays `SWAPPING`**, an interrupted swap that
  `lane-handoff --late --at <UTC> --lane <lane>` completes later. Under the
  same variable the ownership seam reads the mirror's marker, and still refuses
  on 0 and 1, so that an ownership nobody could establish is never read as
  legacy;
- **the launcher's bare window reads**, a workBenches change that the v1.3.0
  re-vendor carries, which pass `LANES_READ_MIRROR=1` to `swapped` and
  `window-lane`: they read the mirror, labelled, where the register cannot
  answer, and exit as in files mode;
- **the guard under a declared outage**, as above.

**Every read says which store answered when it is not the default.** `lanes`
answers `read: register at seq <n>` on stderr where the register answered, and
`read: mirror at <sha12>, unknown (<why>)` where the mirror did. `--index` on
primary changes nothing and prints no `read: index` line, since the default read
is already the database. A deciding read reads one snapshot per verb: the read
door makes one `lanes-index register read --prefetch`, proven by a digest of
each file in the same request, and every read site of the verb reads that cache
at one head `n`, never `origin/<branch>`. A site the prefetch did not cache
makes one more `register read`, and never a git read. The lifecycle scripts read
the register through `register-text` and `register-grep`, never by awk over the
file or `git grep` of the ref, and `lane-worktrees` deletes nothing on a read
that failed.

### Import and rollback

These are operator acts, Brett Heap's, or a lane's on his word, each run with
`LANES_LANE` naming the operator lane and through that lane's own checkout. None
holds a database transaction across a git act, and none puts a password in an
argument list, a URL or an environment a session inherits: each `psql` it runs
reads the configured password file, named in that child's own environment alone.

**Import.** `lanes-index import --at <commit>` reads the workspace repository at
the named commit, **origin's records only** (lanes publish or archive their own
unpublished records before the window), loads the register's tables and the
journal's first seq into a staging transaction it rolls back, renders the mirror
from SQL into a scratch tree, and **refuses with 2 unless the render is
byte-identical to the commit's `lanes/`**, naming the first path that differs.
Only then does it swap the tables in, in one short transaction. It is
repeatable, and runs into a scratch schema (`register_schema=`) as a rehearsal.
It keeps the 15 `WORD` lines of `lanes/log/codeXfactory-2.md`, whose UTC carries
milliseconds, verbatim and flagged, and keeps warning about them. A database it
cannot reach is 69, never 2, so the code alone tells "the files and the register
differ" from "the database is away".

**Cutover.** `lanes-index cutover --import <commit>`, after a byte-identical
import, in one short transaction sets the switch to `primary`, with the marker
`adding`, and writes the cutover's entry, the operator entry that adds
`lanes/STORE`, at the next seq (1 at a first cutover). **No write commits until
the marker's push is confirmed and recorded:** one tried meanwhile exits 69. The
cutover then pushes the marker commit with the renderer identity, as a
fast-forward of origin's tip, which must descend from the imported commit and
have its `lanes` tree, proves it by Amendment 23's S4 read-back and records the
marker `present`, and the flip of each workstation follows (`lanes-index set-mode
primary`, a command run once per host, which rewrites only the `mode=` line,
keeps the file's own mode (0600 where it was), and refuses unless the switch is
`primary`, its marker `present` and the fetched `origin/main` carries a
`lanes/STORE` naming this database). The label it sends comes only from `register_label=`. It **aborts if
`main` moved under `lanes/` since the import**: the switch goes back to `files`,
its entry is deleted and the head goes back to the import's seq, in one
transaction, and only after a read-back proves the marker absent from origin;
then the import is run again at the new tip. A push it cannot confirm is **3**,
takes neither branch, and its rerun reads origin back first. It refuses (2)
while a `HOLD` take more than 30 minutes old, or with no readable time, is open.
The first write after a first cutover is `Register-Seq: 2`.

**Rollback.** `lanes-index rollback` returns the estate to the files, from a
shell or a `CLAUDE_NO_LANE=1` session, with no lane session and no guard:

1. It **refuses to start while `diverged at` is recorded**, and fetches origin
   again right before its transaction to catch a stray not yet recorded: it
   records one and exits 2 with `diverged at <sha>`, writing nothing.
2. In one transaction it **sets the switch to `files`**, so that every SQL write
   refuses from that transaction on, and writes its last seq, `k+1`, the
   operator entry that removes `lanes/STORE`. Nothing is journalled after it.
   Its start declares the outage on its own host, as `lanes-index outage declare
   --rollback --mirror-seq <k>`, `k` being the head the transaction found, or
   prints that line where its configuration's directory cannot be written. Each
   other host runs the same line, so that its guard answers from the mirror once
   the switch reads `files`.
3. It renders until the mirror holds every seq before that one and is
   byte-identical to a full render.
4. The ruleset, where one is in place, comes off here, after the render and
   before the push, and removing it is Brett Heap's act. The rollback can be
   held at this point and run again, which resumes at the push.
5. It pushes the commit that removes `lanes/STORE`, with the renderer identity,
   and proves it by the S4 read-back: exit **3** for a push it cannot confirm,
   with a rerun that reads origin back first. The marker reads `removing` until
   the push is recorded, and no write commits meanwhile. Its last words are the
   steps that follow: `lanes-index set-mode index` on each configured
   workstation, which clears `mode=primary`; a files-mode write; and
   `lanes-index outage clear` on each.

**A rollback needs the database.** Its first step sets the switch there, so with
the database unreachable it **waits**: it refuses, and changes neither the
switch nor the marker, and a partition that cuts only the rolling-back host off
refuses it too. A database lost for good is the disaster procedure (the
runbook's "The database lost for good", below), not a rollback. When a
rollback has run to its end, the files are byte-identical to a
full render, the marker is gone, SQL writes are refused and **a files-mode write
succeeds**, while **a writer the rollback missed cannot write**: a primary
writer that was not cleared exits 69 (2 for `commit`) and the journal's count is
unchanged, after a fetch, with none since, and with `LANES_NO_FETCH=1` alike.

### Roles and logins

Every connection to the register logs in as one of the roles below, and each
role is refused outside its scope. `lanes-index bootstrap` makes the schema and
the four roles; `lanes-index login` makes each engineer's two logins and the
dump job's. Brett Heap runs both, from a shell on his own workstation and never
from a lane session, through his admin service.

| role | SQL role | logs in | made by |
|---|---|---|---|
| owner | `lanes_register_owner` | no | `bootstrap` |
| REGISTER-WRITER | `lanes_register_writer`, a group | no | `bootstrap` |
| READER | `lanes_register_reader`, a group | no | `bootstrap` |
| indexer | `lanes_indexer` | yes | `bootstrap` |
| an engineer's writer login | `lanes_w_<engineer>`, a member of REGISTER-WRITER | yes | `lanes-index login add <engineer>` |
| an engineer's reader login | `lanes_r_<engineer>`, a member of READER | yes | `lanes-index login add <engineer>` |
| the dump job | `lanes_register_dump`, a member of READER alone | yes | `lanes-index login add --dump-job` |
| admin | the database's owner, `lanes_register_admin`, which CNPG's `initdb` makes | yes | the install |

Each engineer, human or AI host, has two logins of their own. **The owner**
owns schema `lanes_register` and every table and function in it, and nothing
logs in as it: the admin takes it by `SET ROLE`. **The admin** is Brett's, with
`LOGIN CREATEROLE CREATEDB` and no `SUPERUSER`; it holds the owner and the
indexer by a GRANT the bootstrap makes first, and runs `bootstrap` and
`lanes-index login`.

**Who holds which:**

- **REGISTER-WRITER,** through the writer login of the workstation's engineer
  (`register_url=` and `register_passfile=`): `lanes-edit.sh`'s writes, through
  `lanes-index register snapshot` and `register commit`; the renderer, for its
  records of a divergence or a failed pass; `import`, `cutover` (its abort and
  its record of the marker included), `rollback` and the two ways out of a
  divergence; and `outage declare`'s probe of the database. It is also
  reserved for the managed ledger's projection into the register row, which
  v1.3.0 does not yet write.
- **READER,** through the reader login (`reader_url=` and `reader_passfile=`):
  the register service's one connection, the guard's direct read, the readers
  and the listings, `status`, `reconcile`, `set-mode`'s read of the switch, and
  `ready`. The `SessionStart` block makes no register call.
- **The indexer** (`url=` and `passfile=`): `lanes-index sync` and `reconcile`
  of the index half. Its one login is shared. On the register's database it is
  `lanes_indexer`, which owns `lanes_index`, and the index half's reads go
  through READER: the bootstrap makes neither of the two roles "The store and
  its configuration" shows, which are #160's.
- **The dump job:** the daily `pg_dump`, with a password that the job's own
  workload identity reads from a Key Vault secret into a 0600 password file of
  its run.

**The GRANTs:**

| on | granted |
|---|---|
| the database | nothing to PUBLIC; CONNECT to the two groups and the indexer |
| schema `lanes_register` | USAGE to the two groups, and SELECT on every table, now and, by default privilege, on a later one; READER may also read the schema's sequences, which `pg_dump` needs |
| its functions | nothing to PUBLIC, now and by the owner's default privilege; REGISTER-WRITER may execute `commit_write`, `switch_to_files`, `record_marker`, `find_write` and `record_mirror_state`, and both groups `render_file` |
| schema `lanes_index` | owned by the indexer; USAGE and SELECT to READER; nothing to REGISTER-WRITER |

REGISTER-WRITER has no INSERT, UPDATE, DELETE or DDL anywhere: a write is a
call of one of its five functions. The indexer has nothing in `lanes_register`.
`ready`'s check 4 tries, as each of the engineer's two logins, a write outside
its scope, and fails unless the database refuses it with SQLSTATE 42501.

**Each login carries its group's floors itself,** since PostgreSQL applies a
role's settings only to a session that logs in as that role, so a group's
settings reach no member. `lanes-index login` sets them on each login it makes,
and `ready`'s check 12 reads each login's own and fails one that lacks them:

```text
lanes_w_<engineer>   lock_timeout=2000ms  statement_timeout=5s  idle_in_transaction_session_timeout=5s
lanes_r_<engineer>   default_transaction_read_only=on  lock_timeout=1000ms  statement_timeout=1500ms
```

**One engineer's file.** Each workstation's `lanes-index.conf` names, of the
register's logins, only its own engineer's two, each as the user of its URL,
and their two password files, each holding only its own login's line. No
workstation holds another engineer's line. Where its index half is on the
register's database, it also names the indexer's one shared login, in `url=`
and `passfile=`. `<endpoint>` is the register's DNS name (the runbook, below):

```text
register_url=postgresql://lanes_w_<engineer>@<endpoint>:5432/<database>?sslmode=verify-full&sslrootcert=<root file>
reader_url=postgresql://lanes_r_<engineer>@<endpoint>:5432/<database>?sslmode=verify-full&sslrootcert=<root file>
```

**`lanes-index bootstrap`** makes the register's schema and roles from the admin
connection, used once and held by nothing:

```sh
lanes-index bootstrap --admin-service '<service>' --label '<name>' --repository '<owner/name>' --lane '<name>'
```

- **`--admin-service`** names a libpq service in Brett's own
  `~/.pg_service.conf`, with its password in his own 0600 `~/.pgpass`, so
  nothing on the command line is a connection string. `psql` receives it only
  as `PGSERVICE=<service>` in its environment, never as `service=` or `-d` on a
  command line, and every inherited variable whose name begins `PG`,
  `PGPASSWORD`, `PGPASSFILE`, `PGSERVICEFILE` and `PGSYSCONFDIR` among them, is
  dropped first, so only his own `~/.pg_service.conf` defines the service, and
  no secret comes from the environment.
- **`--label`** is the database's first label, with no credential, which it
  writes into the switch it creates. **`--repository`** and **`--lane`** name
  the workspace repository the register serves and the lane or person running
  it.
- **What it does:** it creates schema `lanes_register` with its tables, or
  migrates it forward, never down; makes the owner, the two groups and the
  indexer's login with the GRANTs above; confirms that the indexer cannot write
  the register's tables; and writes the switch at `files`, with the marker
  `absent`, its label and a new generation, and no journal entry. Each step is
  one transaction, and a second run prints `unchanged` for every object.
- **What it prints:** one line per object, `created` or `unchanged`; the
  `lanes-index.conf` lines, with no secret, `register_label=` among them; and,
  where it creates the indexer's login, that login's password-file line, once.
  **It refuses when stdout is not a terminal,** unless `--no-passwords` is
  given for roles that log in by peer, trust or certificate, when it makes and
  prints no password; so no lane transcript, log or pipe ever holds a
  password, and it sets each password as a SCRAM-SHA-256 verifier computed on
  the client, never as clear text in SQL.
- **A v1.3.0 limit:** the indexer's password-file line is printed only after
  its step commits; where the response is lost after that commit, a rerun
  prints `unchanged` and no line, and Brett resets the password by hand with
  psql's `\password lanes_indexer` through his own admin service, then writes
  the indexer's password file again.
- **With the switch at `primary`,** as on a restored backup, it verifies,
  applies a pending migration and writes no label, and changes nothing else.
- **Exits:** 0; 2 for an argument that carries a connection string or a
  password, an admin that may not create roles or schemas or does not own the
  database, a schema newer than this build's, a role that holds a privilege
  outside its scope, an existing `lanes_indexer` that cannot log in, before any
  write and under `--roles-only` too, `store=sqlite`, or stdout that is not a
  terminal without `--no-passwords`; 69 where the admin connection fails or the
  database refuses a step, a migration's lock wait among them, which is run
  again; 64 for usage.
- **`--roles-only`,** for the disaster path, makes only what a database dump
  does not carry: the roles, the admin's membership in the owner and the
  indexer, and three database-level statements, `REVOKE ALL ON DATABASE … FROM
  PUBLIC`, `GRANT CONNECT` to the two groups and the indexer's login, and the
  owner's global default privilege that revokes EXECUTE on functions from
  PUBLIC. It is safe to repeat, and prints `unchanged` where the roles are
  already in place.

**`lanes-index login`** makes and rotates the logins, and revokes an
engineer's two, from Brett's shell after the bootstrap, through the same
admin service and under the
bootstrap's rules for a secret: `add` and `rotate`, which print a password,
refuse when stdout is not a terminal, and it sets each password as a
SCRAM-SHA-256 verifier computed on the client.

```sh
lanes-index login add <engineer> --admin-service '<service>'
lanes-index login add --dump-job --admin-service '<service>'
lanes-index login rotate <engineer> --admin-service '<service>'
lanes-index login rotate --dump-job --admin-service '<service>'
lanes-index login revoke <engineer> --admin-service '<service>'
```

- **`add <engineer>`** makes `lanes_w_<engineer>` and `lanes_r_<engineer>`, each
  with a password it generates and its group's floors, and prints, once, their
  two password-file lines and the `register_url=` and `reader_url=` lines for
  that engineer's file. Run again, it changes nothing but a lost membership,
  which it puts back. **`add --dump-job`** makes `lanes_register_dump` alone,
  whose password Brett stores as the Key Vault secret the dump job reads.
- **`rotate <engineer>`** sets new passwords for both logins and prints the new
  lines; a session already open goes on until it ends. Until that engineer's
  two password files carry the new lines, that workstation's new connections
  fail at login, 69, so Brett writes them before its lanes resume.
  **`rotate --dump-job`** rotates the dump job's password alone.
- **`revoke <engineer>`,** for each of the two logins in turn: `NOLOGIN`, then
  its membership revoked, then its sessions ended, each step's result printed.
  A revoked login's new connection is refused (FATAL 28000), an open session's
  next statement is refused (42501), and an ended session gets FATAL 57P01. A
  write of that engineer's that the revoke cuts mid-commit is in doubt; its own
  lookup cannot reconnect, and the journal answers it by its write id.
- **Exits:** 0, each step's result printed; 2 for an engineer name that is not
  `^[a-z][a-z0-9_]{0,40}$`, an argument that carries a connection string or a
  password, or, for `add` and `rotate`, stdout that is not a terminal; 69 where
  the admin connection fails or the database refuses a step; 64 for usage,
  `rotate` with neither an engineer nor `--dump-job`, or with both, and
  `revoke` with `--dump-job` or with no engineer, among it. The dump job's login
  is rotated, never revoked, by this command.
- **What a restore keeps.** A database dump holds no login, membership,
  verifier or floor, and a physical backup of the cluster holds them all. After
  a point-in-time restore each login is there with its old password, and `login
  rotate` gives each a new one; on a new Cluster restored from a dump, or
  re-imported, `bootstrap --roles-only` makes the groups and `login add` makes
  each engineer's logins again ("The database lost for good", below).

### What the REGISTER-WRITER secret can reach

Plainly, and with no protection claimed that the design does not have: **the
REGISTER-WRITER secret on a workstation, the password of its engineer's writer
login `lanes_w_<engineer>` in the 0600 file that `register_passfile=` names, is
readable by every lane process on its host. Rule 9(e) alone forbids a hand-run
SQL write with it, and nothing technical prevents one.** The exposure is the
same as today's git push rights.

- **A hand-run write is still a register write, and still forbidden.**
  REGISTER-WRITER holds no INSERT, UPDATE, DELETE or DDL, only its five
  functions, so a write made by hand is still journalled, rendered, held to the
  register's rules and proved. That does not prevent one.
- **A holder can stop every write.** A call of `commit_write` with a `rollback`
  entry stops every write with 69 until an operator acts. `switch_to_files`
  only undoes a cutover that is not yet confirmed.
- **The reader login's password is in the same position,** in the 0600 file
  that `reader_passfile=` names, on the same host.
- **Two logins per engineer bound the reach.** The secret on a workstation is
  its own engineer's, and revoking that engineer's logins ends their reach
  alone. The indexer's one login stays shared.
- **No secret is in an environment a session inherits.** `lanes-index` hands
  the password file to its own `psql` child through `PGPASSFILE`, and drops an
  inherited one; no `lanes-index` path puts a connection string in an argument
  list, where `/proc` shows it for the process's life.
- **The render key reaches as far, and matters more.** Where a push ruleset is
  in place ("The server-side gate"), the renderer identity's deploy key, in the
  0600 file that `render_key=` names, is the ruleset's only bypass, and every
  lane process on its host can read it too, so any of them could push to the
  rendered paths past the gate. The gate stops a stale or careless writer,
  which pushes with the lane's own rights; it does not stop a process that
  reads the key on purpose.
- **The admin's credential** is a libpq service and a 0600 `~/.pgpass` of
  Brett's own, named by `--admin-service`: never a password or a connection
  string in an argument or the environment, and never stored by the tools. The
  dump job's password is a Key Vault secret that the job's own workload
  identity reads.

### The socket API

**The register service answers the register's text at `n`, and the reader
decides.** It is `lanes-register`, the `lanes-index serve` subcommand: one
service per environment (a bench container or a host shell), only on a
`mode=primary` workstation, holding one connection to the database as the
environment's reader login. For each request it reads the files asked for at
the journal's head `n`, fresh, and the reader runs today's parser on that text
in its own process. It answers no question by key: `who`, `binding`,
`live-holder`, `next-free` and `managed-projection` reach it as the files their
parser reads. **No write depends on it:** a write's snapshot and commit open
their own connections with the writer login, and never wait for or start the
service.

**Who asks it.** `lanes-index register read`, every reader's path, asks the
socket first and reads the database directly when the service is unavailable:
the readers' prefetch and their second read, and the guard's prefetch,
`register read --for guard --prefetch`. These never ask it:

- the guard's second read, of one log at the head it prefetched, which always
  reads directly, since a request cannot name an `n`;
- the `SessionStart` block, which only starts the service, after its own
  answer and without waiting, and asks it nothing;
- every write's own snapshot and commit, which open their own connections.

A writer's reads after its commit, its tail's, are readers' reads and ask it
as a reader's do. `request-handoff`'s wait on primary is the tail that always
does, its polls reading through `lanes-index register read --since`. Others
read too, among them: the lifecycle follow-up of a `log ENDED` or `RETIRED`,
which reads the lane's log at most once; `retire-rows`, which reads each
retired lane's log; and `rename-lane`'s comments, unless `--no-github` or
`LANES_NO_GITHUB=1`, which read every lane's log.

On a workstation that is not `mode=primary` no client asks the socket.

**Where it lives.** One path, with no choice:
`${XDG_STATE_HOME:-~/.local/state}/openRepoTools/run/register.sock`. Its
directory holds five files, `register.sock`, `register.lock`, `register.owner`,
`register.failed` and `register.log`, and nothing secret: `register.log` and
every `refuse db` answer carry fixed phrases and the SQLSTATE only, never
`psql`'s message text and never a URL.

- **The directory is checked on every use,** by the service before it binds
  and by the client before it connects: a directory and not a symlink, owned by
  this uid, mode 0700. `lanes-index` creates it that way. One that fails is
  never used and never repaired: the client reads directly, `serve` exits 2
  naming it, and `lanes-index status` reports it.
- **A path over 103 bytes,** encoded, is never shortened: the client reads
  directly and starts nothing, and `serve` refuses it with 2 and the stamp
  `path-too-long`.
- **Who may connect: this uid only.** The service reads `SO_PEERCRED` on each
  connection and closes one of another uid; the client reads it on its socket
  and treats a server of another uid as unavailable. On macOS
  `LOCAL_PEERCRED` takes its place, untested; where neither is available, the
  directory's owner and mode are the whole protection. No second uid has been
  available to show the service's refusal with a real other user.
- **The owner file,** `register.owner`, holds the service's pid, its start, its
  install's identity and, on Linux, its pid namespace and boot id. A client
  whose own namespace or boot differs, or that cannot use the file, treats the
  service as unavailable and starts none. A client that finds another install's
  file starts a service where the server's uid is its own (the uid check wins),
  and an old service whose own files changed answers `unavailable restarting`
  and exits.
- **Trust rests on the checked directory, the owner file and the uid check at
  both ends,** not on the digests in an answer, which prove only that the bytes
  arrived as sent.

**The request names files, and nothing else:** the render set's files it wants,
singly or by the sets they belong to, or the render set's listing at `n`. It
never carries a session id, a working directory, an agent id, a prompt, a
window, a live record or a liveness fact, and never an `n` or a generation.
Host-local facts stay with the client, and the guard decides.

**The three answers:**

- **`text <n> <generation>`,** then, for each file, its path, its byte count,
  its digest at `n` and its bytes. Each request is one transaction at
  REPEATABLE READ READ ONLY, which reads the head, the digests and the switch,
  and checks the schema version, so the files of one answer are never from two
  seqs, and a kept image is answered only where its digest is the one at `n`.
- **`refuse db <cause>`,** only for a value the request's own transaction read:
  `switch-files`, the switch not `primary`, or `schema`, the tools' schema
  version outside the database's window. The format also names `auth` and
  `unreachable`, but this service never sends them, since it connects only in
  the background, never on a request's time. The client then refuses exactly as
  a direct read would, and makes no direct read after it.
- **`unavailable`,** with `slow` or `restarting` where they apply, never leaves
  the client: it discards the answer and reads directly, within what its budget
  has left, and writes the flag `.direct`, so every later read of that
  invocation goes straight to the direct read. The client also counts as
  `unavailable`: no socket, or a dead one; a failed directory or path check; an
  owner file it cannot use, or one of another namespace or boot; a server of
  another uid; a service that accepts and never answers; and an answer that is
  malformed, over 16 MB, or not complete within its wait.

| what happened | a reader's `register read` | `register read --for guard` | the guard |
|---|---|---|---|
| the text at `n`, through the service | 0 | 0, the files cached | parses, and decides 0 or 2 |
| `unavailable`, then a direct read that succeeded | 0 | 0, the files cached, and `guard: direct (service unavailable)` on stderr | parses, and decides 0 or 2 |
| `refuse db <cause>` | 69 | 69; first on stderr `lanes-index: refuse db <cause> <SQLSTATE>`, and for `schema` a line naming the numbers next; no fallback line | the declared outage's check; otherwise 2 |
| `unavailable`, then a direct read that failed | 69 | 69; first the direct read's cause line, and for `schema` the numbers' line next, then `guard: direct (service unavailable)` | the declared outage's check; otherwise 2 |

The guard's prefetch prints the fallback line once, wherever the service was
unavailable, whether the direct read that follows succeeds or fails, and never
after a `refuse db`. Through the service and through the direct read the
guard's stdout, its stderr less that one line, and its exit are the same. For
`schema`, the numbers' line names the tools' version, the database's two, and
the remedy: "upgrade the tools" where the tools are below the window, and "the
database is behind: run the bootstrap" where they are above it.

**The limits:**

- **The client's wait,** connect and answer together, is 300 ms, or
  `register_rtt_ms=` plus 200 ms where that key is over 100. It is one share of
  the guard's 5 s, spent once; the first fallback's `.direct` means a silent
  service costs one wait a prompt, never one a read.
- **The service's timer** is 200 ms a request from its arrival, or
  `register_rtt_ms=` plus 100 ms above 100, with the server's own bounds just
  inside it, `lock_timeout` 150 ms and `statement_timeout` 180 ms. At its expiry
  the service cancels the query and answers `unavailable slow`.
- **16 MB** an answer: the client reads at most that, and the service builds no
  answer past it, answering `unavailable` instead.
- **At most 16 connections at once,** with a backlog of 64. A connect that is
  not taken is unavailable, and the client reads directly and starts none.

**Its connection.** One long-lived `psql` child on pipes, logged in with
`reader_url=` and `reader_passfile=` from the configuration, its connection in
its own environment and never in its arguments, every inherited `PG` variable
dropped first. It connects at `c` for each of the endpoint's addresses, straight
to the primary's port 5432 and never through a pooler, and **only in the
background**: by its first request at the latest, and again at the next request
after a failure met while serving, so a request that comes before it connects
is answered `unavailable`. While no request comes it sends `SELECT 1` every 60
s, and a failed check starts a new child at once. It never writes, renders,
re-indexes, or reads the mirror or the outage declaration.

**Started on demand, with no supervisor of its own.** No systemd and no launchd:

- **Who starts it:** the `SessionStart` block on a primary workstation, through
  `lanes-index serve --start`; and any client that finds no socket, a dead one,
  or an owner file from another install on a socket whose server is of its own
  uid, unless a failure stamp younger than 60 s stands. Never the guard's second
  read, and never a write's own snapshot or commit; a writer's tail reads as
  any client does, and `request-handoff`'s wait is one. A call that finds no
  socket, or a dead one, reads directly, and a service it starts answers from
  the next call. A call that finds another install's owner file on such a
  socket still sends its request: it takes that service's text or `refuse db`
  answer, and reads directly only where the answer is `unavailable`.
- **How:** detached, from Python and never bash (`setsid`, its input and output
  on `/dev/null`, its errors to `register.log`), with a reduced environment of
  `PATH`, `HOME`, `LANG`, the `XDG_*` variables, `LANES_INDEX_CONFIG` and, where
  they are set, the two test-only variables below, and no password and no URL.
- **One instance per environment:** `serve` holds `register.lock` for its life,
  and a second starter that finds it held exits 0.
- **It stops** after 900 s with no request, removing its socket. On a request
  that finds its own install's files changed, or its configuration now refused
  or no longer `primary`, it answers `unavailable restarting` and exits. Any
  other change to the configuration, or to the reader password file or a TLS
  file the reader URL names, ends the held child, and the service answers
  `unavailable` until a new one connects with the new settings.
- **A v1.3 limit:** where a client of one install finds a service of another
  install running (a checkout run by hand is one case), the `serve` it starts
  exits at the held `register.lock`, and the other service answers it with that
  install's code until that service stops for another cause. The remedy: stop
  the other install's service, whose `pid=` is in the owner file, or do not run
  two installs against one register at once.

**Only on primary.** `serve` refuses with 2 unless the configuration says
`primary`, and while the build refuses `mode=primary` it exits 2 at load, so the
service is dormant on every host until that refusal is lifted.

**The failure stamp,** `register.failed`, records only the causes that make
`serve` exit before it listens, each with 2: `path-too-long`, `config-refused`
(a refused configuration, a mode other than `primary`, or a URL parameter it
does not map) and `no-psql` (no `psql`, or one older than 15). It holds the UTC
and the cause, nothing else. Every start reads it first, and one younger than
60 s holds back that start alone: a client that found no socket, or a dead
one, reads directly, and one that found another install's owner file takes
that service's answer, as above. A database that refuses or does not answer
is never stamped: the service listens, answers, and reconnects in the
background. The service removes the stamp once it listens, and again at its
first good connect. `lanes-index status` reports a stamp and its cause, and a
directory that fails its check.

**`lanes-index serve --start`** runs the start a client runs on finding no
socket and returns at once; it asks the service nothing and waits for nothing.
It exits 0 where it launched `serve` or a stamp younger than 60 s stands; 2 for
a refused configuration, a mode other than `primary`, a failed path or directory
check, a `serve` it could not launch, or a platform with no Unix socket; and 64
for usage. The `SessionStart` block exits 0 always, and never reads that exit.

**Two test-only variables,** `LANES_REGISTER_SERVE_IDLE_CHECK_S` (the `SELECT 1`
interval, 60 by default, ignored at 240 or more) and
`LANES_REGISTER_SERVE_IDLE_STOP_S` (the idle stop, 900 by default), are
honoured only where set, as one to nine ASCII digits, leading zeros counted,
naming whole seconds of at least 1; any other value, ten digits or more among
them, is ignored, and the default kept, so the idle stop honours at most
999,999,999 s.

### The runbook: acts 3 and 4, and the rollback

This is the operators' procedure, Brett Heap's acts and the operator lane's:
the acts that give the estate its database (act 3), move the register into it
(act 4), and take it back to the files (the rollback), or replace a database
lost for good. A lane meets it as the notices and the prompts it names. Each
step gives who acts and where, the command, its check, its refusal and its way
back.

**Who acts, and the words.**

- **Brett** gives every word: the window, the freeze, the cutover, the
  rollback, an outage's declaration and each merge. He does act 3's
  infrastructure and the per-host flips. A word is a turn he typed, and the post
  that acts on it records it; an accepted prompt suggestion is not a word.
- **The operator lane,** which Brett names, runs the `lanes-index` commands the
  steps below give it from its own register checkout, `<W>-lanes/<lane>/`, and
  never from `W`; Brett's own, `bootstrap`, `login`, `set-mode` and `outage`,
  run from his shell or the host, as each step says. `import`, `cutover` and
  `rollback`, the disaster path's `import` and `cutover` among them, run with
  `LANES_LANE=` the operator lane, through that checkout, so each push they make
  is a lane's write; a shell or a `CLAUDE_NO_LANE=1` session that runs one names
  the operator lane the same way.
- **The coordinator,** opensoft/openRepoTools#165's lead lane unless Brett
  names another, posts the notices below and keeps the runbook's record: one
  #165 post per step that changes state, quoting the output it rests on.
- **Each lane** drains, resumes, and checks its own records when it is told to.

**Every `ready` runs with the installed `PATH`,** in act 3 and after, never with
a checkout's `lanes-index` first on it: its check 1 reads the release's list,
`openRepoTools-<release>.sha256`, beside the `lanes-index` that `PATH` names,
where the install places it, and a checkout's directory holds no release list.
`lanes-index ready` makes fourteen checks, each a read that writes nothing:

1. the installed tools' digests equal the release's list, which names
   `lanes-index` and `lanes-edit.sh` at least;
2. `psql` 16 is present;
3. the configuration is readable, has no group or other permission bit, lies
   outside every git work tree and `~/.agents/`, and says the expected mode;
4. each of the engineer's two logins connects within the workstation's `c`, at
   each address the endpoint resolves to, and a write outside its scope, under
   `BEGIN READ WRITE` in a transaction that always rolls back, fails with
   SQLSTATE 42501;
5. the installed `lanes-index` no longer refuses `mode=primary`;
6. the workstation's name is set, so no write is stamped with a container's
   hostname;
7. no outage declaration is recorded;
8. with `--expect primary`, the switch is `primary` with the marker `present`,
   and the fetched `origin/main` carries a `lanes/STORE` naming this database;
9. the tools' schema version lies inside the database's window;
10. with the switch at `primary`, whatever `--expect` says, `register_label=`
    equals the switch's label, or it fails with `register_label=<x>, but the
    switch names <y>`;
11. each login's connection carries `sslmode=verify-full` and an `sslrootcert=`
    (never `system`), its own session shows `ssl`, and it reaches the primary;
12. each login carries its group's floors;
13. the endpoint resolves to no more addresses than `register_addresses=`;
14. git is 2.32 or later.

Each check prints `check <n>: ok` or `check <n>: FAIL: <reason>`, in order, and
a last line names the environment, the host, the workstation and the tools'
version; with all fourteen passing the summary reads `14 of 14 checks ok`. It
prints no secret, and changes nothing. **Exits:** 0 where every check passed, 2
where any failed, each failure named, and 64 for usage; a database it cannot
reach fails check 4, so it exits 2, never 69. It checks one moment, so act 4
runs it again at the window, and again after any recreate.

#### Before act 3

- Feature 007's last code group and v1.3.0 have landed, and the hosts are
  reached, so the installed `lanes-index` no longer refuses `mode=primary`.
  Act 3's first two steps need only `bootstrap`, so Brett may run them earlier,
  from a host shell.
- PostgreSQL 16 for the bench images rides act 3's one rebuild (step 4).
- **The codeXfactory prod install** provides the database: CNPG in that
  install, on the prod AKS cluster, reached without a VPN through a public load
  balancer limited to the engineers' addresses, with TLS by cert-manager, on
  one node with no HA. It is built in its own repository, under its own gates,
  and act 3 waits for the install, not for the merge or the archive of the
  change that makes it: it starts when each item below exists and Brett has
  recorded item 9 on #165.
  1. **The operator:** CNPG 1.30 or later, whose watch list names the
     register's namespace, and the Barman Cloud plugin, 0.15.1 or later.
  2. **The Cluster:** one CNPG `Cluster` in a namespace of its own, PostgreSQL
     16, `instances: 1`, `enableSuperuserAccess: false`; `bootstrap.initdb`
     makes the database `lanes_register` and its owner `lanes_register_admin`,
     whose password is a Key Vault secret, and one `managed.roles` entry gives
     that owner `login`, `createrole`, `createdb` and membership of
     `pg_use_reserved_connections`, the only role it manages (the logins are
     `lanes-index login`'s); `reserved_connections` 2; the server's TCP
     keepalives (`tcp_keepalives_idle` 60, `tcp_keepalives_interval` 10 and
     `tcp_keepalives_count` 6), so that no connection is silent for the load
     balancer's 4-minute idle reset; `log_connections` and
     `log_disconnections` on; TLS 1.3, CNPG's default; `pg_hba` rejecting every
     connection without TLS, then admitting the two groups' members, the
     indexer and the admin to `lanes_register` by SCRAM-SHA-256, ahead of
     CNPG's default line; and no pod disruption budget, so a drain, a node's
     restart or a parameter that needs a restart is an outage until the
     instance runs again.
  3. **The endpoint:** a `LoadBalancer` service on port 5432 in front of the
     primary, its source ranges each engineer's egress address or range (a
     workstation that joins is added before its act 3 steps),
     `externalTrafficPolicy: Local`, a static public IP, and one DNS name,
     `register.codexfactory.opensoft.one`, with one A record that Brett makes
     by hand, and no AAAA, so the endpoint has one address. Never a pooler.
  4. **The certificate:** cert-manager's, for that name and the `-rw`
     service's in-cluster names, from the install's private CA, the interim one
     until the prod OpenXPKI exists, served by CNPG with the leaf and every
     intermediate, and renewed before it expires. Its issuer's root is the file
     every workstation names in `sslrootcert=`.
  5. **The physical backups:** a Barman Cloud object store in the prod
     subscription, the Cluster's WAL archived at least every 5 minutes, and a
     daily `ScheduledBackup`.
  6. **The daily dump:** a `CronJob`, once a day, under a workload identity of
     its own that may only read the dump login's Key Vault secret and write to
     the dump's container, which has soft delete and deletes each dump after
     30 days. It connects in-cluster with `verify-full`.
  7. **The network policy:** ingress on 5432 from the load balancer's source
     ranges, the dump job and the operator, and on 8000 from the operator, and
     from nothing else.
  8. **The server's size,** set once with room, since changing
     `max_connections` restarts the one instance:
     `max_connections` ≥ ⌈1.25 × (Σ_w (S_w + 2·L_w) + I + 2) + C_op + 3 + 2⌉,
     rounded up to a multiple of 10, where `S_w` is the environments on
     workstation `w` that run the service, `L_w` the lanes that can run at once
     there, `I` the workstations whose indexer syncs, 2 the dump job and one
     operator command, `C_op` the connections the operator and the backup
     plugin hold, read at act 3, and 3 and 2 the superuser's and the admin's
     reserved slots. Four engineers with three environments and eight lanes
     each, with `C_op` at 5, give 120. Its memory is at least `shared_buffers`
     plus `max_connections` times the memory one connection takes, with room:
     to start, a 1 GiB limit with `shared_buffers` at 128 MB for
     100 connections, raised with `max_connections`. Its CPU request is 100m
     and its limit 1, and its storage 8 GiB on the cluster's premium class.
  9. **What it hands back,** for Brett to record on #165: the endpoint and its
     address; the root file; the owner's name and the Key Vault secret its
     password is in; the namespace and the Cluster's name; the source ranges;
     and when the operator, the plugin and the backups were first seen
     working.

#### Act 3: the database, the logins and the tools

1. **The database.** Brett: the install creates it, to the items above, and he
   records on #165 item 9, the backups and the daily dump, and his downtime
   limit, **4 hours**.
   - **Check, on the cluster:** from a workstation inside the source ranges,
     `psql` with `verify-full` reaches the endpoint, and from outside them the
     connect times out; `pg_stat_ssl` shows `TLSv1.3`, and the server sees the
     workstation's own address; the owner can create roles and databases, is
     no superuser, and is a member of `pg_use_reserved_connections`; the
     server's parameters read as set, and a session idle for five minutes
     still answers; `sslmode=disable` is refused; the Cluster shows a completed
     backup and a current WAL archive; `pg_stat_activity` at rest gives
     `C_op`; and the instance's restart is timed. **A restore drill,** on
     Brett's word, which he gave ("Yes, drill at act 3 (Recommended)"): a
     point-in-time restore from the backups into a scratch Cluster, whose
     roles, verifiers, memberships and floors equal the source's, checked
     against the live register and timed against his 4 hours.
   - **Each workstation's timers,** measured as it joins: `c`, the URLs'
     `connect_timeout=`, as `max(2, ⌈t_c + 1 s⌉)`, where `t_c` is the p90, not
     the median, of 50 cold connects, each a timed `psql -Xqc 'select 1'`
     under `verify-full`, 1 s apart; `register_addresses=`, the distinct
     addresses that `getent ahosts <host>` prints on its `STREAM` lines; and
     `register_rtt_ms=`, the p90 of 20 `select 1` round trips in one session
     with `\timing on`; each taken while the workstation is at work.
   - **The daily dump,** as the install's job runs it, logged in as
     `lanes_register_dump`, a READER login, so no unattended job holds the
     owner's credential or an engineer's. It dumps both schemas,
     `lanes_register` and the index half's `lanes_index`, with its connection
     in `PG*` variables and its password in a 0600 file of its own run:

     ```sh
     umask 077 && pg_dump --no-password --schema=lanes_register --schema=lanes_index --format=custom --file=lanes-register-$(date -u +%Y%m%dT%H%MZ).dump
     ```

     A dump's head, `b`, reads from the file alone:

     ```sh
     pg_restore --data-only --schema=lanes_register --table=register_head --file=- <file>
     ```

     The dump holds no role, verifier, membership or floor, since those belong
     to the cluster.
   - **Way back:** nothing depends on the database yet.
2. **The schema and the roles.** Brett, from a host shell and not from a lane
   session, runs the bootstrap ("Roles and logins"):

   ```sh
   lanes-index bootstrap --admin-service '<service>' --label '<name>' --repository '<owner/name>' --lane '<name>'
   ```

   - **Check:** a second run prints `unchanged` for every object.
   - **Refusal:** as listed there; nothing is left half-made.
   - **Way back:** the schema holds no register data before the import. Brett
     drops schema `lanes_register` and the roles the bootstrap made, through
     the same admin service.
   - **Then the logins:** `lanes-index login add <engineer> --admin-service
     '<service>'` for each engineer, human or AI host, and
     `lanes-index login add --dump-job --admin-service '<service>'` for the
     dump job. A workstation that joins later gets its engineer's logins the
     same way, before its step 3. The way back above drops these logins too.
   - **Then the dump's check:** the dump job has run once, and `pg_restore -l`
     on its file lists both schemas.
3. **The configuration, per host.** Brett, on every engineer's workstation,
   human or AI host: Eagle and Raven first, together, then each later
   workstation as it joins. Below, "each configured workstation" is every
   workstation configured so far.
   - **Command:** place the 0600 password files and `lanes-index.conf` in the
     host's dedicated read-only mount. The file says `mode=index`, and carries
     `register_url=`, `register_passfile=`, `reader_url=`, `reader_passfile=`,
     `register_label=` (the label step 2 gave), `register_addresses=` and
     `register_rtt_ms=`, and, where the renderer identity is used, `render_key=`
     (step 9), with the lines the bootstrap and `login add` printed, its own
     engineer's alone. In both URLs, `<root file>`, as `login add` prints it,
     becomes the path on that host of the root file item 9 recorded: a URL
     whose `sslrootcert=` names no existing file is refused at load. Where the
     host's index half is on the register's database, the file also carries
     the indexer's `url=` and `passfile=`: the one shared login,
     `lanes_indexer`, whose password-file line step 2's bootstrap printed
     once, which goes alone in the 0600 file `passfile=` names, and a URL
     that the bootstrap prints no line for, which Brett writes with the
     same `sslmode=verify-full` and the same root file as the other two:

     ```text
     url=postgresql://lanes_indexer@<endpoint>:5432/<database>?sslmode=verify-full&sslrootcert=<root file>
     passfile=<indexer password file>
     ```
   - **Check:** `lanes-index status` reads; `ready` (step 6); and, where `url=`
     is set, `lanes-index sync --source inventory` ends 0, which logs in as the
     indexer, whose login `ready` does not read.
   - **Refusal:** `lanes-index` refuses a file that is not a regular file or
     has any group or other permission bit, one inside a git work tree or under
     `~/.agents/`, and a URL that carries a password ("`mode=primary`").
   - **Way back:** remove the new keys. `mode=index` behaves as today.
4. **The bench images, once.** Brett: one rebuild, then a recreate, carrying the
   tools' re-vendor, the configuration mount and `postgresql-16`.
   - **Before it:** the rebuild ends every lane in its container, so notice N1
     goes out first.
   - **Check:** `ready` (step 6).
   - **Way back:** the previous image. The register is unchanged.
5. **The install in each host shell.** Brett, by the host's route:
   - **Eagle and Raven:** `./setup.sh`, at the planned workBenches re-vendor,
     which vendors v1.3.0 as a package, installed with
     `payload/openRepoTools --install`, so that `ready`'s check 1 keeps its
     meaning. **Way back:** the prior re-vendor, installed by `./setup.sh` the
     same way.
   - **Every other host shell:** pipx, v1.3.0, with the root one-liner only as
     a stopgap. **Way back:** `pipx install --force openrepotools==1.2.0`, then
     `openRepoTools --install`.
   - On `mode=index` the register is read and written as today, but for what
     every mode meets: `lane-start`'s one `start-record` write, the guard's
     detached bind, `wip init`'s refusals, the mode question, the marker check,
     and a failed register read ending 69.
6. **Readiness, in every writing environment.** The operator lane runs, with
   each environment's own label:

   ```sh
   lanes-index ready --expect index --release 1.3.0 --env <label>
   ```

   in each environment that writes the register: each bench container on each
   host, each host shell, Raven's install and each later workstation's, and the
   Codex lane's tools, through Brett. This step confirms that list, which is
   Brett's act.
   - **Check:** exit 0 everywhere. The coordinator posts each environment's
     summary line, which carries no secret.
   - **Refusal:** any failing check holds act 4 until it passes.
7. **Stranded records and `W`.** Each lane publishes or archives its own
   stranded records, and Brett repairs `W` once. **Check:** each lane posts that
   it is done.
8. **The rehearsal and the drill.** The operator lane, against a scratch schema
   (`register_schema=`), from a scratch clone whose origin is a local bare copy
   of the workspace repository, so nothing reaches the real one; its scratch
   configuration leaves `render_key=` out.
   - **Command:** `lanes-index import --at <C> --dry-run`, which swaps nothing
     in; then `lanes-index import --at <C>`, since a cutover follows an import
     and refuses with 2 where the journal holds none; then a cutover, writes,
     an outage of the scratch database with its refusals, a rollback, and a
     files-mode write.
   - **Check:** each step's expected end, and the import's three-part proof
     (act 4's step 6) on the real register, which also times the import.
   - **Way back:** drop the scratch schema and the scratch clone.
9. **The renderer identity,** where the organisation's plan supports a push
   ruleset. Brett creates the identity and prepares a ruleset with it as the
   only bypass, over the rendered paths and `lanes/STORE` (never all of
   `lanes/`, since a person still commits `lanes/repos.tsv`), **not enforced
   yet**: enforced now, it would refuse every files-mode writer's push and
   strand its commit. Its deploy key sits in the 0600 file `render_key=` names,
   used only in the push's own environment. Act 4's step 5 enforces it.

#### Act 4: the cutover

**The gate.** The coordinator checks each item and posts the list:

- feature 007's code PRs, the root bump and v1.3.0 have landed;
- Amendment 23's slices 1a, 1b, 2a, 2b and 3 have landed and are installed: the
  last code group need not wait for 2a and 2b, but act 4 waits for 2a, 2b and 3;
- the register service is on the gate, and v1.3.0 with it, since from Eagle
  the guard without it takes about 1.2 to 2.1 s, over Brett's budget of 1 s;
- act 3's steps 6 and 8 have passed.

1. **The window.** Brett names it, and the coordinator posts notice N2 on each
   coordination issue before it opens. N2 asks each lane that holds a `HOLD` to
   write its own `HOLD RELEASED` before the freeze: a `cutover` run that would
   make its first step refuses, listing them, while any `HOLD` more than 30
   minutes old, or with no readable time ("time unreadable"), stays open.
2. **Readiness again.** The operator lane runs
   `lanes-index ready --expect index --release 1.3.0 --env <label>` in every
   environment, with each one's own label, since the check covers one moment,
   and a container started after act 3's run escapes it.
   **Refusal:** any failure stops the window.
3. **The freeze.** Brett gives the word, the coordinator posts N3, and Brett
   prompts each pane. Every lane but the operator lane and the coordinator
   reaches a stopping point, writes its `PAUSED` record with a plain
   `/handoff` (never `/ctx` or `--restart`, whose respawn runs `lane-start` and
   writes `RESUMED`), and writes nothing more until N4. Every lane that holds
   a `HOLD`, those two included, writes `HOLD RELEASED` first. Those writes
   must land before the import. The
   operator lane and the coordinator write no `PAUSED`, since a plain
   `/handoff` ends a lane's turn and its resume is a write: they stay live.
   After any `HOLD RELEASED` of their own, the coordinator writes no register
   line until N4, and the operator lane writes only what steps 6, 7 and 9 give
   it, the import, the cutover and the smoke test's `log NOTED`, each after the
   quiet check.

   The guard itself writes no register line, but two things it starts do.
   While a `HANDOFF-REQUESTED` stands unanswered for a lane, its guard types
   `/handoff --exit` into the lane's pane at its next prompt, which writes
   `PAUSED` and ends the session; and an answer `2` to the guard's question
   binds the window to another lane, which writes. So from the freeze word
   until N4 no request may stand for the operator lane or the coordinator,
   nobody answers `2`, and no paused lane's pane is prompted, since its guard
   lets the prompt through.
   **Way back:** the thaw (step 10); nothing has changed yet.
4. **The quiet check.** The operator lane confirms that the `lanes` tree id on
   origin is unchanged for 2 minutes, and that no `lanes-edit.sh`,
   `lane-start`, `lane-end`, `lane-handoff`, `lane-rename`, `lane` or
   `lane-worktrees` process runs in any container or host shell, or among the
   Codex lane's tools, which Brett reads on each host. Only the process check
   sees a tool between two of its writes, such as `lane-handoff`, `lane-end` or
   `lane-worktrees sweep`, and `write-handoff`'s push under `handoffs/`, which
   leaves the `lanes` tree id unchanged. The process check runs in each
   container and each host shell. It matches each tool's name as a word of the
   whole command line, since the tools run as `bash` and `python3`, and prints
   only a tool's name and its pids, never a command line, which can hold a
   secret. It exits 0, printing nothing, where none runs, 1 where one does,
   and 2 where `pgrep` fails; a container without `pgrep` needs procps. Its
   own text never matches, so `sh -c`, `docker exec` or `ssh` around it is
   safe:

   ```sh
   f=0
   for t in '[l]anes-edit[.]sh' '[l]ane-start' '[l]ane-end' '[l]ane-handoff' '[l]ane-rename' '[l]ane' '[l]ane-worktrees'; do
     p=$(pgrep -f "(^|[ /])$t( |\$)"); r=$?
     [ "$r" -le 1 ] || { echo "CHECK FAILED: pgrep exited $r" >&2; f=2; }
     [ "$r" -ne 0 ] || { echo "RUNNING $(printf %s "$t" | tr -d '[]') pid $(printf %s "$p" | tr '\n' ' ')"; [ "$f" -ne 0 ] || f=1; }
   done
   (exit "$f")
   ```

   It reads one moment, which is why the freeze comes first: a lane idle
   between two tool calls has no process.

   A `lane-worktrees` line is not always a writer. `lane-start` runs
   `lane-worktrees sweep --all --dry-run --report` once a day, detached. It
   only reads and writes one report file at its end, takes no lock and writes
   no register line, so it cannot move the `lanes` tree id. A build from
   before opensoft/openRepoTools-code#70 waits for ever in `open()` of a FIFO
   named `.git` that a test left in the estate. A pid is such a hung report
   only where all four hold, and none of them prints a command line. They run
   where the process check ran, once for each pid it named under
   `lane-worktrees`, with `pid` set to that pid first:

   ```sh
   pid='<pid>'                     # one pid the process check named
   ps -o etime=,ppid= -p "$pid"    # a day or more (a "-" in etime), and a parent of 1
   ps -o pid= --ppid "$pid"        # prints nothing: no child
   cat "/proc/$pid/wchan"          # wait_for_partner
   pgrep -f -- '[s]weep --all --dry-run --report' | grep -qx -- "$pid"   # exit 0: the daily report
   ```

   The fourth names the daily report itself: a `--dry-run` sweep writes no
   register line, so even woken it cannot move the tree id.

   Anywhere else, or where there is no `/proc`, the pid is a tool that may
   write: wait for it.

   **Refusal:** if the tree id moves, the 2 minutes start again. Where the
   process check names a tool, the operator lane finds the lane that runs it
   from the pid, never by printing a command line or environment. It waits for
   that tool to end, and the 2 minutes start again. A pid named under
   `lane-worktrees` that the four reads above show to be a hung report is not
   waited for. The operator lane writes down its pid and its `etime` as shown
   inert. Where the process check exits 1, names at least one pid, and every
   pid it names is shown inert, the process check counts as quiet, even though
   its script exits 1. Where it exits 2, or exits 1 and names no pid, it has
   not looked, and the quiet check has not passed.
5. **The ruleset on,** where act 3's step 9 prepared one. Brett enforces it
   once the quiet check has passed, after the drain's last `PAUSED` write and
   before the import: no writer the quiet check can see is left to push, so
   none is stranded, and from here a stale writer's push is rejected rather than
   landing as a stray. **Way back:** a window given up before the cutover's
   push turns the ruleset off before the thaw.
6. **The import.** The operator lane, in its own checkout, with `LANES_LANE=`
   its name, after a fetch:

   ```sh
   lanes-index import --at <C>
   ```

   where `C` is the tip of `origin/main` ("Import and rollback" says what it
   does). It checks every key its lines and holds give, which makes an import
   of today's register take about 25 s instead of 3 s, inside its 300 s bound.
   - **Check:** the proof's three parts: each rendered path's blob id equals
     `C`'s, the counts match, and every lane's answers are the same from the
     files and from SQL.
   - **Refusal:** any difference, a bad key, or a `C` that is not an ancestor
     of `origin/main`: 2, and nothing is set in the switch. A database it
     cannot reach is 69.
   - **Way back:** a re-import, which replaces the whole store in one
     transaction while the switch is not `primary`.
7. **The cutover.** The operator lane, on Brett's cutover word, with
   `LANES_LANE=` its name:

   ```sh
   lanes-index cutover --import <C>
   ```

   It sets the switch to `primary` and writes seq 1, the entry that adds
   `lanes/STORE`, in one transaction that commits before any git step; builds
   the marker commit on the tip, only where the tip descends from `C` with `C`'s
   `lanes` tree; rebuilds when handoff commits land meanwhile, six push attempts
   in all; and pushes it fast-forward only, with the renderer identity, confirms
   it by Amendment 23's S4 read-back, and records the marker `present`.
   - **Check:** the read-back, and `lanes-index status` showing seq 1.
   - **Refusal:** any change under `lanes/` since `C` aborts it, exit 2: once a
     read-back proves the marker absent from origin, the switch goes back to
     `files` and seq 1 is deleted, and step 6 runs again at the new tip. A push
     it cannot confirm is 3, and its rerun reads origin back first.
   - **Way back:** after the push, the rollback.
8. **The flip.** Brett, on Eagle, then on Raven, then on each other configured
   workstation, once per host and from the host, since one configuration on the
   host's read-only mount serves every environment there. **Each host needs,
   before its flip, the register checkout of every lane it may write for, or
   write routing on first** (`LANES_ROUTE_WRITES=1`, which needs a window that
   exports neither `LANES_REPO` nor `LANES_WORKSPACE_ROOT` at its own checkout:
   opensoft/openRepoTools#356, item 3): on primary a write for a
   lane whose own checkout the host lacks refuses with 2 (64 for
   `request-handoff`) and makes none, since a clone is a fetch, and the bind's
   `set-row-state` on the lane a window left and a `request-handoff` to a holder
   are such writes. `lanes-edit.sh lane-checkout <lane> --create` makes one, and
   none of `ready`'s checks reads one.

   ```sh
   lanes-index set-mode primary
   ```

   It rewrites only the `mode=` line ("Import and rollback" gives its main
   refusal), and prints the path, the old mode and the new one.
   - **Check:** `lanes-index ready --expect primary --release 1.3.0 --env
     <label>` in each environment on each configured workstation, with each
     environment's own label.
   - **Meanwhile:** from the marker's push until a host's own flip, every
     register write on that host refuses, since a files-mode writer meets the
     marker.
   - **Way back:** `lanes-index set-mode index` on each configured
     workstation, then the rollback.
9. **The smoke test.** The operator lane writes one `log NOTED`, and checks its
   row in SQL; its mirror commit at the seq after the cutover's entry,
   `Register-Seq: 2` after a first cutover; the read-back; and a lag of 0 in
   `lanes-index status`, after a fetch of `W` (`lanes-edit.sh
   refresh-workspace`), since `status` never fetches. **Refusal:** any miss is
   a rollback trigger, on Brett's word. A 69 that says "configure
   `mode=primary`" on a host already on `mode=primary` follows a
   ``WARNING: `lanes-index mode` …`` line naming the failed question, most
   often ``did not answer within 2 s``: run the write again before you read it
   as a miss. That 2 s bound holds where `timeout` is installed; without it the
   question has no bound.
10. **The thaw.** The coordinator posts N4, and Brett prompts each pane to
    resume.

**Time.** The mechanical steps take 1 to 2 minutes, and the window 20 to 45,
almost all of it the drain and the thaw. By an estimate, not a measurement,
Brett's own time is 45 to 75 minutes.

**The notices.** The coordinator posts each on the coordination issue of every
project with a row in the register, dormant ones included, which it lists
before it posts N1, and posts a declaration's on #165 too; under act 4 they
also go as Brett's prompt to each pane, and the Codex lane hears them through
Brett.

| notice | when | what it says |
|---|---|---|
| N1 | before act 3's rebuild | the rebuild ends every lane in its container; act 3 changes no register mode (`mode=index` stays), though the new tools change how a lane starts and binds in every mode; `ready` will run in each environment |
| N2 | once Brett names the window, before it opens | "What changes for a lane" in full; the window; the freeze's form; the rollback's triggers |
| N3 | at the freeze word | a lane that holds a `HOLD` writes `HOLD RELEASED` first; then every lane but the operator lane and the coordinator stops at a stopping point, writes `PAUSED` with a plain `/handoff` (never `/ctx` or `--restart`, whose respawn runs `lane-start` and writes `RESUMED`), and writes nothing more until N4, since the ruleset goes on after the drain; the operator lane and the coordinator write no `PAUSED` and stay live, and after any `HOLD RELEASED` of their own the coordinator writes no register line until N4 and the operator lane writes only what act 4's steps 6, 7 and 9 give it, each after the quiet check; until N4 nobody answers the guard's 2, whose bind writes, and nobody prompts a paused lane's pane, whose guard lets the prompt through |
| N4 | after the smoke test | the marker's commit and seq 1; the smoke test's result; resume; the files now lag |
| N5 | at a rollback's start, or a declared outage | what refuses; what the guard answers; until when |
| N6 | after the rollback | the register is files again, and what returns |
| N7 | at the disaster's declaration | the outage; the pause; that the unrendered tail is at risk |
| N8 | at the disaster's resumption | `m`, `h` and the loss; what each lane checks |

A dormant project meets N2 on its issue when it resumes. Its tools must be
upgraded before it writes, or the renderer stops at its first files write; act
4's second `ready` cannot see an environment that is not running.

#### The rollback

A rollback needs the database ("Import and rollback" says what
`lanes-index rollback` does, step by step).

**Its triggers:**

- the renderer has diverged, and SQL is not trusted;
- `reconcile` finds that the mirror is not a full render, and cannot repair it;
- a workstation that must write cannot be configured;
- the credential is compromised;
- the measured costs miss their targets;
- Brett's word, for any reason.

**The database down past Brett's limit, 4 hours,** is no trigger of a rollback,
which cannot run without the database: it leads to waiting, or to "The database
lost for good", below.

1. **The word.** Brett gives it, and the coordinator posts N5.
2. **The switch.** The operator lane runs `lanes-index rollback` in its own
   checkout, with `LANES_LANE=` its name. It refuses with 2 while
   `lanes-index status` shows `diverged at <sha>`: the divergence is resolved
   first, on a person's word, by `lanes-index import --single <sha>` or
   `lanes-index render --revert <sha>`, while the switch is still `primary`.
   - **The declaration:** `rollback` writes the outage's declaration itself
     where its configuration directory can be written; from a bench, whose
     mount is read-only, it prints instead the line
     `lanes-index outage declare --rollback --mirror-seq <k>`. Brett runs that
     line, with the `<k>` the rollback found, on each other host, and on the
     rollback's own host where it ran in a bench; on a host where he runs
     `lanes-index outage declare` on his word instead, it refuses with 2 and
     prints that line to run. Until a
     declaration is written, guards refuse 2, the operator lane's own included,
     so a rerun runs from a shell or a `CLAUDE_NO_LANE=1` session.
   - **Refusal:** with the database unreachable it exits 69 and changes neither
     the switch nor the marker: the lanes wait, or Brett takes the disaster
     path. Other writes that keep taking its turn to the 30 s deadline end it
     75, with nothing written and the switch still `primary`; it is run again.
3. **The render,** which proves the mirror a full render. Every guard whose ref
   is behind the rollback's head refuses 2 until the mirror carries it, so a
   rerun after a failed render runs from a shell, or a `CLAUDE_NO_LANE=1`
   session with `LANES_LANE=` the operator lane.
   - **The ruleset off,** where act 4 enforced one: the operator lane runs the
     rollback in two calls, the first stopping once the render is proved, with
     the marker still on origin and exit 0; Brett then turns the ruleset off,
     and the second call resumes at the push. Without a ruleset, one call runs
     every step.

     ```sh
     lanes-index rollback --hold-marker
     lanes-index rollback
     ```

4. **The marker.** `rollback` pushes the commit that removes `lanes/STORE`, with
   the renderer identity, and confirms it by the S4 read-back; a push it cannot
   confirm exits 3, and the rerun reads origin back first.
5. **The flips back.** Brett runs `lanes-index set-mode index` on each
   configured workstation but one, which stays on `mode=primary` for step 6's
   check. The order does not matter: a workstation still on `mode=primary`
   meets the switch at `files` and refuses, and a flipped one meets the marker
   until it is gone, and refuses.
6. **The check.** A files-mode write on a flipped workstation succeeds, and a
   write on the one not yet flipped back refuses. Brett then runs
   `lanes-index set-mode index` on that one too, after which a files-mode
   write there succeeds, and then `lanes-index outage clear` on each
   configured workstation.
7. **The notice.** The coordinator posts N6, saying what the files lose: the
   journal, though each seq's commit and trailer stay in git; the mode that last
   wrote each row; the database clock's order; and serializable claims, so
   `CLAIM-LOST`, exit 7 and push-order races return. **No write is lost:** the
   database is up, and the rollback renders every seq before the marker goes.

#### The database lost for good

This is not a rollback. It is a restore or a re-import onto a replacement
database, run with every workstation's lanes paused first.

1. **The trigger.** The database has been unreachable past Brett's limit, 4
   hours, and he says it will not return in time. Until that word the lanes
   wait: every write and deciding read refuses with 69, and the guard refuses
   with 2.
2. **The declaration.** Brett declares the outage on each configured
   workstation, from each host, so that he can reach his lanes:

   ```sh
   lanes-index outage declare --until '<utc>' --why "<Brett's words>"
   ```

   It records the newest `Register-Seq` on `origin/main`, after a fetch; where
   the host cannot fetch, Brett names it with `--mirror-seq <m>`, from
   `lanes-index status` on a host that can, and with neither it refuses with 2.
   Every write and every other deciding read still refuses. The coordinator
   posts N7.
3. **The pause.** Brett prompts each pane to stop. A swap writes its handoff
   file from a mirror read; its `PAUSED` record refuses with 69, and the lane
   stays `SWAPPING` until the late path completes it.
4. **The loss, recorded.** The operator lane runs `lanes-index status`. It
   cannot reach the database, so it prints `m`, the mirror tip's
   `Register-Seq`, and `h`, the last head it recorded, or "unknown". **Every
   write that SQL committed after `m` exists only in SQL,** since none was
   rendered: a re-import loses all of them, and a restore keeps only those up
   to the backup's head. Where no head was recorded the bound is unknown, and
   only the lanes can say what they wrote.
5. **The old database, retired.** Brett revokes the old roles wherever the old
   host can still be reached, so that a database that comes back with the
   switch still at `primary` cannot take a write: for each engineer, through
   an admin service that names the old database,
   `lanes-index login revoke <engineer> --admin-service '<old service>'`.
   **Check:** a second run for each engineer prints `unchanged` at steps 1
   and 2 and ends no session. Step 6 re-points every host before any write
   resumes.
6. **The replacement.** The install creates a new CNPG Cluster, to the items
   above. Roles belong to the cluster: a physical backup carries them, and a
   database dump does not. Then, on Brett's word, one of two routes:
   - **Restore,** only where a backup's head `b` is newer than `m`. Lost: every
     seq after `b`.
     - **From the physical backups:** the install creates the new Cluster by
       CNPG's recovery from the Barman Cloud object store, never in place, to
       the latest WAL or to a time Brett names, with its own backups under a
       name of their own, so it never archives over the backups it restores
       from; with WAL archived at least every 5 minutes and the archiver keeping
       up, its `b` is within about 5 minutes of the loss. It carries the roles,
       their verifiers, memberships and floors, so `lanes-index bootstrap
       --roles-only` is not needed (it would print `unchanged`). Brett runs,
       through his admin service:

       ```sh
       lanes-index bootstrap --admin-service '<service>' --label '<new name>' --repository '<owner/name>' --lane '<name>'
       lanes-index login rotate <engineer> --admin-service '<service>'
       lanes-index login rotate --dump-job --admin-service '<service>'
       ```

       The bootstrap migrates it forward or prints `unchanged`, and writes no
       label, since the restored switch reads `primary`; its `--label` names
       the replacement, differs from the lost database's, and is the
       `register_label=` it prints (the re-pointing, below). `login rotate`
       runs once for each engineer, so that a workstation not yet re-pointed
       cannot log in there, and once for the dump job, whose new password
       Brett stores as its Key Vault secret.
     - **From the daily dump,** in this order, each `lanes-index` step and the
       restore through one admin service, which names the register's database:
       1. the register's database exists, owned by the admin: the new
          Cluster's `initdb` made it;
       2. `lanes-index bootstrap --admin-service '<service>' --roles-only`,
          which also makes the three database-level statements a dump does
          not carry ("Roles and logins");
       3. `lanes-index login add <engineer> --admin-service '<service>'` for
          each engineer, and
          `lanes-index login add --dump-job --admin-service '<service>'`;
       4. the restore of both schemas, in an environment that holds no
          inherited `PG` variable and names the service only in `PGSERVICE`,
          as `lanes-index` runs `psql`, so that only Brett's own
          `~/.pg_service.conf` and `~/.pgpass` give the server and its
          password:

          ```sh
          env -i HOME="$HOME" PATH="$PATH" PGSERVICE='<service>' \
            pg_restore --exit-on-error --no-password --dbname=<database> <file>
          ```

       5. `lanes-index bootstrap --admin-service '<service>' --label '<name>'
          --repository '<owner/name>' --lane '<name>'`, which prints
          `unchanged`.
   - **Re-import,** the default, where no backup is newer than `m`. Brett
     runs, through one admin service, which names the register's database:

     ```sh
     lanes-index bootstrap --admin-service '<service>' --roles-only
     lanes-index bootstrap --admin-service '<service>' --label '<name>' --repository '<owner/name>' --lane '<name>'
     lanes-index login add <engineer> --admin-service '<service>'
     lanes-index login add --dump-job --admin-service '<service>'
     ```

     `login add` runs once for each engineer, since a re-imported replacement
     holds no engineer's login until `login add` makes each again, and once
     for the dump job, whose new password Brett stores as its Key Vault
     secret. After the re-pointing the operator lane runs:

     ```sh
     lanes-index import --at <M> --after-loss
     ```

     where `M` is the tip of `origin/main`, which still carries the marker. Its
     baseline takes `M`'s newest `Register-Seq`, `m`, instead of 0, so seqs on
     `main` stay unique. Lost: every seq after `m`. Its key check takes about
     25 s, and a bad key stops it before anything is written.
   - **The re-pointing,** on either route, before any `lanes-index` command
     that connects through `lanes-index.conf` reaches the replacement (the
     bootstrap and `login` above go through the admin service and print the
     lines it uses): on each configured workstation, from each host,
     Brett sets `register_url=`, `reader_url=`, their password files and
     `register_label=` to the replacement's, **in one edit**, with the lines
     the bootstrap printed and that workstation's own engineer's lines from
     `login add` or `login rotate`. In both URLs, `<root file>`, as `login add`
     prints it, becomes the path on that host of the root file item 9
     recorded, as at act 3: a URL whose `sslrootcert=` names no existing file
     is refused at load, so the after-loss import and the cutover refuse, and
     `ready` fails, there. Where the host's file carries the indexer's
     `url=` and `passfile=`, the same edit sets them to the replacement's:
     `url=` to its indexer URL, with that same root file, and `passfile=` to
     the 0600 file that holds the `lanes_indexer` line which the roles-only
     bootstrap printed, once, where it made the role. Where the role already
     exists, as after a restore from the physical backups, no line is
     printed, the old password and its file stand, and `url=` changes only
     where the host or the database name does. `register_label=` names the
     replacement:
     after a re-import, the label its bootstrap wrote; after a restore, the
     `--label` Brett gave its bootstrap, which must differ from the lost
     database's label that the restored switch still holds. The lanes stay
     paused.
   - **The marker, as the cutover's own entry.** After a re-import the operator
     lane runs `lanes-index cutover --import <M> --after-loss`, at seq `m+1`, so
     the next write is `m+2`; after a restore, `lanes-index cutover
     --after-loss`, with no import, at `b+1`, which first renders every seq the
     mirror lacks, up to `b`. Its transaction sets the switch to `primary` with
     the replacement's label and a new generation, and rewrites `lanes/STORE`
     for the replacement, which it pushes and confirms as the cutover does.
     `cutover` takes its label from `register_label=` alone, never from a flag:
     a file with no `register_label=` is refused with 2 before it connects; at a
     switch at `files`, a label that is not the switch's is refused with 2
     before anything is sent; and at a switch at `primary`, a label that does
     not differ from the switch's is refused with 2, writing nothing. `ready`'s
     check 10 fails by design before a restore's after-loss cutover, since the
     restored switch still names the lost label. A re-import's cutover that
     aborts keeps the label and generation it set; a restore's, once a read-back
     proves the rewrite absent from origin, puts the head back to `b` and the
     switch's fields as they were, and the estate stays stopped, with the lanes
     paused, until the cutover is run again.
   - **The generation.** Every pending record and every printed rerun made
     against the lost database carries the old generation, and now refuses
     with 2 rather than apply a write the mirror may already hold. Each lane
     checks the mirror instead.
7. **The resumption.** `lanes-index outage clear` on each configured
   workstation first, since `ready`'s check 7 fails while a declaration is
   recorded. Then `lanes-index ready --expect primary --release 1.3.0 --env
   <label>` in each environment, with each one's own label, and the smoke test
   (act 4's step 9, at `m+2` after a re-import or `b+2` after a restore); and,
   on each host whose file carries `url=`, `lanes-index sync --source
   inventory`, which ends 0 only where the indexer's login works, since
   `ready` reads no indexer login; the lanes stay paused until all pass. The
   coordinator posts N8, naming `m`, `h`
   and the loss. Each lane then checks its last records against the mirror, and
   writes any owed record again, with the database's UTC (a late `PAUSED` is
   dated by its `--at`, and a UTC inside an `append-line` text stays as
   written) and the original time in its free text.

**Back to files by hand.** Brett may instead return the estate to the files: a
commit removing the marker, made and confirmed by the S4 read-back, with any
ruleset turned off first, then `lanes-index set-mode index` on each configured
workstation. It loses the same tail, and it is safe only after step 5, since its
leak, a database that comes back with the switch still at `primary`, needs the
old database to come back. Then `lanes-index outage clear` on each configured
workstation, which removes the declaration that step 2 made there, and a
files-mode write, which must succeed; the lanes stay paused until it has. The
coordinator posts N8, naming `m`, `h` and the loss, and Brett prompts each pane
to resume; each lane then checks its last records against the mirror, and
writes any owed record again, as step 7 has it.

## Retiring a lane's worktrees — `lane-worktrees sweep` (#162)

**On Brett Heap's word of 2026-10-05 ("build 162")**, tracked on
[opensoft/openRepoTools#162](https://github.com/opensoft/openRepoTools/issues/162).
A swap that does not finish gracefully — a usage limit, a killed pane, a crashed
harness — leaves its writers' trees where they stood, and the protocol's
worktree-safety rule (a swap never commits, pushes, stashes, resets or cleans)
is right to leave them. This is the act that retires them afterwards, in one
place, under one rule: **nothing is deleted that is not first on origin or in a
bundle under the sweeps directory, and a tree a live writer owns is never
touched.**

```sh
lane-worktrees sweep <lane>                     # the DRY RUN: the table, nothing changed
lane-worktrees sweep <lane> --yes [--live <path>|--live none]
lane-worktrees sweep <lane> --dry-run --porcelain   # lane-end's gate (#163): 0 / 3 / 2
lane-worktrees add <lane> <slice> [--branch <b>] [--from <ref>]   # make a tree AND record it (#163)
lane-worktrees sweep --expire [--yes]           # archives past retention
```

**On `mode=primary`** this act and the estate report read the register's lines
through `lanes-edit.sh register-grep` (in files mode it runs the same `git grep`
on `origin/<branch>` of the workspace repository), so where this section reads
the register on `origin/<branch>` it reads the database, never the mirror. Any
exit of the verb but 0 and 1 is a register that could not be read, and nothing
is deleted on it. See "The SQL register".

**Which trees are the lane's** is #97's inventory (`lanes-edit.sh lane-trees`)
and the disk — **never the derived index** (Amendment 14 clause (b)). The sweep
also reads every registration of the lane's checkout and every checkout under
its two roots (`<checkout>/.claude/worktrees/*`, `.lane-worktrees/<lane>/*`).
A tree the inventory does not name is still **the lane's** when it stands under
the lane's own root, `.lane-worktrees/<lane>/` (only this lane's starts make it),
or sits in `<checkout>/.claude/worktrees` with its HEAD — a commit origin's
default branch lacks — carrying this lane's `Lane:` trailer AS ITS OWN (a tree
another lane stacked on this lane's commit is not this lane's, #174); its rows
say so. A lane with no #97 snapshot at all (state `NONE`) but trees of its own is
**refused, exit 2**: which trees are its is recorded nowhere, so #163's gate does
not pass it. Any other tree the inventory does not name is **FOREIGN**: reported and left, unless
`--include-foreign` and a `--word "<verbatim>"` (recorded in every register line
it causes). A tree ANOTHER lane's inventory names is that lane's and is never
taken from here, word or no word — and one BOTH inventories name is kept, since
records are history and a path can be reused. Other lanes' claims are read
wherever #97 keeps them (#170 G6): beside this lane's control root, under
`$LANES_LANE_STATE_ROOT` and `$PROJECTS_ROOT/.lane-state`, beside every checkout
the estate's shape walk finds, beside every lane's recorded `dir` in the
register's lane logs that lies in this sweep's estate (#174), and - where the whole estate is walked (a clone the table
could take, or `--include-scratch`) - in every `.lane-state` that walk finds, one
in a plain directory inside a repository included (#174). An inventory record that cannot be
read (no id, no path, an unknown schema; #170 G9) refuses the sweep, exit **2**: its tree's owner
is unknown — and so does ANOTHER lane's record or inventory directory, a lane
worktree root, a checkout's `git worktree list`, or the register's lane logs that
cannot be read, before anything is fetched (#170 G1, G5); another lane's claim
that cannot be read is a NOTE instead where this lane has no tree, since a claim
could only take one from it (#174). The lane's own checkout is never a candidate. The lane name is
resolved first (`lanes-edit.sh canon-lane`, Amendment 15), as `lane-start` and
`lane-end` resolve it; an alias table that cannot be read refuses.

**Who may act** is #97's reconciliation (`lanes-edit.sh lane-reconcile`), read
before anything is written — the fetch included. Under `--yes` the register is
FETCHED FIRST, by the sweep itself (`origin/<branch>` of the workspace
repository, an explicit refspec): `lanes-edit.sh`'s own fetch answers 0 when it
fails, and an inherited `LANES_NO_FETCH=1` skips it, so a lane rebound on another
host after this one's last fetch would read as bound HERE (#170 A1). A register
that cannot be fetched refuses, exit **2**, with nothing changed. The fetch of
each repository acted in pins its refspec as `status --fetch` does
(`+refs/heads/*:refs/remotes/origin/*`, `--refmap` the same, tag pruning off), so
a mirror-style `remote.origin.fetch` cannot let `--prune` delete a local branch
before it is classified (#170 item 6).

| the lane | dry run | `--yes` |
|---|---|---|
| bound on another host or container (Amendment 18(b): UNKNOWN, never dead) | the table, exit **2** | refused, exit **2** |
| its binding, holder or reconciliation could not be read | the table, exit **2** | refused, exit **2** |
| held by ANOTHER live session | the table, exit **2** | refused, exit **2** |
| managed-owned (ruling 2026-10-04) | refused, exit **2** | refused, exit **2** |
| held by THIS session, or with a tree THIS session recorded (an inventory `writer` that is the caller, #170 B5) | the table | only with the coordinator's writer count: `--live <worktree>` per live writer (its ListAgents count, Amendment 17 Addendum 1 (i)), or `--live none`. A path that names no tree of the table, or `none` beside a path, is usage, **64** (#170 B4) |
| `SWAPPING` in a live session | the table | refused: a handoff is in flight |
| no live holder, bound here, free or gone | the table | performed |

### The table, as implemented

Each tree is classified in this order; the first row that matches decides.

| tree | disposition | what `--yes` does |
|---|---|---|
| not in the lane's inventory | `foreign` | nothing (`--include-foreign --word …` applies the rows below) |
| a process stands in it or holds a file open there (`/proc`, else `lsof`), a tmux pane's current path is in it, it is named by `--live`, the sweep was started inside it, or the session that recorded it is live | `live` | nothing, ever — asked again at the moment of the act |
| liveness could not be read — the scan failed, or a process of THIS account whose `/proc` entries cannot be read (not dumpable) is placed in it by an absolute path in its command line or by its parent's working directory (#170 item 2) | `keep` | nothing |
| its directory is gone and git still registers it | `prune` | `git worktree remove <path>` — that one registration; a repository-wide `git worktree prune` is never run (it would unregister other lanes' and FOREIGN trees too); a directory that reappeared meanwhile is left for the next sweep to classify |
| … and its registration's git directory names a commit no branch, tag or origin holds — a detached HEAD, a reflog entry (#170 A4) | `rescue+prune` | a rescue branch pushed (where there is an origin) and a bundle, then that one registration removed |
| … and its registration's git directory keeps a submodule repository (`modules/`) with a branch, tag, stash or reflog entry no remote of it holds, or an annotated tag object none of them holds (#174) | `keep` | nothing: the prune would take that repository (asked again just before) |
| its directory is gone and nothing registers it | `gone` | nothing; the inventory record is history |
| another lane's inventory names it too | `keep` | nothing: which lane owns it now is a person's to say |
| a registration someone LOCKED; a file flagged skip-worktree or assume-unchanged that differs from the index (git status hides it, #170 A2); a submodule with uncommitted work, ignored files no commit carries (an `.env`), a branch, tag or HEAD no remote of it holds, a stash (#170 item 5), a commit only its reflog names, or a skip-worktree or assume-unchanged edit (#174) — its repository lives under the tree's git directory and goes with it; any submodule repository that git directory keeps under `modules/` with a branch, tag, HEAD, stash or reflog entry no remote of it holds, or an ANNOTATED tag whose very tag object none of them holds (asked of each remote at the act), a DEINITIALIZED submodule's included (#174); a repository nested anywhere inside it, a venv's `src/`, node_modules and a cache-named directory included (#170 A3, #174); a CLONE with a branch, a TAG (#170 item 4) or a stash origin lacks, or whose git directory linked worktrees share | `keep` | nothing, and the line says which |
| something leans on it — another repository's `objects/info/alternates`, or a remote whose URL is its path, read from every repository the estate walk finds: every directory of the estate but caches, tool environments and `site-packages`, a plain directory inside a repository included (#170 A11) | `load-bearing` | nothing; the line names every dependent and the remedy (`git repack -a -d`, then drop the alternates or re-point the remote) |
| a CLONE (removed by deleting its directory) when the search for what leans on it was partial: a directory of the estate could not be read, there is no estate root, or the clone lies outside it (#170 A11) | `keep` | nothing |
| no `origin` | `keep` (`bundle+remove` with `--bundle`) | a bundle is its only rescue |
| dirty or untracked work, where git-lfs is configured and an untracked file is `filter=lfs` content | `keep` | nothing: untracked files go to a bundle only (below), and a bundle carries an LFS pointer, never its bytes (#174); asked again at the act, before anything is pushed |
| dirty or untracked work | `wip-rescue+remove` | the tracked changes (`git add -u` into a COPY of its index), `commit-tree` on its head, `rescue/<lane>/<slice>-<UTC>` pushed and seen on origin; UNTRACKED files in a second commit on it that goes to the bundle ONLY, never to origin — an un-ignored key file is not something anyone meant to push (#170 A9; `--push-untracked` pushes them as before). Which files are untracked is read from the two snapshots taken AT THE ACT, never from the table's status, so a file that arrived since is held back too (#174); a bundle, then removed |
| an unborn branch, clean | `remove` | removed |
| detached at a commit origin holds | `remove` | removed |
| detached with commits of its own | `rescue+remove` | `rescue/<lane>/<slice>-<UTC>` pushed, a bundle, then removed |
| MERGED — its PR LANDED in the register, or `gh` says MERGED, into the default branch, and that PR's head holds this tip | `remove+delete-branch` | removed; local branch deleted at the SHA judged merged (`update-ref -d <old>`), and the remote branch too — under a lease on the judged SHA — where its `Lane:` trailer is this lane's, it holds nothing the PR did not, and `gh` does not still answer a PR of it OPEN (a LANDED line with a wrong number never closes an open PR, #170 A8) |
| clean, every commit on its own remote branch (an open PR is untouched) | `remove` | removed; the branch stays |
| clean, its head on some origin branch | `remove` | removed; the branch stays |
| unpublished commits on `main`/`master`, or on a branch origin has DIVERGED from | `rescue+remove` | the tip to `rescue/<lane>/<slice>-<UTC>`, then removed; the branch itself is never force-pushed |
| unpublished commits on a branch an OPEN pull request names (gh's OPEN wins over a register LANDED line naming that PR, #174), whose origin tip carries ANOTHER lane's `Lane:` trailer, or where whether a PR names it could not be read (#170 A7) | `rescue+remove` | the same: the lane's unreviewed commits never go onto somebody else's branch, and origin's branch is left as it is |
| unpublished commits otherwise | `push+remove` | pushed AS IS under its OWN name — never its upstream's, which for a branch made from `origin/main` is `main` — then removed |

**"Merged" is never `git branch --merged` alone**: a squash merge leaves no
ancestry, and a branch whose tip IS on `main` with no pull request naming it is
listed as unmerged and its branch kept. A PR merged into another base (a release
or feature branch) is no landing, and an OPEN PR protects its branch however an
older PR of the same branch was merged — unless the register has LANDED that
very PR. The branch's pull request comes from one `gh pr list --state all` per
repository; with `LANES_NO_GITHUB=1` or no `gh` nothing is called merged. The register's `LANDED` lines are read from
`origin/<branch>` of the workspace repository.

**Liveness is asked again before the rescue and again at the moment of
removal** — processes, open files, tmux panes and the recording session, afresh;
a scan that cannot be made leaves the tree. **At the moment of removal** the
head is re-read, and the tree must still be clean — or, after a WIP rescue, must still be byte for byte the tree the rescue
commit carries. A push that origin refuses leaves the tree exactly as it was:
the WIP commit is made BESIDE the tree, never into it, so its branch, index and
files never move. Every push is checked on origin (`git ls-remote`) before
anything goes, and where the repository has git-lfs configured (`filter.lfs.*`)
it runs its hooks, so git-lfs's pre-push uploads the objects (#170 A10).

**Before each removal git is asked what it would take.** A worktree's removal
takes its own git directory — its HEAD and that HEAD's reflog (and its branch
and the branch's reflog, where the branch is deleted after it); a pruned
registration's the same; a CLONE takes every ref but its remote-tracking ones,
and every reflog — a reflog directory that cannot be listed makes the answer
unknown, and the clone stays (#174) — and an ANNOTATED TAG is its own object,
kept where origin holds that very tag object (`ls-remote --tags`) and bundled,
its message with it, where it does not (#174); a `--branches` delete takes the
branch's tip and its reflog (#174). Commits there that no origin ref, surviving
branch, tag or stash keeps — a commit only a reflog names, after an amend or a checkout away
from it (#170 A5) — go to a bundle first, through temporary
`refs/lane-worktrees/<slice>-<UTC>/*` refs deleted once it verifies. **The
self-check** asks the same question once more right before the removal: a
commit the removal would take that is neither on origin, in a ref that stays,
in a push seen on origin this run, nor in a bundle this run verified is a
defect in the table, and the sweep REFUSES it — exit **2**, a line in
`DISPOSITION.md`, nothing after it acted on. A CLONE with a local git-lfs
store (`.git/lfs/objects`) whose commits would go to a bundle only is kept
instead: a bundle carries LFS pointers, never their bytes (#174). The first register line that
cannot be written stops the sweep the same way (exit 1, #170 B6).

**Ignored files that are not caches or build output** (an `.env`, a local
config) are archived to `<slice>-ignored.tar.gz` before a tree goes: a commit
cannot carry them, and the rule is that nothing goes that is not first saved.
They are listed again at the moment of removal, and a tree whose ignored files
changed, appeared or went since the archive was taken is left.
Build output (`target`, `build`, `dist`, `.tox`, `.next`, `*.egg-info`, …) is
generated and is not archived — judged by the ignored entry's OWN name, never an
ancestor's, so `docker/build/prod.env` is archived (#170 A6) — and so are
`docker/node_modules/prod.env` and `docker/.venv/prod.env`: the archive leaves
caches out only BENEATH an entry it was given, and is read back for every entry
selected, so one missing leaves the tree (#174). More than
`ignored_archive_mb` of the rest leaves the tree in place for a person.

### The other rows

| flag | what | disposition |
|---|---|---|
| `--branches` | local branches no worktree holds, in the lane's checkout | merged by PR evidence: `delete` (`delete+remote` where the `Lane:` trailer is this lane's, origin's tip is inside the PR and gh does not answer it OPEN), each at the SHA judged and only after a fetch of that checkout that worked under `--yes`, and only after what the delete would take — its tip and every commit its reflog names — is on origin, in a ref that stays or in a bundle (a `bundle:` ledger row), asked once more right before the delete: the self-check, exit **2** on a refusal (#174); an open PR's branch: `keep` — and still COUNTED where it is the lane's and `origin/<branch>` lacks its tip (#170 G2); a branch `origin/<branch>` holds, whatever its configured upstream, is published and not listed (#170 G7); unmerged with a missing, diverged, unpushed or unreadable upstream: `list` with its tip, distance from `origin/main`, last commit date and owner — never deleted; `main`, `master` and `rescue/*`: never touched |
| `--include-scratch` | `.lane-worktrees/<lane>/*-scratch`, `briefs/`, `bin/` that are no checkout | `archive+remove`: tar (caches left out) and sha256, then removed — only if no writer arrived and nothing outside the caches changed while the tar ran; `keep` while a tree of the table or any `.git` lies under it (#170 item 7), or the search for what leans on it was partial (#170 A11) |
| `--include-caches` | `__pycache__`, `.pytest_cache`, `.mypy_cache`, `.ruff_cache`, `node_modules`, any directory with `pyvenv.cfg` (and `venv/`, `.venv/` with an activate script), under `.lane-worktrees/<lane>/**` and the lane's inventory trees | `remove`, without archiving — never one git tracks or whose tracking cannot be read, one that is or holds a repository or a directory that cannot be read (`keep`: a venv's `src/` clone is work, #174; asked again just before), one inside scratch being archived, or one in a FOREIGN tree, a live one, one whose liveness is unknown or one left by its own act; the owning tree's liveness is asked again just before |
| `--include-sandboxes` | `tmp.*` and `pytest-of-$USER/pytest-*` in `/tmp` and `$TMPDIR` (or `LANE_WORKTREES_SANDBOX_ROOTS`), and `tests/run.sh`'s run roots, owned by this account | `remove` where the owning process is gone: a pytest `.lock` naming a dead pid (read again at the act), and no live process standing in it, holding it open or naming it in its environment. A `tmp.*` is NEVER removed (#170 item 1, option (b); #174): `mktemp -d` records no owner, so neither a suite's mark in it nor `aging_days` untouched proves a suite made it — one older than `sandbox_min_age_minutes` (by the newest file in it) is `list`ed and left, with its mark and any repository in it (a bare one included), since it may be a person's checkout or scratch |
| `--links` | every symlink under the estate and every worktree `.git` gitdir pointer | `broken`, listed with target and age; nothing changes |
| `--bundle` | every tree acted on | a `git bundle` beside the archive (always, for a rescue) |

### The sweeps directory, the register line, retention

`${XDG_STATE_HOME:-$HOME/.local/state}/openRepoTools/sweeps/<lane>/<UTC>/`,
mode 0700, created EXCLUSIVELY by the first act of a `--yes` (a second sweep
started in the same second gets `<UTC>-2`) and never by a dry run:

* `DISPOSITION.md` — every tree and item acted on: what it was, why, what was
  done, where its rescue is, and the register line that records it;
* `rescues.tsv` — `origin`, rescue branch and sha for each rescue, which
  `--expire` reads; a bundle that is the ONLY copy of what it holds (untracked
  files, commits only a reflog named, a tree with no origin) is a row too, with
  origin `-` and branch `bundle:<file>` (#170 item 8);
* the bundles, `<slice>-ignored.tar.gz` and `scratch-<name>.tar.gz`;
* `MANIFEST.sha256` — `sha256sum` format, every file above.

**One register `NOTED` line per tree acted on** (`lanes-edit.sh log NOTED
lane:<lane>`), written after the act so it says what happened, naming the
disposition, the rescue and the archive; one more each for scratch, branches,
and caches with sandboxes, each written as the session that swept
(`LANES_SESSION`, #170 E7). A line the register refuses is printed whole for a
person to write by hand - quoted for the shell (#170 E6) and naming that same
session (#179) - and the exit is 1. The line is the pointer that
outlives the archive.

**Retention: 90 days** (`sweep.conf`). `sweep --expire` lists archives older than
that, and with `--yes` removes those whose every rescue branch is still on
origin AT THE RESCUED SHA (`git ls-remote`); an archive whose rescue branch is
gone from origin, or was remade at another commit, or that holds a `bundle:` row,
is the only copy and is NEVER expired. Nor is an archive that is not WHOLE — no `MANIFEST.sha256`, a file it
does not list or whose digest differs (a sweep interrupted after a bundle), or a
`rescues.tsv` that is missing or has a row that is not origin, branch and SHA,
or a manifest entry whose file is gone. What expired is appended to
`sweeps/EXPIRED.log`.

`${XDG_CONFIG_HOME:-$HOME/.config}/openRepoTools/sweep.conf` (or
`$LANE_WORKTREES_CONF`), `key=value` lines:

| key | default | what |
|---|---|---|
| `retention_days` | 90 | archive age before `--expire` may remove it |
| `aging_days` | 14 | a rescue branch or dirty inventory tree older than this is "awaiting disposition" in the report |
| `sandbox_min_age_minutes` | 60 | a `tmp.*` younger than this is never a killed suite's |
| `foreign_quiet_hours` | 24 | a FOREIGN tree active within this is treated as live, even with `--include-foreign` |
| `ignored_report_mb` | 50 | the report's threshold for an ignored directory |
| `ignored_archive_mb` | 200 | ignored files (not caches, not build output) a tree may carry into its archive; more, and the tree is left for a person |

### The exit contract (`lane-end`'s gate, #163)

`lane-worktrees sweep <lane> --dry-run --porcelain` prints one tab-separated
row per tree and item, between a `lane` row and a `summary` row:

```text
lane    <lane> <state> <holder> <holder uuid> <binding> <verdict>
refused <why>                                   (only when refused)
tree    <disposition> <retire|-> <path> <branch> <head> <why>
<kind>  <disposition> <retire|-> <path> <detail> <bytes> <why>     (branch, scratch, cache, sandbox, link)
leg     <disposition> <retire|-> <path> <branch> <head> <why>    (an assembly's checkouts only, T018)
summary <to retire> <trees> <live> <foreign>
```

**`lane-end` reads it with `--branches --include-scratch --include-caches`** and
refuses on 3 and on 2 — and on 0 too where a `cache` row is printed, because a
cache under the lane's root is still the lane's to clear though never its work to
retire, or where its own read of the lane's root finds residue no row names
(see [`lane-end <lane>`](#lane-end-lane)). It counts the rows whose second field
is `retire`, plus every `cache` row; a FOREIGN tree, another lane's branch and a
`gone` record count for nothing. It passes `LANES_EDIT` through and sets
`LANES_NO_FETCH=1`, having fetched in its own reads.

Every field is escaped — a backslash is written `\\`, a TAB `\t`, a newline `\n`
and a carriage return `\r` — so a path holding any of them is still one field
of one row; bytes that are not UTF-8 pass through as the bytes they are. The
command exits **0** (nothing to retire), **3** (something to retire — a tree of the
lane's on disk, live or not, a stale registration, an unpublished or merged
branch of the lane's, included scratch), or **2** (refused: bound elsewhere,
held by another session, managed, a lane with trees and no #97 snapshot, a read
failed — a lane root, a registration list, another lane's claim on a lane with
a tree, the register's lane logs, or under `--yes` the register's fetch — or an
error no read caught, which is never a traceback's exit 1 on a dry run). FOREIGN trees, links,
caches and sandboxes are never the lane's to retire. `--yes` exits 0 when every
act completed, 1 when one failed part-way (the rest go on — unless a register
line could not be written, which stops the rest; each failure is in
the table and in `DISPOSITION.md`, which is written even when an error no read
caught stops the run), and **2** when the self-check refused a removal; usage
is 64, a `--live` path that names no tree included.

### A triad: its legs, their worktrees and the paired trees (T018)

**Plan task T018 of the triad migration (opensoft/openRepoTools#186).** A lane's
checkout is an **assembly** when its own `project.yaml` declares a leg other than
itself — the standard's rule, as openRepoShape's `templates/assembly-root/project.yaml`
writes it: a top-level `legs:` list of `- role:` / `repository:` / `path:`, the
assembly's own leg at `path: "."`. One reader answers it for every lane tool:

```sh
lanes-edit.sh project-legs <checkout>   # <repository> TAB <path as declared> TAB <where it is checked out>
```

It is `lane-start`'s rung-5 reading of `legs:`, line for line (a test holds the two
equal), and reads `repository:` and `path:` only — the manifest confers nothing,
and `role:` is never read. 0 with legs; 8 with none (no `project.yaml`, or one naming
no leg but the checkout itself); 1 where the manifest is there and cannot be read,
which is never "no legs"; 64 usage. It touches no workspace.

**Why the sweep needed it.** A leg is a submodule: its `.git` is a file,
`git -C <assembly> worktree list` names none of its worktrees, and a feature's
trees in the standard's three-leg layout, `<assembly>/worktrees/<feature>/{spec,code}`,
are worktrees of the LEGS' repositories. So a code tree was invisible to the sweep,
to `lane-end`'s gate, to the handoff's poll and to the daily report unless it had
been recorded with the code leg as its checkout — and a lane with unpushed work in
one passed `lane-end`.

**Where the lane's checkout is an assembly**, `lane-worktrees sweep` also:

* reads every leg, and every dependency nested in one (an initialized submodule of
  the leg's own, as `code/upstream/openRepoShape` will be), as a **`leg` row** —
  `clean`, `dirty`, `unpushed`, `dirty+unpushed`, `absent` (not checked out here,
  or not there at all) or `unreadable` (a `.git` of its own that git cannot read,
  or a leg directory this account cannot search, #255). A dependency nested in a
  leg that git lists as not initialized has no row where its directory is missing,
  or empty and searchable: it was never initialized. But git lists a dependency it
  cannot search as not initialized too, so where its directory, or one above it,
  cannot be searched (or it holds a `.git` that git cannot read), it is an
  `unreadable` row, counted: a read that failed is never a clean dependency (#255).
  One whose directory holds a checkout that git can read (a clone made by hand,
  which git does not count as initialized) is read like an initialized one: it
  holds the lane's work all the same.
  A leg is the assembly's, shared by every lane the assembly homes, as the
  assembly's own checkout is: **no `--yes` acts on a leg
  checkout** (its branches are another matter: see `--branches` below). It is
  COUNTED (exit 3, and `lane-end`'s gate) only where its HEAD's own `Lane:` trailer
  is this lane's and it holds edits or commits origin lacks — the rule the sweep
  already applies to `.claude/worktrees` — or where it could not be read; one
  that holds another lane's work, or nobody's, is listed and counted for nobody;
* lists the worktrees git registers in **every leg** — a FOREIGN row names the leg
  it is registered in — so a tree recorded with the leg as its checkout is the
  lane's as any recorded tree is;
* takes **`<assembly>/worktrees`** as a root: a tree there that the lane's
  inventory does not record is the lane's by its HEAD's own `Lane:` trailer and by
  nothing else, because the paired root, like `.claude/worktrees`, is per feature
  and not per lane — and `lane-handoff` records a tree there only on that rule, or
  where the lane's own writers file names it (below), so the inventory does not
  take in other lanes' trees; a standalone clone there
  is FOREIGN; the root, a feature directory or a leg's directory under it that
  cannot be listed or searched refuses, exit **2**;
* with `--branches`, makes each leg's branches rows like the checkout's own, each
  naming its leg, and **`--yes` deletes a merged lane branch there exactly as it
  does in the checkout** — bundling first any commits only its reflog names, and
  deleting its origin branch too where the row says `delete+remote`. No `--yes`
  acts on a leg *checkout*. With `--fetch` (and under `--yes`), it asks each leg's
  origin.

A manifest, or a leg's own submodule list, that cannot be read refuses, exit
**2**. **A checkout with no `project.yaml` is asked nothing** — no helper call, no
read, no row — so a single repository's sweep, gate, handoff, report and `add` are
byte for byte what they were; `tests/test_triad_lane_tooling.py` holds every one of
them to the output `c45a452`'s tools printed.

**`lane-end`'s gate** counts a `leg` row marked `retire` as a "leg checkout" it
names, with the remedy a person's (commit and push from inside it; no `--yes` is
offered when legs are all it counted), and lists a leg that holds work and is not
this lane's below the table as NOT counted — whether the gate passes or not.

**`lane-handoff` and `lane-reconcile` (T018 (b)).** Where the lane's checkout is an
assembly, `lane-handoff` also looks in the **paired root**:
`<assembly>/worktrees/<feature>` and `<feature>/<each declared leg's path>`, at every
directory there with a `.git` of its own. The paired root is per feature and not per
lane — every lane the assembly homes keeps its feature trees there — so the handoff
takes a tree there as this lane's on the sweep's terms and on no wider ones. It
**polls and records a paired tree only where one of three things holds**:

* this lane's inventory already names it;
* its HEAD's own last `Lane:` trailer is this lane's, on a commit origin's default
  branch lacks — the sweep's own rule for that root;
* the `--writers-file` names it, because a writer with nothing committed yet has no
  trailer to be found by.

Those trees are in the WRITERS block and its count. **Every other checkout there is
another lane's, or nobody's**: one line in the top block under a heading of its own,
outside WRITERS — `- PAIRED ROOT, not this lane's: <path> — branch …`, with its last
commit and its HEAD's own trailer, which is what finds its lane. It is not counted, not
recorded, and never a writer for the next session to relaunch; its own lane's handoff
records it. So one lane's `lane-end` never refuses on another lane's paired work, and
its `sweep --yes` never acts on it. This is option (a) of the pre-PR review's M1, as
lane 1 ruled on 2026-10-17 (#186, comment 6240800023, item 1; the analysis's §7 item
12): a handoff that recorded every paired tree made other
lanes' trees the running lane's inventory, and a tree two lanes' inventories both name
is `keep`, counted, in both sweeps.

Every tree it polls is **recorded under the checkout it belongs to**. A worktree of a
leg's repository (a paired tree, or one `lane-worktrees add --checkout
<assembly>/code` made under the lane's root) is recorded under that leg, the path
`project-legs` prints and `add` records. A tree the inventory already records under a
checkout of the tree's own repository keeps it: a dependency nested in a leg
(`--checkout <assembly>/code/upstream/<dep>`) is no declared leg. Any other tree keeps
the lane's `dir`, as before. A leg that is not checked out here (never initialized, or
deinitialized) names no tree. A leg with a `.git` of its own that git cannot read, or
a leg directory this account cannot search (#255), is `unreadable`, the sweep's word:
one `- UNREADABLE LEG: …` line in WRITERS and a note on
stderr, and counted on stderr's `legs: N of the assembly's legs under <dir> unreadable`
line, never in WRITERS' `(N found)`. The lane is not held `SWAPPING` for the leg
itself. A polled tree that could then be of that leg's repository is one it cannot
place: its common git directory lies inside the assembly or its git directory (a
leg's `.git/modules/<leg>`) and is no readable leg's and not the assembly's own, or
the inventory records it under the unreadable leg. Such a tree is named, marked
`(NOT recorded)` in its WRITERS line, and not recorded, so the checkout `add` recorded
stays, and the lane stays `SWAPPING` for that tree. The `UNREADABLE LEG` line says how
many trees were withheld, or that none was. A tree of any other repository, such as a
worktree of an unrelated repository in the lane's own root, is recorded under the
lane's `dir`, as when every leg reads. A tree with a `.git` of its own that git cannot
read at all (a damaged `.git/modules/<leg>` takes every worktree of that leg with it)
is listed in WRITERS and counted, marked `(NOT recorded)` and `UNREADABLE`, never
recorded, and the lane stays `SWAPPING` for it. A checkout with no `project.yaml`
naming a leg passes over such a tree, as it always did. A worktree
a leg registers anywhere else is not polled
as a writer, just as the checkout's own registrations elsewhere are not;
`lanes-edit.sh lane-reconcile` reports it. The reconciliation reads what the legs hold
beside what the checkout holds. Every worktree git registers in a checked-out leg that
no sidecar names is a `TREE` row: `unmanaged`, or `stale-registration` where its
directory is gone, naming the leg. A checkout in the paired root that nothing else named
is `unmanaged`. A leg with a `.git` of its own that git cannot read, or a leg directory
this account cannot search, is a `TREE` row `unreadable` that requires recovery:
the sweep's rule and word for its `leg` row, which
it counts. The verdict is not `indeterminate` for it, so the sweep still lists and
counts that leg. Nothing is adopted, deleted or overwritten.

**A `project.yaml` that cannot be read is never "no legs".** `lane-handoff` still
completes (`R-A11-11`), but it does not poll the paired root. It says so in the top
block's WRITERS section (`- NOT POLLED: …`), files no sidecar for a tree it cannot
place (a tree of the lane's own repository is still recorded), and leaves the lane
`SWAPPING`. The reconciliation's verdict is `indeterminate`, said in the words the
sweep refuses with ("the legs of the assembly `<dir>` could not be read"): the sweep
reads that verdict first and refuses on it, exit **2**. The verdict is `indeterminate`
too where a leg's registrations, the paired root or a feature directory under it
cannot be listed, or a leg's directory under a feature cannot be searched. For the
same directories, and where the lane's inventory cannot be read (which paired trees it
names would then be unknown), `lane-handoff` gives the same three signs: a
`- NOT POLLED: …` line in WRITERS, a note on stderr, and the lane left `SWAPPING`.
So does an inventory that holds a record that is not a whole schema-1 one (#255):
`lane-trees` gives such a record its id, its schema (`<unreadable>`, `<none>`, an
unknown one, or #252's `<malformed>`) and any path it keeps, and the note names each
one by those three. It is never read as a record, as the sweep never reads one, so a
paired tree that only it names is not taken as this lane's writer, and the handoff
files no record over it.
**A checkout with no `project.yaml` is asked nothing**, and one whose
`project.yaml` names no leg but itself is asked only `project-legs`, which answers 8.
The handoff's output, top block and inventory, and the reconciliation, are byte for
byte what they were. `tests/test_triad_lane_tooling_handoff.py` holds them to the
output `c45a452`'s tools printed, on a repository laid out to look like a triad: a
submodule's worktree under `worktrees/<feature>/code`, and a tree recorded with the
submodule as its checkout.

**A parked paired tree is not the lane's to the sweep — a gap lane 1 has ruled on, and
not yet closed (docs/triad-lane-tooling-analysis.md §7, item 11).** On 2026-10-17 (#186,
comment 6240800023, item 2) lane 1 ruled that park's WIP commit carries a `Lane: <lane>`
trailer and that `resume` records the tree it rebuilds with `lanes-edit.sh
set-lane-tree`. Both are workBenches changes and neither has landed, so what follows
holds until they do. The sweep and `lane-end`'s gate
give a tree under `<assembly>/worktrees` to a lane by its HEAD's own `Lane:` trailer
and by nothing else, unless the lane's inventory names it. `make park` commits a dirty
leg tree as `wip: park <UTC time> — lane <lane>`, with no trailer, and `resume`
soft-resets those commits, leaving the work uncommitted on the commit below them. So a
parked or resumed paired tree is a FOREIGN row of `lane-worktrees sweep <lane>
--dry-run` ("… its HEAD carries no Lane: trailer of this lane's as its own"), counted
for nobody, and a gate that passes says nothing of it. **Before `lane-end` in an
assembly**, run that dry run and read every FOREIGN row under `<assembly>/worktrees`:
one whose HEAD, or whose branch's upstream, is a `wip: park … — lane <this lane>`
commit, or that holds uncommitted edits, may be this lane's parked work. Land it or
hand it over first, or record it with `lanes-edit.sh set-lane-tree <lane> <path>
--checkout <assembly>/<leg>` so the gate counts it. `lane-handoff` does not record it
for you: on the sweep's terms it records a paired tree with no trailer of the lane's
only where the inventory or the writers file already names it.

### Two protocol lines this act assumes (proposed for the amendment that ratifies it)

1. **A lane creates worktrees, never clones.** Every tree a lane works in is a
   `git worktree` of the estate's checkout, recorded in its inventory (#97) —
   which `lane-worktrees add` does in one act (below). A
   standalone clone under a lane root is FOREIGN to every sweep, and one that is
   a shared store or a local remote is LOAD-BEARING. A lane adds no local remote
   and no alternates.
2. **Scratch lives in the session scratchpad or the lane's state directory,
   never beside repositories.** `.lane-worktrees/<lane>/*-scratch`, `briefs/`
   and `bin/` are grandfathered into the archive-then-remove row; new scratch
   goes to `${XDG_STATE_HOME:-$HOME/.local/state}/openRepoTools/lanes/<lane>/scratch/`
   or the harness scratchpad.

### Making a lane's worktree — `lane-worktrees add` (#163)

**Creation records the tree, in the same act.** Nothing in this tooling made a
worktree before this verb — `lane-start` makes none, and every writer's tree was
a `git worktree add` typed into a brief — so the inventory was written only at a
handoff, and a lane that never handed off had none: lane `openRepoTools-3`'s was
empty while nine trees under its root were its own, and the sweep could not
judge them.

```sh
lane-worktrees add <lane> <slice> [--branch <name>] [--from <ref>] [--checkout <dir>] [--dry-run]
```

It makes `<parent of the lane's dir>/.lane-worktrees/<lane>/<slice>` as a `git
worktree` of the lane's recorded checkout (or `--checkout <dir>`, the root of a
checkout), on `--branch` (default: the slice) — checked out where that branch
exists, otherwise made from `--from` (default `HEAD`) **with `--no-track`**, so
a bare `git push` never aims a writer's commits at the branch it started from —
and then records it with `lanes-edit.sh set-lane-tree <lane> <path> --checkout
<dir>` (with `--writer` where the caller's session is a transcript uuid). The
path is the one line on stdout. It refuses, exit **2** and nothing made, a
managed lane or one whose ownership could not be read, a lane that records no
directory, an inventory that cannot be read, a path that already exists, a
branch name git does not take, and `--from` beside a branch that already exists.
**A record that fails after the tree is made — or a `git worktree add` that
fails after making it, as one does when a `post-checkout` hook fails — takes the
fresh tree and the branch this act made back out** (`git worktree remove`,
without `--force`, and `update-ref -d` at the commit it made) and exits 2; only
where something is left — the undo failed, a directory git does not register,
which is named and never deleted blind, or a new branch that has moved past the
commit this act started it at, which is kept so that commit stays reachable — is
the exit **1**, with the `set-lane-tree` that records it printed. Usage
is 64, and every flag of the sweep's is usage here.

A tree made any other way — a brief's own `git worktree add`, the harness's
`isolation: worktree` — is still legal git and is FOREIGN to every sweep until
`lanes-edit.sh set-lane-tree <lane> <path>` records it.

**In a triad (T018), a tree of a leg is made from the leg** — the guidance of
docs/triad-lane-tooling-analysis.md §4.4, proposal 1:

```sh
lane-worktrees add <lane> <slice> --checkout <assembly>/code
```

Where the lane's checkout is an assembly (its `project.yaml` declares a leg other
than itself, read by `lanes-edit.sh project-legs`), a tree made from any checkout of
a leg's repository — the leg itself, or a worktree of it, a paired
`<assembly>/worktrees/<feature>/code` included — is recorded with **the leg** as its
checkout (`<assembly>/<path>`), which every reader of the inventory enumerates; the
note names the leg, and says so where the checkout given was a worktree of it. Made
from the assembly itself (no `--checkout`), the tree is a worktree of the assembly,
whose legs a worktree does not check out: `add` makes it as before and says that its
legs are empty there, naming the `--checkout <assembly>/<leg>` that makes a leg's
tree. A lane whose checkout has no `project.yaml` is asked nothing, and its `add` is
what it always was. A manifest that cannot be read refuses, exit **2**, nothing made.

### The estate report — `sweep --all --dry-run --report`

The net under every actor that never runs `lane-end`: **creation outpacing
closeout as a number that is read every day.** It reads every lane's #97
inventory and snapshot in every `.lane-state` it can find (#170 G15, B8):
`<estate>/.lane-state`; beside every checkout the estate's shape walk finds,
so a nested checkout's lanes are read where #97 keeps them; under every
directory a walk of the whole estate finds, caches skipped, a plain directory
inside a repository included (`xFactory/xFactories/.lane-state`); beside every
lane's recorded `dir` in the register; and `$LANES_LANE_STATE_ROOT`. It also
reads the workspace repository's register on `origin/<branch>` (on `mode=primary`,
the database, through `register-grep`), and the disk —
**never the derived index** (Amendment 14 clause (b)) — and CHANGES NOTHING:
no fetch, no index refresh (`GIT_OPTIONAL_LOCKS=0`), no expiry. A byte that is
not UTF-8, in a path or in the workspace's `.gitignore`, is carried as itself,
never an error (#170 G10-G12).

```sh
lane-worktrees sweep --all --dry-run --report [--estate <dir>] [--post <file> | --post <owner/repo>#<n>]
```

| section | what is listed |
|---|---|
| FOREIGN repositories | a CLONE inside a worktree container (`.lane-worktrees/<lane>`, `.claude/worktrees`, `worktrees`, `<x>-worktrees`), and a second clone of an origin the estate already has a checkout of: path, size, last commit, owner (the last `Lane:` trailer), and LOAD-BEARING with its dependents where something leans on it |
| Orphaned worktrees of ENDED lanes | trees an inventory records, and directories under `.lane-worktrees/<lane>`, of a lane whose snapshot is `CLOSED` or whose log's last lane line is `ENDED` or `RETIRED` — with the sweep that retires them. Empty beside a record it could not read — a lane snapshot, an inventory, the register's lines, a directory the walk could not list — it says "none found in what could be read", never "none" (#170 B8); a failure that is no lane record (an ignored-file listing, say) is still a row of the last section but leaves "none" as it is (#179); so does Awaiting disposition |
| Unmerged branches | local branches of every checkout — and of every leg of an assembly (T018) — with no upstream, a gone upstream, a diverged one, or unpushed commits: tip, state, age, owner, and whether a worktree holds it. Never deleted; `main`, `master` and `rescue/*` are not listed |
| Root main divergence | a checkout whose local `main` is ahead of `origin/main`; where every changed path is under `handoffs/` or `lanes/`, the remedy is Amendment 4's — those live in the workspace repository and are pushed per write |
| Assembly legs with uncommitted or unpublished work | **only where the estate holds an assembly with a leg checked out** (T018): every leg of a canonical checkout whose `project.yaml` declares legs, holding dirty or untracked paths or a HEAD no origin ref holds, or that could not be read — each named with its assembly. A leg is a submodule (its `.git` a file), so it was never a canonical checkout; it is now read as a checkout of its own here, and in the three branch sections, whose leg rows end with `the <path> leg of <assembly>` |
| Awaiting disposition | a `rescue/*` branch (local or on origin) and a DIRTY inventory tree whose git directory has been still for `aging_days` (14), with owner and age |
| Caches and sandboxes | `--include-caches`' and `--include-sandboxes`' rows across every lane, by size |
| Ignored directories over `ignored_report_mb` | every ignored directory of every repository at or over 50 MB (`du -sk`) |
| Evidence-shaped paths | untracked or ignored — an ignored FILE as much as an ignored directory (#170 item 9) — `junit*.xml`, `MANIFEST*`, `*-report.md`, `*REPORT*.md`, `canary-*`, `*-evidence`, `deployment-evidence` inside a repository, and a directory of reports beside the repositories — each a finding to move to the evidence root below |
| Sweep archives past retention | `sweep --expire`'s own dry run: what WOULD expire, and what is past retention but kept because its rescue left origin. Expiring stays an act (`sweep --expire --yes`) |
| Workspace repository hygiene | the workspace's `.gitignore` lines it lacks (with the one command that adds them, its path quoted for the shell, #170 item 10), and bytecode already in its history (for a person's word: history is rewritten only on one) |
| Records the report could not read | a lane snapshot, inventory directory or record, or an inventory tree's `git status`, that could not be read; a directory the estate walk could not list, the estate root itself (#170 E10), the register's lane logs or its ENDED and RETIRED lines where the `git grep` failed (#170 G3), and a repository whose ignored or untracked files could not be listed (#179) — unknown is never absent, and the sections above may be missing what it holds |
| `status --all` findings | the estate command's ahead/behind, fork, shape-pin and parked-record lines, reported as what they are and never counted as dirt (`LANE_WORKTREES_STATUS` names the command when it is not beside this one) |

The summary table at its head also carries **the count of rescue branches** and
**the sweeps directory's size**, so the cleanup's own footprint is watched.
`--post <file>` writes the report there; `--post <owner>/<repo>#<n>` comments it
on that issue through `gh`; with no `--post` it is printed. `--all` and
`--report` come together, with no lane, no `--yes` and no `--expire` (usage, 64).

**`lane-start` runs it once a day per workstation**, after the row is written
and before the launch: the first start of a UTC day takes the stamp
`${XDG_STATE_HOME:-~/.local/state}/openRepoTools/report-<YYYYMMDD>.stamp`
(an atomic `set -C` create, so two starts at once run one report), removes
older stamps, and starts the report DETACHED — stdin, stdout and stderr closed,
in a subshell that exits at once, under `nohup` and, where there is a `setsid`
(Linux), in a session of its own, so a pane killed after the stamp was taken
does not take the report with it (#170 E8) — over the start's own
`$PROJECTS_ROOT`, writing `…/openRepoTools/reports/<UTC>.md`. The report names
every path, branch and lane of the estate, so it is private: the state and
reports directories are 0700 and the report is written under `umask 077`
(#170 item 11); where they cannot be made 0700 no report starts and no stamp
is taken, so the next start tries again (#179). Every step either works or is skipped in
silence: the report never delays a start and never fails one. `--dry-run`
starts none; `LANE_WORKTREES_REPORT=off` is the switch (both suites set it).

### Nothing new lands beside the code

The sweep retires what is already there; these keep more from arriving.

* **`tests/run.sh`** sends bytecode to
  `${XDG_CACHE_HOME:-~/.cache}/openRepoTools/pycache`, turns pytest's cache off,
  and roots every temporary directory of a run under
  `${XDG_STATE_HOME:-~/.local/state}/openRepoTools/tmp/<UTC>-<pid>/`, which its
  EXIT trap removes however the run ends — after stopping the suite's whole
  process group (TERM, then KILL at ten seconds, and only then the leader
  reaped, so a suite that ignores TERM cannot hold the lock for ever: #170 item
  3), so nothing it started outlives the run root or the lock. With
  a terminal on stdin the suite runs in the FOREGROUND instead, so `--pdb` or a
  `breakpoint()` can read it (a background group would be stopped by SIGTTIN,
  and the wrapper would wait on it holding the lock); it is then in the
  wrapper's own group, whose members are read before the suite starts, and
  what is there afterwards that was not, and is no sibling of the wrapper, is
  stopped the same way before the cleanup (#170 G14). `--basetemp` is the
  wrapper's own, and a caller's is refused, 64 (#170 G8). A SIGKILL is the one end no trap sees;
  the pid in the name is what lets `--include-sandboxes` tell its owner is gone.
* **One virtual environment per repository, outside the estate:**
  `${XDG_CACHE_HOME:-~/.cache}/openRepoTools/venvs/<repo>/`. `openRepoTools
  --install` names this repository's and says whether it is there; `tests/run.sh`
  runs from it when it has `pytest`. A `venv/` or `.venv/` inside a worktree is
  a legacy leftover for `--include-caches`.
* **One evidence root per repository:**
  `${XDG_STATE_HOME:-~/.local/state}/openRepoTools/evidence/<repo>/<UTC>-<slug>/`
  for JUnit files, manifests, reports, canary and deployment evidence — never
  inside a checkout and never beside the repositories. The report lists what is
  still found inside the estate.
* **The workspace repository never carries bytecode.** `openRepoTools wip init`
  seeds `__pycache__/`, `*.pyc`, `*.pyo`, `.pytest_cache/`, `.mypy_cache/`,
  `.ruff_cache/`, `node_modules/`, `.venv/`, `venv/` and `site-packages/` (a
  virtual environment under any other name) into the new
  workspace's `.gitignore` (a line the template already carries is not
  repeated). Every commit `lanes-edit.sh` makes asks git what its pathspec would
  stage (`git add --dry-run`) or holds staged already, and REFUSES, exit **2**,
  nothing staged, when any of it is bytecode, a cache, a dependency tree or a virtual environment — and
  offers the `.gitignore` lines a workspace without them lacks. A pathspec git
  cannot read (a malformed magic pathspec, an unreadable index) is refused the
  same way, never read as clean (#170 G13). Attachments are
  committed by hand, so the same question is a subcommand:

  ```sh
  lanes-edit.sh pathspec-check handoffs/<repo>/attachments/<slug>
  # 0 clean · 2 the offending paths on stdout, the offer on stderr, or the read failed · 64 usage
  ```

## Hand edits

**On `mode=primary` a hand edit of a rendered file is refused,** and never
committed: the files are the register's mirror, and `commit` refuses with 2 over
a pending edit of the register or of any file it renders. Restore the file, and
make the change through its verb. See "The SQL register".

After **any** hand edit made with an allowed tool (python read/write, `sed -i
--follow-symlinks`, a `>>` append), run
`LANES_LANE=<lane> ./lanes-edit.sh commit "<message>"` **immediately** — don't
leave it sitting uncommitted while you do something else. Prefer
`set-row-state` / `replace-in-row` / `append-line` over a hand edit in the
first place: they commit in the same call, so there is no window where the
edit sits uncommitted at all.

An uncommitted hand edit left in the worktree is no longer taken into anyone's
commit (Amendment 23 S3, ratified choice 9). A pending edit to the register
refuses every write but `commit` (2, nothing written), and a pending edit to a
path a write commits refuses that write. The capture that used to sweep it into
the next lane's own commit, or into a separate `LANES(pre-existing@…)` commit,
is withdrawn: the edit may be a peer's in this shared checkout, and only its
author knows what it is (0d84d34 and a1f2438 were captures: content intact,
authorship lost). So commit promptly — a hand edit is never truly "yours" in the
history until it is committed under your own lane's message, and until then it
stops the other writes from this checkout. `lanes-edit.sh commit` itself cannot
split a hand edit that mixes your row with someone else's: it warns, and
annotates its subject, when the dirty diff it is about to wrap touches a row
other than the one named by `$LANES_LANE`.

## What is forbidden

- **A whole-file write from memory.** This is the 2026-09-08 incident. If you
  find yourself about to write out all ~110 lines, stop: the rows you did not
  read are the rows you are about to delete.
- **`git add -A`, a bare `git commit`, `git stash`, `force-push`** — estate
  rules, and this checkout is shared with `handoffs/` and `workspaces/` and
  with every other lane on this workstation.
- **Committing `LANES.md` to a governed or product repository.** It lives on
  `main` of the workspace repository and nowhere else — never in an
  aggregation, a member, or any repo that is released, pinned or swept.
  (Before Amendment 5 this line read "it stays on `lanes`"; the branch that
  named is retired.)

## Snapshots

`LANES.md.snap-*` / `LANES.md.bak-*` at the aggregation root are the pre-git
backup habit. They are no longer required — git history is the backup — and
they are gitignored on `main`, so leaving them is harmless. The last pre-git
snapshot is kept deliberately:
`~/projects/xFactory/LANES.md.snap-20260909T002735Z-pre-git`.

## Adopting this on another workstation (Raven)

**Two commands, and the second one is the same one that creates a workspace
repository from nothing** (Amendment 9(c)). `wip init` is idempotent, so on a
machine whose repository already exists it clones it, adds nothing to a seeded
repository, writes the pointer file and runs `link-estates`:

```sh
openRepoTools --install     # the commands, the three skills, the three command files and the hook entry
openRepoTools wip init      # create or adopt the workspace, and link it
```

If the host was set up from `opensoft/workBenches`, `./setup.sh` has already
run both and there is nothing to type at all — **and that is the whole of
Amendment 11's decision 8(b) as `R-A11-13` corrected it: there is NO second
installer.** `setup.sh` already runs `openRepoTools --install`, so `lane`,
`lanes` and the `/restart` skill ride the step that is there rather than a new
one. What Evidence 5 actually measured was narrower and is worth saying: after a
machine rebuild the launcher was present and `lane-start` and `lane-end` were
**not on `PATH` at all**, so the launcher's documented degradation ran a bare
`claude --resume <uuid>` — no stamps, a derived session name, a window left
`claude`, three stampless restarts in one day. The gap was never that nothing
runs `--install`; it was that the installed `openRepoTools` predated the move
and placed no lane helper.

**And the launcher exports this workstation's name.** `$LANES_WORKSTATION` is
written once per host and exported into every session and container the launcher
starts (`R-A11-14`). Outside a container `hostname -s` still answers; **inside
one with no value every writer here refuses**, because a container's hostname is
the container's id and this log is never rewritten.

The warning from Amendment 3's "Raven setup (operator, Brett)" block still
stands and the linker does not do it for you: **diff Raven's local `LANES.md`
against this one and append its missing rows with `lanes-edit.sh add-row`
BEFORE the local file becomes the symlink.** Raven's file may hold rows this
register has never seen. `link-estates` never deletes a real file — it moves
one aside as `<path>.pre-link-estates-<UTC>` — so the content survives either
way, but a row nobody re-appends is a row nobody reads.

**From v1.3.0 `wip init` refuses, committing nothing, in two cases and in every
mode:** wherever `lanes/STORE` exists, the marker of an estate whose register
is a database; and where the tree a seed commit would build on carries another
file under `lanes/` but lacks `lanes/LANES.md` or `lanes/log/README.md`. On a
`mode=primary` workstation the `add-row` above is a register write. See "The
switch and `lanes/STORE`" and "The SQL register".

### Coming from the pre-move world

A workstation that ran lanes before Amendment 9 carries
`~/.local/bin/lane-start` and `~/.local/bin/lane-end` as **symlinks** into its
workspace checkout, placed by the old `link-estates`. Remove them before the
first `--install`, in this order:

```sh
rm -f ~/.local/bin/lane-start ~/.local/bin/lane-end   # while they are still symlinks
openRepoTools --install                               # places them as regular files
link-estates                                          # repoints ~/projects/xFactory/lanes-edit.sh
```

**You are not asked to remember it: `--install` refuses** (A9 Addendum 4,
R-A9-12). In its planning phase, before any of its artifacts is placed, it walks
every target in the bin directory and dies naming every one that is not a regular file,
what it is, and the one `rm` that clears them. `cp` FOLLOWS A SYMLINK, so an
install over these would leave the two commands UNINSTALLED — the targets stay
links — and would write the post-move bytes into `opensoft/brett-wip`'s working
tree, which is the repository every lane writes: `lanes-edit.sh`'s Amendment
23 S3 then refuses each write whose paths, or whose fast-forward, those bytes are
in the way of, on that workstation, until somebody runs `git checkout -- lanes/`.

It is not tidiness. workBenches' `setup-estate-commands.sh` refuses the WHOLE
estate install when any target "already exists as a symlink", and `setup.sh`
swallows that exit into one `⚠` line — so a host that skips this looks set up
and has no estate commands on it. The installed `link-estates` reports either
stale symlink it finds, with the command that replaces it.

**The helpers no longer resolve the register from their own real path.** They
read `~/.agents/workspace.yaml`, which is what makes an installed command in
`~/.local/bin` able to find a register that is nowhere near it.

Also drop the retired worktree if this workstation still has one:
`git -C ~/projects/xFactory worktree remove .lanes` (Eagle did this on
2026-09-10).

## Reading the history from before either move

**There have been two moves and they used different mechanisms**, so the two
halves of the history are read differently. The REGISTER moved in 2026-09-10
under Amendment 5, by `git subtree`, from the orphan `lanes` branch of
`opensoft/xFactory` into the workspace repository. The CODE — these four
commands, their suite and this manual — moved in 2026-09-13 under Amendment
9(f)(2), by ONE `git filter-repo` run with a written path map and an
`--allow-unrelated-histories` merge, from the workspace repository into
`opensoft/openRepoTools`. `git subtree split` was rejected for that second
move because it carves one prefix and renames nothing, while the move was two
source prefixes landing on three destinations with a rename on every path.

**The code half**, in a checkout of `opensoft/openRepoTools`, where
`--follow` crosses the rename and the unrelated-histories merge:

```sh
git log --follow --oneline -- lanes-edit.sh      # reaches lanes/lanes-edit.sh
git log --follow --oneline -- lane-start         # and so for lane-end,
                                                 # link-estates, repos.tsv,
                                                 # tests/test_lane_helpers.sh
                                                 # and this file
```

The rollback for that move is the annotated tag **`pre-amendment-9-move`** on
`opensoft/brett-wip`, placed at the commit that still carried all seven paths,
immediately before the strip. Checking it out and running its
`scripts/link-estates` puts a workstation back on the pre-move world with its
data untouched, because no data moved.

**The register half**, which is untouched by the 2026-09-13 move — no data
moved. `git subtree add` imports commits unchanged, so the 1242 register
commits made between 2026-09-09 and the Amendment 5 move carry their pre-move
path (`LANES.md`, at the root of the orphan branch), and git's default history
simplification stops at the subtree merge. Its own two halves are one command
each, in the workspace checkout:

```sh
git log --oneline -- lanes/LANES.md              # since the move
git log --oneline pre-move/lanes -- LANES.md     # the 1242 up to the import
git log --oneline --full-history -- LANES.md     # 1256: those, plus the 12 the
                                                 # orphan branch took while the
                                                 # move was in flight, which came
                                                 # in on a later sync and are not
                                                 # under the tag
git show <sha>:LANES.md                          # a lost row, pre-move
git show <sha>:lanes/LANES.md                    # a lost row, since
```

`pre-move/lanes` is an annotated tag on the last commit the orphan branch ever
took. The same shape applies to `pre-move/handoffs` and
`pre-move/workspaces`.

The legacy supervisor uses the current profile launcher's existing-TMUX path,
which keeps the exact operation as a child and forwards its operation/attempt
tokens. The historical #94 incident above describes the earlier launcher. A
respawn also forwards configured `LANES_LANE_STATE_ROOT` and
`AGENT_PROTOCOL_ROOT` with shell quoting so its durable intent remains readable.

Legacy lane rename is an explicit publication exception: it already holds the
writer mutex and now refuses unfinished/failed restart ownership before moving
inputs. Its existing four-file transaction uses EXIT rollback, including stamp
write failures; it is not crash atomic across those files. This change does not
claim stronger crash durability for ordinary rename. Restart preservation and
resume-stamp publishers use the atomic checksum-fenced publication helper.

Reconciliation with PR #97 keeps completed restart history under its original
lane name when a lane is renamed. Only the diagnostic lifecycle snapshot and
worktree inventory move to the new control root. The new canonical lane has no
inherited restart operation; unfinished or unreadable intents still refuse rename.

## Grouped project lookup

`lane-start` discovers new default checkouts directly under `$PROJECTS_ROOT`
and below up to two grouping folders, for example
`$PROJECTS_ROOT/developerTools/Example` or
`$PROJECTS_ROOT/business/Company/Example`. `park`, `resume` and `status` use
the same bounded discovery under their configured `$PROJECTS_DIR` (or the
existing home projects-directory defaults). Git repositories and estate
manifests stop traversal. `workTrees`, `worktrees`, `laneWorkspaces` and
`brett-wip-lanes` are supporting collections and are excluded.

Aliases reaching the same physical checkout count once. Distinct matching
checkouts require an explicit directory; discovery never chooses one by
group ordering. Explicit and recorded lane paths keep their existing
precedence, and a missing recorded path still refuses rather than silently
relocating a lane. Discovery does not move repositories, change pins, or
convert independent register clones into worktrees.

Estate commands also discover grouped clones for `--repo owner/name`. Multiple
distinct development checkouts with the requested origin refuse selection.
Linked worktrees, pinned submodules and bare repositories are not new default
lane checkouts. Explicit paths continue to support intentional lane worktrees.
Estate name lookup retains its existing projects-directory spelling precedence.

For an ambiguous estate name, stand in the intended estate and run `park`,
`resume` or `status` without a name. For an ambiguous new lane default,
use `lane-start --dir <path> <repo> <position>`.

Family compatibility aliases keep their logical folder name during estate
recognition and dispatch, even when the physical storage folder has a different
name. Physical identity deduplicates aliases after recognition; an invalid alias
cannot hide a valid family. New lane defaults continue to use canonical paths.
