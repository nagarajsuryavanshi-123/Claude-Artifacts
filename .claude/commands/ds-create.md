---
description: Build a complete Figma design system from your brand colors and fonts — generates all variables, tokens, text styles, shadows, and base components.
argument-hint: <figma-file-url>
allowed-tools: [Read, Write, Agent]
---

# Create Design System in Figma

## Arguments
$ARGUMENTS — parse: `figma-file-url` (required Figma file URL to write into)

---

## Step 1 — Collect Brand Inputs

Before doing anything else, ask the user for all of the following in a single prompt.
Do NOT proceed until answers are received.

```
I'll build your design system in Figma. Please provide:

BRAND COLORS
  Primary   hex: ___________  (main CTA, buttons, links)
  Secondary hex: ___________  (supporting accent)
  Tertiary  hex: ___________  (optional third accent, press Enter to skip)
  Neutral   hex: ___________  (optional base gray, press Enter to use auto-derived)
  Success   hex: ___________  (optional, default: #22c55e)
  Warning   hex: ___________  (optional, default: #eab308)
  Error     hex: ___________  (optional, default: #ef4444)
  Info      hex: ___________  (optional, default: #3b82f6)

FONTS
  Primary font   : ___________  (headings + body, e.g. Inter)
  Monospace font : ___________  (code blocks, e.g. JetBrains Mono, press Enter to skip)

MODES
  Dark mode? (yes / no): ___
```

Store all answers. Use defaults for anything left blank.

---

## Step 2 — Derive Full Color Scales

For each provided brand color (Primary, Secondary, Tertiary, Neutral), generate a
**10-step tint/shade scale** (50 → 950) using this algorithm:

- **50**  → mix brand color 5% into white
- **100** → mix brand color 10% into white
- **200** → mix brand color 20% into white
- **300** → mix brand color 35% into white
- **400** → mix brand color 55% into white
- **500** → **the brand color itself** (anchor)
- **600** → darken brand color 10%
- **700** → darken brand color 22%
- **800** → darken brand color 36%
- **900** → darken brand color 50%
- **950** → darken brand color 62%

If Neutral was skipped, auto-derive it by desaturating the Primary color 90%.

Also always include a fixed **Base** scale:
```
base-white: #ffffff
base-black: #000000
```

---

## Step 3 — Build Variable Collections in Figma

Load `figma-use` skill (MANDATORY before any Figma tool call), then write to Figma.

Check with `get_variable_defs` if collections already exist — if yes, ask before overwriting.

---

### Collection A — Primitives

One variable per derived color step, named:

```
primary-50, primary-100 … primary-950
secondary-50 … secondary-950
tertiary-50 … tertiary-950      (skip if tertiary not provided)
neutral-50 … neutral-950
success-500, warning-500, error-500, info-500
base-white, base-black
```

Typography primitives (using the fonts the user provided):
```
font-family-primary:   <user primary font>
font-family-mono:      <user mono font or "inherit">

font-size-xs:    10
font-size-sm:    12
font-size-base:  14
font-size-md:    16
font-size-lg:    18
font-size-xl:    20
font-size-2xl:   24
font-size-3xl:   30
font-size-4xl:   36
font-size-5xl:   48
font-size-6xl:   60

font-weight-regular:  400
font-weight-medium:   500
font-weight-semibold: 600
font-weight-bold:     700

line-height-none:     1
line-height-tight:    1.25
line-height-snug:     1.375
line-height-normal:   1.5
line-height-relaxed:  1.625
line-height-loose:    2

letter-spacing-tight:  -0.05em
letter-spacing-normal:  0
letter-spacing-wide:    0.05em
letter-spacing-wider:   0.1em
```

Spacing primitives (4px base):
```
space-0: 0     space-1: 4     space-2: 8     space-3: 12
space-4: 16    space-5: 20    space-6: 24    space-8: 32
space-10: 40   space-12: 48   space-16: 64   space-20: 80
space-24: 96
```

Border radius:
```
radius-none: 0   radius-xs: 2   radius-sm: 4   radius-md: 6
radius-lg: 8     radius-xl: 12  radius-2xl: 16 radius-3xl: 24
radius-full: 9999
```

