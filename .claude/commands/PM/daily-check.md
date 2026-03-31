# Daily Jira Pending Check

Run my daily Jira pending check and give me a clear summary of everything that needs my attention today.

## What to check

Using the Jira MCP tools, do the following:

### 1. Assigned & Pending Issues
Search for all Jira issues where:
- Assignee = Sagar Gupta (account ID: `712020:f9c9a014-44c1-47bf-81bd-671464f5021e`)
- Status is NOT in: Done, Deployed, Closed, Released, Resolved

### 2. Unacknowledged Mentions
Search for Jira issues where:
- A comment mentions `@Sagar Gupta`
- The last comment on that issue mentioning Sagar was NOT written by Sagar himself
(i.e., someone is waiting for his response)

## Output Format

Present the results in two clear sections:

**📌 Assigned & Pending (X items)**
For each issue: ticket key (as a link), summary, status, project name, priority, last updated date

**💬 Needs My Reply (X items)**
For each issue: ticket key (as a link), summary, who mentioned me, when they mentioned me, project name

End with a one-line summary like:
> "You have X pending tasks and Y unacknowledged mentions. Priority item: [highest priority ticket]."

Keep the tone concise and actionable.
