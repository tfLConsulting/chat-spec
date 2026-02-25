# DQ3: Manifest Design

## The core question

The manifest is the heart of the state model. What's the concrete schema? This builds on DQ1 (session model) and DQ2 (state and memory), which established:

- Two-tier state: compact manifest + per-artifact detail files
- `.chat-spec/` directory structure
- Manifest must be ~2-3KB, ~500-700 tokens
- Manifest is the cold-start read — the minimum for session orientation
- YAML format (decided in noodling phase for AI readability)

## Format: YAML

Already decided in noodling, but worth restating why:

- **AI readability:** YAML is more natural-language-adjacent than JSON. No braces, no commas, no quoting keys. AIs parse it well.
- **Human readability:** Users will occasionally open the manifest. YAML is scannable.
- **Editability:** If a user needs to hand-edit (fix a corrupt state, adjust config), YAML is forgiving.
- **Convention:** CLAUDE.md ecosystem uses YAML-like config. Consistency matters.
- **Downside:** YAML has parsing gotchas (Norway problem, indentation sensitivity). But the AI is writing and reading this, not humans, so the gotchas rarely bite.

JSON was the other serious contender. It's unambiguous and every language has a parser. But for a file that's primarily AI-read/AI-written and occasionally human-inspected, YAML wins on readability.

## Schema design principles

1. **Flat over nested.** Every level of nesting costs readability and tokens. Keep it shallow.
2. **Obvious keys.** A key should be self-documenting. `last_evaluated` not `le`. `score` not `s`.
3. **No redundant data.** Don't store what can be derived. If score = sum of checklist weights, don't store both in the manifest (checklist lives in detail files).
4. **Stable across versions.** The schema should be easy to migrate forward. Version field enables this.
5. **Ordered for reading.** Put the most important info first. An AI reading top-to-bottom should get the orientation it needs from the first 10 lines.

## The schema

```yaml
# .chat-spec/manifest.yaml
version: 1

project:
  name: "my-project"                    # human-readable project name
  protocol_version: "0.1"               # which version of chat-spec protocol
  created: 2025-02-18                   # when chat-spec was initialised

config:
  artifact_directory: "specs"            # where spec files live (default: specs/)
  auto_evaluate: true                    # evaluate on every session? (default: true)

artifacts:
  architecture:
    path: "specs/architecture.md"        # relative to project root
    score: 3
    max_score: 5
    status: current                      # current | stale | missing | wip | draft
    last_evaluated: 2025-02-20

  features:
    path: "specs/features.md"
    score: 2
    max_score: 5
    status: stale
    last_evaluated: 2025-02-15

  conventions:
    path: "specs/conventions.md"
    score: 0
    max_score: 5
    status: missing
    last_evaluated: null

backlog:
  - artifact: architecture
    item: "Add data flow diagram"
    priority: high
  - artifact: features
    item: "Document search feature"
    priority: medium
  - artifact: conventions
    item: "Create initial conventions document"
    priority: high

log:
  - "2025-02-20: Evaluated architecture (3/5). Added backlog items for data flow and caching."
  - "2025-02-18: Initial scan. Created manifest with 3 artifacts. Architecture scored 2/5."
```

## Field-by-field rationale

### Top-level: `version`

Schema version number. Integer, starts at 1. The protocol can check this and migrate if needed. Not the protocol version — that's in `project.protocol_version`.

### `project` section

Minimal project metadata:

- **`name`**: Human-readable. Used in status reports ("my-project scores 3.2/5 overall"). The AI can infer this from the directory name or package.json on first run.
- **`protocol_version`**: Which version of the chat-spec protocol this manifest targets. Lets the protocol evolve without breaking existing manifests.
- **`created`**: When chat-spec was initialised. Useful for "how long has this project been using chat-spec?" Not critical, but cheap to store.

### `config` section

User-overridable settings:

