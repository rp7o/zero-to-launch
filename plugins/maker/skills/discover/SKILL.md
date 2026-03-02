---
name: maker:discover
description: Interactive product discovery — what are we building, who is it for, and what does v1 look like. Use when starting product exploration, adding a new feature, revisiting scope or requirements, or when the user says "discover".
---

# discover — Interactive Product Discovery

Deep, interactive exploration of what the project is. Produces the product context that informs all technical decisions. This is where "what and why" gets figured out — not "how."

Before running this command, read `../../references/shared-protocols.md` for shared protocols.

## When to Run

- After kickoff, before maker:design (natural flow).
- When adding a new feature to an existing project.
- When revisiting scope or requirements.
- Anytime the user says `maker:discover`.

## Re-run Behavior

If product context already exists in `.maker/project_state.md`:
1. Read it. Show a summary: "We are building [Product Name] for [Audience]. What new problem or Job to be Done are we exploring today?"
2. Run through the JTBD, Pains/Gains, and Scope questions specifically for the **new feature area**.
3. Capture deltas. Append the new JTBD and Scope to the existing `handbook/product/brief.md` under a new section. Add to Iteration History.

## Workflow

### Phase 1: Understand the Problem

> [!IMPORTANT]
> **Check for Shaper Handoff:** Before starting Phase 1, check if `handbook/product/brief.md` exists. If it does, the `shaper` plugin has already validated the business model. 
> 
> *Do not* ask Q1, Q2, Q3, or Q4. Instead, read the brief, summarize it to the user, and say: "I see the Shaper plugin has already defined the Audience, Problem, and Value Proposition. Let's move straight to scoping v1." 
> 
> Then, skip directly to **Q5 — Target Problem Scope (v1)**.

Ask these one at a time. Branch based on responses. Skip what's already known from kickoff or the Shaper brief.

**Q1 — Target Audience & Market Segment**
"Who is this for? What specific market segment or user persona are we targeting?"

Wait. Based on response:
- If clear → proceed to Q2.
- If vague → follow up: "Can you describe a specific person or business that would pay for or use this?"

**Q2 — Problem & Jobs to be Done (JTBD)**
"What is their core Job to be Done (JTBD)? Please describe the problem *without* mentioning our solution. (Format: When [situation], the user wants to [motivation], so they can [expected outcome])"

Wait. Outline the JTBD.

**Q3 — Value Proposition (Pains & Gains)**
"What are their biggest pains (workarounds or frustrations) today? And what is the core value proposition we are offering to relieve those pains or create unexpected gains?"

Wait. Document the pains and intended value proposition.

**Q4 — Success Metrics**
"How will we measure the success of this product/feature? (e.g., specific business metrics, user adoption rates, time saved)"

Wait. Document the success metrics.

**Q5 — Target Problem Scope (v1)**
"Which of these pains, gains, and jobs absolutely must be addressed in v1? Which problems, user segments, or edge cases are we explicitly deferring?"

Wait. Note the focus areas and the problem exclusions. Do not force artificial cuts if they aren't ready.

**Q6 — Constraints**
"Any hard constraints I should know? Platform requirements, offline support, auth needs, specific integrations, accessibility requirements?"

Wait. If none: move on.

### Phase 2: Identify Deferrals

Don't ask for these — extract them from the conversation.

Review everything discussed. Identify:
1. Problems, segments, or gains the user explicitly said "later" or "not in v1."
2. Assumptions you made due to missing information.
3. Questions that came up but weren't answered.

State these clearly: "Based on our conversation, here are the problem deferrals: [list]. Any of these feel wrong to defer?"

Wait for confirmation.

### Phase 3: The Go / No-Go Gate

This is the formal product validation checkpoint. Present a summarized Lean Canvas using the information gathered so far (Audience, Problem/JTBD, Value Proposition, Success Metrics).

Use the `AskUserQuestion` tool to ask: "Based on the Value Proposition and Success Metrics we've outlined, does this project justify the investment to build?" with these single-select options:
- **Go** — Proceed to solution design.
- **Pivot** — The problem/market isn't quite right. Let's adjust the audience or JTBD.
- **No-Go** — The ROI isn't there, or the market is too crowded. Let's halt the project.
- **Research** — I need more data before deciding. Let's run `/maker:research`.

Wait for the decision.

## Output

Depending on the decision in Phase 3:

### If Pivot:
Loop back to Phase 1, starting from the question that needs to change (e.g., Target Audience or JTBD).

### If Research:
State: "Okay, let's pause here. Run `/maker:research [topic]` to validate the market or competitors. When you're ready, run `/maker:discover` again and we'll pick up where we left off."
Save the current draft to `.maker/project_state.md` so it isn't lost.

### If No-Go:
1. Update `.maker/project_state.md`: Set `Status: Abandoned`.
2. Write a brief to `handbook/product/brief.md` documenting the hypothesis and *exactly why* it was abandoned (e.g., "Market too crowded," "ROI too low"). This preserves the learning.
3. Stop.

### If Go:
Write to `handbook/product/`:

### handbook/product/brief.md
- What this project is (one paragraph).
- Target Audience and Market Segment.
- Jobs to be Done (JTBD) for that audience.
- Pains (current frustrations) and Value Proposition.
- Success Metrics.
- Target Problem Scope for v1 (which pains/gains we are addressing).
- Deferrals (problems or segments we are explicitly ignoring).
- Hard constraints.

### handbook/product/deferred.md
Each entry (Problem Space):
```markdown
## [Topic]
**Status**: Deferred / Assumption
**Detail**: What was deferred or assumed.
**Revisit when**: Trigger for revisiting.
```

### handbook/README.md
Create the handbook skeleton with routing table. Architecture, design, engineering, and workflow sections marked as "added by architect."

Present summary:

```markdown
## Product Context Complete (GO DECISION)

**What**: [one sentence]
**Audience**: [market segment/persona]
**JTBD**: [summary of JTBD]
**Success Metrics**: [summary of metrics]
**v1 Focus**: [brief summary of prioritized pains/gains]
**Problem Deferrals**: [count] items

### Suggested Next Step

→ `/maker:design` — Design the solution based on this problem definition.
→ `/maker:research [topic]` — If there's a technology question or competitive landscape to investigate first.
```

## State Update

- Update `.maker/project_state.md > Product Context` with brief and scope.
- Add deferrals to `.maker/project_state.md > Deferred Decisions`.
- Add to Session Log.

## Interaction Calibration

Respect the working preference from kickoff:
- **Thorough**: Full one-question-at-a-time flow in discover. Detailed confirmations.
- **Balanced**: Grouped questions (2-3 at a time) in discover. Confirm before code only.
- **Fast**: Ask one combined question: "Tell me what you're building, who it's for, and what v1 looks like. I'll fill in the gaps." Then present assumptions and ask for corrections.
