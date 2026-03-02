---
name: maker:architect
description: Design project structure and record technical decisions. Use when the user says "architect", after product exploration is complete, or when revisiting architecture after a pivot. Does NOT generate source code — that is build's job.
---

# architect — Design Project Structure and Record Decisions

Produces the technical plan. Records every decision with rationale. Does NOT generate source code — that's `maker:build`.

Before running this command, read:
- `../../references/shared-protocols.md` for shared protocols
- `../../references/stack-profiles/README.md` for stack profile index
- The matching stack profile for the project shape
- Applicable patterns from `../../references/patterns/`
- `../../references/handbook-templates.md` for handbook section templates

## When to Run

- After `maker:design` (natural flow), or directly after `maker:discover` if design was skipped.
- When revisiting architecture after a pivot.
- User explicitly says `maker:architect`.

## Re-run Behavior

If architecture already exists in `.maker/project_state.md`:
1. Read it. Show a summary: "Current architecture: [shape], [stack], [folder model]. What do you want to change?"
2. Don't re-derive what's already decided.
3. For changes: add old decision to "Superseded" in Decision Log. Record new decision. Add to Iteration History.
4. Flag downstream impacts: "This change affects [these generated files]. You'll want to re-run `maker:build` after we finalize."

## Workflow

### Step 1: Read Product Context

Read `.maker/project_state.md > Product Context` and `handbook/product/` if they exist. If product context is missing, suggest running `maker:discover` first but don't block — the user may want to architect from minimal context.

### Step 2: Determine Project Shape

Based on product context, determine the shape:

| Shape | Signals | What changes |
|---|---|---|
| **Frontend app** | UI described, user-facing screens | UI framework, dev server, styling approach; source organised around components and pages |
| **API / backend service** | No UI, serves data or runs headless | No HTML, no CSS, no styling system; source organised around routes/handlers, domain logic, infrastructure |
| **CLI / library** | Command-line tool or importable package | No HTML, no CSS, no dev server; entry point is a binary or exported API |
| **Static site** | Content-focused, minimal interaction | Lighter folder model; may not need a component system |
| **Fullstack** | Frontend + backend | Both shapes. Clear client/server boundary. Separate build pipelines. |

Present the shape determination: "Based on what you described, this is a [shape]. Here's why: [reasoning]. Does that match?"

Wait for confirmation.

### Step 3: Stack Decisions

Review the stack from `.maker/project_state.md > Profile`. Ask only the decisions relevant to the project shape. Skip inapplicable questions entirely — don't ask about CSS for a backend service, or a dev server for a CLI.

**All shapes:**
1. **Language + runtime** — (e.g., TypeScript/Node, Python, Go, Rust, Java)
2. **Package / dependency manager** — (e.g., npm, pip, Go modules, Cargo, Maven)
3. **Linter / formatter** — (e.g., ESLint/Biome for JS, pylint/black for Python, golangci-lint/gofmt for Go)
4. **Test runner** — (e.g., Vitest/Jest, pytest, Go test, cargo test)

**Frontend app / fullstack only:**
5. **UI framework** — (e.g., React, Preact, Vue, Svelte)
6. **CSS approach** — (e.g., Tailwind + semantic tokens, CSS modules, plain CSS)
7. **Dev server** — (e.g., Vite, webpack dev server)

**Backend / API / fullstack only:**
8. **Framework / router** — (e.g., Express, FastAPI, Gin, Axum, Spring Boot)
9. **Data layer** — (e.g., ORM, query builder, direct driver, or none)

For each decision: state the choice, the rationale (1 sentence), and alternatives considered.

If the user chose "fast" working preference: present all decisions at once and ask "any changes?" If "thorough": present in groups and discuss.

### Step 4: Design Folder Model

Based on shape and stack, design the folder model. Follow the matching profile from `../../references/stack-profiles/` and adapt to the project.

Present as a tree adapted to the shape and language. Do not copy a frontend structure onto a backend project.

Examples by shape:

```
# Frontend SPA (TypeScript + component framework)
/
├── index.html
├── src/
│   ├── app/        ← entry + routing
│   ├── components/ ← UI components
│   ├── lib/        ← pure logic, no UI imports
│   └── styles/     ← if CSS token system
└── handbook/

# API / backend service (any language)
/
├── src/ (or cmd/, app/, lib/ — follow language conventions)
│   ├── routes/ (or handlers/, controllers/)
│   ├── domain/ (or models/, services/)
│   └── infra/  (or db/, clients/, adapters/)
├── tests/
└── handbook/

# CLI tool
/
├── src/ (or cmd/, bin/)
│   ├── commands/
│   └── lib/
├── tests/
└── handbook/
```

