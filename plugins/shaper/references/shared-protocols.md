# Shared Protocols

Protocols that activate across all Shaper commands.

---

## 1. Interactivity Protocol

Controls how the Shaper interacts with the user.

### One Question at a Time
- Ask one question. Wait for the response. Branch based on the answer.
- Never present multiple unrelated questions in a single message.
- If the user gives a detailed answer → skip follow-up questions that are now answered.
- If the user says "I don't know" or "not sure" → note it as a deferral and move on.

### Interactive Options Presentation

> [!IMPORTANT]
> Whenever asking the user to choose from options (e.g., single select, multi-select, ranking priorities), ALWAYS use the `AskUserQuestion` tool to present the options. This allows the user to easily select or rank items using their keyboard or mouse.

### Working Mode Reference

The working preference set in `shaper:kickoff` controls question density and confirmation style throughout the session:

| Preference | Question density | Confirmation style | Defaults |
|---|---|---|---|
| Thorough | One at a time, always | Present before each block | Minimal — ask everything |
| Balanced | 2-3 related blocks grouped | Show section summary, "any changes?" | Moderate — skip obvious follow-ups |
| Fast | One combined prompt | Show full draft, "proceed?" | Aggressive — draft everything |

Read the `working_preference` field from `.shaper/business_state.md` at the start of each skill to apply the correct mode.

---

## 2. The Artifact Handoff Protocol

The Shaper's job is to define the business and the product, and hand off that definition to the Maker plugin.

**Output Locations:**
1. **`.shaper/business_state.md`**: Internal state tracking for the Shaper. Maker never reads this.
2. **`handbook/business/`**: Where Shaper writes human-readable business artifacts (Lean Canvas, Market Research, Pitch).
3. **`handbook/product/brief.md`**: The official handoff point. When the user makes a "Go" decision, Shaper writes the final Problem, Audience, Value Prop, and Metrics to this file.

*Rule: Shaper writes the brief. Maker reads the brief.*

---

## 3. Iteration Protocol

Any Shaper command can be re-run.

### Before Re-running
1. Read `.shaper/business_state.md` for what exists.
2. Identify what's changing and why in the business model.

### During Re-run
1. **Diff, don't replace.** "Last time we thought the Audience was X. You're now saying Y. This invalidates our Value Proposition."
2. **Flag downstream impacts.** If a change invalidates a built app, warn the user: "Changing the core problem means the current codebase may be invalid."

### After Re-run
1. Update `handbook/business/` documents.
2. If the product brief changes, update `handbook/product/brief.md`.

### Cascade Awareness

The shaper workflow has a defined cascade chain:

```
kickoff → canvas → market → validate → pitch → handbook/product/brief.md
```

When a canvas block is changed during a re-run, evaluate whether downstream artifacts are now stale:

- **Changing Problem, Customer Segments, or UVP after `market` has run** → warn: "This change may make your market research stale. Consider re-running `shaper:market`."
- **Changing any canvas block after a Go decision** → warn: "The business model has changed since your Go decision. Re-validate before building."
- **Changing canvas after `brief.md` is written** → warn: "The product brief was already handed to Maker. If this change is material, re-run `shaper:validate`."

Rule: Always check the Session Log in `.shaper/business_state.md` to determine which downstream commands have already run.

---

## 4. Confirmation Protocol

Risk-based. Low-risk commands proceed silently. High-risk decisions require confirmation.

### No Confirmation Needed

- Writing to `.shaper/business_state.md` (always overwritable).
- Writing to `handbook/business/` (docs, easily edited).
- `shaper:help` showing commands (read-only).
- `shaper:status` reading state (read-only, no confirmation needed).

### Confirmation Required

- `shaper:validate` making a Go/No-Go decision → use `AskUserQuestion` to present the choice explicitly.
- `shaper:validate` writing to `handbook/product/brief.md` → the cross-plugin handoff; only write after a confirmed **Go**.
- Any overwrite of an existing canvas or pitch document → ask before overwriting.

