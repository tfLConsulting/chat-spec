# DQ7: The Protocol Document

## The core question

PROTOCOL.md is the actual product. It must be precise enough for consistent AI execution, not so long it fills context, and robust to interpretation variance across different AI tools. What's the structure, tone, length, and content?

## Constraints

From previous DQs:

- **Context budget:** The protocol will be read alongside the manifest (~500 tokens) and at least one artifact. Total protocol should be under 4,000 tokens (~15KB). Ideally closer to 3,000 tokens (~10-12KB).
- **Audience:** AI tools (primary), curious humans (secondary). Written FOR machines, readable BY humans.
- **Must include:** Session model, state management rules, rubrics for all built-in types, first-run and subsequent-run flows.
- **Robustness:** Different AIs (Claude, GPT, Gemini, Llama) should interpret it similarly. This means: explicit instructions, concrete examples, no ambiguity.

## Structure

The protocol needs a clear, sequential structure. AIs follow documents top-to-bottom. The most important information goes first.

### Proposed outline

```
PROTOCOL.md
├── 1. Overview (what this is, 100 words)
├── 2. Quick Reference (the essential flow, scannable)
├── 3. How to Write Effective Specs
│   ├── 3.1 The ETH Zurich Principle (don't restate the obvious)
│   ├── 3.2 Structure for scanning (front-load the non-obvious)
│   ├── 3.3 Capture the delta (decisions, rationale, gotchas)
│   ├── 3.4 Token efficiency
│   └── 3.5 The blank-slate test
├── 4. Session Flow
│   ├── 4.1 First Run
│   └── 4.2 Subsequent Runs
├── 5. State Management
│   ├── 5.1 Directory Structure
│   ├── 5.2 Manifest Schema
│   └── 5.3 Per-Artifact Detail Files
├── 6. Evaluation
│   ├── 6.1 How to Score
│   └── 6.2 Staleness Detection
├── 7. Generation
│   ├── 7.1 Rules
│   └── 7.2 Self-Evaluation
├── 8. Rubrics
│   ├── 8.1 Architecture
│   ├── 8.2 Features
│   ├── 8.3 Conventions
│   ├── 8.4 Development Context
│   └── 8.5 (other built-in types, brief)
├── 9. Artifact Catalogue
└── 10. AI Config Pointer
```

### Why "How to Write Effective Specs" is section 3

This is the most important addition to the protocol, informed by the research grounding (see DQ0). LLMs are trained on human-written documentation — they'll default to writing verbose, obvious prose that restates what they can already see in the code. The ETH Zurich study (2026) showed this kind of documentation *actively harms* AI performance (-2-3% resolve rate, +20% cost). The protocol must teach the AI how to write for its own kind *before* it encounters the session flow or generation rules.

This section also serves as a reference — a "best practices for writing specs that LLMs actually use well" document embedded in the protocol itself. Since the protocol IS what the AI reads, this is the natural home for it.

### Why this order?

1. **Overview + Quick Reference:** The AI knows what it's dealing with and has a cheat sheet for the common case.
2. **Session Flow:** The AI knows what to do RIGHT NOW (first run vs subsequent).
3. **State Management:** The AI knows where to read/write state.
4. **Evaluation + Generation:** The mechanics of the core operations.
5. **Rubrics:** The actual scoring criteria (referenced during evaluation).
6. **Catalogue + Pointer:** Supporting details.

An AI following this document top-to-bottom will orient itself (sections 1-2), know what to do (section 3), know where state lives (section 4), know how to score and generate (sections 5-6), and have the rubrics to execute (section 7).

## Tone

### Options

1. **Instructional** — "You should evaluate the architecture doc by..." Friendly, explanatory.
2. **Mechanical** — "EVALUATE artifact USING rubric. STORE result IN manifest." Direct, unambiguous.
3. **Conversational** — "When the user asks you to improve their specs, here's what to do..." Natural.

### Decision: Instructional-mechanical hybrid

The protocol uses **direct imperative instructions** for procedures and **brief explanatory notes** for context. It reads like well-written API documentation: precise commands with enough explanation to handle edge cases.

