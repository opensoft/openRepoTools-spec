# Data Model: 007-sql-register

Phase 1 of `/speckit.plan`. The register's database objects, as `design.md`
§1.1 to §1.4 define them at Y. This file is an index to that text, not a
restatement of it: where a requirement governs an object, it names the
requirement, and the requirement's own words hold.

**Citations** are spec.md's: `spec:<n>` and `svc:<n>` are lines of the
change's two spec files on this branch, at `ce3fad83`; `Y:<n>` is a line of
`design.md` and `ops:<n>` of `operations.md`, which are unchanged from Y.

## Where it lives

- **One PostgreSQL 16 database,** with a schema of its own, `lanes_register`,
  beside #160's `lanes_index` (§1.1, Y:1025). Paths are `COLLATE "C"`, and
  every connection sets `TimeZone=UTC`.
- **`lanes_register`** is owned by `lanes_register_owner`, and so is every
  table and function in it (Y:1039–1040). **`lanes_index`** is owned by
  `lanes_indexer` (Y:2269).
- **Migration 1** makes the schema, run by `bootstrap` as the owner role, in
  one transaction (§1.2, Y:1033). Forward-only migrations follow it (§1.5,
  Y:2330).

## Tables in `lanes_register`

| Table | Holds | Key | Y |
| --- | --- | --- | --- |
| `schema_version` | the version, and the oldest client versions that may write and read | one row, `id = 1` | Y:1043 |
| `migrations` | one row per migration script applied | `version` | Y:1051 |
| `register_switch` | the switch: `mode` (`files` or `primary`), `marker_state`, the `generation`, `database_label`, the import's commit, tree and seq, and `set_seq` | one row, `id = 1` | Y:1058 |
| `register_head` | the journal's head `seq`, -1 when empty; its row lock orders every write | one row, `id = 1` | Y:1086 |
| `mirror_state` | what the renderer reports: a divergence, and the last failed pass | one row, `id = 1` | Y:1090 |
| `journal` | one entry per write: `seq` is the `Register-Seq`, one gapless chain (`base_seq` is `seq` - 1), with `write_id`, `kind`, `verb`, `lane`, `workstation`, `author` and `message` | `seq` | Y:1102 |
| `journal_edits` | each entry's line edits, in order | `(seq, ord)` | Y:1133 |
| `journal_files` | each touched file's digest after the write | `(seq, path)` | Y:1151 |
| `journal_scopes` | each entry's touched scopes, derived by the database, never sent | `(scope, seq)` | Y:1160 |
| `files` | each rendered file as it stands: its digest, final newline and last seq | `path` | Y:1168 |
| `doc_lines` | the truth: a file is its lines in `line_no` order | `line_id`, and `(path, line_no)` unique | Y:1176 |
| `lanes` | one row per lane, `LANES.md` and `archive/` together | `lane_key` | Y:1190 |
| `log_lines` | every log line, unreadable ones kept | — | Y:1202 |
| `object_holds` | each lane's last line per object | — | Y:1222 |
| `register_lines` | every non-row `LANES.md` line | — | Y:1254 |

**Views:** `holds` (Y:1240), `stale_claims` (Y:1245) and `main_slots`
(Y:1283). **Functions:** `render_file` (Y:1297), and `commit_write` in full in
§1.3.1 (Y:1577); the writer's five are listed under the grants, below.

**The paths** a file row or an edit may name are the render set and
`lanes/STORE`, never `lanes/log/README.md` (Y:1168–1171). spec.md's terms,
above its user stories, define the render set.

**The switch's states:** `mode` is `primary` exactly when `marker_state` is
`adding` or `present` (Y:1081). `bootstrap` writes it at `files`, with the
marker `absent`, `set_seq` -1 and a new generation, and the head at -1
(ops:420). The cutover and the rollback move it (FR-050; FR-051), and so
does the disaster path (FR-053).

## Sequences

- **No sequence orders the writes:** `register_head`'s one row does, since a
  sequence has gaps and is not commit order (Y:1086–1088).
- **`doc_lines.line_id`** is an identity column, so the schema holds one
  sequence. The reader group may read the schema's sequences, for
  `pg_dump`, now and by default privilege for a later one (Y:2252–2254).
- **`lanes_index`** has no sequence; the reader may read a later one
  (Y:2275–2276).

## Roles

| Role | SQL role | Logs in | Made by | Y |
| --- | --- | --- | --- | --- |
| owner | `lanes_register_owner` | no | `bootstrap` | Y:2235 |
| REGISTER-WRITER | `lanes_register_writer`, a group | no | `bootstrap` | Y:2236 |
| READER | `lanes_register_reader`, a group | no | `bootstrap` | Y:2237 |
| indexer | `lanes_indexer` | yes | `bootstrap` | Y:2238 |
| an engineer's writer login | `lanes_w_<engineer>`, a member of REGISTER-WRITER | yes | `lanes-index login add` | Y:2291–2295 |
| an engineer's reader login | `lanes_r_<engineer>`, a member of READER | yes | `lanes-index login add` | Y:2296–2300 |
| the dump job | `lanes_register_dump`, a member of READER | yes | `lanes-index login add --dump-job` | Y:2288–2290; ops:482 |
| admin | the database's owner, CNPG's `initdb` owner | yes | the install | Y:2283 |

- **The admin** holds the owner and the indexer, by the GRANT that comes
  first (Y:2239–2240), and runs `bootstrap` and `lanes-index login` through
  his admin service.
- **Each login carries its group's floors itself,** since a role's settings
  reach no member (Y:2278): the writer login's `lock_timeout`,
  `statement_timeout` and `idle_in_transaction_session_timeout`, and the
  reader login's read-only default and its two timeouts (Y:2293–2300).
- **Requirements:** "The three roles" (spec:1039), the bootstrap
  (spec:845–846), and FR-009 and FR-054.

## Grants

- **The database:** nothing for PUBLIC; CONNECT for the two groups and the
  indexer (Y:2243–2244).
- **`lanes_register`:** USAGE for the two groups, and SELECT on every
  table, now and by default privilege for a later one (Y:2246–2251).
- **Functions:** none for PUBLIC, now and by the owner's global default
  privilege (Y:2255–2257). The writer group may execute `commit_write`,
  `switch_to_files`, `record_marker`, `find_write` and
  `record_mirror_state`, and both groups `render_file` (Y:2258–2265). The
  writer has no INSERT, UPDATE, DELETE or DDL anywhere (Y:2263).
- **`lanes_index`:** USAGE and SELECT for the reader group. The register's
  writer gets nothing there, and the indexer nothing in `lanes_register`
  (Y:2267–2276).
- **After a restore from a dump:** the roles-only form makes the roles, the
  admin's membership and the three database-level grants that a dump does
  not carry (spec:846; research.md's ERR-03).
