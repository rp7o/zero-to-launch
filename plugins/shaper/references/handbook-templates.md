# Handbook Templates

Canonical output templates for all Shaper-written files. Use these exact structures when writing to `handbook/`.

---

## 1. `handbook/business/lean-canvas.md`

```markdown
# Lean Canvas — [Idea Name]

_Last updated: [YYYY-MM-DD]_

## Problem
[1-3 problems your target user faces]

## Customer Segments
[Who has the problem — define your ideal, early-adopter target audience]

## Unique Value Proposition
[A clear message explaining why you are different and worth buying]

## Solution
[1-3 simplest features or services that solve the problem]

## Channels
[How you will reach your customers]

## Revenue Streams
[How you will make money]

## Cost Structure
[Major costs to operate: development, marketing, hosting, etc.]

## Key Metrics
[The activities that matter most — e.g., DAU, conversion rate, churn]

## Unfair Advantage
[A barrier to entry — e.g., proprietary data, exclusive partnerships. "None identified" if not applicable]
```

---

## 2. `handbook/business/competitors.md`

```markdown
# Competitive Analysis — [Idea Name]

_Last updated: [YYYY-MM-DD]_

## Competitor Overview

| Name | URL | Core Value Proposition | Pricing | Key Weakness |
|---|---|---|---|---|
| [Name] | [URL] | [Their core VP] | [Pricing model] | [Key weakness vs our solution] |

## Market Summary

[One paragraph summarizing the competitive landscape: how crowded, where the gaps are, and how our solution is positioned relative to the competition.]

## Primary Research Note

[If primary research was collected, summarize the key connection between what real users said and what the competitive analysis shows. If no primary research: "This analysis is based entirely on secondary research."]
```

---

## 3. `handbook/business/primary-research.md`

This file holds both primary research (user interviews, surveys) and secondary problem-space research (web forums, reviews, complaints). Both types validate the Problem block — the distinction is captured by section heading and confidence level, not by file.

```markdown
# Primary Research — [Idea Name]

_Last updated: [YYYY-MM-DD]_

## Research Entry

- **Source**: [Who was interviewed / survey platform / usability test]
- **Date**: [YYYY-MM-DD or approximate]
- **Method**: [Interview / Survey / Usability Test / Sales call / Other]

## Raw Findings

[Direct quotes, data points, or verbatim summary provided by the user]

## Key Insights

1. [Most important insight]
2. [Second insight]
3. [Third insight]

## Confidence Level

[High — direct user evidence / Medium — informal conversations / Low — inferred from limited data]

## Implications for Canvas

[How this research affects or validates specific canvas blocks — e.g., "Confirms Problem block", "Challenges UVP assumption", "Reveals a new Customer Segment"]

---

## Secondary Research — Problem Space

_Source: Web secondary research (forums, Reddit, reviews, job postings). Confidence: [user-provided / agent-researched]._

_Last updated: [YYYY-MM-DD]_

### Top Pain Themes

1. **[Theme 1]** — [Summary of what people say. Representative quote and source URL.]
   - Frequency: [Common / Occasional / Rare]
2. **[Theme 2]** — [Summary. Quote and source.]
   - Frequency: [Common / Occasional / Rare]
3. **[Theme 3]** — [Summary. Quote and source.]
   - Frequency: [Common / Occasional / Rare]
4. **[Theme 4 — optional]**
5. **[Theme 5 — optional]**

### Alignment with Canvas Problem Block

[Which pain themes confirm the Problem block? Which challenge it? What, if anything, is missing from the current Problem statement?]
```

---

## 4. Pitch Output Files

Pitch content is written to separate files per format. Do not mix formats in a single file.

### `handbook/business/pitch-elevator.md`

```markdown
# Elevator Pitch — [Idea Name]

_Last updated: [YYYY-MM-DD]_

## 30-Second Spoken Pitch

[The elevator pitch — written to be spoken aloud in ~30 seconds. Clear, direct, hooks the listener immediately.]

## Key Hook

[The single most compelling sentence — the one line to remember]
```

### `handbook/business/pitch-landing-page.md`

```markdown
# Landing Page Copy — [Idea Name]

_Last updated: [YYYY-MM-DD]_

## Hero Section

**Headline**: [Main headline — short, punchy, audience-specific]

**Sub-headline**: [Supporting line — explains what it does and who it's for]

**CTA**: [Primary call to action — e.g., "Get early access", "Start free"]

## Feature Highlights

1. **[Feature 1 Name]** — [One-sentence benefit]
2. **[Feature 2 Name]** — [One-sentence benefit]
3. **[Feature 3 Name]** — [One-sentence benefit]
```

### `handbook/business/pitch-deck.md`

```markdown
# Pitch Deck Outline — [Idea Name]

_Last updated: [YYYY-MM-DD]_

## Slide Structure

1. **Title** — [Company name, tagline, presenter]
2. **Problem** — [The pain point, made vivid. 1-3 bullets.]
3. **Solution** — [What you built. Screenshot or visual description.]
4. **Why Now** — [Market timing, enabling technology, or regulatory change]
5. **Market Size** — [TAM → SAM → SOM with sources if available]
6. **Business Model** — [How you make money. Keep it simple.]
7. **Traction** — [Any early signals: signups, interviews, pilots, revenue]
8. **Competition** — [2x2 or table showing where you win]
9. **Team** — [Who you are and why you're the right team for this]
10. **Ask** — [What you need: funding, partners, pilots. Be specific.]
```

---

## 5. `handbook/product/brief.md`

This is the Maker handoff artifact. Its structure must match what `maker:discover` expects to read.

```markdown
# Product Brief — [Idea Name]

_Written by Shaper on [YYYY-MM-DD]. Read by Maker._

## Audience

[Who is this product for — specific, not generic. The early adopter profile.]

## Problem

[The core problem being solved. 2-3 sentences max.]

## Value Proposition

[Why this solution is meaningfully better than the alternatives. What makes it worth building.]

## Key Metrics

[The 2-3 metrics that define success for v1 — e.g., "Weekly active users > 100", "Conversion rate > 5%"]

## Known Risks

_This section is only included if there are open risks from the validation step._

[List of risks the team acknowledged but has not resolved. These are not blockers — they are known variables.]

- [Risk 1]
- [Risk 2]
```
