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
  Tertiary  hex: ___________  (optional third accent, leave blank to skip)

FONTS
  Primary font   : ___________  (headings + body, e.g. Inter)
  Monospace font : ___________  (code blocks, e.g. JetBrains Mono, leave blank to skip)

MODES
  Dark mode? (yes / no): ___

FIGMA FILE URL: ___________
```

Store all answers.

The following are fixed defaults — do NOT ask for them. Only change if the user explicitly requests it later:
- Neutral   → auto-derived by desaturating Primary 90%
- Success   → #22c55e
- Warning   → #eab308
- Error     → #ef4444
- Info      → #3b82f6

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

Border radius primitives:
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

Use the **background / foreground pair convention**: every surface token has a matching `-foreground` token that controls text and icons on that surface.

#### Core Surface & Text Pairs

| Token | Light | Dark | Used In |
|---|---|---|---|
| `background` | base-white | neutral-950 | Page shell, body |
| `foreground` | neutral-900 | neutral-50 | Default body text |
| `card` | base-white | neutral-900 | Card, panels |
| `card-foreground` | neutral-900 | neutral-50 | Card text |
| `popover` | base-white | neutral-900 | Dropdowns, tooltips, command |
| `popover-foreground` | neutral-900 | neutral-50 | Floating panel text |
| `primary` | primary-600 | primary-400 | CTA buttons, badges |
| `primary-foreground` | base-white | neutral-950 | Text on primary |
| `secondary` | neutral-100 | neutral-800 | Secondary buttons |
| `secondary-foreground` | neutral-900 | neutral-50 | Text on secondary |
| `muted` | neutral-100 | neutral-800 | Subtle surfaces, skeletons |
| `muted-foreground` | neutral-500 | neutral-400 | Placeholders, helper text, lead text |
| `accent` | neutral-100 | neutral-800 | Hover states, ghost buttons, toggle active |
| `accent-foreground` | neutral-900 | neutral-50 | Text on accent |
| `destructive` | error-500 | error-600 | Delete buttons, error alerts |
| `destructive-foreground` | base-white | base-white | Text on destructive |

#### Brand Aliases

| Token | Light | Dark |
|---|---|---|
| `brand-primary` | primary-600 | primary-400 |
| `brand-primary-hover` | primary-700 | primary-300 |
| `brand-primary-active` | primary-800 | primary-200 |
| `brand-secondary` | secondary-600 | secondary-400 |
| `brand-secondary-hover` | secondary-700 | secondary-300 |
| `brand-tertiary` | tertiary-600 | tertiary-400 | ← skip if no tertiary |

#### Border & Form

| Token | Light | Dark | Used In |
|---|---|---|---|
| `border` | neutral-200 | neutral-700 (10% white alpha) | Cards, dividers, tables, separators |
| `input` | neutral-200 | neutral-700 (15% white alpha) | Input, textarea, select, combobox borders |
| `ring` | neutral-400 | neutral-500 | Focus rings on buttons, inputs, checkboxes |

#### State / Feedback

| Token | Light | Dark |
|---|---|---|
| `state-success` | success-500 | success-500 |
| `state-success-subtle` | success-50 | success-900 |
| `state-warning` | warning-500 | warning-500 |
| `state-warning-subtle` | warning-50 | warning-900 |
| `state-error` | error-500 | error-500 |
| `state-error-subtle` | error-50 | error-900 |
| `state-info` | info-500 | info-500 |
| `state-info-subtle` | info-50 | info-900 |

#### Chart Palette (5 tokens for data visualisation)

| Token | Light | Dark |
|---|---|---|
| `chart-1` | primary-500 | primary-400 |
| `chart-2` | secondary-500 | secondary-400 |
| `chart-3` | success-500 | success-400 |
| `chart-4` | warning-500 | warning-400 |
| `chart-5` | info-500 | info-400 |

#### Sidebar Tokens (extended set for sidebar component)

| Token | Light | Dark | Purpose |
|---|---|---|---|
| `sidebar` | neutral-50 | neutral-900 | Sidebar background |
| `sidebar-foreground` | neutral-700 | neutral-200 | Sidebar default text |
| `sidebar-primary` | primary-600 | primary-400 | Active nav items, CTAs |
| `sidebar-primary-foreground` | base-white | neutral-950 | Text on sidebar-primary |
| `sidebar-accent` | neutral-100 | neutral-800 | Hover + selected states |
| `sidebar-accent-foreground` | neutral-900 | neutral-50 | Text on sidebar-accent |
| `sidebar-border` | neutral-200 | neutral-700 | Sidebar separators |
| `sidebar-ring` | primary-400 | primary-500 | Focus rings inside sidebar |

#### Radius Scale (proportional from base radius)

```
--radius:     8px                          (base — user can override)
--radius-sm:  calc(var(--radius) * 0.6)   → ~5px
--radius-md:  calc(var(--radius) * 0.8)   → ~6px
--radius-lg:  var(--radius)               → 8px
--radius-xl:  calc(var(--radius) * 1.4)   → ~11px
--radius-2xl: calc(var(--radius) * 1.8)   → ~14px
--radius-3xl: calc(var(--radius) * 2.2)   → ~18px
--radius-4xl: calc(var(--radius) * 2.6)   → ~21px
```

#### Typography Semantic
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

#### Spacing Semantic
```
gap-none → space-0    gap-xs → space-1    gap-sm → space-2
gap-md   → space-4    gap-lg → space-6    gap-xl → space-8    gap-2xl → space-12

