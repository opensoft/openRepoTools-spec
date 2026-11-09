# Contract: `lanes-index.conf` and the `register-outage` declaration (feature 007)

- **Owner:** lane 1 (`[L1]`), task T102. Lane 1's T103 builds the mapping (section 3.3), T104 the keys and refusals (sections 2 and 4), and T121 the declaration's writer (section 6). Lane 5's T507 reads the declaration, and its T512 starts `serve` from the same file.
- **Keyed to commit Y, `a6497d00`.** `Y:<n>` is a line of Y's `design.md`, and `ops:<n>` a line of Y's `operations.md` (the contract writes `Y-ops:<n>`). `code/lanes-index:<n>` is the code leg at the pin Y cites.
- **The contract** is [register-cli.md](register-cli.md), cited by section; its §2.5 is the keys each register call reads. This file was written against its draft 4.5, and checked against draft 4.6 (sha256 `028937e6…`), which keeps every line number and changes only lines of §3.6; a cite "draft 4.6, :n" is a line of draft 4.6. The copy in this branch is draft 4.4, which differs from 4.5 only in §2.5's and §9's wording of the endpoint. Where this file and the contract differ, the contract holds. The exit codes are [exit-codes.md](exit-codes.md)'s.
- **By lane openRepoTools-1, 2026-11-09.**
- **No real value is written here:** every host, address, login and file is a placeholder in angle brackets, and the endpoint is `<endpoint>`.

**The marks** are exit-codes.md's: [Y] Y fixes it, at the line cited; [C] the contract fixes it; [T] lane 1's task text fixes it (block v4.5, as tasks.md carries it); [P] Y leaves it open, and this is lane 1's proposal; [X: hosting] marks text that X settled and Y keeps (plan.md's marks).

---

## 1. The file

- **Its path** [Y]: `${XDG_CONFIG_HOME:-$HOME/.config}/openRepoTools/lanes-index.conf`, or the file `$LANES_INDEX_CONFIG` names (Y:2323). `LANES_INDEX_CONFIG` counts only when it is set and not empty (Y:5705; `code/lanes-index:161–170`).
  - **No file at the default path** is an unconfigured workstation: the index half takes SQLite at its default path, and `lanes-edit.sh` asks no mode question (D3, Y:368–372).
  - **A named file that is not there** is refused (section 4, R2).
- **Its form,** as today (`load_config`, `code/lanes-index:248`):
  - one `key=value` per line, split at the first `=`, each side trimmed;
  - blank lines, and lines that open with `#`, are skipped;
  - a value in matching single or double quotes loses them;
  - a value that opens with `~/` is taken from the home.
- **One parser** [Y]: `lanes-index` is the only parser of the file (FR-001; Y:2323). `lanes-edit.sh` reads no key: it only tests that the file exists, and takes `c`, `N_a` and `w_s` from `lanes-index`'s outputs (D3, Y:368–372; R05; contract §2.5).
- **Where it lives** [Y]: on benches, on the host's read-only mount (Q23(a); Y:2323; ops:208), beside the two 0600 password files and the root file (Y:2324).
- **Who writes it** [Y]: Brett, on each engineer's workstation, human or AI host, with the lines `bootstrap` and `login add` print (ops:208–209; ops:424; ops:482). `set-mode` rewrites only its `mode=` line, by temp file and rename, keeping 0600 (ops:465).
- **One engineer's file** [Y]: each workstation's file names only its own engineer's two logins and their two password files. No workstation holds another engineer's line (Y:2326; ops:209).
- **No URL or password enters an environment a session inherits** (Y:2323) [Y]. The `PG*` variables of section 3.3 are set only in each `psql` child's environment.

## 2. The keys

**Old** keys are #160's, read by its index half today (`code/lanes-index:62–68`). **New** keys are 007's (FR-001; the phase plan's `load_config` row, Y:4849; T104).

