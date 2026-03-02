---
name: maker:research
description: Investigate a specific technology, API, library, framework, competitor, or market. Use when the user says "research [topic]", before making a stack decision, when comparing alternatives, or when validating assumptions about users or market. Accepts a topic as argument.
argument-hint: "[topic]"
---

# research — Investigate Technology, Products, or Markets

Targeted research on a specific topic. Returns findings and a recommendation. Can run anytime — before discover, before architect, during build, or when iterating.

Before running this command, read `../../references/shared-protocols.md` for shared protocols.

## Two Modes

Research auto-detects the mode from the topic:

| Mode | Signals | Examples |
|---|---|---|
| **Technical** | Library, API, framework, tool, pattern, integration | `maker:research React vs Preact`, `maker:research free quote APIs` |
| **Product Execution** | Competitor features, UX patterns, existing solutions, UI implementation | `maker:research competitor quote apps`, `maker:research how people use random generators` |

## When to Run

- User asks about a specific technology, API, library, or pattern → **Technical**
- Before making a stack decision in `maker:architect` → **Technical**
- When comparing alternatives (e.g., "research React vs Preact") → **Technical**
- User wants to understand the UX or feature landscape of existing solutions → **Product Execution**
- Before `maker:discover` or `maker:design` to inform feature decisions → **Product Execution**
- When validating assumptions about feature implementation → **Product Execution**

## Workflow

### Step 1: Clarify the Question

If the topic is clear, proceed. If ambiguous, ask ONE clarifying question:
"When you say [topic], are you asking about [interpretation A] or [interpretation B]?"

### Step 2: Choose a Path

Before delegating to an agent, ask the user how they want to proceed. Present options with `AskUserQuestion` (single-select).

**For Technical topics:**

Ask: "How do you want to approach this research?"

Options:
- **Research it for me** — Find and evaluate the top options _(Recommended if exploring)_
- **I have options to compare** — Name the candidates; I'll evaluate them specifically against the stack
- **I already know — record the decision** — Skip research; log my choice to the Decision Log now

**Path behaviours — Technical:**

- **Research it for me** → proceed to Step 3 as normal (delegate to Tech Scout).

- **I have options to compare** → ask: "Name the options you're comparing." Use the `tech-scout` agent, instructing it to evaluate *only* those specific options and not search for alternatives. Proceed to Step 4 with the returned findings.

- **I already know — record the decision** → ask: "Which option did you choose?" Then ask: "Brief rationale?" Write directly to `.maker/project_state.md > Decision Log`. Skip agent entirely. Confirm the log entry to the user and offer next steps.

---

**For Product / UX topics:**

Ask: "How do you want to approach this research?"

Options:
- **Research it for me** — Map the competitive landscape _(Recommended)_
- **I know the landscape — let me describe it** — Walk through key players interactively

**Path behaviours — Product:**

- **Research it for me** → proceed to Step 3 as normal (delegate to Market Scout).

- **I know the landscape — describe it** → for each competitor, ask in sequence:
  1. "Competitor name + URL (or 'done' to finish)?"
  2. "Core value prop?"
  3. "Key features?"
  4. "Implementation gaps or weaknesses?"
  After each competitor: "Add another competitor or done?"
  Assemble findings into the Product Execution Output Schema (see Step 4). Record with `source: founder-provided` in the Implications section. Proceed directly to Step 5.

---

### Step 3: Delegate to Specialist Agent

Based on the detected mode, delegate to the appropriate agent:

- **Technical topic** → Use the `tech-scout` agent. Give it the topic, the current stack, and any hard constraints. *Explicitly instruct it*: "Limit your search to the top 3-4 options and complete this research in a single fast pass without spawning sub-agents."
- **Product / Feature topic** → Use the `market-scout` agent. Give it the topic and a brief summary of what the project is. *Explicitly instruct it*: "Limit your search to the top 3-4 competitors and focus on how they execute their features and UX. Complete this research in a single fast pass without spawning sub-agents." *Note: If the user is asking about business models or pricing, tell them to use the `shaper` plugin instead.*

