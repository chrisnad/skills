---
name: prd-to-epic
description: Use when user wants to convert a PRD into a Jira epic with child tasks, break a PRD into Jira tickets, or plan implementation work in Jira (project ACTP).
---

# PRD to Epic

Break a PRD into a Jira Epic with child Tasks using vertical slices (tracer bullets). Atlassian MCP only.

## When NOT to use

- Single-task work or bug fixes — create one ticket directly
- Non-<projectID> projects — constants below are <projectID>-specific
- Unapproved PRDs — epic should reflect committed scope

## Constants (personnalize)

- **Cloud ID**: `<company>.atlassian.net`
- **Project**: `<projectID>`
- **Assignee**: `<assignee ID / your atlassian ID>`
- **Board ID**: `<board ID>`

## Quick Reference

| Step | MCP tool | Key fields |
|------|----------|------------|
| Create Epic | `mcp__atlassian__createJiraIssue` | `issueTypeName: "Epic"`, no `parent` |
| Create Task | `mcp__atlassian__createJiraIssue` | `issueTypeName: "Task"`, `parent: "<EPIC>"` |
| Link blocker | `mcp__atlassian__createIssueLink` | `type: "Blocks"`, `inwardIssue`=blocker, `outwardIssue`=blocked |

## Process

### 1. Locate the PRD

Ask the user for the PRD source. It could be:
- A GitHub issue number/URL → fetch with `gh issue view <number>`
- A Confluence page → fetch with `mcp__atlassian__getConfluencePage`
- A Jira ticket → fetch with `mcp__atlassian__getJiraIssue`
- A local file → read it
- Pasted text in the conversation

If the PRD is not already in your context window, fetch it.

### 2. Explore the codebase (optional)

If unfamiliar with the affected area, read 2-3 files referenced in the PRD to ground the slice breakdown in real code. Skip if codebase is already in context.

### 3. Draft vertical slices

Break the PRD into **tracer bullet** slices. Each slice becomes a Jira Task under the Epic.

Slices may be 'HITL' (human-in-the-loop) or 'AFK' (autonomous). HITL slices require human interaction (architectural decision, design review). AFK slices can be implemented without human interaction. Prefer AFK over HITL where possible.

<vertical-slice-rules>
- Each slice delivers a narrow but COMPLETE path through every layer (schema, API, UI, tests)
- A completed slice is demoable or verifiable on its own
- Prefer many thin slices over few thick ones
</vertical-slice-rules>

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each slice, show:

- **Title**: short descriptive name
- **Type**: HITL / AFK
- **Blocked by**: which other slices (if any) must complete first
- **User stories covered**: which user stories from the PRD this addresses

Ask the user:

- Does the granularity feel right? (too coarse / too fine)
- Are the dependency relationships correct?
- Should any slices be merged or split further?
- Are the correct slices marked as HITL and AFK?

**Iterate until the user approves the breakdown.**

### 5. Create the Jira Epic

Create the parent Epic using `mcp__atlassian__createJiraIssue`:

```
cloudId: "<company>.atlassian.net"
projectKey: "<projectID>"
issueTypeName: "Epic"
summary: "<Epic title derived from PRD>"
description: <see epic-template below>
contentFormat: "markdown"
assignee_account_id: ...
```

<epic-template>
## PRD Source

[Link or reference to the original PRD]

## Overview

[1-2 paragraph summary of the PRD goals]

## Slices

[Numbered list of all planned task slices with their titles]
</epic-template>

Record the Epic's issue key (e.g., <projectID>-XXX) for use as the parent.

### 6. Create the Jira Tasks

For each approved slice, create a Task using `mcp__atlassian__createJiraIssue`:

```
cloudId: "<company>.atlassian.net"
projectKey: "<projectID>"
issueTypeName: "Task"
summary: "<slice title>"
description: <see task-template below>
contentFormat: "markdown"
assignee_account_id: ...
parent: "<EPIC-KEY>"
```

Create tasks in dependency order (blockers first) so you can reference real issue keys.

<task-template>
## Parent Epic

[EPIC-KEY]

## What to build

A concise description of this vertical slice. Describe the end-to-end behavior, not layer-by-layer implementation. Reference specific sections of the parent PRD rather than duplicating content.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Blocked by

- Blocked by [TASK-KEY] (if any)

Or "None - can start immediately" if no blockers.

## Type

HITL / AFK

## User stories addressed

- User story 3
- User story 7
</task-template>

### 7. Create dependency links

After all tasks are created, for each blocking relationship use `mcp__atlassian__createIssueLink`:

```
cloudId: "<company>.atlassian.net"
type: "Blocks"
inwardIssue: "<BLOCKER-KEY>"    (the task that blocks)
outwardIssue: "<BLOCKED-KEY>"   (the task that is blocked)
```

### 8. Summary

Print a summary table:

| Key | Title | Type | Blocked by |
|-----|-------|------|------------|
| <projectID>-XXX | Epic title | Epic | - |
| <projectID>-YYY | Slice 1 | AFK | None |
| <projectID>-ZZZ | Slice 2 | HITL | <projectID>-YYY |

Include the board URL: `https://<company>.atlassian.net/jira/software/c/projects/<projectID>/boards/<boardID>`

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Forgot `parent:` on Task | Tasks without parent don't roll up under Epic |
| Reversed `inwardIssue` / `outwardIssue` | `inwardIssue`=blocker, `outwardIssue`=blocked |
| Created tasks before Epic | Need Epic key first to set `parent:` |
| Created links before all tasks exist | Need real keys to reference; do links last |
| Modified or closed the original PRD | Don't — PRD is source of record |
| Skipped the user quiz in step 4 | Slices unvalidated → rework after creation |
| Used `...` or unscoped search to explore codebase | Monorepo — scope to `domains/<team>/` |
