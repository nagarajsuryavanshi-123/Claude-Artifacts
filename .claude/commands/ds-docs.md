---
description: Generate a component documentation page from Figma design context and existing code
argument-hint: <ComponentName> [figma-node-id] [--output <path>]
allowed-tools: [Read, Glob, Grep, Write, Agent]
---

# Generate Component Documentation

## Arguments
$ARGUMENTS — parse:
- `ComponentName` (required, PascalCase)
- `figma-node-id` (optional — pulls design context and screenshot from Figma)
- `--output <path>` (optional — override output directory)

## Steps

1. Read the existing component at `src/components/<ComponentName>/`
   # TODO: verify this path — no component directory was detected in the current project
   - Read `index.tsx` for props, variants, and component logic
   - Read `<ComponentName>.module.css` for styling details
2. If Figma node ID provided:
   - Load the `figma-use` skill (MANDATORY before any Figma call)
   - Call `get_design_context` for structured design info
   - Call `get_screenshot` for visual reference image
3. Generate documentation covering:
   - **Overview** — what this component is and when to use it
   - **Props / Variants** — all props with TypeScript types and default values
   - **Usage Examples** — code snippet for each variant combination
   - **Design Token References** — which tokens (`--color-*`, `--space-*`, etc.) this component uses
   - **Accessibility Notes** — keyboard interactions, aria attributes, color contrast requirements
   - **Figma Screenshot** — embedded if captured
4. Determine output path:
   - If `--output` provided → use that path
   - Otherwise → `src/components/<ComponentName>/README.md`
   # TODO: verify component path

## Output Format

Plain Markdown (no Storybook or VitePress detected — update this if your project adds a docs tool):

```markdown
# ComponentName

Brief description of what this component does and when to use it.

## Usage

\`\`\`tsx
import { ComponentName } from '@/components/ComponentName';

<ComponentName variant="primary" size="md">Label</ComponentName>
\`\`\`

## Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| variant | 'primary' \| 'secondary' \| 'ghost' | 'primary' | Visual style |
| size | 'sm' \| 'md' \| 'lg' | 'md' | Component size |
| disabled | boolean | false | Disables interaction |

## Design Tokens Used

- `--color-primary` — primary background
- `--space-2` — internal padding
- `--font-size-md` — label font size

## Accessibility

- Supports keyboard focus via Tab
- Use `aria-label` when no visible label is present
- Ensure `--color-primary` meets WCAG AA contrast on its background

## Figma Reference

[Screenshot or node link added here if Figma node ID was provided]
```

## Component Directory
`src/components/`
# TODO: verify — update to match your actual component root

## Rules
- Load `figma-use` skill before every Figma MCP call — never call Figma tools directly
- Do not fabricate prop types — read them from the actual source file
- If the component file does not exist yet, say so and suggest running `/ds-add-component <ComponentName>` first
