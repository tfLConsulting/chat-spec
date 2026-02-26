# Scoring Options Analysis

## The problem

The DQ4 rubrics (phase 02) use additive binary scoring: YES/NO items with weights 1-3, summed to levels 1-5. Every item asks "does X exist?" This measures **completeness** — rewarding the presence of content regardless of its quality.

This creates a structural blind spot. The rubrics cannot detect problems common in existing documentation:

| Problem | How common | Current rubric coverage |
|---------|-----------|----------------------|
| Restates what code expresses | Very common | `non_obvious` (W:2) hints at it, doesn't penalize |
| Stale / contradicts code | Very common in existing docs | `current` (W:1) — weakest item in every rubric |
| Verbose prose, low info density | Very common | Nothing |
| Internal contradictions | Common after multiple edits | Nothing |
| Aspirational (documents intent, not reality) | Common | Nothing |

Research (see `research/ai-doc-quality.md`) shows these problems actively harm AI performance. A verbose 5/5 doc that checks every box may perform worse for AI tools than a terse 3/5 doc containing only non-obvious information.

## Five options evaluated

### Option 1: Reframe questions (keep YES/NO)

Change "Lists all major components?" → "Correctly describes components not inferable from code?"

**Strengths:**
- No new mechanism. Same scoring math.
- Directly addresses the completeness bias — each item now measures value-added.

**Weaknesses:**
- "Correctly" and "not inferable from code" require judgment. DQ4 chose binary specifically because binary on *objective* questions is most reproducible (Arize AI, arXiv:2503.16974). Binary on *subjective* questions loses that advantage.
- Can't catch document-level problems. A doc with excellent non-obvious component descriptions AND 3KB of code restatement elsewhere scores well on the reframed question but still has a document-level quality problem.

**Test cases:**
- Fresh spec: Works well — same as current if spec follows protocol.
- Old README: Items score lower (correct) but no gradient — user gets "everything is wrong" with no path forward.
- Verbose AI doc: Correctly scores lower on items where content is code-inferable. Misses document-level bloat.
- Terse 500-byte doc: Scores well on covered items (correct) but still penalized for missing items.

### Option 2: Add penalty items (negative weights)

Keep current items, add items like `code_restatement` (W:-2), `contradictions` (W:-3).

**Strengths:**
- Backwards compatible with current positive items.
- Catches the worst offenders (stale, contradictory docs).

**Weaknesses:**
- Awkward math. Current Architecture max: 17. If a doc scores 17 positive but triggers both penalties: 17 - 5 = 12 (level 4). The penalty may not matter enough. Making penalties heavier (-5, -8) creates asymmetric ranges.
- Negative raw scores are possible (3 positive - 5 penalties = -2). Requires clamping rules.
- Evaluator does two different cognitive tasks: look for presence (positive), then look for problems (negative). Inconsistent evaluation mode.
- Verbose AI doc still scores ~13/17 positive minus ~2 penalty = 11. Still level 3-4. Penalties alone don't fix the completeness bias in the positive items.

### Option 3: Two-pass scoring (coverage + quality)

Pass 1: Current rubrics unchanged (coverage). Pass 2: Universal quality rubric (deductions). Two separate scores or a composite.

**Strengths:**
- Produces the most accurate results across all four test cases.
- Cleanly separates "what's present" from "what's wrong."
- Old README: Coverage 3/5, Quality 2/5 → user sees "you have content but it's low quality." Very actionable.
- Verbose AI doc: Coverage 5/5, Quality 2/5 → perfectly captures the problem.
- Terse 500-byte doc: Coverage 2/5, Quality 5/5 → "what you have is excellent, you need more." Also ideal.

**Weaknesses:**
- Doubles the rubric surface area (26 rubrics for 13 types). Mitigated if quality pass is universal.
- How do two scores combine into one manifest display number? Options: separate display, min(), weighted average, quality as gate. No clean answer.
- Significant complexity increase for v0.1.

### Option 4: Graduated scale (0-2 per item)

Each item scored 0 (absent/harmful), 1 (present but low quality), 2 (good).

**Strengths:**
- Most expressive. Captures quality gradient naturally.
- Handles all test cases with nuance.