- **`artifact_directory`**: Where spec files live. Default `specs/`. Some projects use `docs/`, `documentation/`, or just the root. Setting this once avoids ambiguity.
- **`auto_evaluate`**: Whether the AI should automatically evaluate tracked artifacts at the start of every session. Default true. User might disable for large projects where evaluation is slow.

**What's NOT in config:** Rubric weights, custom rubrics, artifact type definitions. Those are either in the protocol (built-in defaults) or in the per-artifact detail files (customisations). The manifest stays thin.

### `artifacts` section

A map of artifact key → artifact state. The key is a short identifier (lowercase, hyphens ok). Each artifact has:

- **`path`**: Relative path from project root to the artifact file. `null` if the file doesn't exist yet (status: missing).
- **`score`**: Current score, 1-5 integer. 0 means unscored/missing.
- **`max_score`**: Always 5 for built-in types. Included for extensibility (custom types might have different scales, though we should probably normalise to 5).
- **`status`**: One of:
  - `current` — evaluated recently, score reflects reality
  - `stale` — code has changed since last evaluation, score may be outdated
  - `missing` — no artifact file exists, or it's an empty stub
  - `wip` — artifact exists but is known to be incomplete (user is actively working on it)
  - `draft` — AI-generated but not yet reviewed by user
- **`last_evaluated`**: ISO date of last evaluation. `null` if never evaluated.

**What's NOT per-artifact in the manifest:** Checklist results, detailed backlog, notes, generation history. Those live in `.chat-spec/artifacts/<key>.yaml` (the tier-2 detail files from DQ2).

**Why `score` and `max_score`?** Score alone isn't meaningful without knowing the scale. Having `max_score` makes the manifest self-documenting: "3 out of 5." For V1, max_score is always 5, but including it costs almost nothing and future-proofs.

### `backlog` section

A flat list of backlog items, capped at ~10 in the manifest. Each item:

- **`artifact`**: Which artifact this relates to (matches a key in `artifacts`)
- **`item`**: One-line description of the issue or improvement
- **`priority`**: `high` | `medium` | `low`

The full backlog for an artifact lives in its detail file. The manifest holds only the top items — enough for the AI to decide what to propose in an unprompted session without reading detail files.

**Why a flat list, not nested under each artifact?** Because the AI needs to scan priorities across all artifacts. A flat list is easier to scan than jumping between artifact sections. The `artifact` field links back.

**Why cap at ~10?** The manifest is the orientation document. 10 backlog items is plenty for the AI to choose from. More would bloat the manifest. The full backlog per artifact is in the detail files and can grow as needed.

### `log` section

A list of human-readable log entries, most recent first, capped at 5. Each entry is one line: date + what happened.

**Why 5?** The AI needs recent context, not full history. "What happened in the last few sessions?" is the question. Git history provides the full audit trail.

**Why human-readable strings, not structured data?** Because this is for AI orientation. The AI reads "Evaluated architecture (3/5)" and understands. Structured data would be more precise but costs more tokens and isn't needed — the manifest already has the structured data in the `artifacts` section. The log provides narrative context.

## Size analysis

Let's check the budget. A manifest with 8 artifacts:

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
    status: current
    last_evaluated: 2025-02-20
  features:
    path: "specs/features.md"
    score: 2
    max_score: 5
    status: stale
    last_evaluated: 2025-02-15
  conventions:
    path: "specs/conventions.md"
    score: 4
    max_score: 5
    status: current
    last_evaluated: 2025-02-19
  dev-context:
    path: "specs/dev-context.md"
    score: 1
    max_score: 5
    status: draft
    last_evaluated: 2025-02-20
  api-docs:
    path: "specs/api-docs.md"
    score: 0
    max_score: 5
    status: missing
    last_evaluated: null
  data-model:
    path: "specs/data-model.md"
    score: 3
    max_score: 5
    status: current
    last_evaluated: 2025-02-18
  deployment:
    path: "specs/deployment.md"
    score: 2
    max_score: 5
    status: stale
    last_evaluated: 2025-02-10
  dependencies:
    path: "specs/dependencies.md"
    score: 3
    max_score: 5
    status: current
    last_evaluated: 2025-02-19
