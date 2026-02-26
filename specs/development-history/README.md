# Development History

Each directory is a phase of work, in order.

| # | Phase | Status | Summary |
|---|-------|--------|---------|
| 01 | Noodling | **done** | 5 idea sketches, 10 decision points, 8 explorations. Concluded: chat-spec is a protocol distributed as a GitHub repo. The README is the landing page, PROTOCOL.md is the product. |
| 02 | Protocol design | **done** | 9 design explorations (DQ0-DQ8): spec-writing principles (research-grounded), session model, state/memory, manifest schema, rubric design, first-run, subsequent-runs, protocol document structure, README structure. |
| 03 | Build | **done** | Synthesized explorations into deliverables: PROTOCOL.md, RUBRICS.md, README.md. Blank-slate tested and dogfooded. |
| 04 | Rubric redesign | **done** | Rubrics had a new-doc bias — measured completeness but couldn't detect problems in existing docs (verbosity, staleness, code restatement). Reframed questions toward value, boosted `current` W:1→W:2, added universal penalty items (`code_restatement` -2, `contradictions` -3). Research-grounded (see `research/ai-doc-quality.md`). |
| 05 | Guidance layer | **done** | Added `guidance/` directory with research-distilled writing guides (`ai-doc-quality.md`). Added guidance references near the top of PROTOCOL.md and RUBRICS.md. Added repo URL and update mechanism (Section 10) so cached copies can be refreshed without AIs rewriting from memory. |
| 06 | Architecture guidance | **done** | Added `guidance/architecture-specs.md` — writing guide for architecture docs that help AI tools. Research-grounded (AGENTbench, Codified Context paper, ADR practices). Key finding: decision rationale in ADR format is highest-value; component listings are ignored or harmful. Also fixed INDEX.md (missing `dev-context.md` entry). |
| 07 | Conventions guidance | **done** | Added `guidance/conventions.md` — writing guide for conventions/coding-standards specs. Research-grounded (AGENTS.md study: 28.6% task time reduction; Codified Context paper: stale conventions cause silent failures). Key finding: linter-enforceable rules don't belong; human-judgment conventions (architectural patterns, library choices, naming with project meaning) are what AI agents need. |
| 08 | Features guidance | **done** | Added `guidance/features.md` — writing guide for feature specs. Research-grounded (Codified Context: >50% of effective spec content is domain facts; AGENTbench: feature lists hurt -2-3%; GitHub 2,500-repo study: agents drift without scope boundaries). Key finding: document *why* features exist and *how* they interact, not *what* they do — agents discover functionality through exploration. |
