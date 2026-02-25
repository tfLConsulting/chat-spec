# chat-spec Protocol v0.1

You are following the chat-spec protocol. Your job: evaluate this project's documentation against rubrics, identify gaps, and generate improvements — one focused action per session, always with user approval.

## Working Principles

**Persist as you go.** Context windows are finite; disk is not. After any research-heavy phase (scanning, deep code exploration, artifact drafting), write your findings to a working file before moving on. If your context is lost, your work survives.

Use `.chat-spec/working/` for transient research notes. Clean up working files after each session.

## Quick Reference

Every session:

1. Look for `.chat-spec/manifest.yaml`
2. If it doesn't exist → first run (section 3)
3. If it exists → subsequent run (section 4)
4. After any changes → update the manifest
5. Before ending → summarise what was done and what's next

## 1. Writing Effective Specs

Before evaluating or generating anything, understand what makes documentation useful to AI tools. Research shows that LLM-generated documentation which restates what's already in the code **actively hurts AI performance** and increases cost.

**Rule: every statement in a spec must earn its place.** For each line, ask: "Would an AI infer this correctly from the code alone?" If yes, delete it.

### What to write

- Decisions and their rationale — the code shows *what*, specs explain *why*
- Gotchas and non-obvious behaviour — things that cause bugs when assumed wrong
- Conventions that deviate from defaults — "we use snake_case in TypeScript because the API contract requires it"
- Relationships not visible in imports — runtime dependencies, event-driven connections, shared state through external systems
- Things that look like mistakes but aren't — "the circular dependency is intentional, see ADR-003"

### What NOT to write

- What the framework/language is or how it works — the AI already knows
- Standard patterns visible in the code — MVC, REST, CRUD
- What config files say — `package.json`, `Dockerfile`, `tsconfig.json` are already readable
- Directory structures the AI can see with `ls`
- Preamble ("This document describes...") — just start with the content

### Structure rules

- Front-load the non-obvious. Start specs with what would be guessed wrong, not "Overview" boilerplate
- Use specific headings: "API Auth (JWT with Redis sessions)" not "Authentication"
- Keep specs to 1-3KB. If longer, split or use an index-plus-detail pattern
- Lists and tables over prose for structured information
- No empty sections ("## Security\n\nTBD") — either write it or omit it

### The blank-slate test

After generating a spec, self-check: "What would a new AI learn from this that it couldn't learn from the code?" If the answer is thin, the spec needs more unique information.

## 2. Evaluation

### How to score

Read the artifact file. For each rubric item, score YES or NO:

1. Read the rubric for this artifact type (section 7 for core types, RUBRICS.md for others)
2. Score each item: YES (clearly met) or NO (not met or unclear)
3. If an item doesn't apply to this project, mark it N/A and reduce the max score
4. Calculate total: sum of weights for YES items
5. Map to level 1-5 using the rubric's level boundaries
6. Update the manifest: `score`, `last_evaluated`, `status`
7. Update the detail file: per-item results with notes on failing items

### Staleness detection

Check if code has changed since last evaluation:

- **Git (preferred):** `git log --since="<last_evaluated>" --name-only` — if relevant files changed, mark artifact as `stale`
- **Fallback:** Compare file modification times against `last_evaluated`

## 3. First Run

No `.chat-spec/` directory exists. Follow these steps:

### Step 1: Scan the project

Look for existing documentation and context:
- AI config files: `CLAUDE.md`, `.cursorrules`, `.cursor/rules`, `AGENTS.md`
- Documentation: `README.md`, `docs/`, `specs/`
- Project metadata: `package.json`, `Cargo.toml`, `pyproject.toml`, `go.mod`
- Code structure: `src/`, `lib/`, `app/`, `tests/`
- Git history: repo age, activity level

**Write scan findings to disk immediately.** Create `.chat-spec/working/scan.md` with:
- Project summary (stack, structure, notable patterns)
- Existing documentation found and where
- Initial observations about documentation gaps

This file is your working memory. It ensures your scan survives context limits and can be referenced during artifact generation later.

### Step 2: Propose a manifest

Based on the scan, propose which artifacts to track. Do NOT propose all 13 types — only what this project needs. Always include the 4 core types (architecture, features, conventions, dev-context). Add specialised types only if relevant (e.g., api-docs if there are API endpoints).

Present the proposal to the user with a one-line rationale per artifact:

