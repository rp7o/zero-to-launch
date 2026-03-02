# Handbook Templates

Templates for handbook sections. Used by `maker:discover` (product sections) and `maker:architect` (standards sections).

---

## Handbook Structure

```
handbook/
├── README.md                       ← taxonomy + routing
├── product/                        ← written by discover
│   ├── brief.md
│   ├── plan.md
│   └── deferred.md
├── design/                         ← written by discover & architect
│   ├── direction.md                ← (discover)
│   ├── styling-system.md           ← (architect, if tokens)
│   └── assets/                     ← (optional) .pen, .fig, or image files
├── architecture/                   ← written by architect
│   └── project-structure.md
├── engineering/                    ← written by architect
│   ├── conventions.md
│   ├── api-design.md
│   └── data-modeling.md
├── workflows/                      ← written by architect
│   └── context-loading.md
└── lessons.md                      ← initialized by build, appended during work
```

---

## handbook/README.md Template

```markdown
# Handbook

Project knowledge base for humans and coding agents.

## Contents

| Section | Path | What it covers |
|---|---|---|
| Product context | `product/` | Brief, plan, deferred decisions |
| Design | `design/` | Visual direction, styling system |
| Architecture | `architecture/` | Project structure |
| Engineering | `engineering/` | Coding conventions, API design, data modeling |
| Workflows | `workflows/` | Context loading |
| Lessons learned | `lessons.md` | Patterns and corrections accumulated over time |

## Releases

`CHANGELOG.md` at the project root. Create at first release.

## Which doc to read when

| You are doing... | Read |
|---|---|
| Understanding the project | `product/brief.md` |
| Planning or scoping work | `product/plan.md` |
| Checking what's deferred | `product/deferred.md` |
| Looking at design vibe or screens | `design/direction.md` |
| Touching styles or UI tokens | `design/styling-system.md` |
| Starting any coding task | `LLM.md` + this file |
| Touching components or lib | `engineering/conventions.md` |
| Changing APIs | `engineering/api-design.md` |
| Touching the database | `engineering/data-modeling.md` |
| Understanding context loading | `workflows/context-loading.md` |
| Post-correction patterns | `lessons.md` |
```

---

## Product Section Templates (written by discover)

### handbook/product/brief.md

```markdown
# Product Brief

## What this is
[One paragraph. What the project is, from the user's perspective.]

## Target Audience
[Market segment or persona.]

## Jobs to be Done (JTBD)
[When condition, I want to motivation, so I can expected outcome.]

## Value Proposition
[Pains relieved and gains created.]

## Success Metrics
[How we measure success.]

## v1 Scope
[Bullet list of what must work.]

## Out of scope for v1
[Bullet list of explicit exclusions.]

## Hard constraints
[Any non-negotiable requirements.]
```

### handbook/product/plan.md

```markdown
# Plan

## v1 — What must work
[Checkable bullet list.]

## Later phases
[What comes after v1, if discussed.]

## Explicitly out of scope
[What will never be in this project.]
```

### handbook/design/direction.md

```markdown
# Design

## Status
[Design not yet started / Active / References provided]

## UI feel
[Description of visual direction, if discussed.]

## Screens
[Screen descriptions or "TBD".]

## Assets
[List any design files in the `handbook/design/assets/` directory (e.g., .pen files, wireframes, mockups) and what they represent.]
```

### handbook/product/deferred.md

```markdown
# Deferred Decisions

## [Topic]
**Status**: Deferred / Assumption
**Detail**: What was deferred or assumed.
**Revisit when**: Trigger for revisiting.
```

---

## Standards Section Templates (written by architect)

### handbook/architecture/project-structure.md

Must include:
1. Project shape and why.
2. Folder model (tree diagram).
3. Dependency direction rules (inward only).
4. File naming conventions per folder.
5. Extension policy: add folders when 2+ related files exist, add LLM.md for 3+ files.

### handbook/engineering/conventions.md

Must include:
1. Language/style: strict TypeScript, const over let, named function declarations.
2. Import organization: builtins → external → aliases → relative.
3. Testing: colocated test files, [test runner from stack decisions].
4. Comments: only when non-obvious, no commented-out code.
5. Refactor policy: don't refactor unrelated code.

### handbook/engineering/api-design.md (if API exists)

Must include:
1. API style (REST, GraphQL, tRPC) and strictness.
2. Standard response/error envelope shapes.
3. Pagination and filtering standard patterns.
4. Validation rules and where they occur.

### handbook/engineering/data-modeling.md (if DB exists)

Must include:
1. Table/collection naming (singular vs plural).
2. Primary key strategy (UUID vs sequential).
3. Soft delete vs hard delete policy.
4. Common timestamp columns.

### handbook/design/styling-system.md (if tokens)

Must include:
1. Token pipeline overview (profile → tokens → index → components).
2. Three axes: theme, density, typography.
3. Token naming convention (profile vs semantic).
4. Semantic classes rule: no hardcoded values in components.
5. Runtime override pattern (data attributes + JS).
6. Adding a new profile: new file, zero changes to tokens.css.

### handbook/workflows/context-loading.md

Must include:
1. Baseline: LLM.md + handbook/README.md on every task.
2. Path-to-doc routing table (adapt to actual project structure).
3. Local LLM.md precedence: nearest wins.
4. Anti-bloat: don't load unrelated docs.