**Weaknesses:**
- Directly contradicts the DQ4 research. Binary on objective questions achieves near-perfect reproducibility (arXiv:2503.16974, 50 runs). A 0-2 scale triples the judgment surface on every item.
- Every item now requires a quality judgment, multiplying subjectivity across 8-10 items per type.
- Most noisy option. Opposite of the binary design philosophy.

### Option 5: Hybrid (reframe + boost `current` + universal penalties)

Reframe questions toward value. Increase `current` from W:1 to W:2. Add 2-3 universal penalty items.

**Strengths:**
- Reframing makes each item measure value-added (addressing completeness bias).
- Boosted `current` makes accuracy matter proportionally to research findings.
- Penalties catch document-level problems that per-item reframing misses.
- Keeps binary scoring (preserving DQ4 reproducibility finding).
- Natural evolution path to two-pass (Option 3) if needed later.

**Weaknesses:**
- Reframed questions are less reproducible than current objective ones.
- `code_restatement` penalty is the fuzziest item — "significant content" is subjective.
- Level boundaries need recalculation for all 13 types.
- Existing scores become incomparable.

**Test cases:**
- Fresh spec: 4-5/5 (same — penalties not triggered).
- Old README: 1-2/5 (`current` W:2 fails, restatement penalty, reframed questions score lower). Correct.
- Verbose AI doc: ~3/5 (restatement penalty + some reframed questions fail). Correct.
- Terse 500-byte doc: ~3/5 (high-weight reframed items pass, no penalties). Significantly better than current 1-2/5.

## Decision: Option 5 (Hybrid)

**Why not Option 3?** It produces the best results but adds a second scoring mechanism, requires combining two numbers, and doubles rubric surface area. The path from Option 5 → Option 3 is incremental. Can't easily go backwards.

**Why not Option 1 alone?** Misses document-level problems. A doc can pass reframed questions on individual sections while containing 3KB of code restatement elsewhere.

**Why not Option 4?** Contradicts the reproducibility research that grounded DQ4. Over-engineered for v0.1.

## Specific design decisions

### Penalty item design

Only two penalties. Simplicity over comprehensiveness:

| Item | W | Question | Rationale |
|------|---|----------|-----------|
| `code_restatement` | -2 | Contains significant content that restates what an AI would infer from code, configs, or standard conventions? | ETH Zurich: restating code hurts performance 2-3% |
| `contradictions` | -3 | Contains claims that contradict the current codebase? | Anthropic: context poisoning is the most damaging failure mode |

`contradictions` is weighted heavier because wrong information is more damaging than redundant information. A doc that says "we use PostgreSQL" when the project uses MySQL will cause the AI to generate PostgreSQL-specific code. A doc that restates the Express middleware chain wastes tokens but doesn't cause wrong behavior.

A third penalty (`density` — empty sections, filler, preamble) was considered and deferred. It's a real problem but less common and less damaging than the other two. Can be added in a future version.

### Penalty scoring rules

- YES on a penalty means the problem exists → weight is subtracted
- Evaluated against the document as a whole, not per-section
- Evaluator must cite specific examples ("The section on middleware restates server.ts lines 12-45")
- Raw score clamped at 0 before level mapping (no negative display scores)

### Why boost `current` to W:2

Research ranks accuracy/freshness as the #2 quality signal for AI tools (behind relevance, ahead of information density). At W:1, `current` is the weakest item in every rubric — a stale doc loses only 1 point. At W:2, staleness costs the same as missing a W:2 item like `data_flow` or `non_obvious`. This reflects research reality.

### Subjectivity is acceptable

The DQ4 decision for binary-on-objective was correct for v0.1. But the research since then shows that objective completeness scoring produces results that are directionally wrong — rewarding docs that hurt AI performance. Binary-on-quality-oriented is less reproducible but directionally more accurate.

For the primary user (solo dev iterating on their own docs), directional accuracy matters more than cross-session reproducibility. A score that fluctuates 3-4 between sessions but correctly identifies that a verbose doc is worse than a terse one is more useful than a perfectly stable score that rewards bloat.

If chat-spec later serves CI gates or cross-team comparisons, the scoring mechanism may need tightening. This is a known future concern, not a v0.1 blocker.
