# Contract: the render commit, Register-Seq and the push function

- Owner: lane 3. Tag: [L3]. Feature 007, `contracts/render-commit.md`, which tasks.md's T344 lands, and T320, T322, T326 and T327 build on.
- Source: design.md at Y, commit `a6497d00`, with operations.md at the same commit.
- Citations: `Y:<n>` is a line of design.md and `ops:<n>` of operations.md, both at Y.
- Where Y leaves a point to the plan or to a lane, this contract says so. Section 5 records lane 3's choices where lane 3 has made them.

## 1. The render commit (D15)

### Author, committer and dates

- "A render commit's author and committer are the identity the journal recorded" (Y:548–549).
- That identity is the writing environment's `user.name` and `user.email` at write time, and both are dated by the journal entry's commit UTC (Y:549–550).
- The purpose is "so that two renderers build the same commit and the history keeps today's attribution" (Y:550–551).
- The plan takes the identity from `<c>`: "The author identity (`user.name`, `user.email` of `<c>`), passed to `register commit` and stored in the entry (D15)" (Y:5379).
- The journal stores the author as `Name <email>` on one line, and the message as non-empty text with no carriage return (Y:1115–1117).
- The pass builds each commit with "its author and committer and both dates set to the entry's commit UTC, in UTF-8" (Y:2859–2860).
- The commit UTC is the journal's: "The journal entry also records the commit's `now()`, which dates the render commit" (Y:531–532), in whole seconds (Y:1128).
- Whoever pushes, the author is the journal's: "the commit's author stays the journal's `author`, so renderers still make identical commits whoever pushes" (Y:2357).

### How the commit is built

- The build is "a private index (`GIT_INDEX_FILE` in the checkout's git directory), `read-tree` of the parent, the changed blobs written with `hash-object -w` and staged with `update-index`, `write-tree`, and `commit-tree --no-gpg-sign -p <parent>`" (Y:2855–2858).
- Signing: "`commit-tree` runs with `--no-gpg-sign` (git 2.43's `commit-tree` ignores `commit.gpgSign` and signs only with `-S`, which the flag overrides" (Y:552–553); F29 keeps the flag: "`--no-gpg-sign` stays, since it overrides `-S` and costs nothing" (Y:5970).
- Encoding: "the encoding is pinned to UTF-8" (Y:554).
- Parent: "first parent is `origin/main`'s tip; each later commit's parent is the one before it" (Y:2861–2862).
- Tree: the build sets "each seq's render-set entries exactly to the images at `k`" (Y:5775), and a render commit "changes only the render set" (Y:2922–2923).
- Environment: "Every git call starts with the inherited `GIT_*` variables removed, and `LANES_NO_FETCH` dropped (D12)" (Y:5776).
- F29 gives a pin for each input that could change the sha (Y:5944):
  - blob bytes: "`hash-object --no-filters --stdin`, `-c core.autocrlf=false`" (Y:5948);
  - replace refs and grafts: `GIT_NO_REPLACE_OBJECTS=1` (Y:5949), which in git 2.43 does not cover `<git-dir>/info/grafts`, so `render_git_env` also sets `GIT_GRAFT_FILE=/dev/null`, with `advice.graftFileDeprecated=false` beside it as `GIT_CONFIG_COUNT` (`lanes-index:6208–6214` at `40a1f5e`);
  - author and committer name and email: `GIT_AUTHOR_*` and `GIT_COMMITTER_*` from the journal (Y:5950);
  - dates and time zone: `@<epoch> +0000` and `TZ=UTC` (Y:5951);
  - the `encoding` header: `-c i18n.commitEncoding=UTF-8` (Y:5952);
  - signature: `--no-gpg-sign` and `-c commit.gpgSign=false` (Y:5953);
  - message bytes: `-F -` with no cleanup; parent: `origin/main`'s tip as fetched (Y:5958–5959).

### The message

