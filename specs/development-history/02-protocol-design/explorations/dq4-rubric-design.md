# DQ4: Rubric Design

## The core question

Rubrics both score and guide. They need to be precise enough for reproducible AI evaluation, comprehensive enough to capture quality, and compact enough to fit in context. What's the concrete format, and what do the rubrics for the core types look like?

## Format decisions

### Yes/No vs Yes/Partial/No

The noodling phase leaned towards binary yes/no for reproducibility. Let's stress-test this.

**Binary (yes/no):**
- Pro: Maximum reproducibility. Different AIs will agree more often on binary than on three-way. **Research confirms this:** Arize AI (2025) found binary judgments are the most stable evaluation format; numeric scores "bunch, flip, collapse" with prompt wording and model changes. Hamel Husain (hamel.dev, 2024-2025) observed across 30+ companies that domain expert pass/fail judgments correlate better with quality than granular scores.
- Pro: Simpler scoring math. Sum of weights for "yes" items.
- Con: Loses nuance. "Partially documents API endpoints" scores the same as "doesn't document them at all."

**Ternary (yes/partial/no):**
- Pro: Captures the messy middle where most real artifacts live.
- Pro: More actionable — "partial" tells the user "you started this, finish it."
- Con: "Partial" is subjective. When does "mentions components briefly" become "partially lists components"?
- Con: Scoring is uglier. Half weights? Two-thirds?

