## Why
Project roots now live in organizational folders below the projects directory. Direct-child-only lookup requires compatibility aliases and prevents normal discovery after those aliases are retired. The user requested this bounded lookup update on 2026-11-15.

## What Changes
- Discover flat projects and projects below up to two grouping folders.
- Stop at repositories and estate boundaries; exclude worktree and lane workspace collections.
- Deduplicate aliases by physical target and refuse distinct same-name candidates.
- Preserve explicit and recorded lane directory precedence, identity validation, register independence and all pins.

## Capabilities
### New Capabilities
- `grouped-project-lookup`: bounded project and estate discovery across grouped folders.

### Modified Capabilities
None; the current OpenSpec inventory contains no capability specs.

## Impact
Code: lane-start, park, resume and status; isolated lookup tests. Manuals and governance records live in the spec leg. No pin, register schema, lane-state, installer payload or runtime restart change.
