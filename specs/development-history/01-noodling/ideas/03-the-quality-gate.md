# Idea 3: The Quality Gate

## Core concept

Chat-spec is primarily a scoring and reporting tool. It doesn't write your specs — you do (or your AI does, during normal development). Chat-spec's job is to measure how AI-ready your project is, track that over time, and tell you where the gaps are. Think of it as a linter for AI context quality.

## How it works

### Separation of concerns

Most AI coding tools already generate docs and specs. The problem isn't generation — it's knowing whether what you have is any good, and whether it's getting better or worse. Chat-spec focuses exclusively on this evaluation layer.

### The scorecard

Instead of a manifest that tracks individual artifacts, chat-spec produces a **scorecard** — a holistic assessment of the project's AI-readiness across dimensions:

```yaml
scorecard:
  overall: 62/100
  last_assessed: 2026-02-25

  dimensions:
    structural_clarity:
      score: 78
      weight: 0.2
      summary: "Clear directory structure, good separation of concerns"
      evidence:
        - "Consistent /src /tests /docs layout"
        - "Feature-based module organisation"

    feature_documentation:
      score: 35
      weight: 0.2
      summary: "Features exist but aren't documented as discrete capabilities"
      gaps:
        - "No feature manifest"
        - "README lists features but doesn't link to implementation"

    architecture_documentation:
      score: 70
      weight: 0.2
      summary: "Architecture doc exists and is mostly current"
      gaps:
        - "New auth service not reflected"

    conventions_and_standards:
      score: 55
      weight: 0.15
      summary: "Some conventions documented, many implicit"
      gaps:
        - "No error handling conventions"
        - "Testing expectations not written down"

    context_accessibility:
      score: 72
      weight: 0.15
      summary: "Good CLAUDE.md, no .cursorrules"
      gaps:
        - "Only configured for one AI tool"

    freshness:
      score: 50
      weight: 0.1
      summary: "Docs haven't kept pace with recent changes"
      evidence:
        - "12 commits since last doc update"
        - "New API endpoints not reflected in specs"
```

### The workflow

1. **Run chat-spec** — it scans your project and produces the scorecard
2. **Review the scores** — see where you're strong and weak
3. **Act on gaps** — either fix them yourself or ask your AI assistant to help
4. **Run again** — see your score improve

Chat-spec doesn't write the fixes. It identifies them. This keeps it simple and avoids the "AI writing docs about AI context" recursion problem.

### Dimensions are configurable

The default dimensions cover the common cases, but you can add project-specific ones:

```yaml
custom_dimensions:
  api_documentation:
    weight: 0.2
    rubric: "Are all public endpoints documented with request/response examples?"
  deployment_runbook:
    weight: 0.1
    rubric: "Can someone deploy this project from the docs alone?"
```

### Trend tracking

Each run appends to a history file. Over time you can see:
- Overall score trending up (or down)
- Which dimensions improved
- Which are consistently neglected

This is the "iterative improvement" story — not that chat-spec improves your docs, but that it shows you whether your docs are improving.

### CI integration

Because chat-spec is a scorer, it fits naturally into CI:
- Run on every PR: "This PR reduces AI-readiness by 5 points (new module with no docs)"
- Minimum score threshold: "Block merge if score drops below 50"
- Dashboard: track team score over time

## What I like about this

- Clear, focused purpose — it measures, it doesn't generate
- The scorecard is intuitive and actionable
- Trend tracking gives a real "is this getting better?" signal
- CI integration is a natural fit
- Avoids the problem of AI-generated docs being low quality (ETH Zurich finding)
- Weights let projects express what matters to them

## Assumptions baked in

- Evaluation is more valuable than generation
- A numeric score is meaningful and motivating (not reductive)
- AI can score reliably against rubrics (same assumption as other ideas)
- Projects want to track scores over time
- CI integration is desirable (adds complexity, assumes CI exists)
- The separation from generation is a feature, not a limitation
- Dimensions and weights can be standardised enough to be useful defaults
- Users will act on gap reports (the tool can't make them)
- The scoring can be deterministic enough across runs to show real trends (not just AI scoring variance)
