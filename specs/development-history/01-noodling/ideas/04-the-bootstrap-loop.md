# Idea 4: The Bootstrap Loop

## Core concept

Chat-spec is an AI-native tool — it's a Claude Code custom command (or MCP server) that is itself an AI agent. You invoke it, and it has a structured conversation with you about your project's specs. The key difference from the other ideas: it's opinionated about what good looks like and it drives the conversation, not you.

## How it works

### It's an agent, not a script

When you run `/chat-spec`, you're not running a linter or a generator — you're starting a structured conversation with an agent that has a specific job: make this project's AI context better.

The agent has:
- A system prompt that defines the chat-spec methodology
- Access to the filesystem (reads codebase, reads/writes specs)
- A manifest it maintains
- A rubric library
- A conversational protocol for interacting with the user

### The bootstrap problem

New projects have no specs. The agent can't evaluate what doesn't exist. So chat-spec has a distinct **bootstrap mode** that's different from steady-state:

**Bootstrap (first few runs):**
1. Scan the project deeply — structure, dependencies, patterns, README, existing docs
2. Propose a manifest — "Based on what I see, your project would benefit from these artifacts: architecture, features, API docs, conventions. Here's my suggested priority order."
3. User approves/adjusts the manifest
4. Agent starts building the highest-priority artifact — conversationally. It drafts, asks questions ("Is the auth service a separate deployment or co-located?"), iterates.
5. One artifact per session (context window management). Manifest updated at end.

**Steady-state (subsequent runs):**
1. Read manifest, check what's changed since last run
2. Re-evaluate existing artifacts (quick scan, not deep rewrite)
3. Identify the biggest gap or most stale artifact
4. Propose one focused action: improve, update, or create
5. Do the work conversationally

### The conversational protocol

The agent doesn't just dump output. It:
- **Asks before changing** — always. "I think your architecture doc is missing the new payment service. Want me to add it?"
- **Shows its reasoning** — "I'm scoring your feature manifest at 2/5 because it lists features but doesn't link them to code or tests. Here's what 4/5 would look like."
- **Focuses** — one thing per session. Better to do one artifact well than five badly.
- **Remembers** — the manifest is its memory across sessions. Notes field captures context that would otherwise be lost.

### The manifest

Similar to Idea 1 but with richer session tracking:

```yaml
artifacts:
  architecture:
    path: specs/architecture.md
    maturity: 3
    rubric: [standard architecture rubric]
    sessions:
      - date: 2026-02-10
        action: created
        from_maturity: 0
        to_maturity: 2
        notes: "Initial draft, user confirmed microservices arch"
      - date: 2026-02-20
        action: improved
        from_maturity: 2
        to_maturity: 3
        notes: "Added data flow diagrams, still need deployment model"
```

### Multi-tool story

The agent lives in Claude Code (or any tool that supports custom commands / MCP). But the manifest and specs are just files. Any other AI tool can read them. Chat-spec doesn't need to run in every tool — it just needs to run somewhere, and the output is universal.

## What I like about this

- The conversational approach handles ambiguity well — it can ask when it's unsure
- One-thing-per-session is realistic about context windows and attention
- The bootstrap/steady-state distinction acknowledges that first-run is different
- Session history in the manifest creates a narrative — you can see how the project's documentation evolved
- It's an AI tool for AI tools — meta in a way that makes sense

## Assumptions baked in

- An agent-driven conversation is the right UX (vs. user-driven, or non-interactive)
- One artifact per session is the right granularity
- Claude Code (or similar) is the host environment
- The agent can drive a productive conversation about project documentation
- Users will engage with the conversation (not just want a one-click solution)
- Session history is valuable enough to track in the manifest
- The bootstrap/steady-state distinction is meaningful (not just a gradient)
- The agent's system prompt can encode enough methodology to be consistent across runs
- MCP or custom commands are the right distribution mechanism
