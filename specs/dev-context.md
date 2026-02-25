# Dev Context

## Why This Exists

chat-spec solves the gap between "AI tools can generate code" and "AI tools generate *good* code for *your* project." The core bet: iterative, scored documentation tracking produces better AI context than one-shot generation or manual maintenance. No existing tool does this -- the landscape research (specs/landscape-research.md) confirmed the scored-manifest-plus-iterative-improvement approach is novel.

## Key Decisions

| Decision | Choice | Why not the alternative |
|----------|--------|------------------------|
| Protocol, not a tool | Markdown file an AI reads | Zero install friction. Works with any AI tool. Avoids the "install our CLI" barrier that kills adoption for solo devs. |
| One artifact per session | Hard limit in protocol | AI context windows fill up. Users lose patience. Research (ETH Zurich) shows quality degrades when generating too much at once. |
| YAML manifest, not JSON | `.chat-spec/manifest.yaml` | AIs read YAML more reliably than JSON for config-like data. Humans can edit it. Explored in DQ3. |
| Checklist rubrics (YES/NO weighted) | Not Likert scales or LLM-as-judge | Binary scoring is most reproducible across different AI models. Research: "Rubric Is All You Need" (ACM ICER 2025), Hamel Husain's pass/fail findings. |
| Spec-writing rules ban restating code | Section 1 of PROTOCOL.md | ETH Zurich found LLM-generated docs that restate what's in code *hurt* AI performance and increase inference cost 20%+. This is the single most important quality principle. |
| Built-in catalogue of 13 types, 4 core required | Not a blank slate | Opinionated defaults lower the "what do I write?" barrier. Extensible via custom rubrics for projects that need it. |
| Thin pointer in AI config, not full sync | Append-only to CLAUDE.md/.cursorrules | Avoids the cross-tool sync problem entirely. Each tool reads its own config; chat-spec just adds a 3-line pointer. |

## Development Phases

Three phases, each in `specs/development-history/`:

1. **Noodling** (done) -- 5 idea sketches, 10 decision points, 8 explorations. Settled the "what is this thing?" question.
2. **Protocol design** (done) -- 9 design questions (DQ0-DQ8) worked through sequentially. Research-grounded: 7+ academic papers and industry sources cited.
3. **Build** (current) -- PROTOCOL.md, RUBRICS.md, README.md written. Blank-slate testing with Haiku passed (3/3). Dogfooding and final polish remain.

## Gotchas

- **This is not a git repo yet.** The project root has no `.git/`. Staleness detection (section 2 of PROTOCOL.md) falls back to file modification times instead of git log.
- **The "product" is PROTOCOL.md, not code.** There is no src/, no tests/, no build system. Quality is measured by whether AI models follow the protocol correctly, not by unit tests.
- **specs/ is overloaded.** `specs/` serves two purposes: (1) it holds chat-spec's own documentation (decisions, landscape research, development history) and (2) it is the default artifact directory name that the protocol tells *other projects* to use. These are separate concerns that happen to share a name.
- **Development history is intentionally verbose.** The `specs/development-history/` tree has ~30 files from the noodling and design phases. These are kept for provenance, not for regular reading. The decisions.md file is the consolidated view.

## How to "Build" and Test

There is no build step. The deliverables are three markdown files at the project root:

| File | Purpose | How to test |
|------|---------|-------------|
| PROTOCOL.md | The protocol AI tools follow | Hand it to an AI model with zero context and a test project. Does it follow the steps correctly? |
| RUBRICS.md | Scoring rubrics for 13 artifact types | Score a known artifact and check for reproducibility across models. |
| README.md | Human landing page | Does a developer understand what to do in under 60 seconds? |

Blank-slate testing (step 4 in the build plan) is the closest thing to a test suite. Three scenarios were run with Haiku and all passed, with one fix applied (score scale clarification in the manifest schema).

## Roadmap

Per `specs/development-history/03-build/plan.md`, remaining work:

1. Dogfood chat-spec on itself (this run is that)
2. Dogfood on other real projects
3. Final polish (token budget audit, YAML schema validation, cross-reference check)
