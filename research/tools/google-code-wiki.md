# Google Code Wiki

- **Website:** [codewiki.google](https://codewiki.google/)
- **Status:** Public preview

## What it does

AI-driven documentation platform (powered by Gemini) that automatically generates and continuously updates structured, interactive documentation for code repositories. Produces architecture, class, and sequence diagrams (always current). Includes a Gemini-powered chat agent that uses the wiki as context.

## Strengths

- Always-current (regenerated on each code change)
- Visual diagrams (architecture, class, sequence)
- Integrated chat
- Hyperlinked to source code
- Free for open-source projects

## Limitations

- Public repos only (for now)
- Gemini CLI extension for private repos "in development"
- Google ecosystem dependency
- Not self-hostable

## Relevance to chat-spec

Google Code Wiki is the strongest "living documentation" tool but it's about **auto-generating docs from code**, not about **tracking the quality and maturity of project context for AI**. It generates the kind of content that chat-spec would manage and evaluate.

## Sources

- [Code Wiki](https://codewiki.google/)
- [Google Developers Blog](https://developers.googleblog.com/introducing-code-wiki-accelerating-your-code-understanding/)
- [InfoQ coverage](https://www.infoq.com/news/2025/11/google-code-wiki/)