Both agents return structured findings using the output format below.

#### Technical Evaluation Criteria

For each option or finding, evaluate:

1. **Fitness** — Does it fit the project shape, stack, and constraints?
2. **Trade-offs** — What are the pros and cons?
3. **CORS / Auth / Pricing** — For APIs: Is it free? Does it need an API key? Is it CORS-friendly?
4. **Maturity** — Is it maintained? How large is the community? Breaking changes?
5. **Integration** — How well does it work with the chosen stack?

#### Product Execution Evaluation Criteria

For each competitor, solution, or pattern finding, evaluate:

1. **Core Functionality** — What primary jobs does this product solve?
2. **UX/UI Patterns** — How do they handle the interface?
3. **Key Features** — What are the standout features?
4. **Implementation Gaps** — What workflows feel clunky or missing?
5. **Technical Observations** — Speed, integrations, platforms.
6. **Key Takeaway** — What execution detail should we borrow or avoid?

### Step 4: Present Findings

#### Technical Output Schema

```markdown
## Research: [Topic]

### Question
[What we're investigating]

### Findings

#### [Option A]
- **What it is**: [one sentence]
- **Pros**: [bullets]
- **Cons**: [bullets]
- **Fitness for this project**: [High / Medium / Low + why]

#### [Option B]
[same structure]

### Recommendation
[Which option and why. State confidence: High / Medium / Low.]
[If confidence is low, say what additional information would raise it.]

### Next Step
→ `/maker:architect` — Record this decision in the architecture plan.
→ `/maker:research [related topic]` — If another question came up during research.
```

#### Product Execution Output Schema

```markdown
## Research: [Topic]

### Question
[What we're investigating]

### Landscape

#### [Competitor / Solution A]
- **Core Functionality**: [one sentence]
- **UX/UI Patterns**: [how they solve the interface]
- **Key Features**: [bullets]
- **Implementation Gaps**: [bullets]
- **Technical Observations**: [speed, integrations, etc.]
- **Key Takeaway**: [UX/feature finding to learn from]

#### [Competitor / Solution B]
[same structure]

### Implications for Our Project
[How these findings affect our product decisions. What to adopt, avoid, or differentiate on.]

### Next Step
→ `/maker:discover` — Incorporate these insights into product context.
→ `/maker:research [related topic]` — If another question came up during research.
```

### Step 5: Record

If the user accepts the recommendation or findings:

**Technical**: Add to `.maker/project_state.md > Decision Log`:
- Date
- Decision: "Use [X] for [purpose]"
- Rationale: summary
- Alternatives considered: [list]
- Revisable: Yes / No

**Product**: Update `handbook/product/` as appropriate:
- **CRITICAL**: Update `handbook/product/brief.md` (the Lean Canvas) to reflect new learnings about the Audience, Value Proposition, or Success Metrics.
- Note differentiation opportunities in `handbook/product/deferred.md` if not acting now.
- Add to `.maker/project_state.md > Decision Log` if a product decision was made

## Guidelines

- **Don't research what's already decided.** Check `.maker/project_state.md > Decision Log` first.
- **Be specific about fitness.** "Popular library" is not a recommendation. "Works with Bun, has Preact bindings, and the bundle size is 4KB" is.
- **Flag deal-breakers early.** If something won't work with the stack (CORS issues, no TypeScript types, abandoned), say so immediately.
- **Stay scoped.** Research the specific question. Don't expand into a full architecture review or full market analysis.
- **Use subagents.** Research is inherently context-heavy. Spawn a subagent for the investigation and return structured findings to the main context.

## State Update

- Add findings to `.maker/project_state.md > Decision Log` (if a decision is made).
- Update `handbook/product/` (if product research changes context).
- Add to Session Log.

## Interaction Calibration

Respect the working preference from kickoff:
- **Thorough**: Evaluate all top options in full. Surface trade-offs in depth.
- **Balanced**: Evaluate the top 3–4 options. Lead with the recommendation but show the comparison.
- **Fast**: Lead with the recommendation directly. Include a one-paragraph rationale. Skip the full comparison table unless the user asks.