> "I'd track these for your project:
> - **Architecture** — Next.js app with Postgres, no architecture doc exists
> - **Features** — README has a features section, but it's incomplete
> - **Conventions** — your .cursorrules has some patterns I can build on
> - **Dev context** — nothing exists yet
> - **API docs** — you have 12 API routes, no documentation
>
> Want me to set this up?"

### Step 3: Score what exists

For artifacts where content already exists (even partial), evaluate against the rubric. Show the baseline:

> | Artifact | Score | Source |
> |----------|-------|--------|
> | Architecture | 1/5 | Nothing found |
> | Features | 2/5 | README features section |
> | Conventions | 2/5 | .cursorrules |

### Step 4: Create state

On user approval, create:

```
.chat-spec/
  manifest.yaml
  protocol.md          # cached copy of this protocol
  rubrics.md           # cached copy of rubrics
  working/             # transient research notes (safe to .gitignore)
  artifacts/
```

Cache this protocol file and RUBRICS.md into `.chat-spec/`. This pins the version and removes the need to fetch from the network on subsequent runs. Do NOT cache if the protocol is already local (i.e., you are running chat-spec on the chat-spec repo itself).

And the artifact directory if it doesn't exist (default: `specs/`).

### Step 5: Generate one artifact

Pick the highest-impact artifact to generate first:
1. Prefer artifacts that already have partial content to build on
2. Prefer artifacts where the codebase reveals the most (architecture is often good)
3. Prefer whatever the user asked about

**Write research notes as you explore.** As you read code for the artifact, write findings to `.chat-spec/working/<artifact-key>.md` — code references, key patterns, non-obvious details. Then compose the artifact from your notes, not from context alone. Delete the working file after the artifact is complete and scored.

Generate it following the spec-writing rules in section 1. Evaluate it against the rubric. Update the manifest.

Do NOT generate multiple artifacts. One per session.

### Step 6: Create the AI config pointer

Append to the user's AI config file (`CLAUDE.md`, `.cursorrules`, etc.). Do NOT overwrite existing content. Show the diff before writing.

```markdown
<!-- chat-spec:start -->
## Specs
This project uses chat-spec for documentation quality tracking.
See .chat-spec/manifest.yaml for current state.
When asked to work on specs/documentation, follow the protocol cached at .chat-spec/protocol.md
<!-- chat-spec:end -->
```

If no AI config file exists, create a minimal one with just the pointer.

The pointer references the local cached copy, not the remote URL. This ensures the AI reads the same protocol version that scored the artifacts.

## 4. Subsequent Runs

`.chat-spec/manifest.yaml` exists. Follow these steps:

### Step 1: Read the manifest and cached protocol

Read `.chat-spec/manifest.yaml`. This tells you: what artifacts exist, their scores, what's in the backlog, and what happened recently. Do NOT read every detail file or artifact — just the manifest.

If `.chat-spec/protocol.md` and `.chat-spec/rubrics.md` exist, use them — they are the cached protocol and rubrics for this project. Do not re-fetch from the network.

### Step 2: Detect staleness

Check for code changes since last evaluation (see section 2). Update status to `stale` for any affected artifacts.

### Step 3: Decide what to do

If the user has a specific request ("improve my API docs"), go to that artifact.

If the user is vague ("improve my specs") or unprompted ("run chat-spec"), apply this priority order:

1. **Broken:** An artifact marked `current` but code changes have made it incorrect
2. **Missing:** An artifact in the manifest with status `missing`
3. **Low-scoring:** The artifact with the lowest score
4. **Stale:** The artifact not evaluated for the longest time
5. **Backlog:** The highest-priority backlog item

Tie-breaker: prefer core types (architecture, features, conventions, dev-context) over specialised.

**Always present options to the user before acting:**

> "Your project scores 2.6/5. I'd suggest:
> - **Architecture** is stale — code changed since last eval
> - **API docs** are missing (1/5)
> - **Features** could improve (2/5) — missing 3 key features
>
> What would you like to focus on?"

### Step 4: Execute

For the chosen artifact:
1. Read the detail file (`.chat-spec/artifacts/<key>.yaml`) for checklist state
2. Read the artifact file itself
3. Evaluate against the rubric
4. Present the gap analysis (failing items sorted by weight)
5. On user approval, research the codebase and **write findings to `.chat-spec/working/<artifact-key>.md`** as you explore — this protects your research from context loss during long sessions
6. Generate improvements from your research notes
7. Re-evaluate to confirm improvement
8. Update manifest and detail file
9. Delete the working file

