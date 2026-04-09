---
description: Show the current state of your design system — token coverage, component count, audit score, and Figma sync status
argument-hint: [figma-url]
allowed-tools: [Read, Glob, Grep, Bash, Agent]
---

# Design System Status

## Arguments
$ARGUMENTS — optional Figma URL to compare against live Figma state

## Steps

Run all checks in parallel:

### 1. Token check
- Read `src/styles/tokens.css`
  # TODO: verify this path — no token file was detected; update to match your project
- Count total tokens, grouped by category (Color, Spacing, Typography, Radius, Shadows)
- If Figma URL provided: load `figma-use` skill, call `get_variable_defs`, diff against token file

### 2. Component check
- Glob `src/components/*/index.tsx`
  # TODO: verify this path — no component directory was detected; update to match your project
- Count total components and list their names

### 3. Audit score
- Run a quick hardcoded-value scan (no --fix) across `src/`
- Count total violations
- Score = `100 - (violations / total_lines * 100)`, rounded to nearest integer

### 4. Commands check
- Glob `.claude/commands/ds-*.md`
- List all available design system commands

## Output Format

```
DESIGN SYSTEM STATUS
====================
Generated: <timestamp>

## Tokens
Token file:    src/styles/tokens.css
Total tokens:  N
  Colors: N  |  Typography: N  |  Spacing: N  |  Radius: N  |  Shadows: N
Figma sync:    IN SYNC / N tokens differ / NOT CHECKED — no URL provided

## Components
Component dir: src/components/
Total:         N components
  <list component names, one per line>

## Audit Score
Violations: N  →  Score: X%
  (run /ds-audit for full breakdown)

## Commands Available
  /ds-commands      Generate this command suite
  /ds-sync-tokens   Sync tokens from Figma
  /ds-add-component Scaffold a new component
  /ds-audit         Scan for hardcoded values
  /ds-docs          Generate component docs
  /ds-status        This report

## Next Actions
<Based on findings — e.g.:
  - "Run /ds-sync-tokens — 3 tokens are out of sync"
  - "Run /ds-audit --fix — 12 hardcoded values found"
  - "Token file not found — create src/styles/tokens.css or update the path in ds-*.md commands">
```

## Rules
- Load `figma-use` skill before every Figma MCP call — never call Figma tools directly
- If token file or component directory does not exist, report that clearly with the `# TODO` path hint rather than erroring silently
- Run all checks in parallel for speed