padding-xs → space-2    padding-sm → space-3    padding-md → space-4
padding-lg → space-6    padding-xl → space-8
```

#### Elevation
```
elevation-1 → shadow-xs
elevation-2 → shadow-sm
elevation-3 → shadow-md
elevation-4 → shadow-lg
elevation-5 → shadow-xl
elevation-6 → shadow-2xl
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

## Step 6 — Scaffold All Components

Create a **"Foundation"** page in Figma with all 59 components organised by category.
All fills, strokes, radius, and text must be bound to semantic tokens — zero raw hex values.

---

### Category 1 — Form & Input (13 components)

#### Checkbox
Variants: State (unchecked, checked, indeterminate, disabled, invalid)
- Indicator: `primary` fill when checked
- Border: `input` token · Focus ring: `ring` token
- Pair with `Field` for label + description + error composition

#### Combobox
Variants: simple, multiple-selection (with chips), grouped, popup, input-group-addon
States: default, open, auto-highlight, invalid (`aria-invalid`), disabled
Sub-components: `ComboboxInput`, `ComboboxContent`, `ComboboxEmpty`, `ComboboxList`, `ComboboxItem`, `ComboboxChips`, `ComboboxChip`, `ComboboxGroup`, `ComboboxLabel`, `ComboboxSeparator`
- Input border: `input` · Dropdown surface: `popover` bg · Item hover: `accent`
- Chips: `secondary` bg · `secondary-foreground` text · Clear button: `muted-foreground`

#### Field
Orientations: vertical (default), horizontal, responsive
Sub-components: `FieldSet`, `FieldGroup`, `FieldLabel`, `FieldContent`, `FieldDescription`, `FieldError`, `FieldLegend`, `FieldSeparator`, `FieldTitle`
States: default, `data-invalid` (entire field block switches to error), disabled
- Label: `foreground` text · Description: `muted-foreground` · Error: `state-error` color

#### Input
States: default, focused, disabled, invalid, file
Types: text, number, search, file, required
- Border: `input` token · Focus ring: `ring` · Placeholder: `muted-foreground`
- Pair with `Field` + `FieldLabel` + `FieldDescription` for full form layout
- Pair with `InputGroup` to add icons, text, or buttons inline

#### Input Group
Addon alignments: inline-start (default), inline-end, block-start, block-end
Sub-components: `InputGroupInput`, `InputGroupTextarea`, `InputGroupAddon`, `InputGroupButton`, `InputGroupText`
Button sizes inside group: xs, icon-xs, sm, icon-sm · Variants: ghost (default), outline, secondary
- Addon background: `muted` · Addon text: `muted-foreground` · Input border: `input`

#### Input OTP
Lengths: 4-digit, 6-digit, custom (`maxLength`)
Patterns: digits-only (`REGEXP_ONLY_DIGITS`), alphanumeric (`REGEXP_ONLY_DIGITS_AND_CHARS`)
Variants: with separator, without separator
States: default, disabled, invalid (`aria-invalid`)
Sub-components: `InputOTPGroup`, `InputOTPSlot`, `InputOTPSeparator`
- Slot border: `input` · Active slot: `ring` focus · Letter spacing: 0.25em