**Decision: Binary yes/no.** (Validated by Arize AI study and Hamel Husain's cross-company observations. Also supported by "Assessing Consistency and Reproducibility in the Outputs of Large Language Models" (arXiv:2503.16974) which found binary classification achieves near-perfect reproducibility across 50 independent runs.)

The nuance problem is real but solvable without `partial`. Instead of one broad checklist item, use multiple specific ones:

Instead of:
```
- "Documents API endpoints?" → partial (has some, not all)
```

Use:
```
- "Lists all API endpoints?" → no
- "Documents at least one endpoint with request/response?" → yes
```

This gives the same information (some work done, more needed) through binary items, with better reproducibility. The rubric design absorbs the nuance.

### How many items per artifact type?

Too few → coarse scores, items too broad and subjective.
Too many → expensive to evaluate, noisy, diminishing returns.

**Sweet spot analysis:**

With 5 scoring levels (1-5) and binary items, you need enough items to create meaningful gradations. With weights:

- 5-6 items: Could work but each item carries too much weight. One wrong yes/no swings the score.
- 8-10 items: Good granularity. Each item is specific. Score has enough resolution.
- 15+ items: Diminishing returns. Evaluation takes longer. Many items become "nice to have" padding.

**Decision: 8-10 checklist items per artifact type, weighted, summing to a max score that maps to levels 1-5.**

### Weight scheme

Items aren't equally important. "Lists major components" matters more than "includes a diagram."

**Decision: Weights of 1-3.**
- Weight 3: Essential. If this is missing, the artifact is fundamentally incomplete.
- Weight 2: Important. Significantly improves the artifact's usefulness.
- Weight 1: Valuable. Nice to have, distinguishes good from great.

Total max score per rubric should land around 15-20 (sum of all weights). This gives clean level boundaries:

| Level | Score range | Label | Meaning |
|-------|------------|-------|---------|
| 1 | 0-3 | Missing | Absent or placeholder only |
| 2 | 4-7 | Basic | Covers the essentials, major gaps |
| 3 | 8-11 | Solid | Usable, some gaps |
| 4 | 12-15 | Strong | Comprehensive, minor gaps |
| 5 | 16+ | Complete | Thorough, well-maintained |

The exact boundaries depend on the max score per type. The protocol normalises to a 1-5 display score.

### Handling "not applicable" items

Some checklist items don't apply to every project. "Documents authentication flow" doesn't apply to a CLI tool with no auth.

**Options:**
1. Skip N/A items and reduce the max score proportionally
2. Mark them as N/A and exclude from scoring
3. Don't have N/A — design rubrics so every item applies to every project

**Decision: Option 2 — mark as N/A, exclude from scoring.**

The detail file records `result: n/a` for items the AI determines aren't applicable. The max score adjusts. This is more accurate than forcing items to apply or pretending a project with no auth should lose points for missing auth docs.

The AI determines N/A status during evaluation based on the codebase. The user can override ("actually we do have auth, I just haven't documented it").

### Where do rubrics live?

**Options:**
1. In PROTOCOL.md — single source of truth, but makes the protocol long
2. In the manifest — per-project customisation, but bloats the manifest
3. In per-artifact detail files — only loaded when needed, but duplicated across projects
4. In a separate rubrics file — `chat-spec/rubrics/` or similar

**Decision: Built-in rubrics are defined in PROTOCOL.md. Custom rubrics live in the detail file.**

Rationale:
- Built-in rubrics are protocol-level knowledge. They change when the protocol version changes. Defining them in PROTOCOL.md means every AI reads the same rubrics.
- Custom rubrics are project-specific. They belong with the artifact they describe, in the detail file.
- This avoids a separate rubrics directory (more files to manage) and keeps the manifest thin.
- **Research support:** "Rubric Is All You Need" (ACM ICER 2025, arXiv:2503.23989) found that question-specific rubrics significantly outperform question-agnostic rubrics for LLM evaluation. Our per-artifact-type rubrics align with this finding.

The protocol document will include rubrics as a structured section. The AI reads the rubric for the artifact type it's evaluating. For a typical session (one artifact), that's one rubric — ~10 items, ~200 tokens. Cheap.

### How rubrics guide generation

Rubrics aren't just for scoring — they're the content checklist for generation. When the AI generates or improves an artifact, it uses the rubric as a guide:

> "To improve this architecture doc from level 2 to level 3, you need to address these failing items: [list]. Focus on: [highest-weight failing items first]."

The protocol instructs the AI:
1. Evaluate against the rubric first
2. Identify failing items, sorted by weight
3. Address the highest-weight failing items in the current session
4. Re-evaluate after changes to confirm improvement

This creates a natural "improvement ladder" — each session climbs the rubric.

## The universal rubric item

Every artifact type includes one critical item derived from the ETH Zurich finding:

| Item | Weight | Question |
|------|--------|----------|
| non_obvious | 2 | Contains information not inferable from code, configs, and standard conventions alone? |

An artifact that passes every other item but fails this one is producing the kind of documentation that research shows actively harms AI performance. This item ensures specs capture the *delta* — what's surprising, what would be guessed wrong, what the code doesn't express. (See DQ0 for the full research basis.)

## Draft rubrics for the 4 core types

### Architecture

What it covers: System structure, components, relationships, data flow, technology choices.

| # | Item | Weight | Question |
|---|------|--------|----------|
| 1 | components | 3 | Lists all major system components/services? |
| 2 | relationships | 3 | Describes how components communicate (APIs, events, shared state)? |
| 3 | data_flow | 2 | Shows how data moves through the system for key operations? |
| 4 | non_obvious | 2 | Documents architectural decisions, rationale, and non-obvious constraints not inferable from code? |
| 5 | boundaries | 2 | Defines clear boundaries between components (what owns what)? |
| 6 | entry_points | 1 | Identifies system entry points (user-facing, API, jobs, events)? |
| 7 | persistence | 1 | Documents data storage approach (databases, caches, file systems)? |
| 8 | external | 1 | Lists external dependencies and integrations? |
| 9 | constraints | 1 | Notes key architectural constraints or trade-offs? |
| 10 | current | 1 | Reflects the actual current state (not aspirational or outdated)? |

**Max score: 17**

Level boundaries: 1 (0-3), 2 (4-7), 3 (8-10), 4 (11-14), 5 (15-17)

### Features

What it covers: What the project does — its capabilities, user-facing functionality, behaviour.

| # | Item | Weight | Question |
|---|------|--------|----------|
| 1 | core_features | 3 | Lists all major features/capabilities of the project? |
| 2 | feature_detail | 3 | Describes what each feature does (not just names it)? |
| 3 | user_perspective | 2 | Explains features from the user's perspective (what they can do)? |
| 4 | non_obvious | 2 | Documents non-obvious feature behaviour, edge cases, and design rationale not inferable from code? |
| 5 | feature_status | 2 | Indicates status of each feature (complete, in progress, planned)? |
| 6 | interactions | 1 | Describes how features interact or depend on each other? |
| 7 | scope | 1 | Clearly distinguishes what the project does vs. doesn't do? |
| 8 | entry_flows | 1 | Describes key user workflows or paths through the features? |
| 9 | current | 1 | Reflects the actual current state of the features? |

**Max score: 16**

Level boundaries: 1 (0-3), 2 (4-6), 3 (7-10), 4 (11-13), 5 (14-16)

### Conventions

What it covers: Coding standards, patterns, naming, testing expectations, project-specific rules.

| # | Item | Weight | Question |
|---|------|--------|----------|
| 1 | code_style | 3 | Documents code style and formatting rules (or points to config)? |
| 2 | patterns | 3 | Describes key patterns used in the codebase (state management, error handling, etc.)? |
| 3 | non_obvious | 2 | Captures conventions that deviate from language/framework defaults or would be guessed wrong? |
| 4 | naming | 2 | Defines naming conventions for files, functions, variables, etc.? |
| 5 | testing | 2 | Documents testing expectations (what to test, how, coverage)? |
| 6 | file_structure | 2 | Explains file/directory organisation conventions? |
| 7 | anti_patterns | 1 | Lists things to avoid (common mistakes, discouraged patterns)? |
| 8 | examples | 1 | Includes code examples demonstrating conventions? |
| 9 | current | 1 | Reflects actual codebase practices (not aspirational)? |

**Max score: 17**

Level boundaries: 1 (0-3), 2 (4-7), 3 (8-10), 4 (11-14), 5 (15-17)

### Development Context

What it covers: Project history, key decisions, lessons learned, context that a new developer (or AI) needs.

| # | Item | Weight | Question |
|---|------|--------|----------|
| 1 | project_purpose | 3 | Explains what the project is and why it exists? |
| 2 | key_decisions | 3 | Documents major technical/architectural decisions and their rationale? |
| 3 | non_obvious | 2 | Captures gotchas, surprises, and things that look wrong but aren't? |
| 4 | setup | 2 | Describes how to set up the development environment? |
| 5 | build_run | 2 | Documents how to build, run, and test the project? |
| 6 | history | 1 | Provides relevant project history (major refactors, migrations, pivots)? |
| 7 | team_context | 1 | Notes team structure, ownership, or who to ask about what? |
| 8 | roadmap | 1 | Indicates what's planned or in-progress? |
| 9 | current | 1 | Reflects the actual current state of the project? |

**Max score: 16**

Level boundaries: 1 (0-3), 2 (4-6), 3 (7-10), 4 (11-13), 5 (14-16)

## Rubric design principles (for the protocol)

These principles apply to both built-in and custom rubrics:

1. **Items are observable.** Each item can be verified by reading the artifact. No "is the documentation helpful?" — instead "lists all major components?"
2. **Items are specific.** One thing per item. Not "comprehensive and well-organised" — split into separate items.
3. **Highest-weight items are foundational.** Weight-3 items define whether the artifact is functional at all. Weight-1 items distinguish good from great.
4. **Every rubric has a `current` item.** Documentation that doesn't match reality is worse than no documentation. This is always checked.
5. **Rubrics are stable.** Changing a rubric changes all scores. Protocol version bumps when rubrics change.

## Scoring display

The manifest stores the raw score (sum of passing item weights). The protocol maps to display levels:

```
Display score = level (1-5) based on rubric-specific boundaries
```

Each rubric defines its own level boundaries because max scores differ. The boundaries are tuned so that:
- Level 1: The artifact is essentially absent or a placeholder
- Level 2: The basics exist but major gaps remain
- Level 3: Usable — an AI or developer could work with this
- Level 4: Comprehensive — few gaps, well-structured
- Level 5: Complete — thorough, current, and well-maintained

The AI reports both: "Architecture: 3/5 (score: 10/17)"

## Summary of decisions

| Question | Decision |
|----------|----------|
| Binary or ternary? | Binary (yes/no) — use specific items instead of "partial" |
| Items per type? | 8-10 per artifact type |
| Weight scheme? | 1-3 (essential/important/valuable) |
| N/A handling? | Mark as N/A, exclude from max score |
| Where do rubrics live? | Built-in: PROTOCOL.md. Custom: per-artifact detail file |
| How do rubrics guide generation? | Failing items sorted by weight = improvement priority |
| Level mapping? | Per-rubric boundaries, tuned so level 3 = "usable" |
