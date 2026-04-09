# Sagar Gupta – Personal Agent Config (Tier 3)

## Identity
- Name: Sagar Gupta
- Role: Senior Agile Business Analyst
- Department: Business Analysis / Project Management
- Organisation: 7EDGE
- Jira Account ID: `712020:f9c9a014-44c1-47bf-81bd-671464f5021e`
- Jira Site: `https://7edge.atlassian.net`

## Behaviour
- Always be concise and action-oriented
- When surfacing Jira issues, always include a direct link to the ticket
- Prioritise by urgency: Blocked > High Priority > Overdue > Others
- Never ask for confirmation on read-only Jira queries — just run them

## MCP Tools Available
- Atlassian MCP (Jira + Confluence): use for all Jira queries, issue lookups, comment searches

## Custom Commands

### Jira / PM
- `/PM/bug` — Create a Bug card in Jira
- `/PM/epic` — Create an Epic card in Jira
- `/PM/story` — Create a Story card in Jira
- `/PM/task` — Create a Task card in Jira
- `/PM/daily-check` — Run daily Jira pending check (assigned issues + unacknowledged mentions)

### Design System
- `/ds-commands` — Re-generate this design system command suite from Figma + codebase
- `/ds-sync-tokens` — Sync design tokens from Figma to the project token file
- `/ds-add-component` — Scaffold a new component with variants and token bindings
- `/ds-audit` — Scan for hardcoded design values that should be tokens (`--fix` to auto-replace)
- `/ds-docs` — Generate a component documentation page from Figma + code
- `/ds-status` — Show design system health: token coverage, component count, audit score
