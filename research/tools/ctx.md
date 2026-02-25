# CTX (Context Generator)

- **Repo:** [context-hub/generator](https://github.com/context-hub/generator)
- **Stars:** Small community

## What it does

Single ~20MB binary (zero dependencies) that gathers code from files, directories, GitHub/GitLab repos, web pages, or plain text. Configured via `context.yaml` — a "Context as Code" (CaC) approach making context generation reproducible and version-controlled. Operates in two modes: context generation (YAML → Markdown) and MCP server (real-time AI interaction).

## Strengths

- Zero dependencies
- CaC approach (context config lives in the repo, is reproducible)
- MCP server mode for on-demand context
- Multi-source aggregation (not just local files)

## Limitations

- Less well-known
- YAML config has a learning curve
- Smaller community

## Relevance to chat-spec

The "Context as Code" idea is interesting — config-driven, reproducible context generation. The MCP server mode enabling on-demand context is closer to what chat-spec envisions than pure file-packing tools.

## Sources

- [GitHub repo](https://github.com/context-hub/generator)