Shadow:
```
shadow-xs:  0 1px 2px rgba(0,0,0,0.04)
shadow-sm:  0 1px 3px rgba(0,0,0,0.06), 0 1px 2px rgba(0,0,0,0.04)
shadow-md:  0 4px 6px rgba(0,0,0,0.07), 0 2px 4px rgba(0,0,0,0.05)
shadow-lg:  0 10px 15px rgba(0,0,0,0.08), 0 4px 6px rgba(0,0,0,0.04)
shadow-xl:  0 20px 25px rgba(0,0,0,0.09), 0 8px 10px rgba(0,0,0,0.04)
shadow-2xl: 0 25px 50px rgba(0,0,0,0.20)
```

---

### Collection B — Semantic Tokens

All semantic tokens **alias primitives** — never reference raw hex.

Create **Light** mode (and **Dark** mode if user said yes).

**Background**
| Token | Light | Dark |
|---|---|---|
| bg-default | base-white | neutral-950 |
| bg-subtle | neutral-50 | neutral-900 |
| bg-muted | neutral-100 | neutral-800 |
| bg-emphasis | neutral-800 | neutral-100 |
| bg-primary | primary-50 | primary-900 |
| bg-secondary | secondary-50 | secondary-900 |

**Foreground / Text**
| Token | Light | Dark |
|---|---|---|
| fg-default | neutral-900 | neutral-50 |
| fg-muted | neutral-500 | neutral-400 |
| fg-subtle | neutral-400 | neutral-500 |
| fg-disabled | neutral-300 | neutral-700 |
| fg-on-primary | base-white | base-white |
| fg-on-secondary | base-white | base-white |

**Brand**
| Token | Light | Dark |
|---|---|---|
| brand-primary | primary-600 | primary-400 |
| brand-primary-hover | primary-700 | primary-300 |
| brand-primary-active | primary-800 | primary-200 |
| brand-secondary | secondary-600 | secondary-400 |
| brand-secondary-hover | secondary-700 | secondary-300 |
| brand-tertiary | tertiary-600 | tertiary-400 |  ← skip if no tertiary |

**Border**
| Token | Light | Dark |
|---|---|---|
| border-default | neutral-200 | neutral-700 |
| border-muted | neutral-100 | neutral-800 |
| border-emphasis | neutral-400 | neutral-500 |
| border-primary | primary-300 | primary-700 |

**State**
| Token | Light | Dark |
|---|---|---|
| state-success-bg | success-500 | success-500 |
| state-success-subtle | (success-50 or lightest step) | (success-900) |
| state-warning-bg | warning-500 | warning-500 |
| state-warning-subtle | warning-50 | warning-900 |
| state-error-bg | error-500 | error-500 |
| state-error-subtle | error-50 | error-900 |
| state-info-bg | info-500 | info-500 |
| state-info-subtle | info-50 | info-900 |

**Typography Semantic**
```
text-display  → font-size-5xl,  font-weight-bold,     line-height-tight
text-h1       → font-size-4xl,  font-weight-bold,     line-height-tight
text-h2       → font-size-3xl,  font-weight-semibold, line-height-snug
text-h3       → font-size-2xl,  font-weight-semibold, line-height-snug
text-h4       → font-size-xl,   font-weight-semibold, line-height-normal
text-h5       → font-size-lg,   font-weight-medium,   line-height-normal
text-body-lg  → font-size-lg,   font-weight-regular,  line-height-relaxed
text-body-md  → font-size-md,   font-weight-regular,  line-height-relaxed
text-body-sm  → font-size-sm,   font-weight-regular,  line-height-normal
text-body-xs  → font-size-xs,   font-weight-regular,  line-height-normal
text-label-lg → font-size-md,   font-weight-medium,   line-height-normal
text-label-md → font-size-sm,   font-weight-medium,   line-height-normal
text-label-sm → font-size-xs,   font-weight-medium,   line-height-normal
text-code     → font-size-sm,   font-weight-regular,  line-height-relaxed
```

**Spacing Semantic**
```
gap-none → space-0    gap-xs → space-1    gap-sm → space-2
gap-md   → space-4    gap-lg → space-6    gap-xl → space-8    gap-2xl → space-12

padding-xs → space-2    padding-sm → space-3    padding-md → space-4
padding-lg → space-6    padding-xl → space-8
```

**Radius Semantic**
```
component-radius-sm  → radius-sm
component-radius-md  → radius-md
component-radius-lg  → radius-lg
component-radius-pill→ radius-full
```

**Elevation**
```
elevation-1 → shadow-xs
elevation-2 → shadow-sm
elevation-3 → shadow-md
elevation-4 → shadow-lg
elevation-5 → shadow-xl
```

---

## Step 4 — Create Text Styles

Create Figma text styles using **the user's chosen primary font** for all headings and body, mono font for code:

| Style Name | Font | Size | Weight | Line Height |
|---|---|---|---|---|
| Display/2XL | `<primary font>` | 60 | Bold | Tight |
| Display/XL | `<primary font>` | 48 | Bold | Tight |
| Heading/H1 | `<primary font>` | 36 | Bold | Tight |
| Heading/H2 | `<primary font>` | 30 | Semibold | Snug |
| Heading/H3 | `<primary font>` | 24 | Semibold | Snug |
| Heading/H4 | `<primary font>` | 20 | Semibold | Normal |
| Heading/H5 | `<primary font>` | 18 | Medium | Normal |
| Body/LG | `<primary font>` | 18 | Regular | Relaxed |
| Body/MD | `<primary font>` | 16 | Regular | Relaxed |
| Body/SM | `<primary font>` | 14 | Regular | Normal |
| Body/XS | `<primary font>` | 12 | Regular | Normal |
| Label/LG | `<primary font>` | 16 | Medium | Normal |
| Label/MD | `<primary font>` | 14 | Medium | Normal |
| Label/SM | `<primary font>` | 12 | Medium | Normal |
| Code/MD | `<mono font>` | 14 | Regular | Relaxed |
| Code/SM | `<mono font>` | 12 | Regular | Relaxed |

All text styles bind to semantic typography variables.

---

## Step 5 — Create Effect Styles

```
Elevation/1 → shadow-xs
Elevation/2 → shadow-sm
Elevation/3 → shadow-md
Elevation/4 → shadow-lg
Elevation/5 → shadow-xl
Elevation/6 → shadow-2xl
```

---

## Step 6 — Scaffold Base Components

Create a **"Foundation"** page in Figma with these components, all fills/strokes/text bound to semantic tokens:

### Button
Variants: Size (sm, md, lg) × Style (primary, secondary, ghost, danger, link)
- Primary: bg → brand-primary, text → fg-on-primary, hover → brand-primary-hover
- Secondary: bg → bg-secondary, border → border-primary, text → brand-primary
- Ghost: bg → transparent, text → brand-primary, hover → bg-primary
- Danger: bg → state-error-bg, text → fg-on-primary
- Radius → component-radius-md

### Input / Text Field
Variants: State (default, focused, error, disabled)
- bg → bg-default, border → border-default
- focused border → border-primary
- error border → state-error-bg
- label text → fg-muted, input text → fg-default

### Badge / Tag
Variants: Style (neutral, primary, secondary, success, warning, error, info)
- Each bound to matching `state-*` or `brand-*` tokens
- Radius → component-radius-pill

### Color Palette Documentation Frame
A grid frame showing every primitive color step (50–950) for all brand colors, labeled with variable names.

### Typography Scale Frame
A frame showing every text style rendered in both primary and mono fonts.

---

## Step 7 — Final Report

Print this summary after everything is written to Figma:

```
DESIGN SYSTEM CREATED
======================
Figma file: <url>

Brand inputs used:
  Primary:    <hex>  → 11 steps generated (primary-50 … primary-950)
  Secondary:  <hex>  → 11 steps generated
  Tertiary:   <hex>  → 11 steps generated  (or: skipped)
  Neutral:    <hex>  → 11 steps generated  (or: auto-derived from primary)
  Success / Warning / Error / Info: defaults or overrides used
  Primary font:   <font name>
  Monospace font: <font name>  (or: skipped)
  Dark mode:      yes / no

Variable Collections:
  ✓ Primitives  — N variables
  ✓ Semantic    — N variables (Light mode  +  Dark mode)

Text Styles:        16 styles
Effect Styles:      6 elevation levels

Components:
  ✓ Button        (5 styles × 3 sizes)
  ✓ Input         (4 states)
  ✓ Badge         (7 styles)
  ✓ Color palette documentation frame
  ✓ Typography scale frame

Next steps:
  → Run /ds-sync-tokens <figma-url> to pull these tokens into your codebase
  → Run /ds-add-component <Name> <figma-node-id> to scaffold code for any component
```

---

## Rules
- ALWAYS load `figma-use` skill before any Figma MCP tool call — never skip this
- Collect ALL brand inputs before writing a single variable — do not guess or use defaults silently
- Semantic tokens must alias primitives — never reference raw hex in the Semantic collection
- Derived color steps must be computed mathematically — do not invent values
- If any collection already exists in the Figma file → show a diff and ask before overwriting
- Dark mode variables are only created if the user said yes
- Tertiary color section is skipped entirely if the user did not provide it