| Key | Old or new | Value | Default | Read by | Source |
|---|---|---|---|---|---|
| `store=` | old | `sqlite` or `postgres` | `postgres` where `url=` is set, else `sqlite` | the index half | `code/lanes-index:62–68` |
| `sqlite=` | old | the SQLite file's path, outside every git work tree | under the state directory | the index half | `code/lanes-index:62–68` |
| `schema=` | old | the index half's schema, a plain lower-case SQL identifier | `lanes_index` | the index half | `code/lanes-index:62–68` |
| `url=` | old | the indexer's libpq URL, the login `lanes_indexer` | — | `sync` and `reconcile` of the index half | Y:2318; Y:2425; Y:3461 |
| `passfile=` | old | the indexer's 0600 password file | — | the same | Y:2318; Y:2425 |
| `reader_url=` | old, now also the register's READER | where `register_url=` is set, this engineer's READER URL, the login `lanes_r_<engineer>` (section 3.1) | — | `register read` and `journal --last` (contract §2.5); `status`, `ready` and the service; #160's `status` and `export`. Never `register classify` | Y:3460; Y:2317; Y:2326; contract §2.5 |
| `reader_passfile=` | old | a 0600 file holding only that login's line | — | the same | Y:2317; Y:2326 |
| `mode=` | new | `index` or `primary` | `index` | `lanes-index mode`, and every command, to refuse Q6(c) | Y:4849; Y:2421; contract §2.5 |
| `register_url=` | new | this engineer's REGISTER-WRITER URL, the login `lanes_w_<engineer>` at `<endpoint>` (section 3.1) | — | `register snapshot`, `register commit`, `journal --write-id`, `journal --since` and `render` (contract §2.5); `import`, `cutover`, `rollback` and the two ways out; `outage declare`'s probe | Y:3459; Y:2317; Y:2326; D9 (Y:3535); contract §2.5 |
| `register_passfile=` | new | a 0600 file holding only that login's line | — | the same | Y:3459; Y:2326 |
| `register_label=` | new | the label of the database `register_url=` reaches, as that database's `bootstrap --label` gave it; set in the same edit as `register_url=` | — | `cutover`, its one source for the label it sends, in every form; `ready`'s check 10; [P] `outage declare`, for the declaration's `database=` (section 6.3) | Y:2422; Y:2323; ops:379; ops:453 |
| `register_schema=` | new | the register's schema, a plain lower-case SQL identifier; a rehearsal names its scratch schema here | `lanes_register` | every register command | Y:2323; Y:2424; T104 |
| `render_key=` | new, under Q21(b)(B) | the path of the renderer identity's 0600 deploy-key file. It holds no secret itself | — | the pushes of the renderer, `cutover` and `rollback`, in the push's own environment only | D23 (Y:647–652); Y:2423; ops:208 |
| `register_addresses=` | new | `N_a`: the number of addresses the endpoint resolves to, a whole number, measured when the workstation joins | 1 | every register command, for its timers; `ready`'s check 13 | Y:5877; Y:5912; ops:456 |
| `register_rtt_ms=` | new | the p90 of act 3's `select 1` round trips in one session, in whole milliseconds, measured when the workstation joins | none, read as 0 | the service's per-request timer; `register read --prefetch`, for `w_s` | Y:4005–4008; contract §2.5 |

- **`c` has no key of its own** [Y]: it is the `connect_timeout=` query parameter of each URL, default 2 (Y:5877; section 5).
- **`reader_passfile=` and `reader_url=` keep #160's names** [Y]. Where `register_url=` is set, they name the engineer's READER login, which holds SELECT on both halves (Y:3460).

## 3. The URLs

### 3.1 Their form [Y] [X: hosting]

Y's form (Y:2326):

```text
register_url=postgresql://lanes_w_<engineer>@<endpoint>:5432/<database>?sslmode=verify-full&sslrootcert=<root file>
reader_url=postgresql://lanes_r_<engineer>@<endpoint>:5432/<database>?sslmode=verify-full&sslrootcert=<root file>
```

