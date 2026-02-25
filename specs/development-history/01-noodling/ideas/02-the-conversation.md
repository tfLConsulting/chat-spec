# Idea 2: The Conversation

## Core concept

Chat-spec isn't a CLI tool — it's a prompt-driven workflow that runs inside whatever AI assistant you already use. There's no separate binary to install. Instead, chat-spec is a set of structured prompts and a directory convention that any capable AI can follow.

## How it works

### No tool, just protocol

You don't `npm install chat-spec`. Instead, chat-spec defines:

1. **A directory structure** (`specs/`) with well-known filenames
2. **A manifest format** (`specs/manifest.json`)
3. **A set of prompts** that any AI assistant can execute
4. **Rubric definitions** embedded in the manifest itself

The "tool" is a CLAUDE.md file (or equivalent) that teaches your AI assistant how to be a spec manager.

### The prompts

Chat-spec defines a small vocabulary of commands the user runs conversationally:

- **`/chat-spec`** (or just asking "run chat-spec") — the main loop. The AI reads the manifest, evaluates the specs, and proposes improvements
- **`/chat-spec init`** — first-time setup. AI scans the project and proposes an initial manifest and spec stubs
- **`/chat-spec focus architecture`** — deep-dive a specific artifact
- **`/chat-spec status`** — quick summary without proposals

These aren't CLI commands — they're prompts that the AI interprets. They could be slash commands in Claude Code, or just natural language in any AI chat.

### The manifest

Same concept as Idea 1, but simpler because it needs to be maintainable by AI alone (no build step, no schema validation):

```json
{
  "version": 1,
  "artifacts": {
    "architecture": {
      "path": "specs/architecture.md",
      "maturity": 3,
      "lastValidated": "2026-02-20",
      "rubric": "1:missing 2:components-only 3:components+relationships 4:+rationale 5:+validated",
      "notes": "Frontend architecture thin"
    }
  }
}
```

### How evaluation works

When the AI runs chat-spec, it:

1. Reads the manifest
2. Reads each artifact file
3. Reads relevant parts of the codebase (guided by the artifact type — e.g., for architecture, look at directory structure and imports; for features, look at routes and tests)
4. Scores each artifact against its rubric
5. Presents findings conversationally: "Your architecture doc is solid (3/5) but your feature list is stale. Want me to work on the features?"

The key insight: the AI is already reading your codebase. Chat-spec just gives it a framework for what to look at and how to assess it.

### Iteration model

Each conversation where chat-spec runs is an iteration. The AI updates the manifest at the end (with consent). Over time, across many conversations (possibly with different AI tools), the specs improve.

There's no change detection beyond what the AI can observe. No git integration needed. The AI just reads the files and judges them fresh each time.

## What I like about this

- Zero installation friction — works with any AI assistant that can read/write files
- Meets people where they are — no new tool to learn, just a convention
- The AI does the scoring in real-time, using its actual understanding, not a heuristic
- Naturally multi-tool — works with Claude Code, Cursor, Copilot, etc.
- No build step, no dependencies, no updates to manage

## Assumptions baked in

- AI assistants are capable enough to follow this protocol reliably
- JSON (not YAML) because it's more universally parseable by AI tools
- No git integration — the AI judges freshness by reading, not by diffing
- The prompt vocabulary is small and stable
- Users are comfortable with conversational interaction (vs. a deterministic tool)
- Cross-tool consistency doesn't require a shared runtime — the manifest and rubrics are enough
- Slash commands / prompt conventions are sufficient UX
- No need for a web UI, dashboard, or reporting layer
- The manifest stays small enough for AI context windows