#### Label
Variants: standalone (via `htmlFor`), field-integrated (`FieldLabel`)
- Color: `foreground` · Font: `text-label-md`

#### Native Select
Variants: flat options, grouped options (`NativeSelectOptGroup`)
States: default, disabled, invalid (`aria-invalid`)
Sub-components: `NativeSelect`, `NativeSelectOption`, `NativeSelectOptGroup`
- Border: `input` · Background: `background` · Focus ring: `ring`
- Prefer over `Select` when native browser behavior + mobile optimization is needed

#### Radio Group
Spacing variants: default, comfortable, compact
Compositions: description cards, choice cards (`FieldLabel` wrapping), fieldset with legend
States: unselected, selected, disabled, invalid (`aria-invalid`)
Sub-components: `RadioGroup`, `RadioGroupItem`
- Selected indicator: `primary` fill · Border: `input` · Focus: `ring`

#### Select
Positions: `item-aligned` (default), `popper`
States: default, open, selected, disabled, invalid
Sub-components: `SelectTrigger`, `SelectValue`, `SelectContent`, `SelectGroup`, `SelectLabel`, `SelectItem`, `SelectSeparator`
- Trigger border: `input` · Content surface: `popover` bg · Item hover: `accent`
- Group label: `muted-foreground` · Selected item: `primary`

#### Slider
Types: single value, range (two thumbs), multiple thumbs
Orientations: horizontal (default), vertical
States: default, disabled
- Track: `secondary` bg · Range fill: `primary` · Thumb: `background` with `border` stroke · Focus: `ring`

#### Switch
Sizes: default, small (`size="sm"`)
Compositions: standalone, with label (`Field` + `FieldLabel`), choice card
States: off, on, disabled, invalid
- Off track: `input` border color · On track: `primary` fill · Thumb: `background`

#### Textarea
States: default, focused, disabled, invalid
Compositions: with label + description, with submit button
- Border: `input` · Focus ring: `ring` · Placeholder: `muted-foreground`
- Pair with `Field` for labeled composition with `FieldLabel` + `FieldDescription`

---

### Category 2 — Actions / Buttons (4 components)

#### Button
Styles: default, outline, secondary, ghost, destructive, link
Sizes: xs, sm, default, lg, icon, icon-xs, icon-sm, icon-lg
States: default, hover, disabled, loading (with Spinner inside)
Icon positions: `data-icon="inline-start"` · `data-icon="inline-end"`
Token bindings:
- Default: `primary` bg · `primary-foreground` text
- Outline: `border` stroke · `foreground` text · `accent` hover bg
- Secondary: `secondary` bg · `secondary-foreground` text
- Ghost: transparent bg · `accent` hover · `foreground` text
- Destructive: `destructive` bg · `destructive-foreground` text
- Link: no bg/border · `primary` text color

#### Button Group
Pattern: multiple Button components grouped in a row or column
Variants: connected (shared border radius removed between buttons), spaced (with gap)
Sizes: inherits Button sizes
Token bindings: same as Button — no new tokens introduced

#### Toggle
Styles: default, outline
Sizes: sm, default, lg
States: off (inactive), on (active/pressed), disabled
- Inactive: transparent bg · `foreground` text
- Active: `accent` bg · `accent-foreground` text
- Outline variant adds `border` stroke in inactive state

#### Toggle Group
Types: single selection, multiple selection
Styles: default, outline
Orientations: horizontal (default), vertical
Spacing: with gap (`spacing` prop)
States: item active, item disabled, group disabled
Sub-components: `ToggleGroup`, `ToggleGroupItem`
- Same token bindings as Toggle · Items share border radius for connected look

---

### Category 3 — Overlay & Floating UI (9 components)

