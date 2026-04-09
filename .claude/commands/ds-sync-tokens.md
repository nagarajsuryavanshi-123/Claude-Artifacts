---
description: Sync design tokens from Figma to the project token file
argument-hint: <figma-url> [--dry-run]
allowed-tools: [Read, Write, Edit, Agent]
---

# Sync Design Tokens from Figma

## Arguments
$ARGUMENTS — parse: Figma URL (required), `--dry-run` (preview changes without writing)

## Steps

1. Load the `figma-use` skill (MANDATORY before any Figma call)
2. Spawn a sub-agent to call `get_variable_defs` on the Figma file to pull all variable collections
3. Read the current token file at `src/styles/tokens.css`
   # TODO: verify this path — no token file was detected in the current project
4. Diff: which tokens are new, changed, or removed?
5. If `--dry-run`: print the diff and stop
6. If not dry-run: apply changes to `src/styles/tokens.css`
   - New tokens → append as CSS custom properties under the appropriate `:root` block
   - Changed values → update in place
   - Removed tokens → comment out with `/* REMOVED: [date] */` — never silently delete
7. Report: N added, N changed, N flagged for removal

## Token Collections to Sync
(Detected from Figma at runtime — expected collections: Primitives, Color, Spacing, Typography, Radius, Shadows)

## Token File Format
```css
:root {
  /* Color */
  --color-primary: #value;
  --color-secondary: #value;

  /* Spacing */
  --space-1: 4px;
  --space-2: 8px;

  /* Typography */
  --font-size-sm: 12px;
  --font-size-md: 16px;
}
```

## Rules
- Never hardcode hex values — always write as CSS custom properties (`--token-name: value`)
- Preserve all existing comments and custom properties not in Figma
- Flag any token in code that no longer exists in Figma instead of auto-deleting
- Load `figma-use` skill before every Figma MCP call — never call Figma tools directly
