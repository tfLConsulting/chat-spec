# DP3: How does scoring work?

## Why this matters

Scoring is the core differentiator. The original idea explicitly calls out a "maturity rubric" as the thing that makes chat-spec different from tools that just generate docs. If the scoring is bad — unreliable, meaningless, or annoying — the whole thing falls apart.

## The two fundamental approaches

### Per-artifact scoring (Ideas 1, 2, 4)

Each document gets its own score. "Your architecture doc is 3/5."

**How it works:**
- Each artifact type has a rubric (1-5 scale with descriptions)
- AI reads the artifact + relevant code, assigns a score
- Score is stored in the manifest

**Good because:**
- Actionable — you know exactly what to improve
- Transparent — the rubric tells you what each level means
- Simple to implement and explain

**Bad because:**
- 1-5 is coarse. Your architecture doc might be at 3 for months because getting from 3 to 4 requires a big jump. No signal of incremental progress.
- Comparing across artifact types is misleading. Is a 3/5 architecture doc better or worse than a 3/5 feature manifest?
- The score of any individual artifact doesn't tell you about overall project AI-readiness.

### Holistic/dimensional scoring (Idea 3)

The project gets scores across dimensions. "Your structural clarity is 78/100."

**How it works:**
- Dimensions (structural clarity, feature documentation, architecture docs, etc.) are scored 0-100
- Each dimension has a weight
- Combined into an overall score

**Good because:**
- Finer granularity — can show progress within a dimension
- Weights express priorities — "architecture matters more than design system for this project"
- Overall score is a useful headline number

**Bad because:**
- 0-100 is a false precision problem. Can the AI really distinguish 67 from 72? Probably not.
- Weights are hard to set correctly
- Less actionable — "your feature documentation is 35/100" doesn't tell you what to do about it

## The reproducibility problem

Here's the thing nobody talks about: **AI scoring is noisy.**

If you give the same document to Claude twice with the same rubric, you might get 3/5 one time and 4/5 the next. The larger the scale, the worse this gets. On a 1-5 scale, you get maybe ±0.5 variance. On a 1-100 scale, you get ±10 or more.

This matters because trend tracking — "your docs are improving" — requires scores to be comparable across runs. If the noise is larger than the improvement signal, the trend is meaningless.

**Options for handling this:**
1. **Accept the noise.** Use a coarse scale (1-5) where the noise is smaller than the steps. Accept that scores won't change every run.
2. **Average across runs.** Score multiple times per run and average. Expensive (multiple AI calls) but more stable.
3. **Structured rubrics.** Make rubrics so specific that there's only one reasonable answer. "Does the architecture doc include a diagram? Yes/no. Does it list all services? Yes/no." Score by counting yes/no answers.
4. **Relative scoring.** Don't score absolutely. Just say "better than last time" or "worse than last time." Less informative but more reliable.

### Option 3 is the most interesting

What if rubrics aren't prose descriptions but checklists?

```yaml
architecture:
  rubric:
    - id: components_listed
      question: "Does it list all major system components?"
      weight: 2
    - id: relationships_shown
      question: "Does it describe how components communicate?"
      weight: 2
    - id: data_flow
      question: "Does it show how data flows through the system?"
      weight: 1
    - id: rationale
      question: "Does it explain why this architecture was chosen?"
      weight: 1
    - id: deployment_model
      question: "Does it describe how the system is deployed?"
      weight: 1
    - id: validated
      question: "Has the description been validated against the actual code?"
      weight: 3
  max_score: 10  # sum of weights
```

The AI answers each question yes/no (maybe with a confidence level). The score is the sum of weights for "yes" answers. This is:
- **More reproducible** — binary questions are easier to answer consistently
- **More actionable** — you can see exactly which items are missing
- **Self-documenting** — the rubric IS the definition of quality
- **Progressive** — each yes/no is a concrete step forward

The 1-5 summary score still works as a human-readable mapping:
- 0-2: Level 1
- 3-4: Level 2
- 5-6: Level 3
- 7-8: Level 4
- 9-10: Level 5

But the underlying data is the checklist, not the number.

## The "overall score" question

Do you need a single number for the whole project?

**For CI and dashboards:** Yes. "AI-readiness: 67%" is useful as a headline.
**For the solo dev:** Maybe not. They care about "what should I improve next?", not a number.
**For trend tracking:** Yes, but only if it's reliable (see noise problem).

**Lean:** Calculate it but don't emphasise it. The per-artifact checklist scores are the primary interface. The overall score exists for the CI use case and as a vanity metric.

### How to calculate overall

If each artifact has a checklist score, the overall is just:
- Sum of all artifact scores / sum of all max scores × 100
- Optionally weighted by artifact importance

But this means adding a new artifact (that starts at 0) *decreases* the overall score. Is that right? Maybe. "You have gaps" is a real signal. But it could be demoralising: "I added an API docs artifact and my score dropped."

Possible fix: only count artifacts that exist in the manifest. The manifest is the declaration of "what matters for this project." If you add API docs to the manifest, you're saying "API docs matter" — and yes, the score drops until you write them. That's honest.

## Scoring frequency

When does scoring happen?

- **Every run:** Scores all artifacts fresh. Most accurate but slow (many AI calls).
- **On change:** Only re-scores artifacts whose files changed since last run. Efficient but misses "staleness" (code changed, docs didn't).
- **On demand:** User asks to score a specific artifact. Least overhead but relies on user discipline.

**Lean:** Score everything every run but cache intelligently. If the artifact file hasn't changed AND the relevant code hasn't changed (git-based check), skip re-scoring and use the cached result. This gives accuracy without unnecessary AI calls.

But this reintroduces git coupling (see DP6). Without git, you can fall back to file modification timestamps, which is less precise but workable.

---

## Where I'm landing

**Checklist-based rubrics as the foundation.** Each artifact type has a rubric defined as weighted yes/no questions. The AI answers the checklist. The score is derived from the checklist, not assigned directly.

**1-5 summary level for human readability.** Maps from checklist scores. Used in the manifest and conversation.

**Overall project score available but not emphasised.** Calculated from artifact scores. Exists for CI. Not the primary interface.

**Score on every run, with caching.** Use file timestamps or git (if available) to skip re-scoring unchanged artifacts.

---

## Sub-questions

- Who writes the rubric checklists? (Built-in defaults? User customises? AI proposes?)
- How many checklist items per artifact type is right? Too few = not useful. Too many = tedious. (Gut: 5-10 items per artifact type)
- Should the checklist answers be yes/no or yes/partial/no? (Partial adds expressiveness but reduces reproducibility)
- How do you handle rubric items that aren't applicable? ("Does it describe the deployment model?" for a library that isn't deployed)
- Can the AI propose new rubric items based on what it finds? ("Your project uses GraphQL — I'd suggest adding a rubric item for schema documentation")
