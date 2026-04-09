---
description: Analyze your Figma design system and codebase, then auto-generate a tailored suite of design system slash commands specific to your project's tokens, components, and workflows.
argument-hint: [figma-url] [--preview]
allowed-tools: [Read, Glob, Grep, Write, Edit, Bash, Agent]
---

# Generate Design System Commands

## Arguments

The user invoked this with: `$ARGUMENTS`

Parse before doing anything:
- First positional arg → Figma file URL (optional — used to pull real component + token names)
- `--preview` → print what commands would be generated without writing any files

---

## Overview

This command inspects your Figma design system and codebase, then writes a custom set of slash commands tailored to YOUR actual tokens, components, and workflows — not generic templates.

After this runs, your team gets commands like:
- `/ds-sync-tokens` — sync your exact token collections from Figma to code
- `/ds-add-component` — scaffold a component with your actual variants
- `/ds-audit` — check your codebase for hardcoded values that should be tokens
- `/ds-docs` — generate a component documentation page from Figma + code
- `/ds-status` — show design system health at a glance

---

## Phase 1 — Discover What Exists

Run these in parallel:

### Codebase scan
Use Glob + Grep to find:
- Existing token files (`tokens.css`, `theme.ts`, `design-tokens.json`, `tailwind.config.*`)
- Component directory and component names (list all found)
- Styling approach (Tailwind / CSS Modules / styled-components / plain CSS)
- Any existing design system commands in `.claude/commands/`

### Figma inspection (if URL provided)
Spawn `figma-use` sub-agent (MANDATORY before any Figma call) then get:
- Variable collection names and modes
- All component names and their variant axes
- Text style names and effect style names

If no URL provided, skip Figma inspection and work from codebase only. Proceed immediately.

---

## Phase 2 — Build the Command Suite

Based on discovery, generate ALL of the following commands. Fill in real names from your project — never use placeholder names. If a path cannot be detected, write the closest reasonable default and add a `# TODO: verify this path` comment.

---

### Command 1: `/ds-sync-tokens`
Write to `.claude/commands/ds-sync-tokens.md`

### Command 2: `/ds-add-component`
Write to `.claude/commands/ds-add-component.md`

### Command 3: `/ds-audit`
Write to `.claude/commands/ds-audit.md`

### Command 4: `/ds-docs`
Write to `.claude/commands/ds-docs.md`

### Command 5: `/ds-status`
Write to `.claude/commands/ds-status.md`

---

## Phase 3 — Write All Commands

Write each command file to `.claude/commands/`. After writing:

1. List all files created
2. Update `CLAUDE.md` — add each new command to the "Custom Commands" section
3. If `--preview` was passed → print all file contents but do NOT write them

---

## Final Report

```
DESIGN SYSTEM COMMANDS GENERATED
==================================
Commands created: 5
  ✓ /ds-sync-tokens     → .claude/commands/ds-sync-tokens.md
  ✓ /ds-add-component   → .claude/commands/ds-add-component.md
  ✓ /ds-audit           → .claude/commands/ds-audit.md
  ✓ /ds-docs            → .claude/commands/ds-docs.md
  ✓ /ds-status          → .claude/commands/ds-status.md

Customized with:
  Token file: [path]
  Component path: [path]
  Stack: [detected]
  Figma collections: [list or "not inspected"]

CLAUDE.md updated: yes

Run `/ds-status` to see your current design system health.
```

---

## Rules
- All generated commands must use real paths and names from the project — no `[YOUR_PATH]` placeholders left in output files
- If a path cannot be detected, write the closest reasonable default and add a `# TODO: verify this path` comment in the generated file
- Never overwrite an existing command without showing a diff and asking (one question)
- Generated commands inherit the same rules as this project: Windows-compatible, load `figma-use` before Figma calls, no hardcoded values
