# DQ2: State and Memory

## The core question

Between sessions, the AI forgets everything. Where does project state live, and how does the next session pick up where the last one left off?

## Research grounding

- Anthropic CLAUDE.md docs (code.claude.com): "Under 300 lines is best; shorter is even better." Manifests must be compact.
- Aider's repo map (aider.chat): Uses tree-sitter + PageRank to build token-limited index files. Validates the "small index pointing to detail" pattern.
- Anthropic long context research (2023): Placing documents at the top of the prompt improves quality by up to 30%. The manifest should be the first thing read.
- Databricks (2024): Performance degrades after 32K-64K tokens. Our entire state budget (~5K tokens maximum) is trivial against this limit.

## What state needs to persist?

Working backwards from what the AI needs on session N:

1. **Which artifacts are tracked** — the list of what this project cares about
2. **Current scores** — where each artifact stands (1-5, plus checklist detail)
3. **Checklist results** — which rubric items pass/fail for each artifact (this IS the detailed score)
4. **Backlog** — problems found but not yet addressed
5. **Last-evaluated timestamps** — when each artifact was last scored, to detect staleness
6. **Session history** — what happened in past sessions (brief, for orientation)
7. **Project config** — protocol version, custom settings, overrides
8. **Work-in-progress** — if a session ended before completing an action

## The manifest boundary

The manifest is the heart of state, but not everything should be in it. Two failure modes:

- **Too fat:** Manifest grows to thousands of lines, doesn't fit in context, becomes a management burden
- **Too scattered:** State spread across dozens of tiny files, AI has to read many files to orient

### Decision: Two-tier state model

