**Chat-spec** is a framework for preparing projects to work well with AI.

The core insight is this: when you bring an AI into a project — whether it's Claude, Copilot, Cursor, or whatever comes next — the quality of what it produces depends almost entirely on what it can understand about your project. Most people dump an AI into a codebase and hope for the best. Chat-spec takes the opposite approach: you deliberately build and maintain an intelligence layer that gives any AI the context it needs to do good work.

This isn't a one-shot scaffolding tool. The key difference from things like GitHub's Spec Kit or SpecPulse is that chat-spec is designed to be run repeatedly. Each time, it examines what's already there, evaluates how good it is, and proposes improvements — with your consent. The project's AI-readiness improves incrementally because no single pass can capture everything, and projects evolve.

**What chat-spec actually manages:**

A `specs/` directory containing the structured knowledge an AI needs to understand your project. This includes things like:

- **Feature manifest** — what the project does, broken down into discrete capabilities
- **Architecture** — how the system is structured, what talks to what
- **Design system** — visual and component standards
- **Constraints** — rate limits, budget limits, infrastructure boundaries, both current and projected
- **Standards** — coding conventions, testing expectations, review processes
- **Development history** — what was built, when, why, and what was learned

**What makes chat-spec different:**

At the heart of it is a **spec manifest** — a single file that acts as an index of everything in the specs directory, with a maturity rubric for each piece. Every artifact is tracked with:

- Whether it exists
- How complete and useful it is (on a defined scale)
- When it was last validated
- What "good" looks like for that specific artifact (the rubric)
- Where the validation evidence lives

This means that when you run `chat-spec`, it doesn't blindly regenerate everything. It reads the manifest, sees that your architecture doc is solid but your feature manifest hasn't been touched in two months and was only ever a rough list, and focuses its effort there. It asks before it changes anything.

**The workflow:**

You run `chat-spec` and it does three things:

1. **Examines** — looks at your project structure, your codebase, your existing specs, and the manifest
2. **Evaluates** — scores each artifact against its rubric, flags gaps, identifies what's stale
3. **Proposes** — suggests specific improvements, creates missing artifacts from templates, offers to expand thin ones

Each run makes the intelligence layer a little better. Over time, a project that started with nothing builds up a rich, validated set of specifications that any AI can use to do genuinely good work — not because the AI is smarter, but because it has better material to work with.

**Who it's for:**

Anyone who works with AI assistants on real projects and has noticed the pattern: the AI is only as good as the context you give it. Chat-spec is the systematic answer to "how do I give it better context, and how do I keep that context accurate as the project changes?"