#### Alert Dialog
Sizes: default, sm
Compositions: basic, with media (`AlertDialogMedia`), destructive action, small with media
Sub-components: `AlertDialogTrigger`, `AlertDialogContent`, `AlertDialogHeader`, `AlertDialogTitle`, `AlertDialogDescription`, `AlertDialogMedia`, `AlertDialogFooter`, `AlertDialogCancel`, `AlertDialogAction`
- Overlay: `background` at 80% opacity · Content surface: `background` · `border` stroke
- Action button: `primary` · Cancel: `secondary` · Destructive action: `destructive`

#### Context Menu
Variants: basic, with submenus, with shortcuts (`ContextMenuShortcut`), grouped, with icons, checkbox items, radio items, destructive
Trigger: right-click (desktop) · long-press (mobile)
Sub-components: `ContextMenuTrigger`, `ContextMenuContent`, `ContextMenuGroup`, `ContextMenuLabel`, `ContextMenuItem`, `ContextMenuSeparator`, `ContextMenuCheckboxItem`, `ContextMenuRadioGroup`, `ContextMenuRadioItem`, `ContextMenuSub`, `ContextMenuShortcut`
- Surface: `popover` bg · `popover-foreground` text · Item hover: `accent`
- Separator: `border` · Shortcut text: `muted-foreground` · Destructive: `destructive`

#### Dialog
Variants: standard, custom close button, no close button (`showCloseButton={false}`), sticky footer, scrollable content
Sub-components: `DialogTrigger`, `DialogContent`, `DialogHeader`, `DialogTitle`, `DialogDescription`, `DialogFooter`
- Overlay: `background` at 80% opacity · Content: `background` bg · `border` stroke
- Title: `foreground` · Description: `muted-foreground`

#### Drawer
Directions: top, right, bottom (default/mobile), left
Compositions: standard, scrollable content, responsive (Dialog on desktop / Drawer on mobile)
Sub-components: `DrawerTrigger`, `DrawerContent`, `DrawerHeader`, `DrawerTitle`, `DrawerDescription`, `DrawerFooter`
- Surface: `background` · Drag handle: `muted` · Title: `foreground` · Description: `muted-foreground`

#### Dropdown Menu
Variants: basic, with submenus, checkbox items, radio groups, with icons, destructive actions, avatar-triggered
Sub-components: `DropdownMenuTrigger`, `DropdownMenuContent`, `DropdownMenuGroup`, `DropdownMenuLabel`, `DropdownMenuItem`, `DropdownMenuSeparator`, `DropdownMenuCheckboxItem`, `DropdownMenuRadioGroup`, `DropdownMenuRadioItem`, `DropdownMenuSub`, `DropdownMenuShortcut`
- Surface: `popover` bg · Item hover: `accent` · Label: `muted-foreground`
- Separator: `border` · Destructive: `destructive` · Shortcut: `muted-foreground`

#### Hover Card
Directions: top, bottom, left, right (via `side` prop)
Alignment: start, center, end (via `align` prop)
Timing: `openDelay`, `closeDelay` props
Sub-components: `HoverCard`, `HoverCardTrigger`, `HoverCardContent`
- Surface: `popover` bg · `popover-foreground` text · `border` stroke + shadow

#### Popover
Alignments: start, center, end
Compositions: basic, with form fields inside
Sub-components: `Popover`, `PopoverTrigger`, `PopoverContent`, `PopoverHeader`, `PopoverTitle`, `PopoverDescription`
- Surface: `popover` bg · `popover-foreground` text · `border` stroke + shadow

#### Sheet
Sides: top, right (default), bottom, left
Close button: shown by default, hide with `showCloseButton={false}`
Sub-components: `SheetTrigger`, `SheetContent`, `SheetHeader`, `SheetTitle`, `SheetDescription`, `SheetFooter`
- Surface: `background` · Overlay: `background` at 80% opacity · `border` on open edge

#### Tooltip
Positions: left, top (default), bottom, right (via `side` prop)
Patterns: basic, disabled button (wrapped in `<span>`), with keyboard shortcut (`Kbd`)
Requires `TooltipProvider` at app root
Sub-components: `Tooltip`, `TooltipTrigger`, `TooltipContent`
- Surface: `popover` bg · `popover-foreground` text · Rounded: `radius-md`

---

### Category 4 — Navigation (7 components)

