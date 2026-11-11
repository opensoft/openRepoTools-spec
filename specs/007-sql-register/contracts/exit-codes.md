# Contract: exit codes (feature 007)

- **Owner of the `lanes-index` section:** lane 1 (`[L1]`), task T102. Each other lane adds its callers' arms in a section of its own below that section (plan.md's tree; [README.md](README.md)). A change to a `lanes-index` row goes through lane 1.
- **Keyed to commit Y, `a6497d00`.** `Y:<n>` is a line of Y's `design.md`, and `ops:<n>` a line of Y's `operations.md` (the contract writes `Y-ops:<n>`). `code/lanes-index:<n>` is the code leg at the pin Y cites.
- **The contract** is [register-cli.md](register-cli.md), lane 1's contract between groups A and B and the readers' forms, cited by section. This file was written against its draft 4.5, and checked against draft 4.6 (sha256 `028937e6…`), which keeps every line number and changes only lines of §3.6; a cite "draft 4.6, :n" is a line of draft 4.6. The copy in this branch is draft 4.19 (sha256 `43f16aa2…`), which keeps every line of drafts 4.16, 4.15, 4.14, 4.13 and 4.12, with §10, from draft 4.15, and §11, from draft 4.17, after the last line; draft 4.12 keeps every section, and every line to :637, and from :638 on each line is one below draft 4.11's. Its §2.7 75 row (:337) gives an LR004's moved head as lane 3's ruling (#165 6771318480), with no [P]. Where a row here and the contract differ, the contract holds, and the row is a fault in this file.
- **By lane openRepoTools-1, 2026-11-09.** It answers PC-10 (research.md's Part 3; plan.md) and the test plan's open point 1, "The tests read the design's constant".

**The marks:**
- **[Y]:** Y fixes it, at the line cited.
- **[C]:** the contract fixes it, at the section cited.
- **[T]:** lane 1's task text fixes it (block v4.5, as tasks.md carries it).
- **[P]:** Y leaves it open, and this is lane 1's proposal.
- **[open]:** nobody has fixed it yet; the task named fixes it here.

---

## The `lanes-index` section (lane 1, T102)

### 1. The codes, and their named constants

Each code is a named constant in `lanes-index`, and the tests read the constant, never a literal (T102 [T]; test-plan open point 1). The names are lane 1's proposal [P].

| Code | Constant [P] | sysexits | What it means from `lanes-index` | Source |
|---|---|---|---|---|
| 0 | `EXIT_OK` | — | done: committed, already committed, read, found or absent, written, or `mode index` | `code/lanes-index:72–76`; contract §2.7 |
| 1 | `EXIT_FAILED` | — | #160's: a read or a write failed, with nothing half-written, on `sync`, `reconcile`, `status` and `export`. Under `register read --for guard` only, a refused configuration (O14). From any other register command, a crash, which is not a contract code | `code/lanes-index:72–76`; contract §2.7 [C] |
| 2 | `EXIT_REFUSED` | — | a refusal, with nothing written | ops:402; contract §2.7 |
| 3 | `EXIT_IN_DOUBT` | — | it cannot be confirmed whether it landed: a commit call in doubt, a lookup that settled nothing, an operator push that its read-back cannot confirm. `export` keeps #160's own 3, "the index cannot answer" | D8 (Y:463); Y:3157–3159; ops:116; `code/lanes-index:72–76` |
| 64 | `EXIT_USAGE` | `EX_USAGE` | usage, raised before any connection is opened | ops:402; contract §2.7 |
| 69 | `EXIT_UNAVAILABLE` | `EX_UNAVAILABLE` | the database cannot be reached, or refuses | D10 (Y:511–512); ops:402 |
| 75 | `EXIT_BUSY` | `EX_TEMPFAIL` | a lost turn, "register busy", with nothing written | D7 (Y:455–456); contract §2.7 |
| 78 | `EXIT_CONFIG` | `EX_CONFIG` | Q6(c)'s refusal of `mode=primary`, from `mode` and `register read` only | D3 (Y:368–382); Y:4873 |

**PC-10's two constants** [P], each 2 (section 4):
- `EXIT_IMPORT_REFUSED`, the refusal of `import --at`;
- `EXIT_CUTOVER_ABORTED`, the cutover's abort, once it has completed.

**How the constants are kept** [P]:
- They sit beside `SCHEMA_VERSION` and `RENDER_GIT_HALF` (Y:4872), as module-level names the tests import.
- The task that first exits with a code defines its constant. Every exit of a new subcommand uses its constant. #160's own exits may keep their literals.
- Two constants may share a value. A test asserts the constant, and stderr tells two refusals with one code apart.
- At the pin, `lanes-index` exits only 0, 1, 2, 3 and 64 (Y:4873). 69, 75 and 78 are new to it.

### 2. Rules for every subcommand

- **A refused configuration is 2** (`code/lanes-index:72–76`; Y:4872). The contract fixes two exceptions: 3 on `journal --write-id` (CO-3, §3.4) and 1 under `register read --for guard` (O14, §2.7). `lanes-index mode` refuses one with 2 (Y:4830). [config.md](config.md) §4 lists the refusals.
- **Q6(c), while `RENDER_GIT_HALF` is False** [Y]:
  - `mode=primary` makes `mode`, and `register read` in every form, exit 78 (Y:2449–2451; Y:4872);
  - every other subcommand refuses it with 2, "as the configuration parser does" (Y:2451–2453), `serve` among them (FR-003);
  - under `register read`'s form 4, a 78 is a log that could not be read (contract §3.6, B4).
- **A schema that is not a scratch schema, while the constant is unset:** `cutover`, `rollback` and `set-mode primary` exit 2 (Y:4872) [Y].
- **Usage is 64,** raised before any connection is opened (contract §2.7; ops:402) [C].
- **A database that cannot be reached, or refuses, is 69** on every register subcommand (D10, Y:511–512) [Y], but for two ends:
  - `register commit`'s and `journal --write-id`'s in-doubt ends are 3 (contract §2.7, §3.3, §3.4);
  - `outage declare`'s probe, on Brett's word, is answered by the database's silence: the declaration is written (D9, Y:3535).
- **No other code.** A crash (1, or 128+n), or a code outside these rows, is not a contract code. Its caller reads it as the contract's "No other code" says (§2.7) [C].
- **Nothing secret on stderr:** no URL and no password (contract §2.6; Y:3797–3799) [Y].

### 3. The rows, by subcommand

#### 3.1 `mode`, and the register commands the contract fixes

These rows restate the contract's exits, one row per code. The contract's text governs each.

| Subcommand | Code | When | Source | Task |
|---|---|---|---|---|
| `mode` | 0 | prints one line, `index` or `primary` | Y:4830; contract §3.1 | T105 |
| `mode` | 2 | a refused configuration | Y:4830; contract §3.1 | T105 |
| `mode` | 78 | `mode=primary` while Q6(c)'s refusal is built in, with its fixed text | Y:4830; Y:4872; contract §3.1 | T105 |
| `mode` | 64 | usage | contract §2.7 | T105 |
| `register snapshot` | 0 | read; with `found=` where the write id is this write's own | contract §3.2 | T111 |
| `register snapshot` | 2 | check 1 (`LR007`, `DETAIL:  generation`) or check 2′ (`another-write`); a refused configuration; Q6(c) | contract §3.2 | T111 |
| `register snapshot` | 69 | check 3, the version window, or check 4, the switch not `primary` with the marker `present`; a database it cannot reach; any failed read, a 55P03 included; its timer `S` | contract §3.2; Y:3522 | T111 |
| `register snapshot` | 64 | usage: `--write-id <uuid>` without `--generation`; `--at` without `--tree`, or without `--generation`; a `--tree <dir>` that exists and is not an empty directory, or is a symbolic link; a `--checkout` that is not a directory | contract §3.2; lane 1's review of T111, #165 6775233959 (NITs 2 and 4); lane 3's post on round B13, #165 6779518728, and its batch 7, #165 6780364102 (item (2)) | T111 |
| `register snapshot --files` or `--all`, with `--out <dir>` | 0 | the images written, with `image=` rows | contract §3.6 | T111 |
| `register snapshot --files` or `--all`, with `--out <dir>` | 2 | a refused configuration, Q6(c)'s included, at load | contract §3.6 (C13) | T111 |
| `register snapshot --files` or `--all`, with `--out <dir>` | 69 | checks 3 and 4; a database it cannot reach; a failed read | contract §3.6 | T111 |
| `register snapshot --files` or `--all`, with `--out <dir>` | 64 | usage | contract §3.6 | T111 |
| `register commit` | 0 | `committed <seq>`; or `already <seq>` on LR006 | contract §3.3; Y:4832 | T112 |
| `register commit` | 2 | LR005, LR007, LR009 or LR010; a malformed attempt directory; a shipped `repos.tsv` that exists but cannot be read; a `msg` that holds a scissors line, or whose final trailer block already holds a `Register-Seq`, refused before it journals; a refused configuration; nothing sent, or nothing written | contract §3.3; lane 3's NIT, #165 6768006915 (item 4); lane 3's post on round B13, #165 6779518728, after its renderer ruling (`render-commit.md` §1) | T112 |
| `register commit` | 3 | every end of its `psql` but 0 and an exit 3 with a SQLSTATE: exit 1 or 2, a signal, the timer `C`, or an exit 0 whose answer it cannot read; LR004 makes one re-read and no resend, so no second call (lane 3, #165 6755615338) | contract §2.7, §3.3 (CO-6) | T112 |
| `register commit` | 69 | LR002, LR003, LR004 after its one re-read, or any other SQLSTATE, each under `psql`'s exit 3; a `psql` that cannot be started or is too old. An unreachable database is 3 here, never 69. After LR004, one re-read and no resend: a moved head is 75; a base that differs is 69, naming the image cache; equal bases are 69, naming the entry and LR004's own message; and a re-read that fails is 69 (lane 3, #165 6755386771, reading 2) | contract §2.7, §3.3 (CO-5, CO-6); lane 3's ruling, #165 6755615338 | T112 |
| `register commit` | 75 | LR001, 40001, 40P01, 55P03 or LR008, or an LR004 whose one re-read finds the head moved: a lost turn, nothing written | contract §2.7, §3.3; D7; FR-014; DEC-07; lane 3's rulings, #165 6755386771 (reading 1) and #165 6771318480 | T112 |
| `register commit` | 64 | usage; group B reads it as an internal error | contract §2.7 (CO-5) | T112 |
| `journal --write-id` (the in-doubt lookup) | 0 | `found <seq>` with its `workstation=` and `utc=` lines and its rows, or `absent` with the lock held | contract §3.4; lane 3's ruling, #165 6770688147 (T418 item 1) | T114 |
| `journal --write-id` | 2 | LR007 only, and Q6(c)'s refusal | contract §3.4 (CO-3) | T114 |
| `journal --write-id` | 3 | LR011; no try in the window gave an answer; any other SQLSTATE but 55P03 and 57014, which are tried again in the window; a refused configuration other than Q6(c)'s, the TLS refusal included; no `psql`, or one older than 15, with nothing sent; a crash or a code outside the contract's, with the record kept | contract §2.7, §3.4 (CO-2, CO-3); Y:3524; lane 3's ruling, #165 6755922262 (reading 3, and 57014 for lane 4); lane 3's NIT, #165 6768006915 (item 5) | T114 |
| `journal --write-id` | 64 | usage | contract §2.7 | T114 |
| `register classify` | 0 | one line out for each line in | contract §3.5 | T122 |
| `register classify` | 2 | a refused configuration, Q6(c)'s included | contract §3.5 | T122 |
| `register classify` | 64 | usage | contract §3.5 | T122 |
| `register read`, forms 1 to 3 | 0 | the text read, at `n` in forms 1 and 2 and at `n′` in form 3; or `mode index` | contract §3.6 | T113 |
| `register read`, forms 1 and 3 | 2 | a refused configuration, the TLS refusal included | contract §3.6 | T113 |
| `register read`, form 2 (`--for guard --prefetch`) | 2 | only "too late to verify" (Y:4344's row) | contract §2.7, §3.6 | T113; lane 5's T507 |
| `register read`, form 2 | 1 | a refused configuration, the TLS refusal included (O14) | contract §2.7, §3.6 | T113 |
| `register read`, forms 1 to 3 | 69 | `refuse db <cause>`, a failed direct read, or, in forms 1 and 3, its timer, form 2's timer being 2, too late to verify (:94); by either path, a switch not `primary` or a `REGISTER_SCHEMA_VERSION` outside the readers' window (draft 4.6, :653). Under `--for guard`, its first stderr line is the cause: through the service `lanes-index: refuse db <cause> <SQLSTATE>`, `<cause>` one of `unreachable`, `auth`, `switch-files` and `schema`, and the same line, with `-`, from a direct read that found the switch or the window failing (draft 4.6, :767). From form 2, with `.outage` where a declaration applies ([config.md](config.md) §6), and there too, where an unexpired declaration names this database, for a timer cut at the declared cap, `T0` + 3.65 s, which is 69 and not form 2's 2 (contract §3.6, draft 4.19, :776; lane 3's ruling, #165 6807694145) | contract §3.6 (draft 4.6, :653, :767; draft 4.17, :776); lane 1's review of T113, #165 6774791202 (MUST-2), which lane 3 takes, #165 6774846004 | T113; T507 |
| `register read`, forms 1 to 3 | 78 | Q6(c) | contract §2.7; Y:2449–2451 | T113 |
| `register read`, forms 1 to 3 | 64 | usage, an empty `--lane=` in form 2 included | contract §2.7, §3.6 | T113 |
| `register read`, form 4 (`--for guard --at <n>`) | 0 | the one log's files written | contract §3.6 (B4) | T113 |
| `register read`, form 4 | 69 | a log that could not be read: a missing `.head`, a generation that differs, an `--at <n>` above the head or below the generation's first seq, two logs that differ from the key only by case, a switch not `primary` or a `REGISTER_SCHEMA_VERSION` outside the readers' window (draft 4.6, :653), or a failed read | contract §2.7, §3.6 (B3, B4, C11; draft 4.6, :653) | T113; T507 |
| `register read`, form 4 | 2 | too late to verify: a log that could not be read, which the guard notes and does not refuse | contract §2.7, §3.6 (B4; O19) | T113; T507 |
| `register read`, form 4 | 1 | a refused configuration, the TLS refusal included: a log that could not be read | contract §2.7, §3.6 (B4; O14) | T113; T507 |
| `register read`, form 4 | 78 | Q6(c): a log that could not be read | contract §2.7, §3.6 (B4) | T113; T507 |
| `register read`, form 4 | 64 | usage, an empty `--lane=` included: a log that could not be read | contract §2.7, §3.6 (B4) | T113; T507 |
| `journal --since <m>` | 0 | the entries above `<m>` | contract §3.6 | T114 |
| `journal --since <m>` | 2 | a refused configuration, Q6(c)'s included | contract §3.6 | T114 |
| `journal --since <m>` | 69 | a failed read | contract §3.6 | T114 |
| `journal --since <m>` | 64 | usage | contract §3.6 | T114 |
| `journal --last` | 0 | read, with or without a `seq=` line | contract §3.6 (C7) | T114 |
| `journal --last` | 2 | a refused configuration, Q6(c)'s included | contract §3.6 | T114 |
| `journal --last` | 69 | a failed read; with `--at <n>`, an `n` above the head or a generation that differs; a replay mismatch | contract §3.6 (C8); lane 3's ruling, #165 6755922262 (reading 5) | T114 |
| `journal --last` | 64 | usage; `--at` without `--generation`; `--generation` without `--at`, an empty `--grep`, and an empty `--path` or one holding a control character | contract §3.6 (C8); lane 3's ruling, #165 6755922262 (reading 5) | T114 |
| `render --images --at <k> --out <dir>` | 0 | the images at `k` written, with `file=` rows | contract §3.6 | lane 3's T301 |
| `render --images` | 2 | a refused configuration, Q6(c)'s included | contract §3.6 | T301 |
| `render --images` | 69 | a failed read; an image that could not be written under `<dir>`, which is left as it was; and [P] a `k` it cannot render, above the head or before the latest import's baseline, which tasks.md's T301 leaves to this file. It is 69 as form 4 and `journal --last --at` end 69 for an `--at` outside the generation's seqs. [P] An image whose path holds U+0000–U+001F, U+007F, U+0085, U+2028 or U+2029 cannot be a `file=` row, and ends 69. | contract §3.6; line R's merge11, `15d1662b` (lane 1's `f1a2a2d`), for lane 5's NIT 3, #165 6779760684; lane 3's post, #165 6783185080 | T301 |
| `render --images` | 64 | usage | contract §3.6 | T301 |
| `render --checkout`, before T321 | 64 | a form this build does not have | contract §3.6; lane 3's ruling, #165 6754819099 (choice 4) | group C (lane 3) |
| `render --checkout <c>`, in group B's phase-2 tests | 69 | "git half not installed": the answer of group B's test stub, T207's `contract_stub`, which stands in for the form in group B's phase-2 tests; not an answer of the real `lanes-index` | contract §3.6, §3.7; lane 3's ruling, #165 6755449007, item 1 | lane 2's T207 |
| `render --checkout <c>`, from T321 on | 2 | Q6(c)'s refusal, at load, before any answer | contract §3.6 (C13); Y:4872 | group C (lane 3) |

#### 3.2 The operator commands: `import`, `cutover`, `rollback` and `outage`

Each runs with `LANES_LANE=<operator lane>`, through that lane's checkout (D24, Y:653; ops:115).

| Subcommand | Code | When | Source | Task |
|---|---|---|---|---|
| `import --at <C>`, and with `--after-loss` | 0 | the proof passed, and the proved entry was committed (ERR-55), in one short transaction; a rerun proves and swaps again; with `--dry-run`, the proof passed and nothing was swapped in | Y:3169–3175; contract §11 | T115 |
| `import --at <C>` | 2 | `EXIT_IMPORT_REFUSED`, PC-10's code, with nothing swapped in: a render that is not byte-identical to `C`'s render set by R3's three-part proof, stderr naming the first differing path; a key not well formed (LR010); a source that is not origin; the switch not at `files` (section 4); and T115's own refusals, which contract §11 lists: no usable `LANES_LANE`, workstation name, checkout of the operator lane's own (a `LANES_REPO` that names W among them, and, from T115's follow-up 1, a W that cannot be read while `LANES_REPO` is set) or git author; a failed or timed-out fetch, never 69; a file of `C`'s render set that the register cannot hold; `--after-loss` over a chained journal, or at an `M` with no `Register-Seq`; and a plain import over an after-loss root | Y:3169–3173, Y:3178–3179 [Y, the refusal]; T102, T115 [T, the code]; contract §11; lane 3's batch 9, #165 6781033556 (item (2)) | T115 |
| `import --at <C>` | 2 | a refused configuration; Q6(c) | section 2 | T115 |
| `import --at <C>` | 69 | the database cannot be reached or refuses, or the 330 s timer expires: nothing is swapped in, but where the swap's own call gave no answer, whether it committed is not known, and a rerun reads the register again and replaces it | D9 (Y:3530); T115; contract §11 | T115 |
| `import --at <C>` | 64 | usage | section 2 | T115 |
| `cutover`, in every form | 0 | the entry committed, its marker commit pushed as a fast-forward and confirmed by read-back, and the marker recorded `present`. Until T327, T116's 0 is the transaction alone: nothing pushed, the marker `adding` [T] | Y:3185–3196; lane 3's ruling, #165 6770600106 (T116's point 1) | T116; lane 3's T327 |
| `cutover` | 2 | `EXIT_CUTOVER_ABORTED`, PC-10's code: the abort, once it has completed (section 4) | Y:3197–3203; D24 (Y:653); T102, T116 [T, the code] | T116; T327 |
| `cutover` | 2 | its label refusals (N4, S-2): a file with no `register_label=`, before it connects; at `files`, a `register_label=` that is not the switch's label, before it sends anything; at `primary`, an entry whose label does not differ from the switch's, which the transaction refuses with LR004 | ops:379 | T116 |
| `cutover` | 2 | a schema that is not a scratch schema, while the constant is unset | Y:4872; T116 | T116 |
| `cutover` | 2 | a refused configuration; Q6(c) | section 2 | T116 |
| `cutover` | 2 | a `HOLD` take in `main_slots` more than 30 minutes old is open: before its step 1 it sets nothing, and lists each such line with its lane, repository and time | Brett's "Holders release + check (Recommended)", 2026-11-10T22:58:57Z, as lane 3 relays it, #165 6782534165 | T116 |
| `cutover` | 3 | a push that its read-back cannot confirm: the marker's, or, in the abort, the proof that the marker is absent from origin. The rerun reads origin back before it acts | Y:3157–3159, Y:3195–3196; ops:116 | T327 |
| `cutover` | 69 | its transaction's timer expired, with the switch unknown: the rerun reads the switch and the journal first, and goes on from its own entry where it finds one | D9 (Y:3532) | T116 |
| `cutover` | 69 | `switch_to_files`'s timer expired, so the abort has not completed: the rerun reads the switch. At `files`, or back at the restored generation with the head at `b`, the abort is done; otherwise it runs again | D9 (Y:3533) | T116 |
| `cutover` | 69 | `record_marker`'s timer expired: the push stands, the marker stays `adding`, and the rerun reads origin back and records it again | D9 (Y:3534) | T327 |
| `cutover` | 69 | the database cannot be reached or refuses | D10 (Y:511–512) | T116 |
| `cutover` | 64 | usage | section 2 | T116 |
| `rollback` | 0 | done, through step 4; or `--hold-marker`, stopped after step 2 | ops:337–340; Y:3250 | lane 3's T304, T326 |
| `rollback` | 2 | step 0: `diverged at` recorded, or found by its fresh fetch, or LR012 inside step 1's transaction | Y:3227–3236; ops:330–331 | T304; T326 |
| `rollback` | 2 | a schema that is not a scratch schema, while the constant is unset | Y:4872; T304 | T304 |
| `rollback` | 2 | a refused configuration; Q6(c) | section 2 | T304 |
| `rollback` | 3 | step 4's push, which its read-back cannot confirm | ops:340; Y:3157–3159 | T326 |
| `rollback` | 69 | the database unreachable at step 1, with neither the switch nor the marker changed; a call's timer | ops:335; D9 (Y:3529) | T304 |
| `rollback` | 75 | D7's 30 s deadline across step 1's retries, with nothing written and the switch still `primary` | ops:331; D9 (Y:3529) | T304 |
| `rollback` | 64 | usage | section 2 | T304 |
| `outage declare` | 0 | the declaration written ([config.md](config.md) §6) | ops:496–500 | T121 |
| `outage declare` | 2 | each of ops §2.5's refusals, in its order: the database answers from this host (but for the `--rollback` form while the switch reads `files`); no `--until`, on Brett's word; `--rollback` without `--mirror-seq <k>`; with it, a switch at `files` whose entry at `set_seq` is not a `rollback`, or `<k>` not `set_seq - 1`; with it, a probe that cannot read the switch; Brett's word while the probe reads `files` after a rollback, which prints the line to run instead | ops:502–507 | T121 |
| `outage declare` | 2 | no `mirror-seq`: a host that cannot fetch, with no `--mirror-seq <m>` | ops:497 | T121 |
| `outage declare` | 2 | a `--why` that holds a line break | DEC-21; T121 | T121 |
| `outage declare` | 2 | [P] a configuration directory it cannot write, such as a bench's read-only mount: declare it from the host, as `set-mode` refuses (ops:470). Nothing is left half-written, since the file goes in by temp file and rename | ops:470; ops:500 | T121 |
| `outage declare` | 2 | a refused configuration; Q6(c) | section 2 | T121 |
| `outage declare` | 64 | usage | section 2 | T121 |
| `outage clear` | 0 | the file removed; and [P] where none is recorded, saying so, so a clear on every host can be repeated | ops:541 | T121 |
| `outage clear` | 2 | [P] a configuration directory it cannot write, as `outage declare`'s; a refused configuration; Q6(c) | ops:470; section 2 | T121 |
| `outage clear` | 64 | usage | section 2 | T121 |

`outage declare` never exits 69 for a database that does not answer: on Brett's word that silence is the declaration's premise, and its probe's every end before the read completes counts the same (D9, Y:3535). In the `--rollback` form, the same silence is 2 (ops:506).

#### 3.3 The operator tools: `bootstrap`, `login`, `ready` and `set-mode` (ops §2.4)

ops §2.4's four rules hold for each (ops:402–405) [Y]:
- **Exits:** `lanes-index`'s own 0, 2 for a refusal and 64 for usage, plus 69 where the database cannot be reached or refuses (ops:402);
- **Secrets:** none read from the environment (ops:403);
- **Connections:** no connection string in any argument list; the admin connection only as `PGSERVICE=<service>` (ops:404);
- **Bounds:** D9's, set when each connection is made (ops:405).

| Subcommand | Code | When | Source | Task |
|---|---|---|---|---|
| `bootstrap` | 0 | each object `created` or `unchanged` | ops:422–425 | T109 |
| `bootstrap` | 2 | an argument carrying a connection string or a password; an admin that may not create roles or schemas, or does not own the database; a schema newer than this build's `REGISTER_SCHEMA_VERSION`; a role holding a privilege outside its scope; `store=sqlite` | ops:429–433 | T109 |
| `bootstrap` | 2 | stdout not a terminal | ops:426, with ops:402's 2 for a refusal | T109 |
| `bootstrap` | 69 | the admin connection fails; [P] a migration's lock wait refused, which D9 says to rerun (Y:3531), since ops:402 gives 69 where the database refuses | ops:428; ops:402 | T109 |
| `bootstrap` | 64 | usage | ops:402 | T109 |
| `login` (`add`, `rotate`, `revoke`, `--dump-job`) | 0 | each step's result printed | ops:482–484 | T110 |
| `login` | 2 | an engineer name that is not `^[a-z][a-z0-9_]{0,40}$`; an argument carrying a connection string or a password; stdout not a terminal, under the bootstrap's rule for a secret | ops:481, ops:487; T110 | T110 |
| `login` | 69 | the admin connection fails | ops:487 | T110 |
| `login` | 64 | usage; `rotate` with neither an engineer nor `--dump-job`, or with both | ops:402; T110 | T110 |
| `ready` | 0 | every check passed | ops:458 | T119 |
| `ready` | 2 | any check failed, each failure named. A database it cannot reach fails check 4, so `ready` exits 2, never 69 (ops:458 over ops:402; section 5's note) | ops:458 | T119 |
| `ready` | 64 | usage | ops:458 | T119 |
| `set-mode primary` | 0 | the `mode=` line rewritten; where the file already says `primary`, nothing is written, every other check is still made, and it exits 0 with its line, ending `: the file says so already; nothing was written` | ops:465–466; lane 3's ruling, #165 6769217709 (reading 4); lane 3's ruling, #165 6778959652 (T120 fu1 Q1) | T120 |
| `set-mode primary` | 2 | the switch not `primary` with the marker `present`, or a fetched `origin/main` with no `lanes/STORE` naming this database, which needs `store=primary` and a `database=` equal to the configured `register_label=` and to the switch's `database_label`; `register_label=` absent, or not the switch's label; its own fetch of `origin` failed or timed out, a fetch bounded by `LANES_GIT_TIMEOUT` and made under `LANES_NO_FETCH` too, never 69; the configuration as it would be written, at `mode=primary`, refused by `load_config`; Q6(c)'s refusal still built in; where a write is needed, a file it cannot rewrite (a read-only mount, or a file or directory this account cannot write); `store=sqlite` | ops:468–471; lane 3's rulings, #165 6769217709 (readings 1 to 3); lane 3's rulings, #165 6778959652 (T120 fu1 Q3, Q4) | T120 |
| `set-mode primary` | 69 | the database is unreachable | ops:472 | T120 |
| `set-mode index` | 0 | rewritten, with a warning where the switch still reads `primary`, and a note on stderr, not a refusal, where `load_config` still refuses the file as written; where the file already says `index`, nothing is written, and it exits 0 with its line, ending `: the file says so already; nothing was written` | ops:473; lane 3's rulings, #165 6769217709 (readings 1 and 4); lane 3's ruling, #165 6778959652 (T120 fu1 Q1) | T120 |
| `set-mode`, either | 64 | usage; `set-mode index` refuses on nothing else but one ground, which is 2: a file it cannot rewrite, where a write is needed, or cannot read as a regular UTF-8 file, as on a read-only mount, a file that is not regular or not UTF-8, or a named `--config` that does not exist; it never refuses on the configuration's other keys | ops:473; lane 3's ruling, #165 6769217709 (reading 1); lane 3's ruling, #165 6778959652 (T120 fu1 Q3) | T120 |

#### 3.4 #160's subcommands, extended: `status`, `reconcile`, `sync` and `export`

The index half keeps #160's codes, 0, 1, 2 and 64, and `export` its 3 (`code/lanes-index:72–76`). The rows below are the register half's.

| Subcommand | Code | When | Source | Task |
|---|---|---|---|---|
| `status` | 0 | read: the switch, `n`, the generation, the mirror's last `Register-Seq` and the lag, `diverged at`, the pending records and a declaration left in place | Y:4853; T117 | T117 |
| `status` | 69 | the register cannot be read, or its timer expires; it still prints the lag from git where it can read it; 69 whatever #160's half gave, and where the register reads, `status` keeps #160's own 0 or 1 | D9 (Y:3528); lane 3's ruling, #165 6770600106 (T117's point 1) | T117 |
| `status` | 2 | a refused configuration; Q6(c) | section 2 | T117 |
| `reconcile` | 0 | the read-only check done; with `--dry-run` that alone, and a mirror that differs from SQL is reported on stdout with exit 0, as `status` reports a lag. [P] Without `--dry-run`, T325 then re-renders what the mirror lacks through T322's pass, and the exit is the render's: 0 where it rendered, nothing was missing, an operator entry is next, the switch is at `files`, or another render holds the lock | Y:4854; `code/lanes-index:2563`; T322 | T117; lane 3's T325 |
| `reconcile` | 1 | [P] without `--dry-run`, a stop the re-render records with the mirror state: `diverged at`, a failed fetch, walk, build or push, or six rejected pushes, as `render --checkout`'s 1 (AT-6's stop; lane 3, #165 6809045090, L2-6) | T322; TP:1285 | lane 3's T325 |
| `reconcile` | 69 | the register cannot be read, or its timer expires | D10 (Y:511–512); D9 (Y:3527) | T117 |
| `reconcile` | 2 | a refused configuration; Q6(c) | section 2 | T117 |
| `sync` of the register's source (none, `register`, or this workstation's `register:<owner/repo>`), on primary | 0 | nothing done, and it says so | Y:4854; lane 3's ruling, #165 6770600106 (T117's point 2) | T117 |
| `export` | 0, 1, 2, 3, 64 | #160's, unchanged | `code/lanes-index:72–76` | — |

#### 3.5 Other lanes' `lanes-index` subcommands

Y fixes these codes, and the owner's task fixes the rest. The owner adds or corrects its rows here, through lane 1.

| Subcommand | Code | When | Source | Owner |
|---|---|---|---|---|
| `render --revert <sha>`, `import --single <sha>` | 3 | their commit call in doubt, with D8's rerun, as a writer's commit | D9 (Y:3536); T324 | lane 3's T324 |
| `render --revert <sha>`, `import --single <sha>` | 69 | their other calls: a failed read, or in doubt (3) where an earlier commit call of the same entry was | D9 (Y:3537) | lane 3's T324 |
| `render --checkout`, `--until` | — | phase 3's ends are group C's; phase 2's answer is section 3.1's | contract §3.6, §3.7 | lane 3's T321 to T323 |
| `render --checkout <c>`, from T323 on | 0 | rendered, nothing missing, an operator entry next, or the switch at `files`; or another render holds the lock and renders again for this one | `cmd_render_checkout`'s docstring (`lanes-index:7578–7594` at lane 3's local `40a1f5e`); lane 3's ruling on its review's open point 6 | lane 3's T321 to T323 |
| `render --checkout <c>`, from T323 on | 1 | a stop that is recorded with the mirror state (`diverged at`, a failed fetch, walk, build or push, six rejected pushes), a render lock or rerun mark that cannot be used, or a `StoreUnusable` no pass caught | `cmd_render_checkout`'s docstring (`lanes-index:7578–7594` at lane 3's local `40a1f5e`); lane 3's ruling on its review's open point 6 | lane 3's T321 to T323 |
| `render --checkout <c>`, from T323 on | 2 | a refused configuration (Q6(c)'s refusal of `mode=primary` included, at load, before any answer: C13; a push `render_push` refuses), or a `<c>` that is no git checkout | `cmd_render_checkout`'s docstring (`lanes-index:7578–7594` at lane 3's local `40a1f5e`); lane 3's ruling on its review's open point 6 | lane 3's T321 to T323 |
| `render --checkout <c>`, from T323 on | 64 | usage | `cmd_render_checkout`'s docstring (`lanes-index:7578–7594` at lane 3's local `40a1f5e`); lane 3's ruling on its review's open point 6 | lane 3's T321 |
| `render --checkout <c>`, from T323 on | 69 | the register could not be read, or its rows do not replay (Y:3527: the render ends, and the lag stays) | `cmd_render_checkout`'s docstring (`lanes-index:7578–7594` at lane 3's local `40a1f5e`); lane 3's ruling on its review's open point 6 | lane 3's T322 |
| `serve` | 2 | unless the configuration says `primary`, Q6(c)'s refusal included | Y:3948–3953; FR-003 | lane 5's T512 |
| `serve` | 2 | a run directory that fails its check, which is never stamped: `serve` exits 2 naming the directory, and `lanes-index status` reports the failure itself (lane 3's OP-9, #165 6746215510, against Y:3928's `dir-check`); a socket path over 103 bytes, with its stamp, `path-too-long` | Y:3765; Y:3779–3780; Y:3926–3930; OP-9 | lane 5's T512 |
| `serve` | 2 | [P] the stamp `config-refused`: a refused configuration, or a URL parameter SVC-2 does not map, as every refused configuration is 2 | Y:3812–3815; Y:3928–3929 | lane 5's T512 |
| `serve` | 2 | [P] the stamp `no-psql`, lane 5's proposal (#165 6746631207), which lane 1 takes: as with `path-too-long`, the directory check and `config-refused`, it is an end before the socket listens. Whether the service ever answers `refuse` without `psql` (`test-plan.md`:2028–2030) stays T511's OP-10, for lanes 2 and 3 | Y:3926–3929 | lane 5's T512 |

### 4. PC-10: the import's refusal and the cutover's abort, each 2

The test plan's open point 1 left both unnamed, and plan.md's PC-10 gives them to this file. Lane 1 proposes 2 for each (T102, block v4.5:89–90) [T].

**`import --at`'s refusal: 2, `EXIT_IMPORT_REFUSED`.**
- **What it covers:** a failed proof, a bad key (LR010), a source that is not origin, or a switch not at `files` (T102). The proof is Y's: the render byte-identical to `C`'s render set, by `git diff --quiet` and R3's three-part proof (Y:3169–3172). Stderr names the first differing path (AT-7's oracle).
- **What it leaves:** nothing swapped in. The import "only then swaps the staged tables in, in one short transaction" (Y:3172–3173), so a refusal leaves the tables, and any earlier import's seq 0, as they were.
- **Not 69:** a database that cannot be reached or refuses is 69, as D9's import row says (Y:3530). So the runbook tells "the files and SQL differ" from "the database is away" by the code alone.

