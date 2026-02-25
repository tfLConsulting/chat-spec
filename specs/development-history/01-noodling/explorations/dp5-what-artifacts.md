# DP5: What artifacts does it track?

*Mostly covered in DP10 (the artifact catalogue section). This is the remaining question: is the catalogue fixed or dynamic?*

## The real question

DP10 proposed a catalogue of ~13 artifact types across 4 tiers. But should this catalogue be:

**A. Built-in and closed.** Chat-spec knows about these 13 types. You can enable/disable them but not add new ones.

**B. Built-in and extensible.** Chat-spec ships with 13 types. You can add custom artifact types with custom rubrics.

**C. Fully dynamic.** No built-in types. Chat-spec discovers what's needed from the project and proposes artifact types.

## Why extensibility matters

Real projects have real weirdness. Examples of artifacts that don't fit the default catalogue:

- **Regulatory compliance docs** (healthcare, finance) — mapping code to regulatory requirements
- **Localisation guide** — how i18n works, what's translated, what isn't
- **Algorithm documentation** — ML models, trading strategies, scoring algorithms
- **Migration guide** — how to move from v1 to v2, breaking changes
- **Vendor integration runbooks** — step-by-step for specific third-party setups
- **Incident response patterns** — what to do when X breaks

These are all "things an AI should know to work effectively on this project." They just don't fit neat categories.

## The ESLint parallel again

ESLint has built-in rules and plugin rules. The built-in rules work for most people. Plugins extend it for React, TypeScript, accessibility, etc. This is clearly Option B.

## How custom artifacts would work

```yaml
custom_artifacts:
  regulatory_mapping:
    path: specs/regulatory-mapping.md
    description: "Maps code modules to HIPAA compliance requirements"
    rubric:
      - id: requirements_listed
        question: "Does it list all applicable regulatory requirements?"
        weight: 3
      - id: code_mapping
        question: "Does it map requirements to specific code modules?"
        weight: 2
      - id: gap_analysis
        question: "Does it identify compliance gaps?"
        weight: 2
      - id: audit_trail
        question: "Does it describe how compliance is verified?"
        weight: 1
```

The user defines the artifact type, provides a path and rubric, and chat-spec treats it like any built-in type — scores it, tracks it, proposes improvements.

## Should the AI propose custom artifacts?

This is the interesting extension. During a scan, the AI might notice: "This project has a `strategies/` directory with complex trading algorithms. None of the default artifact types cover algorithm documentation. Should I add a custom artifact for this?"

**Lean: Yes, but gently.** The AI can suggest, the user decides. This is part of the "first run" prescriptiveness from DP10 — chat-spec proposes what matters, including things that don't fit the catalogue.

---

## Landing: Option B — built-in and extensible

- 13 built-in types across 4 tiers
- Users can add custom types with custom rubrics
- AI can propose custom types based on project analysis
- Custom types are first-class — scored, tracked, and improved like built-in ones
