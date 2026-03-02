# Patterns

Reusable technical recipes that solve specific architectural problems. Stack profiles reference patterns — a profile decides *whether* to use a pattern, the pattern decides *how*.

---

## Available Patterns

| Pattern | File | Solves |
|---|---|---|
| Semantic Tokens | `semantic-tokens.md` | Runtime-switchable theming via CSS custom properties (color, density, typography) |

More patterns can be added. See [Creating a New Pattern](#creating-a-new-pattern) below.

---

## How Patterns Are Used

1. **`maker:architect`** decides which patterns the project needs based on shape and requirements.
2. **`maker:build`** reads each selected pattern for exact file specs, naming conventions, and validation rules.
3. **Stack profiles** reference patterns: `frontend-spa.md` says "uses `patterns/semantic-tokens.md`" — but the pattern is independent of the profile.

A pattern can be used by multiple profiles. A profile can use multiple patterns. The relationship is many-to-many.

---

## Patterns vs. Stack Profiles

| | Stack Profile | Pattern |
|---|---|---|
| **What it is** | Complete project setup (runtime, deps, folder model, config) | Solution to one specific problem |
| **Scope** | Whole project | One concern |
| **Examples** | Frontend SPA, API server, Fullstack | Semantic tokens, DB migrations, Auth flows, API versioning |
| **Contains** | Config blueprints, folder model, quality gates | File specs, naming conventions, validation rules, component patterns |

---

## Creating a New Pattern

A pattern is a markdown file in this folder. Follow this structure:

### Required Sections

```markdown
# [Pattern Name]

[One sentence: what problem this pattern solves.]

## Overview

[Diagram or description of how the pattern works. Show the data/control flow.]

## When to Use

[Bullet list of signals that this pattern fits the project.]

## When NOT to Use

[When this pattern is overkill or the wrong fit.]

## File Specifications

[Exact file contents for every file the pattern creates. Include full code blocks.
This is the most important section — `maker:build` generates files directly from these specs.]

## Naming Conventions

[Token names, file names, variable names — whatever naming rules the pattern enforces.]

## Validation

[What a validator script should check. Rules that can be mechanically verified.]

## Rules

[Non-negotiable rules for using this pattern. What components can/cannot do.]

## Component Pattern (if applicable)

[How application code consumes this pattern. Show correct and incorrect examples.]
```

### Guidelines

1. **Be prescriptive.** A pattern is not a discussion of approaches — it's a specific solution with exact file contents. `maker:build` should be able to generate every file from the pattern alone.

2. **Include validation rules.** If the pattern has rules, define what a validator should check. This feeds into quality gates.

3. **Show correct AND incorrect usage.** Developers learn fastest from "do this, not that" examples.

4. **Keep it independent.** A pattern should not assume a specific stack profile. It should work with Preact or React, with Bun or Node. If it's stack-specific, note that in "When to Use."

5. **One concern per pattern.** Don't combine auth + database + API versioning into one pattern. Each gets its own file.

6. **Name by what it solves.** `semantic-tokens.md`, `db-migrations.md`, `auth-session.md`, `api-versioning.md`. Not by the technology (`css-custom-properties.md`).
