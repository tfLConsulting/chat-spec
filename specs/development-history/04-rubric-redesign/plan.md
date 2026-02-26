# Plan: Rubric Redesign

## Context

The rubrics (designed in DQ4, built in phase 03) have a structural bias: they measure **completeness** (presence of content) but cannot detect problems common in existing documentation — verbosity, staleness, inaccuracy, code restatement. A verbose 20KB doc that checks every box scores 5/5 but may actively hurt AI performance.

This was identified when asking: "Do we score document length?" — which led to the deeper question: "Have we designed the whole system with a new-doc bias?"

New research (stored at `research/ai-doc-quality.md`) confirms:
- Restating code decreases AI performance by 2-3% (ETH Zurich)
- ~150-200 instructions is the practical ceiling for AI tools (Anthropic Claude Code best practices; arXiv:2507.11538 shows degradation at 100+ instructions)
- Stale docs are worse than no docs (Anthropic, Chroma)
- The `current` item at W:1 is severely misweighted given that accuracy is the #2 quality signal
- The rubrics have zero negative signals — they cannot detect any of the problems that existing docs actually have

## Design decision

After analyzing five options (pure reframing, penalty items, two-pass scoring, graduated scales, hybrid), the decision is **hybrid: reframe questions + boost `current` + add universal penalties**.

See `explorations/scoring-options-analysis.md` for full analysis of alternatives.

Rationale:
- **Reframing** makes each item measure value-added rather than presence. Same binary YES/NO mechanism (preserving DQ4's reproducibility finding), different bar.
- **Boosting `current`** from W:1 to W:2 reflects the research showing accuracy/freshness is the #2 quality signal. A stale doc should lose real points.
- **Universal penalty items** catch document-level problems that per-item reframing misses. A doc can have excellent non-obvious component descriptions AND 3KB of code restatement elsewhere — penalties catch the latter.

Why not two-pass (the most accurate option): it adds a second scoring mechanism, requires combining two numbers into one display score, and doubles the rubric surface area. The path from this approach to two-pass is incremental if needed later.

## Changes required

### Change 1: Reframe rubric questions

Every rubric item shifts from "does X exist?" to "does X exist and provide value beyond what code expresses?" Not every item changes — some are already specific enough.

**Reframing principles:**
- Items that ask about listing/describing things get reframed to focus on non-obvious value
- Items that are already specific and objective (e.g. `entry_points`, `persistence`) stay unchanged or get minor tightening
- The `non_obvious` item (W:2) stays as-is — it already captures this
- The `current` item gets strengthened language: "Accurately reflects the current codebase, with no contradictions or stale claims?"

**Files to change:** PROTOCOL.md (core 4 rubrics), RUBRICS.md (other 9 rubrics)

### Change 2: Increase `current` weight from 1 to 2

In every rubric across all 13 artifact types. This increases each rubric's max score by 1.

**Files to change:** PROTOCOL.md, RUBRICS.md

### Change 3: Add two universal penalty items

These apply to ALL 13 artifact types:

| Item | W | Question |
|------|---|----------|
| `code_restatement` | -2 | Contains significant content that restates what an AI would infer from code, configs, or standard conventions? |
| `contradictions` | -3 | Contains claims that contradict the current codebase? |

Scoring rules:
- Penalties are evaluated against the document as a whole, not per-section
- The evaluator must cite specific examples when triggering a penalty
- Raw score is clamped at 0 before level mapping (no negative display scores)
- YES on a penalty item means the problem exists → weight is subtracted

**Files to change:** PROTOCOL.md (rubric section + scoring rules), RUBRICS.md

### Change 4: Recalculate level boundaries

Every rubric's max score increases by 1 (from `current` weight change). The penalty items don't change the max — they only reduce the score. Boundaries need adjustment for all 13 types.

**Approach:** Keep the same semantic meaning for each level (1=missing, 2=basic, 3=solid, 4=strong, 5=complete). Adjust boundaries proportionally to the new max.

**Files to change:** PROTOCOL.md, RUBRICS.md

### Change 5: Update scoring rules in PROTOCOL.md

Section 2 (Evaluation) needs:
- How to score penalty items (reverse polarity: YES = problem exists = subtract weight)
- Requirement to cite examples for penalties
- Clamping rule (raw score minimum is 0)
- Updated "How to score" steps

### Change 6: Update DQ4 and DQ0 exploration docs

Add notes referencing this redesign so the design history stays coherent. These are historical records, so add an addendum rather than rewriting.

**Files to change:** `specs/development-history/02-protocol-design/explorations/dq4-rubric-design.md`, `specs/development-history/02-protocol-design/explorations/dq0-spec-writing-principles.md`

### Change 7: Update manifest.yaml (if exists)

If `.chat-spec/manifest.yaml` exists for the chat-spec project itself, existing scores become incomparable and need re-evaluation.

### Change 8: Update development history

Add this phase to `specs/development-history/README.md`.

## Steps

| # | Step | Depends on |
|---|------|-----------|
| 1 | Write scoring options analysis exploration | — |
| 2 | Draft reframed rubric items for all 13 types | — |
| 3 | Calculate new level boundaries for all 13 types | 2 |
| 4 | Update PROTOCOL.md (core 4 rubrics + scoring rules) | 2, 3 |
| 5 | Update RUBRICS.md (other 9 rubrics) | 2, 3 |
| 6 | Update DQ4 and DQ0 with addenda | 4 |
| 7 | Update development history README | 4, 5 |
| 8 | Re-evaluate chat-spec's own artifacts against new rubrics | 4, 5 |
| 9 | Blank-slate test: hand updated protocol to a fresh agent | 4, 5 |

Steps 1 and 2 can run in parallel. Steps 4 and 5 can run in parallel once 2 and 3 are done.

## Known tradeoffs

1. **Reframed questions are less reproducible** than the current objective ones. Scores may fluctuate +/- 1 level between evaluations. Acceptable for solo dev use; may need tightening later for CI/team use.
2. **`code_restatement` is the fuzziest penalty.** One sentence of framing context is fine; a paragraph restating the middleware chain is not. The line is blurry and will produce scoring noise.
3. **Existing scores become incomparable.** Any project already scored needs re-evaluation. Acceptable for v0.1.
4. **Level boundaries are hand-tuned.** No formula — each rubric's boundaries are set to maintain the semantic meaning of levels 1-5 with the new max scores.

## Status

| # | Step | Status |
|---|------|--------|
| 1 | Scoring options analysis | done |
| 2 | Draft reframed rubrics | done |
| 3 | Calculate level boundaries | done |
| 4 | Update PROTOCOL.md | done |
| 5 | Update RUBRICS.md | done |
| 6 | Update DQ4/DQ0 addenda | done |
| 7 | Update dev history README | done |
| 8 | Re-evaluate chat-spec artifacts | done (arch 5/5, features 5/5, conventions 5/5, dev-context 4/5 — contradictions penalty caught stale phase count) |
| 9 | Blank-slate test | done (Haiku cold-start scored dev-context 4/5 — same level as manual evaluation, different item reasoning. Penalty items clearly understood. Reframed questions scoreable.) |