**The cutover's abort: 2, `EXIT_CUTOVER_ABORTED`, once the abort has completed.**
- **What it covers:** the tip's `lanes` tree has moved since `C`, so the cutover pushes nothing; or a push was made, and a read-back proves the marker absent from origin. Then `switch_to_files`, in one transaction, sets the switch back to `files` with the marker `absent`, deletes the cutover's entry and sets the head back to the import's seq (Y:3197–3202). A restore's abort leaves no seq after `b` and changes nothing else (Y:3202–3203; D24).
- **When it is 2:** only once `switch_to_files` has committed. The operator then reruns the import at the new tip (Y:3202), which is the operator's act to redo, as D10 gives 2 to an act a lane must redo (Y:508–511).
- **When it is not 2:**
  - a push that its read-back cannot confirm is 3, in either branch, and the rerun reads origin back first (Y:3157–3159; S5);
  - a `switch_to_files` call whose timer expired is 69, and the rerun reads the switch (D9, Y:3533).
- **Telling it from `cutover`'s other 2s:** its label refusals and its scratch-schema refusal are 2 too (section 3.2). Stderr's first line names the abort and the moved tip [P].

**Why 2 for both:** each ends with nothing changed in the register: the import swaps nothing in, and the abort undoes the cutover's entry. That is the contract's 2, "a refusal, with nothing written" (§2.7).

