# Plan: Deployment Guidance

## Context

Deployment is a secondary artifact (max 17). It's the type most prone to restating CI/CD config, Dockerfiles, and IaC that an AI reads directly. The rubric's highest-weight items (`environments` W:3, `deploy_process` W:3) reward documenting what's *different* between environments and what manual steps aren't captured in automation — things invisible in config files. Writers need guidance on what deployment documentation adds value vs. what restates infrastructure-as-code.

The guidance recipe (`specs/guidance-recipe.md`) prescribes 5 steps run as isolated subtasks: load quality principles, research, write, evaluate, final review.

## Why deployment next

- Core artifacts (architecture, conventions, features, dev-context) all have guidance docs
- `environments` (W:3) and `deploy_process` (W:3) are where writers most often just list what CI/CD config already says
- `configuration` (W:2) routinely filled with env var lists already in `.env.example` or config files
- `non_obvious` (W:2) — the gap between "what the pipeline does" and "what you need to know that the pipeline doesn't tell you" is where deployment docs add real value
- `rollback` (W:1) and `monitoring` (W:1) are often omitted or aspirational rather than documenting actual procedures

## Deliverables

| File | What | Size target |
|------|------|-------------|
| `research/deployment-specs.md` | Research findings on deployment documentation for AI | — |
| `guidance/deployment.md` | Distilled writing guide | <2KB |
| `guidance/INDEX.md` | Updated with new entry | +1 row |

## Process (per guidance-recipe.md)

| # | Step | Method |
|---|------|--------|
| 1 | Load quality principles | Subtask reads `guidance/ai-doc-quality.md`, returns summary |
| 2 | Research best practices | Subtask researches deployment documentation for AI, writes to `research/deployment-specs.md` |
| 3 | Write guidance document | Subtask reads research + quality principles, writes `guidance/deployment.md`, updates INDEX |
| 4 | Evaluate document | Subtask applies self-check from `ai-doc-quality.md`, trims to <2KB |
| 5 | Final review | Orchestrator reads final files, presents to user |

## Also needed

- Update `specs/development-history/README.md` with phase 09 row

## Status

| # | Step | Status |
|---|------|--------|
| 1 | Load quality principles | done |
| 2 | Research | done |
| 3 | Write guidance | done |
| 4 | Evaluate | done — 1998 bytes, all checks pass |
| 5 | Final review | done |
| — | Update dev history README | done |
