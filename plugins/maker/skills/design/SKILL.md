---
name: maker:design
description: Design the solution interface (UX/UI, API contract, or agent system). Use when transitioning from the product brief to the engineering phase to determine how the product behaves and looks.
---

# design — Define the Solution Interface

Translates the product intent (from `maker:discover`) into a concrete solution design (wireframes, interfaces, or agent schemas) without making technical engineering decisions.

Before running this command, read `../../references/shared-protocols.md` for shared protocols.

## When to Run

- After `maker:discover`, before `maker:architect` (natural flow).
- When adding a new feature that needs a new screen or interface.
- User explicitly says `maker:design`.

## Re-run Behavior

If a design already exists in `handbook/design/`:
1. Read the existing context. "We currently have [summary of current design]. What do you want to change?"
2. Ask only about what needs to be added or modified.
3. Update `handbook/design/direction.md` and related assets to reflect changes.

## Workflow

### Step 1: Read Product Context

Read `.maker/project_state.md > Product Context` and `handbook/product/` if they exist. If missing, warn the user and suggest running `maker:discover` first.

### Step 2: Pitch the Shape

Based on the Jobs to be Done (JTBD) and constraint, propose 2-3 high-level shapes for the solution.

Use the `AskUserQuestion` tool to present the proposed shapes as single-select options. Example question: "To solve [Problem], which of these fits best?" with options like:
- Build a lightweight CLI tool.
- Build a full web dashboard.
- Build a webhook that runs automatically.

Include "Something else" as a fallback option so the user can describe their own shape.

Wait.

### Step 3: Design the Interface

Branch based on the selected shape. Use the `solution-designer` agent to help generate or explore the interface.

**If Frontend/Fullstack (Web App, Mobile, etc.):**
- Ask about visual directions: "Any vibe or references you like? (Figma, screenshots, style preferences)"
- Use the `solution-designer` agent to propose screen flows or layouts.
- If the user agrees, generate wireframes or design documentation and suggest placing them in `handbook/design/assets/`.

**If API/Backend-only:**
- Ask about the desired contract: "What should the inputs and outputs look like? REST, GraphQL, bulk endpoints?"
- Use the `solution-designer` agent to propose endpoint schemas and data shapes.

**If Agent/AI Tool:**
- Ask about the autonomy level: "Should this run totally autonomously, or interact with a human?"
- Use the `solution-designer` agent to propose the system prompt flow and required tool interfaces.

### Step 4: Determine Feature Scope (v1)

Once the interface is designed, compare it back to the JTBD, Pains, and Gains from the Product Brief.

Propose a v1 feature scope: "To solve the core problem, here is the absolute minimum feature set we should build into this design for v1. We should defer [list of features from the design] until later."

Ask: "Does this feature scope look right for v1? Are there any parts of the design we should cut for now or add back?"

Wait for user confirmation on the scope and design.

### Step 5: Record Design

Then, write to `handbook/product/plan.md`:
- Document the v1 feature scope (what must work).
- List explicitly out-of-scope features or later phases.

Append to `handbook/product/deferred.md`:
- Add the features, UI elements, or endpoints that were deferred from the ideal design. Label them as (Solution Space) to separate them from the Problem Space deferrals.

Write to `handbook/design/direction.md`:
- Document the shape.
- Include the UI feel/vibe, key screens, or API endpoints.
- Add references to any raw materials stored in `handbook/design/assets/`.

Present summary:

```markdown
## Design Complete

**Status**: Defined
**Shape**: [CLI / Dashboard / API / Agent]
**v1 Feature Scope**: [count] core features
**Deferred Features**: [count] design elements deferred

### Suggested Next Step

→ `/maker:architect` — Design the engineering structure and tech stack to support this design.
```

## State Update

- Update `.maker/project_state.md > Design Context` with the selected shape, key interface properties, and v1 feature scope.
- Add to Session Log.

## Interaction Calibration

Respect the working preference from kickoff:
- **Thorough**: Explore multiple solution directions before proposing shapes. Ask about visual references in detail.
- **Balanced**: Propose 2–3 shapes, ask the user to pick, then design the chosen one.
- **Fast**: Propose the most likely shape based on the JTBD. Ask "Does this direction work?" once and proceed.