- The message is "the journal entry's message byte for byte" (Y:555), and the entry's message is "today's commit message exactly" (Y:2796), in the forms `LANES(<lane>@<ws>): …` or `LOG(<lane>@<ws>): …` (Y:2796–2797), `rename-lane`'s on primary excepted (Y:3451).
- `Register-Seq: <n>` goes "in its final trailer block, its last paragraph" (Y:556).
- Where the last paragraph is already a trailer block, the trailer is "appended as that paragraph's last line" (Y:556–557); F29 names such a block as "every line `Token: value` with a token of letters, digits and `-`" (Y:5964).
- Otherwise it goes "after one blank line as a new last paragraph" (Y:558), "rather than starting a second block" (Y:558–559); F29's cases are "a subject alone, or a body ending in prose" (Y:5965).
- "A subject alone never counts as trailers, since git reads the first paragraph as the title" (Y:5965).
- The message goes to "`commit-tree -F -` with no cleanup" (Y:5966).
- "It never runs `git interpret-trailers`" (Y:559), since its "parsing and `trailer.*` configuration would make a host's git version an input to the sha" (Y:559–561).
- The message rules have a test: it "asserts that form C reads `<k>` from each and that two checkouts give one sha" (Y:5968).
- A journal message holding a scissors line, or whose final trailer block already holds a `Register-Seq`, cannot be rendered. A scissors line is git's cut line after a comment string and one space, with any prefix, as `core.commentChar` allows; the code reading is line R's `9d68f1e`, whose `register commit` refuses such a message, and one whose final trailer block holds a `Register-Seq` key in any case, with 2 before it opens the store (lane 1, #165 6783051626). The pass reads each commit it builds back by form C, and where it reads anything but `<k>`, the build ends `build-failed` at that seq with nothing pushed, and stays there until the message is corrected (lane 3's ruling on SHOULD-2 of its review of T322, 2026-11-10).

## 2. Register-Seq

### The value

- It is the seq of the journal entry that the commit renders: the pass builds "for each seq `k` from `m + 1` to `t` in order" (Y:2854), and the journal's `seq` column is "= Register-Seq" (Y:1103).
- D15 writes the value as `<n>` (Y:556); the pass and F29 write `<k>` (Y:2859, Y:5963). The trailer is the only tie: "nothing in the journal ties a seq to a commit but that trailer" (Y:2354).
- The seqs on the chain: "Besides the import's baseline (seq 0, or `m` after a loss), which is journalled at `files`" (Y:2810–2811); "the cutover's entry (seq 1, `m+1` after a re-import, or `b+1` after a restore; D24) and the rollback's last seq" (Y:2807–2808); the rollback writes seq `k+1` (Y:3238–3239).
- A seq is not reused: "so a later cutover never reuses a `Register-Seq` that `main` carries" (Y:3177).

### Where it sits

- In the message's final trailer block, which is its last paragraph (Y:556); "That last paragraph is where git's `%(trailers)` reads it" (Y:561).
- "that last paragraph is the only place `%(trailers:key=Register-Seq,valueonly)` reads, so form C finds it" (Y:5967).

### Which commits carry it

- Render commits: "A render commit is one that carries `Register-Seq: <k>`, changes only the render set, and whose render-set tree equals the images at `k`" (Y:2922–2923).
- Operator entries' commits: "the operator entries' commits follow the same rule" (Y:563–564).
- The cutover's `lanes/STORE` commit: "It carries the entry's `Register-Seq`" (Y:3195); D21 has the fields "in a commit carrying the cutover's entry's `Register-Seq` (D24)" (Y:614–615); at a first cutover `lanes/STORE` is `Register-Seq` 1 (Y:260).
- The rollback's commit removing `lanes/STORE`: "with `Register-Seq` `k+1`, the journal's last seq" (ops:339).
- "Both marker commits carry `Register-Seq`" (Y:1403).
- In (ii′)'s marker-first window the marker commit carries `Register-Seq: 1` on origin while the switch is still at `files` (Y:2850); "Its commit `S` is pushed as a fast-forward of the tip `C`, carrying `Register-Seq: 1`" (ops:295).
- That window's abort commit, removing the marker, "carries no `Register-Seq`, since no journal exists yet" (ops:300).
- A `Register-Seq` "copied or forged past the head" (Y:2850) is reported as a divergence: "a last `Register-Seq` above the journal's head while the switch is at `primary`, recorded at the commit that carries it" (Y:2930).

### How it is read

- The guard (SVC-12, condition 3) needs a tip that "carries a `Register-Seq` of at least the declaration's `mirror-seq=<m>`" (Y:4456–4457).
  - Form C is "exactly one `git log --first-parent -n 200 --format='%H%x00%(trailers:key=Register-Seq,valueonly)'` on `origin/main` as last fetched" (Y:4460–4462).
  - "it takes the first non-empty value, and refuses 2 if none of the 200 carries one" (Y:4462–4463).
  - No `--grep`, since "`-n` counts matches, not commits walked, so the walk would be unbounded" (Y:4464–4465).
  - "Form C needs every render commit to write `Register-Seq: <n>` in its message's final trailer block (D15)" (Y:4465–4466).
  - "A tip older than `m` is a refusal, 2" (Y:3093). Lane 2 timed the lookup at "13 ms median and 16 ms p95 on 34,144 commits" (Y:4259).
- The renderer's pass includes this step: "find `m`, the last `Register-Seq` on `origin/main`'s first-parent history, and its commit `R`" (Y:2840–2841).
  - Form C in pages, `git log --first-parent -n 200 --skip <s> --format='%H%x00%(trailers:key=Register-Seq,valueonly)'` (Y:2843), "taking the first non-empty value, never `--grep`, the whole walk bounded by `LANES_GIT_TIMEOUT`" (Y:2844–2845).
  - "an operator seq counts as rendered once its own command's read-back has put it there" (Y:2841–2842).
  - "Unlike the guard, which refuses 2 after one page, the renderer pages on until it finds one" (Y:5772).
  - On a timeout, or no `Register-Seq` on the whole line: "the pass renders and pushes nothing, records why with the mirror state (D16), and the renderer ends" (Y:2846–2847).
  - A commit that a `stray-import` or `stray-revert` entry names is resolved, and its `Register-Seq` "does not count when the pass finds `m`" (Y:2849–2850).
  - At `primary`, `m` above the head `h` is a divergence at `R` through `record_mirror_state`; "At `files` the rule does not apply" (Y:2850).
  - `render --revert <R>` is the way out: "so the next pass does not count `R`'s trailer" (Y:2952–2953); the pass "renders on up to `h+1` on top of `R`, and from then on `m` is the revert's own seq" (Y:2954–2955).
- The rollback: "Right before step 1 it fetches `origin main` again and checks it for a divergence as the renderer recognises one, a last `Register-Seq` above the head included" (Y:3230–3232).
- `status` "Adds the switch, head `n`, generation, the mirror's last `Register-Seq` and the lag" (Y:4853).
- A declared outage records "`mirror-seq=<m>`, the mirror's last `Register-Seq` on origin when he declares it" (Y:3075–3076).
  - A rollback's declaration differs: "Its `mirror-seq` is `k`, the head that the rollback's transaction found, not the mirror's `m`" (Y:3107–3108), since "seq `k+1`, the rollback's own entry, is never rendered" (Y:3109).
  - "A guard whose ref carries a `Register-Seq` below `k` refuses with 2" (Y:3109–3110).
- The disaster path: "Take `m`, the mirror's last `Register-Seq` on origin, and `b`, the newest backup's head seq" (Y:3280–3281).
  - A re-import is done "with the head set to `m`, so `Register-Seq` stays monotonic in `git log`" (Y:3296–3297); its baseline "takes `M`'s newest `Register-Seq` instead of 0" (ops:374).
  - The after-loss cutover's entry is at `m+1` or `b+1`, "which it pushes, reads back and records (D24)" (Y:3315–3316).

## 3. The push function

### One function, three callers

- "One push function serves the renderer, `cutover`, `cutover --after-loss` and `rollback`" (Y:5801).
- It is phase 3 work: "Under the private-index form, phase 3 changes `lanes-index` and `tests/` only" (Y:5750), and the plan lists "their pushes of the operator entry with the renderer identity, the S4 read-back, and `record_marker`" (Y:5756).
- Operator commands "run with `LANES_LANE=<operator lane>` and go through that lane's checkout" (Y:3151–3152), "so each push they make is a lane's write" (ops:115); "No operator command holds a database transaction across a git act" (Y:3159–3160).

### The destination and its environment

- The refspec is "`<tip>:refs/heads/main`, never forced" (Y:2867).
- It "pushes with the renderer identity (D23), to an explicit `ssh://git@github.com/<owner>/<repo>.git`" (Y:2868–2869).
- `<owner>/<repo>` is "`_norm_repo` of the checkout's own `origin` URL" (Y:2870), "so the push names the repository that was fetched" (Y:2871); the plan adds that "it must equal `source_of`'s" (Y:5798).
- The push's environment: `GIT_SSH_COMMAND='ssh -i <render_key> -o IdentitiesOnly=yes -o BatchMode=yes'`, which T320's code runs as `ssh -i <render_key> -o IdentitiesOnly=yes -o BatchMode=yes -F /dev/null -o ControlMaster=no -o ControlPath=none` (`lanes-index:6235–6237` at `40a1f5e`; MUST-2 of lane 3's review of T320), `SSH_AUTH_SOCK` removed, `GIT_CONFIG_GLOBAL=/dev/null` and `GIT_CONFIG_NOSYSTEM=1`, and `--no-verify` (Y:5799); "Nothing is written to the checkout's config: no `set-url`, no `pushurl`" (Y:5799).
- The reason: "A deploy key authenticates only over SSH, and `git push origin` would use the checkout's HTTPS remote and the lane's own credentials, which the ruleset refuses" (Y:2875–2877).
- The key is "a deploy key in a 0600 file named by `lanes-index.conf`, used only in the push's own environment" (Y:647–648), and `render_key=` names it (Y:2357).
- "Every push of those paths uses it: the renderer's, and the cutover's and the rollback's commits of `lanes/STORE`" (Y:649–651).
- "Without `render_key=` (Q21(b)(A), and the bare-origin fixtures) the push goes to `origin` as it stands" (Y:2880–2881); under (A) the credential is "the push credential every writer holds today" (ops:116).

### Read-back

- "A push to a URL moves no `refs/remotes/origin/main`, so the read-back fetches `origin` and checks ancestry" (Y:2877–2879); "Push, then read back with `fetch` plus `merge-base --is-ancestor`" (Y:5777).
- For an operator entry the read-back is "a fetch of `origin main` whose first-parent history carries the commit, with its `Register-Seq`, and with `lanes/STORE` present after a cutover or absent after a rollback" (ops:116).
- Then the command "confirms it by Amendment 23's S4 read-back and records it with `record_marker`" (Y:2813–2814).

### Retries

- Renderer: "A rejected push moved no ref and touched no HEAD" (Y:2883–2884); it re-fetches, and where origin then carries `t` or more, "that counts as pushed" (Y:2885–2886).
- Otherwise "the pass rebuilds, six push attempts in all, as Amendment 23 bounds a writer" (Y:2886–2887); the plan reads it as "six attempts in all" (Y:5777), and a pass "makes at most six push attempts" (Y:5782). After the sixth: "and then the renderer ends with the lag shown by `status`; the next fork resumes" (Y:2887–2888).
- "a second push of the same sha, one after the other, is a no-op" (Y:2889–2890), and "two at the same moment end with one rejected, which refetches and finds nothing to push" (Y:2890–2891).
- Cutover: "rebuilds when handoff commits land meanwhile, six push attempts in all" (ops:270), "so a handoff commit on the tip only makes it rebuild" (Y:3192–3193), and "pushes fast-forward only, with the renderer's identity" (ops:271).
- Rollback: "it builds the commit on the tip only where the tip's render set still equals the images at `k`" (ops:340).
- "Each fetch and push is bounded by `LANES_GIT_TIMEOUT`" (Y:2892–2893).

### Refusals and exits

- At the destination: "A non-GitHub origin with `render_key=` set is refused with 2" (Y:2879–2880).
- "A push its read-back cannot confirm takes neither the success nor the abort branch until a later read-back settles it: the command exits 3, and its rerun reads origin back before it acts" (Y:3157–3159).
  - Cutover: "A push it cannot confirm exits 3, and the rerun reads origin back first" (Y:3195–3196); "A marker found there completes the cutover; a marker proven absent has the rerun push again, as a rerun that finds its own entry goes on to step 2, and step 2's own ends follow, its abort among them" (ops:275).
  - Rollback: "Push the commit removing `lanes/STORE`, seq `k+1`, with the renderer identity, and confirm it by read-back" (Y:3253–3254), and "a push it cannot confirm exits 3" (Y:3255).
  - The abort: "where a push was made, it first proves by read-back that the marker is absent from origin" (Y:3198–3199).
- `record_marker` after the read-back, on its timer's expiry: "69: the push stands, and the marker stays `adding` or `removing`, so writes keep refusing" (Y:3534).
- The renderer ends after "a pass that cannot progress (an authentication failure, a divergence, an operator seq its command has not yet put on origin)" (Y:2897–2899), and the plan's stop conditions include "a fetch, authentication or six-rejection failure, recorded with the mirror state" (Y:5793).
- "A fetch that fails ends the pass with nothing pushed" (Y:2864); at an operator seq "the pass renders nothing past it, since its command owns it" (Y:2863).
- At `files` the pass renders nothing and records no divergence, since nothing at `files` can clear one: after finding `m` it ends with the outcome `at-files`, 0 (lane 3's ruling on open point 2 of its review of T322, 2026-11-10). The one exception is a rollback's `files`, with the marker `removing`: its entry `k+1` stands and the marker is still on origin, so the pass renders as at `primary`, up to `k` and never `k+1`, an operator entry (S5's rollback, step 2; ops:337; AT-F2, "A rollback's entry at `k+1`"). N6 does not apply there (Y:2850), and a stray after `R`, or a render set at `R` that is not the images at `m`, ends the pass with nothing rendered and nothing recorded but `last_error` (S5's remaining gap) (lane 3's ruling on T326's point 1).
- "Killed between `commit-tree` and the push, it leaves only unreferenced objects" (Y:2908–2909).

## 4. Where Y tests it

- Test 5: "Test 5, two renderers racing, and a renderer killed between commit and push" (Y:5811).
- Test 6: "Test 6, the v1.1.0 `lanes-edit.sh` pushing to a primary fixture, then each way out" (Y:5812).
- "AT-F7 and AT-F11 test the bounded `Register-Seq` lookup, including no trailer within 200, which refuses 2" (Y:6631–6632); and "the renderer's bounded lookup of `m`, with no trailer found" (Y:6635–6636).

## 5. Left open by Y

- The push URL: "The push URL, the deploy key and `insteadOf` are from knowledge, not probed" (Y:6076); "Not probed: no network was available" (Y:5801–5802).
- Facts still to find: "whether the organisation's GitHub plan supports a push ruleset with a deploy-key bypass" (Y:6425–6426), "whether every environment has git 2.32 or later" (Y:6429), and "whether `ready` can prove the key's write right" (Y:6430–6431).
- The batch and cap: "The 64 and the 16 are the design's values, unmeasured" (Y:2904–2905); "six races lost to handoff pushes leave the last write unrendered until the next write anywhere, or `reconcile`" (Y:2905–2907). Y leaves to lane 3 "the two values, and whether to accept the gap after six lost races" (Y:6029). Lane 3 took them in its 007 call: 64 seqs a pass, 16 passes, and the gap accepted, which `reconcile` closes (research.md's PC-3).
- F29 pins not probed: the inherited environment (Y:5955), mode and path bytes on macOS (Y:5956), the object format (Y:5957). The message rules: "The git rules above are read, not probed; the test proves them" (Y:5968).
- A forged trailer beyond the head: "The forged-trailer case with a seq beyond the head was not probed" (Y:6075).
- F29's table still lists a pin that runs `interpret-trailers` (Y:5954); "So `lanes-index` composes the message itself and never runs `interpret-trailers`" (Y:5961). This contract follows D15 (Y:559): D15 governs (lane 3, fix round B, 2026-11-09), and T345 marks the row superseded before the archive.
- Y:6031–6032 list the `commit.gpgSign` and `interpret-trailers` texts as awaiting lane 3, while the composition "is ruled: `lanes-index` composes the message itself" (Y:6473–6474). D15 governs, since it now carries both fixes (lane 3, fix round B, 2026-11-09), and T345 marks rows 5 and 6 as applied before the archive.
- The message's bytes, lane 3's choice (ruling 4 of its review of T322 and T323, `REVIEW-007-T322-T323-aaef9f4`): trailing blank lines are dropped and the message ends with one LF; a message with no text is refused (69).
- Temp files instead of stdin (ruling 5): blobs and messages go through 0600 temp files, because `render_git` gives git no stdin; they sit under the git dir and are cleared with the index (its NIT-4).
- git versions (ruling 12): `--diff-merges` needs git 2.31 or later, and `--no-write-fetch-head` 2.29 or later, inside the 2.32 floor (Y:6429).
- `--no-verify`, lane 3's choice (ruling 2 of its review of T320 and T321, `REVIEW-007-T320-T321-da3689b`): the push applies `--no-verify`, `GIT_TERMINAL_PROMPT=0` and the removal of inherited `GIT_*` to the plain-origin form too, where Y:5799 lists them under the key form.
