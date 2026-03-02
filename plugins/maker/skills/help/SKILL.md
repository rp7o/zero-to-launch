---
name: maker:help
description: Context-aware command menu showing all available maker commands with recommendations based on current project state. Use when the user says "help", asks how to use maker, asks where to start, asks what maker does, or seems unsure what to do next.
---

# help — Context-Aware Command Menu

Shows available commands with context-aware recommendations. Adapts based on current project state.

## When to Run

- User says `maker:help`.
- User asks "how do I use maker?", "what does maker do?", "where do I start?", or any orientation question about the plugin.
- User seems unsure what to do next.
- Proactively after `maker:kickoff` and after first `maker:build`.

## Workflow

### Step 1: Read State

Read `.maker/project_state.md` if it exists to determine context.

### Step 2: Present Commands

```markdown
## Maker Commands

| Command | What it does |
|---|---|
| `/maker:kickoff` | Initialize project context and preferences |
| `/maker:discover` | Interactive product discovery — flesh out what you're building |
| `/maker:research [topic]` | Investigate technology, APIs, competitors, or market |
| `/maker:architect` | Design project structure and record technical decisions |
| `/maker:build` | Generate code from the architecture plan |
| `/maker:status` | See project state, progress, and next steps |
| `/maker:help` | This menu |
```

### Step 3: Contextual Recommendation

Based on project state, add a recommendation section:

**No .maker/project_state.md:**
```markdown
### Where to Start
You don't have a project set up yet.
→ `/maker:kickoff` — Let's get started.
```

**Has profile but no product context:**
```markdown
### Recommended Next
→ `/maker:discover` — Let's figure out what you're building.
```

**Has product context but no architecture:**
```markdown
### Recommended Next
→ `/maker:architect` — Product context is ready. Let's plan the technical structure.
→ `/maker:research [topic]` — If you want to investigate a technology or the competitive landscape first.
```

**Has architecture but not built:**
```markdown
### Recommended Next
→ `/maker:build` — Architecture is planned. Ready to generate code.
```

**Fully built:**
```markdown
### Recommended Next
→ Run quality gates (detect commands from `package.json` scripts).
→ `/maker:discover` — Add a new feature or change requirements.
→ `/maker:status` — See the full project state.
```

## State Update

None. Help is informational only.
