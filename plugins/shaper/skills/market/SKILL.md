---
name: shaper:market
description: Full-spectrum market research across problem space, competitive landscape, and market sizing. Use when validating a business idea against real evidence.
---

# market — Full-Spectrum Market Research

Runs up to three independent research modules — problem-space, competitive landscape, and market sizing — each with a user-agency choice at entry. Primary research is collected first, then the user selects which modules to run.

Before running this command, read `../../references/shared-protocols.md` and `../../references/handbook-templates.md`.

## When to Run

- After `shaper:canvas` to validate the Problem, Solution, and Revenue Streams blocks.
- User explicitly says `shaper:market` or asks about competitors, market size, or problem validation.

---

## Workflow

### Step 1: Read the Canvas

Read `.shaper/business_state.md`. If no canvas exists, ask: "I need to know what we are building first. Should we run `/shaper:kickoff` or `/shaper:canvas`?"

Check the Session Log. If `shaper:market` has already run, apply the **Cascade Awareness** protocol from `shared-protocols.md`: note what changed and warn if downstream artifacts may be stale.

### Step 2: Ask for Primary Research

Before doing any secondary research, ask the user if they have done primary research.

Use the `AskUserQuestion` tool to ask: "Have you done any primary research — user interviews, surveys, usability tests, sales calls, or anything where you talked directly to potential customers?" with these single-select options:
- **Yes, I have materials** — I can share transcripts, notes, survey results, or recordings.
- **Yes, but nothing written down** — I did some informal conversations and can summarize.
- **No** — We'll rely on secondary research for now.

If **Yes, I have materials**: Ask the user to share them (paste text, provide file paths, or describe the key findings). Read and incorporate the primary data.

If **Yes, but nothing written down**: Ask: "Give me the 3 most important things you learned from those conversations." Capture their summary.

If **No**: Proceed. Note in the state that no primary research was done — this becomes a flag for `shaper:validate`.

> **Primary research always takes precedence over secondary research.** When real user quotes, interview data, or survey results conflict with assumptions from web research, the primary data wins.

### Step 3: Select Research Modules

Use `AskUserQuestion` (multi-select) to ask: "What market research do you want to run?" with these options:

- **Problem space** — Find what real people say about this problem online: forums, Reddit, reviews, complaints.
- **Competitive landscape** — Identify who already solves this, how they're priced, and where they're weak.
- **Market sizing** — Estimate TAM/SAM/SOM for the opportunity.

Run each selected module in sequence (A → B → C). Unselected modules are skipped; record them as `none` in state.

---

## Module A: Problem-Space Research

_Runs only if "Problem space" was selected in Step 3._

Use `AskUserQuestion` (single-select) to ask: "How do you want to handle problem-space research?" with options:

- **I have data on this** — I'll share what I've found: reports, posts, screenshots, or summaries.
- **Research it for me** — Search for what people say about this problem online. _(Recommended if no primary research)_
- **Skip** — I'm confident in the problem hypothesis.

**If "I have data on this":**
Ask the user to paste or describe their findings. Structure the input into the top 3 pain points. Record with `confidence: user-provided`. Write output to `handbook/business/primary-research.md` under `## Secondary Research — Problem Space` using the template in Section 3 of `handbook-templates.md`.

**If "Research it for me":**
Tell the user: "I am deploying a Market Researcher to find what real people say about [Problem]. This will take a moment."

Spawn a Market Researcher subagent with this **problem-focused** prompt:
> "You are a Market Researcher. Search for discussions, complaints, reviews, and forum posts where people express frustration with [Problem] in the context of [Target Audience]. Sources to check: Reddit threads, forum posts, App Store/G2 reviews, Twitter/X complaints, job postings (as pain signals), and support ticket themes.
>
> Do NOT look for competitors or products — look for the raw pain expressed by real people.
>
> Return:
> 1. Top 5 pain themes with representative quotes and source URLs.
> 2. Frequency signals for each theme: is this a common complaint or a rare edge case?
> 3. Assessment: which pain themes align with the canvas Problem block, and which challenge it?
>
> Do not invent data. If you cannot find meaningful signal for a theme, say so."

Wait for the subagent to return. Present the top pain themes to the user.

Write output to `handbook/business/primary-research.md` under `## Secondary Research — Problem Space`.

**If "Skip":**
Record `problem_research_secondary: none` in state. Proceed to the next module.

**State update:** Set `problem_research_secondary: yes` (if run) or `none` (if skipped).

---

## Module B: Competitive Landscape

_Runs only if "Competitive landscape" was selected in Step 3._

Use `AskUserQuestion` (single-select) to ask: "How do you want to handle competitive research?" with options:

- **I know the landscape — let me describe it** — I'll walk you through each competitor.
- **Research it for me** — Deploy a Market Researcher to find and analyze competitors. _(Recommended)_
- **Skip** — Not needed right now.

**If "I know the landscape — let me describe it":**
For each competitor, ask in sequence:
1. "Name and URL?"
2. "Core value proposition in one sentence?"
3. "Pricing model?"
4. "Their biggest weakness vs. your solution?"

After each entry, ask: "Add another competitor, or done?" Repeat until the user is done.

Assemble findings into the competitor table format from Section 2 of `handbook-templates.md`. Record with `confidence: founder-provided`.

**If "Research it for me":**
Tell the user: "I am deploying a Market Researcher to find existing solutions for [Problem] targeting [Audience]. This will take a moment."