```markdown
## Evaluation

Read the artifact file. Score each rubric item as YES or NO.
Calculate the total: sum of weights for YES items.
Map to a level (1-5) using the rubric's level boundaries.

If a rubric item doesn't apply to this project, mark it N/A
and reduce the max score accordingly.

Update the manifest with the new score and timestamp.
Update the detail file with per-item results.
```

Not this (too conversational):
```markdown
When you're evaluating an artifact, you'll want to go through the rubric
items one by one and check if each one is met...
```

Not this (too mechanical):
```markdown
PROCEDURE evaluate(artifact):
  FOR EACH item IN rubric[artifact.type]:
    IF item.applicable: result = YES | NO
    ELSE: result = N/A
  score = SUM(item.weight WHERE item.result == YES)
```

The hybrid tone is precise enough for consistent execution and readable enough for humans who want to understand the protocol.

## Length budget

Target: **12-14KB** (~3,500-4,000 tokens).

| Section | Budget |
|---------|--------|
| Overview + Quick Reference | 400 words (~1.2KB) |
| How to Write Effective Specs | 500 words (~1.5KB) |
| Session Flow | 600 words (~2KB) |
| State Management | 500 words (~1.5KB) |
| Evaluation + Generation | 400 words (~1.2KB) |
| Rubrics (4 core types) | 800 words (~2.4KB) |
| Catalogue + Pointer | 300 words (~1KB) |
| **Total** | **~3,500 words (~12KB)** |

This leaves the remaining built-in rubrics (API docs, data model, deployment, etc.) for an appendix or secondary document. The protocol can reference them:

> "Rubrics for all built-in artifact types are defined in RUBRICS.md. The four core rubrics are included below."

This keeps the main protocol under budget while supporting the full catalogue.

**Alternative: Include all rubrics inline.** This pushes the protocol to ~15KB (~4,500 tokens). Still within budget but larger. The tradeoff: one file vs two files. For V1, let's include core rubrics inline and put the rest in a separate file. The AI only needs the relevant rubric for the current session anyway.

## Examples

AIs follow examples extremely well. The protocol should include 2-3 concrete examples that demonstrate the expected behaviour.

### Example 1: First run output

Show what the AI should produce on first run — the scan summary, proposed manifest, and first artifact. This sets expectations for format and tone.

### Example 2: Subsequent run with a stale artifact

Show the re-orientation flow: reading the manifest, detecting staleness, proposing an action, executing it. Demonstrates the full session cycle.

### Example 3: Status report

Show the format of a status report. This is the simplest interaction and demonstrates the "quick value" case.

Examples should be kept short (3-5 lines of dialogue/output each). They're illustrative, not exhaustive. Place them inline in the relevant sections, not in a separate examples section — the AI should encounter the example right after the instruction.

## Robustness to interpretation variance

Different AIs have different tendencies:

- **Claude:** Follows instructions closely, tends to be thorough, may over-explain
- **GPT:** Good at following structure, may take creative liberties with tone
- **Gemini:** Generally follows instructions, may be more concise
- **Open-source models:** More variable, may miss nuance

### Strategies for robustness

1. **Explicit over implicit.** Don't say "evaluate appropriately" — say "score each rubric item YES or NO." Don't say "update state" — say "write the score to manifest.yaml in the artifacts.<key>.score field."

2. **Numbered steps.** Procedures are numbered lists, not prose paragraphs. AIs follow numbered steps more reliably than extracting steps from prose.

3. **Concrete format specifications.** Show the exact YAML structure, don't describe it in words. Example:
   ```yaml
   artifacts:
     architecture:
       score: 3
       max_score: 5
       status: current
       last_evaluated: 2025-02-20
   ```

4. **Guard rails as instructions.** Don't rely on AIs inferring constraints. State them:
   > "Do NOT generate multiple artifacts in one session."
   > "Do NOT modify the artifact file without user approval."
   > "ALWAYS update the manifest after evaluation."

5. **Default behaviours.** When the AI might reasonably do several things, specify the default:
   > "If the user doesn't specify an artifact, propose the highest-priority option from the backlog."

6. **Minimal decision trees.** Reduce the number of decisions the AI must make. The session flow should be nearly deterministic: read manifest → check state → apply priority heuristic → propose → execute → update.

## What NOT to include