backlog:
  - artifact: architecture
    item: "Add data flow diagram"
    priority: high
  - artifact: api-docs
    item: "Create initial API documentation"
    priority: high
  - artifact: features
    item: "Document search feature"
    priority: medium
  - artifact: deployment
    item: "Add CI/CD pipeline documentation"
    priority: medium
  - artifact: dev-context
    item: "Needs human input on architectural decisions history"
    priority: low
log:
  - "2025-02-20: Evaluated architecture (3/5), generated dev-context draft (1/5)"
  - "2025-02-19: Updated conventions (4/5), evaluated dependencies (3/5)"
  - "2025-02-18: Initial scan. Created manifest with 8 artifacts."
```

That's approximately **1.8KB**. Well within the 2-3KB budget. Even with 13 artifacts and 10 backlog items, we'd stay under 3KB.

## Per-artifact detail file schema

For completeness (this is what lives in `.chat-spec/artifacts/<key>.yaml`):

```yaml
# .chat-spec/artifacts/architecture.yaml
artifact: architecture
type: architecture                       # built-in type, determines which rubric
last_evaluated: 2025-02-20

checklist:
  - id: components_listed
    result: yes
  - id: relationships_shown
    result: no
    note: "Only API gateway mentioned, missing queue and cache"
  - id: data_flow
    result: no
  - id: tech_stack
    result: yes
  - id: boundaries
    result: partial
    note: "Service boundaries mentioned but not clearly defined"

backlog:
  - item: "Add data flow diagram"
    priority: high
    found: 2025-02-20
  - item: "Document cache invalidation strategy"
    priority: low
    found: 2025-02-20
  - item: "Clarify service boundaries between auth and user services"
    priority: medium
    found: 2025-02-20

notes: null
```

Key design choices:
- **Checklist items reference by `id` only.** The questions and weights are in the rubric (defined by the protocol or custom). This avoids duplicating rubric content in every detail file.
- **`result` is `yes` | `no` | `partial`.** DQ4 will decide whether `partial` is needed or if strict yes/no is better. Including it here as a placeholder.
- **`note` is optional per checklist item.** Explains why something failed — useful for the next session.
- **`backlog` here is the full list** (not capped like the manifest). The manifest pulls the top items from here.

## Naming conventions

- Artifact keys: lowercase, hyphens for spaces (`api-docs`, `dev-context`, `data-model`)
- Detail filenames match keys: `api-docs.yaml`, `dev-context.yaml`
- Spec files: user's choice, but the default recommendation is `specs/<key>.md`

## What about custom artifact types?

Built-in types have predefined rubrics. Custom types need:
- A rubric (defined where?)
- A type identifier

**Decision for V1:** Custom types store their rubric in the detail file. The detail file has a `custom_rubric` section that defines the checklist items, weights, and scoring levels. This keeps custom types self-contained and avoids needing a separate rubric registry.

```yaml
# .chat-spec/artifacts/compliance.yaml
artifact: compliance
type: custom
custom_rubric:
  - id: regulations_listed
    question: "Lists all applicable regulations?"
    weight: 2
  - id: mapping_complete
    question: "Maps each regulation to implementation?"
    weight: 3
  # ...
```

## Summary of decisions

| Question | Decision |
|----------|----------|
| Format? | YAML |
| Top-level sections? | `version`, `project`, `config`, `artifacts`, `backlog`, `log` |
| Artifact state? | `path`, `score`, `max_score`, `status`, `last_evaluated` |
| Status values? | `current`, `stale`, `missing`, `wip`, `draft` |
| Backlog in manifest? | Flat list, capped at ~10, with artifact reference and priority |
| Log in manifest? | Last 5 entries, human-readable one-liners |
| Size at 8 artifacts? | ~1.8KB, ~450 tokens |
| Detail file contents? | Checklist results (by id), full backlog, notes |
| Custom types? | Rubric defined in the detail file via `custom_rubric` |
| Naming? | Lowercase, hyphenated keys; detail files match keys |
