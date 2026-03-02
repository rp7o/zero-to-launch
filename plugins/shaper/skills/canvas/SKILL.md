---
name: shaper:canvas
description: Develop the 9-block Lean Canvas for a business idea. Use when defining the business model, target audience, and value proposition.
---

# canvas — The 9-Block Lean Canvas

Maps the business model using the Lean Canvas framework to prepare for a Go/No-Go decision.

Before running this command, read `../../references/shared-protocols.md` and `../../references/handbook-templates.md`.

## When to Run

- After `shaper:kickoff` (natural flow).
- When the user explicitly says `shaper:canvas`.
- When revisiting business fundamentals or pivoting.

## Re-run Behavior

If a canvas already exists in `.shaper/business_state.md`:
1. Show the current canvas summary.
2. Ask which block the user wants to update.
3. Apply cascade warnings based on which block is changing (see **Block Dependencies** below).

### Block Dependencies

When a block changes during a re-run, warn about the downstream blocks that are now potentially stale:

| Block Changed | Blocks Potentially Invalidated |
|---|---|
| Problem | Solution, UVP, Channels, Key Metrics |
| Customer Segments | Channels, UVP, Revenue Streams |
| UVP | Channels, Revenue Streams |
| Revenue Streams | Cost Structure alignment |

**Cascade warnings to apply** (from `shared-protocols.md` Section 3):
- If `market` has already run (check Session Log): warn "This change may make your market research stale. Consider re-running `shaper:market`."
- If a Go decision was made (check Validation section): warn "The business model has changed since your Go decision. Re-validate before building."
- If `brief.md` exists: warn "The product brief was already handed to Maker. If this change is material, re-run `shaper:validate`."

After completing a re-run, append a row to the **Decision Log** in `.shaper/business_state.md`:
`| [date] | Canvas re-run — [blocks changed] | [reason given] | Active |`

Also append a row to the **Iteration History**:
`| [date] | [blocks changed] | [reason] | canvas |`

## Workflow

Depending on the working preference set in kickoff (read from `working_preference` in `.shaper/business_state.md`):

### If "Thorough" (Step-by-Step)
Ask about each block sequentially. Do not ask them all at once. Branch and dig deeper if the answers are vague.

1. **Problem (1-3 problems)**: The main pain points your target user faces.
2. **Customer Segments**: Who has the problem? Define your ideal, early adopter target audience.
3. **Unique Value Proposition (UVP)**: A clear message explaining why you are different/worth buying.
4. **Solution (1-3 solutions)**: The simplest features or services that solve the problem.
5. **Channels**: How will you reach your customers?
6. **Revenue Streams**: How will you make money?
7. **Cost Structure**: Major costs to operate (development, marketing, hosting).
8. **Key Metrics**: What activities matter most (e.g., DAU, conversion rate).
9. **Unfair Advantage**: A barrier to entry. (Optional — skip if they don't have one).

### If "Balanced" (Grouped Sections)
Group related blocks (2-3 at a time) and ask about them together. After each group, show a brief summary and ask "Any changes before we move on?"

**Group 1 — The Problem Space**: Problem + Customer Segments
**Group 2 — The Value Story**: UVP + Solution
**Group 3 — Growth & Revenue**: Channels + Revenue Streams
**Group 4 — Operations & Edge**: Cost Structure + Key Metrics + Unfair Advantage

Use reasonable defaults where answers are implied (e.g., if Solution clearly implies a SaaS model, default Revenue Streams to "SaaS subscription" and confirm rather than asking from scratch).

### If "Fast" (Draft & Correct)
Generate a completely filled-out 9-block canvas based *only* on the initial idea from `kickoff`.

Present the drafted Canvas and ask:
"Here is a draft of the Lean Canvas based on your idea. Which blocks should we tweak or completely change before we validate?"

## Output

Write the finalized canvas to `handbook/business/lean-canvas.md` using the template in `../../references/handbook-templates.md` (Section 1).

```markdown
## Lean Canvas Drafted

The canvas has been recorded in `handbook/business/lean-canvas.md`.

### Suggested Next Step

→ `/shaper:market` — Let's look at the competitors and see where this fits in the landscape.
→ `/shaper:validate` — If you don't need market research, let's review this canvas for holes and make a Go/No-Go decision.
→ `/shaper:help` — See all available commands.
```

## State Update

- Update `.shaper/business_state.md` Lean Canvas section with all 9 block values.
- Write to `handbook/business/lean-canvas.md`.
- **Session Log**: Append today's date, "shaper:canvas", "Canvas [drafted/updated] — [N] blocks filled".
