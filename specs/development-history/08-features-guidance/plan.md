# Plan: Features Spec Guidance

## Context

Features is a core artifact (max 17). It's the type most prone to describing what the product does in terms a user would discover by clicking around — or an AI would discover by reading routes, components, and tests. The rubric rewards documenting *intent*, *non-obvious behaviour*, and *feature status* — things invisible in code. Writers need guidance on what features documentation adds value vs. what restates the UI.

The guidance recipe (`specs/guidance-recipe.md`) prescribes 5 steps run as isolated subtasks: load quality principles, research, write, evaluate, final review.

## Why features next

- Last remaining core artifact without a guidance doc (architecture, conventions, dev-context all done)
- `core_features` (W:3) and `feature_detail` (W:3) carry the most weight — but are where writers most often just list what the app does
- `user_perspective` (W:2) is routinely filled with UI walkthroughs instead of non-obvious workflows
- `feature_status` (W:2) is often omitted entirely or left stale
- `non_obvious` (W:2) is the hardest to get right — writers default to documenting the obvious

## Deliverables

| File | What | Size target |
|------|------|-------------|
| `research/features-specs.md` | Research findings on features documentation for AI | — |
| `guidance/features.md` | Distilled writing guide | <2KB |
| `guidance/INDEX.md` | Updated with new entry | +1 row |

## Process (per guidance-recipe.md)

| # | Step | Method |
|---|------|--------|
| 1 | Load quality principles | Subtask reads `guidance/ai-doc-quality.md`, returns summary |
| 2 | Research best practices | Subtask researches features documentation for AI, writes to `research/features-specs.md` |
| 3 | Write guidance document | Subtask reads research + quality principles, writes `guidance/features.md`, updates INDEX |
| 4 | Evaluate document | Subtask applies self-check from `ai-doc-quality.md`, trims to <2KB |
| 5 | Final review | Orchestrator reads final files, presents to user |

## Also needed

- Update `specs/development-history/README.md` with phase 08 row

## Status

| # | Step | Status |
|---|------|--------|
| 1 | Load quality principles | done |
| 2 | Research | done |
| 3 | Write guidance | done |
| 4 | Evaluate | done — trimmed to 1960 bytes, all checks pass |
| 5 | Final review | done |
| — | Update dev history README | done |