### 5. D9's expiries, as codes

D9's table gives each path's expiry (Y:3516–3537) [Y]. For `lanes-index`'s own calls the code is:

| D9 row | Line | The code on expiry |
|---|---|---|
| the guard's direct read | Y:3518 | form 2: 2, too late to verify, but 69, with `.outage`, where an unexpired declaration names this database and the read is cut at the declared cap, `T0` + 3.65 s (contract §3.6, draft 4.19, :776; lane 3's ruling, #165 6807694145); form 4: a log that could not be read (contract §2.7) |
| the mode query, `lanes-index mode` | Y:3521 | the caller's 2 s timer kills it, and the verb runs files mode with a warning (F8, section 6) |
| a writer's snapshot, and `act-begin`'s | Y:3522 | 69, a failed read; the caller makes F3's lookup first where an earlier commit call of the write was in doubt |
| a writer's commit | Y:3523 | 3, in doubt |
| the in-doubt lookup | Y:3524 | a try with no answer, tried again in the window; then 3, the record kept |
| a reader | Y:3526 | 69, a failed read |
| the renderer's reads, and `reconcile` | Y:3527 | 69 (D10); "the render ends; the lag stays" |
| `status` | Y:3528 | 69, with the lag from git where it can read it |
| `rollback`, each call | Y:3529 | 69; at D7's deadline across its tries, 75 |
| `import` | Y:3530 | 69, nothing swapped in |
| a migration | Y:3531 | refused, and rerun; [P] 69, by ops:402 |
| `cutover`'s transaction | Y:3532 | 69, the switch unknown |
| `switch_to_files`, the cutover's abort | Y:3533 | 69 |
| `record_marker` | Y:3534 | 69 |
| `outage declare`'s probe | Y:3535 | on Brett's word, the declaration is written; in the `--rollback` form, 2 |
| `import --single` and `render --revert`, their commit call | Y:3536 | 3, in doubt |
| `import --single` and `render --revert`, their other calls | Y:3537 | 69, or in doubt where an earlier commit call of the same entry was |