#### Breadcrumb
Variants: basic, custom separator, with dropdown collapse, with ellipsis, link-component (asChild)
Sub-components: `Breadcrumb`, `BreadcrumbList`, `BreadcrumbItem`, `BreadcrumbLink`, `BreadcrumbPage`, `BreadcrumbSeparator`, `BreadcrumbEllipsis`
- Link: `primary` · Current page: `foreground` · Separator: `muted-foreground`

#### Command
Variants: basic menu, grouped (with `CommandGroup`), with keyboard shortcuts, dialog variant (`CommandDialog`)
Sub-components: `Command`, `CommandInput`, `CommandList`, `CommandEmpty`, `CommandGroup`, `CommandItem`, `CommandSeparator`, `CommandDialog`
- Surface: `popover` bg · Input: `background` · Item hover: `accent`
- Group label: `muted-foreground` · Separator: `border` · Shortcut: `muted-foreground`

#### Menubar
Variants: basic (File/Edit/View), checkbox items, radio groups, submenus, with icons
Sub-components: `Menubar`, `MenubarMenu`, `MenubarTrigger`, `MenubarContent`, `MenubarGroup`, `MenubarLabel`, `MenubarItem`, `MenubarSeparator`, `MenubarCheckboxItem`, `MenubarRadioGroup`, `MenubarRadioItem`, `MenubarSub`, `MenubarSubTrigger`, `MenubarSubContent`, `MenubarShortcut`
- Bar: `background` · Active trigger: `accent` · Content surface: `popover` bg

#### Navigation Menu
Variants: basic links, with content panel (mega-menu), asChild for custom router links
Sub-components: `NavigationMenu`, `NavigationMenuList`, `NavigationMenuItem`, `NavigationMenuTrigger`, `NavigationMenuContent`, `NavigationMenuLink`, `NavigationMenuIndicator`
- Trigger hover: `accent` bg · Content panel: `background` · Active indicator: `primary`

#### Pagination
Variants: numbered pages, prev/next only, icons-only (for data tables)
States: default, active page (`isActive`), disabled at boundaries
Sub-components: `Pagination`, `PaginationContent`, `PaginationItem`, `PaginationLink`, `PaginationPrevious`, `PaginationNext`, `PaginationEllipsis`
- Active page: `primary` bg · `primary-foreground` text · Inactive: `background` + `border`

#### Sidebar
Variants: sidebar (default), floating, inset
Collapsible modes: offcanvas (slides in/out), icon (collapses to icon strip), none (fixed)
Sides: left (default), right
States: expanded, collapsed · Keyboard: Cmd/Ctrl + B
Width: `--sidebar-width` (16rem desktop), `--sidebar-width-mobile` (18rem)
Sub-components: `SidebarProvider`, `Sidebar`, `SidebarHeader`, `SidebarFooter`, `SidebarContent`, `SidebarGroup`, `SidebarGroupLabel`, `SidebarGroupContent`, `SidebarMenu`, `SidebarMenuItem`, `SidebarMenuButton`, `SidebarMenuAction`, `SidebarMenuSub`, `SidebarMenuSubItem`, `SidebarMenuBadge`, `SidebarRail`, `SidebarInset`, `SidebarTrigger`, `SidebarSeparator`
Token bindings: all 8 sidebar tokens (`sidebar`, `sidebar-foreground`, `sidebar-primary`, `sidebar-primary-foreground`, `sidebar-accent`, `sidebar-accent-foreground`, `sidebar-border`, `sidebar-ring`)

#### Tabs
Styles: default (boxed), line (`variant="line"` on TabsList)
Orientations: horizontal (default), vertical
States: active, inactive, disabled (individual triggers)
Sub-components: `Tabs`, `TabsList`, `TabsTrigger`, `TabsContent`
- Active trigger: `background` with shadow · Inactive: transparent · Line variant: `border-b` + `primary` indicator

---

### Category 5 — Feedback & Status (7 components)

#### Alert
Variants: default, destructive (`variant="destructive"`)
Sub-components: `Alert`, `AlertTitle`, `AlertDescription`, `AlertAction`
- Default: `background` bg · `border` stroke · Icon + title `foreground`
- Destructive: `state-error-subtle` bg · `state-error` border · `destructive` text

