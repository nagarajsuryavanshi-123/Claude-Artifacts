---
description: Build a complete Material Design 3 Figma design system — generates all MD3 tonal palettes, color scheme tokens, typography scale, shape scale, elevation levels, motion tokens, state layers, and all MD3 components.
argument-hint: <figma-file-url>
allowed-tools: [Read, Write, Agent]
---

# Create Material Design 3 Design System in Figma

Source reference: Material 3 Design Kit (Community) — `OotOdh144RvuTzSCCLNJir`
Variable collection: `M3` · Variable set key: `c5d408a1dcb9f7a3cd3c36a5070f681cf8ab13ae`

---

## Step 1 — Collect Brand Inputs

Before doing anything else, ask the user for all of the following in a single prompt.
Do NOT proceed until answers are received.

```
I'll build your Material Design 3 system in Figma. Please provide:

KEY COLORS (seed colors — MD3 will generate full tonal palettes from these)
  Primary   hex: ___________  (main brand color — drives CTA, FABs, nav active states)
  Secondary hex: ___________  (supporting accent — chips, sliders, toggles)
  Tertiary  hex: ___________  (optional contrasting accent, leave blank to auto-derive)
  Neutral   hex: ___________  (leave blank to auto-derive from Primary)

TYPEFACE
  Display / Headline font : ___________  (e.g. Roboto, Google Sans, leave blank → Roboto)
  Body / Label font       : ___________  (same as above or separate, leave blank → Roboto)
  Code / Mono font        : ___________  (e.g. Roboto Mono, JetBrains Mono, leave blank → Roboto Mono)

MODES
  Dark mode? (yes / no): ___

MOTION
  Reduced motion? (yes / no): ___  (yes → caps all durations at Short 4 / 200ms)

FIGMA FILE URL: ___________
```

Store all answers.

Fixed MD3 defaults — do NOT ask for these. Override only if the user explicitly requests:
- Neutral-Variant → auto-derived as a hue-shifted variant of Neutral (same hue, reduced chroma)
- Error seed → #B3261E (MD3 baseline red)

---

## Step 2 — Generate MD3 Tonal Palettes

MD3 uses the **HCT (Hue, Chroma, Tone)** color space. For each key color seed, generate a
**tonal palette** at the following tone stops:

```
0  4  5  6  10  12  17  20  22  24  30  40  50  60  70  80  87  90  92  94  95  96  98  99  100
```

