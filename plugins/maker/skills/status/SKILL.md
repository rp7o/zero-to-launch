---
name: maker:status
description: Show project state, progress, and recommended next steps. Use when the user says "status", when returning to a project after time away, or when unsure what to do next. Read-only.
---

# status — Project State Overview

Shows where the project is: what's done, what's pending, what's next. Read-only.

## When to Run

- Returning to a project after time away.
- When unsure what to do next.
- User explicitly says `maker:status`.

## Workflow

### Step 1: Read State

Read `.maker/project_state.md`. If it doesn't exist: "No project state found. Run `/maker:kickoff` to get started."

### Step 2: Present Overview

```markdown
## Project Status: [Name]

### Profile
- **Type**: [type]
- **Stack**: [stack]
- **Working style**: [preference]

### Phase Progress
| Phase | Status | Last Updated |
|-------|--------|-------------|
| Product context | [not started / discovered / complete] | [date] |
| Architecture | [not started / planned / implemented / revised] | [date] |
| Implementation | [not started / in progress / complete] | [date] |
| Quality gates | [not run / passing / failing] | [date] |

### Recent Decisions
[Last 3-5 entries from Decision Log — date + decision]

### Deferred
[Count] deferred decisions. [List the most relevant 2-3.]

### Known Issues
[List from Implementation Status, if any]

### Iterations
[Count] iterations recorded. [Brief summary of the most recent.]

### Lessons
[Read `.maker/lessons.md`. Count entries. Show most recent 1-2.]
```

### Step 3: Suggest Next Steps

Based on the current state, suggest 2-3 specific commands:

- If product context is missing → suggest `/maker:discover`.
- If architecture is missing → suggest `/maker:architect`.
- If architecture exists but not built → suggest `/maker:build`.
- If built but quality gates haven't passed → suggest running quality gates (detect from `package.json` scripts).
- If everything is done → suggest `/maker:discover` for new features or `/maker:research` for improvements.
- If there are known issues → suggest reviewing known issues and running quality gates.

```markdown
### Suggested Next Steps

→ `/maker:[command]` — [why this makes sense now]
→ `/maker:[command]` — [alternative path]
```

## State Update

- Add to Session Log only.
- Do not modify any other state (this is a read-only command).
