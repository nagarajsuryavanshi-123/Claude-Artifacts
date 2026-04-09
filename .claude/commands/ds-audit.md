---
description: Scan the codebase for hardcoded design values that should be design tokens
argument-hint: [--fix] [--path <directory>]
allowed-tools: [Read, Glob, Grep, Write, Edit, Bash]
---

# Design System Audit

## Arguments
$ARGUMENTS — parse: `--fix` (auto-replace found values with tokens), `--path` (scope scan to a subdirectory, default: entire project)

## What to Scan

Determine scan path:
- If `--path` provided → use that directory
- Otherwise → scan the entire project root, excluding `node_modules`, `.claude`, `.git`

### Hardcoded colors
```bash
grep -rn "#[0-9a-fA-F]\{3,6\}\|rgb(\|hsl(" <scan-path> \
  --include="*.css" --include="*.scss" --include="*.tsx" --include="*.ts" --include="*.vue" \
  --exclude-dir=node_modules --exclude-dir=.git --exclude-dir=.claude
```

### Hardcoded spacing
```bash
grep -rn "padding:\s*[0-9]\|margin:\s*[0-9]\|gap:\s*[0-9]" <scan-path> \
  --include="*.css" --include="*.scss" \
  --exclude-dir=node_modules --exclude-dir=.git --exclude-dir=.claude
```

### Hardcoded font values
```bash
grep -rn "font-size:\s*[0-9]\|font-family:\s*['\"]" <scan-path> \
  --include="*.css" --include="*.scss" \
  --exclude-dir=node_modules --exclude-dir=.git --exclude-dir=.claude
```

## Output Format

```
DS AUDIT REPORT
===============
Scan path:           <path>
Generated:           <timestamp>

Hardcoded colors:    N occurrences in X files
Hardcoded spacing:   N occurrences in X files
Hardcoded fonts:     N occurrences in X files

Top offenders:
  <file>:<line> → <value> → suggested token: --color-primary
  ...

Total violations: N
Audit score: X% (100 - violations/total_lines * 100)
```

## If `--fix`

For each violation:
1. Read the token file at `src/styles/tokens.css`
   # TODO: verify this path — update to match your actual token file
2. Find the closest matching token (within 10% tolerance for numeric values, exact match for colors)
3. If match found → replace the hardcoded value with `var(--token-name)`
4. If no match → flag as `NEEDS_TOKEN: <value>` and skip (do not guess)
5. Write `DS_AUDIT_FIX_LOG.md` in the project root listing every change:
   ```
   FILE: src/components/Button/Button.module.css
   LINE 12: #3b82f6 → var(--color-primary)
   LINE 18: 16px → var(--space-4)
   ```

## Known Token File
`src/styles/tokens.css`
# TODO: verify this path — update to match your actual token file
