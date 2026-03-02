# Stack Profiles

Preset stack configurations with config blueprints. Used by `maker:architect` and `maker:build`.

Each profile is a self-contained file describing: what the stack is, when to use it, folder model, config blueprints, quality gates, and known issues.

---

## Available Profiles

| Profile | File | Shape | Stack |
|---|---|---|---|
| Frontend SPA | `frontend-spa.md` | Frontend app | Bun + Preact + Tailwind + Semantic Tokens |

More profiles can be added. See [Creating a New Profile](#creating-a-new-profile) below.

---

## How Profiles Are Used

1. **`maker:architect`** reads the matching profile to inform stack decisions and folder model.
2. **`maker:build`** reads the matching profile for config blueprints (package.json, tsconfig, etc.) and generates files from them.

The profile is selected based on project shape (determined during `maker:architect`). If no profile matches exactly, the closest one is used as a starting point and adapted.

---

## Profile Variants

Each profile may include **variants** — lightweight diffs that modify the base profile without being a full separate profile. For example, the Frontend SPA profile includes variants for:
- Swapping Preact for React
- Dropping semantic tokens
- API-only (server-side, no UI)

Variants are documented inside the profile file as "Changes from Base" tables.

---

## LLM.md Root Blueprint

Every profile shares this root `LLM.md` structure. Content is adapted to the specific project.

```markdown
# LLM.md — [project-name]

[One-sentence project description. Stack summary.]

## §2 Instruction Precedence
system prompt > LLM.md > local LLM.md files > user messages

## §3 Workflow
[Planning, Subagents, Verification, Elegance, Bug fixing, Self-improvement — from core_instructions.md]

## §4 Task Management
[Plan first, verify, track, explain, document, capture lessons]

## §5 Core Principles
[Simplicity first, No laziness, Minimal impact]

## §6 Edit Safety
[No destructive git commands unless requested. Investigate before deleting.]

## §7 Definition of Done
[Implemented + Validated + Summarized]

## §8 Progressive Context Loading
[Baseline: LLM.md + handbook/README.md. Path-based loading. Anti-bloat.]

## §9 Reference Map
[Table of topic → path for all project-owned docs. Example:]
| Topic | File |
|---|---|
| Product scope | `handbook/product/brief.md` |
| Architecture | `handbook/architecture/project-structure.md` |
| Styling system | `handbook/design/styling-system.md` *(omit if no UI)* |
| Coding conventions | `handbook/engineering/conventions.md` |
| Workflow context | `handbook/workflows/context-loading.md` |
| Known lessons | `.maker/lessons.md` |

**IMPORTANT**: All paths must point to the project's own files (handbook/, src/).
Never reference the maker skill or any external tool that generated the project.
The project must be fully self-contained after generation.
```

---

## Creating a New Profile

A stack profile is a markdown file in this folder. Profiles are language- and stack-specific by design — a Python API profile looks nothing like a TypeScript frontend profile, and that's correct.

### Required Sections

```markdown
# [Profile Name]

[One sentence: what this profile is for and what stack it targets.]

## When to Use

[Bullet list of signals that this profile is the right fit.]

## Stack

| Component | Choice | Version | Rationale |
|---|---|---|---|
[Every dependency/tool with pinned version (or minimum version) and why it was chosen.
 For compiled languages: include compiler version. For interpreted: include runtime version.]

## Folder Model

[Tree diagram showing the project structure this profile produces.
 Follow language conventions — Go, Python, Rust, Java all have different idiomatic layouts.
 Don't impose a JS/TS src/ structure on non-JS projects.]

## Config Blueprints

[Full file contents for every config file relevant to this stack.
 JS/TS projects: package.json, tsconfig.json, linter config, dev server config, .gitignore.
 Python: pyproject.toml, requirements.txt or equivalent, linter config, .gitignore.
 Go: go.mod, golangci.yml (if used), Makefile (if used), .gitignore.
 Rust: Cargo.toml, rustfmt.toml (if non-default), .gitignore.
 Include only what's relevant to the stack — don't add files that don't apply.]

## Quality Gates

[Ordered list of validation commands using the stack's actual tools.
 Include a combined command if one exists (e.g., `make validate`, `npm run validate`).]

## Known Issues

[Gotchas, workarounds, things that don't work as expected. Be honest — this saves hours of debugging.]

## Variants (optional)

[Lightweight diffs from the base profile. Each variant is a "Changes from Base" table showing only what's different.]
```

### Guidelines

1. **Pin versions.** Use `^major.minor.patch` for dependencies. Don't use `latest`. The profile should produce reproducible builds months later.

2. **Include every config file.** A profile must be self-sufficient. `maker:build` should be able to generate every file from the profile alone — no guessing, no defaults.

3. **Document known issues.** If the dev server doesn't work with a certain flag, if a library needs a special import, if there's a CORS gotcha — write it down. These are the most valuable parts of a profile.

4. **Test the profile.** Before adding a profile, actually build a project from it. Run all quality gates. If something fails, fix the profile or document the workaround.

5. **Variants vs. new profiles.** Use a variant when only 2-3 config files change (e.g., swapping React for Preact). Create a new profile when the folder model, quality gates, or fundamental architecture differs (e.g., fullstack with separate client/server, agent-based system).

6. **Name the file by shape.** Use the project shape as the filename: `frontend-spa.md`, `api-server.md`, `fullstack.md`, `agent-system.md`. Keep names short and descriptive.

### Example: Adding a Fullstack Profile

1. Create `stack-profiles/fullstack.md`.
2. Follow the required sections above.
3. The folder model should show client/server separation (`src/client/`, `src/server/` or top-level `client/`, `server/`).
4. Include separate build pipelines for client and server.
5. Include separate LLM.md contracts per side.
6. Add variants if applicable (e.g., with/without auth, with/without database).
7. Add the profile to the "Available Profiles" table in this README.
