# Shared Protocols

Protocols that activate across multiple commands. Read this file before executing any command.

---

## 1. Interactivity Protocol

Controls how the skill interacts with the user. Calibrated by the working preference set during `maker:kickoff`.

### One Question at a Time (Default)

- Ask one question. Wait for the response. Branch based on the answer.
- Never present multiple unrelated questions in a single message.
- Exception: user explicitly asks for a rapid checklist or "just give me everything."

### Branching Logic

Every question response should influence the next question. Don't follow a fixed script:
- If the user gives a detailed answer → skip follow-up questions that are now answered.
- If the user is vague → ask a focused follow-up before moving on.
- If the user says "I don't know" or "not sure" → note it as a deferral and move on. Don't push.
- If the user gives a surprising answer → pause and explore it. The best product insights come from unexpected responses.

### Interactive Options Presentation

> [!IMPORTANT]
> Whenever asking the user to choose from options (e.g., single select, multi-select, ranking priorities), ALWAYS use the `AskUserQuestion` tool to present the options. Never write option lists as plain markdown text. The tool lets users select or rank items using their keyboard or mouse.

### Working Preference Calibration

| Preference | Question density | Confirmation style | Defaults |
|---|---|---|---|
| **Thorough** | One at a time, always | Present plan + discuss before proceeding | Minimal — ask about everything |
| **Balanced** | Small groups (2-3) | Present plan, ask "any changes?" | Moderate — make obvious defaults, ask about non-obvious ones |
| **Fast** | One combined prompt | Show what will be generated, ask "proceed?" | Aggressive — make all reasonable defaults, user course-corrects |

### Handling "Just Do It" Users

Some users want minimal interaction. Signs:
- Very short responses.
- "Just pick something."
- "Whatever you think is best."

When detected: switch to aggressive defaults. Make decisions, present them, and ask for corrections rather than asking questions. State your reasoning so the user can course-correct efficiently.

### Handling "I Want to Discuss Everything" Users

Some users want deep exploration. Signs:
- Long, detailed responses.
- Asking "what do you think about [X]?"
- Wanting to compare alternatives.

When detected: lean into discussion. Present trade-offs. Ask follow-up questions. Don't rush to decisions.

---

## 2. Project Shape Adaptation

The project type determines what commands produce. Never apply a frontend template to an API project.

### Shape Detection

Determine shape from product context. Signals:

| Signal | Shape |
|---|---|
| "UI", "page", "screen", "component", "click", "visual" | Frontend app |
| "endpoint", "API", "server", "handler", "route", "REST", "GraphQL" | API |
| "blog", "content", "static", "Markdown" | Static site |
| "API + UI", "dashboard + backend", "server + client" | Fullstack |

### Shape Impact on Commands

| Command | Frontend App | API Only | Static Site |
|---|---|---|---|
| `maker:kickoff` | Same flow. Type inferred from description. | Same flow. | Same flow. |
| `maker:discover` | Ask about JTBD, success metrics, constraints | Ask about JTBD, payloads | Ask about content structure |
| `maker:design` | Ask about screens, interactions, visual direction | Ask about endpoints, data models, clients | Ask about content types, pages |
| `maker:research` | UI libraries, CSS approaches, browser APIs | ORMs, auth strategies, hosting, DB choices | Static site generators, CMS options, hosting |
| `maker:architect` | CSS tokens, components, lib, styles, Vite | Routes, handlers, middleware, server config | Pages, layouts, content pipeline |
| `maker:build` | index.html, src/styles/, dev server (Vite) | Server entry, route handlers | Build pipeline, content |
| `maker:status` | Show token system health, dev server status | Show API routes, test coverage | Show content count, build output |
| `maker:help` | Suggest design for new screens | Suggest design for new endpoints | Suggest discover for new pages |

### Mixed Shapes

If the user describes both frontend and backend: treat as fullstack. Create a clear boundary:
- `src/client/` and `src/server/` (or `client/` and `server/` at root).
- Separate build pipelines.
- Separate LLM.md contracts for each side.

