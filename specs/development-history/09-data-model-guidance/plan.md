# Plan: Data Model Spec Guidance

## Context

Data Model is a specialised artifact (max 17). It's the type most prone to restating what schemas, migrations, and ORM definitions already express. The rubric rewards documenting *domain meaning*, *implicit relationships*, *storage rationale*, and *lifecycle logic* — things invisible in DDL and model files. Writers need guidance on what data model documentation adds value vs. what duplicates the schema.

The guidance recipe (`specs/guidance-recipe.md`) prescribes 5 steps run as isolated subtasks: load quality principles, research, write, evaluate, final review.

## Why data-model next

- First specialised artifact type to get a guidance doc — extends coverage beyond the 4 core types
- `entities` (W:3) and `relationships` (W:3) carry the most weight — but writers default to listing table columns and foreign keys already visible in schema files
- `storage` (W:2) is where the real value lives — *why* certain data lives in Redis vs Postgres vs S3 — but is routinely filled with "we use Postgres" (already in config)
- `lifecycle` (W:2) covers state machines, soft deletes, archival — often undocumented and a major source of bugs when AI agents modify data operations
- `non_obvious` (W:2) is the hardest to get right — denormalisation decisions, constraint rationale, things that look like schema mistakes but aren't
- `validation` (W:1), `migration` (W:1), `indexes` (W:1) are lower weight but writers frequently restate what the ORM config already expresses

## Deliverables

| File | What | Size target |
|------|------|-------------|
| `research/data-model-specs.md` | Research findings on data model documentation for AI | — |
| `guidance/data-model.md` | Distilled writing guide | <2KB |
| `guidance/INDEX.md` | Updated with new entry | +1 row |

## Process (per guidance-recipe.md)

| # | Step | Method |
|---|------|--------|
| 1 | Load quality principles | Subtask reads `guidance/ai-doc-quality.md`, returns summary |
| 2 | Research best practices | Subtask researches data model documentation for AI, writes to `research/data-model-specs.md` |
| 3 | Write guidance document | Subtask reads research + quality principles, writes `guidance/data-model.md`, updates INDEX |
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
| 4 | Evaluate | done — trimmed to 1987 bytes, removed unsupported "index rationale" row, all checks pass |
| 5 | Final review | done |
| — | Update dev history README | done |