- **`<endpoint>`** is the register's DNS name, for the public `LoadBalancer` in front of the Cluster's primary, at port 5432 (Y:2324; contract §2.5, §9). Every register connection reaches the primary directly: never through a pooler, and never through the `-ro` or `-r` service, which can reach a replica (Y:2324; Y:1575).
- **`sslmode=verify-full` and `sslrootcert=`** [Y]: the root file is the root of the issuer that signs the server's certificate, a file beside the password files on the read-only mount. It is never `system`, which holds no private root (Y:2324). libpq's default, `prefer`, verifies nothing (Y:2324).
- **`connect_timeout=<c>`** joins each URL's query where the workstation's `c`, measured when it joins, is above 2; with none, `c` is 2 (Y:5877; contract §2.5).
- **`target_session_attrs=`,** where `reader_url=` carries one, is `primary`, never `read-write`, which refuses every READER connection (Y:2324) [Y].
- **No password in any URL** (section 4, R5). `register_passfile=` and `reader_passfile=` each name a 0600 file owned by that engineer's account, holding only its own login's line; libpq ignores a password file that group or others can read (Y:2326).

### 3.2 An engineer's whole file [P]

Assembled from Y:2326, ops:208 and the keys above; every value is a placeholder.

```text
mode=index
register_url=postgresql://lanes_w_<engineer>@<endpoint>:5432/<database>?sslmode=verify-full&sslrootcert=<root file>
register_passfile=<writer password file>
reader_url=postgresql://lanes_r_<engineer>@<endpoint>:5432/<database>?sslmode=verify-full&sslrootcert=<root file>
reader_passfile=<reader password file>
register_label=<label>
register_addresses=1
register_rtt_ms=<p90 in ms>
render_key=<deploy key file>
```

- `mode=index` holds until the host's flip, which `set-mode primary` makes after the cutover (ops:208; Q20(B)).
- `render_key=` is set only under Q21(b)(B) (ops:208).
- `register_addresses=1` is the install's one address (`ready`'s check 13 expects it; FR-055).
- Where the index half is on Postgres, `url=` and `passfile=` still name the indexer's login (Y:2326).

### 3.3 The mapping to `PG*` (SVC-2) [Y]

Every `psql` that `lanes-index` runs takes its connection from these variables, and never from `-d <url>` (Y:3491; Y:4970). The mapping is fixed (SVC-2, Y:3805–3812), in one function that the service reuses (T103):

| From | To | Source |
|---|---|---|
| the URL's host | `PGHOST` | Y:3805 |
| its port | `PGPORT` | Y:3805–3806 |
| its database | `PGDATABASE` | Y:3806 |
| its user | `PGUSER` | Y:3806 |
| the query parameter `sslmode` | `PGSSLMODE` | Y:3806–3809 |
| `sslrootcert` | `PGSSLROOTCERT` | Y:3806–3809 |
| `sslcert` | `PGSSLCERT` | Y:3806–3809 |
| `sslkey` | `PGSSLKEY` | Y:3806–3809 |
| `target_session_attrs` | `PGTARGETSESSIONATTRS` | Y:3806–3809 |
| `connect_timeout` | `PGCONNECT_TIMEOUT`: the workstation's `c`; under 2 refused; with none, 2 | Y:3808–3812 |
| the call's password file: `register_passfile=` for REGISTER-WRITER, `reader_passfile=` for READER, `passfile=` for the indexer | `PGPASSFILE`; with none, today's no-password file | contract §2.2; T103 |
| the call's D9 bounds | `PGOPTIONS`, `-c lock_timeout=<ms> -c statement_timeout=<ms>` | Y:3503; contract §2.2 |
| — | `PGAPPNAME`, `lanes-index` | contract §2.2 |

