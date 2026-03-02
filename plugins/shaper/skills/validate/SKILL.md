---
name: shaper:validate
description: Review the Lean Canvas for glaring holes in the business model. Use before sending the project to engineering (maker).
---

# validate — The Go / No-Go Gate

Reviews the 9-block Lean Canvas for logical inconsistencies. This is the Go/No-Go gate before the software is architected.

Before running this command, read `../../references/shared-protocols.md` and `../../references/handbook-templates.md`.

## When to Run

- After `shaper:canvas` and `shaper:market` (natural flow).
- User explicitly says `shaper:validate`.

## Re-run Behavior

If a validation decision already exists in `.shaper/business_state.md`:
1. Show the prior result: "You previously validated this canvas on [decision_date] with a [shaper_recommendation] recommendation. User decision: [user_decision]. Open risks: [open_risks]."
2. Ask: "What changed since then? (e.g., updated canvas blocks, new market research, team changes)"
3. If the canvas changed: re-run the full workflow, noting what has changed since the original validation.
4. If nothing changed: confirm before re-running. "The canvas hasn't changed since validation. Re-run anyway?"
5. After re-run: update the Validation section and append to Decision Log and Session Log.

## Workflow

### Step 1: Read the Canvas and Research

Read `handbook/business/lean-canvas.md` and `.shaper/business_state.md`. If missing, suggest running `shaper:canvas`.

Also read:
- `handbook/business/competitors.md` (if it exists — market research).
- `handbook/business/primary-research.md` (if it exists — interview data, survey results).
- Check `primary_research` field in `.shaper/business_state.md` for status (`yes`, `informal`, or `none`).

### Step 2: Team Right-to-Play Assessment

Before running the critic, understand who is building this. The team's background directly affects whether the business model is executable.

Ask the user: "Before I stress-test the model, I need to understand the team. Tell me about the people who will build and run this business."

Then use the `AskUserQuestion` tool to gather specifics. Ask: "Which of these capabilities does your team currently have?" with these multi-select options:
- **Domain expertise** — Deep knowledge of the industry or problem space.
- **Technical skills** — Can build the product (engineering, design, data).
- **Sales & distribution** — Access to customers, channels, or partnerships.
- **Regulatory / compliance** — Understands legal requirements for this market.

Follow up with: "Is there anything else about the team's background that gives you an edge — prior startup experience, industry connections, unique access to the audience?"

Record the team profile in `.shaper/business_state.md` under the `capabilities` and `additional_context` fields of the Team Profile section.

> **Why this matters:** A great canvas with the wrong team is still a bad bet. If the model requires enterprise sales but the team is all engineers, the Channels and Revenue blocks are at risk. The critic needs to know this.

### Step 3: Spawn the Canvas Critic

Tell the user: "I am deploying a Canvas Critic to review your business model. This will take a moment."

Spawn a Canvas Critic subagent. Include all available evidence in the prompt — the critic should know what's backed by real data vs. assumptions, and whether the team can execute.

**Prompt to subagent:**
"You are a harsh but fair startup advisor. Review this Lean Canvas: [Canvas contents].

Evidence available:
- Market research: [Yes/No — include competitor summary if available]
- Primary research: [Yes/Informal/None — include key findings if available]

Team profile:
- Capabilities: [Value of `capabilities` field from Team Profile in business_state.md]
- Additional context: [Value of `additional_context` field from Team Profile in business_state.md]

Find 1-3 glaring holes. Look specifically at:
1. Misaligned Cost/Revenue (e.g., $5 MRR but relying on expensive enterprise sales channels).
2. Weak Unfair Advantage (e.g., 'first mover').
3. Vague Target Audience.
4. **Assumption risk** — Which canvas blocks are backed by real user evidence vs. founder assumptions? Flag any critical block (Problem, Audience, UVP) that has zero primary research behind it.
5. **Right to Play** — Does the team have the capabilities to execute this specific business model? Flag any critical gap between what the model requires and what the team can deliver (e.g., model needs enterprise sales but team is all engineers; model needs regulatory expertise but nobody has it).

Return a structured critique with actionable suggestions."

Wait for the subagent to return.

### Step 4: Present Critique and Challenge Holes

Present the critique to the user.

**For each glaring hole the critic found**, ask the user to respond. Don't let holes pass unchallenged:
- "The critic flagged [hole]. Do you have a response to this — evidence, a plan, or a reason this isn't a real risk?"

