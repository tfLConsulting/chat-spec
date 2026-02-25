# DQ6: Subsequent-Run Experience

## The core question

Session 2+ is where iterative value lives. The manifest exists, some artifacts exist. How does the AI re-orient, decide what to do, and deliver value every time?

## Re-orientation: the first 30 seconds

Every subsequent session starts the same way:

1. **Read `.chat-spec/manifest.yaml`** (~500 tokens)
2. **Assess the situation:**
   - What artifacts are tracked and their scores?
   - What's in the backlog?
   - What happened in the last few sessions (log)?
   - Are any artifacts stale (code changed since last eval)?

That's it. The AI doesn't read every artifact file, every detail file, or the full protocol on every session. It reads the manifest and orients.

The manifest gives the AI enough to decide what to propose. Detail files and artifact content are loaded only after the user agrees on a direction.

### Staleness detection

How does the AI know code has changed since the last evaluation?

**Primary: Git diff.** If the project is a git repo (most are), the AI can check:
```
git log --since="<last_evaluated date>" --name-only
```

If files relevant to an artifact have changed, that artifact is stale. The AI updates its status in the manifest.

**Fallback: File modification times.** If no git, compare file mtimes against `last_evaluated` timestamps. Less precise but functional.

**What counts as "relevant" changes?** This is imprecise and that's okay. The heuristic:
- Architecture: changes to project structure, config files, new directories/services
- Features: changes to user-facing code, routes, UI components
- Conventions: changes to linting config, test patterns, new file patterns
- API docs: changes to route handlers, API schemas
- General: any change to the artifact file itself

The AI doesn't need to be perfect here. If it marks something stale that's actually fine, the re-evaluation is quick and confirms it. False positives are cheap; false negatives (missing real staleness) are worse.

## The four scenarios

### Scenario 1: "Run chat-spec" (unprompted)

The most common subsequent-run case. The user invokes the protocol with no specific request.

**Flow:**
1. Read manifest, detect staleness
2. Apply priority heuristic (from DQ1): broken > missing > low-scoring > stale > backlog
3. Present the proposed action:

> "Your project scores 2.6/5 overall. Here's what I'd suggest:
>
> **Architecture** is stale — your codebase has changed significantly since it was last evaluated (Feb 15). Want me to re-evaluate and update it?
>
> Other options:
> - API docs are missing (score: 1/5) — I could generate a first draft
> - Features doc could use improvement (score: 2/5) — missing 3 key features
>
> What would you like to focus on?"

4. User picks (or asks for something else entirely)
5. AI proceeds with the chosen action

**Key design choice:** Always present options, never just start working. Even in unprompted mode, the user decides.

### Scenario 2: "Improve my API docs" (directed)

The user has a specific target.

**Flow:**
1. Read manifest — find the `api-docs` artifact
2. Read the detail file (`.chat-spec/artifacts/api-docs.yaml`) for checklist state and backlog
3. Read the artifact file itself (`specs/api-docs.md`)
4. Re-evaluate against the rubric
5. Present the gap analysis:

> "Your API docs score 2/5. Here's what's missing:
>
> - **Endpoint list** (weight 3): You have 5 of 12 endpoints documented
> - **Request/response examples** (weight 2): Only 2 endpoints have examples
> - **Authentication** (weight 2): Not documented
>
> I'd focus on completing the endpoint list first — that has the highest impact. Want me to draft the missing endpoint docs? I can infer most of the details from your route handlers."

6. On approval, generate/improve the content
7. Re-evaluate, update manifest

### Scenario 3: "What's the status?" (inquiry)

The user wants a report, not action.

**Flow:**
1. Read manifest
2. Present a summary:

> "**chat-spec status for my-project:**
>
> | Artifact | Score | Status | Last evaluated |
> |----------|-------|--------|----------------|
> | Architecture | 3/5 | current | Feb 20 |
> | Features | 2/5 | stale | Feb 15 |
> | Conventions | 4/5 | current | Feb 19 |
> | Dev context | 1/5 | draft | Feb 20 |
> | API docs | 1/5 | missing | — |
>
> **Overall: 2.2/5**
>
> **Top priorities:**
> 1. API docs — create initial documentation (high impact, missing entirely)
> 2. Features — re-evaluate, codebase has changed
> 3. Dev context — review and improve the draft
>
> Want me to work on any of these?"

3. No action taken unless the user asks

This is quick and cheap. Read manifest, format, present. No artifact files loaded.

### Scenario 4: "Add a new artifact type" (configuration)

The user wants to track something new.

**Flow:**
1. User: "I want to track my deployment docs"
2. AI checks if `deployment` is a built-in type (it is)
3. AI looks for existing deployment-related docs in the project
4. Proposes:

