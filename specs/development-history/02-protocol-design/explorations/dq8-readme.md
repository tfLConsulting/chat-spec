# DQ8: The README

## The core question

The README is the landing page. It makes humans want to try chat-spec and gives them the one-liner to get started. What goes in it?

## The audience

Unlike PROTOCOL.md (written for AIs), the README is written for **humans** — specifically:

1. **Solo developers** who use AI tools (Claude Code, Cursor, Copilot) and want better documentation
2. **Developers discovering this repo** on GitHub — they need to understand what it is within 30 seconds
3. **Developers who were told to try it** — they need the quickstart immediately

The README must answer three questions fast:
1. What is this?
2. Why should I care?
3. How do I start?

## Structure

### Proposed outline

```
README.md
├── Title + one-line description
├── The Problem (2-3 sentences)
├── What chat-spec Does (2-3 sentences)
├── Quickstart (the paste-this block)
├── How It Works (brief, visual)
├── What You Get (concrete examples)
├── FAQ / Common Questions
└── Links (PROTOCOL.md, RUBRICS.md, contributing)
```

### Why this order?

The developer scanning GitHub sees: title → problem → solution → quickstart. If they're interested, they read further. If they just want to try it, the quickstart is above the fold (within the first screenful).

## The quickstart: the "paste this" moment

This is the most important part of the README. The entire product distribution is one instruction.

### For Claude Code / CLAUDE.md users

```markdown
## Quickstart

Add this to your project's `CLAUDE.md` (or tell your AI assistant):

> Follow the chat-spec protocol at https://github.com/tfLConsulting/chat-spec/blob/main/PROTOCOL.md
> to evaluate and improve this project's documentation.

Then ask your AI to "run chat-spec" in your project.
```

### For Cursor / .cursorrules users

```markdown
Or for Cursor, add to `.cursorrules`:

Follow the chat-spec protocol at https://github.com/tfLConsulting/chat-spec/blob/main/PROTOCOL.md
to evaluate and improve this project's documentation.
```

### The universal version

Some users don't want to edit config files. They just want to try it:

```markdown
**Just want to try it?** Paste this into any AI assistant:

"Read https://github.com/tfLConsulting/chat-spec/blob/main/PROTOCOL.md and follow the chat-spec protocol for this project."
```

**Decision: Lead with the universal paste, then show tool-specific config options below it.** The universal version has the lowest friction — no file editing needed. Power users can then add the config pointer for automatic discovery.

## Content sections

### Title + tagline

```markdown
# chat-spec

Iterative documentation quality tracking for AI-assisted projects.
```

Short. Says what it is. "Iterative" signals this isn't a one-shot tool. "AI-assisted" signals the target audience.

### The Problem

```markdown
## The Problem

Your AI assistant doesn't know your project. It guesses at architecture,
invents conventions, and hallucinates API details. Adding a CLAUDE.md helps,
but keeping it accurate and comprehensive is a manual chore nobody does.
```

Two pain points: (1) AI tools lack context, (2) maintaining that context is tedious. Chat-spec solves both.

### What chat-spec Does

```markdown
## What chat-spec Does

chat-spec is a protocol that your AI assistant follows to evaluate your
project's documentation, identify gaps, and iteratively improve it — one
session at a time.

It tracks what exists, scores it against rubrics, and proposes focused
improvements. You approve, it generates. Every session moves the needle.

No installation. No dependencies. Just a protocol your AI reads and follows.
```

Key points: iterative (not one-shot), scored (measurable), human-in-the-loop (you approve), zero-install.

### Quickstart

As designed above. Universal paste first, tool-specific config second.

### How It Works

A visual/concise explanation of the flow:

```markdown
## How It Works

1. **First run:** Your AI scans the project, creates a manifest tracking
   which docs exist and their quality scores, and generates your first
   spec improvement.

2. **Every run after:** Your AI reads the manifest, identifies the biggest
   gap or most stale doc, and proposes one focused improvement.

3. **You control it:** The AI always proposes before acting. You approve,
   redirect, or skip. Your project, your call.

State lives in `.chat-spec/manifest.yaml` — a small YAML file tracking
scores, backlog, and history. Commit it to git to share with your team.
```

### What You Get

Concrete, tangible outcomes:

```markdown
## What You Get

chat-spec tracks these documentation types (and more):

| Type | What it covers |
|------|---------------|
| Architecture | System structure, components, data flow |
| Features | What the project does, user capabilities |
| Conventions | Coding standards, patterns, testing expectations |
| Dev Context | Setup, key decisions, gotchas |
| API Docs | Endpoints, schemas, authentication |
| + 8 more | Deployment, data model, security, etc. |

Each type has a rubric — a checklist of specific, measurable quality criteria.
Your AI scores docs against these rubrics and shows you exactly what's missing.
```

### FAQ

Keep it short. Answer the objections:

```markdown
## FAQ

**Does it work with [my AI tool]?**
If your AI tool can read a URL, it works. Tested with Claude Code, Cursor,
and GitHub Copilot.

**Will it overwrite my existing docs?**
No. It always proposes changes for your approval. It builds on what you have.

**How long does a session take?**
Most sessions are 2-5 minutes. First run is a bit longer (5-10 minutes).

**Do I need to run it regularly?**
It's most useful when run periodically — after significant code changes,
or when onboarding someone new. But there's no schedule requirement.

**Is the generated documentation any good?**
chat-spec self-evaluates: after generating content, it re-scores against
the rubric. If the output doesn't improve the score, it tells you.
```

### Links

```markdown
## Links

- [PROTOCOL.md](./PROTOCOL.md) — the full protocol (what your AI reads)
- [RUBRICS.md](./RUBRICS.md) — scoring rubrics for all artifact types
```

## What NOT to include

- **Full protocol content.** The README links to PROTOCOL.md, it doesn't duplicate it.
- **Technical implementation details.** Users don't need to know about manifest schema or checklist weights.
- **Comparison charts with other tools.** Save for a blog post, not the README.
- **Contributing guidelines.** Can go in CONTRIBUTING.md if/when needed.
- **Changelog.** Not yet — too early.

## Length

Target: **~400-500 words** of content. Should fit on one GitHub page without scrolling past the fold for the quickstart. The full README (with code blocks and tables) will be ~2-3KB.

## Design principles for the README

1. **Scannable.** Headers, short paragraphs, tables, code blocks. No walls of text.
2. **Honest.** Don't oversell. "Iterative improvement" not "perfect docs instantly."
3. **Frictionless.** The path from "reading" to "trying" is one copy-paste.
4. **Tool-agnostic.** Don't assume Claude Code. Works with any AI tool that can fetch a URL.
5. **Living document.** The README will evolve. Start minimal, add FAQ entries as real questions come in.

## Summary of decisions

| Question | Decision |
|----------|----------|
| Structure? | Title → problem → solution → quickstart → how it works → what you get → FAQ → links |
| Quickstart format? | Universal paste first, tool-specific config options second |
| Tone? | Developer-friendly, scannable, honest, no hype |
| Length? | ~400-500 words, fits on one screen to quickstart |
| How much protocol? | None inline — link to PROTOCOL.md |
| What's excluded? | Protocol details, comparisons, implementation details, changelog |
