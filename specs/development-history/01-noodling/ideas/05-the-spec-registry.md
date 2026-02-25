# Idea 5: The Spec Registry

## Core concept

Chat-spec is two things: a local tool that manages your project's specs, and a shared registry of rubrics, templates, and scoring baselines. The local tool is simple — the value is in the registry, which captures collective knowledge about what "good AI context" looks like for different types of projects.

## How it works

### The registry

A public, community-maintained collection of:

- **Rubrics** — scoring criteria for different artifact types. Not just one rubric for "architecture" but variants: "architecture for a monolith", "architecture for microservices", "architecture for a CLI tool"
- **Templates** — starting points for each artifact type, tuned to project type
- **Baselines** — "a typical well-documented Express API scores around 70 on our scale. Here's where the points come from."
- **Profiles** — bundled sets of rubrics and templates for common project types: "React SPA", "Python API", "CLI tool", "monorepo"

Think of it like ESLint's shared configs, but for documentation quality.

### The local tool

On your machine, chat-spec:

1. Pulls a profile from the registry (or you configure one)
2. Applies the rubrics to your project
3. Scores you against the baseline for your project type
4. Proposes improvements using the templates as starting points

### Why this matters

The hardest part of Idea 1-4 is: who decides what "good" looks like? If every project defines its own rubrics, the scores are meaningless. If chat-spec ships with fixed rubrics, they'll be wrong for many projects.

The registry solves this: rubrics are community-maintained and project-type-aware. Your "architecture doc at maturity 4" means the same thing as someone else's, at least within the same project profile.

### Community dynamics

- **Publish your rubrics**: if you've refined a rubric that works well, share it
- **Rate rubrics**: upvote ones that produce useful scores
- **Fork and customise**: start from a community rubric, adjust for your needs
- **Benchmark**: "how does my project compare to similar ones?"

### The scoring story

With baselines, chat-spec can say not just "your architecture doc is 3/5" but "your architecture doc is 3/5, and similar projects typically reach 4/5 within their first month of using chat-spec."

### How it handles iteration

Same as Idea 1 — manifest tracks state, each run evaluates and proposes. But the proposals are informed by what's worked for similar projects: "Projects like yours that improved their API docs from 2 to 4 typically started by documenting the auth flow first."

## What I like about this

- Solves the "who defines quality" problem
- Creates a network effect — the more people use it, the better the rubrics get
- Baselines give scores meaning beyond the individual project
- Profiles reduce setup friction — "I'm building a React app" gets you started immediately
- Community dynamics could make this self-sustaining

## Assumptions baked in

- A registry/community model is viable (needs critical mass)
- Rubrics can be meaningfully standardised across projects of the same type
- Projects can be usefully categorised into types/profiles
- Baselines are meaningful (not just averages that mislead)
- Users want comparative scoring (not just absolute)
- Community will contribute and maintain rubrics (not just consume)
- A public registry is acceptable (some orgs won't want to publish or pull from it)
- The registry infrastructure is worth building and maintaining
- This isn't premature — should it be a V2 concern, not a V1 concern?
- Network effects will actually materialise