- **Dropped from the inherited environment:** `PGPASSWORD`, `PGPASSFILE`, `PGSERVICEFILE` and `PGSYSCONFDIR`, so no inherited variable points libpq at another file (Y:4970; contract §2.2).
- **`PGCONNECT_TIMEOUT` is set, not defaulted,** on register paths, so an inherited value cannot lengthen the bound. The index paths keep today's 5 s (Y:4970).
- **`PGSERVICE=<service>`** is set only on `bootstrap`'s and `login`'s admin path, never as `service=` or `-d` on a command line (ops:404; T103).
- **Any other query parameter, or a password in the URL, is refused** (Y:3812–3814; Y:4970), so the direct read and the service connect alike (section 4, R5 and R13).

## 4. The refusals

**What a refusal is.** Each is a refused configuration, raised by `load_config` before any connection is opened. Its code is 2, but 3 on `journal --write-id` and 1 under `register read --for guard` (exit-codes.md section 2; contract §2.5, §2.7). Each names the file, the line or the key, and what is wrong, and prints no URL and no password (T104; Y:3797–3799).

| # | Refused | Old or new | Source |
|---|---|---|---|
| R1 | the file readable by group or others, or not a regular file: it must be 0600 | old | Y:2323; `code/lanes-index:62–68` |
| R2 | a file `$LANES_INDEX_CONFIG` names that does not exist | old | Y:5709; `code/lanes-index:161–170` |
| R3 | the file inside a git work tree, or under `~/.agents/` | old | Y:2323 |
| R4 | a line that is not `key=value`; a key `lanes-index` does not know | old | Y:5709; `load_config` (`code/lanes-index:248`) |
| R5 | a URL carrying a password: `url=` and `reader_url=` as today, and now `register_url=` | old, extended | Y:2323; Y:4849 |
| R6 | `store=` other than `sqlite` or `postgres`; `store=postgres` with no `url=`; a `schema=` that is not a plain lower-case SQL identifier; a `passfile=` or `reader_passfile=` that is not a readable 0600 regular file; a SQLite file inside a git work tree | old | `load_config` (`code/lanes-index:248`) |
| R7 | `mode=` other than `index` or `primary` | new | Y:5709; Y:4849 |
| R8 | `mode=primary` with SQLite | new | FR-001; T104 |
| R9 | `mode=primary` with no `register_url=` | new | T104 [T] |
| R10 | `register_url=` with no `reader_url=`: a read never uses the writer's credential | new | T104 [T] |
| R11 | a `register_passfile=` or `render_key=` that is not a readable 0600 regular file | new | Y:4849; T104 |
| R12 | a `register_schema=` that is not a plain lower-case SQL identifier | new | T104 [T] |
| R13 | a query parameter outside section 3.3's six | new | Y:3812–3814; Y:4970 |
| R14 | a `connect_timeout=` under 2, since libpq reads 1 as 2 and 0 as no limit | new | Y:3810–3811; contract §2.5 |
| R15 | the TLS refusal at load, below | new | R09; DEC-03; T104; contract §2.5 |
| R16 | [P] a `register_addresses=` that is not a whole number of at least 1, or a `register_rtt_ms=` that is not a whole number of at least 0 | new | — |
| R17 | `mode=primary` while Q6(c)'s refusal is built in: 78 from `mode` and `register read`, and 2 from every other command | new | Y:4849; Y:4872; Y:2449–2453 |

