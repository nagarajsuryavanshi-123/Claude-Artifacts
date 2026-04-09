---
description: Create a complete Figma design system from scratch — variables, tokens, color styles, typography, spacing, radius, shadows, and component foundations.
argument-hint: <figma-file-url> [--dry-run]
allowed-tools: [Read, Glob, Grep, Write, Agent]
---

# Create Design System in Figma

## Arguments
$ARGUMENTS — parse:
- `figma-file-url` (required) — target Figma file where the design system will be built
- `--dry-run` — print what would be created without writing to Figma

---

## Overview

This command builds a production-ready design system directly inside Figma, including:
- **Color variables** — primitives, semantic aliases, light/dark modes
- **Typography variables** — font families, sizes, weights, line heights
- **Spacing tokens** — 4px base scale
- **Border radius tokens** — none through full
- **Shadow/elevation tokens** — layered elevation system
- **Component foundations** — Button, Input, Badge scaffolded with token bindings

---

## Phase 1 — Load Figma Skill (MANDATORY)

Load the `figma-use` skill before ANY Figma tool call. Never call Figma MCP tools directly.

Then load the `figma:figma-generate-library` skill for full design system generation guidance.

---

## Phase 2 — Inspect Existing Codebase (run in parallel with Phase 1)

Use Glob + Grep to detect:
- Token file: `tokens.css`, `theme.ts`, `design-tokens.json`, `tailwind.config.*`
- Existing color values, font families, spacing scales
- Component directory for variant reference

If token file found → extract real values to seed Figma variables.
If not found → use the default scale defined below.

---

## Phase 3 — Create Variable Collections in Figma

Call `get_variable_defs` first to check if collections already exist. If they do, confirm before overwriting.

### Collection 1: Primitives (raw values — no semantics)

**Colors — Neutral**
```
neutral-0:   #ffffff
neutral-50:  #f9fafb
neutral-100: #f3f4f6
neutral-200: #e5e7eb
neutral-300: #d1d5db
neutral-400: #9ca3af
neutral-500: #6b7280
neutral-600: #4b5563
neutral-700: #374151
neutral-800: #1f2937
neutral-900: #111827
neutral-950: #030712
```

**Colors — Brand (Blue)**
```
blue-50:  #eff6ff
blue-100: #dbeafe
blue-200: #bfdbfe
blue-300: #93c5fd
blue-400: #60a5fa
blue-500: #3b82f6
blue-600: #2563eb
blue-700: #1d4ed8
blue-800: #1e40af
blue-900: #1e3a8a
```

**Colors — Semantic Primitives**
```
green-500: #22c55e
red-500:   #ef4444
yellow-500:#eab308
orange-500:#f97316
```

**Typography Primitives**
```
font-family-sans:  Inter, ui-sans-serif, system-ui, sans-serif
font-family-mono:  JetBrains Mono, ui-monospace, monospace

font-size-xs:   10
font-size-sm:   12
font-size-base: 14
font-size-md:   16
font-size-lg:   18
font-size-xl:   20
font-size-2xl:  24
font-size-3xl:  30
font-size-4xl:  36
font-size-5xl:  48

font-weight-regular: 400
font-weight-medium:  500
font-weight-semibold:600
font-weight-bold:    700

line-height-tight:  1.25
line-height-snug:   1.375
line-height-normal: 1.5
line-height-relaxed:1.625
```

**Spacing Primitives (4px base)**
```
space-0:   0
space-1:   4
space-2:   8
space-3:   12
space-4:   16
space-5:   20
space-6:   24
space-8:   32
space-10:  40
space-12:  48
space-16:  64
space-20:  80
space-24:  96
```

**Border Radius Primitives**
```
radius-none: 0
radius-sm:   2
radius-md:   4
radius-lg:   8
radius-xl:   12
radius-2xl:  16
radius-full: 9999
```

**Shadow Primitives**
```
shadow-sm:  0 1px 2px rgba(0,0,0,0.05)
shadow-md:  0 4px 6px rgba(0,0,0,0.07), 0 2px 4px rgba(0,0,0,0.06)
shadow-lg:  0 10px 15px rgba(0,0,0,0.1), 0 4px 6px rgba(0,0,0,0.05)
shadow-xl:  0 20px 25px rgba(0,0,0,0.1), 0 8px 10px rgba(0,0,0,0.04)
shadow-2xl: 0 25px 50px rgba(0,0,0,0.25)
```

---

### Collection 2: Semantic Tokens (aliases → reference primitives)

Create with **Light** and **Dark** modes:

**Color — Background**
```
bg-default        Light: neutral-0      Dark: neutral-950
bg-subtle         Light: neutral-50     Dark: neutral-900
bg-muted          Light: neutral-100    Dark: neutral-800
bg-emphasis       Light: neutral-800    Dark: neutral-100
```

**Color — Foreground**
```
fg-default        Light: neutral-900    Dark: neutral-50
fg-muted          Light: neutral-500    Dark: neutral-400
fg-subtle         Light: neutral-400    Dark: neutral-500
fg-on-emphasis    Light: neutral-0      Dark: neutral-900
```

