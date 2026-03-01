# Writing Design System Specs

Agents generate new components from training data by default. Without a machine-readable manifest, they ignore existing patterns. The fix is structured metadata, not prose.

## What to Cover

| Content | Example |
|---------|---------|
| Component manifest (props, types, defaults) | `Button: { variant: 'primary' \| 'destructive', size: 'sm' \| 'md' }` |
| Variant selection rules | "`destructive` only with confirmation dialog" |
| Composition constraints | "DataTable composes TableHeader + TableRow; never nest Card in Card" |
| Semantic design tokens | `color-action-primary` not `blue-500`; W3C format (`$value`, `$type`) |
| Accessibility per component | "IconButton requires `aria-label`; Modal traps focus, returns on close" |
| Upstream deviations | "Our Button sets `asChild` true — differs from shadcn default" |
| Responsive breakpoints | "Nav collapses to hamburger at `md` (768px)" |
| File structure template | "`components/[name]/index.tsx` + `[name].stories.tsx`" |

## What Not to Cover

| Content | Why |
|---------|-----|
| Visual descriptions ("rounded, blue") | Encoded in tokens and props; restating drifts |
| Component source in docs | Agents read source directly; bloats context |
| Generic overviews | Inferable from code |
| Variant screenshots | Not parseable by text-based agents |

## Good vs Bad

Good: `Alert: { severity: 'info' | 'warning' | 'error' }. 'error' for blocking failures. Pair with dismiss except 'error'.`
Bad: `Alert displays a message to the user in a coloured box.`

## Self-Check

1. Is there a machine-readable manifest (JSON/YAML), not just prose?
2. Does every entry include props with types, defaults, and when-to-use?
3. Are accessibility requirements explicit per component, not generic WCAG refs?
4. Are composition rules documented — which components nest, which must not?
5. Would removing any line cause an agent to pick the wrong component or variant?

Research backing these principles is in `research/design-system-specs.md`.
