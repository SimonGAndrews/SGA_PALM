# PROJECT_CONTEXT.md

Last updated: 2026-07-31

## Project Summary

PALM is the Personal Asset and Lifecycle Management system. The architecture docs in `docs/architecture/` are the source of truth.

PALM models assets and the events and transactions that change them. Current state must stay easy to query while lifecycle history remains intact.

## Current Direction

- Main UI: Angular Progressive Web Application
- Deployment: Google-hosted services
- Service model: one PALM application service first, not microservices
- Database: PostgreSQL as the authoritative relational store
- Clients: browser, phone, tablet, and small device clients all go through the PALM service
- Device interaction: meaningful transactions, not direct database editing
- Printing: PALM owns label requests and content; a local adapter or agent handles the printer connection when needed

## Documents That Matter

- `docs/architecture/ArchitectureOverview.md`
- `docs/architecture/ApplicationArchitecture.md`
- `docs/architecture/DeviceArchitecture.md`
- `docs/architecture/GoogleDeployment.md`
- `docs/architecture/DocumentationPrinciples.md`

## Status

The old `docs/architecture/notes.md` content has been folded into the architecture docs and the file is deleted.

The repository currently contains architecture decisions only. There is no committed application code, schema, or API implementation yet.

## Working Assumptions

- Keep the first version simple and low-cost.
- Keep business rules, validation, and transaction recording in the service.
- Keep clients thin and workflow-focused.
- Use XState or a similar finite-state approach only where the workflow is genuinely multi-step.
- Add offline queueing only when a real use case justifies the complexity.

## Open Decisions

- exact Google product combinations after testing
- local agent versus Zebra Browser Print, or both
- embedded device credential mechanism
- cloud-generated versus adapter-side label content boundary
- final API grouping when the API spec is written

## Context Rules

- Update this file when the project direction changes in a meaningful way.
- Keep this file short and current.
- Put new decisions in the architecture docs first, then summarize them here.
- Remove scratch files after their useful content has been absorbed.