---

## 3. Iteration Protocol

Any command can be re-run. This protocol ensures iterations are captured, not lost.

### Before Re-running a Command

1. Read `.maker/project_state.md` for what exists.
2. Identify what's changing and why.
3. Preserve what isn't changing.

### During Re-run

1. **Don't re-ask answered questions.** Show what's already known. Ask only about what's new or changed.
2. **Diff, don't replace.** Show what's different from the previous run. "Last time we decided X. You're now saying Y. Here's what changes."
3. **Flag downstream impacts.** If an architecture change invalidates built files, say so: "This means the build output is stale. You'll want to re-run `maker:build` after this."

### After Re-run

1. **Append to Decision Log.** Mark old decision as "Superseded by [new decision]." Add new decision with full rationale.
2. **Add to Iteration History.** Date, what changed, why, which commands were re-run.
3. **Don't delete old state.** The history of how decisions evolved is valuable.

### Cascade Awareness

Changes cascade in this direction:

```
discover (product) → design (solution) → architect (plan) → build (code)
```

**Always check the Session Log in `.maker/project_state.md`** to determine which downstream commands have already run before deciding whether to warn.

| Trigger | Warning to surface |
|---|---|
| **Changing Product Context after design has run** | "This may make your solution design stale. Consider re-running `maker:design`." |
| **Changing Design Context after architect has run** | "Architecture decisions may be invalidated. Consider re-running `maker:architect`." |
| **Changing Architecture after build has run** | "The current build output may be invalid. Re-run `maker:build` after confirming." |

---

## 4. Confirmation Protocol

Risk-based. Low-risk artifacts proceed silently. High-risk artifacts require confirmation.

### No Confirmation Needed

- Writing to `.maker/project_state.md` (always overwritable).
- Writing to `handbook/product/` (docs, easily edited).
- `maker:status` showing state (read-only).
- `maker:help` showing commands (read-only).

### Confirmation Required

- `maker:architect` recording major technical decisions → present plan, ask "does this look right?"
- `maker:build` generating source code → present file list, ask "ready to generate?"
- Any destructive action (deleting files, overwriting existing source code).

### How to Confirm

Don't ask "is this okay?" — present the specific artifact and ask a specific question:
- "Here's the architecture plan. [plan]. Does this look right, or do you want to change anything?"
- "I'll generate these 15 files. [list]. Ready to proceed?"
- "This will overwrite `src/lib/design.ts` which you've modified since the last build. Proceed or skip this file?"

### When the User Seems Unsure

If the user asks "what should I do?", "what comes next?", "how do I use maker?", "where do I start?", or seems lost at any point, run `maker:help` before asking clarifying questions. It reads their current project state and gives a context-aware recommendation.

---

## 5. Command Dependencies

Commands work best with certain prior context, but none are hard-gated.

### Soft Dependencies (recommended but not required)

| Command | Works best after | Can run without |
|---|---|---|
| `maker:discover` | `maker:kickoff` | Yes — will ask for minimal project context inline |
| `maker:design` | `maker:discover` | Yes — will ask about JTBD inline |
| `maker:architect` | `maker:design` | Yes — will use defaults or ask inline |
| `maker:build` | `maker:architect` | Yes — will offer to run architect or use defaults |
| `maker:research` | Any | Always standalone |
| `maker:status` | `maker:kickoff` | Needs .maker/project_state.md to exist |

### When Dependencies Are Missing

If a soft dependency is missing:
1. State what's missing: "I don't have product context yet."
2. Offer the choice: "Want me to run `maker:discover` first, or should I proceed with reasonable defaults?"
3. Respect the answer. If the user says "just go" — go.

### Cross-Command Data Flow

