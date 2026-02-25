# DP6: How does chat-spec handle change detection?

## Why it matters

"Your docs are stale" is one of the most useful things chat-spec can say. But "stale" requires knowing what changed.

## The options

### A. Git-based

Use git log/diff to see what code changed since the last spec validation.

**Good:** Precise. Can tell you "these 15 files changed since your architecture doc was last validated."
**Bad:** Couples to git. Doesn't work in non-git environments. Requires the manifest to track git state (commit hashes).

### B. Timestamp-based

Compare file modification timestamps. "This spec was last modified 3 months ago. These source files were modified yesterday."

**Good:** Works everywhere. No git dependency.
**Bad:** Imprecise. "Files changed" doesn't mean "docs are wrong." And timestamps can be misleading (touched but not meaningfully changed).

### C. AI-based (no explicit change detection)

Don't track changes. Each run, the AI reads the specs and the code and judges freshness directly. "This architecture doc describes 3 services but I see 4 in the code. Stale."

**Good:** Catches staleness that git/timestamps miss (e.g., docs that were never right, not just stale). No infrastructure dependency.
**Bad:** Expensive (re-reads everything every time). Might miss subtle staleness. Can't say "X changed since your last review" — only "X doesn't match."

### D. Hybrid

Use git/timestamps when available for efficiency (skip re-scoring unchanged artifacts). Fall back to AI-based for everything else.

## The connection to DP3 (scoring)

If scoring is checklist-based (from DP3), then "staleness" is partially captured by the rubric. A checklist item like "Has the description been validated against the actual code?" will score "no" if the docs have drifted. That's AI-based staleness detection built into the scoring mechanism.

But it's expensive — you're re-running the full checklist to detect staleness, when a quick git check could tell you "nothing changed, skip this."

## Landing

**D — Hybrid.** Git when available, timestamps as fallback, AI-based as the ground truth.

Priority order:
1. If git is available and neither the spec file nor relevant source files have changed → use cached score
2. If timestamps suggest changes → re-score
3. AI evaluation is always the final arbiter — it catches drift that file changes don't

The manifest stores:
- `last_validated`: date of last scoring
- `last_validated_commit`: git hash (optional, if git available)
- `last_file_hash`: hash of the spec file content (portable, no git needed)

This way chat-spec works everywhere but is smarter when git is present.
