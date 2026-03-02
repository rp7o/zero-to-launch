---
name: shaper:pitch
description: Translate the finalized Lean Canvas into marketing copy, pitch decks, or elevator pitches. Use after the idea is validated.
---

# pitch — Selling the Vision

Generates human-readable marketing artifacts directly from the Lean Canvas.

Before running this command, read `../../references/shared-protocols.md` and `../../references/handbook-templates.md`.

## When to Run

- After `shaper:validate` confirms a "Go" decision.
- Whenever you need to explain the business to someone else.

## Workflow

### Step 1: Read the Canvas

Read `handbook/business/lean-canvas.md`. If missing, warn the user they need to run `shaper:canvas` first.

### Step 2: Choose the Format

Use the `AskUserQuestion` tool to ask: "What kind of pitch do you need?" with these single-select options:
- **Elevator Pitch** — 30-second spoken pitch.
- **Landing Page Copy** — Hero headline, sub-headline, CTA, and 3 features.
- **Pitch Deck Outline** — 10-slide structure for investors.

### Step 3: Check for Existing Output

Before generating, check whether the target output file already exists:

| Format | Output file |
|---|---|
| Elevator Pitch | `handbook/business/pitch-elevator.md` |
| Landing Page Copy | `handbook/business/pitch-landing-page.md` |
| Pitch Deck Outline | `handbook/business/pitch-deck.md` |

If the file exists, ask before overwriting:
"A [format] pitch already exists at `[path]`. Do you want to overwrite it or keep both?"
- **Overwrite** — Replace the existing file.
- **Keep both** — Append `-v2` to the filename (e.g., `pitch-elevator-v2.md`).

### Step 4: Generate the Copy

Generate the requested format directly, using the canvas. Apply these rules:
- Focus on the **Audience**, **Problem**, and **UVP** blocks.
- Be concise: never use 10 words if 5 will do.
- Hook the reader: start with the core problem or tension the audience faces.
- Be specific: no buzzwords ("synergy", "disrupt", "revolutionary") — state exactly what the product does.
- Speak to the audience: use language that resonates with the specific Customer Segment.
- **Do not invent features.** Stick strictly to the Solution block.

**Elevator Pitch:**
- Under 60 words, spoken format.
- Structure: "For [Audience] who struggle with [Problem], [Product] is a [Solution] that delivers [Value Prop]."

**Landing Page Copy:**
- Hero Headline (H1): the biggest promise or hook.
- Sub-headline (H2): how it works in one sentence.
- Primary Call to Action button text.
- 3 specific features that solve the core problems.

**Pitch Deck Outline:**
- Slide-by-slide outline: Title, Problem, Solution, Market Size, Business Model, Team.
- Each slide: title + core message only. No full script.

### Step 5: Present and Refine

Return the drafted copy to the user. Ask:
"How does this sound? Any tone adjustments before I save it?"

## Output

Write the finalized pitch to the format-specific file using the template in `../../references/handbook-templates.md` (Section 4):
- Elevator Pitch → `handbook/business/pitch-elevator.md`
- Landing Page Copy → `handbook/business/pitch-landing-page.md`
- Pitch Deck Outline → `handbook/business/pitch-deck.md`

```markdown
## Pitch Generated

Saved your [Format] to `handbook/business/pitch-[format].md`.

### Suggested Next Step

→ `/maker:discover` — If you haven't already, let's start actually designing the software for this business.
→ `/shaper:pitch` — Generate another format (elevator pitch, landing page, or pitch deck).
→ `/shaper:help` — See all available commands.
```

## State Update

- Update `.shaper/business_state.md` Profile section: set `pitch_generated: [format]` and `pitch_file: [output path]`.
- **Session Log**: Append today's date, "shaper:pitch", "[Format] pitch generated → `[output file]`".
