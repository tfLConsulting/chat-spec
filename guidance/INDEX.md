# Guidance

Reference material for writing better specs. These are not scored artifacts or rubrics — they are research-distilled principles to apply when writing or evaluating documentation.

| Guide | When to use |
|-------|-------------|
| [ai-doc-quality.md](ai-doc-quality.md) | Before writing or evaluating any spec. What helps vs. hurts AI tools, common failure modes, self-check questions. |
| [architecture-specs.md](architecture-specs.md) | Before writing architecture documentation. What content helps vs. hurts AI agents, ADR format, scoping, staleness rules. |
| [claude-md.md](claude-md.md) | Before writing or reviewing a CLAUDE.md file. Claude Code-specific patterns, layering strategy, sizing, what research says works. |
| [conventions.md](conventions.md) | Before writing conventions/coding-standards specs. What belongs vs what linters handle, rule format, scaling strategy. |
| [data-model.md](data-model.md) | Before writing data model specs. What agents discover from schemas vs. what to document — business terms, implicit relationships, denormalisation rationale, state machines. |
| [deployment.md](deployment.md) | Before writing deployment specs. What IaC already covers, what to add (environment decisions, manual steps, security invariants, rollback, monitoring thresholds). |
| [dev-context.md](dev-context.md) | Before writing any dev-context file (CLAUDE.md, AGENTS.md, cursorrules). Progressive loading, what to include/exclude, building from failures. |
| [api-docs.md](api-docs.md) | Before writing API documentation. What agents need beyond schemas — disambiguating descriptions, scopes per endpoint, examples, agent auth patterns. |
| [features.md](features.md) | Before writing feature specs. What to document vs. what agents discover, status tracking, scope boundaries. |

**Creating new guides:** Follow the recipe at [`specs/guidance-recipe.md`](../specs/guidance-recipe.md).
