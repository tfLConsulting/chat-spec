# DQ5: First-Run Experience

## The core question

User pastes the URL, AI reads the protocol. Project has no `.chat-spec/` directory, no manifest, nothing. What happens next?

This is the most critical session. It determines whether the user comes back.

## Research grounding

- **ETH Zurich (2026, arXiv:2602.11988): The first generated artifact MUST NOT restate what's already in the code.** LLM-generated context files that duplicate existing docs decrease performance by 2-3% and increase cost by 20%. When existing docs were removed, the same generated files improved performance by 2.7% — proving they were pure duplication. The first artifact must capture the *delta*: decisions, rationale, gotchas, non-obvious relationships.
- Anthropic context engineering (2025): "Intelligence is not the bottleneck, context is." The first run sets the tone for what chat-spec produces. If the first artifact is boilerplate, the user won't come back.
- Cursor rules empirical study (Jiang & Nam, MSR 2026, arXiv:2512.18925): Analyzed 401 repos — most effective rules cover conventions, guidelines, and project information. First-run scan should look for these.

## What the AI does first

### Step 1: Detect what exists

Before creating anything, the AI scans the project for existing documentation and context. It looks for:

- **AI config files:** `CLAUDE.md`, `.cursorrules`, `.cursor/rules`, `AGENTS.md`, `copilot-instructions.md`
- **Documentation:** `README.md`, `docs/`, `documentation/`, `specs/`
- **Project metadata:** `package.json`, `Cargo.toml`, `pyproject.toml`, `go.mod`, etc.
- **Git history:** Is there a repo? How big? How active?
- **Code structure:** `src/`, `lib/`, `app/`, `tests/`, etc.

This scan is fast and read-only. The AI builds a mental model of the project before proposing anything.

### Step 2: Propose a manifest

Based on the scan, the AI proposes which artifacts to track. Not all 13 — only the ones that make sense for this project.