The protocol should NOT include:
- **Philosophy or motivation.** The README handles this. The protocol is operational.
- **Comparison to other tools.** Irrelevant to execution.
- **Installation instructions.** There's nothing to install. The README has the quickstart.
- **Full history of design decisions.** That's the development-history/ directory.
- **Per-project customisation tutorials.** The protocol defines the mechanism; the manifest defines the configuration.

## Draft structure with section previews

### 1. Overview

> chat-spec is a protocol for iteratively evaluating and improving your project's documentation and specifications. You (the AI assistant) follow this protocol when the user asks you to work on their project's specs.
>
> Your job: evaluate documentation against rubrics, identify gaps, and generate improvements — one focused action per session, always with user approval.

### 3. How to Write Effective Specs

This section distills research on how LLMs actually use documentation. It's placed early because it governs all generation and evaluation. Key points:

> **The ETH Zurich principle:** LLM-generated documentation that restates what's already in the code hurts AI performance (Gloaguen et al. 2026). For every statement you write, ask: "Would an LLM infer this correctly from the code alone?" If yes, don't write it.
>
> **Front-load the non-obvious.** LLMs attend most to the beginning and end of documents, least to the middle (Liu et al. TACL 2024). Start every spec with what would be guessed wrong.
>
> **Capture the delta.** Document decisions and their rationale, gotchas, non-obvious relationships, conventions that deviate from defaults. Not the current state — the *surprising* state.
>
> **The blank-slate test.** After writing a spec, ask: could an AI with only this document (no codebase) make correct decisions? If yes, the spec has value. Could it make those same decisions from the code alone? If yes, the spec is noise.

This section will include concrete examples of good vs bad spec content.

### 2. Quick Reference

A boxed/highlighted section with the essential flow:

> **Every session:**
> 1. Read `.chat-spec/manifest.yaml`
> 2. If first run (no manifest): go to section 3.1
> 3. If subsequent run: go to section 3.2
> 4. After any changes: update the manifest
> 5. Before ending: summarise what was done and what's next

### 3.1 First Run

Numbered steps from DQ5: scan → propose manifest → score existing → generate one artifact → create pointer.

### 3.2 Subsequent Runs

From DQ6: read manifest → detect staleness → apply priority heuristic → present options → user chooses → execute → update.

### 4. State Management

From DQ2 and DQ3: directory structure, manifest schema (with example), detail file schema.

### 5. Evaluation

From DQ4: how to apply rubrics, scoring mechanics, N/A handling, staleness detection.

### 6. Generation

Rules: one artifact per session, user approval required, self-evaluation gate (re-score after generating, discard if no improvement).

### 7. Rubrics

From DQ4: the four core rubrics with checklist items, weights, and level boundaries.

### 8. Artifact Catalogue

The full list of built-in types with one-line descriptions. Non-core rubrics referenced in a separate file.

### 9. AI Config Pointer

How to create the thin pointer in CLAUDE.md / .cursorrules / etc. From DQ5.

## Open question: RUBRICS.md

Should non-core rubrics live in a separate file?

**Option A: All in PROTOCOL.md.** Simpler (one file), but pushes to ~15KB.
**Option B: Core in PROTOCOL.md, rest in RUBRICS.md.** Keeps protocol focused, AI loads rubric file only when needed.

**Decision: Option B.** The AI almost never needs all rubrics at once. The protocol includes the 4 core rubrics (~2.4KB). RUBRICS.md has the full set. The protocol references it:

> "Rubrics for all built-in types are defined in RUBRICS.md. Core rubrics (architecture, features, conventions, development context) are included below for convenience."

This keeps the protocol under 12KB and makes the common case (working on a core artifact) require only one file.

## Summary of decisions

| Question | Decision |
|----------|----------|
| Structure? | 9 sections: overview, quick ref, session flow, state, eval, gen, rubrics, catalogue, pointer |
| Tone? | Instructional-mechanical hybrid — imperative steps with brief explanations |
| Length? | Target 10-12KB (~3,000-3,500 tokens) |
| Examples? | 2-3 inline examples, short, in relevant sections |
| Robustness? | Explicit instructions, numbered steps, concrete formats, stated defaults, guard rails |
| Rubrics split? | Core 4 inline, rest in RUBRICS.md |
| What's excluded? | Philosophy, comparisons, installation, history, tutorials |