### Step 5: Clean exit

Before ending any session:
1. Update the manifest with new scores, backlog items, timestamps
2. Add a log entry: date + what was done + result
3. Summarise what was done and what's next
4. If work is incomplete, note it so the next session can pick up

## 5. State Management

### Directory structure

```
.chat-spec/
  manifest.yaml              # always read first
  protocol.md                # cached protocol (created on first run)
  rubrics.md                 # cached rubrics (created on first run)
  working/                   # transient research notes
    scan.md                  # project scan findings (first run)
    architecture.md          # research notes during generation
  artifacts/                  # per-artifact detail files
    architecture.yaml
    features.yaml
    ...
```

The `working/` directory holds transient files used during research. These are not scored or tracked — they exist to protect against context window limits. Clean them up after each session.

Recommend committing `.chat-spec/` to version control (consider adding `.chat-spec/working/` to `.gitignore`). The cached protocol and rubrics ensure consistent scoring across sessions and team members.

### Manifest schema

```yaml
version: 1

project:
  name: "my-project"
  protocol_version: "0.1"
  created: 2025-02-18

config:
  artifact_directory: "specs"
  auto_evaluate: true

artifacts:
  architecture:
    path: "specs/architecture.md"
    score: 3
    max_score: 5
    status: current          # current | stale | missing | wip | draft
    last_evaluated: 2025-02-20
  features:
    path: "specs/features.md"
    score: 2
    max_score: 5
    status: stale
    last_evaluated: 2025-02-15

backlog:
  - artifact: architecture
    item: "Add data flow diagram"
    priority: high
  - artifact: features
    item: "Document search feature"
    priority: medium

log:
  - "2025-02-20: Evaluated architecture (3/5). Added backlog items."
  - "2025-02-18: Initial scan. Created manifest with 5 artifacts."
```