**R15, the TLS refusal at load** (lane 3's R09 left it to lane 1, and lane 1 took it: DEC-03; T104) [T]:
- **The rule:** `load_config` refuses a `register_url=` that names a TCP host that is not loopback, unless its query carries `sslmode=verify-full` and an `sslrootcert=` that names an existing regular file. An `sslrootcert=system` is refused at such a host (Y:2324).
- **`reader_url=`** is refused on the same terms, but only where `register_url=` is set.
- **Allowed without TLS:** a Unix socket, which is no host or a host that decodes to an absolute path, and a loopback host, as contract §2.5 and DEC-03 list them. So the fixture's socket cluster and its loopback stand-ins keep working. Where a URL names several hosts, each must pass (contract §2.5).
- **Its scope:** a file with no `register_url=` is #160's, and loads as today (US1). #160's tests configure a `reader_url=` with no TLS, and T104's `tls_at_load` case 6 holds that it loads.
- **Its reach** `[X: hosting]`: every real URL names `<endpoint>`, which is not loopback, so the refusal reaches every workstation's file (T104).
- **Its code:** a refused configuration's, as above.
- **FR-011's other two places:** `ready`'s check 11 (ops:454; T119) and the server's `hostnossl` reject (ops:144).

**Refusals by a command that reads a key,** not by `load_config`:
- **`cutover`** refuses with 2 a file with no `register_label=`, before it connects; and, where the switch is at `files`, a `register_label=` that is not the switch's label, before it sends anything (ops:379; N4, S-2).
- **`ready`'s check 10** fails where `register_label=` differs from the switch's `database_label` (ops:453).
- **`ready`'s check 13** fails where the endpoint resolves to more addresses than `register_addresses=` (ops:456).
- **`set-mode primary`** refuses with 2 a file on a read-only mount, which is flipped from the host (ops:470).

## 5. Where `c`, `N_a` and `w_s` come from (R05) [C]

The contract's §2.5 fixes this, and `lanes-edit.sh` parses none of it (R05).

| Value | Read from | Printed by |
|---|---|---|
| `c` | the `connect_timeout=` of the URL the call connects with: `register_url=`'s for the writer's calls, `reader_url=`'s for the reads. Default 2; under 2, R14 | `register snapshot`'s `c=`; `.head`'s `c=`, the larger of the two URLs' (contract §3.2, §3.6) |
| `N_a` | `register_addresses=`, default 1 | `register snapshot`'s `n_a=`; `.head`'s `n_a=` |
| `w_s` | `register_rtt_ms=`: 0.3 s, or `register_rtt_ms=` + 200 ms where that is over 100; 0 where no service can answer | `.head`'s `w_s_ms=`, in whole milliseconds |

Each is measured when the workstation joins, at act 3 (Y:5877; Y:5912; Y:4005–4008).

## 6. The declaration: `register-outage`

### 6.1 Where it lives, and who touches it [Y]

- **The file** is `register-outage`, beside `lanes-index.conf` in the same directory (D20, Y:599; ops:509). Under Q23(a) it sits on the host's read-only mount, so one write per host reaches every guard there (ops:512).
- **Written by** `lanes-index outage declare`, on Brett's word (ops:496–499), and by `rollback`'s start, or, where its directory is read-only, by the `outage declare --rollback --mirror-seq <k>` it prints (ops:500; T304; T121).
- **Removed by** `lanes-index outage clear` (ops:541; T121).
- **Read by:**
  - the guard's client, only on `mode=primary`, never the service, once at the prefetch (SVC-12, Y:4447–4454; ops:518; lane 5's T507);
  - `status`, which reports a declaration left in place (ops:544; T117);
  - `ready`'s check 7, which fails on one (ops:450; T119).

### 6.2 Its fields [Y]

ops:511 lists them, "none of them a secret":
- the database label it covers;
- Brett's words, verbatim;
- the UTC;
- the host;
- the expiry, which a rollback's declaration does not carry;
- `mirror-seq=<m>`, the mirror's `Register-Seq` when it was declared, or the rollback's head `k` in a rollback's declaration (F11; SVC-12; R2's M1);
- for the disaster path's notice, the last head `status` recorded.

A rollback's declaration is also marked as from a rollback (D20, Y:605–606; ops:500).

### 6.3 Its form [P]

Y fixes the fields and the spelling `mirror-seq=`, but not the file's form. T121 writes it, and lane 5's T507 reads it, from this section (T121; tasks.md). Lane 1 proposes:

```text
format=1
kind=<word or rollback>
database=<label>
utc=<UTC>
host=<workstation>
until=<UTC>
mirror-seq=<seq>
why=<Brett's words, on one line>
last-head=<seq>
```