Use the matching stack profile as a starting point if one exists. Adapt the tree to the project's actual language conventions — Go, Python, Rust, Java all have different idiomatic structures. Don't impose a JS/TS layout on a non-JS project.

Explain the dependency direction and naming conventions.

### Step 5: Design Handbook Sections

Plan what the architect phase will write to the handbook. Read `../../references/handbook-templates.md` for the templates. Present the list:

- `handbook/architecture/project-structure.md`
- `handbook/design/styling-system.md` (if tokens)
- `handbook/engineering/conventions.md`
- `handbook/engineering/api-design.md` (if API exists)
- `handbook/engineering/data-modeling.md` (if DB exists)
- `handbook/workflows/context-loading.md`
- `.maker/lessons.md` (initialized empty)

### Step 6: Governance Setup

Scaffold governance infrastructure if it doesn't already exist. Check for each file and create only the missing ones:

**a) `.github/workflows/ci.yml`** — GitHub Actions CI:

```yaml
name: CI
on: [push, pull_request]
jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: oven-sh/setup-bun@v1
      - run: bun install
      - run: bun run validate
```

Adapt the `run` command to whatever `validate` script the project's `package.json` defines (or the equivalent chain of gates).

**b) Pre-commit hook** — create `.husky/pre-commit` (or `.lefthook.yml` if lefthook is in the stack):

```sh
#!/bin/sh
bun run validate
```

Make the file executable (`chmod +x .husky/pre-commit`). If neither husky nor lefthook is in the stack, note the hook as a deferral instead of creating it.

**c) `.claude/settings.json` hooks** — add a `PreToolUse` hook that blocks test file edits when tests are failing:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "node -e \"const f=process.env.TOOL_INPUT_FILE_PATH||''; if(/\\.(test|spec)\\.[^.]+$/.test(f)){const r=require('child_process').spawnSync('bun',['run','test'],{encoding:'utf8'}); if(r.status!==0){process.stdout.write('Tests are failing. Fix the source code rather than modifying the test.');process.exit(2);}}\""
          }
        ]
      }
    ]
  }
}
```

If `.claude/settings.json` already exists, merge the hook into the existing file rather than overwriting it.

After scaffolding: record `governance: configured` in `.maker/project_state.md > Implementation Status`.

### Step 7: Identify Technical Deferrals

Based on decisions made, identify what's deferred:

- PostCSS pipeline (if using a runtime's built-in CSS handling)
- Testing strategy (if tests deferred)
- Deployment target
- Any stack component that wasn't fully resolved

### Step 8: Present the Plan

Combine everything into a single plan document:

```markdown
## Architecture Plan

### Project Shape
[Shape + reasoning]

### Stack
| Component | Choice | Rationale |
|---|---|---|
[rows]

### Folder Model
[tree]

### Dependency Direction
[rules]

### File Naming
[conventions]

### Quality Gates
[ordered list of commands]

### Handbook Sections
[list of what will be written]

### Technical Deferrals
[list]

### Trade-offs
[what was considered and rejected, and why]
```

Ask: **"Does this plan look right? Any changes before I record it?"**

Wait for confirmation. Do NOT proceed until confirmed.

### Step 9: Record

After confirmation:
0. If `handbook/README.md` does not exist (e.g., if `maker:discover` was skipped), create it and the required subdirectories using the template from `../../references/handbook-templates.md` before writing any other sections.
1. Write handbook sections directly using the templates from `../../references/handbook-templates.md`.
2. Update `.maker/project_state.md > Architecture` with shape, folder model, dev server, build tool.
3. Add all stack decisions to `.maker/project_state.md > Decision Log`.
4. Add deferrals to `.maker/project_state.md > Deferred Decisions`.
5. Update `handbook/README.md` routing table with architecture sections.

Present:

```markdown
## Architecture Recorded

[count] decisions logged. [count] items deferred.

### Suggested Next Step

→ `/maker:build` — Generate the project files from this plan.
→ `/maker:research [topic]` — If there's a decision you want to investigate further before building.
```

## State Update

- Update `.maker/project_state.md > Architecture` (shape, folder model, tools).
- Append all decisions to Decision Log.
- Append deferrals to Deferred Decisions.
- Set `governance: configured` in Implementation Status (after Step 6).
- Add to Session Log.
- Write handbook sections: architecture, design, engineering, workflows.

## Key Rule

**Architect does NOT write source code, package.json, LLM.md, or any file that goes into the project's `src/`.** It writes the plan (handbook + state). `maker:build` reads the plan and generates the files. Exception: governance scaffolding (`.github/workflows/ci.yml`, pre-commit hooks, `.claude/settings.json`) is created in Step 6 — these are infrastructure, not project source code.
