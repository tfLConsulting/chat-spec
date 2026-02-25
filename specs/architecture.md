# Architecture

## Protocol-Not-Tool Design

chat-spec has no runtime, no CLI, no dependencies. The "system" is a set of documents that an AI agent reads and a file structure it maintains. The AI's context window is the execution environment — PROTOCOL.md is the program, the manifest is the database, and the AI is the runtime.

This means: no installation step, no version conflicts, no language constraints. It also means the protocol must be fully self-contained — an AI with no prior context must be able to follow it from a cold start (validated via blank-slate testing with Haiku).

## Components

| Component | Location | Role |
|-----------|----------|------|
| Protocol | `PROTOCOL.md` (root) | Instructions the AI follows. The product. |
| Rubrics | `RUBRICS.md` (root) | Scoring criteria for 13 artifact types. Referenced by the protocol. |
| README | `README.md` (root) | Human landing page. Not read by the AI during protocol execution. |
| Manifest | `.chat-spec/manifest.yaml` | Session-persistent state: scores, backlog, log. The AI reads this first on every subsequent run. |
| Detail files | `.chat-spec/artifacts/*.yaml` | Per-artifact checklist results and backlog. Only read when working on that specific artifact. |
| Cached protocol | `.chat-spec/protocol.md` | Pinned copy of the protocol version that scored the artifacts. |
| Cached rubrics | `.chat-spec/rubrics.md` | Pinned copy of the rubrics version used for scoring. |
| AI config pointer | `CLAUDE.md` / `.cursorrules` (3 lines) | Auto-discovery hook. Points the AI to the cached protocol. |
| Artifact files | `specs/*.md` (configurable) | The generated documentation. What the user actually cares about. |

## Three Boundaries

**Root files** (PROTOCOL.md, RUBRICS.md, README.md) — the product. These are what get distributed. They live at the repo root because they *are* the project.

**`.chat-spec/`** — tracking state. Manifest, detail files, cached copies. This is the AI's working memory across sessions. Committed to git so team members share scoring state. Never edited by humans in normal use.

**`specs/`** (or configured `artifact_directory`) — generated output. The documentation artifacts the protocol produces. These are the value delivered to the user's project. The directory name is configurable because on *this* repo, `specs/` is overloaded with chat-spec's own development history.

## Data Flow

### First run

```
User invokes "run chat-spec"
  → AI reads PROTOCOL.md (from URL or local)
  → AI scans project (docs, config, code structure)
  → AI proposes manifest to user
  → User approves
  → AI creates .chat-spec/ (manifest, detail files, cached protocol+rubrics)
  → AI generates ONE artifact into specs/
  → AI evaluates artifact against rubric, writes score to manifest
  → AI appends pointer to CLAUDE.md/.cursorrules
```

### Subsequent runs

```
User invokes "run chat-spec"
  → AI reads .chat-spec/manifest.yaml (NOT the full protocol — cached version used)
  → AI checks for staleness (git log or file mtime)
  → AI picks highest-priority gap (missing > low-score > stale > backlog)
  → AI presents options to user
  → User picks one
  → AI reads detail file + artifact file for chosen artifact
  → AI evaluates, identifies failing rubric items
  → AI proposes improvements to user
  → User approves
  → AI generates/improves artifact
  → AI re-evaluates, updates manifest + detail file
```

### Session boundary

Each session is stateless except through the manifest. The manifest is the only coordination mechanism between sessions — there is no chat history, no thread ID, no external state. This is deliberate: it means any AI tool can pick up where another left off.

## Persistence

All state is flat files in `.chat-spec/`:

- **manifest.yaml** — summary state (scores, backlog top 10, log last 5)
- **artifacts/*.yaml** — detail state (per-item checklist, full backlog)

No database, no API, no cloud storage. Git is the recommended sync mechanism. The manifest is designed to be small enough to read in a single tool call.

## Entry Points

1. **URL paste** — user pastes PROTOCOL.md URL into any AI chat. Cold start, no prior state.
2. **AI config pointer** — CLAUDE.md/.cursorrules contains a 3-line section pointing to the cached protocol. Discovered automatically when the AI reads its config.
3. **Direct invocation** — user says "run chat-spec" and the AI finds the pointer or manifest.

All three converge to the same flow: read manifest (if exists) → follow protocol.

## Constraints and Trade-offs

| Constraint | Why | Consequence |
|-----------|-----|-------------|
| One artifact per session | Context windows fill up; quality degrades with volume (ETH Zurich research) | Multiple sessions needed for full coverage. Intentional — slow and correct beats fast and wrong. |
| Binary scoring only (YES/NO) | Reproducibility across models. Likert scales and LLM-as-judge produce inconsistent results. | Less granularity. Mitigated by weighting items differently. |
| Protocol must fit in ~16KB | Token budget matters — the protocol consumes the AI's context. | Forces conciseness. Rubrics for non-core types are in a separate file loaded only when needed. |
| No cross-tool sync | Sync between CLAUDE.md and .cursorrules is a solved-never problem. | Each tool reads its own config; chat-spec only appends a pointer. Users maintain tool-specific config themselves. |
| Cached protocol pins version | Scoring must be consistent. If the rubric changes between sessions, scores become incomparable. | Users must manually update cached copies to adopt new protocol versions. |

## External Dependencies

None. The protocol assumes only: (1) an AI that can read files and write files, (2) a filesystem. Git is recommended but not required — staleness detection falls back to file modification times.
