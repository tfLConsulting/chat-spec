# Decisions Log

Chronological record of project decisions. For the consolidated key decisions table, see `specs/dev-context.md`.

## 2026-02-26: Guidance layer

**Context:** Research showed AI-generated docs can hurt performance. Needed actionable guidance for spec writers beyond just rubric scores.

**Decision:** Added `guidance/` directory with research-distilled writing guides. First guide: `ai-doc-quality.md`. Created `specs/guidance-recipe.md` to standardise the research-then-write process for future guides.

**Status:** Done. Phase 05 complete.

## 2026-02-26: Rubric redesign

**Context:** Rubrics measured completeness (good for new docs) but couldn't detect problems in existing docs — verbosity, staleness, code restatement all scored well.

**Decision:** Reframed rubric questions toward value-added. Boosted `current` W:1→W:2. Added universal penalty items: `code_restatement` -2, `contradictions` -3. Research-grounded (see `research/ai-doc-quality.md`).

**Status:** Done. Phase 04 complete.

## 2026-02-25: Project approach

**Context:** Initial idea exists. Before building anything, need to understand the landscape.

**Decision:** Research-first approach. Broad survey of existing tools. If something popular already does what we want, adopt it. If not, use research to define what chat-spec needs.

**Status:** Research complete. See `specs/landscape-research.md`. The scored-manifest-plus-iterative-improvement approach is novel — no existing tool does this.
