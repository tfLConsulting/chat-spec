# Plan: API Docs Guidance

## Context

API Docs is a non-core artifact (max 17). It's the type most prone to restating route definitions and OpenAPI schemas that an AI would discover by reading route files, controllers, and type definitions. The rubric rewards documenting *implicit behaviour*, *non-obvious constraints*, and *domain-specific error handling* — things invisible in code. Writers need guidance on what API documentation adds value vs. what restates the framework.

The guidance recipe (`specs/guidance-recipe.md`) prescribes 5 steps run as isolated subtasks: load quality principles, research, write, evaluate, final review.

## Why api-docs next

- All four core artifacts now have guidance (architecture, conventions, dev-context, features)
- `endpoint_list` (W:3) and `request_response` (W:3) carry the most weight — but are where writers most often reproduce route definitions and type signatures
- `authentication` (W:2) is routinely filled with standard OAuth/JWT flows instead of project-specific token logic
- `error_handling` (W:2) typically restates framework defaults instead of documenting domain-specific codes
- `non_obvious` (W:2) defaults to listing status codes an AI already knows from HTTP specs
- API docs are the artifact most likely to go stale — endpoints change frequently and writers document the snapshot rather than the invariants

## Deliverables

| File | What | Size target |
|------|------|-------------|
| `research/api-docs-specs.md` | Research findings on API documentation for AI | — |
| `guidance/api-docs.md` | Distilled writing guide | <2KB |
| `guidance/INDEX.md` | Updated with new entry | +1 row |

## Process (per guidance-recipe.md)

| # | Step | Method |
|---|------|--------|
| 1 | Load quality principles | Subtask reads `guidance/ai-doc-quality.md`, returns summary |
| 2 | Research best practices | Subtask researches API documentation for AI, writes to `research/api-docs-specs.md` |
| 3 | Write guidance document | Subtask reads research + quality principles, writes `guidance/api-docs.md`, updates INDEX |
| 4 | Evaluate document | Subtask applies self-check from `ai-doc-quality.md`, trims to <2KB |
| 5 | Final review | Orchestrator reads final files, presents to user |

## Also needed

- Update `specs/development-history/README.md` with phase 10 row

## Status

| # | Step | Status |
|---|------|--------|
| 1 | Load quality principles | done |
| 2 | Research | done |
| 3 | Write guidance | done |
| 4 | Evaluate | done — trimmed to 1989 bytes, all checks pass |
| 5 | Final review | done |
| — | Update dev history README | done |