**Rules:**
- `score` is always 1-5 (the display level from the rubric's level boundaries), NOT the raw weight total. 0 means unscored/missing.
- `max_score` is always 5
- Backlog capped at ~10 items in manifest (full list in detail files)
- Log capped at 5 entries (most recent first)
- Artifact keys: lowercase, hyphens for spaces
- Paths: relative to project root

### Per-artifact detail files

```yaml
# .chat-spec/artifacts/architecture.yaml
artifact: architecture
type: architecture
last_evaluated: 2025-02-20

checklist:
  - id: components
    result: yes
  - id: relationships
    result: no
    note: "Only API gateway mentioned, missing queue and cache"
  - id: data_flow
    result: no

backlog:
  - item: "Add data flow diagram"
    priority: high
    found: 2025-02-20
```

Checklist items reference rubric item IDs. Questions and weights are defined in the rubric (this file or RUBRICS.md), not duplicated here.

## 6. Generation

### Rules

1. **One artifact per session.** Do not generate or substantially improve multiple artifacts in one session.
2. **User approval required.** Always propose changes before making them. Show what you plan to do and the expected score impact.
3. **Follow the spec-writing principles** (section 1). Do not restate what the code already expresses.
4. **Target the highest-weight failing rubric items first.** This maximises score improvement per session.
5. **Ask questions when you lack information.** If the rubric requires information you can't infer from code (e.g., "why was this architecture chosen?"), ask the user rather than inventing an answer.

### Self-evaluation

After generating or improving an artifact:

1. Re-evaluate against the rubric
2. Compare the new score to the previous score
3. If the score didn't improve, tell the user: "The changes I made didn't improve the score. The artifact may need information I can't find in the codebase — can you help with [specific questions]?"

Do NOT commit changes that don't improve the score unless the user explicitly approves.

## 7. Rubrics

Rubrics for all 13 built-in types are defined in RUBRICS.md. The four core rubrics are included here for convenience.

Scoring: each item is YES (weight counts) or NO (0). Sum YES weights. Map total to level 1-5 using the boundaries below. Items marked N/A reduce the max score.

### Architecture

System structure, components, relationships, data flow.

| Item | W | Question |
|------|---|----------|
| components | 3 | Lists all major system components/services? |
| relationships | 3 | Describes how components communicate? |
| data_flow | 2 | Shows how data moves through the system? |
| non_obvious | 2 | Documents decisions, rationale, and constraints not inferable from code? |
| boundaries | 2 | Defines clear boundaries between components? |
| entry_points | 1 | Identifies system entry points? |
| persistence | 1 | Documents data storage approach? |
| external | 1 | Lists external dependencies? |
| constraints | 1 | Notes architectural constraints or trade-offs? |
| current | 1 | Reflects the actual current state? |

Max: 17. Levels: 1 (0-3), 2 (4-7), 3 (8-10), 4 (11-14), 5 (15-17)

### Features

What the project does — capabilities, user-facing functionality.

| Item | W | Question |
|------|---|----------|
| core_features | 3 | Lists all major features/capabilities? |
| feature_detail | 3 | Describes what each feature does? |
| user_perspective | 2 | Explains features from the user's perspective? |
| non_obvious | 2 | Documents non-obvious behaviour and design rationale not inferable from code? |
| feature_status | 2 | Indicates status of each feature? |
| interactions | 1 | Describes how features interact? |
| scope | 1 | Distinguishes what the project does vs. doesn't? |
| entry_flows | 1 | Describes key user workflows? |
| current | 1 | Reflects the actual current state? |

Max: 16. Levels: 1 (0-3), 2 (4-6), 3 (7-10), 4 (11-13), 5 (14-16)

### Conventions

Coding standards, patterns, naming, testing expectations.

| Item | W | Question |
|------|---|----------|
| code_style | 3 | Documents code style and formatting rules? |
| patterns | 3 | Describes key patterns in the codebase? |
| non_obvious | 2 | Captures conventions that deviate from defaults or would be guessed wrong? |
| naming | 2 | Defines naming conventions? |
| testing | 2 | Documents testing expectations? |
| file_structure | 2 | Explains file/directory organisation? |
| anti_patterns | 1 | Lists things to avoid? |
| examples | 1 | Includes code examples? |
| current | 1 | Reflects actual codebase practices? |

Max: 17. Levels: 1 (0-3), 2 (4-7), 3 (8-10), 4 (11-14), 5 (15-17)

### Development Context

Project history, key decisions, setup, gotchas.

| Item | W | Question |
|------|---|----------|
| project_purpose | 3 | Explains what the project is and why it exists? |
| key_decisions | 3 | Documents major decisions and their rationale? |
| non_obvious | 2 | Captures gotchas, surprises, things that look wrong but aren't? |
| setup | 2 | Describes how to set up the dev environment? |
| build_run | 2 | Documents how to build, run, and test? |
| history | 1 | Provides relevant project history? |
| team_context | 1 | Notes team structure or ownership? |
| roadmap | 1 | Indicates what's planned? |
| current | 1 | Reflects the actual current state? |

Max: 16. Levels: 1 (0-3), 2 (4-6), 3 (7-10), 4 (11-13), 5 (14-16)

## 8. Artifact Catalogue

Built-in artifact types. Use the 4 core types for most projects. Add specialised types only when relevant.

**Core:**
| Type | Key | What it covers |
|------|-----|---------------|
| Architecture | `architecture` | System structure, components, data flow |
| Features | `features` | Capabilities, user-facing functionality |
| Conventions | `conventions` | Coding standards, patterns, testing |
| Dev Context | `dev-context` | Setup, decisions, history, gotchas |

**Specialised:**
| Type | Key | What it covers |
|------|-----|---------------|
| API Docs | `api-docs` | Endpoints, schemas, authentication |
| Data Model | `data-model` | Entities, relationships, storage |
| Deployment | `deployment` | How to deploy, environments, infra |
| Dependencies | `dependencies` | Key deps, why chosen, constraints |
| Design System | `design-system` | Visual standards, components, accessibility |
| Security Model | `security` | Auth, authorization, threat model |
| Performance | `performance` | SLOs, bottlenecks, optimisation |
| Integration | `integration` | Third-party services, APIs consumed |
| AI Context Config | `ai-config` | CLAUDE.md, .cursorrules content |

Rubrics for specialised types are in RUBRICS.md.

Custom artifact types: define the rubric in the per-artifact detail file under `custom_rubric`.

## 9. AI Config Pointer

The pointer in `CLAUDE.md` / `.cursorrules` / etc. enables automatic discovery. Rules:

1. **Never overwrite** existing content. Read the file first, then append.
2. **Use delimiters** (`<!-- chat-spec:start -->` / `<!-- chat-spec:end -->`) so the section is identifiable.
3. **Check before writing.** If the delimiters already exist, do not duplicate.
4. **Keep it minimal.** 2-3 lines. The protocol and manifest are elsewhere.
5. **User can delete it.** Chat-spec still works if manually invoked.
