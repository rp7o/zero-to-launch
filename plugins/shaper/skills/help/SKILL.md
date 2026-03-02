---
name: shaper:help
description: Context-aware command menu showing all available shaper commands with recommendations based on current business state. Use when the user says "help", asks how to use shaper, asks where to start, asks what shaper does, or seems unsure what to do next.
---

Before running this command, read `../../references/shared-protocols.md`.

# help — Context-Aware Command Menu

Shows available commands with context-aware recommendations. Adapts based on current business state.

## When to Run

- User says `shaper:help`.
- User asks "how do I use shaper?", "what does shaper do?", "where do I start?", or any orientation question about the plugin.
- User seems unsure what to do next.
- Proactively after `shaper:kickoff`.

## Workflow

### Step 1: Read State

Read `.shaper/business_state.md` if it exists to determine context.

### Step 2: Present Commands

```markdown
## Shaper Commands

| Command | What it does |
|---|---|
| `/shaper:kickoff` | Start shaping a new business idea |
| `/shaper:canvas` | Develop the 9-block Lean Canvas |
| `/shaper:market` | Analyze the competitive landscape and market sizing |
| `/shaper:validate` | Review the Lean Canvas for holes in the business model |
| `/shaper:pitch` | Translate the validated canvas into marketing copy or a pitch deck |
| `/shaper:status` | Show current business state, canvas progress, and recent decisions |
| `/shaper:help` | This menu |
```

### Step 3: Contextual Recommendation

Based on business state, add a recommendation section:

**No `.shaper/business_state.md`:**
```markdown
### Where to Start
You don't have a business idea set up yet.
→ `/shaper:kickoff` — Let's capture your idea and get started.
```

**State initialized but no Lean Canvas:**
```markdown
### Recommended Next
→ `/shaper:canvas` — Idea captured. Let's map out the 9 blocks of your Lean Canvas.
→ `/shaper:status` — See what's been set up so far.
```

**Has Lean Canvas but no market research:**
```markdown
### Recommended Next
→ `/shaper:market` — Canvas drafted. Let's size the market and check the competition.
→ `/shaper:validate` — Or skip straight to a critical review of your canvas.
→ `/shaper:status` — View canvas progress and recent activity.
```

**Has market research but not yet validated:**
```markdown
### Recommended Next
→ `/shaper:validate` — Market research is done. Let's stress-test the business model.
→ `/shaper:status` — See full state details before validating.
```

**Validated (Go decision made):**
```markdown
### Recommended Next
→ `/shaper:pitch` — Business model validated. Let's turn it into a pitch or landing page copy.
→ `/maker:discover` — Ready to build? Hand off the product brief to the Maker plugin.
→ `/shaper:status` — Review validation details and open risks.
```

**Validated (No-Go decision):**
```markdown
### Recommended Next
→ `/shaper:canvas` — Revisit and rework the Lean Canvas based on the validation findings.
→ `/shaper:kickoff` — Start fresh with a new idea.
→ `/shaper:status` — Review the decision history.
```

## State Update

None. Help is informational only.