**Tier 1: The manifest** — compact, always read, fits in ~2-3KB of YAML. Contains:
- Artifact registry (what's tracked, where it lives, current score, last-evaluated date)
- Project config
- Session log (last ~5 entries, one line each)
- Active backlog (top items only, capped)

**Tier 2: Per-artifact detail** — read on demand when working on a specific artifact. Contains:
- Full checklist results (every rubric item, pass/fail, notes)
- Extended backlog for that artifact
- Work-in-progress notes

### Why two tiers?

The manifest is the "cold start" read — the minimum the AI needs to orient itself and decide what to do. It must be small enough that reading it doesn't consume meaningful context. At ~2-3KB, it's about 500-700 tokens — trivial.

Per-artifact detail is only needed when actually working on that artifact. If the session is "evaluate architecture," the AI reads the manifest (to orient) plus the architecture detail file (to continue from where it left off). It doesn't need to read the detail files for every other artifact.

### Size budget

| Component | Estimated size | Tokens (~) |
|-----------|---------------|------------|
| Manifest (8 artifacts) | 2-3 KB | 500-700 |
| Per-artifact detail | 0.5-1 KB each | 150-250 |
| Total for full read | 6-11 KB | 1,500-2,700 |
| Protocol doc | ~8-15 KB | 2,000-4,000 |

Even reading everything (manifest + all detail files + protocol), we're under 7K tokens. That leaves 95%+ of any modern context window for actual work. This is comfortable.

At scale (15 artifacts, large backlogs), the total might reach 15-20KB (~5K tokens). Still fine, but the manifest itself should stay compact — the detail files absorb the growth.

## Directory structure

### Option A: Dotfile directory
```
.chat-spec/
  manifest.yaml
  artifacts/
    architecture.yaml
    features.yaml
    ...
```

### Option B: Specs directory
```
specs/
  .chat-spec/
    manifest.yaml
    artifacts/
      architecture.yaml
```

### Option C: Top-level manifest, dotfile for detail
```
chat-spec.yaml          # manifest (visible, easy to find)
.chat-spec/             # detail files (hidden, not cluttering)
  artifacts/
    architecture.yaml
```

### Decision: Option A — `.chat-spec/` directory

Rationale:
- **Convention:** Dotfile directories are the established pattern for tool state (`.git/`, `.vscode/`, `.cursor/`). Users expect this.
- **Gitignore-friendly:** Users can choose to commit or ignore. (Default recommendation: commit — it's useful project state.)
- **Clean:** Everything in one place, nothing cluttering the project root.
- **Discoverable:** The protocol tells the AI "look for `.chat-spec/manifest.yaml`" — single, unambiguous location.

Option C (top-level manifest) has appeal — it's more visible. But visibility isn't valuable here. The manifest isn't a human-edited file; it's AI-managed state. Hiding it in a dotfile directory is appropriate.

### Full directory layout

```
.chat-spec/
  manifest.yaml              # always read first — the index
  artifacts/                  # per-artifact detail
    architecture.yaml         # checklist results, backlog, notes
    features.yaml
    conventions.yaml
    ...
```

That's it. Two levels. The `artifacts/` directory has one file per tracked artifact, named to match the artifact key in the manifest. No other subdirectories, no nested state.

## Manifest structure (preview)

Full schema is DQ3's job, but the key structural decisions:

```yaml
version: 1
project:
  name: my-project
  protocol: "0.1"

artifacts:
  architecture:
    path: specs/architecture.md    # where the actual artifact lives
    score: 3
    max_score: 5
    last_evaluated: 2025-02-20
    status: current                # current | stale | missing | wip
  features:
    path: specs/features.md
    score: 2
    max_score: 5
    last_evaluated: 2025-02-18
    status: stale

backlog:
  - artifact: architecture
    item: "Add data flow diagram"
    priority: high
  - artifact: features
    item: "Missing search feature documentation"
    priority: medium

log:
  - "2025-02-20: Evaluated architecture (3/5), updated conventions"
  - "2025-02-18: Initial scan, created manifest, generated architecture stub"
```

## Per-artifact detail file

```yaml
# .chat-spec/artifacts/architecture.yaml
artifact: architecture
last_evaluated: 2025-02-20

checklist:
  - id: components_listed
    question: "Lists all major system components?"
    weight: 2
    result: yes
  - id: relationships_shown
    question: "Describes how components communicate?"
    weight: 2
    result: no
    note: "Only mentions API gateway, missing queue and cache connections"
  - id: data_flow
    question: "Shows how data flows through the system?"
    weight: 1
    result: no

backlog:
  - item: "Add data flow diagram"
    priority: high
    found: 2025-02-20
  - item: "Document cache invalidation strategy"
    priority: low
    found: 2025-02-20

wip: null   # or a note about in-progress work
```

## Work-in-progress handling

What happens if the user closes the session while the AI is mid-generation?

### The problem

The AI is writing a new architecture doc. It's halfway done. The user closes the chat. Next session, what does the AI find?

Possible states:
1. **Nothing written** — the AI was still composing. The artifact file doesn't exist or is unchanged.
2. **Partial file** — the AI wrote some content but didn't finish. The file exists but is incomplete.
3. **File written, manifest not updated** — the AI wrote the full file but didn't get to update scores.

### Decision: The manifest is the source of truth for completion

The protocol instructs the AI:
1. Write/update the artifact file
2. **Then** update the manifest

If the manifest wasn't updated, the next session treats the artifact as if the work didn't happen. It re-evaluates what's on disk. If the file is partially written, the evaluation will score it as-is (probably low). If the file is complete but the manifest is stale, the evaluation will catch up.

This means: **no explicit WIP tracking needed in most cases.** The manifest's `last_evaluated` timestamp and the artifact's file modification date tell the story. If the file is newer than the last evaluation, the AI knows it needs to re-evaluate.

For truly long-running work that spans sessions intentionally (rare in V1), the per-artifact detail file can have a `wip` field with a note. But this is the exception, not the norm.

## The cold-start read set

When the AI starts a new session, it reads, in order:

1. **`.chat-spec/manifest.yaml`** — always, first thing. This tells it: what artifacts exist, their current state, recent history, and what's in the backlog. (~500-700 tokens)
2. **The relevant artifact detail file** — only if the session will work on a specific artifact. (~150-250 tokens)
3. **The artifact file itself** — the actual spec/doc being evaluated or improved. (Variable, but typically 1-5KB)

Total cold-start cost: ~1-2K tokens for orientation + the artifact content. This is minimal.

For an unprompted session (Mode 3 from DQ1), the AI reads only the manifest, decides what to propose, and then reads the relevant detail file + artifact only after the user approves the proposed action.

## Avoiding clutter

Design constraints to prevent state sprawl:

1. **No per-session files.** Session history is a capped log in the manifest (last 5 entries), not individual files.
2. **No per-problem files.** Backlog items live in the manifest (top items) or per-artifact detail files (full list). Not separate files.
3. **One detail file per artifact.** Not per-checklist-item or per-evaluation.
4. **Capped backlog in manifest.** The manifest holds at most ~10 backlog items (the highest priority). Per-artifact detail files can hold more.
5. **Log rotation.** The session log in the manifest keeps only the last 5 entries. Older history is simply dropped — it's not valuable enough to keep.

## Git integration

The `.chat-spec/` directory should be committed to git (recommended, not required). Benefits:
- Team members see the same manifest
- History of score changes is in git log
- Branching works naturally (feature branch can have different spec state)

The protocol should include a note:

> We recommend committing `.chat-spec/` to version control. It's small, it's useful project state, and it lets your team share spec quality tracking.

## Summary of decisions

| Question | Decision |
|----------|----------|
| State model? | Two-tier: compact manifest + per-artifact detail files |
| Where does state live? | `.chat-spec/` directory (dotfile convention) |
| Manifest size budget? | ~2-3KB, ~500-700 tokens |
| Total state budget? | Under 7K tokens for everything, even at scale |
| WIP handling? | Manifest is source of truth for completion; no explicit WIP tracking needed |
| Cold-start read set? | Manifest only (~500-700 tokens), then detail on demand |
| Clutter prevention? | Capped logs, no per-session/per-problem files, one detail file per artifact |
| Git? | Recommend committing `.chat-spec/` |
