# DP2: Does chat-spec generate specs, or only evaluate them?

## The spectrum

This isn't binary. There's a spectrum:

1. **Pure evaluator** — scores what exists, reports gaps, never writes
2. **Evaluator + pointer** — scores, reports, and tells you exactly what to write (but you write it)
3. **Evaluator + drafter** — scores, reports, and offers to draft improvements (you review and approve)
4. **Evaluator + writer** — scores, reports, and writes improvements (you consent but don't review in detail)
5. **Autonomous improver** — runs on a schedule, improves continuously, you review periodically

Most ideas sit around 3. But let's actually think about what each end of the spectrum means.

## The case for evaluate-only (position 1-2)

The ETH Zurich research is the strongest argument here. LLM-generated project instructions actually *hurt* AI performance compared to human-written ones. The study found ~4% improvement from human-written AGENTS.md but degradation from LLM-generated ones.

If chat-spec generates mediocre specs, it's actively making AI assistants worse. That's the opposite of the mission.

**The evaluate-only pitch:** "Chat-spec doesn't write your docs. It tells you honestly how good they are and exactly what's missing. You write the docs — or you have your AI write them during normal development — and chat-spec keeps score."

This is appealing because:
- It sidesteps the quality problem entirely
- It's simpler to build
- It creates a clear feedback loop: write → score → improve → score again
- It positions chat-spec as a quality tool, not a generation tool

But there's a fatal problem: **nobody will do it.** The whole reason people use AI is that they don't want to write documentation. A tool that says "your docs are bad, go fix them" will get ignored after the first run. The gap between "knowing what's wrong" and "fixing it" is where most improvement tools die.

## The case for generate (position 3-4)

If chat-spec doesn't generate, it's just a to-do list. People already know their docs are incomplete — they don't need a tool to tell them.

The value is in the doing. Specifically:

- **First-run value**: A new user gets something immediately. "Here's a draft architecture doc based on what I see in your codebase." That's tangible.
- **Incremental improvement**: "Your feature manifest is at 2/5. Here's what I'd change to get it to 3. Want me to make those changes?" This is actionable.
- **Reducing friction**: The barrier to good docs isn't knowledge, it's effort. AI can absorb that effort.

But the ETH Zurich problem is real. How do you generate without generating garbage?

## Resolving the tension: quality-controlled generation

What if the answer is: **generate, but with the evaluation as the quality gate?**

The workflow:
1. Chat-spec evaluates your existing specs (or lack thereof)
2. It proposes specific improvements with drafts
3. You review and approve (position 3 on the spectrum)
4. Chat-spec scores the result against the rubric
5. **If the generated content doesn't actually improve the score, it says so.** "I drafted this, but honestly it only gets you from 2 to 2. Here's what would actually move the needle, but it requires information only you have: [specific questions]."

This is the key insight: **generation and evaluation as a feedback loop within a single run.** The AI doesn't just generate and declare victory. It generates, evaluates its own output, and is honest about whether it actually helped.

### The "information the AI can't have" problem

The ETH Zurich finding makes sense when you think about it: LLM-generated project context is generic because the LLM doesn't actually know the project-specific things that matter. It can see code structure but not the *reasons* behind decisions. It can list features but not the *constraints* that shaped them.

Good documentation captures human knowledge that isn't in the code:
- Why was this architecture chosen? (Not what it is — the AI can see that.)
- What are the actual constraints? (Budget, timeline, team skills — not in the repo.)
- What was tried and abandoned? (Critical context, never in code.)
- What are the implicit conventions? (The stuff everyone "just knows.")

This suggests a generation approach that's **conversational, not autonomous:**
- AI drafts what it can from code analysis
- Explicitly flags what it can't know and asks
- The human fills in the unique knowledge
- AI structures and polishes the combined output
- Evaluation confirms the result is genuinely useful

This is basically Idea 4 (The Bootstrap Loop) applied to individual artifacts.

## What about the "position 5" end — autonomous improvement?

Running chat-spec on a schedule or in CI, automatically improving docs...

This is seductive but dangerous. Autonomous generation without human review is how you get:
- Generic filler that dilutes good content
- Hallucinated architecture descriptions
- Docs that drift from reality because no human validated them
- The ETH Zurich problem at scale

**Strong lean: never autonomous. Always human-in-the-loop for generation. Evaluation can be autonomous (CI scoring), but generation requires consent.**

## Where I'm landing

**Position 3 with a self-evaluation check:**
- Chat-spec evaluates and generates
- All generation requires user consent (propose → approve → execute)
- Generated content is self-evaluated: "this draft would move you from 2 to 3 because X"
- Where the AI can't improve things without human knowledge, it asks specific questions rather than generating filler
- Evaluation can run without generation (a "score only" mode for CI)

This gives you:
- First-run value (generation)
- Ongoing value (evaluation + targeted improvement)
- Quality control (self-evaluation + human review)
- CI story (evaluation-only mode)

---

## Sub-questions this raises

- How good is AI at self-evaluating its own generated documentation? (Could be tested empirically)
- Should the "specific questions" be stored somewhere so the user can answer them asynchronously? (Between sessions)
- Is "score only" mode a first-class command or just a flag?
- How do you handle the case where the user approves something that the AI scored low? (User override — respect it, note it)
