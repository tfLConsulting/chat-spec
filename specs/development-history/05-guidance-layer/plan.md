# Plan: Guidance Layer

## Context

The protocol tells AIs what gets scored but not how to write well. The research (`research/ai-doc-quality.md`) has the knowledge but it's a 137-line academic report with citations — too long for in-context use. Writers need terse, actionable guidance.

Separately, PROTOCOL.md and RUBRICS.md have no repo URL and no update mechanism. When AIs cache the protocol to `.chat-spec/`, there's no way to refresh. An AI asked to "update" might rewrite from memory rather than fetching the actual files.

## Deliverables

| File | What | Size |
|------|------|------|
| `guidance/INDEX.md` | Lists available guides | ~350 bytes |
| `guidance/ai-doc-quality.md` | Distilled research: what hurts, what helps, self-check | ~1.5KB |
| PROTOCOL.md changes | Guidance reference (near top), Section 10: repo URL + update mechanism (bottom) | +~20 lines |
| RUBRICS.md changes | Guidance reference (near top), repo URL (bottom) | +~10 lines |

## Changes

### Change 1: Create guidance/ directory

The guidance directory holds reference material for writing better specs. Not scored artifacts. Not rubrics. Research-distilled rules.

- `INDEX.md`: one table, one line per guide, 2-sentence framing of what guidance is
- `ai-doc-quality.md`: "What Hurts" table, "What Helps" table, 4-question self-check. No citations (those live in `research/ai-doc-quality.md`)

### Change 2: Add guidance reference to PROTOCOL.md

Unnumbered `## Guidance` section between "Quick Reference" and "## 1. Writing Effective Specs". Preserves existing 1-9 numbering. Points to `guidance/INDEX.md`.

### Change 3: Add Section 10 to PROTOCOL.md

New `## 10. Keeping chat-spec Current` at the end. Contains:
- Repo URL
- Update instruction: delete cached files, fetch fresh from repo
- Explicit: "Do NOT rewrite these files from memory"
- Fallback: if network unavailable, tell user to copy manually

### Change 4: Add guidance reference to RUBRICS.md

One-line `**Guidance:**` note after the `**Scoring:**` paragraph, same bold-key format.

### Change 5: Add repo URL to RUBRICS.md

`## Source` section at end with repo URL. Points to PROTOCOL.md section 10 for update instructions (no duplication).

### Change 6: Update development history and dev-context

- Add phase 05 row to `specs/development-history/README.md`
- Update `specs/dev-context.md`: phase count, phase 05 entry, roadmap

## Steps

| # | Step | Depends on |
|---|------|-----------|
| 1 | Create guidance/INDEX.md and ai-doc-quality.md | — |
| 2 | Add guidance reference to PROTOCOL.md | 1 |
| 3 | Add Section 10 to PROTOCOL.md | — |
| 4 | Add guidance reference to RUBRICS.md | 1 |
| 5 | Add repo URL to RUBRICS.md | — |
| 6 | Update development history README | — |
| 7 | Update specs/dev-context.md | — |

## Status

| # | Step | Status |
|---|------|--------|
| 1 | Create guidance files | done |
| 2 | PROTOCOL.md guidance reference | done |
| 3 | PROTOCOL.md Section 10 | done |
| 4 | RUBRICS.md guidance reference | done |
| 5 | RUBRICS.md repo URL | done |
| 6 | Development history README | done |
| 7 | specs/dev-context.md | done |
