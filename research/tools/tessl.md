# Tessl

- **Website:** [tessl.io](https://tessl.io/)
- **Status:** Closed beta (Framework) / Open beta (Spec Registry)

## What it does

Two products:
1. **Tessl Framework** — uses specifications to define intent before coding, storing specs as "long-term memory" for agents. Detects spec-code drift and reconciles.
2. **Tessl Spec Registry** — holds 10,000+ specs for external libraries, preventing API hallucinations and version mixups.

Specs can be `@generate` (generate code from spec) or `@describe` (document existing code). Code generated from specs is sometimes marked `// GENERATED FROM SPEC - DO NOT EDIT`.

## Strengths

- The registry of 10,000+ library specs is unique — no other tool offers pre-built context for third-party dependencies
- Spec-code drift detection is the closest thing to "iterative quality tracking" in the SDD space
- Spec versioning and evaluation built in

## Limitations

- Commercial/closed beta
- Less open-source community momentum
- Newer entrant

## Relevance to chat-spec

Tessl's **drift detection** is conceptually similar to chat-spec's "evaluate what's stale" capability. The registry concept (pre-built context for libraries) is interesting but orthogonal. Tessl is about spec→code, chat-spec is about code→context quality.

## Sources

- [Tessl website](https://tessl.io/)
- [Martin Fowler on Tessl](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html)
- [Tessl docs](https://docs.tessl.io)
