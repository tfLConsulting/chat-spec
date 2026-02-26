# chat-spec

Markdown-only project. No code, no build, no dependencies. The product is `PROTOCOL.md`, `RUBRICS.md`, and `README.md`.

## Working on this project

IMPORTANT: Every statement in a spec must earn its place. Ask: "Would an AI infer this from the code?" If yes, delete it. Restating code *hurts* AI performance (ETH Zurich research). Read `@guidance/ai-doc-quality.md` before writing or evaluating any spec.

- Specs target 1-3KB. Tables and lists over prose. No preamble, no empty sections.
- One artifact per session when following the chat-spec protocol. Quality degrades generating more.
- `specs/development-history/` is provenance — read for context, rarely edit.
- `research/` files are in a **public repo**. Summarise and link; reproduce no copyrighted content.
- New guidance docs: follow the recipe at `specs/guidance-recipe.md`.

## Conventions

- YAML keys: lowercase with hyphens (`dev-context`). Rubric item IDs: underscores (`non_obvious`).
- Manifest scores: display level 1-5, never raw totals.
- Status enum: `current`, `stale`, `missing`, `wip`, `draft` — no others.

## Testing

Hand the protocol to an AI with zero context and a test project. Does it follow the steps? That's the test.

<!-- chat-spec:start -->
## Specs
This project uses chat-spec for documentation quality tracking.
See .chat-spec/manifest.yaml for current state.
When asked to work on specs/documentation, follow the protocol at PROTOCOL.md.
<!-- chat-spec:end -->
