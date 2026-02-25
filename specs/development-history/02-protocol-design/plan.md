# Plan: Protocol Design

## Context

The noodling phase (01-noodling/) decided: **chat-spec is a protocol, distributed as a GitHub repo, executed by whatever AI the user already has.** The README is the landing page, PROTOCOL.md is the product.

Key positions from noodling (see `01-noodling/plan.md` for full list):
- Protocol first, MCP/CLI later if needed
- Both generates and evaluates, with self-evaluation check and human-in-the-loop
- Checklist-based rubrics (yes/no weighted questions)
- Built-in artifact catalogue (~13 types), extensible
- Primary user: solo developer. Secondary: dev encountering a new codebase
- Thin pointer in CLAUDE.md, not full config sync
- Opinionated defaults, overridable everything

## Design questions

Work through these in order. Each one produces an exploration in `explorations/`.

### DQ0: Spec-writing principles (research-grounded)

*Added during execution.* Foundational principles for how to write documentation that LLMs actually benefit from, grounded in published research. All other DQs reference this.

Key sources:
- ETH Zurich (2026): LLM-generated docs that restate code hurt performance
- "Lost in the Middle" (TACL 2024): LLMs miss information in the middle of long contexts
- Anthropic context engineering (2025): "Intelligence is not the bottleneck, context is"
- Arize AI (2025): Binary evaluations are most reliable
- Hamel Husain (2024-2025): Pass/fail > granular scores across 30+ companies
- "Rubric Is All You Need" (ACM ICER 2025): Question-specific rubrics outperform generic ones
- Cursor rules empirical study (MSR 2026): First large-scale analysis of developer AI config files

### DQ1: Session model

AI tools can't do everything at once. Users might not want them to. How does a single invocation of chat-spec scope its work?

- What's a "session"? One conversation? One artifact? One improvement?
- How does the protocol tell the AI to scope its work?
- User gives a specific request ("fix my architecture doc") vs vague ("improve my specs") vs nothing — what happens in each case?
- How does the AI decide what to work on when the user doesn't specify?
- How does a session end? What gets saved?

### DQ2: State and memory

Between sessions, the AI forgets everything. Where does project state live?

- What state needs to persist? (scores, problems found, work-in-progress, backlog)
- Does it all go in the manifest? Or does that make it too big?
- A directory full of problem files is also bad — too many small files cluttering things
- Work-in-progress: an artifact started but not finished — where's that state?
- How big can state get before it's a problem for AI context windows?
- The AI on session N needs to pick up where session N-1 left off — what's the minimum it needs to read?

### DQ3: Manifest design

The manifest is the heart of the state model. Concrete schema design.

- Fields, types, nesting
- Per-artifact state: path, score, checklist results, notes, backlog?
- Project-level state: last run, protocol version, project type?
- Size budget — must fit comfortably in any AI's context window
- Format: YAML vs JSON vs something else, with "AI readability" as primary lens

*Depends on: DQ1, DQ2*

### DQ4: Rubric design

Rubrics score AND guide. They need to be precise, comprehensive, and compact.

- Concrete rubric format (revisit checklist idea with more rigour)
- How many items per artifact type? Sweet spot?
- Yes/no vs yes/partial/no?
- Handling "not applicable" items
- How rubrics guide generation ("to reach level 3, answer these questions...")
- Where do rubrics live? Protocol doc? Manifest? Separate files?
- Draft rubrics for the 4 core types: architecture, features, conventions, dev context

*Depends on: DQ3*

### DQ5: First-run experience

User pastes the URL, AI reads the protocol. Project has nothing. Now what?

- What does the AI do first?
- How does it detect what already exists? (CLAUDE.md, .cursorrules, docs/, README)
- Does it score what's there or skip to building?
- How much in one session? (Manifest + one artifact? Just manifest?)
- The "wow moment" — what makes the user come back?
- Creating the thin pointer in CLAUDE.md — how, without overwriting user's existing content?
- Guiding the AI to write the right config for the user's tool (CLAUDE.md vs .cursorrules vs AGENTS.md)

*Depends on: DQ3*

### DQ6: Subsequent-run experience

Session 2+ is where iterative value lives.

- How does the AI re-orient? (Read manifest, read backlog, assess state)
- "Run chat-spec" with no specifics — what happens?
- "Improve my API docs" — what happens?
- "What's the status?" — what happens?
- Re-scoring: everything? Only stale? Only what user asks about?
- How does the AI decide what to propose? (Biggest gap? Most stale? Easiest win?)
- Coexisting with user's existing CLAUDE.md content

*Depends on: DQ5*

### DQ7: The protocol document

The actual product. PROTOCOL.md — precise enough for AI consistency, not so long it fills context.

- Structure and sections
- Tone (instructional? mechanical?)
- Length budget
- Examples (AIs follow examples well)
- Robustness to interpretation variance across different AI tools
- First draft or detailed outline

*Depends on: DQ1–DQ6*

### DQ8: The README

The landing page. Makes humans want to try it, gives them the one-liner.

- Structure and content
- The "paste this" quickstart — exact wording
- How much protocol to include vs link to
- First draft

*Depends on: DQ7*

## Sequence

```
DQ1 (session model) ──┐
                       ├──> DQ3 (manifest) ──> DQ4 (rubrics) ──┐
DQ2 (state/memory) ───┘                                        │
                                                                ├──> DQ7 (protocol) ──> DQ8 (README)
DQ5 (first run) ───────────────────────────────────────────────┘
DQ6 (subsequent runs) ─────────────────────────────────────────┘
```

DQ1 and DQ2 can be explored in parallel. DQ5 and DQ6 can start once DQ3 exists. DQ7 synthesises everything. DQ8 is last.

## Status

| DQ | Topic | Status |
|----|-------|--------|
| 0 | Spec-writing principles (research-grounded) | done |
| 1 | Session model | done (research citations added) |
| 2 | State and memory | done (research citations added) |
| 3 | Manifest design | done |
| 4 | Rubric design | done (research citations added, non_obvious rubric item added) |
| 5 | First-run experience | done (ETH Zurich warning integrated) |
| 6 | Subsequent runs | done |
| 7 | Protocol document | done (spec-writing best practices section added) |
| 8 | README | done |