| Key | When | Value | Source |
|---|---|---|---|
| `format=` | always, the first line | `1` | [P], as the contract's outputs carry `format=1` (§2.8) |
| `kind=` | always | `word`, a declaration on Brett's word; or `rollback`, the rollback's mark | D20 (Y:605–606); ops:500 |
| `database=` | always | the label it covers: the declaring host's `register_label=` [P] | ops:511 |
| `utc=` | always | when it was declared | ops:511 |
| `host=` | always | the workstation it was declared on | ops:511 |
| `until=` | with `kind=word` only | the expiry, from `--until` | ops:498; ops:503 |
| `mirror-seq=` | always | `<m>` with `kind=word`; `<k>` with `kind=rollback`; a whole number | ops:497; ops:500 |
| `why=` | always | `--why`, verbatim, on one line. [P] In a rollback's declaration with no `--why`, the word `rollback` | ops:499; DEC-21 |
| `last-head=` | where `status` recorded one | the last head `status` recorded | ops:511 |

**The rules of the form** [P]:
- One `key=value` per line, split at the first `=`, LF-ended, UTF-8, each key at most once.
- Each UTC, `utc=` and `until=`, is `YYYY-MM-DDTHH:MM:SSZ`. `--until` takes the same form, and `outage declare` refuses another with 2.
- A reader ignores a key it does not know within `format=1`, as the contract's callers ignore stdout keys they do not know (§5).
- `outage declare` writes the file by temp file and rename in the same directory, so no reader sees half a file.
- **Well formed** means: `format=1`; each key marked "always" present once; `until=` present exactly when `kind=word`; `mirror-seq=` a whole number; and `why=` with no line break, which `outage declare` refuses (DEC-21).

### 6.4 How the guard reads it [Y]

SVC-12's conditions, checked in order, offline (Y:4439–4466; ops:517–519):
1. **Its database read failed:** `register read` exit 69 (Y:4445–4446; ops:517), or a read that found the switch at `files`, which counts as failed (D20, Y:609–610; ops:517). By either path that read is a 69 whose cause line names `switch-files` (contract draft 4.6, :653, :767).
2. **The declaration counts** (Y:4447–4454; ops:518). `register-outage` is present beside `lanes-index.conf`, well formed and unexpired, and names this workstation's database label: [P] its `database=` equals the reading host's `register_label=`.
   - With `kind=word`, it is unexpired until `until=`, after which every guard ignores it (ops:542).
   - With `kind=rollback`, it carries no expiry and counts as unexpired until it is cleared, but only where condition 1's read found the switch at `files`, its cause `switch-files` (Y:4449–4454; contract draft 4.6, :767).
   - The client reads it once, at the prefetch, before the direct read (Y:4448–4449; ops:518). A file that is not well formed fails this condition.
3. **The mirror is new enough:** `origin/main` as last fetched carries a `Register-Seq` of at least `mirror-seq=`, read by form C (Y:4455–4466; ops:519).

**What it then prints:**
- **All three hold:** the guard answers from the mirror with `(declared outage <utc>: <why>)` in place of the cause, from `utc=` and `why=` (Y:4468–4470; ops:521).
- **A mirror below `mirror-seq=`** refuses 2 with lane 5's text (Y:4472–4474; ops:523).
- **Any other failed condition** refuses 2 with S3's outage text (Y:4475; ops:525).

The client's `.outage` file carries `label=(declared outage <utc>: <why>)` and `declared_seq=` from this file's `utc=`, `why=` and `mirror-seq=` (contract §3.6).

### 6.5 Its writer's refusals

`outage declare`'s and `outage clear`'s codes are in [exit-codes.md](exit-codes.md) section 3.2: ops §2.5's refusals (ops:502–507), no `mirror-seq` (ops:497) and a `--why` with a line break (DEC-21), each 2.

## 7. What this file proposes for others to confirm

Each [P] above is lane 1's proposal, for the reader's lane to confirm or object to:
- **Lane 5, for T507:** the declaration's form (section 6.3), and the key the guard compares `database=` with, `register_label=` (section 6.4).
- **Lane 1's own T104:** R16's value checks on `register_addresses=` and `register_rtt_ms=`.
- **Lane 3:** a ruling where a lane objects.
