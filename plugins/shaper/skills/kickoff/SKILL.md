---
name: shaper:kickoff
description: Start shaping a new business idea. Use when starting from scratch or when the user says "shaper", "shape this idea", or "help me form a business."
---

# kickoff — Shape a New Business Idea

Captures the initial spark of an idea and sets up the Shaper's internal state. It is very fast and leads directly into the Lean Canvas.

Before running this command, read `../../references/shared-protocols.md`.

## When to Run

- User asks to evaluate a new business idea.
- User explicitly says `shaper:kickoff`.

## Re-run Behavior

If `.shaper/business_state.md` already exists:
1. Read it. Show a one-line summary: "You are already shaping: [idea]. Working preference: [preference]."
2. Use the `AskUserQuestion` tool to ask: "What would you like to do?" with these single-select options:
   - **Continue where I left off** — Go to the next recommended step (same as `/shaper:help`).
   - **Start a new idea** — Capture a new idea and reset working preferences. (Previous state is archived in Session Log.)
3. If "Continue": recommend the appropriate next skill based on canvas and research status.
4. If "New idea": proceed with Step 1 and Step 2 of the normal workflow. Append a row to Session Log noting the idea was reset.

## Workflow

### Step 1: The Spark

Ask: "What is the core idea you want to explore? (e.g., 'Airbnb for dogs' or 'A better CRM for mechanics')"

Wait for response.

### Step 2: Working Preferences

Use the `AskUserQuestion` tool to ask: "How do you like to work with me?" with these single-select options:
- **Thorough** — We build the canvas together, one block at a time.
- **Balanced** — I guide you through sections, group related questions, use reasonable defaults. _(Recommended)_
- **Fast** — I draft a complete canvas from your idea; you course-correct it.

## Output

Create `.shaper/business_state.md` in the project root.

```markdown
## Business State Initialized

- **Idea**: [Summary of the idea]
- **Working preference**: [Thorough / Balanced / Fast]

### Suggested Next Step

→ `/shaper:canvas` — Let's map out the 9 blocks of your Lean Canvas to see if this is viable.
→ `/shaper:help` — See all available commands.
```

## State Update

- Create `.shaper/business_state.md` using the schema defined in `../../references/shared-protocols.md` (Section 7). Populate Profile fields (`idea`, `working_preference`, `initialized_date`). Leave all other sections empty.
- Append to Session Log: today's date, "shaper:kickoff", "Initialized with idea: [one-line summary]".