#### Badge
Variants: default, secondary, outline, ghost, destructive, link
States: with icon (`data-icon="inline-start/end"`), with Spinner (loading)
- Default: `primary` bg · `primary-foreground` text
- Secondary: `secondary` bg · `secondary-foreground` text
- Outline: `border` stroke · `foreground` text · transparent bg
- Destructive: `destructive` bg · `destructive-foreground` text

#### Progress
Variants: basic (value 0–100), with label (inside `Field`), controlled (synced to a Slider)
RTL: inverts fill direction
- Track: `secondary` bg · Fill: `primary` · Label: `foreground`

#### Skeleton
Shapes: text line, avatar (circle), card block, form field, table row
- Background: `muted` · Animated shimmer via CSS animation
- Sizes via Tailwind: `h-[Npx]`, `w-[Npx]`, `rounded-*`

#### Sonner
Variants: default, success, info, warning, error, promise (async feedback)
Positions: top-left, top-center, top-right, bottom-left, bottom-center, bottom-right
Requires `<Toaster />` in root layout
- Surface: `background` · Border: `border` · `foreground` text
- Success: `state-success` accent · Warning: `state-warning` · Error: `state-error`

#### Spinner
Sizes: sm (`size-3`), default (`size-4`), lg (`size-6`) via Tailwind `size-*`
States: always spinning (active loading indicator)
Integration: embed inside Button, Badge, InputGroup, Empty
- Color: inherits `currentColor` · Animation: `animate-spin`

#### Toast (Deprecated)
Status: **Deprecated** — use Sonner instead
If legacy support needed: document only, do not scaffold new instances
- Direct teams to migrate to Sonner component

---

### Category 6 — Data Display (8 components)

#### Avatar
Sizes: sm, default, lg
Compositions: basic (image + text fallback), with badge (`AvatarBadge`), with badge icon, avatar group (`AvatarGroup`), group count (`AvatarGroupCount`), as dropdown trigger
Sub-components: `Avatar`, `AvatarImage`, `AvatarFallback`, `AvatarBadge`, `AvatarGroup`, `AvatarGroupCount`
- Fallback bg: `muted` · Fallback text: `muted-foreground` · Badge: `state-success` (online), `state-error` (busy)

#### Calendar
Modes: single date, range selection
Layout: standard, month/year dropdown (`captionLayout="dropdown"`)
Locale: Gregorian (default), Persian/Hijri
Options: `showWeekNumber`, `timeZone`, `dir` (RTL)
Cell size: controlled via `--cell-size` CSS variable
- Selected date: `primary` bg · `primary-foreground` text
- Range fill: `accent` · Today: `accent` outline · Nav buttons: `ghost` variant

#### Card
Sizes: default, sm (`size="sm"` for compact spacing)
Sub-components: `Card`, `CardHeader`, `CardTitle`, `CardDescription`, `CardAction`, `CardContent`, `CardFooter`
`CardAction`: top-right slot for buttons, badges, toggles
- Surface: `card` bg · `card-foreground` text · `border` stroke · Radius: `radius-lg`

#### Carousel
Orientations: horizontal (default), vertical
Item widths: full (100%), half (50%), third (33%), responsive combinations
Features: autoplay plugin, swipe gestures, API access via `setApi`
Sub-components: `Carousel`, `CarouselContent`, `CarouselItem`, `CarouselPrevious`, `CarouselNext`
- Nav buttons: `outline` Button variant · Item bg: inherits content

#### Chart
Types: Bar, Line, Area, Pie, Radar (all powered by Recharts v3)
Config: `ChartContainer` with `config` prop defining label, color, icon per series
Tooltip variants: label, name, indicator (dot/line/dashed), value
Legend: `ChartLegend` + `ChartLegendContent`
Accessibility: `accessibilityLayer` prop for keyboard + screen reader
Color system: `--chart-1` through `--chart-5` CSS variables
- Use `var(--color-KEY)` in Recharts fill/stroke props — never hardcode hex

#### Data Table
Built on TanStack Table (`@tanstack/react-table`)
Features: basic columns, row actions (DropdownMenu), pagination, column sorting, column filtering, column visibility toggle, row selection (checkboxes)
Uses base `Table` components: `Table`, `TableHeader`, `TableRow`, `TableHead`, `TableBody`, `TableCell`, `TableCaption`, `TableFooter`
- Header: `background` · Row hover: `accent` at low opacity · Selected row: `accent`

