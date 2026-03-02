# Maker

A product development companion for building software projects with AI coding agents.

Maker is not a scaffolding tool you run once and throw away. It's an ongoing companion that helps you start a project, make technical decisions, generate code, and iterate — across sessions, with full memory of what's been decided and why.

## What This Skill Does

**Interactive product discovery** — Explores your idea one question at a time. Doesn't rush to code. Understands what you're building, who it's for, and what v1 looks like before making a single technical decision.

**Architecture as a separate discipline** — Technical decisions are planned and recorded before any code is generated. You review the plan. The plan explains trade-offs. Only then does code get written.

**Plan before building** — Complex tasks should always be planned step-by-step before code is written, ensuring you and the agent agree on the implementation approach. Simple tasks can be executed immediately.

**Iteration-native** — Any command can be re-run at any time. Changed your mind about the stack? Re-run `maker:architect`. Want to add a feature? Re-run `maker:discover`. Every decision is logged with rationale, and the history of changes is preserved.

**Session continuity** — `.maker/project_state.md` persists across sessions. Pick up where you left off. The skill remembers your project profile, decisions, implementation status, and lessons learned.

## Commands

| Command | Purpose | When to use |
|---|---|---|
| `maker:kickoff` | Initialize project context and preferences | Starting fresh |
| `maker:discover` | Interactive product discovery | Fleshing out an idea or adding a feature |
| `maker:design` | Define solution shape, UI/UX, interface | After exploring, before generating architecture |
| `maker:research [topic]` | Investigate tech, APIs, competitors, market | Before a technical or product decision |
| `maker:architect` | Design structure + record decisions | After designing, before building |
| `maker:build` | Generate code from the plan | After architecture is approved |
| `maker:status` | Progress overview + next steps | Anytime — especially returning to a project |
| `maker:help` | Context-aware command menu | When unsure what to do next |

## Workflow Examples

### Starting a new project

```
maker:kickoff     → context, preferences
maker:discover    → what are we building? interactive Q&A
maker:design      → solution interface, wireframes, api contracts
maker:architect   → technical plan (review before proceeding)
maker:build       → generate files
```

### Quick start (experienced developer, clear idea)

```
maker:kickoff     → context and preferences
maker:architect   → fast decisions
maker:build       → generate everything
```

### Iterating on an existing project

```
maker:discover    → new feature or changed requirements
maker:design      → update interface/UX for new feature
maker:architect   → update plan (diffs against existing)
maker:build       → generate only what's new/changed
```

### Researching before deciding

```
maker:research react vs preact       → technical comparison + recommendation
maker:research quote APIs            → options + CORS/auth analysis
maker:research competitor apps       → product landscape + differentiation
maker:architect                      → decisions informed by research
```

## How It Works

### Commands are activities, not steps

There is no enforced pipeline. Any command can run at any time. The skill suggests a natural flow but never gates you. If you want to `maker:build` without running `maker:discover` first, it will note what's missing and ask if you want to proceed anyway.

### Confirmation happens before code, not before docs

- `maker:discover` writes product docs → proceeds silently
- `maker:architect` records decisions → presents the plan, asks "does this look right?"
- `maker:build` generates source code → shows what it will generate, asks "proceed?"

### Decisions are append-only

Every technical decision is logged in `.maker/project_state.md` with date, rationale, and alternatives considered. When a decision changes, the old entry stays (marked as superseded) and the new entry is added. You always have the full history of why things are the way they are.

### One question at a time

During `maker:kickoff` and `maker:discover`, the skill asks one question, waits for your response, and branches based on what you said. It doesn't dump five questions at once. This produces better product context because each answer informs the next question.

## Project Shapes

The skill adapts its output to what you're actually building:

| Shape | What changes |
|---|---|
| **Frontend app** | CSS tokens, `src/app`, `src/components`, `src/lib`, `index.html`, dev server |
| **API only** | No CSS, no `index.html`. `src/routes/` or `src/handlers/`. Server entry. |
| **Static site** | Simpler folder model. May not need `src/features/`. |
| **Fullstack** | Both frontend and API shapes. Clear client/server boundary. |

## Files This Skill Creates in Your Project

| File | Purpose | Created by |
|---|---|---|
| `.maker/project_state.md` | Persistent memory across sessions | `maker:kickoff` |
| `handbook/product/` | Brief, plan, deferred decisions | `maker:discover` |
| `handbook/design/` | Visual direction, UX/UI, tokens, styling system | `maker:design` / `maker:architect` |
| `handbook/architecture/` | Project structure, styling system | `maker:architect` |
| `handbook/engineering/` | Conventions | `maker:architect` |
| `handbook/workflows/` | Context loading, quality gates | `maker:architect` |
| `.maker/lessons.md` | Accumulated corrections | Any session |
| `LLM.md` (root) | Agent instruction file | `maker:build` |
| `AGENTS.md` | Agent pointer file | `maker:build` |
| `*/LLM.md` | Per-folder contracts | `maker:build` |
| Config files | package.json, tsconfig, etc. | `maker:build` |
| Source code | App entry, components, lib modules | `maker:build` |

## Repository Structure

```
maker/
├── README.md                    ← this file
├── .claude-plugin/
│   └── plugin.json              ← plugin manifest
├── references/
│   ├── shared-protocols.md      ← shared protocols
│   ├── handbook-templates.md    ← handbook section templates
│   ├── examples.md              ← worked examples per command
│   ├── stack-profiles/          ← preset stack configurations
│   │   ├── README.md            ← profile index + how to create new ones
│   │   └── frontend-spa.md      ← Bun + Preact + Tailwind + tokens
│   └── patterns/                ← reusable technical recipes
│       ├── README.md            ← pattern index + how to create new ones
│       └── semantic-tokens.md   ← CSS token architecture for theming
├── skills/
│   ├── kickoff/
│   ├── discover/
│   ├── design/
│   ├── research/
│   ├── architect/
│   ├── build/
│   ├── status/
│   └── help/
└── agents/
    ├── tech-scout.md
    ├── market-scout.md
    └── solution-designer.md
```
