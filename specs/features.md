# Features

## Core Capabilities

| Feature | What it does | Status |
|---------|-------------|--------|
| Project scanning | AI examines existing docs, config files, code structure, and git history to determine what documentation exists and what's missing | Shipped |
| Manifest tracking | Persistent YAML file tracks which artifacts exist, their quality scores, backlog items, and session log | Shipped |
| Rubric-based evaluation | Binary YES/NO weighted checklists score artifacts on a 1-5 scale. 13 built-in artifact types, 4 core + 9 specialised | Shipped |
| Gap analysis | Compares current artifact state against rubric, identifies failing items sorted by weight, presents prioritised improvement options | Shipped |
| Artifact generation | Produces documentation following spec-writing principles (no code restatement, front-load non-obvious, 1-3KB target) | Shipped |
| Self-evaluation | After generating/improving, re-scores against rubric and reports whether the score actually improved | Shipped |
| Staleness detection | Checks git log (or file mtime fallback) to flag artifacts where the code has changed since last evaluation | Shipped |
| Session prioritisation | When user doesn't specify what to work on, applies priority order: broken > missing > low-scoring > stale > backlog | Shipped |
| AI config pointer | Appends a 3-line discovery section to CLAUDE.md/.cursorrules with delimiters, without overwriting existing content | Shipped |
| Protocol caching | Copies PROTOCOL.md and RUBRICS.md into .chat-spec/ on first run to pin the scoring version | Shipped |
| Custom artifact types | Users can define custom rubrics in per-artifact detail files for project-specific documentation types | Shipped (untested) |

## Non-Obvious Behaviour

- **Generation is constrained to one artifact per session.** This isn't a limitation — it's a design choice backed by research showing quality degrades when generating too much at once. The protocol enforces this even if the AI has remaining context capacity.
- **The AI must ask before acting.** Every generation or improvement requires user approval. The protocol is explicit: "propose changes before making them." This applies even on first run — the manifest proposal needs a yes.
- **Scoring uses display levels (1-5), not raw totals.** The manifest stores the mapped level, not the sum of weights. Raw totals vary by rubric (14-17 max), so levels normalise across artifact types.
- **Self-evaluation can report failure.** If generated changes don't improve the score, the protocol tells the AI to say so and ask the user for information it couldn't find in the code. This prevents silent quality degradation.
- **Staleness doesn't mean wrong.** A `stale` status means code changed since last evaluation — the docs might still be accurate. The AI must re-evaluate to determine if the artifact is actually broken or just needs a fresh score.

## Key User Workflows

**First run** (5-10 min): Paste URL → AI scans → proposes manifest → user approves → AI creates state + generates first artifact → done. User gets a scored baseline and one improved doc.

**Improvement session** (2-5 min): Say "run chat-spec" → AI reads manifest → presents options ranked by impact → user picks one → AI shows gap analysis → user approves → AI generates → score updates. Repeat across sessions.

**Targeted work**: Say "improve my API docs" → AI goes directly to that artifact, skipping the priority ranking. Same evaluate → propose → generate → re-evaluate flow.

**Status check**: Say "what's the status?" → AI reads manifest and reports scores, staleness, and top backlog items. No generation.

## Scope

**chat-spec does:**
- Track documentation quality with persistent, scored state
- Generate and improve documentation artifacts iteratively
- Work with any AI tool that can read a URL and write files
- Provide opinionated defaults (13 types, 4 core) that adapt per project

**chat-spec does not:**
- Generate or sync AI config files (CLAUDE.md, .cursorrules) beyond the 3-line pointer
- Run as a CLI, MCP server, or standalone tool
- Require installation, dependencies, or accounts
- Replace existing documentation — it builds on and improves what exists
- Do anything automatically — every action requires user approval

## Feature Interactions

- **Staleness detection feeds session prioritisation**: stale artifacts get surfaced when the user asks "what next?"
- **Rubric evaluation drives generation**: the AI targets the highest-weight failing items first to maximise score improvement per session
- **Self-evaluation validates generation**: after every generate step, the rubric is re-applied to confirm improvement before updating the manifest
- **Protocol caching enables consistent scoring**: because the rubric version is pinned, scores from session 1 and session 10 are comparable