### When the User Seems Unsure

If the user asks "what should I do?", "what comes next?", "how do I use shaper?", "where do I start?", or seems lost at any point, run `shaper:help` before asking clarifying questions. It reads their current business state and gives a context-aware recommendation. For detailed state information, point them to `shaper:status`.

---

## 5. Command Dependencies Map

Soft-dependency table. Missing dependencies don't block execution — they trigger an inline prompt or a warning.

| Command | Works best after | Can run standalone |
|---|---|---|
| shaper:canvas | shaper:kickoff | Yes — asks for idea/preference inline |
| shaper:market | shaper:canvas | Yes — asks for Problem and Audience inline |
| shaper:validate | shaper:canvas + shaper:market | Canvas required; market optional but recommended |
| shaper:pitch | shaper:validate (Go) | Yes — warns if no canvas |
| shaper:status | shaper:kickoff | Needs `.shaper/business_state.md` |

**Rule:** If a dependency is missing, say what's missing, offer to run it first, and respect the user's choice to proceed anyway.

---

## 6. Subagent Patterns

The main Shaper agent holds the conversation. Subagents do deep market work.

| Subagent | Purpose | Spawned by |
|---|---|---|
| **Market Researcher** | Research competition, pricing, and market size. | `shaper:market` |
| **Canvas Critic** | Review the 9-block Lean Canvas for holes, bad assumptions, or misaligned costs/revenues. | `shaper:validate` |

### How to Spawn

Like the Maker plugin, use the Task tool with `subagent_type="general-purpose"` for all subagent types. The differentiation is in the prompt, not the tool type.

Each subagent prompt must include:
1. **Role** — Which subagent type (e.g., "You are a Market Researcher").
2. **Question** — The specific thing to investigate or produce.
3. **Context** — Relevant business state (Audience, Problem, Solution).
4. **Output format** — The exact schema to return.
5. **Scope boundary** — What NOT to do (e.g., "Do not invent a business model, just analyze competitors.")

---

## 7. State Schema

The canonical schema for `.shaper/business_state.md`. All Shaper skills read from and write to this file.

```markdown
## Profile

- **idea**: [One-sentence description of the core idea]
- **working_preference**: [Thorough / Balanced / Fast]
- **initialized_date**: [YYYY-MM-DD]

## Lean Canvas

- **problem**: [1-3 problems]
- **customer_segments**: [Target audience]
- **uvp**: [Unique Value Proposition]
- **solution**: [1-3 solutions]
- **channels**: [Distribution channels]
- **revenue_streams**: [How money is made]
- **cost_structure**: [Major costs]
- **key_metrics**: [What to measure]
- **unfair_advantage**: [Barrier to entry, or "None identified"]

## Research

- **primary_research**: [yes / informal / none]
- **problem_research_secondary**: [yes / none]
- **competitors_analyzed**: [count, or 0]
- **market_sizing**: [yes / founder-estimate / external / none]
- **market_summary**: [One-paragraph summary, or "Not done"]

## Team Profile

- **capabilities**: [Comma-separated list of: domain expertise, technical skills, sales & distribution, regulatory / compliance]
- **additional_context**: [Free-text background the user shared about the team]

## Validation

- **shaper_recommendation**: [Go / Go with reservations / Pivot / No-Go / Not run]
- **user_decision**: [Go / Pivot / No-Go / Pending]
- **decision_date**: [YYYY-MM-DD or —]
- **open_risks**: [count]
- **override_note**: [If user overrode recommendation, note the specific reservations here]

## Decision Log

| Date | Decision | Rationale | Status |
|---|---|---|---|
| — | — | — | — |

(Status values: Active / Superseded)

## Iteration History

| Date | What Changed | Why | Commands Re-run |
|---|---|---|---|
| — | — | — | — |

## Session Log

| Date | Command | Key Outcomes |
|---|---|---|
| — | — | — |
```