**Practical algorithm (approximate HCT for tools without color-science libraries):**
1. Convert seed hex → HSL
2. Keep the hue fixed across all tones
3. Tone 0 = `#000000`, Tone 100 = `#FFFFFF`
4. For tones 10–90: set Lightness = tone / 100, adjust Chroma (Saturation) using a bell curve — peak chroma at tone 40–50, falling to ~0 at tone 0 and 100
5. **For production accuracy**: use [material-color-utilities](https://github.com/material-foundation/material-color-utilities) or [Material Theme Builder](https://m3.material.io/theme-builder) to generate exact HCT tonal palettes

**Palettes to generate:** Primary · Secondary · Tertiary · Error · Neutral · Neutral-Variant

**Baseline reference values (MD3 default purple seed — use if user provides no custom colors):**

```
Primary palette (seed #6750A4):
  primary0:#000000  primary10:#21005D  primary20:#381E72  primary30:#4F378B
  primary40:#6750A4  primary50:#7F67BE  primary60:#9A82DB  primary70:#B69DF8
  primary80:#D0BCFF  primary90:#EADDFF  primary95:#F6EDFF  primary99:#FFFBFE
  primary100:#FFFFFF

Secondary palette (seed #625B71):
  secondary0:#000000  secondary10:#1D192B  secondary20:#332D41  secondary30:#4A4458
  secondary40:#625B71  secondary50:#7A7289  secondary60:#958DA5  secondary70:#B0A7C0
  secondary80:#CCC2DC  secondary90:#E8DEF8  secondary95:#F6EDFF  secondary99:#FFFBFE
  secondary100:#FFFFFF

Tertiary palette (seed #7D5260):
  tertiary0:#000000  tertiary10:#31111D  tertiary20:#492532  tertiary30:#633B48
  tertiary40:#7D5260  tertiary50:#986977  tertiary60:#B58392  tertiary70:#D29DAC
  tertiary80:#EFB8C8  tertiary90:#FFD8E4  tertiary95:#FFECF1  tertiary99:#FFFBFA
  tertiary100:#FFFFFF

Error palette (fixed — MD3 baseline):
  error0:#000000  error5:#2D0001  error10:#410E0B  error20:#601410  error30:#8C1D18
  error40:#B3261E  error50:#DC362E  error60:#E46962  error70:#EC928E  error80:#F2B8B5
  error90:#F9DEDC  error95:#FCEEEE  error99:#FFFBF9  error100:#FFFFFF

Neutral palette (seed desaturated from Primary):
  neutral0:#000000  neutral4:#100F13  neutral6:#141218  neutral10:#1C1B1F
  neutral12:#201F23  neutral17:#2B2930  neutral20:#313033  neutral22:#36343B
  neutral24:#3B383E  neutral30:#484649  neutral40:#605D62  neutral50:#787579
  neutral60:#939094  neutral70:#AEAAAE  neutral80:#C9C5CA  neutral87:#DED8E1
  neutral90:#E6E1E5  neutral92:#EBE8EC  neutral94:#F0ECF1  neutral95:#F4EFF4
  neutral96:#F7F2FA  neutral98:#FFFBFE  neutral99:#FFFBFE  neutral100:#FFFFFF

Neutral-Variant palette (hue-shifted from Neutral):
  nv0:#000000  nv10:#1D1A22  nv20:#322F37  nv30:#49454F  nv40:#605D66
  nv50:#79747E  nv60:#938F99  nv70:#AEA9B4  nv80:#CAC4D0  nv90:#E7E0EC
  nv95:#F5EEFA  nv99:#FFFBFE  nv100:#FFFFFF
```

---

## Step 3 — Build Variable Collections in Figma

Load `figma-use` skill (MANDATORY before any Figma tool call), then write to Figma.

Check with `get_variable_defs` if collections already exist — if yes, ask before overwriting.

All variables live in a single collection named **`M3`** with groups organised by path prefix.

---

### Collection M3 — Group A: Reference Palette (`ref/palette/*`)

One variable per tone per palette. **No modes** — these are raw values.
Scope: `ALL_SCOPES` (these are source-of-truth values, not applied directly to UI).

```
ref/palette/primary0 … ref/palette/primary100       (all tone stops listed in Step 2)
ref/palette/secondary0 … ref/palette/secondary100
ref/palette/tertiary0 … ref/palette/tertiary100
ref/palette/error0 … ref/palette/error100
ref/palette/neutral0 … ref/palette/neutral100        (include extended stops: 4,6,12,17,22,24,87,92,94,96,98)
ref/palette/neutral-variant0 … ref/palette/neutral-variant100
ref/palette/black: #000000
ref/palette/white: #FFFFFF
```

---

### Collection M3 — Group B: Color Scheme Tokens (`sys/color/*`)

**2 modes: Light · Dark**
All tokens alias `ref/palette/*` — never reference raw hex.
Scope: `FRAME_FILL`, `SHAPE_FILL`, `TEXT_FILL`, `STROKE_COLOR`.

#### Primary group
| Token | Light | Dark |
|---|---|---|
| `sys/color/primary` | primary40 | primary80 |
| `sys/color/on-primary` | primary100 | primary20 |
| `sys/color/primary-container` | primary90 | primary30 |
| `sys/color/on-primary-container` | primary10 | primary90 |
| `sys/color/primary-fixed` | primary90 | primary90 |
| `sys/color/primary-fixed-dim` | primary80 | primary80 |
| `sys/color/on-primary-fixed` | primary10 | primary10 |
| `sys/color/on-primary-fixed-variant` | primary30 | primary30 |
| `sys/color/inverse-primary` | primary80 | primary40 |

#### Secondary group
| Token | Light | Dark |
|---|---|---|
| `sys/color/secondary` | secondary40 | secondary80 |
| `sys/color/on-secondary` | secondary100 | secondary20 |
| `sys/color/secondary-container` | secondary90 | secondary30 |
| `sys/color/on-secondary-container` | secondary10 | secondary90 |
| `sys/color/secondary-fixed` | secondary90 | secondary90 |
| `sys/color/secondary-fixed-dim` | secondary80 | secondary80 |
| `sys/color/on-secondary-fixed` | secondary10 | secondary10 |
| `sys/color/on-secondary-fixed-variant` | secondary30 | secondary30 |

#### Tertiary group
| Token | Light | Dark |
|---|---|---|
| `sys/color/tertiary` | tertiary40 | tertiary80 |
| `sys/color/on-tertiary` | tertiary100 | tertiary20 |
| `sys/color/tertiary-container` | tertiary90 | tertiary30 |
| `sys/color/on-tertiary-container` | tertiary10 | tertiary90 |
| `sys/color/tertiary-fixed` | tertiary90 | tertiary90 |
| `sys/color/tertiary-fixed-dim` | tertiary80 | tertiary80 |
| `sys/color/on-tertiary-fixed` | tertiary10 | tertiary10 |
| `sys/color/on-tertiary-fixed-variant` | tertiary30 | tertiary30 |

#### Error group
| Token | Light | Dark |
|---|---|---|
| `sys/color/error` | error40 | error80 |
| `sys/color/on-error` | error100 | error20 |
| `sys/color/error-container` | error90 | error30 |
| `sys/color/on-error-container` | error10 | error90 |

#### Surface group
| Token | Light | Dark |
|---|---|---|
| `sys/color/surface` | neutral99 | neutral6 |
| `sys/color/on-surface` | neutral10 | neutral90 |
| `sys/color/surface-variant` | nv90 | nv30 |
| `sys/color/on-surface-variant` | nv30 | nv80 |
| `sys/color/surface-tint` | primary40 | primary80 |
| `sys/color/surface-dim` | neutral87 | neutral6 |
| `sys/color/surface-bright` | neutral98 | neutral24 |
| `sys/color/surface-container-lowest` | neutral100 | neutral4 |
| `sys/color/surface-container-low` | neutral96 | neutral10 |
| `sys/color/surface-container` | neutral94 | neutral12 |
| `sys/color/surface-container-high` | neutral92 | neutral17 |
| `sys/color/surface-container-highest` | neutral90 | neutral22 |
| `sys/color/inverse-surface` | neutral20 | neutral90 |
| `sys/color/inverse-on-surface` | neutral95 | neutral20 |

#### Utility group
| Token | Light | Dark |
|---|---|---|
| `sys/color/background` | neutral99 | neutral10 |
| `sys/color/on-background` | neutral10 | neutral90 |
| `sys/color/outline` | nv50 | nv60 |
| `sys/color/outline-variant` | nv80 | nv30 |
| `sys/color/shadow` | black | black |
| `sys/color/scrim` | black | black |

---

### Collection M3 — Group C: State Layers (`sys/state/*`)

**No modes** — opacities are the same in Light and Dark (only the surface color changes).
Scope: `OPACITY`.

```
sys/state/hover-state-layer-opacity:    0.08
sys/state/focus-state-layer-opacity:    0.12
sys/state/pressed-state-layer-opacity:  0.12
sys/state/dragged-state-layer-opacity:  0.16
sys/state/disabled-container-opacity:   0.12
sys/state/disabled-content-opacity:     0.38

/* AI / Generative state layer opacities */
sys/state/ai-generating-pulse-min:      0.04   (lowest opacity in pulse animation cycle)
sys/state/ai-generating-pulse-max:      0.12   (highest opacity in pulse animation cycle)
sys/state/ai-streaming-cursor:          1.00   (cursor fill opacity — blink via animation)
sys/state/ai-failed-wash:               0.08   (error state layer over container)
sys/state/ai-tool-call-overlay:         0.06   (surface wash for function-call containers)
```

**How to apply state layers in Figma:**
- Add a fill layer on top of the component surface using `sys/color/on-surface` (or the relevant "on-" token)
- Set fill opacity to the matching state layer token value
- This creates the correct MD3 tonal overlay without hardcoding colours

---

### Collection M3 — Group D: Typography Scale (`sys/typescale/*`)

**No modes.** Font-size in `sp` (treat as `px` for Figma). Scope: `FONT_SIZE`, `FONT_WEIGHT`, `LETTER_SPACING`, `LINE_HEIGHT`.

```
Display Large:   font-size 57 · weight 400 · line-height 64 · tracking -0.25px
Display Medium:  font-size 45 · weight 400 · line-height 52 · tracking  0px
Display Small:   font-size 36 · weight 400 · line-height 44 · tracking  0px

Headline Large:  font-size 32 · weight 400 · line-height 40 · tracking  0px
Headline Medium: font-size 28 · weight 400 · line-height 36 · tracking  0px
Headline Small:  font-size 24 · weight 400 · line-height 32 · tracking  0px

Title Large:     font-size 22 · weight 400 · line-height 28 · tracking  0px
Title Medium:    font-size 16 · weight 500 · line-height 24 · tracking +0.15px
Title Small:     font-size 14 · weight 500 · line-height 20 · tracking +0.1px

Body Large:      font-size 16 · weight 400 · line-height 24 · tracking +0.5px
Body Medium:     font-size 14 · weight 400 · line-height 20 · tracking +0.25px
Body Small:      font-size 12 · weight 400 · line-height 16 · tracking +0.4px

Label Large:     font-size 14 · weight 500 · line-height 20 · tracking +0.1px
Label Medium:    font-size 12 · weight 500 · line-height 16 · tracking +0.5px
Label Small:     font-size 11 · weight 500 · line-height 16 · tracking +0.5px
```

Variable naming pattern:
```
sys/typescale/display-large/font-family
sys/typescale/display-large/font-weight
sys/typescale/display-large/font-size
sys/typescale/display-large/line-height
sys/typescale/display-large/tracking
```
(Repeat for all 15 type roles)

---

### Collection M3 — Group E: Shape Scale (`sys/shape/*`)

**No modes.** MD3 uses corner-radius as the primary shape expression.
Scope: `CORNER_RADIUS`.

```
sys/shape/corner/none:        0dp    → Chip image, Icon
sys/shape/corner/extra-small: 4dp    → Chip, Menu, Text field, Tooltip
sys/shape/corner/small:       8dp    → Button, Card (elevated variant)
sys/shape/corner/medium:      12dp   → Card (standard), Dialog
sys/shape/corner/large:       16dp   → Bottom sheet, Navigation drawer, Side sheet
sys/shape/corner/extra-large: 28dp   → FAB, Large dialog, Search bar
sys/shape/corner/full:        9999   → Badge, FAB extended, Snackbar, Switch
```

Top-only corner radius variant (for standard bottom sheet handle):
```
sys/shape/corner/extra-large-top: 28dp (top-left + top-right only, bottom = 0)
```

---

### Collection M3 — Group F: Elevation (`sys/elevation/*`)

**No modes.** MD3 elevation uses **tonal colour overlay** (Surface Tint at increasing opacity) layered on top of the surface — NOT drop-shadow alone. Effect styles handle the shadow; the tonal overlay is a separate fill.

Scope: `EFFECT` (for shadow binding — not natively supported in Figma Variables; document as annotation).

| Token | dp | Tonal overlay % | Usage |
|---|---|---|---|
| `sys/elevation/level0` | 0dp  | 0%  | Surface, Card at rest |
| `sys/elevation/level1` | 1dp  | 5%  | Filled/Tonal button hover, Nav rail |
| `sys/elevation/level2` | 3dp  | 8%  | Card hover, FAB at rest |
| `sys/elevation/level3` | 6dp  | 11% | FAB hover, Modal sheet |
| `sys/elevation/level4` | 8dp  | 12% | Navigation drawer |
| `sys/elevation/level5` | 12dp | 14% | Dialog, Bottom sheet (modal) |

---

### Collection M3 — Group G: Motion (`sys/motion/*`)

**No modes.** Figma doesn't support easing curves as variables — document as annotations on the Foundation page and in the Developer Handoff frame.
Scope: none for easing (annotation only); `FONT_SIZE` proxy can carry numeric duration values.

**Duration tokens:**
```
sys/motion/duration/short1:       50ms
sys/motion/duration/short2:      100ms
sys/motion/duration/short3:      150ms
sys/motion/duration/short4:      200ms
sys/motion/duration/medium1:     250ms
sys/motion/duration/medium2:     300ms
sys/motion/duration/medium3:     350ms
sys/motion/duration/medium4:     400ms
sys/motion/duration/long1:       450ms
sys/motion/duration/long2:       500ms
sys/motion/duration/long3:       550ms
sys/motion/duration/long4:       600ms
sys/motion/duration/extra-long1: 700ms
sys/motion/duration/extra-long2: 800ms
sys/motion/duration/extra-long3: 900ms
sys/motion/duration/extra-long4: 1000ms
```

**Easing curves (annotate in Foundation page — not Figma variables):**
```
Emphasized:            cubic-bezier(0.2,  0,    0,    1.0)   → hero / spatial expand
Emphasized Decelerate: cubic-bezier(0.05, 0.7,  0.1,  1.0)  → element entering screen
Emphasized Accelerate: cubic-bezier(0.3,  0,    0.8,  0.15) → element leaving screen
Standard:              cubic-bezier(0.2,  0,    0,    1.0)   → simple utility transitions
Standard Decelerate:   cubic-bezier(0,    0,    0,    1)     → enter
Standard Accelerate:   cubic-bezier(0.3,  0,    1,    1)     → exit
```

**Reduced motion rule:** When user selected reduced-motion = yes, cap all durations at `short4 (200ms)` and replace Emphasized/Emphasized-Decelerate/Emphasized-Accelerate with Standard. Annotate this in the Foundation page.

---

### Collection M3 — Group H: Spacing (`sys/spacing/*`)

MD3 uses a **4dp base grid**. All spacing values are multiples of 4.
**No modes.** Scope: `GAP`, `HORIZONTAL_PADDING`, `VERTICAL_PADDING`, `WIDTH_HEIGHT`.

```
Spacing scale:
sys/spacing/0:   0dp     sys/spacing/1:   4dp     sys/spacing/2:   8dp
sys/spacing/3:  12dp     sys/spacing/4:  16dp     sys/spacing/5:  20dp
sys/spacing/6:  24dp     sys/spacing/7:  28dp     sys/spacing/8:  32dp
sys/spacing/10: 40dp     sys/spacing/12: 48dp     sys/spacing/16: 64dp
sys/spacing/20: 80dp     sys/spacing/24: 96dp     sys/spacing/32: 128dp

Semantic component aliases (alias the scale above):
sys/spacing/component/padding-none:  sys/spacing/0    (0dp)
sys/spacing/component/padding-xs:    sys/spacing/1    (4dp)
sys/spacing/component/padding-sm:    sys/spacing/2    (8dp)
sys/spacing/component/padding-md:    sys/spacing/4    (16dp)
sys/spacing/component/padding-lg:    sys/spacing/6    (24dp)
sys/spacing/component/padding-xl:    sys/spacing/8    (32dp)

Semantic layout aliases:
sys/spacing/layout/gap-xs:           sys/spacing/1    (4dp)
sys/spacing/layout/gap-sm:           sys/spacing/2    (8dp)
sys/spacing/layout/gap-md:           sys/spacing/4    (16dp)
sys/spacing/layout/gap-lg:           sys/spacing/6    (24dp)
sys/spacing/layout/gap-xl:           sys/spacing/8    (32dp)
sys/spacing/layout/gap-2xl:          sys/spacing/12   (48dp)
sys/spacing/layout/section-gap:      sys/spacing/16   (64dp)
sys/spacing/layout/page-gap:         sys/spacing/24   (96dp)
```

---

### Collection M3 — Group I: Grid & Layout (`sys/layout/*`)

MD3 uses **3 primary breakpoints** (Compact / Medium / Expanded) with adaptive grid.
**No modes.** Scope: Number reference annotation — apply via Figma Layout Grid per frame.

```
Breakpoints (dp — minimum width at which breakpoint activates):
sys/layout/breakpoint/compact:    0     (phones portrait — 0 to 599dp)
sys/layout/breakpoint/medium:     600   (tablets, phones landscape — 600 to 839dp)
sys/layout/breakpoint/expanded:   840   (desktops, tablets landscape — 840 to 1199dp)
sys/layout/breakpoint/large:      1200  (large desktops — 1200 to 1599dp)
sys/layout/breakpoint/xlarge:     1600  (ultra-wide — ≥ 1600dp)

Grid columns per breakpoint:
sys/layout/grid/columns-compact:    4
sys/layout/grid/columns-medium:     8
sys/layout/grid/columns-expanded:  12
sys/layout/grid/columns-large:     12
sys/layout/grid/columns-xlarge:    12

Grid outer margins (left + right page padding):
sys/layout/grid/margin-compact:   16dp
sys/layout/grid/margin-medium:    24dp
sys/layout/grid/margin-expanded:  24dp
sys/layout/grid/margin-large:     24dp

Grid gutters (gap between columns):
sys/layout/grid/gutter-compact:   16dp
sys/layout/grid/gutter-medium:    24dp
sys/layout/grid/gutter-expanded:  24dp

Max content widths (constrain reading area):
sys/layout/max-width/body:        720dp   (prose / article content)
sys/layout/max-width/expanded:    1040dp  (standard expanded layout)
sys/layout/max-width/large:       1280dp
```

Apply in Figma — create 3 artboard frames per screen:
- Compact:  360dp wide · 4-col grid · 16dp margin · 16dp gutter
- Medium:   768dp wide · 8-col grid · 24dp margin · 24dp gutter
- Expanded: 1280dp wide · 12-col grid · 24dp margin · 24dp gutter

---

### Collection M3 — Group J: Opacity Scale (`sys/opacity/*`)

Discrete opacity values for state layers, disabled states, and overlay surfaces.
**No modes.** Scope: `OPACITY`.

```
sys/opacity/0:    0      sys/opacity/4:   0.04   sys/opacity/8:   0.08
sys/opacity/12:   0.12   sys/opacity/16:  0.16   sys/opacity/20:  0.20
sys/opacity/38:   0.38   sys/opacity/50:  0.50   sys/opacity/60:  0.60
sys/opacity/80:   0.80   sys/opacity/90:  0.90   sys/opacity/100: 1.0
```

---

### Collection M3 — Group K: Gradient Tokens (`sys/gradient/*`)

MD3 uses gradients sparingly — scrim overlays and tonal surface treatments only.
**2 modes: Light · Dark.** Scope: `FRAME_FILL`, `SHAPE_FILL`.

```
sys/gradient/scrim-top:       linear(180deg, sys/color/scrim @60%, transparent)
sys/gradient/scrim-bottom:    linear(0deg,   sys/color/scrim @60%, transparent)
sys/gradient/surface-fade:    linear(180deg, sys/color/surface 0%, transparent 100%)
sys/gradient/image-overlay:   linear(180deg, transparent 0%, sys/color/scrim @50% 100%)
sys/gradient/brand-primary:   linear(135deg, primary40 0%, primary60 100%)
sys/gradient/tonal-surface:   linear(180deg, sys/color/surface-container-low,
                                             sys/color/surface-container-highest)
```

---

### Collection M3 — Group L: Component Tokens (`comp/*`)

Component tokens are **Tier 3** — they alias `sys/*` tokens ONLY (never `ref/palette/*` directly).
This is the multi-brand surface: swap `comp/*` per brand without touching `sys/` or `ref/`.
**No modes at this level** — modes are already resolved at the `sys/*` tier.
Scope: same as the `sys/*` token aliased.

```
comp/button/filled/container:            → sys/color/primary
comp/button/filled/label:                → sys/color/on-primary
comp/button/filled/container-disabled:   → sys/color/on-surface   (+ 0.12 opacity)
comp/button/filled/label-disabled:       → sys/color/on-surface   (+ 0.38 opacity)
comp/button/filled/shape:                → sys/shape/corner/full
comp/button/filled/label-text:           → sys/typescale/label-large
comp/button/filled/elevation:            → sys/elevation/level0
comp/button/tonal/container:             → sys/color/secondary-container
comp/button/tonal/label:                 → sys/color/on-secondary-container
comp/button/tonal/shape:                 → sys/shape/corner/full
comp/button/outlined/outline:            → sys/color/outline
comp/button/outlined/label:              → sys/color/primary
comp/button/outlined/shape:              → sys/shape/corner/full

comp/text-field/filled/container:        → sys/color/surface-container-highest
comp/text-field/filled/input-text:       → sys/color/on-surface
comp/text-field/filled/label:            → sys/color/on-surface-variant
comp/text-field/filled/label-focused:    → sys/color/primary
comp/text-field/filled/active-indicator: → sys/color/on-surface-variant
comp/text-field/filled/active-focused:   → sys/color/primary
comp/text-field/filled/error-indicator:  → sys/color/error
comp/text-field/filled/supporting:       → sys/color/on-surface-variant
comp/text-field/filled/placeholder:      → sys/color/on-surface-variant
comp/text-field/filled/shape:            → sys/shape/corner/extra-small  (top only)

comp/card/elevated/container:            → sys/color/surface-container-low
comp/card/elevated/headline:             → sys/color/on-surface
comp/card/elevated/subhead:              → sys/color/on-surface-variant
comp/card/elevated/shape:                → sys/shape/corner/medium
comp/card/elevated/elevation:            → sys/elevation/level1
comp/card/filled/container:              → sys/color/surface-container-highest
comp/card/filled/shape:                  → sys/shape/corner/medium
comp/card/outlined/container:            → sys/color/surface
comp/card/outlined/outline:              → sys/color/outline-variant
comp/card/outlined/shape:                → sys/shape/corner/medium

comp/navigation-bar/container:           → sys/color/surface-container
comp/navigation-bar/indicator:           → sys/color/secondary-container
comp/navigation-bar/icon-active:         → sys/color/on-secondary-container
comp/navigation-bar/icon-inactive:       → sys/color/on-surface-variant
comp/navigation-bar/label-active:        → sys/color/on-surface
comp/navigation-bar/label-inactive:      → sys/color/on-surface-variant
comp/navigation-bar/label-text:          → sys/typescale/label-medium

comp/dialog/container:                   → sys/color/surface-container-high
comp/dialog/headline:                    → sys/color/on-surface
comp/dialog/body:                        → sys/color/on-surface-variant
comp/dialog/shape:                       → sys/shape/corner/extra-large
comp/dialog/elevation:                   → sys/elevation/level3
comp/dialog/headline-text:               → sys/typescale/headline-small
comp/dialog/body-text:                   → sys/typescale/body-medium

comp/chip/filter/container:              → transparent
comp/chip/filter/outline:                → sys/color/outline
comp/chip/filter/label:                  → sys/color/on-surface-variant
comp/chip/filter/selected-container:     → sys/color/secondary-container
comp/chip/filter/selected-label:         → sys/color/on-secondary-container
comp/chip/filter/shape:                  → sys/shape/corner/small
comp/chip/filter/label-text:             → sys/typescale/label-large

comp/snackbar/container:                 → sys/color/inverse-surface
comp/snackbar/text:                      → sys/color/inverse-on-surface
comp/snackbar/action:                    → sys/color/inverse-primary
comp/snackbar/shape:                     → sys/shape/corner/extra-small
comp/snackbar/elevation:                 → sys/elevation/level3

comp/list-item/container:                → sys/color/surface
comp/list-item/headline:                 → sys/color/on-surface
comp/list-item/supporting:               → sys/color/on-surface-variant
comp/list-item/leading-icon:             → sys/color/on-surface-variant
comp/list-item/trailing-icon:            → sys/color/on-surface-variant

comp/top-app-bar/container:              → sys/color/surface
comp/top-app-bar/container-scrolled:     → sys/color/surface-container
comp/top-app-bar/headline:               → sys/color/on-surface
comp/top-app-bar/leading-icon:           → sys/color/on-surface
comp/top-app-bar/trailing-icon:          → sys/color/on-surface-variant
```

---

## Step 4 — Create Text Styles

Create Figma text styles named exactly following MD3 convention:

| Style Name | Font | Size | Weight | Line Height | Tracking |
|---|---|---|---|---|---|
| `Display/Large` | `<display font>` | 57 | Regular (400) | 64 | -0.25 |
| `Display/Medium` | `<display font>` | 45 | Regular (400) | 52 | 0 |
| `Display/Small` | `<display font>` | 36 | Regular (400) | 44 | 0 |
| `Headline/Large` | `<display font>` | 32 | Regular (400) | 40 | 0 |
| `Headline/Medium` | `<display font>` | 28 | Regular (400) | 36 | 0 |
| `Headline/Small` | `<display font>` | 24 | Regular (400) | 32 | 0 |
| `Title/Large` | `<body font>` | 22 | Regular (400) | 28 | 0 |
| `Title/Medium` | `<body font>` | 16 | Medium (500) | 24 | +0.15 |
| `Title/Small` | `<body font>` | 14 | Medium (500) | 20 | +0.1 |
| `Body/Large` | `<body font>` | 16 | Regular (400) | 24 | +0.5 |
| `Body/Medium` | `<body font>` | 14 | Regular (400) | 20 | +0.25 |
| `Body/Small` | `<body font>` | 12 | Regular (400) | 16 | +0.4 |
| `Label/Large` | `<body font>` | 14 | Medium (500) | 20 | +0.1 |
| `Label/Medium` | `<body font>` | 12 | Medium (500) | 16 | +0.5 |
| `Label/Small` | `<body font>` | 11 | Medium (500) | 16 | +0.5 |

All text styles bind to `sys/typescale/*` variables.
Tracking values use `{unit: "PIXELS", value: N}` format in Figma API.

---

## Step 5 — Create Effect Styles (Elevation)

Create Figma effect styles for each elevation level. Each level has a Light and Dark variant because shadow opacity is heavier in Light mode.

| Style Name | Shadow 1 | Shadow 2 |
|---|---|---|
| `Elevation/Light/Level 0` | none | — |
| `Elevation/Light/Level 1` | 0px 1px 2px rgba(0,0,0,0.3) | 0px 1px 3px 1px rgba(0,0,0,0.15) |
| `Elevation/Light/Level 2` | 0px 1px 2px rgba(0,0,0,0.3) | 0px 2px 6px 2px rgba(0,0,0,0.15) |
| `Elevation/Light/Level 3` | 0px 1px 3px rgba(0,0,0,0.3) | 0px 4px 8px 3px rgba(0,0,0,0.15) |
| `Elevation/Light/Level 4` | 0px 2px 3px rgba(0,0,0,0.3) | 0px 6px 10px 4px rgba(0,0,0,0.15) |
| `Elevation/Light/Level 5` | 0px 4px 4px rgba(0,0,0,0.3) | 0px 8px 12px 6px rgba(0,0,0,0.15) |
| `Elevation/Dark/Level 1` | 0px 1px 3px rgba(0,0,0,0.4) | 0px 1px 2px rgba(0,0,0,0.3) |
| `Elevation/Dark/Level 2` | 0px 2px 6px rgba(0,0,0,0.4) | 0px 1px 2px rgba(0,0,0,0.3) |
| `Elevation/Dark/Level 3` | 0px 4px 8px rgba(0,0,0,0.4) | 0px 1px 3px rgba(0,0,0,0.3) |
| `Elevation/Dark/Level 4` | 0px 6px 10px rgba(0,0,0,0.4) | 0px 2px 3px rgba(0,0,0,0.3) |
| `Elevation/Dark/Level 5` | 0px 8px 12px rgba(0,0,0,0.4) | 0px 4px 4px rgba(0,0,0,0.3) |

**Important — Tonal Overlay:** MD3 elevation is a two-part system. For elevated surfaces in Light mode, also apply a fill using `sys/color/surface-tint` at the corresponding tonal overlay opacity (Level 1: 5%, Level 2: 8%, Level 3: 11%, Level 4: 12%, Level 5: 14%). In Dark mode, this overlay is the primary elevation cue — shadows alone are insufficient.

---

## Step 5b — Foundation Documentation & Developer Handoff

Create a **"Foundation"** page in Figma with these annotation frames.

---

### Token Naming Convention

```
MD3 path structure:   [group]/[sub-group]/[role]
Examples:
  ref/palette/primary40              → raw tonal palette value #6750A4
  sys/color/primary                  → aliases ref/palette/primary40 (Light mode)
  sys/typescale/body-large/font-size → 16sp
  sys/shape/corner/medium            → 12dp
  sys/motion/duration/medium2        → 300ms

Rules:
  ✓  All tokens use kebab-case
  ✓  Group prefix indicates semantic layer: ref/ (primitive) · sys/ (semantic) · comp/ (component)
  ✓  Color tokens in sys/color/* are ALWAYS mode-aware (Light/Dark)
  ✓  Tokens in ref/palette/* are NEVER mode-aware
  ✗  Never reference ref/palette/* directly in components — always go through sys/color/*
  ✗  Never hardcode hex, dp, sp values in components
```

---

### CSS Custom Properties Export (MD3 token → CSS)

```css
/* Reference Palette — generated, do not edit */
:root {
  --md-ref-palette-primary40:  #6750A4;
  --md-ref-palette-primary80:  #D0BCFF;
  --md-ref-palette-neutral10:  #1C1B1F;
  --md-ref-palette-neutral99:  #FFFBFE;
}

/* System Color Scheme — Light (default) */
:root {
  --md-sys-color-primary:                  var(--md-ref-palette-primary40);
  --md-sys-color-on-primary:               var(--md-ref-palette-primary100);
  --md-sys-color-primary-container:        var(--md-ref-palette-primary90);
  --md-sys-color-on-primary-container:     var(--md-ref-palette-primary10);
  --md-sys-color-surface:                  var(--md-ref-palette-neutral99);
  --md-sys-color-on-surface:               var(--md-ref-palette-neutral10);
  --md-sys-color-surface-container:        var(--md-ref-palette-neutral94);
  --md-sys-color-outline:                  var(--md-ref-palette-neutral-variant50);
}

/* System Color Scheme — Dark */
.dark, [data-theme="dark"] {
  --md-sys-color-primary:                  var(--md-ref-palette-primary80);
  --md-sys-color-on-primary:               var(--md-ref-palette-primary20);
  --md-sys-color-primary-container:        var(--md-ref-palette-primary30);
  --md-sys-color-surface:                  var(--md-ref-palette-neutral6);
  --md-sys-color-on-surface:               var(--md-ref-palette-neutral90);
}

/* System Shape */
:root {
  --md-sys-shape-corner-none:        0px;
  --md-sys-shape-corner-extra-small: 4px;
  --md-sys-shape-corner-small:       8px;
  --md-sys-shape-corner-medium:      12px;
  --md-sys-shape-corner-large:       16px;
  --md-sys-shape-corner-extra-large: 28px;
  --md-sys-shape-corner-full:        9999px;
}

/* System Motion */
:root {
  --md-sys-motion-duration-short2:  100ms;
  --md-sys-motion-duration-medium2: 300ms;
  --md-sys-motion-duration-long2:   500ms;
  --md-sys-motion-easing-emphasized: cubic-bezier(0.2, 0, 0, 1);
  --md-sys-motion-easing-standard:   cubic-bezier(0.2, 0, 0, 1);
}

/* System State Layers */
:root {
  --md-sys-state-hover-opacity:    0.08;
  --md-sys-state-focus-opacity:    0.12;
  --md-sys-state-pressed-opacity:  0.12;
  --md-sys-state-dragged-opacity:  0.16;
  --md-sys-state-disabled-container-opacity: 0.12;
  --md-sys-state-disabled-content-opacity:   0.38;
}
```

---

### JSON Token Export Structure (Style Dictionary / W3C DTCG)

```json
{
  "ref": {
    "palette": {
      "primary": {
        "40": { "$value": "#6750A4", "$type": "color" },
        "80": { "$value": "#D0BCFF", "$type": "color" }
      },
      "neutral": {
        "10": { "$value": "#1C1B1F", "$type": "color" },
        "99": { "$value": "#FFFBFE", "$type": "color" }
      }
    }
  },
  "sys": {
    "color": {
      "primary":   { "$value": "{ref.palette.primary.40}", "$type": "color" },
      "on-primary":{ "$value": "{ref.palette.primary.100}", "$type": "color" }
    },
    "typescale": {
      "body-large": {
        "font-size":   { "$value": "16sp", "$type": "dimension" },
        "font-weight": { "$value": 400, "$type": "number" },
        "line-height": { "$value": "24sp", "$type": "dimension" },
        "tracking":    { "$value": "0.5px", "$type": "dimension" }
      }
    },
    "shape": {
      "corner": {
        "medium": { "$value": "12px", "$type": "dimension" }
      }
    },
    "motion": {
      "duration": {
        "medium2": { "$value": "300ms", "$type": "duration" }
      },
      "easing": {
        "emphasized": { "$value": "cubic-bezier(0.2, 0, 0, 1)", "$type": "cubicBezier" }
      }
    }
  }
}
```

---

### WCAG 2.1 AA Accessibility Requirements

```
MD3 color roles are designed to meet WCAG AA by default (with the baseline palette).
If a custom seed is used, always verify these pairs after generation:

  sys/color/on-primary        on  sys/color/primary              → ≥ 4.5:1
  sys/color/on-secondary      on  sys/color/secondary            → ≥ 4.5:1
  sys/color/on-surface        on  sys/color/surface              → ≥ 4.5:1
  sys/color/on-surface-variant on sys/color/surface-variant      → ≥ 4.5:1
  sys/color/on-error          on  sys/color/error                → ≥ 4.5:1
  sys/color/outline            on  sys/color/surface             → ≥ 3:1 (UI component)

State layers:
  All interactive elements must have visible :focus-visible ring using sys/color/primary
  Disabled state uses disabled-content-opacity (0.38) — never just grey color alone
  Do not communicate state (error, success) through color alone — pair with icon + label
```

---

### MD3 Governance & Multi-Brand Rules

```
VARIABLE HIERARCHY  (arrows must flow one way only)
  ref/palette/*  ──→  sys/color/*  ──→  comp/*
                 Never skip a layer. Never back-reference.

MULTI-BRAND SUPPORT
  Each brand gets its own Figma Variable Mode in sys/color/* (e.g. "Brand A Light", "Brand A Dark")
  Only sys/color/* values change per brand — ref/palette/* is shared
  Brand-specific palettes use their own key-color seeds

CONTRIBUTION RULES
  1. New palette stops only if a scheme token explicitly requires a tone not yet in ref/palette/*
  2. New sys/color/* tokens only for genuinely new color roles, not convenience aliases
  3. Any token addition requires a matching CSS variable update and JSON export update
  4. Always re-verify WCAG contrast pairs after any palette change

DO / DON'T
  ✓ DO use tonal overlay fills (surface-tint) for elevation in Dark mode
  ✓ DO apply state layers as separate fill layers (not baked into component fills)
  ✓ DO use shape corner tokens — never hardcode px radius in components
  ✗ DON'T hardcode any hex, sp, dp, or ms value in a component
  ✗ DON'T create new semantic tokens without a corresponding comp/* token to consume them
  ✗ DON'T use opacity to dim primary — use on-surface with state layer opacity
```

---

## Step 6 — Scaffold All Components

Create a **"Components"** page in Figma.

Inside this page, create a top-level labelled section (frame) named **"md3"** — every component below is a Material Design 3 component and must live inside this group. This establishes the design system library structure: future component libraries (shadcn, custom, Radix, etc.) will each get their own sibling section alongside "md3".

All fills, strokes, corner radius, and text must be bound to `sys/*` tokens — zero hardcoded values.
All state interactions use a separate state-layer fill with `sys/state/*` opacity tokens.
All shadows use the `Elevation/Light/*` or `Elevation/Dark/*` effect styles.

**Atomic Design Layer Rules:**
- **Atoms** — single-token elements (Icon, Avatar, Divider, Badge, Progress dot, Skeleton line): no internal composition, bind tokens directly
- **Molecules** — 2–4 atoms composed into a reusable unit (Button, Chip, Text Field, List Item, Navigation Item, Tooltip): expose Figma component properties for variants + states
- **Organisms** — multi-molecule context-aware components (App Bars, Navigation Drawer, Navigation Bar, Card, Dialog, Data Table, Form): use `INSTANCE_SWAP` for embedded molecule slots
- **Templates** — screen-level scaffolding (Dashboard, Detail View, Form Screen, Error/Empty/Loading screen): combine organisms into complete layout frames per breakpoint

Each Figma component must set:
- `componentPropertyDefinitions` for variant (style), size, state, and boolean slots (icon visible, etc.)
- Auto-layout with spacing variables from `sys/spacing/*`
- Min/Max width constraints where responsive
- All fills/strokes from `comp/*` tokens (not raw `sys/*`) for multi-brand override support

---

### md3 / Category 1 — Actions (4 component families)

#### Common Button
Styles: Filled · Tonal · Outlined · Elevated · Text
Sizes: default (40dp height)
States: enabled, hovered (+hover state layer 8%), focused (+focus ring, state layer 12%), pressed (state layer 12%), disabled (container 12% opacity, content 38% opacity)
- Filled: `sys/color/primary` bg · `sys/color/on-primary` label
- Tonal: `sys/color/secondary-container` bg · `sys/color/on-secondary-container` label
- Outlined: transparent bg · `sys/color/primary` label · `sys/color/outline` border
- Elevated: `sys/color/surface-container-low` bg + Level 1 elevation · `sys/color/primary` label
- Text: transparent bg · `sys/color/primary` label
- Shape: `sys/shape/corner/full` (fully rounded pill)
- Typography: `sys/typescale/label-large`
- Icon slot: optional, 18dp, `sys/color/on-*` fill matching variant

#### Floating Action Button (FAB)
Variants: FAB (56dp) · Small FAB (40dp) · Large FAB (96dp) · Extended FAB (text + icon)
States: enabled (Level 3 elevation), hovered (Level 4 elevation + state layer 8%), pressed (Level 3 + state layer 12%)
- Default: `sys/color/primary-container` bg · `sys/color/on-primary-container` icon
- Surface variant: `sys/color/surface-container-high` bg · `sys/color/primary` icon
- Secondary: `sys/color/secondary-container` bg · `sys/color/on-secondary-container` icon
- Tertiary: `sys/color/tertiary-container` bg · `sys/color/on-tertiary-container` icon
- Shape: `sys/shape/corner/large` (FAB/Small), `sys/shape/corner/extra-large` (Large), `sys/shape/corner/full` (Extended)
- Extended label: `sys/typescale/label-large`

#### Icon Button
Styles: Standard · Filled · Filled Tonal · Outlined
Size: 40dp × 40dp · Icon: 24dp
States: enabled, hovered, focused, pressed, disabled, selected/toggled (toggle variant)
- Standard: transparent bg · `sys/color/on-surface-variant` icon (unselected), `sys/color/primary` (selected)
- Filled: `sys/color/primary` bg · `sys/color/on-primary` icon (selected); neutral surface bg (unselected)
- Filled Tonal: `sys/color/secondary-container` bg · `sys/color/on-secondary-container` icon
- Outlined: `sys/color/outline` border · `sys/color/on-surface-variant` icon
- Shape: `sys/shape/corner/full`

#### Segmented Button
Segment count: 2–5 options · Single or multi-select
Height: 40dp
States: unselected, selected (check icon appears), disabled
- Container: transparent bg · `sys/color/outline` border
- Selected segment: `sys/color/secondary-container` bg · `sys/color/on-secondary-container` label+icon
- Unselected: `sys/color/on-surface` label
- Shape: `sys/shape/corner/full` on outer ends; inner joins are square
- Typography: `sys/typescale/label-large`

---

### md3 / Category 2 — Communication (4 component families)

#### Badge
Variants: Small (8dp dot) · Large (with count, min 16dp)
- Background: `sys/color/error` · Text: `sys/color/on-error`
- Shape: `sys/shape/corner/full`
- Typography: `sys/typescale/label-small`
- Position: anchored top-right of parent (icon button, nav destination)

#### Progress Indicator
Types: Linear · Circular
Sub-types: Determinate (shows progress value) · Indeterminate (continuous loop)
- Track: `sys/color/secondary-container`
- Indicator: `sys/color/primary`
- Linear height: 4dp · Circular stroke: 4dp
- Shape: `sys/shape/corner/full` (linear track caps)

#### Snackbar
Variants: single line · with action button · stacked (action below text) · with icon
Duration: Short (4s) · Medium (6s) · Long (8s) · Indefinite
- Container: `sys/color/inverse-surface` · Text: `sys/color/inverse-on-surface`
- Action: `sys/color/inverse-primary`
- Shape: `sys/shape/corner/extra-small`
- Elevation: Level 3
- Typography: body=`sys/typescale/body-medium` · action=`sys/typescale/label-large`

#### Tooltip
Variants: Plain (short text) · Rich (title + body + optional actions)
- Plain: `sys/color/inverse-surface` bg · `sys/color/inverse-on-surface` text · `sys/shape/corner/extra-small`
- Rich: `sys/color/surface-container` bg · `sys/color/on-surface` text · `sys/shape/corner/medium` · Level 2 elevation
- Plain typography: `sys/typescale/body-small`
- Rich title: `sys/typescale/title-small` · Rich body: `sys/typescale/body-medium`

---

### md3 / Category 3 — Containment (6 component families)

#### Card
Variants: Elevated · Filled · Outlined
States: enabled, hovered, focused, pressed, dragged (Level 4 + drag state layer 16%)
- Elevated: `sys/color/surface-container-low` bg · Level 1 elevation
- Filled: `sys/color/surface-container-highest` bg · Level 0 elevation
- Outlined: `sys/color/surface` bg · `sys/color/outline-variant` border · Level 0 elevation
- Shape: `sys/shape/corner/medium`
- Content: title `sys/typescale/title-medium` · subhead `sys/typescale/body-medium` · body `sys/typescale/body-small`

#### Dialog
Variants: Basic · Full-screen
Height: max 560dp · Width: 280–560dp
Sub-components: `DialogScrim`, `DialogContainer`, `DialogHeader`, `DialogHeadline`, `DialogSupportingText`, `DialogDivider`, `DialogActions`
- Container: `sys/color/surface-container-high` bg · Level 3 elevation · `sys/shape/corner/extra-large`
- Headline: `sys/typescale/headline-small` · `sys/color/on-surface`
- Supporting text: `sys/typescale/body-medium` · `sys/color/on-surface-variant`
- Actions: Text buttons aligned end (Basic); close icon + title (Full-screen)

#### Divider
Variants: Full-width · Inset · Middle-inset
- Color: `sys/color/outline-variant`
- Thickness: 1dp
- Inset offset: 16dp (standard), 72dp (with leading icon/avatar)

#### List
Item types: 1-line · 2-line · 3-line
Leading elements: None · Icon · Avatar · Thumbnail · Checkbox · Radio · Switch
Trailing elements: None · Icon · Badge · Checkbox · Radio · Switch · Meta text
States: enabled, hovered, focused, pressed, selected, disabled
- Container: `sys/color/surface` · Hover: `sys/color/on-surface` state layer 8%
- Headline: `sys/typescale/body-large` · Supporting text: `sys/typescale/body-medium` · `sys/color/on-surface-variant`
- Leading icon: `sys/color/on-surface-variant` · Overline: `sys/typescale/label-small`

#### Bottom Sheet
Variants: Modal · Standard (persistent)
Drag handle: 32dp × 4dp
- Container: `sys/color/surface-container-low` bg · Level 1 elevation (standard), Level 5 (modal)
- Shape: `sys/shape/corner/extra-large-top` (28dp top corners only)
- Handle: `sys/color/on-surface-variant` @ 40% opacity
- Scrim (modal only): `sys/color/scrim` @ 32% opacity

#### Side Sheet
Variants: Standard (docked) · Modal (overlay)
Width: standard 256dp–400dp (on screen edge)
- Container: `sys/color/surface-container-low` · Level 1 elevation (standard), Level 5 (modal)
- Shape: `sys/shape/corner/large` (leading edge, modal only)
- Header: `sys/typescale/title-large` · Scrim (modal): `sys/color/scrim` @ 32%

---

### md3 / Category 4 — Navigation (6 component families)

#### Top App Bar
Variants: Small (56dp) · Center-aligned (56dp) · Medium (112dp) · Large (152dp)
Scroll behaviour: flat at top, elevated on scroll (Level 2), compact on scroll (Medium/Large)
- Container: `sys/color/surface` → `sys/color/surface-container` on scroll
- Leading icon: `sys/color/on-surface` · Trailing icons: `sys/color/on-surface-variant`
- Headline: Small/Center = `sys/typescale/title-large` · Medium = `sys/typescale/headline-small` · Large = `sys/typescale/headline-medium`

#### Bottom App Bar
Height: 80dp · Contains: FAB (optional) + 3–4 icon buttons
- Container: `sys/color/surface-container` · Level 2 elevation
- Icons: `sys/color/on-surface-variant`

#### Navigation Bar
Destinations: 3–5
Height: 80dp
States: inactive, active, focused, pressed, disabled
- Container: `sys/color/surface-container` bg · Level 2 elevation
- Active indicator: `sys/color/secondary-container` pill (64dp × 32dp)
- Active icon: `sys/color/on-secondary-container` · Inactive icon: `sys/color/on-surface-variant`
- Label: `sys/typescale/label-medium` · Active label: `sys/color/on-surface` · Inactive: `sys/color/on-surface-variant`
- Badge: aligned top-right of icon

#### Navigation Drawer
Variants: Modal (overlay) · Standard (side-by-side)
Width: 360dp
Sub-components: `DrawerScrim`, `DrawerContainer`, `DrawerHeader`, `NavigationDrawerItem`
- Container: `sys/color/surface-container-low` bg · Level 0 elevation (standard), Level 5 (modal)
- Shape: `sys/shape/corner/large` (trailing edge)
- Active item indicator: `sys/color/secondary-container` (336dp × 56dp, `sys/shape/corner/full`)
- Active item label+icon: `sys/color/on-secondary-container`
- Inactive label: `sys/typescale/label-large` · `sys/color/on-surface-variant`

#### Navigation Rail
Width: 80dp · Destinations: 3–7
States: inactive, active (indicator pill), focused, pressed
- Container: `sys/color/surface-container` bg
- Active indicator: `sys/color/secondary-container` (56dp × 32dp, `sys/shape/corner/full`)
- Active icon: `sys/color/on-secondary-container` · Inactive: `sys/color/on-surface-variant`
- Label: `sys/typescale/label-medium` below icon · optional FAB at top

#### Tabs
Variants: Primary (filled indicator) · Secondary (underline indicator)
Types: Fixed (equal width) · Scrollable
Height: 48dp
States: active, inactive, hovered, focused, pressed, disabled
- Primary container: `sys/color/surface`
- Primary active: `sys/color/primary` icon+label · indicator fill `sys/color/primary` (height 3dp, full-width, `sys/shape/corner/full` top)
- Secondary active: `sys/color/on-surface` icon+label · `sys/color/primary` underline (2dp)
- Inactive: `sys/color/on-surface-variant` icon+label
- Typography: `sys/typescale/title-small`

---

### md3 / Category 5 — Selection (8 component families)

#### Checkbox
States: unchecked · checked · indeterminate · error · disabled
Size: 18dp × 18dp
- Unchecked border: `sys/color/on-surface-variant` · Checked/indeterminate fill: `sys/color/primary`
- Check icon: `sys/color/on-primary` · Error: `sys/color/error`
- State layer: 40dp touch target · `sys/color/primary` or `sys/color/on-surface`

#### Chip
Types: Assist · Filter · Input · Suggestion
Height: 32dp
States: enabled, hovered, focused, pressed, selected (Filter/Suggestion), disabled
- Enabled: transparent bg · `sys/color/outline` border · `sys/color/on-surface-variant` label+icon
- Selected (Filter): `sys/color/secondary-container` bg · no border · `sys/color/on-secondary-container` label
- Shape: `sys/shape/corner/small` (8dp)
- Typography: `sys/typescale/label-large`
- Leading icon (Assist/Input): 18dp · Trailing close (Input): 18dp · Leading check (Filter selected): 18dp

#### Date Picker
Variants: Modal · Docked · Input (text entry)
- Container: `sys/color/surface-container-high` bg · Level 3 elevation · `sys/shape/corner/extra-large`
- Selected date: `sys/color/primary` fill (40dp circle) · `sys/color/on-primary` text
- Today: `sys/color/primary` outline circle
- Range: `sys/color/primary-container` fill between start/end
- Header: `sys/typescale/headline-large` (selected date display)

#### Menu
Variants: Regular (dropdown) · Exposed Dropdown (select-like)
- Container: `sys/color/surface-container` bg · Level 2 elevation · `sys/shape/corner/extra-small`
- Item: `sys/typescale/body-large` · hover = `sys/color/on-surface` state layer 8%
- Divider: `sys/color/outline-variant`
- Leading icon: `sys/color/on-surface-variant` · Trailing icon/shortcut: same

#### Radio Button
States: unselected · selected · error · disabled
Size: 20dp × 20dp (inner circle 10dp when selected)
- Unselected border: `sys/color/on-surface-variant`
- Selected border+fill: `sys/color/primary`
- State layer: 40dp touch target

#### Slider
Types: Continuous · Discrete (tick marks) · Range (two thumbs)
Orientations: Horizontal (default)
- Active track: `sys/color/primary` · Inactive track: `sys/color/secondary-container`
- Thumb: `sys/color/primary` fill (20dp) · `sys/color/shadow` 0dp elevation at rest, Level 1 active
- Tick marks: `sys/color/on-primary` (on active), `sys/color/on-secondary-container` (on inactive)
- Value indicator label: `sys/color/primary` bg · `sys/color/on-primary` text · `sys/shape/corner/full`

#### Switch
Sizes: default (52dp × 32dp track)
States: unselected-off · unselected-on · selected-off · selected-on · disabled
- Off track: `sys/color/surface-container-highest` border + fill · `sys/color/outline` border
- On track: `sys/color/primary` fill
- Thumb unselected: `sys/color/outline` small 16dp → 24dp on press
- Thumb selected: `sys/color/on-primary` 24dp
- With icon: optional check/x icon inside thumb · `sys/color/primary` (selected), `sys/color/surface-container-highest` (unselected)

#### Time Picker
Variants: Dial (clock face) · Input (text entry)
- Container: `sys/color/surface-container-high` bg · Level 3 elevation · `sys/shape/corner/extra-large`
- Clock face: `sys/color/surface-container-highest` bg
- Selected hour/minute: `sys/color/primary` fill · `sys/color/on-primary` text
- Clock hand: `sys/color/primary`
- AM/PM toggle: `sys/color/outline` border when unselected; `sys/color/tertiary-container` when selected

---

### md3 / Category 6 — Text Input (1 component family)

#### Text Field
Variants: Filled · Outlined
States: enabled, hovered, focused, error, disabled
Sub-components: `TextFieldContainer`, `TextFieldLabel`, `TextFieldInput`, `TextFieldSupportingText`, `TextFieldLeadingIcon`, `TextFieldTrailingIcon`
- **Filled:** `sys/color/surface-container-highest` bg · `sys/shape/corner/extra-small` (top corners only)
  - Active indicator line: `sys/color/on-surface-variant` (1dp) → `sys/color/primary` (2dp, focused) → `sys/color/error` (2dp, error)
- **Outlined:** transparent bg · `sys/color/outline` border (1dp) → `sys/color/primary` (2dp, focused) → `sys/color/error` (2dp, error)
  - Shape: `sys/shape/corner/extra-small` all corners
- Label (floating): `sys/typescale/body-small` (focused/populated) · `sys/typescale/body-large` (resting) · `sys/color/on-surface-variant` → `sys/color/primary` (focused)
- Input text: `sys/typescale/body-large` · `sys/color/on-surface`
- Supporting text: `sys/typescale/body-small` · `sys/color/on-surface-variant` → `sys/color/error` (error state)
- Leading icon: 24dp · `sys/color/on-surface-variant`
- Trailing icon: 24dp · `sys/color/on-surface-variant` → `sys/color/error`

---

### md3 / Category 7 — Layout & Surface (2 component families)

#### Carousel
Variants: Full-width · Multi-browse · Hero · Center-aligned
- Items use Card tokens as base
- Navigation arrows: `sys/color/surface-container` bg circle · `sys/color/on-surface` icon
- Shape per item: `sys/shape/corner/medium`

#### Search
Variants: Search Bar (persistent, replaces Top App Bar) · Search View (modal overlay)
Height: 56dp (bar) · 72dp (view header)
- Bar container: `sys/color/surface-container-high` · `sys/shape/corner/full`
- Bar icons (leading/trailing): `sys/color/on-surface`
- View container: `sys/color/surface` · Level 3 elevation · `sys/shape/corner/extra-large` (top edge only)
- Input: `sys/typescale/body-large`
- Suggestion list: uses List tokens inside the view

---

### md3 / Category 8 — Extended & Organism Components (5 component families)

These are organism-level or dashboard-critical components not in the core MD3 spec but required for production applications.

#### Data Table
Variants: Default · Sortable · Selectable (row checkbox) · Sticky Header · Paginated
- Container: `sys/color/surface` · `sys/shape/corner/extra-small` · Level 1 elevation
- Header row bg: `sys/color/surface-container-low` · `sys/typescale/label-large` · `sys/color/on-surface`
- Header sort icon: `sys/color/on-surface-variant` (inactive) → `sys/color/on-surface` (active) · trailing chevron-up/down
- Body row bg: `sys/color/surface` (default) · `sys/color/surface-container-lowest` (alternate stripe, optional)
- Body cell text: `sys/typescale/body-medium` · `sys/color/on-surface`
- Row hover overlay: `sys/state/hover-state-layer-opacity` (0.08) on `sys/color/on-surface`
- Row selected bg: `sys/color/secondary-container` · `sys/color/on-secondary-container` text
- Row checkbox: uses Checkbox atom component tokens
- Divider between rows: `sys/color/outline-variant` · 1dp stroke
- Pagination row: `sys/color/surface-container-low` · `sys/typescale/label-medium` · Icon Buttons (First/Prev/Next/Last)
- Column widths: flexible via auto-layout; min-width 48dp per column
- Loading state: Skeleton shimmer rows (see Skeleton component)
- Empty state: centered Empty State organism inside table body area
- Sorting indicator: `comp/data-table/header/sort-icon → sys/color/on-surface-variant`
- Token group: `comp/data-table/*`

#### Avatar
Variants: Image · Initials · Icon · Stacked (group of 3+ with overflow badge)
Sizes: XS 24dp · S 32dp · M 40dp (default) · L 56dp · XL 80dp
- Container shape: `sys/shape/corner/full` (always circular)
- Fallback bg (initials/icon): `sys/color/primary-container`
- Fallback text: `sys/typescale/title-medium` · `sys/color/on-primary-container`
- Fallback icon: 24dp icon · `sys/color/on-primary-container`
- Image: full-bleed within circular clip mask
- Stacked variant: each avatar offset by -8dp, rightmost shows "+N" overflow badge
  - Overflow badge bg: `sys/color/surface-container-high` · `sys/color/on-surface` text · `sys/typescale/label-small`
  - Stack border: 2dp `sys/color/surface` outline ring around each avatar (separation)
- Token group: `comp/avatar/*`

#### Skeleton / Loader
Variants: Line · Paragraph (3 lines) · Card · List Item · Avatar · Custom (freeform slot)
States: Shimmer (animated) · Static (reduced motion fallback)
- Base fill: `sys/color/surface-container-highest`
- Shimmer gradient: linear sweep from `sys/color/surface-container-high` → `sys/color/surface-container-highest` → `sys/color/surface-container-high`, 1.5s loop, 30° angle
- Reduced motion: static flat fill at `sys/color/surface-container-highest`, no animation
- Border radius: matches target component shape — e.g. Line: `sys/shape/corner/extra-small` · Avatar: `sys/shape/corner/full`
- Line height: 16dp (body line) · 24dp (headline line) · 12dp (label line)
- Paragraph: 3 lines — L1 full-width, L2 80% width, L3 60% width (asymmetric to signal text content)
- Card skeleton: full card area (matches Card Medium size 200×140dp)
- List Item skeleton: leading avatar skeleton + 2-line paragraph skeleton + trailing rect skeleton
- Circular Progress (indeterminate): 40dp diameter · `sys/color/primary` track · stroke 4dp
- Linear Progress (indeterminate): full container width · height 4dp · `sys/color/primary` indicator · `sys/color/surface-container-high` track
- Token group: `comp/skeleton/*`

#### Empty State
Variants: Default · With Action · Error · Search No Results · Offline
Layout: centered vertical stack — Illustration (optional) → Headline → Supporting text → CTA Button (optional)
- Container: transparent or `sys/color/surface` · minimum height 200dp
- Illustration slot: 120dp × 120dp max (INSTANCE_SWAP to custom illustration, default to generic MD3 icon in `sys/color/surface-variant`)
- Headline: `sys/typescale/headline-small` · `sys/color/on-surface` · centered
- Supporting text: `sys/typescale/body-medium` · `sys/color/on-surface-variant` · centered · max-width 300dp
- CTA Button: Filled variant · `comp/button/filled/*` tokens · optional (boolean property `showAction`)
- Error variant: Illustration replaced by error icon (`sys/color/error`) · Headline in `sys/color/on-surface` (not error color) · CTA = "Retry"
- Search No Results: magnifying glass icon · "No results for '<query>'" headline · "Try different keywords" body
- Offline variant: cloud-off icon · `sys/color/on-surface-variant` · "You're offline" headline · "Check your connection" body
- Token group: `comp/empty-state/*`

#### Alert / Banner
Variants: Inline Alert (contextual) · Banner (top-of-page persistent) · Toast (= Snackbar, already covered in Category 2)
Severity levels: Info · Success · Warning · Error

**Inline Alert:**
- Container: `sys/color/<severity>-container` · `sys/shape/corner/small` · no elevation · full container width
  - Info: `sys/color/secondary-container` · `sys/color/on-secondary-container`
  - Success: `sys/color/tertiary-container` · `sys/color/on-tertiary-container`
  - Warning: `sys/color/error-container` at 60% opacity · `sys/color/on-error-container`
  - Error: `sys/color/error-container` · `sys/color/on-error-container`
- Leading icon: 20dp · severity tone color (on-*-container)
- Title (optional): `sys/typescale/label-large` · bold
- Body: `sys/typescale/body-medium`
- Trailing close icon button: optional (boolean property `dismissible`)
- Action link: `sys/typescale/label-large` · severity on-container color · underline on hover

**Banner:**
- Container: full screen-width · `sys/color/surface-container-low` · Level 1 elevation · no corner radius
- Leading icon: 24dp · `sys/color/on-surface-variant`
- Message: `sys/typescale/body-medium` · `sys/color/on-surface` · single line (truncates)
- Actions: 1–2 Text Buttons (right-aligned) · `sys/color/primary`
- Dismiss: swipe-up gesture or close button
- Placement: directly below Top App Bar · pushes content down (not an overlay)
- Token group: `comp/alert/*`

---

### Foundation Documentation Frames (always created)

These frames live on the **"Foundation"** page:

**MD3 Tonal Palette Grid** — all 6 palettes (Primary, Secondary, Tertiary, Error, Neutral, Neutral-Variant), all tone stops, labeled with variable names and hex values.

**MD3 Color Scheme Map** — side-by-side Light and Dark scheme swatches with token names, showing the role → palette → hex relationship.

**Typography Scale Specimen** — all 15 type styles rendered live in the user's chosen fonts.

**Shape Scale Illustration** — 7 corner radius stops with labeled dp values and real component examples.

**Elevation Guide** — 6 elevation levels showing shadow + tonal overlay combined effect.

**Motion Reference** — duration ramp visualised as a timeline; easing curves as Bézier graphs.

**State Layer Reference** — matrix of surfaces × interaction states showing opacity values.

**Spacing Scale Grid** — all 15 spacing steps visualised as boxes with dp labels. Two-column layout: semantic (component padding, layout gap) vs. raw scale. Annotated with 4dp base-grid overlay.

**Layout Grid Diagram** — 3-column frame set showing Compact / Medium / Expanded artboard with live layout grid applied, margin and gutter labeled. Includes breakpoint threshold table.

**Atomic Design Map** — hierarchy tree showing how the system layers:
```
Atoms (single tokens / primitive elements)
  → Color chips · Type specimens · Shape chips · Spacing blocks · Elevation cards
  → Icon · Avatar · Divider · Badge · Progress indicator
Molecules (2–4 atoms composed)
  → Button · Chip · Text Field · List Item · Navigation Item · Card · Tooltip · Snackbar
Organisms (multi-molecule, context-aware)
  → Top App Bar · Navigation Drawer · Navigation Bar · Navigation Rail
  → Form (field group + submit) · Card Grid · Data Table · Dialog · Bottom Sheet
Screens / Templates
  → Dashboard (App Bar + Nav Rail + Card Grid + Data Table)
  → Detail View (App Bar + List + FAB)
  → Form Screen (App Bar + Form + Snackbar feedback)
  → Empty / Loading / Error state variants of each template
```

**UI States Reference Matrix** — annotated frame showing every state every component must implement:
```
Standard Interaction States:
  Enabled    → rest state, no overlay
  Hovered    → +state layer 8%  (on-surface or relevant on-* token)
  Focused    → +state layer 12% + focus ring (sys/color/primary, 3dp outline)
  Pressed    → +state layer 12%
  Dragged    → +state layer 16% + Level 4 elevation (draggable items)
  Disabled   → container 12% opacity + content 38% opacity — no state layer

Data / Async States:
  Loading    → Skeleton shimmer animation (sys/color/surface-container-highest bg, animated gradient sweep)
  Empty      → Illustrated empty state (icon + headline + supporting text + optional CTA button)
  Error      → Error message (sys/color/error-container bg, sys/color/on-error-container text, retry CTA)
  Success    → Confirmation feedback (Snackbar or inline success icon + text, auto-dismiss 4s)
  Partial    → Skeleton + loaded content mixed (progressive loading pattern)

AI / Generative States (for AI-driven product UIs):
  Generating   → Animated pulse on container (surface-tint fill, 0.04–0.12 opacity cycle, 1.5s loop)
               → Shimmer sweep on text placeholder areas
               → Spinner (Circular Progress Indicator, indeterminate)
  Streaming    → Text renders character by character · blinking cursor (sys/color/primary, 1px × 16px)
               → Container expands in height as content fills
  Regenerating → Previous content fades (0.38 opacity) · Spinner replaces content
               → "Regenerating..." Label Medium text · sys/color/on-surface-variant
  Tool Call    → Pill-shaped container (sys/color/surface-container-high bg, sys/color/outline border)
               → Leading icon (function/tool icon) · label (function name) · trailing spinner
               → sys/shape/corner/full · sys/typescale/label-small
  Failed       → sys/color/error-container bg fill · sys/color/on-error-container headline
               → Trailing retry Button (Text variant) · sys/color/error icon
  Complete     → Brief Snackbar ("Generated" or contextual success message, 4s) · returns to Enabled state
```

---

## Step 7 — Final Report

Print this summary after everything is written to Figma:

```
MATERIAL DESIGN 3 SYSTEM CREATED
==================================
Figma file: <url>

Key color seeds used:
  Primary:    <hex>  → tonal palette generated (25 tones)
  Secondary:  <hex>  → tonal palette generated
  Tertiary:   <hex>  → tonal palette generated  (or: auto-derived)
  Neutral:    <hex>  → extended neutral palette (30 tones)
  Error:      #B3261E → fixed MD3 baseline

M3 Variable Collection:
  ✓ ref/palette/*    — N variables (raw tonal palette values, no modes)
  ✓ sys/color/*      — 49 variables × 2 modes (Light + Dark)
  ✓ sys/state/*      — 11 opacity variables (6 standard + 5 AI states)
  ✓ sys/typescale/*  — 75 variables (15 styles × 5 attributes)
  ✓ sys/shape/*      — 8 variables (7 corner radii + full token)
  ✓ sys/elevation/*  — 10 variables (5 levels × dp + tonal overlay %)
  ✓ sys/motion/*     — 22 variables (16 duration + 6 easing curves)
  ✓ sys/spacing/*    — 20 variables (15 scale steps + 5 semantic aliases)
  ✓ sys/layout/*     — 15 variables (breakpoints, columns, margins, gutters, max-widths)
  ✓ sys/opacity/*    — 12 opacity variables
  ✓ sys/gradient/*   — 6 gradient tokens
  ✓ comp/*           — N component-tier token aliases (button, text-field, nav-bar, etc.)

Text Styles created:  15  (Display/L/M/S · Headline/L/M/S · Title/L/M/S · Body/L/M/S · Label/L/M/S)
Effect Styles created: 11 (Elevation Light/0–5 · Elevation Dark/1–5)

Foundation page frames:
  ✓ MD3 Tonal Palette Grid      — 6 palettes × 25 tones, hex + variable name labels
  ✓ MD3 Color Scheme Map        — Light + Dark side-by-side, 49 role swatches
  ✓ Typography Scale Specimen   — 15 type styles live in chosen typeface
  ✓ Shape Scale Illustration    — 7 stops with dp labels + component examples
  ✓ Elevation Guide             — 5 levels, shadow + tonal overlay combined
  ✓ Motion Reference            — duration ramp + Bézier easing curves
  ✓ State Layer Reference       — surfaces × states opacity matrix
  ✓ Spacing Scale Grid          — 15 steps, semantic vs raw, 4dp grid overlay
  ✓ Layout Grid Diagram         — Compact / Medium / Expanded artboards
  ✓ Atomic Design Map           — Atoms → Molecules → Organisms → Templates tree
  ✓ UI States Matrix            — Standard + Data/Async + AI/Generative states
  ✓ CSS export snippet          — --md-ref-palette-* + --md-sys-* + --md-sys-spacing-* + comp/*
  ✓ JSON token export           — W3C DTCG format, all 3 tiers

Components page:
  ✓ [md3] Actions        — 4 families   (Button, FAB, Icon Button, Segmented Button)
  ✓ [md3] Communication  — 4 families   (Badge, Progress, Snackbar, Tooltip)
  ✓ [md3] Containment    — 6 families   (Card, Dialog, Divider, List, Bottom Sheet, Side Sheet)
  ✓ [md3] Navigation     — 6 families   (Top App Bar, Bottom App Bar, Nav Bar, Nav Drawer, Nav Rail, Tabs)
  ✓ [md3] Selection      — 8 families   (Checkbox, Chip, Date Picker, Menu, Radio, Slider, Switch, Time Picker)
  ✓ [md3] Text Input     — 1 family     (Text Field: Filled + Outlined)
  ✓ [md3] Layout         — 2 families   (Carousel, Search)
  ✓ [md3] Extended       — 5 families   (Data Table, Avatar, Skeleton/Loader, Empty State, Alert/Banner)

Typeface:   <display font name> / <body font name>
Dark mode:  yes / no
Total MD3 components: 36 families

Variable set key: c5d408a1dcb9f7a3cd3c36a5070f681cf8ab13ae
Collection name:  M3
```