Spawn a Market Researcher subagent with this **competitor-focused** prompt:
> "You are a Market Researcher. We are building a product to solve [Problem] for [Target Audience]. Our proposed solution is [Solution]. Find 3-5 direct or indirect competitors. For each, return:
> 1. Name and URL
> 2. Their core Value Proposition
> 3. Their Pricing Model
> 4. Their key weakness or feature gap compared to our proposed solution.
>
> Focus strictly on the competitive landscape. Do not invent a business model."

Wait for the subagent to return.

**If "Skip":**
Record `competitors_analyzed: 0` in state. Proceed to the next module.

**Output:** Write to `handbook/business/competitors.md` using the template in Section 2 of `handbook-templates.md`.

**State update:** Set `competitors_analyzed` to the count found (or 0 if skipped).

---

## Module C: Market Sizing

_Runs only if "Market sizing" was selected in Step 3._

Use `AskUserQuestion` (single-select) to ask: "How do you want to handle market sizing?" with options:

- **I have existing data** — I'll share a report, deck, or estimates I've already worked out.
- **Walk me through it** — Ask me questions and I'll work out the numbers with you.
- **Research it for me** — Deploy a Market Researcher for a TAM/SAM/SOM estimate.
- **Skip for now** — Not needed at this stage.

**If "I have existing data":**
Ask the user to share the data (paste figures, link a report, or summarize). Record TAM/SAM/SOM as stated by the user with `confidence: external estimate (user-provided)`.

**If "Walk me through it":**
Ask these four questions in sequence:

1. "Who is your primary customer — individual consumer, SMB, or enterprise?"
2. "How many of these customers exist in your target market? (Order of magnitude is fine.)"
3. "What does a customer currently spend annually to address this problem — on tools, time, services, or workarounds?"
4. "In years 1-3, what % of that market could you realistically capture given your team and distribution?"

Compute:
- **TAM** = (answer 2) × (answer 3)
- **SAM** = TAM filtered to the specific segment and geography the user described
- **SOM** = SAM × (answer 4 / 100)

Present results with all raw assumptions visible so they are auditable. Label as `confidence: Founder estimate — treat as directional`.

**If "Research it for me":**
Tell the user: "I am deploying a Market Researcher focused on market sizing. This is a rough order-of-magnitude estimate only."

Spawn a Market Researcher subagent with this **sizing-focused** prompt:
> "You are a Market Researcher. Estimate the TAM (Total Addressable Market), SAM (Serviceable Addressable Market), and SOM (Serviceable Obtainable Market) for a product solving [Problem] for [Target Audience].
>
> Use publicly available data — industry reports, analyst estimates, or census/demographic data. Prioritize named sources over extrapolation.
>
> Return:
> - TAM: [estimate + source/reasoning]
> - SAM: [estimate + source/reasoning]
> - SOM (Year 1-3 realistic capture): [estimate + reasoning]
> - Confidence: [High / Medium / Low] with a one-sentence explanation of why.
>
> Important: These are rough order-of-magnitude estimates only. Do not present them as precise figures. Flag any estimate with low confidence prominently."

Wait for the subagent to return. Present the sizing to the user.

> **Confidence caveat**: Always surface the confidence level. Low-confidence estimates should be treated as directional, not definitive.

**If "Skip for now":**
Record `market_sizing: none` in state.

**Output:** Append to `handbook/business/competitors.md` under a `## Market Size Estimate` section.

---

## Step 4: Synthesis

After all selected modules complete, synthesize across all available evidence:

- **Primary research** (Step 2 — user interviews, if provided)
- **Problem-space research** (Module A — if run)
- **Competitive findings** (Module B — if run)
- **Market sizing** (Module C — if run)

Present a combined view:

1. **What the evidence confirms** — Where multiple sources agree.
2. **What the evidence challenges** — Where findings conflict with canvas assumptions.
3. **Open questions** — What remains unvalidated.

If primary research was collected, lead with it. If no primary research: add note:
> "This analysis is based entirely on secondary research. Real user interviews would strengthen the validation significantly."

Ask: "Based on all of this, do we need to adjust any canvas blocks?"

Wait for response. If yes, update `.shaper/business_state.md` and the canvas document.

---

## Output

Write the competitive analysis to `handbook/business/competitors.md` (Modules B and C).

Write problem-space research to `handbook/business/primary-research.md` under `## Secondary Research — Problem Space` (Module A).

If primary research materials were provided in Step 2, save them to `handbook/business/primary-research.md` using the template in Section 3 of `handbook-templates.md`.

```markdown
## Market Research Complete

Modules run: [list of modules that ran]

Outputs saved:
- `handbook/business/primary-research.md` — problem-space findings
- `handbook/business/competitors.md` — competitive analysis and market sizing

### Suggested Next Step

→ `/shaper:validate` — Let's review the Canvas and the Market Data to make a Go/No-Go decision.
→ `/shaper:help` — See all available commands.
```

---

## State Update

- Update `.shaper/business_state.md` if the UVP or Solution changed.
- Record primary research status: `primary_research: yes | informal | none`.
- Record `problem_research_secondary: yes | none`.
- Update `competitors_analyzed` count and `market_summary` in the Research section.
- Record `market_sizing: yes | founder-estimate | external | none`.
- **Session Log**: Append today's date, "shaper:market", "[modules run], primary research: [yes/informal/none]".
