# Plan: Build the Deliverables

## Context

Protocol design (02-protocol-design/) is complete. 9 explorations cover every design question. Now we synthesize them into the actual product: PROTOCOL.md, RUBRICS.md, and README.md.

## Deliverables

| File | What | Size target |
|------|------|-------------|
| `PROTOCOL.md` | The product. What AIs read and follow. | 12-14KB |
| `RUBRICS.md` | Non-core artifact rubrics (9 types). | ~3-4KB |
| `README.md` | Landing page for humans. | ~2-3KB |

## Steps

1. Write PROTOCOL.md section by section (10 sections, synthesizing DQ0-DQ7)
2. Write RUBRICS.md (9 non-core rubrics, format from DQ4)
3. Write README.md (from DQ8's draft)
4. Blank-slate test: hand protocol to Haiku with no context, run 4 test scenarios
5. Dogfood on chat-spec itself
6. Dogfood on other real projects
7. Final polish (token budget, YAML validation, cross-references)

## Status

| Step | Status |
|------|--------|
| 1. PROTOCOL.md | done (~16KB, 2534 words, 10 sections) |
| 2. RUBRICS.md | done (13 rubrics, all types covered) |
| 3. README.md | done (~2.5KB, quickstart + FAQ) |
| 4. Blank-slate test | done (3/3 Haiku tests passed, 1 fix applied: score scale clarification) |
| 5. Dogfood (chat-spec) | done (isolated agent, no context bleed. Created manifest, 4 artifacts tracked, generated dev-context at 4/5) |
| 6. Dogfood (other projects) | deferred (user will test on their other projects) |
| 7. Final polish | done (YAML validation passed, README fixed, file sizes verified) |
