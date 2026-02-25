# Conventions

## Spec-Writing Style (the most important convention)

Every statement must earn its place. Before writing a line, ask: "Would an AI infer this correctly from the code alone?" If yes, delete it. This is grounded in ETH Zurich research showing AI-generated docs that restate code hurt performance and increase inference cost 20%+.

Concretely:
- Front-load the non-obvious — start with what would be guessed wrong
- No "Overview" boilerplate, no "This document describes..." preamble
- Specific headings: "API Auth (JWT with Redis sessions)" not "Authentication"
- 1-3KB per artifact — if longer, split or use index-plus-detail
- Lists and tables over prose for structured information
- No empty sections ("## Security\n\nTBD") — write it or omit it

## Markdown Style

- ATX headings (`#`, `##`) not Setext (underlines)
- One blank line before headings, none after
- Tables for structured comparisons; lists for sequences or catalogues
- Fenced code blocks with language identifier (```yaml, ```markdown)
- No HTML in markdown files except the `<!-- chat-spec:start/end -->` delimiters in AI config pointers

## YAML Conventions

Manifest and detail files follow strict patterns:

| Rule | Example | Why |
|------|---------|-----|
| Keys: lowercase, hyphens for spaces | `dev-context`, not `devContext` | Readability in YAML; consistent with YAML ecosystem norms |
| Scores: 1-5 display level, never raw total | `score: 3` | Raw totals vary by rubric (14-17 max); levels normalise |
| Status values: exactly 5 | `current`, `stale`, `missing`, `wip`, `draft` | Closed enum — AIs shouldn't invent new statuses |
| Dates: ISO 8601 date only | `2026-02-25`, not datetime | Timestamps add precision nobody needs |
| Strings: quoted when containing special chars | `name: "my-project"` | Prevents YAML parsing surprises |
| Checklist results: `yes`, `no`, or `na` | `result: yes` | Lowercase, no quotes — these are YAML booleans treated as strings |
| Backlog: capped at 10 in manifest | Full list in detail files | Keeps manifest small enough for a single AI read |
| Log: capped at 5, most recent first | Oldest entries drop off | Same reason — manifest stays compact |

## Naming Conventions

| Thing | Convention | Example |
|-------|-----------|---------|
| Artifact types (keys) | lowercase, hyphens | `dev-context`, `api-docs`, `design-system` |
| Artifact files | key + `.md` | `specs/dev-context.md` |
| Detail files | key + `.yaml` | `.chat-spec/artifacts/dev-context.yaml` |
| Rubric item IDs | lowercase, underscores | `core_features`, `data_flow`, `non_obvious` |
| Checklist item IDs | Match rubric item IDs exactly | `- id: core_features` references the rubric |
| Protocol sections | Numbered, imperative | "3. First Run", "4. Subsequent Runs" |

**Non-obvious:** Artifact keys use hyphens (`dev-context`) but rubric item IDs use underscores (`non_obvious`). This is intentional — keys appear in YAML map keys where hyphens are natural, while item IDs appear in checklist references where underscores read better as identifiers.

## File Organisation

```
project-root/
  PROTOCOL.md          # The product (distributed)
  RUBRICS.md           # Scoring criteria (distributed)
  README.md            # Human landing page (distributed)
  CLAUDE.md            # AI config with chat-spec pointer
  .chat-spec/          # Tracking state (committed to git)
    manifest.yaml
    protocol.md        # Cached copy — pinned version
    rubrics.md         # Cached copy — pinned version
    artifacts/         # Per-artifact detail files
  specs/               # Generated artifacts + project docs
    architecture.md
    features.md
    ...
  specs/development-history/  # Design provenance (verbose, for reference only)
  research/            # Landscape research and tool analyses
  inbox/               # Raw ideas before processing
```

**Non-obvious:** `specs/` is overloaded in this repo. It holds both chat-spec's own project documentation (decisions, development history) AND it's the default `artifact_directory` name the protocol tells other projects to use. In other projects, `specs/` would only contain generated artifacts.

## Testing Approach

There is no test suite. Quality is validated through:

| Method | What it validates |
|--------|-------------------|
| Blank-slate testing | Hand the protocol to an AI with zero prior context and a test project. Does it follow the steps correctly? 3 scenarios passed with Haiku. |
| Dogfooding | Run chat-spec on itself. This session is part of that. |
| Dogfooding on other projects | Run on real codebases to validate the protocol generalises. Deferred to user. |
| Cross-model reproducibility | Score the same artifact with different AI models. Scores should be within 1 level. Not yet formally tested. |

## Anti-Patterns

- **Don't restate code/config in specs.** The #1 quality killer. If `package.json` says it, don't repeat it.
- **Don't generate multiple artifacts per session.** Even if you have context left. Quality degrades.
- **Don't invent answers.** If the rubric needs information you can't find, ask the user — don't fabricate.
- **Don't score on Likert scales.** Binary YES/NO only. "Partially met" is NO.
- **Don't put full backlogs in the manifest.** Top 10 in manifest, rest in detail files.
- **Don't overwrite AI config files.** Read first, then append inside delimiters.
- **Don't skip the user approval step.** Every generation needs explicit consent, even if the improvement seems obvious.