> "I'll add deployment tracking. I found a `deploy/` directory and a `Dockerfile`. I'll create a spec at `specs/deployment.md` and evaluate against the deployment rubric. Sound good?"

5. On approval: add to manifest, create detail file, evaluate/generate

For custom types (not built-in), the AI helps define a custom rubric:

> "I don't have a built-in rubric for 'compliance'. Let me help you create one. What aspects of compliance documentation matter for your project?"

## Re-scoring strategy

When should the AI re-evaluate artifacts?

**Options:**
1. **Everything, every session** — comprehensive but wasteful. Most artifacts haven't changed.
2. **Only stale artifacts** — efficient but misses improvements the user made manually.
3. **Only what the user asks about** — minimal but the user might not know what's stale.
4. **Stale + the current target** — smart default.

**Decision: Auto-evaluate stale artifacts. Re-evaluate the target artifact. Don't touch the rest.**

Specifically:
- At session start, if `auto_evaluate` is true (default), the AI checks for staleness and re-scores any stale artifacts. This is fast — it reads the artifact file and runs through the checklist. No generation, just scoring.
- When working on a specific artifact, always re-evaluate it before and after changes.
- Never re-evaluate current, non-target artifacts. They're fine.

This keeps sessions fast while catching drift.

## Priority heuristics in detail

From DQ1, the priority order is: broken > missing > low-scoring > stale > backlog.

Let's make this more concrete:

### Priority 1: Broken
An artifact that was `current` but now contradicts the codebase. Example: architecture doc says "we use Redis for caching" but Redis was removed.

Detection: staleness check shows significant changes to relevant code.
Action: re-evaluate, then propose updates to match reality.

### Priority 2: Missing
An artifact in the manifest with status `missing` — no file exists.

Action: propose generating it. The AI can usually produce a useful first draft from code analysis.

### Priority 3: Low-scoring
The artifact with the lowest score relative to its max.

Action: propose improving the highest-weight failing checklist items.

### Priority 4: Stale
An artifact that hasn't been evaluated in a while, even if no obvious code changes.

Action: re-evaluate. If the score changed, propose updates.

### Priority 5: Backlog
The highest-priority backlog item across all artifacts.

Action: propose addressing it.

### Tie-breaker
When two artifacts have equal priority, prefer:
1. Core types (architecture, features, conventions, dev context) over specialised
2. Higher-weight failing items over lower-weight
3. More recently touched code over stale code (momentum)

## Coexisting with existing CLAUDE.md content

Users have their own CLAUDE.md content — custom instructions, project rules, tool preferences. Chat-spec must not disrupt this.

**Rules:**
1. **Never overwrite.** Always read first, then append or insert into a clearly marked section.
2. **Clearly delimited.** The chat-spec section is marked with comments/headers so the user can find and edit it:
   ```markdown
   <!-- chat-spec:start -->
   ## Specs
   This project uses chat-spec. See .chat-spec/manifest.yaml.
   <!-- chat-spec:end -->
   ```
3. **Minimal footprint.** The pointer is 2-3 lines, not a full configuration block. The protocol and manifest are elsewhere.
4. **Idempotent.** If the section already exists, don't duplicate it. Check for the markers first.
5. **User can delete it.** If the user removes the pointer, that's fine. Chat-spec still works if the user manually invokes it — the pointer just makes auto-discovery easier.

## The "value every session" principle

Every session should leave the user feeling like something improved. Even short sessions. Even "just checking status" sessions.

**How:**
- Status sessions: present new insights ("your features doc has drifted since last week")
- Evaluation sessions: show specific, actionable gaps
- Generation sessions: produce tangible content
- Even if the user says "never mind" after seeing the proposal: they still learned something about their project state

**Anti-pattern to avoid:** "Everything looks good, nothing to do." If the AI truly can't find anything to improve, it should say so — but this should be rare. There's almost always a backlog item or a freshness check to run.

## Summary of decisions

| Question | Decision |
|----------|----------|
| How does the AI re-orient? | Read manifest only (~500 tokens). Detail files on demand. |
| Staleness detection? | Git diff primary, file mtimes fallback |
| Unprompted session? | Present top priority + options, user chooses |
| Directed session? | Jump to target, evaluate, present gaps, propose improvements |
| Status inquiry? | Summary table from manifest, no file loading needed |
| Re-scoring strategy? | Auto-evaluate stale + always evaluate current target |
| Priority heuristic? | Broken > missing > low-scoring > stale > backlog, with core type tie-breaker |
| CLAUDE.md coexistence? | Delimited section, never overwrite, minimal footprint, idempotent |
