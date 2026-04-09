---
description: Scaffold a new design system component with correct variants, token bindings, and file structure
argument-hint: <ComponentName> [figma-node-id]
allowed-tools: [Read, Glob, Grep, Write, Edit, Agent]
---

# Add Design System Component

## Arguments
$ARGUMENTS — parse: ComponentName (required, PascalCase), optional Figma node ID

## Steps

1. If Figma node ID provided:
   - Load the `figma-use` skill (MANDATORY before any Figma call)
   - Call `get_design_context` + `get_screenshot` for that node to get exact variants and bindings
2. Check if component already exists in `src/components/`
   # TODO: verify this path — no component directory was detected in the current project
   - If exists → ask: extend with new variants, or abort? (one question, then proceed)
3. Scaffold files:
   - `src/components/<ComponentName>/index.tsx`
   - `src/components/<ComponentName>/<ComponentName>.module.css`
   - Update `src/components/index.ts` barrel export (create if missing)
4. All CSS values must reference tokens from `src/styles/tokens.css` — zero hardcoded values
   # TODO: verify token file path
5. Validate: run `tsc --noEmit` and fix any type errors before reporting done

## Component Path
`src/components/`
# TODO: verify — no component root was detected; update this path to match your project

## Token File
`src/styles/tokens.css`
# TODO: verify — no token file was detected; update this path to match your project

## Variants to Scaffold
Detected from Figma at runtime, or use these sensible defaults:
- Size: `sm` | `md` | `lg`
- Variant: `primary` | `secondary` | `ghost`
- State: `default` | `hover` | `disabled`

## Component Template
```tsx
import styles from './<ComponentName>.module.css';

type <ComponentName>Props = {
  variant?: 'primary' | 'secondary' | 'ghost';
  size?: 'sm' | 'md' | 'lg';
  disabled?: boolean;
  className?: string;
  children: React.ReactNode;
};

export function <ComponentName>({
  variant = 'primary',
  size = 'md',
  disabled = false,
  className,
  children,
}: <ComponentName>Props) {
  return (
    <div
      className={[styles.root, styles[variant], styles[size], className].filter(Boolean).join(' ')}
      aria-disabled={disabled}
    >
      {children}
    </div>
  );
}
```

## CSS Module Template
```css
.root {
  /* base styles using tokens */
  font-size: var(--font-size-md);
  padding: var(--space-2) var(--space-4);
}
.primary { background: var(--color-primary); }
.secondary { background: var(--color-secondary); }
.ghost { background: transparent; }
.sm { font-size: var(--font-size-sm); padding: var(--space-1) var(--space-2); }
.lg { font-size: var(--font-size-lg); padding: var(--space-3) var(--space-6); }
```

## Rules
- Follow PascalCase naming for component files and exports
- All CSS values must reference CSS custom properties from the token file
- No icon package installs — use localhost sources from Figma MCP if assets are needed
- Load `figma-use` skill before every Figma MCP call — never call Figma tools directly
