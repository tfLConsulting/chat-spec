# Writing AI-Friendly Documentation

Every line in a spec competes for a finite attention budget. Restating what code expresses is not neutral — it actively degrades AI performance.

## What Hurts

| Practice | Impact |
|----------|--------|
| Restating code-inferable information | 2-3% performance decrease — worse than saying nothing |
| Verbose comprehensive prose | Models perform worse on coherent long docs than shuffled fragments |
| Stale or contradictory claims | Incorrect info corrupts all downstream reasoning |
| Too many instructions | At 100+: accuracy drops to 50-98%. At 500+: best model hits 63% |
| Information buried mid-document | Architectural attention bias (positional encoding) loses mid-context content |
| Semantically similar but wrong content | Far more damaging than obviously irrelevant text |
| Large context windows filled | Accuracy loss is mild (~0.5%) but latency spikes up to 719% |

## What Helps

| Practice | Why |
|----------|-----|
| Only document what code can't express | Decisions, rationale, gotchas, non-obvious relationships |
| Front-load the non-obvious | Primacy bias means first content gets the most attention |
| Put critical rules at the start or end | "Lost in the middle" is architectural, not a prompting failure |
| Tables and lists over prose | Structured formats are easier for models to parse than paragraphs |
| Keep specs to 1-3KB | Less context = less latency, more room for actual code |
| Delete rather than update questionable docs | A wrong doc is worse than no doc |
| One concept per document | Smaller, focused docs are retrieved and consumed more reliably |

## Self-Check

After writing or improving a spec, ask:

1. Would removing any line cause an AI to make a mistake? If not, cut it.
2. Does anything here repeat what's visible in code, config, or standard conventions?
3. Is every claim accurate against the current codebase right now?
4. Could this be shorter without losing actionable information?

Research backing these principles is in `research/ai-doc-quality.md`.