**Color — Brand**
```
brand-default     Light: blue-600       Dark: blue-400
brand-subtle      Light: blue-50        Dark: blue-900
brand-emphasis    Light: blue-700       Dark: blue-300
brand-fg          Light: neutral-0      Dark: neutral-0
```

**Color — Border**
```
border-default    Light: neutral-200    Dark: neutral-700
border-muted      Light: neutral-100    Dark: neutral-800
border-emphasis   Light: neutral-400    Dark: neutral-500
```

**Color — State**
```
state-success     Light: green-500      Dark: green-500
state-error       Light: red-500        Dark: red-500
state-warning     Light: yellow-500     Dark: yellow-500
state-info        Light: blue-500       Dark: blue-400
```

**Typography Semantic**
```
text-xs      → font-size-xs
text-sm      → font-size-sm
text-base    → font-size-base
text-md      → font-size-md
text-lg      → font-size-lg
text-xl      → font-size-xl
text-2xl     → font-size-2xl
text-3xl     → font-size-3xl

body-font    → font-family-sans
code-font    → font-family-mono
```

**Spacing Semantic**
```
gap-xs   → space-1
gap-sm   → space-2
gap-md   → space-4
gap-lg   → space-6
gap-xl   → space-8

padding-sm  → space-2
padding-md  → space-4
padding-lg  → space-6
```

**Radius Semantic**
```
rounded-sm   → radius-sm
rounded-md   → radius-md
rounded-lg   → radius-lg
rounded-full → radius-full
```

**Elevation**
```
elevation-1  → shadow-sm
elevation-2  → shadow-md
elevation-3  → shadow-lg
elevation-4  → shadow-xl
```

---

## Phase 4 — Create Text Styles

Create Figma text styles for each typography combination:

| Style Name            | Font         | Size | Weight   | Line Height |
|-----------------------|--------------|------|----------|-------------|
| Display/2XL           | Inter        | 48   | Bold 700 | 1.25        |
| Display/XL            | Inter        | 36   | Bold 700 | 1.25        |
| Heading/H1            | Inter        | 30   | Semibold 600 | 1.375   |
| Heading/H2            | Inter        | 24   | Semibold 600 | 1.375   |
| Heading/H3            | Inter        | 20   | Semibold 600 | 1.5     |
| Heading/H4            | Inter        | 18   | Medium 500   | 1.5     |
| Body/LG               | Inter        | 18   | Regular 400  | 1.5     |
| Body/MD               | Inter        | 16   | Regular 400  | 1.5     |
| Body/SM               | Inter        | 14   | Regular 400  | 1.5     |
| Body/XS               | Inter        | 12   | Regular 400  | 1.5     |
| Label/LG              | Inter        | 16   | Medium 500   | 1.5     |
| Label/MD              | Inter        | 14   | Medium 500   | 1.5     |
| Label/SM              | Inter        | 12   | Medium 500   | 1.5     |
| Code/MD               | JetBrains Mono | 14 | Regular 400  | 1.625   |
| Code/SM               | JetBrains Mono | 12 | Regular 400  | 1.625   |

All text styles must bind to semantic typography variables, not raw values.

---

## Phase 5 — Create Effect Styles

Create Figma effect styles for each shadow:
- `Elevation/1` → shadow-sm
- `Elevation/2` → shadow-md
- `Elevation/3` → shadow-lg
- `Elevation/4` → shadow-xl
- `Elevation/5` → shadow-2xl

---

## Phase 6 — Scaffold Foundation Components

After variables and styles are set, scaffold these base components in a dedicated **"Foundation"** page:

### Button
Variants: Size (sm, md, lg) × Style (primary, secondary, ghost, danger)
All fills, strokes, radii, and text bound to semantic tokens.

### Input / Text Field
Variants: State (default, focused, error, disabled)
Border → border-default, bg → bg-default, text → fg-default.

### Badge / Tag
Variants: Style (default, success, error, warning, info)
Each bound to the matching `state-*` color tokens.

### Color Swatch Grid
A documentation frame showing all primitive and semantic color tokens, organized by collection.

---

## Phase 7 — Final Report

```
DESIGN SYSTEM CREATED IN FIGMA
================================
File: <figma-file-url>

Variable Collections:
  ✓ Primitives     — N variables
  ✓ Semantic       — N variables (Light + Dark modes)

Text Styles:        N styles
Effect Styles:      N styles

Components:
  ✓ Button         (4 styles × 3 sizes)
  ✓ Input          (4 states)
  ✓ Badge          (5 styles)
  ✓ Color Swatches (documentation frame)

Run /ds-sync-tokens <figma-url> to pull these tokens into your codebase.
```

---

## Rules
- ALWAYS load `figma-use` skill before any Figma MCP tool call
- Never hardcode raw values in Figma component fills — always bind to a variable
- Semantic tokens must alias primitives — never reference raw hex in the Semantic collection
- Light mode is the default mode; Dark mode is the alternate
- If `--dry-run` → print all collections and token counts but make zero Figma API calls
- If any collection already exists → show a diff and ask before overwriting (one question)
