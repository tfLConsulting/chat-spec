# DQ1: Session Model

## The core question

AI tools can't do everything at once. Users might not want them to. How does a single invocation of chat-spec scope its work?

## Research grounding

- Anthropic's context engineering guidance (September 2025): "Intelligence is not the bottleneck, context is." Sessions should be focused to keep context windows clean.
- Databricks Mosaic Research (2024, arXiv:2411.03538): Model performance degrades after 32K-64K tokens. Bounded sessions prevent context bloat.
- "Lost in the Middle" (Liu et al., TACL 2024): Information in the middle of long contexts is missed. Shorter, focused sessions naturally avoid this.

## What is a "session"?

Three candidates:

1. **One conversation** — a session = one chat thread / one CLI invocation
2. **One artifact** — a session = work on exactly one spec file
3. **One action** — a session = one discrete change (evaluate, generate, update)

### Decision: A session is one conversation, scoped to one focused action

A session maps to whatever "one invocation" means for the user's tool — one Claude Code session, one Cursor chat, one API call. Within that conversation, the protocol scopes the AI to **one focused action**: evaluate one artifact, generate one artifact, update the manifest, answer a status question, etc.

This is the natural boundary. The user opens their AI tool, does one thing with chat-spec, and closes it (or moves on to other work). The protocol shouldn't fight this — it should embrace it.

**Why not "one artifact"?** Because some actions span artifacts (initial scan, status overview) and some actions are sub-artifact (fix one section of an existing doc). Tying sessions to artifacts is too rigid.

**Why not "one action"?** This is close to what we're doing, but "action" needs definition. A session can contain evaluation + a small fix if they're tightly coupled. The key constraint is: **don't try to do everything**.

## Session modes

The protocol needs to handle three distinct entry points. The AI's behaviour differs for each.

### Mode 1: Directed — "Fix my architecture doc"

The user has a specific request. The protocol should:

1. Read the manifest (or note its absence)
2. Find the relevant artifact
3. Evaluate it against the rubric (so the user sees current state)
4. Propose specific improvements, with predicted score impact
5. On approval, make the changes
6. Re-evaluate and update the manifest

**Scoping rule:** Stay on the requested artifact. Don't wander. If the AI notices other problems ("your API docs are also outdated"), it notes them in the manifest backlog but doesn't act on them.

### Mode 2: Vague — "Improve my specs"

The user wants help but hasn't picked a target. The protocol should:

1. Read the manifest
2. Evaluate all tracked artifacts (quick pass — checklist scores, not deep analysis)
3. Rank by opportunity: biggest gap, most stale, or easiest win
4. **Present the top 1-3 options to the user and let them choose**
5. Proceed as Mode 1 on the chosen target

The key design choice: **the AI proposes, the user picks**. This avoids the AI making arbitrary priority calls and keeps the user in control. The presentation should be concrete: "Your architecture doc scores 2/5 — it's missing component relationships and data flow. Your conventions doc scores 3/5 — it could add testing patterns. Want me to work on architecture?"

### Mode 3: Unprompted — "Run chat-spec" / no specifics

The user just wants to invoke the protocol. This is the most common case for repeat users. The protocol should:

1. Read the manifest
2. Check what's changed since last session (git diff if available, otherwise timestamps)
3. Identify the highest-priority action:
   - **If first run:** bootstrap (see DQ5)
   - **If artifacts are stale** (code changed significantly since last eval): re-evaluate the most affected artifact
   - **If backlog items exist:** propose the highest-impact backlog item
   - **If everything is current:** report status and suggest the next improvement
4. Present the proposed action and wait for approval

### Priority heuristic for unprompted sessions

When the AI must decide what to work on, it follows this priority order:

1. **Broken:** An artifact that was valid but code changes have made it incorrect (detected via git diff)
2. **Missing:** An artifact in the manifest with no file or a stub
3. **Low-scoring:** The artifact with the lowest rubric score
4. **Stale:** The artifact not evaluated for the longest time
5. **Backlog:** The highest-weight item in the backlog

