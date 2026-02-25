# Idea 1: The Living Manifest

## Core concept

Chat-spec is a CLI tool that maintains a single manifest file (`specs/manifest.yaml`) as the source of truth about what a project's AI-facing documentation looks like and how good it is. Everything else flows from this manifest.

## How it works

### The manifest

A YAML file that lists every artifact chat-spec tracks, along with metadata:

```yaml
schema_version: 1
project: my-app
last_run: 2026-02-25T14:30:00Z

artifacts:
  architecture:
    path: specs/architecture.md
    status: exists        # exists | missing | stub
    maturity: 3           # 1-5 scale
    last_validated: 2026-02-20
    last_modified: 2026-02-18
    rubric: |
      1: Missing or placeholder
      2: Lists major components but no relationships
      3: Shows components, relationships, and data flow
      4: Includes rationale for key decisions, deployment model
      5: Complete, validated against code, includes evolution notes
    evidence: specs/validation/architecture-validation.md
    notes: "Covers backend well, frontend architecture is thin"

  feature-manifest:
    path: specs/features.md
    status: exists
    maturity: 2
    last_validated: 2026-01-15
    rubric: |
      1: Missing or placeholder
      2: Rough list of features
      3: Features with descriptions and status
      4: Features linked to code, with acceptance criteria
      5: Living document, auto-validated against tests/code
    notes: "Hasn't been touched since initial creation"
```

### The artifact catalogue

Chat-spec has a built-in catalogue of artifact types it knows about (architecture, features, design system, constraints, standards, dev history, API docs, data model, etc.). But projects can also define custom artifacts. The catalogue provides default rubrics that can be overridden per-project.

### The run loop

When you run `chat-spec`:

1. **Read the manifest** (or create one if first run)
2. **Scan the project** — look at the codebase structure, package files, configs, existing docs, README, etc. to build a picture of the project
3. **Evaluate each artifact** — for each tracked artifact:
   - Does the file still exist?
   - Has it been modified since last validation?
   - Score it against its rubric (this is the AI doing the scoring)
   - Compare to previous score
4. **Identify gaps** — artifacts that are missing, stale, or low-maturity
5. **Prioritise** — which gaps matter most? A missing architecture doc for a complex system matters more than a missing design-system doc for a CLI tool
6. **Propose** — present the user with a ranked list of suggested actions:
   - "Your feature manifest is at maturity 2 and hasn't been validated in 6 weeks. Want me to improve it?"
   - "You don't have a data model document. Based on your schema files, I can create one."
   - "Your architecture doc scores well but hasn't been checked since you added the new auth service."
7. **Execute** (with consent) — do the work the user approves, then update the manifest

### How it learns about the project

On first run, it does a deep scan:
- Package files (package.json, Cargo.toml, etc.)
- Directory structure
- README and existing docs
- CI/CD configs
- Test structure
- Git history (recent commits, frequency, contributors)

On subsequent runs, it's incremental — git diff since last run to see what changed, plus re-reading any artifacts it's about to evaluate.

### Cross-tool output

The specs/ directory is the canonical form. But chat-spec can also generate derivative files:
- CLAUDE.md (synthesised from specs for Claude Code)
- .cursorrules (for Cursor)
- AGENTS.md (for GitHub Copilot)

These are generated artefacts, not source. The specs are the source.

## What I like about this

- The manifest is tangible and inspectable — you can read it yourself
- Rubrics make scoring transparent, not a black box
- Incremental by design
- The prioritisation step means it's not just a checklist, it actually thinks about what matters

## Assumptions baked in

- YAML is the right format for the manifest
- 1-5 maturity scale is sufficient granularity
- AI can reliably score documentation against rubrics
- A single manifest file scales to real projects
- Users want a CLI tool (vs. something integrated into their editor/AI assistant)
- The artifact catalogue should be extensible
- Derivative file generation (CLAUDE.md etc.) is valuable
- Git is available for change detection
