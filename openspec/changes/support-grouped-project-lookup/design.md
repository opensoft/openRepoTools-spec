# Design

## Context
The four installed commands are self-contained Bash scripts. Adding a runtime sidecar would change the installer payload contract.

## Goals / Non-Goals
Support bounded grouping while retaining existing path authority. No layout migration, pin updates, register reattachment, alias removal, full installer execution or runtime restart is included.

## Decisions
Use an identical Bash 3.2-compatible discovery block in each command, protected by a consistency test. Inspect up to two grouping levels rather than recursively scanning repositories. Stop on Git metadata, project/family manifests or family holders. Exclude workTrees, worktrees, laneWorkspaces and brett-wip-lanes. Carry logical names alongside physical paths; deduplicate after matching so alternate names remain usable. Preserve projects-root ordering for estate lookup. Explicit/recorded lane paths remain authoritative; grouped discovery fills only an unanswered default rung.

## Risks / Trade-offs
- Deep custom layouts are outside the bound; explicit paths remain available.
- Copied discovery code can drift; a consistency test guards all four copies.
- Symlink cycles are bounded and physical targets are deduplicated.

## Migration Plan
Validate in isolated worktrees and fake lane/register environments. Publish source changes through normal leg review without advancing assembly pins. Installed activation must be distinguished from source verification. Rollback restores the prior command versions; no state schema changes occur.