```
kickoff     → .maker/project_state.md (Context)
discover    → .maker/project_state.md (Product Context) + handbook/product/
design      → .maker/project_state.md (Design Context) + handbook/design/
research    → .maker/project_state.md (Decision Log)
architect   → .maker/project_state.md (Architecture, Decision Log) + handbook/architecture,design,engineering,workflows/
build       → project files (src/, config, LLM.md) + .maker/project_state.md (Implementation Status)
```

Each command reads from state and writes back to state. The state file is the integration point — commands don't need to talk to each other directly.

---

## 6. Subagent Patterns

The main agent holds the conversation. Subagents do deep work and return structured findings. This keeps the interactive flow clean while allowing thorough research and analysis.

### Principle

Spawn a subagent when the work is:
- **Context-heavy** — would flood the main conversation with search results, file contents, or API responses.
- **Parallelizable** — multiple independent investigations can run simultaneously.
- **Scoped** — has a clear question and a clear output format.

### Subagent Types

| Subagent | Purpose | Spawned by | Returns |
|---|---|---|---|
| **Tech Scout** | Research a library, API, tool, or pattern. Evaluate fitness, trade-offs, compatibility with the stack. | `maker:research [tech topic]` | Structured comparison with recommendation + confidence level |
| **Market Scout** | Competitive analysis, existing solutions, market patterns, pricing models. | `maker:research [product topic]` | Landscape summary with implications for our project |
| **Solution Designer** | Determine interface structures, wireframes, API endpoints, or agent tool sets from a JTBD product brief. | `maker:design` | Mockups, schema definitions, UI wireframes, design docs |

### How to Spawn

Use the Task tool with `subagent_type="general-purpose"` for all subagent types. The differentiation is in the prompt, not the tool type.

