# Plan: Security Guidance

## Context

Security is the last Tier 2 specialized artifact. Auth/authz/threat model docs are either dangerously stale or uselessly generic — writers either dump framework defaults (JWT validation, CORS headers, bcrypt rounds) that an AI reads from middleware config, or write threat models so abstract they're unfalsifiable. The guidance needs to focus on what's project-specific and invisible in code: trust boundaries, permission model rationale, security invariants that break silently.

The guidance recipe (`specs/guidance-recipe.md`) prescribes 5 steps run as isolated subtasks: load quality principles, research, write, evaluate, final review.

## Why security next

- Last remaining Tier 2 artifact — completes the "common specialized artifacts" tier
- `api-docs.md`, `data-model.md`, and `deployment.md` already done
- Security docs have the highest staleness risk after API docs — auth flows change, new providers get added, permission models evolve
- Security is the artifact type most prone to restating framework/library defaults (passport config, CORS middleware, helmet headers) that AI reads directly from code
- Writers routinely produce generic OWASP checklists instead of documenting project-specific trust boundaries and permission logic

## Deliverables

| File | What | Size target |
|------|------|-------------|
| `research/security-specs.md` | Research findings on security documentation for AI | — |
| `guidance/security.md` | Distilled writing guide | <2KB |
| `guidance/INDEX.md` | Updated with new entry | +1 row |

## Process (per guidance-recipe.md)

| # | Step | Method |
|---|------|--------|
| 1 | Load quality principles | Subtask reads `guidance/ai-doc-quality.md`, returns summary |
| 2 | Research best practices | Subtask researches security documentation for AI, writes to `research/security-specs.md` |
| 3 | Write guidance document | Subtask reads research + quality principles, writes `guidance/security.md`, updates INDEX |
| 4 | Evaluate document | Subtask applies self-check from `ai-doc-quality.md`, trims to <2KB |
| 5 | Final review | Orchestrator reads final files, presents to user |

## Also needed

- Update `specs/development-history/README.md` with phase 11 row

## Status

| # | Step | Status |
|---|------|--------|
| 1 | Load quality principles | done |
| 2 | Research | done |
| 3 | Write guidance | done |
| 4 | Evaluate | done — 1996 bytes, all checks pass |
| 5 | Final review | done |
| — | Update dev history README | done |
