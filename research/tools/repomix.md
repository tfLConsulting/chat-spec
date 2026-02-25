# Repomix

- **Website:** [repomix.com](https://repomix.com/)
- **Repo:** [yamadashy/repomix](https://github.com/yamadashy/repomix)
- **Stars:** ~70,000
- **Available via:** npm, yarn, bun, Homebrew

## What it does

Packs an entire repository into a single AI-friendly file (XML by default). Features token counting per file, .gitignore respect, `--compress` mode using Tree-sitter to extract key code elements, Secretlint security checks, and MCP server integration.

## Strengths

- De facto standard for "dump your repo into an LLM context window"
- Massive community (70k stars)
- Excellent features: compression, security scanning, token counting
- MCP integration
- Actively maintained
- Nominated for JSNation Open Source Awards 2025

## Limitations

- One-shot tool (not iterative)
- For large repos, even compressed output may exceed context windows
- XML output format is opinionated

## Relevance to chat-spec

Repomix is a **context packing** tool — it creates a snapshot for immediate use. Chat-spec is about **building and maintaining structured knowledge** over time. Complementary rather than competitive. A project could use chat-spec to maintain its specs and Repomix to pack them for a session.

## Sources

- [Repomix website](https://repomix.com/)
- [GitHub repo](https://github.com/yamadashy/repomix)
- [npm](https://www.npmjs.com/package/repomix)