#### Date Picker
Variants: basic (single date), range picker, date of birth (with month/year dropdown), with text input, with time picker, natural language (`chrono-node` parsing)
Composition: `Popover` + `Calendar` + optional `Input`
- Trigger: `outline` Button · Popover surface: `popover` bg · Empty placeholder: `muted-foreground`

#### Table
Compositions: basic, with row actions (DropdownMenu in each row), sortable headers
Sub-components: `Table`, `TableCaption`, `TableHeader`, `TableRow`, `TableHead`, `TableBody`, `TableCell`, `TableFooter`
- Header: `background` or `muted` bg · `muted-foreground` header text
- Row dividers: `border` · Footer: `muted` bg · Hover row: `accent`

---

### Category 7 — Layout & Structure (6 components)

#### Accordion
Types: single (only one open), multiple (many open simultaneously)
Props: `collapsible` (allow closing active item in single mode), `disabled` on individual items
Compositions: plain, with `border` wrapper, inside `Card`
Sub-components: `Accordion`, `AccordionItem`, `AccordionTrigger`, `AccordionContent`
- Trigger: `foreground` · Divider: `border` · Content text: `foreground`
- Chevron rotates on open · Radius: `radius-md` on wrapper

#### Aspect Ratio
Ratios: 16/9 (widescreen), 4/3, 1/1 (square), 9/16 (portrait), any custom numeric ratio
Combine with `object-cover` on child images for consistent scaling
- No color tokens — structural only · Radius via `rounded-*` on child

#### Collapsible
Patterns: basic Q&A disclosure, settings panel, file tree (nested collapsibles)
Sub-components: `Collapsible`, `CollapsibleTrigger`, `CollapsibleContent`
- Trigger: inherits Button or ghost styles · Content: `foreground` text

#### Resizable
Orientations: horizontal (default), vertical
Handle: hidden (default), visible (`withHandle` prop on `ResizableHandle`)
Sub-components: `ResizablePanelGroup`, `ResizablePanel`, `ResizableHandle`
- Panel bg: `background` · Handle: `border` color · Handle icon: `muted-foreground`

#### Scroll Area
Orientations: vertical (default), horizontal
Sub-components: `ScrollArea`, `ScrollBar`
- Scrollbar track: transparent · Thumb: `border` color · Radius: `radius-full`
- Hides native browser scrollbar across all browsers

#### Separator
Orientations: horizontal (default), vertical
- Color: `border` token · No background fill — line only

---

### Category 8 — Content & Display (4 components)

#### Empty
Variants: icon (using `EmptyMedia variant="icon"`), default media, outline border, with background, with avatar, with avatar group, with input form
Sub-components: `Empty`, `EmptyHeader`, `EmptyMedia`, `EmptyTitle`, `EmptyDescription`, `EmptyContent`
- Title: `foreground` · Description: `muted-foreground`
- Icon bg: `muted` · Icon color: `muted-foreground` · Border: `border`

#### Item
Variants: default (transparent), outline (with `border`), muted (`muted` bg)
Sizes: default, sm, xs
States: hover (when `asChild` renders as link), focus, active/selected
Sub-components: `ItemGroup`, `Item`, `ItemHeader`, `ItemMedia` (default/icon/image), `ItemContent`, `ItemTitle`, `ItemDescription`, `ItemActions`, `ItemFooter`
- Title: `foreground` · Description: `muted-foreground`
- Outline border: `border` · Muted bg: `muted` · Hover: `accent`

#### Kbd
Patterns: single key (`<Kbd>Ctrl</Kbd>`), key group (`KbdGroup` for combinations like Ctrl + B)
Integration: inside Button, Tooltip, InputGroup, Command palette hints
Sub-components: `Kbd`, `KbdGroup`
- Background: `muted` · Text: `muted-foreground` · Border: `border` · Radius: `radius-sm`
- Font: monospace (`font-family-mono`)