Tie-breaker: prefer core artifacts (architecture, features, conventions, dev context) over specialised ones.

This heuristic is **prescribed by the protocol** but the user can override it — "I don't care about architecture right now, work on API docs."

## Scope control

The protocol needs explicit instructions to prevent scope creep. AI tools are eager to help with everything they see. Without guardrails, a "fix my architecture doc" session turns into a full project overhaul.

### The scope fence

The protocol tells the AI:

> **In a single session, do ONE of the following:**
> 1. Evaluate one or more artifacts (scoring only, no changes)
> 2. Generate or improve one artifact
> 3. Update the manifest (add/remove artifacts, adjust config)
> 4. Answer a question about project status
>
> **Do not combine generation across multiple artifacts in one session.**

Evaluation can span multiple artifacts because it's lightweight — reading and scoring. Generation is the heavy operation and must be focused.

### What about small fixes?

Sometimes the AI is working on the architecture doc and notices a one-line fix for the conventions doc. Should it be allowed?

**Decision: No.** Note it in the backlog, move on. The cost of "just this one small fix" is scope creep and lost context. The discipline of single-artifact focus is more valuable than the convenience of drive-by fixes.

Exception: manifest updates that are a natural side-effect of the current work (updating scores after evaluation) are fine — they're bookkeeping, not new work.

## How a session ends

A session ends when:

1. **The action is complete:** Artifact generated/updated, scores recalculated, manifest updated
2. **The user says stop:** User ends the conversation or redirects to other work
3. **The AI hits a blocker:** Needs information only the user has, and the user isn't providing it
4. **Context limits:** The conversation is getting long — the AI should wrap up cleanly rather than degrading

### What gets saved at session end

The manifest is the only persistent state. At the end of any session that changes something, the AI must update:

1. **Artifact scores** — if any evaluation happened
2. **Checklist results** — which items passed/failed
3. **Backlog** — any new issues discovered but not addressed
4. **Last evaluated timestamp** — when this artifact was last scored
5. **Session log entry** — one line: date, what was done, result

If the session is interrupted (user closes the window mid-generation), the manifest should still be valid — the worst case is that the manifest is slightly behind (the new artifact exists but the score wasn't updated). The next session can detect and fix this.

### The "clean exit" instruction

The protocol should tell the AI:

> **Before ending a session, always:**
> 1. Update the manifest with any new scores or backlog items
> 2. Summarise what was done and what's next
> 3. If work is incomplete, note the state in the manifest so the next session can pick up

## Edge cases

### User asks for too much

"Rewrite all my docs from scratch" — the protocol should guide the AI to push back:

> "That's a big job. Let me evaluate what you have first, then we can prioritise. Want me to start with a full evaluation?"

### User asks about something outside chat-spec's scope

"Fix my API endpoint" — the AI should recognise this is code work, not spec work, and redirect:

> "That's outside chat-spec's scope — I handle documentation and specs. Want me to look at your API documentation instead?"

### Multiple users / conflicting sessions

Not a V1 concern. The manifest is a file — git handles conflicts the same way it handles any file. If two people run chat-spec simultaneously and both edit the manifest, they'll get a merge conflict. That's fine.

### Very large projects

A project with 10+ artifacts could have a very long evaluation phase. The protocol should support **partial evaluation** — "evaluate just the core artifacts" or "evaluate only what's changed since last time."

## Summary of decisions

| Question | Decision |
|----------|----------|
| What's a session? | One conversation, scoped to one focused action |
| Three entry modes? | Directed, vague, unprompted — each with defined behaviour |
| How to prevent scope creep? | Scope fence: one generation action per session, no cross-artifact drive-by fixes |
| Priority heuristic? | Broken > missing > low-scoring > stale > backlog, with core artifact tie-breaker |
| How does a session end? | Action complete, user stops, blocker hit, or context limits |
| What's saved? | Manifest updates: scores, checklist results, backlog, timestamps, session log entry |
| Clean exit? | Always update manifest and summarise before ending |