The rows for a whole write (Y:3525), the service's connection (Y:3519) and the guard's answer 2 (Y:3520) are `lanes-edit.sh`'s and the service's, so their codes are lane 4's and lane 5's sections.

**One reading, against ops:402.** ops §2.4's first rule gives the operator tools "plus 69 where the database cannot be reached or refuses". `ready`'s own exits are 0, 2 and 64 (ops:458; T119), and a connection that fails is a failed check 4. This file takes `ready`'s own line: its check reports the database, and it never exits 69.

### 6. F8: the ends a caller of `lanes-index mode` reads

F8's table (Y:5707–5716) [Y] is the callers' map, and each caller's arm belongs in its lane's section (lane 4's T409; lane 5's T504). `lanes-index`'s side is:

| `lanes-index mode`'s end | What the caller does (Y) |
|---|---|
| 2: a named file that is missing; a key, line or 0600 check refused; a mode value other than `index` or `primary` | files mode, with a warning |
| 64, from a `lanes-index` too old to have `mode` | files mode, with a warning |
| 127, `lanes-index` or `python3` missing | files mode, with a warning |
| 1, or 128+n: a crash, a signal | files mode, with a warning |
| the caller's 2 s timer | files mode, with a warning |
| 0 with stdout that is not exactly one line, `index` or `primary` | files mode, with a warning |
| any other code, 69, 75 and 3 included | files mode, with a warning |
| 78 | the writer exits 69, writing nothing |

In files mode the marker check then refuses on a SQL estate (D3; Y:5718).

### 7. Other sections

Each lane adds its callers' arms below, in a section of its own, keyed to Y and to the rows above. D10's map is the frame for them: a refused direct write exits 2, 69 tells a lane to stop and tell Brett, and the guard maps 69, 75 and 3 to 2 (Y:508–514).