#### Typography
Styles: h1, h2, h3, h4, paragraph, blockquote, lead, large, small, muted, inline code
- h1: 36px bold, tracking-tight · h2: 30px semibold + border-bottom
- h3: 24px semibold · h4: 20px semibold
- Lead: 20px `muted-foreground` · Large: 18px semibold · Small: 14px medium
- Muted: 14px `muted-foreground` · Inline Code: `muted` bg, `font-mono`, `radius-sm`
- Blockquote: `border-l-2` in `primary` · italic text

---

### Category 9 — Utilities / Providers (1 component)

#### Direction
Purpose: RTL/LTR text-direction provider for internationalisation
Directions: ltr (default), rtl
Hook: `useDirection()` to read current direction inside components
Usage: wrap app root with `<DirectionProvider direction="rtl">` and set `<html dir="rtl">`
- No visual tokens — structural provider only

---

### Foundation Documentation Frames (always created)

**Color Palette Grid** — every primitive step (50–950) for all brand colors, labeled with variable names and hex values.

**Typography Scale** — every text style rendered live in the user's chosen fonts, showing name, size, weight, and line height.

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
    ↳ Core pairs (background/foreground, card, popover, primary, secondary,
                  muted, accent, destructive, border, input, ring)
    ↳ Chart tokens (chart-1 … chart-5)
    ↳ Sidebar tokens (sidebar, sidebar-primary, sidebar-accent, sidebar-border, sidebar-ring)
    ↳ Radius scale (--radius base + sm/md/lg/xl/2xl/3xl/4xl derived steps)
    ↳ State tokens (success, warning, error, info + subtle variants)

Text Styles:        16 styles
Effect Styles:      6 elevation levels

Components (59 total across 9 categories):

  Category 1 — Form & Input (13)
    ✓ Checkbox, Combobox, Field, Input, Input Group, Input OTP
    ✓ Label, Native Select, Radio Group, Select, Slider, Switch, Textarea

  Category 2 — Actions / Buttons (4)
    ✓ Button (6 styles × 8 sizes), Button Group, Toggle, Toggle Group

  Category 3 — Overlay & Floating UI (9)
    ✓ Alert Dialog, Context Menu, Dialog, Drawer
    ✓ Dropdown Menu, Hover Card, Popover, Sheet, Tooltip

  Category 4 — Navigation (7)
    ✓ Breadcrumb, Command, Menubar, Navigation Menu
    ✓ Pagination, Sidebar, Tabs

  Category 5 — Feedback & Status (7)
    ✓ Alert, Badge, Progress, Skeleton, Sonner, Spinner
    ✓ Toast (deprecated — documented only, redirect to Sonner)

  Category 6 — Data Display (8)
    ✓ Avatar, Calendar, Card, Carousel
    ✓ Chart (Bar/Line/Area/Pie/Radar), Data Table, Date Picker, Table

  Category 7 — Layout & Structure (6)
    ✓ Accordion, Aspect Ratio, Collapsible, Resizable, Scroll Area, Separator

  Category 8 — Content & Display (4)
    ✓ Empty, Item, Kbd, Typography

  Category 9 — Utilities / Providers (1)
    ✓ Direction (DirectionProvider — ltr/rtl)

  Foundation frames:
    ✓ Color Palette Grid
    ✓ Typography Scale

Next steps:
  → Run /ds-sync-tokens <figma-url> to pull these tokens into your codebase
  → Run /ds-add-component <Name> <figma-node-id> to scaffold code for any component
  → Run /ds-audit to check codebase for hardcoded values that should be tokens
  → Run /ds-status to see full design system health
```

---

## Rules
- ALWAYS load `figma-use` skill before any Figma MCP tool call — never skip this
- Collect ALL brand inputs before writing a single variable — do not guess or use defaults silently
- Semantic tokens must alias primitives — never reference raw hex in the Semantic collection
- Every surface token must have a matching `-foreground` token (background/foreground pair convention)
- Radius scale must use `calc(var(--radius) * X)` — never hardcode pixel values in semantic layer
- Chart tokens (`chart-1..5`) and sidebar tokens are always created, even if no chart/sidebar component is scaffolded yet
- Toast is deprecated — always note this and direct users to Sonner when Toast is requested
- Derived color steps must be computed mathematically — do not invent values
- If any collection already exists in the Figma file → show a diff and ask before overwriting
- Dark mode variables are only created if the user said yes
- Tertiary color section is skipped entirely if the user did not provide it
