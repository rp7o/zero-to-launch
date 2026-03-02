---
name: shaper:status
description: Show current business state, canvas progress, research status, validation decision, and recommended next steps. Read-only — does not modify any files.
---

# status — Business Dashboard

Renders a dashboard of the current business state from `.shaper/business_state.md`. Read-only.

Before running this command, read `../../references/shared-protocols.md`.

## When to Run

- User says `shaper:status`.
- User asks "where are we?", "what's the current state?", "what have we done so far?", or returns after time away.
- When `shaper:help` recommends checking status for details.

## Workflow

### Step 1: Read State

Read `.shaper/business_state.md`. If it does not exist, output:
```
No business state found. Run `/shaper:kickoff` to get started.
```
and stop.

### Step 2: Render Dashboard

Output the following dashboard, populated from state file fields:

```markdown
## Business Status: [idea from Profile]

_Working preference: [working_preference] | Started: [initialized_date]_

---

### Canvas Progress

| Block | Status |
|---|---|
| Problem | [Drafted / Not started] |
| Customer Segments | [Drafted / Not started] |
| Unique Value Proposition | [Drafted / Not started] |
| Solution | [Drafted / Not started] |
| Channels | [Drafted / Not started] |
| Revenue Streams | [Drafted / Not started] |
| Cost Structure | [Drafted / Not started] |
| Key Metrics | [Drafted / Not started] |
| Unfair Advantage | [Drafted / Not started] |

---

### Research

- **Primary research**: [Yes / Informal / None]
- **Competitors analyzed**: [count from competitors_analyzed, or "Not done"]
- **Market summary**: [First sentence of market_summary, or "Not done"]

---

### Validation

- **Status**: [shaper_recommendation, or "Not run"]
- **User decision**: [user_decision, or "—"]
- **Decision date**: [decision_date, or "—"]
- **Open risks**: [open_risks count, or "—"]

---

### Recent Decisions

[Last 3 rows from Decision Log table, or "No decisions recorded yet"]

---

### Last Activity

[Last 3 rows from Session Log table, or "No sessions recorded yet"]

---

### Suggested Next Steps

[Derived from current state — see logic below]
```

### Step 3: Derive Suggested Next Steps

Apply this logic to determine the recommendation section:

**No canvas blocks filled:**
```
→ /shaper:canvas — Map out the 9 blocks of your Lean Canvas.
```

**Canvas drafted, no market research:**
```
→ /shaper:market — Canvas is ready. Analyze competitors and market size.
→ /shaper:validate — Or skip to stress-testing the business model.
```

**Canvas + market research done, not validated:**
```
→ /shaper:validate — All inputs are ready. Make the Go/No-Go call.
```

**Validated — Go decision:**
```
→ /shaper:pitch — Business validated. Generate a pitch or landing page copy.
→ /maker:discover — Ready to build? Hand off the product brief.
```

**Validated — Pivot or No-Go:**
```
→ /shaper:canvas — Rework the canvas based on validation findings.
→ /shaper:kickoff — Or start fresh with a new idea.
```

## State Update

None. Status is read-only and does not modify any files.
