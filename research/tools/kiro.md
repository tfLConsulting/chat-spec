# Kiro (AWS)

- **Website:** [kiro.dev](https://kiro.dev/)
- **Source:** Closed (IDE binary). [GitHub repo](https://github.com/kirodotdev/Kiro) for issues/docs.
- **Status:** Public preview (free during preview)

## What it does

An agentic IDE from Amazon (VS Code fork) with spec-driven development as a first-class workflow. Takes natural language prompts and generates user stories with acceptance criteria (EARS notation), technical designs, and trackable tasks. Features "agent hooks" — automated triggers that execute agent actions on file events.

## How it works

Linear 3-step process: Requirements → Design → Tasks. Built on Amazon Bedrock with Claude Sonnet models. Includes "steering files" for persistent project context and native MCP integration.

## Strengths

- Full IDE experience (not a CLI bolt-on)
- Agent hooks are unique — file-event-driven automation
- Deep AWS/Bedrock integration
- Free during preview

## Limitations

- Locked into Kiro's IDE
- Not usable as a standalone library/CLI
- AWS ecosystem dependency
- Still in preview

## Relevance to chat-spec

Kiro embeds SDD into the IDE itself. Different approach — chat-spec is tool-agnostic and works alongside whatever editor/agent you use. Kiro's "steering files" concept is somewhat similar to chat-spec's specs directory though.

## Sources

- [Kiro website](https://kiro.dev/)
- [InfoQ coverage](https://www.infoq.com/news/2025/08/aws-kiro-spec-driven-agent/)
