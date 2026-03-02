---
name: maker:build
description: Generate code from the architecture plan. Use when the user says "build", after architect has recorded a plan, or when re-generating after an architecture change. This is the only command that writes source code.
---

# build — Generate Code From the Architecture Plan

Reads the architecture plan and generates all project files: config, source code, LLM.md hierarchy, and any remaining handbook docs. This is the only command that writes source code.

Before running this command, read:
- `../../references/shared-protocols.md` for shared protocols
- `../../references/stack-profiles/README.md` for stack profile index and LLM.md blueprint
- The matching stack profile for config blueprints and pinned versions
- Applicable patterns from `../../references/patterns/` (e.g., `semantic-tokens.md` if tokens are in the plan)

## When to Run

- After `maker:architect` has recorded a plan (natural flow).
- When re-generating after an architecture change.
- User explicitly says `maker:build`.

## Pre-flight Check

Before generating anything:

1. Read `.maker/project_state.md > Architecture`. If architecture status is "not started" or "not planned": warn the user and suggest `maker:architect` first. Don't block — ask "Want me to proceed with reasonable defaults, or run `maker:architect` first?"
2. Read `.maker/project_state.md > Decision Log` for stack decisions.
3. Read `handbook/architecture/project-structure.md` for the folder model.
4. Read `handbook/design/styling-system.md` if tokens are in the plan.
5. Read the matching profile from `../../references/stack-profiles/` for config blueprints.
6. Read applicable patterns from `../../references/patterns/` (e.g., `semantic-tokens.md` if tokens are in the plan).
7. **Governance check** — verify `.github/workflows/ci.yml` and a pre-commit hook (`.husky/pre-commit` or `.lefthook.yml`) exist. If either is missing, scaffold them using the same logic as `maker:architect` Step 6 before continuing. Do not prompt the user — handle silently.

## Re-run Behavior

If files already exist (Implementation Status is not "not started"):
1. Diff the current plan against what was built.
2. Show what needs to change: "These files need updating: [list]. These are new: [list]. These are unchanged."
3. Only regenerate what's changed. Don't overwrite unrelated files.
4. Add to Iteration History: what changed and why.

## Workflow

### Step 1: Present Build Plan

Before writing any file, present the full list of what will be generated:

```markdown
## Build Plan

### Config Files
- [dependency manifest] — (e.g., package.json, pyproject.toml, go.mod, Cargo.toml)
- [language config if applicable] — (e.g., tsconfig.json for TypeScript)
- [linter/formatter config] — (e.g., biome.json, .eslintrc, .pylintrc, golangci.yml)
- .gitignore — standard ignores for stack
- [build/dev server config if applicable] — (e.g., vite.config.ts, Makefile, Dockerfile)

### Source Files
[List adapted to shape and language. Examples:]
- Frontend: index.html, src/app/[entry], src/components/[Component], src/lib/[module]
- Backend: src/main.[ext], src/routes/[handler], src/domain/[model], src/infra/[adapter]
- CLI: src/main.[ext], src/commands/[command], src/lib/[module]

### CSS Token System *(frontend app with token system only)*
- src/styles/themes/theme-light.css
- src/styles/density/density-default.css
- src/styles/typography/typography-default.css
- src/styles/tokens.css
- src/styles/index.css

### Scripts *(if applicable)*
- [project-specific scripts, e.g., validate-tokens, seed-db, generate-types]

### Agent Files
- LLM.md (root)
- AGENTS.md — update the existing file (written by kickoff): preserve the context map section, append a Codebase section with project-specific guidance (folder model, key entry points, how to run the project, where to add new code).
- [key folder]/LLM.md — one per architecture-critical folder
- scripts/LLM.md (if scripts/ exists)

### Handbook (remaining)
- .maker/lessons.md (initialized empty)

**Total: [N] files**
```

Ask: **"Ready to generate these files? Any changes?"**

Wait for confirmation. Do NOT generate until confirmed.

### Step 2: Generate Files

Generate in this order:

1. **Config files** — package.json, tsconfig.json (if TypeScript), linter config, .gitignore, CSS config, dev server config.
2. **CSS token system** — If applicable. Follow `../../references/patterns/semantic-tokens.md` exactly for naming and structure.
3. **HTML entry** — index.html with root element and token data attributes. *(frontend app only — skip for API, CLI, or library projects)*
4. **Source files** — App entry, components, lib modules.
5. **Scripts** — Validators.
6. **Agent files** — Root LLM.md, AGENTS.md, per-folder LLM.md files.
7. **Remaining handbook** — Ensure `handbook/README.md` exists; if not, create it and the basic handbook directories using `../../references/handbook-templates.md`. Write `.maker/lessons.md` (initialized empty) and any remaining handbook sections directly, using `../../references/handbook-templates.md`.