Each subagent prompt must include:
1. **Role** — Which subagent type (e.g., "You are a Tech Scout").
2. **Question** — The specific thing to investigate or produce.
3. **Context** — Relevant project state (stack, shape, constraints). Copy the minimum needed — don't dump the full state file.
4. **Output format** — The exact schema to return (from the relevant command's output schema).
5. **Scope boundary** — What NOT to do (e.g., "Don't make architecture decisions. Just report findings.").

### When NOT to Use Subagents

- **Interactive Q&A** — The main agent handles all user-facing conversation. Subagents never talk to the user.
- **State updates** — The main agent writes to `.maker/project_state.md` and handbook files. Subagents return data; the main agent decides what to record.
- **Trivial lookups** — If a single Grep or Read answers the question, don't spawn a subagent for it.
- **Code exploration, quality gate execution, code review, and documentation writing** — the main agent handles these natively. Spawn a subagent only for web research or genuinely parallelizable independent investigations.

### Parallel Spawning

When multiple independent questions need answers, spawn subagents in parallel:

```
research react vs preact  → spawn Tech Scout for React, Tech Scout for Preact
research competitor apps   → spawn Market Scout
```

The main agent waits for all results, then synthesizes and presents to the user.

---

## 7. State Schema

Canonical schema for `.maker/project_state.md`. All skills read and write this file using these exact field names and sections. When in doubt about a field name, this section is the authority.

### Profile

```
name: [project name]
type: Frontend App | API | Static Site | Fullstack | CLI
working_preference: Thorough | Balanced | Fast
constraints: [free text — e.g., "no paid APIs", "Bun only", "must run in browser"]
stack: [comma-separated — e.g., "TypeScript, Preact, Vite"]
initialized_date: YYYY-MM-DD
shaper_handoff: yes | none
```

### Product Context

```
audience: [who this is for]
jtbd: [jobs to be done — the core user goal]
pain: [the problem being solved]
value_prop: [what makes this solution compelling]
success_metrics: [how we know it's working]
v1_scope: [what is in v1; what is explicitly out]
```

### Design Context

```
shape: Frontend App | API | Static Site | Fullstack | CLI
ui_feel: [visual/interaction style — e.g., "clean, minimal, no framework chrome"]
key_screens: [list of primary screens or views]
v1_feature_scope: [specific features included in v1 design]
```

### Architecture

```
folder_model: [project folder structure summary]
tools: [build tool, test runner, linter, formatter — e.g., "Vite, Vitest, ESLint, Prettier"]
architecture_date: YYYY-MM-DD
```

### Decision Log

| Date | Decision | Rationale | Alternatives | Revisable | Status |
|---|---|---|---|---|---|
| YYYY-MM-DD | Use [X] for [purpose] | [why] | [what else was considered] | Yes / No | Active / Superseded |

- **Status: Active** — current decision in force.
- **Status: Superseded** — overridden by a later decision; keep row for history.

### Implementation Status

```
files_generated: [list of generated source files, or "none"]
quality_gates: passing | failing | not run
governance: not configured | configured
known_issues: [free text, or "none"]
```

### Deferred Decisions

| Decision | Why Deferred | Owner |
|---|---|---|
| [what decision] | [reason it's not being made now] | [maker / founder / team] |

### Iteration History

| Date | What Changed | Why | Commands Re-run |
|---|---|---|---|
| YYYY-MM-DD | [description] | [reason] | [e.g., maker:architect, maker:build] |

### Session Log

| Date | Command | Key Outcomes |
|---|---|---|
| YYYY-MM-DD | maker:[command] | [one-line summary of what was produced or decided] |

---

## 8. Cross-Plugin Handoff Protocol

Formalizes the boundary between the Shaper plugin (business model) and the Maker plugin (engineering). When a product has been shaped before engineering begins, this protocol ensures nothing is re-asked unnecessarily.

### Input

When `handbook/product/brief.md` exists, Maker reads it before asking any product questions.

### Field Mapping

| Shaper brief field | Maps to Maker state field |
|---|---|
| Audience | Product Context → `audience` |
| Problem | Product Context → `pain` |
| Value Prop | Product Context → `value_prop` |
| Key Metrics | Product Context → `success_metrics` |
| Known Risks | Deferred Decisions table |

### Rules

- **Maker reads the brief. Shaper writes the brief.** These are strictly separate roles.
- **When the brief exists, skip redundant questions.** Do not re-ask what Shaper already captured.
- **When the brief is partial**, note which fields are missing and ask only for those.
- **Set `shaper_handoff: yes`** in the Profile section after reading a brief. If no brief exists, set `shaper_handoff: none`.

### When the Brief Is Partial

1. Read the brief and populate all available fields.
2. List the missing fields explicitly: "The Shaper brief is missing: [list]."
3. Ask only for the missing fields, one at a time per the Interactivity Protocol.
4. Do not re-confirm fields that were already captured — trust the brief.

---

## 9. Ambient Protocols

Context orientation for operations the main agent handles natively. Read the relevant section before acting — these tell the agent WHERE to look and WHAT to record, not how to execute.

### Quality Checks

Before running quality gates, detect gate commands from `package.json` scripts.

Triage order when multiple gates fail: token validation → lint → typecheck → test → build. Fix and re-run from the failure point before moving to the next gate.

After running: update `.maker/project_state.md > Implementation Status > quality_gates` and `known_issues`. Add to Session Log.

### Debugging

Before forming hypotheses:
1. Read `.maker/project_state.md` — stack, known issues, what changed recently (Iteration History).
2. Read `.maker/lessons.md` — scan for entries matching this type of failure. Apply relevant lessons before exploring further.

Fix root causes, not symptoms. After fixing: append the lesson to `.maker/lessons.md` (Pattern / Fix / Rule). Update `known_issues` in project state.

### Code Review

Before reviewing:
1. Read `.maker/project_state.md > Architecture` — folder model and dependency direction.
2. Read `handbook/engineering/conventions.md` — agreed rules for this project.
3. Read `.maker/lessons.md` — use as a checklist of known anti-patterns.

Review scope: `src/` and `scripts/`. Not config files or handbook docs. Always acknowledge what's working alongside findings.

After review: append High findings that required fixes to `.maker/lessons.md`. Update `known_issues` if any findings were deferred.
