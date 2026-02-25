# Decisions Log

## 2026-02-25: Project approach

**Context:** Initial idea exists in `inbox/initial-idea.md`. Before building anything, we need to understand the landscape.

**Decision:** Research-first approach. Broad survey of existing tools and frameworks that help prepare projects for AI assistants. If something popular already does what we want, adopt it. If not, use the research to define what features chat-spec needs.

**Status:** Research complete. See `specs/landscape-research.md`.

**Key findings:**
- The space is crowded (Spec Kit 71.9k stars, BMAD 37.8k, OpenSpec 25.6k, Repomix 70k)
- But no tool does what chat-spec proposes: a **scored manifest tracking documentation maturity** with **iterative improvement**
- Closest match is Packmind (214 stars) which evaluates quality but doesn't maintain a persistent manifest
- ETH Zurich research shows content quality matters far more than just having config files
- SDD tools are criticized as "reinvented waterfall" — chat-spec's iterative approach may avoid this

**Next decision needed:** Build or adopt? The research suggests chat-spec's core differentiator (scored manifest + iterative improvement) is genuinely novel. Need to decide scope and approach.