For each file, follow the blueprints in the matching stack profile and the conventions in the architecture plan.

### Step 3: Install Dependencies

Run the package manager install command. Report any warnings or errors.

### Step 4: Run Quality Gates

Run the project's quality gates. Detect gates from `package.json` scripts. Run in triage order: token validation (if applicable) → lint → typecheck → test → build. Report results per gate:

```markdown
## Build Complete

### Quality Gates
- ✓ Token validation
- ✓ Lint
- ✓ Typecheck
- ✓ Tests
- ✓ Build

### Files Generated
[count] files across [count] directories.

### Known Issues
[any warnings, non-blocking issues, or things to be aware of]

### Suggested Next Step

→ `/maker:status` — See the full project state.
→ `/maker:discover` — Add a new feature.
→ Review the generated code or run quality gates when ready.
```

If any gate fails: fix it. Report what failed and what was fixed. Re-run until all gates pass or an issue requires user input.

## File Generation Blueprints

### package.json
- Project name from state.
- Scripts: standard gates (`dev`, `maker:build`, `typecheck`, `lint`, `test`) plus any project-specific scripts (e.g., `validate:tokens` if a token system is in use, `validate` to chain all gates).
- Dependencies matching stack decisions.
- Use pinned versions from the matching stack profile.

### tsconfig.json
- Strict mode.
- Module resolution matching runtime/bundler.
- JSX config matching UI library.
- Path alias `~/*` → `./src/*`.
- Include `src` and `scripts`.

### Linter / Formatter Config
Generate the config file for whichever linter was chosen during `maker:architect`. The format varies by language and tool:
- JavaScript/TypeScript: biome.json, .eslintrc, .prettierrc
- Python: pyproject.toml (ruff/black config), .pylintrc
- Go: golangci.yml (golangci-lint), or rely on gofmt defaults
- Rust: rustfmt.toml (if deviating from defaults)
- Other: follow the linter's standard config format

Follow the conventions from the architecture plan: rule severity, import ordering, indent style, ignored directories. Use the linter's recommended ruleset as a base unless the architecture plan specifies otherwise.

### LLM.md (root)
Must include all sections from the LLM.md blueprint in `../../references/stack-profiles/README.md`:
- §1 Project (name + purpose)
- §2 Instruction Precedence
- §3 Workflow (planning, subagents, verification, elegance, bug fixing, self-improvement)
- §4 Task Management
- §5 Core Principles
- §6 Edit Safety
- §7 Definition of Done
- §8 Progressive Context Loading
- §9 Reference Map

### Per-folder LLM.md files
Create in each architecture-critical folder. Each includes: purpose, allowed/forbidden imports, naming, testing, extension policy.

### CSS Token System
Follow `../../references/patterns/semantic-tokens.md` exactly. The naming convention is:
- Profile: `--{axis}-profile-{token}` (e.g., `--color-profile-bg`)
- Semantic: `--{token}` (e.g., `--color-bg: var(--color-profile-bg)`)

Three profile files (theme, density, typography) + tokens.css (mapping) + index.css (entry).

## State Update

- Update `.maker/project_state.md > Implementation Status` with files generated, quality gate results.
- Add to Session Log.

## Interaction Calibration

Respect the working preference from kickoff:
- **Thorough**: Present the full Build Plan list and confirm each category before generating.
- **Balanced**: Present the Build Plan as a summary (file count by category), confirm once, then generate.
- **Fast**: Show a one-line summary ("Generating N files from the architecture plan") and proceed without waiting for confirmation unless an ambiguity requires input.

## Key Rules

1. **Plan before building complex tasks.** For complex or multi-step tasks, always generate a step-by-step implementation plan before writing any code (can be presented in chat, or written to a temporary `.maker/plan.md` file if the task is large). Simple, isolated tasks do not require a written plan.
2. **Never generate code without a confirmed architecture plan.** If architecture is missing, offer to run `maker:architect` or ask the user if they want defaults.
3. **Token files use exact naming conventions.** See `../../references/patterns/semantic-tokens.md`. Don't improvise token names.
4. **Dev server must actually work.** Use the dev server chosen during `maker:architect`. Verify it starts and serves correctly — don't mark build complete until confirmed.
5. **All quality gates must pass.** If a gate fails, fix it before marking build complete.
6. **Incremental on re-run.** Don't regenerate files that haven't changed. Diff first.
7. **Generated project must be self-contained.** LLM.md, handbook docs, and all generated files must reference only project-owned paths (handbook/, src/, scripts/). Never reference `maker/`, skill files, or any external tool that generated the project. The project must stand alone after generation.
