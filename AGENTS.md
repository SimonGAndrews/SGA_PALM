# AGENTS.md

This repository is spec-led. Before making changes, read `PROJECT_CONTEXT.md` and the architecture docs under `docs/architecture/`.

## Authority order

When context conflicts, use this order:

1. `docs/architecture/*.md`
2. `PROJECT_CONTEXT.md`
3. `AGENTS.md`
4. temporary notes, comments, and scratch files

If a lower-priority source disagrees with a higher-priority one, update it or ask for direction.

## Working rules

- Keep changes aligned with the current architecture direction.
- Prefer updating the spec docs over adding ad hoc notes.
- If a decision changes, update the relevant architecture doc and `PROJECT_CONTEXT.md` in the same work session when practical.
- Do not introduce direct database access from clients.
- Do not let browser or device clients bypass the PALM service for business-rule operations.
- Keep implementation choices aligned with the service-led, relational model.

## Context hygiene

- Treat `PROJECT_CONTEXT.md` as project memory, not a second spec.
- Delete scratch files once their content has been absorbed into the real docs.
- Record unresolved items as open decisions instead of guessing.
- Repair drift before or alongside implementation.

## Editing discipline

- Use `apply_patch` for manual edits when possible.
- Preserve user changes and unrelated work.
- Avoid destructive commands unless explicitly requested.
- Prefer small, reviewable updates.

## Current project shape

- PALM is a Personal Asset and Lifecycle Management system.
- The application direction is an Angular PWA.
- The service owns rules, transactions, and history.
- PostgreSQL is the authoritative relational store.
- Device clients use meaningful PALM transactions rather than direct record editing.
- Local hardware such as Zebra printers may need a local adapter or agent.