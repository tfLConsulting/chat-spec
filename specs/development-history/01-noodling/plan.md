# Development Plan: Design Phase

## Context

The noodling phase (ideas + decision points + explorations) is done. The big decision is made: **chat-spec is a protocol, distributed as a GitHub repo, executed by whatever AI the user already has.** The README is the landing page, the PROTOCOL.md is the product.

Previous work lives in:
- `ideas/` — 5 idea sketches
- `decision-points.md` — 10 DPs extracted and priority-ranked
- `explorations/` — 8 DPs explored + "the README is the tool" synthesis

## What needs designing now

The protocol needs to be concrete enough that an AI can follow it. That means working through these design questions in order, because each one feeds the next.

---

### DQ1: The session model

**The problem:** AI tools can't do everything in one go. Context windows fill up. Users have limited patience. A project might need 6 artifacts improved but the user only has time for one today.

**Questions to answer:**
- What's a "session"? One conversation? One artifact? One improvement?
- How does the protocol tell the AI to scope its work?
- What if the user gives a specific request ("fix my architecture doc") vs. a vague one ("improve my specs")?
- How does the AI decide what to work on when the user doesn't specify?
- How does a session end? What gets saved? What carries over?

**Output:** `explorations/dq1-session-model.md`

---

### DQ2: State and memory

**The problem:** Between sessions, the AI forgets everything. The project needs to remember what was found, what was started, what still needs doing. But where does that state live?

**Questions to answer:**
- What state needs to persist between sessions?
- Does it all go in the manifest, or is that too much for one file?
- Is there a "problems found" / "backlog" concept? Where does it live?
- What about work-in-progress — an artifact that's been started but not finished?
- How does the AI on session N know what the AI on session N-1 found and decided?
- How big can this state get before it's a problem for context windows?

**Output:** `explorations/dq2-state-and-memory.md`

---

### DQ3: The manifest design

**The problem:** The manifest is the heart of the state model. It needs to be the right size and shape — enough information for an AI to pick up where the last one left off, not so much that it fills the context window or becomes unreadable.

**Questions to answer:**
- What's the schema? (Fields, types, nesting)
- Per-artifact: what's stored? (path, score, checklist results, notes, backlog items?)
- Project-level: what's stored? (last run, protocol version, project type?)
- How does it handle the "problems found" backlog?
- What's the manifest's size budget? (Should fit comfortably in any AI's context)
- YAML vs JSON vs something else? (Revisit DP4 with "AI readability" as the primary lens)

**Depends on:** DQ1, DQ2

**Output:** `explorations/dq3-manifest-design.md`

---

### DQ4: Rubric design

**The problem:** Rubrics are the scoring mechanism AND the content guidance. They need to be precise enough for reproducible yes/no answers, comprehensive enough to define quality, and compact enough to ship in or alongside the protocol.

**Questions to answer:**
- What does a rubric look like, concretely? (Revisit the checklist idea from DP3 with more rigour)
- How many items per artifact type? What's the sweet spot?
- Are items always yes/no, or do some need yes/partial/no?
- How do you handle "not applicable" items?
- Who writes the rubrics? (We do, for defaults. Users can extend.)
- How do rubrics guide generation? ("To reach level 3, answer these questions...")
- Should rubrics live in the protocol doc, in the manifest, or in separate files?
- Write draft rubrics for the 4 core artifact types (architecture, features, conventions, dev context)

**Depends on:** DQ3 (to know where rubrics live)

**Output:** `explorations/dq4-rubric-design.md`

---

### DQ5: First-run experience

**The problem:** User pastes the URL, AI reads the protocol. Now what? The project has no manifest, no specs directory, maybe a README, maybe a CLAUDE.md, maybe nothing. The first run needs to be impressive and useful without being overwhelming.

**Questions to answer:**
- What does the AI do first? (Scan project? Ask questions? Propose a manifest?)
- How does it detect what already exists? (Existing CLAUDE.md, .cursorrules, docs/, etc.)
- Does it try to score what's there, or skip straight to building?
- How much does it try to do in one session? (Manifest + one artifact? Just manifest?)
- What's the "wow moment"? The thing that makes the user think "this is worth doing again"?
- How does it create the "thin pointer" in CLAUDE.md (or equivalent) so future sessions auto-discover chat-spec?
- What if the user already has a CLAUDE.md they don't want overwritten?

**Depends on:** DQ1, DQ2, DQ3

**Output:** `explorations/dq5-first-run.md`

---

### DQ6: Subsequent-run experience

**The problem:** Session 2+ is where the iterative value lives. The AI needs to quickly understand the current state, figure out what's most valuable to work on, and either follow the user's lead or propose something.

**Questions to answer:**
- How does the AI re-orient? (Read manifest, read backlog, assess state)
- User says "run chat-spec" with no specifics — what happens?
- User says "improve my API docs" — what happens?
- User says "what's the status?" — what happens?
- How does re-scoring work? (Score everything? Only stale things? Only what the user asks about?)
- How does the AI decide what to propose? (Biggest gap? Most stale? Easiest win?)
- What about existing AI config files — if the user has a CLAUDE.md with their own stuff in it, how does chat-spec coexist?

**Depends on:** DQ1, DQ2, DQ3, DQ5

**Output:** `explorations/dq6-subsequent-runs.md`

---

### DQ7: The protocol document

**The problem:** This is the actual product. PROTOCOL.md needs to be precise enough that different AIs follow it consistently, but not so long that it fills context windows or confuses the AI.

**Questions to answer:**
- Structure and sections
- Tone (instructional? conversational? mechanical?)
- Length budget (how many tokens before AIs struggle?)
- How to handle the dual-audience problem (humans will read it too)
- Should it include examples? (Probably yes — AIs follow examples well)
- How to make it robust to interpretation variance across different AI tools
- Write a first draft (or at least a detailed outline)

**Depends on:** DQ1-DQ6 (this synthesises everything)

**Output:** `explorations/dq7-protocol-doc.md` + first draft of `PROTOCOL.md`

---

### DQ8: The README

**The problem:** The README is the landing page. It needs to make a human want to try chat-spec and give them the one-liner to do it.

**Questions to answer:**
- Structure and content
- The "paste this" quickstart — exact wording
- How much of the protocol to include vs. link to
- What signals credibility? (Research basis? Examples? Testimonials?)
- Write a first draft

**Depends on:** DQ7

**Output:** first draft of `README.md`

---

## Sequence

```
DQ1 (session model) ──┐
                       ├──> DQ3 (manifest) ──> DQ4 (rubrics) ──┐
DQ2 (state/memory) ───┘                                        │
                                                                ├──> DQ7 (protocol) ──> DQ8 (README)
DQ5 (first run) ───────────────────────────────────────────────┘
DQ6 (subsequent runs) ─────────────────────────────────────────┘
```

DQ1 and DQ2 can be explored in parallel. DQ5 and DQ6 can be explored once DQ3 exists (and they feed back into DQ3 if the manifest design doesn't support what they need). DQ7 synthesises everything. DQ8 is last.

## Status

| DQ | Topic | Status | Notes |
|----|-------|--------|-------|
| 1 | Session model | not started | |
| 2 | State and memory | not started | |
| 3 | Manifest design | not started | blocked by DQ1, DQ2 |
| 4 | Rubric design | not started | blocked by DQ3 |
| 5 | First-run experience | not started | blocked by DQ3 |
| 6 | Subsequent runs | not started | blocked by DQ5 |
| 7 | Protocol document | not started | blocked by DQ1-DQ6 |
| 8 | README | not started | blocked by DQ7 |
