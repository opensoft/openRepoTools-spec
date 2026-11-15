# Contract: the pending record, its claims, the done mark and the write id (feature 007)

- Owner: lane openRepoTools-4, under R34. Feature 007, `contracts/pending-record.md`, which tasks.md's T412 lands with the write id and the pending-record library in `lanes-edit.sh`.
- Readers: `lanes-edit.sh`'s write path (T412 to T418); `lanes-index status`, which lists the pending records and resolves each by its id (Y:2501–2502, Y:4853); lane openRepoTools-5's T510, which copies five values out of `start-record`'s record (Y:5431, Y:5515; L5-B1); and lane openRepoTools-2's tests (L2-W3).
- Status: draft 2, 2026-11-09T21:11Z: draft 1 (`83f1013`) with lane 3's rulings on T412's G1 to G13 applied, by lane openRepoTools-4's writer for T412. Lane 4 offers it to lane openRepoTools-3 to fold into the spec PR.
- Citations: `Y:<n>` is a line of the add-sql-register change's `design.md` at spec commit Y, `a6497d00`. "The contract" is `contracts/register-cli.md`, draft 4.10 (`e2b51782`). `FR-<n>` is `spec.md`. `ERR-<n>` and `DEC-<n>` are `research.md` Part 1's errata and decisions, which round B8 records. `R<nn>` is lane 3's ruling of that number (#165 6726556972). "The block" is lane 4's block w11, and "the plan" its `group-B-plan.md`.
- Where Y left a point open, §9 gives lane 3's ruling on it, and §9.1 what is still open after the rulings.

## 0. Basis, and how to read it

| Input | sha256 | Lines | Used for |
|---|---|---|---|
| Y `design.md` | `35ca5179d5e9e47d…` | 6,745 | every [Y] statement |
| The contract, draft 4.10 | `e2b517824510c8d9…` | 963 | §3.2's `--write-id new` and `write_id=`, §3.4's lookup, §4, §7's Q5. Its §3.2, §3.3, §3.4, §4 and §7 are unchanged from draft 4.5 |
| Lane 3's rulings on T412's gaps, #165 6747592174 (18:03Z) and #165 6750702819 (20:30Z) | — | — | §9; ERR-40 to ERR-42, DEC-25 and DEC-26 |
| Lane 4's block w11, `lane4-block-T4xx.md` | `a7c58603e83b36ac…` | 502 | T412 and its detail bullets (`:157–174`) |
| Lane 4's plan w11, `group-B-plan.md` | `801274ded710dfa2…` | 795 | §3.4, §5 (L5-B1's inputs), §11 (open points 36, 38 and 42) |
| `spec.md`, FR-021 (blob `e026aa0b`) | — | — | the requirement |
| Lane 5's block v5, `lane5-tasks-block-v5.md` | `5d427c42b2a2a7bf…` | — | `pid_start_of`'s signature (Q-L5-6, `:516–520`) |

**The marks:**
- **[Y]:** Y fixes it, at the line cited.
- **[C]:** the contract fixes it, at the section cited.
- **[B]:** lane 4's block or plan fixes it, as lane 4's decision or lane 3's ruling it records.
- **[L4]:** Y names the thing and leaves its spelling, and lane 4 fixes the spelling here, as the format's owner (R34). Nothing marked [L4] changes a behaviour.
- **[G]:** Y left it open as T412's G<n>, and lane 3 ruled it (§9). An `ERR-<n>` or `DEC-<n>` names the ruling's own record.

## 1. The files and where they are

- **`<state>`** is `lanes-index`'s state directory [Y, Y:5171 "`<state>` is `lanes-index`'s state directory"]: `${XDG_STATE_HOME:-$HOME/.local/state}/openRepoTools`, as `state_dir()` has it at `code/lanes-index:173–175` [C, §7 Q5]. `lanes-edit.sh`'s `wr_state_dir` is its bash mirror, joined as `os.path.join` joins, and a hygiene test holds the two to one answer [C, §7 Q5].

| Path | What | Mode |
|---|---|---|
| `<state>/register-pending/` | the records and their claims | 0700 [Y, Y:5172] |
| `<state>/register-pending/<d>` | the pending record (§3) | 0600 [Y, Y:5172] |
| `<state>/register-pending/<d>.take.<k>` | a claim (§4); `<k>` is 0, 1, 2 …, with no leading zero [L4] | 0600 [L4] |
| `<state>/register-pending/.<d>.<6 characters>` | a record being written, before its `mv` [L4] | 0600 |
| `<state>/register-pending/.<d>.claim.<6 characters>` | a claim being made, before its `ln` (§4.1) [ERR-42; L4 for the name] | 0600 |
| `<state>/register-done/` | the done marks | 0700 [Y, Y:5180] |
| `<state>/register-done/<write_id>` | a done mark (§6) | 0600 [Y, Y:5180] |
| `<state>/register-writes/<d>/<run>/<k>/` | an attempt directory, which a `call=` line names; its name [C, §3.3] | 0700 [B, plan §2.2; T413] |