**Default for a typical project (web app, API, library):**
- Architecture (always)
- Features (always)
- Conventions (always)
- Development context (always)
- + 1-3 specialised types based on what the project actually has (API docs if there are API endpoints, deployment if there's infra config, etc.)

The AI presents this as a proposal:

> "I've scanned your project. Here's what I'd track:
>
> - **Architecture** — you have a Next.js app with a Postgres database, but no architecture doc
> - **Features** — I found some feature descriptions in your README, but nothing comprehensive
> - **Conventions** — your .cursorrules has some patterns, I can build on that
> - **Dev context** — nothing exists yet
> - **API docs** — you have 12 API routes, no documentation
>
> Want me to set this up? You can add or remove types later."

### Step 3: Score what exists

For artifacts where something already exists (even partial), the AI evaluates it against the rubric. This gives the user an immediate picture:

> "Here's your current state:
>
> | Artifact | Score | Source |
> |----------|-------|--------|
> | Architecture | 1/5 | Nothing found |
> | Features | 2/5 | README has a features section |
> | Conventions | 2/5 | .cursorrules has some rules |
> | Dev context | 1/5 | Nothing found |
> | API docs | 1/5 | Nothing found |
>
> Overall: 1.4/5"

This is the "mirror" moment — the user sees the gap between what they have and what they could have. It should be motivating, not demoralising. The AI frames it as opportunity, not failure.

### Step 4: Create the manifest and directory structure

On user approval, the AI creates:

```
.chat-spec/
  manifest.yaml       # populated with artifact registry, initial scores
  artifacts/           # empty for now (detail files created on first evaluation)
```

And optionally creates the `specs/` directory if it doesn't exist:

```
specs/                 # where artifact files will live
```

### Step 5: Generate the first artifact

This is the "wow moment." Don't just leave the user with a manifest and empty directories. Generate one artifact — the one with the most impact.

**Which one first?** The AI picks based on:

1. **What already has content** — if the README has a decent features section, convert/expand that into a proper features doc. Building on existing content feels productive.
2. **What the codebase reveals most about** — architecture can often be inferred from code structure. The AI can generate a useful first draft from `src/`, `package.json`, and config files alone.
3. **What the user asked about** — if the user said "I need better docs for my API," start there.

Default if no signal: **Architecture.** It's foundational, the AI can infer a lot from code, and it provides immediate value to other AI tools that read it.

The AI generates a draft, evaluates it against the rubric, and updates the manifest.

### Step 6: Create the thin pointer

The protocol needs the AI to discover `.chat-spec/` on future sessions. Two mechanisms:

**Primary: CLAUDE.md / AI config pointer**

The AI adds a small block to the user's AI config file (CLAUDE.md, .cursorrules, etc.). It does NOT overwrite — it appends or inserts.

```markdown
## Specs
This project uses chat-spec for documentation quality tracking.
See .chat-spec/manifest.yaml for current state.
When asked to work on specs/documentation, follow the protocol at [chat-spec URL].
```

**How to avoid overwriting existing content:**
1. Read the existing file
2. Check if a chat-spec section already exists (idempotent)
3. Append the section at the end
4. Show the user the diff before writing

If no AI config file exists, create a minimal one with just the pointer.

**Secondary: The manifest itself is discoverable.** If the AI happens to scan `.chat-spec/manifest.yaml` (many AI tools scan dotfiles), the manifest's structure is self-documenting. The `project.protocol_version` field tells it what protocol to follow.

## How much in one session?

The first session is special — it does more than a normal session. But it still needs to be bounded.

**First session scope:**
1. Scan project (fast, automatic)
2. Propose manifest (present to user)
3. Create manifest + directory structure (on approval)
4. Score existing content (automatic)
5. Generate ONE artifact draft (on approval)
6. Create AI config pointer (on approval)

That's it. Don't try to generate all artifacts. Don't deep-dive into every rubric. Leave the user with:
- A manifest showing their baseline
- One new/improved artifact as proof of value
- A backlog showing what's next
- A pointer so the next session knows where to find everything

**Estimated session length:** 5-10 minutes of AI work, 2-3 user approval points.

## The "wow moment"

What makes the user come back? Three candidates:

1. **The mirror:** "Oh wow, I really don't have much documentation." — Awareness of the gap. Motivating for some, demoralising for others. Not enough alone.

2. **The artifact:** "It actually generated a useful architecture doc from my code." — Tangible value. The AI produced something that would have taken the user 30-60 minutes. This is the strongest motivator.

3. **The roadmap:** "It knows what's missing and has a plan." — The manifest + backlog shows a path from 1.4/5 to 4/5. The user sees the journey, not just the gap.

**Decision: The wow moment is the artifact.** The mirror and roadmap support it, but the first generated artifact is what proves chat-spec is worth coming back to. If the generated artifact is mediocre, nothing else saves it.

This means the first-run AI needs to put serious effort into the generated artifact. Better to generate one good artifact than two mediocre ones.

## Handling edge cases

### Project already has great docs

The AI scans and finds comprehensive documentation. Scores come back 3-4/5 across the board.

Response: "Your documentation is already solid. Here's what I'd suggest to go from good to great: [specific gaps from rubric]. Want me to set up tracking so we can maintain this quality?"

The value proposition shifts from "build docs" to "maintain and improve."

### Project is a mess — no docs, complex codebase

The AI scans and everything is 1/5.

Response: "There's a lot of ground to cover. Let's start with the foundation. I'll create an architecture overview based on your code — that'll help me (and any AI tool) understand the project for future sessions."

Don't overwhelm. One artifact. Build momentum.

### User has a .cursorrules but no other docs

Common case. The AI should:
1. Read .cursorrules and extract useful conventions
2. Credit it: "Your .cursorrules already captures some good patterns"
3. Propose expanding it into a proper conventions doc
4. NOT delete or replace the .cursorrules — build alongside it

### Monorepo / multiple projects

V1: treat each directory with its own `.chat-spec/` as a separate project. The protocol doesn't handle cross-project relationships yet.

If the user invokes chat-spec at the monorepo root, the AI should ask: "This looks like a monorepo. Want me to set up chat-spec for the whole repo, or for a specific package?"

## Summary of decisions

| Question | Decision |
|----------|----------|
| What does the AI do first? | Scan project → propose manifest → score → generate one artifact → create pointer |
| How does it detect existing docs? | Looks for AI configs, docs dirs, READMEs, project metadata, code structure |
| Score before building? | Yes — show the baseline first, then improve |
| How much in first session? | Manifest + one artifact + pointer. No more. |
| The wow moment? | The first generated artifact. Must be genuinely useful. |
| CLAUDE.md pointer? | Append a small section, never overwrite, show diff first |
| Choosing the first artifact? | Prefer what has existing content > what code reveals > architecture as default |