Wait for the user to address each hole. They can:
- **Explain it away** — provide evidence or reasoning that resolves the concern. Accept it and note it.
- **Acknowledge it** — agree it's a risk but accept it. Note it as an open risk.
- **Fix it** — update the canvas. Update `.shaper/business_state.md`.

### Step 5: Give a Recommendation

After the user has responded to all holes, synthesize everything using this rubric:

**Score each dimension 0-2:**
| Dimension | 0 | 1 | 2 |
|---|---|---|---|
| Canvas quality | Multiple structural holes | 1 hole, partially addressed | Canvas is solid |
| Evidence quality | No primary research, assumptions only | Informal conversations | Strong primary research |
| Team fit | Critical capability gaps | Minor gaps | Team matches model demands |
| Hole resolution | Multiple open risks | Some acknowledged, some resolved | All holes addressed or explained |

**Total score → recommendation:**
- **7-8 → Go**
- **5-6 → Go with reservations**
- **3-4 → Pivot**
- **0-2 → No-Go**

> The rubric informs judgment but does not replace it — a score of 0 in any single dimension can override a high total. Use the rubric as a guide, then give your honest read.

**Give a direct recommendation** — one of:

- **"I recommend Go."** — The model is sound, the evidence is there, the team can execute, and the holes have been addressed. Say why.
- **"I recommend Go with reservations."** — The model is viable but there are open risks. List them clearly. Say: "These are risks you're accepting, not risks you've resolved."
- **"I'd lean toward Pivot."** — The model has structural problems that need rework. Say what specifically needs to change.
- **"I'd lean toward No-Go."** — The fundamentals don't hold up. Say why bluntly.

> **The final decision always belongs to the user.** But the Shaper must give an honest opinion, not just present options. If the user overrides the recommendation, that's their right — but the reservations get recorded.

Then use the `AskUserQuestion` tool to ask: "Here's my recommendation: [recommendation + reasoning]. What's your call?" with these single-select options:
- **Go** — Hand off to engineering.
- **Pivot** — Rework the model first.
- **No-Go** — Kill the project.

**If the user chooses Go against a "Pivot" or "No-Go" recommendation:** Note this explicitly. Say: "Your call. I'm recording the open risks so they're visible downstream." Record the override and the specific reservations in the state and the product brief.

### Step 6: Handoff

If **Go**:
Write the product brief to `handbook/product/brief.md` using the template in `../../references/handbook-templates.md` (Section 5). Include Audience, Problem, Value Proposition, and Key Metrics. If there are open risks or reservations (from unresolved critique holes or an overridden recommendation), include the `## Known Risks` section.

Tell the user:
```markdown
## Go Decision — Brief Handed Off

The product brief has been written to `handbook/product/brief.md`.

### Suggested Next Step

→ `/maker:discover` or `/maker:architect` — Begin building the software.
→ `/shaper:pitch` — Generate a pitch or landing page copy before you build.
→ `/shaper:help` — See all available commands.
```

If **Pivot**:
Update `.shaper/business_state.md` with a note on what changed and why.

Tell the user:
```markdown
## Pivot — Back to the Canvas

The model needs rethinking. Let's revise before moving forward.

### Suggested Next Step

→ `/shaper:canvas` — Rework the blocks that need to change.
→ `/shaper:market` — Re-run market research with the new direction in mind.
→ `/shaper:help` — See all available commands.
```

If **No-Go**:
Update `.shaper/business_state.md` with decision status `no-go`.

Tell the user:
```markdown
## No-Go — Project Killed

The ROI isn't there. Good call — better to know now.

### What Next?

→ `/shaper:kickoff` — Start fresh with a new idea.
→ `/shaper:help` — See all available commands.
```

## Output

Update `.shaper/business_state.md` Validation section with:
- `shaper_recommendation`: the recommendation given.
- `user_decision`: the user's final choice.
- `decision_date`: today's date.
- `open_risks`: count of acknowledged-but-unresolved risks.
- `override_note`: if the user overrode the recommendation, the specific reservations.

Update Team Profile section with `capabilities` and `additional_context` from Step 2.

**Decision Log**: Append a row to the Decision Log table:
`| [date] | [Go/Pivot/No-Go] — [one-line rationale] | [Why the user made this call] | Active |`

**Session Log**: Append to Session Log: today's date, "shaper:validate", "[recommendation] → user chose [decision], [N] open risks".

Write to `handbook/product/brief.md` for the cross-plugin handoff on a Go decision (template in `../../references/handbook-templates.md`).