- Every directory is made where it is missing, under `umask 077`, and must be a directory this uid owns [L4].
- Every record and done mark is written by a temporary file and `mv` [Y, Y:5172, Y:5180]. The temporary file sits in the same directory, so the `mv` is a rename.
- A name that begins with a dot is a temporary file, and no reader takes it for a record or a claim: a record is read only by its exact name, `<d>`, and a claim only as `<d>.take.<k>` [ERR-42].

## 2. The digest `<d>`

- **What it is** [Y, Y:5171]: the sha256 of
  `lanes-pending/1` NUL `<LANES_LANE>` NUL `<LANES_SESSION>` NUL `<verb>` NUL `<arg1>` NUL … `<argN>`,
  as given on the original command line, written in 64 lower-case hex digits.
  - There is a NUL between fields and none after the last. A verb with no arguments ends at the verb.
  - The UTC and the host facts are left out [Y, Y:5171].
- **Its fields:**
  - `<LANES_LANE>` and `<LANES_SESSION>` are those variables as the run has them, the empty string where unset [Y, Y:5171; G, T412's G2].
  - `<verb>` and its arguments are the words as they arrived (`ORIG_ARGV`), less the global flags the parser takes before the verb: `--no-sweep`, `--sweep` and `--no-github`, which `cmdline` keeps [Y, Y:2476, Y:5171; lane 3 on T412's G3, #165 6750702819]. Those flags are neither the verb nor its arguments (Y:2476: "the sha256 over `LANES_LANE`, `LANES_SESSION`, the verb and its arguments").
- **What it is for:**
  - it names the record's file [Y, Y:5171];
  - it is the journal's `args_sha256`, which every register call of the write passes as `--args-sha256` [Y, Y:5171; C, §4]. `lanes-index` never recomputes it [C, §4].
- **Test vectors,** each checked against an independent `hashlib` computation:

| `LANES_LANE` | `LANES_SESSION` | Words | `<d>` |
|---|---|---|---|
| `openRepoTools-4` | `11111111-2222-3333-4444-555555555555` | `set-row-state` `openRepoTools-4` `LIVE x` (also with `--no-sweep` before them) | `102bc090999bf3f450bd763764c5a715487a96dda68755947f7c5cf3ee01bf09` |
| `openRepoTools-4` | empty | `start-record` | `b6f1e36bb87926b9b0a7a44de3cbb0765d0d606aaf6b4cd4978d660cb9a3ac3b` |
| unset | unset | `log` `NOTED` `lane:openRepoTools-4` and an empty argument | `d9a64d6333d492773fe1960bcd10e8972c8891bd1dffc0ce4387ec4477467393` |

## 3. The pending record, `<state>/register-pending/<d>`

### 3.1 Its format

- One `key=value` a line, each line LF-ended, in this order [Y, Y:5172].
- A value holds no LF and no CR. A write whose value would hold one writes no record [L4].
- A reader passes over a key it does not know [L4, as the contract's §5 asks of `format=1` outputs].

| Key | Value | Source |
|---|---|---|
| `format` | `1` | [Y, Y:5172] |
| `write_id` | the write id, a lower-case UUID (§5) | [Y, Y:5172; C, §4] |
| `generation` | the register generation the id was made against, a lower-case UUID | [Y, Y:5172, Y:633] |
| `database` | the database's label, written empty in format 1: no source reaches `lanes-edit.sh`, and no reader compares or prints it, since `generation` tells databases apart (Y:632–634). The key stays, so a later source can fill it with no format change | [Y, Y:5172]; [G, ERR-40, T412's G1] |
| `lane` | `<LANES_LANE>` as the digest took it | [Y, Y:5172] |
| `session` | `<LANES_SESSION>` as the digest took it | [Y, Y:5172] |
| `verb` | the verb as the digest took it | [Y, Y:5172] |
| `args_sha256` | `<d>`, which must equal the file's own name | [Y, Y:5171–5172] |
| `cmdline` | `lanes-edit.sh`, then every word of the original command line, global flags included, each quoted by bash's `printf '%q'`, so that bash can `eval` it. The quoting follows the locale, so read it with bash and never by bytes | [Y, Y:5172 "`cmdline` (shell-quoted)"]; [L4] for the quoting |
| `created_utc` | when the record was first written, on this host's clock | [Y, Y:5172] |
| `updated_utc` | when it was last written, on this host's clock (§3.3) | [Y, Y:5172]; [B] for the clock |
| `pid`, `pid_start`, `host`, `container` | copies of the owner's claim (§4.1), kept for `status` and the texts. Liveness is never read from them | [Y, Y:5172, Y:5185] |
| `attempts` | the count of attempts the write has made, a whole number | [Y, Y:5172] |
| `call` | zero or more lines, one per commit call that may have committed (§3.2) | [Y, Y:5172–5173] |
| `tail` | absent; then `sent <s> <utc>`; then `done <s> <rc> <utc>` | [Y, Y:5179, Y:5293–5294]; T418 writes it |

- **UTC stamps** are `YYYY-MM-DDTHH:MM:SSZ` [L4, this file's existing form].
- **The record is whole** where: `format=1`; `write_id` and `generation` are lower-case UUIDs; `args_sha256` equals the file's name; `created_utc` and `updated_utc` are UTC stamps; and every `call=` line has §3.2's five fields [L4]. A record that is not whole is refused (§5.3; T412's G8).

### 3.2 The `call=` lines

- **The form** [Y, Y:5173]: `call=<base> <clock> <not_after> <files> <dir>`.

| Field | What | Its source |
|---|---|---|
| `<base>` | the call's base seq, `n` | the snapshot's `n=` [C, §3.2] |
| `<clock>` | the snapshot's database clock, which the verb's lines carry (D13), in epoch seconds | the snapshot's `clock=` [C, §3.2] |
| `<not_after>` | the call's `not_after`, in epoch seconds | the snapshot's `not_after=` [C, §3.2] |
| `<files>` | the sha256 of the sorted `files` list the call sends: path, present, sha256 | §3.2's "one digest rule" [C, §3.2] |
| `<dir>` | the call's attempt directory | [Y, Y:5173; C, §3.3] |

- `<dir>` is the last field, and it may hold spaces. A reader splits off the first four fields at single spaces and takes the rest as `<dir>` [L4].
- **When a line is written** [Y, Y:5174]: by a temporary file and `mv`, before its call is sent. The caller sends the call only once the write has succeeded [L4].
- **When a line is dropped** [Y, Y:5175]: only where an answer shows the call did not commit: a lost turn, a refusal, or LR008. The caller decides which answers those are (T415).
- **What a line means** [Y, Y:5176]: exactly a call that may have committed. A write is in doubt while its record holds any `call=` line, and no other key says so [Y, Y:5172].
- **Its directory** keeps `snapshot`, `files` and `preimages` while the line stands [Y, Y:5177].

### 3.3 `updated_utc`

- It is rewritten on every write of the record, from this host's clock, `date -u` [B, T412's `updated_utc` bullet; Y:2486–2487 "15 minutes from its last update"].
  - That includes every `call=` line, so it is rewritten before each call is sent.
  - It is never the snapshot's clock, although T413's `utc_now` answers the snapshot's clock in the attempt's child.
- **Why it is the host's.** A takeover whose own snapshot failed passes `journal` the newest `call=` line's clock as `--clock`, and the seconds since `updated_utc`, by this host's clock, as `--clock-age` [C, §3.4; open point 36, taken under R26; T417]. `updated_utc` is no earlier than that line's write, so the estimate can only lag the database's clock, and a lag only lengthens the wait [C, §3.4].
- It also starts the record's age (§7) and the window of another host's claim (§4.2).

### 3.4 Who writes the record, and when

- **Only its owner** writes it, the run in the highest claim (§4) [Y, Y:5190]. A run whose own claim is no longer the highest writes nothing [L4, from Y:5185's definition of the owner].
- **First written** after the first snapshot, which supplies the generation, and before the first commit call [Y, Y:5192]. Its claim was taken before that snapshot [B, T412's "The id" bullet].
  - Where that snapshot found the write in the register (`found=`), no record is written here, since that is the tail's case (T418) [L4; Y:5344 "A run that finds its id with no pending record of it runs no tail"].
- **Taken over** by a plain rerun within the age, with its id, its `call=` lines and its doubt [Y, Y:5206], and by a run under `LANES_WRITE_ID` that names its id (§5). The new owner rewrites `pid`, `pid_start`, `host` and `container` from its own claim [Y, Y:5185], except in a record holding `tail`, which is never rewritten (Y:5342).
- **Replaced** under a new id only:
  - by a plain rerun, where a record of this generation is past its age (§7) [Y, Y:5207];
  - by `LANES_WRITE_ID=new`, where the record is of another generation [Y, Y:5213, Y:5215].
- **Removed,** with its claims, once the outcome is known [Y, Y:5193–5198]:
  - on 0, after the tail is done or named as owed, its `tail` value first going into the done mark (§6);
  - on 2, 69, 75 or the verb's own code, where it holds no `call=` line;
  - where it holds one, only after F3's lookup has settled the write.
  - The `call=` lines decide, not the code [Y, Y:5198].
  - The owner removes the record, then the claims numbered up to its own. Y:5193's "with its claim files" means the owner's claim and those numbered below it. A claim numbered above the owner's is a later run's, which judged the owner dead, and is left: removing it would make that run fail at every record write, and would let a third run take `<d>.take.0` beside it. A claim left behind never blocks, since a dead one is passed by `<d>.take.<k+1>` (Y:5187) [G, DEC-26, T412's G11].
- **Kept** on 3, with its `call=` lines and their directories [Y, Y:5199].
- **No record is ever purged by age** [B, T412's "The age" bullet; Y:5183].

## 4. The claims, `<d>.take.<k>`

### 4.1 A claim's format

- Five `key=value` lines, LF-ended, in this order. Y fixes the fields [Y, Y:5184: "the run's pid, start time, host, container and UTC"], and the names are [L4]:

| Key | Value |
|---|---|
| `pid` | the run's pid, `$$` |
| `pid_start` | the run's start token, `pid_start_of $$`'s stdout; empty where that is not exit 0 |
| `host` | the host, as the writer stamps its lines (`lanes_host_pair`), through `lanes_binding_value_ok`'s fence; empty where the value fails it |
| `container` | the container, as the writer stamps it (`lanes_container_pair`, `none` outside one), through the same fence |
| `utc` | when the claim was made, on this host's clock |

- **Made whole** [Y, Y:5184, as ERR-42 reads it; G, T412's G10]:
  - its body is written to a temporary file in the same directory, `.<d>.claim.<6 characters>`, and read back;
  - the file is then linked to `<d>.take.<k>` with `ln`. The link fails where the name exists, as noclobber's create does, so only one run's create of a name succeeds [Y, Y:5188], and no run ever meets the name with less than the whole body;
  - the temporary file is removed whether the link succeeds or fails, and it is 0600, as the record is;
  - a body that cannot be written whole, or a link that fails with the name still free, is exit 1, with nothing sent, never a wait on the run's own claim [ERR-42].
- The record's `pid`, `pid_start`, `host` and `container` are copies of its owner's claim under the same names [Y, Y:5172].

### 4.2 The chain

- **Every run takes its claim before its first snapshot:** a new write [B, T412's "The id" bullet], a plain rerun, and a run under `LANES_WRITE_ID`, since two live runs must never share an id [Y, Y:5219].
- **The owner** is the run in the highest-numbered claim [Y, Y:5185]:
  - with no claim, the run creates `<d>.take.0` [Y, Y:5186];
  - where the highest claim's owner is dead or its pid reused, it creates `<d>.take.<k+1>` [Y, Y:5187];
  - where its owner is live, the run waits while it lives, or until the claim's cap runs out where it is live only for the cap (§4.3), reading the claims again each second [L4 for the second], up to D7's deadline from the run's own start [Y, Y:5216];
  - a create that finds the name taken lists the claims again [Y, Y:5188];
  - only the run whose create succeeds owns the record [Y, Y:5188].
- **A claim with no record beside it** is a record being made, and its owner is waited for the same way [Y, Y:5189].
- So two runs that read one dead pid together cannot both take the record over [Y, Y:5191].
- **At the deadline,** with the owner still live, the run exits 3 and sends nothing. That is T417's, with the exit-3 rule [B, T412's "Not here" bullet; Y:5218]. A claim with no record beside it gets open point 42's text, which prints the claim's fields [B; plan §11.4]. A claim live only for the seam's cap gets a form that names the claim file and the UTC from which a run may take it [G, DEC-25, T412's G10]; `lanes-edit.sh` leaves T417 the claim's kind (§4.3's last column) and that UTC.

### 4.3 Is a claim's owner live?

| The claim | Live? | Its kind | Source |
|---|---|---|---|
| it names no pid, or cannot be read | live for the seam's cap, `2 × (D + 30 s)`, 120 s at D7's 30 s, from the later of its file's mtime and the record's `updated_utc`; then dead | `no-pid` | [G, DEC-25, T412's G10] |
| it has gone | dead: only its owner removes it | — | [L4] |
| another host or container, or either side's host or container empty | live for the seam's cap, from the later of its own `utc` and the record's `updated_utc`; then dead | `other-host` | [Y, Y:5220]; the cap and `LANES_REGISTER_SEAM_CAP_S`, its variable for tests, are Y:5001's [G, T412's G13] |
| this host and container, the pid this run's own, the claim not this run's | dead: the pid is this run's now | — | [L4, a consequence of Y:5185] |
| this host and container, `pid_start_of <pid>` exit 0, its token equal to `pid_start` | live | `pid` | [Y, Y:5185; Q-L5-6] |
| the same, exit 0, another token | dead: the pid was reused | — | [Y, Y:5185] |
| the same, exit 0, `pid_start` empty, so a reused pid cannot be told | live for the seam's cap, from the later of its `utc` and the record's `updated_utc`; then dead | `no-start` | [G, DEC-25, T412's G10] |
| the same, exit 1 | dead | — | [Q-L5-6] |
| the same, exit 2: the start cannot be read | live, with no bound | `start-unread` | [R49, unchanged; Q-L5-6] |
| the same, any other exit, such as 127 where `pid_start_of` is not yet defined | live, with no bound | `untested` | [L4, R49's reason: "a wrong 'dead' never lets a claim be stolen"]; see §9.1 |

- **The seam's cap** is `2 × (D + 30 s)`, 120 s at D7's 30 s, and `LANES_REGISTER_SEAM_CAP_S` sets it for tests [Y, Y:5001]. A claim from another host gets it "since its pid cannot be tested here" [Y, Y:5220], and DEC-25 gives it, for the same reason, to the two claims of this host that cannot be tested either.
- **Where neither stamp of a capped claim can be read,** the cap runs from the claim file's mtime, and where even that cannot be read the claim is live [L4]; see §9.1.
- **The order of the rows** is the order of the tests: a claim that cannot be read is judged before its host, and its host before its pid.

- **`pid_start_of <pid>`** is lane 5's helper [Y, Y:4865, Y:5434; T502; Q-L5-6]. Exit 0 means live, with the start token on stdout. Exit 1 means not live. Exit 2 means the start cannot be read. The token is field 22 of `/proc/<pid>/stat` on Linux and `ps -o lstart=` on macOS, compared as a string, on the same host and container only.
- **Liveness is never read from the record's `pid`** [Y, Y:5185].

### 4.4 A run's own claim, removed

- A new write whose first snapshot ended 69 has no id and no record, so it removes its own claim [FR-021; open point 38, taken under R26; T415].
- So does a refusal of §5.3, which sends nothing [L4].
- Every other claim stays, and so does the record.

## 5. The write id

### 5.1 Where it comes from

- **The order** [Y, Y:2468–2473; C, §4]:
  1. `LANES_WRITE_ID`, where the caller set it to an id;
  2. a new id that `lanes-index` mints, where the caller set it to `new`;
  3. else the id in the record for this command line;
  4. else a new id, as for `new`.
- **The mint** is the first snapshot's `--write-id new`, and the id is its `write_id=` [C, §3.2, §4; B, review M13].
- **The shapes:** an id and a generation are lower-case UUIDs, as `lanes-index` prints them [C, §4]. A `LANES_WRITE_ID` that is neither a lower-case UUID nor `new`, and a `LANES_WRITE_GENERATION` that is not one, are refused with 2 before any call [L4].

### 5.2 The first snapshot's identity, after the claim

| `LANES_WRITE_ID` | The record at `<d>` | The first snapshot passes | Then |
|---|---|---|---|
| unset | none | `--write-id new` with the lane, verb and `<d>` | the minted id; the record written [Y, Y:5192] |
| unset | within its age | the record's id and generation, all five flags | the record taken over; the run says so on stderr [Y, Y:2481, Y:5206]. A 2 with LR007 `generation` here is a record of another generation (§7) |
| unset | past its age | `--write-id new` | the record's generation against `generation=`: equal, the run warns, prints the lookup and writes anew under the minted id; not equal, refused with 2 (§7) [Y, Y:5207] |
| `new` | none | `--write-id new` | the minted id; the record written |
| `new` | of another generation | `--write-id new` | the record replaced under the minted id [Y, Y:5213, Y:5215] |
| `new` | of this generation | `--write-id new` | refused with 2 [G, T412's G5] |
| an id | holds that id | the five flags: `LANES_WRITE_GENERATION`, else the record's generation [Y, Y:4986] | the record taken over, at any age |
| an id, with `LANES_WRITE_GENERATION` | holds that id under another generation | — | refused with 2 [G, T412's G7] |
| an id, with `LANES_WRITE_GENERATION` | none | the five flags | the record written under that id |
| an id, with `LANES_WRITE_GENERATION` | holds another id | — | refused with 2 [G, T412's G6] |
| an id, without `LANES_WRITE_GENERATION` | none, or one that holds another id | — | refused with 2, naming the printed rerun's form (§8) [B, T412; the contract's O4, ratified by R08] |

- **With no generation, nothing is sent.** So `lanes-index`'s 64 for an id with no `--generation` is never reached from `lanes-edit.sh` [B; C, §3.2 and §8's O4].
- **After the snapshot,** the echoed `write_id=` and `generation=` must be the run's own, and a `format=` other than 1 is a failed read, 69 [C, §2.8].

### 5.3 The refusals, each with 2 and nothing sent

- a `LANES_WRITE_ID` or `LANES_WRITE_GENERATION` of another shape (§5.1) [L4];
- `LANES_WRITE_ID` with no generation that no record here holds [B; R08];
- a record of another generation, at any age, until `LANES_WRITE_ID=new` (§7) [Y, Y:5208–5215];
- a record that is not whole (§3.1), since whether a write of it is in doubt is not known [G, T412's G8];
- T412's G5, G6 and G7, which Y does not settle, refused rather than guessed [G].

A host with neither `sha256sum` nor `shasum` cannot make the digest, and is refused with 1, the exit table's code for the environment [L4]. So is a `register-pending/` directory that cannot be made, and a claim that cannot be written whole [ERR-42].

## 6. The done mark, `<state>/register-done/<write_id>`

- **When it is written:** a run that removes a record on 0 first writes the mark [Y, Y:5181], by a temporary file and `mv`, mode 0600 in 0700 [Y, Y:5180]. T418 writes it.
- **Its format:** `key=value` lines, LF-ended. Y fixes the fields [Y, Y:5181], and the names are [L4]:

| Key | Value |
|---|---|
| `write_id` | the record's write id, which is also the file's name |
| `seq` | the seq the write committed at |
| `tail` | the record's `tail` value: `done <s> <rc> <utc>` or `sent <s> <utc>`, or `owed <utc>` where the tail was named as owed [Y, Y:5181, Y:5340] |
| `pid`, `host`, `container` | the record's, as in its claim |
| `utc` | when the mark was written, on this host's clock |

- **It is keyed by write id,** not by digest, so a plain rerun never meets it, and a new identical intent still writes anew [Y, Y:5182].
- **It is kept for the record's maximum age** (§7) [Y, Y:5183]. Any run that writes a record or a mark first removes the marks older than that, by their `utc` [Y, Y:5183; L4 for the key].
  - A name that is not a lower-case UUID, and a mark whose `utc` cannot be read, are left [L4; G, T412's G12].
- **A run that finds `tail` sent or done,** in the record or in the mark, runs no tail [Y, Y:5296].

## 7. The age

- **`LANES_REGISTER_PENDING_MAX_AGE_S`,** 15 minutes, 900 s, for a record and a done mark [Y, Y:5183, Y:5207]. The variable is for tests [Y, Y:5207].
- **The age runs from `updated_utc`** [Y, Y:5207], on this host's clock (§3.3).
- **A record of this register's generation, past its age,** is not reused: the run warns, prints the lookup, and writes anew under a record of its own [Y, Y:5207].
  - The lookup is the contract's `lanes-index journal --write-id <id> --not-after <n> --generation <g> --lane <lane> --verb <verb> --args-sha256 <d>`, with `<n>` the largest `not_after` among its `call=` lines [C, §3.4].
  - Where it holds no `call=` line, none of its commit calls can have committed, and the warning says so instead [Y, Y:5176].
  - "Of its own" is the same path, `<d>`: the record is replaced (§3.4).
- **A record of another generation has no age** [Y, Y:5207]. It is refused at any age, until `LANES_WRITE_ID=new` [Y, Y:2487–2491, Y:5208–5215]. The record is left as it is, and `lanes-index status` lists it meanwhile [Y, Y:5215].
  - The run's first snapshot says whether the register is the record's generation [Y, Y:5207, as ERR-41 reads it]: by its `generation=`, or by its 2 with LR007 `generation` (§5.2), which names no generation.
- **Only done marks are removed by age** [Y, Y:5183; B]. No record is purged.

## 8. The texts

- **The printed rerun** [Y, Y:5200]:
  `LANES_WRITE_ID=<id> LANES_WRITE_GENERATION=<generation> LANES_LANE=<lane> LANES_SESSION=<uuid> lanes-edit.sh <the same arguments>`.
  - `<lane>` and `<uuid>` are the values the digest took, each quoted with `printf '%q'`, so the rerun's digest, and so its `args_sha256`, are the run's own [L4; G, T412's G2].
  - `<the same arguments>` is the record's `cmdline` less its first word.
- **The plain rerun's note** [Y, Y:2481, Y:5206 "reuses the id, says so on stderr"]: "reusing write `<id>` of register generation `<g>`, from the pending record of this command made at `<created_utc>` and last updated at `<updated_utc>`; this run takes it over, with its `<k>` call line(s)" [L4 for the words].
- **The age's warning** [Y, Y:5207]: "WARNING: the pending record of this command, write `<id>` made at `<created_utc>` against register generation `<g>`, was last updated at `<updated_utc>`, more than `<max>` s ago, so this run does not reuse its id: it writes anew, as write `<new id>`, and replaces that record." It then gives the lookup (§7) [L4 for the words].
- **Another generation's refusal** is Y's text, word for word [Y, Y:5209–5213], with `<g′>` as ERR-41 reads it (below): "a pending record of this command, write `<id>` made at `<created_utc>` against register generation `<g>`, is here, and this register is generation `<g′>`. That write may already be in it: an import carries every write the mirror held (D22), and a restored database holds every write up to its backup. Nothing was sent. Look for its effect in the register as it stands: `lanes-edit.sh history <lane> --since <created_utc>` for a log line, or `lanes-edit.sh register-row <lane>` for a row. If it is there, its after-COMMIT steps may be owed: `<owed>`. Do by hand any that did not run. If it is not there, run the same command with `LANES_WRITE_ID=new`, which mints a new id and replaces this record."
  - **The `tail` forms** [Y, Y:5214]. Where the record's `tail` is `sent`, the sentence reads "its after-COMMIT steps may have run at `<utc>`: `<owed>`". Where it is `done`, it reads "its after-COMMIT steps ran at `<utc>`." [L4 for the words].
  - **`<owed>`** is the record's verb's row of F14's table, with the command that does each step by hand [Y, Y:5214]. Those texts are T418's. Until T418 gives them, the text reads "those of `lanes-edit.sh <verb>`" [G, T412's G9].
  - **The rerun.** The text ends with the `LANES_WRITE_ID=new` command, filled in [L4].
  - **`<g′>`.** Where the first snapshot refused the record's generation (LR007 `generation`), it prints no generation [C, §3.2], so the text reads "this register is another generation" [G, ERR-41, T412's G4]. T415 maps the snapshot's 2 carrying `DETAIL:  generation` to this refusal, with no `<g′>`. Past the record's age the snapshot answered 0, and the text names `<g′>`.
- **The refusal of an id with no generation** [B; R08]: "`LANES_WRITE_ID=<id>` is set and `LANES_WRITE_GENERATION` is not, and no pending record of this command here holds write `<id>`, so the register generation it was made against is not known. Nothing was sent. Run the rerun as it was printed, with both:" and then the printed rerun with `<generation>` as written [L4 for the words].
- **The 3s** are T417's: for a live owner at the deadline [Y, Y:5218], for a claim with no record beside it [B; plan §11.4], and for a claim live only for the seam's cap, naming the claim file and the UTC from which a run may take it [G, DEC-25, T412's G10].

## 9. T412's gaps, and lane 3's rulings on them

Lane 3 ruled T412's G2, G5 to G9, G12 and G13 at 18:03Z (#165 6747592174), and T412's G1, G3, G4, G10 and G11 at 20:30Z (#165 6750702819). Round B8 records ERR-40 to ERR-42, DEC-25 and DEC-26 in `research.md` Part 1. These are lane 3's rulings; they give no word of Brett's. Every row is closed.

| # | The point | The ruling | What `lanes-edit.sh` does |
|---|---|---|---|
| T412's G1 | **`database`** is one of Y:5172's keys, and nothing supplies it. `lanes-edit.sh` reads no configuration [C, §2.5; R05], and the snapshot's stdout has no label [C, §3.2] | ERR-40: written empty in format 1. No reader compares or prints it; `generation` tells databases apart (Y:632–634). The key stays | writes `database=` empty (§3.1) |
| T412's G2 | **The session and the lane in the digest.** Y:5171 digests `LANES_SESSION` and `LANES_LANE` as variables, and Y:5200's rerun prints `LANES_SESSION=<uuid>`. Where the run had no `LANES_SESSION`, its event lines take the row's uuid (`session_for`), and a rerun printing that uuid would compute another `args_sha256` | (a), at 18:03Z: the rerun prints exactly the value the digest took, empty where the run had none. Round B6 records Y:5200's erratum | the digest takes the variables as given, and the rerun prints those same values (§2, §8) |
| T412's G3 | **The global flags.** Y:5171 says "as given on the original command line", and `ORIG_ARGV` holds `--no-sweep`, `--sweep` and `--no-github` where they were typed | as built, with no erratum: Y:5171's formula has no field for a word before the verb, and Y:2476 says "the verb and its arguments". `LANES_NO_GITHUB=1` does what `--no-github` does, and no reading digests it. Round B8 rewords T412's line in `tasks.md` to match | the digest leaves out the leading global flags; `cmdline` and the rerun keep them (§2) |
| T412's G4 | **`<g′>` in another generation's refusal.** Within the record's age, the refusal is the reuse snapshot's 2 with LR007 `generation`, and on a 2 §3.2 prints no `generation=` | ERR-41: Y:5209's `<g′>` reads "this register is another generation" there, and Y:5207's "says which generation the register is" reads "says whether it is the record's". Nothing in the remedy needs `<g′>` | the text says "another generation"; past the age it names `<g′>` (§8) |
| T412's G5 | **`LANES_WRITE_ID=new` over a record of this generation** (Y:2470–2472, Y:5215) | as built, at 18:03Z | refused with 2, naming the record and its lookup (§5.2) |
| T412's G6 | **`LANES_WRITE_ID=<id>` with its generation, where `<d>`'s record holds another id** (Y:5215) | as built, at 18:03Z | refused with 2, naming the record and its lookup |
| T412's G7 | **`LANES_WRITE_ID=<id>`, where `<d>`'s record holds that id under another generation than `LANES_WRITE_GENERATION`** | as built, at 18:03Z | refused with 2 |
| T412's G8 | **A record that is not whole** (§3.1) | as built, at 18:03Z | refused with 2, naming its path |
| T412's G9 | **`<owed>`** is T418's | as built, at 18:03Z | the verb's name stands in until T418 (§8) |
| T412's G10 | **A claim with no pid.** Made with noclobber (Y:5184), a claim whose maker was killed between the create and its write, or whose write failed on a full disk, had no pid and counted as live for ever: every later run of the command waited to its deadline and stopped, until a person deleted the file | ERR-42: a claim is made whole, by a temporary file linked with `ln`, and a body that cannot be written is exit 1. DEC-25: a claim whose owner cannot be tested here is live for the seam's cap, then dead: one with no pid or one that cannot be read, from its mtime; one whose pid lives with no `pid_start`, from its `utc`; each with the record's `updated_utc` where that is later. R49 is unchanged. T417's 3 gains a form for such a claim | §4.1, §4.3 |
| T412's G11 | **Claims numbered above the owner's** at removal (Y:5193 "with its claim files") | DEC-26: the owner's claim and those below it; a higher claim is a later run's | left (§3.4) |
| T412's G12 | **The done mark's `utc` key,** which the age is read from | as built, at 18:03Z | a mark whose `utc` cannot be read is kept (§6) |
| T412's G13 | **Another host's 120 s** is "the seam's cap" (Y:5220), whose variable for tests is `LANES_REGISTER_SEAM_CAP_S` (Y:5001) | as built, at 18:03Z | the window reads `LANES_REGISTER_SEAM_CAP_S`, default 120 (§4.3) |

### 9.1 What is still open after the rulings

- **A capped claim with no readable stamp.** Y:5220 and DEC-25 time a claim from another host or container, and one whose pid lives with no `pid_start`, from the later of its `utc` and the record's `updated_utc`. Where neither can be read, `lanes-edit.sh` starts the cap at the claim file's mtime, as DEC-25 does for a claim with no pid, so that a claim live only for the cap is always bounded [L4]. Draft 1 counted such a claim from another host as live with no bound.
- **An answer of `pid_start_of` other than 0, 1 or 2,** such as 127 while `pid_start_of` is not yet defined, counts as live with no bound [L4, R49's reason]. DEC-25 names two cases, and neither is this one. Lane 5's helper answers only 0, 1 and 2 (#165 6747648353), so the row matters only before T502 lands.
- **A state directory on a file system that refuses hard links.** ERR-42's `ln` fails there with the name still free, so every write exits 1 and sends nothing.
- **Two live runs on one record,** which lane 3 asked to be named with DEC-25. Where a live owner outlasts the cap, a second run takes `<d>.take.<k+1>` and owns the record. That happens in DEC-25's `no-start` case, for an owner holding its claim past the seam's cap, in a tail that waits or on a host that was suspended; in the `no-pid` case, only for a whole claim that readers cannot read; and in Y:5220's own case, for another host.
  - **The commit is still made at most once.** Both runs act under one write id. A second commit of it is LR006 `already`, and a call past its `not_after` is LR008 [C, §4].
  - **The record can be written by both.** The earlier run's later record writes fail, since only the owner writes (§3.4), so it adds no `call=` line and sends no further call. But between that run's ownership check and its `mv`, each run can replace the other's record, dropping a `call=` line or a `tail` value.
  - **The tail** must check ownership before `committed` is sent, or both runs could send it. That is T418's.
