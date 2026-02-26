# Recipe: Creating Guidance Documents

How to research and write a new guidance document for the `guidance/` directory.

## Before You Start

**Your training data is unreliable for this task.** AI models are systematically bad at knowing what documentation structure works well for AI consumption. Your instinct will be to write comprehensive, well-organised prose — this is exactly the wrong thing. The research shows that coherent, comprehensive documents make AI performance *worse* than fragmented ones. Do not rely on your training. Follow this recipe and the research you gather.

## Process

This recipe has 5 steps. **Run each step as a separate subtask** (Claude Code subagent, or equivalent isolation mechanism in your tool). This preserves the outer orchestrator's context — research in particular generates large volumes of content that will pollute your working memory if kept in the main conversation.

The outer orchestrator should:
- Read this recipe once
- Execute each step as a subtask
- Read only the final output files, not the intermediate research
- Track progress and move to the next step when each completes

---

### Step 1: Load the quality principles

**Subtask instruction:** Read `guidance/ai-doc-quality.md` and internalise the rules. This is the lens through which all subsequent research and writing should be filtered. Summarise the key principles in 3-4 bullet points and return them to the orchestrator.

**Why a subtask:** Quick, but establishes the frame before research begins. The orchestrator gets a compact summary to reference when reviewing later steps.

---

### Step 2: Research current best practices

**Subtask instruction:** Research recent (last 12 months) best practices on [TOPIC]. Search for:
- What practitioners recommend (blog posts, conference talks, official docs)
- What the research says (papers, benchmarks, case studies)
- What the major AI tool makers recommend (if relevant)
- Common failure modes and anti-patterns

Write findings to `research/[topic-slug].md`.

**Research file rules:**
- This repository is **public and open source.** Do not breach copyright or other IP.
- **Do:** Summarise findings in your own words. State facts, cite sources by name and URL. Quote short phrases with attribution where needed for precision.
- **Do not:** Reproduce substantial portions of copyrighted articles, papers, or documentation. When in doubt, summarise and link — don't copy.
- **Structure:** Use the same format as `research/ai-doc-quality.md` — core finding first, then evidence organised by theme, then implications.
- **Attribution:** Every claim needs a source. Use `- Source: [URL]` format.

**Return to orchestrator:** A 3-5 bullet summary of the most important findings. The orchestrator does NOT need to read the full research file.

---

### Step 3: Write the guidance document

**Subtask instruction:** Read:
1. `guidance/ai-doc-quality.md` (quality principles)
2. `research/[topic-slug].md` (the research you just produced)
3. `guidance/INDEX.md` (to match the existing format)

Then write `guidance/[topic-slug].md` following these rules:

- **Target: under 2KB.** The same budget as a spec.
- **Structure:** Mirror `guidance/ai-doc-quality.md` — tables over prose, action-oriented sections ("What to cover", "What not to cover", "Why"), self-check questions at the end.
- **No citations in the guidance file.** Reference the research file at the bottom for provenance (`Research backing these principles is in research/[topic-slug].md`).
- **Every line must earn its place.** Apply the self-check from `guidance/ai-doc-quality.md` to your own output.
- **Be specific.** "Separate UI rendering from API orchestration" not "maintain clean architecture." Include brief examples of good vs bad where they clarify.
- **Don't restate general knowledge.** If any competent developer or AI model already knows it, cut it. Focus on what gets done wrong in practice.

Also update `guidance/INDEX.md` to add a row for the new guide.

**Return to orchestrator:** The path of the new file and its size in bytes.

---

### Step 4: Evaluate the document

**Subtask instruction:** Read the guidance document you just wrote. Evaluate it against the self-check in `guidance/ai-doc-quality.md`:

1. Would removing any line cause an AI to make a mistake? If not, cut it.
2. Does anything here repeat what a competent developer or AI would already know?
3. Is every claim accurate and backed by the research?
4. Could this be shorter without losing actionable information?

Also check:
- Is it under 2KB?
- Does every section have concrete, specific content (not platitudes)?
- Would an AI reading this actually change its behaviour?

If issues are found, fix them in the guidance file. Report what was changed and the final size.

**Return to orchestrator:** Pass/fail for each check, any changes made, final file size.

---

### Step 5: Final review

The orchestrator (not a subtask) should:
1. Read the final guidance file
2. Read the INDEX.md update
3. Verify the research file exists and isn't storing copyrighted content
4. Present the result to the user for approval

---

## Checklist for the orchestrator

- [ ] Step 1: Quality principles loaded
- [ ] Step 2: Research written to `research/[topic-slug].md`
- [ ] Step 3: Guidance written to `guidance/[topic-slug].md`, INDEX updated
- [ ] Step 4: Self-evaluation passed, document under 2KB
- [ ] Step 5: User approved
